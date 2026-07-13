# System Overview

## Architecture Diagram
(To be added)

## Components
1. **Frontend Landing Page & Web Client**: Interactive UI built for responsive web and mobile access.
2. **Audio Sync Engine**: Drives the audio playback aligned with visual highlights of text.
3. **Content Engine / CMS**: Structure for Sanskrit verses, transliterations, translations, and audio metadata.

---

## Master Screen Inventory

The following table maps the finalized Information Architecture screen inventory for the Pilot Release. These `SCR-xxx` identifiers serve as the canonical references for coding, routing, design, and testing.

| ID | Screen | Parent Screen | Primary Purpose | Pilot |
| :--- | :--- | :--- | :--- | :--- |
| **SCR-001** | Splash | — | Initialize application, load local preferences, check auth state | ✅ |
| **SCR-002** | Script Selection | Splash | Select Sanskrit display script preference (Devanagari vs. English Transliteration) | ✅ |
| **SCR-003** | Vakratunda Experience | Script Selection | Guided introductory chanting experience demonstrating platform functionality | ✅ |
| **SCR-004** | Google Sign-In | Vakratunda Experience | Google authentication commitment point (guest mode disabled after experience) | ✅ |
| **SCR-005** | Home | Google Sign-In | Main dashboard containing Sadhana Progress resume widget and main entry points | ✅ |
| **SCR-006** | Library | Home | Browse and search available courses (Lalita Sahasranama pilot course) | ✅ |
| **SCR-007** | Course Details | Library | View course significance, trainer summary, structure, and user progress | ✅ |
| **SCR-008** | Lesson / Shloka Index | Course Details | List sections and individual shlokas within the course structure | ✅ |
| **SCR-009** | Learning Session | Lesson/Shloka Index OR Home (Sadhana Progress Widget) | Active learning interface teaching exactly one Shloka at a time | ✅ |
| **SCR-010** | Lesson Complete | Learning Session | Completion screen showing session success and progress tracking indicators | ✅ |
| **SCR-011** | Progress | Home | View detailed learning stats and tracked historical progress | ✅ |
| **SCR-012** | Profile & Settings | Home | Manage account details, preferences synchronization, and display script | ✅ |
| **SCR-013** | Trainer Profile | Course Details | Detailed biography and background of the teacher (child screen of Course Details) | ✅ |

