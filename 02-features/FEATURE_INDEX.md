# Feature Index

## Master Feature Backlog

This backlog tracks all planned, deferred, and future features for Shruti Sadhana. It details the components of each feature to ensure no product requirements are lost during scoping changes.

| Feature ID | Feature Name | Component Details | Target Phase | Implementation Status |
| :--- | :--- | :--- | :--- | :--- |
| **FRD-001** | Authentication | Signup, Login, Password Reset, Profile Management | Phase 0 (Pilot) | Planned (Pilot) |
| **FRD-002** | Onboarding | Onboarding Splash, script preference selection, Vakratunda Mahakaya guided experience, Google Sign-In commitment | Phase 0 (Pilot) | Planned (Pilot) |
| **FRD-003** | Home Dashboard | Continue Learning, Resume Previous Session / Recently Played list, Puja Ghar entry point | Phase 0 (Pilot) | Planned (Pilot) |
| **FRD-004** | Learning Library | Browse courses (Lalita Sahasranama first), Course description and stats | Phase 0 (Pilot) | Planned (Pilot) |
| **FRD-005** | Course Detail | Course metadata, teacher/guru info, structured section/lesson index, overall completion progress | Phase 0 (Pilot) | Planned (Pilot) |
| **FRD-006** | Lesson Experience | Sanskrit text (Devanagari), transliteration (English Transliteration), meaning, normal speed audio streaming | Phase 0 (Pilot) | Planned (Pilot) |
| **FRD-007** | Practice | Repeat playback loop, guided practice sessions, lesson revisits | Phase 0 (Pilot) | Planned (Pilot) |
| **FRD-008** | Progress Tracking | Remember user preferences, automatically track completed sections/shlokas | Phase 0 (Pilot) | Planned (Pilot) |
| **FRD-009** | Puja Ghar | Deity selection, personalized deity space, devotional ambient UI | Phase 1 (MVP) | Deferred |
| **FRD-010** | Subscriptions & Payments | Payment gateway, checkout/purchase flow, premium tier access rules | Phase 1 (MVP) | Deferred |
| **FRD-011** | Participatory Chanting | Guided call-and-response chanting, active user chanting windows | Phase 1 (MVP) | Deferred |
| **FRD-012** | Referrals | Referral code generation, referral tracking, community growth tracking | Phase 1 (MVP) | Deferred |
| **FRD-013** | Donations | Donation flows, contribution acknowledgment screen, support initiatives | Phase 1 (MVP) | Deferred |
| **FRD-014** | Profile & Settings | User account details, manual script preference toggle | Phase 0 (Pilot) | Planned (Pilot) |
| **FRD-015** | Chanting Recording & Playback | Record chanting via mic, local playback for self-check, delete & re-record options | Phase 0 (Pilot) | Planned (Pilot) |
| **FRD-016** | Multi-Speed Audio | Slow-speed (0.75x) and medium-speed (0.85x) master learning audio files | Phase 1 (MVP) | Deferred |
| **FRD-017** | AI Pronunciation Feedback | AI pronunciation evaluation, speech recognition, scoring mechanics | Phase 2 (Future) | Deferred |
| **FRD-018** | Engagement & Gamification | Daily streaks, progress badges, reward triggers, notifications | Phase 1 / Phase 2 | Deferred |
| **FRD-019** | Community Forums | Practice groups, user discussion boards, in-app messaging | Phase 2 (Future) | Deferred |
| **FRD-020** | Expanded Content & Scripts | Support for extra scripts (Indic scripts), courses (Vishnu Sahasranama, Hanuman Chalisa) | Phase 1 / Phase 2 | Deferred |

---

## Dependency Flow

Authentication
→ Onboarding
→ Home
→ Learning Library
→ Course Detail
→ Lesson Experience
→ Practice (with Chanting Recording & Playback)
→ Progress Tracking

- Chanting Recording integrates with Lesson Experience.
- Puja Ghar, Subscriptions, Referrals, and Donations are deferred from the pilot release.

---

## Governance

- Every Pilot feature requires an FRD.
- FRDs define behavior and detailed validation rules.
- Technical Design (TDS) documents define coding implementation.
- Scope changes must be reflected in this Feature Index & Backlog first.
