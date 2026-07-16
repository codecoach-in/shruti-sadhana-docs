# Screen Specification: Home Screen (SS-003 / SS-003A)

**Status:** ✅ Design Frozen (Pilot v1.0)

---

## 1. Screen Purpose

The **Home Screen** serves as the primary dashboard of the application. Its core objective is to get the user back into chanting as quickly as possible. It is designed to feel like a quiet entry path to study rather than a busy, notification-filled command center.

---

## 2. Screen Layout Hierarchy

### 2.1 Returning User Layout (SS-003)

The returning user dashboard layout consists of the following elements, ordered from top to bottom:

```text
+-------------------------------------------------------+
|  🌸 Shree Maatre Namaha                               |  <-- 1. Greeting
|  Hari Om, Sandeep                                     |
+-------------------------------------------------------+
|                                                       |
|   +-----------------------------------------------+   |
|   |             [Book Illustration]               |   |  <-- 2. Continue Learning
|   |  Continue: Lalita Sahasranama                 |   |      (Hero Card with Book/Manuscript)
|   |  Section: Nyasa - Shloka 4                    |   |
|   +-----------------------------------------------+   |
|                                                       |
|   +-----------------------------------------------+   |
|   |  Today's Learning:                            |   |  <-- 3. Today's Learning
|   |  ● Dhyanam (when applicable)                  |   |      (No per-item duration)
|   |  ● Assigned Shlokas                           |   |
|   |  ● Kshama Prarthana                           |   |
|   +-----------------------------------------------+   |
|                                                       |
|   +-----------------------------------------------+   |
|   |  Today's Progress: 12 Shlokas                 |   |  <-- 4. Today's Progress
|   +-----------------------------------------------+   |
|                                                       |
|   +-----------------------------------------------+   |
|   |                  [Native Ad]                  |   |  <-- 5. Native Ad Container
|   +-----------------------------------------------+   |
|                                                       |
|   +-----------------------------------------------+   |
|   |  Overall Progress: 45/108 Shlokas             |   |  <-- 6. Overall Progress
|   +-----------------------------------------------+   |
|                                                       |
|   +-----------------------------------------------+   |
|   |  Support Shruti Sadhana                       |   |  <-- 7. Support Banner
|   |  Help us keep stotra learning free...         |   |      (Near bottom of the scroll)
|   +-----------------------------------------------+   |
|                                                       |
+-------------------------------------------------------+
```

### 2.2 First Time User Layout (SS-003A)

When a devotee signs in for the first time, they are presented with a simplified layout focused entirely on initiating their learning:

```text
+-------------------------------------------------------+
|  🌸 Shree Maatre Namaha                               |  <-- 1. Greeting
|  Hari Om, Sandeep                                     |
+-------------------------------------------------------+
|                                                       |
|   +-----------------------------------------------+   |
|   |             [Book Illustration]               |   |  <-- 2. Course Presentation
|   |  Lalita Sahasranama                           |   |
|   |  Learn • Practice • Sadhana                   |   |
|   +-----------------------------------------------+   |
|                                                       |
|            [ 🌸 Start Journey Button ]                |  <-- 3. Start Journey Button
|                                                       |      (Routes to Setup Wizard SS-005A)
|                                                       |
|   +-----------------------------------------------+   |
|   |  Support Shruti Sadhana                       |   |  <-- 4. Support Banner
|   +-----------------------------------------------+   |
|                                                       |
+-------------------------------------------------------+
```

---

## 3. Component Details

1. **Greeting**:
   - Displays the classical Sanskrit salutation: **"Shree Maatre Namaha"** in a soft Display-Large font.
   - Includes the devotee's name in a smaller subtitle.
2. **Continue Learning Card (Hero Card)**:
   - Fills the prominent upper half of the home screen.
   - Utilizes a hand-painted manuscript/book illustration as its central visual.
   - Action: Tapping routes the user directly to **Learning Session (SS-008)**.
3. **Today's Learning**:
   - Displays the spiritual materials assigned for the day: Dhyanam (when applicable), Assigned Shlokas, and Kshama Prarthana.
   - Boundary: No per-item duration timers.
4. **Split Progress Indicators**:
   - **Today's Progress** and **Overall Progress** are separated visually.
   - Action: Displays completed Shloka units.
5. **Native Ad Card**:
   - Integrated cleanly between Today's Progress and Overall Progress, matching the design system's spacing and border outlines.
6. **Support Banner**:
   - Located near the bottom of the scroll view.
   - Displays a respectful invitation for devotees to support the platform.

---

## 4. Design Decisions

- **Simplicity Over Engagement**: The interface is intentionally flat and simple. It minimizes dashboard creep so that the devotee's attention is immediately drawn to the active chanting items.
- **Greeting Choice**: The salutation "Shree Maatre Namaha" establishes a devotional atmosphere from the moment the app opens.
- **No Gamification Guilt**: By displaying today's accomplished work rather than demanding "goals" or showing broken streak counters, we prevent users from feeling guilt or technological fatigue.
- **Respectful Support Entry**: The support option is integrated organically into the scrolling stream rather than appearing as an overlay banner or push alert.
