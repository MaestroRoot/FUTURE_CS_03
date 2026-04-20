# 🔐 API Security Assessment Report – OWASP crAPI

## Task Name
**API Security Assessment — OWASP crAPI (Completely Ridiculous API)**

---

## 📋 Project Overview

This project documents a manual **API Security Assessment** conducted on the [OWASP crAPI](https://github.com/OWASP/crAPI) (Completely Ridiculous API) — a deliberately vulnerable API application developed by OWASP for security awareness and training purposes. The platform simulates a vehicle management web application exposing a range of real-world API vulnerabilities commonly found in production systems.

The assessment was performed in a controlled **Docker environment** running on `localhost:8888`, using industry-standard tools including **Postman** and **Browser Developer Tools**. All findings are mapped to the **OWASP API Security Top 10 (2023)** framework and accompanied by evidence screenshots, severity ratings, and actionable remediation guidance.

A total of **7 vulnerabilities** were identified — **2 Critical**, **4 High**, and **1 Medium**.

> ⚠️ **Disclaimer:** This assessment was conducted solely for academic and training purposes within a controlled local Docker environment. No production systems were targeted at any point. All activities were performed in full compliance with ethical penetration testing principles.

---

## 🛠️ Tools & Environment

| Tool | Purpose |
|------|---------|
| **Docker** | Running the crAPI vulnerable application locally |
| **Postman** | Crafting and sending API requests to test vulnerabilities |
| **Browser Developer Tools** (Chrome/Firefox) | Observing network requests, inspecting headers, capturing tokens and OTPs |

---

## 🔍 Methodology

The assessment followed a structured manual testing approach guided by the **OWASP API Security Testing Guide**, divided into the following phases:

| Phase | Description |
|-------|-------------|
| **Reconnaissance & Endpoint Discovery** | Identifying all exposed API endpoints by observing network traffic and analyzing API responses |
| **Authentication & Authorization Testing** | Testing for broken authentication, weak token generation, missing access controls, and privilege escalation vectors |
| **Input Validation Testing** | Submitting unexpected and unauthorized parameters to identify mass assignment and improper input handling |
| **Rate Limiting & Resource Abuse** | Sending repeated requests to identify missing rate limits and brute force protections |
| **Information Disclosure Analysis** | Reviewing API responses for sensitive data leakage including tokens, emails, and system metadata |
| **Documentation & Reporting** | Recording all findings with screenshots, OWASP classification, severity scoring, and remediation guidance |

---

## 🚨 Vulnerabilities Found

| # | Vulnerability | OWASP Category | Severity | Status |
|---|--------------|----------------|----------|--------|
| 1 | API Endpoint Discovery | API9:2023 | 🟡 Medium | Confirmed |
| 2 | Broken Object Level Authorization (BOLA) | API1:2023 | 🟠 High | Confirmed |
| 3 | Broken Authorization (Accessing Other Users' Data) | API1:2023 | 🟠 High | Confirmed |
| 4 | Excessive Data Exposure / Information Disclosure | API3:2023 | 🟠 High | Confirmed |
| 5 | Weak Tokens — JWT & OTP Brute Force | API2:2023 | 🔴 Critical | Confirmed |
| 6 | Missing Rate Limiting | API4:2023 | 🟠 High | Confirmed |
| 7 | Mass Assignment / Input Validation Failure | API6:2023 | 🔴 Critical | Confirmed |

### Risk Summary
- 🔴 **Critical:** 2 vulnerabilities
- 🟠 **High:** 4 vulnerabilities
- 🟡 **Medium:** 1 vulnerability

---

## 🔑 Key Vulnerabilities & Risks

### 🔴 Mass Assignment (CRITICAL)
The `/identity/api/auth/signup` endpoint accepts all user-supplied JSON fields and maps them directly to the internal user object — including `role: ADMIN`, `isAdmin: true`, and `accountType: premium`. An attacker can self-elevate their privileges at registration time, gaining full administrative access with no additional authentication.

### 🔴 Weak Tokens — JWT & OTP Brute Force (CRITICAL)
The login endpoint imposes no lockout or progressive delay on failed authentication attempts. The JWT token uses a weak signing algorithm susceptible to offline cracking, and the OTP mechanism has no attempt limit — making full account takeover trivially achievable without knowing the victim's password.

### 🟠 Broken Object Level Authorization — BOLA (HIGH)
The `/workshop/api/vehicles/{vehicleId}/location` endpoint does not verify whether the requesting user owns the resource. By substituting another user's `vehicleId` in a Postman request, their full vehicle location data is returned with a `200 OK` response — a direct violation of horizontal access control.

### 🟠 Broken Authorization — Accessing Other Users' Data (HIGH)
The `/workshop/api/vehicles/{carId}` endpoint lacks ownership verification. Any authenticated user can access any other user's vehicle data by iterating over car IDs, enabling mass data harvesting and potential identity theft.

### 🟠 Excessive Data Exposure / Information Disclosure (HIGH)
The community posts endpoint returns verbose JSON responses containing sensitive fields — including email addresses, vehicle IDs, and internal metadata — not shown in the frontend UI. Filtering is done client-side only, meaning the raw API exposes far more than intended.

### 🟠 Missing Rate Limiting (HIGH)
The community posts endpoint accepts unlimited repeated requests with no throttling, blocking, or rate limit headers in responses. This opens the application to credential stuffing, enumeration attacks, and potential Denial of Service (DoS).

### 🟡 API Endpoint Discovery (MEDIUM)
Internal API endpoints for products, orders, and vehicles are fully visible in browser Developer Tools network traffic without authentication prompts on several routes — enabling attackers to map the entire attack surface and facilitate further exploitation.

---

## ⚖️ Scope & Ethics

### ✅ Allowed
- Testing public or demo APIs
- Read-only requests (GET, safe POST where allowed)
- Documentation-based analysis
- Header, token, and response inspection

### ❌ Not Allowed
- Exploitation or bypass attempts
- Flooding / DoS testing
- Attacking private or production APIs

> All activities conducted during this assessment strictly adhered to the above scope. OWASP crAPI is a sandbox application intentionally designed for security training, and all testing was performed responsibly within a controlled local Docker environment.

---

## 📁 Repository Structure

```
📦 api-security-assessment-crapi/
├── 📁 images/                                         # Evidence & proof-of-concept screenshots
│   ├── crapi_signin_page.png                          # crAPI Sign-In page overview
│   ├── crapi_signup_page.png                          # crAPI Sign-Up page overview
│   ├── endpoint_discovery_evidence_1.png              # API endpoint discovery — Products
│   ├── endpoint_discovery_evidence_2.png              # API endpoint discovery — Orders
│   ├── bola_evidence_1.png                            # BOLA — unauthorized vehicle location access
│   ├── bola_evidence_2.png                            # BOLA — obtaining another user's vehicle ID
│   ├── bola_evidence_3.png                            # BOLA — confirmed unauthorized data returned
│   ├── broken_auth_evidence_1.png                     # Broken Authorization — other users' data
│   ├── excessive_data_exposure_evidence_1.png         # Excessive data exposure — verbose JSON response
│   ├── weak_tokens_evidence_1.png                     # Weak JWT token structure in login response
│   ├── missing_rate_limiting_evidence_1.png           # Rate limiting — repeated 200 OK responses
│   ├── missing_rate_limiting_evidence_2.png           # Rate limiting — no rate limit headers in response
│   └── mass_assignment_evidence_1.png                 # Mass assignment — ADMIN role self-assigned at signup
├── 📄 API_Security_Assessment_Report.pdf              # Full assessment report
└── 📄 README.md                                       # This file
```

---

## 👤 Author

**Samson Budigila**


BSc Cybersecurity — Institute of Finance Management (IFM)

Dar es Salaam, Tanzania

Internship: Future Interns

📧 [samsonbudigila6@gmail.com](mailto:samsonbudigila6@gmail.com)

📞 +255 757 502 743

🔗 [LinkedIn Profile](https://www.linkedin.com/in/samson-budigila-08112131a?utm_source=share_via&utm_content=profile&utm_medium=member_ios)

---

## 📄 License

This project is intended for **educational and academic purposes only**. All findings are confidential and must not be used for unauthorized or malicious activities.
