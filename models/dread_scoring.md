# DREAD Threat Scoring Matrix
## Application: Nexus Corp Employee Web Portal
## Analyst: James | Case ID: TM-2026-001

---

## What is DREAD?

DREAD is a risk scoring framework used to prioritise threats
identified during threat modeling. Each threat is scored
across 5 dimensions — producing a total risk score from 0–10.

```
D — Damage Potential    How bad is the damage if exploited?
R — Reproducibility     How easy is it to reproduce the attack?
E — Exploitability      How easy is it to launch the attack?
A — Affected Users      How many users are impacted?
D — Discoverability     How easy is it to discover the vulnerability?
```

**Scoring Scale:**
```
0 — No impact / impossible
5 — Moderate impact / possible
10 — Maximum impact / trivial to exploit
```

**Risk Rating:**
```
0–3    Low Risk
4–6    Medium Risk
7–9    High Risk
10     Critical Risk
```

---

## DREAD Scoring — All 11 STRIDE Threats

---

### S-01: User Account Spoofing

| Dimension | Score | Reasoning |
|---|---|---|
| Damage Potential | 8 | Full access to employee PII and payroll data |
| Reproducibility | 7 | Credential stuffing tools are automated and widely available |
| Exploitability | 7 | No MFA = easy — With MFA = harder but phishing bypasses it |
| Affected Users | 6 | One user at a time but can pivot to others |
| Discoverability | 8 | Login pages are public — easily found |
| **Total** | **36/50** | |
| **Average Score** | **7.2** | 🔴 High Risk |

---

### S-02: Session Token Hijacking

| Dimension | Score | Reasoning |
|---|---|---|
| Damage Potential | 8 | Full account takeover without needing credentials |
| Reproducibility | 6 | Requires network position or XSS vulnerability |
| Exploitability | 5 | Needs specific conditions — HTTPS reduces risk |
| Affected Users | 5 | One session at a time |
| Discoverability | 6 | Token in browser storage — findable with DevTools |
| **Total** | **30/50** | |
| **Average Score** | **6.0** | 🟡 Medium Risk |

---

### S-03: API Spoofing

| Dimension | Score | Reasoning |
|---|---|---|
| Damage Potential | 9 | Backend service access — no user interaction needed |
| Reproducibility | 5 | Requires internal network access or token theft |
| Exploitability | 5 | Needs compromised service or stolen JWT |
| Affected Users | 9 | All users — backend access affects entire system |
| Discoverability | 4 | API endpoints not publicly documented |
| **Total** | **32/50** | |
| **Average Score** | **6.4** | 🟡 Medium Risk |

---

### T-01: Database Record Tampering

| Dimension | Score | Reasoning |
|---|---|---|
| Damage Potential | 10 | Payroll fraud — direct financial impact |
| Reproducibility | 5 | Requires database access or SQL injection |
| Exploitability | 5 | Parameterised queries block SQLi — but insider threat is easy |
| Affected Users | 8 | All employees if payroll data modified |
| Discoverability | 4 | Database not directly accessible from internet |
| **Total** | **32/50** | |
| **Average Score** | **6.4** | 🟡 Medium Risk |

---

### T-02: File Storage Tampering

| Dimension | Score | Reasoning |
|---|---|---|
| Damage Potential | 7 | Document loss or modification — business disruption |
| Reproducibility | 7 | Any compromised user account can modify files |
| Exploitability | 7 | No special skills required with valid credentials |
| Affected Users | 8 | All employees who use shared documents |
| Discoverability | 6 | File storage accessible to all authenticated users |
| **Total** | **35/50** | |
| **Average Score** | **7.0** | 🔴 High Risk |

---

### R-01: Admin Action Repudiation

| Dimension | Score | Reasoning |
|---|---|---|
| Damage Potential | 8 | Insider threat actions cannot be proven — no accountability |
| Reproducibility | 6 | Requires admin access — elevated but achievable |
| Exploitability | 5 | Needs admin account — higher barrier |
| Affected Users | 9 | All users if admin disables security or creates backdoors |
| Discoverability | 5 | Hard to discover until incident occurs |
| **Total** | **33/50** | |
| **Average Score** | **6.6** | 🟡 Medium Risk |

---

### I-01: Database Data Exposure

| Dimension | Score | Reasoning |
|---|---|---|
| Damage Potential | 10 | GDPR violation + employee PII exposed — maximum damage |
| Reproducibility | 5 | Requires SQL injection or compromised credentials |
| Exploitability | 5 | Parameterised queries reduce SQLi — but insider easy |
| Affected Users | 10 | All 500 employees — full PII database |
| Discoverability | 5 | Database not internet-facing but accessible internally |
| **Total** | **35/50** | |
| **Average Score** | **7.0** | 🔴 High Risk |

---

### I-02: Error Message Information Leakage

| Dimension | Score | Reasoning |
|---|---|---|
| Damage Potential | 4 | Reveals system info — accelerates attacks but not direct damage |
| Reproducibility | 9 | Trivial — any user can trigger error messages |
| Exploitability | 9 | No skill required — browse to invalid URL |
| Affected Users | 2 | Indirect — helps attacker, not direct user impact |
| Discoverability | 9 | Every user can discover this |
| **Total** | **33/50** | |
| **Average Score** | **6.6** | 🟡 Medium Risk |

---

### D-01: HTTP Flood DDoS

| Dimension | Score | Reasoning |
|---|---|---|
| Damage Potential | 7 | Portal unavailable — business operations disrupted |
| Reproducibility | 8 | DDoS tools freely available — easy to execute |
| Exploitability | 8 | Low skill required — DDoS-as-a-service exists |
| Affected Users | 10 | All 500 employees — portal completely unavailable |
| Discoverability | 10 | Public-facing application — target is obvious |
| **Total** | **43/50** | |
| **Average Score** | **8.6** | 🔴 High Risk |

---

### E-01: Horizontal Privilege Escalation (IDOR)

| Dimension | Score | Reasoning |
|---|---|---|
| Damage Potential | 7 | Access to other employees' personal and payroll data |
| Reproducibility | 8 | Trivial if IDOR exists — change one number in URL |
| Exploitability | 9 | No special tools — browser and curiosity is enough |
| Affected Users | 8 | Any employee's record could be accessed |
| Discoverability | 8 | Easy to find by browsing and testing parameters |
| **Total** | **40/50** | |
| **Average Score** | **8.0** | 🔴 High Risk |

---

### E-02: Vertical Privilege Escalation

| Dimension | Score | Reasoning |
|---|---|---|
| Damage Potential | 10 | Full system compromise — admin access |
| Reproducibility | 5 | Requires broken access control or JWT flaw |
| Exploitability | 5 | Needs specific vulnerability to exist |
| Affected Users | 10 | All 500 employees — full system access |
| Discoverability | 5 | Requires testing admin endpoints |
| **Total** | **35/50** | |
| **Average Score** | **7.0** | 🔴 High Risk |

---

## DREAD Priority Matrix — All Threats Ranked

| Priority | Threat ID | Threat Name | DREAD Score | Risk Level |
|---|---|---|---|---|
| 1 | D-01 | HTTP Flood DDoS | 8.6 | 🔴 High |
| 2 | E-01 | Horizontal Privilege Escalation (IDOR) | 8.0 | 🔴 High |
| 3 | S-01 | User Account Spoofing | 7.2 | 🔴 High |
| 4 | T-02 | File Storage Tampering | 7.0 | 🔴 High |
| 5 | I-01 | Database Data Exposure | 7.0 | 🔴 High |
| 6 | E-02 | Vertical Privilege Escalation | 7.0 | 🔴 High |
| 7 | R-01 | Admin Action Repudiation | 6.6 | 🟡 Medium |
| 8 | I-02 | Error Message Information Leakage | 6.6 | 🟡 Medium |
| 9 | T-01 | Database Record Tampering | 6.4 | 🟡 Medium |
| 10 | S-03 | API Spoofing | 6.4 | 🟡 Medium |
| 11 | S-02 | Session Token Hijacking | 6.0 | 🟡 Medium |

---

## Remediation Roadmap — Prioritised by DREAD Score

### Immediate Action (Score 8+)
```
1. D-01 — Deploy WAF and DDoS protection immediately
2. E-01 — Audit all endpoints for IDOR vulnerabilities
```

### Short Term — Within 30 Days (Score 7–7.9)
```
3. S-01 — Enforce MFA for all user accounts
4. T-02 — Deploy file integrity monitoring
5. I-01 — Implement column-level database encryption
6. E-02 — Audit JWT validation and admin access controls
```

### Medium Term — Within 90 Days (Score 6–6.9)
```
7.  R-01 — Implement immutable audit logging
8.  I-02 — Strip error messages — generic responses only
9.  T-01 — Deploy database activity monitoring
10. S-03 — Implement mTLS between services
11. S-02 — Enforce HTTPS + secure cookie flags
```
