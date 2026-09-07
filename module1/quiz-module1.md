# Quiz — HTTP Request & Response

Test your understanding of how HTTP requests and responses are structured and interpreted.

This quiz reviews:

- HTTP request anatomy
- HTTP response anatomy
- URLs and endpoints
- HTTP methods
- Request headers
- Response headers
- Query parameters
- Path parameters
- Request bodies
- Status codes
- Cookies

---

## Instructions

Choose the **one best answer** for each question.

Try to answer based on your understanding rather than memorizing definitions.

---

## Question 1

Which part of an HTTP request identifies the action the client wants the server to perform, such as retrieving or creating data?

A. HTTP method  
B. Response body  
C. Status code  
D. Cookie  

---

## Question 2

What is the main purpose of an endpoint in an HTTP API?

A. Identify where a particular API resource or operation can be accessed  
B. Describe whether the previous request succeeded  
C. Store the API response permanently on the client  
D. Authenticate every user without credentials  

---

## Question 3

A client sends:

```text
GET /posts/42
````

What does the path parameter conceptually identify in this request?

A. A collection of every post
B. The HTTP response status
C. The specific post identified by `42`
D. The request body format

---

## Question 4

Which URL component is commonly used to provide additional parameters for filtering or modifying how a collection is retrieved?

A. Status line
B. Response body
C. Query string
D. HTTP version

---

## Question 5

Which HTTP message component is commonly used to carry structured data submitted by a client, such as JSON sent when creating a resource?

A. Request body
B. Response status code
C. Query parameter
D. Response header

---

## Question 6

A server returns HTTP `404` after a client requests a resource that cannot be found.

What does the status code primarily communicate?

A. The request was successfully created
B. The requested resource was not found
C. The client must always retry immediately
D. The response contains a valid JSON object

---

## Question 7

What is the primary role of an HTTP request header?

A. Replace the requested resource path
B. Guarantee that the server will return a success response
C. Provide metadata or additional information about the request
D. Store every JSON object returned by the API

---

## Question 8

Which statement best describes the relationship between an HTTP request and an HTTP response?

A. The response is sent first and the request confirms it afterward
B. Both messages are always sent by the client
C. The request is sent by the client and the response is returned by the server
D. Both messages are always sent by the server

---

## Question 9

A client requests:

```text
GET /posts/10
```

and receives a response containing JSON data for post `10`.

Which part of the HTTP interaction tells the client whether the operation succeeded?

A. Path parameter
B. Query string
C. Request body
D. HTTP status code

---

## Question 10

What is the main purpose of cookies in HTTP communication?

A. Define which HTTP method the client must use
B. Carry small pieces of state between the client and server across requests
C. Replace every API response body
D. Convert JSON into an HTTP status code

---

# Answer Key

> Use this section only after completing the quiz.

| Question | Answer |
| -------- | ------ |
| 1        | A      |
| 2        | A      |
| 3        | C      |
| 4        | C      |
| 5        | A      |
| 6        | B      |
| 7        | C      |
| 8        | C      |
| 9        | D      |
| 10       | B      |

---

# Score Guide

Calculate your score:

```text
Correct Answers ÷ 10 × 100
```

### 90–100%

**Excellent**

You have a strong understanding of HTTP request and response fundamentals.

### 70–89%

**Good**

You understand most concepts, but some areas may need review.

### 50–69%

**Needs Review**

Review the HTTP request/response lessons before continuing.

### Below 50%

**Review the Module**

Go back through the module and focus on the anatomy and purpose of each HTTP component.

---

# Reflection

After completing the quiz, answer these questions:

### 1. Which HTTP concept was easiest for you?

```text
________________________________

________________________________
```

### 2. Which HTTP concept was most difficult?

```text
________________________________

________________________________
```

### 3. What is the difference between a path parameter and a query parameter?

```text
________________________________

________________________________
```

### 4. What is the difference between a request and a response?

```text
________________________________

________________________________
```

### 5. Why are HTTP status codes useful when working with APIs?

```text
________________________________

________________________________
```

---

## Key Takeaway

An HTTP interaction can be understood by separating its major components:

```text
HTTP REQUEST
     │
     ├── Method
     ├── URL
     ├── Headers
     ├── Query Parameters
     ├── Path Parameters
     └── Body
           │
           ▼
        SERVER
           │
           ▼
HTTP RESPONSE
     │
     ├── Status Code
     ├── Headers
     └── Body
```

> **Understanding HTTP means knowing what each part of the request and response is responsible for.**


