# HTTP Methods Deep Dive — Chunk 1.1
## GET Method — Reading Data in REST API

> Extension material for `introduction-to-rest-api.ts`

---

# GET Method

## Definition

`GET` is the standard HTTP method used to **retrieve**, **read**, or **fetch** data from a server without changing the server state.

When a client sends a GET request, the client is asking:

> "Please give me information about this resource."

GET is the most frequently used HTTP method on the internet because almost every web page, mobile application, dashboard, search engine, and API integration depends on data retrieval.

---

# The Core Mental Model

A GET request should behave like:

- Reading a book
- Viewing a dashboard
- Opening a product page
- Searching articles
- Fetching profile data
- Viewing transaction history

The important principle:

> GET should NOT create, modify, delete, or mutate server data.

The server may process heavy logic internally, but the final operation must remain read-only from the client perspective.

---

# Real World Analogy

Imagine entering a public library.

You ask the librarian:

> "Can I see the history books section?"

The librarian shows you the books.

You read the books.

You leave.

The books remain unchanged.

That is the philosophy of GET.

You requested information without modifying the library inventory.

---

# Basic Example

## Request

```http
GET /api/users/42
````

## Meaning

* `GET` → retrieve data
* `/api/users/42` → specific user with ID 42

---

# Example Response

```json
{
  "id": 42,
  "name": "Alice",
  "role": "student"
}
```

---

# 5W + 1H Analysis

# WHAT — What is GET?

GET is an HTTP method used to retrieve existing resources from a server.

GET is designed for:

* Reading data
* Viewing resources
* Fetching lists
* Searching information
* Loading dashboards
* Viewing reports
* Accessing analytics

GET does not represent mutation.

GET represents observation.

---

# WHY — Why does GET exist?

Without GET:

* websites cannot display data
* mobile apps cannot load feeds
* dashboards cannot show analytics
* search engines cannot retrieve pages
* APIs become unpredictable
* caching becomes impossible
* browsers cannot optimize performance

GET exists because systems need a standardized and safe mechanism for data retrieval.

GET creates predictability between:

* frontend
* backend
* CDN
* proxy
* browser
* caching layer
* monitoring system

---

# WHEN — When should GET be used?

Use GET when:

* retrieving existing data
* loading detail pages
* viewing analytics
* reading reports
* searching information
* filtering resources
* paginating collections
* opening profile pages
* displaying dashboard metrics

---

# Common Real Examples

| Scenario                 | GET Example                       |
| ------------------------ | --------------------------------- |
| Open Instagram profile   | `GET /api/users/rei`              |
| View products            | `GET /api/products`               |
| Read transaction history | `GET /api/transactions`           |
| Search courses           | `GET /api/courses?search=laravel` |
| Load analytics dashboard | `GET /api/analytics/monthly`      |
| Open notification list   | `GET /api/notifications`          |

---

# WHEN NOT To Use GET

Never use GET for operations that change data.

Bad examples:

```http
GET /api/delete-user/42
```

```http
GET /api/create-payment
```

```http
GET /api/publish-post
```

These violate REST semantics because GET should remain safe and read-only.

---

# WHERE — Where is GET heavily used?

GET dominates almost every internet system:

| Industry        | Usage                      |
| --------------- | -------------------------- |
| E-commerce      | product catalog            |
| Banking         | transaction history        |
| Social media    | feeds and profiles         |
| Education       | courses and modules        |
| Healthcare      | patient records            |
| Transportation  | schedules                  |
| AI systems      | inference result retrieval |
| Enterprise SaaS | dashboards                 |
| Cloud platforms | monitoring metrics         |

---

# WHO — Who uses GET?

Almost every system actor uses GET:

| Actor              | Purpose                |
| ------------------ | ---------------------- |
| Browser            | load pages             |
| Mobile apps        | fetch API data         |
| Frontend apps      | render UI              |
| Backend services   | internal communication |
| Search engines     | index pages            |
| Monitoring systems | retrieve metrics       |
| AI agents          | gather context         |
| CDN                | cache responses        |

---

# HOW — How does GET work internally?

The flow usually looks like this:

```text
Client
   ↓
HTTP GET Request
   ↓
Load Balancer
   ↓
API Gateway
   ↓
Authentication Middleware
   ↓
Controller
   ↓
Service Layer
   ↓
Database Query
   ↓
Response Serialization
   ↓
JSON Response
   ↓
Client
```

---

# Step-by-Step Internal Breakdown

## 1. Client Sends Request

Example:

```http
GET /api/products?page=2&category=laptop
```

The request may include:

* URL
* query parameters
* headers
* authorization token

Usually GET has no body.

---

## 2. Gateway Receives Request

Infrastructure components inspect:

* rate limits
* authentication
* routing
* caching rules

---

## 3. Backend Processes Query

The server may:

* validate query parameters
* apply pagination
* filter database records
* join multiple tables
* aggregate metrics
* check permissions

---

## 4. Database Executes Read Operation

Example SQL:

```sql
SELECT *
FROM products
WHERE category = 'laptop'
LIMIT 20 OFFSET 20;
```

---

## 5. Response Returned

Example:

```json
{
  "page": 2,
  "data": [
    {
      "id": 71,
      "name": "ThinkPad X1"
    }
  ]
}
```

---

# Important Characteristics of GET

# 1. Safe

GET should not modify server state.

Safe means:

* no deletion
* no creation
* no update
* no mutation

This is critical for:

* browsers
* crawlers
* cache systems
* retry mechanisms

---

# 2. Idempotent

Calling the same GET multiple times should produce the same effect.

Example:

```http
GET /api/users/42
GET /api/users/42
GET /api/users/42
```

No matter how many times repeated:

* the resource is not duplicated
* server state does not change

---

# 3. Cacheable

GET is highly optimized for caching.

This is one of the biggest reasons why GET is extremely powerful.

Caching can happen at:

* browser
* CDN
* reverse proxy
* API gateway
* server memory

---

# Real Business Impact of GET Caching

Without caching:

* servers become overloaded
* cloud costs increase
* latency becomes high
* user experience degrades

With caching:

* pages load faster
* infrastructure becomes cheaper
* scalability improves dramatically

---

# Real Case Study — E-Commerce Flash Sale

## Situation

An e-commerce platform runs a flash sale.

Suddenly:

* 12 million users open product pages simultaneously
* all users request product data
* recommendation systems request related products
* mobile apps continuously refresh inventory

Every request uses GET.

---

# The Problem

Without proper GET optimization:

* database crashes
* API latency spikes
* infrastructure costs explode
* payment systems become unstable
* customers fail checkout

---

# The Solution

The engineering team implements:

* CDN caching
* Redis caching
* pagination
* query optimization
* response compression
* selective field retrieval

---

# Result

The platform survives peak traffic.

This demonstrates:

> GET scalability architecture directly affects business survival.

---

# Query Parameters in GET

GET frequently uses query parameters.

Example:

```http
GET /api/products?category=laptop&page=2&sort=price
```

---

# Why Query Parameters Matter

Query parameters allow flexible retrieval without changing endpoint identity.

The endpoint remains:

```http
/api/products
```

But the view changes dynamically.

---

# Common Query Parameter Use Cases

| Purpose         | Example            |
| --------------- | ------------------ |
| Pagination      | `?page=2`          |
| Filtering       | `?role=admin`      |
| Sorting         | `?sort=price`      |
| Searching       | `?search=nestjs`   |
| Date Range      | `?from=2026-01-01` |
| Field Selection | `?fields=id,name`  |

---

# GET and Pagination

Large datasets should NEVER return everything at once.

Bad:

```http
GET /api/users
```

Returning:

```text
12 million users
```

Good:

```http
GET /api/users?page=1&limit=20
```

---

# Why Pagination Matters

Without pagination:

* memory usage explodes
* response becomes slow
* mobile devices freeze
* bandwidth usage increases
* databases become stressed

Pagination protects system stability.

---

# GET and Security

GET itself is safe semantically, but data exposure remains dangerous.

---

# Common Security Risks

| Risk                   | Example                  |
| ---------------------- | ------------------------ |
| Sensitive query params | password in URL          |
| Overfetching           | exposing internal fields |
| ID enumeration         | `/users/1`, `/users/2`   |
| Missing authorization  | anyone can read data     |
| Excessive payload      | data leakage             |

---

# Dangerous Example

```http
GET /api/users?password=123456
```

URLs may appear in:

* logs
* browser history
* analytics
* monitoring tools
* proxy systems

Sensitive information should never be placed in query strings.

---

# GET and Observability

GET endpoints are heavily monitored because they dominate traffic volume.

Teams analyze:

* latency
* throughput
* cache hit ratio
* error rate
* database load
* traffic spikes

---

# Enterprise Reality

In many enterprise systems:

> 70%–95% of total traffic volume is GET traffic.

Because reading data is far more common than modifying data.

---

# GET vs POST

| Aspect               | GET           | POST          |
| -------------------- | ------------- | ------------- |
| Purpose              | retrieve data | create data   |
| Safe                 | yes           | no            |
| Idempotent           | yes           | usually no    |
| Cacheable            | yes           | limited       |
| Body                 | usually none  | commonly used |
| Browser bookmarkable | yes           | no            |

---

# Common Beginner Mistakes

# Mistake 1 — Using GET for deletion

Bad:

```http
GET /api/delete-order/42
```

Correct:

```http
DELETE /api/orders/42
```

---

# Mistake 2 — Returning gigantic payloads

Bad architecture causes:

* slow responses
* memory spikes
* mobile crashes

Always paginate.

---

# Mistake 3 — Exposing sensitive information in URL

Never place:

* passwords
* OTP
* tokens
* secrets

inside query parameters.

---

# Mistake 4 — Ignoring caching

Without caching:

* scalability collapses
* cloud cost rises
* performance degrades

---

# System Design Insight

GET is deceptively simple.

But in reality:

* caching architecture
* distributed systems
* indexing strategy
* query optimization
* observability
* CDN behavior
* pagination strategy
* authorization layers

all heavily depend on GET behavior.

---

# High-Level Engineering Philosophy

A high-quality GET endpoint should be:

* predictable
* cache-friendly
* secure
* scalable
* observable
* paginated
* filterable
* fast
* deterministic

---

# Summary

* GET retrieves data from the server
* GET should never mutate server state
* GET is safe and idempotent
* GET powers most internet traffic
* GET heavily depends on caching architecture
* Query parameters shape retrieval behavior
* Pagination is mandatory for scalability
* Security and authorization still matter
* GET optimization directly affects infrastructure cost and user experience

---


