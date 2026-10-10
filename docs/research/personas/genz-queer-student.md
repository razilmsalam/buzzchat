# Persona: Ritu (fictional)

> **Note**
> Simulated persona for design research. Not a real person; quotes are synthetic. Validate with real, consenting participants before relying on these findings.

A designer who needs her posters to travel and her identity to stay put. She wants BuzzChat to be a trustworthy, boring-looking "clipboard that works when the network doesn't": verified notices plus expiring volunteer boards in Marathi, not another social network. She will uninstall it the same evening if it outs her, drains her battery or opens to an empty board.

## At a glance

| Attribute | Detail |
| --- | --- |
| Age | 22 (she/they) |
| Region | Pune, where she lives in a shared hostel with three roommates. Her family lives in a smaller town in Marathwada. |
| Languages | Marathi, Hindi and English. Adds alt text and Marathi and Hindi versions to everything she makes, "because otherwise half of Pune just scrolls past". |
| Role | Final-year communication design student on an education loan, freelancing logos, wedding invite templates and café menus for about 6-8k a month. Makes open-source protest poster kits for the youth movement. Queer, out to friends but not to her family. |
| Shutdown history | At the Pune solidarity sit-in the network was so congested that no story would load, and volunteer coordination fell back to shouting and a paper list. A forged "permission cancelled" notice on a fake letterhead circulated the night before and cut turnout. She heard about the mobile internet cut at the Delhi march from friends; she was not there. |
| Devices | A four-year-old budget Android (about 13k new) with 64 GB of storage that is always full and a dying battery: 20% by 4 pm, so she carries a power bank "like a kidney". Designs on college lab PCs in Illustrator and finishes on her phone in Canva. Apps: Instagram (public design account plus private finsta), WhatsApp (family, class and volunteer groups), X (lurking), Telegram, Discord, and Signal with about four friends. |
| Connectivity | Prepaid plan of about 300 rupees a month with 1.5 GB a day, which runs out by evening if she posts reels. Her budget for BuzzChat is about 5% battery per protest day and 100-150 MB of storage. |
| Literacy / voice preference | Fully literate, visually fluent and comfortable with apps, but rejects jargon and "terminal from 2009" privacy-app looks. Her younger cousin and relatives are Marathi-first and will give an app 30 seconds. |
| Security posture | Fears being outed or having her face morphed through crowd footage, her finsta, group names or notifications, with the damage landing at home, more than she fears the police. Stands at the edges of crowds near people she knows, wears a mask "for pollution", and skips some night protests. Verifies by checking official reposts, asking a friend in the core group and reverse-searching images. |
| Main goals | Get her work to travel without her name or metadata on it, check "official" notices in the moment, replace the paper list and 40 WhatsApp groups with something that works under congestion, and keep anything on her phone from outing her to her family. |

## Key problems

| Problem | Severity | Quote |
| --- | --- | --- |
| Forged "official" notices on fake letterheads cannot be checked in the moment. Visual authenticity cues (stamps, letterheads) are trivially faked, and provenance is lost to "forwarded many times". Even unresolved, the notice suppressed turnout. | Critical | "To trust it, I'd have needed to see where it started, not just 'forwarded'. A pretty stamp wouldn't help, because I can fake a stamp in Illustrator in ten minutes. What I needed was a checkable mark from the organisers saying 'we've seen this notice, and it's real' or 'it's fake', with a time on it." |
| Getting outed to her family through what is visible on the phone (notifications, gallery, group names, account switchers) matters more to her than state surveillance. BuzzChat currently shows sender and message previews in notifications whenever the phone is unlocked (VISIBILITY_PRIVATE only hides them on the lock screen), and the launcher name and icon are fixed. | Critical | "He picks it up saying 'show me your hostel photos' and goes straight to the gallery. Then he sees a notification and asks 'who is this?' The gallery and notifications are where I'd get caught." |
| The Bluetooth beacon makes the app detectable by anyone nearby, which turns a checkpoint, hostel or family visit into a phone-check risk. BuzzChat does not make the app look neutral. | Critical | "'Anyone scanning Bluetooth can tell the app is running.' So at a checkpoint, or in my hostel, someone could know I have it? That's a phone-check risk. Can it at least look boring?" |
| Morphed and deepfaked images target women and queer people, and crowd images carry faces and metadata. Mesh images are re-encoded (which drops EXIF as a side effect), but there is no face blurring and no clear "metadata removed" promise she can see. | High | "My fear is that someone screenshots me from a crowd video, finds my finsta, and morphs my face onto something, or just posts 'is she lesbian or what' in my hometown's Facebook group. Once that reaches my parents, it's over. Not legally. At home." |
| A movement signing key held by one or a few people is a single point of failure. Lost phones, burnout and misuse are realistic. She expects multi-signature for major notices, rotation, visible revocation, and warnings on old notices. | High | "A seal that can never be taken back is basically a crown, and we're protesting exactly that kind of thing." |
| Volunteer coordination is spread across dozens of WhatsApp groups, burns out a few admins, and falls back to paper and shouting when the network is congested. | High | "Volunteer coordination was 40 WhatsApp groups with the same five people admin in all of them, and those five people burnt out." |
| The open area board can be abused by fake accounts ("meet at X") and by harassers. She sees no moderation model and has no way into a safe space that doesn't make her reveal who she is. | High | "If a fake account can post 'meet at X' in the area board, who moderates? Nepal's Discord showed that open spaces get messy." |
| There is no Marathi locale (values-hi exists, values-mr does not), and the UI uses jargon. Low-literacy, Marathi-first relatives will drop off, and she goes with them. | High | "A clunky, English-only interface. My cousin won't use it, and then neither will I." |
| Battery, storage and data budgets are extremely tight. A background mesh service that shows up in battery stats, notification noise or large updates means an uninstall that day. | High | "Honestly, I'd give it about 5% battery for the whole day and maybe 100–150 MB of storage. I'd have to delete reference screenshots even for that." |
| Cold start and empty network: a board with nobody on it is the worst outcome. The mesh only has value with crowd density. | High | "Opening it and finding nobody there, an empty board. That's the worst one. Then why carry it?" |
| Deleting data properly is impossible across apps. Residue survives in media folders, cloud backups and account switchers. She wants one honest "everything we keep, remove it" control. | Medium | "Every app hides things in five different places. One clear button that says 'this is everything we keep, remove it' would be a gift." |
| She wants to prove privately that she made her work (portfolio, anti-plagiarism) without public attribution, and fears both style impersonation spreading false information and being forced to prove authorship. | Medium | "One is a private mark that only I can prove later, maybe to an employer or to the core group, and only when I choose to. The other is a way to show a poster is genuine without showing who made it. But if something can be proven, someone could force me to prove it." |
| Nostr reach is negligible for her audience, so "share to internet" is not a motivator. The culture is video-first and the mesh cannot carry video. | Medium | "Nobody I know is on Nostr. If my post goes there and ten people see it, why would I bother? Instagram is where the audience is, sadly." |
| Per-copy QR codes or location-tagged landing pages can track where a poster was put up and who put it there. | Medium | "Whoever printed it on that wall in that hostel shouldn't be findable through the code. The poster is already physically in a place, and the code shouldn't add anything to that. One code for everyone, the same everywhere." |

## Needs

- A verified-notice seal that a non-technical person understands in one line, with clear states: genuine, revoked as of a date, or unsigned.
- Organisers can countersign or debunk a circulating notice, with a timestamp ("we've seen this, it's real / fake").
- Threshold signing (for example 2 of N) for high-impact notices such as cancellations or venue changes, plus a rotating, diverse keyholder group and public, auditable revocation.
- Expiring area notices with volunteer templates (needs, shifts, headcounts, "female medic needed") in Marathi, Hindi and English.
- No phone number, no real name, no contact-list upload, no profile picture by default.
- Automatic metadata stripping, optional face blurring before sharing, and a visible confirmation that both happened.
- Neutral app name and icon (she would go as far as a disguise; see design implication 1), no message previews in notifications even when the phone is unlocked, and nothing saved to the gallery.
- One-tap "show everything we keep / wipe it" that actually covers media caches and backups.
- Invite-only, vouch-in-person entry to safety channels: boring name, hidden member list, silent leave, practical-purpose framing.
- Report, mute and block for harassment on the area board and in private messages.
- Accessibility: screen reader labels, high contrast, large text and touch targets, correct Devanagari rendering, icons that work for low-literacy users.
- Battery use in the low single digits per day, small install size, works on old Android, small or optional updates.
- A browser landing page from the poster QR: calm, trilingual, seal plus expiry, no login, no location prompt, no tracking, the same code for every copy.
- Optional private proof of authorship for her own designs that only she can reveal.
- Pre-seeded example content and a Marathi-first onboarding, so first launch never shows an empty, confusing screen.
- Shareable, culturally relevant assets (a sticker pack) to help it spread person to person.

## Deal-breakers

- Anything that reveals who she is or where she is to people physically around her (Bluetooth presence, nicknames, rough origin of posts).
- Being outed through a contact list, profile picture, group name or notification preview.
- An app that looks obviously activist (name, icon, dark "hacker" look) to a parent or warden checking the phone.
- Fake "official" posts that look identical to real ones, or a seal whose meaning is unclear.
- An English-only or jargon-heavy interface ("mesh", "relay", "Nostr").
- Appearing at the top of the battery usage screen, notification spam, "always-on" location requests, or large updates over mobile data.
- Opening it to an empty board with nobody around.
- Per-copy or location-tracking QR codes that could point back to the person who put up the poster or to the wall it was on.

## How BuzzChat fits

| Feature | Verdict | Note |
| --- | --- | --- |
| Movement signing key / official seal mirrored on Nostr | Partly | This is directly her top excitement and answers the forged-letterhead problem. As described, though, it is a single key. She needs threshold or multi-signature signing, rotation, visible revocation that flags previously signed notices, and an organiser "attest or debunk this circulating notice" action, because the notice that hurt her was a forwarded image, not a native post. Without one-line plain-language explanations, the seal turns into another stamp people can't read. |
| Area bulletin board with expiring notices and volunteer templates | Partly | Exactly the "clipboard" she wants to replace the paper list and the 40 WhatsApp groups. The gaps are serious: no Marathi locale exists in the app today, there is no moderation, reporting or rate-limiting model against fake "meet at X" posts, and its value disappears on an empty board or in a sparse crowd. Templates need gender-sensitive needs (female medic, walk-to-bus buddy). |
| No account, no phone number, browser join via QR or link | Solves | Huge for her, since her number is tied to Aadhaar, her bank and her family. The poster-as-entry-point fits her craft. The landing page must be calm, trilingual, free of tracking, with one identical QR for all copies, no location prompt, and no app branding splash. Browser-only users depend on a nearby hub phone, which limits it at the edges of a crowd, where she actually stands. |
| Bluetooth/Wi-Fi phone-to-phone mesh | Risk | Detectability of the BLE advertisement directly hits her deal-breaker of being identifiable to people around her and creates hostel and checkpoint phone-check risk. It needs crowd density she may not have at the edges. It also runs as a background service on a phone with a dying battery and a 5% daily budget, and showing up in battery stats means an uninstall that same day. |
| End-to-end encrypted private messages | Partly | Encryption protects content in transit, but her threat is a family member with the unlocked phone in hand. Current notifications show sender nickname and message text whenever the phone is unlocked (VISIBILITY_PRIVATE only hides them on the lock screen), and the launcher name and icon are fixed. She needs an option for no previews, a disguised icon and name, and no media written to the gallery. |
| Panic wipe | Partly | A panic wipe of identity and mesh state exists in the codebase, which matches her "wipe it" need. It is hidden behind a non-obvious gesture, though, and she asked for a clear "this is everything we keep, remove it" screen that she can trust covers cached media and any exported files. |
| Small images over mesh, metadata handling | Partly | Images are downscaled and re-encoded as JPEG, which drops EXIF as a side effect, but this is not presented to the user as a guarantee, and there is no face blurring. Her top must-have is automatic EXIF stripping plus face blurring before sharing, because morphing of women and queer people is her scariest threat. |
| Queue and publish to Nostr via Tor ("share to internet") | Gap | Technically protects authors, but offers her no audience motivation. "Uncensorable Nostr" means nothing to her friends and the reach is tiny. Its real value for her is as a durable verification mirror for sealed notices, not as a broadcast channel. Position it that way. |
| Video over mesh | Gap | Movement culture is video-first and BuzzChat cannot carry reels. She accepts this only if BuzzChat is positioned as "notices and posters", with Instagram remaining the shouting channel. |
| Animated QR screen-to-camera transfer | Gap | She is sceptical it works in harsh sun with a cracked screen and 15% battery. At best a rare fallback, not a selling point. |
| Women/queer-only volunteer channel or "safe person nearby" | Gap | Not in the concept. She specifies an in-person vouch-code invite, boring name, hidden member list, silent leave, practical focus, and nothing in the channel that outs anyone if it leaks. The current model of public area chat plus E2E private messages does not provide this. |
| Private provenance or authorship proof for creators | Gap | She wants a mark only she can prove, revealed only when she chooses, separate from authenticity. It is not offered, and any design must take into account that someone could force her to prove authorship. |
| Accessibility and localisation | Gap | No Marathi locale. Hindi exists. The app's terminal-like heritage risks the "terminal from 2009" look she rejects. She needs screen reader labels, high contrast, large touch targets, Devanagari rendering, and jargon-free icons and copy from the very first screen. |

## Design implications

1. Ship one neutral, honest launcher name and icon (not `bitchat`, not protest-branded), a neutral light theme, and an app home that is unremarkable if opened casually. She asked for an icon that looks like a notes or study app; the first study rejects disguise modes, and [GENZ_RESEARCH_SYNTHESIS](../GENZ_RESEARCH_SYNTHESIS.md), tension 1, keeps that rule until real participants and local counsel have been consulted.
2. Add a notification privacy level that hides sender and content even when the phone is unlocked ("New update"), and a "quiet" mode where area-board posts never buzz and only direct mentions or "check the board" pings do.
3. Never write received or sent media to shared storage or the gallery. Keep it in app-private encrypted storage and say so in the UI.
4. Replace the hidden panic gesture with a discoverable "Everything BuzzChat keeps" screen listing identity, messages, media cache, keys and queued posts, with one wipe button and a plain confirmation of what was removed.
5. Make metadata stripping an explicit, visible guarantee on image share ("Location and camera data removed") and add optional on-device face blurring before an image leaves the phone.
6. Design the seal as threshold signatures (for example 2 of 5) with rotating keyholders, public revocation records ("this seal stopped being valid on <date>"), automatic warnings on notices signed by a revoked key, and an append-only misuse log. Document this in the protocol spec and golden-vector tests.
7. Add an organiser "attest / debunk" action that signs a hash or short description of a circulating forwarded notice with a timestamp, so people can check an image they received elsewhere against it.
8. Write seal copy for a non-technical person in a single line in Marathi, Hindi and English, for example "Signed by the organisers. Cannot be copied.", with distinct visual states for genuine, revoked, and unsigned.
9. Add values-mr (Marathi) and make Marathi, Hindi and English board templates (water, medic, female medic, shift with headcount, walk-with-me) first-class, with icon-led, low-literacy-friendly designs.
10. Use the system locale on first launch with no language picker wall. The first screen shows one line ("No number, no name. Notices for people nearby.") and a pre-rendered example notice so it never looks empty.
11. Strip mesh jargon from the UI: no "relay", "mesh", "Nostr", "peer" in user-facing strings.
12. Build the QR landing page as a static, trilingual, accessible page: seal plus expiry, no login, no location permission, no analytics, no branded splash, the same URL for every printed copy, and a small "get the app" link.
13. Area board moderation: per-key rate limits, local mute and block, community flagging, sealed posts pinned above unsealed ones, and clear "unverified" styling on location-instruction posts such as "meet at X".
14. Safety channels: in-person vouch QR invites, a neutral channel name, hidden member list, silent join and leave, no persistent history by default, and a practical-help framing.
15. Minimise how much the Bluetooth beacon reveals and offer a "mesh only when I open the app" mode with a scheduled off-switch, being honest that while it is advertising it is detectable.
16. Set a battery and storage budget as a release gate: under about 5% per active protest day on an older mid-range device, a small APK, and incremental updates only. Allow sideloading from a friend's phone so there is no update over mobile data.
17. Reposition the product: "the clipboard that works when the network doesn't" (verified notices plus volunteer boards), not a social network. Instagram stays the reach channel and Nostr is framed as a verification mirror, not as an audience.
18. Offer optional private authorship proof (a creator's commitment embedded in a poster, which only they can open later), off by default, explaining the risk that someone could force them to prove it.
19. Seed adoption through volunteer groups and printed posters, with a culturally relevant sticker pack as a share hook, and a "share app from my phone" flow that works fully offline.

## Interview transcript

### Q1. Who you are

I'm Ritu, 22. I'm in my final year of a communication design degree in Pune and I live in a shared hostel with three roommates. My fees come from an education loan, and my father reminds me about it every Sunday on the call, very sweetly. I freelance with logos, wedding invite templates and the occasional café menu. That brings in maybe 6–8k a month in a good month. My family is in a smaller town in Marathwada. They think I'm "doing posters for college". My friends know I'm queer. My family doesn't, and that gap decides a lot of my choices.

I didn't join the movement for the jokes. My younger cousin was preparing for NEET, and when the paper was voided she stopped answering anyone's calls for a week. Then there was the "cockroaches and parasites" remark. Whatever was later said about being misquoted, all of us heard it as being about us. Being unemployed, or about to be, isn't laziness. So I started making posters. Design is the one thing I can actually give.

### Q2. Your phone and online life

I have a four-year-old Android that cost about 13k when new. It has 64 GB, and it's always full because Illustrator exports and reference screenshots eat everything. The battery is dying, so by 4 pm I'm at 20% and I carry a power bank like a kidney. I'm on a prepaid plan of around 300 rupees a month with 1.5 GB a day, which runs out by evening if I'm posting reels.

Instagram is my main thing, with a public design account and a private finsta. On WhatsApp I have family groups, class groups and volunteer groups. I use X mostly to lurk. Telegram is for downloading fonts (don't tell). I only joined Discord because people said the movement was there. Signal I use with maybe four friends. I make posters in Illustrator on the college lab PCs and finish them on my phone in Canva. Reels are mostly other people filming and me doing the type and captions. I always add alt text and Marathi and Hindi versions, because otherwise half of Pune just scrolls past.

### Q3. The movement

Online it moved fast: Instagram first, everything else second. Templates spread well. I put out an open poster kit with editable files, and I saw it remixed in cities I've never been to. That part felt amazing.

What broke: when the X account was withheld, people shared screenshots of a "new official handle" before the real one came back, and for a few hours no one knew which was which. In WhatsApp groups, forwarded "permission cancelled" notices kept appearing on letterheads that looked official. Some were real and some weren't. I still don't know about one of them. At the Pune solidarity sit-in the network was so slow you couldn't load a single story, and volunteer coordination fell back to someone shouting and a paper list. For the Delhi march I heard about the mobile internet being cut from friends who went. I wasn't there.

Volunteer coordination was 40 WhatsApp groups with the same five people admin in all of them, and those five people burnt out.

### Q4. Safety and fear

Honestly? Police are not my top fear. My fear is that someone screenshots me from a crowd video, finds my finsta, and morphs my face onto something, or just posts "is she lesbian or what" in my hometown's Facebook group. Once that reaches my parents, it's over. Not legally. At home.

I don't go to every protest. Some crowds, especially at night, felt unsafe. There were men "joining the cause" who were mostly there to film girls. I stand at the edges near people I know. I wear a mask and say it's for pollution.

Satire also worries me, because legal cases for "hurting sentiments" over a meme are a real thing. My college has already sent one mail about "students' social media conduct". If my phone got checked, I'd want the finsta gone, the queer group chats gone and the location history gone, and I'm not even sure I know how to fully delete half of it.

### Q5. Information problems

I've seen AI images of "massive crowds" that were clearly fake. Our side shared some too, which hurt our credibility. I've also seen deepfake-ish clips of women activists, and that's the scariest form of misinformation for me personally, because it's always women and queer people who get morphed.

How I verify: I check whether the official account reposted it, I ask the one friend who's in the core volunteer group, and I reverse-search images. It's slow. In the actual moment, nobody verifies anything.

The tension is real. The movement needs faces and reach. I need to not be a face. I want my poster to travel without my name or my metadata on it.

### Q6. Reaction to BuzzChat

*(Interviewer explained the concept in plain words.)*

What excites me:
- The seal for official posts, a hundred percent. If every "official" notice had a mark you can check, the forged-letterhead problem gets much smaller.
- The bulletin board with expiring notices: "need water at gate 2", "need a female medic", "shift 4–6 pm, 3 more people". That's exactly what our paper list was trying to be, and it expires instead of living forever in someone's gallery.
- No phone number. That's huge for me. My number is linked to my Aadhaar, my bank, my family.
- QR on a poster, opened in the browser. As a designer, I love that the poster itself becomes the entry point.

What feels cringe or useless:
- "Uncensorable Nostr network" means nothing to my friends. Nobody I know is on Nostr. If my post goes there and ten people see it, why would I bother? Instagram is where the audience is, sadly.
- No reels over Bluetooth. Fair, but our whole culture is video. You'd have to sell it as "notices and posters", not as "social media".
- Animated QR screen-to-camera sounds cool in a demo. At an actual sit-in in the sun, with a cracked screen and 15% battery? I'm doubtful.

What confuses or scares me:
- "Anyone scanning Bluetooth can tell the app is running." So at a checkpoint, or in my hostel, someone could know I have it? That's a phone-check risk. Can it at least look boring?
- Public posts hopping through strangers' phones: can someone nearby see who posted, or roughly where from? If a fake account can post "meet at X" in the area board, who moderates? Nepal's Discord showed that open spaces get messy.
- Does it eat battery? My phone can't afford another background app.
- Is the browser version accessible: screen readers, large text, Marathi script rendering? Most "privacy" apps look like a terminal from 2009.

Would I use it? For the bulletin board and verified notices at a protest, yes, if my volunteer group used it. Would my friends? Only if it's dead simple and someone they trust says "install from my phone". It will spread through volunteers and poster QR codes, not through ads. A good sticker pack would help, honestly.

### Q7. Must-haves and deal-breakers

**Must-haves, in order:**
1. No phone number or real name, and no images with metadata. Strip EXIF automatically and blur faces before sharing.
2. Verified official seal, explained in one line that a non-tech person understands.
3. A quick way to hide or wipe the app, with a neutral icon and name.
4. Expiring area notices with volunteer templates in Marathi, Hindi and English.
5. Accessibility: screen reader labels, high contrast, big touch targets, low-literacy-friendly icons.
6. A way to report or mute harassment, and something like a "safe person nearby" or women/queer-only volunteer channel.
7. Low battery and storage use, and it has to work on old phones.

**Deal-breakers:**
- Anything that leaks who I am or where I am to people around me.
- Getting outed through a contact list, a profile picture or a group name showing up.
- The app looking obviously "activist" to a parent or warden checking my phone.
- Fake "official" posts that look the same as real ones.
- A clunky, English-only interface. My cousin won't use it, and then neither will I.

## Follow-up interview

### Follow-up 1. You said you want your poster to travel without your name on it, and you also put out an open kit that got remixed in cities you've never been to. When a poster of yours travels, is there any part of you that wants it to be traceable back to you, even privately, like a signature only certain people can see? Can you tell me about a time you felt that pull either way?

Yes, and I'm a bit embarrassed by it. A few weeks after I put out the kit, I saw a remix from another city doing the rounds. The type, the colour palette and even my little cockroach doodle were all mine. A guy had posted it with "design by me" and got hundreds of likes. I felt a sting. I'm a design student with a loan, and my portfolio is my future. Then I asked myself what I'd actually want. My name on it? Never.

There's a scarier side too. Someone could take my style and put fake information on it, like a wrong date or a wrong gate, and people would trust it because it looks like "our" posters. So I'd want two separate things. One is a private mark that only I can prove later, maybe to an employer or to the core group, and only when I choose to. The other is a way to show a poster is genuine without showing who made it. But if something can be proven, someone could force me to prove it. Honestly, I haven't worked out where that line is.

### Follow-up 2. Walk me through that one forwarded 'permission cancelled' notice you still aren't sure about. What did you do when you saw it, what did your friends do, and what would have had to be on that notice in that moment for you to trust it or dismiss it?

It came the night before the Pune sit-in, around 10 pm, in a volunteer group, marked "forwarded many times". It was a letterhead with a round stamp and a line about permission being withdrawn for "law and order reasons". I zoomed in like a designer. The stamp edges looked too clean and the date format was odd, but that proves nothing. Real government documents are badly designed too.

I messaged my friend in the core group. No reply for two hours, because she was in eleven other chats. One roommate said "just don't go, why risk it." Another friend said it was fake and meant to scare us. Someone posted a lawyer's number. Nobody actually knew anything.

In the end I went late and stood at the edge. Nothing happened, but turnout was clearly lower, so either way the notice did its job.

To trust it, I'd have needed to see where it started, not just "forwarded". A pretty stamp wouldn't help, because I can fake a stamp in Illustrator in ten minutes. What I needed was a checkable mark from the organisers saying "we've seen this notice, and it's real" or "it's fake", with a time on it.

### Follow-up 3. For the seal to mean something, someone has to decide who counts as 'official'. In your movement, who would you trust to hold that, and how would you feel if the same five burnt-out admins held it? What should happen if that key gets lost, stolen, or misused?

Those five did earn trust. They showed up when nobody else did. But one of them lost his phone on a local train during the busiest week, and we spent two days not knowing who had his WhatsApp. So I don't want them carrying this alone. It isn't fair to them, and it isn't safe for us.

I'd want a small group that rotates, drawn from different cities, with at least one woman and one queer person in it. That's not tokenism. We're the ones who notice when "official" messages are being used against certain people. Big notices, like a cancellation or a change of venue, should need two of them to sign, not one.

If a key is lost or stolen, the others should be able to retire it, and everyone's app should show plainly: "this seal stopped being valid on this date". Old notices it signed should get a warning. If it's misused, that has to be visible too, with no quiet fixing. A seal that can never be taken back is basically a crown, and we're protesting exactly that kind of thing.

### Follow-up 4. You asked for a women/queer-only volunteer channel or a 'safe person nearby', and you also said a deal-breaker is anything that reveals who you are to people around you. How would you want someone to get into that space without having to out themselves to get in?

Nobody should have to declare anything to get in. No checkbox saying "I am queer", no form. The way in should be someone already inside vouching for you, in person: they show you a code from their phone and don't ask why you want it. That's how trust already works for us anyway.

One group handled this badly. It was a queer WhatsApp group where the admin added people by phone number, and the group name popped up in someone's notifications. Her brother saw it. That was it for her.

One handled it well. At a college fest, the women's volunteer desk was just a tent with a purple ribbon. You walked over, nobody asked your story, and you got help or got a task.

So the channel should have a boring name. Nobody should be able to see the member list. Leaving shouldn't notify anyone. It should be for practical things like "walk with me to the bus stop", not for identity. Some creep will eventually get in, I know that, so the space shouldn't hold anything that outs a person if it leaks.

### Follow-up 5. Picture your warden, or your father on a visit, picking up your phone. What exactly do they open first, and what would they need to see, or not see, for the app to pass as boring? And when you've had to hide or delete things before, what did you actually manage to do and what did you give up on?

My warden doesn't really check. My father does, but not like a detective. He picks it up saying "show me your hostel photos" and goes straight to the gallery. Then he sees a notification and asks "who is this?" The gallery and notifications are where I'd get caught.

So the app needs a neutral name and icon, no message previews in notifications, and nothing it saves showing up in my gallery. If he does open it, it should look like a notes or study app, not a black screen with a red logo.

Here's what I actually managed before. I archived the finsta and logged out, but Instagram's account switcher still showed the username. I moved some photos into a locked folder and forgot that Google Photos had already backed them up. I left one queer group chat, but its photos were still sitting in my WhatsApp media folder. And I gave up on location history completely because I couldn't find where it lives. Every app hides things in five different places. One clear button that says "this is everything we keep, remove it" would be a gift.

### Follow-up 6. You love the QR on the poster as an entry point, but you also want the app to look boring. If someone scans your poster's QR on a hostel wall, what should they land on, and what should a stranger standing next to them be able to tell? Should that path ever lead back to the poster's designer or to the place where it was put up?

They should land on a plain, calm page with the notice text in Marathi, Hindi and English, the seal if it has one, an expiry time, and a small "get the app" link. No login, no "allow location", no flashing banner. If my warden scans it out of curiosity, he should just think "oh, some notice".

Someone standing next to them sees a person reading a webpage, nothing more. No sound, no splash screen with the app name in giant letters.

It should never lead back to me unless I chose to sign it. And it definitely shouldn't lead back to the wall. As a designer, I know the trick of putting a different QR on each copy to count scans by location. It's useful for marketing, but here it's tracking. Whoever printed it on that wall in that hostel shouldn't be findable through the code. The poster is already physically in a place, and the code shouldn't add anything to that. One code for everyone, the same everywhere.

### Follow-up 7. Your phone is full, dies by 4 pm, and your data runs out by evening. Think of the last protest day you were at. At what point would you have opened BuzzChat, how much battery and space would you honestly have given it, and what would make you uninstall it the same evening?

The Pune sit-in: I left the hostel around 11 at 78%. By 2 pm I was at 35% because I'd kept trying to load stories that never loaded. I'd have opened BuzzChat on the auto ride to check which gate to use, and again on arrival for the "need water / need volunteers" board. After that, only when someone said "check the board".

Honestly, I'd give it about 5% battery for the whole day and maybe 100–150 MB of storage. I'd have to delete reference screenshots even for that.

What would get it uninstalled that same evening:
- It showing up at the top of my battery usage screen.
- Notification spam, especially a buzz every time a stranger posts.
- Asking for location "all the time".
- Opening it and finding nobody there, an empty board. That's the worst one. Then why carry it?
- A big update download over my 1.5 GB.

If it quietly helped twice in one day and didn't bother me otherwise, I'd keep it.

### Follow-up 8. You said your friends will only join if someone they trust says 'install from my phone', and that Instagram is where the audience is. If BuzzChat stayed small, mainly volunteers and notices, while the posters and reels kept living on Instagram, would that still feel worth it to you? And what would your cousin need to see in the first 30 seconds, in Marathi, to stay instead of giving up?

Yes, honestly, that would be enough. I don't need another Instagram. I need the thing our paper list and our 40 WhatsApp groups were failing to be. Instagram can be the shouting, and BuzzChat can be the clipboard that works when the network doesn't. Small and trusted beats big and full of rumours. If even a few volunteer groups actually used it, it would have paid for itself at that sit-in.

My cousin would give it 30 seconds, maybe less. She'd need:
- Marathi from the very first screen. The phone already knows she uses Marathi, so don't make her scroll through 40 languages.
- One line she gets instantly, something like "नंबर नको, नाव नको. जवळच्या लोकांसाठी सूचना." (No number, no name. Notices for people nearby.)
- One example notice already on screen, like "गेट २ जवळ पाणी हवं आहे" (Water needed near gate 2), so she can see what it's for.
- Big buttons, no signup, no English words like "relay" or "mesh".

Add a cute sticker pack and she'll show it to her friends.
