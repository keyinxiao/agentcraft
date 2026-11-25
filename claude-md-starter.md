<!--
Template file — do not keep this filename.
Buyer: copy this file to CLAUDE.md (repo root for per-project, or ~/.claude/CLAUDE.md for global).
-->

# CLAUDE.md — Global Engineering Rules

# Verification habits

- Run the relevant tests after every change. Never report success without a command that proves it: a green test run, a passing build, or the exact command output.
- A claim you have not verified is labeled as unverified — say "not verified" instead of implying it works.
- Before implementing, check what already exists. Duplicated utilities and second implementations of the same logic are defects.
- Commands whose effect reaches outside this repo (deploy, publish, delete, external API writes) require my explicit go-ahead, every time.

# Change discipline

- Smallest change that solves the stated problem. No drive-by refactors, no reformatting untouched lines, no "modernization" of working code.
- Match the file's existing style even if you prefer another — style consistency is a feature. Only introduce a new pattern when the file is new or the old pattern is actively broken.
- Every bug fix ships with the regression test that failed before the fix. Every new behavior ships with a test that would fail without it.
- Leave the campsite cleaner only when it is on your path: fix what you touch, log what you notice as a finding, never expand the diff.

# Honesty rules

- When blocked or uncertain, stop and report the blocker with what you tried. Do not fabricate outputs, invent file contents, or simulate command results. A wrong report is worse than a missing one.
- If a task is ambiguous at the level that changes the design, ask one precise question. If the ambiguity only affects a detail, pick the boring default, note the assumption, and continue.
- Distinguish fact from inference in summaries: "the tests pass" (fact) vs "this should now handle retries" (inference — go verify).

# Context discipline

- Read the relevant code before writing the relevant code. Understand the function you are modifying and its callers, not just the snippet.
- Keep working state in the repo (code, tests, docs), not in chat history. The next session must be able to continue from the repository alone.
- Prefer the boring, battle-tested approach over the clever one. Cleverness must pay rent: name the constraint that demands it.
