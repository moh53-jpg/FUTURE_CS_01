# 🔐 Web Application Penetration Test — testfire.net

![Type](https://img.shields.io/badge/Type-Black--Box%20Pentest-red?style=flat-square)
![Scope](https://img.shields.io/badge/Scope-XSS%20%26%20SQL%20Injection-orange?style=flat-square)
![OWASP](https://img.shields.io/badge/OWASP-A03%3A2021-blue?style=flat-square)
![Date](https://img.shields.io/badge/Test%20Date-04%20June%202026-lightgrey?style=flat-square)

> ⚠️ **Authorised testing only.** `testfire.net` is IBM's intentionally vulnerable demo application designed for security research and education. Never reproduce these techniques on systems you do not own or have explicit written permission to test.

---

## 🌐 Website Tested

| Field | Details |
|---|---|
| **Target URL** | http://testfire.net |
| **Application** | AltoroMutual Online Banking Demo |
| **Hosted By** | IBM (intentionally vulnerable demo) |
| **Environment** | Internet-facing, authorised for security testing |
| **Test Date** | 03–04 June 2026 |
| **Tester** | Mohamed Abdikadir Ahmed |

AltoroMutual is a fictional online banking application maintained by IBM as a safe, legal target for practising web application security testing techniques.

---

## 🎯 Scope

The assessment focused exclusively on **OWASP A03:2021 — Injection**, covering two vulnerability classes across six endpoints.

### In-Scope Endpoints

| Endpoint | Method | Parameter(s) | Attack Type |
|---|---|---|---|
| `/search.aspx` | GET | `query` | Reflected XSS |
| `/feedback.aspx` | POST | `comment`, `name` | Stored XSS |
| `/default.aspx` | GET | `#fragment` (URL) | DOM-Based XSS |
| `/bank/queryxpath.aspx` | GET | `content_id` | SQLi — Error-Based |
| `/bank/login.aspx` | POST | `uid`, `passw` | SQLi — UNION-Based |
| `/bank/queryxpath.aspx` | GET | `content_id` | SQLi — Blind Boolean |

### Findings Overview

| ID | Vulnerability | Severity | CVSS v3.1 |
|---|---|---|---|
| VA-001 | Reflected XSS — `/search.aspx` | 🔴 CRITICAL | 9.6 |
| VA-002 | Stored XSS — `/feedback.aspx` | 🟠 HIGH | 7.4 |
| VA-003 | DOM-Based XSS — URL Fragment | 🟠 HIGH | 7.1 |
| VA-004 | SQL Injection — Error-Based | 🔴 CRITICAL | 9.8 |
| VA-005 | SQL Injection — UNION-Based | 🔴 CRITICAL | 9.8 |
| VA-006 | SQL Injection — Blind Boolean | 🟡 MEDIUM | 6.5 |

**6 vulnerabilities confirmed — 3 Critical · 2 High · 1 Medium**

---

## 🛠️ Tools Used

| Tool | Version | Purpose |
|---|---|---|
| **Burp Suite Community** | 2023.x | HTTP proxy, request interception, manual payload delivery |
| **OWASP ZAP** | 2.14 | Automated scanning and spider crawl |
| **SQLMap** | 1.7.x | SQL injection detection and automated exploitation |
| **Nikto** | 2.1.6 | Web server misconfiguration and CVE scanning |
| **Firefox DevTools** | 125+ | DOM inspection, JavaScript debugging, cookie analysis |
| **curl** | 8.x | Manual HTTP request crafting and parameter fuzzing |
| **Python 3** | 3.11 | Custom exploit and automation scripts |
| **Hashcat** | 6.2.x | Offline password hash cracking |


## 📁 Repository Structure


├── README.md
├── VULNERABILITY_ASSESSMENT_REPORT.pdf
└── evidence/
    ├── VA-001_Reflected_XSS.jpg
    ├── VA-002_Stored_XSS.jpg
    ├── VA-003_DOM_XSS.jpg
    ├── VA-004_SQLi_ErrorBased.jpg
    ├── VA-005_SQLi_UNION.jpg
    └── VA-006_SQLi_BlindBoolean.jpg

*Prepared by Mohamed Abdikadir Ahmed · 04 June 2026 · CONFIDENTIAL — distribute only to authorised personnel*
