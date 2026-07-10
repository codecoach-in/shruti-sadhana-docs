# ADR-005: Separate Admin Console

## Status
Approved

## Context
Shruti Sadhana serves two fundamentally different users:
- **Devotees**: Seekers learning stotrams with high-quality playback and practice tools.
- **Administrators**: Back-office staff uploading mastered audio, structuring courses, and managing metadata.

Combining both user profiles and their corresponding interfaces into a single application creates security vulnerabilities, bloats the public production bundle with admin screens, and mixes distinct user contexts.

Note: This decision focuses on segregating administrative capabilities at the product architecture level. Whether the admin console is built in Flutter, React, or served as basic server-rendered HTML, and whether it shares a codebase/repository, are implementation details.

## Decision
Keep administration screens and logic entirely separate from the public client application. The public client will not compile, bundle, or expose any admin workflows, endpoints, or scripts.

## Alternatives Considered
- **Public Client with Hidden Admin Screens**: Rejected. This increases the security attack surface (making admin panels discoverable in client bundles), slows down load times for regular users, and complicates testing.

## Consequences
- **Positive**:
  - **Security**: Hardens the public application by removing administrative privileges and UI logic entirely.
  - **Performance**: Keeps the public application bundle size lightweight.
  - **Maintainability**: Allows independent deployment, scaling, and update cycles for user and admin consoles.
- **Negative**: Requires managing and deploying a second application console.

## Related Documents
- [project-context.md](../00-project/project-context.md)
- [PRD.md](../01-product/PRD.md)
