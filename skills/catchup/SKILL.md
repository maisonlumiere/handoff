---
name: catchup
description: Get up to speed on a project at the start of a fresh session — reads STATE.md plus recent git history and reports what is done, what is half-finished, what is blocked and what to do next. Use when opening a new chat on an existing project, or when asked "where were we", "catch me up", "what's the status".
---

# Catch up

Start of a new session. Work out where the project actually is, in one pass, and say so.

## Read, in this order

**1. `STATE.md`** at the repo root. This is the handoff file the last session left. If it exists, it is the primary source.

**2. Git, to check STATE.md against reality.** STATE.md is written by hand (by an agent, at the end of a session) and can be stale or simply wrong if the last session ended abruptly.

```
git log --oneline -15
git status --short
git diff --stat HEAD~5 2>/dev/null || git diff --stat
```

Compare. Commits after STATE.md's last update, or uncommitted changes it does not mention, mean work happened that was never written down. Say so explicitly — a silent gap is how a session repeats work that is already done.

**3. Nothing else, unless the user asked about a specific area.** Do not read the whole codebase "for context". The user is waiting.

## If STATE.md does not exist

Say so in one line, then reconstruct what you can from git history and the README, and offer to create STATE.md at the end of the session. Do not create it now — an empty state file written before any work is just noise.

## Report

Four headings, prose under each, under 200 words total. If the state is genuinely complex, go longer — but length should come from substance, not hedging.

**Done** — what landed since the previous handoff. One line each. Skip anything older; this is not a changelog.

**In flight** — started, not finished. Enough detail to resume cold: the file, the function, the exact error string, which step failed. "Auth is broken" is useless. "`verifyToken()` in src/auth.ts:42 throws `JWTExpired` on refresh tokens, only when the clock skew exceeds 30s" is resumable.

**Blocked** — what cannot proceed and on what. Name the blocker: a missing credential, an upstream bug, a decision only the user can make. If it is waiting on the user, say what you need from them.

**Next** — one or two things, with a reason. Not a menu. If there is an obvious single next action, say that and stop.

## Rules

- Report findings, not the act of reading. No "I'll now check the git log".
- Do not re-propose anything listed under **Decided** in STATE.md. Those were settled, with reasons. Re-raise one only if new evidence contradicts it, and say what the new evidence is.
- An open item is context, not an agenda. Mention it when it bears on what the user asks next. Do not open with a list of everything outstanding.
- Never invent progress. If STATE.md and git disagree, report the disagreement rather than picking the more flattering version.
