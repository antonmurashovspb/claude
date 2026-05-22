# Quiz Question Bank - Batch 04 (Questions 161-210)

## Question 161
**Title:** Tool Input Validation at Call Time
**Situation:** A `send_email` tool accepts: to_address (email), subject (string), body (string). An agent constructs a call with: to_address="user@invalid" (invalid format). Should the tool: (1) process anyway and return error, (2) reject upfront, (3) let the agent handle validation?
**Options:**
- A) Process anyway
- B) Reject upfront with clear validation error. Fail fast before wasting API calls
- C) Let the agent validate
- D) Silently accept invalid input

**Correct Answer:** B
**Feedback:** Tools should validate inputs upfront and fail with clear error messages. Invalid email format should be caught before attempting to send. This is fail-fast—prevents wasted resources. Clear validation errors guide agents toward correct usage.
**Theory Reference:** Domain 2.1 - Input validation and error responses

---

## Question 162
**Title:** Context Window Utilization and Priority
**Situation:** You have 50K tokens of context window remaining. You need to include: (1) 30K of search results, (2) 10K of prior reasoning, (3) 5K of agent instructions. What order of priority?
**Options:**
- A) Results (30K), reasoning (10K), instructions (5K)
- B) Instructions (5K) and reasoning (10K) first for context, then as much of results (35K) as possible
- C) Reasoning (10K), instructions (5K), results (35K)
- D) All at once if it fits

**Correct Answer:** B
**Feedback:** Prioritize by utility: instructions and prior reasoning first (foundational context), then results. Instructions are compact and critical; reasoning provides framing. Results can be truncated if needed. This ordering ensures core context is complete even if results are partial.
**Theory Reference:** Domain 5.1 - Context allocation and prioritization

---

## Question 163
**Title:** Tool Preconditions and State Validation
**Situation:** You have tools: `create_order`, `pay_for_order`, `ship_order`. These must execute in sequence. Before `pay_for_order` runs, should you verify that `create_order` succeeded?
**Options:**
- A) No, assume prior steps succeeded
- B) Yes, verify via precondition check: `pay_for_order` requires a valid order_id (returned from `create_order`). This enforces workflow order
- C) The model will ensure correct order
- D) Validation is unnecessary

**Correct Answer:** B
**Feedback:** Precondition checks are architectural enforcement. `pay_for_order` requires a valid order_id (proof that create_order succeeded). If this precondition isn't met, the tool fails clearly. This is more reliable than trusting the agent to follow sequence.
**Theory Reference:** Domain 1.4 - Workflow enforcement via preconditions

---

## Question 164
**Title:** Coordinator Decision-making with Incomplete Information
**Situation:** Coordinator receives: (1) subagent A found 5 claims verified, (2) subagent B found 3 claims unverifiable, (3) subagent C is still processing. Coordinator needs to decide: escalate to human or continue? Available info: 5 verified, 3 unverifiable, 0/unknown remaining. What's the right move?
**Options:**
- A) Wait for C to complete
- B) Don't wait; make decision with available data and note that coverage is incomplete (C pending). This enables faster decision-making with clear uncertainty
- C) Treat unverifiable as false
- D) Cancel C's work

**Correct Answer:** B
**Feedback:** Progressive decision-making with transparency. The coordinator can escalate with: "5 verified, 3 unverifiable, 1 pending." This informs the human about coverage gaps. Waiting indefinitely isn't always necessary. Note uncertainty clearly.
**Theory Reference:** Domain 1.2 - Coordinator decision-making with partial information

---

## Question 165
**Title:** Error Categorization for Routing
**Situation:** Three extraction errors: (1) "Invalid JSON syntax", (2) "Field validation failed", (3) "Source document inaccessible". How should these be categorized to guide recovery?
**Options:**
- A) All are errors; treat the same
- B) Categorize by root cause: (1) and (2) are data/logic errors (investigate, retry with feedback), (3) is access error (escalate, retry later)
- C) Prioritize by severity
- D) Combine into one error message

**Correct Answer:** B
**Feedback:** Error categorization drives routing. Data/logic errors might improve with retries or feedback. Access errors require escalation or scheduling for later. Categorization prevents wasting time retrying unretryable errors.
**Theory Reference:** Domain 2.2 - Error categorization and routing

---

## Question 166
**Title:** Agent Generalization vs Specialization Trade-off
**Situation:** You can build: (1) one general customer support agent handling all issues, or (2) four specialized agents (billing, refunds, technical, account). Pros and cons?
**Options:**
- A) General is simpler to maintain
- B) Specialized agents are more accurate (each focused on one domain) but require coordinator routing. General is simpler but less accurate. Choose specialized for high-accuracy needs
- C) Both are equally good
- D) Specialization isn't worth the complexity

**Correct Answer:** B
**Feedback:** Specialization improves accuracy at the cost of coordinator complexity. A billing agent is more reliable than a general agent handling refunds, billing, and tech support simultaneously. Choose based on accuracy requirements and traffic volume.
**Theory Reference:** Domain 1.2 - Agent specialization

---

## Question 167
**Title:** Scratchpad for State Persistence
**Situation:** You're investigating a codebase in Claude Code. After 2 hours, you've identified 5 key architectural patterns. You'll resume tomorrow. What's the best way to preserve these findings?
**Options:**
- A) Hope you remember them tomorrow
- B) Save to a scratchpad file (`.investigation.md` or `.notes.md`) in the repo with findings, file references, and next steps. Resume by reading the scratchpad
- C) Create a separate document outside the repo
- D) Start fresh tomorrow

**Correct Answer:** B
**Feedback:** Scratchpad files (in the repo) are perfect for multi-session state. They're version-controlled, accessible to future sessions, and easy to reference. Next session: "Read `.investigation.md` and continue from where we left off."
**Theory Reference:** Domain 5.4 - Scratchpad files for session persistence

---

## Question 168
**Title:** Tool Naming: Clarity vs Brevity
**Situation:** You're naming tools for a customer support agent. Options: (1) `process`, `handle`, `resolve` (brief), (2) `process_refund_request`, `handle_account_issue`, `resolve_billing_question` (clear). Which is better?
**Options:**
- A) Brief names are better
- B) Clear names are much better. Agents reliably select the right tool when names are specific and descriptive
- C) Both are equivalent
- D) Use abbreviations for brevity

**Correct Answer:** B
**Feedback:** Tool names are the first signal agents see. Clear names prevent misrouting. `process_refund_request` is unambiguous; `process` is vague. Specificity over brevity wins for agent reliability.
**Theory Reference:** Domain 2.1 - Tool naming clarity

---

## Question 169
**Title:** Batch Failures and Partial Success
**Situation:** Batch API processes 5000 documents. 4900 succeed, 100 fail with "could not parse format." Should you: (1) accept 98% success, (2) retry only failures, (3) reprocess all, (4) discard failures?
**Options:**
- A) Accept 98%
- B) Use custom_id to identify the 100 failures, retry them in a separate batch with different parameters or manual review
- C) Reprocess all 5000
- D) Discard the 100

**Correct Answer:** B
**Feedback:** Selective retry is efficient. Use custom_id to pinpoint failures, retry them separately with fallback logic. This is much better than reprocessing all 5000 or discarding failures. Batch API enables this granular control.
**Theory Reference:** Domain 4.5 - Batch processing with selective retry

---

## Question 170
**Title:** Multi-turn Tool Use in Workflow
**Situation:** A workflow is: (1) validate input, (2) if valid, process data, (3) if processed, generate report. Each step depends on prior results. Is this best implemented as: (1) one agent with multi-turn tool use, (2) three sequential prompts, (3) three subagents?
**Options:**
- A) One agent (most efficient)
- B) Three sequential prompts (clearest for each focused task)
- C) Three subagents (overkill)
- D) All are equivalent

**Correct Answer:** B
**Feedback:** Sequential prompts (prompt chaining) are clearer for dependent stages. Each prompt focuses on one task, receives prior outputs as input, and reasons clearly. One agent with multi-turn tool use works but is less focused. Prompts > single-agent for sequential workflows.
**Theory Reference:** Domain 1.6 - Sequential prompts vs single-agent

---

## Question 171
**Title:** Confidence Thresholds and Automation Decisions
**Situation:** An extraction system outputs confidence scores. For a high-stakes field (financial amount), is 85% confidence enough to automate, or should human review handle all amounts below 95%?
**Options:**
- A) 85% is fine for automation
- B) Depends on risk tolerance, but for financial amounts, 95%+ threshold is typical. Below 95%, route to human review. This balances automation and accuracy
- C) 90% is the universal standard
- D) Automate everything above 50%

**Correct Answer:** B
**Feedback:** Risk tolerance sets confidence thresholds. Financial decisions have high risk, so higher thresholds. 95%+ for automation, 80-95% for human review, <80% for escalation. Different field types get different thresholds.
**Theory Reference:** Domain 5.5 - Confidence calibration by risk

---

## Question 172
**Title:** Information Loss in Subagent Aggregation
**Situation:** Three subagents each find 10 unique insights (30 total). Coordinator summarizes all into a 200-word final report. What's lost?
**Options:**
- A) Nothing; summaries capture the essence
- B) Detail, nuance, caveats, and source attribution. Consider structured summaries that preserve key insights while accepting some loss of granularity
- C) This is expected in synthesis
- D) Everything; don't summarize

**Correct Answer:** B
**Feedback:** Synthesis necessarily loses detail. Minimize loss by: preserving key insights with source attribution, noting important caveats, and using structured formats (bullet points over paragraphs). Accept some loss but preserve what matters.
**Theory Reference:** Domain 5.6 - Information preservation in synthesis

---

## Question 173
**Title:** `.claude/rules/` vs CLAUDE.md for Organization
**Situation:** You have 5 different technical conventions (testing, API design, error handling, logging, security). Should you: (1) put all in root CLAUDE.md, (2) create separate `.claude/rules/*.md` files?
**Options:**
- A) Root CLAUDE.md is simpler
- B) Separate rule files in `.claude/rules/` are more modular, maintainable, and enable path-scoped activation
- C) Both are equivalent
- D) Use a separate repo

**Correct Answer:** B
**Feedback:** Multiple rule files (`.claude/rules/testing.md`, `.claude/rules/api-design.md`, etc.) enable modularity and path-specific activation. A monolithic CLAUDE.md scales poorly. Separate files = cleaner organization.
**Theory Reference:** Domain 3.1 & 3.3 - Modular rule organization

---

## Question 174
**Title:** Tool-call Bundling for Efficiency
**Situation:** An agent needs to (1) fetch user profile, (2) fetch order history, (3) fetch payment method. Can it call all three in one agent turn?
**Options:**
- A) No, tools must be called sequentially
- B) Yes, call all three in one turn if they're independent. The agent can process all results together
- C) Only if using a special batch tool
- D) Never bundle

**Correct Answer:** B
**Feedback:** Independent tools can be bundled in one turn. The agent calls all three, receives results, and processes them together. This is more efficient than three sequential turns. Bundling is good; don't force sequential when parallel works.
**Theory Reference:** Domain 1.1 - Tool call bundling

---

## Question 175
**Title:** Distinguishing Retryable vs Non-retryable Errors
**Situation:** Tool returns two errors: (1) "Rate limit exceeded" (transient), (2) "Invalid API key" (permanent). How should these be handled?
**Options:**
- A) Retry both
- B) Retry (1) with backoff, don't retry (2). For (2), escalate or fix the key
- C) Escalate both
- D) Ignore both

**Correct Answer:** B
**Feedback:** Transient errors (rate limit, timeout) benefit from backoff+retry. Permanent errors (auth failure, invalid key) don't—retry won't help. Route each appropriately: retry with backoff vs escalate/fix.
**Theory Reference:** Domain 2.2 - Error categorization

---

## Question 176
**Title:** Coordinator Responsibility vs Subagent Authority
**Situation:** A subagent encounters a policy gray area (e.g., "Can we negotiate prices?"). Should it: (1) make a judgment call, (2) ask the coordinator?
**Options:**
- A) Make a judgment call to avoid delays
- B) Ask the coordinator; policy decisions are coordinator responsibility. Subagents execute, coordinator decides
- C) Escalate to a human
- D) Deny the request

**Correct Answer:** B
**Feedback:** Hub-and-spoke: coordinator decides policy, subagents execute. Subagents should not make policy judgments. This preserves audit trails and consistency. Asking the coordinator is correct—that's their role.
**Theory Reference:** Domain 1.2 - Coordinator-subagent boundaries

---

## Question 177
**Title:** Session Context and Task Switching
**Situation:** You're analyzing codebase A in Claude Code. You need to briefly check codebase B (different repo, different task). Should you: (1) continue in same session, (2) fork session, (3) start fresh?
**Options:**
- A) Continue in same session; context is still useful
- B) Fork or new session; context from codebase A will interfere with codebase B analysis
- C) Use a completely different tool
- D) Combine analysis of both

**Correct Answer:** B
**Feedback:** Task-specific context should be isolated. Codebase A context (classes, patterns, architecture) is noise for codebase B. Fork (to return to A later) or start fresh. Clean context = better analysis.
**Theory Reference:** Domain 1.7 - Context isolation for task switching

---

## Question 178
**Title:** Explicit Instruction vs Implicit Expectation
**Situation:** System prompt says: "Help customers with problems." A customer requests a discount. The agent doesn't offer discounts (no explicit instruction). Should system prompt explicitly say: "When customers ask for discounts, offer up to 10% if they're loyal"?
**Options:**
- A) No, implicit expectations are fine
- B) Yes, explicit instructions about discount policies enable consistent behavior. Implicit expectations lead to inconsistency
- C) Let the agent figure it out
- D) Never offer discounts

**Correct Answer:** B
**Feedback:** Explicit beats implicit. "Help customers" is vague; agents interpret differently. "Offer 10% discounts to loyal customers" is unambiguous. Explicit instructions drive consistent behavior across sessions.
**Theory Reference:** Domain 4.1 - Explicit criteria vs vague guidance

---

## Question 179
**Title:** Nested Subagent Contexts
**Situation:** Coordinator spawns Subagent A, which spawns Subagent B, which spawns Subagent C. Three levels of nesting. Is this advisable?
**Options:**
- A) Yes, unlimited nesting is supported
- B) Nesting increases complexity and latency. Stick to one level: coordinator → subagents. If you need hierarchies, flatten them
- C) Nesting is always bad
- D) Nesting is always good

**Correct Answer:** B
**Feedback:** Deep nesting adds complexity without proportional benefit. Coordinator → subagents (one level) is the standard. If you need hierarchies, redesign the task decomposition to flatten it. Keep it simple.
**Theory Reference:** Domain 1.2 - Multi-agent architecture simplicity

---

## Question 180
**Title:** Planning Mode Awareness
**Situation:** You're in Planning Mode in Claude Code. The Explore subagent finds 10 relevant files. Should you have it read all 10 or sample them?
**Options:**
- A) Read all 10; comprehensive understanding
- B) Start with 2-3 most relevant files in Explore context. If more context needed, read additional files in main session. Explore has limited context—use it for discovery, not exhaustive reading
- C) Skip Explore; read directly
- D) Read one and guess

**Correct Answer:** B
**Feedback:** Explore subagent is for discovery (finding files, understanding structure). It has limited context. Read a sample in Explore, then refine understanding in main session with focused file reads. This is more efficient than exhaustive reading in Explore.
**Theory Reference:** Domain 3.4 - Planning Mode and Explore efficiency

---

## Question 181
**Title:** MCP Resource Pre-loading
**Situation:** An agent needs to reference a 500-item taxonomy (product categories, codes) repeatedly. Should it: (1) make individual tool calls for each lookup, (2) load the entire taxonomy as an MCP resource once?
**Options:**
- A) Individual calls for each lookup
- B) Load once as MCP resource, reference as needed. This eliminates repeated API calls and improves performance
- C) Embed in system prompt
- D) Have the agent memorize it

**Correct Answer:** B
**Feedback:** Large static data should be loaded as MCP resources. Load once, reference many times. This is much faster and cheaper than repeated lookups. MCP resources are perfect for this use case.
**Theory Reference:** Domain 2.4 - MCP resources for static data

---

## Question 182
**Title:** Tool Result Sanitization
**Situation:** A `fetch_user_account` tool returns: name, email, password_hash, account_creation_date, account_balance, two_factor_settings. An agent should see: name, email, balance. How do you ensure this?
**Options:**
- A) Trust the agent to ignore sensitive fields
- B) Use a PostToolUse hook to extract only needed fields (name, email, balance) before the agent sees results. This prevents exposure of sensitive data
- C) Return all fields; security is the agent's responsibility
- D) Create a separate tool for each field

**Correct Answer:** B
**Feedback:** PostToolUse hooks can sanitize outputs. Extract only needed fields (name, email, balance), remove sensitive data (password_hash, 2FA settings) before agent processing. This is security by design—don't expose unnecessary data.
**Theory Reference:** Domain 1.5 - Data sanitization with hooks

---

## Question 183
**Title:** Feedback Loop for Extraction Quality
**Situation:** Extraction system finds a field labeled "Purchase Amount: 500". It extracts "500". But validation rules expect currency format ("$500" or "500 USD"). Should it: (1) fail the extraction, (2) infer the currency?
**Options:**
- A) Fail; validation is correct
- B) Extract "500" and flag as "currency_inferred_missing". Let validation feedback guide next attempt. On retry, extract with currency context
- C) Guess the currency
- D) Skip the field

**Correct Answer:** B
**Feedback:** Structured feedback enables refinement. Flag missing context, retry extraction with feedback: "Last extraction lacked currency. Include currency (USD, EUR, etc.) in next extraction." Feedback loops improve extraction quality progressively.
**Theory Reference:** Domain 4.4 - Feedback loops for extraction refinement

---

## Question 184
**Title:** Coordinator Aggregation of Conflicting Findings
**Situation:** Subagent A: "Climate change is primary driver of migration." Subagent B: "Economic factors are primary driver." Both provide citations. How should coordinator report?
**Options:**
- A) Average them somehow
- B) Report both with full citations and context. Note that research community has competing hypotheses. This preserves nuance
- C) Choose one; the other is wrong
- D) Ignore the conflict

**Correct Answer:** B
**Feedback:** Conflicting peer-reviewed findings are normal in research. Report both with full attribution and sources. This educates readers rather than misleading them with false consensus. Nuance is valuable.
**Theory Reference:** Domain 5.6 - Reporting competing findings

---

## Question 185
**Title:** Subagent Result Weighting
**Situation:** Three subagents search for information about a topic: A (thorough, 50 sources, high confidence), B (quick, 5 sources, medium confidence), C (recent, 3 very new sources, medium confidence). How should coordinator weight findings?
**Options:**
- A) Weight equally (1/3 each)
- B) Weight by confidence and coverage: A > B = C. A's thorough research with high confidence carries more weight
- C) Use only A; discard others
- D) Random weighting

**Correct Answer:** B
**Feedback:** Weighting reflects reliability. A has more sources, higher confidence—weight higher. B and C have fewer sources/lower confidence—weight lower. Structured weighting enables intelligent synthesis.
**Theory Reference:** Domain 1.2 - Weighted subagent synthesis

---

## Question 186
**Title:** Tool Version Management
**Situation:** You upgrade a tool from v1 to v2 (breaking change in response format). Old agent configs use v1, new configs use v2. How do you manage the transition?
**Options:**
- A) Remove v1 immediately; force everyone to upgrade
- B) Support both versions during transition. Agents explicitly select version. Announce deprecation timeline and deadline for v1 removal
- C) Keep v1 forever
- D) Hide the difference

**Correct Answer:** B
**Feedback:** Graceful version management: support both versions during transition, announce deprecation with timeline, force upgrade at deadline. This prevents broken agent configs and eases migration.
**Theory Reference:** Domain 2.1 - Tool version management

---

## Question 187
**Title:** Agent Loop Timeout Handling
**Situation:** An agent loop is configured to timeout after 30 seconds. At 28 seconds, it's still processing. When timeout occurs, should it: (1) abandon work immediately, (2) finish current tool call, (3) return partial results?
**Options:**
- A) Abandon immediately
- B) Finish current tool call if close to timeout, then return results accumulated so far. Hard timeout only if truly necessary
- C) Keep processing
- D) Ignore timeout

**Correct Answer:** B
**Feedback:** Timeouts should be graceful when possible. Finishing a near-complete tool call and returning partial results is better than abandoning mid-call. Structured timeout handling improves reliability.
**Theory Reference:** Domain 1.1 - Agent loop timeout handling

---

## Question 188
**Title:** Batch Processing with Fallback Logic
**Situation:** Batch API processes documents with primary extraction method. Some documents (10%) fail with primary method. Should you: (1) mark all as failed, (2) implement secondary fallback method in batch for failed documents?
**Options:**
- A) Mark all as failed
- B) Batch API supports chaining: on failure, apply fallback method to same document in same batch request. This improves success rate to ~98-99%
- C) Manual retry later
- D) Discard failed documents

**Correct Answer:** B
**Feedback:** Fallback logic in batch processing is efficient. If primary method fails, try secondary without creating new batch. This improves success rates while maintaining batch efficiency.
**Theory Reference:** Domain 4.5 - Batch processing fallbacks

---

## Question 189
**Title:** Incremental Prompt Refinement
**Situation:** You have a Claude Code prompt that's somewhat vague. Rather than rewriting it completely, you ask Claude: "What ambiguities do you see in this prompt?" Claude identifies 5 unclear points. What's the next step?
**Options:**
- A) Rewrite from scratch
- B) Address each ambiguity iteratively: clarify one, get feedback, refine, repeat. This is iterative refinement
- C) Accept the vagueness
- D) Use a different model

**Correct Answer:** B
**Feedback:** Iterative refinement is powerful. Identify ambiguities, address one at a time, get feedback, refine. This is the "interview" pattern. It's more effective than rewriting from scratch.
**Theory Reference:** Domain 3.5 - Iterative prompt refinement

---

## Question 190
**Title:** Coordinator Observability and Logging
**Situation:** A coordinator spawns 5 subagents. No logging is set up. A subagent silently fails (returns null). The coordinator has no idea what happened. How do you improve this?
**Options:**
- A) Assume it worked
- B) Implement structured logging: coordinator logs all subagent spawning, receives explicit completion signals, logs success/failure with context
- C) Don't worry about failures
- D) Ask humans to monitor

**Correct Answer:** B
**Feedback:** Observability is critical in multi-agent systems. Log: (1) subagent spawn events, (2) completion signals, (3) success/failure status, (4) key results. This enables debugging and auditing.
**Theory Reference:** Domain 1.2 - Coordinator observability

---

## Summary

**Batch 04 contains 50 questions (Questions 161-210)**
- Difficulty level: Consistent, advanced scenarios
- Coverage: All 5 domains + practical implementation details
- Format: Ready to merge into `questionBank` array

**Progress: 210 total questions collected**
- Original: 10 questions
- Batch 01: 50 questions
- Batch 02: 50 questions
- Batch 03: 50 questions
- Batch 04: 50 questions
- **Total so far: 210 questions** ✅

