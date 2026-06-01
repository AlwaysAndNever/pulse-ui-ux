# Pulse Onboarding Wizard — Refactor Log (10–11 мая 2026)

## Назначение

Execution log по серии refactor'ов **онбординг-/auth-/profile-wizard'а** в Open Design между 10 и 11 мая 2026. Сюда фиксируем:

- какой экран и до какой версии переделан;
- к какой [визуальной семье](../../../Docs/Rules_and_Standards/Pulse_Visual_Design_Families.md) он теперь принадлежит;
- какие critical concept-fixes сделаны (особенно вокруг DROP-парадигмы);
- какие тексты переписаны (before/after);
- какие ARB-ключи требуют обновления значения;
- что осталось в backlog для следующих PR.

Этот документ — **execution log**, а не runtime-truth. Runtime-truth для дизайна = [`Pulse_Visual_Design_Families`](../../../Docs/Rules_and_Standards/Pulse_Visual_Design_Families.md). Артефакт — `.od/projects/*/index.html`. Этот файл просто отвечает на вопрос «что было сделано в этой серии и почему».

---

## 1. Lockstep таблица версий после серии

Все экраны принадлежат **splash-DNA family** (Calm Dawn Aurora + Glass + iPhone 16 Pro Max frame). Tokens binary-locked.

| Экран | OD project | До | После | Статус |
|---|---|---|---|---|
| `MOB/Bootstrap/Startup` | `ddc57182-…d2c3a57696376` | v11 | **v12** | accepted |
| `MOB/Onboarding/ProgressShell` | `7488920a-…b58e655` | v7 | **v8** | accepted |
| `MOB/Onboarding/LanguageCity` | `3eb2f694-…befc4756` | v2 | **v3** | accepted |
| `MOB/Onboarding/AgeGate` (`/18plus`) | `301dcab3-…01490a6` | v3c2 | **v3c3** | accepted (privacy-link chip) |
| `MOB/Auth/Signup` | `164f86fb-…0c0faa` | v5 | **v6** | accepted |
| `MOB/Auth/Login` | `3e6e785a-…297bcdf` | v3.1 | **v3.2** | accepted |
| `MOB/Auth/ForgotPassword` | `c07c1925-…8c8fd` | v7 | **v8** | accepted (тон, без promise о реальной отправке) |
| `MOB/Auth/ContactConfirmation` | `d84d0f21-…5b7ff62d` | junior → v3 (paper) → **v4 splash** → **v5** | **v5** | accepted (best-practice OTP cleanup) |
| `MOB/Profile/Photos` | `7d6b6710-…cc3684e` | v3 | **v4** | accepted |
| `MOB/Profile/Basics` | `3df82a3d-…69953d` | v1 → v2 | **v3** | accepted (DOB + height + weight) |
| `MOB/Profile/IntentAvailability` | `0f89a619-…f13d1f1` | v3 | **v4** | accepted (cleanup) |
| `MOB/Profile/PrivacyStarter` | `26ae254f-…1094c08b` | v1-junior → v1-splash | **v2** | accepted (DROP-only language) |

**Legal screens (paper-frame family) в той же серии:**

| Экран | OD project | До | После |
|---|---|---|---|
| `MOB/Legal/PersonalData` (5.1 consent) | `7ac35724-…d385a3f68` | junior | **v2 paper-frame** |
| `MOB/Legal/PrivacyFull` | `7396496d-…6b6f953747` | junior | **v2 paper-frame** |
| `MOB/Legal/CommunityRulesFull` | `0836d783-…7f9c665d75af` | v2 | **v3 paper-frame** (lockstep) |

---

## 2. Critical concept fix — `allowDiscoveryParticipation`

**До серии:** `MOB/Profile/PrivacyStarter` v1 (как junior, так и splash-DNA вариант) формулировал второй toggle как **«Показывать меня в спокойном поиске»** / «в подборе знакомств».

**Это противоречило концепту Pulse.** Pulse не имеет «свободного поиска» / Tinder-style swipe-feed. Есть только **DROP-окна**. Юзер на review правильно отловил: «у нас нет свободного поиска, в этом наша концепция».

**Что показал backend code-review:**

- [`pulse-core-api/src/modules/drops/services/drop-eligibility.service.ts:21–34`](../../../pulse-core-api/src/modules/drops/services/drop-eligibility.service.ts) — оба флага `allowDropParticipation` + `allowDiscoveryParticipation` входят в `explicitAllow` для **DROP eligibility**, никаких search-feeds.
- `allowDropParticipation` = «меня видят в **активных** окнах DROP сегодня».
- `allowDiscoveryParticipation` = «Pulse подбирает мне **новые будущие** DROP-окна» (matchmaking engine, recommendations).

То есть оба поля — **внутри DROP-парадигмы**, просто два слоя одного и того же. Toggle нужны оба (backend гейт зависит от обоих, поведение разное), но **формулировки переписаны** так, чтобы DROP-only language сохранялся.

**v2 формулировки (приняты):**

| Поле | UI-формулировка |
|---|---|
| Toggle 1 | «Меня видят в окнах DROP сегодня» + helper «Без этого тебя не подберут в активные окна.» |
| Toggle 2 | «Подбирать мне новые окна DROP» + helper «Pulse тихо предложит встречи под твой ритм.» |

Зафиксировано в [`Pulse_Visual_Design_Families §9`](../../../Docs/Rules_and_Standards/Pulse_Visual_Design_Families.md) как concept-guardrail для всего mobile UX.

---

## 3. `MOB/Profile/Basics` v3 — добавлены identity-метрики

### Что добавилось

| Поле | Required | Backend | Источник |
|---|---|---|---|
| `dateOfBirth` (показывается как «25 лет» live-derived) | required | ✓ `dateOfBirthController`: [`profile_basics_screen.dart`](../../../pulse-mobile-app/pulse-mobile-app/lib/features/profile/presentation/screens/profile_basics_screen.dart); UI OD — [`profile_basics_onboarding_od_body.dart`](../../../pulse-mobile-app/pulse-mobile-app/lib/features/profile/presentation/widgets/profile_basics_onboarding_od_body.dart) | новое в v3 |
| `heightCm` (numeric с suffix «см») | optional | ✓ `heightCmController` (те же файлы); лимиты — [`profile_basics_validators.dart`](../../../pulse-mobile-app/pulse-mobile-app/lib/features/profile/presentation/widgets/profile_basics_validators.dart) | новое в v3 |
| `bodyWeightKg` (numeric с suffix «кг») | optional | ✓ `bodyWeightKgController` (те же файлы); валидация — `profile_basics_validators.dart`. Статический OD-preview может задавать другие min/max — канон приложения в валидаторах. | новое в v3 |

### Архитектурное решение «куда положить»

Рассматривалось placement в `MOB/Profile/Basics` vs `MOB/Profile/IntentAvailability`. Решение — **в Basics**, потому что эти три поля — **identity** (как «Кто ты», «Имя»), а не **preferences** (как «Что тебе интересно»). DOB + height + weight — это про юзера, а не про его предпочтения.

### Что убрали из required, чтобы не перегрузить экран

Required count поднят с 3 → **4** (имя, DOB, gender, looking). About / height / weight остались **optional** и не считаются в progress. Соответственно `progress-segments` подняты с 3 → **4 сегмента**. Counter «X из 4».

### Layout

7 полей — это много. Чтобы не было endless scroll:

- DOB остался **full-width** (date picker + age display требуют места)
- Height + Weight сгруппированы в **two-column row** (`grid-template-columns: 1fr 1fr; gap: 10px`)
- About остался full-width внизу (less prominent — opt)

### DOB validation

- `min="1925-01-01"` `max="2008-05-10"` (на момент preview-даты ровно 18 лет)
- JS `ageFromDob()` + `ageWord()` со склонением «год / года / лет»
- На age <18 — calm blue glass `field-error`: «Pulse только для тех, кому уже 18.»

### Body-shaming concern по weight

Tinder/Hinge не показывают вес сознательно. Pulse решил **показывать опционально**, без public-trust signal. В v3 weight явно помечен `опц.` и **не получает зелёный ✓** при заполнении (только border меняется на `accent-light`). Это **не** identity-достижение, чтобы не давить.

---

## 4. `MOB/Profile/IntentAvailability` v4 — best-practice cleanup

### Изменения от v3 → v4

| Где | Было (v3) | Стало (v4) | Почему |
|---|---|---|---|
| Subline (3 строки) | «Расскажи, что тебе ближе и в какие дни проще — подберём окна DROP под твой ритм.» | **«Подберём окна DROP под твой ритм.»** | command-y «Расскажи …» удалено, action implied чипами. |
| Privacy whisper (2 строки) | «Здесь достаточно общих предпочтений — без точных дат, адресов и контактов.» | **«Без точных дат, адресов и контактов.»** | тавтология «общих предпочтений» с уже выбранными чипами. |
| Secondary CTA «Настроить позже» | присутствовала под primary | **удалена** (HTML + JS handler + CSS `.btn-defer` + ARB key `profileIntentDeferLater`) | abandonment-temptation в onboarding wizard. Если юзер хочет прервать — back-arrow вверху. Lockstep с basics v2/v3 (там убрали по тем же причинам). |

`data-design-lockstep` обновлён: `profile-basics-v2 → v3`.

---

## 5. `MOB/Auth/ContactConfirmation` v5 — best-practice OTP

История этого экрана:

- **junior** (oklch palette, no frame) →
- **v3 paper-frame** (юзер сказал «нет ВАУ, экраны в другом стиле») →
- **v4 splash-DNA** (юзер сказал «лучше, но всё нагромождено») →
- **v5 best-practice cleanup** (минимизация cognitive load).

Ключевые правки v4 → v5:

- Удалены `.status-pill`, отдельная `.email-card`, primary CTA-кнопка (auto-submit на full code), glass-wrapped help accordion, footer note.
- Email mask интегрирован inline в subline (`<span class="email-mono">`).
- OTP cells увеличены: 52→56px height, 22→26px font (доминируют в иерархии).
- Retry button появляется **только** на error.
- Footer перестроен: inline text-links для resend и help, compact one-row dev-strip.
- Dedicated `success-pane` для confirmed state (зелёный orb + redirect-bar 1.4s).

Эти patterns подняты в universal-семейный canon (см. [`Pulse_Visual_Design_Families §8`](../../../Docs/Rules_and_Standards/Pulse_Visual_Design_Families.md)).

---

## 6. `MOB/Profile/PrivacyStarter` v2 — DROP-only language + clutter cleanup

### Critical concept fix (см. §2 выше)

Все формулировки переведены в DROP-парадигму. Никаких слов «поиск» / «discovery» / «свайп» / «лента».

### Decluttering (нагромождение)

| Что | Было v1 | Стало v2 |
|---|---|---|
| Live preview видимости | Отдельная glass-карточка с avatar + name + city + статус-чипом (~95px) | **Inline mini effect-chip** под subline в hero — две точки + одна строка (~28px). Имя/возраст убраны (дублируют basics). |
| Soft-warn («оба off») | Отдельная glass-карточка под effect-preview (~52px) | **Inline-warning ВНУТРИ privacy-card** под row 2 — тонкая строка с дотом, в контексте. Освободило ~50px. |
| Иконки в toggle-row | 38×38px squircle | **30×30px** (subtle, не давят) |
| Padding в toggle-row | 14px 4px | **12px 0** (компактнее без потери 44pt tap target) |

После cleanup экран — 4 вертикальных блока (hero+chip → privacy-card → privacy-whisper → CTA) вместо 6. Тот же объём, на ~30% свободнее.

### WOW-эффект на финале

Финальный шаг wizard'а заслуживает celebration:

- **Aurora-pulse**: на `data-state="defaults"` (когда оба toggle ON) `dawn-drift` ускоряется 22s → 14s + saturate +6%. Subliminal positive feedback.
- **CTA halo glow всегда** на этом экране (не только на `ready`) — финальный CTA в wizard.
- **Success pane** lockstep с contact-confirmation v5 (зелёный orb + redirect-bar).
- **Saving label**: «Готовим Pulse…» (warmer, чем «Сохраняем профиль…»).

### Backend gate — экран обязателен

Удалить экран нельзя, потому что:

- [`profile-readiness.service.ts:37, 143–144, 171–172, 182`](../../../pulse-core-api/src/modules/profile/services/profile-readiness.service.ts) — `hasPrivacyStarter` входит в формулу `nextStep`. Без него pipeline возвращает `'privacy'`, юзер физически не доходит до `/home`.
- [`profile_privacy_screen.dart:250, 263–278`](../../../pulse-mobile-app/pulse-mobile-app/lib/features/profile/presentation/screens/profile_privacy_screen.dart) — единственное место, где `currentStep = ProfileOnboardingStep.complete` и `markProfileSetupWizardPersistedComplete()`.
- [`app_router.dart:117, 599–600`](../../../pulse-mobile-app/pulse-mobile-app/lib/app/routing/app_router.dart) — `AppRoute.profilePrivacy` зарегистрирован.
- 7 ARB-ключей уже переведены на ru/en/by.

---

## 7. ARB follow-up (значения, не имена ключей)

Имена ключей **не меняются** — Flutter не нужно ничего пересобирать. Только обновить значения в `app_ru.arb / app_en.arb / app_by.arb` и прогнать `flutter gen-l10n`.

### `MOB/Profile/Basics` v3 — новые ключи (добавить)

```json
"profileBasicsDobLabel": "Дата рождения",
"profileBasicsDobAgeSuffix": "лет",
"profileBasicsDobAgeUnder18Error": "Pulse только для тех, кому уже 18.",
"profileBasicsHeightLabel": "Рост",
"profileBasicsHeightUnit": "см",
"profileBasicsHeightRangeError": "Укажи рост от 120 до 230 см.",
"profileBasicsWeightLabel": "Вес",
"profileBasicsWeightUnit": "кг",
"profileBasicsWeightRangeError": "Укажи вес от 35 до 200 кг.",
"profileBasicsOptional": "опц."
```

### `MOB/Profile/IntentAvailability` v4 — обновить значения

```json
"profileIntentSubline": "Подберём окна DROP под твой ритм.",
"profileIntentPrivacyNote": "Без точных дат, адресов и контактов."
```

Ключ `profileIntentDeferLater` — **больше не используется**, можно пометить deprecated.

### `MOB/Profile/PrivacyStarter` v2 — обновить значения

```json
"profilePrivacyScreenTitle": "Где тебя видят.",
"profilePrivacyScreenDescription": "Включи, где появляться. Поменять можно в любой момент.",
"allowDropToggle": "Меня видят в окнах DROP сегодня",
"allowDiscoveryToggle": "Подбирать мне новые окна DROP",
"privacyHint": "В Настройках это всегда можно поменять.",
"profileFinishSetup": "Готово, я в Pulse →",
"savingProfile": "Готовим Pulse…",
"profilePrivacyBackToIntent": "Интересы и доступность"
```

И **новые ключи** для helpers, effect-chip, soft-warn, success:

```json
"profilePrivacyToggleDropHelper": "Без этого тебя не подберут в активные окна.",
"profilePrivacyToggleDiscoveryHelper": "Pulse тихо предложит встречи под твой ритм.",
"profilePrivacyEffectVisibleBoth": "Сейчас в DROP · подбираем новые",
"profilePrivacyEffectDropOnly": "Сейчас только в активных окнах",
"profilePrivacyEffectDiscoveryOnly": "Тихо подбираем новые DROP",
"profilePrivacyEffectInvisible": "Сейчас тебя нигде не показывают",
"profilePrivacyInvisibleSoftConfirm": "Оба выключены — тебя не подберут ни сегодня, ни на будущее. Уверен(а)?",
"profilePrivacySuccessTitle": "Профиль готов.",
"profilePrivacySuccessSubline": "До встречи в Pulse."
```

Ключ `profileSaveFinalHint` — **больше не нужен** (на финале лишний noise), можно пометить deprecated.

---

## 8. Что осталось в backlog

### Backend revs

- ⚠ **`weightKg`** в `profile.schemas.ts` / Prisma migration / `profile.types.ts` / Flutter `ProfileBasicsFormCard` — нужно добавить, чтобы `MOB/Profile/Basics` v3 weight-поле работало end-to-end. До этого скрывать за feature-flag.

### Дизайн-задолженность

- **Settings hub-карточки** (`/settings`, `/settings/account`, `/settings/preferences/*`) сейчас в Notion-style (oklch / Notion blue). Когда будет следующая итерация settings — мигрировать на splash-DNA tokens, **без** dawn-drift (settings = utility, не celebration). См. [`Pulse_Visual_Design_Families §6 «Граничные случаи»`](../../../Docs/Rules_and_Standards/Pulse_Visual_Design_Families.md).
- **`MOB/Verification/IntroAndStatus`** (`f9fdb532-…b9e40c2c7`) — пока junior, refactor отложен до отдельного PR (нужно решить судьбу Mode A intro / переименование в `StepUpGate`).

### Концептуальные открытые

- **Pronouns** (он / она / они/нейтрально) — best-practice 2026, особенно для inclusive dating. Сейчас можно derive из gender (`woman → она`, `man → он`); если backend заведёт third gender, нужен явный picker. Решение нужно одно: tacit derivation vs explicit field.
- **Hinge-style lifestyle prompts** (курение / алкоголь / питание / уровень образования) — отложены post-MVP, должны жить в отдельном `/profile/lifestyle` экране после photos, **не** в `/profile/basics`.

---

## 9. Cross-links

**Governance / runtime-truth для дизайна:**

- [`../../../Docs/Rules_and_Standards/Pulse_Visual_Design_Families`](../../../Docs/Rules_and_Standards/Pulse_Visual_Design_Families.md)
- [`../../../Docs/Rules_and_Standards/Pulse_Mobile_Design_And_UX_Copy_Baseline`](../../../Docs/Rules_and_Standards/Pulse_Mobile_Design_And_UX_Copy_Baseline.md)
- [`../../../Docs/Rules_and_Standards/Pulse_Mobile_First_Launch_Flow_And_Open_Design_Map`](../../../Docs/Rules_and_Standards/Pulse_Mobile_First_Launch_Flow_And_Open_Design_Map.md)

**Дизайн-handoff:**

- [`Pulse_Open_Design_Artifact_Index`](Pulse_Open_Design_Artifact_Index.md) — index OD-проектов
- [`Pulse_Component_Library_Mapping`](Pulse_Component_Library_Mapping.md)
- [`Pulse_Figma_Handoff_Contract`](Pulse_Figma_Handoff_Contract.md)

**Runtime для cross-check:**

- [`pulse-mobile-app/lib/features/profile/presentation/screens/profile_privacy_screen.dart`](../../../pulse-mobile-app/pulse-mobile-app/lib/features/profile/presentation/screens/profile_privacy_screen.dart)
- [`pulse-mobile-app/.../profile_basics_screen.dart`](../../../pulse-mobile-app/pulse-mobile-app/lib/features/profile/presentation/screens/profile_basics_screen.dart) — контроллеры, persist, навигация
- [`pulse-mobile-app/.../profile_basics_onboarding_od_body.dart`](../../../pulse-mobile-app/pulse-mobile-app/lib/features/profile/presentation/widgets/profile_basics_onboarding_od_body.dart) (+ `profile_basics_od_*`, `profile_basics_onboarding_od_gate.dart`) — OD онбординг
- [`pulse-mobile-app/.../profile_basics_form_card.dart`](../../../pulse-mobile-app/pulse-mobile-app/lib/features/profile/presentation/widgets/profile_basics_form_card.dart) — карточка в edit mode
- [`pulse-core-api/src/modules/profile/services/profile-readiness.service.ts`](../../../pulse-core-api/src/modules/profile/services/profile-readiness.service.ts)
- [`pulse-core-api/src/modules/drops/services/drop-eligibility.service.ts`](../../../pulse-core-api/src/modules/drops/services/drop-eligibility.service.ts)
- [`pulse-mobile-app/lib/l10n/app_ru.arb`](../../../pulse-mobile-app/pulse-mobile-app/lib/l10n/app_ru.arb)

---

## 10. Когда закрывать этот лог

Этот лог — про серию **10–11 мая 2026**. Если будет следующая серия refactor'ов, заводить **новый** лог `Pulse_Onboarding_Wizard_Refactor_Log_YYYY_MM.md`, а этот **не править**. Это history, не plan.

Если найдена ошибка в этой серии (например, regression в одной из принятых версий) — править артефакт, поднимать версию, отмечать в новом логе. Старый лог остаётся как есть.
