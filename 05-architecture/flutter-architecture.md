# Flutter Architecture v1

**Status:** Proposed for Architecture Freeze v1
**Version:** 1.0
**Scope:** Phase 0 Pilot Flutter clients (Android and responsive Web)

## 1. Purpose

This document defines the internal architecture of the Shruti Sadhana Flutter application. It establishes clear feature and layer boundaries so the Android-first client and responsive Flutter Web client can share a maintainable codebase without expanding the frozen pilot scope.

It defines responsibilities and dependency rules, not widgets, packages, endpoint schemas, database schemas, or media-processing implementation.

## 2. Architecture Principles

| Principle | Decision and rationale |
| --- | --- |
| Feature-oriented organisation | Code is grouped by user-facing capability so a vertical feature can be developed, tested, and understood in one place. |
| Lightweight layered design | Presentation, domain, data, and infrastructure responsibilities remain distinct without introducing unnecessary abstractions. |
| Explicit dependencies | Dependencies flow inward toward stable business concepts. This keeps API, storage, player, and platform changes isolated. |
| Server authority | The backend is authoritative for authenticated profile, content, learning-plan, and progress data. The client never treats cached state as canonical. |
| Platform-aware, product-consistent | Android may use permitted streaming cache and native recording capabilities; responsive Web remains a first-class client without depending on Android-only behaviour. |
| Secure streaming by design | The app consumes authorized HLS manifests and direct CDN streams. It does not manage credentials for storage or attempt to download lessons. |
| Convention over framework proliferation | Use a small, consistent set of patterns throughout the application. New architectural layers or global frameworks require a demonstrated need. |
| Testable seams | Platform and external integrations are behind narrow contracts so their behaviour can be tested locally without cloud access. |

## Platform Strategy

Android is the primary native Flutter application for the pilot. There will be no native iOS application in the pilot or foreseeable roadmap. iPhone, iPad, macOS, Windows, and Linux users access Shruti Sadhana through the responsive Flutter Web client in a modern browser.

The architecture maintains one shared Flutter codebase for Android and Flutter Web. Business logic, repositories, state management, navigation, and feature modules remain platform-independent. Platform-specific capabilities—including recording, secure storage, permissions, and device lifecycle integration—are isolated behind infrastructure-level platform adapters so that no feature depends directly on Android or browser APIs.

Every feature must provide an equivalent Flutter Web experience or degrade gracefully where browser limitations prevent identical behaviour. Responsive layouts are mandatory across the application; no screen may assume a fixed mobile viewport or depend on Android-only interaction patterns for essential user flows.

## 3. Application Layers

| Layer | Responsibility | Examples of contents | Must not contain |
| --- | --- | --- | --- |
| Presentation | Render screens, react to user input, and expose view state. | Screens, feature widgets, view-state controllers/providers, route entry points. | API calls, raw JSON, persistence details, or media timing rules. |
| Domain | Express stable application concepts and use cases independent of Flutter or external services. | Entities, value objects, repository contracts, feature actions and rules. | Framework widgets, DTOs, HTTP, Firebase, files, or SQL concerns. |
| Data | Obtain and transform application data through repository implementations. | API models, mappers, remote/local data sources, repository implementations. | UI state, route decisions, or player rendering. |
| Infrastructure | Provide reusable adapters to platform and external capabilities. | HTTP client, Firebase adapter, secure key-value storage, audio-player adapter, recorder adapter, connectivity and configuration adapters. | Feature-specific business rules or screen state. |
| Shared | Hold deliberately reusable, stateless presentation and cross-cutting primitives. | Design-system widgets, theme, common error presentation, formatting, application-wide types. | Feature workflows, feature repositories, or an unbounded utilities collection. |

`app/` composes these layers: startup, application shell, routing, theme, global error boundary, and environment bootstrap. It does not become a home for feature logic.

## 4. Feature Modules

Each feature owns its presentation state, domain contracts, and data implementation when needed. A feature must not import another feature’s internal presentation or data code. Shared contracts are promoted only when two or more features genuinely need them.

| Module | Responsibility | Boundary |
| --- | --- | --- |
| Authentication | Guided-demo handoff, Google sign-in, authenticated session state, sign-out, and session-expiry response. | Does not own profile, course, or access-policy rules beyond representing the authenticated user state returned by the backend. |
| Home | Returning and first-time dashboard composition, resume entry, and today’s learning view. | Consumes course and progress data; does not calculate canonical progress. |
| Course | Library, course details, guide content, plan setup, section and shloka navigation. | Does not own active playback, recording, or global session identity. |
| Learning Session | The focused lesson experience, manuscript display state, completion interaction, and coordination of audio/recording feature contracts. | Does not implement playback engines, recording persistence, or synchronization algorithms. |
| Audio | Authorized playback session, player state, manifest handoff, and synchronization event exposure. | Does not render lesson UI, determine user access, or store permanent media. |
| Recording | Local chant recording lifecycle, playback of a local recording, and deletion/retention actions. | Records stay local in the pilot; the module has no upload, sharing, or evaluation path. |
| Progress | Progress views, milestone presentation, and progress-update interactions. | The backend owns canonical progress calculation and persistence. |
| Settings | User-editable preferences, learning-plan changes, and feedback/help entry points. | Does not directly mutate unrelated feature state or store protected media. |
| Support | Frozen support and voluntary-contribution presentation paths. | It contains no payment or donation processing in the pilot. |
| Shared | Reusable presentation primitives and cross-cutting contracts used across modules. | It must not become a substitute for feature ownership. |

Audio and Recording are separate logical modules because their platform lifecycles and failure modes differ. They may be composed by Learning Session without being coupled to its UI.

## 5. Dependency Rules

### 5.1 Allowed dependencies

```text
Presentation ───────> Domain
      │                  ▲
      │                  │
      └──────────────> Data
                            │
Data ─────────────────> Infrastructure
App composition ──────> Presentation, Data, Infrastructure
Shared presentation <── Presentation and App composition
```

The diagram denotes compile-time dependency direction. At runtime, a presentation controller calls a domain repository contract; composition supplies its data implementation.

| From | May depend on | Rule |
| --- | --- | --- |
| Presentation | Domain, its own presentation code, shared presentation primitives | It may consume repository contracts through state controllers, but never directly handle HTTP, storage, or raw DTOs. |
| Domain | Domain only | Keep it independent of Flutter, Firebase, HTTP, file system, and player packages. |
| Data | Domain, Infrastructure | It implements domain contracts and maps external representations to domain entities. |
| Infrastructure | Infrastructure and narrow shared technical primitives | It adapts a specific platform or vendor service; it must not import feature code. |
| Shared | Stable shared primitives only | It cannot depend on an individual feature. |
| App composition | All layers as required for bootstrap and wiring | It creates dependencies; it does not add feature business logic. |

Features communicate through routes, domain contracts, or explicit shared application state. Direct feature-to-feature imports are prohibited.

## 6. Navigation

Navigation remains the frozen UX hierarchy. The app router owns route definitions, route parameters, redirects, and deep-link parsing; features own the screen reached by a route.

| Concern | Architecture decision |
| --- | --- |
| Startup flow | Bootstrap environment and local preferences, restore Firebase identity when available, then resolve the user to the frozen onboarding, demo, sign-in, or home flow. |
| Authentication flow | The guided demo is available before the commitment point. After that point, protected routes require an authenticated application session. Token expiry or invalidation returns the user to the sign-in commitment screen while preserving no protected route state. |
| Main navigation | Home, Library, Progress, and Settings use the frozen navigation hierarchy. Course and learning-session routes receive stable identifiers rather than in-memory objects. |
| Deep links | Only authenticated, stable resource routes may be deep linked, such as a course or shloka. The app validates the session and access before resolving the target; otherwise it redirects through sign-in and returns to the requested route only after success. |
| Session restoration | The client may restore an authenticated Firebase session, local display preferences, and a last-known resume target. It must revalidate all protected server data and media access after restoration. |

Route guards remain minimal: they check application readiness and authentication, not feature-specific business logic.

## 7. State Management

**Recommendation:** Riverpod with generated, feature-scoped providers.

Riverpod supplies dependency injection and reactive state without a separate global service locator. Its explicit provider graph makes ownership discoverable, keeps state testable, and supports both Flutter platforms with the same approach.

| State category | Owner | Persistence |
| --- | --- | --- |
| Transient UI state | The nearest screen or feature-scoped provider. | None; discard when the route/feature lifecycle ends. |
| Feature workflow state | Feature controller/provider. | Persist only if the frozen product requires resumption. |
| Session state | Authentication feature and app bootstrap. | Firebase-managed identity plus minimal secure local session support where required. |
| Server state | Feature repository provider. | Backend is authoritative; any local copy is an optimization, not a source of truth. |
| Device preferences | Settings and local-preference adapter. | Persist locally and reconcile with the backend after sign-in where the product requires it. |
| Playback and recording state | Audio and Recording feature controllers. | In-memory for active operations; local metadata only where required for a recording lifecycle. |

Avoid global mutable state. A provider’s lifetime must match the state it owns; active player and recorder resources are explicitly released when their lifecycle ends.

## 8. Repository Pattern

Repositories present domain-oriented data operations to features and hide data-source selection, DTO mapping, and cache policy.

| Component | Responsibility |
| --- | --- |
| Repository contract | Defines domain-facing read/write operations and failure outcomes. |
| Repository implementation | Coordinates remote and local sources, converts models to domain entities, and enforces the feature’s cache policy. |
| Remote data source | Calls the NestJS API using the authenticated client and handles transport-level response parsing. |
| Local data source | Reads/writes only permitted preferences, resume metadata, temporary metadata, and local recording references. |
| Mapper | Converts API or local representations to/from domain entities at the data boundary. |

Repositories must never expose raw JSON, HTTP response objects, Firebase SDK types, or database-shaped models to presentation code. They do not access Google Cloud Storage or Cloud CDN directly; protected media access is initiated through the backend playback-authorization contract.

## 9. Services

Services are narrow infrastructure adapters. Feature modules depend on service contracts, not a vendor SDK or platform package directly.

| Service | Responsibility | Lifetime |
| --- | --- | --- |
| Authentication service | Perform Firebase sign-in/sign-out, observe identity, and provide verified identity tokens to the authenticated network client. | Application/session scoped. |
| Audio service | Own the player adapter, play/pause/seek lifecycle, authorized-manifest handoff, and playback event stream. | One active playback session; application-level coordination prevents competing players. |
| Recording service | Request microphone capability, create/play/delete local recordings, and expose recording lifecycle events. | One active recording session; resources are released on completion, cancellation, or app interruption. |
| Connectivity service | Expose current network reachability for user guidance and retry decisions. | Application scoped. |
| Configuration service | Read validated, non-secret runtime/build configuration and select environment drivers. | Immutable after bootstrap. |
| Analytics abstraction | Provide optional, privacy-conscious event recording behind a no-op local/test implementation. | Application scoped; no advanced analytics product capability is implied by the abstraction. |

## 10. Local Storage

| Data class | Local handling | Authority |
| --- | --- | --- |
| Display and accessibility preferences | Persist locally; synchronize after sign-in only where product behaviour requires it. | Backend profile after authentication; local preference before sign-in. |
| Onboarding/demo milestones | Persist only as needed to resume the frozen onboarding flow. | Client for pre-auth flow; server when associated with an authenticated profile. |
| Resume target | Store lightweight identifiers, never full cached lessons. | Backend course/progress state. |
| Authentication material | Use platform-secure mechanisms where supported; do not store secrets in plain preferences. | Firebase and backend validation. |
| Recording metadata and files | Store only on the device under the Recording module’s retention policy. | Device-local for the pilot. |
| Active player and network state | Keep in memory. | Temporary; discard on lifecycle end. |

The client does not persist canonical course content, progress, media decryption keys, signed manifests, or a local replica of server data.

## 11. Audio Integration

Audio is integrated through a player abstraction supplied by the Audio service. Learning Session consumes playback state and synchronization events through feature contracts rather than controlling a platform player directly.

```text
Learning Session presentation
          │
          v
Audio feature controller ──> Playback repository/authorization contract
          │                               │
          v                               v
Player abstraction <── authorized manifest from NestJS API
          │
          v
Playback state and position events ──> synchronization hook ──> presentation
```

The playback lifecycle is: request authorization, receive an authorized manifest, prepare the player, expose state and position events, release resources when the lesson or app lifecycle ends, and surface recoverable failures to the feature controller. The client passes no storage credentials and does not retrieve media from GCS directly.

Recording is coordinated, not merged, with playback. The Learning Session decides when the frozen experience permits recording; the Recording service owns microphone and file lifecycle. The future synchronization hook receives playback position and immutable lesson timing metadata, but contains no timing algorithm in this document.

## 12. Caching

Caching is permitted only to improve responsiveness and short-lived playback resilience. It must never become an offline-media feature.

| May be cached | Boundary |
| --- | --- |
| User preferences and light resume identifiers | Small, non-sensitive local state governed by Local Storage. |
| Transient API responses and immutable content metadata | Bounded and invalidated by repository policy; revalidate before treating it as current. |
| Android player-managed HLS segments | Only already-authorized near-term stream data; encrypted where platform support is available, size/retention bounded, and automatically evicted. |
| In-memory playback buffers | Only for the active authorized player session. |

| Must never be cached as persistent content | Reason |
| --- | --- |
| User-visible downloadable lessons or persistent media library | Explicitly excluded by ADR-004. |
| Decryption keys, signed manifests, or reusable signed URLs | They are sensitive, short-lived authorization material. |
| Full protected audio files or reconstructed HLS streams | Violates the secure-streaming and no-offline-downloads boundary. |
| A complete offline replica of course/progress data | The pilot is not offline-first; server data remains canonical. |

Responsive Web must not rely on persistent media caching. Android cache behaviour must never bypass a fresh playback-authorization check in a later session.

## 13. Error Handling

Errors are classified at the boundary that understands them, then transformed into user-safe feature outcomes. Detailed diagnostics are logged through the logging hook; raw backend or platform errors are not shown to users.

| Condition | User-facing behaviour | Retry behaviour |
| --- | --- | --- |
| No connectivity | Explain that sign-in or streaming needs a connection; retain safe local screen state. | User-initiated retry when connectivity returns. |
| Authentication cancellation/failure/expiry | Keep or return the user to the sign-in commitment screen with a clear, repeatable action. | Sign-in is always retryable; protected requests re-authenticate once where safe. |
| API failure | Show a concise feature-level error without losing user input. | Retry idempotent reads; never blindly replay non-idempotent writes. |
| Playback authorization/manifest failure | Explain that playback could not start or continue; keep the lesson context. | Request fresh authorization before retrying; never reuse expired authorization material. |
| Buffering or transient playback interruption | Show non-intrusive playback status. | Player-controlled recovery first, then a user retry. |
| Recording permission/device/file failure | Explain the required microphone or storage action and preserve the lesson. | Retry after the user changes permission or device state. |

The app shell provides a last-resort error boundary for unexpected failures and a path to recover to a safe route.

## 14. Dependency Injection

Riverpod is the dependency-injection mechanism. Composition providers construct infrastructure services, repositories, and feature controllers. No separate service locator is introduced.

| Dependency type | Lifetime |
| --- | --- |
| Configuration, connectivity, authenticated HTTP client, analytics abstraction | Application scoped. |
| Authentication observer and session repository | Application/session scoped. |
| Feature repositories and controllers | Feature scoped; retain only where ongoing state requires it. |
| Player and recorder resources | Explicit active-session scope; dispose deterministically. |
| Route-local controllers | Route scoped. |

Tests replace provider overrides at the composition boundary, allowing local drivers and deterministic fakes without modifying feature code.

## 15. Environment Configuration

Configuration is injected at build/startup time and validated before the app exposes protected routes. It contains identifiers and endpoint configuration, never committed secrets, signing keys, or service-account credentials.

| Environment | Client behaviour |
| --- | --- |
| Local | Uses local/mock drivers and test media as defined by environment abstraction; works without cloud credentials. |
| Development | Uses isolated development services for day-to-day shared integration when needed. |
| Staging | Uses release-like, isolated services for pre-release validation. |
| Production | Uses production Firebase and backend endpoints with the release configuration. |

Each environment has explicit API base URL, Firebase project configuration, logging level, and service-driver selection. Media and security credentials remain server-side in every environment.

## 16. Testing Strategy

| Test level | Focus | Priority |
| --- | --- | --- |
| Unit | Domain rules, mappers, repository policy, state controllers, error translation, and service contracts using fakes. | High for critical learning, authentication, and playback state. |
| Widget | Frozen screen states, accessibility behaviour, navigation triggers, and error/loading presentation. | High for core flows and reusable shared components. |
| Integration | Startup, sign-in handoff, authenticated navigation, course-to-learning flow, progress update, and playback/recording contract integration using controlled drivers. | Focus on the pilot’s critical paths. |
| Golden | Stable, design-frozen high-value visual states across supported responsive breakpoints. | Target key screens; avoid brittle coverage of every state. |

Production cloud services and real media delivery are validated separately in staging. Local and test drivers keep automated tests deterministic and avoid dependence on credentials or network availability.

## 17. Non-goals

- Widget implementations, package choices beyond the state-management recommendation, or generated code.
- Database schemas, migration strategy, or server persistence logic.
- REST endpoint schemas, API versioning rules, or backend module definitions.
- HLS packaging algorithms, manifest-rewriting implementation, encryption-key design, or CDN configuration.
- Security threat modelling, credential rotation, or incident procedures.
- Offline lessons, downloads, background media libraries, or cross-device recording sync.
- Native iOS-specific architecture, payment flows, AI pronunciation, advanced analytics, or public content uploads.

## 18. Open Items for Future Documents

| Future document | Flutter architecture dependency |
| --- | --- |
| Database Design | Defines canonical entities and ownership that repositories expose to the Flutter domain. |
| API Contracts | Defines the typed remote contracts, errors, and authorization responses consumed by data sources. |
| Media Architecture | Defines manifest, timing-metadata, player-cache, and playback-recovery contracts. |
| Security Architecture | Defines client token handling, storage requirements, logging redaction, and access-control constraints. |

## Review Checklist

- [ ] The document preserves the approved system architecture and all frozen product, UX, and ADR decisions.
- [ ] Feature ownership is clear and no unnecessary feature modules are introduced.
- [ ] Dependency rules prevent presentation code from accessing transport, storage, or platform services directly.
- [ ] Riverpod is accepted as both state-management and dependency-injection approach.
- [ ] Navigation, deep links, authentication, and restoration preserve the frozen user flow.
- [ ] Server authority and the restricted local-storage model are clear.
- [ ] Audio uses backend-authorized HLS playback and does not access GCS or CDN credentials directly.
- [ ] Android’s bounded player cache is permitted only for authorized streaming, not offline use.
- [ ] Local recordings remain device-local for the pilot.
- [ ] The environment and testing strategies support local, development, staging, and production work.

No unresolved architectural blockers.
