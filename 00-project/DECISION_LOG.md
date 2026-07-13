# Decision Log

Purpose: Simple log of accepted product and architecture decisions.

This file records stable decisions that guide documentation and implementation work, pointing to detailed Architectural Decision Records (ADRs) for context, alternatives, and consequences.

## Accepted Decisions

| Date | Area | Decision | Status | Notes |
|---|---|---|---|---|
| 2026-07-10 | Product | Shruti Sadhana is a Sanskrit Stotram Learning Platform, not a generic Sanskrit learning app. | Accepted | The focus is guided stotram learning, chanting, practice, parayana, and devotional experience. |
| 2026-07-10 | Platform | Android is the first release platform. | Accepted | See [ADR-001](../08-decisions/ADR-001-android-first.md) (Native iOS is postponed). |
| 2026-07-10 | Platform | Flutter Web is the preferred MVP experience for iPhone users. | Accepted | See [ADR-002](../08-decisions/ADR-002-flutter-web.md) (Supports web access for Apple users in Kusum Guruji's batch). |
| 2026-07-10 | Platform | Offline downloads are out of scope. | Accepted | See [ADR-004](../08-decisions/ADR-004-no-offline-downloads.md) (Streaming only to protect media). |
| 2026-07-10 | Content | Content is curated rather than user-generated. | Accepted | See [ADR-006](../08-decisions/ADR-006-curated-content.md) (Ensures authentic pronunciation). |
| 2026-07-10 | Media | Gold Standard recordings are premium intellectual property. | Accepted | See [ADR-003](../08-decisions/ADR-003-secure-media.md) (Requires secure playback controls). |
| 2026-07-10 | Media | Backend authorizes playback but does not proxy media streams. | Accepted | See [ADR-003](../08-decisions/ADR-003-secure-media.md) (Media served via secure CDN/GCS). |
| 2026-07-10 | Media | Audio mastering is performed manually before upload, not by the backend. | Accepted | Backend starts after master audio and packaging inputs exist. |
| 2026-07-10 | Console | Separate Admin Console. | Accepted | See [ADR-005](../08-decisions/ADR-005-separate-admin-console.md) (Segregates administration from devotee client). |
| 2026-07-10 | Platform | Environment Abstraction. | Accepted | See [ADR-007](../08-decisions/ADR-007-environment-abstraction.md) (Identical interfaces for local mock and production cloud). |
| 2026-07-10 | Documentation | Repository navigation uses PROJECT_INDEX.md, folder-level INDEX.md, and AGENT_GUIDE.md. | Accepted | Avoids unnecessary documentation frameworks. |
| 2026-07-13 | Product | Pilot focus on Kusum Guruji's batch | Accepted | Validate Shruti Sadhana as a companion app for ~50 students in Guruji's Lalita Sahasranama class. |
| 2026-07-13 | Content | Lalita Sahasranama only content scope | Accepted | Content limited to Lalita Sahasranama (audio, lyrics, structured sections, shlokas). Other courses deferred. |
| 2026-07-13 | Content | Devanagari and English Transliteration scripts only | Accepted | Other Indic scripts deferred post-pilot to reduce preparation and QA overhead. |
| 2026-07-13 | Auth | Onboarding flow with Vakratunda Mahakaya guided experience | Accepted | Flow: Splash → Script Preference → Guided Vakratunda Mahakaya → Google Sign-In. No guest mode post-onboarding. Mobile OTP deferred. |
| 2026-07-13 | Practice | Chanting Recording & Playback | Accepted | Users can record their chanting, play it back, and delete/re-record it for practice. |
| 2026-07-13 | Product | Deferral of all premium payments and monetization features | Accepted | Subscriptions, donations, referrals, and multi-speed playback are deferred. |
| 2026-07-13 | Feedback | Simple feedback collection | Accepted | Focus on WhatsApp group and simple in-app feedback entry point. No sophisticated system. |
| 2026-07-13 | Product | Copy convention: Reserve "Sadhana" for product name | Accepted | Avoid "Sadhana" as a generic UI term. Use "Learning/Chanting/Practice" instead. |
| 2026-07-13 | Product | Repeat usage: Simple continue learning mechanisms | Accepted | No streaks or gamification; uses Continue Learning, Recently Played, Resume Session. |
| 2026-07-13 | Product | Deferral of Puja Ghar feature | Accepted | Deity selection and personalized spaces are deferred under the P0 decision-making rule (Guruji can conduct the batch and devotees can practice without it). |
| 2026-07-13 | IA | IA-001: Information Architecture Frozen | Accepted | High-level pilot screen layout and routing are frozen. No further changes without implementation blockers. |
| 2026-07-13 | IA | IA-002: Stable Screen Identifiers | Accepted | Screen inventory mapped to permanent non-hierarchical identifiers (`SCR-001` through `SCR-013`). |
| 2026-07-13 | IA | IA-006: Local Script Selection Preference | Accepted | Script selection (Devanagari vs. English Transliteration) occurs before auth, stored locally and synchronized later. |
| 2026-07-13 | IA | IA-009: Content Hierarchy Structure | Accepted | Strict Course → Section → Shloka organization. Learning Session teaches exactly one Shloka at a time. |
| 2026-07-13 | Architecture | Flutter Application Architecture Frozen | Accepted | Lightweight clean, feature-first structure (`lib/app`, `core`, `shared`, `features`). Suffix rules for names. See [system-overview.md](../05-architecture/system-overview.md). |
| 2026-07-13 | State / DI | Riverpod State Management & DI | Accepted | Riverpod is the sole framework for view states, logic notifications, and DI. No GetIt or Injectable. |
| 2026-07-13 | Routing | GoRouter Navigation | Accepted | Centrally configured route map utilizing named routes with minimal guards. |
| 2026-07-13 | Networking | Dio Client & Repository Mapping | Accepted | Dio REST client. DTO json serialization strictly in data layer; UI consumes pure Domain Entities. |
| 2026-07-13 | Storage | Local Storage Scope | Accepted | Restricted strictly to user preferences, auth tokens, script choices, and resume state. No audio caching. |
| 2026-07-13 | Config | Environment configuration via `--dart-define` | Accepted | Local, Staging, and Production compilation configurations set via compiler flags. |
| 2026-07-13 | Audio | Audio & Synchronization Architecture | Accepted | Decoupled Playback Engine, Synchronization Engine, Recording Engine, and coordinating Session Controller. UI consumes highlight events. Timing drift/formats/caching are deferred. |






## Future Decision Work

New architecture decisions should follow the ADR format specified in [README.md](../08-decisions/README.md) and be added to the Decisions folder.


