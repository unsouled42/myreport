# Fidelity TAS 5.0 – QA & Performance Testing Portal

This portal centralizes all test results across environments to validate:
- Functional continuity after the TAS 5.0 upgrade
- System performance under load
- Data integrity and API reliability
- Readiness for production rollout

---

<div align="center" style="margin-top: 20px;">

  <div style="display: inline-block; width: 180px; margin: 0 10px;">
    <a href="#/dev/" style="display: block; padding: 10px; background: #42b983; color: white; border-radius: 6px; text-decoration: none; font-weight: bold;">
      DEV Report
    </a>
    <div style="margin-top: 6px; color: #42b983; font-weight: 600;">
      ✅ Completed
    </div>
  </div>

  <div style="display: inline-block; width: 180px; margin: 0 10px;">
    <a href="#/uat/" style="display: block; padding: 10px; background: #ffc107; color: black; border-radius: 6px; text-decoration: none; font-weight: bold;">
      UAT Report
    </a>
    <div style="margin-top: 6px; color: #ffc107; font-weight: 600;">
      🟡 In Progress
    </div>
  </div>

  <div style="display: inline-block; width: 180px; margin: 0 10px;">
    <a href="#/prod/" style="display: block; padding: 10px; background: #6c757d; color: white; border-radius: 6px; text-decoration: none; font-weight: bold;">
      PROD Report
    </a>
    <div style="margin-top: 6px; color: #999; font-weight: 600;">
      ⏳ Pending
    </div>
  </div>

</div>

---

## 🔧 Current Focus: Performance Validation (UAT)

We are actively validating TRIRIGA's **performance, scalability, and stability** in UAT with focus on:

- 🔐 **Login & Session Handling** (CSRF, cookie management)
- 🚀 **Response Time** under load (avg: 56 ms)
- 📈 **Throughput & Error Rate** (0% errors over 300 iterations)
- 🔄 **API Responsiveness** (OSLC endpoints)

👉 [View UAT Performance Test Report](#/uat/perf_testing)

---

**Maintained by:** Souleiman Bentouyer  
**Testing Period:** June–July 2025  
