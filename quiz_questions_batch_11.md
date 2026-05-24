# Quiz Question Bank - Batch 11 (Questions 81-90)

## Question 81
**Title:** Coordinator Delegation vs Direct Agent Execution
**Situation:** Your system needs to analyze three different customer data sources (CRM, purchase history, support tickets) and synthesize findings. Should you use a single agent with three tools or a coordinator spawning three subagents?
**Options:**
- A) Use a coordinator with three specialized subagents, each owning one data source, aggregating results for synthesis
- B) Use one agent with all three tools; it will naturally choose the best sequence without overhead of subagent spawning
- C) Alternate between coordinator and direct execution depending on problem complexity at runtime
- D) Use one agent with database queries since tools add latency compared to direct queries

**Correct Answer:** A
**Feedback:** Coordinator-subagent architecture provides error isolation, clear task boundaries, and allows parallel execution of independent research tasks. A single agent with three tools dilutes focus and complicates observability. Subagents own isolated contexts for each data source, then the coordinator synthesizes—this matches the hub-and-spoke pattern.
**Theory Reference:** Domain 1.2 - Orchestrating Multi-agent Systems (Coordinator–Subagent)

---

## Question 82
**Title:** Feedback Loop Design for Semantic Validation
**Situation:** An extraction tool correctly returns valid JSON (schema-conformant), but 18% of extracted invoice totals don't reconcile with line items. Retrying with the same prompt yields the same errors. What should you change?
**Options:**
- A) Increase `max_tokens` to give the model more space for calculations
- B) Lower temperature to make the model more deterministic
- C) Retry with the document, incorrect extraction, and validation error details
- D) Switch to a model with stronger mathematical abilities

**Correct Answer:** C
**Feedback:** Schema validation catches syntax errors; semantic errors (inconsistent totals) require targeted feedback. When retrying, include the extracted values, the validation failure details, and the source document. This gives the model concrete error information to correct, rather than hoping `max_tokens` or temperature will solve it.
**Theory Reference:** Domain 4.4 - Implementing Validation, Retries, and Feedback Loops for Extraction Quality

---

## Question 83
**Title:** Tool-Choice Configuration for Guaranteed Structured Output
**Situation:** Your pipeline extracts metadata from documents with different formats (PDFs, CSVs, web pages). You have three extraction tools with different JSON schemas. The downstream processor requires structured output—it cannot handle free-text responses. How should you configure the agent?
**Options:**
- A) Use `tool_choice: {"type": "auto"}` and instruct the system prompt to always return JSON, not text
- B) Use `tool_choice: {"type": "any"}` so the agent must call one of the extraction tools
- C) Implement a text-to-JSON parser that processes the agent's text response afterward
- D) Use `tool_choice: {"type": "tool", "name": "extract_generic"}` with a unified schema for all formats

**Correct Answer:** B
**Feedback:** `tool_choice: "any"` guarantees a tool call—the agent must invoke one of the available tools. With `"auto"`, the model can ignore tools and return text instead. Since your pipeline requires structured data and you have three viable extraction schemas, `"any"` lets the model choose the most suitable tool while guaranteeing structured output.
**Theory Reference:** Domain 4.3 - Enforcing Structured Output with `tool_use` and JSON Schemas

---

## Question 84
**Title:** Context Passing in Multi-turn Coordinator Workflows
**Situation:** A coordinator researches a customer complaint using three subagents: (1) purchase history, (2) support tickets, (3) refund eligibility. After subagent 1 returns results, the coordinator invokes subagent 2. Does subagent 2 automatically see subagent 1's output?
**Options:**
- A) Yes, subagents inherit the coordinator's context automatically always
- B) Only if results are stored in a shared database accessible to subagents
- C) Only when subagents share the same session ID identically
- D) No, context is isolated; coordinator must pass subagent 1's output explicitly

**Correct Answer:** D
**Feedback:** Subagents operate with isolated context—they do not inherit the coordinator's history. If subagent 2 needs findings from subagent 1, the coordinator must include those findings explicitly in the Task prompt to subagent 2, in a structured format (e.g., JSON).
**Theory Reference:** Domain 1.3 - Configuring Subagent Calls, Context Passing, and Spawning

---

## Question 85
**Title:** Transient vs Non-Retryable Error Handling in Tool Design
**Situation:** Your MCP tool returns two error types: (1) `{"errorCategory": "timeout", "isRetryable": true}` (database temporarily unreachable) and (2) `{"errorCategory": "invalid_format", "isRetryable": false}` (malformed input). Both occur in the same workflow. How should the agent handle each?
**Options:**
- A) Escalate both immediately; tool errors always indicate system problems
- B) Retry both with exponential backoff; retrying transient issues is always safe
- C) Retry (1) with backoff; for (2), log the error and escalate; don't retry bad input
- D) Store (1) in cache for later, but ignore (2) completely

**Correct Answer:** C
**Feedback:** Structured error metadata enables smart handling. Transient errors (timeouts, temporarily unavailable) benefit from retry logic with exponential backoff. Non-retryable errors (validation, malformed input) indicate the request itself is invalid—retrying with the same input will always fail. Log the error and escalate or adjust the request.
**Theory Reference:** Domain 2.2 - Implementing Structured Error Responses for MCP Tools

---

## Question 86
**Title:** PostToolUse Hooks vs System Prompt for Policy Enforcement
**Situation:** Your refund system has a policy: no refunds above $1000 without manager approval. The system prompt warns about this, but occasional violations occur in production. What is the most reliable enforcement method?
**Options:**
- A) Use `tool_choice` to force selection of an escalation tool when the amount exceeds $1000
- B) Implement a review step after the refund tool executes, reverting transactions that violate the policy
- C) Strengthen the system prompt with more examples of escalation scenarios and lower the temperature to increase consistency
- D) Add a PostToolUse hook that intercepts refund tool calls, blocks those exceeding $1000, and redirects to an escalation tool

**Correct Answer:** D
**Feedback:** System prompts provide probabilistic guidance (the model usually follows, but not always). Hooks provide deterministic guarantees—a PostToolUse hook can prevent the prohibited action **before** execution. This is the architectural approach for business-critical policies with financial or legal consequences.
**Theory Reference:** Domain 1.5 - Agent SDK Hooks for Intercepting Tool Calls and Normalizing Data

---

## Question 87
**Title:** Tool Specialization and Selection Reliability
**Situation:** Your system has two approaches: (1) four specialized tools (`fetch_pdf`, `fetch_html`, `fetch_json`, `fetch_xml`) or (2) one general tool `fetch_content` with a `format` parameter. Agents frequently misroute work. Which design is more reliable?
**Options:**
- A) Four specialized tools are more reliable; agents can identify the right tool by name without reasoning about parameters
- B) One general tool is better; it simplifies the agent's decision space by having fewer choices
- C) Reliability depends on the model version; newer models handle parameters better than older ones
- D) Both are equally reliable; agents handle parameters as well as they handle multiple tools

**Correct Answer:** A
**Feedback:** Specialized tools are more reliable than general tools with parameters. Agents struggle to reason about parameter combinations; explicit tool names are the primary signal in tool selection. Four distinct tools with clear names (`fetch_pdf`, `fetch_html`) lead to better accuracy than one generic tool where the agent must choose a parameter value.
**Theory Reference:** Domain 2.3 - Allocating Tools Across Agents and Configuring `tool_choice`

---

## Question 88
**Title:** Prompt Chaining vs Adaptive Task Decomposition
**Situation:** Your system needs to (1) extract key facts from a document, (2) validate those facts against external sources, (3) assess financial risk based on facts. The steps are always the same. Should you use a fixed prompt sequence or adaptive decomposition?
**Options:**
- A) Use a single comprehensive prompt covering all three steps to minimize API calls
- B) Use fixed prompt chaining; each step is focused without attention dilution, and the sequence never varies
- C) Combine them: fixed chaining for initial extraction, then adaptive decomposition for validation and risk assessment
- D) Use adaptive decomposition; it allows the agent to skip unnecessary steps and optimize dynamically

**Correct Answer:** B
**Feedback:** When steps are predictable and always occur in the same order, fixed prompt chaining is ideal. Each stage receives focused prompting without competing objectives. A single comprehensive prompt dilutes attention. Adaptive decomposition is better for open-ended investigations where intermediate results determine next steps.
**Theory Reference:** Domain 1.6 - Task Decomposition Strategies for Complex Workflows

---

## Question 89
**Title:** Planning Mode vs Direct Execution for Architectural Changes
**Situation:** You need to refactor a monolithic authentication system across 35 files with deep dependencies. Breaking service boundaries would have significant impact. Which approach is most appropriate?
**Options:**
- A) Use direct execution; git allows reverting errors if they occur later
- B) Use incremental changes: execute one, run tests, then continue with next
- C) Use Planning Mode to explore dependencies and surface architectural impact first
- D) Request manual design review from the team before starting work

**Correct Answer:** C
**Feedback:** Planning Mode is designed for complex, high-impact changes. It explores the codebase before making modifications, identifies service boundaries, dependency graphs, and architectural implications. This is exactly the scenario where Planning Mode prevents expensive rework. Direct execution risks missing critical dependencies.
**Theory Reference:** Domain 3.4 - Deciding When to Use Planning Mode vs Direct Execution

---

## Question 90
**Title:** Lost-in-the-Middle Effect and Information Preservation
**Situation:** A coordinator collects findings from five subagents (each returns 2-3 key points plus supporting details). The coordinator concatenates all results and asks a synthesis agent to produce a summary. Key findings from subagent 3 (middle of the list) are missing from the final output. What's a better approach?
**Options:**
- A) Extract key findings into a dedicated "Case Facts" section at the start, separate from supporting details
- B) Increase `max_tokens` so synthesis agent has more room to process the long input properly
- C) Reorder subagent results so subagent 3 comes first or last instead of in the middle position
- D) Use vector database to store findings and retrieve only the most relevant ones for synthesis work

**Correct Answer:** A
**Feedback:** Models reliably process information at the start and end of long inputs (lost-in-the-middle effect). Extract transactional facts from subagent findings into a dedicated "Case Facts" section placed at the beginning. Then include supporting details separately. This structure ensures critical information is not lost due to context ordering.
**Theory Reference:** Domain 5.1 - Managing Conversation Context to Preserve Critical Information

---
