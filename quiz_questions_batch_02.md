# Quiz Question Bank - Batch 02 (Questions 61-110)

## Question 61
**Title:** Agent Loop Iteration Limits
**Situation:** Your agent is designed to solve complex problems by reasoning through multiple steps. You set a hard iteration limit of 10 tool calls. The agent solves the problem in 8 calls on simple tasks but needs 15 calls for complex problems. Users with complex tasks report "Agent stopped working" even though solutions exist.
**Options:**
- A) Increase the iteration limit to 20 for all users
- B) Use `stop_reason` to control the loop, not iteration limits. The agent stops naturally on `"end_turn"`; iteration limits override the model's decision-making
- C) Add a complexity detector to dynamically set iteration limits
- D) Remove tools to force faster completion

**Correct Answer:** B
**Feedback:** `stop_reason` signals when the model is done (end_turn) or needs more tools (tool_use). Arbitrary iteration limits override the model's reasoning. The agent should decide when to stop, not a counter. Let the model think as long as needed; use timeouts for safety, not iteration counts.
**Theory Reference:** Domain 1.1 - Agent loop control via stop_reason

---

## Question 62
**Title:** Coordinator Result Aggregation
**Situation:** You have a coordinator spawning 3 subagents to research different aspects of renewable energy. Subagent A returns 15K tokens of findings, Subagent B returns 22K tokens, Subagent C returns 18K tokens. The coordinator tries to synthesize all 55K tokens into a final report but loses key findings. What's the issue?
**Options:**
- A) The subagents produced too much output; each should produce less
- B) The coordinator should request summaries from each subagent before aggregating, then synthesize from structured summaries
- C) Use vector databases to store subagent outputs
- D) Ask subagents to highlight "key findings" in their output

**Correct Answer:** B
**Feedback:** Instead of synthesizing from raw 55K-token outputs, have subagents provide structured summaries (3-5 key points each). The coordinator then synthesizes from concise summaries (~5K tokens total) rather than overwhelming raw data. This is aggregation best practice—request summaries, not raw findings.
**Theory Reference:** Domain 1.2 - Coordinator result aggregation strategy

---

## Question 63
**Title:** Ambiguous vs Clear Tool Names
**Situation:** Your document processing system has tools named `process`, `handle`, and `manage`. All three can work with documents. Agents frequently misroute work. You rename them to `extract_text_from_pdf`, `parse_structured_data_from_form`, and `validate_document_format`. Misrouting drops to near zero.
**Options:**
- A) Tool names don't matter; it's all about descriptions
- B) General names like `process` are fine; descriptions handle specificity
- C) Tool names should be specific and action-oriented; they're the first signal agents see
- D) Use only one generic tool

**Correct Answer:** C
**Feedback:** Tool names are the first signal an agent sees. Generic names like `process` are ambiguous. Specific names like `extract_text_from_pdf` are self-describing. Clear naming + clear descriptions = reliable tool selection. The near-zero misrouting demonstrates the impact.
**Theory Reference:** Domain 2.1 - Tool naming and description clarity

---

## Question 64
**Title:** Environment Variables in `.mcp.json`
**Situation:** Your team has a shared `.mcp.json` with a GitHub MCP server. One developer hardcodes their personal `GITHUB_TOKEN` in `.mcp.json` and commits it. The token is now public in the repository. What should have been done?
**Options:**
- A) Hardcoding tokens in version control is acceptable as long as the repo is private
- B) Use environment variable substitution: `"token": "${GITHUB_TOKEN}"` in `.mcp.json`, store actual token in local `.env` or shell environment
- C) Store tokens in a separate config file and don't commit it
- D) Rotate the token after each commit

**Correct Answer:** B
**Feedback:** MCP servers support `${VAR_NAME}` syntax for environment variable substitution. Never commit secrets. The `.mcp.json` file references environment variables (which live locally), keeping the config shareable and secrets safe. This is the standard secure pattern.
**Theory Reference:** Domain 2.4 & Chapter 4.3 - MCP server secret management

---

## Question 65
**Title:** Hook Placement: PreToolUse vs PostToolUse
**Situation:** You want to normalize all timestamps returned by external tools. Tool A returns Unix timestamps, Tool B returns ISO 8601, Tool C returns human-readable strings. Where should the normalization hook be placed?
**Options:**
- A) PreToolUse hook before each tool call
- B) In the tools themselves
- C) PostToolUse hook after all tool calls, before the agent processes results
- D) In the system prompt

**Correct Answer:** C
**Feedback:** PostToolUse hooks intercept results after tool execution, before the agent processes them. This is perfect for data normalization. PreToolUse hooks intercept outgoing calls, not incoming results. Post-processing normalizes all tool results centrally without modifying each tool.
**Theory Reference:** Domain 1.5 - Hook placement for data normalization

---

## Question 66
**Title:** Specialized Tools vs General-purpose with Parameters
**Situation:** You're choosing between two architectures: (1) specialized tools (`fetch_pdf`, `fetch_image`, `fetch_json`) or (2) one general tool `fetch_file` with a `format` parameter. Which is better for agent reliability?
**Options:**
- A) General tool with parameters is simpler and more maintainable
- B) Specialized tools reduce selection ambiguity; agents more reliably choose the right tool vs a general tool with parameters
- C) Both are equally reliable; pick based on code organization
- D) Use a single tool and require agents to parse all formats

**Correct Answer:** B
**Feedback:** Specialized tools are more reliable than general tools with parameters. Agents can't easily reason about parameter combinations. Distinct tool names and descriptions (fetch_pdf, fetch_image) lead to better selection than choosing one tool and the right parameter. Clarity beats generality.
**Theory Reference:** Domain 2.3 - Tool specialization for reliability

---

## Question 67
**Title:** Transient vs Non-retryable Errors
**Situation:** Two MCP tool errors: (1) `{"isError": true, "errorCategory": "timeout", "isRetryable": true}` - API temporarily unreachable, (2) `{"isError": true, "errorCategory": "validation", "isRetryable": false}` - invalid query syntax. How should the coordinator handle each?
**Options:**
- A) Retry both; retrying always helps
- B) Retry (1) with backoff; don't retry (2), log the validation error and escalate or skip
- C) Escalate both to a human
- D) Ignore both and continue

**Correct Answer:** B
**Feedback:** Structured error metadata enables smart retry decisions. Transient errors (timeouts, temporarily unavailable) benefit from retries. Non-retryable errors (validation, policy violations) won't succeed on retry—handle them differently (fix, escalate, or skip). The `isRetryable` flag is critical.
**Theory Reference:** Domain 2.2 & 5.3 - Error categorization and retry decisions

---

## Question 68
**Title:** Multi-stage Prompting for Complex Workflows
**Situation:** Your system needs to (1) extract data, (2) validate it, (3) transform it, (4) export it. Should this be one prompt or four separate prompts?
**Options:**
- A) One comprehensive prompt covering all stages
- B) Four separate prompts in sequence (prompt chaining); each stage focuses deeply without attention dilution
- C) Combine into two prompts: extraction+validation, then transformation+export
- D) Use four different models

**Correct Answer:** B
**Feedback:** Separate prompts for distinct stages enable focused reasoning. One prompt trying to handle all four stages dilutes attention. Prompt chaining (sequential, focused prompts) is ideal for predictable multi-stage workflows. Each stage gets full context and clarity.
**Theory Reference:** Domain 1.6 & Chapter 6.3 - Prompt chaining

---

## Question 69
**Title:** Planning Mode and Safe Exploration
**Situation:** You're in Claude Code and need to refactor a critical authentication system (50 files, many dependencies). Should you use Planning Mode or direct execution?
**Options:**
- A) Direct execution; planning adds overhead
- B) Planning Mode; it safely explores the codebase before you commit any changes, surfacing architectural impact
- C) Planning Mode only for small refactors
- D) Ask a senior engineer; don't use Claude for critical systems

**Correct Answer:** B
**Feedback:** Planning Mode is specifically designed for complex, high-impact changes. It explores the codebase, identifies dependencies, and surfaces architectural considerations before making changes. For critical systems, planning prevents costly mistakes. Direct execution is for simple, low-risk changes.
**Theory Reference:** Domain 3.4 - Planning Mode for complex refactors

---

## Question 70
**Title:** Skills and Allowed Tools
**Situation:** You create a `/code-review` skill that should use only linting and analysis tools, not code modification tools. However, developers report it sometimes generates "fix" suggestions. How do you prevent this?
**Options:**
- A) Trust the system prompt to prevent modification attempts
- B) Use the `allowed-tools` field in the skill's SKILL.md frontmatter to restrict the skill to analysis tools only, excluding modification tools
- C) Have a human review every skill output
- D) Remove the skill

**Correct Answer:** B
**Feedback:** The `allowed-tools` field in SKILL.md restricts what tools a skill can use. This prevents the skill from accessing modification tools—an architectural constraint that prompts cannot guarantee. Skills with restricted toolsets are more reliable and predictable.
**Theory Reference:** Domain 3.2 - Skills with allowed-tools constraints

---

## Question 71
**Title:** Combining Tool Results Strategically
**Situation:** You have `fetch_weather` and `fetch_forecast` tools. A user asks "What should I wear tomorrow?" You need both current conditions (weather) and tomorrow's forecast. How should you call these?
**Options:**
- A) Call `fetch_weather`, wait for result, then call `fetch_forecast`
- B) Call both tools in the same agent turn, process results together
- C) Call fetch_forecast only; it includes forecasted conditions
- D) Call fetch_weather only

**Correct Answer:** B
**Feedback:** When two tools provide complementary data needed for a complete answer, call both in the same agent turn. The agent can combine results and provide a unified recommendation. Sequential calling is unnecessary overhead when tools can be called in parallel within the same turn.
**Theory Reference:** Domain 1.1 - Agent loop batching multiple tool calls

---

## Question 72
**Title:** Subagent Reporting Structure
**Situation:** A subagent completes its investigation and must report findings to the coordinator. The subagent's findings include: (1) data (what was found), (2) methodology (how it was found), (3) confidence levels, (4) gaps (what wasn't found). What should be included in the structured report?
**Options:**
- A) Only the data; methodology and gaps are unnecessary detail
- B) Data + confidence levels; methodology is implementation detail
- C) All of it: data, methodology, confidence, gaps. The coordinator needs complete context to synthesize and weight findings
- D) Only what's asked for in the task prompt

**Correct Answer:** C
**Feedback:** Structured subagent reports must include: findings (data), how they were determined (methodology for credibility), confidence levels (for weighting), and gaps (to identify blind spots). This enables the coordinator to synthesize intelligently. Omitting any element reduces the coordinator's decision-making quality.
**Theory Reference:** Domain 1.2 - Subagent reporting structure

---

## Question 73
**Title:** Retryable vs Non-retryable Extraction Errors
**Situation:** Document extraction fails with two errors: (1) "Corrupted PDF file cannot be parsed" (corruption is permanent), (2) "OCR service timeout" (likely temporary). How should these be handled differently?
**Options:**
- A) Retry both with exponential backoff
- B) Retry (2) immediately; (1) is non-retryable, skip or request format conversion
- C) Escalate both to a human
- D) Ignore both errors

**Correct Answer:** B
**Feedback:** Retry strategy depends on error type. Transient errors (timeouts, temporary service failures) benefit from retries. Permanent errors (corrupted files, incompatible formats) don't—retry wastes resources. Distinguish and handle appropriately: retry transient, convert/skip permanent.
**Theory Reference:** Domain 2.2 - Error classification and retry strategy

---

## Question 74
**Title:** Agent Loop Continuity and History
**Situation:** An agent makes a tool call, receives a result, and processes it. On the next iteration, should the tool result remain in the conversation history?
**Options:**
- A) No, remove results after processing to save context
- B) Yes, append tool results to the conversation so the model can reference them in subsequent iterations
- C) Only keep results from the last 3 iterations
- D) Store results in a separate database

**Correct Answer:** B
**Feedback:** Tool results must remain in the conversation history. The model needs to reference prior results when making subsequent decisions. Removing results breaks the agent loop—the model loses context for decision-making. Accumulating history is essential for multi-turn reasoning.
**Theory Reference:** Domain 1.1 - Conversation history and agent loop

---

## Question 75
**Title:** Coordinator Observability and Communication
**Situation:** Three subagents are running concurrently, and one fails silently (returns empty results). The coordinator doesn't know if it succeeded (no matches) or failed (access error). How should the subagent communicate this?
**Options:**
- A) The coordinator should assume all empty results are successful
- B) Subagent should return structured metadata: `{"status": "success", "recordCount": 0}` or `{"status": "failed", "errorReason": "access_denied"}`
- C) Subagent should log errors; the coordinator shouldn't worry about it
- D) Silent failures are acceptable

**Correct Answer:** B
**Feedback:** All communication must flow through the coordinator for observability. Structured metadata (status + context) enables the coordinator to distinguish success from failure. Silent failures compromise observability and make debugging impossible. Explicit communication is essential in multi-agent systems.
**Theory Reference:** Domain 1.2 - Coordinator observability

---

## Question 76
**Title:** JSON Schema Flexibility for Extensibility
**Situation:** You define a schema for extracted entities with fields: `type`, `name`, `value`. Later, you need to add new entity types not in your original list. Should you use a strict enum or allow flexibility?
**Options:**
- A) Use a strict enum: `type: ["person", "company", "location"]`
- B) Use enum with "other": `type: enum: ["person", "company", "location", "other"]` plus `type_detail: "string"` for clarification
- C) Allow any string for flexibility
- D) Remove the type field

**Correct Answer:** B
**Feedback:** Enums with "other" + detail field balance structure and flexibility. Known types are constrained; unknown types use "other" + explanation. This maintains schema structure while allowing extensibility. Pure flexibility loses validation; pure rigidity breaks on new cases.
**Theory Reference:** Domain 4.3 - Schema design for extensibility

---

## Question 77
**Title:** Synchronous vs Asynchronous Escalation
**Situation:** An agent encounters a customer escalation request: "I want to speak to a manager." Should the agent wait for a human response before continuing, or should it continue processing other tasks?
**Options:**
- A) Wait synchronously for the human to respond
- B) Escalate asynchronously: create an escalation ticket, return a confirmation to the user, and continue processing other requests
- C) Ignore the escalation and keep helping
- D) Shut down the agent

**Correct Answer:** B
**Feedback:** Asynchronous escalation is better for workflow efficiency. Create an escalation ticket (with customer ID, reason, context) and notify the user it's being reviewed. The agent can continue helping others. Synchronous blocking would halt the system. Async patterns enable concurrency.
**Theory Reference:** Domain 5.2 - Escalation patterns

---

## Question 78
**Title:** Ambiguous Customer Requests
**Situation:** A customer says "Fix my account." The agent's options: (1) ask clarifying questions, (2) check the account for obvious issues, (3) immediately escalate. What's the best approach?
**Options:**
- A) Immediately escalate; ambiguity always requires a human
- B) Ask clarifying questions first ("What specific problem are you experiencing?") to narrow the scope before escalating
- C) Check for obvious issues first, then ask clarifying questions if needed
- D) Guess what the problem is

**Correct Answer:** B
**Feedback:** Ambiguous requests benefit from clarification before escalation. "Fix my account" could mean password reset, billing issue, or data correction. Asking "What specific problem?" narrows scope and may enable the agent to resolve it. Escalate only if clarification reveals a complex issue outside the agent's scope.
**Theory Reference:** Domain 5.2 - Handling ambiguity and escalation

---

## Question 79
**Title:** Confidence Self-rating and Action
**Situation:** Your extraction system outputs `{"confidence_level": 0.45, "extracted_value": "XYZ"}` for a critical field. What should happen?
**Options:**
- A) Use the value anyway; the model is doing its best
- B) Automatically retry extraction if confidence < 0.7
- C) Route low-confidence extractions to human review with the value and confidence level shown
- D) Reject the entire document

**Correct Answer:** C
**Feedback:** Low confidence signals should trigger human review, not automatic retries (may fail again) or auto-rejection (discards potentially correct data). Show the extracted value and confidence to a human reviewer. They can validate or correct. This is calibrated escalation.
**Theory Reference:** Domain 5.5 - Confidence-based routing

---

## Question 80
**Title:** Attribution and Source Tracking
**Situation:** Your coordinator synthesizes findings from 5 subagents into a report. A key statistic comes from Subagent C. Later, a reader asks "Where does this statistic come from?" If you lost the source attribution, you cannot answer. How do you prevent this?
**Options:**
- A) Subagents should include quotes and sources in their output; coordinator preserves these during synthesis
- B) Store all subagent outputs in a database for later reference
- C) Request that subagents highlight "important facts"
- D) Assume readers won't ask about sources

**Correct Answer:** A
**Feedback:** Attribution must be preserved during synthesis. Subagents should provide: claim + source (URL, document name, quote). The coordinator preserves these mappings during aggregation. If synthesis loses attribution, readers can't verify claims. Structured "claim → source" mappings prevent attribution loss.
**Theory Reference:** Domain 5.6 - Preserving provenance and attribution

---

## Question 81
**Title:** Testing Standards in CLAUDE.md
**Situation:** Your project has 12 packages, each with different testing frameworks. You want all packages to follow standard testing practices (test naming, coverage requirements, fixture patterns). Where should these standards live?
**Options:**
- A) In each package's CLAUDE.md (duplicated)
- B) In a project-level `.claude/rules/testing.md` with `paths: ["**/*.test.*"]` to apply to all test files
- C) In a separate testing documentation website
- D) Communicated verbally to the team

**Correct Answer:** B
**Feedback:** Path-scoped rules in `.claude/rules/testing.md` with `paths: ["**/*.test.*"]` apply testing standards to all test files across all packages, without duplication. This is DRY and scalable. The `paths` field activates the rule only when editing matching files.
**Theory Reference:** Domain 3.3 - Path-specific rules for codebase-wide conventions

---

## Question 82
**Title:** API vs Import in Fetch Operations
**Situation:** Your agent needs to load a codebase CLAUDE.md file. Should it: (1) call a `fetch_file` tool via API, or (2) check if the file is available as an MCP resource?
**Options:**
- A) Always use the tool; tools are general-purpose
- B) Check for MCP resource first; if available, use it (cached, faster). Fall back to tool if not available
- C) Always use MCP resources; never call tools
- D) Ask the user to provide the file manually

**Correct Answer:** B
**Feedback:** MCP resources are pre-loaded and cached; tools require dynamic calls. If a resource is available, use it first (faster, fewer tokens). Fall back to tools for dynamic data. This pattern balances performance (resources) with flexibility (tools).
**Theory Reference:** Domain 2.4 - MCP resources vs tool calls

---

## Question 83
**Title:** Iterative Refinement with Test Cases
**Situation:** You're asking Claude Code to implement a data transformation. Instead of providing a 500-word specification, you provide: (1) 3 input examples, (2) expected output for each, (3) edge cases with expected behavior. How does this affect implementation quality?
**Options:**
- A) Examples are nice but less important than detailed prose specifications
- B) Examples are the most effective way to communicate expectations; concrete input/output pairs guide implementation better than prose
- C) Examples slow down implementation
- D) Use only prose; examples are redundant

**Correct Answer:** B
**Feedback:** Concrete examples (input + expected output) are far more effective than prose. They directly demonstrate intent and handle ambiguity. Claude can implement from examples faster and more accurately than from paragraph descriptions. Test-driven iteration with examples is best practice.
**Theory Reference:** Domain 3.5 - Iterative refinement with concrete examples

---

## Question 84
**Title:** Agent Specialization vs Generalization
**Situation:** You have a general "customer support" agent. It handles refunds, billing questions, technical support, and account issues. It's 40% effective at each. You consider splitting into specialized agents: `refund_agent`, `billing_agent`, `tech_support_agent`. Trade-offs?
**Options:**
- A) Keep one general agent; splitting adds complexity
- B) One agent is fine; add more tools instead
- C) Split into specialized agents; focused agents are more reliable and can maintain specialized knowledge
- D) Keep one agent but train it better

**Correct Answer:** C
**Feedback:** Specialized agents outperform generalists. A refund agent with refund-specific tools and context is more effective than a general agent splitting attention. Hub-and-spoke architecture (coordinator routes to specialists) is a proven pattern. Generalists dilute reliability.
**Theory Reference:** Domain 1.2 - Specialized subagents vs generalists

---

## Question 85
**Title:** Tool Results and Long Conversation History
**Situation:** An agent makes 50 tool calls over 20 iterations. Each result (average 500 tokens) is appended to history. After 50 calls, the conversation is 25K tokens of tool results + reasoning. The agent starts losing focus. What's happening?
**Options:**
- A) The model needs a better system prompt
- B) Conversation history accumulation is normal; ignore it
- C) Context degradation: long conversations lose coherence. Use subagents or scratchpads to reset context
- D) Increase the context window

**Correct Answer:** C
**Feedback:** Long conversation histories degrade model performance. After 20-30 iterations, coherence starts declining. Mitigation: spawn subagents for specific subtasks (isolated context), use scratchpad files to checkpoint findings, or use `/compact` to summarize. Don't just keep accumulating history indefinitely.
**Theory Reference:** Domain 5.4 - Context degradation and subagent delegation

---

## Question 86
**Title:** Empty Results vs Access Failures in Search
**Situation:** Two search subagents return: (1) `{"results": [], "query_executed": true, "query": "solar adoption 2024"}` - query succeeded, zero matches; (2) `{"results": null, "error": "Database unavailable"}` - query failed. How should the coordinator handle these?
**Options:**
- A) Both represent "no data available"; treat them the same
- B) (1) is a valid result (no matches found), continue. (2) is an access failure, retry or escalate
- C) Both are errors; escalate both
- D) Ignore both and continue

**Correct Answer:** B
**Feedback:** Structured results distinguish success from failure. Query succeeded but zero matches is valid (no articles on that topic). Database unavailable is a failure (different handling—retry, escalate, or use cache). The distinction is critical for workflow decisions.
**Theory Reference:** Domain 5.3 - Distinguishing access failures from empty results

---

## Question 87
**Title:** Multi-turn Tool Use in Batch API
**Situation:** You're using the Batch API to process 1000 documents overnight. Your processing logic requires multi-turn tool use: (1) extract data, (2) validate, (3) if validation fails, retry extraction. Can the Batch API handle multi-turn tool use in one request?
**Options:**
- A) Yes, fully supported
- B) No, Batch API does not support multi-turn tool use within a single request
- C) Only for up to 3 tool calls per request
- D) Batch API doesn't support tools at all

**Correct Answer:** B
**Feedback:** Batch API is designed for single-turn requests. Multi-turn logic (where each response determines the next action) is not supported. For multi-turn workflows, use the synchronous API. Batch API is ideal for simple, single-request tasks like "extract data from this document."
**Theory Reference:** Domain 4.5 & Chapter 7 - Batch API limitations

---

## Question 88
**Title:** Tool Selection with Ambiguous Descriptions
**Situation:** You have tools: `search` and `retrieve`. Both can access documents. Description for `search`: "Find documents." Description for `retrieve`: "Get documents." An agent frequently uses them interchangeably with wrong results. What's the root cause?
**Options:**
- A) The agent is confused; train it better
- B) The descriptions are identical in function; differentiate them. Is `search` for finding by keyword? Is `retrieve` for fetching by ID? Make it explicit
- C) Use parameter to distinguish (both use one tool)
- D) Combine into one tool

**Correct Answer:** B
**Feedback:** Ambiguous descriptions cause unreliable selection. "Find documents" (search) and "Get documents" (retrieve) sound interchangeable. Clarify: "search: Find documents by keyword/content search. Returns ranked results." vs "retrieve: Fetch a specific document by ID." Distinct descriptions = reliable selection.
**Theory Reference:** Domain 2.1 - Clear, distinct tool descriptions

---

## Question 89
**Title:** Coordinator Role in Error Handling
**Situation:** A subagent encounters a temporary API error and implements local retry logic (3 retries with backoff). If all retries fail, should it: (1) return the error to the coordinator, or (2) silently give up and return empty results?
**Options:**
- A) Silently give up; don't bother the coordinator with errors
- B) Return structured error to the coordinator: failure type, what was attempted, whether retry was tried. Let the coordinator decide next steps
- C) Implement infinite retries
- D) Escalate immediately without retrying

**Correct Answer:** B
**Feedback:** Subagents should attempt local recovery (retry transient errors). If recovery fails, report structured error to the coordinator: what failed, was retry attempted, why it failed. The coordinator then makes decisions (retry with different strategy, escalate, skip). Silent failures compromise observability.
**Theory Reference:** Domain 5.3 & 1.2 - Error propagation and coordinator oversight

---

## Question 90
**Title:** Batching vs Sequential Tool Calls
**Situation:** An agent needs to fetch 10 user profiles. Option 1: Call `fetch_profile` 10 times sequentially. Option 2: Batch all 10 into one call with an MCP resource. Which is more efficient?
**Options:**
- A) Sequential is simpler and easier to understand
- B) Batch all 10 in one call; MCP resources handle concurrency, reducing total calls and latency
- C) Both are equally efficient
- D) Sequential is faster

**Correct Answer:** B
**Feedback:** Batching reduces API calls and latency. If an MCP resource or tool supports batch operations, use it. One call for 10 profiles is more efficient than 10 sequential calls. This is why MCP resources are valuable—they enable efficient batch access to catalogs.
**Theory Reference:** Domain 2.4 - MCP resource batching

---

## Question 91
**Title:** System Prompt and Tool Selection
**Situation:** Your system prompt says: "You are a helpful customer support agent." You add a new `escalate_to_manager` tool. Now, the agent rarely uses it, preferring to solve problems itself. The system prompt's wording is subtly discouraging escalation. What's the fix?
**Options:**
- A) Ignore tool selection issues; they're inherent to LLMs
- B) Update system prompt: "Escalate to a manager when: (1) customer explicitly requests a human, (2) issue is outside your authority, (3) customer is frustrated." Make escalation culturally acceptable
- C) Use `tool_choice` to force escalation
- D) Create a separate escalation agent

**Correct Answer:** B
**Feedback:** System prompt wording creates unintended associations. "Be helpful" subtly discourages escalation (seen as failure to help). Explicitly normalizing escalation with clear criteria rebalances the agent's decision-making. Prompts shape behavior—use them intentionally.
**Theory Reference:** Domain 2.1 - System prompt and tool selection influence

---

## Question 92
**Title:** Subagent Isolation and Task Specificity
**Situation:** You spawn a subagent with the prompt: "Do research about climate change." The subagent produces a 300K-token comprehensive report. But you only need specific information (CO2 emissions trends). How do you improve this?
**Options:**
- A) Accept the 300K tokens; that's what research produces
- B) Use specific task prompts: "Research CO2 emission trends from 2015-2025, focusing on industrial sources. Provide concise summary with data points and sources." Specificity reduces noise
- C) Pre-filter the results after the fact
- D) Use a different subagent

**Correct Answer:** B
**Feedback:** Task specificity drives subagent output quality and conciseness. Vague prompts ("do research") produce unfocused output. Specific prompts ("research CO2 trends 2015-2025, industrial sources, concise summary") guide focused investigation. This is the coordinator's responsibility—task decomposition includes setting clear boundaries.
**Theory Reference:** Domain 1.2 - Task specificity and subagent prompts

---

## Question 93
**Title:** Handling Partial Results from Subagents
**Situation:** A fact-checking subagent checks 10 claims. It finds definitive answers for 7, but 3 claims lack sufficient information. Should it: (1) report only the 7 verified claims, (2) report all 10 with confidence levels and gaps?
**Options:**
- A) Report only the 7; don't bother with incomplete information
- B) Report all 10 with confidence levels (high for verified, low/unclear for insufficient data) and explicit gaps. This enables the coordinator to make informed decisions about coverage
- C) Retry checking the 3 unclear claims
- D) Escalate the unclear ones to a human

**Correct Answer:** B
**Feedback:** Partial results are valuable. Report what you found, confidence levels, and gaps. Coordinator needs to know: "7 claims verified, 3 lack sufficient data." This enables decisions: continue with partial info, retry specific claims, or escalate. Hiding gaps compromises decision quality.
**Theory Reference:** Domain 5.1 & 5.3 - Handling incomplete information

---

## Question 94
**Title:** `.claude/rules/` Organization
**Situation:** Your project has conventions for testing (Jest, Vitest, Playwright), API design (REST, GraphQL), and deployment (Docker, Kubernetes). Should these be: (1) one large `.claude/rules/testing.md`, or (2) separate files?
**Options:**
- A) One file; keep it simple
- B) Separate files: `.claude/rules/testing.md`, `.claude/rules/api-design.md`, `.claude/rules/deployment.md` with appropriate `paths` frontmatter. This is more modular and maintainable
- C) Spread them across multiple CLAUDE.md files in directories
- D) Store in documentation instead

**Correct Answer:** B
**Feedback:** Multiple focused rule files are more maintainable than one monolithic file. Each `.claude/rules/*.md` file can have specific `paths` patterns (e.g., `paths: ["**/*.test.*"]` for testing). This modular structure scales better as conventions grow.
**Theory Reference:** Domain 3.1 & 3.3 - Modular CLAUDE.md organization

---

## Question 95
**Title:** Recognizing Context Degradation
**Situation:** You've been investigating a codebase in Claude Code for 45 minutes. Early findings were clear and accurate. Now, after 8 file reads and 15 tool calls, the agent is:
- Referring to "typical patterns" instead of specific classes
- Mixing up findings from different files
- Asking imprecise follow-up questions

What's happening?
**Options:**
- A) The agent is tired; give it a break
- B) Context degradation from long conversation history. Checkpoint findings, spawn a subagent for the next phase, or start a fresh session
- C) The codebase is too complex
- D) Increase context window

**Correct Answer:** B
**Feedback:** After ~40-50 iterations, coherence starts declining. Signs: vague references, mixed-up context, imprecise reasoning. Mitigation: save key findings to scratchpad, spawn subagent for next phase (fresh context), or new session. This is a known pattern—manage it proactively.
**Theory Reference:** Domain 5.4 - Context degradation detection and management

---

## Question 96
**Title:** Escalation with Rich Context
**Situation:** A customer requests a refund for a $5000 purchase. Your agent cannot approve refunds over $1000 (policy boundary). When escalating, what context should be included?
**Options:**
- A) Just pass the request: "Customer wants refund"
- B) Structured escalation: customer ID, order ID, amount ($5000), customer history (5-year member, no prior disputes), reason (product defective), agent's assessment (legitimate claim), recommended action (approve). Rich context enables human decision
- C) Include only what policy explicitly requires
- D) Don't escalate; deny the request

**Correct Answer:** B
**Feedback:** Escalations with rich context enable faster, better decisions. Include: who (customer ID), what (order, amount), why (reason, agent assessment), history (relevant context), and recommendation. This structured handoff prevents information loss and accelerates human review.
**Theory Reference:** Domain 1.4 & 5.2 - Structured escalation protocols

---

## Question 97
**Title:** Semantic vs Syntactic Validation
**Situation:** A form extraction tool outputs: `{"date_field": "2024-13-45", "amount": 100, "total": 50}`. JSON syntax is valid. But: invalid date (no 13th month), amount > total (illogical). What type of errors are these?
**Options:**
- A) Both are JSON syntax errors
- B) Date error is syntactic (bad format), amount error is semantic (violates business logic)
- C) Date error is semantic (invalid date), amount error is semantic (contradiction). Both exceed JSON schema validation
- D) Not errors; just unusual

**Correct Answer:** C
**Feedback:** JSON schema validates syntax (valid JSON structure). Semantic validation checks business logic (date is valid, total >= amount). "2024-13-45" passes JSON schema but fails date semantics. Totals contradict but pass schema. Separate concerns: schema (syntax) vs rules (semantics).
**Theory Reference:** Domain 4.4 - Semantic vs syntax errors

---

## Question 98
**Title:** Prioritizing Subagent Work
**Situation:** Your coordinator has 5 research tasks. Three are high-priority (impacts immediate decision), two are low-priority (future reference). Spawning all 5 simultaneously takes 20 minutes. Should you: (1) spawn all, or (2) prioritize?
**Options:**
- A) Spawn all; parallel processing is fastest
- B) Spawn high-priority first, collect results, make decision, then spawn low-priority if time permits
- C) Spawn low-priority first to warm up
- D) Spawn one at a time

**Correct Answer:** B
**Feedback:** Prioritization enables faster decisions when time is constrained. High-priority tasks don't wait for low-priority ones. Coordinator gets critical insights faster, makes decisions, then gathers supporting research. This is resource-aware execution.
**Theory Reference:** Domain 1.2 - Prioritized task spawning

---

## Question 99
**Title:** Session Inheritance vs Fresh Start
**Situation:** You completed a Claude Code session that analyzed 50 files on the authentication system. You want to continue work on a different system (payment processing). Should you: (1) resume the auth session, or (2) start fresh?
**Options:**
- A) Resume the auth session; all context is still useful
- B) Start fresh; the auth session context is irrelevant for payment processing and would interfere with new analysis
- C) Fork the session to keep both
- D) Merge the contexts

**Correct Answer:** B
**Feedback:** Context is task-specific. Auth analysis context is noise for payment work. Starting fresh eliminates irrelevant context and prevents confusion. Resume sessions for the same task; start fresh when switching domains.
**Theory Reference:** Domain 1.7 - Session management and task switching

---

## Question 100
**Title:** Combining Few-shot with Explicit Criteria
**Situation:** You want Claude to categorize customer feedback as positive, negative, or neutral. You provide: (1) explicit criteria ("positive: mentions benefits or satisfaction; negative: complaints or issues; neutral: factual statements"), (2) 3 few-shot examples showing borderline cases. Does combining both improve output?
**Options:**
- A) Redundant; use only criteria
- B) Redundant; use only examples
- C) Yes, combining explicit criteria + targeted examples handles ambiguous cases better than either alone
- D) Combining reduces clarity

**Correct Answer:** C
**Feedback:** Explicit criteria define the categories. Few-shot examples demonstrate how to apply criteria to ambiguous cases. Together, they're more effective than separately. Examples show intent when criteria are unclear; criteria provide structure when examples are insufficient.
**Theory Reference:** Domain 4.1 & 4.2 - Combining criteria and few-shot examples

---

## Summary

**Batch 02 contains 50 questions (Questions 61-110)**
- Difficulty level: Consistent with Batches 01 and original 10
- Coverage: All 5 domains + foundational chapters
- Format: Ready to merge into `questionBank` array
- No domain-specific balancing enforced

**Progress: 110 total questions collected**
- Original: 10 questions
- Batch 01: 50 questions
- Batch 02: 50 questions
- **Total so far: 110 questions**

**Remaining: 90-190 questions to reach 200-300 total**

Ready for:
1. ✅ Review Batch 02?
2. ✅ Create Batch 03 (Q111-Q160)?
3. ✅ Continue batches?

