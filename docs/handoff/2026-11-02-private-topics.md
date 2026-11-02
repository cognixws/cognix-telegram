# 2026-11-02 — private-topics: Assistant topics as private-chat topics (0.4.0, 0.4.1)
0.4.0 (`e9524ba`) maps an Assistant's topics to private-chat topics (core ADR-0376 T4b, door `GET /api/ext/v2/assistants/topics`, core `b8a09a776`): `/topics`, a button per topic (16-character handle), a message in a known chat topic sent with a leading `#slug`, the answer returned to the input's chat topic. Ledger migration 003 (`threads`, `input_threads`).
0.4.0 failed live: the Host API client compared the whole path, query included, with its list of v2 doors and refused `/api/ext/v2/assistants/topics?assistant=…` before any request ("This action could not be completed"). Unit tests used a fake host and missed it.
0.4.1 (`1a06de7`) checks the path alone and refuses one whose resolved path differs (dot segments, encoded dots, backslashes, fragments). test/http.test.mjs fails without the fix and passes with it; npm test 77 pass, 0 fail.
Publication/install: pushed to GitHub main after rebasing on `3408557` (LICENSE); normal preview/install on the owner's instance 0.4.0 → 0.4.1, digest prefix e98ffe4c28ce0cae; channel connected, paired, unpaused. Receipt: PiCode root var/qa/telegram-topics/install-receipt-0.4.1.json. The bot token was never read.
Threaded Mode was turned on in @BotFather's Mini App at the owner's request ("Disallow users to create new threads" left off); the extension was restarted to read getMe.
Live test in Telegram Web, Assistant CTO, all passed: `/topics` lists five topics; a tap on `#jingle` opens chat topic 2007; a message there lands in topic `jingle` and "ok jingle" returns in the chat topic; a message sent outside a chat topic starts one that maps to a new topic and its answer returns there.
visual-review: n/a — no panel change.

## Next up

- Owner decision on implicit chat-topic names (below).

## Debts

- With Threaded Mode on, every message sent outside a chat topic starts one, and Telegram names it from the text (`is_name_implicit`). The extension takes that name, so each one opens a topic with a clipped name (`teste-de-tóp`). Proposal: on an implicit name send unmarked so the core router places it, then `editForumTopic` to the placed topic; needs the placed topic on the answer the extension reads (a core door change).
- The chat topics `/topics` (slug `topics`) and `Teste de tóp…` from the live test stay in the owner's chat; the core topic `teste-de-tóp` stays on the CTO until the owner archives it.
