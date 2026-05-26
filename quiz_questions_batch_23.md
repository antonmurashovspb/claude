# Quiz Question Bank - Batch 23 (Questions 231-240)

## Question 231
**Title:** Subagent Isolation and Context Inheritance
**Situation:** Your coordinator runs a research workflow with three subagents: information gathering, synthesis, and validation. The coordinator accumulated 50K tokens of conversation history while the information-gathering subagent conducted research. The synthesis subagent needs access to the research findings to create a comprehensive analysis.
**Options:**
- A) Explicitly include all information-gathering outputs in the synthesis subagent's prompt since subagents have isolated context
- B) Subagents automatically inherit the coordinator's full conversation history to maintain context continuity
- C) Store research findings in a shared cache that all subagents query automatically
- D) Request only summary statistics from information gathering to minimize the synthesis subagent's context load

**Correct Answer:** A
**Feedback:** Subagents operate with isolated context and do not automatically inherit coordinator history. All required information must be explicitly included in their prompts. This isolation provides better observability and allows fine-grained control over what each subagent knows.
**Theory Reference:** Domain 1.3 - Subagent context passing and isolation

---

## Question 232
**Title:** Tool Naming Impact on Agent Selection Reliability
**Situation:** Your document processing system has three tools: `extract`, `process`, and `convert`. Agents frequently misroute work. For example, a task requiring PDF text extraction sometimes uses `process` instead of `extract`. You analyze performance across tool naming approaches.
**Options:**
- A) Generic names are acceptable if tool descriptions are comprehensive and detailed
- B) Specialized names like `extract_text_from_pdf` self-identify their purpose and improve selection accuracy
- C) Consolidate into a single `document_handler` tool with format parameters to simplify agent choices
- D) Tool selection quality depends on agent version, not naming conventions

**Correct Answer:** B
**Feedback:** Tool names are the first signal agents encounter. Specific, action-oriented names like `extract_text_from_pdf` are self-describing and reduce ambiguity. Agents struggle with generic names even when descriptions are detailed. Clear naming prevents costly misrouting.
**Theory Reference:** Domain 2.1 - Tool naming and description clarity

---

## Question 233
**Title:** PreToolUse vs PostToolUse Hook Placement for Data Transformation
**Situation:** Your system receives data from three different calendar services: Google uses RFC 3339 timestamps, Outlook uses Unix milliseconds, and Apple uses ISO 8601. The agent must normalize these to a standard format before processing. Where should the transformation occur?
**Options:**
- A) Wrapper layer that intercepts agent responses and translates them
- B) PreToolUse hook to standardize input formats before tool calls are executed
- C) PostToolUse hook to normalize results after tools return data
- D) In the system prompt with explicit conversion instructions for each service

**Correct Answer:** C
**Feedback:** PostToolUse hooks intercept tool results before the agent processes them. This ensures the agent always sees consistent, normalized data regardless of the source tool. PreToolUse is for request transformation, not response normalization.
**Theory Reference:** Domain 1.5 - Hook placement for data transformation

---

## Question 234
**Title:** Error Metadata and Recovery Strategy Selection
**Situation:** Your coordinator delegates database queries to a subagent. Two errors occur: (1) a connection timeout with `{"isError": true, "errorCategory": "transient", "isRetryable": true}`, and (2) a malformed query with `{"isError": true, "errorCategory": "validation", "isRetryable": false}`. The coordinator must decide how to recover.
**Options:**
- A) Immediately escalate both to a human since any error indicates a critical failure
- B) Retry transient errors with backoff; for validation errors, log them and escalate without retrying
- C) Implement automatic query rewriting for validation errors before retrying
- D) Retry both errors with exponential backoff since retries never introduce additional harm

**Correct Answer:** B
**Feedback:** Error metadata enables intelligent recovery. Transient errors (timeouts, temporarily unavailable services) benefit from retry logic with backoff. Non-retryable errors (validation, permissions) indicate the request itself is flawed—fixing the input or escalating is correct, not retrying.
**Theory Reference:** Domain 2.2 - Error categorization and recovery

---

## Question 235
**Title:** Prompt Chaining vs Single Comprehensive Prompt for Multi-stage Workflows
**Situation:** You build a data transformation pipeline: extract fields, validate each field, normalize formats, and generate a report. Each stage depends on the previous stage's output. Should you use one large prompt covering all stages or separate focused prompts per stage?
**Options:**
- A) Combine stages that use similar tools: extraction+validation together, normalization+reporting together
- B) Single comprehensive prompt minimizes API calls and keeps the full context visible
- C) Separate prompts per stage; each stage receives focused attention without dilution
- D) Use one prompt for stages 1-2, a second for stages 3-4 to balance context size

**Correct Answer:** C
**Feedback:** Separate prompts per distinct stage ensure each receives focused reasoning. Quality outweighs API call count. Intermediate results can be validated between stages, catching errors early. Attention dilution in comprehensive prompts leads to lower quality outputs.
**Theory Reference:** Domain 1.6 - Prompt chaining for complex workflows

---

## Question 236
**Title:** Least Privilege for Tool Access in Multi-agent Systems
**Situation:** Your multi-agent system has a coordinator that manages research across three regions. The coordinator has access to 20 tools: regional databases, reporting APIs, and aggregation tools. Each regional subagent is given the same 20 tools for consistency.
**Options:**
- A) Subagents benefit from having all tools available, allowing flexibility in solving problems
- B) Divide tools equally among subagents to balance capabilities
- C) Restrict subagents to region-specific tools only; the coordinator retains all tools
- D) Use a central tool registry service that routes requests based on agent identity

**Correct Answer:** D
**Feedback:** Apply the principle of least privilege with a central tool registry service. Subagents need only region-specific tools for their narrow scope. The coordinator retains all tools for aggregation and cross-region coordination. This reduces context, improves tool selection reliability, and prevents misuse.
**Theory Reference:** Domain 2.3 - Tool allocation across agents

---

## Question 237
**Title:** Deterministic Enforcement vs Prompt Guidance for Critical Workflows
**Situation:** Your financial system processes refunds. Company policy: all refunds over $5,000 require manager approval before payment. A developer suggests handling this with clear system prompt instructions. What is the architectural concern?
**Options:**
- A) System prompts provide deterministic guarantees that refunds won't process without approval
- B) System prompts are more maintainable than code-based enforcement
- C) Prompts alone provide only probabilistic guidance; critical business logic requires programmatic enforcement
- D) Using programmatic hooks to block unauthorized refunds is the correct approach for deterministic enforcement

**Correct Answer:** D
**Feedback:** Critical workflows demand deterministic guarantees. System prompts guide the model but cannot guarantee behavior. Use PreToolUse hooks to block `process_refund` if approval is missing. This architectural constraint ensures policy compliance regardless of prompt changes.
**Theory Reference:** Domain 1.4 - Workflow enforcement patterns

---

## Question 238
**Title:** Session Forking for Parallel Exploration
**Situation:** You're redesigning a payment system architecture. Approach A uses microservices with API gateways; Approach B uses a monolithic service with plugins. After deeply exploring Approach A's implications, you want to compare Approach B while preserving your A analysis for later comparison.
**Options:**
- A) Save findings from Approach A, then restart with Approach B in a completely new session
- B) Maintain both explorations in one session by alternating between architectural notes
- C) Use fork_session to create an independent branch exploring Approach B while preserving Approach A context
- D) Continue in the current session and replace context with Approach B exploration

**Correct Answer:** C
**Feedback:** fork_session creates independent branches from shared context. You can explore Approach B without losing Approach A analysis. Later you can compare both architectures side-by-side. This is more efficient than restarting or maintaining scattered notes.
**Theory Reference:** Domain 1.7 - Session forking for comparative analysis

---

## Question 239
**Title:** MCP Configuration and Environment Variable Substitution
**Situation:** Your team shares a `.mcp.json` configuration file that sets up an MCP GitHub server with a token for API access. The file is committed to your repository. What is the secure approach to managing the token while allowing team collaboration?
**Options:**
- A) Each developer hardcodes their personal token directly in `.mcp.json` locally and never commits it
- B) Use environment variable substitution in `.mcp.json` (e.g., `"token": "${GITHUB_TOKEN"}`) with tokens stored locally
- C) Pass tokens as command-line arguments at server startup
- D) Implement a token-rotation system that periodically invalidates and reissues tokens

**Correct Answer:** B
**Feedback:** MCP servers support `${VAR_NAME}` syntax for environment variable substitution. Configuration stays version-controlled while secrets remain local. This balances team collaboration with security—no tokens committed, and developers use their own tokens.
**Theory Reference:** Domain 2.4 - MCP server configuration security

---

## Question 240
**Title:** Stop Reason vs Iteration Limits in Agentic Loop Control
**Situation:** Your agent enforces a hard iteration limit of 8 tool calls. Simple customer support tasks complete in 5 calls. Complex cases with multiple escalations need 12+ calls but should complete based on the agent's reasoning, not an arbitrary counter.
**Options:**
- A) Use stop_reason to control loop termination; stop only on "end_turn", not iteration count
- B) Increase the iteration limit to 15 for all cases to handle complexity
- C) Add manual approval after 8 iterations if the task isn't complete
- D) Implement task-complexity detection to dynamically adjust the limit per request

**Correct Answer:** A
**Feedback:** stop_reason signals actual completion ("end_turn") or tool needs ("tool_use"). Arbitrary iteration limits override the model's reasoning and decision-making. The agent should decide when to stop based on task completion, not a hard counter.
**Theory Reference:** Domain 1.1 - Agent loop control via stop_reason

