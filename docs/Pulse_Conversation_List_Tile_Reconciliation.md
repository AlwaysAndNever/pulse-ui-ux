# Conversation list tile — screen reconciliation (2026-05)

## Scope

Product implementation of timed chat list cards in `pulse-mobile-app` after P0/P1 conversation system work.

Runtime source of truth:

- `lib/features/chat/presentation/conversation/pulse_conversation_tokens.dart`
- `lib/features/chat/presentation/conversation/pulse_conversation_list_tile.dart`

## OD / HTML baseline vs app

| Aspect | Legacy OD HTML (Chats index) | Current app (May 2026) |
| --- | --- | --- |
| Column width | `--col: 390px` | `PulseConversationTokens.columnMaxWidth = 390` |
| Title typography | Literata masthead only | Literata display title on counterpart name |
| Status | Text label inline | Pill: open / grace / closed |
| Countdown | Body text under name | Mono timer right-aligned; compact `8m` style |
| Preview | Single line | Up to 2 lines + optional DROP slot line |
| Grace urgency | Border tint | Accent border + avatar progress ring + soft scale pulse |
| Unread | Dot | Dot + «Reply first» when viewer has not sent |
| Home placement | Glance inside hero tail | Live ribbon above hero with CTA |

## Empty Chats (pre-match)

- Hero orb + subtitle (product copy, no `/chats` route hint in masthead)
- City window line: «Next window in {city} · {time}»
- 3-step mini story: DROP → mutual → chat
- Primary CTA: «Go to DROP» / «К DROP»

## Web split (≥1120px, Flutter web)

- Main list centered in 390 column
- Optional left rail: «Active chat» with live tile + open CTA (`ChatsWebSplitLayout`)

## Thread handoff

- Empty thread: shared-intent icebreaker when API returns `sharedIntentCodes`, else 3 generic chips
- Grace: full-width strip + outline extend; 2-minute in-app banner (haptic already on thread)
- Safety: overflow menu (report / block), not dual app-bar icons

## Follow-up for design repo

When OD HTML for `/chats` is refreshed, align:

1. Section headers «Now» / «Recently»
2. Mono countdown placement (right column)
3. Live ribbon on Home (not in-hero glance)

Until then, treat this reconciliation note + Flutter goldens as the authoritative product spec for list tiles.
