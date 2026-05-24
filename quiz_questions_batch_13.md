# Quiz Question Bank - Batch 13 (Questions 121-130)

## Question 121
**Title:** Preserving Conflicting Source Claims
**Situation:** A research workflow combines a regulator filing and a vendor whitepaper about the same market. One source reports 11% growth for 2024, while the other reports 18%. The publication dates differ by one quarter, and the synthesis agent tends to compress both into a single number for the final report. What is the best design choice?
**Options:**
- A) Preserve both figures with source dates and attribution, then let the coordinator reconcile the conflict
- B) Prefer the regulator filing automatically and suppress the vendor figure to keep the report concise
- C) Average the two percentages and cite both documents as supporting evidence for the blended number
- D) Ask the synthesis agent to pick the value that matches most other narrative claims in context

**Correct Answer:** A
**Feedback:** When sources conflict, the system should preserve both claims with attribution and timing metadata instead of collapsing them into one value. That keeps provenance intact and lets the coordinator decide whether the difference is a true contradiction, a scope mismatch, or a time-window issue. The other options hide uncertainty or manufacture a conclusion that the evidence does not justify.
**Theory Reference:** Domain 5.6 - Preserving provenance and handling uncertainty in multi-source synthesis

---

## Question 122
**Title:** Sharing Team-wide Claude Code Instructions
**Situation:** A staff engineer maintains coding standards in their personal `~/.claude/CLAUDE.md`. New teammates do not receive those instructions when they clone the repository, and the standards are growing large enough that the team wants a cleaner structure. What is the best fix?
**Options:**
- A) Put the standards in each developer's `~/.claude/CLAUDE.md` and distribute setup instructions
- B) Add the standards to a custom skill so developers can invoke it before each task
- C) Store them in project `.claude/CLAUDE.md` and reference detailed files with `@path` imports
- D) Create directory-level `CLAUDE.md` files everywhere so all instructions load in every session

**Correct Answer:** C
**Feedback:** Project-level instructions belong in version control so every contributor gets the same guidance automatically. Using `.claude/CLAUDE.md` solves the sharing problem, and `@path` imports keep the configuration modular as it grows. A remains user-scoped, B depends on manual invocation, and D loads more rules than necessary.
**Theory Reference:** Domain 3.1 - CLAUDE.md hierarchy, scope, and modular organization

---

## Question 123
**Title:** Reducing Tool Misuse in a Specialist Subagent
**Situation:** A synthesis subagent is supposed to summarize evidence, but it has 16 available tools, including search, editing, shell, and reporting tools. Logs show it frequently reaches for unrelated tools instead of focusing on synthesis. What change is most likely to improve reliability?
**Options:**
- A) Keep all tools available and rely on stronger prompt wording to discourage off-role calls
- B) Limit the subagent to role-specific tools plus a small set of necessary utilities
- C) Force `tool_choice: "any"` so the model always chooses some tool before answering
- D) Merge similar tools into one large interface so the agent has fewer names to compare

**Correct Answer:** B
**Feedback:** Tool selection gets less reliable when agents see a large, weakly scoped toolset. Restricting the subagent to tools that match its role follows least privilege and reduces distracting options. Stronger prompting helps less than architectural scoping, while forcing any tool call or merging tools does not address the specialization problem.
**Theory Reference:** Domain 2.3 - Allocating tools across agents and configuring `tool_choice`

---

## Question 124
**Title:** Correcting Semantic Extraction Errors
**Situation:** An invoice extraction workflow uses a JSON-schema tool, so outputs are always valid JSON. However, one result shows line items totaling 980 while `stated_total` is 1080, and the tax field appears misplaced. What is the most effective next step?
**Options:**
- A) Add more required fields to the schema so totals are less likely to mismatch again
- B) Accept the JSON as correct because schema validation already guarantees extraction quality
- C) Retry with a larger model but no error details so the model can rethink from scratch
- D) Retry with the source, prior output, and explicit validation errors about the mismatch

**Correct Answer:** D
**Feedback:** The schema already solved syntax reliability, but this failure is semantic. The best recovery loop is to show the original source, the flawed extraction, and the exact validation error so the model can correct the inconsistent fields. More required fields do not guarantee semantic correctness, and blind retries waste information that would help the correction.
**Theory Reference:** Domain 4.4 - Validation, retries, and feedback loops for extraction quality

---

## Question 125
**Title:** Enforcing Required Workflow Order
**Situation:** A support agent handles account changes and refunds. Even after prompt tuning, it sometimes calls downstream tools before identity verification is complete, creating compliance risk. What architecture gives the strongest guarantee?
**Options:**
- A) Add programmatic preconditions that block downstream actions until verification completes
- B) Expand the system prompt with stronger reminders about the required tool sequence
- C) Lower the model temperature so it follows the ordering more consistently in production
- D) Ask the agent to explain its reasoning before each action so mistakes become easier to spot

**Correct Answer:** A
**Feedback:** When a workflow order is mandatory, prompts are not enough because they only influence behavior probabilistically. Programmatic preconditions enforce the required sequence deterministically by blocking restricted actions until verification data exists. The other options may reduce errors somewhat, but they cannot guarantee compliance.
**Theory Reference:** Domain 1.4 - Multi-step workflows with enforcement and handoff patterns

---

## Question 126
**Title:** Handling Ambiguous Customer Matches
**Situation:** A lookup tool returns three customer profiles that all match the provided last name and city. The user wants an address change processed immediately. What should the agent do next?
**Options:**
- A) Pick the profile with the most recent order because it is usually the active customer
- B) Escalate immediately to a human because ambiguity means automation should stop
- C) Ask the user for another identifier before taking any account-specific action
- D) Use self-rated confidence and continue when the score is above an internal threshold

**Correct Answer:** C
**Feedback:** Multiple matches create real ambiguity, so the correct move is to request another identifier such as email, order ID, or customer number. That resolves the uncertainty directly without unsafe guessing. Human escalation may be unnecessary if the user can clarify, and confidence scores are a weak substitute for actual verification.
**Theory Reference:** Domain 5.2 - Designing effective escalation patterns and resolving ambiguity

---

## Question 127
**Title:** Validating Automation Beyond Aggregate Accuracy
**Situation:** A document extraction system reports 97% overall accuracy across 50,000 samples. Before fully automating approvals, the team wants to know whether a few document types or fields still fail at unacceptable rates. What is the best evaluation method?
**Options:**
- A) Raise the automation threshold because high overall accuracy already indicates stable performance
- B) Measure accuracy by document type and field using stratified labeled samples
- C) Review only low-confidence cases because high-confidence outputs rarely hide systematic errors
- D) Compare this month's average accuracy with last month's and automate if the trend improves

**Correct Answer:** B
**Feedback:** Aggregate accuracy can hide concentrated failures in specific document types or fields. Stratified sampling with labeled data lets the team measure where the system is actually reliable and where human review is still needed. The other choices assume the average tells the whole story, which is exactly the risk this domain warns about.
**Theory Reference:** Domain 5.5 - Human oversight and confidence calibration

---

## Question 128
**Title:** Reviewing Code Without Generation Bias
**Situation:** Claude Code generated a multi-file refactor earlier in the session. When asked to review the same change set, it keeps missing subtle integration bugs and tends to defend its own implementation choices. What process should the team prefer?
**Options:**
- A) Re-run review in the same session with a longer prompt covering more bug categories
- B) Ask the generating session to summarize its reasoning, then review from that summary alone
- C) Use three reviews from the same session and keep findings that appear twice
- D) Use an independent review instance, then add a separate cross-file integration pass

**Correct Answer:** D
**Feedback:** A fresh review instance is more willing to challenge earlier assumptions because it does not carry the original generation context. Adding a separate integration pass also helps catch cross-file issues that a local pass may miss. Reusing the same session preserves the same blind spots, even with stronger prompts or repeated passes.
**Theory Reference:** Domain 4.6 - Designing multi-instance and multi-pass review architectures

---

## Question 129
**Title:** Choosing the Right Built-in Search Tools
**Situation:** You need to investigate all TypeScript files whose names contain `payment`, then trace where `authorize()` is used inside those files. Which approach best fits the built-in tool workflow?
**Options:**
- A) Use Glob to locate matching filenames, then Grep inside them for the symbol
- B) Use Grep for the filenames first, then Edit each result directly to trace usage
- C) Read the repository root files manually because pattern tools hide important context
- D) Start with Bash `find` and custom scripts since built-in tools are too limited here

**Correct Answer:** A
**Feedback:** Glob is the right first step when the problem is file discovery by name pattern, and Grep is the right follow-up when the problem becomes content search within those files. This sequence narrows the search space efficiently and matches the intended tool roles. The other options either misuse tools or skip higher-level built-ins unnecessarily.
**Theory Reference:** Domain 2.5 - Selecting and applying built-in tools

---

## Question 130
**Title:** Resuming Work After the Codebase Changed
**Situation:** You named a session while investigating a bug, then the branch was rebased and several relevant files changed. The prior session contains useful findings, but some conclusions may now be stale. What is the best next step?
**Options:**
- A) Resume the old session unchanged because named sessions already preserve the needed context
- B) Fork the old session automatically so both branches share the same stale findings
- C) Start a new session from a structured summary and note which files changed since then
- D) Keep the old session but remove earlier tool results so the newer files get more attention

**Correct Answer:** C
**Feedback:** When earlier findings may be stale, a new session grounded in a structured summary is often safer than blindly resuming the old context. It preserves the useful discoveries while making the changed files explicit, which helps the agent reason from current facts. Resuming or forking stale context risks carrying forward outdated assumptions.
**Theory Reference:** Domain 1.7 - Session state, resuming, and forking
