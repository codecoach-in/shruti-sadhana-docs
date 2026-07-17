# Product Backlog v1

Purpose: the living inventory of approved product, technical, and quality work for Shruti Sadhana. Architecture documents remain frozen design artifacts; this backlog records what is to be implemented, what is intentionally deferred, and what has been rejected from the current direction.

**Status vocabulary:** ✅ Completed · 🚧 In Progress · 📋 Planned · ⏳ Deferred · ❌ Rejected

## Authentication & User Management

| Feature Name | Short Description | Status | Target Release |
| --- | --- | --- | --- |
| Guided onboarding and Vakratunda demo | Lead the user through script choice, invocation, and guided demo before the sign-in commitment point. | 📋 Planned | Pilot |
| Google Sign-In | Authenticate users with Google after the guided experience and require sign-in for protected use. | 📋 Planned | Pilot |
| Authentication recovery | Handle cancelled sign-in, connectivity failure, token expiry, and repeatable retry. | 📋 Planned | Pilot |
| User bootstrap and profile | Resolve the authenticated application user and server-authoritative profile at launch. | 📋 Planned | Pilot |
| Mobile OTP sign-in | Add mobile-number authentication as an additional sign-in method. | ⏳ Deferred | Post-Pilot |

## Learning Experience

| Feature Name | Short Description | Status | Target Release |
| --- | --- | --- | --- |
| Home dashboard | Provide Continue Learning, today’s learning, and separate today/overall progress views. | 📋 Planned | Pilot |
| Learning Library | Browse the published Lalita Sahasranama learning programme. | 📋 Planned | Pilot |
| Course details and guide | Present course context, guide information, section summaries, and progress. | 📋 Planned | Pilot |
| Learning-plan setup | Let a learner select Slow & Steady or Balanced pace when starting a course. | 📋 Planned | Pilot |
| Section and Shloka index | Navigate canonical Course → Section → Shloka content structure. | 📋 Planned | Pilot |
| Learning Session | Present one shloka’s manuscript, text representations, meaning, and guided learning controls. | 📋 Planned | Pilot |
| Samarpan completion flow | Provide the devotional completion experience and transition after chanting practice. | 📋 Planned | Pilot |
| Word-by-word and line-by-line learning | Add advanced learning modes beyond the pilot’s complete-shloka session. | ⏳ Deferred | Post-Pilot |
| Meaning and memorization courses | Offer learning programmes focused on meaning or memorization. | ⏳ Deferred | Future |
| Parayana experience | Support complete devotional recitation journeys. | ⏳ Deferred | Future |

## Practice & Progress

| Feature Name | Short Description | Status | Target Release |
| --- | --- | --- | --- |
| Chanting recording and playback | Record a learner’s chant locally, play it back, and delete or re-record it. | 📋 Planned | Pilot |
| Guided practice and repeat listening | Support repeated lesson playback and revisits for practice. | 📋 Planned | Pilot |
| Shloka completion progress | Track learning progress using completed shlokas as the canonical unit. | 📋 Planned | Pilot |
| Resume learning | Return the learner to a safe server-authoritative last shloka. | 📋 Planned | Pilot |
| My Shruti Sadhana | Present journey, milestones, learning plan, and overall progress. | 📋 Planned | Pilot |
| Participatory chanting | Add guided call-and-response chanting windows beyond basic practice. | ⏳ Deferred | Post-Pilot |
| Smart revision | Add intelligent revision suggestions based on learning behaviour. | ⏳ Deferred | Future |
| Engagement and gamification | Add streaks, badges, and reward triggers. | ⏳ Deferred | Post-Pilot |

## Audio & Media

| Feature Name | Short Description | Status | Target Release |
| --- | --- | --- | --- |
| Normal-speed secure audio streaming | Stream curated Complete Shloka audio with graceful buffering behaviour. | 📋 Planned | Pilot |
| Secure HLS playback authorization | Issue short-lived authorized manifest access without exposing storage paths or proxying media. | 📋 Planned | Pilot |
| Audio synchronization | Map approved lesson timing metadata to learning-session highlighting hooks. | 📋 Planned | Pilot |
| Android playback cache | Retain only bounded, encrypted, player-managed near-term stream data for smooth authorized playback. | 📋 Planned | Pilot |
| Multi-speed audio | Add slow and medium curated learning audio variants. | ⏳ Deferred | Post-Pilot |
| Offline downloads | Provide intentionally downloadable/offline lesson media. | ⏳ Deferred | Future |
| Hardware DRM | Add platform DRM beyond encrypted HLS and signed delivery. | ⏳ Deferred | Future |

## Content Management & Administration

| Feature Name | Short Description | Status | Target Release |
| --- | --- | --- | --- |
| Lalita Sahasranama launch content | Prepare approved sections, shlokas, text, meaning, audio, and timing metadata for the pilot. | 📋 Planned | Pilot |
| Curated content pipeline | Publish only administrator-managed, expert-verified content and recordings. | 📋 Planned | Pilot |
| Content availability indicators | Show Work in Progress or Coming Soon when an approved shloka recording is unavailable. | 📋 Planned | Pilot |
| Separate Admin Console | Provide administration separately from the devotee client for content and metadata operations. | 📋 Planned | Pilot |
| Additional courses | Add further stotrams such as Vishnu Sahasranama and Hanuman Chalisa. | ⏳ Deferred | Post-Pilot |
| Multiple gurus | Support courses associated with more than one guru. | ⏳ Deferred | Future |
| Additional Indic scripts | Add text representations beyond Devanagari and English Transliteration. | ⏳ Deferred | Post-Pilot |

## Personalization, Support & Feedback

| Feature Name | Short Description | Status | Target Release |
| --- | --- | --- | --- |
| Script and reading preferences | Persist selected text script and supported reading preferences across sessions. | 📋 Planned | Pilot |
| Settings | Let learners manage supported preferences and learning-plan updates. | 📋 Planned | Pilot |
| In-app feedback | Collect authenticated text feedback through a simple entry point. | 📋 Planned | Pilot |
| WhatsApp group integration | Direct the pilot cohort to the class WhatsApp group for feedback and support. | 📋 Planned | Pilot |
| Home native-ad placement | Display the approved native-ad layout only on the Home dashboard. | 📋 Planned | Pilot |
| Support banner | Present the approved non-blocking support banner outside active chanting. | 📋 Planned | Pilot |
| Donations and contribution flow | Enable voluntary financial contributions and acknowledgement. | ⏳ Deferred | Post-Pilot |
| Subscriptions and payments | Add premium access, checkout, and payment processing. | ⏳ Deferred | Post-Pilot |
| Referrals | Add referral-code generation and cohort-growth tracking. | ⏳ Deferred | Post-Pilot |

## AI, Devotional & Community Futures

| Feature Name | Short Description | Status | Target Release |
| --- | --- | --- | --- |
| AI pronunciation assessment | Evaluate pronunciation and provide chanting feedback or scoring. | ⏳ Deferred | Future |
| Virtual Puja Ghar | Add deity selection, personalized devotional spaces, and ambient experience. | ⏳ Deferred | Post-Pilot |
| Community features | Add practice groups, discussions, and in-app messaging. | ⏳ Deferred | Future |
| Notifications | Add system or push reminders and notification triggers. | ⏳ Deferred | Post-Pilot |
| Advanced analytics | Provide detailed learner and product analytics beyond pilot measurement. | ⏳ Deferred | Future |

## Platform & Infrastructure

| Feature Name | Short Description | Status | Target Release |
| --- | --- | --- | --- |
| Android Flutter application | Deliver the primary native client for the pilot cohort. | 📋 Planned | Pilot |
| Responsive Flutter Web client | Deliver browser access for iPhone, iPad, macOS, Windows, and Linux users. | 📋 Planned | Pilot |
| Native iOS application | Deliver a separate native iOS client. | ⏳ Deferred | Future |
| Firebase integration | Verify Firebase identity server-side and map it to an application user. | 📋 Planned | Pilot |
| NestJS modular monolith | Deliver the approved backend modules and public REST contract. | 📋 Planned | Pilot |
| MySQL and Prisma persistence | Deliver the canonical relational data store and migrations. | 📋 Planned | Pilot |
| Google Cloud media platform | Operate private GCS media storage with direct Cloud CDN delivery. | 📋 Planned | Pilot |
| Environment abstraction | Support local/mock, development, staging, and production integration drivers. | 📋 Planned | Pilot |
| API versioning and OpenAPI | Publish and test the versioned API contract used by Flutter. | 📋 Planned | Pilot |
| Contract and integration testing | Validate critical API, authentication, progress, and media-authorization paths. | 📋 Planned | Pilot |
| Release automation and deployment | Establish the production release path and basic CI checks. | 📋 Planned | Pilot |

## Quality Attributes

| Feature Name | Short Description | Status | Target Release |
| --- | --- | --- | --- |
| Secure media protection | Use encrypted HLS, short-lived signed delivery, and no backend media proxy. | 📋 Planned | Pilot |
| Streaming resilience | Handle poor connectivity, buffering, and authorization refresh during playback. | 📋 Planned | Pilot |
| Sanskrit typography | Render supported Sanskrit scripts correctly across Android and responsive Web. | 📋 Planned | Pilot |
| Responsive and accessible UI | Support flexible viewport layouts and available reading-scale preferences. | 📋 Planned | Pilot |
| Error handling and recovery | Provide safe user-facing errors and retry paths for authentication, API, playback, and recording failures. | 📋 Planned | Pilot |
| Logging and health checks | Provide structured, redacted diagnostics and backend liveness/readiness checks. | 📋 Planned | Pilot |
| Crash reporting | Capture client crashes without exposing sensitive runtime information. | 📋 Planned | Pilot |
| Privacy and sensitive-data protection | Protect tokens, credentials, signed media access, and personal data in storage and logs. | 📋 Planned | Pilot |
| Local cache boundaries | Cache only permitted preferences, metadata, and bounded playback data; never create an offline media library. | 📋 Planned | Pilot |

## Inventory Review

- Pilot product capabilities, architecture-required technical work, quality attributes, deferred directions, and intentionally excluded offline media are represented once.
- Deferred work reflects documented future direction only; no new feature ideas have been added.
- This document replaces the detailed feature table previously held in `FEATURE_INDEX.md` as the canonical backlog.
