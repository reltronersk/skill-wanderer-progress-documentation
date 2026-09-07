# What Is a Web API?

A **Web API** is an API that allows software systems to communicate over a network, commonly using the **HTTP protocol**.

The basic idea is:

```text
┌──────────────────┐
│   Client App     │
│                  │
│ Web / Mobile /   │
│ Desktop / Tool   │
└────────┬─────────┘
         │
         │ HTTP Request
         ▼
┌──────────────────┐
│     Web API      │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Backend System   │
│                  │
│ Logic / Database │
└────────┬─────────┘
         │
         │ HTTP Response
         ▼
┌──────────────────┐
│   Client App     │
│                  │
│ Web / Mobile /   │
│ Desktop / Tool   │
└──────────────────┘
````

---

## API vs Web API

**API** is the broader concept.

A **Web API** is an API that communicates through web technologies, commonly HTTP.

```text
API
│
├── Library APIs
├── Operating System APIs
├── Application APIs
└── Web APIs
     │
     └── Commonly uses HTTP
```

So:

> **Every Web API is an API, but not every API is a Web API.**

---

## Why "Web"?

The word **Web** refers to communication through web-based networking technologies.

A typical Web API interaction looks like:

```text
Client
   │
   │ HTTP Request
   ▼
Web API
   │
   │ Process
   ▼
Server
   │
   │ HTTP Response
   ▼
Client
```

The client and server do not have to be on the same computer.

They can communicate across a network or the Internet.

---

## A Simple Example

Suppose an application needs information about a user.

The application can communicate with a Web API:

```text
┌─────────────┐
│ Mobile App  │
└──────┬──────┘
       │
       │ HTTP Request
       ▼
┌─────────────┐
│   Web API   │
└──────┬──────┘
       │
       │ User Data
       ▼
┌─────────────┐
│   Backend   │
└─────────────┘
```

The API provides the communication interface.

The backend system handles the underlying processing and data.

---

## Web API and HTTP

For this course, one relationship is especially important:

```text
Web API
   │
   │ commonly communicates using
   ▼
 HTTP
```

HTTP provides the communication mechanism.

The Web API defines how the application uses that communication to interact with a service.

Later, we will examine HTTP requests and responses in detail.

---

## Web API vs Website

A website and a Web API can exist within the same overall system, but they serve different purposes.

### Website

```text
Human
  │
  ▼
Browser
  │
  ▼
Website
```

The interface is primarily designed for humans.

### Web API

```text
Software
   │
   ▼
Web API
   │
   ▼
Software / Service
```

The interface is primarily designed for programmatic interaction.

---

## Web API in Practice

A Web API can be used by many types of clients:

```text
                 ┌── Web Application
                 │
                 ├── Mobile Application
                 │
                 ├── Desktop Application
                 │
                 └── Postman
                         │
                         ▼
                    ┌─────────┐
                    │ Web API │
                    └─────────┘
```

The client sends a request, and the Web API returns a response.

---

## Web API and REST

REST is one architectural style commonly used to build Web APIs.

```text
Web API
│
├── REST API
├── GraphQL API
├── SOAP API
└── Other approaches
```

Therefore:

```text
API
 ↓
Web API
 ↓
REST API
```

These terms are related, but they are **not interchangeable**.

---

## Key Takeaways

```text
Web API
│
├── Is a type of API
├── Enables software communication over a network
├── Commonly uses HTTP
├── Can be consumed by different types of clients
└── Is often used to expose backend functionality/data
```

Remember:

> **A Web API is an API that enables software systems to communicate over web technologies, commonly through HTTP.**

