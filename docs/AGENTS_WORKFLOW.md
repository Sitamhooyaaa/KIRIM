# Multi-Agent Workflow

> Status: Initial governance model. Adjust to the project's actual complexity.

## 1. Objective

Enable parallel work without duplicated effort, hidden decisions, or conflicting edits. The project owner has final product authority. The lead agent coordinates planning, delegation, integration, and reporting.

## 2. Roles

### Project Owner

- Sets goals, priorities, constraints, and acceptance decisions.
- Approves material product, architecture, security, cost, and scope changes.

### Lead Agent

- Clarifies requests and identifies risks.
- Breaks approved work into independent assignments.
- Assigns ownership and integration order.
- Reviews evidence, resolves conflicts, and reports to the owner.
- May recommend alternatives and should challenge unsafe or incorrect assumptions respectfully.

### Specialist Agents

Examples: product/UX, frontend, backend, data, QA, security, and DevOps.

- Work only within the assigned scope.
- Inspect existing code/contracts before editing.
- Avoid files owned by another active assignment unless coordinated.
- Return implementation details and verification evidence to the lead.

## 3. Work Item States

```text
Proposed -> Clarifying -> Ready -> Assigned -> In Progress -> Review
         -> Blocked                            -> Changes Requested
Review -> Verified -> Done
```

Only work with clear acceptance criteria should enter **Ready**. **Done** requires verification and owner acceptance when requested.

## 4. Assignment Contract

Each delegated task should include:

- Work item ID and objective.
- Relevant requirements/acceptance criteria.
- In-scope and out-of-scope areas.
- Files or subsystem owned.
- Constraints and approved dependencies.
- Expected tests/checks.
- Required output/handoff format.

## 5. Agent Handoff

Each agent returns:

```text
Status: completed | partial | blocked
Summary:
Files changed:
Commands/checks run:
Results:
Decisions/assumptions:
Risks/open questions:
Recommended next action:
```

A report is not proof: the lead reviews diffs and, where practical, reruns relevant checks.

## 6. Parallel-Work Rules

- Parallelize research and work on non-overlapping files/components.
- Serialize changes to shared contracts, schemas, lockfiles, and central configuration.
- Establish API/schema contracts before parallel client and server implementation.
- Never have two agents edit the same file simultaneously without an explicit merge plan.
- Specialist agents recommend architecture; the lead and owner approve material changes.

## 7. Decision and Escalation Rules

Escalate to the owner when a choice changes:

- Product scope or acceptance criteria.
- Public API or persistent data compatibility.
- Security/privacy posture.
- Recurring cost, vendor commitment, or licensing.
- Delivery timeline or significant technical risk.

Document consequential technical decisions as ADRs. If blocked, stop speculative implementation and present options with trade-offs.

## 8. Quality Gates

Before integration:

- Scope and acceptance criteria are satisfied.
- Relevant formatting, linting, type checking, and tests pass.
- Security, privacy, migration, and rollback implications are considered.
- Contracts and documentation match behavior.
- No credentials, generated clutter, debug code, or unrelated changes are included.

Before release:

- Critical user flows pass end-to-end validation.
- Configuration, migrations, monitoring, backup, and rollback are ready.
- Known limitations are documented and accepted.

## 9. Conflict Resolution

1. Preserve both agents' work; do not overwrite blindly.
2. Reconfirm the source-of-truth requirement or contract.
3. Prefer the smallest solution that meets accepted requirements.
4. Ask the owner when the conflict is a product or risk decision.
5. Record the resolution if it affects future work.
