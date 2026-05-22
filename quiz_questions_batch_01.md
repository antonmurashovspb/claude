# Quiz Question Bank - Batch 01 (Questions 11-60)

## Question 11
**Title:** Agent Loop Control Flow
**Situation:** You're implementing an agentic system and your agent is stuck in an infinite loop. Investigation shows the agent keeps calling tools even after reaching a valid conclusion. The model's `stop_reason` is consistently returning `"tool_use"` instead of `"end_turn"`.
**Options:**
- A) Increase the model's temperature to encourage more exploratory behavior
- B) Check the loop control: the agent should continue when `stop_reason = "tool_use"` and stop on `"end_turn"`, not on arbitrary iteration limits
- C) Remove all tools except the most essential ones to reduce opportunities for tool calls
- D) Add a hard limit of 5 iterations regardless of `stop_reason`

**Correct Answer:** B
**Feedback:** The agent loop lifecycle depends on `stop_reason` signals, not iteration counters. `"tool_use"` means the model selected a tool and wants to continue; `"end_turn"` means it's done. Arbitrary iteration limits bypass the model's own decision-making and cause premature termination or infinite loops.
**Theory Reference:** Domain 1.1 - Agent loop lifecycle and stop_reason handling

---

## Question 12
**Title:** Subagent Context Isolation
**Situation:** You have a coordinator agent that maintains a 50K-token conversation history. You spawn a subagent via the `Task` tool to investigate a specific problem. The subagent starts its response with: "Based on the conversation history we've been having..." but this is the subagent's first message.
**Options:**
- A) The subagent is working correctly and has automatically inherited the coordinator's context
- B) Subagents do not automatically inherit the coordinator's context; you must explicitly include relevant information in the subagent prompt
- C) The subagent should have been given read-only access to the coordinator's conversation history
- D) This is a configuration error in the AgentDefinition; context sharing must be enabled

**Correct Answer:** B
**Feedback:** Subagents operate with isolated context. They do not automatically inherit the coordinator's history. Any context needed must be explicitly passed in the prompt or task description. The subagent's mention of "conversation history" suggests it's hallucinating or confusing its own context.
**Theory Reference:** Domain 1.2 & 1.3 - Subagent context and context passing

---

## Question 13
**Title:** MCP Server Configuration Scope
**Situation:** Your team maintains a shared project with Claude integration. One developer adds an MCP server configuration in `~/.claude.json` for experimental database access. They commit this to the repository root. New team members clone the repo but don't see the MCP server available. The original developer confirms it works on their machine.
**Options:**
- A) The repository configuration is broken; MCP servers must be defined in `.claude/mcp.json`
- B) The developer configured it in the user-level file (`~/.claude.json`), which is not shared via VCS. Project-level MCP servers should go in `.mcp.json` in the repository
- C) New team members need to install the MCP server separately; it's not distributed via the repository
- D) GitHub doesn't support syncing MCP configurations; use environment variables instead

**Correct Answer:** B
**Feedback:** MCP servers configured in `~/.claude.json` are user-specific and local only. They won't sync to other team members via Git. Shared MCP servers should be configured in the project's `.mcp.json` file. User-level configs (in `~/.claude.json`) are for personal/experimental integrations.
**Theory Reference:** Domain 2.4 & Chapter 4.3 - MCP server scope and configuration

---

## Question 14
**Title:** Hook Enforcement vs Prompt Guidance
**Situation:** Your financial processing agent occasionally processes transactions that violate company policy (refunds over $10,000 without audit approval). You've added multiple warnings in the system prompt about this policy. Production logs still show 3% of high-value refunds bypass the policy. You have two options: (1) add more detailed policy explanations to the prompt, or (2) use a `PreToolUse` hook to block refund calls with amounts > $10,000 that lack an audit approval flag.
**Options:**
- A) Prompts are always sufficient for policy enforcement; adding hooks introduces unnecessary complexity
- B) Use the `PreToolUse` hook for guaranteed enforcement; prompts only provide probabilistic compliance (~97% is not acceptable for financial rules)
- C) Split refunds into two separate tools: `process_small_refund` and `process_large_refund`, with the large one requiring different permissions
- D) Use both the prompt and the hook for defense-in-depth, even though the hook alone would be sufficient

**Correct Answer:** B
**Feedback:** For business rules requiring guaranteed compliance (especially financial operations), hooks provide deterministic enforcement. Prompts provide probabilistic compliance—no matter how well-written, they can be bypassed. The 3% failure rate confirms prompts are insufficient. Hooks execute at the API level before the agent can circumvent them.
**Theory Reference:** Domain 1.5 - PostToolUse hooks for deterministic compliance

---

## Question 15
**Title:** Tool Description and Selection Reliability
**Situation:** You have two tools: `fetch_content` and `load_document`. Both retrieve content from URLs, but `fetch_content` is general-purpose and `load_document` is specialized for documents (PDFs, DOCX, etc.). Your data extraction agent frequently calls `fetch_content` when it should call `load_document`. The agent's output is inconsistent.
**Options:**
- A) The tool descriptions are too similar; add distinct tool names and descriptions that clearly differentiate their purposes
- B) Reduce the agent's toolset to only `load_document` and remove the ambiguous `fetch_content`
- C) Add more detailed instructions to the system prompt explaining when to use each tool
- D) The agent needs few-shot examples showing correct tool selection

**Correct Answer:** A
**Feedback:** Tool descriptions are the primary mechanism LLMs use to select tools. When descriptions overlap or are ambiguous, tool selection becomes unreliable. Renaming tools (`fetch_content` → `fetch_arbitrary_content`, `load_document` → `load_pdf_docx`) and clarifying descriptions reduces misrouting. Prompt guidance is secondary to clear tool interfaces.
**Theory Reference:** Domain 2.1 - Tool design and clear descriptions

---

## Question 16
**Title:** Batch API for Non-blocking Workloads
**Situation:** Your system needs to extract structured data from 50,000 documents overnight. Each document takes ~5 seconds to process individually via the API. The extraction can happen asynchronously, and results are needed by morning. What's the best approach?
**Options:**
- A) Use the synchronous API with a high-concurrency worker pool to parallelize requests
- B) Use the Batch API for 50% cost savings; submit all requests at once with `custom_id` fields, collect results in 12-24 hours
- C) Use the Batch API but limit batches to 1000 documents each to avoid timeout issues
- D) Use the synchronous API because batch processing doesn't support tool calling

**Correct Answer:** B
**Feedback:** The Batch API is ideal for non-blocking, non-urgent workloads. It offers 50% cost savings and no latency SLA, making it perfect for overnight processing. The `custom_id` field correlates requests with responses. Batch API does support tool use in the request, though results come in 12-24 hours. This workload doesn't need immediate results.
**Theory Reference:** Domain 4.5 & Chapter 7 - Batch API for overnight workloads

---

## Question 17
**Title:** Path-specific Rules and Convention Loading
**Situation:** Your project has 200+ test files spread across multiple directories (`tests/`, `src/__tests__/`, `e2e/tests/`). Testing conventions should apply to all of them, but you don't want to create CLAUDE.md in each directory. What's the most efficient approach?
**Options:**
- A) Create a root-level CLAUDE.md with all testing conventions
- B) Create `.claude/rules/testing.md` with `paths: ["**/*.test.*"]` frontmatter to activate only for test files
- C) Create CLAUDE.md files in each of the three test directories
- D) Store testing conventions in a separate repository and reference them via documentation links

**Correct Answer:** B
**Feedback:** Path-specific rules using glob patterns in `.claude/rules/testing.md` are ideal for conventions that apply across many directories. The `paths: ["**/*.test.*"]` frontmatter activates the rule only when editing matching files, saving context and tokens. This approach scales better than directory-level CLAUDE.md files.
**Theory Reference:** Domain 3.3 - Path-specific rules with glob patterns

---

## Question 18
**Title:** Subagent Error Handling and Propagation
**Situation:** A fact-checking subagent searches for conflicting statistics about a topic and returns: `{ "isError": false, "results": [] }`. The coordinator cannot tell if this means: (1) the search succeeded but found no matches, (2) the search failed silently, or (3) the API was temporarily unavailable. What structure would improve this?
**Options:**
- A) Always use `isError: true` for empty results so the coordinator knows to retry
- B) Return structured metadata: `{ "status": "success", "matchCount": 0, "searchAttempted": true, "coverage": "partial", "fallbackOptions": [...] }`
- C) Have the subagent escalate to a human when no matches are found
- D) Assume empty results are valid and always continue the workflow

**Correct Answer:** B
**Feedback:** Structured error context distinguishes valid empty results from access failures. Returning `status`, `matchCount`, `searchAttempted`, `coverage`, and `fallbackOptions` enables the coordinator to make intelligent decisions: continue (valid empty), retry (access failure), or escalate (partial coverage). Generic responses hide critical context.
**Theory Reference:** Domain 5.3 & Chapter 10 - Structured error context

---

## Question 19
**Title:** Few-shot Examples for Extraction Consistency
**Situation:** Your document extraction system produces inconsistent output formats. Some extractions use "N/A" for missing values, others use null, and others use empty strings. Manual review shows the model is making reasonable choices but inconsistently. What's the most effective fix?
**Options:**
- A) Add validation code that converts all formats to a standard representation
- B) Provide 2-3 few-shot examples showing the exact output format for various document types, including how to handle missing values
- C) Add detailed instructions to the system prompt about output formatting
- D) Split the extraction into separate tools, each with a stricter schema

**Correct Answer:** B
**Feedback:** Few-shot examples are the most effective way to establish consistent output formatting. Examples showing "here's the input, here's the expected output" directly demonstrate the format. Validation code fixes after-the-fact, but few-shot examples guide the model to produce correct output initially.
**Theory Reference:** Domain 4.2 - Few-shot prompting for output consistency

---

## Question 20
**Title:** Lost-in-the-middle Effect and Summarization
**Situation:** A research coordinator has synthesized findings from 5 subagents into a 120K-token report. The findings are organized chronologically: sources from Jan (20K tokens), Feb (30K tokens), Mar (25K tokens), Apr (20K tokens), May (25K tokens). When the synthesis agent reviews the report, it reliably references Jan and May findings but frequently misses Mar findings.
**Options:**
- A) Summarize Mar findings to make them shorter and easier to find
- B) Place a key-findings summary at the top highlighting the most important Mar discoveries, so they appear at the beginning of the report
- C) Run the synthesis in 5 separate passes, one for each month
- D) Split the report into 5 separate 24K-token documents

**Correct Answer:** B
**Feedback:** The lost-in-the-middle effect occurs when models miss content in the middle of long inputs. Placing key findings at the beginning (primacy effect) and using explicit section headings makes middle content more likely to be attended to. Mar findings should be highlighted in the opening summary, not buried chronologically.
**Theory Reference:** Domain 5.1 - Lost-in-the-middle effect mitigation

---

## Question 21
**Title:** Tool Result Trimming and Context Management
**Situation:** Your agent calls a `fetch_user_profile` tool that returns 40+ fields: user ID, name, email, phone, address, preferences, subscription history (500 events), payment methods, support tickets (100 records), etc. The agent only needs the user's name, email, and subscription status. The tool result dominates the context window.
**Options:**
- A) Call the tool as-is; the agent will ignore irrelevant fields
- B) Create a PostToolUse hook that extracts only needed fields before the agent processes the result
- C) Have the agent manually filter the JSON response
- D) Use a simpler tool that returns only essential fields

**Correct Answer:** B
**Feedback:** PostToolUse hooks can trim verbose tool results down to relevant fields before the agent consumes them. Instead of letting 500+ subscription events fill context, extract only the subscription status. This preserves context window space for the agent's reasoning without requiring a new tool.
**Theory Reference:** Domain 5.1 - Trimming tool results and context management

---

## Question 22
**Title:** Forced Tool Selection and Workflow Ordering
**Situation:** Your data processing system has three tools: `validate_data`, `transform_data`, and `export_data`. You want to guarantee the order: (1) validate, (2) transform, (3) export. Using `tool_choice: "auto"`, developers frequently skip validation and go straight to transform. What's the most reliable fix?
**Options:**
- A) Update the system prompt to emphasize the importance of validation
- B) Use `tool_choice: {"type": "tool", "name": "validate_data"}` to force the first call to validate_data, then on subsequent turns force transform_data, then export_data
- C) Create a combined tool `validate_transform_export` that enforces the sequence internally
- D) Use `tool_choice: "any"` to force a tool call, and let the agent choose

**Correct Answer:** B
**Feedback:** Forced tool selection (`tool_choice: {"type": "tool", "name": "..."}`) makes it architecturally impossible to skip steps. Prompts cannot enforce ordering reliably. By forcing validate_data first, transform_data second, etc., you guarantee the workflow order at the API level, not at the prompt level.
**Theory Reference:** Domain 2.3 & Chapter 2.3 - tool_choice forced selection

---

## Question 23
**Title:** MCP Resources and Exploratory Tool Calls
**Situation:** Your coordinator agent needs to understand a complex database schema before running queries. Currently, it calls `describe_schema` tool 20 times with different parameters to explore the structure. This wastes tokens. You have MCP resources available (catalogs of pre-computed information).
**Options:**
- A) Resources don't help with this; the agent needs to call describe_schema repeatedly to learn the schema
- B) Use an MCP resource to pre-load the schema as a "content catalog," reducing exploratory tool calls
- C) Pre-summarize the schema in the system prompt
- D) Have a human provide the schema manually

**Correct Answer:** B
**Feedback:** MCP resources act as "content catalogs" that can be loaded once and referenced multiple times. A schema resource loaded upfront prevents 20 exploratory tool calls. This reduces context usage and API costs. Resources are ideal for static information that agents frequently query.
**Theory Reference:** Domain 2.4 & Chapter 4.5 - MCP resources as content catalogs

---

## Question 24
**Title:** Coordinator vs Subagent Responsibility
**Situation:** You have a coordinator handling multiple requests and three specialized subagents (research, analysis, writing). The coordinator receives a user request: "Write a 5-page report on solar energy adoption." Where should the responsibility split be?
**Options:**
- A) The coordinator should delegate the entire task to one subagent and let it decide how to break it down
- B) The coordinator should task-decompose: spawn research subagent, then analysis subagent, then writing subagent, with each receiving results from the prior step
- C) The coordinator should write the entire report itself
- D) All subagents should work in parallel on the same task without coordination

**Correct Answer:** B
**Feedback:** The coordinator owns task decomposition and routing. It decides "first gather research, then analyze it, then write the report." Each subagent focuses on its specialty and receives the output of prior steps. The coordinator sequences the work and aggregates results. This hub-and-spoke pattern prevents duplication and ensures coherent workflows.
**Theory Reference:** Domain 1.2 - Coordinator-subagent responsibility split

---

## Question 25
**Title:** Session Resumption vs Fresh Sessions
**Situation:** You used Claude Code to investigate a codebase 3 days ago and saved the session as "auth-system-review." You return to the task with new requirements. Should you resume the old session or start fresh?
**Options:**
- A) Always resume; the prior context is still valid
- B) Only resume if the requirements haven't changed; if they have, start a fresh session to avoid contamination
- C) Always start fresh to ensure accuracy
- D) Pause for 30 minutes to let the model "forget" the old session

**Correct Answer:** B
**Feedback:** Resume sessions when the context is still current and the task is unchanged. If 3 days have passed and requirements changed, the old results may be stale. In such cases, a fresh session with a structured summary is more reliable than resuming with potentially outdated findings. Use `--resume` when context is current; start fresh when it's stale or the goal shifted.
**Theory Reference:** Domain 1.7 & Chapter 5.10 - Session resumption and forking

---

## Question 26
**Title:** Scratchpad Files for Long Investigations
**Situation:** You're investigating a 50-file codebase over multiple Claude Code sessions. After session 1, you've identified 8 key patterns and 3 critical issues. In session 2, you notice you're re-discovering the same patterns. What's the best practice?
**Options:**
- A) Accept that rediscovery is part of the investigation process
- B) Use a scratchpad file (e.g., `.investigation.md`) to persist key findings across sessions and reference it in subsequent sessions
- C) Keep all findings in your head and type them into each new session
- D) Use a database to store findings instead of files

**Correct Answer:** B
**Feedback:** Scratchpad files are ideal for persisting investigation state across context boundaries. A `.investigation.md` file in the repo stores key patterns, findings, and links, enabling subsequent sessions to build on prior work without re-discovering. This is especially valuable for long-running investigations across multiple Claude Code sessions.
**Theory Reference:** Domain 5.4 - Scratchpad files for state persistence

---

## Question 27
**Title:** Validation and Retry-with-feedback
**Situation:** Your extraction system extracts invoice totals from documents. Validation detects: "Line items sum to $450, but stated total is $500. Discrepancy: $50." Instead of marking this as failed, you want the model to retry with this error feedback.
**Options:**
- A) Mark the extraction as failed and skip the document
- B) Feed the original document, the incorrect extraction, and the validation error back to the model with a prompt: "Fix the extraction. Here's what was wrong..."
- C) Automatically adjust the total to match the line items
- D) Escalate to a human

**Correct Answer:** B
**Feedback:** Retry-with-error-feedback is powerful for semantic errors where information is present but misinterpreted. Providing the original document, incorrect extraction, and specific validation error (totals don't reconcile) guides the model to self-correct. This works when the information exists in the source; it doesn't work when the info is missing.
**Theory Reference:** Domain 4.4 - Validation and retry-with-feedback loops

---

## Question 28
**Title:** `fork_session` for Parallel Exploration
**Situation:** You're in a Claude Code session investigating two possible refactoring approaches for a legacy system. Approach A involves creating new abstraction layers (conservative). Approach B involves rewriting core modules (aggressive). You want to explore both in parallel without losing your current progress.
**Options:**
- A) Manually duplicate all your work into two separate sessions
- B) Use `fork_session` to create two independent investigation branches from your current session, explore both, then compare results
- C) Investigate one approach fully, then manually backtrack and investigate the other
- D) Ask a colleague to investigate one approach while you investigate the other

**Correct Answer:** B
**Feedback:** `fork_session` creates independent subagent contexts that branch from your current session. Both branches start with the same context but diverge independently. This allows parallel exploration of alternatives without contaminating either branch. Perfect for "what-if" investigations.
**Theory Reference:** Domain 1.7 & Chapter 5.10 - fork_session for parallel exploration

---

## Question 29
**Title:** Confidence Calibration and Human Oversight
**Situation:** Your extraction system reports 98% accuracy overall, but when you stratify by document type, you find: Invoices 99%, Receipts 92%, Bank Statements 85%. Should you automate all document processing?
**Options:**
- A) Yes, 98% is acceptable; all documents should be automated
- B) No; accuracy varies significantly by type. Implement stratified random sampling and calibrate confidence thresholds per document type before full automation
- C) Only automate invoices; manually review receipts and statements
- D) Increase model temperature to improve accuracy on all types equally

**Correct Answer:** B
**Feedback:** Aggregate metrics (98% overall) mask poor performance on specific document types (85% for statements). Before automating, validate accuracy by document type and field segment using stratified random sampling. Set confidence thresholds per type: high for invoices, lower for statements. Route low-confidence extractions to human review.
**Theory Reference:** Domain 5.5 - Confidence calibration and stratified validation

---

## Question 30
**Title:** Handling Conflicting Information in Synthesis
**Situation:** Your research coordinator receives conflicting statistics from two sources: Source A says "solar adoption is 8.5%", Source B says "solar adoption is 12.3%". Publication dates: A is from 2024-01, B is from 2023-06. What should the synthesis report?
**Options:**
- A) Report the average: ~10.4%
- B) Report only the newer source (A)
- C) Report both values with attribution and dates: "As of Jan 2024, adoption is 8.5%; June 2023 data showed 12.3%. The increase may reflect recent growth or methodology differences."
- D) Report the higher number as more optimistic

**Correct Answer:** C
**Feedback:** Never arbitrarily choose one conflicting value. Instead, report both with full attribution (source, date, quotes). This preserves provenance and lets readers understand the discrepancy. Conflicting data often indicates temporal changes or methodology differences—both are valuable context. Including dates prevents misreading temporal differences as contradictions.
**Theory Reference:** Domain 5.6 - Preserving provenance and handling conflicts

---

## Question 31
**Title:** Incremental Investigation Strategy
**Situation:** You need to trace how a request flows through a 30-file codebase from entry point to database query. What's the most efficient investigation pattern?
**Options:**
- A) Read all 30 files sequentially
- B) Use Grep to search for entry point, identify calling functions, then recursively read each layer
- C) Use Glob to find all files, then use Grep to search for keywords, then read specific files to trace the flow
- D) Ask for documentation instead of reading code

**Correct Answer:** C
**Feedback:** Incremental investigation builds understanding progressively: (1) Glob to find relevant files by pattern, (2) Grep to search for entry points and key keywords, (3) Read specific files to trace detailed flow. This avoids reading irrelevant files and focuses context on the critical path. Build a mental map incrementally rather than front-loading all files.
**Theory Reference:** Domain 2.5 & Chapter 13.2 - Incremental investigation strategy

---

## Question 32
**Title:** API vs MCP Configuration for Authentication
**Situation:** Your team uses both Claude API (via backend) and Claude Code (local). You have a GitHub token that needs to be shared for MCP integration. Should you store it in version control?
**Options:**
- A) Store it in `.mcp.json` in the repository with `${GITHUB_TOKEN}` placeholder
- B) Have each developer manually add the token to their `~/.claude.json`
- C) Store it in environment variables and reference it in `.mcp.json` with `${GITHUB_TOKEN}` syntax
- D) Store it in plain text in a shared `.github_token` file

**Correct Answer:** C
**Feedback:** MCP servers support environment variable substitution in `.mcp.json` using `${VAR_NAME}` syntax. Never commit tokens to version control. Instead, define environment variables locally and reference them in `.mcp.json`. This keeps secrets secure while centralizing configuration.
**Theory Reference:** Domain 2.4 & Chapter 4.3 - MCP configuration with env vars

---

## Question 33
**Title:** Interview Pattern for Design Decisions
**Situation:** You're asking Claude Code to implement a caching system. Instead of providing all requirements upfront, you want Claude to surface non-obvious design questions: cache invalidation strategy, failure modes, consistency guarantees. What pattern should you use?
**Options:**
- A) Just ask Claude to "build a cache"; it will figure out the details
- B) Provide a 50-page specification covering every detail
- C) Use the "interview" pattern: ask Claude to ask you clarifying questions about requirements, then iterate based on answers
- D) Ask a colleague instead

**Correct Answer:** C
**Feedback:** The interview pattern surfaces design aspects that aren't obvious from requirements. Claude asks: "How often do you expect cache invalidation? What's acceptable staleness? What happens if the cache fails?" These questions force you to think through edge cases. Iteration based on answers produces better designs than either minimal or exhaustive specifications.
**Theory Reference:** Domain 3.5 & Chapter 6.4 - Interview pattern for design refinement

---

## Question 34
**Title:** Planning Mode vs Direct Execution
**Situation:** You want Claude Code to add a new authentication module to a 50-file backend system. The change touches 6 files, requires new dependencies, and affects how all services are initialized. Should you use Planning Mode or Direct Execution?
**Options:**
- A) Direct Execution; planning adds unnecessary overhead
- B) Planning Mode; the changes are large, multi-file, and have architectural consequences. Explore approaches safely before committing changes
- C) Planning Mode only if the task is ambiguous
- D) Neither; ask a senior engineer instead

**Correct Answer:** B
**Feedback:** Planning Mode is ideal for complex tasks with large changes, multiple viable approaches, and architectural consequences. It lets Claude explore the codebase, understand dependencies, and surface design options before making changes. Direct Execution is fine for small, well-understood fixes. This task warrants planning.
**Theory Reference:** Domain 3.4 - Planning Mode vs Direct Execution

---

## Question 35
**Title:** Edit vs Read+Write Tool Selection
**Situation:** You need to update a single line in a 200-line configuration file. The line you want to change is unique: `database_timeout: 30` should become `database_timeout: 60`. What's the best tool choice?
**Options:**
- A) Use Edit with the unique text match: `database_timeout: 30` → `database_timeout: 60`
- B) Use Read to fetch the entire file, then Write to replace it
- C) Use Grep to find the line first, then Edit
- D) Ask the user to make the change manually

**Correct Answer:** A
**Feedback:** Edit is designed for precise, targeted changes via unique text matches. If the match is unique (only one occurrence of `database_timeout: 30`), Edit makes the change directly without reading the whole file. Read+Write should only be used if Edit fails due to ambiguous matches.
**Theory Reference:** Domain 2.5 - Edit tool for precise changes

---

## Question 36
**Title:** Explicit Criteria for Code Review
**Situation:** You're setting up automated code review with Claude. The current prompt is: "Review the code for quality issues." But reviewers are complaining about too many false positives for stylistic nitpicks that aren't real bugs. How do you fix this?
**Options:**
- A) Add more general language: "Be more lenient"
- B) Define explicit criteria: "Report: (1) logic errors, (2) security vulnerabilities, (3) performance issues. Ignore: stylistic differences, naming conventions, formatting."
- C) Use sentiment analysis to detect reviewer frustration
- D) Ask reviewers to pre-filter issues

**Correct Answer:** B
**Feedback:** Explicit criteria are far more effective than vague guidance. Defining what to report (bugs, security) vs ignore (style) directly reduces false positives. Vague instructions like "be more lenient" don't work reliably. Claude needs categorical boundaries, not subjective advice.
**Theory Reference:** Domain 4.1 - Explicit criteria vs vague instructions

---

## Question 37
**Title:** Multi-instance Code Review
**Situation:** You need to review a 30-file pull request. You run Claude Code once to review all files and get a comprehensive report. But you're concerned about missed issues. What's a better approach?
**Options:**
- A) Run the review again with the same setup; replication will catch missed issues
- B) Spawn two independent Claude instances: one generates the review, a second reviews the changes without generation context (hasn't seen how the code was written)
- C) Have a human review all files manually
- D) Run the review in three separate passes for different aspects

**Correct Answer:** B
**Feedback:** Self-review has inherent bias; the model retains its own reasoning and is less likely to challenge it. A second independent instance without generation context is better at spotting subtle issues. Multi-instance reviews (generator vs independent reviewer) catch errors that single-pass reviews miss.
**Theory Reference:** Domain 4.6 - Multi-instance review for quality assurance

---

## Question 38
**Title:** Integration vs Local Analysis in Code Review
**Situation:** Your code review agent analyzes each file independently and produces a report: "Function X in file A calls unknown method Y." But you know method Y is defined in file B. The local analysis is correct (file A doesn't define Y), but the cross-file context is missing. How do you improve this?
**Options:**
- A) Increase the model's temperature to encourage broader thinking
- B) Add a second pass that analyzes cross-file interactions, checking if "unknown" functions are actually defined elsewhere
- C) Provide all 30 files to the model in a single analysis
- D) Ignore cross-file issues; they're too complex

**Correct Answer:** B
**Feedback:** Multi-pass review is ideal: (1) per-file local analysis (catches file-specific issues), (2) integration pass (checks cross-file dataflow and unknown dependencies). A single pass trying to handle both gets diluted; two focused passes are clearer. The integration pass resolves apparent issues that are actually valid cross-file calls.
**Theory Reference:** Domain 1.6 & 4.6 - Multi-pass review architecture

---

## Question 39
**Title:** Tool Description Ambiguity
**Situation:** You have a search system with two tools: `search_documents` and `search_web`. The descriptions are identical: "Search for information." Your agent uses them interchangeably, sometimes getting document results when you expected web results. What's the minimum fix?
**Options:**
- A) Add a parameter to each tool to specify the source
- B) Rename and clarify descriptions: `search_documents: "Search internal documents (PDFs, files). Returns file paths and snippets."` and `search_web: "Search the internet. Returns URLs and web pages."`
- C) Add few-shot examples to the system prompt
- D) Combine into one tool with a parameter

**Correct Answer:** B
**Feedback:** Clear, distinct tool descriptions are the primary mechanism for correct selection. Renaming tools and clarifying what they search (internal vs web) and what they return (files vs URLs) removes ambiguity. This is more effective than parameters or prompts, as descriptions are what the model reads first.
**Theory Reference:** Domain 2.1 - Tool descriptions and selection reliability

---

## Question 40
**Title:** `isError` Flag in MCP Responses
**Situation:** An MCP tool returns: `{ "isError": true, "content": "No results found" }` for a search that found zero matches. The coordinator treats this as a failure and retries. What's the better response structure?
**Options:**
- A) The current structure is correct; empty results should be marked as errors
- B) Return: `{ "isError": false, "results": [], "resultCount": 0, "searchSucceeded": true }` to indicate the search succeeded but found no matches
- C) Return an error only if the search failed to execute; successful searches with zero matches should not use `isError`
- D) Remove the `isError` flag entirely

**Correct Answer:** C
**Feedback:** The `isError` flag should distinguish execution failures (API unavailable, bad query syntax) from valid empty results (search succeeded, no matches). "No results found" is a valid outcome, not an error. This distinction enables the coordinator to retry appropriately (failed execution) or continue (valid zero matches).
**Theory Reference:** Domain 2.2 - isError flag semantics

---

## Question 41
**Title:** Cascade vs Parallel Subagent Spawning
**Situation:** You have a task: "Write a report on renewable energy." You break it into three subagents: (1) research, (2) analysis, (3) writing. Should you spawn them sequentially or in parallel?
**Options:**
- A) Spawn all three in parallel; they can work independently
- B) Spawn research first, wait for results, then spawn analysis, then spawn writing (cascade). Each step depends on the previous
- C) Spawn research and analysis in parallel, then writing after both complete
- D) Spawn one at a time and manually wait for each

**Correct Answer:** B
**Feedback:** Task dependencies dictate spawning strategy. Research must complete before analysis (analysis needs research data). Analysis must complete before writing (writing needs analysis insights). Cascade spawning (sequential) respects these dependencies. Parallel spawning only works for independent tasks.
**Theory Reference:** Domain 1.2 - Task decomposition and spawning patterns

---

## Question 42
**Title:** Planning Mode Exploration
**Situation:** You're in Claude Code Planning Mode investigating a 100-file codebase to understand authentication flow. Planning Mode is showing you "Explore" subagent results—verbose directory listings, grep outputs, file contents. The output is overwhelming your context. What should you do?
**Options:**
- A) Exit Planning Mode to reduce verbose output
- B) Use the `/explore` subagent deliberately: it isolates verbose discovery output, letting you focus on synthesis in the main session
- C) Ask Claude to summarize everything it found
- D) Read all 100 files directly

**Correct Answer:** B
**Feedback:** The Explore subagent is designed for this purpose. It runs in an isolated context and handles verbose investigation (grep, file reads, directory listings) without filling your main session. You get the findings without the noise. This is perfect for large-scale exploration.
**Theory Reference:** Domain 3.4 - Planning Mode and Explore subagent

---

## Question 43
**Title:** Structured Output with Tool-use Schemas
**Situation:** You need to extract structured data (name, email, phone) from unstructured customer emails. You want guaranteed schema conformance (no missing fields, correct data types). Should you use prompts or tool_use with JSON schemas?
**Options:**
- A) Use detailed prompts describing the JSON structure
- B) Use `tool_use` with a JSON schema defining required fields, types, and constraints
- C) Use prompts with examples
- D) Have humans extract the data

**Correct Answer:** B
**Feedback:** Tool-use with JSON Schemas is the most reliable way to guarantee schema-conformant output. Schemas eliminate syntax errors and enforce required fields. Prompts cannot guarantee schema compliance—even with examples, the model might omit fields or use wrong types. Schemas are deterministic.
**Theory Reference:** Domain 4.3 - Structured output with JSON schemas

---

## Question 44
**Title:** Skill Isolation with `context: fork`
**Situation:** You have a `/summarize-repo` skill that generates a 200K-token codebase summary with visualizations. You want to use this skill in your current Claude Code session to understand a library. Without `context: fork`, the output dominates your session context. With `context: fork`, what happens?
**Options:**
- A) The skill runs in a separate subagent context, and you receive only a summary of its findings
- B) The skill runs but produces no output
- C) The skill runs in your main session normally
- D) The skill is disabled

**Correct Answer:** A
**Feedback:** `context: fork` runs the skill in an isolated subagent, preventing its verbose output from polluting your main session. You get findings (structured summary), not the 200K tokens of intermediate output. This is ideal for skills that produce large amounts of discovery data but are called from another session.
**Theory Reference:** Domain 3.2 - Skills with context: fork

---

## Question 45
**Title:** Prompt Chaining for Multi-aspect Analysis
**Situation:** You need to review a pull request and analyze three independent aspects: (1) logic correctness, (2) security vulnerabilities, (3) performance. These aspects are independent. Should you use prompt chaining?
**Options:**
- A) No, prompt chaining is only for dependent tasks
- B) Yes, use three sequential prompts: analyze logic → analyze security → analyze performance. Each pass focuses deeply, avoiding attention dilution
- C) Analyze all three in one prompt
- D) Ask three different people

**Correct Answer:** B
**Feedback:** Prompt chaining works well for independent aspects too. Three sequential focused passes (one per aspect) produce deeper analysis than one pass trying to handle all three. Each pass gets full context and attention on its specific domain. This is fixed-pipeline decomposition.
**Theory Reference:** Domain 1.6 & Chapter 8.1 - Fixed pipeline task decomposition

---

## Question 46
**Title:** Distinguishing Access Failures from Empty Results
**Situation:** An MCP database tool returns: `{ "isError": false, "records": [], "query": "SELECT * FROM users WHERE age > 150" }`. The query is valid (syntactically), the database is up, but no records match. A search engine tool returns `{ "isError": true, "message": "Service temporarily unavailable" }`. How should the coordinator handle these?
**Options:**
- A) Both are errors; retry both
- B) The database result is a valid empty result (continue); the search is an access failure (retry or escalate)
- C) Both are valid; continue with empty results
- D) Both are errors; escalate to a human

**Correct Answer:** B
**Feedback:** Valid empty results (database query executed, zero matches) and access failures (service unavailable) require different handling. Empty results are often valid—zero users over 150? That's fine. Access failures require retries or escalation. The `isError` flag + detailed context distinguishes these cases.
**Theory Reference:** Domain 5.3 - Error categorization and handling

---

## Question 47
**Title:** Confidence Thresholds in Extraction
**Situation:** Your extraction system outputs `{ "extracted_value": 123, "confidence": 0.72 }` for each field. Field reliability analysis shows amounts are 89% accurate, names are 95% accurate, dates are 78% accurate. What should you do with low-confidence extractions?
**Options:**
- A) Accept all extractions with confidence > 0.5
- B) Set confidence thresholds per field type: amounts > 0.85, names > 0.90, dates > 0.85, then route lower-confidence extractions to human review
- C) Retrain the model on low-confidence fields
- D) Reject all extractions below 0.9

**Correct Answer:** B
**Feedback:** Different fields have different reliability baselines and stakeholder tolerances. Dates (78% baseline) deserve a lower threshold than names (95%). Field-level confidence thresholds enable smart routing: high-confidence extractions automate, low-confidence route to humans. This balances accuracy and automation.
**Theory Reference:** Domain 5.5 - Field-level confidence calibration

---

## Question 48
**Title:** Semantic vs Syntax Errors in Extraction
**Situation:** An extraction tool outputs: `{ "invoice_total": 500, "line_items_sum": 450, "tax": 100 }`. Syntactically correct JSON, but semantically wrong: totals don't reconcile. Validation detects: `500 != 450 + 100`. What type of error is this?
**Options:**
- A) Syntax error; the JSON is malformed
- B) Semantic error; the data structure is valid but values contradict each other
- C) Not an error; the tool did its job
- D) Access error

**Correct Answer:** B
**Feedback:** Syntax errors (malformed JSON) are eliminated by JSON schemas. Semantic errors (values contradict each other, totals don't add up) pass schema validation but violate business logic. Detecting semantic errors requires validation rules separate from schemas. Retry-with-feedback works for semantic errors when the info exists in the source.
**Theory Reference:** Domain 4.4 - Semantic vs syntax errors

---

## Question 49
**Title:** Precedence in Coordinator-Subagent Communication
**Situation:** A subagent encounters a policy ambiguity (e.g., "can we refund if the customer is new?") and has two options: (1) make a reasonable guess and continue, (2) ask the coordinator to clarify. What's the right choice?
**Options:**
- A) The subagent should guess; asking interrupts the workflow
- B) The subagent should ask the coordinator; ambiguities bypass the subagent's authority and require coordinator approval
- C) The subagent should escalate to a human
- D) Policy ambiguities aren't real; the policy must cover all cases

**Correct Answer:** B
**Feedback:** All communication flows through the coordinator (hub-and-spoke). Subagents don't make policy decisions—they ask. The coordinator either clarifies or escalates. This preserves audit trails and prevents subagents from overstepping. Guessing at policy is dangerous.
**Theory Reference:** Domain 1.2 - Hub-and-spoke coordination model

---

## Question 50
**Title:** @path Syntax for Modular CLAUDE.md
**Situation:** Your project has project-wide standards (testing, API design) and package-specific standards (frontend, backend). You want to avoid duplicating standards in each package's CLAUDE.md. How?
**Options:**
- A) Copy standards into each CLAUDE.md
- B) Use `@path` syntax: `@./standards/testing.md` and `@./standards/api.md` in each package's CLAUDE.md to import shared standards
- C) Maintain standards in a separate documentation repo
- D) Have developers memorize all standards

**Correct Answer:** B
**Feedback:** The `@path` syntax (e.g., `@./standards/testing.md`) lets you import external files into CLAUDE.md, avoiding duplication. Each package's CLAUDE.md can include shared standards via `@path` plus package-specific rules. This maintains a single source of truth for shared standards while allowing package customization.
**Theory Reference:** Domain 3.1 - @path syntax for modular CLAUDE.md

---

## Summary

**Batch 01 contains 50 questions (Questions 11-60)**
- Difficulty level: Same as original 10 questions
- Coverage: Spans all 5 domains + foundational chapters
- Format: Ready to merge into `questionBank` array in quiz.html
- No domain-specific balancing enforced (questions randomly distributed)

**Next steps:**
1. Review these 50 questions for quality/accuracy
2. Create Batch 02 with 50 more questions
3. Create Batch 03 with 50 more questions
4. Continue until reaching 200-300 total
5. Manually merge all batches into a single `questionBank` array
6. Inject into quiz.html and test

