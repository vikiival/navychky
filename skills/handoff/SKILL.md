---
name: handoff
description: Use when the context window is filling up, the user says "handoff", "handover", "wrap up for a new chat", or is about to run /compact or /clear. Writes a lean, next-task-focused handoff file so work continues in a fresh session instead of an expensive compaction.
---

# Handoff

Replace `/compact` with a deliberate handoff: write a short file aimed at the **next task**, then start a fresh session that reads it.

Why this beats the alternatives:

- `/compact` re-reads and summarises the whole session — expensive, and you don't choose what survives.
- `/clear` gives you a blank slate and nothing carried over.
- A handoff file is cheap to write (it targets one next task, not the whole history), you control exactly what carries over, and the old session stays intact for `/resume` if you need it.

## When to use

- Context usage is high (roughly >60–70%) or responses are getting slow or forgetful.
- A natural seam: one task finished, the next one is starting.
- The user asks for a handoff/handover, or is about to compact or clear.

Don't use it for a trivial follow-up that fits in the current context — just keep going.

## Procedure

1. **Pin down the next task.** If the user hasn't said what comes next, ask one short question. A handoff written for "whatever's next" turns into a summary, and summaries are what we're avoiding.
2. **Check the ground truth** instead of trusting your memory of the session. Use cheap commands:
   - `git status --short`, `git diff --stat`, `git log --oneline -10`
   - the current branch, any failing test or command you were in the middle of
3. **Write the file** to `.handoff/YYYY-MM-DD-<short-slug>.md` in the project root, using [templates/HANDOFF.md](templates/HANDOFF.md). Create `.handoff/` if needed. If the project is a git repo and `.handoff/` isn't ignored, offer to add it to `.gitignore` (don't do it silently).
4. **Keep it lean.** Aim for 40–120 lines. Include only what the next session can't cheaply rediscover:
   - decisions and *why* (including rejected approaches, so they don't get retried)
   - non-obvious constraints, gotchas, user preferences voiced in this session
   - exact file paths, commands, error messages, IDs, URLs
   - concrete next steps with a definition of done
   Leave out: the conversation narrative, code that's already on disk (point to `path:line` instead), and anything `git log` already says.
5. **Self-check before saving.** Read it as a cold start: could a fresh agent with no history start the next task in under a minute? Remove anything that fails the "would they miss this?" test.
6. **Hand back to the user** with:
   - the file path
   - a one-line kickoff prompt to paste into the new chat, e.g.
     `Read .handoff/2026-10-21-auth-refresh.md and continue from "Next steps". Verify the state section against the repo before editing.`
   - a reminder that the old session is still there (`/resume` in Claude Code, `codex resume` in Codex).

Stop there. Don't start on the next task in the current session — that defeats the point.

## Picking up a handoff (in the new session)

When asked to continue from a handoff file:

1. Read it in full.
2. Check "Current state" against reality (`git status`, branch, run the failing command). Trust the repo over the file if they disagree, and mention any drift.
3. Begin at "Next steps". Don't re-explore anything already settled under "Decisions" unless new evidence contradicts it.
4. When the task is done, delete or archive the handoff file if the user agrees.

## Pitfalls

- **Summarising instead of handing off.** If the file reads like meeting minutes, rewrite it around the next task.
- **Stale state.** Write the state section from commands you just ran, not from memory.
- **Secrets.** Never put tokens, passwords, or private keys in the file. Refer to where they live (env var name, vault entry).
- **Over-length.** A 400-line handoff costs almost as much as compacting. Trim.
- **Dangling work.** Before writing, make sure there are no half-applied edits. Either finish the edit or list it explicitly under "In flight".
