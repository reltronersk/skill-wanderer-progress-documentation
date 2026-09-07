# Client, Server, and API Communication

A Web API usually connects a **client** with a **server**.

The basic relationship is:

```text
┌──────────────┐
│    Client    │
│              │
│ Web App      │
│ Mobile App   │
│ Postman      │
└──────┬───────┘
       │
       │ Request
       ▼
┌──────────────┐
│   Web API    │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│    Server    │
│              │
│ Logic / Data │
└──────┬───────┘
       │
       │ Response
       ▼
     Client
````

---

## 1. What Is a Client?

A **client** is the software that initiates communication with a server.

Examples:

```text
┌─────────────────────┐
│       Clients       │
├─────────────────────┤
│ Web Application     │
│ Mobile Application  │
│ Desktop Application │
│ Postman             │
└─────────────────────┘
```

The client may request data, submit data, or ask the server to perform an operation.

---

## 2. What Is a Server?

A **server** is a system that receives requests and provides a response or performs an operation.

Conceptually:

```text
Client
  │
  │ Request
  ▼
Server
  │
  │ Process
  ▼
Response
```

A server may contain:

```text
┌─────────────────────┐
│       Server        │
├─────────────────────┤
│ API                 │
│ Business Logic      │
│ Database            │
│ Other Services      │
└─────────────────────┘
```

The internal implementation can be much more complex, but the client does not necessarily need to know those details.

---

## 3. Where Does the API Fit?

The API provides the **interface** through which the client communicates with the server.

```text
┌────────────┐
│   Client   │
└─────┬──────┘
      │
      │ Request
      ▼
┌────────────┐
│    API     │
└─────┬──────┘
      │
      ▼
┌────────────┐
│   Server   │
└────────────┘
```

A useful mental model is:

> **Client uses the API to communicate with the server.**

---

## 4. The Communication Flow

A typical API interaction follows this pattern:

```text
      Request
Client ───────────────► Server
  ▲                       │
  │                       │
  │       Processing      │
  │                       │
  └───────────────────────┘
             Response
```

More explicitly:

```text
1. Client needs something
          ↓
2. Client sends a request
          ↓
3. API receives the request
          ↓
4. Server processes it
          ↓
5. Server produces a response
          ↓
6. Client receives the response
```

---

## 5. Example

Imagine a mobile application requesting a user's profile.

```text
┌──────────────┐
│  Mobile App  │
└──────┬───────┘
       │
       │ "Give me user 123"
       ▼
┌──────────────┐
│   User API   │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│    Server    │
│              │
│ Find user    │
│ 123          │
└──────┬───────┘
       │
       │ User data
       ▼
┌──────────────┐
│   User API   │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│  Mobile App  │
└──────────────┘
```

The mobile application does not need direct access to the server's database.

It communicates through the API.

---

## 6. The Restaurant Analogy

The restaurant analogy from the previous lesson can be extended to client-server communication:

```text
Customer
   │
   │ Order
   ▼
Waiter
   │
   │ Request
   ▼
Kitchen
   │
   │ Result
   ▼
Waiter
   │
   │ Response
   ▼
Customer
```

Mapping it to software:

```text
Customer  → Client
Waiter    → API
Kitchen   → Server
Order     → Request
Food      → Response
```

The waiter provides the communication interface between the customer and the kitchen.

Similarly, an API provides an interface between a client and a server system.

---

## 7. Client and Server Are Roles

"Client" and "server" describe **roles in a communication**.

```text
Client
  │
  │ Request
  ▼
Server
  │
  │ Response
  ▼
Client
```

The same software system can sometimes act as a client in one interaction and a server in another.

For example:

```text
Application A
      │
      │ Request
      ▼
Application B
```

Here:

```text
Application A = Client
Application B = Server
```

If Application B communicates with another service:

```text
Application B
      │
      │ Request
      ▼
Application C
```

then Application B is also acting as a **client** in that interaction.

---

## 8. Client → API → Server

For this course, remember this simplified model:

```text
┌──────────────┐
│    Client    │
└──────┬───────┘
       │
       │ Request
       ▼
┌──────────────┐
│     API      │
└──────┬───────┘
       │
       │
       ▼
┌──────────────┐
│    Server    │
└──────┬───────┘
       │
       │ Response
       ▼
┌──────────────┐
│    Client    │
└──────────────┘
```

Later, we will make this model more precise by introducing **HTTP requests and HTTP responses**.

---

## Key Takeaways

```text
Client
  │
  │ Request
  ▼
 API
  │
  ▼
Server
  │
  │ Response
  ▼
Client
```

* **Client** initiates the interaction.
* **Server** receives and processes the request.
* **API** provides the interface for communication.
* The client does not need to know the server's internal implementation.
* Communication commonly follows a **request → processing → response** pattern.

> **A client uses an API to communicate with a server and receive the result of that interaction.**


