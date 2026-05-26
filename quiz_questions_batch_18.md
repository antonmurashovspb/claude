# Quiz Question Bank - Batch 18 (Questions 181-190)

## Question 181
**Title:** Explicit Context for Subagent Reviews
**Situation:** Your coordinator asks a review subagent, "Check whether the migration plan covers rollback," after earlier agents produced schema risks and rollout constraints. The review subagent misses a known replication issue because none of the prior findings were included in its prompt.
**Options:**
- A) Pass the prior findings, rollout constraints, and review criteria directly in the Task prompt
- B) Keep prompts brief and let the subagent infer the missing context from the coordinator history
- C) Put the missing findings in the system prompt so every future subagent reuses them automatically
- D) Ask the subagent to start broad research again and independently rebuild the earlier analysis

**Correct Answer:** A
**Feedback:** Subagents do not inherit the coordinator's history automatically, so the needed findings must be passed explicitly in the Task prompt. B assumes shared context that does not exist. C is too blunt for task-specific evidence. D wastes time and can introduce inconsistent conclusions.
**Theory Reference:** Domain 1.3 - Explicit context passing for subagents

---

## Question 182
**Title:** Choosing Grep vs Glob in Code Investigation
**Situation:** You need to update every place that constructs `OrderSummary`, but you do not yet know which files reference that symbol. A teammate suggests searching for filenames first because the repository has many similarly named directories.
**Options:**
- A) Start with Read on likely files and manually scan for `OrderSummary` references
- B) Use Glob to find files whose names contain `OrderSummary`, then inspect only those
- C) Use Grep to find `OrderSummary` in file contents, then read the relevant files for context
- D) Use Edit on the first suspected file; failed matches will reveal the other call sites

**Correct Answer:** C
**Feedback:** Grep is the right starting point when you need to find a symbol in file contents across the codebase. Glob helps when you know a filename pattern, not when references may live anywhere. A is slow and incomplete. D starts modifying before you understand usage.
**Theory Reference:** Domain 2.5 - Grep for content search, Glob for file discovery

---

## Question 183
**Title:** Sharing Claude Code Standards with the Team
**Situation:** A new engineer clones the repo and Claude Code ignores the team's testing standards, even though they work on your machine. You discover the standards live only in `~/.claude/CLAUDE.md`.
**Options:**
- A) Add the standards to each engineer's user-level CLAUDE.md during onboarding
- B) Move the shared standards into project-level CLAUDE.md or `.claude/` files under version control
- C) Put the standards in commit templates so Claude can infer them from git history
- D) Keep the user-level file and add a system prompt reminding contributors to copy it locally

**Correct Answer:** B
**Feedback:** Team-wide instructions belong in project-level Claude Code files that are version-controlled and clone with the repository. A and D depend on manual copying, which is exactly what failed here. C does not make the standards available as active working instructions.
**Theory Reference:** Domain 3.1 - CLAUDE.md hierarchy and shared project scope

---

## Question 184
**Title:** Forcing Structured Output Across Multiple Schemas
**Situation:** Your extraction workflow exposes three schema-based tools for invoices, receipts, and purchase orders. With `tool_choice: "auto"`, the model sometimes replies in prose when confidence is low, breaking downstream parsing.
**Options:**
- A) Add stronger prose instructions telling the model JSON is preferred but optional
- B) Collapse all schemas into one wide tool so the model has fewer choices
- C) Keep `auto` and retry until the model eventually chooses one of the tools
- D) Set `tool_choice` to `"any"` so the model must pick a schema tool instead of text

**Correct Answer:** D
**Feedback:** `tool_choice: "any"` guarantees a tool call, which is what you need when downstream systems require structured output every time. A still allows text. B weakens schema clarity. C wastes retries without fixing the control setting that caused the failure.
**Theory Reference:** Domain 4.3 - Using `tool_choice: "any"` for guaranteed structured output

---

## Question 185
**Title:** Returning Partial Failure Context to a Coordinator
**Situation:** A search subagent queried eight sources, retrieved three, then hit a rate limit on the rest. The coordinator now needs to decide whether to retry later, switch sources, or continue with partial coverage.
**Options:**
- A) Return a generic "search failed" status so the coordinator does not overfit to one error
- B) Return the failure type, attempted queries, partial results, and possible alternatives
- C) Mark the task successful with the three sources so the workflow can keep moving
- D) Raise the raw exception and terminate the whole multi-agent run immediately

**Correct Answer:** B
**Feedback:** Structured error context lets the coordinator choose an intelligent recovery path: retry, reroute, or proceed with known gaps. A hides critical detail. C masks incomplete coverage as success. D throws away usable work even though partial results already exist.
**Theory Reference:** Domain 5.3 - Structured error propagation with partial results

---

## Question 186
**Title:** Enforcing Verification Before Financial Actions
**Situation:** A refund agent usually verifies identity before issuing credits, but in some rushed cases it calls `process_refund` right after `lookup_order`. The finance team says skipped verification is unacceptable even if it happens rarely.
**Options:**
- A) Block `process_refund` until verification data is present, using a programmatic precondition
- B) Add few-shot examples that show careful verification before each refund
- C) Ask the model to explain its reasoning before every financial action
- D) Route refund requests to a higher-capability model that is less likely to skip steps

**Correct Answer:** A
**Feedback:** When the consequence of failure is unacceptable, you need deterministic enforcement, not better prompting. A prevents the unsafe action entirely. B and C may improve behavior but cannot guarantee compliance. D changes the model, not the control mechanism.
**Theory Reference:** Domain 1.4 - Programmatic enforcement for required workflow order

---

## Question 187
**Title:** Reducing Exploratory Calls with MCP Resources
**Situation:** An internal support agent repeatedly burns latency calling list endpoints just to learn what datasets and tables exist before it can answer questions. The underlying systems are stable and well-documented.
**Options:**
- A) Give the agent more general API tools so it can explore the systems more flexibly
- B) Require the agent to cache every discovery result inside the conversation history
- C) Publish schemas and content catalogs as MCP resources the agent can read directly
- D) Force the agent to ask a human which data source to inspect before each request

**Correct Answer:** C
**Feedback:** MCP resources act like a map of available data, reducing exploratory tool calls and saving latency. A expands the search surface without solving discovery overhead. B bloats context. D adds humans where stable machine-readable context would work better.
**Theory Reference:** Domain 2.4 - MCP resources as content catalogs for context

---

## Question 188
**Title:** Loading Test Rules Only When Test Files Are Edited
**Situation:** Your repository keeps frontend tests, API tests, and integration tests in many directories. A single giant CLAUDE.md with all test rules bloats context even when Claude edits non-test files.
**Options:**
- A) Replace the test rules with one slash command developers run only when they remember
- B) Move test conventions into `.claude/rules/` files with `paths` patterns for test files
- C) Duplicate a directory-level CLAUDE.md beside every test file so rules load locally
- D) Keep the giant CLAUDE.md and rely on the model to ignore irrelevant sections

**Correct Answer:** B
**Feedback:** Path-scoped rules load only when matching files are being edited, which keeps context lean while still applying shared conventions across scattered test locations. A depends on memory. C is unmaintainable. D preserves the token waste you are trying to remove.
**Theory Reference:** Domain 3.3 - Path-specific rules for conditional loading

---

## Question 189
**Title:** Retrying Semantic Extraction Errors Effectively
**Situation:** A contract extractor returns valid JSON, but the `stated_total` does not match the sum of line items. The source document is available, and you want the next attempt to fix the semantic error rather than regenerate blindly.
**Options:**
- A) Retry with the document, prior output, and the exact validation error describing the mismatch
- B) Retry the same prompt unchanged because semantic errors usually self-correct on the second pass
- C) Loosen the schema so the extractor can omit totals when reconciliation is difficult
- D) Accept the first output and flag the mismatch only after downstream systems import it

**Correct Answer:** A
**Feedback:** Semantic retries work best when you provide concrete feedback about what failed, along with the original source and the prior extraction. B is blind retrying. C hides an important field instead of correcting it. D delays a known defect until after it can cause damage.
**Theory Reference:** Domain 4.4 - Retry with explicit validation feedback

---

## Question 190
**Title:** Preserving Exact Facts Through Long Summaries
**Situation:** During a two-hour incident review, the agent keeps compressing prior turns into summaries. By the final response, specific dates, percentages, and ticket IDs have been replaced with vague phrases like "recently" and "about half."
**Options:**
- A) Increase `max_tokens` so the agent can keep every raw tool result in the thread
- B) Ask the agent to write longer summaries so fewer exact values are lost
- C) Move the important details to the middle of each summary where they receive equal attention
- D) Maintain a separate structured facts block for exact numbers, dates, and identifiers

**Correct Answer:** D
**Feedback:** A dedicated facts block preserves high-value details that summaries tend to blur, especially numbers, dates, and identifiers. A grows context without solving summarization drift. B still risks losing precision. C ignores the lost-in-the-middle problem instead of mitigating it.
**Theory Reference:** Domain 5.1 - Preserving critical facts outside summarized history
