# Continuous Learning

Use coding work to improve technical skills and product judgment through practice and specific feedback.
Keep the task useful; avoid unrelated exams or a fixed curriculum for every change.

## Select a goal

Choose one or two learning goals from the task and the human's previous answers.
Cover a real gap or a concept the current change makes useful.
Examples include data ownership, concurrency, testing, compatibility, user problems, product assumptions, and measures of success.
Explain why the selected concept matters to this change.
When no evidence of a gap exists, ask rather than assume the person's level.
Skip extra practice for trivial work unless the human requests it.

## Practice and feedback

Use open questions that require explanation, comparison, or prediction.
For context, connect the requirement to an affected user and a concrete problem.
For architecture, trace existing behavior and identify important boundaries or conditions.
For technical choices, connect the mechanism to constraints, failure cases, and validation.
For product judgment, distinguish delivered functionality from evidence of user value.
Ask how an observation could challenge the product assumption.
For maintenance tasks, reliability or reduced operating cost can be the relevant outcome.
Do not force a business metric into work where it adds no useful decision.

Assess source-checkable technical claims against code, tests, or official documentation.
Assess product reasoning through explicit assumptions, evidence, trade-offs, and a feasible way to learn.
A defensible alternative can pass even when it differs from the assistant's proposal.

After a gap appears, give specific feedback and a short explanation.
Then change an input, constraint, or failure condition and ask the human to reason again.
After the task, name one concept demonstrated, one remaining gap, and one useful next practice step when applicable.
Avoid general labels such as "senior", invented competence scores, and praise without evidence.

## Personal record

Keep learning notes outside the repository unless the user requests a repository location.
Use a user-selected location when provided.
Otherwise use `$AGENT_LEARNING_DIR/anti-surrender.md`, or `~/.agents/learning/anti-surrender.md` when `AGENT_LEARNING_DIR` is unset.
Use the same file for all agent harnesses, so progress continues across tools.
Read this record before selecting goals when it exists and access is permitted.
Create or update it only within the available write permissions.
If access is unavailable, keep the note in the conversation and report the persistence limit.
Do not block the coding task merely because the learning record cannot be saved.

Use a small table:

| Concept | Evidence and date | Current observation | Next practice |
| --- | --- | --- | --- |

Use observations such as gap observed, explained after instruction, applied to a new case, or retained on a later task.
Also record the hint step the human needed, a large difference between confidence rating and result, and check overrides.
Base each observation on an actual human answer. Do not invent progress or record model answers as human evidence.
Update the existing concept row to keep the record short.
Store generalized concepts and concise evidence, without proprietary code, ticket contents, or personal performance rankings.
Tell the human which learning note changed.

## Later tasks

On a later relevant task, ask one new application of a previously difficult concept before giving its answer.
Use that question within the normal question budget.
Record retained understanding only after an adequate answer on that later task.
Use an incorrect answer to select the next practice step, not as a penalty.

## Fade support

Reduce support as understanding grows. Support that helps a beginner can slow down a person who already knows the concept.

- Retained concept: ask one check question or none. Skip the human-approach step for that concept.
- Concept applied to a new case once: ask one question. Start hints at step 2 or skip them.
- Gap observed: use the full question budget and the full hint order.

Restore full support when a retained concept receives an incorrect answer.
Repeated confidence that is higher than the results is a reason to keep support, even for a retained concept.
These notes support learning. They do not certify ability or establish a causal effect from this workflow.
