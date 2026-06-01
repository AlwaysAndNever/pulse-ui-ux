# Pulse Core Flow Screen Priority List

## Назначение
Это `Figma-ready` priority list для core flows `Phase 4`.

Приоритеты здесь подчинены:
- `Docs/Done/UI_UX_Design_Package/Pulse_Mobile_Screen_State_Backlog.md`
- `Docs/Done/UI_UX_Design_Package/Pulse_Admin_Screen_State_Backlog.md`

## Индекс Open Design (MOB)

Чтобы не заводить второй макет «на тот же экран», смотри реестр статических OD:

- **Таблица всех проектов + сверка P0/P1 только по mobile:** `open-design-main/.od/docs/MOB_SCREEN_INDEX.md`
- **JSON:** `open-design-main/.od/mob-screen-index.json` — после добавления экрана прогони `node open-design-main/.od/scripts/build-mob-screen-index.mjs` из корня репозитория `cs`.
- **Расхождения OD ↔ доки ↔ mobile ↔ API:** `open-design-main/.od/docs/Screen_Reconciliation_OD_Docs_Mobile_Backend.md`

**Admin:** в `open-design-main/.od/projects` сейчас нет экранов с префиксом `ADM/…` — весь блок Admin ниже пока **без** Open Design-артефактов в этом дереве.

## P0. Сначала Рисуем И Полируем
### Mobile

**IA:** первый таб shell — `/home` (готовность + следующий шаг); DROP как ядро — вкладка `/drops`. OD-id `MOB/Home/HomeNextDrop` соответствует маршруту **`/drops`** (Drop hub), не табу Home.

1. `MOB/Auth/Signup`
2. `MOB/Auth/Login`
3. `MOB/Auth/ContactConfirmation`
4. `MOB/Verification/IntroAndStatus`
5. `MOB/Restriction/AccountRestricted`
6. `MOB/Home/HomeNextDrop`
7. `MOB/Drop/SlotDetails`
8. `MOB/Drop/ActiveDropRecovery`
9. `MOB/Chat/TimedChatActive`
10. `MOB/Chat/TimedChatExpiredOrGrace`
11. `MOB/Trust/ReportFlow`
12. `MOB/Settings/NotificationSettings`
13. `MOB/Settings/RuntimeTruth`

### Admin
1. `ADM/Login/ManualToken`
2. `ADM/Login/SessionRecoveryNotCompleted`
3. `ADM/Nav/PermissionAwareSidebar`
4. `ADM/Dashboard/Overview`
5. `ADM/Moderation/QueueDefault`
6. `ADM/Moderation/CaseDetail`
7. `ADM/Moderation/RestrictionConfirm`
8. `ADM/Verification/LookupDefault`
9. `ADM/Verification/LookupResult`
10. `ADM/Verification/StepUpResolutionConfirm`
11. `ADM/LaunchOps/Checkpoint`
12. `ADM/LaunchOps/SlotInspector`
13. `ADM/LaunchOps/CommandConfirm`

## P1. После Закрытия P0
### Mobile
- `MOB/Onboarding/Welcome`
- `MOB/Onboarding/LanguageCity`
- `MOB/Onboarding/AgeGate`
- `MOB/Profile/Photos`
- `MOB/Profile/Basics`
- `MOB/Profile/IntentAvailability`
- `MOB/Profile/PrivacyStarter`
- `MOB/Settings/BlockedAccounts`
- `MOB/Settings/SupportAndPolicy`

### Admin
- `ADM/Dashboard/HiddenSurfaces`
- `ADM/Moderation/WorkflowAudit`
- `ADM/Verification/ReadLimitedWarning`
- `ADM/LaunchOps/PostSlotReview`
- `ADM/Playbook/RecoveryGuides`

## P2. Не Блокирует Закрытие Phase 4
- marketing/brand handoff;
- расширенные visual alternatives;
- secondary polish без влияния на trust clarity или operator throughput.

## Правило Приоритета
Если появляется конфликт между visual attractiveness и runtime honesty, выигрывает runtime honesty.
