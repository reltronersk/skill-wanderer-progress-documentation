# API vs HTTP vs REST

These three terms are closely related, but they are **not the same thing**.

The simplest mental model is:

```text
┌──────────────┐
│     API      │
│  Interface   │
└──────┬───────┘
       │
       │ communicates through
       ▼
┌──────────────┐
│     HTTP     │
│ Communication│
│   Protocol   │
└──────┬───────┘
       │
       │ REST is an
       │ architectural style
       ▼
┌──────────────┐
│     REST     │
│ Architectural│
│    Style     │
└──────────────┘
````

---

## API

**API** is the broadest concept.

An API defines an interface that allows software systems to interact.

```text
Client
  │
  │ Uses an API
  ▼
┌───────────┐
│    API    │
└─────┬─────┘
      │
      ▼
Other System
```

Think:

> **API = the interface**

---

## HTTP

**HTTP (Hypertext Transfer Protocol)** is a communication protocol commonly used by Web APIs.

It defines how requests and responses are exchanged.

```text
Client                         Server
  │                              │
  │────── HTTP Request ─────────►│
  │                              │
  │◄───── HTTP Response ─────────│
  │                              │
```

Think:

> **HTTP = the communication protocol**

HTTP defines things such as:

* Requests
* Responses
* Methods
* Headers
* Status codes

These will be explored in Module 2.

---

## REST

**REST (Representational State Transfer)** is an architectural style for designing networked systems.

REST commonly uses HTTP to implement interactions with resources.

```text
Client
  │
  │ HTTP
  ▼
REST API
  │
  ▼
Resources
```

Think:

> **REST = an architectural style**

REST introduces concepts such as:

* Resources
* Representations
* Stateless communication
* Resource-oriented interactions

These concepts will be explored in Module 3.

---

## How They Work Together

A typical RESTful Web API can be understood like this:

```text
┌──────────────────┐
│      Client      │
└────────┬─────────┘
         │
         │ HTTP
         ▼
┌──────────────────┐
│   RESTful API    │
│                  │
│  REST principles │
│  + HTTP          │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Server / Backend │
└──────────────────┘
```

The concepts have different roles:

```text
API
│
│ defines the interface
▼
REST
│
│ provides an architectural approach
▼
HTTP
│
│ commonly carries the communication
▼
Request / Response
```

---

## A Practical Example

Suppose a client wants to retrieve a user.

Conceptually:

```text
Client
  │
  │ "Give me user 123"
  ▼
RESTful API
  │
  │ HTTP GET request
  ▼
Server
  │
  │ User data
  ▼
HTTP response
  │
  ▼
Client
```

Here:

```text
API   → interface being used
REST  → architectural style
HTTP  → communication protocol
```

---

## They Are Not Synonyms

Avoid these assumptions:

```text
❌ API = HTTP
❌ API = REST
❌ HTTP = REST
```

Instead:

```text
API
│
├── Can use different communication mechanisms
│
└── Web API
      │
      └── Commonly uses HTTP
             │
             └── REST is one architectural approach
```

---

## Quick Mental Model

Remember these three words:

```text
┌───────────┐
│    API    │ → Interface
└───────────┘

┌───────────┐
│   HTTP    │ → Communication Protocol
└───────────┘

┌───────────┐
│   REST    │ → Architectural Style
└───────────┘
```

Or simply:

> **API tells us how software can interact. HTTP defines how web communication is exchanged. REST provides an architectural style for organizing those interactions.**

---

## Key Takeaways

```text
API
 ↓
Interface between software systems

HTTP
 ↓
Protocol commonly used for Web API communication

REST
 ↓
Architectural style commonly implemented using HTTP
```

The important relationship is:

```text
        API
         │
         ▼
      Web API
         │
         ▼
   RESTful API
         │
         ▼
   HTTP Request
         │
         ▼
  HTTP Response
```

In the next lessons, we will move from these concepts into the actual flow of an API interaction:

> **Request → Processing → Response**

