# Instruction-file verification workflow

Reusable checklist for auditing any instruction file under `.claude/` or `~/.claude/`, or a chapter of the root `CLAUDE.md`/`AGENTS.md`, against reality before trusting or extending it.

## Checks

1. **Factual accuracy** - every concrete claim (technology, path, package, naming pattern, version, count) must be verified against actual code/config/git history, not assumed. Cite evidence (file:line, commit, %).

2. **Necessity, not obviousness** - run Rule 8's test on every rule in the file, not just the ones that look weak: obviousness alone is never grounds for a cut.

3. **Actionability** - check whether a rule is actually followed, not just stated. For conventions (naming, commit format, etc.), measure real compliance (e.g. % of recent commits/files matching) rather than only confirming the rule text exists. Near-zero real-world adherence isn't necessarily wrong, but flag it - it may need an explicit "ignore surrounding non-compliant examples" clause to become actionable.

4. **Contradictions** - check the rule against every other currently-loaded instruction file, not just itself. Watch for a generic rule (e.g. "match existing patterns") silently overriding a specific new one, or two files prescribing different things for the same situation.

5. **Staleness** - named files/classes/counts/dates cited in the rule: confirm they still exist and are still numerically accurate.

6. **Staleness-proneness** - separate from #5 (which checks current accuracy), judge whether the *phrasing itself* invites drift. A hardcoded enumeration of code artifacts (e.g. every service bean, every current package) or a specific count/finding (e.g. "~154 classes", "3 violations") goes stale the moment ordinary feature work touches that area - nobody remembers to update a docs line for an incidental class addition. Prefer phrasing that describes *how to find* the current state (a package name, a naming pattern, a search command) over a snapshot of it. Exception: when the enumeration itself is the deliberate, load-bearing content (e.g. "exactly these 4 packages are the intended structure"), not an incidental listing of whatever exists today.

## Process

1. List every atomic, independently-checkable claim in the target file/section.
2. Batch related claims by domain (e.g. stack/build, architecture, API/auth, git conventions, i18n) and verify each batch with a dedicated search (agent, or direct grep/read) - ask for evidence per claim, not a yes/no. Note that searching `./.claude/` needs `rg --hidden --no-ignore` - it is hidden and often git-ignored, so a plain search finds nothing there and you wrongly conclude content is unique or a rule unenforced.
3. Judge each claim/rule against the six checks above.
4. Report findings with a recommendation. Don't edit the target file yourself - wait for a decision.
5. Once content is settled, run the proofreading pass below - separate from steps 1-4, since these are copy-editing issues, not correctness issues, and get missed by content-focused review.

## Proofreading pass

Use `grep` for objective checks rather than eyeballing - free-hand reading reliably misses this class of issue:

- Spelling and grammar, full read-through (not a skim).
- Punctuation consistency within a list - do all bullets in a section end with a period, or none? Mixed is a defect. Check per-section, since fragment-style lists and full-sentence lists can legitimately differ from each other. One legitimate mix within a list: a bullet spanning several sentences takes a closing period even where single-fragment bullets alongside it don't.
- No non-keyboard characters - files use only US-keyboard ASCII plus `äöüÄÖÜß` and `€` (see the keyboard-only rule). Scan with `rg -nP '[^\x00-\x7F]'`. Every hit must be one of the allowed exceptions - anything else (em-dash, en-dash, curly quotes, ellipsis, stray emoji) is a defect, replace with the ASCII equivalent.
- One line per paragraph and bullet, no hard wrapping to a column (see the one-line rule). Find continuation lines with `rg -nUP '[[:alnum:],]\n *(?![-*+#>|]|[0-9]+[.)])[[:alnum:]]'`. Hits inside fenced blocks and YAML frontmatter are fine, everything else is a wrapped paragraph - join it.
- A blank line after every heading, before the text or list that follows.
- After a mechanical mass edit (rewrapping, renaming, a case change), compare structure counts before and after - headings, bullets, fences, table rows. Identical text is no proof: a bullet list collapsed onto one line keeps every word.
- Cross-reference formatting - is a referenced file always backticked? A referenced section always italicized? Is the connector consistent (e.g. always `` `File.md` -> *Section* ``, not sometimes `(see File.md, Section)`)?
