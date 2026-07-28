---
name: friction
description: >
  Log a friction - a rough edge or mistake in how the agent or the collaboration played out - to
  the friction log, to track it and later promote a recurring one into a rule or fix. Run with
  /friction.
disable-model-invocation: true
---

# Friction

A friction is a pattern - usually a rough edge or mistake in how an agent (or the user's collaboration with one) played out - that isn't yet clear-cut enough to act on directly.

`./.claude/local/friction/` holds one file per friction-slug: `[slug].md` (template: `friction-template.md`, in this skill's folder).

- Before logging a new friction: check whether `./.claude/local/friction/[slug].md` already exists for an obvious candidate slug (cheap file-existence check, not a full read). If not, `grep -rl` across `./.claude/local/friction/` by keyword for a similar pattern under a different name. Read any candidate file to confirm genuine overlap before treating it as a match - a hit isn't automatic proof, a miss isn't proof of absence (paraphrased entries won't show up in a literal search).
- No match found -> create a new file from `friction-template.md`. Match found -> append one line to that file's Occurrences list.
- Trust the existence-check and grep above - they surface only what's relevant, however many entries accumulate, so read only the candidates they turn up rather than the whole directory.
- The 2nd occurrence in a friction's Occurrences list is the trigger to act: log the occurrence first (history is never deleted, even once promoted), then stop and propose a rule/doc change to the user.
