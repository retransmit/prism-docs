---
title: onConnect Hook
---

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

