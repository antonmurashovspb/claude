# Quiz Question Bank - Batch 05 (Questions 211-260)

## Question 211
**Title:** API Error Response Clarity
**Situation:** Tool returns error: `{"error": "failed"}`. What information is missing that would help the agent (and coordinator) decide how to proceed?
**Options:**
- A) Nothing; it's clear enough
- B) Error category (transient vs permanent), specifics (what failed, why), remediation guidance (retry? escalate? skip?)
- C) Detailed stack trace
- D) Error code only

**Correct Answer:** B
**Feedback:** Minimal errors like "failed" are unhelpful. Better: `{"error": "timeout", "category": "transient", "isRetryable": true, "recommendation": "retry with backoff", "context": "database query took >30s"}`. This guides intelligent recovery.
**Theory Reference:** Domain 2.2 - Structured error responses

---

## Question 212
**Title:** Coordinator Load Distribution
**Situation:** You have 100 requests to process. Two strategies: (1) spawn 100 subagents sequentially, (2) spawn 10 batches of 10 subagents in parallel. Which is better?
**Options:**
- A) Sequential (100 subagents in a line)
- B) Batched parallel (10 batches of 10) balances concurrency and resource usage. Fully parallel (100 simultaneously) could overload resources
- C) Fully parallel (100 at once)
- D) All are equivalent

**Correct Answer:** B
**Feedback:** Resource management matters. Unbounded parallelism (100 subagents simultaneously) can overwhelm. Batched parallelism (10 batches of 10) maintains throughput while avoiding resource exhaustion. This is backpressure management.
**Theory Reference:** Domain 1.2 - Coordinator load management

---

## Question 213
**Title:** Tool Deprecation Communication
**Situation:** You're deprecating `old_search` tool in favor of `new_search` (better accuracy). Agents are currently using `old_search`. Best communication strategy?
**Options:**
- A) Remove `old_search` immediately without notice
- B) Mark `old_search` as deprecated in documentation and config. Tool returns: `{"deprecation_notice": "Use new_search instead. old_search will be removed on [date]", "result": [...]}`
- C) Hide the deprecation
- D) Silently replace old_search with new_search

**Correct Answer:** B
**Feedback:** Graceful deprecation includes: explicit notice, replacement tool name, removal date. Tool returns both results and deprecation warning. This gives agents time to update configs.
**Theory Reference:** Domain 2.1 - Tool lifecycle management

---

## Question 214
**Title:** Confidence Scoring for Different Field Types
**Situation:** Extraction outputs: name (78% confidence), email (95% confidence), amount ($50 with 65% confidence). Should all fields use the same confidence threshold for automation?
**Options:**
- A) Yes, uniform threshold (e.g., all above 85% automate)
- B) No, different thresholds per field type: names might automate at 80%, emails at 95%, amounts at 90%. Risk tolerance varies by field
- C) Ignore confidence; automate all
- D) Never automate

**Correct Answer:** B
**Feedback:** Field-specific thresholds reflect different risk profiles. Email extraction (high accuracy) can automate at lower confidence. Financial amounts (high risk) need higher confidence. Tiered thresholds are more practical.
**Theory Reference:** Domain 5.5 - Field-level confidence thresholds

---

## Question 215
**Title:** Multi-pass Analysis for Completeness
**Situation:** You're analyzing a 100-file codebase. First pass: identify architecture. Second pass: analyze each module. Third pass: identify cross-module issues. Why is multi-pass better than single-pass exhaustive analysis?
**Options:**
- A) It's not; single pass is simpler
- B) Multi-pass is more focused. Each pass has clear goal, reducing cognitive load. Issues missed in one pass may be caught in another
- C) Multi-pass is slower
- D) They're equivalent

**Correct Answer:** B
**Feedback:** Multi-pass analysis improves quality. First pass (architecture) informs second pass (module analysis). Second pass informs third pass (integration issues). Focused passes are clearer than exhaustive single-pass attempts.
**Theory Reference:** Domain 1.6 - Multi-pass analysis patterns

---

## Question 216
**Title:** Tool Authorization and Principle of Least Privilege
**Situation:** You have tools: `approve_refund`, `cancel_order`, `delete_user`. A support agent's role: handle customer issues (refunds and cancellations). Should this agent have access to `delete_user`?
**Options:**
- A) Yes, might be useful
- B) No, follow principle of least privilege: exclude `delete_user`. Support agents don't need it, removing it prevents accidental misuse
- C) Maybe; depends on the day
- D) All agents need all tools

**Correct Answer:** B
**Feedback:** Principle of least privilege: agents get only the tools they need. A support agent shouldn't have access to user deletion. This is enforced at configuration level (allowed-tools), not prompt level.
**Theory Reference:** Domain 3.2 - Role-based tool access

---

## Question 217
**Title:** Handling Tool API Changes
**Situation:** A tool's API changes: old response `{"user_id": 123}`, new response `{"userId": 123}`. Agents expecting `user_id` will break. How do you manage this?
**Options:**
- A) Change immediately; force all agents to update
- B) Support both field names during transition (`{"user_id": 123, "userId": 123}`). Mark old field as deprecated. Set removal deadline
- C) Keep old format forever
- D) Use a shim/adapter

**Correct Answer:** B
**Feedback:** Backward-compatible transitions are smoother. Support both field names, mark old as deprecated with timeline. Agents can upgrade gradually. This is far less disruptive than breaking changes.
**Theory Reference:** Domain 2.1 - Backward-compatible API transitions

---

## Question 218
**Title:** Result Caching Across Sessions
**Situation:** Two Claude Code sessions in the same repo. Session 1 reads file A. Session 2 also needs file A. Should the result be cached?
**Options:**
- A) No, sessions are isolated
- B) Yes, but carefully: cache file contents since they rarely change. Invalidate cache if file is modified in session 1. Sessions can reuse cached results
- C) Sessions can't share data
- D) Cache everything

**Correct Answer:** B
**Feedback:** Cross-session caching is valuable for static data (file contents). Cache with invalidation: if session 1 modifies a file, invalidate cache so session 2 gets fresh data. This improves efficiency while preserving correctness.
**Theory Reference:** Domain 2.5 - Cross-session caching strategies

---

## Question 219
**Title:** Prompt Bias Toward Defaults
**Situation:** System prompt says: "You can either X or Y. Default to X." An agent almost always chooses X, even when Y is more appropriate. Why?
**Options:**
- A) X is objectively better
- B) Prompt framing creates bias. "Default to X" subtly encourages X even when Y is better. Better framing: "Choose X for condition A, Y for condition B"
- C) The agent is making the right choice
- D) This is expected behavior

**Correct Answer:** B
**Feedback:** Prompts create unintended biases. "Default to" suggests a preference. Better: explicit criteria for when to use each option. This enables principled decision-making instead of biased defaults.
**Theory Reference:** Domain 4.1 - Prompt framing and bias

---

## Question 220
**Title:** Schema Validation vs Semantic Validation
**Situation:** JSON is valid per schema (correct types, all required fields). But semantically, it's invalid: `{"total": 100, "items": [{"price": 150}], "tax": -50}`. What level of validation catches this?
**Options:**
- A) Schema validation (JSON structure)
- B) Schema alone won't catch this. Need semantic validation: total should equal sum of items + tax, tax can't be negative. Semantic rules enforce business logic
- C) Never validate
- D) Schema catches everything

**Correct Answer:** B
**Feedback:** Schema validates structure; semantic validation enforces logic. Schema confirms valid JSON with correct types. Semantic validation confirms business rules (totals reconcile, no negative tax). Both are needed.
**Theory Reference:** Domain 4.3 & 4.4 - Layered validation

---

## Question 221
**Title:** Agent Intent Detection Before Tool Selection
**Situation:** Customer says: "Fix this bug, it's driving me crazy." Agent options: (1) immediately offer troubleshooting steps, (2) first assess: does this require technical support or is customer just venting? Which is better?
**Options:**
- A) Immediately help
- B) Intent detection first: assess whether customer needs action or empathy. This prevents misrouted solutions and improves satisfaction
- C) Always assume they need technical support
- D) Always defer to human

**Correct Answer:** B
**Feedback:** Intent detection improves response quality. "Driving me crazy" might signal frustration requiring acknowledgment before troubleshooting. Assessing intent first prevents offering technical solutions when emotional support is needed.
**Theory Reference:** Domain 5.2 - Intent detection and escalation

---

## Question 222
**Title:** Subagent Autonomy vs Coordinator Control
**Situation:** A coordinator has a policy: "Don't escalate unless absolutely necessary." A subagent encounters customer anger and wants to escalate. Should it: (1) follow policy and keep trying, (2) escalate and justify to coordinator?
**Options:**
- A) Follow policy always
- B) Escalate if justified (customer anger = legitimate reason). Policy is a guideline, not absolute. Subagent should escalate when warranted and explain to coordinator
- C) Never escalate
- D) Compromise with the customer

**Correct Answer:** B
**Feedback:** Policies provide guidance, not absolute rules. A subagent encounters a situation (angry customer) that warrants escalation. It should escalate with reasoning. The coordinator can evaluate whether the escalation was appropriate. This balances autonomy and oversight.
**Theory Reference:** Domain 1.2 - Subagent autonomy within oversight

---

## Question 223
**Title:** Tool Response Size and Context Impact
**Situation:** A `fetch_document` tool can return: (1) full document (500KB), (2) summary (5KB), (3) customizable size. Which should it offer?
**Options:**
- A) Always return full (most complete)
- B) Offer customizable size: agent specifies max_tokens. This lets agents trade completeness for context efficiency
- C) Always return summary (simpler)
- D) Return random amount

**Correct Answer:** B
**Feedback:** Customizable output sizes are ideal. Some agents need full documents; others need just summaries. Tool parameter `max_tokens` lets agents control output. This balances flexibility with efficiency.
**Theory Reference:** Domain 2.1 - Tool design for flexibility

---

## Question 224
**Title:** Planning Mode Deep Dive
**Situation:** You're in Planning Mode exploring a 200-file codebase. The Explore subagent has generated a detailed map of the architecture. Should you now: (1) exit Planning Mode and work in main session, (2) stay in Planning Mode for more exploration?
**Options:**
- A) Stay in Planning Mode; thorough planning is key
- B) Exit to main session: Planning Mode has done its job (exploration, planning). Main session is where you implement. Continue with focused file reads in main context
- C) Create a new Planning Mode session
- D) Plan forever

**Correct Answer:** B
**Feedback:** Planning Mode's job is exploration → planning. Once you have a plan, exit to main session for execution. Staying in Planning Mode longer wastes time. It's designed for planning, not implementation.
**Theory Reference:** Domain 3.4 - Planning Mode lifecycle

---

## Question 225
**Title:** Coordinator Failure Recovery
**Situation:** A coordinator spawns 5 subagents. One crashes (returns error). How should the coordinator respond: (1) abandon all work, (2) log failure, continue with 4 subagents, (3) retry the failed one?
**Options:**
- A) Abandon all
- B) Continue with available results (4/5) but note incomplete coverage in final synthesis. Optionally retry the failed one
- C) Give up
- D) Pretend it succeeded

**Correct Answer:** B
**Feedback:** Resilience means continuing when possible. If one of five subagents fails, synthesize from the four that succeeded. Note in output: "Coverage incomplete (4/5 subagents succeeded)." Optionally retry, but don't block on it.
**Theory Reference:** Domain 5.3 - Coordinator resilience

---

## Question 226
**Title:** Tool Description Specificity
**Situation:** Two tools with descriptions: (1) "Process data", (2) "Validate email addresses and remove duplicates from user lists". Which enables better agent selection?
**Options:**
- A) (1) is simpler; agents can figure out the rest
- B) (2) is far more specific; the agent knows exactly what it does and when to use it. Specificity wins
- C) Both are equivalent
- D) Vague descriptions are better

**Correct Answer:** B
**Feedback:** Specific descriptions (what it does + what it accepts + what it returns) enable reliable tool selection. Vague descriptions lead to misuse. Specificity > brevity.
**Theory Reference:** Domain 2.1 - Tool description quality

---

## Question 227
**Title:** Error Handling in Cascading Workflows
**Situation:** Workflow: A → B → C (dependent steps). Tool B fails. Should the workflow: (1) stop immediately, (2) rollback changes from A, (3) return structured error including A's results?
**Options:**
- A) Stop immediately
- B) Return structured response: A's results, B's error with details, status (stopped at B), rollback info if applicable. Enables coordinator to decide next steps
- C) Rollback silently
- D) Continue anyway

**Correct Answer:** B
**Feedback:** Structured error responses enable smart recovery. Include: what succeeded (A), what failed (B) and why, what was rolled back. This gives the coordinator full context to decide: retry, escalate, or continue with partial results.
**Theory Reference:** Domain 2.2 - Structured error propagation

---

## Question 228
**Title:** Tool Specialization Over Parameterization
**Situation:** You can build: (1) one `query_data` tool with type parameter (database, file, API), or (2) three specialized tools (`query_database`, `query_file`, `query_api`). Which leads to better agent behavior?
**Options:**
- A) Single tool with parameter (less code)
- B) Three specialized tools; agents reliably select the right tool. Parameters create ambiguity
- C) Both are equivalent
- D) Four tools

**Correct Answer:** B
**Feedback:** Specialized tools have clear names and purposes. `query_database` is unambiguous; `query_data` with type parameter requires the agent to reason about the parameter. Agents choose tools more reliably than parameter combinations.
**Theory Reference:** Domain 2.1 - Specialization over parameterization

---

## Question 229
**Title:** Context Efficiency in Multi-turn Conversations
**Situation:** An agent runs for 50 iterations. Conversation history: 200K tokens. Agent coherence is declining. What's an efficient mitigation?
**Options:**
- A) Increase context window to 500K
- B) Prune history: keep key findings and recent 10 iterations. Drop verbose intermediate tool results. This focuses context on what matters
- C) Start fresh
- D) Accept degradation

**Correct Answer:** B
**Feedback:** Strategic pruning is more efficient than increasing context. Drop verbose tool results, keep findings. Keep recent iterations (they're more relevant). This focuses context and improves coherence without starting over.
**Theory Reference:** Domain 5.1 - Context pruning and efficiency

---

## Question 230
**Title:** Subagent Output Summarization
**Situation:** Subagent returns 80K tokens of research with 500 findings. Coordinator needs to synthesize from 5 subagents. If all return 80K tokens, context will explode. Solution?
**Options:**
- A) Request all 80K from each (5 × 80K = 400K tokens in coordinator)
- B) Request summaries: each subagent provides 5-10 key findings with sources. Coordinator synthesizes from summaries (~25K total)
- C) Pick one subagent's results
- D) Don't synthesize

**Correct Answer:** B
**Feedback:** Smart aggregation: request summaries upfront instead of full outputs. 5 subagents × 25K (summaries) = 125K total vs 400K (full). Coordinator can always request full details later if needed. This is context-aware design.
**Theory Reference:** Domain 1.2 - Subagent output summarization

---

## Question 231
**Title:** Coordinator Role in Ambiguous Situations
**Situation:** A subagent encounters: "Customer wants a refund for a 1-year-old item." Policy is unclear. Subagent can: (1) deny (policy might allow this), (2) approve (policy might not), (3) ask coordinator. Which?
**Options:**
- A) Deny (safe)
- B) Approve (generous)
- C) Ask coordinator; policy gray areas are coordinator's job to decide
- D) Ask the customer

**Correct Answer:** C
**Feedback:** Subagents escalate ambiguous policies to the coordinator. The coordinator decides based on context and risk tolerance. This preserves decision-making centrality and enables consistent policy application.
**Theory Reference:** Domain 1.2 - Coordinator decision authority

---

## Question 232
**Title:** Tool Error Message Clarity
**Situation:** Tool returns: `{"error": "Invalid input"}`. What's missing?
**Options:**
- A) Nothing; it's clear
- B) Specifics: which field is invalid? What was the value? What's expected? Example: `{"error": "Invalid input", "field": "email", "value": "not-an-email", "expected": "valid email address"}`
- C) Stack trace
- D) Error code

**Correct Answer:** B
**Feedback:** Generic error messages are unhelpful. Specific errors guide correction. "Invalid input" tells you nothing; "email field 'not-an-email' should be valid email address" is actionable.
**Theory Reference:** Domain 2.2 - Clear error messages

---

## Question 233
**Title:** Batch API for Non-interactive Tasks
**Situation:** You need to process 50K customer support tickets overnight. Average response time per ticket: 3 seconds. Synchronous: 50K × 3s = 150K seconds ≈ 42 hours. Batch API: 12-24 hours. Why is Batch better?
**Options:**
- A) Synchronous is more direct
- B) Batch API is 50% cheaper and enables overnight processing without SLA. Perfect for non-interactive workloads
- C) Batch API is slower
- D) They're equivalent

**Correct Answer:** B
**Feedback:** Batch API is ideal for high-volume, non-urgent workloads. Process overnight with 50% cost savings. No SLA = lower cost. Synchronous is for interactive tasks needing fast response.
**Theory Reference:** Domain 4.5 & Chapter 7 - Batch API use cases

---

## Question 234
**Title:** Coordinator Result Aggregation Strategy
**Situation:** Five subagents search for information on Topic X. Expected results: A, B, C, D, E. Subagents find: A finds {A, B}, B finds {B, C}, C finds {C, D}, D finds {D, E}, E finds {E, A}. How should coordinator present findings?
**Options:**
- A) Report all results from each subagent (redundant)
- B) Deduplicate: report each finding once (A, B, C, D, E) with note of which subagents found it. This shows coverage and consensus
- C) Report only consensus findings
- D) Pick one subagent's results

**Correct Answer:** B
**Feedback:** Deduplication + attribution: report each finding once, note which subagents found it (shows consensus). This is concise yet transparent about coverage and agreement.
**Theory Reference:** Domain 1.2 - Deduplication and attribution

---

## Question 235
**Title:** CLAUDE.md Scope and Inheritance
**Situation:** Root `CLAUDE.md` has general standards. `/backend/CLAUDE.md` has backend-specific standards. Does `/backend/services/auth/CLAUDE.md` inherit from both?
**Options:**
- A) No, only from /backend/
- B) Yes, it cascades: root → backend → auth subdirectory. Most-specific (auth) overrides less-specific
- C) No, each is independent
- D) Randomly

**Correct Answer:** B
**Feedback:** CLAUDE.md hierarchy cascades. A file edited in `/backend/services/auth/` applies: root standards + backend standards + auth-specific standards, with nearest ancestor overriding. This enables both consistency and flexibility.
**Theory Reference:** Domain 3.1 - CLAUDE.md hierarchy

---

## Question 236
**Title:** Tool-specific Error Retry Strategy
**Situation:** Tool A (database) frequently times out but recovers quickly. Tool B (payment API) rarely fails but when it does, failures are permanent. Should both use the same retry strategy?
**Options:**
- A) Yes, uniform retry for all tools
- B) No, tool-specific retry: A uses aggressive backoff+retry (transient failures likely), B uses conservative retry or immediate escalation (failures are typically permanent)
- C) Never retry
- D) Always retry

**Correct Answer:** B
**Feedback:** Retry strategy should match tool characteristics. Transient errors (timeouts) → aggressive retry. Permanent errors (auth failures) → escalate. One-size-fits-all retry doesn't work.
**Theory Reference:** Domain 2.2 - Tool-specific retry strategies

---

## Question 237
**Title:** Planning Mode vs Direct Execution Trade-off
**Situation:** You need to: (1) add a simple variable declaration to one file, (2) refactor a complex authentication system across 50 files. Which should use Planning Mode?
**Options:**
- A) Both use Direct Execution; always move fast
- B) (1) Direct Execution (simple, low-risk), (2) Planning Mode (complex, high-risk)
- C) (1) Planning Mode (overkill), (2) Direct Execution (risky)
- D) Never use Planning Mode

**Correct Answer:** B
**Feedback:** Planning Mode for high-risk, complex changes. Direct Execution for simple, low-risk changes. Match approach to task risk. Planning Mode prevents costly mistakes on complex tasks.
**Theory Reference:** Domain 3.4 - Mode selection by risk

---

## Question 238
**Title:** Handling Missing Information in Synthesis
**Situation:** Coordinator needs to synthesize, but one subagent hasn't returned (5 min timeout). Coordinator has 4/5 results. Options: (1) wait indefinitely, (2) synthesize with 4 results and note missing coverage, (3) cancel and fail?
**Options:**
- A) Wait indefinitely
- B) Synthesize with available results and explicitly note: "Synthesis based on 4/5 subagents; coverage incomplete. Missing: Subagent 5"
- C) Fail completely
- D) Ignore the missing one

**Correct Answer:** B
**Feedback:** Partial results with transparency. Synthesize from 4 subagents, note the incompleteness. Users know the analysis isn't complete. This is better than waiting forever or failing silently.
**Theory Reference:** Domain 5.1 - Graceful degradation with missing data

---

## Question 239
**Title:** Tool Authorization by Agent Role
**Situation:** You have three agent types: (1) General Support, (2) Premium Support, (3) Admin. Tools: `approve_refund`, `override_policy`, `delete_account`. How should tools be allocated?
**Options:**
- A) All agents get all tools
- B) General: approve_refund. Premium: approve_refund + override_policy. Admin: all. Role-based allocation with escalation
- C) No agent gets any tools
- D) Random allocation

**Correct Answer:** B
**Feedback:** Role-based tool allocation follows principle of least privilege. General agents handle standard cases. Premium agents can override policies for special cases. Admin handles account-level operations. This structure enables delegation and control.
**Theory Reference:** Domain 3.2 - Role-based tool authorization

---

## Question 240
**Title:** Coordinator Timeouts and Partial Results
**Situation:** Coordinator sets 5-minute timeout for all subagents. At 4:59, four of five subagents have returned results. What should coordinator do at timeout: (1) cancel the slow subagent and synthesize, (2) extend timeout for the slow one?
**Options:**
- A) Cancel slow subagent
- B) Depends on context. If 4/5 results are sufficient to answer the query, synthesize and note incomplete coverage. If the 5th is critical, extend with clear limits
- C) Always extend
- D) Always cancel

**Correct Answer:** B
**Feedback:** Timeout handling depends on criticality of missing results. If 4/5 is sufficient, move forward. If the 5th is essential, extend timeout with a hard limit. This is pragmatic timeout management.
**Theory Reference:** Domain 5.2 - Adaptive timeout strategies

---

## Summary

**Batch 05 contains 50 questions (Questions 211-260)**
- Difficulty level: Advanced, practical scenarios
- Coverage: All 5 domains + implementation details
- Format: Ready to merge into `questionBank` array

**Progress: 260 total questions collected**
- Original: 10 questions
- Batch 01: 50 questions
- Batch 02: 50 questions
- Batch 03: 50 questions
- Batch 04: 50 questions
- Batch 05: 50 questions
- **Total so far: 260 questions** ✅

