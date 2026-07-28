---
name: create-plan
description: >
  Analyze a ticket and produce a step-by-step implementation plan (no code). Invoke this skill and
  provide the ticket - pasted, or a reference to fetch. Put any notes of your own below a line
  containing only `>>>`.
---

# Create implementation plan

Produce an implementation plan for an AI agent as a Markdown file, using `plan-template.md` (in this skill's folder) as the structure. Analysis and planning only - implement nothing.

Before planning, make sure the relevant instructions are in context: your global agent instructions, the project's, and the rule files for the areas you'll touch (`./.claude/rules/...`). These load only when a matching file is actually read into context - grepping or searching it doesn't trigger that - so read them yourself.

> The plan will be carried out by a smaller, coding-oriented model in a fresh session - typically
> run with low or no reasoning, and a weaker reasoner than the model writing the plan. Calibrate to
> that reader: write self-contained, make every decision explicit (leave no design question to the
> implementer), spell out non-obvious pitfalls and the *why* behind choices it might otherwise
> "correct", and don't rely on inference steps you would make yourself.

## Process

### Phase 1: Research (autonomous)

Research the code:
- Identify affected components
- Understand the current implementation
- Check dependencies and what can be reused
- Find similar patterns already in the codebase to follow
- Trace callers and data flows end-to-end before concluding what is or isn't affected

For each affected area, read its rule file (`./.claude/rules/...`) together with `./CLAUDE.md`'s layout map - they point you at where things live and at the non-obvious gotchas and blast-radius traps to plan around. A change usually spans several layers of an area, so check them all (e.g. a view and its controller, an entity and its service, an API endpoint and its DTOs, a migration and its entity) plus the existing tests.

### Phase 2: Interview

Deviates from your global "batch questions" default: dependent decisions can't be batched without guessing at branches nobody has resolved yet.

Goal: reach a shared, concrete understanding of every open point before planning - not just work through a fixed list once. Interview one question at a time:
- Before asking, check whether the answer is actually a fact you can find yourself (code, tooling, existing docs) rather than a decision only the user can make - only put real decisions to them.
- Work out which open decisions depend on each other and ask in that order. Wait for each reply before moving on.
- Treat the topics below as a starting point, not a checklist to clear - if an answer opens a new branch (an option, edge case, or constraint neither of you had listed), follow it with a new question on the spot, and drop or reshape any planned question an earlier answer has made moot.
- Continue until no genuine ambiguity remains, not until each topic has been touched once.

Topics likely relevant: scope (modules/layers/flows), UI/UX approach, API surface (mirrored elsewhere? migration? permissions?), validation/error handling, consolidation, explicit non-goals and related/dependent tickets, and ticket size (prefer splitting into functional stories over merge blocks - if you propose a split, say which story gets what and in which order).

### Phase 3: Plan creation

Create the plan based on the answers. Fill in the "Ticket delta" section with all clarifications, scope changes, and bugfixes that emerged during Phases 1-2.

### Phase 4: Critical reflection

Before presenting the plan:
- Verify that all file paths, line numbers, and method signatures match the current codebase (use search tools, don't assume)

Then reflect:
- "Is every assumption the table marks validated actually confirmed - by code or by the user?"
- "Are there contradictions between the requirements?"
- "Are acceptance criteria or edge cases missing?"
- "For each new API call or async operation: what happens on error? Is there a loading state? An error state?"
- "Could a fresh instance of the smaller model that will execute this plan carry out each step without inferring anything that is neither written in the plan nor plainly visible in the code?"

If any issues surface, raise them with the user before finalizing.

## Quality rules

- Affected files must include their corresponding tests.
- Every acceptance criterion must be testable.
- Design for testability: keep new logic (calculations, validation, state transitions, data mapping) in methods/classes that run without heavy infrastructure (a live DB, a container). Each step names the class carrying that pure logic. Where existing code couples logic to such infrastructure and is hard to unit-test, don't extend that pattern for new logic.
- Each step must include concrete test cases and validation steps.
- A step that adds or changes user-facing text: check `./CLAUDE.md` -> Conventions for the project's i18n handling. If it ships multiple languages, add the new key to every one, not just the default.
- Plan must be comprehensible for both AI agents and humans, but concise: the implementing agent reads the codebase itself, so state what to do, not what it can already see from file paths and signatures.

## Template fill-in instructions

When filling `plan-template.md`, follow these rules per section:

### Original ticket

Preserve the original ticket content **verbatim**. This is the reference point for the "Ticket delta" section and for anyone comparing implementation to requirements.

### Requirement & acceptance criteria

Tickets often bring their own acceptance criteria - adopt those as the baseline and sharpen them into testable form. Don't invent a parallel list. Mark any criteria you add as additions (-> Ticket delta). Derive criteria from scratch only when the ticket has none.

### Open questions / decisions

After Phase 2, list only questions that are still genuinely open, each with its current working assumption. Answered questions become Ticket delta entries or Assumptions rows instead.

### Assumptions & preconditions

List all assumptions made during planning. The implementing agent MUST validate each assumption before relying on it. Common categories: data formats, timezone handling, enum completeness, nullable fields, backward compatibility, infrastructure configuration.

### Architecture overview

**Optional** - include only when >=2 architecture layers are affected or the data flow is non-obvious. Skip for single-layer changes or straightforward bug fixes.

### Affected components & files

All file paths MUST be complete and relative to the repository root. No abbreviations with "..." in the path. The implementing agent must be able to find files directly.

**"Not affected" sub-section:** Only list areas where the exclusion is non-obvious and might confuse the implementing agent (e.g. a component that looks related but is intentionally left unchanged).

### Risk & blast radius

Fill in during Phase 1, finalize in Phase 4 (the template's blockquote states the "every Yes needs a step" rule - no need to repeat it). Add rows for any project-specific trap surfaced by the area's rule file or the architecture, beyond the six baseline rows - never replace or reorder the baseline rows themselves. Row 5 (shared method changed): list the callers found via grep (count + locations) so the implementing agent doesn't re-derive them. Row 6 (untested legacy logic): flag it and discuss with the user whether a characterization step is warranted - if yes, plan it as its own step before the modifying step, pinning the current behavior with unit tests (pure-logic parts only - behavior coupled to infrastructure like a DB may not be pinnable with the existing test setup).

### Merge blocks & merge order

Optional - only when the plan uses independently mergeable blocks. Blocks are the **fallback** for a ticket too large for one reviewable PR - prefer splitting it into functional stories first, one PR each (see Phase 2). When blocks are used, slice functional-first: each block should be usable and manually testable on its own. A dormant slice (merged but not yet wired up / unreachable) is allowed only when no functional cut is possible - state that in the invariant table and move its manual tests to the block that wires it up. Delete the whole section for single-PR changes, and explain why the chosen merge order is necessary.

### Implementation steps

- Steps must be in the correct order (dependencies considered)
- Each step must be independently verifiable
- Code examples are guidance, not exact templates
- Verification order per step: see the relevant `./.claude/rules/` file
- If using blocks: each block's steps are grouped under a block header

**Block-based plans:** Include the agent instruction blockquote from the template when using blocks. Each block ends with a "Final validation" section that the implementing agent uses as its "After all steps" checklist for that block.

### Manual testing checklist

List concrete steps the developer must check by hand after implementation - automated tests miss visual, layout, and UX regressions and other things only a human notices. Be specific: which entry point, which action, what to look for. Trim or skip when the change has no manually observable surface.

### Improvements & consolidation

**Optional.** Technical improvements noticed during analysis: duplication to resolve, shared utilities to extract, or follow-up refactorings outside ticket scope.

### Ticket delta

Compact log of decisions made during planning that deviate from, clarify, or extend the original ticket. Track this DURING planning - it becomes part of the final ticket report. Keep entries short (1 sentence each). Only note actual deviations, not confirmations.

### Summary

Fill in last. The module list must match the Affected components tables. Give the risk rating a one-line rationale.

## Output

Save the plan to `./.claude/local/tickets/[ticket-id]-plan.md`

## Input

- A line containing only `>>>` separates the ticket (above) from your notes and instructions (below). No `>>>` -> the whole input is the ticket, with no separate notes.
- The ticket is the plan's "Original ticket" section - preserve it there verbatim. It may be pasted above the marker, or only referenced (a link, a tracker ID). If referenced, fetch it and preserve what you get verbatim just the same.
- Ticket ID and title fill the plan header and become its filename. Take them from the invocation or from the ticket itself. Ask rather than invent if either is unclear.
- Your notes (below `>>>`) are guidance for you - context, corrections, and where or how to fetch the ticket. They are not ticket content. Keep them out of "Original ticket".
- Type/Epic: if stated, fill the plan header. Otherwise derive Type from the content when unambiguous, else ask in Phase 2. No epic = "None".
