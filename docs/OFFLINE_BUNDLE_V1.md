# Offline bundle transfer v1

Status: **draft / proposed**. Every magic number, type value, flag bit, MIME
type, file extension and QR profile in this document is PROPOSED. None of them
is a mesh wire value, but they must still be coordinated with the upstream
bitchat iOS and Android maintainers before any client emits them, so that a
single bundle format serves both clients.

An offline bundle is one portable file that carries already-signed, already-
consented public gateway posts
([Mesh gateway posts v1](MESH_GATEWAY_V1.md)) from one device to another
**without any radio mesh**. A bundle can move in three ways:

1. **Animated QR.** One screen plays a sequence of QR codes and another
   device's camera reads them. Each frame carries one fountain-code symbol, so
   the order of frames and any missed frames do not matter. Light cannot be
   jammed by radio.
2. **Hand-off.** The Android share sheet, Quick Share, AirDrop, or any app
   that moves files.
3. **Save and share later.** A file kept on the device or saved elsewhere,
   carried physically and imported later.

The receiving device checks every item on its own, exactly as it would check
a post that arrived over the mesh. Items that pass are fed into the normal
gateway relay store and forwarding path, under the same budgets plus the
`IMPORT` admission rules in [Flood control](#flood-control). The bundle is
only a carrier. It adds no trust, takes away no checks, and carries no
identity field for the person who made it. Its contents can still hint at
where and when its carrier has been (see
[Residual risks](#residual-risks-stated-honestly)).

All multi-byte integers in this document use network byte order (big-endian).
Unix times are seconds since 1970-01-01T00:00:00Z. Paths are
repository-relative.

## Purpose and scope

### Goals

- Let a person carry public posts across a gap that the mesh cannot cross:
  from a crowd to a device that has internet, between two partitions, or past
  a jammed area.
- Keep every item independently verifiable. A bundle can be split, merged,
  re-exported or partially lost without changing whether any single post is
  valid.
- Reuse the [MESH_GATEWAY_V1](MESH_GATEWAY_V1.md) validator, seen set, relay
  store, forwarding model and flood control. Their rules apply unchanged to
  each item, except for the documented deviations for the synthetic `IMPORT`
  link listed in
  [Amendments to MESH_GATEWAY_V1](#amendments-to-mesh_gateway_v1).
- Put no device or person metadata in the file: no device name, no creation
  time field, no file-system metadata, and a size that reveals only a coarse
  size class. The posts themselves are not neutral: their `g` tags and
  timestamps imply the carrier's recent region and an approximate export time
  (see [Residual risks](#residual-risks-stated-honestly)).
- Fail closed. A bundle or frame that is malformed, truncated, oversized or
  inconsistent is rejected with no partial effect.
- Keep import to one action: scan or open, then one confirming tap.

### Non-goals

- Not a way to move private messages, Noise traffic, DMs, contact lists,
  identities, keys or settings. See [Bundle contents](#bundle-contents).
- Not a way to move ordinary public mesh chat (`MESSAGE 0x02`), files
  (`0x22`), voice (`0x29`), geohash chat or location notes. Those carry the
  device's stable signature or were never consented for wider reach.
- Not a backup or sync format, and not a replacement for the mesh. Posts that
  can travel over the mesh should travel over the mesh.
- Not an app-update channel. Signed app-update bundles are a separate future
  type with their own specification; see
  [Future: signed app-update bundles](#future-signed-app-update-bundles).
- Not a high-throughput optical modem. v1 favours robustness on low-end
  cameras over the throughput of colour codes.
- Not supported on Wear OS in v1 (see [Compatibility](#compatibility)).

## Threat model

The adversaries of [MESH_GATEWAY_V1 Threat model](MESH_GATEWAY_V1.md#threat-model)
still apply. A bundle adds the following.

| Adversary | Capability | Design response |
| --- | --- | --- |
| Hostile bundle author | Crafts malformed, truncated, oversized, padded or deeply nested files; fills a bundle with forged, expired, duplicate or maximum-size posts; grinds event ids to win ordering | Strict container parser with fixed caps checked before allocation; no compression; trailing bytes and unknown values rejected; every item runs the full gateway fail-closed sequence; ordering fixed by event id and checked; store admission chosen at random, not by bundle order; `IMPORT` limited by a slot quota and a byte quota and never evicts another post to make room (see [Flood control](#flood-control)) |
| Hostile app on the same phone | Fires share or view intents with large, hostile or never-ending content, repeatedly; passes `file://` URIs or URIs for BuzzChat's own `FileProvider`; draws an overlay over the confirmation screen | Content-scheme URIs from other apps only; size cap and no-progress deadline enforced while reading; no pending queue; one generic error for every intent reject; nothing is imported without a tap on a confirmation screen that appears only after verification and ignores obscured touches (see [Share-sheet integration](#share-sheet-integration-on-android)) |
| Hostile screen in camera view, same transfer | Shows frames with the same transfer id but wrong symbols, to break decoding | Per-frame CRC, first copy of each symbol id wins, a conflicting copy is counted, and the whole-bundle digest plus the full container parse must pass before anything is imported. The worst result is a failed transfer, never a wrong import |
| Competing screen, different transfer | A hostile screen, or simply a second honest giver nearby, is seen first and captures the receiver with a different, internally valid bundle | The receiver reads the largest code in view and re-locks automatically when the locked transfer stops progressing (see [Receiver behaviour](#receiver-behaviour-and-partial-receive)). The confirmation screen previews the posts before anything is added. A captured receiver can still import a valid bundle the user did not intend; it cannot push out posts already held, because `IMPORT` never evicts |
| Bystander with a camera | Films the animated QR or the screen | Bundles carry only public posts. Anyone who films the code gets the same posts, which is harmless by construction. Being seen carrying posts, and the matchable transfer id, are residual risks, stated below |
| Device seizure | Forensic access to a phone that exported or imported a bundle | Staged export file in a panic-wiped cache directory with verified deletion and a bounded lifetime; bundles that BuzzChat reads from a URI are held in memory only; imported posts held in the in-memory relay store; no per-bundle logs. Received bundle files kept by other apps, and the staged file before its sweep, are residual risks, stated below |
| File-transfer service | AirDrop, Quick Share or a messaging app records sender name, device name, time and file name, or scans content by hash or signature | Default file name, fixed internal layout, size classes, no metadata field inside the file. Transport metadata, detectability and hash matching are outside the bundle and are residual risks, stated below |
| Fake hub | Sets up a lookalike hub QR and serves its own pages and APK over plain HTTP | Out of scope for v1 bundles, which are never served by the hub. The existing hub APK path has no integrity check; see the residual risk below |

### Residual risks (stated honestly)

- **Shoulder surfing and filming.** Posts in a bundle are public. An animated
  QR shown in a crowd can be read by any camera that can see it. This is
  acceptable only because nothing private may ever be put in a bundle. What a
  camera does reveal is that **this person, at this place and time, is
  carrying these posts**. Under a hostile authority, carrying protest posts can
  be dangerous on its own. The UI says so.
- **Carrier linkage.** The person who receives a bundle knows who handed it
  over. If the exporter includes their own posts that are not yet circulating,
  the receiver can guess authorship. Export therefore excludes own unreleased
  posts by default (see [Export](#export)).
- **Hand-off metadata.** AirDrop and Quick Share show the sender's device name
  to nearby receivers, and publicly reported research has shown that AirDrop's
  contact-discovery hashes can be reversed to phone numbers or email
  addresses. Messaging apps keep their own logs. None of this is controlled by
  BuzzChat. The UI recommends the animated QR, or a device name that does not
  identify the person, when this matters.
- **Saved copies.** A bundle saved with "Save file" or sent to another app is
  outside BuzzChat's storage and is not erased by the emergency wipe.
- **Received copies.** A bundle that arrives by Quick Share, AirDrop through a
  relay device, a messaging app or a download is already a file on the
  receiving phone, kept by another app (for example in Downloads) with that
  app's timestamps. Importing it does not remove it, and the panic wipe cannot
  reach it. BuzzChat says so after a file import and offers deletion only where
  the granted URI allows it (see [Import](#import)). A bundle received as a mesh
  file attachment sits in BuzzChat's received-files cache until the import
  screen closes, panic, or cache eviction.
- **Staged export lifetime.** The app cannot know when a share target has
  finished reading the staged file, so the file stays in `cacheDir/bundle_share/`
  until the sweep described in [Export](#export), the next export, or panic. If
  the app is not opened again, that can be a long time.
- **Flash storage.** The panic wipe unlinks files. It does not overwrite flash.
  A staged export file holds only public posts, which limits the harm.
- **Contents locate the carrier.** The items come from the exporter's relay
  store, which fills with posts from nearby mesh peers. Their author-chosen `g`
  tags (up to 5 characters, city level or coarser) taken together suggest
  where the carrier has been. The newest item's `created_at` (jittered by at
  most 900 s, [MESH_GATEWAY_V1 Privacy](MESH_GATEWAY_V1.md#privacy)) bounds the
  export time from below, and the earliest `expiresAt` minus the 30-minute
  export margin bounds it from above. Anyone who films the code or obtains the
  file learns this. Export picks items at random rather than by recency to
  widen the window (see [Export](#export)), and the Give posts screen says so.
- **Matchable copies.** Export is deterministic: the same set of envelopes
  always gives the same bytes, and the QR `transferId` is a prefix of the
  digest. One copy, from a seized phone, a filmed screen or a messaging
  service, lets an observer match every byte-identical copy by file hash, in
  hash-scanning services, on other seized devices, or against a `transferId`
  filmed at a known place and time. That links the person who displayed the
  code, people who later forwarded the file, and people who re-exported the
  same set. v1 keeps determinism (it lets two screens showing the same bundle
  be combined and keeps the format testable); a per-export random salt is an
  open question. The Send file and Save file warnings say that copies of the
  same file can be matched.
- **Detectability.** The default file name, the `.buzzbundle` extension, the
  MIME type and the 8-byte magic let a checkpoint search, a forensic tool or a
  content scanner find every BuzzChat bundle on a device or in transit with
  one name or byte-signature match. Under a hostile authority the presence of
  such a file can be dangerous on its own. Renaming on Save hides the name, not
  the magic. The Send file and Save file warnings say so.
- **Courier identification.** After an import, the importer's radio becomes the
  first visible source of posts nobody in that partition has heard. Unlike a
  sync re-origination, which follows an observable `REQUEST_SYNC` and response
  on the same link, an import has no antecedent. The importer's stable peer ID
  is advertised over BLE and Wi-Fi Aware and each id is re-originated at most
  once per node ([MESH_GATEWAY_V1 Residual risks](MESH_GATEWAY_V1.md#residual-risks-stated-honestly),
  first hop and carrier mapping), so an RF observer can mark the device as a
  courier or as the first hop for those posts, and its later gateway sync
  filters keep showing those ids. The confirmation screen previews the posts
  and says that this phone's radio will transmit them.
- **Fake hub APK.** The hub already serves its APK over plain HTTP with no
  digest or signing-certificate fingerprint anywhere on the page
  (`app/src/main/java/com/bitchat/android/hotspot/ApkWebServer.kt:40-94`;
  `app/src/main/java/com/bitchat/android/hotspot/HotspotActivity.kt:521`).
  Anyone who shows a fake hub QR can serve a trojaned APK to people joining
  with one scan. Bundles do not change this and v1 bundles are never served by
  the hub, but the hub must not become a distribution channel for bundles or
  updates until it shows the release-signing certificate fingerprint tied to
  `docs/reproducible-builds.md` (see
  [Future: signed app-update bundles](#future-signed-app-update-bundles)).
- **Size class.** Padding hides the exact size, not the size class. A
  1 KiB bundle holds exactly one post (the smallest envelope is 512 bytes); a
  2 MiB bundle holds many.
- **Integrity is not authenticity.** The bundle digest detects accidental
  damage. Anyone can recompute it. Authenticity comes only from each post's
  own signatures.

### Fail-closed rules

A device must not store, display, re-originate, sync, acknowledge or publish
anything from a bundle unless all of the following hold, in this order:

1. The input was read through a bounded reader that stopped at
   `MAX_BUNDLE_BYTES + 1` bytes, and the byte count is exactly a valid size
   class no larger than the cap for its transport.
2. The container decodes completely per [Container format](#container-format):
   magic, known version, known bundle type, zero flags and reserved fields,
   item count in range, consistent lengths, no trailing bytes, all-zero
   padding.
3. The bundle digest matches.
4. Every item record parses, its type is known, its first byte is envelope
   version `0x01`, its bytes 8 to 14 are `{"id":"`, and items are in strictly
   ascending event-id order with no duplicates (see
   [Item record](#item-record)).
5. Each item independently passes every step of the
   [MESH_GATEWAY_V1 fail-closed rules](MESH_GATEWAY_V1.md#fail-closed-rules),
   steps 1 to 6, including the validity window. Because step 4 already fixes
   the envelope version to `0x01`, the unknown-envelope-version exception never
   applies to bundles. The outer-packet and fragment clause of gateway step 1
   (`IS_COMPRESSED` clear) has no counterpart in a bundle, which has no outer
   packet; its role is taken by [No compression](#no-compression).
6. The user tapped the confirmation for this import.

A failure in steps 1 to 4 rejects the whole bundle and imports nothing. A
failure in step 5 rejects only that item. No item is ever partly accepted, and
no item is accepted from a bundle that failed steps 1 to 4. There is no
"repair" mode.

Because steps 2 and 4 read the item length and the first 79 bytes of each
envelope, some envelope faults reject the whole bundle rather than one item:
an envelope length that is not a multiple of 256 or outside `256..20,736`, an
envelope version other than `0x01`, anything other than `{"id":"` at bytes 8
to 14 (for example whitespace or reordered keys), and an event id that is not
lowercase hex or is out of order. Every other envelope fault rejects only that
item.

## Design principle: carrier only

- **No new trust.** A post is valid in a bundle if and only if the same
  envelope would be valid on the mesh. The bundle adds no signature, flag or
  field that changes the outcome.
- **No new consent.** Only envelopes whose authors already consented to
  internet reach (a `GATEWAY_POST` envelope signed by the event key) may be
  carried. Carrying is the importer's choice; publishing remains the author's.
- **Verbatim.** Items are the exact envelope bytes that travel on the mesh
  as the `0x2A` payload. They are never re-encoded, re-padded or re-signed.
- **No new mesh types.** A bundle never travels as a mesh packet type of its
  own. Imported posts enter the mesh as ordinary `0x2A` re-originations.

## Bundle contents

### Allowed in v1

| Item type | Value | Content | Notes |
| --- | --- | --- | --- |
| `GATEWAY_POST_ENVELOPE` | `0x01` | One complete `GatewayPostEnvelope`, envelope version `0x01`, exactly as defined in [MESH_GATEWAY_V1](MESH_GATEWAY_V1.md#gatewaypostenvelope-payload-of-0x2a) | Keeps the envelope signature, so consent flags (`REQUIRE_TOR`, `WANT_ACK`) and `expiresAt` stay verifiable and gateways may publish the post. Item type `0x01` means envelope version `0x01` only; a future envelope version needs a new item type |

### Reserved

| Value | Reserved for | v1 behaviour |
| --- | --- | --- |
| `0x02` | `GatewayInbound` (`0x2C`) envelopes, "claimed from the internet" posts | Rejected (whole bundle, step 4). Needs its own feed rules before it is allowed |
| `0x00`, `0x03`-`0xFF` | Not assigned | Rejected (whole bundle, step 4) |

### Never allowed, in any version

- Noise-encrypted packets, Noise payloads, or any private message, in clear or
  encrypted. Private messages are not public and were never consented for
  carriage.
- Bare Nostr events without an envelope. They lack the consent signature, and
  a gateway must not publish them.
- Public mesh chat, files, voice notes, announcements, geohash or location
  traffic. Their outer signatures tie them to a device identity, and their
  authors consented to local broadcast only.
- Keys, identities, favorites, nicknames, peer IDs, settings, logs, or any
  device information.
- Anything that is not already public and consented. If in doubt, it is not
  allowed.

## Type and value assignments (PROPOSED)

| Item | Value |
| --- | --- |
| File magic | `89 42 5A 42 0D 0A 1A 0A` (`\x89BZB\r\n\x1A\n`) |
| Container version | `0x01` |
| Bundle type `POST_BUNDLE` | `0x01` |
| Bundle type `APP_UPDATE` | `0x02`, reserved; rejected by v1 importers |
| Item type `GATEWAY_POST_ENVELOPE` | `0x01` |
| Digest domain tag | `bitchat/offline-bundle/v1` |
| QR frame version | `0x01` |
| MIME type | `application/vnd.buzzchat.post-bundle` (unregistered; see [Open questions](#open-questions)) |
| File extension | `.buzzbundle` |
| Exported file name | `posts.buzzbundle` by default; the user may rename it on Save |

The magic follows the PNG pattern. The non-ASCII first byte stops the file
being taken for text, and the CR LF, EOF and LF bytes detect line-ending
conversion and text-mode truncation by transfer tools.

No mesh `MessageType`, `REQUEST_SYNC` TLV or announcement capability bit is
added. The enum today holds `0x01`-`0x03`, `0x10`, `0x11`, `0x20`-`0x22` and
`0x29` (`app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:13-29`),
and this specification leaves it unchanged.

## Container format

```text
+----------------+------------------------+----------------+-----------+
| header         | item records           | bundleDigest   | padding   |
| 24 bytes       | bodyLength bytes       | 32 bytes       | zero bytes|
+----------------+------------------------+----------------+-----------+
|<------------------------- totalLength (a size class) --------------->|
```

### Header (24 bytes)

| Offset | Size | Field | Rule |
| --- | --- | --- | --- |
| 0 | 8 | `magic` | `89 42 5A 42 0D 0A 1A 0A` |
| 8 | 1 | `version` | `0x01`; any other value rejects the bundle |
| 9 | 1 | `bundleType` | `0x01` (`POST_BUNDLE`); any other value rejects the bundle |
| 10 | 1 | `flags` | `0x00`; every bit is reserved, and a non-zero bit rejects the bundle |
| 11 | 1 | `reserved` | `0x00`; any other value rejects the bundle |
| 12 | 2 | `itemCount` | UInt16, `1..200` |
| 14 | 2 | `reserved` | `0x0000`; any other value rejects the bundle |
| 16 | 4 | `bodyLength` | UInt32, the exact total length of all item records |
| 20 | 4 | `totalLength` | UInt32, the exact file length; must be a size class |

There is no creation time, creator, device, application version, language or
count of skipped items. A bundle made from the same set of envelopes is the
same byte string on every device. This keeps the format testable and lets two
screens showing the same bundle be combined, but it also makes every copy
matchable by its hash (see [Residual risks](#residual-risks-stated-honestly)).

### Item record

| Offset | Size | Field | Rule |
| --- | --- | --- | --- |
| 0 | 1 | `itemType` | `0x01` in v1; any other value rejects the bundle |
| 1 | 1 | `itemFlags` | `0x00`; non-zero rejects the bundle |
| 2 | 2 | `itemLength` | UInt16; a multiple of 256 in `256..20,736` (the envelope size rules of [MESH_GATEWAY_V1](MESH_GATEWAY_V1.md#size-limits-and-fragmentation)) |
| 4 | `itemLength` | `item` | The envelope bytes, verbatim |

Item records are packed back to back. The sum of `4 + itemLength` over all
records must equal `bodyLength`, and the number of records must equal
`itemCount`.

**Ordering key.** A canonical version-`0x01` envelope starts with an 8-byte
header followed by `{"id":"`, so the 64 lowercase hex characters of the event
id sit at bytes 15 to 78 of the item. The container parser requires item byte
0 to be `0x01` (envelope version) and bytes 8 to 14 to be `{"id":"`, then
reads bytes 15 to 78 as the ordering key. It requires them to be `[0-9a-f]`,
and requires the keys of
consecutive items to be strictly ascending in byte order. Byte order of
lowercase hex equals the numeric order of the raw 32-byte ids. This makes
duplicates impossible, gives one encoding per set of items, and hides the
order in which the exporter collected the posts. The key is a structural check
only. The full item check in step 5 still recomputes and compares the id.

### Bundle digest

```text
bundleDigest = TaggedHash("bitchat/offline-bundle/v1", header || itemRecords)
TaggedHash(tag, m) = SHA256(SHA256(tag) || SHA256(tag) || m)
```

The digest covers everything before it. It detects damage in transit and
names the transfer in [QR frames](#animated-qr-framing). It is **not** a
signature. It needs the public `taggedHash` that
[MESH_GATEWAY_V1 Author key](MESH_GATEWAY_V1.md#requirement) adds to
`GatewayCrypto`.

### Padding and size classes

- Size classes: `1 KiB × 2^n` for `n = 0..11`, that is 1,024, 2,048, 4,096,
  8,192, 16,384, 32,768, 65,536, 131,072, 262,144, 524,288, 1,048,576 and
  2,097,152 bytes.
- The exporter picks the smallest class that holds
  `24 + bodyLength + 32` bytes and fills the rest with zero bytes.
- The importer rejects a file whose length is not a size class, whose
  `totalLength` differs from the bytes read, where `24 + bodyLength + 32`
  exceeds `totalLength`, or where any padding byte is non-zero.

### Size caps

| Limit | Value | Reason |
| --- | --- | --- |
| `MAX_BUNDLE_BYTES` (file, share sheet) | 2,097,152 (largest size class) | About 480 text-only envelopes' worth, more than `itemCount` allows; fits easily in memory on minSdk 26 phones |
| `MAX_QR_BUNDLE_BYTES` (animated QR, receive limit) | 131,072 | Largest bundle a receiver accepts by code; leaves room for a later denser profile. At least 21 s of ideal transfer in the standard profile; see [Throughput](#throughput) |
| `QR_EXPORT_BYTES` (animated QR, Show code) | 16,384 | Largest bundle v1 Show code produces: about 2.7 s ideal in Standard and 10.8 s in Robust, so a transfer can finish within the ten-second rule. More posts go by Send file or Save file |
| `itemCount` | 1..200 | Equals the relay store's post cap ([MESH_GATEWAY_V1 Relay store](MESH_GATEWAY_V1.md#relay-store)) |
| `itemLength` | 256..20,736 | Envelope cap from MESH_GATEWAY_V1 |
| Schnorr verifications per import | at most `2 × itemCount` = 400 | Only items that pass every cheap check reach the two signature checks |

The cap is enforced **while reading**: the importer reads from the content URI
into a buffer and stops at `MAX_BUNDLE_BYTES + 1` bytes. A reported
`OpenableColumns.SIZE` is a hint for the progress display only and is never
trusted. Reading also has a deadline: if no byte arrives for 10 s, the stream
is closed from another thread and the import fails closed, so a provider that
blocks without sending anything cannot hold the import slot. This differs on purpose from
`FileUtils.copyFileForSending`, which copies with no size cap
(`app/src/main/java/com/bitchat/android/features/file/FileUtils.kt:65-129`).

### No compression

Bundles are never compressed, at any layer, in v1. There is no compression
flag, and an importer never inflates, unzips or unpacks anything. A file that
starts with a zip, gzip or zlib header fails the magic check.

Reasons:

- **No decompression bomb by construction.** The parser's memory use is
  bounded by the bytes read, which are bounded by the cap. The generic
  inflater bounds output by a declared size up to 10 MiB
  (`app/src/main/java/com/bitchat/android/protocol/CompressionUtil.kt:78-83`;
  `app/src/main/java/com/bitchat/android/util/AppConstants.kt:70`) and waits on
  a shared memory pool for up to 1 s
  (`app/src/main/java/com/bitchat/android/protocol/DecompressionResourcePool.kt:14-45`).
  Avoiding it removes a whole class of bugs.
- **Padding stays meaningful.** Compressed output would track content length
  and defeat size classes, just as it would defeat envelope padding
  ([MESH_GATEWAY_V1 Padding and compression](MESH_GATEWAY_V1.md#padding-and-compression)).
- **Little to gain.** Envelopes are short JSON plus signatures and padding.
  The space lost to envelope padding is at most 255 bytes per item.

### Is the bundle signed?

**No.** v1 post bundles carry no bundle signature, and the reserved `flags`
byte must be zero. A future version may define an optional signature for some
other purpose, but **no version may ever make a bundle signature a condition
for the validity of any item.** The reasons:

1. **Validity must not depend on the carrier.** Anyone may split, merge or
   re-export bundles. A post must stay valid through any number of carriers,
   exactly as it does through any number of mesh relays.
2. **A carrier signature identifies the carrier.** A stable key would link
   every bundle a person ever made, and link that person to the posts. That is
   the opposite of what carriers need under a hostile authority.
3. **It would prove nothing useful.** A signature by an unknown or fresh key
   says nothing about the posts. A signature by a known key would move trust
   from the authors' signatures to the carrier's reputation, which the
   gateway design deliberately avoids
   ([MESH_GATEWAY_V1 Design principle](MESH_GATEWAY_V1.md#design-principle-pre-signed-consented-verbatim)).
4. **Fail-closed behaviour stays simple.** Each item has exactly one way to be
   valid.

Signed app-update bundles are the opposite case. There, the publisher's
signature **is** the authority, and it is mandatory. That is why they are a
separate bundle type (see
[Future: signed app-update bundles](#future-signed-app-update-bundles)).

## Animated-QR framing

### Frame format

Each QR code carries one frame. The frame bytes are encoded as text with
Base45 (RFC 9285) and placed in the QR code as a single alphanumeric-mode
segment.

| Offset | Size | Field | Rule |
| --- | --- | --- | --- |
| 0 | 1 | `frameVersion` | `0x01`; others are ignored |
| 1 | 1 | `frameFlags` | `0x00`; non-zero frames are ignored |
| 2 | 8 | `transferId` | First 8 bytes of `bundleDigest` |
| 10 | 4 | `F` | UInt32, bundle length in bytes; a size class ≤ `MAX_QR_BUNDLE_BYTES` |
| 14 | 2 | `T` | UInt16, symbol size in bytes; exactly 622 or 254 (the two [QR profiles](#qr-profiles)); any other value is ignored |
| 16 | 4 | `ESI` | UInt32, encoding symbol id; top byte must be `0x00` (24-bit ESI) |
| 20 | `T` | `symbol` | One fountain-code encoding symbol |
| 20 + `T` | 4 | `crc32` | CRC-32 (ISO-HDLC, as `java.util.zip.CRC32`) over bytes `0 .. 19 + T`, big-endian |

Fixed overhead is 24 bytes per frame. A frame whose length is not `24 + T`,
whose CRC does not match, or whose Base45 text is invalid is ignored. It does
not abort the transfer.

**Why Base45 text and not binary.** The existing analyzer reads only
`rawValue`, a String
(`app/src/main/java/com/bitchat/android/ui/VerificationSheet.kt:515-537`), and
browser QR decoders return text with inconsistent handling of binary bytes.
What ML Kit's `rawBytes` contains for byte-mode segments is not confirmed (see
[Open questions](#open-questions)). Base45 uses only the 45 QR alphanumeric
characters, and alphanumeric mode packs them at 5.5 bits each, so 2 bytes cost
16.5 bits: about 97% of byte-mode efficiency, with no binary handling at all.
Decoders reject a string whose length mod 3 is 1, any character outside the
alphabet, any 3-character group whose value exceeds 65,535, and a final
2-character group whose value exceeds 255.

**Why `transferId` is the digest prefix.** It is not random, so it carries no
session or device information, and two screens showing the same bundle can be
combined. The cost is that the same bundle always has the same `transferId`,
which an observer who films it can match against other copies (see
[Residual risks](#residual-risks-stated-honestly)). Lock-on and re-lock rules
are in [Receiver behaviour](#receiver-behaviour-and-partial-receive).

### Fountain code

v1 uses **RaptorQ (RFC 6330)** with one source block and one sub-block
(`Z = 1`, `N = 1`, alignment `Al = 1`), symbol size `T` from the frame, and
transfer length `F`. `K = ceil(F / T)` source symbols. The last source symbol
is zero-padded to `T` bytes.

- RaptorQ is systematic. Frames with `ESI < K` carry the bundle bytes
  directly, so a receiver that sees every frame of the first pass decodes with
  no overhead. Frames with `ESI ≥ K` are repair symbols. With `K + 2` distinct
  symbols, decoding fails with probability on the order of 10^-6 (RFC 6330).
- It is fully specified by an RFC, so a native and a browser implementation
  can interoperate without this document inventing a degree distribution or
  random-number generator. An LT code would need both defined here bit for bit.
- The sender shows ESIs `0, 1, 2, ...` in order: the systematic pass, then
  repair symbols without end. It never repeats an ESI during one display
  session, so a receiver that joins late or misses frames is never waiting for
  one specific frame.
- Because receivers accept only `T` = 622 or 254 and `F` ≤ 131,072, `K` is at
  most 517 (Robust profile, 128 KiB), far below the RFC limit of 56,403. A v1
  Show code bundle is at most 16 KiB, so `K` is at most 27 (Standard) or 65
  (Robust).

No RaptorQ implementation exists in this repository today. Adding one is a
dependency or new-code decision; see [Open questions](#open-questions).

### QR profiles

All frames of one display session use one profile, so module density never
changes between frames.

| Profile | QR version | Modules (with 4-module quiet zone) | ECC | Alphanumeric capacity | Frame bytes | `T` | Frame rate |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard | 20 | 97 (105) | M | 970 chars | 646 (969 chars) | 622 | 10 fps |
| Robust | 12 | 65 (73) | M | 419 chars | 278 (417 chars) | 254 | 6 fps |

- **ECC level M** (about 15% recovery) handles glare, moiré and screen
  refresh banding better than L. Lost frames cost little because of the
  fountain code, so higher per-frame ECC (Q, H) would only cut throughput.
- **Encoder.** One shared QR encoder replaces the two copies in
  `app/src/main/java/com/bitchat/android/hotspot/QrCodeGenerator.kt` and
  `app/src/main/java/com/bitchat/android/ui/VerificationSheet.kt:492-513`,
  which call `QRCodeWriter().encode` with default hints. The bundle encoder sets
  the error-correction level, a 4-module margin and a fixed version
  (zxing `EncodeHintType` values; support for a fixed-version hint in zxing
  3.5.4 must be confirmed). It must not log the payload: the existing
  generator logs the SSID and URL it encodes
  (`app/src/main/java/com/bitchat/android/hotspot/QrCodeGenerator.kt:42, 60`).
- **Rendering.** The existing generator encodes inside `remember` during
  composition and fills a screen-sized bitmap one pixel at a time
  (`app/src/main/java/com/bitchat/android/ui/VerificationSheet.kt:480-513`).
  That is too slow for 10 frames per second on the main thread, and repair
  symbols never end, so frames cannot all be rendered ahead of time. Frames are
  therefore encoded off the main thread into a bitmap of one pixel per module,
  kept in a small look-ahead buffer (a few frames), and drawn with
  nearest-neighbour scaling at an integer number of screen pixels per module.
- **Display.** Hard cuts between frames on vsync, no transition animation,
  black on white, at least 4 screen pixels per module, the screen held on and
  at full brightness while the code plays. The default is Standard. One button,
  "Having trouble? Use bigger squares", switches to Robust and restarts at
  ESI 0 with the same bundle (same `transferId` and `F`, new `T`); receivers
  re-lock on the new `T` automatically.
- **Automatic stop.** The display stops after 2 minutes, with a one-tap "Keep
  showing" that restarts the timer. It also stops at once when the app goes to
  the background or the screen turns off. On stop, the previous brightness and
  screen-timeout behaviour are restored. This saves battery and limits how
  long the phone visibly advertises that it carries posts.

### Throughput

Ideal figures (every frame read, no repair overhead):

| Bundle size | Standard (6,220 B/s) | Robust (1,524 B/s) |
| --- | --- | --- |
| 1 KiB (1 post) | 0.2 s (K = 2) | 0.8 s (K = 5) |
| 16 KiB (about 30 short posts) | 2.7 s (K = 27) | 10.8 s (K = 65) |
| 128 KiB (cap) | 21.1 s (K = 211) | 86.2 s (K = 517) |

v1 Show code never exceeds 16 KiB (`QR_EXPORT_BYTES`), the second row, so an
ideal transfer takes under 3 s in Standard and about 11 s in Robust. The
128 KiB row is the receive limit, kept for a later denser profile; v1 senders
do not produce it. Ideal figures are not the acceptance criterion: tests 6.3
and 6.10 set pass thresholds on measured end-to-end time.

Real transfers lose frames, and the manual camera tests measure how many. Other
projects report far higher rates: about 129 KB/s for a browser-based optical
transfer with a roughly 2 MB limit (Decimen), and about 106 KB/s for libcimbar
colour codes with an Android decoder. These figures are from those projects
and have not been reproduced here. v1 stays with black-and-white QR at modest
density because the phone app already ships a QR encoder and scanner, and
because it has to work on cheap cameras in poor light. A denser or colour
profile is an open question.

### Receiver behaviour and partial receive

- **Lock-on.** The receiver locks on to the first valid frame's `transferId`,
  `F` and `T`. When an image holds several codes, the scanner passes all of
  them on and the receiver prefers frames from the largest code in view.
- **Re-lock on a new profile.** A valid frame with the locked `transferId` and
  `F` but the other `T` means the sender switched profile. The receiver drops
  its symbols and re-locks on the new `T` at once, with no user action; the
  progress ring restarts from zero.
- **Re-lock on a stalled transfer.** If no new distinct symbol for the locked
  transfer arrives for 3 s while valid frames with another `transferId` keep
  arriving, the receiver drops its state and locks on to that other transfer.
  A stray or hostile screen seen first therefore cannot leave the receiver
  stuck. A "Start over" button on the Get posts screen does the same on
  demand.
- Keep a set of distinct ESIs and their symbols, in memory only, at most
  `K + 20` of them. The first copy of each ESI wins. A later copy of the same ESI with different bytes but
  a valid CRC is counted as a conflict. It is evidence of a second, hostile
  screen.
- Do not use the existing scanner's "ignore a value equal to the last one"
  rule (`app/src/main/java/com/bitchat/android/ui/VerificationSheet.kt:408-430`).
  Every decoded string goes to the frame decoder, and duplicates are handled by
  the ESI set.
- Progress = distinct symbols / `K`, shown as a ring.
- Try to decode once `K` distinct symbols are held, and again after each new
  symbol up to `K + 20`. A successful decode yields `F` bytes, which then go
  through the full container and item checks exactly like a file. If decoding
  still fails with `K + 20` symbols held, no further symbols are stored and the
  outcome is the same as a digest failure below.
- If the decoded bytes fail the digest or container check, discard all state
  and show "That did not work. Try again." If conflicts were counted, the
  message says another screen may have interfered. Never retry by guessing
  which symbols were bad.
- **No partial import.** Even if the symbols already held contain whole items,
  nothing is imported until the whole bundle has decoded and passed every
  check.
- If no new valid frame arrives for 15 s, show "Move closer and hold steady".
  State is discarded when the user leaves the screen, after 10 minutes, or on
  panic. These timers run on an injected clock (see
  [Implementation seams](#implementation-seams)).

### Camera and secure-context requirement

The receiver needs a camera, and camera access has platform rules:

- **Native Android app.** Uses the existing `CAMERA` permission
  (`app/src/main/AndroidManifest.xml:51-52`), the camera feature declared
  not required (`app/src/main/AndroidManifest.xml:71`), and the Compose
  permission flow already used for verification
  (`app/src/main/java/com/bitchat/android/ui/VerificationSheet.kt:323`). The
  scanner reuses CameraX 1.6.1 and ML Kit barcode scanning 17.3.0, which are
  already dependencies (`app/build.gradle.kts:148-155`;
  `gradle/libs.versions.toml:62, 68-69`). `ScannerView` and `QRCodeAnalyzer`
  are private to `VerificationSheet`
  (`app/src/main/java/com/bitchat/android/ui/VerificationSheet.kt:408-477, 515-537`)
  and must be extracted into a shared scanner with a callback for every frame.
  The existing configuration is not enough for dense animated codes: the
  analysis use case sets only `STRATEGY_KEEP_ONLY_LATEST` and no resolution
  (`app/src/main/java/com/bitchat/android/ui/VerificationSheet.kt:442-445`),
  each image is closed only after ML Kit finishes, so the receive rate is
  1 / decode latency, and only `barcodes.firstOrNull()` is used
  (`app/src/main/java/com/bitchat/android/ui/VerificationSheet.kt:531-536`).
  The shared scanner must set an explicit analysis resolution (for example a
  1280×720 target through a `ResolutionSelector`), emit every barcode in each
  image together with its bounding box, and record frames decoded per second
  in the manual tests. If test 6.4 shows that the API-26 reference phone cannot
  decode the Standard profile reliably, Robust becomes the default.
- **Browser.** `getUserMedia` works only in a secure context: an HTTPS page
  (including an installed web app first loaded over HTTPS) or a local origin.
  Whether a `file://` page may use the camera varies by browser. The hub's
  landing page is plain HTTP on a LAN address
  (`app/src/main/java/com/bitchat/android/hotspot/HotspotActivity.kt:521`), so
  **a page served by a hub cannot open the camera**. Displaying is limited too:
  the Screen Wake Lock API is also secure-context-only and no web API sets
  screen brightness, so a plain-HTTP page cannot meet the display rule above,
  and platform auto-lock can cut a transfer short. v1 therefore makes **no
  browser claim**: browser-only phones can neither give nor get bundles, and
  the hub does not serve bundle files (its HTTP server logs the URI and remote
  address of every request,
  `app/src/main/java/com/bitchat/android/hotspot/ApkWebServer.kt:43`). There
  is no browser client in this repository today; a future one needs its own
  review (see [Open questions](#open-questions)).
- **No camera.** The camera is optional on the phone, so import must always
  also work by opening a file.

## Behaviour

### Export

1. The user opens "Carry posts offline" and picks Show code, Send file or Save
   file.
2. The exporter takes envelopes from the in-memory relay store
   ([MESH_GATEWAY_V1 Relay store](MESH_GATEWAY_V1.md#relay-store)). Every one
   is re-checked through the fail-closed sequence at export time, so a stale or
   expired post is never exported.
3. **Exclusions.** Posts with less than 30 minutes left before `expiresAt` are
   left out. The device's own outbox posts that it has not yet received back
   from another node are left out by default, matching the sync rule in
   [MESH_GATEWAY_V1 Gossip sync](MESH_GATEWAY_V1.md#gossip-sync). An advanced
   switch, "Include my own posts", adds them after a warning that the
   receiver may guess they are yours.
4. If more posts qualify than fit (200 items, or the size limit for the chosen
   transport: `QR_EXPORT_BYTES` for Show code, `MAX_BUNDLE_BYTES` for files),
   items are chosen uniformly at random with a CSPRNG from the eligible set,
   adding each in random order if it still fits. Random rather than
   most-recent choice widens the export-time window that the contents reveal
   (see [Residual risks](#residual-risks-stated-honestly)).
5. Items are sorted by event id, framed, digested and padded to a size class.
   No device field enters the file.
6. **Show code** plays the frames from memory; no file is written. **Send
   file** writes `posts.buzzbundle` into a dedicated cache subdirectory,
   `bundle_share/`, replacing any earlier file, and opens the share sheet.
   **Save file** uses `ActivityResultContracts.CreateDocument` with
   `posts.buzzbundle` as the suggested name, which the user may change.
7. **Staged file lifetime.** The share pattern reused below is
   fire-and-forget (`context.startActivity(chooser)`, no result,
   `app/src/main/java/com/bitchat/android/ui/AboutSheet.kt:644-654`), and share
   targets such as Quick Share or messaging apps may read the granted URI after
   the chooser closes. The app therefore never deletes the staged file on
   chooser return. It is deleted: when the next export replaces it; when the
   app returns to the foreground and the file is older than 10 minutes; in a
   sweep at app start that deletes any file in `bundle_share/` older than
   10 minutes; and on panic. Until then it stays on disk (see
   [Residual risks](#residual-risks-stated-honestly)).

### Share-sheet integration on Android

- **Outbound.** Reuse the existing APK pattern: `FileProvider.getUriForFile`
  with the `${applicationId}.fileprovider` authority
  (`app/src/main/java/com/bitchat/android/ui/ApkDownloadViewModel.kt:261-265`),
  then `ACTION_SEND` with `EXTRA_STREAM`, `ClipData.newRawUri` and
  `FLAG_GRANT_READ_URI_PERMISSION` through `Intent.createChooser`
  (`app/src/main/java/com/bitchat/android/ui/AboutSheet.kt:644-654`), with the
  bundle MIME type. Add a
  `<cache-path name="bundle_share" path="bundle_share/" />` entry next to the
  existing `apk_share/` entry in `app/src/main/res/xml/file_paths.xml`.
  The provider is already declared with `exported=false` and
  `grantUriPermissions=true`
  (`app/src/main/AndroidManifest.xml:98-107`). Its paths also expose the whole
  cache and files directories; narrowing them is an open question because
  other features use them.
- **Inbound.** Today the app has no `ACTION_SEND` or file `ACTION_VIEW`
  filter. `MainActivity` has only the launcher filter and the
  `bitchat://verify` link (`app/src/main/AndroidManifest.xml:120-129`;
  `app/src/main/java/com/bitchat/android/MainActivity.kt:851-860`). v1 adds a
  dedicated exported `BundleImportActivity` with `ACTION_SEND` and
  `ACTION_VIEW` filters for `application/vnd.buzzchat.post-bundle` only. It
  does not register for `application/octet-stream` or `*/*`, so BuzzChat does
  not appear for every shared file. Files that arrive with a generic MIME type
  are imported from inside the app with `ActivityResultContracts.OpenDocument`,
  as the file picker already does
  (`app/src/main/java/com/bitchat/android/ui/media/FilePickerButton.kt:30-35`).
- **Inbound URI rules.** `BundleImportActivity` accepts only `content://` URIs.
  It rejects `file://` and every other scheme, and rejects any URI whose
  authority is BuzzChat's own `${applicationId}.fileprovider`. That provider
  maps the whole cache and files directories (`app/src/main/res/xml/file_paths.xml`),
  so without this rule another app could make BuzzChat read its own private
  files with its own permissions. Every reject of an intent-supplied URI, for
  any reason (scheme, authority, size, magic, parse), shows the same message,
  "This file could not be opened.", so the calling app cannot learn whether a
  file exists or how big it is. The distinct error messages in
  [UI requirements](#ui-requirements) are used only for files the user picked
  inside the app.
- **Confirmation tap protection.** The confirmation tap is the only consent
  gate between another app's intent and storing plus re-originating posts, and
  `minSdk` 26 includes Android versions that do not block touches through
  untrusted overlays. The confirmation screen sets `filterTouchesWhenObscured`
  on its buttons (or checks the obscured flags on each `MotionEvent`), and on
  API 31+ also calls `setHideOverlayWindows(true)`.
- **Content sniffing.** The importer decides by the magic bytes alone, never
  by MIME type or file name. `FileUtils.getMimeTypeFromExtension` maps unknown
  extensions to `application/octet-stream`
  (`app/src/main/java/com/bitchat/android/features/file/FileUtils.kt:134-165`);
  add a mapping for `.buzzbundle`.
- **Reading.** Read the stream directly into a bounded in-memory buffer.
  Do not copy it to disk first, and do not log the URI or the original name.
  The existing copy helper logs both on error
  (`app/src/main/java/com/bitchat/android/features/file/FileUtils.kt:123-126`).
- **`FileShareDispatcher` is not involved.** It is a UI callback for sending a
  file over the mesh
  (`app/src/main/java/com/bitchat/android/ui/events/FileShareDispatcher.kt:1-17`),
  not an import or export path.
- **Bundles over the mesh.** Mesh files (`0x22`) carry the device's stable
  signature, so a bundle sent as a mesh file attachment would be attributable
  to the carrier's device identity. The BuzzChat file-send path therefore
  refuses any file whose content starts with the bundle magic, with one line:
  "Posts files can't be sent this way. Use Carry posts offline." The posts
  already travel natively over the mesh, under per-post flood control. A
  bundle that arrives from an older or other client as a mesh file is received
  like any file, into the evictable cache
  (`app/src/main/java/com/bitchat/android/features/file/FileUtils.kt:196-206`),
  and is imported only when the user taps it, through the same importer. That
  received copy is on disk; the importer deletes it when the import screen
  closes, whatever the outcome.

### Import

1. Input arrives from the scanner, a share or view intent, or the in-app file
   picker. One import runs at a time and there is no pending queue: while an
   import is active, a new inbound request is dropped without opening its
   stream, and a one-line notice says "Finish the current import first."
2. Checks run on a background dispatcher in the fail-closed order. The UI shows
   "Checking posts…".
3. Item outcomes are counted in memory: new and valid, already held,
   expired, invalid. There is no "unsupported" item outcome in v1: an unknown
   item type or envelope version rejects the whole bundle at step 4 of the
   [Fail-closed rules](#fail-closed-rules). **Already held** means the event id is in
   the seen set or in the relay store. This includes posts this device saw and
   later evicted (their ids stay in the seen set,
   [MESH_GATEWAY_V1 Relay store](MESH_GATEWAY_V1.md#relay-store)). Already-held
   items are counted and skipped: never stored again, never re-originated, so
   the re-originate-once rule
   ([MESH_GATEWAY_V1 Forwarding model](MESH_GATEWAY_V1.md#forwarding-model),
   step 5) holds.
4. The confirmation screen shows the count of new posts, a scrollable preview
   of them, and one line for skipped ones (see
   [UI requirements](#ui-requirements)). Nothing has been stored yet.
5. On the tap, valid new items enter the gateway pipeline as if they had come
   from one ingress link named `IMPORT` (see
   [Flood control](#flood-control)):
   - **Admission.** The importer shuffles the valid new items with a CSPRNG,
     never using bundle order, so grinding event ids gains nothing. It then
     admits each item in that order only if, after admission, `IMPORT` stays
     within its slot quota and its byte quota, the per-pubkey cap holds, and
     the store stays within both store caps **without evicting any
     non-expired post**. Expired posts may be evicted to make room; no other
     post, from a radio link or from an earlier import, is ever evicted to
     admit an `IMPORT` item. An item that does not fit is not stored.
   - Only stored items are added to the event-id seen set
     ([MESH_GATEWAY_V1 Dedup key](MESH_GATEWAY_V1.md#dedup-key)), after
     verification. An item that was valid but not stored is not marked seen, so
     a hostile bundle cannot stop the device from accepting the same post from
     the mesh or through sync later. This is a deviation from MESH_GATEWAY_V1
     forwarding step 4, recorded in
     [Amendments to MESH_GATEWAY_V1](#amendments-to-mesh_gateway_v1).
   - Each stored item is re-originated once: origin TTL drawn uniformly from
     `1..3`, through the unsigned origination API, scheduled by the per-link
     fair queue under the `IMPORT` link
     ([MESH_GATEWAY_V1 Forwarding model](MESH_GATEWAY_V1.md#forwarding-model),
     step 6). The fair queue spreads them over time (one share of the per-node
     re-origination budget). A low TTL keeps the extra load on legacy relays
     small. It does not hide that this radio is the first source of these
     posts (see [Residual risks](#residual-risks-stated-honestly), courier
     identification).
   - The power rules of
     [MESH_GATEWAY_V1 Power](MESH_GATEWAY_V1.md#power) apply unchanged: in
     the LOW band the re-origination budget is halved, and in the CRITICAL
     band (not charging) or `ULTRA_LOW_POWER` stored items are not
     re-originated and are served only through sync responses. Import adds no
     power exemption.
   - Stored items enter the gateway-post sync filter.
   - If the device is a gateway, stored items go into the publish queue under
     the normal gateway rules, including Tor readiness and the publish budget.
   - **Honest count.** The result screen reports the number of items actually
     stored, not the number previewed. Valid items that did not fit (quota,
     store or per-pubkey cap) are counted separately and reported in one line
     (see [UI requirements](#ui-requirements)).
6. Cancel at any point drops everything. No state survives the screen.
7. **Received file notice.** After an import from a share or view intent or
   from the file picker, the result screen adds one line: "The file you
   received is still on this phone. Delete it in the app it came from." When
   the granted URI supports deletion (a document URI whose flags include
   `FLAG_SUPPORTS_DELETE`), a "Delete file" button calls
   `DocumentsContract.deleteDocument`. BuzzChat never asks for broader
   storage access to do this.

**Expiry.** A carried bundle may be hours old. The validity window from
[MESH_GATEWAY_V1 Expiry and clock skew](MESH_GATEWAY_V1.md#expiry-and-clock-skew)
applies unchanged. Expired items are dropped and counted, never stored.

## Flood control

All values are receiver policy, not wire fields.

- **One synthetic ingress link.** Every import, from any source, is charged to
  one shared link ID, `IMPORT`. Repeated imports therefore cannot multiply
  their share. Imports never use the shared null-link bucket, so they cannot
  starve mesh links that lack a link ID.
- **Per-link delivery ceiling: not applied.** The MESH_GATEWAY_V1 ceiling of
  30 `0x2A` deliveries per minute per ingress link would discard everything
  past the first 30 items of an import. It does not apply to `IMPORT`. Its role
  is taken by the confirmation tap, the one-import-at-a-time rule with no
  pending queue, the 200-item cap per bundle, and the `IMPORT` store quotas.
- **Relay store quota.** `IMPORT` may occupy at most **100 of the 200 store
  slots** and at most **512 KiB (524,288 bytes) of the 2 MiB store**, whichever
  is reached first. The relay store counts bytes as MESH_GATEWAY_V1 does
  (envelope bytes). The slot quota is higher than the 50-slot per-link quota
  for radio links, because a physically carried bundle is user-chosen and
  often the only path across a partition. The byte quota is needed because the
  store also has a byte cap: without it, 100 maximum-size envelopes
  (100 × 20,736 = 2,073,600 bytes) would fill about 99% of the store.
- **No eviction for imports.** An `IMPORT` admission never evicts a
  non-expired post, whether from a radio link or from an earlier import. At
  quota, or when the store is full of live posts, further items are simply not
  stored, and they are not marked seen. So `IMPORT` always leaves at least
  three quarters of the store's bytes, and every slot it did not take, to the
  mesh; a mesh post is never pushed out by an import and so never left
  suppressed in the seen set or the sync filter; a post waiting in the
  re-origination queue is never lost to an import; and a later import cannot
  push out posts the user imported earlier on purpose. Mesh arrivals evict as
  MESH_GATEWAY_V1 defines, which may evict `IMPORT` posts like any other.
- **Re-origination share.** `IMPORT` posts go through the per-node
  deficit-round-robin re-origination queue as one link. A 200-post import
  therefore gets one fair share of the 20 re-originations per minute and
  cannot crowd out posts from radio neighbours.
- **Per pubkey.** The 10-posts-per-pubkey store cap applies.
- **Repeated imports.** With the store otherwise unchanged, re-importing the
  same bundle admits nothing: every stored item is already held, and every
  item that was left out is still blocked by the same quota, store or
  per-pubkey limit. Items left out earlier can be admitted by a later import
  once room exists (for example after `IMPORT` posts expire or are evicted by
  mesh arrivals). Each such admission is a new store entry and is re-originated
  once; no id is ever re-originated twice. Imports require a tap each time.
  Hostile intents from other apps cannot import without one.
- **Gateway publish budget.** Unchanged: imported posts compete with mesh posts
  inside the existing per-minute and per-hour limits.

### Amendments to MESH_GATEWAY_V1

The `IMPORT` link deviates from
[MESH_GATEWAY_V1](MESH_GATEWAY_V1.md) in these ways only. All other rules
apply to imported items unchanged. MESH_GATEWAY_V1 must record these
deviations in its Relay store, Forwarding model step 4 and Flood control
sections before either specification leaves draft.

| MESH_GATEWAY_V1 rule | `IMPORT` link |
| --- | --- |
| Per-link store quota: 50 of 200 slots; at quota the link's oldest post is evicted | 100 of 200 slots and at most 512 KiB of the 2 MiB; at quota further items are not stored and nothing is evicted, not even an older `IMPORT` post |
| "Eviction never fails a receive. A full store simply evicts" | An `IMPORT` admission never evicts a non-expired post; an item that does not fit is not stored |
| Forwarding step 4: a valid new post is recorded in the seen set and stored "whatever the budget state" | Only stored items are recorded in the seen set; valid items left out are not |
| Per-link ceiling: 30 `0x2A` deliveries per minute | Not applied; replaced by the tap, one import at a time and the store quotas |

## Metadata hygiene

- **Inside the file:** no device name, peer ID, nickname, app version, locale,
  creation time, export time, item collection order or skipped-item counts.
  The same set of envelopes always gives the same bytes. That removes
  per-device variation but makes copies matchable by hash, and the posts'
  own tags and timestamps still imply a region and an export-time window (see
  [Residual risks](#residual-risks-stated-honestly)).
- **File name:** `posts.buzzbundle` by default. No date, count, nickname or
  random suffix. The fixed name and extension make bundles easy to find by
  name; the user may rename on Save, and the importer decides by content
  alone.
- **Size:** padded to a size class.
- **File-system timestamps:** the staged file is written fresh for each share
  and deleted by the sweep in [Export](#export), step 7. A `FileProvider` URI exposes only display name and size
  to the receiving app. Apps or services that keep their own modification time
  are outside BuzzChat's control.
- **Archives:** bundles are never wrapped in zip or any other archive by
  BuzzChat, so archive entry metadata (paths, owner IDs, times) cannot appear.
  Wrapped files are rejected on import.
- **Images:** any thumbnail inside an item was checked by the author before
  signing ([MESH_GATEWAY_V1 Media](MESH_GATEWAY_V1.md#media)). The exporter
  never adds images.
- **QR frames:** the transfer id is a digest prefix, not a random session id,
  so it carries no device information but is the same for every showing of the
  same bundle. Frames carry no other identifying field.

## Privacy

- Bundles carry public posts only. The UI never suggests that a bundle is
  private or encrypted.
- **Logging.** Bundle code must not log, at any level: content, event ids or
  prefixes, pubkeys, bundle digests, transfer ids, URIs, original file names,
  per-bundle item counts, or the time of an import. The logging rules and the
  R8 and lint requirements of
  [MESH_GATEWAY_V1 What gateways must not log or keep](MESH_GATEWAY_V1.md#what-gateways-must-not-log-or-keep)
  extend to the bundle packages.
- **Storage.** Bundles that BuzzChat reads from a URI or decodes from QR are
  held in memory and never written to disk by BuzzChat. Partial QR state lives
  in memory. BuzzChat's only own bundle files are the staged export file in
  `cacheDir/bundle_share/` and, transiently, a bundle received as a mesh file
  attachment, which the existing receive path writes into the received-files
  cache (`app/src/main/java/com/bitchat/android/features/file/FileUtils.kt:196-206`)
  and the importer deletes when the import screen closes. Received files in
  general are kept in plain text in the cache today
  (`docs/security-review-jul-27.md` M7, line 99). A bundle file delivered by
  another app stays wherever that app put it.
- **Panic.** `ChatViewModel.performPanicClearAllData`
  (`app/src/main/java/com/bitchat/android/ui/ChatViewModel.kt:1454-1534`) must,
  in this order:
  1. cancel any running import, export, QR display or scan, and stop the
     re-origination of `IMPORT` posts with the rest of the gateway pipeline;
  2. drop in-memory import buffers and partial QR state;
  3. delete `cacheDir/bundle_share/` and **verify** that it no longer exists.
     Today `clearAllMedia` deletes the whole cache directory but ignores the
     result (`app/src/main/java/com/bitchat/android/features/file/FileUtils.kt:283-327`),
     and the panic flow does not check it
     (`app/src/main/java/com/bitchat/android/ui/ChatViewModel.kt:1496`). A
     failed deletion keeps transports stopped, using the same pattern as the
     conversation database check
     (`app/src/main/java/com/bitchat/android/ui/ChatViewModel.kt:1521-1526`).
  The relay store, seen set and gateway queue are cleared by the gateway panic
  steps. The panic screen says that bundles already shared or saved elsewhere,
  and received bundle files kept by other apps (for example in Downloads), are
  not erased.
- **Location.** Bundles carry whatever coarse geohash tag an author chose
  ([MESH_GATEWAY_V1 Privacy](MESH_GATEWAY_V1.md#privacy)). The bundle feature
  never reads, derives or adds location. The set of tags in a bundle can still
  suggest where its carrier has been; this is a stated residual risk, not
  something the feature computes.
- **Network.** Export and import make no network requests of any kind.
- Synthetic data only in tests, vectors, screenshots and bug reports, per
  `AGENTS.md`.

## UI requirements

Every screen must pass the ten-second test in
[BuzzChat principles](BUZZCHAT_PRINCIPLES.md): someone who has never seen
BuzzChat understands it within ten seconds, one-handed, in a crowd.

- **Entry point.** One item in the Gateway posts feed menu: "Carry posts
  offline". It opens a sheet with two large buttons: **Give posts** and
  **Get posts**. Get posts is also reachable in one tap from the main screen,
  so a newcomer who has just been handed a phone with BuzzChat open does not
  have to find a feed menu.
- **Stock cameras.** A frame is bare Base45 text, so a system camera app that
  scans the animated code shows meaningless text and offers nothing to open.
  Newcomers without BuzzChat join first through the hub's join code; the
  animated code is only for phones that already run BuzzChat. A frame prefix
  that makes stock cameras open the app is an open question.
- **Give posts.** Three choices with icons: "Show code" (default, largest),
  "Send file" and "Save file". Lines under them: "These posts are public.
  Anyone who sees your screen can copy them." and "The posts can hint at where
  you have been." "Save file" adds: "Saved files are not erased by emergency
  wipe. Copies of the same file can be matched, and the file can be found by
  its type."
- **Show code.** Full-screen animated code, brightness up, a short instruction
  ("Point the other phone's camera here"), and the "Use bigger squares" button.
  No settings. After 2 minutes it stops with "Keep showing".
- **Get posts.** Opens the camera immediately (after the one-time permission
  prompt) with a progress ring and "Hold the camera on the code". A small
  "Open a file instead" link is always visible, and is the only option on a
  device without a camera. A small "Start over" button drops the current
  transfer.
- **Confirmation.** One sentence, a preview and one button: "Found 12 public
  posts. Your phone's radio will share them with people nearby." with **Add
  posts** and Cancel. On a gateway device the sentence adds: "This phone will
  also try to post them online." Below it, a scrollable list shows the first
  line of each new post, so the user sees what they are about to transmit. One
  quiet line for skipped items, for example "3 were expired or damaged and
  were skipped." Touches on the buttons are ignored while the window is
  obscured (see
  [Share-sheet integration](#share-sheet-integration-on-android)).
- **No new posts.** If no item is new and valid: "No new posts found." with
  one **OK** button and no Add button.
- **Honest limits.** After adding: "Added 12. They will be passed to people
  nearby." On a gateway device: "Added 12. They will be passed to people
  nearby, and this phone will try to post them online." The number is the
  count actually stored. If some valid posts did not fit, one more line says
  so, for example "8 did not fit and were not added. They may still arrive
  over the mesh." If none fit: "No room for more posts right now." with one
  **OK** button. Never "sent to the internet".
- **Nothing to give.** If no post is eligible for export (empty relay store,
  only own unreleased posts, or every post within 30 minutes of expiry), Give
  posts shows "No posts to give yet." with one **OK** button, and no empty
  bundle is ever produced (`itemCount` is at least 1).
  After a file import, the received-file notice from [Import](#import), step 7.
- **Hand-off warning.** On "Send file", lines: "AirDrop and Quick Share show
  your device name to people nearby." and "Copies of the same file can be
  matched, and the file can be found by its type." Shown each time; no "don't
  show again".
- **Errors** are one short sentence with one action. For files picked inside
  the app: "That did not work. Try again." / "This file is not a BuzzChat
  posts file." / "This file is too big." For any file handed over by another
  app's intent, every reject shows only "This file could not be opened."
- **Panic** stays one tap away on every bundle screen.
- Visible changes need before and after screenshots with synthetic data.

## Principles conformance

How this specification measures against
[BuzzChat principles](BUZZCHAT_PRINCIPLES.md), including where v1 falls short.

| Principle | v1 position |
| --- | --- |
| One-scan join; ten-second test | Get posts is one tap from the main screen, then point the camera and tap **Add posts**. The animated code is not a join code: a newcomer without BuzzChat joins through the hub's join code first (see [UI requirements](#ui-requirements), stock cameras) |
| Works on whatever device people hold | **Partial.** Android app: give and get. Browser-only phones: neither, because the hub page is plain HTTP and cannot open the camera ([Camera and secure-context requirement](#camera-and-secure-context-requirement)). iPhone with bitchat: no support until the format is agreed upstream. Wear OS: excluded. These gaps are stated, not hidden ([Compatibility](#compatibility)) |
| Stay bitchat-compatible | No new mesh type, TLV or capability bit; imported posts are ordinary `0x2A` re-originations. The file and frame formats are BuzzChat-local until agreed upstream |
| No single point of failure | Needs no server, hub or network; any two phones with BuzzChat can exchange a bundle |
| Fail closed | Whole-bundle rejection for any container fault, per-item gateway checks, no repair mode, no partial QR import ([Fail-closed rules](#fail-closed-rules)) |
| Consent before reach | Only envelopes the author signed for internet reach are carried; the importer's tap is consent for their own radio to transmit them; publishing stays under the author's signed flags |
| Honest limits | Result screens report the count actually stored; "post them online" is phrased as an attempt; residual risks are shown on the Give posts and Send file screens |
| Protect the person | No identity, time or device field in the file; memory-only import; panic deletes the staged file and verifies it; the bundle feature never reads or derives location |

## Future: signed app-update bundles

Bundle type `0x02` (`APP_UPDATE`) is reserved and is rejected by every v1
importer. It needs its own specification before any client accepts it. That
specification must at least:

- require a signature by a **release key pinned in the installed app**, never
  a key carried in the bundle and never trust-on-first-use;
- reject downgrades and replays of older signed builds;
- tie the signed payload to the reproducible-build output
  (`docs/reproducible-builds.md`), so that independent builders can check the
  same hash;
- never install anything without an explicit user action through the system
  installer, and never reuse the post-bundle import screen.

**Poisoned-update risk.** An update bundle installs code. If the release key
is stolen, or its holder is coerced, every device that trusts it can be made
to install surveillance software, and offline distribution makes a bad update
hard to recall. A hostile stranger can also offer an "update" signed by some
other key and rely on the user to accept it. Whether to require several
independent signatures, and how the user tells a real update from a fake one
in ten seconds, are open questions. Until they are answered, post bundles and
update bundles must stay separate types, so that no post-bundle code path can
ever lead to installing code.

**The code path that already exists.** The hub already distributes the app:
it serves `/bitchat.apk` over plain HTTP with no digest or signing-certificate
fingerprint on the landing page
(`app/src/main/java/com/bitchat/android/hotspot/ApkWebServer.kt:40-94`;
`app/src/main/java/com/bitchat/android/hotspot/HotspotActivity.kt:521`). A
fake hub QR can therefore serve a trojaned APK to anyone joining with one
scan. This is outside v1 post bundles, but it is the same poisoned-code risk.
Before the hub is treated as a distribution channel for anything beyond
today's APK, and before any update bundle is specified, the hub page and its
QR must show the release-signing certificate fingerprint tied to
`docs/reproducible-builds.md`, and the pinned-key rules above must apply.

## Versioning

- The container `version` byte governs the whole file. v1 importers reject
  every other version. Additive changes need a new version, never a new
  meaning for reserved bits or fields.
- The `bundleType` byte separates purposes with different trust rules. A new
  purpose needs a new type and its own specification.
- Item types are checked at the container level: an unknown item type rejects
  the whole bundle. Each item type fixes its envelope version (item type
  `0x01` is envelope version `0x01` only), so a new envelope version needs a
  new item type, and the container check in step 4 rejects a mismatch.
- `frameVersion` governs QR frames independently. A receiver ignores frames
  with an unknown frame version.
- Size classes, caps and QR profiles are part of the v1 contract; changing
  them needs a new version.

## Compatibility

- **Mesh.** No new mesh packet type, TLV or capability bit. Imported posts
  travel as ordinary `0x2A` re-originations, so every compatibility statement
  in [MESH_GATEWAY_V1 Compatibility summary](MESH_GATEWAY_V1.md#compatibility-summary)
  applies unchanged.
- **iOS bitchat.** No iOS bundle or animated-QR format was found in this
  repository. An iPhone that receives a bundle by AirDrop keeps it as a file
  but cannot import it. Adoption by iOS requires this format to be agreed
  upstream; until then, no claim of iOS support is made.
- **Older Android builds.** Do not recognize the file. If a bundle arrives as
  a mesh file attachment, it is shown as a generic file.
- **Wear OS.** Excluded from v1. Wear has no camera, ML Kit or zxing
  dependency, and its shared sources exclude Nostr verification, so it cannot
  check items (see
  [MESH_GATEWAY_V1 Compatibility summary](MESH_GATEWAY_V1.md#compatibility-summary)).
  Wear's `minSdk` is 33 (`wear/build.gradle.kts:16`).
- **Phone platform floor.** `minSdk` is 26 (`gradle/libs.versions.toml:6`).
  `CreateDocument`, `OpenDocument`, `FileProvider` and CameraX work there. No
  API 29+ storage API (such as `MediaStore.Downloads`) is used.
- **Browsers.** v1 offers browser-only phones neither give nor get. The format
  is specified so that a future web client could show and read codes, but
  camera reading, screen wake lock and full brightness all need a secure
  context or are unavailable to web pages, and the hub page is plain HTTP (see
  [Camera and secure-context requirement](#camera-and-secure-context-requirement)).
- **Dependencies.** The phone app already ships zxing core 3.5.4, ML Kit
  barcode scanning 17.3.0 and CameraX 1.6.1 (`gradle/libs.versions.toml:62,
  68-69`). The lockfile also resolves
  `play-services-mlkit-barcode-scanning` 18.3.1 and other Play services
  artifacts (`app/gradle.lockfile:225-229`). A RaptorQ implementation is new.
  Any new library must update the lockfiles and verification metadata as
  `docs/reproducible-builds.md` requires.

## Test plan

Types follow [MESH_GATEWAY_V1 Test plan](MESH_GATEWAY_V1.md#test-plan):
**U** = JVM unit (JUnit 4 / Robolectric, injected clock, randomness and
dispatcher), **S** = JVM simulation with in-memory transports and a fake
camera frame source, **M** = Mesh Lab on physical devices
(`docs/release-gate-runbook.md`), **Man** = manual test on physical devices
with real cameras and screens. Negative tests assert fail-closed outcomes.
Randomized tests take their seed from `TEST_SEED` and print it on failure
(`docs/testing-conventions.md`).

### Implementation seams

- `BundleCodec` (container encode and decode) and `QrFrameCodec` (frame and
  Base45) as pure Kotlin with no Android types, in a package that Wear could
  include later.
- `BundleImporter` takes an injected `Clock`, `Random`, `CoroutineDispatcher`,
  the gateway validator, and a sink for the relay store, so tests can count
  store admissions, evictions, seen-set inserts and re-originations.
- `QrReceiveSession` (or its ViewModel) takes an injected `Clock` and
  `CoroutineDispatcher` and has an explicit `onLeave()`, so the 3 s re-lock,
  15 s stall and 10-minute discard are tested by advancing a fake clock, never
  by sleeping (`docs/testing-conventions.md`).
- A `FountainDecoder` interface, so tests can drive symbol loss and order.
- A bounded `ByteSource` with an injected byte limit, an injected clock for
  the no-progress deadline, and counters for bytes read and the largest
  buffer allocated, so tests can prove that reading stops at
  `MAX_BUNDLE_BYTES + 1` without relying on heap or OOM behaviour.
- `StagedExportStore` with an injected clock and directory, so the staged-file
  sweep can be tested.
- The extracted scanner emits every decoded string with its bounding box to a
  callback that a fake frame source can drive.
- Debug-only `TestHookDriver` commands in `app/src/debug/`, next to the
  existing ones (`app/src/debug/java/com/bitchat/android/testhook/TestHookDriver.kt:59-95`):
  `bundle_export` (builds a bundle of at most 16 KiB and returns it as base64
  in the JSON result, plus counts), `bundle_import` (takes a `bundle_b64`
  extra and imports it **without the UI tap**, debug builds only),
  `bundle_counts` (counts only), and `wifi_aware` (enable or disable, next to
  the existing `ble` toggle at
  `app/src/debug/java/com/bitchat/android/testhook/TestHookDriver.kt:92`).
  Mesh Lab moves the base64 bundle from A to B itself. Because the hook path
  skips the tap, the tap path is covered only by U test 1.4 and the manual
  tests.

### 1. Safety

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 1.1 | Export with private messages, mesh chat, files and Noise sessions present | Bundle contains only `0x01` items whose bytes equal relay-store envelopes; a scan of the bundle for every private and chat test string finds none | U |
| 1.2 | Bundle with an item type `0x02`, `0x00` or `0xFF` | Whole bundle rejected; zero store admissions | U |
| 1.3 | Bare Nostr event (no envelope) as an item | Whole bundle rejected (item byte 0 is not `0x01` or bytes 8 to 14 are not `{"id":"`); never published | U |
| 1.4 | Import without the confirmation tap (intent fired by another app) | Zero store admissions, zero seen-set inserts, zero transmissions | U |
| 1.5 | Panic during scan, during import verification, after confirmation, and while the staged export file exists | Work cancelled; buffers dropped; `bundle_share/` absent; a simulated failed deletion keeps transports stopped | U; M for the staged-file case only |
| 1.6 | Bundle code under a recording logger | No id, prefix, pubkey, digest, transfer id, URI, file name or item count logged | U |
| 1.7 | Own unreleased outbox post present at export | Excluded by default; included only with the advanced switch | U |
| 1.8 | Bundle type `0x02` (`APP_UPDATE`) | Rejected; no install path reachable | U |
| 1.9 | Inbound intent with a `file://` URI, a URI for BuzzChat's own `FileProvider` authority, and a non-bundle `content://` file | All three rejected before any read of BuzzChat's private files; each shows the same generic message | U |
| 1.10 | Tap on Add posts delivered with the obscured flag set | Ignored; zero admissions | U |
| 1.11 | Mesh file send of a file that starts with the bundle magic | Refused with the one-line explanation; nothing transmitted | U |
| 1.12 | Bundle received as a mesh file attachment, then imported or cancelled | The received copy in the incoming cache is gone after the import screen closes | U |
| 1.13 | Staged file after Send file: right after the chooser launches; after foreground at 9 and 11 minutes; app start sweep; panic | Present after launch and at 9 minutes; absent at 11 minutes, after the sweep of a file older than 10 minutes, and after panic | U |
| 1.14 | Give posts with an empty relay store, and with only own unreleased posts | "No posts to give yet."; no bundle encoded, no file written, no frames shown | U |

### 2. Malformed and hostile containers

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 2.1 | Truncation at every byte offset of the golden bundle | Rejected without crash; zero admissions | U |
| 2.2 | Wrong magic, line-ending converted magic, version `0x02`, non-zero flags, non-zero reserved fields | Rejected | U |
| 2.3 | `itemCount` 0 or 201; `itemCount` disagreeing with the records; `bodyLength` too short, too long, or past `totalLength` | Rejected before any item is parsed | U |
| 2.4 | `totalLength` not a size class; file longer or shorter than `totalLength`; one non-zero padding byte; trailing bytes after the padding | Rejected | U |
| 2.5 | Digest off by one bit | Rejected before item parsing | U |
| 2.6 | Items out of order; two items with the same id; a non-hex ordering key; an item whose byte 0 is `0x02` (unknown envelope version); an item whose bytes 8 to 14 are not `{"id":"` | Whole bundle rejected | U |
| 2.7 | `itemLength` 0, 255, 257 or 20,992; `itemFlags` non-zero | Rejected | U |
| 2.8 | Zip, gzip and zlib files, including a deflate bomb that would expand to 10 GB, renamed to `.buzzbundle` | Rejected at the magic check; an injected inflater counter shows zero calls | U |
| 2.9 | Giant input: a 100 MB stream with a valid header claiming 2 MiB; a stream that never ends | Bytes read equal `MAX_BUNDLE_BYTES + 1`; largest buffer allocated ≤ `MAX_BUNDLE_BYTES + 1`; rejected | U |
| 2.10 | Content provider reports a size of 1 KB for a 50 MB stream | Reported size ignored; read cap holds | U |
| 2.11a | MESH_GATEWAY_V1 test 2.7 cases that change the envelope length, byte 0, bytes 8 to 14 or the id (truncation, total length not a multiple of 256, whitespace, reordered keys, uppercase hex in the id) placed inside an otherwise valid bundle | Whole bundle rejected; zero Schnorr calls | U |
| 2.11b | All other MESH_GATEWAY_V1 test 2.7 cases (wrong `eventLength`, non-zero padding, reserved flag set, invalid UTF-8, forbidden tag, U+2028, non-ASCII `t` tag, duplicate `content` key, extra field, `1.7e9` `created_at`, uppercase hex outside the id, `+`/`-` in a hex field, wrong-length sig), each re-padded to a valid length and placed inside an otherwise valid bundle | That item rejected; other items accepted; no Schnorr call for the bad item | U |
| 2.12 | Content provider that blocks forever without sending a byte | Stream closed after 10 s with no progress (fake clock); import fails closed; a new import can start | U |

### 3. Forged, expired and duplicate content

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 3.1 | 200 items, all with forged signatures but valid PoW | All rejected; at most 400 Schnorr calls; "No new posts found." screen with no Add button | U |
| 3.2 | 200 items, all expired by 1 s past `expiresAt + 600` | All rejected as expired; none stored, marked seen or re-originated | U |
| 3.3 | Items with `created_at` 11 min in the future | Rejected | U |
| 3.4 | Bundle of posts already in the relay store | Counted as already held; no re-origination; store unchanged | U |
| 3.5 | Envelope with `REQUIRE_TOR` cleared after signing | Envelope signature fails; item rejected | U |
| 3.6 | Empty store; 200 valid new text posts (distinct keys) with ids ground to sort last | Exactly 100 stored, chosen uniformly at random (chi-square over seeds); exactly 100 seen-set inserts, all for stored ids; zero evictions | U |
| 3.7 | Valid post left out by the import quota, then received from the mesh | Accepted from the mesh, because it was never marked seen | U |
| 3.8 | 11 valid posts under one persona key | At most 10 stored | U |
| 3.9 | A post received from the mesh and later evicted (id still in the seen set), then imported | Counted as already held; not stored; not re-originated a second time | U |

### 4. Flooding

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 4.1 | Empty store; the same 200-post bundle imported 50 times with no other store change | First import stores exactly 100; imports 2 to 50 store 0, evict 0 and re-originate 0; total re-originations = 100 | U |
| 4.2 | 50 different 200-post bundles imported back to back | `IMPORT` never holds more than 100 slots or 512 KiB; zero evictions of non-expired posts; radio-link posts keep their slots | U, S |
| 4.3 | Import of 200 posts while two radio links each deliver posts | `IMPORT` re-originations limited to one fair share; radio-link posts still re-originated within their share | S |
| 4.4 | 1,000 share intents in a burst from another app while one import is active | Exactly 0 streams opened beyond the active import; at most 1 confirmation shown; 0 admissions without a tap | U |
| 4.5 | Gateway device imports 200 posts | Publish rate stays within the gateway budget | U |
| 4.6 | Store holds 100 radio-link posts; import of 100 maximum-size (20,736-byte) posts | Exactly 25 stored (distinct keys; 25 × 20,736 ≤ 524,288 < 26 × 20,736); every radio-link post still stored | U |
| 4.7 | Store full of live radio-link posts; import of 50 valid new posts | 0 stored; 0 evictions; 0 seen-set inserts; the posts are later accepted from the mesh | U |
| 4.8 | Import of 200 valid new posts into an empty store | Not truncated at 30: 100 stored (the per-link delivery ceiling does not apply to `IMPORT`) | U |
| 4.9 | Two imports from different bundles; the second arrives with `IMPORT` at quota | Every post from the first import is still stored | U |
| 4.10 | Empty store; 200 valid new posts (distinct keys); the confirmation previewed 200 | Result screen reports 100 added and 100 that did not fit; never "Added 200" | U |
| 4.11 | Device in the CRITICAL battery band, not charging, imports 20 valid new posts | 20 stored; 0 re-originations; posts served in sync responses | U |

### 5. Animated QR

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 5.1 | Frames delivered in random order, with 30% loss, with every frame duplicated | Decodes; output equals the bundle | S |
| 5.2 | Only repair symbols (`ESI ≥ K`) | Decodes | S |
| 5.3 | Stream stops at `K − 1` distinct symbols | No decode, no import; progress below 100%; stall message when the fake clock reaches 15 s; state gone after `onLeave()` | S |
| 5.4 | Frames with a bad CRC, invalid Base45, length ≠ `24 + T`, `frameVersion` 2, non-zero flags, ESI top byte non-zero, `F` above 128 KiB or not a size class, `T` other than 622 or 254 | Each ignored; transfer continues | U |
| 5.5 | Frames from a second bundle interleaved (different `transferId`) while the locked transfer keeps progressing | Ignored | S |
| 5.6a | Hostile frames with the same `transferId` and valid CRC but wrong symbols, honest copy of every ESI first (seeded) | Decode succeeds; output equals the honest bundle | S |
| 5.6b | As 5.6a but hostile copies first (seeded) | Digest check fails; state discarded; interference message shown; zero admissions | S |
| 5.7 | Encoder output for both profiles | Fixed QR version and ECC level M for every frame; alphanumeric segment; frame Base45 length within capacity | U |
| 5.8 | Decoded bundle that is a valid file but fails item checks | Same outcome as file import | U |
| 5.9 | Sender switches Standard to Robust mid-transfer (same `transferId` and `F`, new `T`) | Receiver re-locks on the new `T` with no user action and decodes | S |
| 5.10 | One hostile frame with another `transferId` arrives first, then the honest stream | Receiver re-locks on the honest transfer when the fake clock passes 3 s with no progress, and decodes | S |
| 5.11 | Decoding fails with `K + 20` distinct symbols held (fake decoder) | No further symbols stored; state discarded; "That did not work. Try again." | S |
| 5.12 | Image with two codes of different sizes | Frames from the larger code preferred | U |
| 5.13 | Sender display after 2 minutes; app backgrounded; screen off | Display stops; previous brightness restored | U |

### 6. Device, camera and hand-off (physical)

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 6.1 | Two phones with BLE and Wi-Fi Aware off (hook toggles, or B without Wi-Fi Aware): bundle exported on A and imported on B through the debug hooks (no tap) | B stores the posts; after radios are turned on, B re-originates each once with origin TTL in 1..3 | M |
| 6.2 | B is a gateway with the relay fixture of MESH_GATEWAY_V1 | Imported posts published over Tor; one event per id at the fixture | M |
| 6.3 | Low light (dim room), bright sunlight glare, a cracked or dirty screen, and a low-end camera phone, with a 16 KiB bundle | Median end-to-end time 15 s or less in Standard, or after switching to Robust; record time, frames decoded per second and frames needed vs `K` | Man |
| 6.4 | Oldest supported phone (API 26) as receiver at 10 fps, and as sender | Receiver: record frames decoded per second for each profile; sender: no dropped display frames | Man |
| 6.5 | Screen recorder or second phone filming the code | Second phone also decodes the same posts (confirms the public-content warning is accurate) | Man |
| 6.6 | Share via Quick Share, AirDrop (to and from a Mac or iPhone acting as a file relay) and a messaging app | File arrives as `posts.buzzbundle`; import works; the received-file notice is shown; record what device name and metadata the receiver sees, locally only; the staged file on the sender is gone after the sweep | Man |
| 6.7 | Share target that wraps the file in a zip or renames it | Rejected or imported by magic regardless of name | Man |
| 6.8 | Camera permission denied; device without a camera | "Open a file instead" works; no crash | U, Man |
| 6.9 | Storage nearly full during "Send file" | Clear error; no partial file left in `bundle_share/` | U, Man |
| 6.10 | Ten-second test: five people who have never used BuzzChat give and get posts | Each completes without help; median end-to-end time 15 s or less; record time | Man |
| 6.11 | Two phones, radios off: Show code on A, Get posts and Add posts on B (with the tap) | B stores the posts; preview showed them before the tap | Man |
| 6.12 | A stock camera app scans the animated code | Record what it shows; BuzzChat does not claim the stock camera opens anything | Man |

Tests 6.3 to 6.7, 6.10, 6.11 and 6.12 cannot run in Mesh Lab, which drives
devices over adb and has no way to aim a camera. Evidence from them, including
screenshots and recordings, stays local.

## Golden vectors

All data is synthetic. The single item is the `0x2A` envelope from
[MESH_GATEWAY_V1 Golden vectors](MESH_GATEWAY_V1.md#golden-vectors) (author
secret key `3`, `created_at` 1700000000, flags `0x03`). Its bytes are taken
from that section unchanged. The values below were computed with Python's
`hashlib` and `zlib` from those bytes; the envelope hash recomputed from them
matches the value printed in MESH_GATEWAY_V1. On re-verification, every value
in the block below was recomputed independently and matched, including the
event id from its NIP-01 serialization, and both the event signature and the
envelope signature were checked with a reference BIP-340 verifier against the
author pubkey. Base45 output follows RFC 9285 and the self-check strings below.

```text
# item: GatewayPostEnvelope, 512 bytes
#   SHA-256(envelope) =
e4e4e2618e96bacdaa929652753499c714933517ed68de87368db6673b4ad1ac
#   ordering key (envelope bytes 15..78) =
00005452b9eeac2f3ac54a033fc15940cc9f0b73db0d9e317f50965368b6704a

# header: magic, version 01, type 01, flags 00, reserved 00,
#         itemCount 1, reserved 0000, bodyLength 516, totalLength 1024
89 42 5A 42 0D 0A 1A 0A 01 01 00 00 00 01 00 00 00 00 02 04 00 00 04 00

# item record header: type 01, flags 00, itemLength 512
01 00 02 00
# followed by the 512 envelope bytes

# bundleDigest = TaggedHash("bitchat/offline-bundle/v1", header || item record)
11 C2 4A E6 C0 D8 14 0C 92 EC 93 F0 F5 7C F2 82 B7 FB B4 C2 C1 77 47 C1
6F 11 C7 86 DA 21 5A 23

# padding: 452 zero bytes; total file length 1024 (size class 1 KiB)
# SHA-256(whole bundle file) =
806373c55d6d26bc48838da07c074604f41b908fbd699b9f68929cf9c6922d7a

# QR frame, Standard profile: T = 622, K = 2, ESI 0 (systematic)
# frame header: version 01, flags 00, transferId, F 1024, T 622, ESI 0
01 00 11 C2 4A E6 C0 D8 14 0C 00 00 04 00 02 6E 00 00 00 00
# symbol = bundle bytes 0..621; frame length 646
# crc32 = BE 9E CA 62
# Base45 text: 969 characters, starting "V501B24L93HO2O2000YM0$D0000000"
# SHA-256(Base45 text as ASCII) =
a50ed160c209cf98dbc4207d1d56615ee18b123315cba69417d1908fb551d3d3

# QR frame, Standard profile, ESI 1 (bundle bytes 622..1023, then 220 zero bytes)
# frame header ends 00 00 00 01; crc32 = 5A F3 05 CE
# Base45 text: 969 characters, starting "V501B24L93HO2O2000YM0$D0000100"
# SHA-256(Base45 text) =
75cb90101b5df97788f63089de20fefc03309dbcb7c4ff25d51813750ccdbc83

# QR frame, Robust profile: T = 254, K = 5, ESI 0
01 00 11 C2 4A E6 C0 D8 14 0C 00 00 04 00 00 FE 00 00 00 00
# crc32 = 93 C0 A5 79; Base45 text 417 characters, starting
# "V501B24L93HO2O2000YM0T50000000"
# SHA-256(Base45 text) =
fd6ac16784d6089ac69e433ce1f0139ad5cc5f2d71c12ea34a075bd0698f12ca

# Base45 self-check (RFC 9285): "AB" -> "BB8", "Hello!!" -> "%69 VD92EX0"

# RaptorQ repair symbols (ESI >= K) for both profiles: TO BE GENERATED with
# the chosen RFC 6330 implementation.
# Two-item bundle (adds a multibyte-UTF-8 post with g and t tags) and the
# negative vectors of tests 2.1-2.8 and 5.4: TO BE GENERATED.
```

**Before this specification leaves draft**, the vectors must also include, as
literal bytes:

- a two-item bundle with both ordering keys shown, and the same pair swapped
  as a negative vector (whole bundle rejected);
- one repair-symbol frame (`ESI = K`) per profile;
- literal negative frames for each test 5.4 case;
- the negative containers of tests 2.1 to 2.8 and 2.11a.

Until a RaptorQ implementation is chosen (open question 2), test 5.2 and the
repair-symbol vectors are blocked, and no client may emit or accept animated
codes outside tests.

Fixture and executable vectors:

- Add `app/src/test/resources/contracts/offline-bundle-v1.json` holding every
  value above, the full bundle and frame bytes in hex, and the negative cases,
  as `docs/testing-conventions.md` requires for interoperability fixtures.
- `app/src/test/kotlin/com/bitchat/android/bundle/BundleCodecTest.kt`:
  `` `encoder matches golden bundle bytes` ``,
  `` `decoder rejects every truncation` ``,
  `` `non zero padding and unaligned total length are rejected` ``,
  `` `compressed and archive inputs never reach an inflater` ``.
- `app/src/test/kotlin/com/bitchat/android/bundle/QrFrameCodecTest.kt`:
  `` `systematic frames match golden bytes and base45 text` ``,
  `` `invalid base45 and bad crc frames are ignored` ``.
- `app/src/test/kotlin/com/bitchat/android/bundle/BundleImporterTest.kt`:
  `` `forged and expired items are never stored` ``,
  `` `repeated import admits nothing when the store is unchanged` ``,
  `` `import never evicts a live post` ``,
  `` `import byte quota holds for maximum size items` ``,
  `` `import quota admission is random not bundle order` ``.

Vectors change only with an intentional, coordinated version bump
(`docs/client-rewrite-contracts.md`).

## Implementation references

- `docs/MESH_GATEWAY_V1.md` (validator, seen set, relay store, forwarding,
  flood control, logging rules)
- `app/src/main/java/com/bitchat/android/ui/VerificationSheet.kt` (scanner and
  QR generation to extract)
- `app/src/main/java/com/bitchat/android/hotspot/QrCodeGenerator.kt`
- `app/src/main/java/com/bitchat/android/ui/ApkDownloadViewModel.kt` and
  `app/src/main/java/com/bitchat/android/ui/AboutSheet.kt` (share-sheet pattern)
- `app/src/main/java/com/bitchat/android/ui/media/FilePickerButton.kt`
- `app/src/main/java/com/bitchat/android/features/file/FileUtils.kt`
- `app/src/main/res/xml/file_paths.xml`, `app/src/main/AndroidManifest.xml`
- `app/src/main/java/com/bitchat/android/ui/ChatViewModel.kt` (panic)
- `app/src/main/java/com/bitchat/android/hotspot/HotspotActivity.kt` and
  `app/src/main/java/com/bitchat/android/hotspot/ApkWebServer.kt` (local HTTP
  hub)
- `app/src/debug/java/com/bitchat/android/testhook/TestHookDriver.kt`
- `docs/reproducible-builds.md`, `docs/testing-conventions.md`,
  `docs/release-gate-runbook.md`

### Follow-ups found while writing this specification

- `docs/file_transfer.md:161` says Android rejects unknown file TLVs, but the
  decoder skips them
  (`app/src/main/java/com/bitchat/android/model/BitchatFilePacket.kt:130-141`).
  The class comment in the same file describes an 8-byte size and chunked
  content (`BitchatFilePacket.kt:8-15`), which its encoder does not use.
- `docs/file_transfer.md` contains absolute developer-machine paths inherited
  from upstream (for example around line 136). They should be replaced with
  repository-relative paths, per `AGENTS.md`.
- `docs/BUZZCHAT_PRINCIPLES.md` lists only MESH_GATEWAY_V1 under Related
  specifications; this specification should be added there.
- The APK share path builds the provider authority from
  `context.packageName`
  (`app/src/main/java/com/bitchat/android/ui/ApkDownloadViewModel.kt:261-265`)
  while the manifest declares `${applicationId}.fileprovider`
  (`app/src/main/AndroidManifest.xml:98-107`). They are equal at runtime; the
  bundle code and the inbound authority check should use one shared constant.

## Open questions

1. **Format agreement.** Will upstream bitchat (iOS and Android) adopt this
   container and frame format, or propose another? Until agreed, the magic,
   types and MIME type are BuzzChat-local.
2. **RaptorQ implementation.** Write one in pure Kotlin (and later in JS or
   WASM for browsers), or add a library? Any library must pass the
   reproducible-build lock and verification process. Is a simpler LT code with
   a fully specified generator worth defining instead, at the cost of more
   overhead?
3. **ML Kit and de-Googled phones.** Does barcode scanning need Google Play
   services at runtime? The lockfile resolves
   `play-services-mlkit-barcode-scanning` next to the bundled
   `com.google.mlkit:barcode-scanning` (`app/gradle.lockfile:228, 268`); which
   dependency pulls it in was not checked. A zxing-only decode path may be
   needed for phones without Play services, and for a fully FOSS build.
4. **ML Kit `rawBytes`.** If `rawBytes` reliably returns the decoded payload
   of a byte-mode segment, binary frames would gain about 3% over Base45. Is
   that worth losing browser decoder compatibility?
5. **Decode rate on low-end phones.** Can ML Kit decode version-20 codes at
   10 fps on the oldest supported phones, with the explicit analysis
   resolution required above? The receive rate is bounded by one frame per
   decode latency. Tests 6.3 and 6.4 will tell; if Standard is not reliable,
   Robust becomes the default and the profiles may need to change.
6. **zxing fixed version.** Confirm that zxing 3.5.4 supports a fixed QR
   version hint and selects alphanumeric mode for Base45 text.
7. **Denser profiles.** Is a colour (libcimbar-style) or larger-version
   profile worth adding for good cameras, and how would the sender choose it
   without adding a setting?
8. **Browser give and get.** v1 makes no browser claim. A hub page on plain
   HTTP cannot open the camera, hold a wake lock or set brightness. Can the hub
   serve a secure context offline (for example a self-signed certificate,
   which browsers warn about)? Which JS or WASM QR decoder would a browser
   receiver use where `BarcodeDetector` is not available, and what decode rate
   does it reach for version-20 and version-12 codes on iOS Safari, Android
   Chrome and Firefox? A browser also lacks a vetted BIP-340 verifier today. If
   the hub ever serves bundle files, its HTTP server must first stop logging
   request URIs and remote addresses and cap downloads per client.
9. **Wear.** Could a watch show an animated code (export only), given its
   small screen and the zxing dependency it would need?
10. **Import quota.** Are 100 of 200 slots and 512 KiB of 2 MiB the right
    share for `IMPORT`? Should a gateway device give imports their own publish
    queue share? The deviations in
    [Amendments to MESH_GATEWAY_V1](#amendments-to-mesh_gateway_v1) must be
    written into MESH_GATEWAY_V1.
11. **Export selection.** v1 picks uniformly at random among eligible posts.
    Should export instead prefer posts with `REQUIRE_TOR` set or posts not yet
    acknowledged? Should there be an export choice that leaves out `g`-tagged
    posts, or keeps only posts older than a minimum age, to weaken what the
    contents reveal about the carrier, without adding a setting to Show code?
12. **FileProvider scope.** `file_paths.xml` exposes the whole cache and files
    directories. Can these be narrowed without breaking the camera, file
    viewer and APK sharing flows?
13. **Inbound intent surface.** Is the new exported `BundleImportActivity`,
    with the URI rules and tap protection above, worth the extra attack
    surface, or should v1 import only from inside the app?
14. **App-update bundles and the hub APK.** Pinned single key or threshold
    signatures? How is a release key rotated offline? The existing hub serves
    its APK over plain HTTP with no digest or fingerprint
    (`app/src/main/java/com/bitchat/android/hotspot/ApkWebServer.kt:40-94`).
    How should the hub page and QR show the release-signing certificate
    fingerprint so that a person can check it in ten seconds?
15. **Verification cost.** How long do 400 Schnorr verifications take on the
    oldest supported phone with the current verifier? This bounds the worst
    import time.
16. **MIME registration.** Register `application/vnd.buzzchat.post-bundle`
    with IANA, or use it unregistered? Some share targets may still deliver it
    as `application/octet-stream`.
17. **Per-export salt.** Should each export add a random salt (inside the
    digest, ignored by item checks), or allow random padding bytes, so that
    copies cannot be matched by hash? That gives up "same bytes everywhere",
    the combining of two screens showing the same set, and fixed golden
    vectors for the whole file.
18. **Import without proactive transmission.** Should the importer offer a
    store-only choice that serves imported posts through sync without
    re-originating them, or wait to re-originate until at least one other node
    has been heard, to weaken courier identification?
19. **Stock-camera scans.** Should each frame start with a fixed uppercase
    URL-like prefix in QR alphanumeric mode so that a system camera offers to
    open BuzzChat or its install page and lands on Get posts? It costs
    capacity per frame, changes the golden frame vectors, and whether stock
    camera apps open such links is unverified.
20. **Transfer fingerprint.** Should both screens show a short visual
    fingerprint of the `transferId`, so that two people can confirm the
    receiver locked on to the right screen? It adds a UI element and makes the
    matchable id more visible.
21. **Staged-file deletion without reopening the app.** Is a scheduled
    background deletion (for example with a work scheduler) worth a new
    dependency, so that the staged file does not wait for the next app start?
22. **Show code on low battery.** Show code holds the screen at full
    brightness. Should it refuse, shorten the 2-minute timer, or only warn in
    the CRITICAL battery band, given the principle that BuzzChat must work on
    a low battery and the posts can also go by file?
