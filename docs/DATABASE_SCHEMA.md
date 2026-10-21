# Database Schema

> Status: Draft — no database technology or domain model has been approved.

## 1. Data Design Principles

- Model the approved domain, not hypothetical future features.
- Use stable identifiers and explicit ownership boundaries.
- Enforce integrity with constraints in addition to application validation.
- Store timestamps consistently (prefer UTC) and document precision.
- Classify sensitive data and minimize its collection.
- Design migrations to be reversible or provide a tested recovery procedure.

## 2. Data Classification and Lifecycle

| Data category | Examples | Classification | Retention | Deletion/export rule |
|---|---|---|---|---|
| TBD | TBD | Public/Internal/Confidential/Restricted | TBD | TBD |

## 3. Entity Relationship Overview

```mermaid
erDiagram
    PLACEHOLDER {
        uuid id PK
        timestamp created_at
        timestamp updated_at
    }
```

Replace the placeholder only after requirements identify the core entities.

## 4. Table Definitions

### `placeholder` (remove when the real schema is defined)

| Column | Type | Null | Default | Constraints | Description |
|---|---|---:|---|---|---|
| `id` | UUID/TBD | No | Generated | Primary key | Stable identifier |
| `created_at` | Timestamp/TBD | No | Current time | — | Creation time in UTC |
| `updated_at` | Timestamp/TBD | No | Current time | — | Last update time in UTC |

## 5. Relationships and Delete Behavior

| Parent | Child | Cardinality | On delete | Reason |
|---|---|---|---|---|
| TBD | TBD | TBD | Restrict/Cascade/Soft delete | TBD |

## 6. Indexes and Access Patterns

Indexes must follow measured or clearly expected access patterns.

| Table | Query/access pattern | Index | Unique | Notes |
|---|---|---|---:|---|
| TBD | TBD | TBD | No | TBD |

## 7. Authorization and Tenant Isolation

- Ownership model: TBD
- Tenant boundary: TBD
- Row-level access strategy: TBD
- Administrative access/auditing: TBD

## 8. Migration Strategy

- Tool: TBD
- Naming/version convention: TBD
- Backward-compatibility window: TBD
- Backfill strategy: TBD
- Rollback/recovery procedure: TBD
- Migration tests: TBD

## 9. Backup and Recovery

- Backup cadence: TBD
- Retention: TBD
- Recovery point objective (RPO): TBD
- Recovery time objective (RTO): TBD
- Restore-test cadence: TBD

## 10. Open Questions

- [ ] Which datastore best fits the approved access and consistency needs?
- [ ] Which records contain personal or regulated data?
- [ ] Are soft deletion, audit history, or event history required?
- [ ] What are the expected data volume and growth rate?
