# Persona: Sana (fictional)

> **Note**
> Simulated persona for design research. Not a real person; quotes are synthetic. Validate with real, consenting participants before relying on these findings.

A first-time protester and NEET aspirant from a small town in Rajasthan who wants to "shout without a face". She would use BuzzChat on protest days only, for logistics (water, medical tent, lost friends) and for anonymous memes. She needs no phone number, no strangers able to message her, a Hindi-first interface, and a look boring enough to survive her father's near-daily phone checks. BuzzChat's mesh is designed around police and shutdowns, but her everyday threat is a family member picking up her phone, and her audience is on Instagram, not Nostr.

## At a glance

| Attribute | Detail |
| --- | --- |
| Age | 19 |
| Region | An unnamed small town in Rajasthan ("everybody there knows everybody"). She spent two years at a coaching hub and is now back at home. |
| Languages | Hindi and Marwari, with basic English. Wants a Hindi interface; finds X confusing and tech words off-putting. |
| Role | NEET (medical entrance exam) aspirant. Her family spent about four lakh rupees (savings plus a loan against her mother's gold) on coaching, and the leaked paper cancelled her attempt. She is preparing for the re-exam. A first-time protester: one solidarity sit-in plus two days in Delhi, hidden from her family behind a "doubt-clearing camp" cover story. |
| Shutdown history | At Jantar Mantar the network failed in the crowd: full signal, nothing loading, messages an hour late. Her group lost a friend for almost two hours and found her by walking around shouting her name. On a later protest day she was at home; friends in Delhi said the internet stopped in the centre and their videos only came out at night. |
| Devices | Budget Android, about 9,000 rupees and two years old. The screen is cracked in one corner (the keyboard misses keys) and the camera is cracked too. 64 GB of storage, always full of lecture PDFs and test series. Battery is dead by about 4 pm with YouTube. Apps: Instagram (scroll only), YouTube (lectures and news explainers), WhatsApp (family group, PG hostel group, a muted 900-person protest group), Telegram (PDFs). No X, Discord or Signal. |
| Connectivity | One prepaid SIM with about 1.5 GB a day, shared by hotspot with her two siblings. Her brother uses it up on cricket, so data is effectively gone after 7 pm. |
| Literacy / voice preference | Fully literate in Hindi and comfortable with apps, but not with jargon. Her mother understands green and red, not "verified signature". Her 15-year-old sister is more phone-savvy and more wary of creeps. |
| Security posture | Posts almost nothing publicly; shares memes only with a close group. Has archived the protest group and hidden her protest photos in a folder renamed "Bio diagrams". Fears her face or number being linked to the movement, an FIR (police case) ending her medical career, harassment from men, and family talk about her "character". Her father checks her phone almost daily. |
| Main goals | Be heard without being seen, get through protest days safely with her friends, avoid fake notices, and never let any of it cost her re-exam preparation: "We just want our exam back and our parents' money to mean something." |

## Key problems

| Problem | Severity | Quote |
| --- | --- | --- |
| Her most likely real-world threat is a family phone check, not the state. An unfamiliar app icon called "BuzzChat" is itself the giveaway, and BuzzChat has no disguise or decoy mode. | Critical | "In the next month? Papa, a hundred percent. ... He picks up my phone almost every day. ... If Papa sees a strange app called BuzzChat, I'll get questions. Can it hide itself, or look like something boring?" |
| Unsolicited messages from men decide whether she adopts it. Girls (and her 15-year-old sister) will leave at the first stranger "hi" or the first "nearby people" list full of boys' names. Open mesh chat and peer lists, as inherited from bitchat, show exactly that. | Critical | "Girls especially will ask one question first: can boys message me on it? If random men can DM, they won't touch it." |
| BLE advertising can be detected, which makes the app a beacon on her body. She will refuse it unless the app is upfront and clear about this, and she rejects any promise of invisibility. | Critical | "If police or anyone can see the app on my phone, isn't that like a sign on me? Then I'd rather not have it. ... Don't pretend it's invisible if it's not. That's worse than not having it." |
| Fake official circulars with seals and letterheads cause real harm. A seal only works if the app itself checks it and loudly flags fakes. A missing tick goes unnoticed, and most fakes reach her mother as WhatsApp forwards, outside BuzzChat entirely. | High | "If there's no tick, people won't notice that it's missing. They'll just forward it. The fake one should look ugly, with a red warning on it." |
| Lookalike fake pages already copy the movement's logo and run donation QR scams. A fake movement can create its own key and get a "valid" seal under a similar name, and a fake poster QR can lead to a cloned web client. | High | "I did see fake pages with the same logo and a slightly different name. One of them was asking for "donations" through a QR code." |
| In dense crowds the cellular network fails and friends get separated. Searching takes hours of walking and shouting, and a dead phone defeats any app-only fix. | High | "We lost one PG girl for almost two hours, and we found her by walking around shouting her name. ... Her phone actually died. That's why we couldn't reach her. So the app alone can't fix that." |
| Battery drain from constant BLE scanning at a sit-in with no charging point would kill her phone by midday. | High | "if Bluetooth is on all day at a sit-in, my phone is dead by noon. There's no charging point there, people were sharing power banks." |
| Publishing to Nostr feels pointless because her audience and the movement's reach are on Instagram. Without a path into the big page, and feedback that the post was picked up, posting feels like writing in a diary. | High | "If my post goes somewhere nobody sees it, it's like writing in a diary. Maybe that's still useful if the movement's page picks it up from there and reposts it, I don't know." |
| Content can identify her even without a name: EXIF and device metadata, editor-app watermarks, and a recognisable personal style. A generic "metadata removed" message would not reassure her. | High | "Every editor app has a watermark, the image has the phone's details hidden inside it somewhere, and my friends know my style. ... Don't just say "metadata removed", say "nobody can find out who made this."" |
| BuzzChat's look and jargon risk reading as illegal or "hacker". Its green-on-dark terminal style from bitchat would scare her, her sister and her mother. | High | "It looks dark and green like a hacker movie. She'll think it's something illegal and get scared, and Mummy will definitely think so." |
| Wiping everything conflicts with keeping protest memories. She needs off-device safekeeping, not only destruction. | Medium | "So "delete everything" is for the emergency, not for my memories. What I really want is a way to put them somewhere safe, so that if I delete them from my phone they still exist somewhere. Is that even possible?" |
| There are no other mesh users in her hometown, so the app has no everyday value. It becomes a protest-day-only tool competing for scarce storage. | Medium | "in my town who is going to be nearby with this app? Nobody. So for me it's a protest-day thing only, not an everyday thing." |
| The movement runs on video, and BuzzChat cannot carry reels over Bluetooth. | Medium | "No reels is honestly a big minus. Our movement runs on videos. But small photos of a poster or a notice are enough for logistics." |
| Animated screen-to-camera QR assumes a working camera and confident users. Her camera is cracked. | Medium | "Animated QR screen-to-camera sounds cool but complicated. My camera is cracked too, so I don't know if it would even scan." |

## Needs

- No phone number, account, real name or photo, ever. A random nickname is pre-assigned on first launch.
- Consent-gated messaging: nobody can message her until she allows it, with a large one-tap block, stated in Hindi on the first screen before any tech talk.
- An everyday disguise against family phone checks: a neutral icon and name, or a no-install browser mode that leaves no app icon.
- Plain, honest disclosure that Bluetooth use can be detected, with a per-day on/off decision and no invisibility claims.
- An official seal checked by the app itself: a big green tick or a loud red "NOT from them" warning in Hindi and colour. It must also be checkable for posts that were forwarded as screenshots or PDFs.
- A Hindi protest-day bulletin board for water, medical tent, lost-and-found, safe exits and volunteer headcounts.
- A morning "if lost, go to ___" meeting-point agreement for her group, a "can't find my friend" alert, and short messages that keep retrying until they get through.
- An anonymous submission box to the movement's page volunteers, built so they never receive her identity, plus a private "your post was shared" signal.
- Image sanitising that strips EXIF and device data, explained as "nobody can find out who made this".
- Small install size, low battery use and no background drain on normal days.
- Auto-expiry of protest-day content after two or three days unless she saves it, plus an emergency wipe that can spare chosen memories, with a safe off-device place to keep them.
- A clean, soft, school-notice look rather than a hacker aesthetic.

## Deal-breakers

- Making her easier to identify or spot (for example Bluetooth detectability) without telling her clearly up front.
- Requiring an account, phone number or photo, especially as the first thing asked.
- An English-only or jargon-heavy UI ("verified signature", "metadata", "encryption" up front).
- Creeps or fake pages: one unsolicited stranger "hi" in the first hour, or a "nearby people" list of boys' names.
- Dead weight on normal days: taking storage or battery while doing nothing.
- A dark, green, "hacker movie" look that signals illegality to her, her sister or her mother.
- Costing her re-exam preparation (lecture notes, physics lectures) or her protest photos to make room.

## How BuzzChat fits

| Feature | Verdict | Note |
| --- | --- | --- |
| Phone-to-phone mesh chat and posts, no internet or SIM | Partly | Fits her Jantar Mantar case, where signal was full but nothing loaded and messages arrived an hour late. It is useless in her hometown ("Nobody" nearby) and costs battery at sit-ins with no charging. It is a protest-day-only tool, so day-of activation via the poster matters more than installing ahead of time. |
| Join via poster QR or link: no install, no account, no number | Solves | Her favourite feature ("my number is the thing I'm most scared of"). Browser-only mode also leaves no app icon for her father to find, which is an underrated disguise benefit. Risks: it only works near a hub phone; browser history and saved tabs can still reveal it; and the fake donation-QR precedent shows posters can be swapped for cloned client links. |
| Shutdown queue: "share to internet" via Tor to Nostr, author hidden | Partly | Meets her "shout without a face" goal technically, but she sees Nostr's small audience as a "diary". Her identity is safe only if each post uses a throwaway key. A persistent per-user Nostr key would link all her posts together, and her style is already recognisable. There is no bridge to the Instagram page she follows and no "your post was shared" feedback. |
| Area bulletin board with expiring notices and volunteer templates | Solves | Matches her request almost word for word: "water at the left gate, medical tent here, lost-and-found here", plus headcounts for volunteers. Expiry fits her wish for auto-cleanup. It must be in Hindi and needs lost-friend and meeting-point templates. |
| Movement signing key and unforgeable seal mirrored on Nostr | Partly | Her top-liked feature, but only if the app checks it and shouts red on fakes; a silently missing tick fails. Big gaps: (a) fakes reach her mother as WhatsApp PDFs and screenshots, where a seal is just a copyable picture, so there must be a way to check forwarded copies; (b) a lookalike movement can sign with its own key and get a valid seal, so the real key must be pinned from a trusted source and close-name lookalikes flagged; (c) "verified signature" wording fails her mother. |
| End-to-end encrypted private messages | Risk | Encryption is not what she asked about. What decides adoption is whether strangers can message her. If any nearby peer can open a DM or appear in a peer list, girls leave immediately. She needs message requests that are off by default, strangers' first messages hidden, and easy blocking, all stated in Hindi on the first screen. |
| Small images over mesh, no video over Bluetooth | Partly | Photos of a poster or notice are "enough for logistics", but "our movement runs on videos". Images also carry EXIF and device data; she needs automatic stripping plus a plain-language promise before she shares memes beyond her close group. |
| Animated QR screen-to-camera relay when radio is jammed | Gap | She called it "cool but complicated" and her camera is cracked. It is unlikely to work for her, and on the first-run screen it would add to the "hacker app" impression. |
| Honest limit: Bluetooth presence is detectable | Risk | Her first deal-breaker unless disclosed clearly. She explicitly prefers blunt honesty plus a per-day toggle over any claim of invisibility. This must be the default onboarding copy, not buried in settings. |
| Everyday disguise against family phone checks | Gap | Her highest-frequency threat ("almost every day"). A visible "BuzzChat" icon and a bitchat-style terminal look fail it. She needs a neutral name and icon option, a quiet notification style, or a browser-only path. |
| Emergency wipe (inherited bitchat triple-tap panic clear on the chat header) | Partly | Covers "one-button delete everything", but it is an undiscoverable gesture and wipes everything. She wants auto-expiry of protest-day data and a wipe that does not destroy memories she chose to keep elsewhere. |
| Anonymous submission to the movement page with pickup notification | Gap | Her own design: "a box where I drop my meme" that volunteers check without seeing her name, plus "your post was shared". Not in the concept, but feasible: encrypt to the movement key from a throwaway sender key, and detect a signed repost locally by matching the content hash. |
| Safe off-device keeping of protest memories | Gap | She asked "Is that even possible?" BuzzChat offers nothing here. It is out of scope for a mesh messenger, but onboarding should say so honestly rather than imply the panic wipe is all she needs. |

## Design implications

1. Put safety and consent copy first, in Hindi: "Koi bhi aapko tab tak message nahi kar sakta jab tak aap allow na karein" [Nobody can message you until you allow it] should be the first screen, before Bluetooth, encryption or Nostr.
2. Make direct messages request-only by default: a stranger's first message goes to a collapsed "requests" tray with no preview and no notification, and Block is one tap from any message. Hide or anonymise the "nearby people" list by default (counts, not names).
3. Auto-assign a random, non-gendered nickname at first launch, never ask for a name, photo or number, and rotate the nickname per event.
4. Add protection against household phone checks: a neutral, honest app label and icon, discreet notification text, and a recommended browser-only path with a "clear this from your browser" step for users whose risk is a home phone check. (She asked for an icon that looks like something else; the first study rejects disguise modes, and [GENZ_RESEARCH_SYNTHESIS](../GENZ_RESEARCH_SYNTHESIS.md), tension 1, keeps that rule until real participants and local counsel have been consulted.)
5. Rewrite the Bluetooth detectability limit as plain Hindi onboarding copy with a big per-day on/off choice, for example: "Bluetooth on hone par aas-paas koi jaan sakta hai ki yeh app chal raha hai" [When Bluetooth is on, someone nearby can tell this app is running]. Never claim invisibility.
6. Seal UX: a large green tick plus the movement name, or a loud red full-width "YEH UNKA NAHI HAI" [this is not theirs] banner when a post claims the movement but fails verification or uses a lookalike name. Pin the movement key from the poster QR, warn on similar names, and add a "check a forward" flow where a pasted text, PDF or screenshot is matched against the movement's signed posts.
7. Make protest-day mode an explicit, time-boxed session. Start it from the poster QR, have the group pick an "if lost, go to ___" meeting point in the morning, offer a one-tap "can't find my friend" broadcast with a loud alert on nearby group phones, and auto-expire all session data after 48-72 hours unless the user saves it.
8. Provide bulletin board templates in Hindi for water, medical tent, lost-and-found, safe exits and volunteer headcounts. Use icons and colour over text so low-literacy and older users can read them.
9. Use a fresh throwaway key for each "share to internet" post by default, so that separate posts cannot be linked to one author.
10. Strip EXIF and device data from images before sending. Show "Is photo se koi pata nahi laga sakta ki yeh kisne banaya" [nobody can tell from this photo who made it] instead of "metadata removed", and warn gently that personal style and watermarks are not removed.
11. Build an "anonymous drop box" addressed to a movement key, so volunteers receive content with no sender identity. Detect a signed repost locally and show a private "your post was shared" notice to fix the diary feeling.
12. Keep install size well under Instagram's. Use duty-cycled BLE scanning with a visible battery estimate and a "low battery" mode. Run no background radio when not in protest-day mode.
13. Make the emergency wipe discoverable (a labelled button with confirm, not only a hidden triple-tap) and spell out what it deletes. Separate "clear protest data" from "wipe everything".
14. Use a soft, light, school-notice visual language with no terminal green on black. Treat the bitchat-inherited aesthetic as an adoption blocker for this segment.
15. Do not lead with animated QR in onboarding. Offer it as a fallback that degrades well with cracked cameras (large codes, slow frame rate, retry hints).

## Interview transcript

### Q1. Who you are

My name is Sana. I'm 19 and I'm from a small town in Rajasthan. Please don't write which one, everybody there knows everybody. My father has a small cloth shop and my mother runs the house. I have one elder brother and one younger sister. Two years back I went to the coaching city for NEET. Rent for the PG (paying-guest hostel), coaching fees and test series came to around four lakh in total. Some of it was Papa's savings and some was a loan against my mother's gold. I studied twelve, fourteen hours a day. In my last mock I was safe for a government college.

Then the paper leak happened and the whole exam was cancelled. I didn't cry that day, I just felt empty. The next week a girl from my coaching batch, not my friend but I knew her face, did something to herself. That is when I got angry.

The "cockroach" thing was a joke to the boys online. To me it was not a joke, because that is exactly how people look at us. *Ghar baithi ladki, padhai chhod ke naare laga rahi hai* [a girl sitting at home, left studies to shout slogans]. My first protest was the solidarity sit-in in the coaching city. After that I went to Jantar Mantar for two days with three PG girls. I told Mummy it was a "doubt-clearing camp". She still doesn't know.

### Q2. Your phone and online life

My phone cost about 9,000 and it's two years old. The screen is cracked in the corner, so the keyboard misses the letter "q", which is fine because I never use it. It has 64 GB and it's always full of PDFs and lecture downloads. The battery dies by 4 pm if I watch YouTube. My data is one prepaid SIM, roughly 1.5 GB a day, and I share it by hotspot with my brother and sister. My brother finishes it watching cricket, so after 7 pm the internet is basically gone.

I'm on Instagram the whole day, but only scrolling. I've posted maybe six times, all of them mehendi or sunsets. YouTube I use for lectures, and these days for news explainers. WhatsApp means the family group, the PG group, and one protest group of 900 people where I mute everything. I have Telegram for test-series PDFs. I don't use X, it confuses me. Discord I don't have at all, it feels like a boys' gaming thing. I'd never heard of Signal before this.

I have made memes, but I send them only to my close group. I make them in a free editor app with a Hindi film dialogue over an exam-hall photo. I don't post because if a relative sees it, it's a whole discussion at dinner.

### Q3. The movement

From my side it mostly ran through Instagram. Someone posts a story, the date and place spread, and then the WhatsApp groups forward the poster. The poster forwards were sometimes wrong, with an old date or a different gate number.

At Jantar Mantar it was very well organised in some ways. Didis (elder sisters) were giving water and food packets. There was a medical tent, and one of the medical volunteers gave me ORS. But the network was terrible. With so many people my phone showed full signal and nothing loaded. Messages went through one hour late. We lost one PG girl for almost two hours, and we found her by walking around shouting her name.

On 20 July I was back home. Friends in Delhi said the internet stopped in the centre, so their videos only came out at night after they left. When the X account was blocked I didn't even notice, because I don't use X. Instagram kept working for me. I did see fake pages with the same logo and a slightly different name. One of them was asking for "donations" through a QR code.

### Q4. Safety and fear

My biggest fear is my face. Somebody put a reel of the sit-in on Instagram and I'm visible for two seconds in the back with my dupatta half on my face. My cousin's friend recognised me. He didn't tell anyone, but it could have gone to my father in one minute.

The legal side I don't fully understand. I hear "FIR" and get scared, because one FIR and my medical career is finished, or that is what people say. Also, I'm a girl. If my number gets out, the messages I'd get... I have already had one random man DM me "cockroach ki rani" [queen of the cockroaches] after I liked a post.

At home, Papa sometimes picks up my phone to "check the time". If he ever went through it properly I would delete the protest WhatsApp group, the Jantar Mantar photos and the memes. I've already archived the group and hidden the gallery folder.

### Q5. Information problems

There is so much fake stuff. One "official notice" said the re-exam was postponed, with a government-looking letterhead. Half my PG believed it and stopped studying for two days. Then there are AI photos of police doing things, and I can't tell if they're real. Some uncle in the family group sent the "Pakistan followers" thing, and when I said it's not true he said "you are brainwashed".

How do I verify? Honestly, I wait. If the big page posts it and two news channels say it, I believe it. Otherwise I ask the one senior from coaching who is very into this.

I want the message to go out, that we are not lazy and the exam system is broken. But I don't want to be the face of it, that's the problem. Our message needs crowds, and crowds have faces.

### Q6. Reaction to BuzzChat

*[Interviewer explained the concept in plain Hindi-English.]*

Okay, the poster QR thing is actually good. No install and no number. I liked that a lot, because my number is the thing I'm most scared of. At Jantar Mantar, the day we lost my friend, something that works without the network would have helped. Even just a board saying "water at the left gate, medical tent here, lost-and-found here". The notice board idea with headcounts is useful for the volunteer didis.

The official seal I like the most, honestly. If the real movement's notices have a seal and the fake "postponed exam" letter doesn't, that saves people from panic. But someone has to teach my mother what a "seal" means, and that is the hard part. People won't check. They'll just forward.

Now the doubts. Bluetooth "tens of metres" is fine inside a crowd, but in my town who is going to be nearby with this app? Nobody. So for me it's a protest-day thing only, not an everyday thing. And my battery: if Bluetooth is on all day at a sit-in, my phone is dead by noon. There's no charging point there, people were sharing power banks.

"Anyone scanning Bluetooth can tell the app is running" scares me a lot. If police or anyone can see the app on my phone, isn't that like a sign on me? Then I'd rather not have it. The same goes for a phone check at home. If Papa sees a strange app called BuzzChat, I'll get questions. Can it hide itself, or look like something boring?

Nostr I don't understand. If it's a small audience, what is the point? My whole life is Instagram. If my post goes somewhere nobody sees it, it's like writing in a diary. Maybe that's still useful if the movement's page picks it up from there and reposts it, I don't know.

No reels is honestly a big minus. Our movement runs on videos. But small photos of a poster or a notice are enough for logistics.

Animated QR screen-to-camera sounds cool but complicated. My camera is cracked too, so I don't know if it would even scan.

Would my friends use it? Only if the big page tells them to on the day, with a QR on the poster. Nobody will install it "just in case". Girls especially will ask one question first: can boys message me on it? If random men can DM, they won't touch it.

### Q7. Must-haves and deal-breakers

**Must-haves, in order:**
1. No phone number and no real name, ever. My face and name must not be linked to anything.
2. Strangers cannot DM me unless I allow it, and blocking is easy.
3. The official seal on notices, shown simply in Hindi as well, so fake circulars die fast.
4. A protest-day notice board for water, medical help, lost friends and safe exits, in Hindi.
5. It must work on a cheap phone with low storage and not drain the battery.
6. A one-button "delete everything" for when my phone gets checked.

**Deal-breakers:**
1. If it makes me easier to identify or to spot, for example the Bluetooth thing, without telling me clearly.
2. If it needs an account or number.
3. If it's English-only or full of tech words.
4. If it's full of creeps or fake pages like every other app.
5. If it sits there doing nothing on normal days. I already have no space.

One last thing. Please don't make it look like a "hacker app". My generation isn't trying to be revolutionaries. We just want our exam back and our parents' money to mean something.

## Follow-up interview

### Follow-up 1. You said you've archived the protest group and hidden the gallery folder, but you haven't deleted the Jantar Mantar photos. You also put a one-button 'delete everything' on your must-have list. Tell me about the last time you came close to deleting them. What stopped you, and what would you lose if they were gone?

It was about three weeks back. Chachaji (my uncle) came over and his son took my phone to play a game. My heart was going *dhak-dhak* the whole time. That night I opened the hidden folder and selected everything. My thumb was on "delete" and then I saw one photo. It's us four PG girls sitting on the footpath at night, sharing one plate of chole kulche, all of us tired and laughing. In that photo I don't look like a failure. I look like someone who did something.

So I didn't delete it. I just moved the folder again and renamed it "Bio diagrams".

If they were gone, nobody would ever believe I was there, maybe not even me after some years. The exam took two years from me. These photos are the only thing from this year that is actually mine.

So "delete everything" is for the emergency, not for my memories. What I really want is a way to put them somewhere safe, so that if I delete them from my phone they still exist somewhere. Is that even possible?

### Follow-up 2. Your memes go only to your close group, and you're visible in that reel for just two seconds, yet you said 'our message needs crowds, and crowds have faces.' When you picture your exam-hall meme reaching lakhs of people with nothing linking it to you, how does that feel? What would have to be true for you to send it beyond your close group?

Honestly? It feels *mast* (great). Thinking of my meme with the Hindi film dialogue on lakhs of phones, and some boy in Bihar laughing at it the same way we laughed in the PG. It would be like I'm shouting, but my face is not there.

But I'm also a bit scared of it. Every editor app has a watermark, the image has the phone's details hidden inside it somewhere, and my friends know my style. If my close group sees it go viral, one of them will say "arre, this is Sana's" in front of someone.

For me to send it out, three things would have to be true. First, nothing on the file can point to me: no name, no number, no phone details. Second, the app has to tell me that in simple words. Don't just say "metadata removed", say "nobody can find out who made this." Third, I need to know it went through a big page, so it looks like it came from the crowd and not from one girl in Rajasthan.

And once it goes out, I can't take it back. That part I'd have to accept.

### Follow-up 3. You said a post that nobody sees is like writing in a diary, but that it might count if the movement's page picks it up. Walk me through how something you made could get from BuzzChat to the Instagram page you follow. Who would have to see it, and why would you trust them to repost it without your name?

Let me think about how it actually happens now. Someone sends a meme to the senior from my coaching, he sends it to someone who knows the admin, and then it's on the page. It's always a chain of people.

So with BuzzChat it would need something like a box where I drop my meme or photo, and the page's volunteers check that box. They don't see my name, they only see the meme. If they like it, they post it.

Why would I trust them? Honestly, I don't trust the admins as people, because I don't know them. What I'd trust is the system, if the app can show me that they never got my name in the first place. If they don't have it, they can't leak it, even if they want to or someone forces them.

One more thing. I'd want to know when they picked it up, maybe a small "your post was shared" message. Otherwise it's again the diary feeling. I don't need credit with my name. Just knowing is enough. I'd show my PG girls the reel and say "see, that one is good", and only I would know.

### Follow-up 4. Before the fake 're-exam postponed' letter was exposed, what made it look real to you and the PG girls? And if a fake page copied the movement's seal the way they copied the logo for the QR donation scam, how would you or your mother tell the difference?

It looked exactly like the real ones: the Ashoka emblem on top, a reference number, a date, a signature, and "Hindi-English" both. It also came as a PDF, not a photo, and somehow a PDF feels more official. And honestly, we *wanted* to believe it. Everyone was so tired. When something says you can rest, you believe it.

It also came from a girl's cousin "who works in Delhi". That made it feel like inside news.

About the seal, the problem is that if it's just a picture, anyone can copy it. Then it's useless, it's only another logo. For it to work, the app itself has to check it and say "this is real" or "this is NOT from them" in big letters, in a colour, in Hindi. A green tick or a red cross. My mother understands green and red. She doesn't understand "verified signature".

And when it's fake, the app should shout it, not just quietly skip the tick. If there's no tick, people won't notice that it's missing. They'll just forward it. The fake one should look ugly, with a red warning on it.

### Follow-up 5. Think back to the two hours you were looking for your PG friend at Jantar Mantar. If all four of you had the app that day, what would you want it to show or do in the first five minutes? And how would your friend have known to open it, with her phone dying or out of battery?

In the first five minutes I'd want something very simple. I'd press "I can't find my friend", and her phone, if it's nearby, gets a loud message: "Your group is looking for you, come to the medical tent." I don't need a map, a map would confuse me in that crowd. Just a meeting point we already agreed on.

Also a short message that keeps trying to get through even when the network is dead, like "I'm near the water stall, left side". Even a one-line message would have saved us.

But you're right about the battery. Her phone actually died. That's why we couldn't reach her. So the app alone can't fix that. What would help is if, in the morning, the app made us choose one meeting point together, like "If lost, go to ___". Then even with her phone dead she'd know where to go. And maybe it could show on the notice board that her name was last seen near the medical tent, if she said so herself.

Honestly, the before-plan matters more than the app in the moment.

### Follow-up 6. You'd want BuzzChat to look like a boring app if Papa picks up your phone, and you also said you wouldn't want police or anyone else to spot it. Are those the same worry or two different ones? Which is more likely to actually happen to you in the next month?

They're different. With Papa, the worry is that he'll be disappointed and that the whole family will discuss my "character" and my studies. That would hurt and it would be embarrassing, but it's my family. With police or strangers, the worry is a real record, something that follows me into college admissions and jobs. That's scarier, but it's further away from me.

In the next month? Papa, a hundred percent. I'm at home now, preparing for the re-exam. He picks up my phone almost every day. Police, I've never even spoken to one. I'm in a small town, and the protest days are over for me for now.

So for me, the boring-look thing matters every single day. The other fear matters only on protest day, when I'm in a crowd.

But I'd want the app to just be honest with me about the second one. If it can't hide that it's running, tell me straight, and I'll decide whether to turn it on that day. Don't pretend it's invisible if it's not. That's worse than not having it.

### Follow-up 7. Your data is gone after 7 pm, your storage is full of lecture PDFs, and your battery is dead by 4. If BuzzChat asked you for something to make room for it on the protest day, what would you give up: storage, battery, cricket time with your brother, a lecture download? And what would you never give up?

For one day? I'd delete old test-series PDFs. I have three copies of some of them anyway, from different Telegram channels. I'd also give up YouTube on that day. Battery for news explainers isn't worth it if I'm standing at the protest myself.

Cricket time is not mine to give up, *bhai* (my brother) will fight me. But on a protest day I'm not home, so the data is mine that day. Ha.

If it's small, less than Instagram, I'd keep it. If it's big like a game, no way.

What I'd never give up are my current lecture notes and the physics lectures for the re-exam. That's the whole point, I'm fighting *for* the exam, not instead of it. If protest day costs me my preparation, then they've won twice.

And the photos, as I told you.

Also, I'd like it if the app cleaned itself up after the day. Everything from the protest day gets deleted automatically after two or three days, unless I save it. That way it doesn't sit on my phone forever, taking space and creating risk.

### Follow-up 8. You said girls will ask one question first: can boys message me on it? Imagine your younger sister downloads it. What would she need to see on the first screen to feel safe, and what would make her delete it straight away?

My sister is 15. She's smarter on phones than me, but she's more scared of creeps too, because it already happened to her on Instagram.

On the first screen she'd need to read, in Hindi, something like: "Koi bhi aapko tab tak message nahi kar sakta jab tak aap allow na karein" [Nobody can message you until you allow it]. That should come before anything about Bluetooth or encryption or any tech thing. And a big, easy block button that she doesn't have to search for.

She also wouldn't want her real name to show anywhere. A random nickname should be set already, so she doesn't have to think of one.

She'd delete it straight away if any of this happened:
- The first thing it asks for is her number or her photo.
- She opens it and sees a "nearby people" list full of boys' names.
- She gets even one "hi" from a stranger in the first hour.
- It looks dark and green like a hacker movie. She'll think it's something illegal and get scared, and Mummy will definitely think so.

If it looks clean, simple and soft, like a normal school-notice app, she'll trust it.
