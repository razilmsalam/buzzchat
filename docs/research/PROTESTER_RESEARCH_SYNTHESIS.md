# Protester research synthesis (simulated persona study)

> [!WARNING]
> **This is a simulated persona study, not field research.** Every person,
> name, age, place detail and quote in this document and in
> `docs/research/personas/` is **fictional and synthetic**. The personas were
> written to generate design hypotheses for BuzzChat. They are not evidence about
> any real person, community or organisation. Real public events (the
> communications restrictions in Kashmir from August 2019, including the long
> 2G-only period that followed, the 2023 mobile internet ban in Manipur, the
> 2020-2021 farmer protest camps on Delhi's borders, the 2019-2020 CAA
> protests, and the December 2019 Assam shutdown) are used only as general
> background. The personas' first-person accounts of those events are
> composites: their dates, sequences and incidents are illustrative, not
> verified facts, and must not be quoted as reporting. No statement here is
> attributed to a real individual or organisation, and nothing here describes
> Indian law; legal questions belong with qualified counsel.
>
> **Nothing in this document should ship as a decision until it has been checked
> with real, consenting participants**, using the protocol below.

## Validation protocol (minimum bar before acting on these findings)

1. **Participant safety first.** Do not recruit anyone during an active
   shutdown, protest or legal proceeding. Recruit through intermediaries the
   participant already trusts (local volunteer groups, legal-aid or health
   networks), never through cold outreach. Anyone can stop at any time with no
   reason given and no follow-up.
2. **Informed consent** in the participant's own language, read aloud where
   needed. Explain what is recorded, where it is kept, who sees it and when it
   is destroyed. Get consent verbally. Do not collect a signed form, because a
   signature is itself identifying.
3. **No identifying data.** Do not record names, phone numbers, villages or
   neighbourhoods, workplaces, organisation names, photos, faces or voices.
   Give each participant a random code. Do not record device serials or
   account handles. Describe a phone by its rough class only ("budget Android,
   about 3 years old").
4. **No stored recordings.** If audio must be captured to transcribe a
   non-English session, transcribe it on the spot on an offline device and
   delete the audio before the session ends. Never record video.
5. **Local-only notes.** Keep notes on an encrypted, offline device. Do not use
   cloud documents, chat apps, transcription services or AI services. Strip
   quotes of names, places and dates before they go into any shared synthesis.
6. **Usability sessions** use synthetic content only (made-up notices and
   messages), on study phones, never on the participant's own phone. Do not
   install anything on a participant's device.
7. **Independent review.** Before fieldwork, have the protocol reviewed by
   someone outside the product team with research-ethics and local
   human-rights experience.

---

## Summary

Seven fictional personas looked at the BuzzChat concept: the mesh chat, join
by QR or link, APK sharing, the browser client, Tor gateway posts, an expiring
area bulletin board, E2E DMs, animated QR bundles and the emergency wipe.
Five hypotheses came up in every interview:

1. **Being identified matters more than having messages read.** Every persona
   feared that their name, face or the app's presence on their phone would
   mark them. That fear was directed at strangers, family members, supervisors
   and officials alike. E2E encryption, the concept's strongest privacy
   feature, does not address this. Two current behaviours make it worse: BLE
   advertising that shows the app is running, and a visible emergency wipe.
2. **Language and voice come before features.** All seven either rely mainly
   on voice notes or depend on family, members or sources who do. (Voice-first
   must never mean voice-only: Deaf and hard-of-hearing users are not
   represented here and need text and visual equivalents; see
   *Missing perspectives*.) All seven need an interface in
   an Indian language: Gurmukhi Punjabi, Kashmiri or Urdu, Meiteilon, Marathi,
   Hindi, Assamese or Tamil. An English, text-first UI fails the ten-second
   test in [BuzzChat principles](../BUZZCHAT_PRINCIPLES.md) before anything
   else gets a chance.
3. **The bulletin board is the strongest idea, and also the biggest risk.**
   Everyone liked expiring local notices ("a paper notice that does not get
   wet"). Everyone also said that anonymous, unverifiable, unstoppable posts
   would make it a "rumour machine". The board only works with seals vouched
   for in person, corrections attached to the original notice, and no raw
   "seen by N" counts.
4. **The mesh needs a crowd, and the worst moments have none.** Curfews,
   hamlets 1-2 km apart, buffer zones, and a reporter working alone are all
   places with no crowd. The realistic model is **carried, burst
   store-and-forward**: people physically carry messages to the next place, and
   phones sync in short sessions the user starts. An always-on live mesh is not
   realistic there.
5. **The emergency wipe, as described, backfires.** All seven rated it a risk.
   Pressing it in front of someone looks like guilt. On a shared phone it
   threatens a family member's data. It gives false confidence because it
   cannot clear the camera roll. For a reporter it destroys evidence. This
   directly contradicts the principle "keep the emergency wipe one obvious tap
   away" (`docs/BUZZCHAT_PRINCIPLES.md`, *Commitments that follow*).

## Persona index

| Key | Persona (fictional) | Region and context | One-line summary |
| --- | --- | --- | --- |
| [gurpreet](personas/gurpreet.md) | Gurpreet Kaur, 52, farmer and langar logistics volunteer | Rural Punjab; Delhi-border protest camp | A waterproof notice board for the camp, but only in Gurmukhi and by voice, with very low battery use, vouched committee labels and an app-only wipe. Otherwise she quietly stops using it within a week. |
| [aamir](personas/aamir.md) | Aamir, 24, postgraduate student | Srinagar, Kashmir; 2019-2020 blackout and 2G period | Wants an app that is "honestly ordinary": it tells his mother he reached home and is never the reason he, or his sister holding his phone, gets questioned. |
| [thoibi](personas/thoibi.md) | Thoibi, 31, ER night nurse and relief-camp supply coordinator | Imphal, Manipur; 2023 mobile internet ban | Wants exact supply lists sent phone to phone with no data, plus expiring camp-needs posts. Calls the anonymous design a "rumour machine" and doubts the mesh can cross buffer zones. |
| [ramesh](personas/ramesh.md) | Ramesh, 63, retired clerk and Dalit rights organiser | Marathwada village, Maharashtra | His network is people, paper and the milk centre. The board fits, but with no voice or Marathi, no feature-phone path, no committee seal and no protection for young women, it would split the community. |
| [priya](personas/priya.md) | Priya, 22, campus organiser | Delhi (family in UP) | A backup ("still carry the printed list") for role-tagged nearby notices and for getting a short clip out with faces blurred, location stripped and someone credible vouching for it. Drops it if it drains her old iPhone or overclaims. |
| [farhan](personas/farhan.md) | Farhan, 35, freelance journalist | Guwahati and Lucknow | His job is verifiable evidence, not chat. The design fails him without signed provenance, media support, source protection that travels with the file, and a low profile. |
| [lakshmi](personas/lakshmi.md) | Lakshmi, 40, community health worker and union leader | Small town, Tamil Nadu | Uses a shared, overloaded Android Go phone. Wants venue changes to reach a sit-in, and drops anything that looks secret at home, singles women out over Bluetooth, or slows the health app her income depends on. |

### See also: Gen Z persona set

A second set of six fictional personas, aged 19 to 24 and modelled on the
2026 youth protests over exam-paper leaks, is analysed separately in
[GENZ_RESEARCH_SYNTHESIS](GENZ_RESEARCH_SYNTHESIS.md). That set also reacted
to a movement signing key (seal). It was not part of the concept shown to
this set, although this set asked on its own for in-person committee and
role seals (see *Verified-source labels*). Its scores use a
maximum of 18 and are not merged with the scores here.

| Key | Persona (fictional) | One-line role |
| --- | --- | --- |
| [genz-meme-admin](personas/genz-meme-admin.md) | Kabir, 21 | Co-admin of a satirical Bhojpuri/Hindi meme page, Bihar |
| [genz-neet-aspirant](personas/genz-neet-aspirant.md) | Sana, 19 | Medical entrance aspirant and first-time protester, Rajasthan |
| [genz-volunteer-medic](personas/genz-volunteer-medic.md) | Arjun, 20 | Paramedical diploma student, field medic and volunteer coordinator, Kolkata |
| [genz-factchecker](personas/genz-factchecker.md) | Meera, 23 | Software tester and volunteer fact-checker on iPhone, Bengaluru |
| [genz-gig-worker](personas/genz-gig-worker.md) | Imran, 24 | Food-delivery rider, Hyderabad |
| [genz-queer-student](personas/genz-queer-student.md) | Ritu (she/they), 22 | Queer design student and poster-kit maker, Pune |

## Method and limits

- **Inputs.** Each persona is a synthetic interview, structured as top
  problems, needs, deal-breakers, BuzzChat fit and design implications. The
  concept they reacted to was BuzzChat as described in the
  [principles](../BUZZCHAT_PRINCIPLES.md) and the three draft specs:
  [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md),
  [OFFLINE_BUNDLE_V1](../OFFLINE_BUNDLE_V1.md) and
  [WEB_JOIN_V1](../WEB_JOIN_V1.md).
- **Scoring.** Problems are scored per persona as critical = 3, high = 2,
  medium = 1, not raised = 0, then summed. The maximum is 21, which means all
  seven rated it critical. The score combines severity and frequency. It is a
  way to prioritise, not a measurement.
- **Known biases.** The personas were written by the same team that designed
  the product, so they may echo our own worries. All seven are adults with
  some phone access. People with no phone, children, disabled users, LGBTQ+
  people, Adivasi communities, speakers of languages beyond the eight major
  languages covered here (for example Santali, Gondi, Bodo or Bhojpuri), and
  the people opposing the
  protests (who could misuse the tool) are not represented. Thoibi's account
  of Manipur reflects one community's experience of a two-sided conflict; the
  other side's experience is absent and must be heard before any Manipur
  pilot. A single "persona" stands in for a whole community, which no real
  community would accept. See *Missing perspectives*.
- **Event details are composites.** Persona recollections of real public
  events (timelines, rumours, costs) were written to be plausible, not
  checked. Treat them as prompts for questions, not as evidence.
- **Concept vs. code.** Some "gaps" the personas raised already partly exist
  in the Android app but were not part of the concept they were shown. These
  are marked below. Others are real gaps.

### What the app already has (checked against the repository)

| Persona assumption | Repository reality | Implication |
| --- | --- | --- |
| "There are no voice notes" | The Android app has voice notes (`app/src/main/java/com/bitchat/android/ui/media/VoiceNotePlayer.kt`) and live push-to-talk ([LIVE_VOICE_V1](../LIVE_VOICE_V1.md)) | Partly a gap in what we communicated. Voice is still missing from the browser client (a [WEB_JOIN_V1](../WEB_JOIN_V1.md) non-goal), gateway posts and bundles (non-goals in both specs), and any board notice |
| "English only" | Translations exist for `hi`, `ta`, `ur`, `bn` and `pa-rPK`, each covering about 58-66% of the base strings. `pa-rPK` is Punjabi in **Shahmukhi** script, not Gurmukhi | Hindi, Tamil and Urdu need finishing. Gurmukhi Punjabi (`pa-rIN`), Marathi, Assamese, Kashmiri and Meiteilon (Bengali script and Meetei Mayek) are missing |
| "Notifications show who wrote what" | Private-message notifications hide content on the lock screen (`VISIBILITY_PRIVATE` with a hidden public version, `app/src/main/java/com/bitchat/android/ui/NotificationManager.kt`). Unlocked, the shade still shows sender and text | Lakshmi's and Aamir's household threat (someone reading the shade on an unlocked phone) is not covered |
| "Scanners can tell the app is running" | True. There is a fixed BLE service UUID (`app/src/main/java/com/bitchat/android/util/AppConstants.kt`), a stable peer ID in the scan response that rotates only after a panic wipe (MESH_GATEWAY_V1 *Residual risks*), and a hub SSID prefix `DIRECT-BC-` (WEB_JOIN_V1 *Residual risks*) | Confirmed as the top cross-cutting risk |
| "A strange name shows up in the Bluetooth list" (Lakshmi's son) | BLE advertising sets `setIncludeDeviceName(false)` (`app/src/main/java/com/bitchat/android/mesh/BluetoothGattServerManager.kt`), and the code that renamed the Bluetooth adapter is commented out, so the app adds no name to ordinary Bluetooth pairing lists. The service UUID is still visible to a scanner app. A phone acting as a hub creates a Wi-Fi network named `DIRECT-BC-...`, which **does** appear in anyone's ordinary Wi-Fi list | The household fear is real, but the visible clue is the hub's Wi-Fi name, not a Bluetooth name |
| "The app is called something odd" | `app_name` is `bitchat` | Every persona wants a name they can explain in one sentence |
| "Wipe might delete family photos" | Panic clears app-owned data. Saved bundle files and other apps' files are outside its scope (OFFLINE_BUNDLE_V1 *UI requirements*) | The fear is unfounded for photos, but the UI never says so in the user's language. Priya's opposite worry, that people will *assume* the wipe cleared the camera roll, is valid |

## Device and connectivity landscape

| Persona | Main device | Shared with | Data | Power | Main input | Script(s) needed |
| --- | --- | --- | --- | --- | --- | --- |
| Gurpreet | Mid-range Android, ~3 yrs, storage full daily | Adult son (has it at night) | ~Rs 300/mo prepaid, YouTube uses it up | Charging queue from tractor battery or solar; data off to save battery | Voice notes (~90%) | Gurmukhi |
| Aamir | Budget Android, 4 GB RAM, 64 GB full | Teenage sister (online classes) | ~Rs 300/mo; useless at 2G | Weak battery, long winter cuts, power bank | Typing (English, Roman Urdu); mother uses Kashmiri voice | Urdu, Roman Urdu, Kashmiri |
| Thoibi | Android, 4 GB RAM, 64 GB full | Mother, 6-year-old niece | ~Rs 300/mo | ~30% by evening; charges at work | Typing (English, Roman); aunties use voice | Bengali script, Meetei Mayek, Roman |
| Ramesh | **KaiOS feature phone** (T9) | Sometimes borrows granddaughter's Android | Rs 150-200/mo | 2-day battery; 4-5 h power cuts | Voice notes, calls | Devanagari (Marathi), large type |
| Priya | **Old iPhone**, ~74% battery health | Roommates know passcode | ~1.5 GB/day, gone in an hour of streaming | Dead by 3 pm on march days | Text; Hindi voice to grandmother | Devanagari (Hindi), Latin |
| Farhan | Mid-range Android + cheap backup Android | Daughter (separate profile) | Two SIMs, ~Rs 300-350 each | 20,000 mAh bank | Text, voice for Assamese | Devanagari, Bengali-Assamese, Latin |
| Lakshmi | **Android Go**, 2 GB RAM, full | Son (mornings), mother-in-law (evenings), husband checks at night | ~Rs 300/28 days, used up by son | ~1 day, no power bank | Voice notes; half her members cannot type | Tamil |

### What this means for "works on whatever device people already hold"

Rule 2 in [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md) lists four device
roles. The personas show at least five situations that the table does not
cover:

1. **Feature phones (KaiOS).** Ramesh has never scanned a QR code. Whether
   the browser client works on KaiOS is unmeasured, and the
   [WEB_JOIN_V1](../WEB_JOIN_V1.md) device matrix (*Test plan* §5) does not
   list KaiOS. Even if it works, a browser-only device works only next to a
   hub, so in practice it is "her phone, not mine". The principle needs either
   a tested path or an honest "not supported, here is how you still get
   notices" entry.
2. **Android Go / 2 GB RAM.** Lakshmi will uninstall the app the same day if
   it slows her government health app. Nothing in the specs sets a RAM, APK
   size or storage budget. The power sections in
   [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) (*Mesh propagation and storage →
   Power*) and [WEB_JOIN_V1](../WEB_JOIN_V1.md) (*Power*) cover battery
   only.
3. **Shared and checked phones.** Five of the seven share a phone, and two
   have phones that family members actively check. "Any phone" has to mean
   "any phone, as one of several people using it", and every screen has to be
   safe to show to a family member.
4. **Old iPhones.** Priya needs a one-sentence answer to "app or browser, and
   is bitchat the same network?" Safari has no Web Bluetooth, so the browser
   path depends on a hub. Background BLE limits on iOS make the bitchat app
   less reliable than the Android role suggests.
5. **Full storage, rationed data, unreliable power.** Every persona has full
   storage, and data is budgeted like rice: "I plan my data like I plan rice."
   The specs already default to zero background data, which is right. They do
   not yet cap relay or media caches, and they do not promise a battery
   figure.

**Voice versus text.** For Gurpreet, Ramesh and Lakshmi, and for the people
around Aamir, Thoibi, Priya and Farhan, voice is the main medium and a
familiar voice is how they decide whether to trust a message. The
browser client, gateway posts and offline bundles are all text-only by design.
So voice users get the least on exactly the paths meant to reach "every
device".

---

## Cross-cutting problems, ranked

Score = sum of per-persona severity (critical 3, high 2, medium 1). Persona
initials: G Gurpreet, A Aamir, T Thoibi, R Ramesh, P Priya, F Farhan,
L Lakshmi.

| # | Problem | Score | Personas (severity) | Representative quote |
| --- | --- | --- | --- | --- |
| 1 | **App presence marks the user.** Anyone scanning BLE can tell the app is running. An unusual icon, name or notification invites questions from officials, supervisors or family | 18 | A3 R3 P3 L3 G2 T2 F2 | "In my situation, the app being found on my phone is itself the risk." (Aamir) |
| 2 | **Rumour amplification.** Anonymous, unstoppable, uncheckable posts spread panic. Voice notes feel real. Raw counts make rumours look credible | 17 | G3 T3 R3 A2 P2 F2 L2 | "That's the rumour machine." (Thoibi) |
| 3 | **Language and voice exclusion.** English, text-first UI and jargon ("Tor", "hub", "end-to-end") | 16 | G3 R3 A2 T2 P2 F2 L2 | "If an app shows only English menus, I press buttons and hope." (Gurpreet) |
| 4 | **The mesh needs a crowd.** Curfew, hamlets 1-2 km apart, buffer zones and lone reporters are out of range | 16 | A3 T3 R3 G2 F2 L2 P1 | "Mesh works on a protest day and fails in a curfew, and curfew is exactly when we need it." (Aamir) |
| 5 | **Impersonation and forged notices.** Fake committee, gurdwara or organiser messages ("cancelled", "all calm, go home"). A label anyone can type means nothing | 15 | R3 G2 T2 L2 A2 P2 F2 | "People believed it because it sounded careful, and careful sounds like me." (Ramesh) |
| 6 | **The emergency wipe backfires.** It looks like guilt, puts a shared phone at risk, gives false confidence about the camera roll, and destroys evidence | 14 | all seven at 2 | "A wiped phone invites questions." (Aamir) |
| 7 | **Battery, storage and RAM.** Old batteries, full storage, Android Go, a shared charging queue | 13 | G3 L3 P2 F2 A1 T1 R1 | "If my phone dies at noon, the langar has no phone." (Gurpreet) |
| 8 | **Shared and checked phones.** Notification previews, family reading, a child holding the phone at a check, "secret-looking" features causing conflict at home | 12 | L3 G2 A2 R2 T1 P1 F1 | "Hiding is itself the evidence." (Lakshmi) |
| 9 | **Harassment, doxxing and gendered targeting.** Photos morphed, names screenshotted, "traitor" campaigns | 12 | P3 G2 T2 R2 A1 F1 L1 | "What scares me most isn't the police. It's being identified and then trolled." (Priya) |
| 10 | **Outbound credibility vs. anonymity.** Anonymous internet posts are worthless to editors and officers. Source protection doesn't travel with the file | 11 | P3 F3 R2 T2 L1 | "Anonymity and authenticity pull in opposite directions." (Farhan) |
| 11 | **Structured coordination is lost.** Headcounts, exact supply lists, rotas and fallback plans | 10 | T3 G2 L2 P2 R1 | "He wrote down 'Metformin 500' as 'Metformin 50'." (Thoibi) |
| 12 | **Fragile onboarding and silent abandonment.** Needs a helper, and one failed first try means people leave for good | 10 | G2 A2 R2 T1 P1 F1 L1 | "I didn't complain to anyone. I just stopped. Most people do that." (Gurpreet) |
| 13 | **Sideloaded APKs and QR codes look like scams.** No way people can understand to check that a file is genuine | 8 | G2 P2 A1 R1 F1 L1 | "How do I know the file from a friend is the real one?" (Gurpreet) |
| 14 | **Evidence can't get out.** Video, original files, provenance | 8 | F3 P3 R2 | "It cost us the story." (Priya) |
| 15 | **No way to say "I am safe" or "who is safe".** Family far away, parents of detained students | 7 | A3 P3 G1 | "My brother was outside and we had no way to tell him we were fine." (Aamir) |
| 16 | **Exposure inside groups.** Informers, presence or reader lists, strangers adding you | 6 | A2 R2 L2 | "If the board shows a list of who is 'there'... the supervisor gets a register of union women." (Lakshmi) |
| 17 | **Legal uncertainty.** Is carrying it, relaying for others or wiping it a problem? | 5 | R2 F2 A1 | "I want a lawyer or someone from our side to say whether keeping it on the phone can be used against us." (Ramesh) |
| 18 | **Stale information circulating** | 5 | A2 L1 P1 F1 | "An old notice that is still floating around is also a kind of rumour." (Lakshmi) |

---

## Feature-fit matrix

Persona verdicts on each BuzzChat capability. **Solves** = meets the need as
described. **Partly** = useful with significant changes. **Gap** = not
offered, or unusable by this persona. **Risk** = could harm this persona as
described.

| Capability (spec) | Gurpreet | Aamir | Thoibi | Ramesh | Priya | Farhan | Lakshmi |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Mesh chat over BLE / Wi-Fi Aware | Partly | Partly | Partly | Partly | Partly | Partly | Partly |
| QR / link join, no account ([WEB_JOIN_V1](../WEB_JOIN_V1.md)) | Partly | Solves | Partly | Gap | Partly | Partly | Partly |
| APK sharing from a friend's phone | Risk | Solves | Partly | Partly | Partly | Risk | Risk |
| Browser client via hub ([WEB_JOIN_V1](../WEB_JOIN_V1.md)) | Partly | Partly | Partly | Gap | Risk | Partly | Risk |
| Tor gateway posts ([MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md)) | Partly | Risk | Risk | Risk | Partly | Partly | Partly |
| Expiring area bulletin board (no spec yet) | Solves | Partly | Partly | Solves | Partly | Partly | Partly |
| E2E DMs (Noise) | Partly | Partly | Partly | Partly | Partly | Partly | Partly |
| Animated QR bundles ([OFFLINE_BUNDLE_V1](../OFFLINE_BUNDLE_V1.md)) | Gap | Partly | Partly | Gap | Partly | Gap | Gap |
| Emergency wipe | Risk | Risk | Risk | Risk | Risk | Risk | Risk |
| Voice notes / push-to-talk ([LIVE_VOICE_V1](../LIVE_VOICE_V1.md)) | Gap† | Gap† | Gap† | Gap† | Gap† | Gap† | Gap† |

† Voice notes and live push-to-talk exist in the Android app but were not
part of the concept the personas saw. They are absent from the browser
client, gateway posts, bundles and any board notice. They also lack what
voice users asked for: a "forwarded, original speaker unknown" label, a
compression and length budget suited to BLE hops, press-and-hold recording
that behaves like WhatsApp, and a local-language UI. For these personas the
practical verdict stays **Gap** until those are in place.

**How to read the matrix.**

- No capability is marked **Solves** by a majority. The only near-universal
  win is a property, not a feature: **no account and no phone number**, which
  every persona valued ("Our phone numbers are basically our identities
  here").
- The **board** is the most promising capability, but no spec exists for it.
- The **wipe** is unanimously a risk. That reflects how it is built and
  presented, not whether a wipe should exist at all.
- **Animated QR** suits a specialist carrier role, such as Thoibi's crossing
  carrier, Priya's jammed-radio fallback or Farhan's in-person key exchange.
  It is not a feature for everyone.

---

## Gaps BuzzChat does not address yet

| Gap | Who needs it | Nearest existing spec |
| --- | --- | --- |
| Full UI in Gurmukhi, Marathi, Assamese, Kashmiri and Meiteilon, finished Hindi, Tamil and Urdu, plus read-aloud (TTS) and voice-first navigation | All | None (resources under `app/src/main/res/values-*`) |
| Signed group, role or organisation identities, vouched for in person, with a visible seal; amendments and cancellations must carry the same seal | G R L T P F A | None. Per-post keys in [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) (*Author key*) are deliberately unlinkable |
| A bulletin board spec: structured notice types (needs, rota, headcount, venue change), expiry, corrections attached to the original, no reader or presence lists | All | None |
| Session-based radio ("meeting mode" / "sync now") with a countdown and a completion summary ("3 sent, 5 received, nothing left to pass") | A L R F G | Power sections of [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) and [WEB_JOIN_V1](../WEB_JOIN_V1.md) |
| Private store-and-forward to a known contact outside the region (courier DM) | A G P | Explicit non-goal of [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) (*Non-goals*) |
| Feature-phone path: a "notice friend" role with loudspeaker playback, a print or copy layout for the wall | R L G | None. KaiOS is not in the [WEB_JOIN_V1](../WEB_JOIN_V1.md) device matrix |
| Marshal-only "safe / not yet seen" roster in a private group | P A | None |
| Evidence provenance: signed press mode, hash manifest, original vs. compressed, metadata strip, optional face blur, "protect source" label inside the file, "send to a verifier" | F P R | Thumbnail re-encode rules in [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) (*Media*) only |
| Slow, resumable document transfer in a separate "Files" area | A | `docs/file_transfer.md` (not scoped for this) |
| Block, mute and report; no unsolicited adds or DMs; no profile photos by default | T R L A P | None |
| APK integrity check that people can understand (short spoken or visual fingerprint) | G F L A R | Required, but not designed, in [OFFLINE_BUNDLE_V1](../OFFLINE_BUNDLE_V1.md) (*Future: signed app-update bundles*) |
| Resource budget (APK size, RAM ceiling, cache caps, published battery figure) and coexistence testing with heavy foreground apps | L G A T F P | None |
| Discreet notifications in the unlocked shade by default ("1 new update") | L A T R | Partly in `NotificationManager.kt` |
| "If someone opens this app on your phone" screen and a "What this app is" screen in local languages | A R P L | None |
| Backlog rate limiting on reconnect for low-RAM phones | L | Flood control sections of the three specs (mesh side only) |

## Risks BuzzChat could create

These are harms the product itself could cause or make worse. Each comes
with the mitigation direction the personas pointed to. Mitigations are
product privacy measures and honest disclosure. They are not advice on
evading anyone.

| Risk | How BuzzChat could cause it | Personas | Mitigation |
| --- | --- | --- | --- |
| **Misinformation at scale** | An anonymous board and gateway posts are strongest exactly when no other source of truth exists. Anonymous outward posts and media reach the whole internet. "Seen by N" counts make rumours look credible | All | Show unverified posts as unverified. Turn public media off by default. Never publish anonymous photos or video outward. Add a "seen with my own eyes?" prompt before forwarding. Label voice notes as forwarded or original. Attach corrections to the original notice. Drop raw counts and show "confirmed by N people you know" instead. Never hide posts by crowd vote |
| **Identifying users by detecting the app** | A fixed BLE service UUID, a stable advertised peer ID, the `DIRECT-BC-` SSID, the name "bitchat", always-on advertising. While few people use it, the app works as a badge | All | Turn radio off by default outside sessions the user starts. Research rotating, non-distinctive advertising within bitchat compatibility (MESH_GATEWAY_V1 *Residual risks* already names this). Use a neutral name and icon. Promote everyday uses (floods, cyclones, melas, health camps) so that carrying the app is ordinary. Describe what remains detectable plainly in local languages |
| **Coerced unlocking and device seizure** | E2E encryption doesn't protect an unlocked phone. Stored contacts, chats, media and peer history expose a whole network (Farhan's sources, Lakshmi's union list) | A F R P L T | Make retention the main defence: expiry by default, no peer-met log, no sync-time history, no media saved to the gallery, no membership register. Offer an app-only wipe in tiers (source links, chats and keys, keep media) that reports exactly what it cleared. Say honestly that a seized, unlocked phone cannot be fully protected |
| **The wipe as evidence or false comfort** | A visible red button looks like guilt and could look like destroying evidence. Users may believe the camera roll was cleared | All | Move the wipe out of the main UI. Make it a deliberate gesture. Confirm in the user's language what it clears and what it cannot touch. Never show a blank "Wiped!" screen. Get legal-implications text written by qualified local counsel |
| **Harassment and doxxing** | Profile photos, nicknames tied to messages, re-sharing someone's words under their name, anonymous harassers who can't be blocked, outward media showing faces | P T R G L | No photos or numbers by default. Temporary names, removed when the person leaves a group. Block, mute and report. Groups by invitation only. Strip metadata and offer face blur (warning that it can miss faces) before anything goes outward. Say screenshots cannot be prevented |
| **Legal exposure** | Carrying, relaying or wiping may have legal consequences that users don't understand. Gateway operators carry others' posts | R F A | Make relaying opt-in per session, with time, size and type limits and a summary of what is being carried. Provide a "What this app is" screen reviewed by local legal experts. Do not have the product team give legal advice |
| **The hub person becomes a target** | One visible phone carries everyone, holds certain authorship of browser guests' posts (WEB_JOIN_V1 *Residual risks*) and drains its battery | L P G | Rotate hub duty. Never label a device "Hub" or "Leader" in any UI. Tell the host about the exposure (already required). Show a clear "you are disconnected" state |
| **Family and community conflict** | App locks, disguises and vanishing messages read as an affair or a secret. A shared phone puts a child at risk during a check | L A R G | Design to be opened openly: make every screen safe to show family, use an ordinary name, show no previews. Support separate installs per family member. App lock is optional and never pushed (see Tensions) |
| **Splitting the community** | Smartphone users get notices first. Button-phone and non-English households are left out ("Being informed late feels the same as being left out.") | R L G | Add a notice-friend role, loudspeaker playback and a print-for-wall layout. Measure KaiOS support. Never market the app as for everyone until these exist |
| **A register for supervisors or informers** | Reader lists, presence lists, member counts and "N nearby users" on the public board | L A R | Never show reader, presence or member lists on public surfaces. Show only a rough audience size in private layers, if at all |
| **Compromised distribution** | A trojaned APK served by a fake hub, or passed from phone to phone (OFFLINE_BUNDLE_V1 *Future* section) | F G L A | Show the release-signing fingerprint on the hub page and in the app as a short code people can read aloud. Rely on reproducible builds (`docs/reproducible-builds.md`). Get an independent audit before targeting journalists |
| **Carrier exposure** | A person seen holding or showing an animated QR is visibly carrying protest posts. Bundle contents hint at where the carrier has been (OFFLINE_BUNDLE_V1 *Residual risks*) | A T F | Keep the existing warnings. Add the carrier-mode summary of counts and types. Let carriers choose what they carry and never carry blind |
| **Overclaiming reach** | People assume the mesh reaches the next village or family 300 km away | G A T R L | Show honest per-post status ("still here", "left with a courier", "reported outside, unverified"). Onboarding says plainly that it works inside a crowd, not between villages |

### Tensions between personas (must be resolved with real participants)

- **App PIN or lock.** Aamir and Thoibi want an app-level PIN. Lakshmi says
  an app lock is itself evidence at home. → Make it optional and off by
  default, and never use security language to push it.
- **Disappearing messages.** Aamir wants them by default. Lakshmi says
  vanishing messages look secret. → Use expiry framed as "notices end after
  the event", like paper on a wall, rather than "vanishing".
- **Identity.** Farhan needs a stable signed press identity, and Ramesh needs
  a committee seal. Gurpreet and Aamir want no personal attribution at all. →
  Keep group, role and press keys strictly separate from per-post anonymous
  keys, and make each choice explicit on every compose screen.
- **Who verifies.** Gurpreet trusts committees vouched for in person.
  Thoibi says no one in a divided place is trusted by both sides. → Use a web
  of trust the user chooses, never a central or default authority.
- **"Looks like a notes app" vs. no disguise.** Thoibi asked for the app to
  look "like an ordinary notes app" when closed. Aamir, Priya and Lakshmi say
  any disguise is worse than the app itself if discovered. → Ordinary
  messenger name and icon, nothing shown when closed, but never pretend to be
  another kind of app (see *Things NOT to build*).
- **Unverified posts: hide or label?** Thoibi would hide unverified posts by
  default on the area board. Gurpreet wants them shown and labelled ("Unknown
  sender: nobody has confirmed") so she can point at the label. → Show
  labelled and visually collapsed, never silently removed; test both with
  real participants.
- **Voice-first vs. Deaf and hard-of-hearing users.** Every persona pushed
  voice. → Voice must always have a text or visual equivalent (transcript,
  icon cue, vibration), never be the only path.

---

## Prioritised recommendations

Priority: **P0** = before any field pilot; **P1** = before wider release;
**P2** = later or exploratory.

### (a) Changes to principles and specs

| Pri | Change | Document |
| --- | --- | --- |
| P0 | Replace "keep the emergency wipe one obvious tap away" with: **"Store little. The wipe clears only BuzzChat data, takes a deliberate action, and says exactly what it did and did not clear."** | [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md) (*Commitments*); panic notes in all three specs |
| P0 | Add a commitment: **"Ordinary, not hidden."** Neutral name and icon, no disguise mode, no lock-screen or shade previews by default, every screen safe to show a family member | [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md) |
| P0 | Restate the ten-second test as: "...in their own language, by voice if they cannot read, on a shared, full, low-battery phone." Allow a one-time setup by a trusted local helper. Daily use stays at ten seconds | [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md) (*Rule 1*) |
| P0 | Extend the Rule 2 device table with Android Go (2 GB), shared phones, old iPhones and feature phones. For each, state the supported role or say "not supported, served via notice friend" | [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md) (*Rule 2*) |
| P0 | Add a commitment: **"Radio on purpose."** No background advertising by default. Mesh runs in sessions the user starts, with a visible timer | [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md); *Power* in [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) and [WEB_JOIN_V1](../WEB_JOIN_V1.md) |
| P0 | Add a commitment: **"No registers."** Public surfaces never show reader lists, presence lists or member counts | [BUZZCHAT_PRINCIPLES](../BUZZCHAT_PRINCIPLES.md) |
| P1 | Add a resource budget section: APK size, RAM ceiling, relay and media cache caps, idle drain target, coexistence testing with a heavy foreground app on Android Go | New section in each spec's *Test plan → Device*; Mesh Lab entries |
| P1 | Gateway: make relay opt-in **per session**, with a time limit, a data cap and a carried-content summary. Add an optional **signed organisation or press author mode**, kept strictly apart from per-post keys. Require an "unverified" label on anonymous outward posts. Forbid outward publishing of anonymous media | [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) (*Author key*, *Gateway behavior → Enabling*, *Media*, *UI requirements*) |
| P1 | Join by link: add KaiOS, Android Go and old iOS to the device matrix. Add a hub status line naming connection quality, and a hub hand-off and rotation story. Research a less distinctive SSID than `DIRECT-BC-`. Add voice-note *playback* to the browser as a scoped exception to the voice non-goal. Put the release fingerprint on the hub page | [WEB_JOIN_V1](../WEB_JOIN_V1.md) (*Non-goals*, *Test plan §5*, *UI requirements*, *Residual risks*) |
| P1 | Offline bundles: define a **carrier mode** (one big button, chosen content types, a summary of counts and types). Reserve an item type for **sealed board notices**. Move the APK-verification prerequisite out of *Future* into a planned spec | [OFFLINE_BUNDLE_V1](../OFFLINE_BUNDLE_V1.md) (*Bundle contents → Reserved*, *Future: signed app-update bundles*) |
| P1 | Voice: set a compression and length budget for multi-hop BLE, add a "forwarded" flag carried with the note, and define press-and-hold recording behaviour | [LIVE_VOICE_V1](../LIVE_VOICE_V1.md) and the voice-note path |
| P2 | Revisit the non-goal "not a replacement for Noise DMs" with a separate **courier DM** spec: encrypted, store-and-forward to a known contact, with honest delay estimates | New spec; cross-reference [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) (*Non-goals*) |

### (b) New features

| Pri | Feature | Serves | Notes |
| --- | --- | --- | --- |
| P0 | **Language and voice-first UI.** Finish `hi`, `ta`, `ur`. Add `pa-rIN` (Gurmukhi), `mr`, `as`, `ks`, `mni` (Bengali script and Meetei Mayek). Read notices aloud with TTS. Big push-to-talk button. Explain each OS permission in the user's language before the system prompt appears. Remove jargon ("only your group can hear this" instead of "end-to-end encrypted") | All | Any English string in a core flow blocks release for that locale. Pre-permission voice clips. Large-type mode |
| P0 | **Session radio and low-power mode.** "Meeting mode" with a 10 min to 3 h timer, a sync summary, and relaying only while charging if the user chooses. Show a battery estimate in plain words | A L R G F P | Must stay compatible with bitchat neighbours. Measure on 3-year-old phones with full storage |
| P0 | **Discreet notifications.** Default to "1 new update" with no sender or content, even when unlocked. Option to turn notifications off. No alerts at night unless a sealed notice is marked urgent | L A G T | Small change in `NotificationManager.kt`, large effect |
| P0 | **Honest limits screens.** First-run "what this can and can't do" (crowd range, no livestreaming, detectable radio, wipe limits). An "If someone opens this app on your phone" screen. A shareable "What this app is" screen. All with voice versions | All | Legal wording reviewed by qualified local counsel |
| P1 | **Verified-source labels.** Group, role, medic or press keys granted face to face by QR. A visible seal plus a spoken line ("from the Samiti, seal checked"). Cancellations and changes must carry the same seal. Conflicting unsealed notices are flagged. Trust is shown as "confirmed by N people you know" | G R L T P F A | Web of trust, no central or default authority. Warn that vouching groups can become targets |
| P1 | **Bulletin board v1 (new spec).** Templates for needs (item, quantity, unit, place, needed-by), rota or shift, headcount ("how many coming / bringing what"), venue change. Expiry and a "fulfilled" state. Expired notices pruned from the mesh. No reader lists. Copy or print layout | G T R L P A | Write `docs/BULLETIN_BOARD_V1.md`. Reuse gateway validation and flood control patterns |
| P1 | **Rumour friction.** Unverified label by default. Public media off. "Seen with my own eyes?" before forwarding. Forwarded/original on voice notes. Corrections attached to the original. "I'm here: confirm / not happening" markers, only if this leaks no location. Notices fade fast. No crowd-vote hiding | All | Never style counts as endorsement |
| P1 | **Exact-list flow.** Send a text list that arrives exactly as typed, with delivered and read ticks and "confirmed" on the same list | T G L | Flagship day-one flow for coordinators |
| P1 | **Courier and carrier mode.** One-button carrier for relatives, messages that wait for days, per-post status ("still here / left with a courier / reported outside"), backlog rate limiting on reconnect | G A T R L | Builds on bundles and gateway store-and-forward |
| P1 | **Harassment controls.** Block, mute, report. No unsolicited adds or DMs. Invite-only groups. No profile photos by default. Name removed when someone leaves | T R L A P | Needs design for key-based blocking without accounts |
| P1 | **APK integrity check people can understand.** A short code shown on the sharing phone and the receiving phone, readable aloud. Signing fingerprint on the hub page | G F L A R | Depends on reproducible builds |
| P1 | **Feature-phone path.** Notice-friend role with loudspeaker playback. Measure KaiOS browser support and publish the result, even if the answer is "no". Optional call or SMS relay only when a network exists | R L G | Do not claim support before testing |
| P2 | **Safe / not-yet-seen roster.** Private group, marshal-only edits, never public | P A G | Never expose names outside the group |
| P2 | **Evidence provenance and "send to a verifier".** Signed press mode, hash manifest, original vs. compressed, per-clip choice of what metadata to keep (time only / time and place / nothing), optional face blur, "protect source" label that warns when the file is opened | F P R | Independent audit and press-freedom review first |
| P2 | **Slow document transfer** in a separate Files area, resumable, showing who posted first and when | A | Keep it apart from chat and notice syncs |
| P2 | **Pilot kit.** Facilitator scripts in local languages, a 5-minute explainer, a 5-20 person drill mode with an "I can see N people" indicator, failure messages that name the cause | All | Supports silent-abandonment monitoring without telemetry |

### (c) Things NOT to build

- **No disguise or decoy mode** (calculator icons, fake apps, decoy duress
  profiles). Every persona said being caught with one is worse than having
  the app, and a decoy shown under coercion raises legal exposure. If
  "duress" comes up, the answer is to store less, not to pretend.
- **No visible red panic button** on the main screen, and no wipe that
  claims to clear what it cannot clear.
- **No raw "seen by N", "N nearby users", reader lists, presence lists or
  member counts** on public surfaces.
- **No crowd-vote hiding or fading of posts.** In a divided place it buries
  true but unwelcome information.
- **No default or central verifying authority**, whether government,
  political or community-named.
- **No single labelled "Hub" or "Leader" device** that everyone depends on.
- **No anonymous outward publishing of photos or video.**
- **No promises of livestreaming, inter-village reach, or "invisibility".**
- **No automatic saving of media to the gallery**, and no background mobile
  data use.
- **No live location, proximity or geofencing features** for this context.
  Manual coarse geohash stays off by default, as in
  [MESH_GATEWAY_V1](../MESH_GATEWAY_V1.md) (*Privacy*).
- **No usage telemetry to detect abandonment.** Use in-person pilot
  follow-ups instead.
- **No protest-branded identity** in name, icon, store listing or onboarding
  copy.

---

## Missing perspectives

The seven personas leave out groups whose needs could change the design, not
just extend it. Write these next, keep them fictional, and treat each as a
prompt for recruiting real, consenting participants through organisations
they already trust (see *Validation protocol*). Do not write any persona that
reveals an LGBTQ+ person's identity, a disability or a caste in a way that
could be used to single out real people.

| Persona to add (fictional) | Why it matters for BuzzChat | Questions it should test |
| --- | --- | --- |
| A blind or low-vision volunteer who uses a screen reader (TalkBack) on a budget Android | Every core flow, QR join and seal check assumes sight. Animated QR is unusable without help | Do all screens work with TalkBack in Indian languages? Can a seal or "you know this person" mark be heard, not only seen? |
| A Deaf protester who uses Indian Sign Language and reads some written Hindi or English | Voice-first design, the main recommendation here, could exclude them entirely | Text and visual equivalents for voice notes and spoken alerts; video-sign clips vs. data and battery limits; vibration cues |
| An older person with no phone of their own (or a basic non-KaiOS keypad phone) | The "notice friend" idea makes them dependent on someone else; Ramesh speaks *for* them, not as them | Is a notice friend trusted or a new gatekeeper? What does "informed at the same time" mean without a device? |
| A trans woman or other LGBTQ+ person in a protest or mutual-aid network, possibly on a shared family phone | Outing through a shared or checked phone, harassment and doxxing are severe risks; the "every screen safe to show family" principle may not be enough | What must never appear in notifications, contact names or group lists? Is a separate install on a separate phone realistic? |
| An Adivasi forest-rights or anti-displacement organiser in a sparse forest or hill area (for example in Jharkhand, Chhattisgarh or Odisha), speaking Santali (Ol Chiki script), Gondi or Kurukh | Settlements far apart, weak power, languages and scripts with little keyboard or TTS support; a crowd-dependent mesh may not fit at all | Do carried store-and-forward and weekly-market syncs work here? Which scripts and TTS voices exist? Who are trusted intermediaries? |
| A relief volunteer from the hill-district side of the Manipur conflict, writing in a Kuki-Chin language in Roman script | Thoibi represents one community; a one-sided study risks designing trust features that one side would reject | Would that volunteer trust the same hospitals or groups as verifiers? Does a "both communities" audience feel safe or threatening? |
| An inter-state migrant worker (for example a construction or factory worker far from home) whose language differs from the local one | Language mismatch with local notices, a single shared phone in a dormitory, and family hundreds of kilometres away | Multilingual notices, the courier DM to family, and whether employers or contractors could misuse rosters |
| A volunteer street medic or legal-aid volunteer at protests | The board's trust model leans on medic and legal roles; their own exposure as role-holders is untested | What does carrying a role seal cost them? How are seals handed over or revoked when a phone is lost? |
| A Muslim woman student or a woman from a religious minority who faces online abuse | Harassment controls are designed from secondhand accounts (Aamir, Priya, Farhan) rather than from the people most targeted | Which defaults (no photos, temporary names, block and report) actually matter, and what is missing? |

**Partly covered by the Gen Z set** ([GENZ_RESEARCH_SYNTHESIS](GENZ_RESEARCH_SYNTHESIS.md)).
Each of these is still a fictional persona, not a substitute for real
participants:

| Gap above | Gen Z persona | What it now covers | What is still missing |
| --- | --- | --- | --- |
| LGBTQ+ person | [Ritu](personas/genz-queer-student.md), queer student (she/they), out to friends but not family | Outing through notifications, gallery, group names and morphed images; invite-only safety channels; a household check as the main threat | A trans person's experience; a shared family phone (Ritu has her own); anyone outside a student setting |
| Inter-state migrant worker | [Imran](personas/genz-gig-worker.md), food-delivery rider (gig worker) | Time and battery poverty, a phone that is a work tool, multilingual low-text needs (Telugu, Urdu, Dakhni), voice notes, distrust of sideloaded APKs | He lives at home in his own city: a dormitory shared phone, a local-language mismatch and family far away are still uncovered |
| Volunteer street medic or legal-aid volunteer | [Arjun](personas/genz-volunteer-medic.md), field medic and coordinator | Role seals handed out in person, revocation by two leads, identity-free patient handover, urgent requests with acknowledgement | A legal-aid volunteer |
| Muslim woman student or minority woman facing online abuse | [Meera](personas/genz-factchecker.md) (gendered trolling after a debunk) and [Imran](personas/genz-gig-worker.md) (communal targeting of a Muslim-named man) | Harassment after public verification; writing style and names as targeting cues | A Muslim woman's own perspective is still missing |
| Bhojpuri speakers (see *Known biases*) | [Kabir](personas/genz-meme-admin.md) | Hindi-first UI with Bhojpuri strings for relatives | Other less-supported languages listed above |

The Gen Z set adds no coverage for blind, low-vision or Deaf users, people
without a phone, Adivasi communities, or the other side of the Manipur
conflict. Those rows stay open.

Also consider, for adversarial review rather than as users: a person who
wants to disrupt a peaceful gathering with fake notices, and a family member
who checks phones. These are threat-model exercises, not interviews. Do not
recruit minors; reach young people's needs through their teachers, families
and youth workers instead.

## Open research questions for real-world validation

1. **Detectability.** Does a less distinctive BLE advertisement within
   bitchat compatibility meaningfully reduce identifiability in practice?
   How do real users weigh "visible while used" against "useless when off"?
2. **Session radio.** Will people actually start sync sessions at the shop,
   the milk centre or a gate? How long must a session be to move a day's
   notices on cheap phones?
3. **Voice.** What clip length and codec give acceptable quality over 3-5 BLE
   hops on 3-year-old phones? Do forwarded/original labels change how much
   people trust or forward a voice note?
4. **Seals.** Can committees and roles be vouched for in person in under a
   minute, and how do they hand over or revoke a seal when a volunteer leaves
   or a phone is seized?
5. **Rumour friction.** Which mechanisms (unverified labels, the
   "seen with my own eyes" prompt, attached corrections, contact-based
   confirmation) reduce harmful forwarding without silencing true urgent
   warnings?
6. **Feature phones.** Does the browser client run on current KaiOS devices
   at all? Would a notice friend with a loudspeaker be accepted, or would it
   create a new gatekeeper?
7. **Shared phones.** On a shared phone, which is safer and more acceptable
   per household: separate installs, an optional app PIN, or neither?
8. **The wipe.** What do real users expect a wipe to remove? Does a tiered,
   honest wipe reduce false confidence? What do qualified local lawyers say
   about wiping and relaying?
9. **Range.** In real camps, villages and campuses, how far does a notice
   travel in an hour with carried store-and-forward, compared with a live
   mesh?
10. **Outbound credibility.** Will editors, legal-aid groups and officials
    accept "signed by a known group" or provenance manifests? Does vouching
    put those groups at risk?
11. **Resource coexistence.** On Android Go with a heavy government app in
    the foreground, does any BuzzChat background activity cause that app to
    be killed or freeze?
12. **Abandonment.** How can silent drop-off be detected in pilots without
    telemetry or identifying follow-up?
13. **Everyday use.** Which non-protest uses (cyclone and flood notices,
    health camps, melas) would real communities adopt, enough that carrying
    the app is unremarkable?
14. **Who is missing.** How do people with no phone, people with
    disabilities, LGBTQ+ people, Adivasi communities, speakers of
    less-supported languages, the other side of the Manipur conflict, and
    people the tool could be used against experience BuzzChat? Those groups
    are not represented in this study (see *Missing perspectives*).
