# Contact Form Vendor Evaluation & Execution Plan

---

## Executive Summary

| Area | Summary |
|------|--------|
| Problem | Current contact workflow lacks visibility, structured review, and reliable notification handling |
| Business Impact | Risk of missed leads, delayed responses, and lack of intake tracking |
| Decision | Use Formspree as Phase 1 stabilization solution |
| Timeline | Go live within 1 week |
| Effort | Low engineering effort (1-2 days integration) |
| Next Step | Transition to NestJS internal system after workflow validation |

### One-Line Decision

We will implement Formspree this week to stabilize intake operations and prepare for a future internal system.

## 1. Objective

Skill-Wanderer needs a vendor comparison because the current `/contact` page already runs two real intake workflows from the Nuxt frontend:

- `Hire the Guild` for project and partnership inquiries
- `Join the Guild` for guild applications

Today, those workflows are handled directly in `pages/contact.vue` with client-side writes to Firebase. That keeps the UI simple, but it does not give the business owner a strong review console, notification workflow, or low-friction operating model for day-to-day intake handling.

Phase 1 is not about building the final long-term intake platform. Its role is to stabilize submission handling quickly with a managed vendor so the owner can reliably receive, review, and act on inquiries without waiting for a NestJS backend program.

This document supports one immediate decision:

- Which third-party form vendor should Skill-Wanderer use for Phase 1 stabilization?
- How should that vendor be implemented in the current Nuxt codebase with minimal confusion for both the owner and engineers?

## Business Impact Summary

### Current Risks

- Silent lead loss due to lack of structured intake visibility
- Delayed response time due to missing notification discipline
- No centralized review queue -> inconsistent follow-up
- Operational ambiguity -> owner cannot confidently track inquiries

### Business Consequences

- Direct revenue loss from missed project inquiries
- Reduced conversion from slow or inconsistent responses
- Lower trust in contact channel reliability
- Increased manual overhead and cognitive load for the owner

### Phase 1 Impact (After Implementation)

- Immediate visibility of all incoming submissions
- Reliable notification system with backup channels
- Structured review workflow with clear ownership
- Faster response time and improved lead handling discipline

---

## 2. Vendors Evaluated

- **Formspree**
- **Web3Forms**
- **Getform / FormInit**

Note: `getform.io` currently redirects to `forminit.com`. For evaluation purposes, this document treats that option as **Getform / FormInit**, because that is the live product a buyer would actually configure today.

---

## 3. Vendor Comparison (High-Level)

| Dimension | Formspree | Web3Forms | Getform / FormInit |
|----------|----------|-----------|--------------------|
| Setup complexity | Low. Create a project and two forms, then swap Nuxt handlers to vendor endpoints. | Very low. Generate an access key and post to one API endpoint. | Low to medium. Create a workspace and form objects, then map review and notification settings. |
| Dashboard availability | Strong. Inbox, analytics, search, export, project-level management. | Basic. Submission history exists, but the operating model is still primarily email-first. | Strong. Central inbox, analytics, logs, and workspace-style administration. |
| Notification options | Email notifications, autoresponses, multiple recipients on paid tiers, direct integrations, webhooks. | Direct email delivery, autoresponders on paid tiers, multiple recipients on paid tiers, webhooks and app integrations. | Email notifications, autoresponders, Slack, Discord, webhooks, Zapier, custom sender on higher tier. |
| Integration flexibility | High. HTML, JavaScript, API-style integration, direct integrations, webhooks, rules on higher tier. | Medium. Works with any frontend and is extremely simple, but has fewer workflow primitives. | High. Headless endpoint, TypeScript SDK, REST API, webhooks, workspace-oriented operations. |
| Pricing model | Free: 50 submissions/month. Paid tiers start at $10/month and scale by submissions and workflow features. | Free: 250 submissions/month. Paid tiers are low-cost yearly plans with clear submission limits. | Free: 1 form and 50 submissions/month. Paid tiers start higher but include richer operator tooling. |
| Vendor maturity | High. Established platform, extensive help docs, status page, large public customer base. | Medium. Well-suited for static sites, lower-friction product, lighter operating surface. | Medium to high. Long-running product lineage, stronger operator features, but current branding transition adds some procurement ambiguity. |
| Workflow capability | Strong for Phase 1. Good inbox, analytics, exports, and upgrade path into richer routing. | Adequate for simple owner inbox workflows. Best when email is the main review surface. | Strong. Good fit when the owner wants a centralized queue plus logs and team-oriented review surfaces. |

## Effort & Cost Overview

### Vendor Comparison (Effort vs Cost)

| Vendor | Engineering Effort | Time to Implement | Cost (Estimated) | Operational Complexity |
|--------|------------------|------------------|------------------|------------------------|
| Formspree | Low | 1-2 days | Free -> ~$10/month | Low |
| Web3Forms | Very Low | <1 day | Free -> Low-cost | Low |
| FormInit | Medium | 1-2 days | Paid earlier | Medium |

### Phase Effort Overview

| Phase | Effort Level | Time | Description |
|------|------------|------|-------------|
| Phase 1 | Low | 2-5 days | Vendor integration and workflow stabilization |
| Phase 2 | Medium-High | 4-8 weeks | Internal NestJS system development and migration |

### Interpretation

- Phase 1 is low-cost, low-risk, and fast to deploy
- Phase 2 is higher investment but enables long-term control
- Choosing Formspree minimizes initial effort while preserving future flexibility

---

## 4. Deep Comparison (Operational)

### 4.1 Formspree

#### Overview

Formspree is the best-balanced Phase 1 option when the business needs a managed inbox, reliable notifications, and a low-effort Nuxt integration without immediately building custom backend workflow. For this repo, it maps cleanly to two separate form endpoints: one for `Hire the Guild` and one for `Join the Guild`.

#### 5W1H Table

| Dimension | Detail |
|----------|--------|
| What | A managed form backend with hosted form endpoints, owner-facing inbox, analytics, export, and integration options. |
| Why | It gives Skill-Wanderer the fastest path to a business-usable intake workflow instead of only storing submissions behind the scenes. |
| Who | An engineer configures the two forms and integrates the endpoints. The business owner reviews submissions in Formspree and responds from email or downstream tools. |
| When | Best for immediate Phase 1 stabilization when the goal is to go live quickly this week and reduce lead-handling ambiguity. |
| Where | User interaction stays in Nuxt. Submission storage, notifications, and owner review happen in Formspree's inbox and connected channels. |
| How | Create a Formspree project, create two forms, store the form IDs in Nuxt runtime configuration, and replace the current Firebase write logic with `fetch` or native form POSTs. |

#### Workflow (Visual Diagram)

##### High-Level Flow (Simple View)

```text
User (Client)
  -> Nuxt Frontend (Client)
  -> Formspree Endpoint (Vendor System)
  -> Notification Layer (Vendor System)
  -> Review Layer (Business Layer)
```

##### Detailed System Flow (Production View)

```text
User (Client)
  |
  v
Nuxt /contact Page (Client)
  |
  +--> Hire the Guild Tab
  |      +--> name / email / topic / message / valuesQuality
  |
  +--> Join the Guild Tab
  |      +--> name / email / skill / experience / portfolio / message
  |
  v
Submission Handler (Client)
  |
  +--> UI state handling
  |      +--> loading
  |      +--> success message
  |      +--> retryable error message
  |
  +--> Payload shaping
  |      +--> form_type=hire or join
  |      +--> source=/contact
  |      +--> business-readable field names
  |
  v
Formspree Endpoint over HTTPS (Vendor System)
  |
  v
Formspree Intake Layer (Vendor System)
  |
  +--> Vendor validation / filtering
  |      +--> field checks
  |      +--> vendor-side spam controls
  |
  +--> Routing Decision
  |      |
  |      +--> hire-the-guild form
  |      +--> join-the-guild form
  |
  +--> Notification Layer
  |      |
  |      +--> Email notification to owner inbox
  |      +--> Slack / webhook integration (if enabled)
  |      +--> Autoresponse to submitter (optional)
  |
  v
Formspree Storage / Inbox (Vendor System)
  |
  +--> Submission archive
  +--> Search / export / analytics
  +--> Queue separated by form or tag
  |
  v
Business Review Layer (Business Layer)
  |
  +--> Who reviews
  |      +--> business owner
  |      +--> optional backup reviewer
  |
  +--> Where review happens
  |      +--> Formspree Inbox
  |      +--> notification email inbox
  |
  +--> How decisions are made
  |      +--> project lead
  |      +--> partnership discussion
  |      +--> guild applicant
  |      +--> duplicate / low-priority archive
  |
  +--> Failure handling branch
  |      +--> if notification delayed -> open Formspree Inbox directly
  |      +--> if inbox backlog grows -> run manual dashboard sweep
  |
  v
Business Action Layer (Business Layer)
  |
  +--> Reply email
  +--> Calendar scheduling / proposal follow-up
  +--> CRM or tracker entry
  +--> Internal routing to delivery / recruiting workflow
  |
  v
Feedback Loop (Business Layer + Vendor System)
  |
  +--> mark handled / tagged in vendor inbox
  +--> refine notification rules and labels
  +--> use export data to inform Phase 2 design
```

##### Phase 1 Workflow (Vendor-based)

```text
User (Client)
  |
  v
Nuxt Frontend (Client)
  |
  +--> Hire form submission
  +--> Join form submission
  |
  v
Formspree Forms A and B (Vendor System)
  |
  +--> Notification Layer
  |      +--> Email
  |      +--> Slack / webhook (optional)
  |
  +--> Formspree Inbox / archive
  |
  v
Owner Review Loop (Business Layer)
  |
  +--> review in dashboard first
  +--> confirm via email notification
  +--> decide next business action
  |
  v
Follow-up / response / tracker update (Business Layer)
```

##### Phase 2 Workflow (NestJS-based)

```text
User (Client)
  |
  v
Nuxt Frontend (Client)
  |
  v
Submission API / BFF (Internal System)
  |
  v
NestJS Intake Controller (Internal System)
  |
  +--> validation / classification
  +--> route to inquiry type
  |
  v
Primary Storage (Internal System)
  |
  +--> inquiry records
  +--> application records
  |
  v
Queue / Worker Layer (Internal System)
  |
  +--> Email notification
  +--> Slack / webhook
  +--> CRM sync
  |
  +--> if notification delayed -> queue retry + internal review fallback
  |
  v
Internal Review Surface (Business Layer)
  |
  +--> dashboard / CRM / internal admin
  +--> manual review and categorization
  |
  v
Business Action Layer (Business Layer)
  |
  +--> reply
  +--> assign owner
  +--> move to delivery or recruiting flow
```

##### Transition Workflow

```text
User (Client)
  |
  v
Nuxt Frontend (Client)
  |
  +--> Primary Path: Formspree (Vendor System)
  |      +--> live production intake
  |      +--> owner continues review in Formspree Inbox
  |
  +--> Secondary Path: NestJS shadow or limited rollout (Internal System)
  |      +--> mirrored or sampled submissions
  |      +--> internal storage and notification testing
  |
  v
Cutover Decision Point (Business Layer + Internal System)
  |
  +--> if parity confirmed
  |      +--> promote NestJS to primary intake
  |      +--> keep Formspree available for rollback and export
  |
  +--> if operational gaps remain
         +--> keep Formspree primary
         +--> continue transition testing

Temporary coexistence:
  Formspree = operational safety net
  NestJS = emerging internal control plane

Fallback path:
  if cutover disrupts notifications or review visibility
    -> route traffic back to Formspree
    -> continue business review from vendor inbox
```

#### Operational Characteristics Table

| Category | Detail |
|----------|--------|
| Operational Characteristics | - Operates as a managed intake layer with clear separation between the public Nuxt form experience and the vendor-managed submission workflow.<br>- Daily usage pattern is dashboard-first: submissions land in a central inbox, notifications are dispatched, and the owner can review or export from one place instead of relying only on raw email threads.<br>- Supports a clean Phase 1 operating model for this repo because `Hire the Guild` and `Join the Guild` can be modeled as separate forms with separate routing and review paths.<br>- Gives the business an immediate operating console for intake visibility, which is materially better than silent storage with no structured review queue.<br>- System evolution is moderate to strong: the business can start with inbox + email, then add tags, exports, autoresponses, and integrations before committing to an internal NestJS control plane. |
| Strengths | - Fast deployment in practical terms: a single engineer can usually configure the account, create two forms, and integrate the frontend in hours to a day.<br>- Strong owner usability: inbox, search, archive, and export reduce the risk that an inquiry is technically received but operationally missed.<br>- Flexible workflow surface for a small services business: multiple notification routes, integrations, and later upgrade options support gradual maturity without immediate backend work.<br>- Clear business impact: improves lead handling discipline quickly by making intake visible, reviewable, and auditable from a business-owner perspective.<br>- Better decision support than an email-only tool because historical submissions remain accessible in a structured queue. |
| Limitations | - Business process state still lives primarily in a vendor tool, so the canonical review experience is external to Skill-Wanderer's own systems.<br>- Costs can rise as monthly submission volume, automation needs, or team coordination needs increase, especially if the owner wants richer routing or more advanced workflow behavior.<br>- Once the team starts relying on tags, inbox habits, exports, and vendor integrations, migration becomes more operationally involved even if technically feasible.<br>- Good for structured intake, but not ideal as a permanent system of record if the business later wants custom states, internal notes, or deeper operational analytics tied to delivery workflows. |
| Operational Risks (Non-Security) | - Vendor lock-in risk is moderate: exports exist, but the owner's operating habits may become anchored to the Formspree inbox rather than an internal system.<br>- Pricing predictability is acceptable at low volume, but can become less comfortable if campaigns or content growth increase submissions and require higher tiers.<br>- Visibility gaps can still happen if the owner treats email as the only control plane and stops reviewing the dashboard regularly.<br>- Dependency risk is real: if Formspree changes plan features, routing behavior, or integration availability, the operational model must adapt around a third-party roadmap rather than an internal one.<br>- Follow-up drift can emerge if dashboard review, inbox replies, and downstream CRM or spreadsheet tracking are not kept aligned. |
| Best-Fit Scenarios | - Startup landing page or service-business site where owner visibility and fast deployment matter more than deep backend customization.<br>- Portfolio or consultancy site that needs submissions captured reliably and reviewed from a clean inbox rather than from ad hoc email forwarding logic.<br>- Low-to-mid volume growth-stage site with multiple inquiry types, especially where project leads and applicant flows should be separated cleanly.<br>- Less ideal for a high-volume SaaS or internal enterprise tool where submissions are expected to feed custom lifecycle states, internal approval flows, or system-of-record reporting from day one. |
| Scalability Behavior | - **Low traffic (0-50 submissions/day):** excellent fit; low operational overhead, strong cost efficiency, and the inbox remains easy for one owner to manage without additional process layers.<br>- **Medium traffic (50-500/day):** still viable, but costs, inbox discipline, and routing expectations become more important; the business may need stronger tagging, multiple reviewers, or downstream integrations to avoid backlog buildup.<br>- **High traffic (500+/day):** platform can still receive submissions, but business operations become the bottleneck; queue triage, cost growth, and demand for custom routing/reporting make a managed inbox less comfortable as the primary operating model.<br>- Operational implication: Formspree scales better as a small-team intake platform than as a long-term high-throughput workflow engine. |
| Failure Behavior | - If the vendor has downtime, the frontend can remain visually available while submission handling degrades, so the owner must be ready to fall back to direct email visibility and vendor status checks.<br>- If notifications are delayed, the main fallback is dashboard-first review: the owner should check the Formspree inbox directly rather than assuming that no email means no submission.<br>- Submission-loss scenarios are more likely to appear as operational blind spots than as obvious user-facing incidents, for example when an inquiry lands in the inbox but nobody reviews it promptly.<br>- Recovery behavior is mostly vendor-dependent, while business continuity depends on having a review routine that includes inbox checks, not only notifications. |
| System Ownership | - Formspree owns the intake infrastructure, hosted storage layer, notification mechanics, and dashboard behavior.<br>- Skill-Wanderer owns the public form UX, field design, review discipline, follow-up process, and any downstream business handling after a submission is received.<br>- Data flow is vendor-controlled once the Nuxt client posts to the endpoint; the business can export and integrate, but it does not control the runtime path or internal vendor processing behavior.<br>- Responsibility for business failure is shared but asymmetrical: vendor issues affect delivery mechanics, while missed reviews, slow replies, and process confusion remain the responsibility of the owner and internal operators.<br>- Dependency level is moderate: Formspree is a strong Phase 1 operating partner, but not the final ownership model if Skill-Wanderer wants a fully internal control plane later. |

### 4.2 Web3Forms

#### Overview

Web3Forms is the simplest and lowest-cost operational option. It is especially strong when the business owner's preferred workflow is direct email intake and the engineering team wants the fastest possible replacement for the current Firebase submission path.

#### 5W1H Table

| Dimension | Detail |
|----------|--------|
| What | A lightweight API-style form backend that routes submissions to email and stores them in a basic hosted history. |
| Why | It minimizes setup time and cost while still replacing the current hidden intake flow with a vendor-managed delivery path. |
| Who | An engineer wires the Nuxt handlers to the Web3Forms endpoint. The business owner mainly operates through email, with occasional use of the hosted submission history. |
| When | Best when Phase 1 must be fast and low-cost, and the owner is comfortable running intake primarily from their inbox. |
| Where | Form capture remains on the Nuxt page. Submission delivery happens through the Web3Forms API, email inboxes, and limited hosted history/export. |
| How | Create an access key, configure one endpoint per flow or add a `form_type` field, post the payload from Nuxt, and route alerts to the owner's inbox and backup channel. |

#### Workflow (Visual Diagram)

##### High-Level Flow (Simple View)

```text
User (Client)
  -> Nuxt Frontend (Client)
  -> Web3Forms API (Vendor System)
  -> Notification Layer (Vendor System)
  -> Review Layer (Business Layer)
```

##### Detailed System Flow (Production View)

```text
User (Client)
  |
  v
Nuxt /contact Page (Client)
  |
  +--> Hire the Guild submission
  +--> Join the Guild submission
  |
  v
Submission Handler (Client)
  |
  +--> prepares payload
  |      +--> access_key
  |      +--> form_type
  |      +--> business fields
  |
  +--> manages UI state
  |      +--> sending
  |      +--> success
  |      +--> retry message
  |
  v
Web3Forms API over HTTPS (Vendor System)
  |
  v
Web3Forms Intake Layer (Vendor System)
  |
  +--> basic validation / required field handling
  +--> vendor-side spam filtering / honeypot support
  +--> submission acceptance decision
  |
  +--> Notification Layer
  |      |
  |      +--> Email notification (primary path)
  |      +--> Slack / webhook / external integration (if configured)
  |      +--> Autoresponder (paid tiers)
  |
  v
Web3Forms Submission History (Vendor System)
  |
  +--> limited hosted history
  +--> export / lookup fallback
  |
  v
Business Review Layer (Business Layer)
  |
  +--> Who reviews
  |      +--> owner as primary reviewer
  |      +--> optional backup reviewer for overflow
  |
  +--> Where review happens
  |      +--> primary: email inbox
  |      +--> fallback: Web3Forms submission history
  |
  +--> How decisions are made
  |      +--> urgent lead -> immediate reply
  |      +--> guild application -> scheduled review
  |      +--> low-fit inquiry -> archive or defer
  |
  +--> Failure handling branch
  |      +--> if notification delayed -> check submission history directly
  |      +--> if inbox becomes noisy -> export and track manually
  |
  v
Business Action Layer (Business Layer)
  |
  +--> reply email
  +--> spreadsheet / CRM entry
  +--> internal handoff to delivery or guild review
  |
  v
Feedback Loop (Business Layer)
  |
  +--> refine field labels / redirect behavior
  +--> tighten owner review cadence
  +--> use observed workflow pain to justify Phase 2 timing
```

##### Phase 1 Workflow (Vendor-based)

```text
User (Client)
  |
  v
Nuxt Frontend (Client)
  |
  v
Web3Forms API (Vendor System)
  |
  +--> Email notification to owner
  +--> Optional webhook / Slack path
  +--> Submission history fallback
  |
  v
Owner Review Loop (Business Layer)
  |
  +--> primary review in inbox
  +--> fallback review in submission history
  +--> manual decision on follow-up and tracking
  |
  v
Business Action Layer (Business Layer)
  |
  +--> reply
  +--> log in tracker
  +--> continue manual workflow
```

##### Phase 2 Workflow (NestJS-based)

```text
User (Client)
  |
  v
Nuxt Frontend (Client)
  |
  v
Submission API / BFF (Internal System)
  |
  v
NestJS Intake Controller (Internal System)
  |
  +--> classify inquiry type
  +--> persist request
  |
  v
Storage + Queue Layer (Internal System)
  |
  +--> primary records
  +--> notification jobs
  +--> webhook / CRM jobs
  |
  +--> if notification delay occurs -> queue retry + dashboard fallback
  |
  v
Internal Review Surface (Business Layer)
  |
  +--> structured queue
  +--> explicit handled states
  |
  v
Business Action Layer (Business Layer)
  |
  +--> response
  +--> ownership assignment
  +--> reporting and workflow iteration
```

##### Transition Workflow

```text
User (Client)
  |
  v
Nuxt Frontend (Client)
  |
  +--> Current production path: Web3Forms (Vendor System)
  |      +--> email-first operational flow remains active
  |
  +--> Transition path: NestJS shadow or feature-flag rollout (Internal System)
  |      +--> mirrored submission testing
  |      +--> internal notification and queue validation
  |
  v
Cutover Decision Point (Business Layer + Internal System)
  |
  +--> if internal review is reliable
  |      +--> move primary traffic to NestJS
  |      +--> retain Web3Forms briefly for rollback
  |
  +--> if review confidence is low
         +--> keep Web3Forms primary
         +--> continue transition testing

Temporary coexistence:
  Web3Forms inbox/email path = business continuity
  NestJS queue = future-state operating model

Fallback path:
  if NestJS cutover causes review confusion
    -> revert routing to Web3Forms
    -> continue owner review through inbox + submission history
```

#### Operational Characteristics Table

| Category | Detail |
|----------|--------|
| Operational Characteristics | - Operates as a lightweight API backend where the primary operational experience is email delivery first and hosted history second.<br>- Fits teams that want minimum setup friction and do not need a rich vendor-side operating console from the start.<br>- For this repo, it can stabilize both contact workflows quickly, but it does so with a more manual owner review pattern than Formspree because email remains the practical center of operations.<br>- Works best when the business is intentionally keeping the workflow simple: receive, read, reply, then manually log or categorize if needed.<br>- System evolution capability is moderate: easy to wire in, but operational sophistication usually grows by adding external tools rather than by expanding the native vendor workflow. |
| Strengths | - Fastest path to go live operationally; a developer can usually connect the frontend with minimal platform setup and very little coordination overhead.<br>- Strong cost efficiency for an early-stage site because the free tier is generous relative to common low-volume contact traffic.<br>- Good fit for a single-owner business where the inbox is already the de facto operating queue and the team does not want to manage another dashboard daily.<br>- Low business friction: simple endpoint, low ceremony, and predictable behavior are useful when the primary goal is immediate stabilization rather than workflow redesign.<br>- Can cover the essential Phase 1 job well if the team treats it as an email-routing solution rather than a structured operations platform. |
| Limitations | - Review workflow is less mature than Formspree or FormInit because the business process tends to live in the owner's inbox instead of a strong queue-oriented dashboard.<br>- Hosted submission history exists, but it is not as naturally positioned to be the daily operating surface for multi-step review, tagging, or team collaboration.<br>- As submission volume or variety grows, manual reconciliation between inbox, submission history, and follow-up tracker becomes more likely.<br>- Less suitable when the business wants to separate response states, coordinate among multiple reviewers, or build strong intake analytics before Phase 2. |
| Operational Risks (Non-Security) | - Vendor lock-in is lower at the data-model level than inbox-centric tools with heavier workflow features, but operational lock-in can still happen if the owner's inbox becomes the only real queue.<br>- Pricing is predictable at low volume, but add-on features and plan upgrades can become a discussion once the team wants more recipients, richer integrations, or file-heavy workflows.<br>- Operational visibility gaps are the main risk: a delayed email or crowded inbox can make a valid submission effectively invisible to the business for too long.<br>- Dependency risk is concentrated in email operations; when the inbox is the workflow, any noise, filtering, or review lapse becomes a business-process issue rather than just a tool issue.<br>- Manual triage can become inconsistent because the platform does not naturally enforce structured handling states. |
| Best-Fit Scenarios | - Startup landing page that mainly needs quick contact capture and a low monthly cost ceiling.<br>- Portfolio site or personal consultancy site where one owner handles nearly all inquiries from email and only occasionally checks vendor history.<br>- Small campaign or brochure site that wants a low-lift Phase 1 path without committing to a richer vendor operating surface.<br>- Less ideal for a high-volume SaaS business, multi-reviewer operations team, or internal enterprise tool where queue structure, visibility, and explicit ownership states matter more than raw simplicity. |
| Scalability Behavior | - **Low traffic (0-50 submissions/day):** strong fit; low cost, low ceremony, and manageable inbox-based review for one owner or one small team.<br>- **Medium traffic (50-500/day):** technically still workable, but inbox overload becomes the central scaling problem; the team usually needs a separate tracker, automation, or internal process discipline to avoid losing responsiveness.<br>- **High traffic (500+/day):** the platform can still accept submissions, but business operations become fragile because human review, categorization, and reporting depend too heavily on inbox behavior and manual process overlays.<br>- Cost implication: the raw platform cost may still look attractive, but the hidden cost is increased manual handling and reduced operational visibility at higher volume. |
| Failure Behavior | - If the vendor is down, the site may still render normally while the submission path fails, leaving the owner dependent on status awareness and post-incident reconciliation.<br>- If notifications are delayed, the practical fallback is to inspect the hosted submission history directly, but that requires discipline because email remains the default operating habit.<br>- Submission loss scenarios are more likely to look like review failure than obvious system failure, such as an email landing late or being buried while the submission technically exists in vendor history.<br>- Fallback behavior is operationally weaker than dashboard-first tools because recovery depends on the owner consciously switching from inbox review to vendor-history review. |
| System Ownership | - Web3Forms owns the backend intake service, hosted submission storage window, and notification dispatch infrastructure.<br>- Skill-Wanderer owns the public form behavior, field design, review routine, and any manual or semi-manual downstream tracking after an inquiry is received.<br>- Data flow control is vendor-mediated at submission time, while practical business control remains with the owner's inbox and any side systems used for follow-up.<br>- Responsibility for missed business outcomes sits mostly with the internal team because the major operational failure mode is not just delivery failure, but weak review discipline in an email-first model.<br>- Dependency level is moderate: vendor dependency is present, but the deeper operational dependency is actually on internal inbox habits staying disciplined as volume grows. |

### 4.3 Getform / FormInit

#### Overview

Getform now resolves to FormInit, which positions itself as a headless form backend with a stronger operations console: centralized inbox, analytics, logs, workspace features, and richer notification options. It is operationally compelling, but its free tier is less favorable for this repo because the current `/contact` page has two separate intake flows.

#### 5W1H Table

| Dimension | Detail |
|----------|--------|
| What | A headless form backend with centralized submission management, analytics, logs, notifications, and team-oriented workspaces. |
| Why | It gives the business a more review-centric operating surface than a simple email-only form service. |
| Who | Engineering sets up the form endpoints and field mapping. The business owner or small ops team uses the inbox, logs, and analytics for daily review. |
| When | Best when the team wants a richer operator console than Web3Forms and is comfortable moving to a paid tier earlier than with Formspree. |
| Where | Capture remains in Nuxt. Submission operations move into the FormInit inbox, notifications, workspace, and export/API surfaces. |
| How | Create a FormInit workspace, define the forms, wire the Nuxt handlers to the form endpoints, and configure owner notifications plus any Slack or webhook handoff needed. |

#### Workflow (Visual Diagram)

##### High-Level Flow (Simple View)

```text
User (Client)
  -> Nuxt Frontend (Client)
  -> FormInit Endpoint (Vendor System)
  -> Notification Layer (Vendor System)
  -> Review Layer (Business Layer)
```

##### Detailed System Flow (Production View)

```text
User (Client)
  |
  v
Nuxt /contact Page (Client)
  |
  +--> Hire the Guild submission
  +--> Join the Guild submission
  |
  v
Submission Handler (Client)
  |
  +--> prepares structured payload
  |      +--> sender identity fields
  |      +--> inquiry / application fields
  |      +--> form_type and source tags
  |
  +--> drives UI state
  |      +--> loading
  |      +--> success
  |      +--> recoverable failure notice
  |
  v
FormInit Endpoint over HTTPS (Vendor System)
  |
  v
FormInit Intake Layer (Vendor System)
  |
  +--> built-in validations
  +--> vendor spam filtering
  +--> submission normalization
  +--> UTM / metadata capture where configured
  |
  +--> Routing Decision
  |      |
  |      +--> inbox / workspace queue
  |      +--> email notification
  |      +--> Slack / Discord / webhook
  |
  v
FormInit Storage / Inbox / Logs / Analytics (Vendor System)
  |
  +--> centralized submission store
  +--> logs and delivery history
  +--> analytics and export surface
  +--> workspace-style visibility for multiple reviewers
  |
  v
Business Review Layer (Business Layer)
  |
  +--> Who reviews
  |      +--> owner
  |      +--> optional recruiter / delivery lead
  |
  +--> Where review happens
  |      +--> FormInit inbox
  |      +--> notification channels
  |
  +--> How decisions are made
  |      +--> respond now
  |      +--> tag and defer
  |      +--> assign internally
  |
  +--> Failure handling branch
  |      +--> if notification is delayed -> review centralized inbox
  |      +--> if queue ownership is unclear -> use logs to reconcile recent submissions
  |
  v
Business Action Layer (Business Layer)
  |
  +--> reply email
  +--> CRM / sheet / internal tracker update
  +--> assign recruiting or project follow-up owner
  |
  v
Feedback Loop (Business Layer + Vendor System)
  |
  +--> tune form layout and routing tags
  +--> refine workspace ownership
  +--> use logs and analytics to inform Phase 2 requirements
```

##### Phase 1 Workflow (Vendor-based)

```text
User (Client)
  |
  v
Nuxt Frontend (Client)
  |
  v
FormInit Forms / Workspace (Vendor System)
  |
  +--> Central inbox
  +--> Notification layer
  |      +--> Email
  |      +--> Slack / webhook / Discord
  +--> Logs and analytics
  |
  v
Owner / Team Review Loop (Business Layer)
  |
  +--> review in shared inbox
  +--> tag or assign if needed
  +--> decide business action
  |
  v
Follow-up / handoff / status tracking (Business Layer)
```

##### Phase 2 Workflow (NestJS-based)

```text
User (Client)
  |
  v
Nuxt Frontend (Client)
  |
  v
Submission API / BFF (Internal System)
  |
  v
NestJS Intake Controller (Internal System)
  |
  +--> validation / classification / ownership rules
  |
  v
Storage + Queue Layer (Internal System)
  |
  +--> canonical submission records
  +--> worker queue for notifications and integrations
  |
  +--> Notification Layer
  |      +--> Email
  |      +--> Slack / webhook
  |      +--> CRM integration
  |
  +--> if notification delay occurs -> queue retry + internal review queue fallback
  |
  v
Internal Review Surface (Business Layer)
  |
  +--> owner and team review
  +--> assignment and categorization
  |
  v
Business Action Layer (Business Layer)
  |
  +--> follow-up
  +--> internal process launch
  +--> reporting loop
```

##### Transition Workflow

```text
User (Client)
  |
  v
Nuxt Frontend (Client)
  |
  +--> Current path: FormInit workspace flow (Vendor System)
  |      +--> inbox / logs / analytics remain primary
  |
  +--> Transition path: NestJS pilot flow (Internal System)
  |      +--> dual flow or selective traffic cutover
  |      +--> internal storage, queue, and review validation
  |
  v
Cutover Decision Point (Business Layer + Internal System)
  |
  +--> if team review works cleanly in internal system
  |      +--> move primary intake to NestJS
  |      +--> keep FormInit for rollback and historical export
  |
  +--> if process ownership is still unclear
         +--> keep FormInit primary
         +--> continue coexistence until review flow stabilizes

Temporary coexistence:
  FormInit = proven review console
  NestJS = emerging internal system of record

Fallback path:
  if transition creates notification or assignment gaps
    -> route production back to FormInit
    -> resume owner review from inbox and logs
```

#### Operational Characteristics Table

| Category | Detail |
|----------|--------|
| Operational Characteristics | - Operates as a more fully featured headless form backend with a stronger operational center of gravity in its inbox, logs, analytics, and workspace model.<br>- For Skill-Wanderer, it behaves more like a lightweight intake operations platform than a simple submission relay, which is useful if the owner wants better review visibility from day one.<br>- Daily usage pattern is queue-oriented rather than purely inbox-oriented: submissions are reviewed in a central console, with logs and analytics supporting reconciliation and follow-up discipline.<br>- Better suited than Web3Forms for multi-person review or business stakeholders who want operational visibility beyond basic email notifications.<br>- System evolution capability is strong for Phase 1 and early Phase 1.5, but still ultimately vendor-bound if the business later wants a truly owned lifecycle model in NestJS. |
| Strengths | - Strongest operator-facing surface among the evaluated vendors for centralized intake review, with inbox, logs, analytics, and workspace features all reinforcing daily operations.<br>- Better fit for teams that want explicit review visibility rather than relying on email as the only source of operational truth.<br>- Notification options and integration paths support a more mature business process, including shared review, webhooks, and communication handoffs.<br>- Logs and analytics add real business value by helping reconcile what was submitted, what was delivered, and what still needs action.<br>- Good choice when the business wants Phase 1 stabilization to feel closer to a managed queueing tool than to a simple form relay. |
| Limitations | - The free tier is structurally awkward for this repo because the current `/contact` page already has two distinct workflows, which means a paid plan may become necessary earlier than expected.<br>- Higher baseline cost than Web3Forms for comparable early-stage usage, especially if the business is still validating volume and does not yet need richer workspace behavior.<br>- Branding transition from Getform to FormInit can create procurement and documentation friction, especially for non-technical stakeholders comparing tools or searching for support references.<br>- Richer vendor tooling can make the team more comfortable staying in the vendor environment longer, which is useful short term but can increase migration friction later. |
| Operational Risks (Non-Security) | - Vendor lock-in risk is moderate to high because the operational habits formed around inbox, logs, workspace roles, and analytics can become embedded in daily review behavior.<br>- Pricing scaling can feel less predictable in practice because the team may need paid access not due to raw submission volume, but because form count, collaboration, or operator tooling becomes necessary early.<br>- Visibility is stronger than lighter competitors, but that can also create a false sense that the vendor dashboard is the final system of record rather than a Phase 1 operating surface.<br>- Dependency risk is meaningful: if the vendor's logs, workspace semantics, or plan packaging change, the business process may have to adapt around external constraints.<br>- Internal ambiguity can arise if some stakeholders refer to the product as Getform and others as FormInit, leading to process and support confusion during rollout. |
| Best-Fit Scenarios | - Startup or small business site where the owner wants a real submission console, not just email delivery, and is comfortable paying for better day-to-day visibility.<br>- Portfolio or consultancy site run by more than one person, where shared review and logs are valuable from the start.<br>- High-consideration services workflow where a missed inquiry has meaningful business cost and a centralized inbox is worth more than the cheapest possible setup.<br>- Less ideal for an extremely cost-sensitive landing page, a very small solo setup that only wants inbox delivery, or a high-volume SaaS / internal enterprise tool that will soon require a fully owned internal workflow platform. |
| Scalability Behavior | - **Low traffic (0-50 submissions/day):** very comfortable operationally; the inbox, logs, and analytics feel polished, but the value comes with a higher likelihood of moving to a paid tier sooner than simpler tools.<br>- **Medium traffic (50-500/day):** strong fit; this is where the richer operator tooling starts to pay off because multiple reviewers, queue visibility, and submission reconciliation become more important.<br>- **High traffic (500+/day):** vendor infrastructure may still handle intake volume, but strategic friction grows around cost, ownership, and the need for business-specific routing and status models; at this point the internal NestJS path becomes more compelling.<br>- Operational implication: FormInit scales better than email-first tools for review complexity, but not indefinitely for organizations that want full control over lifecycle and reporting semantics. |
| Failure Behavior | - If the vendor experiences downtime, the business loses not only delivery but also access to its primary shared review console, which can affect both intake visibility and ongoing reconciliation work.<br>- If notifications are delayed, the centralized inbox and logs provide a stronger fallback than email-first tools because reviewers can verify recent submissions directly inside the platform.<br>- Submission loss scenarios are less likely to remain ambiguous because logs and analytics help distinguish between acceptance, notification, and review issues, but recovery still depends on vendor availability.<br>- Fallback behavior is stronger than Web3Forms because the dashboard is a more complete operational surface, but weaker than an internal NestJS system because ultimate recovery control still belongs to the vendor. |
| System Ownership | - FormInit owns the intake runtime, hosted data flow, logs, analytics surfaces, workspace model, and notification plumbing.<br>- Skill-Wanderer owns the frontend form experience, field definitions, review responsibilities, and all downstream business actions after a submission becomes visible.<br>- Data flow is vendor-controlled during intake and short-term operations, though exports, APIs, and integrations create better escape paths than a purely inbox-bound tool.<br>- Responsibility for failures is shared: the vendor is responsible for system availability and platform behavior, while the business remains responsible for response discipline, queue ownership, and operational follow-through.<br>- Dependency level is moderate to high: the richer the team's use of inbox, logs, and workspaces, the more the vendor becomes an operational dependency rather than just a form endpoint provider. |

---

## 5. Comparative Decision Matrix (Detailed)

| Dimension | Formspree | Web3Forms | Getform / FormInit |
|----------|----------|-----------|---------|
| Time to Deploy | **Fast.** Same-day setup is realistic for two forms and one owner review flow. | **Very fast.** Probably the fastest initial go-live path. | **Fast.** Still quick, but slightly more admin setup than Web3Forms. |
| Ease of Setup | **High.** Clear dashboard-led setup and form-by-form configuration. | **Very high.** Access-key model is simple and easy to wire into Nuxt. | **High.** Straightforward, but more platform concepts to configure. |
| Dashboard UX | **Strong.** Good for owner review, search, export, and ongoing intake visibility. | **Basic.** Enough for submission history, but not the strongest review console. | **Strong.** Central inbox and logs are attractive for operational review. |
| Notification Flexibility | **High.** Email, autoresponders, multiple recipients, integrations, webhooks. | **Medium.** Strong email workflow, but deeper routing depends on paid features and external tools. | **High.** Email plus team-oriented notification paths such as Slack, Discord, and webhooks. |
| Workflow Capability | **High.** Best overall balance for Phase 1 queue visibility and owner usability. | **Medium.** Works well for email-first review, weaker for structured workflow. | **High.** Strong if the team wants a more explicit review console from the start. |
| Integration Options | **High.** Direct integrations plus API and webhook style extension paths. | **Medium.** Good endpoint-based integration, lighter operational ecosystem. | **High.** Headless model, SDK, REST API, webhooks, Zapier. |
| Scalability | **High for Phase 1.** Good headroom before internalization is needed. | **Medium.** Throughput is fine, but process sophistication becomes the limit sooner. | **High.** Handles small-team review growth well, though internal ownership may still win later. |
| Cost Predictability | **Medium to high.** Clear monthly tiers, but richer workflow features move the cost up. | **High.** Lowest-cost predictable option for a simple intake flow. | **Medium.** Clear plans, but paid usage likely starts earlier because of form-count needs. |
| Vendor Lock-in Risk | **Medium.** Strong dashboard value, but exports and integrations reduce exit friction. | **Medium to low.** Simpler model means fewer embedded workflow habits. | **Medium.** Central inbox and workspace habits can become sticky over time. |

---

## Execution Priority

| Priority | Timeline | Action |
|----------|--------|--------|
| P0 (Immediate) | This week | Replace Firebase submission with Formspree and establish owner review workflow |
| P1 (Short-term) | 2-4 weeks | Observe submission patterns, refine notification routing, improve review discipline |
| P2 (Next phase) | 1-2 months | Design and plan NestJS internal system based on real workflow data |

### Interpretation

- P0 is NOT optional - current system risks business impact
- P1 improves operational quality
- P2 enables long-term ownership and scalability

## Final Decision (Recommended)

We will:

1. Implement Formspree this week as Phase 1 stabilization
2. Use it as the primary intake system for all contact workflows (`Hire the Guild` and `Join the Guild`)
3. Establish a consistent owner review workflow using Formspree inbox and notifications
4. Begin collecting operational data to inform Phase 2 (NestJS internal system)

### Why This Decision

- Eliminates immediate risk of missed leads
- Provides a structured and visible intake workflow
- Requires minimal engineering effort and coordination
- Improves response speed and operational discipline
- Enables clean transition to an internal system later

### Decision Framing

This is not a tool choice - it is an operational decision:

- Phase 1 = restore business continuity
- Phase 2 = build long-term ownership

## Decision Confidence

| Factor | Confidence |
|-------|-----------|
| Implementation Feasibility | High |
| Time to Value | High |
| Business Impact | High |
| Risk Level | Low |
| Long-Term Flexibility | High |

### Summary

This decision is low-risk, high-impact, and reversible, making it the safest and most effective immediate action.

---

## 7. Phase 1 Execution Plan (CRITICAL)

Phase 1 should treat the current `/contact` page as **two separate intake workflows**, not one generic contact form:

- **Workflow A:** `Hire the Guild`
- **Workflow B:** `Join the Guild`

The recommended Phase 1 implementation is to create **two vendor forms/endpoints** so the owner can review project inquiries and guild applications separately from day one.

### Step-by-Step Implementation Plan

| Step | Action | Owner | Output |
|------|--------|-------|--------|
| 1 | Setup vendor account and workspace | Business owner + engineer | Active Formspree account, owner admin access, primary business email connected |
| 2 | Configure form endpoint | Engineer | Two forms created: `hire-the-guild` and `join-the-guild`, each with a dedicated Formspree endpoint or form ID |
| 3 | Integrate with Nuxt frontend | Engineer | `pages/contact.vue` updated to submit both forms to Formspree instead of Firebase while preserving loading, success, and error states |
| 4 | Configure notifications | Business owner + engineer | Primary notification email, backup notification destination, and distinct labels or subject lines for `Hire` vs `Join` |
| 5 | Test submission flow | Engineer | Verified test submissions for both tabs on `/contact`, visible in vendor inbox and received by notification channels |
| 6 | Validate review workflow | Business owner | Defined intake review routine, ownership of first response, and a clear method for marking submissions as handled |
| 7 | Go live | Engineer + business owner | Production form IDs enabled, smoke test passed, owner confirms live review workflow is usable |

### Timeline

| Phase | Duration | Outcome |
|------|--------|--------|
| Setup | 1 day | Vendor account, two forms, owner access, and notification targets configured |
| Integration | 1-2 days | Nuxt handlers switched from Firebase writes to vendor POST flow for both contact tabs |
| Testing | 1 day | End-to-end verification for both inquiry types, notification paths, and dashboard visibility |
| Go-live | Same week | Contact intake stabilized in production with owner-ready review workflow |

### Success Criteria

- Submissions reliably received
- Notifications delivered consistently
- Owner can review all submissions
- No manual gaps in workflow

Recommended operating interpretation of those criteria:

- Both `/contact` tabs produce visible submissions in the vendor dashboard within the same business workflow
- The owner can distinguish `Hire` from `Join` without opening engineering tools
- A missed email notification does not mean a missed submission because the dashboard remains the source of review truth

### Operational Checklist

- [ ] Email notification verified
- [ ] Dashboard access confirmed
- [ ] Test submissions logged
- [ ] Review process defined
- [ ] Backup notification channel configured
- [ ] Separate handling for `Hire the Guild` and `Join the Guild` confirmed

Recommended implementation notes for engineers:

- Keep the current `isSubmitting`, `isGuildSubmitting`, success messages, and error messages so the user experience does not change during Phase 1.
- Add vendor identifiers through Nuxt runtime configuration rather than hardcoding them into `pages/contact.vue`.
- Preserve the current field groupings so business review remains understandable:
  - `Hire the Guild`: `name`, `email`, `topic`, `message`, `valuesQuality`
  - `Join the Guild`: `name`, `email`, `skill`, `experience`, `portfolio`, `message`
- Add a lightweight `form_type` or separate endpoint naming convention so exports and downstream review remain unambiguous.

Recommended operating notes for the owner:

- Review the dashboard at least twice daily during the first live week.
- Keep one backup notification destination in addition to the primary email inbox.
- Use a simple handled-state rule from day one, for example: `New -> Reviewed -> Replied`.

---

## 8. Risks & Mitigation (Operational Only)

| Risk | Mitigation |
|------|-----------|
| Vendor downtime | Keep direct email contact visible on the contact page, define a rollback note for temporary manual intake, and review vendor status during any incident window. |
| Notification delay | Use both a primary notification email and one backup channel, and require dashboard review as part of the routine rather than relying on alerts alone. |
| Misconfiguration | Configure test forms first, run a two-form test matrix before cutover, and verify each field mapping against the existing `/contact` page. |
| Workflow confusion | Assign one named owner for initial review, define simple handled states, and separate project inquiries from guild applications from the first day of rollout. |

---

## 9. Transition Readiness for Phase 2

Phase 1 should not just stabilize intake. It should also produce the operating evidence needed to decide when a NestJS-based internal system becomes worthwhile.

What data should be observed:

- Submission volume by workflow: `Hire` vs `Join`
- Time from submission to first owner review
- Time from owner review to first reply
- Number of submissions that require manual copy/paste into email, spreadsheets, or other tools
- Common routing patterns, such as project inquiry vs partnership vs applicant review
- Frequency of export needs or reporting requests from the owner
- Monthly plan usage and whether the vendor tier still matches actual operating behavior

Signals that indicate readiness for a NestJS system:

- The owner needs a canonical internal database rather than a vendor inbox
- More than one person must collaborate in the review workflow regularly
- The team wants custom status models, internal notes, or richer queue states than the vendor provides
- Manual handoff into spreadsheets, CRM tools, or project systems becomes a daily burden
- Reporting questions become more specific than submission counts and email notifications
- Vendor cost or workflow constraints start shaping the business process more than the business itself

Recommended Phase 2 readiness rule:

Move to a NestJS-based intake system when the team can clearly describe the workflow it wants to own, not merely when it prefers the idea of owning it.
