# UI Design System

Purpose: The single source of truth for the visual style guidelines, component layouts, design tokens, and frontend implementation patterns of the Shruti Sadhana application.

---

## 1. Design Philosophy (Sacred Editorial Design)

Shruti Sadhana follows a **Sacred Editorial Design** language. The visual interface is heavily inspired by traditional Indian sacred manuscripts, classical temple architecture, and premium editorial print layouts while remaining structurally compliant with Material 3 patterns. 

The UI is designed to always feel:
- **Sacred**
- **Calm**
- **Premium**
- **Elegant**
- **Warm**
- **Minimal**
- **Focused** (Every screen must have **one primary purpose**)

The primary rule of this philosophy is that **the UI must disappear behind the sacred content rather than compete with it.** Avoid information overload. Encourage devotion and consistency rather than gamification.

---

## 2. Design Principles

- **Sacred Restraint**: Avoid loud gradients, heavy neon shades, or flashy elements. Animations must be slow, fluid, and minimal.
- **Content is Divine**: The Sanskrit stotra text is the hero. Margins, layout constraints, and text scaling must optimize shloka legibility.
- **Atmospheric Devotion**: Color palettes, iconography, and illustration backdrops should evoke a quiet, warm temple environment using watercolor spiritual illustrations and manuscript-inspired aesthetics.
- **Spiritual Visuals over Generic Controls**: Spiritual illustrations (Mala, Havan Kund, Agarbatti, Manuscript) are preferred over generic Material Design icons. Ornamentation is kept to a minimum with generous whitespace.
- **Accessibility for Devotees**: Keep buttons large, tap targets clear, and ensure high contrast ratios to accommodate older devotees. Primary call-to-actions (CTAs) utilize the Muted Maroon shade.

---

## 3. Visual Tokens

### 3.1 Color Palette

| Name | Role | HEX | Description |
| :--- | :--- | :--- | :--- |
| **Warm Ivory** | Primary Background | `#FDFBF7` | Resembles palm-leaf and aged paper manuscripts. |
| **Muted Maroon** | Primary Brand / Headers | `#801D24` | Traditional vermillion/kumkum shade for titles and actions. |
| **Antique Gold** | Accents / Highlights | `#D4AF37` | Used sparingly for borders, active tabs, and highlights. |
| **Devotional Charcoal**| Body text | `#2B2B2B` | Soft off-black that maintains contrast without harsh glare. |
| **Sacred Amber** | Warning / Alerts | `#D97706` | Soft amber representing deepam light for caution states. |
| **Soft Sand** | Cards / Surface containers | `#F5EFE6` | Background card surfaces. |

### 3.2 Typography

We use Google Fonts (**Outfit** for headers/numbers, **Inter** for descriptions/English transliteration, and **Yatra One** or **Noto Sans Devanagari** for Sanskrit verses).

| Token | Family | Weight | Size | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **Display Large** | Outfit | Bold (700) | 32pt | App greetings, hero titles |
| **Display Medium** | Noto Sans Devanagari | Regular (400) | 26pt | Sanskrit shloka verses |
| **Title Large** | Outfit | SemiBold (600) | 20pt | Course titles, screen headers |
| **Body Large** | Inter | Medium (500) | 16pt | English transliterations |
| **Body Medium** | Inter | Regular (400) | 14pt | Sanskrit shloka meanings, bios |
| **Label Large** | Outfit | SemiBold (600) | 14pt | Button text, segment labels |

### 3.3 Spacing, Grid & Corner Radius

- **Grid Layout**: 4-column layout for mobile views, 8-column layout for tablet/web views.
- **Core Padding**: 16dp and 24dp are the standard layout margins.
- **Spacing Scale**: 4dp (micro-spacing), 8dp (component elements), 16dp (card padding), 24dp (section gaps), 32dp+ (hero gaps).
- **Corner Radius**:
  - Small Elements (input fields, buttons): 8dp (`r-8`)
  - Medium Elements (cards, lists, modals): 12dp (`r-12`)
  - Large Elements (bottom sheets, hero banners): 24dp (`r-24`)

---

## 4. Components & Iconography

- **Iconography**: Use sharp, thin Material Symbols or custom outline SVGs. Avoid solid/filled icon variations unless indicating an active state (e.g., active navigation icon).
- **Custom Icons**:
  - `Mala`: Chanting count
  - `Conch`: Devotional section
  - `Deepam`: Active lesson indicators
- **Borders**: Thin outlines (`1dp`) in Muted Maroon or Antique Gold are preferred over heavy shadows to separate layout cards.

---

## 5. Motion & Transitions

- **Transitions**: Screen routing should employ a slow fade-in transition (300ms duration) rather than rapid sliding animations.
- **Micro-interactions**: Action feedback (such as tapping the play button or recording audio) should use subtle scale triggers (e.g., shrinking by 2% on press) to feel organic.

---

## 6. Do's & Don'ts

### Do
- Always surround Sanskrit verses with generous whitespace.
- Use warm ivory instead of pure white `#FFFFFF` for screen backgrounds.
- Keep contrast ratios for text at a minimum of 4.5:1.

### Don't
- Never place neon badges, streak count fire emojis, or competitive gamified items in the user path.
- Avoid banner advertisements or pop-up notices in the middle of a chanting or learning session.
- Never use complex hierarchies; routing should feel flat and clean.
