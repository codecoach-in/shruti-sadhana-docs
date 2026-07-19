# Shruti Sadhana — Implementation Briefing

Produced after reading all documentation in the prescribed order.  
Dated: 2026-07-17

---

## 1. Project Overview

**Shruti Sadhana** is a **Sanskrit Stotram Learning Platform** — not a generic Sanskrit language app or a meditation app. Its core philosophy is *Learn → Practice → Sadhana*: helping devotees learn sacred stotrams with correct pronunciation, practice through guided repetition, and develop a consistent devotional habit.

The **Phase 0 Pilot** serves ~50 learners from Kusum Guruji's Lalita Sahasranama batch. The pilot validates Shruti Sadhana as a practice companion app — not to validate market demand. The primary delivery platform is **Android native** (Flutter), with **Flutter Web** serving iPhone/iPad users.

The pilot is **not** a throwaway MVP. Every line of implementation must be production-quality. Scope is reduced by *excluding* features, not by creating temporary or substandard implementations.

**Core Pilot Capabilities:**
- Google Sign-In authentication via an onboarding flow (Splash → Script Selection → Invocation → Vakratunda Demo → Google Sign-In)
- Browse Lalita Sahasranama course (sections and shlokas)
- Lyrics display in Devanagari and English Transliteration
- Normal-speed secure audio streaming (HLS via CDN)
- Chanting recording, playback, and delete/re-record (device-local)
- Learning progress tracking (shloka as canonical unit)
- Settings (script preference)
- Simple in-app feedback and WhatsApp group link

**All monetization, AI features, Puja Ghar, streaks, multiple courses, iOS native, and additional scripts are explicitly deferred.**

---

## 2. Architecture Summary

### Frontend — Flutter

- One shared Flutter codebase for Android and Flutter Web
- **Feature-first, lightweight Clean Architecture**: `lib/app/`, `core/`, `shared/`, `features/`
- **Riverpod** is the sole state management and dependency injection mechanism (no GetIt, no Injectable)
- **GoRouter** for navigation with named routes and minimal guards
- **Dio** as the HTTP client; DTO serialization strictly in the data layer; UI consumes domain entities only
- Platform-specific capabilities (recording, secure storage) isolated behind infrastructure-level adapters
- Local state: preferences, auth tokens, script choice, resume identifiers only — no audio caching
- Environment configuration via `--dart-define` compiler flags

### Backend — NestJS

- **Modular monolith**: one deployable application with explicit module boundaries
- **TypeScript strict mode** on Node.js LTS
- **MySQL** via **Prisma** (Prisma stays in infrastructure; models never leave the infrastructure boundary)
- **Firebase Admin SDK** for server-side ID-token verification
- `class-validator` + `class-transformer` for DTO validation at API boundaries
- **NestJS Config** for typed, validated environment configuration
- **Jest** for unit, integration, and contract tests

### Authentication

- Firebase Authentication with Google Sign-In issues the Firebase ID token
- Flutter sends `Authorization: Bearer <Firebase ID token>` on every protected request
- NestJS verifies the token server-side and maps the Firebase subject (`uid`) to a Shruti Sadhana `User` row
- Firebase handles identity; NestJS owns all authorization decisions
- No additional application JWT is issued in v1

### Storage and Media

- **Google Cloud Storage (GCS)**: private source HLS media, packaged encrypted HLS segments, timing metadata
- **Cloud CDN**: delivers authorized encrypted HLS segments directly to the Flutter player — the backend never proxies audio bytes
- **Playback flow**: Flutter → NestJS (authorization) → GCS/CDN signing adapter → short-lived signed/rewritten HLS manifest → Flutter player → Cloud CDN (direct HLS segment retrieval)
- Local chanting recordings stay on device; no upload or backend API surface for recordings in the pilot

### Database

- **MySQL** with Prisma ORM
- Internal PKs: unsigned `BIGINT AUTO_INCREMENT` (serialized as decimal strings in JSON)
- Stable external identifiers: `Course.slug` for public routes, Firebase UID for identity
- 12 entities (see Module Map below)

---

## 3. Module Map

### NestJS Backend Modules

| Module | Owns | Does Not Own |
|---|---|---|
| **Authentication** | Firebase ID-token verification, auth guard applied at controller boundary, session identity | User profile data, authorization policy beyond identity |
| **Users** | Application-user identity mapping (`firebaseUid` → `User`), profile data, `GET /me` bootstrap response | Firebase credential issuance, course content |
| **Courses** | Published course catalogue, course details, sections, shlokas, guru data, publication state | Media delivery, canonical user progress, admin UI |
| **Progress** | Shloka-level learning progress, learning-plan pace, resume target | Presentation metrics, media playback |
| **Playback Authorization** | Eligibility check, short-lived signed HLS manifest generation | Streaming, proxying, media packaging, CDN cache |
| **Settings** | Server-authoritative user preferences after sign-in | Pre-auth device-only preferences |
| **Feedback** | Authenticated feedback submission and persistence | Messaging, community, notifications |
| **Content Management** | Backend API surface for the separate Admin Console (content metadata, publication state) | Admin Console UI, devotee client, practice recordings |
| **Health** | Liveness/readiness checks | Product analytics, user-facing status |
| **Configuration** | Typed config validation, environment driver selection | Feature-specific policy decisions |
| **Shared** | Cross-cutting primitives (error codes, request correlation, result types, logging) | Domain ownership of any business capability |

### Flutter Feature Modules

| Module | Responsibility |
|---|---|
| **Authentication** | Guided-demo handoff, Google sign-in, session state, sign-out, expiry handling |
| **Home** | Dashboard composition, Continue Learning, today's learning view |
| **Course** | Library, course details, guide content, learning-plan setup, section and shloka navigation |
| **Learning Session** | Focused lesson experience, manuscript display, Samarpan trigger, coordination of audio/recording contracts |
| **Audio** | Authorized playback session, player state, manifest handoff, synchronization events |
| **Recording** | Local chant recording lifecycle, playback, deletion |
| **Progress** | Progress views, milestone presentation, progress-update interactions |
| **Settings** | Script preference, learning-plan changes, feedback entry point |
| **Support** | Support banner and voluntary contribution presentation (no payment logic in pilot) |
| **Shared** | Design-system widgets, theme, error presentation, cross-cutting types |

---

## 4. Feature Map

### Authentication and Onboarding

| Layer | Implementation |
|---|---|
| **Flutter** | Onboarding screens SS-001 → SS-002 → SS-002A → SS-002B → Google Sign-In; Authentication feature module; GoRouter guards |
| **API** | `GET /api/v1/me` — resolves User + UserSettings after Firebase sign-in |
| **Database** | `User` table (maps `firebaseUid`), `UserSettings` table (created on first bootstrap) |

### Home Dashboard

| Layer | Implementation |
|---|---|
| **Flutter** | SS-003 (first-time and returning user layouts); Home feature module; consumes progress data |
| **API** | `GET /api/v1/me/progress` (course-level summary); `GET /api/v1/courses` (catalogue) |
| **Database** | `UserCourseProgress`, `UserShlokaProgress`, `Course` |

### Course Library

| Layer | Implementation |
|---|---|
| **Flutter** | SS-004 Learning Library; Course feature module; paginated course list |
| **API** | `GET /api/v1/courses?page=1&pageSize=20` |
| **Database** | `Course`, `Guru`, `UserCourseProgress` (progress counts) |

### Course Details and Shloka Index

| Layer | Implementation |
|---|---|
| **Flutter** | SS-005 Course Details, SS-006 Meet Your Guide, SS-007 All Shlokas Index |
| **API** | `GET /courses/{courseSlug}`, `GET /courses/{courseSlug}/sections`, `GET /sections/{sectionId}`, `GET /sections/{sectionId}/shlokas` |
| **Database** | `Course`, `Guru`, `Section`, `Shloka`, `UserShlokaProgress` |

### Learning Session (Core Experience)

| Layer | Implementation |
|---|---|
| **Flutter** | SS-008 Learning Session; Learning Session + Audio + Recording feature modules; Samarpan trigger |
| **API** | `GET /shlokas/{shlokaId}?scripts=DEVANAGARI,ENGLISH_TRANSLITERATION`, `POST /playback-sessions` |
| **Database** | `Shloka`, `ShlokaText`, `ShlokaAudio`, `LearningResource`, `UserShlokaProgress` |
| **Media** | Signed/rewritten HLS manifest → Flutter player → Cloud CDN direct streaming |

### Samarpan and Completion Flow

| Layer | Implementation |
|---|---|
| **Flutter** | SS-009 Samarpan Screen (lotus bloom animation); SS-010 Support Screen (periodic) |
| **API** | `PATCH /api/v1/me/progress/shlokas/{shlokaId}` (marks COMPLETED, increments practiceCount) |
| **Database** | `UserShlokaProgress` (status, completedAt, practiceCount) |

### Progress Tracking

| Layer | Implementation |
|---|---|
| **Flutter** | SS-011 My Shruti Sadhana (Journey So Far, Milestones, Learning Plan, Overall Progress) |
| **API** | `GET /me/progress`, `PATCH /me/progress/courses/{courseSlug}`, `PATCH /me/progress/shlokas/{shlokaId}` |
| **Database** | `UserCourseProgress` (pace, resume target), `UserShlokaProgress` (shloka-level state) |

### Settings

| Layer | Implementation |
|---|---|
| **Flutter** | SS-012 Settings; SS-BS-001 (Manuscript gear bottom sheet); SS-BS-002 |
| **API** | `GET /me/settings`, `PATCH /me/settings` |
| **Database** | `UserSettings` (scriptPreference, fontScale) |

### Feedback

| Layer | Implementation |
|---|---|
| **Flutter** | Settings feedback entry point |
| **API** | `POST /me/feedback` |
| **Database** | `UserFeedback` |

### Chanting Recording

| Layer | Implementation |
|---|---|
| **Flutter** | Recording feature module; device-local file lifecycle; SS-008 recording controls |
| **API** | None — recordings are device-local in the pilot |
| **Database** | None — no recording table exists |

---

## 5. Vertical Slice Roadmap

The 7 slices are sequential with defined dependencies. Each slice is complete only when Flutter surface, backend contract, persistence path, and tests are all delivered.

| Slice | Name | Flutter Screens | Key API Endpoints | NestJS Modules | Depends On |
|---|---|---|---|---|---|
| **1** | User Bootstrap | App bootstrap, post-auth transition | `GET /me` | Users, Authentication guard | Firebase project configuration |
| **2** | Course Library | SS-004, Home library entry | `GET /courses` | Courses, Progress read contract | Slice 1 |
| **3** | Course Details & Shloka Index | SS-005, SS-006, SS-007 | `GET /courses/{slug}`, `GET /courses/{slug}/sections`, `GET /sections/{id}` | Courses | Slice 2 |
| **4** | Learning Session & Playback Auth | SS-008 | `GET /shlokas/{id}`, `POST /playback-sessions` | Courses, Playback Authorization | Slice 3 + media signing adapter |
| **5** | Progress & Resume | SS-003, SS-008, SS-011 | `GET /me/progress`, `PATCH /me/progress/shlokas/{id}`, `PATCH /me/progress/courses/{slug}` | Progress | Slices 1 and 3 |
| **6** | Settings | SS-012, SS-BS-001, SS-BS-002 | `GET /me/settings`, `PATCH /me/settings` | Settings | Slice 1 |
| **7** | Feedback | Settings feedback entry | `POST /me/feedback` | Feedback | Slice 1 |

> Slice 4 and Slice 5 may be implemented in either order after Slice 3.

### 5.1 Detailed Vertical Slice Delivery Plan

This section is the handoff plan for Antigravity. Vertical slicing here means complete product increments across Flutter, NestJS, MySQL, Firebase, media/config adapters, tests, and demo readiness. It is not an API roadmap.

A slice is considered done only when the user-visible workflow, client state, backend contract, database persistence, infrastructure adapter, error handling, seed data, and verification steps work together in one environment.

#### Slice Anatomy

Every slice should be planned and delivered across these tracks.

| Track | Responsibility |
|---|---|
| User journey | The exact pilot workflow and screen-to-screen path the learner can complete. |
| Flutter app | Screens, navigation, Riverpod state, repositories, services, platform adapters, responsive layouts, loading states, and error states. |
| Backend app | NestJS controllers, services, guards, policies, repositories/read ports, DTO validation, and standard errors. |
| Data and content | Prisma models, migrations, seed data, relational constraints, ordering, and server-authoritative state. |
| Infrastructure | Firebase configuration, media signing/local drivers, environment configuration, logging hooks, and deployment/runtime assumptions. |
| Verification | Unit, widget, integration, contract, and manual demo evidence needed to prove the slice works end to end. |

#### Working Rules for Every Slice

| Rule | Instruction |
|---|---|
| Slice ownership | Implement one slice at a time and keep pull requests aligned to slice boundaries where possible. |
| User-flow first | Start from the learner/admin-visible workflow, then connect Flutter, backend, data, and infrastructure work needed to make that workflow real. |
| Contract discipline | Confirm endpoint request/response DTOs against `05-architecture/api-contracts.md` before wiring Flutter repositories to live backend calls. |
| Server authority | Treat NestJS and MySQL as canonical for authenticated user, progress, settings, and content state. Flutter may cache or hold temporary state only. |
| Seed before UI | Make the minimum seed data available before building screens that depend on it. Do not hardcode production content in Flutter. |
| Demo before expansion | Each slice must produce a working demo path before the next slice expands the surface area. |
| No deferred features | Do not add monetization, AI, Puja Ghar, native iOS, offline downloads, multiple courses, or extra scripts unless later approved. |
| Pilot quality | Keep error handling, logging hooks, loading states, empty states, and tests in scope even when the feature is small. |

#### Shared Setup Before Slice 1

These tasks are not a product slice, but they prevent rework across all slices.

| Area | Required setup |
|---|---|
| Repository structure | Create the Flutter and NestJS project structure according to the approved architecture documents. |
| Environment config | Define local, development, staging, and production configuration keys without committing secrets. |
| Firebase | Configure Firebase project identifiers for Android, Flutter Web, and NestJS Admin SDK verification. |
| Database | Create the first Prisma migration from `05-architecture/database-design.md`, including the custom SQL check constraint for `LearningResource`. |
| Seed data | Seed Kusum Guruji, the Lalita Sahasranama course, pilot sections, sample shlokas, script text, and a development user/admin path. |
| Backend foundation | Add API version prefix, standard response envelope, standard error envelope, request validation, auth guard shell, and request correlation logging. |
| Flutter foundation | Add routing shell, Riverpod provider structure, API client, environment config, theme, responsive layout primitives, common loading/error states, and Android/Web platform adapter shells. |
| Test foundation | Add backend unit/integration/contract test setup and Flutter unit/widget/integration test setup. |

#### Slice 1 - User Bootstrap

**Goal:** After Google sign-in, the app can establish the authenticated Shruti Sadhana user and load server-authoritative settings.

| Track | Work items |
|---|---|
| User journey | A fresh or returning user opens the app, completes the approved auth handoff, and enters the authenticated app shell. |
| Flutter | Implement app startup state, onboarding-to-auth handoff, Firebase Google sign-in, authenticated API client token attachment, `GET /me` call, session restore, sign-out path. |
| Backend | Implement Firebase ID-token verification, authenticated request context, `GET /api/v1/me`, first-user bootstrap, `User` lookup/create, `UserSettings` lookup/create, standard 401/403 responses. |
| Database | Use `User` and `UserSettings`; ensure `firebaseUid` is unique and settings are created once per user. |
| Infrastructure | Configure Firebase client apps and backend Admin SDK for local/development environments. |
| Tests | Backend auth guard tests, `GET /me` success and invalid-token contract tests, Flutter auth state/provider tests, startup navigation tests. |
| Demo checkpoint | A fresh pilot user signs in and lands in the authenticated app with canonical user/settings loaded from the backend. |
| Do not include | Course browsing, progress, playback, feedback, or local recording. |

**Completion checklist:**
- Invalid or missing Firebase token returns the standard 401 envelope.
- Backend does not issue or persist an application JWT.
- Flutter does not treat local profile data as authoritative after sign-in.
- Session restore works without showing the wrong screen flash.

#### Slice 2 - Course Library

**Goal:** Authenticated users can see the published course catalogue and enter the Lalita Sahasranama course.

| Track | Work items |
|---|---|
| User journey | A signed-in user reaches the Library from Home and sees available curated courses. |
| Flutter | Implement Learning Library screen (SS-004), Home library entry, course cards, loading/empty/error states, pagination-ready client structure. |
| Backend | Implement `GET /api/v1/courses` with pagination, published-only filtering, canonical ordering, guru summary, and caller-specific progress summary when available. |
| Database | Read `Course`, `Guru`, `UserCourseProgress`, and `UserShlokaProgress`; seed the pilot course as published. |
| Infrastructure | Ensure environment seed commands can create repeatable local/staging catalogue data. |
| Tests | Contract tests for pagination and published filtering, repository tests for progress counts, Flutter widget tests for library states. |
| Demo checkpoint | A signed-in user opens the Library and sees the Lalita Sahasranama course with correct guide and progress summary. |
| Do not include | Section details, shloka text, audio authorization, or progress mutation. |

**Completion checklist:**
- Draft/unpublished courses never appear in devotee-facing responses.
- API returns pagination metadata even if only one course exists in the pilot.
- Flutter does not assume only one course in layout or client models.
- Empty catalogue state is graceful for development environments.

#### Slice 3 - Course Details and Shloka Index

**Goal:** Users can open a course, understand its structure, meet the guide, and choose a shloka from the canonical index.

| Track | Work items |
|---|---|
| User journey | A user opens a course, reads its overview, meets the guide, browses sections, and selects a shloka. |
| Flutter | Implement Course Details (SS-005), Meet Your Guide (SS-006), All Shlokas Index (SS-007), section navigation, shloka-row progress indicators, responsive layouts. |
| Backend | Implement `GET /api/v1/courses/{courseSlug}`, `GET /api/v1/courses/{courseSlug}/sections`, `GET /api/v1/sections/{sectionId}`, and `GET /api/v1/sections/{sectionId}/shlokas`. |
| Database | Read `Course`, `Guru`, `Section`, `Shloka`, and caller progress summaries; use `Section.sequence` and `Shloka.number` for ordering. |
| Infrastructure | Keep all content environment-specific through database seed/publication state, not Flutter constants. |
| Tests | Contract tests for not-found/unpublished access, ordering tests, Flutter navigation/widget tests for details and index screens. |
| Demo checkpoint | A user enters the course, views the guide, sees ordered sections and shlokas, and selects a shloka for learning. |
| Do not include | Full shloka manuscript payload, playback sessions, completion updates, or recording. |

**Completion checklist:**
- Course route uses `courseSlug`; internal BIGINT IDs remain JSON strings where exposed.
- Shlokas are ordered by canonical `number`, not by a redundant sequence field.
- The index can show progress status without owning progress mutation logic.
- Unknown, draft, or unpublished content uses standard error responses.

#### Slice 4 - Learning Session and Playback Authorization

**Goal:** Users can open a single shloka learning session, view manuscript text, and start authorized normal-speed audio playback.

**Current status:** This slice is blocked for final delivery until the Audio Synchronization, Media Architecture, and Security Architecture details are completed. Antigravity may build a local driver and UI shell earlier, but production playback must wait for those documents.

| Track | Work items |
|---|---|
| User journey | A user opens one shloka, reads the manuscript, starts authorized audio, and remains in a focused learning session. |
| Flutter | Implement Learning Session screen (SS-008), manuscript display, script preference application, audio player abstraction integration, playback loading/error states, graceful web behavior. |
| Backend | Implement `GET /api/v1/shlokas/{shlokaId}` for published shloka detail and `POST /api/v1/playback-sessions` for transient playback authorization. |
| Media | Use signed/rewritten HLS manifest handoff only; player streams directly from Cloud CDN. Backend must not proxy or stream media bytes. |
| Database | Read `Shloka`, `ShlokaText`, `ShlokaAudio`, `LearningResource`, and current `UserShlokaProgress`. |
| Infrastructure | Provide local/staging media driver configuration, manifest expiry settings, playback logging hooks, and Android/Web adapter behavior. |
| Tests | Contract tests for shloka detail and playback authorization, authorization denial tests, Flutter player-state tests, integration smoke test with local/stub media driver. |
| Demo checkpoint | A user opens a shloka, sees Devanagari/transliteration text, requests normal-speed playback, and the player starts from an authorized manifest. |
| Do not include | Offline downloads, persisted playback sessions, media byte proxying, user recording upload, or AI feedback. |

**Completion checklist:**
- API never exposes private GCS object paths, signing keys, or long-lived URLs.
- Playback session ID is transient and not backed by a database table.
- Manifest URL expiry and retry behavior are visible to Flutter as recoverable playback errors.
- Android-only player caching, if required, remains bounded and only supports smooth streaming; it is not an offline download feature.
- Flutter Web degrades gracefully where browser recording/playback limitations apply.

#### Slice 5 - Progress and Resume

**Goal:** Users can complete shlokas, see progress, and resume the correct learning position from Home and Progress screens.

| Track | Work items |
|---|---|
| User journey | A user completes a shloka, offers it through Samarpan, returns Home, and sees progress/resume updated. |
| Flutter | Implement Home returning-user progress view (SS-003), My Shruti Sadhana (SS-011), Samarpan completion handoff, Continue Learning, and progress-refresh behavior. |
| Backend | Implement `GET /api/v1/me/progress`, `PATCH /api/v1/me/progress/shlokas/{shlokaId}`, and `PATCH /api/v1/me/progress/courses/{courseSlug}` with transaction-safe updates. |
| Database | Write `UserCourseProgress` and `UserShlokaProgress`; keep daily target and resume state course-specific. |
| Infrastructure | Add progress mutation logging/correlation so exhibition issues can be traced without exposing private data. |
| Tests | Transaction tests for shloka completion plus resume updates, idempotency tests for repeated completion, Flutter provider tests for refresh after mutation. |
| Demo checkpoint | A user marks a shloka complete through Samarpan, returns Home, and sees updated course progress and resume target. |
| Do not include | Streaks, social sharing, certificates, gamified leaderboards, adaptive planning, or multi-course analytics. |

**Completion checklist:**
- Server-calculated progress replaces client-local progress after every mutation.
- Completion updates are scoped to the authenticated user only.
- Shloka progress and course resume updates commit atomically where both change.
- Repeating a completion request does not inflate progress incorrectly.

#### Slice 6 - Settings

**Goal:** Users can view and update supported preferences, with server values prevailing after sign-in.

| Track | Work items |
|---|---|
| User journey | A user opens Settings or manuscript controls, changes supported preferences, and sees them reflected in the learning experience. |
| Flutter | Implement Settings screen (SS-012), manuscript gear bottom sheet (SS-BS-001), script chooser (SS-BS-002), local pre-auth defaults, post-auth synchronization. |
| Backend | Implement `GET /api/v1/me/settings` and `PATCH /api/v1/me/settings` with validation, enum handling, and standard error envelopes. |
| Database | Read/write `UserSettings`; keep script preference and font scale server-authoritative after authentication. |
| Infrastructure | Ensure defaults are environment-independent and do not require remote config for the pilot. |
| Tests | Validation tests for accepted/rejected values, settings persistence tests, Flutter widget/provider tests for preference changes. |
| Demo checkpoint | A user changes manuscript preference, sees the learning session reflect it, restarts the app, and receives the server value. |
| Do not include | Extra scripts in the pilot UI, notification preferences, account deletion, payment settings, or admin settings. |

**Completion checklist:**
- Pilot UI exposes only approved pilot script choices unless later changed.
- Non-pilot enum values are handled according to the final validation decision.
- Local unauthenticated preferences are replaced by server settings after sign-in.
- Settings update failures do not leave the UI permanently out of sync.

#### Slice 7 - Feedback

**Goal:** Users can submit simple in-app feedback from Settings, and the backend records it for review.

| Track | Work items |
|---|---|
| User journey | A signed-in user opens feedback from Settings, submits a message, and receives confirmation. |
| Flutter | Implement feedback entry point, form validation, submit/loading/success/error states, and return path to Settings. |
| Backend | Implement `POST /api/v1/me/feedback`, validation, authenticated ownership, default status assignment, rate limiting, and standard errors. |
| Database | Write `UserFeedback` with authenticated `userId` and server-assigned status. |
| Infrastructure | Configure rate-limit behavior and request logging consistently across environments. |
| Tests | Contract tests for success/validation/rate limit paths, repository tests, Flutter widget tests for form states. |
| Demo checkpoint | A signed-in user submits feedback and receives a clear success response without leaving the app in a blocked state. |
| Do not include | Admin feedback triage UI, email notifications, support ticket workflow, or public community features. |

**Completion checklist:**
- Feedback cannot be submitted anonymously in the pilot app.
- Backend ignores any client-supplied ownership/status fields.
- Rate limiting is present or explicitly stubbed for local development.
- Validation errors are readable and non-destructive to the form.

#### Exhibition Build Path

For an exhibition demo, prioritize a stable end-to-end devotional learning path over breadth. The recommended demo path is:

1. Complete shared setup and Slice 1.
2. Complete Slice 2 and Slice 3 using seeded Lalita Sahasranama content.
3. Complete Slice 5 enough to show completion, progress refresh, and resume behavior.
4. Complete Slice 6 for script preference if it is needed in the demo narrative.
5. Complete Slice 4 only with an approved local/staging media driver until Media and Security Architecture are finalized.
6. Add Slice 7 if there is time after the learning loop is stable.

The minimum credible exhibition loop is: sign in, open course, choose shloka, view manuscript, play approved audio or local demo media, complete via Samarpan, return to Home, and see progress updated.

#### Per-Slice Handoff Template for Antigravity

Use this template when starting each slice.

| Field | Required detail |
|---|---|
| Slice objective | One user-visible outcome. |
| User journey | Exact screens/routes included in the slice. |
| Flutter delivery | Screens, state ownership, navigation, repositories, services, adapters, responsive behavior, and visible states. |
| API contracts | Endpoint names, DTOs, success responses, and standard errors. |
| Backend modules | Controllers, services, repositories, guards, adapters, and database tables touched. |
| Infrastructure delivery | Firebase, media/config drivers, environment variables, logging hooks, and deployment assumptions touched. |
| Seed data | Minimum records required for local demo and automated tests. |
| Acceptance criteria | Observable behavior that proves the slice is complete. |
| Test evidence | Backend tests, Flutter tests, and one manual demo path. |
| Out of scope | Features explicitly not allowed in this slice. |

---

## 6. Risks and Documentation Gaps

The following items represent potential implementation friction. None require redesign — they are open specification details or items requiring a decision before the first code is written.

### R1 — Audio Synchronization Spec is Incomplete

**File:** `05-architecture/audio-synchronization.md`  
**Status:** 🟡 Placeholder — marked "To be completed during Technical Design"  
**Impact:** The Learning Session and Audio feature modules depend on timing metadata format, synchronization event contracts, and the highlight mapping logic. Without this, audio-text synchronization cannot be implemented.  
**Action needed:** Complete the audio synchronization technical design document before Slice 4 implementation.

### R2 — Media Architecture and Security Architecture Documents Are Missing

**Impact:** The Playback Authorization module, signed URL lifecycle, HLS packaging pipeline, manifest format, and CDN cache-control behaviour are referenced repeatedly across system overview, NestJS, and API contracts, but the dedicated Media Architecture and Security Architecture documents are not present in the repository.  
**Action needed:** These documents must be authored (or a decision made to incorporate their content inline) before Slice 4 can be delivered.

### R3 — Admin Console Architecture is Undefined

**Impact:** The backend Content Management module is described as serving the "separate internal Admin Console," but the Admin Console's API surface, authentication mechanism, and deployment model are completely unspecified.  
**Risk level:** Low for pilot launch (the Admin Console can use a manual Prisma seed + direct DB access initially), but blocks the content publication workflow that keeps devotee-facing content up to date.  
**Action needed:** Clarify the minimum Admin Console capability needed before pilot launch.

### R4 — LearningResource CHECK Constraint Requires a Custom SQL Migration

**File:** `database-design.md` §6  
**Detail:** MySQL/Prisma cannot express the "exactly one owner (courseId, sectionId, or shlokaId) is non-null" constraint in Prisma schema DSL. It must be delivered as a raw SQL migration and covered by an integration test.  
**Action needed:** Author the raw SQL migration with the CHECK constraint alongside the first Prisma migration.

### R5 — Enum Vocabulary Not Fully Approved for Implementation

**File:** `database-design.md` §11, Recommendation 1  
**Detail:** The database design itself notes that the controlled enum vocabulary (scripts, resource types) should be approved before the first Prisma migration. The Script enum includes IAST, TELUGU, TAMIL, KANNADA — scripts that are deferred post-pilot. These should be present in the DB schema but their inclusion in API response enums should be handled carefully.  
**Action needed:** Confirm the approved initial enum vocabulary before the first migration.

### R6 — Support Screen (SS-010) Trigger Parameter is Remote-Configurable but No Config Endpoint Exists

**File:** `SAMARPAN_FLOW.md` §4  
**Detail:** The Support Screen trigger frequency `N` (shlokas completed) is described as "remote configurable" and "fetched at application launch" — but no endpoint for this configuration appears in the API contracts.  
**Impact:** Minor for pilot (can use a hardcoded default), but the architecture implies a config endpoint that isn't defined.  
**Action needed:** Decide whether to hardcode `N` for the pilot or add a small configuration endpoint.

### R7 — Pagination in Progress Endpoint Unspecified

**File:** `api-contracts.md` §7.5  
**Detail:** `GET /me/progress` returns `CourseProgress[]`. No pagination is specified. For the pilot (one course), this is fine, but the absence of pagination metadata here contrasts with the paginated courses endpoint.  
**Impact:** Negligible for pilot. Worth noting for future multi-course growth.

### R8 — Non-Pilot Scripts Accepted by PATCH `/me/settings`

**File:** `api-contracts.md` §7.1  
**Detail:** `PATCH /me/settings` lists `IAST | ENGLISH_TRANSLITERATION | TELUGU | TAMIL | KANNADA` as acceptable `scriptPreference` values. Only DEVANAGARI and ENGLISH_TRANSLITERATION are in pilot scope. The API contract exposes the full enum.  
**Action needed:** Confirm whether the backend should accept all enum values and store them (forward-compatible) or reject non-pilot scripts with a validation error.

### R9 — `PlaybackSession.id` is Transient but Present in Response DTO

**File:** `api-contracts.md` §5 and §13  
**Detail:** The response DTO includes an `id` field, but the repository ownership guidelines confirm PlaybackSessions are not persisted. The `id` must be a synthetic UUID generated in memory.  
**Action needed:** Confirm the `id` in `PlaybackSession` response is a transient UUID not backed by any DB table.

---

## 7. Implementation Readiness

### Assessment

**READY FOR IMPLEMENTATION — Slice 2 and beyond**

**Slice 1 (User Bootstrap) is COMPLETE.**

Slices 2, 3, 5, 6, and 7 can be implemented immediately and completely from the existing documentation. The API contracts, database design, module boundaries, dependency rules, authentication flow, and DTO ownership are fully specified for these slices.

**Slice 4 (Learning Session and Playback Authorization) is BLOCKED** pending:
- R1: Audio synchronization technical design
- R2: Media architecture and security architecture documents

**Slice 5 (Progress and Resume)** can be implemented after Slice 3, in parallel with resolving the Slice 4 blockers.

### Cleared Items Summary

| Concern | Status |
|---|---|
| Product vision and pilot scope | ✅ Clear |
| Technology stack (Flutter, NestJS, MySQL, Firebase, GCS, CDN) | ✅ Clear |
| NestJS module boundaries and dependency rules | ✅ Clear |
| Flutter feature modules, layers, dependency rules | ✅ Clear |
| Authentication flow end-to-end | ✅ Clear |
| Database entities, relationships, constraints, indexes | ✅ Clear |
| API endpoints, DTOs, error model, versioning | ✅ Clear |
| Vertical slice sequence and dependencies | ✅ Clear |
| Repository and DTO ownership per module | ✅ Clear |
| Transaction guidance | ✅ Clear |
| Pagination strategy | ✅ Clear |
| Environment abstraction (local / staging / production) | ✅ Clear |
| UX screen hierarchy and navigation tree (SS-001 to SS-012) | ✅ Frozen |
| Visual design palette (warm ivory, antique gold, muted maroon) | ✅ Frozen |
| Samarpan flow and completion loop | ✅ Clear |
| Chanting recording (device-local, no backend surface) | ✅ Clear |
| Audio synchronization technical design | ❌ Incomplete (R1) |
| Media Architecture document | ❌ Missing (R2) |
| Security Architecture document | ❌ Missing (R2) |
| Admin Console API surface | ❌ Undefined (R3) |

---

## Appendix: Key Domain Terms

| Term | Meaning |
|---|---|
| **Shloka** | Single Sanskrit verse; atomic learning unit; the canonical unit of progress |
| **Section** | Ordered organisational container within a Course (e.g., Nyasa, Dhyanam) |
| **Course** | A learning programme (e.g., Lalita Sahasranama Chanting Course) |
| **Learning Session** | Screen SS-008; teaches exactly one Shloka at a time |
| **Samarpan** | Devotional submission trigger at the end of a Learning Session; marks Shloka as COMPLETED |
| **Learning Plan** | Pace selection: Slow & Steady (3 shlokas/day) or Balanced (7 shlokas/day) |
| **Enrollment** | `UserCourseProgress` — links a user to a course with pace and resume state |
| **Recording** | Device-local audio of user's chanting; no backend surface in pilot |
| **Guru** | Curated guide assigned to a course (Kusum Guruji for pilot) |
| **HLS** | HTTP Live Streaming; encrypted segments delivered via Cloud CDN |
| **PlaybackSession** | Transient authorization response containing short-lived signed manifest URL |
| **firebaseUid** | Stable Firebase subject used to map identity to application User row |
