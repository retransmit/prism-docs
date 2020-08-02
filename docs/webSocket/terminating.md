---
title: Terminating
---

A connection is terminated when a service posts a 'disconnect' message to the responseChannel ([previosly discussed](basics)).

The message needs to be in the following format. Remember that the id property uniquely identifies a connection; so services will need to track the 'ids' they're speaking to.

```ts
export type WebSocketResponse = {
  id: string;
  type: "disconnect";
  route: string;
  service: string;
};
```

Http Services which handle Web Socket connections via Prism's Url Polling mechanisms may also terminate connections in a similar manner. They need to send the response object described above as the response body when the service is polled.

For example with the Koa Web Server:

```ts
async function handleWebSocketRequest(ctx) {
  ctx.body = {
    id: "someid",
    type: "disconnect",
    route: "/quotes",
    service: "quoteservice",
  };
}
```
