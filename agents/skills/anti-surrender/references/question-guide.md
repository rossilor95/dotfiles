# Question Guide

Use these forms with actual source locations, inputs, and expected behavior.
Choose the smallest set that covers the critical claims.
Use open questions about context, architecture, and technical choices. Include product reasoning when relevant.

| Form | Example | Sufficient answer |
| --- | --- | --- |
| Context | Which user problem does this change address, and what evidence would show improvement? | Separate the requested feature from the problem and a testable outcome |
| Architecture | Trace a request across the affected components. Where does each component own data or decisions? | Explain real boundaries, dependencies, and the condition that must remain true |
| Technical choice | Why does this approach fit these constraints? When would an alternative be better? | Connect the chosen mechanism to a real constraint and a concrete trade-off |
| Product assumption | Which assumption could make this feature unnecessary, and how could we test it cheaply? | Identify a plausible assumption and a practical observation or experiment |
| Predict | A request commits, but its response is lost. What happens when the caller retries? | Trace the second request, stored result, and side effects |
| Explain a condition | Which check prevents one customer from reading another customer's record? | Name the real check, execution path, and limits |
| Find a failure | Which existing caller fails if this field becomes optional? | Identify a real caller and show the failing input |
| Check a test | Which test fails if this protection disappears? What would that failure show? | Connect the assertion to the protection; identify missing coverage honestly |
| Transfer | A second worker starts at the same time. Does your previous answer still hold? | Explain ordering and the relevant lock, constraint, or missing protection |
| Compare | Which rejected design becomes useful if this constraint changes? | Connect a real trade-off to the changed constraint |
| Recover | The schema change is live. Can the old application version still run? | Explain compatibility, data state, and recovery limits |

## Assessment

Assess each answer as sufficient, incomplete, incorrect, or unresolved.
State the exact causal claim that supports the assessment.
An answer can correctly identify that the implementation fails. Preserve that finding and fix the implementation.
Then update the questions and revision record.

Use at least one scenario that the explanation did not already solve.
Ask about intended behavior and actual behavior separately when they differ.
Use source evidence to settle disputes. Escalate important disputes to another human.
Avoid trivia, function-name recall, suggested answers, and unsupported hypothetical defects.
Avoid multiple-choice questions and binary confirmation questions.
For product questions, accept several defensible answers. Ask for evidence and trade-offs rather than a model's preferred opinion.
Ask about recovery only when the change needs a recovery path.
For pure logic, focus on inputs, outputs, and boundary conditions.

## Practical example

Change: retry payment creation after a timeout.

Question: The provider charged the card, but the response timed out. What prevents a second charge?

Before asking, inspect the idempotency key, lifetime, storage, provider contract, and retry path.
Use verified behavior as the assessment criterion.
The human must explain the complete protection or identify where it fails.

After instruction, change the scenario: two workers retry at the same time.
Assess the new prediction using actual concurrency control.
Never insert a defect into production code merely to test the human.

## Pilot

Try the workflow on real, noncritical code changes before making it a team requirement.
Include work with and without PRs.
Track review time, gaps found, disputed assessments, and unnecessary interruptions.
With participant agreement, ask one new scenario later to check retained understanding.
Use a separate human assessment to inspect a sample of model grades.
Compare similar changes and experience levels. A before-and-after comparison alone does not establish causation.
Keep learning results separate from employee performance rankings.
