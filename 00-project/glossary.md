# Glossary

This document serves as the canonical source of truth for domain terminology and business entities used throughout the Shruti Sadhana platform. All code structures, API responses, database schemas, and user interfaces must align with these terms.

---

## Core Entities & Concepts

### Course
* **Definition**: A complete spiritual text or stotram (e.g., *Lalita Sahasranama*). A Course is the highest level container in the content hierarchy.
* **MVP Scope**: Lalita Sahasranama is the only course available during the Phase 0 pilot.

### Section
* **Definition**: A logical group of shlokas within a Course, organized according to traditional chanting structures (e.g., *Nyasa*, *Dhyanam*, *Phalashruti*).
* **Hierarchy**: Course → Section → Shloka.

### Shloka
* **Definition**: A single Sanskrit verse or rhythmic couplet. This is the atomic learning, tracking, and progress unit within the application.

### Learning Session
* **Definition**: The active screen view and coordinate controller (`SS-008`) that guides the devotee through studying, practicing, and recording exactly **one Shloka** at a time. It decouples audio playback, text rendering, and recording controls.

### Daily Plan
* **Definition**: A dynamic progress container that defines the subset of Shlokas assigned to a devotee for a single day. The quantity of Shlokas in a Daily Plan is determined by the devotee's chosen pace (e.g., "Slow & Steady" = 3 Shlokas/day vs. "Balanced" = 7 Shlokas/day) selected in the Learning Plan Setup (`SS-005A`).

### Recording
* **Definition**: A user-captured audio file of their own chanting for a specific Shloka. Recordings are stored locally for self-review.

### Enrollment
* **Definition**: The relationship linking a devotee to a specific Course. It establishes their active progress state, current shloka position, and learning plan settings.

### Progress
* **Definition**: The completion metrics of a devotee. It is calculated strictly on the count of unique Shlokas completed (via the Samarpan offering). It is separated into **Today's Progress** (Shlokas completed today) and **Overall Progress** (cumulative Shlokas completed in the course).

### Samarpan
* **Definition**: The devotional act of offering the fruits of a chanting practice session to the Divine. In the app, this is the primary submit action at the end of a Learning Session, triggering the lotus offering animation (`SS-009`) and marking the Shloka as completed in the database.

### Support
* **Definition**: The voluntary contribution mechanism allowing devotees to financially support the app. Triggers periodically via a dedicated screen (`SS-010`) after completing every $N$ Shlokas.

### Script Preference
* **Definition**: The user's selection for displaying Sanskrit text. The supported script types are:
  1. **Devanagari** (Sanskrit script)
  2. **English Transliteration** (Phonetic Roman text)
