---
id: response-aggregation
title: Response Aggregation
sidebar_label: Response Aggregation
---

Let's borrow the configuration from the previous article.

```ts
module.exports = {
  http: {
    routes: {
      "/dashboard": {
        GET: {
          services: {
            accountservice: {
              type: "http" as "http",
              url: "http://localhost:6666/account",
            },
            messagingservice: {
              type: "http" as "http",
              url: "http://localhost:6667/messages",
            }
          }}}}}};
```

As previously discussed, if accountservice returned `{ "name": "batman" }` and messaging service returned `{ "messages": 10 }` the final response will be `{ "name": "batman", "messages": 10 }`.

Prism merges responses that are received (into a final response body) in the same order in which services are defined in the configuration file. So if account service returned `{ "name": "bruce wayne", "messages": 10 }` instead of `{ "messages": 10 }`, the final response will be `{ "name": "bruce wayne", "messages": 10 }`.

### Merge Fields

Fields being overwritten can be a problem. Fortunately, there's an easy fix - 'mergeField'.

```ts
module.exports = {
  // parts omitted for brevity
  services: {
    accountservice: {
      type: "http" as "http",
      url: "http://localhost:6666/account",
      mergeField: "account",
    },
    messagingservice: {
      type: "http" as "http",
      url: "http://localhost:6667/messages",
      mergeField: "inbox"
    },
  }
```

This puts the response from account service into a field named 'account', and that from messaging service into a field named 'inbox'. In this case, the final response body will be

```json
{
  "account": {
    "name": "batman"
  },
  "inbox": {
    "name": "bruce wayne",
    "messages": 10
  }
}
```
