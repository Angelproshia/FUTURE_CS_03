# FUTURE_CS_03 — API Security Risk Analysis Report
### Prepared by: Angel Proshia F
### Future Interns | Cyber Security Track | Task 3 | June 2026

---

## Task Overview
A professional, read-only API Security Risk Analysis conducted as part of
the **Future Interns Cyber Security internship programme**. This task
simulates the kind of paid API security audit performed by AppSec
consultants and SaaS security teams — analyzing public/demo APIs for
authentication, authorization, rate-limiting, and data-exposure risks.

---

## APIs Tested

| API | Base URL | Auth Model |
|-----|----------|------------|
| **JSONPlaceholder** | jsonplaceholder.typicode.com | None — fully open, no authentication required |
| **ReqRes** | reqres.in/api/* | Single-tier `x-api-key` header + Bearer session tokens for `/app/*` |
| **GitHub REST API** | api.github.com | Optional — unauthenticated allowed at lower rate limit (60 req/hr) |

> All APIs tested are explicitly public, demo, or test services intended
> for this kind of evaluation. No private, production, or unauthorized
> systems were accessed at any point.

---

## Scope

### In Scope
- Testing public or demo APIs only
- Read-only requests (GET) — no exploitation or bypass attempts
- Documentation-based analysis of authentication and authorization design
- Header, token, and response inspection

### Out of Scope
- Exploitation or authentication bypass attempts
- Flooding / Denial-of-Service testing
- Attacking private or production APIs
- Any destructive write/delete operations

---

## Tools Used

| Tool | Purpose |
|------|---------|
| **Postman / curl** | Sending read-only GET requests and inspecting raw responses |
| **Browser DevTools** | Inspecting request/response headers, status codes, timing |
| **Official API Documentation** | Confirming authentication requirements and endpoint behaviour |
| **OWASP API Security Top 10** | Risk classification framework for all findings |

---

## Findings Summary

| ID | Risk | API | Severity | OWASP Category |
|----|------|-----|----------|-----------------|
| RISK-01 | Unauthenticated Access to Full User Records | JSONPlaceholder | 🔴 HIGH | API2:2023 Broken Authentication |
| RISK-02 | Missing Authorization on x-api-key | ReqRes | 🔴 HIGH | API5:2023 Broken Function Level Authorization |
| RISK-03 | Session Token Trust Gap | ReqRes | 🟠 MEDIUM | API1:2023 Broken Object Level Authorization |
| RISK-04 | IP-Based Rate Limit With No Authentication Tied To It | JSONPlaceholder | 🟠 MEDIUM | API4:2023 Unrestricted Resource Consumption |
| RISK-05 | Verbose Error Disclosure | GitHub REST API | 🟢 LOW | API8:2023 Security Misconfiguration |

**Overall Risk Posture: Mixed** — public test APIs (by design) demonstrate
common real-world risk patterns, while GitHub's API shows what correct
rate-limiting and authentication design looks like in production.

---

## Key Evidence Highlights

- **JSONPlaceholder `/users`** returns full personal records (name, email,
  address, phone, company) with **zero authentication required**
- **ReqRes** documentation confirms a single `x-api-key` grants both
  read access and admin/management operations — no scoped key tiers
- **Live test against `api.github.com`** during this audit actually hit a
  real `403 Forbidden` rate-limit response — genuine evidence of what
  proper rate-limiting enforcement and headers look like, used as a
  comparison baseline against the other two APIs
- **Live test against JSONPlaceholder `/posts`** confirmed it does enforce
  a 1000 req/hour rate limit via `X-Ratelimit-*` headers — more generous
  than GitHub's 60/hour, but necessarily IP-based since no authentication
  exists on this API

---

## Repository Structure

```
FUTURE_CS_03/
│
├── README.md                              ← This file
│
├── report/
│   └── API_Security_Risk_Analysis_Report_FUTURE_CS_03.pdf
│
├── evidence/
│   ├── api_test_evidence.md               ← Full request/response log for all 5 tests
│   └── owasp_api_top10_mapping.md         ← Risk-to-OWASP-category mapping
│
└── screenshots/
    └── README.md                          ← Screenshot capture guide
```

---

## Report

**[Download Full Report PDF](report/API_Security_Risk_Analysis_Report_FUTURE_CS_03.pdf)**

The PDF report includes:
- Cover page with summary badges (3 APIs tested, 5 risks found, 2 high severity)
- Table of contents
- Executive summary
- Scope and methodology
- APIs tested overview table
- Risk severity key
- 5 detailed findings — each with real request/response evidence, business impact, and remediation
- Risk summary matrix
- Remediation roadmap with timelines
- Scope and ethics statement

---

## Ethics Statement

This audit was conducted **solely for educational purposes** as part of
the Future Interns Cyber Security Task 3 programme.

-  Only read-only GET requests were used; no exploitation or bypass attempts
-  No flooding, denial-of-service, or brute-force testing was performed
-  No real user credentials, personal data, or private API keys are included
-  Rate-limit responses encountered during testing were treated as evidence, not bypassed
-  Full compliance with Future Interns Task 3 ethical guidelines

---

## Author

| Field | Details |
|-------|---------|
| **Name** | Angel Proshia F |
| **Programme** | Future Interns — Cyber Security Track |
| **Task** | Task 3 — API Security Risk Analysis |
| **Track Code** | CS |
| **Repository** | FUTURE_CS_03 |
| **Date** | June 2026 |

---

*This report is for educational purposes only. All APIs tested are public,
demo, or test services explicitly intended for this kind of evaluation.*
