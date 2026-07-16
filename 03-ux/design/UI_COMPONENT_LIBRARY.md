# Reusable UI Component Library

Purpose: Reference specifications for reusable Flutter components. This bridges the UI design system tokens with actual widget implementation, ensuring code consistency.

---

## Component Index

### 1. SacredBackground (`SS-C-013`)
- **Purpose**: Unified screen background applying the Warm Ivory color and subtle textured watermarks.
- **Props (Parameters)**:
  - `Widget child`: Content displayed on top of the background.
  - `bool showWatermark`: Toggle to overlay the low-opacity Sri Yantra graphic.
- **States**: Stateless.
- **Reuse Locations**: All primary screens (`SS-001` through `SS-012`).
- **Design Tokens**: Background: Soft Sand (`#F5EFE6`) / Warm Ivory (`#FDFBF7`), Sri Yantra watermark opacity: `3%`.

### 2. SacredHeroHeader (`SS-C-001`)
- **Purpose**: Displays the devotional app greetings and user profile information.
- **Props**:
  - `String greetingText`: Text shown (default: `"Shree Maatre Namaha"`).
  - `String userName`: Display name of the user.
- **States**: Stateless.
- **Reuse Locations**: Home (`SS-003`), Progress (`SS-011`).
- **Design Tokens**: Font: Outfit Bold (Display Large), Color: Muted Maroon (`#801D24`).

### 3. SectionHeader (`SS-C-011`)
- **Purpose**: Labeled category divider displaying conch ornaments.
- **Props**:
  - `String title`: Title text of the section.
  - `bool showConchOrnaments`: Option to show custom outline conch icons on sides.
- **States**: Stateless.
- **Reuse Locations**: Home (`SS-003`), Library (`SS-004`), Course Details (`SS-005`).
- **Design Tokens**: Font: Outfit SemiBold (Title Large), Color: Devotional Charcoal (`#2B2B2B`).

### 4. ContinueLearningCard (`SS-C-002`)
- **Purpose**: Main hero card directing users back to their last chanting session.
- **Props**:
  - `String stotraTitle`: Title of the course (e.g., Lalita Sahasranama).
  - `String sectionTitle`: Active section name (e.g., Nyasa).
  - `int shlokaNumber`: Shloka index.
  - `double progressFraction`: Completion percentage value (0.0 to 1.0).
  - `VoidCallback onTap`: Route action handler.
- **States**: Stateless.
- **Reuse Locations**: Home (`SS-003`).
- **Design Tokens**: Surface: Soft Sand (`#F5EFE6`), Border: 1dp Antique Gold (`#D4AF37`), Corner Radius: `r-12`.

### 5. SacredStatCard (`SS-C-003`)
- **Purpose**: Display numerical progress values (e.g., chanting minutes, shlokas completed).
- **Props**:
  - `String value`: Text number (e.g., `"12"`).
  - `String label`: Short context text (e.g., `"Shlokas Today"`).
  - `IconData icon`: Icon mapping representing the stat.
- **States**: Stateless.
- **Reuse Locations**: Home (`SS-003`), Progress (`SS-011`).
- **Design Tokens**: Surface: Warm Ivory (`#FDFBF7`), Card Border: 1dp Sand, Corner Radius: `r-8`.

### 6. ManuscriptCard (`SS-C-004`)
- **Purpose**: Renders the shloka text and transliteration block inside a simulated palm-leaf container.
- **Props**:
  - `String devanagariText`: Core Sanskrit verse.
  - `String transliterationText`: English phonetic transliteration.
  - `String translationText`: Sanskrit meaning translation.
  - `VoidCallback onGearTap`: Event when the script/font config gear is tapped.
- **States**: Stateless.
- **Reuse Locations**: Learning Session (`SS-008`), Onboarding Vakratunda Experience (`SS-003`).
- **Design Tokens**: Surface: Warm Ivory, Borders: 1dp Muted Maroon, Font: Yatra One / Noto Sans Devanagari, Corner Radius: `r-24`.

### 7. AudioPlayer (`SS-C-005`)
- **Purpose**: Unified progress player bar for stotra streaming.
- **Props**:
  - `String audioUrl`: Resource streaming path.
  - `bool isPlaying`: Active state.
  - `Duration currentPos`: Elapsed track duration.
  - `Duration totalDuration`: Track length.
  - `ValueChanged<Duration> onSeek`: Seek event logic.
- **States**: Streams track position updates.
- **Reuse Locations**: Learning Session (`SS-008`), Vakratunda Experience (`SS-003`).
- **Design Tokens**: Controls: Muted Maroon (`#801D24`), Track: Antique Gold (`#D4AF37`) / Soft Sand, Spacing: 16dp.

### 8. RecordingCard (`SS-C-006`)
- **Purpose**: Renders user-recorded chanting clips with play and delete triggers.
- **Props**:
  - `String recordingId`: Unique key of the audio track.
  - `Duration duration`: Audio track length.
  - `VoidCallback onDelete`: Deletion logic handler.
  - `VoidCallback onPlay`: Playback trigger handler.
- **States**: State tracking for active playback (`isPlaying`).
- **Reuse Locations**: Learning Session (`SS-008`).
- **Design Tokens**: Surface: Warm Ivory, Border: 1dp Sand, Suffix icon: Muted Maroon.

### 9. SamarpanButton (`SS-C-007`)
- **Purpose**: Primary action button to submit chants and initiate devotional offerings.
- **Props**:
  - `String text`: Button text (default: `"🌸 Samarpan"`).
  - `VoidCallback onPressed`: Trigger logic handler.
- **States**: Stateless.
- **Reuse Locations**: Learning Session (`SS-008`).
- **Design Tokens**: Background: Muted Maroon (`#801D24`), Text: Warm Ivory (`#FDFBF7`), Font: Outfit SemiBold (Label Large), Corner Radius: `r-8`.

### 10. ProgressTile (`SS-C-008`)
- **Purpose**: ListItem display for completed sections in the course menu.
- **Props**:
  - `String title`: Section title name.
  - `bool isCompleted`: Toggle completion icon display.
  - `double completionFraction`: Section completion value.
- **States**: Stateless.
- **Reuse Locations**: Course Details (`SS-007`), Lesson Index (`SS-008`).
- **Design Tokens**: Active Color: Antique Gold (`#D4AF37`), Font: Inter (Body Medium).

### 11. NativeAdCard (`SS-C-009` / `SS-104`)
- **Purpose**: Standard container for rendering Native Advertisements.
- **Props**:
  - `String adId`: Key identifier.
- **States**: Active loading states.
- **Reuse Locations**: Home (`SS-003`).
- **Design Tokens**: Border: 1dp Soft Sand, Background: Warm Ivory, Corner Radius: `r-12`.

### 12. SupportCard (`SS-C-010`)
- **Purpose**: Invitation card for collecting devotee contributions/donations.
- **Props**:
  - `String message`: Context invitation text.
  - `VoidCallback onSupportTap`: Launch donation gateway trigger.
- **States**: Stateless.
- **Reuse Locations**: Home (`SS-003`), Support Screen (`SS-010`).
- **Design Tokens**: Surface: Soft Sand, Border: 1dp Antique Gold, Corner Radius: `r-12`.
