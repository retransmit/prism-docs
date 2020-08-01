---
title: Basics
---

Prism can act as a Gateway for WebSocket connections, allowing multiple backend services to send data to connected clients. We recommend building WebSocket backend services using Redis Channels and Pub-Sub, but Prism can service connections by periodically polling Http Services as well. Let's get into details.

A typical configurations will be as below.

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

Let's go step-by-step. When a client initiates a web socket connection, Prism will send a connect request in serialized JSON form to all participating services (defined under the same route) on their requestChannels . The serialized message looks like this:

```ts
  id: string;
  type: "connect";
  route: string;
  path: string;
  remoteAddress: string | undefined;
  remotePort: number | undefined;
  request: string;
  responseChannel: string;
```

In the example above, the id field will uniquely identify a connection, and can be used for sending messages to the client. The responseChannel defined in the message is the channel on to which services should post responses; it is IMPORTANT to note that this is NOT the same as the responseChannel defined in ..... TODO



Whenever a message is received from the client, it is serialized and send sent to the channels mentioned in 'requestChannel'. Since multiple services can subscribe to the same channel, it's probably best for all services on a route ("/quotes" in the example above) to use the same channel to receive messages.

A serialized incoming message looks like this. 

```ts
export type RedisWebSocketConnectRequest = {
  id: string;
  type: "connect";
  route: string;
  path: string;
  remoteAddress: string | undefined;
  remotePort: number | undefined;
};
```

Now that the connection is initialized, the client and various services can exchange messages asynchronously. Messages from the same client arrive with the same id. Messages from clients look like the following. The message sent my the client is in the 'request' field.

```ts
export type RedisWebSocketMessageRequest = {
  id: string;
  type: "message";
  route: string;
  path: string;
  remoteAddress: string | undefined;
  remotePort: number | undefined;
  request: string;
};
```

Similarly, any participating service may send a response back to the client whenever they have data to send (since it's a web socket). 

Responses should look like this. Note that the id is used to uniquely identify the client.


Client messages are delivered on 



Since WebSocket offers asynchronous communication, services can send messages to clients whenver it wants. The 'id' field in the incoming message uniquely identifies an active connection, and does not change as long as the connection is kept alive. So to 
