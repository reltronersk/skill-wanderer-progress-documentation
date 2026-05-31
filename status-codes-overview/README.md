# HTTP Status Codes Overview

HTTP status codes are the server's way of answering a request.

When a client sends a request, the server responds with two things:

1. A status code
2. A response body (optional)

The status code immediately tells the client whether the request succeeded, failed, or requires additional action.

---

## Why Status Codes Matter

Imagine submitting a form on a website.

Without a status code, the client would have no quick way to know:

* Was the request successful?
* Was the resource missing?
* Was authentication required?
* Did the server encounter an error?

Status codes provide a standardized language that every browser, mobile app, API client, and server understands.

---

## The Five Status Code Categories

Every HTTP status code belongs to one of five categories.

| Range | Category      | Meaning                                |
| ----- | ------------- | -------------------------------------- |
| 1xx   | Informational | Request received, processing continues |
| 2xx   | Success       | Request completed successfully         |
| 3xx   | Redirection   | Additional action required             |
| 4xx   | Client Error  | Problem with the request               |
| 5xx   | Server Error  | Problem on the server                  |

The first digit tells you the category immediately.

For example:

* 200 → Success
* 404 → Client Error
* 500 → Server Error

---

# 1xx Informational Responses

Informational responses indicate that the server received the request and is continuing to process it.

Most frontend developers rarely interact with these codes directly.

---

## 100 Continue

The server has received the initial part of the request and allows the client to continue sending the rest.

Example:

```http
HTTP/1.1 100 Continue
```

Common usage:

* Large file uploads
* Streaming requests

---

## 101 Switching Protocols

The server agrees to switch communication protocols.

Example:

```http
HTTP/1.1 101 Switching Protocols
```

Common usage:

* WebSocket connections

---

# 2xx Success Responses

These status codes indicate successful requests.

This is the category developers see most often.

---

## 200 OK

The request succeeded.

Example:

```http
GET /users/10
```

Response:

```http
HTTP/1.1 200 OK
```

Common usage:

* Fetching data
* Successful updates
* Successful searches

---

## 201 Created

A new resource was successfully created.

Example:

```http
POST /users
```

Response:

```http
HTTP/1.1 201 Created
```

Common usage:

* Creating users
* Creating orders
* Creating posts

---

## 202 Accepted

The request was accepted but processing has not finished yet.

Example:

```http
HTTP/1.1 202 Accepted
```

Common usage:

* Background jobs
* Queue processing
* Long-running tasks

---

## 204 No Content

The request succeeded, but no response body is returned.

Example:

```http
DELETE /users/10
```

Response:

```http
HTTP/1.1 204 No Content
```

Common usage:

* Delete operations
* Successful actions that do not return data

---

# 3xx Redirection Responses

Redirection status codes tell the client to perform another action before completing the request.

---

## 301 Moved Permanently

The resource has permanently moved to a new URL.

Example:

```http
HTTP/1.1 301 Moved Permanently
Location: https://example.com/new-page
```

Common usage:

* Website migrations
* URL restructuring

---

## 302 Found

The resource temporarily exists at another URL.

Example:

```http
HTTP/1.1 302 Found
```

Common usage:

* Temporary redirects

---

## 304 Not Modified

The resource has not changed since the last request.

Example:

```http
HTTP/1.1 304 Not Modified
```

Common usage:

* Browser caching
* Performance optimization

---

# 4xx Client Errors

These errors indicate a problem with the client's request.

The server understood the request but could not process it.

---

## 400 Bad Request

The request is malformed or invalid.

Example:

```http
POST /users
```

Invalid body:

```json
{
  "email":
}
```

Response:

```http
HTTP/1.1 400 Bad Request
```

Common causes:

* Invalid JSON
* Missing required fields
* Incorrect request format

---

## 401 Unauthorized

Authentication is required.

Example:

```http
HTTP/1.1 401 Unauthorized
```

Common causes:

* Missing access token
* Invalid token
* Expired token

---

## 403 Forbidden

The user is authenticated but lacks permission.

Example:

```http
HTTP/1.1 403 Forbidden
```

Common causes:

* Role restrictions
* Access control rules

---

## 404 Not Found

The requested resource does not exist.

Example:

```http
GET /users/999999
```

Response:

```http
HTTP/1.1 404 Not Found
```

Common causes:

* Wrong URL
* Missing resource
* Deleted data

---

## 405 Method Not Allowed

The endpoint exists but does not support the requested method.

Example:

```http
DELETE /login
```

Response:

```http
HTTP/1.1 405 Method Not Allowed
```

Common causes:

* Wrong HTTP method

---

## 409 Conflict

The request conflicts with existing data.

Example:

```http
POST /users
```

Body:

```json
{
  "email": "existing@example.com"
}
```

Response:

```http
HTTP/1.1 409 Conflict
```

Common causes:

* Duplicate email
* Duplicate username
* Unique constraint violations

---

## 422 Unprocessable Entity

The request format is valid, but the data fails validation.

Example:

```json
{
  "email": "not-an-email"
}
```

Response:

```http
HTTP/1.1 422 Unprocessable Entity
```

Common causes:

* Validation failures
* Business rule violations

---

## 429 Too Many Requests

The client exceeded rate limits.

Example:

```http
HTTP/1.1 429 Too Many Requests
```

Common causes:

* API abuse
* Excessive requests
* Rate limiting protection

---

# 5xx Server Errors

These errors indicate that something went wrong on the server.

The client usually cannot fix the issue directly.

---

## 500 Internal Server Error

A generic server-side failure.

Example:

```http
HTTP/1.1 500 Internal Server Error
```

Common causes:

* Unhandled exceptions
* Database failures
* Programming errors

---

## 502 Bad Gateway

One server received an invalid response from another server.

Example:

```http
HTTP/1.1 502 Bad Gateway
```

Common causes:

* Reverse proxy failures
* Upstream service failures

---

## 503 Service Unavailable

The service is temporarily unavailable.

Example:

```http
HTTP/1.1 503 Service Unavailable
```

Common causes:

* Maintenance
* Overloaded infrastructure
* Temporary downtime

---

## 504 Gateway Timeout

A server waited too long for another service to respond.

Example:

```http
HTTP/1.1 504 Gateway Timeout
```

Common causes:

* Slow backend services
* Network issues
* Database timeouts

---

# Status Code Troubleshooting Guide

| Status Code | Typical Meaning      | First Thing to Check        |
| ----------- | -------------------- | --------------------------- |
| 200         | Success              | Response body               |
| 201         | Resource created     | Created data                |
| 204         | Success without body | Expected empty response     |
| 400         | Invalid request      | Request payload             |
| 401         | Authentication issue | Access token                |
| 403         | Permission issue     | User role                   |
| 404         | Resource missing     | URL or ID                   |
| 409         | Data conflict        | Duplicate records           |
| 422         | Validation failure   | Input fields                |
| 429         | Rate limit exceeded  | Request frequency           |
| 500         | Server failure       | Server logs                 |
| 502         | Upstream failure     | Gateway configuration       |
| 503         | Service unavailable  | Infrastructure status       |
| 504         | Timeout              | Network/backend performance |

---

# Real-World Examples

## Login API

Successful login:

```http
HTTP/1.1 200 OK
```

Wrong password:

```http
HTTP/1.1 401 Unauthorized
```

---

## Product API

Product exists:

```http
HTTP/1.1 200 OK
```

Product not found:

```http
HTTP/1.1 404 Not Found
```

---

## User Registration

Successful registration:

```http
HTTP/1.1 201 Created
```

Email already exists:

```http
HTTP/1.1 409 Conflict
```

---

## File Upload

Upload accepted:

```http
HTTP/1.1 202 Accepted
```

File too large:

```http
HTTP/1.1 413 Payload Too Large
```

---

# Common Beginner Mistakes

## Mistake 1: Treating All Errors as 500

Many beginners assume every failure is a server issue.

Not all failures are server errors.

Examples:

* Invalid request → 400
* Missing token → 401
* Missing resource → 404

---

## Mistake 2: Ignoring Response Codes

Some developers only inspect the response body.

Always check:

```http
HTTP Status Code
```

before processing data.

---

## Mistake 3: Confusing 401 and 403

401:

```text
You are not authenticated.
```

403:

```text
You are authenticated,
but not allowed.
```

---

## Mistake 4: Treating 404 as a System Failure

A missing resource is often a normal situation.

Example:

```http
GET /users/999999
```

Returning:

```http
404 Not Found
```

may be completely expected.

---

# Final Summary

HTTP status codes are a universal language for communicating request outcomes.

The most important codes every developer should know are:

* 200 OK
* 201 Created
* 204 No Content
* 400 Bad Request
* 401 Unauthorized
* 403 Forbidden
* 404 Not Found
* 409 Conflict
* 422 Unprocessable Entity
* 429 Too Many Requests
* 500 Internal Server Error
* 503 Service Unavailable

Learning status codes allows you to quickly diagnose problems, understand API behavior, and build applications that respond correctly to success and failure scenarios.
