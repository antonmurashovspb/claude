# Quiz Question Bank - Batch 34 (Questions 331-340)

## Question 331
**Title:** Path-specific Rules for Convention Loading
**Situation:** Your repository has test files spread across multiple directories (`src/`, `lib/`, `api/`, `utils/`). Each location needs consistent test conventions (async/await patterns, mocking setup). Creating a CLAUDE.md in each directory is cumbersome and hard to maintain.
**Options:**
- A) Create a single `.claude/rules/` file with glob patterns like `**/*.test.tsx`
- B) Create separate CLAUDE.md files in each directory with tests
- C) Store all conventions in root CLAUDE.md and apply contextually
- D) Create individual rule files for each directory separately

**Correct Answer:** A
**Feedback:** Path-specific rules with glob patterns are designed exactly for this scenario. `paths: ["**/*.test.tsx"]` loads the rule only when editing matching files, saving context across the entire codebase. Directory-level CLAUDE.md files require duplication. Root CLAUDE.md is unreliable due to attention dilution. Individual rule files per directory defeat the purpose of centralization.
**Theory Reference:** Domain 3.3 - Using Path-specific Rules for Conditional Convention Loading

---

## Question 332
**Title:** Planning Mode vs Direct Execution for Architecture Changes
**Situation:** You need to refactor an authentication system that spans 40+ files across multiple services (API, frontend, backend), affecting 15 interdependent components. You've identified a clear migration path, but want to ensure you understand all impact zones before making changes.
**Options:**
- A) Use direct execution with incremental changes; you can revert if needed
- B) Use planning mode to safely explore dependencies and architectural impact before committing changes
- C) Start with direct execution and switch to planning if you encounter issues
- D) Ask a senior engineer to do it manually; automation risks are too high for this scope

**Correct Answer:** B
**Feedback:** Planning mode is designed for complex tasks with large scope (45+ files), multiple services, and architectural dependencies. It enables safe exploration of the codebase structure and impact zones before making changes, preventing expensive rework. Direct execution risks missing dependencies. C is reactive and less efficient. D overestimates automation risks when proper planning is available.
**Theory Reference:** Domain 3.4 - Deciding When to Use Planning Mode vs Direct Execution

---

## Question 333
**Title:** Direct Execution for Well-scoped Changes
**Situation:** A user reports a specific bug: validation logic in `auth/login.ts` incorrectly rejects valid passwords with special characters. The stack trace points directly to line 47 in one file. The fix involves adding a single regex character escape.
**Options:**
- A) Use planning mode to explore all related authentication files first
- B) Create a separate branch to experiment before committing
- C) Use direct execution; the scope is clear and localized
- D) Run automated tests to identify similar bugs elsewhere before fixing

**Correct Answer:** C
**Feedback:** Direct execution is appropriate when the problem is well-understood (clear stack trace), the fix is localized (single file), and the scope is minimal. Planning mode introduces unnecessary overhead for straightforward fixes. Creating a separate experimental branch or running comprehensive tests is appropriate after the core fix is done, not before.
**Theory Reference:** Domain 3.4 - Deciding When to Use Planning Mode vs Direct Execution

---

## Question 334
**Title:** Explicit Review Criteria in Code Review Prompts
**Situation:** Your code review system flags both critical bugs and stylistic inconsistencies, leading developers to dismiss findings because the false-positive rate in style categories is 60%. Developers ignore valid bug reports mixed with style flags.
**Options:**
- A) Disable the entire review process requiring manual reviews only
- B) Define explicit criteria: flag logical errors and security only
- C) Add a note saying "be more conservative" in the system prompt
- D) Implement reviewer voting where consensus is required

**Correct Answer:** B
**Feedback:** Explicit categorical criteria are far more effective than vague guidance like "be more conservative." High false-positive rates in low-severity categories (style) damage trust in high-severity findings (bugs). By explicitly defining what to report (bugs, security) vs ignore (style), you maintain developer confidence. Vague guidance (C) doesn't address the root cause. A and D are overreactions.
**Theory Reference:** Domain 4.1 - Designing Prompts with Explicit Criteria to Improve Accuracy

---

## Question 335
**Title:** Explicit Severity Criteria in Extraction Tasks
**Situation:** Your system extracts compliance issues from audit documents. It consistently flags ambiguous or borderline issues as "critical," flooding stakeholders with unclear findings. Trust in the extraction system is low because severity isn't calibrated.
**Options:**
- A) Increase the threshold to reduce findings
- B) Require human review of all findings before reporting
- C) Switch to a different model for extraction
- D) Define severity criteria with examples per level

**Correct Answer:** D
**Feedback:** Explicit severity criteria with examples enable consistent calibration. Ambiguous borderline cases now have clear categorical guidance instead of relying on model judgment. This maintains stakeholder confidence by reducing noise and false alarms. A (threshold increase) is a blunt instrument. B doesn't solve calibration—the model still makes incorrect severity assignments. C assumes the model is the problem.
**Theory Reference:** Domain 4.1 - Designing Prompts with Explicit Criteria to Improve Accuracy

---

## Question 336
**Title:** Few-shot Prompting for Consistent Extraction Format
**Situation:** Your system extracts structured data from unstructured documents (invoices with varying formats). Results are inconsistent: some include currency symbols, some don't; some round amounts differently. Without examples, the model generalizes inconsistently to new document structures.
**Options:**
- A) Provide detailed written instructions covering all edge cases
- B) Use 2–4 targeted few-shot examples for consistent output
- C) Use a stricter JSON schema to enforce syntax level format
- D) Train a specialized model specifically for extraction

**Correct Answer:** B
**Feedback:** Few-shot examples are the most effective method for producing consistently formatted output across varying input structures. 2–4 targeted examples demonstrate how to handle ambiguous cases (currency, rounding) and help the model generalize to new document types. Detailed written instructions (A) are less effective than concrete examples. Schema alone (C) enforces syntax but not semantic consistency. Custom training (D) is unnecessary overhead.
**Theory Reference:** Domain 4.2 - Using Few-shot Prompting to Improve Output Consistency

---

## Question 337
**Title:** Few-shot Examples for Handling Ambiguous Tool Selection
**Situation:** Your extraction system incorrectly categorizes invoice line items. Sometimes it uses the `extract_product` tool, sometimes `extract_line_item`. Both tools have overlapping descriptions. The model needs guidance on when to use each.
**Options:**
- A) Update the tool descriptions to be more detailed
- B) Merge the two tools into a single generic tool
- C) Implement a routing classifier that decides tool usage
- D) Provide 2–3 few-shot examples with clear rationale

**Correct Answer:** D
**Feedback:** Few-shot examples with rationale are the most effective way to disambiguate between similar tools. Concrete examples showing "use `extract_product` for SKU-based items, use `extract_line_item` for service billing" demonstrate the correct approach. Updated descriptions (A) are less effective. Merging tools (B) loses specificity. A routing classifier (C) adds unnecessary complexity—examples solve the problem directly.
**Theory Reference:** Domain 4.2 - Using Few-shot Prompting to Improve Output Consistency

---

## Question 338
**Title:** Validation and Error Feedback in Extraction Retries
**Situation:** Your extraction system extracts invoice totals. On first pass, it returns `subtotal: 150`, `tax: 18`, `total: 160`. Validation shows math doesn't add up (150 + 18 = 168, not 160). You want to retry with error feedback.
**Options:**
- A) Include the original document, incorrect extraction, and specific error in retry
- B) Re-run extraction on the full document without error feedback
- C) Ask for manual data entry since automation isn't working
- D) Increase the model temperature to encourage different reasoning

**Correct Answer:** A
**Feedback:** Retry-with-error-feedback is the gold standard. Include: the original document, the incorrect extraction showing the error, and specific validation error ("subtotal + tax should equal total; you calculated 160 but 150 + 18 = 168"). This gives the model concrete guidance to self-correct. Re-running without feedback (B) repeats the same error. Manual entry (C) defeats automation. Temperature changes (D) don't address the root cause.
**Theory Reference:** Domain 4.4 - Implementing Validation, Retries, and Feedback Loops for Extraction Quality

---

## Question 339
**Title:** When Retries Are Ineffective in Data Extraction
**Situation:** Your extraction system needs to find "authorized signatory name" from financial documents. The document shows only a signature block with an illegible signature and no printed name. Validation catches that the field is empty. Should you retry?
**Options:**
- A) Retry with stronger prompting to find the name more thoroughly
- B) Retry with a different extraction format (JSON instead of text)
- C) Recognize that the required information is absent from the source
- D) Combine multiple documents to infer the signatory from context

**Correct Answer:** C
**Feedback:** Retries are ineffective when required information is absent from the source. Stronger prompting (A) won't create data that isn't there. Format changes (B) don't change the source data. Cross-document inference (D) introduces hallucination risk and is architecturally wrong—extract what's present, not what's inferred. Design extraction to return nullable fields with "not found" or "illegible" metadata instead of retrying indefinitely.
**Theory Reference:** Domain 4.4 - Implementing Validation, Retries, and Feedback Loops for Extraction Quality

---

## Question 340
**Title:** Multi-instance Review Architecture for Unbiased Findings
**Situation:** Claude Code generates a complex refactoring of a database migration system. The same Claude instance that generated the code is now reviewing it but retains all its original reasoning context. It's overlooking subtle issues in error-handling logic.
**Options:**
- A) Use an independent review instance without generation context
- B) Run self-review with stronger prompting to be more critical
- C) Have the generation instance generate tests first, then review
- D) Combine self-review with additional static analysis tools

**Correct Answer:** A
**Feedback:** Self-review limitations: the model retains reasoning context and unconsciously reinforces its own decisions, making it less effective at challenging itself. An independent review instance (without generation context) approaches the code fresh and is superior at finding subtle logical errors, especially in complex scenarios like error handling. Stronger prompting (B) doesn't overcome the cognitive bias. Tests (C) validate behavior but don't identify logic issues. Static analysis (D) supplements but doesn't replace independent review for semantic correctness.
**Theory Reference:** Domain 4.6 - Designing Multi-instance and Multi-pass Review Architectures
