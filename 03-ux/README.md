# Information Architecture & Navigation Specs

Purpose: Canonical reference for user experience, navigation flows, and screen specifications.

---

## 1. Pilot Screen Inventory

These screen identifiers represent the permanent reference markers for design, coding, and testing.

| ID | Screen Name | Description |
| :--- | :--- | :--- |
| **SCR-001** | Splash | App initialization, checking local state (stored script, auth token) |
| **SCR-002** | Script Selection | Allow user to choose display script preference |
| **SCR-003** | Vakratunda Experience | First guided chanting experience to demonstrate app value before sign-in |
| **SCR-004** | Google Sign-In | Commit point for authentication |
| **SCR-005** | Home | Main client dashboard containing progress widget and primary entry points |
| **SCR-006** | Library | Course catalog browsing screen (Lalita Sahasranama for Pilot) |
| **SCR-007** | Course Details | Introduces course significance, structure, trainer details, and completion progress |
| **SCR-008** | Lesson / Shloka Index | Lists course content by sections and individual shlokas |
| **SCR-009** | Learning Session | The active learning screen teaching one shloka at a time |
| **SCR-010** | Lesson Complete | Confirms success and progress save for the completed shloka |
| **SCR-011** | Progress | Displays detailed devotee learning statistics and history |
| **SCR-012** | Profile & Settings | Manage user details, script preferences, and account sync |
| **SCR-013** | Trainer Profile | Detailed biography and info on the course teacher (child screen of Course Details) |

---

## 2. Navigation Hierarchy

```
Splash (SCR-001)
    ↓
Script Selection (SCR-002)
    ↓
Vakratunda Experience (SCR-003)
    ↓
Google Sign-In (SCR-004)
    ↓
Home (SCR-005)
 ├── Library (SCR-006)
 │      ↓
 │  Course Details (SCR-007)
 │      ├── Trainer Profile (SCR-013) [Child Screen]
 │      └── Lesson/Shloka Index (SCR-008)
 │                ↓
 │         Learning Session (SCR-009)
 │                ↓
 │         Lesson Complete (SCR-010)
 │
 ├── Progress (SCR-011)
 │
 └── Profile & Settings (SCR-012)
```

---

## 3. Resume Learning Flows (SCR-009 Entry Points)

To minimize friction and encourage repeat practice, the **Learning Session** (SCR-009) has two intentional entry flows:

### Resume Flow (Direct Entry)
Serves as the primary pathway for returning users.
```
Home (SCR-005)
    ↓  [Sadhana Progress Widget click]
Learning Session (SCR-009)
```

### Browse Flow (Catalog Entry)
Serves as the exploratory pathway for searching and selecting lessons.
```
Home (SCR-005)
    ↓
Library (SCR-006)
    ↓
Course Details (SCR-007)
    ↓
Lesson/Shloka Index (SCR-008)
    ↓
Learning Session (SCR-009)
```

---

## 4. Script Selection Behavior (SCR-002)

- **Timing**: Triggered before the introductory Vakratunda Mahakaya experience (SCR-003) to ensure Sanskrit text displays in the devotee's preferred script immediately.
- **Supported Scripts**: Devanagari, English Transliteration.
- **Persistence**: 
  1. Stored **locally** on the device upon selection.
  2. Synchronized and saved to the **user's backend profile** immediately after Google Sign-In (SCR-004) completes.

---

## 5. Screen Boundaries

### Course Details (SCR-007)
- **Responsibilities**: Host course introduction, spiritual significance metadata, trainer summary, course learning structure, overall user progress stats, and primary "Start / Continue Learning" button.
- **Boundary**: Does *not* host active learning, audio player logic, or verse-by-verse chanting interfaces. These belong strictly inside the Learning Session (SCR-009).

### Trainer Profile (SCR-013)
- **Boundary**: Retained as a secondary child screen of Course Details. It is not part of the primary app navigation structure and has no direct links from Home or Profile.

---

## 6. Content Hierarchy vs. Navigation Hierarchy

We maintain a strict separation of concerns between content models and navigation paths. Do not conflate or merge them in code or specifications.

### Navigation Hierarchy
Specifies user flow and routing through the interface:
```
Home (SCR-005) → Library (SCR-006) → Course Details (SCR-007) → Lesson Index (SCR-008) → Learning Session (SCR-009)
```

### Content Hierarchy
Specifies the structural organization of the spiritual materials in the database:
```
Course (e.g., Lalita Sahasranama)
    ↓
Section (e.g., Nyasa, Main Sahasranama Stotram)
    ↓
Shloka (Individual Verse unit)
```

---

## 7. Learning Unit (SCR-009 teaching scope)

- The **Learning Session** screen teaches exactly **one Shloka** at a time.
- Longer logical sections (such as the Dhyana Shlokas or Nyasa) must be chunked and divided into separate, single-shloka learning sessions at the content model layer.

---

## 8. Deferred Navigation & Interactions

The following elements are excluded from the Pilot Release navigation tree:
- **AI Pronunciation**: AI review triggers or pronunciation quality indicator screens.
- **Premium Navigation**: Speed selector toggles (slow/medium speed) in the audio player.
- **Advanced Learning Layouts**: Toggle views for word-by-word or line-by-line learning.
- **Engagement Layouts**: Streaks calendar, badge drawers, or progress awards screens.
- **Social & Growth**: Community message boards, messaging, donation flow screens, or referral share triggers.
- **Utility Features**: Offline download screens/indicators, push notifications config, and deep linking handlers.