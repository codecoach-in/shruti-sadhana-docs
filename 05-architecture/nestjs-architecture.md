# NestJS Architecture v1

**Status:** Proposed for Architecture Freeze v1
**Version:** 1.0
**Scope:** Phase 0 Pilot backend

## 1. Purpose

This document defines the internal architecture of the Shruti Sadhana NestJS backend. It establishes the module boundaries, dependency direction, service ownership, and operational conventions required to support the approved Flutter clients and the separate internal Admin Console.

The backend is a modular monolith. It provides application APIs, verifies identity, applies authorization, maintains canonical application data, and authorizes secure media playback. It does not define database schemas, REST endpoint contracts, implementation code, Prisma models, migrations, or media-packaging details.

## 2. Technology Stack

| Concern | Technology | Architectural role |
| --- | --- | --- |
| Framework | NestJS | Modular HTTP application composition and dependency injection. |
| Language and runtime | TypeScript in strict mode on Node.js LTS | Safe, explicit service and domain contracts. |
| Database | MySQL | Canonical relational application data. |
| ORM | Prisma | Persistence adapter and transaction boundary; never a domain model exposed to callers. |
| Authentication | Firebase Authentication and Firebase Admin SDK | Client identity issuance and server-side ID-token verification. |
| Validation | `class-validator` and `class-transformer` | Transport DTO validation and transformation at API boundaries. |
| API documentation | OpenAPI / Swagger | Generated, versioned API reference from the eventual API contract. |
| Configuration | `@nestjs/config` | Typed, validated environment configuration. |
| Testing | Jest | Unit, integration, and contract test execution. |
| Object storage | Google Cloud Storage | Private source and packaged media storage, accessed only through infrastructure adapters. |
| CDN | Google Cloud CDN | Direct delivery of authorized protected media; never proxied through NestJS. |

## 3. Architecture Principles

| Principle | Decision and rationale |
| --- | --- |
| Modular monolith | Keep all pilot backend modules in one deployable application to minimize operational overhead while preserving explicit ownership. |
| Layered boundaries | Controllers, application services, domain contracts, and infrastructure adapters separate transport and vendor concerns from application rules. |
| Explicit ownership | Each module owns one coherent capability and its use cases; shared code contains only stable cross-cutting primitives. |
| Firebase identity, backend policy | Firebase proves identity. NestJS maps that identity to an application user and remains the authorization decision point. |
| Server authority | The backend is the canonical source of truth. Flutter clients retain transient UI state and local caches only; server-authoritative state prevails during synchronization. |
| Direct media delivery | The backend authorizes playback and returns short-lived signed URLs or rewritten signed manifests. It never proxies, streams, caches, or transforms media bytes. |
| Curated-content boundary | Only administrator-managed content enters the catalogue. Devotees have no content creation, edit, or upload capability. |
| Simple synchronous workflows | The pilot uses request/response application services. It introduces no microservices, queues, event bus, scheduled jobs, workers, or background processing. |
| Environment abstraction | All external integrations have local/test and cloud-backed adapters so development and tests do not depend on production services. |
| Secure by default | Validate inputs at entry, keep secrets server-side, use least-privilege infrastructure access, and log diagnostic data without sensitive tokens or media URLs. |

## 4. Application Layers

| Layer | Responsibility | May contain | Must not contain |
| --- | --- | --- | --- |
| Controllers | Receive HTTP requests, apply transport validation, invoke an application service, and translate service outcomes into HTTP responses. | DTOs, guards, request context, response mapping, OpenAPI metadata. | Business rules, Prisma access, Firebase Admin calls, or signing logic. |
| Application Services | Coordinate use cases, transactions, authorization checks, repository contracts, and infrastructure-facing domain ports. | Use-case orchestration and feature-level policies. | HTTP concerns, raw Prisma models, or Cloud CDN transport logic. |
| Domain | Express stable business concepts, rules, failures, and repository/service contracts. | Entities, value objects, policy interfaces, domain exceptions. | NestJS decorators, Prisma, Firebase, GCS, HTTP, or environment access. |
| Infrastructure | Implement domain contracts and adapt external systems. | Prisma repositories, Firebase verifier, GCS/CDN signing adapter, configuration adapter, logger adapter. | Controller behaviour or feature-specific presentation decisions. |
| Shared | Supply small, stable cross-cutting primitives. | Request correlation, error vocabulary, result types, logging interfaces, common configuration validation. | Feature workflows, catch-all utility code, or module-to-module shortcuts. |

The NestJS composition root assembles modules and binds infrastructure implementations to domain contracts. It is the only place where environment-specific drivers are selected.

## 5. Module Architecture

Recommended modules are logical ownership boundaries, not a requirement to create empty folders or overly granular NestJS modules. Closely related responsibilities may be combined when that preserves a single clear owner.

| Module | Owns | Does not own |
| --- | --- | --- |
| Authentication | Firebase ID-token verification, application-session request context, and sign-out/session invalidation integration where applicable. | User profile data or authorization policy beyond verified identity. |
| Users | Application-user identity mapping and user-owned profile data. | Firebase credential issuance or course content. |
| Courses | Read access to curated catalogue, course structure, sections, shlokas, guide data, and publication availability. | Admin user interfaces, media delivery, or canonical user progress. |
| Progress | Shloka-level learning progress, learning-plan pace, resume state, and related progress-oriented use cases. | Client presentation metrics or media playback. |
| Playback Authorization | Eligibility check, playback-session authorization, and delivery of a short-lived signed URL or signed/re-written HLS manifest. | Streaming, proxying, media packaging, encryption, or CDN caching. |
| Content Management | Backend capabilities used by the separate internal Admin Console to manage curated content metadata and publication state. | The Admin Console application itself, public client workflows, devotee uploads, or practice recordings. |
| Settings | Authenticated user preferences that are server-authoritative after sign-in. | Pre-auth device-only preferences and client layout state. |
| Feedback | Authenticated feedback submission and controlled retrieval for administrators if required. | Messaging, community, or notification systems. |
| Health | Liveness/readiness checks and dependency health reporting appropriate to the environment. | Product analytics or user-facing status screens. |
| Configuration | Typed configuration validation and environment driver selection. | Feature-specific policy decisions. |
| Shared | Cross-cutting primitives used by more than one module. | Domain ownership for a business capability. |

Practice recordings remain private user artifacts on the client device. They are explicitly outside Content Management, Courses, media storage, and the pilot backend API surface.

## 6. Dependency Rules

### 6.1 Allowed dependencies

```text
Controllers
     │
     v
Application Services ─────> Domain contracts
     │                            ▲
     v                            │
Infrastructure adapters ──────────┘
     │
     v
Prisma / Firebase Admin / GCS signing / Cloud CDN

Composition root ──> all application modules and infrastructure bindings
Shared primitives <── all layers, where stable and cross-cutting
```

| From | May depend on | Reason |
| --- | --- | --- |
| Controller | Its application service, transport DTOs, guards, shared HTTP/error primitives | Keeps HTTP concerns at the edge. |
| Application service | Domain contracts, domain rules, shared primitives | Coordinates a use case without knowing external implementation details. |
| Domain | Domain and stable shared abstractions | Preserves framework- and vendor-independent rules. |
| Infrastructure | Domain contracts and shared primitives | Implements the ports required by the application. |
| Module | Another module’s explicit public application/domain contract | Allows intentional collaboration without importing internals. |
| Composition root | All modules and adapters | Performs dependency binding and environment selection. |

### 6.2 Forbidden dependencies

- Controllers must not access Prisma, Firebase Admin, Google Cloud Storage, CDN signing, or environment variables directly.
- Domain code must not import NestJS, Prisma, Firebase, Google Cloud, HTTP, or OpenAPI types.
- A module must not access another module’s Prisma repository or infrastructure implementation directly.
- Public devotee-facing controllers must not import or expose Content Management workflows.
- Playback Authorization must not return raw storage credentials, proxy media responses, or read/media-stream bytes.
- Shared code must not depend on a feature module.

## 7. Authentication

Firebase Authentication is the identity provider. The Flutter client obtains a Firebase ID token through Google Sign-In and submits it with authenticated requests. The Authentication module uses Firebase Admin SDK to verify the token server-side and constructs a trusted request identity.

The Users module maps the verified Firebase subject to the Shruti Sadhana application user. The mapping is created or retrieved through an application service, not in a controller or guard. Firebase claims and tokens remain identity inputs; the application database owns user profile and product-specific state.

Authentication verification is applied consistently at the controller boundary. Public pre-auth flows are limited to the approved guided-demo and any explicitly public health endpoint. All post-commitment application APIs require a verified identity.

## 8. Authorization

Authorization is a backend responsibility applied inside application services before protected data is read or changed. Controllers and Flutter clients may route users, but they never determine access policy.

| Boundary | Decision |
| --- | --- |
| Devotee access | An authenticated user may access only permitted published content and their own profile, plan, settings, progress, feedback, and playback authorization. |
| Administrator access | Administrator-managed content operations are available only through the separate Admin Console’s backend surface and require explicit administrator authorization. |
| Ownership | User-owned operations enforce the authenticated application-user identity; identifiers supplied by the client are never sufficient authorization. |
| Playback | Playback Authorization checks authenticated identity, content publication/availability, and future entitlement rules before generating short-lived access. |
| Future extensibility | Policy contracts may accommodate additional roles or paid entitlements later without changing the Firebase identity boundary. The pilot does not implement those product capabilities now. |

The pilot keeps the role model intentionally small: authenticated devotee and authorized administrator. Detailed persistence representation is deferred to Database Design.

## 9. Repository Architecture

Repository contracts belong to the domain/module that owns the data use case. Prisma is used only by infrastructure repository implementations; it is not a global data-access service and its generated models do not leave the infrastructure boundary.

| Concern | Responsibility |
| --- | --- |
| Domain repository contract | Names the data operations needed by one owning module in application terms. |
| Prisma repository | Executes MySQL persistence, manages query/mutation mapping, and participates in application-service transaction boundaries. |
| Domain mapping | Translates Prisma persistence representations to domain entities/value objects at the infrastructure boundary. |
| Transaction ownership | The application service owns a use case’s transaction boundary; repositories do not coordinate cross-module workflows independently. |
| Read models | A module may define purpose-specific query contracts when needed, without leaking database tables or creating generic repositories. |

No module may bypass its owning repository through direct Prisma access. Database schema, relation design, indexes, and migrations remain deferred to Database Design.

## 10. Service Layer

Application services are named for a use case and keep one clear orchestration responsibility. They call repositories and adapters through contracts, enforce business validation, and return domain-safe results or failures.

| Service area | Responsibilities |
| --- | --- |
| Authentication service | Verify client identity through the authentication adapter and establish trusted request identity. |
| User service | Resolve application-user mapping and manage authenticated user profile operations. |
| Course service | Provide published, curated course and lesson information appropriate to the caller. |
| Progress service | Read and update shloka-level progress, manage the current learning-plan pace, and resolve a safe resume target. |
| Playback-authorization service | Validate access, request short-lived media access from the signing adapter, and return an authorized playback response. |
| Content-management service | Manage curated content metadata and publication state for authorized administrators; it has no public devotee workflow. |
| Settings service | Reconcile authenticated user preferences with server-authoritative profile settings. |
| Feedback service | Accept validated feedback and retain it through its module repository. |
| Health service | Aggregate safe liveness/readiness status without revealing secrets or internal implementation details. |

## 11. Media Authorization

The backend is an authorization control plane, not a media data plane.

```text
Flutter client -> Playback Authorization controller
                 -> Playback-authorization service
                 -> user/content access checks
                 -> GCS/CDN signing adapter
Flutter client <- short-lived signed URL or signed/re-written HLS manifest
Flutter client -> Cloud CDN -> protected HLS segments and key material
```

For HLS, the backend may obtain a private manifest and return an authorized rewritten manifest containing short-lived signed segment and key URLs, as validated by the media security POC. The client then retrieves all media bytes directly from Cloud CDN. The backend must not proxy a manifest’s segment bytes, stream media, cache protected media, expose GCS credentials, or retain reusable signing material.

The Media Architecture document will define packaging, manifest format, signing expiry, key handling, cache behaviour, and operational details. Security Architecture will define the associated threat model and secret-handling controls.

## 12. Validation Strategy

Validation occurs in three distinct stages:

| Stage | Owner | Responsibility |
| --- | --- | --- |
| Transport validation | Controllers and DTOs | Use `class-transformer` and `class-validator` to transform and reject malformed request shapes at the boundary. |
| Business validation | Application services and domain rules | Enforce ownership, allowed state transitions, publication state, and feature policy. |
| Persistence/infrastructure validation | Repositories and adapters | Translate constraint conflicts, provider failures, and unavailable dependencies into stable application failures. |

DTO validation protects the API surface but does not replace business validation. Controllers translate only recognized application outcomes; they do not expose provider, Prisma, or Firebase error details.

## 13. Exception Handling

A global exception filter maps known application failures to consistent HTTP error responses and catches unexpected failures at the transport edge. Domain exceptions describe meaningful business failures without HTTP coupling; application services return or throw these deliberately. Infrastructure adapters translate vendor-specific failures before they cross into application services.

| Failure type | Response and logging rule |
| --- | --- |
| Validation failure | Return a stable client-safe validation response; log only diagnostic context required for investigation. |
| Authentication failure | Return an unauthenticated response without revealing token details. |
| Authorization/ownership failure | Return a forbidden or not-found-safe outcome according to the future API contract; do not reveal protected resource existence unnecessarily. |
| Expected domain conflict | Return a stable conflict or business-rule outcome. |
| Dependency failure | Return a safe unavailable/internal outcome and emit structured diagnostic logs. |
| Unexpected failure | Return a generic safe error, preserve correlation information, and log the full server-side diagnostic context with redaction. |

Structured logs include timestamp, severity, module, operation, correlation identifier, and safe error classification. They must never include Firebase tokens, credentials, signing keys, raw signed URLs, or private recording/media data.

## 14. Configuration

`@nestjs/config` is the configuration boundary. Configuration is loaded once at application bootstrap, parsed into typed settings, validated before the application starts serving requests, and injected through configuration contracts rather than read ad hoc from environment variables.

| Environment | Configuration expectation |
| --- | --- |
| Local | Uses local/mock adapters for authentication, persistence, storage, media signing, and observability where practical; requires no production credentials. |
| Development | Uses isolated shared development resources when integration with cloud services is required. |
| Staging | Uses release-like, isolated Firebase, MySQL, GCS, CDN, and backend configuration for pre-release verification. |
| Production | Uses least-privilege production service identities and production endpoints. |

Configuration may include endpoint and project identifiers, feature-safe logging levels, and driver selection. Secrets, service-account material, and signing keys are provided by the deployment environment and never committed to source control.

## 15. Dependency Injection

NestJS modules provide the composition mechanism. Domain contracts are represented by injection tokens; infrastructure adapters bind to those tokens in the composition root or dedicated infrastructure module. This keeps application services independent of Prisma, Firebase Admin, GCS, and CDN implementations.

| Provider type | Recommended scope |
| --- | --- |
| Configuration, Prisma client, Firebase verifier, storage/signing adapter, logger, health adapter | Singleton application scope. |
| Repositories and application services | Singleton application scope when stateless. |
| Request identity/context | Request scope or explicit request-context parameter, kept narrow to avoid turning all services request-scoped. |
| Controllers | NestJS default singleton scope; they remain stateless. |

No service locator, global mutable singleton, or runtime module discovery is introduced. Tests replace adapter bindings at the module composition boundary.

## 16. Observability

Observability is deliberately lightweight for the pilot and supports diagnosis without introducing external event infrastructure.

| Concern | Architecture boundary |
| --- | --- |
| Logging | Structured, redacted application logs behind a logger interface; levels vary by environment. |
| Health checks | Health module exposes liveness and readiness checks appropriate to deployment orchestration; dependency details are not exposed publicly. |
| Metrics hooks | Module-level timing/counter interfaces provide future instrumentation seams without requiring a metrics platform in the pilot. |
| Audit boundaries | Log security-relevant administrator actions, authentication failures, playback-authorization decisions, and content-publication changes with actor and correlation identifiers—not sensitive payloads or credentials. |

Analytics, queues, scheduled reporting, and operational dashboards are not introduced by this architecture.

## 17. Testing Strategy

| Test level | Focus | Environment |
| --- | --- | --- |
| Unit tests | Domain rules, application services, authorization decisions, DTO-to-service translation, and adapter contracts using mocks/fakes. | Local/Jest. |
| Integration tests | Prisma repositories, module wiring, global validation/exception handling, and selected controller-to-service paths using isolated drivers. | Local or isolated development services. |
| Contract tests | Verify the eventually documented API behaviour and stable error shapes between Flutter clients/Admin Console and backend. | Controlled test environment. |

Tests must not require production Firebase credentials, production media, or live CDN access. Local drivers and adapter fakes cover ordinary execution; staging validates real integration separately.

## 18. Non-goals

- Microservices, Redis, message queues, event buses, Kafka, RabbitMQ, Pub/Sub, search engines, GraphQL, WebSockets, background workers, and scheduled jobs.
- Database schema, Prisma models, SQL, migrations, endpoint inventory, or request/response schemas.
- The Admin Console user interface, deployment, or frontend architecture.
- Public content upload, devotee content editing, user-generated learning content, or moderation workflows.
- Storage of, synchronization of, or backend APIs for private user practice recordings.
- Backend media streaming, media proxying, transcoding, HLS packaging, or CDN cache management.
- Offline downloads, AI pronunciation, payments, subscriptions, community features, notifications, or advanced analytics.

## 19. Open Items

| Future document | Deferred responsibility |
| --- | --- |
| Database Design | MySQL schema, Prisma model boundaries, indexes, transactions, and migration strategy. |
| API Contracts | Resource contracts, endpoint shapes, versioning, authorization error semantics, and OpenAPI detail. |
| Media Architecture | HLS packaging, manifests, signed URL lifecycle, timing metadata, cache policy, and content pipeline operations. |
| Security Architecture | Threat model, role and access-control details, token/secret handling, signing controls, redaction, and incident boundaries. |

## Review Checklist

- [ ] The backend remains a modular monolith with no premature distributed infrastructure.
- [ ] Controllers, application services, domain contracts, and infrastructure adapters have clear responsibilities and dependency direction.
- [ ] Firebase establishes identity while NestJS maps users and owns authorization decisions.
- [ ] Content Management is limited to backend capabilities for the separate internal Admin Console.
- [ ] Devotees cannot create, edit, upload, or administer learning content.
- [ ] Practice recordings remain private client-side artifacts outside the backend and Content Management.
- [ ] Playback authorization returns short-lived signed access while Cloud CDN delivers all media bytes directly.
- [ ] Prisma is isolated to infrastructure repositories and database details remain deferred.
- [ ] Configuration, logs, health checks, and tests support local, development, staging, and production environments.
- [ ] The document defers only Database Design, API Contracts, Media Architecture, and Security Architecture.

**No unresolved architectural blockers.**
