# Agent Guide

Purpose: Stable guidance for AI coding agents working in this repository.

## AI Role
Act as a Documentation Engineer.
- Do NOT redesign the product.
- Do NOT introduce unnecessary documentation.
- Prefer updating existing documents over creating duplicates.
- Keep documentation concise and easy to navigate.

## Documentation Philosophy
The repository answers three questions:
- **What?** -> Product documents (PRD, MVP Scope, Feature Index, FRDs)
- **Why?** -> Architectural Decision Records (ADRs)
- **How?** -> Technical Design (TDS)

Research documents capture experiments and Proof of Concept (POC) findings.

### Navigation Philosophy
- Every major folder must contain an `INDEX.md`.
- The repository root contains `PROJECT_INDEX.md`.
- The navigation design is built to help both humans and AI quickly find files. Keep it simple and manual; avoid introducing complex documentation frameworks.

## Current Roadmap

### Milestone 1: Repository Navigation (Completed)
- Improve repository discoverability.
- Add folder-level indexes and root `PROJECT_INDEX.md`.
- Create `DECISION_LOG.md`.

### Milestone 1.1: Agent Guide Setup (Current Setup)
- Create `AGENT_GUIDE.md`.
- Clean up outdated `CODEX_CONTEXT.md` and update navigation files.

### Milestone 2: Architectural Decision Records (Current)
Create Architectural Decision Records (ADRs) in `07-decisions/` for approved decisions using a simple structure (Title, Status, Context, Decision, Alternatives Considered, Consequences, Related Documents):
- ADR-001: Android First
- ADR-002: Flutter Web Strategy
- ADR-003: Secure Media Platform
- ADR-004: No Offline Downloads
- ADR-005: Separate Admin Console
- ADR-006: Curated Content Pipeline
- ADR-007: Environment Abstraction

### Milestone 3: Media Security POC Documentation
Convert the completed POC into repository documentation under `08-research/`.
Topics: Executive Summary, Technical Findings, Lessons Learned, Known Limitations, Performance, Security. Reference the validated POC rather than rewriting it.

### Milestone 4: Technical Design
Document the architecture conceptually (no implementation code, no framework-specific details).
Topics: Media Platform, Media Module, Playback Authorization, Storage Driver, Packaging Pipeline, Admin Console.

### Milestone 5: Refresh Product Documentation
Update PRD, MVP Scope, and Roadmap to reflect the validated architecture and decisions.

## Working Rules
- **Principles Over Technologies**: Whenever an ADR discusses technologies (Flutter, NestJS, GCS, Firebase, etc.), treat them as the current implementation context—not the architectural decision itself. The ADR must capture the principle being adopted, not bind the project to a specific framework or vendor unless that choice is itself intentional.
- **Modularity**: Prefer updating existing files and avoid duplicate information. Keep documents concise.
- **Cross-Referencing**: Link related files where useful (using relative markdown links).
- **Git Commit Workflow**: Commit one documentation milestone at a time, using meaningful messages. Push changes after each completed milestone.

## Git Commit Conventions
- Milestone 1.1: `docs: add agent guide and update navigation`
- Milestone 2: `docs: add architectural decision records`
- Milestone 3: `docs: document secure media platform POC`
- Milestone 4: `docs: add media platform technical design`
- Milestone 5: `docs: refresh product documentation after architecture validation`
