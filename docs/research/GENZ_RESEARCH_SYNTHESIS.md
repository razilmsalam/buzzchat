# Gen Z protester research synthesis (simulated persona study)

> [!WARNING]
> **This is a simulated persona study, not field research.** Every person,
> name, age, place detail and quote in this document and in
> `docs/research/personas/genz-*.md` is **fictional and synthetic**. The six
> personas were written to generate design hypotheses for BuzzChat. They are
> not evidence about any real person, community, movement or organisation.
>
> Real public events are used only as general background: the 2026 Cockroach
> Janta Party (CJP) youth protests in India, the 2025 Gen Z protests in Nepal
> and Morocco, the 2024 student protests and shutdown in Bangladesh, the 2024
> #RejectFinanceBill2024 protests in Kenya, and earlier mesh-app use in Hong
> Kong. The personas take part in a movement *modelled on* public reporting of
> the CJP protests. Their first-person incidents (fake backup accounts, forged
> letterheads, a heatstroke case, a missed gathering) are illustrative
> composites, not verified facts, and must not be quoted as reporting. No
> statement here is attributed to a real individual or organisation, nothing
> here describes Indian law, and nothing here is advice on evading anyone.
>
> **Nothing in this document should ship as a decision until it has been
> checked with real, consenting participants**, using the validation protocol
> in [PROTESTER_RESEARCH_SYNTHESIS](PROTESTER_RESEARCH_SYNTHESIS.md#validation-protocol-minimum-bar-before-acting-on-these-findings).

## Validation note

This study follows the same protocol as the first persona set. Three extra
points apply to young participants and online movements:

1. **Do not recruit minors.** All six personas are 19 to 24. Reach younger
   people's needs through teachers, families and youth workers, never directly.
2. **Do not recruit through movement pages, Discord servers or comment
   threads.** Those spaces are watched, impersonated and full of phishing
   (the personas' own top problem). Recruit through intermediaries the
   participant already trusts, as in the first study.
3. **Career and family risk is the main harm to protect against.** Several
   personas fear an employer background check, a placement cell, a
   professional registration or a family member more than the police on the
   day. Session notes must never contain anything that could reach those
   people: no college, employer, course, page name, follower count or handle.

---

## Summary

Six fictional Gen Z personas reacted to the same BuzzChat concept as the
first set: the mesh chat, join by QR or link, APK sharing, the browser
client, "share to internet" gateway posts over Tor to Nostr, an expiring
area bulletin board, a movement signing key ("seal"), E2E DMs, small images,
animated QR bundles and the emergency wipe. Six hypotheses stood out:

1. **The seal is the product, and as specified it is not safe yet.** The
   three trust problems (forged notices and lookalike accounts, a seal check
   that ordinary people cannot do in one glance, and single-holder keys in a
   movement that has already split) together score 34 of a possible 54. Every
   persona named impersonation and five ranked it critical. Four warned that
   a single-holder key would turn today's fakes into "signed fakes, which is
   worse than unsigned ones" (Meera).
2. **These personas want the message loud and the person invisible.** The movement
   runs on reach, faces, crowds and reels; each persona wants none of that to
   point back to them ("shout without a face", Sana). That is a different
   shape from the first set's "ordinary, not hidden" low profile.
3. **Bluetooth detectability is still the top personal risk.** All six rated
   it a risk (16 of 18). They accept it only with an honest, plain-language
   radio status, radio off by default, and a timed event mode with a battery
   estimate. "One lie and I uninstall" (Kabir).
4. **Reach lives on Instagram and WhatsApp, not Nostr.** Five of six said
   nobody they know is on Nostr; posting there feels like "writing in a
   diary" (Sana). BuzzChat is valued
   as "mesh for the blackout, mainstream for reach afterwards" (Meera), which
   means signed content must survive being exported and screenshotted.
5. **Content identifies people even when names are hidden.** Voice, hands,
   backgrounds, spelling habits, watermark fonts, posting times, EXIF and
   faces of bystanders or patients. Tor hides the network sender, not these.
6. **First impressions decide adoption.** A dark "hacker movie" look, English
   jargon ("mesh", "relay", "Tor", "Nostr"), a stranger's "hi", an empty
   board, or a battery-stats appearance was each, for at least one persona,
   enough to uninstall the same day.

## Context from public reporting

This is a condensed summary of public reporting gathered before the study. It
is general background only. Several sources were seen only as search
snippets, and items marked *unconfirmed* rest on a single source. Follower
counts are self-reported platform metrics.

### India: the CJP youth protests, 2026

- **Origin.** In May 2026, India's Chief Justice was reported to have called
  some unemployed young people and activists "cockroaches" and "parasites"
  during a hearing; he later said he meant people with fake degrees and had
  been misquoted. On 16 May 2026 a political communications strategist
  started the CJP online as satire of the ruling party, with the tagline
  "Voice of the Lazy & Unemployed". Reporting describes it as not registered
  with the Election Commission.
- **Demands.** An early manifesto focused on institutions (no parliamentary seat
  for retired judges, reservation for women, media reform, a ban on legislators who
  switch parties). After the medical entrance exam (NEET-UG) was voided over
  leaked questions, with more than 2 million students to retake it, demands
  centred on the Education Minister's resignation, an end to paper leaks,
  compensation for affected students and for families of aspirants who died
  by suicide, and withdrawal of cases against protesters. The underlying
  anger is about unemployment and economic pressure.
- **Scale.** The Instagram account reportedly passed 15 million followers in
  its first week and later about 20-22 million (sources give different
  figures). Protests at Jantar Mantar in New Delhi ran from June to 25 July;
  sources differ on the start date (6 June or 20 June). Estimates for the 20
  July march on Parliament range from "hundreds" to "tens of thousands"; no
  single reliable figure exists. Solidarity protests took place in more than a
  dozen Indian cities and some cities abroad.
- **How it organised.** Online: Instagram, X and the party's website (reportedly
  built with AI tools), which also links YouTube, Reddit, WhatsApp, Telegram
  and Discord. No reporting found describes how those groups were run.
  Offline: clean-up drives in costume, sit-ins, hunger strikes, a march to
  Parliament, courting-arrest protests, and cultural protest (anthem singing,
  banging plates, hip-hop). Volunteers collected and handed out donated food and
  water (some reporting mentions delivery apps; this is not well sourced);
  medical volunteers worked on site for about a month; lawyers filed public-interest petitions on police force and
  surveillance.
- **Outcomes.** The Education Minister resigned on 25 July and the CJP ended
  that phase. The government introduced a bill to amend the public
  examinations law with higher penalties and announced a reform task force;
  fast-track courts, compensation and case withdrawals were reported in some
  sources but are *not confirmed* in the fuller ones. The
  CJP later said commitments were not met. A breakaway faction formed in
  September. A "Season 2" protest was planned in Delhi around 10 October; some
  volunteers travelling to it were reportedly detained. One report says
  founders were detained and mobile internet was blocked (*unconfirmed*; other
  October reporting does not mention it).
- **Disruptions and contested claims.** The official X account was withheld
  in India on 21 May after a government legal demand whose reasons stay
  confidential; the party returned within hours under a new handle, and in
  July a court ordered the account restored. The Instagram account was not
  blocked. Internet services were reported shut down near Parliament during the
  20 July march and several central Metro stations were closed; who ordered
  the shutdown, how long it lasted and how far it reached are poorly
  documented. Claims about foreign followers (including one that about half
  the followers were in Pakistan) and foreign funding were made and disputed;
  audience data shared by the party showed most followers were in India; reporting says courts dismissed petitions seeking central-agency
  probes. Police and organisers gave conflicting figures on injuries and
  detentions. The founder reportedly received death threats over WhatsApp.
- **Not found.** No specific fact-checks about fake or impersonation accounts
  were found. Treat that area as unresearched, not as absent.

### Comparisons with recent Gen Z movements

- **Nepal (September 2025).** Triggered by a block on 26 unregistered
  platforms. After deadly clashes the Prime Minister resigned and the ban was
  lifted. A Discord server with more than 100,000 members worked as an
  informal "parliament" and held a vote that helped pick an interim prime
  minister. Organisers ran their own fact-checking, but the server also
  carried misinformation and some calls for violence, and moderators admitted
  it was not representative.
- **Bangladesh (July-August 2024).** Described by OONI as the most severe
  shutdown in the country's history: mobile data cut around campuses, a
  nationwide mobile and broadband blackout from 18 to 23 July cut upstream at
  gateways, then throttling, layered platform blocks and VPN blocking. Some
  2G voice and SMS stayed on. Commentators recommended mesh apps; **no
  confirmation was found that mesh apps were widely used.**
- **Kenya (June 2024).** Organising ran through X, TikTok and Instagram, with
  X Spaces town halls and a youth-built chatbot explaining the bill. A major
  national network disruption was recorded on 25 June and attributed by the
  operator to undersea cables, which many doubted. Reports of specific
  walkie-talkie app use are unverified. The bill was withdrawn amid deadly
  crackdowns.
- **Morocco (GenZ 212, September-October 2025).** Anonymous collectives
  organised mainly on Discord, published a manifesto there and recruited local
  coordinators to stop outside groups claiming leadership.
- **Hong Kong (2014, 2019).** Protesters used FireChat and Bridgefy. Security
  researchers later found spoofing and confidentiality flaws in Bridgefy, with
  untrusted relay phones as the key concern.

### Patterns relevant to BuzzChat

- Large open Discord servers end up working as assemblies, but they are hard
  to moderate and hard to verify.
- Satire and meme branding grow fast; platform takedowns against them are
  blunt and quickly worked around, which creates a window for impersonators.
- Shutdowns tend to be layered (local cuts, throttling, platform blocks, VPN
  blocks) and are often denied or blamed on technical faults.
- Misinformation and contested numbers come from all sides. Account
  authenticity and verifiable channels matter.
- Mesh apps are recommended far more often than they are shown to be used at
  scale. Their known weak points are trust and spoofing.

### Sources

Pages fetched in full unless marked. Both Wikipedia articles carried
maintenance notices about unverified text at the time of reading, and their
figures sometimes differ from each other and from news reports; where they
disagree, this summary gives a range or marks the point as uncertain.

- https://en.wikipedia.org/wiki/Cockroach_Janta_Party
- https://en.wikipedia.org/wiki/2026_Delhi_Jantar_Mantar_protests
- https://time.com/article/2026/07/22/india-cockroach-janta-party-protest-movement/
- https://www.aljazeera.com/news/2026/7/20/police-attack-cockroach-activists-as-thousands-march-on-indian-parliament
- https://www.aljazeera.com/news/2026/7/24/indias-cockroach-leaders-hold-talks-with-govt-as-protests-continue
- https://www.npr.org/2026/07/25/g-s1-135483/india-cockroach-movement (search snippet only)
- https://eastasiaforum.org/2026/10/10/indias-youth-protests-test-the-bjps-hold-on-a-new-generation/ (search snippet only)
- https://www.cnn.com/2026/10/09/india/india-cockroach-party-protests-season-2-intl-hnk (search snippet only)
- https://www.medianama.com/2026/05/223-x-withholds-cockroach-janta-party-account-india-days-after-launch/ (search snippet)
- https://scroll.in/latest/1092997/scroll_in (search snippet)
- https://www.jagonews24.com/en/international/news/94448 (search snippet)
- https://www.newsbytesapp.com/news/india/four-cockroach-janta-party-volunteers-detained-by-bhopal-security/tldr (search snippet)
- https://www.cockroachjantaparty.org/ (search snippet)
- https://en.wikipedia.org/wiki/2025_Nepalese_Gen_Z_protests (search snippet)
- https://cjr.org/the_media_today/discord-nepal-protests-social-media-ban-discord.php
- https://wionews.com/world/year-ender-nepal-politics-saw-a-tectonic-shift-after-gen-z-protests-of-2025-1766048960620 (search snippet)
- https://ooni.org/post/2025-bangladesh-report/
- https://thedailystar.net/node/3659521 (search snippet)
- https://www.csis.org/analysis/taking-charge-gen-z-leads-historic-protests-kenya (search snippet)
- https://capitalfm.africa/from-stones-to-smartphones-how-gen-zs-are-transforming-protests-in-kenya/ (search snippet)
- https://internetwithoutborders.org/kenya-internet-disruptions-and-anti-government-protests/ (search snippet)
- https://en.wikipedia.org/wiki/2025_Moroccan_Gen_Z_protests (search snippet)
- https://siliconrepublic.com/?p=837857 (search snippet)
- https://forbes.kz/articles/hong_kong_protesters_are_using_this_mesh_messaging_appbut_should_they_trust_it (search snippet)

---

## Gen Z persona index

| Key | Persona (fictional) | Region and context | One-line summary |
| --- | --- | --- | --- |
| [genz-meme-admin](personas/genz-meme-admin.md) | Kabir, 21, B.Com student and co-admin of a satirical Bhojpuri/Hindi meme page | Tier-2 city, Bihar; local solidarity sit-in | Would use BuzzChat on "the one bad day" and for official posts that can't be faked. Won't move his reel-driven page off Instagram, and uninstalls if Bluetooth gives him away, the battery drains, or "hidden author" leaks who he is. |
| [genz-neet-aspirant](personas/genz-neet-aspirant.md) | Sana, 19, NEET aspirant and first-time protester | Small town, Rajasthan; one sit-in and two days in Delhi | Wants to "shout without a face": no number, no strangers able to message her, Hindi first, and boring enough to survive her father's near-daily phone checks. |
| [genz-volunteer-medic](personas/genz-volunteer-medic.md) | Arjun, 20, paramedical (emergency care) diploma student and volunteer coordinator | Kolkata; a week with a medical team in Delhi | Wants a sealed, expiring needs-and-shifts board and identity-free patient handover notes. Adopts only if leads test it first on cheap phones and it never shows his location. |
| [genz-factchecker](personas/genz-factchecker.md) | Meera, 23, software tester and volunteer fact-checker | Bengaluru; verification Discord, not a marcher | For her the seal is the whole product. Would tell people *not* to use BuzzChat if it ships single-holder keys, forgeable QR posters, always-visible Bluetooth or a second-class iPhone experience. |
| [genz-gig-worker](personas/genz-gig-worker.md) | Imran, 24, food-delivery rider | Hyderabad; short sit-ins after shifts | Would scan a sealed "official updates" QR to escape fake forwards. Drops it if it costs him orders, looks like a "protest app" at a checkpoint, or asks him to read paragraphs. |
| [genz-queer-student](personas/genz-queer-student.md) | Ritu (she/they), 22, communication design student and poster-kit maker | Pune; family in Marathwada | Wants "a clipboard that works when the network doesn't": verified notices and expiring boards in Marathi, not another social network. Uninstalls the same evening if it outs her, drains her battery or opens to an empty board. |

## Method and limits

- **Inputs.** Each persona is a synthetic interview with a follow-up,
  structured as key problems, needs, deal-breakers, BuzzChat fit and design
  implications. The concept matched the first study's
  ([principles](../BUZZCHAT_PRINCIPLES.md),
  [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md),
  [OFFLINE_BUNDLE_V1](../OFFLINE_BUNDLE_V1.md),
  [WEB_JOIN_V1](../WEB_JOIN_V1.md)) plus one addition the first set did not
  see: a **movement signing key** whose seal is mirrored on Nostr.
- **Scoring.** Same as the first study: each persona's *Key problems* table
  is scored critical = 3, high = 2, medium = 1, not raised = 0, taking the
  highest severity when a persona has several rows on one problem. Maximum is
  18 (all six critical). A few rows name two problems at once (for example
  Kabir's "no video and few people reach it") and count toward both. Problems
  a persona raised only in needs or fit notes are listed but not scored. The
  score is a way to prioritise, not a measurement.
- **Persona initials** in tables: K Kabir, S Sana, A Arjun, M Meera, I Imran,
  R Ritu. They are local to this document and differ from the first set's.
- **Known biases.** The same team wrote the product and the personas. All six
  are urban or small-town young adults with their own smartphones, literate in
  at least one language, and connected to one exam-leak-driven movement. Four
  of six are students. Nobody here is disabled, Adivasi, from the
  Northeast, a minor, without a phone, opposed to the movement, or a
  movement leader who would actually hold a signing key. Five of the six
  have a close family member hit by an exam-paper leak; that is a writing
  choice that makes the set more alike than real participants would be, not
  a finding. Gen Z is not one group; these six are not a sample of it, and
  "Gen Z" in headings below is shorthand for these six fictional personas.
- **Concept vs. code.** Some gaps already partly exist in the repository; see
  the table below.

### What the app and specs already have (checked against the repository)

| Persona assumption | Repository reality | Implication |
| --- | --- | --- |
| "It looks dark and green like a hacker movie" (Sana) | The dark scheme in `app/src/main/java/com/bitchat/android/ui/theme/Theme.kt` uses a black background with green primary and green-tinted surfaces; a light scheme also exists | The light scheme is a starting point, not the "school notice" look Sana and Ritu asked for. `app_name` is still `bitchat` |
| "Notifications will out me" (Ritu, Sana) | Private-message notifications use `VISIBILITY_PRIVATE` in `app/src/main/java/com/bitchat/android/ui/NotificationManager.kt`: hidden on the lock screen, but sender and text show in the shade when unlocked | Same gap as the first study's household threat; now raised by two more personas |
| "Images carry my phone's details" (Sana, Meera, Ritu) | Mesh images are re-encoded by `app/src/main/java/com/bitchat/android/features/media/ImageUtils.kt`, which drops EXIF as a side effect. The generic file path keeps EXIF ([MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md), *Media*). There is no face blur and no user-visible promise | Make stripping a stated guarantee on every image path, and say plainly what it does not remove (faces, style, watermarks) |
| "Posting times will give me away" (Kabir) | Gateway posts already jitter `created_at` by 0-900 s and the outer timestamp by ±60 s ([MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md), *Privacy*) | Partly covered for single posts. Not covered: bursts of queued posts released together when a shutdown ends, and a user's daily rhythm across many posts |
| "Browser users will think it's broken 50 m away" (Kabir, Imran) | [WEB_JOIN_V1](../WEB_JOIN_V1.md) *UI requirements* already require "Lost the host. You can't send right now." instead of a spinner | Covered for connection loss. Not covered: a first scan with no hub in range at all |
| "A fake poster QR leads to a lookalike board" (Meera, Sana) | [WEB_JOIN_V1](../WEB_JOIN_V1.md) *Poisoned code* lists the evil-twin risk and a "Check this host" audit for app users | That audit checks the hub's *code*, not whether the *board or movement* is the real one. The lookalike-content attack is unaddressed |
| "There are no voice notes" (Imran) | The Android app has voice notes (`app/src/main/java/com/bitchat/android/ui/media/VoiceNotePlayer.kt`) | Missing from the browser client, gateway posts, bundles and board notices, as the first study found |
| "My camera is cracked" (Sana) | [OFFLINE_BUNDLE_V1](../OFFLINE_BUNDLE_V1.md) *QR profiles* already offers "Use bigger squares" | Partly covered for bundles; join-by-QR has no fallback for a camera that cannot scan |
| "Wipe is a hidden gesture" (Sana, Ritu) | The chat header has a triple-tap handler (`app/src/main/java/com/bitchat/android/ui/ChatHeader.kt`); the web client shows a visible "Wipe" ([WEB_JOIN_V1](../WEB_JOIN_V1.md)) | Both personas want a labelled screen listing everything kept, not a gesture |
| "No Marathi / Telugu / Bhojpuri" | `values-hi`, `values-bn` and `values-ur` exist with about 59-60% of the base strings each. There is no `values-mr`, `values-te`, `values-kn` or Bhojpuri locale | Hindi, Bengali and Urdu need finishing; Marathi and Telugu are missing; Bhojpuri has no standard Android locale tag and needs a decision |
| "iPhone users are second-class" (Meera) | [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md) Rule 2 says iPhones run bitchat from the App Store as full mesh nodes | Chat works, but the BuzzChat-only features (seal checks, board, gateway posts) need upstream bitchat iOS support or they do not exist on iPhone. That is the real gap |
| "Any stranger can DM me" (Sana) | Inherited from bitchat: any peer can open a private chat with any visible peer | Confirmed; there is no request-only mode |

## Device and platform landscape

| Persona | Main device | Storage | Data | Power | Where their audience is | Scripts needed |
| --- | --- | --- | --- | --- | --- | --- |
| Kabir | Mid-range Android, ~Rs 14k on EMI, 2 yrs | 128 GB, full (video-editor drafts) | 2 GB/day, gone by evening on posting days | Lasts to ~4 pm when editing | Instagram reels and carousels; YouTube Shorts; WhatsApp admin groups | Devanagari (Hindi), Bhojpuri |
| Sana | Budget Android, ~Rs 9k, 2 yrs, **cracked screen and camera** | 64 GB, full (lecture PDFs) | 1.5 GB/day prepaid, shared by hotspot with siblings, gone after 7 pm | Dead by ~4 pm | Instagram and YouTube (watch only); muted WhatsApp group | Devanagari (Hindi), Marwari |
| Arjun | Mid-range Android, ~Rs 13k | 128 GB, full | 1.5 GB/day, gone by evening on site | ~20% by 4 pm; power bank | WhatsApp rota; Instagram stories | Bengali, Devanagari, Latin |
| Meera | **iPhone 15**, on EMI; cannot sideload | 128 GB, full (evidence screenshots) | Postpaid plus office Wi-Fi | Lasts to ~7 pm on heavy days | X threads; Instagram carousels; Discord | Latin, Kannada, Devanagari |
| Imran | Budget Android, ~Rs 9k, worn battery; **the phone is his work tool** | 64 GB, full | 1.5 GB/day, 2G speed by evening | 30% by 3 pm; power bank | WhatsApp forwards, voice notes, reels | Telugu, Devanagari, Urdu (right-to-left) |
| Ritu | Budget Android, 4 yrs | 64 GB, full | 1.5 GB/day, gone by evening | 20% by 4 pm; power bank | Instagram (public account and private second account); WhatsApp groups | Devanagari (Marathi, Hindi), Latin |

### What this means for "works on whatever device people already hold"

1. **The phone is a creative studio, a work terminal and a family device at
   once.** Kabir's storage is video drafts; Imran's phone navigates and takes
   orders all day; Sana's father picks hers up almost daily. Rule 2 in
   [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md) should treat "a phone that
   is someone's income" and "a phone that others check" as device situations
   with their own rules (no background cost; nothing that outs the user).
2. **Budgets are explicit and small.** Ritu offered about 5% battery per
   protest day and 100-150 MB; Arjun 25-30% per afternoon at most; Kabir
   switches it off himself at 30%; Imran says any relaying during a shift is a
   deal-breaker. No spec yet sets a battery, storage or APK budget (the first
   study recommended one at P1; these personas push it to a release gate).
3. **iPhone parity is about features, not chat.** Meera can install bitchat,
   but she joins for the seal and the board. Until upstream bitchat iOS can
   verify seals and show board notices, an iPhone user is back on the
   hub-dependent browser path.
4. **Cracked cameras and full storage are normal.** QR join and animated QR
   assume a working camera; at least one persona's camera would not scan.
5. **Everyone runs on two or three mainstream platforms.** None uses Nostr;
   two use neither X nor Discord. Anything BuzzChat publishes has to be
   exportable into WhatsApp and Instagram to matter to them.

---

## Cross-cutting problems, ranked

Score = sum of per-persona severity (critical 3, high 2, medium 1). Maximum 18.

| # | Problem | Score | Personas (severity) | Representative quote |
| --- | --- | --- | --- | --- |
| 1 | **Impersonation, forged notices and lookalikes.** Fake "backup official" accounts after a takedown, forged letterheads ("cancelled", "tent moved"), lookalike pages with donation QR codes, fake poster QR codes leading to lookalike boards | 17 | K3 A3 M3 I3 R3 S2 | "Five 'backup official' accounts appeared within hours. Only one was real. One of the fakes asked for donations to a UPI ID." (Kabir) |
| 2 | **App presence marks the user.** Anyone scanning Bluetooth can tell the app is running; a visible "protest app" at a checkpoint, college gate or hostel | 16 | K3 S3 I3 R3 A2 M2 | "So the app on my phone is itself evidence? That scares me more than the meme does." (Kabir) |
| 3 | **Content identifies people when names are hidden.** Voice, hands, backgrounds, spelling and writing style, watermarks, posting times, EXIF; faces of bystanders and patients | 13 | K3 S2 A2 M2 I2 R2 | "The time I post: if every meme drops at 11:40pm right after cyber cafe closing, someone can join the dots." (Kabir) |
| 4 | **Battery, data and storage.** Old batteries, full storage, rationed data; relaying costs a rider his orders | 12 | I3 K2 S2 A2 R2 M1 | "If Bluetooth and Wi-Fi are always on and it's relaying other people's messages, my phone dies by 1pm and I lose orders." (Imran) |
| 5 | **Verification must be one glance, loud on fakes, and survive forwards.** A missing tick goes unnoticed; seals mean nothing once screenshotted into WhatsApp; nobody reads fingerprints | 9 | M3 S2 A2 I2 | "It needs to work in one look at a red light... I can't read paragraphs. I have ten seconds." (Imran) |
| 5 | **The audience is on Instagram and WhatsApp; Nostr reaches nobody** | 9 | K2 S2 M2 I2 R1 | "If my post goes somewhere nobody sees it, it's like writing in a diary." (Sana) |
| 7 | **Seal key governance.** One holder detained or a key copied; factions with rival valid seals; no rotation or continuity when a platform disappears | 8 | M3 K2 R2 I1 (A in needs) | "If one founder holds it and gets detained, or the key gets copied, then the fakes become signed fakes." (Meera) |
| 8 | **Crowd networks collapse; urgent and lost-friend messages fail** | 7 | A3 K2 S2 | "She typed 'need stretcher + cold water' into the rota group. One tick." (Arjun) |
| 8 | **Movement culture is video-, voice- and image-first; BuzzChat is text-first** | 7 | K2 I2 S1 M1 R1 | "A meme page without reels is a newspaper." (Kabir) |
| 8 | **No everyday value, cold start, network effects.** Nobody nearby at home; an empty board; installing alone | 7 | A2 I2 R2 S1 | "Signal didn't stick because nobody was there. Installing it alone is like reaching a party two hours early." (Arjun) |
| 8 | **Jargon, missing languages and a "hacker" look** | 7 | S2 I2 R2 K1 | "It looks dark and green like a hacker movie... Mummy will definitely think so." (Sana) |
| 12 | **Household phone checks and outing.** App icon, notification previews, gallery, group names | 6 | S3 R3 | "The gallery and notifications are where I'd get caught." (Ritu) |
| 12 | **Harassment, unsolicited contact and board abuse** | 6 | S3 R2 M1 | "Can boys message me on it? If random men can DM, they won't touch it." (Sana) |
| 14 | **Fragile, unequal coordination tools.** Sheets anyone can wipe, admin burnout, requests that assume one person fills them | 5 | I2 R2 K1 | "Volunteer coordination was 40 WhatsApp groups with the same five people admin in all of them." (Ritu) |
| 14 | **Retention, wipe and self-incriminating archives.** Panic deletion spills into personal files; evidence folders look like guilt; memories vs. wipe | 5 | M2 S1 I1 R1 | "The archive itself is the most dangerous thing I own." (Meera) |
| 16 | **Stale information.** Old hazards and met needs keep circulating | 4 | K2 A2 | "Old news that looks new is just another kind of fake." (Kabir) |
| 16 | **Image provenance.** Recycled photos; "is this from today?" | 4 | K2 M2 | "People asked 'is this from today?' and we couldn't say." (Meera) |
| 18 | **Platform dependence.** Accounts withheld, new handles, phishing takeovers | 3 | K2 A1 | "Someone tried a phishing 'Meta Verification' link on Ritesh." (Kabir) |
| 18 | **iPhone users are second-class** | 3 | M3 | "That makes me a second-class user, and most of my friends are iPhone or high-end Android." (Meera) |
| 20 | **Distrust of sideloaded APKs; clean uninstall** | 2 | I2 | "No APK from somebody's phone." (Imran) |
| 20 | **No small "we're safe" message for families far away** | 2 | K2 | "One line saying 'safe' was worth more than any video." (Kabir) |
| 22 | Saying "where" without GPS (landmarks, named zones) | 1 | A1 | "The big cardboard cockroach banner, next to the barricade with the blue tarp." (Arjun) |
| 22 | Overclaiming in marketing ("uncensorable") | 1 | M1 | "Nothing is. Say 'harder to take down'." (Meera) |
| 22 | Intimidation by unverifiable legal threats | 1 | K1 | "Maybe it was fake. I couldn't tell. I couldn't sleep for two days." (Kabir) |
| 22 | Creator attribution and per-copy poster tracking | 1 | R1 | "One code for everyone, the same everywhere." (Ritu) |

**Reading the ranking.** Rows 1, 5 and 7 are one problem seen from three
sides: who is really speaking for the movement. Together they score 34 of 54,
far above anything else. Rows 2, 3 and 12 are also one problem: being
identified, by the radio, by the content or by someone holding the phone.

---

## Feature-fit matrix

**Solves** = meets the need as described. **Partly** = useful with significant
changes. **Gap** = not offered, or unusable by this persona. **Risk** = could
harm this persona as described. **—** = not rated by this persona.

| Capability (spec) | Kabir | Sana | Arjun | Meera | Imran | Ritu |
| --- | --- | --- | --- | --- | --- | --- |
| Mesh chat over BLE / Wi-Fi | Partly | Partly | Partly | Partly | Partly | Risk |
| QR / link join, no account ([WEB_JOIN_V1](../WEB_JOIN_V1.md)) | Partly | Solves | Partly | Risk | Partly | Solves |
| APK sharing from a friend's phone | Partly† | — | Partly† | Gap | Risk | — |
| Share to internet: Tor gateway posts to Nostr ([MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md)) | Risk | Partly | Risk | Partly | Gap | Gap |
| Expiring area bulletin board (no spec yet) | Solves | Solves | Solves | Partly | Partly | Partly |
| Movement signing key / seal on Nostr (no spec yet) | Partly | Partly | Partly | Partly | Solves | Partly |
| E2E DMs (Noise) | Partly | Risk | Partly | Partly | Partly | Partly |
| Small images over mesh | Partly | Partly‡ | Risk | Risk‡ | Gap‡ | Partly |
| No video over Bluetooth (stated limit) | Gap | Partly‡ | Partly | Risk‡ | Gap‡ | Gap |
| Animated QR bundles ([OFFLINE_BUNDLE_V1](../OFFLINE_BUNDLE_V1.md)) | Gap | Gap | Partly | Partly | Partly | Gap |
| Bluetooth presence detectable (stated limit) | Risk | Risk | Risk | Risk | Risk | Risk§ |
| Emergency wipe | — | Partly | — | — | — | Partly |

† Rated together with QR / link join. ‡ The persona rated images and the
no-video limit as one item; the verdict is repeated in both rows. § Ritu's
mesh verdict gives detectability as the reason.

**How to read the matrix.**

- The **board** is again the strongest capability: three Solves and three
  Partly, with no Risk. It still has no spec.
- The **seal** is everyone's favourite idea, yet only Imran, who needs a
  one-glance green state and nothing more, rates it Solves. The other five
  rate it Partly for the same reasons: key custody, factions, one-glance
  checks and screenshots.
- **Join with no account and no number** is again the clearest win. Meera's
  Risk is about poster phishing, not the absence of an account.
- **Share to internet** has no Solves. The objection is audience and
  self-identification, not the cryptography.
- **Detectability** is a unanimous Risk, matching the first study.

### Asked for, but not in the concept

| Capability | Asked for by | Nearest existing spec or code |
| --- | --- | --- |
| Honest radio status ("Bluetooth broadcasting: OFF") and a timed event mode with a battery estimate, off by default | K S A M I R | *Power* in [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) and [WEB_JOIN_V1](../WEB_JOIN_V1.md) |
| Seal governance: threshold signing, scoped and expiring role seals, rotation endorsed by the old key, revocation, visible forks, sealed corrections | K A M I R | None. Per-post keys in [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) *Author key* are deliberately unlinkable |
| Verification outside the app: share cards with a verify code, a "check a forward" flow, a standalone verifier | K S A M I R | None |
| Loud "NOT from organisers" banner on unsealed posts using official-sounding words or lookalike names | S A M I R | None |
| Pre-share safety check: stated EXIF removal, optional face blur, background-text and voice warnings, style and watermark caveat | K S A M R | Re-encode in `ImageUtils.kt`; [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) *Media* |
| Request-only DMs, counts instead of names in nearby lists, random neutral nicknames | S I R | None |
| Neutral name and icon, notifications with no sender or text even when unlocked, no gallery writes | S A I R | `NotificationManager.kt`; `app_name` |
| Board extras: partial pledges, "resolved", live headcount, named zones instead of GPS, add-only history, rate limits, moderation | K A I R | None |
| Urgent request type with a visible "received / on my way" acknowledgement | A | None |
| "We're okay" check-in, lost-friend alert, agreed meeting point | K S | First study's courier DM (P2) |
| Message age shown as sent and received time; staleness rules per notice type | K A | [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) *Expiry and clock skew* |
| Image provenance: "first seen here", "seen before", sealed or relay-attested time | K M | None |
| Short sealed voice notes (under 40 s) and short low-bitrate audio clips | I M | Voice notes exist in the Android app only |
| Slow, compressed short video nearby | K (M, R ambivalent) | None; video is a non-goal in [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) |
| Medical handover card keyed to a wristband code, auto-deleted | A | None |
| Hub mode for a spare phone on a power bank | A | Hub power rules in [WEB_JOIN_V1](../WEB_JOIN_V1.md) *Power* |
| Seal checks and board on iPhone | M | Principles Rule 2 (bitchat iOS) |
| Store or website distribution with checksums; clean uninstall | I | *Install the app* row in [WEB_JOIN_V1](../WEB_JOIN_V1.md) *Poisoned code* |
| Marathi, Telugu and Bhojpuri; finished Hindi, Bengali and Urdu | K S A I R | `app/src/main/res/values-*` |
| Everyday positioning (campus outages, fests, floods, power cuts) | A I S | None |
| "Everything BuzzChat keeps" screen with an app-only clear | S I R | Panic clear; [OFFLINE_BUNDLE_V1](../OFFLINE_BUNDLE_V1.md) wipe notes |
| Anonymous drop box to the movement with a private "your post was shared" signal | S | None |
| Group-signed verification verdicts and a hash-only evidence ledger | M | None |
| Invite-only safety channels (vouch in person, hidden member list) | R | None |
| Private, optional proof of authorship for creators | R | None |

---

## Where Gen Z differs from the first persona set

These contrasts compare two small sets of fictional personas. They are
hypotheses about what to test, not claims about how a generation behaves;
many differences below (media habits, platforms, polish) track income,
urban access and occupation as much as age.

### Visibility vs. hiding

The first set wanted the whole thing to be low-profile: "ordinary, not
hidden", no disguise, every screen safe to show family. Gen Z splits the
question in two. The **message** should be as visible as possible: reach,
virality and crowds are the movement's strategy. The **person** should be
invisible: no face, no voice, no number, no rhythm. And two personas (Sana,
Ritu) explicitly ask for a disguise against a family member who checks the
phone, which most of the first set rejected (only Thoibi asked for a
notes-app look). Imran sides with the first set: "something
that looks normal, not something that looks hidden." The threat has also
shifted from officials and supervisors towards **future gatekeepers**:
placement cells, employers' background checks, professional registration,
and family "character" talk.

### Media and memes

The first set was voice-first. Gen Z is video- and meme-first, with voice
for some (Imran's riders) and images as the logistics format (posters,
notices). Without video BuzzChat is "a utility, not a home" (Meera). Most
accept that, *if* the app is positioned as notices and logistics, with short
audio clips, small posters and signed hashes of original videos as the
compromise. Memes also bring a new identification risk: style, watermark fonts
and editing habits are recognisable.

### Platform dependence and suspension risk

The first set's world was WhatsApp, committees and paper. Gen Z lives on
Instagram and X, where accounts get withheld, re-handled and phished. Three
consequences: Nostr reach is worthless to them; a seal is valued mainly as
**continuity across platforms** ("the old mark should say 'this new one is
ours'", Imran); and BuzzChat's output must be exportable to the platforms they
actually use, without losing its verifiability.

### Movement identity and impersonation

The first set trusted local, known bodies (a committee, a samiti, a hospital)
vouched for in person. Gen Z's movement is a satirical brand with millions of
followers, no registered structure, and factions. Impersonation happens at
brand scale (lookalike pages, donation scams, AI-edited images), and the seal
has to cope with forks, theft, rotation and the gap between "really posted
by this group" and "this group is right". No persona wants the app or its
developers to pick the "official" faction.

### Speed and leaderlessness

Meme templates spread overnight; Discord servers become assemblies (Nepal,
Morocco). There is no committee to vouch, and authority goes to "whoever was
loudest on WhatsApp" (Kabir). Gen Z wants tools that resist one person
wiping the shared sheet (add-only history, limited delete rights) and wants
disagreement shown openly rather than resolved by the software. Votes and
polls are familiar from Story polls and Discord, but the Nepal experience
shows open votes are not representative.

### Design expectations

The first set needed a helper, voice navigation and large type. These six
expect polish comparable to the apps they already use, within seconds: a soft, light, "school notice" look,
no terminal green, "connected, 34 people nearby" feedback within 30 seconds,
pre-seeded content so the first screen is never empty, and plain words
("hides who posted") instead of "Tor". They will explain honest limits to
friends but punish hype ("say 'harder to take down'").

### Where they agree

- **Being identified matters more than having messages read.** E2E does not
  touch the main fear in either set.
- **Bluetooth detectability is the top personal risk** (18 of 21 in the first
  set, 16 of 18 here).
- **No account and no phone number** is the clearest win in both.
- **The board is the strongest idea** and becomes a rumour machine without
  seals, expiry and corrections.
- **Seals must be vouched for by people, never by a central or default
  authority.**
- **Language before features.** Both sets need several Indian languages and no
  jargon; this set adds Bhojpuri, Marathi, Telugu, Bengali and right-to-left
  Urdu.
- **Battery, storage and data budgets** decide whether the app stays installed.
- **The mesh needs a crowd** and is a "one bad day" tool unless it has
  everyday uses.
- **Stale information is a kind of misinformation.**
- **No location.** Arjun's named zones match the first set's ban on live
  location and geofencing.
- **Honest limits** win trust; overclaiming loses it.

## Tensions to resolve

These need real participants and, where noted, outside expert review.

1. **Disguise vs. "ordinary, not hidden."** Sana and Ritu want a neutral
   alternate icon and label; Imran and the whole first set say a disguise that
   is discovered is worse than the app. → Ship one neutral, honest name and
   icon for everyone (not `bitchat`, not protest-branded), discreet
   notifications and no gallery writes, and point the highest household-risk
   users to the browser path, which leaves no icon (it can still leave
   browser history). The first study's rule stands: **no disguise or decoy
   mode** ([PROTESTER_RESEARCH_SYNTHESIS](PROTESTER_RESEARCH_SYNTHESIS.md),
   *Things NOT to build*). These two personas are a reason to re-test that
   rule with real participants and qualified local counsel, not a reason to
   override it.
2. **Loud movement, invisible people.** The movement wants faces and crowds;
   individuals want none. → BuzzChat serves the person: defaults strip and
   blur, and the movement's reach happens on mainstream platforms, by choice,
   per post.
3. **Radio off by default vs. mesh density.** Every persona wants the radio
   off unless they turn it on, which thins the mesh. → Event mode with a
   countdown, organiser hub phones on power banks, and carried bundles to make
   up the density.
4. **"Connected, N people nearby" vs. "no registers."** Kabir wants instant
   feedback; Sana wants counts, never names; the first set bans raw "N nearby
   users". → Show a coarse band ("a few", "dozens", "many") on the user's own
   screen only, never on shared or public surfaces. Test whether even that is
   acceptable.
5. **One-glance green vs. honest forks.** Imran and Sana's mother need green or
   red; Kabir and Meera need "two real seals disagree" shown openly. → Three
   states plus one: green (signed by a key you trust), grey or red (unsigned
   or not theirs), amber "disputed: two groups use this name", each with one
   plain line. Test whether amber is understood in under ten seconds.
6. **Unforgeable vs. retractable.** For Arjun, uncensorable also means
   unretractable: a mistaken "tent moved" cannot be recalled. For Ritu, "a
   seal that can never be taken back is basically a crown." → Gateway posts stay permanent, with the existing warning;
   seals get expiry, revocation and sealed corrections that sit on top of the
   original.
7. **Exact age vs. hidden timing.** Kabir wants "sent 2h 14m ago" on every
   message *and* no exact authored timestamps. → Show coarse relative age
   from the local receive time and the jittered authored time ("about 2 h
   ago"), never exact seconds.
8. **Provenance vs. location privacy.** Meera wants relay-attested time and a
   coarse area for blackout photos. Relay phones are untrusted (the Bridgefy
   lesson), so a relay's stamp proves only what that relay claims, and is
   worth no more than the user's trust in the relay's key; [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md)
   says never collect location, and `AGENTS.md` treats location as highly
   sensitive. → Time-only attestations by default; any area must be chosen
   manually, coarse, and off by default, as with geohash in
   [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) *Privacy*.
9. **Reach bridges vs. privacy.** Export cards for Instagram and WhatsApp put
   content on platforms that log everything, and a screenshot of a seal proves
   nothing. → Export is manual and per post, strips metadata, embeds a short
   verify code, and says so.
10. **Proof of authorship vs. coercion.** Ritu wants private proof that she
    made a poster, but "if something can be proven, someone could force me to
    prove it." → Optional, off by default, with that risk stated.
11. **Store distribution vs. shutdown resilience.** Imran's household trusts
    only a store or a proper website; stores can delist an app. → Store,
    F-Droid and a website with checksums as the normal path; friend-to-friend
    APK sharing as a shutdown fallback with a short code people can compare.
12. **On-device content checks vs. budgets and false comfort.** Face, text and
    voice detection cost size and battery and will miss things. → Run them
    only at the moment of sharing, say plainly that they can miss faces, and
    never call a post "safe".
13. **Anonymous drop box vs. volunteer load.** Sana's anonymous submissions
    could flood volunteers with spam or worse. → Rate limits and proof-of-work
    per submission; volunteers can close the box.
14. **Native iOS vs. bitchat compatibility.** A native BuzzChat iOS app splits
    effort; relying on bitchat iOS depends on upstream. → Coordinate seal and
    board formats with upstream first.

---

## Prioritised recommendations

Priority: **P0** = before any field pilot; **P1** = before wider release;
**P2** = later or exploratory. These extend, and do not replace, the first
study's recommendations in
[PROTESTER_RESEARCH_SYNTHESIS](PROTESTER_RESEARCH_SYNTHESIS.md#prioritised-recommendations).

### (a) Changes to principles and specs

| Pri | Change | Document |
| --- | --- | --- |
| P0 | Add a commitment: **"Signed voices, no single holder."** A movement or group seal is never held by one person or one phone; it can be rotated, revoked and corrected in the open; a split shows as a visible fork; the seal says "really posted by", never "right"; BuzzChat and its developers never choose the canonical key | [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md) (*Commitments*) |
| P0 | Strengthen the first study's **"Radio on purpose"**: the radio is off unless the user starts a timed session; the app always shows its true radio state in plain words, including anything the OS may still do; no green tick that overstates protection | [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md); *Power* in [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) and [WEB_JOIN_V1](../WEB_JOIN_V1.md) |
| P0 | Add **"Consent before contact"** beside "Consent before reach": nobody can message you until you allow it, and nearby lists show coarse counts, never names | [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md) |
| P0 | Extend **"Honest limits"** to marketing and store copy: "harder to take down", never "uncensorable", "invisible" or "100% safe"; a published "what it does not protect" page (detectable radio, an unlocked phone that is taken, faces and style in content, iOS background limits, Nostr's small audience) | [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md) (*Commitments*) |
| P0 | Rule 2 device table: say which BuzzChat features work on an iPhone running bitchat (chat only, today); add rows for "a phone that is someone's work tool" (no background cost) and "a phone others check" (nothing that outs the user) | [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md) (*Rule 2*) |
| P0 | **New spec: movement seal.** Threshold (k-of-n) signing; scoped, expiring role seals ("Medical, this gathering") granted in person; rotation endorsed by the old key; revocation by 2 of N shown on every phone; an append-only key history mirrored on Nostr; fork display with co-signers and history length; sealed corrections and "attest / debunk this circulating notice" records over a content hash; verification from a QR, a link or a share-card code. Fail closed, golden vectors, coordinated with upstream bitchat | New `docs/MOVEMENT_SEAL_V1.md` (first study's verified-source labels become its role-seal section) |
| P1 | Gateway posts: add an optional **collective author mode** signed under a movement seal, kept strictly apart from per-post keys (alongside the first study's press/organisation mode). Add an on-device pre-publish check (faces, readable background text, voice) before signing. Spread the release of posts queued during a shutdown so they do not leave in one burst. Replace "Require Tor" and "Nostr" in the main UI with plain words ("hides who posted", "share to internet later") | [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) (*Author key*, *Media*, *Privacy*, *Author lifecycle*, *UI requirements*) |
| P1 | Join by link: printed invites use **one identical code for every copy**, never per-copy or location-tagged; the landing screen shows the board's seal state full-width (green, grey/red, amber) and a three-word code the organisers also post on their mainstream channels; an explicit "no host nearby" first-scan state | [WEB_JOIN_V1](../WEB_JOIN_V1.md) (*Invite and join flow*, *Poisoned code*, *UI requirements*) |
| P1 | Offline bundles: when the reserved sealed-notice item type is defined (first study, P1), every received item shows its seal state on the confirmation screen | [OFFLINE_BUNDLE_V1](../OFFLINE_BUNDLE_V1.md) (*Bundle contents → Reserved*, *UI requirements*) |
| P1 | Bulletin board spec (first study, P1) gains: partial pledges with a live remaining count, "resolved" and auto-close, per-type staleness, named zones and landmark text instead of any location, add-only history with limited delete rights, per-key rate limits, sealed notices pinned above unsealed ones, a reserved-word "NOT from organisers" banner, and gender-sensitive templates (female medic, walk-to-bus buddy) | New `docs/BULLETIN_BOARD_V1.md` |
| P1 | Add a **resource budget as a release gate**: battery per active hour and per event day on a 3-4-year-old budget phone, idle drain with the radio off (target zero), APK size, media and relay cache caps | *Test plan* in each spec; Mesh Lab entries per `docs/release-gate-runbook.md` |
| P2 | Voice and media: define short sealed voice notes (under 40 s) and 10-15 s low-bitrate audio clips for board and broadcast use; scope a nearby-only short-video experiment over Wi-Fi with face blur and no outward publishing | [LIVE_VOICE_V1](../LIVE_VOICE_V1.md); new spec if pursued |

### (b) New features

| Pri | Feature | Serves | Notes |
| --- | --- | --- | --- |
| P0 | **Honest radio status and event mode.** "Bluetooth broadcasting: OFF. Nearby phones cannot see BuzzChat", time since it stopped, a quick-settings tile; a 1-3 h event mode with a battery estimate before it starts, auto-off at a user-set threshold (default about 30%) | K S A M I R | Measure the estimate on old phones; never show a figure that has not been measured |
| P0 | **Seal UX** (with the spec). Full-width green / grey-red / amber states with one plain line in the user's language; loud "NOT from them" on unsealed posts using reserved words or lookalike names; seal pinned from a trusted source, with lookalike-name warnings | K S A M I R | Release gate: Meera's five-second fake-poster test with a non-technical person |
| P0 | **Consent-gated contact.** Request-only DMs with no preview or notification, one-tap block, nearby counts not names, random non-gendered nicknames rotated per event, no avatars by default | S I R | Key-based blocking without accounts needs design (first study, P1) |
| P0 | **Discreet by default.** Neutral app name and icon, notifications with no sender or text even when unlocked, quiet mode for board posts, nothing written to the gallery | S A I R | Extends the first study's P0 discreet notifications |
| P0 | **Plain words and languages.** Finish `hi`, `bn`, `ur`; add `mr` and `te`; decide how to ship Bhojpuri; remove "mesh", "relay", "Tor", "Nostr" and "peer" from user-facing strings; one permission screen with a one-line reason each | K S A I R | Any English string in a core flow blocks release for that locale |
| P0 | **Image safety at the moment of sharing.** "Location and camera details removed" stated on every image path; optional on-device face blur; a gentle note that faces it missed, style and watermarks are not removed; crop-to-wound default and no forwarding for medical images | K S A M R | Never call an image "safe" |
| P0 | **First-run that is never empty or scary.** A light, soft visual language for onboarding; one line ("No number, no name. Notices for people nearby."); a pre-rendered example notice; a coarse "connected" confirmation within 30 s | S R K | Full visual refresh is P1 |
| P1 | **Instagram-grade UI refresh.** Light default theme, sun-readable contrast, large targets, correct Devanagari, Bengali, Telugu and Urdu (right-to-left) rendering, screen-reader labels | All | Before/after screenshots with synthetic data per `AGENTS.md` |
| P1 | **Verify anywhere.** Share cards for WhatsApp and Instagram with the seal and a short verify code; a "check a forward" flow that matches pasted text or a scanned code against signed posts; a static verifier page organisers can host | K S A M I R | No BuzzChat server; organisers host. Screenshot matching is best-effort; the code is the reliable path |
| P1 | **Outbound queue people trust.** "Share to internet later, your name hidden" with plain per-post status, a pre-publish checklist, spread release after a shutdown, and a local "your post was picked up" signal when a signed repost carries the same content hash | K S I | Builds on the gateway author lifecycle |
| P1 | **Board v1** (with the spec). Need / Shift / Headcount / Venue-change templates with partial pledges, "resolved", live counts, expiry and named zones; a medical handover card (wristband code, no name or face, auto-deleted at handover) restricted to the medical role | K S A I R | The flagship feature in both studies |
| P1 | **Urgent request and "we're okay".** A priority "MEDICAL: stretcher + cold water @ zone" type relayed before media with a one-tap "on my way" and a visible "N received" for the sender; a few-byte "we're okay, 6 of us, next check-in 10 pm" template; a lost-friend alert and morning meeting point | A K S | "We're okay" to far-away contacts depends on the first study's courier DM |
| P1 | **Image and meme posts with provenance, not verdicts.** Small posters and memes on the board; "first seen here at", "seen before on this phone" by local hash; optional sealed "group X attests this is from today" | K M I R | Never automated true/false labels |
| P1 | **Short sealed voice notes** in board and broadcast (under 40 s) | I | Text or visual equivalent always required (first study) |
| P1 | **Hub mode** for a spare phone on a power bank: screen off, relay and board cache only, battery and uptime readout; browser users see which hub they are on | A | Never labelled "Hub" or "Leader" in shared UI (first study) |
| P1 | **"Everything BuzzChat keeps."** A labelled settings screen listing identity, chats, media cache, keys and queued posts, with an app-only clear that reports what it removed; event data auto-expires after 48-72 h unless saved | S I M R | Matches the first study's tiered, honest wipe |
| P1 | **Trusted distribution.** Play Store, F-Droid and a website with checksums; friend-to-friend sharing as a shutdown fallback with a readable short code; clean uninstall | I K | Depends on reproducible builds (`docs/reproducible-builds.md`) |
| P1 | **Seal checks and board on iPhone** through upstream bitchat iOS | M | Coordinate formats before either client ships them |
| P1 | **Organiser and lead kits.** A short, non-technical explainer creators can turn into a reel; a one-page picture guide per language; a 10-phone airplane-mode drill with a 3-hour battery report | All | Adoption runs through the movement's own channels, never through the app's |
| P2 | **Poll and decision tools**, limited to sealed groups and logistics ("which gate?"), results shown as "responses received on this phone", never as a mandate | K R | Accountless polls are trivially stuffed; Nepal's server vote was admittedly unrepresentative |
| P2 | **Collective verdicts and evidence ledger.** Fact-check groups sign verdicts under a group key; a ledger keeps hash, verdict and note only, re-checks images by hash, no cloud backup | M | Builds on the seal spec |
| P2 | **Anonymous drop box** encrypted to a movement key from a throwaway sender key, with rate limits | S | See tension 13 |
| P2 | **Safety channels** joined by in-person vouch codes, neutral names, hidden member lists, silent join and leave, no history by default | R S | Write with real LGBTQ+ and women participants, not from personas |
| P2 | **Short nearby video** over Wi-Fi with an honest size and time estimate, face blur, never outward | K | Only after the resource budget is met |
| P2 | **Private authorship commitment** for creators, off by default, with the coercion risk stated | R | — |
| P2 | **Everyday packaging**: campus outages, hostel power cuts, fests, blood camps, floods | A I S | Makes carrying the app unremarkable |

### (c) Things NOT to build

- **No single-holder movement key**, and no seal without rotation, revocation
  and expiry.
- **No developer-chosen or default "official" key**, and no badge that reads
  "Official" or implies endorsement.
- **No automated true/false labels** on images or posts.
- **No votes presented as the movement's decision**, and no crowd-vote hiding
  of posts (first study).
- **No per-copy or location-tagged poster QR codes**, and no analytics or
  tracking on any landing page.
- **No open DMs from strangers**, no nearby lists with names, no profile
  photos by default.
- **No follower counts, likes or virality metrics.** BuzzChat is a notice
  board and a messenger, "not another social network" (Ritu).
- **No "uncensorable", "invisible" or "100% safe" copy**, and no "Tor",
  "Nostr" or "mesh" in the main UI.
- **No anonymous outward photos or video with faces**, and no medical images
  marked for the internet, ever.
- **No GPS "area".** Named zones and landmark text only; no live location,
  proximity or geofencing (first study and `AGENTS.md`).
- **No automatic posting to Instagram, WhatsApp or X** through their APIs or
  linked accounts. Export is a manual, per-post user action.
- **No cloud backup of media or evidence** by default, and no gallery writes.
- **No background relaying on browser or low-end clients by default**, and no
  feature that only works after a user recruits friends.
- **No icon that pretends to be another kind of app**, as in the first
  study's "no disguise or decoy mode". Tension 1 only asks for that rule to be
  re-tested with real participants.

---

## Open questions for real validation

1. **Seal comprehension.** Can a non-technical relative tell green, grey/red
   and amber "disputed" apart in under ten seconds, on a cracked screen in
   sunlight?
2. **Key custody.** Will real movement organisers accept k-of-n signing and
   in-person role seals, and who in a leaderless movement would hold the
   shares? What happens to a share holder who is detained?
3. **Forks.** When a real movement splits, do users want rival seals shown
   side by side, or does that confuse more than it helps?
4. **Off-platform verification.** Will people scan or type a verify code from a
   WhatsApp forward or an Instagram post? Does a "check a forward" flow change
   forwarding behaviour?
5. **Disguise.** In households that check phones, is a neutral honest name
   enough, is the browser path used, or do people still want an alternate
   icon? What do qualified local lawyers say about each?
6. **Radio off by default.** How much does off-by-default plus event mode cut
   mesh reach at a real gathering, and do hub phones on power banks make up
   for it?
7. **Battery budget.** What does an event hour actually cost on a 3-4-year-old
   budget phone, and is that inside the 5-30% range these personas gave?
8. **Content checks.** Do on-device face, text and voice warnings reduce
   self-identification, or create false confidence?
9. **Timing.** Is coarse relative age ("about 2 h ago") enough for hazard
   notices while protecting authors' rhythm?
10. **Reach.** Will movement pages actually repost from BuzzChat's outbound
    queue or drop box, and would a "your post was picked up" signal matter?
11. **Media.** Do short audio clips and small posters satisfy a video-first
    movement for logistics, or is nearby short video required for adoption?
12. **Consent-gated contact.** Does request-only messaging remove the "boys can
    DM me" barrier for young women without killing coordination?
13. **Everyday use.** Which campus or neighbourhood uses would keep the app
    installed between gatherings?
14. **iPhone.** How many participants in a real gathering are on iPhone, and
    will upstream bitchat iOS support seals and board notices?
15. **Who is missing.** Disabled young people, Adivasi and Northeast youth,
    young Muslim women, trans people, young people without their own phone,
    students outside the exam-leak movement, and people the tool could be
    used against. Hear them before any pilot; see *Missing perspectives* in
    [PROTESTER_RESEARCH_SYNTHESIS](PROTESTER_RESEARCH_SYNTHESIS.md#missing-perspectives).
