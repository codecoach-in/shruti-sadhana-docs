# API Contracts v1

**Status:** Proposed for Architecture Freeze v1
**Version:** 1.0
**Scope:** Phase 0 Pilot public Flutter-client API

## 1. API Design Philosophy

This API is the stable contract between the Flutter clients and the NestJS modular monolith. It derives from the approved system, Flutter, NestJS, and database architectures; it does not introduce product capabilities or expose infrastructure details.

The API is resource-oriented, JSON over HTTPS, and versioned under `/api/v1`. The backend remains the canonical source of truth. Flutter may retain transient view state and bounded cache entries, but every mutation response and later read from the API prevails over a conflicting client value.

Key boundaries:

- Firebase Authentication proves user identity; no login or token-issuance endpoint is provided.
- All post-demo endpoints require a verified Firebase ID token.
- Only published curated content is available to devotees.
- Internal database `BIGINT` identifiers are serialized as decimal strings in JSON. Course slugs are used in public course routes.
- The API never exposes GCS object paths, Cloud CDN configuration, signing keys, or media bytes.
- User practice recordings remain device-local and have no API surface in the pilot.

## 2. REST Design Guidelines

| Rule | Contract decision |
| --- | --- |
| Base path | All routes begin with `/api/v1`. |
| Resource names | Use lowercase plural kebab-case nouns: `courses`, `sections`, `shlokas`, `playback-sessions`. |
| Field names | JSON request and response fields use `camelCase`. |
| Identifiers | `courseSlug` is a stable public course identifier; `sectionId`, `shlokaId`, and other internal IDs are decimal strings. |
| Dates | ISO 8601 UTC strings. |
| Enums | Uppercase strings matching the documented contract values. |
| Mutations | Use `PATCH` for partial state changes and `POST` to create a new resource, such as a playback session or feedback item. |
| Visibility | Resources that are unpublished, unavailable, or inaccessible return a safe `404` rather than leaking internal publication state. |
| Idempotency | Read operations are idempotent. `PATCH` requests replace supplied fields with validated canonical values. `POST /playback-sessions` creates a new short-lived authorization resource on every call. |

## 3. Authentication Flow

```text
Flutter client -> Firebase Authentication: Google Sign-In
Firebase Authentication -> Flutter client: Firebase ID token
Flutter client -> NestJS API: Authorization: Bearer <Firebase ID token>
NestJS API -> Firebase Admin SDK: verify token
NestJS API -> MySQL: resolve application user and authorization
NestJS API -> Flutter client: requested resource or standard error
```

The client sends the Firebase ID token in the `Authorization` header. NestJS verifies it on every protected request and maps its subject to an application user. The backend does not issue an additional application JWT in API Contracts v1.

An expired, revoked, or invalid token returns `401 AUTHENTICATION_REQUIRED`. Flutter clears protected in-memory state, returns to the frozen sign-in commitment flow, and may retry the original request only after Firebase has refreshed identity.

## 4. Response and Error Model

### 4.1 Success envelope

Every successful response uses the same minimal envelope. `meta` is omitted when it adds no value.

```json
{
  "data": {},
  "meta": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 1,
    "totalPages": 1
  }
}
```

This consistent envelope provides room for pagination without wrapping every nested object in transport-specific structures.

### 4.2 Error envelope

```json
{
  "error": {
    "code": "VALIDATION_FAILED",
    "message": "One or more fields are invalid.",
    "details": [
      {
        "field": "dailyTargetShlokas",
        "rule": "min",
        "message": "Must be greater than zero."
      }
    ],
    "requestId": "req_..."
  }
}
```

`details` is supplied only for safe validation failures. `requestId` supports support and log correlation; it contains no user or credential data.

| HTTP status | Error code | Meaning |
| --- | --- | --- |
| 400 | `VALIDATION_FAILED` | Request shape, enum, or field value is invalid. |
| 401 | `AUTHENTICATION_REQUIRED` | Firebase token is missing, invalid, expired, or revoked. |
| 403 | `FORBIDDEN` | Caller is authenticated but cannot perform the requested mutation. |
| 404 | `RESOURCE_NOT_FOUND` | Resource does not exist, is unpublished, or is not visible to the caller. |
| 409 | `STATE_CONFLICT` | Request conflicts with the current canonical state. |
| 429 | `RATE_LIMITED` | Caller should retry after the supplied retry guidance. |
| 500 | `INTERNAL_ERROR` | Unexpected server failure; no internal details are returned. |
| 503 | `SERVICE_UNAVAILABLE` | A required dependency is temporarily unavailable. |

## 5. Common DTOs

The following component models are OpenAPI-ready. All `id` fields described as strings are decimal serializations of unsigned database `BIGINT` values.

| Model | Fields |
| --- | --- |
| User | `id`, `displayName`, `photoUrl`, `role` |
| UserSettings | `scriptPreference`, `fontScale` |
| GuruSummary | `id`, `displayName`, `biography`, `imageUrl` |
| CourseSummary | `slug`, `title`, `shortDescription`, `guru`, `displayOrder`, `progressSummary` |
| CourseDetail | `slug`, `title`, `shortDescription`, `description`, `guru`, `progressSummary`, `sections` |
| SectionSummary | `id`, `title`, `description`, `sequence`, `totalShlokas`, `completedShlokas` |
| SectionDetail | `id`, `courseSlug`, `title`, `description`, `sequence`, `shlokas` |
| ShlokaSummary | `id`, `number`, `title`, `progressStatus`, `audioAvailability` |
| ShlokaDetail | `id`, `courseSlug`, `section`, `number`, `title`, `texts`, `audioVariants`, `resources`, `progress` |
| TextRepresentation | `script`, `content` |
| AudioVariantSummary | `variant`, `durationMs`, `available` |
| LearningResourceSummary | `id`, `type`, `title`, `description` |
| CourseProgress | `courseSlug`, `learningPace`, `dailyTargetShlokas`, `completedShlokas`, `totalShlokas`, `lastResumedShlokaId` |
| ShlokaProgress | `shlokaId`, `status`, `practiceCount`, `completedAt`, `lastPracticedAt` |
| PlaybackSession | `id`, `shlokaId`, `variant`, `manifestUrl`, `expiresAt` |
| Feedback | `id`, `status`, `createdAt` |

`imageUrl` and `manifestUrl` are authorized delivery URLs when present; they are never storage object paths. `LearningResourceSummary` deliberately omits `resourceLocation`. Resource-access delivery is not part of the pilot client flows and must not be inferred from metadata.

## 6. Endpoint Catalog

All endpoints below require Firebase bearer authentication unless marked otherwise. The approved guided demo is client-side and does not require an API endpoint in this contract.

| Method | Path | Purpose | Auth |
| --- | --- | --- | --- |
| GET | `/me` | Bootstrap current user identity and server-authoritative settings. | Required |
| GET | `/me/settings` | Read current user settings. | Required |
| PATCH | `/me/settings` | Update supported user settings. | Required |
| GET | `/courses` | List published course catalogue summaries. | Required |
| GET | `/courses/{courseSlug}` | Read one published course and section summaries. | Required |
| GET | `/courses/{courseSlug}/sections` | Refresh section summaries for a course without loading shloka content. | Required |
| GET | `/sections/{sectionId}` | Read one section and its shloka summaries. | Required |
| GET | `/sections/{sectionId}/shlokas` | Refresh shloka summaries for a section. | Required |
| GET | `/shlokas/{shlokaId}` | Read one shloka’s text, published audio metadata, resource metadata, and caller progress. | Required |
| POST | `/playback-sessions` | Create short-lived authorization for one published audio variant. | Required |
| GET | `/me/progress` | Read course-level progress and resume state. | Required |
| PATCH | `/me/progress/courses/{courseSlug}` | Update the current course pace or resume target. | Required |
| PATCH | `/me/progress/shlokas/{shlokaId}` | Update canonical status/practice state for one shloka. | Required |
| POST | `/me/feedback` | Submit authenticated user feedback. | Required |

The separate Admin Console uses a distinct internal API surface and is explicitly outside this document.

## 7. OpenAPI-Ready Endpoint Definitions

### 7.1 User bootstrap and settings

#### `GET /api/v1/me`

**Purpose:** Resolve the authenticated user and settings at application launch after Firebase identity is available.

| Item | Definition |
| --- | --- |
| Authentication | Required bearer Firebase ID token. |
| Path/query parameters | None. |
| Request body | None. |
| 200 response | `data: { user: User, settings: UserSettings }` |
| Errors | 401, 500, 503. |

Example response:

```json
{
  "data": {
    "user": { "id": "42", "displayName": "Sita", "photoUrl": null, "role": "DEVOTEE" },
    "settings": { "scriptPreference": "DEVANAGARI", "fontScale": null }
  }
}
```

#### `GET /api/v1/me/settings`

**Purpose:** Read server-authoritative preferences after bootstrap or on opening Settings.

| Item | Definition |
| --- | --- |
| Authentication | Required. |
| Path/query parameters | None. |
| Request body | None. |
| 200 response | `data: UserSettings` |
| Errors | 401, 500, 503. |

#### `PATCH /api/v1/me/settings`

**Purpose:** Persist supported preference changes.

| Item | Definition |
| --- | --- |
| Authentication | Required. |
| Request body | `scriptPreference?: DEVANAGARI | IAST | ENGLISH_TRANSLITERATION | TELUGU | TAMIL | KANNADA`, `fontScale?: integer` |
| Validation | At least one supported field; `fontScale`, when supplied, must be within the client-supported accessibility range defined by implementation. |
| 200 response | `data: UserSettings` with canonical persisted values. |
| Errors | 400, 401, 500, 503. |

### 7.2 Course catalogue and course details

#### `GET /api/v1/courses`

**Purpose:** Load the Library without section, shloka, text, audio, or resource data.

| Item | Definition |
| --- | --- |
| Authentication | Required. |
| Query parameters | `page?: integer` (default 1), `pageSize?: integer` (default 20, maximum 50). |
| Validation | Positive page; page size 1–50. |
| 200 response | `data: CourseSummary[]`, `meta: PaginationMeta` |
| Errors | 400, 401, 500, 503. |

`CourseSummary.progressSummary` contains only `completedShlokas` and `totalShlokas` for that course. It does not include a course plan, section list, or resume detail.

#### `GET /api/v1/courses/{courseSlug}`

**Purpose:** Load Course Details and section summaries without shloka text, audio metadata, or resources.

| Item | Definition |
| --- | --- |
| Authentication | Required. |
| Path parameters | `courseSlug: string`, a published Course slug. |
| Request body | None. |
| 200 response | `data: CourseDetail` |
| Errors | 401, 404, 500, 503. |

`CourseDetail.sections` contains `SectionSummary[]`. `SectionSummary` exposes counts and caller progress but not individual Shloka records.

#### `GET /api/v1/courses/{courseSlug}/sections`

**Purpose:** Refresh section summaries after a course mutation or direct navigation without repeating course/guru data.

| Item | Definition |
| --- | --- |
| Authentication | Required. |
| Path parameters | `courseSlug: string`. |
| Request body | None. |
| 200 response | `data: SectionSummary[]` |
| Errors | 401, 404, 500, 503. |

### 7.3 Sections and shlokas

#### `GET /api/v1/sections/{sectionId}`

**Purpose:** Load the Shloka Index for one section.

| Item | Definition |
| --- | --- |
| Authentication | Required. |
| Path parameters | `sectionId: decimal string`. |
| Request body | None. |
| 200 response | `data: SectionDetail` |
| Errors | 400, 401, 404, 500, 503. |

`SectionDetail.shlokas` contains `ShlokaSummary[]` only. It never contains text bodies, HLS details, or learning-resource locations.

#### `GET /api/v1/sections/{sectionId}/shlokas`

**Purpose:** Refresh one section’s lightweight shloka index.

| Item | Definition |
| --- | --- |
| Authentication | Required. |
| Path parameters | `sectionId: decimal string`. |
| Request body | None. |
| 200 response | `data: ShlokaSummary[]` |
| Errors | 400, 401, 404, 500, 503. |

#### `GET /api/v1/shlokas/{shlokaId}`

**Purpose:** Load the Learning Session’s canonical content only when the learner opens a shloka.

| Item | Definition |
| --- | --- |
| Authentication | Required. |
| Path parameters | `shlokaId: decimal string`. |
| Query parameters | `scripts?: comma-separated Script enum values`; default is the user’s selected script plus English Transliteration when available. |
| Request body | None. |
| 200 response | `data: ShlokaDetail` |
| Errors | 400, 401, 404, 500, 503. |

`ShlokaDetail.audioVariants` includes only published/available variants as metadata (`variant`, `durationMs`, `available`). The response has no manifest URL and no GCS/CDN path. `resources` contains only `LearningResourceSummary[]`.

### 7.4 Playback authorization

#### `POST /api/v1/playback-sessions`

**Purpose:** Create a short-lived, authorized playback resource for one published shloka audio variant.

| Item | Definition |
| --- | --- |
| Authentication | Required. |
| Request body | `shlokaId: decimal string`, `variant: AudioVariant`. |
| Validation | The shloka ID is valid; the variant exists and is published; caller may access its parent course. |
| 201 response | `data: PlaybackSession` |
| Errors | 400, 401, 404, 429, 500, 503. |

Example response:

```json
{
  "data": {
    "id": "901",
    "shlokaId": "314",
    "variant": "COMPLETE_SHLOKA",
    "manifestUrl": "https://media.example/...",
    "expiresAt": "2026-07-17T12:15:00Z"
  }
}
```

`manifestUrl` is a short-lived signed manifest URL or authorized manifest endpoint. The client passes it to its player abstraction; subsequent protected HLS requests go directly to Cloud CDN. The API does not proxy those requests or return refresh tokens, keys, object paths, or storage credentials.

### 7.5 Progress

#### `GET /api/v1/me/progress`

**Purpose:** Load course-level progress for Home, Progress, and resume entry.

| Item | Definition |
| --- | --- |
| Authentication | Required. |
| Query parameters | `courseSlug?: string`; when omitted, returns all enrolled/current pilot course summaries. |
| Validation | Supplied slug must identify a visible course. |
| 200 response | `data: CourseProgress[]` |
| Errors | 400, 401, 404, 500, 503. |

This endpoint returns course-level state only; it never returns every UserShlokaProgress row.

#### `PATCH /api/v1/me/progress/courses/{courseSlug}`

**Purpose:** Change the course-specific learning pace or current resume target.

| Item | Definition |
| --- | --- |
| Authentication | Required. |
| Path parameters | `courseSlug: string`. |
| Request body | `learningPace?: SLOW_AND_STEADY | BALANCED`, `lastResumedShlokaId?: decimal string | null`. |
| Validation | At least one field; resume shloka must belong to the specified course; pace maps to the canonical daily target server-side. |
| 200 response | `data: CourseProgress` |
| Errors | 400, 401, 404, 409, 500, 503. |

The server calculates and persists `dailyTargetShlokas`; Flutter does not submit a client-calculated target in the pilot.

#### `PATCH /api/v1/me/progress/shlokas/{shlokaId}`

**Purpose:** Persist a shloka’s canonical practice/completion state.

| Item | Definition |
| --- | --- |
| Authentication | Required. |
| Path parameters | `shlokaId: decimal string`. |
| Request body | `status?: NOT_STARTED | IN_PROGRESS | COMPLETED`, `practiceOccurred?: boolean`. |
| Validation | At least one field; shloka must be published and accessible; status transition must be allowed by Progress rules. |
| 200 response | `data: ShlokaProgress` |
| Errors | 400, 401, 404, 409, 500, 503. |

When `practiceOccurred` is `true`, the server increments `practiceCount` and updates `lastPracticedAt`. Completion rules remain server-side; the response supplies canonical timestamps and counts.

### 7.6 Feedback

#### `POST /api/v1/me/feedback`

**Purpose:** Submit user feedback through the frozen in-app feedback entry point.

| Item | Definition |
| --- | --- |
| Authentication | Required. |
| Request body | `message: string`. |
| Validation | Required trimmed text; 1–2,000 characters; no client-supplied status or user ID. |
| 201 response | `data: Feedback` |
| Errors | 400, 401, 429, 500, 503. |

## 8. Validation Rules

| Area | Rule |
| --- | --- |
| IDs | Section, Shloka, resource, and playback-session IDs are non-empty decimal strings in transport. The backend parses and validates them as unsigned `BIGINT` without JavaScript number coercion. |
| Course slug | Lowercase, URL-safe course slug; route lookup is exact. |
| Enums | Reject unknown enum values; do not silently default a mutation. |
| Partial updates | PATCH body must include at least one mutable field. Unknown fields are rejected. |
| Settings | Only approved preference fields are mutable. |
| Progress | Mutations are scoped to the authenticated user; client identifiers never determine ownership. |
| Playback | A variant must be published and linked to a published, visible shloka before authorization is issued. |
| Feedback | Trim whitespace, reject empty content, and apply rate limiting. |

## 9. Versioning Strategy

URI versioning with `/api/v1` is recommended. It is explicit in Flutter configuration, simple to observe in logs and OpenAPI, and allows incompatible contracts to be introduced later without ambiguous content negotiation.

Backward-compatible additions—optional response fields, new enum values handled safely by clients, and new routes—remain within v1. Removing/renaming a field, changing its type, changing authorization semantics, or changing an error contract requires a new major version.

## 10. Pagination Strategy

Pagination is used only for the catalogue, where additional courses may grow beyond the pilot. `GET /courses` returns page-based `PaginationMeta` with a default page size of 20 and maximum of 50.

Sections in one course and shlokas in one section are deliberately unpaginated: they are finite canonical learning structures required for navigation and progress context. Shloka detail remains separate to prevent their content from becoming an oversized response.

## 11. Primary User-Flow Sequences

### 11.1 App Launch

```text
Flutter -> Firebase: restore/refresh identity
Flutter -> API GET /me: bearer ID token
API -> Firebase Admin: verify identity
API -> MySQL: load user and settings
API -> Flutter: User + UserSettings
Flutter -> API GET /me/progress: bearer ID token
API -> Flutter: CourseProgress summaries
```

### 11.2 Course Catalog

```text
Flutter -> API GET /courses?page=1&pageSize=20
API -> MySQL: published courses + caller progress summaries
API -> Flutter: paginated CourseSummary list
```

### 11.3 Course Details

```text
Flutter -> API GET /courses/lalita-sahasranama-chanting
API -> MySQL: published course, guru, section summaries, caller progress counts
API -> Flutter: CourseDetail without shloka content
```

### 11.4 Learning Session

```text
Flutter -> API GET /shlokas/314?scripts=DEVANAGARI,ENGLISH_TRANSLITERATION
API -> MySQL: visible shloka, requested texts, audio metadata, resources, caller progress
API -> Flutter: ShlokaDetail without media locations
Flutter -> API POST /playback-sessions
API -> MySQL: verify content visibility and caller access
API -> signing adapter: create short-lived signed manifest access
API -> Flutter: PlaybackSession(manifestUrl, expiresAt)
Flutter -> Cloud CDN: authorized HLS manifest, segments, and key requests
```

### 11.5 Progress Update

```text
Flutter -> API PATCH /me/progress/shlokas/314
API -> MySQL: validate ownership/content state and update canonical progress
API -> Flutter: canonical ShlokaProgress
Flutter -> API PATCH /me/progress/courses/lalita-sahasranama-chanting
API -> MySQL: update valid resume target or learning pace
API -> Flutter: canonical CourseProgress
```

## 12. Module Ownership Matrix

Module ownership follows the approved NestJS modular-monolith boundaries. Sections and Shlokas remain content entities owned by the Courses module; they do not become separate NestJS modules merely because they have resource routes.

| Endpoint | Owning NestJS module | Owning controller responsibility |
| --- | --- | --- |
| `GET /me` | Users | Resolve the authenticated application user and compose the bootstrap response. |
| `GET /me/settings`, `PATCH /me/settings` | Settings | Read and update server-authoritative user preferences. |
| `GET /courses`, `GET /courses/{courseSlug}` | Courses | Serve published catalogue and course-detail read models. |
| `GET /courses/{courseSlug}/sections` | Courses | Serve lightweight section summaries for a course. |
| `GET /sections/{sectionId}`, `GET /sections/{sectionId}/shlokas` | Courses | Serve published section and shloka-index read models. |
| `GET /shlokas/{shlokaId}` | Courses | Serve one published learning-unit detail without authorizing media delivery. |
| `POST /playback-sessions` | Playback Authorization | Authorize one playback request and return short-lived manifest access. |
| `GET /me/progress`, `PATCH /me/progress/courses/{courseSlug}`, `PATCH /me/progress/shlokas/{shlokaId}` | Progress | Read and mutate canonical user course/shloka progress. |
| `POST /me/feedback` | Feedback | Validate and persist authenticated user feedback. |

Firebase verification is a cross-cutting Authentication concern applied by guards; it does not make Authentication the business owner of every protected endpoint.

## 13. Repository Ownership Guidelines

Repositories are private implementation details of their owning module. A module that needs another module’s data depends on that module’s explicit application/domain read contract, never on its Prisma repository.

| Repository or read port | Owning module | Owns | May serve |
| --- | --- | --- | --- |
| UserRepository | Users | User identity mapping and profile data. | Users only. |
| SettingsRepository | Settings | UserSettings persistence. | Settings only. |
| CourseRepository | Courses | Course, Guru, Section, and curated course-level read composition. | Published course read contracts. |
| ShlokaRepository | Courses | Shloka, ShlokaText, ShlokaAudio, and LearningResource persistence/read models. | Published content read contracts. |
| ProgressRepository | Progress | UserCourseProgress and UserShlokaProgress. | Progress summaries through an explicit Progress read contract. |
| FeedbackRepository | Feedback | UserFeedback persistence. | Feedback only. |
| PlaybackContentReader | Courses public read port | Published ShlokaAudio eligibility/metadata required for playback authorization. | Playback Authorization only through the contract. |
| PlaybackSessionRepository | None | Not created in the pilot; playback sessions are short-lived authorization responses, not persisted application entities. | Not applicable. |

The Playback Authorization module uses `PlaybackContentReader` and a signing adapter. It must not import `ShlokaRepository`, query Prisma directly, or own a duplicate media-metadata repository.

## 14. DTO Ownership Guidelines

Transport DTOs live beside the controller/module that owns the endpoint. Shared nested response shapes may live in a small `api-contracts` shared package only when multiple module-owned DTOs use the same stable shape; shared types must not own endpoint logic.

| DTO | Owning module | Used by |
| --- | --- | --- |
| MeResponseDto, UserResponseDto | Users | `GET /me` |
| UserSettingsResponseDto, UpdateUserSettingsRequestDto | Settings | Settings endpoints; embedded in `MeResponseDto` by contract composition. |
| CourseSummaryResponseDto, CourseDetailResponseDto, SectionSummaryResponseDto, SectionDetailResponseDto | Courses | Course and section endpoints. |
| ShlokaSummaryResponseDto, ShlokaDetailResponseDto, TextRepresentationResponseDto, AudioVariantSummaryResponseDto, LearningResourceSummaryResponseDto | Courses | Section-index and Shloka-detail endpoints. |
| CreatePlaybackSessionRequestDto, PlaybackSessionResponseDto | Playback Authorization | `POST /playback-sessions` |
| CourseProgressResponseDto, ShlokaProgressResponseDto, UpdateCourseProgressRequestDto, UpdateShlokaProgressRequestDto | Progress | Progress endpoints. |
| CreateFeedbackRequestDto, FeedbackResponseDto | Feedback | `POST /me/feedback` |
| PaginationMetaDto, ErrorResponseDto, ValidationErrorDetailDto | Shared API contracts | Consistent envelope and error payloads. |

DTOs use the API’s `camelCase` field names. DTOs that expose identifiers serialize database `BIGINT` values as decimal strings at the transport boundary.

## 15. Transaction Guidance

Transactions are introduced only when one API operation must atomically update multiple canonical records. Read-only endpoints do not open write transactions.

| Mutation endpoint | Transaction | Rationale |
| --- | --- | --- |
| `PATCH /me/settings` | No explicit transaction | One UserSettings update/upsert is a single atomic database statement. |
| `POST /playback-sessions` | No | It authorizes and signs a short-lived response without persisting a PlaybackSession record. |
| `PATCH /me/progress/courses/{courseSlug}` | No explicit transaction | One UserCourseProgress update is atomic after validating the resume target. |
| `PATCH /me/progress/shlokas/{shlokaId}` | Yes | Canonical practice/completion state and the affected course resume state must remain consistent if both change. |
| `POST /me/feedback` | No explicit transaction | One UserFeedback insert is atomic. |

If a future mutation changes the content publication state and related records together, its transaction design belongs to the separate internal Admin Console contract, not this public API.

## 16. Caching Recommendations

Caches are performance optimizations, not alternate sources of truth. Any mutation response replaces affected client state; protected user state is revalidated on app launch.

| GET endpoint | Client cache recommendation | Server cache recommendation | Rationale |
| --- | --- | --- | --- |
| `GET /me` | No persistent cache; retain only for the active app session. | No. | Identity and settings are user-specific and bootstrap-critical. |
| `GET /me/settings` | No persistent cache; update from mutation response. | No. | Settings are small and user-specific. |
| `GET /courses` | Short-lived, revalidated catalogue cache. | Optional short-lived cache for published catalogue data. | Catalogue changes infrequently but caller progress must remain current. |
| `GET /courses/{courseSlug}` | Short-lived, revalidated content cache. | Optional short-lived cache for published content portions. | Course metadata changes infrequently; caller progress remains dynamic. |
| `GET /courses/{courseSlug}/sections` | Short-lived, revalidated content cache. | Optional short-lived cache for published section data. | Supports efficient index refresh without caching shloka details. |
| `GET /sections/{sectionId}` and `/shlokas` | Short-lived, revalidated content cache. | Optional short-lived cache for published content portions. | Section/shloka indexes are stable but contain caller progress summaries. |
| `GET /shlokas/{shlokaId}` | In-memory only for the active learning session; revalidate on re-entry. | No default cache. | Includes caller progress and content chosen by script query. |
| `GET /me/progress` | No persistent cache; replace after progress mutations. | No. | Canonical user progress must not drift. |

`POST /playback-sessions` responses, manifest URLs, signed URLs, and authorization material must never be stored in a persistent client or server response cache.

## 17. Vertical Slice Implementation Plan

Each slice is complete only when its Flutter surface, backend contract, module boundary, persistence path, tests, and acceptance criteria are delivered together.

### Slice 1 — User Bootstrap

```text
Flutter app bootstrap
        ↓
GET /me
        ↓
UsersController -> UsersService -> UserRepository
        ↓
Prisma -> MySQL
```

**Acceptance criteria:** After Firebase sign-in, the app receives a canonical User and UserSettings response; invalid identity produces the standard 401 payload; no Firebase tokens are persisted by the backend.

### Slice 2 — Course Library

```text
Flutter Library screen (SS-004)
        ↓
GET /courses
        ↓
CoursesController -> CoursesService -> CourseRepository
        ↓
Prisma -> MySQL
```

**Acceptance criteria:** The Library displays only published course summaries in canonical display order, with caller-specific completion counts and pagination metadata; no sections, shlokas, text, or media paths are returned.

### Slice 3 — Course Details and Shloka Index

```text
Flutter Course Details / Shloka Index (SS-005, SS-007)
        ↓
GET /courses/{courseSlug}
GET /courses/{courseSlug}/sections
GET /sections/{sectionId}
        ↓
CoursesController -> CoursesService -> CourseRepository / ShlokaRepository
        ↓
Prisma -> MySQL
```

**Acceptance criteria:** Course Details returns guru and section summaries; the Shloka Index returns canonical shloka numbers and caller progress summaries; neither path loads shloka text, audio authorization, or resource locations.

### Slice 4 — Learning Session and Playback Authorization

```text
Flutter Learning Session (SS-008)
        ↓
GET /shlokas/{shlokaId}
POST /playback-sessions
        ↓
CoursesController -> ShlokaRepository
PlaybackAuthorizationController -> PlaybackAuthorizationService -> PlaybackContentReader + signing adapter
        ↓
Prisma/MySQL                     Cloud signing infrastructure
```

**Acceptance criteria:** A learner can load permitted script text, published audio metadata, resource metadata, and current progress; requesting an available variant returns a short-lived manifest URL; no GCS object path, key, or media byte crosses the API boundary.

### Slice 5 — Progress and Resume

```text
Flutter Home / Progress / Learning Session (SS-003, SS-011, SS-008)
        ↓
GET /me/progress
PATCH /me/progress/shlokas/{shlokaId}
PATCH /me/progress/courses/{courseSlug}
        ↓
ProgressController -> ProgressService -> ProgressRepository
        ↓
Prisma transaction where required -> MySQL
```

**Acceptance criteria:** The app reads canonical course progress, updates only the authenticated user’s shloka state, and receives the server-calculated course target/resume state. Shloka progress and any resulting resume update commit atomically.

### Slice 6 — Settings

```text
Flutter Settings / script controls (SS-012, SS-BS-001, SS-BS-002)
        ↓
GET /me/settings
PATCH /me/settings
        ↓
SettingsController -> SettingsService -> SettingsRepository
        ↓
Prisma -> MySQL
```

**Acceptance criteria:** Supported preferences persist server-side, returned values replace local state, and invalid enum/value updates receive standard validation errors.

### Slice 7 — Feedback

```text
Flutter feedback entry point
        ↓
POST /me/feedback
        ↓
FeedbackController -> FeedbackService -> FeedbackRepository
        ↓
Prisma -> MySQL
```

**Acceptance criteria:** An authenticated user can submit validated feedback; the server assigns status and user ownership; request rate limiting and standard errors apply.

## 18. Slice Dependency Matrix

| Slice | Flutter screens | API endpoints | NestJS modules | Repositories/read ports | Database tables | Depends on |
| --- | --- | --- | --- | --- | --- | --- |
| 1. User Bootstrap | App bootstrap; post-auth transition | `GET /me` | Users, Authentication guard | UserRepository, SettingsRepository | User, UserSettings | Firebase project configuration. |
| 2. Course Library | SS-004; Home library entry | `GET /courses` | Courses, Progress read contract | CourseRepository, Progress read port | Course, Guru, UserCourseProgress, UserShlokaProgress | Slice 1. |
| 3. Course Details and Shloka Index | SS-005, SS-006, SS-007 | Course/section GET endpoints | Courses | CourseRepository, ShlokaRepository | Course, Guru, Section, Shloka, UserShlokaProgress | Slice 2. |
| 4. Learning Session | SS-008 | `GET /shlokas/{shlokaId}`, `POST /playback-sessions` | Courses, Playback Authorization | ShlokaRepository, PlaybackContentReader | Shloka, ShlokaText, ShlokaAudio, LearningResource, UserShlokaProgress | Slice 3; media signing adapter/local driver. |
| 5. Progress and Resume | SS-003, SS-008, SS-011 | Progress GET/PATCH endpoints | Progress | ProgressRepository | UserCourseProgress, UserShlokaProgress, Course, Shloka | Slices 1 and 3. |
| 6. Settings | SS-012, SS-BS-001, SS-BS-002 | Settings GET/PATCH endpoints | Settings | SettingsRepository | UserSettings | Slice 1. |
| 7. Feedback | Settings feedback entry point | `POST /me/feedback` | Feedback | FeedbackRepository | UserFeedback, User | Slice 1. |

Slice 4 and Slice 5 may be implemented in either order after Slice 3. Their only shared contract is the canonical Shloka identifier and caller progress returned by the existing API models.

## 19. Recommendations Before Implementation

1. Generate the NestJS OpenAPI document from these endpoint and component contracts, then use it to generate or validate strongly typed Flutter DTOs.
2. Implement the first vertical slice in this order: `GET /me`, `GET /courses`, `GET /courses/{courseSlug}`, `GET /shlokas/{shlokaId}`, and `POST /playback-sessions`.
3. Treat all database BIGINT values as transport strings in NestJS serializers and Flutter models from the first endpoint onward.
4. Add contract tests for every success and standard error response before expanding the API surface.
5. Validate signed-manifest expiry and playback recovery against the Media Architecture and Security Architecture documents before production release.
