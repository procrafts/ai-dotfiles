---
name: mine-instructions
description: >
  Mine an external source - another project's AGENTS.md, a skill, a config, any rules text -
  for rules worth adopting into your own instruction files.
disable-model-invocation: true
---

# Mine instructions

Mining an external source means reading its rules as candidates for your own instruction files (`./CLAUDE.md` / `./.claude/rules/*`) and deciding, rule by rule, which are worth adopting. The source can be a skill, another project's `AGENTS.md`, a config, or any prompt/rules text with encoded working rules. Most won't be worth taking - the value is the disciplined filter, not bulk copying.

## Process

1. **Get the raw source.** Find the source's canonical text - a `SKILL.md`, an `AGENTS.md`, a rules/prompt file in its repo, not a write-up *about* it - and fetch it raw (`curl raw.githubusercontent.com/...` or equivalent), never an AI-summarized rendering. Summaries silently restructure content (see Pitfalls) and can't be trusted for exact wording or counts.
2. **Separate the portable rules.** Note the source's scope, and split the portable rule/persona text from what's bound to the source itself - its tooling (slash commands, hooks, installers, telemetry) and its own stack, paths, and domain specifics - which doesn't port.
3. **Enumerate the rules** as atomic, independently comparable statements.
4. **Cross-check each rule against every loaded instruction file** - `./CLAUDE.md`/`./AGENTS.md` and all `./.claude/rules/*` - not just the most obviously related one.
5. **Classify each rule into exactly one bucket:**
   - **Gap** - no semantic equivalent exists in your instructions.
   - **Duplicate, comparable** - an equivalent exists and is roughly as effective.
   - **Duplicate, weaker current wording** - an equivalent exists, but the external phrasing is concretely sharper (state *why*: a good/bad example pair, or it names the mechanism instead of just the outcome).
   - **Not applicable** - bound to the source's own tooling, stack, or domain, not portable.
   - **Conflict** - the external rule doesn't just differ *in wording*, it contradicts an explicit rule of yours. Don't present these as candidates - name the incompatibility and treat your existing rule as a deliberate decision that stands, unless you say it was wrong.
6. **For every Gap and weaker-wording item, present** the source's verbatim wording (quote, not paraphrase) alongside your current wording, if any, then reformulation proposals in your own voice/format - not the original copied verbatim, technology-neutral unless the specific technology is the whole point. Surface open placement decisions (new file vs. new section vs. extend an existing row) rather than assuming one.
7. **Weigh the slot, not just the wording.** A rule in a consult-on-suspicion list is structurally weaker than one in an always-scanned table - prefer the stronger existing slot over a new section. These files load every session, so extending a proven slot (one more row) beats adding a new heading or worked example, even when the source uses one. State the added cost per candidate (e.g. "+1 row ~ 25 tokens") so it can be weighed.
8. **Apply only the rules you selected, then re-read the diff before saving** - a previously-approved wording can still hide an issue caught only on the final pass.

## Pitfalls

- An AI-summary of a fetched source file silently restructures content - reordering, collapsing, or miscounting levels, rows, and lists. Pull the raw source whenever exact wording or counts matter. Never cite a summary as a direct quote.
- A source's standout example (a punchy good/bad pair) is often a generic teaching device, not about the rule you're comparing. Don't force a "more effective" finding onto one of your rules just because the source has a vivid example elsewhere - check that the example actually illustrates *that* rule.
- Separate rules that belong in your instructions from rules the agent already follows by default (its built-in tone/style defaults). Duplicating a default adds noise without changing behavior - worth it only for a stricter or domain-specific version.
