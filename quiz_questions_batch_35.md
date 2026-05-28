# Quiz Question Bank - Batch 35 (Questions 341-350)

## Question 341
**Title:** CLAUDE.md Hierarchy: User vs Project Level Configuration
**Situation:** Your team has shared project-level instructions in `.claude/CLAUDE.md`, but a new team member still misses critical standards on their first day. You notice the standards are clear in the repo, yet the developer didn't follow them. Investigation reveals the developer has a personal `~/.claude/CLAUDE.md` with different conventions that overrides the project settings.
**Options:**
- A) User-level CLAUDE.md takes precedence over project-level settings; standards should be project-level to ensure team consistency
- B) Have each developer manually copy project standards into their home directory
- C) Use environment variables to enforce project standards across all users
- D) Create individual reminder documents for each developer

**Correct Answer:** A
**Feedback:** CLAUDE.md hierarchy is: user-level (home directory) > project-level (repository). User-level settings are not shared via VCS and take precedence. For team-wide standards, use `.claude/CLAUDE.md` (project-level) so all team members inherit the same conventions. User-level settings should be reserved for personal preferences that don't affect team collaboration.
**Theory Reference:** Domain 3.1 - Configuring CLAUDE.md with Hierarchy, Scope, and Modular Organization

---

## Question 342
**Title:** Modularizing Large CLAUDE.md with @path Syntax
**Situation:** Your project's root CLAUDE.md has grown to 3000+ lines covering API conventions, testing standards, database schemas, and deployment procedures. Developers say the file is hard to navigate, and CI checks are slower because the agent loads all conventions for every file edit, even when working on unrelated components.
**Options:**
- A) Split the monolithic file into `.claude/rules/api.md`, `.claude/rules/testing.md`, `.claude/rules/deployment.md` and reference them with `@path` syntax in the main CLAUDE.md to load selectively
- B) Keep everything in one file but add table-of-contents anchors for navigation
- C) Create separate CLAUDE.md files in each subdirectory instead of using @path
- D) Reduce the file to only the most critical conventions and remove detailed sections

**Correct Answer:** A
**Feedback:** The `@path` syntax allows modularizing CLAUDE.md by including external files (e.g., `@./standards/api.md`). This keeps conventions organized by topic and is more maintainable than a monolithic file. It also allows selective loading: when working on tests, load `testing.md`; when working on API code, load `api.md`. This saves context tokens and improves developer experience. Option C duplicates conventions across the repo. Option D loses important guidance.
**Theory Reference:** Domain 3.1 - Configuring CLAUDE.md with Hierarchy, Scope, and Modular Organization

---

## Question 343
**Title:** Path-specific Rules with Glob Patterns for Test Conventions
**Situation:** Your repository uses Jest for tests spread across `src/`, `lib/`, `api/`, and `utils/` directories. Each location needs identical async/await patterns and mocking setup. Maintaining separate CLAUDE.md files in 20+ directories is error-prone and duplicates conventions. You want a single source of truth for test standards.
**Options:**
- A) Create a `.claude/rules/testing.md` with `paths: ["**/*.test.ts"]` to apply rules only when editing matching test files, regardless of location
- B) Create root CLAUDE.md with test conventions and rely on developers to apply them selectively
- C) Maintain CLAUDE.md files in each directory with identical conventions
- D) Store test conventions in a shared documentation wiki external to the repository

**Correct Answer:** A
**Feedback:** Path-specific rules with glob patterns are designed for this exact scenario. A `.claude/rules/testing.md` with `paths: ["**/*.test.ts"]` loads only when editing files matching that glob, saving context and ensuring consistency across the entire codebase. This is more maintainable than directory-level CLAUDE.md files (which require duplication and are fragile) and more reliable than relying on developers to apply root CLAUDE.md rules selectively. External documentation (D) is harder to keep in sync with code.
**Theory Reference:** Domain 3.3 - Using Path-specific Rules for Conditional Convention Loading

---

## Question 344
**Title:** Choosing Between Path-specific Rules and Directory CLAUDE.md
**Situation:** Your data pipeline has test utilities in `utils/testing/`, data extraction in `data/extraction/`, and API handlers in `api/handlers/`. Each subsystem needs different testing patterns. You want to minimize context overhead when editing files and avoid maintaining many configuration files.
**Options:**
- A) Create CLAUDE.md in each subsystem directory to keep conventions localized and self-contained
- B) Use glob-based path-specific rules in `.claude/rules/` when conventions span many directories or apply by file type rather than just directory structure
- C) Store all conventions in the root CLAUDE.md and update them centrally
- D) Let each developer maintain their own conventions in their personal `~/.claude/CLAUDE.md`

**Correct Answer:** B
**Feedback:** Path-specific rules in `.claude/rules/` are preferable to directory-level CLAUDE.md when conventions apply across multiple locations via glob patterns (e.g., `**/*.integration.test.ts`) or when the same pattern recurs throughout the codebase. This approach is more maintainable and contextually efficient. Directory-level CLAUDE.md is appropriate only when conventions are genuinely localized to a single subsystem. Root CLAUDE.md leads to attention dilution. Personal configurations (D) hurt team consistency.
**Theory Reference:** Domain 3.3 - Using Path-specific Rules for Conditional Convention Loading

---

## Question 345
**Title:** Independent Review Instances vs Self-review for Finding Subtle Issues
**Situation:** Your code review system generates code changes and then asks the same Claude instance to review those changes for bugs. Reviewers report that the system consistently misses subtle issues like off-by-one errors in loops, even though the issues are present in the generated code. Using the same instance for both generation and review has a hidden cost.
**Options:**
- A) Add more detailed review criteria and examples to the system prompt to improve detection
- B) Use a second, independent Claude instance (without access to generation context) to review changes, which is better at challenging assumptions and finding subtle issues missed in self-review
- C) Implement a voting system where 3 independent reviewers must agree before flagging issues
- D) Use static analysis tools instead of LLM-based review

**Correct Answer:** B
**Feedback:** Self-review has a fundamental limitation: the model retains its reasoning context from generation and is less likely to challenge its own decisions. An independent review instance (without generation context) approaches the code with fresh eyes and is better at finding subtle issues. This is a core architectural principle for multi-instance review. Adding more criteria (A) doesn't address the root problem of retained reasoning context. Voting (C) is expensive; independent instances are more efficient. Static analysis (D) can't detect logical errors only an LLM can identify.
**Theory Reference:** Domain 4.6 - Designing Multi-instance and Multi-pass Review Architectures

---

## Question 346
**Title:** Multi-pass Review: Local Analysis and Cross-file Integration
**Situation:** You're reviewing a 15-file refactoring where changes span data models, API handlers, and frontend state management. A single Claude instance reviewing all changes at once produces scattered findings: some files are thoroughly analyzed, others receive only surface-level comments. Cross-file dataflow analysis is missed entirely. Your review quality varies by position in the file list.
**Options:**
- A) Ask developers to submit smaller pull requests so all changes fit in a single review context
- B) Use a multi-pass architecture: per-file local analysis in one pass, then a separate cross-file integration pass that verifies dataflow, API contracts, and state mutations across files
- C) Increase the context window size so the model can see all files simultaneously
- D) Use vector embeddings to detect cross-file relationships automatically

**Correct Answer:** B
**Feedback:** Multi-pass review (per-file local analysis + cross-file integration) is the recommended architecture for complex refactorings. Each pass has a focused goal: local pass finds per-file issues, integration pass verifies that changes work together correctly. This avoids attention dilution. Smaller PRs (A) reduce scope but don't solve the cross-file analysis problem. Larger context windows (C) don't solve attention dilution. Vector embeddings (D) can't reason about code semantics.
**Theory Reference:** Domain 4.6 - Designing Multi-instance and Multi-pass Review Architectures

---

## Question 347
**Title:** Stratified Random Sampling for Validating Extraction Accuracy
**Situation:** Your document extraction system reports 97% accuracy overall. However, after reviewing a random sample of extracted financial documents, you discover the system consistently fails on a specific document format (tabular layouts), while performing well on prose. Aggregate accuracy masked poor performance in a subset, risking errors in production.
**Options:**
- A) Trust the 97% aggregate accuracy; statistically, it's reliable enough for production use
- B) Implement stratified random sampling by document type and field to detect error patterns in subsets; validate stable performance before automating
- C) Increase the model's accuracy by retraining or fine-tuning
- D) Require human review of all documents before using extracted data

**Correct Answer:** C
**Feedback:** Aggregate metrics like "97% overall accuracy" can mask poor performance on specific document types or fields. Stratified random sampling—dividing the population by type or field and checking each subset—reveals error patterns that aggregate metrics hide. This is critical before automating. Option A relies on insufficient evidence. Option B is reactive; you need systematic sampling before deployment. Option D is overly cautious if validation shows stable performance. Option C is the most proactive approach when you've identified the problem.
**Theory Reference:** Domain 5.5 - Designing Workflows with Human Oversight and Confidence Calibration

---

## Question 348
**Title:** Field-level Confidence Calibration for Automated Review Routing
**Situation:** Your extraction system flags some fields as high-confidence and some as low-confidence, but you notice that high-confidence extractions still have errors on certain field types (dates with ambiguous formats, currency amounts in non-standard formats). Sending all high-confidence results to automation causes silent failures. You need to detect when confident-looking extractions are actually unreliable.
**Options:**
- A) Disable automation and require manual review of all extractions to ensure 100% correctness
- B) Use a second model to verify results from the first model
- C) Ask the model to double-check its own confidence scores before automation
- D) Output field-level confidence scores and calibrate review thresholds using labeled validation data; route low-confidence or ambiguous extractions to human review

**Correct Answer:** D
**Feedback:** Field-level confidence calibration using labeled validation data is the standard approach. By analyzing which field types have errors despite high confidence, you can lower thresholds for those fields (e.g., require 95% confidence for dates, 80% for standard amounts). This routes ambiguous cases to human review while automating reliable ones. Option A loses efficiency. Option B (second model verification) adds cost without solving calibration. Option C (self-verification) doesn't address the root problem—the model's confidence is already miscalibrated.
**Theory Reference:** Domain 5.5 - Designing Workflows with Human Oversight and Confidence Calibration

---

## Question 349
**Title:** Preserving Claim-to-Source Mappings in Multi-source Synthesis
**Situation:** Your system aggregates research findings from three sources: a government report, an academic paper, and news articles. The coordinator synthesizes the findings into a summary. Later, a stakeholder asks "where did you get the statistic about 45% increase?" You realize the summary statement is present, but you've lost track of which source it came from—attribution was lost during synthesis.
**Options:**
- A) Store raw extracts from all sources and let stakeholders browse the original documents themselves
- B) Add footnotes with source names but not specific quotes or evidence
- C) Require subagents to output "claim → source" mappings (including URL, document name, direct quotes); preserve these mappings during aggregation so attribution survives synthesis
- D) Ask the model to remember which source each claim came from based on context

**Correct Answer:** C
**Feedback:** Attribution is lost during summarization unless "claim → source" mappings are explicitly preserved. Subagents should output structured mappings: e.g., `{"claim": "45% increase", "source": "report.pdf", "quote": "...45% increase..."}`. The coordinator preserves these during synthesis, so final reports include full provenance. Option A shifts responsibility to stakeholders. Option B (footnotes only) lacks specificity needed for verification. Option D relies on model memory, which is unreliable for multi-source synthesis.
**Theory Reference:** Domain 5.6 - Preserving Provenance and Handling Uncertainty in Multi-source Synthesis

---

## Question 350
**Title:** Handling Conflicting Statistics in Multi-source Research
**Situation:** Your multi-source research system finds conflicting statistics: Source A reports "unemployment is 4.8%" (January 2024), Source B reports "unemployment is 5.2%" (March 2024). You must decide how to handle this. One approach treats it as contradictory data; another recognizes a temporal pattern between measurements.
**Options:**
- A) Choose the statistic from the most authoritative source and discard alternatives
- B) Average both statistics to get a middle value representing the data range
- C) Preserve both values and include dates and sources for coordinator reconciliation
- D) Flag as a conflict and escalate immediately for human judgment

**Correct Answer:** D
**Feedback:** When sources conflict critically, escalation ensures proper human judgment on reconciliation. Simply preserving data (C) may work for minor variations, but temporal conflicts warrant review. Choosing one source (A) loses information. Averaging (B) obscures differences. Escalation is appropriate when temporal context matters for decision-making, ensuring stakeholder confidence in data handling.
**Theory Reference:** Domain 5.6 - Preserving Provenance and Handling Uncertainty in Multi-source Synthesis
