---
title: Circuit Breaker
---

Circuit breaker is a design pattern that is used to detect failures and encapsulates the logic of preventing a failure from constantly recurring, during maintenance, temporary external system failure or unexpected system difficulties.

```ts
module.exports = {
  http: {
    routes: {
      "/users": {
        GET: {
          services: {
            userservice: {
              type: "http" as "http",
              url: "http://localhost:6666/users",
            },
            messagingservice: {
              type: "http" as "http",
              url: "http://localhost:6667/users",
            },
          },
        },
      },
    },
    circuitBreaker: {
      maxErrors: 3,
      duration: 150,
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
          circuitBreaker: {
            maxErrors: 3,
            duration: 150,
          },
          services: {
            userservice: {
              type: "http",
              url: "http://localhost:6666/users",
            }}}}}}};
```

### Circuit Breaker State Management

Circuit Breaker requires failures to be stored somewhere - the options available currently are in-memory and Redis.  For production, you should always be using Redis for storing state. In-memory state exists primarily for development workflows. The default is in-memory state.

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