---
title: Basics
---

Prism can act as a Gateway for WebSocket connections, allowing multiple backend services to send data to connected clients.

The best way to write backend services which talk to WebSocket clients (using Prism) is by using Redis Channels. See the configuration below.

```ts
const config: UserAppConfig = {
  webSocket: {
    routes: {
      "/quotes": {
        services: {
          quoteservice: {
            type: "redis",
            requestChannel: "input",
          },
          messageservice: {
            type: "redis",
            requestChannel: "input",
          }
        },
      },
    },
    redis: {
      responseChannel: "output",
    },
  },
};
```

Let's go step-by-step. When a new websocket connection is established on "/quotes", Prism will post an onConnect message to the requestChannels mentioned in the configuration against that endpoint.

It is not necessary to handle the onConnect message. But if you want to, it looks looks like this for Redis WebSocket Services:

```ts
export type WebSocketRequestBase = {
  type: "connect";
  id: string;
  route: string;
  path: string;
  remoteAddress: string | undefined;
  remotePort: number | undefined;
  responseChannel: string;
};
```




When a WebSocket connection is initialized or when a message is received from a client, 

Let's look at a basic configuration:

