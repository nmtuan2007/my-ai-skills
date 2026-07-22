---
name: spec-kit
description: "Use for running the Spec-Driven Development (SDD) workflow. Orchestrates the full development cycle: intakes feature, drafts spec, creates technical plan, generates tasks, executes tasks step-by-step via subagents, and converges/verifies the codebase."
---

# Spec Kit - Spec-Driven Development Orchestrator (Global)

## Purpose

This skill guides the AI coding agent and the user through the **Spec-Driven Development (SDD)** lifecycle step-by-step. It eliminates the need for the user to remember or type individual `/speckit.` commands.

While this skill is active, you are acting as an **SDD Conductor, Architect, Orchestrator, and QA Verifier**. You will orchestrate the phases in strict sequence, enforce the project's local **Constitution** (located at `.specify/memory/constitution.md` under the active workspace), delegate execution to subagents, and strictly verify all builds, tests, and migrations before completion.

---

## 🛑 RULES OF ENGAGEMENT

1. **ONE PHASE AT A TIME (DESIGN & PLANNING):** Do NOT proceed to the next planning phase until the current phase is fully completed, written to disk, and approved by the user.
2. **OBEY HARD STOPS IN PLANNING:** At the end of each planning phase (Specify, Clarify, Plan, Tasks), you must stop and wait for the user's explicit confirmation before launching implementation.
3. **READ COMMAND TOMLS FOR LOGIC:** When executing a phase, read the corresponding TOML instruction file in the active workspace's [`.gemini/commands/`](file:///.gemini/commands/) directory if present to understand prompt templates and execution flows:
   - Phase 1 (Specify) $\rightarrow$ [`speckit.specify.toml`](file:///.gemini/commands/speckit.specify.toml)
   - Phase 2a (Clarify) $\rightarrow$ [`speckit.clarify.toml`](file:///.gemini/commands/speckit.clarify.toml)
   - Phase 2b (Plan) $\rightarrow$ [`speckit.plan.toml`](file:///.gemini/commands/speckit.plan.toml)
   - Phase 3 (Tasks) $\rightarrow$ [`speckit.tasks.toml`](file:///.gemini/commands/speckit.tasks.toml)
   - Phase 4 (Implement) $\rightarrow$ [`speckit.implement.toml`](file:///.gemini/commands/speckit.implement.toml)
   - Phase 5 (Converge) $\rightarrow$ [`speckit.converge.toml`](file:///.gemini/commands/speckit.converge.toml)
4. **ENFORCE CONSTITUTION:** During the planning phase, always review the workspace's local [`.specify/memory/constitution.md`](file:///.specify/memory/constitution.md) and check that proposed architectural changes do not violate its rules.
5. **ZERO-ASSUMPTION INTERVIEW PROTOCOL (CRITICAL):** You are strictly prohibited from assuming or guessing any business logic, UI behavior, edge case, data model, or technical detail. During intake and clarification, you MUST aggressively interview the user with clear, structured questions. Every ambiguous requirement (even 0.1%) MUST be resolved through explicit user answers before creating `spec.md` or `plan.md`.
6. **PURE ORCHESTRATION & DELEGATION (NO CONTEXT POLLUTION):** Once the user approves the implementation plan and task list, the main session acts purely as an **Orchestrator/Manager**. All code generation, file creation, editing, and task-level execution MUST be delegated to subagents (`invoke_subagent`). Never write code directly in the parent session context or output raw code diffs/snippets into the main conversation context.
7. **AUTOMATED PARALLEL & SEQUENTIAL SUBAGENT DISPATCH:** Once Phase 4 begins, orchestrate task execution automatically according to the task dependency graph in `tasks.md`:
   - Independent tasks must be dispatched in parallel across concurrent subagents.
   - Dependent tasks must be dispatched sequentially upon prerequisite completion.
   - Do NOT interrupt or ask the user for confirmation on every individual task or code edit during Phase 4.
8. **LEVERAGE GITNEXUS FOR RECONNAISSANCE:** Before and during Phase 1 (Specify) and Phase 2 (Plan), you MUST use GitNexus MCP tools (if available) to query and verify codebase symbols, architecture, and context. Never guess existing implementations.
9. **MANDATORY EMPIRICAL VERIFICATION (NON-NEGOTIABLE):** The main orchestrator MUST NEVER declare a feature, task, or session completed based on subagent claims alone. Phase 5 (Convergence & Verification) requires running concrete, empirical verification commands:
   - Audit all subagents to ensure 100% clean exit without unhandled failures.
   - Run workspace build and compilation commands (`npm run build`, `dotnet build`, `go build`, `cargo build`, `npx tsc`, etc.) to prove 0 compilation/type errors.
   - Execute database schema migrations and verify DB state if migrations were added or updated.
   - Run the unit and integration test suites to guarantee zero regressions.
   - **Auto-Remediation Loop:** If any verification step (build, test, migration) fails, dispatch a subagent to fix the failure, re-run verification, and repeat until 100% green before reporting completion to the user.

---

## The SDD Workflow

### Phase 1: Feature Intake & Zero-Assumption Specification
*Goal: Interview user aggressively to eliminate all business logic assumptions, query codebase context with GitNexus, brainstorm edge cases, and draft baseline spec.*

1. **Reconnaissance (GitNexus):** If available, use GitNexus MCP tools (`gitnexus_query`, `gitnexus_context`, `gitnexus_route_map`) to inspect existing repository structure and patterns.
2. **Zero-Assumption Interactive Interview:**
   - List all potential assumptions, edge cases, failure states, state management choices, and user flow ambiguities.
   - Interrogate the user with structured, numbered questions to clarify 100% of business logic.
   - Do NOT proceed to draft the specification until all questions are explicitly answered.
3. Execute prompt logic in [`speckit.specify.toml`](file:///.gemini/commands/speckit.specify.toml) to draft `specs/[feature-name]/spec.md`.
4. **[HARD STOP]** Ask the user:
   > "Does this specification capture all requirements with ZERO assumptions? Please confirm or provide adjustments to proceed to Design Trade-offs & Clarification."

---

### Phase 2a: Design Trade-offs & Architecture Selection
*Goal: Present technical design options with clear trade-offs and confirm architectural choices.*

1. **Explore Design Approaches:** Present 2-3 viable technical design approaches. Highlight your recommended option with trade-offs (complexity, scalability, maintainability, risk).
2. **[HARD STOP]** Ask the user:
   > "Please select which architectural approach you approve for the Technical Plan."

---

### Phase 2b: Technical Plan Generation
*Goal: Generate the final implementation plan based on approved design and verify against Project Constitution.*

1. **Detailed Architecture Inspection (GitNexus):** Query existing classes, routes, and interfaces.
2. Execute prompt logic in [`speckit.plan.toml`](file:///.gemini/commands/speckit.plan.toml) to draft `specs/[feature-name]/plan.md`.
3. Perform **Constitution Check** against [`.specify/memory/constitution.md`](file:///.specify/memory/constitution.md).
4. **[HARD STOP]** Present proposed plan and ask:
   > "Do you approve this technical plan? Please review and confirm to proceed to generating tasks."

---

### Phase 3: Actionable Tasks & Dependency Graph Generation
*Goal: Break down implementation plan into discrete, actionable tasks and construct a dependency DAG.*

1. Execute prompt logic in [`speckit.tasks.toml`](file:///.gemini/commands/speckit.tasks.toml) to generate `specs/[feature-name]/tasks.md`.
2. Map out independent parallel tasks and dependent sequential tasks.
3. **[HARD STOP]** Present task list and dependency graph to the user, and ask:
   > "Here is the task checklist and execution graph. Please confirm to launch automated subagent implementation."

---

### Phase 4: Automated Subagent Orchestration
*Goal: Execute all tasks automatically via isolated subagents without context pollution or per-task user prompts.*

1. Execute prompt logic in [`speckit.implement.toml`](file:///.gemini/commands/speckit.implement.toml).
2. Parse `specs/[feature-name]/tasks.md` into an execution DAG (Directed Acyclic Graph).
3. **Automated Subagent Dispatch:**
   - Launch subagents (`invoke_subagent`) concurrently for independent tasks.
   - Launch subagents sequentially for tasks dependent on previous completions.
4. **Context Cleanliness:**
   - Subagents perform all code edits, file creations, and localized checks inside their isolated contexts.
   - The main orchestrator session receives status updates, updates task progress `[x]` in `tasks.md`, and keeps the main conversation context clean of code diffs.
   - Do NOT ask user confirmation for individual code edits or sub-tasks once Phase 4 has been authorized.

---

### Phase 5: Strict Empirical Convergence & Verification (MANDATORY)
*Goal: Validate complete system health, build compilation, database migrations, and test pass rate before completion.*

1. **Subagent Completion Audit:** Verify that all spawned subagents have completed cleanly with status `SUCCESS` and zero unhandled errors.
2. **Empirical Build Execution:** Run active build and compilation commands (e.g. `npm run build`, `dotnet build`, `go build`, `cargo build`, `npx tsc`). Verify exit code 0 and zero compilation errors.
3. **Database & Migration Audit:** If database migrations were created or altered, execute migration commands (or migration dry-runs) in the dev/test environment to ensure they execute cleanly without errors or rollback issues.
4. **Automated Test Suite:** Execute unit tests, integration tests, and linters.
5. **Auto-Remediation Loop:** If any verification step fails:
   - Do NOT declare completion or report failure without attempting resolution.
   - Spawn a dedicated subagent with the exact build/test/migration log error to fix the code.
   - Re-run verification commands until 100% green.
6. **Walkthrough & Final Handoff:**
   - Execute prompt logic in [`speckit.converge.toml`](file:///.gemini/commands/speckit.converge.toml).
   - Generate `walkthrough.md` with explicit command execution logs proving build, test, and migration success.
   - Present the final summary to the user.
