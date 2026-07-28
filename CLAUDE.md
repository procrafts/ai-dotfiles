Personal agent instructions, skills, and settings for Claude Code. The files here are the active global configuration - they are symlinked into `~/.claude/` where Claude Code reads user-level config. See README.md for the symlink setup and the three-layer model.

## Structure

- `agent-instructions.md` - working-style and engineering-method rules, loaded every session as `~/.claude/CLAUDE.md`
- `settings.json` - permissions, skill overrides, model defaults, symlinked to `~/.claude/settings.json`
- `skills/` - reusable workflows invoked on demand, symlinked to `~/.claude/skills`
- `templates/project/` - starting point for a new project's `./CLAUDE.md` and `./.claude/rules/`

## Working in this repo

When editing instruction files here, use `/optimize-docs`.
