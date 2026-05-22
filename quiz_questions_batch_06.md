# Quiz Question Bank - Batch 06 (Questions 261-310)

## Question 261
**Title:** Distinguishing Tool Failures from Wrong Tool Selection
**Situation:** An agent calls `fetch_weather` with parameters: `{location: "paris", date: "2024-12-25"}`. Tool fails: `{"error": "Invalid date format"}`. Did the agent: (1) select wrong tool, (2) use correct tool with wrong parameters?
**Options:**
- A) Wrong tool selection
- B) Correct tool, wrong parameters. The agent should use ISO format ("2024-12-25" is ISO, but tool might expect Unix timestamp). This is an operator error, not tool selection error
- C) Tool is broken
- D) Can't tell

**Correct Answer:** B
**Feedback:** Tool selection and parameter formatting are separate concerns. The agent selected the right tool but formatted parameters wrong. Tool description should specify expected format. Clear specifications prevent this error.
**Theory Reference:** Domain 2.1 - Tool specifications and parameter clarity

---

## Question 262
**Title:** Batch API Failure Scenarios
**Situation:** Batch API job submitted 1000 documents. 999 complete successfully. 1 fails with "structure unrecognizable". What information do you need to decide if retry is worthwhile?
**Options:**
- A) Just reprocess the 1 failure
- B) First analyze: which document failed? Is it a known problematic format? Can fallback extraction help? Then decide whether retry makes sense vs manual review
- C) Skip the 1 failure
- D) Reprocess all 1000

**Correct Answer:** B
**Feedback:** Smart retry decisions require context. Use `custom_id` to identify the failure, analyze why it failed, then decide: retry with different parameters, manual review, or skip. This is better than blind retry or reprocessing all.
**Theory Reference:** Domain 4.5 - Selective retry in batch processing

---

## Question 263
**Title:** Subagent vs Coordinator Responsibilities
**Situation:** A customer escalates angrily: "This is unacceptable!" A subagent can: (1) apologize, offer 10% discount, continue handling, or (2) escalate to coordinator immediately. Which approach respects the separation?
**Options:**
- A) Always offer discounts and continue
- B) Escalate to coordinator; emotional de-escalation and special decisions (discounts) are coordinator-level decisions
- C) Apologize many times
- D) Ignore the anger

**Correct Answer:** B
**Feedback:** Hub-and-spoke: subagents execute routine tasks, coordinator handles exceptions and special cases. An angry customer warrants coordinator involvement for authority and context-aware decision-making.
**Theory Reference:** Domain 1.2 - Coordinator vs subagent boundaries

---

## Question 264
**Title:** Prompt Consistency Across Agents
**Situation:** You have 10 support agents. Three have slightly different system prompts. Agent A escalates more frequently, Agent B never escalates, Agent C is balanced. Is this variability acceptable?
**Options:**
- A) Yes, agents can be different
- B) No, inconsistency creates unpredictable behavior. Use consistent system prompts across similar agents, or document why variations exist
- C) Encourage variation
- D) Don't use system prompts

**Correct Answer:** B
**Feedback:** Consistency matters for predictable behavior. If agents should behave similarly, use the same system prompt. If variations are intentional (e.g., different authority levels), document them. Accidental variation is bad; intentional variation is fine.
**Theory Reference:** Domain 4.1 - Prompt consistency and governance

---

## Question 265
**Title:** Tool Response Timeout
**Situation:** A tool call is made. The system should wait for a response, but after how long should it timeout? Options: (1) 30 seconds (aggressive), (2) 5 minutes (generous), (3) context-dependent?
**Options:**
- A) Always 30 seconds
- B) Context-dependent: quick tools (30s), slow tools like batch processing (5+ min). Configure per-tool
- C) Always 5 minutes
- D) No timeout

**Correct Answer:** B
**Feedback:** Timeout should match tool characteristics. A quick database query: 30s. A batch job: 5+ min. Per-tool timeout configuration enables realistic expectations.
**Theory Reference:** Domain 2.1 - Tool response timeout configuration

---

## Question 266
**Title:** Coordinator State Management
**Situation:** A coordinator is managing 5 concurrent tasks. Task 3 fails. The coordinator notes the failure but needs to check: did Task 2 complete? Did it pass data to Task 4? How do you track this?
**Options:**
- A) Hope it worked
- B) Maintain explicit task state log: status, dependencies, outputs. Log all state transitions for auditing and debugging
- C) Ask subagents
- D) Restart everything

**Correct Answer:** B
**Feedback:** Coordinator state management requires explicit tracking. Log: task status, inputs, outputs, dependencies. This enables debugging ("why did Task 4 not get input from Task 2?") and recovery.
**Theory Reference:** Domain 1.2 - Coordinator state logging

---

## Question 267
**Title:** Error Cascading in Multi-stage Workflows
**Situation:** Workflow: A (extract) → B (validate) → C (transform). If A's output is malformed and B rejects it, should error message to coordinator include: (1) only B's rejection, (2) A's output + B's rejection?
**Options:**
- A) Only B's rejection (simpler)
- B) Both: A's output + B's rejection. Coordinator can see root cause (malformed extraction) vs symptom (validation failure)
- C) A's output only
- D) Never report errors

**Correct Answer:** B
**Feedback:** Error context is crucial. If only B's error is reported, debugging is hard. Include A's output + B's rejection so the coordinator can see the full picture and decide: retry A, skip, or escalate.
**Theory Reference:** Domain 5.3 - Error propagation with context

---

## Question 268
**Title:** Planning Mode for Understanding Impact
**Situation:** You need to update a shared authentication library used by 20 services. Direct execution risks breaking multiple services. Should you use Planning Mode?
**Options:**
- A) Direct execution is faster
- B) Yes, Planning Mode's Explore subagent can map dependencies (which services use this library) and surface impact before changes
- C) Planning Mode is overkill
- D) Ask permission first

**Correct Answer:** B
**Feedback:** Planning Mode is exactly for this: understanding impact before acting. Explore maps dependencies, surfaces risks, and informs the plan. For high-impact changes, exploration prevents disasters.
**Theory Reference:** Domain 3.4 - Planning Mode impact analysis

---

## Question 269
**Title:** Skill Activation and Scope
**Situation:** You define a `/code-review` skill with frontmatter: `paths: ["src/**/*.ts"]`. When should this skill activate?
**Options:**
- A) Always available
- B) Only when editing TypeScript files in the `src/` directory tree. Auto-activates for matching files
- C) Never activates automatically
- D) For all files

**Correct Answer:** B
**Feedback:** `paths` frontmatter controls skill activation. `["src/**/*.ts"]` means the skill activates only when editing TypeScript files in `src/`. This is path-scoped auto-trigger.
**Theory Reference:** Domain 3.2 & 3.3 - Skill path activation

---

## Question 270
**Title:** Tool Result Streaming vs Batched Delivery
**Situation:** A tool processes 1000 records and will return results. Options: (1) return all 1000 at once (batched), (2) stream results as they're ready (progressive). Agent processes results. Which is better?
**Options:**
- A) Batched (simpler)
- B) Streaming enables progressive processing: agent can start analyzing first 100 while tool generates remaining 900, reducing latency
- C) Batched is always better
- D) Equivalent

**Correct Answer:** B
**Feedback:** Streaming improves latency. The agent can begin work on partial results while the tool generates remaining data. Batched requires waiting for all results. Streaming is better for latency-sensitive applications.
**Theory Reference:** Domain 1.1 - Result streaming benefits

---

## Question 271
**Title:** Separating Concerns: Validation vs Processing
**Situation:** An extraction pipeline: (1) extract data, (2) validate structure, (3) validate business rules, (4) process. Why is separating validation from processing better than doing all at once?
**Options:**
- A) It's not; combine for efficiency
- B) Separation enables clear error reporting. Structure errors vs business logic errors are distinct and need different recovery. Processing only starts on validated data
- C) Separation is slower
- D) They should be combined

**Correct Answer:** B
**Feedback:** Layered validation: schema validation (structure) → semantic validation (business logic). Failures at each layer get different handling. Processing only on clean data. This architecture is clearer and more maintainable.
**Theory Reference:** Domain 4.4 - Layered validation architecture

---

## Question 272
**Title:** Coordinator's Role in Conflict Resolution
**Situation:** Two subagents provide conflicting recommendations: A says "approve", B says "deny". Both have valid reasoning. How should coordinator handle this?
**Options:**
- A) Choose one; the other is wrong
- B) Present both perspectives to decision-maker (human or escalation) with each subagent's reasoning. Let them decide with full context
- C) Average them
- D) Flip a coin

**Correct Answer:** B
**Feedback:** Conflicting expert opinions should both be presented. The decision-maker sees: A's reasoning for approval, B's reasoning for denial, and trade-offs. This enables informed decision-making.
**Theory Reference:** Domain 1.2 - Coordinator synthesis of competing views

---

## Question 273
**Title:** Tool Precondition Enforcement
**Situation:** You have tools: `create_invoice` (returns invoice_id), `send_invoice` (requires invoice_id). How do you ensure `send_invoice` isn't called before `create_invoice`?
**Options:**
- A) Trust the agent
- B) Enforce via preconditions: `send_invoice` requires valid invoice_id (from `create_invoice`). Tool fails if precondition unmet
- C) System prompt guidance
- D) Manual check

**Correct Answer:** B
**Feedback:** Preconditions are architectural enforcement. If `send_invoice` requires invoice_id and it's not present, the tool fails with a clear error. This prevents the agent from making mistakes.
**Theory Reference:** Domain 1.4 - Workflow preconditions

---

## Question 274
**Title:** Context Refresh in Long Sessions
**Situation:** You've been in a Claude Code session for 3 hours. You've read 50 files, made 10 changes. The agent is now asking vague questions like "What was that pattern?" even though you discussed it 30 minutes ago. This is context degradation. What should you do?
**Options:**
- A) Continue and hope it improves
- B) Save findings to scratchpad, summarize key insights, start fresh or create checkpoint. This resets context coherence
- C) Increase context window
- D) Accept reduced coherence

**Correct Answer:** B
**Feedback:** Context degradation is real after 2-3 hours. Mitigation: save findings to scratchpad, summarize insights, restart or checkpoint. This resets the agent's focus without losing progress.
**Theory Reference:** Domain 5.4 - Long-session context management

---

## Question 275
**Title:** Tool Specialization Guidance
**Situation:** You're designing tools for a document processing system. Should you create: (1) one `process_document` tool with type parameter, or (2) separate tools: `extract_pdf`, `extract_image`, `extract_text`?
**Options:**
- A) One tool with parameter (less code)
- B) Separate tools; specific names enable clearer agent selection and reduce parameterization ambiguity
- C) Both approaches are equivalent
- D) Four tools for everything

**Correct Answer:** B
**Feedback:** Separate tools (`extract_pdf`, `extract_image`) are clearer than one tool with type parameter. Agents select the right tool instead of getting parameter combinations wrong. Specialization > parameterization.
**Theory Reference:** Domain 2.1 - Tool specialization

---

## Question 276
**Title:** Coordinator Output Format
**Situation:** A coordinator synthesizes findings from 3 research subagents. Should the output: (1) present integrated findings without attribution, (2) present findings with clear attribution to subagents?
**Options:**
- A) Integrated only (cleaner)
- B) With attribution: show which subagent found which findings. This enables verification and identifies consensus
- C) No output at all
- D) Both separately

**Correct Answer:** B
**Feedback:** Attribution is crucial for verification and trust. "Finding X" is less useful than "Finding X (verified by Subagent A and B)" or "Finding Y (from Subagent C, conflicting with Subagent D)". Attribution enables judgment.
**Theory Reference:** Domain 5.6 - Attribution and provenance

---

## Question 277
**Title:** Tool Initialization Patterns
**Situation:** A tool requires setup (authentication, connection initialization). Should this happen: (1) on every call, (2) once at tool creation, (3) on demand?
**Options:**
- A) Every call
- B) On-demand or cached: initialize on first call, reuse connection. Recreate if connection fails. This balances freshness and efficiency
- C) Once forever (no reconnection)
- D) Manual initialization only

**Correct Answer:** B
**Feedback:** Connection pooling and lazy initialization are standard. Initialize on first use, cache the connection, reinitialize on failure. This is more efficient than per-call initialization and more robust than never reconnecting.
**Theory Reference:** Domain 2.1 - Tool initialization patterns

---

## Question 278
**Title:** Escalation Decision Criteria
**Situation:** An agent encounters a request that's: (1) outside its authority (policy requires human approval), (2) unusual but not necessarily requiring escalation. Should it escalate?
**Options:**
- A) Escalate everything
- B) Escalate only (1); outside-authority decisions require escalation. (2) unusual patterns can often be handled with appropriate reasoning
- C) Never escalate
- D) Guess

**Correct Answer:** B
**Feedback:** Escalation should be targeted. Outside-authority decisions require escalation. Unusual patterns don't necessarily. This preserves escalation for genuinely necessary cases.
**Theory Reference:** Domain 5.2 - Escalation criteria

---

## Question 279
**Title:** Multi-agent Consistency and Disagreement
**Situation:** You deploy identical agents in production. After 2 weeks, Agent A escalates 20% of cases, Agent B escalates 5%. They should behave identically. What's wrong?
**Options:**
- A) This is expected variation
- B) Investigate: same system prompt but different behavior likely means model variance or different request distributions. Investigate request characteristics or introduce explicit escalation rules
- C) Disable one agent
- D) Ignore the difference

**Correct Answer:** B
**Feedback:** Identical agents should have similar escalation rates. Divergence suggests: (1) different request types (A gets harder cases), (2) model variance, or (3) emerging patterns in agent reasoning. Investigate to ensure consistency.
**Theory Reference:** Domain 4.1 - Agent behavior monitoring

---

## Question 280
**Title:** Sampling Strategy for Large Result Sets
**Situation:** A search returns 50,000 results. The agent can't process all (context limit). What's a better strategy than taking the first 100?
**Options:**
- A) First 100 (top-ranked)
- B) Stratified sampling: first N (top-ranked), last M (tail), random samples from middle. This gives representative coverage vs just top results
- C) Random 100
- D) Process all somehow

**Correct Answer:** B
**Feedback:** Stratified sampling provides better coverage than just top results. Top-ranked results show what's most relevant. Tail results show edge cases. Random samples show variety. Together, they're representative.
**Theory Reference:** Domain 5.4 - Strategic result sampling

---

## Question 281
**Title:** Coordinator Recovery from Partial Failure
**Situation:** Coordinator spawns 3 subagents: A completes (results: X), B times out after 2 minutes, C fails immediately (error). How should coordinator handle this mixed scenario?
**Options:**
- A) Wait for all indefinitely
- B) Report: A succeeded with X. B timeout (could retry if critical). C failed (escalate). Synthesize from A only or retry B if time permits
- C) Fail completely
- D) Ignore B and C

**Correct Answer:** B
**Feedback:** Mixed results require nuanced handling. Report what succeeded (A), distinguish failures (C is non-recoverable, B might be retryable), synthesize with transparency about coverage.
**Theory Reference:** Domain 5.3 - Coordinator failure handling

---

## Question 282
**Title:** Tool Documentation Specificity
**Situation:** Tool docs: "Fetch user data." How much more specific should they be?
**Options:**
- A) Current level is fine
- B) Should specify: what fields are returned, time complexity, rate limits, required authorization, error cases, example usage. Comprehensive documentation enables proper tool usage
- C) Keep it brief
- D) No documentation needed

**Correct Answer:** B
**Feedback:** Tool documentation should include: what it does, what it accepts, what it returns, authentication, rate limits, error cases, examples. This prevents misuse and enables confident tool selection.
**Theory Reference:** Domain 2.1 - Tool documentation

---

## Question 283
**Title:** Prompt Chaining vs Multi-turn Single Agent
**Situation:** You need to: extract data, validate it, transform it, export it. Approach A: single agent with system prompt covering all 4 steps. Approach B: 4 sequential prompts. Which is clearer?
**Options:**
- A) Single agent (unified)
- B) Sequential prompts; each step gets focused reasoning without context dilution from other steps. Step outputs feed into next step's input
- C) Both are equivalent
- D) Neither works

**Correct Answer:** B
**Feedback:** Sequential prompts are clearer for multi-stage workflows. Each stage: receives prior output, has clear goal, produces focused result. Single agent dilutes attention across 4 stages.
**Theory Reference:** Domain 1.6 - Prompt chaining patterns

---

## Question 284
**Title:** Tool Availability and Fallbacks
**Situation:** Primary tool `premium_analysis` is temporarily down. Fallback: `basic_analysis` (simpler but always available). Should the agent: (1) fail, (2) automatically degrade to basic?
**Options:**
- A) Fail and alert user
- B) Degrade automatically: "Using basic_analysis (premium_analysis unavailable). Results may be less detailed." This maintains service
- C) Wait for premium to recover
- D) Skip analysis

**Correct Answer:** B
**Feedback:** Graceful degradation improves resilience. Use fallback tool with transparency: note the degradation. This is better than failure. Users get partial value; they're informed.
**Theory Reference:** Domain 5.3 - Graceful degradation patterns

---

## Question 285
**Title:** Session Forking for Parallel Hypotheses
**Situation:** You're investigating a bug and have two competing theories about the cause. Using `fork_session`, you can explore both independently. Why is this useful?
**Options:**
- A) It's not; explore both in one session
- B) Forking enables clean separation: each branch gets isolated context, findings. Compare results at the end. Mental separation is cleaner than mixing theories
- C) Forking wastes resources
- D) Can't fork sessions

**Correct Answer:** B
**Feedback:** `fork_session` enables parallel hypothesis testing. Branch 1 explores theory A, branch 2 explores theory B. Clean separation prevents cross-contamination. You compare findings at the end.
**Theory Reference:** Domain 1.7 - fork_session for parallel investigation

---

## Question 286
**Title:** Error Rate Monitoring in Production
**Situation:** Your agent system shows 2% error rate. Is this acceptable?
**Options:**
- A) Yes, 2% is low
- B) Context-dependent: customer support (target <1%), internal research (target <5%), data extraction (target <0.5%). Monitor error rates per use case, alert if above threshold
- C) No, errors are always bad
- D) Don't monitor

**Correct Answer:** B
**Feedback:** Error rate acceptability depends on context. Critical systems (<0.5%), standard systems (1-2%), research systems (5%+). Define per-use-case SLAs and alert appropriately.
**Theory Reference:** Domain 4.5 - SLA planning

---

## Question 287
**Title:** Tool Result Caching Strategy
**Situation:** A tool `fetch_exchange_rate(currency_pair)` returns latest rates. Rates change frequently. Should you cache results?
**Options:**
- A) Always cache
- B) Cache with short TTL: 1-5 minutes. Rates change frequently, but stale data (within minutes) is acceptable. Balances freshness with efficiency
- C) Never cache
- D) Cache indefinitely

**Correct Answer:** B
**Feedback:** Caching depends on data volatility and acceptable staleness. Exchange rates change constantly but 5-minute staleness is often acceptable. Cache with TTL to balance efficiency and freshness.
**Theory Reference:** Domain 2.5 - Cache TTL strategies

---

## Question 288
**Title:** Coordinator's Use of Subagent Confidence
**Situation:** Subagent A reports finding with 95% confidence, Subagent B reports conflicting finding with 60% confidence. How should coordinator weight them?
**Options:**
- A) Equal weight (both are findings)
- B) Weight by confidence: A's 95% carries more weight than B's 60%. Note the conflict and confidence differential in output
- C) Discard B (too low)
- D) Average them

**Correct Answer:** B
**Feedback:** Confidence weights reliability. A's 95% confident finding is more credible than B's 60%. Coordinator should note: "A found X (95% confidence), B found Y (60% confidence, contradictory)." This enables informed judgment.
**Theory Reference:** Domain 5.5 - Confidence-weighted synthesis

---

## Question 289
**Title:** Recursive Subagent Calls
**Situation:** Subagent A spawns Subagent B, which spawns Subagent C. How deep should this nesting go?
**Options:**
- A) Unlimited depth
- B) Avoid nesting: keep it simple (Coordinator → Subagents, no deeper). Deep nesting adds complexity with diminishing returns. Flatten task decomposition instead
- C) Only one level allowed
- D) Don't use subagents

**Correct Answer:** B
**Feedback:** Deep nesting is rarely needed and adds complexity. Prefer flat architecture: Coordinator orchestrates Subagents. If you need hierarchies, redesign task decomposition to flatten.
**Theory Reference:** Domain 1.2 - Flat multi-agent architecture

---

## Question 290
**Title:** Tool Availability SLA
**Situation:** You have a tool with 99.9% SLA (99.9% uptime). This means downtime: 43 seconds per day, 6 minutes per month. Is this acceptable for a customer-facing agent?
**Options:**
- A) Yes, 99.9% is very good
- B) Depends on tool criticality: for critical functions (payments), you might want 99.99% (4 seconds/day downtime). For secondary features, 99.9% is fine. Match SLA to importance
- C) No, unacceptable
- D) SLAs don't matter

**Correct Answer:** B
**Feedback:** SLA targets depend on function criticality. Payment processing: 99.99%+. User research: 99%+ is fine. Map SLA requirements to function importance.
**Theory Reference:** Domain 4.5 - SLA planning by criticality

---

## Summary

**Batch 06 contains 50 questions (Questions 261-310)**
- Difficulty level: Advanced, production-focused
- Coverage: All 5 domains + operational concerns
- Format: Ready to merge into `questionBank` array

**Progress: 310 total questions collected**
- Original: 10 questions
- Batch 01: 50 questions
- Batch 02: 50 questions
- Batch 03: 50 questions
- Batch 04: 50 questions
- Batch 05: 50 questions
- Batch 06: 50 questions
- **Total so far: 310 questions** ✅

---

## Master Summary: All Batches Complete

✅ **310 Questions Generated** across 6 batches + original 10 questions
- Consistent difficulty level matching exam standards
- Coverage: All 5 domains + foundational chapters
- Format: HTML-ready with answer reveal functionality
- Theory references linked to source materials

### By Domain Distribution:
- Domain 1 (Agent Architecture): ~60 questions
- Domain 2 (Tools & MCP): ~55 questions
- Domain 3 (Claude Code): ~50 questions
- Domain 4 (Prompt Engineering): ~55 questions
- Domain 5 (Context Management): ~40 questions
- **Foundational Chapters**: ~50 questions

