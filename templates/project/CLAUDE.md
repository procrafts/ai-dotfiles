# {{Project name}} - agent context

<!-- Project-specific context for agents. Global working style and engineering rules live in ~/.claude. Don't repeat them here. Keep this file minimal and team-shared.

Include only what an agent can't cheaply derive, or would get wrong by guessing: intent, current-vs-legacy, conventions, canonical commands, and pointers. Don't restate tech it already knows or facts it reads straight from the repo.

For example: skip "Built with Spring Boot and PostgreSQL" (the agent reads that from the build files). Instead capture things like "payments must stay idempotent - the gateway retries on timeout."

`{{double-curly}}` spans are filled in once, when setting up this file for a new project. -->

## Overview

{{One or two sentences: what the system does and for whom. If the domain has jargon, list the key terms an agent meets in code and tickets.}}

## Layout

{{A map of the modules/directories: what each part is, and where a new feature goes. Add a one-line stack summary for quick orientation. Exact versions live in the build files, not here.}}

## Architecture

{{The global dependency direction and the hard boundary rules (for example, "domain logic lives only in module X"). This stays here because it is cross-module and always needed. Module-internal detail belongs in `./.claude/rules/{{area}}.md`, which loads only when that area is edited.}}

## Conventions

- Commits: {{format, types, scope, how to reference issues. State it even if the git history is inconsistent.}}
- Branching / PRs: {{branch model, naming, how PRs are opened and merged, what gets deployed.}}
- API: {{style and current version. Call out any frozen or legacy surfaces to avoid. Put detail in `./.claude/rules/{{area}}.md` if it is complex.}}
- i18n / UI strings: {{how UI strings are handled, if this applies. Point to the rule file if there is a sync gotcha.}}

## Build & verify

{{The canonical build, test, and run commands, with the right flags. This is the agent's quality gate: it should run all of them. Point to a run skill if one exists.}}
