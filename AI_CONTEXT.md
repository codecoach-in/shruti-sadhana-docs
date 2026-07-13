# AI_CONTEXT.md

# Shruti Sadhana

Purpose: Quick briefing for AI assistants. Contains accepted decisions, implementation context, and documentation hierarchy.

## Start Here

Root repository navigation:
-> [PROJECT_INDEX.md](PROJECT_INDEX.md)

Stable Codex guidance:
-> [00-project/AGENT_GUIDE.md](00-project/AGENT_GUIDE.md)

Accepted decision summary:
-> [00-project/DECISION_LOG.md](00-project/DECISION_LOG.md)

## Documentation Hierarchy

The repository is structured to represent the natural lifecycle of product development:

1. **Vision & Context** -> [00-project/project-context.md](00-project/project-context.md) & [00-project/AGENT_GUIDE.md](00-project/AGENT_GUIDE.md)
2. **Product Requirements** -> [01-product/PRD.md](01-product/PRD.md) & [01-product/MVP-SCOPE.md](01-product/MVP-SCOPE.md)
3. **Features Inventory** -> [02-features/FEATURE_INDEX.md](02-features/FEATURE_INDEX.md)
4. **UX & User Journeys** -> [03-ux/INDEX.md](03-ux/INDEX.md)
5. **Functional Requirements** -> [04-frd/INDEX.md](04-frd/INDEX.md)
6. **System Architecture** -> [05-architecture/INDEX.md](05-architecture/INDEX.md)
7. **Technical Design** -> [06-technical-design/INDEX.md](06-technical-design/INDEX.md)
8. **Content Models** -> [07-content/INDEX.md](07-content/INDEX.md)
9. **Decision Records (ADRs)** -> [08-decisions/INDEX.md](08-decisions/INDEX.md)
10. **Research & Experiments** -> [09-research/INDEX.md](09-research/INDEX.md)
11. **Assets & Mockups** -> [10-assets/INDEX.md](10-assets/INDEX.md)

## Project Summary

Product: Sanskrit Stotram Learning, Practice & Parayana Platform

Primary platforms:
- Android native app (primary).
- Flutter Web for iPhone/iPad users in Kusum Guruji's batch.
- Native iOS postponed.

Status: Phase 0 Pilot Release (Frozen)

Core Journey:
Learn -> Practice -> Parayana

## Accepted Product Decisions

- Flutter Frontend
- Flutter Web for iPhone/iPad users during pilot/MVP
- Native iOS application postponed
- Android first native release platform
- NestJS Backend
- MySQL Database
- Firebase / Google Sign-In Authentication (no guest mode, mobile OTP deferred)
- Google Cloud Infrastructure
- Personalized Puja Ghar deferred post-pilot
- Chanting Recording & Playback (FRD-015)
- Slow/Medium Speed Audio, Subscriptions, Donations, Referrals, and Puja Ghar deferred
- AI Pronunciation Feedback Deferred to future phase
- Offline downloads out of scope
- Curated content instead of user-generated content
- Gold Standard recordings treated as premium intellectual property
- Product copy convention: Reserve "Sadhana" for brand name (Shruti Sadhana)
- State Management: Riverpod
- Navigation: GoRouter (Named routes centrally configured)
- Dependency Injection: Riverpod Providers only (no GetIt/Injectable)
- Networking: Dio (REST endpoints, Repository pattern with DTO mapping in Data layer)
- Local Storage: preferences, access tokens, and resume states only
- Environment Config: Local, Staging, Production via `--dart-define` compiler flags
- Flutter architecture: Feature-First folder structures combined with Lightweight Clean layers (Presentation/Domain/Data)
- Coding Convention: `snake_case` files, `PascalCase` classes, and suffix rules (`Screen`, `Repository`, `Model`) defined in system overview
- Audio Architecture: Decoupled Playback Engine, Synchronization Engine, Recording Engine, and coordinating Session Controller. UI consumes highlight events. Implementation timing drift/formats/caching are deferred.



## Current MVP Modules

- Onboarding & Google Sign-In
- Home Dashboard
- Learning Library & Course Detail (Lalita Sahasranama only)
- Lesson Experience (Devanagari, English Transliteration, Normal-speed audio streaming)
- Chanting Recording & Playback (FRD-015)
- Progress Tracking (no streaks/badges)
- Simple In-app Feedback Form & WhatsApp Link


## Scope Authority

Product Scope:
-> [01-product/PRD.md](01-product/PRD.md)

Included / Excluded Features:
-> [01-product/MVP-SCOPE.md](01-product/MVP-SCOPE.md)

Feature Inventory:
-> [02-features/FEATURE_INDEX.md](02-features/FEATURE_INDEX.md)

Detailed Behaviour:
-> FRD Documents

## Information Architecture

- **IA Freeze**: The Phase 1 Information Architecture is frozen.
- **Finalized Hierarchy**: All implementation discussions, Flutter routes, and designs must follow the screen inventory and navigation flows in [03-ux/README.md](03-ux/README.md).
- **Naming Rule**: Always use `SCR-xxx` identifiers when referring to screens (e.g., SCR-005 Home, SCR-009 Learning Session).
- **Stability**: Do not introduce new screens or modify navigation paths unless a confirmed engineering blocker is encountered.

## Guidelines For AI Assistants

1. Read [PROJECT_INDEX.md](PROJECT_INDEX.md) before navigating the repository.
2. Read [00-project/AGENT_GUIDE.md](00-project/AGENT_GUIDE.md) before making repository changes.
3. Read [00-project/project-context.md](00-project/project-context.md) before making product recommendations.
4. Treat [01-product/PRD.md](01-product/PRD.md) as the source of product requirements.
5. Treat [01-product/MVP-SCOPE.md](01-product/MVP-SCOPE.md) as the source of scope boundaries.
6. Treat [02-features/FEATURE_INDEX.md](02-features/FEATURE_INDEX.md) as the source of feature inventory.
7. Do not treat brainstorming notes as accepted requirements.
8. Prefer existing patterns and documented decisions.
9. Refer to [03-ux/README.md](03-ux/README.md) and [05-architecture/system-overview.md](05-architecture/system-overview.md) for canonical screen routing and identifiers (`SCR-xxx`).
10. Adhere strictly to the project directories (`lib/app`, `core`, `shared`, `features`), Clean layers (`presentation/`, `domain/`, `data/`), and naming/suffix rules defined in [05-architecture/system-overview.md](05-architecture/system-overview.md).


