---
name: retrospective
description: >
  Reflect on a session and identify improvements to the AI workflow documentation, agent behavior,
  and user communication. Run with /retrospective after an AI workflow session.
disable-model-invocation: true
---

# Retrospective

Reflect on the current chat session and identify improvements - to the AI workflow documentation, the agent's behavior, and the user's communication.

Before starting, re-read the `./.claude/` files that were used or changed during the session to ensure you work with their current state. If the session ran create-plan or implement-plan, also read the ticket's plan/log in `./.claude/local/tickets/`: the log's "Unforeseen obstacles", "Ticket deltas & plan deviations", and the Step log's "Issues & discoveries" are primary evidence for area 1, and the plan's "Open questions / decisions" and "Ticket delta" for area 2.

## Approach

Work through the following areas **one at a time**. For each area: analyze, present frictions in a table, then stop - let the user pick which friction(s) to tackle and in what order. Don't open discussion on a friction yourself. Discuss the chosen friction(s) with the user iteratively, propose changes (with diffs), and implement only after approval. Skip areas with no frictions.

**Thread tracking:** as soon as more than 2 frictions/threads are open in parallel, maintain a harness task list - one task per thread, slug in the subject - and keep it current every turn. The user may process threads in parallel across areas, park one ("pause todo X" - keep it pending until they say otherwise), or interleave answers. The task list, not message order, is the source of truth for what is open.

### 1. Agent perspective

What about the instructions, templates, or workflow caused friction, rework, or misunderstandings?

For each friction:
- What does the instruction say?
- What happened in the session?
- What would be clearer or more effective?

### 2. User perspective

Where did the user's communication lead to unnecessary iterations or misunderstandings?

For each friction:
- What was said or omitted?
- What did the agent interpret?
- Concrete suggestion for next time.

### 3. Structural improvements

Are there structural issues with the skills, templates, or their interplay? Consider: redundant sections, missing sections, wrong granularity, unclear data flow between files, sections that are never read or consumed.

## Where friction goes

- If it's already clear a fix or rule is needed, propose it directly, routed by scope:
  - personal, project-independent working-style rule -> `~/.claude/CLAUDE.md`
  - project-wide rule -> `./CLAUDE.md`
  - module-specific rule -> the matching `paths:`-scoped `./.claude/rules/` file
  - workflow mechanics -> the affected skill or template
- If the fix is clear but too large to do here, capture it as a self-contained agenda item in `./.claude/local/agenda/` (see `~/.claude/skills/agenda/SKILL.md`) so a later session can run it.
- Otherwise, log it in `./.claude/local/friction/` instead (see `~/.claude/skills/friction/SKILL.md`). On a friction's 2nd occurrence, promote it using the same routing as above.

<!-- Duplicated in skills/optimize-docs/SKILL.md. Edit both when changing. -->

## Applying a change

When you implement a chosen friction, you are editing instruction files - apply the care those files demand of any edit:

- **Preserve information.** Before replacing or deleting a passage, list the distinct points it makes. After editing, confirm each one either survived or was deliberately dropped and stated as such.
- **Trace the impact.** For each change, work out who reads the file, when it's active, and what breaks if it's missing or wrong.
- **Check readability.** Reread the new text cold, as a first-time reader. Rewrite anything that only parses on the second pass.

## Rules

1. **Consider multi-agent context** - plans and implementations may be executed by different agents in separate chat sessions. Changes must work across context boundaries.
2. **Scope is not limited to `./.claude/`** - if a friction leads to changes outside `./.claude/`, that's fine.
