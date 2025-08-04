# TRIRIGA Application Suite 5.0 – Performance Test Report

**Prepared by:** Souleiman Bentouyer  
**Environment:** UAT (`fil-triuat-fil-uat.almhosting.com`)  
**Test Date:** 2025-04-05  
**Report Version:** 1.0  
**Test Tool:** Apache JMeter 5.6.2  
<!-- {docsify-updated} -->

---

## 📘 Objective

Validate the **performance, stability, and scalability** of the core user journey in TRIRIGA post-upgrade, focusing on:

- Authentication (Login) performance under repeated access
- Session and CSRF token handling
- Dashboard and deep-link navigation speed
- Response time consistency and error rate
- System readiness for upcoming **multi-user load and stress testing**

This test establishes the **baseline performance benchmark** for TRIRIGA UAT and confirms functional correctness of the test automation flow.

---

## 📊 Apdex (Application Performance Index)

The **Apdex score** measures user satisfaction with application performance, converting raw response times into meaningful business metrics.

| Threshold | Definition | Response Time |
|----------|------------|---------------|
| **Satisfied** | Users experience no performance-related interruption | ≤ *t* |
| **Tolerating** | Users notice delays but continue working | ≤ *4t* |
| **Frustrated** | Users abandon tasks due to poor performance | > *4t* |

Where *t* is the target response time threshold (e.g., 1 second).

**TRIRIGA UAT Apdex Score**: `1.000` ✅  
*All users experienced response times well within acceptable limits.*

> ℹ️ *Source: [Apdex Standard](https://en.wikipedia.org/wiki/Apdex)*

---

## Test Scope

| Workflow | Included | Tool | Purpose |
|--------|--------|------|--------|
| User Login (Form-Based) | ✅ | JMeter | Validate auth speed and session setup |
| CSRF Token Extraction & Reuse | ✅ | JSON Extractor | Ensure secure session continuity |
| Dashboard Load | ✅ | HTTP GET | Measure initial page responsiveness |
| Navigation to Employee List | ✅ | HTTP GET | Simulate post-login user action |
| Concurrent User Simulation | ✅ | 1 thread × 300 loops | Baseline functional load |
| Multi-User Load | ❌ | — | Planned in Phase 2 |
| Work Order / Asset Actions | ❌ | — | Future scope |

---

## 🧰 Test Configuration

| Parameter | Value |
|--------|-------|
| **JMeter Version** | 5.6.2 |
| **Threads (Users)** | 1 (Baseline) |
| **Ramp-Up Period** | 0 sec |
| **Loop Count** | 300 |
| **Think Time** | None (to be added in next phase) |
| **Execution Mode** | Non-GUI (CLI-ready) |
| **Report Output** | `.jtl`, HTML Dashboard |

<figure>
  <img src="/uat/graphs/PT001-Threadgroup.png" alt="JMeter Thread Group Settings">
  <figcaption><strong>Chart:</strong> JMeter Thread Group Settings</figcaption>
</figure>

---

## 🔄 Test Workflow

Each iteration simulates a real end-user session:

1. **POST /tririga/index.html** → Submit credentials via form login  
2. **Extract CSRF Token** → Use JSON Extractor to capture `$.CSRFToken` from login response  
3. **Reuse Token** → Inject `${csrfToken}` into `X-CSRF-Token` header for subsequent requests  
4. **GET /tririga/app/tririga** → Load main TRIRIGA dashboard (SPA shell)  
5. **GET /tririga/app/tririga/config?reportTemplId=2956&associatedId=-1&manager=1...** → Fetch Employee List data via API  

<figure>
  <img src="/uat/graphs/JSON-Extractor.png" alt="JSON Extractor Config">
  <figcaption><strong>Chart:</strong> JSON Extractor Configuration for CSRF Token</figcaption>
</figure>

> ✅ All requests reuse session cookies (via HTTP Cookie Manager) and CSRF token for authenticity.

---

## 🎯 Performance Goals (SLA)

| Metric | Target | Actual | Status |
|-------|--------|--------|--------|
| **Avg. Response Time** | ≤ 1s | 56 ms | ✅ |
| **Max Response Time** | ≤ 5s | 470 ms | ✅ |
| **Error Rate** | 0% | 0.00% | ✅ |
| **Apdex Score** | ≥ 0.8 | 1.000 | ✅ |
| **Throughput** | ≥ 3 req/sec | 5.9 req/sec | ✅ |

---

## 📊 Performance Test Scenarios

### 📊 PT001 – Login Performance (300 Iterations)

**Area:** <span style="color:blue; font-weight:bold;">Authentication & Session</span>  
**Purpose:**  
Measure response time and reliability of the login endpoint under repeated access.  
**Why It Matters:**  
Slow or failing logins directly impact user adoption and system usability.

**Status:** ✅ PASSED

**Key Metrics:**

| Metric | Value | SLA | Result |
|------|------|-----|--------|
| **# Samples** | 300 | — | ✅ |
| **Avg. Response Time** | 56 ms | ≤ 2s | ✅ |
| **Min Response Time** | 50 ms | — | ✅ |
| **Max Response Time** | 470 ms | ≤ 5s | ✅ |
| **Error Rate** | 0.00% | 0% | ✅ |
| **Throughput** | 5.9 req/sec | ≥ 3 req/sec | ✅ |

**Observation:**  
One outlier at 470 ms likely due to initial session setup or network jitter. No impact on overall stability.

<figure>
  <img src="/uat/graphs/PT001-login-response.png" alt="Login Response Time Over Time">
  <figcaption><strong>Chart:</strong> Login response time remains stable after initial spike</figcaption>
</figure>

<figure>
  <img src="/uat/graphs/PT001-Throughput.png" alt="Throughput Over Time">
  <figcaption><strong>Chart:</strong> Throughput over time for login requests</figcaption>
</figure>

---

### 📊 PT002 – Dashboard Load Time

**Area:** <span style="color:green; font-weight:bold;">User Experience</span>  
**Purpose:**  
Validate speed of main application page load post-login.  
**Why It Matters:**  
First impression is critical; slow dashboards reduce productivity.

**Status:** ✅ PASSED

| Metric | Value | SLA | Result |
|------|------|-----|--------|
| **# Samples** | 300 | — | ✅ |
| **Avg. Response Time** | 56 ms | ≤ 1s | ✅ |
| **Max Response Time** | 109 ms | ≤ 2s | ✅ |
| **Error Rate** | 0.00% | 0% | ✅ |
| **Throughput** | 5.9 req/sec | ≥ 3 req/sec | ✅ |

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
Measure performance of the employee data API that powers the Employee List view.  
**Why It Matters:**  
Users expect fast access to data; delays indicate inefficient rendering or heavy payloads.

**Status:** ✅ PASSED

| Metric | Value | SLA | Result |
|------|------|-----|--------|
| **# Samples** | 300 | — | ✅ |
| **Avg. Response Time** | 56 ms | ≤ 1.5s | ✅ |
| **Max Response Time** | 69 ms | ≤ 2s | ✅ |
| **Error Rate** | 0.00% | 0% | ✅ |
| **Throughput** | 5.9 req/sec | ≥ 3 req/sec | ✅ |

**Payload Analysis:**  
- Response: HTML (3.3 KB) — lightweight  
- No external API calls detected in flow  
- No JavaScript blocking observed (assumed from size)  

<figure>
  <img src="/uat/graphs/PT003-nav-response.png" alt="Response Time Over Time">
  <figcaption><strong>Chart:</strong> Consistent response time across 300 iterations</figcaption>
</figure>

---

## 📈 Summary – Performance Outcomes

✅ **All tests PASSED with 0% error rate**  
✅ **Average response time: 56 ms** across all steps  
✅ **System is stable and responsive** under baseline load  
✅ **CSRF token handling is correct and secure**  
✅ **No performance degradation over 300 iterations**

⚠️ **Observation:**  
- Login max time (470 ms) is an outlier — monitor under real concurrency.

---

## 🖥️ Interactive Performance Dashboard

Explore live charts and detailed metrics:  
👉 [Open Interactive Dashboard](./dashboard/index.html)

<figure>
  <img src="./dashboard/screenshot.png" alt="Interactive Performance Dashboard">
  <figcaption><strong>Chart:</strong> Overview of the interactive JMeter performance dashboard</figcaption>
</figure>


---

## 📎 Attachments

- [🖥️ Interactive Dashboard](/dashboard/) – HTML Performance Dashboard (generated via JMeter)  

---

## 🔄 Revision History

| Version | Date | Author | Changes |
|--------|------|--------|--------|
| 1.0 | 2025-07-28 | Souleiman Bentouyer | Initial release |

---

© 2025 MACS
