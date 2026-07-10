# Codex Context

Purpose: Stable guidance for Codex and other AI coding agents working in this repository.

## Role

Codex maintains and improves the documentation repository while respecting existing product and architecture decisions.

Codex is not the Product Owner and should not redesign the product unless explicitly asked. If a requested change appears to affect product scope, architecture, or accepted decisions, highlight that before changing the documents.

## Product Context

Shruti Sadhana is a Sanskrit Stotram Learning Platform. It is not a generic Sanskrit learning app, meditation app, audio streaming app, or course marketplace.

The product focuses on:
- Guided stotram learning.
- Correct pronunciation and chanting.
- Practice and parayana.
- Participatory chanting.
- Personalized Puja Ghar experiences.

## Accepted Direction

These decisions are accepted unless explicitly changed:
- Android is the first release platform.
- Flutter Web is the preferred MVP experience for iPhone users.
- Native iOS application is intentionally postponed.
- Offline downloads are out of scope.
- Content is curated rather than user-generated.
- Gold Standard recordings are premium intellectual property.

## Media Platform Direction

The current approved media direction is:
- Flutter Android.
- Flutter Web.
- NestJS backend.
- Private Google Cloud Storage.
- Google Cloud CDN.
- HLS streaming.
- AES-128 encrypted HLS segments.
- Secure authenticated playback.
- Dedicated Media Module.
- Separate Admin Console.
- Local media packaging pipeline.

The backend should authorize playback only. It should not proxy media streams.

## Audio Workflow

Gold Standard recordings are manually mastered before upload.

Audio mastering is not performed by the backend.

Workflow:
1. Guruji recording.
2. Manual editing in Audacity or equivalent tooling.
3. Master audio.
4. Packaging.
5. Secure streaming.

## Repository Navigation

Use these navigation concepts:
- [../PROJECT_INDEX.md](../PROJECT_INDEX.md) as the root repository index.
- `INDEX.md` inside each major folder.
- Cross references between related documents.

Avoid introducing additional documentation frameworks unless there is a real need.

## Working Principles

- Prefer updating existing documents over creating duplicates.
- Keep documentation concise and easy to navigate.
- Do not modify PRDs or architecture documents unless the requested milestone includes that work.
- Preserve repository structure unless a structure change is explicitly approved.
- Treat product decisions as more important than implementation details.
- When unsure, audit first, propose the changes, then implement.

