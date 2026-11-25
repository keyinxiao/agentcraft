---
name: systematic-debugging
description: Use when any bug, test failure, stack trace, error message, or unexpected behavior appears. Runs a four-phase protocol — reproduce, isolate, fix root cause, verify — instead of guess-and-patch. Forbids changing code before the failure is understood and reproducible.
---

# Systematic Debugging

You do not guess. Every bug gets the four phases, in order. The most expensive debugging mistake is not being slow — it is "fixing" something you never understood, which produces a symptom patch that breaks again next week in a different costume.

**Standing rule: no code changes until Phase 1 and 2 are complete.** Every edit made before understanding is a bet with your future self as the loser.

## Phase 1 — Reproduce and Read

**Make it fail on demand.** An intermittent bug you cannot reproduce is a bug you cannot fix — you can only make it stop coincidentally. Find the shortest reliable path to the failure: a test, a script, a curl command, a set of steps. If it is intermittent, that is not a dead end, it is a finding: the cause involves state, timing, or environment (concurrency, caching, ordering, date-dependent logic). Hunt the nondeterminism itself — seed randomness, freeze time, pin ordering — until the failure is deterministic if at all possible.

**Read the actual error.** All of it. The stack trace from top to bottom, the full log line, not the first line that confirms your prior. The error message usually states the cause in plain words; the skill is believing it instead of your theory. Check: what is the exact type and message? What line did it come from? What was the *value* in the message — is the empty string actually empty, or `""` vs `null` vs `" "`?

**Form a hypothesis you could be wrong about.** "X should be Y at this point" — not "something is wrong with the config." If your hypothesis cannot fail, it cannot be tested, and it is not a hypothesis.

## Phase 2 — Isolate

**Binary search the causal chain.** You have a path from input to failure. Cut it in half, repeatedly:

- Does the failure happen with the previous commit? (`git bisect` is exactly this, automated.) If yes, the diff is your suspect pool. Read the diff before doing anything else — a recent change explains a new bug far more often than anything exotic.
- Add probes at the midpoint of the pipeline: log or assert the actual value at each boundary. Compare observed against expected. Where observed first diverges from expected is the fault region. Move the probe into that region, halve again.
- Strip the repro: delete inputs, stub dependencies, minimize the data. The minimal reproducing case is usually 5–10 lines and often reveals the cause outright. (Keep it — it becomes your regression test in Phase 4.)

**Distrust your assumptions about the environment.** "It works on my machine" is a data point about *your machine*, not about truth. Check the actual versions, the actual env vars, the actual config file being loaded (print the loaded config, do not assume which file won). Stale build artifacts, a cached process running old code, a different virtualenv — these cause a shocking fraction of "impossible" bugs. When behavior defies the source code, suspect the deployed artifact before the laws of computer science.

**One variable at a time.** Change one thing, observe, revert or keep, then next. Changing three things and getting a different failure teaches you nothing about which change did what.

## Phase 3 — Fix the Cause, Not the Symptom

You now have a reproducible failure and a located cause. Fix the *cause*:

- Symptom: wrap the crashing call in a try/except that logs and continues. Cause: the input can legitimately be absent; handle absence explicitly at the boundary.
- Symptom: add `if (x == null) return`. Cause: understand why x is null when the invariant says it cannot be — the invariant is broken somewhere upstream, and that is the bug.
- Symptom: retry the network call 5 times. Cause: the connection pool is exhausted because a client never closes. Retrying exhausts it faster.

Ask "what invariant is violated?" The bug is the violated invariant. Restore the invariant — do not teach every caller to survive its absence.

Resist the urge to make the failure impossible by deleting the code path's *purpose*. If you cannot explain why the fix works in one sentence to another engineer, you do not have the fix yet.

## Phase 4 — Verify and Lock It Down

1. Run the minimal repro. It must now pass.
2. Convert the repro into a permanent regression test (see test-driven-development). A fix without a regression test is a temporary fix.
3. Run the full suite. The fix must not have broken behavior elsewhere — a fix that trades this bug for a different one is not a fix.
4. Look for the same bug pattern nearby. If the cause was "timezone-naive datetime," grep for the same mistake in sibling code. Bugs travel in litters.
5. Say what you learned in one line. If the cause was surprising, that surprise is a gap in your mental model or your tests — consider closing it.

## When You Are Stuck

Stuck is a phase, not a verdict. In order:

- **Explain the bug out loud**, rubber-duck style, step by step: what happens, what should happen, what you have ruled out. The contradiction usually surfaces mid-sentence.
- **Question the impossible premise.** "This function cannot receive null" — verify, do not assume. List every belief of the form "obviously X" and check the top two.
- **Get fresh eyes on the diff**, not on the theory: show someone (or re-read, hours later) only what changed recently.
- **Walk away for 20 minutes.** Deliberately. The pattern-match that finds the bug runs in the background, and staring harder at the same screen runs the same failed loop again.

## Anti-Patterns That Void This Skill

- Changing multiple things while hoping something works ("shotgun debugging"). Every change without understanding is debt.
- Deleting the failing test to make the suite green.
- "Fixing" by adding broader exception handling that swallows the failure.
- Restarting the service and declaring victory when the symptom disappears.
- Reading only the first stack frame. The interesting frame is usually yours, two lines below the library frames.
- Declaring the cause without the repro: "probably a race condition" with no way to trigger it is a confession, not a diagnosis.

## Hard Rules

1. No fix before a reproducible failure and a stated cause.
2. One change at a time between observations.
3. Every fix lands with a regression test that failed first.
4. Intermittent = state, timing, or environment. Treat nondeterminism as the primary clue, not as noise.
5. When the code defies the source, suspect the artifact (stale build, old process, wrong config) before suspecting physics.
