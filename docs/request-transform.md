---
id: request-transform
title: Request Transform
---

Prism gives you a simple way to transform requests sent to various services.

The simplest approach to use here is the 'mapping' property in configuration. In the following example the header field "x-app-token" is mapped to the field "x-auth-token". So userservice will see "x-auth-token" instead.

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
              mapping: {
                headers: {
                  include: {
                    "x-app-token": "x-auth-token",
                  }
                }}}}}}}}};
```

The same technique can be applied to the request body. In the following example, the field 'username' in the request body is mapped to 'uname' before sending to accountservice.

```ts
module.exports = {
  // parts omitted for brevity
  services: {
    accountservice: {
      type: "http",
      url: "http://localhost:6666/users",
      mapping: {
        headers: {
          include: {
            username: "uname",
          },
        }}}}}
```

You could exclude things too. The following example excludes a specific header as well as a field from the request body.

```ts
module.exports = {
  // parts omitted for brevity
  services: {
    userservice: {
      type: "http" as "http",
      url: "http://localhost:6666/users",
      mapping: {
        headers: {
          exclude: ["x-auth-token"],
        },
        fields: {
          exclude: ["username"],
        },
      }}}}
```

### Programmatic Modification

If your needs are more complex, you can use JavaScript snippets to achieve full control over the request object. To do this, you define the onRequest hook for a service as you can see below.

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
            },
            messageservice: {
              type: "http" as "http",
              url: "http://localhost:6667/messages",
              onRequest: async (request, responses) => {
                return {
                  handled: false,
                  request: {
                    ...request,
                    headers: { ...request.headers, "x-app-name": "something" }
                  }}}}}}}}}};
```

Your custom onRequest hook needs to return a value in the following structure, or it can return nothing (void/undefined). 

```ts
export type Result = 
  | { handled: true; response: HttpResponse; }
  | { handled: false; request: HttpRequest }
```

If the handled is false, you need to return a modified request. If handled is set to true, Prism will expect a response which can be immediately sent to the client. As you would expect, if handled is set to true none of the backend services are invoked. If it returns nothing, Prism proceeds as if without the hook.

What are HttpRequest and HttpResponse objects? See the type definition for [HttpRequest](http-request-type) and [HttpResponse](http-response-type).

Let's look at an example.

```ts
module.exports = {
  http: {
    routes: {
      "/users": {
        POST: {
          onRequest: async (request, responses) => {
            return {
              handled: true,
              response: {
                status: 200,
                body: 'It worked!'
              }
            }
          },
          services: {
            userservice: {
              type: "http",
              url: "http://localhost:6666/users",
            },
            messageservice: {
              type: "http" as "http",
              url: "http://localhost:6667/messages",
              }}}}}}};
```

Note that the onRequest function can return a promise. So you're free to call an external service as well. If it returned void (or undefined), the Prism proceeds with the request as usual.

Like this:
```ts
module.exports = {
  //omitted for brevity
  onRequest: async (request, responses) => {
    const response = await getLocationFromCache(request.body.location)
    if (response) {
      return {
        handled: true,
        response: {
          status: 200,
          body: 'It worked!'
        }
      }
    }}}    
```

In the previous examples, you've seen onRequest being applied for a route. But it can be applied to all routes as well as a single specific service.

To apply globally to all routes:

```ts
module.exports = {
  http: {
    onRequest: async (request, responses) => {
      if (!request.headers["x-custom-auth-token"]) {
        return {
          handled: true,
          response: {
            status: 401,
            body: 'No cookie for you!'
          }
        }
      }
    },
    routes: {
      "/users": {
        POST: {          
          services: {
            userservice: {
              type: "http",
              url: "http://localhost:6666/users",
            },
            messageservice: {
              type: "http" as "http",
              url: "http://localhost:6667/messages",
              }}}}}}};
```

Sometimes you want to customize a request to a single route. That's easy.

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
              onRequest: async (request, responses) => {
                const responseFromCache = await getUserFromCache(request.headers.userid);
                if (responseFromCache) {                
                  return {
                    handled: true,
                    response: {
                      status: 200,
                      body: responseFromCache.data,
                    }
                  };
                }}},
            messageservice: {
              type: "http" as "http",
              url: "http://localhost:6667/messages",
              }}}}}}};