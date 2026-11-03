---
name: prd-engineering
description: >-
  Produces engineering- and QA-ready Product Requirement Documents (PRDs) with
  deterministic business logic, state machines, compliance mapping, a 5-category QA test matrix,
  and sprint-ready Agile User Story decomposition.
  Use when creating, drafting, or generating a PRD, Functional Specification Document,
  or feature specification. Use when translating a rough product idea, business
  requirement, or epic into an actionable spec with edge cases, compliance coverage,
  and QA readiness. Use when auditing or stress-testing an existing PRD for gaps
  in state transitions, failure modes, regulatory alignment, or test coverage.
---

# PRD Engineering

## Overview

A PRD that Engineering and QA can execute without follow-up clarification meetings. This skill produces structured, unambiguous Product Requirement Documents where every requirement is atomic, verifiable, and traceable to at least one test case. It enforces deterministic business rules, explicit state machines, measurable non-functional targets, and a compliance section scoped to the product's actual regulatory domain — not boilerplate.

The quality bar: if a backend engineer, frontend engineer, and QA engineer read the PRD independently, they should reach the same interpretation of every requirement without talking to each other.

## When to Use

- Creating a new PRD, feature specification, or functional specification document
- Translating a business requirement, epic, or rough idea into an engineering-ready spec
- Auditing an existing PRD for edge cases, state transition gaps, compliance holes, or QA coverage
- Upgrading a lightweight spec into a full PRD for a regulated or complex feature

**When NOT to use:**

- Single-line bug fixes, config changes, or typo corrections
- The feature already has a complete spec and only needs engineering task breakdown → use `planning-and-task-breakdown`
- The user's intent is still ambiguous and needs extraction before any spec work → use `interview-me` first
- The task is a lightweight spike or throwaway prototype where a full PRD is overhead
- A Technical Design Document (TDD) or Architecture Decision Record (ADR) is what's actually needed → use `documentation-and-adrs`

## The Process

### Phase 0: Scope & Complexity Assessment

Before writing anything, determine the PRD tier. Not every feature needs a 15-section document.

| Tier | Signal | PRD Scope |
|---|---|---|
| **Lightweight** | ≤3 requirements, no multi-state lifecycle, no regulatory overlay, single system | Sections 1 + 3 + 7 only (metadata, functional requirements, NFRs). No state machine or compliance section needed. |
| **Standard** | 4–15 requirements, has a state lifecycle, touches ≤3 systems, standard compliance | Full PRD minus optional technical sections (API schemas, DB models). State machine required. |
| **Comprehensive** | 15+ requirements, multi-system integration, regulatory compliance, financial transactions, PII handling | Full PRD with all sections including compliance module, RBAC matrix, and complete QA test matrix. |

State the tier explicitly at the start of the PRD and justify it. If the user disagrees, adjust.

### Phase 1: Discovery & Scoping

Evaluate the user's input for completeness. Two modes:

**Interactive Mode (default when input is high-level):**

Before batching questions, form a hypothesis about what the user needs (borrow the `interview-me` discipline). Then ask focused clarifying questions one or two at a time:

1. **Core flow & failure modes:** What happens during partial failures, timeouts, concurrent requests, or retries? What are the terminal vs. non-terminal states?
2. **Regulatory domain:** What compliance regime applies? (e.g., Indian Fintech, US Healthcare/HIPAA, EU/GDPR, none). Don't assume — ask.
3. **Scale & SLA targets:** Are there specific throughput (TPS/RPM), latency (p95/p99), availability, or disaster recovery constraints?
4. **Technical depth:** Should this PRD include API schemas and/or data models, or will those live in a separate TDD?
5. **Story Decomposition & Tool Gate (Optional):** Ask the user which output mode they prefer:
   - **Mode A (PRD Only):** Pure requirement specification without story decomposition.
   - **Mode B (Standard Stories):** Include Section 10 with clean, tool-agnostic INVEST user stories in Markdown.
   - **Mode C (With Tool Gate):** Include Section 10 AND generate a dedicated companion backlog file (`docs/backlog-[tool].md`) tailored for direct import or copy-paste into their project management tool:
     - **If Jira:** Ask whether using **Jira Cloud** (Standard Markdown) or **Jira Data Center** (Wiki Markup); ask for Parent Epic name/key, Components, and Labels; and ask whether they want **Jira Markdown/Markup only** or also want an **optional Jira CSV import table** (`docs/backlog-jira.csv`).
     - **If GitLab:** Ask for Region label value (default `region::`), confirm Workflow Status (`workflow::grooming`, `triage::pending`), Work Item Type (`type::enhancement` or `type::customisation`), and Parent Group Epic. Embed GitLab Quick Actions (`/label`, `/epic`, `/weight`).
     - **If Other Tool (Linear, GitHub, etc.):** Ask for any specific tags, labels, or milestone metadata to embed.
6. **Definition of Done (DoD) Checklist (Optional):** Ask if the user wants an optional Definition of Done (DoD) checklist included. If yes, **show the proposed baseline DoD checklist to the user first for review/customization** before adding it to the PRD or stories:
   - Propose standard quality gates: unit test coverage threshold, integration test pass, telemetry/trace IDs emitted, zero PII in logs, feature flag verified off/on.
   - Explicitly ask: *"Here is the proposed Definition of Done checklist. Would you like to keep these, customize them, or omit DoD?"*
   - Never embed a DoD checklist without first presenting it to the user.

Stop when you can predict the user's answer to the next question you would ask. If after 6 questions you still can't, step back and ask whether something foundational is missing.

**Fast-Track Mode (when the user says "use best-practice assumptions" or provides detailed input):**

Proceed directly to generating the PRD. Document all assumptions in a dedicated **Assumptions & Open Questions** table:

| ID | Assumption | Category | Impact if Wrong | Owner | Due Date |
|---|---|---|---|---|---|
| `ASM-001` | Product targets Indian market only | Business | High — changes compliance baseline | PM | - |

### Phase 2: PRD Generation

Structure the PRD using requirement IDs (`REQ-001`, `NFR-001`, `SEC-001`, `QA-001`) for bidirectional traceability. Every requirement must be atomic, unambiguous, verifiable, and free of vague adjectives ("fast", "seamless", "robust", "user-friendly").

#### Section 1: Document Control & Executive Summary

- **Metadata:** Version, Status (Draft / In Review / Approved), Target Release, Author, Tech Lead, QA Lead, Compliance Sign-off (if applicable), PRD Tier (Lightweight / Standard / Comprehensive)
- **Problem Statement:** What user/system problem are we solving? What is the business driver?
- **Success Metrics (KPIs):** North-star metric + operational metrics + guardrail metrics. Every metric must be quantified (e.g., "transaction success rate ≥ 99.2%", not "high success rate").
- **Scope Boundaries:** Explicit **In-Scope (P0/P1)** vs. **Out-of-Scope (Non-Goals)**

#### Section 2: User Personas, System Actors & Permissions *(Standard + Comprehensive tiers)*

- Define human actors (End User, Admin, Support Agent, Compliance Officer) and system actors (Upstream APIs, Webhook Listeners, Schedulers)
- **Permission Matrix:** Who can Create / View / Edit / Approve / Delete / Export for each entity. Include PII visibility levels where applicable.
- For regulated products: include Maker-Checker workflows where required by the compliance regime.

#### Section 3: Functional Requirements & Deterministic Rules

Group by module or epic. For each requirement:

- **Requirement ID & Priority:** `REQ-101 [P0 — Must Have]`
- **Deterministic Business Logic:** Exact validation rules, formulas, rounding rules (specify rounding mode and precision for monetary values), idempotency requirements, retry policies, and rate limits
- **Error & Exception Handling:** Exact error codes/types, user-facing messages, system log actions, and fallback behavior when dependencies fail or time out. No "handle gracefully" — specify what happens.

#### Section 4: State Machine & Flow *(Standard + Comprehensive tiers)*

Every feature with a multi-state lifecycle **must** include:

- **Mermaid Diagram:** A `stateDiagram-v2` or `sequenceDiagram` showing the end-to-end flow including async callbacks, timeout transitions, and compensation paths.
- **State Transition Table:**

| Current State | Trigger / Event | Guard Condition | Next State | Side Effects | Rollback / Failure Action |
|---|---|---|---|---|---|

- Explicitly separate **non-terminal** states (`INITIATED`, `PROCESSING`, `PENDING_CALLBACK`) from **terminal** states (`SUCCESS`, `FAILED`, `REVERSED`, `EXPIRED`).
- Every non-terminal state must have: a defined timeout, a retry/backoff policy, and a reconciliation path to reach a terminal state.

#### Section 5: Technical Specifications *(Optional — include when requested or when context demands)*

- **API Contracts:** Method, path, headers, request/response schemas with field-level types, validation constraints, and status code mapping
- **Data Models:** Entities, keys, indexing, immutability constraints, PII encryption/hashing indicators
- **Monetary Precision:** All currency fields must specify storage format (integer in smallest unit like paise/cents, or exact decimal like `DECIMAL(19,4)`) with explicit rounding mode.

#### Section 6: Compliance & Regulatory Requirements *(When a regulatory domain applies)*

Do not hardcode a regulatory baseline. Instead:

1. During discovery, identify the applicable regulatory domain(s)
2. Load the relevant compliance module from `references/` (e.g., [Indian Fintech compliance baseline](./references/compliance-indian-fintech.md))
3. Map each regulatory requirement to a concrete system behavior in the PRD
4. Include a compliance sign-off row in the metadata table

If no regulatory domain applies, omit this section entirely and note "No regulatory overlay identified" in the metadata.

> [!IMPORTANT]
> Agent-generated compliance content is a starting point for discussion with qualified legal/compliance counsel, not legal advice. Flag any compliance requirement you are uncertain about in the Open Questions table.

#### Section 7: Non-Functional Requirements & SLAs

Never leave NFRs generic. Define concrete, testable thresholds:

| Category | Metric | Target | Measurement Method |
|---|---|---|---|
| **Performance** | p95 response time | `< 200ms` (reads), `< 800ms` (writes) | Load test at peak TPS |
| **Availability** | Uptime | `99.95%` monthly | Monitoring dashboard |
| **Recovery** | RPO / RTO | Specify per data tier | DR drill |
| **Idempotency** | Window | e.g., 24h on idempotency key | Integration test |
| **Concurrency** | Locking strategy | Optimistic / pessimistic per resource | Race condition test |
| **Observability** | Required fields | `trace_id`, `transaction_id`, error codes | Log audit |
| **Alerting** | Threshold | e.g., "alert if 5xx rate > 0.5% over 3 min" | Alert rule config |

If the user hasn't specified targets, propose sensible defaults and flag them as assumptions.

#### Section 8: QA Test Matrix

Provide a test matrix table that QA engineers can convert directly into test suites. Cover **all five categories**:

| Test ID | Req ID | Category | Scenario | Preconditions & Test Data | Steps | Expected Result & State |
|---|---|---|---|---|---|---|

**Categories:** Positive (happy path), Negative (validation/auth failures), Boundary/Edge Cases, Concurrency/Idempotency/Network Failures, Security/Compliance.

Scale the matrix to the PRD tier:
- **Lightweight:** ≥1 test per requirement, covering at minimum positive + one negative
- **Standard:** ≥2 tests per requirement across ≥3 categories
- **Comprehensive:** Full coverage across all 5 categories with domain-specific edge cases

See [QA Matrix Guide](./references/qa-matrix-guide.md) for domain-specific edge case catalogs.

#### Section 9: Rollout Strategy & Open Questions *(Standard + Comprehensive tiers)*

- **Phased Rollout:** Feature flag strategy, canary percentages (e.g., 1% → 10% → 50% → 100%), rollback triggers, and kill-switch criteria
- **Open Questions & Assumptions Log:** Unresolved dependencies with Owner, Due Date, Current Assumption, and Impact if Wrong

#### Section 10: Agile User Story Decomposition *(Standard + Comprehensive tiers)*

Decompose the PRD requirements into sprint-ready, vertical Agile User Stories (`US-xxx`) adhering to the **INVEST** criteria. Every story must cut vertically through system layers to deliver testable end-user or system value.

See [Story Decomposition Guide](./references/story-decomposition-guide.md) for vertical slicing techniques, INVEST heuristics, and anti-patterns.

1. **Executive Story Mapping Table:**

| Story ID | Epic / Module | User Story Title | Traced REQ | Traced QA | Sizing | Dependencies |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `US-101` | Checkout | As a shopper, I want dynamic UPI QR... | `REQ-101` | `QA-101` | 3 pts (M) | None |

2. **Detailed Story Specifications:**
Format each story cleanly with strict Gherkin acceptance criteria, metadata, and operational notes:

```markdown
#### US-101: [Imperative Title]
- **As a** <Persona / Role>
- **I want to** <Action / Capability>
- **So that** <Business / User Value>

**Metadata & Traceability:**
- **Module / Epic:** <Module Name>
- **Traced Requirements:** `REQ-xxx`
- **Traced NFRs / Security:** `NFR-xxx`, `SEC-xxx`
- **Traced QA Tests:** `QA-xxx`
- **Complexity Sizing:** <Story Points / T-Shirt Size>
- **Preconditions / Blockers:** <Prerequisite states or stories>

**Acceptance Criteria (Gherkin):**
```gherkin
Scenario: [Happy path scenario]
  Given [precondition]
  When [action]
  Then [expected outcome]
  And [state transition]

Scenario: [Error or timeout scenario]
  Given [failure condition]
  When [action occurs]
  Then [expected error message and compensation action]
```

**Technical & Operational Notes:**
- **Feature Flag:** `<flag_name>`
- **State Transition:** `<FROM_STATE>` ➔ `<TO_STATE>`
- **Side Effects:** DB mutations, events published, webhooks fired

**Definition of Done (Optional — only when confirmed with user):**
- [ ] Unit & integration tests pass with stated coverage threshold
- [ ] Observability fields (`trace_id`, error codes) emitted
- [ ] Zero unmasked PII in application logs
- [ ] Feature flag tested enabled and disabled
```

3. **Tool Gate Output (When Mode C is Selected):**
When the user requests tool integration, generate a companion backlog file alongside the PRD:
- **`docs/backlog-jira.md`:** Formatted with Parent Epic hierarchy, Jira Components, and Labels. Rendered in Markdown (for Jira Cloud) or Wiki Markup (for Jira Data Center) as confirmed during discovery.
- **`docs/backlog-jira.csv` (Optional):** When requested, generates a ready-to-import CSV file containing: `Issue Type,Summary,Description,Parent,Components,Labels,Story Points,Acceptance Criteria` for instant Jira Bulk Import.
- **`docs/backlog-gitlab.md`:** Formatted with Group Epic parent link and executable **GitLab Quick Actions** (`/epic`, `/weight`, `/label ~"region::..." ~"workflow::grooming" ~"type::enhancement"`).
- **`docs/backlog-[tool].md`:** Formatted with the user's custom tags, milestones, and work item hierarchy.

### Phase 3: Self-Audit

Before delivering the PRD, verify every item in this checklist passes internally. Do not declare the PRD complete until all applicable criteria are met:

- **Traceability:** Every `REQ-xxx`, `NFR-xxx`, and `SEC-xxx` is referenced by at least one `QA-xxx` and mapped into at least one `US-xxx` *(Standard + Comprehensive)*
- **INVEST Stories:** Every story is vertically sliced with explicit Gherkin acceptance criteria
- **Interactive DoD Confirmation:** If DoD was included, the proposed checklist was displayed and confirmed by the user first
- **Zero vagueness:** No unquantified adjectives ("fast", "seamless", "robust", "gracefully")
- **Monetary precision:** All currency fields specify storage format and rounding mode
- **State completeness:** Every non-terminal state has a timeout, retry policy, and reconciliation path *(Standard + Comprehensive)*
- **Compliance mapping:** Every identified regulatory requirement maps to a system behavior *(when applicable)*
- **No truncation:** Zero `TODO`, `TBD` (outside Open Questions), `...`, or `[insert here]` placeholders

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "This feature is simple — a high-level summary is enough." | Simple features don't need *long* PRDs, but they still need deterministic validation rules and at least one negative test case. Use the Lightweight tier, not a hand-wave. |
| "I'll provide 3 sample test cases and QA can expand the rest." | Truncated test matrices miss race conditions, callback delays, and boundary rounding bugs. Cover all applicable categories for the PRD tier. Don't use `...` or "etc." in tables. |
| "NFRs and SLAs will be decided by the infra team later." | Untestable NFRs produce unscalable architecture. Propose concrete numerical targets and flag them as assumptions if unconfirmed. |
| "Standard security best practices apply." | "Standard security" can't be audited or tested. Specify what encryption, what masking pattern, what verification mechanism — or explicitly defer to the `security-and-hardening` skill. |
| "The diagram alone shows the flow." | Visual diagrams omit guard conditions, DB side effects, and rollback logic. Always pair a Mermaid diagram with the structured state transition table. |
| "Compliance doesn't apply to this feature." | Validate that claim during discovery. If confirmed, omit the compliance section and note it. Don't silently skip it. |
| "The PRD is too long already." | Length is a symptom, not a problem. If the PRD is long, the feature is complex. Consider splitting into multiple PRDs using a capability map (see `spec-driven-development` Phase 0). |
| "Engineering will write their own user stories later." | Deferring user stories produces misalignment and rework. Slicing requirements into INVEST user stories with Gherkin criteria ensures PM, Dev, and QA agree on scope before sprint planning. |

## Red Flags

- A PRD with a compliance section but no named regulatory body or statute — it's probably boilerplate
- QA test matrix with only happy-path scenarios — negative, boundary, and concurrency cases are missing
- Stories sliced horizontally by technical layer (e.g. "build DB", "build API") instead of vertical customer/system value
- Orphaned user stories that don't trace back to a functional requirement (`REQ-xxx`)
- User stories with vague acceptance criteria instead of testable Gherkin scenarios
- No state transition diagram for a feature with more than two states
- NFRs expressed as adjectives ("high availability", "fast response") instead of numbers
- `TODO` or `TBD` markers outside the dedicated Open Questions table
- Every requirement at the same priority level — no real prioritization was done
- Error handling described as "handle gracefully" or "show appropriate error"
- Monetary values stored as floating-point without specifying precision and rounding
- Compliance requirements copy-pasted from a template without mapping to specific system behaviors
- The PRD prescribes implementation tools (specific databases, vendors, libraries) instead of outcomes and constraints
- A "Comprehensive" tier PRD for a feature that only has 2 requirements — the tier wasn't assessed

## Interaction with Other Skills

- **`interview-me`** — upstream. When the user's intent is still ambiguous, run `interview-me` first to extract confirmed intent. This skill receives that intent and turns it into a structured PRD.
- **`spec-driven-development`** — sibling. `spec-driven-development` produces lightweight engineering specs (objective, commands, project structure, code style, boundaries). This skill produces heavier PRDs with compliance, state machines, QA matrices, and vertical user stories. Use `spec-driven-development` for implementation specs; use this skill for product requirement specs. They can coexist: PRD first (what to build), then engineering spec (how to build it).
- **`idea-refine`** — upstream. If the concept is still vague and needs divergent exploration before converging on requirements, run `idea-refine` first.
- **`security-and-hardening`** — downstream. The PRD's compliance and security requirements inform the threat model and hardening work. Don't duplicate `security-and-hardening` content — reference it.
- **`constraint-driven-development`** — complementary. NFR thresholds defined in the PRD can be recorded as enforceable constraints in `CONSTRAINTS.md`.
- **`documentation-and-adrs`** — downstream. Architectural decisions made during PRD creation should be captured as ADRs.
- **`planning-and-task-breakdown`** — downstream. Once the PRD and stories are approved, use this skill to decompose stories into sprint tasks and dependency graphs.

## Verification

After completing the PRD:

- [ ] PRD tier (Lightweight / Standard / Comprehensive) is stated and justified
- [ ] Every functional requirement has a unique ID (`REQ-xxx`) and a priority level
- [ ] Every requirement is referenced by at least one test case (`QA-xxx`)
- [ ] Every requirement is decomposed into at least one INVEST-compliant user story (`US-xxx`) with Gherkin acceptance criteria (Standard + Comprehensive)
- [ ] Stories are sliced vertically (delivering end-to-end functionality), not horizontally by technical layer
- [ ] If Definition of Done (DoD) was requested, the proposed checklist was displayed and confirmed by the user before inclusion
- [ ] If Tool Gate was requested, companion backlog file (`docs/backlog-[tool].md`) is generated with verified parent/child hierarchy and tool-specific markup/actions (Jira Cloud Markdown vs DC Wiki markup; GitLab Quick Actions & scoped labels; optional Jira CSV table if selected)
- [ ] No unquantified adjectives exist anywhere in the document
- [ ] Monetary fields specify storage format and rounding mode (if applicable)
- [ ] Every non-terminal state has a timeout transition, retry policy, and reconciliation path (Standard + Comprehensive)
- [ ] Applicable compliance requirements are mapped to system behaviors, not stated as generic principles
- [ ] NFRs have concrete numerical targets, not qualitative descriptions
- [ ] Zero `TODO`, `TBD` (outside Open Questions), `...`, or placeholder text exists
- [ ] The human has reviewed and approved the PRD
- [ ] Open questions have assigned owners and due dates
