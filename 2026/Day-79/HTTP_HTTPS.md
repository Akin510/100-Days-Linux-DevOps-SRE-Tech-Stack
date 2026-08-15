# What Is HTTP/HTTPS?
> **Understanding HTTP Requests, Responses, and API Communication**

---

# 🎯 Learning Objectives

By the end of this lesson, you will be able to:

- Understand what HTTP is.
- Understand what HTTPS is.
- Explain the difference between HTTP and HTTPS.
- Understand how Postman communicates with an API server.
- Understand the concept of a **request** and a **response**.
- Understand what an HTTP protocol is.
- Identify the major components of an HTTP request.
- Identify the major components of an HTTP response.
- Understand HTTP request methods.
- Understand the purpose of HTTP headers.
- Understand the purpose of the HTTP request and response body.
- Understand the meaning of the `200 OK` status code.
- Understand how these components appear inside Postman.

---

# 📖 Introduction

In the previous lesson, we learned that an API provides an interface that allows different software systems to communicate.

Now let's look more closely at what happens when Postman communicates with an API endpoint.

Suppose we connect to a status endpoint of an API.

Postman sends a message to the server running the API.

We call this message a:

```text
Request
```

It is called a request because Postman is requesting something from the server.

The server receives the request, understands what we want, and sends another message back.

We call this message a:

```text
Response
```

The communication between Postman and the API server commonly takes place using:

```text
HTTP
```

or its secure version:

```text
HTTPS
```

---

# What Is HTTP?

HTTP stands for:

```text
Hypertext Transfer Protocol
```

HTTP is a **protocol** that allows a client and server to communicate.

In our example:

```text
Client = Postman
Server = API Server
```

The communication looks like this:

```text
Postman
   │
   │ HTTP Request
   ▼
API Server
   │
   │ HTTP Response
   ▼
Postman
```

---

# What Is a Protocol?

A **protocol** is essentially a set of rules that both sides agree to follow.

For communication to work successfully:

```text
Client
   │
   │
   ▼
HTTP Rules
   ▲
   │
   │
Server
```

Both the client and server must understand the same rules.

You can think of a protocol as an agreed language and set of procedures for communication.

---

# Why Is HTTP Important for APIs?

Most Web APIs use HTTP or HTTPS for communication.

Therefore, understanding the basics of HTTP is extremely important when working with APIs.

You will frequently see HTTP concepts when:

- Reading API documentation.
- Sending API requests.
- Testing APIs.
- Using Postman.
- Troubleshooting API problems.
- Developing applications that communicate with APIs.

---

# What Is HTTPS?

When looking at an API address in Postman, you will commonly see:

```text
https://
```

HTTPS stands for:

```text
Hypertext Transfer Protocol Secure
```

The important difference is the:

```text
S
```

which means:

```text
Secure
```

---

# HTTP vs HTTPS

The basic communication concepts we discuss in this course apply to both HTTP and HTTPS.

For simplicity, we may often refer to the protocol simply as:

```text
HTTP
```

However, whenever possible, you should use:

```text
HTTPS
```

because HTTPS provides secure, encrypted communication.

---

# Why Is HTTPS Important?

HTTPS encrypts the communication between the client and the server.

For example:

```text
Postman
   │
   │ Encrypted HTTPS Communication
   ▼
API Server
```

This protects the data being transmitted.

Without encryption, someone who is able to observe the network traffic may potentially be able to read sensitive information.

HTTPS helps prevent this by encrypting the communication.

---

# HTTP vs HTTPS Communication

## HTTP

```text
Client
   │
   │ HTTP
   │
   ▼
Server
```

The communication is not protected by HTTPS encryption.

---

## HTTPS

```text
Client
   │
   │ 🔒 Encrypted Communication
   │
   ▼
Server
```

HTTPS provides an encrypted connection.

---

# 📌 Important Rule

> **Whenever possible, use HTTPS instead of HTTP.**

For API communication:

```text
HTTP
   │
   ▼
Communication

HTTPS
   │
   ▼
Encrypted Communication
```

---

# Understanding HTTP Messages

HTTP communication normally involves two major messages:

```text
Request
```

and:

```text
Response
```

The client sends the request.

The server sends the response.

---

# HTTP Communication Flow

```text
Client / Postman
       │
       │
       │ HTTP Request
       ▼
    API Server
       │
       │
       │ HTTP Response
       ▼
Client / Postman
```

---

# HTTP Request

A simplified HTTP request contains several important components.

These include:

```text
URL
Request Method
Headers
Body
```

We can visualize it like this:

```text
HTTP REQUEST
┌──────────────────────────────┐
│ Request Method               │
│ URL                          │
│ Headers                      │
│                              │
│ Body                         │
└──────────────────────────────┘
```

---

# HTTP Response

The server sends an HTTP response.

A simplified response contains:

```text
Status Code
Headers
Body
```

Example:

```text
HTTP RESPONSE
┌──────────────────────────────┐
│ Status Code                  │
│ Headers                      │
│                              │
│ Body                         │
└──────────────────────────────┘
```

---

# Request vs Response

| HTTP Request | HTTP Response |
|---|---|
| Request Method | Status Code |
| URL | — |
| Headers | Headers |
| Body | Body |
| Sent by Client | Sent by Server |
| Received by Server | Received by Client |

---

# Complete HTTP Communication

```text
POSTMAN / CLIENT
       │
       │
       │ REQUEST
       │
       │ Method
       │ URL
       │ Headers
       │ Body
       │
       ▼
┌─────────────────────┐
│     API SERVER      │
└─────────────────────┘
       │
       │ RESPONSE
       │
       │ Status Code
       │ Headers
       │ Body
       │
       ▼
POSTMAN / CLIENT
```

---

# Understanding HTTP Requests in Postman

Let's look at where this information appears inside Postman.

At the top of the Postman interface, we configure our:

```text
Request
```

This includes things such as:

```text
Method
URL
Headers
Body
```

After sending the request, Postman displays the:

```text
Response
```

usually in the lower response area.

---

# 1. HTTP Request Method

The first important component is the:

```text
Request Method
```

Postman allows you to select different HTTP request methods.

By default, you will commonly see:

```text
GET
```

selected.

---

# Common HTTP Methods

There are several HTTP methods that you will encounter while working with APIs.

Examples include:

```text
GET
POST
PUT
PATCH
DELETE
```

We will explore these methods in greater detail later.

---

# GET Request

A `GET` request is commonly used when we want to retrieve information.

For example:

```text
GET
        │
        ▼
Request Information
        │
        ▼
API Server
        │
        ▼
Return Information
```

---

# Example

Suppose we have an API endpoint:

```text
https://api.example.com/status
```

We could send:

```text
GET https://api.example.com/status
```

The client is essentially asking:

```text
Please give me the status information.
```

---

# 2. URL

The next important component is the:

```text
URL
```

The URL tells Postman where the request should be sent.

For example:

```text
https://api.example.com/status
```

The address may contain both:

- The API address.
- The specific API endpoint.

---

# What Is an Endpoint?

An **endpoint** is a specific location within an API that provides access to a particular resource or function.

For example:

```text
https://api.example.com/status
```

Here:

```text
https://api.example.com
```

may represent the API server.

While:

```text
/status
```

represents a specific endpoint.

---

# URL Structure Example

```text
https://api.example.com/status
│       │               │
│       │               └── Endpoint
│       │
│       └── API / Server Address
│
└── HTTPS Protocol
```

---

# 3. HTTP Headers

Another important part of an HTTP request is:

```text
Headers
```

Headers contain additional information about the request.

Postman may automatically add certain headers for you.

We will discuss headers in more detail later in the course.

---

# Headers as Metadata

You can think of headers as additional information describing the request.

For example:

```text
HTTP Request
      │
      ├── Method
      ├── URL
      ├── Headers
      └── Body
```

Headers may provide information that helps the server understand how to process the request.

---

# 4. HTTP Request Body

The final major component of the request is the:

```text
Body
```

The body contains information that we may want to send to the server.

For some requests, there may be no body.

For example, in the `GET` request demonstrated in this lesson, we did not configure a request body.

---

# GET Request Example

```text
GET Request
     │
     ├── URL
     │
     ├── Headers
     │
     └── No Body Required in This Example
```

Other types of requests may send additional information inside the body.

---

# Request Configuration Area in Postman

The upper portion of Postman is where we configure our request.

Conceptually:

```text
┌─────────────────────────────────────────┐
│              POSTMAN                    │
├─────────────────────────────────────────┤
│ REQUEST                                 │
│                                         │
│ Method: [ GET ▼ ]                       │
│ URL:    https://api.example.com/status  │
│                                         │
│ Headers                                 │
│ Body                                    │
│                                         │
│                 [ SEND ]                │
├─────────────────────────────────────────┤
│ RESPONSE                                │
│                                         │
│ Status: 200 OK                          │
│ Headers                                 │
│ Body                                    │
│                                         │
└─────────────────────────────────────────┘
```

---

# Understanding the HTTP Response

After we send the request, the server processes it and returns a response.

The response normally contains:

```text
Status Code
Headers
Body
```

---

# 1. HTTP Status Code

The status code tells us what happened when the server processed our request.

In our example, the server returned:

```text
200 OK
```

This means that the server successfully understood and processed the request.

---

# What Does 200 OK Mean?

```text
200 OK
```

essentially means:

> The request was successful.

The server understood what we wanted and successfully returned a response.

---

# Successful Request Flow

```text
Postman
   │
   │ GET Request
   ▼
API Server
   │
   │ Request Understood
   │ Request Processed
   ▼
200 OK
   │
   ▼
Response Returned
```

---

# 2. Response Headers

The server may also send:

```text
Response Headers
```

These contain additional information about the response.

Some headers can be quite technical.

At this stage, we do not need to understand every header.

We will explore important headers later.

---

# 3. Response Body

One of the most important parts of an API response is usually the:

```text
Body
```

The body contains the main information returned by the API.

This is often the data that we actually care about.

---

# Example Response

```text
HTTP/1.1 200 OK

Headers:
Content-Type: application/json

Body:
{
    "status": "OK"
}
```

Conceptually:

```text
Server Response
      │
      ├── Status Code → Did the request work?
      │
      ├── Headers → Additional information
      │
      └── Body → Actual data/message
```

---

# 📬 Real-World Analogy – Sending a Letter

To better understand HTTP messages, think about sending a traditional letter through the postal service.

An envelope contains several pieces of information.

The HTTP request works in a similar way.

---

# HTTP Request as an Envelope

```text
┌─────────────────────────────────┐
│                                 │
│   Address                       │
│                                 │
│   Additional Postal Information │
│                                 │
│   ┌─────────────────────────┐   │
│   │                         │   │
│   │       LETTER            │   │
│   │       CONTENT           │   │
│   │                         │   │
│   └─────────────────────────┘   │
│                                 │
└─────────────────────────────────┘
```

---

# URL and Method = Where and How the Message Goes

The request method and URL help define where the request is going and what type of action is being requested.

Think of them as important delivery instructions.

```text
HTTP
Method + URL
      │
      ▼
Where and What
```

---

# Headers = Additional Envelope Information

HTTP headers are similar to additional information placed on an envelope.

For example:

- Postage information
- Barcodes
- Delivery instructions
- Routing information

These details help the postal service correctly process and deliver the letter.

Similarly, HTTP headers provide additional information that helps the server process the request.

---

# Body = The Letter Inside the Envelope

The HTTP body is similar to the actual letter inside the envelope.

```text
Envelope
   │
   ▼
Letter
   │
   ▼
Actual Message
```

For HTTP:

```text
HTTP Request
   │
   ▼
Body
   │
   ▼
Actual Data / Message
```

---

# HTTP Letter Analogy

| HTTP Component | Postal Letter Analogy |
|---|---|
| Request | Entire envelope being sent |
| Method | Type/purpose of delivery |
| URL | Destination/address |
| Headers | Additional postal information |
| Body | Letter/message inside |
| Response | Reply sent back |
| Status Code | Result of processing/delivery |

---

# Why Do We Need Rules?

Just like postal mail, HTTP communication must follow certain conventions.

If you write a letter but leave out important information, the postal service may not know where to deliver it.

Similarly, if an API request is missing required information, the API may not understand what you want.

---

# Incorrect Request

```text
Client
   │
   │ Missing Required Information
   ▼
API Server
   │
   ▼
Cannot Process Request Correctly
```

---

# Correct Request

```text
Client
   │
   │ Correct Method
   │ Correct URL
   │ Correct Headers
   │ Correct Body
   ▼
API Server
   │
   ▼
Request Processed
```

---

# How Do We Know What an API Requires?

The rules you need to follow are normally documented in:

```text
API Documentation
```

API documentation tells you things such as:

- Which URL to use.
- Which endpoint to use.
- Which HTTP method to use.
- Which headers are required.
- Whether a body is required.
- What information should be placed in the body.
- What kind of response to expect.

---

# API Documentation Example

An API documentation page might tell you:

```text
Method:
GET

Endpoint:
/status

Headers:
Accept: application/json

Body:
None
```

You can then configure Postman according to those instructions.

---

# Postman + API Documentation

```text
API Documentation
        │
        ▼
Read Requirements
        │
        ▼
Configure Postman
        │
        ├── Method
        ├── URL
        ├── Headers
        └── Body
        │
        ▼
Send Request
        │
        ▼
API Server
        │
        ▼
Receive Response
```

---

# Complete API Communication Example

Suppose we want to retrieve the status of an API.

We configure Postman:

```text
Method:
GET

URL:
https://api.example.com/status
```

Then click:

```text
Send
```

Postman sends:

```text
GET Request
        │
        ▼
https://api.example.com/status
        │
        ▼
API Server
```

The server processes the request and responds:

```text
200 OK
```

with a response body such as:

```json
{
    "status": "OK"
}
```

---

# Complete Request/Response Flow

```text
                    CLIENT
                   POSTMAN
                      │
                      │
              HTTP/HTTPS REQUEST
                      │
                      ├── Method
                      │      GET
                      │
                      ├── URL
                      │      /status
                      │
                      ├── Headers
                      │
                      └── Body
                      │
                      ▼
              ┌───────────────┐
              │  API SERVER   │
              └───────────────┘
                      │
                      │
               HTTP RESPONSE
                      │
                      ├── Status Code
                      │      200 OK
                      │
                      ├── Headers
                      │
                      └── Body
                      │      API Data
                      │
                      ▼
                    POSTMAN
```

---

# HTTP/HTTPS and the API Architecture

Putting everything together:

```text
User
 │
 ▼
Postman
 │
 │ HTTPS Request
 ▼
API Endpoint
 │
 ▼
API Server
 │
 ▼
Application Logic
 │
 ▼
Database
 │
 ▼
Application Logic
 │
 ▼
API Server
 │
 │ HTTPS Response
 ▼
Postman
 │
 ▼
User Sees Response
```

---

# 🧪 Knowledge Check

## Question 1

What does HTTP stand for?

**Answer:**

```text
Hypertext Transfer Protocol
```

---

## Question 2

What does HTTPS stand for?

**Answer:**

```text
Hypertext Transfer Protocol Secure
```

---

## Question 3

What does the `S` in HTTPS mean?

**Answer:**

```text
Secure
```

---

## Question 4

What is a protocol?

**Answer:**

A protocol is a set of rules that both sides follow in order to communicate.

---

## Question 5

In our Postman example, who is the client?

**Answer:**

```text
Postman
```

---

## Question 6

Who receives the HTTP request?

**Answer:**

The API server.

---

## Question 7

Who sends the HTTP response?

**Answer:**

The API server sends the response back to the client.

---

## Question 8

What are the major components of an HTTP request?

**Answer:**

```text
Request Method
URL
Headers
Body
```

---

## Question 9

What are the major components of an HTTP response?

**Answer:**

```text
Status Code
Headers
Body
```

---

## Question 10

What does `200 OK` mean?

**Answer:**

The request was successfully processed.

---

## Question 11

What is usually the most important part of the API response?

**Answer:**

The response body, because it normally contains the data or message we requested.

---

## Question 12

Where do we learn the rules for communicating with a particular API?

**Answer:**

```text
API Documentation
```

---

# 📌 Quick Revision

| Term | Meaning |
|---|---|
| HTTP | Hypertext Transfer Protocol |
| HTTPS | Hypertext Transfer Protocol Secure |
| Protocol | Set of communication rules |
| Client | Software sending the request |
| Server | System receiving and processing the request |
| Request | Message sent from client to server |
| Response | Message returned from server to client |
| Method | Defines the requested action |
| URL | Address where the request is sent |
| Endpoint | Specific API resource/location |
| Headers | Additional information about the message |
| Body | Main data or content of the message |
| Status Code | Indicates the result of the request |
| `200 OK` | Successful request |
| API Documentation | Instructions for using an API |

---

# 📖 Key Takeaways

- HTTP stands for **Hypertext Transfer Protocol**.
- HTTPS stands for **Hypertext Transfer Protocol Secure**.
- HTTP is a protocol that allows clients and servers to communicate.
- HTTPS provides encrypted communication.
- Postman acts as an HTTP client when communicating with an API.
- The client sends an HTTP request.
- The API server processes the request.
- The server returns an HTTP response.
- An HTTP request contains a method, URL, headers, and possibly a body.
- An HTTP response contains a status code, headers, and usually a body.
- `200 OK` indicates that the request was successful.
- The response body normally contains the information we are most interested in.
- API documentation tells us how requests must be constructed.
- Postman provides an easy graphical interface for creating requests and examining responses.

---

# 💡 Remember – The Postal Letter Analogy

> **Think of an HTTP request like sending a letter.**
>
> ```text
> HTTP Request
>       │
>       ├── Method
>       │      What do I want to do?
>       │
>       ├── URL
>       │      Where am I sending it?
>       │
>       ├── Headers
>       │      Additional delivery information
>       │
>       └── Body
>              What message/data am I sending?
> ```
>
> The server receives the request and sends a response:
>
> ```text
> HTTP Response
>       │
>       ├── Status Code
>       │      Did it work?
>       │
>       ├── Headers
>       │      Additional information
>       │
>       └── Body
>              Here is the requested data
> ```

---

# ⭐ Golden Rule

```text
CLIENT
Postman
   │
   │
   │ HTTPS Request
   │
   ├── Method
   ├── URL
   ├── Headers
   └── Body
   │
   ▼
API SERVER
   │
   │
   │ HTTPS Response
   │
   ├── Status Code
   ├── Headers
   └── Body
   │
   ▼
CLIENT
Postman
```

> **HTTP defines the rules for communication. HTTPS uses those communication rules over a secure, encrypted connection.**