# Persona: Arjun (fictional)

> **Note**
> Simulated persona for design research. Not a real person; quotes are synthetic. Validate with real, consenting participants before relying on these findings.

A careful field medic and volunteer coordinator who badly wants a sealed, expiring needs-and-shifts board and identity-free patient handover notes. He will only adopt BuzzChat if trusted leads test it first on cheap phones, it does not drain the battery or fill storage, it never shows his location, and it looks boring enough to keep installed between gatherings. What worked for him on a 40-degree day with no network was "a whistle, a runner and a paper chart", and BuzzChat has to beat that before the next big gathering, not during it.

## At a glance

| Attribute | Detail |
| --- | --- |
| Age | 20 |
| Region | Kolkata, where he studies and lives with his family. He volunteered at a Kolkata solidarity sit-in and spent one week in Delhi with a medical volunteer group. |
| Languages | Bengali, Hindi and English. Wants all three in the app, with big, sun-readable buttons, "so my mother could use it". |
| Role | Second-year paramedical diploma student (emergency care) at a private institute. Fees come from an education loan, his father's small electrical repair shop and his mother's evening tuitions. His younger sister is retaking a voided entrance exam. In the field he handed out water and ORS, gave first aid, and ended up coordinating the fan and food volunteers "because nobody else was keeping a list". |
| Shutdown history | Once the crowd grew, the 200+ person WhatsApp rota choked and messages sat on one tick for twenty minutes. On the march day internet services near Parliament were reported shut down and nearby Metro stations were closed. A "need stretcher + cold water" message never arrived; a runner went instead and a heatstroke case waited about fifteen minutes for proper cooling. The team fell back to a paper chart at the water table and runners in coloured dupattas. |
| Devices | Mid-range Android costing about Rs 13,000. 128 GB of storage that is always full (lecture PDFs, WhatsApp media, his sister's mock-test screenshots). Battery is at about 20% by 4 pm, so he carries a cheap power bank. Apps: WhatsApp is primary (family, college, the volunteer rota); Telegram for notes and PDFs; Instagram for watching and stories; X for reading only. He tried Signal once and dropped it. Teammates have old cracked-screen phones in drawers, and one carries a 20,000 mAh power bank. |
| Connectivity | Prepaid plan of about Rs 350 a month for 1.5 GB a day, used up by evening at a site. Many friends use Rs 8,000 phones and have no data left by evening. |
| Literacy / voice preference | Fully literate and comfortable with text and apps, but dislikes jargon ("uncensorable Nostr through Tor" sounds "cringe"). His mother and uncle are less careful users who trust emoji and forward first. |
| Security posture | Practical and avoids slogans. Fears his face, a place and a date being linked later, which could end his professional registration, more than police on the day. Wears a mask, posts without face, location or the word "protest", and posts a week later. Protective of patient privacy after a mistake with identifiable handover photos; he has since cleared them and left the big groups. Promised his mother to stay "only in the medical tent". |
| Main goals | Get urgent medical requests through fast, keep a reliable list of needs and shifts, protect patients' identities, and keep his own career safe: "the skill can be public, but the skill joined to my face, a place and a date can't." |

## Key problems

| Problem | Severity | Quote |
| --- | --- | --- |
| Urgent medical requests do not get through at crowded sites. Cellular congestion and the shutdown left a heatstroke case waiting about 15 minutes for proper cooling, and a runner had to replace the network. | Critical | "She typed "need stretcher + cold water" into the rota group. One tick. We waited maybe five minutes, which is a very long time with heatstroke, and then she said, "Forget it, send someone."" |
| Forged "official" notices could not be told apart from real ones and directly cut the number of volunteers who turned up. | Critical | "A "march cancelled" notice on a forged letterhead went around one morning. Half my rota didn't come. I still don't know who made it." |
| Medical-logistics rumours put patients physically at risk, and the only way to verify was a personal phone call. | Critical | "The dangerous rumours were the medical ones, like "Medical tent moved to the other gate." If that's fake, someone with heatstroke walks the wrong way in the sun." |
| Patient handover happened through identifiable photos and names in big groups. That breaches medical privacy and exposes patients if a phone is taken. | High | "People sent photos of injured protesters in the group for handover, with names. That's wrong. It's a medical privacy problem, and also if someone takes my phone those people get identified." |
| Being identified later could end his career. He fears a face, place and date being linked, more than police on the day. | High | "If my college or a hospital internship coordinator sees my face in some viral video with a caption calling me "anti-national", that's my career." |
| Stale needs keep circulating after they are met and send donations to the wrong place. | High | "old "need water here!" messages kept circulating after we were flooded with bottles, and that wasted donations." |
| Battery, data and storage are very tight on his phone and his friends' phones. Any app that uses them heavily gets uninstalled right away. | High | "It doesn't kill my battery. If it drains 30% in an hour, I'll uninstall it on the spot." |
| Just having or broadcasting an app can mark him at college or in the hostel. BuzzChat's Bluetooth detectability conflicts directly with this. | High | "Even if I'm doing nothing wrong, I don't want to look like I'm carrying a "protest app" at a college gate or in a hostel." |
| Verification marks are ignored by less careful users and lost once content is screenshotted into WhatsApp. | High | "My mother trusts anything with a red cross emoji in it. My uncle forwards first and thinks later... My uncle will screenshot it straight into WhatsApp, and there the seal means nothing." |
| Network effects decide adoption. Privacy alone did not make Signal stick, and nobody installs anything once they are on site. | High | "Signal didn't stick because nobody was there. Installing it alone is like reaching a party two hours early." |
| Official identity broke when accounts were withheld and moved to new handles, so nobody knew which source was real. | Medium | "The official X account got withheld, came back under a new handle, and nobody knew which handle was real." |
| People describe places on site by landmarks, not addresses or coordinates. Tools need a way to say "where" without GPS. | Medium | The runner "knew where to go because the fan girl had described landmarks, not an address: "the big cardboard cockroach banner, next to the barricade with the blue tarp."" |

## Needs

- Fast, reliable delivery of short urgent requests (e.g. "stretcher + cold water") inside a dense crowd, with visible delivery or acknowledgement so the lead knows within seconds whether to send a runner.
- A needs-and-shifts board with templates, headcounts, a "resolved" button and auto-expiry, to replace the paper chart and stop stale requests.
- Seals that are tied to a role and a gathering ("Medical, this gathering"), handed out in person, vouched for by several leads, and expiring after the event.
- Revocation by any two leads, shown loudly on every phone when a seal is cancelled.
- Sealed corrections that sit on top of the original (marked "corrected") instead of silently deleting it, and two-lead confirmation for high-stakes posts such as "tent moved" or "cancelled".
- A loud "NOT from organisers" warning in a different colour on unsealed posts that use official-sounding words (cancelled, moved, medical).
- A structured, identity-free patient handover note (event and time, care given, risks, companion) keyed to a wristband code like K-14, auto-deleted from every phone at handover or at the end of the day.
- Images cropped to the wound only, never a face, by default, with even faster expiry.
- Named zones ("water table", "medical tent", landmark notes) instead of GPS or precise location.
- Joining by QR code without a phone number, plus installing the APK from a friend's phone for people with no data.
- A battery budget of about 25-30% per afternoon at most, and a dedicated hub mode for an old phone plugged into a power bank.
- Bengali, Hindi and English with big, sun-readable buttons, simple enough for his mother.
- A boring name and icon, a clear "off until I turn it on" toggle with no background scanning, an app PIN and auto-clearing of old history.
- Everyday use cases (campus Wi-Fi outages, hostel power cuts, fests, blood camps, first-aid workshops) so it is worth keeping installed.
- Plain-language explanations ("your post will reach the internet later without your name on it") instead of Nostr/Tor jargon.
- A low-stakes pilot kit for leads: about 10 cheap phones in airplane mode, a 3-hour battery check, and a one-page picture guide in Bengali and Hindi.

## Deal-breakers

- It needs a phone number or an account linked to his real identity.
- Anyone can post in the medical or organiser channel, so fake "official" posts get in.
- It drains the battery (30% an hour means an instant uninstall) or fills his already-full storage.
- It shows or infers his precise location.
- It markets itself so loudly as a "protest app" that simply having it installed causes trouble at college or in the hostel.
- It has no use outside protests (he will uninstall it the week after).
- It fails its first trial with the leads: "if the first try fails, she won't give it a second chance."

## How BuzzChat fits

| Feature | Verdict | Note |
| --- | --- | --- |
| Phone-to-phone mesh over Bluetooth/Wi-Fi with no internet or SIM | Partly | It targets his exact failure (the stretcher message stuck on one tick), and a dense crowd helps hop coverage. But he says plainly "if there are jammers, it fails exactly when it matters, like everything else did." It is also unproven that flood-style relaying stays fast with thousands of phones in a 40-degree crowd. A delayed "stretcher" message is no better than WhatsApp's one tick unless the app shows a clear acknowledgement, so the lead knows when to send a runner anyway. |
| QR/link join in a browser, no account or number; Android install from a friend's phone | Partly | No phone number removes a deal-breaker and the reason Signal "didn't feel that different". Installing from a friend's phone fits ("Half my friends have no data left by the evening"). But "nobody installs anything at the site", so on-the-day QR joining matters less than onboarding before the event. Browser-only mode confused him ("Who is the hub? Me? Will my battery die being the hub?"). |
| Shutdown queue + "share to internet" via Nostr over Tor | Risk | Anonymous delayed publishing matches his line ("the skill can be public, but the skill joined to my face, a place and a date can't"). But the framing is "cringe" ("a crypto bro explaining something to me at a chai stall") and the audience is small. More seriously, "uncensorable" also means unretractable. A queued post carrying a patient's face or a mistaken "tent moved" notice cannot be recalled once relayed, which conflicts with his visible-correction model and his patient-privacy ethic. Publication timing can also link a post to a place and date, the exact link he fears. |
| Area bulletin board with expiring notices and volunteer templates | Solves | The strongest fit and the feature that excites him: "Need 10 ORS at the water table, headcount 3 volunteers, expires 30 min. That is exactly what we needed." Gaps: no "resolved" button or live headcount is described, and "area" raised his question "does it know my location?" If it relies on geohash or GPS location channels, it hits a deal-breaker. He wants named zones like "water table" and "medical tent". |
| Movement signing key / unforgeable seal mirrored on Nostr | Partly | It would kill the "tent moved" and forged-letterhead problems. But one movement-wide key does not match how trust works for him: in person, per role, per gathering, vouched for by several leads, expiring, revocable by any two leads, with sealed corrections and two-lead confirmation for high-stakes posts. One key on one phone that is lost or taken makes forgery easy. The seal is also invisible to his mother and uncle (asked whether they would look for it: "No") and lost when screenshotted into WhatsApp, so a passive badge alone fails. |
| End-to-end encrypted private messages | Partly | It protects messages in transit, but his threat is a phone being taken or a classmate grabbing it. Encryption does not help with stored patient photos and names. He needs an app PIN, default auto-delete and a structured, identity-free handover format, none of which the concept offers. |
| Small images over the mesh | Risk | Without guardrails this recreates his worst mistake: identifiable patient photos relayed to many phones. It needs crop-to-wound by default, warnings or blurring when a face is detected, short forced expiry and no forwarding of medical images. |
| No video/reels over Bluetooth | Partly | An acceptable limit for field coordination, which needs text and headcounts. His public reach (the ORS reel) stays on Instagram, posted later and without his face. It does mean BuzzChat cannot carry the "every video public" movement goal, which he is ambivalent about anyway. |
| Animated-QR screen-to-camera relay when radio is jammed | Partly | A real fallback for jamming, but it needs line of sight and someone physically carrying the phone. In practice that is the red-dupatta runner, so the gain over a runner with a paper note is small. It could help sync the board between the water table and the tent once a runner arrives. |
| Bluetooth detectability ("anyone scanning can tell the app is running") | Risk | Conflicts directly with "I don't want my phone to be a statement". Without a firm "off until I turn it on" default, a plain name and icon, and no background advertising, he will uninstall it between gatherings. |
| Battery use and hub phones | Gap | His budget is 25-30% per afternoon, never 30% an hour, and the concept makes no battery promise. His own fix (an old cracked-screen phone plus a power bank under the water table, rotated every two hours) is a hub mode the product should support explicitly, rather than turning a volunteer's personal phone into the hub. |
| Bengali/Hindi localisation and big, sun-readable UI | Gap | Must-have number 4 ("so my mother could use it") and not covered by the concept. Leads will also ask for a one-page picture guide in Bengali and Hindi before endorsing it. |
| Everyday, non-protest usefulness | Gap | "If its only use is protests, I'll uninstall it the week after." Campus Wi-Fi outages, hostel power cuts, fests, blood camps and first-aid workshops are the use cases that would keep it installed and allow pre-event onboarding. The concept pitch does not show them. |

## Design implications

1. Treat urgent requests as a separate high-priority message type ("MEDICAL: stretcher + cold water @ zone") with hop-by-hop delivery and a one-tap "on my way" reply. Show the sender within seconds whether anyone received it, so the decision to send a runner happens at once instead of after five minutes.
2. Build the board around templates (Need / Shift / Headcount) with required expiry, a "resolved" button, live headcount (3 of 5 filled) and visible staleness ("posted 40 min ago"). Expired or resolved needs must stop relaying.
3. Replace GPS "area" with named zones that organisers define (water table, medical tent, gate names) plus a free-text landmark field. Never store or broadcast coordinates, and say so in the UI.
4. Redesign the seal as delegated, scoped credentials: leads cross-sign each other in person and together vouch for role seals ("Medical, this gathering") that expire automatically. Any 2 of N leads can revoke a seal, shown loudly on every phone, and leads publish sealed corrections that mark the original "corrected". High-stakes categories (moved, cancelled, medical) need two leads to confirm.
5. Make missing seals loud, not only present seals subtle: unsealed posts that use reserved words (cancelled, moved, medical, official) show a coloured "NOT from organisers" banner. Put a visible seal and a "verify in BuzzChat" watermark into any screenshot or share so it carries context into WhatsApp.
6. Ship a medical handover card: wristband code (K-14), time, event, care given, risks, companion, with no name or face fields. Restrict it to the medical role and auto-delete it on "handed over" or at the end of the day on every phone.
7. Default medical images to crop-to-wound with face blurring, block forwarding and expire them faster than text. Never let medical images be marked "share to internet".
8. Add a review step to "share to internet": before queuing, check for faces, names and location hints, add publishing delay or jitter so timing does not reveal place and date, and warn plainly that published posts cannot be recalled.
9. Rewrite the copy in plain words in Bengali, Hindi and English: "Your post will reach the internet later without your name on it." Keep Nostr/Tor out of the main UI.
10. Add an explicit hub mode for a spare phone on a power bank (screen off, relay and board cache only, battery and uptime readout), and make the browser-only flow show which hub it is connected to.
11. Publish and enforce a battery budget (target under 10% per hour on budget phones, with a visible meter). Keep the mesh off by default with a single "Off until I turn it on" toggle and no background Bluetooth advertising while off.
12. Ship a low-profile build: neutral name and icon (local chat / notice board), app PIN, auto-clear of history after N days, small install size and a storage cap on cached media.
13. Position and onboard around everyday campus uses (fests, power cuts, blood camps, first-aid workshops) so phones already have the app and the group already exists before any large gathering.
14. Produce a lead pilot kit: a guided 10-phone airplane-mode demo on low-end devices, a 3-hour battery report, a one-page pictorial guide in Bengali and Hindi, and a clear "who made this / what data it asks for" page. Pitch it as "as well as WhatsApp", never "instead of".

## Interview transcript

### Q1. Who you are

I'm Arjun, 20, second year of a paramedical diploma in emergency care at a private institute in Kolkata. The fees are paid with an education loan and whatever my father makes from his small electrical repair shop. My mother does home tuitions in the evenings. So every rupee is counted. If I fail a semester, that's not only shame, it's another year of interest.

My younger sister is one of the lakhs of students who have to retake NEET. She studied two years for it, coaching fees, everything. When the paper was voided she didn't talk for three days. Then the "cockroach" remark was everywhere, and the movement started as a joke on Instagram. I was laughing at the memes, but honestly I was angry. I don't do slogans. I joined the Kolkata solidarity sit-in because a senior from my college said, "they need people who know first aid, it's 40 degrees out there." Later I went to Delhi for one week in July with a medical volunteer group. I handed out water and ORS, did first aid, and ended up coordinating the fan and food volunteers because nobody else was keeping a list.

### Q2. Your phone and online life

My phone cost about ₹13,000. It has 128 GB and it's always full: lecture PDFs, WhatsApp media, my sister's mock-test screenshots. By 4 pm the battery is at 20%, so I carry a cheap power bank. I'm on a prepaid plan, roughly ₹350 a month for 1.5 GB a day. By evening at a protest site that's finished.

WhatsApp is my main app: family, college, volunteer groups. Telegram is for notes and PDFs. On Instagram I mostly watch, and I post stories, not posts. I have X, but I only read it. I've never used Discord. I installed Signal once and nobody I know was on it. I made one reel with a CapCut template, "ORS > your excuses", showing how to mix ORS properly in the heat. It got more views than anything I've ever posted. Useful stuff spreads if it's funny.

### Q3. The movement

Online it was all Instagram and forwards. Offline it was surprisingly organised. People sent food and water through delivery apps, medical students rotated through shifts, and lawyers were around.

What broke was coordination at the site itself. Our volunteer rota was a WhatsApp group with more than 200 people. Once the crowd got big, the network just choked. Messages sat on one tick for twenty minutes. On the day of the march to Parliament, people said the internet had been shut down near Parliament, and the nearby Metro stations were closed. A heatstroke case collapsed near one side of the crowd and our message "need stretcher + cold water" never arrived. In the end we sent a runner. So we went back to paper: a chart taped to the water table, and runners with coloured dupattas [scarves] so people knew who was medical.

Then there was the noise. The official X account got withheld, came back under a new handle, and nobody knew which handle was real. A "march cancelled" notice on a forged letterhead went around one morning. Half my rota didn't come. I still don't know who made it.

### Q4. Safety and fear

My biggest fear isn't police on the day. It's later. In my field you need clean registration. If my college or a hospital internship coordinator sees my face in some viral video with a caption calling me "anti-national", that's my career. My principal already gave a "general warning" in class without naming the movement. My parents support the cause for my sister, but my mother made me promise I'd stay "only in the medical tent".

I wore a mask, partly for the dust. What scares me about my phone is patient data. People sent photos of injured protesters in the group for handover, with names. That's wrong. It's a medical privacy problem, and also if someone takes my phone those people get identified. I've since cleared those photos and left the big groups. I'm not a hero; I just don't want someone else's face on my phone.

Trolling: a friend who made satire reels got her number posted in comments. She went private for a month.

### Q5. Information problems

AI images were the worst. There was a picture of police beating a girl that everyone in my family group shared. It was fake: the hands were wrong, the uniforms were wrong. My uncle also forwarded the "49% followers are from Pakistan" claim, and I had to explain it was disputed. Nobody fact-checks the fact-check.

The dangerous rumours were the medical ones, like "Medical tent moved to the other gate." If that's fake, someone with heatstroke walks the wrong way in the sun. How I verified: I called someone I personally knew on the ground, when calls worked. That doesn't scale.

There's a real tension here. The movement wants every video public. Individuals want to be blurred. I wanted the message out, but not the faces of the people I was treating.

### Q6. Reaction to BuzzChat

*(Interviewer explains: messages hop phone-to-phone over Bluetooth or Wi-Fi with no internet; you join from a QR code in the browser; posts wait during a shutdown and go out to the internet later; there's an area notice board with expiring posts; official posts get a seal that can't be faked; private chats are encrypted; small images work but videos mostly don't; and you can pass posts screen-to-camera.)*

What excites me: the notice board with expiring posts and templates. "Need 10 ORS at the water table, headcount 3 volunteers, expires 30 min." That is exactly what we needed. The expiry matters because old "need water here!" messages kept circulating after we were flooded with bottles, and that wasted donations. A shift template would replace my paper chart. And the seal: if the medical coordinators' posts had a seal nobody could fake, the "tent moved" rumour dies.

What feels cringe: "uncensorable Nostr through Tor" sounds like a crypto bro explaining something to me at a chai stall. My friends won't know what Nostr is and won't care. If the audience is small, it's not replacing Instagram. Say it plainly: "your post will reach the internet later without your name on it."

What confuses me: the browser-only mode working "only near a hub phone". Who is the hub? Me? Will my battery die being the hub? Also, does "area" mean it knows my location? I'd want zones like "water table" or "medical tent", not GPS.

What scares me: you said anyone scanning Bluetooth can tell the app is running. That bothers me. Even if I'm doing nothing wrong, I don't want to look like I'm carrying a "protest app" at a college gate or in a hostel. And if there are jammers, it fails exactly when it matters, like everything else did.

Would we actually use it? Honestly, nobody installs anything at the site. You're sweating, the battery is at 15%, the network is dead. It only works if it's on phones before the day. It would spread if the medical volunteer leads said "everyone on the rota, scan this", with the QR printed at the water table and the first-aid tent, and if it works in Bengali and Hindi on an ₹8,000 phone. Installing it from a friend's phone is good. Half my friends have no data left by the evening anyway.

### Q7. Must-haves and deal-breakers

**Must-haves, in order:**
1. **It doesn't kill my battery.** If it drains 30% in an hour, I'll uninstall it on the spot.
2. **Verified seal for medical and organiser posts**, shown clearly, so fake "official" notices are obvious.
3. **A needs and shifts board with a "resolved" button and auto-expiry**, plus simple headcounts.
4. **Bengali, Hindi and English, simple enough to use in the sun** with big buttons, so my mother could use it.
5. **Easy auto-delete for chats and images**, especially anything with patient details.
6. **Joining from a QR code without a phone number.**

**Deal-breakers:**
- It needs a phone number or an account linked to my identity.
- Anyone can post in the medical or organiser channel, so fakes get in.
- It drains the battery or fills my already-full storage.
- It shows my precise location.
- It markets itself as a "protest app" so loudly that just having it gets me into trouble at college.

My honest doubt: I like it on paper. But on a 40-degree day with no network, I've learnt that the thing that worked was a whistle, a runner and a paper chart. If this can beat that, good. Prove it before the next big gathering, not during.

## Follow-up interview

### Follow-up 1. Can you take me back to the moment the 'need stretcher + cold water' message didn't arrive? Who noticed the person collapse, who decided to send a runner, how long did it take to get help to them, and how did the runner know where to go and who to find?

It was around 2 pm, the worst of the heat. The first person to notice wasn't medical. It was one of my fan volunteers, a college girl handing out paper fans at the edge of the crowd. She saw a boy sit down and then just tip sideways. She yelled, people made a little space, and someone called our medical lead. The lead was a final-year MBBS student. Everyone called her "Didi", and she's the one who actually decides things.

She typed "need stretcher + cold water" into the rota group. One tick. We waited maybe five minutes, which is a very long time with heatstroke, and then she said, "Forget it, send someone." I gave my junior with the red dupatta two bottles and an ice pack. He ran.

He knew where to go because the fan girl had described landmarks, not an address: "the big cardboard cockroach banner, next to the barricade with the blue tarp." He found the boy, then ran back to the tent for the stretcher team. It took about fifteen minutes from collapse to proper cooling. He was okay, but it shouldn't take fifteen.

### Follow-up 2. Your ORS reel spread because it was public and funny, but your biggest fear is your face going viral with the wrong caption. When you posted it, what did you show and what did you leave out? Where is the line for you between 'my useful work being seen' and 'me being identifiable'?

I showed my hands, an ORS packet, a steel glass, a one-litre bottle, and big text: "1 packet, 1 litre, NOT half. Stop making it sweet like sherbet." I used trending audio instead of my own voice. I shot it on our kitchen counter at home and cropped out the calendar from my father's shop, because it has the shop's name on it. No face, no location tag, and the word "protest" isn't anywhere. I posted it a week after I came back from Delhi, not while I was there.

In the comments people asked, "Bhai, where are you volunteering?" I didn't reply.

So for me the line is this: the skill can be public, but the skill joined to my face, a place and a date can't. Mixing ORS properly is useful in any heatwave, at a wedding or at a cricket match. Once you can tell it was me, at that site, on that day, it stops being an ORS reel and becomes evidence of something, and somebody else gets to write the caption.

### Follow-up 3. For the medical seal to mean something, someone has to decide who counts as a medical coordinator. At the sites you worked, who did people actually trust as 'official', and how would you want that decided? What should happen if a coordinator's phone is lost or taken, or if a trusted person posts something wrong?

Honestly, trust was about faces. People trusted Didi because they'd seen her at the tent every day. They trusted the two resident doctors who came after their hospital shifts. They trusted my senior because he brought half of us. Nobody "appointed" them. They showed up and kept showing up.

I'd want the seal handed out the same way, in person. Three or four leads meet before the day, scan each other's codes face to face, and together they vouch for whoever else gets the seal. It should be tied to a role and a date, like "Medical, this gathering", and expire afterwards, so last month's seal doesn't carry over.

If a phone is lost or taken, any two of the other leads should be able to say, "this seal is cancelled", and every phone should show that loudly. If a trusted person posts something wrong, don't silently delete it. Put a sealed correction on top, with the old post marked "corrected", so people can see what changed. For something big like "tent moved", I'd even want two leads to confirm before it shows as official.

### Follow-up 4. When the forged 'march cancelled' notice went around, or the 'tent moved' rumour, what would have made you or your rota stop and check it before acting? Honestly, do you think people in your 200-person group would look for a seal or notice it was missing, especially your uncle or your mother?

What made me stop was small things. It came at 6 am from a number nobody had saved, and the letterhead looked too clean, like a template. But I only stopped because I had a gut feeling. Half my rota acted on it, and part of the reason is that "cancelled" meant not spending a day in 40 degrees. People believe the rumour they're hoping for.

Would they look for a seal? No. My mother trusts anything with a red cross emoji in it. My uncle forwards first and thinks later. So the seal can't just be a small badge people are supposed to notice. When it's missing, the app has to say so loudly: "NOT from organisers", in a different colour. I also think words like "cancelled", "moved" and "medical" shouldn't be allowed to look official unless there's a seal.

The other problem is screenshots. My uncle will screenshot it straight into WhatsApp, and there the seal means nothing. So the leads also have to repeat it at every briefing: "If it doesn't have the seal, it isn't us." That has to become a habit, like the dupatta colours.

### Follow-up 5. Those patient photos were sent for a reason, to hand people over. When you hand over a patient to another volunteer or an ambulance, what's the minimum someone really needs to know? How could that work without a photo or a full name, and how long should that information exist on anyone's phone?

For handover, the next person needs to know:

- **What happened and when:** "collapsed in heat at 2:10."
- **What we did:** "cold sponging and ice packs, conscious but talking slowly."
- **Anything risky:** known allergy, asthma, diabetes, if they told us.
- **Who's with them:** "friend in green shirt."

That's it. Nobody needs a face for that.

Instead of a name, I'd use a number. We could write "K-14" on a paper wristband, or even on the back of the hand in marker, and the handover note is tied to K-14. The ambulance or the hospital will take the real name themselves, properly, with consent. That's their job.

How long should it stay? Until the person is handed over or discharged from the tent, and at most until the end of the day. Then it should disappear by itself from every phone, without anyone having to remember to delete it. If a wound photo is ever really needed, crop it to the wound only, never the face, and let it expire even faster. I learnt this from my own mistake.

### Follow-up 6. You said nobody installs anything on the day, and also that you don't want to look like you're carrying a 'protest app' at the college gate or in the hostel. Between gatherings, what would you actually do with BuzzChat on your phone: keep it, hide it, switch it off, uninstall it? What would the app have to look or behave like for you to feel fine leaving it installed?

If its only use is protests, I'll uninstall it the week after. Partly because of storage, and partly because I don't want my phone to be a statement.

I'd keep it if it were useful on normal days. Our college Wi-Fi dies all the time, there are power cuts in the hostel blocks, and at the college fest nobody can find anybody. A local notice board and chat for that is just useful. Blood donation camps and first-aid workshops too, since our department runs those.

For me to feel fine, it has to look boring: a plain name and a plain icon, something like a local chat or notice board, not a fist or a megaphone. It should do nothing in the background unless I switch it on, with one clear toggle saying "off until I turn it on", so it isn't scanning or eating battery in my bag. I'd like a PIN on it, like the one I put on my gallery, so a classmate grabbing my phone doesn't see old chats. And it should auto-clear old stuff, so there's no archive building up for months.

### Follow-up 7. If BuzzChat needed a few phones to act as 'hubs' at the water table or first-aid tent, who on your team would realistically do that? How much battery would you, or they, give up in an afternoon, and would people bring power banks just for that?

Realistically, me, or whoever is at the water table, because the table doesn't move. The runners and fan volunteers are walking all day, so they can't be hubs. The tent always has somebody sitting there too, usually the person doing the register.

In every team there's also one person with the huge 20,000 mAh power bank. In ours it was a quiet boy from pharmacy who charged everyone's phones. He'd happily be a hub.

On my own phone, I'd give up maybe 25 or 30 percent across an afternoon, if I knew in advance and could plan for it. Not 30 percent an hour. Then I'd stop.

The better idea is an old phone. Half of us have an old cracked-screen phone in a drawer at home. Put the app on it, plug it into a power bank, and leave it under the water table as the hub, so nobody's personal phone is the one running out. Rotate the power bank every two hours like a shift. If the leads put "bring a charged power bank" on the rota, people would bring one. They already bring water bottles because they're told to.

### Follow-up 8. Think about the medical volunteer leads who would have to say 'everyone on the rota, scan this'. What would they need to see or try before they'd risk telling 200 people to use something new instead of WhatsApp? And why do you think Signal didn't stick for you?

Didi would never say "instead of WhatsApp". At most she'd say "as well as WhatsApp". Before even that, she'd need to try it at something small and low-stakes: a college blood camp, a first-aid workshop, or the fest. Put ten phones in airplane mode in one hall and show messages still going across, on the cheap phones, not on someone's expensive one. Check the battery after three hours. Give everyone a one-page sheet in Bengali and Hindi with big pictures. She'd also ask who made it and whether it asks for numbers, because she's careful. And if the first try fails, she won't give it a second chance.

Signal didn't stick because nobody was there. Installing it alone is like reaching a party two hours early. My family, my college and my volunteer groups are all on WhatsApp. It also still asked for my phone number to sign up, so it didn't feel that different to me, just emptier. Apps don't spread because they're private. They spread when the person who makes the rota says "scan this", and the group is already there waiting.
