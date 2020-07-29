---
title: Load Balancing
---

Prism supports basic round-robin load balancing now with some customizability.

In its simplest form, you could provide an array of endpoints for a service and Prism will randomly pick one of them. Your configuration should look like this.

```ts
// Example 1
module.exports = {
  http: {
    routes: {
      "/users": {
        GET: {
          services: {
            userservice: {
              type: "http" as "http",
              url: ["http://server1.example.com/users", "http://server2.example.com/users"],
            }}}}}}};
```

For more advanced use cases, Prism allows you to dynamically provide endpoints by defining a urlSelector (getUrl in the example below). The first argument to the urlSelector is the value of the 'url' array defined in the configuration.

```ts
// Example 1
module.exports = {
  http: {
    routes: {
      "/users": {
        GET: {
          services: {
            userservice: {
              type: "http" as "http",
              url: ["http://server1.example.com/users", "http://server2.example.com/users"],
              getUrl: async (urls) => {
                const healthyServers = await getHealthyServers("userservice");
                return healthyServers.map(
                  (x) => `http://${healthyServers}/users`
                );
              },
            }}}}}}};
```

### Load Balancing with Retransmit Stack

When Prism is integrated with Retransmit Stack (which includes Health Monitoring), Load Balancing avoids sending requests to servers which are down. This feature is not available when Prism is used in standalone mode.
