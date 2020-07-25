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
            }
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

### Rate Limiting State Management

Rate Limiting requires request counts to be stored somewhere - the options available currently are in-memory and Redis.  For production, you should always be using Redis for storing state. In-memory state exists primarily for development workflows. The default is in-memory state.

To store state in redis:

```ts
// host and port can be omitted if it's localhost and if it's the default port.
module.exports = {
  state: {
    type: "redis",
    host: "myredishost",
    port: 6379
  },
  http: {
    // the rest of the configuration...
  }
}      
```

To store the state in memory, can leave the 'state' field undefined. 
However, if you want to be more explicit about it:

```ts
// host and port can be omitted if it's localhost and if it's the default port.
module.exports = {
  state: {
    type: "memory",
  },
  http: {
    // the rest of the configuration...
  }
}      
```