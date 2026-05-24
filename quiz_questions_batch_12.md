# Quiz Question Bank - Batch 12 (Questions 111-120)

## Question 111
**Title:** Context Window Management with Tool Results
**Situation:** Your agent completes a web search that returns 52 fields with 10,000+ tokens of results, but only 3 fields (title, link, summary) are actually relevant for the next step. The agent is approaching context limits. What is the best approach to manage this?
**Options:**
- A) Implement a PostToolUse hook to extract only relevant fields before the agent processes results
- B) Increase the max_tokens limit to accommodate all results without loss
- C) Discard the tool results entirely and ask the agent to use a different source
- D) Store the full results in a vector database and let the agent query it later

**Correct Answer:** A
**Feedback:** PostToolUse hooks intercept results before the model consumes them. Extract only the 3 relevant fields—reducing context from 10,000+ tokens to ~500 tokens. This preserves the agent's decision-making capacity without data loss. B is inefficient. C loses valuable data. D adds unnecessary complexity.
**Theory Reference:** Domain 1.5 - Hook placement for data normalization; Domain 2.2 - Structured error responses

---

## Question 112
**Title:** System Prompt and Unintended Tool Associations
**Situation:** Your customer support system instructs: "Always verify customer identity before processing." The agent now calls `get_customer` even for simple FAQs, wasting tokens. How to fix?
**Options:**
- A) Remove the instruction and rely on tool descriptions
- B) Use a less general tool to reduce temptation to call it
- C) Refine it to specific scenarios (e.g., "before refunds or account changes")
- D) Add a precondition hook to block unnecessary calls

**Correct Answer:** C
**Feedback:** Refining the instruction to concrete scenarios constrains when verification applies. A loses security guidance. B hides the problem. D is overengineering.
**Theory Reference:** Domain 1.1 - Agent loop control; Chapter 1.4 - System prompt

---

## Question 113
**Title:** Subagent Context Isolation and Result Aggregation
**Situation:** Your coordinator spawns 3 subagents to research different market sectors. Each returns 25K tokens of analysis. The coordinator needs to synthesize findings across sectors. What is the most efficient approach?
**Options:**
- A) Have subagents provide 5-key-points summaries (2-3K tokens each) that the coordinator synthesizes from structured summaries
- B) Request subagents to provide raw output, then use a separate LLM to compress before coordinator synthesis
- C) Include the full 75K tokens in the coordinator's context and let it reason across all data
- D) Use vector embeddings to store subagent outputs and selectively retrieve relevant chunks

**Correct Answer:** A
**Feedback:** Structured summaries from subagents reduce context from 75K to ~9K tokens. The coordinator synthesizes from high-signal summaries, preserving quality while scaling. B adds latency. C wastes context on repetitive detail. D introduces unnecessary infrastructure.
**Theory Reference:** Domain 1.2 - Coordinator result aggregation; Domain 1.3 - Context passing

---

## Question 114
**Title:** Tool Specialization vs General Tools with Parameters
**Situation:** Design document processing with specialized tools (`extract_pdf`, `extract_json`, `extract_csv`) or one general `parse_document` tool with `format` parameter. Which is more reliable for agent selection?
**Options:**
- A) Specialized tools; agents select based on tool names reliably
- B) General tool with parameters; simpler and maintainable code
- C) Equally reliable; choose based on code organization
- D) Only specialize if you have 5+ document types

**Correct Answer:** A
**Feedback:** Tool names are the primary selection signal. Agents struggle with parameter combinations. Specialized tool names like `extract_pdf` are unambiguous; `parse_document` with format parameter requires inference. Specialized tools scale better.
**Theory Reference:** Domain 2.1 - Tool naming and description clarity; Domain 2.3 - Tool specialization

---

## Question 115
**Title:** Error Categorization and Recovery Decisions
**Situation:** Your MCP tool returns two errors: (1) API temporarily unreachable with `"isRetryable": true`, (2) Invalid input with `"isRetryable": false`. How should the orchestrator handle these?
**Options:**
- A) Retry both with exponential backoff; retrying never hurts recovery chances
- B) Retry (1) with backoff; log (2) and escalate or skip without retrying
- C) Treat both as failures and escalate immediately to a human
- D) Return a generic error message and let the agent decide what to do next

**Correct Answer:** B
**Feedback:** Structured error metadata enables intelligent recovery. Transient errors (timeouts) benefit from retries. Non-retryable errors (validation, permissions) need fixes, not retries. A wastes resources. C blocks recovery unnecessarily. D ignores the metadata.
**Theory Reference:** Domain 2.2 - Error categorization and retry decisions; Domain 2.4 - MCP server integration

---

## Question 116
**Title:** Prompt Chaining vs Single Comprehensive Prompt
**Situation:** Your system needs to (1) parse logs, (2) classify severity, (3) aggregate incidents, (4) generate report. Performance is acceptable. Use four sequential prompts or one comprehensive prompt?
**Options:**
- A) One comprehensive prompt to minimize API calls
- B) Alternate approaches based on complexity
- C) Sequential prompts; each stage focuses deeply
- D) Four specialized models per stage

**Correct Answer:** D
**Feedback:** Specialized models optimize for specific tasks better than diluted single prompts. Maintains quality while keeping latency acceptable. A sacrifices accuracy. B lacks consistency. C uses only LLMs.
**Theory Reference:** Domain 1.6 - Task decomposition strategies; Chapter 6.3 - Prompt chaining

---

## Question 117
**Title:** Environment Variable Substitution and Secret Management
**Situation:** Your `.mcp.json` contains a hardcoded token that was committed publicly. How should you manage MCP server credentials?
**Options:**
- A) Have developers pass tokens as command-line arguments at startup
- B) Use `"token": "${GITHUB_TOKEN}"` in `.mcp.json`; store tokens in `.env`
- C) Rotate the token and implement a centralized management system
- D) Use a secrets vault that all developers access at runtime

**Correct Answer:** B
**Feedback:** MCP servers support `${VAR_NAME}` environment variable substitution. The `.mcp.json` file (in VCS) references variables; secrets stay local. Prevents accidental commits. A is manual and error-prone. C is reactive. D adds infrastructure overhead.
**Theory Reference:** Domain 2.4 - MCP server secret management; Chapter 4.3

---

## Question 118
**Title:** Planning Mode for Complex Refactoring
**Situation:** Refactor critical authentication across 50+ files with interdependencies. Errors could block developers. Use Planning Mode or direct execution?
**Options:**
- A) Direct execution with incremental changes; revert if needed
- B) Ask senior engineer; don't automate critical systems
- C) Direct execution with detailed per-file instructions
- D) Planning Mode; explores and surfaces architectural impact

**Correct Answer:** D
**Feedback:** Planning Mode is designed for complex, high-impact changes. It explores dependencies before making changes. A risks expensive rework. B is inefficient. C still misses interdependencies.
**Theory Reference:** Domain 3.4 - Planning Mode for complex refactors

---

## Question 119
**Title:** Skills with Tool Restrictions and Allowed-Tools
**Situation:** Create a `/code-review` skill for code analysis only. Some developers report the skill suggests code modifications. How should you prevent this architecturally?
**Options:**
- A) Add detailed instructions in the prompt to prevent modifications
- B) Use `allowed-tools` in SKILL.md to restrict to analysis tools
- C) Require human review approval before any changes
- D) Create two separate skills for different purposes

**Correct Answer:** B
**Feedback:** The `allowed-tools` field in SKILL.md is an architectural constraint that prevents the skill from accessing modification tools. This constraint cannot be overridden by prompts alone. A is probabilistic and can fail. C adds unnecessary latency. D doubles maintenance burden and complexity.
**Theory Reference:** Domain 3.2 - Skills with allowed-tools constraints

---

## Question 120
**Title:** Transient vs Non-retryable Errors in Subagent Workflows
**Situation:** A web-search subagent times out (temporary unavailability). The coordinator receives an error response. How should the coordinator handle this failure?
**Options:**
- A) Immediately retry the search query with the same subagent
- B) Continue the workflow with empty results marked unavailable
- C) Return structured error context to enable coordinator recovery
- D) Escalate the entire workflow to human intervention

**Correct Answer:** C
**Feedback:** Structured error context (failure type, query, partial results) enables the coordinator to decide intelligently: retry with backoff, try alternative sources, or continue with partial results. A risks repeated failures. B masks the failure. D escalates unnecessarily when alternatives exist.
**Theory Reference:** Domain 1.2 - Coordinator error handling; Domain 2.2 - Structured error responses

