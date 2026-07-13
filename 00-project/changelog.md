# Changelog

All notable changes to the Shruti Sadhana documentation and codebase will be documented in this file.

## [Unreleased]

### Updated (Audio & Sync Freeze - 2026-07-13)
- **Audio Architecture Specs**: Documented the boundary separation between Playback Engine, Synchronization Engine, Recording Engine, and coordinating Session Controller in `05-architecture/system-overview.md`.
- **Audio Sync Flow**: Added a synchronization decision table detailing UI/engine boundaries and mapped the high-level data stream flow in `system-overview.md`.
- **AI Assist Guidelines**: Updated `AI_CONTEXT.md` to note the frozen audio architecture and deferred implementation details (buffering, timing schemas, drift corrections).
- **Decisions Log**: Added the Audio & Synchronization Architecture decisions to `00-project/DECISION_LOG.md`.


### Updated (Flutter Architecture Freeze - 2026-07-13)
- **Frontend Architecture Specs**: Added master technical architecture rules (Riverpod state management, GoRouter navigation, Dio networking, environment compilation via `--dart-define`, local preferences constraints, and logger/crashlytics setups) to `05-architecture/system-overview.md`.
- **Frozen Decisions Summary**: Integrated a canonical Frozen Decisions index table at the top of `system-overview.md`.
- **UX Design Maturity**: Added design maturity and status declarations (frozen routing/IA/screen inventories, mockups reference, and visual design layout rules) to `03-ux/README.md`.
- **AI Assist Guidelines**: Updated accepted decisions and assistant guidelines in `AI_CONTEXT.md` to ensure future coding agents adhere to folder structures, Clean layers, and suffix naming rules.
- **Decisions Log**: Added entries for state management, routing, networking, storage scope, and environment configurations to `00-project/DECISION_LOG.md`.


### Updated (Phase 1 IA Freeze - 2026-07-13)
- **Screen Inventory Mapping**: Added canonical screen inventory mapping table (SCR-001 through SCR-013) to `05-architecture/system-overview.md` mapping screen names, parent relationships, and primary purposes.
- **Information Architecture Specs**: Overwrote placeholder document `03-ux/README.md` to act as the master IA reference, defining navigation trees, screen boundaries, direct/resume flows, and learning content hierarchies.
- **AI Assist Guidelines**: Updated `AI_CONTEXT.md` to include IA compliance rules and Guidelines for AI Assistants to use the `SCR-xxx` identifiers.
- **Decisions Log**: Added entries for IA-001, IA-002, IA-006, and IA-009 to `00-project/DECISION_LOG.md`.


### Updated (Phase 0 Release Freeze - 2026-07-13)
- **Product PRD & Scope**: Aligned `PRD.md` and `MVP-SCOPE.md` with the Phase 0 Pilot Release scope, focusing on Kusum Guruji's Lalita Sahasranama batch (~50 learners).
- **Core Capabilities**: Included user chanting recording, playback, and delete/re-record capability (`FRD-015`) in scope.
- **Onboarding Flow**: Documented onboarding flow (Splash → Script Selection → Vakratunda Mahakaya → Google Sign-In) and deferred mobile OTP.
- **Feature Deferrals**: Marked subscriptions, payments, donations, referrals, streaks, badges, notifications, multi-speed audio, and Puja Ghar as deferred/out of scope for the pilot.
- **Feedback & Content**: Documented simple WhatsApp/in-app feedback channels and "Coming Soon" indicators.
- **Success Metrics**: Updated `SUCCESS-METRICS.md` to focus on cohort validation, engagement, and retention rather than commercial/referral metrics.
- **Decision & AI Log**: Updated `DECISION_LOG.md` and `AI_CONTEXT.md` to log all Phase 0 frozen decisions.


### Refactored (Milestone 2.1)
- **Repository Organization**: Reorganized directory naming (00-project through 10-assets) to align directly with the product development lifecycle.
- **Architecture Duplication**: Merged duplicate architecture files into a single `05-architecture` folder.
- **Index Refactoring**: Simplified `README.md` and `PROJECT_INDEX.md` to remove navigation redundancies and distinct role purposes.

### Added (Milestone 2)
- **Architectural Decision Records**: Authored 7 initial ADRs under `08-decisions/` capturing context, decisions, alternatives, and consequences for major accepted product/platform choices.
- **Decision Log Links**: Linked the simple decision log (`00-project/DECISION_LOG.md`) to the detailed ADRs.

### Added (Milestone 1.1)
- **Agent Guide**: Created `00-project/AGENT_GUIDE.md` to serve as a unified stable context for AI agents, replacing the outdated `CODEX_CONTEXT.md` file.

### Added (Milestone 1)
- Documentation Milestone 1 navigation files: PROJECT_INDEX.md, folder-level INDEX.md files, CODEX_CONTEXT.md, and DECISION_LOG.md.
- Initial project documentation structure, including README, AI Context, Project Context, PRD, System Overview, Functional Requirements, and Research POC documents.
