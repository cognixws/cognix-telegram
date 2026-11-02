# 2026-11-02 — private-topics: Assistant topics as private-chat topics (0.4.0, 0.4.1)
0.4.0 (`e9524ba`) maps an Assistant's topics to private-chat topics (core ADR-0376 T4b, door `GET /api/ext/v2/assistants/topics`, core `b8a09a776`): `/topics`, a button per topic (16-character handle), a message in a known chat topic sent with a leading `#slug`, the answer returned to the input's chat topic. Ledger migration 003 (`threads`, `input_threads`).
0.4.0 failed live: the Host API client compared the whole path, query included, with its list of v2 doors and refused `/api/ext/v2/assistants/topics?assistant=…` before any request ("This action could not be completed"). Unit tests used a fake host and missed it.
0.4.1 (`1a06de7`) checks the path alone and refuses one whose resolved path differs (dot segments, encoded dots, backslashes, fragments). test/http.test.mjs fails without the fix and passes with it; npm test 77 pass, 0 fail.
Publication/install: pushed to GitHub main after rebasing on `3408557` (LICENSE); normal preview/install on the owner's instance 0.4.0 → 0.4.1, digest prefix e98ffe4c28ce0cae; channel connected, paired, unpaused. Receipt: PiCode root var/qa/telegram-topics/install-receipt-0.4.1.json. The bot token was never read.
Threaded Mode was turned on in @BotFather's Mini App at the owner's request ("Disallow users to create new threads" left off); the extension was restarted to read getMe.
Live test in Telegram Web, Assistant CTO, all passed: `/topics` lists five topics; a tap on `#jingle` opens chat topic 2007; a message there lands in topic `jingle` and "ok jingle" returns in the chat topic; a message sent outside a chat topic starts one that maps to a new topic and its answer returns there.
visual-review: n/a — no panel change.

## Next up

- None for topics; the debts below stay open in the PiCode topic.

## Debts

- Implicit chat-topic names: fixed in 0.4.2 (section below).

## 0.4.2 — implicit chat-topic names (owner approved the recommendation)
`da9019d` + `26dcc69`: a chat topic Telegram names from its text (`is_name_implicit`, from the service message or the first message's `reply_to_message`) waits unnamed; its messages go unmarked so the core's router places them; the first delivered routed answer (`#topic · …`) from its Assistant names it and `editForumTopic` renames it. No core change: the routed line is the core's existing contract (T4a). Adversarial review found the owner-mark path naming topics the core keeps as text (`#c++`) — removed — plus delivery/fallback risks, all fixed and tested (78 pass).
Installed 0.4.1 → 0.4.2, digest prefix f0e0b7ed83d8e71b, connected and paired (receipt var/qa/telegram-topics/install-receipt-0.4.2.json). Live, CTO: a message outside any topic ("Sobre o jingle: …") was routed to `jingle`, answered "#jingle · …" in its chat topic, and the chat topic was renamed `jingle`; no new topic. The CTO's test topics (`teste-de-tóp`, `teste-canal`, `festa-fim-de-ano`, `viagem-lisboa`) were archived at the owner's request.
Left as is: two chat topics can map to one topic (the newest wins for `/topics`).

## 0.4.3 — a chat topic whose mark the core refused
`89f05fc`: a message filed by its mark is never routed (measured live: "ok jingle", "ok segunda" came with no line), so a `#topic · ` line on an answer to a named chat topic means the core refused the mark. The chat topic takes the placed topic and is renamed, or waits again when it went to General. No core change, 79 tests pass; no separate adversarial review for this small change.
Installed 0.4.2 → 0.4.3, digest prefix 5e8d16e9651ae0d3, connected and paired (var/qa/telegram-topics/install-receipt-0.4.3.json). Live, CTO: a second message in a renamed chat topic went by mark to `jingle`; a message in chat topic 2012 (`teste-de-tóp`, archived) was routed to `jingle`, `teste-de-tóp` stayed archived, the answer said "#jingle · ok arquivado" and the chat topic was renamed. The refused word stays in the message text the Assistant reads (`#teste-de-tóp …`).
The live-test chat topics "/topics" and 2012 were deleted at the owner's request. The owner then had test chat topics 2007, 2017 (both `jingle`) and 2026 (`migracao-postgres`) deleted as well. A deleted chat topic's ledger row stays. Correction, measured live after those deletions: the `sendChatAction` probe in `openTopic` is not refused for a deleted thread, so a tap on `#jingle` (rows 2007, 2012, 2017 deleted) made no topic and showed the owner nothing. The unit test only simulates a 400. Open in the PiCode topic; chat topic 2037 (`/topics`, waiting) stays from this test.
