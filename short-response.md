# Short Response Questions

Answer each of these questions completely but concisely. Use the proper technical terminology. You may refer to the [Marcy Lab School Docs](https://marcylabschool.gitbook.io/marcy-lab-school-docs) or Google but do NOT copy and paste definitions or explanations verbatim.

You can earn up to 6 points for each response (3 points for writing quality, 3 points for technical content).

Before submitting your responses, use a spell checker / AI to ensure that you have no grammar or spelling mistakes.

## Question 1: Servers and HTTP

What is a server? Describe the HTTP request-response cycle including the key components of a request (method, endpoint, headers, body) and a response (status code, headers, body). Use an analogy to support your explanation.

**Your Answer:**

A **server** is a program that listens for incoming network requests and sends back responses. Kind of like a restaurant, you (the client) place an order, the waiter delivers it to the kitchen (the **server**), and the kitchen sends back your food. 

An HTTP request consists of, a **method**, the **action** to perform (`GET`, `POST`, `PATCH`, `DELETE`), an **endpoint**, the **URL** identifying the resource (`/api/users/3`), **headers**, metadata like `content type` or **auth tokens**, and **body**, which is data sent with the request (used in `POST` or `PATCH`).
The **server** responds with a status code (`200 OK`, `404 Not Found`), **headers**, and a **body** containing the returned data, typically `JSON`.


## Question 2: Middleware

What is middleware in Express? How does it differ from a regular controller? Explain the role of `next()` and provide an example of when middleware is useful.

**Your Answer:**

**Middleware** is a function in **Express** that runs during the **request-response cycle** before the final **route handler**. It shares the (`req`, `res`, `next`) signature with **controllers**, but instead of sending a response, it performs intermediate work and calls `next()` to pass control forward.

```js
const logRoutes = (req, res, next) => {
  const time = new Date().toLocaleString();
  console.log(`${req.method}: ${req.originalUrl} - ${time}`);
  next();
};

app.use(logRoutes);
```

This **middleware** is useful because it allows us to see how long a request response cycle takes, and what **methods** are taking place and when.

## Question 3: API Key Security

Why is it dangerous to use API keys in client-side (frontend) code? Explain how a backend server solves this problem (the "proxy" pattern). Include what role environment variables (`.env`) play in this approach.

**Your Answer:**

**API keys** used in frontend code are publicly visible, so anyone can open the developer tools and steal them. The **proxy pattern** fixes this, the client calls the **Express** server, which attaches the secret key and forwards the request to the **third-party API**. The key never reaches the browser.

**Environment variables** (`.env`) keep keys out of source code entirely. You store `API_KEY=abc123` in `.env`, add it to `.gitignore`, and access it via `process.env.API_KEY`, keeping it secret from both the browser and your version control history.

## Question 4: Debugging a Server

A fellow student is building an Express server. They send a `PATCH` request to `/api/bookmarks/1` using Postman, but they receive a `404` status code. List at least three things you would check to debug this issue and explain why each one could be the cause of the problem.

**Your Answer:**

1. I would check if they defined `app.get('/api/bookmarks/:id')` instead of `app.patch(...)`, **Express** won't match the `PATCH` request and falls through to a `404`.
2. I would check a route typo like `/api/bookmark/:id` vs. `/api/bookmarks/:id`. Also verifying your `app.use()` prefix isn't duplicated, using a router at `/api` while the router also defines `/api/bookmarks/:id` results in `/api/api/bookmarks/:id`.
3. If the **router file** was never **imported** and connected via `app.use()` in `server.js`, none of its routes are reachable regardless of how they're defined.
