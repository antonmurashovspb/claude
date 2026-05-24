# Quiz Question Bank - Batch 10 (Questions 71-80)

## Question 71
**Title:** Full History in the Agent Loop
**Situation:** To save tokens, an engineer sends Claude only the latest user message after each tool call. The agent starts forgetting prior tool results and makes inconsistent decisions in later steps. What is the best fix?
**Options:**
- A) Send the full conversation history on every request, including assistant turns and tool results
- B) Keep only the system prompt and most recent tool result in each new request
- C) Store prior turns in a database and send just a session ID to Claude
- D) Summarize the conversation after every tool call and discard the original turns

**Correct Answer:** A
**Feedback:** Claude does not persist state between API calls. The reliable pattern is to resend the full history, including assistant outputs and tool results, so the model can continue the loop with complete context.
**Theory Reference:** Domain 5.1 & Chapter 1.2 - Sending full conversation history

---

## Question 72
**Title:** Guaranteeing Structured Output
**Situation:** You have two extraction tools with JSON schemas, and the workflow must always return structured data rather than a free-text answer. Which configuration is most appropriate?
**Options:**
- A) Keep `tool_choice: {"type": "auto"}` and instruct the model to prefer JSON responses
- B) Force one specific extraction tool even when different documents require different schemas
- C) Use `tool_choice: {"type": "any"}` so Claude must call one available tool
- D) Increase `max_tokens` so the model has enough room to format valid JSON

**Correct Answer:** C
**Feedback:** `tool_choice: "any"` guarantees a tool call while still letting the model choose the most suitable schema among the available tools. Prompt instructions alone cannot guarantee structured output.
**Theory Reference:** Domain 4.3 & Chapter 2.3 - Using `tool_choice` for structured output

---

## Question 73
**Title:** Context Passing to Subagents
**Situation:** A coordinator spawns a document-analysis subagent with the prompt “Review the report and extract risks.” The subagent misses important facts from earlier web research because that context was never included. What should the coordinator do?
**Options:**
- A) Pass the report, prior search results, and output requirements explicitly in the Task prompt
- B) Assume subagents inherit the coordinator session and only add a clearer system prompt
- C) Ask the subagent to request missing context later if it needs more background
- D) Rely on shared memory between subagents so research findings appear automatically

**Correct Answer:** A
**Feedback:** Subagents have isolated context. If the coordinator wants the subagent to use prior findings, it must include that context directly in the Task prompt in a structured form.
**Theory Reference:** Domain 1.3 & Chapter 3.4 - Explicit context passing for subagents

---

## Question 74
**Title:** Deterministic Refund Compliance
**Situation:** A support agent must never issue refunds above $500 without escalation. Prompt instructions reduce mistakes, but occasional violations still occur in production. What is the most reliable solution?
**Options:**
- A) Add stronger wording in the system prompt and more examples of escalation cases
- B) Lower the model temperature so it follows the refund rule more consistently
- C) Add a review agent that checks refund decisions after the tool call completes
- D) Use a `PreToolUse` hook to block large refunds and redirect them to escalation

**Correct Answer:** D
**Feedback:** When the rule has financial consequences, you need deterministic enforcement. A `PreToolUse` hook prevents the prohibited action before execution, unlike prompts that only influence behavior probabilistically.
**Theory Reference:** Domain 1.5 & Chapter 3.5 - Hooks for deterministic policy enforcement

---

## Question 75
**Title:** Path-specific Test Conventions
**Situation:** Test files are scattered across many packages, but the team wants Claude Code to apply the same testing conventions automatically whenever it edits a test file. What setup fits best?
**Options:**
- A) Put all testing guidance in the root `CLAUDE.md` and rely on Claude to infer scope
- B) Create `.claude/rules/` files with `paths` globs that match test files across the repo
- C) Add a directory-level `CLAUDE.md` to every package and duplicate the testing rules
- D) Store the testing guidance in a skill and require developers to invoke it manually

**Correct Answer:** B
**Feedback:** `.claude/rules/` with `paths` is designed for conventions that apply across many locations, such as `**/*.test.ts`. It loads only when matching files are edited, saving context and keeping behavior consistent.
**Theory Reference:** Domain 3.3 & Chapter 5.3 - Path-scoped rules with glob patterns

---

## Question 76
**Title:** Semantic Errors After Schema Validation
**Situation:** An extraction tool uses a JSON schema, so outputs are always valid JSON. However, invoice totals still do not reconcile because values are placed in the wrong fields. What should you add next?
**Options:**
- A) Tighten the schema with more required fields so the model has fewer choices
- B) Force the same extraction tool twice and keep the first result that looks cleaner
- C) Add validation checks and retry with concrete error feedback about the mismatch
- D) Increase the context window so the model can inspect the invoice more carefully

**Correct Answer:** C
**Feedback:** JSON schemas eliminate syntax errors, not semantic mistakes. You need downstream validation and targeted retry feedback so the model can correct inconsistencies like totals that do not add up.
**Theory Reference:** Domain 4.4 & Chapter 2.5 - Validation and retry loops for semantic errors

---

## Question 77
**Title:** MCP Tool vs Built-in Tool Preference
**Situation:** Your custom MCP tool can search an internal catalog with tagged metadata, but agents keep using Grep instead. The MCP tool description only says “Search project data.” How should you improve selection reliability?
**Options:**
- A) Rewrite the MCP description to highlight its unique metadata and advantages over built-in tools
- B) Remove Grep from the environment so the agent has no competing search option available
- C) Add a note in the user prompt saying internal tools are usually better than built-ins
- D) Rename the MCP tool to `grep_internal` so it resembles the built-in search command

**Correct Answer:** A
**Feedback:** Tool descriptions are the primary selection signal. If an MCP tool overlaps with built-ins, its description must explain what unique data or advantages it provides so the model can distinguish it reliably.
**Theory Reference:** Domain 2.1 & Chapter 2.2 - Tool descriptions and built-in vs MCP differentiation

---

## Question 78
**Title:** Batch API for Agentic Workflows
**Situation:** A team wants to move a nightly document workflow to the Message Batches API. Each document often requires tool calls, retries, and follow-up reasoning before completion. What is the best architectural choice?
**Options:**
- A) Use batches anyway and split each document into many separate one-step batch requests
- B) Submit the first prompt in a batch, then poll and continue the loop inside later batches
- C) Keep the workflow in batches but replace tool calls with longer prompts and more examples
- D) Use the synchronous agentic loop for this workflow and reserve batches for one-shot jobs

**Correct Answer:** D
**Feedback:** The Message Batches API is good for non-blocking one-shot work, but it does not support a multi-turn tool-calling loop inside a single request. Agentic workflows with follow-up steps should stay synchronous.
**Theory Reference:** Domain 4.5 - Batch processing limits for multi-turn workflows

---

## Question 79
**Title:** Preserving Provenance in Conflicting Findings
**Situation:** Two credible subagents return different market-growth figures from different sources and publication dates. The coordinator must prepare a synthesis without losing traceability. What output structure is best?
**Options:**
- A) Keep only the newer figure because publication date is the strongest signal of correctness
- B) Preserve claim-to-source mappings and annotate the conflict with dates and attribution
- C) Average the two figures and mark the result as a balanced estimate for synthesis
- D) Omit the disputed figures and summarize only the points where all sources agree

**Correct Answer:** B
**Feedback:** When sources conflict, the correct move is to preserve provenance rather than arbitrarily choosing or collapsing values. Attribution and dates let the coordinator or user reconcile the difference transparently.
**Theory Reference:** Domain 5.6 - Preserving provenance and annotating conflicts

---

## Question 80
**Title:** Mitigating Lost-in-the-middle During Synthesis
**Situation:** Aggregated findings for a synthesis step reach 60K tokens. The model consistently uses details from the start and end of the input but misses critical points buried in the middle. How should you restructure the input?
**Options:**
- A) Rotate which subagent output appears first on each run so all sources get equal exposure
- B) Keep the same order but instruct the model to read carefully through the middle sections
- C) Put a key-findings summary first and organize the rest with clear section headings
- D) Append all raw outputs unchanged so no potentially useful details are lost in compression

**Correct Answer:** C
**Feedback:** This directly addresses the lost-in-the-middle effect. Putting key findings at the start and organizing detailed sections with headings makes critical information easier for the model to locate and retain.
**Theory Reference:** Domain 5.1 - Lost-in-the-middle mitigation through structured aggregation
