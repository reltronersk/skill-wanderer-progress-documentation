# How an API Interaction Works

An API interaction is a conversation between a **client** and a **server** through an API.

The client asks for something.

The server processes the request.

The server sends back a response.

---

## 01 — The Basic Flow

The simplest API interaction looks like this:

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
     │ Process
     ▼
┌──────────┐
│  Server  │
└────┬─────┘
     │
     │ Response
     ▼
┌──────────┐
│  Client  │
└──────────┘
````

The important idea:

> **Request → Processing → Response**

---

## 02 — The Client Starts the Interaction

The **client** is the software that wants to use the API.

Examples:

* Web browser
* Mobile application
* Postman
* Another backend service
* CLI tools

For example, a client may want to retrieve a post:

```text
Client
  │
  │ "Give me post #1"
  ▼
API
```

The client initiates the interaction.

---

## 03 — The API Receives the Request

The API acts as the interface through which the client communicates with the server.

```text
Client
   │
   │ Request
   ▼
┌───────────────┐
│      API      │
│               │
│ "What does    │
│  the client   │
│  want?"       │
└───────┬───────┘
        │
        ▼
      Server
```

The API determines how the request should be handled.

---

## 04 — The Server Processes the Request

The server contains the application logic and data needed to handle the request.

For example:

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
   ├── Validate request
   ├── Find data
   ├── Process logic
   └── Prepare result
   │
   ▼
Response
```

The exact processing depends on the API and the request.

---

## 05 — The Server Sends a Response

After processing the request, the server returns a response.

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

The response may contain:

* Requested data
* A success result
* An error
* Metadata
* Information about what happened

For example:

```json
{
  "id": 1,
  "title": "Example Post",
  "userId": 1
}
```

---

## 06 — A Complete Interaction

Putting everything together:

```text
┌──────────┐
│  Client  │
└────┬─────┘
     │
     │ 1. Request
     │    "Get post #1"
     ▼
┌──────────┐
│   API    │
└────┬─────┘
     │
     │ 2. Forward / handle
     ▼
┌──────────┐
│  Server  │
└────┬─────┘
     │
     │ 3. Process
     │
     │ 4. Response
     ▼
┌──────────┐
│  Client  │
└──────────┘
```

This interaction happens very quickly.

From the user's perspective, it may look like:

```text
Click button
     ↓
Data appears
```

But underneath:

```text
User action
     ↓
Client
     ↓
API request
     ↓
Server processing
     ↓
API response
     ↓
Client updates interface
```

---

## 07 — Example: Getting a Post

Imagine a client wants post `1`.

```text
Client
   │
   │ GET /posts/1
   ▼
API
   │
   ▼
Server
   │
   │ Find post #1
   ▼
Database / Data
   │
   ▼
Server
   │
   │ JSON response
   ▼
Client
```

The client receives data such as:

```json
{
  "id": 1,
  "title": "Example Post"
}
```

The client can then use that data.

For example:

```text
API Response
     ↓
Client receives JSON
     ↓
Application reads the data
     ↓
User sees the result
```

---

## 08 — Request and Response Are Different

An API interaction has two directions:

```text
CLIENT                         SERVER
  │                              │
  │─────── Request ─────────────>│
  │                              │
  │                              │ Process
  │                              │
  │<────── Response ─────────────│
  │                              │
```

### Request

The client tells the server what it wants.

### Response

The server tells the client what happened.

This distinction is fundamental when working with APIs.

---

## 09 — The Client Does Not "See" the Server

The client normally does not directly access the server's internal implementation.

Instead:

```text
┌──────────┐
│  Client  │
└────┬─────┘
     │
     │ API interaction
     ▼
┌──────────┐
│   API    │
└────┬─────┘
     │
     ▼
┌──────────┐
│  Server  │
└──────────┘
```

The client interacts with the **API interface**.

The server decides how to process that interaction internally.

This separation allows clients and servers to evolve independently.

---

## 10 — The Mental Model

When you encounter an API, think:

```text
WHO?
  ↓
Client

WHAT?
  ↓
Request

WHERE?
  ↓
API

HOW?
  ↓
Server processes it

WHAT COMES BACK?
  ↓
Response
```

A useful simplified model is:

```text
        REQUEST
Client ───────────► API / Server
Client ◄─────────── API / Server
        RESPONSE
```

---

## Key Takeaways

* The **client** initiates an API interaction.
* The client sends a **request**.
* The API provides the interface for communication.
* The **server** processes the request.
* The server returns a **response**.
* The client consumes the response.
* An API interaction is fundamentally a **request → processing → response** cycle.

> **Think of an API as a communication interface: the client asks, the server processes, and the client receives the result.**

### Next Lesson

**Reading a Simple API Interaction**
