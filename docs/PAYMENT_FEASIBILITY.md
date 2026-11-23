# Payment Provider Feasibility Action Plan

> Status: Draft execution plan — this document does not approve Stripe, authorize live payments, or provide legal/accounting advice.
>
> Leading candidate: Stripe Connect for a Malaysia-only Kirim pilot (`DECISIONS.md`, DEC-057).

## 1. Objective

Determine, with written provider evidence, sandbox results, cost analysis, and Malaysian professional review, whether Stripe Connect can support Kirim's approved MVP payment behavior safely and lawfully.

The decision must end in one of three outcomes:

1. **Approve:** Stripe supports every mandatory gate and the Product Owner accepts the documented costs and residual risks.
2. **Conditionally approve:** Only named non-production work may continue while time-bounded conditions are completed. Stage B remains blocked.
3. **Reject:** A mandatory gate fails, so Kirim evaluates another provider or changes an approved requirement through the formal decision process.

Silence, marketing material, a successful happy-path sandbox payment, or the ability to configure an API call is not provider approval.

The provider-neutral Product Owner rules in `docs/PAYMENT_DOMAIN_MODEL.md` Section 20 are accepted under DEC-062. Provider, legal, accounting, and technical feasibility gates remain open.

## 2. Kirim Payment Model to Validate

The evaluation must use the actual Kirim workflow, not a generic marketplace example:

1. The Customer and Shopper agree on a Shopper fee.
2. Customer final confirmation locks the fee.
3. The Customer pays an initial amount equal to the Shopper fee, plus separately disclosed processing costs where applicable.
4. The provider confirms the required initial funds are secured before price collection begins.
5. The Shopper records actual item prices.
6. Kirim issues an itemized outstanding-balance invoice.
7. The Customer pays the outstanding balance.
8. The provider confirms all required Customer funds are secured before the Shopper purchases.
9. The Shopper pays the merchant using personal funds.
10. Kirim reconciles receipt evidence and delivery.
11. Normal settlement becomes eligible 24 hours after confirmed delivery only if no dispute is open.
12. Settlement reimburses the verified item cost and pays the locked Shopper fee, subject to approved cancellation or dispute outcomes.
13. Refunds, reversals, failed transfers/payouts, disputes, duplicate events, and uncertain responses are reconciled without duplicate financial effects.

Kirim earns no platform fee during MVP. Do not describe the arrangement publicly as **escrow**, a wallet, deposit-taking, or provider-guaranteed protection unless the provider contract and Malaysian professional advice explicitly permit that terminology.

## 3. Roles and Responsibilities

| Role | Responsibilities | Cannot delegate |
|---|---|---|
| Product Owner | Own provider account and billing, provide accurate business information, approve costs/terms/risks, engage professional advisers, make final provider decision | Acceptance of contracts, legal/business representations, final approval |
| Engineering Lead | Prepare technical questions, implement disposable sandbox/fake-provider spikes, test failures, document evidence, recommend approve/reject | Cannot provide legal/accounting advice or accept provider terms for the owner |
| Malaysian legal adviser | Review licensing, custody, marketplace roles, consumer terms, disputes, privacy, and regulatory responsibilities | Written legal conclusions and limitations |
| Malaysian accountant/tax adviser | Review money flows, ledgers, reconciliation, records, invoicing, taxes, and reporting | Written accounting/tax conclusions and limitations |
| Stripe | Confirm product/account eligibility and contractual capabilities in writing | Provider-specific capability and contractual interpretation |

## 4. Action 1 — Create and Control a Stripe Malaysia Test Account

### 4.1 Before account creation

The Product Owner prepares:

- Legal/business name and Malaysian registration details, if an entity exists.
- Operating address and authorized representative details.
- A dedicated owner-controlled business email address.
- A dedicated password-manager entry and recovery procedure.
- A short accurate product description that does not call Kirim an escrow service.
- Expected Stage B scope: Kuala Lumpur corridor, Malaysia-only users, at most 50 invited accounts, RM200 item budget, no Kirim platform fee.
- Expected low pilot transaction volume and the separate RM300/month operating-stack target. Payment transaction fees are tracked separately.

If no suitable legal entity or business bank account exists, record that as a blocker rather than entering inaccurate information.

### 4.2 Account creation controls

The Product Owner should:

1. Create the account through the official Stripe Malaysia site.
2. Select Malaysia as the business country only if factually correct.
3. Enable the strongest available owner MFA, preferably a passkey/security key or authenticator rather than SMS alone.
4. Store recovery material in the owner's password manager; do not place it in the repository or chat.
5. Keep the account in test/sandbox mode. Do not activate live payments during feasibility work.
6. Do not enter synthetic identity or business information into a live onboarding process.
7. Invite individual team members using least-privilege roles instead of sharing the owner login.
8. Restrict access promptly when a contributor no longer needs it.
9. Record the selected Stripe account country, pricing model, Connect availability shown in the dashboard, and any onboarding limitations.

### 4.3 Developer/security setup

When the engineering spike begins:

- Use test keys only.
- Put local keys in ignored environment files and CI test keys in the approved secret store.
- Never commit, screenshot, paste into documentation, or send keys through ordinary chat/email.
- Use restricted keys where Stripe supports the required test operation.
- Register separate test webhook destinations for local/CI/staging use.
- Verify webhook signatures; never trust source IP or payload content alone.
- Rotate any key exposed accidentally and record the incident.
- Do not use real Customer/Shopper personal data, cards, bank accounts, receipts, or addresses in test mode.

### 4.4 Evidence to retain

Store no secrets, but record:

- Account owner and recovery owner.
- Account country and environment.
- Connect products/account types available for evaluation.
- Test-mode team roles.
- Pricing/document versions reviewed.
- Provider ticket identifiers.
- Date of each capability check.
- Known blockers or prerequisites.

### 4.5 Completion criteria

This action is complete when:

- The Product Owner controls a secured Malaysia test account.
- Test mode and team roles work.
- No shared credentials or repository secrets exist.
- The account exposes enough Connect/test capability to run the approved spike, or Stripe has documented why it cannot.

## 5. Action 2 — Contact Stripe About the Exact Connect Model

### 5.1 Contact method

Use an authenticated Stripe Support ticket and, if offered, Connect sales/solutions contact. Request written responses linked to the Malaysia account. Record the ticket/case number and the date/version of every referenced document.

Do not rely solely on community answers, generic sales pages, or verbal calls. If a call occurs, send a written summary afterward and ask Stripe to confirm or correct it.

### 5.2 Information to give Stripe

Provide a concise, accurate summary:

- Malaysia-only platform and participants for MVP.
- Invite-only pilot of at most 50 accounts.
- One Customer and one Shopper per transaction.
- Maximum requested-item budget RM200.
- Shopper uses personal funds at the merchant.
- Two Customer payment stages: initial Shopper fee, then itemized balance.
- No Kirim platform fee or revenue during MVP.
- Shopper receives verified item-cost reimbursement plus the agreed Shopper fee.
- Normal transfer/payout eligibility starts 24 hours after confirmed delivery if no dispute is open.
- Kirim needs refunds, partial allocations, dispute blocking, reconciliation, and manual support outcomes.
- Kirim does not intend to hold funds outside a licensed provider or market the service as escrow without approval.

### 5.3 Mandatory questions for Stripe

#### A. Malaysia eligibility and account model

1. Is this exact marketplace/personal-shopper use case eligible for Stripe Connect in Malaysia?
2. Which connected-account type is appropriate: Standard, Express, Custom, or another supported configuration?
3. Who contracts with the Customer and Shopper for payment services?
4. Who performs Shopper onboarding, identity/KYC, sanctions screening, bank-account verification, and ongoing review?
5. Which party bears chargeback, fraud, refund, negative-balance, connected-account, and payout loss liability?
6. Does the proposed model require a Stripe product, pricing model, or approval currently in preview or unavailable to Malaysian platforms?
7. Are all Customers, Shoppers, connected accounts, bank accounts, charges, and payouts required to remain Malaysian for this model?

#### B. Customer funding flow

8. Can Kirim create the initial Customer payment equal to the agreed Shopper fee and a later separate itemized-balance payment for the same Kirim transaction?
9. Which Stripe objects and charge model should represent those payments?
10. Can Kirim prevent any transfer to the Shopper until its server-side conditions are met?
11. What does Stripe consider funds to be before transfer: platform balance, connected-account balance, authorized funds, captured funds, or another state?
12. Which wording may Kirim use to tell users that payment is secured without incorrectly claiming escrow, guaranteed funds, or legal custody?
13. Can payment-processing costs be separately disclosed/charged to the Customer under Stripe rules and Malaysian law, and which methods have surcharge restrictions?
14. Do card and FPX or other Malaysian methods behave differently for delayed settlement, refunds, disputes, confirmation, or asynchronous success?

#### C. Authorization, capture, availability, and timing

15. Should Kirim authorize then capture, charge immediately, or use another flow for each payment stage?
16. What are the authorization/capture expiry windows for every intended payment method?
17. How long before captured funds become available for transfer?
18. Can Kirim keep available platform funds untransferred for the transaction duration plus the 24-hour dispute window?
19. Do Stripe fund-segregation, reserves, risk holds, first-payout delays, or maximum holding periods affect this flow?
20. Clarify the distinction between delaying a **transfer** to a connected account and setting a connected account to **manual payout**. Which control is needed for Kirim?
21. Can Stripe or the connected account initiate an automatic payout before Kirim's conditions if the wrong configuration is used?

#### D. Reimbursement, earnings, and settlement

22. Can one settlement transfer include the verified item reimbursement and Shopper fee while Kirim records them as separate ledger components?
23. Can Kirim make partial transfers after cancellation/dispute outcomes?
24. Can a timely dispute atomically prevent a transfer that has not yet been created?
25. What remedies exist if a transfer is created just as a dispute arrives?
26. Can a transfer be reversed, and under what balance, timing, consent, and liability constraints?
27. What happens when the connected account has already paid funds to its bank?
28. How are failed, returned, delayed, or rejected payouts reported and retried?

#### E. Cancellation, refund, and dispute behavior

29. Can Kirim issue full and partial Customer refunds for both payment stages?
30. Are original processing fees returned? If not, may Kirim disclose/deduct them under provider rules and Malaysian law?
31. How should refunds be handled before and after a transfer?
32. How are Stripe payment disputes different from Kirim's internal 24-hour delivery dispute?
33. Can a Stripe chargeback occur after Kirim has reimbursed/paid the Shopper, and who bears that exposure?
34. How are negative balances recovered, limited, or reserved?
35. Does Stripe support evidence submission and dispute deadlines for the intended payment methods?

#### F. Idempotency, webhooks, and reconciliation

36. Which create/confirm/capture/refund/transfer/reversal operations support idempotency keys and for how long?
37. Which webhook events are required for payment, refund, dispute, transfer, payout, account, capability, and balance changes?
38. Are webhook deliveries duplicated, delayed, retried, reordered, or delivered after API timeouts? What are the retry/retention limits?
39. Which API/report/balance-transaction records are authoritative for daily reconciliation?
40. Can Kirim export all provider records necessary to reconstruct charges, fees, refunds, disputes, transfers, reversals, and payouts?
41. What event or API state is the final confirmation that Customer funds are safe enough for the Shopper to purchase?
42. What event or API state is the final confirmation that the Shopper received a bank payout?

#### G. Pricing, limits, data, and termination

43. Provide Malaysia pricing for Connect account activity, charges, payment methods, refunds, disputes, transfers, payouts, FX, tax, and optional products.
44. Are there monthly minimums or paid-plan requirements that conflict with a small pilot?
45. What reserves, rolling holds, volume limits, first-payout delays, or account reviews should the pilot expect?
46. Where are Malaysian platform, Customer, Shopper, payment, identity, and support data processed/stored, and what DPA/subprocessor terms apply?
47. What records remain available after account closure, and what export must Kirim perform before termination?
48. What suspension/termination events could strand an active transaction, and what recovery/support path exists?

### 5.4 Written contact template

```text
Subject: Malaysia Stripe Connect feasibility for invite-only personal-shopping pilot

We are evaluating Stripe Connect for Kirim, a Malaysia-only, invite-only pilot with at most 50 accounts. A Customer requests items up to RM200 from one Shopper. The Shopper pays the merchant using personal funds.

The Customer pays in two stages: (1) an initial amount equal to the agreed Shopper fee, and (2) an itemized balance after actual prices are recorded. Kirim earns no platform fee during MVP. After receipt and delivery verification, verified item reimbursement plus the Shopper fee becomes eligible for transfer only after a 24-hour delivery-dispute window and only if no dispute is open.

We need written confirmation of the supported Malaysian Connect account/charge/transfer/payout model, liability allocation, delayed-transfer limits, refund/reversal behavior, payment-method differences, webhook/reconciliation records, pricing, and whether this model requires any preview or separately approved product.

We do not intend to hold funds outside a licensed provider or describe the arrangement as escrow without explicit confirmation. Please review the attached flow/questions against our Malaysia account and identify any unsupported or contractually restricted step.
```

### 5.5 Completion criteria

This action is complete only when Stripe provides written, account-applicable answers sufficient to map every mandatory money movement and exception. Unanswered or ambiguous mandatory questions remain blockers.

## 6. Action 3 — Arrange Malaysian Legal, Accounting, and Tax Review

### 6.1 Timing

Begin adviser selection while provider questions and sandbox work are underway. Conduct the substantive review after Stripe supplies enough written detail to identify the actual contractual funds flow. Complete all required advice before Stage B, live-mode activation, public payment claims, or production payment implementation approval.

### 6.2 Adviser selection

The Product Owner should seek advisers with relevant Malaysian experience in:

- Payment services/fintech and marketplace/platform arrangements.
- E-commerce and consumer terms/refunds.
- PDPA and cross-border processor arrangements.
- Marketplace bookkeeping, payouts/reimbursements, SST/tax, and record retention.

Ask each adviser to state scope, assumptions, exclusions, deliverables, fee estimate, and whether separate specialist advice is required. Do not ask the Engineering Lead or Stripe to substitute for independent professional advice.

### 6.3 Review package to provide

Provide advisers with:

- Relevant sections of `docs/REQUIREMENTS.md`.
- This feasibility plan.
- A money-flow/state diagram and sample amounts.
- Cancellation, expiry, failed-delivery, and dispute outcomes.
- Stripe's written responses and applicable terms/pricing.
- Proposed Customer/Shopper terms and privacy notice when drafted.
- Proposed invoices/receipts and processing-fee disclosure.
- Ledger and reconciliation design when available.
- Data-retention and cross-border provider list.
- Statement that Kirim earns no MVP platform fee.

Use synthetic examples and redact credentials/provider secrets.

### 6.4 Mandatory legal questions

1. Does any step constitute regulated custody, money transmission, merchant acquiring, e-money, escrow, remittance, or another licensed activity for Kirim?
2. Does using Stripe Connect and the selected charge/transfer model keep regulated payment custody with the provider, and under what conditions?
3. What is Kirim's legal role relative to Customer, Shopper, merchant, and payment provider: marketplace, disclosed agent, principal, facilitator, or another classification?
4. May Kirim delay transfer for the transaction duration and 24-hour dispute window?
5. Which user-facing words are permitted for held, secured, pending, reimbursed, released, refunded, and paid funds?
6. Are Customer-paid processing fees/surcharges permitted for each payment method, and what disclosure/consent is required?
7. Are the cancellation allocations, including 50% of the Shopper fee after valid price evidence, enforceable and adequately disclosed?
8. What consumer refund, unfair-contract, complaint, and chargeback rights override Kirim policy?
9. What KYC/AML/sanctions/fraud-monitoring duties remain with Kirim despite provider onboarding?
10. Are Shopper reimbursement and fees subject to employment, agency, gig-work, tax-reporting, or other classification obligations?
11. What terms, privacy notices, consent, age restrictions, prohibited-item controls, support process, and dispute disclosures are required?
12. What financial/payment/location/evidence records must Kirim retain, and for how long?
13. What cross-border transfer or processor disclosures/contracts are required for Stripe and related providers?
14. What happens legally and operationally if Stripe suspends Kirim while transactions remain active?

### 6.5 Mandatory accounting/tax questions

1. How should Customer receipts, provider balances, Shopper reimbursements, Shopper earnings, refunds, processing fees, disputes, reserves, and chargebacks be recorded?
2. Does Kirim recognize gross transaction value, no revenue, agency amounts, liabilities, or another presentation during MVP?
3. How should reimbursement be distinguished from Shopper earnings?
4. What invoices, receipts, statements, or self-billing documents must be issued to Customers or Shoppers?
5. Are SST, withholding, income-reporting, e-invoicing, or other Malaysian obligations triggered?
6. How should provider fees and non-returned processing costs be allocated and disclosed?
7. What daily/month-end reconciliation and exception records are required?
8. What retention period applies to financial and tax records?
9. How should negative balances, unrecoverable chargebacks, failed payouts, and abandoned/unclaimed amounts be treated?
10. What controls are required before the pilot can process real money?

### 6.6 Required deliverables

Obtain written outputs containing:

- Adviser identity, qualifications, scope, date, and assumptions.
- Clear conclusions for each mandatory question.
- Required product/contract/accounting changes.
- Prohibited wording or flows.
- Required registrations, policies, records, controls, or ongoing filings.
- Open questions and reliance limitations.
- Explicit statement of whether Stage B may proceed under the reviewed model.

### 6.7 Completion criteria

This action is complete when the Product Owner has written advice covering the implemented provider model, all required changes are tracked, and no unresolved issue could make the pilot unlawful, misleading, unaccounted, or operationally unsafe.

## 7. Engineering Sandbox and Failure-Recovery Plan

This work supports the owner actions; it does not replace provider or professional approval. The proposed provider-neutral states, journal, interfaces, and open Product Owner questions are documented in `docs/PAYMENT_DOMAIN_MODEL.md`.

### 7.1 Build order

1. Implement a deterministic fake payment provider against the proposed Kirim payment interface.
2. Model the Kirim ledger and state transitions independently from Stripe object names.
3. Implement a disposable Stripe adapter only after the expected Connect model is identified.
4. Run automated contract tests against Stripe test mode where practical.
5. Reconcile API responses, webhook events, balance transactions, transfers, and payouts into one test report.

### 7.2 Mandatory sandbox scenarios

| Area | Scenarios |
|---|---|
| Initial payment | Success, decline, challenge, abandon, timeout, duplicate submit, idempotent retry, webhook before/after API response |
| Balance payment | Success, asynchronous method, expiry, duplicate, amount changed, Customer cancellation race |
| Purchase authorization | Ensure Shopper is never authorized before every required payment reaches the approved provider state |
| Cancellation | Before price collection; after first valid price; balance expiry; Shopper-caused cancellation; provider fee not returned |
| Delivery window | Settlement job early, exactly at deadline, dispute just before deadline, concurrent dispute/settlement, clock skew |
| Settlement | Transfer success, duplicate command, timeout with unknown result, insufficient platform balance, account capability disabled |
| Payout | Pending, failed, returned, delayed, bank rejected, connected account restricted |
| Refund | Full/partial before transfer, after transfer, transfer reversal unavailable, duplicate refund, refund failure |
| Provider dispute | Chargeback before/after Shopper transfer/payout, evidence deadline, negative balance |
| Webhooks | Invalid signature, duplicate, delayed, reordered, missing event, unknown type, schema/version change |
| Reconciliation | Missing local record, missing provider object, amount/fee mismatch, stale pending state, manual correction through compensating audit event |
| Outage | Stripe unavailable, webhook outage, database commit uncertainty, Worker crash, retry exhaustion, permanent-failure queue |

### 7.3 Required technical evidence

- Sequence diagrams for each money flow.
- Stripe-object-to-Kirim-ledger mapping.
- State/event/error matrix.
- Idempotency-key scope and replay tests.
- Webhook signature/deduplication/order tests.
- Daily reconciliation report and exception queue.
- Demonstrated dispute-versus-settlement race protection.
- Demonstrated recovery from lost API responses and Worker crashes.
- Cost estimate using expected pilot volumes and worst reasonable abuse/retry volume.
- Manual support/reconciliation and pilot-pause runbook.

## 8. Action 4 — Approve or Reject Stripe Based on Evidence

### 8.1 Mandatory pass/fail gates

Stripe cannot be approved unless all are true:

- [ ] The Malaysia account and intended use are eligible under current Stripe terms.
- [ ] The connected-account type, onboarding, capabilities, and payout path are supported.
- [ ] Kirim can confirm Customer funds before allowing Shopper purchase.
- [ ] Kirim can delay transfer until after delivery plus the 24-hour dispute window.
- [ ] A timely dispute reliably prevents unsettled funds from being transferred.
- [ ] Full/partial refunds, cancellation allocations, reversals, failed payouts, and negative balances have documented handling.
- [ ] Provider payment disputes and post-payout losses have an accepted liability/control model.
- [ ] Signed webhooks, idempotent commands, authoritative reconciliation records, exports, and outage recovery are sufficient.
- [ ] The model does not require Kirim to perform an unapproved regulated custody/escrow/payment activity.
- [ ] Malaysian legal, accounting, and tax reviewers accept the documented model subject only to completed tracked actions.
- [ ] User-facing terminology, fees, refund terms, privacy, and records are approved.
- [ ] Expected fixed provider requirements fit the approved operating budget; variable payment fees are separately disclosed and accepted.
- [ ] Support can pause payments/settlement and manually reconcile exceptions safely.
- [ ] Stage B entry tests pass with no duplicate charge, refund, reimbursement, transfer, or payout.

A score or majority vote cannot override a failed mandatory gate.

### 8.2 Decision meeting inputs

The Product Owner reviews:

- Stripe's written answers and applicable terms.
- Sandbox/fake-provider test report.
- Legal opinion/action list.
- Accounting/tax opinion and ledger requirements.
- Pricing and projected pilot costs.
- Privacy/DPA and data-flow review.
- Security/threat review.
- Operational support, reconciliation, incident, and exit plans.
- Open risks with named owner and deadline.

### 8.3 Possible decisions

#### Approve

Record a new decision that supersedes the candidate-only status in DEC-057. Identify the exact Stripe account country, Connect account type, charge/transfer/payout model, supported payment methods, liability allocation, settlement rules, pricing basis, and approved contract/document versions.

#### Conditionally approve

Only non-production implementation may continue. Record every condition, owner, evidence required, deadline, and automatic expiry. Stage B remains blocked until all mandatory conditions close.

#### Reject

Record the failed gates and preserve evidence. Do not reshape the implementation secretly around unsupported behavior. Evaluate the fallback shortlist—such as Adyen for Platforms, Airwallex, Xendit, or an appropriate Malaysian provider—against the same Kirim model and checklist, or propose an explicit product-requirement change.

### 8.4 Post-approval controls

Approval is not permanent. Re-review when Stripe terms/products/pricing change, the account model changes, Kirim begins earning revenue, transaction limits/regions expand, new payment methods are added, dispute/loss incidents exceed tolerance, or legal/accounting advice changes.

## 9. Evidence Register

Maintain this table without secrets or unnecessary personal data:

| Evidence ID | Item | Owner | Requested | Received | Version/date | Result | Blocker/action |
|---|---|---|---|---|---|---|---|
| PAY-EV-001 | Stripe Malaysia account eligibility | Product Owner | — | — | — | Pending | — |
| PAY-EV-002 | Connect model written response | Product Owner | — | — | — | Pending | — |
| PAY-EV-003 | Pricing and fee schedule | Product Owner | — | — | — | Pending | — |
| PAY-EV-004 | Fake-provider test report | Engineering Lead | — | — | — | Pending | — |
| PAY-EV-005 | Stripe sandbox test report | Engineering Lead | — | — | — | Pending | — |
| PAY-EV-006 | Legal review | Product Owner | — | — | — | Pending | — |
| PAY-EV-007 | Accounting/tax review | Product Owner | — | — | — | Pending | — |
| PAY-EV-008 | Privacy/DPA review | Product Owner / Engineering | — | — | — | Pending | — |
| PAY-EV-009 | Reconciliation/incident runbook test | Engineering Lead / Support | — | — | — | Pending | — |
| PAY-EV-010 | Final approval record | Product Owner | — | — | — | Pending | — |

## 10. Immediate Next Actions

| Order | Action | Owner | Output |
|---:|---|---|---|
| 1 | Confirm whether a suitable Malaysian legal entity/business account exists for provider onboarding | Product Owner | Recorded prerequisite or blocker |
| 2 | Create and secure the Stripe Malaysia test account | Product Owner | PAY-EV-001 |
| 3 | Submit the Section 5 provider brief/questions | Product Owner, prepared with Engineering Lead | PAY-EV-002 and PAY-EV-003 |
| 4 | Review and refine `docs/PAYMENT_DOMAIN_MODEL.md`, then turn its states/interfaces into executable fake-provider tests | Engineering Lead / Product Owner | Reviewed domain model and technical spike specification |
| 5 | Shortlist Malaysian legal and accounting/tax advisers and request scoped quotations | Product Owner | Adviser comparison and expected one-time cost |
| 6 | Run fake-provider tests while awaiting Stripe answers | Engineering Lead | PAY-EV-004 |
| 7 | Map confirmed Stripe model and run sandbox tests | Engineering Lead | PAY-EV-005 |
| 8 | Obtain legal/accounting/privacy review of the confirmed model | Product Owner | PAY-EV-006 through PAY-EV-008 |
| 9 | Exercise reconciliation, outage, and pilot-pause procedures | Engineering Lead / Support | PAY-EV-009 |
| 10 | Conduct mandatory-gate review and record approve/conditional/reject decision | Product Owner | PAY-EV-010 and new decision record |

## 11. Official Starting References

- Stripe Connect availability for Malaysian businesses: https://support.stripe.com/questions/connect-availability-for-businesses-located-in-malaysia
- Stripe Connect Malaysia pricing: https://stripe.com/en-my/connect/pricing
- Stripe Connect marketplace overview: https://stripe.com/connect/marketplaces
- Stripe idempotent requests: https://docs.stripe.com/api/idempotent_requests
- Stripe webhook signatures: https://docs.stripe.com/webhooks/signature
- Stripe Connect webhooks: https://docs.stripe.com/connect/webhooks
- Stripe separate charges and transfers: https://docs.stripe.com/connect/separate-charges-and-transfers

Always verify the current account-specific documentation and contract rather than relying only on this reference list.
