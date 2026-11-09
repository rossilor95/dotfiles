---
name: create-pr
description: Use when the user asks to "create a PR", "open a pull request", "make a PR", "submit a PR", or any variation of creating/opening a pull request. Focuses on extracting the INTENT behind changes and creating meaningful PR descriptions.
metadata: {version: "0.0.5", author: "rossilor95", tags: ["github", "pull-request", "pr", "gh-cli", "git", "workflow", "pr-template", "description", "jira", "issue-linking"]}
---

# Create PR

## Pull Request Creation

### Dependency Bump PRs

For PRs that only update dependency versions, use this description rule instead of the general intent-writing rules below.

- State the dependency name, current version, and target version.
- Include the official changelog for every release between those versions, with source links.
- Verify versions against the diff. If no official changelog exists, state that fact.
- Keep required repository template sections and the related issue link.
- Limit the explanation to versions and the changelog. Omit motivation, implementation plans, and validation summaries unless explicitly requested.
- A dependency bump needs no separate intent question. Continue with the existing authorization and issue context.

### Core Rules

1. **NEVER fabricate intent.** Most users say "do X" without explaining why. When intent is missing (which is usually the case), ASK before creating the PR.

2. **NEVER narrate code changes.** Don't explain what the code does in human language. The diff shows the implementation.

   **Red flags — if any apply, you're narrating the diff, not explaining intent:**
   - The sentence names the **before** and **after** API / method / call chain ("replaced `.foo()` with `.bar()`").
   - The sentence could be rewritten as *"I changed X to Y"* without losing any meaning.
   - The sentence would be accurate and useful even if the reader hadn't read the issue or had any context about why.
   - You're describing a function's new signature, return type, or control flow.
   - You're walking through what a method chain does step by step.

   **What to write instead:** the user-visible symptom before the fix, the user-visible behavior after, and (if non-obvious) the *root cause category* — not the mechanism. The root cause belongs in the description only when it's subtle enough that a reviewer wouldn't infer it from the diff (e.g., "`Result` implements `IntoIterator` so `Err` became zero items"). Everything else is noise.

   **Recommended template for user-facing bug fixes** — frame the body around **failure modes** rather than implementation. Each mode gets: (a) the exact command that reproduces it, (b) the actual error/output the user saw, (c) what GNU/the spec does for comparison (if applicable). Then one line on the new behavior. This is richer than "intent + approach" and uses fewer words than diff narration. See [examples/good-pr-description-bugfix.md](examples/good-pr-description-bugfix.md) for a canonical merged example ([uutils/coreutils#11996](https://github.com/uutils/coreutils/pull/11996)) with annotations on why each part is there.

   **Examples:**
   - ❌ "Replaced `.expect()` with `?` and changed the return type to `UResult<Vec<String>>`." → diff narration.
   - ❌ "Switched `.flat_map(file_as_vec)` to `.map(file_as_vec).collect::<UResult<_>>()?.into_iter().flatten()`." → diff narration.
   - ✅ "`du -X nonexistent-file` panicked instead of printing a clean error." → symptom.
   - ✅ "`flat_map` over a `Result`-returning function silently drops `Err` (zero items), so I/O errors would have been hidden even after removing the panic." → non-obvious root cause worth documenting.

3. **Be thorough but concise.** Cover the intent, the approach, and any context a reviewer genuinely needs — but every sentence must earn its place. No padding, no restating the diff, no hedging.

4. **The target repo's PR template is the source of truth for structure.** If a template exists (see workflow step 4.6), fill every section it defines, in the order it defines them. Do not invent, reorder, or drop sections.

5. **If no PR template is configured, do NOT scaffold `# What`, `# Why`, or `# How` sections.** Write a plain concise description derived from the user's intent. Imposed headers on an untemplated repo are noise.

6. **Default anti-patterns (apply only when there is NO template):**
   - No "Files Updated" / "Files Changed" sections — GitHub already shows this.
   - No "Test plan" section — testing is implicit in QA processes.
   - No "Risks" section unless the user explicitly mentioned specific concerns.

   When a template is present and asks for any of these, **fill them** — the template wins.

7. **Always look for a related issue (GitHub or Jira) and link it if found.** PRs without an issue link are harder to triage, orphan release notes, and leave `git blame` readers with no "why." Before writing the description, search for candidate issues (see workflow step 4.55), surface matches to the user, and include the reference only after they confirm. **Never invent or guess an issue number** — a fabricated `Fixes #NNN` auto-closes an unrelated ticket on merge.

### Workflow

#### 1. Look for intent in session history

Did the user explicitly state:
- What problem they're solving?
- Why they need this change?

**"Do X" is not intent.** "Add button to page" describes WHAT, not WHY.

#### 2. Ask for intent (usually needed)

Most sessions won't have intent. Ask:

```
Before creating this PR, I need to understand the intent behind this change.

What problem does this solve, and why is this change needed?
```

#### 3. Commit and push if needed

If changes aren't committed and pushed, do that first.

#### 4. Determine the base branch

The base branch is the branch this PR should merge INTO.

1. Get the repo's default branch: `gh repo view --json defaultBranchRef -q '.defaultBranchRef.name'`
2. Check the current branch's upstream: `git rev-parse --abbrev-ref @{upstream} 2>/dev/null`
3. If the upstream tracking branch differs from the default branch (e.g., tracks `full` instead of `main`), use the upstream's remote branch as the base.
4. If uncertain, ask the user which branch to target.
5. Always pass `--base <branch>` to `gh pr create`.

#### 4.5. Validate diff matches intent

Before writing the PR description, review the actual diff to ensure it matches the user's intent:

1. Run `git diff <base-branch>...HEAD --stat` to see all files changed
2. Compare the changed files against what the user discussed in this session
3. If there are **unexpected files** — files changed that weren't part of the conversation — STOP and warn the user:

```
I notice the diff includes changes to files we didn't discuss:
- <unexpected file 1>
- <unexpected file 2>

These may be leftover changes from a previous session. Should I:
1. Proceed with all changes in one PR
2. Help you split these into separate commits/PRs
3. Exclude them (you'll need to stash or reset those files)
```

4. Only proceed to step 5 once the user has confirmed the diff is intentional
5. When writing the PR description, base the approach/implementation section on the **actual diff**, not just the conversation context

#### 4.55. Find related issues (GitHub/Jira)

Every PR should reference the issue it addresses. Search *before* writing the description so the references can be woven into the prose, not tacked on as an afterthought.

**Where to look, in order:**

1. **Conversation history** — did the user paste an issue URL, ticket ID (e.g. `ABC-1234`), or issue number (`#11924`)? That's the authoritative answer; use it and skip searching.

2. **Branch name** — many teams encode the ticket ID in the branch (`fix/ABC-1234-null-check`, `feat/11924-exclude-from`). Extract with:
   ```bash
   git rev-parse --abbrev-ref HEAD
   ```

3. **Commit messages** — look for `Closes #NNN`, `Fixes ABC-1234`, or bare issue IDs:
   ```bash
   git log <base-branch>..HEAD --format=%B
   ```

4. **GitHub issue search** — derive keywords from the diff (changed function names, user-visible symptom) and search open+closed issues:
   ```bash
   gh issue list --state all --search "<keywords>" --limit 10 \
     --json number,title,state,url
   ```
   Prefer multiple short targeted searches over one broad one. If the first search returns nothing, try different keywords (function name, error message text, affected CLI flag) before giving up.

5. **Jira** — if the repo or its agent instructions (`AGENTS.md`, `CLAUDE.md`, or similar) reference a Jira project key (e.g. `PROJ-`, `ABC-`), and a Jira tool, skill, or MCP server is available, search there. Otherwise ask the user directly.

**What to do with the result:**

- **1 clear match** — surface it to the user with a one-line confirmation: *"I found issue #11924 ('du -X with a nonexistent file panics') — it matches the panic trace in this diff (line 759:37). Reference it in the PR? (y/n)"*. Wait for confirmation.
- **Multiple candidates** — list them and ask the user to pick, or confirm "none of these."
- **No matches after reasonable search** — ask the user: *"I couldn't find a related issue. Is there a GitHub issue, Jira ticket, or tracking doc I should reference?"* If they confirm there is none, proceed without one — but note this is the exception, not the default.

**Once confirmed, the issue reference goes:**
- **At the top of the PR body** as `Fixes #NNN` / `Closes #NNN` (GitHub — auto-closes on merge) or `ABC-1234` (Jira — for linking via Smart Commits/integrations).
- **In the commit message body** (`Closes #NNN` trailer) so `git blame` readers can find the issue from the commit alone.

**Verifying the match before citing it:** if the issue has a panic trace, error message, or file:line reference, confirm the diff actually touches that location. A fabricated or mis-matched `Fixes #NNN` auto-closes the wrong issue on merge — a worse failure mode than omitting it.

#### 4.6. Detect the repo's PR template

GitHub resolves templates from several locations. Check all of them before writing the body:

**Single-template locations** (first match wins, in this order):

```
.github/PULL_REQUEST_TEMPLATE.md
.github/pull_request_template.md
docs/PULL_REQUEST_TEMPLATE.md
docs/pull_request_template.md
PULL_REQUEST_TEMPLATE.md
pull_request_template.md
```

**Multi-template directory:** `.github/PULL_REQUEST_TEMPLATE/*.md`

Concrete lookup:

```bash
# Single-template check
for f in .github/PULL_REQUEST_TEMPLATE.md .github/pull_request_template.md \
         docs/PULL_REQUEST_TEMPLATE.md docs/pull_request_template.md \
         PULL_REQUEST_TEMPLATE.md pull_request_template.md; do
  [ -f "$f" ] && echo "FOUND: $f" && break
done

# Multi-template check
ls .github/PULL_REQUEST_TEMPLATE/*.md 2>/dev/null
```

**Branch on the result:**

1. **Single template found** — Read it. Use its sections/headers verbatim as the PR body skeleton. Fill every section from the user's intent + validated diff, in the order the template defines. Do not add `Why?`/`How?` if the template doesn't. Do not drop sections you think are redundant.

2. **Multi-template directory found** — Auto-select based on the current branch name:
   - `fix/*`, `bugfix/*`, `hotfix/*` → `bugfix.md` (or closest match: `fix.md`, `hotfix.md`)
   - `feat/*`, `feature/*` → `feature.md` (or `feat.md`)
   - `chore/*` → `chore.md`
   - `docs/*` → `docs.md` (or `documentation.md`)
   - `refactor/*` → `refactor.md`

   Do a case-insensitive filename match. If no template matches the branch prefix, fall back to asking the user which template to use. Never guess silently.

3. **No template found** — Skip the `### Why?` / `### How?` scaffold entirely. Write a plain concise description (1-3 short paragraphs) covering intent and approach. Still include the `<details>` Implementation Plan block if a plan file exists (see "Finding the plan file" below).

**Core Rules interaction:** When a template is present, its structure wins over the default anti-patterns in Core Rule 6 — if the template has `## Files Changed` or `## Test Plan`, fill them. Core Rules 1-5 (no fabricated intent, no code narration, thoroughness/conciseness) still apply.

#### 5. Create or update PR

Use `gh` CLI for all PR operations:

**Check if PR exists:** `gh pr view --json number 2>/dev/null`

If PR already exists for branch, update its description. Otherwise create new PR.

**Create new PR:**

```bash
gh pr create --base "<base-branch>" --title "<title>" --body "$(cat <<'EOF'
<description body here>
EOF
)"
```

**Update existing PR:**

```bash
gh pr edit --body "$(cat <<'EOF'
<description body here>
EOF
)"
```

**Description format:**

Structure depends on what step 4.6 found:

- **Template found** → use the template's sections verbatim, in the order it defines. Fill each section from intent + validated diff. Append the `<details>` Implementation Plan block at the end if a plan file exists.
- **No template** → write a plain concise description (1-3 short paragraphs) covering intent and approach. No `# What / # Why / # How` scaffold. Append the `<details>` Implementation Plan block at the end if a plan file exists.

The Implementation Plan block (append to either format when a plan file exists):

```
<details>
<summary>Implementation Plan</summary>

[PLAN_CONTENT — see "Finding the plan file" below.]

</details>
```

**Issue/PR references:** When referencing related issues or PRs, use bulleted lists (`- #123` or `- https://github.com/owner/repo/issues/123`) so GitHub renders them as rich linked cards.

**Avoid accidental issue links:** On GitHub, `#` followed by a number (e.g., `#1`, `#42`) automatically creates a hyperlink to the issue/PR with that number. Only use `#NUMBER` when intentionally linking to an issue or PR. Never use it in prose like "the #1 cause" or "#3 priority" — rephrase instead (e.g., "the top cause", "third priority"). If a literal `#` before a number is unavoidable, escape it with a backslash (`\#1`).

**Finding the plan file:**

1. **Check conversation history first.** Look in this conversation for a plan file path from the harness (for example, Claude Code injects `~/.claude/plans/<name>.md` after plan mode). Read that file.
2. **If not in history**, and the harness keeps plan files in a known folder (for example `~/.claude/plans/`), list its 5 most recently modified `.md` files. Read the first few lines of each to identify which one matches the current task. If no plan clearly matches, omit the plan section.
3. Paste the plan file's full markdown contents into the `<details>` block. Do NOT include the file path — plan files are gitignored and won't exist in the PR.
4. If no plan file is found by either method, omit the entire `<details>` block.

**Optional sections** (untemplated repos only — if template is present, follow it instead):

- `### Decisions` - if user explained trade-offs or choices made
- `### Risks` - ONLY if user mentioned specific concerns

### Anti-Patterns

| Don't | Why |
|-------|-----|
| Fabricate intent | User didn't explain why -> ASK, don't invent |
| Ignore the repo's PR template | The template is the reviewers' explicit ask — always follow it |
| Scaffold `# What / # Why / # How` on untemplated repos | Imposed headers on a short PR are noise |
| List files changed (untemplated) | GitHub already shows this — unless the template asks for it |
| Speculate on risks (untemplated) | Only include if user mentioned them — unless the template asks for it |
| Narrate code changes | Diff shows the implementation. Test: if the sentence names the before/after API or could be rewritten as "I changed X to Y", delete it — reviewers already read the diff |
| Add "Test plan" (untemplated) | Testing is implicit in QA — unless the template asks for it |
| Pad descriptions for length | Thorough ≠ verbose. Every sentence must earn its place |
| Describe intent, not diff | PR description must reflect the ACTUAL changes, not just what was discussed |
| Use `#NUMBER` in prose | `#42` links to issue 42 — only use for intentional references, rephrase otherwise |
| Open a PR with no issue link when one exists | Orphan release notes, harder triage, loss of `why` context in `git blame` — always search (step 4.55) before giving up |
| Guess or fabricate an issue number | `Fixes #NNN` auto-closes on merge — a wrong number closes an unrelated ticket. Confirm with the user before citing |

### Examples

Walk-through material for the patterns above. Each is a single idea; read whichever matches what you're stuck on.

- [examples/good-pr-description-bugfix.md](examples/good-pr-description-bugfix.md) — canonical "failure modes" template (Core Rule 2), derived from a merged PR.
- [examples/diff-narration-antipatterns.md](examples/diff-narration-antipatterns.md) — five ❌/✅ rewrite pairs, grown from the inline examples in Core Rule 2.
- [examples/issue-search-workflow.md](examples/issue-search-workflow.md) — worked transcript of step 4.55's lookup → verify → confirm flow.
