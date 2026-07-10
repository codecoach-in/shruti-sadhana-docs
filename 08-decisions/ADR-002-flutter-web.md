# ADR-002: Flutter Web Strategy

## Status
Approved

## Context
While Android is the first native release platform (ADR-001), we still want to provide Apple/iOS users access to the MVP without investing in native iOS application development, deployment, and App Store verification costs. 

Note: While the client is currently built using Flutter (allowing web compilation), this decision focuses on serving iOS users via a responsive web client rather than locking the architecture to a specific web framework long-term.

## Decision
Deploy a responsive web client to serve Apple/iOS users during the MVP. The mobile-responsive web version will run on the same backend and share client logic.

## Alternatives Considered
- **Native iOS Application**: Postponed. Bypassed for MVP to avoid native iOS development, testing overhead, and Apple Developer program distribution constraints.
- **No iOS Support during MVP**: Rejected. Excluding iOS users entirely would skew user feedback and significantly reduce the MVP's reach.

## Consequences
- **Positive**: Lowers the bar to entry for iOS users, bypasses Apple App Store review cycles, and enables cross-platform availability using a unified codebase.
- **Negative**: Certain native features (like background audio playback, system media control integration, and local caching optimizations) may face web browser sandboxing and performance limitations.

## Related Documents
- [ADR-001-android-first.md](ADR-001-android-first.md)
- [MVP-SCOPE.md](../01-product/MVP-SCOPE.md)
