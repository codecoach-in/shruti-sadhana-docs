# Information Architecture & Navigation Specs

Purpose: Canonical reference for user experience, navigation hierarchies, screen routing paths, and design status.

---

## 1. Design & Component Specifications Index

For detailed layouts, tokens, guidelines, and widgets, refer to the dedicated specification documents:
- **Visual Design System**: [DESIGN_SYSTEM.md](design/DESIGN_SYSTEM.md) (Philosophy, color palettes, typography, spacing, and motion guidelines).
- **Home Screen Specification**: [home.md](screens/SS-003-home/home.md) (Greeting, Continue Learning hero card, and Today's Practice widgets).
- **Learning Session Screen Specification**: [learning-session.md](screens/SS-008-learning-session/learning-session.md) (Manuscript container, audio player, recording limits, and Samarpan triggers).
- **Illustration Guide**: [ILLUSTRATION_GUIDE.md](design/ILLUSTRATION_GUIDE.md) (Hand-painted miniatures style, colors, and core asset inventory).
- **UI Component Library**: [UI_COMPONENT_LIBRARY.md](design/UI_COMPONENT_LIBRARY.md) (Reusable Flutter widget properties, states, and design parameters).
- **Devotional Chanting Flow**: [SAMARPAN_FLOW.md](../01-product/SAMARPAN_FLOW.md) (The core Listen → Chant → Record → Samarpan loop, and support configurations).


---

## 2. Navigation Hierarchy (Routing Tree)

The following ASCII tree maps the screen relationship and primary navigation paths:

```text
SS-001 Splash Screen (Simplified copy, establishments)
    ↓
SS-002 Choose Your Shloka Script
    ↓
SS-002A Let's Begin (Invocation Screen)
    ↓
SS-002B Vakratunda Demo Learning (Uses Learning Session UI)
    ↓
[ Google Sign-In Authentication ]
    ↓
SS-003A Home Screen (First Time User layout)
    ↓  [ Start Journey ]
SS-005A Learning Plan Setup (Wizard pace selection)
    ↓
SS-003 Home Screen (Returning User layout)
 ├── SS-004 Learning Library (Explore courses)
 │      ↓
 │  SS-005 Course Details (Overview & sections index)
 │      ├── SS-006 Meet Your Guide (Teacher details/philosophy)
 │      └── SS-007 All Shlokas Index (Navigation & flexibility focus)
 │                ↓
 │         SS-008 Learning Session (Signature chanting screen)
 │                ↓
 │         SS-009 Samarpan Screen (Lotus offering animation)
 │                ↓
 │         SS-010 Support Shruti Sadhana (Periodic request screen)
 │
 ├── SS-011 My Shruti Sadhana (Devotee statistics & completed stotras list)
 └── SS-012 Settings (App preferences & reminder configurations)
```

---

## 3. Chanting Entry Flow Paths

To minimize friction and encourage regular practice, devotees can enter the active chanting screen via two routes:

### 3.1 Resume Flow (Direct Entry)
Bypasses indices to launch practice immediately.
```text
SS-003 Home Screen
    ↓  [Continue Learning Hero Card Tap]
SS-008 Learning Session
```

### 3.2 Browse Flow (Catalog Entry)
Used for exploring courses and navigation between stotras.
```text
SS-003 Home Screen
    ↓
SS-004 Learning Library
    ↓
SS-005 Course Details
    ↓
SS-007 All Shlokas Index
    ↓
SS-008 Learning Session
```

---

## 4. Script Selection Behavior (SS-002)

- **Timing**: Choose Your Shloka Script selection (`SS-002`) occurs during onboarding before entering the guided Invocation (`SS-002A`) and Vakratunda Demo (`SS-002B`), ensuring the stotra verses are immediately rendered in the devotee's preferred layout script.
- **Preferences**: Sanskrit script choice (Devanagari vs. English Transliteration).
- **State Flow**:
  1. Selected values are stored **locally** in device preferences immediately.
  2. Synced with the devotee's backend profile on the server immediately after authentication completes.
  3. Modifiable anytime post-onboarding from Settings (`SS-012`) or the Manuscript gear bottom sheets (`SS-BS-001`).

---

## 5. Screen Boundaries & Responsibilities

- **Course Details (`SS-005`)**: Responsible for presenting course introductory content, spiritual context, course section indices, and primary learning triggers. It does not house active audio playback or chanting recording panels.
- **Meet Your Guide (`SS-006`)**: Sub-page of Course Details. Houses teacher bio, teaching philosophy, trust indicators, and a message from the guide. It excludes social follower counts and founder designations.
- **Learning Session (`SS-008`)**: Immersive manuscript container managing audio synchronizations and chanting recordings. It does not display overall course statistics or course-wide catalog lists.

---

## 6. Content Hierarchy vs. Navigation Hierarchy

We maintain a strict separation of concerns between database content structures and visual navigation layouts:

- **Navigation Hierarchy (UI Routes)**:
  `SS-003 Home → SS-004 Library → SS-005 Details → SS-007 All Shlokas Index → SS-008 Learning Session`
- **Content Hierarchy (Data Relations)**:
  `Course (e.g., Lalita Sahasranama) → Section (e.g., Nyasa) → Shloka (Single Verse unit)`

---

## 7. Design Maturity & Status

- **Information Architecture**: ✅ Frozen for Pilot Release (v1.0).
- **Screen Hierarchy**: ✅ Frozen (`SS-001` through `SS-012`).
- **Navigation Flow**: ✅ Frozen (as defined in Section 2.0).
- **UI Mockups**: Mapped layout mockups (v1.0) are located under `03-ux/mockups/` for reference.
- **Visual Design**: ✅ Design Frozen. Warm ivory backgrounds, antique gold accents, and muted maroon primary buttons constitute the visual palette. Hand-painted spiritual illustrations are preferred over generic Material icons.