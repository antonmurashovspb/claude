# Quiz Question Bank - Batch 22 (Questions 211-220)

## Question 211
**Title:** Preserving State Across Claude API Calls  
**Situation:** Your support workflow verifies a customer, checks an order, and then decides whether a refund is allowed. To save tokens, an engineer sends only the latest user message on the next Claude API call, and the model starts asking for identity verification again.  
**Options:**
- A) Send the full conversation history, including prior assistant turns and tool results, on each request
- B) Send only the latest user message because Claude persists prior state across API calls
- C) Send only a short assistant recap because tool results do not affect later reasoning
- D) Send the system prompt again and omit earlier turns to keep the request lightweight

**Correct Answer:** A  
**Feedback:** Claude API calls are stateless, so the model needs the relevant conversation history each time. That includes prior assistant messages and tool results, which are part of the reasoning context for the next step.  
**Theory Reference:** Chapter 1.2 & 3.1 - Full history requirement and agentic loop context

---

## Question 212
**Title:** Preventing Built-in Tools from Overshadowing MCP Tools  
**Situation:** Your team adds an MCP tool called `search_product_catalog` that queries live inventory and pricing. Agents still keep using built-in Grep against stale local docs, producing outdated recommendations for sales operations.  
**Options:**
- A) Remove built-in search tools whenever any MCP server is connected to the workspace
- B) Strengthen the MCP tool description with live-data advantages, inputs, and clear usage boundaries
- C) Merge the MCP search and Grep into one generic search tool with a source parameter
- D) Add a prompt saying "prefer MCP tools" while leaving both tool descriptions unchanged

**Correct Answer:** B  
**Feedback:** Tool descriptions are the main signal the model uses when choosing among overlapping tools. A stronger MCP description should highlight why it is preferable, such as live inventory, pricing freshness, and cases where local files are insufficient.  
**Theory Reference:** Domain 2.1 & Chapter 2.2 - Tool descriptions and MCP vs built-in selection

---

## Question 213
**Title:** Forcing Structured Extraction Without Fixing the Exact Tool  
**Situation:** A document intake step must always call one of several extraction tools before any downstream decision is made. You do not care which extractor is chosen, but prose answers are unacceptable because the next stage expects structured output.  
**Options:**
- A) Use `tool_choice: {"type": "auto"}` so the model decides whether extraction is needed
- B) Force one named extraction tool even when multiple tools may fit the input equally well
- C) Use `tool_choice: {"type": "any"}` so the model must call some tool before replying
- D) Omit `tool_choice` and rely on prompt wording to imply that structured output is required

**Correct Answer:** C  
**Feedback:** `tool_choice: "any"` guarantees that the model will call a tool instead of returning plain text. It preserves flexibility across multiple valid extractors while still enforcing a structured, tool-driven first step.  
**Theory Reference:** Domain 2.3 & Chapter 2.3 - Configuring `tool_choice`

---

## Question 214
**Title:** Avoiding Fabricated Values in JSON Schema Output  
**Situation:** Your batch classifier extracts `sector`, `region`, and `renewal_date` from vendor contracts. Many contracts do not include a renewal date, but the current schema marks it as required, so the model keeps inventing dates to satisfy the structure.  
**Options:**
- A) Keep `renewal_date` required and filter out suspicious placeholder dates after generation
- B) Replace `renewal_date` with a free-text notes field so missing values are easier to explain
- C) Remove `renewal_date` from the schema entirely so the model never sees that attribute
- D) Make `renewal_date` nullable and require only the fields that are reliably present

**Correct Answer:** D  
**Feedback:** Required fields push the model to fabricate values when the source does not contain them. Nullable optional fields let the model return `null` honestly, which is more reliable than forcing a fake answer into the schema.  
**Theory Reference:** Chapter 2.4 - Required vs optional fields and nullable schema design

---

## Question 215
**Title:** Reducing Duplicate Work in Multi-agent Research  
**Situation:** A coordinator asks three subagents to "investigate payment failures" with the same broad prompt. All three agents search the same logs and return overlapping findings, creating token waste and weak coverage of distinct failure modes.  
**Options:**
- A) Assign separate scopes such as gateway failures, fraud rejects, and database timeouts before synthesis
- B) Keep the prompts broad but raise token limits so each subagent can investigate in more depth
- C) Ask each subagent to read the others' findings first so duplication is caught during execution
- D) Give all subagents identical tasks and let the coordinator deduplicate the overlap afterward

**Correct Answer:** A  
**Feedback:** Coordinators should decompose work into distinct coverage areas so subagents complement rather than duplicate one another. Separation by evidence source or failure type improves observability, breadth, and aggregation quality.  
**Theory Reference:** Domain 1.2 - Coordinator decomposition and result aggregation

---

## Question 216
**Title:** Mitigating Lost-in-the-Middle for Critical Policy Rules  
**Situation:** A refund-review agent receives a long policy packet where the only rule requiring executive approval appears in the middle of the document. The agent repeatedly misses the threshold and approves transactions that should have been escalated.  
**Options:**
- A) Increase `max_tokens` so the model has more room to reason about the full packet
- B) Move or summarize the critical threshold near the start or end of the provided context
- C) Split the document into arbitrary chunks so the rule appears in a different place each run
- D) Replace the policy text with a shorter prompt telling the agent to read more carefully

**Correct Answer:** B  
**Feedback:** Long contexts suffer from the lost-in-the-middle effect, where details in the middle are easier to miss. Repositioning or summarizing critical rules near the beginning or end makes them more salient during reasoning.  
**Theory Reference:** Chapter 1.5 - Context window limits and lost-in-the-middle mitigation

---

## Question 217
**Title:** Isolating High-volume Skill Output from the Main Session  
**Situation:** Your `/dependency-audit` skill produces long package trees, license tables, and remediation notes. After developers run it, the main coding session becomes cluttered and the model starts losing focus on the original bug-fix task.  
**Options:**
- A) Store the skill only in a user directory so fewer developers can trigger the extra output
- B) Convert the skill to a normal slash command and keep all audit output in the active session
- C) Set `context: fork` so the skill runs in an isolated subagent context before returning results
- D) Append the full audit report after every code edit so the model keeps dependency context fresh

**Correct Answer:** C  
**Feedback:** `context: fork` isolates verbose skill execution from the main session, preserving focus and context quality. It is especially useful for audits, investigations, and other flows that generate large intermediate output.  
**Theory Reference:** Domain 3.2 - Skills with isolated forked context

---

## Question 218
**Title:** Running Claude Code Reliably in Nightly Batch Pipelines  
**Situation:** Your CI system runs Claude Code each night to review hundreds of changed files across multiple services. The job sometimes hangs waiting for interaction, and downstream automation cannot reliably parse free-form terminal output into structured review records.  
**Options:**
- A) Use interactive mode and parse the terminal transcript with a regular-expression postprocessor
- B) Replace the batch review with a static checklist file maintained by developers each sprint
- C) Run separate plain-text review sessions per file and manually merge the comments later
- D) Use `-p` for non-interactive execution plus JSON output with a schema for downstream parsing

**Correct Answer:** D  
**Feedback:** Batch workflows need non-interactive execution so the process never blocks on user input. Structured JSON output with a schema also makes it much easier for CI systems to validate and route findings automatically.  
**Theory Reference:** Domain 3.6 - Claude Code in CI/CD pipelines

---

## Question 219
**Title:** Where to Handle Transient Failures in Large Subagent Batches  
**Situation:** A coordinator launches 20 subagents to process invoices with an OCR MCP service. Several subagents hit temporary 503 errors, but most of those requests succeed when retried shortly afterward.  
**Options:**
- A) Let subagents retry transient failures locally and escalate only unresolved cases to the coordinator
- B) Return every transient failure immediately so the coordinator owns all retry logic centrally
- C) Disable retries because repeated OCR calls can distort the subagent's reasoning process
- D) Treat each 503 as a non-retryable business failure and skip the affected invoice outright

**Correct Answer:** A  
**Feedback:** Local recovery keeps the coordinator focused on unresolved exceptions rather than routine transient noise. Structured retryable errors are meant to support exactly this kind of intelligent, layered error handling.  
**Theory Reference:** Domain 2.2 & 1.2 - Structured retry logic and coordinator responsibilities

---

## Question 220
**Title:** Recovering When Precise Edit Operations Become Ambiguous  
**Situation:** You need to update a compliance flag across many configuration files. The Edit tool fails because the target text appears multiple times in the same file, so the match is no longer unique and the change cannot be applied safely.  
**Options:**
- A) Switch to Grep because it can both find and rewrite ambiguous matches without further context
- B) Retry the same Edit request repeatedly until one of the duplicate matches is accepted
- C) Use Write on the repository root so the system can replace every duplicate in one operation
- D) Read the affected files, then apply edits or writes using unique local context for each change

**Correct Answer:** D  
**Feedback:** When Edit cannot identify a unique match, the safe fallback is to inspect the file contents and use more specific local context. That preserves precision and avoids accidental modification of the wrong occurrence.  
**Theory Reference:** Domain 2.5 - Read, Edit, Write, Grep, and Glob usage patterns
