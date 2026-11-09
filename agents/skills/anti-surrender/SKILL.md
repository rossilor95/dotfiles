---
name: anti-surrender
description: Check human understanding and build technical and product skills during coding tasks, with or without Jira or a PR. Use open questions about context, architecture, and technical choices before nontrivial code changes and handoff. Require answers before any PR, including drafts. Exempt verified trivial changes.
---

# Anti Surrender

Keep the human responsible for the problem, design, and result.
Use two understanding checks for nontrivial work. A PR is optional; the checks are not PR-specific.
Base this process on the general and coding prompts in [cognitive-surrender](https://github.com/mmarseglia/cognitive-surrender/tree/main/prompts).
The two checks are our extension. They are not a validated protocol from that project or the research.
Use open questions to build technical understanding and product judgment over time.
Read [the learning guide](references/learning.md) when selecting learning goals or recording progress.

## 1. Read and classify

Read the task, acceptance checks, repository rules, and relevant existing code before asking questions.
For Jira work, include issue comments and linked requirements. Retrieve available facts instead of asking the human to find them.
Summarize the requested behavior and scope without proposing a solution yet.

Classify the actual change:

- Trivial: a mechanical change with no material behavior, contract, or design decision.
- Nontrivial: changed behavior, a design choice, uncertain impact, or an area the human needs to understand.

An ordinary dependency version bump can be trivial. Check release notes and compatibility when relevant.
Escalate bumps with breaking changes, migrations, or material runtime or security effects.
Use impact and uncertainty rather than line count. Give one short reason for the classification.
For trivial work, skip both checks and continue normal implementation and validation.
Reclassify when new evidence makes the change nontrivial. Complete the first check before further code changes.

## 2. Check understanding before code changes

After reading the task, ask the human to explain these points in their own words:

- What behavior should change, and what would show that it works?
- What important behavior or constraint must stay the same?
- What assumption, dependency, or failure case needs attention?

Connect the first question to the user problem and expected product outcome when the task changes product behavior.
Include one architecture question about the existing flow, component boundary, or data ownership when relevant.
Use three or four questions in total; adapt the set to the task rather than adding a separate exam.
Use task-specific questions. Ask a short group together when the questions are independent.
Require explanations or predictions. Avoid multiple-choice questions, yes/no questions, and "does this make sense?" checks.
Keep proposed solutions and model answers out of the questions.
Wait for actual human answers before editing code, tests, or behavior-related configuration.
Read-only inspection can continue while answers are pending.
Opening a branch or assigning an issue does not satisfy this check.

Compare the answers with requirements and source evidence. Challenge incorrect premises before proposing a design.
Accept honest uncertainty. Explain missing context, then ask a fresh question about the affected concept.
The check passes when the human correctly explains the target behavior and the important constraint.
Record remaining unknowns with a concrete way to resolve them.
Resolve unknowns that determine scope or required behavior before editing dependent code.
If the human already saw an AI solution, record that exposure rather than claim an independent starting view.

## 3. Implement with clear evidence

Before coding, challenge a flawed approach and explain any material alternative or hidden failure case.
Separate verified facts, inferences, and unknowns for important API, library, and configuration claims.
Verify important uncertain claims against repository code or official documentation.
Keep human decisions separate from AI proposals.

During implementation, record important assumptions, new unknowns, and departures from the agreed plan.
Pause dependent edits when new evidence changes required behavior or an important constraint.
Ask a focused follow-up; retain valid earlier answers instead of restarting the whole check.
Continue authorized work that does not depend on the answer.
Run normal tests and required checks. Record omitted tests and uncovered cases plainly.

## 4. Check understanding before handoff

Apply this check even when no PR is planned.
Complete it before presenting nontrivial work as finished or handing it over for use or review.
When a PR is planned, complete it before creating the PR, including a draft.
A request to implement, open a PR, or finish quickly does not itself satisfy this check.
Prepare local PR text while waiting if useful; keep PR creation pending.

Explain the actual change, important decisions, effects on existing code, and remaining validation limits.
Include source locations and a concrete example. Distinguish required behavior from actual behavior.
Read [the question guide](references/question-guide.md), then select three to five relevant questions.
Cover context, architecture, and the technical solution through the questions that matter for this change.
For product changes, include the user outcome, a product assumption, or evidence that could challenge the proposed value.
Explain any category that does not apply; avoid questions invented merely to fill a category.
Prepare source-backed assessment criteria before asking. Mark critical questions without revealing their answers.
Ask one question at a time and wait for the human's answer.
Include at least one new case that the explanation did not already solve.
Prioritize failure prediction, affected dependencies, test limits, and the reason for important decisions.

Allow source code and official documentation. Request the human's own answer rather than an AI-generated answer.
Accept plain language and valid alternatives. Assess causal understanding rather than exact wording or trivia.
For an incorrect answer, explain the gap and ask a different case about the same concept.
After two unsuccessful attempts on one concept, offer a focused walkthrough and keep the check pending.
Verify disputed question premises against evidence. Use human review when an important dispute remains unresolved.

The check passes when critical behavior is correctly explained and a new case receives a correct, reasoned prediction.
Resolve other material gaps through instruction and a new question before recording completion.
If an answer exposes a defect, fix it and repeat affected questions against the corrected result.
Passing this check does not replace code review or prove software correctness.

## 5. Record and resume

Keep a short record in the current conversation unless another location is requested.
Record classification, human answers, important evidence, open gaps, and each check's state: pending or complete.
Use a reasoned exemption for trivial work. Never turn silence, elapsed time, or an AI-written answer into a pass.
For pending checks, report the exact remaining question and stop dependent actions.

Bind the final check to the actual diff, including relevant uncommitted changes.
Record base and head revisions when available. Also identify working-tree changes that those revisions do not cover.
Before handoff or PR creation, confirm that the checked result still matches the current code and requirements.
Repeat affected questions after material changes. Mechanical changes need only a brief recorded reason.
Resume from valid answers; do not repeat completed checks without a relevant change.
After the final check, record one demonstrated concept, one remaining gap, and one next practice step when applicable.
Use the learning guide to update the personal learning record from actual answers.

Publish notes, change external state, or merge only within existing authorization.
If the user explicitly overrides a check, record the exception and its limits. Never report it as a passed check.
Jira and PR workflows must wait at pending checks rather than bypass them through a draft PR.

## Limits and sources

This skill supplies instructions, not a technical command block or a measure of someone's mental state.
Model agreement and model confidence are not proof of correctness. Avoid invented confidence percentages.
Keep architecture review and targeted code inspection together.
Read [the evidence notes](references/evidence.md) when explaining this process or its sources.
