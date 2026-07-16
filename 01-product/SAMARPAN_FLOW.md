# Product Feature Specification: Samarpan Flow

**Status:** ✅ Concept Frozen

---

## 1. Devotional Philosophy

In traditional Indian chanting, learning and practicing a stotram is not a mechanical checkbox exercise. Chanting culminates in an offering (**Samarpan**) of the practice to the Divine. 

The **Samarpan Flow** is the core loop of Shruti Sadhana. Instead of a simple "Lesson Completed" message, the devotee actively offers their chanting effort. This reinforces the sacred atmosphere and anchors the practice in devotion rather than digital achievements.

---

## 2. Core User Flow

The learning loop progresses through the following steps:

```text
Listen (SS-008 Audio play)
        ↓
Chant (SS-008 Practice)
        ↓
Record (SS-008 Chanting capture)
        ↓
Review (Evaluate recordings)
        ↓
🌸 Samarpan (Devotional submission trigger)
        ↓
Samarpan Screen (SS-009 Lotus bloom animation)
        ↓
Next Shloka / Transition Point
        ↓
(Optional) Support Shruti Sadhana (SS-010 Periodic contribution invitation)
        ↓
Next Shloka Session (SS-008)
```

---

## 3. Screen Specifications

### 3.1 Samarpan Screen (SS-009)
- **Visuals**: A clean screen with a Warm Ivory background. Features a stylized hand-painted **Lotus bloom animation** (using the Lotus illustration asset) unfolding over 1.5 seconds.
- **Content**: 
  - Displays a devotional statement: *"Chant offered with devotion. May the practice purify mind and speech."*
  - Offers a clean primary action: `Next Shloka` (routes directly to the next lesson page) and `Return to Index` (routes back to SS-007 All Shlokas Index).
- **Decisions**: This screen must feel tranquil. It must have no notifications, gamified stats, or advertisements.

### 3.2 Support Shruti Sadhana (SS-010)
- **Visuals**: Simple card layout styled in Muted Maroon and Sand containers.
- **Content**:
  - Displays a respectful, warm invitation text inviting devotees to voluntarily support the continued development and preservation of Shruti Sadhana.
  - Action buttons: `Support Shruti Sadhana` (launches payment channel) and `Skip / Continue Learning` (returns to the study sequence).
- **Boundary**: Contributions are strictly voluntary (soft paywall). Skipping returns the devotee to their active learning course instantly.

---

## 4. Monetization & Trigger Rules

To support infrastructure costs without compromising user focus, we apply strict rules for display triggers:

- **Periodic Frequency**: The Support Shruti Sadhana screen (SS-010) is displayed periodically. It appears after a devotee completes **every N shlokas** (e.g., after completing 5 shlokas).
- **Remote Configurable**: The interval parameter `N` is set dynamically on the backend and fetched at application launch.
- **Ads Restriction**: Native ads (`SS-104`) may only reside on the Home Screen (`SS-003`) dashboard scroll.
- **Chanting Protection**: Ads and support requests must never interrupt the active chanting or learning sessions. Specifically, **no ads, banners, or popups may ever be rendered during the Learning Session (SS-008) or the Samarpan Screen (SS-009).**
