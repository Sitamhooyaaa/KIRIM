# LLM-Assisted Project Decision Checklist

> Status: Living planning checklist
> Last updated: 2026-10-18

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

## B. Primary Technology Stack — Review Draft Complete

- [x] **DEC-006:** Flutter/Dart mobile client with Android-first release.
- [x] **DEC-007:** React/TypeScript Admin SPA.
- [x] **DEC-008:** Python/FastAPI backend.
- [x] **DEC-009:** PostgreSQL/PostGIS authoritative database.
- [x] **DEC-010:** PostgreSQL-backed durable jobs and no initial shared cache.

## C. Backend and API Decisions

- [x] **DEC-018:** Pragmatic REST-style JSON API with OpenAPI and explicit action subresources.
- [x] **DEC-019:** `/api/v1`, RFC 9457 errors, cursor pagination, allowlisted filters/sorts, idempotency keys, and optimistic/domain concurrency controls.
- [x] **DEC-020:** CPython 3.14 runtime line; compatibility-test and lock exact FastAPI/Pydantic/Starlette/ASGI dependency set at scaffolding.
- [x] **DEC-050:** uv for Python with a committed lockfile, subject to the mandatory Windows/Linux/Python 3.14/backend compatibility spike.
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

- [x] **DEC-059:** Flutter 3.47 stable line with its bundled Dart SDK; exact stable patch pinned after the plugin/Gradle/device spike.
- [x] **DEC-059:** Android 11/API 30 minimum, API 36 compile/target baseline, and physical low-resource/current OEM device matrix.
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
- [x] **DEC-041:** Stage A via Firebase App Distribution with separate staging identity/key; Stage B via Play internal/closed testing, AAB, Play App Signing, and protected CI upload key.

## F. React Admin Console Decisions

- [x] **DEC-043:** React 19.3 and TypeScript 7.0 stable lines, exact compatible patches and matching React types locked at scaffolding with strict TypeScript and controlled upgrades.
- [x] **DEC-044:** Vite static SPA build with official React plugin, separate type-check/test gates, public-only validated client configuration, and no initial service worker or React Compiler.
- [x] **DEC-045:** React Router Data Mode with browser-history URLs, nested lazy routes/errors, runtime-validated URL state, limited session/prefetch loaders, and TanStack Query-owned server mutations.
- [x] **DEC-042:** Feature-first Admin SPA with TanStack Query for in-memory server state, safe URL list state, local UI state, limited Context, and no optimistic consequential mutations.
- [x] **DEC-046:** React Hook Form with Zod/resolver integration, backend-authoritative validation, accessible RFC 9457 errors, safe money/value handling, and explicit consequential-action confirmation.
- [x] **DEC-047:** Material UI Core with a restrained centralized Kirim theme, semantic HTML where simpler, explicit WCAG testing, core semantic tables, and no initial MUI X/second styling system.
- [x] **DEC-014 / DEC-048:** Managed password + mandatory TOTP, Kirim opaque server session, preferred same-origin host-only Secure HttpOnly cookie, synchronizer CSRF header, strict origin validation, bounded expiry, rotation, and revocation.
- [x] **DEC-049:** Vitest, Testing Library/user-event, MSW, axe-core, and Playwright layered Admin testing.
- [x] **DEC-052 / DEC-053:** GitHub Actions immutable builds/promotion and Firebase Hosting same-origin Admin deployment to Cloud Run API.

## G. External Providers

- [ ] **DEC-057 gated:** Stripe Connect Malaysia is the leading candidate; production approval awaits exact funds-flow, liability, legal/tax/accounting, sandbox, and 24-hour settlement proof.
- [ ] **DEC-057 gated:** Signed webhook inbox, idempotent commands, immutable ledger, reconciliation, refund/reversal/payout exceptions, fake provider, and sandbox proof must pass before implementation/Stage B.
- [ ] **DEC-034 / DEC-056 / DEC-061 gated:** Split-capable maps architecture and minimized route-retention policy accepted; final vendor must permit server matching/corridor derivation and the approved temporary retention, with KL quality, privacy, SDK, cost, and contract proof.
- [x] **DEC-037 / DEC-056:** Direct Firebase Cloud Messaging with minimal hints and server catch-up.
- [ ] **DEC-055 gated:** Twilio Verify is the leading Malaysian phone-verification candidate pending compliance, deliverability, fraud/rate, DPA, and cost tests.
- [ ] **DEC-055 gated:** Amazon SES Singapore is the leading Kirim transactional-email candidate pending domain, sandbox-exit, deliverability, event, DPA, and cost tests; Auth0 credential email ownership remains part of the identity spike.
- [x] **DEC-053 / DEC-056:** Regional private Google Cloud Storage with separated pending/clean/original/derivative/quarantine access.
- [x] **DEC-056:** Magic/decode/size checks, maintained ClamAV isolation, controlled image derivatives, fail-closed quarantine, and manual review.
- [x] **DEC-053:** Google Cloud Run/Firebase Hosting managed application platform.
- [x] **DEC-053:** Singapore `asia-southeast1` core-data region, with explicit third-party/global-service transfer review rather than a universal residency claim.
- [x] **DEC-053:** Cloud SQL for PostgreSQL/PostGIS with backups/PITR and Stage B HA default.
- [x] **DEC-053:** Google Secret Manager with least-privilege service identities and versioned rotation.
- [x] **DEC-054:** Cloud Logging/Monitoring/Error Reporting plus OpenTelemetry; privacy-minimized Crashlytics and scrubbed Admin browser errors.

## H. Testing and Engineering Standards

- [x] **DEC-051:** pytest unit/integration/contract/E2E layers plus DEC-049/DEC-040 client layers.
- [x] **DEC-051:** Disposable real PostgreSQL/PostGIS through Docker/CI services or Testcontainers; no SQLite substitute for database behavior.
- [x] **DEC-057:** Deterministic fake provider plus real sandbox/contract/reconciliation tests before Stage B.
- [x] **DEC-051:** Ruff; Prettier/ESLint/typescript-eslint; `dart format`/`flutter analyze`.
- [x] **DEC-043 / DEC-051:** Pyright strict and TypeScript strict `tsc --noEmit`, with narrow documented generated-code exceptions.
- [x] **DEC-040 / DEC-049 / DEC-051:** Risk-based direct critical-path coverage; percentages diagnose gaps but do not replace behavior evidence.
- [x] **DEC-051:** Dependency review/alerts, CodeQL, secret scanning/Gitleaks, OSV audit, and Trivy release-image scans.
- [x] **DEC-031 / DEC-051:** OpenAPI validation, generated-client drift/consumer compilation, and a breaking-change checker with oasdiff leading.
- [x] **DEC-051:** Protected `main`, short-lived branches, PR checks/review, squash merges, and Conventional Commit-style messages.
- [x] **DEC-043 / DEC-050 / DEC-051:** Exact pins/lockfiles, isolated automated patch updates, reviewed minors, explicitly approved majors, Renovate leading.

## I. Repository and Delivery

- [x] **DEC-050:** One Git monorepo with independently deployable mobile, Admin, backend, and infrastructure areas.
- [x] **DEC-050:** `apps/mobile`, `apps/admin`, `services/backend`, `infra`, `docs`, and scripts; generated clients remain consumer-owned/generated.
- [x] **DEC-050 / DEC-053:** Windows-friendly pinned ecosystem tools plus containerized real dependencies and GCP/Firebase emulation/sandboxes where appropriate.
- [x] **DEC-041 / DEC-044 / DEC-048 / DEC-053:** Typed fail-fast per-process/flavor configuration, public client config, Secret Manager, and strict environment isolation.
- [x] **DEC-052:** GitHub Actions with required quality/security gates and OIDC/Workload Identity Federation.
- [x] **DEC-052 / DEC-053:** Immutable Artifact Registry/Firebase artifacts promoted through staging and approved production environments.
- [x] **DEC-023 / DEC-052 / DEC-058:** Explicit Cloud Run migration job with expand/migrate/contract and resumable backfills.
- [x] **DEC-052 / DEC-058:** Compatible immutable artifact rollback where possible; forward recovery for schema/data; no automatic migration downgrade.
- [x] **DEC-058:** Synthetic-only local/CI/staging fixtures and isolated provider sandboxes; no production copies.
- [x] **DEC-053 / DEC-058:** Cloud SQL backups/PITR, GCS recovery/retention controls, scheduled isolated restore/reconciliation drills.
- [x] **DEC-058:** Audited granular pilot pause controls plus incident, provider, payment, reconciliation, communication, and post-incident runbooks.

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

- Major accepted decision records: **61**.
- `docs/TECH_STACK.md` is complete as a Product Owner review draft.
- Remaining gates are evidence-driven rather than preference decisions: final maps vendor; production payment approval; production identity/SMS/email approval; PostgreSQL job library compatibility; exact locked versions and measured Stage B operating parameters. Stage A/Stage B budget targets are approved under DEC-060.
- Current next step: **Product Owner review of the completed technical-stack draft and gated selections**.
