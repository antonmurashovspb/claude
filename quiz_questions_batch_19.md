# Quiz Question Bank - Batch 19 (Questions 191-200)

## Question 191

**Title:** Session State Preservation vs Fresh Start
**Situation:** You're analyzing a complex codebase. After 3 hours of investigation, you discover the initial understanding was incorrect. The previous session has 50K tokens of stale findings. You need current results. Should you `--resume` the session or start fresh?
**Options:**
- A) Add corrections to context as you discover them
- B) Resume the session to preserve efficiency
- C) Start a fresh session with corrected summary
- D) Compare both sessions in parallel runs

**Correct Answer:** A
**Feedback:** When interim findings are stale or incorrect, a fresh session with a corrected summary is more effective than resuming. The model will not reliably challenge its own stale reasoning. Fresh context + corrected summary enables accurate re-analysis. B wastes tokens on outdated analysis. C adds corrections but keeps the flawed reasoning. D doubles cost without adding value.
**Theory Reference:** Domain 1.7 - Session state and forking strategies

---

## Question 192

**Title:** Tool Results Accumulation and Context Efficiency
**Situation:** Your agent calls a database tool that returns 45 fields: customer ID, name, email, phone, address, billing history (35 fields), preferences, loyalty status. Only customer ID and name are needed for the next step. What should you do?
**Options:**
- A) Extract only needed fields; trim unnecessary data before appending
- B) Include all 45 fields to preserve information for future use
- C) Request the agent ignore unused fields via system prompt
- D) Store results in vector database; pass only reference

**Correct Answer:** A
**Feedback:** Tool outputs accumulate in context with each iteration. Keeping all 45 fields wastes tokens disproportionately. Extract and include only relevant fields—preserves information needed downstream without context bloat. B wastes tokens across subsequent iterations. C relies on prompts that the model may ignore. D adds unnecessary complexity.
**Theory Reference:** Domain 5.1 - Context management and tool output trimming

---

## Question 193

**Title:** Preconditions vs Prompts for Multi-step Enforcement
**Situation:** Your refund workflow requires: (1) verify customer identity, (2) check refund policy eligibility, (3) process refund. You notice step (3) sometimes runs without (1) completing. Should you use prompt guidance or a precondition hook?
**Options:**
- A) Implement a prompt reminding agents to verify customers
- B) Add a precondition hook blocking process until verified
- C) Combine both prompt and hook for added safety
- D) Require human approval for all refund requests

**Correct Answer:** B
**Feedback:** Precondition hooks provide deterministic guarantees that prompts cannot. A hook enforces the rule: `process_refund` cannot execute if `verify_identity` has not succeeded. This is non-negotiable for financial operations. A relies on model behavior which can fail. C adds redundancy without added safety. D abandons efficiency without reason.
**Theory Reference:** Domain 1.4 - Multi-step workflow enforcement; Domain 1.5 - Hook patterns

---

## Question 194

**Title:** Required vs Optional Fields in Extraction Schemas
**Situation:** You design an extraction tool to identify key contacts from documents. Sometimes the document includes emails and phone numbers, sometimes only names. If you mark email and phone as required fields in the schema, what happens when they are absent?
**Options:**
- A) The model returns `null` for missing fields and moves to the next document
- B) The tool returns a validation error, forcing the document to be skipped
- C) The model fabricates plausible email/phone values to satisfy the schema
- D) The document is re-submitted for retry with different prompting

**Correct Answer:** C
**Feedback:** Required fields pressure the model to fabricate values rather than admit absence. Mark email/phone as optional with `"type": ["string", "null"]` to allow the model to return `null` when data is missing. This prevents hallucination. A is correct behavior but requires optional fields, not required. B and D don't reflect schema behavior.
**Theory Reference:** Domain 4.3 - JSON schema design for extraction

---

## Question 195

**Title:** Adaptive vs Fixed Task Decomposition
**Situation:** You need to process 100 legal documents: extract claims, identify risks, recommend actions. Should you pre-define 3 sequential stages (extract → assess → recommend) or generate stages dynamically based on each document's characteristics?
**Options:**
- A) Pre-define 3 fixed stages; consistent ordering ensures uniform quality
- B) Submit all documents to a single comprehensive prompt to reduce API calls
- C) Generate stages dynamically per document based on detected complexity; adapt to variations
- D) Use specialized models for each stage; fixed pipelines minimize coordination overhead

**Correct Answer:** C
**Feedback:** Adaptive decomposition adjusts task structure based on discovered characteristics. A complex claim might need additional investigation stages; simple claims proceed faster. This scales better than fixed pipelines. A ignores document variance. D combines fixed pipelines with unnecessary multi-model overhead. B sacrifices focus for call efficiency.
**Theory Reference:** Domain 1.6 - Task decomposition strategies

---

## Question 196

**Title:** Vector Database vs Raw Context Passing for Coordinator Aggregation
**Situation:** Your coordinator spawns 8 subagents researching different market segments. Each returns 12K tokens. The coordinator must synthesize findings into a market analysis. Budget allows either (A) raw context passing of all 96K tokens or (B) vector DB storage with semantic search. Which is better?
**Options:**
- A) Pass raw 96K tokens directly; full context for all findings
- B) Request each subagent summarize to 2K tokens; aggregated context
- C) Use a separate summarization model to compress findings
- D) Use vector database; semantic search retrieves relevant findings only

**Correct Answer:** D
**Feedback:** Vector databases enable semantic search, allowing the coordinator to retrieve relevant findings without loading all 96K tokens. This preserves context for synthesis reasoning and scales to larger datasets. A exhausts context wastefully. B loses detail through summarization. C adds latency without proportional benefit.
**Theory Reference:** Domain 1.2 - Coordinator result aggregation at scale

---

## Question 197

**Title:** Tool Specialization Impact on Selection Reliability
**Situation:** Your document system offers two options: (1) three specialized tools (`extract_pdf_text`, `extract_image_text`, `extract_docx_text`) or (2) one general tool `extract_document_text` with a `format` parameter. Testing shows agents misselect the parameter frequently. Which design is more reliable?
**Options:**
- A) General tool is simpler; parameter selection is equally reliable
- B) Both are equally reliable; choose based on maintainability
- C) Specialized tools are more reliable; agents select by tool name
- D) Specialized tools only matter if you have 5+ format types

**Correct Answer:** C
**Feedback:** Tool names are the primary selection signal in agent decision-making. Specialized names (`extract_pdf_text`) are unambiguous. Parameter combinations (`format="pdf"`) require additional reasoning where errors occur. Tests confirm agents misuse parameters more than misname tools. A underestimates reliability differences. B ignores the selection mechanism. D sets an arbitrary threshold.
**Theory Reference:** Domain 2.3 - Tool specialization vs parameters

---

## Question 198

**Title:** PreToolUse vs PostToolUse Hook Placement Strategy
**Situation:** You need to: (1) validate all tool input parameters are in correct format before execution, and (2) convert all returned timestamps from Unix to ISO 8601 for the agent to process. Which hook(s) should you use?
**Options:**
- A) Both use PostToolUse for consistency; normalize everything in one place
- B) Input validation uses PreToolUse; timestamp conversion uses PostToolUse
- C) Both use PreToolUse; intercept before any tool interaction occurs
- D) Neither; use system prompt instructions for all transformations

**Correct Answer:** B
**Feedback:** PreToolUse intercepts outgoing requests (perfect for input validation). PostToolUse intercepts results before the model processes them (perfect for output normalization). Each hook serves its purpose in the data flow. A applies wrong hook for input. C applies wrong hook for output. D sacrifices deterministic guarantees that hooks provide.
**Theory Reference:** Domain 1.5 - Hook placement patterns

---

## Question 199

**Title:** Transient vs Non-retryable Error Categorization
**Situation:** Your MCP integration encounters: (1) API temporarily down (timeout), (2) malformed input to the tool call, (3) API rate limit exceeded, (4) database connection pool exhausted. Which should be retried vs escalated?
**Options:**
- A) Never retry; escalate all errors to a human
- B) Retry (1) and (3); escalate or fix (2) and (4)
- C) Retry all four with exponential backoff
- D) Retry (1) and (3); escalate or fix (2) and (4)

**Correct Answer:** D
**Feedback:** Transient errors benefit from retries with backoff: (1) timeout recovers, (3) rate limit clears. Non-retryable errors need remediation: (2) malformed input requires correction, (4) exhausted pool needs investigation. Retrying non-transient errors wastes resources. A over-escalates automatically-recoverable failures. C retries non-recoverable errors. B's premise is incorrect while D correctly categorizes errors.
**Theory Reference:** Domain 2.2 - Error categorization and retry decisions

---

## Question 200

**Title:** Lost-in-the-Middle Effect and Information Placement
**Situation:** Your agent processes 100K tokens: 10K system prompt, 20K conversation history, 40K tool results from subagents, 30K contextual documents. Critical findings from subagents appear in the middle of the 40K tool result block. The model frequently misses them. How should you restructure?
**Options:**
- A) Place a summary of critical findings at the beginning with clear headers
- B) Reduce the conversation history to 10K to make room for findings at the end
- C) Compress all tool results; summarize findings to eliminate detail loss
- D) Use a separate vector database to retrieve critical findings

**Correct Answer:** A
**Feedback:** The lost-in-the-middle effect means models reliably process information at the start and end of long inputs but may miss details in the middle. Place critical findings at the beginning with explicit section headers: "**Critical Subagent Findings:**". This guarantees processing. B reduces context inefficiently. C loses detail. D adds infrastructure unnecessarily.
**Theory Reference:** Domain 5.1 - Context management and lost-in-the-middle mitigation

---
