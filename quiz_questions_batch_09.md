# Quiz Question Bank - Batch 09 (Questions 181-190)

## Question 181
**Title:** Context Window Management in Multi-step Workflows
**Situation:** Your coordinator spawns three subagents to analyze different aspects of a legal contract. After aggregating results, the coordinator's context window is at 180K tokens (out of 200K). A fourth subagent needs to be spawned for validation. What's the best approach?
**Options:**
- A) Summarize the first three results into a concise summary (~5K tokens) before spawning the fourth subagent
- B) Spawn the fourth subagent; overflow will be handled with graceful truncation mechanisms
- C) Ask all four subagents to produce shorter outputs to reduce total token consumption
- D) Cancel the validation step to completely avoid any context exhaustion risk

**Correct Answer:** A
**Feedback:** Progressive summarization reduces context burden mid-task. Instead of carrying 180K tokens forward, extract key findings into a 5K-token summary. This preserves information quality while creating room for new subagents. Truncation (B) loses critical data; cancellation (D) skips validation.
**Theory Reference:** Domain 1.2 & Chapter 1.5 - Context window management and progressive summarization

---

## Question 182
**Title:** Tool Description Clarity and Agent Misrouting
**Situation:** You have two tools: `validate_customer_payment` and `check_transaction_history`. Both accept a customer ID and could theoretically answer "Has this customer paid their invoice?" The agent frequently routes to the wrong one. Descriptions are minimal. What's the root cause?
**Options:**
- A) Tool descriptions lack specificity about input formats, return data, and use-case boundaries
- B) The tools have too much functional overlap and should be merged into a single tool
- C) The agent behavior is simply unreliable and requires a dedicated routing classifier
- D) This is normal LLM behavior that cannot be improved by configuration alone

**Correct Answer:** A
**Feedback:** Clear, specific tool descriptions are the primary selection mechanism. "Validates customer payment status (checks if most recent payment was received within 30 days)" is far more precise than "checks payment information." Tool descriptions should include example inputs, return data, and when to use each vs alternatives.
**Theory Reference:** Domain 2.1 - Tool naming and description clarity

---

## Question 183
**Title:** Error Categorization for Smart Recovery
**Situation:** A tool returns: `{"isError": true, "errorCategory": "timeout", "isRetryable": true}`. Another returns: `{"isError": true, "errorCategory": "auth_failed", "isRetryable": false}`. How should an agent handle these differently?
**Options:**
- A) Treat both the same way; retry with exponential backoff
- B) Retry the timeout with backoff; for auth failure, log the error and escalate
- C) Ignore both errors and proceed as if the tools succeeded
- D) Retry only if the result is critical; otherwise skip

**Correct Answer:** B
**Feedback:** Structured error metadata enables intelligent recovery. Transient errors (timeouts) benefit from retries with backoff—the operation may succeed on the next attempt. Auth failures are non-retryable; retrying won't fix the underlying permission issue. Log the error, escalate, or use alternative credentials.
**Theory Reference:** Domain 2.2 & 5.3 - Error categorization and retry decisions

---

## Question 184
**Title:** Prompt Chaining vs Monolithic Prompts for Complex Tasks
**Situation:** Your system needs to: (1) extract figures from financial reports, (2) validate for consistency, (3) reconcile against account ledgers, (4) generate a compliance summary. Should you use one comprehensive prompt or separate prompts per stage?
**Options:**
- A) One comprehensive prompt covering all four stages minimizes API calls and costs
- B) Separate prompts per stage; each focuses deeply without dilution for better quality
- C) Two combined prompts: extraction+validation together, then reconciliation+summary separately
- D) Four different specialized models, each optimized for a particular workflow stage

**Correct Answer:** B
**Feedback:** Prompt chaining (sequential focused prompts) outperforms monolithic prompts for complex workflows. One prompt juggling four tasks dilutes focus and reasoning quality. Each stage gets clearer instructions, intermediate outputs serve as quality checkpoints, and errors in one stage don't cascade into later stages.
**Theory Reference:** Domain 1.6 & Chapter 6.3 - Prompt chaining

---

## Question 185
**Title:** Subagent Specificity and Output Conciseness
**Situation:** You spawn a subagent with: "Research machine learning fairness issues." It returns a 250K-token comprehensive report. You only need: (1) recent regulations, (2) common bias types, (3) mitigation strategies. How to fix this?
**Options:**
- A) Accept the 250K tokens; that is the nature of comprehensive research work
- B) Use a specific task prompt: "Research recent fairness regulations, hiring bias types, and mitigation strategies in bullet format."
- C) Ask the subagent to filter and summarize the report after completion for your specific needs
- D) Use a different subagent model that was trained with improved data filtering capabilities

**Correct Answer:** B
**Feedback:** Task specificity is the primary driver of output quality and conciseness. Vague prompts ("do research") produce unfocused, verbose output. Specific prompts with clear scope boundaries and format constraints produce targeted, concise results that fit the context window and reduce token waste.
**Theory Reference:** Domain 1.2 - Task specificity and subagent prompts

---

## Question 186
**Title:** Preventing Secrets in Configuration Files
**Situation:** Your team uses `.mcp.json` to configure MCP servers. One developer hardcodes their personal API token in the file: `"token": "sk-1234567890..."` and commits it. The token is now public. How should this be structured instead?
**Options:**
- A) Implement a token-rotation system that periodically invalidates and refreshes old tokens
- B) Have all developers manually pass tokens as startup command-line arguments when needed
- C) Use environment variable substitution: `"token": "${API_TOKEN}"` and store the actual token in `.env` (not version-controlled)
- D) Encrypt all secrets within the version-controlled config file using encryption keys

**Correct Answer:** C
**Feedback:** MCP servers support `${VAR_NAME}` syntax for environment variable substitution. Store the `.mcp.json` with variable references (no secrets) in version control. Actual tokens live in local `.env` files or shell environment variables, never in VCS. This separates configuration from secrets.
**Theory Reference:** Domain 2.4 & Chapter 4.3 - MCP server secret management

---

## Question 187
**Title:** PostToolUse Hook Placement for Data Normalization
**Situation:** Your system integrates three external APIs: Tool A returns Unix timestamps, Tool B returns ISO 8601 strings, Tool C returns human-readable dates. The agent needs to process all timestamps uniformly. Where should normalization occur?
**Options:**
- A) In the system prompt with explicit conversion instructions
- B) In a wrapper layer that calls tools and translates responses before returning
- C) In a PostToolUse hook after tool execution, before the agent processes results
- D) In a PreToolUse hook before each tool call

**Correct Answer:** C
**Feedback:** PostToolUse hooks intercept results after tool execution, before the agent processes them. This is the ideal place for data normalization—the agent sees consistent format across all tools. PreToolUse (D) normalizes inputs, not outputs. System prompts (A) cannot reliably enforce complex transformations.
**Theory Reference:** Domain 1.5 - Hook placement for data normalization

---

## Question 188
**Title:** Specialized Tools vs General Tools with Parameters
**Situation:** You're designing a file-fetching system and choosing between: (1) specialized tools (`fetch_pdf`, `fetch_csv`, `fetch_json`, `fetch_xml`) or (2) one general tool `fetch_file` with a `format` parameter. Which is more reliable for agent selection?
**Options:**
- A) General tool with parameters is simpler and requires fewer tool definitions
- B) Both approaches have equal reliability; choose based on maintainability preferences
- C) Specialized tools reduce selection ambiguity; agents more reliably choose the correct tool
- D) General tools are more flexible and agents can reason better about parameter combinations

**Correct Answer:** C
**Feedback:** Specialized tool names are more reliable than general tools with parameters. Agents struggle to reason about parameter combinations. Tool names (`fetch_pdf`, `fetch_csv`) are the first signal agents see and are self-describing. Combined with clear descriptions, specialized tools lead to better selection accuracy and fewer misroutes.
**Theory Reference:** Domain 2.3 - Tool specialization for reliability

---

## Question 189
**Title:** Planning Mode vs Direct Execution for Complex Changes
**Situation:** You need to refactor authentication across 40+ files spanning multiple services (API, frontend, admin panel). The impact of mistakes would be significant. Should you use Planning Mode or direct execution?
**Options:**
- A) Direct execution with incremental changes; you can revert if needed
- B) Planning Mode; it safely explores the codebase first, surfacing architectural dependencies before making changes
- C) Direct execution with detailed written instructions covering all anticipated issues
- D) Use manual code review; don't trust automation for large refactors

**Correct Answer:** D
**Feedback:** For large, high-impact changes spanning multiple services, manual code review by experienced architects ensures domain expertise is applied. While Planning Mode can assist in exploration, critical authentication refactors benefit from human oversight to catch subtle security issues and architectural trade-offs that automation may miss.
**Theory Reference:** Domain 3.4 - Planning Mode and large-scale refactoring decisions

---

## Question 190
**Title:** Skill Tool Constraints with allowed-tools
**Situation:** You create a `/code-review` skill intended only for analysis (linting, type checking, structure review). However, developers report it generates "fix" code that modifies files. How do you prevent unintended tool usage?
**Options:**
- A) Manually review every skill invocation before allowing execution by developers
- B) Update the system prompt to explicitly instruct against code modifications
- C) Implement a separate review-only skill without any code modification tools
- D) Use the `allowed-tools` field in SKILL.md frontmatter to restrict the skill to analysis-only tools

**Correct Answer:** D
**Feedback:** The `allowed-tools` field in SKILL.md provides architectural constraints that restrict which tools a skill can access. This enforces tool restrictions at the system level—prompts alone cannot reliably prevent tool usage. Specifying `allowed-tools: ["grep", "view"]` removes access to modification tools like `edit`.
**Theory Reference:** Domain 3.2 - Skills with allowed-tools constraints
