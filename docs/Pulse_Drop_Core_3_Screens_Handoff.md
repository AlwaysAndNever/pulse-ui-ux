# Pulse Drop Core 3 Screens Handoff

## 1. Scope

This handoff fixes a single local-first baseline for the first three DROP-critical mobile surfaces:

1. `Drop Hub / Next Drop` (mobile route **`/drops`**) — отдельно от таба **`Home`** (**`/home`**, readiness hub и главный next action перед входом в DROP UI).
2. `Match / Candidate Deck` (`/matches/slots/:slotId`)
3. `Timed Chat State`

The document is implementation-facing and must not override runtime truth from code/routes/contracts.

## 2. Runtime Truth Anchors (must stay primary)

- Mobile route map: `pulse-mobile-app/pulse-mobile-app/docs/screen_map.md`
- Router and path ownership: `pulse-mobile-app/pulse-mobile-app/lib/app/routing/app_router.dart`
- Priority order for Phase 4 core flows: `pulse-ui-ux/pulse-ui-ux/docs/Pulse_Core_Flow_Screen_Priority_List.md`
- Drift discipline: `Docs/Done/Rules_and_Standards/Pulse_Phase_4_Design_Drift_Checklist.md`

Route anchors for these screens:

- `Drop Hub / Next Drop` -> `/drops` (`DropHomeScreen`). **`/home`** (`HomeHubScreen`) — первый таб shell: готовность профиля и primary CTA (в т.ч. «открыть DROP» / календарь), см. `screen_map.md`.
- `Match / Candidate Deck` -> `/matches/slots/:slotId` (`MatchCandidatesScreen`, fullscreen over shell)
- `Timed Chat State` -> `/chats/:chatId` (`ChatScreen`, fullscreen over shell)

## 3. Fixed Visual Baseline (start point)

For first implementation wave we fix one baseline:

- Open Design selected system: `notion`
- Direction: `editorial-monocle`
- Pulse action accent: Notion Blue `#0075de`
- Pulse adaptation: "warm editorial + high-contrast action states for DROP timing"

Why this baseline:

- preserves human/warm tone for Minsk-first launch;
- avoids generic swipe-app visual language;
- supports clear hierarchy for timed actions (countdown, enter window, chat expiry);
- aligns with existing Pulse constraints: privacy-first, trust-visible, no fake realtime/offline promises.

### 3.1 Baseline token intent

- Primary background: warm-neutral, low visual noise.
- Action accent: restrained pulse accent for CTA and active time state.
- Warning/destructive states: explicit but calm, never alarmist.
- Typography: readable sans, medium density, no decorative overload.
- Motion: subtle and sparse; countdown/timer states may animate, but no gamification effects.

## 4. Screen Handoff Specs

## 4.1 Drop Hub / Next Drop (`/drops`)

Purpose:

- Answer "what is my next meaningful DROP action right now?" (продуктовый центр временных окон; не смешивать с **`/home`**, где фокус на readiness и одном next step ко входу в DROP).

Required blocks:

1. Hero with nearest drop window (date, local time, status label).
2. Primary CTA (one only): reserve slot, enter drop, or view active drop.
3. Participation state card: not joined / reserved / active / closed.
4. Short mechanic explainer (2-3 bullet lines max).
5. Secondary entrypoints: calendar and slot details.

Copy constraints:

- user-facing only, no internal technical labels;
- translate limitations into next actions, not system diagnostics.

Do not:

- show fake "live now" confidence where runtime is polling-first;
- invent product promises beyond current route/state reality.

## 4.2 Match / Candidate Deck (`/matches/slots/:slotId`)

Accepted Open Design artifact:

- Project: `6891912c-8800-4cda-8bd3-ec7230541c0e`
- Local artifact: `open-design-main/.od/projects/6891912c-8800-4cda-8bd3-ec7230541c0e/index.html`
- Status: Accepted v1 baseline.

Purpose:

- Support decisive like/pass selection during active DROP without debug-style UI.

Runtime notes:

- OD `data-route` matches Flutter (**`/matches/slots/:slotId`**); **`/drops/slots/:slotId`** remains slot details only.
- Flutter gates deck UI unless participation matches the slot, user is in active participation flow, and slot is **live** (same condition as **Active DROP** primary CTA).
- Candidate API today does not include photo preview URLs — shipped UI may stay anonymous/text-first until the contract grows.

Required blocks:

1. Candidate media and short identity-safe header.
2. Context line about current DROP window.
3. Main actions: `Skip` and `Like` (equal clarity, not dark-patterned).
4. Quick safety entrypoint (report/block path, contextual).
5. Empty/error fallback with clear next action.

Behavior notes:

- this surface is fullscreen above tab shell;
- avoid infinite swipe-deck tropes: bounded list/stack tied to the DROP window is acceptable;
- card behavior must remain consistent with DROP timing states.

Do not:

- expose hidden/internal trust scoring;
- leak moderation internals into normal candidate UI.

## 4.3 Timed Chat State (`/chats/:chatId`)

Accepted Open Design artifact:

- Project: `d7f5d98a-2d15-4d1e-9d58-9b2f7c8a13f1`
- Local artifact: `open-design-main/.od/projects/d7f5d98a-2d15-4d1e-9d58-9b2f7c8a13f1/index.html`
- Status: Accepted v1 baseline.

Purpose:

- Make chat window boundaries explicit: active, grace, expired, closed.

Required blocks:

1. Top status bar with current chat window state.
2. Time-left presentation for active/grace windows.
3. Input affordance that clearly changes by state (enabled/disabled reason).
4. One calm next-step CTA for expired/closed states.
5. Contextual safety actions reachable in 1-2 taps.

State clarity:

- active: user can send;
- grace: user can finish but sees close boundary;
- expired/closed: no fake send affordance.

Accepted state notes:

- active shows a calm time-left status and enabled composer;
- grace keeps sending available but makes the close boundary visible;
- expired/closed disables the composer, changes the send affordance to closed, and offers a single return-to-chats CTA;
- loading/error stay user-facing and avoid backend diagnostics.

Do not:

- mimic realtime certainty if runtime does not guarantee it;
- hide expiry reason behind generic error copy.

## 4.4 Related P0 Support Screens

These screens are outside the original Core 3 DROP handoff scope, but are accepted as P0 support surfaces because they carry runtime-honesty, notification, and safety expectations used by the core flow.

Accepted Open Design artifacts:

| Screen | Route | Project | Local artifact | Status |
| --- | --- | --- | --- | --- |
| `MOB/Trust/ReportFlow` | `/chats/:chatId`, `/matches/slots/:slotId` | `81537268-a304-4f7d-891f-2afee23a5c20` | `open-design-main/.od/projects/81537268-a304-4f7d-891f-2afee23a5c20/index.html` | Accepted v1 baseline |
| `MOB/Settings/NotificationSettings` | `/settings/notifications` | `fef6f327-bfa3-40ee-a61a-8929c92a3adf` | `open-design-main/.od/projects/fef6f327-bfa3-40ee-a61a-8929c92a3adf/index.html` | Accepted v1 baseline |
| `MOB/Settings/RuntimeTruth` | `/settings/runtime-truth` | `743f4898-5e39-433d-b954-6dd40c2ceee8` | `open-design-main/.od/projects/743f4898-5e39-433d-b954-6dd40c2ceee8/index.html` | Accepted v1 baseline |

Shared constraints:

- keep safety/reporting available without promising instant moderation;
- keep notification copy explicit about OS permissions and non-guaranteed delivery;
- keep runtime-truth copy user-facing, without backend/debug wording;
- do not add public trust badges, public safety scores, or fake offline/realtime guarantees.

## 5. Open Design Generation Contract (Cursor workflow)

Use `open-design-main` as design generation workspace, then map output back to runtime anchors.

For each screen generation task include:

1. screen id and route anchor (`/drops`, `/matches/slots/:slotId`, `/chats/:chatId`);
2. baseline: `notion` system + `editorial-monocle` direction + Pulse constraints;
3. fixed anti-goals:
   - no generic swipe visual cliches;
   - no public trust badges;
   - no fake offline/realtime claims;
   - no technical backend wording in user layer.

Minimum output per screen:

- one main frame;
- state variants (loading/empty/error + timing-specific where relevant);
- CTA hierarchy and short copy draft.

Generated artifact tracking lives in `pulse-ui-ux/pulse-ui-ux/docs/Pulse_Open_Design_Artifact_Index.md`.

## 6. Drift Check Before Merge

For each update run this quick check:

1. Route/state still matches `app_router.dart`.
2. Handoff does not raise promise level above runtime.
3. `pulse-ui-ux` naming still matches current mobile backlog naming.
4. Caveats are explicit where runtime is limited.

If any check fails, update runtime docs or handoff immediately (do not leave mixed truth).

## 7. Local Run Note

Local startup command validated:

- from `open-design-main`: `pnpm tools-dev run web`
- runtime URLs were provided by the tool runner (web + daemon) and process stays foreground until stopped.
