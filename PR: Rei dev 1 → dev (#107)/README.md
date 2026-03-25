# 📘 PR: `Rei dev 1` → `dev` (#107)

---

# 🎯 0. OBJECTIVE

```text
Refactor landing page from monolithic → modular component architecture
+ lifecycle cleanup
+ design system alignment
+ infrastructure hardening
```

This PR focuses on transforming a tightly coupled page into a scalable, maintainable, and production-ready architecture.

---

# 🧭 1. INITIAL STATE (BEFORE PR)

```text
pages/index.vue = MONOLITHIC
```

### Key Issues

* All logic centralized in a single file
* Duplicated scroll listeners
* Improper event listener cleanup
* CSS tightly coupled with logic
* Not scalable
* Not reusable

👉 This created **technical debt and high maintenance risk**

---

# 🔨 2. CORE REFACTOR (FOUNDATIONAL CHANGE)

## 🎯 Goal

Transform:

```text
Monolithic Page
```

Into:

```text
Component-Based Architecture
```

---

## 🔧 Implementation

Decomposed into:

```text
HeroSection.vue
FeaturesSection.vue
ValuesSection.vue
JourneySection.vue
ExpertiseBriefSection.vue
```

### New `pages/index.vue`

```vue
<template>
  <div>
    <HeroSection />
    <FeaturesSection />
    <ValuesSection />
    <JourneySection />
    <ExpertiseBriefSection />
  </div>
</template>
```

---

## 🎯 Result

```text
pages/index.vue = ORCHESTRATION LAYER ONLY
```

This ensures clear separation between structure and behavior.

---

# 🧠 3. ARCHITECTURAL PRINCIPLE APPLIED

```text
Separation of Concerns
```

| Layer      | Responsibility   |
| ---------- | ---------------- |
| Page       | Composition only |
| Component  | UI + behavior    |
| Composable | Logic (optional) |

👉 This improves maintainability, testability, and scalability.

---

# ⚠️ 4. PROBLEMS DISCOVERED (REVIEW PHASE)

Identified via Copilot + manual review:

### ❌ Issue 1 — Incorrect lifecycle usage

* `onUnmounted` nested inside `onMounted`

### ❌ Issue 2 — Potential memory leaks

* Event listeners not removed

### ❌ Issue 3 — IntersectionObserver not cleaned up

### ❌ Issue 4 — Duplicate logic

* Scroll listener exists in:

  * `index.vue`
  * `TheNavigation.vue`

### ❌ Issue 5 — Design system misalignment

### ❌ Issue 6 — Insecure docker-compose configuration

### ❌ Issue 7 — Misleading artifact

* `structure.txt` not reflecting actual repo state

---

# 🛠️ 5. FIX ITERATIONS (CRITICAL)

## 🔷 FIX 1 — Lifecycle Correction

From:

```ts
onMounted(() => {
  onUnmounted(() => {})
})
```

To:

```ts
onMounted(() => {})
onUnmounted(() => {})
```

---

## 🔷 FIX 2 — Observer Cleanup

```ts
onUnmounted(() => {
  observer?.disconnect()
})
```

---

## 🔷 FIX 3 — Remove Duplicate Scroll Logic

Removed from:

```text
pages/index.vue
```

Because already handled in:

```text
components/TheNavigation.vue
```

---

## 🔷 FIX 4 — Remove Global Anchor Click Listener

### Reason

Handled natively by CSS:

```css
html {
  scroll-behavior: smooth;
}
```

### Action

```text
Removed global document click listener
```

---

## 🔷 FIX 5 — Design System Alignment

From:

```css
minmax(250px, 1fr)
gap: 40px
```

To:

```css
minmax(280px, 1fr)
gap: 30px
```

👉 Ensures visual consistency across layout system

---

## 🔷 FIX 6 — Docker Compose Security Hardening

From:

```yaml
ports:
  - "5432:5432"
environment:
  POSTGRES_PASSWORD: postgres
```

To:

```yaml
ports:
  - "127.0.0.1:5432:5432"
env_file:
  - .env
```

👉 Prevents external exposure & removes hardcoded secrets

---

## 🔷 FIX 7 — Remove Invalid Artifact

```bash
delete structure.txt
```

### Reason

* Misleading
* Not reflecting real repository
* High risk of becoming stale

---

# ⚔️ 6. MERGE CONFLICT PHASE

Conflict occurred in:

```text
pages/index.vue
```

---

## 🧠 Resolution Strategy

```text
Do not choose one side
→ COMBINE intelligently
```

---

## 🎯 Result

```text
✔ Modular architecture (feature branch)
✔ Updated content (dev/main branch)
```

👉 Demonstrates **context-aware merging**, not destructive overwrite

---

# 🧼 7. FINAL CLEANUP (VERY IMPORTANT)

### Final Commit

```text
fix: remove duplicated scroll logic and global listeners
```

### Actions

* Removed duplicate scroll logic
* Removed global anchor listener
* Enforced clean composition boundary

---

# 🧩 8. FINAL ARCHITECTURE STATE

## pages/index.vue

```text
✔ Composition only
✔ No DOM logic
✔ No global event listeners
✔ Clean orchestration layer
```

---

## Components

```text
✔ Self-contained behavior
✔ Scoped responsibility
✔ Reusable
```

---

# 📊 9. PR STATUS (CURRENT STATE)

```text
✔ Code: COMPLETE
✔ Refactor: COMPLETE
✔ Cleanup: COMPLETE
✔ Conflict: RESOLVED
✔ Push: COMPLETE
✔ PR Updated: COMPLETE
```

---

## ❗ Remaining Blocker

```text
Merge blocked:
→ Requires 1 approval
```

---

# 🧠 10. ENGINEERING LEVEL ANALYSIS

### Demonstrated Capabilities

| Skill                 | Level              |
| --------------------- | ------------------ |
| Refactoring           | Mid → Senior       |
| Conflict Resolution   | Senior             |
| Architectural Cleanup | Senior             |
| PR Handling           | Strong Contributor |
| Feedback Handling     | Strong             |

---

# ⚡ 11. FINAL DECISION LOGIC

```text
IF conflict exists → resolve
IF issues found → fix
IF review feedback exists → iterate
IF everything is clean → stop
```

---

## Current State

```text
ALL CLEAR → STOP EXECUTION
```

---

# 🏁 12. FINAL CONCLUSION

```text
✔ Engineering Work: 100% COMPLETE
⏳ Merge: Awaiting approval
```

---

# 🔥 13. KEY TAKEAWAY

This is no longer about:

```text
"Did I finish coding?"
```

But:

```text
"Did I deliver a clean, production-ready engineering artifact?"
```

### Answer:

👉 **YES**

---

# 🚀 14. CURRENT POSITIONING

```text
System-aware Contributor
```

Not:

```text
Task Executor
```

