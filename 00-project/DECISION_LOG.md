# Decision Log

Purpose: Simple log of accepted product and architecture decisions.

Detailed ADRs are planned for Documentation Milestone 2. Until then, this file records stable decisions that should guide documentation and implementation work.

## Accepted Decisions

| Date | Area | Decision | Status | Notes |
|---|---|---|---|---|
| 2026-07-10 | Product | Shruti Sadhana is a Sanskrit Stotram Learning Platform, not a generic Sanskrit learning app. | Accepted | The focus is guided stotram learning, chanting, practice, parayana, and devotional experience. |
| 2026-07-10 | Platform | Android is the first release platform. | Accepted | Native iOS is intentionally postponed. |
| 2026-07-10 | Platform | Flutter Web is the preferred MVP experience for iPhone users. | Accepted | Supports iOS access without a native iOS app during MVP. |
| 2026-07-10 | Platform | Offline downloads are out of scope. | Accepted | Streaming and authenticated access remain the current direction. |
| 2026-07-10 | Content | Content is curated rather than user-generated. | Accepted | Gold Standard recordings and learning material are controlled content. |
| 2026-07-10 | Media | Gold Standard recordings are premium intellectual property. | Accepted | Media security and access control are important product requirements. |
| 2026-07-10 | Media | Backend authorizes playback but does not proxy media streams. | Accepted | Media delivery uses secure storage/CDN/streaming infrastructure. |
| 2026-07-10 | Media | Audio mastering is performed manually before upload, not by the backend. | Accepted | Backend starts after master audio and packaging inputs exist. |
| 2026-07-10 | Documentation | Repository navigation should use PROJECT_INDEX.md, folder-level INDEX.md files, and cross references. | Accepted | Avoid unnecessary documentation frameworks. |

## Future Decision Work

Documentation Milestone 2 should convert major accepted decisions into ADRs where detailed context, alternatives, and consequences are useful.

