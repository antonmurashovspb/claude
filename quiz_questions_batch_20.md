# Quiz Question Bank - Batch 20 (Questions 191-200)

## Question 191
**Title:** Coordinator vs Subagent Tool Responsibility
**Situation:** Your multi-agent system spans multiple regions searching energy policies. The coordinator spawns regional subagents with access to 15+ tools: databases, APIs, reporting. This creates inefficient selection and token waste.
**Options:**
- A) Restrict subagents to region tools only; coordinator keeps all tools
- B) Duplicate all tools across agents for consistency
- C) Guide selection through improved descriptions
- D) Implement a central tool registry service

**Correct Answer:** A
**Feedback:** Apply least privilege principle. Subagents need only region-specific research tools. Coordinator retains aggregation and cross-region tools. This reduces context and improves reliability.
**Theory Reference:** Domain 2.3 - Allocating tools across agents

---

## Question 192
**Title:** Error Context Propagation Between Agents
**Situation:** A coordinator delegates research to a subagent. The subagent hits an API rate limit (transient error), returning `{"isError": true, "errorCategory": "transient", "isRetryable": true}` with no additional context about the failed query or partial results.
**Options:**
- A) Retry immediately since transient errors always resolve
- B) Provide context: query, partial results, retry options for informed decisions
- C) Escalate immediately; all errors require human intervention
- D) Have subagent retry internally before returning

**Correct Answer:** B
**Feedback:** Structured error context lets coordinator decide: retry with modified query, try alternatives, or use partial results. Without context, the coordinator cannot make intelligent recovery choices.
**Theory Reference:** Domain 2.2 - Structured error responses

---

## Question 193
**Title:** Session Forking for Comparative Analysis
**Situation:** You analyze a codebase for refactoring. Approach A organizes modules by feature; Approach B organizes by technical layer. After exploring approach A deeply, you want to compare approach B while preserving your A analysis.
**Options:**
- A) Restart analysis with approach B in a new session
- B) Use --resume to reload approach A context after B analysis
- C) Have a separate agent analyze approach B in parallel
- D) Fork current session to explore approach B independently

**Correct Answer:** D
**Feedback:** fork_session creates independent branches from shared context. You can explore approach B without losing approach A analysis. Later compare both approaches.
**Theory Reference:** Domain 1.7 - Session forking

---

## Question 194
**Title:** Tool Specialization for Agent Reliability
**Situation:** You design validation tools for financial systems. Option 1: specialized tools `validate_compliance`, `validate_accounts`, `validate_fraud`. Option 2: single `validate` tool with type parameter. Agents misuse the general approach.
**Options:**
- A) Specialized tools are more reliable; agents struggle with parameter reasoning
- B) Equally reliable; good descriptions solve the issue
- C) Consolidate into a single pipeline
- D) Add examples to teach parameter combinations

**Correct Answer:** A
**Feedback:** Specialized tools with distinct names self-identify their purpose. Agents fail to reason about parameter combinations effectively. Specific names prevent misrouting.
**Theory Reference:** Domain 2.3 - Tool specialization

---

## Question 195
**Title:** Multi-step Workflow: Single Prompt vs Chaining
**Situation:** Build a document pipeline: extract metadata, normalize formats, validate content, classify type, generate summary. Each step uses prior output. Use one large prompt or separate focused prompts?
**Options:**
- A) Single prompt minimizes API calls and maintains coherence throughout
- B) Separate prompts per stage; each gets focused attention without dilution
- C) Combine related stages: extract+normalize, validate+classify, then summary
- D) Have stages 1-3 in one prompt; stages 4-5 in another

**Correct Answer:** B
**Feedback:** Prompt chaining uses focused prompts per distinct stage. Each stage concentrates fully on its task. Quality outweighs API calls. Intermediate results can be validated between steps.
**Theory Reference:** Domain 1.6 - Task decomposition

---

## Question 196
**Title:** Hook Placement: Business Rule Enforcement
**Situation:** Your system enforces compliance: refunds exceeding $5,000 require manager approval before processing. The `process_refund` tool calls a payment API. Where should you implement this amount check?
**Options:**
- A) In system prompt with explicit instructions
- B) PostToolUse hook to validate amounts after execution
- C) PreToolUse hook to block requests before execution
- D) Separate verification tool called first

**Correct Answer:** C
**Feedback:** PreToolUse hooks intercept requests before tool execution. This prevents unauthorized refunds from being processed. Hooks guarantee compliance; prompts provide only probabilistic guidance.
**Theory Reference:** Domain 1.5 - Agent SDK hooks

---

## Question 197
**Title:** Context Passing in Multi-agent Architectures
**Situation:** Your coordinator decomposes a task into subagents: research, analysis, synthesis. The research subagent returns 25K tokens of structured findings. Now the coordinator must pass these to the analysis subagent.
**Options:**
- A) Subagents inherit coordinator's full conversation history automatically
- B) Store findings in database; pass only reference identifier
- C) Request only top 2-3 findings from research to minimize context load
- D) Full research findings must be included in analysis subagent's prompt since subagents have isolated context

**Correct Answer:** D
**Feedback:** Subagents have isolated context; they don't inherit coordinator history. All required information must be explicitly included. Pass full 25K tokens for complete analysis.
**Theory Reference:** Domain 1.3 - Context passing

---

## Question 198
**Title:** Managing Secrets in Shared Configuration
**Situation:** Your team shares `.mcp.json` configuring an MCP GitHub server. Options: hardcode tokens directly, use environment variable substitution, or pass tokens at runtime. Which balances security and practicality?
**Options:**
- A) Hardcoding is acceptable if the file is gitignored locally
- B) Developers manually pass tokens via command-line
- C) Use `"token": "${GITHUB_TOKEN}"` in `.mcp.json`; store actual tokens locally
- D) Implement periodic automatic token rotation

**Correct Answer:** C
**Feedback:** MCP servers support `${VAR_NAME}` substitution. Configuration stays version-controlled while tokens remain local. This balances team collaboration with security.
**Theory Reference:** Domain 2.4 - MCP server configuration

---

## Question 199
**Title:** Stop Reason vs Iteration Limits in Agentic Loops
**Situation:** Your agent enforces a 5-tool-call limit. Simple tasks complete in 3 calls, but complex tasks fail at call 5 because they haven't received `stop_reason = "end_turn"` yet.
**Options:**
- A) Use stop_reason to control loop; stop only on "end_turn"
- B) Implement task complexity detection for dynamic limits
- C) Add manual approval after 5 iterations
- D) Increase limit to 15 for all users

**Correct Answer:** A
**Feedback:** stop_reason signals actual completion ("end_turn") or tool needs ("tool_use"). Arbitrary iteration limits override the model's judgment. Let the model decide when to stop.
**Theory Reference:** Domain 1.1 - Agentic loops

---

## Question 200
**Title:** Constraining Skills with Tool Access Restrictions
**Situation:** Your team creates `/code-security-check` skill for static code analysis. It should use only analysis tools, never modification tools. However, developers report it sometimes suggests code modifications.
**Options:**
- A) Separate skills: one for analysis, one for modifications
- B) Manually review each skill invocation before execution
- C) Use allowed-tools restriction in SKILL.md frontmatter
- D) Strengthen system prompt guidance toward analysis

**Correct Answer:** C
**Feedback:** The allowed-tools field in SKILL.md restricts tool access at the system level. This architectural constraint cannot be overridden by prompts. Specify only analysis tools.
**Theory Reference:** Domain 3.2 - Custom skills
