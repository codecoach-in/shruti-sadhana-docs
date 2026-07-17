# Shruti Sadhana Documentation Repository Structure

This document lists the intended documentation hierarchy. To assist both human developers and AI coding agents, each document is labeled with one of the following statuses:
* ✅ **Existing**: The document is complete and exists in the repository.
* 🟡 **Planned**: The document is scoped and will be created or fully detailed during the upcoming Architecture and Technical Design phases.
* 🔴 **Future**: The document is deferred to post-pilot/future phases and is currently out of scope.

---

```text
shruti-sadhana-docs/
│
├── README.md ✅
├── PROJECT_INDEX.md ✅
├── AI_CONTEXT.md ✅
│
├── 00-project/
│   ├── INDEX.md ✅
│   ├── AGENT_GUIDE.md ✅
│   ├── project-context.md ✅
│   ├── glossary.md ✅
│   ├── DECISION_LOG.md ✅
│   ├── changelog.md ✅
│   ├── docs-structure.md ✅
│   ├── vision-and-principles.md 🔴 (Future)
│   └── personas.md 🔴 (Future)
│
├── 01-product/
│   ├── INDEX.md ✅
│   ├── PRD.md ✅
│   ├── MVP-SCOPE.md ✅
│   ├── SUCCESS-METRICS.md ✅
│   └── SAMARPAN_FLOW.md ✅
│
├── 02-features/
│   ├── INDEX.md ✅
│   └── FEATURE_INDEX.md ✅
│
├── 03-ux/
│   ├── INDEX.md ✅
│   ├── README.md ✅
│   ├── design/
│   │   ├── DESIGN_SYSTEM.md ✅
│   │   ├── ILLUSTRATION_GUIDE.md ✅
│   │   └── UI_COMPONENT_LIBRARY.md ✅
│   └── screens/
│       ├── SS-003-home/
│       │   └── home.md ✅
│       ├── SS-008-learning-session/
│       │   └── learning-session.md ✅
│       ├── SS-001-splash/ 🟡 (Planned)
│       ├── SS-002-script-selection/ 🟡 (Planned)
│       ├── SS-002A-invocation/ 🟡 (Planned)
│       ├── SS-002B-vakratunda-demo/ 🟡 (Planned)
│       ├── SS-004-learning-library/ 🟡 (Planned)
│       ├── SS-005-course-details/ 🟡 (Planned)
│       ├── SS-005A-plan-setup/ 🟡 (Planned)
│       ├── SS-006-meet-guide/ 🟡 (Planned)
│       ├── SS-007-shlokas-index/ 🟡 (Planned)
│       ├── SS-009-samarpan/ 🟡 (Planned)
│       ├── SS-010-support/ 🟡 (Planned)
│       ├── SS-011-progress/ 🟡 (Planned)
│       └── SS-012-settings/ 🟡 (Planned)
│
├── 04-frd/
│   ├── INDEX.md ✅
│   ├── FRD-00-architecture.md ✅
│   ├── FRD-01-authentication.md 🟡 (Planned)
│   ├── FRD-02-content-model.md 🟡 (Planned)
│   ├── FRD-03-learning-experience.md 🟡 (Planned)
│   ├── FRD-04-audio-engine.md 🟡 (Planned)
│   ├── FRD-08-chanting-recording.md 🟡 (Planned)
│   ├── FRD-05-poojaghar.md 🔴 (Future)
│   ├── FRD-06-progress-and-notifications.md 🔴 (Future)
│   └── FRD-07-referrals-and-donations.md 🔴 (Future)
│
├── 05-architecture/
│   ├── INDEX.md ✅
│   ├── system-overview.md ✅
│   ├── audio-synchronization.md ✅  <-- Placeholder added to reserve contract
│   ├── frontend-flutter.md 🟡 (Planned)
│   ├── backend-nestjs.md 🟡 (Planned)
│   ├── database-design.md 🟡 (Planned)
│   ├── api-guidelines.md 🟡 (Planned)
│   ├── security.md 🟡 (Planned)
│   └── integrations.md 🟡 (Planned)
│
├── 06-technical-design/
│   ├── INDEX.md ✅
│   ├── README.md ✅
│   ├── TDS-authentication.md 🟡 (Planned)
│   ├── TDS-learning.md 🟡 (Planned)
│   ├── TDS-audio-engine.md 🟡 (Planned)
│   ├── TDS-chanting-recording.md 🟡 (Planned)
│   └── TDS-poojaghar.md 🔴 (Future)
│
├── 07-content/
│   ├── INDEX.md ✅
│   ├── README.md ✅
│   ├── content-model.md 🟡 (Planned)
│   ├── course-structure.md 🟡 (Planned)
│   └── guru-model.md 🟡 (Planned)
│
├── 08-decisions/
│   ├── INDEX.md ✅
│   ├── README.md ✅
│   ├── ADR-001-android-first.md ✅
│   ├── ADR-002-flutter-web.md ✅
│   ├── ADR-003-secure-media.md ✅
│   ├── ADR-004-no-offline-downloads.md ✅
│   ├── ADR-005-separate-admin-console.md ✅
│   ├── ADR-006-curated-content.md ✅
│   └── ADR-007-environment-abstraction.md ✅
│
├── 09-research/
│   ├── INDEX.md ✅
│   ├── README.md ✅
│   ├── media-security-poc/
│   │   ├── INDEX.md ✅
│   │   ├── Executive-Summary.md ✅
│   │   ├── Technical-Findings.md ✅
│   │   ├── Lessons-Learned.md ✅
│   │   └── Future-Recommendations.md ✅
│   ├── participatory-chanting-poc.md ✅
│   ├── competitor-analysis.md 🟡 (Planned)
│   └── future-ideas.md 🔴 (Future)
│
└── 10-assets/
    ├── INDEX.md ✅
    ├── README.md ✅
    ├── mockups/ 🟡 (Planned)
    ├── diagrams/ 🟡 (Planned)
    └── references/ 🟡 (Planned)
```
