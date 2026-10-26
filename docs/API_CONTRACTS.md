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
- Coordinates: EPSG:4326; GeoJSON/array order is always `[longitude, latitude]`; reject invalid ranges or ambiguous/reversed input (`DECISIONS.md`, DEC-028).
- Distances: expose documented metre values/thresholds derived through authoritative PostGIS geography operations; clients do not determine eligibility using local degree calculations.

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

### In-App Messaging

- Create and retrieve authoritative messages through REST endpoints; exact resource names remain TBD (`DECISIONS.md`, DEC-027).
- Message creation requires a client-generated idempotency identifier and current conversation authorization.
- Responses use server identifiers, receipt timestamps, and deterministic per-conversation ordering; client timestamps do not establish canonical order.
- Catch-up uses opaque cursors. Clients must tolerate duplicate responses/notifications and synchronize after reconnect, resume, push, or cursor gaps.
- Android push is a minimal, non-sensitive synchronization hint only. It is not the message, proof of delivery/read, or authorization.
- Support/Admin message access is case-scoped and audited. Permitted attachments reference evidence handled under DEC-026 rather than using a separate chat-upload path.
- Polling intervals, page limits, acknowledgement/read-state contracts, and moderation/redaction operations remain TBD and must not be invented from this transport decision.

### Location and Live Sharing

- Matching and event-evidence operations accept approved foreground/manual locations with explicit purpose and DEC-028 coordinate semantics (`DECISIONS.md`, DEC-035).
- Location samples include server receipt time, device-recorded time, reported horizontal accuracy, source/permission precision, transaction/workflow purpose, and idempotency/sequence metadata as applicable.
- Active live-sharing operations require current transaction authorization and explicit session lifecycle; exact start/stop/sample/batch resources remain TBD.
- APIs tolerate duplicate, delayed, and out-of-order samples and do not treat missing data as automatic user fault.
- The server validates state, freshness, plausibility, retention, and current sharing authorization; client permission/service state never establishes workflow truth.
- Precise coordinates never appear in push payloads, problem details, ordinary logs, or disclosure-unsafe responses.

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

- Flutter transport: Generate a pinned OpenAPI Generator `dart-dio` package behind repositories after the mandatory OpenAPI 3.1 and Kirim contract-shape spike (`DECISIONS.md`, DEC-031).
- Generated output/configuration is committed initially; CI regenerates from the canonical OpenAPI artifact and fails on drift. Generated files are never manually edited.
- Unsupported bounded operations may use documented handwritten Dio adapters without weakening the approved API contract; broad generator incompatibility requires a new decision.
- Schema linting and exact canonical-artifact workflow: TBD.
- Consumer/provider contract tests: TBD, including RFC 9457, nullable/omitted fields, enums, headers, cursor pages, multipart/binary transfer, and additive compatibility.
- Breaking-change detection: TBD.
- Deprecation period: TBD.

## 6. Open Questions

- [ ] What clients consume the API?
- [ ] Is public API access in scope?
- [ ] Which operations require idempotency or asynchronous processing?
- [ ] What latency and availability targets apply?
