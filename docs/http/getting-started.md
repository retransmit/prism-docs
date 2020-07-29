---
title: Getting Started
---

The first step is to install Prism. During the beta, you will need Node.JS. Docker images are coming soon.

```sh
# Assuming you have Node.JS installed
npm install -g @retransmit/prism
```

For running Prism, there are two command line options that you need to know.

- '-p' specifies the port on which requests will be accepted
- '-c' specifies the location of the configuration file.

Example:

```sh
prism -p 8000 -c /home/bruce/configs/prism.js
```

Note: The config file is a JS file, not JSON. If you prefer TypeScript (like us), the types for the config file are available at https://github.com/retransmit/prism/blob/master/src/types/index.ts.

Let's look at a basic config file. In this example, we are going to expose an end point which merges responses from two services (account service and a messaging service).

```ts
module.exports = {
  http: {
    routes: {
      "/dashboard": {
        GET: {
          services: {
            accountservice: {
              type: "http",
              url: "http://localhost:6666/account",
            },
            messagingservice: {
              type: "http",
              url: "http://localhost:6667/messages",
            },
          },
        },
      },
    },
  },
};
```

Prism accepts a request at the url '/dashboard' and passes it on to account service and messaging service. Once both responses are received, Prism will merge the results and send the final response back to the client. For instance, if accountservice returned `{ "name": "batman" }` and messaging service returned `{ "messages": 10 }` the final response will be `{ "name": "batman", "messages": 10 }`.

So that's a basic configuration file. There are some ready to use [example config files](https://github.com/retransmit/retransmit-examples) on GitHub.
