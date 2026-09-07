# Reading a Simple API Interaction

Before working with complex API requests, you should be able to **read a simple API interaction** and understand what happened.

The goal is not to memorize syntax.

The goal is to answer:

> **What did the client ask for, and what did the server return?**

---

## 01 — Start With the Interaction

A simple API interaction can be represented like this:

```text
CLIENT
  │
  │ Request
  ▼
API / SERVER
  │
  │ Response
  ▼
CLIENT
````

There are two main things to read:

```text
REQUEST
   ↓
What did the client ask for?

RESPONSE
   ↓
What did the server return?
```

---

## 02 — Example Request

Consider this API request:

```text
GET https://jsonplaceholder.typicode.com/posts/1
```

Read it from left to right:

```text
GET
 ↓
What operation?

https://jsonplaceholder.typicode.com
 ↓
Which API?

/posts/1
 ↓
Which resource?
```

So we can describe the interaction as:

> The client asks the API to retrieve post `1`.

At this stage, you do not need to know every detail of HTTP syntax.

Focus on the meaning.

---

## 03 — Example Response

The API may return:

```json
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  "body": "quia et suscipit..."
}
```

Read the response as **data**.

```text
Response
   │
   ├── userId
   ├── id
   ├── title
   └── body
```

The client can now use this information.

For example:

```text
API Response
     ↓
Client receives JSON
     ↓
Application reads "title"
     ↓
Application displays the title
```

---

## 04 — Read Request and Response Together

Instead of looking at them separately:

```text
REQUEST
GET /posts/1
```

and:

```text
RESPONSE
{
  "id": 1,
  ...
}
```

connect them:

```text
┌─────────────────────────────┐
│          CLIENT             │
└─────────────┬───────────────┘
              │
              │ GET /posts/1
              ▼
┌─────────────────────────────┐
│            API              │
└─────────────┬───────────────┘
              │
              │ Find post #1
              ▼
┌─────────────────────────────┐
│           DATA              │
│           Post #1            │
└─────────────┬───────────────┘
              │
              │ JSON response
              ▼
┌─────────────────────────────┐
│          CLIENT             │
│                             │
│  Receives post information  │
└─────────────────────────────┘
```

The response makes sense because we know what the request asked for.

---

## 05 — Ask Four Questions

When reading a simple API interaction, ask:

### 1. Who?

Who initiated the interaction?

```text
Client
```

### 2. What?

What did the client request?

```text
GET /posts/1
```

### 3. What came back?

What data did the API return?

```json
{
  "id": 1,
  "title": "..."
}
```

### 4. What can the client do with it?

For example:

```text
Display
Store
Process
Use in another operation
```

This gives you a basic interpretation of the interaction.

---

## 06 — A Second Example

Consider:

```text
GET https://jsonplaceholder.typicode.com/posts?userId=1
```

The request asks for:

```text
posts
   │
   └── belonging to user 1
```

The API may return multiple objects:

```json
[
  {
    "userId": 1,
    "id": 1,
    "title": "...",
    "body": "..."
  },
  {
    "userId": 1,
    "id": 2,
    "title": "...",
    "body": "..."
  }
]
```

Notice the difference:

```text
/posts/1
     ↓
One specific resource

/posts?userId=1
     ↓
A collection filtered by user
```

You are already beginning to **interpret API behavior**, not just read syntax.

---

## 07 — Think in Meaning, Not Just Syntax

A beginner may see:

```text
GET /posts/1
```

and think:

> "This is some API syntax."

Instead, learn to translate it:

```text
GET /posts/1
      ↓
Retrieve
      ↓
the post resource
      ↓
with identifier 1
```

The same approach works with responses.

Instead of seeing:

```json
{
  "id": 1,
  "title": "..."
}
```

think:

```text
The API returned
a resource
with an identifier
and associated data.
```

---

## 08 — The Reading Pattern

Use this pattern whenever you inspect an API:

```text
REQUEST
   ↓
What is being requested?
   ↓
RESOURCE
   ↓
What is the API working with?
   ↓
RESPONSE
   ↓
What did the API return?
   ↓
RESULT
   ↓
What can the client do with it?
```

This is the foundation for reading real APIs later.

---

## 09 — What You Should Be Able to Explain

After reading a simple interaction, you should be able to say something like:

> "The client requested post 1 from the API. The API processed the request and returned the post data as JSON. The client can then use that data in the application."

You do **not** need to know the server's internal implementation to understand this interaction.

```text
Client
  │
  │ "Give me this resource."
  ▼
API
  │
  │ "Here is the result."
  ▼
Client
```

---

## Key Takeaways

* Read an API interaction as a **conversation**.
* Start with the **request**.
* Determine what resource or information is being requested.
* Then inspect the **response**.
* Interpret the returned data based on the request.
* Focus on **meaning**, not just syntax.
* The client does not need to know the server's internal implementation.

> **Reading an API means connecting the request to the response and understanding what happened.**

### Next Lesson

**Assignment — Practical API Interaction**

