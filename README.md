# Shruti Sadhana Documentation Repository

This repository is the single source of truth for Shruti Sadhana product, UX, architecture, and engineering decisions.

## Start Here

Use [PROJECT_INDEX.md](PROJECT_INDEX.md) as the root navigation document for this repository.

For AI assistant guidance, see:
- [AI_CONTEXT.md](AI_CONTEXT.md)
- [00-project/CODEX_CONTEXT.md](00-project/CODEX_CONTEXT.md)

For accepted decisions, see:
- [00-project/DECISION_LOG.md](00-project/DECISION_LOG.md)

## Recommended Reading Order

1. [PROJECT_INDEX.md](PROJECT_INDEX.md)
2. [AI_CONTEXT.md](AI_CONTEXT.md)
3. [00-project/CODEX_CONTEXT.md](00-project/CODEX_CONTEXT.md)
4. [00-project/project-context.md](00-project/project-context.md)
5. [01-product/PRD.md](01-product/PRD.md)
6. [01-product/MVP-SCOPE.md](01-product/MVP-SCOPE.md)
7. [02-features/FEATURE_INDEX.md](02-features/FEATURE_INDEX.md)
8. Relevant folder `INDEX.md` files
9. Relevant FRDs
10. Relevant architecture and technical design documents

## Documentation Hierarchy

### Why are we building this?

[00-project/project-context.md](00-project/project-context.md)

Contains:
- Vision
- Problem statement
- Philosophy
- Personas
- Positioning

### What product are we building?

[01-product/PRD.md](01-product/PRD.md)

Contains:
- Product goals
- Product scope
- Success metrics
- Release criteria

### What is included in MVP?

[01-product/MVP-SCOPE.md](01-product/MVP-SCOPE.md)

Contains:
- Included features
- Excluded features
- Launch scope
- Future phases

### What features exist?

[02-features/FEATURE_INDEX.md](02-features/FEATURE_INDEX.md)

Contains:
- Feature inventory
- Dependencies
- Status tracking
- Future features

### How should features behave?

FRD documents define feature behavior.

Examples:
- Authentication
- Onboarding
- Learning Library
- Lesson Experience
- Puja Ghar
- Subscriptions

### How should features be implemented?

Architecture and technical design documents define implementation direction.

## Documentation Principles

- Keep documents focused and modular.
- Avoid large monolithic specifications.
- Separate vision, scope, requirements, and implementation.
- Record accepted decisions explicitly.
- Treat documentation as a living system.

## AI Workflow

Product decisions:
AI_CONTEXT -> Project Context -> PRD -> MVP Scope -> Feature Index

Feature design:
AI_CONTEXT -> Feature Index -> FRD

Implementation:
AI_CONTEXT -> FRD -> Architecture Documents -> Code

This repository contains documentation only and remains the authoritative source for Shruti Sadhana product and engineering decisions.

