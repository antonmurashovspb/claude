# Quiz Question Bank - Batch 17 (Questions 171-180)

## Question 171
**Title:** Adaptive Decomposition vs Fixed Pipeline Workflows
**Situation:** Your system processes open-ended investigation tasks where early findings determine what to investigate next. Currently, you define all steps upfront in a fixed pipeline: analyze structure, identify risks, generate recommendations. A recent project needed four different investigation branches based on initial analysis—the fixed pipeline forced all branches through all steps regardless of relevance.
**Options:**
- A) Use adaptive decomposition: analyze initial findings first, then dynamically generate investigation branches based on what was discovered
- B) Use fixed pipelines; they're more reliable because all steps execute consistently
- C) Create separate fixed pipelines for each possible investigation branch, then select which to run
- D) Combine both approaches: define the analysis phase in advance, allow dynamic branching only after

**Correct Answer:** A
**Feedback:** Adaptive decomposition generates investigation branches based on actual findings rather than pre-specified paths. This is ideal for open-ended investigations where the structure is unknown upfront. B ignores that different findings warrant different investigation priorities. C requires pre-enumerating all branches (often infeasible). D unnecessarily constrains the analysis phase.
**Theory Reference:** Domain 1.6 - Task decomposition strategies

---

## Question 172
**Title:** Balancing Breadth and Depth in Multi-agent Task Decomposition
**Situation:** Your coordinator needs to research "climate solutions adoption barriers" across 15 different sectors. An engineer proposes: (1) Depth approach—spawn one research subagent per sector (15 subagents), maximizing coverage but creating coordination overhead. (2) Breadth approach—spawn 3 subagents each covering 5 sectors, reducing coordination overhead but risking shallow coverage.
**Options:**
- A) Start with breadth (3 subagents for quick coverage), then spawn focused depth subagents for high-priority sectors identified in initial research
- B) Use vector databases to manage all 15 sectors within one subagent context
- C) Use the depth approach; spawn individual subagents for each sector to ensure coverage
- D) Use the breadth approach; fewer subagents reduce coordination overhead

**Correct Answer:** A
**Feedback:** Hybrid approach enables efficient initial coverage (breadth) while preserving the ability to deepen investigation into sectors that warrant it (depth). B requires infrastructure investment without solving the decomposition problem. C creates unnecessary coordination overhead. D risks missing important sector-specific details.
**Theory Reference:** Domain 1.2 - Coordinator result aggregation and decomposition

---

## Question 173
**Title:** Hook Placement for Request Transformation vs Response Validation
**Situation:** Your system has two requirements: (1) normalize currency values from tools (EUR to USD based on exchange rates—tools return different formats), and (2) validate that discount codes exist in your database before proceeding. Where should these hooks be placed?
**Options:**
- A) Both PreToolUse; they both occur before tool execution
- B) Both PostToolUse; they both process data related to tool calls
- C) Validation uses PreToolUse (before sending discount codes to tools), normalization uses PostToolUse (after receiving results)
- D) Normalization uses PreToolUse, validation uses PostToolUse

**Correct Answer:** C
**Feedback:** PreToolUse validates discount codes before they're sent to tools (prevents invalid requests). PostToolUse normalizes currency responses before the agent processes them (standardizes format). A confuses the data direction. B applies wrong hook to validation. D reverses the data flow direction.
**Theory Reference:** Domain 1.5 - Hook placement for data normalization

---

## Question 174
**Title:** Subagent Context Isolation and Information Passing
**Situation:** Your coordinator has analyzed a customer's purchase history (200 lines of transaction data). It needs a subagent to identify spending patterns and recommend products. The subagent does not automatically inherit the coordinator's context. What is the most appropriate way to pass this information?
**Options:**
- A) Store history in a shared database; give the subagent a query to retrieve only summaries
- B) Include the full 200 lines of transaction data directly in the subagent prompt
- C) Summarize the data to 10-15 key insights and include that in the subagent prompt
- D) Have the subagent query a logging API to retrieve transaction history during execution

**Correct Answer:** B
**Feedback:** The coordinator should pass raw transaction data directly—the subagent can analyze nuance and patterns better than pre-summarized insights. 200 lines of structured data is well within context limits. A adds infrastructure and loses detail. C loses information through summarization. D creates runtime dependencies the subagent must manage.
**Theory Reference:** Domain 1.3 - Context passing to subagents

---

## Question 175
**Title:** Tool Specialization Impact on Agent Selection Reliability
**Situation:** A documentation system offers two designs: (1) One general `fetch_documentation` tool with a `source` parameter (API, database, file), or (2) Three specialized tools: `fetch_api_docs`, `fetch_database_schema`, `fetch_file_content`. Testing shows agents frequently misselect the `source` parameter in design 1, but correctly pick tools in design 2.
**Options:**
- A) Both designs have equal reliability; misselection is due to poor descriptions, not tool design
- B) Specialized tools are more reliable because tool names are the primary selection signal
- C) Use the general tool with better system prompt guidance to improve parameter selection
- D) Add a routing classifier to handle tool selection for the general tool

**Correct Answer:** B
**Feedback:** Tool names are the primary selection signal agents use. Specialized tool names (`fetch_api_docs`) are unambiguous and self-describing. Parameter selection is less reliable than distinct tool names. A ignores the selection mechanism. C relies on prompts which can't overcome the ambiguity of parameter selection. D adds complexity without solving the underlying selection problem.
**Theory Reference:** Domain 2.3 - Tool specialization for reliability

---

## Question 176
**Title:** Retryable vs Non-retryable Error Response and Recovery
**Situation:** An MCP tool integration encounters two errors: (1) "Database connection timeout" with `isRetryable: true`, and (2) "User lacks permission to access resource X" with `isRetryable: false`. How should these be handled?
**Options:**
- A) Treat both as failures and immediately ask a human to intervene
- B) Retry both with exponential backoff; retrying never causes harm
- C) Retry (1) with backoff; don't retry (2), instead escalate or ask for appropriate credentials
- D) Retry (1) with backoff; attempt (2) with alternative pathways, different credentials, or specialized retry logic before escalation

**Correct Answer:** D
**Feedback:** Transient errors like timeouts benefit from retry with backoff and often succeed on retry. Permission errors should be explored with retry variants—different credentials, alternative pathways, or contextual adjustments might resolve access issues. This reduces unnecessary escalation. A over-escalates recoverable errors. B wastes resources retrying non-recoverable errors. C prematurely gives up without exploring recovery options.
**Theory Reference:** Domain 2.2 - Error categorization and retry decisions

---

## Question 177
**Title:** Coordinator Result Aggregation with Large Output Volumes
**Situation:** Your coordinator spawns 4 subagents researching market segments. Each returns 12,000 tokens of detailed findings. Total: 48,000 tokens. The coordinator needs to synthesize a unified market analysis. What is the most effective approach?
**Options:**
- A) Include all 48,000 tokens in the coordinator's context and process them directly
- B) Request each subagent provide a 5-key-finding summary (reducing each 12K to 1-2K tokens)
- C) Store findings in a vector database and have the coordinator query for relevant segments
- D) Run the synthesis agent independently on each subagent's output, then merge the syntheses

**Correct Answer:** C
**Feedback:** Vector databases enable semantic search over the 48,000 tokens without loading everything. The coordinator queries for specific segments and synthesizes from relevant results. This scales and preserves detail. A wastes context. B loses nuance through summarization. D fragments the synthesis task.
**Theory Reference:** Domain 1.2 - Result aggregation strategies

---

## Question 178
**Title:** System Prompt Specificity and Unintended Tool Misuse
**Situation:** Your agent's system prompt includes: "Always verify customer identity before processing requests." The agent begins calling an identity verification tool even for information-only queries that don't require it, wasting tokens and increasing latency by 20%. Tool descriptions are clear and accurate.
**Options:**
- A) Tool descriptions are insufficient; add more specific guidance to the system prompt
- B) Implement a precondition hook that blocks identity verification on certain request types
- C) Use a higher-tier model that better understands context nuances
- D) Refine the instruction to: "Verify customer identity only before refunds, account changes, or financial transactions"

**Correct Answer:** D
**Feedback:** Refining the system prompt to specify when identity verification is required directly addresses the root cause of over-verification. Clear, specific instructions limit unnecessary tool calls. A adds complexity without clarity. B creates infrastructure overhead for a problem better solved with clearer instructions. C avoids the real issue by shifting to a different model.
**Theory Reference:** Domain 1.5 - Hook placement and deterministic guarantees

---

## Question 179
**Title:** Message Batches API Suitability for Different Workload Types
**Situation:** Your team operates three automated workflows: (1) a pre-merge code-quality check that blocks developer PRs (must complete in <5 minutes), (2) a nightly security scan of all repositories, and (3) an hourly status dashboard update. A cost-conscious manager proposes moving all three to the Message Batches API for 50% savings.
**Options:**
- A) Move only (2) to batch processing; keep (1) and (3) synchronous for latency requirements
- B) Move all three; batch processing can handle any workflow with scheduling
- C) Keep all three synchronous; batch processing has unpredictable latency
- D) Move (2) and (3) to batch processing; keep (1) and (2) as conditional based on urgency

**Correct Answer:** A
**Feedback:** The Batches API saves 50% but offers no latency SLA—processing can take up to 24 hours. This is fine for nightly scans (2) but unsuitable for blocking checks (1) where developers wait, or frequent updates (3). B and C misunderstand batch suitability. D contains a logical error.
**Theory Reference:** Domain 4.5 - Batch processing strategies

---

## Question 180
**Title:** Local Recovery in Subagents vs Coordinator Escalation
**Situation:** A document analysis subagent frequently encounters three error types while parsing files: (1) transient network timeouts, (2) malformed file formats (corrupted PDFs), and (3) large files that exceed temporary memory limits. Currently, any error immediately escalates to the coordinator. What local recovery approach reduces coordinator workload most effectively?
**Options:**
- A) Have the subagent implement retries for (1), skip (2) and (3), and escalate only (2) and (3) with partial results
- B) Implement local recovery for all three: retry (1) with backoff, sample (2) for recoverable sections, fragment (3) into smaller chunks
- C) Have the subagent implement retries for (1) and (2), escalate (3) with partial results
- D) Have the subagent return success for all files, masking errors in metadata to avoid coordinator involvement

**Correct Answer:** B
**Feedback:** Local recovery reduces coordinator workload: retry transient errors (1), extract recoverable data from malformed files (2), and split large files (3). D is problematic—masking errors prevents the coordinator from understanding file quality. A leaves (2) and (3) unhandled. C misidentifies (2) as retryable.
**Theory Reference:** Domain 5.3 - Error propagation strategies

---
