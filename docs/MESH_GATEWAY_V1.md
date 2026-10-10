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
The feature reuses the existing Nostr event model and signer
(`app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt:12-20, 98-108`),
the existing relay publish path
(`app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt:446-475`),
and the existing mesh packet, fragmentation and relay machinery. It covers
Android phones as authors, relays and gateways; Wear OS as relays only; and iOS
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
| Local RF observer | Scans BLE and Wi-Fi Aware, records advertisements, timing and packet sizes | Outer packet carries no stable identity; random per-transmission sender ID; envelope padding; coarse timestamps. First-hop linkage is a stated residual risk. |
| Malicious mesh relay | Drops, delays, replays, edits or floods packets | Nostr id and BIP-340 signature checks, envelope signature over consent flags, event-id dedup, expiry, PoW, per-link rate limits |
| Malicious gateway | Edits, withholds, publishes over clearnet, logs, forges acks | Gateways cannot edit (signature) or re-attribute (no gateway identity). Withholding is countered by many gateways and author retries. Clearnet publishing is forbidden when the author requires Tor, and other gateways still publish. Acks are advisory. Logging rules are below. |
| Malicious Nostr relay | Rejects, censors, lies in `OK` frames, correlates by IP | Publish over Tor to several relays; `OK` is treated as unverified evidence; any online node can re-verify by event id |
| Spam / sybil | Generates many keys and posts | Per-post keys make per-author limits weak, so NIP-13 PoW, per-link and per-node budgets, and size and store caps are the primary controls |
| Device seizure | Forensic access to author, relay or gateway phone | Author outbox and keys in panic-cleared storage; relays keep carried posts in memory only; gateways keep no handoff logs |

### Residual risks (stated honestly)

- **First hop.** The author's phone transmits the first copy over a BLE or
  Wi-Fi Aware link. Its BLE scan response advertises the stable peer ID
  (`app/src/main/java/com/bitchat/android/mesh/BluetoothGattServerManager.kt:395-404`),
  and the peer ID never rotates except after a panic wipe
  (`app/src/main/java/com/bitchat/android/identity/SecureIdentityStateManager.kt:394-396`).
  A neighbor that receives the first transmission over a direct link can
  therefore suspect that the advertising device authored the post. A random
  outer sender ID removes the link from the packet itself, not from the radio
  link. Mitigations: a random delay before first transmission, and
  originating only once the author's device is also relaying other gateway
  posts. These are partial.
- **Content.** The post is public and permanent. Text, writing style and any
  geohash can identify the author regardless of transport.
- **Gateway location.** A gateway's Tor circuit hides its IP from relays. It
  does not hide from a local RF observer that the gateway device has internet,
  and a gateway that advertises a capability bit makes this worse (see
  [Gateway discovery](#gateway-discovery)).

### Fail-closed rules

A node must not store, display, relay (for new clients), sync, acknowledge or
publish a gateway post unless all of the following hold:

1. The envelope decodes completely, with no truncation, no unknown envelope
   version, no reserved flag bits and no non-zero padding.
2. The embedded event's id equals SHA-256 of its NIP-01 canonical
   serialization, and its BIP-340 signature verifies against its `pubkey`.
3. The envelope signature verifies against the same `pubkey`.
4. The event satisfies the [event profile](#event-profile) (kind, tag
   allow-list, size, character set, PoW).
5. The post is within its validity window per
   [Expiry and clock skew](#expiry-and-clock-skew).

If the author requires Tor, a gateway that cannot prove a Tor route must not
publish. It keeps the post and never silently falls back to a direct
connection. Any uncertainty is resolved by not publishing.

## Design principle: pre-signed, consented, verbatim

- The author's phone creates and Schnorr-signs a **complete** Nostr event
  (`id`, `pubkey`, `created_at`, `kind`, `tags`, `content`, `sig`) offline.
  Signing needs no network today: `NostrIdentity.signEvent` simply calls
  `NostrEvent.sign`
  (`app/src/main/java/com/bitchat/android/nostr/NostrIdentity.kt:78-80`;
  `app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt:98-108`).
- Gateways publish that event unchanged. They never re-sign, add tags, change
  `created_at` or wrap it. The existing publish path already accepts any
  `NostrEvent` and serializes its fields without re-signing
  (`app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt:446-475`;
  `app/src/main/java/com/bitchat/android/nostr/NostrRequest.kt:38-41, 64-70`).
  It performs no validation, so gateways must validate first.
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

### Requirement

- Gateway posts must not be signed with the account key or a per-geohash key.
  The account key links the post to the user's DM identity and, through
  favorites, to their mesh peer ID. A per-geohash key links the post to every
  geohash message the user has sent in that cell.
- **Default: a fresh per-post key.** The client generates a new secp256k1 key
  for each post (as `NostrIdentity.generate()` does today), mines PoW, signs the
  event and the envelope, and then erases the private key. Retries reuse the
  stored signed envelope byte for byte, so the key is never needed again.
  Justification: per-post keys are unlinkable across posts and from every mesh
  or Noise identity, and they leave nothing on the device that can sign more
  posts after seizure.
- **Optional: a persona key.** A user may create a named, stable "public
  persona" key to build reputation. It is stored in `bitchat_identity` with
  `storeSecureValue`, so `clearIdentityData`
  (`app/src/main/java/com/bitchat/android/identity/SecureIdentityStateManager.kt:468-481`)
  wipes it on panic. `NostrIdentityBridge.clearAllAssociations` must also
  remove it explicitly. The UI must state that every post under a persona is
  linkable to the others.
- Neither key may ever be sent over a Noise session, included in an
  announcement, or linked to the peer ID by any protocol message.
- The cost of per-post keys is that per-author rate limits are weak, because a
  spammer can also use a new key per post. PoW and per-link budgets carry the
  anti-spam load (see [Flood control](#flood-control)).

## Type assignments (PROPOSED)

| Layer | Name | Value | Purpose |
| --- | --- | --- | --- |
| Outer mesh packet | `MessageType.GATEWAY_POST` | `0x2A` | Author-signed Nostr event plus envelope, eligible for publishing |
| Outer mesh packet | `MessageType.GATEWAY_ACK` | `0x2B` | Advisory "reached the internet" report keyed by event id |
| Outer mesh packet | `MessageType.GATEWAY_INBOUND` | `0x2C` | Internet-origin event injected by a gateway; never re-published |
| `REQUEST_SYNC` TLV | `GATEWAY_POST_FILTER` | `0x04` | Optional GCS filter over gateway-post event ids |

Rationale: the values in use are `0x01`-`0x03`, `0x10`, `0x11`, `0x20`-`0x22` and
`0x29`. `fromValue` returns null for anything else and nothing throws
(`app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:13-28`). No
ranges are formally reserved. `0x23`-`0x28` sit next to types iOS may already
have used, so this draft takes the next values after `VOICE_FRAME`. Existing
`REQUEST_SYNC` TLVs are `0x01`-`0x03` (`docs/sync.md:133`).

Once the values are agreed, they are canonical. Implementations must not emit
these formats under any other type, and must not allocate aliases.

## Transport envelope

### Outer packet

A `GATEWAY_POST` is a normal mesh packet with:

- version `1` (version 3 or higher is dropped by every current decoder,
  `app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:409`);
- type `GATEWAY_POST (0x2A)`;
- a broadcast recipient: no recipient field, or the all-`FF` value (receivers
  accept both);
- origin TTL `7` (`MESSAGE_TTL_HOPS`,
  `app/src/main/java/com/bitchat/android/util/AppConstants.kt:10`);
- **senderID**: 8 bytes from a CSPRNG, fresh for every transmission and every
  retry. It is never the stable peer ID;
- **timestamp**: the transmission time truncated to whole seconds, with uniform
  random jitter in ±60 s, expressed in milliseconds. It is not used for
  freshness;
- **no outer signature** (`HAS_SIGNATURE` clear) and no source route;
- no undefined flag bits. Bits `0x10`-`0x80` are stripped on re-encode by
  current clients
  (`app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:213-218, 282-296`);
- `IS_COMPRESSED` allowed. `BinaryProtocol` may DEFLATE automatically, and
  relays preserve the originator's compressed bytes
  (`app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:38-57`).

Why no outer Ed25519 signature: current clients verify outer signatures only
against a signing key learned from the sender's verified announce
(`app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt:300-315`). An
offline author's announce is long gone by the time the post travels, and an
outer signature would tie the post to the stable mesh identity. Authenticity
comes from the Nostr signature and the envelope signature instead.

### `GatewayPostEnvelope` (payload of `0x2A`)

```text
+---------+--------+-------------+-------------+--------------+--------------+-----------+
| version | flags  | expiresAt   | eventLength | event        | envelopeSig  | padding   |
| UInt8   | UInt8  | UInt32 BE   | UInt16 BE   | eventLength  | 64 bytes     | 0..255    |
|  0x01   |        | unix secs   |             | UTF-8 JSON   | BIP-340      | zero bytes|
+---------+--------+-------------+-------------+--------------+--------------+-----------+
```

- `version`: `0x01`. Receivers must not store, display, ack or publish any
  other version. See [Versioning](#versioning) for relay behavior.
- `flags`:
  - `0x01 REQUIRE_TOR`: gateways must publish only over a proven Tor route.
    Clients set it by default.
  - `0x02 WANT_ACK`: the author asks for a `GATEWAY_ACK`.
  - `0x04`-`0x80`: reserved. They must be zero, and a non-zero reserved bit
    makes the envelope invalid. Because flags are signed, they cannot be
    silently ignored.
- `expiresAt`: last moment a gateway may publish. See
  [Expiry and clock skew](#expiry-and-clock-skew).
- `event`: one NIP-01 event JSON object containing all seven fields. This exact
  byte string is what the gateway publishes.
- `envelopeSig`: BIP-340 Schnorr signature by the event's `pubkey` over:

```text
envelopeHash = TaggedHash("bitchat/gateway-envelope/v1",
                          version || flags || expiresAt || eventId)
TaggedHash(tag, m) = SHA256(SHA256(tag) || SHA256(tag) || m)
eventId = the 32 raw bytes of the event id
```

  This binds the consent flags and expiry to the event without adding anything
  to the published event. The domain tag prevents the signature from being
  reused as a Nostr event signature.
- `padding`: zero bytes that bring the whole envelope to the next multiple of
  256 bytes. Receivers reject non-zero padding and more than 255 padding bytes.
  BLE pads only Noise frames
  (`app/src/main/java/com/bitchat/android/mesh/BLEPacketPaddingPolicy.kt:10-17`),
  so this in-envelope padding is what hides the exact post length on BLE.

Fixed overhead is 72 bytes plus padding.

### Event profile

The author's client must produce, and every validating node must enforce:

- `kind` = `1` (stored text note). Ephemeral kinds such as 20000 are rejected,
  because relays need not store them, so neither an ack nor a later
  verification by id would be meaningful.
- Allowed tags, in any order:
  - `["nonce", <decimal>, <target>]`: exactly one, NIP-13, target ≥ 16;
  - `["g", <geohash>]`: at most one, 2 to 5 characters (CITY or coarser,
    `app/src/main/java/com/bitchat/android/geohash/LocationChannel.kt:7-13`);
  - `["t", <hashtag>]`: at most 3, each at most 32 bytes, lowercase.
  Any other tag invalidates the post. In particular there is no `n`
  nickname tag, no client tag, and no `p`/`e` tags in v1.
- `content`: 1 to 2,000 Unicode scalar values, valid UTF-8. It must not contain
  U+2028, U+2029, or C0 control characters other than U+000A. This avoids a
  known divergence: `NostrEvent` computes ids through Gson
  (`app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt:122-146`), whose
  escaping of those characters differs from NIP-01's canonical form.
- `created_at`: see [Privacy](#privacy). It must fit the current 32-bit field
  (`app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt:15`).
- Serialized event size: at most 4,096 bytes for text-only posts, and at most
  20,480 bytes with a thumbnail.
- Id verification must use a NIP-01-exact canonical serializer. A mismatch is a
  rejection, never a re-serialization.

### PoW

- Every gateway post must carry NIP-13 PoW of at least **16** leading zero bits
  on its id, and the id must meet the nonce tag's committed target.
- PoW must be mined on the author's device before signing, with `created_at`
  held fixed at its jittered value. The existing `mineEvent` resets
  `created_at` to now on every iteration
  (`app/src/main/java/com/bitchat/android/nostr/NostrProofOfWork.kt:142-158`),
  so it cannot be used unchanged.
- Gateways and relays check PoW with the same rule as
  `NostrProofOfWork.validateDifficulty`. Under load, nodes may prefer
  higher-PoW posts when choosing what to evict or relay first, but they must
  accept any valid post at 16 bits or more.

### Size limits and fragmentation

- Envelope maximum: 72 + 20,480 = 20,552 bytes, padded to 20,736, which fits the v1
  16-bit payload length
  (`app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:229-236`).
- Packets above 512 bytes are split into unsigned `FRAGMENT (0x20)` packets of
  about 454-469 data bytes
  (`app/src/main/java/com/bitchat/android/mesh/FragmentManager.kt:63-158`;
  `app/src/main/java/com/bitchat/android/util/AppConstants.kt:37-46`). A
  text-only post (at most about 4.4 KB) uses at most 10 fragments, and a
  maximum-size post about 46, well under the 256-fragment cap.
- The reassembled packet is processed at TTL 0 and never relayed whole; only
  fragments propagate
  (`app/src/main/java/com/bitchat/android/mesh/FragmentManager.kt:270-275`).
  Every node that wants the post must reassemble it, and integrity comes only
  from the envelope and event signatures.
- **Compression-bomb rule:** before inflating a compressed `0x2A`, `0x2B` or
  `0x2C` payload, new receivers must reject a declared original size above the
  type's maximum (20,736 bytes for `0x2A`, 39 for `0x2B`, 2,304 for `0x2C`).
  This is stricter than the generic 10 MiB / 50,000:1 limits
  (`app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:501-539`).

### Dedup key

The dedup key for gateway posts is the 32-byte Nostr event id. The mesh
`PacketIdUtil` id covers sender, timestamp and payload
(`app/src/main/java/com/bitchat/android/sync/PacketIdUtil.kt:13-25`) and lasts
5 minutes (`app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt:51-104`).
Because every retry has a fresh sender ID and timestamp, the mesh id
deliberately differs per transmission. New clients keep an event-id seen set
that holds each id until `expiresAt + 10 min`, capped at 10,000 entries, in
memory only.

### Expiry and clock skew

- The author sets `expiresAt` to compose time + 24 h by default. The user may
  choose 6 h, 24 h or 72 h.
- Valid only if `expiresAt − created_at` ≤ 72 h + 10 min (the jitter allowance).
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
origin TTL 7.

- `flags`: `0x01 VIA_TOR` (the gateway published over Tor); other bits are
  reserved and must be zero.
- `relaysAccepted`: the number of distinct relays that answered
  `["OK", id, true, ...]`, including `duplicate:` replies, capped at 255.
- `reportedAt`: rounded down to 10 minutes.

### What an ack proves (nothing cryptographic)

Nostr relays do not sign `OK` frames, so an ack is **advisory**. Anyone can
forge one. The rules follow from that:

- An ack carries no gateway identity and no author peer ID. The author never
  answers it, so it creates no online/location oracle
  (`docs/security-review-jul-27.md` H6).
- A node relays an ack only for an event id it holds in its store or seen set.
  It relays each event id at most once per 10 minutes, and the ack is never
  stored or synced.
- The author's UI shows "Reported reached the internet (unverified)". It shows
  "Verified on relay" only after the author's own device, once online, fetches
  the event by id from a relay and checks its signature.
- An ack reduces author retries (see [Author lifecycle](#author-lifecycle)) but
  never stops them, and **gateways must never skip publishing because of a mesh
  ack.** A gateway may skip only when it has itself fetched the event by id
  from a relay over the same route and verified it. This defeats suppression
  by forged acks.
- A gateway sends an ack only when `WANT_ACK` is set, and only after at least
  one relay accepted.

## `GatewayInbound` (payload of `0x2C`, optional)

```text
+---------+--------+-------------+--------------+-----------+
| version | flags  | eventLength | event        | padding   |
| UInt8   | UInt8  | UInt16 BE   | eventLength  | 0..255    |
|  0x01   |  0x00  |             | UTF-8 JSON   | zero bytes|
+---------+--------+-------------+--------------+-----------+
```

- Event limit 2,048 bytes, text only: kind 1, and the tag allow-list above
  except that PoW is not required. The envelope is at most 2,304 bytes after
  padding to a multiple of 256.
- Origin TTL `3`.
- Receivers verify the id and signature before displaying the post or marking
  it seen. The relay-layer dedup in `NostrRelayManager` runs before signature
  checks (`app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt:934-948`),
  so gateway code must not mark an inbound id as seen until it has verified it.
- **Inbound copies never count as published.** A node must not treat an
  event id seen in a `0x2C` packet as handled for publishing. Packet types are
  not authenticated, so this prevents an attacker from suppressing a real
  `0x2A` by replaying its event as `0x2C`. Gateways never publish `0x2C`
  contents.
- Shown in a separate "From the internet" feed and labelled as such. Media and
  links are never fetched automatically.

## Author lifecycle

1. The user composes a post. "Share to internet" defaults to **off**.
2. On send with the toggle on, and only then:
   1. validate content against the event profile;
   2. generate a per-post key (or load the persona key);
   3. compute the jittered `created_at` and mine PoW;
   4. sign the event, then the envelope;
   5. erase the per-post private key;
   6. write the envelope to the outbox.
   If the toggle is turned off at any point before step 4 completes, abort, and
   do not sign. A user may delete a queued post before it leaves the device.
   Deleting it removes the outbox entry and the event-id record, and the
   client never transmits it.
3. First transmission happens after a uniform random delay of 5-60 s.
4. Retries reuse the same envelope with a new senderID and timestamp at 5, 10,
   20, 40 and then every 60 minutes, at most 12 transmissions, until
   `expiresAt`. After an advisory ack, at most 2 further retries are sent
   (spaced at least 30 minutes apart), then retries stop. After "Verified on
   relay", retries stop immediately.
5. The outbox holds at most 20 posts and at most 512 KiB. It is persisted
   encrypted, either in `bitchat_identity` or in a dedicated directory added to
   `FileUtils.clearAllMedia`
   (`app/src/main/java/com/bitchat/android/features/file/FileUtils.kt:283-300`),
   so that panic wipes it. It survives process death and reboot.
6. Panic wipe erases the outbox, persona key, seen set and relay store. Copies
   already transmitted to other devices cannot be recalled, and the panic UI
   must say so.

## Mesh propagation and storage

### Relay rules for new clients

On receiving a reassembled `0x2A`:

1. Apply the fail-closed rules. If any check fails, drop the packet and do not
   relay it.
2. If the event id is already in the seen set, do not relay it. Exception: a
   post with no ack seen may be re-flooded at most once per 10 minutes, so that
   author retries can still cross legacy paths. Otherwise, record the id.
3. Charge the per-link and per-node budgets (see
   [Flood control](#flood-control)). Over budget, store the post but do not
   relay it.
4. Relay through the normal path
   (`app/src/main/java/com/bitchat/android/mesh/PacketRelayManager.kt:40-115`)
   at the **lowest priority**, behind chat, announce, Noise and voice traffic,
   with 50-250 ms random jitter.
5. Store the post in the relay store.

Validation runs on the reassembled packet. Fragments themselves cannot be
validated, and current clients relay them for any `originalType`
(`app/src/main/java/com/bitchat/android/mesh/PacketProcessor.kt:141, 209-220`).
A new client that rejects a reassembled post should also stop relaying further
fragments of that fragment ID. See Open questions.

`TransportBridgeService` decrements TTL a second time when bridging BLE and
Wi-Fi Aware
(`app/src/main/java/com/bitchat/android/service/TransportBridgeService.kt:88-112, 203-226`),
so the effective radius through bridge nodes is smaller than 7 hops. Gossip sync
compensates.

### Relay store

- In memory only. Posts carried for others are never written to disk, so device
  seizure of a relay reveals nothing after process death or panic.
- Caps: 200 posts and 2 MiB, whichever is reached first.
- Eviction order: expired posts first, then the earliest `expiresAt`, then the
  lowest PoW, then the oldest insertion. Eviction never fails a receive. A full
  store simply evicts.
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
posts do **not** join the existing filter. They use a separate optional TLV:

```text
[0x04][length UInt16 BE][P UInt8][M UInt32 BE][GCS bytes]
```

- Elements are h64 = the first 8 bytes of SHA-256(eventId 32 bytes), reduced
  mod M exactly as in `docs/sync.md`. GCS bytes are at most 512.
- The current decoder skips unknown TLV types
  (`app/src/main/java/com/bitchat/android/model/RequestSyncPacket.kt:53-72`), so
  old responders ignore the TLV and answer only the base filter.
- A responder sends the stored gateway posts missing from the requester's
  filter, newest `expiresAt` first. It sends at most 20 posts per exchange,
  paced at least 500 ms apart, as unicast with TTL 0. The existing responder
  has no pacing
  (`app/src/main/java/com/bitchat/android/sync/GossipSyncManager.kt:177-208`);
  this is new.
- Gateway-post sync does not run in `ULTRA_LOW_POWER`.

### Power

- Follow `PowerManager.profile`
  (`app/src/main/java/com/bitchat/android/mesh/PowerManager.kt:61-77`). In
  `ULTRA_LOW_POWER` a node stores and serves sync on direct request, but does
  not flood-relay gateway posts. In `POWER_SAVER`, per-node budgets are halved.
- The author's outbox retries are skipped while the battery is CRITICAL
  (10% or less, `app/src/main/java/com/bitchat/android/util/AppConstants.kt:80-83`)
  unless the device is charging.

## Flood control

Receiver policy (not wire fields) for Android/Wear v1:

- **PoW** ≥ 16 bits on every `0x2A`.
- **Per incoming link** (the neighbor connection a packet arrived on, which
  requires no identity): at most 6 new valid `0x2A` per minute and 60 per hour.
  Excess posts are dropped, neither stored nor relayed.
- **Per node relay budget:** at most 30 `0x2A` relays per minute, 1 `0x2B` per
  event id per 10 minutes, and 2 `0x2C` per minute.
- **Per pubkey:** at most 10 stored posts. This only matters for persona keys.
- **Reassembly:** gateway posts share the existing limits (64 active sets,
  4 MiB global, 30 s timeout,
  `app/src/main/java/com/bitchat/android/util/AppConstants.kt:37-46`). New
  clients should cap concurrent gateway-type reassemblies at 16 so posts cannot
  starve chat reassembly.
- **Gateway publish budget:** at most 30 events per minute and 600 per hour per
  gateway, so a flood cannot get the gateway's Tor circuits or relays
  rate-limited or banned.

Legacy nodes apply none of this. Any attacker can already flood unknown
broadcast types through them (`docs/security-review-jul-27.md` H10/H11). That
risk exists with or without this feature.

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

### Readiness and Tor semantics

A gateway may publish only when all of the following hold:

1. `TorMode.ON`
   (`app/src/main/java/com/bitchat/android/net/TorPreferenceManager.kt:18-25`),
   `ArtiTorManager.isProxyEnabled()`
   (`app/src/main/java/com/bitchat/android/net/ArtiTorManager.kt:116-120`) and
   the OkHttp route resolving to `Route.TOR`, never `Route.DIRECT`
   (`app/src/main/java/com/bitchat/android/net/OkHttpProvider.kt:69-80, 109-120`).
2. At least one target relay socket is actually connected. Sockets back off for
   up to 300 s with no connectivity callback
   (`app/src/main/java/com/bitchat/android/nostr/RelayReconnectPolicy.kt:10-44`).

Today, with Tor off, Nostr silently goes direct
(`app/src/main/java/com/bitchat/android/net/OkHttpProvider.kt:119`). The
gateway feature must not inherit that:

- **`REQUIRE_TOR` set:** never publish without a proven Tor route. If Tor is off,
  bootstrapping or blocked, keep the post until it expires and retry when Tor
  becomes ready. Never downgrade.
- **`REQUIRE_TOR` clear:** still publish only over Tor, unless the gateway user
  has also enabled a separate "Allow clearnet gateway publishing" setting
  (default off, with a warning that the gateway's IP becomes tied to other
  people's posts).
- The route must be checked immediately before each send. If Tor drops
  mid-batch, the remaining posts wait.

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

### Publish-once and backoff

- Use a dedicated gateway queue, separate from the shared 500-entry
  `NostrPendingEventQueue`
  (`app/src/main/java/com/bitchat/android/nostr/NostrPendingEventQueue.kt:42`),
  so mesh posts cannot evict the user's own DMs. Cap it at 100 entries, keyed
  by event id, in memory only.
- Before publishing, wait a random 0-30 s, then send `REQ` with
  `{"ids":[<id>]}` to one target relay. If a valid event with that id comes
  back, mark it done without publishing.
- Publish by sending `["EVENT", <event bytes from the envelope, verbatim>]`.
  This needs a raw-send API: the current path re-serializes through Gson, which
  keeps the id and signature valid but is not byte-identical.
- Success means at least one `["OK", id, true, ...]`, including a `duplicate:`
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

Gateways must not log, persist or transmit:

- the radio link, BLE address, RSSI, Wi-Fi Aware peer, or outer senderID a post
  arrived from;
- the arrival time of a post;
- content, pubkey, or event id in logcat;
- which relays accepted which event, beyond the in-memory queue entry.

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

## Privacy

- Use synthetic, non-identifying data only in tests, vectors, screenshots and
  bug reports, per `AGENTS.md`.
- `created_at` = compose time truncated to the minute, minus a uniform random
  0-15 minutes. Public builders currently use the exact second
  (`app/src/main/java/com/bitchat/android/nostr/NostrProtocol.kt:122`).
- The outer packet timestamp is jittered by ±60 s, and the outer senderID is
  random per transmission (see [Outer packet](#outer-packet)).
- Geohash is optional, off by default, chosen manually, and at most 5
  characters. It is never derived automatically from live location unless a
  `LiveLocationPrivacyGate` token is held
  (`app/src/main/java/com/bitchat/android/geohash/LiveLocationPrivacyGate.kt:54-98`),
  and it is never BUILDING precision.
- No nickname, client tag or device information appears in the event.
- The author key is per-post by default (see [Author key](#author-key)).
- Gateway inbound subscriptions use only user-chosen hashtags or coarse
  geohashes, and are torn down using the `NostrLiveSubscriptionPrivacy`
  close-targets pattern
  (`app/src/main/java/com/bitchat/android/nostr/NostrLiveSubscriptionPrivacy.kt:3-12`).
- Raw logs, Mesh Lab evidence and screenshots stay local.

## UI requirements

- **Composer:** a "Share to internet" toggle for each post, default off. When
  turned on, show this blocking warning: "Internet posts are public and
  permanent. Anyone can copy them, and they cannot be deleted. Do not include
  anything that identifies you." Show the expiry choice and the "Require Tor"
  option, which is on and locked unless the user opens advanced settings.
- **Feed:** a "Gateway posts" feed separate from mesh chat. Each post shows the
  short key fingerprint (npub prefix), never a display name. The UI states that
  names are worthless for identity.
- **Status per own post:** Queued on this phone → Sent to mesh (n attempts) →
  Reported reached internet (unverified) → Verified on relay; or Expired.
- **Gateway settings:** the two toggles, the clearnet override, live counts
  (queued, published, waiting for Tor), and a clear "waiting for Tor" state
  instead of any silent fallback.
- **Panic:** a notice that posts already handed to the mesh cannot be recalled.
- Visible changes need before/after screenshots made with synthetic data.

## Versioning

- The outer packet stays at version 1. No new header flag bits are used.
- The envelope `version` byte governs the payload. A new client receiving an
  unknown envelope version relays it live under the `0x2A` budgets, exactly as
  a legacy node would. It never stores, syncs, displays, acks or publishes it.
- Additive changes need a new envelope version, never a reinterpretation of
  reserved bits.

## Compatibility summary

- **New Android to new Android/Wear:** full validation, store, sync TLV,
  budgets, gateway publishing (phones only).
- **New Android to older Android/Wear:** older nodes relay `0x2A`/`0x2B`/`0x2C`
  as unknown broadcast types without any check
  (`app/src/main/java/com/bitchat/android/mesh/PacketProcessor.kt:119-167`;
  `app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt:256-264`),
  including their fragments. They do not display, store or sync them
  (`app/src/main/java/com/bitchat/android/sync/GossipSyncManager.kt:105-110`),
  and they ignore TLV `0x04`. Consequence: legacy nodes are live-flood-only
  carriers, so author retries matter on legacy paths. They also forward
  invalid or forged posts, which new nodes then reject.
- **Wear:** shares protocol, mesh and sync code (`wear/build.gradle.kts:88-128`),
  so it relays like a phone. It cannot verify Schnorr signatures unless
  verification code is added to the shared include list. Until then, new Wear
  builds relay live only and do not store or sync gateway posts.
- **iOS:** **unknown.** The iOS source is not in this repository.
  `docs/LIVE_VOICE_V1.md:41-42` says only that older clients "ignore" `0x29`.
  If iOS drops unknown types instead of relaying them, iOS nodes are holes in
  the flood but cause no harm. This must be confirmed by iOS maintainers or a
  Mesh Lab interop run before release.
- **Ordinary Nostr clients:** see a normal kind 1 note with a `nonce` tag and
  optionally `g`/`t` tags.

## Test plan

Types: **U** = JVM unit (JUnit 4 / Robolectric, injected clock, randomness and
transport, no public relays), **S** = JVM multi-node simulation with in-memory
transports and a local fake relay, **M** = Mesh Lab on physical devices with a
disposable local relay and Tor fixture (`docs/release-gate-runbook.md`). Tests
are named by observable behavior. Negative tests assert fail-closed outcomes.

### 1. Safety (must never fail)

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 1.1 | Ordinary `MESSAGE` with text identical to a post reaches a gateway | Never published; gateway publisher rejects every type except validated `0x2A` | U, S |
| 1.2 | Modified gateway tries to publish a post the author did not mark | Impossible: no signed event exists; the test asserts that no signing call happens with the toggle off | U |
| 1.3 | Toggle switched off before send / during PoW mining | No event signed, key erased, outbox empty, zero transmissions | U |
| 1.4 | Thumbnail from a source with GPS EXIF | Signed event contains no APP1/GPS; composer refuses a crafted JPEG with EXIF | U |
| 1.5 | Linkage: post `created_at` and outer timestamp vs author's exact send time and peer ID | `created_at` on a minute boundary minus jitter; outer senderID ≠ peer ID and differs per retry | U |
| 1.6 | Panic with 20 queued posts and a populated relay store | Outbox, persona key, staged media, relay store, seen set and gateway queue are all empty, including after restart | U, M |
| 1.7 | Seized gateway: dump logcat and app storage after publishing synthetic posts | No pubkey, event id, senderID, link address or arrival time in logs or on disk | M |
| 1.8 | `REQUIRE_TOR` with Tor OFF / bootstrapping / blocked | Zero relay connections for the post; post remains queued | U, M |
| 1.9 | Attacker clears `REQUIRE_TOR` in transit | Envelope signature fails; dropped and not relayed | U |

### 2. Tampering

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 2.1 | One character of content, tag, `created_at` or sig edited | Id or signature mismatch; dropped, not relayed, not published | U |
| 2.2 | Event swapped into another envelope | Envelope signature fails | U |
| 2.3 | Replay after expiry, and replay inside the window | Expired: rejected. Inside: dedup by event id, no re-flood within 10 min | U, S |
| 2.4 | Same text under a different key claiming the same "name" | Shown as a different fingerprint; no name field exists | U |
| 2.5 | Forged `0x2B` for an unpublished post | Author shows unverified status only and keeps reduced retries; gateways still publish | U, S |
| 2.6 | `0x2C` replay of a pending `0x2A` event | Gateway still publishes the `0x2A` | U |
| 2.7 | Truncation at every byte, wrong `eventLength`, non-zero padding, reserved flag set, unknown version, invalid UTF-8, forbidden tag, U+2028 in content | Rejected without crash | U |
| 2.8 | Oversized event (4,097 bytes text-only), 257-fragment plan, compression bomb declaring 10 MiB | Rejected before allocation or inflation | U |
| 2.9 | PoW below 16, or nonce target lower than actual requirement | Rejected | U |

### 3. Flooding

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 3.1 | 10,000 valid posts from one persona key | ≤ 10 stored for that key; per-link cap holds; chat latency stays within its budget | S |
| 3.2 | 1,000 sybil per-post keys, valid PoW | Per-link and per-node budgets bound relays; store stays ≤ 200 / 2 MiB; higher PoW is retained | S |
| 3.3 | Malicious gateway injects `0x2C` at line rate | New nodes relay ≤ 2/min; BLE chat delivery still succeeds | S, M |
| 3.4 | Relay store full with continuous arrivals | Eviction order as specified; no crash, no OOM | U |
| 3.5 | Gateway receives 5,000 valid posts | Publishes ≤ 30/min; queue ≤ 100; DM queue unaffected | U |

### 4. Network

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 4.1 | 1,000-node crowd, 1% gateways | Post reaches a gateway; bounded total transmissions per post | S |
| 4.2 | Single gateway flapping online/offline | Exactly one event at the fake relay; backoff schedule respected | S, M |
| 4.3 | 50 gateways receive the same post | Fake relay stores one event id; duplicate `OK`s counted as success | S |
| 4.4 | Two partitions merge after 2 h | Sync TLV delivers missing posts, paced ≤ 20 per exchange | S, M |
| 4.5 | Post delivered to a gateway 6 h after composing (24 h expiry) | Published | S |
| 4.6 | Mixed new Android, legacy Android, Wear, iOS | Legacy Android/Wear relay fragments; iOS behavior recorded | M |
| 4.7 | Tor blocked, `REQUIRE_TOR` clear, clearnet override off | Not published | U, M |
| 4.8 | All relays unreachable | Post stays queued and is retried until expiry; never dropped early | S |
| 4.9 | Path through a BLE↔Wi-Fi bridge node | Delivery succeeds despite the double TTL decrement, via sync | M |

### 5. Device

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 5.1 | Kill / reboot author mid-retry | Outbox survives; retries resume with a new senderID | U, M |
| 5.2 | Kill gateway mid-publish | Post re-received from mesh and published; relay sees one event | M |
| 5.3 | Battery saver / CRITICAL battery | Relay and retry behavior follow the power rules | U, M |
| 5.4 | Low-memory old phone | Caps hold; reassembly never exceeds global limits | M |
| 5.5 | Author clock off by days or years | Warning before signing; gateways reject out-of-window posts; no crash | U |
| 5.6 | Gateway clock off by days | Validity checks reject; nothing is published outside the window | U |
| 5.7 | BLE permission denied / no Wi-Fi Aware hardware | Composer queues the post and explains why nothing is sent; no crash | U, M |

### Release-gate entries

Add device scenarios to `tools/release_gate/scenarios.json` under the next free
`A2A-`/`XCL-` IDs, with capabilities `local-relay` and `tor` and aggregate
evidence keys only, such as `event-id-count`, `publish-count` and `ack-count`.
Add debug-only `TestHookDriver` commands in `app/src/debug/`:
`gateway_post_send`, `gateway_status` and `gateway_store_counts` (counts only).
Add a `scenario_gateway_post` entry to the `SCENARIOS` dict in
`tools/release_gate/mesh_lab.py`, and to `WATCH_SCENARIOS` for the watch relay
case. Mesh Lab today has no relay or Tor plumbing, so a local relay fixture
must be added first.

## Golden vectors

All keys and data below are synthetic. The author secret key is the scalar `3`,
the first BIP-340 test-vector key; it is public knowledge and must never be
used for real posts. Signatures are BIP-340 with `aux_rand` = 32 zero bytes.
Android's signer adds random nonce input
(`app/src/main/java/com/bitchat/android/nostr/NostrCrypto.kt:530-560`), so
Android tests must **verify** these signatures and compare all other bytes
literally. They must not expect Android to reproduce the signatures.

```text
# author x-only pubkey (secret key = 3)
f9308a019258c31049344f85f89d5229b531c845836f99b08601f113bce036f9

# NIP-01 canonical serialization (UTF-8)
[0,"f9308a019258c31049344f85f89d5229b531c845836f99b08601f113bce036f9",1700000000,1,[["nonce","55500","16"]],"Synthetic gateway post. Test data only."]

# event id (16 leading zero bits)
00005452b9eeac2f3ac54a033fc15940cc9f0b73db0d9e317f50965368b6704a

# event sig
6b4766864dff4133a1c0c63b248021d37c053ca3167d9b20b60b9c12a0ee072bf540d79785b76311dd28433b87b57fc805ec9b0629c2427be4ee91e473d7e145

# event JSON as carried (403 bytes)
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
# padding bytes: total envelope length 512

# GatewayAck: version 01, flags 01 (VIA_TOR), eventId, relaysAccepted 3,
# reportedAt 1700003400
01 01 00 00 54 52 B9 EE AC 2F 3A C5 4A 03 3F C1 59 40 CC 9F 0B 73 DB 0D
9E 31 7F 50 96 53 68 B6 70 4A 03 65 53 FE 48

# gateway-post GCS element input h64 = SHA-256(eventId)[0..8]
0A BD E2 04 8D 55 26 C2
```

Executable vectors to add, following
`app/src/test/kotlin/com/bitchat/android/contracts/ClientRewriteWireContractTest.kt`
(inline hex, private `hex()` helper, `assertArrayEquals`, backtick names):

- `app/src/test/kotlin/com/bitchat/android/contracts/ClientRewriteNostrContractTest.kt`
  (new; it is already listed in `docs/client-rewrite-contracts.md:18-20` but
  does not exist): `` `NIP-01 serialization and event id match canonical bytes` ``,
  `` `BIP-340 signature vector verifies` `` (BIP-340 vectors 0-3 plus the
  vector above), and `` `content with U+2028 is rejected by the gateway profile` ``.
- `app/src/test/kotlin/com/bitchat/android/gateway/GatewayPostEnvelopeTest.kt`:
  `` `envelope encoder matches canonical bytes` ``,
  `` `envelope signature vector verifies` ``,
  `` `decoder rejects every truncation` ``,
  `` `reserved flags and nonzero padding are rejected` ``.
- `app/src/test/kotlin/com/bitchat/android/gateway/GatewayAckTest.kt`:
  `` `ack encoder matches canonical bytes` ``.
- `app/src/test/kotlin/com/bitchat/android/contracts/ClientRewriteWireContractTest.kt`:
  `` `request sync with gateway filter TLV is skipped by legacy decoder` `` and
  a full outer `0x2A` packet vector with a fixed synthetic senderID
  `01 02 03 04 05 06 07 08`.
- `app/src/test/kotlin/com/bitchat/android/mesh/PacketRelayManagerTest.kt` (or
  a new `PacketProcessor` test): `` `unknown broadcast type is relayed with TTL decremented` ``.
  No such test exists today.

Vectors change only with an intentional, coordinated version bump
(`docs/client-rewrite-contracts.md:23-32`). Cross-client parity is checked by
release-gate scenario XCL-006.

## Implementation references

- `app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt`
- `app/src/main/java/com/bitchat/android/mesh/PacketProcessor.kt`
- `app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt`
- `app/src/main/java/com/bitchat/android/mesh/PacketRelayManager.kt`
- `app/src/main/java/com/bitchat/android/mesh/FragmentManager.kt`
- `app/src/main/java/com/bitchat/android/sync/GossipSyncManager.kt`
- `app/src/main/java/com/bitchat/android/model/RequestSyncPacket.kt`
- `app/src/main/java/com/bitchat/android/nostr/NostrEvent.kt`
- `app/src/main/java/com/bitchat/android/nostr/NostrCrypto.kt`
- `app/src/main/java/com/bitchat/android/nostr/NostrProofOfWork.kt`
- `app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt`
- `app/src/main/java/com/bitchat/android/net/ArtiTorManager.kt`
- `app/src/main/java/com/bitchat/android/net/OkHttpProvider.kt`
- `app/src/main/java/com/bitchat/android/identity/SecureIdentityStateManager.kt`
- `app/src/main/java/com/bitchat/android/features/media/ImageUtils.kt`
- `wear/build.gradle.kts` (shared-source include list)
- `docs/sync.md`, `docs/LIVE_VOICE_V1.md`, `docs/client-rewrite-contracts.md`

## Open questions

1. **Type values.** `0x2A`/`0x2B`/`0x2C` and TLV `0x04` need agreement with iOS.
   Does iOS already use any of `0x23`-`0x2C`?
2. **iOS relay behavior.** Does iOS relay unknown broadcast types and their
   fragments? It cannot be determined from this repository.
3. **Capability bit 2.** What does `GATEWAY` mean on iOS today, and should v1
   ever advertise it, given the RF-observer risk?
4. **Thumbnail encoding.** There is no Nostr-standard inline image. Options are
   a `data:` URI in content (rendered poorly by other clients, and relays may
   cap event size) or text-only v1. What event sizes do the default relays
   accept?
5. **Raw publish.** Should the gateway send the envelope's JSON bytes verbatim
   (new API), or is Gson re-serialization of the same fields acceptable? The
   signature stays valid either way.
6. **Fragment-level filtering.** Should new nodes stop relaying fragments of a
   fragment ID whose reassembly failed validation? Fragments are relayed before
   reassembly completes, so the benefit is limited.
7. **Clock trust.** How should gateways handle their own badly wrong clocks?
   Does Arti bootstrap fail with a skewed clock, and can that serve as a
   sanity signal? How should authors with wrong clocks be warned reliably?
8. **Persona keys and deletion.** Should per-post keys be kept until expiry so
   the author could issue a NIP-09 deletion? That trades seizure risk for a
   (relay-dependent) ability to retract.
9. **Wear verification.** Should pure-Kotlin Schnorr verification move into
   shared sources, so Wear can store and sync, or should Wear stay a live-only
   relay?
10. **Spoofed floods through legacy nodes.** No mitigation is possible on
    nodes we do not control. Is a lower origin TTL (for example 5) a better
    trade-off than 7?
11. **Sync constants.** `docs/sync.md` and the code disagree on defaults (for
    example 100/256 in the docs vs 500/400 in
    `app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt:160-170`).
    The gateway TLV defines its own bounds, but the base values should be
    reconciled.
12. **First-hop linkage.** Are stronger mitigations worth their cost, such as
    rotating or withholding the scan-response peer ID while originating, or
    handing the post to a neighbor over Noise first?
13. **Wrapped posts by non-authors.** Should a user be able to forward someone
    else's valid gateway post (already consented by its author) into a new
    partition? It is allowed implicitly by relaying and sync; confirm that no
    explicit "reshare" is needed.
