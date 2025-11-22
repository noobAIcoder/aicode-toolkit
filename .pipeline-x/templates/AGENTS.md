# REPO_ROOT/.pipeline-x/templates/AGENTS.md

Purpose: Minimal reference for template structure and naming.

---

## Template Folders

- templates/coding-tasks/ - codebase registry, module specifications, ui specifications, debugging, etc.
- templates/ingestion/ — input normalization and verification.
- templates/machine-state/ - template for storing state of the coding agent.
- templates/meta-plan/ — deconstructing user request and inferring what requires planning.
- templates/high-plan/ — high-level plans.
- templates/todo-plan/ — detailed actionable TODOs, one per file.
- templates/output/ — unify and normalize agent output.
- templates/processing/ — agent logic and scripts.
- templates/reporting/ — agent reports.
- templates/verification/ — templates to plan and verify agent output.
- templates/workflows/ — agent workflows in template form.


---

## Helper Folders

- templates/template-plans/
- templates/tmp/

---

## Naming Convention

- Plans: `.pipeline-x/plans/plan-{2-byte-id}-{very-nice-five-word-slug}.md`.
- High-level plans: `.pipeline-x/templates/high-plan/template-high-plan-{id}-{slug}.json`.
- Meta-plans: `.pipeline-x/templates/meta-plan/template-meta-plan-{id}-{slug}.json`.
- Stage templates: `.pipeline-x/templates/{ingestion|processing|output|verification}/template-<category>-<stage>-pipeline-x.json`.
- Workflows: `.pipeline-x/templates/workflows/workflow-<class>-<subclass>-{id}.json`.

## Templates Registry

- `.pipeline-x/templates/template-registry.json` (short agent-ingestion registry with allowed templates and usage tips)

---
