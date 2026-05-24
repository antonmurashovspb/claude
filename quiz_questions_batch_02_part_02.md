# Quiz Question Bank - Batch 02 Part 02 (Questions 71-80)

## Question 71
**Title:** Combining Tool Results Strategically
**Situation:** You have `fetch_weather` and `fetch_forecast` tools. A user asks "What should I wear tomorrow?" You need both current conditions (weather) and tomorrow's forecast. Should you call them sequentially or together?
**Options:**
- A) Call both tools in the same agent turn, process results together
- B) Call fetch_weather, wait for result, then call fetch_forecast
- C) Call fetch_forecast only; it includes both current and forecasted conditions
- D) Call fetch_weather only; weather includes forecast information

**Correct Answer:** A
**Feedback:** When two tools provide complementary data needed for a complete answer, call both in the same agent turn. The agent can combine results and provide a unified recommendation. Sequential calls add latency without benefit.
**Theory Reference:** Domain 1.1 - Agent loop batching multiple tool calls

---

## Question 72
**Title:** Subagent Reporting Structure
**Situation:** A subagent completes its investigation and must report findings to the coordinator. The subagent's findings include: (1) data (what was found), (2) methodology (how it was found), (3) confidence levels, (4) gaps. What should be included?
**Options:**
- A) All of it: data, methodology, confidence, gaps. The coordinator needs complete context to synthesize and weight findings
- B) Only the data; methodology and gaps are unnecessary detail
- C) Data + confidence levels; methodology is implementation detail
- D) Only answers to the original task; don't include metadata

**Correct Answer:** A
**Feedback:** Structured subagent reports must include: findings (data), how they were determined (methodology for credibility), confidence levels (for weighting), and gaps (to identify blind spots). This enables informed coordinator synthesis.
**Theory Reference:** Domain 1.2 - Subagent reporting structure

---

## Question 73
**Title:** Retryable vs Non-retryable Extraction Errors
**Situation:** Document extraction fails with two errors: (1) "Corrupted PDF file cannot be parsed" (corruption is permanent), (2) "OCR service timeout" (likely temporary). How should these be handled differently?
**Options:**
- A) Retry (2) immediately; (1) is non-retryable, skip or request format conversion
- B) Retry both with exponential backoff; retries can't hurt
- C) Escalate both to a human for manual intervention
- D) Ignore both errors and flag the document as unprocessable

**Correct Answer:** A
**Feedback:** Retry strategy depends on error type. Transient errors (timeouts, temporary service failures) benefit from retries. Permanent errors (corrupted files, incompatible formats) don't—fix the root cause or skip the document.
**Theory Reference:** Domain 2.2 - Error classification and retry strategy

---

## Question 74
**Title:** Agent Loop Continuity and History
**Situation:** An agent makes a tool call, receives a result, and processes it. On the next iteration, should the tool result remain in the conversation history?
**Options:**
- A) Yes, append tool results to the conversation so the model can reference them in subsequent iterations
- B) No, remove results after processing to save context tokens
- C) Only keep results from the last 3 iterations to balance context usage
- D) Store results in a separate database, not in the conversation

**Correct Answer:** A
**Feedback:** Tool results must remain in the conversation history. The model needs to reference prior results when making subsequent decisions. Removing results breaks the agent loop—the model loses reasoning continuity.
**Theory Reference:** Domain 1.1 - Conversation history and agent loop

---

## Question 75
**Title:** Coordinator Observability and Communication
**Situation:** Three subagents are running concurrently, and one fails silently (returns empty results). The coordinator doesn't know if it succeeded (no matches) or failed (access error). How should subagents communicate status?
**Options:**
- A) Subagent should return structured metadata: `{"status": "success", "recordCount": 0}` or `{"status": "failed", "errorReason": "access_denied"}`
- B) The coordinator should assume all empty results are successful queries
- C) Subagents should log errors to a central system; the coordinator can query logs if needed
- D) Silent failures are acceptable; the coordinator shouldn't worry about partial data

**Correct Answer:** A
**Feedback:** All communication must flow through the coordinator for observability. Structured metadata (status + context) enables the coordinator to distinguish success from failure. Silent failures break observability and decision-making.
**Theory Reference:** Domain 1.2 - Coordinator observability

---

## Question 76
**Title:** JSON Schema Flexibility for Extensibility
**Situation:** You define a schema for extracted entities with fields: `type`, `name`, `value`. Later, you need to add new entity types not in your original list. Should you use a strict enum or allow flexibility?
**Options:**
- A) Use enum with "other": `type: enum: ["person", "company", "location", "other"]` plus `type_detail: "string"` for clarification
- B) Use a strict enum: `type: ["person", "company", "location"]` to enforce consistency
- C) Remove the type field entirely and let the agent infer types dynamically
- D) Allow any string for flexibility without enum constraints

**Correct Answer:** A
**Feedback:** Enums with "other" + detail field balance structure and flexibility. Known types are constrained; unknown types use "other" + explanation. This maintains schema structure while allowing growth.
**Theory Reference:** Domain 4.3 - Schema design for extensibility

---

## Question 77
**Title:** Synchronous vs Asynchronous Escalation
**Situation:** An agent encounters a customer escalation request: "I want to speak to a manager." Should the agent wait for a human response before continuing, or continue processing other requests?
**Options:**
- A) Escalate asynchronously: create an escalation ticket, return a confirmation to the user, and continue processing other requests
- B) Wait synchronously for the human to respond before closing the conversation
- C) Deny the escalation and continue helping the customer autonomously
- D) Shut down the agent and require manual restart

**Correct Answer:** A
**Feedback:** Asynchronous escalation is better for workflow efficiency. Create an escalation ticket (with customer ID, reason, context) and notify the user it's being reviewed. The agent can continue with other requests. Synchronous blocks and reduces throughput.
**Theory Reference:** Domain 5.2 - Escalation patterns

---

## Question 78
**Title:** Ambiguous Customer Requests
**Situation:** A customer says "Fix my account." The agent's options: (1) ask clarifying questions, (2) check the account for obvious issues, (3) immediately escalate. What's the best approach?
**Options:**
- A) Ask clarifying questions first ("What specific problem are you experiencing?") to narrow the scope
- B) Immediately escalate; ambiguity always requires a human
- C) Check for obvious issues first, then ask clarifying questions if nothing obvious is found
- D) Assume the most common problem type and proceed

**Correct Answer:** A
**Feedback:** Ambiguous requests benefit from clarification before escalation. "Fix my account" could mean password reset, billing issue, or data correction. Asking "What specific problem?" narrows scope, reduces escalations, and enables faster resolution.
**Theory Reference:** Domain 5.2 - Handling ambiguity and escalation

---

## Question 79
**Title:** Confidence Self-rating and Action
**Situation:** Your extraction system outputs `{"confidence_level": 0.45, "extracted_value": "XYZ"}` for a critical field. The confidence is low (below 0.7 threshold). What should happen?
**Options:**
- A) Route low-confidence extractions to human review with the value and confidence level shown
- B) Use the value anyway; the model is doing its best with available data
- C) Automatically retry extraction if confidence < 0.7
- D) Reject the entire document and require manual resubmission

**Correct Answer:** A
**Feedback:** Low confidence signals should trigger human review, not automatic retries (may fail again) or auto-rejection (discards potentially correct data). Show the extracted value and confidence—let a human verify. This balances automation with accuracy.
**Theory Reference:** Domain 5.5 - Confidence-based routing

---

## Question 80
**Title:** Attribution and Source Tracking
**Situation:** Your coordinator synthesizes findings from 5 subagents into a report. A key statistic comes from Subagent C. Later, a reader asks "Where does this statistic come from?" If you lost the source mapping, you can't answer. How to prevent this?
**Options:**
- A) Subagents should include quotes and sources in their output; coordinator preserves these during synthesis
- B) Request that subagents highlight "important facts" for tracking purposes
- C) Store all subagent outputs in a database for later reference
- D) Assume readers won't ask about sources; document attribution after the fact

**Correct Answer:** A
**Feedback:** Attribution must be preserved during synthesis. Subagents should provide: claim + source (URL, document name, quote). The coordinator preserves these mappings during aggregation. This enables accountability and verification.
**Theory Reference:** Domain 5.6 - Preserving provenance and attribution
