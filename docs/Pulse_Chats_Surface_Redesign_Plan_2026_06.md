# Pulse — Chats Surface Redesign Plan (2026-06)

**Статус:** approved design direction, готов к реализации разработчиком.
**Автор ревью:** дизайн-ревью экрана «Чаты» (mobile + web), июнь 2026.
**Scope:** весь surface «Чаты» — пустое состояние, список, тайлы, фильтры, поиск,
loading/error и **экран переписки** (timed chat thread). Реализацию ведёт разработчик;
этот документ — single source of truth для редизайна.

> Runtime truth остаётся Flutter + `pulse-core-api`. OD HTML — визуальный референс,
> не источник истины. При расхождении с этим планом — приоритет у кода и у этого документа.

## 0. Зачем (проблема)

Технически surface сделан крепко (фильтры, пагинация, grace-кольца, аналитика,
web-split, motion-tiers, a11y-семантика — всё есть). Проблема — **визуальная режиссура
и когезия**:

1. **Фоны не связаны.** Онбординг/сплэш — тёплая dawn-aurora (peach→сирень) + живой ECG.
   Продукт (`Чаты`/`Home`/`Drop`) — плоская кремовая бумага (`PulseProductSurfaces`,
   `#F7F5F2 → #F0EDE8`). Между слоями нет визуального моста → ощущение «двух приложений».
2. **Пустой экран не продаёт ядро (DROP).** Центр композиции — серый орб-плейсхолдер
   (читается как «не загрузился аватар»); главный крючок — обратный отсчёт до ближайшего
   DROP — спрятан ниже сгиба.
3. **Шум на пустом состоянии.** Фильтр-бар (`Все/Активные/Закрытые`) рендерится при 0 чатов —
   фильтровать нечего.
4. **Экран переписки сырой:** «шапка» из голого `Row` (back + sticky-bar + `more_vert`),
   без аватара собеседника и визуального таймера; пузыри/композер дефолтно-материальные.

## 1. Дизайн-направление: «Dawn Bridge» (одно приложение)

Решение: **не тащить полную dawn-aurora в продукт** (она убивает читаемость списков и
дорогая на web), а ввести **облегчённую подпись рассвета**, общую для онбординга и продукта.

Четыре связующих элемента (часть уже в коде):

| Связка | Онбординг (есть) | Продукт сейчас | Решение |
|---|---|---|---|
| **Шрифты** | Literata / Manrope | Literata / Manrope | ✅ уже едины — закрепить |
| **Акцент** | Pulse blue `#0075de` + glow | bronze `#C49A6C` (иконка) + blue в тайлах | Primary = blue («живой пульс»), bronze — тёплый вторичный |
| **Фон** | full dawn-aurora + grain | плоская бумага | **Dawn-wash сверху:** 1 мягкий тёплый радиал (`#FFE7C8`→прозрачный) за masthead поверх бумаги; низ остаётся бумажным для читаемости |
| **Brand-motif** | живой ECG / heartbeat | — | Затухающий ECG-штрих как signature в hero пустого экрана и как micro-разделитель секции «Сейчас» |

**Эмоция:** онбординг = «рассвет, начинается»; Чаты = «тот же рассвет, но уже спокойный день».
Один мир, два времени суток. Это честно закрывает «фоны разные», не ломая читаемость.

> ⚠️ Это **сознательное отступление** от прежнего правила
> [`Pulse_Visual_Design_Families.md`](../../../Docs/Rules_and_Standards/Pulse_Visual_Design_Families.md) §6
> и guardrail в [`Pulse_Conversation_P2_Alpha_Backlog.md`](Pulse_Conversation_P2_Alpha_Backlog.md)
> («не применять dawn к продукту»). Оба документа обновлены под это решение (см. их changelog).
> Dawn Bridge ≠ full aurora: это мягкий **wash + ECG-подпись**, не drift-анимация и не glass-spam.

### 1.1. Шов онбординг → продукт: arrival-settle («рассвет переходит в день»)

Смена насыщенной onboarding-авроры на спокойную продуктовую бумагу — **правильна и намеренна**
(продукт = читаемая рабочая поверхность, лёгкая на web), но **стык не должен быть резким**.
Решение: **не одинаковый фон везде**, а **переход**.

- **Первый продуктовый кадр после онбординга** (`/home`, флаг `postOnboardingHomeArrivalProvider`)
  проигрывает **одноразовый settle**: более насыщенный dawn-wash (эхо авроры peach→coral→violet,
  токен `PulseDawnBridgeTokens.arrivalDawnWash`) **затухает** в спокойный wash за ~1.3 c через
  `PulseDawnBridgeScaffoldLayer(arrival: true)`.
- **Реализация web-safe:** один fade-слой `Opacity` поверх спокойного wash, **без** drift/blur;
  при `reduced`-motion settle пропускается (сразу спокойный кадр). Повседневные заходи
  (`arrival: false`) спокойны — settle не повторяется.
- **Эхо-ECG (WOW на стыке):** тонкий ECG-штрих (`PulseDawnBridgeEcgSignature`, height 22) вверху
  первого кадра **рисуется слева-направо** (ShaderMask-wipe) и затухает на том же arrival-контроллере
  — эхо onboarding-героя. Один static-painter под маской (web-cheap), за контентом, играет один раз.
- **Не делать** full-aurora фон на всех продуктовых экранах: убьёт читаемость списков/переписки
  и перегрузит web. Связь даёт общий ДНК (peach-оттенок, Literata/Manrope, blue, ECG) + этот settle.
- **Базовый продуктовый wash усилен** (2026-06): тёплый «рассветный верх» peach `#FFD7A6` →
  coral `#F5C09A`, растворяется в бумагу к ~70% (раньше был near-white whisper и читался как
  «другое приложение»). Альфы под WCAG AA gate. Web и mobile — одинаковые слои, разница в drift.
- **Handoff-пейн privacy → home** (`ProfilePrivacyOdSuccessPane`): success-акцент перекрашен с
  generic green на **Pulse blue** (dawn-семья, не backstage-экран), dwell сокращён `1750 → 900 ms`.
  Подтверждение остаётся, но «не мигает чужим экраном»; момент прибытия несёт arrival-settle на Home.

## 2. Спецификация по экранам

### A. Пустые «Чаты» (новый юзер)

Цель: за один экран объяснить «почему пусто», показать ядро (ближайший DROP с таймером),
дать одно очевидное действие.

Композиция (сверху вниз):
1. **Masthead** «Чаты» + подзаголовок для нового юзера.
2. **Фильтры — СКРЫТЬ при `items.isEmpty`** (вернуть, как только ≥1 чат).
3. **Hero «сердце окна»** вместо серого орба:
   - Живой, но лёгкий **ECG-штрих** (мотив из `WelcomeOdHeartbeatStage`, ужать в 1 линию,
     амплитуда тише). На web — статичный кадр.
   - Крупный **город** («в Минске») как часть заголовка (локальный крючок запуска).
   - **Обратный отсчёт до ближайшего DROP** — главный визуальный объект (моно-таймер,
     как в тайлах). Нет слота → «Скоро откроем окно в Минске» + «Напомнить».
4. **3-step story** (DROP → взаимный интерес → чат на время): оставить
   `DropHowItWorksIconRail`, убрать «недорисованные» линии → connected-dots / нумерованные
   шаги, поочерёдный fade-rise (уже реализован, тюнинг тайминга).
5. **Один primary CTA** «Зарезервировать место · {день, время}» (слот есть) /
   «К ближайшему окну» (слота нет). Вторичная — текст-кнопка «Как это работает?» (sheet есть).

WOW (mobile): мягкий параллакс dawn-wash при скролле, staggered fade-rise карточек
(220–360 ms), pulse-glow вокруг таймера в последние минуты до окна. Web — reduced.

### B. Список чатов (populated) + тайлы + секции + поиск

База сильная (`PulseConversationListTile`: countdown-моно-таймер, grace-ring, status-pill,
unread-дот, press-scale). Правки точечные:
- **Секция «Сейчас» / «Недавно»** (`buildChatListSections` есть) — «живой» вид секции
  `Сейчас`: тонкая ECG-линия-разделитель + счётчик активных.
- **Live-тайл (открытый чат)** — поднять: тёплая bronze-glass подложка, крупнее таймер,
  тонкое «дышащее» кольцо у аватара, **пока чат открыт** (сейчас дыхание только grace/highlight).
- **Поиск** — стиль поля свести к онбордингу (`SignupOdVisual.fieldShellDecoration`, focus-glow blue).
- **Pull-to-refresh** — брендовый ECG-spinner вместо дефолтного `RefreshIndicator`.
- **Фильтры** → анимированный **сегмент-контрол** (скользящий pill между Все/Активные/Закрытые),
  бейдж активных на «Активные». Понятнее и моднее трёх отдельных таблеток.

### C. Экран переписки (timed chat)

Самый большой апсайд. План:
- **Настоящий app-bar:** аватар собеседника + имя/лейбл; под аватаром **таймер сессии =
  тонкое progress-кольцо** (переиспользовать `_GraceRingPainter`, но для всей сессии, не только grace).
  Back-arrow слева, safety-меню справа — оставить.
- **Фон:** Dawn-wash сверху, бумага внизу — тред читается как «письмо при тёплом свете».
- **Пузыри** (`timed_chat_od_message_bubble`): own = blue-gradient, counterpart = warm-paper-card,
  скруги 18px, время — мелким моно; появление новых — slide-up 160 ms.
- **Icebreakers** (есть) → «карточки-подсказки» со staggered-входом, видны только при пустом треде.
- **Grace** (эмоциональный пик «время уходит»): верхний strip с убывающим прогресс-баром
  (`TimedChatOdGraceStrip` есть), мягкий тёплый→тревожный сдвиг акцента в последние 2 минуты
  (хук `_maybeShowGraceTwoMinuteHint` есть).
- **Терминальное состояние** (`_TerminalComposerFooter`) — «закрытый конверт»: спокойная карточка
  «Окно закрылось» + primary «Следующий DROP» + secondary «К чатам». Без ощущения ошибки.
- **Composer** — поднять до уровня онбординг-полей (field-shell, focus-glow, blue send + haptic;
  `PulseProductHaptics.lightTap` уже есть).

### D. Loading / Error / Empty-after-filter

- **Skeleton** (`chats_od_loading_skeleton`) — добавить shimmer (сейчас статичные `PulseSkeletonBox`),
  гасить на reduced/web через `PulseMotionProfile`.
- **Error** (`ChatsOdErrorPanel`) — «calm blue glass error» (без alarm-red), ⓘ + retry.
- **Empty после фильтра/поиска** — мягкая строка + кнопка «Сбросить фильтр».

## 3. Web vs Mobile (web не должна тормозить)

Переиспользуем существующие «рубильники», **новый web-код с нуля не пишем:**

- **Фон:** Dawn-wash через паттерн `OnboardingOdWebPerf.providesSharedDawn`. На web —
  статический `LinearGradient` + 1 `RadialGradient`, без drift, без grain-shader, без backdrop-blur.
  На mobile — лёгкий радиал + опц. медленный drift.
- **Motion:** всё «дыхание»/ECG/параллакс — за `PulseMotionProfile.tier`; web и
  `prefers-reduced-motion` → `reduced` (статичный кадр). Эталон ветки `reduced` —
  `ChatsEmptyStorySteps._runEntrance`.
- **Blur:** glass-карточки — через `pulseOdOmitBackdropBlur(context)`: на web вместо
  `BackdropFilter` плоская `fieldShellDecoration` (эталон — `chats_empty_story_steps.dart`).
- **Таймеры:** на web — централизовать в **один тикер на экран** (сейчас несколько
  `Timer.periodic(1s)` на тайлах = главный риск jank); неактивное — `TweenAnimationBuilder` без таймера.
- **Раскладка:** широкий web (`>=1120`) → `ChatsWebSplitLayout` (есть); rail = «ближайший DROP»
  (пустые) / активный чат, правый pane — список 404px.

**Принцип:** mobile = полный WOW; web = тот же визуальный язык в «still»-режиме
(статичные градиенты, no-blur, минимум таймеров). Расширяет матрицу
[`web_mobile_parity.md`](../../../pulse-mobile-app/pulse-mobile-app/docs/web_mobile_parity.md)
на продуктовый слой.

## 4. Пакеты из pub

Проект **намеренно** не использует сторонние анимационные либы — вся motion рукописная
(`AnimationController`, `CustomPainter`, `PulseMotionProfile`). Это осознанный стандарт
(лёгкие зависимости + контроль web-веса). Рекомендация — остаться на in-house, добавить максимум 1-2:

- ✅ **`flutter_animate`** (опц.) — декларативная хореография (fade/slide/shimmer) поверх motion-tier,
  web-дёшево, уважает reduced-motion; уберёт часть `AnimationController`-бойлерплейта во входах.
- ✅ **`visibility_detector`** (опц.) — надёжный impression-трекинг `chatsEmptySeen`/`chatsListSeen`
  (сейчас `addPostFrameCallback`).
- ⚠️ **`shimmer`** — только если не хотите дорисовывать shimmer в существующий `PulseSkeleton`.
- ❌ **Lottie / Rive** для hero — **не для web** (доп. рантайм/ассеты, бьёт по CanvasKit-весу и
  first-paint). Свой ECG-painter легче и брендовее. Если очень нужен арт — только mobile + lazy +
  статичный web-fallback.

**Вывод:** ECG/heartbeat — рукописный (бренд + web-friendly); `flutter_animate` — опционально.

## 5. Файловая карта изменений

**Новое:**
- `app/theme/pulse_product_dawn_bridge.dart` — токены + виджет «dawn-wash» (web-still / mobile-live),
  общий для Home/Chats/Drop/Chat-thread.
- `chat/presentation/chats_od/chats_empty_pulse_hero.dart` — новый hero пустого экрана
  (ECG + город + таймер), заменяет серый орб.
- (опц.) `app/widgets/pulse_segmented_control.dart` — анимированный сегмент-контрол фильтров.

**Правки:**
- `chat/presentation/screens/chats_list_screen.dart` — скрыть `ChatsOdFilterBar` при `items.isEmpty`;
  обернуть body в Dawn Bridge; централизовать таймеры на web.
- `chat/presentation/widgets/chats_empty_hero_card.dart`, `chats_empty_story_steps.dart` — новая иерархия.
- `chat/presentation/chats_od/chats_od_filter_bar.dart` — сегмент-контрол.
- `chat/presentation/chats_od/chats_od_loading_skeleton.dart` — shimmer через motion-tier.
- `chat/presentation/chats_od/chats_od_error_panel.dart` — calm-blue-glass.
- `chat/presentation/screens/chat_screen.dart` — product-grade app-bar (аватар + таймер-кольцо),
  Dawn Bridge фон.
- `chat/presentation/timed_chat_od/timed_chat_od_message_bubble.dart`, `timed_chat_od_composer.dart` —
  брендовые пузыри/композер.
- `chat/presentation/conversation/pulse_conversation_list_tile.dart` — апгрейд live-тайла.
- `l10n/app_localizations*.arb` — новые строки (+ `flutter gen-l10n`).

## 6. Фазовый план

1. **Фундамент когезии:** `PulseProductDawnBridge` + применить фон на пустые Чаты + Home
   (мост виден сразу). Web-still / mobile-live.
2. **Пустые Чаты «ВАУ»:** новый hero (ECG + город + таймер), скрыть фильтры на empty,
   переработать story + CTA.
3. **Список:** сегмент-контрол, live-тайл, секция «Сейчас», shimmer-skeleton, calm-error,
   брендовый refresh.
4. **Переписка:** app-bar с аватаром/таймером, пузыри, композер, icebreakers, grace-драма,
   терминальный «конверт».
5. **Web-split + QA:** rail-контент; `flutter analyze` + `flutter test` + chrome web smoke
   (`chrome_web_smoke_checklist.md`); проверка motion на web/reduced; perf-budget gate
   (`performance_budget_gate.md`).

Каждая фаза — самостоятельный, ревьюабельный PR (≤~500 строк/файл по правилу декомпозиции).

## 7. Guardrails (не нарушать при реализации)

- Сохранить `Semantics`/`liveRegion`/`role: status` из `chats_list_empty_body.dart`.
- Все строки — через `AppLocalizations` (ru/en/be), затем `flutter gen-l10n`. Без хардкода.
- Concept-guardrails: никакого «поиска анкет/ленты/свайпа» в копирайте — только язык DROP-окон.
- Никаких public safety scores / read receipts / typing / online status.
- Один канонический тайл — `PulseConversationListTile` (`list` / `live`); **не** вводить третий вариант.
- Контраст текста над dawn-wash — WCAG AA в light/dark (wash очень мягкий, не ронять контраст onSurface).

## 8. Метрики успеха

- Onboarding → Chats: ноль ощущения «другого приложения» (один фон-язык, один акцент).
- `chats_empty_cta_tapped` (reserve / open_drop) растёт — пустой экран ведёт в ядро.
- Web: first-paint / jank не выросли (нет per-frame paint/blur); perf-budget gate зелёный.
- Переписка: `first_message_sent` растёт (icebreakers + понятный таймер снижают порог).

## 9. Связанные документы

- [`Pulse_Visual_Design_Families.md`](../../../Docs/Rules_and_Standards/Pulse_Visual_Design_Families.md) — добавлен Dawn Bridge (продуктовый слой).
- [`Pulse_Conversation_P2_Alpha_Backlog.md`](Pulse_Conversation_P2_Alpha_Backlog.md) — guardrail про dawn обновлён.
- [`web_mobile_parity.md`](../../../pulse-mobile-app/pulse-mobile-app/docs/web_mobile_parity.md) — матрица расширена на продукт.
- [`Pulse_Conversation_List_Tile_Reconciliation.md`](Pulse_Conversation_List_Tile_Reconciliation.md) — канонический тайл.
