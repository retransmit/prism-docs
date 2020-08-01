---
title: Authentication
---

You often need authenticate your web socket clients. With Prism, you do that with the onConnect hook. When onConnect is defined in the configuration, the first message received from the client is passed to the onConnect handler. After evaluating the credentials in the message, onConnect may respond with `{ dropped: true }` to terminate the connection.

See the example configuration below:

```ts
module.exports = {
  webSocket: {
    onConnect: async (requestId: string, message: string) => {
      const authData = JSON.parse(message);
      const isValidUser = await doSomeAuthStuff(authData.username, authData.password);      
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
          }}}}}};
```

If onConnect is defined in configuration, the connection is not initialized until the first message is received from the user and evaluated. If the connection is not dropped, subsequent messages from the client do not flow through the onConnect handler.