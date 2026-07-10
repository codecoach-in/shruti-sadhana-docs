# Media Security POC: Future Recommendations

This document outlines the long-term engineering recommendations, classifying them based on their current evaluation status.

---

## 1. Accepted Decisions (Current Directions)

These choices have been validated, approved, and integrated into the project context and architecture guidelines:
- **AES-128 Segmented HLS**: Approved as the secure streaming format for all premium audio tracks (Lalita Sahasranama, Vishnu Sahasranama, etc.).
- **Dynamic Manifest Rewriting**: Selected as the authoritative mechanism to distribute short-lived query-string signatures directly to client media players.
- **HTML Renderer for Web**: Mandated for the Flutter Web build to guarantee fast load times and correct Sanskrit text presentation.
- **Environment Abstraction**: Mandated across all infrastructure integrations (Storage, Auth, payments, etc.) to support offline local development and mock testing.

---

## 2. Deferred Decisions (Future Phase Evaluations)

These improvements remain open and will be evaluated in subsequent product phases based on validation data and growth needs:
- **Native iOS Application**: *Deferred*. Compile and serve via Flutter Web for iOS/Apple users during MVP to defer App Store and native development costs.
- **Hardware-Level DRM (Widevine / FairPlay)**: *Deferred*. The security provided by AES-128 encrypted HLS is sufficient for the MVP launch. If piracy risks increase or corporate partnerships require full DRM protection, full DRM integration will be re-evaluated.
- **Offline Download Support**: *Deferred*. Excluded from the MVP to simplify client caching and secure key storage. Re-evaluation will depend on user retention data and direct feedback requesting offline practice capabilities.
- **AI Pronunciation Feedback & Scoring**: *Deferred*. Complex speech recognition and phoneme analysis engines are deferred to Phase 2 of the roadmap.

---

## 3. Rejected Alternatives

These strategies were evaluated during the POC and rejected based on technical blocks or product goals:
- **Proxying Media Streams Through App Server**: *Rejected*. Introduces a high compute bottleneck on application nodes and scales bandwidth hosting costs unnecessarily.
- **CDN Signed Cookies**: *Rejected*. Fails to support cross-domain mobile web streaming because of third-party cookie restrictions enforced on mobile web browsers (especially iOS/Safari).
- **CanvasKit Web Rendering**: *Rejected*. Sapped initial page load speed due to heavy engine downloads and resulted in rendering flaws on custom Sanskrit fonts.
- **Crowdsourced / User-Generated Audio uploads**: *Rejected*. UGC fails to guarantee correct pronunciation and compromises the brand trust required for Sanskrit learning.

---

## Related Documents
- [ADR-001: Android First](../../08-decisions/ADR-001-android-first.md)
- [ADR-002: Flutter Web Strategy](../../08-decisions/ADR-002-flutter-web.md)
- [ADR-003: Secure Media Platform](../../08-decisions/ADR-003-secure-media.md)
- [ADR-004: No Offline Downloads](../../08-decisions/ADR-004-no-offline-downloads.md)
- [ADR-005: Separate Admin Console](../../08-decisions/ADR-005-separate-admin-console.md)
- [ADR-006: Curated Content Pipeline](../../08-decisions/ADR-006-curated-content.md)
- [ADR-007: Environment Abstraction](../../08-decisions/ADR-007-environment-abstraction.md)
