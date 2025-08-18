# API Testing

**Prepared for:** Fidelity TAS 5.0 API Validation  
**Tool:** Postman Collection & Environment  

<!-- {docsify-updated} -->

---

## 📘 Objective

This guide documents the **complete flow** for validating TRIRIGA Desk Booking APIs. It provides:

- Step-by-step request sequence
- JSON request/response examples
- Captured variables for chaining requests

---

## ⚙️ 0. Postman Setup

### 1. Import Postman Environment

**File:** `Tririga Reserve – ENV.postman_environment.json`

- Contains variables: username, password, locationId, userId, etc.

<figure>
  <img src="./screenshots/env.png" alt="Environment Variables in Postman">
  <figcaption><strong>Graph:</strong> Environment Variables in Postman</figcaption>
</figure>

### 2. Import Postman Collection

**File:** `DeskBooking.postman_collection.json`

- Contains all API requests in correct order.

<figure>
  <img src="./screenshots/collection.png" alt="Postman Collection overview">
  <figcaption><strong>Graph:</strong> Postman Collection overview</figcaption>
</figure>

---

# 🔑 1. Authentication

```http
GET /tririga/oslc/login?USERNAME={{username}}&PASSWORD={{password}}
```

**Expected:**
- Response: 200 OK
- Cookie: JSESSIONID (required for all subsequent requests)

<figure>
  <img src="./screenshots/JESSIONID.png" alt="successful Login response showing JSESSIONID">
  <figcaption><strong>Graph:</strong> successful Login response showing JSESSIONID</figcaption>
</figure>

---

# 👤 2. Get User Profile

```http
GET /tririga/rest/UserProfile
```

**Captured variables:**
- userId → used for bookings
- Default groupId

**Postman Test:**
```javascript
const j = pm.response.json();
pm.environment.set("userId", j.result?.id);
pm.test("UserId captured", () => pm.expect(pm.environment.get("userId")).to.exist);
```

<figure>
  <img src="./screenshots/userprofile1.png" alt="UserProfile response showing userId">
  <figcaption><strong>Graph:</strong> UserProfile response showing userId</figcaption>
</figure>

---

# 🏢 3. Floors (Verbose)

```http
POST /tririga/rest/FloorsVerbose
```

**Body:**
```json
{
    "locationId": 17692537,
    "pageNumber": 1,
    "pageSize": 100
}
```

**Captured variables:**
- locationId
- floorNumber
- groupId

**Expected:**
- Response: 200 OK
- At least one floor returned

<figure>
  <img src="./screenshots/floor.png" alt="FloorsVerbose response">
  <figcaption><strong>Graph:</strong> FloorsVerbose response</figcaption>
</figure>
---

# 📅 4. Book a Desk

```http
POST /tririga/rest/CreateBooking
```

**Body Example:**
```json
{
    "userId": "23514135",
    "deskId": "24212236",
    "bookingType": "Whole Day",
    "dateList": ["2025-08-20"]
}
```

**Expected:**
- Response: 200 OK or 201 Created
- Response contains bookingId

**Postman Test:**
```javascript
const j = pm.response.json();
pm.environment.set("bookingId", j.result?.bookingId);
pm.test("BookingId captured", () => pm.expect(pm.environment.get("bookingId")).to.exist);
```

<figure>
  <img src="./screenshots/bookingID.png" alt="Response showing bookingId">
  <figcaption><strong>Graph:</strong> response showing bookingId</figcaption>
</figure>
---

# 🔄 5. Desk Progression (Check-In / Check-Out)

```http
PUT /tririga/rest/DeskBooking?action=deskprogression
```

**Body Example:**
```json
{
    "bookingId": 30849320,
    "deskAction": 1,
    "userId": "23514135"
}
```

**deskAction Codes:**
- `1` → Check-out
- `2` → Check-in

**Expected:**
- Response: 200 OK
- Desk status updated (deskStatus in response)

---

# ✅ Flow Summary

```mermaid
graph TD
  A[Login] --> B[Get User Profile]
  B --> C[Floors Verbose]
  C --> D[Book a Desk]
  D --> E[Desk Progression]
```

**Flow Notes:**
- **Login** → Required once per session
- **Floors** → Used to capture locationId, floorNumber, groupId
- **Book Desk** → Mandatory for reservations
- **Desk Progression** → Only valid after successful booking
