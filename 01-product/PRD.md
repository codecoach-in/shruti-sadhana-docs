# Product Requirements Document (PRD)

## Product

**Shruti Sadhana**

Status: Frozen (Phase 0 Pilot Release)

Version: Pilot v1.0 (Design Freeze)

---

Help every seeker chant correctly, confidently, and devotionally — bringing the temple learning experience into their homes.

Shruti Sadhana is a Stotram Learning Platform, not a Sanskrit language learning app. For the Phase 0 Pilot Release, the product functions as a practice and revision companion for Kusum Guruji's Lalita Sahasranama batch, providing the experience of being guided by a traditional teacher rather than navigating a conventional educational app.

---

# 2. Problem Statement

Many devotees wish to learn sacred stotrams but struggle with:

- Lack of authentic and structured learning resources
- Incorrect pronunciation habits
- Dependence on unstructured YouTube videos
- Difficulty practicing consistently
- Lack of progress visibility

Existing apps generally focus on reading, audio playback, meditation, or mantra counting rather than guided stotram learning.

---

# 3. Product Goals

## G1. Learn Correctly

Enable users to learn stotrams with guided audio and structured lessons.

## G2. Practice Confidently

Allow users to listen, repeat, and practice at their own pace.

## G3. Build Daily Sadhana

Encourage consistent devotional practice through a personalized experience.

## G4. Deliver Premium Learning Experience

Provide a learning experience superior to scattered online resources.

---

# 4. Target Audience

## Phase 0 Pilot Cohort
- **Cohort**: ~50 learners from Kusum Guruji's Lalita Sahasranama batch.
- **Platforms**: Android native app (primary) and Flutter Web (for iPhone/iPad users).
- **Behavior**: revision and practice companion used primarily after class.

## Long-Term Personas

### Elder Devotee
- Wants correct pronunciation
- Prefers guided learning
- Limited technical expertise

### Busy Professional
- Limited daily time
- Wants structured progression
- Learns on mobile devices

### Dedicated Sadhaka
- Regular practitioner
- Wants mastery and consistency
- Interested in multiple stotrams

---

# 5. MVP Scope (Phase 0 Pilot)

The Phase 0 Pilot focuses on the following capabilities:

## Learning Library
- Access to Lalita Sahasranama only, structured by sections and shlokas.

## Guided Learning & Practice
- Lyrics in Devanagari and English Transliteration.
- Normal-speed audio streaming (reliable, handles poor connectivity gracefully).
- Chanting recording, playback, and delete/re-record capability.

## Feedback & Onboarding
- Onboarding flow: Splash → Choose Your Shloka Script → Let's Begin (Invocation Screen) → Vakratunda Demo (Guided Demo Session) → Google Sign-In. No guest mode post-guided experience.
- Feedback collection via simple in-app entry point and WhatsApp group.

Detailed feature scope is maintained in [MVP-SCOPE.md](MVP-SCOPE.md).

---

# 6. Monetization Strategy

For the Phase 0 Pilot Release, all features are free of charge for the target cohort.
All monetization, subscription models, payments, and donation mechanisms are deferred to future phases.

---

# 7. Out of Scope for Phase 0 Pilot

The following features are intentionally deferred:
- Subscriptions and payments
- Multi-speed playback (slow/medium speed)
- Referrals and donations
- Streaks, badges, and gamification
- In-app notifications
- AI pronunciation evaluation and scoring
- Word-by-word/line-by-line learning modes
- Puja Ghar (deity selection and devotional ambience)
- Native iOS application (iPhone/iPad supported via Flutter Web)
- Additional script support (only Devanagari and English Transliteration included)
- Advanced analytics

---

# 8. Success Metrics

For detailed pilot metrics, see [SUCCESS-METRICS.md](SUCCESS-METRICS.md).
Key areas tracked:
- **Acquisition**: App installs and sign-ups.
- **Engagement**: Cohort active usage, weekly sessions, practice duration, and chanting recordings.
- **Learning**: Lesson completion rates and repeat practice sessions.
- **Feedback & Retention**: WhatsApp active discussion and simple feedback submissions; Day 7/14/30 cohort retention.

---

# 9. Phase 0 Pilot Release Criteria

The application is considered ready for the pilot release when:
- The onboarding flow (Splash → Choose Your Shloka Script → Let's Begin (Invocation Screen) → Vakratunda Demo (Guided Demo Session) → Google Sign-In) is fully functional and stable.
- Google Sign-In is active (no guest mode post-onboarding, mobile OTP deferred).
- Lalita Sahasranama course content is complete (audio, lyrics, shlokas, sections).
- Normal speed audio streaming is reliable and handles poor internet gracefully.
- Chanting recording, playback, and delete/re-record works.
- The simple in-app feedback entry point is functional and links to the WhatsApp group.

---

# Product Statement

Shruti Sadhana exists to make authentic Sanskrit stotram learning accessible through guided learning, structured practice, and a devotional digital experience (Learn • Practice • Sadhana).