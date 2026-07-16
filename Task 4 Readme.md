# Task 4 – Reconnaissance & Penetration Testing

## Objective

This task consists of two parts:

- **Part A:** Passive reconnaissance of `dodopayments.tech` using only publicly available information.
- **Part B:** Authorized penetration testing of the designated vulnerable target (lab host or local application).

---

# Tools Used

## Reconnaissance

- Git Bash
- subfinder
- assetfinder
- amass (Passive Mode)
- crt.sh
- httpx
- whatweb
- testssl.sh

## Penetration Testing

- Burp Suite Community
- OWASP ZAP
- nuclei
- ffuf
- sqlmap

---

# Part A – Passive Reconnaissance

## Scope

Target Domain

```
dodopayments.tech
```

Passive techniques only:

- Certificate Transparency Logs
- DNS Enumeration
- HTTP Banner Collection
- Technology Fingerprinting
- TLS Analysis

No active scanning or exploitation was performed.

---

## Methodology

### 1. Subdomain Enumeration

Tools used:

- subfinder
- assetfinder
- amass (passive)
- crt.sh

Example commands:

```bash
subfinder -d dodopayments.tech

assetfinder --subs-only dodopayments.tech

amass enum -passive -d dodopayments.tech
```

---

### 2. Live Host Detection

```bash
httpx -l all_subdomains.txt
```

---

### 3. Technology Fingerprinting

```bash
whatweb https://target
```

---

### 4. TLS Analysis

```bash
./testssl.sh https://target
```

---

# Reconnaissance Findings

## Public Attack Surface

| Host | Status | Technology | Notes |
|------|--------|------------|------|
| Add your findings here | | | |

---

## Risk Observations

Examples:

- Public API endpoints
- Admin portals
- Development environments
- Weak TLS configuration
- Missing security headers
- Exposed documentation

Document only verified observations.

---

# Part B – Authorized Penetration Test

## Scope

Testing was performed **only** against the designated vulnerable target provided as part of the assignment.

Production systems were not tested.

---

## Methodology

The assessment followed a black-box methodology.

Testing included:

- Authentication
- Authorization
- Input Validation
- Injection Testing
- Session Management
- Security Misconfiguration
- Information Disclosure

---

# Findings

## Finding 1

### Title

Example Finding

### Severity

High

### CVSS v3.1

Vector:

```
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:L
```

Score:

```
8.8
```

### Affected Endpoint

```
/example
```

### Description

Describe the issue.

### Steps to Reproduce

1.
2.
3.

### Proof of Concept

Attach screenshots or request/response.

### Impact

Describe business impact.

### Remediation

Explain how to fix the issue.

---

## Finding 2

Repeat the same format.

---

# Risk Ranking

| Severity | Count |
|-----------|-------|
| Critical | |
| High | |
| Medium | |
| Low | |
| Informational | |

---

# Defensive Controls

| Finding | Preventive Control |
|----------|-------------------|
| Finding 1 | Input Validation |
| Finding 2 | Authentication |
| Finding 3 | Authorization |
| Finding 4 | Security Headers |

---

# Bonus (Optional)

## Attack Chain

Describe how multiple findings could be chained together to increase impact.

---

## Retest

Describe remediation verification after fixes were applied.

---

# Conclusion

The assessment identified security observations within the authorized scope. Findings have been documented with severity, impact, and remediation guidance.

Only passive reconnaissance was performed against the public domain, while active testing was limited to the authorized vulnerable target in accordance with the rules of engagement.