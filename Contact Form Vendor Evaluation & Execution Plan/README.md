# Contact Form Vendor Evaluation & Execution Plan

---

## 1. Objective

Skill-Wanderer needs a vendor comparison because the current `/contact` page already runs two real intake workflows from the Nuxt frontend:

- `Hire the Guild` for project and partnership inquiries
- `Join the Guild` for guild applications

Today, those workflows are handled directly in `pages/contact.vue` with client-side writes to Firebase. That keeps the UI simple, but it does not give the business owner a strong review console, notification workflow, or low-friction operating model for day-to-day intake handling.

Phase 1 is not about building the final long-term intake platform. Its role is to stabilize submission handling quickly with a managed vendor so the owner can reliably receive, review, and act on inquiries without waiting for a NestJS backend program.

This document supports one immediate decision:

- Which third-party form vendor should Skill-Wanderer use for Phase 1 stabilization?
- How should that vendor be implemented in the current Nuxt codebase with minimal confusion for both the owner and engineers?

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

```text
User
  -> Nuxt /contact
  -> Formspree form endpoint
  -> Formspree Inbox
  -> Email / integrations
  -> Owner review and follow-up
```

#### Operational Characteristics Table

| Category | Detail |
|----------|--------|
| Strengths | Mature inbox experience; easy export; clear analytics surface; strong integration catalog; low engineering lift for the current Nuxt page. |
| Limitations | Costs rise if the team needs more submissions, more advanced automation, or richer team workflow; business process state still lives in a vendor dashboard unless exported elsewhere. |
| Operational Risks (Non-Security) | Owner may rely only on email and stop checking the inbox; plan limits can be reached if campaign activity spikes; follow-up state may drift if review happens partly in Formspree and partly in email threads. |
| Best-Fit Scenarios | One owner or a very small team; two clearly separated intake paths; a need for dashboard visibility now with minimal engineering effort. |
| Scalability Behavior | Scales well for low to moderate volume. Operational friction appears only when the team needs custom routing, richer internal states, or more advanced reporting. |
| Observability | Strong for Phase 1: inbox, search, submission archive, export, analytics, and status visibility through vendor surfaces. |

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

```text
User
  -> Nuxt /contact
  -> Web3Forms API
  -> Email delivery + submission history
  -> Owner inbox review
  -> Follow-up and manual tracking
```

#### Operational Characteristics Table

| Category | Detail |
|----------|--------|
| Strengths | Very fast deployment; low cost; simple endpoint model; easy fit for static or Nuxt-based forms; generous free submission volume. |
| Limitations | The workflow is more email-first than dashboard-first; lighter review tooling; less comfortable if intake grows beyond a single-owner inbox routine. |
| Operational Risks (Non-Security) | Inbox overload can become the real queue; review state may become manual and inconsistent; limited dashboard depth can make reporting and triage harder over time. |
| Best-Fit Scenarios | Cost-sensitive Phase 1 rollout; single reviewer; low to moderate traffic; simple response process with minimal internal coordination. |
| Scalability Behavior | Raw submission volume scales well for early-stage use, but operational complexity becomes the bottleneck before throughput does. |
| Observability | Good enough for a lightweight setup: submission history and exports exist, but the operating picture is still centered on email delivery rather than a rich queue. |

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

```text
User
  -> Nuxt /contact
  -> FormInit endpoint
  -> Central inbox + logs + analytics
  -> Email / Slack / webhook notification
  -> Owner or team review
```

#### Operational Characteristics Table

| Category | Detail |
|----------|--------|
| Strengths | Strong inbox model; logs and analytics are useful for business review; workspace support is better than lightweight competitors; good webhook and notification surface. |
| Limitations | Free tier supports only one form, which is awkward for the current two-flow contact page; pricing steps up sooner; current brand transition from Getform to FormInit may create owner confusion during evaluation or procurement. |
| Operational Risks (Non-Security) | The owner may need paid plan access sooner than expected; naming mismatch between Getform and FormInit can create ambiguity in internal documentation; team habits can become tied to vendor-specific inbox flows. |
| Best-Fit Scenarios | Small teams that want a proper review console, analytics, and logs from day one and are willing to pay for that structure. |
| Scalability Behavior | Handles low to moderate volume well, especially when multiple people need visibility. Long-term friction appears when a fully owned internal queue becomes more valuable than a vendor console. |
| Observability | Strong for Phase 1. Better than email-only tools because submission logs, analytics, and a centralized inbox are part of the product experience. |

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

## 6. Recommendation (Forced Choice)

### Recommended Vendor (Phase 1)

We recommend: **Formspree**

Reason:

- It provides the best owner-facing operating model for Skill-Wanderer's actual Phase 1 problem: reliable intake, clear notifications, and a reviewable inbox for two separate workflows.
- It maps cleanly to the current Nuxt implementation because the existing `handleSubmit()` and `handleGuildSubmit()` handlers can be swapped from Firebase writes to two Formspree endpoints with minimal UX disruption.
- It gives the best balance between immediate deployment speed and later Phase 2 transition readiness because exports, integrations, and a mature dashboard reduce the risk of getting stuck in an email-only process.

Practical interpretation:

- If the goal is **owner-ready stabilization with minimal confusion**, choose **Formspree**.
- If the goal is **lowest possible cost and fastest raw setup**, choose **Web3Forms**.
- If the goal is **richer inbox and logs for a small paid operational setup**, consider **Getform / FormInit** as the main alternative.

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
