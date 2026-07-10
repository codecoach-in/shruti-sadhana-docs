# Media Security POC: Lessons Learned

This document details the core engineering learnings and insights gained during the design, execution, and validation of the Media Security Proof of Concept (POC).

---

## 1. Web Rendering Strategy: HTML Renderer Over CanvasKit
- **Insight**: In cross-platform web client development (specifically targeting mobile Safari and iOS web views), CanvasKit introduces unacceptable initial download weight (approximately 1.5 - 2.0MB engine footprint) and creates complex font-rendering bugs for non-Latin Sanskrit characters.
- **Decision**: The HTML Renderer is preferred for web client delivery. It leverages native browser text layout engines, ensuring correct Sanskrit font presentation and providing a faster initial application startup experience.

## 2. Media Authorization Strategy: Dynamic Manifest Rewriting Over Signed Cookies
- **Insight**: While CDN-level Signed Cookies are a common way to secure static assets, they are highly problematic on mobile web applications. Cross-domain HTTP requests frequently block third-party cookies by default on modern mobile browsers (especially Safari/iOS). This breaks streaming access for web client users.
- **Decision**: Dynamic Manifest Rewriting with query-string Signed URLs was selected. By rewriting HLS manifest paths on-the-fly, signed parameters are passed via the URL query string rather than cookie headers, successfully bypassing all cookie blocking mechanisms.

## 3. Development Productivity: Local-First Environment Abstraction
- **Insight**: Developing and testing secure media delivery directly in live cloud environments creates massive friction. Developers require persistent cloud credentials, face latency when uploading segments to GCS, and incur cloud compute and CDN costs for test iterations.
- **Decision**: Standardizing on environment abstraction (ADR-007) allows local-first development. Creating mock static servers to host HLS chunks and generating local mock signed parameters allows the entire media stack to run offline, dramatically increasing developer iteration speed and enabling stable, deterministic automated tests.

## 4. MVP Security Bar: Secure HLS is Sufficient
- **Insight**: Implementing full hardware-level digital rights management (DRM) like Widevine or FairPlay requires expensive licensing, separate client plugins, and heavy server configuration. 
- **Decision**: Segmented HLS with AES-128 encryption and short-lived URL signatures provides a highly effective level of security for the MVP. It raises the technical effort to extract media far beyond the capabilities of generic scraping tools, which is sufficient protection for launch.

## 5. Deployment Focus: Postponing Native iOS
- **Insight**: Flutter Web compiled clients are capable of handling authenticated HLS streaming natively via Safari's built-in media engines.
- **Decision**: We can confidently defer native iOS client development to a future phase (ADR-002) and launch on Android first (ADR-001) while still supporting Apple users via the web client, reducing initial MVP scope and saving development runway.

## 6. Security Caching: Deferring Offline Downloads
- **Insight**: Supporting offline playback requires the client application to cache decrypted media files or store encryption keys locally on the device's storage system. This exposes raw media files to easy root/jailbreak extractions and increases storage syncing complexity.
- **Decision**: Restricting the MVP to online-only streaming (ADR-004) avoids local key storage risks, simplifies client cache invalidation, and significantly strengthens initial intellectual property protection.

---

## Related Documents
- [ADR-001: Android First](../../08-decisions/ADR-001-android-first.md)
- [ADR-002: Flutter Web Strategy](../../08-decisions/ADR-002-flutter-web.md)
- [ADR-003: Secure Media Platform](../../08-decisions/ADR-003-secure-media.md)
- [ADR-004: No Offline Downloads](../../08-decisions/ADR-004-no-offline-downloads.md)
- [ADR-007: Environment Abstraction](../../08-decisions/ADR-007-environment-abstraction.md)
