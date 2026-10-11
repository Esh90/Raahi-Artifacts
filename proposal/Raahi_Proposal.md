# Raahi (راہی): A Path You Can Hear

**AI-powered indoor navigation in Urdu and English for blind and visually impaired people, using only an Android phone.**

| | |
|---|---|
| **Team** | Tech Titans: Esha Shabbir, Uswa Khan, Abeera Amir |
| **Institution** | National University of Sciences and Technology (NUST), Islamabad |
| **Track** | AI in Healthcare (assistive technology / accessibility) |
| **Status** | Working prototype, end to end (Android app + AI server) |
| **Project artifacts** | [github.com/Esh90/Raahi-Artifacts](https://github.com/Esh90/Raahi-Artifacts) |

---

## 1. Project Overview & Problem Statement

Vision loss affects a very large number of people worldwide, and most of them live in low- and middle-income countries like Pakistan.

- **At least 2.2 billion people** worldwide have a near or distance vision impairment. For at least 1 billion of them it was preventable or has not been treated *(WHO, Blindness and Vision Impairment fact sheet; World Report on Vision, 2019)*.
- Around **90% of people with vision loss live in low- and middle-income countries** *(WHO / IAPB)*.
- In **Pakistan**, the Ministry of Health's Third National Blindness Survey (2019–21) estimated **about 9 million people aged 50 and over** with some vision impairment, about **484,000 of them blind** *(Ministry of NHSR&C, reported Sept 2022)*. The figure covers only people aged 50+, so the national total is higher.

Treatment fixes the causes that can be fixed. People who are already blind still have to move through the world every day, and indoors is where the tools stop working:

1. **The white cane** finds obstacles only within arm's reach and at ground level. It cannot tell you that a chair is three steps ahead, which way to step around it, or where the door is.
2. **GPS navigation apps** do not work indoors (homes, hospitals, offices, universities, shopping malls).
3. **AI apps that describe a photo** say *what* is in the picture. They do not say *how far away* it is or *where to step*, and they need a still photo, not continuous guidance while you walk. Most do not speak Urdu.
4. **Smart wearables cost far more than most people here can pay.** OrCam MyEye Pro costs about **US$4,250**, Envision Glasses about **US$2,500–3,500**, and the WeWALK Smart Cane 2 about **US$850–1,000**. In Pakistan that is several months to years of income.
5. **Language.** Almost all of these tools are designed for English speakers. A blind grandmother in Lahore who speaks only Urdu or Punjabi cannot use them.

**The gap:** no affordable, Urdu-speaking tool tells a blind person in real time *what is in front of them, how far away it is, and which way to move*, using a phone they may already own.

## 2. Proposed Solution

**Raahi** turns an ordinary Android phone into a talking guide. The user holds the phone in front of them, or wears it on a chest strap, and Raahi speaks short instructions in Urdu or English:

> *"Ruk jayein. Saamne kursi hai, 3 qadam pr. 2 qadam baayein jayein."*
> ("Stop. Chair ahead, 3 steps. Move 2 steps left.")

**How it works (See → Measure → Decide → Speak):**

1. **See:** the phone camera streams frames to the AI server. **YOLOE**, an open-vocabulary real-time object detector, identifies 28 everyday indoor objects such as people, chairs, doors, tables, sofas, beds and bins.
2. **Measure:** **Depth Anything V2** (metric indoor), a monocular depth-estimation model, gives the distance in metres to everything in view, including objects the detector has no name for, such as walls and boxes. The scene is split into left, centre and right zones.
3. **Decide:** a safety-first navigation engine turns distances into actions:
    - **< 0.8 m:** *Stop*, plus a way out (sidestep left or right, or step back), with strong vibration.
    - **0.8–2.0 m:** name the obstacle, give the number of steps to it, and say which side is clearer and by how many steps.
    - **> 2.0 m:** path clear.
    - Hysteresis, smoothing across frames and a "sticky side" rule keep the advice steady instead of flickering left and right.
4. **Speak:** pre-recorded, natural female-voice clips in **Urdu and English** are put together into sentences on the phone, so speech starts with almost no delay. Danger also triggers vibration.

![Raahi system architecture](https://raw.githubusercontent.com/Esh90/Raahi-Artifacts/main/diagrams/system-architecture.png)

**Hands-free voice control:** the user says *"Raahi"* plus a command in Urdu, Roman Urdu or English (*"Raahi, darwaza dhoondo"*, "find the door"; *"Raahi, aage kya hai?"*, "what's ahead?"; *"Raahi, settings kholo"*, "open settings"). Recognition is by ElevenLabs Scribe, and commands are matched on the phone.

![Raahi user flow](https://raw.githubusercontent.com/Esh90/Raahi-Artifacts/main/diagrams/user-flow.png)

**Why this is different**

| | White cane | Photo-AI apps | Smart glasses / cane | **Raahi** |
|---|---|---|---|---|
| Distance in metres | ✗ | ✗ | Partly | **✓** |
| Tells you *where to step* | ✗ | ✗ | ✗ | **✓** |
| Continuous, real-time | ✓ | ✗ | ✓ | **✓** |
| Urdu voice | n/a | ✗ (mostly) | ✗ | **✓** |
| Works indoors, no map | ✓ | ✓ | ✓ | **✓** |
| Cost to user | Low | Free | US$850–4,250 | **Phone the user already has** |

## 3. Objectives & Expected Outcomes

**Objectives**

1. Give blind users continuous, metric, direction-giving guidance indoors, in their own language.
2. Keep the time from camera frame to spoken guidance low enough for walking pace (target ≤ 300 ms; ~78 ms measured for the processing pipeline).
3. Make the app fully usable without sight: voice control, haptics, a screen-reader-friendly UI and high-contrast themes.
4. Need no extra hardware: a mid-range Android phone is enough.

**Expected outcomes**

- A working Android app and AI server, demonstrated live (already achieved at prototype level).
- Fewer collisions and more confidence when moving through unfamiliar indoor spaces, measured in user trials (see §6).
- An open, extensible Urdu assistive-voice and detection pipeline that other Pakistani accessibility projects can reuse.

## 4. Target Users

- **Primary:** blind and low-vision people in Pakistan, especially Urdu-speaking, older and lower-income users who cannot pay for wearables.
- **Settings:** homes, hospitals and clinics, universities, offices, shopping malls, mosques and other indoor public spaces where GPS does not work.
- **Secondary:** organisations for the blind (e.g. Pakistan Association of the Blind), special-education schools, eye hospitals and rehabilitation centres, which can give Raahi to patients after a diagnosis that cannot be treated.

## 5. Key Features / Deliverables

**Built and working:**

- Real-time obstacle detection (YOLOE, 28 indoor classes; ~29 ms per frame on a laptop CPU for the exported ONNX model).
- Metric depth estimation (Depth Anything V2 Small, metric-indoor; ~67 ms on Apple M2).
- Safety-first navigation engine: stop / sidestep N steps / turn / step back, with hysteresis and smoothing.
- **Find mode:** *"Raahi, darwaza dhoondo"* guides the user to a door, chair, person, etc. and confirms on arrival.
- **"What's ahead?"**: a spoken description of the scene.
- Bilingual voice (**Urdu + English**): 96+ natural recorded clips joined into sentences, plus a live text-to-speech fallback.
- Hands-free wake word *"Raahi"*, voice navigation of the whole app, and gestures (long-press to talk, double-tap to repeat, swipe to pause).
- Native Android app (Kotlin + Jetpack Compose): 38 designed screens and states, including onboarding, live guidance, danger alert, session history, accessibility settings, high contrast, dark/light themes and text size.
- Live judges' dashboard showing detections, depth zones, decisions and latency.
- An automated test suite (70+ tests) covering the navigation engine, voice intents, the server and the voice commands.

**Deliverables for the Championship:** live demo, Android APK, architecture and user-flow diagrams, and pitch deck (all supporting material in the [Raahi-Artifacts](https://github.com/Esh90/Raahi-Artifacts) repository).

## 6. Implementation Plan & Timeline

| Phase | Dates | Work | Status |
|---|---|---|---|
| **0. Prototype** | Sept – 10 Oct 2026 | Research, UX design (38 screens), detector + depth pipeline, navigation engine, Urdu voice library, Android app, judges' dashboard | ✅ Done |
| **1. Hardening** | 11 – 17 Oct | Tune the depth calibration in real rooms, improve detector accuracy on missed classes (fine-tuning), make the app more stable on devices, record the remaining clips | In progress |
| **2. User testing** | 15 – 22 Oct | Supervised trials with visually impaired volunteers; measure collisions, task completion (e.g. "find the door") and how users rate the voice; fix issues found | Planned |
| **3. Championship** | 24 Oct 2026 | Live demonstration at FAST Lahore | — |
| **4. Post-event roadmap** | Nov 2026 – Mar 2027 | **On-device inference** (TFLite, works offline, no server); stairs and step-down detection; Urdu text reading (signs, medicine labels); Punjabi, Sindhi and Pashto voices; a pilot with an eye hospital or blind association | Roadmap |

## 7. Resources or Requirements

- **Hardware:** a mid-range Android phone (Android 8+), and a laptop or cloud server for the AI pipeline (until inference runs on the device).
- **Software / models:** YOLOE (Ultralytics), Depth Anything V2, FastAPI, ONNX Runtime / TFLite, Kotlin + Jetpack Compose, ElevenLabs (voice recording and speech recognition).
- **People:** a 3-person team. Esha Shabbir (object detection and Urdu/English voice AI), Uswa Khan (depth estimation, server and judge dashboard), Abeera Amir (Android app, UX and accessibility).
- **Needed from partners:** access to visually impaired volunteers for testing (via blind associations or special-education institutes), GPU credits for fine-tuning, and mentorship on deploying in clinics.
- **Cost to the user:** nothing beyond a phone they already own. Once inference runs on the phone, there is no server cost either.

## 8. Potential Challenges or Risks

| Risk | Mitigation |
|---|---|
| **Safety:** a missed obstacle could cause a fall | Raahi *adds to* the white cane and does not replace it. Unknown objects are still caught by depth even when the detector has no label. Conservative 0.8 m danger threshold with vibration. Always gives a way out |
| Depth estimate drifts by phone or room | Calibrated against a tape measure (scale tool included); hysteresis around thresholds |
| Network delay (phone ↔ server) | Prerecorded clips play at once; the newest guidance replaces older guidance and anything older than 2 s is dropped; on-device model on the roadmap |
| Poor lighting / motion blur | Phone-orientation check, smoothing across frames; future: torch control and low-light models |
| Battery and heat | Small 320 px input, depth every second frame; on-device quantised models |
| Privacy (camera in homes and clinics) | Frames are processed in memory and not stored; no account needed; API keys kept on the server only |
| Urdu speech recognition errors | Commands accepted in Urdu, Roman Urdu and English with tolerance for typos; gestures as a fallback |

## 9. Additional Details

- **Impact on health care:** vision loss raises the risk of falls, injury, depression and social isolation, and reduces independence. Raahi supports *rehabilitation and independent living* after a diagnosis that cannot be treated, an area that gets much less attention than treatment.
- **Scalability:** the pipeline does not depend on a language (adding a language means adding a clip library) or on a map (no setup per building), so it can be used anywhere at once.
- **Pakistan first:** built in Pakistan, in Urdu, for the people current products ignore.

**Sources:** WHO, *Blindness and vision impairment* fact sheet (2026) and *World Report on Vision* (2019); Pakistan Ministry of National Health Services, Third National Blindness Survey 2019–21 (via ProPakistani, 7 Sept 2022); Dineen et al., Pakistan National Blindness and Visual Impairment Survey, *BJO* 2007; manufacturer and retailer list prices (OrCam, Envision, WeWALK; RNIB / Sight & Sound), 2024–26.
