# Assignment — Practical API Interaction

## Inspect an API Interaction with Postman

In this assignment, you will use **Postman** to inspect a real API interaction.

You will send requests, observe responses, and explain what happened.

You are not building an API.

You are learning to **use and read an existing API**.

---

## Requirements — Device & Tools

Before starting, make sure you have:

### Device

A computer or laptop with:

- Windows
- macOS
- or Linux

A mobile device is **not recommended** for this assignment because the exercises use the Postman desktop application.

### Required Tool

Install and open:

**Postman**

You will use Postman as the API client.

### Internet Connection

You need an active internet connection because the assignment communicates with a public API.

### Practice API

This assignment uses:

```text
JSONPlaceholder

https://jsonplaceholder.typicode.com/
````

No API key or account is required for this exercise.

### Important

You do **not** need:

* A local server
* A database
* A code editor
* Node.js
* Python
* A backend project

The assignment can be completed entirely with **Postman** and an internet connection.

---

# Objective

By completing this assignment, you should be able to:

* Send a simple API request with Postman.
* Identify the client and API.
* Identify what the request is asking for.
* Inspect the API response.
* Read basic JSON data.
* Compare different API requests.
* Explain the relationship between a request and its response.

---

# 01 — Open Postman

Open Postman on your computer.

Postman will act as the **client**.

```text
┌────────────┐
│   Postman  │
│   Client   │
└─────┬──────┘
      │
      │ API Request
      ▼
┌────────────┐
│JSONPlaceholder│
│     API    │
└─────┬──────┘
      │
      │ API Response
      ▼
┌────────────┐
│   Postman  │
│   Client   │
└────────────┘
```

---

# 02 — Create the First Request

Create a new request in Postman.

Set the method to:

```text
GET
```

Set the request URL to:

```text
https://jsonplaceholder.typicode.com/posts/1
```

Your request should look like:

```text
Method:
GET

URL:
https://jsonplaceholder.typicode.com/posts/1
```

Then click:

**Send**

---

# 03 — Observe the Response

After sending the request, look at the response section in Postman.

You should receive JSON similar to:

```json
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  "body": "quia et suscipit..."
}
```

Your exact response may contain more text in the `title` and `body` fields.

Do not worry about understanding every field yet.

Focus on identifying the returned data.

---

# 04 — Read the Interaction

Connect the request and response:

```text
REQUEST

GET
https://jsonplaceholder.typicode.com/posts/1

        ↓

API processes the request

        ↓

RESPONSE

{
  "userId": 1,
  "id": 1,
  "title": "...",
  "body": "..."
}
```

Ask:

> What did the client request?

The answer should be related to:

```text
Post #1
```

Then ask:

> What did the API return?

The answer should be related to:

```text
Data representing post #1
```

---

# 05 — Identify the Components

Inspect the interaction and identify:

| Component       | Your Answer |
| --------------- | ----------- |
| Client          | ?           |
| API             | ?           |
| HTTP method     | ?           |
| Resource        | ?           |
| Resource ID     | ?           |
| Response format | ?           |

Use what you can observe from the request and response.

---

# 06 — Inspect the JSON

Look at the response:

```json
{
  "userId": 1,
  "id": 1,
  "title": "...",
  "body": "..."
}
```

Identify:

```text
userId → ?

id     → ?

title  → ?

body   → ?
```

Then answer:

> Which field identifies the post?

> Which field contains the title?

> Which field contains the main content?

---

# 07 — Try a Second Request

Create another request:

```text
GET https://jsonplaceholder.typicode.com/posts?userId=1
```

Click:

**Send**

This time, the API returns multiple posts.

Conceptually:

```text
/posts/1
    ↓
One specific post

/posts?userId=1
    ↓
Posts associated with user 1
```

Compare the two interactions.

### First Request

```text
GET /posts/1
```

Expected concept:

```text
One specific resource
```

### Second Request

```text
GET /posts?userId=1
```

Expected concept:

```text
A collection of posts filtered by user
```

---

# 08 — Compare the Responses

Look at both responses in Postman.

Consider:

```text
First Request
      ↓
One JSON object

Second Request
      ↓
Multiple JSON objects
```

Observe how the structure of the response changes depending on what the client requested.

Do not simply copy the response.

Try to explain **why** the responses are different.

---

# 09 — Build the Interaction Model

Now represent the first interaction:

```text
┌────────────┐
│  Postman   │
│   Client   │
└─────┬──────┘
      │
      │ GET /posts/1
      ▼
┌────────────────┐
│ JSONPlaceholder│
│      API       │
└───────┬────────┘
        │
        │ Returns post #1
        ▼
┌────────────┐
│  Postman   │
│   Client   │
└────────────┘
```

Then represent the second interaction:

```text
┌────────────┐
│  Postman   │
│   Client   │
└─────┬──────┘
      │
      │ GET /posts?userId=1
      ▼
┌────────────────┐
│ JSONPlaceholder│
│      API       │
└───────┬────────┘
        │
        │ Returns matching posts
        ▼
┌────────────┐
│  Postman   │
│   Client   │
└────────────┘
```

---

# 10 — Form Your Own Explanation

Try to explain the interaction without looking at the lesson.

Use this pattern:

```text
Client
  ↓
Request
  ↓
API
  ↓
Processing
  ↓
Response
  ↓
Client
```

Your explanation should answer:

> What did the client ask for?

> How did the request identify the target data?

> What did the API return?

> How was the second request different?

---

# Assignment Submission

Submit your answers to the questions below.

Your submission should be based on **your own observation in Postman**.

Do not simply copy the lesson text.

---

## Part A — Environment

### 1. What device did you use?

Example:

```text
Windows laptop
```

Your answer:

```text
________________________________
```

### 2. What tool did you use as the API client?

```text
________________________________
```

### 3. Was an API key required for this assignment?

```text
________________________________
```

---

## Part B — First API Interaction

For this request:

```text
GET https://jsonplaceholder.typicode.com/posts/1
```

### 4. What is the client?

```text
________________________________
```

### 5. What API did the client communicate with?

```text
________________________________
```

### 6. What HTTP method did you use?

```text
________________________________
```

### 7. What resource was requested?

```text
________________________________
```

### 8. What identifies the specific resource?

```text
________________________________
```

### 9. What format was used for the response data?

```text
________________________________
```

---

## Part C — Reading the Response

Look at the response returned by Postman.

### 10. What is the value of `userId`?

```text
________________________________
```

### 11. What is the value of `id`?

```text
________________________________
```

### 12. What does the `title` field contain?

```text
________________________________
```

### 13. What does the `body` field contain?

```text
________________________________
```

### 14. In your own words, what does the response represent?

```text
________________________________

________________________________
```

---

## Part D — Second API Interaction

For this request:

```text
GET https://jsonplaceholder.typicode.com/posts?userId=1
```

### 15. What is different about this request compared with `/posts/1`?

```text
________________________________

________________________________
```

### 16. Does the response represent one post or multiple posts?

```text
________________________________
```

### 17. Why do you think the API returned that type of result?

```text
________________________________

________________________________
```

---

## Part E — Request → Response Reasoning

### 18. Complete the interaction:

```text
Postman
   ↓
________________________
   ↓
JSONPlaceholder API
   ↓
________________________
   ↓
Postman
```

### 19. Explain what happened during the first API interaction.

Write **2–4 sentences**.

```text
________________________________

________________________________

________________________________
```

### 20. Explain the difference between these two requests:

```text
GET /posts/1

GET /posts?userId=1
```

Write **2–4 sentences**.

```text
________________________________

________________________________

________________________________
```

---

# Submission Checklist

Before submitting, make sure you have:

* [ ] Opened Postman.
* [ ] Sent the first request.
* [ ] Observed the response.
* [ ] Identified the request components.
* [ ] Read the returned JSON.
* [ ] Sent the second request.
* [ ] Compared both responses.
* [ ] Answered all 20 questions.
* [ ] Written the explanations in your own words.

---

## What This Assignment Evaluates

Your submission should demonstrate that you can:

```text
USE
Postman
  ↓
OBSERVE
API interaction
  ↓
IDENTIFY
Request + Response
  ↓
INTERPRET
Returned data
  ↓
EXPLAIN
What happened
```

The goal is **not** to memorize the URL.

The goal is to demonstrate that you understand the interaction between a client and an API.

> **A good API user can explain what was requested, what was returned, and why the response makes sense.**

