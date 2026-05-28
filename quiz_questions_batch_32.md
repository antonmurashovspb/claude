# Quiz Question Bank - Batch 32 (Questions 291-300)

## Question 291
**Title:** Scratchpad Files and Context Boundaries
**Situation:** You are investigating a large Python codebase (500+ files) to identify authorization vulnerabilities. After 2 hours of investigation across 40 files, the model starts producing vague answers like "typical patterns suggest this might be wrong" instead of citing specific class methods. You've already sent 180K tokens across the session.
**Options:**
- A) Create a scratchpad file documenting key findings (files with auth checks, identified gaps, CVE references) and reference it in subsequent queries to preserve findings across context boundaries
- B) Increase the model temperature to make responses less repetitive
- C) Use vector embeddings to index all 500 files and retrieve only the most relevant ones
- D) Switch to a new session with a full summary of findings and continue the investigation from there

**Correct Answer:** A
**Feedback:** Scratchpad files preserve structured findings across context boundaries, preventing context degradation symptoms. Unlike switching sessions (D), scratchpad files are instant and maintain investigation continuity. Vector embeddings (C) don't solve context window saturation. Temperature (B) doesn't address the root cause—context exhaustion.
**Theory Reference:** Domain 5.4 - Scratchpad files for context preservation

---

## Question 292
**Title:** Context Degradation Symptoms in Long Investigations
**Situation:** After 3 hours investigating a financial module, your agent produces this output: "Based on typical validation patterns, this number field probably needs range checks." When you ask for the specific class and method name, the agent cannot cite them, despite having referenced them 50 messages earlier in the session.
**Options:**
- A) This is a sign of context degradation—key information is being lost mid-context due to token accumulation. Use `/compact` or spawn subagents to restart with a summary
- B) Add more examples to the system prompt to improve model clarity
- C) The model lacks sufficient training data on financial modules; use a different model
- D) The agent needs better tool selection logic to avoid exploring irrelevant code paths

**Correct Answer:** A
**Feedback:** Context degradation symptoms include vague references to "typical patterns" instead of specific classes and inability to recall recently-cited information. `/compact` reduces context usage; subagents isolate verbose output. System prompt improvements (B) won't help when the issue is token saturation, not clarity.
**Theory Reference:** Domain 5.4 - Context degradation symptoms and remediation

---

## Question 293
**Title:** Delegating Large Codebases to Subagents
**Situation:** You need to analyze a 2000-file monolithic repository to plan a microservice migration. The main agent will get lost in the details. Your investigation will involve multiple phases: mapping service boundaries, identifying data flows, proposing splits.
**Options:**
- A) Spawn focused subagents for each phase (boundary mapping, dataflow analysis, split proposal), keeping high-level coordination in the main agent. Each subagent receives a scoped context with relevant findings
- B) Preprocess all 2000 files to extract only "important" modules, then analyze those
- C) Use a simpler model to handle volume, then verify with a stronger model afterward
- D) Use one large session with iterative queries to explore all 2000 files and build the plan incrementally

**Correct Answer:** A
**Feedback:** Subagents isolate verbose discovery output and prevent the main agent from being overwhelmed. Each subagent gets focused context for its specific question. Single large sessions (D) lead to context saturation. Preprocessing (B) risks losing important context. Model switching (C) doesn't scale.
**Theory Reference:** Domain 5.4 - Delegating to subagents for large codebases

---

## Question 294
**Title:** Project vs User Commands in Team Environment
**Situation:** Your team of 4 developers uses Claude Code. You've created a `/test-coverage` command that analyzes test coverage according to the team's standards. It should be available to all team members when they clone the repository, without each developer configuring it separately.
**Options:**
- A) Each developer should store the command in their personal `~/.claude/commands/` directory
- B) Store the command in `.claude/commands/test-coverage.md` in the project repository so it's version-controlled and automatically available to everyone
- C) Add the command logic to the root `CLAUDE.md` file as instructions
- D) Create a shared shell script in the repository that developers manually execute

**Correct Answer:** B
**Feedback:** Project commands stored in `.claude/commands/` are version-controlled and automatically discovered by all developers who clone the repository. User commands in `~/.claude/commands/` are personal and not shared. CLAUDE.md (C) is for instructions, not command definitions. Shell scripts (D) bypass Claude integration.
**Theory Reference:** Domain 3.2 - Project vs user command storage

---

## Question 295
**Title:** Interview Pattern for Clarifying Design Considerations
**Situation:** A developer asks Claude Code: "Generate a caching layer for database queries." Claude immediately starts generating code with default TTL values and in-memory storage. The developer later realizes the cache needs distributed support and different invalidation strategies for different data types.
**Options:**
- A) Provide more detailed instructions upfront covering all possible cache scenarios
- B) Claude should use the interview pattern—ask clarifying questions about cache scope (single-process or distributed), invalidation triggers, and consistency requirements before implementing
- C) Generate a basic implementation first, then iterate through multiple refinement cycles
- D) Use specialized model fine-tuned on caching patterns to reduce design uncertainty

**Correct Answer:** B
**Feedback:** The interview pattern surfaces non-obvious design considerations before implementation. Claude asking about distribution scope, consistency models, and invalidation strategies prevents wasted implementation effort. Detailed instructions (A) are hard to anticipate. Iteration (C) is expensive after implementation. Fine-tuning (D) doesn't replace asking the user's actual requirements.
**Theory Reference:** Domain 3.5 - Interview pattern for design clarity

---

## Question 296
**Title:** Test-driven Iteration with Concrete Examples
**Situation:** You ask Claude Code to "improve data validation for customer records" but results in 5 iterations don't match your expectations. Tests fail sporadically; the implementation keeps changing direction.
**Options:**
- A) Let Claude iterate freely and review changes periodically
- B) Provide 2–3 concrete input/output examples upfront with expected validation behavior (e.g., "email missing → validation fails; duplicate ID in same batch → validation fails; but conflicting names with same ID → passes with warning")
- C) Describe the validation requirements in greater detail using narrative
- D) Delegate to a junior developer with clear verbal instructions

**Correct Answer:** B
**Feedback:** Concrete examples are the most effective way to communicate expectations. They show edge cases and decision boundaries clearly. Narrative descriptions (C) are subject to interpretation. Free iteration (A) wastes effort on false starts. Human delegation (D) doesn't solve the ambiguity problem.
**Theory Reference:** Domain 3.5 - Test-driven iteration with concrete examples

---

## Question 297
**Title:** Documenting Testing Standards for Test Generation
**Situation:** Your team generates tests with Claude Code, but the generated tests don't match the team's existing style. New tests use different fixtures, different assertion libraries, and different test data patterns than existing tests.
**Options:**
- A) Ask Claude to infer testing style from existing tests during generation
- B) Have developers manually refactor all generated tests to match style after generation
- C) Document testing standards in CLAUDE.md—include testing frameworks, available fixtures, test data patterns, and existing test files to include in context when generating new tests
- D) Use a code formatter to normalize test style after generation

**Correct Answer:** C
**Feedback:** Documenting standards in CLAUDE.md and including existing test files in generation context ensures generated tests follow team conventions. Inferring style (A) requires reading many files and is unreliable. Manual refactoring (B) is inefficient. Formatters (D) normalize style but don't ensure fixture consistency or test patterns.
**Theory Reference:** Domain 3.6 - Documenting testing standards in CLAUDE.md

---

## Question 298
**Title:** CI/CD Integration with Claude Code Non-Interactive Mode
**Situation:** You've created a Claude Code script to analyze pull requests for security issues. When you try to run it in your CI pipeline, the job hangs indefinitely, waiting for user input that will never come.
**Options:**
- A) Set `CLAUDE_HEADLESS=true` environment variable
- B) Redirect stdin from `/dev/null` to suppress prompts
- C) Use the `-p` (or `--print`) flag to run Claude Code in non-interactive mode: `claude -p "Analyze this PR for security issues"`. This processes the prompt, prints results to stdout, and exits without waiting for input
- D) Use `--batch` flag to enable batch processing mode

**Correct Answer:** C
**Feedback:** The `-p` flag (or `--print`) is the documented way to run Claude Code in CI pipelines without interactive input. It processes the prompt and exits immediately. `CLAUDE_HEADLESS` (A) is not a real feature. Stdin redirection (B) is a Unix workaround, not the proper approach. `--batch` (D) is for batch processing, not non-interactive mode.
**Theory Reference:** Domain 3.6 - Claude Code in CI/CD with -p flag

---

## Question 299
**Title:** Batch Processing Strategy and SLA Alignment
**Situation:** Your company needs to audit 50,000 documents for compliance violations. The audit can run overnight or over a week; no immediate results needed. Your budget for API calls is limited.
**Options:**
- A) Use synchronous API calls to ensure all results are processed immediately
- B) Implement a queuing system that processes documents incrementally throughout the day
- C) Batch documents into groups of 1,000 and submit them as single API calls to reduce overhead
- D) Use the Message Batches API: submit all 50,000 documents with a 24-hour processing window. This provides ~50% cost savings vs synchronous API calls. Use `custom_id` fields to correlate requests and handle failures by re-submitting only documents with failed IDs

**Correct Answer:** D
**Feedback:** Batch API is designed for non-blocking workloads (overnight audits, reports) with 50% cost savings. The 24-hour window is acceptable for compliance audits. Synchronous API (A) is appropriate for blocking checks but wastes money here. Incremental queueing (B) is more complex and provides no cost benefit. Batching documents into single calls (C) doesn't use the Batch API and loses cost advantages.
**Theory Reference:** Domain 4.5 - Batch API for cost-effective processing

---

## Question 300
**Title:** Iterating on Prompts Before Large-Scale Batch Processing
**Situation:** You're about to submit 100,000 documents for extraction via the Batch API. The extraction prompt looks correct, but you have no data on how it performs on real documents in your domain.
**Options:**
- A) Submit the full 100,000 documents immediately; monitor results and refine on subsequent batches if needed
- B) Test the prompt on a few synthetic examples in your local environment, then proceed
- C) Have human reviewers validate the extraction approach manually first, then proceed to batch processing
- D) Test the prompt on a small sample set (50-100 documents) first, analyze failures, refine the prompt, then submit the full 100,000-document batch. This prevents wasting batch quota on a prompt that doesn't work at scale

**Correct Answer:** D
**Feedback:** Testing on a realistic sample before large-scale batch submission prevents expensive failures. The sample enables prompt refinement based on actual performance. Submitting immediately (A) risks batch failure on a large scale. Synthetic examples (B) don't reflect real document complexity. Manual validation (C) is slow and defeats the automation purpose.
**Theory Reference:** Domain 4.5 - Iterating on prompts with sample sets before batch processing
