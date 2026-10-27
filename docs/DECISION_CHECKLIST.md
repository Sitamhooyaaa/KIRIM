# LLM-Assisted Project Decision Checklist

> Status: Living planning checklist
> Last updated: 2026-10-05

Use this file as a reminder of the major decisions required to build Kirim with human and AI/LLM contributors.

## Rules for Humans and Agents

- This checklist tracks progress; it is not the authority for accepted decisions.
- `DECISIONS.md` is the durable source of accepted decisions and rationale.
- `docs/REQUIREMENTS.md` defines product behavior and constraints.
- `docs/TECH_STACK.md` summarizes selected architecture, technologies, and standards.
- Never mark a decision complete until the Product Owner explicitly approves it and the decision is recorded in the appropriate source-of-truth files.
- Do not infer a provider, framework, library, version, contract, or product behavior from an unchecked item.
- Verify current versions and provider capabilities using official documentation, sandbox tests, and applicable contractual/legal review before selection.
- When a decision is revisited, update or supersede its record; do not silently rewrite history.

## A. Architecture Overview — Complete

- [x] **DEC-001:** Modular monolith architecture.
- [x] **DEC-002:** One Flutter user app and separate minimal Admin web console.
- [x] **DEC-003:** API process, durable Worker, and nine domain modules.
- [x] **DEC-004:** Managed commodity infrastructure with Kirim-owned domain logic.
- [x] **DEC-005:** Managed application platform deployment model.

## B. Primary Technology Stack — Complete So Far

- [x] **DEC-006:** Flutter/Dart mobile client with Android-first release.
- [x] **DEC-007:** React/TypeScript Admin SPA.
- [x] **DEC-008:** Python/FastAPI backend.
- [x] **DEC-009:** PostgreSQL/PostGIS authoritative database.
- [x] **DEC-010:** PostgreSQL-backed durable jobs and no initial shared cache.

## C. Backend and API Decisions

- [x] **DEC-018:** Pragmatic REST-style JSON API with OpenAPI and explicit action subresources.
- [x] **DEC-019:** `/api/v1`, RFC 9457 errors, cursor pagination, allowlisted filters/sorts, idempotency keys, and optimistic/domain concurrency controls.
- [x] **DEC-020:** CPython 3.14 runtime line; compatibility-test and lock exact FastAPI/Pydantic/Starlette/ASGI dependency set at scaffolding.
- [ ] Python package and dependency-management tool — intentionally deferred to backend scaffolding; `uv` is the leading candidate, subject to Windows/Linux, deployment, and dependency-group verification.
- [x] **DEC-021:** Psycopg 3 with synchronous PostgreSQL access initially for API and Worker.
- [x] **DEC-022:** SQLAlchemy 2 hybrid with typed ORM mappings and explicit Core/parameterized SQL where appropriate.
- [x] **DEC-023:** Alembic with reviewed committed migrations, controlled release execution, and forward-safe PostgreSQL/PostGIS policy.
- [x] **DEC-024:** Small explicitly bounded SQLAlchemy `QueuePool` per long-running process; exact global connection budget deferred to deployment selection.
- [ ] PostgreSQL job implementation or library — intentionally deferred to backend scaffolding; Procrastinate is the leading candidate, subject to the approved transaction, Python 3.14, Psycopg 3/SQLAlchemy 2, crash-recovery, and connection-budget spike.
- [x] **DEC-025:** Job-class-specific at-least-once handling with idempotency, bounded jittered retries, short leases, reconciliation, and visible permanent failure.
- [x] **DEC-026:** Backend-streamed evidence transfer to private object storage with quarantine processing, immutable originals, controlled derivatives, and audited access.
- [x] **DEC-027:** Durable REST messaging with PostgreSQL ordering, push hints, and foreground cursor refresh; no initial persistent socket.
- [x] **DEC-028:** EPSG:4326 canonical coordinates; geography metre distances, geometry versioned geofences, boundary-inclusive coverage, and GiST indexing.

## D. Authentication and Authorization

- [x] **DEC-011:** Managed authentication proof with Kirim-owned accounts and authorization.
- [x] **DEC-012:** Email/password login with mandatory email and SMS phone verification; social login deferred for MVP.
- [x] **DEC-013:** Native OAuth Authorization Code with PKCE/OIDC, provider API access tokens, and provider-managed refresh sessions.
- [x] **DEC-014:** Kirim server-side opaque Admin session using a Secure HttpOnly cookie.
- [x] **DEC-014:** Mandatory TOTP MFA for Administrator/Support accounts; phishing-resistant MFA deferred for evaluation.
- [x] **DEC-012:** One-time SMS code for phone verification; provider remains undecided.
- [x] **DEC-015:** Provider-managed email verification and email-only password-reset links with controlled contact changes.
- [x] **DEC-016:** Deny-by-default hybrid module-owned authorization policies.
- [x] **DEC-017:** Provider revocation plus Kirim security version/status and compromised-account hold.

## E. Flutter Mobile Decisions

- [ ] Flutter and Dart versions.
- [ ] Supported Android OS versions and device baseline.
- [x] **DEC-029:** Pragmatic feature-first Flutter architecture with optional layers based on actual complexity.
- [x] **DEC-029:** Riverpod with manual providers initially, immutable unidirectional state, and widget-local state where appropriate.
- [x] **DEC-030:** `go_router` with manual routes, verified HTTPS Android App Links, allowlisted targets, and Riverpod-backed bootstrap/auth redirects.
- [x] **DEC-031:** Dio plus a pinned generated OpenAPI `dart-dio` transport package behind repositories, subject to a mandatory compatibility spike.
- [x] **DEC-032:** Provider SDK-managed mobile refresh session, transient access tokens where practical, and narrowly scoped Keystore-backed fallback with credentials excluded from backup.
- [x] **DEC-033:** Online-first Flutter client with in-memory server state, minimal non-sensitive preferences, controlled temporary files, and no offline consequential-action queue.
- [ ] **DEC-034 partial:** Provider-separated Flutter map/backend routing architecture approved; Google Maps is the leading candidate, but exact renderer/routing/geocoding vendor remains deferred pending licence/retention, quality, cost, privacy, and SDK verification.
- [x] **DEC-035:** Foreground location for matching/evidence plus explicit active-transaction foreground-service sharing; no unrestricted background-location permission initially.
- [x] **DEC-036:** Official `image_picker` with system camera, approved Photo Picker fallback, preview/retake, original-byte preservation, Android lost-result recovery, and existing secure upload pipeline.
- [x] **DEC-037:** Direct FCM via `firebase_messaging`, backend HTTP v1 sending, authenticated installation registration, privacy-minimal payloads, and server-authoritative catch-up.
- [x] **DEC-038:** Flutter `gen_l10n` with English/Bahasa Malaysia ARB resources, typed accessors, `intl`, English fallback, in-app override, and human-reviewed consequential copy.
- [x] **DEC-039:** Material 3 with a small semantic token layer, WCAG 2.2 AA and Android/Flutter accessibility gates, 48 dp targets, 200% text scaling, and TalkBack/manual testing.
- [x] **DEC-040:** Official Dart/Flutter unit, provider, widget, golden, and integration tooling plus selective Patrol native-system tests after a compatibility spike.
- [ ] Android signing and controlled distribution.

## F. React Admin Console Decisions

- [ ] React and TypeScript versions.
- [ ] Build tool.
- [ ] Routing approach.
- [ ] API data-fetching and cache approach.
- [ ] Forms and runtime validation.
- [ ] UI/component approach.
- [ ] Admin authentication and browser-session security.
- [ ] React testing tools.
- [ ] Admin build and deployment process.

## G. External Providers

- [ ] Payment provider.
- [ ] Payment webhook, reconciliation, refund, and payout integration.
- [ ] Maps/geocoding/routing provider.
- [ ] Android push-notification provider/configuration.
- [ ] SMS-delivery provider.
- [ ] Email-delivery provider.
- [ ] Private object-storage provider.
- [ ] File malware/content-safety approach.
- [ ] Managed application hosting provider.
- [ ] Hosting region and data-location policy.
- [ ] Managed PostgreSQL/PostGIS provider.
- [ ] Secrets-management provider.
- [ ] Logging, metrics, error-reporting, and alerting provider.

## H. Testing and Engineering Standards

- [ ] Backend unit, integration, contract, and end-to-end test tools.
- [ ] Real PostgreSQL/PostGIS test-environment approach.
- [ ] Payment-provider sandbox and fake-provider testing strategy.
- [ ] Formatting and linting tools.
- [ ] Strict Python and TypeScript type-checking tools.
- [ ] Coverage and critical-path testing policy.
- [ ] Security and dependency-scanning tools.
- [ ] API compatibility/breaking-change checks.
- [ ] Branch and commit conventions.
- [ ] Dependency-update policy.

## I. Repository and Delivery

- [ ] Monorepo or multiple repositories.
- [ ] Final repository/package structure.
- [ ] Local development environment.
- [ ] Configuration and environment-variable conventions.
- [ ] CI provider and quality gates.
- [ ] Deployment workflow.
- [ ] Database migration release process.
- [ ] Rollback and forward-recovery strategy.
- [ ] Staging-data and synthetic-fixture policy.
- [ ] Backup and restore implementation.
- [ ] Incident and pilot-pause implementation.

## J. Design Decisions After `TECH_STACK.md`

- [ ] Database entities, ownership, constraints, indexes, and retention.
- [ ] Complete transaction state machine.
- [ ] Financial ledger and reconciliation model.
- [ ] API resources, request/response schemas, and error codes.
- [ ] Evidence/file metadata and access model.
- [ ] Geofence and route-geometry schema.
- [ ] Audit-event structure.
- [ ] Payment-provider contract mapping.
- [ ] Deployment and support runbooks.

## Decision Timing

### Before Initial Scaffolding

- Authentication direction.
- API style.
- Backend data-access and migration tools.
- Repository structure.
- Core versions and package managers.
- Initial testing and quality tools.

### Before Implementing the Relevant Feature

- Maps and location.
- Files and evidence.
- Messaging and notifications.
- Payment provider and payment contracts.

### Before Stage B Real-User Pilot

- Production hosting and provider details.
- Real payment-provider contract and sandbox proof.
- Production SMS/email configuration.
- Backup and restoration proof.
- Security, privacy, support, and operational launch gates.

## Current Progress

- Major accepted decision records: **40**.
- Remaining planning decisions: approximately **18**, including final map/routing vendor selection.
- Current next topic: **Android signing and controlled distribution**.
