---
name: optimize-docs
description: >
  Audit or improve agent-instruction files - CLAUDE.md, AGENTS.md, and the docs under
  .claude/ or ~/.claude/ - for restructuring, cleanup, deduplication, or review. Analyzes and proposes
  before implementing, works one file at a time, stops for approval after each change.
  Run with /optimize-docs.
disable-model-invocation: true
---

# Optimize documentation

Help the user improve, restructure, slim down, or audit the agent-instruction files. These files are written for agents - the target is maximum agent value per context token. For the quality criteria and placement logic behind these rules, read `authoring-principles.md` in this folder.

## Rules

1. **Validate before changing** - the doc you're auditing is not a source of truth. Verify its claims (paths, names, commands, patterns) against the code, not against the doc. A command quoted in a doc counts as verified only after you run it verbatim once (noting the expected pre-existing hits) - reading it is not verification.
2. **Declare scope upfront** - before the first change, list which files will be analyzed and restate the user's audit criteria. At the end, confirm all declared files were covered or explain why any were skipped.
3. **One file per iteration** - unless changes are interdependent across files, then update them in the same step.
4. **Checkpoint after each change** - stop and wait for user input:
   - Step overview (all steps with status: done, current, open)
   - What was changed (show as diff, fall back to concise summary if diff is impractical)
   - Commit suggestion in a fenced code block, following the project's commit convention (omit the ticket reference if there is none)
   - Skip checkpoint for analysis steps with no findings. Summarize skipped steps at the next checkpoint
   - Adapt granularity to the task: section-level for audits, file-level for restructuring. For simple changes, propose and implement in one checkpoint cycle (propose -> user confirms -> implement + next proposal).
5. **Keep each file lean in proportion to its load cost** - always-loaded (the project's root instruction file, the global agent instructions) is strictest. Path-scoped rules come next (they load when a matching file is edited). Skills and templates are loosest (on demand) - and loosest still means earning every line.
6. **A hard constraint has to load before the action it forbids** - a skill or template won't do, since it loads only on demand and may not be in context when it matters. Universal constraints belong in an always-loaded location (the project's root instruction file, or a rules file it loads). Module-specific ones belong in the matching path-scoped rule file, which loads when that module is edited.
7. **Keep analysis concise** - show the per-criterion assessment as a table, not prose.
8. **Obviousness is not grounds to cut a rule** - only genuine duplication elsewhere in these docs, or a factually wrong/stale claim, justifies dropping content. Rules often encode past failures. Before trimming or rewording one, construct 2-3 concrete scenarios where a capable model without it would plausibly still do the wrong thing (e.g. copying a bad nearby example, defaulting to a generic assumption). If you can't and the rule's origin is unknown, ask before cutting.
9. **Cross-reference check** - if content seems to belong in a different instruction file, propose the move explicitly rather than fixing it in place or noting it silently.
10. **Full audit pass** - when the task is a systematic audit of a file (not a targeted fix), follow `verification-workflow.md` in this skill's folder: 6 checks (factual accuracy, necessity, actionability, contradictions, staleness, staleness-proneness) plus a grep-based proofreading pass.

<!-- Duplicated in skills/retrospective/SKILL.md. Edit both when changing. -->

## Applying a change

When you implement a change, you are editing instruction files - apply the care those files demand of any edit:

- **Preserve information.** Before replacing or deleting a passage, list the distinct points it makes. After editing, confirm each one either survived or was deliberately dropped and stated as such.
- **Trace the impact.** For each change, work out who reads the file, when it's active, and what breaks if it's missing or wrong.
- **Check readability.** Reread the new text cold, as a first-time reader. Rewrite anything that only parses on the second pass.

## Context

Before starting, list the agent-instruction files to understand the current structure: the `./CLAUDE.md` (or `./AGENTS.md`) and everything under `./.claude/` (including subdirectories, excluding the untracked `./.claude/local/` scratch). Global files in `~/.claude` are in scope only when the task targets them - Rule 2 pins the actual scope per run. Read files on demand as the task needs.
