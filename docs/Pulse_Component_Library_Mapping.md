# Pulse Component Library Mapping

## Назначение
Этот документ связывает `Figma` components с runtime-backed inventory из:
- `Docs/Done/UI_UX_Design_Package/Pulse_Shaped_Component_Inventory.md`
- `pulse-mobile-app/pulse-mobile-app/lib/app/theme/pulse_theme.dart` (Figma *Foundations* — цвет, тип, spacing, motion; в `MaterialApp` должны быть **и** `theme`, **и** `darkTheme` для согласованного day/night, см. Figma *Page 1* в `Pulse_Figma_Handoff_Contract.md`)
- `pulse-admin-console/pulse-admin-console/src/shared/ui/components.tsx`

## Shared Components
| Figma family | Runtime anchor | Notes |
| --- | --- | --- |
| Button | Flutter button patterns + `Button` in admin | variants: primary, secondary, ghost, destructive, disabled, loading |
| Input | `TextFormField` patterns + `Field`/`.input`/`.textarea` | include multiline and error |
| Card | Flutter `Card` + admin `Card` | default, warning, restricted, interactive |
| Inline message | mobile info/error cards + `InlineMessage` | info, warning, error, success |
| Badge | mobile status chips + `StatusBadge` | neutral, info, success, warning, danger |
| Confirm dialog | `AlertDialog` + `ConfirmDialog` | standard and destructive |

## Mobile-Specific Components
| Figma family | Runtime anchor | Notes |
| --- | --- | --- |
| Mobile foundations | `pulse_theme.dart` + `pulse_motion.dart` | `theme` + `darkTheme`; warm accent, deep neutrals, restrained motion |
| Brand mark | `PulseBrandMark` + `assets/brand/pulse_app_icon.png` | abstract pulse ring / time window; no text in icon |
| Hero visual | `PulseHeroVisual` | welcome, home, drop; brand signature without stock photos |
| Empty-state illustration | `PulseIllustration` | drop, chat, privacy, generic variants |
| Surface card / hero card | `PulseSurface` / `PulseCard` | home, drop, status, interactive cards |
| Empty state | `PulseEmptyState` | loading/empty/error with one primary next action |
| Runtime truth card | `settings_screen.dart` | local-first caveats must stay visible |
| Verification status tile | `verification_intro_screen.dart` | verification-lite, no fake KYC |
| Toggle row | `SwitchListTile` patterns | notification/privacy settings |
| Timed chat state block | chat screens | open, grace, expired, closed |

## Admin-Specific Components
| Figma family | Runtime anchor | Notes |
| --- | --- | --- |
| Sidebar nav | `app-shell.tsx` | permission-aware visibility |
| Metric tile | `Metric` | dashboard and launch ops |
| Queue row | `.queue-item` | moderation and slot lists |
| Permission state | `PermissionState` | no-access and partial-access |
| Audit timeline | `.timeline` | workflow trail |

## Mapping Rule
Новый visual component нельзя считать обязательным для runtime, пока:
1. он не появился в `Pulse_Shaped_Component_Inventory.md`;
2. не указан его use-case в mobile/admin backlog;
3. не понятно, как он снижает ambiguity, trust debt или operator error.
