# Screenshots — Evidence Index
**Analyst:** Angel Proshia F

**Task:** API Security Risk Analysis — Task 3

**Tool:** Postman (desktop app)

---

## 01_postman_jsonplaceholder_users.png
**Request:** `GET https://jsonplaceholder.typicode.com/users` — no auth tab configured
**Shows:** `200 OK` response (257 ms) with full user records — name, username,
email, complete address — for all 10 users, with zero authentication required.
**Confirms:** RISK-01 — Unauthenticated Access to Full User Records

---

## 02_github_rate_limit_403.png
**Request:** `GET https://api.github.com/users/octocat`
**Shows:** `403 rate limit exceeded` response body, including the caller's
real IP address (103.148.1.114) and GitHub's rate-limit guidance message.
**Confirms:** RISK-05 — Verbose Error Disclosure (genuine, organically
triggered during testing — not simulated)

---

## 03_github_headers_detail.png
**Request:** Same as above
**Shows:** Full response headers — `X-RateLimit-Limit: 60`,
`X-RateLimit-Remaining: 0`, `X-RateLimit-Used: 60`, `X-RateLimit-Resource: core`
**Confirms:** RISK-04/05 — proper rate-limit header implementation (used as
the comparison baseline against JSONPlaceholder)

---

## 04_postman_jsonplaceholder_posts_body.png
**Request:** `GET https://jsonplaceholder.typicode.com/posts`
**Shows:** `200 OK` response (82 ms) with post data
**Confirms:** Baseline successful request prior to header inspection

---

## 05_postman_jsonplaceholder_posts_headers.png
**Request:** Same as above
**Shows:** Response headers — `X-Ratelimit-Limit: 1000`,
`X-Ratelimit-Remaining: 999`, `X-Ratelimit-Reset: 1775329346`,
`X-Powered-By: Express`, `Via: 2.0 heroku-router`
**Confirms:** RISK-04 (corrected) — JSONPlaceholder DOES enforce rate
limiting (1000 req/hr), tracked per-IP since no authentication exists

> **Important:** This screenshot directly corrected an earlier draft of
> the report, which had incorrectly assumed JSONPlaceholder had no rate
> limiting at all. Real evidence always takes priority over assumption —
> the report finding (RISK-04) was rewritten to reflect what this
> screenshot actually shows.

---

## 06_postman_reqres_apikey.png
**Request:** `GET https://reqres.in/api/users` with header
`x-api-key: free_user_3FWeiYhpa3ls7U0UtXRrgWdrKYz`
**Shows:** `200 OK` response (504 ms) with full user data including real
emails, names, and avatar URLs — returned using only a free-tier API key
**Confirms:** RISK-02 — Missing Authorization Scope on x-api-key

---

## Summary Table

| # | Screenshot | Confirms |
|---|-----------|----------|
| 1 | 01_postman_jsonplaceholder_users.png | RISK-01 |
| 2 | 02_github_rate_limit_403.png | RISK-05 |
| 3 | 03_github_headers_detail.png | RISK-04 / RISK-05 baseline |
| 4 | 04_postman_jsonplaceholder_posts_body.png | Supporting evidence |
| 5 | 05_postman_jsonplaceholder_posts_headers.png | RISK-04 (corrected finding) |
| 6 | 06_postman_reqres_apikey.png | RISK-02 |

---

*All screenshots above are real, live captures taken in Postman during
this audit — none are simulated or fabricated.*
