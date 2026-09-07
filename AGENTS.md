# AISkills — Codex instructions

## Load the project rules

Before working in this repository, read these files in full using file tools.
Treat them as project instructions, subject to the precedence below:

1. [`CLAUDE.md`](CLAUDE.md), relative to this repository root.
2. `~/projects/ComputAItionalThinking/ComputAItionalThinkingRules.md`.
3. `~/projects/ComputAItionalThinking/ExperAImentalThinking.md`.
4. `~/projects/GRASE/GRASERules.md`.

Expand `~` to the user's home directory. Read each source once per session;
read it again if it changes. The list explicitly requests file reads; do not
treat seeing Claude's `@` references as having read their contents. If a file
is unavailable, identify its path and which work depends on it. Continue work
that does not require the missing instructions.

`CLAUDE.md` owns this project's purpose, layout, timezone, measurements,
wrappers, package conventions, experiments, and GRASE process. Maintain those
facts there. KRustSemantics is a reference project; its Rust/K commands and
coverage obligations are not AISkills instructions.

## Resolve overlapping instructions

System and developer instructions and tool permissions remain controlling.
Within project guidance, apply the user's current request first, then this
file's explicit adaptations, then AISkills' `CLAUDE.md`, then the shared
rulesets. Skills supply task-specific guidance within those constraints.
Discussion requests, including `DISCUSS`, remain discussion requests.

The following adaptations are deliberate:

1. ComputAItionalThinking's “Think Before Coding” requires surfacing uncertainty.
   For routine implementation choices, state the assumption and proceed. Ask
   when the answer materially changes correctness, scope, or authorization.
2. Its precise computer-science vocabulary remains the writing standard.
   Plain language does not require replacing established technical terms.
   Preserve the distinction between testing and formal verification.
3. AISkills' measurement tables and indexed rows remain appropriate for
   comparisons. A preference for prose does not override their purpose.
4. Required package checks, eval cases, wrapper logging, and experiment
   records remain required. Calibrating test effort does not waive them.
5. Subagent work follows AISkills' worktree ownership and capacity rules.
   Delegation also depends on the active harness instructions and available
   tools; a model prompting example does not establish permission or capacity.

## Astra working defaults

Complete authorized work through its acceptance criteria. Prepare reviewable
changes before seeking any remaining approval. Keep working on independent
parts while a necessary answer is pending.

When a file causes a pause, cite its path and exact rule, explaining whether
the restriction is explicit or inferred. Do not manufacture approval steps.

Write concise paragraphs with concrete verbs. Remove stock introductions,
decorative jargon, invented compounds, and repeated conclusions. Avoid
“delve,” “foster,” “leverage,” “genuinely,” and “it's worth noting” as filler.
Preserve literal quotations and technical identifiers when needed.

Select checks that establish the changed behavior. After required checks
pass, repeat or broaden them only for a changed input, failure, or unresolved
concern. Report actual outcomes and any checks not run.

## Scope and sources

This file configures instructions. It does not install skills, select a model,
configure permissions, or implement Claude's GRASE hooks in Codex. Check the
actual repository and harness before claiming that a wrapper or hook runs.

The adaptations above were reviewed against OpenAI's
[Astra model guide](https://developers.openai.com/api/docs/guides/latest-model#prompting-best-practices)
on 2026-09-06. The guide URL tracks current model guidance; confirm that its
selected model is GPT-6 Astra when consulting it later.

Codex instruction discovery is documented in
[Custom instructions with AGENTS.md](https://learn.chatgpt.com/docs/agent-configuration/agents-md).
Start a fresh Codex session in this repository to test discovery and ask it
to list the instruction sources it actually read. File review alone does not
establish that a fresh session followed every rule.
