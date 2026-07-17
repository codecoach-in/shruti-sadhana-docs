# System Architecture & Flutter Specifications

**Status:** ✅ Frozen for Pilot Release

This document defines the system-wide architecture and coding specifications for the Shruti Sadhana frontend. These decisions are frozen to ensure a stable implementation foundation and optimize delivery within the pilot timeline.

---

## Frozen Decisions Summary

| Architectural Area | Decision | Reference Spec |
| :--- | :--- | :--- |
| **Frontend Framework** | Flutter (Mobile & Web) | ADR-001 / ADR-002 |
| **Project Structure** | Feature-First Project Layout | Section 2.1 |
| **Folder Pattern** | Clean Architecture (Presentation, Domain, Data) | Section 2.2 |
| **State Management** | Riverpod | Section 3.0 |
| **Navigation** | GoRouter (Named Routing) | Section 4.0 |
| **Dependency Injection** | Riverpod Providers (No GetIt/Injectable) | Section 5.0 |
| **Networking Client** | Dio (REST APIs, Repository Pattern) | Section 6.0 |
| **Local Storage** | Preferences, tokens, and resume states only | Section 7.0 |
| **Configuration** | Environment config via `--dart-define` | Section 8.0 |
| **Error Handling** | Centralized application wrapper & Logger / Crashlytics | Section 9.0 |
| **Audio & Sync** | Decoupled Playback/Sync/Recording engines with Stream outputs | Section 13.0 |


---

## 1. System Components

1. **Frontend Flutter Client (Mobile & Web)**: Serves as the primary native client on Android and a responsive web client on Flutter Web for iOS/iPad users.
2. **Audio Sync Engine**: Client-side playback engine driving real-time audio synchronization with highlighted visual shloka text.
3. **Application Backend (NestJS)**: Serves course configurations, transcripts, and validates playback requests (Signed URLs).
4. **Content Database (MySQL)**: Holds structural stotram courses, shlokas, and user learning states.

---

## 2. Codebase Organization

We follow a **Feature-First** structure combined with a **lightweight Clean Architecture** layer within features. Naming conventions, routing, and shared UI boundaries must adhere strictly to these conventions.

### 2.1 Top-Level Directory Layout

All source code resides inside the `lib/` directory:

```text
lib/
├── app/
├── core/
├── shared/
├── features/
└── main.dart
```

| Directory | Responsibility | Components Included |
| :--- | :--- | :--- |
| **app/** | Application bootstrapping | Main MaterialApp, global themes, GoRouter setup, global app configurations. |
| **core/** | Low-level system utilities & infrastructure | API clients, network intercepts, local database wrappers, global utility services, error interceptors. |
| **shared/** | Reusable UI assets and presentation code | Common widgets (custom buttons, load indicators), dialogue boxes, extension helpers, color schemes. |
| **features/** | Isolated business domains | Subfolders representing distinct functional areas (e.g., library, learning, progress). |

### 2.2 Feature Module Conventions

Inside `lib/features/`, every feature module (such as `home`, `library`, `learning`, `progress`, `settings`, `onboarding`, `auth`) is structured into presentation, domain, and data layers:

```text
lib/features/<feature_name>/
├── presentation/
│   ├── screens/
│   ├── widgets/
│   └── providers/
├── domain/
│   ├── entities/
│   └── repository_interfaces/
└── data/
    ├── models/
    ├── data_sources/
    └── repository_implementations/
```

- **presentation/**: Hosts screens, widgets layout, and Riverpod providers managing view-state.
- **domain/**: Contains pure business objects (entities) and abstract definitions for interfaces. This layer is completely independent of external dependencies (e.g., API structures).
- **data/**: Implements the abstract domain interfaces, handles REST requests (data sources), and parses JSON payload into model schemas.
- *Constraint*: Empty folders must not be created. Only initialize directories (e.g., `data/`) when actual files are ready to occupy them.

---

## 3. State Management & Dependency Injection

- **Framework**: **Riverpod** is the frozen state management library.
- **Providers**: State is managed via feature-scoped providers using the `@riverpod` code generation syntax.
- **Business Logic**: Encapsulated within `Notifier` or `AsyncNotifier` classes to update state in response to user actions.
- **Dependency Injection**: Riverpod providers act as the primary DI mechanism. Avoid introducing external DI libraries (such as `GetIt` or `Injectable`) unless explicitly required by a verified implementation blocker.
- **Rule**: Avoid global mutable state. Keep providers scoped to the lifecycle of their matching screens.

---

## 4. Navigation & Routing

- **Framework**: **GoRouter** is the frozen navigation package.
- **Configuration**: Managed centrally within `lib/app/router/` containing named routes.
- **Guards**: Keep route guards minimal (e.g., redirecting unauthenticated users to SS-002 Onboarding).
- **Named Routes**: Always navigate using names rather than path strings to ease routing refactoring.

### Pilot Route Mapping

| ID | Route Name | Path |
| :--- | :--- | :--- |
| **SS-001** | `splash` | `/` |
| **SS-002** | `shloka_script_selection`| `/choose-script` |
| **SS-002A**| `invocation` | `/let-begin` |
| **SS-002B**| `vakratunda_demo` | `/demo-learning` |
| **SS-003** | `home_returning` | `/home` |
| **SS-003A**| `home_first_time` | `/home-first-time` |
| **SS-004** | `library` | `/library` |
| **SS-005** | `course_details` | `/course/:courseId` |
| **SS-005A**| `learning_plan_setup` | `/course/:courseId/plan-setup` |
| **SS-006** | `meet_your_guide` | `/course/:courseId/guide` |
| **SS-007** | `all_shlokas_index` | `/course/:courseId/shlokas` |
| **SS-008** | `learning_session` | `/learning/:shlokaId` |
| **SS-009** | `samarpan` | `/learning/:shlokaId/samarpan` |
| **SS-010** | `support_shruti_sadhana`| `/support` |
| **SS-011** | `my_shruti_sadhana` | `/progress` |
| **SS-012** | `settings` | `/settings` |


---

## 5. Networking Flow & Data Flow

We follow the Repository Pattern. All network requests must flow as follows:

```text
Screen (UI) 
   ↓
Provider (State Management)
   ↓
Repository Interface (Domain Layer)
   ↓
Repository Implementation (Data Layer)
   ↓
Remote Data Source (Network client)
   ↓
API (NestJS Backend)
```

- **Networking Client**: Built on **Dio** for REST client capabilities.
- **DTO Mapping**: JSON parsing and mapping must occur strictly inside the Data layer (e.g., inside the repository implementation using mapping extension methods).
- **Rule**: The UI must never consume raw JSON or model classes directly. It must only interact with clean Domain Entities.

---

## 6. Local Storage Boundaries

- **Allowed Scope**: Local storage must only be used for user preferences and lightweight application states.
- **Examples of stored fields**:
  - Authentication JWT access and refresh tokens.
  - Display script preference (Devanagari vs. English Transliteration).
  - Language selection.
  - Custom UI preferences (font scale, dark mode).
  - Resume state (last opened course ID, current shloka ID).
  - Onboarding milestone completions.
- **No Heavy Caching**: Do not implement extensive local audio caching, offline sync, or database replication protocols. Media assets must remain strictly streamed online.

---

## 7. Shared Component Boundaries

To prevent dependency creep and maintain a clean separation of concerns:
- **`core/`**: Restricted to infrastructural utilities (e.g., HTTP clients, storage keys, date format helpers). It must contain no UI widgets or business logic.
- **`shared/`**: Houses reusable visual components (e.g., app bars, custom buttons, layout grids) which are completely stateless.
- **`features/`**: All business logic, feature states, course rules, and screen layouts must remain isolated within features. Features must never import code directly from another feature (inter-feature interaction must happen via shared contract interfaces or shared providers).

---

## 8. Environment Configuration

- **Environments**: Supported environments are **Local**, **Staging**, and **Production**.
- **Execution**: Configured at build time via compiler flags:
  `--dart-define` or `--dart-define-from-file`
- **Rule**: Never commit sensitive API credentials, environment URLs, or signing keys directly to the repository codebase.

---

## 9. Logging & Error Handling

- **Logging**: Console logging during development uses the `logger` package to prevent exposing sensitive runtime parameters in production releases.
- **Crash Reports**: Integration of **Firebase Crashlytics** for remote crash logging and reporting.
- **Error UI**: Implement a centralized handler that catches failures and presents user-friendly alert views. Keep detailed diagnostic messages inside developer logs only.

---

## 10. Naming Conventions

- **File Names**: `snake_case` (e.g., `learning_session_screen.dart`, `shloka_repository.dart`).
- **Class Names**: `PascalCase` (e.g., `LearningSessionScreen`, `ShlokaRepository`).
- **Variable & Method Names**: `camelCase` (e.g., `currentShlokaId`, `fetchCourseList()`).
- **Suffix Rules**:
  - Screen views: Suffix with `Screen` (e.g., `SettingsScreen`).
  - Repository interfaces: Suffix with `Repository` (e.g., `CourseRepository`).
  - Data transfer objects: Suffix with `Model` (e.g., `CourseModel`).
  - Riverpod state handlers: Suffix with `Provider` or follow `@riverpod` generation class names.

---

## 11. Testing Strategy

- **Pilot Scope**: Exclude heavy integration suites or complex test framework dependencies.
- **Priority**: Focus testing on critical business logic widgets and critical path integration handlers (e.g., correct parsing of script preferences and auth tokens initialization).

---

## 12. Architectural Constraints (Excluded Features)

The following components are **intentionally excluded** from the Pilot Release architecture:
- Microservices, event bus setups, event sourcing frameworks, or event-driven CQRS.
- Complex runtime plug-in loaders.
- Offline-first database replication and local encryption storage.
- Runtime feature flags services.
- Over-engineered generic base repository layers.

---

## 13. Audio & Synchronization Architecture

The audio capabilities represent the core learning loop of Shruti Sadhana. We define a clean separation of concerns at the architectural boundary, separating audio execution from UI rendering and timing conversion.

### 13.1 Audio Modules & Responsibilities

The audio architecture is composed of four decoupled functional modules:

- **Playback Engine**: Responsible for playing the lesson audio files from remote streams, managing playback state (play, pause, stop, seek), and emitting real-time playback updates.
- **Synchronization Engine**: Listens to the current playback position and maps it to the active text segment (Section, Line, or Shloka/Word) using course-specific synchronization metadata.
- **Recording Engine**: Interacts with the device microphone to record the user's chanting, saves the recording locally, and provides playback controls for self-evaluation.
- **Session Controller**: The coordinator for the active `Learning Session` (SS-008). It orchestrates state between the Playback, Sync, and Recording engines, and triggers progress updates.

### 13.2 Audio Synchronization Model

The Synchronization Engine abstracts timing mapping away from UI components.

| Topic | Decision |
| :--- | :--- |
| **Purpose** | Provide synchronized highlighting and navigation during guided chanting. |
| **Source of Truth** | Server-provided synchronization metadata packaged with each lesson. |
| **UI Responsibility** | Render synchronized highlighting and update playback position. |
| **Audio Engine Responsibility** | Play audio and emit playback position updates. |
| **Sync Engine Responsibility** | Convert playback position into the active section/line/word based on available metadata. |
| **Extensibility** | Designed to support lesson-, line-, and word-level synchronization. Pilot uses the synchronization granularity required by the prepared content. |
| **Independence** | UI should consume synchronization events rather than implementing timing logic. |

### 13.3 High-Level Data Flow

```text
Learning Session (SS-008)
        ↓
Audio Controller
        ↓
Playback Position Stream
        ↓
Synchronization Engine
        ↓
Current Highlight State
        ↓
UI Rendering
```

### 13.4 Deferred Audio Details
The following implementation-level items are deferred:
- Specific timestamp file formats and JSON schemas.
- Word-timing structures and line interpolation algorithms.
- Playback drift correction and audio buffering strategies.
- Future AI pronunciation alignment.

---

## 14. Master Screen & Component Inventory

These stable identifiers serve as the canonical references for coding, routing, design, and testing.

### 14.1 Primary Screen Inventory

| ID | Screen | Status | Parent / Trigger | Primary Purpose / Notes |
| :--- | :--- | :--- | :--- | :--- |
| **SS-001** | Splash | ✅ Design Frozen | — | Established brand/emotion, simplified layout |
| **SS-002** | Shloka Script Selection | ✅ Design Frozen | Splash | Allows the user to choose how Sanskrit shlokas will be displayed throughout the app. Supported scripts: Devanagari, English Transliteration. Extensible architecture. |
| **SS-002A** | Invocation (Let's Begin) | ✅ Design Frozen | SS-002 | Transition / mental prep illustration card before first demo |
| **SS-002B** | Vakratunda Demo Learning | ✅ Uses Session UI | SS-002A | Initial guided experience before mandatory login commitment |
| **SS-003** | Home (Returning User) | ✅ Design Frozen | Auth / Home | Continue learning hero, Today's Learning, Native Ad, separated stats |
| **SS-003A** | Home (First Time User) | ✅ Design Frozen | Auth / Setup | Initial clean dashboard screen, Start Journey button |
| **SS-004** | Learning Library | ✅ Design Frozen | Home | Browse courses (Lalita Sahasranama pilot) |
| **SS-005** | Course Details | ✅ Design Frozen | Library | Overview, sections checklist list |
| **SS-005A** | Learning Plan Setup | ✅ Design Frozen | SS-003A | Wizard selecting learning pace (Slow & Steady vs. Balanced) |
| **SS-006** | Meet Your Guide | ✅ Design Frozen | Course Details | Teacher details, bio, philosophy, trust indicators (no metrics) |
| **SS-007** | All Shlokas Index | ✅ Design Frozen | Course Details | Browse and navigate all Shlokas for flexibility |
| **SS-008** | Learning Session | ✅ Design Frozen | Index / Home | Core learning experience for a single shloka, including guided audio playback, chanting practice, and self-recording for review |
| **SS-009** | Samarpan | ✅ Design Frozen | SS-008 | A devotional completion screen allowing the learner to mentally offer the fruits of their chanting before returning to the learning journey |
| **SS-010** | Support Shruti Sadhana | ✅ Design Frozen | Samarpan / Home | Invites devotees to voluntarily support the continued development and preservation of Shruti Sadhana (periodic trigger) |
| **SS-011** | My Shruti Sadhana | ✅ Design Frozen | Home | Progress metrics, spiritual illustrations of Havan Kund, Mala, Agarbatti, Manuscript |
| **SS-012** | Settings | ✅ Design Frozen | Home | Manage reminders, learning plan, script, help & feedback |

### 14.2 Secondary / Utility Screens

| ID | Screen / Element | Trigger / Opened From | Notes |
| :--- | :--- | :--- | :--- |
| **SS-BS-001** | Script Selection Bottom Sheet | Learning Session (SS-008) | Select Devanagari vs. English Transliteration |
| **SS-BS-002** | Font Size Bottom Sheet | Learning Session (SS-008) | Adjust stotra text display scale |
| **SS-BS-003** | Recording Options Bottom Sheet | Learning Session (SS-008) | Delete, rename, or save chanting recordings |
| **SS-104** | Native Ad Card | Reusable layout card | Placed on Home Dashboard (SS-003) |
| **SS-105** | Support Dialog | Future enhancement | Respectful donation trigger (non-blocking) |
| **SS-106** | Error / Offline State | Reusable visual template | Shown on network failure or offline states |
| **SS-107** | Empty State | Reusable visual template | Shown on empty Library, empty Progress log |

### 14.3 Dialogs / Bottom Sheets

| ID | Component Name | Description / Placement |
| :--- | :--- | :--- |
| **SS-BS-001** | Script Selection | Bottom sheet to pick active Sanskrit script preference |
| **SS-BS-002** | Font Size | Bottom sheet to adjust typography text scale |
| **SS-BS-003** | Recording Options | Bottom sheet containing options to manage user recordings |

### 14.4 Reusable UI Components

| ID | Component Name | Purpose / Placement |
| :--- | :--- | :--- |
| **SS-C-001** | Sacred Hero Header | Greeting bar displays devotional salutation |
| **SS-C-002** | Continue Learning Card | Dashboard card loading the user's last shloka |
| **SS-C-003** | Sacred Stat Card | Simple visual counters for completed chanting duration |
| **SS-C-004** | Manuscript Card | Traditional border card holding stotra text |
| **SS-C-005** | Audio Player | Basic normal-speed track streaming bar |
| **SS-C-006** | Recording Card | Chanting mic controls and local file list item |
| **SS-C-007** | Samarpan Button | Primary maroon action button initiating offering |
| **SS-C-008** | Progress Tile | Individual course section checklist item |
| **SS-C-009** | Native Ad Card | Reusable Google Native Ad container card |
| **SS-C-010** | Support Card | Contribution call-to-action layout |
| **SS-C-011** | Section Header | Thin-bordered headers containing conch details |
| **SS-C-012** | Bottom Navigation | App navigation bar (Home, Library, Progress, Settings) |

