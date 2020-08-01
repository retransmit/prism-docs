---
title: Basics
---

Prism can act as a Gateway for WebSocket connections, allowing multiple backend services to send data to connected clients. We recommend building WebSocket backend services using Redis Channels and Pub-Sub, but Prism can also service connections by periodically polling Http Services for new messages. Let's get into details.

A typical configuration which uses Redis pub-sub may be as below.

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
          chartservice: {
            type: "redis",
            requestChannel: "input",
          }}}}}};
```

Let's go step-by-step. When a client initiates a web socket connection, Prism will send a connect request in serialized JSON form to all participating services (defined under the same route) on their requestChannels . The serialized message looks like this:

```ts
export type RedisWebSocketConnectRequest = {
  id: string;
  type: "connect";
  route: string;
  path: string;
  remoteAddress: string | undefined;
  remotePort: number | undefined;
  request: string;
  responseChannel: string;
};
```

In the example above, the id field will uniquely identify a connection, and can be used for sending messages to the client. The responseChannel defined in the message is the channel on to which services should post responses. responseChannel will remain the same for a websocket connection; but may vary between connections.

Once a connect message is received (in the format above), services may start to send replies back to the client. Responses should be posted to the responseChannel (received in the 'connect' message earlier),

A message from a service should be in the following format, and the response property contains the string that will sent back to the client:

```ts
export type WebSocketResponse = {
  id: string;
  type: "message";
  route: string;
  service: string;
  response: string;
};
```

WebSocket connections are bi-directional; clients may also send messages to services. These messages are serialized and sent to all participating services defined for the WebSocket route. Note that betweem multiple messages from the client, the id and responseChannel will remain the same.

The message from a client looks like this:

```ts
export type RedisWebSocketMessageRequest = {
  id: string;
  type: "message";
  route: string;
  path: string;
  remoteAddress: string | undefined;
  remotePort: number | undefined;
  request: string;
  responseChannel: string;
};
```

So that's it. Prism offers a simple way for multiple backend services to talk to a client using a single connection. Read on for more details like authentication, dropping connections etc.
