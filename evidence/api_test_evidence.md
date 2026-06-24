# API Test Evidence — Request/Response Log
**Analyst:** Angel Proshia F

**Date:** June 2026

**Method:** Read-only GET requests only, captured live in Postman

---

## TEST 1 — JSONPlaceholder: Unauthenticated User Data Access

**Request**
```
GET https://jsonplaceholder.typicode.com/users
(no Authorization header, no API key, no auth tab configured)
```

**Response** — `200 OK` (257 ms, 2.95 KB) — **live capture, screenshot 01**
```json
[
  {
    "id": 1,
    "name": "Leanne Graham",
    "username": "Bret",
    "email": "Sincere@april.biz",
    "address": {
      "street": "Kulas Light",
      "suite": "Apt. 556",
      "city": "Gwenborough",
      "zipcode": "92998-3874",
      "geo": { "lat": "-37.3159", "lng": "81.1496" }
    }
  }
  // ... 9 more full user records returned, same structure
]
```

**Screenshot:** `01_postman_jsonplaceholder_users.png`

**Risk:** HIGH — Full PII (name, email, address) returned with zero access
control. See Report RISK-01.

---

## TEST 2 — ReqRes: Single-Tier API Key (No Scope Separation)

**Request**
```
GET https://reqres.in/api/users
Header: x-api-key: free_user_3FWeiYhpa3ls7U0UtXRrgWdrKYz
```

**Response** — `200 OK` (504 ms, 2.62 KB) — **live capture, screenshot 06**
```json
{
  "page": 1,
  "per_page": 6,
  "total": 12,
  "total_pages": 2,
  "data": [
    {
      "id": 1,
      "email": "george.bluth@reqres.in",
      "first_name": "George",
      "last_name": "Bluth",
      "avatar": "https://reqres.in/img/faces/1-image.jpg"
    }
    // ... more users
  ]
}
```

**Screenshot:** `06_postman_reqres_apikey.png`

**Note:** A free-tier `x-api-key` (the cheapest/most basic tier available)
was sufficient to read full user records via `/api/users`. Per ReqRes's own
documentation, this same key tier also works across all `/api/*` endpoints
including collections and management operations — no separate read-only
vs. admin key distinction exists.

**Risk:** HIGH — See Report RISK-02.

---

## TEST 3 — JSONPlaceholder: Rate Limit Headers (Corrected Finding)

**Request**
```
GET https://jsonplaceholder.typicode.com/posts
```

**Response** — `200 OK` (82 ms, 8.02 KB) — **live capture, screenshots 04 & 05**

Response headers observed:
```
Via: 2.0 heroku-router
X-Content-Type-Options: nosniff
X-Powered-By: Express
X-Ratelimit-Limit: 1000
X-Ratelimit-Remaining: 999
X-Ratelimit-Reset: 1775329346
Age: 20286
```

**Screenshots:** `04_postman_jsonplaceholder_posts_body.png`,
`05_postman_jsonplaceholder_posts_headers.png`

**Important correction:** An earlier draft of this report incorrectly
assumed JSONPlaceholder had no rate limiting at all. Live evidence captured
in Postman shows this is **false** — JSONPlaceholder enforces a real limit
of 1000 requests/hour, tracked per IP address (visible via the
`X-Ratelimit-*` headers above). The actual finding (revised) is that this
limit can only be IP-based, since the API has no authentication layer to
tie the limit to instead. See Report RISK-04 (corrected).

---

## TEST 4 — GitHub REST API: Real Enforced Rate Limit

**Request**
```
GET https://api.github.com/users/octocat
```

**Response** — `403 rate limit exceeded` (33 ms, 1.26 KB) — **live capture, screenshot 02 & 03**
```json
{
  "message": "API rate limit exceeded for 103.148.1.114. (But here's the
   good news: Authenticated requests get a higher rate limit. Check out
   the documentation for more details.)",
  "documentation_url": "https://docs.github.com/rest/overview/resources-in-the-rest-api#rate-limiting"
}
```

Response headers observed:
```
X-GitHub-Media-Type: github.v3; format=json
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 0
X-RateLimit-Used: 60
X-RateLimit-Resource: core
```

**Screenshots:** `02_github_rate_limit_403.png`, `03_github_headers_detail.png`

This is a genuine, organically triggered rate-limit response captured
during normal testing in this audit — not a simulated or invented example.
It confirms GitHub enforces a 60 requests/hour limit for unauthenticated
callers, with proper `X-RateLimit-*` headers and a clear error message.

**Risk:** LOW (the disclosure itself) — See Report RISK-05. Also used as
the comparison baseline for RISK-04.

---

## Summary of Tools Used

| Tool | Purpose |
|------|---------|
| Postman (desktop app) | Sending all GET requests, inspecting full HTTP responses and headers |

---

## Screenshot Index

| File | Captures |
|------|----------|
| `01_postman_jsonplaceholder_users.png` | RISK-01 evidence — full user data, no auth |
| `02_github_rate_limit_403.png` | RISK-05 evidence — 403 error body |
| `03_github_headers_detail.png` | RISK-04/05 evidence — GitHub rate-limit headers |
| `04_postman_jsonplaceholder_posts_body.png` | RISK-04 evidence — posts response body |
| `05_postman_jsonplaceholder_posts_headers.png` | RISK-04 evidence — JSONPlaceholder rate-limit headers |
| `06_postman_reqres_apikey.png` | RISK-02 evidence — x-api-key full data access |

---

## Ethical Notes

- All requests above were **GET only** — read-only, non-destructive
- No authentication bypass, credential brute-forcing, or token forgery was attempted
- The GitHub rate-limit response was an organic result of normal testing, not an intentional flood/DoS attempt
- The ReqRes API key used is a free-tier key created specifically for this educational test
- No private, production, or unauthorized systems were tested
