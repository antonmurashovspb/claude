# Quiz Question Bank - Batch 25 (Questions 241-250)

## Question 241
**Title:** Context Window Management with Large Tool Result Sets
**Situation:** Your research coordinator delegates a task to a subagent which returns 5 tool calls with detailed results: API response (12K tokens), database query (8K tokens), file content (15K tokens), metadata (3K tokens), and error logs (2K tokens). The coordinator needs only 3 key findings from the combined results.
**Options:**
- A) Have the subagent summarize to key findings (3-5 bullet points) before returning to coordinator
- B) Pass all 40K tokens to the coordinator; it has enough context to filter
- C) Split results across multiple coordinator requests to reduce per-request size
- D) Use vector embeddings to compress the results automatically

**Correct Answer:** A
**Feedback:** Subagents should provide structured summaries before returning to the coordinator. Raw 40K-token outputs waste context and increase coordinator processing time. Summarization at the source (3-5 key points) enables the coordinator to synthesize efficiently from concise data.
**Theory Reference:** Domain 1.2 - Coordinator result aggregation with context efficiency

---

## Question 242
**Title:** Precondition Enforcement vs Prompt Guidance for Critical Workflows
**Situation:** Your financial system processes refunds. Business rule: verify customer identity before processing refund, enforce this rule without prompts if possible. Two approaches: (1) system prompt instruction "always verify identity first", (2) programmatic precondition blocking `process_refund` until `verify_identity` completes successfully.
**Options:**
- A) Implement programmatic precondition; it guarantees compliance without relying on model inference
- B) System prompt is sufficient; the model understands the criticality
- C) Both approaches together for redundancy
- D) Use few-shot examples to guide the model

**Correct Answer:** A
**Feedback:** Critical business logic requires deterministic guarantees. Prompts are probabilistic—the model might skip verification on edge cases or under token pressure. Programmatic preconditions are deterministic: the tool is blocked until the prior step succeeds, regardless of model reasoning.
**Theory Reference:** Domain 1.4 - Programmatic enforcement vs prompt guidance

---

## Question 243
**Title:** Tool Naming Impact on Agent Selection Reliability
**Situation:** Your document processing system has two overlapping tools: `extract_text` and `read_content`. Agents are split roughly 50-50 between both tools for the same task. Both have identical descriptions. You suspect the tool names are causing confusion.
**Options:**
- A) Rename to more specific names like `extract_text_from_pdf` and `read_html_content`, aligning names to use cases
- B) Add few-shot examples to the system prompt
- C) Merge into single tool with a format parameter
- D) Improve descriptions since naming has minimal impact

**Correct Answer:** A
**Feedback:** Tool names are the first signal agents encounter. Ambiguous names like `extract_text` and `read_content` create selection confusion. Specific, action-oriented names (`extract_text_from_pdf`, `read_html_content`) are self-describing and prevent misrouting without additional prompt engineering.
**Theory Reference:** Domain 2.1 - Tool naming clarity

---

## Question 244
**Title:** Error Response Structuring for Intelligent Recovery
**Situation:** A tool call fails with error message: "Request failed." The coordinator receives this and cannot determine if the failure is transient (network timeout) or permanent (invalid input). Should the coordinator retry, modify the request, or escalate?
**Options:**
- A) Coordinator should retry with exponential backoff regardless
- B) Tool should return structured error metadata: category, retryability, partial results, failed query details
- C) Coordinator should immediately escalate to a human
- D) Tool should provide plain text explanation of what went wrong

**Correct Answer:** B
**Feedback:** Structured error responses enable intelligent recovery. Include: `errorCategory` (transient vs non-retryable), `isRetryable` boolean, partial results if available, and details of the failed request. This lets the coordinator decide: retry, modify query, use partial results, or escalate.
**Theory Reference:** Domain 2.2 - Structured error responses

---

## Question 245
**Title:** Prompt Chaining vs Single Comprehensive Prompt
**Situation:** Your system analyzes code for (1) structural issues, (2) performance bottlenecks, (3) security vulnerabilities, (4) maintainability concerns. You can either: (1) one prompt covering all aspects, or (2) four sequential prompts, each focused on one aspect.
**Options:**
- A) Single comprehensive prompt reduces token usage and API calls
- B) Four sequential prompts; each focused prompt produces deeper analysis despite higher token cost
- C) Use different models for each aspect to parallelize
- D) Alternate between single and sequential based on code complexity

**Correct Answer:** B
**Feedback:** Prompt chaining (sequential, focused prompts) produces better results than one comprehensive prompt. When a prompt juggles multiple concerns, attention dilutes and quality suffers. Focused analysis of structure → performance → security → maintainability yields richer insights despite higher API call count.
**Theory Reference:** Domain 1.6 - Prompt chaining for complex workflows

---

## Question 246
**Title:** Specialized Tools vs General Tools with Parameters
**Situation:** You're designing validation tools for a platform. Option 1: specialized tools `validate_email`, `validate_phone`, `validate_credit_card`. Option 2: single tool `validate_input` with a `type` parameter. Agents frequently misroute to the general tool.
**Options:**
- A) Both are equally reliable; choose based on code organization preferences
- B) Specialized tools are more reliable; agents better distinguish between distinct tool names than parameter combinations
- C) General tool with parameter is simpler and agents learn the parameter names quickly
- D) Use a routing classifier to map input types to specialized tools

**Correct Answer:** B
**Feedback:** Agents struggle with parameter combinations in general tools. They reliably distinguish between specialized tool names like `validate_email` vs `validate_phone` vs `validate_credit_card`. Specialization reduces selection ambiguity and improves accuracy.
**Theory Reference:** Domain 2.3 - Tool specialization for reliability

---

## Question 247
**Title:** Subagent Tool Access and Least Privilege Principle
**Situation:** Your multi-agent system has a global tool registry with 40+ tools: database queries, APIs, reporting, authentication, notifications. A subagent researching customer feedback needs only 5 specific tools. Should the subagent have access to all 40 tools?
**Options:**
- A) Yes, give full access so the subagent has flexibility
- B) No, restrict to the 5 required tools per least privilege principle; fewer tools reduces context and improves reliability
- C) Give subagent 20 tools and let it choose
- D) Create a separate subagent with all tools available

**Correct Answer:** C
**Feedback:** Proper implementation of least privilege restricts subagents to tools they need. Broad access increases context waste and reduces accuracy. However, the balanced approach considers task complexity; providing 20 tools allows flexibility while still limiting bloat compared to 40.
**Theory Reference:** Domain 2.3 - Allocating tools across agents

---

## Question 248
**Title:** Session Forking for Comparative Analysis
**Situation:** You're designing a system refactor with two candidate approaches. Approach A modularizes by feature; Approach B modularizes by layer. You explore approach A in detail (50+ files analyzed), then want to compare approach B while preserving your A analysis context.
**Options:**
- A) Restart analysis with approach B in a new session, losing approach A context
- B) Fork the current session to explore approach B independently while keeping A analysis accessible
- C) Use resume to reload context after B analysis concludes
- D) Have two separate agents analyze approaches in parallel

**Correct Answer:** C
**Feedback:** `fork_session` creates independent branches, but when both approaches are sequential, `--resume` is more efficient for specific scenarios. You can resume the A session later, analyze B in a fresh session, then compare results. Choose based on whether context is still valid or needs refresh.
**Theory Reference:** Domain 1.7 - Session management strategies

---

## Question 249
**Title:** PostToolUse Hooks vs PreToolUse Hooks for Data Transformation
**Situation:** Your system uses three third-party APIs that return timestamps in different formats: Unix epoch, ISO 8601, and human-readable strings. The agent needs consistent timestamp format. Where should normalization occur?
**Options:**
- A) PreToolUse hook to normalize inputs before each tool call
- B) PostToolUse hook to normalize outputs after each tool call, before agent processes results
- C) In the system prompt with explicit conversion instructions
- D) Create wrapper tools that handle normalization internally

**Correct Answer:** D
**Feedback:** Creating wrapper tools encapsulates normalization logic and provides a single transformation layer. While PostToolUse hooks are effective, wrapper tools offer better maintainability and reusability across multiple coordinators or contexts. They centralize transformation logic in one place.
**Theory Reference:** Domain 1.5 - Data transformation patterns

---

## Question 250
**Title:** Skill Tool Restrictions with allowed-tools Constraint
**Situation:** You create a `/code-review` skill that should only perform analysis (no code modifications). You restrict the skill's access to linting and analysis tools via the `allowed-tools` field in SKILL.md. A developer tries to invoke the skill, asking it to fix issues. What happens?
**Options:**
- A) The skill generates fix suggestions because the system prompt encourages it
- B) The system warns the developer but allows the modification
- C) The skill generates text suggestions but cannot execute them automatically
- D) The skill cannot access modification tools; the architectural constraint prevents access regardless of requests

**Correct Answer:** D
**Feedback:** The `allowed-tools` field in SKILL.md is an architectural constraint that prevents tool access at runtime. Even if the system prompt encourages fixes or the developer requests modifications, the skill cannot invoke modification tools—this constraint is enforced at the infrastructure level.
**Theory Reference:** Domain 3.2 - Skills with allowed-tools constraints
