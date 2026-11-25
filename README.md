# AgentCraft — free debugging skill for AI coding agents

The **systematic-debugging** skill from AgentCraft: makes Claude Code, OpenAI Codex CLI, Google Gemini CLI and 30+ other tools *refuse to touch code until the bug is reproducible*. Full version, free, no email wall.

Most agent debugging is guess-and-patch: wrap the crash in a try/except, delete the failing test, declare victory. This skill encodes the discipline a senior engineer enforces instead:

- **Reproduce** — make the failure happen on demand. Intermittent = state, timing, or environment; hunt the nondeterminism itself.
- **Isolate** — binary search the causal chain, probe values at boundaries, read the whole stack trace instead of the first confirming line.
- **Fix the cause** — ask "what invariant is violated?", not "how do I silence this symptom".
- **Verify** — regression test that failed first, full suite green, same-pattern sweep in sibling code.

Plus the standing rule: **no code changes until Phase 1 and 2 are done.**

## Install

```bash
# Claude Code (personal — applies to every project):
mkdir -p ~/.claude/skills/systematic-debugging
cp skills/systematic-debugging/SKILL.md ~/.claude/skills/systematic-debugging/

# One project (committed, shared with your team):
mkdir -p .claude/skills/systematic-debugging
cp skills/systematic-debugging/SKILL.md .claude/skills/systematic-debugging/
```

`SKILL.md` follows the open [Agent Skills](https://agentskills.io) format — Claude Code picks it up on next session start; Codex CLI, Gemini CLI, ChatGPT, Copilot and ~30 other tools each document their skills directory. Verify by asking your agent: *"which engineering skills do you have?"*

`claude-md-starter.md` is a bonus: rename it to `CLAUDE.md` for repo-level rules that pair with the skill (verification habits, minimal diffs, honesty rules).

## How to judge it

Run one real debugging session. Watch the agent ask "what invariant is violated?" instead of wrapping the crash in a try/except — then decide whether that depth is worth having across the whole workflow.

## The full pack

Seven more skills at the same depth — test-driven-development, code-review, refactoring-safely, legacy-onboarding, security-audit, api-design, write-docs — plus CLAUDE.md/AGENTS.md templates and a one-command installer:

**[AgentCraft on Gumroad](https://keyinxiao.gumroad.com)** — $19, free updates for v1.x, refund if it doesn't work in your tool of choice.

## License

MIT for everything in this repo. The paid pack has its own single-developer license.
