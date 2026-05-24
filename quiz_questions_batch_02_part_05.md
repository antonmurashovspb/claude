# Quiz Question Bank - Batch 02 Part 05 (Questions 101-110)

## Question 101
**Title:** Coordinator Task Decomposition Boundaries
**Situation:** Your coordinator researches "effectiveness of renewable energy policies" and decomposes it into: (1) "tax incentives in Germany," (2) "subsidies in Denmark," (3) "grid integration challenges." The synthesis reveals all searches focus on Europe, missing policies in Asia, Africa, and the Americas. What's the root cause?
**Options:**
- A) The coordinator's decomposition was geographically narrow and should have included regional breakdowns
- B) Subagents failed to autonomously expand geographic scope when researching assigned topics
- C) The synthesis agent should have detected and flagged geographic gaps in the results
- D) Global policy research exceeded web search capabilities available to the system

**Correct Answer:** A
**Feedback:** Task decomposition is the coordinator's responsibility. Narrow decomposition produces narrow results. The coordinator should map the task space first: policy types AND geographic regions. Subagents execute what they're assigned; they don't correct coordinator blindspots.
**Theory Reference:** Domain 1.2 - Coordinator task decomposition strategy

---

## Question 102
**Title:** Transient vs Persistent Errors in Structured Responses
**Situation:** Your MCP tool returns: `{"isError": true, "errorCategory": "rate_limit", "isRetryable": true, "retryAfter": 30}` in one scenario, and `{"isError": true, "errorCategory": "permission_denied", "isRetryable": false}` in another. How should the agent handle these differently?
**Options:**
- A) Treat both errors the same way and ask the user to resolve them
- B) Retry the rate-limit error after 30 seconds; don't retry permission_denied; instead escalate or provide feedback
- C) Implement automatic retries internally and never expose permission errors to the coordinator
- D) Retry both errors with exponential backoff; both are eventually recoverable

**Correct Answer:** B
**Feedback:** Structured error metadata enables smart recovery. Rate limits are transient—retry after the specified delay. Permission errors are non-transient—retrying won't help. Treating both the same wastes attempts on unrecoverable errors.
**Theory Reference:** Domain 2.2 - Structured error responses and retry decisions

---

## Question 103
**Title:** Hook Placement for Request Transformation
**Situation:** You have tools with inconsistent input requirements: Tool A expects `customer_id`, Tool B expects `customer_email`, Tool C expects `customer_phone`. Where should input normalization happen?
**Options:**
- A) Use PreToolUse hook to transform agent input into each tool's expected format before execution
- B) Add transformation logic in each tool's wrapper code to handle multiple input formats
- C) Use PostToolUse hook after execution to clean up and normalize the response
- D) Add conversion instructions to the system prompt for the agent to handle

**Correct Answer:** A
**Feedback:** PreToolUse hooks intercept outgoing tool calls and transform requests. This is ideal for input normalization—the agent requests `customer_id`, the PreToolUse hook transforms it to the format each tool expects. PostToolUse is for response transformation, not input.
**Theory Reference:** Domain 1.5 - Hook placement for request/response handling

---

## Question 104
**Title:** Subagent Context Inheritance and Explicit Passing
**Situation:** Your coordinator has researched market data (25K tokens of findings). It spawns a subagent to analyze this data. The coordinator assumes the subagent automatically has the market data available. The subagent returns: "I don't have access to any market data." What went wrong?
**Options:**
- A) The Task tool didn't properly propagate context between agents during execution
- B) The coordinator should store market data in a shared database for subagent access
- C) Subagents don't inherit coordinator context; the coordinator must explicitly include findings in the Task prompt
- D) The subagent needs a system prompt that grants access to coordinator findings

**Correct Answer:** C
**Feedback:** Subagents operate with isolated context. The coordinator owns all inter-agent communication. If a subagent needs prior findings, the coordinator must explicitly include them in the prompt (e.g., "Here are prior market findings: ..."). Assumption of automatic inheritance leads to missing context.
**Theory Reference:** Domain 1.3 - Subagent context passing

---

## Question 105
**Title:** Tool Specialization vs Ambiguity
**Situation:** Your data processing system has three tools: (1) `transform_data` with a `format` parameter (JSON, CSV, XML), (2) a `validate_schema` tool, and (3) an `export_file` tool. Agents frequently misroute work to `transform_data` when they should call `export_file`. Tool descriptions are identical or overlapping.
**Options:**
- A) Keep the general tools and improve their descriptions to clarify when to use each
- B) Add a pre-processing step to classify tasks before tool selection occurs
- C) Reduce the agent's overall tool count to eliminate source of confusion
- D) Rename tools to be specialized: `transform_to_json`, `transform_to_csv`, `transform_to_xml`

**Correct Answer:** D
**Feedback:** Specialized tool names reduce ambiguity. `transform_to_json` is self-describing and more reliable than `transform_data` with a format parameter. Agents struggle to reason about parameter combinations; distinct tool names lead to better selection accuracy.
**Theory Reference:** Domain 2.1 & 2.3 - Tool naming and specialization

---

## Question 106
**Title:** Planning Mode for Architectural Changes
**Situation:** Your monolithic web application uses a single database. You need to split it into three microservices with separate databases, but the change touches 200+ files across routing, API, database layers, and configuration. The impact is high and architectural paths vary.
**Options:**
- A) Start with direct execution and switch to planning mode if it gets complicated during implementation
- B) Use Planning Mode to explore the codebase, understand dependencies, and design an approach first
- C) Hire a senior engineer to design the architecture, then use direct execution to implement
- D) Use direct execution with clear step-by-step instructions; planning mode adds unnecessary overhead

**Correct Answer:** B
**Feedback:** Planning Mode is specifically designed for complex, high-impact changes with multiple viable approaches. It safely explores dependencies and surface architectural considerations before any code is modified. This prevents expensive rework.
**Theory Reference:** Domain 3.4 - Planning Mode for complex refactors

---

## Question 107
**Title:** Coordinator Result Aggregation Patterns
**Situation:** Your coordinator spawns three subagents researching different data sources. Subagent A returns 30K tokens, Subagent B returns 18K tokens, Subagent C returns 22K tokens. Synthesizing from raw output exhausts context. What's the most efficient pattern?
**Options:**
- A) Reduce subagent autonomy and constrain each to produce less output overall
- B) Use vector databases to retrieve selective sections from each subagent's output
- C) Directly synthesize from all raw outputs using extended context
- D) Request summaries from each subagent (3-5 key points, ~2-3K tokens) before synthesis

**Correct Answer:** D
**Feedback:** Structured summaries reduce token waste while preserving key information. Having subagents produce concise summaries (3-5 key points, 2-3K tokens) instead of raw 70K tokens enables the coordinator to synthesize effectively. This scales better than raw output processing.
**Theory Reference:** Domain 1.2 - Coordinator result aggregation

---

## Question 108
**Title:** Environment Variable Substitution in MCP Configuration
**Situation:** Your team shares a `.mcp.json` file in the repository with a GitHub MCP server configuration. A developer accidentally commits their personal `GITHUB_TOKEN` in the file, exposing it publicly. How should this be handled going forward?
**Options:**
- A) Implement a token rotation system to periodically invalidate old tokens automatically
- B) Require developers to manually pass tokens at startup via command-line arguments only
- C) Store tokens in a separate `.env` file (not committed) and use `${GITHUB_TOKEN}` substitution
- D) Store the `.mcp.json` file outside the repository in a secure shared location instead

**Correct Answer:** C
**Feedback:** MCP servers support `${VAR_NAME}` syntax for environment variable substitution. The `.mcp.json` references variables; the actual tokens live in local `.env` or shell environment (never committed). This keeps secrets out of version control while maintaining team access.
**Theory Reference:** Domain 2.4 - MCP secret management

---

## Question 109
**Title:** Subagent Iteration with Coordinator Feedback
**Situation:** You spawn a research subagent. It returns initial findings that are incomplete. Instead of having the subagent revise, you route the coordinator's feedback back to the subagent for a second pass. Does this pattern work well?
**Options:**
- A) No; subagents don't persist state between Task calls and need explicit context for each new call
- B) Yes; subagents maintain full context across Task calls from the same coordinator automatically
- C) Yes, but you must store state in a shared database for the subagent to access it
- D) No; subagents should produce complete results on first attempt without iteration

**Correct Answer:** A
**Feedback:** Each Task call spawns a stateless subagent. Feedback from the coordinator is not automatically retained. If iteration is needed, the coordinator must either: (1) make another Task call with modified instructions + prior context, or (2) route to a different subagent. Design for coordinator-driven iteration, not implicit subagent loops.
**Theory Reference:** Domain 1.3 - Subagent execution and statefulness

---

## Question 110
**Title:** Context Degradation Detection in Long Sessions
**Situation:** You've been using Claude Code for code analysis in the same session for 90 minutes. Early findings (first 20 minutes) were precise. Now (after 50+ tool calls and file reads), the agent is making vague references ("that earlier file"), confusing variable names, and reasoning is less focused. What's the most reliable indicator that this is context degradation, not a difficult problem?
**Options:**
- A) Measure response latency; slower responses indicate degraded context
- B) Compare precision of early findings to recent findings; early ones are specific, recent ones are vague
- C) Check the conversation history token count to see if it exceeds thresholds
- D) Ask the agent to recall specific details from the early part of the session

**Correct Answer:** B
**Feedback:** Context degradation shows a specific pattern: early findings are accurate and specific, recent reasoning becomes vague and imprecise. Lost-in-the-middle effect and accumulating history reduce coherence. Checkpoint findings and start a fresh session or spawn a subagent for the next phase.
**Theory Reference:** Domain 5.4 - Context window management and degradation

---
