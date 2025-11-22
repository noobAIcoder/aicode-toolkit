# REPO_ROOT/.pipeline-x/AGENTS.md

---

## Standard Workflow

All requirements in this document use RFC 2119 terminology (MUST, SHOULD, MAY).

## General rules:

- On every HALT, the agent MUST update `REPO_ROOT/.pipeline-x/states/agent_state.json` before stopping.
- In `--full-auto` mode, approval gates are bypassed; otherwise the agent MUST stop at each gate until the operator explicitly resumes.
- If no applicable template exists for a required artifact, the agent MUST:
  - Ask the operator for guidance.
  - Create a new template for this job in the appropriate templates directory (see “Workflow folders”).
  - Update `.pipeline-x/states/agent_state.json`.
  - HALT pending operator approval of the new template.

### 1. Ingest and clarify

1. The agent MUST deconstruct the user request:
   - Goals, constraints, assumptions, and success criteria.
2. The agent MUST identify relevant files and read the first 10 lines of each.
3. If any requirement or context is ambiguous, the agent MUST:
   - Ask the operator for clarification.
   - HALT until clarification is provided.

### 2. Meta-plan

1. The agent MUST search the templates tree under `REPO_ROOT/.pipeline-x/templates/meta-plan/` for an applicable **meta-plan** template (stage `meta-plan`; see “Workflow folders”).
2. If no applicable template exists, the agent MUST follow the general template rule (create + HALT).
3. Using the selected or newly created template, the agent MUST generate a meta-plan that:
   - Identifies workstreams.
   - Identifies dependencies.
   - Identifies unknowns and open questions for the operator.
4. The agent MUST review the meta-plan for internal consistency and obvious gaps.

### 3. High-level plan

1. The agent MUST search the templates tree under `.pipeline-x/templates/` for an applicable **plan** template (stage `plan`).
2. If no applicable template exists, the agent MUST follow the general template rule (create + HALT).
3. Using the meta-plan and plan template, the agent MUST generate a high-level plan that:
   - Defines phases / workstreams.
   - Includes at least one explicit acceptance check.
   - For non-trivial work, SHOULD list key constraints and risks.
4. The agent MUST persist the high-level plan under `.pipeline-x/plans/` using the naming conventions in “Workflow folders”.
5. The agent MUST review the high-level plan for clarity, coherence, and alignment with the request.

**Approval gate – high-level plan**

- If not in `--full-auto`:
  - The agent MUST present the high-level plan to the operator.
  - The agent MUST update `.pipeline-x/states/agent_state.json` with the approval status.
  - The agent MUST HALT until the operator explicitly resumes.

### 4. Low-level plan

1. From the approved high-level plan, the agent MUST derive a low-level, execution-ready plan that:
   - Breaks work into concrete, ordered steps.
   - Includes many objectively verifiable acceptance checks.
2. The agent MUST persist the low-level plan under `.pipeline-x/plans/` using the naming conventions.
3. The agent MUST review the low-level plan for:
   - Granularity.
   - Dependency ordering.
   - Testability against acceptance checks.

**Approval gate – low-level plan**

- If not in `--full-auto`:
  - The agent MUST present the low-level plan to the operator.
  - The agent MUST update `.pipeline-x/states/agent_state.json` with the approval status.
  - The agent MUST HALT until the operator explicitly resumes.

### 5. TODOs

1. The agent MUST generate actionable TODO items derived from the low-level plan, one TODO item per file. Each TODO:
   - MUST have clear preconditions and expected outputs.
   - MUST have at least one acceptance check with an objective pass/fail condition.
2. The agent MUST persist TODOs under `.pipeline-x/todos/` using the naming conventions in “Workflow folders”.
3. The agent MUST review the TODO set for:
   - Completeness.
   - Correct dependency ordering.
   - Traceability back to the low-level plan.

**Approval gate – TODOs**

- If not in `--full-auto`:
  - The agent MUST present the TODOs to the operator.
  - The agent MUST update `.pipeline-x/states/agent_state.json` with the approval status.
  - The agent MUST HALT until the operator explicitly resumes.

### 6. Execution and verification

1. The agent MUST execute the approved TODOs (and thereby the plan).
2. For each completed TODO, the agent MUST:
   - Evaluate the defined acceptance checks.
   - Record pass/fail and any relevant notes in the appropriate artifacts (plans, reports, or state).
3. The agent MUST log significant actions, decisions, and outcomes as required by the surrounding system.

---

## Workflow folders

Directory and naming conventions are normative.

$1 = {coding-tasks|ingestion|meta-plan|high-plan|todo-plan|processing|reporting|machine-state|output|verification|workflows}

- Plans  
  - Path pattern:  
    `.pipeline-x/plans/plan-{four-byte-rolling-id)-{very-nice-five-word-slug}.md`  
  - Contains high-level and low-level plans.

- Reports  
  - Path pattern:  
    `.pipeline-x/reports/report-$1-{four-byte-rolling-id)-{very-nice-five-word-slug}.json`  
  - Contains execution and verification reports.

- TODOs  
  - Path pattern:  
    `.pipeline-x/todos/todo-{plan|spec|deliver}-$1-{four-byte-rolling-id)-{very-nice-five-word-slug}.json`  
  - Contains structured TODO sets keyed by work kind and stage.

- Scripts  
  - Directory:  
    `.pipeline-x/scripts/`  
  - Contains workflow helper scripts.

- States  
  - Directory:  
    `.pipeline-x/states/`  
  - Contains machine state artifacts (including `agent_state.json`).

- Templates  
  - Path pattern:  
    `.pipeline-x/templates/{coding-tasks|ingestion|meta-plan|high-plan|todo-plan|processing|reporting|machine-state|output|verification|workflows}/template-$1-{4-byte-rolling-id)-{very-nice-five-word-slug}.json`  
