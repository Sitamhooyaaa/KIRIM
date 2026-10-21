# AGENTS.md

This file governs all human and AI contributors in this repository. More specific `AGENTS.md` files in subdirectories may add local rules; the closest applicable file takes precedence.

## Mission

Build Kirim according to explicit, approved requirements. The project owner has final authority. Contributors should respectfully identify incorrect assumptions, risks, and better alternatives rather than silently agreeing.

## Current State

The project is in discovery/planning. Documentation templates are not approved requirements. Values marked `TBD`, examples, and proposed conventions must not be treated as implementation decisions.

## Sources of Truth

Read relevant documents before making changes:

1. `docs/REQUIREMENTS.md` — product scope, user stories, and acceptance criteria.
2. `DECISIONS.md` — accepted consequential decisions and their rationale.
3. `docs/TECH_STACK.md` — approved architecture, tooling, and standards.
4. `docs/DATABASE_SCHEMA.md` — persistent data model and migrations.
5. `docs/API_CONTRACTS.md` — external and internal interfaces.
6. `docs/SYSTEM_PROMPT.md` — AI behavior and project boundaries.
7. `docs/AGENTS_WORKFLOW.md` — delegation, handoff, and quality gates.

If documents conflict, stop and raise the conflict. Do not guess. Product requirements outrank implementation convenience, while security and data-safety concerns must always be escalated.

## Required Workflow

1. Inspect the repository and applicable documentation.
2. Restate the objective and identify ambiguities or high-impact assumptions.
3. Make a small plan tied to requirement IDs/acceptance criteria.
4. Obtain approval before material architecture, schema, dependency, public-contract, security, or scope changes.
5. Implement the smallest coherent change; avoid unrelated refactors.
6. Add or update tests for changed behavior.
7. Run the relevant format, lint, type, test, and build checks defined by the project.
8. Review the diff for secrets, unrelated edits, generated files, and documentation drift.
9. Report changes, checks actually run, limitations, and next steps.

## Engineering Rules

- Never invent missing product behavior. Ask or present explicit options and trade-offs.
- Preserve backward compatibility unless a breaking change is approved and documented.
- Validate input at every external boundary and enforce authorization server-side.
- Do not log secrets, tokens, credentials, or unnecessary personal data.
- Keep domain logic independent from UI, transport, and persistence where practical.
- Prefer simple, readable code over premature abstraction.
- Do not add a dependency when the platform or existing stack adequately solves the problem.
- Pin versions according to the selected ecosystem and update lockfiles intentionally.
- Comments should explain why, not restate what the code does.
- Do not suppress errors or tests merely to make checks pass.

## Testing

Every behavior change should include an appropriate test. Cover at minimum:

- The expected path.
- Validation and authorization failures.
- Important boundaries and edge cases.
- Regression behavior for bug fixes.

Never state that tests pass unless they were run. If checks cannot run, explain why and provide the exact recommended command.

## Data and Migrations

- Do not edit an already-applied migration; add a new one.
- Back up or provide a recovery plan before destructive operations.
- Make migrations safe for the deployment strategy and expected data volume.
- Keep schema definitions, API contracts, fixtures, and tests synchronized.
- Use synthetic data in development/tests unless explicitly authorized otherwise.

## Git and Change Hygiene

- Do not discard or overwrite uncommitted work from others.
- Keep changes focused and commits logically separable.
- Do not commit secrets, local environment files, build output, caches, or editor metadata.
- Avoid force pushes, destructive resets, or history rewrites without explicit owner approval.
- Name branches/commits according to the convention selected in `TECH_STACK.md`.

## Multi-Agent Coordination

Follow `docs/AGENTS_WORKFLOW.md`.

- The lead agent owns decomposition, assignments, integration, and the final report.
- Delegate independent, clearly bounded tasks; avoid concurrent edits to the same files.
- Shared contracts, schemas, lockfiles, and central configuration require serialized ownership.
- Subagents must report files changed, commands run, results, assumptions, and blockers.
- The lead must inspect and validate agent output before accepting it.

## Definition of Done

A task is done only when:

- Approved acceptance criteria are met.
- Relevant automated checks pass, or documented exceptions are accepted.
- Security, privacy, accessibility, migration, and rollback impacts are addressed as applicable.
- Documentation and contracts reflect the implemented behavior.
- No known critical defect or unresolved requirement ambiguity remains.
- The completion report is accurate and concise.
