# Quiz Question Bank - Batch 14 (Questions 131-140)

## Question 131
**Title:** Subagent Context Isolation and Information Inheritance
**Situation:** Your coordinator collects market research from three subagents researching different regions. Subagent 1 (North America) completes and returns 15K tokens of findings. You then invoke Subagent 2 (Europe). Does Subagent 2 automatically see Subagent 1's findings, or must the coordinator pass them explicitly?
**Options:**
- A) Subagent 2 automatically inherits the coordinator's full history, including Subagent 1's findings, by default
- B) Subagent 2 sees Subagent 1's findings only if they share the same session ID
- C) Subagent 2 operates with isolated context; the coordinator must explicitly include Subagent 1's output in the Task prompt
- D) Subagent 2 can access Subagent 1's findings through a shared database if configured in the MCP server

**Correct Answer:** C
**Feedback:** Subagents operate with isolated context—each one starts fresh without the coordinator's history. If Subagent 2 needs findings from Subagent 1, the coordinator must include them explicitly in a structured format (e.g., JSON summary) within the Task prompt. This isolation provides context control and prevents unintended information mixing.
**Theory Reference:** Domain 1.3 - Configuring Subagent Calls, Context Passing, and Spawning

---

## Question 132
**Title:** Deterministic vs Probabilistic Compliance: Hooks vs Prompts
**Situation:** Your financial system must enforce a policy: "No refund over $5,000 without manager approval." The system prompt currently instructs agents about this policy, but audits show 3% of violations slip through production. What is the most reliable enforcement method?
**Options:**
- A) Implement a PreToolUse hook that blocks refund tool calls exceeding $5,000 and redirects to escalation
- B) Use a vector database to store refund history and flag suspicious patterns before execution
- C) Add a validation step after refunds that reverses transactions violating the policy
- D) Update the system prompt with more detailed escalation examples and lower temperature to 0.1

**Correct Answer:** A
**Feedback:** System prompts provide probabilistic compliance—models usually follow but sometimes deviate (3% failure rate). Hooks provide deterministic guarantees: a PreToolUse hook can prevent prohibited actions before execution. For business-critical policies with financial consequences, architecture-level enforcement (hooks) always outperforms prompt-based guidance.
**Theory Reference:** Domain 1.5 - Agent SDK Hooks for Intercepting Tool Calls and Normalizing Data

---

## Question 133
**Title:** Tool Naming Clarity and Selection Accuracy
**Situation:** Your document system has three functions: (1) parse CSV files, (2) extract tables from PDFs, (3) parse XML documents. A developer proposes a single general-purpose tool `parse_document` with a `format` parameter instead of three specialized tools. Which approach yields better agent selection accuracy?
**Options:**
- A) One general tool is better; fewer choices reduces decision complexity
- B) Three specialized tools with clear names are more reliable; agents select based on tool names as the primary signal
- C) Both approaches have equal reliability; the difference depends on model version
- D) The question is moot; tool descriptions determine selection, not names

**Correct Answer:** B
**Feedback:** Tool names are the primary signal agents use for selection. `parse_csv`, `extract_pdf_table`, and `parse_xml` are unambiguous. A single `parse_document` tool requires agents to reason about parameter combinations, which they do less reliably. Specialized tool names eliminate ambiguity and improve accuracy.
**Theory Reference:** Domain 2.1 - Designing Tool Interfaces with Clear Descriptions

---

## Question 134
**Title:** Error Metadata and Intelligent Recovery Decisions
**Situation:** Your web-search MCP tool encounters two errors in a single workflow: (1) `"errorCategory": "timeout", "isRetryable": true` (API temporarily unreachable), and (2) `"errorCategory": "invalid_input", "isRetryable": false` (malformed search query). How should the orchestrator handle each?
**Options:**
- A) Retry both with exponential backoff; retrying is always safe
- B) Treat both as failures and escalate immediately to a human operator
- C) Retry (1) with backoff; for (2), log the error and escalate or fix the input without retrying
- D) Cache the errors and attempt recovery 24 hours later when services might be more stable

**Correct Answer:** C
**Feedback:** Structured error metadata enables intelligent recovery. Transient errors (timeouts, temporarily unavailable) benefit from retries with exponential backoff. Non-retryable errors (validation, malformed input) indicate the request itself is invalid—retrying with the same input will always fail. Log and escalate or adjust the input instead.
**Theory Reference:** Domain 2.2 - Implementing Structured Error Responses for MCP Tools

---

## Question 135
**Title:** Prompt Chaining vs Single Comprehensive Prompt
**Situation:** Your system consistently performs three steps in order: (1) extract invoice data, (2) validate extracted values against source document, (3) generate payment recommendation. The workflow is always the same. Should you use three sequential prompts or one comprehensive prompt?
**Options:**
- A) One comprehensive prompt to minimize API calls and latency
- B) Mix both: sequential for extraction/validation, then one comprehensive prompt for recommendation
- C) Use three different specialized models, one per step
- D) Three sequential prompts; each stage receives focused attention without dilution

**Correct Answer:** D
**Feedback:** When steps are predictable and always occur in order, fixed prompt chaining is ideal. Each stage receives focused prompting without competing objectives, resulting in higher quality per stage. A single comprehensive prompt dilutes attention and produces less consistent results. Prompt chaining trades API calls for quality—a worthwhile tradeoff for deterministic workflows.
**Theory Reference:** Domain 1.6 - Task Decomposition Strategies for Complex Workflows

---

## Question 136
**Title:** Coordinator Task Decomposition Scope and Subagent Specialization
**Situation:** Your coordinator researches "emerging technologies in developing nations." It decomposes the task into: "AI in sub-Saharan Africa," "blockchain in South Asia," "quantum computing in Latin America." The final report covers only three narrow technologies and misses renewable energy, biotechnology, and telecommunications. What's the root cause?
**Options:**
- A) The research subagents lack sufficient time for thorough investigation
- B) The coordinator decomposed the topic too narrowly, missing broader technology categories
- C) The synthesis agent fails to identify gaps in coverage and request additional research
- D) The vector database does not retrieve sufficient sources on emerging technologies

**Correct Answer:** B
**Feedback:** The coordinator's task decomposition was overly narrow. "Emerging technologies" encompasses many domains (renewable energy, biotech, telecom, AI, blockchain, quantum). By decomposing only by geography while restricting to three specific technologies, the coordinator predetermined incomplete coverage. Better decomposition: split by geography AND technology categories, or use a synthesis pass to identify and remediate gaps.
**Theory Reference:** Domain 1.2 - Orchestrating Multi-agent Systems (Coordinator–Subagent)

---

## Question 137
**Title:** Planning Mode for High-Impact Architectural Changes
**Situation:** You need to refactor a payment processing system across 42 files with intricate interdependencies between auth, validation, and settlement modules. Errors could block payment processing for days. Should you use Planning Mode or direct incremental execution?
**Options:**
- A) Direct execution with git reverting available if issues arise later
- B) Incremental execution: one file at a time, running full test suite after each change
- C) Manual code review from senior engineers before starting any work
- D) Planning Mode to explore dependencies and surface architectural impact before changes

**Correct Answer:** D
**Feedback:** Planning Mode is specifically designed for complex, high-impact changes. It explores the codebase before making modifications, identifies service boundaries, creates a dependency graph, and surfaces architectural considerations. This prevents expensive rework and ensures changes respect module boundaries. Direct execution (A, B) risks missing critical dependencies across the 42 files.
**Theory Reference:** Domain 3.4 - Deciding When to Use Planning Mode vs Direct Execution

---

## Question 138
**Title:** Lost-in-the-Middle Effect and Information Placement
**Situation:** A coordinator collects synthesis input from four research subagents. Each returns key findings plus supporting details. When concatenated sequentially and sent to a synthesis agent, findings from the second subagent (middle position) are consistently missing from the final summary. How should the coordinator structure the input?
**Options:**
- A) Extract key findings into a dedicated "Key Facts" section at the start, separate from supporting details
- B) Reorder subagent results so subagent 2 appears first or last instead of in the middle
- C) Increase max_tokens so the synthesis agent has more space to process all input
- D) Split the synthesis into two passes: first for subagents 1-2, then for subagents 3-4

**Correct Answer:** A
**Feedback:** Models reliably process information at the start and end of long inputs (lost-in-the-middle effect). Extract transactional facts from all subagent findings into a dedicated "Key Facts" section placed at the beginning, then include supporting details separately. This structure ensures critical information is not lost due to position effects.
**Theory Reference:** Domain 5.1 - Managing Conversation Context to Preserve Critical Information

---

## Question 139
**Title:** Tool Restrictions via `allowed-tools` Field
**Situation:** You create a `/code-review` skill for code analysis only—it should use linting and analysis tools, not code modification tools. However, developers report the skill sometimes suggests modifications that create new files. How should you prevent this architecturally?
**Options:**
- A) Add stronger instructions to the system prompt to avoid modification suggestions
- B) Use the `allowed-tools` field in SKILL.md frontmatter to restrict the skill to analysis tools
- C) Require human approval before the skill can execute any tool calls
- D) Create a separate skill for modifications and route developers to the correct one

**Correct Answer:** B
**Feedback:** The `allowed-tools` field in SKILL.md is an architectural constraint that prevents the skill from accessing modification tools. This constraint cannot be overridden by prompts alone. System prompts (A) provide probabilistic guidance and can fail. B provides deterministic enforcement at the platform level.
**Theory Reference:** Domain 3.2 - Creating and Configuring Custom Slash Commands and Skills

---

## Question 140
**Title:** Environment Variable Substitution and Secret Management
**Situation:** A team member hardcodes a GitHub API token directly in `.mcp.json` and commits it publicly. Now the token is exposed in the repository history. Which approach should teams use for managing MCP credentials going forward?
**Options:**
- A) Implement a token-rotation system that periodically invalidates and regenerates tokens
- B) Have developers pass tokens as command-line arguments every time they start Claude
- C) Use MCP environment variable substitution: `"token": "${GITHUB_TOKEN}"` in `.mcp.json`; store actual tokens in `.env`
- D) Require all MCP servers to authenticate through a centralized secrets vault

**Correct Answer:** C
**Feedback:** MCP servers support `${VAR_NAME}` environment variable substitution. The `.mcp.json` file (committed to version control) references environment variables; secrets stay local in `.env` or shell environment variables. This prevents accidental commits of secrets. B is manual and error-prone. A is reactive. D adds infrastructure overhead.
**Theory Reference:** Domain 2.4 - Integrating MCP Servers into Claude Code and Agent Workflows

---
