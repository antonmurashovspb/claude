# Quiz Question Bank - Batch 27 (Questions 251-260)

## Question 251
**Title:** API Rate Limiting and Subagent Retry Logic
**Situation:** Your research coordinator spawns a subagent to fetch data from a third-party API. The subagent receives `{"isError": true, "errorCategory": "rate_limited", "isRetryable": true}`. It retries after 1 second and gets the same error. Should the subagent retry with exponential backoff or escalate to the coordinator?

**Options:**
- A) Use exponential backoff locally; retry 2-3 times before escalating to the coordinator if still failing
- B) Escalate immediately since rate limits are outside the subagent's control
- C) Retry with fixed 60-second delays until the rate limit resets
- D) Skip retries and let the coordinator decide how to handle rate-limited errors

**Correct Answer:** A
**Feedback:** Structured error metadata enables smart retry decisions. Exponential backoff respects transient errors without blindly waiting. Local recovery reduces coordinator overhead. Escalate only after 2-3 failed attempts.
**Theory Reference:** Domain 2.2 - Structured error responses and transient failure recovery

---

## Question 252
**Title:** System Prompt Wording and Tool Bias
**Situation:** Your system processes customer requests. The system prompt says: "Always verify customer identity for all requests." Agents now call verify_customer for public-data requests that don't require identity verification. The tool is being misused due to the prompt.

**Options:**
- A) Revise the system prompt; replace "always verify" with situation-specific guidance in tool descriptions
- B) Implement a precondition blocking verification until a legitimate request context exists
- C) Add few-shot examples showing when verification is not needed
- D) Create a separate routing tool to decide when verification is required

**Correct Answer:** A
**Feedback:** Overly broad prompt wording ("always verify") creates unintended tool associations. Better fix: revise the prompt to emphasize context ("verify when handling account-sensitive requests") and strengthen tool descriptions with clear boundaries. This prevents tool misuse at the source.
**Theory Reference:** Domain 1.1 & Chapter 1.4 - System prompt wording and tool selection reliability

---

## Question 253
**Title:** MCP Server Environment Variable Substitution and Secret Management
**Situation:** Your project `.mcp.json` includes a GitHub MCP server. A developer commits a personal GitHub token directly in the file, making it public. How should this be prevented for future configurations?

**Options:**
- A) Use environment variable substitution: `"token": "${GITHUB_TOKEN}"`, store the actual token locally or in CI/CD secrets
- B) Implement an automatic token-rotation service that invalidates compromised tokens
- C) Use a separate authentication proxy to manage all token handling
- D) Require all developers to use machine accounts instead of personal tokens

**Correct Answer:** A
**Feedback:** MCP servers support `${VAR_NAME}` syntax for environment variable substitution. The `.mcp.json` file references variables (which live locally), keeping secrets out of version control. This is the simplest approach.
**Theory Reference:** Domain 2.4 - MCP server secret management with environment variables

---

## Question 254
**Title:** Coordinator Context Window and Subagent Decomposition
**Situation:** Your coordinator receives a complex research request. A naive approach: spawn one subagent with all 60K tokens of background context. A better approach: decompose the task into three subagents, each receiving 15K tokens of **relevant** context. Which strategy reduces coordinator context pressure?

**Options:**
- B) Decompose into multiple subagents with filtered, task-relevant context for each
- A) Send all context to one subagent to avoid duplication and coordination overhead
- C) Use vector embeddings to dynamically select which context to include
- D) Split the context across multiple requests to the same subagent sequentially

**Correct Answer:** B
**Feedback:** Task decomposition with context filtering is more efficient than sending all context to one subagent. Each subagent receives focused context (15K tokens of relevant material), reducing processing overhead. The coordinator then synthesizes smaller outputs. This trades decomposition overhead for better context efficiency and parallel execution.
**Theory Reference:** Domain 1.2 - Coordinator task decomposition and context efficiency

---

## Question 255
**Title:** Hook Placement for Compliance Enforcement
**Situation:** Your financial system processes fund transfers. Business rule: block transfers above $50,000 without manager approval. You want this rule enforced deterministically, not suggested by the system prompt. Should you use a PreToolUse or PostToolUse hook?

**Options:**
- A) PreToolUse hook to inspect the request before the tool executes
- B) PostToolUse hook to intercept the result after execution but before returning
- C) System prompt instruction; prompts are sufficient for compliance
- D) Implement the check inside the tool itself

**Correct Answer:** B
**Feedback:** Critical compliance rules need deterministic enforcement. A PostToolUse hook intercepts the transfer after the agent decides to execute it but before the result returns. This captures the attempted action and can block/redirect it. System prompts alone are probabilistic and unreliable.
**Theory Reference:** Domain 1.5 - Hook placement for compliance and business rule enforcement

---

## Question 256
**Title:** Tool Specialization and Agent Reliability
**Situation:** Your system has overlapping tools: `process_document`, `analyze_document`, `extract_metadata`. Agents confuse `process_document` and `analyze_document` frequently despite clear descriptions. Tool names are functionally ambiguous.

**Options:**
- A) Descriptions are sufficient; naming has minimal impact on selection
- B) Rename to specialized names: `convert_pdf_to_text`, `generate_summary`, `extract_metadata`
- C) Merge all tools into a single `handle_document` with a format parameter
- D) Implement a downstream routing classifier to guide agent tool selection

**Correct Answer:** B
**Feedback:** Tool names are the first signal agents encounter. Generic names like `process_document` and `analyze_document` create selection confusion. Specialized, action-oriented names are self-describing and prevent misrouting more effectively than descriptions or routing layers.
**Theory Reference:** Domain 2.1 & 2.3 - Tool naming clarity and specialization

---

## Question 257
**Title:** Prompt Chaining vs Adaptive Decomposition for Bug Detection
**Situation:** You're building a system to detect bugs in a large codebase. Option 1: use prompt chaining—fixed sequence of (1) scan for type errors, (2) check logic bugs, (3) verify resource cleanup. Option 2: adaptive decomposition—map the codebase structure first, then create targeted subtasks based on what was discovered.

**Options:**
- A) Adaptive decomposition produces more accurate results by targeting subtasks based on codebase structure
- B) Prompt chaining; it is more predictable and costs fewer API calls
- C) Use different models for each step to parallelize analysis
- D) Single comprehensive prompt that handles all bug categories at once

**Correct Answer:** C
**Feedback:** For open-ended bug detection, adaptive decomposition is superior. First map the codebase structure (identify patterns, risk zones), then create targeted subtasks. This discovers bugs that a fixed pipeline might miss. Prompt chaining works better for **predictable multi-aspect workflows** (validation → transformation → export). B is cheaper but less accurate for exploratory analysis.
**Theory Reference:** Domain 1.6 - Task decomposition strategies (fixed vs adaptive)

---

## Question 258
**Title:** Tool Access Scoping and Least Privilege
**Situation:** Your multi-agent system manages both customer support and internal analytics. A customer support subagent needs: `get_customer`, `lookup_order`, `process_refund`, and `escalate_to_human`. An internal-metrics subagent needs: `query_analytics_db`, `generate_report`, `export_data`. Should customer support access all 40 global tools?

**Options:**
- A) Yes, provide full access to all 40 tools; restricting tools limits flexibility
- B) Create a middleware layer that decides which tools to expose per subagent
- C) No, restrict each subagent to only its required tools per least privilege principle; fewer tools reduce context waste and selection errors
- D) Give customer support 15 tools (all customer-facing ones) for flexibility

**Correct Answer:** C
**Feedback:** Least privilege principle: restrict each subagent to its specific task tools. A customer support agent with access to 40 tools wastes context, increases confusion, and hurts tool selection reliability. Scoped access (4-5 tools per subagent) improves focus and accuracy. Middleware (D) adds complexity without the deterministic guarantees of architecture-level restriction.
**Theory Reference:** Domain 2.3 - Allocating tools and least privilege principle

---

## Question 259
**Title:** `.claude/rules/` Path Matching for Convention Loading
**Situation:** Your repository has tests in: `tests/unit/`, `src/components/__tests__/`, `integration/`. You want test conventions (factories, mocks) to apply everywhere tests exist.

**Options:**
- A) Create `CLAUDE.md` in each test directory separately
- B) Add test conventions to root `CLAUDE.md` for all file types
- C) Create a skill that applies test conventions manually
- D) Create `.claude/rules/test-conventions.md` with glob patterns activating on matching files

**Correct Answer:** D
**Feedback:** Path-specific rules activate automatically when editing matching files. This applies conventions across dispersed directories without duplicating CLAUDE.md files or manual skill invocation.
**Theory Reference:** Domain 3.3 - Path-specific rules for convention loading

---

## Question 260
**Title:** Session Forking for Comparative Analysis
**Situation:** You explore Approach A in detail (analyze 30+ files, create a design plan). Now you want to explore Approach B without losing Approach A's analysis, then compare both.

**Options:**
- A) Save Approach A results, start fresh for B, then manually compare later
- B) Use a single session for both approaches sequentially
- C) Resume the A session after B; B runs in a separate new session
- D) Fork the current session; explore B in the fork while keeping A in the parent session

**Correct Answer:** D
**Feedback:** `fork_session` creates independent branches from shared context. Explore B in the fork while keeping A in the parent. After both explorations, compare results side-by-side. This preserves both investigation threads without losing context.
**Theory Reference:** Domain 1.7 - Session forking for comparative analysis

