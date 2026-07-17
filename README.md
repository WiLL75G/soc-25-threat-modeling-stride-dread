# Threat Modeling with STRIDE and DREAD

Thinking like an attacker before the thing exists. Eleven threats found across a web portal, scored, ranked, and turned into a remediation order that spends the budget in the right sequence.

## At a Glance

| Field | Detail |
| --- | --- |
| Work Type | Threat modeling and risk scoring |
| Frameworks | STRIDE, DREAD |
| Target | Employee web portal, 500 users, fictional |
| Components Modelled | 8 |
| Threats Identified | 11 across all 6 STRIDE categories |
| High Risk | 6 |
| Top Threats | HTTP flood DDoS at 8.6, IDOR privilege escalation at 8.0 |

## What This Is

Threat modeling is the work that happens before there is anything to defend.

Most security is reactive by structure. An alert fires, an analyst responds, a rule gets written. All of it happens after the vulnerability exists. Threat modeling asks the question at design time, when a fix costs a conversation instead of an incident.

Scope stated plainly: this is a paper exercise against a fictional application. No code was tested, no scan was run, nothing was exploited. What it demonstrates is systematic threat enumeration and the ability to turn a threat list into a defensible remediation order.

## Why STRIDE

Six categories, and the point is completeness rather than depth.

Left to instinct, everyone models the same three threats. Credential theft, SQL injection, malware. STRIDE forces the question in categories people skip, and the skipped ones are where the surprises live.

Spoofing, pretending to be someone.
Tampering, changing data.
Repudiation, denying you did it.
Information disclosure, seeing what you should not.
Denial of service, making it unavailable.
Elevation of privilege, becoming someone bigger.

Repudiation is the one nobody models. It is not a breach, it is a gap in the record, and it only becomes a problem when an auditor or a lawyer asks who did this and the system cannot say.

## Threats Identified

### Spoofing

S-01, user account spoofing via credential theft. DREAD 7.2, high.
S-02, session token hijacking via XSS or MITM. DREAD 6.0.
S-03, API spoofing via forged JWT. DREAD 6.4.

### Tampering

T-01, database record tampering, payroll fraud. DREAD 6.4.
T-02, file storage tampering, document deletion. DREAD 7.0, high.

### Repudiation

R-01, admin action repudiation, no audit trail. DREAD 6.6.

### Information Disclosure

I-01, database PII exposure, GDPR exposure. DREAD 7.0, high.
I-02, error message information leakage. DREAD 6.6.

### Denial of Service

D-01, HTTP flood DDoS, portal unavailable. DREAD 8.6, high.

### Elevation of Privilege

E-01, horizontal privilege escalation via IDOR. DREAD 8.0, high.
E-02, vertical privilege escalation to admin. DREAD 7.0, high.

## Why DREAD

Damage, reproducibility, exploitability, affected users, discoverability.

A threat list is not a plan. Eleven threats and a finite budget is a prioritisation problem, and "this one feels worse" is not something you can take to a stakeholder.

DREAD makes the argument arithmetic. It also produces answers that contradict intuition, which is the point of scoring rather than guessing.

### The Top Three

**D-01, HTTP flood DDoS, 8.6**

All 500 users affected. Tooling is free. The target is public by definition, so discoverability is maximal.

This scoring highest is the finding. Nobody's instinct puts DDoS above credential theft, because DDoS is unglamorous and does not steal anything. But it hits every user, requires no skill, and cannot be hidden from. Intuition ranks by sophistication. DREAD ranks by consequence, and the two disagree.

**E-01, IDOR privilege escalation, 8.0**

Any employee reaches any other employee's data by changing a number in a URL.

The exploitability score is what carries it. This is not an attack, it is a discovery. No tooling, no skill, no intent required — someone edits a URL out of curiosity and is now looking at payroll. The vulnerabilities that score highest are usually the ones that need nothing.

**S-01, account spoofing, 7.2**

Full PII and payroll access. Phishing has high reproducibility, and it works on people rather than systems.

## Remediation Order

Ordered by score, not by category.

### Immediate, 8 and above

Deploy WAF and DDoS protection. Blocks D-01.
Audit every endpoint for IDOR. Blocks E-01.

### 30 days, 7 to 7.9

Enforce MFA. Blocks S-01.
File integrity monitoring. Blocks T-02.
Column level database encryption. Blocks I-01.
JWT validation audit. Blocks E-02.

### 90 days, 6 to 6.9

Immutable audit logging. Blocks R-01.
Generic error messages. Blocks I-02.
Database activity monitoring. Blocks T-01.
mTLS between services. Blocks S-03.
HTTPS with secure cookies. Blocks S-02.

The IDOR audit is the highest leverage item on the list. It costs a developer's week and closes an 8.0. The WAF costs a purchase order and closes an 8.6. Both go first because the ratio of effort to risk removed is not close.

## The SOC Angle

Every threat here becomes a detection rule.

That is the connection worth making. A threat model is not just a list of things to fix — the ones you cannot fix, or cannot fix yet, become things to watch. R-01 sits at 90 days, which means for 90 days the SOC needs a rule on admin actions rather than a control preventing them.

Threat modeling tells the SOC where to look before the SIEM has anything to say.

## MITRE ATT&CK Mapping

| Technique | ID | Threat |
| --- | --- | --- |
| Valid accounts | T1078 | S-01, account spoofing |
| Steal web session cookie | T1539 | S-02, session hijacking |
| Access token manipulation | T1134 | S-03, forged JWT |
| Exploit public facing application | T1190 | T-01, database tampering |
| Data destruction | T1485 | T-02, file deletion |
| Data from information repositories | T1213 | I-01, database PII exposure |
| Endpoint denial of service | T1499 | D-01, HTTP flood |
| Exploitation for privilege escalation | T1068 | E-01 and E-02, both escalation paths |

Mapping note: these are the techniques each modelled threat corresponds to. Nothing was observed or tested. R-01 has no ATT&CK mapping, because repudiation is an absence of logging rather than an adversary technique, and forcing a mapping onto it would be dishonest.

## Findings

11 threats across all 6 STRIDE categories, 6 rated high.

DDoS scored highest, above credential theft, which is the opposite of intuition and the reason scoring exists.

IDOR scored second on exploitability alone. It needs no attacker, only a curious user.

Database PII exposure carries regulatory exposure for all 500 users, not just technical risk.

Repudiation surfaced only because STRIDE forced the category. Nobody models it voluntarily.

## What This Demonstrates

Applying STRIDE systematically rather than modelling the threats that come to mind.

Scoring with DREAD to produce a prioritisation that survives a stakeholder asking why.

Recognising when the score contradicts instinct, and trusting the score.

Ordering remediation by risk removed per unit of effort, not by category or by severity label.

Connecting threat modeling to SOC detection, so unfixed threats become monitored threats.

Declining to map a threat to ATT&CK when the technique does not exist.

## Repository Structure

```
threat-modeling-stride-dread/
├── README.md
└── models/
    ├── stride_analysis.md
    └── dread_scoring.md
```

---

[![LinkedIn](https://img.shields.io/badge/LinkedIn-WilliamInCyber-blue?style=flat&logo=linkedin)](https://linkedin.com/in/WilliamInCyber)
[![X](https://img.shields.io/badge/X-@WilliamInCyber-black?style=flat&logo=x)](https://x.com/WilliamInCyber)
