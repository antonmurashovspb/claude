# Quiz Question Bank - Batch 03 (Questions 111-160)

## Question 111
**Title:** Agent Decision-Making Bias in Tool Selection
**Situation:** Your agent has two tools: `search_internal_docs` (fast, limited) and `search_web` (slower, comprehensive). The system prompt says "be efficient." The agent rarely uses web search even when internal docs have no relevant results. Why?
**Options:**
- A) The agent is being smart by avoiding slow operations
- B) System prompt framing ("be efficient") creates implicit bias toward the faster tool, even when it's wrong
- C) Web search tool descriptions need improvement
- D) This is expected behavior and acceptable

**Correct Answer:** B
**Feedback:** Prompts create unintended biases. "Be efficient" subtly encourages choosing the fast tool, even when it's the wrong choice. Better framing: "Use the right tool for the job: internal docs for company info, web search for external facts. Efficiency is secondary to accuracy." Prompt design matters deeply.
**Theory Reference:** Domain 4.1 - Explicit criteria and bias in prompts

---

## Question 112
**Title:** Batch API with Custom IDs and Result Correlation
**Situation:** You submit 5000 documents to Batch API with `custom_id` fields: "doc_001", "doc_002", etc. Results return in random order. How do you correlate results back to original documents?
**Options:**
- A) Results return in the same order as the request
- B) Results return in random order, but each has the `custom_id` from the request; use this to map back to original documents
- C) Store the mapping in a database and query it
- D) Batch API doesn't support result correlation

**Correct Answer:** B
**Feedback:** The `custom_id` field is specifically designed for result correlation. Each result includes the `custom_id` from the request, enabling you to map it back even if results arrive in random order. This is essential for large batch processing.
**Theory Reference:** Domain 4.5 & Chapter 7.3 - custom_id for result correlation

---

## Question 113
**Title:** MCP Server Error Handling
**Situation:** An MCP server times out on 30% of requests. Your agent retries immediately, often getting the same timeout. The coordinator has no visibility into timeouts. What's the best fix?
**Options:**
- A) Increase the MCP timeout setting
- B) MCP server should return `{"isError": true, "errorCategory": "timeout", "isRetryable": true}` with backoff guidance. Coordinator uses this to implement exponential backoff
- C) Retry infinite times
- D) Stop using the MCP server

**Correct Answer:** B
**Feedback:** Structured error responses with retry metadata enable smart recovery. Returning `errorCategory: "timeout"` + `isRetryable: true` signals the coordinator to apply backoff. Without this, retries are dumb (immediate retry often fails again). Backoff is essential for transient failures.
**Theory Reference:** Domain 2.2 - MCP error responses and retry guidance

---

## Question 114
**Title:** Subagent Output Compression and Summarization
**Situation:** A research subagent returns 45K tokens of findings. The coordinator needs to synthesize from multiple subagents, but 45K per agent would exceed context limits. Should the coordinator: (1) request a summary, (2) store full output in a database, (3) use only the first 5K tokens?
**Options:**
- A) Request summaries: subagent provides 2-3 key findings + sources before returning full output
- B) Store full output and reference it later; lose no information
- C) Use only first 5K tokens
- D) Request fewer findings initially

**Correct Answer:** A
**Feedback:** Structured summaries (key findings + sources) from subagents enable efficient coordinator synthesis. The coordinator aggregates summaries (5-10K total) instead of raw output (45K+). This is more efficient and context-aware. Full outputs can be stored for reference if needed.
**Theory Reference:** Domain 1.2 - Subagent output summarization

---

## Question 115
**Title:** Escalation Confidence and Risk Tolerance
**Situation:** Your support agent has a confidence score for each resolution: (1) refund processing - 94% confidence, (2) troubleshooting steps - 67% confidence, (3) policy clarification - 81% confidence. What's a reasonable escalation threshold?
**Options:**
- A) Escalate everything below 90%
- B) Set per-category thresholds based on risk: high-risk (refunds) > 95%, medium-risk (policy) > 85%, low-risk (troubleshooting) > 70%
- C) Escalate everything below 50%
- D) Never escalate; always try to help

**Correct Answer:** B
**Feedback:** Escalation thresholds should match risk tolerance. High-risk decisions (financial) need higher confidence; low-risk (suggestions) can tolerate lower confidence. Per-category thresholds are more effective than uniform thresholds. This enables calibrated decision-making.
**Theory Reference:** Domain 5.5 - Confidence calibration and escalation

---

## Question 116
**Title:** Coordinator Task Sequencing for Dependency Chains
**Situation:** You have tasks: A (independent), B (depends on A), C (depends on B), D (depends on A). Optimal sequencing?
**Options:**
- A) A → B → C → D (strict sequence)
- B) A → {B, D in parallel} → C (spawn B and D after A, then C after B)
- C) Spawn all in parallel
- D) Random order

**Correct Answer:** B
**Feedback:** Dependency awareness enables parallelism. A runs first. After A completes, spawn B and D in parallel (both depend only on A). After B completes, spawn C (which depends on B). This is faster than strict sequencing while respecting dependencies. The coordinator should build a dependency graph.
**Theory Reference:** Domain 1.2 - Task dependency management

---

## Question 117
**Title:** Tool Description Precision and Agent Behavior
**Situation:** You have a tool with description: "Process data." An agent uses it for PDF conversion, database querying, and form validation—all wrong. The tool actually does: "Normalize and deduplicate user records." What's the fix?
**Options:**
- A) Blame the agent; it should infer the tool's purpose
- B) Rename and clarify: `normalize_deduplicate_users` with description "Normalize user records (standardize formats, capitalization) and remove duplicates. Input: user list. Output: deduplicated user records"
- C) Add parameters to `process_data` to specify what kind of processing
- D) Create examples in the system prompt

**Correct Answer:** B
**Feedback:** Precise tool names and descriptions prevent misuse. "Process data" is so vague it invites misuse. Renaming to `normalize_deduplicate_users` + specific description makes its scope unmistakable. Agents can't reliably infer from vague descriptions.
**Theory Reference:** Domain 2.1 - Tool naming and description clarity

---

## Question 118
**Title:** Handling Streaming Outputs in Multi-turn Workflows
**Situation:** You're implementing a multi-turn workflow where each subagent's output should be streamed to the coordinator as it arrives. But your current system batches all subagent output before sending to coordinator. Impact?
**Options:**
- A) Batching is fine; streaming adds no value
- B) Streaming enables partial results: coordinator can start synthesis on first 10% of results while subagent generates remaining 90%, reducing latency
- C) Streaming and batching are equivalent
- D) Batching is better for accuracy

**Correct Answer:** B
**Feedback:** Streaming outputs enable progressive refinement. The coordinator can begin synthesis on partial results while subagents complete remaining work. Batching requires waiting for all results before starting synthesis. Streaming is better for latency-sensitive applications.
**Theory Reference:** Domain 1.2 - Output streaming and progressive synthesis

---

## Question 119
**Title:** Error Recovery in Tool Chains
**Situation:** A workflow has three sequential tools: A → B → C. Tool B fails with a validation error (invalid input from A). Should the agent: (1) immediately fail the workflow, (2) retry B with the same input, (3) retry A to get different output, (4) ask A for clearer output and retry B?
**Options:**
- A) Immediately fail
- B) Retry B multiple times
- C) Retry A with feedback: "Your output failed validation in tool B. Try again with focus on X." Then retry B
- D) Skip tool B and go to C

**Correct Answer:** C
**Feedback:** Smart error recovery involves feedback loops. If B's input (from A) is invalid, retry A with specific guidance on what went wrong. This is more effective than blind retries of B. The feedback loop addresses root cause (A's output) rather than symptom (B's failure).
**Theory Reference:** Domain 4.4 - Error recovery with feedback loops

---

## Question 120
**Title:** Planning Mode for Understanding Before Action
**Situation:** You're in Claude Code and need to modify a config file that affects 50+ services. The file has dense YAML with inheritance, overrides, and environment-specific sections. Should you use Planning Mode?
**Options:**
- A) Direct Execution; just make the change
- B) Planning Mode; use the Explore subagent to understand the config structure, dependent services, and impact before making changes
- C) Planning Mode is for code, not config files
- D) Ask someone else

**Correct Answer:** B
**Feedback:** Planning Mode's Explore subagent is ideal for understanding complex configuration. It can map dependencies, highlight services affected, and surface risks before changes. For high-impact changes, exploration prevents costly mistakes.
**Theory Reference:** Domain 3.4 - Planning Mode for impact analysis

---

## Question 121
**Title:** Conversation History Pruning Strategy
**Situation:** An agent has been running for 100 iterations and the conversation history is 200K tokens. The agent is still relevant but coherence is declining. What's the best approach?
**Options:**
- A) Accept the full history; large windows are better
- B) Prune irrelevant tool calls from history, keeping only key findings and reasoning steps. Compress: 200K → 50K
- C) Start a fresh session
- D) Increase context window

**Correct Answer:** B
**Feedback:** Strategic pruning keeps relevant reasoning while removing verbose tool calls. A search tool returning 50K tokens of results—you only need the findings summary. Prune to: agent's reasoning, key tool outputs, conclusions. This preserves coherence without starting over.
**Theory Reference:** Domain 5.1 - Conversation history pruning

---

## Question 122
**Title:** Tool Authorization and Role-based Access
**Situation:** You have tools: `approve_refund`, `cancel_order`, `delete_user_account`. A support agent should be able to call `approve_refund` (up to $500) but not `delete_user_account`. How do you enforce this?
**Options:**
- A) Trust the system prompt to guide the agent
- B) Use the `allowed-tools` field or agent configuration to explicitly exclude `delete_user_account` from this agent's toolset
- C) Add permission checks inside each tool
- D) Create separate agents for different permission levels

**Correct Answer:** B
**Feedback:** Architectural constraints (allowed-tools) are more reliable than prompt-based guidance. The support agent's configuration should specify exactly which tools are available. This is enforced at the API level, not the prompt level—much more secure.
**Theory Reference:** Domain 3.2 - Allowed-tools constraints

---

## Question 123
**Title:** Distinguishing Tool Failure from Empty Results
**Situation:** A database query tool returns: (1) `{"error": "Database connection failed"}` or (2) `{"results": [], "query_count": 0, "queryExecuted": true}`. The agent should: escalate (1), continue with (2). But if the tool always returns JSON, how does the agent distinguish?
**Options:**
- A) By checking if `results` field exists
- B) By checking the `error` field first; if `error` exists → failure. If `results` and `queryExecuted: true` exist → valid empty result
- C) Both mean the same thing
- D) The tool should use different response formats

**Correct Answer:** B
**Feedback:** Structured response schemas distinguish failure from empty results. Check error field first for failures; check queryExecuted flag for valid results. Consistency enables reliable routing. Without structure, agents can't reliably distinguish cases.
**Theory Reference:** Domain 2.2 - Structured error responses

---

## Question 124
**Title:** Skill Activation with Path-specific Triggers
**Situation:** Your `/code-review` skill should activate automatically when reviewing TypeScript files, but not Python files. How do you set this up?
**Options:**
- A) Create a custom slash command that requires manual invocation
- B) Define the skill with `auto-trigger: true` and `paths: ["**/*.ts"]` frontmatter to activate only for matching files
- C) Always invoke the skill manually
- D) One skill cannot target specific file types

**Correct Answer:** B
**Feedback:** Skills can have `auto-trigger` and `paths` frontmatter to activate conditionally. A code review skill with `paths: ["**/*.ts"]` activates only when editing TypeScript files. This is more efficient than manual invocation.
**Theory Reference:** Domain 3.2 & 3.3 - Skill activation and path triggers

---

## Question 125
**Title:** Merging Conflicting Information from Multiple Sources
**Situation:** Two research subagents report different statistics: A says "unemployment is 3.8%", B says "unemployment is 4.2%". Both include sources and dates: A is from May 2024 (Bureau of Labor Statistics), B is from May 2024 (Census Bureau). What should the synthesis report?
**Options:**
- A) Average them: 4.0%
- B) Report both with sources and agencies: "BLS reports 3.8%, Census reports 4.2%. Discrepancy may reflect different methodologies. See sources for details."
- C) Choose the higher number as more conservative
- D) Ask which source is "official"

**Correct Answer:** B
**Feedback:** Conflicting data deserves full attribution. Report both values with sources, agencies, and possible explanations. Let readers judge. Averaging or choosing obscures the discrepancy. Transparency builds trust.
**Theory Reference:** Domain 5.6 - Handling conflicting information

---

## Question 126
**Title:** Incremental Tool Evaluation
**Situation:** You're building an agent that needs to understand a complex API. Rather than providing full API documentation upfront, you have two strategies: (1) provide everything and let the agent explore, (2) as the agent encounters unknown endpoints, provide documentation for just those endpoints. Which scales better?
**Options:**
- A) Strategy 1; full upfront documentation is clearer
- B) Strategy 2 (incremental/on-demand); it reduces context load and focuses learning on relevant endpoints
- C) Both are equivalent
- D) Neither works for complex APIs

**Correct Answer:** B
**Feedback:** Incremental information reduces cognitive load. The agent doesn't need full API docs upfront. When it encounters an unknown endpoint, provide docs for that endpoint. This is cheaper in tokens and clearer for the agent. Large upfront docs often go unused.
**Theory Reference:** Domain 5.4 - Incremental context loading

---

## Question 127
**Title:** Subagent Result Deduplication
**Situation:** You spawn 3 subagents to research the same question independently (for coverage/reliability). Each returns similar findings. When synthesizing, should you: (1) report each result, (2) deduplicate and report unique findings?
**Options:**
- A) Report all; duplication shows consensus
- B) Deduplicate and report unique findings, note which subagents found which facts (maintains credibility without redundancy)
- C) Combine all into one narrative
- D) Choose one result and discard others

**Correct Answer:** B
**Feedback:** Deduplication improves clarity without losing credibility. Report unique findings once, note which subagents found them ("Found by A, B, C"). This shows consensus without repetition. Synthesized reports should be concise while maintaining attribution.
**Theory Reference:** Domain 1.2 - Multi-subagent synthesis and deduplication

---

## Question 128
**Title:** Handling Ambiguous Policy Boundaries
**Situation:** An agent encounters: "Can I give a 30% discount?" Policy says: "Up to 25% for loyal customers, up to 15% for others." Customer history is unclear (was with company 3 years ago, recently reactivated). The agent shouldn't guess at loyalty status. What should it do?
**Options:**
- A) Apply the stricter rule (15%)
- B) Apply the more generous rule (25%)
- C) Escalate with full context: customer history, policy, request, and ambiguity. Let human decide
- D) Refuse the discount

**Correct Answer:** C
**Feedback:** Policy ambiguities require human judgment. Escalate with structured context: customer history, applicable policies, the specific ambiguity, and recommendation if any. Humans can make judgment calls; agents should not guess at policy intent.
**Theory Reference:** Domain 5.2 - Escalation with ambiguity clarification

---

## Question 129
**Title:** Tool Result Caching in Agent Loops
**Situation:** An agent calls `fetch_user_profile` for user_id=123 twice in one session (5 iterations apart). The second call repeats the same query. Should you: (1) cache the first result and reuse it, (2) fetch again in case data changed?
**Options:**
- A) Always fetch again; data might have changed
- B) Cache within the same session (5 iterations is short); reuse cached results. If stale data is a concern, invalidate cache per turn
- C) Cache for 24 hours
- D) Never cache

**Correct Answer:** B
**Feedback:** Session-level caching reduces redundant calls. Within a single agent session (5 iterations), data is unlikely to have changed. Reuse cached results for efficiency. If fresh data is critical, invalidate cache at turn boundaries or provide cache TTL guidance.
**Theory Reference:** Domain 2.5 - Tool result caching in loops

---

## Question 130
**Title:** Explicit Task Boundaries for Subagents
**Situation:** You spawn a subagent with: "Research electric vehicles." After 2 hours, it's still researching (battery types, charging infrastructure, government incentives, manufacturing). The task is unbounded. What's the fix?
**Options:**
- A) Accept the thorough research
- B) Set explicit boundaries: "Research electric vehicle adoption rates in the US market, 2020-2024. Focus on consumer trends and adoption barriers. Provide summary with key statistics."
- C) Give it more time
- D) Sub-agents can't handle complex research

**Correct Answer:** B
**Feedback:** Bounded tasks produce better results. Specify: geography (US), time period (2020-2024), focus areas (adoption, barriers), output format (summary with statistics). Without boundaries, research sprawls endlessly. Clear task definition = focused execution.
**Theory Reference:** Domain 1.3 - Task definition and subagent control

---

## Question 131
**Title:** CLAUDE.md Hierarchy and Inheritance
**Situation:** You have: `/CLAUDE.md` (project-wide), `/frontend/CLAUDE.md` (frontend-specific), `/frontend/components/CLAUDE.md` (component-specific). When editing a frontend component, which rules apply?
**Options:**
- A) Only `/frontend/components/CLAUDE.md`
- B) Only `/frontend/CLAUDE.md`
- C) All three, with most-specific (component level) overriding less-specific
- D) Only `/CLAUDE.md`

**Correct Answer:** C
**Feedback:** CLAUDE.md hierarchy cascades from root to most-specific. Component-level rules override package-level, which override project-level. When editing `/frontend/components/Button.tsx`, all three files apply, with nearest-ancestor overriding. This enables both consistency and flexibility.
**Theory Reference:** Domain 3.1 - CLAUDE.md hierarchy and scoping

---

## Question 132
**Title:** Pre-execution Validation in Workflows
**Situation:** Your workflow has 5 tools that must execute in order. Before tool 1 runs, should you validate all 5 inputs upfront?
**Options:**
- A) No, validate each tool's input just before execution
- B) Yes, validate all inputs upfront to catch issues early and fail fast
- C) Validation is unnecessary; tools handle their own validation
- D) Validate only critical tools

**Correct Answer:** B
**Feedback:** Upfront validation catches issues early, saves time, and improves UX. If input 5 is invalid, you discover it before wasting time on tools 1-4. Fail-fast principle. However, if tool outputs feed into subsequent tools, you can't fully validate until prior tool runs.
**Theory Reference:** Domain 1.4 - Workflow validation and enforcement

---

## Question 133
**Title:** Handling Tool Unavailability
**Situation:** Your agent usually calls `premium_analysis_tool`, but it's temporarily unavailable. You have a `standard_analysis_tool` that's slower but always available. Should the agent: (1) fail, (2) automatically degrade to standard, (3) skip analysis, (4) ask the user?
**Options:**
- A) Fail completely
- B) Automatically degrade: call standard tool with a note that results may be slower. This ensures resilience without user interruption
- C) Skip analysis
- D) Ask the user to wait

**Correct Answer:** B
**Feedback:** Degradation patterns improve resilience. When preferred tools are unavailable, automatically use alternatives if they exist. Note the degradation in output so users understand potential impact. This is better than failure or blocking.
**Theory Reference:** Domain 5.3 - Graceful degradation and fallback strategies

---

## Question 134
**Title:** Agent Context Carryover Between Sessions
**Situation:** You end a Claude Code session after analyzing file A. You start a new session, and the agent immediately mentions findings from file A even though you didn't re-read it. What's happening?
**Options:**
- A) Sessions share context automatically
- B) Sessions are isolated; the agent is hallucinating or confusing instructions. Sessions do not automatically inherit prior context
- C) The agent remembered from the previous session
- D) You accidentally resumed the previous session

**Correct Answer:** B
**Feedback:** Claude Code sessions are isolated. Each new session starts fresh. If the agent mentions findings from a prior session without you re-reading them, it's hallucinating. To carry context forward, use scratchpad files or explicitly include findings in the new session prompt.
**Theory Reference:** Domain 1.7 - Session isolation and context transfer

---

## Question 135
**Title:** Specialization vs Generalization in Agent Design
**Situation:** You're redesigning a customer support system. Current: one general agent handling all issues (refunds, tech support, account, billing). Proposed: four specialized agents (each handles one type). Trade-offs?
**Options:**
- A) General agent is simpler; keep it
- B) Specialized agents are more accurate and maintainable, each with focused tools and knowledge. Coordinator routes tickets appropriately
- C) Specialization is slower
- D) Both approaches are equivalent

**Correct Answer:** B
**Feedback:** Specialization improves accuracy. A refund agent with only refund-related tools is more reliable than a general agent juggling multiple domains. Coordinator routes based on issue type. Maintenance is easier—each agent is focused. This is the hub-and-spoke pattern.
**Theory Reference:** Domain 1.2 - Specialized subagents

---

## Question 136
**Title:** Feedback Loop Closure in Extraction
**Situation:** An extraction system finds conflicting data within a single document: "Customer balance is $500" in one section, "$450" in another. It flags the conflict but doesn't resolve it. The coordinator receives a flag but no data. What's better?
**Options:**
- A) Just flag it and let the coordinator handle it
- B) Extraction system should extract both values with location info ("$500 found in Section A, $450 found in Section B"). Coordinator can then decide if this is a data quality issue or if one is actually correct
- C) Choose the first value
- D) Mark the document as invalid

**Correct Answer:** B
**Feedback:** Don't just flag problems—provide the data needed to resolve them. Extract both conflicting values with locations. This enables the coordinator to investigate (Is one a correction? Is it a data quality issue?). Bare flags are unhelpful.
**Theory Reference:** Domain 4.4 - Structured error reporting

---

## Question 137
**Title:** Coordinate vs Chaos in Concurrent Subagents
**Situation:** You spawn 10 subagents to process 10 different tasks. They all communicate directly with each other, sharing results ad-hoc. Management becomes chaotic. What's the architectural fix?
**Options:**
- A) Reduce to fewer subagents
- B) All subagents report to the coordinator only. Coordinator orchestrates all communication and result sharing (hub-and-spoke)
- C) Have one "super-subagent" manage the others
- D) Direct communication is fine

**Correct Answer:** B
**Feedback:** Hub-and-spoke (coordinator in the middle) is essential for observability and control. Direct subagent communication causes chaos—you lose visibility. All results flow through the coordinator, which sequences work, aggregates findings, and makes decisions. This is a proven pattern.
**Theory Reference:** Domain 1.2 - Hub-and-spoke architecture

---

## Question 138
**Title:** MCP Server Lifecycle and Resource Management
**Situation:** Your MCP server maintains a large in-memory cache (10GB). Every time a Claude Code session starts, it initializes a new server instance, allocating another 10GB. After 10 sessions, you've allocated 100GB. What's the issue and fix?
**Options:**
- A) This is expected; allocate more hardware
- B) MCP servers should be persistent (single instance shared across sessions) with lifecycle management. Implement connection pooling and cache reuse
- C) Restart the server frequently
- D) Use smaller caches

**Correct Answer:** B
**Feedback:** MCP servers should be long-lived, not per-session. Initialize once, share across sessions. Use connection pooling and lifecycle hooks to manage resources. Per-session initialization wastes memory and time. Persistence is the standard pattern.
**Theory Reference:** Domain 2.4 - MCP server lifecycle management

---

## Question 139
**Title:** Handling Incomplete Extraction Across Batches
**Situation:** Your Batch API job processes 1000 documents. 950 succeed, but 50 fail with "structure not recognized." The failures are scattered throughout the batch. How should you handle them?
**Options:**
- A) Accept 95% success rate and discard the 50 failures
- B) Identify the 50 failures by `custom_id`, retry them in a separate batch with fallback extraction logic or manual review marking
- C) Reprocess the entire batch
- D) Mark all 1000 as failed

**Correct Answer:** B
**Feedback:** Use `custom_id` to identify failures, retry selectively in a second batch with different extraction parameters or human review. This is more efficient than reprocessing all 1000 or discarding failures. Batch API enables this workflow via custom IDs.
**Theory Reference:** Domain 4.5 - Handling batch failures selectively

---

## Question 140
**Title:** Prompt Chaining with Information Reduction
**Situation:** You have a 200K-token document to summarize, analyze, and extract entities. One approach: single prompt handling all three. Better approach: (1) summarize (output: 10K), (2) analyze summary (output: 5K), (3) extract entities from analysis (output: 2K). Why is stage-wise better?
**Options:**
- A) Processing all at once is more efficient
- B) Stage-wise reduces information volume: 200K → 10K → 5K → 2K. Each stage focuses on essentials, reducing noise and improving focus
- C) Stage-wise is slower
- D) Both are equivalent

**Correct Answer:** B
**Feedback:** Information reduction through stages is more effective than single-pass processing. Each stage distills information to essentials. Summarization removes irrelevant details; analysis focuses on key insights; extraction targets specific entities. Noise is eliminated progressively.
**Theory Reference:** Domain 8.1 - Fixed pipelines and information reduction

---

## Question 141
**Title:** Coordinator Buffering vs Real-time Subagent Results
**Situation:** You have a coordinator receiving results from 5 concurrent subagents. Should the coordinator: (1) buffer all results and synthesize once all 5 are complete, or (2) process results progressively as they arrive?
**Options:**
- A) Buffer all; consistency is important
- B) Process progressively: start synthesis on first result, refine as others arrive. This reduces latency and enables partial results if needed
- C) Discard early results
- D) Process them randomly

**Correct Answer:** B
**Feedback:** Progressive processing reduces latency. The coordinator can begin synthesis on the first 2-3 results while waiting for others. If you need to release partial results quickly, progressive processing is essential. Buffering delays output unnecessarily.
**Theory Reference:** Domain 1.2 - Progressive result processing

---

## Question 142
**Title:** Tool Result Pagination
**Situation:** A search tool returns: `{"results": [list of 10], "total_count": 10000, "page": 1}`. The agent needs to analyze 10,000 results. Should it: (1) request all 1000 pages sequentially, (2) request only first page and analyze, (3) sample pages strategically?
**Options:**
- A) Request all 1000 pages
- B) Context-aware sampling: request first page, last page, and random samples to estimate distribution. Analyze strategically rather than exhaustively
- C) Only first page
- D) None; 10,000 results is too many

**Correct Answer:** B
**Feedback:** Strategic sampling is better than exhaustive retrieval. First page (top-ranked results), last page (tail), and random samples give a representative view. Analyzing samples often answers the question without context explosion. This is a practical pattern for large result sets.
**Theory Reference:** Domain 5.4 - Strategic sampling from large datasets

---

## Question 143
**Title:** API Schema Versioning and Compatibility
**Situation:** Your MCP API was at version 1.0 with field `user_id`. Version 2.0 changes this to `userId` (camelCase). Old code still expects `user_id`. How do you handle the transition?
**Options:**
- A) Break everyone immediately; force upgrade to v2.0
- B) Deprecation period: v2.0 returns both fields (`user_id` for backward compat, `userId` for new code). Clearly mark `user_id` as deprecated
- C) Keep v1.0 forever
- D) Hide the breaking change

**Correct Answer:** B
**Feedback:** Backward-compatible deprecation is best practice. Return both old and new field names during transition. Mark old fields as deprecated in docs. Give users time to migrate. Breaking changes cause pain; gradual transitions are smoother.
**Theory Reference:** Domain 2.1 - API backward compatibility

---

## Question 144
**Title:** Conditional Tool Availability
**Situation:** You have a `delete_user_account` tool that should only be available to admin-level agents, never to general support agents. How do you enforce this?
**Options:**
- A) Include the tool in all agent configurations and hope the agent doesn't misuse it
- B) Use role-based tool assignment: include `delete_user_account` in admin agent config, explicitly exclude from support agent config
- C) Trust the system prompt to prevent misuse
- D) Create duplicate agents with different tool sets

**Correct Answer:** B
**Feedback:** Role-based tool assignment is a configuration-level security control. Don't include the tool in a support agent's allowed-tools. This is deterministic—the tool simply isn't available. Prompt-based restrictions are probabilistic and can be bypassed.
**Theory Reference:** Domain 3.2 - Role-based tool configuration

---

## Question 145
**Title:** Coordinator Timeout and Partial Results
**Situation:** Coordinator spawns 5 subagents. After 5 minutes, 3 have returned results but 2 are still processing. Should the coordinator: (1) wait indefinitely, (2) timeout and synthesize from available results, (3) cancel the slow subagents?
**Options:**
- A) Wait indefinitely
- B) Timeout with configurable patience: synthesize from available results with a note about incomplete coverage. This enables faster delivery of partial results
- C) Cancel slow subagents
- D) Restart the workflow

**Correct Answer:** B
**Feedback:** Coordinated timeouts enable graceful degradation. If 3/5 results arrive quickly, synthesize from those with a note: "Complete findings from X, partial from Y, pending from Z." This is better than infinite waiting or discarding slow results. Users get value sooner.
**Theory Reference:** Domain 5.2 - Timeout strategies and partial results

---

## Question 146
**Title:** Skill Context Preservation
**Situation:** You invoke a `/research` skill from within a Claude Code session. The skill forks context and runs in an isolated subagent. After it completes, should its findings automatically be available in your main session?
**Options:**
- A) Yes, all findings are preserved automatically
- B) No, isolated context means isolated results. The findings exist in the subagent; you must explicitly reference them or request a summary to bring them into main session
- C) Findings are available but hidden
- D) Skills don't support context forking

**Correct Answer:** B
**Feedback:** `context: fork` creates isolation—results don't automatically flow back. To use skill findings in your main session, explicitly reference the findings or request a summary. This isolation is intentional (prevents context pollution) but requires manual retrieval.
**Theory Reference:** Domain 3.2 - Skill context forking and result retrieval

---

## Question 147
**Title:** Query Optimization in Tool Calls
**Situation:** An agent needs to find "customers in California who spent over $1000." It has two options: (1) fetch all customers (100K), filter locally in agent logic, or (2) call database tool with filter: "state='CA' AND total_spending > 1000". Which is better?
**Options:**
- A) Fetch all and filter locally
- B) Push filtering to the tool (option 2). It's faster, uses less bandwidth, and puts work on the system designed for it
- C) Both are equivalent
- D) Filtering at tool level is dangerous

**Correct Answer:** B
**Feedback:** Filters should be pushed to the source (database tool). Fetching 100K records locally wastes bandwidth and context. Database-side filtering is optimized, faster, and scalable. This is a fundamental database principle.
**Theory Reference:** Domain 2.1 - Tool design and query optimization

---

## Question 148
**Title:** Handling Tool Deprecation Gracefully
**Situation:** You have a `fetch_weather_v1` tool that's being replaced by `fetch_weather_v2` (better accuracy). Some agents still use v1, some use v2. How should you transition?
**Options:**
- A) Immediately remove v1; force everyone to v2
- B) Deprecation period: v1 returns results + deprecation warning ("This tool is deprecated. Use fetch_weather_v2 instead"). After 30 days, remove v1
- C) Keep v1 forever
- D) Run both versions in parallel

**Correct Answer:** B
**Feedback:** Graceful deprecation: return results + warning, then timeline to removal. Gives agents time to update configs. If you remove v1 abruptly, agent configs break. Planned deprecation is smoother.
**Theory Reference:** Domain 2.1 - Tool lifecycle and deprecation

---

## Question 149
**Title:** Synthesizing from Contradictory Subagent Reports
**Situation:** Two subagents report: (1) "Feature X is broken" (with reproduction steps), (2) "Feature X works perfectly" (with test results). Coordinator needs to synthesize. What approach?
**Options:**
- A) Choose one; the other is wrong
- B) Report both with full context: what conditions trigger the issue (A's steps vs B's test environment), which configuration causes the discrepancy. This reveals the real issue
- C) Escalate without investigating
- D) Assume one agent is malfunctioning

**Correct Answer:** B
**Feedback:** Contradictions signal incomplete context. The issue likely depends on environmental conditions (version, config, OS). Report both findings and note the difference: "Works in test environment (B), breaks in production (A) under specific conditions." This guides root cause investigation.
**Theory Reference:** Domain 5.6 - Synthesizing contradictory findings

---

## Question 150
**Title:** Session Forking for Hypothesis Testing
**Situation:** You're analyzing a codebase and have two competing hypotheses about an architecture issue. You want to explore both independently without contaminating each other. What technique enables this?
**Options:**
- A) Continue in the same session and track both hypotheses mentally
- B) Use `fork_session` to create two independent investigation branches from the current session. Explore both hypotheses separately
- C) Use two entirely separate sessions
- D) Combine hypotheses in one analysis

**Correct Answer:** B
**Feedback:** `fork_session` creates independent subagent contexts that branch from your current checkpoint. Ideal for hypothesis testing. Both branches start with the same context but diverge. You can compare results and choose the stronger hypothesis.
**Theory Reference:** Domain 1.7 - fork_session for parallel exploration

---

## Summary

**Batch 03 contains 50 questions (Questions 111-160)**
- Difficulty level: Consistent with prior batches
- Coverage: All 5 domains + advanced topics
- Format: Ready to merge into `questionBank` array
- Questions focus on real-world complexities and nuanced decisions

**Progress: 160 total questions collected**
- Original: 10 questions
- Batch 01: 50 questions
- Batch 02: 50 questions
- Batch 03: 50 questions
- **Total so far: 160 questions** ✅

