# AI_CONTEXT.md

# Shruti Sadhana

Version: 1.0

Purpose: Quick briefing for AI assistants. Contains project facts, accepted decisions, scope boundaries, and implementation context.

---

## Project Summary

| Item             | Value                                                  |
| ---------------- | ------------------------------------------------------ |
| Product          | Sanskrit Stotra Learning, Practice & Parayana Platform |
| Domain           | Stotra / Stotram Chanting                              |
| Primary Platform | Mobile App                                             |
| Status           | MVP Planning                                           |

---

## Core User Journey

```text
Learn
  ↓
Practice
  ↓
Parayana
```

---

## MVP Modules

| Module                 | Included |
| ---------------------- | -------- |
| Authentication         | Yes      |
| User Preferences       | Yes      |
| Course Catalog         | Yes      |
| Course Detail          | Yes      |
| Shloka Learning        | Yes      |
| Audio Playback         | Yes      |
| Recording & Playback   | Yes      |
| Progress Tracking      | Yes      |
| Notifications          | Yes      |
| Poojaghar              | Yes      |
| Participatory Chanting | Yes      |
| Referrals              | Yes      |
| Donations              | Yes      |

---

## Deferred Features

| Feature                     | Status       |
| --------------------------- | ------------ |
| AI Pronunciation Assessment | Future Phase |
| Deep Meanings & Commentary  | Future Phase |
| Offline Mode                | Future Phase |

---

## Content Structure

```text
Course
 └── Section
       └── Shloka
```

Examples:

### Lalita Sahasranama

* Dhyana
* Nyasa
* Main Sahasranama
* Uttara Nyasa

### Vishnu Sahasranama

* Dhyana
* Nyasa
* Main Sahasranama
* Phalashruti

---

## Sanskrit Guru Model

| Item              | Value          |
| ----------------- | -------------- |
| Real Teachers     | Yes            |
| Recorded Chanting | Yes            |
| Recorded Guidance | Yes            |
| Live Teaching     | No             |
| Multiple Teachers | Future Support |

---

## Accepted Decisions

| Decision                               | Status   |
| -------------------------------------- | -------- |
| Flutter Frontend                       | Accepted |
| NestJS Backend                         | Accepted |
| MySQL Database                         | Accepted |
| Firebase Authentication                | Accepted |
| Single Deity Per Poojaghar Session     | Accepted |
| Participatory Chanting Included In MVP | Accepted |
| Referral Tree Included In MVP          | Accepted |
| AI Pronunciation Deferred              | Accepted |

---

## Technology Stack

| Area           | Technology                       |
| -------------- | -------------------------------- |
| Frontend       | Flutter                          |
| Backend        | NestJS                           |
| Database       | MySQL                            |
| Authentication | Firebase Auth                    |
| Notifications  | Firebase Cloud Messaging         |
| Storage        | Firebase Storage / Cloud Storage |
| Hosting        | Google Cloud                     |

---

## Documentation Reading Order

### Product Understanding

```text
AI_CONTEXT.md
↓
project-context.md
↓
PRD.md
```

### Feature Design

```text
AI_CONTEXT.md
↓
Relevant FRD
↓
Relevant TDS
```

### Feature Implementation

```text
AI_CONTEXT.md
↓
Relevant FRD
↓
Relevant TDS
↓
Architecture Documents
```

---

## Guidelines For AI Assistants

1. Read only relevant documents.
2. Follow documented architecture and conventions.
3. Treat ADRs as source of truth for accepted decisions.
4. Do not treat research notes or brainstorming discussions as requirements.
5. Prefer extending existing patterns over introducing new patterns.
