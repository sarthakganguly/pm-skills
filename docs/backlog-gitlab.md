# GitLab Backlog: CloudPulse Analytics Dashboard (v1.2)

> **Parent Epic:** `&"CloudPulse Analytics Dashboard v1.2"`  
> **Source PRD:** [`PRD-CP-2026-009`](./PRD-CP-2026-009-cloudpulse-analytics.md)  
> **Default Scoped Labels:** `~"region::global"`, `~"workflow::ready-for-development"`, `~"triage::accepted"`, `~"type::feature"`, `~"security::soc2"`

This companion backlog contains sprint-ready, vertically sliced GitLab issues embedded with executable **GitLab Quick Actions**. Developers and product owners can copy and paste any issue block directly into a new GitLab issue description.

---

## Issue 1: [US-AUTH-101] Enterprise SAML 2.0 & OAuth2 SSO Login Flow

/epic &"CloudPulse Analytics Dashboard v1.2"
/weight 5
/label ~"region::global" ~"workflow::ready-for-development" ~"triage::accepted" ~"type::feature" ~"priority::high" ~"security::soc2"

### Description
**As a** DevOps Engineer or FinOps Manager  
**I want to** authenticate into CloudPulse via our corporate Okta SAML 2.0 or Google Workspace SSO  
**So that** I don't need to manage separate application passwords and our IT team maintains centralized access control.

### Acceptance Criteria (Gherkin)
```gherkin
Scenario: Successful SAML 2.0 SSO authentication via Okta
  Given an enterprise user belongs to a domain with Okta SSO configured
  When the user initiates login and provides valid Okta identity credentials
  Then the system verifies the SAML assertion signature and clock skew within 60 seconds
  And issues a signed RS256 JWT with an 8-hour expiry
  And records an immutable audit log entry "SSO_LOGIN"
  And redirects the user to the CloudPulse dashboard within 1.5 seconds

Scenario: Rejection of replayed SAML assertion
  Given an authenticated SAML assertion has already been processed by CloudPulse
  When an attacker submits the identical Assertion ID to "/api/v1/auth/sso/saml/callback"
  Then the system detects the duplicate ID in the distributed cache
  And returns HTTP 401 Unauthorized with code "AUTH_SAML_REPLAY_DETECTED"
  And records a security alert log with the client IP address
```

### Traceability & Operational Notes
- **Parent Requirements:** `REQ-AUTH-01`, `REQ-SEC-01` (TLS 1.3)
- **Verification Tests:** `QA-AUTH-01`, `QA-AUTH-02`
- **State Transition:** `UNAUTHENTICATED` ➔ `AUTHENTICATED`
- **Feature Flag:** `auth_sso_saml_v1`
- **Dependencies:** None

### Definition of Done Checklist
- [ ] Unit & integration tests pass with $\ge 80\%$ code coverage
- [ ] Gherkin acceptance criteria verified in test environment
- [ ] Distributed tracing tags (`trace_id`, `span_id`, error codes) emitted
- [ ] Zero unmasked credentials / tokens in application logs
- [ ] Feature flag tested in both enabled and disabled states
- [ ] API schemas conform to OpenAPI 3.0 specification

---

## Issue 2: [US-AUTH-102] Enforced MFA Enrollment & Challenge for Admin & FinOps Roles

/epic &"CloudPulse Analytics Dashboard v1.2"
/weight 5
/label ~"region::global" ~"workflow::ready-for-development" ~"triage::accepted" ~"type::feature" ~"priority::high" ~"security::soc2"

### Description
**As a** System Administrator or FinOps Manager  
**I want to** be prompted for a TOTP verification code during login and forced to register MFA if not enrolled  
**So that** privileged financial and administrative capabilities are shielded from credential compromise.

### Acceptance Criteria (Gherkin)
```gherkin
Scenario: Forced MFA enrollment on first login for privileged role
  Given an enterprise user has been granted the "FinOps Manager" role
  And the user has not yet registered an MFA factor
  When the user authenticates via SSO
  Then the system restricts access to all dashboard APIs
  And displays the mandatory MFA enrollment screen with a QR code and secret key
  When the user inputs a valid 6-digit TOTP code
  Then the system registers the TOTP factor, generates 8 backup codes, and grants access

Scenario: Temporary account lockout after 5 consecutive invalid MFA entries
  Given a user is on the MFA verification prompt
  When the user submits 5 consecutive incorrect TOTP codes within 5 minutes
  Then the system locks the account for 15 minutes
  And returns HTTP 423 Locked with error code "AUTH_ACCOUNT_LOCKED_MFA_ATTEMPTS"
  And logs an audit event "MFA_LOCKOUT"
```

### Traceability & Operational Notes
- **Parent Requirements:** `REQ-AUTH-02`, SOC 2 CC6.8, ISO 27001 A.9.4.2
- **Verification Tests:** `QA-MFA-01`, `QA-MFA-02`
- **State Transition:** `PRE_MFA_AUTHENTICATED` ➔ `MFA_VERIFIED` (or `LOCKED`)
- **Feature Flag:** `auth_mfa_enforcement_v1`
- **Dependencies:** `US-AUTH-101`

### Definition of Done Checklist
- [ ] Unit & integration tests pass with $\ge 80\%$ code coverage
- [ ] Gherkin acceptance criteria verified in test environment
- [ ] Distributed tracing tags (`trace_id`, `span_id`, error codes) emitted
- [ ] Zero unmasked credentials / tokens in application logs
- [ ] Feature flag tested in both enabled and disabled states
- [ ] API schemas conform to OpenAPI 3.0 specification

---

## Issue 3: [US-MON-103] Multi-Cloud Real-Time Telemetry Dashboard (15s Refresh)

/epic &"CloudPulse Analytics Dashboard v1.2"
/weight 8
/label ~"region::global" ~"workflow::ready-for-development" ~"triage::accepted" ~"type::feature" ~"priority::high" ~"performance::p95"

### Description
**As a** DevOps Engineer  
**I want to** view CPU, RAM, and Network I/O metrics across AWS and GCP instances with updates $\le 15$ seconds  
**So that** I can detect infrastructure performance bottlenecks in real time from a single dashboard.

### Acceptance Criteria (Gherkin)
```gherkin
Scenario: Real-time telemetry streaming update
  Given an active session on the CloudPulse telemetry dashboard
  When 15 seconds elapse
  Then the client receives the latest metric snapshot via SSE/WebSocket
  And the CPU, RAM, and Network I/O charts update smoothly without a page reload
  And client-side render completion takes less than 200 milliseconds

Scenario: Fallback to polling when SSE connection drops
  Given a user is streaming telemetry metrics via Server-Sent Events
  When the network connection drops the SSE channel
  Then the client automatically initiates HTTP polling every 15,000 milliseconds
  And attempts background reconnection of the SSE stream using exponential backoff
```

### Traceability & Operational Notes
- **Parent Requirements:** `REQ-MON-01`, `REQ-NFR-01` (< 1.5s load), `REQ-NFR-02` (50k events/sec)
- **Verification Tests:** `QA-MON-01`, `QA-MON-02`, `QA-MON-03`
- **State Transition:** `DISCONNECTED` ➔ `STREAMING` (or `POLLING_FALLBACK`)
- **Feature Flag:** `monitoring_streaming_telemetry_v1`
- **Dependencies:** AWS & GCP cloud read credentials registered

### Definition of Done Checklist
- [ ] Unit & integration tests pass with $\ge 80\%$ code coverage
- [ ] Gherkin acceptance criteria verified in test environment
- [ ] Distributed tracing tags (`trace_id`, `span_id`, error codes) emitted
- [ ] Zero unmasked credentials / tokens in application logs
- [ ] Feature flag tested in both enabled and disabled states
- [ ] API schemas conform to OpenAPI 3.0 specification

---

## Issue 4: [US-MON-104] Multi-Dimensional Filter Engine (Region, Service, Tags)

/epic &"CloudPulse Analytics Dashboard v1.2"
/weight 5
/label ~"region::global" ~"workflow::ready-for-development" ~"triage::accepted" ~"type::feature" ~"priority::medium"

### Description
**As a** DevOps Engineer or FinOps Manager  
**I want to** filter telemetry metrics simultaneously by cloud region, service type, and custom tags  
**So that** I can isolate performance and cost anomalies to specific microservices or deployment environments.

### Acceptance Criteria (Gherkin)
```gherkin
Scenario: Filtering metrics across multiple compound dimensions
  Given the telemetry dashboard is rendering metrics from AWS and GCP
  When the user selects Region "us-east-1", Service "EC2", and Tag "env:production"
  Then the dashboard renders only EC2 instances located in us-east-1 with the env:production tag
  And updates the chart views within 50 milliseconds for currently loaded data

Scenario: Handling empty filter results gracefully
  Given the user applies a tag filter "env:non-existent"
  When no instances match the applied criteria
  Then the dashboard displays an empty-state message: "No instances found matching the selected filters"
  And provides a one-click button to reset active filters
```

### Traceability & Operational Notes
- **Parent Requirements:** `REQ-MON-02`, `NFR-PERF-01`
- **Verification Tests:** `QA-FILT-01`, `QA-FILT-02`
- **State Transition:** `UNFILTERED_VIEW` ➔ `FILTERED_VIEW`
- **Feature Flag:** `monitoring_filtering_engine_v1`
- **Dependencies:** `US-MON-103`

### Definition of Done Checklist
- [ ] Unit & integration tests pass with $\ge 80\%$ code coverage
- [ ] Gherkin acceptance criteria verified in test environment
- [ ] Distributed tracing tags (`trace_id`, `span_id`, error codes) emitted
- [ ] Zero unmasked credentials / tokens in application logs
- [ ] Feature flag tested in both enabled and disabled states
- [ ] API schemas conform to OpenAPI 3.0 specification

---

## Issue 5: [US-COST-105] Automated Daily Spend Anomaly Detection & Dispatch

/epic &"CloudPulse Analytics Dashboard v1.2"
/weight 8
/label ~"region::global" ~"workflow::ready-for-development" ~"triage::accepted" ~"type::feature" ~"priority::high" ~"security::soc2"

### Description
**As a** FinOps Manager  
**I want to** receive immediate email and webhook notifications when daily cloud spend breaches our baseline by > 20%  
**So that** our team can stop accidental cloud cost runaways before they impact monthly budgets.

### Acceptance Criteria (Gherkin)
```gherkin
Scenario: Spend breach triggers email and webhook notifications
  Given an account has a 14-day rolling baseline of $1,000.00 USD
  When hourly spend evaluation projects daily spend of $1,250.00 USD (a 25.00% breach)
  Then the system records an anomaly in state "ALERT_TRIGGERED"
  And dispatches an SMTP email alert containing the breach amount and contributing services
  And posts an HMAC-SHA256 signed JSON payload to the configured webhook URL within 60 seconds

Scenario: Webhook delivery failure and exponential backoff
  Given a cost anomaly alert has been triggered
  When the customer webhook endpoint responds with HTTP 500 or times out
  Then the notification worker retries delivery up to 5 times at intervals of 30s, 60s, 120s, 240s, 480s
  And marks the notification status as "DELIVERY_FAILED" if all 5 retries fail
```

### Traceability & Operational Notes
- **Parent Requirements:** `REQ-COST-01`, `REQ-SEC-01`, `REQ-SEC-02`, `NFR-ALERT-01`
- **Verification Tests:** `QA-COST-01`, `QA-COST-02`, `QA-COST-03`, `QA-SEC-01`, `QA-SEC-02`
- **State Transition:** `NORMAL` ➔ `EVALUATING` ➔ `ALERT_TRIGGERED`
- **Feature Flag:** `cost_anomaly_evaluator_v1`
- **Dependencies:** `US-AUTH-101`, billing ingestion active

### Definition of Done Checklist
- [ ] Unit & integration tests pass with $\ge 80\%$ code coverage
- [ ] Gherkin acceptance criteria verified in test environment
- [ ] Distributed tracing tags (`trace_id`, `span_id`, error codes) emitted
- [ ] Zero unmasked credentials / tokens in application logs
- [ ] Feature flag tested in both enabled and disabled states
- [ ] API schemas conform to OpenAPI 3.0 specification

---

## Issue 6: [US-COST-106] Notification Center Anomaly Lifecycle: Acknowledge & Snooze

/epic &"CloudPulse Analytics Dashboard v1.2"
/weight 5
/label ~"region::global" ~"workflow::ready-for-development" ~"triage::accepted" ~"type::feature" ~"priority::high" ~"security::soc2"

### Description
**As a** FinOps Manager or DevOps Engineer  
**I want to** acknowledge or snooze active cost anomalies directly from the Notification Center  
**So that** expected spend spikes (such as load tests) do not trigger repetitive alerts.

### Acceptance Criteria (Gherkin)
```gherkin
Scenario: Snoozing an active anomaly for 4 hours with audit justification
  Given an anomaly is displayed in the Notification Center with state "ALERT_TRIGGERED"
  When the FinOps user selects "Snooze for 4 hours" and enters reason "Running load test for release v1.2"
  Then the system transitions the anomaly state to "SNOOZED"
  And suppresses all alert dispatches until the 4-hour snooze expires
  And creates an immutable entry in the audit log with actor ID, reason, and timestamp

Scenario: Rejection of snooze action with insufficient explanation
  Given an active anomaly in state "ALERT_TRIGGERED"
  When a user submits a snooze request with a reason string containing fewer than 10 characters
  Then the system rejects the mutation with HTTP 400 Bad Request
  And displays an error: "Reason description must contain at least 10 characters."
  And the anomaly remains in state "ALERT_TRIGGERED"
```

### Traceability & Operational Notes
- **Parent Requirements:** `REQ-COST-02`, `NFR-CONC-01` (optimistic locking), `NFR-IDEMP-01`
- **Verification Tests:** `QA-SNOOZ-01`, `QA-SNOOZ-02`, `QA-SNOOZ-03`
- **State Transition:** `ALERT_TRIGGERED` ➔ `SNOOZED` (or `ACKNOWLEDGED`)
- **Feature Flag:** `cost_anomaly_lifecycle_v1`
- **Dependencies:** `US-COST-105`

### Definition of Done Checklist
- [ ] Unit & integration tests pass with $\ge 80\%$ code coverage
- [ ] Gherkin acceptance criteria verified in test environment
- [ ] Distributed tracing tags (`trace_id`, `span_id`, error codes) emitted
- [ ] Zero unmasked credentials / tokens in application logs
- [ ] Feature flag tested in both enabled and disabled states
- [ ] API schemas conform to OpenAPI 3.0 specification
