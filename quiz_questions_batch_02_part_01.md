# Quiz Question Bank - Batch 02 Part 01 (Questions 61-70)

## Question 61
**Title:** Agent Loop Iteration Limits
**Situation:** Your agent is designed to solve complex problems by reasoning through multiple steps. You set a hard iteration limit of 10 tool calls. The agent solves the problem in 8 calls on simple cases but might need 15+ calls on complex problems.
**Options:**
- A) Increase the iteration limit to 20 for all users to handle complex cases safely
- B) Use `stop_reason` to control the loop, not iteration limits. The agent stops naturally on `"end_turn"`; iteration limits override the model's decision-making
- C) Remove the limit entirely and let the agent run indefinitely to avoid failures
- D) Add a complexity detector to dynamically adjust the iteration limit per task

**Correct Answer:** B
**Feedback:** `stop_reason` signals when the model is done (end_turn) or needs more tools (tool_use). Arbitrary iteration limits override the model's reasoning. The agent should decide when to stop, not a hard counter.
**Theory Reference:** Domain 1.1 - Agent loop control via stop_reason

---

## Question 62
**Title:** Coordinator Result Aggregation
**Situation:** You have a coordinator spawning 3 subagents to research different aspects of renewable energy. Subagent A returns 15K tokens of findings, Subagent B returns 22K tokens, Subagent C returns 18K tokens. Total: 55K tokens. The coordinator needs to synthesize these into a concise executive summary. How should the coordinator handle this volume?
**Options:**
- A) The coordinator should request summaries from each subagent before aggregating, then synthesize from structured summaries
- B) Use vector databases to store and retrieve subagent outputs selectively
- C) Each subagent should produce less output overall to reduce context burden
- D) Ask subagents to highlight "key findings" sections within their output for faster coordinator review

**Correct Answer:** A
**Feedback:** Instead of synthesizing from raw 55K-token outputs, have subagents provide structured summaries (3-5 key points each). The coordinator then synthesizes from concise summaries (~5K tokens). This scales better and maintains quality.
**Theory Reference:** Domain 1.2 - Coordinator result aggregation strategy

---

## Question 63
**Title:** Ambiguous vs Clear Tool Names
**Situation:** Your document processing system has tools named `process`, `handle`, and `manage`. All three can work with documents. Agents frequently misroute work to the wrong tool, causing reduced effectiveness.
**Options:**
- A) Tool names should be specific and action-oriented; they're the first signal agents see
- B) Use only one generic tool with a format parameter to simplify the agent's choices
- C) General names like `process` are fine; descriptions handle specificity
- D) Tool names are secondary to good descriptions; naming doesn't significantly impact selection

**Correct Answer:** A
**Feedback:** Tool names are the first signal an agent sees. Generic names like `process` are ambiguous. Specific names like `extract_text_from_pdf` are self-describing. Clear naming + clear descriptions prevent misrouting.
**Theory Reference:** Domain 2.1 - Tool naming and description clarity

---

## Question 64
**Title:** Environment Variables in `.mcp.json`
**Situation:** Your team has a shared `.mcp.json` with a GitHub MCP server. One developer hardcodes their personal `GITHUB_TOKEN` in `.mcp.json` and commits it. The token is now public in the repository.
**Options:**
- A) Use environment variable substitution: `"token": "${GITHUB_TOKEN}"` in `.mcp.json`, store actual token in local `.env` or shell environment
- B) Store tokens in a separate config file and don't commit it
- C) Have developers manually pass tokens as command-line arguments at startup
- D) Implement a token-rotation system that periodically invalidates old tokens

**Correct Answer:** A
**Feedback:** MCP servers support `${VAR_NAME}` syntax for environment variable substitution. Never commit secrets. The `.mcp.json` file references environment variables (which live locally), keeping secrets out of version control.
**Theory Reference:** Domain 2.4 & Chapter 4.3 - MCP server secret management

---

## Question 65
**Title:** Hook Placement: PreToolUse vs PostToolUse
**Situation:** You want to normalize all timestamps returned by external tools. Tool A returns Unix timestamps, Tool B returns ISO 8601, Tool C returns human-readable strings. Where should the normalization happen?
**Options:**
- A) PostToolUse hook after all tool calls, before the agent processes results
- B) In a wrapper layer that calls each tool and translates the response
- C) PreToolUse hook before each tool call to standardize input formats
- D) In the system prompt with explicit conversion instructions

**Correct Answer:** A
**Feedback:** PostToolUse hooks intercept results after tool execution, before the agent processes them. This is perfect for data normalization—the agent sees consistent format across all tools. PreToolUse is for request transformation, not response normalization.
**Theory Reference:** Domain 1.5 - Hook placement for data normalization

---

## Question 66
**Title:** Specialized Tools vs General-purpose with Parameters
**Situation:** You're choosing between two architectures: (1) specialized tools (`fetch_pdf`, `fetch_image`, `fetch_json`) or (2) one general tool `fetch_file` with a `format` parameter. Which is more reliable for agent selection?
**Options:**
- A) Specialized tools reduce selection ambiguity; agents more reliably choose the right tool vs a general tool with parameters
- B) General tool with parameters is simpler and more maintainable
- C) Both approaches have equal reliability; pick based on code organization
- D) Use a single tool and require agents to parse all formats

**Correct Answer:** A
**Feedback:** Specialized tools are more reliable than general tools with parameters. Agents can't easily reason about parameter combinations. Distinct tool names and descriptions (`fetch_pdf`, `fetch_image`) lead to better selection accuracy.
**Theory Reference:** Domain 2.3 - Tool specialization for reliability

---

## Question 67
**Title:** Transient vs Non-retryable Errors
**Situation:** Two MCP tool errors: (1) `{"isError": true, "errorCategory": "timeout", "isRetryable": true}` - API temporarily unreachable, (2) `{"isError": true, "errorCategory": "validation", "isRetryable": false}` - malformed input. How should these be handled?
**Options:**
- A) Retry both with exponential backoff; retrying never hurts
- B) Retry (1) with backoff; don't retry (2), log the validation error and escalate or skip
- C) Treat both as failures and immediately escalate to a human
- D) Ignore both errors and continue as if they succeeded

**Correct Answer:** B
**Feedback:** Structured error metadata enables smart retry decisions. Transient errors (timeouts, temporarily unavailable) benefit from retries with backoff. Non-retryable errors (validation, permissions) don't—fix the input or escalate instead.
**Theory Reference:** Domain 2.2 & 5.3 - Error categorization and retry decisions

---

## Question 68
**Title:** Multi-stage Prompting for Complex Workflows
**Situation:** Your system needs to (1) extract data, (2) validate it, (3) transform it, (4) export it. Should this be one prompt or four separate prompts?
**Options:**
- A) Four separate prompts in sequence (prompt chaining); each stage focuses deeply without attention dilution
- B) One comprehensive prompt covering all stages to minimize API calls
- C) Combine stages with similar concerns: extraction+validation in one, transformation+export in another
- D) Use four different models, each optimized for one stage

**Correct Answer:** A
**Feedback:** Separate prompts for distinct stages enable focused reasoning. One prompt trying to handle all four stages dilutes attention. Prompt chaining (sequential, focused prompts) is ideal for complex workflows—quality over API call count.
**Theory Reference:** Domain 1.6 & Chapter 6.3 - Prompt chaining

---

## Question 69
**Title:** Planning Mode and Safe Exploration
**Situation:** You're in Claude Code and need to refactor a critical authentication system (50 files, many dependencies). The impact of a mistake would be significant. Should you use Planning Mode or direct execution?
**Options:**
- A) Planning Mode; it safely explores the codebase before you commit any changes, surfacing architectural impact
- B) Direct execution with incremental changes; you can revert if needed
- C) Ask a senior engineer to do it; don't risk automation on critical systems
- D) Direct execution is fine; Planning Mode only for small refactors

**Correct Answer:** A
**Feedback:** Planning Mode is specifically designed for complex, high-impact changes. It explores the codebase, identifies dependencies, and surfaces architectural considerations before making changes. This is exactly when you should use it.
**Theory Reference:** Domain 3.4 - Planning Mode for complex refactors

---

## Question 70
**Title:** Skills and Allowed Tools
**Situation:** You create a `/code-review` skill that should use only linting and analysis tools, not code modification tools. However, developers report it sometimes generates "fix" suggestions that modify code. How do you prevent this?
**Options:**
- A) Use the `allowed-tools` field in the skill's SKILL.md frontmatter to restrict the skill to analysis tools only
- B) Trust the system prompt to prevent modification attempts
- C) Manually review every skill invocation before allowing execution
- D) Remove the skill and implement code review differently

**Correct Answer:** A
**Feedback:** The `allowed-tools` field in SKILL.md restricts what tools a skill can use. This prevents the skill from accessing modification tools—an architectural constraint that prompts cannot override.
**Theory Reference:** Domain 3.2 - Skills with allowed-tools constraints
