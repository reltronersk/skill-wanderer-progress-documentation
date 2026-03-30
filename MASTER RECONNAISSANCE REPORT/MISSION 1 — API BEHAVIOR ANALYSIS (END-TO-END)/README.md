# 🔍 MISSION 1 — API BEHAVIOR ANALYSIS (END-TO-END)

**Scope:** URI · Parameter Semantics · Response Contract · Error Behavior
**Mode:** Greybox · Deterministic · Contract-Oriented · Non-Assumptive

---

# 🧠 1️⃣ Executive Objective

Mission 1 is not merely API exploration.

It is a process of:

> **Extracting the system contract purely from observable behavior**
> without access to backend implementation.

Target capability:

* Reverse-engineer API contract
* Identify behavioral invariants
* Distinguish identity vs filtering semantics
* Validate response determinism

---

# 🧱 2️⃣ System Under Observation

**Target Endpoint:**

```text
https://jsonplaceholder.typicode.com
```

**Observed Resource:**

```text
/posts
```

---

# 🧬 3️⃣ Reconnaissance Methodology

Framework used:

```text
Observe → Compare → Validate → Conclude
```

Constraints:

* No backend access
* No dependency on documentation
* Only HTTP input/output allowed

---

# 🌐 4️⃣ Layer 1 — Collection Endpoint Discovery

## Request

```http
GET /posts
```

---

## Observed Response

```json
[
  {
    "userId": 1,
    "id": 1,
    "title": "...",
    "body": "..."
  }
]
```

---

## Structural Analysis

### Type

```text
array[]
```

### Element Schema

```text
userId: integer
id: integer
title: string
body: string
```

---

## Contract Extraction

```text
Endpoint: /posts
Type: Collection
Return: array<object>
Status: 200
Schema: stable
```

---

## Invariant

```text
Collection endpoint MUST return an array
```

---

# 🔍 5️⃣ Layer 2 — Query Parameter Behavior

## Request

```http
GET /posts?userId=1
```

---

## Observed Behavior

* Response remains an array
* Data count decreases
* All records satisfy the condition

---

## Behavioral Deduction

```text
Query parameter = filtering mechanism
```

---

## Internal Model (Inferred)

```pseudo
dataset.filter(condition)
```

---

## Contract

```text
Return type: array
Subset: yes
Empty possible: yes
Status: always 200
```

---

## Invariant

```text
Query parameters do NOT change resource type
```

---

# 🔍 6️⃣ Layer 3 — Path Parameter Behavior

## Request

```http
GET /posts/1
```

---

## Observed Response

```json
{
  "userId": 1,
  "id": 1,
  "title": "...",
  "body": "..."
}
```

---

## Behavioral Shift

| Aspect      | Before | After  |
| ----------- | ------ | ------ |
| Structure   | array  | object |
| Cardinality | many   | single |

---

## Contract Extraction

```text
Endpoint: /posts/{id}
Type: Resource Identity
Return: object
Status: 200 / 404
```

---

## Invariant

```text
Path parameter resolves EXACT resource identity
```

---

# ⚠️ 7️⃣ Edge Case Validation (Critical)

---

## Case A — Missing Resource

```http
GET /posts/9999
```

### Response

```text
404 Not Found
{}
```

---

## Contract

```text
Path parameter failure → 404
```

---

## Case B — Empty Filter

```http
GET /posts?userId=9999
```

### Response

```json
[]
```

---

## Contract

```text
Query parameter failure → 200 + empty array
```

---

# 🔥 8️⃣ Behavioral Comparison Matrix

| Dimension    | Path Param | Query Param       |
| ------------ | ---------- | ----------------- |
| Role         | Identity   | Filter            |
| Endpoint     | `/posts/1` | `/posts?userId=1` |
| Output Type  | object     | array             |
| Failure Mode | 404        | 200 + []          |
| System Layer | Routing    | Data filtering    |
| Cardinality  | Single     | Multiple          |

---

# 🧠 9️⃣ Greybox System Deduction

Without viewing the backend, the system can be confirmed to have:

```text
✔ Resource: posts
✔ Schema consistency
✔ Routing layer (ID-based)
✔ Filtering layer (query-based)
✔ Deterministic response structure
✔ Distinct error semantics
```

---

# 🧬 10️⃣ API Contract Model (Derived)

```text
GET /posts
    → array<Post>

GET /posts?filter
    → array<Post>

GET /posts/{id}
    → Post | 404
```

---

# 🔐 11️⃣ Determinism Validation

All endpoints demonstrate:

```text
✔ Stable schema
✔ Predictable status code
✔ No randomness
✔ No hidden fallback
✔ Input → Output consistency
```

---

# 🧠 12️⃣ Core Engineering Insights

---

## 🔑 1. Resource vs Representation

```text
Resource = entity (post)
Representation = JSON output
```

---

## 🔑 2. Identity vs Condition

```text
Path → identity
Query → condition
```

---

## 🔑 3. Contract-Driven Thinking

```text
API = contract, not just an endpoint
```

---

## 🔑 4. Failure Semantics Matter

```text
404 = resource missing
200 + [] = valid query, no result
```

---

## 🔑 5. Greybox Engineering Skill

```text
Understand the system without internal access
```

---

# 🧱 13️⃣ Invariants Locked

```text
✔ Collection → array
✔ Identity → object
✔ Query → subset
✔ Missing identity → 404
✔ Missing filter → []
✔ Schema consistency across responses
```

---

# 🧭 14️⃣ Architectural Position

Mission 1 establishes the foundation:

> **API Behavior Literacy Layer**

Which will be used in:

* Auth system
* Token flow
* Middleware design
* Backend service architecture

---

# 🚀 15️⃣ Transition to Mission 2

Mission 1:

```text
READ · URI · CONTRACT · BEHAVIOR
```

Mission 2:

```text
AUTH · HEADER · TOKEN · STATE · AUTOMATION
```

---

# Closing Statement

Mission 1 is not about GET requests.

It is about:

* Contract discovery
* Behavior validation
* Deterministic thinking
* Greybox system understanding

The system is no longer “mysterious.”

Now:

```text
Input → Behavior → Output → Contract → Architecture
```

---
