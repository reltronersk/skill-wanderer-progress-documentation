# What Is an API?

An **API (Application Programming Interface)** is a defined interface that allows software systems to communicate and interact with each other.

At its simplest:

```text
┌──────────────────┐
│   Client App     │
└────────┬─────────┘
         │
         │ API
         ▼
┌──────────────────┐
│   Other System   │
└──────────────────┘
````

---

## Where Did the Idea of an API Come From?

The idea of an API is older than modern web APIs.

Software developers have long needed a **defined interface** for one part of a software system to use another part without knowing its internal implementation.

Over time, APIs evolved from:

```text
Software Libraries
       ↓
Operating System APIs
       ↓
Application APIs
       ↓
Web APIs
       ↓
Modern REST APIs
```

Today, when developers talk about APIs, they often mean **web APIs** that allow different applications and services to communicate over a network.

---

## API as a Communication Boundary

The same idea applies to software:

```text
┌──────────────────┐
│      Client      │
│                  │
│  Mobile / Web /  │
│  Desktop / Tool  │
└────────┬─────────┘
         │
         │ Request
         ▼
   ┌───────────┐
   │    API    │
   └─────┬─────┘
         │
         │ Interaction
         ▼
┌──────────────────┐
│      System      │
│                  │
│ Business Logic   │
│ Database         │
│ Internal Services│
└──────────────────┘
```

The client does not need direct access to the system's internal implementation.

It communicates through the API.

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

---

## API ≠ REST

**API** is a broad concept.

**REST** is one architectural style used for APIs.

```text
API
│
├── REST API
├── GraphQL API
├── SOAP API
└── Other API styles
```

So:

> **A REST API is a type of API.**

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

In this course, we will later explore this interaction through **HTTP**, **REST**, and **Postman**.

---

## Key Takeaways

```text
API
│
├── A defined software interface
├── Enables software-to-software interaction
├── Creates a communication boundary
├── Hides internal implementation
└── Uses defined rules for requests and responses
```

> **The API acts as an interface between the client and the system, just as a waiter acts as an interface between a customer and a restaurant kitchen.**
