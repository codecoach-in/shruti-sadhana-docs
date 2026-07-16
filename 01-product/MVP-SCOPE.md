# Phase 0 Pilot Scope

## Purpose

This document defines what is included in and excluded from the Phase 0 Pilot Release of Shruti Sadhana, which acts as our MVP.

The goal of the pilot is to validate Shruti Sadhana as a practical practice companion for Kusum Guruji's Lalita Sahasranama batch (~50 learners) before adding monetization, referrals, or advanced AI features.

---

# Included Features

## Authentication & Onboarding
- **Onboarding Flow**: Splash (SS-001) → Choose Your Shloka Script (SS-002) → Let's Begin (Invocation Screen - SS-002A) → Vakratunda Demo (Guided Demo Session - SS-002B) → Google Sign-In.
- **Commitment Point**: Users must sign in via Google to use the application after experiencing the guided Vakratunda Demo.
- **No Guest Mode**: No guest access exists past the introductory guided experience.
- **Mobile OTP**: Deferred.

## Home Dashboard
- **Continue Learning**: Quick access to resume the previous learning session via a prominent manuscript/book illustration.
- **Today's Learning**: Displays Dhyanam (when applicable), Assigned Shlokas, and Kshama Prarthana (no per-item duration).
- **Progress Split**: Shows Today's Progress and Overall Progress as separate indicators.
- **Native Ad Placement**: Google Native Ad positioned directly between Today's Progress and Overall Progress.
- **Support Banner**: Positioned near the bottom of the scroll view.

## Learning Library
- **Browse Course**: Access to Lalita Sahasranama only.
- **Course Detail Page**: Detailed summary, section index, Meet Your Guide details, and progress indicators.
- **Learning Plan Setup (SS-005A)**: Selection wizard triggered only once when beginning a course. Allows choosing between "Slow & Steady" (3 Shlokas/day, ~20 mins) and "Balanced" (7 Shlokas/day) paces (no daily decision-making; pace can be changed later from Settings).

## Lesson Experience
- **Sanskrit Text**: Display in Devanagari script.
- **Transliteration**: English Transliteration.
- **Meaning**: Verse translations and contextual meanings.
- **Audio Playback**: Normal-speed audio streaming.
- **Streaming Core**: Graceful handling of poor connectivity (robust streaming, buffering).

## Chanting Practice (Recording & Playback)
- **Record Chanting**: Ability for the user to record their chanting of a shloka/section.
- **Playback**: Play back the user's recorded chant to let them self-evaluate.
- **Delete/Re-record**: Quickly delete a recording and record again.

## Progress Tracking & Preferences
- **Preferences**: Remember the user's script selection (Devanagari vs. English Transliteration).
- **My Shruti Sadhana Screen (SS-011)**: Includes the hero deity illustration, Journey So Far, Milestones, Learning Plan, a Native Ad, and a Support Banner. Uses hand-painted spiritual illustrations (Mala, Havan Kund, Agarbatti, Manuscript) instead of generic Material icons.
- **Progress Calculation**: Progress is calculated based on completed Shlokas only. Shloka is the permanent learning unit, and Session is a dynamic temporary container.

## Feedback Collection
- **In-App Feedback**: Simple entry point to submit text-based feedback.
- **WhatsApp Integration**: Redirection link to join the class WhatsApp group.

## Content Management
- **Backend-Driven**: Playback configuration, audio assets, and transcripts managed backend-side.
- **WIP Indicators**: Display "Coming Soon" or "Work in Progress" indicators for shlokas where recordings are not yet available.

---

# Launch Content

- **Lalita Sahasranama**: Audio, lyrics, structured sections, and shlokas matching Guruji's teaching structure.

---

# Excluded / Deferred Features

These features remain on the long-term roadmap and are deferred post-pilot:

- **Monetization & Premium Tiers**: Subscriptions, payments, donations, and multi-speed playback (slow/medium audio speeds).
- **Referrals**: Code generation and tracking for cohort growth.
- **AI Pronunciation**: AI pronunciation evaluation, scoring, and alignment engine.
- **Advanced Learning**: Word-by-word and line-by-line learning modes.
- **Engagement Features**: Streaks, badges, and gamification elements.
- **Notifications**: System or push notifications.
- **Community Features**: Discussion forums, groups, and messaging.
- **iOS Native App**: Excluded (iPhone/iPad users are supported via Flutter Web).
- **Puja Ghar**: Deity selection, personalized deity spaces, and devotional ambient UI.
- **Additional Scripts**: Indic scripts other than Devanagari.
- **Additional Content**: Vishnu Sahasranama and Hanuman Chalisa.
- **Advanced Analytics**: Detailed user tracking dashboard.