# PM Skills: Bulletproof PRD Engineering

> **Production-grade Product Management & Specification Engineering skills for AI coding agents.**  
> Built for [Claude Code](https://claude.ai/code), [Cursor](https://cursor.com), [Codex](https://github.com/openai/codex), [OpenCode](https://github.com/opencode-ai), [Google Antigravity](https://deepmind.google), and all coding assistants supporting the open [Agent Skills standard](https://skills.sh).

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Skills: Agent-Skills](https://img.shields.io/badge/Skills-Open%20Standard-success.svg)](https://skills.sh)

---

## Why PM Skills?

Most AI-generated Product Requirement Documents (PRDs) fail when handed to engineering and QA teams because they:
1. **Rely on vague adjectives** (*"fast"*, *"seamless"*, *"graceful"*, *"robust"*) instead of quantified SLAs.
2. **Omit edge cases and lifecycle states**, causing race conditions and webhook callback mismatches in production.
3. **Generate hallucinated or outdated regulatory boilerplate** instead of mapping exact statutory mandates to system behaviors.
4. **Leave requirements at document level**, requiring hours of manual work to translate into Jira or GitLab sprint backlogs.

**`pm-skills` eliminates this gap.** It teaches AI coding agents to think like veteran Principal Product Managers and Solutions Architects, producing airtight specifications where every requirement is deterministic, auditable, and directly convertible into automated test suites and sprint tickets.

---

## Core Capabilities

```
  DISCOVER           SPECIFY           VERIFY             DECOMPOSE           TOOL GATE
 ┌─────────┐      ┌───────────┐      ┌──────────┐      ┌─────────────┐      ┌───────────┐
 │ Tiers & │ ───▶ │10-Section │ ───▶ │ 5-Axis QA│ ───▶ │   INVEST    │ ───▶ │Jira/GitLab│
 │ Scoping │      │State Table│      │  Matrix  │      │User Stories │      │Companion  │
 └─────────┘      └───────────┘      └──────────┘      └─────────────┘      └───────────┘
```

### 1. Bidirectional Traceability (ISO/IEC/IEEE 29148)
Every requirement receives a unique identifier that traces through the entire development lifecycle:
* **`REQ-xxx`**: Functional business requirements and deterministic validation formulas.
* **`NFR-xxx`**: Concrete, testable non-functional thresholds (e.g., `< 200ms` p95 latency, `RPO = 0`).
* **`SEC-xxx`**: Security and statutory controls (encryption at rest, masking patterns, audit logs).
* **`QA-xxx`**: Explicit test cases covering happy path, boundaries, concurrency, and security.
* **`US-xxx`**: Sprint-ready Agile User Stories with Gherkin acceptance criteria.

### 2. Tiered Complexity Sizing
Not every feature needs a 20-page document. The skill assesses complexity up front:
* **Lightweight Tier (≤ 3 requirements):** Lean functional spec for simple features.
* **Standard Tier (4–15 requirements):** Complete PRD including state machine, NFRs, QA matrix, and user stories.
* **Comprehensive Tier (15+ requirements / Regulated):** Full enterprise PRD including RBAC matrices, compliance modules, reconciliation loops, and companion backlogs.

### 3. Deterministic State Machines & Rollback Tables
Every multi-state lifecycle is mapped with:
* Fenced **Mermaid diagrams** (`stateDiagram-v2` or `sequenceDiagram`).
* Structured **State Transition Tables** with columns: `Current State | Trigger/Event | Guard Condition | Next State | Side Effects | Rollback/Failure Action`.
* Complete separation of terminal (`SUCCESS`, `FAILED`, `REVERSED`) and non-terminal pending states (`INITIATED`, `PENDING_WEBHOOK`), ensuring timeouts and recovery loops are strictly defined.

### 4. Pluggable Compliance Baselines (2025–2026 Standards)
Includes pre-built, production-audited regulatory references for high-stakes domains (with Indian Fintech as a portable specialization):
* **RBI Digital Lending Directions (2025):** Direct fund flows, standardized Key Fact Statements (KFS), look-up cooling periods, LSP data minimization.
* **RBI Payment Aggregator Directions (2025):** PA-Online, PA-Physical, and PA-Cross Border rules, escrow settlement discipline.
* **Data Localization & Tokenization:** RBI 2018 payment data storage directive, Card-on-File Tokenization (CoFT), and UIDAI Aadhaar Data Vault (ADV Circular 8 of 2025) with HSM (FIPS 140-2 Level 3) requirements.
* **DPDP Act (2023) & DPDP Rules (2025):** Itemized notice/consent workflows, purpose limitation, Consent Manager integration, child data safeguards.
* **CERT-In (2022):** Mandatory 6-hour cybersecurity incident reporting, NTP clock synchronization, and 185-day system log retention.
* **SEBI CSCRF (2024):** Cybersecurity & Cyber Resilience Framework for capital markets.

### 5. 5-Category QA Matrix & Edge Case Catalog
A structured test matrix ready for QA automation covering all five distinct vectors:
1. **Positive (Happy Path):** Core functional flow.
2. **Negative (Validation/Auth):** Tampered tokens, expired sessions, invalid payloads.
3. **Boundary & Edge Cases:** Monetary limits, ₹0.01 smallest units, Banker's rounding (`HALF_EVEN`).
4. **Concurrency & Idempotency:** Rapid double-clicks (same idempotency key within 10ms), out-of-order webhooks, upstream gateway timeouts (`504`) with delayed callbacks.
5. **Security & Compliance:** PII leakage checks in logs/APM, unauthenticated HMAC webhooks, replay attacks.

### 6. Agile User Story Decomposition (INVEST Standard)
Slices features **vertically** (delivering end-to-end functionality across UI, API, and DB in each slice) rather than horizontally:
* Standard narrative format: *As a `<persona>`, I want `<capability>`, so that `<benefit>`*.
* Explicit Gherkin Acceptance Criteria (`Scenario`, `Given`, `When`, `Then`, `And`).
* Preconditions, complexity sizing (story points / T-shirt sizes), and feature flag tags.

### 7. Optional Tool Gate (Jira & GitLab Backlog Generation)
Decomposes epics into parent/child issue hierarchies and outputs dedicated companion backlog files:
* **Jira Support:**
  * Asks whether your team uses **Jira Cloud** (Standard Markdown) or **Jira Data Center** (Wiki Markup `h2.`, `{code}`).
  * Formats parent epics using modern Jira **`Parent`** field semantics.
  * Embeds Jira Components and Labels.
  * **Optional Bulk CSV (`docs/backlog-jira.csv`):** Generates ready-to-import CSV files for Jira's web importer.
* **GitLab Support (`docs/backlog-gitlab.md`):**
  * Group Epic ➔ Project Issue hierarchy.
  * Scoped labels (`region::*`, `workflow::grooming`, `triage::pending`, `type::enhancement`).
  * Executable **GitLab Quick Actions** (`/epic`, `/weight`, `/label`) for instant copy-pasting into GitLab.
* **Generic Issue Trackers (`docs/backlog-[tool].md`):** Custom tags and milestone formatting for Linear, GitHub Issues, etc.

### 8. Interactive Definition of Done (DoD) Gate
Quality gates are **never silently assumed**. If a Definition of Done is requested, the skill presents a baseline checklist to the user for review and customization (code coverage thresholds, telemetry events, zero PII logging, feature flag verification) before embedding.

---

## Installation via CLI

Install using the open `skills` CLI directly into your AI coding assistant:

### Install all skills in this package:
```bash
npx skills add sarthakganguly/pm-skills
```

### Install only `prd-engineering`:
```bash
npx skills add sarthakganguly/pm-skills --skill prd-engineering
```

### Install globally across all projects:
```bash
npx skills add sarthakganguly/pm-skills -g
```

### Updating to the Latest Version:
```bash
npx skills update prd-engineering
```

---

## Directory Structure & Architecture

```text
pm-skills/
├── LICENSE                                    # MIT Open Source License
├── package.json                               # npm & Agent Skills discovery metadata
├── README.md                                  # Documentation & capability reference
└── skills/
    └── prd-engineering/
        ├── SKILL.md                           # Main skill instructions & execution workflow
        └── references/                        # Portable reference modules (loaded on demand)
            ├── compliance-indian-fintech.md   # Statutory baseline (RBI, NPCI, DPDP, CERT-In, UIDAI)
            ├── qa-matrix-guide.md             # 5-category test matrix & distributed edge cases
            └── story-decomposition-guide.md   # INVEST vertical slicing, Jira/GitLab Tool Gate, DoD
```

### Architecture Principles:
* **Progressive Disclosure:** Reference guides live inside `references/` and are loaded by the agent only when needed, preserving context window budget and token efficiency.
* **Portability:** Placing `references/` directly within `skills/prd-engineering/` ensures that when someone installs the skill via `npx skills add ... --skill prd-engineering`, all supporting guides travel with the skill.
* **Zero Model Bias:** Written with model-neutral process guidelines rather than model-specific prompt tricks.

---

## Example Invocations

You can invoke this skill across various development scenarios:

* *"Draft a comprehensive PRD for our new UPI AutoPay recurring payment mandate flow."*
* *"Create an engineering spec for dynamic QR code generation during checkout, with Jira Cloud story decomposition."*
* *"Audit this draft PRD for regulatory compliance gaps under Indian DPDP Act and RBI guidelines."*
* *"Generate a lightweight PRD and QA test matrix for our webhook retry mechanism."*

---

## Contributing

Contributions, domain extensions (e.g., US Healthcare/HIPAA compliance modules, EU MiCA modules), and improvements are welcome! Please open an issue or pull request.

---

## License

MIT © [Sarthak Ganguly](https://github.com/sarthakganguly)
