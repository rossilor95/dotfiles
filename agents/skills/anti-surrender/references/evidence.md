# Evidence and Design Status

This skill combines research findings with proposed engineering rules.
No cited study validates this complete code-change workflow or its question count.

## Research

- [Shaw and Nave, Thinking—Fast, Slow, and Artificial](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6097646).
  This preprint has not completed peer review. It studies reliance on AI during reasoning tasks.
  Accuracy rose when the AI was correct and fell when it was wrong; confidence rose even after errors.
  Basis for the human confidence rating. It does not test a PR comprehension gate.
- [Buçinca, Malaya, and Gajos, To Trust or to Think](https://doi.org/10.1145/3449287).
  Experiments support active cognitive forcing over explanation alone in their task. Transfer to software review remains a hypothesis.
  Participants rated the most effective designs least favorably. Basis for fading support and recording overrides.
- [Shen and Tamkin, How AI Impacts Skill Formation](https://arxiv.org/abs/2601.20245).
  A randomized study found lower immediate mastery with AI assistance while participants learned an unfamiliar Python library.
  Interaction-pattern findings are observational. The study does not establish long-term skill loss or validate this gate.
  Delegation and iterative AI debugging had low quiz scores; conceptual questions and explanation requests had high scores.
  Basis for watching low-learning interaction patterns.
- [Bastani and colleagues, Generative AI without guardrails can harm learning](https://doi.org/10.1073/pnas.2422633122).
  A field experiment with high school math students found lower scores after unrestricted GPT-4 access was removed.
  A tutor version that gave hints instead of answers largely removed the harm. One school, one subject, short period.
  Basis for the human-approach step and the hint order. Transfer to professional software work remains a hypothesis.
- [Kalyuga, Ayres, Chandler, and Sweller, The Expertise Reversal Effect](https://doi.org/10.1207/S15326985EP3801_4).
  Instructional support that helps novices can reduce learning for people with more prior knowledge.
  Basis for fading support for retained concepts. The research comes from instructional design, not AI-assisted coding.
- [Vasconcelos and colleagues, Explanations Can Reduce Overreliance on AI Systems](https://arxiv.org/abs/2212.06823).
  Explanations reduced overreliance only when they made verification cheaper. The task was a maze, with a simulated AI.
  Basis for giving the human a runnable verification command.
- [Lee and colleagues, The Impact of Generative AI on Critical Thinking](https://doi.org/10.1145/3706598.3713778).
  A survey of 319 knowledge workers. Higher confidence in AI was associated with less critical thinking.
  Higher self-confidence was associated with more. The findings are self-reported and correlational.
- [METR, Early-2025 AI and Experienced Open-Source Developer Productivity](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/).
  A randomized study with 16 developers on their own mature repositories. Tasks with AI took 19% longer.
  Developers still estimated a 20% speedup. Small sample and early-2025 tools.
  Supports the confidence rating: perceived benefit can differ from measured benefit.
- [Prather and colleagues, The Widening Gap](https://arxiv.org/abs/2405.17739).
  A lab study with 21 novice programmers. Students who struggled with AI often believed that they had done better than they had.
  Qualitative study. Supports the confidence rating and the watch for low-learning patterns.
- [Kosmyna and colleagues, Your Brain on ChatGPT](https://arxiv.org/abs/2506.08872).
  Participants who wrote without help first and then used an LLM recalled more than LLM-only participants.
  Preprint, small sample, essay task. Weak support for asking the human's approach first.
- [Roediger and Karpicke, Test-Enhanced Learning](https://doi.org/10.1111/j.1467-9280.2006.01693.x).
  Retrieval practice improved delayed retention in learning experiments. This does not prove that PR quizzes prevent production defects.
- [Gosline and colleagues, Nudge Users to Catch Generative AI Errors](https://sloanreview.mit.edu/article/nudge-users-to-catch-generative-ai-errors/).
  The authors describe an Accenture field experiment with review support for generated text.
  The accessible article reports improved accuracy without a significant task-time increase.
  The accessible text does not provide full methods or effect estimates.

## Practice Reports

- [mmarseglia/cognitive-surrender, general prompts](https://github.com/mmarseglia/cognitive-surrender/blob/main/prompts/README.md).
  Basis for challenging weak premises, stating uncertainty, and keeping human and AI contributions distinct.
- [mmarseglia/cognitive-surrender, coding prompt](https://github.com/mmarseglia/cognitive-surrender/blob/main/prompts/coding.md).
  Basis for checking designs before coding and reporting assumptions, omitted tests, and uncovered cases.
  Our skill adds human understanding checks before code changes and before handoff or any PR, including drafts.
  These checks are our design extension, not an upstream requirement or an experimentally validated protocol.
- [Addy Osmani, Cognitive Surrender](https://addyosmani.com/blog/cognitive-surrender/).
  Engineering interpretation and suggested habits. Treat these as guidance, not as a controlled PR experiment.
- [Thariq Shihipar, Finding Your Unknowns](https://claude.com/resources/articles/a-field-guide-to-claude-fable-finding-your-unknowns).
  Describes unknowns, implementation notes, explanations, and quizzes before merge. This is a personal practice report.
- [Workday, Overcoming Cognitive Surrender](https://www.workday.com/en-us/perspectives/ai/overcome-ai-cognitive-surrender.html).
  Proposes workflow pauses, including hidden confidence scores. These proposals do not establish efficacy for each mechanism.

## Proposed Rules

The trivial-change exemption, two checks, three-to-five questions, two-attempt limit, and change record are design choices.
The three-step hint order, the fading levels, and the low-medium-high confidence rating are also design choices.
Use pilot results to adjust them.
Do not invent a confidence percentage or claim that the assistant can read a person's mental state.
The human's own confidence rating is different: it is self-reported, and it is used only for calibration feedback.
Do not treat a correct explanation of faulty code as evidence that the code meets its requirement.
