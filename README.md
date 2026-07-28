# Claude Code dotfiles

Personal, global configuration for [Claude Code](https://code.claude.com), shared across every project on this machine. This README is for human readers. The files it describes are written for Claude agents.

## Layout

| Path | Purpose |
|---|---|
| `agent-instructions.md` | Global working style and engineering method - the always-on baseline for every session |
| `settings.json` | Harness config: model, permissions, attribution, skill overrides, memory |
| `skills/*/SKILL.md` | Workflows and utilities, invoked on demand - see below |
| `templates/project/` | Starting point for a new project's `./CLAUDE.md` and `./.claude/rules/` |
| `CLAUDE.md` | Project-level context for working in this repo itself |

`agent-instructions.md` is what Claude Code auto-loads as `~/.claude/CLAUDE.md` via symlink.

## Install

Claude Code reads user-level config from `~/.claude/`, which also holds runtime state (sessions, history). So this repo is not symlinked wholesale - link the individual pieces:

```bash
ln -s "$PWD/agent-instructions.md" ~/.claude/CLAUDE.md
ln -s "$PWD/skills"                ~/.claude/skills
ln -s "$PWD/settings.json"         ~/.claude/settings.json
```

## Three layers

Instructions merge softly across three layers (settings, by contrast, follow a hard precedence):

1. **Global baseline** (`~/.claude/`, this repo) - personal working style and defaults, present in every project. A baseline, not an override.
2. **Project / team** (committed `./CLAUDE.md`, `./.claude/rules/`, `./.claude/settings.json`) - domain, conventions, project settings.
3. **Project-local** (untracked `./CLAUDE.local.md`, `./.claude/settings.local.json`, `./.claude/local/`) - personal exceptions for one project. The reliable place for an override.

## Skills

The ticket workflow, in order:

1. **`/create-plan`** - give it a ticket (pasted, or a reference to fetch). It researches the code, interviews you on open decisions one at a time, and writes a self-contained plan to `./.claude/local/tickets/[ticket-id]-plan.md` with a risk and blast-radius table, tests-first steps, and a manual-testing checklist. Plans target a weaker implementing model, so every decision is made at planning time.
2. **`/implement-plan`** - invoke with the ticket ID. It implements one step at a time (tests first, red-green evidence), verifies each step, keeps a log at `./.claude/local/tickets/[ticket-id]-log.md`, and stops for approval at every checkpoint. At the end it produces a German ticket report.
3. **`/retrospective`** - run right after planning or implementation. It mines the plan, the log, and the chat for improvements to these instruction files and proposes changes.

Utilities:

- **`/optimize-docs`** - audit and improve the instruction files (`CLAUDE.md`, `AGENTS.md`, `.claude/`).
- **`/friction`** - log a rough edge or mistake in how the agent or the collaboration went, to track it and later promote a recurring one into a rule.
- **`/agenda`** - a personal, local-only list of self-chosen tasks, kept separate from a project's ticket tracker.
- **`/mine-instructions`** - extract rules worth adopting from an external source (another project's `AGENTS.md`, a skill, any rules text).
- **user-voice** - triggers automatically when the agent writes German text on your behalf (ticket or PR comments), so it reads in your voice.

Generated artifacts and personal scratch live under each project's git-ignored `./.claude/local/`: plans and logs under `tickets/`, friction under `friction/`, agenda items under `agenda/`.

## Bootstrapping a new project

1. Copy `templates/project/CLAUDE.md` to the new project's root and fill the `{{...}}` placeholders (overview, layout, architecture, conventions, build commands).
2. For each area with non-obvious gotchas, add a `./.claude/rules/{{area}}.md` from `templates/project/.claude/rules/module.md`, and set its `paths:` glob so it loads when that area is edited.

Keep the project's `./CLAUDE.md` lean - it carries only what an agent cannot cheaply derive from the code.

## Maintaining these files

- English, and US-keyboard characters only (see the keyboard-only rule in `agent-instructions.md`).
- Verify every claim against reality before writing it down. These files are instructions, not documentation, so a wrong claim becomes wrong agent behavior.
- Global working style and engineering rules go in `~/.claude/CLAUDE.md`. Project domain and conventions go in `./CLAUDE.md`. Module-specific gotchas go in a `paths:`-scoped `./.claude/rules/` file.
- Placeholder convention in templates: `[square brackets]` = filled fresh each execution (plans, logs, frictions). `{{double braces}}` = filled once when setting up infrastructure (new project, new rule file).
- `settings.json` here is global. Per-project overrides belong in that project's uncommitted `./.claude/settings.local.json`.
- Sub-agents see only the project's root `./CLAUDE.md`, not path-scoped rules or skill files. When writing a skill that delegates, embed every constraint the sub-agent must follow directly in the delegation prompt.
- User-invoked skills don't load their description into context between invocations - the agent can't see them by name. Reference them by file path (e.g. `~/.claude/skills/agenda/SKILL.md`) in always-loaded files, never by skill name.
