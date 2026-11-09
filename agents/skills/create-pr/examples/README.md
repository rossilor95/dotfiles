# Examples

Canonical, review-accepted PR descriptions and workflow transcripts for the `create-pr` skill. Each file shows one idea in action.

| File | What it illustrates |
|------|--------------------|
| [good-pr-description-bugfix.md](good-pr-description-bugfix.md) | User-facing bug fix framed as numbered failure modes with reproductions. The "recommended template" in SKILL.md Core Rule 2. |
| [diff-narration-antipatterns.md](diff-narration-antipatterns.md) | Sentence-level rewrites: diff narration (❌) vs. symptom/root-cause framing (✅). Expands the four inline examples in SKILL.md Core Rule 2. |
| [issue-search-workflow.md](issue-search-workflow.md) | Worked transcript of the step 4.55 issue lookup: conversation → branch → `gh issue list` → confirm with user. Shows the "verify before citing" gate. |

All three are derived from the same real session (the uutils/coreutils#11996 fix). Reading them in order — workflow → anti-patterns → final PR body — retraces the decisions the skill is meant to reproduce.
