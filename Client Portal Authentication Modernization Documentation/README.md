# Client Portal Authentication Modernization Documentation

## End-to-End Resolution: Keycloak Multi-Realm Token Validation, Same-Origin API Routing, and Dashboard Authorization

---

## 1. Executive Summary

This document describes the end-to-end work completed to modernize the Client Portal authentication and API access flow.

The goal was to make the Client Portal Backend validate real Keycloak access tokens from two trusted realms, support same-origin API access through `client.skill-wanderer.com/api/*`, remove frontend dependency on `api.skill-wanderer.com`, and migrate the dashboard endpoint from legacy session-based backend authentication to the new Keycloak JWT-based authorization model.

The completed work covers:

1. Keycloak multi-realm token validation.
2. Accepting access tokens from the `client-portal` realm.
3. Accepting access tokens from the `skill-wanderer-admin` realm only when the token contains the `client` realm role.
4. Backend deployment to Rancher using the new image.
5. Same-origin API routing through `https://client.skill-wanderer.com/api/*`.
6. Frontend runtime API base migration away from `https://api.skill-wanderer.com`.
7. Dashboard API requests sending `Authorization: Bearer <access_token>`.
8. Backend dashboard route migration from legacy session authentication to `keycloak.token`.

Final result:

```txt
Client Portal frontend:
https://client.skill-wanderer.com

Same-origin API:
https://client.skill-wanderer.com/api/*

Backend:
Laravel Client Portal BE

Identity Provider:
Keycloak

Accepted realms:
- client-portal
- skill-wanderer-admin, only with realm role client

Final dashboard result:
Dashboard loads successfully with authenticated API data.
```

---

## 2. Original Problem

The Client Portal Backend needed to validate access tokens from Keycloak instead of relying on legacy runtime/session authentication only.

The required identity model had two trusted realms:

```txt
1. client-portal
2. skill-wanderer-admin
```

The expected authorization rule was:

```txt
client-portal realm:
- Token is accepted if valid, signed, not expired, and audience contains client-portal-be.

skill-wanderer-admin realm:
- Token is accepted only if valid, signed, not expired, audience contains client-portal-be, and realm_access.roles contains client.
```

The broader production requirement also included removing browser-facing API dependency on:

```txt
https://api.skill-wanderer.com
```

and moving frontend API calls to same-origin:

```txt
https://client.skill-wanderer.com/api/*
```

---

## 3. Problems Identified During Execution

### 3.1 Keycloak Tokens Did Not Initially Contain the Required Backend Audience

The backend should only accept access tokens intended for the backend resource server.

Required audience:

```json
"aud": ["client-portal-be"]
```

Initially, tokens from the frontend client were valid Keycloak tokens, but not necessarily intended for the backend. Accepting any valid token would have created an authorization boundary problem.

### 3.2 Admin Realm Token Needed Role-Based Restriction

The `skill-wanderer-admin` realm should not be accepted broadly.

It needed this additional rule:

```txt
realm_access.roles must contain client
```

This means an admin-realm token is accepted only when it explicitly carries the allowed business role.

### 3.3 Backend Initially Deployed With Old Runtime Metadata

After the backend image was updated, the response still showed:

```txt
X-Deployment-ID: client-portal-be-0.1.6
```

This was fixed by updating the Rancher ConfigMap to:

```txt
BE_DEPLOYMENT_ID=client-portal-be-sha-c8f77e8
```

and later:

```txt
BE_DEPLOYMENT_ID=client-portal-be-sha-4ab8871
```

### 3.4 Same-Origin API Initially Returned Next.js 404

Before the frontend proxy was implemented, this request:

```txt
https://client.skill-wanderer.com/api/v1/auth/me
```

returned a Next.js/OpenNext 404 response.

That proved `/api/*` was still being handled by the frontend Worker instead of reaching Laravel.

### 3.5 Cloudflare Tunnel Could Not Directly Add `client.skill-wanderer.com/api/*`

Cloudflare rejected adding a tunnel route directly for `client.skill-wanderer.com/api/*` because the hostname was already managed by the frontend Worker.

Therefore, the solution was not to force a tunnel route, but to add a Next.js API route proxy inside the frontend application itself.

### 3.6 Frontend Dashboard Initially Did Not Send Authorization Header

After same-origin routing worked, dashboard calls still returned `401`.

Investigation showed the browser request to:

```txt
/api/v1/client/dashboard
```

initially did not send:

```txt
Authorization: Bearer <access_token>
```

That meant the frontend dashboard fetch logic needed to attach the active OIDC access token.

### 3.7 Dashboard Backend Route Still Used Legacy Middleware

After the frontend started sending the Bearer token, the dashboard endpoint still returned `401`.

Backend route inspection showed:

```txt
GET api/v1/client/dashboard
⇂ dashboard.audit
⇂ bearer.validate
⇂ session.load
⇂ rbac:client
```

This proved the dashboard route was still protected by the old bearer/session stack.

The route needed to move to:

```txt
GET api/v1/client/dashboard
⇂ dashboard.audit
⇂ keycloak.token
```

---

## 4. Final Architecture

### 4.1 Identity Architecture

```txt
Keycloak
├── Realm: client-portal
│   ├── Client: client-portal-fe
│   └── Client: client-portal-be
│
└── Realm: skill-wanderer-admin
    └── Client: skill-wanderer-admin
```

### 4.2 Backend Authorization Boundary

The backend accepts only tokens that satisfy:

```txt
Valid JWT signature
AND valid issuer
AND valid expiration
AND aud contains client-portal-be
AND allowed realm rule passes
```

Allowed realm rules:

```txt
Realm: client-portal
→ accepted if token is valid and audience contains client-portal-be.

Realm: skill-wanderer-admin
→ accepted only if token is valid, audience contains client-portal-be, and realm_access.roles contains client.
```

### 4.3 Final Runtime Request Flow

```txt
Browser
  ↓
https://client.skill-wanderer.com/dashboard
  ↓
Frontend app obtains OIDC access_token
  ↓
Frontend calls:
https://client.skill-wanderer.com/api/v1/client/dashboard
  ↓
Next.js / OpenNext API proxy route:
app/api/[...path]/route.ts
  ↓
Upstream:
https://client-portal-api.skill-wanderer.com
  ↓
Cloudflare Tunnel
  ↓
Kubernetes Service:
client-portal-be.client-portal:80
  ↓
Laravel Backend
  ↓
Middleware:
dashboard.audit
keycloak.token
  ↓
Dashboard response:
200 OK
```

---

## 5. Keycloak Configuration

### 5.1 Realm: `client-portal`

The frontend client issues tokens:

```txt
client-portal-fe
```

The backend resource server audience is:

```txt
client-portal-be
```

The token must contain:

```json
"aud": [
  "client-portal-be",
  "account"
]
```

Final verified token shape:

```json
{
  "iss": "https://sso.skill-wanderer.com/realms/client-portal",
  "aud": ["client-portal-be", "account"],
  "azp": "client-portal-fe",
  "typ": "Bearer"
}
```

### 5.2 Realm: `skill-wanderer-admin`

The admin token must contain:

```json
"aud": [
  "client-portal-be",
  "account"
]
```

and:

```json
"realm_access": {
  "roles": [
    "client"
  ]
}
```

Final verified token shape:

```json
{
  "iss": "https://sso.skill-wanderer.com/realms/skill-wanderer-admin",
  "aud": ["client-portal-be", "realm-management", "account"],
  "azp": "skill-wanderer-admin",
  "typ": "Bearer",
  "realm_access": {
    "roles": [
      "offline_access",
      "client",
      "uma_authorization",
      "default-roles-skill-wanderer-admin",
      "Admin"
    ]
  }
}
```

### 5.3 Why Audience Mapping Was Required

The backend must not accept a token merely because it is signed by Keycloak.

The token also needs to be intended for the backend.

That is why the backend validates:

```txt
aud contains client-portal-be
```

This prevents the backend from accepting tokens issued only for unrelated applications.

### 5.4 Why Custom Audience Was Needed for Admin Realm

The `client-portal-be` client exists in the `client-portal` realm, not in the `skill-wanderer-admin` realm.

Because Keycloak client audience dropdowns are realm-local, the admin realm mapper needed:

```txt
Included Custom Audience:
client-portal-be
```

rather than selecting a local client from the dropdown.

---

## 6. Backend Implementation

### 6.1 Environment Configuration

The backend uses these Keycloak settings:

```env
KEYCLOAK_BASE_URL=https://sso.skill-wanderer.com
KEYCLOAK_ALLOWED_REALMS=client-portal,skill-wanderer-admin
KEYCLOAK_EXPECTED_AUDIENCE=client-portal-be
KEYCLOAK_ALLOWED_ALGORITHMS=RS256
KEYCLOAK_ADMIN_REALM=skill-wanderer-admin
KEYCLOAK_ADMIN_REQUIRED_REALM_ROLE=client
KEYCLOAK_JWKS_CACHE_TTL_SECONDS=3600
KEYCLOAK_CLOCK_LEEWAY_SECONDS=60
```

Production ConfigMap final value:

```txt
APP_URL=https://client.skill-wanderer.com
BE_DEPLOYMENT_ID=client-portal-be-sha-4ab8871
CORS_ALLOWED_ORIGINS=https://client.skill-wanderer.com
KEYCLOAK_BASE_URL=https://sso.skill-wanderer.com
KEYCLOAK_ALLOWED_REALMS=client-portal,skill-wanderer-admin
KEYCLOAK_EXPECTED_AUDIENCE=client-portal-be
KEYCLOAK_ADMIN_REALM=skill-wanderer-admin
KEYCLOAK_ADMIN_REQUIRED_REALM_ROLE=client
```

### 6.2 Backend Components Added

The backend implementation added Keycloak security components such as:

```txt
App\Security\Keycloak\KeycloakPrincipal
App\Security\Keycloak\KeycloakJwksProvider
App\Security\Keycloak\KeycloakTokenValidator
App\Http\Middleware\EnsureKeycloakBearerToken
```

The middleware alias registered:

```txt
keycloak.token
```

### 6.3 JWT Validation Rules

The backend validates:

```txt
1. Authorization header exists.
2. Header starts with Bearer.
3. JWT has valid compact token shape.
4. JWT algorithm is allowed.
5. Issuer is exactly one of the allowed Keycloak realm issuers.
6. JWKS is loaded from the matching realm.
7. Signature is valid.
8. Token is not expired.
9. Audience contains client-portal-be.
10. If issuer is skill-wanderer-admin, realm_access.roles contains client.
```

### 6.4 Auth Principal

After successful validation, the middleware attaches a Keycloak principal to the request:

```php
$request->attributes->set('keycloak_principal', $principal);
```

Principal fields include:

```txt
sub
issuer
realm
preferred_username
email
name
audiences
realm_roles
raw_claims
```

---

## 7. Backend Deployment

### 7.1 First Backend Deployment

Initial new backend image:

```txt
ghcr.io/skill-wanderer/client-portal-be:sha-c8f77e8
```

Verified in Rancher:

```txt
Image: ghcr.io/skill-wanderer/client-portal-be:sha-c8f77e8
Ready: 1/1
Restarts: 0
```

### 7.2 Final Backend Dashboard Fix Deployment

Final backend image after dashboard route migration:

```txt
ghcr.io/skill-wanderer/client-portal-be:sha-4ab8871
```

Final deployment metadata:

```txt
X-Deployment-ID: client-portal-be-sha-4ab8871
```

### 7.3 Final Route Middleware Verification

In the production pod:

```bash
php artisan route:list --path=api/v1/client/dashboard -v
```

Final output:

```txt
GET|HEAD api/v1/client/dashboard
⇂ dashboard.audit
⇂ keycloak.token
```

This confirmed that the dashboard route no longer uses:

```txt
bearer.validate
session.load
rbac:client
```

---

## 8. Frontend Same-Origin API Routing

### 8.1 Problem

The frontend was deployed on:

```txt
https://client.skill-wanderer.com
```

but API runtime originally pointed to:

```txt
https://api.skill-wanderer.com
```

The requirement was to stop using `api.skill-wanderer.com` as the browser-facing API base.

### 8.2 Cloudflare Constraint

The hostname:

```txt
client.skill-wanderer.com
```

was already managed by the frontend Worker.

Directly adding a Cloudflare Tunnel route for:

```txt
client.skill-wanderer.com/api/*
```

failed because Cloudflare reported that the hostname was already managed by Workers.

### 8.3 Solution

A frontend route proxy was added:

```txt
app/api/[...path]/route.ts
```

This route receives same-origin requests:

```txt
https://client.skill-wanderer.com/api/*
```

and forwards them to:

```txt
https://client-portal-api.skill-wanderer.com
```

### 8.4 Frontend Runtime Variables

Final runtime variables:

```txt
API_BASE_URL=https://client.skill-wanderer.com
NEXT_PUBLIC_API_BASE_URL=https://client.skill-wanderer.com
API_UPSTREAM_URL=https://client-portal-api.skill-wanderer.com
APP_URL=https://client.skill-wanderer.com
NEXT_PUBLIC_APP_URL=https://client.skill-wanderer.com
```

### 8.5 Why `client-portal-api.skill-wanderer.com` Still Exists

The browser no longer uses:

```txt
https://api.skill-wanderer.com
```

However, the frontend proxy still needs an upstream backend URL:

```txt
https://client-portal-api.skill-wanderer.com
```

Therefore, `client-portal-api.skill-wanderer.com` should remain active as the backend upstream for the frontend proxy.

---

## 9. Frontend Authorization Header Fix

### 9.1 Problem

After same-origin proxy routing worked, the dashboard still returned:

```txt
401 Unauthorized
```

The browser request to:

```txt
/api/v1/client/dashboard
```

initially did not include:

```txt
Authorization: Bearer <access_token>
```

### 9.2 Solution

The frontend dashboard API request was updated to send the active OIDC access token.

Final request header includes:

```txt
authorization: Bearer ey...
```

### 9.3 Result

The dashboard request reached Laravel with a valid access token.

This confirmed that the frontend auth header issue was resolved.

---

## 10. Dashboard Backend Route Migration

### 10.1 Problem

Even after the frontend sent the Authorization header, the dashboard endpoint still returned:

```json
{
  "success": false,
  "error": {
    "reason": "INACTIVE_BEARER_TOKEN",
    "failure_code": "BE_BEARER_TOKEN_INVALID",
    "runtime_boundary": "backend_auth"
  }
}
```

This showed that the route was still using legacy backend auth.

Route-list confirmed:

```txt
GET api/v1/client/dashboard
⇂ dashboard.audit
⇂ bearer.validate
⇂ session.load
⇂ rbac:client
```

### 10.2 Solution

The route was migrated to the new Keycloak middleware:

```txt
GET api/v1/client/dashboard
⇂ dashboard.audit
⇂ keycloak.token
```

The controller now reads identity from:

```php
$request->attributes->get('keycloak_principal')
```

The dashboard route no longer requires:

```txt
X-Session-Id
```

when a valid Keycloak Bearer token is present.

### 10.3 Result

Final production test:

```powershell
curl.exe -i https://client.skill-wanderer.com/api/v1/client/dashboard `
  -H "Authorization: Bearer <valid-token>" `
  -H "X-Contract-Version: 2026-05-21"
```

Returned:

```txt
HTTP/1.1 200 OK
x-powered-by: PHP/8.4.22
x-deployment-id: client-portal-be-sha-4ab8871
```

Response:

```json
{
  "success": true,
  "data": {
    "user": {
      "id": "6f40167f-5c06-4aad-a635-0c625a426171",
      "email": "test@reltroner.com"
    },
    "dashboard": {
      "status": "ready"
    },
    "summary": {
      "activeProjects": 0,
      "pendingActions": 0,
      "unreadMessages": 0,
      "recentFiles": 0
    },
    "projects": [],
    "tasks": [],
    "files": []
  }
}
```

---

## 11. Verification Evidence

### 11.1 No Token

Request:

```bash
curl -i http://127.0.0.1:8000/api/v1/client/dashboard
```

Result:

```txt
HTTP/1.1 401 Unauthorized
X-Deployment-ID: client-portal-be-sha-4ab8871
{"message":"Unauthenticated."}
```

This is correct.

### 11.2 Valid Client Portal Token

Request:

```powershell
curl.exe -i https://client.skill-wanderer.com/api/v1/client/dashboard `
  -H "Authorization: Bearer <valid-client-portal-access-token>" `
  -H "X-Contract-Version: 2026-05-21"
```

Result:

```txt
HTTP/1.1 200 OK
x-powered-by: PHP/8.4.22
x-deployment-id: client-portal-be-sha-4ab8871
```

This is correct.

### 11.3 Dashboard Browser Verification

Browser dashboard final state:

```txt
Welcome back
Email: test@reltroner.com
Role: AUTHENTICATED
```

Network request:

```txt
Request URL:
https://client.skill-wanderer.com/api/v1/client/dashboard

Status:
200 OK
```

This confirms the browser dashboard successfully loads authenticated backend data.

---

## 12. Final Result

The final result is a complete production authentication and routing upgrade.

### Completed

```txt
✅ Keycloak multi-realm token validation
✅ client-portal realm token accepted
✅ skill-wanderer-admin realm token accepted only with role client
✅ Backend deployed to Rancher
✅ Backend image updated to sha-4ab8871
✅ Same-origin /api/* works through client.skill-wanderer.com
✅ Frontend runtime no longer uses api.skill-wanderer.com as API base
✅ Dashboard sends Authorization: Bearer access token
✅ Dashboard backend route uses keycloak.token
✅ Legacy session auth removed from dashboard route
✅ Dashboard loads successfully in browser
```

### Final Production Behavior

```txt
https://client.skill-wanderer.com/dashboard
→ loads frontend dashboard

https://client.skill-wanderer.com/api/v1/client/dashboard
→ goes through frontend proxy
→ reaches Laravel backend
→ validates Keycloak access token
→ returns dashboard data
```

### Final Security Boundary

```txt
The backend only accepts tokens that:
- are signed by trusted Keycloak realm keys
- have valid issuer
- are not expired
- contain aud=client-portal-be
- satisfy realm-specific authorization rules
```

For `skill-wanderer-admin`:

```txt
role client is mandatory
```

For `client-portal`:

```txt
valid audience and issuer are sufficient
```

---

## 13. Operational Notes

### 13.1 Do Not Remove `client-portal-api.skill-wanderer.com`

This hostname is still used as the upstream target for the frontend proxy.

Current purpose:

```txt
Frontend proxy upstream:
API_UPSTREAM_URL=https://client-portal-api.skill-wanderer.com
```

### 13.2 Avoid Using `api.skill-wanderer.com` in Browser Runtime

The browser-facing API base should remain:

```txt
https://client.skill-wanderer.com
```

### 13.3 Token Handling Rule

Never expose, log, or paste access tokens, refresh tokens, or ID tokens into public channels.

Access tokens should only be used locally for testing and should be treated as sensitive credentials.

### 13.4 Deployment Metadata

Backend deployment metadata should match the active backend image:

```txt
BE_DEPLOYMENT_ID=client-portal-be-sha-4ab8871
```

Frontend deployment metadata can remain separate because frontend and backend are separate services.

The frontend proxy must not forward frontend deployment identity as backend deployment identity.

---

## 14. Summary: Problem → Solution → Result

### Problem

The Client Portal needed secure, production-grade Keycloak token validation across two realms and same-origin API routing, but the system was split across frontend Worker, Cloudflare Tunnel, Rancher backend, legacy session middleware, and multiple domains.

### Solution

The system was upgraded by:

```txt
1. Configuring Keycloak audience mapping.
2. Requiring aud=client-portal-be.
3. Requiring role client for skill-wanderer-admin tokens.
4. Implementing Laravel JWKS/JWT validation.
5. Deploying backend image updates to Rancher.
6. Adding same-origin API proxy in the frontend.
7. Updating frontend runtime API base to client.skill-wanderer.com.
8. Ensuring dashboard fetch sends Authorization Bearer token.
9. Migrating dashboard backend route from legacy auth to keycloak.token.
```

### Result

The Client Portal now has a clean, auditable authentication flow:

```txt
User logs in with Keycloak
→ frontend receives access token
→ frontend calls same-origin API
→ backend validates token cryptographically
→ backend enforces realm/audience/role rules
→ dashboard returns authenticated data
```

The production dashboard now loads successfully.
