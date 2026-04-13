# 📘 ENGINEERING FRAMEWORK — COURSE SYSTEM (END-TO-END)

---

# 🧭 0. GLOBAL OBJECTIVE

## 🎯 System Goal

Build a **deterministic, scalable, and data-safe course system** with:

* Modular lesson structure
* Core engine abstraction
* Strict separation between **domain data** and **system logic**
* Clean PR workflow
* Review-safe architecture

---

# 🧠 1. CORE PRINCIPLES (NON-NEGOTIABLE)

## 1.1 Separation of Concerns

| Layer        | Responsibility      |
| ------------ | ------------------- |
| Lesson File  | Domain Data (truth) |
| Core Engine  | Logic & defaults    |
| Course Layer | Orchestration       |

---

## 1.2 Data Ownership Rule

```txt
System MUST NOT override domain data.
System MAY provide safe defaults only.
```

---

## 1.3 Deterministic Behavior

* No hidden fallback
* No implicit mutation
* All behavior predictable

---

## 1.4 Single Source of Truth

| Data      | Source       |
| --------- | ------------ |
| status    | course layer |
| createdAt | lesson       |
| updatedAt | lesson       |

---

---

# 🏗️ 2. SYSTEM ARCHITECTURE

```txt
app/
 └── data/
      ├── core/
      │    ├── lesson.ts
      │    ├── module.ts
      │    └── course.ts
      │
      └── courses/
           └── <course-name>/
                ├── module-x/
                │    ├── lesson-a.ts
                │    ├── lesson-b.ts
                │    └── index.ts
                └── index.ts
```

---

# ⚙️ 3. CORE ENGINE SPECIFICATION

---

## 3.1 `createLesson`

### Contract

```ts
createLesson(config: Lesson): Lesson
```

---

### Implementation Rules

```ts
const DEFAULT_METADATA = {
  durationMinutes: 15,
  hideCompletion: false,
}

return {
  ...DEFAULT_METADATA,
  ...config,
}
```

---

### ❗ STRICT RULES

* ❌ DO NOT include:

  * `createdAt`
  * `updatedAt`
* ✔ MUST accept full Lesson config
* ✔ MUST NOT override domain data

---

---

## 3.2 `createCourse`

### Contract

```ts
createCourse(course: CourseInput, options?): Course
```

---

### Status Resolution Logic (CRITICAL)

```ts
status: openLessons.has(lesson.slug)
  ? 'published'
  : (lesson.status ?? course.status ?? 'draft')
```

---

### ❗ STRICT RULES

* ❌ NEVER force status
* ✔ ALWAYS respect existing lesson.status
* ✔ ALWAYS fallback safely

---

---

## 3.3 `createModule`

### Responsibility

* normalize lesson order
* ensure structure consistency

---

---

# 🧱 4. DOMAIN DATA STANDARD

---

## 4.1 Lesson File (MANDATORY FORMAT)

```ts
export default createLesson({
  id: 'lesson-id',
  slug: 'lesson-slug',
  title: 'Lesson Title',
  type: 'lesson',
  order: 1,
  content: '...',

  createdAt: '2026-04-08',
  updatedAt: '2026-04-11',
})
```

---

## ❗ RULES

* ✔ createdAt REQUIRED
* ✔ updatedAt REQUIRED
* ✔ NO implicit metadata

---

---

## 4.2 Module File

```ts
import lesson1 from './lesson-1'
import lesson2 from './lesson-2'

export default {
  id: 'module-id',
  slug: 'module-slug',
  title: 'Module Title',
  order: 1,
  lessons: [lesson1, lesson2],
}
```

---

---

## 4.3 Course File

```ts
const course = createCourse(baseCourse, {
  openLessonSlugs: new Set([...])
})
```

---

---

# 🔒 5. ENGINEERING CONSTRAINTS

---

## 5.1 Forbidden Patterns

❌ Hardcoded domain data in core
❌ Multiple sources of truth
❌ Implicit fallback logic
❌ Hidden mutation

---

## 5.2 Required Guarantees

✔ Deterministic output
✔ Explicit data ownership
✔ Clean modular structure

---

---

# 🧪 6. VALIDATION CHECKLIST (MANDATORY)

Before commit:

---

## Code Level

* [ ] No TypeScript errors
* [ ] No unused imports
* [ ] No lint warnings

---

## Data Level

* [ ] All lessons have createdAt
* [ ] All lessons have updatedAt
* [ ] No hardcoded metadata in core

---

## Behavior Level

* [ ] No unexpected draft status
* [ ] No hidden lessons
* [ ] No 404

---

---

# 🔁 7. DEVELOPMENT WORKFLOW

---

## Step-by-step

### 1. Analyze requirement

### 2. Identify domain vs system

### 3. Modify in local environment

### 4. Validate behavior

### 5. Self-review diff

### 6. Commit (single responsibility)

### 7. Push

### 8. PR review

---

---

# 🧾 8. PR STANDARD

---

## PR MUST:

* contain only relevant files
* have clear title
* have deterministic description
* have no noise

---

## PR MUST NOT:

* include docs accidentally
* include experimental code
* mix UI + data changes

---

---

# 🧠 9. DEBUGGING FRAMEWORK

---

## When issue occurs:

### Step 1 — Identify Layer

| Issue Type       | Layer  |
| ---------------- | ------ |
| wrong data       | lesson |
| wrong behavior   | core   |
| wrong visibility | course |

---

### Step 2 — Apply Fix

* NEVER patch blindly
* ALWAYS respect ownership

---

---

# 🧠 10. DECISION TREE (CRITICAL)

---

## When adding new logic:

```txt
Is this domain data?
  YES → put in lesson
  NO  → continue

Is this behavior?
  YES → put in core
  NO  → continue

Is this orchestration?
  YES → put in course
```

---

---

# 🔄 11. EXTENSION PROTOCOL (FOR NEXT AI)

---

## When extending system:

### Allowed:

* add new course
* add new module
* add new lesson
* extend core safely

---

### Not Allowed:

* modifying core without validation
* introducing implicit behavior
* duplicating logic

---

---

# 🚨 12. FAILURE MODES (IMPORTANT)

---

## Common mistakes

### ❌ Overriding status

→ leads to hidden lessons

---

### ❌ Hardcoding metadata

→ breaks domain truth

---

### ❌ Dirty PR

→ rejected by owner

---

---

# 🏁 13. FINAL SYSTEM STATE

---

## System Characteristics

```txt
Deterministic ✔
Data-safe ✔
Modular ✔
Scalable ✔
Reviewable ✔
```

---

---

# 🧠 14. ENGINEERING MINDSET

---

## Golden Rule

> “System should assist data, not control it.”

---

## Senior Rule

> “Every default must be non-destructive.”

---

---

# 🚀 15. HANDOFF INSTRUCTION (FOR NEXT AI)

---

If you are another AI continuing this system:

1. Read this document fully
2. Do NOT assume behavior
3. Respect data ownership strictly
4. Validate before modifying core
5. Keep PR clean and scoped

---

# 🏆 FINAL CONCLUSION

This framework transforms development from:

```txt
Trial-and-error coding
```

into:

```txt
Deterministic engineering system
```

---

