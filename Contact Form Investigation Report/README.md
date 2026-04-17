# Contact Form Investigation Report

**Author:** Staff Engineer Investigation  
**Date:** April 17, 2026  
**Status:** READ-ONLY — No code was modified during this investigation  
**Scope:** `pages/contact.vue`, `plugins/firebase.client.ts`, `nuxt.config.ts`, `server/middleware/`

---

## Executive Summary

The contact form system at `/contact` is a **client-side-only implementation** built with Vue 3 + Nuxt 3, writing directly to Google Cloud Firestore via the Firebase JS SDK. There is no backend API layer, no server-side validation, no spam protection, no rate limiting, and no email notification mechanism. Form submissions land in Firestore but nothing alerts the business owner — making **silent lead loss the primary operational risk**. Additionally, Firebase credentials are hardcoded as fallback values in `nuxt.config.ts`, a critical security exposure. The system works for minimal traffic but carries meaningful risk at any scale.

---

## Current System (AS-IS)

### Architecture Overview

```
User Browser
  │
  ├── pages/contact.vue          (Vue 3 SFC — client-side only)
  │     ├── "Hire the Guild" form   →  Firestore: contact-messages
  │     └── "Join the Guild" form   →  Firestore: guild-applications
  │
  └── plugins/firebase.client.ts (Nuxt plugin — browser-only)
        └── initializeApp() + getFirestore()
              └── Firebase Project: skill-wanderer-hub (GCP)
```

### Component Inventory

| File | Role |
|---|---|
| `pages/contact.vue` | Both forms, all UI state, both submission handlers |
| `plugins/firebase.client.ts` | Firebase initialization, injects `$firestore` |
| `nuxt.config.ts` | Firebase runtime config (with hardcoded fallbacks) |
| `server/middleware/apikey.ts` | Unrelated — serves IndexNow key, not contact-related |

### Data Flow (Step-by-Step)

**"Hire the Guild" form:**
1. User fills in: `name`, `email`, `topic`, `message`, `valuesQuality`
2. User clicks "Request an Architectural Audit"
3. `handleSubmit()` fires (`@submit.prevent`)
4. `useNuxtApp().$firestore` retrieved from plugin injection
5. `firebase/firestore` dynamically imported (`addDoc`, `collection`, `serverTimestamp`)
6. Document written to Firestore collection `contact-messages` with fields: `name`, `email`, `topic`, `message`, `valuesQuality`, `timestamp` (server), `status: 'new'`
7. On success: success message shown for 5 seconds, form reset
8. On failure: generic error message shown, no retry logic, no error logging

**"Join the Guild" form:**
1. User fills in: `name`, `email`, `skill`, `experience`, `portfolio` (optional), `message`
2. `handleGuildSubmit()` fires
3. Document written to Firestore collection `guild-applications` with same timestamp/status pattern
4. On success: success message for 6 seconds, form reset
5. On failure: same generic error, no logging

### Validation — What Exists vs. What's Missing

| Check | Hire Form | Guild Form |
|---|---|---|
| HTML5 `required` attribute | ✅ (name, email, topic, message) | ✅ (name, email, skill, experience, message) |
| `type="email"` browser check | ✅ | ✅ |
| `type="url"` browser check | n/a | ✅ (portfolio) |
| Server-side validation | ❌ | ❌ |
| Field length limits | ❌ | ❌ |
| XSS sanitization before storage | ❌ | ❌ |
| Duplicate submission detection | ❌ | ❌ |
| CAPTCHA / bot protection | ❌ | ❌ |
| Rate limiting | ❌ | ❌ |
| Email delivery confirmation | ❌ | ❌ |

### Dependencies

| Dependency | Version | Purpose |
|---|---|---|
| `firebase` | ^11.9.1 | Firestore write (client-side only) |
| `nuxt` | ^3.16.2 | Full-stack framework (but no server routes used) |
| `@nuxtjs/sitemap` | ^7.4.0 | Sitemap (unrelated) |

---

## Problem Identification

### Is This a Real Problem?

**Yes.** This is not a perceived issue. The combination of missing email notifications and hardcoded credentials represents active operational and security risk in production today.

### Specific Problems

#### P1 — CRITICAL: Firebase credentials hardcoded as fallback values in source control

In `nuxt.config.ts` lines 93–100:

```ts
firebase: {
  apiKey: process.env.NUXT_PUBLIC_FIREBASE_API_KEY || 'AIzaSyCr_1Fo6hzJDsKZrjw2u3HlrFhBnfeHmxE',
  authDomain: process.env.NUXT_PUBLIC_FIREBASE_AUTH_DOMAIN || 'skill-wanderer-hub.firebaseapp.com',
  projectId: process.env.NUXT_PUBLIC_FIREBASE_PROJECT_ID || 'skill-wanderer-hub',
  storageBucket: process.env.NUXT_PUBLIC_FIREBASE_STORAGE_BUCKET || 'skill-wanderer-hub.appspot.com',
  messagingSenderId: process.env.NUXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID || '801841516442',
  appId: process.env.NUXT_PUBLIC_FIREBASE_APP_ID || '1:801841516442:web:77c33043420a581b95f423',
}
```

The hardcoded values mean that if environment variables are absent (e.g., local dev, a misconfigured CI pipeline), the production Firebase project is used and credentials are visible in version control history.

#### P2 — HIGH: No email notification on form submission

A business lead submitting `contact-messages` creates a Firestore document with `status: 'new'` — but nothing triggers an alert to the business owner. Leads can sit unread indefinitely. This is a direct revenue risk for a services business.

#### P3 — HIGH: No spam protection or rate limiting

Any bot or automated script can submit the forms indefinitely. Firestore write costs scale per operation. A spam attack could: fill Firestore with junk, increase GCP costs, and bury real leads.

#### P4 — MEDIUM: No server-side validation or sanitization

All validation is browser-only (HTML5 attributes). These checks are trivially bypassed using `fetch()` or `curl`. Malicious payloads (including XSS scripts, oversized payloads) are stored directly in Firestore without any cleansing.

#### P5 — MEDIUM: No error reporting or observability

When `handleSubmit` catches an error, it shows a generic message and swallows the error. There is no logging (e.g., Sentry, console error forwarding) and no way to know how often submissions fail in production.

#### P6 — LOW: Single client-side point of failure

If Firebase JS SDK fails to initialize (network issue, ad blocker blocking Firebase domains, quota exceeded), the form is silently broken. The user sees the form normally until they try to submit.

### Stakeholder Impact

| Stakeholder | Current State | Risk |
|---|---|---|
| **User (submitter)** | Receives success feedback, but no confirmation email | Uncertainty — did it send? |
| **Business owner** | Leads land in Firestore with no notification | Lost leads, missed revenue |
| **Developer** | No error visibility | Hard to diagnose production failures |

---

## Constraints & Risks

### Technical Constraints

- **Deployment:** Cloudflare Pages (`nitro.preset: 'cloudflare-pages'`). Nuxt server routes run as Cloudflare Workers (edge functions). This supports simple serverless handlers but not long-running processes.
- **Stack:** Nuxt 3 / Vue 3 / Firebase / Tailwind. No traditional backend infrastructure. No existing email sending library.
- **Firebase project:** `skill-wanderer-hub` — single project, free tier limits apply.
- **No existing auth:** Firebase Auth is initialized but commented out. Firestore writes happen unauthenticated from the client.

### Risks

| Risk | Likelihood | Impact | Severity |
|---|---|---|---|
| Spam / bot flood of Firestore | High | High (cost + data pollution) | **CRITICAL** |
| Business owner never sees a lead | Medium | High (lost revenue) | **HIGH** |
| Firebase quota exhaustion from abuse | Medium | High (all Firestore writes fail) | **HIGH** |
| Credential exposure in git history | Already occurred | Medium (project key public) | **HIGH** |
| Silent submission failures in production | Medium | Medium (lost leads undetected) | **MEDIUM** |
| XSS payload stored in Firestore | Low | Medium (affects admin UI if any) | **MEDIUM** |

---

## Blast Radius Analysis

### Failure Scenario: System fails entirely

- Users submit form → see error message → no lead captured
- Business owner receives no indication anything is broken
- Could go undetected for days or weeks
- **Impact:** All inbound project leads and guild applications lost during outage window

### Failure Scenario: Spam attack

- Thousands of documents written to `contact-messages` and `guild-applications`
- Firestore free tier (50K reads/day, 20K writes/day) exhausted
- Legitimate submissions begin failing with Firestore quota error — same generic error as all other failures
- Real leads buried or failing
- **Impact:** Revenue loss, increased GCP costs, data integrity compromise

### Who Is Impacted

1. **Prospective clients** — their inquiry never reaches the business
2. **Guild applicants** — application disappears into void
3. **Business owner** — no pipeline visibility, no lead management
4. **Firebase project** — potential quota abuse costs

---

## Solution Options

---

### Solution 1 — Third-Party Form Service (Formspree / Web3Forms)

**Overview:** Replace Firestore writes with a POST to a managed form service API. The service handles storage, spam filtering, and email delivery.

**Workflow:**
```
User fills form
  → handleSubmit() POSTs to https://formspree.io/f/{formId}
  → Formspree validates, filters spam
  → Stores submission in Formspree dashboard
  → Sends email notification to quan.nguyen@skill-wanderer.com
  → Returns 200/422 JSON
  → UI shows success/error
```

**Pros:**
- Zero backend code to write
- Built-in spam filtering (honeypot, reCAPTCHA option)
- Instant email notifications
- Submission dashboard
- Free tier: 50 submissions/month (Formspree)
- GDPR-compliant options available

**Cons:**
- Data leaves your infrastructure (third-party custody)
- Monthly submission limit on free tier
- Paid plan required for high volume or custom logic
- Schema is fixed by form field names — less flexible
- Loss of Firestore as a queryable submissions database

**Trade-offs:**
- Best for: fast fix, minimal engineering investment
- Worst for: data ownership, customization, high volume

---

### Solution 2 — Nuxt Server API Route + Email (Nodemailer / Resend)

**Overview:** Create a Nuxt server route (`server/api/contact.post.ts`) that handles validation, writes to Firestore server-side with admin credentials, and sends a transactional email via Resend (or similar).

**Workflow:**
```
User fills form
  → handleSubmit() POSTs to /api/contact (JSON body)
  → Nuxt server route (Cloudflare Worker) runs:
      ├── Input validation (length, format, required fields)
      ├── Basic spam check (keyword filter, honeypot field)
      ├── Write to Firestore via Firebase Admin SDK
      └── Send email via Resend API (server-side API key)
  → Returns structured JSON response
  → UI handles success/error state
```

**Pros:**
- Full data ownership (Firestore stays as storage)
- Server-side validation — bypass-proof
- Email notification on every lead
- Credentials never exposed to client
- Extensible: can add rate limiting, CAPTCHA verification, CRM webhook
- Error can be logged properly server-side

**Cons:**
- Requires writing Nuxt server API route
- Requires Firebase Admin SDK (`firebase-admin` package) or Firestore REST API
- Requires Resend/SendGrid account (free tiers available)
- Cloudflare Worker environment has constraints (no Node.js `net` module — Nodemailer won't work; use Resend or fetch-based email API instead)
- More moving parts than Solution 1

**Trade-offs:**
- Best for: long-term maintainability, data ownership, extensibility
- Worst for: quick deployment

---

### Solution 3 — Firestore + Firebase Cloud Functions (Trigger-Based Email)

**Overview:** Keep the current client-side Firestore write as-is, but add a Firebase Cloud Function that triggers on new documents in `contact-messages` and `guild-applications`, then sends an email notification.

**Workflow:**
```
User fills form
  → handleSubmit() writes to Firestore (as today)
  → Firebase Cloud Function triggers on onDocumentCreated()
      ├── Reads new document fields
      ├── Sends email via Firebase Extensions (Email Trigger) or Nodemailer
      └── Updates document status: 'notified'
  → Business owner receives email
```

**Pros:**
- Minimal change to existing frontend code
- Decoupled — form still works even if email function fails
- Scales independently
- Firebase Extensions make email setup simple (Trigger Email extension)
- Retries on failure (Cloud Functions retry policy)

**Cons:**
- Requires Firebase Blaze plan (pay-as-you-go) — Spark plan does not support Cloud Functions
- Cold start latency on email delivery
- Spam problem is NOT solved — bots still write to Firestore
- Still no server-side validation
- Two separate systems to maintain (Nuxt app + Firebase Functions)
- Adds operational complexity

**Trade-offs:**
- Best for: minimal frontend changes, async notification
- Worst for: spam prevention, validation, keeping stack simple

---

### Solution 4 — Hybrid: Nuxt Server Route + Firestore + Rate Limiting + Email

**Overview:** Combines Solutions 2 and 3 into a single coherent system. The server route is the single entry point — it validates, rate-limits, writes to Firestore with admin credentials, and triggers email.

**Workflow:**
```
User fills form
  → handleSubmit() POSTs to /api/contact
  → Server route:
      ├── Validate IP-based rate limit (Cloudflare KV or in-memory)
      ├── Validate honeypot field (must be empty)
      ├── Validate required fields and length limits server-side
      ├── Sanitize string inputs
      ├── Write to Firestore (Firebase Admin REST API — no Node dep)
      └── Send email via Resend API
  → 200 or 400/429 response
  → UI renders appropriate message
```

**Pros:**
- Single control point for all security concerns
- No Firestore credentials in browser
- Rate limiting at the edge (Cloudflare Worker compatible)
- Email notification with structured lead data
- Full observability possible
- No upgrade to Firebase Blaze plan required

**Cons:**
- Highest initial engineering effort
- Firebase Admin SDK does not run in Cloudflare Workers (need REST API workaround)
- More files to maintain

**Trade-offs:**
- Best for: production-grade, long-term system
- Worst for: speed of deployment

---

## Architecture Blueprint

### AS-IS (Current)

```
┌─────────────────────────────────────────────────────────────────┐
│  Browser (Client Only)                                          │
│                                                                 │
│  pages/contact.vue                                              │
│    ├── handleSubmit()                                           │
│    │     └── useNuxtApp().$firestore                            │
│    │           └── addDoc(collection, "contact-messages")       │
│    │                 └── [NO VALIDATION]                        │
│    │                 └── [NO SPAM CHECK]                        │
│    │                 └── [NO EMAIL NOTIFICATION]                │
│    │                 └── [NO RATE LIMIT]                        │
│    └── handleGuildSubmit()                                      │
│          └── addDoc(collection, "guild-applications")           │
│                └── [same missing controls]                      │
└──────────────────────────────────┬──────────────────────────────┘
                                   │ Firebase JS SDK
                                   │ (API key exposed in browser)
                                   ▼
                    ┌──────────────────────────┐
                    │  Google Firestore         │
                    │  Project: skill-wanderer  │
                    │  ├── contact-messages     │
                    │  └── guild-applications   │
                    │                           │
                    │  [DEAD END — no alerts]   │
                    └──────────────────────────┘
```

### TO-BE (Recommended — Solution 2/4 Hybrid)

```
┌─────────────────────────────────────────────────────────────────┐
│  Browser (Client Only)                                          │
│                                                                 │
│  pages/contact.vue                                              │
│    ├── handleSubmit()                                           │
│    │     └── fetch('/api/contact', { method: 'POST', body })    │
│    └── handleGuildSubmit()                                      │
│          └── fetch('/api/guild-application', { ... })           │
└──────────────────────────────────┬──────────────────────────────┘
                                   │ HTTPS POST (no Firebase key exposed)
                                   ▼
┌─────────────────────────────────────────────────────────────────┐
│  Nuxt Server Route (Cloudflare Worker / Edge)                   │
│                                                                 │
│  server/api/contact.post.ts                                     │
│    ├── 1. Honeypot field check → 400 if filled                  │
│    ├── 2. Rate limit (IP, max 5 req/min) → 429 if exceeded      │
│    ├── 3. Input validation (required, length, format) → 400     │
│    ├── 4. Sanitize input (strip HTML tags)                      │
│    ├── 5. Write to Firestore (REST API w/ service account JWT)  │
│    └── 6. POST to Resend API → email to business owner          │
└────────────────┬───────────────────────┬────────────────────────┘
                 │                       │
                 ▼                       ▼
  ┌──────────────────────────┐  ┌──────────────────────────────┐
  │  Google Firestore         │  │  Resend (email delivery)     │
  │  ├── contact-messages     │  │  → quan.nguyen@skill-...com  │
  │  └── guild-applications   │  │  → Structured lead summary   │
  └──────────────────────────┘  └──────────────────────────────┘
```

---

## Comparison Matrix

| Criteria | Current | Solution 1 (Formspree) | Solution 2 (Server API) | Solution 3 (Cloud Functions) | Solution 4 (Hybrid) |
|---|---|---|---|---|---|
| **Scalability** | Low — Firestore quota abusable | Medium — managed limits | High — rate limiting at edge | Medium — CF cold starts | High — edge + rate limiting |
| **Maintainability** | Low — no visibility | High — third-party managed | Medium — own code, simple route | Low — two separate systems | Medium — single API layer |
| **Security** | Critical issues | Medium — third-party data | High — no client-side keys | Low — client writes still unguarded | High — full server control |
| **Reliability** | Low — silent failures | High — managed uptime | High — structured error handling | Medium — async retry | High — sync with retry |
| **Extensibility** | None | Low — third-party lock-in | High — add webhooks, CRM, etc. | Medium — Firebase-only extensions | High — any HTTP integration |
| **Email Notification** | ❌ None | ✅ Built-in | ✅ Resend / SMTP | ✅ Trigger Email Extension | ✅ Resend |
| **Spam Protection** | ❌ None | ✅ Built-in | ✅ Honeypot + rate limit | ❌ Still client writes | ✅ Honeypot + rate limit |
| **Implementation Effort** | — | Very Low | Medium | Medium | Medium-High |
| **Cost** | Free (until abused) | Free to ~$10/mo | Free (Resend free tier) | Requires Blaze plan | Free (Resend free tier) |

---

## Risk Simulation

### Scenario 1: Spam Attack

**Trigger:** Bot discovers the endpoint and submits 1,000 forms/minute.

| System | Outcome |
|---|---|
| **Current** | 1,000 Firestore writes/minute, free tier (20K writes/day) exhausted in 20 minutes. All subsequent legitimate submissions fail silently. Real leads buried. |
| **Solution 1 (Formspree)** | Formspree bot protection activates. Spam submissions blocked at Formspree edge. Firestore not touched. |
| **Solution 2 (Server API)** | Rate limiter returns 429 after 5 requests/minute/IP. Bots blocked. Honeypot catches automated form fills. |
| **Solution 3 (Cloud Functions)** | Spam writes Firestore unchecked. Cloud Function triggers 1,000 email sends — Resend/SendGrid rate limited, email floods inbox. Firestore quota still exhausted. |
| **Solution 4 (Hybrid)** | Same as Solution 2 — rate limiting and honeypot block at edge. |

### Scenario 2: High Traffic (Product Hunt / HN Launch)

**Trigger:** 500 legitimate users visit `/contact` and 50 submit forms within 1 hour.

| System | Outcome |
|---|---|
| **Current** | 50 Firestore writes succeed (well within quota). Business owner receives no notification. Leads sit unread. |
| **Solution 2 / 4** | 50 server API calls processed. 50 emails sent. Business owner notified within seconds of each submission. Rate limit (per IP) not triggered by organic traffic. |

### Scenario 3: Failed Submission

**Trigger:** Firebase is temporarily unavailable (GCP incident or network partition).

| System | Outcome |
|---|---|
| **Current** | `addDoc()` throws. Catch block shows generic error. No retry. No logging. Lead permanently lost. Developer has no idea this happened. |
| **Solution 2 / 4** | Server route catches error, logs it server-side (visible in Cloudflare Workers logs), returns structured `500` response. User sees specific message. Developer can set up alert on error rate. |

### Scenario 4: Email Delivery Failure

**Trigger:** Resend API is down or API key is invalid.

| System | Outcome |
|---|---|
| **Current** | N/A — no email system. |
| **Solution 2 / 4** | Email send fails. Server route can be designed to still write to Firestore (lead is saved) but return partial success. Business owner sees lead next time they check Firestore. Degraded but not catastrophic. |

### Scenario 5: Developer Accidentally Commits Without Env Vars

**Trigger:** New developer clones repo, runs without `.env`. Submits test form.

| System | Outcome |
|---|---|
| **Current** | Hardcoded Firebase fallbacks activate. Writes go to **production** Firestore silently. Credentials committed to git are already exposed. |
| **Solution 2 / 4** | Server route reads `process.env.FIREBASE_*` — if absent, throws at startup. No silent fallback to production credentials. Developer sees immediate configuration error. |

---

## Final Recommendation

### For a Quick Fix (Days)

**→ Solution 1: Formspree or Web3Forms**

Replace `handleSubmit()` and `handleGuildSubmit()` to POST to Formspree endpoints. Remove Firebase dependency from the contact flow entirely. Takes 2–4 hours. Immediately solves:
- Email notifications
- Basic spam protection
- Silent failure risk

**Confidence: High** — Formspree is production-tested at scale. The trade-off (data leaves your infrastructure, ~50 free submissions/month) is acceptable for current traffic volume.

---

### For the Long-Term Scalable System (Weeks)

**→ Solution 4: Nuxt Server API Route + Firestore REST + Resend**

Create `server/api/contact.post.ts` and `server/api/guild-application.post.ts`. Implement honeypot validation, per-IP rate limiting using Cloudflare KV (or a simple in-memory map for initial version), server-side input sanitization, Firestore write via REST API with a service account, and Resend transactional email.

**Confidence: High** — This aligns with the existing stack (Nuxt 3 + Cloudflare Pages), keeps data in your infrastructure, eliminates credential exposure, and provides a foundation for CRM integration, webhook delivery, and lead management.

**Priority actions before any solution is implemented:**

1. **Remove hardcoded Firebase credentials from `nuxt.config.ts`** — use only `process.env.*` without fallback values. Rotate the exposed API key in the Firebase console and restrict it to allowed HTTP referrers.
2. **Set up Firestore security rules** to reject unauthenticated writes to `contact-messages` and `guild-applications` from the client (only the server service account should write).
3. **Add a Firestore listener or scheduled check** as a stopgap to alert on new `status: 'new'` documents until a full solution is deployed.

---

*This document is for engineering investigation purposes only. No production code was modified. Do not include in PR.*
