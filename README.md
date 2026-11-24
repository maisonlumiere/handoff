# Handoff

Two commands for Claude Code: `/catchup` and `/wrapup`.

Stop keeping one long chat alive. End a session by writing what happened to `STATE.md`, start the next one by reading it.

```
/plugin marketplace add maisonlumiere/handoff
/plugin install handoff@handoff
```

## The problem

A long chat does not remember more. It remembers less.

Past a certain length every assistant compacts the conversation — summarises it to fit. Then it summarises the summary. Detail goes first: the exact error string, the thing you already ruled out, the path to the file you fixed last Tuesday. What is left is a vague sense that something happened.

Meanwhile every turn in that thread re-sends the entire history. The longer it gets, the more each message costs, and the less it knows. You pay more to be understood less.

The usual response is to scroll back and re-explain. That works until the thread is months old.

## The fix

Keep the state in a file instead of in the conversation.

`/wrapup` at the end of a session writes `STATE.md` — what landed, what is half-finished with the exact error text, what is blocked and on what, what was decided and why.

`/catchup` at the start of the next one reads it, checks it against recent git history in case the last session ended abruptly, and tells you where things actually are in about 150 words.

Then start a fresh chat every time. It knows everything that matters and nothing that doesn't.

## What STATE.md looks like

```markdown
# STATE

_Last updated: 2026-11-24_

## Now
Porting the ingest pipeline off the legacy queue. Reader and writer are done;
the retry path is the open question.

## Done
- 2026-11-24 — writer cut over to the new queue, 40k messages clean
- 2026-11-22 — reader cut over behind a flag

## In flight
- Retry path drops messages when the broker restarts mid-ack. Reproduces with
  `pytest tests/test_retry.py::test_broker_restart`. Fails in `_ack_batch()`
  at ingest/consumer.py:118 — the in-flight set is cleared before the ack
  is confirmed. Tried: wrapping in try/finally (did not help, the clear is
  in the happy path), and an idempotency key (works but doubles write load).

## Blocked
- Staging broker is on 3.4; the fix needs 3.6+. Waiting on infra ticket OPS-2291.

## Decided
- 2026-11-20 — queue over event log. Event log was a better fit long term but
  needs a schema registry nobody owns yet. Revisit if ordering bites us.

## Next
- Reproduce the ack race against a local 3.6 broker in Docker, since staging
  is stuck.
```

The **Decided** section is the one people underestimate. It is what stops the same good idea being proposed, considered and rejected every other week.

## Why a file and not memory

A file is portable, diffable, and reviewable. It travels with the repo, so a teammate gets it for free, and so does any other agent. It survives switching tools. You can read it yourself without asking anything, and correct it when it is wrong — which it occasionally will be, because it is written by something that was also doing the work.

Assistant memory features are useful but tied to one product and invisible to everyone else on the project. `STATE.md` is just a file in your repo.

## Honest limitations

- It is only as good as the wrap-up. A session that ends by closing the laptop writes nothing, which is why `/catchup` cross-checks git.
- It holds project state, not a transcript. If the reasoning behind something matters, it belongs in **Decided** — otherwise it is gone.
- It is two markdown files of instructions. There is no clever machinery here, and none is needed.

## Licence

MIT.
