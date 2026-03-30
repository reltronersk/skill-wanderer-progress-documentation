# 📄 CV SECTION — PROJECT EXPERIENCE (HIGH IMPACT)

## 🔷 Full-Cycle Frontend & System Contributor

**Skill-Wanderer Ecosystem (Multi-Repository Contribution)**
*Mar 2026 – Present*

---

### 🧩 Key Contributions

* Led a **modular refactor of landing page architecture** in a Nuxt-based application
  → Transformed monolithic `index.vue` into reusable, maintainable component structure
  (`HeroSection`, `FeaturesSection`, `ValuesSection`, `JourneySection`, `ExpertiseBriefSection`)

* Implemented **clean lifecycle management** for browser interactions
  → Proper use of `onMounted` / `onUnmounted` with deterministic event cleanup
  → Eliminated potential memory leaks (scroll + anchor navigation handling)

* Standardized **UI consistency with design system alignment**
  → Unified grid systems, spacing, and reusable styles (`main.css`)
  → Improved maintainability and visual consistency across sections

* Applied **production-safe infrastructure practices**
  → Secured Docker configuration using environment variables (`env_file`)
  → Restricted database port exposure to localhost
  → Designed local-first infrastructure strategy (not pushed to repo)

---

### 🔀 Cross-Repository Engineering Workflow

Worked across multiple repositories simultaneously:

#### 1. Landing Page (Nuxt)

* Refactor & system restructuring
* PR: `rei-dev-1 → dev` (#107)

#### 2. LMS Frontend (LMS-FE)

* Created feature branch: `rei-lyra`
* Added chatbot asset integration:

  ```text
  public/images/lyra.webp
  ```
* Delivered via PR-ready branch

#### 3. Blog Platform (blog-v2)

* Created feature branch: `rei-nova`
* Added chatbot asset integration:

  ```text
  public/images/nova.webp
  ```
* Synced with platform UI requirements

---

### ⚙️ Engineering Practices Demonstrated

* **Branching Strategy**

  * Feature branching (`rei-dev-1`, `rei-lyra`, `rei-nova`)
  * PR flow aligned with `dev` environment (not direct to `main`)

* **Conflict Resolution**

  * Resolved merge conflicts between `main` and `dev`
  * Maintained architectural consistency during refactor

* **Git Workflow Mastery**

  * Multi-repo coordination
  * Upstream tracking & synchronization
  * Safe handling of tracked vs local-only files

* **Code Quality & Structure**

  * Separation of concerns (SEO, UI, lifecycle)
  * Deterministic event handling
  * Component-based architecture

---

### 🧠 System Thinking Highlights

* Transitioned from **feature implementation → system-level thinking**
* Designed with:

  * **Maintainability**
  * **Scalability**
  * **Separation of concerns**
  * **Production readiness**

---

### 🚀 Impact

* Reduced complexity of landing page structure
* Improved onboarding readability for future contributors
* Enabled scalable UI development via modular components
* Established consistent engineering patterns across repos

---

