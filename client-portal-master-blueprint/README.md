# 🧠 MASTER ARCHITECTURE BLUEPRINT
## Client Portal System (Skill Wanderer)

---

## 0. PURPOSE

This document defines the **authoritative system architecture** for:

- `client-portal-fe`
- `client-portal-be`
- `Keycloak (client realm)`
- Infrastructure routing (Cloudflare)

This blueprint is designed to ensure:

- deterministic behavior
- strong security boundaries
- scalable multi-client architecture
- minimal blast radius evolution

---

## 1. ENGINEERING CONSTRAINTS (NON-NEGOTIABLE)

### Controlled Scope

```text
✔ Control Keycloak client realm only
✔ Control Backend (Laravel)
✔ Control Frontend (Next.js)
✔ Control infrastructure routing (Cloudflare)
````

### Not Controlled

```text
❌ No control over external admin realm
❌ No control over global Keycloak instance
❌ No control over other teams' implementation
```

---

## 2. CORE ARCHITECTURE PRINCIPLES

### 2.1 Separation of Concerns

```text
Frontend → Presentation only
Backend  → Business logic + Auth orchestration
Keycloak → Identity provider only
Database → Authorization + domain authority
```

---

### 2.2 Trust Model

```text
Client (browser) = untrusted
Backend = single source of truth
Database = authorization authority
Keycloak = identity source (not access control)
```

---

### 2.3 Deterministic Flow

```text
Login → Session → Domain Mapping → RBAC → Data Access
```

---

### 2.4 Deny-by-Default

```text
No session → reject
No mapping → reject
Invalid role → reject
Invalid ownership → reject
```

---

## 3. SYSTEM ARCHITECTURE

### 3.1 High-Level Topology

```text
Browser
  ↓
client.skill-wanderer.com (FE - Next.js)
  ↓ HTTPS
api.skill-wanderer.com (BE - Laravel)
  ↓
PostgreSQL
  ↓
sso.skill-wanderer.com (Keycloak - client realm)
```

---

### 3.2 Domain Routing (Cloudflare)

```text
client.skill-wanderer.com → Frontend (Next.js)
api.skill-wanderer.com    → Backend (Laravel)
sso.skill-wanderer.com    → Keycloak
```

---

### 3.3 Responsibilities

#### Frontend (client-portal-fe)

```text
✔ UI rendering
✔ API consumption
✔ navigation
✔ session presence awareness

❌ no auth logic
❌ no token handling
❌ no business logic
```

---

#### Backend (client-portal-be)

```text
✔ authentication orchestration
✔ session management
✔ RBAC enforcement
✔ domain mapping
✔ business logic
✔ database access
✔ audit logging
```

---

#### Keycloak (client realm)

```text
✔ user authentication
✔ identity issuance (id_token)

❌ no authorization authority
❌ no business role enforcement
```

---

#### Database (PostgreSQL)

```text
✔ user provisioning
✔ role definition
✔ ownership enforcement
✔ system-of-record for access control
```

---

## 4. AUTHENTICATION ARCHITECTURE

### 4.1 Flow Overview

```text
FE → BE (/auth/login)
→ Keycloak (client realm)
→ BE (/auth/callback)
→ session creation
→ cookie (__session)
→ FE redirected
```

---

### 4.2 Backend Auth Endpoints

```text
GET  /auth/login
GET  /auth/callback
POST /auth/logout
GET  /auth/me
```

---

### 4.3 Key Rules

```text
✔ Browser never receives access_token
✔ Browser never receives refresh_token
✔ All token operations handled by backend
✔ Backend performs code exchange
```

---

## 5. SESSION ARCHITECTURE

### 5.1 Strategy

```text
Server-side session (mandatory)
Storage: Redis
```

---

### 5.2 Cookie Contract

```text
Cookie: __session

Properties:
✔ HttpOnly
✔ Secure
✔ SameSite=Strict
✔ Path=/
```

---

### 5.3 Session Structure

```json
{
  "sessionId": "...",
  "user": {
    "email": "...",
    "sub": "...",
    "realm": "client-portal"
  },
  "tokens": {
    "accessToken": "...",
    "refreshToken": "...",
    "expiresAt": 123456789
  }
}
```

---

### 5.4 Lifecycle

```text
create → read → refresh → expire → destroy
```

---

## 6. DOMAIN MAPPING (CRITICAL LAYER)

### 6.1 Mapping Flow

```text
session.user.email
↓
users table
↓
internal user (portalUser)
↓
role + scope
```

---

### 6.2 Rules

```text
✔ must map to exactly one active user
❌ no mapping → 403
❌ multiple mapping → 500
```

---

### 6.3 User Table Design

```sql
users
- id
- email (unique)
- role (client | admin)
- status (active | inactive)
```

---

## 7. AUTHORIZATION MODEL

### 7.1 Layered Enforcement

```text
1. Authentication (session valid)
2. Domain mapping (user exists)
3. Role check (RBAC)
4. Ownership check (resource-level)
```

---

### 7.2 RBAC Rules

```text
Role source = database (NOT Keycloak)

client → limited to own data
admin  → elevated access
```

---

### 7.3 Ownership Enforcement

```sql
SELECT * FROM projects
WHERE client_id = current_user_id
```

---

## 8. API ARCHITECTURE

### 8.1 Structure

```text
/api/v1/*
```

---

### 8.2 Segmentation

```text
/api/v1/client/*
/api/v1/admin/*
```

---

### 8.3 Rules

```text
✔ derive user from session
❌ never trust request body userId
✔ enforce role at route level
✔ enforce ownership at query level
```

---

## 9. INFRASTRUCTURE (CLOUDFLARE)

### 9.1 Responsibilities

```text
✔ DNS routing
✔ TLS termination
✔ performance optimization
✔ edge security (optional)
```

---

### 9.2 Optional Enhancements

```text
✔ Zero Trust (admin access protection)
✔ WAF rules
✔ rate limiting
```

---

## 10. OBSERVABILITY

### 10.1 Required Signals

```text
✔ correlation ID
✔ request logs
✔ auth logs
✔ session lifecycle logs
✔ DB query tracing (optional)
```

---

### 10.2 Log Example

```json
{
  "event": "auth_success",
  "user": "test@reltroner.com",
  "path": "/api/dashboard",
  "correlationId": "..."
}
```

---

## 11. SECURITY MODEL

### 11.1 Core Principles

```text
✔ zero trust client
✔ server-side authority
✔ explicit failure
✔ no token leakage
✔ no implicit behavior
```

---

### 11.2 Critical Rules

```text
✔ all access via backend
✔ no direct DB access from FE
✔ no reliance on Keycloak roles
✔ no hidden session state
```

---

### 11.3 Keycloak Hardening

```text
✔ replace temporary admin
✔ enforce strong passwords
✔ enable PKCE (S256)
✔ restrict redirect URIs
```

---

## 12. FAILURE MODEL

### Deterministic Outcomes

```text
No session           → redirect login
Invalid session      → session cleared + redirect
No user mapping      → 403
Insufficient role    → 403
Ownership violation  → 404
DB failure           → 500
```

---

## 13. SYSTEM INVARIANTS

```text
✔ Backend is the only authority
✔ Session is required for all protected routes
✔ Role comes from DB, not Keycloak
✔ All access is scoped
✔ System is deterministic
```

---

## 14. FINAL STATE

```text
✔ FE = UI only
✔ BE = brain (auth + logic + control)
✔ Keycloak = identity provider
✔ DB = access authority
✔ Redis = session layer
✔ Cloudflare = edge control
```

---

## 15. READINESS LEVEL

```text
Functional readiness: HIGH
Security posture: STRONG
Scalability: READY (with Redis + stateless BE)
Production readiness: CONDITIONAL (infra + deployment required)
```

---

## 16. CONCLUSION

This architecture ensures:

```text
✔ predictable behavior
✔ strong security guarantees
✔ clean separation of concerns
✔ scalability across multiple clients
✔ minimal blast radius for future changes
```

---

## 🔥 ONE-LINE SUMMARY

```text
Backend owns everything critical.
Frontend only renders.
Keycloak only authenticates.
Database decides access.
```
