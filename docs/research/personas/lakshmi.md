# Persona: Lakshmi (fictional)

> **Note**
> Simulated persona for design research. Not a real person; quotes are synthetic. Validate with real, consenting participants before relying on these findings.

A Tamil-speaking health worker and union leader on a shared, overloaded Android Go phone. She wants venue changes to reach a sit-in crowd reliably, and she will drop any app that looks secret at home, singles women out over Bluetooth, drains battery or data, or slows down the health app her income depends on.

## At a glance

| Attribute | Detail |
| --- | --- |
| Age | 40 |
| Region | A small town in Tamil Nadu. She lives with her auto-driver husband, two children (a son in Class 11 and a daughter who has just started college) and her mother-in-law. |
| Languages | Reads Tamil well. Basic English: she recognises "OK", "Send" and "Delete". Types Tamil slowly. |
| Role | Community health (ASHA-type) worker for 14 years, paid an "honorarium". Reluctant women workers' union leader for about six years. Organises peaceful sit-ins outside the block office over pending dues, a fixed salary and an unpaid recharge allowance. |
| Shutdown history | No government shutdown. On a cyclone day, towers were down for most of the day before a district sit-in. About forty women came instead of two hundred, other groups waited at the wrong place, and a false "leaders arrested" rumour sent women home. |
| Devices | Low-cost Android Go phone (about Rs 6,000, chosen by her son) with 2 GB RAM and storage that is always full. Battery lasts about a day without video, and she charges at the health centre. No power bank. Apps: WhatsApp, YouTube, a heavy government health/registers app essential to her job, PhonePe and the dialer. The phone is shared: her son uses it in the morning and notices new icons, her mother-in-law watches YouTube in the evening, and her husband checks WhatsApp, the gallery and notifications at night. Keeps a paper diary of important numbers. |
| Connectivity | Prepaid plan of about Rs 300 for 28 days (roughly 1.5 GB a day), mostly used up by her son's online classes by evening. "I plan my data like I plan rice." |
| Literacy / voice preference | Voice-first. She mostly sends voice notes, as do all the union women, half of whom cannot type. Some older members have only button phones. Needs a full Tamil UI with pictures and big buttons. |
| Security posture | Her threats are a husband who checks her phone, a curious son, a department supervisor who keeps track of union members, one or two informers inside the union, and gendered and caste-based harassment, more than the police. Trusts about six women. Deletes planning chats and keeps group names boring. Practical and cautious, not technophobic. |
| Main goals | Get venue changes and real news to a sit-in crowd reliably, stop rumours from breaking up protests, and keep union work from causing trouble at home or at work. |

## Key problems

| Problem | Severity | Quote |
| --- | --- | --- |
| The shared, checked phone shows notifications to her husband before she sees them. A sender name and message preview on the lock screen or notification shade is enough to cause trouble at home. | Critical | "So if a new app shows a message like "Meena: tomorrow 9 am" on the screen, he will see it before I do." |
| Anything that looks hidden (an app lock, a disguised icon, messages that vanish, a panic wipe) creates suspicion of an affair at home and causes worse conflict than the union messages themselves would. | Critical | "Her husband found it, and that became a much bigger fight than the union messages would have been. Hiding is itself the evidence." |
| Because Bluetooth scanning can reveal that the app is running, owning it could mark union women in a supervisor's review meeting, and a strange device name could be noticed by her son. | Critical | "So the fear isn't really technology. It is being singled out in a room. If many ordinary people have the app, like everyone has WhatsApp, then it means nothing. If only union women have it, it becomes a badge." |
| Any slowdown or crash of the government health app threatens her job. She has already lost entries to heavy apps and will uninstall on the first incident. | Critical | "So if your app slows the health app even once, I will uninstall it that same day, and I'll tell the other women to do the same." |
| Rumours and fake notices (cancellations, arrests, forged orders) break up protests. Typed text from unknown senders is not trusted. She trusts only familiar voices, and only when two of her trusted six agree. | High | "Typed words, I wouldn't believe. Anybody can type "cancelled." A voice is different." |
| Bluetooth range doesn't match her worst case. The groups that missed the venue change were about 2 km away, too far for a short-range mesh without a dense crowd in between. | High | "You said tens of metres per hop. The other group was two kilometres away. So it would help inside the protest, not between villages, unless many people are in between." |
| A hub-phone model concentrates battery drain and personal risk on one visible person, most likely her. | High | "Needs one "hub" person to carry everyone. Then that person becomes the target, and it will be me." |
| A public board that shows who is present or who read a notice would give the supervisor a register of union women, because informers can join the open channel. | High | "If the board shows a list of who is "there" or who read it, the supervisor gets a register of union women. That must not happen." |
| Technical jargon and English-only UI exclude her and her members. Terms such as Tor, end-to-end and hub are meaningless to them. | High | ""Tor," "end-to-end," "hub phone." I don't know these words. If the women have to understand them, they won't use it." |
| Severe storage and RAM limits: the phone is permanently near full and hangs under load. | High | "Every week I get the message "Storage almost full."" |
| Her coordination data lived only in one internet-dependent app, so she was cut off when towers failed. | Medium | "I had a list of block coordinators, but it lived only inside WhatsApp, and I had no written numbers." |
| A backlog arriving all at once on reconnect hangs her phone. | Medium | "Network came back, and two hundred messages arrived at once. My phone hung. I had to restart it three times." |
| Notices that are out of date but still circulating act as rumours. | Medium | "An old notice that is still floating around is also a kind of rumour." |
| She fears and distrusts installing an app file from outside an app store, which blocks the "install from a friend's phone" route. | Medium | "installing the app file from a friend's phone: my son always says never install from outside, there are viruses. Women will be afraid of that." |
| She budgets a scarce data allowance that is shared with her son, so any background data use is unacceptable. | Medium | "I plan my data like I plan rice." |
| Gendered and caste-based harassment through protest photos shared by people from her own town. | Medium | "Once my photo from a protest was shared in a men's group with dirty comments. Somebody from my own town did that." |

## Needs

- A full Tamil UI with big buttons and pictures, and no technical terms such as encryption, Tor or hub.
- Short, small voice notes as the main way to communicate, including voice notices on the board.
- Very light on resources: runs on 2 GB RAM, takes little storage, and never slows or crashes the government health app.
- No data use unless she explicitly allows it in that moment.
- Messages and notices that expire automatically, with the poster's name and time and the time left shown.
- An ordinary name and icon that she can open calmly in front of family, with a work-related explanation ("the department women use this for camp days").
- Notifications that reveal no sender or content on the lock screen or shade.
- Reliable reach across a crowd in one place, such as a sit-in.
- A simple "you know this person" mark for contacts verified face to face.
- A way for high-impact notices like a cancellation to count only when two trusted people confirm them.
- A private group for her trusted six, kept separate from the public board.
- A public board that shows no list of readers, present people or members.
- No single hub: hub duty rotates between people, carries no "Hub" or "Leader" label, and has power banks from the union fund.
- Introduction by a trusted local person at a small meeting before any wider use.
- Coexistence with paper: it must not become the only place where critical contacts are stored.

## Deal-breakers

- Requires an English account or English-language setup.
- Drains her battery in half a day.
- Makes the phone hang or slows or crashes the government health app, even once (immediate uninstall, and she will tell other women to do the same).
- Looks like a secret app (hidden icon, app lock, disguise, vanishing messages, visible panic button) and so causes trouble at home.
- Depends on one hub person carrying everyone, who then becomes the target.
- Uses mobile data in the background.
- Has no voice notes.
- Shows a list of who is present at or read a public notice.

## How BuzzChat fits

| Feature | Verdict | Note |
| --- | --- | --- |
| Phone-to-phone chat and posts over Bluetooth/Wi-Fi without internet or SIM | Partly | This directly addresses the cyclone-day coordination failure inside a gathered crowd. It does not reach groups 2 km apart or waiting in villages, which was the failure that actually cost them turnout. Continuous BLE scanning on an Android Go phone also threatens her one-day battery and her RAM headroom. |
| Join by QR or browser link, no install, no account, no phone number | Partly | No account and no phone number is a real benefit for women who cannot have an identity found on their phones. But browser-only users work only near a hub phone, and she has no data, so whoever runs the hub carries the battery and the risk. |
| Direct APK install from a friend's phone | Risk | Her household rule ("never install from outside, there are viruses") and her members' fears make sideloading a trust barrier. It also produces exactly the unexplained new icon her son will question. An official app-store listing or a trusted trainer would lower this barrier. |
| "Share to internet" via Tor when someone nearby is online | Partly | Her goal is visibility ("We want officers to see us"), and she currently depends on one local reporter, so getting news out matters. But anonymous posts carry little weight with officers or press, "Tor" means nothing to her, and if her phone becomes the bridge it spends the data she rations. Bridging needs a per-use opt-in with a visible data cost. |
| Area bulletin board with expiring notices | Partly | This was her favourite idea ("Venue changed to X, 10 am. Then it disappears"). As described, though, an open board lets anyone post "Protest cancelled", the same rumour that sent women home. To work for her it needs the poster's name and time, a known-person mark, voice notices, confirmation by two trusted people for cancellations, and no reader or presence list. |
| End-to-end encrypted private messages | Partly | A private group of her six, formed by meeting in person, matches how she already works. But her real adversary reads the unlocked phone and its notifications, not network traffic, so encryption does nothing against the husband or the supervisor's "show me your phone for the update". Without voice-note support it also fails her members. |
| Voice notes | Gap | Voice notes are not in the concept, yet they are her second must-have and the main medium for members who cannot type. A familiar voice is also how the women verify messages. They must be small, compressed clips that are practical over BLE mesh. |
| Animated QR screen-to-camera transfer when radio is jammed | Gap | This is irrelevant to her threat model, since her outages come from cyclones and tower failures, not jammers. It is fiddly on a low-end camera and adds complexity she would not use. It is low priority for this persona. |
| Emergency wipe button | Risk | A visible panic button signals "secret app", which is her biggest fear at home ("Why do you have a secret app?"). Her strategy is to be calm and able to open the app in front of family, not to destroy data. Automatic expiry of planning messages suits her far better than a panic wipe. |
| Bluetooth detectability (honest limit) | Risk | She pictures a young male staffer at the monthly review saying "somebody here has that protest app", followed by phone checks she cannot refuse. She also fears her son spotting a strange device name. (Repository check: the app's Bluetooth advertising includes no device name, but a phone acting as a hub creates a Wi-Fi network named `DIRECT-BC-...` that does appear in ordinary Wi-Fi lists.) Detectability stops mattering only if ordinary non-union people use the app too. |
| Hub-phone dependency | Risk | It concentrates battery drain and personal exposure on one person, which is one of her deal-breakers. She would accept the role only with rotation, power banks, no "Hub" or "Leader" label anywhere, and a work-related explanation family members accept ("helping with the camp phone"). |
| Resource footprint on Android Go (implicit) | Risk | The concept says nothing about RAM, storage or background behaviour. Her history of heavy updates breaking the health app means any memory pressure leads to instant uninstall and a warning to the whole group. |
| Language and UI (implicit) | Gap | The concept is described in technical English terms (Tor, end-to-end, hub, QR). She needs full Tamil, pictures and plain-language labels, or the women will not adopt it. |
| Notifications (implicit) | Risk | Default Android notifications that show a sender and preview would expose her to her husband the same day. Discreet notifications need to be the default, not a buried setting. |

## Design implications

1. Make notifications discreet by default: no sender name or content in the shade or on the lock screen, only a neutral line such as "1 new update", with an option to turn notifications off completely.
2. Design for being opened openly, not hidden. Use an ordinary name and icon, framed around community and camp-day use. Avoid disguised icons, single-app locks and conspicuous "secret" features. Every screen should be safe to show calmly to a family member.
3. Put the panic wipe in a settings menu, out of the main UI, and use automatic expiry of planning messages and notices as the main privacy mechanism.
4. Keep the Bluetooth advertisement free of any device name (as it already is), research rotating, less distinctive identifiers within bitchat compatibility (a scanner app can still recognise the fixed service UUID), and replace the distinctive `DIRECT-BC-` hub Wi-Fi name, which any family member can see in the Wi-Fi list. Pursue broad community use cases (disaster or cyclone notices, health camp announcements) so that having the app is not a union badge. Explain the remaining detectability honestly in plain Tamil.
5. Set a strict performance budget for Android Go: small APK, low RAM ceiling, tight storage caps, and no persistent background service by default. Test coexistence with a heavy foreground government app. The app must never cause another app to be killed or freeze.
6. Offer a time-boxed "meeting mode" (for example 2 to 3 hours) that switches the mesh on for a sit-in and then off again automatically, to protect battery and avoid background activity.
7. Set mobile data use to zero by default. Internet bridging and "share to internet" should need an explicit per-use opt-in with a visible data estimate.
8. Make voice the first-class medium: short, heavily compressed clips with a length cap, practical over BLE; voice notices on the board; push-to-talk with large controls.
9. Ship full Tamil localisation with pictures and large buttons, and replace all jargon with plain labels (for example "only your group can hear this" instead of "end-to-end encrypted"). Include Tamil voice onboarding.
10. On the board, show who posted, when, and how long the notice remains, plus a "you know this person" mark for contacts verified in person by QR. Visibly label notices from unknown posters as unconfirmed.
11. Support confirmation for high-impact notices: a cancellation or venue change shows "confirmed by 2 people you know" and stays marked unconfirmed until then.
12. Never show reader lists, read receipts, "who is nearby" rosters or member counts on the public board, so it behaves like a wall poster.
13. Remove expired notices from the mesh promptly so old notices stop circulating as rumours.
14. Remove the single hub: let any phone relay, support hand-off between rotating volunteers, and never label a device "Hub" or "Leader". Include guidance on power-bank use for union-funded relays.
15. Rate-limit and batch delivery of backlogs on reconnect so a burst of messages does not hang a low-RAM phone.
16. Never save received media to the shared gallery automatically, and strip metadata from any photos posted to the public board.
17. Distribute through channels people already trust (app store listing or browser) where possible, and back sideloading with a trusted local trainer and a simple plain-language integrity check.
18. Run adoption as a small in-person pilot led by a trusted local person, with Tamil training material built around "try it at the next sit-in, keep paper numbers too".
19. Set honest range expectations in onboarding: it works inside a crowd, not reliably between villages unless people are spread in between. Do not market it for inter-village reach.
20. Keep paper as the backup: do not require the app to be the only store of critical contacts, and do not keep a membership register that could be seized during a phone check.

## Interview transcript

### Q1. Who I am

My name is Lakshmi. I am forty years old. I live in a small town in Tamil Nadu, the kind of town where everyone knows whose daughter married whose son. For fourteen years I have worked as a health worker: home visits, pregnant mothers, vaccination days, TB tablets, BP checks for old people. They call us "volunteers," so the pay is an "honorarium." That word makes me laugh. It sounds respectable, but it means they can pay us late and pay us little.

My husband drives an auto. I have two children: my son is in Class 11, and my daughter has just started college. My mother-in-law lives with us. Morning I cook, then I go out on visits until afternoon. Evening I do the registers, and now the registers are also on the phone, so it is double work.

About six years ago I became a union leader. I never planned that. The other women pushed me forward because I talk loudly and I don't cry in front of officers. We protest for pending dues, for a fixed salary, and for the phone recharge allowance they promise and never give. Mostly we do sit-ins outside the block office. We sit, we sing, we give a petition. Sometimes the police come and take us in a van to a *kalyana mandapam* (wedding hall) and release us in the evening. My husband does not like it. He says, "Neeye ellaathukkum munnaadi poganuma?" (Do you have to be in front of everything?). Some days he is proud. Some days he is not.

### Q2. My phone

My phone is a cheap Android Go model. I paid around six thousand rupees for it, and my son chose it. It has 2 GB RAM, and the storage is always full. Every week I get the message "Storage almost full." WhatsApp photos are the problem: forwards from relatives, good-morning flowers, God pictures. I delete them, and they come back.

The government health app is also on it, and it is heavy. When it is open, WhatsApp hangs. I can't keep many apps. I have WhatsApp, YouTube (my mother-in-law watches serials on it), the health app, PhonePe, and the dialer. That's all.

Data is a prepaid plan. I recharge for about 300 rupees for 28 days, which is roughly 1.5 GB a day, but by evening my son has used it for his classes. I plan my data like I plan rice. Battery is maybe one day if I don't watch videos. I carry a small charger cable and charge at the health centre.

Many people use this phone. My son, my mother-in-law for serials, and sometimes my husband. He picks it up and scrolls through my WhatsApp, not every day, but enough.

I read Tamil well. My English is basic. I can read "OK," "Send," "Delete." Typing in Tamil is slow for me, so I mostly send voice notes. All the women in my union send voice notes. Half of them cannot type at all.

### Q3. My worst network failure

It was not a big government shutdown like Kashmir or Manipur. Ours was smaller, but for us it was big. We had planned a district-level sit-in, with women from many blocks coming. The night before, the cyclone rain came, power went off, and the towers in our area were down most of the next day.

- **Early morning, 6 am:** No signal at all. I had a list of block coordinators, but it lived only inside WhatsApp, and I had no written numbers.
- **7 to 9 am:** I walked to the bus stand because I heard one network was working there. It was not. Women who had already left their villages were calling me, and I couldn't receive the calls.
- **10 am:** We had about forty women at the site instead of two hundred. Other groups were waiting at the wrong place because the location had changed the night before and the last message never reached them.
- **Afternoon:** A rumour spread that the protest was cancelled and the police had arrested the leaders. Nobody had been arrested. But women went home, because they were scared.
- **Evening:** Network came back, and two hundred messages arrived at once. My phone hung. I had to restart it three times.

What did it cost? Our credibility with the officers, because they saw a small crowd. The women's bus fare, which is real money for them. And for weeks after, some women said, "Next time, confirm properly. We won't come on rumours."

Now I keep a paper diary with important numbers, and that is my workaround. Paper doesn't need a tower.

### Q4. Safety and fear

My biggest fear is not the police. It is home, and the supervisor.

The supervisor is a man from the department. He has told women, "If you join the union, your name will be noted." Some women's husbands check their phones and ask, "Who is this man messaging you?" when it is only a coordinator talking about dates. One woman was beaten at home because of union messages. So many women leave the group quietly. They come to protests but won't be in the WhatsApp group.

I delete chats after meetings, not all of them, only the planning ones. I keep the WhatsApp group name very boring.

There is gendered harassment too. Once my photo from a protest was shared in a men's group with dirty comments. Somebody from my own town did that. Caste matters as well. Some of our women are Dalit, and when they speak up, the insults are different and worse.

Who do I trust? About six women. Not everyone in the union. There are always one or two who carry news to the supervisor, and I know roughly who they are.

### Q5. Information problems

The rumours are terrible. "Salary hike announced," then nothing. "Protest cancelled," but it wasn't. "Leaders arrested," a lie. Fake government orders with a fake seal are easy to make on a phone.

I verify by calling someone I know at the district office, or by waiting for the newspaper. For getting news out, there is one local reporter who sometimes takes our photos, and if he doesn't come, we are invisible. Getting news in is mostly WhatsApp forwards, and you can't tell the truth from the lies.

### Q6. Reaction to the BuzzChat concept

*(Interviewer explained in Tamil and simple English.)*

**What excites me:** Messages going phone to phone with no tower. On the cyclone day, all forty of us were sitting together, and if messages could have hopped to the women at the wrong place... but wait. Were they near enough? You said tens of metres per hop. The other group was two kilometres away. So it would help inside the protest, not between villages, unless many people are in between. I am not sure.

The bulletin board that expires is good. "Venue changed to X, 10 am." Then it disappears. I like that.

No phone number and no account is good for women who can't have it found on their phones.

**What confuses me:** "Tor," "end-to-end," "hub phone." I don't know these words. If the women have to understand them, they won't use it. And scanning a QR code to open a browser: will it work on my phone with no data? You said only near a hub phone. Who will be the hub? Me? Then my battery dies.

**What scares me:** You said anyone scanning Bluetooth can tell the app is running. So the supervisor or the police can know I have it? Then having the app itself becomes a proof against me. That is a problem. Also, installing the app file from a friend's phone: my son always says never install from outside, there are viruses. Women will be afraid of that.

The emergency wipe button: good, but what if my husband sees the app and asks, "Why do you have a secret app?" A secret app looks like an affair. That is honestly my biggest fear. If it looks hidden, it creates suspicion at home.

**What would stop me:** If it is in English only. If it is heavy and makes my phone slow. If it uses data in the background. If there are no voice notes. If it drains battery.

**Before I trust it:** Someone I know, not a stranger from Chennai, has to show it to me and use it with us at one small meeting first. It needs to be in Tamil, and the name and icon should look ordinary.

**What I would tell others:** "Try it at the next sit-in, but keep paper numbers also."

### Q7. Must-haves and deal-breakers, in order

**Must-haves:**
1. Full Tamil, with big buttons and pictures, not words like "encryption."
2. Voice notes that are short and small in size.
3. Very light: works on 2 GB RAM and doesn't fill my storage.
4. Uses no data unless I say so.
5. Messages and notices that delete themselves after some time.
6. An ordinary-looking name and icon, so it doesn't look suspicious at home.
7. Works for a crowd at one place, like a sit-in.

**Deal-breakers:**
1. Needs an English account or setup.
2. Kills my battery in half a day.
3. Makes my phone hang or crashes the health app. That is my job, and I can't lose it.
4. Gets women into trouble at home because it looks like a secret app.
5. Needs one "hub" person to carry everyone. Then that person becomes the target, and it will be me.

## Follow-up interview

### Follow-up 1. You said your son, your mother-in-law and sometimes your husband all use your phone. Can you walk me through what happens on a normal day when someone else picks it up? What do they open, what do they see, and what would they notice if a new app appeared?

Morning, my son takes it for his online class. He opens YouTube and sometimes a learning app he installs and uninstalls. He is the one who notices everything. If a new icon comes, he says "Amma, what is this?" He isn't suspicious, he is curious, and he will open it to check. Afternoon, while I'm out, the phone is with me. Evening, my mother-in-law watches serials on YouTube. She only knows the red icon, and she won't notice anything else. At night my husband sometimes picks it up while I'm cooking. He opens WhatsApp, looks at who messaged last, and checks the photos in the gallery. He doesn't go deep into settings. But he does look at the notifications that come at the top. So if a new app shows a message like "Meena: tomorrow 9 am" on the screen, he will see it before I do. A new icon on the home screen, he'll notice in maybe two or three days. The notifications he'll notice the same day.

### Follow-up 2. You described a 'secret app' as something that could look like an affair. What would make an app on your phone seem normal to your husband, and what would make it seem hidden? Are there apps or ways of using the phone that you or other women already explain easily at home?

Normal means it is connected to work. Nobody at home questions the health app, because my husband knows that app pays our rice. The same goes for the attendance app, a training video app, or a self-help group app. If I can say "the department women use this for camp days," he says "seri" (okay) and keeps scrolling.

Things that look hidden: a lock on one app only, a fake calculator icon, or messages that vanish when he walks near. One woman in our block used one of those "hide WhatsApp" apps. Her husband found it, and that became a much bigger fight than the union messages would have been. Hiding is itself the evidence.

What we already do is keep group names boring, like "Block 3 Camp Duty," and use voice notes, because a husband hears the voice of a woman and relaxes. So the app has to be something I can open in front of him without my face changing. If I can show it calmly, that is safe. If I have to quickly close it, that is dangerous.

### Follow-up 3. On the cyclone day, the rumour that leaders had been arrested sent women home. If a message like 'Protest cancelled' or 'Venue changed' appeared on a notice board inside this app, how would you or the women decide whether to believe it? Whose voice or name would you trust, and whose would make you doubt it?

Typed words, I wouldn't believe. Anybody can type "cancelled." A voice is different. If it is my voice, or Selvi akka's, the senior from the next block, the women will believe it, because they know how we talk. We say the women's names, we mention the tea shop, small things a stranger wouldn't know.

I would doubt any message from a name I don't recognise, anything that sounds too official ("By order of..."), and anything that creates panic, like "arrested" or "police coming." Our rule should be that a cancellation only counts if two of the six of us say it. One voice is not enough for a cancellation.

It would help if the app showed something simple, like a mark next to people I have already met and added face to face. Not a technical word. Just "you know this person." And if the notice says "Venue changed," it should show who posted it and when. An old notice that is still floating around is also a kind of rumour.

### Follow-up 4. You mentioned there are one or two women in the union who carry news to the supervisor. If an app lets anyone nearby see messages on a shared notice board, how would that change what you post, or who you would let join?

Then the notice board is like a wall poster at the bus stand. I would only put there what I'm okay with the supervisor reading: time, place, "bring your ID card, bring water." Those things he will find out anyway. A protest is public. We want officers to see us.

The real planning (who will speak, what we will say if they offer a compromise, which women are scared at home) never goes on the open board. That is only for my six, in a private group I made by meeting them.

Honestly, I can't stop the one or two from joining the open part, and I don't want to start a fight inside the union by pushing them out. That creates more trouble. Better that they see only the boring information. What worries me is names. If the board shows a list of who is "there" or who read it, the supervisor gets a register of union women. That must not happen. Let the poster be seen, but don't show who stood and read it.

### Follow-up 5. When you heard that someone scanning Bluetooth could tell the app is running, what did you picture happening? Who would be scanning, and in what situation?

Honestly, I didn't picture police with machines. I don't know what such a machine looks like. I pictured the supervisor's office. Some of the young male staff there are very good with phones. I imagined one of them at the monthly review meeting, all of us sitting in one hall, looking at his phone and saying, "Ei, somebody here has that protest app." Then everybody looks at everybody. Then he asks to see our phones "for the health app update," and we can't refuse him, because he is the one who signs our forms.

The other picture was my son. He plays with Bluetooth speakers and earphones, and he sees the device names in the list. If the app shows some strange name there, he will ask about it at dinner, in front of his father.

So the fear isn't really technology. It is being singled out in a room. If many ordinary people have the app, like everyone has WhatsApp, then it means nothing. If only union women have it, it becomes a badge.

### Follow-up 6. You worried that whoever becomes the 'hub' becomes the target, and that it would be you. Thinking about a sit-in, which women, if any, could share that role, and what would they need (battery, phone, permission at home) to be willing to do it?

There are a few. Kavitha is about twenty-eight, newly in the job. She has a better Samsung phone her brother gave her, and her husband works in the Gulf, so nobody checks her phone. Fatima akka is a widow. She's very brave, but her phone is older than mine. Revathi's daughter studies computers, and she sometimes comes to sit-ins with her mother. Young people understand these things quickly.

What they would need:
- **A power bank.** Most of us don't own one, so the union could buy two or three from our fund.
- **Turns.** One hour Kavitha, one hour Revathi's daughter, so no single face is always the one.
- **No name shown.** The phone shouldn't be called "Hub" or "Leader" anywhere.
- **A reason at home.** "I'm helping with the camp phone" is something families accept.

And it has to be a choice. Fatima akka would do it for the cause, but if her son-in-law finds out, she has nobody to protect her. I won't push anyone.

### Follow-up 7. Your paper diary works when towers are down. What does it do well that you would not want an app to replace? And when has paper let you down?

Paper never says "Storage full." It doesn't need charging, and it doesn't need my son to fix it. My husband sees a diary full of numbers and thinks it is work, because my health registers look the same. I can hand a page to a woman who has no phone at all. Some older members still use button phones. And I can tear a page out.

The app shouldn't replace the "tear out" part, or the part where I decide what I write. Paper is also slow, and that is good. I think before writing.

When has it let me down? Rain. During the same cyclone season my bag got wet, and half the ink ran. Once I left the diary at the block office during a sit-in and spent the whole night worried about who had read it. Luckily, the peon's wife is one of us, and she brought it back. And paper can't tell forty women "venue changed" at once. For that I have to shout, or send someone running. So paper keeps the numbers, but it can't spread the news.

### Follow-up 8. Your work health app is the one you cannot lose. Has any app or update ever slowed it down or caused a problem with your work registers? What happened, and what did you do?

Yes, many times. Last year a big department update came during the vaccination week. The new version was much heavier. When I opened it, WhatsApp would close by itself, and sometimes the health app went white and I lost the entry I was typing. I had to enter one pregnant mother's details three times. The block office still marked our data as "pending," and in the review meeting they scolded us as if we were lazy.

Another time my son installed a game. The phone became so slow that the health app couldn't open the camera to upload the photo for the immunisation record. I deleted the game without asking him, and he sulked for two days.

What I do now: before vaccination days, I clear WhatsApp photos, close everything, and keep only the health app open. If something new is installed and the health app misbehaves, I remove the new thing first, no discussion. So if your app slows the health app even once, I will uninstall it that same day, and I'll tell the other women to do the same.
