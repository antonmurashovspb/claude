# Quiz Question Bank - Batch 15 (Questions 151-160)

## Question 151
**Title:** Subagent Spawning and Parallel Execution Latency
**Situation:** Your coordinator needs to execute three research tasks: extract data from documents (Task 1), validate extracted data (Task 2), and generate a report (Task 3). Task 2 depends on Task 1's output. How should you spawn these tasks?
**Options:**
- A) Spawn all three tasks in parallel; the coordinator will handle dependencies naturally
- B) Spawn Task 1, wait for results, then spawn Task 2, then Task 3 sequentially
- C) Spawn Tasks 1 and 2 together since both are extraction tasks
- D) Force Task 1 to complete first, then spawn remaining tasks in parallel

**Correct Answer:** B
**Feedback:** When tasks have dependencies, sequential execution is required. Task 2 cannot execute without Task 1's output. Sequential chaining ensures data flows correctly. A creates failures due to missing dependencies. C ignores the dependency between 1 and 2. D is inefficient when 3 can run after 2.
**Theory Reference:** Domain 1.3 - Subagent spawning and context passing; Domain 1.6 - Task decomposition strategies

---

## Question 152
**Title:** Tool Description Clarity and Selection Ambiguity
**Situation:** Your system has three extraction tools with minimal descriptions ("Extracts data"). Agents frequently misroute requests to the wrong tool. What is the best fix?
**Options:**
- A) Expand descriptions to state what data each tool returns and when to use it
- B) Create a routing classifier to select the correct tool before execution
- C) Use more specific tool names and keep descriptions minimal
- D) Add system prompt instructions describing each tool's purpose

**Correct Answer:** A
**Feedback:** Detailed descriptions are the primary tool selection mechanism for models. Clear descriptions directly address misrouting. B adds complexity. C ignores that descriptions are critical. D is less reliable than tool descriptions.
**Theory Reference:** Domain 2.1 - Tool naming and description clarity

---

## Question 153
**Title:** Hook Placement for Input Validation vs Output Transformation
**Situation:** Your system must: (1) validate user inputs are in correct format before tool execution, and (2) normalize all tool outputs to consistent timestamp format. Should these use PreToolUse or PostToolUse hooks?
**Options:**
- A) Both should use PreToolUse since it intercepts all tool interactions
- B) Input validation uses PreToolUse; output normalization uses PostToolUse
- C) Both should use PostToolUse for consistency across the system
- D) Use system prompts instead of hooks for both operations

**Correct Answer:** B
**Feedback:** PreToolUse intercepts outgoing calls (perfect for validation). PostToolUse intercepts results before the model processes them (perfect for normalization). Using the correct hook for each purpose ensures proper data flow. A applies wrong hook for output. C applies wrong hook for input. D sacrifices deterministic guarantees.
**Theory Reference:** Domain 1.5 - Hook placement for data normalization

---

## Question 154
**Title:** Context Passing to Subagents and Data Structure
**Situation:** Your coordinator has 200 lines of customer feedback. It needs a subagent to classify this data. How should the coordinator pass this?
**Options:**
- A) Pass the raw feedback directly in the subagent prompt
- B) Summarize it to 10 key points; pass the summary
- C) Group it into sections: issues, requests, praise; pass it
- D) Store it in a vector database; give the subagent a query

**Correct Answer:** A
**Feedback:** Raw feedback preserves all information and nuance for classification. The subagent can see the full context and make better decisions. B loses important details. C requires preprocessing the subagent could do better. D adds unnecessary infrastructure.
**Theory Reference:** Domain 1.3 - Context passing; Domain 1.2 - Result aggregation

---

## Question 155
**Title:** Retryable vs Non-retryable Error Handling
**Situation:** Your MCP tool integration encounters three errors: (1) network timeout (transient), (2) malformed input parameters (validation), (3) API rate limit hit (transient). Which should be retried?
**Options:**
- A) Retry only (1); escalate or fix (2) and (3)
- B) Retry all three with exponential backoff
- C) Retry (1) and (3); escalate or fix (2)
- D) Never retry; escalate all errors to a human

**Correct Answer:** C
**Feedback:** Transient errors like timeouts (1) and rate limits (3) benefit from retry with backoff. Validation errors (2) need input correction, not retries. Retrying (2) wastes resources. B includes a non-retryable error. A skips a legitimate retry candidate. D over-escalates errors that can be automatically recovered.
**Theory Reference:** Domain 2.2 - Error categorization and retry decisions

---

## Question 156
**Title:** System Prompt Specificity and Agent Behavior
**Situation:** Your agent's system prompt says: "Always check customer eligibility before processing requests." The agent calls `check_eligibility` even for simple FAQ queries, wasting tokens. What is the best fix?
**Options:**
- A) Remove the instruction; tool descriptions will guide the agent correctly
- B) Use a more expensive model that better understands context nuances
- C) Add a precondition hook that blocks checks on certain request types
- D) Refine it to: "Before refunds or returns, verify customer eligibility"

**Correct Answer:** C
**Feedback:** A precondition hook provides deterministic control, preventing checks on specific request types (e.g., FAQ). Hooks guarantee behavior; prompts only make it more likely. A loses safety guardrails. B doesn't address the issue. D still relies only on prompts which can't guarantee prevention.
**Theory Reference:** Domain 1.1 - Agent loop control; Domain 1.5 - Hook placement

---

## Question 157
**Title:** Tool Specialization vs General Tool with Parameters
**Situation:** Your document processing system offers specialized tools (`extract_text_from_pdf`, `extract_text_from_docx`, `extract_text_from_image`) and a proposed alternative: one general `extract_text` tool with a `format` parameter. Testing shows agents misuse the general tool (e.g., selecting wrong format parameter). Which approach is more reliable?
**Options:**
- A) Specialized tools are more reliable; agents select based on tool names
- B) General tool is simpler and maintainable; parameter selection is equally reliable
- C) Both approaches have equal reliability; choose based on code organization
- D) Specialized tools are only beneficial if you have 5+ format types

**Correct Answer:** A
**Feedback:** Tool names are the primary selection signal. Specialized names (`extract_text_from_pdf`) are unambiguous. Agents struggle with parameter combinations. Specialized tools prevent format mismatches. B underestimates the reliability difference. C ignores the tool selection mechanism. D sets an arbitrary threshold.
**Theory Reference:** Domain 2.3 - Tool specialization for reliability

---

## Question 158
**Title:** Multi-stage Workflow and Prompt Chaining
**Situation:** Your system performs four sequential stages: (1) log parsing, (2) severity classification, (3) root-cause analysis, (4) remediation recommendation. Should you use four separate prompts or one comprehensive prompt?
**Options:**
- A) One comprehensive prompt to minimize API calls
- B) Two combined prompts: (1+2) and (3+4) as a compromise
- C) Four separate prompts; each focuses deeply without distraction
- D) Use four specialized models, each optimized for its stage

**Correct Answer:** D
**Feedback:** Specialized models for each stage allow you to select the most cost-efficient model per task while maintaining quality. Each model is optimized for its specific domain. A sacrifices quality. B creates arbitrary boundaries. C uses one model for all stages, missing optimization.
**Theory Reference:** Domain 1.6 - Task decomposition strategies; Chapter 6.3 - Prompt chaining

---

## Question 159
**Title:** Coordinator Result Aggregation at Scale
**Situation:** Your coordinator spawns 5 subagents researching market segments. Each returns 18,000 tokens. Total: 90,000 tokens. The coordinator must synthesize findings. How should this be handled?
**Options:**
- A) Include all 90,000 tokens in the coordinator's context and process them
- B) Compress the 90,000 tokens with a separate model before the coordinator reads it
- C) Request each subagent provide a 5-key-point summary (1,500 tokens each)
- D) Store all outputs in a vector database; coordinator queries selectively

**Correct Answer:** D
**Feedback:** Vector databases enable semantic search, allowing the coordinator to retrieve only relevant findings without loading all 90,000 tokens. This scales better than summarization and preserves nuance. A wastes context. B adds latency. C loses detail through summarization.
**Theory Reference:** Domain 1.2 - Coordinator result aggregation; Domain 1.3 - Context passing

---

## Question 160
**Title:** Environment Variable Substitution and Secret Management
**Situation:** Your team's `.mcp.json` configures a GitHub MCP server. A developer hardcodes their `GITHUB_TOKEN` directly in `.mcp.json` and commits it publicly. How should credentials be managed?
**Options:**
- A) Have each developer manually pass tokens as CLI arguments at startup
- B) Use `${GITHUB_TOKEN}` in `.mcp.json`; store tokens in local `.env`
- C) Implement automatic token rotation and centralized management systems
- D) Use a local secrets manager that all developers can access at runtime

**Correct Answer:** B
**Feedback:** MCP supports `${VAR_NAME}` syntax for environment substitution. The `.mcp.json` references variables while secrets live in developers' local `.env` files—never committed. This keeps secrets out of version control. A is cumbersome. C adds complexity. D requires infrastructure.
**Theory Reference:** Domain 2.4 - MCP server secret management; Chapter 4.3 - Environment substitution
