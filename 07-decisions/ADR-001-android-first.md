# ADR-001: Android First

## Status
Approved

## Context
Shruti Sadhana is a Sanskrit Stotram Learning Platform. We need to focus development resources during MVP to validate product-market fit quickly and efficiently. Building for multiple native platforms simultaneously increases design, testing, and deployment overhead.

Note: The current mobile client is implemented in Flutter, but this decision prioritizes the target operating system (Android) rather than committing permanently to any cross-platform tool.

## Decision
Prioritize and launch on Android first as the primary native mobile experience. Native mobile engineering effort will focus on Android validation first.

## Alternatives Considered
- **Simultaneous Android and Native iOS Launch**: Rejected. High development cost, strict Apple App Store review cycles during early iteration, and resource constraints would slow down validation.
- **iOS First**: Rejected. The target demographic and initial user base are predominantly Android users.

## Consequences
- **Positive**: Focuses all mobile design and development resources on a single native client experience, speeding up feedback loops and simplifying QA.
- **Negative**: iOS users will not have access to a native application at launch (mitigated by the Flutter Web strategy).

## Related Documents
- [project-context.md](../00-project/project-context.md)
- [PRD.md](../01-product/PRD.md)
- [ADR-002-flutter-web.md](ADR-002-flutter-web.md)
