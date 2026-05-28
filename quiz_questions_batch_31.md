# Quiz Question Bank - Batch 31 (Questions 271-280)

## Question 271
**Title:** Concrete Input/Output Examples for Clarifying Transformation Requirements
**Situation:** You're building an agent that converts unstructured customer feedback into structured data. You've written a detailed prompt explaining normalization rules. The agent still produces inconsistent output. What's the most effective fix?
**Options:**
- A) Rewrite the prompt with more detailed instructions about each field
- B) Add error handling to catch and reject bad outputs
- C) Increase max_tokens to give the agent more reasoning space
- D) Provide concrete input/output examples showing expected transformations

**Correct Answer:** D
**Feedback:** Concrete input/output examples communicate expectations far more effectively than lengthy prose instructions. A single well-chosen example (messy input → clean output) clarifies normalization rules better than hundreds of words. Examples make implicit assumptions explicit and enable the agent to generalize to similar patterns.
**Theory Reference:** Domain 3.5 - Iterative Refinement with concrete examples

---

## Question 272
**Title:** Test-Driven Iteration for Agent Output Quality
**Situation:** You're developing an agent that generates legal document summaries. After three prompt rewrites, the output is still missing key clauses. How should you approach the next iteration?
**Options:**
- A) Have the agent summarize 100 documents and manually review each one
- B) Ask for more guidance about what constitutes complete in system prompt
- C) Write a test suite with expected outputs, then iterate based on failures
- D) Switch to a different model with better summarization capabilities

**Correct Answer:** C
**Feedback:** Test-driven iteration creates a feedback loop: write tests with expected outputs for edge cases (long documents, ambiguous clauses), run the current prompt against test cases, identify failures, adjust the prompt. This is far more efficient than manual review or hunting for the "perfect" prompt without measurable targets. Failures guide the next change.
**Theory Reference:** Domain 3.5 - Test-driven iteration for output quality

---

## Question 273
**Title:** Designing Explicit Prompt Criteria to Reduce False Positives
**Situation:** Your code review agent flags comments in 30% of cases, but 20% are false positives. This undermines developer trust. Which approach best addresses this?
**Options:**
- A) Simplify the system prompt to be less strict
- B) Define explicit criteria: flag contradictions or outdated comments
- C) Disable comment checking entirely until perfectly tuned
- D) Ask developers to rate each flagged comment

**Correct Answer:** B
**Feedback:** Explicit categorical criteria (with examples) eliminate ambiguity and false positives. Instead of vague guidance ("check comment accuracy"), specify exactly when to flag: contradicts behavior, references removed variables, outdated by version. This deterministic approach reduces false positives while maintaining catches on real issues.
**Theory Reference:** Domain 4.1 - Explicit Prompt Criteria to improve accuracy

---

## Question 274
**Title:** Few-shot Prompting for Handling Ambiguous Cases
**Situation:** Your extraction tool misclassifies "invoices" as "receipts" 40% of the time. Both have similar formats. What's the most reliable way to improve classification?
**Options:**
- A) Add a detailed prose description of the differences
- B) Use a separate classification model trained on your documents
- C) Provide few-shot examples with correct labels and rationale
- D) Require users to manually classify documents before extraction

**Correct Answer:** C
**Feedback:** Few-shot examples are the most effective method for handling ambiguous cases. Showing "this document has a PO field → invoice" and "this document has a cash total → receipt" demonstrates the distinction better than prose. The model generalizes from examples to new cases it hasn't seen.
**Theory Reference:** Domain 4.2 - Few-shot Prompting for ambiguous cases

---

## Question 275
**Title:** Incremental Re-review in CI Pipelines
**Situation:** Your CI runs code review after every commit. On commit 2, it finds 5 issues. On commit 3, 3 are fixed and 2 new ones appear. What should the report show on commit 3?
**Options:**
- A) Only the 2 new issues and confirm the 3 fixed ones
- B) All 4 current issues from both commits combined
- C) All issues from all commits to track full history
- D) Only the 3 remaining issues from commit 2

**Correct Answer:** A
**Feedback:** Incremental re-review reports only newly detected issues plus confirmation that fixed issues are resolved. This prevents reviewer fatigue from re-reading solved problems while highlighting progress and new concerns. Tracking which issues are newly introduced (vs carried over) provides actionable feedback to developers.
**Theory Reference:** Domain 3.6 - Incremental re-review in CI/CD pipelines

---

## Question 276
**Title:** Batch Processing with custom_id for Correlation
**Situation:** You're submitting 10,000 documents for overnight processing via the Batch API. Document 847 fails due to malformed JSON. How do you identify and resubmit only the failed document?
**Options:**
- A) Use `custom_id` to tag each request; failures return the `custom_id`
- B) Resubmit the entire batch with corrected JSON and track results
- C) Ask users to resubmit failed documents manually through the UI
- D) Use the synchronous API for better error handling

**Correct Answer:** A
**Feedback:** `custom_id` is the batch processing correlation mechanism. Tag each request with a unique identifier. When failures occur, the API returns the `custom_id`, allowing you to identify exactly which documents failed and resubmit only those in the next batch run.
**Theory Reference:** Domain 4.5 - Batch API custom_id for correlation

---

## Question 277
**Title:** Planning Batch Submission Cadence Around SLA Requirements
**Situation:** Your SLA guarantees results within 30 hours. The Batch API has a 24-hour processing window. How should you structure batch submission?
**Options:**
- A) Submit smaller batches every 4-6 hours to create a buffer window
- B) Submit one large batch every 30 hours and accept delays beyond 24 hours
- C) Submit batches once per week to minimize costs and API overhead
- D) Use the synchronous API for guaranteed latency compliance

**Correct Answer:** A
**Feedback:** The Batch API offers no latency SLA—processing can take up to 24 hours. To guarantee results within 30 hours, submit smaller batches every 4-6 hours, creating a buffer. Early batches complete within their guarantee window, even if later batches take the full 24 hours. This ensures consistent SLA compliance across all submission times.
**Theory Reference:** Domain 4.5 - Batch submission cadence planning

---

## Question 278
**Title:** Self-Rated Confidence for Routing to Human Review
**Situation:** Your agent extracts financial figures from documents. On complex documents with conflicting numbers, should the agent self-rate its confidence (1–10) for escalation?
**Options:**
- A) Yes; self-rated confidence is a reliable accuracy indicator for routing
- B) Ask users to rate complexity and escalate high-complexity cases
- C) No; use field-level calibration from validation data instead
- D) Use confidence only as a secondary signal to avoid over-routing

**Correct Answer:** C
**Feedback:** Models can be confidently wrong. Self-rated confidence does not correlate reliably with accuracy, especially on ambiguous cases. Instead, calibrate field-level confidence thresholds using labeled validation data: "figures from scanned PDFs have 87% accuracy; route below 80% to human review." Field-level calibration is far more reliable than generic self-confidence scores.
**Theory Reference:** Domain 5.5 - Confidence calibration for routing

---

## Question 279
**Title:** Policy-Gap Escalation Criteria with Few-shot Examples
**Situation:** Your support agent handles refund requests. The policy states: "refunds within 30 days are automatic; beyond 30 days requires manager approval." A customer requests a refund at day 31 due to a shipping delay. Should the agent escalate?
**Options:**
- A) No; the policy clearly denies refunds after day 30 passes
- B) Ask the customer for more information to decide independently
- C) Always escalate refund requests to avoid customer complaints
- D) Yes; escalate with few-shot examples of similar policy exceptions

**Correct Answer:** D
**Feedback:** The policy contains a clear gap: day 31 is a borderline case where context matters (shipping delays may warrant exceptions). Escalate with structured reasoning: "Policy is ambiguous at day 31; this is a policy-gap case, not an ambiguous customer request." Include few-shot examples of similar day-31 cases and their outcomes to help the human reviewer establish a consistent pattern.
**Theory Reference:** Domain 5.2 - Policy-gap escalation with few-shot criteria

---

## Question 280
**Title:** Rendering Content by Type for Clarity and Usability
**Situation:** Your synthesis agent combines findings from financial reports, news articles, and technical documentation about a company. The raw output mixes all formats: paragraphs, statistics, and code. How should content be rendered?
**Options:**
- A) Output all findings as uniform prose paragraphs for consistency and simplicity
- B) Render financial → tables, news → prose, technical → structured lists
- C) Ask users to specify their preferred format before processing begins
- D) Use JSON to structure all content identically

**Correct Answer:** B
**Feedback:** Content type determines optimal presentation. Financial data is clearest in tables (easy comparison), news is best as narrative prose (context and timeline matter), technical findings benefit from structured lists (quick scanning). Matching format to content type improves usability and accuracy of interpretation. Uniform formatting masks important distinctions in source material.
**Theory Reference:** Domain 5.6 - Rendering content by type for clarity
