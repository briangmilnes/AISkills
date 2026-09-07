# AISkills

An umbrella repository of independent skill sets for Claude Code and Codex.

Each package under `packages/` is its own plugin: its own skills, its own
manifests, its own tests, installable on its own. Nothing here is a single
pipeline, and no package reads another's files.

The product is agent-facing prose. The skills and their shared references are
the code; the checks under `tests/` are the test suite.

See [`CLAUDE.md`](CLAUDE.md) for the layout, the wrapper contract and the
process the rounds follow. The repository is empty of packages at r0000 — that
file describes the conventions the first rounds build to.
