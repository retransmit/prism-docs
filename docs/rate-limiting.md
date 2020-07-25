---
title: Rate Limiting
---

Rate limiting cap the number of requests coming in from a certain IP in a specified duration. Further responses will get an HTTP 429 - Too many requests, .

The following configuration disallows an IP from calling any specific route more than 4 times every 1000 milliseconds (1 second). Since this is defined globally, all routes will have rate limiting applied.

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
          },
        },
      },
    },
    rateLimiting: {
      type: "ip",
      maxRequests: 4,
      duration: 1000,
    },
  },
};
```

You can define it for a specific route too. In the following example GET /users cannot be called more than 4 times every second.

```ts
module.exports = {
  http: {
    routes: {
      "/users": {
        GET: {
          rateLimiting: {
            type: "ip",
            maxRequests: 4,
            duration: 1000,
          },
          services: {
            userservice: {
              type: "http",
              url: "http://localhost:6666/users",
            }}}}}}};
```
