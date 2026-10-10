# Join by link v1

Status: **draft / proposed**. Nothing in this document changes the bitchat mesh
wire format. Browsers speak the existing binary packet, announcement and Noise
formats. The new formats here are **local to one hub**: the HTTP routes, the
hub-to-browser WebSocket framing and its Noise channel, the bind proof, the
WebRTC signaling record and the web-bundle manifest. They never travel over BLE
or Wi-Fi Aware and need no upstream type assignment. Gateway posts from browsers use the PROPOSED `0x2A`-`0x2C` types of
[Mesh gateway posts v1](MESH_GATEWAY_V1.md) and inherit that document's release
blockers.

Join by link lets a person who has no BuzzChat or bitchat app scan a code or open
a link and chat with the local mesh in their phone's browser (iOS Safari,
Android Chrome, Firefox) within seconds. An Android phone running BuzzChat (the
**hub**) already runs a Wi-Fi P2P group and a small HTTP server that hands out
the APK (`app/src/main/java/com/bitchat/android/hotspot/ApkWebServer.kt:13-22`).
v1 extends that server so it also serves:

1. a bundled, static **web client**;
2. a **WebSocket bridge** that connects each browser to the mesh through the hub;
3. **WebRTC signaling**, so browsers on the same network can open direct
   DataChannels to each other and keep talking when the hub app is gone, if the
   network itself survives (see [Browser-to-browser links](#browser-to-browser-links-webrtc)).

Each browser is a full bitchat identity (a "virtual peer") whose keys exist only
in that browser tab. The hub carries signed bytes; it never signs for a browser
and, running honest code, cannot read a browser user's private messages.

All multi-byte integers in this document use network byte order (big-endian).
Unix times are seconds since 1970-01-01T00:00:00Z unless a field says
milliseconds.

## Purpose and scope

### Goals

- **Zero-install join.** Satisfy the first rule of
  [BuzzChat principles](BUZZCHAT_PRINCIPLES.md): scan, and you are chatting,
  with no account, number, password or install, on whatever phone you hold
  (`docs/BUZZCHAT_PRINCIPLES.md:9-17, 21-28`).
- **One invite.** One "Invite" screen on the hub works for every phone: the web
  client opens for everyone, and Android users are offered the APK as a
  secondary choice.
- **Indistinguishable to the mesh.** Existing iOS bitchat, Android bitchat and
  BuzzChat nodes see a browser user as an ordinary peer whose signed packets
  were relayed by the hub. No change is needed on any node except the hub,
  subject to the verifier-version caveat in [Signing](#identity-model) and
  [Compatibility summary](#compatibility-summary): verifiers that recompress a
  decoded payload before checking its signature reject some browser packets.
- **End-to-end private messages.** Private messages to and from a browser use
  Noise XX inside the browser. The hub only relays ciphertext.
- **Internet reach with consent.** A browser user can compose a gateway post
  per MESH_GATEWAY_V1. A browser that happens to have internet may, after an
  explicit opt-in, publish other people's non-Tor posts directly to Nostr
  relays.
- **Fail closed** on integrity, identity, consent, limits and Tor, as the
  principles require (`docs/BUZZCHAT_PRINCIPLES.md:33-36`).
- **Honest limits.** The page and the hub UI never claim more than the browser
  platform and the hub can guarantee.

### Non-goals

- Not a replacement for the app. Browsers get public chat, private text
  messages and gateway posts. They do not get file transfer (`0x22`), private
  media, live voice (`0x29`), geohash or Nostr DMs, favorites, or background
  delivery.
- Not BLE from the browser. Web Bluetooth is absent on iOS Safari and Firefox,
  and in Chrome it is central-only; v1 does not use it.
- Not an internet service. The page loads nothing from the internet, and no
  BuzzChat server exists anywhere.
- Not a way for a browser to satisfy `REQUIRE_TOR`. A page cannot use the hub's
  Arti SOCKS proxy (`app/src/main/java/com/bitchat/android/net/OkHttpProvider.kt:68-79`).
- Not persistent identity. A browser identity lasts as long as the tab (see
  [Identity](#identity-model)).
- Not protection against a malicious hub for a first-time visitor. This is the
  top residual risk and is stated as such below.

## Background: what exists today

| Fact | Evidence |
| --- | --- |
| The server is `NanoHTTPD(port)` on port 9999, built with no bind host, so it listens on every interface | `app/src/main/java/com/bitchat/android/hotspot/ApkWebServer.kt:13-22` |
| Routes: `/bitchat.apk` serves the APK; `/favicon.ico` returns 404; every other URI and method returns the cached landing page | `ApkWebServer.kt:40-56` |
| Every request is logged with the client IP | `ApkWebServer.kt:43` |
| The APK response advertises `Accept-Ranges: bytes` but no Range handling exists, and an exception message is echoed in the 500 body | `ApkWebServer.kt:61-94` |
| The landing page has no CSP or other security headers | `ApkWebServer.kt:99-105` |
| The server starts with NanoHTTPD's default runner and no connection cap | `ApkWebServer.kt:302-310` |
| The server starts only after the P2P group is up and lives in an Activity-scoped ViewModel, not a service | `app/src/main/java/com/bitchat/android/hotspot/HotspotViewModel.kt:80-100` |
| `getConnectionInfo` returns the P2P IPv4 address, or `192.168.49.1` when none is found, with no way for callers to tell the two apart; the server starts whenever it returns non-null | `app/src/main/java/com/bitchat/android/hotspot/HotspotManager.kt:357-367`; `app/src/main/java/com/bitchat/android/hotspot/HotspotViewModel.kt:76-86` |
| The advertised URL is built as `http://$ipAddress:$port`: no path and no token | `app/src/main/java/com/bitchat/android/hotspot/HotspotActivity.kt:521` |
| The Wi-Fi QR carries the WPA passphrase in clear (`WIFI:S:...;T:WPA;P:<password>;;`); the group is WPA2-PSK created with `setPassphrase` on 2.4 GHz | `app/src/main/java/com/bitchat/android/hotspot/QrCodeGenerator.kt:40`; `HotspotManager.kt:509-513` |
| The hosted group name is persisted in the `hotspot` SharedPreferences (`owned_group_name`) | `HotspotManager.kt:43-44, 96-99, 504-506` |
| The client count shown is `group.clientList.size`, polled every second | `HotspotManager.kt:32, 365, 606-630` |
| Panic is a triple-tap on the chat screen; the panic flow does not touch the hotspot | `app/src/main/java/com/bitchat/android/ui/ChatScreen.kt:802`; `app/src/main/java/com/bitchat/android/ui/ChatViewModel.kt:1454-1533` |
| The hotspot stops Wi-Fi Aware for its whole lifetime | `app/src/main/java/com/bitchat/android/wifi-aware/WifiAwareController.kt:138-164` |
| Only `org.nanohttpd:nanohttpd:2.3.1` is a dependency; there is no WebSocket artifact | `gradle/libs.versions.toml:55-56, 137-138` |
| Wire peer ID = `hex(SHA-256(noiseStaticPublicKey)[0..8])` | `app/src/main/java/com/bitchat/android/noise/NoisePeerIdentity.kt:18-29` |
| An ANNOUNCE is accepted only with that binding, a 32-byte Ed25519 key, ≤ 10 min skew and a valid Ed25519 signature. Nothing proves freshness or key possession: a captured ANNOUNCE replays for 10 minutes | `app/src/main/java/com/bitchat/android/mesh/AnnouncementIdentityValidator.kt:9-36` |
| An already-seen ANNOUNCE passes the duplicate check again when its TTL is ≥ 7; TTL is outside the signature | `app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt:80-90`; `app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:116-132` |
| `MeshCore.processIncoming` returns `Unit` and queues the packet on a per-peer actor; acceptance is visible only through the `onAnnounceProcessed` hook | `MeshCore.kt:44-55, 180-194, 471-475`; `app/src/main/java/com/bitchat/android/mesh/PacketProcessor.kt:61-86` |
| BLE packets are validated and relayed by BLE's own `PacketProcessor`; they reach other transports only through `broadcastRoutedPacket`, after the relay decision | `PacketProcessor.kt:117-166`; `app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt:203-209` |
| Every node relays any packet with TTL ≥ 4 unconditionally; no inbound TTL clamp exists | `app/src/main/java/com/bitchat/android/mesh/PacketRelayManager.kt:58-66, 142-146` |
| Encoding DEFLATEs a payload of ≥ 100 bytes whose unique-byte ratio is < 0.9, unless the packet carries the originator's decoded wire bytes (`WirePayload`, added in commit `4b2a9c3`, 2026-07-31); signers sign that compressed preimage | `BinaryProtocol.kt:243-259`; `app/src/main/java/com/bitchat/android/protocol/CompressionUtil.kt:21-35`; `app/src/main/java/com/bitchat/android/util/AppConstants.kt:69`; `BluetoothMeshService.kt:1590-1603` |
| Android announces every 30 s in the foreground (60/120/300 s in the background); a peer goes stale after 180 s | `app/src/main/java/com/bitchat/android/mesh/PowerManager.kt:338-345`; `AppConstants.kt:15` |
| `GossipSyncManager` keeps the latest ANNOUNCE per peer (younger than the stale timeout) and recent broadcast messages; `handleRequestSync` replays them to one peer, filtered by a GCS filter | `app/src/main/java/com/bitchat/android/sync/GossipSyncManager.kt:44-48, 128-140, 177-207` |
| Shared mesh code logs peer IDs at `Log.w`/`Log.e` | `SecurityManager.kt:270, 278, 287, 294, 302, 313, 335`; `app/src/main/java/com/bitchat/android/mesh/MessageHandler.kt:72, 205, 222, 400` |
| An Android receiver sends a delivered ack for every private message | `MessageHandler.kt:100, 122, 150, 237-241` |
| MESSAGE, FILE_TRANSFER, VOICE_FRAME and LEAVE must be signed by the key from the sender's verified announce; other types skip this check | `app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt:251-333` |
| Key replacement for a known peer ID is rejected | `SecurityManager.kt:273-296` |
| The signing preimage fixes TTL at 0 and covers every other field, including the route | `app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt:116-132` |
| Every origination API signs with the device's one key, and the signer fails open (sends unsigned on error) | `app/src/main/java/com/bitchat/android/mesh/BluetoothMeshService.kt:1590-1614` |
| Wi-Fi Aware feeds each socket frame into `meshCore.processIncoming(pkt, sender, logicalPeer, ingressLinkID)` | `app/src/main/java/com/bitchat/android/wifi-aware/WifiAwareMeshService.kt:1286-1319`; `app/src/main/java/com/bitchat/android/mesh/MeshCore.kt:180-194` |
| `MeshTransport` is the small interface `MeshCore` needs | `app/src/main/java/com/bitchat/android/mesh/MeshTransport.kt:9-36` |
| `TransportBridgeService` fans packets out to other registered layers, decrements TTL and drops at TTL 0; layers transmit without local validation | `app/src/main/java/com/bitchat/android/service/TransportBridgeService.kt:68-112, 203-226`; `MeshCore.kt:196-207` |
| Relay probability falls to 0.4 as network size passes 100 when TTL < 4 | `app/src/main/java/com/bitchat/android/mesh/PacketRelayManager.kt:142-171` |
| The BLE broadcaster fragments oversize packets itself | `app/src/main/java/com/bitchat/android/mesh/BluetoothPacketBroadcaster.kt:176-184` |
| DIRECT_NEIGHBORS gossip takes at most 10 peers from the cross-transport union | `BluetoothMeshService.kt:1358-1373`; `docs/ANNOUNCEMENT_GOSSIP.md:45-53` |
| `UnifiedMeshService.myPeerID` is the BLE peer ID, and delegates are wired only for BLE and Wi-Fi | `app/src/main/java/com/bitchat/android/mesh/UnifiedMeshService.kt:41-53` |
| Mesh Noise is `Noise_XX_25519_ChaChaPoly_SHA256`, empty payloads, 4-byte explicit nonce prefix, 1024-message replay window. Only the message 1 and 2 size constants are used; `XX_MESSAGE_3_SIZE = 48` is unused and omits the empty-payload tag | `app/src/main/java/com/bitchat/android/noise/NoiseSession.kt:24-41, 293, 301, 337, 349` |
| `TransportBridgeService` is a process-global `object` with one dedup map keyed by kind and logical packet id, timed by `System.currentTimeMillis` | `app/src/main/java/com/bitchat/android/service/TransportBridgeService.kt:20, 203-226` |
| The Wear build compiles the shared `mesh/**` package and `TransportBridgeService` | `wear/build.gradle.kts` (`sharedSourceIncludes`) |
| Public chat payload is `BitchatMessage.toBinaryPayload()` with a literal vector | `app/src/test/kotlin/com/bitchat/android/contracts/ClientRewriteWireContractTest.kt:89-104` |
| Default TTL for regular packets is 7 | `app/src/main/java/com/bitchat/android/util/AppConstants.kt:10` |

## Threat model

| Adversary | Capability | Design response |
| --- | --- | --- |
| **Malicious hub** (top risk) | Serves tampered JavaScript or a trojaned APK; logs IPs, MAC addresses and user agents; reads metadata; knows which browser authored each gateway post; drops, delays or injects bridge traffic; impersonates a browser by holding its keys if its code stole them; plants cookies or storage on the shared origin to track a phone across later hubs | Honest hubs run the reproducible bundle embedded in the signed APK; installed peers can audit a hub (see [Poisoned code](#poisoned-code)); per-session URLs defeat cache poisoning across hubs; the honest client scrubs and never writes browser storage; UI states that browser security depends on the hub. **Cannot be prevented for a first visit.** |
| **Holder of the Wi-Fi QR** (any guest, bystander or camera that saw Step 1) | The WPA2 passphrase is in the Step 1 QR. With it and an observed 4-way handshake, a passive listener derives every guest's per-client keys and reads all plaintext `http://` traffic, including the invite token. Actively, it can run an evil-twin AP with the same SSID, passphrase and `192.168.49.1` address and serve tampered code at the same URLs, or ARP-spoof the hub if the group owner forwards client-to-client traffic (open question 2) | The bridge runs inside a Noise channel to the hub ([Bridge channel](#bridge-channel-noise_nk)), so a passive listener sees only frame sizes and timing, not frames, peer IDs or which socket sent a gateway post. **Code integrity cannot be achieved over `http://`:** an evil twin is a malicious hub for everyone who joins it, and a hub audit cannot detect it. WPA2 is treated as no protection against QR holders. WPA3-SAE, which resists passive decryption by other password holders, is an open question |
| Malicious browser | Floods packets, creates many identities, replays another peer's ANNOUNCE to steal its peer ID, abuses signaling, exhausts hub threads and memory, takes every bridge slot | One identity per socket, bound only after a challenge-response proof of signing-key possession ([Binding](#binding-and-resume)); sender ID bound to the socket; inbound TTL clamp; per-socket and hub-wide rate, size and new-identity limits mapped onto mesh flood control; all packets pass the hub's full `processIncoming` validation before relay; signaling `from` bound to the socket and Ed25519-signed |
| Malicious mesh peer | Sends forged or replayed packets toward browsers; MITMs WebRTC through signaling; floods a browser with private messages to push its receipts over budget | Browsers verify announce binding and signatures themselves; signaling records are signed by the announced Ed25519 key and bind the DTLS fingerprint; receipts are shaped by the browser and never cause a disconnect |
| Other device on another network the hub is on | Reaches port 9999 through Wi-Fi or cellular interfaces | Bind only to the P2P interface address; per-session path token; strict `Host` and `Origin` checks |
| Earlier hub at the same IP | `192.168.49.1:9999` is the same web origin on every hub, so an earlier malicious hub could leave cached scripts, cookies or stored IDs | Every invite uses a fresh random path; assets are content-addressed under that path; responses are `no-store`; the honest client clears every storage area it can reach on load and writes none; the honest hub ignores and expires cookies. A later *malicious* hub can still read what an earlier colluding hub planted in storage the client could not clear before the malicious code ran (residual risk) |
| Local RF / network observer without the passphrase | Sees who joins the hub's Wi-Fi, traffic sizes and timing | Content: private messages are Noise ciphertext and the bridge is Noise-encrypted. Joining a visible `DIRECT-BC-` network is itself observable (residual risk) |
| Device seizure (browser phone) | Inspects browser storage, history and app-switcher snapshots | Keys and messages in memory only; one-tap Wipe; the token is removed from the visible URL after load. Global browser history may still hold the invite URL with its per-session token, which can match a photo of the hub screen or a seized hub's state and so serve as evidence of attendance (residual) |
| Device seizure (hub) | Inspects hub logs, preferences and state | No logging of client IPs, user agents, tokens, peer IDs or content anywhere in the pipeline; bridge state in memory only; panic tears down the group and clears the hotspot preferences |

### Residual risks (stated honestly)

- **Poisoned code on first visit.** A browser trusts whatever JavaScript the
  hub sends. A modified hub app can serve a client that leaks keys, forges
  messages under the browser user's identity, or reads private messages in
  the clear. Subresource Integrity does not help: the `integrity` attribute
  arrives in HTML from the same hub. A safety code shown *by the page* does
  not help either: tampered code shows the genuine code. The only defences are
  external: audits by installed peers, a pinned copy obtained elsewhere, or
  installing an app whose signing certificate was verified elsewhere. Because
  the Wi-Fi passphrase is public, "the hub" here includes any evil twin run by
  anyone who saw the Step 1 code. See [Poisoned code](#poisoned-code).
- **Hub metadata.** Even an honest hub, as the Wi-Fi access point and bridge,
  sees every browser's MAC address, IP address, connection times, packet sizes,
  public messages and who sends private messages to whom. It cannot see private
  message content when it runs honest code.
- **Gateway-post authorship is certain to the hub.** Each socket is bound to
  one peer ID and arrives from one MAC and IP, and a browser never forwards
  other people's traffic to the hub. So every gateway post arriving on a
  socket was authored by that browser. MESH_GATEWAY_V1's random outer
  senderID, origin TTL and delay do not hide this from the hub; its
  first-hop *suspicion* (`docs/MESH_GATEWAY_V1.md:70-86`) becomes certain
  linkage here. The composer says so.
- **The host bears first-hop suspicion for every guest's post.** The hub
  re-originates browser posts onto BLE from its stable, advertised peer ID
  (`docs/MESH_GATEWAY_V1.md:70-76, 751-762`). RF observers see the host's
  device make the first BLE transmission of posts from up to 32 guests. The
  extra hold before re-origination ([Browser as a gateway-post author](#browser-as-a-gateway-post-author))
  only partly mitigates this. The Invite screen tells the host.
- **A QR holder can take every bridge slot.** Address-based limits rely on the
  guest's cooperation: anyone with the passphrase can use many MAC and IP
  addresses on the hub's network. Hub-wide limits bound the damage to the mesh;
  they cannot guarantee a slot to every honest joiner.
- **Cross-hub tracking by colluding malicious hubs.** Storage or cookies
  planted on the shared origin by one malicious hub can be read by a later
  malicious hub's code before or instead of the honest scrub.
- **Gateway helper exposure.** An opted-in browser gateway shows relays its
  phone's address, browser `User-Agent` and an `Origin` of
  `http://<p2p-ip>:9999` on each WebSocket handshake (browser-platform fact),
  which marks it as a join-by-link guest. Its mobile carrier sees connections
  to the named relay hosts (TLS SNI) with timing that can correlate with posts.
- **No secure context.** A page served from `http://<hotspot-ip>` is not a
  secure context (browser-platform fact, not repository code). No Service
  Worker, no `crypto.subtle`, no camera, no Web Share, no `crypto.randomUUID`.
  `crypto.getRandomValues` *is* available. All cryptography is bundled
  JavaScript, and nothing is cached for offline use.
- **No background.** Mobile browsers suspend background tabs and drop
  WebSockets and DataChannels. An insecure page cannot hold a Screen Wake Lock
  (browser-platform fact), so the phone's own screen auto-lock disconnects the
  page even if the user never taps lock. A browser user whose phone locks stops
  receiving; on return the page resumes its identity if the tab survived (see
  [Binding and resume](#binding-and-resume)). Messages sent to them meanwhile are lost unless the sender's
  client retries (bitchat clients keep a private-message outbox; public chat
  has no retry).
- **Identity is the tab.** Closing or reloading the tab creates a new peer ID.
  Other people see a new person, and private conversations restart.
- **Hub dependency.** On the hub's own Wi-Fi P2P group, the hub *is* the
  network. When the hub stops, the Wi-Fi group disappears and every browser
  loses connectivity, WebRTC included. Browser-to-browser survival works only
  on a network hosted by something else (see
  [Browser-to-browser links](#browser-to-browser-links-webrtc)).
- **Wi-Fi Aware down on the hub.** While a hotspot runs, the hub's Wi-Fi Aware
  transport is stopped (`WifiAwareController.kt:138-164`). The hub reaches the
  rest of the mesh over BLE only.
- **Fingerprintable join.** The hub's SSID starts with `DIRECT-BC-`
  (`app/src/main/java/com/bitchat/android/hotspot/HotspotStartupPolicy.kt:19`,
  `SSID_PREFIX`). Anyone scanning Wi-Fi learns a hub is present.

### Fail-closed rules

1. **No hub, no server.** The HTTP server and every WebSocket close when the
   P2P group drops, the hub's foreground service stops, or panic runs. The
   server never runs with no group, and panic also removes the group (see
   [Privacy](#privacy)).
2. **Bind narrowly.** The server binds only to the P2P interface IPv4 address.
   If that address cannot be found, the server does not start and the Step 2
   code is not shown; the Invite screen shows a retry state. Today
   `getConnectionInfo` silently substitutes `192.168.49.1`
   (`HotspotManager.kt:364`), so `ConnectionInfo` must expose the fallback
   (a nullable address or an `isFallback` flag) and `HotspotViewModel` must not
   start the server on it.
3. **Unknown path, nothing.** Any request outside the route allowlist below
   gets a 404. A `/j/<unknown or stale token>` path gets the same fixed
   "Scan the code on the host's screen again" body as `GET /`, which reveals
   nothing about valid tokens. Error bodies never echo exception messages.
4. **Host and Origin.** Requests whose `Host` header is not exactly
   `<p2p-ip>:9999` get 421 (defeats DNS rebinding). A WebSocket upgrade whose
   `Origin` is not exactly `http://<p2p-ip>:9999` is refused.
5. **One identity per socket, proven.** A bridge socket binds to a peer ID only
   after its ANNOUNCE validates *and* a `BIND` frame proves possession of the
   announced signing key over a fresh hub nonce
   ([Binding and resume](#binding-and-resume)). A replayed ANNOUNCE never binds.
   Any later packet whose `senderID` differs (except gateway types, below)
   closes the socket.
6. **Validate before relay.** Every browser packet goes through
   `MeshCore.processIncoming` with the socket's ingress link ID, so it passes
   exactly the checks a BLE neighbor's packet passes before anything is relayed
   or shown. Browser bytes never go to `TransportBridgeService.broadcast`
   directly and never go through `signPacketBeforeBroadcast`.
7. **Verbatim forwarding, clamped TTL.** The hub never re-signs, re-routes,
   re-pads or re-compresses a browser's packet. Only TTL changes, as on any
   relay. TTL is outside the signature, so the hub enforces it on ingress: a
   non-gateway browser packet must carry TTL exactly 7, a gateway packet TTL
   4..7 (MESH_GATEWAY_V1 origin range); anything else closes the socket with
   `BYE 0x02`.
8. **No custodial keys.** The hub never generates, receives, stores or uses a
   browser's private keys, and never decrypts a browser's Noise traffic.
9. **Limits first.** Rate, size and count limits run before decoding into the
   mesh pipeline. A browser over a user-originated limit is throttled, then
   disconnected; excess receipts are dropped, never a reason to disconnect.
10. **Browser verifies too.** The web client verifies announce binding and
    packet signatures for everything it displays, and drops what fails.
11. **Tor is never faked.** A browser never publishes a `REQUIRE_TOR` post and
    never claims `VIA_TOR`. Gateway publishing from a browser needs an explicit
    opt-in each session.
12. **No external loads.** The page loads no resource from outside the hub
    and opens no network connection to anything but the hub, with two
    exceptions: WebRTC DataChannels to other browsers' local host candidates,
    negotiated through signed `SIGNAL` records with `iceServers` empty (see
    [Browser-to-browser links](#browser-to-browser-links-webrtc)), and the
    relay sockets of an opted-in browser gateway.
13. **Encrypted bridge.** No bridge frame is sent or accepted outside the
    Noise channel. A failed handshake or a transport message that fails to
    decrypt closes the socket. This protects against passive listeners only;
    it gives no code integrity.

## Architecture

```text
        browser A ──ws──┐                      ┌── BLE ── iOS / Android peers
        browser B ──ws──┤  hub (Android app)   │
        browser C ──ws──┼─ ApkWebServer ──► WebMeshService ── MeshCore ─┤
           ▲  ▲         │   (HTTP + WS)         (MeshTransport,          └── (Wi-Fi Aware is
           │  └─ WebRTC DataChannel ─┐           TransportLayer "WEB")        down while the
           └──── (optional) ─────────┘                                        hotspot is up)
```

### Hub components

- **`ApkWebServer`** keeps exact-match routing (no filesystem mapping) and gains
  the routes below. Static assets are read from APK assets under a fixed
  allowlist, so no path traversal is introduced.
- **WebSocket support.** NanoHTTPD core has none. v1 adds
  `org.nanohttpd:nanohttpd-websocket:2.3.1`, matching the core version, to
  `gradle/libs.versions.toml`, `app/gradle.lockfile` and
  `gradle/verification-metadata.xml` under `docs/reproducible-builds.md`. A
  small in-house RFC 6455 server is the alternative (see Open questions).
- **`WebMeshService`** (new, phone-only, `app/src/main/java/com/bitchat/android/web/`)
  is modelled on `WifiAwareMeshService`. It implements `MeshTransport`
  (id `"WEB"`) and `TransportBridgeService.TransportLayer`, owns a `MeshCore`
  built on the shared identity and gossip manager, gives every socket a random
  `ingressLinkID`, and feeds each inbound packet into
  `meshCore.processIncoming(pkt, senderHex, "web:<link>", ingressLinkID)`.
- **Binding before the pipeline.** Because `processIncoming` returns nothing
  and validates later on an actor (`MeshCore.kt:180-194`;
  `PacketProcessor.kt:61-86`), `WebMeshService` binds synchronously, before
  `processIncoming`, by calling `AnnouncementIdentityValidator.verify` and the
  key-replacement checks of `SecurityManager.kt:273-296` itself, plus the
  `BIND` proof. Other frames from that socket wait until binding completes.
- **Registration.** `WebMeshService` registers `"WEB"` with
  `TransportBridgeService` so the hub's own originations reach browsers (see
  [Hub to browser delivery](#hub--browser-delivery) for how received packets
  reach them). It publishes its bound virtual peers with `AppStateStore.setTransportDirectPeers("WEB", ...)`
  so the hub's DIRECT_NEIGHBORS gossip can include them, within the 10-peer cap.
  `UnifiedMeshService.refreshDelegates` gains the web service so virtual peers
  appear in the hub's own peer list.
- **Foreground service.** A hub serving browsers runs inside a foreground
  service with a persistent notification, a working "Stop sharing" action and
  a "Wipe" (panic) action.
  Today the hotspot has none, and the `STOP_HOTSPOT` handler in
  `app/src/main/java/com/bitchat/android/hotspot/HotspotActivity.kt:88-95` has
  no sender. The 30-minute partial wake lock
  (`app/src/main/java/com/bitchat/android/hotspot/HotspotManager.kt:757-779`)
  must be renewed while browsers are connected, and released when the last
  browser leaves and the invite screen is closed.
- **Wear.** `WebMeshService` and the web assets are phone-only and are not
  added to the shared include list in `wear/build.gradle.kts`. The shared
  pipeline changes (the post-validation delivery callback in
  `PacketProcessor`, any `MeshCore` hook, the `TransportBridgeService` and
  `SecurityManager` seams, and log redaction) do compile into Wear, because
  `mesh/**` and `TransportBridgeService.kt` are shared. They must default to
  no-ops there, and the test plan runs `:wear:assembleDebug` and the Wear unit
  tests.

### Web client

- Plain ES modules, no framework, no WASM (so CSP needs no
  `wasm-unsafe-eval`). Cryptography from audited pure-JS libraries vendored at
  pinned versions (for example `@noble/curves` for X25519, Ed25519 and
  secp256k1/BIP-340; `@noble/hashes` for SHA-256 and HMAC/HKDF;
  `@noble/ciphers` for ChaCha20-Poly1305). Randomness only from
  `crypto.getRandomValues`.
- Implements: BinaryProtocol v1/v2 decode and v1 encode, including the padded
  TTL-0 signing preimage; raw-deflate and zlib *decode*
  (`DecompressionStream('deflate-raw')` where present, a bundled inflater
  otherwise). Having no compressor, the client verifies a received compressed
  packet over a preimage built from the **received compressed wire bytes**,
  never by re-deriving them; the bridge channel (`Noise_NK`, below);
  ANNOUNCE TLVs 0x01-0x03 and 0x05; Ed25519 packet signing and
  verification; BitchatMessage payloads; Noise XX with bitchat framing and
  `NoisePayloadType` 0x01-0x03; MESH_GATEWAY_V1 gateway post authoring and,
  for the opt-in gateway role, its full validator.
- Bundle size budget: 250 KiB transferred for the first screen, in at most
  three requests (one HTML, one JS, one CSS), to stay inside the HTTP
  connection limits (see Open questions). Source lives in a new top-level
  `web/` directory; a reproducible
  Gradle task copies the built, content-hashed files into
  `app/src/main/assets/web/` and writes the [manifest](#web-bundle-manifest).

## HTTP routes

`<t>` is the **session token**: 16 bytes from `SecureRandom`, base64url without
padding (22 characters), generated when the hotspot starts and rotated by the
host's "New invite" action. Rotating closes nothing that is already
connected; it stops new page loads and new identities with the old code. The
hub keeps the previous tokens of the last 10 minutes for one purpose only: a
WebSocket on an old token path may *resume* a peer ID bound within those 10
minutes ([Binding and resume](#binding-and-resume)); it can never bind a new
identity. "New invite" does **not** rotate the bridge key
([Bridge channel](#bridge-channel-noise_nk)); the key changes only when the
hotspot restarts or panic runs, so a tab holding an older invite's `#k` can
still resume.

| Method and path | Response | Notes |
| --- | --- | --- |
| `GET /` | Short static page: "Scan the code on the host's screen again." | No token, no app. Replaces today's catch-all landing page |
| `GET /j/<t>` | 308 to `/j/<t>/` | So relative URLs resolve under the token |
| `GET /j/<t>/` | Web client HTML | The invite link. Same page for every user agent. The HTML uses only token-free relative asset URLs (`a/app.<hash>.js`), so its bytes are identical in every session and can be audited |
| `GET /j/<t>/a/<name>.<sha256-prefix>.<ext>` | One allowlisted static asset | `<sha256-prefix>` is 16 lowercase hex characters of the file's SHA-256; immutable name |
| `GET /j/<t>/ws/v1` (Upgrade) | WebSocket bridge | Fails if the browser cap is reached (503 and close) |
| `GET /j/<unknown>`, `GET /j/<unknown>/...` | 404 with the same fixed body as `GET /` | Keeps a guest whose tab reloaded after "New invite" moving |
| `GET /bitchat.apk` | APK, unchanged | Kept for the Android banner. Today's QR codes encode `http://<ip>:<port>` with no path (`HotspotActivity.kt:521`), so they reach `GET /`, not this route |
| `GET /favicon.ico` | 404 | Unchanged |
| anything else, any other method | 404 | Fail closed |

v1 serves no `manifest.txt`: auditors use only the manifest compiled into
their own APK (see [Poisoned code](#poisoned-code)).

**Connection handling.** NanoHTTPD's default runner holds a thread per
connection until its read timeout
(`app/src/main/java/com/bitchat/android/hotspot/ApkWebServer.kt:302-305`). The
hub therefore enforces, before routing: at most 4 concurrent HTTP connections
per client address; a 5 s deadline to receive complete request headers; and a
5 s keep-alive idle timeout. At any cap the hub answers a fixed
`503` with `Connection: close` (or resets the connection if headers never
arrived). WebSocket capacity is counted separately from HTTP capacity.

**Cookies.** The honest hub never sets a cookie, ignores and never logs any
`Cookie` header, and answers a request that carries cookies with a
`Set-Cookie` expiring each received cookie name (`Max-Age=0`, `Path=/`).
`Clear-Site-Data` cannot be relied on in an insecure context.

Every response carries:

```text
Cache-Control: no-store
X-Content-Type-Options: nosniff
Referrer-Policy: no-referrer
Cross-Origin-Opener-Policy: same-origin
Cross-Origin-Resource-Policy: same-origin
Permissions-Policy: camera=(), microphone=(), geolocation=(), bluetooth=()
```

HTML responses also carry:

```text
Content-Security-Policy: default-src 'none'; script-src 'self'; style-src 'self';
  img-src 'self' data: blob:; connect-src 'self' ws://<p2p-ip>:9999 <relay-origins>;
  worker-src 'self'; base-uri 'none'; form-action 'none'; frame-ancestors 'none'
```

`ws://<p2p-ip>:9999` is listed explicitly because older Safari versions do not
match `ws:` under `'self'`. `<relay-origins>` is the fixed list of
`wss://` origins of the default relays
(`app/src/main/java/com/bitchat/android/nostr/NostrRelayManager.kt:42-47`),
needed only by the opt-in browser gateway. Geohash relays are deliberately not
listed (see [Browser as a non-Tor gateway](#browser-as-a-non-tor-gateway)). The client opens no relay socket
before opt-in, and a test asserts it.

The APK route either implements single-range `Range` requests or stops sending
`Accept-Ranges` (today it advertises a feature it does not honour,
`ApkWebServer.kt:83`). Its 500 body becomes a fixed string.

## Invite and join flow

### Why two codes are unavoidable on the hub's own Wi-Fi

A `WIFI:` QR payload carries no URL, and a URL QR cannot join a network.
Captive-portal auto-open would need the hub to answer DNS on port 53 and HTTP on
port 80, which an unprivileged Android app cannot bind. So a person who is not
yet on the hub's network needs two scans. v1 makes the second scan effortless
instead of pretending it away.

### The Invite screen

One screen replaces today's two tabs
(`app/src/main/java/com/bitchat/android/hotspot/HotspotActivity.kt:370-443`):

1. **Step 1, "1 Join Wi-Fi"**: the existing `WIFI:S:<ssid>;T:WPA;P:<password>;;`
   QR, with the network name and password below it as large, easy-to-type
   text for phones whose camera cannot join from a `WIFI:` code.
2. **Step 2, "2 Open chat"**: the invite-link QR for
   `http://<p2p-ip>:9999/j/<t>/#k=<hub bridge key>` (see
   [Bridge channel](#bridge-channel-noise_nk)). The fragment is never sent in
   an HTTP request.

Both codes are shown **at once**, side by side or stacked, with no timer, so
several people can scan different steps at the same moment. Only when the
screen is too small for both at a scannable size does it show one code at a
time, and then the host switches with a tap; it never switches on a timer or on
the client count. (An auto-switch on `group.clientList` changes would hide
Step 1 in a steady stream of joins, and whether legacy, non-P2P stations
appear in `clientList` is unverified; see Open questions.) QR codes use
error-correction level M and a 4-module quiet zone; today the hints are unset
(`QrCodeGenerator.generateQrBitmap`).

The Invite screen also carries the panic gesture (a "Wipe" action and the same
triple-tap as the chat screen), and tells the host once: "People who join by
link trust your phone, and anyone who sees the Wi-Fi code could pretend to be
it. Posts guests share to the internet go out from your phone first."

When the hub and the guests already share another network (for example an
offline venue router), the hub may show only the Step 2 code for that network's
address. That is the true one-scan join. Binding to that interface is a
separate, explicit host choice in v1.1 (see Open questions).

### What each phone does

- **Every phone** opens the web client. No user-agent choice page sits in
  front of it, because a choice costs seconds.
- **Android browsers** additionally see a dismissible banner: "Get the app:
  works without a host. Best from a store or a friend you trust." It offers
  `/bitchat.apk` as one option. An APK from a hub carries the same trust as
  that hub (a first install accepts any signing key), unless its signing
  certificate is checked against the release fingerprint obtained elsewhere
  (app store, F-Droid, a trusted peer's installed app). The user agent is used
  only for this cosmetic decision, read client-side, and never logged or sent
  anywhere.
- **iPhones** see a one-line hint, once: "Keep this page open to stay
  connected." iOS bitchat users who already have the App Store app are told
  they can use it instead.

### Ten-second budget

| Time | Step |
| --- | --- |
| 0-4 s | Camera scans Step 1; the OS joins the Wi-Fi |
| 4-7 s | Camera scans Step 2, already on screen beside Step 1; browser opens |
| 7-8 s | HTML and first-screen assets load (≤ 250 KiB in ≤ 3 requests over a local 2.4 GHz link) |
| 8-9 s | Keys generated (X25519, Ed25519; well under 100 ms on reference phones, to be benchmarked); WebSocket opens; bridge handshake; ANNOUNCE and `BIND` sent; `BOUND` received |
| ≤ 10 s | Chat is visible with recent public messages and the senders' announces (join backfill); the composer is focused; a random nickname such as `guest-4821` is pre-filled and editable later |

No dialog, permission prompt or setting stands between page load and the first
message. Everything optional (nickname, gateway help, app install) is reachable
but never blocking. Clock skew is corrected automatically (see
[Identity model](#identity-model)).

The budget assumes the OS camera joins Wi-Fi from a `WIFI:` code. Some older
stock cameras reportedly do not (platform fact, to be confirmed in the device
matrix); those users type the SSID and password shown under Step 1. That
manual path is timed and recorded separately and is not counted in the
ten-second median.

## Identity model

- On load, the page generates a fresh X25519 static key pair (Noise) and a
  fresh Ed25519 key pair (packet signing) from `crypto.getRandomValues`. Its
  peer ID is `hex(SHA-256(x25519Public)[0..8])`, exactly as
  `NoisePeerIdentity.derivePeerID` computes it.
- Keys, nickname, messages and Noise sessions live **in memory only**. The page
  writes no `localStorage`, `sessionStorage`, IndexedDB or cookies, and never
  reads them for its own use. Reason: every hub serves the same origin
  (`http://192.168.49.1:9999` in the common case), so any stored secret would be
  readable by the next, possibly malicious, hub's script.
- **Scrub on load.** Because an earlier malicious hub may have planted an ID on
  that origin, the honest client, before anything else, clears `localStorage`
  and `sessionStorage`, deletes every IndexedDB database it can enumerate, and
  expires every cookie visible in `document.cookie` (`HttpOnly` cookies are
  expired by the honest hub instead; see [HTTP routes](#http-routes)). Right
  after load it calls `history.replaceState` to drop the token and the `#k`
  fragment from the visible URL; global browser history may still have
  recorded the original URL. Reload means a new identity, and the page shows
  this as a persistent line in private chats ("Leaving or reloading resets
  your name and private chats"), not in a `beforeunload` prompt.
- **ANNOUNCE.** The browser originates a normal signed ANNOUNCE (TLV 0x01
  nickname, 0x02 Noise key, 0x03 signing key; TLV 0x05 omitted or empty, because
  the web client implements none of the assigned capability bits,
  `app/src/main/java/com/bitchat/android/model/PeerCapabilities.kt:32-47`).
  `senderID` is its peer ID, TTL is 7 (`AppConstants.kt:10`), timestamp is the
  corrected clock (below) in milliseconds. It re-announces every 30 s, the
  Android foreground interval (`PowerManager.kt:338-345`), well inside the
  180 s stale-peer timeout (`AppConstants.kt:15`) and above the hub's
  1-per-20 s announce limit.
- **What other peers see.** The hub relays the ANNOUNCE like any other packet.
  Its BLE neighbors receive it with TTL already decremented by the hub, so they
  treat the browser as a peer behind the hub, not as a direct neighbor. That is
  accurate. The first announce is trust-on-first-use, as for every bitchat peer
  (`docs/NOISE_PEER_ID_BINDING.md:51-62`).
- **Signing.** The browser signs every ANNOUNCE, MESSAGE and LEAVE with
  Ed25519 over the BinaryProtocol signing preimage: the packet encoded with
  `signature` absent, TTL 0, and default PKCS#7 padding
  (`BinaryProtocol.kt:116-132`). The browser never sets `IS_COMPRESSED`, so its
  wire payload and preimage payload are the same bytes. Wire padding follows
  the BLE policy (pad only Noise frames).
- **Compression caveat.** Existing signers sign a *compressed* preimage
  whenever `CompressionUtil.shouldCompress` holds (payload ≥ 100 bytes and a
  unique-byte ratio < 0.9; `BinaryProtocol.kt:243-259`,
  `CompressionUtil.kt:21-35`). A verifier that keeps the received wire bytes
  (Android since commit `4b2a9c3`, which added `WirePayload`) verifies the
  browser's uncompressed preimage correctly. A verifier that re-encodes from
  the decoded payload recompresses it and rejects a browser MESSAGE (or a
  long-nickname ANNOUNCE) whose payload qualifies. v1 accepts that limit for
  pre-`4b2a9c3` Android builds and states it in the
  [Compatibility summary](#compatibility-summary); iOS behaviour is an open
  question and a release blocker. The alternative, a JS raw-deflate encoder
  matching `java.util.zip.Deflater` at the default level byte for byte (proven
  by golden vectors) so the browser can follow the originator rule, is
  recorded as an open question, because it would still not match a different
  deflate implementation on another client.
- **Leaving.** On `pagehide` the browser sends a signed LEAVE if the socket is
  still open. The hub cannot sign one for it, so an abrupt loss falls back to
  the normal presence timeout.
- **Clock.** If the browser clock differs from `WELCOME.hubTimeMs` by more than
  60 s, the client applies the hub offset automatically to every outgoing
  timestamp and shows a non-blocking notice ("Using the host's clock"). The
  hub already serves the code, so trusting its clock adds no risk. This keeps
  announces inside the 10-minute window
  (`AnnouncementIdentityValidator.kt:11, 20-22`) and LEAVE inside the 5-minute
  window (`SecurityManager.kt:64-74`) without a blocking choice.
- **Private messages.** Browser-side `Noise_XX_25519_ChaChaPoly_SHA256` with
  bitchat's framing: empty prologue, empty handshake payloads (32/96/64-byte
  messages: message 3 is the encrypted static key, 48 bytes, plus the 16-byte
  tag of the encrypted empty payload; `NoiseSession.kt:33` holds an unused,
  misleading 48; the JS port accepts exactly what the production-configuration
  transcript vector produces), peer-ID binding checked before `split()`, a 4-byte big-endian
  explicit nonce prefix, a 1024-message replay window, empty associated data,
  rekey after 1 hour or 10,000 messages (`NoiseSession.kt:24-41`). Inner
  payloads use `NoisePayloadType` 0x01 (private message), 0x02 (read) and 0x03
  (delivered) (`app/src/main/java/com/bitchat/android/model/NoiseEncrypted.kt:20-29`).
  The web client ignores other inner types. The hub relays `NOISE_HANDSHAKE`
  and `NOISE_ENCRYPTED` packets addressed to the virtual peer and sees only
  ciphertext. Receipts are shaped so incoming traffic can never push the
  browser over its Noise budget: when its outgoing Noise traffic nears the
  budget, the browser first coalesces and then drops read receipts, then
  delivered receipts; user messages and handshakes keep priority.
- **Message length.** Browsers do not fragment, and non-gateway packets are
  capped at 512 bytes. The composer therefore encodes and measures each packet
  before sending and refuses text that would exceed 512 bytes after signature
  (public: 86 bytes of v1 header, sender and signature plus the
  `BitchatMessage` fields) or after Noise overhead and padding (a Noise packet
  over 496 raw bytes pads past 512; `MessagePadding.kt:11-29`,
  `BLEPacketPaddingPolicy.kt:12-16`), showing the remaining length. The exact
  maximum UTF-8 lengths are fixed by boundary vectors generated with the Kotlin
  encoder. App users can send longer text because they fragment; browsers
  cannot, which the composer states.
- **Fingerprint.** The private-chat screen offers the same Noise fingerprint
  comparison as the app, with the honest caveat in
  [UI requirements](#ui-requirements).

### What the hub can and cannot see (honest code)

| The hub can see | The hub cannot see |
| --- | --- |
| Each browser's MAC and IP address, connect and disconnect times | Private message content |
| Each browser's peer ID, nickname and public messages (public to everyone anyway) | Browser private keys |
| Who sends Noise packets to whom, sizes and timing | What a browser did elsewhere, when no colluding hub planted storage on the shared origin |
| The user agent in HTTP headers (must not log or keep it) | |
| **Which bound browser (peer ID, nickname, MAC, IP) authored each gateway post**, with certainty (must not log or keep it) | |

## Bridge protocol (hub ⇄ browser, local only)

One WebSocket per browser at `/j/<t>/ws/v1`. Every WebSocket message is a
**binary** message. Text messages close the socket (code 1003).

### Bridge channel (`Noise_NK`)

The Wi-Fi passphrase is public (it is in the Step 1 code), so WPA2 does not
hide bridge traffic from anyone who saw the code. The bridge therefore runs
inside `Noise_NK_25519_ChaChaPoly_SHA256` with prologue
`"buzzchat/web-bridge/v1"` (22 ASCII bytes, no terminator):

- The hub generates a fresh X25519 **bridge key** whenever the hotspot starts,
  kept in memory only and discarded by panic. Its public half travels in the
  Step 2 URL fragment `#k=<base64url, no padding, 43 characters>`, which is
  never sent in an HTTP request.
- The first two WebSocket messages are the handshake: browser → hub
  `e, es` (48 bytes: 32-byte ephemeral key plus the 16-byte tag of an empty
  payload), hub → browser `e, ee` (48 bytes). Every later WebSocket message is
  one Noise transport message (standard 8-byte counter nonces, no explicit
  nonce, no rekey; a socket lives far below 2^64 messages) whose plaintext is
  exactly one frame below. The 16-byte tag is not counted in the frame limits.
- A wrong-length handshake message, a failed decryption or any frame before the
  handshake completes closes the socket (code 1002).

What this buys: a passive listener who knows the passphrase sees only frame
sizes and timing. It does not see peer IDs, nicknames, flags, signaling
records, or which socket sent a gateway post. What it does not buy: an evil
twin or a malicious hub serves its own code, which ignores the key. Code
integrity over `http://` remains impossible.

```text
+-----------+-------------------+
| frameType | body              |
| UInt8     | frame-specific    |
+-----------+-------------------+
```

| frameType | Name | Direction | Body |
| --- | --- | --- | --- |
| `0x01` | `PACKET` | both | One complete bitchat packet in wire encoding (v1 or v2) |
| `0x02` | `HELLO` | browser → hub, first frame, once | `version` UInt8 = `0x01`, `flags` UInt8 |
| `0x03` | `WELCOME` | hub → browser, once, after `HELLO` | see below |
| `0x04` | `SIGNAL` | both | One [`WebRtcSignal`](#webrtc-signaling-record) record |
| `0x05` | `THROTTLE` | hub → browser | `reason` UInt8, `retryAfter` UInt16 BE seconds |
| `0x06` | `BYE` | both | `reason` UInt8 |
| `0x07` | `FLAGS` | browser → hub | `flags` UInt8 (same bits as `HELLO`) |
| `0x08` | `BIND` | browser → hub, once, after its ANNOUNCE | `peerID` 8 bytes, `signature` 64 bytes (see [Binding and resume](#binding-and-resume)) |
| `0x09` | `BOUND` | hub → browser, once | empty: the socket is bound to the browser's peer ID |

These values are local to the bridge, versioned by the `/ws/v1` path, and are
not mesh `MessageType` values. Unknown frame types, a missing or repeated
`HELLO`, an unknown `HELLO.version`, reserved flag bits set, or a truncated or
over-long body close the socket with code 1002. A future version uses a new
path.

`HELLO` / `FLAGS` bits:

- `0x01 WEBRTC`: the browser will accept `SIGNAL` frames.
- `0x02 GATEWAY_HELPER`: the user has opted in, this session, to publish other
  people's non-Tor gateway posts (see [Browser gateway](#browser-as-a-non-tor-gateway)).
- `0x04`-`0x80`: reserved, must be zero.

`WELCOME`:

```text
+---------+-------+----------------+-----------+--------------+-----------+
| version | flags | maxPacketBytes | hubPeerID | hubTimeMs    | bindNonce |
| UInt8   | UInt8 | UInt16 BE      | 8 bytes   | UInt64 BE    | 32 bytes  |
|  0x01   | 0x00  |                |           | unix millis  | random    |
+---------+-------+----------------+-----------+--------------+-----------+
```

`maxPacketBytes` is the size limit for non-gateway `PACKET` frames from this
browser (512 in v1). `hubTimeMs` is used only for the automatic clock offset.
`bindNonce` is 32 fresh bytes from `SecureRandom` per socket. Per-type rate
budgets are fixed by this spec and ship in the same bundle as the hub, so
`WELCOME` does not carry them.

`THROTTLE.reason`: `0x01` rate, `0x02` size, `0x03` hub busy, `0x04` hub low
battery. `BYE.reason`: `0x00` normal, `0x01` limit exceeded, `0x02` protocol
error, `0x03` identity violation, `0x04` hub stopping, `0x05` announce or bind
rejected (invalid announce, failed proof, clock outside the window), `0x06`
superseded by a newer socket (a takeover for the same peer ID, or a newer
socket from the same client address over its per-address cap).

### Binding and resume

1. After `WELCOME`, the browser sends its ANNOUNCE as a `PACKET`, then `BIND`.
   `BIND.signature` is Ed25519, by the signing key in that ANNOUNCE, over
   `"buzzchat/web-bind/v1"` (20 ASCII bytes, no terminator) ‖ `bindNonce` ‖
   `hubPeerID` ‖ `peerID`.
2. The hub, synchronously and before `processIncoming`, binds only if: the
   ANNOUNCE passes `AnnouncementIdentityValidator.verify` for that `peerID`
   with the TTL rule; it passes the key-replacement checks of
   `SecurityManager.kt:273-296`; and `BIND.signature` verifies. A replayed
   ANNOUNCE cannot produce the signature over a fresh nonce. On success the hub
   sends `BOUND`, then the join backfill, then hands the ANNOUNCE to
   `processIncoming`. On failure it sends `BYE 0x05`.
3. A peer ID that is bound, or was bound within the last 10 minutes, to a
   socket on this hub binds only as a **takeover**: allowed only when the
   signing key equals the one already bound (so it is the same key holder).
   The old socket gets `BYE 0x06` and addressed packets switch to the new
   socket. Any other peer ID the hub already knows (one whose packets it has
   received over BLE or another transport while it was not bound here, within
   the 180 s stale-peer timeout) is refused with `BYE 0x05`. Echoes of a bound
   browser's own relayed packets do not count as "known elsewhere".
4. **Resume.** After a Wi-Fi blip, screen lock or "Reconnect", a tab that still
   holds its keys opens a new socket, completes the handshake, and repeats
   steps 1-3; this is a takeover, so it works while the old socket is still
   waiting for missed pongs. It may use the current token path or any token
   rotated within the last 10 minutes, but on an old token only a takeover of
   a peer ID bound within those 10 minutes succeeds. The hub sends join
   backfill again on resume. No resume secret is needed: the bind proof is the
   credential.
5. Before `BOUND`, the browser sends nothing but its ANNOUNCE and `BIND`, and
   keeps its composer disabled. Any other `PACKET` arriving before binding is
   dropped, not fatal, and counts toward rate limits.

Delivery to a socket never replaces the normal relay path: a packet addressed
to a virtual peer is also relayed exactly as it would be without join by link,
so a socket cannot black-hole traffic for anyone.

### Browser → hub `PACKET` rules

The hub applies, in this order, before `processIncoming`:

1. Size: at most `maxPacketBytes` (512), except `GATEWAY_POST (0x2A)` up to
   the MESH_GATEWAY_V1 text-only maximum (envelope ≤ 4,352 bytes, 4,374 bytes
   with header and sender; `docs/MESH_GATEWAY_V1.md:482-484`), sent
   unfragmented over the socket.
2. Type allowlist: `ANNOUNCE`, `MESSAGE`, `LEAVE`, `NOISE_HANDSHAKE`,
   `NOISE_ENCRYPTED`, and, once MESH_GATEWAY_V1 ships, `GATEWAY_POST` and
   (only from sockets with `GATEWAY_HELPER` set) `GATEWAY_ACK`. Anything else
   (including `FRAGMENT`, `FILE_TRANSFER`, `VOICE_FRAME`, `REQUEST_SYNC`,
   unknown types, or `GATEWAY_ACK` from a non-helper) closes the socket with
   `BYE 0x02`.
3. TTL: exactly 7 for non-gateway types, 4..7 for gateway types; otherwise
   `BYE 0x02`.
4. Identity: before binding, only the ANNOUNCE and `BIND` of
   [Binding and resume](#binding-and-resume) are processed; other packets are
   dropped. After binding, any non-gateway packet with a different `senderID`
   closes the socket with `BYE 0x03`. Gateway types carry random sender IDs
   by design and are charged to the socket's ingress link instead.
5. Flags: on gateway types, `IS_COMPRESSED` and `HAS_SIGNATURE` clear
   (`docs/MESH_GATEWAY_V1.md:273`), else `BYE 0x02`; `HAS_SIGNATURE` required
   on `ANNOUNCE`, `MESSAGE` and `LEAVE`.
6. Rate limits, per socket and hub-wide (see [Hub abuse limits](#hub-abuse-limits)).

Then the packet enters `meshCore.processIncoming` like a BLE neighbor's packet,
and the existing validator, handlers and relay manager decide the rest, subject
to the hub-wide BLE egress budgets. Gateway types go to the MESH_GATEWAY_V1
`GatewayForwarder`, which validates and re-originates (and fragments) them. For
gateway fair queueing and store quotas, **all WEB sockets together count as one
ingress link**, so many sockets cannot take more than one share of the hub's
re-origination budget (`docs/MESH_GATEWAY_V1.md:914-918, 940-941`). The
MESH_GATEWAY_V1 per-link *ceilings* (30 `0x2A` and 12 `0x2B` plus `0x2C` per
minute, `docs/MESH_GATEWAY_V1.md:903-913`), including duplicate and
invalid-set charges, are instead counted **per socket**, where the stricter
per-socket limits below already apply. Counting them on the aggregate link
would let one socket that floods duplicates or invalid posts exhaust the shared
ceiling and lock every other browser's posts out for the rest of the minute.
The hub-wide new-binding limit and socket cap bound how many per-socket buckets
an attacker can hold.

### Hub → browser delivery

The existing relay gate is probabilistic below TTL 4
(`PacketRelayManager.kt:142-171`), and the bridge drops at TTL 0
(`TransportBridgeService.kt:203-206`). Browsers must not depend on that. BLE
packets are validated and relayed by BLE's own `PacketProcessor` and reach
other transports only through `broadcastRoutedPacket`, after the relay decision
(`PacketProcessor.kt:117-166`; `BluetoothMeshService.kt:203-209`), so a hook
inside `WebMeshService`'s `MeshCore` never sees them. v1 therefore changes the
shared pipeline:

- `PacketProcessorDelegate` gains a post-validation callback
  (`onPacketValidated(routed)`, default no-op), invoked in
  `PacketProcessor.handleReceivedPacket` after a valid type handler and before
  `packetRelayManager.handlePacketRelay`, and after reassembly for fragmented
  packets. `BluetoothMeshService` and `MeshCore` wire it to a `WebDeliverySink`
  that `WebMeshService` registers, so packets received on any transport reach
  it deterministically.
- The hub's own originations and relays reach `WebMeshService` through its
  `TransportLayer.send` and `MeshTransport.broadcastPacket`.
- **All paths pass one per-socket seen set** in `WebMeshService`, keyed by
  logical packet id, bounded in size and timed by the injected clock. A packet
  that arrives by the callback, by bridge send, by `dispatchGlobal` and again
  by backfill reaches each socket exactly once. (The bridge's own dedup map is
  global, keyed by kind and packet id, and drops TTL 0,
  `TransportBridgeService.kt:203-226`; it cannot provide this.)

Delivery rules:

- **Addressed packets** whose `recipientID` is a bound virtual peer go to that
  one socket, whatever their TTL, *in addition to* normal relay.
- **Broadcast** `ANNOUNCE`, `MESSAGE` and `LEAVE` go to every bound socket except
  the ingress socket.
- **Gateway types** go only to sockets that need them, never to the ingress
  socket: validated `0x2A` with `REQUIRE_TOR` clear to sockets with
  `GATEWAY_HELPER`; `0x2B` for an event id a browser authored (the hub cannot
  know which, so `0x2B` goes to all browsers, which ignore unknown ids); `0x2C`
  is not forwarded in v1.
- Fragmented packets are delivered **after reassembly**, as one `PACKET`
  frame holding the original packet bytes, so browsers need no fragment
  reassembly. Only allowlisted types are reassembled for delivery.
  `maxPacketBytes` limits only browser → hub frames. Hub → browser `PACKET`
  frames may be larger (an app user's long reassembled `MESSAGE`; a `0x2A` of
  up to 20,758 bytes with a thumbnail, `docs/MESH_GATEWAY_V1.md:473-484`, for
  helper sockets). The hub never sends a `PACKET` frame over 65,536 bytes, and
  the browser closes the socket with 1002 on one that is larger.
- Nothing else is delivered: no `FILE_TRANSFER`, `VOICE_FRAME`, `REQUEST_SYNC`
  or fragments.

`WebMeshService.broadcastPacket` (called from `MeshCore.dispatchGlobal` when the
hub itself relays or originates) uses the same rules, excluding the ingress
socket, so browser-to-browser traffic on one hub needs no BLE round trip.

**Join backfill (MUST).** Rule 10 makes the browser drop anything it cannot
verify against an announce it has seen, and peers re-announce only every
30-300 s (`PowerManager.kt:338-345`). So, on binding and on resume, the hub
sends the latest held ANNOUNCE of **every** currently known peer, then up to
50 recent valid public `MESSAGE` packets, oldest first, from what
`GossipSyncManager` already holds (`GossipSyncManager.kt:44-48`). The source is
a filter-free variant of `handleRequestSync` aimed at the socket
(`GossipSyncManager.kt:177-207`); the current path cannot simply be passed an
empty filter, because it reduces each id modulo the filter's `m`. Backfilled
packets go through the per-socket seen set, and the browser verifies them like
live traffic.

### Network-size accounting

Virtual peers are counted as peers in the hub's UI. BLE's network size comes
from BLE's own `PeerManager` (`BluetoothMeshService.kt:362-364`), which never
processes browser packets, so it is unaffected. Virtual peers *do* enter the
`PeerManager` of `WebMeshService`'s own `MeshCore`, whose packet-processor
`getNetworkSize` (`MeshCore.kt:455-457`) drives relay probability for packets
entering through WEB (`PacketRelayManager.kt:150-168`). `WebMeshService`'s
`MeshCore` must exclude virtual peers from that count; otherwise 32 browsers
would lower the relay probability of low-TTL packets entering through WEB.

Virtual peers are not free on the radio: their packets are rebroadcast over
BLE like anyone else's. The hub-wide egress budgets in
[Hub abuse limits](#hub-abuse-limits) bound that load.

## Browser-to-browser links (WebRTC)

WebRTC is an **optional enhancement**. The bridge alone must give a complete
v1 experience; any WebRTC failure falls back silently to the bridge.

### What it can and cannot do

- On the hub's own Wi-Fi P2P group, every frame between two browsers crosses
  the hub's radio anyway, because the hub is the access point. DataChannels
  then save only hub CPU and threads. They do **not** survive the hub leaving,
  because the network leaves with it.
- On a network hosted by something else (an offline venue router, a separate
  tethering hotspot), DataChannels keep browsers connected after the hub app
  stops. New people still cannot join without some hub, because nothing else
  serves the page.

### Feasibility unknowns (must be measured, not assumed)

- **mDNS host candidates.** Chrome and Safari replace host IPs with random
  `.local` names unless the page has media permission, which an insecure page
  cannot get. Resolving those names needs multicast DNS between clients.
  Whether Android P2P groups forward multicast between clients is unknown.
- **Client-to-client forwarding.** Whether the Android P2P group owner forwards
  unicast between clients at all (no client isolation) is unknown and may vary
  by chipset.
- **Local Network Access.** Chrome's Local Network Access / Private Network
  Access rules may prompt or block connections to private addresses. Pages
  served from a private address talking to private addresses should be
  exempt, but this must be tested per browser version.
- **No STUN or TURN.** `iceServers` is always empty. Contacting any external
  STUN server would leak addresses and needs internet anyway. Only host
  candidates are possible.

### WebRTC signaling record

Browsers exchange offers, answers and ICE candidates in `SIGNAL` frames. The
hub routes a record to the socket bound to `to`, after checking that `from`
equals the sending socket's bound peer ID (otherwise `BYE 0x03`). The hub does
not need to verify the signature; receivers must.

```text
+---------+------+---------+---------+-------------+------------+------------+-----------+
| version | kind | from    | to      | timestampMs | bodyLength | body       | signature |
| UInt8   | UInt8| 8 bytes | 8 bytes | UInt64 BE   | UInt16 BE  | bodyLength | 64 bytes  |
|  0x01   |      | peer ID | peer ID | unix millis | ≤ 8192     | UTF-8      | Ed25519   |
+---------+------+---------+---------+-------------+------------+------------+-----------+
```

- `kind`: `0x01` offer (body = SDP), `0x02` answer (body = SDP), `0x03` ICE
  candidate (body = one `candidate:` line), `0x04` bye (body empty). Others are
  invalid.
- `signature`: Ed25519 by the `from` peer's announced signing key over
  `"buzzchat/webrtc-signal/v1"` (25 ASCII bytes, no terminator) followed by
  every preceding field of the record.
- A receiver accepts a record only if: it parses exactly (no trailing bytes);
  `to` is its own peer ID; `from` is a peer whose ANNOUNCE it has verified; the
  signature verifies against that peer's signing key; `timestampMs` is within
  ±600 s of its clock; and the SHA-256 of the complete signed record has not
  been seen within that window. (Trickle ICE often emits several `0x03`
  records in the same millisecond, so `(from, timestampMs, kind)` is not a
  usable replay key; distinct candidates differ in body and therefore in hash.)
- Size: a `SIGNAL` frame is at most 1 + 92 + 8,192 bytes (frame type, the
  record's 28-byte header and 64-byte signature, and the body). Offers and
  answers are commonly 1-4 KB, far above the 512-byte `PACKET` limit, which
  does not apply to `SIGNAL`.
- Because the SDP carries the DTLS fingerprint, the signature binds the
  DataChannel to the announced identity. A relay or another browser cannot MITM
  the channel. (A malicious hub's *code* in both browsers still can; see
  [Poisoned code](#poisoned-code).)
- ICE candidates of type `srflx` or `relay` are rejected.

### Introductions and mesh behaviour

- A browser offers DataChannels to at most 6 other browsers that set
  `WEBRTC`, chosen at random, and accepts at most 8. Larger groups stay on
  the bridge.
- An existing DataChannel neighbor may carry `SIGNAL` records for a
  newcomer it is linked to, so links can form when the hub is gone (on a
  network that survives). The same signature rules apply.
- Over a DataChannel, browsers exchange the same `PACKET` frames (only
  `PACKET` and `SIGNAL` are valid there). Each browser relays allowlisted
  packets to its other DataChannel neighbors with TTL decremented, dropping at
  TTL 0, deduplicating by logical packet hash for 5 minutes, verifying
  signatures before relaying, and applying the same per-link limits as the hub.
- While its bridge socket is up, a browser never forwards DataChannel traffic
  to the hub; the hub already has it.

## Browser as a gateway-post author

A browser user can compose a "Share to internet" post as specified in
MESH_GATEWAY_V1 ([Author lifecycle](MESH_GATEWAY_V1.md#author-lifecycle)): a
fresh secp256k1 key per post, NIP-01 canonical encoding, and PoW. Mining runs
in a dedicated Web Worker loaded from a same-origin script (dedicated workers
do not need a secure context, unlike Service Workers), which is why the CSP
lists `worker-src 'self'`. The envelope is sent as one `PACKET` frame.
As in the app, "Share to internet" is **off** by default for every post and is
turned on per post by the author (consent before reach); with it off, nothing
is mined, signed or sent as `0x2A`. The outer packet follows MESH_GATEWAY_V1
[Outer packet](MESH_GATEWAY_V1.md#outer-packet) exactly: a fresh 8-byte random
`senderID` from `crypto.getRandomValues` (never the browser's peer ID), origin
TTL drawn uniformly from `4..7`, a jittered timestamp, `HAS_SIGNATURE` and
`IS_COMPRESSED` clear (`docs/MESH_GATEWAY_V1.md:263-278`). The per-post
secp256k1 key lives in a `Uint8Array` that is zeroed right after the envelope
is signed, also on cancel or error, mirroring
`docs/MESH_GATEWAY_V1.md:656-663` (JavaScript cannot guarantee that no copy
remains in engine memory; residual). If the author cancels before signing
completes, nothing is signed.
Differences from the app:

- **Text only, short-lived.** Browser posts carry no thumbnail, have a
  lifetime of at most 24 h, and use the 16-bit PoW target, so the event stays
  within the text-only size (`docs/MESH_GATEWAY_V1.md:446-447, 461-464,
  482-484`). Without `crypto.subtle` or WASM, each attempt is a pure-JS SHA-256
  of the whole event (about 65,536 × up to 4 KiB for 16 bits), which may take
  tens of seconds on a low-end phone. The composer shows progress, a battery
  note and a cancel button; a benchmark with a pass threshold on the oldest
  matrix row is required.
- **Sent once.** After mining, the post waits the MESH_GATEWAY_V1 uniform
  random 5-60 s delay, then goes out once over the bridge. Retries over the
  bridge are useless: the hub's `GatewayForwarder` records the event id on
  first receipt, re-originates it at most once in its lifetime, and treats
  every later copy as a duplicate charged to the link
  (`docs/MESH_GATEWAY_V1.md:723-730, 749-753, 903-907`). After the hub accepts
  it, persistence depends on the hub's relay store, re-origination and gossip
  sync. The browser may retry only over a different path (a DataChannel
  neighbor). The UI says plainly: "Closing this page before it is sent cancels
  the post." The browser keeps no persistent outbox. This is a stated
  deviation from MESH_GATEWAY_V1's author lifecycle, which retries up to
  12 times from an encrypted, persisted outbox
  (`docs/MESH_GATEWAY_V1.md:668-686`): a browser has neither persistent
  storage it may use nor a background, and bridge retries would only be
  charged as duplicates.
- **Author linkage.** The composer warns before sending: "The phone you joined
  through will know this post came from you." (See
  [Residual risks](#residual-risks-stated-honestly).) Sending gateway posts on
  a separate, unbound socket would not remove the MAC and IP linkage; it is an
  open question.
- **Host hold.** Before re-originating a post that arrived from a WEB socket,
  the hub holds it for an extra uniform random 5-60 s and always decrements
  the TTL (never the `TTL = T` option), mixing it with other traffic. This
  weakens, but does not remove, the first-hop suspicion that falls on the host.
- **Never self-published.** The client keeps an in-memory set of the event ids
  it authored this session. In the helper role it never publishes, acks or
  `REQ`-checks those ids, mirroring `docs/MESH_GATEWAY_V1.md:602-604`; the hub
  also never sends a `0x2A` back to its ingress socket.
- `REQUIRE_TOR` is on and locked by default, as in the app. The browser never
  publishes its own post, so this only governs which gateways may publish it.

## Browser as a non-Tor gateway

A browser whose phone also has an internet path may help publish **other
people's** posts. This is the only case where the page connects outside the
hub.

- **Opt-in per session.** Off by default. Turning it on shows: "Your phone's
  internet address will be linked to other people's posts by the relays you
  send to. Your mobile carrier will see that this phone connects to Nostr
  relays, and the relays will see your phone's address and browser type. Only
  posts whose authors allowed sending without Tor will be sent." The user must
  confirm. It turns off when the tab closes.
- **Scope.** Only posts with `REQUIRE_TOR` clear. A browser never publishes a
  `REQUIRE_TOR` post, and never sets `VIA_TOR` in an ack. This is the browser
  equivalent of MESH_GATEWAY_V1's "Allow clearnet gateway publishing"
  ([Readiness and Tor semantics](MESH_GATEWAY_V1.md#readiness-and-tor-semantics)),
  and the hub forwards only such posts to helpers.
- **Validation.** The browser runs all six MESH_GATEWAY_V1 fail-closed checks
  itself, in order, with its own canonical encoder and strict parser
  ([Fail-closed rules](MESH_GATEWAY_V1.md#fail-closed-rules)). It does not
  trust the hub's validation.
- **Relays.** Only the fixed default relay list over `wss://`, one dedicated
  socket per relay, opened only after opt-in. This is an explicit deviation
  from MESH_GATEWAY_V1, which adds up to 3 geohash relays for events with a `g`
  tag (`docs/MESH_GATEWAY_V1.md:1041-1047`): a browser gateway publishes only
  to the default relays, has no relay directory lookup, and its CSP lists
  nothing else. Other MESH_GATEWAY_V1 publish rules apply: no subscriptions
  other than the per-event `REQ` presence check, and the publish-once, backoff
  and budget rules
  ([Publish-once and backoff](MESH_GATEWAY_V1.md#publish-once-and-backoff)).
- **Own posts skipped.** Event ids this tab authored are never published,
  acked or checked (see [Never self-published](#browser-as-a-gateway-post-author)).
- **Acks.** If `WANT_ACK` is set and a relay accepted, the browser may send one
  `GATEWAY_ACK` with `VIA_TOR` clear through the bridge, built exactly as
  MESH_GATEWAY_V1 specifies (39 bytes, `reportedAt` rounded down to
  10 minutes, fresh random outer `senderID`, origin TTL uniform in `4..7`, no
  signature; `docs/MESH_GATEWAY_V1.md:540-558`).
- **Honest limits.** Whether a phone on a no-internet Wi-Fi also routes
  `wss://` traffic over cellular depends on the OS and settings; the page only
  reports "Internet reachable" after a relay socket actually opens.
- **Logging.** The no-log rules of
  [What gateways must not log or keep](MESH_GATEWAY_V1.md#what-gateways-must-not-log-or-keep)
  apply to the web client: no `console` output of ids, keys, content or relays
  in release bundles.

## Poisoned code

A hub serves the code that holds the browser user's keys. Because the Wi-Fi
passphrase is in the Step 1 code, anyone who saw it can run an evil twin with
the same SSID, passphrase and address and become "the hub" for whoever joins
it. Every user of join by link is exposed to this, not only visitors of a
malicious host. This section lists every mitigation considered and what each
actually buys. None of them achieves code integrity over `http://`.

| Mitigation | Status in v1 | What it protects | What it does not |
| --- | --- | --- | --- |
| Reproducible bundle inside the signed, reproducible APK | **Required** | A hub running the official APK serves the official bundle byte for byte | A modified APK; an evil twin |
| Per-session URL path and content-addressed assets, `no-store` | **Required** | Cache poisoning by an earlier hub at the same origin | The current hub |
| Scrub storage on load; write none; hub expires cookies | **Required** | IDs planted by an earlier hub, when the current hub is honest | The current hub; a later malicious hub that reads planted storage before any scrub |
| Encrypted bridge (`Noise_NK` to the hub's bridge key) | **Required** | Passive listeners who know the passphrase | Code integrity; an evil twin or malicious hub |
| **Hub audit by installed peers** | **Required** | Any app user on the hub's Wi-Fi can tap "Check this host". The app loads `/j/<t>/` in an Android WebView, so request headers, order, parallelism and the favicon request match Chrome, opens the WebSocket and completes the handshake, ANNOUNCE and `BIND` like a real guest, and hashes every response body against the manifest compiled into its own APK. It never requests a manifest from the hub. Green on an exact match with the current bundle or one in the APK's shipped list of known-good digests of past releases; red for a digest on the APK's revoked list; "Can't check: unknown version" otherwise. It also hashes `/bitchat.apk` against its own release. A failure is shown prominently and can be told to others out loud | A hub that tells auditors from browsers by other means (address, timing, a known app user's MAC) and targets only some visitors; an evil twin the auditor is not connected to. How to capture response bytes from a WebView without changing its requests is an open question |
| Short bundle code compared out loud | Defined; low value alone | When shown by an **installed app's** audit screen, people can compare "5064-6010-6079" between auditors | Shown by the page itself it proves nothing: tampered code shows the right code. The page therefore never displays its own bundle code |
| Subresource Integrity | Not used | Nothing here: hashes arrive from the same hub | Everything |
| Minimal signed loader verifying a release-key signature | Future | Only if the loader itself is pinned (below) | A loader served by the hub can be swapped like any script |
| Pinned copy: HTTPS mirror with Service Worker | Future, open question | A copy obtained while online, verified by TLS, cached by a Service Worker; secure context unlocks `crypto.subtle` and camera | It cannot open `ws://` to the hub (mixed content) and Local Network Access may block it; needs `wss://` to the hub, which needs a trusted certificate |
| Pinned copy: saved single-file HTML (`file://`) | Future, open question | User-held code | Saving from the hub copies the hub's code; mobile browsers handle `file://` poorly; origin `null` must be allowed by the hub's Origin check |
| Name-constrained local CA | Last resort, not v1 | `https://` to the hub, secure context | Installing a CA takes minutes, iOS needs a profile plus a trust toggle, and a leaked CA key is dangerous; fails the ten-second rule |
| **Install the app** | Always offered | From an app store, F-Droid or a trusted peer, or from a hub with its signing certificate checked against the release fingerprint obtained elsewhere: no dependence on any hub's code | An APK downloaded from a hub and installed unchecked carries exactly the hub's trust (a first install accepts any signing key). Takes longer than ten seconds; iPhones need the App Store |

## Hub abuse limits

Limits are receiver policy, enforced before `processIncoming`: per socket
(each socket is one ingress link for chat traffic, like a BLE connection) and
hub-wide. Values are v1 defaults; hub-wide values are placeholders to be sized
from measured BLE throughput and group-owner capacity (see Open questions).

**Per socket**

| Limit | Value | Reason |
| --- | --- | --- |
| Frame size, `PACKET` | 512 bytes; `0x2A` up to the text-only maximum (4,374 bytes) | No fragmentation from browsers |
| Frame size, `SIGNAL` | 1 + 92 + 8,192 bytes | SDP offers and answers |
| Frame size, `HELLO` / `FLAGS` / `BYE` / `BIND` / `BOUND` | 3 / 2 / 2 / 73 / 1 bytes exactly | Fixed formats |
| `ANNOUNCE` | 1 per 20 s after the first | 30 s re-announce cadence plus margin |
| `MESSAGE` (broadcast) | 1 per second, burst 5 | Human typing speed |
| `NOISE_HANDSHAKE` + `NOISE_ENCRYPTED` | 30 per minute | Private chat; the browser shapes receipts to stay under it |
| `GATEWAY_POST` | 2 per minute | A realistic author rate, far below MESH_GATEWAY_V1's 30-per-link ceiling (counted per socket; the aggregate WEB link gets one fair-queue share) |
| `GATEWAY_ACK` (helper sockets only) | 12 per minute | MESH_GATEWAY_V1 per-link ceiling for `0x2B` plus `0x2C` (`docs/MESH_GATEWAY_V1.md:911-913`) |
| `SIGNAL` | 30 per minute | ICE trickle |
| Outbound queue | 256 KiB; oldest broadcasts dropped first, addressed packets kept | A stalled browser cannot grow hub memory |
| Unbound socket lifetime | 15 s to complete the handshake, `HELLO`, ANNOUNCE and `BIND` | Slow-loris protection |
| Idle socket | WebSocket ping every 20 s; closed after 2 missed pongs | Backgrounded tabs |

**Per client address** (in memory for the window only, never logged; a guest
with the passphrase can use many addresses, so these are courtesy limits)

| Limit | Value | Reason |
| --- | --- | --- |
| Concurrent HTTP connections | 4; 5 s header deadline; 5 s keep-alive idle | Browsers open several parallel connections; NanoHTTPD holds a thread per connection |
| HTTP requests | 120 per minute | Static assets only |
| Bridge sockets | 2; a third evicts the oldest (`BYE 0x06`) instead of being refused | A reload or two while old sockets time out |

**Hub-wide**

| Limit | Value | Reason |
| --- | --- | --- |
| Concurrent bridge sockets | 32 (16 in the LOW band), or the measured group-owner client capacity if lower | Thread per connection; memory; Wi-Fi client capacity |
| Concurrent HTTP connections (non-WebSocket) | 16, separate from sockets | Thread per connection |
| New bindings | Token bucket across all sockets: burst 16, refilled at 6 per minute; takeovers and resumes of a peer ID bound within the last 10 minutes are not charged. After a socket from an address gets `BYE`, that address waits 10 s, doubling per further `BYE` up to 5 min | Identity churn grows peer tables on every node; the burst lets a group scan the code together (test 3.7) |
| BLE egress of browser-originated traffic | Per type: broadcast `MESSAGE` 4 per second, `ANNOUNCE` 1 per second, `NOISE_*` 2 per second, shared fairly across sockets by deficit round robin | 32 sockets at their per-socket limits would far exceed BLE airtime. Over-budget broadcasts still reach other browsers but are dropped for BLE |
| Held announces | A new virtual peer's ANNOUNCE is held back from BLE until just before its first `MESSAGE` or `NOISE_*` packet, or 30 s after binding | Throwaway identities do not reach every node's peer table |

When the socket cap is reached, the hub evicts the newest bound socket that has
sent no user message (`BYE 0x01`) to admit a newcomer; only when every socket
has sent a message does it answer 503.

Exceeding a user-originated rate sends `THROTTLE`; a sustained flood (3
throttles in a minute on `MESSAGE`, `ANNOUNCE`, `GATEWAY_POST` or `SIGNAL`), or
any identity or protocol violation, sends `BYE` and closes the socket. Excess
`NOISE_*` packets are queued briefly or dropped, never counted toward a `BYE`,
because a remote peer controls how many receipts a browser owes. The existing
relay manager does **not** bound browser traffic (it relays every packet with
TTL ≥ 4 and has no rate limit, `PacketRelayManager.kt:40-114, 142-146`); the
hub-wide egress budgets do. Many short-lived browser identities grow the per-sender actor map in
`PacketProcessor` (`app/src/main/java/com/bitchat/android/mesh/PacketProcessor.kt:46-75`);
idle-actor eviction, already required by MESH_GATEWAY_V1, is a prerequisite
for enabling join by link.

## Power

- **Hub.** Serving browsers keeps Wi-Fi P2P, a WifiLock, a renewed partial wake
  lock and the foreground service up. The browser cap halves in the LOW band.
  In the CRITICAL band and not charging, the hub stops accepting new browsers,
  sends `THROTTLE 0x04` to connected ones, and shows the host a choice to keep
  sharing or stop. The hub screen shows "Sharing uses extra battery" with the
  connected count.
- **Browser.** The client does no work while hidden beyond keeping the socket
  open; it runs no timers faster than 20 s and mines PoW only on request.

## Privacy

- **No analytics, no external resources, no fonts or images from the
  internet.** CSP enforces this for honest code.
- **Hub logging.** The hub must not log, at any level, client IP addresses, MAC
  addresses, user agents, session tokens, virtual peer IDs, nicknames, message
  content or packet sizes. Today `ApkWebServer.kt:43` logs every client IP and
  must be removed. The SSID and the URL are logged by `HotspotManager` and
  `QrCodeGenerator` today; they must become debug-only and be stripped from
  release builds with the R8 rule MESH_GATEWAY_V1 already requires. That rule
  strips only `Log.v`, `Log.d` and `Log.i` (`docs/MESH_GATEWAY_V1.md:1107-1110`),
  but every browser packet runs through the shared pipeline, whose
  `SecurityManager` and `MessageHandler` log peer IDs at `Log.w` and `Log.e`
  (`SecurityManager.kt:270, 278, 287, 294, 302, 313, 335`;
  `MessageHandler.kt:72, 205, 222, 400`), and `PacketRelayManager` logs every
  sender (`PacketRelayManager.kt:44`). Before join by link is enabled, warn and
  error logs in `SecurityManager`, `MessageHandler`, `PacketProcessor` and
  `PacketRelayManager` must not contain peer IDs (remove or redact them), or
  release builds must strip every `Log` level. A `lintDebug`-enforced rule
  forbids `Log` calls in `com.bitchat.android.web` and in `ApkWebServer`, and
  forbids peer-ID interpolation in those mesh classes.
- **Hub memory.** Per-address rate counters and socket maps hold addresses only
  as long as a window or a connection, are never persisted, and are cleared by
  panic.
- **Browser.** No persistent storage, and storage scrubbed on load; nothing in
  the URL beyond the session token and the bridge key fragment, both removed
  from the visible URL by `history.replaceState` after load;
  `Referrer-Policy: no-referrer`. The token cannot be used after the hotspot
  session, but a copy in global browser history is unique to one session and
  can match a photo of the hub screen or a seized hub, so it can serve as
  evidence of attendance (residual).
- **Wipe in the browser.** The web client has a one-tap "Wipe" control and a
  triple-tap on the header, like the app. Wipe sends LEAVE if possible, closes
  the sockets and DataChannels, zeroes key buffers, clears in-memory state and
  the DOM, and navigates to `GET /` with `location.replace`, with no
  `beforeunload` prompt. The page never registers a `beforeunload` prompt at
  all. App-switcher snapshots taken before the wipe are outside the page's
  control (residual).
- **Location.** The web client never requests geolocation
  (`Permissions-Policy` denies it) and offers no geohash features.
- **Panic on the hub**, in order: gates origination; closes every socket with
  `BYE 0x04`; stops the server; removes the Wi-Fi P2P group
  (`HotspotManager.stopHotspot`), so the `DIRECT-BC-` network disappears;
  releases the WifiLock and wake lock; clears the `hotspot` preferences
  (including `owned_group_name`, `HotspotManager.kt:43-44, 96-99`) and the
  in-memory SSID, passphrase, token and bridge key; clears all bridge state;
  dismisses the Invite screen; then the existing wipe continues
  (`ChatViewModel.kt:1454-1533`, which today does not touch the hotspot). Panic
  is reachable from the chat screen, the Invite screen and the foreground
  notification.
- **Synthetic data only** in tests, vectors and screenshots, per `AGENTS.md`.

## UI requirements

Every item below must pass the ten-second test in
`docs/BUZZCHAT_PRINCIPLES.md:15-17`.

- **Hub Invite screen:** both codes at once as described, with the SSID and
  password as text, a live "N people joined by link" count (from bound
  sockets), "New invite" (rotates the token), "Stop sharing", "Wipe", "Host
  full" when at capacity, and the battery note. The host is told once the
  message in [The Invite screen](#the-invite-screen) plus "Keep BuzzChat up to
  date."
- **Hub peer list:** browser peers carry a small "via link" label and are never
  shown as verified or direct BLE neighbors.
- **Web client first screen:** the familiar chat layout, recent messages, a
  focused composer, the random nickname. A thin status line: "Connected through
  a nearby phone".
- **Web client, first private chat:** one line, once: "Private messages here
  are only as safe as the phone that gave you this page. The app is safer."
- **Web client, Wipe:** a "Wipe" control always visible in the header, plus
  the header triple-tap (see [Privacy](#privacy)).
- **Connection loss:** "Lost the host. You can't send right now." with
  "Reconnect", which resumes the same identity if the tab survived; if
  DataChannels remain: "Host left. Still connected to N people nearby." Never
  a spinner that implies messages are going out. The first-screen hint says
  "Keep the screen on: if it locks, you will need to tap Reconnect."
- **Reset notice:** a persistent line in private chats, "Leaving or reloading
  resets your name and private chats." No `beforeunload` prompt: browsers do
  not show custom text there and mobile Safari often shows none
  (browser-platform fact), and it would slow an escape.
- **Share to internet composer:** "The phone you joined through will know this
  post came from you", "Closing this page before it is sent cancels the post",
  PoW progress with cancel, and the battery note.
- **Message length:** a remaining-length counter in the composer near the
  limit.
- **Gateway-post status:** "Share to internet" off by default on every post;
  after sending, the status reads at most "Sent to the host" and, when a
  `0x2B` for that event id arrives, "Reported reached the internet
  (unverified)", never "delivered" or "published" (honest limits,
  `docs/BUZZCHAT_PRINCIPLES.md:37-38`). A browser never shows "Verified on
  relay": v1 does not implement that optional check in the web client.
- **Gateway help toggle** in a secondary menu, never on the first screen.
- **Check this host** (app only): one button on the peer list when the app is on
  a hub's Wi-Fi; result green / red / can't check, with the short code.
- **Accessibility:** works one-handed at 320 px width, large tap targets,
  readable in bright light, no motion required.
- Visible changes need before/after screenshots made with synthetic data.

## Type assignments

Join by link proposes **no new mesh type values**: no `MessageType`, no
`NoisePayloadType`, no announce TLV, no capability bit and no `REQUEST_SYNC`
TLV. Browsers emit only existing types (`ANNOUNCE`, `MESSAGE`, `LEAVE`,
`NOISE_HANDSHAKE`, `NOISE_ENCRYPTED`) and, once MESH_GATEWAY_V1 ships, its
PROPOSED `0x2A` `GATEWAY_POST` and `0x2B` `GATEWAY_ACK`
([Type assignments](MESH_GATEWAY_V1.md#type-assignments-proposed)). Browsers
never emit `0x2C`. Gateway support in browsers therefore inherits the
MESH_GATEWAY_V1 release blocker on confirming those values with iOS.

The only new code points are local to one hub and never reach BLE or Wi-Fi
Aware: bridge `frameType` `0x01`-`0x09`, `HELLO` / `FLAGS` bits `0x01`-`0x02`,
`THROTTLE` and `BYE` reasons, and `WebRtcSignal.kind` `0x01`-`0x04`. They are
versioned by the `/ws/v1` path and the domain tags below.

## Versioning

- Mesh wire: unchanged. Browsers emit version-1 packets.
- Bridge: the path `/ws/v1` and `HELLO.version = 0x01`. A breaking change uses
  `/ws/v2`; a hub may serve both. Additive changes use reserved `HELLO` flag
  bits only after a version bump, never by reinterpreting reserved bits in v1.
- `WebRtcSignal.version = 0x01` and the domain tag
  `buzzchat/webrtc-signal/v1`. Unknown versions are dropped.
- Bridge channel prologue `buzzchat/web-bridge/v1` and bind domain tag
  `buzzchat/web-bind/v1`; both change with the bridge version.
- The web bundle and the hub app ship together in one APK, so hub and client
  versions always match. A browser never talks to a hub of a different version,
  because it always loads its code from the hub it talks to.
- Manifest format: `web-bundle-manifest-v1`, named in the first line (below).

## Web bundle manifest

```text
web-bundle-manifest-v1\n
<sha256 hex>  <path>\n      (two spaces; one line per file; sorted by path bytes)
```

`<path>` is the URL suffix after `/j/<t>/`, with one exception: the client HTML,
served at `/j/<t>/` itself, has the path `index.html`. Every other file is
`a/<name>.<16 hex>.<ext>`, where the 16 lowercase hex characters are the start
of that file's SHA-256. The HTML references assets only by these token-free
relative paths, so its bytes do not depend on the session token.

`bundleDigest = SHA-256(all manifest bytes after the first line)`.
`bundleCode` = the first 5 bytes of `bundleDigest` as a big-endian integer,
modulo 10^12, printed as 12 decimal digits in three groups of four. The digest
is compiled into the app (`BuildConfig`) by the reproducible build, so each
APK knows its own expected bundle. Each APK also ships the full manifests of
known-good past releases and a list of revoked bundle digests, for the audit.
The manifest is not served by the hub.

## Compatibility summary

- **iOS bitchat and existing Android bitchat/BuzzChat (non-hub):** no change.
  They see ordinary peers with valid signed announces and messages relayed by
  the hub, and can open Noise sessions with them. **Minimum verifier:** Android
  builds that include commit `4b2a9c3` (`WirePayload`, 2026-07-31) verify every
  browser packet. Older Android builds recompress a decoded payload before
  verifying, so they reject browser MESSAGE and ANNOUNCE packets whose payload
  meets `shouldCompress` (≥ 100 bytes, unique-byte ratio < 0.9); shorter or
  high-entropy payloads verify. Unverified: how iOS builds its verification
  preimage for a received uncompressed payload, whether iOS has peer-count or
  per-relay limits that many peers behind one hub would trip, and whether iOS
  enforces the identical binding and signature rules (the iOS source is not in
  this repository).
- **Peers that try to send files, voice or private media to a browser:** the hub
  does not forward `FILE_TRANSFER` or `VOICE_FRAME` to browsers, and the browser
  ignores inner Noise types other than 0x01-0x03. The browser sends no
  authenticated peer-state proof (`0x21`), so a new Android sender treats it as
  an unpinned legacy client and, after its five-second watchdog, offers the
  legacy raw-file consent prompt (`docs/PRIVATE_MEDIA_V1.md`). That file is then
  silently not delivered. See Open questions.
- **Gateway posts:** to other nodes, browser-authored `0x2A` posts are
  indistinguishable from app-authored ones (text-only, ≤ 24 h). The hub is
  not fooled: it knows which browser authored each, and RF observers see the
  host's device transmit them first (see
  [Residual risks](#residual-risks-stated-honestly)). All MESH_GATEWAY_V1
  compatibility notes apply, including its type-assignment release blocker.
  Browser gateways publish only to the default relays (a stated deviation).
- **Wear OS:** sees browser peers as ordinary relayed peers. The shared
  pipeline changes compile into Wear as no-ops.
- **Older BuzzChat hubs:** keep serving only the APK and landing page; old
  `http://<ip>:9999` QR codes reach `GET /`, which now explains to rescan.

## Test plan

Types: **U** = JVM unit (JUnit 4 / Robolectric, injected clock, randomness and
transport); **W** = web-client unit tests in a headless JS runner, loading
`app/src/test/resources/contracts/web-join-v1.json` with a fake clock and seeded
RNG; **S** = JVM simulation with in-memory WebSocket fakes and the in-memory
mesh harness MESH_GATEWAY_V1 requires; **I** = interop: the bundled JS client
in the headless runner against an in-process Kotlin hub (`ApkWebServer` and
`WebMeshService` on loopback, injected clock and RNG) and a Kotlin `MeshCore`
peer; **M** = Mesh Lab on physical devices (`docs/release-gate-runbook.md`);
**X** = manual device matrix. Tests are named by observable behavior, follow
`docs/testing-conventions.md` (no sleeps; injectable time, randomness,
dispatchers and transports, `docs/testing-conventions.md:21-27`), use no public
relays and print `TEST_SEED` on randomized failure.

**JS toolchain and CI.** The repository has no JS toolchain and CI runs only
`testDebugUnitTest lintDebug` (`AGENTS.md`). W and I tests need a named runner
and a pinned, checksum-verified toolchain, vendored under
`docs/reproducible-builds.md`, invoked by a Gradle task (for example
`:app:webClientTest`) that `testDebugUnitTest` depends on, so CI runs them.
The runner choice is an open question. Until it exists, every safety-critical
W row also has a Kotlin counterpart where one is possible.

Required runs: `./gradlew :app:assembleDebug :wear:assembleDebug
testDebugUnitTest lintDebug`, which covers the Wear build and Wear unit tests
of the shared pipeline changes.

### Implementation seams

- `ApkWebServer` takes an injected asset source, token provider, clock, bind
  address and logger so routes and headers are testable without a device.
- `WebMeshService` takes an injected socket abstraction, a `Clock`, a
  `CoroutineDispatcher`, a `Random` and a packet-sink interface instead of a
  concrete `MeshCore`, so S tests can attach hundreds of fake browsers and
  test 1.6 can assert that a packet never reaches `processIncoming`. Its
  time-based rules (rates, 15 s unbound limit, pings, throttles, takeovers,
  backoff) all read the injected clock.
- `TransportBridgeService` (a process-global `object` timed by
  `System.currentTimeMillis`, `TransportBridgeService.kt:20, 210`) gains an
  instance form, or a reset hook and an injected clock, for tests.
- `PacketProcessor` takes a dispatcher (today `Dispatchers.IO`,
  `PacketProcessor.kt:38`) and exposes an observable actor count (test 3.5);
  `SecurityManager` takes a clock (today `System.currentTimeMillis`,
  `SecurityManager.kt:58`); `PacketRelayManager` and `FragmentManager` reuse
  the seams already required by `docs/MESH_GATEWAY_V1.md:1247-1268`.
  `MeshCore` needs a `Context` and builds `SecureAuthenticatedPeerStateStore`
  itself (`MeshCore.kt:33-36, 60`), so S tests run under Robolectric or the
  store is injected.
- Tests 1.6, 3.2, 3.5, 3.6 and all takeover and backoff tests depend on these
  seams.
- The web client takes injected `WebSocket`, clock, RNG and `RTCPeerConnection`
  factories.

### 1. Safety (must never fail)

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 1.1 | Group drops while browsers connected | Server stops, sockets closed with `BYE 0x04`, port closed | U, M |
| 1.2 | Request to any interface other than the P2P address | NanoHTTPD hostname equals the injected P2P address; connection refused on other interfaces | U, M |
| 1.2a | No real P2P address (`ConnectionInfo` fallback) | Server not started; no Step 2 code; retry state shown | U |
| 1.3 | Unknown path, wrong token, rotated token, `POST /j/<t>/` | 404; `/j/<unknown>` paths carry the fixed "scan again" body | U |
| 1.3a | Client HTML across two sessions with different tokens | Byte-identical; every asset URL is token-free and relative; `/j/<t>` answers 308 to `/j/<t>/` | U |
| 1.4 | `Host` mismatch; WebSocket `Origin` mismatch | 421; upgrade refused | U |
| 1.5 | Every response | Carries the required headers; HTML carries the exact CSP | U |
| 1.6 | Browser sends packet with another peer's `senderID` after binding | Socket closed, packet never reaches `processIncoming` (packet-sink fake) | U |
| 1.6a | Browser replays another peer's recent signed ANNOUNCE (a BLE peer's and another browser's), including with TTL rewritten to 7, then sends `BIND` without the key | No binding; `BYE 0x05`; victim's addressed packets still reach the victim; victim never appears as a WEB direct neighbor | U, S |
| 1.6b | Valid ANNOUNCE and `BIND` for a peer ID the hub knows over BLE | Refused with `BYE 0x05` | U |
| 1.6c | Browser packet with TTL ≠ 7 (non-gateway) or outside 4..7 (gateway) | `BYE 0x02`; never relayed | U |
| 1.7 | Browser sends unsigned or wrongly signed `MESSAGE` | Rejected by `SecurityManager`, never relayed, never shown on hub | U, S |
| 1.8 | Browser `MESSAGE` relayed to BLE | Outbound bytes equal inbound bytes except TTL; no `signData` call | U |
| 1.9 | Hub code path search | No call from web code into `signPacketBeforeBroadcast`, `sendMessage` or `TransportBridgeService.broadcast` with browser bytes | U (static check) |
| 1.10 | Hub key custody (structural) | `WebMeshService` and `ApkWebServer` expose no API that accepts key material; a recording `EncryptionService` fake sees no decrypt call for any virtual peer ID during a browser-app private chat | U, S |
| 1.11 | Web client without gateway opt-in | Zero connections to any non-hub address during a scripted session, other than DataChannels to signaled local host candidates; with `WEBRTC` off, zero non-hub connections; no STUN or TURN request ever | W, X |
| 1.12 | Opted-in browser gateway receives `REQUIRE_TOR` post (hub bug simulated) | Not published | W (Kotlin counterpart: the hub never forwards it, U) |
| 1.12a | Author-helper browser receives its own post back | Zero relay connections, no ack, no `REQ` for that id; hub never sends `0x2A` to the ingress socket | W, U |
| 1.13 | Logging: every route and failed-validation scenario (bad signature, replayed announce, key replacement) with a recording logger, then a release-build logcat scan | Nothing logged contains the synthetic IP, user agent, token, browser peer ID or content | U, M |
| 1.13a | Lint | `lintDebug` fails on a `Log` call in `com.bitchat.android.web` or `ApkWebServer` and on peer-ID interpolation in the listed mesh classes | U (lint test) |
| 1.14 | Panic on hub (also from the Invite screen and the notification) | Sockets closed, server stopped, P2P group removed, `hotspot` preferences empty, in-memory SSID, passphrase, token and bridge key cleared, Invite screen dismissed | U, M |
| 1.15 | Bridge without the Noise handshake, with a wrong-length handshake, or a corrupted transport message | Socket closed with 1002 before any frame is processed | U, W |
| 1.16 | Passive capture with the passphrase (synthetic) | Captured bridge bytes contain no peer ID, nickname, flags or signaling record in clear | S, M |
| 1.17 | Browser Wipe | LEAVE sent, sockets closed, key buffers zeroed, DOM cleared, navigation to `/` with no `beforeunload` prompt | W, X |
| 1.18 | Page load with planted `localStorage`, `sessionStorage`, IndexedDB and a cookie (synthetic) | All cleared before the client runs; hub expires the cookie and logs nothing about it | W, U |

### 2. Tampering and identity

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 2.1 | Signal record with wrong signature, wrong `to`, unknown `from`, stale timestamp, identical resend, trailing bytes | Dropped | W (Kotlin counterpart in `WebRtcSignalTest`) |
| 2.1a | Two different `0x03` records from A with the same `timestampMs` | Both accepted | W |
| 2.2 | Signal `from` not equal to socket peer | Socket closed by hub | U |
| 2.3 | Malformed bridge frames (unknown type, truncated, text message, second `HELLO`, reserved flags, over-long body) | Close with 1002/1003 | U |
| 2.3a | `SIGNAL` frame with a 2,000-byte synthetic SDP; with an 8,193-byte body | Routed; closed with 1002 | U |
| 2.4 | Browser receives forged `MESSAGE` over DataChannel | Not displayed, not relayed | W |
| 2.4a | Browser receives a compressed packet signed over the compressed preimage | Verified over the received compressed bytes; displayed | W |
| 2.5 | Installed-app audit against a hub serving a modified asset; an older official bundle; a revoked bundle | Red; green (known-good list); red | U, M |
| 2.6 | Earlier hub left a cached script at the same origin | New invite path loads fresh assets (cache key differs) | X |
| 2.7 | Browser clock off by 30 min (fake clock) | Offset applied automatically; ANNOUNCE carries the corrected timestamp; non-blocking notice; socket bound within 15 s | W, I |
| 2.8 | Invalid ANNOUNCE (bad signature) | `BYE 0x05` at once, not a 15 s timeout | U |
| 2.9 | ANNOUNCE then `BIND` then MESSAGE sent immediately | MESSAGE delivered after `BOUND`; a MESSAGE before `BIND` is dropped without closing | U |
| 2.10 | Takeover: a second socket proves the same key for a bound peer ID (fake clock, old socket still awaiting pongs) | Old socket `BYE 0x06`; addressed packets go to the new socket; backfill resent | U |
| 2.11 | Resume on a token rotated 5 min earlier; new identity on that token | Takeover succeeds; new identity refused | U |

### 3. Flooding and limits

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 3.1 | 33rd browser connects | The newest silent socket is evicted; if all have sent messages, 503; others unaffected | U, S |
| 3.2 | One browser floods `MESSAGE` at 100/s | Throttled then disconnected; BLE side sees ≤ the rate limit | S |
| 3.3 | Many addresses open sockets with new identities | Hub-wide new-binding bucket (burst 16, 6 per minute) and `BYE` backoff hold; resumes are not charged; held announces never reach BLE for identities that send nothing | S |
| 3.3a | 32 browsers each at their per-socket limits | BLE egress of browser traffic ≤ the hub-wide budgets per type; browsers still receive each other's broadcasts | S |
| 3.3b | A remote peer sends 100 private messages per minute to a browser | Browser stays connected; receipts coalesced or dropped; no `BYE` | I |
| 3.3c | Three rapid reconnects from one address (fake clock) | All succeed; the oldest socket is evicted each time | U |
| 3.4 | Stalled browser stops reading | Outbound queue capped at 256 KiB; hub memory flat | S |
| 3.5 | 200 simulated browsers join and leave over an hour (fake clock) | Actor map and peer maps bounded; with 32 bound browsers, a TTL-3 packet entering through WEB is relayed under a seeded `Random` (virtual peers excluded from that `MeshCore`'s network size) | S |
| 3.6 | Slow-loris HTTP clients; 16 idle TCP connections | Closed after the 5 s header deadline; per-address cap holds; real clients still served with a fixed 503 at the cap | U, M |
| 3.7 | 10 phones scan Step 2 at once | All load within the budget | M |
| 3.8 | Browser `GATEWAY_ACK` over 12 per minute; from a non-helper socket | Excess dropped; non-helper closed with `BYE 0x02` | S, U |
| 3.9 | Many sockets send gateway posts | Together they get one fair share of re-origination | S |
| 3.9a | One socket sends duplicate and invalid `0x2A` posts at its limit while another socket sends one valid post (fake clock) | The flooding socket is throttled and closed; the valid post is accepted, stored and queued; no aggregate ceiling blocks it | S |
| 3.10 | Browser re-announce over 10 simulated minutes | Never trips the hub's announce limit | W |

### 4. Network and interop

| # | Scenario | Expected | Type |
| --- | --- | --- | --- |
| 4.1 | Browser ↔ iOS bitchat public chat through hub | Messages both ways; iOS shows browser as a normal peer | X |
| 4.2 | Browser ↔ Android app private chat | Noise session completes; read and delivered receipts work | Kotlin fake browser (M) plus JS client (I) |
| 4.3 | Browser ↔ browser on the same hub | Delivered without BLE | S, M |
| 4.4 | DM to a browser arriving at hub with TTL 1 | Delivered to the socket (deterministic path) and relayed as before | U, S |
| 4.4a | BLE `PacketProcessor` gets a TTL-1 broadcast with network size > 100 | Exactly one `WebDeliverySink` delivery | U |
| 4.5 | Large crowd: network size > 100 | Broadcasts still reach every browser | S |
| 4.5a | Same MESSAGE arrives by BLE relay, by the hub's own relay and by backfill | Socket gets exactly one `PACKET` frame | S |
| 4.6 | WebRTC on hub's P2P group per browser pair | DataChannel success or fallback recorded per browser/OS | X |
| 4.7 | WebRTC on an offline venue router, hub app killed | DataChannels survive; messages flow; page shows "Host left" state | X |
| 4.8 | Browser gateway posts to a local fake relay | Exactly one event; budgets respected | W, S |
| 4.9 | Browser-authored gateway post reaches a Tor gateway phone | Published by the phone per MESH_GATEWAY_V1 | Kotlin fake browser (M) plus JS client (I) |
| 4.10 | Join backfill | Browser shows recent verified messages; forged backfill dropped | S |
| 4.11 | Peer P announces at t = 0; browser binds at t = 25 s; P sends a MESSAGE at t = 26 s (fake clock) | Browser displays it | S, W |
| 4.12 | JS client completes Noise XX and a signed MESSAGE with a Kotlin `MeshCore` peer through the in-process hub | Both directions verify and decrypt | I |
| 4.13 | Invite screen, both codes visible | Step 1 always visible while N guests join in quick succession (injected clock and client-count flow) | U |
| 4.14 | Browser 16-bit text-only PoW on the oldest matrix row | Within the benchmark threshold | W, X |

### 5. Device matrix (manual, timed)

Time each row from scan to composer-ready (chat visible and composer
focused), recorded separately for the Wi-Fi-join scan and the link scan. The
target is ≤ 10 s on **every** row; over 10 s on any row is a failure that needs
a documented simplification. The manual Wi-Fi join (typing SSID and password)
is timed in its own column and not counted toward the target.

| Browser / OS | Join Wi-Fi from QR | Manual Wi-Fi join | Load | Public chat | Private chat | Lock 30 s and return | WebRTC | Notes to record |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| iOS Safari, current and previous major | X | X | X | X | X | X | X | Captive-network sheet behaviour; "no internet" prompts; cellular routing |
| Android Chrome, current | X | X | X | X | X | X | X | Local Network Access prompts; mDNS candidates |
| Android Chrome on an old phone (Android 8-9) | X | X | X | X | X | X | X | Whether the stock camera joins from `WIFI:`; load time; key generation and PoW time |
| Firefox for Android | X | X | X | X | X | X | X | `DecompressionStream` fallback path |
| Samsung Internet | X | X | X | X | X | X | X | |
| iPadOS Safari | X | X | X | X | X | X | X | |

Expected result for "Lock 30 s and return": "Reconnect" restores the same peer
ID if the tab survived (no reset notice); otherwise a new identity with the
reset notice. Record which happened.

Hub matrix: one low-end and one current Android hub; API 29-32, 33-36 and 37+
permission paths (`app/src/main/java/com/bitchat/android/hotspot/HotspotPermissions.kt:10-33`);
battery LOW and CRITICAL; 1, 10 and 30 browsers, or the measured maximum if
lower; 60-minute session (wake-lock renewal). Per hub chipset, measure the
maximum number of associated legacy (non-P2P) Wi-Fi clients, throughput per
client, BLE coexistence on the shared 2.4 GHz band (`HotspotManager.kt:512`),
and whether `group.clientList` counts legacy stations; set the browser cap from
the measured value.

### Mesh Lab entries

- Add debug-only `TestHookDriver` commands in `app/src/debug/`:
  `web_invite_url` (returns the URL to the host only), `web_counts` (sockets,
  bound peers, throttles; counts only) and `web_fake_browser` (attaches an
  in-process fake browser that speaks the bridge protocol, so two-device Mesh
  Lab can test hub ↔ BLE peer without a real browser).
- Add `scenario_web_join` to `tools/release_gate/mesh_lab.py`: device A hosts,
  device B runs the app; A attaches fake browsers; B must see their messages and
  complete a Noise session with one. Evidence keys are aggregate counts only.
- `web_fake_browser` exercises Kotlin, not the JS Noise, announce, signing or
  PoW code; that code is covered by the I tier and the manual matrix.
- Real-browser runs stay in the manual matrix; raw evidence stays local.

## Golden vectors

All keys below are public test-vector keys (RFC 7748 for X25519, RFC 8032 for
Ed25519) and must never be used for anything real. Values marked computed were
produced with a standard X25519/Ed25519/SHA-256 implementation; Ed25519 is
deterministic, so implementations must reproduce the signature exactly. Every
value in the block below (public keys, peer IDs, frame bytes, the signaling
digest and signature, the `BIND` signature and the manifest digest and code)
was recomputed independently and matches; the `BIND` signature verifies
against browser A's Ed25519 public key.

```text
# Browser A: X25519 private key = RFC 7748 Alice private key
x25519 public A  8520f0098930a754748b7ddcb43ef75a0dbf3a0d26381af4eba4a98eaa9b4e6a
peerID A         300c9c9603b92a4b            (SHA-256(public A)[0..8], computed)
# Browser A: Ed25519 secret = RFC 8032 test 1
ed25519 public A d75a980182b10ab7d54bfed3c964073a0ee172f3daa62325af021a68f707511a

# Browser B: X25519 private key = RFC 7748 Bob private key
x25519 public B  de9edb7d7b7dc1b4d35b61c2ece435373f8343c85b78674dadfc7e146f882b4f
peerID B         f35e5616160a30bf            (computed)
# Browser B: Ed25519 secret = RFC 8032 test 2
ed25519 public B 3d4017c3e843895a92b70aa74d1b7ebc9c982ccf2ec4968cc0cd55f12af4660c

# HELLO: frameType 02, version 01, flags 01 (WEBRTC)
02 01 01

# WELCOME: frameType 03, version 01, flags 00, maxPacketBytes 512,
# hubPeerID 01 02 03 04 05 06 07 08 (synthetic), hubTimeMs 1700000000000,
# bindNonce 00 01 02 ... 1F (synthetic)  -- 53 bytes
03 01 00 02 00 01 02 03 04 05 06 07 08 00 00 01 8B CF E5 68 00
00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F
10 11 12 13 14 15 16 17 18 19 1A 1B 1C 1D 1E 1F

# BOUND: frameType 09
09

# THROTTLE: rate, retry after 5 s
05 01 00 05

# WebRtcSignal fields (offer from A to B, timestampMs 1700000000000,
# body = "v=0\r\nsynthetic-offer\r\n", 22 bytes)  -- 50 bytes, computed
01 01 30 0C 9C 96 03 B9 2A 4B F3 5E 56 16 16 0A 30 BF 00 00 01 8B CF E5
68 00 00 16 76 3D 30 0D 0A 73 79 6E 74 68 65 74 69 63 2D 6F 66 66 65 72
0D 0A

# signing input = "buzzchat/webrtc-signal/v1" || fields (75 bytes)
# SHA-256(signing input), for debugging only
2f5e6d0b80e3665959b4dd937929c6c1ca783510998ad21152c646f7a410480c

# Ed25519 signature by A (computed, deterministic)
E4 5B 41 A9 F9 64 AB 96 3B 7D 7B 68 F1 D8 7F 58 C9 69 0B D0 7F 1B B6 89
8D 82 1B 7D 1A F4 24 5A B9 0D 1C C9 E7 DA 5B 4B C1 5A F7 3B AF 51 64 75
49 30 1F 8E 15 6F E1 BC 8E 5A 4F 19 38 76 B4 0F
# full SIGNAL bridge frame = 04 || fields || signature (115 bytes)

# BIND for the WELCOME vector above (computed, deterministic).
# signed message = "buzzchat/web-bind/v1" || bindNonce (00..1F)
#                  || hubPeerID 0102030405060708 || peerID A  (68 bytes)
# BIND frame = 08 || peerID A || Ed25519 signature by A  (73 bytes)
08 30 0C 9C 96 03 B9 2A 4B
3C F6 4F EE 4A A7 5F 56 4A E2 7D 54 EE 68 55 5B 78 05 27 CC E7 98 C9 34
EC B6 5F 57 8A 94 38 BF 8B 77 2E 09 1C A6 BA 38 0B 02 E7 6F 87 59 67 A4
D7 C9 C2 89 69 DE B5 63 04 C3 30 92 E3 F2 F9 01

# Web bundle manifest body (after the first line), synthetic files whose
# contents are the ASCII strings "test" (a JS asset, content-addressed name
# a/app.<first 16 hex of its SHA-256>.js) and "test2" (index.html), sorted by
# path bytes ("a/..." before "index.html"):
9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08  a/app.9f86d081884c7d65.js\n
60303ae22b998861bce3b28f33eec1be758a213c86c93c076dbe9f558c11c752  index.html\n
bundleDigest  75eb5fed5f0f98c3240994f9000d6970950abbed7680ffa767e4bce35759a1eb
first 5 bytes 75 eb 5f ed 5f = 506460106079 (mod 10^12)
bundleCode    5064-6010-6079
# URLs: index.html -> /j/<t>/ ; a/app.9f86d081884c7d65.js -> /j/<t>/a/app.9f86d081884c7d65.js
```

TO BE GENERATED with the real Kotlin encoder (`BinaryProtocol`,
`IdentityAnnouncement`, `BitchatMessage`) and checked by both the Kotlin and the
JS test suites:

- browser A's ANNOUNCE packet (nickname `guest-0001`, timestamp
  1700000000000, TTL 7), its signing preimage and Ed25519 signature;
- browser A's broadcast `MESSAGE` ("Synthetic web message.") and its signature;
- a browser MESSAGE whose payload meets `shouldCompress` (≥ 100 bytes,
  low entropy), sent uncompressed and signed over the uncompressed preimage,
  which current Kotlin verification accepts; and an app-originated compressed
  MESSAGE that the JS client must verify over its received compressed bytes;
- boundary MESSAGE and Noise packets at exactly 512 bytes and one byte over,
  fixing the composer's maximum UTF-8 lengths;
- a `Noise_NK` bridge handshake and one transport message carrying the `HELLO`
  vector, with synthetic hub bridge and ephemeral keys;
- a `PACKET` frame wrapping that MESSAGE;
- a Noise XX transcript between A and B using the production configuration
  (empty prologue; 32/96/64-byte handshake messages), plus one framed
  transport message with explicit nonce.
  The existing external vector (`app/src/test/kotlin/com/bitchat/android/noise/NoiseExternalVectorTest.kt`)
  uses the prologue `John Galt`, so the JS Noise port must accept a prologue
  parameter to run it too;
- the MESH_GATEWAY_V1 vectors, reused unchanged for browser-authored posts
  ([Golden vectors](MESH_GATEWAY_V1.md#golden-vectors)).

Fixture and tests:

- `app/src/test/resources/contracts/web-join-v1.json` holds every value above.
  The Kotlin tests and the web client's test runner load the same file.
- `app/src/test/kotlin/com/bitchat/android/web/WebBridgeFrameTest.kt`:
  `` `bridge frames match canonical bytes` ``, `` `malformed frames close the socket` ``.
- `app/src/test/kotlin/com/bitchat/android/web/WebRtcSignalTest.kt`:
  `` `signal record vector verifies` ``, `` `every field change breaks the signature` ``.
- `app/src/test/kotlin/com/bitchat/android/web/WebBundleManifestTest.kt`:
  `` `manifest digest and bundle code match vector` ``.
- `app/src/test/kotlin/com/bitchat/android/hotspot/ApkWebServerTest.kt` (none
  exists today): routes, headers, token, Host and Origin checks, token-free
  HTML, connection caps, cookie expiry.
- `app/src/test/kotlin/com/bitchat/android/web/WebBindTest.kt`:
  `` `bind proof vector verifies` ``, `` `replayed announce never binds` ``,
  `` `takeover requires the same signing key` ``.
- `app/src/test/kotlin/com/bitchat/android/web/WebBridgeChannelTest.kt`:
  `` `noise nk bridge vector decrypts` ``, `` `frame before handshake closes the socket` ``.

## Implementation references

- `app/src/main/java/com/bitchat/android/hotspot/ApkWebServer.kt`
- `app/src/main/java/com/bitchat/android/hotspot/HotspotManager.kt`
- `app/src/main/java/com/bitchat/android/hotspot/HotspotViewModel.kt`
- `app/src/main/java/com/bitchat/android/hotspot/HotspotActivity.kt`
- `app/src/main/java/com/bitchat/android/hotspot/QrCodeGenerator.kt`
- `app/src/main/java/com/bitchat/android/wifi-aware/WifiAwareMeshService.kt` (transport template)
- `app/src/main/java/com/bitchat/android/wifi-aware/WifiAwareController.kt`
- `app/src/main/java/com/bitchat/android/mesh/MeshCore.kt`
- `app/src/main/java/com/bitchat/android/mesh/MeshTransport.kt`
- `app/src/main/java/com/bitchat/android/mesh/PacketProcessor.kt`
- `app/src/main/java/com/bitchat/android/mesh/PacketRelayManager.kt`
- `app/src/main/java/com/bitchat/android/mesh/SecurityManager.kt`
- `app/src/main/java/com/bitchat/android/mesh/AnnouncementIdentityValidator.kt`
- `app/src/main/java/com/bitchat/android/mesh/UnifiedMeshService.kt`
- `app/src/main/java/com/bitchat/android/service/TransportBridgeService.kt`
- `app/src/main/java/com/bitchat/android/noise/NoisePeerIdentity.kt`
- `app/src/main/java/com/bitchat/android/noise/NoiseSession.kt`
- `app/src/main/java/com/bitchat/android/protocol/BinaryProtocol.kt`
- `app/src/main/java/com/bitchat/android/protocol/CompressionUtil.kt`
- `app/src/main/java/com/bitchat/android/mesh/PowerManager.kt`
- `app/src/main/java/com/bitchat/android/mesh/MessageHandler.kt`
- `app/src/main/java/com/bitchat/android/sync/GossipSyncManager.kt`
- `app/src/main/java/com/bitchat/android/ui/ChatViewModel.kt` (panic flow)
- `wear/build.gradle.kts` (shared include list)
- `gradle/libs.versions.toml`, `app/gradle.lockfile`, `gradle/verification-metadata.xml`
- `docs/MESH_GATEWAY_V1.md`, `docs/PRIVATE_MEDIA_V1.md`,
  `docs/NOISE_PEER_ID_BINDING.md`, `docs/ANNOUNCEMENT_GOSSIP.md`,
  `docs/reproducible-builds.md`, `docs/testing-conventions.md`

## Open questions

1. **iOS behaviour.** Does iOS enforce the same peer-ID binding and signature
   checks, and does it limit peers per relay or rate-limit announces in a way
   that many browser peers behind one hub would trip? Not determinable here.
2. **WebRTC feasibility.** Do Android P2P groups forward client-to-client
   unicast and multicast (mDNS)? Do current Chrome and Safari connect host
   candidates between two insecure private-address pages without prompts? If
   not, WebRTC stays off on hub-hosted networks.
3. **WebSocket implementation.** `nanohttpd-websocket` 2.3.1 is old and
   unmaintained. Is a small in-house RFC 6455 server, fuzz-tested, a better
   supply-chain choice? Also: how many concurrent sockets does a low-end hub
   sustain with one thread per connection?
4. **Other networks.** Should a host be able to serve the web client on a
   shared non-hotspot network (venue router) for true one-scan join, and how is
   that consent shown? This changes the bind rule and the threat model.
5. **Pinned copies.** Is an HTTPS mirror plus Service Worker plus `wss://` to the
   hub ever practical without a trusted local certificate? Can a pinned copy use
   WebRTC to the hub (not subject to mixed content) with QR-based signaling?
6. **iOS re-announce cadence and presence timeout.** Android is known (30 s
   foreground announce, 180 s stale timeout); the iOS values are not
   determinable here, so whether a 30 s browser cadence avoids flapping on iOS
   is unverified.
7. **Join backfill API shape.** The filter-free `handleRequestSync` variant is
   required; open is only whether it lives in `GossipSyncManager` or in
   `WebMeshService` reading the same stores.
8. **Files to browsers.** Should the browser send an authenticated `0x21` proof
   with capabilities 0 so senders know it cannot receive files, or should the
   hub answer on its behalf? (It must not sign for the browser.) Should the hub
   tell senders "recipient joined by link, files unsupported"?
9. **Bundle size and crypto performance.** Measured cost of key generation,
   Noise handshakes (XX and the NK bridge) and 16-bit PoW in JavaScript on the
   oldest supported phones; is a WASM build (needing `wasm-unsafe-eval`) worth
   it?
10. **Cellular routing for browser gateways.** On iOS and Android, does a page
    on a no-internet Wi-Fi reach `wss://` relays over cellular, and does iOS's
    captive-network handling disconnect the hotspot?
11. **Below API 29 hubs.** Framework-generated SSID and passphrase: can they break
    the `WIFI:` QR? Should join by link require API 29+ hubs?
12. **Browser identity continuity.** Is an opt-in, encrypted, passphrase-protected
    export of a browser identity worth the cross-hub storage risk?
13. **Capability bit.** Should a browser announce advertise a "joined by link"
    hint so other clients can explain missing features? It is self-asserted,
    fingerprints the user, and needs upstream agreement, so v1 says no.
14. **iOS signature preimage (release blocker).** Does iOS bitchat verify a
    received uncompressed payload of ≥ 100 bytes over the received bytes, or
    does it recompress? If it recompresses, browser MESSAGEs meeting
    `shouldCompress` fail on iOS.
15. **Matching deflate encoder.** Is a JS raw-deflate encoder that matches
    `java.util.zip.Deflater` at the default level byte for byte (golden
    vectors from the Kotlin encoder) worth shipping so browsers can follow the
    originator rule? It would still not match a different deflate
    implementation on another client.
16. **WPA3-SAE.** Can the hub's P2P group use WPA3-SAE on supported devices,
    which resists passive decryption by other password holders, and do the
    browsers' phones join it from a QR code?
17. **Group-owner client capacity.** How many legacy (non-P2P) Wi-Fi clients
    does each hub chipset accept, at what throughput and BLE coexistence, and
    does `group.clientList` list them?
18. **Hub-wide budgets.** The BLE egress budgets, new-binding limit and held
    announce interval are placeholders; they must be sized from measured BLE
    mesh throughput.
19. **JS test runner.** Which runner (a pinned Node.js toolchain or a
    JVM-embedded engine) best fits `docs/reproducible-builds.md` and CI?
20. **Audit capture.** How does the app capture response bytes from an
    Android WebView without altering the requests the WebView makes, and how
    does the auditor's temporary guest identity leave cleanly?
21. **Gateway posts without the chat identity.** Would sending gateway posts on
    a separate, unbound socket opened after a random delay help, given that MAC
    and IP still link it to the device? Or should a browser hand its post to a
    non-hub peer over Noise?
22. **Per-socket gateway ceilings.** Counting MESH_GATEWAY_V1's per-link
    ceilings per socket (to stop one socket locking others out) while the
    fair-queue share stays aggregate is a hub-local reading of that spec. Should
    MESH_GATEWAY_V1 state it for virtual links in general, and is the
    new-binding bucket (burst 16, 6 per minute) enough to bound the extra
    buckets a QR holder can create?
23. **Hub → browser frame cap.** The 65,536-byte cap on delivered `PACKET`
    frames is chosen, not measured: what is the largest reassembled `MESSAGE`
    an app sends today, and can low-end browsers parse and verify a
    20,758-byte `0x2A` for the helper role without stalling the page?
