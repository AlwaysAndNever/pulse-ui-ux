# Conversation system — P2 backlog (post-alpha / data-driven)

Scope: reviewer items after P0/P1 conversation work. Runtime truth remains Flutter + `pulse-core-api`; OD lab artifacts are visual reference only.

Related: [`Pulse_Chat_PRD_And_Backend_Spec.md`](../../../Docs/Business_and_Concepts/Pulse_Chat_PRD_And_Backend_Spec.md), [`Pulse_Conversation_List_Tile_Reconciliation.md`](Pulse_Conversation_List_Tile_Reconciliation.md).

## Best-practice alignment (dating + speed-dating)

| Practice | Baseline (May 2026) | Target | Status |
| --- | --- | --- | --- |
| Limited window → first message | Empty thread + icebreakers | Prompts + gentle urgency in list | **Partial** — icebreakers + «Write first» / «Reply first» badges |
| Scarcity after match | Single list | «Сейчас» section + grace visuals | **Done** — sections + grace pulse |
| No anxiety features | No read receipts / typing / online | Keep | **Done** |
| Chat = bridge, not destination | Inbox-style list | CTA to next DROP after close | **Done** — terminal «Next DROP» |
| Fair timer | Countdown visible | + grace explanation + mutual extend | **Partial** — grace strip + extend pending UI (May 2026) |
| Safety 2–3 taps | Overflow safety menu | Menu + bubble report | **Done** |

## P2 items — triage

| Item | Alpha now | Post-alpha / data-driven |
| --- | --- | --- |
| **Mutual extend pending UI** | Inline banner in grace strip; `extensionPendingState` on session view; confirm CTA for counterpart | A/B copy; push when counterpart must confirm |
| **Push/local notification copy audit** | Backend FCM templates + mobile local copy — no «timed» jargon (PRD §17) | Full locale matrix audit; admin evidence dashboard |
| **Conversation analytics UI badges** | «Write first» / «Reply first» on list tile (client-side from preview) | Admin funnel dashboard; server `viewerHasSentMessage` if preview insufficient |
| **TimedChatStateLab goldens** | `timed_chat_thread_expired.png`, `timed_chat_thread_closed.png` | Grace + extend-pending goldens |
| **Analytics dashboard hooks (PRD §18)** | Client: `first_message_sent`, `chats_list_seen` | Ops: `conversation_start_rate`, `chat_extension_rate` in admin |

## Guardrails — do not

- Move **dawn-aurora / splash-DNA** onto Chats or Home product surfaces (editorial-monocle only).
- Add candidate feed or «find dates» in empty chat.
- Show public safety scores, read receipts, typing, online status.
- Use `/chats`, `timed`, `polling` in **user-facing** copy (route hints / dev labels OK in OD only).
- Introduce a **third** chat card variant — canonical tile: `PulseConversationListTile` (+ live density).

## Card variants (intentional two)

1. **`PulseConversationListTile`** — list + Home live ribbon (`list` / `live` density).
2. **Legacy OD HTML cards** — design handoff only; do not add parallel Flutter implementations.

## Notification copy (PRD §17) — audited May 2026

| Surface | Before | After |
| --- | --- | --- |
| FCM `chat_new_message_neutral` | «New message in your timed chat» | «New message» |
| FCM `chat_ending_soon_neutral` | «Timed chat ending soon» | «Chat closing soon» |
| Mobile local push ARB | `timed-чат` in title | Neutral «Новое сообщение» / «New message» |
| Thread footer | «Messages refresh automatically» | «New messages appear automatically» |

Allowed neutral signals only: new chat, someone messaged you, chat closing soon (no body leak, no romantic context).

## OD reference

- **`MOB/Chat/TimedChatStateLab`** (`d7f5d98a-…`) — lab states for expired/closed/grace; marked archive/lab in Screen_Reconciliation; goldens reuse terminal layouts, not duplicate product chrome.

## Suggested post-alpha metrics gates

Before investing in extend push or dashboard UI:

1. `conversation_start_rate` below target with high empty-thread rate → prioritize icebreaker experiments.
2. `chat_extension_rate` low but grace exits high → tune extend copy, not new card types.
3. `first_message_latency` p95 → validate list urgency badges correlate with sends.
