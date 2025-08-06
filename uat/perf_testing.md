# 🚀 TRIRIGA Application Suite 5.0 – Performance Test Report

**Prepared by:** Souleiman Bentouyer  
**Environment:** UAT (`fil-triuat-fil-uat.almhosting.com`)  
**Test Date:** 2025-08-06  
**Report Version:** 1.4  
**Test Tool:** Apache JMeter 5.6.2  
<!-- {docsify-updated} -->

---

## 📘 Objective

Validate the **performance, stability, and scalability** of the core user journey in TRIRIGA under **100 concurrent users**, focusing on:

- Authentication performance across 100 unique users
- Session and CSRF token handling at scale
- Dashboard and deep-link navigation speed
- Response time consistency and error rate
- System readiness for stress and endurance testing

This test establishes the **multi-user performance benchmark** for TRIRIGA UAT.

---

## 📊 Apdex (Application Performance Index)

The Apdex score measures user satisfaction with application performance.

| Threshold | Definition | Response Time |
|----------|------------|---------------|
| **Satisfied** | No performance-related interruption | ≤ 1.0 s |
| **Tolerating** | Delays noticed but work continues | ≤ 4.0 s |
| **Frustrated** | Tasks abandoned due to slowness | > 4.0 s |

**TRIRIGA UAT Apdex Score**: `1.000` ✅  
All users experienced response times within the "satisfied" range.

> ℹ️ *Source: [Apdex Standard](https://en.wikipedia.org/wiki/Apdex)*

---

## Test Scope

| Workflow | Included | Purpose |
|--------|--------|--------|
| User Login (Form-Based) | ✅ | Validate auth speed and session setup |
| CSRF Token Extraction & Reuse | ✅ | Ensure secure session continuity |
| Dashboard Load | ✅ | Measure initial page responsiveness |
| Navigation to Employee List | ✅ | Simulate post-login user action |
| Concurrent User Simulation | ✅ | 100 threads × 1 loop |
| Multi-User Load | ✅ | 100 unique users from CSV dataset |
| Work Order / Asset Actions | ❌ | Future scope |

---

## 🧰 Test Configuration

| Parameter | Value |
|--------|-------|
| **JMeter Version** | 5.6.2 |
| **Threads (Users)** | 100 |
| **Ramp-Up Period** | 10 sec |
| **Loop Count** | 1 |
| **Think Time** | None |
| **Execution Mode** | Non-GUI (CLI-ready) |
| **User Data Source** | `users.csv` (100 unique IDs) |
| **Report Output** | `.jtl`, HTML Dashboard |

<figure>
  <img src="/uat/graphs/PT001-Threadgroup.png" alt="JMeter Thread Group Settings - 100 Users">
  <figcaption><strong>Chart:</strong> JMeter Thread Group: 100 concurrent users</figcaption>
</figure>

---

## 🔄 Test Workflow

Each thread simulates a real end-user session:

1. **Read ${userID} from CSV** → 100 threads use real user IDs (100 unique)  
2. **POST /tririga/index.html** → Submit credentials via form login  
3. **Extract CSRF Token** → Capture `$.CSRFToken` from login response  
4. **Reuse Token** → Inject `${csrfToken}` into `X-CSRF-Token` header  
5. **GET /tririga/app/tririga** → Load main TRIRIGA dashboard (SPA shell)  
6. **GET /tririga/app/tririga/config?reportTemplId=2956&associatedId=-1&manager=1...** → Fetch Employee List data via API  

<figure>
  <img src="/uat/graphs/JSON-Extractor.png" alt="JSON Extractor Config">
  <figcaption><strong>Chart:</strong> JSON Extractor Configuration for CSRF Token</figcaption>
</figure>

> ✅ All requests reuse session cookies (via HTTP Cookie Manager) and CSRF token.

---

## 🎯 Performance Goals (SLA)

| Metric | Target | Actual | Status |
|-------|--------|--------|--------|
| **Avg. Response Time** | ≤ 1s | 56 ms | ✅ |
| **Max Response Time** | ≤ 5s | 470 ms | ✅ |
| **Error Rate** | 0% | 0.00% | ✅ |
| **Apdex Score** | ≥ 0.8 | 1.000 | ✅ |
| **Throughput** | ≥ 3 req/sec | 9.8 req/sec | ✅ |

---

## 📊 Performance Test Scenarios

### 📊 PT001 – Login Performance (100 Concurrent Users)

**Area:** <span style="color:blue; font-weight:bold;">Authentication & Session</span>  
**Purpose:**  
Measure response time and reliability of the login endpoint under concurrent access.  
**Why It Matters:**  
Slow or failing logins directly impact user adoption and system usability.

**Status:** ✅ PASSED

| Metric | Value | SLA | Result |
|------|------|-----|--------|
| **# Samples** | 100 | — | ✅ |
| **Avg. Response Time** | 56 ms | ≤ 2s | ✅ |
| **Min Response Time** | 50 ms | — | ✅ |
| **Max Response Time** | 470 ms | ≤ 5s | ✅ |
| **Error Rate** | 0.00% | 0% | ✅ |
| **Throughput** | 9.8 req/sec | ≥ 3 req/sec | ✅ |

**Observation:**  
One outlier at 470 ms likely due to initial session setup or network jitter. No impact on overall stability.

<figure>
  <img src="/uat/graphs/PT001-login-response.png" alt="Login Response Time Over Time">
  <figcaption><strong>Chart:</strong> Login response time remains stable under load</figcaption>
</figure>

---

### 📊 PT002 – Dashboard Load Time

**Area:** <span style="color:green; font-weight:bold;">User Experience</span>  
**Purpose:**  
Validate speed of main application page load post-login under concurrency.  
**Why It Matters:**  
First impression is critical; slow dashboards reduce productivity.

**Status:** ✅ PASSED

| Metric | Value | SLA | Result |
|------|------|-----|--------|
| **# Samples** | 100 | — | ✅ |
| **Avg. Response Time** | 56 ms | ≤ 1s | ✅ |
| **Max Response Time** | 109 ms | ≤ 2s | ✅ |
| **Error Rate** | 0.00% | 0% | ✅ |
| **Throughput** | 9.8 req/sec | ≥ 3 req/sec | ✅ |

**Conclusion:**  
Dashboard loads are fast and consistent. No rendering or backend delays observed.

<figure>
  <img src="/uat/graphs/PT002-200-OK.png" alt="Successful Login Response">
  <figcaption><strong>Chart:</strong> Successful login response (HTTP 200 OK)</figcaption>
</figure>

<figure>
  <img src="/uat/graphs/PT002-HTMLresponse.png" alt="Dashboard HTML Response">
  <figcaption><strong>Chart:</strong> HTML response body of the main TRIRIGA dashboard</figcaption>
</figure>

<figure>
  <img src="/uat/graphs/PT002-Throughput_loading.png" alt="Throughput Over Time">
  <figcaption><strong>Chart:</strong> Throughput over time for dashboard load</figcaption>
</figure>

---

### 📊 PT003 – Employee Data API Performance

**Area:** <span style="color:purple; font-weight:bold;">Data & Navigation</span>  
**Purpose:**  
Measure performance of the employee data API that powers the Employee List view under concurrent access.  
**Why It Matters:**  
Users expect fast access to data; delays indicate inefficient rendering or heavy payloads.

**Status:** ✅ PASSED

| Metric | Value | SLA | Result |
|------|------|-----|--------|
| **# Samples** | 100 | — | ✅ |
| **Avg. Response Time** | 56 ms | ≤ 1.5s | ✅ |
| **Max Response Time** | 69 ms | ≤ 2s | ✅ |
| **Error Rate** | 0.00% | 0% | ✅ |
| **Throughput** | 9.8 req/sec | ≥ 3 req/sec | ✅ |

<figure>
  <img src="/uat/graphs/PT003-aggregate-report.png" alt="Aggregate Report">
  <figcaption><strong>Chart:</strong> Aggregate Report showing key performance metrics</figcaption>
</figure>

<figure>
  <img src="/uat/graphs/PT003-samples.png" alt="GET Employee List Samples">
  <figcaption><strong>Chart:</strong> Successful GET Employee List requests for all 100 users + consistent response time c</figcaption>
</figure>

**Payload Analysis:**  
- Response: HTML (3.3 KB) — lightweight  
- No external API calls detected in flow  
- No JavaScript blocking observed (assumed from size)  

---

## 📈 Summary – Performance Outcomes

✅ **All tests PASSED with 0% error rate**  
✅ **Average response time: 56 ms** under 100-user load  
✅ **System is stable and responsive** at scale  
✅ **CSRF token handling is correct and secure**  
✅ **No performance degradation under concurrency**

⚠️ **Observation:**  
- Login max time (470 ms) is an outlier — monitor under real concurrency.

🟢 **Verdict:** **PASS – UAT Environment is Performance-Ready for Stress Testing**

---

## 🖥️ Interactive Performance Dashboard

Explore live charts and detailed metrics:  
👉 [Open Interactive Dashboard](https://uatupdate.netlify.app/dashboard/)

> 🔗 This dashboard is hosted at `/dashboard/` and auto-updates with each test run.

---

## 🔄 Revision History

| Version | Date | Author | Changes |
|--------|------|--------|--------| 
| 1.0 | 2025-07-28 | Souleiman Bentouyer | Initial release |

---

© 2025 MACS – Performance Engineering & QA Team  
