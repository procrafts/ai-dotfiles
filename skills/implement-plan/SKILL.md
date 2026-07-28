---
name: implement-plan
description: >
  Implement a plan step by step with a checkpoint after each step.
  Builds on a plan created by create-plan. Invoke with just the ticket ID.
---

# Implement plan

Implement the plan for the ticket named in the user's message. Read the `./.claude/rules/` files for the areas the plan touches up front - they load only when a matching file is read into context (grepping doesn't trigger that), so their rules inform your approach announcement, not just your edits.

## Finding the files

Derive the ticket ID, then:
- Read `./.claude/local/tickets/[ticket-id]-plan.md` - required (stop if missing)
- Check whether `./.claude/local/tickets/[ticket-id]-log.md` exists - yes = resumed session (read it), no = new session

## Rules

1. **The plan is the source of truth** - follow it step by step. Do not skip steps or change the order unless a dependency forces it.
2. Read the plan and all affected files first - check prerequisites (migrations, packages, config) and announce your approach before coding.
3. **Implementation log:**
   - **New session:** create `./.claude/local/tickets/[ticket-id]-log.md` from this skill's `log-template.md` **before Step 1**, not at the first update
   - **Resumed session:** resume from the next incomplete step
   - **Log update cadence:**
     - After EACH step: Progress overview and the step's Step log entry
     - After each step IF applicable: Ticket deltas & plan deviations, Open TODOs, Unforeseen obstacles
     - End of implementation (or end of each block): Quality gate results, Acceptance criteria review, persist the ticket report(s) in the log's "Ticket reports" section, and set the header **Status** field (complete - set paused whenever pausing mid-implementation). For block-based plans: append results per block, do not overwrite previous entries.
   - **Ticket deltas & plan deviations:** Only add items newly discovered during implementation - planning-phase deltas already live in the plan's "Ticket delta" section.
   - **Open TODOs:** Track temporary workarounds left in the code, documentation updates, or recommended follow-up work to resolve before or after merging.
   - **Unforeseen obstacles:** Only significant blockers across the whole implementation (consolidated). Minor issues stay in the step's "Issues & discoveries". This section feeds the ticket report's "Hindernisse" section.
4. Implement one step at a time. Each step must be independently committable.
5. **Silent implementation** - no text output between receiving the go-ahead and delivering the checkpoint report. Exceptions: blocking question (ask the user directly), or reporting failure after 2+ failed attempts.
6. **Red-green step protocol:** within each step, in this order:
   1. write the step's tests first
   2. run them and confirm they fail (red), capturing the failing output
   3. implement
   4. run the tests again and confirm they pass (green)

   Both runs' evidence goes into the checkpoint report. If red-first isn't possible for a step (e.g. pure refactoring covered by existing tests), state that in the checkpoint instead of skipping silently.
7. **Tests are frozen once red-verified:** afterwards a step's tests may only change if the test itself is wrong - that is a plan deviation: flag it in the checkpoint report with justification and log it (Ticket deltas & plan deviations). Never adapt a test to make a wrong implementation pass. This binds rule 8's sub-agents too.
8. **When your implementation doesn't pass the step's test** (the red *after* you implement - the intentional red *before*, rule 6, is not a failure and never triggers this). A quick inline fix is fine, but after your second failed attempt, stop and hand it to a synchronous sub-agent - you're looping, and a fresh start breaks that. It only knows what your prompt gives it, so hand over the failing output, the frozen test, the changed production files, the relevant rule content (`./.claude/rules/` doesn't auto-load into a sub-agent), and this instruction:

```
Make the production code satisfy the test in at most three attempts. Fix obvious
production bugs, not design decisions. The test is frozen - never edit it to pass.
When you finish, list the production changes you made. If it isn't green by then,
or the test looks wrong, stop and report.
```

Review its changes against the plan. If still red, re-delegate once with correction context, then stop and hand to the user - never build later steps on a red test. The user decides: a stronger model, more tries, skip, or treat the test as wrong.
9. After each step: verify - run the build/test commands from `./CLAUDE.md` -> Build & verify for the modules you touched, plus a whole-project compile (confirms that modules depending on yours still build), plus the Verification checklist of the matching `./.claude/rules/` file - then provide the checkpoint report.
10. **Self-review before the checkpoint:** re-read the step's diff and check it against the plan step and the loaded `./.claude/rules/` files. Fix small findings, flag the rest in the checkpoint report.
11. **STOP after each checkpoint and wait for approval before continuing**
12. **After all steps** (or after the last step of each **block**, if the plan defines independently mergeable blocks):
    - Run the project's full quality gate - a complete build including the full test suite (`./CLAUDE.md` -> Build & verify)
    - If the quality gate fails, the Definition of Done is not met - do not report done (for block-based plans, do not start the next block). Fix it: a failing test runs through the step-failure protocol (rule 8). Fix a build break directly. If you can't resolve it, report the failing output and stop for the user - never present a done report on a red gate.
    - Review every acceptance criterion (for block-based plans: only the criteria assigned to the current block)
    - Cross-reference changed files against the plan (log any discrepancies)
    - Fill in Quality gate results and Acceptance criteria review in the log
    - Generate the ticket report (see format below)
    - **Definition of Done** - confirm before reporting:
      - all acceptance criteria reviewed
      - tests added/adapted for changed code
      - new UI-string keys added to every language the project ships (`./CLAUDE.md` -> Conventions)
      - mirrored/versioned surfaces checked if the plan's Risk & blast radius table flagged them
      - migration checks run (if the change includes a schema migration)
      - manual-test checklist handed over
      - log complete incl. Status
    - **Report to user:** the ticket report (format below) and the quality-gate status (compact, failures expanded)
    - For block-based plans: **STOP and wait for user approval before starting the next block.** The user merges each block separately.

## Checkpoint report format

After each step, report:

- Step overview (all plan steps with status: done, current, open) - compact table
- Changes made (files and what changed) - use a Markdown table with a blank line before it
- Verification results (build, tests, Verification checklist), incl. red/green evidence (failing run before, passing run after implementation)
- Manually testable now: which Manual testing checklist items can already be checked after this step (or "none")
- Ticket delta (any deviation from the original ticket? If none: "None")
- Open issues/TODOs from this step (if none: "None")
- Commit suggestion (see `./CLAUDE.md` -> Commits for format)
- What comes next
- Then WAIT for my response ("continue", adjustments, or questions)

## Ticket report

At the end of implementation (per block for block-based plans), write the ticket report: a compact German summary that serves both as the report to the user and as text to reuse on a ticket or PR. Give it as a fenced code block only, in the user's voice (see the `user-voice` skill).

Sources: ticket deltas (plan's "Ticket delta" section, checkpoint "Ticket delta" entries, log entries of type "Ticket delta", excluding plan deviations) and open items (log's "Open TODOs"). Adapt "Manuelle Tests" to what was actually built: drop inapplicable tests, add ones for edge cases or flows found during implementation. Fill only the sections that apply.

For block-based plans: one report per block, covering only that block. After the last block, add a summary report covering all blocks (consolidated deltas, all acceptance criteria, combined manual tests). Persist all reports in the log's "Ticket reports" section.

```
[1-2 Sätze Kontext.]

Entscheidungen
- ...

Scope-Änderungen
- ...

Bugfixes
- ...

Hindernisse
- ...

Offene Punkte / TODOs
- ...

Manuelle Tests
1. ...
```
