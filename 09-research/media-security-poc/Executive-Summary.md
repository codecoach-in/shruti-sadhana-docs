# Media Security POC: Executive Summary

## Problem Statement
Shruti Sadhana's primary asset and differentiator is its curated library of "Gold Standard" Sanskrit stotram recordings. Standard audio playback techniques make media downloads and scraping trivial, threatening the platform's premium monetization model. Conversely, proxying high-bandwidth audio streams through application servers would trigger high compute costs and degrade server performance. We required an architecture that protects media assets from simple theft while maintaining scalability and fast streaming speeds.

## Goals
1. Verify a mechanism to prevent simple, unauthorized direct downloads of premium audio files.
2. Validate direct-to-client secure streaming from private cloud storage via CDN.
3. Ensure compatibility with both Flutter Android and mobile/desktop Web clients (especially iOS users on web browsers).
4. Establish a low-latency, responsive playback experience suitable for interactive learning.

## Scope
The POC tested secure media delivery pipelines, specifically evaluation of:
- **Streaming formats**: MP3 delivery vs. segmented HLS (HTTP Live Streaming).
- **Client runtimes**: Flutter Web rendering engines (CanvasKit vs. HTML renderer) and mobile web browsers.
- **Authorization methods**: Signed CDN Cookies vs. Dynamic HLS Manifest Rewriting with Signed URLs.
- **Encryption mechanisms**: Segment-level AES-128 encryption.

## High-Level Outcome
The POC successfully demonstrated that segmenting and encrypting audio using AES-128 HLS makes audio reconstruction non-trivial for general users. It validated that dynamic backend rewriting of HLS manifest files successfully propagates authorization headers to subsequent segment and key requests, solving browser-level cookie and header limitations. The resulting architecture meets security criteria and delivers fast playback without proxying streams.

## Final Architecture Recommendation
We recommend a decoupled security architecture:
1. **Direct CDN Streaming**: Audio segments are cached on a Google Cloud CDN and served directly to the client from private Google Cloud Storage.
2. **Backend Playback Authorization**: The application server validates user authentication and subscription privileges, then dynamically rewrites and signs the HLS manifest files (`.m3u8`) to redirect client players to short-lived encryption key endpoints.
3. **Flutter Web Optimization**: The client web app must use the HTML rendering engine to bypass CanvasKit's startup latency and Sanskrit text rendering limitations.

## Business Impact
Implementing this architecture secures the platform's core intellectual property. It supports the premium monetization tier by locking multi-speed audio features behind robust authentication. Because media delivery is handled entirely by CDN and Cloud Storage, server infrastructure remains lean and cost-effective, guaranteeing high margin viability for the MVP.

---

## Validation Matrix

| Research Question / Component | Outcome / Solution | Status | Impact / Notes |
| :--- | :--- | :--- | :--- |
| **Secure Streaming** | Segmented HLS + AES-128 segment encryption | **Accepted** | Makes asset scraping and direct download non-trivial. |
| **Media Authorization** | Dynamic HLS Manifest Rewriting with Signed URLs | **Accepted** | Solves cross-domain header propagation and browser third-party cookie restrictions. |
| **Platform Compatibility** | Mobile Web (Flutter Web) is suitable for MVP | **Accepted** | Deferring native iOS app development while serving Apple users immediately. |
| **Flutter Web Rendering** | HTML Renderer | **Accepted** | Noticeably faster startup latency and better Sanskrit font compatibility than CanvasKit. |
| **CanvasKit Renderer** | Excluded | **Rejected** | Rejected due to slow startup load times and font rendering issues. |
| **CDN Signed Cookies** | Excluded | **Rejected** | Rejected due to compatibility blocks with cross-domain requests on mobile web browsers. |
| **Offline Downloads** | Excluded from MVP scope | **Deferred** | Simplifies client caching, sync logic, and eliminates local decryption key exposure. |
| **Native iOS App** | Deferred to future phases | **Deferred** | Flutter Web serves Apple users adequately for MVP. |
| **Hardware DRM** | Excluded from MVP scope | **Deferred** | Deferring high-cost DRM integration (Widevine/FairPlay) until product scaling. |

---

## Related Documents
- [ADR-003: Secure Media Platform](../../08-decisions/ADR-003-secure-media.md)
- [ADR-002: Flutter Web Strategy](../../08-decisions/ADR-002-flutter-web.md)
