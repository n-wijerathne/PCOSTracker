# Theme Colors and Font Selection

## Theme Colors

| Color Role | Hex Code | Sample | Usage |
|---|---:|---|---|
| Primary Color | `#8B7BB5` | Purple | Main buttons, selected tabs, active bottom navigation, app heart logo, floating action button |
| Secondary Color | `#4DB6AC` | Teal | Meal tracking, yoga category, completed meal states, healthy food guide accents |
| Tertiary Color | `#E57373` | Soft Red | Period tracking, period warnings, period cards, delete/history accents |
| App Background | `#F0EEF8` | Light Purple | Main background color for almost all app screens |
| Primary Light | `#EDE9F7` | Light Purple Card | Icon backgrounds, selected badges, soft purple highlights |
| Secondary Light | `#E0F5F1` | Light Teal | Meal cards, yoga cards, healthy food choice cards |
| Red Light | `#FFEBEE` | Light Red | Period icon backgrounds, warning/avoid cards |
| White | `#FFFFFF` | White | Cards, input boxes, bottom navigation, report panels |
| Dark Text | `#2D2D2D` | Dark Grey | Main headings, card titles, important text |
| Body Text | `#444444`, `#555555`, `#666666` | Grey | Paragraphs, report details, guide content |
| Muted Text | `#888888`, `#AAAAAA` | Light Grey | Captions, subtitles, inactive labels, helper text |
| Success Green | `#4CAF50`, `#66BB6A` | Green | Normal cycle messages, positive/complete states |
| Warning Orange | `#FFB74D` | Orange | Partial/medium status and cycle scale warning area |
| Overlay Dark | `#DD000000` / gradient | Black overlay | Used over image banners to keep white text readable |

## Sample Swatch

| Primary | Secondary | Tertiary | Background | Text |
|---|---|---|---|---|
| `#8B7BB5` | `#4DB6AC` | `#E57373` | `#F0EEF8` | `#2D2D2D` |

## Font Selection

The PCOS Tracker app uses Android's default system sans-serif font family. The system font was selected because it is readable, lightweight, and consistent with native Android UI behavior.

| UI Area | Font Type | Style | Color | Size | Example Screens |
|---|---|---|---:|---:|---|
| Main Screen Title | Android system sans-serif | Bold | `#2D2D2D` | 24sp-28sp | Home, Reports, Settings, Tracking hubs |
| Page Subtitle | Android system sans-serif | Regular | `#888888` | 13sp-14sp | Reports hub subtitle, Privacy Policy subtitle |
| Card Title | Android system sans-serif | Bold | `#2D2D2D` | 16sp-20sp | Period Report, Meal Report, Workout Report cards |
| Card Body Text | Android system sans-serif | Regular | `#444444` / `#555555` | 13sp-15sp | Guides, reports, meal details |
| Small Labels | Android system sans-serif | Regular / Uppercase | `#AAAAAA` / module color | 10sp-12sp | Date labels, category labels, weekly labels |
| Button Text | Android system sans-serif | Bold / uppercase | `#FFFFFF` | 14sp-16sp | Save Changes, Save Period, Login |
| Bottom Navigation | Android system sans-serif | Regular | Navigation tint selector | 10sp-12sp | Home, Calendar, Add, Reports, Settings |
| Numeric Report Values | Android system sans-serif | Bold | Module color | 18sp-28sp | Meal count, workout percentage, period day |
| Input Text | Android system sans-serif | Regular | `#2D2D2D` | 14sp-15sp | Login, Personal Information, Period notes |

## Font Rationale

- **Readability:** System sans-serif works well on mobile screens and supports different Android display sizes.
- **Consistency:** The same font family is used across all screens to keep the app professional and simple.
- **Hierarchy:** Larger bold text is used for screen titles, medium bold text for cards, and smaller muted text for descriptions.
- **Accessibility:** Strong contrast is used between text and background, especially on white cards and image overlays.
