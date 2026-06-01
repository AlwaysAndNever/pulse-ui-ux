# Pulse Web Split Rail Pattern

## Canonical links (не отдельная design truth)

**Program / governance (канон поведения и границ):**

- `Docs/Rules_and_Standards/Pulse_Web_Split_Rail_Layout_Guideline.md` — когда rail обязателен, breakpoints, a11y, токены.
- Roadmap: `Docs/ROADMAP/Execution/Pulse_Remaining_Work_Mobile.md`, `Docs/ROADMAP/Execution/Pulse_Remaining_Work_Admin.md` (в т.ч. admin shell: persistent nav + long-scroll).

**Реализация (проверка соответствия коду):**

- Mobile: `pulse-mobile-app/pulse-mobile-app/lib/app/widgets/web_split_rail.dart`, `pulse_theme.dart` (`PulseWebLayout`), `pulse_motion.dart`.
- Admin shell (фиксированный sidebar + отдельный скролл контента):
  - стили: `pulse-admin-console/pulse-admin-console/src/index.css` (классы `app-shell`, медиазапросы для узких ширин);
  - разметка и навигация: `pulse-admin-console/pulse-admin-console/src/app/app-shell.tsx` (`sidebar-link`, карточка скрытых разделов по RBAC);
  - маршруты и guards чувствительных поверхностей: `pulse-admin-console/pulse-admin-console/src/app/router.tsx` (модерация / фото / верификация / аудит / launch-ops разделены `Navigate` + проверками прав).
  - см. README консоли — оболочка **визуальная**; operator proof остаётся на API и страницах доменов.

**Документы продукта / mobile:**

- `pulse-mobile-app/pulse-mobile-app/docs/mobile_architecture.md` — раздел про web desktop layout.
- Аналитика rail: `pulse-mobile-app/pulse-mobile-app/docs/alpha_analytics_event_map.md`.

Этот файл — **UX/IA deep-dive**; при конфликте приоритет у **кода**, затем у guideline в `Docs/`.

## Scope

Паттерн фиксированного rail для web-поверхностей:

- левый rail: компактная навигация и подсказки готовности;
- правая панель: основной скроллируемый контент задачи.

Привязка к токенам и брейкпоинтам в рантайме; не допускать расхождения с кодом.

## Runtime anchors

**Mobile (Flutter):**

- `pulse-mobile-app/lib/app/theme/pulse_theme.dart` (`PulseWebLayout`, `PulseSpacing`)
- `pulse-mobile-app/lib/app/theme/pulse_motion.dart` (`PulseMotion`)
- `pulse-mobile-app/lib/app/widgets/web_split_rail.dart`

**Admin (React):**

- `pulse-admin-console/pulse-admin-console/src/index.css` — сетка shell, ширина rail (`--shell-sidebar-width`), `100vh` / внутренние скроллы; одноколоночный режим при `max-width: 1200px` (в guideline для mobile часто порог `1120px` — осознанное отличие; при унификации менять оба слоя согласованно).
- `pulse-admin-console/pulse-admin-console/src/app/app-shell.tsx` — список `navigationItems` + `hasAnyPermission`, карточка «скрытые разделы».
- `pulse-admin-console/pulse-admin-console/src/app/router.tsx` — `ProtectedLayout`, отдельные guards для `/moderation`, `/profile-photo-moderation`, `/verification`, `/audit`, `/launch-ops`.

## Visual and interaction principles

- Спокойный premium-стиль: минимум motion, ясная иерархия, сдержанные градиенты.
- Действия в rail короткие и явные; без перегруза текстом.
- Блокеры готовности — понятный UX-текст, не сырые коды с бэкенда.
- Неизвестные коды блокеров с бэкенда — безопасный общий fallback в копирайте.

## Breakpoint policy

- Split rail на desktop-ширинах (в Flutter — от `1120px`; в admin CSS одноколонка при ширине до `1200px` — см. выше).
- Одноколоночная вёрстка для tablet/mobile.

## Accessibility

- Элементы rail доступны с клавиатуры.
- Семантические подписи для быстрых действий и чипов блокеров.
- Контраст WCAG AA для текста/иконок на тонированных карточках.

## QA checklist (общий)

- Desktop: rail визуально фиксирован, пока скроллится правая панель.
- Tablet/mobile: нет регрессии single-column.
- Home и Settings на mobile используют те же spacing/motion токены (где применимо).
- Чипы причин блокировки маппятся на локализованный копирайт (`RU/EN/BE`), включая fallback для неизвестных кодов.

## QA checklist (admin console)

- **RBAC в навигации:** без прав на раздел ссылки в `sidebar-nav` отсутствуют; названия скрытых разделов видны только в раскрываемом блоке «Скрытые разделы» (не подменяют прямой доступ к маршруту).
- **Согласованность с router:** прямой заход по URL на `/audit`, `/verification`, `/moderation` и т.д. без права ведёт на редирект guard’ом, а не только скрытием ссылки.
- **Разделение доменов:** копирайт и потоки модерации контента, проверки фото, верификации аккаунтов и **журнала аудита** не смешиваются на одном экране без явного сценария; журнал аудита не притворяется очередью модерации.
- **Чувствительные действия:** подтверждения/причины на страницах доменов сохраняются; смена layout не убирает дисциплину ввода при write-операциях.
- **Ошибки и восстановление:** `authError` в `main.app-shell__content`, состояние «сессия не подтверждена» из `router`/`ProtectedLayout`, кнопки обновления сессии и выхода остаются доступны и читаемы при скролле.
- **Ручной прогон shell:** focus visibility, клавиатура, zoom браузера, длинный сайдбар, полосы прокрутки Windows — см. комментарий у `.app-shell` в `pulse-admin-console/.../src/index.css`.
- **Workflow / RBAC артефакты:** пошаговые сценарии модерации и верификации, журнал vs workflow audit, матрица read-limited, ошибки оператора и таблица автотестов — `pulse-admin-console/pulse-admin-console/docs/operator_workflows_and_rbac_test_plan.md`.
