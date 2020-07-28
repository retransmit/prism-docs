---
title: Web Sockets
sidebar_label: Web Sockets
---

Prism can act as a Gateway for WebSocket connections, allowing multiple backend services to send data to connected clients.

The best way to write backend services which talk to WebSocket clients (via Prism) is by using Redis Channels. When a WebSocket connection is initialized or when a message is received from a client, 

Let's look at a basic configuration:

```ts
const config: UserAppConfig = {
  webSocket: {
    routes: {
      "/quotes": {
        services: {
          quoteservice: {
            type: "redis" as "redis",
            requestChannel: "input",
          },
        },
      },
    },
    redis: {
      responseChannel: "output",
    },
  },
};
```
