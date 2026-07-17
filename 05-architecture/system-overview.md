# System Architecture

**Status:** Proposed for Architecture Freeze v1
**Scope:** Phase 0 Pilot

## 1. Purpose

This document defines the system-level architecture for Shruti Sadhana. It describes the major runtime components, their boundaries, and the key system-wide data flows. It preserves the frozen product, UX, and ADR decisions; it does not define code structure, database schemas, endpoint contracts, or player internals.

The detailed architecture sequence is:

1. This system overview
2. Flutter architecture
3. NestJS architecture
4. Database design
5. API contracts
6. Media architecture
7. Security architecture
8. Implementation roadmap

## 2. Scope and Constraints

The pilot supports the existing Lalita Sahasranama cohort with a Flutter Android application as the primary client and a responsive Flutter Web client for iPhone and iPad users. The system provides Google sign-in, curated course content, secure online audio playback, local chanting recordings, preferences, and learning progress.

The following decisions constrain this architecture:

- Android is the primary native release; iOS/iPad access is through the responsive web client ([ADR-001](../08-decisions/ADR-001-android-first.md), [ADR-002](../08-decisions/ADR-002-flutter-web.md)).
- Authentication uses Firebase Authentication with Google Sign-In.
- Course content is curated and managed through a closed content pipeline; there are no public content-upload paths ([ADR-006](../08-decisions/ADR-006-curated-content.md)).
- Protected media is delivered directly from CDN-backed private storage. The application backend authorizes playback but does not proxy audio bytes ([ADR-003](../08-decisions/ADR-003-secure-media.md)).
- Downloading media for offline use is out of scope ([ADR-004](../08-decisions/ADR-004-no-offline-downloads.md)).
- Infrastructure integrations must support a local driver and a production driver ([ADR-007](../08-decisions/ADR-007-environment-abstraction.md)).

## 3. System Context

```text
                         +-----------------------+
                         | Firebase Authentication |
                         | Google Sign-In          |
                         +-----------+-----------+
                                     |
                                     v
+-------------------+       +--------+---------+       +------------------+
| Flutter clients   | HTTPS | NestJS application| SQL   | MySQL            |
| Android           +------>+ API and authz      +------>+ application data |
| Responsive Web    |<------+                     |<------+                  |
+---------+---------+       +--------+---------+       +------------------+
          |                            |
          | HTTPS playback manifest    | reads/writes
          v                            v
+---------+----------------------------+-------------------------------+
| Google Cloud media platform                                           |
| Private GCS source content -> Cloud CDN -> encrypted HLS segments    |
+-----------------------------------------------------------------------+
```

The client communicates only with the NestJS application for application data and playback authorization. Once the backend returns an authorized, rewritten HLS manifest, the media player retrieves its encrypted segments directly from Cloud CDN. This keeps high-bandwidth traffic outside the application service.

## 4. Major Components

| Component | Responsibility | Does not own |
| --- | --- | --- |
| Flutter clients | Render the frozen user experience, obtain Firebase credentials, request application data, play authorized HLS streams, retain permitted local state, and record a user’s chant. | Content authorization policy, canonical progress, or media files. |
| Firebase Authentication | Authenticate the user through Google Sign-In and issue identity tokens. | Application profiles, roles, course access, or progress. |
| NestJS application | Verify Firebase identity tokens; manage user profiles, catalog, learning plans, progress, feedback, and playback authorization; return application APIs and rewritten manifests. | High-bandwidth delivery of HLS segments or long-term media storage. |
| MySQL | Store canonical relational application data and curated content metadata. | Audio files, HLS segments, or client session state. |
| Google Cloud Storage | Hold private source media, generated HLS packages, synchronization metadata, and related immutable content assets. | Authentication, user progress, or public listing of protected assets. |
| Cloud CDN | Deliver authorized encrypted HLS segments and other approved static content at scale. | User authentication decisions or application business rules. |
| Content operations | Prepare, validate, package, and publish approved course content and audio packages. | Public user uploads or runtime client content editing. |

## 5. Trust and Data Boundaries

### 5.1 Identity and application access

Firebase establishes user identity. The Flutter client sends the resulting Firebase ID token to the NestJS application. NestJS verifies that token server-side and applies Shruti Sadhana’s application rules before returning protected data or playback access.

Firebase identity alone does not grant content access. The backend remains the policy-enforcement point for user state and future eligibility rules.

### 5.2 Application data

MySQL is the canonical store for user profiles, preferences after sign-in, learning plans, shloka-level progress, course structure, publication state, and feedback. The client may retain a small local copy of preferences and resume information to improve startup and recover from transient network loss; the backend remains authoritative after authentication.

### 5.3 Media and synchronization data

Audio is packaged as encrypted HLS and stored privately. Timing metadata is delivered as content metadata and is consumed by the client’s future synchronization engine to map playback position to the frozen learning-session UI. Detailed timing formats and player behaviour are deferred to the Media and Flutter Architecture documents.

### 5.4 User chanting recordings

Pilot recordings are created and played back on the user’s device. They are not part of the curated media pipeline and are not uploaded, shared, or used for evaluation in this phase. Exact platform storage and retention behaviour belongs to the Flutter Architecture document.

## 6. Key System Flows

### 6.1 Sign-in and session establishment

```text
Flutter client -> Firebase Authentication: Google Sign-In
Firebase Authentication -> Flutter client: Firebase ID token
Flutter client -> NestJS application: authenticated API request
NestJS application -> Firebase Authentication: verify token
NestJS application -> MySQL: load or create application profile
NestJS application -> Flutter client: application session and profile data
```

The guided demo remains available before the commitment point described in the frozen product scope. All post-demo application access requires successful sign-in.

### 6.2 Content and progress

```text
Flutter client -> NestJS application: request course, lesson, plan, or progress
NestJS application -> MySQL: read or update canonical application data
NestJS application -> Flutter client: typed application response
```

The permanent learning unit is the shloka. A learning session is a temporary presentation container, not the unit used to calculate canonical progress.

### 6.3 Secure audio playback

```text
Flutter client -> NestJS application: request authorized lesson playback
NestJS application -> MySQL: validate user and lesson availability
NestJS application -> GCS/CDN signing service: obtain short-lived asset access
NestJS application -> Flutter client: rewritten, short-lived HLS manifest
Flutter client -> Cloud CDN: fetch encrypted HLS segments and key material
Cloud CDN -> Flutter client: protected media bytes
```

The rewritten manifest uses short-lived signed URLs because native and browser HLS subrequests cannot reliably carry the client’s authorization header. The backend may process a manifest but must not proxy media segments. See the validated [media security POC](../09-research/media-security-poc/Executive-Summary.md).

### 6.4 Content publishing

```text
Content operations -> packaging process: approved recordings and verified text
Packaging process -> GCS: private encrypted HLS package and metadata
Content operations -> NestJS application/MySQL: publication metadata and availability
NestJS application -> Flutter client: published catalog and lesson configuration
```

Only curated, approved content may enter this flow.

## 7. Caching and Offline Boundary

The pilot does not offer offline downloads, offline lessons, or persistent media libraries. A user must be online to receive playback authorization and obtain the protected stream.

Android may use a bounded, encrypted, player-managed cache only when it is technically required to provide smooth streaming. This cache is an implementation detail with the following limits:

- It may contain only already-authorized HLS segments needed for near-term playback and transient network resilience.
- It must be size- and retention-bounded, encrypted at rest where the platform supports it, and evicted automatically by the player or app.
- It must not expose a user-visible download, make a lesson intentionally available offline, or bypass fresh playback authorization for a later session.
- The responsive web client must not depend on equivalent persistent caching behaviour.

This clarification permits reliable playback without changing the decision in ADR-004. Cache policy and platform implementation will be specified in the Flutter and Media Architecture documents.

## 8. Deployment Environments

| Environment | Purpose | Required external services |
| --- | --- | --- |
| Local | Deterministic development and automated tests. | No cloud services; use local/mock auth, storage, media, and data drivers as appropriate. |
| Staging | Integration validation before release. | Isolated Firebase, database, storage, CDN, and backend resources. |
| Production | Pilot user traffic. | Production Google Cloud infrastructure and Firebase project. |

Production secrets, service credentials, signing keys, and environment URLs must not be committed to source control. Configuration mechanics are deferred to the relevant implementation architecture documents.

## 9. Explicit Non-Goals for the Pilot

- Microservices, event buses, event sourcing, CQRS, and separate media-streaming services.
- Backend proxying of audio segments.
- Offline downloads, offline-first sync, or persistent media libraries.
- Native iOS application delivery.
- Public creator uploads, user-generated course content, or content moderation workflows.
- AI pronunciation scoring, subscriptions, payments, notifications, and advanced analytics.

## 10. Open Design Work

The following items are intentionally deferred, not undecided product work:

| Next document | Will define |
| --- | --- |
| Flutter Architecture | Feature structure, state, routing, client storage, recording lifecycle, player integration, and Android cache mechanics. |
| NestJS Architecture | Module boundaries, token verification, authorization services, content operations boundary, and local/production drivers. |
| Database Design | Relational entities, ownership, constraints, and migrations. |
| API Contracts | Request/response schemas, error conventions, and versioning. |
| Media Architecture | HLS packaging, manifest rewriting, metadata, cache behaviour, expiry, and operational pipeline. |
| Security Architecture | Threat model, token and signing-key handling, access controls, logging, and incident boundaries. |

## 11. Related Documents

- [Project context](../00-project/project-context.md)
- [Phase 0 pilot scope](../01-product/MVP-SCOPE.md)
- [Architecture decisions](../08-decisions/INDEX.md)
- [Media security POC](../09-research/media-security-poc/INDEX.md)
- [Audio synchronization placeholder](audio-synchronization.md)
