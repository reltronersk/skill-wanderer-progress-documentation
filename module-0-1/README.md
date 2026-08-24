# Lesson 0.1 --- Understand the Visible Shape of HTTP

> **Module 0 --- Discovering RESTful APIs Through Testing**

## Lesson Overview

You may already understand RESTful APIs from theory.

You may know terms such as:

-   HTTP method
-   Endpoint
-   Request
-   Response
-   Status code
-   Headers
-   JSON

But when these concepts appear in a real API test, they can look very
different from the diagrams in a textbook.

For example, you may see:

``` text
GET /api/courses
200 OK
```

At first, this can look like one mysterious piece of information.

It is not.

It is a short summary of information that belongs to an HTTP request and
response.

This lesson helps you connect the theory you already know with the
**visible shape of HTTP communication**.

------------------------------------------------------------------------

## Learning Objectives

By the end of this lesson, you should be able to:

-   Recognize the two sides of HTTP communication: request and response.
-   Identify the method and URL in a request.
-   Identify the status code in a response.
-   Understand that headers and body are separate parts of HTTP
    communication.
-   Translate a short form such as `GET /api/courses → 200 OK` into its
    actual structure.

------------------------------------------------------------------------

## 1. REST Theory Meets Real HTTP

A RESTful API communicates through HTTP.

The basic communication looks like this:

``` mermaid
flowchart LR
    A["Client"] -->|"HTTP Request"| B["API / Server"]
    B -->|"HTTP Response"| A
```

The client could be:

-   A web browser
-   A mobile application
-   Postman
-   Another application

The important idea is:

> A client sends a request, and the server returns a response.

------------------------------------------------------------------------

## 2. What Is Inside the Request?

An HTTP request can contain several pieces of information.

``` mermaid
flowchart TB
    A["HTTP Request"]

    A --> B["Method"]
    A --> C["URL"]
    A --> D["Headers"]
    A --> E["Body"]
```

For example:

``` text
Method:
GET

URL:
https://example.com/api/courses
```

The method tells the server what kind of operation the client is
requesting.

The URL tells the client where to send the request.

For this lesson, focus on the simplest case:

``` text
GET
+
URL
```

------------------------------------------------------------------------

## 3. What Is Inside the Response?

The server sends an HTTP response back to the client.

``` mermaid
flowchart TB
    A["HTTP Response"]

    A --> B["Status Code"]
    A --> C["Headers"]
    A --> D["Body"]
```

For example:

``` text
Status:
200 OK
```

The response may also contain headers and a response body.

A JSON response body could look like:

``` json
{
  "title": "RESTful API Mastery",
  "level": "beginner"
}
```

------------------------------------------------------------------------

## 4. The Short Form Can Be Misleading

A beginner may see:

``` text
GET /api/courses
200 OK
```

and think:

> "What is this? Is this the API?"

No.

This is a compact way of describing parts of an HTTP exchange.

Let's expand it.

``` mermaid
flowchart TB
    A["GET /api/courses"] --> B["Request"]
    B --> C["GET"]
    B --> D["/api/courses"]

    E["200 OK"] --> F["Response"]
    F --> G["200"]
    F --> H["OK"]
```

Now we can read it as:

``` text
GET
↓
HTTP Method

/api/courses
↓
Request Path / Endpoint

200 OK
↓
Response Status
```

------------------------------------------------------------------------

## 5. See the Complete Picture

The short form:

``` text
GET /api/courses
200 OK
```

can represent a much larger exchange:

``` mermaid
sequenceDiagram
    participant Client
    participant API as API / Server

    Client->>API: GET /api/courses
    Note right of API: HTTP Request
    API-->>Client: 200 OK
    Note right of API: HTTP Response
```

A more detailed mental model is:

``` text
REQUEST
│
├── Method
│   └── GET
│
├── URL
│   └── https://example.com/api/courses
│
├── Headers
│
└── Body
    └── Optional


            ↓


RESPONSE
│
├── Status
│   └── 200 OK
│
├── Headers
│
└── Body
    └── JSON data
```

The exact information shown depends on the request.

A `GET` request, for example, commonly has no request body.

------------------------------------------------------------------------

## 6. Why Do Tools Show It Differently?

When you use a real HTTP tool, you usually do not see everything as one
line.

Instead, the tool separates the information.

For example:

``` text
REQUEST

Method:
GET

URL:
https://example.com/api/courses


RESPONSE

Status:
200 OK

Headers:
...

Body:
{
  "title": "RESTful API Mastery",
  "level": "beginner"
}
```

This is easier to inspect because each part has a specific place.

Later, Chrome DevTools and Postman will show these pieces visually.

------------------------------------------------------------------------

## 7. Request → Server → Response

The most important visual model in this lesson is:

``` mermaid
flowchart LR
    A["Client"] -->|"1. Request"| B["API / Server"]
    B -->|"2. Response"| A
```

The request can contain:

``` text
Method
URL
Headers
Body
```

The response can contain:

``` text
Status
Headers
Body
```

So:

``` text
Client
  |
  | Method + URL + ...
  v
API / Server
  |
  | Status + Headers + Body
  v
Client
```

------------------------------------------------------------------------

## 8. What Does `200 OK` Actually Mean?

Consider:

``` text
GET /api/courses
200 OK
```

The first line describes the request:

``` text
GET
/api/courses
```

The second line describes the result:

``` text
200 OK
```

`200 OK` means the server successfully processed the request.

It does not mean:

> "200 is part of the URL."

It is a **response status**.

------------------------------------------------------------------------

## 9. What About `404 Not Found`?

A different request may produce:

``` text
GET /api/courses/does-not-exist
404 Not Found
```

The structure is still the same:

``` mermaid
flowchart LR
    A["Client"] -->|"GET /api/courses/does-not-exist"| B["API / Server"]
    B -->|"404 Not Found"| A
```

The important difference is the response status.

``` text
200 OK
↓
Request succeeded

404 Not Found
↓
Requested resource could not be found
```

A `404` does not mean that there was no response.

The server responded with:

> `404 Not Found`

------------------------------------------------------------------------

## 10. From Theory to What You Will See

You already know these concepts:

``` text
HTTP Method
Endpoint
Request
Response
Status Code
Headers
Body
```

Now connect them to what a real tool will show:

``` mermaid
flowchart TB
    A["REST API theory"]

    A --> B["HTTP Request"]
    A --> C["HTTP Response"]

    B --> D["Method"]
    B --> E["URL / Endpoint"]
    B --> F["Request Headers"]
    B --> G["Request Body"]

    C --> H["Status Code"]
    C --> I["Response Headers"]
    C --> J["Response Body"]
```

This is the bridge between **knowing the terms** and **recognizing them
in a real tool**.

------------------------------------------------------------------------

## 11. Mini Exercise

Look at this:

``` text
GET /api/lessons/123
200 OK
```

Try to identify:

1.  What is the HTTP method?
2.  What is the endpoint?
3.  What is the response status?
4.  Which part belongs to the request?
5.  Which part belongs to the response?

```{=html}
<details>
```
```{=html}
<summary>
```
Show answer
```{=html}
</summary>
```
``` text
GET
↓
HTTP method

/api/lessons/123
↓
Request path / endpoint

200 OK
↓
Response status
```

So:

``` text
REQUEST
├── Method: GET
└── Endpoint: /api/lessons/123

RESPONSE
└── Status: 200 OK
```

```{=html}
</details>
```

------------------------------------------------------------------------

## 12. Lesson Takeaway

Do not try to memorize:

``` text
GET /api/courses 200 OK
```

as one special API format.

Instead, break it apart:

``` text
GET
↓
Method

/api/courses
↓
Endpoint

200 OK
↓
Response status
```

And remember the larger structure:

``` text
REQUEST
    ↓
API / SERVER
    ↓
RESPONSE
```

With:

``` text
REQUEST
├── Method
├── URL
├── Headers
└── Body

RESPONSE
├── Status
├── Headers
└── Body
```

This is the **visible shape of HTTP** that you will start seeing in real
tools.

------------------------------------------------------------------------

## Next Lesson

In **Lesson 0.2 --- Discovering API Requests with Chrome DevTools**, you
will move from this mental model to a real website.

You will see:

``` text
Website
   ↓
Browser makes request
   ↓
Chrome DevTools records it
   ↓
You find the request
   ↓
You open its details
```

The goal is not to learn Chrome DevTools for its own sake.

The goal is to use DevTools to **find the RESTful API communication
happening behind a website**.
