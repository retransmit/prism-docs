---
title: Request Transform
---

Just as with HTTP requests, you can transform WebSocket requests as well. This can be done for all requests, or for a specific route or for a specific service.

See the example configuration below:

```ts
module.exports = {
  webSocket: {
    onConnect: async (requestId: string, message: string) => {
      const authData = JSON.parse(message);
      const isValidUser = await doSomeAuthStuff(
        authData.username,
        authData.password
      );
      if (isValidUser) {
        return { drop: false };
      } else {
        return { drop: true };
      }
    },
    routes: {
      "/quotes": {
        services: {
          quoteservice: {
            type: "http" as "http",
            url: "http://localhost:6666/quotes",
          },
        },
      },
    },
  },
};
```

Drop can also optionally send a message before disconnecting. Just use `{ drop: true, message: "Bad password." }`.

If the connection is not dropped in the onConnect handler, subsequent messages from the client will not flow through the onConnect handler.
