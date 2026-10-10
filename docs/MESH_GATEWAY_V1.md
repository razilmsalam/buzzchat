# Mesh gateway posts v1

Status: **draft / proposed**. Every type value, TLV number and flag bit in this
document is PROPOSED and must be coordinated with the upstream bitchat iOS and
Android maintainers before any client emits it.

Mesh gateway posts let a user compose a public post while offline, sign it on
their own phone as a complete NIP-01 Nostr event, and hand it to the BLE /
Wi-Fi Aware mesh. Any phone that has opted in as a gateway and has a working
Tor route publishes that event to Nostr relays without changing it. A gateway
can optionally report "reached the internet" back into the mesh, and can
optionally bring a small, rate-limited trickle of outside posts into the mesh.
The feature reuses the existing Nostr event model
(`app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt:12-20`) and the
existing mesh packet and fragment formats. It does **not** reuse the generic
relay path, the default packet signer or the shared Nostr relay sockets; the
sections below say why and what replaces them. It covers Android phones as
authors, relays and gateways; Wear OS as a legacy-style relay only; and iOS
once the maintainers agree on the type assignments.

All multi-byte integers in this document use network byte order (big-endian).
Unix times are seconds since 1970-01-01T00:00:00Z.

## Purpose and scope

### Goals

- Let a person in an internet shutdown publish a short public text post to the
  wider internet through any consenting nearby phone that still has a working
  connection.
- Make the gateway a dumb, verifiable courier. It cannot alter the post,
  cannot attach its own identity to it, and cannot publish anything the author
  did not explicitly mark for the internet.
- Require explicit consent for each post. Ordinary mesh chat is never bridged.
- Survive hours of store-and-forward delay and network partitions without
  losing a consented post or publishing it twice from the relays' point of
  view.
- Fail closed whenever authenticity, consent, freshness, size or the Tor
  requirement cannot be established.

### Non-goals

- Not live video, live audio or file transfer. Media is limited to an optional
  small thumbnail (see [Media](#media)).
- Not a replacement for Noise direct messages or NIP-17 DMs. Gateway posts are
  public and permanent by construction.
- Not an anonymity system against a global adversary. The limits are stated in
  the [Threat model](#threat-model).
- Not a general mesh-to-internet tunnel, a relay proxy or a way to fetch
  arbitrary internet content.
- Not a way to republish ordinary public mesh `MESSAGE (0x02)` traffic, geohash
  chat (kind 20000) or location notes.

## Threat model

The design targets protests and internet shutdowns. Assume the following
adversaries, alone or working together.

| Adversary | Capability | Design response |
| --- | --- | --- |
| Local RF observer | Scans BLE and Wi-Fi Aware, records advertisements, timing and packet sizes | Outer packet carries no stable identity and no outer signature; random sender ID; randomized origin TTL; envelope padding with compression forbidden; jittered timestamps and retry intervals. First-hop linkage is a stated residual risk. |
| Malicious mesh relay | Drops, delays, replays, edits or floods packets | Canonical carried bytes, Nostr id and BIP-340 signature checks, envelope signature over consent flags, event-id dedup, expiry, PoW, per-link ceilings and fair scheduling. Unauthenticated acks change nothing but UI. |
| Malicious gateway | Edits, withholds, publishes over clearnet, logs, forges acks | Gateways cannot edit (signature, canonical bytes) or re-attribute (no gateway identity). Withholding is countered by many gateways and author retries. Clearnet publishing is forbidden when the author requires Tor, and other gateways still publish. Acks are advisory and never change propagation. Logging rules are below. |
| Malicious Nostr relay | Rejects, censors, lies in `OK` frames, correlates by IP or by connection | Publish over isolated Tor circuits on dedicated sockets to several relays; presence is checked per relay; `OK` is treated as unverified evidence |
| Spam / sybil | Generates many keys and posts | Per-post keys make per-author limits weak. Controls are per-link ceilings, per-link fair scheduling of re-origination, per-link store quotas, and size and store caps. PoW is a cost floor for honest-device spam, not a primary sybil control, because a laptop mines 16 bits in milliseconds. |
| Device seizure | Forensic access to author, relay or gateway phone | Author outbox and keys in panic-cleared storage with verified deletion; relays keep carried posts in memory only; no per-post identifiers in logcat; gateways keep no handoff logs |

### Residual risks (stated honestly)

- **First hop.** The author's phone transmits the first copy over a BLE or
  Wi-Fi Aware link. Its BLE scan response advertises the stable peer ID
  (`app/src/main/java/com/bitchat/android/mesh/BluetoothGattServerManager.kt:395-404`),
  and so does its Wi-Fi Aware publish and subscribe service-specific info
  (`app/src/main/java/com/bitchat/android/wifi-aware/WifiAwareMeshService.kt:372, 442`).
  The peer ID never rotates except after a panic wipe
  (`app/src/main/java/com/bitchat/android/identity/SecureIdentityStateManager.kt:394-396`).
  A neighbor that receives a transmission over a direct link can therefore
  suspect that the advertising device authored the post, and an observer that
  sees the same event id leave the same device several times can strengthen
  that suspicion, because relays re-originate a given id at most once. A
  random outer sender ID removes the link from the packet itself, not from the
  radio link. Mitigations: a random delay before first transmission, randomized
  origin TTL (so the TTL value does not prove a first hop), randomized retry
  intervals, and suppressing a retry once the post is overheard circulating.
  These are partial. Handing the post to one neighbor over Noise instead of
  originating it is an open question.
- **TTL.** Originators draw the origin TTL from a range and new relays
  sometimes forward without decrementing (see [Outer packet](#outer-packet)
  and [Forwarding model](#forwarding-model)). A received TTL at the top of the
  range is therefore only weak evidence of a first hop, not proof. Legacy
  relays always decrement, so on legacy-only paths the evidence is stronger.
- **Content.** The post is public and permanent. Text, writing style and any
  geohash can identify the author regardless of transport.
- **Gateway location.** A gateway's Tor circuit hides its IP from relays. It
  does not hide from a local RF observer that the gateway device has internet,
  and a gateway that advertises a capability bit makes this worse (see
  [Gateway discovery](#gateway-discovery)). Injecting `0x2C` inbound posts
  marks a device as a likely gateway to its neighbors even with randomized TTL.
- **Carrier mapping.** Gateway-post sync uses an ephemeral, unsigned sender ID
  (see [Gossip sync](#gossip-sync)), but a requester's filter and a
  responder's answers still reveal, to RF neighbors, that the transmitting
  radio holds those posts. Authors exclude their own posts until they have
  received them back from another node, which removes the clearest authorship
  signal but not carrier mapping in general.
- **Key material in memory.** Per-post keys are held in a zeroed `ByteArray`,
  but the BouncyCastle signer builds `BigInteger` copies
  (`app/src/main/java/com/bitchat/android/nostr/NostrCrypto.kt:411-418`) that
  stay on the heap until garbage collection. Memory forensics on a seized,
  running device may recover a recent per-post key.

### Fail-closed rules

A node must not store, display, re-originate, sync, acknowledge or publish a
gateway post unless all of the following hold. Checks run in this order, so
that the two Schnorr verifications run only on posts that already passed every
cheap check:

1. The envelope decodes completely, with no truncation, no reserved flag bits,
   no non-zero padding, a total length that is a multiple of 256, and a known
   envelope version. The outer packet and any fragments it arrived in have
   `IS_COMPRESSED` clear.
2. The event bytes parse with the strict parser and are byte-identical to the
   validator's own canonical encoding (see [Event profile](#event-profile)).
3. The event satisfies the event profile (kind, tag allow-list and
   character sets, size) and the validity window per
   [Expiry and clock skew](#expiry-and-clock-skew).
4. The event's id equals SHA-256 of its NIP-01 canonical serialization, and the
   id meets the [PoW](#pow) rule.
5. The event's BIP-340 signature verifies against its `pubkey`.
6. The envelope signature verifies against the same `pubkey`.

The single exception is the forward-compatibility rule for unknown envelope
versions in [Versioning](#versioning), which allows bounded re-origination but
never storage, display, sync, ack or publication.

If the author requires Tor, a gateway that cannot prove a Tor route must not
publish. It keeps the post and never silently falls back to a direct
connection. Any uncertainty is resolved by not publishing.

## Design principle: pre-signed, consented, verbatim

- The author's phone creates and Schnorr-signs a **complete** Nostr event
  (`id`, `pubkey`, `created_at`, `kind`, `tags`, `content`, `sig`) offline.
  Signing needs no network today: `NostrIdentity.signEvent` simply calls
  `NostrEvent.sign`
  (`app/src/main/java/com/bitchat/android/nostr/NostrIdentity.kt:78-80`;
  `app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt:98-108`). Gateway
  posts are signed through a new `GatewaySigner` instead (see
  [Author key](#author-key)).
- Gateways publish that event unchanged. They never re-sign, add tags, change
  `created_at` or wrap it. They send the carried canonical bytes verbatim. The
  existing publish path re-serializes through Gson and performs no validation
  (`app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt:446-475`;
  `app/src/main/java/com/bitchat/android/nostr/NostrRequest.kt:38-41, 64-70`),
  so gateways use a dedicated client and validate first.
- **Consent is structural.** The only events ever eligible for publication are
  those carried inside a `GATEWAY_POST` envelope whose envelope signature was
  made by the event's own key. The author's client creates the per-post key and
  signs the event and envelope only when the user has turned on "Share to
  internet" for that post and pressed send. No other code path creates such an
  event, so a gateway, modified or not, has nothing else it could publish under
  that key.
- Ordinary public mesh chat (`MESSAGE 0x02`), files (`0x22`), voice (`0x29`) and
  geohash Nostr traffic are never bridged, whatever their content. A gateway
  must not wrap or publish any packet type other than a validated
  `GATEWAY_POST`.

## Author key

### What exists today

- The account Nostr key is random, generated once and stored as
  `nostr_private_key` in the `bitchat_identity` encrypted preferences
  (`app/src/main/java/com/bitchat/android/nostr/NostrIdentity.kt:94-130`). It
  is used for NIP-17 DMs and delivery acks
  (`app/src/main/java/com/bitchat/android/nostr/NostrTransport.kt:65`). It is
  also explicitly disclosed to favorites alongside the Noise identity and
  peer ID
  (`app/src/main/java/com/bitchat/android/mesh/UnifiedMeshService.kt:126-136`;
  `app/src/main/java/com/bitchat/android/mesh/MessageHandler.kt:625-637`).
- Per-geohash keys are deterministic, HMAC-SHA256(deviceSeed, geohash ||
  iteration), and never rotate
  (`app/src/main/java/com/bitchat/android/nostr/NostrIdentity.kt:132-186`;
  `docs/security-review-jul-27.md` H8).
- Keys are generated, stored and consumed as immutable hex `String`s
  (`app/src/main/java/com/bitchat/android/nostr/NostrIdentity.kt:15-19`;
  `app/src/main/java/com/bitchat/android/nostr/NostrCrypto.kt:43-74, 411-418`),
  and `NostrIdentity.generate()` logs the full npub at debug level
  (`app/src/main/java/com/bitchat/android/nostr/NostrIdentity.kt:32`). Neither
  is acceptable for per-post keys.

### Requirement

- Gateway posts must not be signed with the account key or a per-geohash key.
  The account key links the post to the user's DM identity and, through
  favorites, to their mesh peer ID. A per-geohash key links the post to every
  geohash message the user has sent in that cell.
- **Default: a fresh per-post key.** The client generates a new secp256k1 key
  for each post into a `ByteArray`, mines PoW, signs the event and the
  envelope, and zeroes the array in a `finally` block. Retries reuse the stored
  signed envelope byte for byte, so the key is never needed again.
  Justification: per-post keys are unlinkable across posts and from every mesh
  or Noise identity, and they leave nothing on disk that can sign more posts
  after seizure. The residual heap-copy risk is stated in
  [Residual risks](#residual-risks-stated-honestly).
- **Signer API.** Add an injectable `GatewayCrypto` / `GatewaySigner`
  interface with `generateKey(): ByteArray`, `taggedHash(tag, msg)`,
  `sign(hash, key: ByteArray)` and `verify(hash, sig, pubkey)`. It must not log
  keys, npubs, ids or hashes at any level. `NostrCrypto.taggedHash` is private
  today (`app/src/main/java/com/bitchat/android/nostr/NostrCrypto.kt:370`), so
  this is a new public API. `NostrIdentity.generate()` must not be used.
- **Optional: a persona key.** A user may create a named, stable "public
  persona" key to build reputation. It is stored in `bitchat_identity` with
  `storeSecureValue`, so `clearIdentityData`
  (`app/src/main/java/com/bitchat/android/identity/SecureIdentityStateManager.kt:468-481`)
  wipes it on panic, subject to the verified-deletion rule in
  [Author lifecycle](#author-lifecycle). `NostrIdentityBridge.clearAllAssociations`
  must also remove it explicitly. The UI must state that every post under a
  persona is linkable to the others.
- Neither key may ever be sent over a Noise session, included in an
  announcement, or linked to the peer ID by any protocol message.
- The cost of per-post keys is that per-author rate limits are weak, because a
  spammer can also use a new key per post. Per-link controls carry the
  anti-spam load (see [Flood control](#flood-control)).

## Type assignments (PROPOSED)

| Layer | Name | Value | Purpose |
| --- | --- | --- | --- |
| Outer mesh packet | `MessageType.GATEWAY_POST` | `0x2A` | Author-signed Nostr event plus envelope, eligible for publishing |
| Outer mesh packet | `MessageType.GATEWAY_ACK` | `0x2B` | Advisory "reached the internet" report keyed by event id |
| Outer mesh packet | `MessageType.GATEWAY_INBOUND` | `0x2C` | Internet-origin event injected by a gateway; never re-published |
| `REQUEST_SYNC` TLV | `GATEWAY_POST_FILTER` | `0x04` | GCS filter over gateway-post event ids, sent in its own `REQUEST_SYNC` |

Rationale: the values in Android's enum are `0x01`-`0x03`, `0x10`, `0x11`,
`0x20`-`0x22` and `0x29`. `fromValue` returns null for anything else and nothing
throws (`app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:13-28`).
No ranges are formally reserved. The enum's comment says it is "exact same as
iOS", but that is stale: iOS has features with no Android type, as shown by the
capability bits for private groups, board, vouch and bridge that "match iOS"
(`app/src/main/java/com/bitchat/android/model/PeerCapabilities.kt:32-38`;
`docs/ANNOUNCEMENT_GOSSIP.md:31-35`). `0x2A`-`0x2C` are the slots right after
`VOICE_FRAME` and therefore among the most likely values for iOS to have used
already. A collision would make iOS parse gateway envelopes as another feature's
payload. **Confirming that these values are free on iOS, ideally by agreeing a
reserved range, is a release blocker.** Existing `REQUEST_SYNC` TLVs are
`0x01`-`0x03` (`docs/sync.md:133`).

Once the values are agreed, they are canonical. Implementations must not emit
these formats under any other type, and must not allocate aliases.

## Transport envelope

### Outer packet

A `GATEWAY_POST` is a normal mesh packet with:

- version `1` (version 3 or higher is dropped by every current decoder,
  `app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:409`);
- type `GATEWAY_POST (0x2A)`;
- a broadcast recipient: no recipient field, or the all-`FF` value (receivers
  accept both). Senders omit the field;
- **origin TTL**: drawn uniformly from `4..7` by every originator (author and
  re-originating relay alike; see [Forwarding model](#forwarding-model)). A
  fixed origin TTL would tell any neighbor whether it is the first hop, because
  relays decrement before forwarding
  (`app/src/main/java/com/bitchat/android/mesh/PacketRelayManager.kt:58-66`);
- **senderID**: 8 bytes from a CSPRNG, fresh for every transmission and every
  retry. It is never the stable peer ID;
- **timestamp**: the transmission time truncated to whole seconds, with uniform
  random jitter in ±60 s, expressed in milliseconds. It is not used for
  freshness;
- **no outer signature** (`HAS_SIGNATURE` clear) and no source route;
- **`IS_COMPRESSED` clear**, on the packet and on every fragment of it. See
  [Padding and compression](#padding-and-compression);
- no undefined flag bits. Bits `0x10`-`0x80` are stripped on re-encode by
  current clients
  (`app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:213-218, 282-296`).

The same outer rules apply to `0x2B` and `0x2C`, and to the gateway-post
`REQUEST_SYNC` described in [Gossip sync](#gossip-sync), except where those
sections give a different TTL.

Why no outer Ed25519 signature: current clients verify outer signatures only
against a signing key learned from the sender's verified announce
(`app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt:300-315`). An
offline author's announce is long gone by the time the post travels, and an
outer signature would let any neighbor holding the device's announce identify
the author (for `0x2A`) or the gateway (for `0x2B`/`0x2C`). Authenticity comes
from the Nostr signature and the envelope signature instead.

**Unsigned origination API (required).** Every existing origination path signs
with the device's stable key: `sendPacket` calls `signPacketBeforeBroadcast`
(`app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt:386-390`),
which also applies a source route and calls `encryptionService.signData`
(`app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt:1590-1614`);
the gossip manager's signer is the same function
(`app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt:174-176`);
and `MeshCore` signs every origination
(`app/src/main/java/com/bitchat/android/mesh/MeshCore.kt:146-147, 334, 546, 572`).
Gateway types, their fragments and the gateway-post `REQUEST_SYNC` must
therefore be sent only through a dedicated `sendUnsignedGatewayPacket` path that
skips `signPacketBeforeBroadcast` and `applyRouteIfAvailable`, never through
`sendPacket`, `sendBroadcast` or the gossip delegate. A unit test asserts that
the outbound bytes of every gateway type and fragment have `HAS_SIGNATURE`
clear and that no `signData` call happens.

### `GatewayPostEnvelope` (payload of `0x2A`)

```text
+---------+--------+-------------+-------------+--------------+--------------+-----------+
| version | flags  | expiresAt   | eventLength | event        | envelopeSig  | padding   |
| UInt8   | UInt8  | UInt32 BE   | UInt16 BE   | eventLength  | 64 bytes     | 0..255    |
|  0x01   |        | unix secs   |             | UTF-8 JSON   | BIP-340      | zero bytes|
+---------+--------+-------------+-------------+--------------+--------------+-----------+
```

- `version`: `0x01`. Receivers must not store, display, sync, ack or publish
  any other version. See [Versioning](#versioning) for forwarding behavior.
- `flags`:
  - `0x01 REQUIRE_TOR`: gateways must publish only over a proven Tor route.
    Clients set it by default.
  - `0x02 WANT_ACK`: the author asks for a `GATEWAY_ACK`.
  - `0x04`-`0x80`: reserved. They must be zero, and a non-zero reserved bit
    makes the envelope invalid. Because flags are signed, they cannot be
    silently ignored.
- `expiresAt`: last moment a gateway may publish. See
  [Expiry and clock skew](#expiry-and-clock-skew).
- `event`: one NIP-01 event JSON object in the single canonical encoding defined
  in [Event profile](#event-profile). This exact byte string is what the
  gateway publishes.
- `envelopeSig`: BIP-340 Schnorr signature by the event's `pubkey` over:

```text
envelopeHash = TaggedHash("bitchat/gateway-envelope/v1",
                          version || flags || expiresAt || eventId)
TaggedHash(tag, m) = SHA256(SHA256(tag) || SHA256(tag) || m)
eventId = the 32 raw bytes of the event id
```

  This binds the consent flags and expiry to the event without adding anything
  to the published event. The domain tag prevents the signature from being
  reused as a Nostr event signature. The carried event bytes are not in this
  preimage; they are bound instead by the canonical-encoding rule, which makes
  the bytes a deterministic function of the signed fields.
- `padding`: zero bytes that bring the whole envelope to the next multiple of
  256 bytes. Receivers **must** reject an envelope whose total length is not a
  multiple of 256, padding longer than 255 bytes, and any non-zero padding
  byte.

Fixed overhead is 72 bytes plus padding.

### Padding and compression

BLE pads only Noise frames
(`app/src/main/java/com/bitchat/android/mesh/BLEPacketPaddingPolicy.kt:11-16`),
so in-envelope padding is what hides the exact post length. It only works if the
padded bytes reach the air uncompressed. `BinaryProtocol.encode` DEFLATEs any
payload of 100 bytes or more whose unique-byte ratio is under 90%
(`app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:253-258`;
`app/src/main/java/com/bitchat/android/protocol/CompressionUtil.kt:21-35`). A
JSON envelope followed by a run of zero bytes always qualifies, the zeros
compress away, and the wire length again tracks the content length. Therefore:

- `BinaryProtocol.encode` gains a per-type compression opt-out. Originators
  must leave `IS_COMPRESSED` clear for `0x2A`, `0x2B` and `0x2C`, and for
  `FRAGMENT` packets whose `originalType` is one of them. Legacy relays
  preserve the originator's uncompressed wire bytes
  (`app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:244-252`),
  so this holds across legacy paths.
- New receivers reject, inside `BinaryProtocol.decode` and keyed on the header
  type, a compressed `0x2A`/`0x2B`/`0x2C` packet before any inflation, and
  discard a fragment set whose `originalType` is a gateway type if any of its
  fragments is compressed. This replaces a per-type decompression cap: the
  generic 10 MiB / 50,000:1 limits
  (`app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:501-539`)
  run during reassembly, before gateway code
  (`app/src/main/java/com/bitchat/android/mesh/FragmentManager.kt:269`), so
  the rejection must live in the decoder.
- With compression off, the encoded size of an envelope, the fragment count
  and the last-fragment length depend only on the 256-byte padding class.

### Event profile

The author's client must produce, and every validating node must enforce:

- **Canonical carried encoding.** The `event` bytes must be exactly one
  encoding: a JSON object with the keys `id`, `pubkey`, `created_at`, `kind`,
  `tags`, `content`, `sig` in that order; no whitespace; no other or duplicate
  keys; integers in plain decimal with no sign, fraction or exponent; lowercase
  hex of exactly 64 characters for `id` and `pubkey` and 128 for `sig`; and
  strings escaped as in NIP-01, which with the character rules below means only
  `\"`, `\\` and `\n` ever appear as escapes. Validators parse with a strict
  parser (rejecting duplicate keys, unknown keys, non-integer numbers and
  non-canonical hex), re-encode the parsed fields with their own canonical
  encoder, and **byte-compare**; any mismatch is a rejection. The default
  `Gson().fromJson` used by `NostrEvent.fromJsonString`
  (`app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt:44-50`) takes the
  last of duplicate keys, ignores unknown fields and accepts forms such as
  `1.7e9`, and `hexToByteArray` accepts `+` and `-` signs through
  `String.toInt(16)`
  (`app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt:224-229`); none of
  them may be used. Without this rule a relay could rewrite the bytes without
  changing the id, race the mutated copy ahead of the genuine one, and so
  suppress the post (dedup is by id) or watermark the path it took.
- `kind` = `1` (stored text note). Ephemeral kinds such as 20000 are rejected,
  because relays need not store them, so neither an ack nor a later
  verification by id would be meaningful.
- Allowed tags, in any order:
  - `["nonce", <decimal>, <target>]`: exactly one, exactly three elements;
    both values ASCII digits only with no leading zeros; see [PoW](#pow);
  - `["g", <geohash>]`: at most one, 2 to 5 characters from the base32 geohash
    alphabet `0123456789bcdefghjkmnpqrstuvwxyz` (CITY or coarser,
    `app/src/main/java/com/bitchat/android/geohash/LocationChannel.kt:7-13`);
  - `["t", <hashtag>]`: at most 3, each 1 to 32 characters from ASCII
    `[a-z0-9_-]`. Non-ASCII hashtags are not allowed in v1, because "lowercase"
    is locale-dependent outside ASCII.
  Any other tag, or any tag with a different element count, invalidates the
  post. In particular there is no `n` nickname tag, no client tag, and no
  `p`/`e` tags in v1.
- `content`: 1 to 2,000 Unicode scalar values, valid UTF-8.
- **Every string in the event** (content and all tag values) must not contain
  U+2028, U+2029, or C0 control characters other than U+000A in `content`
  (tag values allow no control characters at all). This avoids a known
  divergence: `NostrEvent` computes ids through Gson
  (`app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt:122-146`), whose
  escaping of those characters differs from NIP-01's canonical form.
- `created_at`: see [Privacy](#privacy). It must fit the current 32-bit field
  (`app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt:15`).
- Serialized event size: at most 4,096 bytes for text-only posts, and at most
  20,480 bytes with a thumbnail.
- Id verification must use a NIP-01-exact canonical serializer. A mismatch is a
  rejection, never a re-serialization.

### PoW

The gateway PoW rule is defined here and must not be delegated to
`NostrProofOfWork.validateDifficulty`, which checks only actual ≥ minimum and
committed ≥ minimum, never actual ≥ committed; passes a missing or non-numeric
target; reads the first nonce tag only; and logs event-id prefixes
(`app/src/main/java/com/bitchat/android/nostr/NostrProofOfWork.kt:54-81, 165-170`).
A new non-logging `GatewayPowValidator` applies, after the id has been
recomputed and matched (fail-closed step 4):

1. exactly one `nonce` tag, with exactly three elements;
2. the committed target is a decimal integer ≥ 16, or ≥ 18 when
   `expiresAt − created_at` exceeds 24 h (longer-lived posts cost more);
3. the number of leading zero bits of the verified id is ≥ the committed
   target.

Mining rules:

- PoW must be mined on the author's device before signing, with `created_at`
  held fixed at its jittered value. The existing `mineEvent` resets
  `created_at` to now on every iteration, builds a new Gson instance and
  re-serializes the whole event per attempt
  (`app/src/main/java/com/bitchat/android/nostr/NostrProofOfWork.kt:103-158`;
  `app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt:122-146`), so it
  cannot be used.
- Reference miner: serialize once with the canonical encoder, then splice the
  decimal nonce bytes into that buffer for each attempt and hash it. Because
  `tags` precede `content`, SHA-256 midstate reuse is not possible and each
  attempt hashes the full event; expected cost for 16 bits is about 65,536
  hashes of up to 4 KiB (text) or 20 KiB (thumbnail). The miner runs on a
  background dispatcher, shows progress, and is cancellable by the toggle.
  Benchmark targets on a low-end reference device are an open question.
- Ordering credit: when a node prefers one post over another (eviction
  tiebreak only), it uses `min(actualBits, 20)`. Excess PoW buys nothing, so a
  cheaply mined 30-bit attacker post does not outrank an honest 16-bit post.

### Size limits and fragmentation

- Envelope maximum: 72 + 20,480 = 20,552 bytes, padded to 20,736, which fits the v1
  16-bit payload length checked on encode
  (`app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:303-306`).
- Packets above 512 bytes are split into unsigned `FRAGMENT (0x20)` packets
  (`app/src/main/java/com/bitchat/android/mesh/FragmentManager.kt:63-158`;
  `app/src/main/java/com/bitchat/android/util/AppConstants.kt:37-46`). For a
  broadcast v1 packet with no recipient field each fragment carries
  512 − 50 = 462 data bytes
  (`app/src/main/java/com/bitchat/android/mesh/FragmentManager.kt:98-108`).
  A text-only post (envelope at most 4,352 bytes, 4,374 with the 22-byte
  header and sender) uses at most 10 fragments, and a maximum-size post
  (20,758 bytes) uses 45, well under the 256-fragment cap.
- Every real `0x2A` is fragmented: even the golden vector (512-byte envelope
  plus 22 bytes of header and sender) exceeds the 512-byte threshold. `0x2B`
  (39 bytes) is never fragmented. `0x2C` uses up to 6 fragments.
- A reassembled packet is processed at TTL 0
  (`app/src/main/java/com/bitchat/android/mesh/FragmentManager.kt:274`) and the
  relay path drops TTL 0
  (`app/src/main/java/com/bitchat/android/mesh/PacketRelayManager.kt:58-61`),
  while current clients relay each fragment for any `originalType` before
  reassembly (`app/src/main/java/com/bitchat/android/mesh/PacketProcessor.kt:141, 162-166`).
  New clients therefore use the [Forwarding model](#forwarding-model) below
  instead of the generic relay path.

### Dedup key

The dedup key for gateway posts is the 32-byte Nostr event id. Because the
carried bytes are canonical, the id determines the bytes up to the author's own
choice of signature. The mesh `PacketIdUtil` id covers sender, timestamp and
payload (`app/src/main/java/com/bitchat/android/sync/PacketIdUtil.kt:13-25`) and
lasts 5 minutes (`app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt:51-104`).
Every transmission has a fresh sender ID and timestamp, so the mesh id
deliberately differs per transmission. New clients keep an event-id seen set
that holds each id, with a `published-or-verified` bit for gateways, until
`expiresAt + 10 min`, capped at 10,000 entries (oldest first-seen evicted
first), in memory only.

The existing packet-dedup cache is pruned only every 5 minutes and then trimmed
to its cap in arbitrary `HashSet` order, not LRU
(`app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt:380-407`). Gateway
fragments add many entries per post. New clients must make that cache
time-ordered (LRU) before the feature is enabled; legacy nodes keep the
existing behavior.

### Expiry and clock skew

- The author sets `expiresAt = created_at + lifetime`, where `created_at` is
  the already-jittered value (see [Privacy](#privacy)) and the lifetime is 24 h
  by default; the user may choose 6 h, 24 h or 72 h. Deriving `expiresAt` from
  the jittered `created_at` keeps it from revealing the exact compose time.
- Valid only if `0 < expiresAt − created_at ≤ 72 h` (259,200 s).
- A receiver with clock `now` must reject a post if:
  - `created_at > now + 600` (more than 10 minutes in the future, matching the
    ANNOUNCE skew tolerance,
    `app/src/main/java/com/bitchat/android/mesh/MessageHandler.kt:31`); or
  - `now > expiresAt + 600`.
- An expired post is evicted from every store and must never be published.
- Clock sanity on the author: before signing, the client compares its clock
  with the median timestamp of recently verified announcements. If they differ
  by more than 10 minutes, it warns the user and does not sign until the user
  confirms. See Open questions.

## `GatewayAck` (payload of `0x2B`, optional)

```text
+---------+--------+------------+----------------+-------------+
| version | flags  | eventId    | relaysAccepted | reportedAt  |
| UInt8   | UInt8  | 32 bytes   | UInt8          | UInt32 BE   |
|  0x01   |        |            |                | unix secs   |
+---------+--------+------------+----------------+-------------+
```

Fixed 39 bytes, no padding, no signature, random outer senderID, broadcast,
origin TTL drawn uniformly from `4..7`, sent through the unsigned origination
API.

- `flags`: `0x01 VIA_TOR` (the gateway published over Tor); other bits are
  reserved and must be zero.
- `relaysAccepted`: the number of distinct relays that answered
  `["OK", id, true, ...]`, including `duplicate:` replies, capped at 255.
- `reportedAt`: rounded down to 10 minutes.

### What an ack proves (nothing cryptographic)

Nostr relays do not sign `OK` frames, so an ack is **advisory**. Anyone can
forge one. The rules follow from that:

- An ack is **UI only**. It never changes relay forwarding, sync, the author's
  retry schedule, or any gateway's decision to publish. One forged `0x2B` for
  an observed id therefore cannot suppress propagation.
- An ack carries no gateway identity and no author peer ID. The author never
  answers it, so it creates no online/location oracle
  (`docs/security-review-jul-27.md` H6).
- A new node re-originates an ack only for an event id currently in its relay
  store, at most once per event id over the node's lifetime, and at most
  6 acks per minute in aggregate. Acks are never stored or synced.
- The author's UI shows "Reported reached the internet (unverified)".
- A gateway sends an ack only when `WANT_ACK` is set, only after at least one
  relay accepted, and only once per event id.

### Verified on relay (optional, off by default)

An author's device may check that its own post reached a relay only if the user
enables "Check delivery over Tor" and all of the following hold:

1. The device meets the gateway [readiness](#readiness-and-tor-semantics)
   rules (proven Tor route), whatever the post's `REQUIRE_TOR` flag.
2. The lookup runs on the dedicated gateway relay client (see
   [Gateway relay client](#gateway-relay-client)) on its own isolated circuit,
   never on the shared `NostrRelayManager` sockets, which carry the account
   gift-wrap subscription (`#p` = account pubkey) and geohash subscriptions
   (`app/src/main/java/com/bitchat/android/nostr/NostrSubscriptionManager.kt:33-47`;
   `app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt:85, 125`)
   and may be direct when Tor is off
   (`app/src/main/java/com/bitchat/android/net/OkHttpProvider.kt:72-79`).
3. The lookup happens after a uniform random delay of 1 to 6 hours from the
   moment the device first becomes ready, and each `REQ` asks for the own id
   mixed with at least 3 decoy ids taken from the relay store (or random ids if
   the store is too small), in random order.

Only then may the UI show "Verified on relay", and retries stop. Without the
setting, no lookup happens and the status stays at most "Reported reached the
internet (unverified)".

An author's device never publishes its own post itself, even if it is a
gateway. Own posts leave only through the mesh. The gateway queue skips any
event id present in the local outbox.

## `GatewayInbound` (payload of `0x2C`, optional)

```text
+---------+--------+-------------+--------------+-----------+
| version | flags  | eventLength | event        | padding   |
| UInt8   | UInt8  | UInt16 BE   | eventLength  | 0..255    |
|  0x01   |  0x00  |             | UTF-8 JSON   | zero bytes|
+---------+--------+-------------+--------------+-----------+
```

- Event limit 2,048 bytes, text only: kind 1, the canonical encoding and the
  tag allow-list above, and the same PoW rule as `0x2A` (target ≥ 16).
  Requiring PoW keeps the inbound feed from being a free spam channel; the
  gateway forwards only internet events that already carry it. The envelope is
  at most 2,304 bytes after padding to a multiple of 256.
- Origin TTL drawn uniformly from `1..3`, unsigned origination API.
- Receivers verify the id and signature before displaying the post or marking
  it seen. The relay-layer dedup in `NostrRelayManager` runs before signature
  checks (`app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt:934-948`),
  so gateway code must not mark an inbound id as seen until it has verified it.
- **Injection coordination.** A gateway injects an event only after a uniform
  random delay of 0-120 s, and only if no copy of that id (as `0x2C` or
  `0x2A`) has been seen in the meantime. Each gateway injects at most 2 events
  per minute. New nodes dedup inbound copies by event id after verification
  and re-originate each id at most once, at most 2 per minute.
- **Inbound copies never count as published.** A node must not treat an
  event id seen in a `0x2C` packet as handled for publishing. Packet types are
  not authenticated, so this prevents an attacker from suppressing a real
  `0x2A` by replaying its event as `0x2C`. Gateways never publish `0x2C`
  contents.
- **No proof of internet origin.** Any mesh node can inject a self-signed
  `0x2C`. Posts are shown in a separate feed labelled **"Claimed from the
  internet (unverified)"**, never "From the internet". Media and links are
  never fetched automatically.

## Author lifecycle

1. The user composes a post. "Share to internet" defaults to **off**.
2. On send with the toggle on, and only then:
   1. validate content against the event profile;
   2. generate a per-post key into a `ByteArray` (or load the persona key);
   3. compute the jittered `created_at` and `expiresAt`, and mine PoW;
   4. sign the event, then the envelope;
   5. zero the per-post key array (in `finally`, also on abort);
   6. write the envelope to the outbox.
   If the toggle is turned off at any point before step 4 completes, abort, and
   do not sign. A user may delete a queued post before it leaves the device.
   Deleting it removes the outbox entry and its event-id record, and the
   client never transmits it.
3. First transmission happens after a uniform random delay of 5-60 s.
4. Retries reuse the same envelope with a new senderID, timestamp and origin
   TTL. Nominal intervals are 5, 10, 20, 40 and then every 60 minutes, each
   multiplied by an independent uniform factor in [0.5, 1.5], at most 12
   transmissions, until `expiresAt`. A retry is skipped if, within the last
   60 minutes, the author's device has received the same event id back from
   another node (live or through sync), because the post is then already
   circulating. Acks never change this schedule. Retries stop at expiry, at
   12 transmissions, or at "Verified on relay" if that optional check is
   enabled.
5. The outbox holds at most 20 posts and at most 512 KiB. It is persisted
   encrypted, either in `bitchat_identity` or in a dedicated directory added to
   `FileUtils.clearAllMedia`
   (`app/src/main/java/com/bitchat/android/features/file/FileUtils.kt:283-300`),
   so that panic wipes it. It survives process death and reboot.
   **Retention:** each outbox entry and its event-id record are deleted at
   `expiresAt + 10 min` or on verification, whichever comes first, so no
   own-post envelope or id is kept for more than 72 h 10 min. Per-post status
   is shown only while the entry exists.
6. **Panic** runs in this order:
   1. stop the outbox retry scheduler, the gateway queue, the inbound injector
      and all gateway re-origination, and gate the unsigned origination API
      shut, so nothing is transmitted during or after the wipe;
   2. wipe the outbox, persona key, staged media, seen set, relay store and
      gateway queue;
   3. **verify** the wipe: the outbox directory no longer exists (or the
      preference keys are absent after a successful `commit()`) and the persona
      key is absent. Today `clearAllMedia` ignores the result of
      `deleteRecursively` (`app/src/main/java/com/bitchat/android/features/file/FileUtils.kt:295-299`)
      and `clearIdentityData` only logs a failed commit
      (`app/src/main/java/com/bitchat/android/identity/SecureIdentityStateManager.kt:474-476`);
      both must report success or failure to the panic flow;
   4. if verification fails, keep transports stopped, using the same pattern as
      the conversation-database check
      (`app/src/main/java/com/bitchat/android/ui/ChatViewModel.kt:1521-1526`).
   Copies already transmitted to other devices cannot be recalled, and the
   panic UI must say so. Panic does not clear logcat, which is why the
   [logging rules](#what-gateways-must-not-log-or-keep) forbid per-post
   identifiers in logs.

## Mesh propagation and storage

### Forwarding model

New clients **re-originate** gateway posts instead of relaying their fragments.
This is the only model in which validation, budgets and priority can gate what
a node forwards.

1. **Hold, do not relay.** A new client never passes a `0x2A`, `0x2B` or `0x2C`
   packet, or a `FRAGMENT` whose `originalType` is one of them, to
   `PacketRelayManager.handlePacketRelay`. `PacketProcessor` diverts them to a
   new `GatewayForwarder` (classes that change: `PacketProcessor` around
   `handleFragment` and the central relay call,
   `app/src/main/java/com/bitchat/android/mesh/PacketProcessor.kt:141, 162-166, 209-223`;
   `FragmentManager`; and the new forwarder).
2. **Early duplicate check.** Because the envelope is uncompressed and the
   canonical event starts with `{"id":"`, fragment index 0 of a `0x2A` carries
   the 64 hex characters of the event id at a fixed offset (byte 37 of the
   reassembled packet with no recipient field). When index 0 arrives and that
   id is already in the seen set, the node discards the fragment set and
   charges a duplicate to the ingress link (see
   [Flood control](#flood-control)). This saves reassembly memory; it is never
   used as a validity signal.
3. **Reassemble and validate.** Gateway-type fragment sets use a reassembly
   timeout of 120 s instead of 30 s
   (`app/src/main/java/com/bitchat/android/util/AppConstants.kt:40`), capped at
   16 concurrent gateway-type sets so they cannot starve chat reassembly. The
   reassembled post goes through the fail-closed checks.
4. **Record and store.** A valid post whose id is new is recorded in the seen
   set and stored in the relay store, whatever the budget state.
5. **Re-originate once.** Each new node re-originates each event id **at most
   once over its lifetime**, scheduled by the per-link fair queue in
   [Flood control](#flood-control). There is no duplicate-triggered re-flood.
   The re-origination is a fresh packet built through the unsigned origination
   API: the same envelope bytes; a new random senderID; a new jittered
   timestamp; a new random fragment ID; `IS_COMPRESSED` clear; and
   `TTL = T − 1`, where `T` is the TTL of the last fragment received for that
   set (or of the unfragmented packet), except that with probability 1/4 the
   node forwards with `TTL = T` instead. If the resulting TTL is 0 the node
   stores the post but does not re-originate it. The TTL is never reset to the
   origin range. Not decrementing is loop-safe because each new node
   re-originates an id only once; legacy nodes always decrement.
6. **Posts learned through sync** arrive at TTL 0
   (`app/src/main/java/com/bitchat/android/sync/GossipSyncManager.kt:192, 203`;
   `app/src/main/java/com/bitchat/android/util/AppConstants.kt:11`). A new node
   that learns a valid, previously unseen post through sync re-originates it
   once with an origin TTL drawn uniformly from `1..3`, so sync can bridge a
   partition boundary without starting a full flood.
7. **Priority.** Re-originations go out at the lowest priority, behind chat,
   announce, Noise and voice traffic. No priority mechanism exists today:
   outbound BLE packets go through one serialized FIFO actor
   (`app/src/main/java/com/bitchat/android/mesh/BluetoothPacketBroadcaster.kt:156-158`).
   `BluetoothPacketBroadcaster` and the Wi-Fi Aware sender must gain a
   prioritized outbound queue with a lowest class for gateway types and their
   fragments. Only the start of a re-origination is deferred; once its first
   fragment is sent, its remaining fragments go out contiguously at the
   existing 20 ms spacing, so downstream reassembly completes well inside the
   timeout. A 50-250 ms random jitter precedes each re-origination.
8. **Unknown envelope versions** follow [Versioning](#versioning).

Unfragmented gateway packets (in practice only `0x2B`) follow the same rules.

**Legacy interaction.** Legacy nodes, and new Wear builds without a Schnorr
verifier (see [Compatibility summary](#compatibility-summary)), still relay
gateway fragments blindly with packet-id dedup only. Each new-node
re-origination has a new fragment ID and so is a new fragment set to them, which
they flood for its remaining TTL. Because a new node re-originates each id only
once and the TTL is carried, never reset, the extra legacy load is bounded by
the number of new nodes times one TTL-limited legacy flood per post. Legacy
relays also apply the probabilistic gate below TTL 4: relay probability falls to
0.4 when more than 100 peers are active
(`app/src/main/java/com/bitchat/android/mesh/PacketRelayManager.kt:142-171`;
`app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt:362-363`),
decided per fragment, so a 10-fragment post crosses a low-TTL legacy hop in a
crowd with probability about 0.4^10 ≈ 1e-4. On legacy paths, low-TTL delivery
in crowds relies on new nodes and sync. `TransportBridgeService` decrements TTL
a second time when bridging BLE and Wi-Fi Aware
(`app/src/main/java/com/bitchat/android/service/TransportBridgeService.kt:88-112, 203-226`),
which further shortens radius through bridge nodes.

### Relay store

- In memory only. Posts carried for others are never written to disk, so device
  seizure of a relay reveals nothing after process death or panic.
- Caps: 200 posts and 2 MiB, whichever is reached first.
- **Per-link quota:** posts first received over any single ingress link may
  occupy at most 50 of the 200 slots. At quota, the oldest post from that link
  is evicted first.
- Eviction order otherwise: expired posts first, then the oldest local
  first-seen time, then the lower capped PoW credit. Eviction is never ordered
  by remaining lifetime, so choosing the maximum expiry gains nothing. Eviction
  never fails a receive. A full store simply evicts, and evicted ids stay in the
  seen set.
- Retention is tied to `expiresAt`, never to the liveness of any peer. It must
  not be hooked into `removeAnnouncementForPeer` or `pruneStaleAnnouncements`,
  which delete a sender's messages about 3 minutes after its announce goes
  stale
  (`app/src/main/java/com/bitchat/android/sync/GossipSyncManager.kt:265-329`).
- Cleared in `BluetoothMeshService.clearAllInternalData`
  (`app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt:1621-1637`),
  the Wi-Fi Aware and MeshCore equivalents, and
  `ChatViewModel.performPanicClearAllData`
  (`app/src/main/java/com/bitchat/android/ui/ChatViewModel.kt:1454-1533`).

### Gossip sync

Gossip sync stores only ANNOUNCE and broadcast MESSAGE
(`app/src/main/java/com/bitchat/android/sync/GossipSyncManager.kt:105-110`).
That set is a cross-client consensus item (`docs/sync.md:134`), so gateway
posts do **not** join the existing filter. The existing `REQUEST_SYNC` is also
sent under the stable peer ID and signed
(`app/src/main/java/com/bitchat/android/sync/GossipSyncManager.kt:145-171`), so
adding gateway ids to it would publish a per-device, identity-attributed list of
carried posts. Gateway-post sync therefore uses a **separate** `REQUEST_SYNC`
whose payload contains only TLV `0x04`:

```text
[0x04][length UInt16 BE][P UInt8][M UInt32 BE][GCS bytes]
```

- The packet uses the outer rules of [Outer packet](#outer-packet): random
  senderID, unsigned, unsigned origination API, broadcast, TTL 0.
- Size: GCS bytes are at most 480, so the packet is at most
  14 + 8 + 3 + 5 + 480 = 510 bytes and is never fragmented. At P = 7 that is
  about 420 ids (`app/src/main/java/com/bitchat/android/sync/GCSFilter.kt:34-38`).
- Element hash: h64 = the first 8 bytes of SHA-256(eventId, 32 raw bytes)
  read as a big-endian integer, masked with `0x7FFF_FFFF_FFFF_FFFF`, then
  reduced mod M, with a result of 0 mapped to 1. This is what the code does
  (`app/src/main/java/com/bitchat/android/sync/GCSFilter.kt:115`;
  `app/src/main/java/com/bitchat/android/sync/GossipSyncManager.kt:180-182`),
  and it differs from `docs/sync.md:48`, which omits the mask and the 0→1
  mapping. See the vector with the top bit set in
  [Golden vectors](#golden-vectors).
- Filter contents: ids in the relay store plus the most recently first-seen
  ids in the seen set (including ids dropped or evicted), up to capacity. A
  device never includes an id from its own outbox until it has received that id
  back from another node, and never offers such an id in a sync response
  before then.
- Legacy decoders require P and return null without it
  (`app/src/main/java/com/bitchat/android/model/RequestSyncPacket.kt:53-75`),
  so legacy nodes ignore this packet. Whether iOS ignores a `REQUEST_SYNC`
  without P must be confirmed; if not, a dedicated type is needed (open
  question). `RequestSyncPacket` gains an optional `gatewayFilter` (P, M,
  bytes), round-tripped by `encode`, and `decode` accepts a packet carrying
  either the base TLVs or `0x04`.
- Requesters send at most one gateway sync request per 60 s ± 15 s.
- **Responders** answer a given ingress link at most once per 60 s, after a
  uniform random delay of 0-2 s, with at most 5 stored posts missing from the
  filter, chosen uniformly at random (not by expiry), paced at least 500 ms
  apart, sent on that link with TTL 0 through the unsigned origination API. With
  up to 8 BLE links plus Wi-Fi Aware
  (`app/src/main/java/com/bitchat/android/util/AppConstants.kt:90-92`), this
  bounds the burst a requester can receive. The existing responder has no
  pacing (`app/src/main/java/com/bitchat/android/sync/GossipSyncManager.kt:173-207`);
  this is new.
- Sync deliveries are charged to the ingress link like live deliveries; a
  valid post is always stored (step 4 of the forwarding model) and so enters
  the next filter, which stops resend loops.
- In `ULTRA_LOW_POWER` a node serves sync responses but does not initiate
  requests and does not re-originate.

### Power

- Gateway budgets follow battery band and charging state, not `PowerMode`,
  because the resolver puts every backgrounded app into `POWER_SAVER`
  regardless of battery
  (`app/src/main/java/com/bitchat/android/mesh/PowerManager.kt:303-306`):
  NORMAL band or charging, full budgets; LOW band, half budgets; CRITICAL band
  and not charging, no re-origination and no sync requests (sync responses
  only).
- The author's outbox retries are skipped while the battery is CRITICAL
  (10% or less, `app/src/main/java/com/bitchat/android/util/AppConstants.kt:80-83`)
  unless the device is charging.

## Flood control

Receiver policy (not wire fields) for Android v1. Link identity is
`RoutedPacket.ingressLinkID`, an opaque, process-local per-connection ID
(`app/src/main/java/com/bitchat/android/model/RoutedPacket.kt:16-18`). A
fragment set is charged to the ingress link of its **first** fragment. A null
link ID falls into one shared, conservative bucket.

- **Per-link ceiling:** at most 30 reassembled gateway-type fragment sets per
  minute per ingress link, counting valid posts, invalid posts and duplicates
  (including early duplicates detected at fragment index 0). Beyond that,
  further sets from that link are discarded before reassembly. Honest
  neighbors re-originate at most 20 posts per minute, so an honest link stays
  under the ceiling; only an attacker's own link saturates.
- **Per-node re-origination budget:** at most 20 `0x2A` re-originations per
  minute (halved in the LOW band), served from a per-ingress-link
  deficit-round-robin queue, so a flooding link gets at most its fair share.
  A post waiting in the queue is already stored and servable through sync; it
  is never dropped for being over budget.
- **Acks and inbound:** at most 6 `0x2B` and 2 `0x2C` re-originations per
  minute per node.
- **Unknown envelope versions:** at most 2 re-originations per minute per node
  (see [Versioning](#versioning)).
- **Per pubkey:** at most 10 stored posts. This only matters for persona keys.
- **Reassembly:** gateway posts share the existing limits (64 active sets,
  4 MiB global, `app/src/main/java/com/bitchat/android/util/AppConstants.kt:37-46`)
  with the gateway-specific 120 s timeout and 16-set cap above.
- **Gateway publish budget:** at most 30 events per minute and 600 per hour per
  gateway, so a flood cannot get the gateway's Tor circuits or relays
  rate-limited or banned.

**Throughput ceiling.** Every post must be re-originated by each new node it
reaches, so a partition of new nodes carries at most about 20 new posts per
minute (1,200 per hour), and 10 per minute where most nodes are in the LOW
band. A 2,000-person crowd in which 3% post once an hour generates 60 posts per
hour, well under the ceiling. Link IDs are fresh per BLE or Wi-Fi Aware
connection
(`app/src/main/java/com/bitchat/android/mesh/BluetoothGattServerManager.kt:182`;
`app/src/main/java/com/bitchat/android/mesh/BluetoothGattClientManager.kt:466`;
`app/src/main/java/com/bitchat/android/wifi-aware/WifiAwareMeshService.kt:1288`),
so an attacker can reset its per-link ceiling by reconnecting. Fair queueing
still limits it to one share per concurrent link.

Legacy nodes apply none of this. Any attacker can already flood unknown
broadcast types through them (`docs/security-review-jul-27.md` H10/H11). That
risk exists with or without this feature. Replays of a valid post with fresh
sender IDs cost nothing at the legacy layer, but new nodes recognize the
duplicate event id, at fragment index 0 when possible, charge it to the
ingress link and never re-originate it.

## Gateway behavior

### Enabling

- Settings toggle "Act as internet gateway", default **off**. Separate toggle
  "Bring outside posts into the mesh", default **off**, which requires the
  first.
- Wear OS never acts as a gateway. The wear module excludes `nostr` and `net`
  (`wear/build.gradle.kts:80-87`).

### Gateway discovery

Announcement capability bit 2 (`GATEWAY`) already exists
(`app/src/main/java/com/bitchat/android/model/PeerCapabilities.kt:34`;
`docs/ANNOUNCEMENT_GOSSIP.md:31-35`). It is only a discovery hint, never
authorization. Because it tells a local RF observer which device has internet,
v1 gateways must **not** set it by default. Authors do not need it: they flood
and retry. Whether to use it, and its iOS semantics, are open questions.

### Gateway relay client

Gateway publishes, presence lookups, the optional author verification lookup
and inbound subscriptions must not use the shared `NostrRelayManager`
connections. Those are one WebSocket per relay URL
(`app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt:85, 446-470`)
on one cached client with a single unauthenticated SOCKS proxy
(`app/src/main/java/com/bitchat/android/net/OkHttpProvider.kt:57-79`), and they
carry the user's own gift-wrap subscription for the account pubkey and geohash
subscriptions
(`app/src/main/java/com/bitchat/android/nostr/NostrSubscriptionManager.kt:33-47`).
Sharing them would let a relay link "this account publishes these mesh posts"
and, through favorites, tie that to the gateway user's peer ID. Instead:

- A dedicated `GatewayRelayClient` builds its own `OkHttpClient` per connection
  with a SOCKS proxy and **unique SOCKS username/password isolation
  credentials** (Arti isolates streams by SOCKS credentials). Publishing uses a
  new isolation token per event; inbound subscriptions use one token per
  gateway session, distinct from any publish token.
- Its sockets never carry account, gift-wrap or geohash subscriptions, and the
  shared client never carries gateway traffic.
- Each socket records the route it was created on. Sockets are created only
  when the gateway is ready (below). Any Tor state change closes every gateway
  socket; a socket created on `DIRECT`, or before readiness, is never used for
  a gateway send. A raw-send API sends the carried canonical bytes as
  `["EVENT", <bytes>]`.

### Readiness and Tor semantics

A gateway may publish only when all of the following hold, checked atomically
with the send against the socket's own recorded route:

1. `TorMode.ON`
   (`app/src/main/java/com/bitchat/android/net/TorPreferenceManager.kt:18-25`),
   `ArtiTorManager.isProxyEnabled()`
   (`app/src/main/java/com/bitchat/android/net/ArtiTorManager.kt:116-120`), and
   Arti status RUNNING with bootstrap at 100%.
2. The gateway socket used for the send was created after that point and
   recorded route `TOR`.
3. At least one target relay socket is actually connected. Sockets back off for
   up to 300 s with no connectivity callback
   (`app/src/main/java/com/bitchat/android/nostr/RelayReconnectPolicy.kt:10-44`).

"Proven Tor route" in this document means exactly these three conditions. The
existing `Route.TOR` is not enough: it only means a SOCKS address is configured
(`app/src/main/java/com/bitchat/android/net/OkHttpProvider.kt:72-79`), and that
address is set before Arti binds or bootstraps, moving to the next port on a
bind failure (`app/src/main/java/com/bitchat/android/net/ArtiTorManager.kt:240-241, 288-296`).
`Route` is also reported only by `routedHttpClient()`, not by the WebSocket
client (`app/src/main/java/com/bitchat/android/net/OkHttpProvider.kt:42-67`), and
`NostrRelayManager` sends to whatever socket is in `connections[url]` and flushes
its queue in `onOpen` with no route check
(`app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt:463-468, 1136-1147`).
The SOCKS port is an unauthenticated loopback port (default 9060,
`app/src/main/java/com/bitchat/android/util/AppConstants.kt:113`) that another
app could in principle occupy; how to authenticate the local Arti endpoint is an
open question.

Today, with Tor off, Nostr silently goes direct
(`app/src/main/java/com/bitchat/android/net/OkHttpProvider.kt:119`). The
gateway feature must not inherit that:

- **`REQUIRE_TOR` set:** never publish without a proven Tor route. If Tor is off,
  bootstrapping or blocked, keep the post until it expires and retry when Tor
  becomes ready. Never downgrade.
- **`REQUIRE_TOR` clear:** still publish only over Tor, unless the gateway user
  has also enabled a separate "Allow clearnet gateway publishing" setting
  (default off, with a warning that the gateway's IP becomes tied to other
  people's posts). Clearnet publishing still uses the dedicated client and
  never the shared sockets.
- If Tor drops mid-batch, the remaining posts wait.

### Relay selection

- Targets: the default relays
  (`app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt:42-47`).
  If the event has a `g` tag, also up to 3 relays closest to that geohash via
  `RelayDirectory.closestRelaysForGeohash`
  (`app/src/main/java/com/bitchat/android/nostr/RelayDirectory.kt:88-106`).
  The gateway's own location is never used.
- The relay directory is unsigned (`docs/security-review-jul-27.md` M9). Treat
  its entries as untrusted targets, which is harmless for publishing an
  already-signed event.
- A debug-only override of the target list exists for Mesh Lab (see
  [Release-gate entries](#release-gate-entries)); release builds have none.

### Publish-once and backoff

- Use a dedicated gateway queue, separate from the shared 500-entry
  `NostrPendingEventQueue`
  (`app/src/main/java/com/bitchat/android/nostr/NostrPendingEventQueue.kt:42`),
  so mesh posts cannot evict the user's own DMs. Cap it at 100 entries, keyed
  by event id, in memory only.
- Before queueing, consult the seen set: an id whose `published-or-verified`
  bit is set is not queued again. The bit lasts until `expiresAt + 10 min`, so
  re-deliveries through sync or re-origination do not cause re-publishing. It
  is lost on process death, which only costs a duplicate publish that relays
  dedup.
- Queue eviction when full: drop expired entries, then entries every target
  has answered `blocked:` or `invalid:` for, then the entry with the most
  failed attempts, then the oldest-attempted.
- Before publishing, wait a random 0-30 s, then send `REQ` with
  `{"ids":[<id>]}` to **each** target relay. Publish to every target relay that
  does not return a valid copy. Mark the event done only when every target has
  either returned it or accepted it. One relay holding the event therefore
  cannot stop others from receiving it.
- Publish by sending `["EVENT", <event bytes from the envelope, verbatim>]`
  through the gateway relay client.
- Success for a relay means `["OK", id, true, ...]`, including a `duplicate:`
  message. This needs new plumbing: today `OK` is only logged, and an event
  counts as delivered when `webSocket.send()` buffers it
  (`app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt:877-907, 955-961`).
- On no acceptance, back off per event over 30 s, 2, 8 and 30 minutes, then
  every 30 minutes until `expiresAt`. Do not retry a relay that answered
  `blocked:` or `invalid:` for that event.
- Many gateways publishing the same event is safe. Relays dedup by id, so the
  network sees one event. Delivery is at-least-once from each gateway and
  exactly-once by identity.

### What gateways must not log or keep

Gateway, author and relay code for these types must not log (at any level),
persist or transmit:

- the radio link, BLE address, RSSI, Wi-Fi Aware peer, link ID, or outer
  senderID a post arrived from;
- the arrival time of a post;
- content, pubkey, npub, event id or id prefix;
- which relays accepted which event, beyond the in-memory queue entry.

Existing helpers violate this: `NostrIdentity.generate()` logs the npub
(`app/src/main/java/com/bitchat/android/nostr/NostrIdentity.kt:32`),
`validateDifficulty` logs id prefixes
(`app/src/main/java/com/bitchat/android/nostr/NostrProofOfWork.kt:59, 66, 70, 76`),
and `PacketRelayManager` logs the sender of every packet it evaluates
(`app/src/main/java/com/bitchat/android/mesh/PacketRelayManager.kt:44`), with
logcat timestamps recording arrival times. Gateway code must use the
non-logging `GatewayCrypto` and `GatewayPowValidator`, and gateway packets never
reach `PacketRelayManager` (see [Forwarding model](#forwarding-model)). Release
builds must also strip `Log.v`, `Log.d` and `Log.i` with R8
`-assumenosideeffects` (`app/proguard-rules.pro` has none today), and a lint
rule must forbid `Log` calls in gateway packages. Panic does not clear logcat.

The only state is the in-memory queue and the seen set, which expire with the
post and are cleared on panic.

## Media

- v1 is text-first. A post may carry at most one thumbnail: JPEG, maximum
  edge 320 px, at most 12 KiB, embedded in the event (encoding: see Open
  questions). The serialized event is then at most 20,480 bytes.
- The thumbnail must be produced only by the re-encode path
  (`app/src/main/java/com/bitchat/android/features/media/ImageUtils.kt:14-69`),
  which decodes and re-compresses and so drops EXIF/GPS, **before signing**.
  The generic file path keeps EXIF and the original filename
  (`app/src/main/java/com/bitchat/android/features/file/FileUtils.kt:65-117`)
  and must be blocked for gateway posts.
- No filename is carried. The `img_<millis>.jpg` name
  (`app/src/main/java/com/bitchat/android/features/media/ImageUtils.kt:34, 59`)
  must not appear in the event.
- Before signing, the composer re-parses the final JPEG and refuses to sign it
  if it contains any APP1 (EXIF/XMP) segment or a GPS tag.
- Staged thumbnails live in a directory covered by `FileUtils.clearAllMedia`.
- Mining cost scales with event size (see [PoW](#pow)); a text-only v1 is an
  open question.

## Privacy

- Use synthetic, non-identifying data only in tests, vectors, screenshots and
  bug reports, per `AGENTS.md`.
- `created_at` = compose time in whole seconds minus a uniform random integer
  0-900 seconds. Public builders currently use the exact second
  (`app/src/main/java/com/bitchat/android/nostr/NostrProtocol.kt:122`).
  `expiresAt` is derived from this jittered value (see
  [Expiry and clock skew](#expiry-and-clock-skew)), so neither field reveals the
  exact compose time.
- The outer packet timestamp is jittered by ±60 s, the outer senderID is
  random per transmission, the origin TTL is randomized, and retry intervals
  are randomized (see [Outer packet](#outer-packet) and
  [Author lifecycle](#author-lifecycle)).
- Geohash is optional, off by default, chosen manually, and at most 5
  characters. It is never derived automatically from live location unless a
  `LiveLocationPrivacyGate` token is held
  (`app/src/main/java/com/bitchat/android/geohash/LiveLocationPrivacyGate.kt:54-98`),
  and it is never BUILDING precision.
- No nickname, client tag or device information appears in the event.
- The author key is per-post by default (see [Author key](#author-key)).
- Gateway inbound subscriptions use only user-chosen hashtags or coarse
  geohashes, run on the isolated [gateway relay client](#gateway-relay-client),
  and are torn down using the `NostrLiveSubscriptionPrivacy` close-targets
  pattern
  (`app/src/main/java/com/bitchat/android/nostr/NostrLiveSubscriptionPrivacy.kt:3-12`).
- Raw logs, Mesh Lab evidence and screenshots stay local.

## UI requirements

- **Composer:** a "Share to internet" toggle for each post, default off. When
  turned on, show this blocking warning: "Internet posts are public and
  permanent. Anyone can copy them, and they cannot be deleted. Do not include
  anything that identifies you." Show the expiry choice and the "Require Tor"
  option, which is on and locked unless the user opens advanced settings.
  Show PoW mining progress with a cancel action.
- **Feed:** a "Gateway posts" feed separate from mesh chat. Each post shows the
  short key fingerprint (npub prefix), never a display name. The UI states that
  names are worthless for identity. Inbound posts appear only in a separate
  "Claimed from the internet (unverified)" feed.
- **Status per own post** (shown only while the outbox entry exists): Queued on
  this phone → Sent to mesh (n attempts) → Seen circulating → Reported reached
  internet (unverified) → Verified on relay (only with the optional check); or
  Expired, shown until the entry is deleted at `expiresAt + 10 min`.
- **Gateway settings:** the two toggles, the clearnet override, live counts
  (queued, published, waiting for Tor), and a clear "waiting for Tor" state
  instead of any silent fallback.
- **Panic:** a notice that posts already handed to the mesh cannot be recalled.
- Visible changes need before/after screenshots made with synthetic data.

## Versioning

- The outer packet stays at version 1. No new header flag bits are used.
- The envelope `version` byte governs the payload. A new client receiving an
  unknown envelope version never stores, syncs, displays, acks or publishes it.
  For forward compatibility it may re-originate it once, by the
  [Forwarding model](#forwarding-model) rules (carried TTL, unsigned,
  uncompressed), under the separate budget of 2 per minute, provided the
  packet is within the `0x2A` size cap. This is the only exemption from the
  fail-closed checks, and it covers only checks 2-6; it never covers
  compression or size.
- Additive changes need a new envelope version, never a reinterpretation of
  reserved bits.

## Compatibility summary

- **New Android to new Android:** full validation, store, separate sync request,
  budgets, re-origination, gateway publishing.
- **New Android to older Android/Wear:** older nodes relay `0x2A`/`0x2B`/`0x2C`
  as unknown broadcast types without any check
  (`app/src/main/java/com/bitchat/android/mesh/PacketProcessor.kt:119-167`;
  `app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt:256-264`),
  including their fragments. They do not display, store or sync them
  (`app/src/main/java/com/bitchat/android/sync/GossipSyncManager.kt:105-110`),
  and they ignore the gateway sync request. Consequence: legacy nodes are
  live-flood-only carriers, so author retries matter on legacy paths. They also
  forward invalid or forged posts, which new nodes then reject. Their
  amplification is bounded as stated in [Forwarding model](#forwarding-model).
- **Per-sender actors.** Every receiver creates a per-sender coroutine actor
  keyed by the packet senderID and removes actors only at shutdown
  (`app/src/main/java/com/bitchat/android/mesh/PacketProcessor.kt:55, 75, 270`).
  Random senderIDs therefore grow that map on every node, legacy Android and
  Wear included, by one entry per received gateway transmission (bounded per
  post by the author's 12 transmissions plus one re-origination per new node,
  plus sync and ack traffic). Legacy nodes cannot be patched. New clients must
  add idle-actor eviction to `PacketProcessor` in the same release.
- **Wear:** shares protocol, mesh and sync code (`wear/build.gradle.kts:88-128`).
  It cannot verify Schnorr signatures unless verification code is added to the
  shared include list. Until then, new Wear builds keep the legacy behavior:
  they relay gateway fragments blindly through `PacketRelayManager` and do not
  divert, store, sync or re-originate gateway posts.
- **iOS:** **unknown.** The iOS source is not in this repository.
  `docs/LIVE_VOICE_V1.md:41-42` says only that older clients "ignore" `0x29`.
  Whether iOS relays, drops or misparses `0x2A`-`0x2C` (including a possible
  type-value collision, see [Type assignments](#type-assignments-proposed)) and
  how it treats a `REQUEST_SYNC` without P must be confirmed by iOS
  maintainers before release. Until then, no claim of harmlessness is made.
- **Ordinary Nostr clients:** see a normal kind 1 note with a `nonce` tag and
  optionally `g`/`t` tags.

## Test plan

Types: **U** = JVM unit (JUnit 4 / Robolectric, injected clock, randomness and
transport, no public relays), **S** = JVM multi-node simulation with in-memory
transports and a local fake relay, **M** = Mesh Lab on physical devices with a
disposable relay fixture and Tor (`docs/release-gate-runbook.md`). Tests are
named by observable behavior. Negative tests assert fail-closed outcomes. All
randomized U and S tests take their seed from `TEST_SEED` when set and print
the seed on failure (`docs/testing-conventions.md:21-28`).

### Implementation seams

The S tier needs a harness that does not exist today, and the production classes
it drives have no seams for time, randomness or transport:
`PacketRelayManager` owns a `Dispatchers.IO` scope and uses global
`kotlin.random.Random`
(`app/src/main/java/com/bitchat/android/mesh/PacketRelayManager.kt:34, 73, 167`),
`FragmentManager` reads `System.currentTimeMillis` and owns its scope
(`app/src/main/java/com/bitchat/android/mesh/FragmentManager.kt:44, 218, 319, 346`),
and `NostrRelayManager` is a private-constructor singleton using the static
`OkHttpProvider.webSocketClient()`
(`app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt:22, 36, 125`).
Required seams:

- inject a `Clock`, a `Random` and a `CoroutineDispatcher` into
  `FragmentManager`, the new `GatewayForwarder`, `GatewayStore`,
  `GatewayPublisher`, the outbox scheduler and the inbound injector;
- inject `GatewayCrypto` so tests can use a recording fake (test 1.2 asserts
  zero sign calls) and inject a key buffer (test 1.3 asserts it is zeroed);
- define a `GatewayRelayTransport` interface implemented by
  `GatewayRelayClient`, with a fake relay built on
  `okhttp-mockwebserver` (`gradle/libs.versions.toml:126`) and an injected
  route/readiness provider;
- put the multi-node harness under `app/src/test/**/testsupport/`.

### 1. Safety (must never fail)

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 1.1 | Ordinary `MESSAGE` with text identical to a post reaches a gateway | Never published; gateway publisher rejects every type except validated `0x2A` | U, S |
| 1.2 | Modified gateway tries to publish a post the author did not mark | Impossible: no signed event exists; a recording `GatewayCrypto` fake sees zero sign calls with the toggle off | U |
| 1.3 | Toggle switched off before send / during PoW mining | No event signed; injected key buffer all zeros after abort; no key reaches storage; outbox empty; zero transmissions | U |
| 1.4 | Thumbnail from a source with GPS EXIF | Signed event contains no APP1/GPS; composer refuses a crafted JPEG with EXIF | U |
| 1.5 | Linkage: `created_at`, `expiresAt`, outer timestamp, origin TTL and retry times vs the author's exact compose time and peer ID (injected clock and Random) | `compose − 900 ≤ created_at ≤ compose`; `expiresAt − created_at` equals the chosen lifetime and `expiresAt − compose` is not constant across seeds; origin TTL takes every value in 4..7; outer senderID ≠ peer ID and differs per retry; retry intervals are not constant | U |
| 1.6 | Panic with 20 queued posts and a populated relay store | Scheduler stopped before wipe; no transmission after panic starts; outbox, persona key, staged media, relay store, seen set and gateway queue are empty, including after restart; a simulated failed delete keeps transports stopped | U, M |
| 1.7 | Seized phone: dump logcat and app storage on an author, a relay and a gateway after synthetic posts, before and after a panic wipe | No pubkey, npub, event id or prefix, senderID, link address or arrival time in logs or on disk | M |
| 1.8 | `REQUIRE_TOR` with Tor OFF / bootstrapping / blocked; gateway socket opened before Tor ON; Tor drops mid-batch | Zero relay connections for the post; a pre-readiness or `DIRECT` socket is never used; remaining posts wait; post remains queued | U, M |
| 1.9 | Attacker clears `REQUIRE_TOR` in transit | Envelope signature fails; stored nowhere; zero re-originated fragments | U |
| 1.10 | Outbound bytes of `0x2A`, `0x2B`, `0x2C`, their fragments and the gateway sync request | `HAS_SIGNATURE` and `IS_COMPRESSED` clear; no route; no `signData` call | U |
| 1.11 | Two envelopes in the same 256-byte class with different content lengths | Identical outer and fragment byte lengths | U |
| 1.12 | Author's own post, author device is also a gateway | Never published by the author device; not in its sync filter or responses until received back | U |

### 2. Tampering

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 2.1 | One character of content, tag, `created_at` or sig edited | Id or signature mismatch; zero re-originated fragments; not published | U |
| 2.2 | Event swapped into another envelope | Envelope signature fails | U |
| 2.3 | Replay after expiry, and replay inside the window with fresh senderIDs | Expired: rejected. Inside: dropped as duplicate (at fragment index 0 where possible), charged to the ingress link, zero re-originations | U, S |
| 2.4 | Same text under a different key claiming the same "name" | Shown as a different fingerprint; no name field exists | U |
| 2.5 | Forged `0x2B` for an unpublished post | Author shows unverified status only; author retry count and timing unchanged; relay re-origination count unchanged; gateways still publish | U, S |
| 2.6 | `0x2C` replay of a pending `0x2A` event | Gateway still publishes the `0x2A` | U |
| 2.7 | Truncation at every byte, wrong `eventLength`, non-zero padding, total length not a multiple of 256, reserved flag set, invalid UTF-8, forbidden tag, U+2028 in content or a tag, non-ASCII `t` tag; non-canonical JSON: whitespace, reordered keys, duplicate `content` key, extra field, `1.7e9` `created_at`, uppercase hex, `+`/`-` in a hex field, wrong-length sig | Rejected without crash, before any Schnorr verification | U |
| 2.8 | Oversized event (4,097 bytes text-only), 257-fragment plan, compressed `0x2A` (whole packet and as a reassembled fragment set) | Rejected before allocation; an injected decompression counter shows decompress is never called | U |
| 2.9 | PoW cases: target < 16; actual < 16; actual < committed target; two nonce tags; non-numeric target; nonce tag with 2 elements; 72 h lifetime with target 16 | Each rejected | U |
| 2.10 | Unknown envelope version | Not stored, displayed, synced, acked or published; re-originated at most 2 per minute with carried TTL | U |
| 2.11 | 72 h post at maximum `created_at` jitter; one second past the bound | First accepted; second rejected | U |

### 3. Flooding

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 3.1 | 10,000 valid posts from one persona key | ≤ 10 stored for that key; per-link ceiling holds; p95 simulated chat delivery latency ≤ 1.5× the no-gateway baseline | S |
| 3.2 | 1,000 sybil per-post keys with valid PoW from one link | That link's sets beyond 30/min discarded; its store share ≤ 50; honest posts from other links still re-originated within their fair share; store ≤ 200 / 2 MiB | S |
| 3.3 | Malicious node injects `0x2C` at line rate | Each new node originates or re-originates ≤ 2 `0x2C`/min; BLE chat delivery still succeeds | S, M |
| 3.4 | Relay store full with continuous arrivals | Eviction order as specified; no crash, no OOM | U |
| 3.5 | Gateway receives 5,000 valid posts | Publishes ≤ 30/min; queue ≤ 100; DM queue unaffected | U |
| 3.6 | Queued chat packets and queued gateway fragments on one fake link | All chat packets sent before the next gateway re-origination starts | U |
| 3.7 | A post whose fragments arrive over two links | Charged once, to the link of the first fragment | U |

### 4. Network

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 4.1 | 1,000-node all-new crowd, 1% gateways | Post reaches a gateway; each node re-originates the post at most once; total fragment transmissions per post ≤ (node count + 12) × fragment count | S |
| 4.2 | Single gateway flapping online/offline | Exactly one event at the fake relay; backoff schedule respected | S, M |
| 4.3 | 50 gateways receive the same post | Fake relay stores one event id; duplicate `OK`s counted as success | S |
| 4.4 | Two partitions merge after 2 h | Separate sync request delivers missing posts, ≤ 5 per responder per 60 s; no node re-originates a post twice | S, M |
| 4.5 | Post delivered to a gateway 6 h after composing (24 h expiry) | Published | S |
| 4.6 | Mixed new Android, legacy Android, Wear, iOS | Legacy Android/Wear relay fragments; iOS behavior recorded | Manual matrix until Mesh Lab supports ≥ 3 devices |
| 4.7 | Tor blocked, `REQUIRE_TOR` clear, clearnet override off | Not published | U, M |
| 4.8 | All relays unreachable; one relay holds the event, others do not | Post stays queued and is retried until expiry; published to every relay that lacks it | S |
| 4.9 | Path through a BLE↔Wi-Fi bridge node | Delivery succeeds despite the double TTL decrement, via sync | Manual matrix until Mesh Lab supports ≥ 3 devices |
| 4.10 | Legacy relays at network size > 100 | Reassembly success vs hop count measured and reported | S |
| 4.11 | Author retries observed by one neighbor | Retry times not periodic; retries stop being sent once the post is received back | M |

### 5. Device

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 5.1 | Kill / reboot author mid-retry | Outbox survives; retries resume with a new senderID | U, M |
| 5.2 | Kill gateway mid-publish | Post re-received from mesh and published; relay sees one event | M |
| 5.3 | LOW / CRITICAL battery, charging and not charging | Re-origination and retry behavior follow the power rules | U, M |
| 5.4 | Low-memory old phone | Caps hold; reassembly never exceeds global limits | M |
| 5.5 | Author clock off by days or years | Warning before signing; gateways reject out-of-window posts; no crash | U |
| 5.6 | Gateway clock off by days | Validity checks reject; nothing is published outside the window | U |
| 5.7 | BLE permission denied / no Wi-Fi Aware hardware | Composer queues the post and explains why nothing is sent; no crash | U, M |

### Release-gate entries

The release-gate validator rejects any scenario that is not `required: true`
(`tools/release_gate/release_gate.py:159-160`), and `unsupported`, `blocked`
and `pending` never satisfy approval (`docs/release-gate-runbook.md`). Every
existing `XCL-` scenario lists `ios-current`
(`tools/release_gate/scenarios.json`). Therefore:

- Add `A2A-`/`END-` scenarios to `tools/release_gate/scenarios.json` only when
  the feature ships enabled, with capabilities `local-relay` and `tor` and
  aggregate evidence keys only, such as `event-id-count`, `publish-count` and
  `ack-count`.
- Defer `XCL-` entries until iOS has agreed the type values, or until the
  release gate supports a feature-flagged, non-blocking scenario class.
- Add debug-only `TestHookDriver` commands in `app/src/debug/`:
  `gateway_post_send`, `gateway_status`, `gateway_store_counts` (counts only),
  `gateway_set_relay_targets` (accepts only the fixture's onion or loopback
  URL) and `tor_set_state` (off / bootstrapping / blocked / on). The watch
  relay case needs matching count commands in the separate wear driver
  (`wear/src/debug/java/com/bitchat/watch/testhook/WearTestHookDriver.kt:45-77`).
- `tools/release_gate/mesh_lab.py` drives exactly two devices
  (`tools/release_gate/mesh_lab.py:951-963`). Add a `scenario_gateway_post`
  entry to its `SCENARIOS` dict (two-device author → gateway) and to
  `WATCH_SCENARIOS` for the watch relay case. Author → relay → gateway paths
  and tests 4.6 and 4.9 need N-device support (for example `--serial-c` or a
  role map) and stay in the manual matrix until then.
- The relay fixture is a disposable Nostr relay exposed as an onion service
  (Arti is built with `onion-service-client`, `tools/arti-build/Cargo.toml:14`),
  because a Tor exit cannot reach a LAN relay. It depends on the public Tor
  network but not on any public Nostr relay. Mesh Lab has no relay or Tor
  plumbing today, so this fixture must be added first.

## Golden vectors

All keys and data below are synthetic. The author secret key is the scalar `3`,
the first BIP-340 test-vector key; it is public knowledge and must never be
used for real posts. Signatures are BIP-340 with `aux_rand` = 32 zero bytes.
Android's signer adds random nonce input
(`app/src/main/java/com/bitchat/android/nostr/NostrCrypto.kt:530-560`), so
Android tests must **verify** these signatures and compare all other bytes
literally. They must not expect Android to reproduce the signatures.

The event's `created_at` is second-granular, consistent with the
[Privacy](#privacy) jitter rule, and `expiresAt = created_at + 86,400` (24 h).
The carried event JSON is in the canonical encoding.

```text
# author x-only pubkey (secret key = 3)
f9308a019258c31049344f85f89d5229b531c845836f99b08601f113bce036f9

# NIP-01 canonical serialization (UTF-8)
[0,"f9308a019258c31049344f85f89d5229b531c845836f99b08601f113bce036f9",1700000000,1,[["nonce","55500","16"]],"Synthetic gateway post. Test data only."]

# event id (16 leading zero bits)
00005452b9eeac2f3ac54a033fc15940cc9f0b73db0d9e317f50965368b6704a

# event sig
6b4766864dff4133a1c0c63b248021d37c053ca3167d9b20b60b9c12a0ee072bf540d79785b76311dd28433b87b57fc805ec9b0629c2427be4ee91e473d7e145

# event JSON as carried (403 bytes, canonical encoding)
{"id":"00005452b9eeac2f3ac54a033fc15940cc9f0b73db0d9e317f50965368b6704a","pubkey":"f9308a019258c31049344f85f89d5229b531c845836f99b08601f113bce036f9","created_at":1700000000,"kind":1,"tags":[["nonce","55500","16"]],"content":"Synthetic gateway post. Test data only.","sig":"6b4766864dff4133a1c0c63b248021d37c053ca3167d9b20b60b9c12a0ee072bf540d79785b76311dd28433b87b57fc805ec9b0629c2427be4ee91e473d7e145"}

# envelope signing message: version 01, flags 03 (REQUIRE_TOR|WANT_ACK),
# expiresAt 1700086400 (65 55 42 80), eventId
01 03 65 55 42 80 00 00 54 52 B9 EE AC 2F 3A C5 4A 03 3F C1 59 40 CC 9F
0B 73 DB 0D 9E 31 7F 50 96 53 68 B6 70 4A

# envelopeHash = TaggedHash("bitchat/gateway-envelope/v1", message)
3E 79 E1 5B A9 46 3E 09 D8 84 4A 8E CD 51 D7 AD 10 A6 18 14 41 EE F1 64
60 27 14 55 BF 50 5F A9

# envelopeSig
7C 9E 68 E5 1F 2D AB 4E 58 EF 80 32 9F F0 09 87 73 F4 66 3E 4D D1 72 F6
CD 12 F3 66 CA 06 43 E2 5C 8B 54 5E DA 9B 30 D4 9D B5 88 00 53 21 BB 86
4C 47 0A 01 DF 00 3D E5 F6 31 97 8F 80 A0 10 C7

# envelope header (version, flags, expiresAt, eventLength = 403)
01 03 65 55 42 80 01 93
# followed by the 403 event bytes, the 64-byte envelopeSig, and 37 zero
# padding bytes: total envelope length 512. With a 22-byte header and
# senderID the packet is 534 bytes, so it is sent as 2 fragments.

# GatewayAck: version 01, flags 01 (VIA_TOR), eventId, relaysAccepted 3,
# reportedAt 1700003400
01 01 00 00 54 52 B9 EE AC 2F 3A C5 4A 03 3F C1 59 40 CC 9F 0B 73 DB 0D
9E 31 7F 50 96 53 68 B6 70 4A 03 65 53 FE 48

# gateway-post GCS element input h64 = SHA-256(eventId)[0..8]
0A BD E2 04 8D 55 26 C2
# top bit clear; with M = 58240 (455 * 2^7): value = 23362

# GCS h64 vector with the top bit set (synthetic 32-byte id: 32 x 0x04)
# SHA-256(id)[0..8]            = 9F 4F B6 8F 3E 1D AC 82
# masked with 0x7FFF...FFFF    = 1F 4F B6 8F 3E 1D AC 82
# value mod M = 58240          = 50818   (unmasked would give 25346)
```

Fixture and executable vectors:

- Add a versioned interoperability fixture
  `app/src/test/resources/contracts/mesh-gateway-v1.json`, as
  `docs/testing-conventions.md:17` requires (the directory does not exist
  yet). It holds every value above plus: BIP-340 vectors 0-14, with the
  invalid ones asserted to verify false (the Android verifier is hand-written,
  `app/src/main/java/com/bitchat/android/nostr/NostrCrypto.kt:479-526`); an
  event with `g` and `t` tags and multibyte UTF-8 content; a `0x2C` envelope;
  the gateway sync request bytes with a GCS bitstream; the full outer `0x2A`
  packet and its fragment plan with fixed synthetic senderID
  `01 02 03 04 05 06 07 08`, timestamp, TTL and fragment ID; and the negative
  envelope and non-canonical JSON cases of test 2.7. Kotlin tests load this
  file, and it is listed in the vector manifest that XCL-006 digests
  (`tools/release_gate/scenarios.json`, evidence `vector-manifest-digest`).
- `app/src/test/kotlin/com/bitchat/android/contracts/ClientRewriteNostrContractTest.kt`
  (new; it is already listed in `docs/client-rewrite-contracts.md:18-20` but
  does not exist): `` `NIP-01 serialization and event id match canonical bytes` ``,
  `` `BIP-340 signature vectors verify and invalid vectors fail` ``, and
  `` `content with U+2028 is rejected by the gateway profile` ``.
- `app/src/test/kotlin/com/bitchat/android/gateway/GatewayPostEnvelopeTest.kt`:
  `` `envelope encoder matches canonical bytes` ``,
  `` `envelope signature vector verifies` ``,
  `` `decoder rejects every truncation` ``,
  `` `reserved flags nonzero padding and unaligned length are rejected` ``,
  `` `non canonical event bytes are rejected` ``.
- `app/src/test/kotlin/com/bitchat/android/gateway/GatewayAckTest.kt`:
  `` `ack encoder matches canonical bytes` ``.
- `app/src/test/kotlin/com/bitchat/android/contracts/ClientRewriteWireContractTest.kt`:
  `` `gateway sync request round trips and stays within one packet` ``,
  `` `gateway filter over 480 bytes is rejected` ``,
  `` `gateway filter TLV with truncated P or M is rejected` ``, and
  `` `gateway GCS hash masks the top bit and maps zero to one` ``. The existing
  `` `sync request matches canonical TLV bytes and skips extensions` `` already
  covers legacy skipping of unknown TLVs
  (`app/src/test/kotlin/com/bitchat/android/contracts/ClientRewriteWireContractTest.kt:202-215`).
- `app/src/test/kotlin/com/bitchat/android/mesh/PacketRelayManagerTest.kt` (or
  a new `PacketProcessor` test): `` `unknown broadcast type is relayed with TTL decremented` ``
  (legacy behavior; no such test exists today) and
  `` `gateway fragments are diverted and never reach the relay manager` ``.

Vectors change only with an intentional, coordinated version bump
(`docs/client-rewrite-contracts.md:23-32`). Cross-client parity is checked by
release-gate scenario XCL-006 once iOS has agreed the type values.

## Implementation references

- `app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt`
- `app/src/main/java/com/bitchat/android/protocol/CompressionUtil.kt`
- `app/src/main/java/com/bitchat/android/mesh/PacketProcessor.kt`
- `app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt`
- `app/src/main/java/com/bitchat/android/mesh/PacketRelayManager.kt`
- `app/src/main/java/com/bitchat/android/mesh/FragmentManager.kt`
- `app/src/main/java/com/bitchat/android/mesh/BluetoothPacketBroadcaster.kt`
- `app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt`
- `app/src/main/java/com/bitchat/android/mesh/MeshCore.kt`
- `app/src/main/java/com/bitchat/android/sync/GossipSyncManager.kt`
- `app/src/main/java/com/bitchat/android/sync/GCSFilter.kt`
- `app/src/main/java/com/bitchat/android/model/RequestSyncPacket.kt`
- `app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt`
- `app/src/main/java/com/bitchat/android/nostr/NostrCrypto.kt`
- `app/src/main/java/com/bitchat/android/nostr/NostrProofOfWork.kt`
- `app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt`
- `app/src/main/java/com/bitchat/android/net/ArtiTorManager.kt`
- `app/src/main/java/com/bitchat/android/net/OkHttpProvider.kt`
- `app/src/main/java/com/bitchat/android/identity/SecureIdentityStateManager.kt`
- `app/src/main/java/com/bitchat/android/features/file/FileUtils.kt`
- `app/src/main/java/com/bitchat/android/features/media/ImageUtils.kt`
- `app/proguard-rules.pro`
- `wear/build.gradle.kts` (shared-source include list)
- `docs/sync.md`, `docs/LIVE_VOICE_V1.md`, `docs/client-rewrite-contracts.md`,
  `docs/testing-conventions.md`

## Open questions

1. **Type values.** `0x2A`/`0x2B`/`0x2C` and TLV `0x04` need agreement with iOS.
   Does iOS already use any of `0x23`-`0x2C`? Can a range be reserved? This is
   a release blocker, not only a question.
2. **iOS relay behavior.** Does iOS relay unknown broadcast types and their
   fragments, and does it ignore a `REQUEST_SYNC` without P? It cannot be
   determined from this repository. If not, the gateway sync request needs its
   own type.
3. **Capability bit 2.** What does `GATEWAY` mean on iOS today, and should v1
   ever advertise it, given the RF-observer risk?
4. **Thumbnail encoding.** There is no Nostr-standard inline image. Options are
   a `data:` URI in content (rendered poorly by other clients, and relays may
   cap event size) or text-only v1, which also bounds mining cost. What event
   sizes do the default relays accept?
5. **Mining benchmarks.** What PoW time is acceptable on a low-end reference
   device for 4 KiB and 20 KiB events, and does that argue for text-only v1?
6. **Clock trust.** How should gateways handle their own badly wrong clocks?
   Does Arti bootstrap fail with a skewed clock, and can that serve as a
   sanity signal? How should authors with wrong clocks be warned reliably?
7. **Persona keys and deletion.** Should per-post keys be kept until expiry so
   the author could issue a NIP-09 deletion? That trades seizure risk for a
   (relay-dependent) ability to retract.
8. **Wear verification.** Should pure-Kotlin Schnorr verification move into
   shared sources, so Wear can divert, store, sync and re-originate, or should
   Wear stay a legacy-style relay?
9. **Spoofed floods through legacy nodes.** No mitigation is possible on
   nodes we do not control. Is a lower origin TTL range (for example 3-5) a
   better trade-off than 4-7?
10. **Sync constants.** `docs/sync.md` and the code disagree on defaults (for
    example 100/256 in the docs vs 500/400 in
    `app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt:160-170`)
    and on the h64 recipe. The gateway TLV defines its own bounds and recipe,
    but `docs/sync.md` should be reconciled.
11. **First-hop linkage.** Are stronger mitigations worth their cost, such as
    rotating or withholding the scan-response and Wi-Fi Aware peer ID while
    originating, or handing the post to one neighbor over Noise and letting
    relays and sync carry it instead of repeated origination?
12. **Local Tor endpoint.** How can the gateway confirm that the loopback SOCKS
    port belongs to the in-process Arti instance rather than another app?
13. **Wrapped posts by non-authors.** Should a user be able to forward someone
    else's valid gateway post (already consented by its author) into a new
    partition? It is allowed implicitly by re-origination and sync; confirm
    that no explicit "reshare" is needed.
