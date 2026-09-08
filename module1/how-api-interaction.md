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

After processing the request, the server returns a response to the client.

A real API response contains more than just the data.

For example, a client might send:

```http
GET /posts/1 HTTP/1.1
Host: jsonplaceholder.typicode.com
Accept: application/json
```

The server processes the request and sends something like:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
}
```

There are several important parts here:

```text
HTTP/1.1 200 OK
       │
       └── Status: the request succeeded

Content-Type: application/json
       │
       └── Response format

{
  "userId": 1,
  "id": 1,
  ...
}
       │
       └── Actual response data
```

The response therefore tells the client:

```text
Did the request work?
        ↓
      200 OK

What format is the result?
        ↓
   application/json

What data was returned?
        ↓
       JSON
```

A response may contain:

* Requested data
* A success result
* An error
* Metadata
* Information about what happened

The important point is that the **response communicates the result of the request back to the client**.

---

## 06 — A Complete Interaction

Putting everything together:

```text
┌──────────┐
│  Client  │
└────┬─────┘
     │
     │ 1. Request
     │
     │ GET /posts/1
     ▼
┌──────────┐
│   API    │
└────┬─────┘
     │
     │ 2. Handle request
     ▼
┌──────────┐
│  Server  │
└────┬─────┘
     │
     │ 3. Find post #1
     │
     │ 4. Prepare response
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

Let's look at the complete interaction more closely.

Imagine a client wants to retrieve **post #1** from JSONPlaceholder.

The client sends:

```http
GET /posts/1 HTTP/1.1
Host: jsonplaceholder.typicode.com
Accept: application/json
```

Think of this as:

```text
GET
 │
 └── "I want to retrieve something"

 /posts/1
 │
 └── "The resource I want is post #1"

 Accept: application/json
 │
 └── "I can receive JSON"
```

The request travels to the API:

```text
┌──────────────┐
│    Client    │
│   Postman    │
└──────┬───────┘
       │
       │ GET /posts/1
       ▼
┌──────────────────────────┐
│ JSONPlaceholder API      │
│                          │
│ /posts/1                 │
└────────────┬─────────────┘
             │
             │ Find post #1
             ▼
        ┌──────────┐
        │   Data   │
        └────┬─────┘
             │
             │ Post #1
             ▼
┌──────────────────────────┐
│ JSONPlaceholder API      │
└────────────┬─────────────┘
             │
             │ 200 OK
             │ JSON response
             ▼
┌──────────────┐
│    Client    │
│   Postman    │
└──────────────┘
```

The server returns:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
}
```

The client can then read the JSON:

```text
Response
   ↓
JSON data
   ↓
Client reads:
   ├── userId
   ├── id
   ├── title
   └── body
```

For example:

```text
id
↓
1

userId
↓
1

title
↓
"sunt aut facere..."

body
↓
"quia et suscipit..."
```

The client can then use this data in the application:

```text
API Response
     ↓
Client receives JSON
     ↓
Application reads the data
     ↓
Application uses the data
     ↓
User sees the result
```

The important idea is not the specific post.

The important idea is the **interaction pattern**:

```text
Client
   │
   │ GET /posts/1
   ▼
API
   │
   │ Find resource
   ▼
Server / Data
   │
   │ Result
   ▼
API
   │
   │ 200 OK + JSON
   ▼
Client
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

Example:

```http
GET /posts/1 HTTP/1.1
```

### Response

The server tells the client what happened.

Example:

```http
HTTP/1.1 200 OK
```

followed by the response data:

```json
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit"
}
```

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

For example, the client only needs to know:

```text
GET /posts/1
```

It does not need to know:

```text
How is the data stored?
Which database is used?
Which programming language is used?
Which internal function finds the post?
```

The server handles those details internally.

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
API / Endpoint

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

A more realistic view is:

```text
┌──────────┐
│  Client  │
└────┬─────┘
     │
     │ HTTP Request
     │ GET /posts/1
     ▼
┌──────────────┐
│     API      │
└────┬─────────┘
     │
     │ Process request
     ▼
┌──────────────┐
│    Server    │
│              │
│ Find data    │
│ Process      │
└────┬─────────┘
     │
     │ HTTP Response
     │ 200 OK + JSON
     ▼
┌──────────────┐
│    Client    │
└──────────────┘
```

---

## Key Takeaways

* The **client** initiates an API interaction.
* The client sends a **request**.
* The API provides the interface for communication.
* The **server** processes the request.
* The server returns a **response**.
* A response contains information about the result, often including data and a status code.
* JSON is a common format for API data.
* The client does not need to know the server's internal implementation.
* An API interaction is fundamentally a **request → processing → response** cycle.

> **Think of an API as a communication interface: the client asks, the server processes, and the client receives the result.**

### Next Lesson

**Reading a Simple API Interaction**

