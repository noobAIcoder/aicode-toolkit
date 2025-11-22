# Template Registry Check and Populate Workflow

 Purpose: Keep `.pipeline-x/templates/template-registry-full.json` synchronized with actual templates so policy tools can trust it. The short agent-ingestion registry lives at `.pipeline-x/templates/template-registry.json` and is **not** the target of this workflow.

## Preconditions

- Read `AGENTS.md`, `templates/AGENTS.md`, and `.pipeline-x/templates/template-registry-full.json` for naming and schema rules.
- List current files under `.pipeline-x/templates/` (e.g., `find .pipeline-x/templates -type f`).
- Have a JSON linter available (`jq`, `python -m json.tool`, etc.).

## Steps

1. Inventory templates: record each JSON/MD file path, category folder, name, and version; note ones missing from the full registry catalog under `.pipeline-x/templates/`.
2. Validate registry file structure:
   - Ensure `.pipeline-x/templates/template-registry-full.json` exists and parses as JSON.
   - Confirm top-level fields: `name`, `description`, `version`, `category`, and `registry{}` with `id`, `title`, `schema`, `templates_root`, `generated_at`, and `allowed_templates`.
3. Check allowed template entries:
   - Verify each entry holds `id`, `name`, `category`, `path`, `version`, and `enabled`.
   - Ensure `path` exists under `.pipeline-x/templates/` and matches the `category` folder.
   - Reject duplicate `id` or `path` values.
4. Populate missing data:
   - For each template not listed, gather metadata (id, name, version, category, description, tags, owner) and append a new entry modeled after the processing template catalog.
   - Update `generated_at` to the current ISO-8601 timestamp.
   - If entries reference obsolete files, disable them (set `enabled` false) or remove them once confirmed.
   - Keep the short registry `.pipeline-x/templates/template-registry.json` lean; update it separately only to reflect the concise ingestion view once the full registry is accurate.
5. Re-validate:
   - Re-run the JSON linter and any internal checks (e.g., `jq` queries for duplicates).
   - Cross-check against folder inventory to confirm 1:1 coverage for all enabled templates.
6. HALT for operator review; share the diff and inventory summary before merging the changes.

## Acceptance Check

- `.pipeline-x/templates/template-registry-full.json` parses, contains required metadata, and references only existing files.
- Every template under `.pipeline-x/templates/` that MUST be tracked has a corresponding `allowed_templates[]` entry with accurate fields.
- Duplicate ids/paths eliminated; `generated_at` updated.
- Operator review completed before promotion; AGENTS docs remain pointed at the short registry only.

## PlusCal (pseudocode)

--algorithm RegistryCheckPopulate
variables regFile, inventory, entries, missing, ok \in {FALSE, TRUE};
begin
  Init:
    regFile := LoadJSON(".pipeline-x/templates/template-registry-full.json"); \* parse registry file
    inventory := ListTemplates(".pipeline-x/templates/"); \* gather actual template files
    entries := regFile.registry.allowed_templates;
    ok := TRUE;

  ValidateRegistry:
    if regFile.name = Nil \/ regFile.version = Nil \/ regFile.registry = Nil then
      ok := FALSE;
    end if;

  CheckEntries:
    missing := Compare(inventory, entries); \* detect untracked or stale templates
    \* Verify each entry has required fields and paths exist
    if ExistsInvalidEntry(entries) then ok := FALSE; end if;

  Populate:
    if Cardinality(missing) > 0 then
      entries := entries \o BuildEntries(missing); \* append new metadata
      regFile.registry.generated_at := NowISO();
    end if;

  Review:
    assert ok = TRUE; \* ensure validations passed
    AwaitOperatorApproval(); \* HALT until human review completes
end algorithm
