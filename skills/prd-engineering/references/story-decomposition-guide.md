# Agile Story Decomposition & Slicing Guide

This reference guide provides engineering-ready heuristics, INVEST principles, and templates for decomposing PRD functional requirements (`REQ-xxx`) into atomic, sprint-ready Agile User Stories (`US-xxx`).

---

## 1. Core Principles: The INVEST Standard

Every decomposed user story must satisfy the **INVEST** quality criteria:

| Letter | Principle | Description & Verification |
| :---: | :--- | :--- |
| **I** | **Independent** | Stories should be decoupled so they can be prioritized, planned, and implemented in any order whenever possible. |
| **N** | **Negotiable** | Details and edge cases can be refined during sprint planning without altering the core functional contract. |
| **V** | **Valuable** | Delivers concrete user or system value (avoid purely technical stories like "create database table"). |
| **E** | **Estimable** | Clear enough that engineering can provide a reliable complexity estimate (story points or T-shirt size). |
| **S** | **Small** | Sized to fit comfortably within a single sprint (typically 1–5 story points; ≤ 3 days of dev time). |
| **T** | **Testable** | Possesses deterministic acceptance criteria (Gherkin format) that QA can automate directly. |

---

## 2. Vertical Slicing Techniques

> [!IMPORTANT]
> **Slice Vertically, Never Horizontally.**
> A horizontal slice separates layers (e.g., UI ticket, Backend ticket, DB ticket). None of these deliver value independently.
> A vertical slice cuts through all layers (UI + API + DB) to deliver a thin, end-to-end slice of working software.

```
       Horizontal Slicing (Anti-pattern)
┌──────────────────────────────────────────────┐
│  Story 1: Build Frontend UI Components       │
├──────────────────────────────────────────────┤
│  Story 2: Build Backend Controller & API     │
├──────────────────────────────────────────────┤
│  Story 3: Create Database Tables & Migrations│
└──────────────────────────────────────────────┘

        Vertical Slicing (Recommended)
┌───────────┬───────────┬───────────┬───────────┐
│  Story 1  │  Story 2  │  Story 3  │  Story 4  │
│Happy Path │Validation │Timeouts & │Refunds &  │
│ End-to-End│ & Errors  │ Retries   │ Reversals │
│(UI+API+DB)│(UI+API+DB)│(UI+API+DB)│(UI+API+DB)│
└───────────┴───────────┴───────────┴───────────┘
```

### Heuristic 1: Workflow Steps / State Transitions
Slice along the states of the feature's state machine:
* `US-101`: Initiation & Initial Display (State: `INITIATED` ➔ `PENDING`)
* `US-102`: Successful Completion & Callback (State: `PENDING` ➔ `SUCCESS`)
* `US-103`: Expiry & Timeout Recovery (State: `PENDING` ➔ `EXPIRED`)

### Heuristic 2: Happy Path First, Then Exceptions
* **Slice 1 (Trunk):** Core happy path with zero special cases.
* **Slice 2 (Branch):** Input validations and rejected flows.
* **Slice 3 (Branch):** Network failures, timeouts, and third-party error handling.
* **Slice 4 (Branch):** Edge cases, concurrency limits, and idempotency collisions.

### Heuristic 3: Business Rule Variations
When a feature has multiple variations (e.g., multiple payment methods, tier-based discounts), make each major variant its own story:
* Story A: Standard Flat Discount
* Story B: Tiered Percentage Discount with Upper Cap

---

## 3. Standard Story Specification Schema

Each story in the PRD should follow this schema to make downstream processing through a tool gate (Jira, Linear, GitHub Issues) completely deterministic:

```markdown
#### [US-ID]: [Imperative Action Title]

- **As a** <Persona / Role>
- **I want to** <Action / Capability>
- **So that** <Business / User Value>

**Metadata & Traceability:**
- **Module / Epic:** <Module or Epic Name>
- **Traced Requirements:** `REQ-xxx`
- **Traced NFRs / Security:** `NFR-xxx`, `SEC-xxx`
- **Traced QA Tests:** `QA-xxx`
- **Complexity Sizing:** <Story Points or T-shirt Size> (e.g., 3 pts / Medium)
- **Preconditions / Blockers:** <Prerequisite states or dependency stories>

**Acceptance Criteria (Gherkin):**
```gherkin
Scenario: [Title of scenario]
  Given [precondition or initial system state]
  When [user or system action occurs]
  Then [expected outcome and state change]
  And [subsequent side effect or event published]
```

**Technical Notes (Tool-Gate Ready):**
- **Feature Flag:** `<flag_name>`
- **State Transition:** `<FROM_STATE>` ➔ `<TO_STATE>`
- **Side Effects:** DB ledger record, notification dispatch, webhook callback
```

---

## 4. Anti-Patterns to Avoid

| Anti-Pattern | Why It Fails | Correction |
| :--- | :--- | :--- |
| **Technical Slicing** (*"Create table schema"*) | Zero end-user value; cannot be verified independently by QA. | Group schema creation inside the first functional vertical story. |
| **Vague Acceptance Criteria** (*"Should look good and handle errors"*) | Untestable by QA; causes sprint debate. | Use strict Gherkin scenarios with explicit error messages and HTTP status codes. |
| **Orphaned Stories** | Story without a parent `REQ-xxx` creates unapproved scope creep. | Every `US-xxx` must link to at least one `REQ-xxx` in the PRD. |
| **Oversized Stories (> 8 pts)** | Cannot finish within a sprint; blocks pipeline. | Split by workflow steps, happy path vs. edge cases, or data variations. |

---

## 5. Tool-Gate Tagging Readiness

To ensure stories are ready to pass through any project management tool gate:
1. **Labels / Components:** Map to the PRD module name (e.g., `component:checkout`, `epic:payments`).
2. **Priority:** Derive priority from the traced `REQ-xxx` (P0 = Blocker/Highest, P1 = High, P2 = Medium).
3. **Acceptance Criteria:** Gherkin text can be pasted cleanly into Jira's Acceptance Criteria field, Linear description, or GitHub issue body.
