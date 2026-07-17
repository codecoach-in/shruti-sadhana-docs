# Database Design v1

**Status:** Proposed for Architecture Freeze v1
**Version:** 1.0
**Scope:** Phase 0 Pilot relational data model (Prisma + MySQL)

## 1. Overview

The database models Shruti Sadhana as a curated guided-practice platform, not a generic LMS or marketplace. Its centre is a stable learning hierarchy—Course, Section, and Shloka—alongside authenticated user progress and settings. MySQL is the canonical source of truth for application and content metadata; Firebase owns identity issuance, and Google Cloud Storage owns media bytes.

The design favours explicit business entities, stable ordering, and simple constraints over generic content abstractions or speculative future modules. Prisma maps the relational model to the NestJS infrastructure layer; Prisma models are not exposed directly to the Flutter client.

Internal relational entities use unsigned `BIGINT AUTO_INCREMENT` identifiers. In this centralized MySQL architecture, they provide compact indexes, efficient joins, and straightforward operational debugging without the distributed-ID benefits that `cuid()` would provide. API contracts must serialize these identifiers as strings to avoid JavaScript integer-precision loss. Firebase UIDs and course slugs remain stable external identifiers.

## 2. Domain Assumptions

| Assumption | Database implication |
| --- | --- |
| A course is a learning programme, not a scripture. | Multiple courses may later be built around the same underlying spiritual content without changing the Course model. |
| The canonical hierarchy is Course → Section → Shloka. | Every shloka belongs to one section; a course without natural sections receives an implicit/default section. |
| Sections are organisational containers. | Nyasa, Dhyanam, Main Stotram, and Phalashruti are values of Section, not entity types. |
| Shloka is the smallest learning unit. | Progress, audio, and textual representations attach to Shloka. Divine Names are not modelled. |
| Numbering resets inside a section. | `Shloka.number` is unique only within its parent section. |
| Ordering is canonical and not user-sortable. | Sections have an explicit sequence; shlokas use their section-scoped canonical number as the order. |
| Content is curated. | Content has administrator-managed publication state; no user-generated content entities exist. |
| Audio lives in GCS/CDN. | The database stores private object-path metadata, never media bytes, signed URLs, decryption keys, or CDN credentials. |
| Pilot recordings stay on device. | No recording table, storage relation, or recording-upload API data model exists. |
| Learning progress represents practice, not memorisation. | Progress tracks shloka completion and practice activity without scores, mastery, or memory claims. |
| The backend is authoritative. | Client caches and UI state are never persisted as competing canonical records. |

Audio variants are modelled as a controlled enum. The pilot may publish only `COMPLETE_SHLOKA`; `WORD_BY_WORD` and `LINE_BY_LINE` are represented to avoid a future schema migration, but their learning experiences remain out of scope.

## 3. Entity List

| Entity | Purpose |
| --- | --- |
| User | Maps a verified Firebase subject to the Shruti Sadhana application user and minimal role state. |
| UserSettings | Holds authenticated, server-authoritative user preferences. |
| Guru | Holds the single curated guide associated with a pilot course. |
| Course | Represents one learning programme and its publication state. |
| Section | Represents an ordered organisational container inside a course. |
| Shloka | Represents the smallest canonical learning and progress unit. |
| ShlokaText | Stores a script-specific representation of one shloka. |
| ShlokaAudio | Stores metadata for a curated audio variant and its private media locations. |
| LearningResource | Attaches one supplementary curated resource to a Course, Section, or Shloka. |
| UserCourseProgress | Holds a user’s course-level plan pace and resume target. |
| UserShlokaProgress | Holds a user’s learning/practice progress for one shloka. |
| UserFeedback | Holds a validated feedback submission from an authenticated user. |

## 4. Entity Relationships

```text
Guru 1 ─────── * Course 1 ─────── * Section 1 ─────── * Shloka
                    │                                      │
                    │                                      ├── * ShlokaText
                    │                                      ├── * ShlokaAudio
                    │                                      └── * UserShlokaProgress * ── 1 User
                    │
                    ├── * UserCourseProgress * ────────────── 1 User
                    └── * LearningResource

Section ── * LearningResource
Shloka  ── * LearningResource
User    ── 1 UserSettings
User    ── * UserFeedback
```

`LearningResource` has exactly one owner: Course, Section, or Shloka. This is a controlled polymorphic attachment model for supplementary resources, not a generic CMS. A database check constraint enforces the one-owner rule.

## 5. Prisma Models

The following schema is the Database Design v1 contract. It deliberately stores GCS object paths rather than public URLs, because playback authorization and signed delivery remain backend/media concerns.

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}

enum UserRole {
  DEVOTEE
  ADMINISTRATOR
}

enum PublicationStatus {
  DRAFT
  PUBLISHED
  ARCHIVED
}

enum Script {
  DEVANAGARI
  IAST
  ENGLISH_TRANSLITERATION
  TELUGU
  TAMIL
  KANNADA
}

enum AudioVariant {
  WORD_BY_WORD
  LINE_BY_LINE
  COMPLETE_SHLOKA
}

enum ResourceType {
  AUDIO
  PDF
  IMAGE
  VIDEO
  TEXT_NOTE
  EXTERNAL_LINK
}

enum LearningPace {
  SLOW_AND_STEADY
  BALANCED
}

enum ShlokaProgressStatus {
  NOT_STARTED
  IN_PROGRESS
  COMPLETED
}

enum FeedbackStatus {
  NEW
  REVIEWED
  CLOSED
}

/// Authenticated Shruti Sadhana user, mapped from a verified Firebase subject.
model User {
  id             BigInt               @id @default(autoincrement()) @db.UnsignedBigInt
  firebaseUid    String               @unique @db.VarChar(128)
  email          String?              @db.VarChar(320)
  displayName    String?              @db.VarChar(160)
  photoUrl       String?              @db.VarChar(1024)
  role           UserRole             @default(DEVOTEE)
  settings       UserSettings?
  courseProgress UserCourseProgress[]
  shlokaProgress UserShlokaProgress[]
  feedback       UserFeedback[]
  createdAt      DateTime             @default(now())
  updatedAt      DateTime             @updatedAt

  @@index([role])
}

/// Server-authoritative preferences for one authenticated user.
model UserSettings {
  id               BigInt   @id @default(autoincrement()) @db.UnsignedBigInt
  userId           BigInt   @unique @db.UnsignedBigInt
  scriptPreference Script   @default(DEVANAGARI)
  fontScale        Int?
  user             User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  createdAt        DateTime @default(now())
  updatedAt        DateTime @updatedAt
}

/// Curated guide assigned directly to a course in Database Design v1.
model Guru {
  id          BigInt   @id @default(autoincrement()) @db.UnsignedBigInt
  displayName String   @db.VarChar(160)
  biography   String?  @db.Text
  imagePath   String?  @db.VarChar(1024)
  courses     Course[]
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}

/// A curated learning programme, distinct from the scripture or subject it teaches.
model Course {
  id              BigInt                @id @default(autoincrement()) @db.UnsignedBigInt
  slug            String                @unique @db.VarChar(160)
  title           String                @db.VarChar(255)
  shortDescription String?              @db.VarChar(500)
  description     String?               @db.Text
  guruId          BigInt                @db.UnsignedBigInt
  status          PublicationStatus     @default(DRAFT)
  displayOrder    Int                   @default(0)
  guru            Guru                  @relation(fields: [guruId], references: [id], onDelete: Restrict)
  sections        Section[]
  resources       LearningResource[]    @relation("CourseResources")
  userProgress    UserCourseProgress[]
  createdAt       DateTime              @default(now())
  updatedAt       DateTime              @updatedAt

  @@index([status, displayOrder])
  @@index([guruId])
}

/// Ordered organisational container within a course; it is not a content subtype.
model Section {
  id          BigInt                @id @default(autoincrement()) @db.UnsignedBigInt
  courseId    BigInt                @db.UnsignedBigInt
  title       String                @db.VarChar(255)
  description String?               @db.Text
  sequence    Int
  isImplicit  Boolean               @default(false)
  status      PublicationStatus     @default(DRAFT)
  course      Course                @relation(fields: [courseId], references: [id], onDelete: Cascade)
  shlokas     Shloka[]
  resources   LearningResource[]    @relation("SectionResources")
  createdAt   DateTime              @default(now())
  updatedAt   DateTime              @updatedAt

  @@unique([courseId, sequence])
  @@index([courseId, status, sequence])
}

/// Smallest canonical learning and progress unit. Its number is its canonical order in a section.
model Shloka {
  id                    BigInt                @id @default(autoincrement()) @db.UnsignedBigInt
  sectionId             BigInt                @db.UnsignedBigInt
  number                Int
  title                 String?               @db.VarChar(255)
  status                PublicationStatus     @default(DRAFT)
  section               Section               @relation(fields: [sectionId], references: [id], onDelete: Cascade)
  texts                 ShlokaText[]
  audio                 ShlokaAudio[]
  resources             LearningResource[]    @relation("ShlokaResources")
  userProgress          UserShlokaProgress[]
  resumeReferences      UserCourseProgress[]  @relation("ResumeShloka")
  createdAt             DateTime              @default(now())
  updatedAt             DateTime              @updatedAt

  @@unique([sectionId, number])
  @@index([sectionId, status, number])
}

/// One script-specific view of a shloka; it does not represent meaning or commentary.
model ShlokaText {
  id        BigInt   @id @default(autoincrement()) @db.UnsignedBigInt
  shlokaId  BigInt   @db.UnsignedBigInt
  script    Script
  content   String   @db.LongText
  shloka    Shloka   @relation(fields: [shlokaId], references: [id], onDelete: Cascade)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@unique([shlokaId, script])
  @@index([script])
}

/// Metadata for one curated audio variant; media bytes remain in private object storage.
model ShlokaAudio {
  id                       BigInt                @id @default(autoincrement()) @db.UnsignedBigInt
  shlokaId                 BigInt                @db.UnsignedBigInt
  variant                  AudioVariant
  status                   PublicationStatus     @default(DRAFT)
  hlsManifestObjectPath    String                @db.VarChar(1024)
  synchronizationObjectPath String?              @db.VarChar(1024)
  durationMs               Int?
  shloka                   Shloka                @relation(fields: [shlokaId], references: [id], onDelete: Cascade)
  createdAt                DateTime              @default(now())
  updatedAt                DateTime              @updatedAt

  @@unique([shlokaId, variant])
  @@index([shlokaId, status])
}

/// Supplementary curated material attached to exactly one course, section, or shloka.
model LearningResource {
  id          BigInt                @id @default(autoincrement()) @db.UnsignedBigInt
  courseId    BigInt?               @db.UnsignedBigInt
  sectionId   BigInt?               @db.UnsignedBigInt
  shlokaId    BigInt?               @db.UnsignedBigInt
  type        ResourceType
  status      PublicationStatus     @default(DRAFT)
  title       String                @db.VarChar(255)
  description String?               @db.Text
  /// Private object path or approved external URI; never a signed delivery URL.
  resourceLocation String            @db.VarChar(1024)
  sequence    Int                   @default(0)
  course      Course?               @relation("CourseResources", fields: [courseId], references: [id], onDelete: Cascade)
  section     Section?              @relation("SectionResources", fields: [sectionId], references: [id], onDelete: Cascade)
  shloka      Shloka?               @relation("ShlokaResources", fields: [shlokaId], references: [id], onDelete: Cascade)
  createdAt   DateTime              @default(now())
  updatedAt   DateTime              @updatedAt

  @@index([courseId, status, sequence])
  @@index([sectionId, status, sequence])
  @@index([shlokaId, status, sequence])
}

/// Per-user, per-course progress state, including course-specific plan pace and resume target.
model UserCourseProgress {
  id                   BigInt                @id @default(autoincrement()) @db.UnsignedBigInt
  userId               BigInt                @db.UnsignedBigInt
  courseId             BigInt                @db.UnsignedBigInt
  learningPace         LearningPace          @default(SLOW_AND_STEADY)
  dailyTargetShlokas   Int                   @default(3)
  lastResumedShlokaId  BigInt?               @db.UnsignedBigInt
  user                 User                  @relation(fields: [userId], references: [id], onDelete: Cascade)
  course               Course                @relation(fields: [courseId], references: [id], onDelete: Cascade)
  lastResumedShloka    Shloka?               @relation("ResumeShloka", fields: [lastResumedShlokaId], references: [id], onDelete: SetNull)
  createdAt            DateTime              @default(now())
  updatedAt            DateTime              @updatedAt

  @@unique([userId, courseId])
  @@index([courseId])
  @@index([lastResumedShlokaId])
}

/// Per-user practice and completion state for one canonical shloka.
model UserShlokaProgress {
  id              BigInt                @id @default(autoincrement()) @db.UnsignedBigInt
  userId          BigInt                @db.UnsignedBigInt
  shlokaId        BigInt                @db.UnsignedBigInt
  status          ShlokaProgressStatus  @default(NOT_STARTED)
  practiceCount   Int                   @default(0)
  completedAt     DateTime?
  lastPracticedAt DateTime?
  user            User                  @relation(fields: [userId], references: [id], onDelete: Cascade)
  shloka          Shloka                @relation(fields: [shlokaId], references: [id], onDelete: Cascade)
  createdAt       DateTime              @default(now())
  updatedAt       DateTime              @updatedAt

  @@unique([userId, shlokaId])
  @@index([userId, status, updatedAt])
  @@index([shlokaId])
}

/// Authenticated feedback retained for internal review, not a messaging feature.
model UserFeedback {
  id        BigInt         @id @default(autoincrement()) @db.UnsignedBigInt
  userId    BigInt         @db.UnsignedBigInt
  message   String         @db.Text
  status    FeedbackStatus @default(NEW)
  user      User           @relation(fields: [userId], references: [id], onDelete: Restrict)
  createdAt DateTime       @default(now())
  updatedAt DateTime       @updatedAt

  @@index([status, createdAt])
  @@index([userId, createdAt])
}
```

## 6. Database Constraints

| Constraint | Rule and rationale |
| --- | --- |
| Identity uniqueness | `User.firebaseUid` is unique. Firebase remains the identity issuer; no password or provider-token records are stored in MySQL. |
| Canonical content order | `Section(courseId, sequence)` is unique, and `Shloka(sectionId, number)` is unique. Primary keys are never used as ordering. |
| Section-scoped numbering | `Shloka.number` is both the canonical order and the display number within its section, allowing each section to restart at 1 without redundant state. |
| One text per script | `ShlokaText(shlokaId, script)` is unique. Script views cannot silently overwrite each other. |
| One audio per variant | `ShlokaAudio(shlokaId, variant)` is unique. A variant is a curated content record, not a user preference. |
| One progress record | `UserCourseProgress(userId, courseId)` and `UserShlokaProgress(userId, shlokaId)` are unique. |
| Resource ownership | `LearningResource` must reference exactly one of `courseId`, `sectionId`, or `shlokaId`. Add a MySQL `CHECK` constraint through a Prisma SQL migration because Prisma schema syntax cannot declare this cross-column rule. |
| Referential integrity | Content children cascade when an unpublished/draft parent is deliberately removed. A Guru is restricted from deletion while referenced by a Course. User feedback restricts user removal to preserve controlled audit history. |
| Publication lifecycle | Curated content uses `DRAFT`, `PUBLISHED`, and `ARCHIVED`; archived content is not served to devotees. |
| Soft delete strategy | No generic soft-delete column is introduced. Content is retained through publication status and archived rather than deleted. Any legal/account-erasure policy is deferred to Security Architecture and must be implemented as an explicit workflow, not an implicit ORM filter. |
| Timestamps | All entities use `createdAt`; mutable entities also use `updatedAt`. Timestamps are stored in UTC by the database/runtime convention. |
| Numeric validation | Application services validate non-negative section/resource sequence values, positive section-scoped shloka numbers, positive audio duration when present, and a positive daily target. |

The exact `LearningResource` check constraint is conceptually:

```text
exactly_one(course_id, section_id, shloka_id) = true
```

It must be delivered in a reviewed SQL migration and covered by an integration test.

## 7. Indexing Strategy

| Query pattern | Supporting index or constraint |
| --- | --- |
| Load published course library in display order | `Course(status, displayOrder)` |
| Load a course’s sections in canonical order | `Section(courseId, status, sequence)` |
| Load a section’s shlokas in canonical order | `Shloka(sectionId, status, number)` |
| Resolve a shloka by its number inside a section | unique `Shloka(sectionId, number)` |
| Load a requested script representation | unique `ShlokaText(shlokaId, script)` |
| Resolve an authorized audio variant | unique `ShlokaAudio(shlokaId, variant)` plus `ShlokaAudio(shlokaId, status)` |
| Load resources for an attachable entity | target-specific `(ownerId, status, sequence)` indexes on LearningResource |
| Load a user’s course state | unique `UserCourseProgress(userId, courseId)` |
| Load a user’s progress by state | `UserShlokaProgress(userId, status, updatedAt)` |
| Resolve resume target reference | `UserCourseProgress(lastResumedShlokaId)` |
| Review feedback queue | `UserFeedback(status, createdAt)` |

These indexes are intentionally query-driven. Additional indexes require an observed query pattern or an explain-plan finding; the pilot does not add speculative indexes.

## 8. Future Extensibility

| Future need | Existing extension point | Deliberate deferral |
| --- | --- | --- |
| Meaning courses | A new Course can reference the same spiritual material conceptually without changing the current course hierarchy. Meaning content is introduced as a separate entity only when the product requires it. | No scripture/master-text model now. |
| Memorisation | New practice/memorisation state can extend or sit beside UserShlokaProgress. | No mastery score or memorisation fields now. |
| Divine Names | A future DivineName entity can attach to Shloka through a join relation. | No name-level entities or progress now. |
| Multiple gurus | Course currently has one Guru foreign key; a future CourseGuru join relation can replace it when co-teaching is a real requirement. | No marketplace, follower, or payout data. |
| AI pronunciation | A future attempt/evaluation model can reference User and Shloka. | No recordings, uploads, scores, or evaluation data now. |
| Commentary and meanings | Separate resources or dedicated commentary/meaning entities can attach to Course, Section, or Shloka. | Text representations stay strictly script views. |
| Additional resource types | `ResourceType` can gain controlled enum values. | No generic blob or arbitrary metadata store. |
| Custom pace | A future `CUSTOM` learning pace can be added while `dailyTargetShlokas` already supports a validated value. | The pilot exposes only Slow & Steady and Balanced. |

## 9. Data Dictionary

All primary and foreign keys below are unsigned `BIGINT`. All timestamps are UTC.

### User

| Field | Type | Description |
| --- | --- | --- |
| id | BIGINT | Internal surrogate primary key. |
| firebaseUid | VARCHAR(128) | Stable Firebase subject used to map verified identity. |
| email | VARCHAR(320), nullable | Latest identity email when available; not a login credential. |
| displayName | VARCHAR(160), nullable | User-facing name supplied by identity. |
| photoUrl | VARCHAR(1024), nullable | Identity profile image reference. |
| role | ENUM | Minimal authorization role: devotee or administrator. |
| createdAt / updatedAt | DATETIME | Record lifecycle audit timestamps. |

### UserSettings

| Field | Type | Description |
| --- | --- | --- |
| id | BIGINT | Internal surrogate primary key. |
| userId | BIGINT, unique FK | One-to-one owner; makes settings server-authoritative after sign-in. |
| scriptPreference | ENUM | Preferred text script for presentation. |
| fontScale | INT, nullable | Optional persisted reading-scale preference. |
| createdAt / updatedAt | DATETIME | Record lifecycle audit timestamps. |

### Guru

| Field | Type | Description |
| --- | --- | --- |
| id | BIGINT | Internal surrogate primary key. |
| displayName | VARCHAR(160) | Curated guide name shown with a course. |
| biography | TEXT, nullable | Approved guide biography. |
| imagePath | VARCHAR(1024), nullable | Private/static asset path, not a generated delivery URL. |
| createdAt / updatedAt | DATETIME | Record lifecycle audit timestamps. |

### Course

| Field | Type | Description |
| --- | --- | --- |
| id | BIGINT | Internal surrogate primary key. |
| slug | VARCHAR(160), unique | Stable, human-readable external identifier for routes and administration. |
| title | VARCHAR(255) | Human-readable learning-programme title. |
| shortDescription | VARCHAR(500), nullable | Concise catalogue summary. |
| description | TEXT, nullable | Full approved course description. |
| guruId | BIGINT FK | Single curated guide for Database Design v1. |
| status | ENUM | Draft, published, or archived lifecycle. |
| displayOrder | INT | Canonical catalogue order; not user-customizable. |
| createdAt / updatedAt | DATETIME | Record lifecycle audit timestamps. |

### Section

| Field | Type | Description |
| --- | --- | --- |
| id | BIGINT | Internal surrogate primary key. |
| courseId | BIGINT FK | Parent learning programme. |
| title | VARCHAR(255) | Organisational title such as Nyasa or Dhyanam. |
| description | TEXT, nullable | Optional approved section context. |
| sequence | INT | Canonical order inside the course. |
| isImplicit | BOOLEAN | Identifies the default section used when a course has no natural sections. |
| status | ENUM | Curated publication lifecycle. |
| createdAt / updatedAt | DATETIME | Record lifecycle audit timestamps. |

### Shloka

| Field | Type | Description |
| --- | --- | --- |
| id | BIGINT | Internal surrogate primary key. |
| sectionId | BIGINT FK | Parent organisational section. |
| number | INT | Canonical display number and order inside the section; numbering resets per section. |
| title | VARCHAR(255), nullable | Optional editorial title, not a substitute for shloka text. |
| status | ENUM | Curated publication lifecycle. |
| createdAt / updatedAt | DATETIME | Record lifecycle audit timestamps. |

### ShlokaText

| Field | Type | Description |
| --- | --- | --- |
| id | BIGINT | Internal surrogate primary key. |
| shlokaId | BIGINT FK | Parent canonical shloka. |
| script | ENUM | Text representation, such as Devanagari or English transliteration. |
| content | LONGTEXT | Full text in the selected script; never meaning or commentary. |
| createdAt / updatedAt | DATETIME | Record lifecycle audit timestamps. |

### ShlokaAudio

| Field | Type | Description |
| --- | --- | --- |
| id | BIGINT | Internal surrogate primary key. |
| shlokaId | BIGINT FK | Parent canonical shloka. |
| variant | ENUM | Fixed curated audio mode; only Complete Shloka is published in the pilot. |
| status | ENUM | Independent media publication lifecycle. |
| hlsManifestObjectPath | VARCHAR(1024) | Private GCS path to the source HLS manifest; never a signed URL. |
| synchronizationObjectPath | VARCHAR(1024), nullable | Private path to timing metadata when prepared. |
| durationMs | INT, nullable | Curated media duration for display and validation. |
| createdAt / updatedAt | DATETIME | Record lifecycle audit timestamps. |

### LearningResource

| Field | Type | Description |
| --- | --- | --- |
| id | BIGINT | Internal surrogate primary key. |
| courseId / sectionId / shlokaId | BIGINT FK, nullable | Exactly one owner is set; selects the resource attachment level. |
| type | ENUM | Controlled resource category, not a generic content type registry. |
| status | ENUM | Curated publication lifecycle. |
| title | VARCHAR(255) | Approved resource title. |
| description | TEXT, nullable | Optional learner-facing resource context. |
| resourceLocation | VARCHAR(1024) | Private object path or approved external URI; never a signed URL. |
| sequence | INT | Canonical order among resources for the same owner. |
| createdAt / updatedAt | DATETIME | Record lifecycle audit timestamps. |

### UserCourseProgress

| Field | Type | Description |
| --- | --- | --- |
| id | BIGINT | Internal surrogate primary key. |
| userId | BIGINT FK | User following the course. |
| courseId | BIGINT FK | Course whose plan and resume state are recorded. |
| learningPace | ENUM | Named pilot plan choice: Slow & Steady or Balanced. |
| dailyTargetShlokas | INT | Course-specific daily target; enables future course-specific/custom pace without global settings coupling. |
| lastResumedShlokaId | BIGINT FK, nullable | Safe server-authoritative resume target. |
| createdAt / updatedAt | DATETIME | Record lifecycle audit timestamps. |

### UserShlokaProgress

| Field | Type | Description |
| --- | --- | --- |
| id | BIGINT | Internal surrogate primary key. |
| userId | BIGINT FK | Practising user. |
| shlokaId | BIGINT FK | Canonical learning unit being tracked. |
| status | ENUM | Not started, in progress, or completed learning state. |
| practiceCount | INT | Count of completed practice interactions; not a mastery score. |
| completedAt | DATETIME, nullable | First/most recent canonical completion time as defined by Progress rules. |
| lastPracticedAt | DATETIME, nullable | Most recent practice activity for resume and progress views. |
| createdAt / updatedAt | DATETIME | Record lifecycle audit timestamps. |

### UserFeedback

| Field | Type | Description |
| --- | --- | --- |
| id | BIGINT | Internal surrogate primary key. |
| userId | BIGINT FK | Authenticated feedback author. |
| message | TEXT | User-submitted feedback body. |
| status | ENUM | Internal triage lifecycle. |
| createdAt / updatedAt | DATETIME | Record lifecycle audit timestamps. |

## 10. Database Seed Plan

The seed plan establishes a concrete, minimal dataset for local development, integration tests, and Admin Console onboarding. It is a data plan only; it does not prescribe seed code.

1. **Guru:** create Kusum Guruji with approved biography and image path.
2. **Course:** create `lalita-sahasranama-chanting` titled “Lalita Sahasranama Chanting Course,” assigned to Kusum Guruji and published.
3. **Sections:** create the approved course sequence: Dhyanam, Nyasa, Main Stotram, and Phalashruti. If a later course lacks such divisions, seed one implicit default section instead.
4. **Sample shlokas:** seed three to five representative shlokas in their canonical section-scoped number order.
5. **Text:** seed Devanagari and English Transliteration representations for each sample shloka.
6. **Audio:** seed exactly one published `COMPLETE_SHLOKA` record per sample shloka with private HLS-manifest and timing-metadata paths. Keep Word-by-Word and Line-by-Line records absent or draft.
7. **Resources:** attach one optional curated course introduction resource and, if useful, one shloka-level pronunciation note to demonstrate the one-owner rule.
8. **Users and progress:** seed a development devotee, settings, Slow & Steady course progress, and a small mix of completed/in-progress shloka records. Seed one administrator separately for Content Management tests.

## 11. Design Review

### Improvements Made

- Replaced internal `cuid()` identifiers with unsigned `BIGINT AUTO_INCREMENT` keys across every relational model.
- Removed redundant `Shloka.sequence`; section-scoped `Shloka.number` is now the single canonical shloka order.
- Kept `dailyTargetShlokas` on UserCourseProgress because pace belongs to a user’s relationship with a particular course, not global settings.
- Renamed the vague `locator` field to `resourceLocation` and documented its signed-URL boundary.
- Added Prisma documentation comments, a complete Data Dictionary, and a practical database seed plan.

### Strengths

- Mirrors the actual spiritual learning hierarchy and keeps shloka as the durable learning unit.
- Keeps content, media metadata, user state, and device-only recordings at their correct ownership boundaries.
- Enforces canonical section order and section-scoped shloka numbering in the database, not only in application code.
- Supports curated lifecycle and supplementary resources without creating a generic CMS.
- Preserves a small, practical path for future course types and audio variants without modelling unsupported product features.

### Trade-offs

- `LearningResource` uses three nullable foreign keys plus a database check constraint because MySQL/Prisma lacks a native polymorphic relation with referential integrity. This is simpler and safer than a generic owner-type/owner-id pair.
- One Guru per Course is intentionally simple. A join table is deferred until multiple gurus per course are an actual product requirement.
- The schema represents deferred audio variants so content packaging does not force a later migration, while publication state prevents those variants from appearing in the pilot.
- User progress stores practice activity and completion only; it does not attempt to infer spiritual attainment or memorisation.

### Suggestions Rejected

- **Keeping CUID primary keys:** rejected because this is a centralized MySQL application with no offline ID generation or multi-writer topology; BIGINT keys are smaller and easier to operate.
- **Keeping both Shloka number and sequence:** rejected because the frozen domain makes the number canonical and immutable within a section, so a second ordering field could drift.
- **Moving daily target to UserSettings:** rejected because a learner may follow different courses at different paces; course progress owns that relationship.
- **Replacing LearningResource with a generic owner-type/owner-id model:** rejected because it would lose relational foreign-key integrity and make administration harder to understand.
- **Creating extra entities for meanings, scripture, divine names, recordings, or multiple gurus:** rejected because no pilot use case requires them.

### Assumptions

- The approved pilot publishes `COMPLETE_SHLOKA` audio only.
- Default/implicit sections are created as real Section rows for courses without natural sections.
- Application services enforce publication visibility, daily-target validity, and owner authorization in addition to the constraints listed here.
- The database holds metadata only; signed URLs, manifests, keys, and media bytes remain outside MySQL.

### Recommendations Before Implementation

1. Approve the controlled enum vocabulary before the first Prisma migration, especially script labels and resource types.
2. Implement and test the `LearningResource` one-owner check constraint in the initial SQL migration.
3. Seed a minimal Lalita Sahasranama dataset with one Guru, one Course, real/implicit Sections, Devanagari and English Transliteration text, and only published Complete Shloka audio where available.
4. Validate course-loading and progress-query explain plans against representative pilot data before adding indexes beyond this document.

### Final Assessment

| Dimension | Rating | Assessment |
| --- | --- | --- |
| Simplicity | 9/10 | Small set of concrete entities with no generic CMS or speculative tables. |
| Maintainability | 9/10 | Clear ownership, documentation, constraints, and predictable naming. |
| Extensibility | 8/10 | Supports the next credible extensions without modelling them prematurely. |
| Domain Alignment | 10/10 | Encodes guided practice, curated content, section-scoped shloka progression, and device-local recordings. |
| Performance | 9/10 | Compact relational keys and query-led indexes fit the pilot and early production workload. |

**Overall architecture score: 9.0/10.** The design is ready to guide the initial Prisma migration after enum vocabulary and the resource-owner check constraint are approved.
