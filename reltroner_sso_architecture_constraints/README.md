# Reltroner SSO Migration — Architecture Constraint Specification

**Document Type:** Architecture Constraint / Scope Lock Requirement  
**Project:** Reltroner Learning Academy / Reltroner Identity  
**Primary Domains:** `reltroner.com`, `lms.reltroner.com`, `sso.reltroner.com`  
**Related Systems:** Cloudflare DNS, Cloudflare Tunnel, Cloudflare Pages, Keycloak, OIDC, Next.js LMS, Rancher/Kubernetes  
**Purpose:** Prevent scope creep, accidental infrastructure changes, and cross-domain production impact during SSO-related engineering work.  
**Status:** Scope-locked baseline after successful emergency SSO migration.

---

## 1. Purpose of This Document

This document defines the **locked architecture constraints** for Reltroner SSO and LMS authentication work.

The goal is to make sure any engineer working on this system understands:

1. What is allowed.
2. What is forbidden.
3. What must remain unchanged.
4. What must be verified before work is considered complete.
5. Which systems are inside the engineering scope.
6. Which systems are outside the engineering scope.
7. Which decisions require owner approval before execution.

This document exists to prevent engineers from accidentally exceeding the intended scope.

---

## 2. Fundamental Case Context

Reltroner Learning Academy previously used the shared Skill-Wanderer SSO authority:

```text
https://sso.skill-wanderer.com/realms/reltroner
```

The required final state was to isolate Reltroner authentication under the Reltroner-owned SSO authority:

```text
https://sso.reltroner.com/realms/reltroner
```

The migration was completed without creating a new Keycloak instance and without disrupting existing Skill-Wanderer or Chanhdao identity routes.

The final working architecture is:

```text
https://lms.reltroner.com
        |
        | OIDC Authorization Code + PKCE
        v
https://sso.reltroner.com/realms/reltroner
        |
        v
Cloudflare Tunnel: blog-skill-wanderer
        |
        v
http://keycloak.keycloak:8080
        |
        v
Keycloak Realm: reltroner
```

---

## 3. Core Architecture Principle

### 3.1 Principle Statement

Reltroner must have its own public SSO identity boundary while reusing the existing shared Keycloak backend service.

### 3.2 What This Means

Reltroner owns the public authentication authority:

```text
https://sso.reltroner.com/realms/reltroner
```

But Reltroner does **not** own or operate a separate Keycloak server for this scope.

The internal Keycloak service remains:

```text
http://keycloak.keycloak:8080
```

The public routing is handled through the existing active Cloudflare Tunnel:

```text
blog-skill-wanderer
```

---

## 4. Locked Architecture

### 4.1 Final Public LMS Domain

```text
https://lms.reltroner.com
```

This is the production frontend domain for the LMS.

### 4.2 Final Public SSO Domain

```text
https://sso.reltroner.com
```

This is the public SSO hostname for Reltroner.

### 4.3 Final OIDC Authority

```text
https://sso.reltroner.com/realms/reltroner
```

This is the only valid OIDC authority for the Reltroner LMS production environment.

### 4.4 Final Keycloak Internal Service

```text
http://keycloak.keycloak:8080
```

This internal service must be reused. Engineers must not create a new Keycloak server for this scope.

### 4.5 Final Cloudflare Tunnel

```text
blog-skill-wanderer
```

This is the active tunnel that must serve `sso.reltroner.com`.

### 4.6 Final Cloudflare Tunnel Route

```text
Hostname: sso.reltroner.com
Path: *
Service: http://keycloak.keycloak:8080
Tunnel: blog-skill-wanderer
```

### 4.7 Final LMS OIDC Environment Variables

Production environment must use:

```env
NEXT_PUBLIC_OIDC_AUTHORITY=https://sso.reltroner.com/realms/reltroner
NEXT_PUBLIC_OIDC_CLIENT_ID=lms-reltroner
NEXT_PUBLIC_OIDC_REDIRECT_URI=https://lms.reltroner.com/auth/callback
NEXT_PUBLIC_OIDC_POST_LOGOUT_REDIRECT_URI=https://lms.reltroner.com
NEXT_PUBLIC_OIDC_SCOPE=openid profile email
```

---

## 5. Architecture Invariants

The following conditions must always remain true.

### 5.1 Identity Invariants

| ID | Invariant | Required State |
|---|---|---|
| ID-01 | Reltroner LMS must use Reltroner SSO authority | `https://sso.reltroner.com/realms/reltroner` |
| ID-02 | Reltroner LMS must not use Skill-Wanderer SSO authority | No `https://sso.skill-wanderer.com/realms/reltroner` in production config |
| ID-03 | Reltroner realm must remain `reltroner` | Do not rename realm |
| ID-04 | Reltroner LMS client must remain `lms-reltroner` | Do not change client ID unless explicitly approved |
| ID-05 | Login must return to LMS callback | `https://lms.reltroner.com/auth/callback` |
| ID-06 | Logout must return to LMS guest state | `https://lms.reltroner.com` |

### 5.2 Infrastructure Invariants

| ID | Invariant | Required State |
|---|---|---|
| INF-01 | Keycloak backend service must be reused | `http://keycloak.keycloak:8080` |
| INF-02 | No new Keycloak instance for this scope | Forbidden |
| INF-03 | No new Kubernetes namespace for Keycloak | Forbidden |
| INF-04 | No local laptop-based Cloudflare connector | Forbidden |
| INF-05 | Use existing active tunnel | `blog-skill-wanderer` |
| INF-06 | `reltroner-sso` tunnel must not be used | Deprecated / invalid for this scope |
| INF-07 | Skill-Wanderer routes must not be modified | Preserve existing behavior |
| INF-08 | Chanhdao routes must not be modified | Preserve existing behavior |

### 5.3 DNS Invariants

| ID | Invariant | Required State |
|---|---|---|
| DNS-01 | `reltroner.com` DNS authority must remain Cloudflare | Cloudflare authoritative nameservers |
| DNS-02 | `lms.reltroner.com` must resolve to Cloudflare Pages target | `lms-fe-3gl.pages.dev` |
| DNS-03 | `sso.reltroner.com` must be tunnel-managed by `blog-skill-wanderer` | Not a standalone inactive tunnel |
| DNS-04 | `hrm.reltroner.com` records must not be deleted | Preserve A and AAAA records |
| DNS-05 | Email records must not be deleted | Preserve MX, SPF, DKIM, DMARC, Resend |
| DNS-06 | No unrelated DNS cleanup during SSO work | Forbidden |

---

## 6. Scope Lock

### 6.1 In Scope

Engineers may work on the following items only when the task is explicitly related to Reltroner LMS SSO:

```text
Cloudflare DNS records for reltroner.com
Cloudflare Tunnel route for sso.reltroner.com
Cloudflare Pages production environment variables for lms-fe
LMS OIDC frontend configuration
Keycloak reltroner realm client settings
OIDC login/callback/logout verification
Documentation and operational runbook updates
```

### 6.2 Out of Scope

The following items are outside scope and must not be touched unless a new approved requirement is created:

```text
Creating a new Keycloak server
Creating a new Kubernetes namespace for Keycloak
Changing Skill-Wanderer SSO routes
Changing Chanhdao SSO routes
Changing existing Keycloak realms unrelated to Reltroner
Changing Rancher cluster-level configuration
Changing Cloudflare connector deployment
Installing cloudflared on a personal laptop for production
Changing registrar ownership
Migrating email provider
Changing HRM infrastructure
Changing unrelated DNS records
Changing production secrets without approval
Changing Google OAuth configuration unless Google login is explicitly required
```

---

## 7. Use Case Constraints

## UC-01 — User Logs In to Reltroner LMS

### Goal

Allow a Reltroner LMS user to authenticate through Reltroner SSO.

### Actor

```text
Reltroner LMS user
```

### Expected Flow

```text
1. User opens https://lms.reltroner.com
2. User clicks Login
3. LMS redirects to https://sso.reltroner.com/realms/reltroner/protocol/openid-connect/auth
4. User enters credentials
5. Keycloak authenticates user
6. User returns to https://lms.reltroner.com/auth/callback
7. LMS displays authenticated user state
```

### Required Constraints

```text
MUST use sso.reltroner.com
MUST use realm reltroner
MUST use client lms-reltroner
MUST use redirect URI https://lms.reltroner.com/auth/callback
MUST NOT redirect to sso.skill-wanderer.com
MUST NOT redirect to sso.chanhdao.vn
```

### Acceptance Criteria

```text
Login button redirects to sso.reltroner.com
User can authenticate
Callback succeeds
Navbar shows authenticated user email
No Skill-Wanderer SSO domain appears in the Reltroner LMS login flow
```

---

## UC-02 — User Logs Out from Reltroner LMS

### Goal

Allow authenticated user to log out and return LMS to guest state.

### Actor

```text
Authenticated Reltroner LMS user
```

### Expected Flow

```text
1. User is logged in at https://lms.reltroner.com
2. Navbar shows user email and Logout
3. User clicks Logout
4. OIDC logout/session cleanup runs
5. User returns to https://lms.reltroner.com
6. Navbar returns to Login state
```

### Required Constraints

```text
MUST use Reltroner OIDC authority
MUST return to https://lms.reltroner.com
MUST clear authenticated LMS state
MUST NOT redirect user to sso.skill-wanderer.com
MUST NOT leave stale authenticated UI state
```

### Acceptance Criteria

```text
Logout succeeds
LMS returns to guest state
Login button is visible again
No stale user email remains in navbar
```

---

## UC-03 — Engineer Updates LMS OIDC Configuration

### Goal

Allow controlled updates to LMS OIDC configuration.

### Actor

```text
Frontend / Platform Engineer
```

### Allowed Files

```text
.env.example
src/lib/auth/account-url.ts
src/lib/auth/oidc-config.ts
Cloudflare Pages production variables
```

### Required Constraints

```text
MUST set NEXT_PUBLIC_OIDC_AUTHORITY to https://sso.reltroner.com/realms/reltroner
MUST preserve client ID lms-reltroner
MUST preserve callback URI https://lms.reltroner.com/auth/callback
MUST preserve post logout redirect URI https://lms.reltroner.com
MUST run build before deployment
MUST verify production redirect after deployment
```

### Forbidden Actions

```text
MUST NOT hardcode sso.skill-wanderer.com as fallback
MUST NOT change client ID without approval
MUST NOT change realm name without approval
MUST NOT bypass OIDC PKCE flow
MUST NOT store secrets in public frontend variables
```

### Acceptance Criteria

```text
ripgrep search shows no old Reltroner Skill-Wanderer authority fallback
npm run build succeeds
Cloudflare Pages production variable is updated
Production LMS redirects to sso.reltroner.com
```

---

## UC-04 — Engineer Adds or Repairs SSO Tunnel Route

### Goal

Allow controlled repair or recreation of `sso.reltroner.com` tunnel route.

### Actor

```text
Platform / Cloud Engineer
```

### Required Route

```text
Tunnel: blog-skill-wanderer
Hostname: sso.reltroner.com
Path: *
Service: http://keycloak.keycloak:8080
```

### Required Constraints

```text
MUST use existing blog-skill-wanderer tunnel
MUST route to http://keycloak.keycloak:8080
MUST keep path as *
MUST NOT create a new tunnel unless explicitly approved
MUST NOT use inactive tunnel reltroner-sso
MUST NOT install cloudflared on local laptop for production
MUST NOT modify sso.skill-wanderer.com route
MUST NOT modify sso.chanhdao.vn route
```

### Acceptance Criteria

```text
sso.reltroner.com appears under blog-skill-wanderer routes
https://sso.reltroner.com loads Keycloak login page
No Cloudflare Error 1033
No Cloudflare DNS duplicate-host error
```

---

## UC-05 — Engineer Migrates or Repairs DNS

### Goal

Allow controlled DNS repair without breaking dependent services.

### Actor

```text
Cloud / DNS Engineer
```

### DNS Records That Must Be Preserved

```text
lms.reltroner.com
hrm.reltroner.com
www.reltroner.com
reltroner.com apex A record
Hostinger MX records
Hostinger SPF record
DMARC record
Hostinger DKIM CNAME records
Resend MX/TXT/DKIM records
Google site verification TXT
```

### Required Constraints

```text
MUST take inventory before changing DNS
MUST preserve email records
MUST preserve HRM records
MUST preserve LMS CNAME
MUST validate DNS after changes
MUST NOT use Cloudflare auto-scan as the only source of truth
MUST NOT delete unknown records without ownership confirmation
MUST NOT perform registrar transfer when DNS connect is sufficient
```

### Acceptance Criteria

```text
reltroner.com nameservers point to Cloudflare
lms.reltroner.com still resolves
hrm.reltroner.com still resolves
email records still exist
sso.reltroner.com loads Keycloak
```

---

## UC-06 — Engineer Verifies OIDC Discovery Metadata

### Goal

Ensure Reltroner OIDC metadata is clean and does not reference Skill-Wanderer SSO.

### Actor

```text
Engineer / QA / Release Owner
```

### Verification Command

```powershell
$cfg = Invoke-RestMethod "https://sso.reltroner.com/realms/reltroner/.well-known/openid-configuration"

$cfg | Select-Object issuer, authorization_endpoint, token_endpoint, jwks_uri, end_session_endpoint | Format-List

($cfg | ConvertTo-Json -Depth 20) -match "sso.skill-wanderer.com"
```

### Required Output

```text
issuer                 : https://sso.reltroner.com/realms/reltroner
authorization_endpoint : https://sso.reltroner.com/realms/reltroner/protocol/openid-connect/auth
token_endpoint         : https://sso.reltroner.com/realms/reltroner/protocol/openid-connect/token
jwks_uri               : https://sso.reltroner.com/realms/reltroner/protocol/openid-connect/certs
end_session_endpoint   : https://sso.reltroner.com/realms/reltroner/protocol/openid-connect/logout

False
```

### Required Constraints

```text
MUST verify issuer
MUST verify authorization endpoint
MUST verify token endpoint
MUST verify logout endpoint
MUST confirm no sso.skill-wanderer.com reference exists
MUST NOT approve release if issuer points to old domain
```

---

## UC-07 — Engineer Tests Cross-Domain Isolation

### Goal

Ensure Reltroner changes do not break Skill-Wanderer or Chanhdao.

### Actor

```text
Engineer / QA
```

### Required Test Matrix

| System | Test URL | Expected SSO Domain |
|---|---|---|
| Reltroner | `https://lms.reltroner.com` | `https://sso.reltroner.com/realms/reltroner` |
| Skill-Wanderer | `https://dojo.skill-wanderer.com` | `https://sso.skill-wanderer.com/realms/skill-wanderer` |
| Chanhdao | `https://chanhdao.vn` | `https://sso.chanhdao.vn/realms/chanhdao` |

### Required Constraints

```text
MUST test all three systems after routing changes
MUST NOT assume isolation from route table alone
MUST verify actual browser redirect behavior
MUST report any cross-domain contamination immediately
```

### Acceptance Criteria

```text
Reltroner uses sso.reltroner.com
Skill-Wanderer remains on sso.skill-wanderer.com
Chanhdao remains on sso.chanhdao.vn
No unrelated domain is redirected to Reltroner SSO
```

---

## UC-08 — Engineer Handles Google Login Requirement

### Goal

Define strict boundary if Google login is required in the future.

### Current State

The Reltroner Identity login page does not show a Google login button.

Therefore, Google login is not part of the current completed scope.

### Future Constraint

If Google login is introduced later, it must use Reltroner-owned Google OAuth configuration.

### Required Google OAuth Redirect URI

```text
https://sso.reltroner.com/realms/reltroner/broker/google/endpoint
```

### Required Constraints

```text
MUST NOT reuse Skill-Wanderer Google OAuth client
MUST NOT show "Sign in to skill-wanderer.com" for Reltroner login
MUST create or use Reltroner-owned Google Cloud project
MUST configure authorized redirect URI for sso.reltroner.com
MUST validate Google consent screen branding
```

### Acceptance Criteria

```text
Google consent screen does not show skill-wanderer.com
Google login redirects back to sso.reltroner.com
OIDC callback returns to lms.reltroner.com
```

---

## 8. Forbidden Actions

The following actions are explicitly forbidden under this scope.

```text
FORBIDDEN: Create a new Keycloak instance
FORBIDDEN: Create a new Keycloak namespace
FORBIDDEN: Rename the reltroner realm
FORBIDDEN: Change lms-reltroner client ID without approval
FORBIDDEN: Use sso.skill-wanderer.com as Reltroner LMS authority
FORBIDDEN: Recreate reltroner-sso tunnel for production
FORBIDDEN: Bind sso.reltroner.com to inactive tunnel
FORBIDDEN: Install cloudflared on personal laptop for production service
FORBIDDEN: Delete HRM DNS records
FORBIDDEN: Delete mail DNS records
FORBIDDEN: Delete Resend records
FORBIDDEN: Modify Skill-Wanderer SSO routes
FORBIDDEN: Modify Chanhdao SSO routes
FORBIDDEN: Modify unrelated Cloudflare tunnel routes
FORBIDDEN: Transfer registrar ownership unless explicitly required
FORBIDDEN: Expose secrets in frontend environment variables
FORBIDDEN: Mark work complete before login and logout are tested
```

---

## 9. Required Approval Gates

Some actions are high risk and require explicit approval before execution.

| Action | Approval Required | Reason |
|---|---|---|
| Delete DNS record | Yes | Could break production routing |
| Delete tunnel binding | Yes | Could break SSO hostname |
| Modify shared tunnel route | Yes | Shared by multiple production systems |
| Change Keycloak realm setting | Yes | Could affect authentication metadata |
| Change LMS client redirect URIs | Yes | Could break login callback |
| Enable Google login | Yes | Requires branding and OAuth ownership validation |
| Modify Skill-Wanderer route | Yes | Outside Reltroner scope |
| Modify Chanhdao route | Yes | Outside Reltroner scope |
| Change email records | Yes | Could break email delivery |

---

## 10. Completion Definition

Work is not complete until all required checks pass.

### 10.1 Infrastructure Checks

```text
sso.reltroner.com resolves
sso.reltroner.com loads Keycloak login page
No Cloudflare Error 1033
No Cloudflare duplicate-host conflict
sso.reltroner.com is under blog-skill-wanderer tunnel
```

### 10.2 OIDC Checks

```text
issuer is https://sso.reltroner.com/realms/reltroner
authorization_endpoint uses sso.reltroner.com
token_endpoint uses sso.reltroner.com
jwks_uri uses sso.reltroner.com
end_session_endpoint uses sso.reltroner.com
well-known metadata contains no sso.skill-wanderer.com
```

### 10.3 LMS Checks

```text
Login button redirects to sso.reltroner.com
User can log in successfully
Callback returns to lms.reltroner.com
Navbar shows authenticated user email
Logout succeeds
Navbar returns to guest state
```

### 10.4 Isolation Checks

```text
Skill-Wanderer still uses sso.skill-wanderer.com
Chanhdao still uses sso.chanhdao.vn
Reltroner uses sso.reltroner.com
```

---

## 11. Release Checklist

Use this checklist before approving any future SSO-related release.

```text
[ ] Confirm task is within Reltroner SSO scope
[ ] Confirm no unrelated DNS records are modified
[ ] Confirm no unrelated tunnel routes are modified
[ ] Confirm no new Keycloak instance is created
[ ] Confirm no new namespace is created
[ ] Confirm sso.reltroner.com route exists under blog-skill-wanderer
[ ] Confirm service target is http://keycloak.keycloak:8080
[ ] Confirm Cloudflare Pages NEXT_PUBLIC_OIDC_AUTHORITY points to sso.reltroner.com
[ ] Confirm LMS build succeeds
[ ] Confirm LMS production deploy succeeds
[ ] Confirm login redirect uses sso.reltroner.com
[ ] Confirm user login succeeds
[ ] Confirm callback succeeds
[ ] Confirm logout succeeds
[ ] Confirm well-known issuer uses sso.reltroner.com
[ ] Confirm well-known metadata has no sso.skill-wanderer.com
[ ] Confirm Skill-Wanderer still uses sso.skill-wanderer.com
[ ] Confirm Chanhdao still uses sso.chanhdao.vn
[ ] Capture screenshots or logs for evidence
```

---

## 12. Rollback Constraints

Rollback must be controlled and approved.

### 12.1 Allowed Rollback Actions

```text
Revert LMS OIDC environment variable
Revert LMS code commit
Remove newly created sso.reltroner.com route
Restore previous DNS tunnel binding only if explicitly approved
```

### 12.2 Forbidden Rollback Actions

```text
Do not delete unrelated DNS records
Do not modify Skill-Wanderer routes
Do not modify Chanhdao routes
Do not change Keycloak realm globally without approval
Do not create emergency local laptop tunnel as production fallback
```

### 12.3 Preferred Rollback Order

```text
1. Confirm failure type
2. Check Cloudflare tunnel route
3. Check OIDC metadata
4. Check Cloudflare Pages environment variables
5. Revert frontend environment if needed
6. Redeploy LMS
7. Re-test login
8. Notify owner with evidence
```

---

## 13. Engineer Handoff Instructions

Before starting work, engineer must read and acknowledge:

```text
1. Reltroner uses sso.reltroner.com, not sso.skill-wanderer.com.
2. sso.reltroner.com must be served through blog-skill-wanderer tunnel.
3. Keycloak service is reused at http://keycloak.keycloak:8080.
4. Do not create new Keycloak.
5. Do not create new namespace.
6. Do not install local cloudflared for production.
7. Do not touch Skill-Wanderer or Chanhdao routes.
8. Do not change DNS records outside the approved list.
9. Do not mark complete without login/logout verification.
```

---

## 14. Scope Breach Examples

### Example 1 — Wrong Tunnel

```text
Engineer creates reltroner-sso tunnel and binds sso.reltroner.com to it.
```

Result:

```text
Scope breach.
This can cause Cloudflare Error 1033 if no connector is attached.
```

Correct action:

```text
Use blog-skill-wanderer tunnel.
```

### Example 2 — Wrong OIDC Authority

```text
Engineer sets NEXT_PUBLIC_OIDC_AUTHORITY to https://sso.skill-wanderer.com/realms/reltroner
```

Result:

```text
Scope breach.
Reltroner becomes dependent on Skill-Wanderer SSO domain again.
```

Correct action:

```text
Use https://sso.reltroner.com/realms/reltroner
```

### Example 3 — New Keycloak Deployment

```text
Engineer creates a new Keycloak namespace and deploys a new Keycloak instance.
```

Result:

```text
Scope breach.
Owner explicitly required using the same existing Keycloak service.
```

Correct action:

```text
Reuse http://keycloak.keycloak:8080
```

### Example 4 — Local Connector

```text
Engineer runs cloudflared service install on personal laptop.
```

Result:

```text
Scope breach.
Production SSO becomes dependent on a personal machine.
```

Correct action:

```text
Use existing production tunnel connector.
```

---

## 15. Requirement Summary

The engineering scope is locked to the following final state:

```text
Reltroner LMS:
https://lms.reltroner.com

Reltroner SSO:
https://sso.reltroner.com/realms/reltroner

Cloudflare Tunnel:
blog-skill-wanderer

Internal Keycloak Service:
http://keycloak.keycloak:8080

Keycloak Realm:
reltroner

OIDC Client:
lms-reltroner
```

Anything outside this final state requires a new approved requirement.

---

## 16. Final Constraint Statement

Engineers are allowed to maintain, verify, and repair the Reltroner SSO integration only within the locked architecture described in this document.

They must not expand the scope into new identity infrastructure, new tunnels, new namespaces, unrelated DNS changes, unrelated realm changes, or shared production route modifications without explicit approval.

The success condition is not merely that the page loads.

The success condition is:

```text
Reltroner LMS uses sso.reltroner.com end-to-end,
login works,
logout works,
OIDC issuer is clean,
and other production systems remain isolated.
```
