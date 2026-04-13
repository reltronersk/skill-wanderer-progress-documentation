# SEO Audit Research — End-to-End Progress Log

> **Assignment:** SEO Audit Research Task for the LMS  
> **Repository:** `skill-wanderer/LMS-FE`  
> **Site:** `https://dojo.skill-wanderer.com` — Nuxt 4 SSR, Cloudflare Pages  
> **Engineering Branch:** `feature/seo-audit-research` → PR #81 → target: `dev`  
> **Date started:** 2026-04-13  
> **Status:** Phases 1–5 implemented and pushed. Phases 6–8 pending post-deploy.

> ⚠️ This file is gitignored (`docs/seo-progress-log.md`). It exists for local reference only and must never be committed or pushed.

---

## Table of Contents

1. [Assignment Statement](#1-assignment-statement)
2. [Stakeholder Alignment & Requirements](#2-stakeholder-alignment--requirements)
3. [Constraints & Risks](#3-constraints--risks)
4. [Project Scan — Scope & Findings](#4-project-scan--scope--findings)
5. [Git State Analysis & Rebase Decision](#5-git-state-analysis--rebase-decision)
6. [SEO Audit Research — Prioritized Report](#6-seo-audit-research--prioritized-report)
7. [Execution — Phase by Phase](#7-execution--phase-by-phase)
8. [PR #81 — Final State](#8-pr-81--final-state)
9. [Post-Deploy Phases (6–8)](#9-post-deploy-phases-68)
10. [Definition of Done Tracker](#10-definition-of-done-tracker)
11. [Decisions Log](#11-decisions-log)

---

## 1. Assignment Statement

**Owner request (verbatim summary):**

> Research and determine what should be done first in an SEO audit and why.
> Identify all major components. Analyze and decide order based on impact and importance.
> For each step: what it is, why it matters, what happens if skipped.
> Provide a prioritized list. Include tool references.
> Output: short report, clear priorities, logical reasoning.

**Interpretation:** This is both a research task AND an implementation task. Research determines order; implementation executes the highest-impact, lowest-effort fixes that can be done in code right now, without waiting for off-page or design work.

---

## 2. Stakeholder Alignment & Requirements

| Item | Decision |
|---|---|
| **Primary goal** | Improve organic search ranking → drive traffic to free LMS courses |
| **Target audience** | Tech learners searching for free courses on software testing, git, API, AI |
| **Platform facts** | Nuxt 4 (^4.1.3), Cloudflare Pages, `@nuxtjs/sitemap` v7, `@nuxtjs/robots` v5, `nuxt-schema-org` v5 |
| **Delivery standard** | Code-only fixes in this PR; off-page and manual-tool phases post-deploy |
| **Non-negotiable constraint** | Every change must be non-destructive. No refactor of course data model. No breaking existing behavior. |

---

## 3. Constraints & Risks

| Constraint | Applies To |
|---|---|
| `feat/rest-course-clean-final-v2` must NOT be rebased or touched | Branch hygiene — the REST course branch is 5 commits ahead of `dev` and conflict-free. Leave it. |
| `public/_robots.txt` is NOT the source of `/robots.txt` in production | `@nuxtjs/robots` v5 generates `/robots.txt` from `nuxt.config.ts`. `mergeWithRobotsTxtPath` looks for `public/robots.txt` (no underscore). Editing `_robots.txt` has zero production effect. |
| `/public/og-image.png` does not exist | Referenced as fallback OG image globally. Temporarily replaced with an existing asset. Design team must deliver the branded 1200×630 image. |
| `/public/logo.png` does not exist | Referenced in `schemaOrg.identity.logo` in `nuxt.config.ts`. Organization schema is incomplete until this asset is provided. |
| Course thumbnails are `.png`/`.jpg` — NOT WebP | `@nuxt/image` is configured with `provider: 'none'` — no WebP transform is active. Do not claim WebP is in use. |
| `app/data/core/course.ts` does not exist | The `Course` type is in `app/types/course.ts`. Course data lives in `app/data/courses/<slug>/index.ts`. |
| `getAllPublishedLessons()` does not exist | Use `getAllLessons(course).filter(isPublishedLesson)` from `~/types/course`. |
| REST course (`restful-api-mastery-greybox`) is not in `app/data/courses/index.ts` | It lives on `feat/rest-course-clean-final-v2`. Do not count it as "currently registered". |

| Risk | Mitigation Applied |
|---|---|
| Sitemap server handler crashes on bad import | Verified Nuxt server alias: `~/` → `../app/*` in `.nuxt/tsconfig.server.json`. Import pattern is safe. |
| Canonical conflicts with `og:url` | `<link rel="canonical">` and `<meta property="og:url">` are different HTML elements with independent meanings. No conflict. |
| Force-push overwrites someone else's work | Used `--force-with-lease` (not `--force`). It aborts if remote was updated by another person since last fetch. |

---

## 4. Project Scan — Scope & Findings

### 4.1 Directory Structure (SEO-relevant files)

```
nuxt.config.ts               ← SSR enabled, site.url, schemaOrg.identity, robots, sitemap config
app/
  composables/
    useSeo.ts                ← useSeo(), useCourseSeo(), useLessonSeo() — all SEO injection
  pages/
    index.vue / about.vue / courses/index.vue / paths.vue / search.vue / lyra.vue
    courses/[slug]/index.vue ← useCourseSeo()
    courses/[slug]/lessons/[lessonSlug].vue ← useLessonSeo()
  data/
    courses/index.ts         ← Course registry (4 courses registered)
  types/
    course.ts                ← Course, Module, Lesson types + getAllLessons(), isPublishedLesson(), isPublishedCourse()
server/
  routes/__sitemap__/
    urls.get.ts              ← Dynamic sitemap source (previously: 1 course hardcoded)
public/
  _robots.txt                ← Served at /_robots.txt (NOT /robots.txt — see constraint above)
  _headers                   ← CSP headers for /courses/* (Cloudflare Pages)
  images/courses/            ← ai-first.jpg, git.png, html.png, manual-software-testing.png
```

### 4.2 Findings — Pre-Implementation Gaps

| # | Gap | Severity | File |
|---|---|---|---|
| 1 | Sitemap only includes 1 of 4 registered courses (manual testing only). New courses not auto-added. | 🔴 Critical | `server/routes/__sitemap__/urls.get.ts` |
| 2 | No `<link rel="canonical">` anywhere on the site. `?utm_source=`, `?ref=` variants create duplicate content. | 🔴 Critical | `app/composables/useSeo.ts` |
| 3 | `/public/og-image.png` referenced globally as OG fallback but file does not exist → 404 on all non-course social shares | 🟠 High | `nuxt.config.ts`, `useSeo.ts` |
| 4 | `robots.txt` has no `Disallow` rules and no `Sitemap:` pointer | 🟠 High | `nuxt.config.ts` (robots section) |
| 5 | Text/assignment lessons emit no structured data except `WebPage` — no `LearningResource` or `Article` fallback | 🟠 High | `app/composables/useSeo.ts` |
| 6 | Organization schema references `/logo.png` which does not exist in `/public/` | 🟡 Medium | `nuxt.config.ts` |
| 7 | Sitemap `lastmod` is hardcoded to a single date for all URLs | 🟡 Medium | `server/routes/__sitemap__/urls.get.ts` |
| 8 | Static pages (`/`, `/courses`, `/paths`, `/about`) not in sitemap | 🟡 Medium | `server/routes/__sitemap__/urls.get.ts` |
| 9 | Title/description length not validated (risk of truncation > 60/160 chars) | 🟡 Medium | Per-page audit needed |
| 10 | Heading hierarchy (`<h1>` uniqueness) not audited across Vue templates | 🟡 Medium | Per-page audit needed |
| 11 | Image `alt` text consistency not audited in components | 🟡 Medium | `CourseCard.vue`, `TheHero.vue` |

---

## 5. Git State Analysis & Rebase Decision

### 5.1 State snapshot (2026-04-13, post-fetch)

```
origin/main   8d702e8  ← Merge PR #80 (dev→main). No new source code beyond dev.
origin/dev    1ea156d  ← Latest integration branch (Merge PR #77)
local  dev    1ea156d  ← In sync with origin/dev ✅
feat/rest-clean-final-v2  6ada5f4  ← 5 commits ahead of dev, 0 behind ✅
feature/seo-audit-research  07c05d5  ← 4 commits ahead of dev (this PR)
```

### 5.2 Rebase decision matrix

| Branch | Rebase needed? | Reasoning |
|---|---|---|
| `feat/rest-course-clean-final-v2` | **No** | Built on `1ea156d` (latest dev). `origin/main`'s only delta is a merge commit — no new source changes. Rebasing adds noise with zero benefit. |
| `feature/seo-audit-research` | **No** | Created fresh from `dev` (`1ea156d`). Branch is clean. |

**Senior Rule applied:** "Every default must be non-destructive." REST branch left completely untouched.

### 5.3 Merge strategy for REST course (when ready)

Open PR targeting `dev`. CI confirms no conflicts. No force or rebase needed.

---

## 6. SEO Audit Research — Prioritized Report

### Pillar Order & Reasoning

```
PRIORITY 1: Technical SEO
  → Foundation. Broken crawlability nullifies all other work.
  
PRIORITY 2: On-Page SEO
  → Once crawlers reach pages, these signals determine relevance and ranking.
  
PRIORITY 3: Structured Data
  → Already partially implemented. Completing gaps unlocks Google rich results (course cards).
  
PRIORITY 4: Open Graph / Social
  → Doesn't affect Google ranking directly, but broken OG images kill social sharing trust.
  
PRIORITY 5: Sitemap + robots.txt
  → Already 80% done. Complete to maximize crawl efficiency.
  
PRIORITY 6: Content SEO
  → Requires keyword research, planning, writing. Higher effort, longer timeline.
  
PRIORITY 7: Off-Page SEO
  → No code changes possible. Requires sustained outreach. Lowest engineering priority.
```

### Why This Order (Reasoning Chain)

1. **Technical first** — A page that cannot be crawled or rendered earns zero ranking, regardless of how good its content or meta tags are. Fix the foundation before painting the walls.

2. **On-Page second** — Title tags, canonical URLs, and meta descriptions are the primary signals that tell crawlers *what* a page is about and *which URL* is authoritative. High impact, low engineering effort.

3. **Structured data third** — `nuxt-schema-org` is already installed. The `Course`, `BreadcrumbList`, and `VideoObject` schemas are partially active. Completing gaps (canonical, LearningResource fallback) directly unlocks Google rich results (course cards in SERP) which directly increase CTR without needing to climb rankings.

4. **Social tags fourth** — Missing `og:image` is a broken trust signal, not just a traffic issue. Any page shared on LinkedIn, Slack, or Twitter/X shows a broken image. Low effort, high visibility fix.

5. **Sitemap + robots.txt fifth** — These are structural signals that are 80% implemented already. Completing them accelerates discovery and ensures crawl budget is not wasted on non-indexable pages.

6. **Content sixth** — Keyword research and content optimization require research, planning, and editorial work. The technical foundation must be solid first.

7. **Off-page last** — Backlinks and domain authority cannot be earned through code changes. Requires sustained external effort.

### Full Prioritized Action Table

| # | Category | Action | Impact | Effort | Status |
|---|---|---|---|---|---|
| 1 | Technical | Add all courses + lessons to sitemap dynamically | 🔴 Critical | Low | ✅ Done (Phase 1) |
| 2 | On-Page | Add canonical `<link>` tag site-wide | 🔴 Critical | Low | ✅ Done (Phase 2) |
| 3 | Social | Fix broken og:image fallback | 🟠 High | Low | ✅ Done (Phase 3, interim fix) |
| 4 | Crawl | Configure disallow + sitemap in robots.txt | 🟠 High | Low | ✅ Done (Phase 4) |
| 5 | Schema | LearningResource fallback for videoless lessons | 🟠 High | Low | ✅ Done (Phase 5) |
| 6 | Schema | Fix missing `/public/logo.png` for Organization schema | 🟠 High | Low | ❌ Design asset needed |
| 7 | Schema | Add `sameAs` social profiles to Organization | 🟡 Medium | Low | ❌ Social URLs needed |
| 8 | Technical | Lighthouse CWV baseline | 🟡 Medium | Low | ⏳ Post-deploy (Phase 6) |
| 9 | On-Page | Audit title/description length | 🟡 Medium | Medium | ⏳ Post-deploy (Phase 7) |
| 10 | On-Page | Audit H1 uniqueness per page | 🟡 Medium | Medium | ⏳ Post-deploy (Phase 7) |
| 11 | On-Page | Audit `<img>` alt text in components | 🟡 Medium | Low | ⏳ Post-deploy (Phase 7) |
| 12 | Sitemap | Replace og:image fallback with branded 1200×630 PNG | 🟡 Medium | Low | ❌ Design asset needed |
| 13 | Content | Keyword research per course | 🟡 Medium | High | ❌ Not started |
| 14 | Content | Internal linking (next lesson, related courses) | 🟡 Medium | Medium | Partial |
| 15 | Off-Page | Submit sitemap to Google Search Console | 🔵 Setup | Very Low | ⏳ Phase 8 |
| 16 | Off-Page | Backlink outreach strategy | 🔵 Long-term | Very High | ❌ Not started |

---

## 7. Execution — Phase by Phase

### Phase 0 — Branch Setup (Completed)

```bash
git checkout dev
git checkout -b feature/seo-audit-research
# Branch created clean from dev @ 1ea156d
```

---

### Phase 1 — Sitemap Completion ✅

**File changed:** `server/routes/__sitemap__/urls.get.ts`

**Problem before:**
- Single hardcoded course (`manual-software-testing-black-box-techniques`)
- 13 hardcoded lesson slugs
- Static `lastmod: '2026-01-08'` for all URLs
- 3 of 4 registered courses invisible to Google

**Solution implemented:**
```typescript
import allCourses from '~/data/courses'
import { getAllLessons, isPublishedLesson, isPublishedCourse } from '~/types/course'

export default defineSitemapEventHandler(() => {
  const urls = []
  
  // Static pages
  for (const page of [
    { loc: '/', priority: 1.0 },
    { loc: '/courses', priority: 0.9 },
    { loc: '/paths', priority: 0.7 },
    { loc: '/about', priority: 0.6 },
  ]) {
    urls.push(asSitemapUrl({ ...page, changefreq: 'weekly' }))
  }
  
  // All published courses + lessons (dynamic)
  for (const course of allCourses) {
    if (!isPublishedCourse(course)) continue
    urls.push(asSitemapUrl({ loc: `/courses/${course.slug}`, lastmod: course.updatedAt, ... }))
    for (const lesson of getAllLessons(course)) {
      if (!isPublishedLesson(lesson)) continue
      urls.push(asSitemapUrl({ loc: `/courses/${course.slug}/lessons/${lesson.slug}`, lastmod: lesson.updatedAt || course.updatedAt, ... }))
    }
  }
  return urls
})
```

**Key decisions:**
- `isPublishedCourse()` / `isPublishedLesson()` guards ensure draft content is never in the sitemap
- `lastmod` derived from actual `updatedAt` field — no hardcoded date
- New courses registered in `app/data/courses/index.ts` are automatically included with zero manual effort

**Import path confirmed safe:** Nuxt server alias `~/` → `../app/*` verified in `.nuxt/tsconfig.server.json`

**Commit:** `8aa4269 feat(sitemap): dynamically generate URLs for all published courses and lessons`

---

### Phase 2 — Canonical Tag ✅

**File changed:** `app/composables/useSeo.ts`

**Problem before:**
- No `<link rel="canonical">` on any page
- `?utm_source=`, `?ref=` query parameters create distinct URLs that Google treats as duplicates
- Trailing slash variants (`/courses/x` vs `/courses/x/`) split ranking signals

**Solution implemented:**
```typescript
export function useSeo(options: SeoOptions) {
  const config = useRuntimeConfig()
  const requestUrl = useRequestURL()
  const canonical = options.url || `${requestUrl.origin}${requestUrl.pathname}`
  // ↑ options.url = explicit canonical (used on course/lesson pages)
  // ↑ requestUrl.pathname = auto-derived, strips ?query params by design

  useHead({
    title: options.title,
    link: [{ rel: 'canonical', href: canonical }],  // ← injected here
    meta: [ ... ],
  })
}
```

**Key decisions:**
- `options.url` takes priority — course/lesson pages pass their exact canonical URL already
- `useRequestURL().pathname` stripping query params is the correct SSR-safe Nuxt 4 approach
- `<link rel="canonical">` and `<meta property="og:url">` are separate elements — no conflict

**Commit:** `3ee877b feat(seo): add canonical link tag and LearningResource fallback schema`

---

### Phase 3 — OG Image Fallback Fix ✅

**File changed:** `nuxt.config.ts`

**Problem before:**
```typescript
// nuxt.config.ts:
{ property: 'og:image', content: '/og-image.png' }  // ← FILE DOES NOT EXIST
// useSeo.ts:
image: course.thumbnail || '/og-image.png'           // ← FILE DOES NOT EXIST
```
Every non-course page (home, about, courses index, paths) shows a broken image on any social share.

**Solution implemented:**
- Replaced `/og-image.png` with `/images/courses/manual-software-testing.png` (confirmed to exist in `/public/`)
- TODO comment left in code marking where branded og-image.png should be dropped

**Outstanding action (design team):** Produce a branded 1200×630 PNG/WEBP at `public/og-image.png`. Once delivered, update both references.

**Commit:** `84bdbf8 fix(seo): replace non-existent /og-image.png fallback with existing course image`

---

### Phase 4 — robots.txt Hardening ✅

**File changed:** `nuxt.config.ts` (robots section only)

**Critical finding during re-sanitization (Copilot review caught this):**
The original Phase 4 implementation edited `public/_robots.txt`. This is wrong.

**Source of truth for `/robots.txt` in this project:**

| File | What it serves | Is it `/robots.txt`? |
|---|---|---|
| `public/_robots.txt` | `/_robots.txt` (underscore prefix) | ❌ NO |
| `nuxt.config.ts` robots config | `/robots.txt` (generated dynamically by `@nuxtjs/robots` v5) | ✅ YES |
| `public/robots.txt` (no underscore) | Would be merged via `mergeWithRobotsTxtPath: true` | Not present in repo |

**Original Phase 4 (wrong):**
```
# public/_robots.txt (DEAD FILE for /robots.txt purposes)
User-Agent: *
Disallow: /auth/
Disallow: /search
Allow: /
Sitemap: https://dojo.skill-wanderer.com/sitemap.xml
```

**Corrected Phase 4 (implemented):**
```typescript
// nuxt.config.ts
robots: {
  disallow: ['/auth/', '/search'],
  sitemap: 'https://dojo.skill-wanderer.com/sitemap.xml',
},
```
- `public/_robots.txt` was fully reverted to its original state: `User-Agent: *\nDisallow:`
- `Allow: /` is the module default — no need to declare it explicitly

**Why `/auth/` and `/search` must be disallowed:**
- `/auth/callback` — OAuth redirect handler. No content. Indexing it leaks redirect flow details.
- `/search` — Dynamic thin-content page. No static value to index. `sitemap.exclude` already drops both from XML; robots.txt enforces the same at crawl level.

**Commit:** `07c05d5 feat(robots): configure disallow and sitemap via @nuxtjs/robots module`
*(amended after re-sanitization — previous commit `0536643` was incorrect)*

---

### Phase 5 — Structured Data Fallback ✅

**File changed:** `app/composables/useSeo.ts` (bundled into Phase 2 commit)

**Problem before:**
`useLessonSeo()` only emitted a `VideoObject` schema if a YouTube embed URL was detected. Text-only lessons (type: `'lesson'` or `'assignment'`) emitted only a `WebPage` schema — no `LearningResource` or `Article`. Google's rich results for educational content require explicit schema types.

**Solution implemented:**
```typescript
// After VideoObject block:
if (!schemas.length) {
  schemas.push({
    '@type': 'LearningResource',
    'name': lesson.title,
    'description': lesson.description || `Lesson: ${lesson.title} from ${lesson.courseTitle}`,
    'learningResourceType': 'lesson',
    'url': lessonUrl,
    'isAccessibleForFree': true,
  })
}

useSchemaOrg(schemas)  // always called (was: conditional)
```

**Key decisions:**
- `!schemas.length` guard — only fires when VideoObject was not produced
- `isAccessibleForFree: true` reinforces that this is a free LMS
- `useSchemaOrg(schemas)` is now unconditional — every lesson page always emits at least one schema

---

### Re-Sanitization (Iteration 2)

After the initial implementation and force-push, a Copilot automated review on PR #81 identified 10 factual errors across the documents. All were addressed:

**Code correction (1):**
- Phase 4 robots.txt fix was in the wrong file (`public/_robots.txt`). Corrected to `nuxt.config.ts`.

**Documentation corrections (9, in untracked docs):**
| Document | Issue | Correction |
|---|---|---|
| Both | "Nuxt 3" stated | Changed to Nuxt 4 (`^4.1.3` confirmed in `package.json`) |
| Research | "WebP thumbnails ✅" stated | Corrected: thumbnails are `.png`/`.jpg`; WebP is a future optimization |
| Research | "1 of 5 courses missing" stated | Corrected: 1 of 4 registered; REST is planned/not yet merged |
| Research | Organization logo `✅` | Corrected: `/logo.png` does not exist in `/public/` — schema is incomplete |
| Research | `og:image` section: "WebP files" | Corrected to `.png`/`.jpg` |
| Research | robots.txt: "Current state: Allow: /" confusing | Clarified: `@nuxtjs/robots` is the real source; `_robots.txt` is dead for `/robots.txt` |
| Blueprint | "1 of 5" | Corrected to "1 of 4 currently registered" |
| Blueprint | Referenced `app/data/core/course.ts` | Corrected to `app/types/course.ts` (actual file) |
| Blueprint | `import { courses }` from index | Corrected to `import courses` (default export) |
| Blueprint | `getAllPublishedLessons()` helper | Corrected to `getAllLessons(course).filter(isPublishedLesson)` |
| Blueprint | Phase 4 file: `public/_robots.txt` | Corrected to `nuxt.config.ts` with source-of-truth explanation |

---

## 8. PR #81 — Final State

**URL:** https://github.com/skill-wanderer/LMS-FE/pull/81  
**Base:** `dev`  
**Head:** `feature/seo-audit-research`

### Commit History (4 commits, oldest first)

```
8aa4269  feat(sitemap): dynamically generate URLs for all published courses and lessons
3ee877b  feat(seo): add canonical link tag and LearningResource fallback schema
84bdbf8  fix(seo): replace non-existent /og-image.png fallback with existing course image
07c05d5  feat(robots): configure disallow and sitemap via @nuxtjs/robots module
```

### Diff Summary

```
app/composables/useSeo.ts             | 19 +++++++--
nuxt.config.ts                        |  5 +++-
server/routes/__sitemap__/urls.get.ts | 74 ++++++++++++++++-----------------
3 files changed, 57 insertions(+), 41 deletions(-)
```

**No documentation files in the PR.** Docs (`seo-audit-research.md`, `seo-execution-blueprint.md`, `seo-progress-log.md`) are gitignored.

### Branch Hygiene Checklist

- [x] Branched from `dev` (`1ea156d`) — no stale base
- [x] `feat/rest-course-clean-final-v2` untouched — no rebase, no interference
- [x] No documentation files committed
- [x] No unrelated files changed
- [x] Each commit is atomic and scoped to one phase
- [x] `--force-with-lease` used (not bare `--force`) for safe force-push
- [x] Zero errors reported by TypeScript/ESLint at time of commit
- [x] Docs gitignored via `.gitignore` local entry

---

## 9. Post-Deploy Phases (6–8)

These phases require the PR to be merged and deployed to production first.

### Phase 6 — Lighthouse CWV Baseline

**When:** Immediately after `feature/seo-audit-research` is merged and deployed.

**Command:**
```bash
npx lighthouse https://dojo.skill-wanderer.com --output=json --output-path=./docs/lighthouse-baseline.json
npx lighthouse https://dojo.skill-wanderer.com/courses/manual-software-testing-black-box-techniques --output=json --output-path=./docs/lighthouse-course.json
```

**What to record:** LCP, INP, CLS scores for desktop and mobile. Store results locally (gitignored).

**Success target:** Performance score ≥ 90 on desktop.

---

### Phase 7 — On-Page Audit (Screaming Frog or Ahrefs)

**When:** After Phase 6 baseline is established.

**Checklist per page:**
- [ ] Title length: 30–60 characters; primary keyword within first 60 chars
- [ ] Meta description: 120–160 characters; includes a call to action
- [ ] Exactly 1 `<h1>` per page, matching title intent
- [ ] No missing `alt` text on any `<img>` (especially `CourseCard.vue`, `TheHero.vue`)
- [ ] Canonical URL matches intended clean URL (no query params, no trailing slash issues)

**Tool:** Screaming Frog SEO Spider — crawl `https://dojo.skill-wanderer.com` and export CSV reports.

---

### Phase 8 — Google Search Console Submission

**Pre-condition:** Phases 1–5 must be live on production.

**Steps:**
1. Verify property ownership at `dojo.skill-wanderer.com` in GSC
2. Submit `https://dojo.skill-wanderer.com/sitemap.xml`
3. Monitor **Index Coverage** report — all course + lesson URLs should appear within 7–14 days
4. Monitor **Core Web Vitals** report — flag any pages with CWV failures
5. Monitor **Search Performance** → establish query volume baseline

---

## 10. Definition of Done Tracker

| Requirement | Status | Notes |
|---|---|---|
| All 4 registered courses visible in `/sitemap.xml` | ✅ Done | Verified by code review |
| All published lessons per course in `/sitemap.xml` | ✅ Done | `isPublishedLesson()` guard applied |
| Static pages (`/`, `/courses`, `/paths`, `/about`) in sitemap | ✅ Done | Phase 1 |
| `<link rel="canonical">` present on every rendered page | ✅ Done | Phase 2 — `useSeo()` composable |
| `og:image` fallback resolves with HTTP 200 | ✅ Done | Phase 3 — uses existing `.png` asset (interim) |
| Branded `og:image.png` (1200×630) | ❌ Pending | Design team deliverable |
| `robots.txt` disallows `/auth/` and `/search` | ✅ Done | Phase 4 — `nuxt.config.ts` |
| `robots.txt` declares `Sitemap:` URL | ✅ Done | Phase 4 |
| `LearningResource` schema on all lesson types | ✅ Done | Phase 5 |
| `VideoObject` schema on video lessons | ✅ Already existed | Pre-existing implementation |
| Organization `logo.png` asset in `/public/` | ❌ Pending | Design team deliverable |
| Google Rich Results Test passes on 2+ course pages | ⏳ Post-deploy | Phase 6–7 |
| Lighthouse Performance ≥ 90 (desktop) | ⏳ Post-deploy | Phase 6 |
| Sitemap submitted to Google Search Console | ⏳ Post-deploy | Phase 8 |

---

## 11. Decisions Log

| Date | Decision | Reasoning |
|---|---|---|
| 2026-04-13 | Branch from `dev`, not `main` | `dev` is the integration branch. PRs target `dev`. |
| 2026-04-13 | Do NOT rebase `feat/rest-course-clean-final-v2` | Branch is ahead of dev, zero behind. No source change on main to pull in. Senior Rule: non-destructive default. |
| 2026-04-13 | Use `--force-with-lease` not `--force` | Prevents overwriting concurrent remote commits by other contributors. |
| 2026-04-13 | Keep docs untracked (gitignored) | SEO research docs are internal tooling artifacts, not source code. They would pollute the PR diff and make it harder to review actual code changes. |
| 2026-04-13 | Phase 4 corrected: edit `nuxt.config.ts`, not `_robots.txt` | `@nuxtjs/robots` v5 is the source of truth for `/robots.txt`. `_robots.txt` is dead for this purpose. Editing the wrong file would mean zero production effect. |
| 2026-04-13 | Interim og:image fix: use existing `.png` not a placeholder | Non-destructive interim state is better than a missing file 404. A TODO comment marks the permanent replacement point. |
| 2026-04-13 | Canonical uses `useRequestURL().pathname` (not `useRoute().fullPath`) | `pathname` excludes query string and hash. `fullPath` includes them. Canonical must strip query params. |
| 2026-04-13 | `useSchemaOrg(schemas)` made unconditional in `useLessonSeo()` | Previously `if (schemas.length)` meant some lessons emitted no schema at all. `LearningResource` fallback ensures all lessons are covered. |
