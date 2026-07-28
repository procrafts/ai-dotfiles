---
paths:
  - "src/{{area}}/**"   # globs that load this file. Scope to the area's files
---

<!-- One file per area with non-obvious gotchas (not reflexively per module). It loads only when a matching file is read into context - editing one triggers that, searching does not - so keep it to THIS area. Group by shared concern. Capture what an agent would get wrong. Mark anything unverified as "unconfirmed". `{{double-curly}}` spans are filled in once, when this file is created for a new area. -->

## Topic outline

<!-- What breaks here that isn't obvious. Kinds of entry:
 - a reference the build doesn't check (template binding, string-literal name, config key) and what to do on rename/delete
 - a change that ripples to callers elsewhere ("grep all callers before changing")
 - pre-existing debt to flag, not copy or fix in passing -->

- {{gotcha 1}}
- {{gotcha 2}}

## Verification checklist

<!-- Concrete commands, what a hit means, false positives to eyeball, and where nothing automated catches it (self-check that boundary by hand - the concrete partner to the global "you are the enforcement" rule). -->

- {{command - what a hit means, which false positives to ignore}}
- {{boundary with no automated check - verify by hand before merging}}
- {{build/test command - what it proves and what it doesn't}}
