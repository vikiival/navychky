# navychky

the cutest skills collection

A monorepo of agent skills. Each skill lives in its own directory under `skills/` and works with any agent that supports the [Agent Skills](https://agentskills.io) format (Claude Code, Codex, Cursor, OpenCode, Hermes, …).

## Install

All skills:

```bash
npx skills add vikiival/navychky
```

A single skill:

```bash
npx skills add vikiival/navychky --skill handoff
```

See what's available without installing:

```bash
npx skills add vikiival/navychky --list
```

Add `-g` to install globally (user-level) instead of into the current project.

## Skills

| Skill | What it does |
| --- | --- |
| [handoff](skills/handoff/SKILL.md) | Writes a lean, next-task-focused handoff file and starts a fresh session, instead of an expensive `/compact`. |

## Layout

```
skills/
  <skill-name>/
    SKILL.md        # required: YAML frontmatter (name, description) + instructions
    templates/      # optional supporting files
    references/
    scripts/
```

## Adding a skill

1. `npx skills init skills/<skill-name>` (or create `skills/<skill-name>/SKILL.md` by hand).
2. Set `name` to match the directory name, and write a `description` that says **when** to use the skill.
3. Add a row to the table above.
4. Check that it gets picked up: `npx skills add ./ --list`.

## License

MIT
