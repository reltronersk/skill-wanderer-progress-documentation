# What Is an API?

An **API (Application Programming Interface)** is a defined interface that allows software systems to communicate with each other.

The basic idea is:

```text
┌──────────────────┐
│   Client App     │
│                  │
│  Mobile / Web /  │
│  Desktop / Tool  │
└────────┬─────────┘
         │
         │ API
         ▼
┌──────────────────┐
│   Other System   │
│                  │
│ Application      │
│ Business Logic   │
│ Database         │
└──────────────────┘
````

The client does not need to know how the other system works internally.

It communicates through the **API interface**.

---

## API as a Communication Boundary

```text
        Client System
             │
             │ Request
             ▼
      ┌─────────────┐
      │     API     │
      └──────┬──────┘
             │
             │ Interaction
             ▼
       Server System
             │
             │ Response
             ▼
      ┌─────────────┐
      │     API     │
      └──────┬──────┘
             │
             ▼
        Client System
```

At a high level:

> **Client → API → System → API → Client**

---

## Why Do We Need APIs?

APIs allow different software systems to interact without exposing their internal implementation.

For example:

```text
┌──────────────┐
│  Mobile App  │
└──────┬───────┘
       │
       │ API Request
       ▼
┌──────────────┐
│  Weather API │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Weather Data │
│ & Processing │
└──────────────┘
```

The mobile app can use the weather service without directly accessing its internal database or business logic.

---

## API ≠ Database

An API is an **interface**, not the database itself.

```text
Client
  │
  ▼
 API
  │
  ▼
Business Logic
  │
  ▼
Database
```

Each part has a different role.

---

## API ≠ REST

**API** is a broad concept.

**REST** is one architectural style that can be used to build APIs.

```text
API
│
├── REST API
├── GraphQL API
├── SOAP API
└── Other API styles
```

So:

> **REST API is a type of API.**

---

## The Basic API Interaction

The fundamental pattern is:

```text
┌──────────┐
│  Client  │
└────┬─────┘
     │
     │ Request
     ▼
┌──────────┐
│   API    │
└────┬─────┘
     │
     │ Processing
     ▼
┌──────────┐
│  System  │
└────┬─────┘
     │
     │ Response
     ▼
┌──────────┐
│  Client  │
└──────────┘
```

In this course, you will later examine this interaction using **HTTP** and **Postman**.

---

## Key Takeaways

```text
API
│
├── Interface between software systems
├── Defines how systems communicate
├── Hides internal implementation
└── Enables programmatic interaction
```

Remember:

> **An API is a defined interface that allows software systems to communicate and interact.**

