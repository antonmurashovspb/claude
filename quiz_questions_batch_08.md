# Quiz Question Bank - Batch 08 (Questions 81-90)

## Question 81
**Title:** Context Window Management in Large Multi-agent Workflows
**Situation:** Your coordinator spawns 5 parallel research subagents, each returning 30K tokens of unstructured findings. The coordinator needs to synthesize across all 150K tokens into a recommendation. However, your model's context window is 200K tokens, and after accounting for system prompt, instructions, and output space, you have only 120K tokens available for aggregation.
**Options:**
- A) Accumulate all unstructured findings and use a higher-tier model with a larger context window to handle the full 150K tokens
- B) Use a higher-tier model with a larger context window to avoid losing information
- C) Process subagent outputs sequentially in multiple synthesis passes to avoid context overflow
- D) Request each subagent to provide structured summaries (3-5 key findings each, ~2K tokens), then synthesize from the compressed output to stay within budget

**Correct Answer:** D
**Feedback:** Requesting structured summaries reduces raw token load from 150K to ~10K while preserving signal. The coordinator then synthesizes from concise, focused summaries with better quality. This is the most practical solution—structured summarization at the source beats attempting to compress or cascade processing.
**Theory Reference:** Domain 1.2 - Coordinator result aggregation with context awareness

---

## Question 82
**Title:** PreToolUse vs PostToolUse Hook Placement for Input Validation
**Situation:** Your document processing system has tools that accept file paths as input. Users frequently pass malformed paths (relative paths when absolute are required, paths with escape sequences). You want to normalize paths before tool execution—converting relative to absolute, sanitizing escape sequences—to prevent downstream tool failures.
**Options:**
- A) Add validation logic directly in each tool's input schema
- B) PreToolUse hook before tool execution to intercept and normalize input paths
- C) PostToolUse hook after tool execution to clean the results
- D) Create a separate validation tool that must be called before the main tool

**Correct Answer:** B
**Feedback:** PreToolUse hooks intercept outgoing tool calls. This is the correct layer for input transformation—normalize paths before passing to the tool, preventing failures. PostToolUse is for result transformation. A separate validation tool requires the agent to remember to call it.
**Theory Reference:** Domain 1.5 - Hook placement for input normalization

---

## Question 83
**Title:** Subagent Tool Specialization and Misuse Prevention
**Situation:** Your document review coordinator spawns a `content_analysis` subagent that should only extract and analyze text, never modify files. However, you notice the subagent occasionally attempts to generate "improved" versions of documents using code-modification tools, deviating from its intended analysis scope.
**Options:**
- A) Add a system prompt instruction warning the agent not to modify files
- B) Manually review subagent outputs before allowing execution
- C) Configure the subagent with `allowed-tools: ["read_file", "grep", "analyze_text"]` to restrict access to analysis-only tools
- D) Create a separate subagent role specifically for modification tasks and route all write operations to it

**Correct Answer:** C
**Feedback:** The `allowed-tools` field in subagent configuration provides architectural constraints that the system enforces—the subagent cannot access modification tools regardless of what the prompt suggests. This is more reliable than prompt-based restrictions.
**Theory Reference:** Domain 3.2 - Subagent tool constraints via allowed-tools

---

## Question 84
**Title:** Error Recovery in Nested Tool Calls
**Situation:** Your coordinator spawns a research subagent that calls a tool chain: `search` → `fetch_url` → `parse_content`. The `fetch_url` call returns `{"isError": true, "errorCategory": "validation", "isRetryable": false, "message": "Invalid URL format"}`. The subagent retries 3 times with the same URL before escalating.
**Options:**
- A) The subagent should immediately check `isRetryable` and skip retries for non-retryable errors, escalating with structured context
- B) Retrying non-retryable errors is harmless; it ensures recovery if the transient condition passes
- C) The coordinator should implement a global retry policy that overrides individual error metadata
- D) The subagent should modify the URL itself before retrying to resolve the validation error

**Correct Answer:** A
**Feedback:** Error metadata provides intelligence for recovery decisions. Transient errors (timeouts) benefit from retries; non-retryable errors (validation, permissions) don't. Checking `isRetryable: false` should immediately terminate retry loops and escalate with context for the coordinator to adjust the request or handle differently.
**Theory Reference:** Domain 2.2 - Error categorization and retry strategies

---

## Question 85
**Title:** Tool Naming and Agent Selection Reliability
**Situation:** Your system has four document processing tools with vague names: `process`, `handle`, `execute`, and `manage`. All four accept documents and return processed outputs. Agents frequently call the wrong tool, leading to inefficiency and poor results. You have limited time for refactoring.
**Options:**
- A) Rename tools to be specific and action-oriented: `extract_text_from_document`, `detect_entities_in_document`, `generate_summary_from_document`, `classify_document_type`
- B) Keep the names and expand descriptions; tool names are less important than clear descriptions
- C) Merge all four tools into a single `process_document` tool with format parameters
- D) Implement a routing classifier that pre-selects the correct tool for each request

**Correct Answer:** A
**Feedback:** Tool names are the primary signal agents use for selection. Vague names like `process` and `handle` force agents to reason about semantic differences in descriptions. Specific action-oriented names (`extract_text`, `detect_entities`, `generate_summary`) are self-describing and prevent misrouting. This directly addresses the root cause and is the most efficient fix.
**Theory Reference:** Domain 2.1 - Tool naming clarity for selection reliability

---

## Question 86
**Title:** MCP Server Integration and Shared Configuration
**Situation:** Your team has multiple developers working on a project that uses a GitHub MCP server. The `.mcp.json` file is version-controlled. A developer commits a hardcoded personal GitHub token in `.mcp.json`, making it public on GitHub. What is the proper recovery approach?
**Options:**
- A) Implement a token-rotation system that periodically invalidates all tokens and generates new ones
- B) Move the entire `.mcp.json` to each developer's local user config so tokens are never committed
- C) Store the token in a separate configuration file outside version control
- D) Use environment variable substitution: update `.mcp.json` to reference `${GITHUB_TOKEN}`, invalidate the compromised token, and have developers store tokens locally in `.env` files

**Correct Answer:** D
**Feedback:** MCP supports `${VAR_NAME}` substitution in `.mcp.json`. Store configuration in version control, secrets in local environment. First: invalidate the compromised token on GitHub. Then: update `.mcp.json` to use `${GITHUB_TOKEN}`, have developers store actual tokens in `.env` files (which are gitignored). This balances shared configuration with secret isolation.
**Theory Reference:** Domain 2.4 - MCP server secret management with environment variables

---

## Question 87
**Title:** Role-based Tool Allocation and Subagent Specialization
**Situation:** Your multi-agent system has a `coordinator` role and three `researcher` subagents. You want to prevent researchers from directly calling an `approve_and_publish` tool (a privileged operation). Currently, all researchers have access to all tools.
**Options:**
- A) Add a system prompt instruction to researchers not to use the tool
- B) Configure each researcher subagent with `allowed-tools` to exclude `approve_and_publish`, forcing all publication requests through the coordinator
- C) Implement a permissions layer in the tool itself that checks the caller's role
- D) Create a router subagent that intercepts tool calls and blocks unauthorized operations

**Correct Answer:** B
**Feedback:** The `allowed-tools` field enforces architectural access control at the subagent configuration level—researchers cannot access the tool, period. This is more reliable than prompt-based or runtime layer approaches. It's the correct layer for role-based tool allocation and prevents accidental misuse.
**Theory Reference:** Domain 2.3 - Tool allocation across agents with allowed-tools

---

## Question 88
**Title:** Session Forking for Parallel Investigation Approaches
**Situation:** Your coordinator needs to research a complex topic with two competing hypotheses: hypothesis A suggests using strategy X, while hypothesis B suggests strategy Y. Rather than pick one approach, you want to explore both in parallel and compare results.
**Options:**
- A) Run the research sequentially: complete hypothesis A investigation, then run hypothesis B investigation in the same session, comparing results afterward
- B) Use `fork_session` to spawn two independent investigation branches from shared context, run both in parallel, then merge findings
- C) Spawn two separate subagents, each investigating one hypothesis independently, then have the coordinator synthesize
- D) Create two separate sessions manually and coordinate results through external messaging

**Correct Answer:** B
**Feedback:** `fork_session` creates independent investigation branches that preserve shared initial context while allowing divergent exploration. Both branches run from the same starting point, then diverge. This is superior to sequential investigation (faster), separate subagents (less context awareness), or manual session management (complex).
**Theory Reference:** Domain 1.7 - Session forking for parallel investigation branches

---

## Question 89
**Title:** CLAUDE.md Hierarchy and Convention Application Scope
**Situation:** Your team has testing conventions that apply across many directories: tests co-located with code, named `*.test.ts`, using a specific assertion library. You have two options: (1) add conventions to a root `CLAUDE.md`, or (2) create a `.claude/rules/tests.md` with `paths: ["**/*.test.ts"]`.
**Options:**
- A) Use the root `CLAUDE.md` because it's simpler and centralizes all conventions in one place
- B) Create a `CLAUDE.md` file in each directory that contains tests to ensure local conventions are applied
- C) Use `.claude/rules/tests.md` with path patterns to load testing conventions only when editing test files, preserving context
- D) Use `.claude/commands/` to define testing tools that apply uniformly across the repository

**Correct Answer:** C
**Feedback:** Path-scoped rules in `.claude/rules/` load **only** when editing matching files. For testing conventions spread across many directories, this approach saves context tokens by applying conventions conditionally (via glob patterns) rather than always loading them. This is preferable to a root `CLAUDE.md` which would always be loaded.
**Theory Reference:** Domain 3.3 - Path-specific rules for conditional convention loading

---

## Question 90
**Title:** Planning Mode vs Direct Execution for Large Refactors
**Situation:** You need to refactor a payment processing system: migrate from one payment provider to another, affecting 45+ files, multiple service boundaries, and critical business logic. Mistakes could cause transaction failures. Do you use Planning Mode or direct execution?
**Options:**
- A) Planning Mode to safely explore the codebase, identify dependencies, surface architectural impact before making changes
- B) Direct execution with incremental changes and Git history for rollback if issues arise
- C) Use a junior developer to explore the codebase first, then execute changes once architecture is understood
- D) Decompose the refactor into many small tasks and assign each to a different agent to parallelize

**Correct Answer:** A
**Feedback:** Planning Mode is specifically designed for complex, high-impact changes. It safely explores the codebase before committing changes, identifies critical dependencies, and surfaces architectural considerations. This is exactly the scenario where Planning Mode should be used—large refactors affecting many files and business-critical systems.
**Theory Reference:** Domain 3.4 - Planning Mode for complex, high-impact refactors
