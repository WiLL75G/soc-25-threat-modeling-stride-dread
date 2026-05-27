# STRIDE Threat Analysis
## Application: Nexus Corp Employee Web Portal
## Analyst: James | Case ID: TM-2026-001

---

## What is STRIDE?

STRIDE is a threat modeling framework developed by Microsoft.
It identifies six categories of security threats that every
system must be evaluated against.

```
S — Spoofing
T — Tampering
R — Repudiation
I — Information Disclosure
D — Denial of Service
E — Elevation of Privilege
```

---

## System Being Modelled

**Application:** Nexus Corp Employee Web Portal
**Description:** Internal web application used by 500 employees
to access HR data, payroll information, and company documents.

**Components:**
```
1. Web Browser (Client)
2. Load Balancer
3. Web Application Server
4. Authentication Service (SSO)
5. Database Server (Employee PII + Payroll)
6. File Storage (Company Documents)
7. Admin Panel
8. API Gateway
```

**Data Flow:**
```
User → Browser → Load Balancer → Web App → Auth Service
                                         → Database
                                         → File Storage
                                         → Admin Panel
```

---

## S — Spoofing Threats

**Definition:** Attacker pretends to be someone or something they are not.

---

### Threat S-01: User Account Spoofing
```
Description: Attacker steals or guesses user credentials and
             logs in as a legitimate employee

Attack vector: Phishing, brute force, credential stuffing

Affected component: Authentication Service

Impact: Attacker accesses HR data, payroll, and documents
        as the victim user

Evidence in logs:
- Login from new IP or location
- Impossible travel alert
- Multiple failed attempts before success
```

**Mitigation:**
```
✅ MFA mandatory for all users
✅ Risk-based conditional access
✅ Account lockout after 5 failed attempts
✅ Login anomaly alerts in SIEM
```

---

### Threat S-02: Session Token Hijacking
```
Description: Attacker steals an active session token and
             impersonates a logged-in user without credentials

Attack vector: XSS, man-in-the-middle, network sniffing

Affected component: Web Application Server

Impact: Full account takeover without needing credentials
        Session persists until token expires
```

**Mitigation:**
```
✅ HTTPS enforced on all connections
✅ Secure and HttpOnly cookie flags
✅ Short session timeout — 30 minutes
✅ Session token rotation after privilege change
```

---

### Threat S-03: API Spoofing
```
Description: Attacker sends forged API requests impersonating
             a legitimate service or internal component

Attack vector: Compromised service, forged JWT tokens

Affected component: API Gateway

Impact: Unauthorised access to backend services
        Data extracted without user interaction
```

**Mitigation:**
```
✅ Mutual TLS (mTLS) between services
✅ JWT token signature validation
✅ API key rotation every 90 days
✅ API rate limiting and anomaly detection
```

---

## T — Tampering Threats

**Definition:** Attacker modifies data or code without authorisation.

---

### Threat T-01: Database Record Tampering
```
Description: Attacker modifies payroll or HR records in the database

Attack vector: SQL injection, compromised admin account,
               direct database access

Affected component: Database Server

Impact: Salary manipulation, HR record falsification,
        compliance violations, financial fraud
```

**Mitigation:**
```
✅ Parameterised queries — prevent SQL injection
✅ Database activity monitoring (DAM)
✅ Write operations logged and alerted
✅ Database access requires Tier 1 approval
✅ Regular database integrity checks
```

---

### Threat T-02: File Storage Tampering
```
Description: Attacker modifies or deletes company documents
             in file storage

Attack vector: Compromised user account, insider threat,
               misconfigured permissions

Affected component: File Storage

Impact: Data integrity loss, compliance violations,
        business process disruption
```

**Mitigation:**
```
✅ File integrity monitoring (FIM)
✅ Immutable backups — cannot be deleted by users
✅ Version control on all critical documents
✅ Alerts on bulk file deletion or modification
```

---

## R — Repudiation Threats

**Definition:** User denies performing an action — no proof it happened.

---

### Threat R-01: Admin Action Repudiation
```
Description: Admin performs a malicious action and claims
             they never did it — no audit trail exists

Attack vector: Shared admin accounts, disabled logging,
               log tampering

Affected component: Admin Panel

Impact: Inability to prove malicious insider activity
        Compliance violations — SOX, GDPR, HIPAA
```

**Mitigation:**
```
✅ Individual admin accounts — no shared credentials
✅ Immutable audit logs — cannot be modified or deleted
✅ All admin actions logged with timestamp and user ID
✅ Log forwarding to SIEM — logs exist outside the app
✅ Digital signatures on critical log entries
```

---

## I — Information Disclosure Threats

**Definition:** Sensitive data is exposed to unauthorised parties.

---

### Threat I-01: Database Data Exposure
```
Description: Attacker extracts employee PII or payroll data
             from the database

Attack vector: SQL injection, compromised credentials,
               misconfigured database permissions

Affected component: Database Server

Impact: GDPR violation, employee PII exposed,
        regulatory fines, reputational damage
```

**Mitigation:**
```
✅ Encryption at rest — AES-256
✅ Column-level encryption for PII fields
✅ Database access restricted to application service account
✅ No direct user access to database layer
✅ DLP alerts on bulk data queries
```

---

### Threat I-02: Error Message Information Leakage
```
Description: Application error messages reveal internal
             system details — stack traces, file paths,
             database schema, version numbers

Attack vector: Intentional error triggering, fuzzing

Affected component: Web Application Server

Impact: Attacker learns system architecture
        Accelerates targeted attack planning
```

**Mitigation:**
```
✅ Generic error messages shown to users
✅ Detailed errors logged internally only
✅ Application version numbers not exposed
✅ HTTP headers stripped — no server version leakage
```

---

## D — Denial of Service Threats

**Definition:** Attacker makes the system unavailable to legitimate users.

---

### Threat D-01: HTTP Flood DDoS
```
Description: Attacker floods the web application with HTTP
             requests — overwhelming the server

Attack vector: Botnet, amplification attack, volumetric flood

Affected component: Load Balancer, Web Application Server

Impact: Portal unavailable — employees cannot access HR
        or payroll systems. Business operations disrupted.
```

**Mitigation:**
```
✅ Web Application Firewall (WAF)
✅ Rate limiting — 100 requests per minute per IP
✅ CDN with DDoS protection (Cloudflare, AWS Shield)
✅ Auto-scaling to absorb traffic spikes
✅ SIEM alert — traffic anomaly detection
```

---

## E — Elevation of Privilege Threats

**Definition:** Attacker gains higher permissions than authorised.

---

### Threat E-01: Horizontal Privilege Escalation
```
Description: Standard user accesses another user's data
             by manipulating request parameters

Attack vector: Insecure Direct Object Reference (IDOR)
               Example: /profile?user_id=1234
               Attacker changes to: /profile?user_id=1235

Affected component: Web Application Server

Impact: Employee A accesses Employee B's payroll data
```

**Mitigation:**
```
✅ Server-side authorisation checks on every request
✅ Never trust client-supplied user IDs
✅ Object-level access control validation
✅ SIEM alert — user accessing other users' records
```

---

### Threat E-02: Vertical Privilege Escalation
```
Description: Standard user gains admin-level access

Attack vector: Broken access control, JWT manipulation,
               misconfigured roles

Affected component: Admin Panel, Authentication Service

Impact: Full system compromise — attacker can modify
        any record, disable logging, create backdoors
```

**Mitigation:**
```
✅ Role verification on every admin endpoint — server side
✅ JWT claims validation — reject tampered tokens
✅ Admin actions require re-authentication
✅ SIEM alert — standard user accessing admin endpoints
```

---

## STRIDE Coverage Summary

| Category | Threats Identified | Mitigations Produced |
|---|---|---|
| Spoofing | 3 | 12 controls |
| Tampering | 2 | 10 controls |
| Repudiation | 1 | 5 controls |
| Information Disclosure | 2 | 10 controls |
| Denial of Service | 1 | 5 controls |
| Elevation of Privilege | 2 | 10 controls |
| **Total** | **11** | **52 controls** |
