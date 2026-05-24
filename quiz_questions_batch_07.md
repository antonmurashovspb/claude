# Quiz Question Bank - Batch 07 (Questions 71-80)

## Question 71
**Title:** Subagent Context Isolation and Information Loss
**Situation:** Your coordinator passes a research task to a subagent with a summary of prior findings. The subagent executes successfully but misses a critical constraint from the coordinator's original context—it interprets ambiguous instructions differently than intended. The subagent's output doesn't match expectations.
**Options:**
- A) You must explicitly include all necessary information and constraints in the prompt passed to subagents
- B) Subagents automatically inherit the coordinator's full conversation history and system prompt
- C) Shared session variables can pass implicit context between coordinator and subagents
- D) The coordinator should minimize subagent count to prevent context fragmentation

**Correct Answer:** A
**Feedback:** Subagents do not inherit coordinator context automatically. Each subagent operates in isolation. You must explicitly include task details, constraints, prior findings, and formatting requirements in the subagent prompt. Failing to do so leads to misalignment.
**Theory Reference:** Domain 1.3 - Subagent context passing

---

## Question 72
**Title:** Adaptive Decomposition vs Fixed Pipelines
**Situation:** You're building a system to analyze customer feedback. Option 1: always extract sentiment, then topics, then generate summary (fixed 3-step pipeline). Option 2: analyze sentiment first; if negative, drill into topics; if positive, generate summary directly (adaptive based on results).
**Options:**
- A) Fixed pipelines are superior; they ensure predictable and reproducible results
- B) Adaptive approaches are typically overengineering for most practical scenarios
- C) Use fixed pipelines for predictable tasks; use adaptive when intermediate results guide next steps
- D) Adaptive workflows consistently lead to inconsistent and unreliable results

**Correct Answer:** C
**Feedback:** Fixed pipelines (prompt chaining) work well for predictable multi-step tasks. Adaptive decomposition applies when results from one stage determine what happens next. For feedback analysis, sentiment might determine investigation depth—adaptive is justified here.
**Theory Reference:** Domain 1.6 - Task decomposition strategies

---

## Question 73
**Title:** System Prompt Ambiguity and Tool Overuse
**Situation:** Your system prompt says "Always verify the customer before processing orders." Data shows the agent now calls `get_customer` excessively—even for simple order tracking queries that don't require verification. Tool misuse is hurting performance.
**Options:**
- A) Narrow the prompt to "Verify customer for refunds and high-value orders" to clarify when it's needed
- B) The system prompt works correctly; all customer operations require verification
- C) Remove the system prompt instruction and rely only on tool descriptions instead
- D) Add alternative tools so the agent has options other than verification

**Correct Answer:** A
**Feedback:** Overly broad system prompt wording ("always verify") creates false associations. The agent internalizes this as a universal rule. Narrowing the instruction to specific scenarios ("when processing refunds") prevents unnecessary tool calls while maintaining correctness.
**Theory Reference:** Domain 1.4 & Chapter 1.4 - System prompt precision

---

## Question 74
**Title:** Tool Descriptions and Selection Reliability
**Situation:** Your system has three tools: `search_documents`, `search_web`, and `search_database`. All have minimal descriptions like "Searches for information." Agents frequently call the wrong tool.
**Options:**
- A) Minimal descriptions are adequate; the real issue is having too many similar tools
- B) Expand descriptions with input formats, use cases, and boundaries to help agents distinguish them
- C) Merge the tools into one generic `search` tool with a format parameter
- D) Add a routing layer that pre-selects the correct tool before exposing options

**Correct Answer:** B
**Feedback:** Tool descriptions are the primary selection mechanism for LLMs. Minimal descriptions cause confusion. Expanding them to specify what each tool searches (documents, web, database), what queries each handles best, and example use cases dramatically improves selection accuracy.
**Theory Reference:** Domain 2.1 - Tool naming and description clarity

---

## Question 75
**Title:** MCP Server Configuration and Secret Management
**Situation:** Your team has a shared `.mcp.json` file in the project. You want to add a GitHub MCP server that needs a GitHub token. A teammate suggests hardcoding the token directly in `.mcp.json` for simplicity.
**Options:**
- A) Hardcoding tokens is acceptable if you restrict repository access to team members
- B) Store the token in a separate configuration file outside version control
- C) Implement a token-rotation service that generates new tokens periodically
- D) Use environment variable substitution in `.mcp.json` (e.g., `${GITHUB_TOKEN}`) with tokens stored locally

**Correct Answer:** D
**Feedback:** MCP servers support `${VAR_NAME}` substitution syntax. Store the `.mcp.json` reference in version control; store actual secrets in local `.env` files or shell environment variables. This keeps secrets out of git while keeping configuration shared.
**Theory Reference:** Domain 2.4 - MCP server secret management

---

## Question 76
**Title:** Error Categorization and Retry Strategy
**Situation:** Your MCP tool returns three error responses: (1) API timeout, (2) invalid user input format, (3) permission denied for this user account. How should your coordinator handle each?
**Options:**
- A) Retry all errors with exponential backoff since retrying is generally safe
- B) Don't retry any errors; escalate all failures to a human for handling
- C) Retry transient errors (1) with backoff; don't retry validation (2) or permission (3) errors
- D) Retry errors 1 and 2 as they may eventually resolve; escalate only permission errors

**Correct Answer:** C
**Feedback:** Error categorization enables intelligent recovery. Transient errors (timeouts, temporary unavailability) benefit from retries. Validation errors (bad input) require fixing the input, not retrying. Permission errors indicate a policy violation—escalate or handle differently than transient failures.
**Theory Reference:** Domain 2.2 - Error categorization and retry decisions

---

## Question 77
**Title:** PostToolUse Hooks for Data Normalization
**Situation:** Your system integrates four external tools that return timestamps in different formats: Unix epoch (tool A), ISO 8601 (tool B), human-readable (tool C), and milliseconds since epoch (tool D). You want the agent to see consistent timestamp format.
**Options:**
- A) Add normalization logic to each individual tool's wrapper function
- B) Instruct the agent in the system prompt to normalize timestamps after tool calls
- C) Create a separate normalize_timestamp tool that the agent calls after each tool
- D) Use a PostToolUse hook to intercept and normalize all results before the agent processes them

**Correct Answer:** D
**Feedback:** PostToolUse hooks intercept results after tool execution but before the agent consumes them. This is the correct layer for data normalization—the agent sees a consistent format across all tools without needing to know about individual tool quirks or call a normalization tool explicitly.
**Theory Reference:** Domain 1.5 - Hook placement for data normalization

---

## Question 78
**Title:** Coordinator Context Management with Large Subagent Outputs
**Situation:** A coordinator spawns 4 subagents researching different aspects of a topic. Subagent outputs total 80K tokens. The coordinator must synthesize these into a concise report. Instead of processing raw output, you ask each subagent to provide only top 5 key findings (~1K tokens each).
**Options:**
- A) This is micromanagement; subagents should decide their own output format
- B) The coordinator should use a higher-tier model with larger context window
- C) Vector databases should be used to store subagent outputs for selective retrieval
- D) Requesting structured summaries reduces context burden while maintaining quality

**Correct Answer:** D
**Feedback:** Requesting structured summaries from subagents reduces raw token load while preserving signal. The coordinator synthesizes from concise, focused summaries (~5K total) instead of raw 80K-token outputs. This scales better and improves synthesis quality by reducing noise.
**Theory Reference:** Domain 1.2 - Coordinator result aggregation strategy

---

## Question 79
**Title:** Tool Specialization vs General Tools with Parameters
**Situation:** You're designing a document processing system. Option 1: Create specialized tools `extract_text_from_pdf`, `extract_images_from_pdf`, `extract_metadata_from_pdf`. Option 2: Create one general tool `extract_from_pdf` with a `content_type` parameter (text, images, metadata).
**Options:**
- A) Both approaches have equal reliability; choose based on code organization preference
- B) Specialized tools are more reliable because agents can precisely target the right tool without reasoning about parameters
- C) General tools with parameters are simpler and reduce the total number of tools the agent sees
- D) Agents are equally good at reasoning about parameter combinations as they are at selecting distinct tool names

**Correct Answer:** B
**Feedback:** Specialized tool names are more reliable than general tools with parameters. Agents can't easily reason about parameter combinations. Distinct names (`extract_text_from_pdf`, `extract_images_from_pdf`) provide clearer signals than one tool with multiple parameter options.
**Theory Reference:** Domain 2.3 - Tool specialization for reliability

---

## Question 80
**Title:** Prompt Chaining for Complex Multi-stage Workflows
**Situation:** Your system needs to (1) parse legal contract, (2) identify obligations, (3) assess risk, (4) generate summary. You're deciding between one large prompt covering all 4 stages vs four separate sequential prompts.
**Options:**
- A) Four separate prompts is preferable; each stage gets focused reasoning without attention dilution
- B) One comprehensive prompt is better to minimize API calls and latency
- C) Both approaches produce identical quality; choose based on API cost
- D) Quality depends only on prompt engineering, not the number of stages

**Correct Answer:** A
**Feedback:** Prompt chaining (sequential focused prompts) enables better reasoning for complex workflows. One large prompt trying to handle all four stages dilutes attention across concerns. Quality and reliability improve when each stage has a dedicated prompt. This is worth the additional API calls.
**Theory Reference:** Domain 1.6 & Chapter 6.3 - Prompt chaining
