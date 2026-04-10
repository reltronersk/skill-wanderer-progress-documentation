# 📘 Phase 6 — Pull Request Sanitization & Clean Branch Strategy

## 🎯 Objective

Transform a **noisy, over-scoped pull request** into a **clean, minimal, and reviewable PR** that strictly aligns with:

* Intended feature scope
* Owner expectations
* Deterministic engineering practices

---

# 🧠 Initial Problem State

The original PR:

```txt
feat(course): add RESTful API Mastery and stabilize lesson system (#78)
```

## ❌ Issues Identified

### 1. Over-scoped PR

* ~54 files changed
* +2400 lines
* Mixed concerns:

  * Course system refactor ✅
  * UI changes ❌
  * Docs ❌
  * Navigation logic ❌

---

### 2. Dirty Branch History

Branch:

```bash
feat/lesson-navigation-hardened
```

Contained:

* Previous unrelated commits
* Merge commits from `dev`
* Experimental and transitional work

---

### 3. Cherry-pick Failure

```bash
git cherry-pick 5f19d77
```

Result:

```bash
The previous cherry-pick is now empty
```

### Root Cause:

* Commit changes already existed in branch history
* Branch was not clean → cherry-pick unusable

---

# ⚠️ Engineering Insight

> ❗ Pull Requests represent **reviewable change units**, NOT development history.

---

# 🧭 Strategy Decision

## ❌ Rejected Approaches

* Force push to existing branch
* Manually removing files from PR
* Continuing with dirty branch

---

## ✅ Chosen Approach

> 🔥 **Rebuild a clean branch from `dev` using selective file restoration**

---

# 🚀 Execution Steps (Deterministic)

---

## 🔴 Step 1 — Stash All Changes

```bash
git stash -u
```

### Purpose:

* Save all local changes (including untracked files)
* Ensure a clean working directory

---

## 🔵 Step 2 — Reset to Clean `dev`

```bash
git checkout dev
git pull origin dev
```

### Result:

* Local environment matches remote `dev`
* No residual state

---

## 🟢 Step 3 — Create Clean Branch

```bash
git checkout -b feat/rest-course-clean-final-v2
```

---

## 🟡 Step 4 — Inspect Stash

```bash
git stash show -p
```

### Purpose:

* Understand what changes exist
* Avoid blind restore

---

## 🟣 Step 5 — Selective Restore (CRITICAL STEP)

```bash
git checkout stash -- app/data/core
git checkout stash -- app/data/courses/restful-api-mastery-greybox
git checkout stash -- app/data/courses/index.ts
```

---

### ✅ Included Scope

```txt
app/data/core/
app/data/courses/restful-api-mastery-greybox/
app/data/courses/index.ts
```

---

### ❌ Excluded Scope

```txt
docs/
structure.txt
UI components
composables
config files
```

---

## 🔴 Step 6 — Remove Unwanted Files

```bash
git rm --cached app/data/courses/restful-api-mastery-greybox/structure.txt
```

---

## 🔍 Step 7 — Validate Working Tree

```bash
git status
```

### Expected State:

* Only relevant files staged
* No docs or unrelated files

---

## 🟢 Step 8 — Stage Files

```bash
git add app/data/core
git add app/data/courses/restful-api-mastery-greybox
git add app/data/courses/index.ts
git add app/data/authors.ts
```

---

## 🔵 Step 9 — Commit

```bash
git commit -m "feat(course): modularize RESTful API course and introduce core lesson engine"
```

---

## 🚀 Step 10 — Push Branch

```bash
git push origin feat/rest-course-clean-final-v2
```

---

## 🔗 Step 11 — Create Pull Request

GitHub auto-generated link:

```txt
https://github.com/skill-wanderer/LMS-FE/pull/new/feat/rest-course-clean-final-v2
```

---

## 🧾 Step 12 — PR Metadata

### Title

```txt
feat(course): modularize RESTful API course and introduce core lesson engine
```

---

### Description

```md
## Summary

This PR introduces a modular and scalable course architecture and applies it to the RESTful API Mastery course.

## Key Changes

- Extracted lessons into individual files (module → lesson structure)
- Introduced `app/data/core` as a reusable course engine:
  - `createLesson`
  - `createCourse`
- Centralized lesson metadata and removed duplication
- Enforced single source of truth for lesson status via course layer
- Removed legacy lesson shell implementation

## Result

- Deterministic course structure
- Scalable architecture for future courses
- Improved maintainability and readability

## Scope

- RESTful API course only
- No UI behavior changes
```

---

## ❌ Step 13 — Close Old PR (#78)

Comment:

```md
Closing this PR as it contains unrelated changes.

A new clean PR has been opened with only the intended scope:
- REST course modularization
- core lesson engine

Please review the new PR instead.
```

---

# 🧠 Final System State

## Before

```txt
Messy Branch → Mixed PR → Hard to review
```

---

## After

```txt
Clean Branch → Focused PR → Deterministic Review
```

---

# 🔒 Engineering Guarantees

### ✅ Determinism

* Only intended files included

### ✅ Isolation

* No unrelated changes

### ✅ Reproducibility

* Process can be repeated reliably

### ✅ Reviewability

* Clear diff
* Logical grouping

---

# 🧠 Key Engineering Insights

## 1. PR ≠ Work History

> A Pull Request is a **review unit**, not a timeline of development.

---

## 2. Clean Branch > Fixing Dirty Branch

> Rebuilding is often faster and safer than patching.

---

## 3. Selective Restore > Cherry-pick (in messy history)

| Scenario      | Tool                |
| ------------- | ------------------- |
| Clean commit  | cherry-pick         |
| Dirty history | selective restore ✅ |

---

## 4. Control the Diff, Not Just the Code

> Reviewers evaluate **diff clarity**, not just correctness.

---

# 🏁 Final Outcome

## ✅ Achieved

* Clean branch with single responsibility
* Focused PR aligned with feature scope
* Scalable architecture introduced (`core/`)
* No unintended side effects

---

