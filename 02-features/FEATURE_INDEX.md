# Feature Index

## Purpose

This document is the master inventory of product features.

Every significant feature should have:

- A unique feature ID
- A status
- An owner (optional)
- A corresponding FRD

The Feature Index acts as the single source of truth for scope management.

---

# Status Definitions

| Status | Meaning |
|----------|----------|
| Planned | Approved but not yet specified |
| In Design | UX and requirements being defined |
| Ready | FRD completed and ready for implementation |
| In Development | Actively being built |
| Completed | Delivered |
| Deferred | Explicitly postponed |
| Future | Outside MVP |

---

# MVP Features

| ID | Feature | Status | FRD |
|----|----------|----------|----------|
| FRD-001 | Authentication | Planned | FRD-001-Authentication.md |
| FRD-002 | Onboarding | Planned | FRD-002-Onboarding.md |
| FRD-003 | Home Dashboard | Planned | FRD-003-Home.md |
| FRD-004 | Learning Library | Planned | FRD-004-Learning-Library.md |
| FRD-005 | Course Detail | Planned | FRD-005-Course-Detail.md |
| FRD-006 | Lesson Experience | Planned | FRD-006-Lesson-Experience.md |
| FRD-007 | Puja Ghar | Planned | FRD-007-Puja-Ghar.md |
| FRD-008 | Progress Tracking | Planned | FRD-008-Progress.md |
| FRD-009 | Subscription & Paywall | Planned | FRD-009-Subscriptions.md |
| FRD-010 | Profile & Settings | Planned | FRD-010-Profile.md |

---

# Future Features

## AI Learning Features

| ID | Feature | Status |
|----|----------|----------|
| FUT-001 | Pronunciation Feedback | Future |
| FUT-002 | Pronunciation Scoring | Future |
| FUT-003 | Sanskrit Alignment Engine | Future |
| FUT-004 | Personalized Learning Suggestions | Future |

---

## Community Features

| ID | Feature | Status |
|----|----------|----------|
| FUT-101 | Practice Groups | Future |
| FUT-102 | Community Discussions | Future |
| FUT-103 | Guided Group Parayan | Future |
| FUT-104 | Community Challenges | Future |

---

## Content & Learning Expansion

| ID | Feature | Status |
|----|----------|----------|
| FUT-201 | Learning Paths | Future |
| FUT-202 | Structured Sadhana Programs | Future |
| FUT-203 | Advanced Progress Insights | Future |
| FUT-204 | Live Learning Events | Future |

---

# Dependency Notes

Core dependency chain:

Authentication
→ Onboarding
→ Home
→ Learning Library
→ Course Detail
→ Lesson Experience
→ Progress Tracking

Puja Ghar integrates with Home and Lesson Experience.

Subscriptions integrate with Learning Library and Lesson Experience.

---

# Governance Rules

1. Every feature must have a unique ID.
2. Every MVP feature must have an FRD.
3. Scope changes must be reflected here before implementation begins.
4. Deferred features should not appear inside MVP FRDs.
5. This file is the authoritative feature inventory for Shruti Sadhana.