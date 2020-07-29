---
title: Redis Web Backends 😈
---

One unique feature of Prism is that it in addition to sending requests to HTTP Endpoints, it can also serialize the [HttpRequest](http-request-type) as JSON and post them to Redis Queues/Channels. A non-HTTP service (let's call them Redis Services) subscribed to the channel will be able to pick them up and process it further. The non-HTTP service is also expected to post an [HttpResponse](http-response-type) back so that Prism can respond to the client.

Let's see how this works.

```ts
module.exports = {
  http: {
    routes: {
      "/users": {
        POST: {
          services: {
            userservice: {
              type: "redis",,
              requestChannel: "userserviceinput",
            },
            messagingservice: {
              type: "redis",
              requestChannel: "messagingserviceinput",
            },
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

The example above defines two Redis Http Services (userservice and messageservice) bound to the route POST /users. The type of the service needs to be specified as `type: "redis"`, as in the example above. When Prism receives a request at '/users', it serializes the request as JSON into a [RedisHttpRequest](redis-http-request) and posts it to the 'serviceChannel' specified for each service. In the example above, the channels are 'userserviceinput' and 'messagingserviceinput'.

The serialized [RedisHttpRequest](redis-http-request-type) is as below, where the request property is a serialized [HttpRequest](http-request-type).

```ts
export type RedisHttpRequest =
  | {
      type: "request";
      id: string;
      request: HttpRequest;
      responseChannel: string;
    }
  | {
      id: string;
      request: HttpRequest;
      type: "rollback";
    };
```

Each request comes with a responseChannel, which is the channel to which the service should post the response back. Prism will be subscribed to those channels.

The response is JSON serialized [RedisHttpResponse](redis-http-response-type) in the following form, where the response property contains an [HttpResponse](http-response-type).

The 'id' property of the response should be the same as the id received in the request; this is how Prism identifies the corresponding request. The 'service' property should be the name of the service.

```ts
export type RedisHttpResponse = {
  id: string;
  service: string;
  response: HttpResponse;
};
```

Let's look at an example (for messagingservice):

```ts
// Create a redis client.
const subscriber = redis.createClient();

// Subscribe to the 'messagingserviceinput' channel
subscriber.subscribe("messagingserviceinput");

// message handler
subscriber.on("message", (channel, message) => {
  const redisHttpRequest = JSON.parse(message);

  // Get the responseChannel
  const responseChannel = redisHttpRequest.responseChannel;

  // Get the request id
  const id = redisHttpRequest.id;

  // Unwrap the http request from the redis request
  const request = redisHttpRequest.request;

  // Let's say, userid 1 has 10 messages and userid 2 has 50
  const numMessages =
    request.body.userid === 1 ? 10 : request.body.userid === 2 ? 50 : 0;

  // Create the redis http response
  const response = JSON.stringify({
    id,
    service: "messagingservice",
    response: {
      status: 200, //HTTP 200
      body: {
        userid: request.body.userid,
        numMessages,
      },
    },
  });

  // Publish into the responseChannel
  publisher.publish(responseChannel, response);
});
```

Redis Http Services benefit from all the Prism features mentioned in the docs, such as Rate Limiting, Authentication, Circuit Breakers, etc. In fact, a single route can have a combination of regular Http Endpoints as well as Redis Http Services - as in this example below.

```ts
module.exports = {
  http: {
    routes: {
      "/users": {
        POST: {
          services: {
            userservice: {
              type: "http",,
              url: "http://localhost:6666/users",
            },
            messagingservice: {
              type: "redis",
              requestChannel: "messagingserviceinput",
            },
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

We have no idea if you'll need this, but go crazy. It's a fully supported feature.