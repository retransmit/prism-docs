---
title: Streams
---

The request-response pipelines in Prism are not ideal when services send back large binary blobs. There's a streaming mode to handle such routes. Enable stream mode with the 'useStream' property on a route.

```ts
module.exports = {
  http: {
    routes: {
      "/users": {
        GET: {
          useStream: true,
          services: {
            userservice: {
              type: "http" as "http",
              url: "http://localhost:6666/users",
            }}}}}}};
```

Streams have some limitations. Since Prism pipes the response from the endpoint directly to the client, only one service is allowed for a route in stream mode. Response Transformation is disabled for streams, so the response from the service is piped back unmodified to the client.

Note that an API Gateway is not ideal for very large files such as videos; those should be handled with CDNs or directly from an HTTP server such as ngnix.