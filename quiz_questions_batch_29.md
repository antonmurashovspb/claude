# Quiz Question Bank - Batch 29 (Questions 251-260)

## Question 251
**Title:** Avoiding Ambiguous Tool Descriptions Through Input Format Specification
**Situation:** Your system has two tools with overlapping purposes: `search_database` and `query_information`. Both have vague descriptions: "Retrieves information from system." Agents misroute between them 40% of the time. You want to fix this without adding multiple tools.
**Options:**
- A) Add specific input format examples to each tool's description
- B) Combine both into one tool with a `method` parameter
- C) Use few-shot examples in the system prompt
- D) Tool descriptions are sufficient; the issue is elsewhere

**Correct Answer:** A
**Feedback:** Clear input format specifications remove selection ambiguity. Instead of generic descriptions, specify exactly what each tool accepts: `search_database` → table name + column filters; `query_information` → natural language queries. Agents use input format as a routing signal—explicit examples prevent misrouting.
**Theory Reference:** Domain 2.1 - Tool descriptions with input format clarity

---

## Question 252
**Title:** Hook-Based Compliance vs Runtime Validation
**Situation:** Your payment system has a policy: block any refund request over $5,000 unless explicitly approved. You can implement this via (1) a `PreToolUse` hook that blocks the tool call, or (2) runtime validation inside the `process_refund` tool. Which is more reliable?
**Options:**
- A) `PreToolUse` hook prevents execution and guarantees compliance
- B) Runtime validation in the tool is simpler
- C) Neither; use a system prompt instruction instead
- D) Both together for defense-in-depth

**Correct Answer:** A
**Feedback:** Hooks provide architectural enforcement that prompts cannot override. A `PreToolUse` hook inspects the refund amount and blocks tool execution if over $5,000, guaranteeing compliance regardless of model reasoning. Runtime validation in the tool is a safety net, but hooks are the deterministic layer.
**Theory Reference:** Domain 1.5 - Hook placement for policy enforcement
**Feedback:** Hooks provide architectural enforcement that prompts cannot override. A `PreToolUse` hook inspects the refund amount and blocks tool execution if over $5,000, guaranteeing compliance regardless of model reasoning. Runtime validation in the tool is a safety net, but hooks are the deterministic layer.
**Theory Reference:** Domain 1.5 - Hook placement for policy enforcement

---

## Question 253
**Title:** Coordinator Context Isolation and Subagent Independence
**Situation:** A coordinator holds 80K tokens of historical context from previous research tasks. It spawns a new subagent to research an unrelated topic. Should the coordinator pass its full context history to the subagent?
**Options:**
- A) Pass only the specific task description and relevant context
- B) Use a shared memory store all agents can access
- C) Pass context incrementally as the subagent asks
- D) Yes, pass full context for consistency

**Correct Answer:** A
**Feedback:** Subagents are independent workers. The coordinator owns inter-agent orchestration, but each subagent operates with isolated, focused context. Passing 80K tokens of unrelated history wastes subagent context and creates decision-making noise. Provide only the task specification and essential background for the current task.
**Theory Reference:** Domain 1.2 - Subagent context isolation
**Feedback:** Subagents are independent workers. The coordinator owns inter-agent orchestration, but each subagent operates with isolated, focused context. Passing 80K tokens of unrelated history wastes subagent context and creates decision-making noise. Provide only the task specification and essential background for the current task.
**Theory Reference:** Domain 1.2 - Subagent context isolation

---

## Question 254
**Title:** Balancing Tool Count vs Tool Specialization for Agent Reliability
**Situation:** Your research system can use (1) two general tools (`fetch_resource` with format parameter, `process_data` with operation parameter) or (2) five specialized tools (`fetch_pdf`, `fetch_html`, `fetch_json`, `transform_structure`, `extract_text`). Which approach leads to better agent selection?
**Options:**
- A) One universal tool that handles everything via parameters
- B) Five specialized tools with clear, distinct names
- C) Three tools as a middle ground
- D) Two general tools reduce decision complexity

**Correct Answer:** B
**Feedback:** Specialized tools with clear names are more reliable than general tools with parameters. Agents must reason about parameter combinations, which is cognitively harder. Five tools with names like `fetch_pdf`, `fetch_html`, `transform_structure` are self-describing and lead to better selection than asking agents to infer parameters.
**Theory Reference:** Domain 2.1 - Tool specialization for reliability
**Feedback:** Specialized tools with clear names are more reliable than general tools with parameters. Agents must reason about parameter combinations, which is cognitively harder. Five tools with names like `fetch_pdf`, `fetch_html`, `transform_structure` are self-describing and lead to better selection than asking agents to infer parameters.
**Theory Reference:** Domain 2.1 - Tool specialization for reliability

---

## Question 255
**Title:** Session Resumption vs Fresh Start for Evolving Requirements
**Situation:** A code refactoring investigation started 4 hours ago on a legacy monolith. Midway through, the stakeholder changed requirements: instead of full refactoring, just implement a new module. The session has 120K tokens of accumulated investigation history, much of which is now irrelevant.
**Options:**
- A) Continue the session and ignore the requirement change
- B) Start a fresh session with the new requirements and a summary
- C) Fork the session to keep both investigation tracks
- D) Resume the session and update it with new requirements

**Correct Answer:** B
**Feedback:** When requirements change significantly, stale context creates noise. Resume is best when requirements are stable but results are fresh. Here, a fresh session with a summary ("Monolith structure: 5 domains, legacy auth, 12 files") and new requirements is cleaner than navigating 120K tokens of outdated analysis. The new session maintains focus on the actual goal.
**Theory Reference:** Domain 1.7 - Session state and requirement evolution
**Feedback:** When requirements change significantly, stale context creates noise. Resume is best when requirements are stable but results are fresh. Here, a fresh session with a summary ("Monolith structure: 5 domains, legacy auth, 12 files") and new requirements is cleaner than navigating 120K tokens of outdated analysis. The new session maintains focus on the actual goal.
**Theory Reference:** Domain 1.7 - Session state and requirement evolution

---

## Question 256
**Title:** Error Metadata for Smart Retry Strategies
**Situation:** Three tool failures with different error responses: (1) `{"isError": true, "errorCategory": "rate_limit", "isRetryable": true, "retryAfter": 30}`, (2) `{"isError": true, "errorCategory": "permission", "isRetryable": false}`, (3) `{"isError": true, "errorCategory": "timeout", "isRetryable": true}`. How should these be handled?
**Options:**
- A) Ignore all errors and continue
- B) Retry (1) and (3) with backoff; escalate (2)
- C) Retry (1) only; others are infrastructure issues
- D) Retry all three with exponential backoff

**Correct Answer:** B
**Feedback:** Structured error metadata enables intelligent recovery. `rate_limit` is transient—retry with the specified delay (30 seconds). `timeout` is transient—retry with backoff. `permission` is not transient; retrying will fail identically. Escalate (2) to request permissions or fix the issue, don't waste retries.
**Theory Reference:** Domain 2.2 - Error categorization and retry decisions
**Feedback:** Structured error metadata enables intelligent recovery. `rate_limit` is transient—retry with the specified delay (30 seconds). `timeout` is transient—retry with backoff. `permission` is not transient; retrying will fail identically. Escalate (2) to request permissions or fix the issue, don't waste retries.
**Theory Reference:** Domain 2.2 - Error categorization and retry decisions

---

## Question 257
**Title:** Programmatic Preconditions for Multi-step Business Logic
**Situation:** Your customer service workflow requires: (1) verify customer ID, (2) look up order, (3) process refund. The system currently relies on the system prompt: "Always verify ID before lookup, and lookup before refund." Agents skip steps 20% of the time. You want deterministic guarantees.
**Options:**
- A) Implement a separate orchestration service
- B) Improve the system prompt with stronger language
- C) Implement programmatic preconditions to block tools until dependencies complete
- D) Use few-shot examples of correct ordering

**Correct Answer:** C
**Feedback:** Prompts are probabilistic. Agents can skip steps under token pressure or context confusion. Programmatic preconditions are deterministic—the tool is literally unavailable until its dependency succeeds. This is the architectural layer that guarantees correct sequencing without relying on model reasoning.
**Theory Reference:** Domain 1.4 - Programmatic workflow enforcement
**Feedback:** Prompts are probabilistic. Agents can skip steps under token pressure or context confusion. Programmatic preconditions are deterministic—the tool is literally unavailable until its dependency succeeds. This is the architectural layer that guarantees correct sequencing without relying on model reasoning.
**Theory Reference:** Domain 1.4 - Programmatic workflow enforcement

---

## Question 258
**Title:** Context Window Optimization Through Structured Summaries
**Situation:** A subagent completes four tasks and generates 45K tokens of detailed output: analysis (12K), findings (18K), supporting evidence (10K), metadata (5K). The coordinator needs only the key findings to make a decision. Should the coordinator process all 45K tokens?
**Options:**
- A) Yes, more information improves decision quality
- B) Use vector compression to reduce output size
- C) No, have the subagent extract and return only key findings
- D) Split results across multiple coordinator requests

**Correct Answer:** C
**Feedback:** At-source summarization is more efficient than asking the coordinator to filter. The subagent should extract key findings (3-5 bullet points with supporting metrics) and return concise output. The coordinator then receives ~2K tokens instead of 45K, freeing context for deeper synthesis or handling more parallel tasks.
**Theory Reference:** Domain 1.2 - Result aggregation with context efficiency
**Feedback:** At-source summarization is more efficient than asking the coordinator to filter. The subagent should extract key findings (3-5 bullet points with supporting metrics) and return concise output. The coordinator then receives ~2K tokens instead of 45K, freeing context for deeper synthesis or handling more parallel tasks.
**Theory Reference:** Domain 1.2 - Result aggregation with context efficiency

---

## Question 259
**Title:** Tool Call Ordering via Hook Interception vs Agent Decision-Making
**Situation:** Your system has tools: `extract_data`, `validate_data`, `export_data`. The business rule is: validation must happen before export. Should you (1) rely on the agent to choose the correct order based on prompting, or (2) use a hook to block export if validation hasn't occurred?
**Options:**
- A) Trust the agent; clear prompts are sufficient
- B) Merge tools to enforce sequencing directly
- C) Implement a routing classifier to enforce ordering
- D) Use a `PostToolUse` hook to block export if validation is missing

**Correct Answer:** D
**Feedback:** When business logic requires guaranteed ordering, use hooks with state tracking. A `PostToolUse` hook can monitor which tools have been called and block `export_data` until `validate_data` completes successfully. This is deterministic enforcement that doesn't rely on agent compliance or prompt adherence.
**Theory Reference:** Domain 1.5 - Hook-based tool ordering
**Feedback:** When business logic requires guaranteed ordering, use hooks with state tracking. A `PostToolUse` hook can monitor which tools have been called and block `export_data` until `validate_data` completes successfully. This is deterministic enforcement that doesn't rely on agent compliance or prompt adherence.
**Theory Reference:** Domain 1.5 - Hook-based tool ordering

---

## Question 260
**Title:** Adaptive Decomposition vs Fixed Pipelines for Open-ended Tasks
**Situation:** Your system analyzes a security vulnerability report. The analysis might require: (1) code review (always), (2) threat modeling (maybe), (3) dependency scanning (maybe), (4) incident history lookup (maybe). Should you use a fixed four-step pipeline or adaptive decomposition?
**Options:**
- A) Fixed four-step pipeline ensures consistency
- B) Let the agent decide which steps to perform
- C) Run all steps in parallel to save time
- D) Adaptive decomposition based on initial findings

**Correct Answer:** D
**Feedback:** Fixed pipelines work for predictable workflows. Adaptive decomposition is better for open-ended tasks. Start with code review; if no threats found, skip threat modeling. If third-party code detected, trigger dependency scanning. This data-driven decomposition avoids wasted work while maintaining depth where needed.
**Theory Reference:** Domain 1.6 - Adaptive task decomposition
**Feedback:** Fixed pipelines work for predictable workflows. Adaptive decomposition is better for open-ended tasks. Start with code review; if no threats found, skip threat modeling. If third-party code detected, trigger dependency scanning. This data-driven decomposition avoids wasted work while maintaining depth where needed.
**Theory Reference:** Domain 1.6 - Adaptive task decomposition

---
