# Introduction to APIs
> **Understanding What an API Is and Why APIs Are Important**

---

# 🎯 Learning Objectives

By the end of this lesson, you will be able to:

- Understand what API stands for.
- Understand the meaning of an interface.
- Explain the idea of an API using a real-world analogy.
- Understand how APIs allow software systems to communicate.
- Understand the relationship between APIs, servers, and databases.
- Explain why APIs are commonly used on the web.
- Understand how applications use APIs to retrieve data.
- Understand why APIs are different from normal websites and mobile applications.
- Understand how Postman helps you work with APIs without writing code.

---

# 📖 Introduction

If you are completely new to APIs, this lesson provides a quick introduction.

API stands for:

```text
Application Programming Interface
```

The most important word to understand is probably:

```text
Interface
```

An interface provides a defined way for different systems to communicate and work together.

---

# What Does API Stand For?

API means:

```text
Application Programming Interface
```

Breaking it down:

| Word | Meaning |
|---|---|
| Application | A software program or system |
| Programming | Instructions and software logic |
| Interface | A defined way for different systems to interact |

The key idea is the **interface**.

---

# What Is an Interface?

An interface can be thought of as a contract between two different systems.

It defines:

- How they connect.
- What rules they must follow.
- What information can be exchanged.
- How each side should behave.

As long as both sides follow the rules, they can work together.

---

# Real-World Example – Charging Your Phone

Think about charging your phone.

Years ago, many phone manufacturers used their own special charging connectors.

For example:

```text
Phone Manufacturer A
        │
        ▼
Special Connector A

Phone Manufacturer B
        │
        ▼
Special Connector B

Phone Manufacturer C
        │
        ▼
Special Connector C
```

A charger designed for one phone might not work with another phone.

---

# Modern Standard – USB-C

Today, many devices use a common standard:

```text
USB-C
```

With one compatible charger, you may be able to charge:

- A phone
- A tablet
- A laptop
- A gaming controller
- Other compatible devices

This is possible because all of those devices follow the same interface standard.

---

# USB-C as an Interface

The USB-C standard defines things such as:

- Connector size
- Connector shape
- Number of pins
- Electrical behavior
- Communication rules
- Other technical requirements

Because manufacturers follow the same rules, different devices can work together.

This is called:

```text
Interoperability
```

---

# Interface as a Contract

You can think of an interface as a contract.

```text
Device A
   │
   │ Follows Rules
   ▼
Interface Standard
   ▲
   │ Follows Rules
   │
Device B
```

Each side can be designed differently internally.

However, as long as both sides obey the agreed interface, communication works.

---

# What Is an API?

An API is an interface that allows software applications to communicate with another software system.

Often, the remote system contains data stored on a server.

That data may be stored in a database.

The API provides a controlled way for another application to access or exchange that data.

---

# Basic API Architecture

```text
Application
     │
     ▼
    API
     │
     ▼
   Server
     │
     ▼
 Database
```

The application does not normally communicate directly with the database.

Instead, it communicates through the API.

---

# Why Is the API Important?

Without an API, the server's internal data would usually not be directly available to outside applications.

The API provides a controlled interface.

It defines:

- What data can be requested.
- What data can be sent.
- How requests should be formatted.
- How responses should be returned.

---

# API Communication Flow

```text
Application
     │
     │ Request
     ▼
    API
     │
     ▼
   Server
     │
     ▼
 Database
     │
     ▼
   Server
     │
     ▼
    API
     │
     │ Response
     ▼
Application
```

---

# Web APIs

When people talk about APIs today, they are often referring to:

```text
Web APIs
```

Web APIs communicate over networks such as the internet.

They allow applications running on one system to communicate with services running on another server.

---

# APIs Are Everywhere

APIs are used almost everywhere in modern software.

Applications frequently rely on APIs to obtain or send data.

Examples include:

- Weather applications
- Banking applications
- Shopping applications
- Social media applications
- Maps
- Transportation applications
- Cloud platforms

---

# Example – Weather Application

Consider a weather application on your phone.

The application itself may not generate weather forecasts.

Instead, it sends a request to a weather API.

---

# Weather API Flow

```text
Weather App
     │
     ▼
Weather API
     │
     ▼
Weather Server
     │
     ▼
Weather Data
     │
     ▼
Weather API
     │
     ▼
Weather App
```

The application receives the latest weather data and displays it to the user.

---

# One API Can Support Many Applications

There may only be a small number of large services providing certain weather data.

However, thousands of different weather applications may use those APIs.

Example:

```text
                Weather API
               /     |      \
              /      |       \
             ▼       ▼        ▼
       Weather App  Weather App  Weather App
            1            2            3
```

Each application can have a completely different user interface while using the same underlying data source.

---

# Important Concept to Remember

An API is an interface to data or functionality that is available somewhere else.

In many cases:

```text
Your Application
       │
       ▼
      API
       │
       ▼
Remote Server
       │
       ▼
Stored Data
```

The server may be located anywhere on the internet.

---

# APIs Are Designed for Software

A normal website is designed for humans.

It contains things such as:

- Buttons
- Menus
- Images
- Forms
- Navigation
- Text

An API is normally designed for software programs.

It does not usually have a friendly graphical interface like a normal website.

---

# Website vs API

| Website | API |
|---|---|
| Designed for humans | Designed for software |
| Uses buttons and menus | Uses requests and responses |
| Easy to browse visually | Usually requires tools or code |
| Displays formatted pages | Returns structured data |
| Human interaction | Programmatic interaction |

---

# Working with APIs Without Postman

Normally, to communicate with an API, you may need to:

- Write programming code.
- Use a terminal.
- Run command-line tools.
- Construct HTTP requests manually.

For beginners, this can seem difficult.

---

# Example Without Postman

A developer might communicate with an API using:

```text
Application Code
```

or a command-line tool.

The user has to understand:

- URLs
- HTTP methods
- Headers
- Request bodies
- Authentication
- Responses

---

# Where Postman Helps

This is where **Postman** becomes useful.

Postman can communicate with a server through an API and exchange data.

Instead of manually writing code for every request, Postman gives you a graphical interface.

---

# Postman API Flow

```text
You
 │
 ▼
Postman
 │
 ▼
API Request
 │
 ▼
Server
 │
 ▼
API Response
 │
 ▼
Postman
 │
 ▼
You See the Result
```

---

# Why Use Postman?

Postman allows you to work with APIs through a mostly intuitive graphical interface.

You can use Postman to:

- Send requests.
- Receive responses.
- View returned data.
- Change request parameters.
- Add headers.
- Test APIs.
- Organize API requests.

You do not need to write a complete software program just to test an API.

---

# Postman Does the Heavy Lifting

Instead of manually constructing everything from a terminal, Postman helps manage many of the details.

For example:

```text
Without Postman
     │
     ▼
Write Code
     │
     ▼
Create HTTP Request
     │
     ▼
Send Request
     │
     ▼
Process Response
```

With Postman:

```text
Postman
   │
   ▼
Select Request Type
   │
   ▼
Enter API URL
   │
   ▼
Click Send
   │
   ▼
View Response
```

---

# Do Not Worry If APIs Still Seem Unclear

At first, the idea of an API may seem abstract.

That is completely normal.

The concept becomes much easier once you start working with real API requests.

Hands-on experience will help connect all of these ideas together.

---

# API Example Summary

Suppose an application needs weather information.

```text
User Opens Weather App
        │
        ▼
Weather App Sends Request
        │
        ▼
Weather API
        │
        ▼
Remote Server
        │
        ▼
Weather Data Retrieved
        │
        ▼
API Sends Response
        │
        ▼
Weather App Displays Forecast
```

The user never communicates directly with the remote database.

The API provides the interface.

---

# 📌 Quick Revision

| Term | Meaning |
|---|---|
| API | Application Programming Interface |
| Interface | A defined way for systems to interact |
| Server | Computer or service providing data or functionality |
| Database | Location where data may be stored |
| Web API | API accessed over a network or internet |
| Request | Information sent to an API |
| Response | Information returned by an API |
| Postman | Tool used to interact with and test APIs |
| Interoperability | Different systems working together through agreed standards |

---

# 🧪 Knowledge Check

## Question 1

What does API stand for?

**Answer:**

```text
Application Programming Interface
```

---

## Question 2

What is the most important concept in the term API?

**Answer:**

The idea of an **interface**.

---

## Question 3

What is an interface?

**Answer:**

A defined set of rules that allows different systems to communicate and work together.

---

## Question 4

Why is USB-C a useful analogy for an API?

**Answer:**

Because USB-C provides a common interface that allows many different devices to work with compatible chargers.

---

## Question 5

What does an API normally connect?

**Answer:**

An application to data or functionality available on another system or server.

---

## Question 6

Where is API data often stored?

**Answer:**

On a server, often inside a database.

---

## Question 7

What is a Web API?

**Answer:**

An API that communicates over a network such as the internet.

---

## Question 8

How does a weather application get current weather information?

**Answer:**

It can send a request to a weather API and receive weather data in response.

---

## Question 9

Why are APIs not normally as easy for humans to use as websites?

**Answer:**

Because APIs are primarily designed for software programs rather than direct human interaction.

---

## Question 10

What problem does Postman solve?

**Answer:**

Postman gives users a graphical interface for communicating with APIs without requiring them to write a complete program or manually construct requests from the terminal.

---

# 📖 Key Takeaways

- API stands for **Application Programming Interface**.
- The key concept is the word **interface**.
- An interface defines rules that allow different systems to work together.
- USB-C is a useful real-world example of an interface standard.
- APIs allow applications to communicate with remote servers.
- API data is often stored in databases behind those servers.
- Web APIs communicate across networks such as the internet.
- Many different applications can use the same API.
- APIs are primarily designed for software rather than humans.
- Postman provides a graphical way to communicate with APIs.
- Hands-on API practice makes the concepts much easier to understand.

---

# 💡 Remember

> **Think of an API as a controlled doorway into another software system.**
>
> ```text
> Application
>      │
>      ▼
>     API
>      │
>      ▼
>   Server
>      │
>      ▼
>    Data
> ```
>
> The application does not need to know exactly how the server works internally.
>
> It only needs to understand the rules of the API.

---

# Golden Rule

```text
Client Application
        │
        │ Request
        ▼
       API
        │
        ▼
Remote Server
        │
        │ Response
        ▼
       API
        │
        ▼
Client Application
```

**An API provides a standard way for one software system to request data or functionality from another software system.**