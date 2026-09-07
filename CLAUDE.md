# AISkills

project-tz: America/Los_Angeles

An umbrella repository of **several independent skill sets** for Claude Code and
Codex. Not one pipeline: each package under `packages/` is its own plugin, with
its own skills, its own manifests and its own tests, installable on its own and
answering for its own quality.

The product of this repository is agent-facing prose. The skills and their
shared references are the code; the checks under `tests/` are its test suite.
A skill that is not measured against a suite is a claim, not a capability.

This file is the contributor guide for agents. Nothing below describes a tree
that exists yet — the repository is empty at r0000, and the layout and command
tables are the conventions the first rounds build to.

## The user's review time is the single most important thing to optimize

Everything else in this file is subordinate to that. The user reads far more
than they write, and a document that wastes a minute of their attention costs
more than the work it describes.

| # | Rule | Consequence |
| - | ---- | ----------- |
| 1 | One fact, one home. If two documents say it, one links instead. | A reader who meets the same table twice cannot tell which is current. |
| 2 | Never hand-copy a generated number. Point at the generated file, or splice it in through a marker region. | A copied number is wrong at the next commit and the reader cannot tell. |
| 3 | A status document carries the current state. Solved defects belong in `reports/`, not in status. | History in a status page is noise the reader must filter every time. |
| 4 | Lead with the measurement. A table of numbers beats a paragraph about numbers. | The user is checking a claim, not reading an essay. |
| 5 | Summary and detail are separate documents, linked. A package page says what the state is; its evals page says which cases. | The reader chooses the depth; neither page has to be skimmed to reach the other. |
| 6 | Say plainly what was **not** measured. | An unmarked gap is read as a measurement, and the user finds out later. |
| 7 | Answer the question asked, first, in the first paragraph. | The rest can follow; the answer should not have to be hunted for. |

When a change would make the work faster but the review slower, it is the wrong
change.

The same rules bind the skills themselves, because a skill is a document an
agent reads under time pressure. Progressive disclosure over completeness; a
pointer over a copy; an edit that only adds tokens "for clarity" is bloat.

## The docs are updated and opened after every batch of agents

Not on request. When a set of rounds has been merged and verified, the
orchestrator updates the documentation and opens it, before doing anything
else. The user reads the rendered docs, not the terminal and not the reports;
docs that lag the merges make a review pass useless.

| # | Step | Note |
| - | ---- | ---- |
| 1 | Regenerate what is generated | Whatever `scripts/analyze.sh` covers once it exists |
| 2 | Hand-update the pages carrying state | `docs/Status.md`, `docs/Packages.md`, `docs/Defects.md`, and each package's own page |
| 3 | Commit, then open the docs | A new window in the **running** Chrome |

**Opening the docs is the orchestrator's alone.** It opens a window in the
user's running Chrome, so an agent that runs it interrupts whatever the user is
reading — and an agent has no reason to: it cannot see the result. A round that
wants to prove the wrapper works checks its exit status and its log, and does
not open anything.

Opening deliberately reuses the running Chrome rather than starting an instance:
a fresh `--user-data-dir` profile has none of the user's extensions, and the
markdown renderer is one of them, so a separate instance shows raw text.

**Take every number from a committed log; never re-run to get one** (GRASE rule
5.2). An agent's log is a claim about `main` once its branch is merged with a
clean `--no-ff`, because the tree is then identical.

## Name a measurement by its package and its suite

"The skill passes" is not a measurement. Three things have to be named before a
figure means anything, and a figure missing any of them is not a result:

| # | Name | Means |
| - | ---- | ----- |
| 1 | the **package** | which skill set was exercised |
| 2 | the **suite** | which cases, at which commit — a suite that changed between two figures makes them incomparable |
| 3 | the **model** | which Claude or Codex model ran it, by exact id |

"41 of 60" is meaningless without all three. State the harness's own failures
separately from the skill's: a case that never reached the skill is not a
skill failure, and folding the two hides a broken harness behind a quality
number.

Say plainly which direction a failure runs. A skill that **declines work it
should do** and a skill that **does work it should have declined** are two
different defects with two different fixes; naming both "a failure" loses the
distinction the fix depends on.

## Every subagent task runs in its own git worktree branch

Never in the main checkout. GRASE rule 2.1 already says this; it is restated
here where the orchestrator will see it before dispatching, because it is the
rule that gets broken first.

| # | Rule | Reason |
| - | ---- | ------ |
| 1 | One worktree per agent: `~/projects/AISkills-agent-N` on branch `agent-N`, created with `git worktree add`. | Agents make mistakes. A mistake on a branch is a merge conflict; the same mistake in `main` is a broken tree the user is reading. |
| 2 | The agent commits on its own branch and never on `main`. The orchestrator reviews, verifies and merges. | The merge is where a claim gets checked. An agent that pushes to `main` skips the check. |
| 3 | Two agents never own the same files. State the ownership split in the plan. | Two rounds editing one package's manifests produce a conflict in the one file that must stay consistent. |
| 4 | Where an overlap is unavoidable — a shared reference, or a repo-wide check in `tests/` — keep the edits minimal and require the agent to list them in its report. | A listed edit is a cheap merge; a discovered one is not. |
| 5 | The orchestrator works in `main` and dispatches. It does not also run an agent there. | Otherwise the reviewer and the reviewed are the same working tree. |

Worktree role detection is by path, so an agent working in `main` also writes
its logs into the `orchestrator` slot and the GRASE record silently
misattributes them.

## A fixed pool of agent worktrees, and rounds as the name

### The agent pool is fixed, not per round

`~/projects/AISkills-agent-N` for **N = 1..10** and no further — note the
hyphen. A round is assigned a free slot; when its work merges, the slot's
branch is reset to `main` and the slot is free again. Creating a new worktree
per round is how a neighbouring project reached 22 worktrees and 3.9 GB on a
disk that was 98% full.

| # | Rule |
| - | ---- |
| 1 | Ten slots, `agent-1` through `agent-10`. Never an eleventh. Concurrency is capped lower than ten; the plan states the cap it is running under. |
| 2 | A slot is free when its branch is merged into `main` and its worktree has no uncommitted files. |
| 3 | Free a slot with `git branch -f agent-N main` in the worktree, not by removing it — the checkout is the expensive part to recreate, the branch ref is free. |
| 4 | Remove a worktree only when the pool is being shrunk. GRASE rule 2.4 leaves worktree lifetime to the user, so say what you are removing and why. |
| 5 | Before dispatching, check the pool. If no slot is free, the round waits — that is the queue working, not a problem to route around. |

### A round is the name; the slot is an address

Plans are `plans/rNNNN-plan-from-…`, and **the round id is how the work is
referred to** — in reports, in commits, in conversation. `agent-N` is only the
worktree the round happened to run in, the way a build directory is not the
name of the build.

| # | Say | Not |
| - | --- | --- |
| 1 | "r0050 landed the review skill set" | "agent-3 landed the review skill set" |
| 2 | "r0040 is blocking r0050" | "agent-2 is blocking agent-3" |
| 3 | "r0042's design" | "agent-4's design" |

A report is `reports/rNNNN-report-from-agent-N-to-orchestrator-…` because GRASE
rule 7.1 fixes that filename, and the `from-agent-N` slot records which worktree
ran it. That is the one place the slot number belongs.

## Layout

Each package is self-contained: it can be installed without the rest of the
repository, and a change to one package does not touch another. Each directory
gets a `README.md`, and that README is where its contents are described; this
table says only what the directory is for.

| # | Directory | What it is for |
| - | --------- | -------------- |
| 1 | `packages/<name>/` | **One skill set.** Holds its own `skills/<skill>/SKILL.md`, its `.claude-plugin/` and `.codex-plugin/` manifests, its `README.md` and its `tests/`. Independently installable, independently versioned. |
| 2 | `shared/` | References more than one package loads. Nothing here is a skill and nothing here is a copy: a package points at it. |
| 3 | `evals/` | The suites the packages are measured against, one directory per suite, each pinned and dated. A suite is data, never edited to make a figure move. |
| 4 | `tests/` | Repo-wide checks that run across every package — frontmatter, manifest sync, links, prose width, one-home-per-fact. Package-local checks live with the package. |
| 5 | `scripts/` | The GRASE wrappers. The **only** way to run anything (see below). |

Everything else is record.

| # | Directory | Contents |
| - | --------- | -------- |
| 6 | `docs/` | What is true now — state and measurements, not history. `docs/README.md` is the index and says which page owns which fact. |
| 7 | `standards/` | Long-form project standards, one per subject. Read the standard before working on its subject; a standard with no check in `scripts/analyze.sh` is aspirational and says so. |
| 8 | `experiments/` | One question, one answer, written once and not edited after (see below). |
| 9 | `prompts/` `plans/` `reports/` `logs/` `analyses/` | GRASE process artifacts. |

## Building and running

Every task goes through a wrapper in `scripts/`, and every wrapper invocation
writes exactly one log to `logs/<program>.YYYY-MMDD-HH:MM.<role>.log` with ANSI
escapes stripped and a `# rNNNN` round tag when run under a plan (GRASE rules
4.1–4.5). **This is rule 5.1 and it is not optional: never invoke a checker, a
linter or an eval runner directly.** An unlogged run is a result nobody can
reproduce, and a skills repository's results are the only thing it ships.

No wrapper exists yet. The round that adds one adds it to this table in the
same commit, or the table is already wrong.

| # | Wrapper | Effect |
| - | ------- | ------ |
| 1 | `scripts/test.sh [-r rNNNN] [package ...]` | Every structural check, repo-wide and package-local. One `SUMMARY <package> <pass> <fail> <error> <total>` per package. |
| 2 | `scripts/rtt.sh` | Alias of `test.sh` (GRASE rule 5.4). |
| 3 | `scripts/lint.sh [-r rNNNN] [path ...]` | Markdown lint with the repo config. |
| 4 | `scripts/eval.sh [-r rNNNN] [--model ID] <suite> [package ...]` | Run a package against an eval suite. The model id is recorded in the log, never defaulted silently — a figure whose model is unrecorded is not a measurement. |
| 5 | `scripts/analyze.sh [-r rNNNN] [all\|…]` | Regenerate `analyses/` and the generated docs from `logs/` and the package tree. Subcommands that enforce a standard exit non-zero on a defect and are run in `main` before and after each merge. |
| 6 | `scripts/opendocs.sh [-r rNNNN] [doc ...]` | Open the documentation in the **running** Chrome. Orchestrator only. |
| 7 | `scripts/newslot.sh agent-N` | Bring a pool worktree to a state where the harness runs. |

Need an option the wrappers lack? Negotiate a new wrapper (rule 5.7); do not
reach around them.

## Writing a skill set

| # | Rule | Why |
| - | ---- | --- |
| 1 | A package is self-contained. It never reads another package's files; anything two packages need moves to `shared/` and both point at it. | Independent installation is the whole premise. A cross-package read makes one package silently depend on a repo it will not ship with. |
| 2 | One `SKILL.md` per skill, and its frontmatter `description` is what the router matches on — write it for selection, not for summary. | An unselected skill is dead weight no matter how good its body is. |
| 3 | Progressive disclosure: `SKILL.md` carries what every invocation needs; everything else is a reference the skill loads when it needs it. | Every token in `SKILL.md` is paid on every invocation. |
| 4 | One home per fact, inside a package as much as across the repo. A reference is cited, never pasted. | Two copies drift, and the reader cannot tell which one the skill actually followed. |
| 5 | Manifests stay in sync. A package's `.claude-plugin/plugin.json`, its marketplace entry and its `.codex-plugin/plugin.json` share one version; bump them together. | A version skew installs one thing and documents another. |
| 6 | A new skill ships with at least one eval case, and prose changes ship separately from behaviour changes. | Prose is not evidence of behaviour, and a mixed commit cannot be bisected. |
| 7 | A skill that must decline says so explicitly and says what it will do instead. Silence reads as capability. | A skill that quietly does the wrong thing is worse than one that stops. |
| 8 | No benchmark leakage: skill and README text never names an eval suite's cases or files. | A skill written against the suite measures the suite, not the skill. |

## Experiments

`@~/projects/ComputAItionalThinking/ExperAImentalThinking.md` is imported below
and rule 1 requires this project to say where its experiments live.

| # | Kind | Location |
| - | ---- | -------- |
| 1 | A question about what the **harness** can or cannot do — a frontmatter field, a router behaviour, a plugin manifest shape, a tool contract | `experiments/`, one directory per question |
| 2 | A question about how a **model** responds to a piece of prose | an `evals/` case with its expected outcome |

An experiment asks **one** question and is run through a wrapper so the run is
logged like any other. It carries `RESULT: SUCCEEDS | FAILS | TESTING |
PARTIAL`, the evidence, a `DATE:`, and the exact model id and tool version it
was run against (rules 2, 11) — a harness answer without a version is a fact
with an unknown expiry.

**An experiment is never run in `/tmp` or a scratchpad.** It is written into
`experiments/` first, so it is on disk and its run is logged before the answer
is known. A probe run somewhere temporary produces an answer with no record,
and the next round has only a sentence claiming it — which is the thing rule 6
exists to stop.

**Rule 6: no claimed limitation without an experiment.** "The router cannot do
X" is a claim until an experiment says so, and claims about agent harnesses go
stale faster than claims about compilers. A failed experiment stays on disk
marked `FAILS` — it is the finding (rule 5).

## Reference projects

Read before duplicating work. Never edit these from this project.

| # | Project | What it gives us |
| - | ------- | ---------------- |
| 1 | `~/projects/kit` | Eight K-verification skills as one Claude Code / Codex plugin, with an orchestrated construction-and-audit pipeline and a structural test suite in `tests/check-structure.py`. The closest working model for a package here: read its `CLAUDE.md` for the conventions its tests enforce. |
| 2 | `~/projects/KRustSemantics` | The GRASE process at scale — the worktree pool, the round naming, the docs discipline, and the standards-with-checks pattern. This file is adapted from its `CLAUDE.md`. |
| 3 | `~/projects/ComputAItionalThinking` | The agent language ruleset imported below: concrete computer-science terms, no analogies or metaphors. |
| 4 | `~/projects/GRASE` | The process ruleset imported below. |
| 5 | `~/projects/veracity` | Analysis binaries, including `veracity-review-string-hacking`, which fails any analysis code that pattern-matches source text instead of walking a tree. |

## Tools

**No string hacking.** Any program that inspects a skill parses it: YAML
frontmatter through a YAML parser, markdown body through a markdown parser,
manifests through a JSON parser. Regular expressions over a document's text are
a defect class, not a shortcut — a heading inside a fenced code block is the
first thing they get wrong, and a check that is wrong is worse than no check,
because the merge that trusts it is the one that breaks `main`.

The same holds for anything that inspects agent transcripts or logs: read the
structured form the tool emits, not the rendered text.

## Process

This project follows the GRASE process (Git Recording Agentic Software
Engineering) at level **GRASE.0** — time capture. The rulesets are imported
below and are the agent's default mode (rule 1.8): rounds are numbered, plans
and reports are paired, every tool run is logged.

@~/projects/ComputAItionalThinking/ComputAItionalThinkingRules.md
@~/projects/ComputAItionalThinking/ExperAImentalThinking.md
@~/projects/GRASE/GRASERules.md
