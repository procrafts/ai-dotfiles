# Implementation plan: [Short title]

- **Ticket:** [ticket-id] - [Title]
- **Type:** Problem | Story | Task
- **Epic:** [ticket-id] - [Epic title] *(or "None")*

## Original ticket

> [Paste original ticket content here, as a blockquote]

## Requirement

[Functional description of the requirement in 2-4 sentences. What should change? Why? This is the agent's interpretation of the ticket, enriched with technical context.]

### Acceptance criteria

- [Criterion 1]
- [Criterion 2]
- ...

---

## Open questions / decisions

- [Question 1]
- [Question 2]

<!-- If none: write "None - all questions have been clarified." instead of a list. -->

---

## Assumptions & preconditions

| # | Assumption | Validated? | How |
|---|---|---|---|
| 1 | [e.g. "Date inputs are always parsed as midnight UTC"] | yes / no | [e.g. "Checked date parsing in request handler"] |

<!-- If none: write "No assumptions - all aspects are explicitly specified in the ticket or codebase." instead of a table. -->

---

## Architecture overview

[Description of affected architecture layers, data flow, and component relationships]

---

## Affected components & files

### New files

| File | Description |
|---|---|
| `[full/path/from/repo/root/File]` | [Description] |

### Files to modify

| File | Description |
|---|---|
| `[full/path/from/repo/root/File]` | [Description] |

### Secondary (review / possibly minor adjustments)

| File | Description |
|---|---|
| `[full/path/from/repo/root/File]` | [Description] |

### Not affected

| File / Area | Reason |
|---|---|
| `[Path or area]` | [Why not affected] |

---

## Risk & blast radius

> Every "Yes" must name a mitigation/verification step in Implementation steps - no dangling risks.
> The six traps below are a generic baseline. Add project-specific ones as further rows if the
> area's rule file (`./.claude/rules/...`) or the architecture surfaces others.

| # | Trap | Hit? | Mitigation step |
|---|---|---|---|
| 1 | Schema / DB migration involved (see your DB-migration rule) | Yes/No | [Step N / -] |
| 2 | Rename/signature change referenced from non-compiled places (templates, i18n keys, config, reflection) - the build won't catch it | Yes/No | [Step N / -] |
| 3 | Change to a consumer-facing or versioned API/contract that another surface mirrors | Yes/No | [Step N / -] |
| 4 | Touched code that is duplicated and must stay in sync | Yes/No | [Step N / -] |
| 5 | Shared method changed - callers: [grep count + locations, or "-"] | Yes/No | [Step N / -] |
| 6 | Untested legacy logic modified | Yes/No | [Step N / -] |

---

## Merge blocks & merge order

```
Block 1 (...)  -->  Block 2 (...)  -->  Block 3 (...)
  [effect]            [effect]            [effect]
```

| Block | Branch | Files | Dependency |
|---|---|---|---|
| **1 - [Name]** | `[ticket-id]-[slug]` | [count] | None |
| **2 - [Name]** | `[ticket-id]-[slug]` | [count] | After Block 1 ([reason]) |

**Invariant after each block** - every merged state must leave the application working and shippable (new code may be dormant/unreachable). The main branch can be deployed at any time:

| After Block | System state |
|---|---|
| **1** | [What works, what doesn't yet] |
| **2** | [What works now] |

---

## Implementation steps

### [Block 1: Name]  <!-- Only for block-based plans -->

---

### Step 1: [Title]

**Goal:** [What should be achieved?]

**Files:**
- `[full/path/from/repo/root/File]`

**Details:**
- [Detail 1]
- [Detail 2]

**Tests** (write these first - red before implementing):
- `[Test name 1]`
- `[Test name 2]`

**Validation:**
- [ ] [How does the agent verify the step succeeded? E.g. "the project's build/test command for the module passes", plus the Verification checklist of the matching `./.claude/rules/` file]

### Step 2: [Title]

...

---

### [Block N] - Final validation  <!-- Only for block-based plans -->

**Quality gates:**
- [ ] [Build/test commands for the affected modules]

**Acceptance criteria covered by this block:**
- [ ] [AC from the plan's Acceptance criteria section that this block satisfies]

**Deliverables:**
- [ ] Cross-reference changed files against plan - log discrepancies
- [ ] Update implementation log
- [ ] Generate ticket report for this block

---

## Manual testing checklist

- [ ] [Page/View or entry point]: [Action to perform] -> [Expected result]
- [ ] [Page/View or entry point]: [Action to perform] -> [Expected result]
- [ ] ...

---

## Improvements & consolidation (optional)

- [Improvement / consolidation suggestion]

---

## Ticket delta

**Clarifications:**
- [What was ambiguous in the ticket -> what was decided]

**Scope changes:**
- [What was added/removed compared to the ticket]

**Bugfixes included:**
- [Bugs discovered during analysis that are fixed along the way]

<!-- If none: write "Plan matches ticket description exactly." instead of the three sub-lists. -->

---

## Summary

| Aspect | Details |
|---|---|
| **Affected modules** | [modules touched] |
| **New files** | [Count and description] |
| **Modified files** | [Count and description] |
| **DB migration** | [Yes/No - details] |
| **Risk** | [Low/Medium/High - rationale] |
