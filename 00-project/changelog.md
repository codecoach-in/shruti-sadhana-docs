# Changelog

All notable changes to the Shruti Sadhana documentation and codebase will be documented in this file.

## [Unreleased]

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
