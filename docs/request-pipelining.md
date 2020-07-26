---
title: Request Pipelining
---

By default, when Prism aggregates responses from multiple endpoints it sends all requests in parallel and merges the results. However, sometimes you might want to get the response from the first endpoint and send it to a second endpoint. Prism lets you do this with a feature called 'stages'.

Consider the configuration below:


```ts
module.exports = {
  http: {
    routes: {
      "/users": {
        POST: {
          services: {
            userservice: {
              type: "http",
              url: "http://localhost:6666/users",
              stage: 1,
            },
            messageservice: {
              type: "http",
              url: "http://localhost:6667/users",
              stage: 2,
              onRequest: async (request, responses) => {
                return {
                  handled: false,
                  request: {
                    ...request,
                    body: {
                      ...request.body,
                      userid: responses[0].response.body.userid,
                    }}};
              },
            } 
          }}}}}};
```

In the example above, POST to '/users' has two participating endpoints (userservice and messageservice). Userservice is defined as stage 1, while messageservice is stage 2. So Prism will flight the request to userservice first and pass that to onRequest handlers for subsequent services (in this case, messageservice).

In the example above, the onRequest handler for messageservice picks up the userid from the response obtained from userservice and appends it to the Request Body.

The responses object is an array of [FetchedHttpResponse](fetched-http-response-type) received from services in previous stages. In the example above, there were only two services - so responses[0] contained the response from the previous service. But if you have multiple services and stages, you could look at the 'service' and 'stage' properties of FetchedHttpResponse to identify the correct response.
