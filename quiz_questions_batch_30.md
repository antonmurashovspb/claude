# Quiz Question Bank - Batch 30 (Questions 261-270)

## Question 261
**Title:** Test-Driven Iteration for Specification Clarity
**Situation:** Your code generation system must transform data from format A to format B. You've written a 500-word specification document explaining the transformation rules. Claude Code frequently produces correct logic but with formatting errors: wrong indentation, missing newlines, inconsistent naming. Adding more words to the specification doesn't help.
**Options:**
- A) Provide 2–3 concrete input/output examples showing exact expected format, then iterate with Claude Code based on test failures
- B) Increase the specification length to 1,500 words with more detailed formatting rules
- C) Add system prompt instructions about formatting conventions
- D) Split the task: have one prompt for logic, another for formatting

**Correct Answer:** A
**Feedback:** Concrete input/output examples communicate expectations more effectively than written specifications. Test-driven iteration (write tests first, iterate based on failures) reveals which requirements are misunderstood. With 2–3 examples and test feedback, Claude Code converges quickly. Additional words add noise without clarity.
**Theory Reference:** Domain 3.5 - Iterative refinement with concrete input/output examples and test-driven iteration

---

## Question 262
**Title:** Explicit Criteria to Reduce False Positives
**Situation:** Your automated security review flags 400 potential issues per week, but manual analysis shows 85% are false positives (static patterns that don't indicate real vulnerabilities). Your team has stopped reviewing output because trust is lost. The prompt uses vague guidance like "flag suspicious code patterns" and "look for potential issues."
**Options:**
- A) Define explicit categorical criteria: "flag SQL injections only when user input reaches SQL without parameterized queries; ignore prepared statements, ORMs, and string templates"
- B) Add more examples to the review prompt
- C) Switch to a different static analysis tool
- D) Accept that automated review will always have high false-positive rates

**Correct Answer:** A
**Feedback:** Vague guidance like "look for potential issues" triggers false positives. Explicit categorical criteria with concrete boundaries (e.g., "user input reaching SQL unparameterized") directly address the root cause. This reduces false positives from 85% to 10–15%, restoring team trust. Examples without categorical criteria amplify false positives.
**Theory Reference:** Domain 4.1 - Explicit prompt criteria versus vague guidance to reduce false-positive rates

---

## Question 263
**Title:** Few-shot Examples for Consistent Extraction Output
**Situation:** Your document extraction system extracts line items from invoices. Without few-shot examples, output is inconsistent: descriptions vary from 5 to 50+ words; dates appear as "2025-01-15", "Jan 15, 2025", and "January 15"; amounts sometimes include tax, sometimes don't. Developers waste time normalizing formats.
**Options:**
- A) Provide 2–3 few-shot examples of complete invoice extractions with correct formatting, then iterate based on failing cases
- B) Add more detailed system prompt instructions about formatting
- C) Implement post-processing validation to normalize formats programmatically
- D) Use JSON Schemas to enforce structure without providing examples

**Correct Answer:** A
**Feedback:** Few-shot examples are the most effective method for producing consistent, formatted output. Showing 2–3 complete examples (input invoice → extracted JSON with exact date format, exact description length, amounts excluding tax) teaches the pattern better than words. Instructions add complexity without demonstrating the pattern. Post-processing treats symptoms. Schemas alone don't ensure format consistency.
**Theory Reference:** Domain 4.2 - Few-shot prompting for consistent output format and handling ambiguous cases

---

## Question 264
**Title:** Skill Configuration with argument-hint Field
**Situation:** You create a `/refactor` skill that developers invoke with a code snippet. Without guidance, developers struggle with how to specify which aspect to refactor: performance, readability, or API design. You need the skill to prompt for clarity without requiring documentation.
**Options:**
- A) Document the skill usage in README and hope developers read it
- B) Add an `argument-hint` field in the skill's SKILL.md frontmatter that prompts developers for specific parameters (e.g., "Refactor for: [performance|readability|API]")
- C) Make the skill very generic so developers can phrase requests naturally
- D) Split into three separate skills: `/refactor-performance`, `/refactor-readability`, `/refactor-api`

**Correct Answer:** B
**Feedback:** `argument-hint` in SKILL.md frontmatter automatically prompts developers for required parameters when they invoke the skill. This improves clarity without extra documentation or creating many skills. Documentation requires discipline. Generic skills create ambiguity. Multiple skills increase cognitive load.
**Theory Reference:** Domain 3.2 - Skills configuration with argument-hint field for developer experience

---

## Question 265
**Title:** Using Explore Subagent to Prevent Context Exhaustion
**Situation:** You're investigating a 200-file codebase to determine how to add a new feature. Your main agent starts reading files and accumulates 100K tokens of discovery output (class hierarchies, dependencies, file cross-references). The agent's responses become unfocused and it begins referring to "typical patterns" instead of specific classes from the code.
**Options:**
- A) Summarize findings manually into a scratchpad file before continuing
- B) Use an Explore subagent to perform discovery in isolated context, then provide the main agent with a concise architecture summary and key findings
- C) Use the `/compact` command to reduce context after every 5 files
- D) Switch to a model with a larger context window

**Correct Answer:** B
**Feedback:** Explore subagent isolates verbose discovery output in independent context, preventing exhaustion in the main session. The subagent explores freely (100K tokens in its context), then the main agent receives a concise 5K-token summary. This preserves focus while enabling thorough investigation. Manual summarization is a workaround. `/compact` provides temporary relief. Larger context delays the issue.
**Theory Reference:** Domain 3.4 - Using Explore subagent to prevent context-window exhaustion

---

## Question 266
**Title:** Incremental Re-review for CI Integration
**Situation:** Your CI system runs Claude Code to review code quality after each commit. On the first commit, Claude flags 8 issues. Developer fixes 5 issues and pushes a new commit. You re-run the review and want Claude to flag only the 3 remaining unfixed issues plus any new issues introduced by the fix, not repeat the already-fixed ones.
**Options:**
- A) Run a fresh review on each commit; developers compare results to see which were fixed
- B) Include prior review results when re-running after commits, with the directive "report only new or unfixed issues" and exclude already-fixed ones
- C) Maintain a separate database of flagged issues and filter duplicates programmatically
- D) Require developers to manually mark which issues they fixed

**Correct Answer:** B
**Feedback:** CI re-runs should include prior review results with the directive "report only new or unfixed issues." This avoids redundant flagging while catching new problems introduced by the fix. Manual comparison is inefficient. Database filtering adds infrastructure complexity. Shifting burden to developers reduces adoption.
**Theory Reference:** Domain 3.6 - Incremental re-review in CI/CD pipelines (report only new issues)

---

## Question 267
**Title:** Batch Processing with custom_id for Document Correlation
**Situation:** Your system processes 10,000 documents daily through the Message Batches API (processing completes in 2–24 hours with no guaranteed SLA). You submit batches daily but want to resubmit only failed documents if a batch partially fails. The problem: response payloads are large and you cannot easily match which responses correspond to which documents.
**Options:**
- A) Store all results in a database and manually query which documents failed
- B) Resubmit the entire batch if any document fails
- C) Use `custom_id` fields in batch requests to correlate each request with its source document, enabling selective resubmission of only failed `custom_id`s
- D) Split documents into smaller batches (100 each) to reduce resubmission overhead

**Correct Answer:** C
**Feedback:** The Message Batches API allows you to set a `custom_id` field for each request. When processing results, you identify failed documents by their `custom_id`, then resubmit only those documents in the next batch. This saves 90%+ of API costs compared to resubmitting all 10,000 documents. Manual database tracking is error-prone. Resubmitting everything wastes resources. Smaller batches don't solve correlation.
**Theory Reference:** Domain 4.5 - Batch processing with custom_id for correlation and selective resubmission

---

## Question 268
**Title:** Policy-Gap Escalation Criteria in System Prompt
**Situation:** Your support agent handles refund requests with a clear policy: "Refund if item returned within 30 days." Today a customer requests a refund after 45 days with a compelling reason (item was defective but discovered after 30 days). The policy is silent on defective items discovered late. The agent attempts to reason through the exception autonomously.
**Options:**
- A) Trust the agent's judgment; it has good context and reasoning ability
- B) Let the agent try to resolve it and escalate only if it seems uncertain
- C) Train the agent with few-shot examples of policy-gap cases and explicit escalation criteria: "Escalate immediately if the request involves a policy gap (clear policy + plausible exception rationale)"
- D) Update the policy document to be exhaustive and cover all edge cases

**Correct Answer:** C
**Feedback:** Explicit escalation criteria with few-shot examples teach the agent to recognize policy gaps (clear rule + plausible exception) and escalate immediately rather than reasoning through exceptions autonomously. This prevents risky judgment calls. Trusting agent reasoning is unreliable. Uncertainty is vague and unreliable. Exhaustive policies are impossible to maintain.
**Theory Reference:** Domain 5.2 - Policy-gap escalation trigger with few-shot criteria in system prompt

---

## Question 269
**Title:** Self-Rated Confidence for Routing to Human Review
**Situation:** Your extraction system processes invoices. Some extractions are high-confidence (clear, well-formatted invoices), others are low-confidence (handwritten sections, blurry text, ambiguous line items). Currently, all extractions go to the same manual review queue. Review time per invoice is unpredictable—some take 2 minutes, others 30 minutes.
**Options:**
- A) Use sentiment analysis or heuristic uncertainty measures to guess confidence
- B) Increase the model's temperature to make uncertainty more visible
- C) Measure accuracy on a labeled test set and use field-level confidence scores as routing criteria
- D) Have the model self-rate its extraction confidence (1–10) and route low-confidence items to human review first

**Correct Answer:** D
**Feedback:** The model can self-rate confidence during extraction (explicitly ask "How confident are you in this field?"). While self-rated confidence can be overconfident, it captures the model's own uncertainty better than heuristics. Use this to route low-confidence items to human review first, improving reviewer efficiency. Sentiment analysis is indirect. Temperature is indirect. Labeled test sets are superior but require setup effort.
**Theory Reference:** Domain 4.6 - Self-rated confidence for routing to human review

---

## Question 270
**Title:** Content Rendering by Type in Multi-source Synthesis
**Situation:** Your research system synthesizes findings from three sources: financial data (stock prices, trading volume), news articles (company announcements, market sentiment), and technical documentation (API specifications, deployment requirements). The synthesis produces a single flat text report mixing all types equally. Financial stakeholders complain numbers are buried in prose; technical architects complain API details are too verbose; journalists want narrative context preserved.
**Options:**
- A) Create separate reports for each stakeholder
- B) Use a generic markdown format and let stakeholders parse it themselves
- C) Render content by type: financial data as structured tables, news as narrative prose, technical findings as structured lists with code snippets
- D) Ask subagents to produce separate output for each data type

**Correct Answer:** D
**Feedback:** Different content types require different rendering approaches. Financial data is most useful as tables (columns: metric, value, trend). News narratives work best as prose. Technical findings need structured lists with code examples. This respects each audience's needs and reduces post-processing. Separate reports are inefficient. Generic markdown ignores preferences. Rendering by type in a coordinator is more efficient than asking subagents to produce separate outputs.
**Theory Reference:** Domain 5.6 - Rendering content by type (financial → tables, news → prose, technical → structured lists)
