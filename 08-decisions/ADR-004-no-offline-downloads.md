# ADR-004: No Offline Downloads

## Status
Approved

## Context
Allowing offline media downloads is a common feature request for educational and spiritual audio platforms. However, local storage of high-value media files (especially on desktop web browsers and native mobile filesystems) significantly heightens the risk of piracy and content extraction. Furthermore, offline caching, local data synchronization, and offline DRM or license checks add immense engineering complexity for an MVP.

## Decision
Exclude offline downloads from the MVP scope. The application will strictly stream media online, requiring a continuous network connection to fetch signed playback tokens and stream encrypted segments.

## Alternatives Considered
- **Encrypted Local Cache (Android-only)**: Rejected for MVP. Storing encrypted chunks locally is technically feasible on Android, but does not solve the challenge for Web users and introduces massive database and state synchronization complexity.
- **Unencrypted Local Download (Standard MP3)**: Rejected. High piracy risk; conflicts with the decision to protect Gold Standard recordings (ADR-003).

## Consequences
- **Positive**: Simplifies client-side storage architecture, eliminates complex offline state management/sync bugs, and preserves media protection controls.
- **Negative**: Users with weak or no internet connectivity cannot practice or listen, which may impact user retention in low-connectivity environments.

## Related Documents
- [MVP-SCOPE.md](../01-product/MVP-SCOPE.md)
- [ADR-003-secure-media.md](ADR-003-secure-media.md)
