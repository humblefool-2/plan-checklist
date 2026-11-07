# plan-checklist

A Claude skill. It saves a plan, or the open work of a conversation, as a markdown checklist in your project. Later, in a new session, it resumes work from that file.

Use it when work spans more than one session. The checklist keeps the context, the finished steps, and the next step. You do not need to explain the task again.

## What it does

**Mode A: create a checklist.** The skill reads one of these sources, in this order:

1. A plan or scope you name in the arguments.
2. The plan you approved in this session (via `ExitPlanMode`).
3. The open work of the current conversation. Finished milestones appear as checked items.

It writes `<slug>-checklist.md` in your project directory. The file has a title, metadata, a short context section, numbered steps, and a notes section.

**Mode B: resume a checklist.** Point the skill at an existing checklist file. It will:

1. Read the file.
2. Summarize what is done, what is pending, and the next step. It also shows your notes.
3. Wait for you to choose the work. It does not start by itself.
4. Update only the changed lines when a step is done. It flips `[ ]` to `[x]` and adds a dated note when needed.
5. Set `Status: Done` when all steps are finished.

## Features

- Numbered steps with dependency markers: `(after: N)` and `(parallel)`.
- Checked items for finished work, so a new session sees the state.
- Optional phase headings kept from the original plan.
- Metadata: created date, source, source project, ticket (only if you give one), status.
- Safe by design:
  - Manual only (`disable-model-invocation: true`). It never runs on its own.
  - Asks before it overwrites an existing checklist.
  - Asks which directory to use when it is not clear. It does not guess.
  - Never copies secrets or personal data into the file.
  - Keeps a "check before you send" step when the work includes production writes.
  - Resume edits are surgical. It never rewrites the whole file.

See [examples/example-checklist.md](examples/example-checklist.md) for sample output.

## Install

### Option 1: copy the skill (personal use)

```bash
git clone https://github.com/humblefool-2/plan-checklist.git
mkdir -p ~/.claude/skills
cp -r plan-checklist/skills/plan-checklist ~/.claude/skills/
```

For one project only, copy it to `<project>/.claude/skills/` instead.

### Option 2: install as a plugin

```
/plugin marketplace add humblefool-2/plan-checklist
/plugin install plan-checklist@plan-checklist
```

Restart Claude or Claude Code after you install.

## Usage

Create a checklist from the plan you just approved:

```
/plan-checklist
```

Create a checklist from a specific scope in the conversation:

```
/plan-checklist what is left on the login fix
```

Save to a given directory:

```
/plan-checklist save to ~/code/my-service
```

Add a ticket:

```
/plan-checklist ticket PROJ-123
```

Resume from a checklist, in a new session:

```
/plan-checklist continue the login-fix checklist
/plan-checklist ~/code/my-service/login-fix-checklist.md
```

If the skill has no clear directory, it asks you. If the conversation has several unrelated topics, it asks which one to save.

## Checklist format

```markdown
# <Title>

- Created: 2026-01-15
- Source: plan | conversation
- Source project: <directory>
- Ticket: PROJ-123
- Status: In progress

## Context
<2-5 sentences: goal, constraints, key facts found so far>

## Steps
- [x] <finished milestone>
- [ ] 1. <first concrete step>
- [ ] 2. <next step> (after: 1)
- [ ] 3. <independent step> (parallel)

## Notes
<filled in over time>
```

## Tips

- Commit checklists with the project, or add `*-checklist.md` to `.gitignore`. Choose one.
- Keep steps concrete. Name files, functions, and services. A future session can then act without the old conversation.
- Run the skill at the end of a session, when you stop with work still open.

## Repository layout

```
.claude-plugin/
  plugin.json           plugin manifest
  marketplace.json      marketplace entry
skills/
  plan-checklist/
    SKILL.md            the skill
examples/
  example-checklist.md  sample output
```

## License

MIT. See [LICENSE](LICENSE).
