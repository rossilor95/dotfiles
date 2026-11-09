---
name: pick-up-issue
description: "Pick up a Jira or GitHub issue: read it, check human understanding, assign it, implement it, and open a PR. Update the issue for review using its existing workflow. Use for issue implementation requests, not lookup or planning alone."
---

# Pick up Issue

Complete this sequence for the issue the user selects:
read it, check understanding, assign it, start work, implement, open a PR, and comment with its URL.
Then update the tracker for review. Use In Review for Jira and an existing review status for GitHub Projects.
Use `anti-surrender` for nontrivial work before code changes and before creating any PR, including a draft.

## Scope and access

- Apply the user's current limits before these defaults. A lookup or planning request permits no Jira or GitHub writes.
- This skill supplies a procedure, not new permissions. Ask before a write if the user's intent is unclear.
- Use available Jira tools and GitHub tools or `gh`. Use browser control when the required operation has no supported tool.
- Keep existing authentication and security controls. Stop at an access blocker; do not change credentials or access policy.
- Leave merging, closing issues, and production deployment outside this procedure unless the user requests them.

## 1. Resolve the issue and repository

Identify the tracker from the issue URL or explicit user context.
Accept a Jira key, GitHub issue URL, or GitHub number when its repository is known.
Resolve a bare issue number against a verified repository. Ask if its repository is ambiguous.
For GitHub, retain the exact hostname, owner, repository, and issue number for every operation.
Confirm that the selected item is an issue rather than a pull request.
The issue repository can differ from the implementation repository; verify both when needed.

Read the issue, acceptance checks, comments, assignee, status, and linked PRs.
For GitHub, also inspect linked Projects and relevant existing status fields when accessible.
Resolve an implied issue from recent context only when there is one clear match.
Ask when the issue, repository, or required scope is unclear.

Read the repository instructions. Confirm the repository through its remote URL, not its folder name.
Use the issue and user context to establish intent before writing a PR description.

Check for an existing branch, worktree, or PR for this issue. Resume suitable work instead of creating duplicates.
If the issue is closed or its PR is merged, confirm that further work is needed before changing state.

### Check understanding before code changes

Load the installed `anti-surrender` skill, normally at `../anti-surrender/SKILL.md` relative to this skill.
Use that skill to classify the change after reading the issue and relevant code.
For nontrivial work, complete its first understanding check with the human before editing code, tests, or behavior-related configuration.
Base questions on this issue's acceptance checks, important constraints, and affected code.
Use open questions about product context, existing architecture, and technical choices, as applicable to this issue.
Wait for actual human answers. Record an honest gap, explain the context, and ask a fresh question when needed.
For trivial work, record the reason and skip the questions. An ordinary dependency bump can qualify.
Reclassify when a bump introduces breaking changes, migrations, or material runtime or security effects.
On resumed work, reuse valid answers and check that the scope still matches.
If `anti-surrender` is unavailable, report the missing skill and pause nontrivial code changes and PR creation.

## 2. Start the issue

For nontrivial work, wait until the first understanding check is complete before assignment or status changes.
Resolve the authenticated account for the selected tracker when the user says "me".
Do not assume the Git author, Jira account, and GitHub account are the same person.
Assign the issue to that account, unless the user names another assignee.
For GitHub, preserve other assignees unless the user requests their removal.
Verify assignment after the change. Skip assignment when it is already correct.

### Jira

Discover the workflow transitions and move the issue to In Progress.
Use the project's equivalent status when its meaning is clear; otherwise ask.
Verify the resulting status. Skip a transition already complete.

### GitHub

Keep the issue open while work proceeds.
Use the existing Project Status field when the issue belongs to a known workflow.
Select an existing In Progress option or a clearly equivalent option. Verify the saved value.
When several Projects apply, use the one established by repository rules or user context.
Ask only when the choice changes a required workflow action and remains unclear.
If no Project or suitable option exists, continue with assignment and the open issue.
Report an inaccessible Project status as unchanged; continue work that does not depend on that update.
Create no Projects, fields, status options, labels, or memberships merely to imitate Jira.

## 3. Implement and check

For new work, use the requested base branch or the repository's default branch. Fetch its current remote state first.
Reuse a suitable checkout or worktree. Create a new worktree when requested or needed to protect existing changes.
Keep unrelated user changes outside this PR.

Implement the issue's scope. Run the repository's required checks and focused tests.
For dependency removal, check runtime registration, transitive consumers, and security constraints as well as source references.
Record which checks passed, failed, were skipped, or could not run. Preserve any user-approved test limits.

If implementation cannot continue, report the blocker and completed work. Do not claim completion or advance to review.

## 4. Commit and open the PR

For nontrivial work, complete `anti-surrender`'s final understanding check against the finished change before creating any PR.
Ask about actual behavior, important choices, affected dependencies, and a new failure case.
Include product reasoning and architecture when relevant. Use `anti-surrender` to record learning gaps and the next practice step.
Keep PR creation pending until the human passes. This includes draft PRs and validation-blocked PRs.
Local tests, local commits, and PR text preparation may continue within existing authorization.
Before creating the PR, confirm that the current diff still matches the checked result.
Repeat affected questions after material changes. Keep the assessment rules in `anti-surrender` rather than duplicating them here.

Before every commit, check the attribution rules from the repository, the user, and the agent harness.
When these rules require an agent co-author trailer, add the specified trailer once to every agent-created commit.
Preserve existing trailers. Put one blank line before the trailer block.
Leave human commits and existing history unchanged. Verify the final commit message before pushing.

Use the `create-pr` skill when available. Follow the repository's PR template in all cases.
Explain the issue's intent and material review decisions. Include an accurate check summary.
Use the selected issue as the confirmed reference; avoid asking the user to confirm it again.
For Jira, include exactly one Jira link in the description: this issue's link.
For a subtask, use the subtask link. Omit parent, epic, and other Jira links and keys.
For GitHub, include the selected issue's canonical URL once in the description.
Use a plain reference such as `Refs: <issue URL>` by default.
Use closing keywords or a link that closes the issue on merge only when that behavior is requested.
Apply this rule to commit messages too. It overrides the generic closing-keyword defaults in `create-pr`.

Review the final diff, push the issue branch, and create or update its PR with an explicit base branch.
If required validation is blocked, use a draft PR only after the applicable understanding checks are complete.
State what remains unverified. A draft is not proof that the issue's acceptance checks passed.
Attach the PR to the current chat when the app supports PR attachments.
Verify the PR URL, base, title, description, and draft state after creation.
For Jira, check for an automatic duplicate Jira footer. Remove it once if needed.
Report repeated bot changes instead of retrying indefinitely.

## 5. Comment and prepare review

Start the comment on the selected issue with the verified PR URL.
Add a short result, draft state, and any pending checks.
If an equivalent PR comment already exists, reuse it or update your own comment when needed.
If there are no comments, this becomes the first comment. Preserve existing comments.
Verify that the saved comment links to the correct PR.

### Jira

Move the issue to In Review only after the PR exists and its comment is saved.
Discover the transition instead of using a stored status ID. Verify the resulting status and assignee.

### GitHub

Keep the issue open; a PR awaiting review does not mean the issue is complete.
After the PR comment is saved, use an existing In Review Project option when the workflow has one.
Apply the same Project selection rules used when starting work. Verify the saved value.
If no review option exists, leave the Project status unchanged and report that no review status applies.
Report access failures without inventing a successful transition. Preserve the completed PR and comment.

Disclose any draft state and validation blocker in the comment and final response.

## Completion report

Return the issue link, tracker, assignee, and PR link. State whether the PR is a draft.
Report Jira status, or GitHub issue state and any Project status changed. Include check limits.
If a step failed, name it and stop dependent steps. On retry, read current state before repeating any write.
For a pending understanding check, return the remaining question and completed work; keep PR creation and the tracker review update pending.
