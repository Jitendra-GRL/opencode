**Hierarchical Planner**

- **Inputs:** normalized user intent (goal text + context tags), current session state snapshot (open files, active tools, permissions), and latest embeddings/notes from the skill graph.
- **Step 1 — Goal framing:** planner calls a low-latency LLM to restate the goal as $g = \langle\text{objective},\ \text{constraints},\ \text{success criteria}\rangle$ and tags required capabilities.
- **Step 2 — Retrieval:** uses the capability tags to fetch relevant skills, prior run logs, and artifacts; the result is an evidence bundle $E$.
- **Step 3 — Draft plan:** submits $\{g,E\}$ to a planning LLM that emits an ordered task list or DAG with step metadata (tool hints, estimated cost, dependency edges, rollback ideas).
- **Step 4 — Critique & refinement:** a critic prompt replays the draft plus guardrails (security, resource limits) and either approves or patches the plan, ensuring every step has: inputs, tool choice, exit condition, and hand-off payload definition.
- **Step 5 — Packaging:** converts the vetted plan into an executable graph `PlanSpec` whose nodes contain (a) trigger condition, (b) `requiredContext` (files, secrets, MCP servers), (c) `expectedOutputs`, and (d) escalation rules.
- **Outputs:** `PlanSpec`, risk notes, and a permission manifest enumerating resources the orchestrator must request before execution.

**Execution Orchestrator**

- **Inputs:** `PlanSpec`, current capability token set, live system telemetry (filesystem, process table), MCP connection info, and UX callbacks for status.
- **Step 1 — Dependency resolution:** topologically sorts ready nodes, verifying each has required permissions; if not, sends approval prompts back to the UX shell.
- **Step 2 — Context assembly:** for each runnable node, gathers real inputs (files, MCP records, cached web data) and normalizes them into the tool’s expected format (e.g., shell command, browser script, MCP request payload).
- **Step 3 — Dispatch:** invokes the appropriate connector (System Tooling, MCP client, Browser driver, App adapter) with the prepared context; attaches tracing IDs so outputs map back to the originating plan node.
- **Step 4 — Monitoring & retries:** streams stdout/stderr or API responses into the audit log, enforces guardrails (timeouts, diff size limits), and retries idempotent steps when policies allow; on failure, emits structured error objects so the planner (or human) can re-plan.
- **Step 5 — Output normalization:** parses tool results into canonical artifacts (file diffs, JSON data, screenshots, search summaries) and records them in the state store while updating skill confidence scores.
- **Step 6 — Feedback loop:** feeds normalized outputs back to the planner interface (auto-reflection trigger) and to the UX shell for user visibility; if downstream steps depended on the new data, marks them ready and repeats.
- **Outputs:** execution log entries, updated artifacts in the knowledge base, per-step status (success/failure/pending), and any newly-learned skill metadata (e.g., “browser search→summarize” latency, accuracy notes).

This sequencing keeps planning cognitive work separated from execution-side side effects, while still sharing a single state store so both components stay synchronized on context, permissions, and learned skills.
