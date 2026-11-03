# QA Test Matrix Guide & Edge Case Catalog

This guide provides test design patterns, coverage rules, and an edge case catalog for transforming PRD requirements into comprehensive test suites.

---

## 1. Five-Category Coverage Requirement

Every PRD must structure its QA Test Matrix across these five categories:

```
┌────────────────────────────────────────────────────────┐
│                   QA Test Matrix                       │
├─────────────┬─────────────┬────────────┬───────────────┤
│  Positive   │  Negative   │  Boundary  │ Concurrency / │  Security &   │
│ (Happy Path)│ (Validation)│(Edge Cases)│  Idempotency  │  Compliance   │
└─────────────┴─────────────┴────────────┴───────────────┴───────────────┘
```

### Table Column Format
All test cases must use this explicit tabular layout:

| Test ID | Req ID | Category | Scenario / Test Case | Preconditions & Test Data | Execution Steps | Expected System Result & State |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |

---

## 2. Universal Edge Case Catalog

### A. Network & Distributed Systems
* **Upstream Timeout / 504 Gateway Timeout:** The client initiates a request, downstream takes > threshold, client receives 504. System must maintain non-terminal pending state until reconciliation or async webhook resolves it.
* **Webhook Delay / Out-of-Order Webhooks:** Webhook `PAYMENT_COMPLETED` arrives before `PAYMENT_INITIATED` DB commit or 3 hours after transaction expiry.
* **Network Partition / Half-Open Connection:** Server writes DB record, but TCP connection drops before HTTP response reaches client. Client retries with same idempotency key.

### B. Idempotency & Concurrency
* **Rapid Concurrent Submissions:** Two identical requests fired within 10ms with identical `Idempotency-Key` or payment token. System must lock execution row; second request must return cached response or `409 Conflict`.
* **Balance / Limit Race Conditions:** Two concurrent withdrawals of ₹5,000 on an account with balance ₹6,000. Exactly one must succeed, second must fail with insufficient funds.
* **Idempotency Expiry:** Request repeated after idempotency window (e.g., 24 hours). System must treat as new request, not duplicate.

### C. Boundary & Monetary Rounding
* **Smallest Currency Unit:** Transactions of ₹0.01 / 1 paisa (or lowest denomination).
* **Maximum Regulatory Limit:** Maximum single-transaction cap (e.g., ₹1,00,000 or ₹5,00,000 for UPI / immediate settlement).
* **Rounding Half-Even (Banker's Rounding):** Verifying tax or fee splits that produce fractional currency units (e.g., 18% GST on ₹10.25) across ledger legs so sum of debits equals sum of credits to the exact paisa.

### D. Security, Auth & PII Checks
* **Unauthenticated Webhook:** Webhook sent with invalid, expired, or missing HMAC signature. Expected result: `401 Unauthorized` / `403 Forbidden`, rejection logged, zero state mutation.
* **Replay Attack:** Re-sending valid, signed payload with old timestamp outside tolerance window (>300 seconds). Expected result: rejected.
* **PII Leakage in Logs / Error Payloads:** Simulating unhandled exceptions to verify stack traces, server logs, and APM tools do not capture unmasked PII (card numbers, Aadhaar, password, CVV).
* **Role-Based Privilege Escalation:** Maker attempting to execute Checker approval action or standard customer user invoking merchant-scoped endpoints.

---

## 3. Tier-Based Test Matrix Sizing

* **Lightweight Tier (≤3 requirements):**
  * Minimum 1 Positive + 1 Negative test per requirement.
  * Focus on basic inputs, auth rejection, and primary output state.
* **Standard Tier (4–15 requirements):**
  * Minimum 2 tests per requirement across at least 3 categories.
  * Must include State Machine transition validations and boundary cases.
* **Comprehensive Tier (15+ requirements or Regulated):**
  * Full coverage of all 5 categories.
  * Must include all applicable catalog scenarios (idempotency, concurrency, reconciliation, and audit trail validation).
