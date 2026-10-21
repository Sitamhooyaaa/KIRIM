# Product Requirements

> Status: Draft — requirements must be agreed before implementation.

## 1. Product Summary

- **Product name:** Kirim

- **Problem:** People in busy urban areas such as Kuala Lumpur may spend significant time dealing with traffic, parking, and queues when purchasing everyday items. Meanwhile, people already travelling to those locations may be willing to fulfil nearby requests for additional income. Informal arrangements may lack reliable matching, payment protection, accountability, and dispute handling.

- **Proposed solution:** Kirim will be a mobile marketplace that matches people who need items with people who are already travelling along a compatible route.

  Kirim will support two primary roles:

  - **Customer:** asks another user to purchase and deliver one or more items.
  - **Shopper:** publishes an intended route and may accept compatible requests in exchange for a service fee.

  A Shopper publishes an intended route and travel window. For example: Trion 2 @ KL → TRX → Suria KLCC → Trion 2 @ KL. A Customer activates matching, allowing Kirim to use the Customer's location to identify compatible Shopper routes. The Customer receives relevant opportunities and can submit a request to a Shopper, who may accept or reject it.

  Kirim will provide route-based matching, request and order status, payment safeguards, delivery confirmation, and mutual ratings for verified completed transactions.

- **Primary users:**

  - Customers who want another person to purchase and deliver items for them.
  - Shoppers who want to earn additional income while making journeys they already intended to make.
  - Personal shoppers who want a platform for discovering and managing customer requests.

- **Value proposition:** Customers can avoid unnecessary travel, traffic, parking, and queues. Shoppers can earn additional income while completing journeys they already intended to make. Personal shoppers can use Kirim to discover and manage requests. Kirim provides structured matching, transaction tracking, trust mechanisms, and payment safeguards for both parties.

### Proposed Delivery Journey

> This is an initial proposal for refinement. It is not yet an approved workflow.

1. **Shopper publishes a journey:** The Shopper provides an intended route, destinations, and travel window.
2. **Customer activates matching:** Kirim uses the Customer's location and request needs to identify compatible Shopper routes. For the MVP, matching uses a 1 km corridor around the complete planned route rather than a radius around only the entered stops.
3. **Customer submits a request and fee offer:** The Customer specifies the requested items, preferred shop or location when relevant, acceptable substitutions, estimated budget up to the RM200 MVP order limit, delivery or meeting point, and an initial Shopper-fee offer. Kirim displays a suggested fee to guide the offer.
4. **Shopper responds once:** The Shopper sees the request, relevant locations, expected deviation, and proposed fee. The Shopper has 10 minutes to reject it, accept the proposed terms, or make one counteroffer.
5. **Customer gives final confirmation:** The Customer reviews the Shopper and final terms, then accepts or rejects them within 10 minutes. No transaction is formed until the Customer explicitly confirms. The Shopper fee is locked after confirmation.
6. **Kirim secures the initial payment:** After final confirmation and before the Shopper begins collecting items, the Customer pays an initial amount equal to the agreed Shopper fee. The funds are held for the Kirim transaction through the selected payment mechanism and are not yet paid to the Shopper.
7. **Shopper records actual prices:** At the shop, the Shopper scans or otherwise records the price of every requested product. Any unavailable item, substitution, or price outside the Customer's pre-approved limits requires Customer approval within 5 minutes; otherwise, the Shopper skips that item.
8. **Kirim calculates the final amount:** Kirim totals the recorded item prices, agreed Shopper fee, all Customer-paid payment-processing costs, and any other disclosed charges. It subtracts the held initial payment and every processing amount already paid, then sends the Customer an itemized invoice for the outstanding balance.
9. **Kirim secures the balance:** The Customer has 10 minutes to pay the outstanding balance before the Shopper completes the purchase. This payment is also held for the Kirim transaction rather than immediately paid to the Shopper.
10. **Shopper purchases the items using personal funds:** For the MVP, once Kirim confirms that the required Customer total has been secured, the Shopper pays the merchant using the Shopper's own money and provides final purchase evidence, such as a receipt.
11. **Delivery and handover are confirmed:** The parties complete delivery at the agreed location and confirm handover using a mechanism such as a one-time code or in-app confirmation.
12. **Shopper reimbursement and earnings are paid:** After the required payment, purchase, and delivery conditions are satisfied, Kirim reimburses the verified item cost and pays the Shopper's earnings according to the payout rules.
13. **Transaction is closed:** Both parties receive a final record and may rate each other. Refunds, disputes, cancellations, and failed handovers follow defined resolution rules.

### Proposed Payment Approach

The Customer first pays an initial amount, which is held for the Kirim transaction and is not immediately released to the Shopper. At the shop, the Shopper scans or records each product's actual price—for example, Item 1 at RM3 and Item 2 at RM5. Kirim calculates the complete transaction total and issues an itemized invoice showing the item costs, disclosed fees, initial payment, and outstanding balance.

The Customer then pays the outstanding balance, which is also held for the transaction. Kirim confirms to the Shopper that the required total has been secured. For the MVP, the Shopper pays the merchant using personal funds. After the purchase and successful delivery are verified, Kirim reimburses the verified item cost and pays the Shopper's earnings according to the payout and refund rules.

The intended product behavior is for Kirim to control the transaction's payment state. In implementation, the funds should be safeguarded by an appropriately licensed payment provider rather than placed directly under Kirim's custody unless the applicable Malaysian legal, regulatory, accounting, and operational requirements have been reviewed and satisfied.

#### MVP Checkout-Funding Decision

For the MVP, the Shopper will advance the purchase cost using personal funds. Kirim must show the full amount that the Shopper may need to advance before the Shopper confirms the purchase. The Shopper must not be instructed to purchase until the Customer's required funds are confirmed as secured. Reimbursement requires valid purchase evidence and successful completion under the approved delivery and dispute rules.

A platform-issued payment method, such as a controlled virtual card with a transaction spending limit, is recorded as a future option. It will be evaluated through potential-user research before being considered for implementation.

For the MVP:

- The initial payment equals the agreed Shopper fee.
- Kirim collects no platform fee or revenue.
- The Customer pays all clearly disclosed payment-processing fees.
- The maximum requested-item budget is RM200 per transaction.
- The Customer must approve substitutions or prices outside pre-approved limits within 5 minutes; otherwise, the affected item is skipped.
- The recorded prices and final receipt must be retained and reconciled before reimbursement.
- Verified item-cost reimbursement and Shopper earnings become payable only after successful delivery, except where an approved cancellation or dispute outcome provides otherwise.

The exact mix of barcode scanning, receipt scanning, shelf-label scanning, and manual price entry remains a technical/product-design decision. Regardless of input method, the Shopper must confirm each recorded price and submit a final receipt.

## 2. Goals and Success Metrics

The following targets are pilot hypotheses rather than production promises. Measure them during the first eight weeks of the real-user pilot. Treat outcome-based results as preliminary until at least 30 transactions have been completed.

| Goal | Metric | Target | Measurement window |
|---|---|---:|---|
| Demonstrate marketplace demand | Eligible requests receiving at least one Shopper response within 15 minutes | ≥ 60% | First 8 pilot weeks |
| Convert requests into transactions | Eligible submitted requests reaching Customer final confirmation | ≥ 40% | First 8 pilot weeks |
| Complete transactions reliably | Purchased transactions successfully delivered | ≥ 85% | First 8 pilot weeks; preliminary before 30 completions |
| Limit Shopper inconvenience | Shoppers reporting no more than 15 minutes of additional travel/work beyond expectations | ≥ 80% | Post-transaction survey during first 8 pilot weeks |
| Provide a satisfactory experience | Average verified rating for both roles | ≥ 4.0/5 | First 8 pilot weeks; report each role separately |
| Control disputes | Completed or failed-delivery transactions entering dispute | ≤ 10% | First 8 pilot weeks |
| Resolve pilot disputes promptly | Median time from dispute creation to recorded outcome | ≤ 24 hours | First 8 pilot weeks |
| Encourage repeated use | Surveyed users stating that they would use Kirim again | ≥ 60% | End-of-pilot survey |
| Protect payment integrity | Purchases started before required Customer funds are confirmed as secured | 0 | Continuous |
| Prevent payment duplication | Duplicate charges, reimbursements, or payouts | 0 | Continuous |
| Protect users | Confirmed critical safety incidents or personal-data exposures caused by Kirim | 0 | Continuous |

## 3. Non-Goals

For the MVP, Kirim will not provide:

- Operation outside the Kuala Lumpur Central Pilot Corridor.
- A Kirim revenue model; the MVP validates the service model before monetization.
- Advanced route optimization, traffic-based detour calculation, or Shopper-configured maximum-detour rules.
- A platform-issued payment method, such as a controlled virtual card.
- IC/MyKad identity verification, pending feasibility and privacy, security, and legal review.
- Requests spanning multiple shops or shopping locations. One request may contain multiple item lines, but all must come from one shop or location.
- Splitting one request between multiple Shoppers. One transaction has exactly one Customer, one Shopper, one shopping location, and one delivery or meeting point.
- More than one active accepted or confirmed transaction per Shopper. A Shopper must complete, cancel, or otherwise close the current transaction before accepting another Customer request.
- Cash on delivery, direct payment between users, off-platform payment, or unrecorded fee changes.
- Live merchant inventory, guaranteed product availability or pricing, direct merchant checkout, or point-of-sale integration.
- Automatic consequential dispute, refund, payout, fraud, or permanent-suspension decisions.
- Recurring requests, subscription orders, group orders, request splitting, multi-customer shopping batches, combined transaction payments, or multi-stop delivery for one transaction.
- Surge pricing, Shopper auctions, automatic parking or toll calculation, demand-based pricing, loyalty points, promotional credits, or referral rewards.
- Dedicated merchant dashboards, enterprise accounts, advertising, or paid business features.
- An iOS release; Stage A and the initial Stage B pilot are Android-first.

These exclusions may be reconsidered after MVP evidence. In particular, accepting requests from multiple Customers during one Shopper journey and adding iOS support are future options, not MVP capabilities.

## 4. Personas and Roles

A single account may act as either a Customer or a Shopper at different times. Role selection is contextual rather than a permanent account type. A user must not act as both parties in the same transaction.

| Role | Primary needs | Authority and constraints |
|---|---|---|
| Customer | Discover compatible Shopper journeys, request items, negotiate terms, make secured payments, track fulfilment, and receive items. | Controls only their own requests and Customer-side decisions; cannot perform Shopper actions in the same transaction. |
| Shopper | Publish a journey, evaluate requests, negotiate compensation, purchase and deliver items, and receive reimbursement and earnings. | Accesses only relevant matched or accepted requests; uses personal funds for MVP purchases; may have only one active accepted or confirmed transaction. |
| Platform administrator/support | Operate the pilot, enforce product and safety policies, investigate reports, and resolve payment or delivery exceptions. | Uses least-privilege access; consequential actions require a reason and audit record; original transaction evidence is immutable. |

### Shared User Rules

- One account may switch between Customer and Shopper modes.
- A user must not transact with their own account.
- Email address and phone number must both be verified.
- The user must provide a date of birth and declare that they are at least 18 years old. This is self-declared for the MVP and does not constitute verified proof of age.
- Phone numbers and email addresses remain hidden from other users; communication occurs in-app.
- Users may report another user or a transaction.
- Account suspension prevents new requests and acceptances immediately. Existing active transactions enter support review rather than being silently abandoned.

### Customer Authority

A Customer may:

- Create and cancel requests according to the approved cancellation rules.
- Make an initial Shopper-fee offer.
- Accept or reject the Shopper's single counteroffer.
- Give or withhold final transaction confirmation.
- Approve substitutions and price changes.
- Make required payments and view their own transaction/payment history.
- Confirm handover, open a dispute, submit evidence, and rate the Shopper after an eligible transaction.

A Customer must not:

- Modify locked terms after final confirmation except through an approved cancellation, substitution, or dispute flow.
- Access another user's transactions.
- Request prohibited products.
- Make off-platform payments or unrecorded fee changes.
- Rate a Shopper without an eligible transaction.

### Shopper Authority

A Shopper may:

- Publish and close journeys.
- Review compatible requests.
- Accept, reject, or make one counteroffer.
- Record prices and unavailable products.
- Request substitution or price-change approval.
- Upload purchase evidence and update fulfilment/delivery status.
- Submit delivery evidence, open a dispute, and rate the Customer after an eligible transaction.

A Shopper must not:

- Purchase before Kirim confirms that the required Customer funds are secured.
- Change the locked Shopper fee.
- Substitute or exceed approved prices without Customer approval.
- Accept another request while an accepted or confirmed transaction is active.
- Release funds, decide disputes, or access unrelated Customer information.

### Administrator/Support Authority

An authorized Administrator/Support user may:

- Review reported users, transactions, and prohibited requests.
- Access only the transaction, payment, communication, location, and identity information necessary for an assigned support case.
- Review evidence and manually record an MVP dispute outcome.
- Approve authorized refunds, reimbursements, and payouts under the documented rules.
- Suspend accounts when required for safety, policy enforcement, or investigation.
- Remove or block prohibited requests.

Administrator/Support users must:

- Provide a reason for each consequential action.
- Produce an attributable audit record for every financial, dispute, evidence-access, or account-status decision.
- Never alter original receipts, messages, approvals, location evidence, or transaction history.
- Avoid accessing or exposing personal information beyond what the assigned case requires.

### MVP Eligibility and Account Verification

- The working minimum age is 18 years old, subject to legal confirmation before a public pilot.
- MVP account verification uses a verified phone number and email address plus self-declared date of birth.
- Phone/email verification and self-declaration do not prove identity or age; the limitation must be disclosed and considered in pilot risk controls.
- IC/MyKad verification is deferred until its feasibility and privacy, security, and legal implications have been investigated.

### MVP Product Policy

The requested-item budget must not exceed RM200 per transaction.

Allowed categories are:

- Groceries.
- Prepared food.
- Household products.
- Personal-care products.
- Basic clothing and ordinary retail products.
- Other legal, low-risk products approved by Kirim and the payment provider.

The following are prohibited for the MVP:

- Illegal or stolen goods.
- Alcohol, tobacco, and vape products.
- Prescription or controlled medication.
- Weapons, explosives, and hazardous substances.
- Cash, lottery products, gift cards, and other cash equivalents.
- Live animals.
- Adult products.
- Oversized items requiring specialized transportation.
- Products requiring licenses, age checks, or specialized handling.
- Products prohibited by the payment provider.

Kirim may reject or remove a request that violates this policy. Product-policy enforcement and administrative actions must be recorded for audit and dispute purposes.

## 5. User Stories

### USR-001 — Register and Verify an Account

**As a** prospective user, **I want** to register and verify my contact channels **so that** I can use Kirim as a Customer or Shopper.

**Acceptance criteria**

- Given a new user, when they register, then Kirim requires a unique account, phone number, email address, date of birth, and acceptance of applicable policies.
- Given an unverified phone number or email address, when the user attempts to create or accept a request, then Kirim blocks the action.
- Given a declared age below 18, when registration is submitted, then Kirim rejects eligibility for the MVP.
- Kirim clearly states that MVP phone/email verification and date-of-birth declaration do not prove legal identity or age.

**Priority:** Must

### USR-002 — Manage Profile and Role Mode

**As a** verified user, **I want** to maintain my profile and switch between Customer and Shopper modes **so that** one account supports both use cases.

**Acceptance criteria**

- A verified user can select either Customer or Shopper mode without creating another account.
- Changing mode does not change the user's identity, history, ratings, obligations, or active transaction.
- A user cannot act as both parties in the same transaction or transact with their own account.

**Priority:** Must

### USR-003 — Recover Account Access

**As a** registered user, **I want** to recover account access securely **so that** loss of a password or session does not permanently lock me out.

**Acceptance criteria**

- Recovery requires control of an approved verified channel and does not reveal whether unrelated personal details are registered.
- Successful recovery invalidates recovery tokens after use and permits invalidation of prior sessions.
- Recovery attempts are rate-limited and security-relevant events are recorded.

**Priority:** Must

### JRN-001 — Publish a Shopper Journey

**As a** Shopper, **I want** to publish my intended route and travel window **so that** compatible Customers can discover the journey.

**Acceptance criteria**

- A Shopper can provide an origin, destination, optional planned stops, and future travel window within the supported pilot area.
- Kirim validates the route and creates the provisional 1 km matching corridor.
- Other users see only the minimum approximate journey information allowed by the privacy policy before confirmation.
- A Shopper can close a journey, but closing it must not silently abandon an accepted or confirmed transaction.

**Priority:** Must

### MAT-001 — Discover Compatible Journeys

**As a** Customer, **I want** to discover compatible Shopper journeys **so that** I can request service without asking Shoppers who would need a substantial detour.

**Acceptance criteria**

- Matching uses location only after the Customer grants the required permission or provides a location manually.
- A journey is eligible only when the shopping and delivery locations are compatible with its provisional 1 km route corridor and pilot boundary.
- A Shopper who already has an active accepted or confirmed transaction cannot accept another request.
- Matching results do not expose exact private route, contact, or delivery information prematurely.

**Priority:** Must

### REQ-001 — Create a Compliant Request

**As a** Customer, **I want** to describe the items I need from one shop **so that** a Shopper can evaluate the work accurately.

**Acceptance criteria**

- A request identifies one shop/location, item lines and quantities, estimated prices or budget, substitution preferences, delivery point, and initial Shopper-fee offer.
- The total requested-item budget cannot exceed RM200.
- Kirim rejects prohibited items, unsupported locations, incomplete details, and requests involving more than one shop.
- Creating a request does not itself form a transaction or charge the Customer.

**Priority:** Must

### NEG-001 — Negotiate and Confirm Terms

**As a** Customer and Shopper, **we want** a bounded negotiation **so that** compensation is agreed without an open-ended bargaining process.

**Acceptance criteria**

- Kirim suggests RM5 for up to three distinct item lines plus RM1 for each additional distinct item line.
- The Customer makes the initial offer, and the Shopper has 10 minutes to accept, reject, or make one counteroffer.
- If the Shopper counters, the Customer has 10 minutes to accept or reject; no further counteroffer is allowed.
- No transaction is formed until the Customer explicitly confirms the final terms.
- Final confirmation locks the Shopper fee and assigns the Shopper's single active transaction slot atomically.

**Priority:** Must

### PAY-001 — Secure the Initial Payment

**As a** Customer, **I want** to secure the initial payment **so that** the Shopper can begin price collection with evidence of my commitment.

**Acceptance criteria**

- The held initial payment equals the locked Shopper fee. Any payment-processing cost charged at that stage is disclosed separately and is not treated as Shopper funds.
- The Shopper cannot begin the recorded price-collection stage until the payment provider confirms that the required funds are secured.
- Initial-payment retries are idempotent and cannot create duplicate charges.
- A failed or abandoned payment leaves the transaction unapproved for price collection and provides a safe retry or cancellation path.

**Priority:** Must

### SHP-001 — Record Prices and Request Substitutions

**As a** Shopper, **I want** to record actual prices and unavailable products **so that** the Customer can approve the final basket before checkout.

**Acceptance criteria**

- The Shopper confirms each item, quantity, actual price, and availability using the supported input methods.
- A substitution or price outside a pre-approved limit creates an approval request for the Customer.
- The Customer has 5 minutes to approve or reject; no response causes the affected item to be skipped.
- The Shopper cannot mark a prohibited product, unapproved substitution, or over-limit item as approved for purchase.

**Priority:** Must

### PAY-002 — Review and Pay the Final Balance

**As a** Customer, **I want** an itemized final invoice **so that** I understand and secure the amount before the Shopper pays the merchant.

**Acceptance criteria**

- The invoice identifies each approved item and price, the locked Shopper fee, all disclosed processing costs, every amount already paid, and the outstanding balance.
- The calculation credits the held initial payment and any processing costs already paid exactly once and contains no Kirim platform fee during the MVP.
- The Customer has 10 minutes to pay the balance.
- Kirim does not instruct the Shopper to check out until the provider confirms that all required Customer funds are secured.
- Balance-payment retries are idempotent and cannot create duplicate charges.

**Priority:** Must

### SHP-002 — Record the Completed Purchase

**As a** Shopper, **I want** to record my completed purchase **so that** my personal expenditure can be verified for reimbursement.

**Acceptance criteria**

- The Shopper pays the merchant using personal funds only after Kirim confirms that Customer funds are secured.
- The Shopper submits a readable final receipt and confirms purchased quantities and prices.
- Kirim reconciles the receipt against the approved basket; material discrepancies block automatic progression and enter support review.
- The transaction preserves original price records, approvals, and receipt evidence.

**Priority:** Must

### DEL-001 — Complete or Report Delivery

**As a** transaction participant, **I want** delivery to be verifiable **so that** successful and failed handovers are handled consistently.

**Acceptance criteria**

- Delivery occurs at the confirmed current location, saved address, or agreed meeting point.
- Successful handover requires the approved in-app confirmation mechanism, such as a one-time code.
- If the Customer is unavailable, the Shopper contacts them in-app, waits 10 minutes, and submits arrival evidence.
- An uncompleted handover enters `delivery_failed`; it must not be represented as successful delivery.

**Priority:** Must

### PAY-003 — Reimburse and Pay the Shopper

**As a** Shopper, **I want** verified reimbursement and earnings **so that** I recover my purchase cost and receive agreed compensation.

**Acceptance criteria**

- Reimbursement uses reconciled receipt amounts, and earnings use the locked Shopper fee and approved cancellation/dispute rules.
- Normal settlement becomes eligible only after 24 hours have elapsed from confirmed delivery and no dispute is open.
- The deadline check and settlement reservation are atomic so a timely dispute cannot race with payout.
- Kirim does not release a duplicate reimbursement or payout when an operation is retried.
- A material discrepancy or open dispute blocks any affected automatic release until an authorized outcome is recorded.
- Every reimbursement, earning, refund, or release is linked to the transaction and auditable.

**Priority:** Must

### CAN-001 — Cancel a Request or Transaction

**As a** Customer or Shopper, **I want** cancellation consequences to be shown before confirmation **so that** I understand the financial result.

**Acceptance criteria**

- Before Customer final confirmation, either party may leave without a Shopper charge.
- Customer cancellation after confirmation but before price collection refunds the Shopper fee, subject to disclosed non-refundable provider costs.
- After at least one valid price is recorded with required location/time evidence, Customer cancellation or balance-payment expiry pays 50% of the agreed Shopper fee to the Shopper and refunds the remainder, subject to provider costs.
- Shopper-caused cancellation before purchase provides no Shopper compensation and refunds the Customer according to provider capabilities.
- After purchase, normal cancellation is unavailable and the case enters manual support/dispute handling.

**Priority:** Must

### DSP-001 — Open and Resolve a Dispute

**As a** Customer or Shopper, **I want** to dispute a transaction with evidence **so that** exceptional outcomes receive human review.

**Acceptance criteria**

- Either party may open a dispute within 24 hours of delivery or failed delivery.
- Kirim preserves the request, terms, messages, prices, receipt, approvals, location evidence, and handover evidence.
- Funds remain held where the provider supports it, and affected automatic release is blocked while the dispute is open.
- Only authorized support can record the outcome, financial adjustments, reason, and evidence considered.
- Kirim does not automatically decide a consequential MVP dispute.

**Priority:** Must

### RAT-001 — Rate the Other Party

**As a** Customer or Shopper, **I want** to rate the other participant after an eligible transaction **so that** future users have verified trust information.

**Acceptance criteria**

- Each party may submit no more than one rating for the other party per eligible transaction.
- Ratings are linked to real transactions and cannot be submitted by unrelated users or self-accounts.
- Kirim shows aggregate ratings without exposing private transaction information.
- Reported ratings can be moderated without silently changing the original submission or audit history.

**Priority:** Must

### SAF-001 — Report a User or Transaction

**As a** user, **I want** to report safety, fraud, privacy, or product-policy concerns **so that** Kirim can investigate them.

**Acceptance criteria**

- A report identifies a category, related user/transaction where applicable, description, and optional evidence.
- The reporter receives confirmation without being shown internal support-only information.
- Reports are restricted to authorized support and retained according to the approved policy.
- Urgent account restrictions do not erase or silently abandon active transaction obligations.

**Priority:** Must

### ADM-001 — Review an Assigned Support Case

**As an** authorized support user, **I want** case-scoped evidence access **so that** I can investigate without unrestricted access to user data.

**Acceptance criteria**

- Support access is limited to assigned cases and the minimum evidence required.
- Access to personal, location, communication, or financial evidence is attributable and audited.
- Support can identify prohibited requests, failed delivery, payment discrepancies, and reported conduct.
- Support cannot alter original messages, receipts, approvals, location evidence, or transaction history.

**Priority:** Must

### ADM-002 — Record a Consequential Decision

**As an** authorized Administrator/Support user, **I want** to record a reasoned decision **so that** refunds, payouts, disputes, and suspensions are accountable.

**Acceptance criteria**

- A consequential action requires the actor, timestamp, reason, case, evidence references, and resulting state/financial adjustment.
- The action is rejected when the actor lacks permission or the transaction state is incompatible.
- Repeated requests cannot duplicate a refund, reimbursement, payout, or suspension event.
- Original records remain immutable, and corrections occur through linked compensating actions.

**Priority:** Must

## 6. Core User Flows

### Flow 1 — Account Onboarding and Role Selection

1. A prospective user registers with a unique account, phone number, email address, date of birth, and required policy acceptance.
2. The user verifies both phone and email.
3. Kirim rejects users who declare an age below 18 and discloses that MVP verification does not prove identity or age.
4. The verified user selects Customer or Shopper mode and may switch modes later without creating another account.
5. Kirim preserves one identity, history, ratings, and obligations across modes and prevents self-transactions.

**Alternate outcomes:** Duplicate contact information, failed/expired verification, excessive attempts, ineligible age, suspension, and recovery are handled without granting transaction access prematurely.

### Flow 2 — Shopper Journey Publication

1. A verified user enters Shopper mode.
2. The Shopper provides an origin, destination, optional planned stops, and a future travel window.
3. Kirim validates the route against the Kuala Lumpur Central Pilot Corridor and generates a provisional 1 km matching corridor.
4. The Shopper reviews and publishes the journey.
5. Kirim exposes only approximate journey information before a transaction is confirmed.
6. The Shopper may close the journey if doing so does not abandon an active accepted or confirmed transaction.

**Alternate outcomes:** Unsupported or invalid routes are rejected; a Shopper with an active transaction remains unable to accept another request.

### Flow 3 — Customer Matching, Request, and Negotiation

1. A verified user enters Customer mode and grants location permission or supplies a location manually.
2. Kirim shows eligible journeys whose shopping and delivery locations are compatible with the route corridor and pilot boundary.
3. The Customer selects a journey and creates a one-shop request within the RM200 item-budget and product-policy limits.
4. Kirim displays the configurable suggested fee, and the Customer submits an initial offer.
5. The Shopper has 10 minutes to accept, reject, or make one counteroffer.
6. The Customer has 10 minutes to accept or reject the result.
7. Customer confirmation locks the terms and atomically reserves the Shopper's single active transaction slot.
8. The Customer pays a held initial amount equal to the locked Shopper fee and separately pays any processing cost disclosed for that payment.
9. Only confirmed payment-provider success advances the transaction to price collection.

**Alternate outcomes:** Rejection, expiry, invalid products, unsupported locations, unavailable Shopper capacity, failed payment, and safe retry do not create duplicate or partially active transactions.

### Flow 4 — Price Collection, Final Payment, and Purchase

1. The Shopper arrives at the selected shop and records each requested item's availability, quantity, and actual price.
2. An unavailable item, substitution, or price outside pre-approved limits creates a Customer approval request.
3. The Customer has 5 minutes to approve or reject; no response causes that item to be skipped.
4. Kirim generates an itemized invoice containing approved item costs, the locked Shopper fee, processing costs, initial payment, and outstanding balance.
5. The Customer has 10 minutes to pay the outstanding balance.
6. Kirim waits for confirmed payment-provider success before authorizing checkout.
7. The Shopper pays the merchant with personal funds and uploads the final receipt.
8. Kirim reconciles the receipt with the approved basket. Material discrepancies enter support review instead of automatic progression.

**Alternate outcomes:** Customer cancellation, balance expiry, payment failure, unavailable items, receipt discrepancy, and Shopper cancellation follow the approved cancellation and dispute rules.

### Flow 5 — Delivery, Settlement, and Ratings

1. After a reconciled purchase, the Shopper travels to the confirmed delivery or meeting point.
2. The app may share consent-based live location only during the active transaction.
3. The parties complete handover using the approved in-app confirmation mechanism, such as a one-time code.
4. Kirim records successful delivery and starts the 24-hour dispute-submission window.
5. If no dispute is open when the 24-hour window ends, normal settlement becomes eligible and Kirim reimburses the verified item cost and pays eligible Shopper earnings through the provider.
6. A timely dispute atomically blocks affected settlement until an authorized outcome is recorded.
7. Both parties receive a final transaction record and the Shopper can see the expected settlement time.
8. Each party may submit one verified rating for the other.
9. Live location sharing stops when the transaction ends.

### Flow 6 — Cancellation and Payment Expiry

1. Kirim identifies the transaction's current state when either party cancels or a required action expires.
2. Before Customer final confirmation, either party may leave without a Shopper charge.
3. After confirmation but before price collection, Customer cancellation refunds the Shopper fee subject to disclosed non-refundable provider costs.
4. After at least one valid price is recorded with required location/time evidence, Customer cancellation or balance-payment expiry allocates 50% of the agreed Shopper fee to the Shopper and refunds the remainder, subject to provider costs.
5. Shopper-caused cancellation before purchase provides no Shopper compensation and refunds the Customer according to provider capabilities.
6. After purchase, normal cancellation is unavailable and the transaction enters manual support/dispute handling.
7. Every financial operation is idempotent, attributable, and linked to the cancellation reason.

### Flow 7 — Failed Delivery, Dispute, and Support Resolution

1. If handover cannot occur, the Shopper contacts the Customer in-app, waits 10 minutes, and records arrival evidence.
2. Kirim marks the transaction `delivery_failed`; it does not treat the transaction as delivered.
3. Either party may open a dispute within 24 hours of delivery or failed delivery and submit evidence.
4. A dispute blocks affected automatic financial release, and funds remain held where supported.
5. Assigned support reviews only necessary request, terms, message, price, receipt, approval, location, and handover evidence.
6. Authorized support records the outcome, reason, evidence considered, state change, and financial adjustments.
7. Refunds, reimbursements, payouts, or suspensions are executed idempotently through audited actions; original evidence remains immutable.
8. The parties are notified of the recorded outcome without receiving restricted internal or unrelated personal information.

## 7. Functional Requirements

All requirements below are proposed for the MVP and remain subject to final approval of this document.

### Account and Authorization

| ID | Requirement | Priority | Acceptance criteria | Status |
|---|---|---|---|---|
| FR-AUTH-001 | Kirim shall register one unique account with phone number, email address, date of birth, and policy acceptance. | Must | Duplicate identities/contact channels are handled safely; required fields and acceptance are validated. | Proposed |
| FR-AUTH-002 | Kirim shall require verification of both phone number and email before transactional actions. | Must | Unverified users cannot create, accept, confirm, purchase, deliver, rate, or dispute a transaction. | Proposed |
| FR-AUTH-003 | Kirim shall reject MVP registration when the declared age is below 18 and disclose that self-declaration is not proof of age. | Must | Under-18 declarations cannot activate an account; the limitation is visible to eligible users. | Proposed |
| FR-AUTH-004 | Kirim shall allow one account to switch between Customer and Shopper modes while preserving one identity and history. | Must | Switching does not remove obligations or permit a user to act as both parties in one transaction. | Proposed |
| FR-AUTH-005 | Kirim shall prevent self-transactions. | Must | Customer and Shopper account identifiers must differ before confirmation. | Proposed |
| FR-AUTH-006 | Kirim shall provide rate-limited account recovery through approved verified channels. | Must | Recovery tokens are single-use/expiring, and successful recovery can invalidate prior sessions. | Proposed |
| FR-AUTH-007 | Kirim shall enforce account status server-side. | Must | Suspended users cannot create or accept new work; active obligations enter support review. | Proposed |

### Journey and Matching

| ID | Requirement | Priority | Acceptance criteria | Status |
|---|---|---|---|---|
| FR-JRN-001 | Kirim shall let a Shopper publish an origin, destination, optional stops, and future travel window. | Must | Invalid, expired, or unsupported journeys are rejected. | Proposed |
| FR-JRN-002 | Kirim shall validate journeys against the Kuala Lumpur Central Pilot Corridor. | Must | Journeys outside the approved map boundary cannot be published for the MVP. | Proposed |
| FR-JRN-003 | Kirim shall generate a provisional 1 km matching corridor around the complete planned route. | Must | Matching uses the route geometry rather than only circles around entered stops. | Proposed |
| FR-JRN-004 | Kirim shall allow a Shopper to close a journey without abandoning an active transaction. | Must | Closure removes future matching but preserves or routes active obligations to support. | Proposed |
| FR-MAT-001 | Kirim shall use Customer location only with permission or manual location entry. | Must | Denied location permission has a manual alternative; location is not collected silently. | Proposed |
| FR-MAT-002 | Kirim shall match only requests whose shopping and delivery points are compatible with the pilot boundary and route corridor. | Must | Incompatible requests are not presented as matches. | Proposed |
| FR-MAT-003 | Kirim shall prevent a Shopper from accepting more than one active accepted or confirmed transaction. | Must | Concurrent attempts cannot bypass the limit. | Proposed |
| FR-MAT-004 | Kirim shall minimize route, delivery, and contact disclosure before confirmation. | Must | Exact restricted details remain unavailable to unrelated or unconfirmed users. | Proposed |

### Requests and Negotiation

| ID | Requirement | Priority | Acceptance criteria | Status |
|---|---|---|---|---|
| FR-REQ-001 | Kirim shall support multiple item lines from exactly one shop/location per request. | Must | Multi-shop requests are rejected; quantities of one product remain one distinct item line. | Proposed |
| FR-REQ-002 | Kirim shall enforce the RM200 requested-item budget limit. | Must | Requests exceeding RM200 cannot be submitted or confirmed. | Proposed |
| FR-REQ-003 | Kirim shall enforce the approved allowed/prohibited-product policy. | Must | Prohibited or payment-provider-restricted products are rejected or routed for review. | Proposed |
| FR-REQ-004 | Kirim shall capture item, quantity, budget, substitution, shop, delivery, and fee-offer details. | Must | Incomplete or invalid requests cannot enter negotiation. | Proposed |
| FR-NEG-001 | Kirim shall suggest RM5 for up to three distinct item lines plus RM1 per additional distinct item line. | Must | The suggestion is calculated consistently and remains configurable. | Proposed |
| FR-NEG-002 | Kirim shall allow a Customer initial offer and exactly one Shopper response: accept, reject, or counter. | Must | A second counteroffer is rejected. | Proposed |
| FR-NEG-003 | Kirim shall expire each required negotiation response after 10 minutes. | Must | Expired offers cannot be accepted without a new valid request/offer. | Proposed |
| FR-NEG-004 | Kirim shall form a transaction only after explicit Customer final confirmation. | Must | Confirmation locks terms and reserves the Shopper's active slot atomically. | Proposed |

### Payments, Shopping, and Evidence

| ID | Requirement | Priority | Acceptance criteria | Status |
|---|---|---|---|---|
| FR-PAY-001 | Kirim shall calculate the held initial payment as the locked Shopper fee and disclose any associated processing cost separately. | Must | Processing cost is Customer-paid but is not treated as held Shopper funds; no Kirim platform fee is included. | Proposed |
| FR-PAY-002 | Kirim shall rely on confirmed payment-provider state before allowing price collection or checkout. | Must | Client-side success alone cannot advance the transaction. | Proposed |
| FR-PAY-003 | Kirim shall produce an itemized invoice with approved items, fees, all amounts already paid, and outstanding balance. | Must | Totals reconcile; the held initial payment and previously paid processing costs are each credited exactly once. | Proposed |
| FR-PAY-004 | Kirim shall give the Customer 10 minutes to secure the outstanding balance. | Must | Expiry invokes the approved cancellation-compensation rule. | Proposed |
| FR-PAY-005 | Kirim shall make charge, refund, reimbursement, and payout operations idempotent. | Must | Retried operations cannot duplicate financial effects. | Proposed |
| FR-PAY-006 | Kirim shall represent held, failed, refunded, disputed, reimbursed, and released payment states distinctly. | Must | Users and support cannot mistake a pending/held amount for a final payout or refund. | Proposed |
| FR-PAY-007 | Kirim shall make normal Shopper settlement eligible 24 hours after confirmed delivery when no dispute is open. | Must | The deadline/dispute check and settlement reservation are atomic; open or timely disputes block affected release; the Shopper sees the expected timing. | Proposed |
| FR-SHP-001 | Kirim shall let the Shopper record each item's quantity, availability, and actual price. | Must | Each value is attributable to the Shopper and transaction. | Proposed |
| FR-SHP-002 | Kirim shall require Customer approval within 5 minutes for substitutions or prices outside pre-approved limits. | Must | Rejection or expiry skips the affected item. | Proposed |
| FR-SHP-003 | Kirim shall block purchase authorization until all required Customer funds are confirmed as secured. | Must | A Shopper is never instructed to purchase from a pending or failed payment state. | Proposed |
| FR-SHP-004 | Kirim shall record that the Shopper uses personal funds for the MVP merchant purchase. | Must | The maximum amount to advance is shown before checkout authorization. | Proposed |
| FR-SHP-005 | Kirim shall require a readable final receipt and reconcile it against the approved basket. | Must | Material discrepancies block automatic settlement and create a support-review path. | Proposed |
| FR-SHP-006 | Kirim shall preserve original prices, receipts, approvals, and evidence. | Must | Corrections append linked records rather than overwrite originals. | Proposed |

### Delivery, Cancellation, and Disputes

| ID | Requirement | Priority | Acceptance criteria | Status |
|---|---|---|---|---|
| FR-DEL-001 | Kirim shall support delivery to a confirmed current location, saved address, or agreed meeting point. | Must | Exactly one delivery/handover point is locked per transaction. | Proposed |
| FR-DEL-002 | Kirim shall support consent-based live location only during an active transaction. | Must | Sharing is visible, revocable where safely possible, and stops when the transaction ends. | Proposed |
| FR-DEL-003 | Kirim shall require approved in-app handover confirmation for successful delivery. | Must | Missing confirmation cannot be represented as successful without authorized support resolution. | Proposed |
| FR-DEL-004 | Kirim shall implement the 10-minute failed-delivery procedure. | Must | In-app contact, waiting period, and arrival evidence precede `delivery_failed`. | Proposed |
| FR-CAN-001 | Kirim shall calculate cancellation outcomes from the verified transaction state and responsible party. | Must | Outcomes match Section 9 and are shown before cancellation confirmation. | Proposed |
| FR-CAN-002 | Kirim shall pay 50% of the locked Shopper fee after qualifying verified price collection when the Customer cancels or balance payment expires. | Must | At least one valid price with required location/time evidence exists; suspicious evidence enters review. | Proposed |
| FR-DSP-001 | Kirim shall allow either party to dispute delivery or failed delivery within 24 hours. | Must | Late submissions are handled according to a documented support exception rather than silently accepted as timely. | Proposed |
| FR-DSP-002 | Kirim shall block affected automatic settlement while a dispute is open. | Must | Funds remain held where supported until an authorized outcome. | Proposed |
| FR-DSP-003 | Kirim shall require human authorization for consequential MVP dispute outcomes. | Must | The system may organize evidence but cannot autonomously decide refunds, payouts, or fault. | Proposed |

### Trust, Administration, and Notifications

| ID | Requirement | Priority | Acceptance criteria | Status |
|---|---|---|---|---|
| FR-RAT-001 | Kirim shall allow one verified rating per party per eligible transaction. | Must | Self, duplicate, and unrelated ratings are rejected. | Proposed |
| FR-RAT-002 | Kirim shall display aggregate ratings without exposing private transaction information. | Must | Moderation preserves the original submission and audit history. | Proposed |
| FR-SAF-001 | Kirim shall allow users to report safety, fraud, privacy, product-policy, or conduct concerns. | Must | Reports support categories, description, related records, optional evidence, and receipt confirmation. | Proposed |
| FR-ADM-001 | Kirim shall restrict support access to assigned cases and minimum necessary evidence. | Must | Sensitive evidence access is authorized, attributable, and audited. | Proposed |
| FR-ADM-002 | Kirim shall record every consequential support action with actor, time, reason, case, evidence references, and effect. | Must | Original records remain immutable; corrections use linked compensating actions. | Proposed |
| FR-ADM-003 | Kirim shall authorize and execute refunds, reimbursements, payouts, suspensions, and prohibited-request removal according to role and state. | Must | Unauthorized or state-incompatible actions are rejected without partial effects. | Proposed |
| FR-NOT-001 | Kirim shall send in-app and push notifications for time-sensitive transaction actions. | Must | Notifications cover offers, counteroffers, approvals, payment deadlines, delivery, cancellations, disputes, and outcomes without exposing sensitive content on a locked device. | Proposed |
| FR-NOT-002 | Kirim shall treat notifications as prompts, not transaction authority. | Must | Missing or duplicate delivery of a notification does not change state; users can reconcile current state in-app. | Proposed |

## 8. Non-Functional Requirements

These are MVP service objectives rather than guarantees to end users unless explicitly adopted in published terms.

| Area | Requirement/target |
|---|---|
| Availability | Target 99% monthly availability during the pilot, excluding announced maintenance. Payment and support records must remain recoverable even when the main service is unavailable. |
| API performance | For requests not waiting on an external provider, p95 response time is under 2 seconds for normal reads and under 3 seconds for normal writes. |
| Matching performance | p95 compatible-journey results are returned within 5 seconds under the defined pilot load. |
| Scalability | Support at least 1,000 registered pilot users and 100 concurrently active users without architectural replacement. Load testing must validate critical matching and transaction paths before expanding the pilot. |
| Payment reliability | Financial operations are idempotent, provider states are reconciled, ambiguous outcomes remain pending rather than assumed successful, and duplicate financial effects are prohibited. |
| Security | Encrypt data in transit and sensitive stored data; use secure password hashing and session/token handling; enforce authorization server-side and least privilege; protect secrets; rate-limit abuse-sensitive actions; validate untrusted input; scan dependencies; and audit sensitive access/actions. Complete threat modeling and security review before a real-user pilot. |
| Privacy | Before confirmation, disclose only minimum approximate route/location information. Reveal exact delivery details only to confirmed parties. Hide phone numbers and email addresses from other users and keep communication in-app. Live location requires consent, is limited to an active transaction, and stops immediately when the transaction closes. |
| Data retention | Retain precise transaction-location evidence for 30 days after closure unless disputed. Retain in-app messages, receipts, approvals, and dispute evidence for 90 days after closure. Retain an account profile while active. Complete deletion requests within 30 days except for active disputes, fraud prevention, accounting, or legal holds. Confirm financial-record retention against Malaysian legal/accounting obligations before public launch. |
| Data rights | Provide an authenticated process for users to request access/export, correction where records are mutable, and deletion. Immutable transaction evidence is corrected through linked records and is disclosed or retained only as permitted. |
| Accessibility | Target WCAG 2.2 Level AA where applicable and support mobile screen readers, scalable text, sufficient contrast, non-color status indicators, and appropriately sized touch targets. Accessibility checks are part of release verification. |
| Compatibility | Stage A and the initial Stage B pilot are Android-first. Supported Android OS versions and device baselines must be selected in `TECH_STACK.md` from target-user and framework evidence before implementation. iOS is deferred, but avoid unnecessary domain/API choices that would prevent later support. |
| Localization | MVP user-facing content supports English and Bahasa Malaysia. Currency is MYR, business time uses Malaysia time, and dates/times are displayed unambiguously while stored consistently. |
| Observability | Use structured privacy-safe logs, metrics for critical transaction/payment state transitions, error monitoring, and alerts for elevated payment, settlement, matching, or delivery-confirmation failures. Do not place secrets, full payment data, or unnecessary personal/location data in logs. |
| Backup | Create encrypted daily backups of required persistent data and perform a documented restore test at least monthly during the pilot. Restrict and audit backup access. |
| Recovery | Target a recovery point objective of 24 hours and recovery time objective of 8 hours for the MVP, while financial state must also be reconciled against the payment provider after recovery. |
| Auditability | Financial, administrative, evidence-access, authorization, and consequential state-changing events are attributable, timestamped, append-only or tamper-evident, and retained according to policy. |
| Resilience | External payment, mapping, notification, or verification failures preserve the last verified state, do not imply success, and provide safe reconciliation/retry behavior. |
| Support coverage | Pilot support operates daily from 9:00 AM to 9:00 PM Malaysia time. Urgent safety reports submitted outside these hours enter a priority queue and may trigger bounded temporary risk controls pending human review; permanent or consequential outcomes require authorization. |

## 9. Edge Cases and Failure Behavior

### Cancellation

| Situation | Required outcome |
|---|---|
| Before the Shopper responds | Customer may cancel without charge. |
| Shopper rejects the request | No transaction is formed; any temporary authorization is released. |
| During negotiation | Either party may leave without charge. |
| After Shopper response but before Customer confirmation | No binding transaction exists and no charge applies. |
| After confirmation but before price collection begins | Customer receives a refund, subject only to disclosed non-refundable payment-provider costs. |
| After price collection begins but before purchase | If at least one valid price has been recorded with required location/time evidence, Customer cancellation pays 50% of the agreed Shopper fee to the Shopper. The remainder is refunded, subject to disclosed non-refundable provider costs. |
| Customer does not pay the balance within 10 minutes | The request is cancelled. If at least one valid price has been recorded with required location/time evidence, 50% of the agreed Shopper fee is paid to the Shopper and the remainder is refunded, subject to disclosed non-refundable provider costs. |
| Shopper cancels before purchase | Customer receives a full refund, including the Shopper fee, subject to payment-provider capabilities. |
| After purchase | Normal cancellation is unavailable; the transaction enters manual support or dispute handling. |
| Shopper cannot complete delivery after purchase | Funds remain held where supported, and support determines reimbursement and refund responsibility from the evidence. |

No cancellation compensation is paid when the Shopper causes the cancellation. Suspicious, insufficient, or disputed price-collection evidence enters manual review before any compensation is released.

### Substitutions and Price Changes

- A Shopper must not substitute an item without Customer approval.
- A Customer may pre-approve specific substitutions and price limits.
- A Shopper must not purchase above the Customer's approved maximum.
- The Customer has 5 minutes to respond to an approval request; otherwise, the affected item is skipped.
- Final receipt prices must be reconciled against recorded prices.
- Material discrepancies enter manual review and block automatic payout.

### Failed Delivery

1. The Shopper arrives at the agreed delivery point and contacts the Customer through in-app communication.
2. The Shopper waits 10 minutes and records arrival evidence.
3. If handover does not occur, the transaction enters `delivery_failed` and funds remain held where supported.
4. Support reviews location, communication, purchase, and delivery evidence.
5. The Customer may remain responsible for verified non-returnable item costs and applicable Shopper compensation.

### Disputes

- Either party may open a dispute within 24 hours of delivery or failed delivery.
- Disputed funds remain held where supported until an outcome is recorded.
- Support reviews the request, negotiated terms, in-app communication, recorded prices, receipt, substitution approvals, location evidence, and handover evidence.
- All support decisions and financial adjustments must be attributable and audited.
- MVP disputes are manually reviewed; the system must not decide consequential disputes automatically.

### Technical and Concurrency Failures

- Invalid or incomplete input must be rejected with actionable feedback and must not advance transaction state.
- Payment, confirmation, cancellation, and payout operations must be idempotent so retries cannot duplicate charges or releases.
- A request may form a transaction with only one Shopper, and a Shopper may have only one active accepted or confirmed transaction. Concurrent acceptance attempts must not create duplicate transactions or bypass the Shopper activity limit.
- If a payment or mapping dependency is unavailable, the transaction remains in its last verified state and the user is told to retry safely.
- Offline or slow clients must not assume success; they must reconcile with the server before showing a final state.
- Permission or account-status changes take effect server-side and may block subsequent actions without exposing another user's private data.

## 10. Release Scope

The MVP is released in two stages so that the complete workflow can be validated before Kirim handles real personal data and money.

### Stage A — Internal MVP Prototype

Stage A validates product behavior without public or financial exposure:

- Synthetic users, transactions, evidence, and locations only.
- Payment-provider sandbox only; no real charges, held funds, refunds, reimbursements, or payouts.
- Test routes within the versioned provisional pilot geofence.
- Complete Customer, Shopper, and Administrator/Support workflows.
- Complete success, cancellation, expiry, failed-delivery, dispute, recovery, and external-provider-failure paths.
- English and Bahasa Malaysia user-interface validation.
- Android usability, device-compatibility, and accessibility validation for the approved supported versions/devices.
- No public registration and no claim of production readiness.

### Stage B — Controlled Real-User Pilot

Stage B validates demand and operations under controlled real-world conditions:

- Invite-only access with a maximum of 50 pilot accounts initially.
- Kuala Lumpur Central Pilot Corridor only: a versioned geofence extending approximately 2 km around an approved canonical Trion 2 → TRX → Bukit Bintang → KLCC route snapshot. The saved GeoJSON/polygon, not the informal place names, is authoritative.
- Maximum RM200 requested-item budget per transaction.
- One Customer, one Shopper, one shop, and one delivery/meeting point per transaction.
- No more than one active accepted or confirmed transaction per Shopper.
- Real transactions only through an approved payment provider with the required marketplace/held-funds capabilities.
- Shopper advances merchant purchase costs using personal funds.
- No Kirim platform revenue; the Customer pays disclosed payment-processing costs.
- Daily support from 9:00 AM to 9:00 PM Malaysia time.
- Eight-week initial measurement period, with outcome results considered preliminary before 30 completed transactions.

### Required MVP Capabilities

The complete MVP includes:

1. Account registration, phone/email verification, recovery, role switching, and suspension handling.
2. Shopper journey publication and provisional 1 km corridor matching.
3. One-shop Customer requests, RM200 limit, and product-policy enforcement.
4. Suggested Shopper fee and one-counteroffer negotiation with Customer final confirmation.
5. Initial payment, itemized final invoice, balance payment, and payment-state reconciliation.
6. Item availability/price recording and Customer-controlled substitution/price approval.
7. Personal-funds purchase, receipt submission, and basket reconciliation.
8. Delivery, consent-based live location, handover confirmation, and failed-delivery evidence.
9. Shopper reimbursement, earnings, cancellation compensation, and Customer refunds.
10. Manual dispute resolution, ratings, safety reports, and audited administrative actions.
11. In-app communication and privacy-safe transaction/push notifications.
12. English and Bahasa Malaysia localization.
13. Security, privacy, accessibility, monitoring, backup, recovery, and support controls defined in this document.

### Stage B Entry Gates

The controlled real-user pilot must not begin until:

- Supported Android OS versions/devices are approved and tested.
- The canonical route snapshot and its approximately 2 km versioned GeoJSON/polygon are generated, visually reviewed, and approved.
- The payment provider, held-funds model, 24-hour post-delivery settlement rule, webhook/reconciliation behavior, and failure handling are approved and technically validated.
- The payment model receives applicable Malaysian legal, regulatory, accounting, and tax review.
- User terms, privacy notice, product policy, cancellation/refund policy, and dispute policy are published and accepted in a versioned form.
- Threat modeling, authorization review, dependency/security scanning, and critical-path security tests are completed.
- Payment idempotency, reconciliation, refund, reimbursement, payout, and ambiguous-provider-outcome tests pass.
- Backup restoration and provider-state recovery are tested.
- Support, safety, dispute, privacy-request, and incident-response procedures are documented and rehearsed.
- No unresolved critical safety, payment, authorization, data-integrity, or privacy defect remains.

### Pilot Completion Review

At the end of the initial pilot, Kirim will:

- Report every Section 2 metric with sample sizes and limitations.
- Review Customer and Shopper interviews and support feedback.
- Evaluate Shopper willingness and practical limits for advancing personal funds.
- Reassess the 1 km matching corridor, RM200 item limit, fee formula, response times, and support coverage.
- Review disputes, cancellations, payment failures, delivery failures, prohibited requests, and safety/privacy reports.
- Decide explicitly whether to continue unchanged, revise and repeat, expand, pause, or stop the pilot.

### Later Releases

The following remain outside the MVP:

- Expansion beyond the Kuala Lumpur Central Pilot Corridor and an iOS release.
- Multiple simultaneous Customer requests per Shopper journey.
- Multi-shop, multi-Shopper, group, recurring, batched, subscription, or multi-stop orders.
- Platform-issued payment methods, including controlled virtual cards.
- Advanced route optimization, traffic-aware detour estimation, and Shopper-configurable matching preferences.
- IC/MyKad identity verification unless approved after feasibility, privacy, security, and legal review.
- Merchant inventory, checkout, point-of-sale, or business-account integrations.
- Automated consequential dispute, refund, payout, fraud, or permanent-suspension decisions.
- Kirim monetization, surge pricing, promotions, referrals, loyalty programs, advertising, and enterprise features.

## 11. Risks, Assumptions, and Open Questions

The previously deferred product decisions are resolved: the MVP is Android-first, normal settlement waits through the 24-hour dispute window, and the pilot area uses a versioned approximately 2 km geofence around the approved canonical corridor. Implementation details and validation work remain gated below.

| Type | Item | Planned control / resolution | Owner | Deadline |
|---|---|---|---|---|
| Implementation prerequisite | Select and test supported Android OS versions/devices. | Use target-user and framework evidence in `TECH_STACK.md`; do not infer support silently. | Product Owner / Engineering Lead | Before Stage A implementation approval |
| Payment prerequisite | Validate that the selected provider can support the 24-hour post-delivery settlement and dispute-blocking model. | Prove the behavior in sandbox and contract review, including race conditions, webhooks, reconciliation, reversals, and provider outages. | Product Owner / Engineering Lead | Before Stage B |
| Geofence prerequisite | Materialize the approved pilot concept as a versioned approximately 2 km GeoJSON/polygon around a canonical Trion 2 → TRX → Bukit Bintang → KLCC route snapshot. | Visually review and approve the stored boundary; future changes create a new version and do not reinterpret historical transactions. | Product Owner | Before Stage B |
| Marketplace risk | Too few Customers or Shoppers may prevent useful matching. | Invite a balanced pilot cohort and monitor response, confirmation, and completion rates. | Product Owner | Pilot recruitment and monitoring |
| Financial/adoption risk | Shoppers may be unwilling or unable to advance personal funds. | Keep the RM200 limit, show the maximum advance, secure Customer funds first, and validate willingness through interviews/pilot data. | Product Owner | Before and during Stage B |
| Payment/legal risk | A provider may not support the intended marketplace held-funds, reimbursement, refund, and payout model. | Complete provider due diligence and applicable Malaysian legal, regulatory, accounting, and tax review before architecture/payment-contract approval. | Product Owner | Before Stage B architecture is finalized |
| Fraud risk | A party may falsify prices, receipts, location, delivery, or cancellation evidence. | Reconcile evidence, retain immutable originals, detect anomalies, apply transaction limits, and use manual review. | Product Lead / Support | Before Stage B and continuous |
| Identity/safety risk | Phone/email verification and self-declared age provide weak identity assurance. | Use invite-only access, verified channels, limits, reporting, suspension, safety guidance, and later evaluate stronger verification. | Product Owner | Before Stage B |
| Privacy risk | Routes, live location, addresses, messages, and contact data could expose users' homes or habits. | Apply approved minimum disclosure, consent, least privilege, short precise-location retention, access audit, and deletion/export procedures. | Product Lead | Before Stage B |
| Physical-safety risk | Customer–Shopper handover may expose either party to unsafe conduct or locations. | Support agreed public meeting points, in-app reporting, bounded location sharing, safety guidance, escalation, and pilot suspension capability. | Product Owner / Support | Before Stage B |
| Product/food risk | Prepared food, groceries, allergies, spoilage, or damaged goods may create health and quality disputes. | Publish responsibility and prohibited-item guidance, preserve timestamps/evidence, and review category incidents during the pilot. | Product Owner / Support | Before Stage B |
| Matching assumption | A 1 km route corridor provides useful matches without excessive Shopper detours. | Measure acceptance and reported additional travel/work; adjust only through an approved requirement change. | Product Owner | During Stage B review |
| Operational risk | Manual disputes and safety reports may exceed support capacity. | Limit Stage B to 50 accounts, use defined hours/evidence requirements, monitor queue age, and pause intake if capacity is unsafe. | Product Owner / Support | Continuous during Stage B |
| Dependency risk | Payment, maps, notifications, or verification providers may be delayed, duplicated, or unavailable. | Preserve last verified state, use idempotency and reconciliation, retry safely, monitor failures, and provide support fallback. | Engineering Lead | Before Stage B and continuous |
| Legal assumption | The working minimum age is 18 and the selected product/payment model is permissible. | Obtain applicable Malaysian professional review rather than relying on this document as legal advice. | Product Owner | Before Stage B |
| Cost risk | Kirim earns no MVP revenue while payment, maps, notifications, support, and infrastructure incur costs. | Approve a capped pilot budget and review costs before expansion. | Product Owner | Before Stage B |
| Critical-incident risk | A safety, payment, authorization, data-integrity, or privacy defect may require immediate suspension. | Maintain an incident runbook, transaction/pilot pause control, backups, communication process, and explicit owner authority. | Product Owner / Product Lead | Before Stage B |

## 12. Definition of Done

### Work Item or Feature

A work item or feature is done only when:

1. It is linked to approved requirement and user-story IDs.
2. All applicable acceptance criteria pass.
3. Tests cover the expected path, validation, authorization, important edge cases, and regressions.
4. Relevant formatting, linting, type-checking, automated tests, and build checks pass.
5. Payment and other consequential state-changing behavior is tested for retries, concurrency, idempotency, and duplicate prevention.
6. Authentication and authorization are enforced at the server-side trust boundary.
7. Security, privacy, accessibility, data-retention, and abuse implications are reviewed.
8. Applicable database migrations, backup, restoration, and recovery behavior are tested.
9. API, schema, operational, support, and user documentation match the implemented behavior.
10. Required privacy-safe logs, metrics, alerts, and support diagnostics exist.
11. Deployment and rollback or recovery procedures are documented and tested as appropriate.
12. No unresolved critical safety, payment, authorization, data-integrity, privacy, or functional defect remains.
13. Reviewer-Tester approves the implementation and verification evidence, or documents an explicitly accepted exception.
14. The Product Owner accepts the completed result.

### Stage A Completion

Stage A is done only when:

- All Must-priority requirements included in the approved Stage A scope are implemented.
- All seven Section 6 core flows pass end-to-end using synthetic data and sandbox payments.
- Cancellation, expiry, duplicate request, concurrency, external-provider failure, failed delivery, dispute, backup/recovery, and account-recovery paths are verified.
- No real personal data or real money has been used.
- Known limitations, deferred decisions, and Stage B blockers are documented accurately.
- The Product Owner explicitly accepts the Stage A result without interpreting it as production readiness.

### Stage B Readiness

Stage B is ready only when:

- Every Stage B entry gate in Section 10 is satisfied with evidence.
- Critical security, authorization, privacy, payment, and data-integrity tests pass.
- Backup restoration and payment-provider reconciliation are demonstrated.
- Support, safety, privacy-request, dispute, and incident-response procedures are rehearsed.
- Monitoring and pilot-pause controls are operational.
- The Product Owner gives explicit real-user pilot launch approval.

### Completion Report

Every completion report must identify:

- Requirement and user-story IDs completed.
- Behavior and files changed.
- Checks actually run and their results.
- Security, privacy, accessibility, payment, data, and migration implications.
- Known limitations or accepted exceptions.
- Remaining risks, blockers, and the recommended next action.
