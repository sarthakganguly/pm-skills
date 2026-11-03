# PM Skills: Bulletproof PRD Engineering

Production-grade Product Management & Specification Engineering skills for AI coding agents (Claude Code, Cursor, Codex, OpenCode, Antigravity, and other agents supporting the open skills standard).

---

## Skills Included

### 1. `prd-engineering`
**Produces engineering- and QA-ready Product Requirement Documents (PRDs)** with deterministic business logic, state machines, pluggable regulatory compliance mapping, a 5-category QA test matrix, and sprint-ready Agile User Story decomposition.

* **Standards-aligned:** Requirement traceability (`REQ-xxx`, `NFR-xxx`, `SEC-xxx`, `QA-xxx`, `US-xxx`).
* **Tiered complexity:** Lightweight (≤3 requirements), Standard (4–15 requirements), and Comprehensive (15+ requirements / regulated).
* **Deterministic logic:** Explicit boundary values, failure paths, monetary storage (`BIGINT`/paise), and Banker's rounding (`HALF_EVEN`).
* **Deterministic State Machines:** Mermaid diagrams paired with state transition tables detailing triggers, guard conditions, side effects, and rollback logic.
* **Pluggable Compliance Modules:** Indian Fintech (RBI Digital Lending Directions 2025, PA Directions 2025, DPDP Act/Rules 2025, CERT-In 2022, UIDAI ADV), extensible to US/EU domains.
* **5-Category QA Matrix:** Positive, Negative, Boundary, Concurrency/Idempotency, and Security/Compliance test cases.
* **Agile Story Decomposition:** INVEST-compliant vertical slicing with Gherkin acceptance criteria (`Given/When/Then`) and tool-gate ready metadata.

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

---

## Directory Structure

```text
pm-skills/
├── LICENSE
├── package.json
├── README.md
└── skills/
    └── prd-engineering/
        ├── SKILL.md
        └── references/
            ├── compliance-indian-fintech.md
            ├── qa-matrix-guide.md
            └── story-decomposition-guide.md
```

---

## License

MIT © [Sarthak Ganguly](https://github.com/sarthakganguly)
