# Avatar & face gate — UI/UX contract (Pulse)

This document ties **design** (`pulse-ui-ux`) to **implementation** (`pulse-mobile-app`, `pulse-core-api`) so Figma and admin flows stay aligned with engineering truth.

## User-facing mobile (`pulse-mobile-app`)

- **Primary photo**: ML Kit face check on Android/iOS before upload (not on web in the current app). Copy must stay **honest**: heuristic, not KYC.
- **Minimum photos & no duplicate files**: product rule for drop readiness; see `pulse-mobile-app/docs/face_detection_pulse.md` and `ProfilePhotosScreen`.
- **Server 422 `media_avatar_no_detectable_face`**: user may pass on-device check but fail server BlazeFace on `complete` — allow **retry** with calm, non-accusatory messaging (aligned with `AppStrings.profileAvatarNoDetectableFace`).

## Server (`pulse-core-api`)

- Optional **BlazeFace** assist and optional **avatar face gate** are env-flagged; heavy CPU. Ops baseline: `Docs/Rules_and_Standards/Pulse_Photo_Identity_Gate_And_Moderation_Assist_Baseline.md`.
- **Admin queue preview stub** is **blocked** in `staging` / `production` env validation — only `development` / `test` may enable it for local/CI.

## Admin / staff (`pulse-admin-console`)

- Moderation queue uses **short-lived signed URLs** in real environments; stub URLs are **dev/test only**.

## Handoff

- Figma tokens and component mapping: `docs/Pulse_Figma_Handoff_Contract.md`, `docs/Pulse_Component_Library_Mapping.md`.
- Screen priority for flows touching photos: `docs/Pulse_Core_Flow_Screen_Priority_List.md`.
