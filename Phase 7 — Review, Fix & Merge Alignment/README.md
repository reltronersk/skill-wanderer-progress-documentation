# 📘 Phase 7 — Review, Fix & Merge Alignment

## 🎯 Objective

Transform a **review-blocked Pull Request** into a **safe, deterministic, and mergeable PR** by:

* Respecting domain data ownership
* Eliminating destructive defaults
* Aligning system behavior with existing data
* Ensuring PR cleanliness and reviewability

---

# 🧠 Initial State (Before Phase 7)

The PR was technically correct in structure but **rejected due to subtle design flaws**.

---

## ❌ Issues Identified

### 1. Destructive Status Override

```ts
status: openLessons.has(lesson.slug)
  ? 'published'
  : 'draft'
```

**Problem:**

* Overwrites existing lesson status
* Ignores course-level status
* Causes hidden lessons (unexpected 404)

---

### 2. Hardcoded Metadata in System Layer

```ts
createdAt: '2026-01-01',
updatedAt: '2026-01-01',
```

**Problem:**

* Overrides real domain data
* Breaks SEO / last updated semantics
* Prevents flexibility

---

### 3. PR Noise

* `structure.txt` accidentally included
* Formatting inconsistencies
* Unused imports

---

## 🧠 Root Cause

> ❗ The system layer was **overriding domain data instead of respecting it**

---

# 🧭 Phase 7 Strategy

## Core Principle

```txt
System must not guess or override domain data.
System must respect and extend it.
```

---

# 🚀 Execution Steps (Deterministic)

---

# 🔴 STEP 1 — Fix Status Logic (Critical)

## File: `app/data/core/course.ts`

---

### ❌ Before

```ts
status: openLessons.has(lesson.slug)
  ? 'published'
  : 'draft'
```

---

### ✅ After

```ts
status: openLessons.has(lesson.slug)
  ? ('published' as const)
  : (lesson.status ?? course.status ?? 'draft')
```

---

## ✅ Result

| Case                 | Behavior           |
| -------------------- | ------------------ |
| Explicit open lesson | published          |
| Lesson has status    | respected          |
| No lesson status     | fallback to course |
| No course status     | fallback to draft  |

---

## 🧠 Insight

> This converts the system from **destructive override** → **safe augmentation**

---

# 🔵 STEP 2 — Remove Hardcoded Metadata

## File: `app/data/core/lesson.ts`

---

### ❌ Before

```ts
const DEFAULT_METADATA = {
  createdAt: '2026-01-01',
  updatedAt: '2026-01-01',
}
```

---

### ✅ After

```ts
const DEFAULT_METADATA = {
  durationMinutes: 15,
  hideCompletion: false,
} as const
```

---

### ✅ Final Factory

```ts
export function createLesson(
  config: Lesson
): Lesson {
  return {
    ...DEFAULT_METADATA,
    ...config,
  }
}
```

---

## 🧠 Insight

| Data Type             | Ownership        |
| --------------------- | ---------------- |
| createdAt / updatedAt | Lesson (domain)  |
| duration / UI flags   | System (factory) |

---

# 🟢 STEP 3 — Enforce Domain Consistency

## Requirement

All lessons must explicitly define:

```ts
createdAt: '2026-04-08'
updatedAt: '2026-04-13'
```

---

## ✅ Action

Applied across all lesson files.

---

## 🧠 Insight

> Explicit duplication is acceptable when it represents **domain truth**

---

# 🟡 STEP 4 — Remove PR Noise

---

## ❌ Problem

`structure.txt` was accidentally committed multiple times.

---

## ✅ Fix

```bash
git rm --cached app/data/courses/restful-api-mastery-greybox/structure.txt
```

Then:

```bash
git commit -m "chore: remove internal structure file from course module"
git push
```

---

## 🧠 Insight

> PR should only contain **production-relevant artifacts**

---

# 🟣 STEP 5 — Fix Formatting & Lint Issues

### ✔ Removed:

* trailing whitespace
* unused imports

---

## 🧠 Insight

> Small inconsistencies reduce reviewer confidence

---

# 🔍 STEP 6 — Validate System Behavior

---

## Checklist

* No TypeScript errors
* No lint errors
* Lessons render correctly
* No unintended draft state
* No missing content (404)

---

## Result

```txt
All validations passed ✔
```

---

# 🧪 STEP 7 — Commit & Push Fixes

---

### Commit

```bash
git commit -m "fix(core): preserve lesson status and remove hardcoded metadata defaults"
```

---

### Push

```bash
git push origin feat/rest-course-clean-final-v2
```

---

# 🧾 STEP 8 — PR Communication

Responded to reviewer with:

```md
Fixes applied:
- Preserved existing lesson status instead of forcing draft fallback
- Removed hardcoded createdAt/updatedAt from lesson factory
- Cleaned up unused imports and formatting issues

System now respects domain data while keeping core engine deterministic.
```

---

# 🧠 Final System State

---

## BEFORE

```txt
System overrides data ❌
Hardcoded metadata ❌
Hidden lessons ❌
PR contains noise ❌
```

---

## AFTER

```txt
System respects data ✔
Metadata is domain-driven ✔
Safe fallback logic ✔
Clean PR ✔
Deterministic behavior ✔
```

---

# 🔒 Engineering Guarantees

| Property      | Status |
| ------------- | ------ |
| Deterministic | ✅      |
| Data-safe     | ✅      |
| Scalable      | ✅      |
| Reviewable    | ✅      |
| Maintainable  | ✅      |

---

# 🧠 Key Engineering Insights

---

## 1. System vs Domain Separation

> System defines behavior
> Domain defines truth

---

## 2. Never Override Existing Data

> Default logic must be **non-destructive**

---

## 3. PR is a Communication Tool

> Clean PR = faster approval

---

## 4. Explicit > Implicit (for domain data)

> Duplication is acceptable if it preserves clarity

---

## 5. Debugging Strategy

| Tool      | Role      |
| --------- | --------- |
| GitHub PR | Analysis  |
| Local IDE | Execution |

---

# 🏁 Final Outcome

## ✅ Achieved

* All review blockers resolved
* Architecture aligned with data ownership principles
* PR cleaned and stabilized
* System behavior predictable and safe

---

# 🟢 Final Status

```txt
✅ MERGEABLE
✅ REVIEW-READY
✅ PRODUCTION-SAFE
```

---

# 🏆 Conclusion

Phase 7 represents a critical transition:

```txt
From: System that works
To:   System that can be trusted
```

---
