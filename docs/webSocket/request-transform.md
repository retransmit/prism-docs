---
title: Request Transform
---

Just as with HTTP requests, you can transform WebSocket requests as well. This can be done for all requests, or for a specific route or for a specific service.

See the example configuration below:

```ts
module.exports = {
  webSocket: {
    onRequest: async (request: WebSocketClientRequest) => {
      return {
        handled: true,
        response: {
          id: request.id,
          service: "",
          type: "message",
          message: "GOOG:1000.00",
        },
      };
    },
    routes: {
      "/quotes": {
        services: {
          quoteservice: {
            type: "http" as "http",
            url: "http://localhost:6666/quotes",
          }}}}}};
```

The result of the async onRequest handler should be the following structure.

```ts
export const Result = 
  { handled: true; response?: WebSocketServiceResponse } |
  { handled: false; message: string } |
  void
```

If handled is true, you need to return a [WebSocketServiceResponse](webSocket-service-response-type). If handled is false, you need to return a modified (or unmodified) string which will be used as the input message (in place of the original message). 

As in the HTTP pipeline, you may also define onRequest handles for specific routes or even services. The following hook only affects a connection received at the /routes endpoint. 

```ts
module.exports = {
  webSocket: {
    routes: {
      "/quotes": {
        onRequest: async (request: WebSocketClientRequest) => {
          return {
            handled: true,
            response: {
              id: request.id,
              service: "",
              type: "message",
              message: "GOOG:1000.00",
            },
          };
        },
        services: {
          quoteservice: {
            type: "http" as "http",
            url: "http://localhost:6666/quotes",
          }}}}}};
```
