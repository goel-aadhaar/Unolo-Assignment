# QUESTIONS

## Technical Questions

---

### 1. If this app had 10,000+ employees checking in simultaneously, what would break first? How would you fix it?

If this app had **10,000+ employees checking in at the same time**, the **database would be the first bottleneck**.

**What would break:**

* Database concurrency limits (especially with SQLite)
* Race conditions allowing multiple active check-ins per employee
* Database lock errors and request timeouts
* Slow queries without proper indexing

SQLite is not designed for heavy concurrent writes, so it would struggle under this load.

**How to fix it:**

* Migrate to **PostgreSQL or MySQL** for better concurrency handling
* Add proper **indexes** (employee_id, checkin_time, status)
* Enforce **database-level constraints** to prevent duplicate active check-ins
* Implement **horizontal scaling** for backend services
* Use **read replicas (master–slave architecture)** for dashboards and reports
* Optionally use **sharding by employee_id** if the system scales further

---

### 2. The current JWT implementation has a security issue. What is it and how would you improve it?

**Security issue:**

* The JWT payload contains the **password (hashed)**, which is sensitive data
* JWT is stored in **localStorage**, making it vulnerable to **XSS attacks**
* JWT payload is larger than necessary

**Why this is risky:**

* If the token is leaked, sensitive information is exposed
* JWTs should only carry identity and authorization claims

**Improvements:**

* Never store passwords in JWT, even hashed
* Keep JWT payload minimal (user_id, role)
* Store tokens in **HttpOnly, Secure cookies**
* Use **access tokens + refresh tokens**

  * Short-lived access tokens
  * Long-lived refresh tokens for session renewal

---

### 3. How would you implement offline check-in support?

The idea is to **store check-ins locally when offline and sync later**.

**Approach:**

* Detect offline/online status
* When offline:

  * Store check-in data locally (client_id, latitude, longitude, timestamp)
  * Mark the check-in as pending
* When the user comes back online:

  * Send pending check-ins to the backend
  * Backend validates and stores them safely

This allows employees to check in without internet and sync data once connectivity is restored.

---

## Theory / Research Questions

---

### 4. Explain the difference between SQL and NoSQL databases. Which would you recommend and why?

**SQL Databases:**

* Fixed schema (tables, columns)
* Relationships using foreign keys
* Strong ACID transactions (high consistency)
* Powerful JOIN operations
* Primarily vertical scaling

**NoSQL Databases:**

* Flexible / schema-less
* Data stored as documents, key-value, or wide-column
* Easier horizontal scaling
* Weak or no JOINs (handled in application logic)
* Often eventual consistency

**Recommendation:**
For the Field Force Tracker application, **SQL is the better choice**.

**Reason:**

* Data is highly structured
* Strong relationships between users, clients, and check-ins
* Heavy reporting and aggregation queries
* Data consistency is critical

NoSQL can be used for logs or analytics but not for core business data.

---

### 5. What is the difference between authentication and authorization?

**Authentication (Who are you?):**

* Verifies the identity of a user
* Implemented using login and JWT verification

**Authorization (What are you allowed to do?):**

* Controls access to resources
* Implemented using role checks (manager vs employee)
* Also enforced through client assignment validation

Both are clearly separated and correctly implemented in this codebase.

---

### 6. What is a race condition? Any in this codebase? How would you prevent them?

**Race condition:**
A race condition occurs when multiple concurrent requests access shared data and the result depends on execution order.

**Example in this codebase:**

* Two simultaneous check-in requests
* Both detect no active check-in
* Both create a new active check-in

**Prevention:**

* Enforce **database-level constraints** (one active check-in per employee)
* Avoid relying only on application-level checks
* Make APIs idempotent to handle retries
* Use transactions where required

---
