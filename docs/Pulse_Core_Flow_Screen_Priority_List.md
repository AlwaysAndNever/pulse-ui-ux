# Pulse Core Flow Screen Priority List

## Назначение
Это `Figma-ready` priority list для core flows `Phase 4`.

Приоритеты здесь подчинены:
- `Docs/Done/UI_UX_Design_Package/Pulse_Mobile_Screen_State_Backlog.md`
- `Docs/Done/UI_UX_Design_Package/Pulse_Admin_Screen_State_Backlog.md`

## P0. Сначала Рисуем И Полируем
### Mobile
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
