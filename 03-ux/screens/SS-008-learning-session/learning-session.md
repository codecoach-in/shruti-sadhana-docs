# Screen Specification: Learning Session (SS-008)

**Status:** ✅ Frozen (Pilot v1)

---

## 1. Screen Purpose

The **Learning Session** screen serves as the core chanting interface of the application. It teaches exactly **one Shloka** at a time, providing structured text layouts, audio playback guidance, and a chanting recorder to let devotees revise and validate their chanting independently.

---

## 2. Screen Layout Hierarchy

The screen layout is divided into vertical blocks scrolling inside the Warm Ivory background:

```text
+-------------------------------------------------------+
|  [Exit]            Lalita Sahasranama       [Settings]|  <-- Header
+-------------------------------------------------------+
|                                                       |
|   +-----------------------------------------------+   |
|   |                  Verse 1                      |   |  <-- Sacred Manuscript
|   |                                               |   |      (Fills majority of screen)
|   |         कल्याणगुणबिम्बाय नमोस्तुते...          |   |
|   |                                               |   |
|   |         [Script Selector / Font Gear]         |   |
|   +-----------------------------------------------+   |
|                                                       |
|   +-----------------------------------------------+   |
|   |  ( ▶ )   ========= 01:24 / 02:30              |   |  <-- Audio Player (Normal speed only)
|   +-----------------------------------------------+   |
|                                                       |
|   +-----------------------------------------------+   |
|   |  [🎙️ Tap to Record Chanting]                 |   |  <-- Record Yourself
|   +-----------------------------------------------+   |
|                                                       |
|   +-----------------------------------------------+   |
|   |  ● Recording 1  (▶) [Delete]                  |   |  <-- My 3 Recordings
|   |  ● Recording 2  (▶) [Delete]                  |   |      (Max 3)
|   +-----------------------------------------------+   |
|                                                       |
|               🌸 Samarpan (Submit Chant)               |  <-- Samarpan Button
|                                                       |
+-------------------------------------------------------+
```

### 2.1 Component Specifications

1. **Header**: Contains the back/exit button to return to the Lesson Index (SS-007) and the course title metadata.
2. **Sacred Manuscript Container**:
   - Fills the majority of the screen space.
   - Employs a custom background resembling classical palm-leaf manuscripts with red/gold borders.
   - Displays the **Sanskrit Text** (Devanagari) in display-medium font size.
   - Displays the **English Transliteration** and **Meaning** details.
   - Contains a small **Gear Icon** (in the bottom-right corner of the manuscript card) to open the Script & Font Size bottom sheets.
3. **Audio Player**: Simple streaming playback bar (Play/Pause, track progress bar, time elapsed/duration).
4. **Record Yourself**: A single tap-to-record mic controller. When recording, it turns into a red pulsing stop button.
5. **My 3 Recordings**:
   - A unified list container holding up to **3 recordings** max.
   - Each item shows the title, length, a play button for self-review, and a delete icon.
6. **Samarpan Button**:
   - A large, prominent button styled with floral icons (🌸) at the bottom.
   - Action: Submits the chanting progress and navigates to the **Samarpan Screen** (SS-009).

---

## 3. Design Decisions

- **Immersive Manuscript-Centric Focus**: The screen background, typography, and borders prioritize the shloka text. Unnecessary buttons, navigators, or progress paths are removed to keep the devotee focused.
- **Simplistic Play-Only Audio**: Normal speed streaming is the only option. Advanced adjustments like loop modes, custom speeds, or scrub controls are removed to keep implementation and UI clean.
- **Recording Limit (Max 3)**: Restricting to three recordings prevents local database clutter and encourages users to choose and evaluate their best chanting efforts.
- **Samarpan Offering Action**: The learning session does not end with a generic "Done" checkbox. It culminates in an active, devotional "Samarpan" (offering) flow.

---

## 4. Explicitly Out of Scope

The following options are deferred and must **not** be implemented:
- Loop toggles
- Multi-speed playbacks (slow, medium, fast)
- Word-by-word highlighting overlays
- Line-by-line highlight selection tabs
- Premium player layout drawers
- AI Pronunciation scoring displays

---

## 5. User Recording Lifecycle (Functional Specification)

This section defines the behavior of devotee chanting recordings at a functional level:

* **Maximum Recordings**: Devotees can save a maximum of **3 recordings** per Shloka. If a user attempts to record a 4th track, the application must prompt them to delete one of the existing recordings via the Recording Options Bottom Sheet (`SS-BS-003`) before starting a new recording.
* **Local vs. Cloud Storage**: Chanting recordings are stored **strictly locally** on the device's storage. No user audio recordings are uploaded to the backend server or cloud database.
* **Deletion Behavior**: Deleting a recording permanently removes the audio file from local storage.
* **Device Migration Expectations**: Since recordings are stored locally, recordings will **not migrate** if a user switches devices or clears application data.
* **MVP Limitations**: Chanting recordings are intended for real-time self-evaluation only. No permanent audio archiving, sharing, or backend backup capabilities are supported in this phase.

