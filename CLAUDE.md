# CLAUDE

- Reply in English.
- Minimize `build` and `compile`; only run the smallest useful verification.
- Match the appropriate `sdd-*` skill first; do not bypass skills for large changes.
- `sdd-review` follows [addyosmani/agent-skills **code-review-and-quality**](https://github.com/addyosmani/agent-skills/blob/main/skills/code-review-and-quality/SKILL.md) (five steps, prefix severities) — `skills/sdd-review/SKILL.md`. Pass `agents/code-reviewer.md` + SKILL + context when dispatching a reviewer.
- `sdd-simplify` reduces complexity inside the agreed simplify scope only and does not change behavior; it does not require feature/plan docs or a prior review artifact.
- Write Git commit messages in English, including the problem/requirement or the fix idea.
- This repository is an SDD skill repository; do not force an application-style structure onto it.
