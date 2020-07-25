---
title: Response Transform
---

By implementing the onResponse hook, the response can be transformed before being sent to the client.

The signature of the onResponse hook is:

```ts
type onResponse = (
  // Response formed by Prism
  response: HttpResponse,
  // Incoming Request
  request: HttpRequest,
  // Responses received from backend services
  fetchedResponses: FetchedHttpResponse[]
) => Promise<HttpResponse | void>;
```

You should look up the type definitions for [HttpRequest](http-request-type), [HttpResponse](http-response-type) and [FetchedResponse](fetched-response-type).

Here's an example of how to implement an onResponse hook:

```ts
module.exports = {
  http: {
    routes: {
      "/users": {
        GET: {
          onResponse: async (response, request, fetchedResponses) => {
            // Get the response object from a fetchedResponse object
            const receivedResponses = fetchedResponses.map((x) => x.response);
            // Concat the body of all responses
            const concatenatedData = receivedResponses
              .map((x) => x.body.toString())
              .join("--");
            // Send it back.
            return {
              status: 200,
              body: concatenatedData,
            };
          },
          services: {
            userservice: {
              type: "http",
              url: "http://localhost:6666/users",
            },
            messageservice: {
              type: "http" as "http",
              url: "http://localhost:6667/messages",
              }}}}}}};
```

As with onRequest, you can implement it for all routes or for a single specific service.

To implement globally for all routes:

```ts
module.exports = {
  http: {
    onResponse: async (response, request, fetchedResponses) => {
      // Get the response object from a fetchedResponse object
      const receivedResponses = fetchedResponses.map((x) => x.response);
      // Concat the body of all responses
      const concatenatedData = receivedResponses
        .map((x) => x.body.toString())
        .join("--");
      // Send it back.
      return {
        status: 200,
        body: concatenatedData,
      };
    },
    routes: {
      "/users": {
        GET: {          
          services: {
            userservice: {
              type: "http",
              url: "http://localhost:6666/users",
            },
            messageservice: {
              type: "http" as "http",
              url: "http://localhost:6667/messages",
              }}}}}}};
```

When you implement it for a single service, the response object (first parameter) is the response received from that specific service. (In the other cases, the response object represents to aggregated response from all services.)

To implement for a single service on a route:

```ts
module.exports = {
  http: {    
    routes: {
      "/users": {
        GET: {          
          services: {
            userservice: {
              type: "http",
              url: "http://localhost:6666/users",
            },
            messageservice: {
              type: "http" as "http",
              url: "http://localhost:6667/messages",
              onResponse: async (response, request) => {
                response.body.isConfidential = true;
                return {
                  status: response.status,
                  body: response.body
                };
              }}}}}}}};
```