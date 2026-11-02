# Indian Fintech Regulatory & Compliance Baseline Reference

This reference document outlines regulatory baselines across major Indian financial regulatory authorities (RBI, NPCI, SEBI, IRDAI, MeitY, UIDAI). Use this reference when building fintech products targeting the Indian market or operating under Indian regulatory jurisdiction.

---

## 1. Reserve Bank of India (RBI) Regulations

### A. Digital Lending Framework (Consolidated Directions, 2025)
* **Direct Fund Flow:** Disbursal of loans must flow directly from the Regulated Entity's (RE's) bank account to the borrower's verified bank account without pass-through via Lending Service Providers (LSPs) or pooling accounts.
* **Standard Key Fact Statement (KFS):** Must be issued to the borrower prior to execution of the loan contract, displaying Annual Percentage Rate (APR), recovery charges, fee breakdown, and grievance redressal contacts.
* **Cooling-Off / Look-Up Period:** Explicit look-up period (minimum 1 day) enabling borrower exit without prepayment penalty by paying back principal and proportionate APR.
* **Data Privacy & Storage in Lending:** LSPs cannot store borrower biometric data. App permissions must be strictly one-time for camera/microphone (for KYC only); no access to mobile phone resources (contacts, storage, call logs).

### B. Payment Aggregator Framework (PA Directions, 2025)
* **Categorization:** Scope explicitly categorizes PA-Online (PA-O), PA-Physical (PA-P), and PA-Cross Border (PA-CB).
* **Escrow Account Operations:** Strict T+0/T+1 settlement discipline, automated reconciliation, and restriction of non-permissible debits/credits in escrow accounts.
* **Merchant Onboarding & Due Diligence:** Mandatory merchant KYC/CDD, periodic risk profiling, and continuous transaction monitoring for suspicious activity.

### C. Data Localization & Storage of Payment System Data (RBI Directive 2018)
* All payment system providers must ensure that the complete end-to-end transaction data is stored in systems located **only within India**.
* For cross-border transactions, the foreign component can be processed abroad if necessary, but data must be deleted from foreign systems within 24 hours / statutory turnaround, and permanently housed in India.

### D. Card-on-File Tokenization (CoFT)
* Storage of raw 16-digit Primary Account Number (PAN), CVV/CVC, and card expiry date is strictly prohibited for merchants, aggregators, and acquiring banks.
* Only tokenized credentials issued through authorized card networks/issuers may be stored.
* Display masking: Only last 4 digits visible to user and in system logs.

---

## 2. National Payments Corporation of India (NPCI) / UPI

### A. Unified Payments Interface (UPI) Guardrails
* **Idempotency & Replay Prevention:** All payment requests must maintain unique transaction IDs, customer reference numbers (RRN), and strict idempotency handling to prevent double-debit / double-credit.
* **Third-Party Application Providers (TPAP) Compliance:** Adherence to UI guidelines, security controls, and market-share risk monitoring frameworks.
* **UPI AutoPay / Mandates:** Explicit user consent for recurring mandate registration, pre-debit notifications (minimum 24 hours prior to debit), and seamless mandate revocation workflows.

---

## 3. Digital Personal Data Protection (DPDP Act, 2023 & DPDP Rules, 2025)

* **Notice & Consent Architecture:** Consent requests must be accompanied by an itemized notice available in English and the 22 languages specified in the Eighth Schedule to the Indian Constitution.
* **Purpose Limitation & Data Minimization:** Collect only data strictly required for specified purpose. Once purpose is fulfilled, data must be erased unless statutory retention (e.g., PMLA, RBI KYC) mandates otherwise.
* **Consent Manager Integration:** System must support interoperable consent capture, verification, and revocation through registered Consent Managers.
* **Children's Data Protection:** Explicit prohibition on tracking, behavioral monitoring, or targeted advertising directed at children (<18 years); verifiable parental consent required.
* **Data Breach Reporting:** Mandated reporting of personal data breaches to the Data Protection Board of India (DPBI) and affected Data Principals without delay.

---

## 4. CERT-In Cybersecurity Directives (April 2022)

* **6-Hour Incident Notification:** Mandatory reporting of specified cybersecurity incidents (ransomware, unauthorized database access, API abuse, data breaches) to CERT-In within 6 hours of discovery.
* **NTP Clock Synchronization:** All ICT system logs, infrastructure clocks, and server instances must synchronize with National Informatics Centre (NIC) or National Physical Laboratory (NPL) NTP servers.
* **185-Day System Log Retention:** Maintenance of rolling logs for a minimum of 185 days within Indian jurisdiction.

---

## 5. UIDAI & Aadhaar Data Vault (ADV)

* **Zero Storage of Raw Aadhaar:** Storing plaintext 12-digit Aadhaar numbers in general databases or application caches is strictly prohibited.
* **Centralized Aadhaar Data Vault:** Isolated, highly restricted database zone using Hardware Security Modules (HSMs) conforming to FIPS 140-2 Level 3 or higher.
* **UID Tokens & Reference Keys:** Internal application systems must use randomized reference keys/UID tokens generated by UIDAI-compliant tokenization algorithms.
* **Hosting Restrictions:** On-premises, Government Community Cloud (GCC), or UIDAI-empanelled ADV-as-a-Service environments with annual SOC 2 Type II audit.

---

## 6. Capital Markets & Insurance (SEBI & IRDAI)

### A. SEBI Cybersecurity and Cyber Resilience Framework (CSCRF, August 2024)
* Tier-based resilience goals: Anticipate, Withstand, Contain, Recover, Evolve.
* Stringent API security standards, real-time SOC monitoring, and business continuity / disaster recovery drills for Market Infrastructure Institutions (MIIs) and Qualified REs.

### B. IRDAI (Regulatory Sandbox & Digital Guidelines, 2024–2025)
* Principle-based compliance for digital distribution, algorithmic policy issuance, and telematics/IoT data safeguards.
* Strict prohibition of dark patterns in customer journey and pre-selected policy add-ons.

---

## 7. Audit Trail & Statutory Retention Timelines

| Regulatory Mandate | Minimum Retention | Scope |
|---|---|---|
| **PMLA / Prevention of Money Laundering Act** | 5 years | Identity records, transaction ledgers, CDD records after business relationship termination |
| **RBI KYC Master Direction** | 5 years | Customer identification files, video-KYC recordings, address verification docs |
| **CERT-In Directives** | 185 days | ICT logs, access logs, network flow logs, admin access trails |
| **Companies Act / Tax Records** | 8 years | Financial ledgers, invoice logs, tax settlement ledgers |
