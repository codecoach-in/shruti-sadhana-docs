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

### Architecture vs. Technical Design
- **Architecture (05-architecture)**: Defines how the system-wide components are organized, their relationships, integrations, deployment, and security models (e.g. system overview, deployment architectures).
- **Technical Design (06-technical-design)**: Defines how a specific module, integration interface, or logic component works at the code level (e.g. Media Module specification, Storage Driver APIs, Auth hooks).

### Navigation Philosophy
- Every major folder must contain an `INDEX.md`.
- The repository root contains `PROJECT_INDEX.md`.
- The navigation design is built to help both humans and AI quickly find files. Keep it simple and manual; avoid introducing complex documentation frameworks.

## Current Roadmap

### Milestone 1: Repository Navigation (Completed)
- Improve repository discoverability.
- Add folder-level indexes and root `PROJECT_INDEX.md`.
- Create `DECISION_LOG.md`.

### Milestone 1.1: Agent Guide Setup (Completed)
- Create `AGENT_GUIDE.md`.
- Clean up outdated `CODEX_CONTEXT.md` and update navigation files.

### Milestone 2: Architectural Decision Records (Completed)
- Author the 7 initial ADRs in the Decisions folder.

### Milestone 2.1: Repository Cleanup & Lifecycle Refactoring (Current)
- Resolve duplicate architecture folders.
- Clarify Architecture vs Technical Design.
- Simplify README so it doesn't duplicate `PROJECT_INDEX.md`.
- Reorganize directories into an 11-folder lifecycle sequence (00 to 10).

### Milestone 3: Media Security POC Documentation
- Document the Media Security POC under `09-research/` (Executive Summary, Technical Findings, Lessons Learned, Known Limitations, Performance, Security).
- Maintain the POC implementation plan as a historical archive under `09-research/`.

### Milestone 4: Technical Design
Document the architecture conceptually (no implementation code, no framework-specific details).
Topics: Media Platform, Media Module, Playback Authorization, Storage Driver, Packaging Pipeline, Admin Console.

### Milestone 5: Refresh Product Documentation
Update PRD, MVP Scope, and Roadmap to reflect the validated architecture and decisions.

## Working Rules
- **Principles Over Technologies**: Whenever an ADR discusses technologies (Flutter, NestJS, GCS, Firebase, etc.), treat them as the current implementation context—not the architectural decision itself. The ADR must capture the principle being adopted, not bind the project to a specific framework or vendor unless that choice is itself intentional.
- **POC Knowledge Distillation**: A Proof of Concept (POC) is not considered complete until its validated findings have been distilled into the repository as research documents. The implementation artifacts (code, scripts, logs, plans) remain with the POC project, while the extracted knowledge must become permanent documentation.
- **Modularity**: Prefer updating existing files and avoid duplicate information. Keep documents concise.
- **Cross-Referencing**: Link related files where useful (using relative markdown links).
- **Git Commit Workflow**: Commit one documentation milestone at a time, using meaningful messages. Push changes after each completed milestone.

## Git Commit Conventions
- Milestone 1.1: `docs: add agent guide and update navigation`
- Milestone 2: `docs: add architectural decision records`
- Milestone 2.1: `docs: refactor folder structure to align with development lifecycle`
- Milestone 3: `docs: document secure media platform POC`
- Milestone 4: `docs: add media platform technical design`
- Milestone 5: `docs: refresh product documentation after architecture validation`
