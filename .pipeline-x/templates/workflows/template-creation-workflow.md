# Template Creation Workflow

Purpose: Create a minimal, reusable template for recurring requests with lowest effort.

## Steps

1. Execute the user request normally (keep notes on repeatable parts).
2. Write a brief plan and save it to `.pipeline-x/plans/plan-{2-byte-id}-{very-nice-five-word-slug}.md`.
3. Identify repeatable elements (inputs, steps, acceptance) and define placeholders.
4. Draft a JSON template with the minimum fields: `name`, `description`, `version`, `category`, `placeholders`, `steps`, `acceptance`.
5. Save the draft under the correct category path using the standard name:
   - Path: `.pipeline-x/templates/{ingestion|meta-plan|high-plan|processing|output|verification|workflows|machine-state}/`
   - Name: follow the folder’s naming convention (e.g., `template-high-plan-{id}-{slug}.json` or `template-<category>-<stage>-pipeline-x.json`)
6. HALT for operator review and approval before promoting the draft.

## Acceptance Check

- A plan file exists in `.pipeline-x/plans/` with the correct naming.
- A draft template JSON exists under the chosen `.pipeline-x/templates/<category>/` path with the correct naming and fields.

## PlusCal (pseudocode)

--algorithm TemplateCreation
variables req, planFile, templateFile, approved \in {FALSE, TRUE};
begin
  Init:
    req := "user_request"; \* Captured from the operator context
    planFile := CreatePlan(req); \* Save to .pipeline-x/plans/plan-..md
    assert FileExists(planFile);

  IdentifyRepeatables:
    \* Extract inputs, steps, and acceptance from executed work
    \* Define placeholders for variable parts

  DraftTemplate:
    templateFile := SaveTemplate(
      category,                   \* ingestion|meta-plan|planning|processing|output|verification|workflows|machine-state
      kind,                       \* plan|act|report|test
      id, slug,                   \* short id + four-word slug
      fields                      \* minimal JSON fields
    );
    assert FileExists(templateFile);

  Review:
    approved := FALSE;
    await approved = TRUE;       \* HALT until operator approves
end algorithm
