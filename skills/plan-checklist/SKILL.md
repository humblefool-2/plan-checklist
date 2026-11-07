---
name: plan-checklist
disable-model-invocation: true
description: >
  Turn a plan, or the open work of the current conversation, into a persistent
  checklist file in the relevant project directory, or resume work from an
  existing checklist file. Use when the user runs /plan-checklist, says "save
  this plan as a checklist", "checklist this plan", "checklist what is left" or
  "checklist this", or points to a `*-checklist.md` file and wants to continue
  it.
---

# Plan Checklist

Saves a plan, or the open work of the current conversation, as a standalone
markdown checklist in the relevant project directory (see step 3 of Mode A) so
work on it can span multiple separate sessions. Manual only — the user runs this themselves when
they want it; do not run it automatically after a plan is approved or after
work is discussed.

Two modes. Decide which one applies from `$ARGUMENTS` and conversation context.

## Mode A — Create a checklist (from a plan, or in general from the conversation)

Use this when there is work to save. There are two sources:

- **Plan:** a plan just approved via `ExitPlanMode` in this session, or a plan
  the user describes/pastes now.
- **General (no plan):** the current conversation. Typical cases are an
  investigation or fix that is partly done, a list of things still left, or
  an ad-hoc task list the user gave. No approved plan is needed.

1. Get the content, in this order:
   - If `$ARGUMENTS` names a plan, a file, or a scope (for example "what is
     left on the login fix"), use that.
   - Otherwise, if a plan was approved in this session, use the most recent one.
   - Otherwise, build it from the conversation: the work still open, the
     decisions still pending, and the checks still to do. List finished
     milestones as checked items (`- [x]`) so a future session sees the state.
   If the conversation holds several unrelated threads and `$ARGUMENTS` does
   not pick one, ask the user which one to save. If there is nothing to save,
   say so.
2. Derive a short kebab-case slug from the task/title (3-6 words).
3. Pick the directory for the file, in this order:
   - A directory the user gave in `$ARGUMENTS`. Use it as given.
   - Otherwise, the project directory the work is in (for example
     `~/code/my-service`). Use it only when it is clear
     from the plan or conversation.
   - Otherwise, ask the user which directory to use. Do not guess. Do not use
     one fixed default directory.

   Then check `<dir>/<slug>-checklist.md`. If it already exists, ask before
   overwriting — the user may want a new file instead (append a date suffix)
   or to resume the existing one (switch to Mode B).
4. Write the file with this structure:

   ```markdown
   # <Title>

   - Created: <YYYY-MM-DD>
   - Source: <plan | conversation>
   - Source project: <dir the work is in, if known>
   - Ticket: <issue key and/or PR link, e.g. PROJ-123; omit the line if none>
   - Status: In progress

   ## Context

   <2-5 sentences: the goal, and any constraint/decision that shaped the work.
   For a conversation checklist, also the key facts already found (ids, file
   paths, what was ruled out), so a future session does not repeat the
   investigation. Enough to orient without re-reading the original plan or
   conversation.>

   ## Steps

   - [x] <finished milestone, only for a conversation checklist>
   - [ ] 1. <first concrete step — name the file/function/service it touches>
   - [ ] 2. <next step> (after: 1)
   - [ ] 3. <independent step> (parallel)
   - [ ] ...

   ## Notes

   <empty — filled in over time as work progresses>
   ```

   Number every step. Add `(after: N)` when a step needs earlier steps to be
   done first. Add `(parallel)` when a step can run at the same time as
   others. A step with no marker follows the previous step in order.
   Take the Ticket value from the plan, the conversation, or `$ARGUMENTS`.
   Never invent one.

   Keep each step concrete: file paths, function/module names, service names —
   whatever lets a future session act on it without the original plan or
   conversation. Preserve the plan's own phase grouping as sub-headings under
   `## Steps` if it had distinct phases.

   Never copy secrets or personal data into the file: no tokens, passwords or
   emails. Use ids only. If the work involves production writes, keep the
   "check before you send" step in the list.

5. Report the file path in chat. Do not open it in plannotator and do not
   print the full contents unless the user asks — a one-line confirmation
   plus the path is enough.

## Mode B — Resume from an existing checklist

Use this when the user points at an existing `<slug>-checklist.md` file (by
path, name, or "continue the X checklist").

1. Read the file. If the user gave only a name, search for
   `*<name>*-checklist.md` in the current project directory first, then in
   the parent directory of the current project and its subdirectories. If
   several files match, ask which one.
2. Summarize for the user: what's done (checked items), what's pending
   (unchecked items), and what the next unchecked item is. Surface anything
   under `## Notes`.
3. Stop there and wait for the user to say what to work on. Do not start
   implementing the next item on your own.
4. Once the user directs work and an item is completed in this session,
   update the file with a surgical edit: flip that item's `- [ ]` to `- [x]`,
   and append a short dated line under `## Notes` if anything worth
   remembering came up (a decision, a blocker, a deviation from the original
   step). Never rewrite the whole file — edit only the lines that changed.
5. If the user finishes everything in the checklist, change `Status:` to
   `Done` and confirm with the user before considering the file stale.
