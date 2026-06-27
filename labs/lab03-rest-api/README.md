# Lab 3 - REST API with Express

## Overview

In this lab, you will build a small REST-style API using Express.

In Lab 2, you worked with HTTP and JSON using Node's built-in `http` module. In this lab, you will use Express to organize API routes, parse JSON request bodies, return JSON responses, and use HTTP methods and status codes more clearly.

You will create an API for managing a small in-memory collection of items. The data does not need to be stored in a database. It can reset each time the server restarts.

## Learning Goals

By the end of this lab, you should be able to:

- Create an Express server.
- Define API routes using `app.get()`, `app.post()`, `app.put()`, and `app.delete()`.
- Use route parameters such as `/items/:id`.
- Read JSON request bodies using `express.json()`.
- Return JSON responses using `res.json()`.
- Use appropriate HTTP status codes.
- Implement basic REST-style CRUD operations.
- Describe an API using a simple OpenAPI YAML file.
- Test API behavior with automated tests and `curl`.

## Required Features

Your API must manage a collection of items.

Each item must have at least:

```json
{
  "id": 1,
  "name": "keyboard",
  "quantity": 10
}
```

You may add additional fields, but your API must still support `id`, `name`, and `quantity`.

Your server must support the following routes:

| Method | Route | Description |
|---|---|---|
| GET | `/health` | Return a simple health check response |
| GET | `/items` | Return all items |
| GET | `/items/:id` | Return one item by ID |
| POST | `/items` | Create a new item |
| PUT | `/items/:id` | Update an existing item |
| DELETE | `/items/:id` | Delete an existing item |

## Expected Behavior

### `GET /health`

Returns:

```json
{
  "status": "ok"
}
```

### `GET /items`

Returns an array of items.

Example response:

```json
[
  {
    "id": 1,
    "name": "keyboard",
    "quantity": 10
  },
  {
    "id": 2,
    "name": "mouse",
    "quantity": 5
  }
]
```

### `GET /items/:id`

Returns the matching item if it exists.

Example response:

```json
{
  "id": 1,
  "name": "keyboard",
  "quantity": 10
}
```

If the item does not exist, return a `404` response.

Example error response:

```json
{
  "error": "Item not found"
}
```

### `POST /items`

Creates a new item.

The client should send a JSON request body with `name` and `quantity`.

Example request body:

```json
{
  "name": "monitor",
  "quantity": 4
}
```

The server should assign the `id`.

Example response:

```json
{
  "id": 3,
  "name": "monitor",
  "quantity": 4
}
```

A successful create request should return status code `201`.

If the request body is missing required fields or contains invalid data, return a `400` response with a JSON error message.

### `PUT /items/:id`

Updates an existing item.

For this lab, `PUT /items/:id` should replace the `name` and `quantity` values for the item. The request body should include both `name` and `quantity`.

Example request body:

```json
{
  "name": "mechanical keyboard",
  "quantity": 12
}
```

If the item exists, return the updated item.

If the item does not exist, return a `404` response.

If the request body is missing required fields or contains invalid data, return a `400` response with a JSON error message.

### `DELETE /items/:id`

Deletes an existing item.

If the item exists, delete it and return status code `204`.

A `204` response does not need to include a response body.

If the item does not exist, return a `404` response.

## Error Responses

Your API should return reasonable error messages as JSON.

Example:

```json
{
  "error": "Item not found"
}
```

You do not need to use exactly the same wording as this README, but your errors should be clear and helpful.

## OpenAPI Requirement

Include an `openapi.yaml` file that describes your API.

The OpenAPI file should describe:

- The API title and version.
- The local server URL.
- Each required route.
- The expected request body for creating or updating an item.
- The expected response body for each route.
- Basic error responses.

The OpenAPI file does not have to be perfect, but it should match the API you implemented.

YAML is indentation-sensitive. Use spaces, not tabs. The examples in this course use 2 spaces per indentation level.

## Running the Starter

Move into the lab directory:

```bash
cd labs/lab03-rest-api/starter
```

Install dependencies:

```bash
npm install
```

Start the server:

```bash
npm run server
```

The server listens on port `3000` by default.

## Testing with curl

After starting the server, test the health route:

```bash
curl http://localhost:3000/health
```

Expected response:

```json
{
  "status": "ok"
}
```

As you implement the lab, add additional `curl` tests for the item routes.

Example:

```bash
curl http://localhost:3000/items
```

Example `POST` request:

```bash
curl -X POST http://localhost:3000/items \
  -H "Content-Type: application/json" \
  -d '{"name":"monitor","quantity":4}'
```

Example `PUT` request:

```bash
curl -X PUT http://localhost:3000/items/1 \
  -H "Content-Type: application/json" \
  -d '{"name":"mechanical keyboard","quantity":12}'
```

Example `DELETE` request:

```bash
curl -X DELETE http://localhost:3000/items/1
```

## Automated Testing

Your project should include automated tests.

At minimum, your tests should verify that:

- `GET /health` returns `{ "status": "ok" }`.
- `GET /items` returns a list of items.
- `POST /items` creates a new item.
- `GET /items/:id` can retrieve an item.
- `PUT /items/:id` updates an item.
- `DELETE /items/:id` deletes an item.
- A missing item returns `404`.

The starter includes one small test for `GET /health`. You should add more tests as you complete the lab.

Run tests with:

```bash
npm test
```

## Reflection Questions

Answer the following questions in your submission:

1. What makes this API more "REST-like" than the previous HTTP/JSON lab?
My answer:
- This lab is using an API that follows a REST architecture by using the standard HTTP methods GET, POST, PUT, and DELETE to tell the
  server what operation the client wants to perform. Instead of manually handling every HTTP request like in the previous lab, Express
  makes organizing the routes much cleaner and simpler. Another difference is that this lab focuses on building the server-side API.
  Instead of writing our own client program, we documented the API using the openapi.yaml file and tested it using automated tests and curl.
  
2. What is the purpose of a route parameter such as `/items/:id`?
My answer:
- The route parameter passes information to the server that tells it what specific resource the client is asking for. After the server
  receives the request, it uses the value of the parameter to find the correct item and then sends it back to the client. For example,
  if the client requests /items/1, the server knows it needs to retrieve the item whose ID is 1. This same idea can also be used when updating or deleting an item.

3. Why should `POST`, `PUT`, and `DELETE` use different HTTP methods?
My answer:
- Each HTTP method has a different purpose. POST creates a new item or resource, such as adding a new employee to a database. PUT updates
  an existing item or resource with new information, and DELETE removes an item or resource. Since each method performs a different action,
  they should remain separate so the server knows exactly what operation the client wants to perform. It also keeps the API cleaner and easier to understand.

4. What is the difference between a `400` error and a `404` error?
My answer:
- Both status codes indicate that something went wrong, but they mean different things. A 400 Bad Request means the client sent invalid
  or incomplete data that the server cannot process. For example, the request might be missing required information or contain invalid values.
  A 404 Not Found means the resource the client requested does not exist. A common example is requesting an item ID that isn't in the collection
  or visiting a URL that doesn't exist.

5. How does the OpenAPI file relate to your Express server code?
My answer:
- The OpenAPI file documents the Express server code by describing the routes, request bodies, response bodies, and HTTP status codes.
  This allows other developers to understand how to use the API without having to read through the server code itself. It serves as
  documentation for the system and should match what the Express server actually does.

## Submission

Submit your GitHub link in the Canvas assignment for `lab03-rest-api`.

Your submission should include:

- Your completed source code.
- Your automated tests.
- Your `openapi.yaml` file.
- Your answers to the reflection questions.
- Any graduate extension work, if applicable.

Before submitting, verify that:

```bash
npm test
```

runs successfully.
