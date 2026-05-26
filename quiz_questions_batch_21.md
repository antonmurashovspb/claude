# Quiz Question Bank - Batch 21 (Questions 201-210)

## Question 201
**Title:** Avoiding Runaway Iteration in Agentic Loops
**Situation:** Your agent performs document analysis with tool calls for extraction, summarization, and validation. You observe that sometimes the agent enters an infinite loop calling tools repeatedly without reaching a decision. Should you rely on iteration limits or model behavior?
**Options:**
- A) Monitor `stop_reason` to detect natural completion at `"end_turn"`
- B) Set a hard iteration limit to 8 calls to constrain execution
- C) Implement iteration limits alongside monitoring `stop_reason`
- D) Add a timeout mechanism to forcefully halt execution

**Correct Answer:** A
**Feedback:** `stop_reason = "end_turn"` indicates the model has completed its reasoning. Respecting this signal allows complex problems to finish naturally. Hard iteration limits prematurely stop reasoning; `stop_reason` is the reliable completion signal.
**Theory Reference:** Domain 1.1 - Agent loop control via stop_reason

---

## Question 202
**Title:** Decomposing Tasks for Effective Multi-agent Systems
**Situation:** Your coordinator handles customer support: classify issue type, research policy, draft response, escalate if needed. You can execute all steps in one prompt or delegate to specialized subagents. Which approach scales better?
**Options:**
- A) One large prompt allows all steps in single request
- B) Delegate to subagents; each focuses without dilution
- C) Combine classification and research; separate drafting
- D) Use single subagent to handle all steps sequentially

**Correct Answer:** B
**Feedback:** Specialized subagents per task reduce context and enable parallel execution. Coordinator orchestrates and synthesizes. One large prompt dilutes attention across unrelated tasks. Delegation improves quality and observability.
**Theory Reference:** Domain 1.2 - Multi-agent orchestration

---

## Question 203
**Title:** Tool Naming Precision for Reliable Agent Selection
**Situation:** Your platform has tools: `get_order_info`, `get_customer_history`, and `get_account_status`. During testing, the agent misroutes queries about customer lifetime value to the wrong tool due to unclear naming.
**Options:**
- A) Add few-shot examples to improve tool selection
- B) Merge similar tools to reduce agent confusion
- C) Use specific, action-oriented names to clarify purpose
- D) Tool names are secondary; improve descriptions instead

**Correct Answer:** C
**Feedback:** Tool names are the first signal agents see. Specific names like `get_customer_lifetime_value` self-describe purpose. Vague names require agents to reason about descriptions, which fails. Clear naming prevents misrouting.
**Theory Reference:** Domain 2.1 - Tool naming and clarity

---

## Question 204
**Title:** Preconditions vs Prompts for Critical Workflows
**Situation:** Your payment system requires: verify customer identity first, then approve payment amount, then process transaction. The agent sometimes skips verification and goes directly to processing. Should you use prompts or code-level enforcement?
**Options:**
- A) Use both prompt and precondition for extra safety
- B) Require manual approval instead of automation
- C) Add detailed system prompt reminding agent to verify
- D) Implement a precondition hook blocking payment until verified

**Correct Answer:** D
**Feedback:** Preconditions provide deterministic guarantees. A hook can block `process_payment` until `verify_identity` has succeeded. Prompts provide only probabilistic compliance; critical workflows need code-level guarantees.
**Theory Reference:** Domain 1.4 - Multi-step workflow enforcement

---

## Question 205
**Title:** Data Normalization Using PostToolUse Hooks
**Situation:** Your agent aggregates data from three sources: API A returns Unix timestamps, API B returns ISO 8601 format, API C returns human-readable dates. The agent struggles to compare times. Where should normalization happen?
**Options:**
- A) In the system prompt with conversion instructions
- B) In a separate normalization tool called after each data retrieval
- C) In a PostToolUse hook that intercepts results before agent processing
- D) Have each API wrapper normalize before returning

**Correct Answer:** C
**Feedback:** PostToolUse hooks intercept tool results before the agent processes them. This ensures all data appears in consistent format—perfect for normalization. The agent sees uniform timestamps without special handling.
**Theory Reference:** Domain 1.5 - PostToolUse hooks for data normalization

---

## Question 206
**Title:** Specialized Tools vs General-purpose with Parameters
**Situation:** You're designing tools for report generation. Option 1: specialized tools (`generate_pdf_report`, `generate_html_report`, `generate_csv_report`). Option 2: single `generate_report` with a format parameter. Which is more reliable?
**Options:**
- A) Both equally reliable if descriptions are detailed
- B) Specialized tools because agents reliably select distinct tool names
- C) Specialized tools are less maintainable due to code duplication
- D) General tool with parameters because it requires less code

**Correct Answer:** B
**Feedback:** Agents struggle to reason about parameter combinations in general tools. Specialized, distinct tool names (`generate_pdf_report` vs `generate_html_report`) lead to reliable selection. Tool specialization increases clarity and reduces misrouting.
**Theory Reference:** Domain 2.3 - Tool specialization

---

## Question 207
**Title:** Error Handling with Structured Retry Information
**Situation:** A subagent tries to fetch user data but receives: `{"isError": true, "errorCategory": "rate_limit", "isRetryable": true}`. The coordinator gets this error with no context about failed query or retry timing.
**Options:**
- A) Log the error and continue without the data
- B) Escalate the issue to a human reviewer  
- C) Include query, results, and retry timing in response
- D) Immediately retry since it is retryable

**Correct Answer:** C
**Feedback:** Structured error context enables intelligent recovery. The coordinator can retry with modified query, try alternatives, or use partial results. Without context, retry decisions are blind.
**Theory Reference:** Domain 2.2 - Structured error responses

---

## Question 208
**Title:** Context Isolation in Multi-agent Delegation
**Situation:** Your coordinator has 40K tokens of discovery context from internal analysis. It now delegates to a research subagent. Does the subagent automatically inherit this context?
**Options:**
- A) Context is shared through a central database, not in prompts
- B) Subagents inherit partial context depending on relevance
- C) Subagents inherit all coordinator context automatically for consistency
- D) Subagents have isolated context; all information must be explicitly passed

**Correct Answer:** D
**Feedback:** Subagents have isolated context; they don't inherit coordinator history. All required information must be explicitly included in the subagent's prompt. This isolation protects focus and clarity.
**Theory Reference:** Domain 1.3 - Context passing between agents

---

## Question 209
**Title:** Session Forking for Exploration and Comparison
**Situation:** You're exploring two architectural approaches for code refactoring. Approach X modularizes by feature; Approach Y by technical layer. After deep analysis of X, you want to explore Y while preserving X findings.
**Options:**
- A) Fork the session to explore Y as an independent branch from X
- B) Run both approaches in separate agents in parallel
- C) Start a fresh session for Approach Y; reference your X findings manually
- D) Resume the session to keep context after finishing Y analysis

**Correct Answer:** A
**Feedback:** fork_session creates independent branches from shared context. You explore Approach Y without losing Approach X analysis. Later compare both branches from the same starting point.
**Theory Reference:** Domain 1.7 - Session forking

---

## Question 210
**Title:** Restricting Skill Access with Allowed Tools
**Situation:** You create a `/security-audit` skill that should perform only analysis—checking code for vulnerabilities, identifying risky patterns. You prohibit modification tools. How do you enforce this restriction?
**Options:**
- A) Create separate skills: one for analysis, one for modifications
- B) Use the `allowed-tools` field in SKILL.md to restrict tool access
- C) Require manual review of each skill execution
- D) Strengthen system prompt to discourage modifications

**Correct Answer:** B
**Feedback:** The `allowed-tools` field in SKILL.md is an architectural constraint that restricts available tools. Specify only analysis tools in this list. System prompts cannot override architectural constraints.
**Theory Reference:** Domain 3.2 - Skills with allowed-tools constraints

---
