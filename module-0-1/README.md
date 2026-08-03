# Your First Look at a RESTful API

> **Module 0-1 — Discovering RESTful APIs Through Testing**

## Lesson Overview

When you open a course page, the browser may need to ask a server for information such as:

- The course title
- The course description
- The lesson list
- Your learning progress

This communication can happen through an API.

In this lesson, you will learn the basic RESTful API pattern by looking at requests and responses. Later, you will observe this pattern with Chrome DevTools and test it directly with Postman.

No programming or API experience is required.

---

## What You Will Learn

By the end of this lesson, you should be able to:

- Explain what an API does.
- Recognize a client, request, server, and response.
- Recognize an endpoint and HTTP method.
- Understand the basic meaning of a status code.
- Recognize JSON data returned by an API.
- Explain how Chrome DevTools and Postman help us test APIs.
- Read the most important parts of a basic Postman result.

---

## 1. A Simple Website Example

Imagine that you open a course page.

The page shows:

- A course title
- A description
- A difficulty level
- A list of lessons

The browser may ask a server:

> Please send me the course information.

The server may answer:

> Here is the course information.

The browser then displays the returned information on the page.

---

## 2. The Basic API Flow

```mermaid
flowchart LR
    A[Browser or Postman] -->|Request| B[RESTful API]
    B --> C[Server processes the request]
    C -->|Response| A
```

If Mermaid is not supported, use this text version:

```text
Client
  |
  | Request
  v
RESTful API / Server
  |
  | Response
  v
Client
```

The basic pattern is:

```text
Client sends a request.
Server processes the request.
Server returns a response.
```

---

## 3. Important Beginner Terms

| Term | Simple meaning |
|---|---|
| Client | An application that sends a request |
| Server | A system that receives and processes the request |
| Request | A message asking the server for data or an action |
| Response | The result returned by the server |
| API | A structured way for applications to communicate |
| RESTful API | An API that works with resources through HTTP |
| Resource | Something managed by the API, such as a course or lesson |
| Endpoint | The address used to access an API resource |
| HTTP method | The type of action requested |
| Status code | A number that describes the request result |
| Header | Extra information attached to a request or response |
| Response body | The main data returned by the server |

Examples of clients:

- A web browser
- A mobile application
- Postman
- Another software system

---

## 4. What Is an API?

API stands for **Application Programming Interface**.

For now, use this simple definition:

> An API is a structured way for one application to request data or actions from another system.

Example:

```text
Browser:
Give me the available courses.

API:
Here is the course list.
```

Postman can also send a request:

```text
Postman:
Give me information about one course.

API:
Here is the requested course.
```

The browser and Postman are clients.

They are not the API.

---

## 5. What Is a RESTful API?

A RESTful API organizes communication around resources.

Resources in a learning platform may include:

- Courses
- Lessons
- Users
- Submissions
- Quiz results

A client can use HTTP requests to retrieve or change these resources.

For this lesson, remember:

> A RESTful API is a structured way to access and manage resources through HTTP.

You will learn the detailed REST rules later.

---

## 6. Your First RESTful API Request

Consider this request:

```http
GET /api/courses/restful-api-mastery-greybox
```

This request has two important parts.

### HTTP Method

```text
GET
```

`GET` commonly means:

> Retrieve information.

### Endpoint

```text
/api/courses/restful-api-mastery-greybox
```

The endpoint tells us where the request is sent.

In simple language, the complete request means:

> Retrieve information about the RESTful API Mastery course.

---

## 7. Your First RESTful API Response

The server may return this status:

```text
200 OK
```

This usually means:

> The request succeeded.

The server may also return this response body:

```json
{
  "slug": "restful-api-mastery-greybox",
  "title": "RESTful API Mastery: The Greybox Approach",
  "level": "beginner"
}
```

This response uses JSON.

> JSON is a common text format used by APIs to organize data using names and values.

In this example:

- `slug` is the course identifier.
- `title` is the course title.
- `level` is the difficulty level.

---

## 8. Complete Request and Response Diagram

```mermaid
sequenceDiagram
    participant Client as Browser or Postman
    participant API as RESTful API

    Client->>API: GET /api/courses/restful-api-mastery-greybox
    API-->>Client: 200 OK
    API-->>Client: Course data in JSON
```

Text version:

```text
Browser or Postman
        |
        | GET /api/courses/restful-api-mastery-greybox
        v
RESTful API
        |
        | 200 OK
        | Course data in JSON
        v
Browser or Postman
```

From this test result, we can identify:

| Evidence | Result |
|---|---|
| Client | Browser or Postman |
| Method | GET |
| Endpoint | `/api/courses/restful-api-mastery-greybox` |
| Status | `200 OK` |
| Response | Course data in JSON |

A useful pattern to remember is:

```text
Method + Endpoint + Status + Response
```

---

## 9. Successful and Unsuccessful Results

### Successful Request

```text
GET /api/courses/restful-api-mastery-greybox
200 OK
```

Meaning:

> The server found the requested course and processed the request successfully.

### Resource Not Found

```text
GET /api/courses/course-that-does-not-exist
404 Not Found
```

Meaning:

> The server could not find the requested resource.

A `404 Not Found` result does not automatically mean that the entire server is broken.

It only tells us that this request did not find the requested endpoint or resource.

---

## 10. How Chrome DevTools Helps

Chrome DevTools can show API requests made by the browser.

For example:

```mermaid
flowchart TD
    A[User opens a course page] --> B[Browser sends an API request]
    B --> C[Server returns course data]
    C --> D[Browser displays the course]
```

With Chrome DevTools, you can inspect:

- The request URL
- The HTTP method
- The status code
- The request headers
- The response headers
- The returned data

The main question is:

> What API request did the browser send?

You will practice this in the next lesson.

---

## 11. How Postman Helps

Postman allows you to send an API request directly.

A simple Postman test may use:

```text
Method:
GET

URL:
https://example.com/api/courses

Action:
Send
```

Postman can display:

- The status code
- The response headers
- The response body
- The response time

The main question is:

> What happens when I send this request directly to the API?

---

## 12. Reading a Real Postman Result

The screenshot below shows a real request sent with Postman.

> Place the image in your repository at:
>
> `public/images/courses/restful-api-mastery/postman-404-response-example.png`

![Postman GET request returning 404 Not Found](postman-404-response-example.png)

### What Happened in This Test?

Postman sent this request:

```http
GET https://httpbin.org/status/404
```

The server returned:

```text
404 Not Found
```

This is an intentionally unsuccessful request. The endpoint is designed to return status code `404`.

The screenshot highlights four important parts.

### 1. Request Method

The request method is:

```text
GET
```

`GET` tells the server that the client wants to retrieve something.

In Postman, the method appears in the dropdown to the left of the request URL.

### 2. Request URL

The request URL is:

```text
https://httpbin.org/status/404
```

The URL tells Postman where to send the request.

In this example:

- `https://httpbin.org` is the server address.
- `/status/404` is the endpoint path.
- The endpoint is designed to return `404 Not Found`.

### 3. Status Code

The returned status is:

```text
404 Not Found
```

This tells us that the request reached the server, but the server returned a not-found result.

This is important:

> A `404` response is still a valid HTTP response.

The server did not remain silent. It processed the request and deliberately returned a result that describes what happened.

### 4. Response Headers

The lower part of the screenshot shows response headers.

Response headers provide additional information about the server response.

Examples visible in the screenshot include:

| Header | Example value | Simple meaning |
|---|---|---|
| `:status` | `404` | The numeric HTTP status |
| `date` | A GMT date and time | When the response was generated |
| `content-type` | `text/html; charset=utf-8` | The format of the returned content |
| `content-length` | `0` | The response body contains zero bytes |
| `server` | `gunicorn/19.9.0` | Information about the server software |
| `access-control-allow-origin` | `*` | Cross-origin requests are allowed from any origin |
| `access-control-allow-credentials` | `true` | The server allows credentials in supported cross-origin requests |

You do not need to memorize all these headers yet.

For now, understand that headers are extra information attached to the response.

### Why Is the Response Body Empty?

The screenshot shows:

```text
content-length: 0
```

This means the response does not contain a response body.

The server returned only the status and headers.

That is valid. Not every API response must contain JSON or visible text.

### Beginner Interpretation

From this Postman result, we can say:

```text
Client:
Postman

Method:
GET

Request URL:
https://httpbin.org/status/404

Status:
404 Not Found

Response body:
Empty

Response headers:
Present
```

The result can be summarized as:

> Postman sent a GET request to an endpoint that intentionally returns `404 Not Found`. The server responded with a valid HTTP response containing a status code and response headers, but no response body.

---

## 13. Chrome DevTools and Postman

| Chrome DevTools | Postman |
|---|---|
| Observes requests made by the browser | Sends requests directly |
| Starts from a website action | Starts from a request created by the tester |
| Helps explain application behavior | Helps test the API without the website interface |

Both tools help us understand the same RESTful API interaction from different starting points.

---

## 14. Common Beginner Misunderstandings

### “The website is the API”

The website interface may use an API, but the interface and API are not the same thing.

### “Postman is the API”

Postman is a client used to send requests to an API.

### “Chrome DevTools creates the API request”

Chrome DevTools mainly helps you observe requests made by the browser.

### “A 404 response means no response was returned”

This is incorrect.

`404 Not Found` is a response returned by the server.

The server processed the request and reported that the requested resource was not found.

### “Every response must contain JSON”

A response may contain:

- JSON
- HTML
- Plain text
- A file
- An image
- No response body

### “Every failed request means the server is broken”

A request may fail because of:

- An incorrect endpoint
- A missing resource
- Invalid input
- Missing authentication
- Missing permission
- A server problem

Always inspect the method, endpoint, status code, headers, and response body.

### “RESTful API means JSON”

JSON is commonly used by RESTful APIs, but RESTful APIs are not defined only by JSON.

---

## 15. Lesson Summary

A RESTful API allows a client to communicate with a server through HTTP requests and responses.

The client sends:

- An HTTP method
- An endpoint
- Optional headers or data

The server returns:

- A status code
- Response headers
- Optional response data

Chrome DevTools helps you observe requests made by a browser.

Postman helps you send requests directly.

The Postman example demonstrated that:

- `GET` was the request method.
- `https://httpbin.org/status/404` was the request URL.
- `404 Not Found` was the returned status.
- Response headers provided additional information.
- The response body was empty.
- An unsuccessful result can still be a valid HTTP response.

The basic evidence pattern is:

```text
Method + Endpoint + Status + Headers + Response Body
```

---

## Next Lesson

In the next lesson, you will use Chrome DevTools to observe a RESTful API request inside a real web application.

You will identify:

- The request URL
- The HTTP method
- The status code
- The response headers
- The returned response data
