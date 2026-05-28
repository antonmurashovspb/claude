# Quiz Question Bank - Batch 33 (Questions 321-330)

## Question 321
**Title:** CLAUDE.md Hierarchy for Team Consistency
**Situation:** Your team has 12 developers. You define testing conventions in `~/.claude/CLAUDE.md` (user-level). New developers joining the team miss these conventions and follow their own patterns. Other developers' conventions are stored in `.claude/CLAUDE.md` (project-level) and are applied consistently.
**Options:**
- A) Project-level `.claude/CLAUDE.md` ensures conventions are version-controlled and shared with all team members automatically
- B) User-level CLAUDE.md is fine; conventions apply when developers set it up personally
- C) Both approaches are equivalent; the location doesn't matter as long as conventions exist
- D) Create separate CLAUDE.md files in each subdirectory instead of centralizing

**Correct Answer:** A
**Feedback:** CLAUDE.md hierarchy matters for team consistency. User-level settings (`~/.claude/CLAUDE.md`) are personal and not shared via VCS. Project-level (`.claude/CLAUDE.md` or root `CLAUDE.md`) is version-controlled and automatically applied to all team members. New developers inherit project conventions immediately.
**Theory Reference:** Domain 3.1 - CLAUDE.md hierarchy and modular organization

---

## Question 322
**Title:** Modularizing Large CLAUDE.md with `@path` Syntax
**Situation:** Your root `CLAUDE.md` has grown to 800 lines covering API conventions, testing standards, database patterns, and deployment rules. Adding features requires searching through all sections. You want each package to inherit only relevant standards.
**Options:**
- A) Use `@path` syntax to reference external files (e.g., `@./standards/testing.md`), enabling selective inclusion in each package's CLAUDE.md
- B) Keep everything in one `CLAUDE.md`; developers manually apply relevant sections
- C) Create a separate agent for each domain to handle conventions independently
- D) Duplicate the entire CLAUDE.md in every subdirectory

**Correct Answer:** A
**Feedback:** The `@path` syntax (e.g., `@./standards/testing.md`) modularizes CLAUDE.md without duplication. Each package's CLAUDE.md can selectively include relevant standards files. This keeps the main file focused and enables teams to inherit only what they need.
**Theory Reference:** Domain 3.1 - Modularizing CLAUDE.md with @path

---

## Question 323
**Title:** Path-specific Rules for Conditional Convention Loading
**Situation:** Your codebase has tests spread across many directories (`backend/tests/`, `frontend/tests/`, `utils/tests/`, etc.). You want to apply test-specific conventions (test naming, fixture patterns, assertion styles) only when developers edit test files, not when editing source code.
**Options:**
- A) Use `.claude/rules/` with YAML frontmatter including `paths: ["**/*.test.tsx"]` to load rules only for matching files
- B) Create directory-level CLAUDE.md in each tests/ directory to maintain conventions
- C) Put test conventions in the root CLAUDE.md so all files load them
- D) Manually remind developers about test conventions in PR reviews

**Correct Answer:** A
**Feedback:** Path-specific rules in `.claude/rules/` with glob patterns (e.g., `**/*.test.tsx`) load conditionally based on which files are being edited. Rules load **only** when editing matching files, saving context and tokens while applying conventions consistently across the codebase.
**Theory Reference:** Domain 3.3 - Path-specific rules for conditional convention loading

---

## Question 324
**Title:** Planning Mode for High-impact Architectural Changes
**Situation:** You need to decompose a monolithic API into microservices. The change affects 65 files, introduces new service boundaries, and has implications for authentication, deployment, and testing. You're uncertain about optimal service boundaries.
**Options:**
- A) Split the work into small changes and use direct execution for each one
- B) Use Planning Mode to explore the codebase, identify dependencies, and design approaches safely before making changes
- C) Ask a senior engineer to design it manually first
- D) Use direct execution with incremental changes; you can revert if needed

**Correct Answer:** B
**Feedback:** Planning Mode is designed for complex tasks with large changes, multiple viable approaches, and architectural decisions. It safely explores dependencies and design implications before any changes are made. Direct execution (D) risks expensive rework. Manual design (C) bypasses automation benefits. Small incremental changes (A) lose architectural context across the system.
**Theory Reference:** Domain 3.4 - Planning Mode vs direct execution for architectural tasks

---

## Question 325
**Title:** Explicit Review Criteria vs Vague Instructions
**Situation:** Your code review system uses the instruction: "Flag comments that are inaccurate or misleading." Reviewers report high false-positive rates on comments that are merely outdated or context-dependent. Developer trust in the review system is eroding.
**Options:**
- A) Use sentiment analysis to detect misleading tone
- B) Define explicit categorical criteria: flag only comments that contradict current code logic, ignore contextual or outdated comments; provide code examples for each category
- C) Add more reviewer training to improve judgment
- D) Disable the comment review category until accuracy improves

**Correct Answer:** B
**Feedback:** Explicit criteria (e.g., "flag only when comment contradicts current code") are more effective than vague instructions like "check accuracy." Concrete categorical criteria with code examples reduce false positives. High false-positive rates in one category undermine trust in accurate categories, so calibrating criteria is essential.
**Theory Reference:** Domain 4.1 - Explicit prompt criteria

---

## Question 326
**Title:** Few-shot Examples for Ambiguous Extraction Patterns
**Situation:** Your data extraction system processes invoice documents with varying formats. Sometimes the "Total" field includes tax; sometimes it doesn't. Sometimes "Subtotal" appears; sometimes the calculation must be inferred. Extraction accuracy is 82% with many ambiguous cases being misclassified.
**Options:**
- A) Add generic guidance: "Be careful to extract totals accurately"
- B) Increase the number of tools available to the extraction system
- C) Implement a validation layer that flags uncertain extractions
- D) Provide 2–4 targeted few-shot examples showing correct extraction for different document structures with rationale explaining why each classification is correct

**Correct Answer:** D
**Feedback:** Few-shot examples are the most effective method for handling ambiguous cases. Provide 2–4 targeted examples demonstrating correct extraction from documents with different structures, including rationale for each decision. Few-shot helps the model generalize to new patterns and reduces hallucinations in extraction tasks.
**Theory Reference:** Domain 4.2 - Few-shot prompting for ambiguous scenarios

---

## Question 327
**Title:** Validation Retry with Error Feedback for Extraction Correction
**Situation:** Your extraction system processes contracts. Initial extraction yields `contract_value: 50000` but validation detects an error: "Stated total is 50,000 but calculated from line items sums to 48,500." A retry could correct this. However, the system needs to determine if the discrepancy indicates the wrong field was extracted or if it's genuinely ambiguous in the source document.
**Options:**
- A) Retry multiple times with different instructions until a match is found
- B) Mark as failed and escalate to human without retry
- C) Follow-up prompt with the original document, the incorrect extraction, specific validation errors, and details of what was calculated vs stated to guide correction
- D) Retry immediately with generic instruction "Fix the extraction error"

**Correct Answer:** C
**Feedback:** Retry-with-error-feedback provides concrete validation errors and context in the follow-up prompt. Include the original document, incorrect extraction, specific discrepancies (stated vs calculated totals), and ask the model to reconcile. This guides correction without requiring human intervention. Retries are ineffective without concrete feedback about what failed.
**Theory Reference:** Domain 4.4 - Validation, retries, and feedback loops

---

## Question 328
**Title:** Independent Review Instances vs Self-review
**Situation:** Your code generation system produces changes to 8 files implementing a new authentication flow. The same model that generated the code is asked to review its own changes. It identifies 2 issues. An independent Claude instance (without generation context) reviewing the same changes identifies 7 issues. Which approach is more reliable?
**Options:**
- A) Both approaches catch the same issues; use whichever is more convenient
- B) Self-review is sufficient; having the same model review is efficient
- C) Independent review instances without generation context find more subtle issues; they are better at challenging the original reasoning
- D) Self-review catches most issues; independent review is redundant

**Correct Answer:** C
**Feedback:** Self-review has limitations: the model retains its reasoning context and is less likely to challenge its own decisions. Independent review instances (without generation context) are significantly better at finding subtle issues. They approach the code with fresh perspective and challenge design decisions the original model took for granted.
**Theory Reference:** Domain 4.6 - Multi-instance review for better detection

---

## Question 329
**Title:** Field-level Confidence Calibration for Human Oversight
**Situation:** Your extraction system reports 97% overall accuracy across all fields (contract date, value, parties, terms). However, when you validate by field type: contract value shows 94% accuracy, date extraction shows 99%, party names show 91%. You're considering automating all extractions.
**Options:**
- A) Retry extraction for all fields to improve overall accuracy
- B) Implement additional validation rules for all fields equally
- C) Proceed with automation; 97% overall accuracy is acceptable
- D) Analyze accuracy by field segment and route low-confidence categories (e.g., party names at 91%) to human review; automate high-confidence fields (dates at 99%)

**Correct Answer:** D
**Feedback:** Aggregate metrics like "97% overall accuracy" mask poor performance on specific fields. Field-level analysis reveals that party name extraction (91%) is less reliable than date extraction (99%). Route low-confidence fields to human review while automating high-confidence ones. This stratified approach balances efficiency with accuracy.
**Theory Reference:** Domain 5.5 - Confidence calibration and human oversight

---

## Question 330
**Title:** Stratified Sampling for Detecting New Error Patterns
**Situation:** Your extraction system claims high confidence on 85% of extractions with an audit showing 98% accuracy on high-confidence items. Low-confidence extractions (15%) show 76% accuracy. You want to validate that high-confidence extractions remain stable as you add new document types.
**Options:**
- A) Focus validation effort only on low-confidence extractions
- B) Audit all high-confidence extractions since they're most numerous
- C) Trust the metrics; high-confidence extractions are clearly more accurate
- D) Implement stratified random sampling: sample high-confidence AND low-confidence extractions to measure error rates separately and detect new error patterns in high-confidence categories

**Correct Answer:** D
**Feedback:** Stratified random sampling measures error rates across confidence levels separately. This detects when new document types introduce errors even in high-confidence extractions. Trusting aggregate metrics (C) misses emerging patterns. Focusing only on low-confidence items (A) fails to catch degradation in the larger high-confidence pool.
**Theory Reference:** Domain 5.5 - Stratified sampling for error detection

---
