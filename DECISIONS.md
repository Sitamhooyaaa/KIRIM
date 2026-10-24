# Project Decisions

This file is Kirim's durable record of consequential product, architecture, workflow, dependency, and operational decisions. Chat history is not the source of truth.

## Decision Record Template

```markdown
## DEC-XXX — YYYY-MM-DD — Decision title

- **Status:** Proposed / Accepted / Superseded / Deprecated
- **Decision owner:**

### Context

[Why a decision is required and which constraints apply.]

### Decision

[The selected option and its boundaries.]

### Rationale

[Why this option was selected.]

### Alternatives Considered

- **Option:** Why it was not selected.

### Consequences

**Benefits**

- ...

**Costs and constraints**

- ...

### Revisit Triggers

- ...

### Affected Areas

- ...
```

---

## DEC-001 — 2026-10-05 — Use a Modular Monolith for the MVP Backend

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim requires a trusted backend for route-based matching, bounded negotiation, payment-state reconciliation, shopping evidence, delivery confirmation, disputes, and audited administrative actions. These workflows require strong consistency and clear domain boundaries, but the MVP will be built and operated by a small team before demand or scaling characteristics are proven.

The architecture must therefore balance MVP delivery speed, maintainability, financial correctness, operational simplicity, and the ability to evolve.

### Decision

Use a **modular monolith** as the backend architecture style for Stage A and the initial Stage B pilot.

The backend will be one primary application/deployment boundary with explicitly separated business modules. Modules must communicate through controlled interfaces and must not bypass ownership boundaries by directly depending on another module's internal implementation.

This decision selects the architecture style only. It does not yet select:

- Programming language or framework.
- API protocol or contract format.
- Database technology.
- Background-job or messaging technology.
- Cloud or hosting provider.
- Android implementation framework.

Managed external services may still be used. They do not replace the backend's ownership of authorization, transaction state, payment decisions, and business invariants.

### Rationale

- A single deployment is practical for a small MVP team.
- One transactional boundary simplifies financial and workflow consistency.
- Explicit modules reduce the coupling and maintainability risks of an unstructured monolith.
- Local development, integration testing, deployment, and observability remain simpler than with distributed services.
- Modules can be extracted later if measured scale or team structure creates a justified need.
- The approach avoids making provider lock-in the foundation of the domain model.

### Alternatives Considered

- **Traditional monolith:** Fastest initially, but not selected because weak internal boundaries increase the risk of tightly coupled payment, matching, delivery, and dispute logic becoming difficult to test and change safely.
- **Microservices:** Not selected for the MVP because independent services would introduce distributed transactions, network failure modes, service discovery, cross-service observability, more deployments, and greater local-development complexity before scale justifies them.
- **Serverless/event-driven architecture:** Not selected as the primary architecture because Kirim has long-running stateful workflows, strict transition rules, deadlines, payment reconciliation, and dispute handling. These can be implemented serverlessly, but would require greater event, idempotency, debugging, and provider-specific complexity. Serverless components may still be considered for isolated supporting tasks.
- **Backend-as-a-Service-centric architecture:** Not selected as the primary domain architecture because direct client/data patterns and provider-specific security rules are a poor ownership boundary for consequential payment and transaction logic. Managed authentication, storage, or messaging may still be evaluated as supporting services behind trusted server-side controls.

### Consequences

**Benefits**

- Simpler deployment and operations than distributed services.
- Strong support for atomic transaction-state changes.
- Clearer testing and traceability across the complete user journey.
- Lower infrastructure burden during product validation.
- A controlled path to future service extraction.

**Costs and constraints**

- Module boundaries require active enforcement in code, reviews, and tests.
- A faulty deployment can affect the entire backend.
- Modules initially scale and release together unless explicitly separated later.
- Shared-database access can create hidden coupling if table/data ownership is not defined.
- The team must resist turning the design into an unstructured monolith.

### Revisit Triggers

Reconsider this decision only when evidence shows one or more of the following:

- A module requires independently measured scaling characteristics that materially affect cost or reliability.
- Multiple teams require independent deployment and ownership boundaries.
- Deployment frequency or failure isolation cannot be managed safely in one application.
- Regulatory or security isolation requires a separate service boundary.
- A module has a stable contract and the operational benefit of extraction exceeds the distributed-system cost.

A future extraction requires a new decision record; it must not occur as an unplanned refactor.

### Affected Areas

- `docs/TECH_STACK.md`
- Future backend repository structure
- Module ownership and dependency rules
- Database ownership conventions
- Testing and deployment strategy
- Observability and failure handling

---

## DEC-002 — 2026-10-05 — Use One Android User App and a Separate Minimal Admin Web Console

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim allows one account to act as a Customer or Shopper at different times. It also requires privileged support workflows for disputes, refunds, reimbursements, payouts, evidence review, prohibited requests, and account suspension. The public user experience and privileged administrative experience therefore need clear boundaries without expanding the MVP into multiple public clients.

### Decision

Use the following MVP system context:

- Customers and Shoppers share one Android application and switch role mode within the same account.
- Authorized Administrator/Support users use a separate private web application with a simple, minimal interface focused on operational tasks rather than public-facing design.
- Both clients communicate with the Kirim backend; neither client accesses the database or external payment controls directly.
- The backend integrates with external payment, maps/routing, push-notification, and email/SMS verification providers.
- Physical merchants have no direct Kirim system integration during the MVP. The Shopper interacts with the merchant and submits evidence to Kirim.
- The Kirim backend is authoritative for business workflow and authorization. The payment provider is authoritative for provider-side financial events, which Kirim reconciles into its own records.
- No public Customer/Shopper web application is included in the MVP.

This decision defines system boundaries and client roles only. It does not select the Android framework, web framework, API protocol, external providers, or hosting platform.

### Rationale

- A shared Android app matches the approved requirement that one account can switch between Customer and Shopper modes.
- One public mobile client minimizes duplicated authentication, profile, messaging, transaction, testing, and release work.
- A separate private console keeps privileged operations out of the public application and supports stronger administrative controls.
- A web console is more practical for reviewing receipts, conversations, location evidence, disputes, and financial adjustments.
- Minimal Admin Console UI keeps the MVP focused while still providing safe alternatives to direct database manipulation.
- Excluding a public web client and merchant integration avoids unsupported MVP scope.

### Alternatives Considered

- **One Android app for Customers, Shoppers, and Administrators:** Not selected because privileged administrative workflows and sensitive evidence should not be embedded in the public mobile client, and complex support review is poorly suited to a phone interface.
- **Separate Customer and Shopper Android apps plus an Admin Console:** Not selected because it conflicts with easy role switching, requires users who perform both roles to manage two apps, and duplicates substantial development and testing.
- **Android app plus public Customer/Shopper web app and private Admin Console:** Not selected because a second public client expands compatibility, security, location, camera, notification, and accessibility scope without an approved MVP need.
- **No Admin Console; operate through the database/provider dashboards:** Rejected because direct operational access weakens authorization, validation, auditability, and transaction invariants and creates unacceptable financial/privacy risk.

### Consequences

**Benefits**

- One coherent public user application.
- Clear public-versus-privileged trust boundary.
- Lower MVP client-development and testing scope than separate role apps.
- Safer, auditable support operations through a purpose-built interface.
- Future public web or role-specific clients can use the same backend contracts if later approved.

**Costs and constraints**

- Two user interfaces still require implementation and testing.
- The Admin Console requires strong authentication, role-based access, case-scoped authorization, session security, and audit logging despite its minimal visual design.
- Shared Customer/Shopper navigation must remain understandable when users switch modes.
- The backend API must support both mobile and administrative use cases without exposing privileged operations to ordinary users.

### Revisit Triggers

- User research demonstrates a material need for iOS or public web access.
- Customer and Shopper workflows diverge enough that separate applications produce measurable benefit.
- Administrative volume requires a more advanced case-management interface.
- Approved merchant integrations introduce a new external actor or portal.

Any expansion requires a new or superseding decision record.

### Affected Areas

- `docs/TECH_STACK.md`
- Android application information architecture
- Admin/Support authorization and web interface
- API contracts and trust boundaries
- Authentication and session policies
- Deployment topology
- Security and audit testing

---

## DEC-003 — 2026-10-05 — Use an API Process, Durable Worker, and Domain-Oriented Modules

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim's MVP includes interactive API requests and time-dependent or retryable work such as offer expiry, substitution-response expiry, balance-payment expiry, payment-event reconciliation, notification retries, the 24-hour settlement rule, and retention tasks. Running all of this work only inside the interactive API process would make restarts, latency, retries, and operational failures harder to control.

The selected modular-monolith architecture also needs module boundaries that are meaningful enough to protect payment and workflow correctness without creating microservice-like overhead inside one application.

### Decision

Use the following primary component model for Stage A and the initial Stage B pilot:

**Clients**

- Kirim Android application.
- Private minimal Admin/Support web console.

**Application runtime**

- Backend API process for interactive client/provider requests.
- Durable background worker/scheduler for delayed, retryable, reconciliation, notification, settlement, and retention work.
- API and worker use the same modular-monolith codebase, domain rules, and primary relational database. Separate runtime processes do not make them microservices.

**Data components**

- Relational database as the authoritative structured-data store.
- Private evidence/file storage for receipts and other approved files, with ownership, integrity, and retention metadata controlled by the backend.
- Durable job state. Its exact implementation is deferred until queue/background-job technology is selected.

Organize the backend into these initial domain-oriented modules:

1. **Identity and Accounts** — registration, verification, sessions, recovery, role mode, account status, and authorization foundations.
2. **Journeys and Matching** — journey publication, pilot geofence, route corridor, matching, and Shopper capacity.
3. **Requests and Negotiation** — one-shop requests, product policy, fee suggestions, offers, counteroffers, expiry, and final confirmation.
4. **Fulfilment Workflow** — authoritative transaction state machine, item pricing, substitutions, purchase reconciliation, delivery, failed delivery, and cancellation transitions.
5. **Payments and Ledger** — Kirim-side immutable financial events, provider reconciliation, invoices, refunds, cancellation compensation, reimbursement, earnings, dispute blocks, and settlement eligibility. This ledger does not replace the payment provider's records.
6. **Communication and Notifications** — in-app messages, push notifications, verification/transaction reminders, retries, and delivery status.
7. **Trust and Safety** — ratings, reports, prohibited-product enforcement, and bounded account restrictions.
8. **Support and Audit** — support cases, dispute outcomes, consequential administrative actions, sensitive evidence-access records, and audit history.
9. **Evidence and Files** — receipt/delivery-evidence metadata, authorization, integrity, retention, and deletion.

Modules must expose controlled interfaces, own their invariants and data access, avoid circular dependencies, and not modify another module's data directly merely because a database is shared.

This decision does not select the framework, database engine, object-storage provider, queue/job implementation, API protocol, or hosting provider.

### Rationale

- A dedicated worker prevents slow and retryable work from blocking interactive API requests.
- Durable jobs survive API restarts and support explicit retry, failure, and reconciliation behavior.
- One codebase and shared domain model preserve modular-monolith simplicity.
- Nine domain modules align with approved requirements without creating a module for every small feature.
- Explicit Payments/Ledger and Support/Audit boundaries give higher-risk behavior clear ownership.
- Components can be separated later only when measured scale or reliability evidence justifies the added operational cost.

### Alternatives Considered

- **Minimal components with all jobs inside the API process:** Not selected because API restarts or load could disrupt deadlines, reconciliation, notifications, and settlement work, and slow tasks could degrade interactive latency.
- **Multiple specialized worker deployments from the start:** Deferred because separate payment, notification, matching, settlement, and retention workers would increase deployment, routing, monitoring, and failure-handling complexity before workload characteristics are known.
- **Five broad modules (Accounts, Marketplace, Transactions, Payments, Operations):** Not selected because Marketplace, Transactions, and Operations would own too many unrelated invariants and could become poorly bounded internal monoliths.
- **Highly granular modules for each feature:** Not selected because ordinary MVP workflows would cross too many interfaces and create excessive ceremony and dependency management.

### Consequences

**Benefits**

- Better isolation of interactive and background workloads.
- Durable handling of deadlines and provider reconciliation.
- Clear business-rule and data ownership.
- Focused test boundaries for high-risk payment and administrative operations.
- A practical path to future scaling without premature distributed services.

**Costs and constraints**

- At least two application processes must be developed, deployed, monitored, and shut down safely.
- Job claiming, idempotency, retry, timeout, dead-letter/failure visibility, and recovery semantics must be designed and tested.
- Cross-module workflows require explicit orchestration and dependency-direction rules.
- Shared database access must not be used to bypass module ownership.
- Evidence storage requires independent access, integrity, malware/content-safety, retention, and deletion controls.

### Revisit Triggers

Review this decision after the MVP pilot and earlier only if evidence shows:

- A worker workload requires materially different independent scaling or failure isolation.
- API and worker releases cannot remain safely coordinated.
- Module dependency analysis reveals persistent ownership conflicts or cycles.
- One module requires a regulatory or security isolation boundary.
- Job volume, latency, or reliability exceeds the selected durable-job implementation.
- Operational evidence justifies specialized workers or extraction into a service.

Any split, merge, or extraction of primary modules requires an updated or superseding decision record.

### Affected Areas

- `docs/TECH_STACK.md`
- Backend package/repository structure
- Database table and migration ownership
- Background-job and queue selection
- API and worker deployment topology
- Module contracts and dependency tests
- Observability, retry, and incident procedures

---

## DEC-004 — 2026-10-05 — Use Managed Commodity Infrastructure While Retaining Domain Ownership

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim is being built by a small MVP team but requires reliable database operation, backups, private file storage, durable jobs, secrets, monitoring, payment integration, maps, verification delivery, and push notifications. Self-operating every infrastructure capability would consume substantial time and introduce security and reliability risk. Conversely, allowing a Backend-as-a-Service or collection of provider workflows to own Kirim's transaction, authorization, payment, and audit behavior would weaken domain boundaries and create significant lock-in.

### Decision

Use a **bounded managed-services strategy**:

- Prefer managed services for commodity infrastructure when they satisfy security, privacy, reliability, recovery, cost, and portability requirements.
- Keep all consequential Kirim domain logic inside the trusted modular-monolith backend.
- Public clients must not directly mutate authoritative domain or financial data through provider databases or provider-specific business rules.
- External/provider events are inputs that Kirim verifies, records, and reconciles; they do not bypass Kirim authorization or state-transition rules.

Managed commodity capabilities may include:

- Application/runtime hosting.
- Relational database infrastructure and backups.
- Private object/file storage.
- Durable queue/job infrastructure.
- Secret and configuration storage.
- Logs, metrics, traces, error reporting, and alerting.

Required external functional integrations are:

- A payment provider for the approved Stage B money flow.
- Maps/geocoding/routing capability.
- Android push-notification delivery.
- Email and SMS delivery for verification, recovery, and security communication.
- A controlled Android distribution mechanism.

Kirim retains ownership of:

- Authentication policy and server-side authorization decisions.
- Account and role state.
- Pilot-geofence and route-matching rules.
- Request, negotiation, fulfilment, cancellation, and dispute state machines.
- Kirim-side financial ledger, provider reconciliation, and settlement eligibility.
- Evidence ownership/access rules and audit semantics.
- Administrative authority and consequential decision records.

Exact vendors and whether each managed capability is adopted remain separate technology decisions. No provider capability is assumed until documentation, sandbox behavior, contractual terms, and applicable legal/security requirements are verified.

### Rationale

- Managed commodity infrastructure reduces operational and patching burden for a small team.
- Provider-managed backups, storage durability, and monitoring can improve MVP reliability when properly configured and tested.
- Keeping domain behavior in Kirim code preserves one reviewable source of business truth.
- Backend-only authoritative mutations prevent mobile clients from bypassing workflow and authorization controls.
- Provider adapters and explicit reconciliation reduce the impact of vendor-specific APIs.
- The approach balances delivery speed with financial correctness and future portability.

### Alternatives Considered

- **Minimal external dependencies with mostly self-operated infrastructure:** Not selected because operating databases, backups, storage, queues, secrets, monitoring, and delivery infrastructure would add substantial security and operational work without differentiating Kirim.
- **Managed-service-heavy/BaaS-centric domain architecture:** Not selected because direct client-to-database access and provider-owned workflows could distribute consequential business rules across security rules, triggers, functions, and dashboards, making payment correctness, testing, auditing, and migration harder.
- **Self-host as much as possible:** Not selected for the MVP because map/routing infrastructure, telecommunications delivery, monitoring, and reliable data services would impose disproportionate operational burden. Regulated payment capability would remain external regardless.

### Consequences

**Benefits**

- Faster MVP delivery with less infrastructure administration.
- Managed operational capabilities for backups, durability, secrets, and monitoring.
- Kirim retains control of domain logic and trust boundaries.
- Individual providers can be wrapped and evaluated independently.
- Lower risk of accidental direct-client access to consequential records.

**Costs and constraints**

- Kirim depends on provider availability, contracts, pricing, quotas, and platform behavior.
- Each provider requires due diligence, failure handling, monitoring, and an exit/recovery plan.
- Managed does not mean automatically secure; configuration, least privilege, encryption, backup restoration, and incident response remain Kirim responsibilities.
- Provider adapters and reconciliation add implementation work.
- Data location, retention, export, deletion, and subprocessors must be reviewed before Stage B.

### Revisit Triggers

Review the strategy or an individual provider when:

- Cost, quotas, latency, outages, or support no longer satisfy approved targets.
- A provider prevents required data export, deletion, recovery, or security controls.
- Legal, privacy, or data-residency obligations require a different boundary.
- Provider lock-in materially blocks product changes or migration.
- Operating a capability internally becomes demonstrably safer or more economical at measured scale.
- A managed service begins to own domain behavior that belongs in Kirim.

Changing an individual vendor does not necessarily supersede this strategy, but transferring domain ownership to a provider requires a new decision record.

### Affected Areas

- `docs/TECH_STACK.md`
- Provider evaluation and contract criteria
- Backend adapters and reconciliation
- Infrastructure and deployment design
- Data classification and retention
- Secrets and access control
- Backup, recovery, observability, and incident response

---

## DEC-005 — 2026-10-05 — Deploy the MVP on a Managed Application Platform

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim's modular monolith runs an interactive API and a durable background worker, plus a private Admin/Support web application. Stage B will be a real-money, real-user pilot, so the deployment must support process supervision, environment isolation, secrets, controlled migrations, health checks, monitoring, managed data services, and recovery without requiring the small MVP team to operate a full server or container-orchestration platform.

### Decision

Use a **managed application platform** as the deployment model for Stage A shared environments and the initial Stage B production pilot.

The target topology is:

- Separately configured API and worker services built from the same modular-monolith codebase/artifact.
- A separately deployed private Admin/Support web application.
- Managed relational database, private evidence/file storage, secrets, and durable job infrastructure selected through later technology decisions.
- Isolated local, CI/test, staging, and production configurations and data stores.
- Explicit one-off database migration/release tasks rather than migrations run concurrently by every API instance.
- Production database and object storage inaccessible directly from public clients.
- Separate sandbox/test and production external-provider credentials.

The platform must be verified to support long-running workers, safe restarts, health checks, private service connectivity, one-off release commands, secret management, logs/monitoring integration, backup/recovery requirements, and acceptable region/data-handling terms.

This deployment model is valid for a real production service; it is not merely a disposable prototype choice. Kirim will not plan a migration solely because the MVP ends. A change requires evidence that the current model no longer satisfies approved cost, reliability, scale, security, compliance, or operational needs.

No hosting vendor, region, container strategy, CI/CD product, or release strategy is selected by this decision.

### Rationale

- Reduces operating-system patching, TLS, process supervision, and basic deployment burden.
- Supports separate API and worker processes without Kubernetes complexity.
- Makes staging/production isolation and repeatable deployment more practical for a small team.
- Matches the bounded managed-services strategy in DEC-004.
- Can support the invite-only Stage B pilot and may remain appropriate well beyond it.
- Preserves the option to move to managed containers or another model if measured requirements later justify migration.

### Alternatives Considered

- **Single VPS/virtual machine:** Not selected because the team would own operating-system hardening, patching, process supervision, TLS, monitoring, capacity, and more recovery work, with a larger single-host failure risk.
- **Managed container platform:** Deferred because it adds image, networking, identity, scaling, and worker-lifecycle configuration that may not provide MVP value. It remains a likely future option if greater portability or runtime control becomes necessary.
- **Kubernetes:** Rejected for the MVP because cluster operations, networking, upgrades, security, observability, and resource management are disproportionate to an initial 50-account pilot.
- **Function-based serverless deployment:** Not selected as the primary model because it conflicts with the accepted API-plus-durable-worker topology and would distribute long-running workflow behavior across provider-specific functions/events.

### Consequences

**Benefits**

- Lower infrastructure-administration burden.
- Managed process restart, health, TLS, and deployment capabilities where supported.
- Straightforward API/worker separation from one codebase.
- Easier production-like staging and isolated production configuration.
- A deployment model that can continue into real production if it remains suitable.

**Costs and constraints**

- Platform pricing, quotas, supported runtimes, worker behavior, and networking constrain later technology choices.
- Platform outages and vendor changes become operational risks.
- Kirim still owns application security, least privilege, migration safety, monitoring rules, backup restoration tests, and incident response.
- Portability requires externalized configuration, reproducible builds, standard data export, and documented provider dependencies.

### Revisit Triggers

Review the deployment model after the MVP pilot and earlier only if evidence shows:

- Cost is materially disproportionate to actual workload.
- API or worker scaling/control requirements exceed platform capability.
- Reliability, recovery, region, privacy, or compliance requirements cannot be met.
- Platform limits prevent safe networking, migrations, observability, or incident response.
- Vendor lock-in blocks required changes or tested recovery.
- Team/service growth justifies managed containers, specialized workers, or orchestration.

Possible future outcomes include remaining on the platform, moving to a managed container runtime, or—only at justified scale—adopting more advanced orchestration. Migration is not automatic after MVP.

### Affected Areas

- `docs/TECH_STACK.md`
- Hosting-provider evaluation
- Environment and secret isolation
- API/worker process configuration
- Database migration and release procedure
- Networking and access control
- CI/CD, observability, backup, recovery, and incident response

---

## DEC-006 — 2026-10-05 — Use Flutter for the Mobile Client and Release Android First

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim's approved MVP is Android-first, while iOS is a possible later release. The mobile client requires maps, location permission and optional active-transaction location sharing, camera/file evidence, push notifications, secure local credential/token handling, accessibility, localization, and reliable transaction-state presentation. The Product Owner has prior Dart and React experience and prefers Flutter over learning a new native Android stack for the MVP.

### Decision

Use **Flutter with Dart** for the Kirim mobile client.

- Maintain one Flutter mobile codebase.
- Build, test, and release only the Android target for Stage A and the initial Stage B pilot.
- Defer iOS release, signing, device testing, and production support until separately approved.
- Share mobile UI, networking, state presentation, localization, and suitable client-side logic where practical.
- Use platform-specific Android implementations/configuration when maps, location, background behavior, notifications, secure storage, camera/files, accessibility, or lifecycle behavior requires it.
- Do not assume 100% Android/iOS code reuse.
- Keep consequential domain rules and authorization in the backend; Flutter is not authoritative for matching eligibility, payment state, cancellation outcomes, disputes, or settlement.

This decision applies only to the Customer/Shopper mobile client. The private Admin/Support web technology remains undecided.

Exact Flutter/Dart versions, state-management approach, navigation, networking, local persistence, maps/location packages, and testing libraries will be selected separately and pinned from verified official compatibility information before implementation.

### Rationale

- Existing Dart experience reduces initial learning and delivery risk.
- One mobile codebase can support Android now without requiring immediate iOS scope.
- A future iOS client can reuse appropriate code while retaining platform-specific adaptations.
- Flutter supports a coherent mobile experience without duplicating the complete Customer/Shopper workflow in Kotlin and Swift.
- The backend/API boundary keeps future client choices independent from server domain ownership.

### Alternatives Considered

- **Native Android with Kotlin and Jetpack Compose:** Strong fit for Android-only development and direct platform integration, but not selected because the Product Owner prefers Flutter and already has Dart experience. It would also require a separate Swift/iOS implementation if iOS is approved later.
- **React Native with TypeScript:** Viable given prior React experience and potential cross-platform reuse, but not selected because Flutter/Dart is preferred. React Native would still require platform-specific integration and dependency-management review.
- **Separate native Kotlin/Android and Swift/iOS clients:** Not selected for the MVP because duplicating the full mobile implementation, testing, and release process is disproportionate for a small team. It remains possible later if measured platform requirements justify separate clients.
- **Kotlin Multiplatform:** Deferred because shared-code and UI strategy would add build and architecture complexity without immediate iOS scope.
- **Progressive Web Application:** Rejected for the MVP because it conflicts with the approved Android-app context and would add a public web client while creating variable background, location, push, camera, and secure-platform behavior.

### Consequences

**Benefits**

- Faster initial work using an already familiar language/ecosystem.
- One mobile workflow implementation with a path to later iOS evaluation.
- Consistent shared UI and state presentation where appropriate.
- Ability to add native Android code for capabilities that require it.

**Costs and constraints**

- Flutter and package compatibility become mobile-client dependencies.
- Platform plugins must be evaluated for maintenance, security, licensing, Android-version support, and lifecycle/background behavior.
- Android-specific configuration and testing remain necessary.
- A future iOS release still requires macOS/Xcode, Apple signing, iOS devices, permission review, and platform-specific testing.
- Cross-platform abstractions must not hide platform safety or accessibility differences.

### Revisit Triggers

Review this decision if:

- Required Android capabilities cannot be implemented reliably or safely through maintained Flutter/native integrations.
- Performance, accessibility, background execution, or security testing reveals unresolved platform limitations.
- Plugin abandonment or supply-chain risk affects critical capabilities.
- iOS is approved and user research shows materially different platform requirements.
- Mobile team size and specialization make separate native clients more sustainable.

Changing the mobile framework or creating a separate native client requires a superseding decision record and migration plan.

### Affected Areas

- `docs/TECH_STACK.md`
- Mobile repository/package structure
- Android build, signing, permissions, and distribution
- Mobile API contracts and generated/manual client models
- Location, maps, notification, camera/file, and secure-storage integrations
- Mobile accessibility, localization, testing, and CI

---

## DEC-007 — 2026-10-05 — Use a Minimal React and TypeScript SPA for the Admin Console

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim requires a private Admin/Support interface for assigned-case review, receipt and delivery-evidence inspection, disputes, reports, authorized financial actions, account restrictions, and audit history. The console is operational rather than public-facing: it does not need SEO, public content rendering, or consumer visual complexity. It must remain separate from the Flutter Customer/Shopper application and must never bypass backend authorization or workflow rules. The Product Owner already has React experience.

### Decision

Build the private Admin/Support web client as a **minimal React single-page application using TypeScript**.

- Deploy it separately from the backend and Flutter mobile application.
- Communicate only with authenticated, authorized Kirim backend Admin API endpoints.
- Do not connect the browser directly to the database, private evidence storage, payment provider, or infrastructure-management APIs.
- Treat browser state and client-side route guards as user-interface behavior only; the backend reauthorizes and validates every sensitive read and action.
- Keep the initial interface focused on necessary tables, filters, case details, evidence review, action forms, and audit history.
- Apply web accessibility, secure session handling, content-security controls, safe output rendering, and protection against applicable browser attacks.

This decision does not select the React version, build tool, routing library, data-fetching library, component system, form library, test libraries, or authentication/session mechanism. Those must be selected and pinned separately from verified compatibility information.

### Rationale

- Existing React experience reduces implementation and learning cost.
- React is suitable for interactive tables, forms, filters, evidence views, and evolving case-management workflows.
- A small SPA preserves the accepted separate-client boundary without introducing another full-stack application server.
- TypeScript improves API-model and action-payload checking at development time.
- Normal web technology better fits a desktop-oriented operational interface than sharing Flutter mobile UI.

### Alternatives Considered

- **Server-rendered Admin pages from the backend:** Not selected because it would couple the interface to the undecided backend framework and change the separately deployed Admin Console topology. It remains viable only through a superseding decision if the console proves extremely small.
- **Full-stack React framework:** Not selected because server-side rendering, public SEO, and frontend-specific server logic are not required, while another server runtime would create additional deployment and trust-boundary complexity.
- **Flutter Web:** Not selected because the mobile Customer/Shopper UI and desktop administrative workflows have little useful UI overlap, and standard web technology is a better fit for dense forms, tables, navigation, and evidence review.
- **Low-code/third-party Admin platform:** Not selected because sensitive identity, payment, location, communication, and evidence data require precise case-scoped authorization, auditing, provider review, and no direct database access. The speed benefit does not currently outweigh those risks.

### Consequences

**Benefits**

- Familiar technology for the Product Owner.
- Small independently deployable static frontend where supported.
- Clear separation from mobile and backend code.
- Practical foundation for operational workflows that may grow after the pilot.

**Costs and constraints**

- A separate frontend dependency/build/test toolchain is required.
- Browser XSS, CSRF, session, caching, and sensitive-data exposure risks must be addressed.
- API contracts or generated/manual models must remain synchronized with the backend.
- Client-side checks never replace backend permissions and state validation.
- Minimal visual design does not permit minimal accessibility or security work.

### Revisit Triggers

Review this decision if:

- The Admin Console remains so small that a server-rendered alternative would measurably reduce total complexity.
- Operational workflows require capabilities poorly served by a SPA.
- Security review identifies an unresolved risk specific to the selected browser/session model.
- A larger support organization requires a dedicated case-management product after provider/privacy review.
- React ecosystem maintenance or dependency risk becomes disproportionate.

Changing the Admin web approach requires a superseding decision and migration plan.

### Affected Areas

- `docs/TECH_STACK.md`
- Admin web repository/package structure
- Admin API contracts
- Administrator authentication, authorization, and session management
- Web security headers and browser threat model
- Admin accessibility, testing, deployment, and CI

---

## DEC-008 — 2026-10-05 — Use Python and FastAPI for the Backend

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim needs a backend for the Flutter mobile client, React Admin Console, payment-provider events, route/matching workflows, evidence metadata, strict transaction-state transitions, and a durable worker. The backend must fit the accepted modular-monolith architecture and be practical for a small MVP team. The Product Owner has prior FastAPI experience and is open to learning, but Kirim already introduces substantial new product, mobile, payment, geospatial, deployment, security, and operational work.

### Decision

Use **Python with FastAPI** for the Kirim backend API and shared backend application/domain code.

- API and worker processes import and execute the same application/domain services; background jobs must not duplicate business rules in a separate implementation.
- Organize code by the nine domain modules accepted in DEC-003, not as one routes file and one shared database file.
- Keep HTTP route handlers thin: parse authenticated requests, invoke application use cases, and map results/errors to contracts.
- Keep consequential business rules independent from FastAPI request objects, response objects, and transport concerns.
- Enforce module data ownership and dependency direction despite the shared relational database.
- Apply runtime validation at every external boundary and strict static type checking during development/CI.
- Use explicit transactions, idempotency, locking/concurrency controls, provider-event verification, and reconciliation for financial/state-changing operations.
- Generate and validate the API schema/contract in CI, while treating the approved API contract—not internal ORM/domain models—as the client boundary.

This decision does not yet select:

- Python or FastAPI versions.
- REST resource conventions or another API contract style.
- ASGI server/process configuration.
- ORM/database-access and migration libraries.
- Dependency-injection conventions.
- Durable worker/queue technology.
- Authentication/session libraries.
- Serialization, HTTP client, testing, linting, formatting, or type-checking tools.

Those choices will be evaluated separately and pinned using verified official compatibility information before implementation.

### Rationale

- Existing FastAPI experience reduces avoidable framework-learning risk.
- FastAPI provides practical runtime request/response validation and OpenAPI generation for Flutter and React clients.
- Python supports rapid MVP iteration and broad provider/geospatial/testing integrations.
- The initial 50-account pilot is dominated by workflow correctness rather than raw throughput.
- The project still provides substantial learning through modular architecture, transactions, idempotency, workers, payments, security, observability, and deployment.
- Explicit architectural rules can provide the structure that FastAPI itself does not impose.

### Alternatives Considered

- **TypeScript with NestJS:** Strong module, controller, service, guard, and dependency-injection conventions and useful alignment with the React Admin Console. Not selected because adopting a new backend framework adds simultaneous delivery risk without a proven Kirim requirement; TypeScript still requires runtime validation and does not guarantee correct modular or financial design.
- **Python with Django and Django REST Framework:** Mature ORM, migrations, authentication, and conventional administration. Not selected because Kirim has separately approved clients and benefits from FastAPI's explicit typed API models and existing team familiarity; Django's built-in admin would not satisfy Kirim's case-scoped authorization/audit needs without significant work.
- **Kotlin with Spring Boot or Ktor:** Strong typing and mature transaction/security ecosystem. Not selected because the JVM/backend learning and framework overhead are disproportionate for the small MVP team and do not provide a necessary product capability.
- **Go:** Strong deployment and concurrency characteristics. Not selected because Kirim's initial challenge is domain/workflow correctness, and Go would require more unfamiliar ecosystem and application-structure work without a measured performance need.

### Consequences

**Benefits**

- Faster delivery using existing experience.
- Clear request/response validation and API-schema generation.
- One Python application/domain implementation shared by API and worker.
- Broad ecosystem for database, testing, provider, and geospatial needs.

**Costs and constraints**

- FastAPI does not enforce modular boundaries; architecture tests and reviews must do so.
- Python type hints do not replace runtime validation or tests.
- Async/sync boundaries, database sessions, worker execution, and transaction lifetimes require explicit conventions.
- Performance and memory behavior must be measured under approved targets rather than assumed.
- Dependency and supply-chain risk must be controlled through pinning, review, scanning, and updates.

### Revisit Triggers

Review this decision only if evidence shows:

- Approved performance or concurrency targets cannot be met after profiling and reasonable optimization.
- Required provider/runtime behavior is unsupported or unsafe in the selected Python stack.
- Module enforcement or code quality cannot be maintained despite documented controls.
- Operational constraints make the runtime unsuitable for the selected platform.
- Team composition changes enough that migration benefits exceed its cost and risk.

Changing the backend language/framework requires a superseding decision, compatibility plan, data/API migration plan, and rollback strategy.

### Affected Areas

- `docs/TECH_STACK.md`
- Backend source/package structure
- API contract generation and client integration
- Worker and job execution
- Database access, transactions, and migrations
- Authentication and authorization
- Backend testing, typing, linting, packaging, and CI
- Deployment runtime and observability

---

## DEC-009 — 2026-10-05 — Use PostgreSQL with PostGIS as the Authoritative Database

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim requires strong relational transactions and constraints for accounts, workflow states, idempotency, financial events, evidence metadata, disputes, and audit history. It also requires geospatial representation and queries for the versioned pilot geofence, Shopper routes, shop/delivery points, 1 km route corridors, and matching. The initial Stage B pilot is limited to 50 accounts, but the database should remain viable through substantial growth without an unnecessary early migration.

### Decision

Use **PostgreSQL with PostGIS** as Kirim's authoritative relational database and geospatial query engine.

- Use PostgreSQL transactions, constraints, indexes, and concurrency controls to protect approved invariants.
- Use PostGIS geometry/geography capabilities for approved geofences, routes, points, corridors, and proximity/containment queries.
- Continue using an external maps/routing provider for address/place search and route generation; PostGIS stores and queries approved geometry but is not the route provider.
- Use managed PostgreSQL for Stage B, subject to verified PostGIS extension support, backups, restoration, security, region, export, monitoring, and version compatibility.
- Run database integration tests against PostgreSQL/PostGIS rather than treating SQLite behavior as equivalent.
- Keep authoritative financial, authorization, and workflow fields relational; generic JSON must not replace modeled constraints or searchable ownership/state fields.
- Define coordinate reference systems, units, boundary semantics, geometry validation, and geofence/route version links explicitly in `docs/DATABASE_SCHEMA.md`.

This decision does not select PostgreSQL/PostGIS versions, managed provider, Python driver, ORM/query layer, migration tool, connection pooler, backup product, or durable-job implementation. Those are separate decisions and must be compatibility-verified before implementation.

### Rationale

- PostgreSQL provides mature transactions, constraints, indexing, locking, and recovery capabilities for Kirim's consequential workflows.
- PostGIS directly supports the geospatial types and indexed queries required by the matching model.
- Keeping transactional and spatial data in one authoritative database simplifies consistency for the MVP.
- PostgreSQL/PostGIS is widely available in managed form and can scale substantially before distributed SQL is justified.
- It avoids implementing all geospatial filtering in Python or repeatedly outsourcing match eligibility to a maps provider.

### Alternatives Considered

- **PostgreSQL without PostGIS initially:** Not selected because route/geofence matching is core product behavior, and postponing spatial storage/indexing would likely create provider dependence and later migration/rework.
- **MySQL with spatial features:** Viable relationally, but not selected because Kirim has no MySQL-specific constraint and PostgreSQL/PostGIS provides a stronger fit for the approved geospatial workload.
- **SQLite:** Not selected for production or database-integration equivalence because its concurrency, locking, server operation, and geospatial behavior differ from PostgreSQL/PostGIS. It may be used only for isolated tests/tools that do not claim production-database compatibility.
- **Distributed SQL database:** Deferred because the MVP does not require multi-region writes or horizontal database distribution, while compatibility, transaction semantics, geospatial features, latency, cost, and operations would require additional proof.

### Consequences

**Benefits**

- One transactional source of truth for domain, financial, audit, and spatial records.
- Database-enforced integrity and concurrency controls.
- Indexed geofence and route-proximity queries.
- Strong managed-service and tooling ecosystem.
- Significant growth headroom without an automatic database migration.

**Costs and constraints**

- The team must learn PostGIS data types, indexes, coordinate systems, and query behavior.
- Spatial correctness and performance require representative tests and query-plan monitoring.
- Managed-service extension/version availability constrains provider selection.
- Schema migrations and large geometry/index changes require operational planning.
- PostgreSQL scaling, pooling, vacuum/maintenance, backups, and restore verification remain engineering responsibilities even when managed.

### Revisit Triggers

User acceptance or user count alone does not justify distributed SQL. First optimize and scale PostgreSQL through measured steps such as query/index tuning, connection pooling, larger instances, caching where correct, read replicas for suitable reads, partitioning/archival, and workload isolation.

Reconsider the database architecture only when measured evidence shows one or more of the following and reasonable PostgreSQL scaling no longer satisfies it:

- Sustained write throughput or data size exceeds an approved single-primary strategy.
- Required availability cannot be achieved with managed PostgreSQL high availability and recovery.
- A genuine multi-region write requirement is approved.
- Geographic latency targets cannot be met through the existing architecture.
- Operational cost or reliability is materially better under a validated alternative.
- Required PostGIS behavior and transaction semantics are proven compatible in the candidate system.

Any move to distributed SQL requires a separate decision record, compatibility/load tests, geospatial validation, data migration and reconciliation plan, staged cutover, and rollback strategy.

### Affected Areas

- `docs/TECH_STACK.md`
- `docs/DATABASE_SCHEMA.md`
- Geofence, journey, matching, and delivery design
- Financial, audit, idempotency, and transaction-state design
- Backend data-access and migration tooling
- Testing, hosting, backups, recovery, and observability

---

## DEC-010 — 2026-10-05 — Use PostgreSQL-Backed Durable Jobs and No Shared Cache for the MVP

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim requires delayed and retryable background work for offer/substitution/balance expiry, payment-event reconciliation, notifications, the 24-hour settlement rule, and data-retention tasks. These jobs must survive API/worker restarts and must remain consistent with authoritative workflow state. The initial Stage B pilot is limited to 50 accounts and does not have a measured high-throughput or shared-cache requirement.

### Decision

Use **PostgreSQL-backed durable jobs with a transactional outbox pattern** for Stage A and the initial Stage B pilot. Do not add a shared application cache initially.

- Create required outbox/job records atomically in the same PostgreSQL transaction as the related business-state change where applicable.
- Run jobs through the durable worker accepted in DEC-003.
- Assume at-least-once delivery/execution; never assume exactly-once processing.
- Make every consequential job idempotent and protect financial/state effects with database constraints, transactions, and provider reconciliation.
- Store identifiers and minimal routing metadata in jobs; keep sensitive/domain data in authoritative domain tables.
- Support scheduled time, attempts, backoff, lease/claim ownership, timeout/reclaim, completion, permanent failure/dead-letter visibility, and error diagnostics.
- Expose failed/stalled jobs through privacy-safe monitoring and authorized operational tooling.
- Query PostgreSQL/PostGIS directly for MVP reads and matching. A cache must not be authoritative for payment, authorization, transaction state, Shopper capacity, cancellation, dispute, or settlement decisions.

This decision does not select a Python job library, polling interval, database schema, exact retry policy, scheduler implementation, or hosting-provider queue. Those details require separate evaluation and representative concurrency/failure tests.

### Rationale

- PostgreSQL is already the authoritative database, avoiding another infrastructure service.
- Business-state changes and required jobs can commit atomically, avoiding a database/queue dual-write gap.
- Job records are inspectable, recoverable, and included in existing backup/recovery planning.
- Initial job volume does not justify Redis, a managed queue, or a dedicated broker.
- Avoiding shared cache reduces stale-data and invalidation risks during the correctness-focused pilot.
- The approach can be replaced or augmented later if measured load proves it insufficient.

### Alternatives Considered

- **Redis-backed queue and shared cache:** Deferred because it adds another secured/monitored service, creates a database-to-Redis dual-write problem without an outbox relay, and has no measured MVP throughput or caching requirement. Redis remains a future option for justified queue, rate-limit, or non-authoritative cache workloads.
- **Managed cloud queue/scheduler:** Deferred until a hosting provider is selected and its durability, delay, delivery, retry, dead-letter, payload, local-testing, portability, and cost characteristics are verified. An outbox bridge would still be required for atomic domain changes.
- **Dedicated message broker:** Not selected because its routing and distributed-consumer capabilities are disproportionate to one modular-monolith API and worker.
- **In-memory jobs/timers inside the API:** Rejected because work would be lost or duplicated across restarts/instances and could not reliably satisfy payment, expiry, and settlement requirements.
- **Shared cache from the start:** Not selected because PostgreSQL/PostGIS should satisfy pilot reads, and authoritative workflow data must not depend on cache freshness.

### Consequences

**Benefits**

- Fewer infrastructure dependencies.
- Atomic state/outbox creation.
- Durable, inspectable scheduling and retry state.
- One backup/recovery boundary for domain and pending-job records.
- Simpler correctness model for the MVP.

**Costs and constraints**

- Polling and job claiming add database load and require careful indexes/locking.
- The team owns lease, retry, timeout, backoff, failure visibility, and cleanup semantics unless a verified library supplies them.
- Long-running job work must not hold database locks unnecessarily.
- Job-table growth requires retention/archival and operational monitoring.
- Absence of cache means database query quality and indexes must meet performance targets.

### Revisit Triggers

Review the queue/cache strategy when evidence shows:

- Job polling or table contention materially harms API/database performance.
- Queue latency or throughput misses approved objectives after tuning.
- Independent queue scaling, routing, or consumer isolation is required.
- A selected managed queue offers validated reliability/operational benefit that exceeds migration and lock-in costs.
- Repeated read workloads fail approved latency targets after query/index tuning and safe database scaling.
- A specific non-authoritative cache use case has a measured hit-rate and invalidation strategy.

Moving jobs to Redis, a managed queue, or a broker requires an outbox migration/relay design, duplicate-delivery tests, draining/cutover procedure, and rollback plan. Adding caching requires explicit key ownership, TTL, invalidation, privacy, failure, and authority rules.

### Affected Areas

- `docs/TECH_STACK.md`
- `docs/DATABASE_SCHEMA.md`
- Worker/job schema and lifecycle
- Transactional outbox and event publication
- Idempotency and concurrency controls
- Database indexing, retention, and backup/recovery
- Monitoring, alerting, and Admin/Support operations

---

## DEC-011 — 2026-10-05 — Use Managed Authentication with Kirim-Owned Accounts and Authorization

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim requires verified phone and email, account recovery, mobile sessions, stronger Administrator authentication, suspension, role switching, case-scoped support access, and server-side transaction authorization. Implementing credential storage, recovery, MFA, abuse protection, and token issuance entirely within Kirim would add high-risk security work. Allowing an identity provider to own Kirim roles and business access would conflict with the accepted backend-authoritative domain boundary.

### Decision

Use a **managed identity provider for authentication proof**, while Kirim owns the internal account, profile, authorization, and business access model.

The managed provider may handle:

- Credential or passwordless authentication.
- Phone/email verification capabilities.
- Authentication-factor and MFA capabilities.
- Credential recovery.
- Signed identity/session tokens and provider-side revocation capabilities.
- Authentication abuse controls supported by the selected service.

Kirim owns and enforces:

- Stable internal Kirim user identifiers and mapping to immutable provider subject identifiers.
- Customer/Shopper mode, Administrator/Support assignments, and account status.
- Date of birth/age declaration and product eligibility.
- Server-side authorization for every domain action and sensitive read.
- Case-scoped support access, transaction ownership, suspension effects, and audit history.
- Product-data retention, deletion, correction, and legal-hold behavior.
- Final acceptance or rejection of every authenticated request.

A provider-authenticated token is identity evidence only. It does not grant a Kirim role, prove transaction ownership, bypass account suspension, or authorize any business action by itself.

Public clients must not use provider authentication to access authoritative Kirim database/storage records directly. The backend validates provider proof and applies Kirim policy.

The exact identity provider, login factors, password/passwordless method, mobile and browser session mechanisms, Administrator MFA policy, account-linking rules, verification delivery, and recovery flow remain separate decisions. Provider capability must be verified through official documentation, integration tests, security/privacy review, contractual terms, and failure/recovery analysis.

### Rationale

- Reduces Kirim's direct credential-storage, reset, MFA, and login-abuse implementation burden.
- Preserves Kirim ownership of consequential authorization and product account state.
- Matches the bounded managed-services strategy in DEC-004.
- Allows the Flutter app and React Admin Console to use an identity service while sharing one backend authorization model.
- Provides a replaceable boundary: provider subject proof maps to an internal Kirim account rather than becoming the domain identity itself.

### Alternatives Considered

- **Fully Kirim-owned authentication:** Not selected because securely operating credential hashing, recovery, factor enrollment, MFA, token issuance/rotation, abuse prevention, and account takeover response would add substantial high-risk MVP work. It remains possible only with a superseding security design and review.
- **Fully provider-controlled identity and authorization:** Rejected because provider roles/rules must not become authoritative for Customer/Shopper behavior, transaction access, support case scope, payment decisions, suspension effects, or audit semantics.
- **Direct client-to-provider database access:** Rejected because authentication success must not permit clients to bypass Kirim backend authorization, workflow transitions, validation, or audit controls.

### Consequences

**Benefits**

- Lower credential-security implementation burden.
- Provider-supported authentication, recovery, and MFA capabilities can be evaluated.
- Kirim retains one authoritative account and permission model.
- Backend policy remains consistent across Flutter, Admin web, API, and worker operations.

**Costs and constraints**

- Identity-provider availability, pricing, quotas, data handling, and lifecycle affect login.
- Provider-to-Kirim account linking, unlinking, deletion, suspension, and recovery require explicit consistency rules.
- Token validation, key rotation, issuer/audience checks, replay/session controls, and internal account checks remain Kirim responsibilities.
- Phone-plus-email verification on one identity and Administrator MFA must be proven rather than assumed.
- Migration requires preserving internal user IDs and safely relinking provider subjects.

### Revisit Triggers

Review this decision if:

- No provider can satisfy required phone/email linking, MFA, session, privacy, export, recovery, and Malaysia-delivery needs.
- Provider cost, outages, lock-in, or data-handling terms become unacceptable.
- Authentication risk or regulation requires an independently operated identity boundary.
- Provider limitations prevent safe account recovery, revocation, or incident response.
- A migration can demonstrably improve security and reliability enough to justify account relinking risk.

Changing provider does not necessarily supersede this ownership model. Moving credential authentication into Kirim or provider authorization into the domain requires a superseding decision and security-reviewed migration plan.

### Affected Areas

- `docs/TECH_STACK.md`
- `docs/API_CONTRACTS.md`
- Internal account/profile schema and provider-subject mapping
- Flutter and Admin web login/session flows
- Backend token validation and authorization
- Verification, recovery, MFA, suspension, and incident response
- Privacy, retention, deletion, audit, and provider due diligence

---

## DEC-012 — 2026-10-05 — Use Email and Password Login with Mandatory Email and Phone Verification for MVP Users

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Customer/Shopper accounts require a simple MVP login method plus verified email and phone contact channels. Social login is attractive for user convenience, but supporting it alongside another method creates account-linking, duplicate-account, recovery, and provider-policy scenarios. Phone-only OTP would add recurring SMS delivery/cost and SIM-swap/number-recycling dependence. The selected managed identity provider will handle credentials under DEC-011, while Kirim retains account activation and authorization.

### Decision

For Stage A and the initial Stage B Customer/Shopper experience:

- Use **email and password** as the single login method.
- Require one-time email verification and phone verification through an SMS one-time code before transactional access.
- Keep the managed identity provider responsible for password storage/hashing, credential authentication, and provider-supported password-reset mechanics.
- Use the provider's immutable subject identifier—not email or phone—as the external identity mapping key to Kirim's stable internal user ID.
- Treat email and phone as changeable verified contact attributes, not permanent domain identifiers.
- Block request creation, acceptance, confirmation, payment, purchase, delivery, rating, and dispute actions until both contact channels are verified and the Kirim account is active.
- Require recent reauthentication and verification of a replacement email/phone before changing sensitive contact details; notify existing verified channels where possible.
- Rate-limit and monitor registration, login, verification, reset, and contact-change attempts.
- Do not use knowledge-based security questions.
- Defer social login, phone-only login, email magic-link login, and passkeys.

Administrator/Support login, browser sessions, and MFA are separate decisions and may be stronger than Customer/Shopper authentication.

The exact identity provider, password policy, email verification mechanism, SMS/email provider, reset-token/session behavior, anti-automation controls, and recovery exception process remain undecided and must be verified before implementation.

### Rationale

- Provides one predictable MVP login and recovery path.
- Avoids recurring SMS dependency for ordinary login while still verifying the required phone channel.
- Avoids multi-method account linking and accidental duplicate identities during the pilot.
- Reduces reliance on a phone number that may be SIM-swapped or recycled.
- Delegates password credential handling to the managed identity provider.
- Keeps future social-login adoption possible after user research and safe linking design.

### Alternatives Considered

- **Social login:** Preferred by the Product Owner for convenience but deferred for MVP because it does not remove phone/email verification and introduces provider dependency, duplicate-account/linking, recovery, and current platform-policy research. It is the first additional login method to evaluate after MVP evidence.
- **Phone number with SMS OTP for every login:** Not selected because of recurring cost/deliverability and SIM-swap, number-recycling, and lost-number recovery risks.
- **Email magic link or email OTP:** Not selected because switching applications, deep-link handling, email delay/filtering, and mailbox compromise create different usability and security risks without a demonstrated pilot need.
- **Passkeys:** Deferred because cross-device/recovery behavior, provider support, Android-device coverage, and user understanding require research and broader testing.
- **Multiple simultaneous login methods:** Not selected because account linking, takeover recovery, duplicate identity prevention, UI, and test scope are disproportionate for the initial pilot.

### Consequences

**Benefits**

- One login path to implement, explain, test, and support.
- Lower SMS usage than phone-OTP login.
- Both approved contact channels are verified before transactions.
- Social login can be added later without making it the original domain identity.

**Costs and constraints**

- Users must create and remember a password.
- Credential stuffing, password reuse, phishing, and reset abuse remain risks that the provider and Kirim controls must address.
- Phone verification still depends on SMS delivery quality and cost.
- Provider account creation and Kirim internal-account creation require safe retry/reconciliation to avoid orphaned or duplicate mappings.
- Social-login convenience is deferred despite Product Owner preference.

### Revisit Triggers

Evaluate social login first after the MVP when:

- User research or login/drop-off metrics show material demand.
- A managed identity provider is selected and safe account linking/merge behavior is designed and tested.
- Duplicate-account, recovery, unlinking, and takeover scenarios have explicit rules.
- Current Android and future iOS platform policies are reviewed.

Also review the login method if password-reset abuse, credential attacks, user support burden, provider limitations, or passkey adoption materially changes the risk/benefit balance.

Adding or replacing a login method requires a decision record, account-linking model, migration/recovery plan, security review, and regression tests.

### Affected Areas

- `docs/TECH_STACK.md`
- Registration, verification, login, recovery, and contact-change flows
- Identity-provider evaluation
- Internal account/provider-subject mapping
- Flutter authentication UI and secure session storage
- Email/SMS provider selection and abuse controls
- Security monitoring, support procedures, and privacy notices

---

## DEC-013 — 2026-10-05 — Use Provider Access Tokens and Provider-Managed Refresh Sessions for Mobile

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

After Customer/Shopper authentication, the Flutter app needs a secure way to call the Kirim API without resending credentials. Kirim has accepted managed authentication while retaining internal authorization. The mechanism must fit a native public client, minimize Kirim token-issuer responsibilities, support revocation/rotation, and avoid treating client or identity claims as business authorization.

### Decision

Use a standards-based native application authorization flow with:

- OAuth 2.0 Authorization Code flow with PKCE through an external user-agent/system browser where supported by the selected provider.
- OpenID Connect for authentication identity information.
- A short-lived provider access token intended for the Kirim API as the API credential.
- Provider-managed refresh/session behavior with refresh-token rotation or another standards-compliant replay-detection mechanism for a public client.
- Vetted Flutter/native secure storage backed by Android platform security for persistent session material; keep access tokens in memory where practical.
- Backend validation of token issuer, audience, expiration, signature or provider-supported introspection, key rotation, provider subject, and all required protocol claims.
- A fresh Kirim internal account/status/authorization check for every protected action.

Do not use an OpenID Connect ID Token as the Kirim API access credential. Do not embed a client secret in the Flutter application. Do not store passwords, tokens, or authorization codes in ordinary preferences, logs, analytics, URLs, screenshots, crash reports, or notification payloads.

Logout/account-security handling must clear local session material, stop sensitive background/location behavior, request provider revocation/logout where supported, and preserve Kirim's ability to block a suspended account even before provider tokens expire.

Offline clients may display explicitly marked cached non-sensitive state but cannot treat a consequential action as authenticated or successful until the backend confirms it.

This decision is conditional on the selected identity provider proving the required native-app flow, Kirim API audience/access tokens, refresh replay protection, revocation, key rotation, Flutter/Android integration, and FastAPI validation. If the provider cannot do so, Kirim must revisit provider selection or evaluate a Kirim token exchange/opaque session through a new decision rather than misuse ID tokens.

Exact access-token lifetime, refresh/session lifetime, secure-storage package, redirect mechanism, scopes, claims, revocation/device UI, and provider are undecided.

### Rationale

- Follows established native-public-client guidance rather than inventing a Kirim authentication protocol.
- Keeps credential verification, token issuance, refresh, and replay controls with the managed identity provider.
- Avoids making Kirim a signing-key/token issuer for the MVP.
- Short-lived access credentials reduce exposure while refresh sessions preserve usability.
- Backend account and policy checks maintain Kirim authority regardless of token claims.

### Alternatives Considered

- **Exchange provider proof for Kirim-issued access/refresh tokens:** Deferred because Kirim would become responsible for signing keys, token rotation, refresh replay, revocation, and two session systems. Reconsider only if suitable provider API access tokens are unavailable.
- **Opaque Kirim server-side session token:** Deferred because it adds session creation, storage, lookup, expiry, rotation, and abuse controls despite the managed-authentication decision.
- **One long-lived bearer token:** Rejected because theft would create prolonged payment/location/account exposure and weak device-loss/revocation behavior.
- **ID Token sent as API authorization:** Rejected because an ID Token identifies authentication to its intended client; it is not a substitute for an API access token with the correct resource audience and validation rules.

### Consequences

**Benefits**

- Standards-based native authentication flow.
- Reduced Kirim token-issuer and refresh implementation.
- Short-lived API credentials and provider-supported replay detection.
- Explicit separation of authentication identity from Kirim authorization.

**Costs and constraints**

- Login/refresh availability depends on the provider.
- Native redirect, app-link, browser, storage, and lifecycle behavior require device testing.
- Token theft remains possible on compromised devices; secure storage lowers but does not eliminate risk.
- Kirim must handle provider signing-key rotation, validation failures, clock issues, outages, revocation limitations, and account-mapping consistency.
- A provider-specific SDK must not bypass protocol/security review.

### Revisit Triggers

Review this decision if:

- No viable provider issues access tokens suitable for a custom Kirim API.
- Provider refresh/revocation/session behavior fails security or recovery requirements.
- Mobile security testing finds unacceptable token exposure.
- Regulation or higher-risk product scope requires sender-constrained access tokens, stronger device binding, or step-up authentication.
- Multiple identity providers require a Kirim session normalization layer.

Changing the mobile session mechanism requires threat modeling, token/session migration, forced reauthentication planning, compatibility tests, and rollback procedures.

### References

- RFC 8252, *OAuth 2.0 for Native Apps*: https://www.rfc-editor.org/rfc/rfc8252.html
- RFC 9700, *Best Current Practice for OAuth 2.0 Security*: https://www.rfc-editor.org/rfc/rfc9700.html
- OpenID Connect Core 1.0: https://openid.net/specs/openid-connect-core-1_0.html

### Affected Areas

- `docs/TECH_STACK.md`
- Flutter login, redirect, secure-storage, refresh, and logout behavior
- FastAPI authentication middleware/dependencies and provider-key handling
- Internal account/provider-subject mapping
- Account suspension, recovery, device-loss, and incident response
- Mobile security testing and provider evaluation

---

## DEC-014 — 2026-10-05 — Use Server-Side Admin Sessions with Mandatory TOTP MFA

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Administrator/Support users can access sensitive identity, payment, location, communication, receipt, and delivery evidence and can record consequential dispute, refund, payout, and account-status actions. Their browser sessions therefore require stronger controls than ordinary mobile sessions. The Admin Console is a React SPA, while credential authentication remains managed under DEC-011.

### Decision

After managed-provider authentication, create a **Kirim server-side opaque Admin session** and authenticate Admin API requests through a secure browser cookie.

- Require managed-provider email/password authentication plus mandatory TOTP authenticator-app MFA for every Administrator/Support account.
- Store only a high-entropy opaque session identifier in a cookie configured as Secure and HttpOnly, with the exact SameSite/domain/path policy chosen from the final deployment topology.
- Store authoritative Admin session state server-side, including internal user ID, provider subject, MFA assurance/time where available, creation time, last activity, absolute expiry, revocation state/reason, and security metadata limited to what is necessary.
- Store session identifiers in a non-recoverable hashed/verifier form where the chosen implementation safely supports it.
- Use a 30-minute inactivity timeout and an 8-hour absolute maximum session for the MVP, with no permanent remember-me option.
- Rotate the session identifier after successful authentication and relevant privilege/session changes.
- Revoke sessions immediately after account suspension, Administrator-role removal, password reset, MFA reset, confirmed compromise, or an authorized security action.
- Require recent authentication/MFA confirmation for defined consequential actions when the existing assurance is too old; the exact freshness window and action list remain to be specified in authorization design.
- Protect cookie-authenticated state-changing requests against CSRF and enforce strict allowed origins, HTTPS, security headers, safe caching, and backend authorization.
- Never store Admin access/refresh/session secrets in browser local storage, session storage, URLs, logs, analytics, or ordinary application state exposed to scripts.
- Audit login, failed login, logout, session creation/rotation/revocation, MFA recovery/reset, sensitive evidence access, and consequential actions without recording secrets.
- Use provider-supported offline recovery codes only if their generation, one-time use, storage guidance, reset effects, and audit behavior pass security review.
- Do not use SMS or email as the normal Admin MFA factor or fallback.

The managed provider still owns password authentication and TOTP verification. Kirim owns the privileged browser session, role/status checks, case scope, action authorization, and revocation effects.

WebAuthn/passkeys or physical security keys are the preferred future phishing-resistant improvement, subject to provider capability, enrollment/recovery design, device support, and testing.

This decision does not select the identity provider, session library, cookie name/domain, CSRF implementation, TOTP enrollment/recovery details, recent-authentication window, concurrent-session limit, or WebAuthn technology.

### Rationale

- HttpOnly cookies reduce exposure of session credentials to ordinary browser JavaScript.
- Server-side state gives Kirim direct session expiry, listing, and revocation control for privileged users.
- Separate Admin sessions allow shorter lifetimes and stronger assurance than mobile sessions.
- TOTP is feasible for a small invite-only administrative team and is materially stronger than ordinary SMS/email fallback.
- The model preserves managed credential authentication while keeping Kirim administrative authority in the backend.

### Alternatives Considered

- **Provider access token kept only in SPA memory:** Viable but not selected because refresh/page-reload behavior and provider token lifetime/revocation give Kirim less direct control over privileged browser sessions.
- **Access or refresh tokens in browser local/session storage:** Rejected because script-accessible storage increases credential theft impact from XSS.
- **Dedicated Admin backend-for-frontend:** Deferred because it adds another server/deployment and duplicates concerns that the Kirim backend can handle for the minimal Admin Console.
- **SMS MFA:** Rejected as the normal Admin factor due to SIM-swap, number-recycling, delivery, and recurring-cost risks.
- **Email OTP MFA:** Rejected as the normal factor because compromise of the recovery/login mailbox could also defeat MFA independence.
- **WebAuthn/passkey/security key required from MVP launch:** Deferred despite stronger phishing resistance because provider capability, enrollment, backup/recovery, device coverage, and support procedures require additional validation.

### Consequences

**Benefits**

- Stronger privileged-session boundary than the ordinary mobile flow.
- Immediate Kirim-controlled revocation and bounded session lifetime.
- No script-readable long-lived Admin credential.
- MFA without recurring SMS cost.
- Auditable session and assurance lifecycle.

**Costs and constraints**

- Kirim must implement secure session generation, storage, lookup, rotation, expiry, cleanup, and revocation.
- Cookie deployment requires correct same-site/cross-origin design and CSRF protection.
- TOTP remains phishable and requires controlled enrollment, recovery, reset, and support procedures.
- Managed-provider assurance claims and reset/revocation behavior must be verified.
- Administrators need secure recovery-code handling and may require support after device loss.

### Revisit Triggers

Review this decision if:

- WebAuthn/passkey/security-key support becomes verified and operationally practical.
- TOTP phishing, recovery, or support incidents exceed accepted risk.
- Deployment topology prevents a secure/reliable cookie model.
- Administrative scale requires device/session management, conditional access, or enterprise identity federation.
- Regulation or security review requires phishing-resistant MFA.

Changing Admin session/MFA behavior requires threat modeling, forced-session invalidation and reenrollment planning, provider tests, support procedures, and rollback/recovery design.

### Affected Areas

- `docs/TECH_STACK.md`
- Admin login, MFA enrollment, recovery, and session UI
- FastAPI Admin session, cookie, CSRF, CORS/origin, and authorization middleware
- Identity-provider requirements
- Session database schema and cleanup jobs
- Admin auditing, security monitoring, and incident response
- Admin web deployment domains, HTTPS, and security headers

---

## DEC-015 — 2026-10-05 — Use Email Links for Verification and Password Recovery with Controlled Contact Changes

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim requires verified email and phone contacts, managed email/password authentication, password recovery, and safe contact changes. The Product Owner prefers familiar provider-managed email links and an email-only reset flow rather than verification codes or mandatory two-channel recovery. Contact changes should remain reasonably simple while reducing the risk that a stolen session silently replaces account recovery channels.

### Decision

For Customer/Shopper accounts, use provider-managed email links for both email verification and password recovery.

**Email verification**

- Send a single-use, short-lived verification link to the registered email.
- Mark email verified only after the provider/backend confirms successful completion of the intended verification action.
- Rate-limit requests/resends and prevent older or completed challenges from being reused where provider capability permits.
- Do not grant transactional access until email and phone verification plus Kirim account activation are complete.

**Password recovery**

- Accept a submitted email and return a generic response regardless of account existence.
- Send a provider-managed single-use, short-lived password-reset link to the verified email when an eligible account exists.
- After successful reset, revoke provider sessions where supported, invalidate Kirim-recognized pre-reset authentication where token claims/provider behavior allow, notify verified email and phone, and require login again.
- Do not require an SMS challenge before ordinary transactional access resumes.
- Do not use knowledge-based security questions.

**Phone-number change**

- Require recent password/provider reauthentication.
- Require confirmation through the current verified email.
- Require SMS verification of the new phone number.
- Apply the new number only after all required checks succeed.
- Notify the old phone where deliverable and the verified email.

**Email-address change**

- Require recent password/provider reauthentication.
- Require verification of the new email address through a provider-managed link.
- Apply the new email only after successful verification.
- Notify the old email and current verified phone.
- Revoke existing sessions and require login again.
- Do not require the old email to approve the change beyond the recent reauthentication requirement.

Use the provider's immutable subject and Kirim's internal user ID as identity keys; changing email or phone must not create a new account or rewrite historical ownership. Contact identifiers must remain unique according to the final normalization/linking rules.

Email is accepted as the primary password-recovery authority for the MVP. This intentionally accepts greater risk if the email account is compromised; notifications, rate limits, provider protections, session invalidation, and support suspension mitigate but do not eliminate that risk.

The exact link lifetimes, resend limits, recent-authentication window, provider-hosted confirmation pages, token-consumption behavior, address/phone normalization, active-transaction contact-change policy, and manual lost-channel recovery process remain separate details requiring provider and threat-model review.

Administrator password/MFA recovery is not governed by this decision and requires the stricter controls accepted in DEC-014 plus an operational recovery procedure.

### Rationale

- Verification and reset links are familiar to users and commonly supported by managed identity providers.
- Email-only recovery reduces SMS cost and recovery friction.
- Session revocation and channel notifications provide visible response after credential changes.
- Phone changes receive an additional current-email confirmation because phone verification alone would permit a stolen session to replace the number.
- Recent reauthentication and new-email verification reduce risk while preserving the Product Owner's preference not to require old-email approval for email changes.

### Alternatives Considered

- **Email verification code entered in the Flutter app:** Not selected because the Product Owner prefers a verification link. It would avoid deep-link/browser flow but adds code-entry and resend/attempt handling.
- **Two-channel password recovery with mandatory SMS after email reset:** Not selected because the Product Owner prefers email-only recovery and accepts its residual mailbox-compromise risk.
- **Phone-only recovery:** Rejected because SIM-swap, number recycling, lost-number handling, delivery, and cost make it unsuitable as the sole recovery authority.
- **Support-only recovery:** Not selected as the normal flow because it creates high support burden and social-engineering risk; manual restricted recovery remains necessary when channels are lost.
- **Old-and-new channel approval for every contact change:** Not selected because it can permanently block legitimate users who lost the old channel. The approved flow uses recent reauthentication, new-channel verification, notifications, and additional current-email confirmation for phone changes.

### Consequences

**Benefits**

- Familiar registration and recovery experience.
- Managed provider owns verification/reset challenge security.
- Lower SMS use than two-channel recovery.
- Explicit contact-change controls and security notifications.
- Stable internal identity across contact changes.

**Costs and constraints**

- Compromised email can enable password reset and contributes to email-change risk.
- Email delivery delays, filtering, link scanners, browser/app transitions, and provider outages require testing and support handling.
- Kirim/provider session invalidation must be coordinated and verified.
- Lost access to email may require restricted manual recovery that the MVP cannot resolve through strong government-ID proof.
- Old channel notifications are detective controls, not preventive approval.

### Revisit Triggers

Review this decision if:

- Account-takeover or reset-abuse incidents exceed accepted risk.
- Email delivery/link completion causes significant user drop-off.
- User research strongly prefers in-app codes or social login.
- Stronger identity verification enables safer manual recovery.
- Provider session revocation or link-security behavior is insufficient.
- Regulation, payment scope, or stored-value behavior increases required authentication assurance.

Any change to recovery authority or contact-linking rules requires threat modeling, provider tests, account-migration/linking analysis, forced-session behavior, support procedures, and audit updates.

### Affected Areas

- `docs/TECH_STACK.md`
- Flutter verification, reset, and contact-change UI
- Identity-provider and email/SMS provider evaluation
- FastAPI account activation, contact-change, session-version, and authorization checks
- Internal identity/contact schema and uniqueness rules
- Security notifications, audit events, support recovery, and incident response

---

## DEC-016 — 2026-10-05 — Use Hybrid Module-Owned Authorization Policies

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim authorization depends on more than a global role. A single account may use Customer and Shopper modes, but access to a transaction depends on the actor's relationship to that resource, account verification/status, the current workflow state, active Shopper capacity, case assignment, recent authentication, and dispute/payment conditions. Administrator and background/provider actions also require bounded authority and auditability.

### Decision

Use **deny-by-default hybrid authorization policies** owned by the relevant backend domain modules.

Authorization decisions combine:

- Coarse role/capability, such as verified user, assigned Support, Administrator, system job, or verified provider event.
- Resource relationship, such as transaction Customer, confirmed Shopper, or assigned support-case handler.
- Principal/account attributes, such as active status, verified contacts, authentication assurance/freshness, and active-transaction capacity.
- Resource/workflow state, such as negotiating, collecting prices, awaiting balance, purchased, delivering, disputed, or settlement eligible.

Implementation rules:

- The FastAPI authentication boundary creates an internal principal using the stable Kirim user ID and Kirim-owned status/capabilities; provider claims do not directly become domain permissions.
- Customer/Shopper UI mode is presentation context, not authorization authority.
- Each module owns named policies for its actions and exposes controlled authorization interfaces.
- Application/use-case services enforce authorization before sensitive reads or state changes. Flutter UI checks, React route guards, and FastAPI route dependencies may improve usability/early rejection but never replace service-layer authorization.
- API and worker paths call the same application policies and state-transition rules.
- Read queries are scoped to authorized resources where practical to reduce accidental disclosure and unnecessary data access.
- Database constraints, uniqueness, locking, and transaction rules provide a second integrity layer but do not replace authorization.
- Support access requires explicit assignment/capability, minimum necessary evidence, and audited sensitive reads/actions.
- Background jobs are scoped by job type/entity/current state and do not receive an unrestricted bypass.
- Provider webhooks require verified authenticity, replay/idempotency controls, provider/account mapping, and valid state transitions; provider events do not bypass domain invariants.
- Authorization failures use disclosure-safe API behavior defined later in `docs/API_CONTRACTS.md`.
- Policies and denial paths receive direct automated tests, including cross-user, wrong-role/relationship, suspended account, wrong state, unassigned support, stale assurance, and replay scenarios.

This decision does not select an external policy library, decorator pattern, policy function API, permission naming convention, SQL row-level security, or exact 403/404 response rule. For the MVP, policy logic remains inside Kirim code rather than an external policy engine.

### Rationale

- Pure roles cannot represent transaction ownership, case assignment, or workflow state.
- Module ownership keeps policy close to domain invariants while avoiding scattered route-handler checks.
- Shared application policies keep API and worker behavior consistent.
- Deny by default reduces accidental authorization through missing rules.
- Avoiding an external engine keeps MVP deployment and debugging manageable.

### Alternatives Considered

- **Role-based access control only:** Not selected because Customer/Shopper modes and broad Admin/Support roles cannot determine ownership, case scope, transaction state, verification, or active capacity.
- **Scattered route/controller ownership checks:** Rejected because checks would be duplicated, easy to omit, inconsistent between API and worker, and difficult to audit/test systematically.
- **External policy engine:** Deferred because another runtime and policy language are disproportionate for one modular monolith and would require securely synchronizing application state into policy inputs. Reconsider only if organizational or cross-service policy complexity later justifies it.
- **Client-side authorization:** Rejected as a security control because public clients are untrusted and can be modified or bypassed.

### Consequences

**Benefits**

- Policies accurately model role, relationship, account attributes, state, and assignment.
- Consistent enforcement across clients, API, worker, and provider-event processing.
- Testable denial and ownership boundaries.
- Clear policy ownership within the modular monolith.
- No additional policy runtime for the MVP.

**Costs and constraints**

- Policy interfaces and naming require discipline to avoid duplication or cycles.
- Complex queries must preserve both authorization and performance.
- Policy changes can affect multiple workflows and require regression/security tests.
- Sensitive-read auditing and support-case scoping add data and operational design work.
- Developers must not confuse UI mode, identity-provider groups, or possession of an ID with authorization.

### Revisit Triggers

Review this decision if:

- Policy duplication or cross-module inconsistencies persist despite shared conventions.
- Multiple services or teams need independently managed policy distribution.
- Regulation requires centrally administered policy or database row-level enforcement.
- Policy evaluation performance cannot meet targets with application/database scoping.
- Security review demonstrates a benefit from a verified policy engine that exceeds its operational cost.

Moving to an external engine or database row-level security as a primary layer requires a new decision, policy migration/equivalence tests, outage/fallback behavior, and rollback plan.

### Affected Areas

- `docs/TECH_STACK.md`
- `docs/API_CONTRACTS.md`
- Module application-service and policy structure
- Principal/account/session models
- Database query scoping and integrity constraints
- Support assignment, evidence access, and auditing
- Worker and provider-webhook execution
- Authorization and security test suites

---

## DEC-017 — 2026-10-05 — Combine Provider Revocation with Kirim Security State

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Managed-provider access tokens may remain cryptographically valid for part of their lifetime after password reset, device loss, suspension, contact change, or reported compromise. Kirim needs immediate control over business access without depending solely on provider revocation timing, while preserving active transaction records and routing unresolved obligations to Support.

### Decision

Combine provider session/refresh revocation with **Kirim-owned account security state** checked on every protected action.

Kirim maintains at least the conceptual equivalents of:

- Account status.
- Security/compromise hold status.
- Authentication-valid-after timestamp or security version.
- Relevant recovery/suspension reason and audit references.

For each protected request, Kirim validates provider authentication and then rejects access when the internal account is suspended/on hold or the authentication/session predates the latest required security reset. The exact mechanism depends on verified provider claims/session capabilities; if issuance-time or equivalent binding is unavailable, Kirim must introduce another reviewed session-binding mechanism rather than claiming immediate invalidation.

Security events such as password reset, confirmed/reported compromise, logout-all-devices, sensitive contact changes as defined by policy, role removal, or authorized suspension can:

- Advance the Kirim authentication-valid-after/security version.
- Request provider refresh/session revocation.
- Revoke Kirim Admin sessions immediately where applicable.
- Reject new business actions and sensitive reads.
- Stop active location sharing and sensitive background behavior where technically possible.
- Preserve active transaction/evidence records and route obligations to Support rather than silently cancelling, settling, refunding, or changing them.
- Notify verified channels where safe.
- Create immutable, attributable security/audit events.

Support these user-facing concepts where provider capability permits:

- Logout this device: clear local credentials and revoke the relevant provider session.
- Logout all devices: advance Kirim security validity/version and request provider-wide revocation.
- Security hold: deny Kirim business access even when provider authentication still succeeds.

Restoring access requires an approved recovery path, account-state update, fresh authentication, and new security events. Existing sessions are not silently reactivated.

Do not maintain a denylist of every token by default and do not call provider introspection on every request unless the selected token format/provider requires it. Exact schema fields, cache behavior, session/device UI, security-event triggers, recovery authority, and provider APIs remain to be designed and tested.

### Rationale

- Gives Kirim immediate authority over its own business access.
- Reduces dependence on provider access-token revocation timing.
- Reuses the mandatory account-status/authorization check on protected actions.
- Security version/valid-after invalidates broad classes of old authentication without tracking every token.
- Preserves transaction integrity during compromise instead of performing unsafe automatic financial actions.

### Alternatives Considered

- **Provider revocation and token expiry only:** Rejected because existing access tokens may remain usable until expiry and provider behavior may not provide immediate Kirim-side suspension.
- **Denylist every revoked token:** Not selected as the primary model because it requires reliable token identifiers, storage/cleanup, and still does not solve refresh-session revocation by itself.
- **Provider introspection on every request:** Not selected by default because it adds provider latency, availability, cost, and rate-limit dependency to every API call. It remains necessary if a future provider uses opaque access tokens requiring introspection.
- **Long-lived access tokens:** Rejected because they increase compromise duration and weaken device-loss response.

### Consequences

**Benefits**

- Immediate internal account suspension and compromise holds.
- Clear logout-all and security-reset semantics.
- Provider and Kirim controls reinforce each other.
- Active obligations are preserved for safe Support resolution.

**Costs and constraints**

- Every protected path must reliably enforce current Kirim account/security state.
- Provider token issuance/session claims and revocation APIs must be verified.
- Database/account lookup and security-state consistency affect authenticated request availability.
- Device-specific revocation may remain limited by provider capabilities.
- Recovery, false-positive holds, and Support authority require careful procedures.

### Revisit Triggers

Review this decision if:

- The provider cannot expose or bind sufficient token/session timing information.
- Account-state lookup becomes a measured performance bottleneck.
- Device-bound or sender-constrained tokens become required.
- Stored-value, regulation, or incident evidence requires stronger continuous authentication.
- Multiple identity providers require a unified Kirim session layer.

Any replacement requires revocation-equivalence tests, forced reauthentication planning, provider-outage behavior, active-transaction safety, and rollback procedures.

### Affected Areas

- `docs/TECH_STACK.md`
- Internal account/security schema
- FastAPI authentication and authorization pipeline
- Flutter logout, device-loss, and compromised-account UX
- Admin session revocation
- Provider evaluation and reconciliation
- Support cases, security notifications, audit, and incident response

---

## DEC-018 — 2026-10-05 — Use a Pragmatic REST-Style JSON API with OpenAPI

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim needs one backend contract for the Flutter application, React Admin Console, provider webhooks, automated tests, and potential generated client models. The domain combines ordinary resource reads/creation with strict workflow actions such as confirmation, cancellation, substitution approval, handover, dispute, refund, and settlement. Clients must not set arbitrary state values or receive direct ORM representations.

### Decision

Use a **pragmatic REST-style HTTP JSON API documented through OpenAPI**.

- Use resource-oriented endpoints for collection/item reads and ordinary creation.
- Represent consequential state transitions as explicit action/subresource creation rather than generic status mutation. Examples of the style include transaction confirmations, cancellations, handover confirmations, and disputes.
- Do not expose a generic endpoint that lets clients set transaction/payment state directly.
- Keep transport schemas separate from ORM/database and internal domain models.
- Generate and validate an OpenAPI document in CI and use it to generate or validate Flutter/TypeScript client models where practical.
- Use dedicated verified provider-webhook endpoints; webhook payloads are external contracts and must be authenticated, idempotent, retained/reconciled as required, and translated into domain commands.
- Apply backend authorization and workflow validation to every operation regardless of what the client UI permits.
- Return only fields permitted for the authenticated principal and current relationship/state.
- Treat API state retrieval as authoritative; push notifications only prompt clients to refresh/reconcile.

This decision selects the API style, not the detailed conventions. Versioning, error format, pagination, filtering, idempotency headers, concurrency control, file transfer, real-time messaging, and exact endpoint/resource names remain separate decisions for `docs/API_CONTRACTS.md` and the technology standards.

### Rationale

- FastAPI has direct support for HTTP validation and OpenAPI generation.
- REST-style JSON is straightforward for Flutter, React, provider webhooks, testing, and operational debugging.
- Explicit action subresources accurately represent workflow intent without allowing arbitrary state mutation.
- One OpenAPI contract can reduce client/backend drift.
- The approach avoids adding GraphQL/gRPC tooling without an approved need.

### Alternatives Considered

- **GraphQL:** Not selected because client-defined query flexibility does not currently justify field-level authorization, query-cost, caching, schema, data-loader, and audit complexity. Mutations would still need strict workflow semantics.
- **Pure RPC/command-style JSON:** Not selected as the sole style because resource reads, collections, standard HTTP semantics, and discoverability would become less consistent. Explicit action subresources retain command clarity where needed.
- **gRPC:** Not selected because browser/mobile integration and operational debugging add complexity, while Kirim has no internal microservices or throughput requirement that needs it.
- **REST plus GraphQL hybrid:** Not selected because two schemas, authorization surfaces, toolchains, and test strategies are disproportionate for the MVP.

### Consequences

**Benefits**

- One inspectable HTTP/JSON contract for both clients.
- Strong FastAPI/OpenAPI fit.
- Clear resource ownership and explicit workflow actions.
- Practical contract testing and client-model generation.
- Familiar provider-webhook integration.

**Costs and constraints**

- API conventions must be consistently designed and reviewed.
- Complex screens may require multiple requests or purpose-built authorized projections.
- OpenAPI generation does not guarantee compatibility; breaking-change checks and contract review remain necessary.
- Action-subresource naming and response semantics require discipline to avoid arbitrary RPC disguised as REST.

### Revisit Triggers

Review this decision if:

- Measured client read patterns create unmanageable request multiplication despite suitable projections.
- A public API or partner ecosystem introduces substantially different contract needs.
- Internal service extraction creates a proven need for another protocol.
- OpenAPI tooling cannot represent an approved contract requirement safely.

Adding GraphQL, gRPC, or another protocol requires a new decision, authorization/threat review, compatibility strategy, and client migration plan.

### Affected Areas

- `docs/TECH_STACK.md`
- `docs/API_CONTRACTS.md`
- FastAPI transport schemas and routing
- Flutter and React API clients
- Provider webhook adapters
- Authorization, idempotency, contract tests, and CI

---

## DEC-019 — 2026-10-18 — Standardize API Versioning, Problems, Pagination, Filtering, Idempotency, and Concurrency

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Flutter and React clients need predictable API behavior across independently deployed versions, retries, changing collections, validation failures, and concurrent workflow actions. Kirim also handles consequential payment and state transitions where network retries or stale client state must not create duplicate or overwritten effects.

### Decision

Adopt the following API conventions for the REST-style JSON API accepted in DEC-018.

**Versioning**

- Begin public application endpoints under `/api/v1`.
- Keep compatible additive changes within `v1`.
- Require explicit review for breaking changes; introduce a new major path only when compatibility cannot reasonably be preserved.
- Provide deprecation notice, migration guidance, telemetry/usage review where available, and an approved transition period before retiring a supported version.
- Provider webhook paths/contracts may follow provider-specific versioning but must be isolated from public client contracts.

**Errors**

- Use RFC 9457 Problem Details with media type `application/problem+json`.
- Include standard fields as applicable and Kirim extensions for a stable machine-readable `code`, safe request/correlation ID, field-level validation issues, and safe retry information.
- Never expose stack traces, SQL/database internals, secrets/tokens, provider credentials, private resource existence, or internal fraud/security reasoning.
- Document stable error-code semantics independently from human-readable/localized text.

**Pagination**

- Use opaque cursor pagination as the default for collections.
- Use deterministic stable ordering with a unique tie-breaker.
- Define default/maximum page size, direction behavior, and cursor expiry/validation per contract.
- Bind cursors to relevant filter/sort context and prevent clients from constructing or modifying cursor internals.
- Allow offset pagination only through a separately approved endpoint-specific exception, such as a bounded low-volume Admin report.

**Filtering and sorting**

- Support only endpoint-specific allowlisted filters and sort fields.
- Reject unknown/invalid filters rather than silently ignore them.
- Document null, range, date/time, case, enum, and multi-value semantics.
- Apply authorization before returning/filtering sensitive data and constrain expensive ranges/queries.
- Define a deterministic default order.

**Idempotency**

- Require a client-generated `Idempotency-Key` for consequential creation/action requests, including confirmation, payment creation, cancellation, purchase completion, handover confirmation, dispute creation, refund, reimbursement, payout/settlement, and consequential Admin actions as applicable.
- Scope the key by authenticated principal/service identity and operation/resource context.
- Store a canonical request fingerprint, processing state, response/result reference, and retention metadata.
- Return/reconstruct the original outcome for a repeated matching request without repeating its effect.
- Reject reuse of a key with a different canonical request as a conflict.
- Define safe in-progress/retry behavior and combine Kirim idempotency with database uniqueness/transactions and provider idempotency identifiers where available.
- Retention is operation-specific: ordinary action keys may expire sooner, while financial keys/effects remain linked according to financial/audit retention rules.
- Treat the `Idempotency-Key` specification as a Kirim-documented contract; the current IETF work is an Internet-Draft, not assumed to be a finalized RFC.

**Concurrency**

- Use strong ETags and `If-Match` for eligible editable resources.
- Return `412 Precondition Failed` when an explicit `If-Match` precondition fails.
- For workflow action endpoints, re-evaluate authorization/current state inside the database transaction and use locking, versioning, uniqueness, and constraints as required.
- Return `409 Conflict` when a syntactically valid action conflicts with current domain state.
- Do not automatically retry stale consequential business actions; clients refresh authoritative state and obtain explicit user intent where required.
- Never use silent last-write-wins for consequential or user-editable records.

Exact URL/resource names, problem type URIs, error-code catalogue, cursor encoding, limits, canonical fingerprinting, idempotency retention, ETag representation, and compatibility windows will be specified in `docs/API_CONTRACTS.md` and tested before implementation/release.

### Rationale

- URL major versions are visible and practical for mobile clients with delayed upgrades.
- RFC 9457 provides a standard machine-readable error shape without inventing the full envelope.
- Cursor pagination remains stable as transaction/message collections change.
- Allowlisted filters protect authorization, performance, and contract clarity.
- Idempotency keys address retry uncertainty for non-idempotent financial/workflow actions.
- ETags/preconditions and transactional state checks prevent lost updates and stale transitions.

### Alternatives Considered

- **Header-based or unversioned APIs:** Not selected because URL major versioning is clearer in mobile debugging, routing, logs, and parallel support. Unversioned contracts weaken breaking-change control.
- **Custom-only error envelope:** Not selected because RFC 9457 supplies interoperable standard fields while still allowing Kirim extensions.
- **Offset pagination by default:** Not selected because changing timelines can duplicate/skip records and large offsets scale poorly. Endpoint-specific bounded exceptions remain possible.
- **Arbitrary client query/filter language:** Rejected because it increases data-exposure, authorization, indexing, query-cost, and documentation risk.
- **No explicit idempotency keys:** Rejected because lost responses and retries could duplicate consequential effects despite client intentions.
- **Per-token or random retry detection only:** Not selected because the client needs a stable operation key and the server must compare request intent.
- **Last-write-wins concurrency:** Rejected because it can silently overwrite decisions and violate workflow/payment integrity.

### Consequences

**Benefits**

- Predictable contracts for Flutter, React, tests, and support.
- Safer retries and concurrent updates.
- Stable collection traversal.
- Standardized machine-readable failures.
- Clearer breaking-change governance.

**Costs and constraints**

- Idempotency storage/fingerprinting, cursor encoding, ETags, and compatibility checks require implementation and tests.
- Clients must persist operation keys through retries and handle conflicts/precondition failures.
- Endpoint designers must define filters, ordering, errors, and action semantics explicitly.
- Old major versions may require temporary parallel support.
- Error details must be reviewed continuously for privacy and security disclosure.

### Revisit Triggers

Review individual conventions if measured client, scale, or public-partner needs demonstrate a limitation. Any exception must be endpoint-specific, documented, security/performance reviewed, and contract tested. Changing global versioning, error, pagination, or idempotency semantics requires a superseding decision and client migration plan.

### References

- RFC 9457, *Problem Details for HTTP APIs*: https://www.rfc-editor.org/rfc/rfc9457.html
- RFC 9110, *HTTP Semantics* (`If-Match` and preconditions): https://www.rfc-editor.org/rfc/rfc9110.html
- IETF HTTPAPI `Idempotency-Key` work item (Internet-Draft status must be rechecked before use): https://datatracker.ietf.org/doc/draft-ietf-httpapi-idempotency-key-header/

### Affected Areas

- `docs/TECH_STACK.md`
- `docs/API_CONTRACTS.md`
- FastAPI middleware, routing, schemas, and transaction handling
- Flutter and React API clients
- Database idempotency, versioning, and indexing schema
- OpenAPI/compatibility tests, observability, and support tooling

---

## DEC-020 — 2026-10-18 — Use Python 3.14 and Compatibility-Tested Locked Backend Dependencies

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

DEC-008 selected Python and FastAPI for Kirim's API and durable Worker. The runtime and framework versions must balance security/support lifetime, hosting and native-package availability, ecosystem compatibility, reproducibility, and upgrade effort. Python 3.15 and the newest FastAPI releases were newly available at the time of review, while implementation and the managed application provider had not yet been selected. Pinning a just-released or soon-to-stale package set in planning would create risk without validating the complete dependency graph.

### Decision

- Use **CPython 3.14** as the initial backend runtime minor line for the API, Worker, local development, CI, and production.
- At implementation/scaffolding time, select the latest non-pre-release, non-yanked Python 3.14 patch supported by the chosen managed application platform and the complete tested dependency set.
- Use FastAPI with an ASGI server, expected initially to be Uvicorn, subject to deployment-platform verification.
- Select FastAPI, Pydantic, Starlette, Uvicorn, database drivers, geospatial/native dependencies, and related backend packages as a compatible set rather than independently choosing each newest release.
- Record exact direct and transitive versions in the selected lockfile and pin the deployed Python patch/container image or digest according to the deployment mechanism.
- Use the same runtime/dependency lock in CI and both API and Worker builds. Development environments must use the same Python minor line.
- Exclude prerelease, development, and yanked packages by default.
- Do not automatically upgrade production dependencies. Upgrades require changelog/security review and relevant format, lint, type, unit, integration, migration, OpenAPI/compatibility, worker, and startup/smoke checks.
- Security fixes may require accelerated upgrades, but must still receive proportionate compatibility and rollback testing.
- Python 3.15 is not the initial baseline. Reconsider it after hosting and critical dependency support are verified and the upgrade has a demonstrated benefit.

Exact package versions are intentionally deferred until the first reproducible backend scaffold. They are implementation configuration recorded in manifests and the lockfile, not permanent architecture choices. The accepted architectural constraint is the Python 3.14 runtime line and compatibility-tested locking policy.

### Rationale

- Python 3.14 provides a current supported runtime without adopting a newly released minor immediately.
- Selecting the dependency graph together accounts for FastAPI/Pydantic/Starlette compatibility and native/database package constraints.
- A lockfile gives reproducible builds while avoiding planning-time pins that become stale before implementation.
- Matching API, Worker, CI, and production reduces environment-specific defects.
- The policy supports deliberate security maintenance without uncontrolled automatic upgrades.

### Alternatives Considered

- **Immediately adopt Python 3.15 and the newest package releases:** Not selected because the runtime and several packages were newly released, hosting and full dependency compatibility were unverified, and there was no approved feature requiring them.
- **Pin all exact versions during discovery:** Rejected because implementation timing and the hosting provider remain undecided; those pins could be obsolete before the first build and would not prove compatibility.
- **Use an older Python line such as 3.12 or 3.13:** Not selected because 3.14 offers a longer forward support window and current ecosystem metadata indicates relevant ASGI tooling supports it; downgrade remains possible before scaffolding if a critical verified incompatibility appears.
- **Allow broad unpinned production ranges:** Rejected because builds would not be reproducible and indirect releases could change production behavior unexpectedly.
- **Treat a backend-language rewrite as the expected production path:** Rejected. Python/FastAPI is intended to be production-capable; another language requires measured justification and a separate migration decision.

### Consequences

**Benefits**

- A modern but less speculative runtime baseline.
- Reproducible API and Worker environments.
- Compatibility is proven across the dependency set rather than inferred package by package.
- Cleaner security and upgrade governance.
- Exact pins remain timely when implementation starts.

**Costs and constraints**

- Scaffolding has a required compatibility-verification gate before versions are accepted.
- The project must actively review security and end-of-support dates.
- Managed-platform or native-library limitations could force a documented downgrade before implementation.
- Lockfiles and runtime/container pins must be intentionally maintained.

### Revisit Triggers

Review this decision if:

- The selected application platform does not support Python 3.14 adequately.
- A required database, geospatial, payment, authentication, observability, or security dependency is incompatible.
- Python 3.14 approaches end of security support.
- A newer Python minor has mature provider/dependency support and a concrete project benefit.
- Production evidence demonstrates a language/runtime limitation that cannot be addressed economically within Python.

A runtime-minor change requires CI/production compatibility testing and a documented deployment/rollback plan. A backend-language change requires a separate architectural decision and incremental migration plan.

### References

- Python 3.14 release schedule (PEP 745): https://peps.python.org/pep-0745/
- FastAPI package metadata: https://pypi.org/project/fastapi/
- Uvicorn package metadata: https://pypi.org/project/uvicorn/

### Affected Areas

- `docs/TECH_STACK.md`
- Backend runtime and dependency manifests/lockfile
- API and Worker deployment images/configuration
- Local development and CI
- Security maintenance and upgrade procedures

---

## DEC-021 — 2026-10-18 — Use Psycopg 3 with Synchronous PostgreSQL Access Initially

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim's FastAPI API and durable Worker need a consistent PostgreSQL/PostGIS access model. The invite-only MVP is expected to be dominated by short transactional operations at modest concurrency. Asynchronous database access can reduce thread occupancy under high concurrent wait, but it propagates `async` through application and persistence code, adds session/cancellation complexity, and does not increase PostgreSQL's connection capacity. The chosen driver should remain current and leave a credible async migration path if measurements later justify it.

### Decision

- Use **Psycopg 3** as the PostgreSQL driver and use its **synchronous access model initially** for both API and Worker database operations.
- Use one database session/connection and transaction scope per request, Worker job, or explicitly bounded unit of work. Never share a session/connection across concurrent requests, jobs, or threads.
- Keep transactions short. Do not hold an open database transaction while calling payment, identity, messaging, notification, mapping, or object-storage providers.
- FastAPI operations that perform synchronous database/application work must use a consistent synchronous execution path that FastAPI can offload, or explicitly offload the complete bounded synchronous unit. Never call blocking database helpers directly on an event-loop thread from `async def` code.
- Separate transaction boundaries from transport handlers and make commit/rollback ownership explicit. Do not commit implicitly inside repositories.
- Coordinate process/thread concurrency, database pool sizes, Worker concurrency, and the managed PostgreSQL connection limit.
- Apply statement, lock, connection, and external-call timeouts through later operational decisions; do not rely on unlimited waits.
- Reconsider asynchronous database access only after representative load/latency evidence identifies thread occupancy or concurrent database waiting as a material bottleneck.
- If async access is later adopted, use one async session per task and migrate bounded modules/use cases deliberately; do not mix sync and async database operations inside one transaction.

This decision selects the driver and initial I/O model only. ORM/query style, migration tooling, pooling implementation, transaction isolation, and exact dependency versions remain separate decisions.

### Rationale

- Synchronous transactional code is simpler to implement, review, debug, and test for a domain with payments, ledger effects, disputes, and workflow guards.
- It naturally fits the durable Worker as well as the API, reducing duplicate persistence patterns.
- FastAPI supports synchronous route/dependency execution through its thread-pool behavior when used correctly.
- MVP throughput does not currently justify async database complexity.
- Psycopg 3 is the current driver generation and supports both synchronous and asynchronous APIs, preserving a future option without selecting two drivers now.

### Alternatives Considered

- **Psycopg 3 asynchronous access:** Not selected initially because no measured concurrency requirement offsets the additional async transaction, cancellation, session-lifecycle, and testing complexity.
- **`asyncpg`:** Not selected because it is async-only and Psycopg 3 already provides a supported async path if required. Kirim has no demonstrated driver-specific performance need.
- **Psycopg 2:** Not selected because Kirim is a new project and Psycopg 3 is the current driver generation with modern sync/async support.
- **Mixed sync API and async Worker, or the reverse:** Rejected initially because two persistence execution models would increase testing and operational complexity without a proven benefit.

### Consequences

**Benefits**

- Straightforward transaction and unit-of-work behavior.
- One initial persistence execution model across API and Worker.
- Easier testing, debugging, and failure analysis.
- Future async capability remains available through Psycopg 3.

**Costs and constraints**

- Blocking database work consumes a thread while waiting.
- Route declarations and helper calls require discipline to avoid blocking the ASGI event loop.
- Process, thread, Worker, and pool concurrency must be bounded against database limits.
- Moving to async later requires application/repository call-chain changes despite retaining the same driver family.

### Revisit Triggers

Review this decision if representative load tests or production telemetry show unacceptable queueing/thread saturation attributable to database waiting; if a required library lacks adequate synchronous support; or if a future real-time workload produces a demonstrated concurrency need. Revisit only after query/index optimization, transaction-duration review, and pool/process tuning are evaluated.

### References

- Psycopg 3 basic usage: https://www.psycopg.org/psycopg3/docs/basic/usage.html
- Psycopg 3 concurrent/async operations: https://www.psycopg.org/psycopg3/docs/advanced/async.html
- FastAPI concurrency guidance: https://fastapi.tiangolo.com/async/

### Affected Areas

- `docs/TECH_STACK.md`
- Persistence adapters and transaction/unit-of-work boundaries
- FastAPI route/dependency execution style
- Worker execution model
- Connection pooling and deployment concurrency
- Database integration and load tests

---

## DEC-022 — 2026-10-18 — Use a Pragmatic SQLAlchemy 2 ORM and Explicit-Query Hybrid

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim needs ordinary relational persistence alongside workflow locking, authorization-constrained reads, audit/support projections, ledger queries, and PostGIS matching. An ORM improves routine mapping and unit-of-work behavior, but forcing every spatial or performance-sensitive query through object relationships can hide SQL, cause accidental loading, and make database-specific capabilities awkward. Conversely, Core/raw-SQL-only access would add repetitive mapping for normal transactional work. API contracts must remain independent from persistence representations.

### Decision

- Use **SQLAlchemy 2** as the database toolkit with a pragmatic hybrid approach:
  - typed declarative ORM mappings and sessions for ordinary persistence;
  - SQLAlchemy Core expressions or reviewed parameterized SQL for complex PostGIS, reporting, locking, bulk, and performance-sensitive queries where they are clearer or more controllable.
- Use SQLAlchemy 2-style `select()` and typed `Mapped[...]`/`mapped_column()` APIs; do not introduce the legacy `Query` API.
- Keep request/response Pydantic schemas separate from ORM mappings. Never directly serialize ORM objects as public API responses.
- Each domain module owns its mappings and persistence/query adapters. Cross-module reads use explicit application contracts or purpose-built read projections rather than arbitrary relationship traversal.
- Do not create a generic repository/base-service abstraction. Introduce a repository or query interface only where it expresses a domain boundary, improves testing, or supports multiple implementations.
- Make transaction/unit-of-work ownership explicit in the application layer, consistent with DEC-021. Repositories may flush but do not independently commit hidden transactions.
- Load relationships explicitly for each use case. Configure or apply raise-on-unexpected-lazy-load behavior where practical, and test important query counts to detect N+1 regressions.
- Apply authorization/disclosure constraints before or within data retrieval when loading unauthorized rows would create unnecessary exposure risk. Response mapping remains a second defensive boundary.
- Use bound parameters exclusively for dynamic values. Never construct SQL using string interpolation or concatenation of untrusted values. Dynamic identifiers and sort/filter fields come only from allowlists.
- Consider GeoAlchemy2 for PostGIS type/function integration after Python 3.14, SQLAlchemy, Psycopg, Alembic, and PostGIS compatibility verification. Its selection and exact version are implementation details unless testing reveals a consequential limitation.
- Permit database-specific SQL where it provides correctness or clarity. Isolate it in the owning persistence adapter, document why it is database-specific, and cover it with real-PostgreSQL integration tests.
- Profile and inspect generated SQL/query plans before replacing straightforward ORM/Core queries solely for presumed performance.

This decision does not select migration tooling, schema conventions, spatial representation/SRID semantics, pooling, or exact package versions.

### Rationale

- SQLAlchemy provides one session, transaction, mapping, and expression ecosystem over Psycopg 3.
- Typed ORM mappings reduce repetitive ordinary persistence code.
- Core and parameterized SQL preserve direct use of PostgreSQL/PostGIS strengths.
- Explicit loading and query ownership reduce hidden I/O and N+1 behavior.
- Separating API schemas from mappings protects contracts and disclosure boundaries.
- Avoiding a generic repository prevents abstractions that merely mirror SQLAlchemy without domain value.

### Alternatives Considered

- **SQLAlchemy ORM for every query:** Not selected because complex PostGIS, reporting, locking, and tuned projections may be clearer and safer as Core or explicit SQL.
- **SQLAlchemy Core/parameterized SQL only:** Not selected because it would add mapping and persistence boilerplate for normal transactional aggregates without a demonstrated benefit.
- **SQLModel:** Not selected because combining Pydantic and SQLAlchemy concepts provides limited value when Kirim intentionally separates transport and persistence models, and it adds another abstraction over SQLAlchemy.
- **Direct Psycopg only:** Not selected because Kirim would need to recreate mapping, unit-of-work, expression, and migration integration patterns while still supporting many ordinary relational operations.
- **A generic repository framework:** Rejected because it tends to obscure useful SQLAlchemy/PostgreSQL capabilities and produce lowest-common-denominator interfaces.

### Consequences

**Benefits**

- Productive ordinary persistence plus precise complex queries.
- One transaction and database-toolkit model across modules.
- Explicit boundary between API contracts and stored representations.
- Direct path to PostGIS and PostgreSQL-specific correctness/performance features.
- Reduced pressure to prematurely abstract all persistence behavior.

**Costs and constraints**

- Contributors must understand SQLAlchemy ORM, Core, SQL, PostgreSQL, and transaction behavior.
- The codebase needs review discipline to keep query styles coherent.
- Mapping between transport/domain projections and persistence objects adds explicit code.
- Integration tests require real PostgreSQL/PostGIS for database-specific behavior.
- Lazy-loading and query-count regressions require active prevention and detection.

### Revisit Triggers

Review this decision if SQLAlchemy/Psycopg/PostGIS compatibility fails; profiling shows the ORM causes persistent unacceptable behavior despite explicit query use; mapping overhead becomes disproportionate; or a module has a proven need for a different persistence approach. Exceptions remain module-bounded and require integration/contract tests; they do not automatically replace the project-wide toolkit.

### References

- SQLAlchemy 2 typed declarative mapping: https://docs.sqlalchemy.org/en/20/orm/declarative_tables.html
- SQLAlchemy 2 ORM querying: https://docs.sqlalchemy.org/en/20/orm/queryguide/select.html
- SQLAlchemy relationship loading: https://docs.sqlalchemy.org/en/20/orm/loading_relationships.html
- GeoAlchemy2 documentation: https://geoalchemy-2.readthedocs.io/

### Affected Areas

- `docs/TECH_STACK.md`
- `docs/DATABASE_SCHEMA.md`
- Domain persistence adapters and read projections
- API response mapping
- PostGIS, query-plan, authorization, and integration tests

---

## DEC-023 — 2026-10-18 — Use Alembic with Reviewed, Forward-Safe Database Migrations

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim needs reproducible PostgreSQL/PostGIS schema evolution coordinated with SQLAlchemy mappings, application deployments, persistent financial/workflow data, and managed-database recovery. Schema changes must be reviewable, testable, and safe under deployment overlap. SQLAlchemy model metadata alone is not migration history, while provider-console changes would not be reproducible. Alembic integrates with the selected SQLAlchemy toolkit, but its own documentation treats autogenerated revisions as candidates requiring manual review, and spatial operations need additional care.

### Decision

- Use **Alembic** as Kirim's database schema-migration tool.
- Commit every migration and apply migrations in deterministic revision order.
- Never edit, reorder, or delete a migration after it has been applied to any shared environment. Add a corrective migration instead.
- Maintain one linear migration head by default. Parallel heads and merge revisions require explicit review and CI detection.
- Treat Alembic autogeneration as a drafting aid only. Review generated operations, names, types, defaults, constraints, indexes, data-loss implications, downgrade behavior, and generated SQL before acceptance.
- Permit reviewed Alembic operations and static/parameterized PostgreSQL/PostGIS SQL when explicit SQL is clearer or required. Never interpolate untrusted data into migration SQL.
- Keep migrations self-contained and stable: do not import mutable runtime services, current ORM models, provider clients, or business workflows. Copy small stable helpers/constants into a migration when necessary and justified.
- Execute migrations once per deployment through a controlled release/migration job with a dedicated identity and appropriate privileges. Do not let every API or Worker replica migrate automatically on startup.
- Test migration from an empty database and from the previous supported schema against real PostgreSQL/PostGIS. CI must detect multiple heads and model/schema drift according to the eventual schema-check procedure.
- Use expand-and-contract sequencing for changes where old and new application versions may overlap: add compatible structures, deploy compatible code/backfill, switch reads/writes, then remove obsolete structures in a later release.
- Before destructive or high-risk operations, document backup/restore readiness, affected data/volume, lock/runtime expectations, application compatibility, abort criteria, and recovery steps.
- Prefer a corrective forward migration for production recovery. Implement `downgrade()` only when genuinely safe and useful; never treat a destructive downgrade as an automatic rollback guarantee.
- Separate large data backfills from blocking schema changes where practical. Make backfills bounded, observable, idempotent/resumable, and safe to pause.
- Configure appropriate lock and statement timeouts and avoid long table locks. Index/constraint creation strategy must account for table size and the managed provider's PostgreSQL capabilities.
- Verify PostGIS extension ownership/privileges and manually review spatial columns, SRIDs, functions, constraints, and indexes. If GeoAlchemy2 is selected, configure and test its Alembic helpers rather than assuming generic autogeneration is correct.
- Do not run irreversible production migrations without explicit approval under the project's data-safety workflow.

Exact Alembic/GeoAlchemy2 versions, file layout, revision naming convention, CI schema-drift command, deployment platform job, timeout values, and backup mechanism remain implementation/provider decisions.

### Rationale

- Alembic aligns with SQLAlchemy 2 and supports both declarative operations and explicit PostgreSQL SQL.
- Versioned committed migrations make environments reproducible and changes reviewable.
- Manual review compensates for autogeneration limitations, especially around constraints and spatial objects.
- A single controlled migration job prevents races among replicas.
- Forward-safe expand/contract changes protect deployments where application versions temporarily overlap.
- Explicit recovery and backfill rules reflect Kirim's financial, audit, and workflow data sensitivity.

### Alternatives Considered

- **Versioned SQL with a separate or custom runner:** Not selected because it would give up useful SQLAlchemy metadata integration or require additional tooling while Alembic can still execute explicit SQL where appropriate.
- **Flyway or Liquibase:** Not selected because a separate migration ecosystem/runtime provides no demonstrated MVP benefit for this Python/SQLAlchemy application.
- **Provider-managed console changes:** Rejected because they are not an adequate reviewable, testable, repository-owned migration history.
- **Automatic model-to-database synchronization at application startup:** Rejected because it is unsafe for production data, cannot represent controlled data transformations reliably, and can race across replicas.
- **Always require reversible downgrades:** Not selected because many destructive/data transformations cannot be honestly reversed; backup/restore, application rollback compatibility, and corrective forward migrations are safer than fictional reversibility.

### Consequences

**Benefits**

- Auditable and reproducible schema history.
- Direct fit with the selected SQLAlchemy toolkit.
- Controlled deployment and rollback/recovery planning.
- Explicit treatment of PostGIS and data backfills.
- CI can validate migration topology and upgrade behavior.

**Costs and constraints**

- Every generated migration requires human review and database testing.
- Expand/contract changes can span multiple releases.
- A release-job mechanism and migration credentials must be configured.
- Realistic migration tests require PostgreSQL/PostGIS infrastructure.
- Operational recovery may require forward fixes or database restoration rather than a simple downgrade command.

### Revisit Triggers

Review this decision if Alembic cannot safely represent a required migration; the system becomes polyglot with independently managed schemas; organizational database governance mandates another tool; or migration scale requires dedicated online-schema-change capabilities. Any replacement must preserve the applied revision history and provide a tested transition plan.

### References

- Alembic documentation: https://alembic.sqlalchemy.org/
- Alembic autogeneration limitations/review guidance: https://alembic.sqlalchemy.org/en/latest/autogenerate.html
- Alembic offline SQL mode: https://alembic.sqlalchemy.org/en/latest/offline.html
- GeoAlchemy2 Alembic integration: https://geoalchemy-2.readthedocs.io/en/stable/alembic.html

### Affected Areas

- `docs/TECH_STACK.md`
- `docs/DATABASE_SCHEMA.md`
- Migration files and CI
- Deployment/release workflow and credentials
- PostgreSQL/PostGIS backup, recovery, and operational runbooks

---

## DEC-024 — 2026-10-18 — Use Small Explicitly Bounded SQLAlchemy Connection Pools Initially

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim's synchronous API and Worker processes need efficient connection reuse without exhausting the managed PostgreSQL service. SQLAlchemy creates a pool per process, so replicas, API processes, Worker processes, release jobs, and operational tools can multiply the effective connection demand. An external pooler can help at larger or highly elastic scale but adds configuration and compatibility concerns that are not yet justified for the invite-only MVP. Exact provider limits and deployment concurrency remain unselected.

### Decision

- Use SQLAlchemy's synchronous **`QueuePool`** initially for long-running API and Worker processes.
- Configure `pool_size`, `max_overflow`, and `pool_timeout` explicitly; do not rely blindly on SQLAlchemy defaults.
- Choose exact values only after selecting the managed PostgreSQL plan and deployment process/replica/Worker concurrency. Apply a documented connection budget:
  - include every API replica and process;
  - include every Worker replica/process;
  - include migrations, support/administrative access, monitoring, and provider-reserved needs;
  - budget for maximum configured overflow, not only steady-state pool size;
  - retain a safety reserve below the provider/database connection limit.
- Enable `pool_pre_ping` initially to detect stale/disconnected pooled connections. Set `pool_recycle` only when verified provider/network idle-lifetime behavior requires it.
- Acquire sessions/connections as late as practical and release them promptly. Never retain a checked-out connection or open transaction while waiting for an external provider or user interaction.
- Bound request, job, thread, and pool concurrency together. A larger pool is not a substitute for query/index optimization or short transactions.
- Create engines/pools within each operating-system process after process creation. Never share live pooled connections across process forks.
- Use a dedicated minimal pool or `NullPool` for one-off migration/release processes when appropriate, so they do not retain unnecessary connections.
- Monitor current/peak database connections, pool checkout wait, checkout duration, overflow use, timeouts, transaction duration, and connection errors. Alert thresholds are an observability decision.
- Do not introduce a self-managed PgBouncer initially.
- If the selected provider requires or strongly recommends an external pooler/proxy, verify pooling mode and compatibility with Psycopg 3, SQLAlchemy, prepared statements, session state, advisory locks, temporary objects, LISTEN/NOTIFY if later used, and Alembic. Avoid unintentionally stacking persistent pools; use `NullPool` at the application layer when that is the tested design.

Exact values and provider-specific parameters are deployment configuration and must be load-tested before production. This decision does not select the managed PostgreSQL provider or deployment topology.

### Rationale

- `QueuePool` provides sufficient connection reuse with no additional MVP service.
- Explicit bounded settings prevent each process from silently consuming SQLAlchemy's default pool plus overflow allocation.
- A global connection budget reflects the actual multiplicative effect of replicas/processes.
- Deferring exact values avoids inventing capacity before provider limits and measured concurrency are known.
- The strategy leaves a clear path to a provider/external pooler if deployment characteristics later require one.

### Alternatives Considered

- **Provider/external pooler such as PgBouncer immediately:** Not selected because the provider and workload are unknown, while pooling modes add feature and operational compatibility considerations.
- **Layer a persistent SQLAlchemy pool over an external persistent pool by default:** Rejected because it can create redundant queueing and obscure connection capacity without a tested need.
- **No connection reuse for long-running processes:** Rejected because repeatedly creating direct PostgreSQL connections adds avoidable latency and resource cost.
- **Use default SQLAlchemy pool settings:** Rejected because defaults multiply per process/replica and may exceed a managed plan unexpectedly.
- **Psycopg's separate pool under SQLAlchemy's pool:** Rejected initially because stacked application pools are unnecessary and make lifecycle/capacity behavior harder to reason about.

### Consequences

**Benefits**

- Simple connection reuse within the existing SQLAlchemy stack.
- Explicit protection against connection exhaustion.
- Capacity can be calculated and tested per deployment topology.
- No additional pooler service for the MVP.

**Costs and constraints**

- Every process still owns a separate pool, so autoscaling must remain connection-budget aware.
- Configuration cannot be finalized until hosting and database plans are chosen.
- Pool metrics and operational alerts must be implemented.
- Highly elastic scaling may eventually require an external/provider pooler.

### Revisit Triggers

Review this decision if the selected provider mandates a connection proxy; maximum replica/process requirements cannot fit safely within the database connection limit; pool waits/timeouts remain unacceptable after query and transaction tuning; connection setup cost becomes material; or serverless/highly elastic deployment is approved. Any external pooler adoption requires compatibility and failure-mode testing plus updated connection budgeting.

### References

- SQLAlchemy connection pooling: https://docs.sqlalchemy.org/en/20/core/pooling.html
- SQLAlchemy engine configuration: https://docs.sqlalchemy.org/en/20/core/engines.html
- PostgreSQL connection settings: https://www.postgresql.org/docs/current/runtime-config-connection.html

### Affected Areas

- `docs/TECH_STACK.md`
- API and Worker engine/session factories
- Deployment process/replica/concurrency configuration
- Managed PostgreSQL sizing
- Migrations and administrative tooling
- Load testing, metrics, alerts, and runbooks

---

## DEC-025 — 2026-10-18 — Use Job-Class-Specific Bounded Retries, Leases, and Visible Permanent Failure

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim's PostgreSQL-backed Worker will execute notifications, evidence processing, payment reconciliation, settlement, refunds, reimbursements, and other consequential tasks. Network failures, provider rate limits, worker crashes, and uncertain external outcomes are normal distributed-system conditions. One retry policy cannot safely cover every job class; unlimited retries can create storms or repeated effects, while no retries make ordinary transient failures operationally expensive. The behavior must remain valid regardless of which PostgreSQL job library passes the implementation spike.

### Decision

**Delivery and handler contract**

- Use **at-least-once delivery**. Never claim exactly-once execution.
- Every handler must be idempotent and must define its idempotency/deduplication scope, supported payload version, retry classification, attempt/deadline policy, and observability severity.
- Consequential external operations use stable Kirim idempotency identifiers and provider idempotency mechanisms where supported, plus reconciliation where outcomes can be uncertain.
- Version durable job payloads and reject/route unsupported versions safely rather than guessing their meaning.

**Failure classification**

Each job handler classifies failures into documented categories:

- **Transient:** temporary network/database availability, bounded timeout, provider `5xx`, or another explicitly recoverable condition.
- **Rate limited:** retry according to a validated provider delay such as `Retry-After`, bounded by Kirim's policy.
- **Permanent:** invalid/unsupported payload, non-recoverable provider rejection, revoked/no-longer-applicable operation, impossible domain transition, or another documented non-retryable condition.
- **Unknown external outcome:** the provider might have accepted a consequential operation but Kirim lacks a definitive response. Enter provider-status reconciliation; do not blindly repeat the effect.

Do not treat every exception or HTTP `4xx`/`5xx` uniformly. Each adapter maps documented provider behavior conservatively, and unexpected errors receive a bounded policy plus visibility.

**Retries and backoff**

- Use bounded exponential backoff with jitter for ordinary transient failures to avoid synchronized retry storms.
- Configure maximum attempts, initial delay, multiplier, maximum delay, age/deadline, and retryable classifications by job class.
- Use **five total attempts as an initial ordinary-job default**, subject to implementation/load/provider testing; this is not the policy for every job.
- Financial reconciliation and scheduled obligations may have longer-lived schedules but still require escalation and operator visibility rather than invisible infinite retries.
- Successful completion, permanent failure, cancellation, supersession, and retry scheduling must be explicit persisted states/events as supported by the selected implementation.

**Timeouts**

- Apply bounded database lock and statement timeouts, provider connect/read/request timeouts, and an expected overall job deadline appropriate to the job class.
- Since synchronous Python threads cannot be safely terminated arbitrarily, handlers must use bounded underlying operations and cooperative deadlines. Do not mark work as safely stopped while an untracked thread can still create effects.
- Long-running or uninterruptible processing requires an explicit process-isolation or provider-job design review rather than relying on unsafe thread cancellation.

**Claiming, leases, and crash recovery**

- Claim jobs in short transactions and commit the claim before external processing; never keep a row lock or transaction open for the full execution.
- Associate each claim with an unguessable or collision-resistant worker/lease token and lease expiry.
- Renew leases only for job classes intentionally designed to exceed the base lease duration.
- Verify current lease ownership/fencing before recording completion or retry state where the implementation permits it.
- Recover expired claims after worker failure while assuming the previous attempt may have partially executed; recovery therefore relies on handler idempotency and reconciliation, not only the lease.
- Keep the durable table/state authoritative. Notifications such as PostgreSQL `NOTIFY` may wake workers but are never the sole job payload or delivery guarantee.

**Permanent failure and replay**

- After policy exhaustion or a permanent classification, retain the job in a visible terminal/dead-letter state. Never silently discard it.
- Preserve attempt timestamps, sanitized classification/error details, correlation identifiers, and result references according to retention policy. Do not persist secrets, tokens, unnecessary personal data, or uncontrolled full provider payloads in errors.
- Alert/escalate according to severity and outstanding obligation. A failed promotional notification and an incomplete refund do not receive the same response.
- Authorized manual replay occurs only after the cause and current domain/provider state are reviewed. Replay creates a traceable new attempt/job while preserving original history and is audited for consequential Admin actions.

**Financial safeguards**

- Settlement, payment, refund, reimbursement, and financial-reconciliation jobs use stable idempotency identifiers and query/reconcile authoritative provider state after uncertain responses.
- Do not record a successful external financial/ledger transition without the required authoritative confirmation and local transactional checks.
- Incomplete financial obligations remain visible and high-severity after automated retry exhaustion, with controlled repair paths that preserve ledger and audit history.

Exact values beyond the ordinary five-attempt starting point—including delays, lease durations, timeouts, job-specific attempts, alert thresholds, and retention—must be specified per job class after provider and deployment testing. The eventual queue library must demonstrate these required semantics or document the bounded adapter/custom behavior that supplies them.

### Rationale

- Job-specific policies reflect different consequence, provider, and timing profiles.
- At-least-once plus idempotency is honest about crashes and network uncertainty.
- Bounded backoff with jitter recovers transient failures without unbounded storms.
- Short claims plus leases permit crash recovery without holding long transactions.
- Visible dead-letter state and audited replay protect supportability and financial obligations.
- Reconciliation prevents a timeout from becoming an unsafe duplicate payment attempt.

### Alternatives Considered

- **One uniform retry policy:** Rejected because job classes have materially different risks and deadlines.
- **Infinite automatic retries:** Rejected because poison jobs and permanent provider failures could consume resources or repeat effects indefinitely without escalation.
- **No automatic retries:** Rejected because normal transient failures would create avoidable manual work and reduced reliability.
- **Exactly-once execution claim:** Rejected because database commits, process crashes, and external provider effects cannot be made universally atomic.
- **Hold a database lock/transaction during execution:** Rejected because long external work would cause contention, timeout, and recovery problems.
- **Delete failed jobs:** Rejected because it removes evidence required for support, audit, repair, and incident analysis.

### Consequences

**Benefits**

- Predictable, inspectable failure behavior.
- Safe recovery from common transient faults and worker crashes.
- Stronger duplicate-effect and uncertain-financial-outcome protection.
- Clear operational distinction between low-impact and critical jobs.
- Queue-library choice remains replaceable behind required semantics.

**Costs and constraints**

- Every job class needs explicit policy and tests.
- Lease, idempotency, reconciliation, metrics, and replay handling add implementation work.
- Operations need dead-letter views, alerts, and repair runbooks.
- Five attempts is only a starting default and cannot substitute for provider-specific analysis.

### Revisit Triggers

Review this decision if the selected queue implementation cannot supply safe leases/recovery; measured failure patterns require adjusted policies; a provider's API changes idempotency or reconciliation guarantees; jobs become long-running/CPU-heavy; or the system adopts infrastructure with a different delivery model. Changes must preserve auditability and prevent duplicate consequential effects.

### References

- PostgreSQL `SKIP LOCKED` queue-like access: https://www.postgresql.org/docs/current/sql-select.html
- PostgreSQL `NOTIFY`: https://www.postgresql.org/docs/current/sql-notify.html
- PostgreSQL `LISTEN`: https://www.postgresql.org/docs/current/sql-listen.html

### Affected Areas

- `docs/TECH_STACK.md`
- Job payload contracts and persistence schema
- Worker handlers and provider adapters
- Payments, ledger, settlement, refunds, and reimbursements
- Admin/support repair and replay tooling
- Metrics, alerts, audit, retention, and incident runbooks

---

## DEC-026 — 2026-10-18 — Proxy Evidence Files Through the Backend for the MVP

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim requires receipt, price/location, arrival, handover, dispute, safety-report, and other evidence that may contain personal, financial, location, or sensitive contextual data. Original evidence must be immutable, access-controlled, auditable, retained/deleted according to policy, and reviewable by assigned Support only. DEC-005 states that production object storage is not directly accessible from public clients. Direct signed uploads can reduce application bandwidth but introduce a separate authorization/completion lifecycle and would revise that boundary. MVP scale is limited and expected evidence files are bounded images/documents rather than large media.

### Decision

**Transfer architecture**

- For the MVP, upload and download evidence through authenticated, authorized Kirim backend endpoints:
  - client to API to private object storage for uploads;
  - private object storage to API to authorized client for downloads.
- Stream bytes with bounded buffers; never load an entire permitted file into application memory by default.
- Keep storage containers/buckets private. Do not expose public object URLs, storage credentials, or storage object keys as authorization capabilities.
- Do not use direct client signed-storage upload/download URLs initially. Adopting them later requires an explicit amendment to the DEC-005 public-client boundary and a dedicated threat/abuse review.

**Upload lifecycle**

- Require authentication, account eligibility, resource relationship/workflow authorization, allowed evidence purpose, and count/size policy before accepting an upload.
- Use an idempotent upload operation and a random server-controlled object key unrelated to user filenames or predictable identifiers.
- Create a pending evidence metadata record, stream the original to a private quarantine/pending location, and finalize availability only after required processing succeeds.
- Define explicit states for pending/uploaded/processing/accepted/rejected/failed/deleted or their approved equivalent. Domain workflows must not treat unvalidated evidence as accepted.
- Use durable jobs for validation/derivative generation/cleanup. Detect and clean abandoned metadata/objects and orphans safely without deleting held or referenced evidence.
- Because database and object storage cannot share one atomic transaction, make every lifecycle transition idempotent and reconciliable.

**Validation and safe serving**

- Enforce purpose-specific maximum file count and byte size before and while streaming.
- Allowlist supported formats. Do not trust filename extensions or client `Content-Type`; inspect signatures/magic bytes and decode supported image/document structures.
- Reject malformed, truncated, unsupported, encrypted where unsupported, decompression-bomb, or otherwise unsafe content. Verify malware-scanning capabilities and limits before implementation; scanning does not replace format validation.
- Never execute uploaded content or serve untrusted active content from an application origin in a way that can execute in the browser.
- Use safe response content types, `Content-Disposition`, content-sniffing protections, authorization, and bounded streaming for downloads.
- Exact formats, dimensions, counts, and size limits require Android-device and product-flow testing. HEIC and conversion support are not assumed.

**Integrity and metadata**

- Preserve every accepted original as immutable. Corrections/additions create linked records/objects and never overwrite the original.
- Record at minimum a random object key, cryptographic hash, byte size, detected media type, evidence purpose, linked domain record, uploader, server receipt time, processing/version status, retention category, legal/dispute hold state, and derivative references.
- Verify hashes when the storage/processing flow requires integrity checks, and record storage-provider version/integrity metadata where useful without treating provider metadata as the sole application audit record.
- Preserve an original privately when evidentiary metadata may matter, while generating separate sanitized display derivatives where required for safe/minimized viewing.
- Do not treat EXIF time/location as authoritative alone. Store server time, approved app-collected location, workflow context, and provenance separately and display uncertainty appropriately.
- Do not leak sensitive original metadata through display derivatives unless an authorized case-specific use requires it.

**Authorization, audit, and lifecycle**

- Reauthorize every upload and download against current account, relationship, case assignment, workflow state, and evidence purpose. Possession of an identifier is never sufficient.
- Record attributable audit events for Support/Admin evidence access and consequential evidence decisions without logging file contents or unnecessary personal data.
- Encrypt in transit and use provider-supported encryption at rest; exact key-management requirements follow provider/security selection.
- Apply approved retention categories, dispute/legal/fraud/account holds, user deletion/export rules, and provider deletion verification through durable lifecycle jobs.
- Backups, replicas, derivatives, and caches must be included in retention/deletion design, with documented limitations and restoration behavior.

### Rationale

- Backend proxying preserves the already approved no-direct-public-storage boundary.
- It centralizes authorization and access auditing for sensitive evidence.
- MVP scale and bounded evidence sizes do not yet justify signed-upload lifecycle complexity.
- Streaming controls memory usage while retaining a simple trust boundary.
- Quarantine/processing states prevent unvalidated objects from becoming authoritative evidence.
- Immutable originals plus sanitized derivatives balance evidentiary integrity and safe/minimized display.

### Alternatives Considered

- **Short-lived signed direct storage URLs:** Deferred because they would change DEC-005's public-client boundary and add upload-intent, restriction, completion, orphan, and audit complexity. Reconsider if measured bandwidth/file reliability requires it.
- **Store binary evidence in PostgreSQL:** Rejected because it would increase primary database, backup, and restore burden and conflicts with the approved object-storage component.
- **Public object URLs:** Rejected because evidence is private and relationship/case authorization can change over time.
- **Trust filename or client media type:** Rejected because both are attacker-controlled and insufficient for safe processing/serving.
- **Modify/replace originals during sanitization:** Rejected because requirements call for immutable original evidence and correction through linked records.

### Consequences

**Benefits**

- One backend-controlled authorization and audit path.
- Private storage remains isolated from public clients.
- Strong evidence provenance and immutable-original handling.
- Simpler MVP client and storage lifecycle.

**Costs and constraints**

- API instances carry upload/download bandwidth and connection load.
- Streaming, cancellation, size enforcement, quarantine, orphan cleanup, and reconciliation require careful implementation and tests.
- Large files or increased volume may eventually require signed direct transfer.
- Processing and retention add Worker load and storage metadata.

### Revisit Triggers

Review this decision if measured bandwidth/cost, upload reliability, API connection duration, evidence volume/size, or hosting limits make proxying unacceptable. A signed-transfer design must retain backend-created upload intent, strict object/size/type/expiry constraints, private storage, authenticated completion, post-upload validation, orphan cleanup, download authorization/audit, and revocation/retention controls, and must explicitly amend DEC-005.

### Affected Areas

- `docs/TECH_STACK.md`
- `docs/API_CONTRACTS.md`
- `docs/DATABASE_SCHEMA.md`
- Evidence and Files module
- Flutter upload/download UX
- Admin/Support evidence review
- Object storage, Worker processing, retention/deletion, audit, and observability

---

## DEC-027 — 2026-10-18 — Use Durable REST Messaging with Push Hints and Foreground Cursor Refresh

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim requires transaction-scoped communication for negotiation, substitutions, shopping, handover coordination, failed-delivery procedure, disputes, and support evidence. Messages must remain available across disconnections and app restarts and may become dispute evidence. Android background execution and network connectivity are unreliable, while permanent WebSocket/SSE connections would still require durable persistence, push fallback, and catch-up. The invite-only MVP does not require typing indicators, presence, or sub-second real-time guarantees.

### Decision

**Authoritative delivery model**

- Use REST-style message creation and cursor-based retrieval backed by authoritative PostgreSQL persistence.
- Use Android push notifications only as best-effort hints that prompt an authenticated client to synchronize. Push receipt is never authoritative proof of message delivery, reading, or workflow completion.
- While an eligible conversation screen is active, use bounded adaptive foreground polling/cursor refresh. Reduce or stop aggressive polling when the conversation is not active or the app is backgrounded.
- Do not implement WebSockets, Server-Sent Events, or a third-party managed chat service initially.

**Message creation and ordering**

- Authorize every send against the authenticated account, transaction/conversation relationship, account status, and workflow/policy rules.
- Require a client-generated idempotency identifier scoped to the sender/conversation so retries do not create duplicate messages.
- Persist the message before creating notification work, and create the transactional outbox/job record atomically with the message transaction.
- Assign an authoritative server message identifier, server receipt timestamp, and deterministic per-conversation ordering value with a unique tie-breaker. Client timestamps are presentation metadata only and do not establish order.
- Preserve original accepted message content as immutable evidence. Corrections, moderation/system notices, redactions required by approved policy, or follow-up context use linked append-only records/events rather than silent overwrite.
- Exact edit/delete/redaction product behavior is not inferred by this decision and requires explicit requirements if introduced.

**Synchronization**

- Retrieve messages using opaque cursor pagination consistent with DEC-019.
- On opening, resuming, push handling, reconnecting, or detecting a cursor gap, fetch authoritative messages after the last acknowledged cursor and reconcile local state.
- Design clients to tolerate duplicate push events, duplicate HTTP responses/retries, out-of-order notification arrival, and missing notifications.
- Exact polling intervals, backoff, page limits, local cache duration, and acknowledgement/read-state behavior require mobile/network/product testing.

**Push privacy and security**

- Keep push payloads minimal and avoid sensitive message text on lock screens by default. Include only opaque routing/synchronization information needed to open the app safely.
- Opening a push does not grant access. The client must authenticate, and the API must reauthorize current conversation access before returning content.
- Token invalidation and push-delivery failure must not alter message persistence or make content inaccessible through normal synchronization.

**Privacy, evidence, and support access**

- Limit conversation access to authorized transaction participants and explicitly assigned Support users under case scope. Audit Support/Admin message access where required by the approved sensitive-evidence policy.
- Retain messages according to the approved 90-day post-closure category unless dispute, fraud, accounting/legal, deletion, or another approved hold/exception applies.
- Do not claim end-to-end encryption because Kirim must support authorized server-side dispute/evidence review. Use TLS in transit and provider/database encryption controls at rest.
- Do not expose personal phone numbers or require off-platform communication.
- Route permitted message attachments through the Evidence and Files architecture in DEC-026; do not create an independent public chat-upload path.

**Future real-time upgrade**

- Preserve REST persistence, cursor catch-up, push fallback, authorization, and audit as the authoritative design if WebSockets or SSE are later added.
- A real-time transport is an optional acceleration channel only; it must tolerate reconnect/replay and cannot become the sole delivery path.

### Rationale

- Durable REST persistence works across mobile disconnects and app lifecycle transitions.
- Push provides background awareness without becoming a source of truth.
- Foreground polling is operationally simple and adequate for the small pilot.
- Avoiding persistent connections removes connection authentication, fan-out, backpressure, and cross-replica infrastructure before measurements justify them.
- Cursor catch-up and immutable server ordering preserve evidence integrity and deterministic synchronization.

### Alternatives Considered

- **WebSockets plus push fallback:** Deferred because they add persistent-connection lifecycle and cross-replica fan-out while REST, push, and catch-up remain necessary anyway.
- **Server-Sent Events plus REST sending:** Deferred because they retain persistent mobile-connection/reconnection complexity without a demonstrated MVP benefit.
- **Managed third-party chat:** Not selected because sensitive message retention, authorization, dispute evidence, export/deletion, provider processing, and audit requirements would add dependency and governance complexity.
- **Push payload as the message:** Rejected because push delivery can be delayed, duplicated, omitted, or exposed on lock screens and is not authoritative persistence.
- **Client timestamps for canonical ordering:** Rejected because device clocks are untrusted and inconsistent.

### Consequences

**Benefits**

- Simple, inspectable, durable MVP communication.
- Offline/background-safe catch-up.
- No additional real-time broker or connection infrastructure.
- Strong message evidence, authorization, and retention boundaries.

**Costs and constraints**

- Foreground message arrival has a bounded polling delay.
- Polling interval and query/index design require testing to avoid waste.
- Push infrastructure is still needed for background awareness.
- WebSocket-like presence/typing behavior is unavailable initially.

### Revisit Triggers

Review this decision if measured foreground latency materially harms task completion/safety; polling load or battery/network cost becomes unacceptable; message volume exceeds the selected approach; or approved features require real-time presence/typing/streaming. Any upgrade must retain durable REST/cursor synchronization and push fallback and requires authentication, authorization, fan-out, capacity, reconnect, and abuse testing.

### Affected Areas

- `docs/TECH_STACK.md`
- `docs/API_CONTRACTS.md`
- Communication and Notifications module
- Flutter local synchronization and notification handling
- PostgreSQL message schema/indexes and transactional outbox
- Support evidence access, retention, audit, metrics, and testing

---

## DEC-028 — 2026-10-18 — Use WGS 84 with Geography-Based Metric Distance and Geometry Geofences

- **Status:** Accepted
- **Decision owner:** Product Owner

### Context

Kirim needs consistent spatial semantics for the versioned pilot geofence, 1 km route-matching corridor, journey routes, shop/delivery points, and location-linked evidence. External mobile/map coordinates use longitude/latitude, while distance thresholds are specified in metres. Treating degrees as metres is incorrect; storing a projected local CRS as the primary external representation would add conversion and coordinate-mixing risk. PostGIS supports geodetic `geography` distance predicates in metres and `geometry` polygon validity/containment operations.

### Decision

**Canonical coordinates**

- Use **WGS 84 / EPSG:4326** as Kirim's canonical stored and external coordinate reference system.
- API and GeoJSON coordinate order is always `[longitude, latitude]`. Never silently interpret `[latitude, longitude]`.
- Validate numeric finiteness and valid longitude/latitude ranges at every external boundary. Apply additional purpose/pilot sanity bounds where approved.
- Do not use elevation/Z values in MVP eligibility or distance decisions unless a later requirement explicitly introduces them.

**PostGIS types**

- Use `geography(Point, 4326)` for canonical location points and location-linked evidence where metre distance is relevant.
- Use `geography(LineString, 4326)` for canonical journey routes used in proximity matching.
- Use valid `geometry(MultiPolygon, 4326)` for versioned pilot geofence boundaries. Normalize approved Polygon inputs to MultiPolygon where the schema requires it.
- Use GiST indexes on spatial columns used by proximity/containment queries, with indexes and query plans verified against representative data.
- Do not duplicate every spatial value in both geography and projected geometry. Temporary query transformations or bounded non-authoritative display derivatives are allowed when justified.

**Route corridor and distance**

- Implement the approved 1 km route corridor using indexed `ST_DWithin(route_geography, point_geography, 1000)` or its semantically equivalent argument order.
- Distance thresholds are in metres and boundary-inclusive: a measured distance equal to 1,000 metres qualifies.
- Use spheroid-aware geography behavior by default. A faster spherical approximation requires measured justification, documented acceptable error, and tests before it can affect eligibility.
- Use `ST_Distance` only when actual distance output/ranking is required; use `ST_DWithin` for threshold filtering so PostGIS can use spatial-index prefiltering.
- Add deterministic non-distance tie-breakers when ranked results have equal/effectively equal distances.
- Keep the route plus versioned threshold authoritative; do not persist a route-buffer polygon unless a demonstrated query, audit, or display requirement justifies it.

**Pilot geofence**

- Store every approved pilot boundary as an immutable, versioned MultiPolygon with status/effective interval, provenance, approval metadata, and audit history.
- Validate geometry type, SRID, non-empty content, and `ST_IsValid` before activation. Do not silently repair an invalid approved boundary; reject it and require an explicit corrected version.
- Use boundary-inclusive `ST_Covers(active_geofence_geometry, point_geometry)` for point eligibility where the product rule requires geofence membership.
- Persist/reference the geofence version and relevant rule version used for consequential eligibility/matching evidence so later boundary updates do not rewrite historical decisions.
- Define deterministic behavior for overlapping effective versions and prohibit ambiguous simultaneous active versions for the same scope.

**Location evidence and uncertainty**

- Store the point separately from server receipt time, device-recorded time, reported horizontal accuracy, source/purpose, related account/transaction/workflow event, applicable geofence/rule version, and provenance/integrity metadata.
- Treat device GPS, provider routes, and EXIF coordinates as evidence with uncertainty, not guaranteed truth.
- Exact acceptable accuracy, freshness, sampling, spoof/anomaly handling, and missing-location behavior require Android field testing and explicit product approval before affecting financial or fault outcomes.
- Preserve original observations; corrected/derived spatial interpretations use linked records rather than overwriting evidence.

**Testing**

- Test exact-threshold and near-threshold route distances; geofence interior, exterior, boundary, and holes; invalid/self-intersecting polygons; empty/malformed routes; coordinate-order mistakes; antimeridian/pole behavior as defensive validation even though outside the pilot; reported accuracy/freshness boundaries; and spatial index/query-plan use.
- Use real PostgreSQL/PostGIS integration tests for authoritative spatial behavior rather than substituting Python geometry calculations.

A projected CRS such as the local UTM zone may be used transiently for a specialized calculation after validation, but it is not canonical persisted state under this decision.

### Rationale

- Geography predicates express approved thresholds directly in metres over WGS 84 coordinates.
- EPSG:4326 aligns with mobile location APIs, map providers, and GeoJSON.
- Geometry is well suited to geofence validity, versioning, and boundary-inclusive containment.
- `ST_DWithin` with GiST indexing avoids loading candidates and calculating distances in Python.
- Immutable geofence/rule versions preserve historical auditability.
- Explicit accuracy/provenance prevents false precision in location evidence.

### Alternatives Considered

- **Projected local CRS as canonical:** Not selected because external boundaries use longitude/latitude and constant transformations increase coordinate-mixing risk without an MVP need.
- **Store both WGS 84 and projected copies everywhere:** Rejected because duplicate authoritative representations can drift and require unnecessary indexes/synchronization.
- **Plain latitude/longitude numeric columns with Python/manual distance:** Rejected because degree arithmetic is not metre distance, bypasses PostGIS spatial indexing, and risks inconsistent implementations.
- **Persist pre-buffered route corridors as authority:** Not selected because threshold/version changes and derived-polygon integrity would add unnecessary state; `ST_DWithin` expresses the rule directly.
- **Use `ST_Contains` for geofence eligibility:** Not selected because points exactly on a polygon boundary are excluded; the approved semantic is boundary-inclusive through `ST_Covers`.

### Consequences

**Benefits**

- One external/canonical CRS and explicit coordinate order.
- Correct metre-based distance semantics.
- Indexed route matching and clear geofence boundaries.
- Versioned, auditable spatial decisions.
- Realistic handling of device-location uncertainty.

**Costs and constraints**

- Contributors must understand geometry versus geography semantics.
- Geometry/geography conversion must be explicit in mixed containment queries.
- Spatial migrations, indexes, and query plans require PostGIS-aware review/testing.
- Product decisions for GPS accuracy/freshness and provider-route quality remain outstanding.

### Revisit Triggers

Review this decision if Kirim expands far beyond the pilot and geography performance becomes inadequate; advanced local geometric operations require a stable projected representation; route-provider data cannot be normalized safely; or measured accuracy requirements demand a different model. Any canonical CRS/type change requires data migration, API compatibility, historical-decision preservation, and spatial regression tests.

### References

- PostGIS geography data management: https://postgis.net/docs/using_postgis_dbmanagement.html
- PostGIS `ST_DWithin`: https://postgis.net/docs/ST_DWithin.html
- PostGIS `ST_Covers`: https://postgis.net/docs/ST_Covers.html
- PostGIS `ST_IsValid`: https://postgis.net/docs/ST_IsValid.html

### Affected Areas

- `docs/TECH_STACK.md`
- `docs/DATABASE_SCHEMA.md`
- `docs/API_CONTRACTS.md`
- Journeys and Matching module
- Evidence and Files module
- PostgreSQL/PostGIS migrations and indexes
- Flutter/map-provider coordinate handling
- Spatial integration, boundary, and query-plan tests
