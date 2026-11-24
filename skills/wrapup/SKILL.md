---
name: wrapup
description: Write the session's state into STATE.md so the next session can resume cold — what changed, what is half-finished, what is blocked, and what was decided. Use at the end of a working session, or when asked to "wrap up", "save state", "write the handoff".
---

# Wrap up

End of session. Write down what the next session needs, in `STATE.md` at the repo root.

Assume the reader knows nothing about this session and cannot ask you questions. That is the standard: could a cold agent resume from this file alone?

## The file

```markdown
# STATE

_Last updated: YYYY-MM-DD_

## Now
One paragraph. What this project is currently about — the thing being worked on,
not the project's overall purpose. Rewrite this every time.

## Done
- YYYY-MM-DD — what landed, in one line.

## In flight
- What was started and not finished. File paths, function names, the exact error
  text, which step failed. Enough to resume without re-investigating.

## Blocked
- What cannot proceed, and on what. If it waits on the user, say what you need.

## Decided
- YYYY-MM-DD — what was chosen, and why. Include what was rejected and the reason.

## Next
- One or two things worth doing, with a reason.
```

## How to write each section

**Now** — replaced wholesale each session. It should read like the first sentence you would say to someone walking in.

**Done** — newest first. Keep roughly the last 15 entries. Older ones move to `STATE-archive.md` rather than being deleted, so the trail survives without the file growing forever.

**In flight** — the section that earns the file. Be specific to the point of tedium. Quote error strings verbatim, including the line number and the file. If you tried three fixes and all failed, list all three so the next session does not repeat them.

**Blocked** — distinguish "waiting on the user" from "waiting on the world". A blocked item with no named blocker is just an in-flight item you gave up on; write it honestly as that.

**Decided** — the section that saves the most time, because it stops good ideas being re-proposed every week. Record the rejection and the reason, not just the choice: "Chose Postgres over SQLite — needs concurrent writes from three services" is useful; "Using Postgres" is not. Keep these permanently; they rarely go stale.

**Next** — short. A long Next list means nothing was prioritised.

## Rules

- **Update in place.** Read the existing STATE.md, merge, write the whole file back. Do not append a new dated section each session — the file is a current picture, not a log.
- **Resolve, do not accumulate.** An item fixed this session moves from In flight to Done. A blocker that cleared is deleted, not left with "(resolved)" beside it.
- **No optimism.** If something half-works, say which half. The next session trusts this file completely, so a cheerful summary of broken work costs real time.
- **Commit it.** `STATE.md` belongs in git — that is what carries it between machines and to anyone else on the project. Mention the commit; do not make it silently.
- Keep the whole file under roughly 150 lines. If it is longer, the Done section needs archiving or the In flight section is holding things that were actually abandoned.
