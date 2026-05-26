# Quiz Question Bank - Batch 24 (Questions 201-210)

## Question 201
**Title:** Context Window Management in Multi-agent Synthesis
**Situation:** Your multi-agent system has a coordinator that aggregates research from 5 subagents. Each subagent returns 20K tokens of findings. The coordinator receives 100K tokens total and must synthesize into a 5K-token report. Should the coordinator process raw outputs or request summaries?
**Options:**
- A) Request summaries from each subagent first; coordinator synthesizes from structured ~10K token summaries, preserving key facts
- B) Process all raw 100K tokens in one pass for complete accuracy
- C) Have subagents filter outputs to 5K each before returning
- D) Use a larger model to handle the full 100K token input

**Correct Answer:** A
**Feedback:** Raw 100K tokens dilute coordinator attention and waste context. Requesting focused summaries (2–4 key points per subagent) yields ~10K tokens that the coordinator synthesizes efficiently. This maintains quality while managing context effectively.
**Theory Reference:** Domain 1.2 - Coordinator result aggregation strategy

---

## Question 202
**Title:** Programmatic Enforcement vs Prompt-based Ordering
**Situation:** Your customer support system requires identity verification before processing refunds. Currently, the system prompt says "always verify the customer before refunding." Verification succeeds in 98% of cases, but 2% of refund requests skip verification entirely.
**Options:**
- A) Improve the system prompt with more explicit warnings
- B) Use preconditions to block refund tools until identity verification returns a customer ID
- C) Implement a routing classifier that routes to verification first
- D) Add few-shot examples showing correct verification-then-refund sequences

**Correct Answer:** B
**Feedback:** System prompts provide probabilistic compliance (~98%). For critical business logic requiring deterministic guarantees, use programmatic preconditions. These block refund execution until verification is complete—the model cannot override.
**Theory Reference:** Domain 1.4 - Workflow enforcement and handoff patterns

---

## Question 203
**Title:** Session Resumption vs Fresh Sessions
**Situation:** An investigative analysis session discovered architectural insights 6 days ago. You want to continue the investigation on recent code changes. Should you resume the old session or start fresh?
**Options:**
- A) Resume the old session; it preserves findings and maintains investigation continuity
- B) Fork the session to preserve old findings while exploring new changes in parallel
- C) Resume only if the codebase has changed by less than 5% of files
- D) Start a fresh session; the old results are stale after 6 days and may contain outdated architectural assumptions

**Correct Answer:** D
**Feedback:** Old session findings are stale—architectural insights from 6 days ago may no longer apply if code changed significantly. A fresh session with a summary of prior findings is more reliable than resuming with potentially outdated assumptions.
**Theory Reference:** Domain 1.7 - Session state and resumption decisions

---

## Question 204
**Title:** Tool Overload and Subagent Reliability
**Situation:** You allocate 22 tools to a research subagent to maximize flexibility: 10 database queries, 8 API calls, 3 report generators, and a fallback. Field data shows the subagent misroutes 31% of requests to wrong tools. A simplified subagent with only 4 region-specific tools achieves 94% accuracy.
**Options:**
- A) Keep 22 tools but improve tool descriptions to fix routing accuracy
- B) Accept the 31% misrouting as unavoidable with complex tasks
- C) Restrict the subagent to 4–5 specialized tools; coordinator retains flexible tools for cross-region work
- D) Split into 5 separate subagents, each with 4–5 tools

**Correct Answer:** C
**Feedback:** Tool overload reduces reliability. The simplified subagent (4 tools, 94% accuracy) confirms that scope reduction works. Apply least privilege: subagents get role-specific tools only. The coordinator retains broader tools for aggregation and cross-region logic.
**Theory Reference:** Domain 2.3 - Tool allocation across agents

---

## Question 205
**Title:** Data Normalization via PostToolUse Hooks
**Situation:** Your system calls three external tools: Tool A returns timestamps as Unix seconds, Tool B as ISO 8601 strings, Tool C as epoch milliseconds. Agents frequently misinterpret these inconsistent formats, causing calculation errors.
**Options:**
- A) Normalize in tool descriptions to guide agent expectations
- B) Have the system prompt instruct agents to convert timestamps
- C) Use PostToolUse hooks to normalize all returned timestamps to ISO 8601 before the agent processes results
- D) Create wrapper functions for each tool that standardize outputs

**Correct Answer:** C
**Feedback:** PostToolUse hooks intercept results after tool execution, before the agent sees them. This provides deterministic normalization—the agent sees consistent ISO 8601 format regardless of tool. Prompts and descriptions cannot guarantee format consistency.
**Theory Reference:** Domain 1.5 - Hook placement for data normalization

---

## Question 206
**Title:** Specialized vs General-purpose Tools
**Situation:** Your system has two design options: (1) specialized tools `extract_text_from_pdf`, `extract_tables_from_pdf`, `extract_images_from_pdf` or (2) a general tool `extract_content` with a `type: ["text"|"tables"|"images"]` parameter. Testing shows agents select correctly 85% of the time with (2) but 97% with (1).
**Options:**
- A) Both approaches have similar reliability; choose based on code organization preferences
- B) Accuracy differences between approaches are negligible for most tasks
- C) General tools with parameters are simpler to maintain despite lower accuracy
- D) Specialized tools reduce selection ambiguity and improve reliability; specialized names are clearer signals than parameter combinations

**Correct Answer:** D
**Feedback:** Specialized tools (97% accuracy) outperform general tools with parameters (85%) because agents cannot reliably reason about parameter combinations. Distinct tool names are clear signals. Specialization trades code organization for selection reliability.
**Theory Reference:** Domain 2.3 - Tool specialization for reliability

---

## Question 207
**Title:** Structured Error Context for Intelligent Recovery
**Situation:** A search subagent times out while researching policy documents. Currently, the subagent returns only `{"isError": true, "message": "timeout"}`. The coordinator cannot decide whether to retry, try alternatives, or use partial results.
**Options:**
- A) Return generic errors; timeouts always mean the same thing and don't require additional context
- B) Structure error responses with category, retryability, original query, partial results found, and suggested alternatives
- C) Retry internally with exponential backoff and return only final success/failure status
- D) Escalate timeouts immediately to a human operator

**Correct Answer:** B
**Feedback:** Structured error context enables intelligent recovery. The coordinator learns: the query that failed, what partial results exist, whether retrying makes sense, and what alternatives remain. Generic errors prevent good decision-making.
**Theory Reference:** Domain 2.2 - Structured error responses

---

## Question 208
**Title:** Complexity-aware Iteration Limits
**Situation:** Your agent has a 15-iteration limit for tool calls. Simple requests complete in 3 calls; complex reasoning problems often need 12–18 calls. The fixed limit causes complex requests to fail with "iteration limit exceeded" errors.
**Options:**
- A) Use `stop_reason` to guide the loop; iterate while `stop_reason == "tool_use"`, stop on `"end_turn"`. Let the model decide when to stop, not iteration limits
- B) Increase the limit to 30 to handle all complex cases
- C) Implement complexity detection to dynamically adjust limits per request
- D) Split complex requests into multiple sub-tasks with lower limits each

**Correct Answer:** A
**Feedback:** `stop_reason` signals when the model is done (end_turn) or needs tools (tool_use). Arbitrary iteration limits override the model's decision-making. Remove the hard counter; let the agent decide based on reasoning progress.
**Theory Reference:** Domain 1.1 - Agent loop control via stop_reason

---

## Question 209
**Title:** MCP Server Token Management
**Situation:** Your team's `.mcp.json` file contains a GitHub MCP server with `"token": "ghp_ABC123XYZ..."` hardcoded. A developer commits it to the repository, and the token is now public.
**Options:**
- A) Have developers manually pass tokens as command-line arguments
- B) Create a separate secrets file outside the repository and load it at runtime
- C) Implement a token-rotation system that invalidates old tokens automatically
- D) Use environment variable substitution: `"token": "${GITHUB_TOKEN}"` in `.mcp.json`; store the actual token in `.env` (not committed)

**Correct Answer:** D
**Feedback:** MCP servers support `${VAR_NAME}` syntax. `.mcp.json` references environment variables (stored locally); actual secrets never enter version control. This is the standard approach for team configurations.
**Theory Reference:** Domain 2.4 - MCP server secret management

---

## Question 210
**Title:** Least Privilege Tool Access
**Situation:** A financial validation coordinator manages refund decisions across 3 regions. It delegates verification to subagents. Currently, all subagents have access to: payment APIs, compliance databases, fraud detection, reporting tools, account modification, and escalation channels (12 tools total). Subagents often misuse tools outside their scope.
**Options:**
- A) Add role labels to tools in descriptions so subagents understand their purpose better
- B) Restrict each subagent to verification-only tools: compliance check, fraud flag, escalation. Coordinator retains payment and modification tools
- C) Give subagents access to all tools but use system prompt to guide appropriate usage
- D) Implement a central tool-routing service that filters tool access at runtime

**Correct Answer:** B
**Feedback:** Apply least privilege. Subagents need only to verify and flag issues—compliance check and fraud detection. Coordinator controls modifications and payments. This reduces context, prevents misuse, and scales reliably.
**Theory Reference:** Domain 2.3 - Allocating tools across agents

---
