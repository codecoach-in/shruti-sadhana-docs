# Decision Log

Purpose: Simple log of accepted product and architecture decisions.

This file records stable decisions that guide documentation and implementation work, pointing to detailed Architectural Decision Records (ADRs) for context, alternatives, and consequences.

## Accepted Decisions

| Date | Area | Decision | Status | Notes |
|---|---|---|---|---|
| 2026-07-10 | Product | Shruti Sadhana is a Sanskrit Stotram Learning Platform, not a generic Sanskrit learning app. | Accepted | The focus is guided stotram learning, chanting, practice, parayana, and devotional experience. |
| 2026-07-10 | Platform | Android is the first release platform. | Accepted | See [ADR-001](../08-decisions/ADR-001-android-first.md) (Native iOS is postponed). |
| 2026-07-10 | Platform | Flutter Web is the preferred MVP experience for iPhone users. | Accepted | See [ADR-002](../08-decisions/ADR-002-flutter-web.md) (Supports web access for Apple users). |
| 2026-07-10 | Platform | Offline downloads are out of scope. | Accepted | See [ADR-004](../08-decisions/ADR-004-no-offline-downloads.md) (Streaming only to protect media). |
| 2026-07-10 | Content | Content is curated rather than user-generated. | Accepted | See [ADR-006](../08-decisions/ADR-006-curated-content.md) (Ensures authentic pronunciation). |
| 2026-07-10 | Media | Gold Standard recordings are premium intellectual property. | Accepted | See [ADR-003](../08-decisions/ADR-003-secure-media.md) (Requires secure playback controls). |
| 2026-07-10 | Media | Backend authorizes playback but does not proxy media streams. | Accepted | See [ADR-003](../08-decisions/ADR-003-secure-media.md) (Media served via secure CDN/GCS). |
| 2026-07-10 | Media | Audio mastering is performed manually before upload, not by the backend. | Accepted | Backend starts after master audio and packaging inputs exist. |
| 2026-07-10 | Console | Separate Admin Console. | Accepted | See [ADR-005](../08-decisions/ADR-005-separate-admin-console.md) (Segregates administration from devotee client). |
| 2026-07-10 | Platform | Environment Abstraction. | Accepted | See [ADR-007](../08-decisions/ADR-007-environment-abstraction.md) (Identical interfaces for local mock and production cloud). |
| 2026-07-10 | Documentation | Repository navigation uses PROJECT_INDEX.md, folder-level INDEX.md, and AGENT_GUIDE.md. | Accepted | Avoids unnecessary documentation frameworks. |

## Future Decision Work

New architecture decisions should follow the ADR format specified in [README.md](../08-decisions/README.md) and be added to the Decisions folder.


