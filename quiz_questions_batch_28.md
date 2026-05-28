# Quiz Question Bank - Batch 28 (Questions 271-280)

## Question 271
**Title:** Maximum Token Constraints in Coordinator-Subagent Communication
**Situation:** Your coordinator delegates five tasks to subagents. Task 1 returns analysis of 60 files (120K tokens). Task 2 returns API documentation research (45K tokens). Task 3 returns database schema review (35K tokens). Tasks 4 and 5 return smaller results (10K tokens each). The coordinator now has 220K tokens of subagent output to synthesize into a report. What should the coordinator do?
**Options:**
- A) Request each subagent provide key findings instead of raw data
- B) Process all 220K tokens; the context window is large enough
- C) Split results across multiple requests to parallelize processing
- D) Use automated compression before presenting to coordinator

**Correct Answer:** A
**Feedback:** Rather than overwhelming the coordinator with raw subagent outputs, request structured summaries from each subagent. This reduces context to ~15-20K tokens of high-quality findings. The coordinator synthesizes from concise summaries instead of raw data, maintaining quality while improving efficiency.
**Theory Reference:** Domain 1.2 - Coordinator result aggregation strategy

---

## Question 272
**Title:** Distinguishing Tool Use Signals in Agent Loops
**Situation:** Your agent calls a document retrieval tool and receives a response. The API response includes `"stop_reason": "tool_use"`. What does this signal mean?
**Options:**
- A) The tool execution failed completely and cannot continue
- B) The model wants to call another tool; continue the loop
- C) The model rejected the tool's response and stopped
- D) The tool request was incorrectly formatted

**Correct Answer:** B
**Feedback:** `stop_reason: "tool_use"` indicates the model executed a tool and needs the next action. The correct flow is: append tool results to context, send the updated conversation back to Claude, and continue the agentic loop. The model will then decide the next action based on the results.
**Theory Reference:** Domain 1.1 - Agent loop control via stop_reason

---

## Question 273
**Title:** Specialization Principle for Complex Tool Ecosystems
**Situation:** Your platform has 35+ tools. You notice that agents frequently mix up `query_customer_database`, `query_order_database`, and `query_analytics_database`. All three have detailed descriptions, but agents struggle with tool selection. What is the root cause?
**Options:**
- A) Descriptions need better differentiation and clarity
- B) Agent lacks examples of proper tool usage patterns
- C) Overlapping names create confusion; use specialized names
- D) Add a routing classifier for tool selection

**Correct Answer:** C
**Feedback:** Tool names are the first signal agents encounter. Overlapping names like `query_*_database` create inherent ambiguity. Renaming to specialized actions (`fetch_customer_profile`, `lookup_order_history`, `fetch_analytics_metrics`) makes selection explicit. Specialization reduces confusion more effectively than descriptions or classifiers.
**Theory Reference:** Domain 2.1 - Tool naming and description clarity

---

## Question 274
**Title:** Structured Error Responses for Intelligent Error Handling
**Situation:** A tool returns the error: "Failed to complete request." Your coordinator receives this but cannot determine if it should retry, modify the request, or escalate to a human. How should the tool be designed to enable intelligent recovery?
**Options:**
- A) Add more detailed error messages to help explain failure
- B) Return structured error metadata with category and retryability
- C) Have the tool retry transient failures automatically itself
- D) Include a human-readable explanation of what went wrong

**Correct Answer:** B
**Feedback:** Structured error responses with `errorCategory` (transient vs non-retryable), `isRetryable` boolean, and failed request details enable intelligent recovery. The coordinator can retry timeouts, modify malformed requests for validation errors, or escalate permissions failures—all based on metadata rather than parsing error text.
**Theory Reference:** Domain 2.2 - Structured error responses for intelligent recovery

---

## Question 275
**Title:** System Prompt and Tool Association Bias
**Situation:** Your support system has tools `get_customer`, `lookup_order`, and `escalate_to_human`. Your system prompt states: "Always verify the customer identity first before any other action." Agents now overuse `get_customer` even on questions that don't require customer identity. What is the problem?
**Options:**
- A) Strong prompt directives create tool association bias
- B) The agent lacks examples of when NOT to use this tool
- C) The tool description makes it seem more appealing
- D) This is expected; the system prompt is working correctly

**Correct Answer:** A
**Feedback:** System prompts with strong directives (e.g., "Always verify...") can create tool association bias. The model overweights this instruction and misapplies the tool on edge cases or irrelevant queries. Context-specific guidance ("When handling refunds, verify identity first...") is more nuanced than blanket rules.
**Theory Reference:** Domain 1.5 & Chapter 1.4 - System prompt wording and tool selection

---

## Question 276
**Title:** Enforcing Critical Workflow Dependencies Deterministically
**Situation:** Your financial system requires: (1) customer verification, (2) fraud check, (3) refund processing, in that exact sequence. A system prompt states "follow this order," but in edge cases under high token pressure, the model occasionally skips verification. How do you guarantee the sequence?
**Options:**
- A) Use code-level preconditions to block downstream steps
- B) Add more examples to the system prompt instructions
- C) Use a hardcoded state machine instead of prompts
- D) Have manual review of high-value transactions first

**Correct Answer:** A
**Feedback:** Prompts are probabilistic—the model might deviate under pressure or complex scenarios. Programmatic preconditions are deterministic: the refund tool is literally unavailable until prior steps complete. This provides guaranteed compliance without relying on model interpretation.
**Theory Reference:** Domain 1.4 - Programmatic enforcement vs prompt guidance

---

## Question 277
**Title:** Hook Placement for Response Transformation
**Situation:** Your system integrates three external APIs: API A returns timestamps as Unix epoch, API B returns ISO 8601, API C returns human-readable strings. You want to normalize all timestamps to ISO 8601 before the agent processes results. Should you use PreToolUse or PostToolUse hooks?
**Options:**
- A) PreToolUse hook to standardize input before tools execute
- B) Use a wrapper layer instead; hooks are not suitable for this
- C) PostToolUse hook to normalize responses after execution
- D) Both hooks together for redundancy and safety

**Correct Answer:** C
**Feedback:** PostToolUse hooks intercept tool results after execution, before the model processes them. This is the correct place for response normalization—the agent sees consistent data format across all tools. PreToolUse is for request transformation (modifying what you send to tools), not response transformation.
**Theory Reference:** Domain 1.5 - Hook placement for data normalization

---

## Question 278
**Title:** Explicit Context Passing to Subagents
**Situation:** Your coordinator has spent three turns researching a complex topic (history, market data, competitor analysis totaling 50K tokens). It now needs to delegate a synthesis task to a subagent. Should the coordinator assume the subagent inherits this context automatically?
**Options:**
- A) Yes, if the session is marked "shared" with the subagent
- B) Partially; system prompts are inherited but not history
- C) Yes, subagents inherit all coordinator conversation context
- D) No, you must explicitly pass context in the prompt

**Correct Answer:** D
**Feedback:** Subagents have completely isolated context. They do not automatically inherit the coordinator's conversation history or prior findings. All required context must be explicitly passed in the subagent prompt. This design ensures subagents are truly independent and prevents accidental context leakage.
**Theory Reference:** Domain 1.3 - Subagent context isolation and explicit passing

---

## Question 279
**Title:** Retry Strategy for Different Error Types
**Situation:** Your system encounters two errors: (1) `"timeout": true, "errorCategory": "transient"` from an overloaded service, and (2) `"validation_error": true, "errorCategory": "non-retryable"` from malformed input. What is the appropriate recovery strategy?
**Options:**
- A) Retry both with backoff; retrying cannot make outcomes worse
- B) Escalate both immediately to avoid wasting resources
- C) Retry validation errors only; timeouts usually resolve by themselves
- D) Retry transients with backoff; fix or escalate non-retryable errors

**Correct Answer:** D
**Feedback:** Error categorization enables smart recovery decisions. Transient errors (timeouts, temporarily unavailable) benefit from retry with backoff—the service may recover. Non-retryable errors (validation, permissions) cannot be fixed by retrying. Instead, modify the request or escalate. This approach optimizes both resource usage and success rates.
**Theory Reference:** Domain 2.2 & 5.3 - Error categorization and retry decisions

---

## Question 280
**Title:** Multi-stage Prompt Chaining vs Unified Prompting
**Situation:** Your system must (1) parse raw unstructured data, (2) identify key entities, (3) validate data consistency, (4) generate a summary report. Should this be one comprehensive prompt or four sequential prompts?
**Options:**
- A) One comprehensive prompt to minimize API calls and token usage
- B) Four sequential prompts for deeper focused analysis at each stage
- C) Use a routing classifier to pick between unified or sequential
- D) Two prompts: parsing+identification, then validation+summary

**Correct Answer:** B
**Feedback:** Prompt chaining (sequential, focused prompts) produces higher quality than one unified prompt. When a prompt juggles four distinct stages, attention dilutes and mistakes compound. Separate prompts (parse → identify → validate → summarize) allow each stage to focus deeply. The quality improvement justifies the higher API call count.
**Theory Reference:** Domain 1.6 - Prompt chaining for complex workflows
