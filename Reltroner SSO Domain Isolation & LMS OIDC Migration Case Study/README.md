# Reltroner SSO Domain Isolation & LMS OIDC Migration Case Study

**Portfolio Case Study**  
**Project:** Reltroner Learning Academy / Reltroner Identity  
**Primary Domains:** `reltroner.com`, `lms.reltroner.com`, `sso.reltroner.com`  
**Stack:** Cloudflare DNS, Cloudflare Tunnel, Cloudflare Pages, Keycloak, OIDC Authorization Code + PKCE, Next.js, Rancher/Kubernetes  
**Repository:** `Reltroner/LMS-FE`  
**Final Commit:** `f2d4041` — `fix: point Reltroner LMS OIDC to Reltroner SSO`  
**Status:** Completed end-to-end for emergency SSO migration scope

---

## 1. Executive Summary

This case study documents an emergency identity and domain-isolation migration for **Reltroner Learning Academy**, a frontend-only LMS hosted at:

```text
https://lms.reltroner.com
```

The system previously used the owner’s shared Skill-Wanderer SSO domain:

```text
https://sso.skill-wanderer.com/realms/reltroner
```

This created an urgent business and technical problem: Reltroner authentication traffic, identity branding, OIDC issuer metadata, and login redirects were mixed with Skill-Wanderer infrastructure and domain identity. The owner explicitly requested that Reltroner stop using the Skill-Wanderer SSO domain and move to a Reltroner-owned domain:

```text
https://sso.reltroner.com/realms/reltroner
```

The migration was completed without deploying a new Keycloak instance, without creating a new namespace, and without disrupting existing production SSO routes for Skill-Wanderer and Chanhdao.

The final validated flow is:

```text
User opens LMS
→ https://lms.reltroner.com
→ clicks Login
→ redirects to https://sso.reltroner.com/realms/reltroner/...
→ authenticates through Keycloak
→ returns to https://lms.reltroner.com/auth/callback
→ LMS shows authenticated user
→ logout returns LMS to guest state
```

The final OIDC discovery document confirms that Reltroner no longer depends on the Skill-Wanderer SSO issuer:

```text
issuer: https://sso.reltroner.com/realms/reltroner
skill-wanderer match: False
```

---

## 2. Business Context

### 2.1 Initial Stakeholder Requirement

The owner raised an urgent concern:

```text
"don't use my SSO for your reltroner"
"you should point to your own domain"
"otherwise I need to take your realm down"
"don't use my domain name please otherwise the SEO and GEO gonna be mixing up"
```

The business implication was clear: Reltroner needed its own public SSO domain and identity boundary.

### 2.2 Why This Was Critical

The previous architecture created several risks:

1. **Brand contamination**  
   Reltroner authentication was visibly using `skill-wanderer.com`.

2. **SEO/GEO confusion**  
   Identity-related traffic and domain association could mix Reltroner and Skill-Wanderer signals.

3. **Operational dependency**  
   If the owner removed or disabled the Reltroner realm from the Skill-Wanderer SSO domain, the LMS login flow would break.

4. **Identity ownership gap**  
   Reltroner had its own LMS domain but did not yet have its own SSO authority domain.

5. **Emergency deadline pressure**  
   The owner warned that the realm could be taken down if the dependency was not removed.

---

## 3. System Background

### 3.1 LMS Frontend

Reltroner Learning Academy is a frontend-only LMS.

Production URL:

```text
https://lms.reltroner.com
```

Hosting platform:

```text
Cloudflare Pages
```

Repository:

```text
Reltroner/LMS-FE
```

Main branch:

```text
main
```

Build command:

```text
npm run build
```

Build output:

```text
out
```

### 3.2 Identity Provider

The authentication layer uses Keycloak with OIDC.

Original authority:

```text
https://sso.skill-wanderer.com/realms/reltroner
```

Target authority:

```text
https://sso.reltroner.com/realms/reltroner
```

Client:

```text
lms-reltroner
```

OIDC flow:

```text
Authorization Code + PKCE
```

Relevant frontend variables:

```env
NEXT_PUBLIC_OIDC_AUTHORITY
NEXT_PUBLIC_OIDC_CLIENT_ID
NEXT_PUBLIC_OIDC_REDIRECT_URI
NEXT_PUBLIC_OIDC_POST_LOGOUT_REDIRECT_URI
NEXT_PUBLIC_OIDC_SCOPE
```

---

## 4. Initial Architecture Before Migration

Before the migration, the LMS login flow was:

```text
https://lms.reltroner.com
        |
        v
https://sso.skill-wanderer.com/realms/reltroner
        |
        v
Keycloak realm: reltroner
```

This meant Reltroner had its own LMS domain but not its own SSO domain.

### 4.1 Original OIDC Authority

```env
NEXT_PUBLIC_OIDC_AUTHORITY=https://sso.skill-wanderer.com/realms/reltroner
```

### 4.2 Original Fallback in Code

File:

```text
src/lib/auth/account-url.ts
```

Before migration:

```ts
export function getAccountUrl(): string {
  let authority =
    process.env.NEXT_PUBLIC_OIDC_AUTHORITY || "https://sso.skill-wanderer.com/realms/reltroner";
  if (authority.endsWith("/")) {
    authority = authority.slice(0, -1);
  }
  return `${authority}/account`;
}
```

This meant that even if environment variables were missing, the fallback still pointed to the Skill-Wanderer SSO domain.

---

## 5. Target Architecture

The desired final architecture was:

```text
https://lms.reltroner.com
        |
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
Keycloak realm: reltroner
```

The existing Keycloak server remained the same. The migration only changed the public hostname and OIDC authority.

### 5.1 Final Routing Pattern

The owner clarified that Reltroner should follow the same pattern as Chanhdao:

```text
sso.skill-wanderer.com → http://keycloak.keycloak:8080
sso.chanhdao.vn       → http://keycloak.keycloak:8080
sso.reltroner.com     → http://keycloak.keycloak:8080
```

The correct tunnel was:

```text
blog-skill-wanderer
```

not a separate new tunnel.

---

## 6. Migration Strategy

The migration was split into deterministic phases:

1. Move `reltroner.com` DNS authority from Hostinger to Cloudflare.
2. Preserve all critical DNS records.
3. Create `sso.reltroner.com` routing to Keycloak.
4. Avoid disrupting existing Skill-Wanderer and Chanhdao routes.
5. Update LMS code fallback and environment examples.
6. Update Cloudflare Pages production variables.
7. Redeploy LMS.
8. Validate OIDC issuer, login, callback, and logout end-to-end.

---

## 7. Phase 1 — DNS Authority Migration to Cloudflare

### 7.1 Problem

The domain `reltroner.com` was managed at Hostinger using Hostinger nameservers:

```text
ns1.dns-parking.com
ns2.dns-parking.com
```

To create and control `sso.reltroner.com` through Cloudflare Tunnel, Cloudflare needed to become the authoritative DNS provider for `reltroner.com`.

### 7.2 Decision

Use **Connect a domain**, not **Transfer a domain**.

Reason:

- **Connect a domain** changes DNS authority quickly.
- **Transfer a domain** moves registrar ownership and can take longer.
- The emergency requirement was DNS control, not registrar transfer.

### 7.3 Cloudflare Nameservers

Cloudflare assigned:

```text
eloise.ns.cloudflare.com
louis.ns.cloudflare.com
```

Hostinger nameservers were changed from:

```text
ns1.dns-parking.com
ns2.dns-parking.com
```

to:

```text
eloise.ns.cloudflare.com
louis.ns.cloudflare.com
```

### 7.4 Verification

PowerShell verification:

```powershell
Resolve-DnsName reltroner.com -Type NS
```

Result:

```text
reltroner.com NS eloise.ns.cloudflare.com
reltroner.com NS louis.ns.cloudflare.com
```

Conclusion:

```text
Hostinger DNS Authority: Removed
Cloudflare DNS Authority: Active
```

---

## 8. Phase 2 — DNS Record Preservation

Before switching DNS authority, all critical DNS records had to be preserved.

### 8.1 Records Initially Found by Cloudflare

Cloudflare scan detected:

```text
1 A
6 CNAME
2 MX
3 TXT
```

However, some important records were missing.

### 8.2 Missing LMS Record

PowerShell check:

```powershell
Resolve-DnsName lms.reltroner.com
```

Result:

```text
lms.reltroner.com CNAME lms-fe-3gl.pages.dev
```

This record was not initially imported by Cloudflare and had to be added manually.

Final LMS record:

```text
CNAME lms → lms-fe-3gl.pages.dev
Proxy: DNS Only
TTL: Auto
```

### 8.3 Missing HRM Records

Hostinger DNS contained:

```text
A    hrm → 145.79.28.61
AAAA hrm → 2a02:4780:5c:2146:0:e07:6065:6
```

These were manually added to Cloudflare.

Final HRM records:

```text
A    hrm → 145.79.28.61
AAAA hrm → 2a02:4780:5c:2146:0:e07:6065:6
Proxy: DNS Only
TTL: Auto
```

### 8.4 Resend Email Records

Hostinger also contained Resend-related records:

```text
TXT resend._domainkey.resend
TXT send.resend
MX  send.resend
```

These were also migrated to Cloudflare.

### 8.5 Final DNS Record Set

Final important records preserved:

```text
reltroner.com                      A      76.76.21.21
www.reltroner.com                  CNAME  cname.vercel-dns.com
lms.reltroner.com                  CNAME  lms-fe-3gl.pages.dev
hrm.reltroner.com                  A      145.79.28.61
hrm.reltroner.com                  AAAA   2a02:4780:5c:2146:0:e07:6065:6
reltroner.com                      MX     mx1.hostinger.com
reltroner.com                      MX     mx2.hostinger.com
reltroner.com                      TXT    SPF
_dmarc.reltroner.com               TXT    DMARC
hostingermail-a._domainkey         CNAME  Hostinger DKIM
hostingermail-b._domainkey         CNAME  Hostinger DKIM
hostingermail-c._domainkey         CNAME  Hostinger DKIM
send.resend.reltroner.com          MX     Amazon SES feedback SMTP
send.resend.reltroner.com          TXT    Resend SPF
resend._domainkey.resend           TXT    Resend DKIM
```

---

## 9. Phase 3 — Cloudflare Tunnel Investigation

### 9.1 Initial Problem

The owner wanted Reltroner to use its own domain, but Keycloak was still reachable through:

```text
https://sso.skill-wanderer.com
```

The first assumption was that a new dedicated tunnel might be needed for Reltroner.

A tunnel named:

```text
reltroner-sso
```

was created.

### 9.2 Discovery

The Cloudflare tunnel list showed:

```text
blog-skill-wanderer       Healthy
client-portal-api         Down
prabhat-namespace-tunnel  Healthy
reltroner-sso             Inactive
```

The new `reltroner-sso` tunnel had no connector:

```text
Status: Inactive
Connectors: 0
```

This caused:

```text
Error 1033 — Cloudflare Tunnel error
```

when visiting:

```text
https://sso.reltroner.com
```

### 9.3 Existing Tunnel Audit

The existing `blog-skill-wanderer` tunnel had active routes:

```text
sso.skill-wanderer.com → http://keycloak.keycloak:8080
sso.chanhdao.vn       → http://keycloak.keycloak:8080
```

The owner clarified:

```text
sso.reltroner.com should be added to the existing blog-skill-wanderer tunnel.
```

### 9.4 Wrong Path Identified

The wrong temporary architecture was:

```text
sso.reltroner.com
        |
        v
reltroner-sso tunnel
        |
        v
No connector
        |
        v
Error 1033
```

The correct architecture was:

```text
sso.reltroner.com
        |
        v
blog-skill-wanderer tunnel
        |
        v
http://keycloak.keycloak:8080
```

---

## 10. Phase 4 — Cleanup of Incorrect Tunnel Binding

### 10.1 Problem

DNS contained:

```text
sso.reltroner.com
Type: Tunnel
Target: reltroner-sso
```

This conflicted with adding `sso.reltroner.com` to the `blog-skill-wanderer` tunnel.

Cloudflare error:

```text
An A, AAAA, or CNAME record with that host already exists.
```

### 10.2 Owner Approval

The owner instructed:

```text
delete current tunnel and dns for sso.reltroner.com
make the clean create from scratch
```

### 10.3 Action Taken

The incorrect DNS tunnel binding was deleted:

```text
sso.reltroner.com → reltroner-sso
```

The DNS records count decreased from 19 to 18, confirming removal.

### 10.4 Clean Route Creation

A new route was created under the active tunnel:

```text
Tunnel: blog-skill-wanderer

Hostname:
sso.reltroner.com

Path:
*

Service:
http://keycloak.keycloak:8080
```

Final active tunnel routes included:

```text
sso.skill-wanderer.com → http://keycloak.keycloak:8080
sso.chanhdao.vn       → http://keycloak.keycloak:8080
sso.reltroner.com     → http://keycloak.keycloak:8080
```

---

## 11. Phase 5 — Keycloak Exposure Validation

### 11.1 Direct SSO Domain Test

Browser test:

```text
https://sso.reltroner.com
```

Result:

```text
Keycloak login page loaded successfully
```

This proved:

```text
DNS: OK
Cloudflare Tunnel: OK
Kubernetes internal routing: OK
Keycloak service reachability: OK
```

### 11.2 Reltroner Realm Login Page

The LMS login redirect loaded:

```text
https://sso.reltroner.com/realms/reltroner/protocol/openid-connect/auth?client_id=lms-reltroner...
```

The login page displayed:

```text
RELTRONER IDENTITY
```

This confirmed that the correct realm and branding were being served from the Reltroner SSO domain.

---

## 12. Phase 6 — LMS Code Update

### 12.1 Local Code Search

Command:

```powershell
rg -n "sso\.skill-wanderer\.com|skill-wanderer\.com/realms/reltroner|NEXT_PUBLIC_OIDC_AUTHORITY" .
```

Relevant results:

```text
src/lib/auth/oidc-config.ts
src/lib/auth/account-url.ts
.env.example
```

### 12.2 Updated `.env.example`

Before:

```env
NEXT_PUBLIC_OIDC_AUTHORITY=https://sso.skill-wanderer.com/realms/reltroner
```

After:

```env
NEXT_PUBLIC_OIDC_AUTHORITY=https://sso.reltroner.com/realms/reltroner
```

### 12.3 Updated Fallback in `account-url.ts`

File:

```text
src/lib/auth/account-url.ts
```

Final code:

```ts
export function getAccountUrl(): string {
  let authority =
    process.env.NEXT_PUBLIC_OIDC_AUTHORITY || "https://sso.reltroner.com/realms/reltroner";
  if (authority.endsWith("/")) {
    authority = authority.slice(0, -1);
  }
  return `${authority}/account`;
}
```

### 12.4 Build Validation

Command:

```powershell
npm run build
```

Result:

```text
Compiled successfully
Finished TypeScript
Content validation passed
Resource validation passed
Orphan validation passed
Generated static pages successfully
```

A Contentlayer warning appeared on Windows:

```text
Warning: Contentlayer might not work as expected on Windows
TypeError: The "code" argument must be of type number. Received an instance of Object
```

However, the full Next.js build completed successfully.

### 12.5 Git Commit

Commit command:

```powershell
git add .env.example src/lib/auth/account-url.ts
git commit -m "fix: point Reltroner LMS OIDC to Reltroner SSO"
git push origin main
```

Commit:

```text
f2d4041 fix: point Reltroner LMS OIDC to Reltroner SSO
```

Push target:

```text
https://github.com/Reltroner/LMS-FE.git
main -> main
```

---

## 13. Phase 7 — Cloudflare Pages Environment Update

### 13.1 Problem

Cloudflare Pages production still contained:

```env
NEXT_PUBLIC_OIDC_AUTHORITY=https://sso.skill-wanderer.com/realms/reltroner
```

This meant the deployed LMS would continue redirecting to the old Skill-Wanderer SSO domain even after the code fallback was updated.

### 13.2 Production Variable Update

Cloudflare Pages project:

```text
lms-fe
```

Environment:

```text
Production
```

Variable changed from:

```env
NEXT_PUBLIC_OIDC_AUTHORITY=https://sso.skill-wanderer.com/realms/reltroner
```

to:

```env
NEXT_PUBLIC_OIDC_AUTHORITY=https://sso.reltroner.com/realms/reltroner
```

Other production variables remained:

```env
NEXT_PUBLIC_OIDC_CLIENT_ID=lms-reltroner
NEXT_PUBLIC_OIDC_REDIRECT_URI=https://lms.reltroner.com/auth/callback
NEXT_PUBLIC_OIDC_POST_LOGOUT_REDIRECT_URI=https://lms.reltroner.com
NEXT_PUBLIC_OIDC_SCOPE=openid profile email
```

### 13.3 Deployment

After the Git push and variable update, Cloudflare Pages redeployed the LMS.

---

## 14. Final End-to-End Validation

### 14.1 OIDC Discovery Validation

PowerShell command:

```powershell
$cfg = Invoke-RestMethod "https://sso.reltroner.com/realms/reltroner/.well-known/openid-configuration"

$cfg | Select-Object issuer, authorization_endpoint, token_endpoint, jwks_uri, end_session_endpoint | Format-List

($cfg | ConvertTo-Json -Depth 20) -match "sso.skill-wanderer.com"
```

Result:

```text
issuer                 : https://sso.reltroner.com/realms/reltroner
authorization_endpoint : https://sso.reltroner.com/realms/reltroner/protocol/openid-connect/auth
token_endpoint         : https://sso.reltroner.com/realms/reltroner/protocol/openid-connect/token
jwks_uri               : https://sso.reltroner.com/realms/reltroner/protocol/openid-connect/certs
end_session_endpoint   : https://sso.reltroner.com/realms/reltroner/protocol/openid-connect/logout

False
```

Interpretation:

```text
The Reltroner OIDC issuer is now fully on sso.reltroner.com.
No sso.skill-wanderer.com references remain in the Reltroner well-known OIDC configuration.
```

### 14.2 LMS Login Redirect Validation

From:

```text
https://lms.reltroner.com
```

Clicking Login redirected to:

```text
https://sso.reltroner.com/realms/reltroner/protocol/openid-connect/auth?client_id=lms-reltroner...
```

This confirmed that the LMS production frontend was using the new authority.

### 14.3 Login Success Validation

Test user:

```text
test.student@reltroner.com
```

After username/password login, the user was redirected back to:

```text
https://lms.reltroner.com
```

Navbar changed from guest state:

```text
Login
```

to authenticated state:

```text
test.student@reltroner.com
Account
Logout
```

### 14.4 Logout Validation

After clicking Logout:

```text
https://lms.reltroner.com
```

returned to guest state:

```text
Login
```

This confirmed that logout and session cleanup were working.

### 14.5 Google Login Check

The Reltroner Identity login page did not show a Google login button.

Therefore, the original Google consent-screen concern was not active in the final Reltroner login flow.

If Google login is enabled later, Reltroner must use a separate Google OAuth project and client with Reltroner-owned branding and authorized redirect URI:

```text
https://sso.reltroner.com/realms/reltroner/broker/google/endpoint
```

---

## 15. Cross-System Isolation Validation

The migration had to avoid breaking other domains.

### 15.1 Skill-Wanderer LMS Test

Test URL:

```text
https://dojo.skill-wanderer.com
```

Login redirect:

```text
https://sso.skill-wanderer.com/realms/skill-wanderer/...
```

Result:

```text
Skill-Wanderer still uses sso.skill-wanderer.com.
```

### 15.2 Chanhdao Test

Test URL:

```text
https://chanhdao.vn
```

Login redirect:

```text
https://sso.chanhdao.vn/realms/chanhdao/...
```

Result:

```text
Chanhdao still uses sso.chanhdao.vn.
```

### 15.3 Reltroner Test

Test URL:

```text
https://lms.reltroner.com
```

Login redirect:

```text
https://sso.reltroner.com/realms/reltroner/...
```

Result:

```text
Reltroner now uses sso.reltroner.com.
```

Conclusion:

```text
Domain isolation was achieved without disrupting Skill-Wanderer or Chanhdao.
```

---

## 16. Final Architecture After Migration

```text
Reltroner LMS
https://lms.reltroner.com
        |
        | OIDC Authorization Code + PKCE
        v
Reltroner SSO Authority
https://sso.reltroner.com/realms/reltroner
        |
        v
Cloudflare Tunnel
blog-skill-wanderer
        |
        v
Kubernetes Service
http://keycloak.keycloak:8080
        |
        v
Keycloak Realm
reltroner
```

Parallel production routes remain isolated:

```text
Skill-Wanderer:
dojo.skill-wanderer.com
→ sso.skill-wanderer.com
→ realm skill-wanderer

Chanhdao:
chanhdao.vn
→ sso.chanhdao.vn
→ realm chanhdao

Reltroner:
lms.reltroner.com
→ sso.reltroner.com
→ realm reltroner
```

---

## 17. Final Status

### 17.1 Completed Items

```text
✅ reltroner.com DNS authority moved to Cloudflare
✅ Critical DNS records preserved
✅ LMS DNS preserved
✅ HRM DNS preserved
✅ Hostinger email records preserved
✅ Resend records preserved
✅ Incorrect reltroner-sso tunnel binding removed
✅ sso.reltroner.com added to active blog-skill-wanderer tunnel
✅ sso.reltroner.com routes to http://keycloak.keycloak:8080
✅ Keycloak login page loads from sso.reltroner.com
✅ OIDC issuer is now sso.reltroner.com
✅ OIDC well-known config has no sso.skill-wanderer.com references
✅ LMS production environment updated
✅ LMS login redirects to sso.reltroner.com
✅ User login succeeds
✅ Callback succeeds
✅ Authenticated navbar state works
✅ Logout succeeds
✅ Skill-Wanderer remains isolated
✅ Chanhdao remains isolated
```

### 17.2 Final Completion Assessment

For the emergency SSO migration scope:

```text
Completion: 100%
Status: Successfully completed end-to-end
```

---

## 18. Problems Encountered and How They Were Solved

### Problem 1 — Cloudflare DNS Scan Missed Critical Records

Cloudflare did not initially import the `lms` subdomain.

Solution:

```text
Manually verified existing DNS using Resolve-DnsName.
Added lms.reltroner.com CNAME to Cloudflare before changing nameservers.
```

### Problem 2 — HRM Could Have Gone Down

`hrm.reltroner.com` was hosted on Hostinger and had A/AAAA records that were not initially visible in Cloudflare.

Solution:

```text
Audited Hostinger DNS.
Copied HRM A and AAAA records into Cloudflare before nameserver migration.
```

### Problem 3 — Resend Records Were Missing

Email-related Resend DNS records were not initially imported.

Solution:

```text
Manually copied Resend MX, SPF, and DKIM records into Cloudflare.
```

### Problem 4 — Wrong Tunnel Was Created

A new `reltroner-sso` tunnel was created, but it had no connector and produced Error 1033.

Solution:

```text
Confirmed with owner that sso.reltroner.com should use the existing blog-skill-wanderer connector.
Deleted the incorrect DNS tunnel binding.
Created clean route under blog-skill-wanderer.
```

### Problem 5 — Cloudflare Hostname Conflict

Attempting to add `sso.reltroner.com` to the correct tunnel failed because the hostname was already bound to the wrong tunnel.

Solution:

```text
Deleted the old sso.reltroner.com Tunnel DNS record.
Recreated sso.reltroner.com cleanly under blog-skill-wanderer.
```

### Problem 6 — LMS Still Redirected to Old SSO

After infrastructure migration, the LMS still redirected to `sso.skill-wanderer.com`.

Solution:

```text
Updated local fallback configuration.
Updated .env.example.
Updated Cloudflare Pages production environment variable.
Redeployed LMS.
```

---

## 19. Engineering Lessons Learned

### 19.1 DNS Migration Requires Full Record Inventory

Cloudflare auto-scan is useful but not sufficient. Critical records can be missed, especially custom subdomains and email-related records.

### 19.2 Tunnel Routing and DNS Records Are Tightly Coupled

Cloudflare Tunnel creates DNS bindings. If a hostname is already bound to one tunnel, it cannot be added to another tunnel until the old binding is removed.

### 19.3 Do Not Deploy Connectors Without Understanding Ownership

Running a Cloudflare connector from a local laptop would have made production SSO depend on a personal machine. The correct production pattern was to reuse the existing healthy cluster connector.

### 19.4 Public SSO Domain and OIDC Issuer Must Match

For clean identity isolation, the OIDC issuer must be under the correct domain:

```text
https://sso.reltroner.com/realms/reltroner
```

It is not enough for the login page to load. The well-known OIDC metadata must also be clean.

### 19.5 Environment Variables Override Code Fallbacks

Updating code fallback is not sufficient if production environment variables still point to the old authority.

### 19.6 End-to-End Verification Matters

The work was not considered complete until all of the following passed:

```text
DNS
Tunnel
Keycloak login page
OIDC well-known issuer
LMS redirect
Login callback
Authenticated state
Logout
Cross-domain isolation
```

---

## 20. Portfolio Positioning

This project demonstrates practical full-cycle engineering capability across infrastructure, identity, frontend configuration, debugging, stakeholder communication, and production risk control.

### 20.1 Skills Demonstrated

```text
Cloudflare DNS migration
Cloudflare Tunnel routing
Keycloak OIDC configuration
OIDC issuer validation
Frontend environment configuration
Next.js production build validation
Cloudflare Pages deployment
Rancher/Kubernetes investigation
Production debugging
Stakeholder communication
Risk-controlled infrastructure changes
Emergency incident handling
```

### 20.2 Why This Case Study Is Valuable

This was not a simple coding task. It required identifying the real system boundary across several layers:

```text
DNS
Tunnel
Kubernetes internal service
Keycloak realm
OIDC metadata
Cloudflare Pages environment variables
Frontend authentication flow
```

The solution required careful coordination because multiple production domains shared the same Keycloak backend:

```text
sso.skill-wanderer.com
sso.chanhdao.vn
sso.reltroner.com
```

A careless change could have broken other production systems. The final solution isolated Reltroner while preserving existing systems.

---

## 21. Final Outcome

The migration achieved the emergency requirement:

```text
Reltroner no longer uses sso.skill-wanderer.com as its LMS authentication authority.
```

Final Reltroner authority:

```text
https://sso.reltroner.com/realms/reltroner
```

Final LMS login flow:

```text
https://lms.reltroner.com
→ https://sso.reltroner.com/realms/reltroner/protocol/openid-connect/auth
→ login success
→ https://lms.reltroner.com/auth/callback
→ authenticated LMS session
→ logout success
```

Final status:

```text
Emergency SSO migration completed successfully end-to-end.
```

---

## 22. Suggested Resume Bullet Points

- Led an emergency SSO domain-isolation migration for a production LMS, moving OIDC authority from a shared Skill-Wanderer SSO domain to a dedicated Reltroner SSO domain.
- Migrated `reltroner.com` DNS authority from Hostinger to Cloudflare while preserving LMS, HRM, email, DKIM, SPF, DMARC, and Resend records.
- Configured Cloudflare Tunnel routing for `sso.reltroner.com` to an existing Kubernetes-hosted Keycloak service without disrupting Skill-Wanderer or Chanhdao production SSO routes.
- Updated Next.js LMS OIDC configuration and Cloudflare Pages production environment variables to use `https://sso.reltroner.com/realms/reltroner`.
- Validated OIDC issuer metadata, login redirect, authorization callback, authenticated session state, logout behavior, and cross-domain isolation end-to-end.
- Resolved Cloudflare Tunnel Error 1033 by identifying and removing an incorrect inactive tunnel binding, then recreating the SSO route under the correct healthy tunnel connector.

---

## 23. Suggested Portfolio Title

```text
Emergency SSO Domain Isolation for a Production LMS: Cloudflare Tunnel, Keycloak, and OIDC Migration
```

Alternative titles:

```text
Reltroner Identity Migration: Isolating LMS Authentication from a Shared SSO Domain

Production OIDC Migration Case Study: Moving Reltroner LMS from Skill-Wanderer SSO to a Dedicated SSO Domain

Cloudflare + Keycloak SSO Migration: End-to-End Identity Boundary Isolation for Reltroner LMS
```
