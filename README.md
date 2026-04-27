# pulse-ui-ux

## Назначение
`pulse-ui-ux` - это репозиторий для `Figma` handoff, design execution artifacts, annotations и review-ready материалов по `Phase 4`.

Это не новый product-canon и не новый runtime source of truth.

## Источники истины
Перед любым обновлением handoff нужно сверяться с:
- `Docs/Done/Rules_and_Standards/Docs/Done/Rules_and_Standards/Pulse_Phase_4_Design_Runtime_Truth_Map.md`
- `Docs/Done/Rules_and_Standards/Docs/Done/Rules_and_Standards/Pulse_Phase_4_Design_Drift_Checklist.md`
- `Docs/Done/UI_UX_Design_Package/UI_UX_Design_Package_Index.md`
- `Docs/Done/UI_UX_Design_Package/Pulse_Mobile_Screen_State_Backlog.md`
- `Docs/Done/UI_UX_Design_Package/Pulse_Admin_Screen_State_Backlog.md`
- `Docs/Done/UI_UX_Design_Package/Pulse_Shaped_Component_Inventory.md`
- `Docs/Done/UI_UX_Design_Package/Pulse_UI_Copy_And_Tone_Matrix.md`
- `pulse-mobile-app/pulse-mobile-app/lib/app/routing/app_router.dart`
- `pulse-admin-console/pulse-admin-console/src/app/router.tsx`
- `pulse-core-api/docs/openapi.json`

## Что Храним Здесь
- структуру `Figma` handoff;
- screen priority lists;
- component mapping для design work;
- annotations и visual review artifacts;
- exports и placeholders для handoff-пакетов.

## Что Не Храним Здесь
- переопределение backend/mobile/admin semantics;
- новые product promises без runtime confirmation;
- финальные бизнес-правила вместо governance/docs;
- фантазийные future features без пометки `mocked seam` или `future-backed`.

## Структура
- `docs/Pulse_Figma_Handoff_Contract.md`
- `docs/Pulse_Core_Flow_Screen_Priority_List.md`
- `docs/Pulse_Component_Library_Mapping.md`
- `exports/README.md`
- `annotations/README.md`

## Правила Работы
1. Если `Figma` или handoff расходятся с runtime, правдой считается runtime.
2. Каждый ключевой frame должен иметь ссылку на source backlog или runtime source.
3. Любой vendor-ready seam должен быть честно промаркирован.
4. Нельзя рисовать public trust badges, fake realtime или fake offline-first.