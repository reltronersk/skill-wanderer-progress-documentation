# Assignment — Practical API Interaction

## Inspect an API Interaction with Postman

In this assignment, you will use **Postman** to inspect a real API interaction.

You will send a request, observe the response, and explain what happened.

You are not building an API.

You are learning to **use and read an existing API**.

---

## Objective

By completing this assignment, you should be able to:

- Send a simple API request with Postman.
- Identify the client and API.
- Identify what the request is asking for.
- Inspect the API response.
- Read basic JSON data.
- Explain the relationship between a request and its response.

---

## 01 — Open Postman

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
````

---

## 02 — Create a Request

Create a new HTTP request in Postman.

Set the method to:

```text
GET
```

Set the request URL to:

```text
https://jsonplaceholder.typicode.com/posts/1
```

Your request should look conceptually like:

```text
Method: GET

URL:
https://jsonplaceholder.typicode.com/posts/1
```

Then click:

**Send**

---

## 03 — Observe the Response

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

Do not worry about understanding every field yet.

Focus on identifying the returned data.

---

## 04 — Read the Interaction

Now connect the request and response:

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

Ask yourself:

> What did the client request?

Answer:

```text
The client requested post 1.
```

Then ask:

> What did the API return?

Answer:

```text
The API returned data representing post 1.
```

---

## 05 — Identify the Components

Inspect the interaction and identify:

| Component       | Your Answer |
| --------------- | ----------- |
| Client          | ?           |
| API             | ?           |
| Method          | ?           |
| Resource        | ?           |
| Resource ID     | ?           |
| Response format | ?           |

Use what you can observe from the request and response.

---

## 06 — Inspect the JSON

Look at the response again:

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

> Which field contains the main body/content?

---

## 07 — Try a Second Request

Create another request:

```text
GET https://jsonplaceholder.typicode.com/posts?userId=1
```

Click **Send**.

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

Compare the two responses.

### Request 1

```text
GET /posts/1
```

What did you receive?

```text
?
```

### Request 2

```text
GET /posts?userId=1
```

What did you receive?

```text
?
```

---

## 08 — Write Your Observation

Complete the following:

### Interaction 1

> Postman acted as the __________.

> The API was __________.

> I sent a __________ request.

> The requested resource was __________.

> The API returned __________.

### Interaction 2

> The second request was different because __________.

> The response contained __________.

---

## 09 — Final Check

Before completing the assignment, make sure you can explain this:

```text
Postman
   │
   │ GET /posts/1
   ▼
JSONPlaceholder API
   │
   │ JSON response
   ▼
Postman
```

In your own words:

> **What did the client ask for, and what did the API return?**

If you can clearly explain that interaction, you have completed the core objective of this assignment.

---

## Expected Outcome

You should finish with a basic ability to:

```text
Send Request
     ↓
Observe Response
     ↓
Read JSON
     ↓
Connect Request ↔ Response
     ↓
Explain What Happened
```

This is the foundation for working with APIs in later modules.

> **Your goal is not to memorize the request. Your goal is to understand the interaction.**


