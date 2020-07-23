---
id: caching
title: Caching
sidebar_label: Caching
---

Response Caching can be enabled for all HTTP routes (with explicit opt-out) or for specific routes.

To enable for all services, define your cache settings under 'http'.

```ts
// Cache all routes for 20 seconds (ie, 20*1000 milliseconds)
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
          },
        },
      },
    },
  },
  caching: {
    expiry: 20000,
  },
};
```

You can also define caching for a specific route.

```ts
// Cache GET /users for 20 seconds
module.exports = {
  http: {
    routes: {
      "/users": {
        GET: {
          caching: {
            expiry: 20000,
          },
          services: {
            userservice: {
              type: "http" as "http",
              url: "http://localhost:6666/users",
            },
          }}}}}};
```

### In-Memory and Redis Cache

For production, you should always be using redis for storing state. In-memory state exists primarily for development workflows. The default is in-memory state.

To store application state in redis:

```ts
// host and port can be omitted if it's localhost and if it's the default port.
module.exports = {
  state: {
    type: "redis",
    host: "myredishost",
    port: 6379
  },
  http: {
    // routes and services..
  }
}      
```

To store the application state in memory, can leave the 'state' field undefined. 
However, if you want to be more explicit about it:

```ts
// host and port can be omitted if it's localhost and if it's the default port.
module.exports = {
  state: {
    type: "memory",
  },
  http: {
    // routes and services..
  }
}      
```

### Varying by headers, query or body

Cached data can be stored separately for combinations of header, query or body fields. The following example caches for a combination of a specific header named 'userid' and a query parameter named 'location'.

```ts
// Cache GET /users for 20 seconds
module.exports = {
  http: {
    routes: {
      "/users": {
        GET: {
          caching: {
            expiry: 20000,
            varyBy: {
              headers: ["userid"],
              query: ["location"]
            }
          },
          services: {
            userservice: {
              type: "http" as "http",
              url: "http://localhost:6666/users",
            },
          }}}}}};
```

