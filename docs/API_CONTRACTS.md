# API Contracts

> Status: Draft — define contracts from approved user flows before implementation.

## 1. API Overview

- **Style:** Pragmatic REST-style HTTP JSON with explicit action subresources (`DECISIONS.md`, DEC-018).
- **Base path:** `/api/v1` (hostnames/environment URLs remain TBD).
- **Versioning strategy:** URL major versioning; compatible additive changes remain in `v1`; breaking changes require review, migration guidance, and an approved transition (`DECISIONS.md`, DEC-019).
- **Contract representation:** OpenAPI generated and validated in CI; exact source/generation workflow remains TBD.
- **Compatibility policy:** Preserve documented `v1` behavior for supported clients; define deprecation windows and breaking-change checks before implementation.

## 2. Cross-Cutting Conventions

### Authentication and Authorization

- Authentication mechanism: TBD
- Authorization model: TBD
- Service-to-service authentication: TBD

### Data Format

- Content type: TBD
- Field naming: TBD
- Date/time format: ISO 8601 UTC (confirm)
- Identifier format: TBD
- Null/omitted-field semantics: TBD

### Errors

Use RFC 9457 Problem Details (`application/problem+json`) with stable Kirim extensions (`DECISIONS.md`, DEC-019):

```json
{
  "type": "https://api.kirim.example/problems/transaction-state-conflict",
  "title": "Transaction state conflict",
  "status": 409,
  "detail": "The transaction can no longer be confirmed.",
  "instance": "/api/v1/transactions/example/confirmations/example",
  "code": "TRANSACTION_STATE_CONFLICT",
  "requestId": "request-id"
}
```

The example hostname and resource paths are placeholders, not approved endpoint names. Document status/problem codes per operation. Human-readable text may change or be localized; clients depend on stable `code` semantics. Never leak stack traces, SQL/database internals, secrets/tokens, provider credentials, private resource existence, or internal fraud/security reasoning.

### Collection Operations

- Pagination: Opaque cursor pagination by default with deterministic ordering and a unique tie-breaker.
- Filtering and sorting: Endpoint-specific allowlists; reject unknown/invalid fields.
- Page limits, cursor encoding/expiry, direction behavior, and endpoint-specific bounded offset exceptions: TBD.

### Reliability

- Idempotency: Require client-generated `Idempotency-Key` for applicable consequential creation/action requests; scope and fingerprint requests; replay matching results without duplicate effects; reject conflicting reuse.
- Request timeout: TBD.
- Rate limits: TBD.
- Retry guidance: Retry only documented safe/idempotent operations and preserve the same idempotency key for the same intended operation.
- Concurrency control: Strong ETag/`If-Match` for eligible editable resources; transactional state checks, locking/versioning, uniqueness, and constraints for workflow actions. Use `412` for failed explicit preconditions and `409` for current domain-state conflicts.

### Evidence File Transfer

- Upload and download bytes through authenticated, currently authorized backend endpoints; clients do not connect directly to production object storage (`DECISIONS.md`, DEC-026).
- Upload operations are idempotent and use bounded streaming multipart requests; exact endpoint names, field names, limits, formats, and timeout/retry rules remain TBD.
- The backend controls object keys and evidence purpose. Filenames and client media types are untrusted metadata.
- Upload responses expose processing state rather than imply immediate acceptance; unvalidated evidence cannot satisfy a workflow requirement.
- Download operations reauthorize current relationship/case assignment and return safe media/disposition/sniffing headers. Storage keys are never authorization capabilities.
- Support/Admin evidence reads are case-scoped and audited.
- OpenAPI contracts describe metadata/state operations; binary streaming behavior and client cancellation/retry tests must also be documented explicitly.

## 3. REST Operation Template

### `[METHOD] /resource`

**Requirement:** `FR-XXX`
**Purpose:** TBD
**Required permission:** TBD

#### Request

| Location | Name | Type | Required | Validation/description |
|---|---|---|---:|---|
| TBD | TBD | TBD | Yes | TBD |

```json
{}
```

#### Success Response — `[status]`

```json
{}
```

#### Errors

| Status/code | Condition | Client action |
|---|---|---|
| TBD | TBD | TBD |

## 4. Events/Webhooks (if applicable)

| Event | Producer | Consumer | Delivery guarantee | Schema version |
|---|---|---|---|---|
| TBD | TBD | TBD | TBD | TBD |

Document signing, ordering, duplication, retry, dead-letter, and replay behavior.

## 5. Contract Validation

- Schema linting/generation: TBD
- Consumer/provider contract tests: TBD
- Breaking-change detection: TBD
- Deprecation period: TBD

## 6. Open Questions

- [ ] What clients consume the API?
- [ ] Is public API access in scope?
- [ ] Which operations require idempotency or asynchronous processing?
- [ ] What latency and availability targets apply?
