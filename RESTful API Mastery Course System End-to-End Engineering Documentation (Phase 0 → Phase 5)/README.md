# 📘 RESTful API Mastery Course System

## End-to-End Engineering Documentation (Phase 0 → Phase 5)

---

# 🧭 OVERVIEW

## 🎯 Objective

Design and implement a **modular, scalable, and deterministic course system** by transforming a monolithic course structure into a **factory-driven architecture with a reusable core engine**.

---

## 🏗️ Final Architecture (After Phase 5)

```txt
app/
 └── data/
      ├── core/                    ← Global engine (Phase 5)
      │    ├── lesson.ts
      │    ├── module.ts
      │    └── course.ts
      │
      └── courses/
           └── restful-api-mastery-greybox/
                ├── module-1/
                │    ├── lesson-a.ts
                │    ├── lesson-b.ts
                │    └── index.ts
                ├── module-2/
                └── index.ts
```

---

# 🚀 PHASE 0 — FOUNDATION & PROBLEM IDENTIFICATION

## 🎯 Goal

Understand limitations of the current system and define a deterministic blueprint.

---

## ❌ Initial State

Course defined as:

```ts
lessons: [
  { id, slug, title, content, ... }
]
```

---

## ❌ Problems

1. Monolithic lesson definition
2. No reuse of logic
3. High duplication:

   * `createdAt`
   * `updatedAt`
   * `status`
4. No separation of concerns
5. Hard to scale across multiple courses

---

## ✅ Outcome

Defined a **blueprint**:

```txt
Course → Modules → Lessons (file-based)
```

---

# 🧱 PHASE 1 — EXTRACTION (LESSON MODULARIZATION)

## 🎯 Goal

Convert inline lessons → individual files

---

## 🔧 Transformation

### Before

```ts
lessons: [
  { id: 'lesson-1', title: 'Intro', ... }
]
```

---

### After

```ts
import lesson1 from './lesson-1'

lessons: [lesson1]
```

---

## 📁 New Structure

```txt
module-1/
 ├── lesson-a.ts
 ├── lesson-b.ts
 └── index.ts
```

---

## ✅ Benefits

* Isolated lesson logic
* Easier editing & maintenance
* File-level granularity

---

# 🧠 PHASE 2 — FACTORY INTRODUCTION

## 🎯 Goal

Eliminate duplication and standardize lesson creation

---

## 🔧 Implementation

### Introduced factory:

```ts
createRestLesson(config)
```

---

## ❌ Before

```ts
{
  id,
  slug,
  title,
  createdAt,
  updatedAt,
  durationMinutes,
  ...
}
```

---

## ✅ After

```ts
createRestLesson({
  id,
  slug,
  title,
  content,
})
```

---

## 🧠 Internal Factory Logic

```ts
function createBaseLesson(partial): Lesson {
  return {
    durationMinutes: 15,
    createdAt: '...',
    updatedAt: '...',
    ...partial,
  }
}
```

---

## ✅ Benefits

* DRY (no duplication)
* Consistent defaults
* Centralized logic

---

# 🧪 PHASE 3 — EXECUTION & SCALING

## 🎯 Goal

Apply modular + factory pattern across all modules (1–6)

---

## 🔁 Pattern

For each module:

1. Extract lessons
2. Wrap with factory
3. Import into module index

---

## 📁 Result

```txt
module-1/
module-2/
module-3/
module-4/
module-5/
module-6/
```

All fully modularized.

---

## ⚠️ Key Fix During Phase

### Issue:

```ts
createRestLesson not found
```

### Solution:

```ts
export function createRestLesson(...) { ... }
```

---

## ✅ Outcome

* All modules consistent
* No inline lessons remaining
* Fully modular structure

---

# 🔒 PHASE 4 — HARDENING

## 🎯 Goal

Eliminate hidden bugs and enforce strict consistency

---

## 🔴 Issue 1 — Dual Source of Truth (status)

### Before

```ts
status: 'published' // inside lesson
```

AND

```ts
status: OPEN_LESSON_SLUGS.has(...)
```

---

### ❌ Problem

Conflicting sources → inconsistent UI

---

### ✅ Fix

👉 Remove `status` from lesson

```ts
// lesson has NO status
```

👉 Controlled at course level only

---

## 🔴 Issue 2 — Metadata Duplication

### Before

```ts
createdAt: '2026-04-08'
updatedAt: '2026-04-08'
```

---

### ✅ Fix

Moved to factory:

```ts
DEFAULT_METADATA
```

---

## 🔴 Issue 3 — Weak Type Safety

### Fix

```ts
export function createRestLesson(
  config: Omit<Lesson, 'createdAt' | 'updatedAt'>
)
```

---

## 🔴 Issue 4 — Type Over-restriction Bug

### Error:

```txt
durationMinutes does not exist
```

---

### Fix:

```ts
Omit<Lesson, 'createdAt' | 'updatedAt'>
```

NOT removing operational fields.

---

## ✅ Outcome

* Single source of truth
* Strong typing
* No duplication
* Deterministic behavior

---

# 🏗️ PHASE 5 — SYSTEM DESIGN (CORE ENGINE)

## 🎯 Goal

Generalize system → reusable across all courses

---

## ❌ Before

```txt
rest-lesson-shell.ts (local only)
```

---

## ✅ After

```txt
app/data/core/
```

---

## 📁 Core Engine

---

### 1. `lesson.ts`

```ts
export function createLesson(config): Lesson
```

---

### 2. `module.ts`

```ts
export function createModule(module): Module
```

* ensures lesson ordering

---

### 3. `course.ts`

```ts
export function createCourse(course, options): Course
```

---

## 🔥 Responsibilities

### createCourse:

* inject status
* calculate lessonCount
* normalize modules

---

## 🧠 Critical Design Fix

### Problem:

```ts
const baseCourse: Course
```

---

### Fix:

```ts
type CourseInput = Omit<Course, 'lessonCount'>
```

---

## 🔧 Status Injection

```ts
status: openLessons.has(lesson.slug)
  ? ('published' as const)
  : ('draft' as const)
```

---

## ✅ Outcome

* Full separation:

  * Input (raw data)
  * Output (computed course)
* Reusable across all courses
* Centralized behavior

---

# 🧠 SYSTEM TRANSFORMATION SUMMARY

## BEFORE

```txt
Inline lessons
Manual status
Duplicated metadata
Course-specific logic
```

---

## AFTER

```txt
Modular lessons (file-based)
Factory-driven creation
Core engine abstraction
Single source of truth
Reusable architecture
```

---

# 🔒 ENGINEERING GUARANTEES

| Property      | Status |
| ------------- | ------ |
| Deterministic | ✅      |
| Scalable      | ✅      |
| Maintainable  | ✅      |
| Type-safe     | ✅      |
| Reusable      | ✅      |

---

# 🏁 FINAL STATE

## You now have:

> 🔥 A **Course Engine**, not just a course

---

# 🧠 KEY INSIGHTS

## 1. Separation of Concerns

* Lesson = data
* Factory = logic
* Course = orchestration

---

## 2. Input vs Output Types

```txt
CourseInput → createCourse → Course
```

---

## 3. Single Source of Truth

```txt
status → ONLY in course layer
```

---

## 4. Deterministic Architecture

No hidden behavior
No duplicated logic
No ambiguity

---

# 🚀 NEXT PHASE

## Phase 6 (already completed separately)

* PR sanitization
* clean branch strategy
* review-ready engineering

---

## Future (optional)

* Schema validation (Zod)
* CLI generator
* CMS integration
* dynamic content system

---

# 🏆 FINAL CONCLUSION

From Phase 0 → Phase 5, you have transformed:

```txt
Monolithic Course Data
```

into:

```txt
Scalable Learning Platform Engine
```

---

