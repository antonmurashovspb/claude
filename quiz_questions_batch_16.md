# Quiz Question Bank - Batch 16 (Questions 161-170)

## Question 161
**Title:** Adaptive vs Fixed Task Decomposition
**Situation:** Coordinator handles expense reports (always same 4 stages) and bug triage (investigate depth depends on severity). Expenses are predictable. Bug triage varies. Which approach fits each?
**Options:**
- A) Both fixed for consistency
- B) Expenses fixed; bug triage adaptive
- C) Both adaptive for flexibility
- D) Expenses adaptive; bug triage fixed

**Correct Answer:** B
**Feedback:** Fixed pipelines work for predictable stages. Adaptive decomposition works when subtasks depend on intermediate findings. Expenses are fixed; bug triage depth varies based on severity.
**Theory Reference:** Domain 1.6 - Task decomposition; Chapter 6.3 - Prompt chaining

---

## Question 162
**Title:** Tool Result Filtering for Context Efficiency
**Situation:** Your MCP tool returns customer profiles with 200+ fields (ID, name, email, contact history, transactions, preferences, notes). Most workflows need only 5-10 fields. Including all fields wastes context. What is the best solution?
**Options:**
- A) Use PostToolUse hooks to filter results globally
- B) Split the tool into specialized variants like `get_customer_contact`
- C) Add a `fields` parameter so callers request only needed data
- D) Ask agents to ignore unused fields in the system prompt

**Correct Answer:** C
**Feedback:** A `fields` parameter is flexible—each caller requests exactly what it needs. A is less precise. B is less flexible. D relies on agents ignoring unused data, which wastes context.
**Theory Reference:** Domain 2.5 - Tool result filtering; Domain 1.5 - Hook placement

---

## Question 163
**Title:** Preserving Provenance in Multi-source Data Synthesis
**Situation:** Your coordinator aggregates research from three sources. During synthesis, conflicting statistics appear: source A says 15% growth, source B says 22%. Both seem credible. The synthesis agent wants to pick the higher number. How should this be handled?
**Options:**
- A) Use average (18.5%) with both sources in the bibliography
- B) Apply credibility heuristics and silently pick the most authoritative
- C) Escalate to coordinator with both values, source attribution, and dates
- D) Include both numbers without attribution, letting readers decide

**Correct Answer:** C
**Feedback:** Preserving provenance means annotating findings with sources and publication dates. Escalating conflicting data to the coordinator respects separation of responsibilities. A loses granularity. B hides reasoning. D obscures origins.
**Theory Reference:** Domain 5.6 - Preserving provenance; Domain 1.2 - Coordinator responsibilities

---

## Question 164
**Title:** `tool_choice` for Structured Output
**Situation:** Your `extract_metadata` tool needs strict JSON. Agents skip it and return text. What should you do?
**Options:**
- A) Set "auto" and improve prompt
- B) Set to "any" mode
- C) Force specific tool
- D) Use few-shot examples only

**Correct Answer:** C
**Feedback:** Forcing the tool guarantees output format. A allows text. B allows wrong tool. D is unreliable.
**Theory Reference:** Domain 2.3 - Tool_choice; Chapter 2.3 - Output

---

## Question 165
**Title:** Local vs Escalated Error Recovery
**Situation:** A document processing subagent encounters: (1) corrupted PDF section (can skip), (2) API rate limit (can retry with backoff), (3) permission error (needs auth). Which should the subagent handle locally?
**Options:**
- A) Handle all three to minimize coordinator involvement
- B) Handle (1) and (2) locally; escalate (3) with context
- C) Escalate all three to ensure consistent error handling
- D) Handle only (1); escalate (2) and (3)

**Correct Answer:** D
**Feedback:** Errors (1) and (2) are recoverable locally. Error (3) requires external authorization the subagent cannot resolve—escalate with attempted steps and partial results. A overloads the subagent. B and C handle recoverable errors inefficiently.
**Theory Reference:** Domain 1.2 - Error handling and escalation; Domain 1.4 - Subagent responsibilities

---

## Question 166
**Title:** System Prompt Overly Broad Directives
**Situation:** Prompt says "Always verify identity first." Agent verifies all requests wasting tokens. What fixes it?
**Options:**
- A) Reword to specify sensitive ops
- B) Use PreToolUse hooks  
- C) Reduce token budget only
- D) Switch to cheaper model

**Correct Answer:** A
**Feedback:** Rewording fixes root cause. B overengineers. C and D are workarounds.
**Theory Reference:** Domain 1.1 - Prompts; Chapter 1.4 - Wording

---

## Question 167
**Title:** Context Window Lost-in-the-Middle Effect
**Situation:** Your agent gets a 45K-token document. Key info is at start, middle, and end. Which position is riskiest?
**Options:**
- A) Middle is riskiest  
- B) End is risky
- C) All equal risk
- D) Start is risky

**Correct Answer:** A
**Feedback:** Models miss middle details in long docs. Put critical info near start or end.
**Theory Reference:** Domain 1.1 - Context; Chapter 1.5 - Lost-in-middle

---

## Question 168
**Title:** MCP Resource Caching for Exploratory Calls
**Situation:** Your agent makes 15-20 `get_schema` calls per task to discover available database fields. Each call wastes context tokens. An MCP server offers a resource endpoint returning all schemas at once. How should this be used?
**Options:**
- A) Load schemas once at startup; use the resource as a system prompt reference
- B) Continue making individual calls; resources are not for this use case
- C) Use the resource endpoint once at startup and share it across agents
- D) Load the resource once; reference it in the system prompt as a "task summary"

**Correct Answer:** D
**Feedback:** MCP resources serve as "content catalogs"—pre-loaded reference data that reduce exploratory calls. Load once at session start and reference in the prompt. A is less precise. B misses the optimization. C limits reusability.
**Theory Reference:** Domain 2.4 - MCP resources as content catalogs; Domain 1.1 - Context efficiency

---

## Question 169
**Title:** Coordinator Visibility and Observability in Multi-agent Systems
**Situation:** Your coordinator spawns three agents: research, analysis, synthesis. One developer proposes allowing the analysis agent to fetch data directly from external APIs when gaps are identified. What is the main risk?
**Options:**
- A) Improves efficiency; routes are simplified for the analysis agent
- B) Breaks observability; coordinator loses visibility into data sources
- C) Improves freshness; agents bypass outdated coordinator caches
- D) No risk; visibility maintained as long as agents report results

**Correct Answer:** B
**Feedback:** The coordinator owns inter-agent communication for observability. Direct calls by agents hide what data was fetched and when. The coordinator must mediate all external data access. A prioritizes efficiency over control. C is secondary concern. D underestimates impact.
**Theory Reference:** Domain 1.2 - Hub-and-spoke architecture; Domain 1.1 - Observability

---

## Question 170
**Title:** Specialized vs General Tools
**Situation:** Document system uses `extract_pdf`, `extract_docx`, `extract_image` or one `extract` tool with `format` parameter. Agents misuse the parameter. Why?
**Options:**
- A) Tool names signal function directly
- B) Agents struggle with parameter logic
- C) General tools are unreliable
- D) Specialized tools save tokens

**Correct Answer:** A
**Feedback:** Tool names are the primary selection signal. Specialized names are self-describing. Parameter selection requires reasoning and fails more often.
**Theory Reference:** Domain 2.1 - Tool naming; Domain 2.3 - Tool specialization
