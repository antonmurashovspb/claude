# Quiz Question Bank - Batch 02 Part 03 (Questions 81-90)

## Question 81
**Title:** Testing Standards in CLAUDE.md
**Situation:** Your project has 12 packages, each with different testing frameworks. You want all packages to follow standard testing practices (test naming, coverage requirements, fixture patterns). Where should these standards be defined to avoid duplication?
**Options:**
- A) In a project-level `.claude/rules/testing.md` with `paths: ["**/*.test.*"]` to apply to all test files
- B) In each package's CLAUDE.md (duplicated across all 12 packages)
- C) In a separate testing documentation website outside the repo
- D) Communicated verbally to the team during onboarding

**Correct Answer:** A
**Feedback:** Path-scoped rules in `.claude/rules/testing.md` with `paths: ["**/*.test.*"]` apply testing standards to all test files across all packages, without duplication. This is DRY and scales.
**Theory Reference:** Domain 3.3 - Path-specific rules for codebase-wide conventions

---

## Question 82
**Title:** API vs Resource in Fetch Operations
**Situation:** Your agent needs to load a codebase CLAUDE.md file. Should it: (1) call a `fetch_file` tool via API, or (2) check if the file is available as an MCP resource?
**Options:**
- A) Check for MCP resource first; if available, use it (cached, faster). Fall back to tool if not available
- B) Always use the tool; tools are general-purpose
- C) Always use MCP resources; never call tools
- D) Ask the user to provide the file manually

**Correct Answer:** A
**Feedback:** MCP resources are pre-loaded and cached; tools require dynamic calls. If a resource is available, use it first (faster, fewer tokens). Fall back to tools for dynamic data. This hybrid approach is optimal.
**Theory Reference:** Domain 2.4 - MCP resources vs tool calls

---

## Question 83
**Title:** Iterative Refinement with Test Cases
**Situation:** You're asking Claude Code to implement a data transformation. Instead of providing a 500-word specification, you provide: (1) 3 input examples, (2) expected output for each, (3) edge case notes. How effective is this approach vs prose?
**Options:**
- A) Examples are the most effective way to communicate expectations; concrete input/output pairs guide implementation better than prose
- B) Examples are helpful but less important than detailed prose specifications
- C) Examples slow down implementation by forcing the model to process extra data
- D) Use only prose; examples are redundant with detailed text

**Correct Answer:** A
**Feedback:** Concrete examples (input + expected output) are far more effective than prose. They directly demonstrate intent and handle ambiguity. Claude can implement from examples faster and more accurately than from text descriptions.
**Theory Reference:** Domain 3.5 - Iterative refinement with concrete examples

---

## Question 84
**Title:** Agent Specialization vs Generalization
**Situation:** You have a general "customer support" agent handling refunds, billing questions, technical support, and account issues. It's 40% effective at each. You consider splitting into specialized agents. Would specialization help?
**Options:**
- A) Split into specialized agents; focused agents are more reliable and can maintain specialized knowledge
- B) Keep one general agent; splitting adds complexity without proven benefit
- C) One agent is fine; just add more tools to improve performance
- D) Keep one agent but provide better training data

**Correct Answer:** A
**Feedback:** Specialized agents outperform generalists. A refund agent with refund-specific tools and context is more effective (80%+) than a general agent splitting attention (40%). Hub-and-spoke architecture enables better performance and easier maintenance.
**Theory Reference:** Domain 1.2 - Specialized subagents vs generalists

---

## Question 85
**Title:** Tool Results and Long Conversation History
**Situation:** An agent makes 50 tool calls over 20 iterations. Each result (average 500 tokens) is appended to history. After 50 calls, the conversation is 25K tokens of tool results + reasoning. Performance degrades significantly. Why?
**Options:**
- A) Context degradation: long conversations lose coherence. Use subagents or scratchpads to reset context
- B) The model needs a better system prompt to maintain focus
- C) Conversation history accumulation is normal; ignore performance decline
- D) Increase the context window size to process longer histories

**Correct Answer:** A
**Feedback:** Long conversation histories degrade model performance. After 20-30 iterations, coherence starts declining. Mitigation: spawn subagents for specific subtasks (isolated context), use scratchpads for intermediate findings, or restart with key findings summarized.
**Theory Reference:** Domain 5.4 - Context degradation and subagent delegation

---

## Question 86
**Title:** Empty Results vs Access Failures in Search
**Situation:** Two search subagents return: (1) `{"results": [], "query_executed": true, "query": "solar adoption 2024"}` - query succeeded, zero matches; (2) `{"results": null, "error": "Database connection timeout"}` - access failure. Are these equivalent?
**Options:**
- A) (1) is a valid result (no matches found), continue. (2) is an access failure, retry or escalate
- B) Both represent "no data available"; treat them identically
- C) Both are errors; escalate both to a human
- D) Ignore both and continue processing

**Correct Answer:** A
**Feedback:** Structured results distinguish success from failure. Query succeeded but zero matches is valid (no articles on that topic). Database unavailable is a failure (different handling—retry or escalate). Confusing these breaks decision logic.
**Theory Reference:** Domain 5.3 - Distinguishing access failures from empty results

---

## Question 87
**Title:** Multi-turn Tool Use in Batch API
**Situation:** You're using the Batch API to process 1000 documents overnight. Your processing logic requires multi-turn tool use: (1) extract data, (2) validate, (3) if validation fails, retry extraction. Does Batch API support this?
**Options:**
- A) No, Batch API does not support multi-turn tool use within a single request
- B) Yes, Batch API fully supports multi-turn interactions
- C) Only for up to 3 tool calls per request
- D) Batch API doesn't support tools at all

**Correct Answer:** A
**Feedback:** Batch API is designed for single-turn requests. Multi-turn logic (where each response determines the next action) is not supported. For multi-turn workflows, use the synchronous API with tool use, or pre-process documents with single-turn extraction.
**Theory Reference:** Domain 4.5 & Chapter 7 - Batch API limitations

---

## Question 88
**Title:** Tool Selection with Ambiguous Descriptions
**Situation:** You have tools: `search` and `retrieve`. Both can access documents. Description for `search`: "Find documents." Description for `retrieve`: "Get documents." An agent frequently uses the wrong tool. Why?
**Options:**
- A) The descriptions are identical in function; differentiate them. Is `search` for keyword-based lookup? Is `retrieve` for fetching by ID?
- B) The agent needs better training data
- C) Use a parameter to distinguish; combine both into one tool
- D) Combine the tools into a single, more general tool

**Correct Answer:** A
**Feedback:** Ambiguous descriptions cause unreliable selection. "Find documents" (search) and "Get documents" (retrieve) sound interchangeable. Clarify: "search: Find documents by keyword/content match. retrieve: Fetch documents by ID." Distinct, clear descriptions prevent confusion.
**Theory Reference:** Domain 2.1 - Clear, distinct tool descriptions

---

## Question 89
**Title:** Coordinator Role in Error Handling
**Situation:** A subagent encounters a temporary API error and implements local retry logic (3 retries with backoff). If all retries fail, what should the subagent do?
**Options:**
- A) Return structured error to the coordinator: failure type, what was attempted, whether retry was tried. Let the coordinator decide next steps
- B) Silently fail and return empty results; don't bother the coordinator
- C) Implement infinite retries until the API responds
- D) Escalate immediately to a human without context

**Correct Answer:** A
**Feedback:** Subagents should attempt local recovery (retry transient errors). If recovery fails, report structured error to the coordinator: what failed, was retry attempted, why it failed. The coordinator then decides: retry differently, escalate, skip, or notify user.
**Theory Reference:** Domain 5.3 & 1.2 - Error propagation and coordinator oversight

---

## Question 90
**Title:** Batching vs Sequential Tool Calls
**Situation:** An agent needs to fetch 10 user profiles. Option 1: Call `fetch_profile` 10 times sequentially. Option 2: Batch all 10 into one call with an MCP resource or batch-capable tool. Which is more efficient?
**Options:**
- A) Batch all 10 in one call; MCP resources handle concurrency, reducing total calls and latency
- B) Sequential is simpler and easier to understand
- C) Both are equally efficient; choose based on preference
- D) Sequential is actually faster due to reduced network overhead

**Correct Answer:** A
**Feedback:** Batching reduces API calls and latency. If an MCP resource or tool supports batch operations, use it. One call for 10 profiles is more efficient than 10 sequential calls. Batching enables parallelization and reduces overhead.
**Theory Reference:** Domain 2.4 - MCP resource batching
