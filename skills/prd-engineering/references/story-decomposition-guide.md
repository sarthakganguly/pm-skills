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

## 5. The Optional Tool Gate & Companion Backlog Files

The Tool Gate is an optional downstream transformation step. If requested, it takes the decomposed INVEST stories and outputs a dedicated **companion markdown file** (e.g., `docs/backlog-jira.md`, `docs/backlog-gitlab.md`, or `docs/backlog-generic.md`) tailored to the specific ticketing system.

---

### A. Jira Tool Gate

#### 1. Discovery Checklist (Never Assume):
Ask the user:
- **Jira Environment:** Are you using **Jira Cloud** (Standard Markdown) or **Jira Data Center / Server** (Wiki Markup)?
- **Parent Epic:** What is the Jira Epic Key (e.g., `PROJ-101`) or Epic Summary name to link as Parent?
- **Components:** Any project-level components to assign (e.g., `Payments`, `Checkout`, `Core-Banking`)?
- **Labels:** Any specific labels to apply (e.g., `upi-autopay`, `fintech`, `q3-initiatives`)?

#### 2. Jira Hierarchy:
* **Parent Level 1:** Epic (`Parent` field in Jira Cloud)
* **Child Level 0:** Story or Task (peers under the Epic)
* **Child Level -1:** Sub-tasks (for granular technical chores)

#### 3. Jira Companion Output Template (`docs/backlog-jira.md`):

##### When Jira Cloud (Markdown Auto-formatting):
```markdown
# Epic: [EPIC-KEY / Name] [Module / Feature Title]

## Story: [US-101] [Imperative Action Title]
**Parent Epic:** [PROJ-101]  
**Issue Type:** Story  
**Components:** Payments, Checkout  
**Labels:** upi-autopay, fintech  
**Story Points:** 3  

### Description
**As a** Registered Shopper  
**I want to** view a dynamic UPI QR code with pre-filled amount during checkout  
**So that** I can scan and complete my payment without manual entry  

### Acceptance Criteria
```gherkin
Scenario: Successful QR generation
  Given the shopper has a cart with payable amount ₹1,250.00
  When the shopper selects "Pay via UPI QR"
  Then the system displays the dynamic UPI QR within 500ms
```

### Technical Notes
- **Traced REQ:** `REQ-101`
- **Traced QA:** `QA-101`, `QA-105`
- **Feature Flag:** `checkout_upi_dynamic_qr_v1`
```

##### When Jira Data Center (Text Formatting Notation / Wiki Markup):
```text
h2. [US-101] [Imperative Action Title]
*Parent Epic:* PROJ-101
*Issue Type:* Story
*Components:* Payments, Checkout
*Labels:* upi-autopay, fintech
*Story Points:* 3

h3. Description
*As a* Registered Shopper
*I want to* view a dynamic UPI QR code with pre-filled amount during checkout
*So that* I can scan and complete my payment without manual entry

h3. Acceptance Criteria
{code:gherkin}
Scenario: Successful QR generation
  Given the shopper has a cart with payable amount ₹1,250.00
  When the shopper selects "Pay via UPI QR"
  Then the system displays the dynamic UPI QR within 500ms
{code}

h3. Technical Notes
* *Traced REQ:* REQ-101
* *Traced QA:* QA-101, QA-105
* *Feature Flag:* checkout_upi_dynamic_qr_v1
```

---

### B. GitLab Tool Gate

#### 1. Discovery Checklist:
Ask the user for specific scoped labels or apply defaults:
- **Region Labels:** Ask user for specific region value (e.g. `in`, `us`, `global`), else default to `region::`.
- **Workflow Status:** Default to `workflow::grooming` and `triage::pending` (or ask for custom workflow labels).
- **Work Item Type:** Default to `type::enhancement` or `type::customisation` (ask user preference).
- **Parent Epic:** Group Epic name or ID (e.g. `&123` or `Epic Name`).

#### 2. GitLab Hierarchy:
* **Parent:** Group Epic (`&123`)
* **Child:** Project Issue (`#456`)
* **Sub-items:** Tasks

#### 3. GitLab Companion Output Template (`docs/backlog-gitlab.md`):
Include **GitLab Quick Actions** directly in the markdown block so developers can paste the entire block into a new GitLab issue and have labels, weight, and epic automatically applied:

```markdown
# Epic: [Group Epic Name or &123]

---

## Issue: [US-101] [Imperative Action Title]

/epic [Epic Name or &123]
/weight 3
/label ~"region::in" ~"workflow::grooming" ~"triage::pending" ~"type::enhancement"

### Description
**As a** Registered Shopper  
**I want to** view a dynamic UPI QR code with pre-filled amount during checkout  
**So that** I can scan and complete my payment without manual entry  

### Acceptance Criteria
```gherkin
Scenario: Successful QR generation
  Given the shopper has a cart with payable amount ₹1,250.00
  When the shopper selects "Pay via UPI QR"
  Then the system displays the dynamic UPI QR within 500ms
```

### Traceability & Notes
- **Parent Requirements:** `REQ-101`
- **Verification Tests:** `QA-101`, `QA-105`
- **State Transition:** `CART_LOCKED` ➔ `PENDING_SCAN`
- **Feature Flag:** `checkout_upi_dynamic_qr_v1`
```

---

### C. Other Tools (Linear, GitHub Issues, Azure DevOps, Shortcut)

#### 1. Discovery Checklist:
Ask the user:
- What tool are you using?
- Are there specific labels, tags, team names, or milestones to embed?
- What issue hierarchy does your team use (e.g., Linear Projects ➔ Issues ➔ Sub-issues; GitHub Milestones ➔ Issues ➔ Tasklists)?

#### 2. Companion Output Template (`docs/backlog-[tool].md`):
Format with clean standard markdown tasklists, metadata badges, and explicit parent/child relationships compatible with the chosen tool's import or copy-paste workflow.
