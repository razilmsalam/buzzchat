# BuzzChat principles

BuzzChat is a decentralized messenger for places where the internet is shut
down, filtered, or jammed. It builds on the bitchat mesh. Every design decision
is measured against the two rules below.

## Rule 1: Simple enough for anyone, under stress

- Joining is one scan: point a camera at a QR code (or open a link) and you
  are in. No account, phone number, password, or install is required to start.
- The main screen is a familiar chat and feed layout. Nothing needs to be
  configured before the first message.
- It must work in a noisy crowd, with one hand, on a low battery.

The test for every feature: **could someone who has never seen BuzzChat use
this within ten seconds of scanning the code, on whatever phone they are
holding?** If not, simplify it or hide it.

## Rule 2: Works on whatever device people already hold

| Device | Role |
| --- | --- |
| Any phone with a browser | Joins by link; chats and posts through a nearby hub or browser peers |
| Android with the BuzzChat app | Full mesh node, hub, and Tor gateway |
| iPhone with bitchat (App Store) | Full mesh node, compatible with BuzzChat |
| Any device that has internet | Quietly helps everyone's opted-in posts reach the outside world |

## Commitments that follow

- **Stay bitchat-compatible.** A larger network beats a perfect protocol.
- **No single point of failure.** No company, server, or device has to stay
  online for the network to work.
- **Fail closed.** When something is uncertain (consent, Tor, signatures,
  integrity), BuzzChat does nothing rather than something unsafe.
- **Consent before reach.** Nothing leaves the local mesh for the internet
  unless its author chose that for that post.
- **Honest limits.** The UI never claims more than the system guarantees
  (for example, "reached the internet" is shown as unverified).
- **Protect the person, not just the message.** Minimize metadata, keep the
  emergency wipe one obvious tap away, and never collect location.

## Related specifications

- [Mesh gateway posts v1](MESH_GATEWAY_V1.md)
