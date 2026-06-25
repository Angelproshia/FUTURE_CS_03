# OWASP API Security Top 10 — Risk Mapping
**Reference:** https://github.com/OWASP/API-Security
**Analyst:** Angel Proshia F

This document maps each finding in the report to its corresponding OWASP
API Security Top 10 (2023) category, for anyone wanting to cross-reference
against the official OWASP project. Each row also links to the real
Postman screenshot that provided the evidence for that finding.   

---

| Report Finding | OWASP API Security Top 10 (2023) Category | Official Description (Paraphrased) | Screenshot Evidence |
|----------------|---------------------------------------------|-------------------------------------|----------------------|
| RISK-01 — Unauthenticated User Records | **API2:2023 — Broken Authentication** | Authentication mechanisms are often implemented incorrectly, allowing attackers to compromise tokens or exploit flaws to assume other users' identities — or, as in this case, access data with no authentication at all | `01_postman_jsonplaceholder_users.png` |
| RISK-02 — Missing Authorization Scope | **API5:2023 — Broken Function Level Authorization** | Complex access control policies with different roles/hierarchies make it easy to introduce authorization flaws, allowing attackers to access functions they shouldn't (e.g. admin functions via a regular key) | `06_postman_reqres_apikey.png` |
| RISK-03 — Session Token Trust Gap | **API1:2023 — Broken Object Level Authorization** | APIs tend to expose endpoints handling object identifiers, creating a wide attack surface for object-level access control issues — the #1 most common API vulnerability category | *(documentation-based finding — no live screenshot, see report for details)* |
| RISK-04 — IP-Based Rate Limit With No Authentication Tied To It | **API4:2023 — Unrestricted Resource Consumption** | API requests consume resources (network, CPU, memory). Rate limits must be tied to an identity wherever possible; limiting by IP alone (as observed here) is weaker because it can't distinguish individual callers behind a shared IP | `04_postman_jsonplaceholder_posts_body.png`, `05_postman_jsonplaceholder_posts_headers.png` |
| RISK-05 — Verbose Error Disclosure | **API8:2023 — Security Misconfiguration** | APIs and their supporting systems often contain complex configurations meant to make them more customizable — misconfigurations like verbose error messages can leak sensitive information | `02_github_rate_limit_403.png`, `03_github_headers_detail.png` |

---

## Note on RISK-04 (Corrected Finding)

An earlier draft of this mapping listed RISK-04 as "No Rate Limiting" on
JSONPlaceholder. Live testing captured in Postman (screenshots 04 and 05)
proved this assumption wrong — JSONPlaceholder actually enforces a real
1000 requests/hour limit, visible via the `X-Ratelimit-Limit`,
`X-Ratelimit-Remaining`, and `X-Ratelimit-Reset` response headers.

The finding was corrected to reflect what the evidence actually showed:
the limit exists and is reasonably generous, but because JSONPlaceholder
has no authentication layer (RISK-01), the limit can only be tracked per
IP address rather than per user or API key — which is the real, more
nuanced risk under API4:2023.

This is also why screenshots 02 and 03 (GitHub's `403` response and its
`X-RateLimit-*` headers) are used as the comparison baseline — GitHub
enforces a stricter 60 req/hour limit for unauthenticated callers, but
ties its limit system to the same identity-aware design.

---

## Why This Framework Was Used

The OWASP API Security Top 10 is maintained by the OWASP Foundation
specifically for API-focused risks (distinct from the general OWASP Top 10
for web applications). It is the industry-standard reference used by
AppSec consultants and SaaS security teams when scoping and reporting on
API security audits — exactly the kind of engagement this task simulates.

---

## Additional Reference Used

**API Security Checklist** (https://github.com/shieldfy/API-Security-Checklist)
was used as a secondary practical checklist to confirm common hardening
recommendations cited in the Remediation sections of the main report
(e.g. rate limiting, scoped API keys, object-level authorization checks).

Both references were used for **study and methodology guidance only** —
no content was copied directly from either repository.
