# Quiz Question Bank - Batch 02 Part 04 (Questions 91-100)

## Question 91
**Title:** System Prompt and Tool Selection
**Situation:** Your system prompt says: "You are a helpful customer support agent." You add a new `escalate_to_manager` tool. Now, the agent rarely uses it, preferring to solve problems itself. The tool exists but isn't selected. Why?
**Options:**
- A) Update system prompt: "Escalate to a manager when: (1) customer explicitly requests a human, (2) issue is outside your authority, (3) customer is frustrated." Make escalation culturally acceptable
- B) Ignore tool selection issues; they're inherent to LLM behavior
- C) Use `tool_choice` to force escalation
- D) Create a separate escalation-only agent

**Correct Answer:** A
**Feedback:** System prompt wording creates unintended associations. "Be helpful" subtly discourages escalation (seen as failure). Explicitly normalizing escalation with clear criteria reframes it as a smart decision, not an admission of defeat.
**Theory Reference:** Domain 2.1 - System prompt and tool selection influence

---

## Question 92
**Title:** Subagent Isolation and Task Specificity
**Situation:** You spawn a subagent with the prompt: "Do research about climate change." The subagent produces a 300K-token comprehensive report. But you only need specific information (CO2 emission trends). How to fix this?
**Options:**
- A) Use specific task prompts: "Research CO2 emission trends from 2015-2025, focusing on industrial sources. Provide concise summary." Specificity reduces noise
- B) Accept the 300K tokens; that's what research produces
- C) Post-process results to filter for relevant information
- D) Use a different subagent with better data filtering

**Correct Answer:** A
**Feedback:** Task specificity drives subagent output quality and conciseness. Vague prompts ("do research") produce unfocused output. Specific prompts ("research CO2 trends 2015-2025, industrial sources, 1-page summary") constrain scope and reduce token waste.
**Theory Reference:** Domain 1.2 - Task specificity and subagent prompts

---

## Question 93
**Title:** Handling Partial Results from Subagents
**Situation:** A fact-checking subagent checks 10 claims. It finds definitive answers for 7, but 3 claims lack sufficient information to verify. Should it report all 10 with confidence levels, or only the 7 verified claims?
**Options:**
- A) Report all 10 with confidence levels (high for verified, low/unclear for insufficient data) and explicit gaps. Enables informed coordinator decisions
- B) Report only the 7 verified claims; don't include incomplete information
- C) Retry checking the 3 unclear claims to find definitive answers
- D) Escalate the unclear ones to a human immediately

**Correct Answer:** A
**Feedback:** Partial results are valuable. Report what you found, confidence levels, and gaps. Coordinator needs to know: "7 claims verified, 3 lack sufficient data." This enables decisions: confirm via alternative sources or accept the gap. Hiding gaps is worse than reporting them.
**Theory Reference:** Domain 5.1 & 5.3 - Handling incomplete information

---

## Question 94
**Title:** `.claude/rules/` Organization
**Situation:** Your project has conventions for testing (Jest, Vitest, Playwright), API design (REST, GraphQL), and deployment (Docker, Kubernetes). How should these be organized in `.claude/rules/`?
**Options:**
- A) Separate files: `.claude/rules/testing.md`, `.claude/rules/api-design.md`, `.claude/rules/deployment.md` with appropriate `paths` frontmatter. Modular approach
- B) Keep everything in one monolithic `.claude/rules/conventions.md` for simplicity
- C) Store conventions in project documentation outside `.claude/`
- D) Create CLAUDE.md files in each relevant directory

**Correct Answer:** A
**Feedback:** Multiple focused rule files are more maintainable than one monolithic file. Each `.claude/rules/*.md` file can have specific `paths` patterns (e.g., `paths: ["**/*.test.*"]` for testing). Modular = easier updates and clearer ownership.
**Theory Reference:** Domain 3.1 & 3.3 - Modular CLAUDE.md organization

---

## Question 95
**Title:** Recognizing Context Degradation
**Situation:** You've been investigating a codebase in Claude Code for 45 minutes. Early findings were clear and accurate. Now, after 8 file reads and 15 tool calls, the agent is: referring to "that file from earlier" (can't find the name), mixing up variable names, and making vague statements. What's happening?
**Options:**
- A) Context degradation from long conversation history. Checkpoint findings, spawn a subagent for the next phase, or start a fresh session
- B) The agent is tired; give it a break
- C) The codebase is too complex; simplify the task
- D) Increase the context window to process longer histories

**Correct Answer:** A
**Feedback:** After ~40-50 iterations, coherence starts declining. Signs: vague references, mixed-up context, imprecise reasoning. Mitigation: save key findings to scratchpad, spawn subagent for next phase, or start fresh. Long histories hurt, not help.
**Theory Reference:** Domain 5.4 - Context degradation detection and management

---

## Question 96
**Title:** Escalation with Rich Context
**Situation:** A customer requests a refund for a $5000 purchase. Your agent cannot approve refunds over $1000 (policy boundary). When escalating to a manager, what context should be included?
**Options:**
- A) Structured escalation: customer ID, order ID, amount ($5000), customer history (5-year member, no prior disputes), reason (product defective), agent's assessment (legitimate claim), recommendation
- B) Just pass the request: "Customer wants refund"
- C) Include only what policy explicitly requires
- D) Don't escalate; deny the request to enforce the policy

**Correct Answer:** A
**Feedback:** Escalations with rich context enable faster, better decisions. Include: who (customer ID), what (order, amount), why (reason, agent assessment), history (relevant context), and recommendation. Rich context = fast, informed manager decision; poor context = delays.
**Theory Reference:** Domain 1.4 & 5.2 - Structured escalation protocols

---

## Question 97
**Title:** Semantic vs Syntactic Validation
**Situation:** A form extraction tool outputs: `{"date_field": "2024-13-45", "amount": 100, "total": 50}`. JSON syntax is valid. But: invalid date (no 13th month), amount > total (illogical). Which type of error is each?
**Options:**
- A) Date error is semantic (invalid date), amount error is semantic (contradiction). Both exceed JSON schema capabilities
- B) Both are JSON syntax errors
- C) Date error is syntactic (bad format), amount error is semantic (violates logic)
- D) Neither are errors; values are just unusual

**Correct Answer:** A
**Feedback:** JSON schema validates syntax (valid JSON structure). Semantic validation checks business logic (date is valid, total >= amount). "2024-13-45" passes JSON schema but fails date semantics. Both errors require domain-specific validation beyond schema.
**Theory Reference:** Domain 4.4 - Semantic vs syntax errors

---

## Question 98
**Title:** Prioritizing Subagent Work
**Situation:** Your coordinator has 5 research tasks. Three are high-priority (impacts immediate decision), two are low-priority (future reference). Spawning all 5 simultaneously takes 20 minutes total. Spawning high-priority first takes 12 minutes. What's the best strategy?
**Options:**
- A) Spawn high-priority first, collect results, make decision, then spawn low-priority if time permits
- B) Spawn all 5 in parallel; parallel processing is fastest overall
- C) Spawn low-priority first to warm up the system
- D) Spawn one at a time based on importance

**Correct Answer:** A
**Feedback:** Prioritization enables faster decisions when time is constrained. High-priority tasks don't wait for low-priority ones. Coordinator gets critical insights faster (12 min vs 20 min), makes decisions, then optionally gathers low-priority context.
**Theory Reference:** Domain 1.2 - Prioritized task spawning

---

## Question 99
**Title:** Session Inheritance vs Fresh Start
**Situation:** You completed a Claude Code session analyzing the authentication system (50 files). You want to continue work on a different system (payment processing). Should you resume the auth session or start fresh?
**Options:**
- A) Start fresh; auth session context is irrelevant for payment processing and would interfere
- B) Resume the auth session; all context is still useful
- C) Fork the session to keep both contexts available
- D) Merge the contexts from both sessions

**Correct Answer:** A
**Feedback:** Context is task-specific. Auth analysis context is noise for payment work. Starting fresh eliminates irrelevant context and prevents confusion. Resume sessions for the same task; start fresh for unrelated tasks.
**Theory Reference:** Domain 1.7 - Session management and task switching

---

## Question 100
**Title:** Combining Few-shot with Explicit Criteria
**Situation:** You want Claude to categorize customer feedback as positive, negative, or neutral. You provide: (1) explicit criteria ("positive: mentions benefits; negative: complaints; neutral: no clear sentiment") and (2) three labeled examples. Is this redundant?
**Options:**
- A) Yes, combining explicit criteria + targeted examples handles ambiguous cases better than either alone
- B) Redundant; use only criteria
- C) Redundant; use only examples
- D) Combining reduces clarity

**Correct Answer:** A
**Feedback:** Explicit criteria define the categories. Few-shot examples demonstrate how to apply criteria to ambiguous cases. Together, they're more effective than separately. Examples show interpretation; criteria show intent. Complementary, not redundant.
**Theory Reference:** Domain 4.1 & 4.2 - Combining criteria and few-shot examples
