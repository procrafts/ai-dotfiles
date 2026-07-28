# Authoring principles for instruction files

Principles governing how optimize-docs evaluates, rewrites, and places content. They are the quality criteria behind the SKILL's rules - read alongside it, not instead of it.

## Placement

- Anchor a rule at the narrowest scope that covers every case where it must fire: always (including ad-hoc) -> `~/.claude/CLAUDE.md`. Only in a specific workflow -> that workflow's skill. Only when editing a module -> the matching `paths:`-scoped rule file.
- Never write a rule in two places. Workflow skills carry only their deltas over the global baseline.
- A generic rule without a triggered partner is weak - it gets known but not applied in the moment. Pair: thin global stance + concrete enforcement that fires at action time (a `paths:` rule loading at edit, a skill step loading at invocation).

## Writing rules

- Positive imperative is the default. Use don't-do contrast only for behavior the model gets wrong unprompted.
- Add a rationale only where the "why" isn't obvious and its absence would cause wrong behavior. If the agent complies without it, the rationale is dead weight.
- Bullets over prose or tables - faster to parse, cleaner to diff.
- "IMPORTANT" / "YOU MUST" is an emphasis lever, not a volume knob. Reserve for must-always constraints the model otherwise underweights.
- When clear wording still gets violated, the fix is hooks or tooling, not more instruction text.

## Evaluating content

- Unproven content is not a battle scar. Rules written speculatively (during setup, before real use) get pruned aggressively - verify against reality before enshrining. Exception: rules guarding high-consequence or irreversible failures stay even unproven. The rule is cheap, the failure catastrophic.
- A rule that restates model or harness defaults wastes tokens. Test: remove it - if behavior doesn't change, cut it.

## Rewriting

- A rewrite must decode without the original. Preserving information is necessary but not sufficient - the new text must be comprehensible to a reader who never saw the old version. Jargon and abstract shorthands that only parse with source knowledge are a failure.
- Test: read your rewrite cold, as if the original never existed. If any phrase requires "what they used to call X" to make sense, rewrite that phrase.

## Cost budget

- Always-loaded files stay under ~200 bullets (not raw lines - after the one-line-per-bullet convention, count bullets or estimate tokens). Prune or split before crossing that.
- Templates and skill instructions prioritize clarity over brevity - they load on demand and their context cost is bounded. Be explicit, use examples. The token discipline shifts to what they produce: generated artifacts must be lean.

## Skill authoring

- Description = function + trigger. Not a body recap - the description is the agent's decision surface for invocation, not a preview of the steps inside.
- User-invoked skills don't load their description into context between invocations. Reference them by file path in always-loaded files, never by name - the name is invisible to the agent.
- Each piece of guidance lives in exactly one place (single source of truth). Co-locate templates and checklists with the one skill that uses them. If a second skill needs it, promote to a shared location or its own skill.
