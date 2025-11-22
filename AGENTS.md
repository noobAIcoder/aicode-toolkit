# REPO_ROOT/AGENTS.md

## Repo-wide Rules

- All requirements in this document MUST use RFC 2119/8174 (BCP 14) terms.
- If any action requires sudo, the agent MUST output the complete sudo command and HALT until the operator executes it manually.
- Agent MUST operate in greenfield mode and MUST prefer the simplest viable solution.
- Target platforms MUST be Windows and WSL only.
- Pseudocode MUST be in well commented inline PlusCal (docs, comments, issues).
- Agent MUST NOT overengineer (no unnecessary abstractions, deps, or complexity beyond the request).
- Agent MUST NOT generate or plan source code unless explicitly requested by the user. When allowed, limit offers to one sentence with no links, code blocks, or bullets. Example: 'I can apply these edits now if you want.'
- Non-code documentation and configuration MAY be created or edited unless the user restricts them.

## File Reference Standard

- Line and column 1-based. If the column is unknown, use 1.
- Each reference MUST be on its own line directly after the summary; paths are workspace-relative.
- For multiple references, list each on its own line.
- If no exact line is obvious, use the first line of the most relevant block or the file's first line.
- Output format:
    {bullet} {problem; impact; cause; fix}
    `path/to/file.ext:line:column`
    Fix:
    [drop-in plain text patch, no bullets no numbers no diff marks]
    [separator line]

## Standard Workflow

- Deconstruct and analyze the user request and what exactly it implies and demands. Read 10 first lines of all relevant files. If anything ambiguous, ask the operator and HALT.
- Check `.pipeline-x/templates/meta-plan/` for an applicable meta-plan template
  - If not exists, ask the operator for guidance and HALT pending approval
  - If exists, read.
- Generate meta-plan: what needs planning.
- Review meta-plan.
- Check `.pipeline-x/templates/planning/` for an applicable plan template
  - If not exists, ask the operator for guidance and HALT pending approval
  - If exists, read.
- Generate high level plan (at least one acceptance check; optional constraints/risks when non-trivial).
- Save plan in `.pipeline-x/plans/` in properly formatted and sectioned markdown.
- Review and polish the plan.
- If not in '--full-auto' mode, present the plan for operator approval.
- HALT for operator review unless in --full-auto mode
- Execute the plan.

## Platform Conventions

- Shells: Bash on WSL; PowerShell on Windows; prefer POSIX-compatible commands.
- Newlines: Agent MUST preserve the existing newline style in edited files (LF in WSL contexts; CRLF in Windows-only files).

## Workflow folders

`.pipeline-x/plans/plan-{2-byte-id}-{very-nice-five-word-slug}.md` - plans
`.pipeline-x/reports/report-{2-byte-id}-{very-nice-five-word-slug}.json`
`scripts/` - repo helper scripts
`.pipeline-x/scripts/` - workfow helper scripts
`.pipeline-x/states/` - machine states
`.pipeline-x/templates/{coding-tasks|ingestion|meta-plan|high-plan|todo-plan|processing|reporting|machine-state|output|verification|workflows}/template-$1-{4-byte-rolling-id)-{very-nice-five-word-slug}.json` - templates
`tests/` - repo tests
`tmp/` - repo temporary
`lib/` - repo downloaded libraries
