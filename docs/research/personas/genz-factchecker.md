# Persona: Meera (fictional)

> **Note**
> Simulated persona for design research. Not a real person; quotes are synthetic. Validate with real, consenting participants before relying on these findings.

A volunteer fact-checker and software tester for whom the unforgeable seal is the whole product and the mesh is a "fire extinguisher" for friends on the ground. She would use seal verification daily to back her debunks on X, and she would actively tell people not to use BuzzChat if it adds single-holder signing keys, forgeable QR posters, always-visible Bluetooth or a second-class iPhone experience.

## At a glance

| Attribute | Detail |
| --- | --- |
| Age | 23 |
| Region | Bengaluru, where she shares a PG (paying guest) room. Her family lives in Mysuru, where her father's shop is struggling. |
| Languages | Kannada, English and Hindi. Posts debunks in English; WhatsApp with family. |
| Role | Manual-plus-automation software tester at a mid-size services company, taking home about 38k rupees a month. About 12k goes on her room share and she sends 8k home. Her younger brother is a NEET aspirant, and the paper leak brought her into the movement. She is not a marcher: she volunteers in a small Discord group that hunts impersonation accounts, forged circulars and AI-edited images. |
| Shutdown history | Experienced shutdowns second-hand. On the 20 July march day, internet services near Parliament were reported shut down and friends on the ground went dark. Photos reached her group hours late, and nobody could answer "is this from today?". A fake shift schedule sent people to a different gathering point. |
| Devices | An iPhone 15 (128 GB, always full of evidence screenshots, battery health 86%, lasts until about 7 pm on heavy verification days, still on EMI). A work laptop she keeps movement activity off, because IT can see everything. Apps: X (debunk threads), Instagram (lurking and reposting carousels), Discord (the verification group, and the app she would most fear a phone check exposing), Signal (four or five trusted people), WhatsApp (family only). Makes "spot the edit" graphics in Canva, not reels. Does not trust iCloud. Cannot sideload. She could run upstream bitchat from the App Store for mesh chat, but BuzzChat-only features (seals, board, gateway posts) would put her on the browser-only, hub-dependent mode. |
| Connectivity | Jio postpaid at about ₹450 a month plus office Wi-Fi. Reliable connectivity herself; her concern is friends who lose data during shutdowns. |
| Literacy / voice preference | Highly technical and text-first. Thinks in provenance, chains of custody and attack testing. Designs for non-technical people like her brother and mother, who will not read fingerprints or URLs. |
| Security posture | Had two weeks of gendered, two-sided trolling after publicly debunking an AI-expanded crowd photo from her own side. Has since locked down her accounts and deleted old joke tweets. Fears, in order: an employer background-verification flag, her mother finding the trolling, and satire being reread as something legal later. Deliberately avoids picking factional winners so she does not become the "IT cell" she was accused of being. |
| Main goals | Make forged notices and impersonation easy to disprove, publish verdicts without making herself a target, and keep her own evidence archive from becoming a liability. |

## Key problems

| Problem | Severity | Quote |
| --- | --- | --- |
| The signing key is a single point of failure. If one person holds it and is detained, or the key is copied, BuzzChat produces signed forgeries that are more convincing than today's unsigned ones. The concept has no threshold or co-signing, no revocation and no visible key history. | Critical | "If one founder holds it and gets detained, or the key gets copied, then the fakes become signed fakes, which is worse than unsigned ones." |
| Browser QR join is the most obvious phishing vector. A copied poster with a QR that leads to a lookalike board looks the same as the real one, and non-technical users judge by who holds the poster and whether it looks official. | Critical | "A fake poster with a fake QR leading to a lookalike board is the first attack I'd test." |
| Non-technical users cannot tell a real board from a lookalike in the few seconds they have at a gate. Any safety model that relies on reading fingerprints or URLs fails her five-second test. | Critical | "What he won't do is read a key fingerprint, compare hex strings, or check a URL carefully. If the safety depends on him doing that, it's already broken." |
| iPhone users get no native BuzzChat app. Upstream bitchat on the App Store gives them mesh chat, but not the seal checks or board she joins for, so for those they are on the browser version and tied to a nearby Android hub, which makes them second-class users. That is most of her social circle, and it kills her willingness to recommend BuzzChat. | Critical | "I'm on iPhone, so I can't install from a friend's phone. I'd get plain chat from the App Store app, but for the seal and the board I'd be on the browser version, tied to a nearby hub. That makes me a second-class user, and most of my friends are iPhone or high-end Android." |
| The Bluetooth advertisement reveals that the app is running. In a crowd with police around, that is a personal risk. She expects Bluetooth to be off by default, with an explicit control. | High | ""Anyone scanning Bluetooth can tell the app is running." That alone would make me nervous in a crowd with police around. Can it at least be off by default?" |
| Faction splits produce two valid seals that both claim to be official. A plain valid/invalid badge cannot express "real but disputed", and the software or its developers silently choosing a winner would be the worst outcome. | High | "Two factions means two keys, and both say "official". Is that a "seal" or just a brand war?" |
| The seal is only valuable if it can be verified where the debunking happens, on X and Instagram, from a screenshot or link. Nostr reach is negligible for her audience, and the movement measured everything by reach. | High | "My friends are not on Nostr. If posts don't reach Instagram, the movement won't care, because reach was the entire strategy." |
| Unsigned and signed bulletin notices look too similar. A forged shift notice that redirected people to a different spot is exactly the harm the board must prevent, and a small icon will not do that. | High | "But the templates need a signed/unsigned badge that ordinary people actually notice, not a tiny icon." |
| Photos that circulate during a blackout carry no trustworthy time, place or custody chain. EXIF can be edited and usually gets stripped anyway, so verifiers cannot answer the basic "is this from today?" question. | High | "People asked "is this from today?" and we couldn't say." |
| Images that hop across the mesh may leak metadata, location and the faces of the people in them. She expects stripping by default and optional face blur; the concept promises neither. | High | "Images hopping phone to phone: do they strip metadata and faces' location info? For people in photos I'd want automatic face blur as an option." |
| A verifier's evidence archive is self-incriminating. Out of context, forged circulars look like she made them, and troll screenshots expose her contacts. Any tool that hoards raw media or syncs to cloud backup by default makes this worse. | High | "I collect evidence to protect the truth, and the archive itself is the most dangerous thing I own. Out of context, a folder of fake circulars looks like I *made* fake circulars." |
| Debunks posted under a personal handle invite gendered harassment and doxxing threats. BuzzChat has no concept of a group verdict signed by a collective key. | Medium | "The debunk was right, and making it personal was the mistake. That's partly why I want signing: a verdict from a group key, not from "some girl on X"." |
| Without video, the movement sees BuzzChat as a utility, not a home. The emotional glue (anthem clips, reels) is missing, and so is a way to sign originals so that edited or AI reposts can be disproven. | Medium | "Our whole movement ran on reels and anthem-singing clips. Small images only is a big ask." |
| Overclaiming in the marketing ("uncensorable") damages credibility with exactly the sceptical, technical users who would champion BuzzChat. | Medium | ""Uncensorable" in the marketing. Nothing is. Say "harder to take down"." |
| Her phone storage is already full. Without auto-delete, mesh media and queued posts compete with her evidence screenshots, and storage pressure becomes a reason to uninstall. | Medium | "Storage is the hard part, because my phone is already full, so it needs an auto-delete setting." |

## Needs

- Seal verification in one tap that works offline, from a scan, a link, a saved image or a screenshot, so she can cite it in X debunks.
- Key governance: k-of-n co-signing (her example is three of five organisers), visible revocation, and a public, append-only key history where a faction split shows up as a visible fork rather than being silently resolved.
- Wording that separates authenticity from endorsement: "this group really posted this", not "this group is right".
- A full-width, high-contrast signed/unverified banner after scanning a QR or opening a board, plus a short spoken word code that the movement also publishes on its Instagram story and volunteers say out loud.
- An interrupting warning when a board's name or identity is almost the same as a known signed board.
- EXIF and metadata stripping on every image by default, optional automatic face blur, and no device model or exact location.
- Provenance for blackout photos: a timestamp rounded to the hour, confirmed when a relay received it (not only EXIF), a coarse area such as "central Delhi", and a trusted relay vouching "I got this directly from someone who was there", without naming the photographer.
- Group or collective signing keys for verification verdicts, so debunks are not tied to her personal identity.
- A local verification ledger that stores a hash, a verdict and a short note, can re-check an image against its hash so the raw image can be deleted, and never keeps private-chat screenshots, sender identities, contact lists, location data or default cloud backups.
- A native iOS app from the App Store with no phone number, foreground-only mesh disclosed honestly, offline seal verification, nearby board read/post, and image stripping and expiry on the device.
- An event mode that turns Bluetooth on for a set time and switches itself off, with a clear statement of the battery cost (she accepts 20–30% over a few hours).
- Auto-delete and storage caps for mesh media and expired notices.
- A bridge to mainstream platforms: export a signed post as an image with a verify code or link that can be checked from X or Instagram.
- Short low-bitrate audio clips (10–15 seconds of crowd singing), optionally very short low-quality face-blurred video, and signing of original videos so edited or AI reposts can be disproven.
- Honest marketing copy: "harder to take down", not "uncensorable".

## Deal-breakers

- Bluetooth that is always on and detectable, with no clear user control, or not off by default.
- A single person holding the movement's signing key.
- Anything that requires a phone number or links to her real identity.
- Forged QR posters or lookalike boards that ordinary people cannot easily tell apart from real ones. She would publicly advise people not to use BuzzChat.
- The software or its developers quietly choosing which faction's key is "official".
- On iPhone, working only with the screen on and the app in front without saying so up front.
- Cloud backup of verification evidence by default.

## How BuzzChat fits

| Feature | Verdict | Note |
| --- | --- | --- |
| Phone-to-phone mesh chat and public posts (no internet or SIM) | Partly | Not her daily tool, but she names one clear use case: on 20 July, a signed "no change, ignore the other notice" passed hand to hand would have killed the fake gathering-point rumour. Its value depends on signed content. Unsigned mesh posts would spread fakes just as fast while no one can check anything. |
| Browser QR/link join with no install or account | Risk | The no-account, no-number part matches her needs. But she calls the poster QR the first attack she would test, and the post-scan screen currently carries no strong authenticity signal. For iPhone users it is also the only route to BuzzChat-only features (bitchat from the App Store covers chat only) and depends on a nearby hub, which makes them second-class users. |
| Android install from a friend's phone | Gap | Useless to her and to most of her iPhone and high-end Android circle. It reinforces the two-tier experience. The repository ships only Android phone and Wear clients. iPhone users can run upstream bitchat from the App Store as a mesh node, but it lacks BuzzChat's seal and board features. |
| Shutdown queue plus "share to internet" via Nostr over Tor | Partly | Keeping authors hidden fits her fears about doxxing and her employer. But Nostr reach is close to zero for her audience, and the movement measured reach. Without exporting to X and Instagram as verifiable images, organisers will not care. |
| Area bulletin board with expiring notices and volunteer templates | Partly | Expiry limits the damage from fake shift notices, which she welcomes. It fails her test unless unsigned notices carry a loud, impossible-to-miss label and signed templates are visually distinct, not a tiny icon. |
| Movement signing key with an unforgeable seal mirrored on Nostr | Partly | Her favourite feature: "half my volunteer work disappears". But as specified it has single-key custody, no threshold signing, no revocation, no key history and no fork display for faction splits, and it cannot be verified from a screenshot or link on X. As described, a stolen key turns it into a signed-forgery machine. |
| End-to-end encrypted private messages | Partly | Not raised as a need. Signal already covers her four or five trusted contacts, so there is little extra value unless E2E messages work over the mesh during blackouts. Her bigger concern is that on-device artefacts (group names, contact traces) are visible during a phone check, which encryption in transit does not address. |
| Small images over mesh, no video over Bluetooth | Risk | Images without default metadata stripping and optional face blur endanger the people in them. No video makes BuzzChat "a utility, not a home". Short low-bitrate audio is a cheap, face-free substitute she rates highly, and the concept omits it. |
| Animated QR screen-to-camera relay under jamming | Partly | She did not discuss it directly. It is a resilient channel, but it is unauthenticated by default and carries the same forged-content risk as posters unless every transferred item shows the signed/unverified banner on receipt. |
| Bluetooth presence detectable by scanners | Risk | The disclosed limit she reacted to most strongly. It conflicts directly with her fear of being identifiable near police. She needs Bluetooth off by default and a time-limited event mode. This is in tension with the need for a dense crowd of active relays. |
| Photo provenance (relay-confirmed time, coarse area, custody chain) | Gap | Not in the concept. This is her core blackout verification need, "the relay vouches for the photo, so the person in it doesn't have to", and BuzzChat's relays could provide part of it. Relay phones are untrusted, though, so a relay's time stamp proves only what that relay claims, and is worth as much as the trust in that relay's key. |
| Group-signed verification verdicts and a hash-only evidence ledger | Gap | Not in the concept. It would protect her from personal targeting and from her own evidence archive. It fits naturally on top of the signing-key system. |

## Design implications

1. Make the seal the main product surface. Ship a standalone verifier (a web page and inside the app) that checks a signed post from a QR, a link, a saved image or a screenshot-embedded verify code, works offline once the keys are cached, and needs one tap.
2. Treat key governance as protocol work, not settings. Support threshold (k-of-n) signing for movement keys, signed rotation and revocation records, and an append-only key-history log mirrored on Nostr. Show a split as a visible fork with history length and co-signers, and never auto-select a canonical branch. Update the protocol spec and golden-vector tests in line with the fail-closed guidance in `AGENTS.md`.
3. Change badge copy from "Official" to "Signed by [key name], key history since [date], co-signed by N" so it states authenticity without implying endorsement. Add a "disputed: competing keys claim this name" state.
4. After a scan, show a full-width banner: green for signed by a known key, grey or red for unverified. Add a three-word code derived from the board key that volunteers can say aloud and organisers can post daily. Interrupt with a warning when a board's name or identity is similar to a known one. Use her five-second fake-poster test with a non-technical participant as a release gate.
5. Turn mesh radios off by default and add an "Event mode" with a visible countdown that switches itself off, an honest battery estimate, and clear copy about what an observer scanning Bluetooth can see. Acknowledge in the design that off-by-default reduces mesh density, and offset it with QR and animated-QR relays.
6. Strip EXIF and metadata from every outgoing image on the device by default (sender side, not relay side). Offer optional automatic face blur. Never transmit device model or precise location.
7. Add relay-attested provenance. Each hop can append a signed "received at [hour-rounded time], coarse area" stamp plus an optional "direct from source" vouch, without the photographer's identity. Use only coarse granularity, and only synthetic coordinates in tests and fixtures, in line with the location-privacy rules.
8. Support collective verification identities, so fact-check groups can publish signed verdicts ("forged: letterhead font mismatch") under a group key instead of personal handles.
9. Build a verification ledger that stores hash, verdict and note only, re-checks images against stored hashes, defaults to no cloud backup and keeps no sender or contact links. Offer "keep proof, delete pile" cleanup and auto-delete for mesh media and expired notices.
10. Give iPhone users a complete experience, either through seal and board support in upstream bitchat iOS (the route the synthesis recommends) or a native client, with App Store install, no phone number, foreground-only mesh disclosed honestly, offline seal verification, board read/post, and image stripping on the device. The browser-plus-hub mode should be a fallback, not the iPhone story.
11. Ship a mainstream bridge: export any signed post as a shareable image or card with a visible verify code or URL for X and Instagram. Position BuzzChat as "mesh for the blackout, mainstream for reach afterwards."
12. Add 10–15 second low-bitrate audio clips (face-free, small enough for the mesh) and signed hashes of original videos, so edited or AI reposts can be disproven even though the reels themselves live on Instagram.
13. Rewrite marketing to say "harder to take down", not "uncensorable". State the limits plainly (range, jamming, Bluetooth visibility, iOS background limits, Nostr's small audience). Sceptical technical champions like her will explain honest limits to friends but will reject hype.
14. Adoption depends on organisers endorsing BuzzChat through their existing mainstream channels ("this is the real board, check the seal") and on gate volunteers handing out the QR. Plan onboarding kits for organisers, not just individual onboarding.

## Interview transcript

### Q1. Who you are

I'm Meera, 23, and I work as a manual-plus-automation tester at a mid-size services company in Bengaluru. My take-home pay is about 38k a month. Roughly 12k of that goes on my share of a PG room, and I send 8k home to Mysuru every month because Appa's shop hasn't really recovered. My younger brother is preparing for NEET, so when the paper leak happened it wasn't news to me. It was my brother crying on a video call, saying "one more year, akka (elder sister)". That's what got me in.

I didn't march at the beginning. I'm not a slogan person. But when the satire accounts blew up, forged "official" screenshots started showing up within days, and my whole job is finding the thing that looks right but isn't. So I joined a small volunteer group that hunted fakes: impersonation accounts, forged circulars, AI-edited photos. Honestly it's the only part of activism where I feel useful rather than just angry.

### Q2. Your phone and online life

I have an iPhone 15, bought on EMI that I'm still paying off. It has 128 GB and it's always full, because I screenshot everything as evidence. The battery's at 86% health and lasts until about 7 pm if I'm verifying a lot. I'm on a Jio postpaid plan at around ₹450, plus office Wi-Fi. I also have a work laptop, but I keep movement stuff off it completely, because IT can see everything.

- **X:** where I post debunks, as threads with side-by-side crops.
- **Instagram:** mostly lurking. I repost the movement's carousels.
- **Discord:** where our verification group actually works.
- **Signal:** for the four or five people I really trust.
- **WhatsApp:** family only, which in practice means family forwarding fakes to me.

I don't make reels. I make "spot the edit" graphics in Canva: two images, three red circles, done. Boring formats get trusted more.

### Q3. The movement

From where I sat, the online side was massive reach on Instagram and chaos everywhere else. Big Discord servers, Telegram groups, WhatsApp groups, and no clear map of which ones were actually connected to the organisers. Within a day of the X account being withheld in May, there were maybe six "new official handles". One was real. Our team spent the whole night comparing posting patterns and bio links.

What worked:
- The website linking out to the real channels. It was the only stable anchor.
- Offline, food and medical volunteers at the Delhi sit-ins seemed well coordinated, but I only know that from friends.

What broke:
- On the 20 July march day, friends on the ground just went dark. We heard the internet had been shut down near Parliament, and the photos we got were from hours earlier, which made our verification job harder. People asked "is this from today?" and we couldn't say.
- Fake shift schedules circulated. One told people to gather at a different spot.

### Q4. Safety and fear

After I debunked a viral image (a crowd photo that had been AI-expanded to look bigger, and it was on our side, which made it worse), I got about two weeks of trolling. Both sides came at me. "Sellout", "IT cell", and then the usual stuff for women: comments on my face, someone saying they knew my office. My office isn't named anywhere, but I still locked everything down.

I'm scared of three things:
1. **My employer.** A background-verification flag would end me.
2. **Amma finding the trolling.**
3. **A satire post being reread as something legal later.**

I've already deleted old tweets that were just jokes. If someone asked to check my phone, I'd worry about the Discord app more than Signal, because Discord shows server names right there.

### Q5. Information problems

This is my area, so I'll be blunt. Most fakes weren't deepfakes. They were cheap fakes: a real letterhead with edited text, a real photo with a new caption, an old protest relabelled. AI images were maybe 20% of the volume, but they got the biggest outrage spikes.

There was also the "half the followers are from Pakistan" type of claim, and counter-claims, and inflated crowd numbers on our own side. I'll be honest: the movement's own people sometimes shared unverified stuff because it helped.

Verifying means reverse image search, checking EXIF when it survives (it usually doesn't), comparing fonts on notices, and asking someone on the ground. The real problem is that the movement *wants* everything loud and identifiable, and individuals need to be *unidentifiable*. Those two pull against each other in every single post.

### Q6. Reaction to BuzzChat

*(Interviewer explains the concept.)*

**What genuinely excites me** is the signing key. If the movement's official notices carried a seal that can't be forged, half my volunteer work disappears. Mirroring it somewhere accounts can't be suspended solves the "which handle is real today" mess too.

But (and sorry, tester brain) a signature proves the *key* signed it, not that the *right human* did. Who holds the key? If one founder holds it and gets detained, or the key gets copied, then the fakes become signed fakes, which is worse than unsigned ones. And there was a faction split in September. Two factions means two keys, and both say "official". Is that a "seal" or just a brand war? I'd want things like several organisers having to sign together, a visible way to revoke a key, and a public history showing when the key changed.

**The expiring bulletin board** is useful. A fake shift notice that expires in six hours does less damage. But the templates need a signed/unsigned badge that ordinary people actually notice, not a tiny icon.

**What feels cringe or useless:**
- "Uncensorable" in the marketing. Nothing is. Say "harder to take down".
- Nostr reach. My friends are not on Nostr. If posts don't reach Instagram, the movement won't care, because reach was the entire strategy.
- No video. Our whole movement ran on reels and anthem-singing clips. Small images only is a big ask.

**What confuses or scares me:**
- "Anyone scanning Bluetooth can tell the app is running." That alone would make me nervous in a crowd with police around. Can it at least be off by default?
- The browser QR join is clever, but a QR on a poster is exactly what a faker would copy. A fake poster with a fake QR leading to a lookalike board is the first attack I'd test.
- Images hopping phone to phone: do they strip metadata and faces' location info? For people in photos I'd want automatic face blur as an option.

**Would I use it?** I'm on iPhone, so I can't install from a friend's phone. I'd get plain chat from the App Store app, but for the seal and the board I'd be on the browser version, tied to a nearby hub. That makes me a second-class user, and most of my friends are iPhone or high-end Android. Honestly, I'd use the verification seal daily. Would my friends use the mesh chat? Only at a big sit-in, and only if someone at the gate is handing out the QR. It spreads if the movement's own Instagram says "this is the real board, check the seal." Without the organisers backing it, it's just one more app.

### Q7. Must-haves and deal-breakers

**Must-haves, in order:**
1. **A seal you can verify in one tap**, ideally from a screenshot or link too, so I can point to it in debunks on X.
2. **Key governance:** shared signing, revocation, and a visible history of key changes.
3. **Metadata stripping on images** by default, plus optional face blur.
4. **Expiry and a clear unsigned label** on bulletin notices.
5. **A full iPhone experience**, even if it's limited, not "find a hub".
6. **Some bridge to mainstream reach.** Even an "export this signed post as an image with a verify code" would help.

**Deal-breakers:**
- Bluetooth that's always on and visible, with no clear control.
- A single person holding the movement's key.
- Anything that needs my phone number or links to my real identity. I've been doxxed enough.
- If a forged QR poster can't easily be told apart from a real one, I'd tell people *not* to use it. That would hurt more than help.

## Follow-up interview

### Follow-up 1. You said the fake you debunked was on your own side, and that this made it worse. Can you walk me through that decision? What did you weigh before posting, did anyone in the group push back, and would you make the same call today, knowing the two weeks that followed?

Honestly, I sat on it for almost a day. The photo had been AI-expanded: the crowd at the edges repeated, and there were two identical flags. What I weighed was simple. If I could spot it, so could the other side, and they'd use it to call every crowd photo fake. At least two people in our Discord pushed back. One said, "Let it run, it's giving people hope." Another said that debunking our own side publicly was a gift to trolls, and that we should quietly message the account that posted it. We tried that first. They didn't reply, and by then the post had 40k reposts.

So I posted, but carefully. I didn't name the original account, I used only side-by-side crops, and I added a line saying the real crowd was large anyway. Would I do it again? Yes. But I'd post it from a group account instead of my own handle. The debunk was right, and making it personal was the mistake. That's partly why I want signing: a verdict from a group key, not from "some girl on X".

### Follow-up 2. On 20 July, people kept asking 'is this from today?' and you couldn't answer. Think of one specific photo from that day. What would you have needed to know about it, and from whom, to feel confident? Which of those things would you be comfortable having attached to a photo of you?

There was a photo of people sitting on a road with umbrellas, and it reached us around 6 pm. It looked like that day, but it was also raining in the photos from the sit-in a month earlier. What I needed was boring:

- **When it was taken**, roughly, to the hour, and ideally a time that someone else had confirmed when they received it, not just EXIF, which people can edit.
- **Roughly where**: an area, not a spot.
- **Who passed it along**: someone our group already trusted saying "I got this directly from a person who was there."

I didn't need the photographer's name. I needed a chain I could trust.

For a photo *of me*, I'd accept a timestamp rounded to the hour and maybe "central Delhi". I wouldn't accept my face unblurred, the device model, or any exact location. And never anything that links to my phone number. The ideal for me is that the relay vouches for the photo, so the person in it doesn't have to.

### Follow-up 3. You described the September split as possibly 'a brand war' with two keys that both say official. If BuzzChat had shown both seals as valid, what would you have told your family WhatsApp group, or your followers on X, about which one to trust? Who, if anyone, should get to decide that?

To my family group I'd say: "Both are real people, and they disagree. Don't forward either one as 'the official word'." Amma doesn't need faction politics. She needs to know which notices are forged, and that's a different question.

On X I'd be more precise. A valid seal means "this group really posted this", not "this group is right". I'd show which key has the longer history and who co-signed it, and I'd let people decide. I really don't want a verification volunteer like me picking the winner. That turns us into exactly the "IT cell" people accused me of being.

Who should decide? Not the app, and definitely not BuzzChat's developers. If the movement had agreed rules beforehand, like "the key needs three of five organisers" and "a key changes only when a majority signs the change", then the split would show up as a visible fork in the key history. People could see the fork and judge for themselves. The worst outcome is the software quietly choosing one.

### Follow-up 4. Your phone is full of evidence screenshots, and you said you'd worry more about Discord than Signal if someone checked it. How do you think about the risk that your own evidence archive poses to you? What would you want a verification tool to keep on your phone, and what should it never keep?

It's ironic. I collect evidence to protect the truth, and the archive itself is the most dangerous thing I own. Out of context, a folder of fake circulars looks like I *made* fake circulars. Screenshots of troll profiles include their handles, and my replies are visible next to them. And the archive tells anyone who opens it who I talk to and when.

What I'd want a tool to keep:
- A fingerprint (hash) of the item and the verdict, like "forged, letterhead font mismatch".
- The signed debunk I published, since that's public anyway.
- A short note on why, without names.

What it should never keep:
- Full screenshots of private chats or group names.
- Contact lists, or who sent me each item.
- Location data from the original photos.
- Anything that syncs to a cloud backup by default. My iCloud is the thing I trust least.

Ideally the tool can re-check an image against its fingerprint later, so I can delete the image itself. Keep the proof that I checked it, not the pile of stuff I checked.

### Follow-up 5. Your top must-have is checking a seal from a screenshot or link on X, and you also called reach the movement's whole strategy. If the real value of BuzzChat for you happens on Instagram and X, what is the mesh part actually for in your life? Has there been a moment when you'd have used it instead of those platforms?

Fair question. I was dodging it a bit. For me, day to day, the mesh is mostly invisible. The seal is the product.

But 20 July is the honest answer. Friends on the ground had zero data. One of them told me later that she spent two hours not knowing whether the gathering point had moved, because the fake shift schedule was going around on printouts and screenshots people had saved earlier. If the people around her could have passed a signed "no change, ignore the other notice" from phone to phone, that rumour dies right there.

So the mesh is for the moment the platforms disappear. That's exactly when fakes do the most damage, because nobody can check anything. I'm in Bengaluru, so it's not *my* daily tool. It's more like a fire extinguisher for the people who are physically there. And when they come back online, the signed posts should flow out to X and Instagram as verifiable images. Mesh for the blackout, mainstream for reach afterwards.

### Follow-up 6. You said you'd test a fake poster with a fake QR first, and that if people couldn't tell them apart you'd tell them not to use BuzzChat. Picture someone at a sit-in gate who isn't technical, maybe your brother. What would they actually look at, in the few seconds they have, to decide a QR or board is real?

My brother checks two things: whether someone he recognises is holding the poster, and whether it looks "official". Fakers can copy the second one perfectly. So the poster can't be the proof. The app screen after scanning has to be.

What he'd realistically notice:
- **A big, full-width banner**: green for "signed by [movement name]", grey or red for "unverified". Not a tiny tick.
- **A short word code**, like three simple words, that the movement also posted that morning on its Instagram story and that volunteers say out loud. If the screen says something different from what the volunteer says, walk away.
- **A warning that interrupts** when a board's name is almost the same as a known one.

What he won't do is read a key fingerprint, compare hex strings, or check a URL carefully. If the safety depends on him doing that, it's already broken. My test would be giving him a fake poster, and if he can't spot it in five seconds, it isn't ready.

### Follow-up 7. Being on the browser version would make you a 'second-class user'. What would a limited but complete iPhone experience have to include for you to recommend it to iPhone friends? And what would you give up (battery, storage, background Bluetooth) to get it?

For me, "complete" means I never have to say "first find someone with an Android hub". Minimum:

- Install from the App Store, and no phone number to sign up.
- Verify a seal offline, from a scan, a link or a saved image.
- Read and post on nearby boards while the app is open, even if it can't do that in the background.
- Notices that expire, and image stripping built into the iPhone app itself.
- Signed messages saved and synced when I'm back online.

What I'd give up: background Bluetooth, gladly. I *want* it off when I'm not using it, so that suits me fine. I'd accept heavier battery drain during an event, say 20–30% in a few hours, if there's a clear "event mode" toggle that switches itself off. I'd accept low-resolution images. Storage is the hard part, because my phone is already full, so it needs an auto-delete setting.

What I won't accept is "works only when the screen is on and the app is in front" without telling me. Be honest about the limits and I'll explain them to my friends.

### Follow-up 8. Without video, what happens to the anthem clips and reels the movement ran on? Is there any form, like a short low-quality clip, audio only, or a signed still with a link, that would still carry what those videos did for people? Or is a movement without video simply not interested?

Video was what made people feel the movement, not what informed them. The anthem clips made people feel they weren't alone. A still image doesn't do that. So without any video, organisers will see BuzzChat as a utility, not a home. Maybe that's fine, as long as you're honest about it.

But audio would carry more than you'd expect. Ten or fifteen seconds of a crowd singing, low bitrate, is tiny and still gives you goosebumps. And it's safer, because there are no faces. A signed still plus a short audio clip feels almost like a reel to people standing in that crowd.

A very short, very low-quality clip, a few seconds with faces blurred automatically, would also help. Mostly as proof: "this is happening now, here, signed".

The big reels belong on Instagram, and that's okay. What I'd want is for BuzzChat to sign the original, so when someone reposts an edited or AI version, I can prove the difference. Honestly, that's the video problem I care about most.
