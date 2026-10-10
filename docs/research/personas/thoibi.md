# Persona: Thoibi (fictional)

> **Note:** Simulated persona for design research. Not a real person; quotes are synthetic. Validate with real, consenting participants before relying on these findings.

**In one line:** She wants something plain and dependable that sends an exact supply list from phone to phone with no data and posts camp needs that expire. She also says BuzzChat as described is a "rumour machine" (anonymous, can't be stopped, can't be checked, and author-hidden when it reaches the internet), and that its mesh probably can't cross the buffer zones where she most needs coordination.

Thoibi (fictional, synthetic), 31, is an emergency-ward staff nurse who works night shifts, and a volunteer supply coordinator for relief camps in Imphal, Manipur. She speaks Meiteilon and English. Her salary and her brother's shop income support a household of five. During the 2023 mobile internet ban she ran medicine and supply lists for relief camps, first on paper and then with scooter couriers. She has joined peaceful candlelight sit-ins but does not see herself as a protester. She trusts about ten people. Her biggest concern is rumours and misinformation fuelling violence, and close behind it is gendered, community-based social targeting.

> **Perspective note:** Thoibi's account reflects one community's experience of the Manipur conflict. It deliberately does not name or characterise "the other community", and it must not be read as a balanced account of events. A persona and real participants from the other side are listed under *Missing perspectives* in the synthesis and are needed before any design decision for Manipur.

## At a glance

| Attribute | Detail |
| --- | --- |
| Age | 31 |
| Region | Imphal, Manipur (North-East India); communities now live apart, separated by buffer zones |
| Languages | Meiteilon and English. Types English or Romanised Meiteilon; some young people use Meetei Mayek, which many keyboards handle badly |
| Role | Emergency-ward staff nurse, mostly night shifts; volunteer supply coordinator for relief camps (insulin, ORS, sanitary pads, baby formula, BP tablets). Has joined peaceful candlelight sit-ins but does not call herself a protester |
| Devices | One shared Android phone, Redmi-class, about ₹13,000, 4 GB RAM / 64 GB storage that is always full (WhatsApp alone about 20 GB). Old battery at about 30% by evening; carries a cheap power bank. Her six-year-old niece watches YouTube on it and her mother (Ima) makes video calls on it: "the house phone that happens to sit in my pocket." Apps: WhatsApp, Facebook, PhonePe, YouTube, Google Sheets |
| Connectivity | Jio prepaid plan, about ₹300 a month. Lost mobile data for months in the 2023 ban; SMS and calls worked off and on; broadband only at a few offices with queues. Charged at the hospital during power cuts |
| Literacy / voice preference | Fully literate, comfortable with spreadsheets. Her mother and most camp aunties cannot type and use only voice notes, so any tool for the camps must be voice-first and work in local scripts |

## Key problems

| Problem | Severity | Quote |
| --- | --- | --- |
| Rumours and fake media spread with nothing to check them against. BuzzChat's anonymous, undeletable, author-hidden posts would make this worse exactly when no other source of truth exists. | Critical | "you're building a machine that spreads anonymous posts, unstoppable and unverifiable, and it's strongest exactly when there's no other source of truth. That's the rumour machine." |
| Without data she could not send supply lists accurately. Reading them out over a call caused dangerous transcription errors in drug doses. | Critical | "I read out forty medicine names to a pharmacist, he wrote down "Metformin 500" as "Metformin 50," and we found out only when the stock came." |
| Camps went silent the moment the ban started, so urgent shortages such as insulin could not be reported. | Critical | "One camp told me later that by the afternoon they were out of insulin and had no way to let me know." |
| The coordination she needs most is across communities, but the communities are physically separated by buffer zones a Bluetooth mesh cannot cross. | Critical | "Bluetooth won't hop across a zone where nobody can stand. So the cross-community coordination I most want is the one thing this probably can't do." |
| Physical courier runs were expensive and dangerous, and a volunteer quit because of them. | High | "One volunteer quit because her family said the scooter runs were too dangerous for a girl, and honestly I didn't blame them." |
| Social and gendered targeting: working across communities can be screenshotted and turned into "traitor" accusations and attacks on her character. | High | "someone could screenshot it and call me a traitor. Women get it worse. They go after your character and your family" |
| Checkpoint visibility: a Bluetooth-detectable app, an unusual icon, or a visible wipe action marks the user. | High | "if anyone scanning can tell the app is running, then at some checkpoint it marks me. And an emergency wipe looks suspicious if someone sees you press it." |
| Crowd-voting to fade "false" posts would bury true but unwelcome information in a divided place. | High | "In a divided place, "many people mark it false" can mean "many people from one side don't like it."" |
| No single authority is trusted by both sides to hand out verification marks. | High | "I'm not sure anyone in Imphal is trusted by everyone right now." |
| Voice notes, the main channel for aunties, feel real and get forwarded with no clue who first spoke. | High | "Voice notes are more dangerous, because they feel more real. You hear a frightened woman's voice and your body believes it before your head does." |
| A language barrier would keep camps on paper if the tool only works in English and only for young people. | High | "Something only English-speaking young people can use. Then the camps stay on paper, and I stay on the scooter." |
| On a shared house phone, private chats and notices from the other side can leak to family and then to the neighbourhood. | Medium | "she'd tell my aunt "just so someone knows," my aunt would tell someone else, and then it's in the locality. Love leaks too." |
| She cannot spare much storage or battery on a full, old, low-end phone. | Medium | "Works on a cheap, full, shared phone without killing the battery. I can't give it 2 GB." |
| The concept itself confuses her: she does not understand Tor and can't tell whether a browser-only phone is connected. | Medium | "The Tor part confuses me. My niece would probably understand it, I don't. And I don't know whether a phone with only the browser link is "in" or not." |

## Needs

- Send a text list from one phone to another with no data so that it arrives exactly as typed ("Metformin 500, 3 strips"), with a delivered tick, a read tick, and a "confirmed" reply the recipient can put on the same list
- Setup in under one minute on day one: open the app, show a QR code, the other person scans it, and they are connected. No account, no email, no English explanation of Tor
- Structured needs posts (item, quantity, camp, time) that expire automatically so nobody chases old needs
- Verification at the organisation level (hospital, relief committee): the system knows a real person stands behind the post, but the public does not see which person
- Trust she chooses herself, not a central authority: her ten people vouch for groups, and groups vouch for camps ("a chain of faces")
- Per-post audience: her locality only, or both communities (for example disease-outbreak or vaccination notices under the hospital's name)
- Anonymous posts clearly labelled "unverified"; photos and videos off by default or labelled with where they came from
- Reports go to trusted reviewers, or slow down sharing of photos and videos, instead of hiding posts automatically by crowd vote
- Voice notes that show whether the sender recorded them or forwarded them ("forwarded, original speaker unknown"), ideally with a small transcript
- Meiteilon in Bengali script, Meetei Mayek and Roman letters, plus voice-first use for people who can't type
- Small storage footprint, low battery drain, no media auto-save, no separate photo gallery
- A plain, ordinary look when closed (she said "like a notes app"; the design response is an ordinary messenger that shows nothing when closed, not a disguise, see the synthesis *Tensions*); PIN or fingerprint every time the private circle opens; notices stored separately and also locked; the rest of the phone stays free for family
- Ability to block or report anonymous harassers
- Tools that make one trusted person's job easier and safer when they physically cross between communities with a phone

## Deal-breakers

- Anonymous photos or videos that spread with no "unverified" label and no way to slow them down. She would actively warn people against the app
- An app that makes her phone look suspicious at a checkpoint (strange icon, detectable broadcast, dramatic red panic button)
- Being the only woman in a group where anonymous people can harass her and she cannot block or report them
- An app only English-speaking young people can use, with no voice notes or Meiteilon scripts
- Setup taking more than about a minute while a camp waits for insulin, or an app that drains her battery or wants about 2 GB

## How BuzzChat fits

| Feature | Verdict | Note |
| --- | --- | --- |
| Phone-to-phone chat and posts over Bluetooth/Wi-Fi, no internet or SIM | Partly | Could replace some scooter runs inside one neighbourhood or between a camp and its nearby coordinator when enough people are around. It fails where she needs it most: hops are tens of metres, camps are spread out, and buffer zones have no people to relay through. Across communities it works only if one trusted person physically carries a phone. BuzzChat currently pitches crowd hopping, not that carried-phone model. |
| Join by QR or browser link, no install, no account, no phone number; APK passed between phones | Partly | QR joining suits aunties and fits her one-minute setup rule. But a browser-only phone works only near a hub phone, and she can't tell whether such a phone is "in". Having no accounts also means nothing to verify or block against. A sideloaded app with an unusual icon is exactly what draws "what is this?" at checkpoints. |
| Share to internet via Tor with hidden authorship | Risk | She sees this as amplifying fakes to the whole world with the author hidden. She did not understand the feature and never asked for it. What she wants is the opposite: outward notices carrying the hospital's or organisation's name, not anonymous ones. |
| Area bulletin board with expiring notices | Partly | Her strongest draw: expiry keeps old needs from being chased. But as described the board is free text, with no structured item/quantity/camp/time fields, no verification marks, no labelling of anonymous posts and no choice of audience. Without those, the same board carries anonymous "attack tonight" rumours. |
| End-to-end encrypted private messages | Partly | Meets her must-have #5 (encrypted chat for her circle of ten) on the network side. It does nothing about the real leak, which is family on a shared phone. That needs a PIN or fingerprint on every open, no media auto-save, and a plain appearance. Delivery and read ticks plus a "confirmed" reply on the same list are not described. |
| Animated QR codes passed screen-to-camera when radio is jammed | Partly | Exact-copy transfer fixes the Metformin 500/50 problem, and it could be what a trusted carrier uses at each end of a crossing. Her verdict was "sounds silly, but if it really works camp to camp, I'd use it". It needs people face to face, and reliability on cheap, worn cameras is unproven. |
| Emergency wipe button | Risk | She says pressing a wipe where others can see is itself suspicious, and a dramatic panic button "puts ordinary people at risk without meaning to". She would rather have expiry and no stored media, so there is less to find in the first place. |
| Bluetooth advertising reveals the app is running (stated limit) | Risk | Directly hits deal-breaker #2. Anyone scanning at a checkpoint can tell she runs a protest-associated app, even though she is a nurse who mostly moves supplies. |
| Anonymous public posting with no verification or moderation layer | Risk | Her central objection: strongest when no other source of truth exists. No trust marks she chooses, no labelling of where photos or videos came from, no way to slow them down. Crowd-vote fading would also be harmful, as the missing-man story shows. |
| Voice notes and Meiteilon (Bengali script, Meetei Mayek, Roman) support | Gap | Not in the concept. Without voice notes and local scripts the camps "stay on paper". Forwarded-versus-original labels and transcripts are also missing. |
| Block and report for harassment | Gap | Not described. Having no accounts makes blocking hard. This is her deal-breaker #3 as a woman in mixed anonymous groups. |
| Battery and storage footprint on low-end shared phones | Gap | Constant Bluetooth scanning and relaying for the mesh is likely to drain an old battery that is already at 30% by evening. Nothing commits to a storage budget or to not saving media automatically. |

## Design implications

1. Make "send exact text list, get delivered/read ticks, recipient replies confirmed on the same list" the flagship flow that works on day one, ahead of public posts. Trust grows from that one flow working every time.
2. Add a structured needs template (item, quantity, unit, camp, needed-by) with automatic expiry and a "fulfilled" state on the bulletin board.
3. Add verification marks for organisations, backed by keys from a real person who is not shown publicly. Build trust as a web of trust the user picks (my contacts vouch for groups, groups vouch for camps) rather than a central authority. Never use political, community-named or government bodies as default authorities.
4. Show posts from orgs the user trusts and anonymous "unverified" posts in clearly different styles. She would hide unverified posts by default; other personas want them shown with a label, so test collapsing them behind a clear "unverified" label rather than silently removing them.
5. Media off by default for public posts. If allowed, add a "source unknown/forwarded" label and limit forwarding or slow the spread of media. Never fade text posts automatically by crowd vote. Route reports to reviewers the user trusts, or add friction to sharing, instead of hiding.
6. Add per-post audience: "my locality" vs "both communities", with an org-signed template for public-health notices (outbreak warnings, vaccination camps).
7. Rethink "share to internet" for this setting: make it off by default, require an org signature or an "unverified" label on outbound posts, explain it in one plain sentence in local languages, and never publish anonymous photos or videos outward.
8. Treat voice notes as first-class: record vs forward shown on every note ("forwarded, original speaker unknown"), optional transcription on the device, and UI in Meiteilon (Bengali script and Meetei Mayek) with Roman typing.
9. A discreet, honest presentation: an ordinary messenger name and icon (never disguised as a notes or other app), no red panic button, no app gallery, no media auto-save, expiry as the main way data is minimised. If a wipe exists, it should be a calm, deliberate action in settings that clears only BuzzChat data and says what it cleared, not a dramatic button.
10. Require a PIN or fingerprint each time the private circle and the notices area open, keeping the rest of the shared phone unaffected.
11. Add block, mute and report for anonymous users in groups, plus closed groups by invite only for women and small trusted circles.
12. State in the Manipur onboarding that the mesh will not cross buffer zones, and design for the "trusted carrier": a phone that stores messages and delivers them on reconnection, animated QR batch transfer, and a protected relay mode for medical or humanitarian staff who cross.
13. Make it plain whether a browser-only phone is connected (a clear "connected via hub nearby / not connected" state).
14. Set a battery and storage budget suited to a ₹13,000, 4 GB/64 GB phone: tens of MB, an adaptive Bluetooth duty cycle, and a low-power mode that relays only when charging.
15. Treat Bluetooth detectability as an unsolved risk for this persona: let users turn off relaying or broadcasting when it isn't needed, and be open about what an observer can see.

## Interview transcript

### Q1. Who you are

I'm Thoibi. That's not my real name, and please keep it that way. I'm 31 and a staff nurse at a hospital in Imphal. Mostly I work in the emergency ward, and I do a lot of night shifts. I live with my Ima (mother), my younger brother and his wife, and their small daughter. My father passed some years back, so the household runs on my salary and my brother's shop income.

I don't really think of myself as a protester. People call me a volunteer coordinator. When the violence started in 2023 and the relief camps filled up, nurses who knew how to make lists ended up running supplies: insulin, ORS, sanitary pads, baby formula, BP tablets for old people. Most of my work is spreadsheets, fixing quarrels and counting. I've joined peaceful sit-ins with the women of my locality, the candlelight kind. But mostly I want the people in the camps to get medicine and to stop dying of things nobody should die of.

### Q2. My phone

It's a Redmi-type phone, around ₹13,000, Android. It has 4 GB RAM and 64 GB storage, and the storage is always full. WhatsApp alone is something like 20 GB because of hospital groups, relief groups and family groups. Every month I delete photos and I'm sad about it.

My niece uses it for YouTube cartoons, and Ima uses it to video call my aunt. So it's not really "my" phone. It's the house phone that happens to sit in my pocket.

The battery is old. By evening it's at 30% and I carry a cheap power bank. Power cuts were common in some localities, so we charged at the hospital. Data is a Jio plan, around ₹300 a month.

The apps I use are WhatsApp (everything happens there), Facebook, PhonePe, YouTube and Google Sheets. I type in English, or in Meiteilon written in Roman letters. Ima can't type at all, she only sends voice notes. Most aunties in the camps use voice notes too. Some young people use Meetei Mayek script, but a lot of keyboards handle it badly.

### Q3. My worst shutdown

The mobile internet ban came in May 2023 and went on for months. Some broadband came back later with conditions, but for most people, mobile data was just gone.

On the first day we thought it would last a day or two. All my supply lists were in WhatsApp groups, and suddenly I couldn't reach the camp volunteers or the pharmacy distributor. One camp told me later that by the afternoon they were out of insulin and had no way to let me know.

In the first week we went back to paper. I made a handwritten sheet for each camp listing headcount, children under five, pregnant women, diabetics and medicines needed. Volunteers carried the sheets on scooters. Each run cost petrol money, and some days moving around wasn't safe at all. Normal SMS and calls worked off and on, so we called, but calls cost money and you can't send a list over a call. I read out forty medicine names to a pharmacist, he wrote down "Metformin 500" as "Metformin 50," and we found out only when the stock came.

A few offices and institutions had broadband, so there would be one laptop and a queue of people waiting to email a family member in Delhi or Bangalore. For my brother, whose shop takes PhonePe, losing that was very bad. People who couldn't pay digitally simply didn't buy.

What it cost: medicines arrived late, and I think some people suffered because of that. I'm not going to make up a number. I slept maybe four hours a night for weeks. One volunteer quit because her family said the scooter runs were too dangerous for a girl, and honestly I didn't blame them.

### Q4. Safety and fear

There were checkpoints, and people did look at phones sometimes. I mostly worried about what was in the gallery: a photo from a camp, a forwarded video that wasn't even mine but sat there because someone sent it to a group. Now I clear downloaded media every few days, and I turned off auto-download.

But the fear I live with isn't mainly the government. It's my own neighbours seeing something and misunderstanding it. If I coordinate with a nurse from the other community, and I did, because medicine doesn't have a community, then someone could screenshot it and call me a traitor. Women get it worse. They go after your character and your family, with comments about who you're "with." I know a woman whose photo got passed around with a lie attached, and she stopped going out.

The people I trust are maybe ten: two senior nurses, a doctor, my brother, and some volunteers I have known since before 2023. Anything bigger than that is a crowd, and in a crowd, one person can make up a story.

### Q5. Information problems

This is my biggest topic. I've seen with my own eyes that rumours killed people in this crisis. A blurry video, a voice note saying "they are coming to such-and-such village tonight," a photo from some other state or some other year. People believed it, and people acted on it. You've probably read about the fake images and videos that went around back then. Everyone here remembers it.

During the shutdown, the rumours didn't stop. They slowed down, and that made them worse, because there was nothing to check them against. Somebody hears something at the market and it grows with every telling. At the Ema Keithel (the women's market) you'd hear five versions of one incident by evening.

For news coming in, we had local TV, newspapers and phone calls. For news going out, people stood in that one broadband queue, or relatives outside reached us by calling. My cousin in Delhi knew less than a stranger watching national TV, and some of what the stranger saw was wrong too.

To verify something, I ask a person I trust who was physically there. That's the only method that works.

### Q6. Reaction to BuzzChat

What excites me is the area bulletin board, and the expiry. Imagine "Camp X needs 2 boxes ORS, 1 insulin pen, today," and then it disappears so nobody is chasing old needs. And since there's no install, the aunties could join with a QR code. Phone to phone without a SIM would have saved us petrol and saved the scooter girls a lot of risk. Animated QR codes for passing lists sounds silly, but if it really works camp to camp, I'd use it.

The Tor part confuses me. My niece would probably understand it, I don't. And I don't know whether a phone with only the browser link is "in" or not.

What scares me is this: you're building a machine that spreads anonymous posts, unstoppable and unverifiable, and it's strongest exactly when there's no other source of truth. That's the rumour machine. In 2023, the problem wasn't too little anonymous speech. Somebody could post "attack tonight at ___" with no name, it hops phone to phone, and you can't delete it. Then "share to internet" puts the same fake out to the whole world with the author hidden. Thagatchari (thank you), but no. Not unless you've thought about this properly.

There's also a hard truth about the mesh. You say it needs a crowd, but our crowds are separated. The communities live apart now, with buffer zones in between. Bluetooth won't hop across a zone where nobody can stand. So the cross-community coordination I most want is the one thing this probably can't do. You should say that honestly in Manipur.

Another worry: if anyone scanning can tell the app is running, then at some checkpoint it marks me. And an emergency wipe looks suspicious if someone sees you press it. I'd rather the app was simply boring to look at.

Before I trust it, I'd need to see who the trusted posters are: hospitals, relief committees, named volunteers whose posts carry some verified mark, with anonymous posts clearly labelled "unverified." I'd need photos and videos to be off by default, or at least labelled "where did this come from." I'd want posts to be reportable and to fade when many people mark them false. And it has to work in Meiteilon, with voice notes.

What I'd tell others: "Use it for supply lists and camp notices. Don't believe anything on it you can't check with a person."

### Q7. Must-haves and deal-breakers

Must-haves, in order:
1. Verified posts from known relief or medical groups, visually different from anonymous posts.
2. Notices that expire, with structured needs lists (item, quantity, camp, time).
3. Voice notes and Meiteilon support, in both Bengali script and Meetei Mayek, plus Roman typing.
4. Works on a cheap, full, shared phone without killing the battery. I can't give it 2 GB.
5. Encrypted private chat for my circle of ten.

Deal-breakers:
1. Anonymous photos or videos that spread with no "unverified" label and no way to slow them down. I would actively warn people against it.
2. An app that makes my phone look suspicious at a checkpoint.
3. Being the only woman in a group where anyone anonymous can harass me and I can't block or report them.
4. Something only English-speaking young people can use. Then the camps stay on paper, and I stay on the scooter.

## Follow-up interview

### Follow-up 1. You said the people you trust are about ten, and that working with a nurse from the other community could get you called a traitor. You also asked for posts with a verified mark from named volunteers. How would you feel if your own name or your relief group's name sat on a post that people in both communities could see?

My honest first reaction is fear. My stomach goes tight just picturing it. Asking for verified names is easy when it's somebody else's name.

I'd separate two kinds of post. One is "Camp X needs ORS, insulin, sanitary pads." That's boring and useful, and I could live with our group's name on it, because the group is a shield. Ten people share it, and it's not Thoibi alone. The other kind is anything that sounds like a judgment about an incident, like "this video is fake." I would never put my name on that. That is how women get targeted.

So I'd want a verified mark for an organisation, not for my personal name. The app should know a real person stands behind it, but the public shouldn't see which person. I'd also want to choose who sees each post: only our locality, or both sides. Some medical notices should reach both sides, like a vaccination camp or a diarrhoea outbreak warning. Disease doesn't care about buffer zones. Even for those, I'd want the hospital's name on it, not mine.

### Follow-up 2. During the shutdown, who decided which camp lists or notices counted as real? If someone had to give out 'verified' marks in a tool like this, who in Imphal could do that in a way people on different sides would accept, and who could not?

Nobody decided officially. It came down to faces. A list was real if a volunteer I knew had carried it, or the camp in-charge had signed it, or I recognised the handwriting. We once had a fake list asking for things no camp needed, and we caught it because nobody knew the person who brought it.

As for who could give out marks, I'm not sure anyone in Imphal is trusted by everyone right now. Hospitals come closest, maybe, because people from both communities have been treated in them. Medical and nursing professional bodies might work, and some humanitarian groups that worked on both sides. Even those would be doubted by somebody.

Who could not: any political party, any group with a community name in it, and honestly the government too. Many people on both sides felt the administration had failed them, in different ways.

So I'd say don't build one central "verified" authority. Let me choose whose marks I trust, the way I already do in my head. My ten people vouch for groups, and those groups vouch for camps. It's a chain of faces.

### Follow-up 3. You suggested posts should fade when many people mark them false. Tell me about a time a true piece of information was disbelieved, or pushed down, by a lot of people at once. What happened?

You've caught me there. Yes, this happened.

An elderly man from our area was missing for some days, and everyone assumed the worst. Then a call came through a hospital network that he was alive, injured, and being treated somewhere outside our usual circles. The message had come partly through a contact from the other community. People wouldn't accept it. "It's a trick," "they want us to stop searching," "who is this source?" In the groups that still worked on broadband, people shouted it down. His family stayed in agony longer than they needed to. It got sorted out only when someone could physically go and see him.

So if the crowd had voted on that message, it would have disappeared. In a divided place, "many people mark it false" can mean "many people from one side don't like it."

I'd change what I said. Maybe reports should send a post to trusted reviewers instead of hiding it automatically. Or reports should slow sharing rather than delete the post, and only for photos and videos. Inconvenient truths need protection too.

### Follow-up 4. Ima and most of the aunties in the camps share information mostly by voice note. When a voice note reached you during the crisis, how did you decide whether to believe it or pass it on? Is that any different from how you handle a typed message?

Voice notes are more dangerous, because they feel more real. You hear a frightened woman's voice and your body believes it before your head does. A typed message is just letters. I can be cold with letters.

My rule, which I didn't always follow, was this. If I recognise the voice, I ask myself whether that person was actually there or is repeating something. Most of the time they're repeating. If I don't recognise the voice, I treat it as nothing, however scared it sounds. I never forward a stranger's voice. Ima forwards everything, by the way. We fought about it many times.

For relief work, voice notes were actually good. A camp in-charge saying "we have twelve diabetics, insulin finished" is clear, and I know her voice.

So for the app: show me whether a voice note was recorded by the sender or forwarded from someone else. Make that obvious, maybe "forwarded, original speaker unknown." If it's possible, a small transcript would help too, so I can read it calmly instead of only feeling it.

### Follow-up 5. Your phone is also the house phone: your niece watches cartoons on it and Ima makes video calls. Walk me through what would happen if a family member opened a private chat with your circle of ten, or saw a notice from a camp on the other side. What would you want them to see, if anything?

My niece is six. She'd tap everything and might send a cartoon sticker to the senior doctor. That's funny, not dangerous.

Ima is different. If she saw my chat with the nurse from the other community, she wouldn't report me. But she'd worry, she'd tell my aunt "just so someone knows," my aunt would tell someone else, and then it's in the locality. Love leaks too.

A camp notice from the other side would upset her. Not because she's hateful. She has lost things too, and seeing their needs might feel like a betrayal of ours. I understand that.

So when the app is closed, I'd want it to look like nothing, or like an ordinary notes app. The private circle should need my PIN or fingerprint every time it opens, not just once a day. Notices could sit in a separate place, also locked. The cartoons and video calls stay free for everyone.

I don't want to hide my whole life from my family. I just want to decide what to tell them and when, myself.

### Follow-up 6. You said the mesh probably can't cross the buffer zones. During the shutdown, when information or supplies did get from one community to the other, how did it actually travel, and who carried it?

Mostly through people whose jobs gave them a reason to cross, or who were seen as neutral. Medical people were part of it: a referral from one hospital to another, a doctor calling a doctor on a normal voice call. Some humanitarian and faith-based relief groups worked in more than one area. Government relief went through official channels, slowly. Sometimes it was drivers, ambulance and supply trucks, though I won't say more about who or how, because those people deserve their privacy.

Most of the information crossed by plain phone calls, person to person, between people who knew each other from before. I had a batchmate from nursing school on the other side, and we'd talk late at night. "Do you have extra ORS? Do you know where this patient went?" Short calls, no names in groups.

So the bridge was always a person with a reason and a relationship. For the app, that means the crossing won't happen by Bluetooth hopping through the crowd. It might happen through one trusted phone carried by one of those people. If BuzzChat makes that one person's job easier and safer, that's the real use. Don't promise more than that.

### Follow-up 7. You said you'd rather the app was 'simply boring to look at,' and that pressing an emergency wipe could itself look suspicious. When someone did look at a phone at a checkpoint, what kinds of things drew attention, and what kinds passed without notice?

I'll only answer in general terms, because the people stopping you weren't always the same kind of people, and I don't want this read as a guide to anything.

What I saw draw attention was pictures and videos first, especially anything that looked like an incident, a crowd, or the other community's area. Group names came next, if a group had a political-sounding or community name. Unusual apps with strange icons got a "what is this?" Mostly, though, it was nervousness. A person fumbling and swiping quickly away looked worse than anything that was actually on the phone.

What passed was family photos, hospital duty rosters, cartoons, cooking videos. My phone is mostly a tired nurse's phone, and that was honestly my protection.

That's why I say boring. I'm not asking for tricks. I'm saying that an app with a dramatic red panic button or a hacker-looking icon puts ordinary people at risk without meaning to. If it looks like a normal messaging or notes app, keeps no photo gallery of its own by default, and doesn't save media automatically, then most people are just people with phones. Expiring messages help simply because there's less old stuff sitting there.

### Follow-up 8. Think back to the Metformin 500 that became Metformin 50, and the volunteer who stopped doing scooter runs. If a tool had helped in that first week, what is the smallest thing it would have had to do well, on day one, with no training, for you to keep using it?

One thing: send a list from my phone to another phone with no data, and have it arrive exactly as I typed it. "Metformin 500, 3 strips" arrives as "Metformin 500, 3 strips." No retyping, no reading out over a call.

Then a tick that shows it reached them, and a second tick when they opened it. That alone would have cut half the scooter runs. Ideally the pharmacist could reply "confirmed" on the same list, so we both see the same thing.

On day one, the setup is: I open it, it shows a QR code, the other person scans it, and we're connected. No account, no email, no English paragraph explaining Tor. If it needs more than one minute while a camp is waiting for insulin, I go back to paper.

It also can't eat my battery or ask for 2 GB.

If it did only that, reliably for a week, I'd trust it enough to try the bulletin board next. Trust comes from the small thing working every time, not from a list of features. The scooter girl might have stayed if half her runs had become one scan.
