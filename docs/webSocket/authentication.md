---
title: Authentication
---

If you need to authenticate your web socket clients, you can do that with the onConnect hook. When onConnect is defined in the configuration, the first message received from the client is passed to the onConnect handler. After evaluating the credentials in the message, onConnect may respond with `{ drop: true }` to terminate the connection.

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
