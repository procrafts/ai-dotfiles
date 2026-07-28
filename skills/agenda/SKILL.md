---
name: agenda
description: >
  Maintain a personal, local-only list of self-chosen tasks and track progress on them.
  Run with /agenda.
disable-model-invocation: true
---

# Agenda

A personal, local-only list of self-chosen tasks - not for team consumption, not tied to the project's issue/ticket tracker. Agents maintain it on request.

`./.claude/local/agenda/` holds one file per item: `[slug].md` (template: `agenda-item-template.md`, in this skill's folder). A new item starts as a short stub (title, date, status, description). When work begins, the same file grows working sections (Goal, Progress, Notes).

**Rules for agents:**
- Open items only - a done item's file is deleted, no "Done" log kept (no forward value, and nothing else records history in the agenda).
- Adding or adjusting an item needs no confirmation. Deleting an item file - or rewriting one in a way that discards existing content - needs a quick go-ahead first, since `./.claude/local/` has no git safety net. Don't delete an item whose Progress list still has open steps (`[ ]`/`[~]`) - it isn't done yet.
- Session-internal TODOs (current session only) are tracked in the chat context, not the agenda.
- **Write self-contained** - a fresh session with zero memory of the current conversation must be able to understand and act on every item. Don't reference this session's internal shorthand (phase/idea numbering like "3a-bis"/"I10", a file that got deleted, "the handoff") - spell out the actual fact or link a file that still exists instead. Exception: a deliberately terse personal memory-jog is fine, but mark it as such inline so it isn't mistaken for an oversight.
- **Date new items** - record *(YYYY-MM-DD)* in each item when created, via `date +%Y-%m-%d`, don't guess it.
- **Slug new items** - a short, unique, kebab-case filename slug, so it can be kicked off with minimal typing ("agenda item `#slug`") - no file attachment needed.

## While working

- When starting work on an item, expand its file in place with the working sections (Goal, Progress, Notes) from `agenda-item-template.md`.
- **Slug every step** - short, unique, kebab-case - so a fresh session can open with "agenda #[slug] #[step-slug]" and jump straight to both, no file attachment needed.
- **Item status** - a status line near the top of each file: `open`, `in progress`, or `blocked` (reason directly below if blocked). "Done" isn't a status - the file is deleted.
- **Step status** - `[ ]` open or `[~]` active - only one step carries `[~]` at a time. "Blocked" is not a checkbox state - any step, open or active, can be blocked. Note it and why directly in that step's text (e.g. "... - blocked: waiting on X").
- Completed steps are removed, not checked off - same "no Done log" convention as the agenda itself.
- The last Progress step is always `#cleanup`: transfer any Notes with lasting value to their real home (code, ticket, a rule/doc) so nothing is lost, then delete the file. Work it once it's the only step left.
- Before starting the next step, don't just work the list top-to-bottom - weigh the open steps yourself and propose which one to tackle next, with a short rationale.
- When something new surfaces while working, don't resolve it inline and leave it untracked - route it, and let the user choose. If it's part of this item's work, add it as a Progress step. If it stands on its own (doesn't need this session's context), actively propose spinning it off as its own, self-contained agenda item. Don't move it unprompted.
