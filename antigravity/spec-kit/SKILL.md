---
name: spec-kit
description: "Use for running the Spec-Driven Development (SDD) workflow. Orchestrates the full development cycle: intakes feature, drafts spec, creates technical plan, generates tasks, executes tasks step-by-step, and converges/verifies the codebase."
---

# Spec Kit - Spec-Driven Development Orchestrator (Global)

## Purpose

This skill guides the AI coding agent and the user through the **Spec-Driven Development (SDD)** lifecycle step-by-step. It eliminates the need for the user to remember or type the individual `/speckit.` commands.

While this skill is active, you are acting as an **SDD Conductor, Architect, and QA Reviewer**. You will orchestrate the phases in strict sequence and enforce the project's local **Constitution** (located at `.specify/memory/constitution.md` under the active workspace) at all times.

---

## 🛑 RULES OF ENGAGEMENT

1. **ONE PHASE AT A TIME:** Do NOT proceed to the next phase until the current phase is fully completed, written to disk, and approved by the user.
2. **OBEY HARD STOPS:** At the end of each phase/sub-phase (Specify, Clarify, Plan, Tasks), you must stop and wait for the user's explicit confirmation or feedback (e.g. "Proceed").
3. **READ COMMAND TOMLS FOR LOGIC:** When executing a phase, read the corresponding TOML instruction file in the active workspace's [`.gemini/commands/`](file:///.gemini/commands/) directory to understand the prompt template, execution flow, and exact file paths:
   - Phase 1 (Specify) $\rightarrow$ [`speckit.specify.toml`](file:///.gemini/commands/speckit.specify.toml)
   - Phase 2a (Clarify) $\rightarrow$ [`speckit.clarify.toml`](file:///.gemini/commands/speckit.clarify.toml)
   - Phase 2b (Plan) $\rightarrow$ [`speckit.plan.toml`](file:///.gemini/commands/speckit.plan.toml)
   - Phase 3 (Tasks) $\rightarrow$ [`speckit.tasks.toml`](file:///.gemini/commands/speckit.tasks.toml)
   - Phase 4 (Implement) $\rightarrow$ [`speckit.implement.toml`](file:///.gemini/commands/speckit.implement.toml)
   - Phase 5 (Converge) $\rightarrow$ [`speckit.converge.toml`](file:///.gemini/commands/speckit.converge.toml)
4. **ENFORCE CONSTITUTION:** During the planning phase, always review the workspace's local [`.specify/memory/constitution.md`](file:///.specify/memory/constitution.md) and check that the proposed architectural changes do not violate its rules.
5. **SUBAGENT DELEGATION:** All implementation tasks (Phase 4) MUST be delegated to subagents (`self` or specialized write-capable agents). You, as the coordinator, must NOT write code directly in your parent conversation context.
6. **SUBAGENT TASK ISOLATION (GRANULARITY):** Subagents must only be given small, highly specific, and granular sub-tasks (e.g., single-file or single-component tasks). Do NOT pass large, broad, or compound tasks to a subagent to prevent context pollution and ensure clean code execution.
7. **EXPLICIT CODING CONFIRMATION GATES:** Before performing any code generation, file modification, or file deletion, you MUST present the planned edits/file creations and obtain explicit confirmation from the user.
8. **DATABASE & MIGRATION LOCK:** If a task involves database schema modifications or running database migrations, you MUST explicitly request and receive separate permission from the user before executing them. Never run migrations automatically or silently.
9. **LEVERAGE GITNEXUS FOR RECONNAISSANCE:** Before and during Phase 1 (Specify) and Phase 2 (Plan), you MUST use GitNexus MCP tools (if available) to query and verify codebase symbols and context. Never guess or hallucinate existing implementations.
10. **ZERO ASSUMPTIONS & GUESSING (CRITICAL):** You are strictly prohibited from assuming or guessing any business logic, UI behavior, edge case, or technical implementation detail. If even 0.1% of a requirement is ambiguous or unverified, you MUST stop and ask the user. The clarification loop MUST continue until 100% clarity is achieved.
11. **MANDATORY EDGE CASE & EXCEPTION BRAINSTORMING:** Before drafting any specification (`spec.md`) or technical plan (`plan.md`), you MUST proactively brainstorm and document all possible edge cases, network timeouts, data corruptions, user errors, and failure modes. Present these to the user for explicit decision-making; do not assume default handling.

---

## The SDD Workflow

### Phase 1: Feature Intake & Specification
*Goal: Understand the user's intent, query codebase context with GitNexus, brainstorm edge cases, and establish baseline user scenarios, edge cases, and success criteria.*

1. **Reconnaissance (GitNexus):** If available, use GitNexus MCP tools (`gitnexus_query`, `gitnexus_context`, `gitnexus_route_map`) to check if similar concepts, classes, or patterns exist in the repository to avoid duplicate work.
2. **Edge Case Brainstorming:** Proactively list potential edge cases, exceptions, and failure states related to the raw feature request (e.g. offline behavior, slow network, corrupted local cache) and ask the user how to handle them.
3. Prompt the user to describe the feature they want to build (if not already provided).
4. Execute the prompt logic in [`speckit.specify.toml`](file:///.gemini/commands/speckit.specify.toml) to draft the feature specification at `specs/[feature-name]/spec.md`.
5. **[HARD STOP]** Ask the user:
   > "Does this specification capture all requirements correctly? Please confirm or provide adjustments to proceed to the Clarification & Design phase."

---

### Phase 2a: Clarification & Design Trade-offs
*Goal: Ask structured clarification questions and present technical design approaches with clear trade-offs before generating a plan.*

1. **Structured Clarification:** Execute the prompt logic in [`speckit.clarify.toml`](file:///.gemini/commands/speckit.clarify.toml) to generate questions for any remaining ambiguous areas (functional, performance, security, data validation).
2. **Explore Design Approaches:** Present 2-3 viable technical design approaches for the feature. Highlight your recommended option and clearly analyze the trade-offs (complexity, dependency, extensibility, risk).
3. **[HARD STOP]** Ask the user:
   > "Please answer the clarification questions above and select which architectural approach you approve for the Technical Plan."

---

### Phase 2b: Technical Plan Generation
*Goal: Generate the final implementation plan based on the approved design and verify it against the Project Constitution.*

1. **Detailed Architecture Query (GitNexus):** Before writing the plan, use GitNexus tools (if available) to inspect existing codebase structures (classes, interfaces, routing schemas).
2. Execute the prompt logic in [`speckit.plan.toml`](file:///.gemini/commands/speckit.plan.toml) to draft the technical plan at `specs/[feature-name]/plan.md`.
3. Map the technical approach, files to be modified/created, and dependency layers.
4. Perform the **Constitution Check** as defined in the plan template (verifying architectural layering, modular boundaries, tests, and external integration bounds).
5. **[HARD STOP]** Present the proposed plan and ask:
   > "Do you approve this technical plan? Please review and confirm to proceed to generating tasks."

---

### Phase 3: Actionable Tasks Generation
*Goal: Generate a granular checklist of tasks to implement the approved plan.*

1. Execute the prompt logic in [`speckit.tasks.toml`](file:///.gemini/commands/speckit.tasks.toml) to generate the task list at `specs/[feature-name]/tasks.md`.
2. Present the task list to the user, highlighting the foundational blocking tasks and user story separation.
3. **[HARD STOP]** Ask the user:
   > "Here is the checklist of tasks. Please confirm to begin implementation."

---

### Phase 4: Implementation (via Subagents)
*Goal: Execute the tasks one-by-one using isolated subagents, verifying correctness at each step.*

1. Execute the prompt logic in [`speckit.implement.toml`](file:///.gemini/commands/speckit.implement.toml).
2. For each task in `specs/[feature-name]/tasks.md`:
   - Split the task into small, isolated sub-tasks to ensure subagent context cleanliness.
   - For each sub-task:
     - **Gate 1 (Coding Confirmation):** Present the planned file changes/creations to the user and obtain explicit confirmation.
     - **Gate 2 (Database Migration Lock):** If the sub-task requires database schema modifications or running migrations, obtain explicit separate user permission.
     - **Gate 3 (Subagent Delegation):** Spawn a subagent to implement the sub-task.
     - Run project-specific test suites (unit tests, integration tests, or linters) to ensure no regressions and verify compilation/build correctness.
     - Mark the sub-task/task as `[x]` in `tasks.md`.
3. Keep the user updated on progress.

---

### Phase 5: Convergence & Verification
*Goal: Perform final checks, cleanups, and handoff.*

1. Execute the prompt logic in [`speckit.converge.toml`](file:///.gemini/commands/speckit.converge.toml) to audit the implemented code against `spec.md`.
2. Generate the walkthrough at `walkthrough.md` summarizing the changes and instructions for manual play-tests.
3. Present the final summary to the user.
