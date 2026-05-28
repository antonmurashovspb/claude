# Quiz Question Bank - Batch 26 (Questions 251-260)

## Question 251
**Title:** Token Budget Allocation Across Multi-Turn Conversations
**Situation:** Your coordinator spawns multiple subagents in sequence: Subagent 1 uses 45K tokens, Subagent 2 uses 38K tokens, Subagent 3 uses 42K tokens. The total coordinator context is 200K tokens. The coordinator needs to summarize all three results into an executive summary. How should token allocation be managed?
**Options:**
- A) Request each subagent to provide structured summaries (3-5 key points) before returning results to the coordinator
- B) Store all raw results (125K tokens) and rely on the coordinator to filter during synthesis
- C) Split results across multiple coordinator requests to accommodate all data
- D) Limit each subagent to 25K tokens regardless of task complexity

**Correct Answer:** A
**Feedback:** Subagents should produce structured summaries (3-5 bullet points each) rather than raw 40K-token outputs. This reduces coordinator context consumption from 125K to approximately 15-20K tokens, enabling efficient synthesis while preserving key information.
**Theory Reference:** Domain 1.2 - Coordinator result aggregation with context efficiency

---

## Question 252
**Title:** Tool Naming Precision in Overlapping Domains
**Situation:** Your data analytics system has overlapping tools: `analyze_metrics`, `evaluate_data`, and `assess_performance`. Agents frequently route queries inconsistently, choosing different tools for identical analysis tasks. Tool descriptions are detailed but ambiguous.
**Options:**
- A) Rename to action-specific names: `calculate_revenue_metrics`, `evaluate_data_quality`, `assess_performance_trends` to eliminate ambiguity
- B) Merge into a single tool with a `category` parameter
- C) Add more examples to the system prompt
- D) Implement a classifier to route requests

**Correct Answer:** A
**Feedback:** Tool names are the first signal agents encounter. Generic names like `analyze_metrics` and `evaluate_data` create routing confusion. Specific, action-oriented names (`calculate_revenue_metrics`, `evaluate_data_quality`, `assess_performance_trends`) self-describe their purpose and prevent misrouting.
**Theory Reference:** Domain 2.1 - Tool naming clarity for agent selection

---

## Question 253
**Title:** Least Privilege Tool Access in Subagent Design
**Situation:** Your platform has 50+ tools across multiple domains (finance, analytics, HR, security, customer service). A subagent tasked with customer satisfaction analysis needs tools from customer service and analytics domains only. Current setup gives it access to all 50 tools.
**Options:**
- A) Restrict to 8-10 tools from customer service and analytics domains per least privilege principle
- B) Give all 50 tools; subagent can choose what it needs and flexibility outweighs context cost
- C) Create 25 tools (customer + analytics subset) as a middle ground
- D) Give 20 tools and let the subagent discover relevant ones

**Correct Answer:** A
**Feedback:** Least privilege restricts subagents to tools they need. Broad access (50 tools) wastes context, increases token consumption, and reduces tool selection reliability. Scoped access (8-10 relevant tools) improves accuracy and efficiency without sacrificing capability for the task.
**Theory Reference:** Domain 2.3 - Tool allocation and least privilege

---

## Question 254
**Title:** Programmatic Preconditions for Critical Transaction Ordering
**Situation:** Your payment system processes refunds. Business rule: customers must verify their identity through two-factor authentication before refunds are processed. Two approaches: (1) system prompt: "always verify identity before processing refund", (2) programmatic precondition blocking `process_refund` tool until `verify_identity_2fa` completes successfully.
**Options:**
- A) System prompt is sufficient; model understanding of criticality ensures compliance
- B) Programmatic preconditions provide deterministic guarantees independent of model reasoning or token pressure
- C) Both equally effective; choose based on team preference
- D) Use few-shot examples to guide model behavior

**Correct Answer:** B
**Feedback:** Critical business logic requires deterministic compliance. Prompts are probabilistic—the model might skip verification under token pressure or on edge cases. Programmatic preconditions guarantee compliance by blocking `process_refund` access until `verify_identity_2fa` succeeds, regardless of model reasoning.
**Theory Reference:** Domain 1.4 - Programmatic enforcement vs prompt guidance

---

## Question 255
**Title:** Structured Error Responses for Intelligent Recovery
**Situation:** Your coordinator receives an error from a tool: "Operation failed." The coordinator cannot determine if this is a transient network timeout (retry with backoff), validation error (fix input), or permission denied (escalate). How should tools be designed?
**Options:**
- A) Return plain text explanations of what went wrong
- B) Retry all errors with exponential backoff regardless of cause
- C) Return structured error metadata: errorCategory (transient/validation/permission), isRetryable flag, partial results, and failed request details
- D) Escalate all errors to a human immediately

**Correct Answer:** C
**Feedback:** Structured error responses enable intelligent recovery. Include: `errorCategory` (transient vs non-retryable), `isRetryable` boolean, partial results if available, and details of the failed request. This lets coordinators decide: retry, modify query, use partial results, or escalate.
**Theory Reference:** Domain 2.2 - Structured error responses for recovery

---

## Question 256
**Title:** Focused Prompting vs Comprehensive Coverage in Analysis Tasks
**Situation:** Your code audit system needs to analyze (1) security vulnerabilities, (2) performance bottlenecks, (3) code maintainability, (4) architectural compliance. You can use one comprehensive prompt or four sequential focused prompts.
**Options:**
- A) One comprehensive prompt reduces API calls and token usage
- B) Four sequential focused prompts; each focused analysis produces deeper insights despite higher API cost
- C) Use different models, each optimized for one aspect
- D) Alternate between strategies based on code complexity

**Correct Answer:** B
**Feedback:** Prompt chaining (sequential, focused prompts) produces better quality than one comprehensive prompt. When a prompt juggles multiple concerns, attention dilutes and quality suffers. Focused analysis of security → performance → maintainability → architecture yields richer insights, justifying higher API call count.
**Theory Reference:** Domain 1.6 - Prompt chaining for complex workflows

---

## Question 257
**Title:** PostToolUse Hook Placement for Data Normalization
**Situation:** Your system integrates three APIs that return dates in different formats: Unix epoch (seconds since 1970), ISO 8601 (2025-05-28T14:29:27Z), and human-readable (May 28, 2025). The agent needs consistent date format. Where should normalization occur?
**Options:**
- A) In the system prompt with explicit conversion instructions
- B) In a wrapper layer that calls each API and translates responses internally
- C) PostToolUse hook after all tool calls, before the agent processes results, normalizing all dates to ISO 8601
- D) PreToolUse hook to standardize inputs before each tool call

**Correct Answer:** C
**Feedback:** PostToolUse hooks intercept results after tool execution, before the agent processes them. This is ideal for normalizing inconsistent output formats—the agent sees consistent ISO 8601 dates regardless of source. PreToolUse is for request transformation, not response normalization.
**Theory Reference:** Domain 1.5 - Hook placement for data transformation

---

## Question 258
**Title:** Session Forking vs Resuming for Comparative Analysis
**Situation:** You're designing a system migration with two candidate approaches: Approach A (microservices) and Approach B (monolithic). You've explored Approach A in depth (100+ files analyzed, 80K tokens of context). Now you want to explore Approach B while keeping Approach A context accessible for comparison.
**Options:**
- A) Start analysis of Approach B in a new session, losing Approach A context
- B) Use `fork_session` to create an independent branch for Approach B, keeping A analysis preserved
- C) Use `--resume` to reload Approach A context after B analysis concludes
- D) Have two separate agents analyze approaches in parallel

**Correct Answer:** B
**Feedback:** `fork_session` creates independent investigation branches, preserving the original context. You can explore Approach B in a forked session while keeping Approach A analysis accessible. This enables direct side-by-side comparison without losing context.
**Theory Reference:** Domain 1.7 - Session management strategies

---

## Question 259
**Title:** Tool Specialization vs General Tools with Parameters
**Situation:** Your e-commerce platform has overlapping tools: `validate_email`, `validate_phone`, and `validate_credit_card` vs one general `validate_input` tool with a `type` parameter. Agents show inconsistent selection between specialized and general tools.
**Options:**
- A) Both are equally reliable; choose based on code organization
- B) General tool with parameter is simpler; agents learn parameter names quickly
- C) Use a routing classifier to map input types to specialized tools
- D) Specialized tools are more reliable; agents distinguish distinct tool names better than parameter combinations

**Correct Answer:** D
**Feedback:** Agents reliably distinguish between specialized tool names (`validate_email`, `validate_phone`, `validate_credit_card`) but struggle with parameter combinations in general tools. Specialization reduces selection ambiguity and improves accuracy without additional prompt engineering.
**Theory Reference:** Domain 2.3 - Tool specialization for reliability

---

## Question 260
**Title:** Skills Tool Restrictions with allowed-tools Constraint
**Situation:** You create a `/security-audit` skill restricted to security analysis tools only via the `allowed-tools` field in SKILL.md. The skill is blocked from accessing file modification tools. A developer asks the skill to fix security issues and modify code.
**Options:**
- A) The skill generates fix suggestions via text because prompts encourage it
- B) The system warns the developer but allows modifications since they explicitly requested it
- C) The skill cannot access modification tools; the `allowed-tools` constraint prevents access at runtime regardless of requests
- D) The skill provides recommendations but cannot execute them

**Correct Answer:** D
**Feedback:** The `allowed-tools` field in SKILL.md is an architectural constraint enforced at runtime. The skill can generate text recommendations but cannot invoke modification tools—access is architecturally blocked. This prevents both accidental and intentional misuse regardless of requests or prompts.
**Theory Reference:** Domain 3.2 - Skills with allowed-tools constraints

