# Pulse Figma Handoff Contract

## Назначение
Этот документ переводит `Docs/Done/UI_UX_Design_Package/Pulse_Figma_Handoff_Brief.md` в рабочий handoff-контракт для `pulse-ui-ux`.

## Структура Файла `Figma`
### Page 1. Foundations
- colors;
- typography;
- spacing;
- radius;
- elevation/borders;
- semantic tokens;
- motion tokens.

### Page 2. Components
- buttons;
- inputs;
- toggles;
- banners/messages;
- cards;
- list rows;
- badges;
- dialogs/sheets;
- countdown/timed status block;
- sidebar nav;
- table/filter primitives.

### Page 3. Mobile Flows
- bootstrap and auth;
- onboarding;
- home and drops;
- match and timed chat;
- settings and trust.

### Page 4. Admin Flows
- login and recovery;
- dashboard;
- moderation queue and case detail;
- verification lookup and resolution;
- launch ops;
- permission-restricted and error states.

### Page 5. Brand And Marketing
- moodboard;
- hero direction;
- promo blocks;
- app store visuals;
- screenshots framing.

### Page 6. Annotations And Rules
- product constraints;
- backend truth notes;
- local-first vs offline-first clarification;
- unsupported states and caveats;
- content rules.

## Обязательные Пометки
Каждый ключевой frame должен содержать:
- purpose;
- entry point;
- primary CTA;
- required states;
- runtime constraints;
- unsupported assumptions to avoid.

## Маркировка Seam-ов
Использовать только такие пометки:
- `mocked seam`
- `future-backed`
- `not in current runtime`

## Обязательные Caveats Для Phase 4
- `Pulse` не offline-first;
- chat пока `polling-first`;
- verification трактуется как `verification-lite`;
- notification settings локальны и не равны provider-backed delivery policy;
- admin surfaces строятся вокруг `RBAC` и audit;
- public trust badges запрещены.
