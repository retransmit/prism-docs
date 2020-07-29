---
title: Authentication
---

Currently Prism only supports JWT based authentication. It internally uses the [https://github.com/auth0/node-jsonwebtoken](jsonwebtoken) library. So all the options supported by jsonwebtoken can be used here.

JWT is typically passed in the Authorization header as follows:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIXVCJ9...
```

If this is how you are passing JWT, the configuration is as simple as:

```ts
// Example 1
module.exports = {
  http: {
    routes: {
      "/users": {
        GET: {
          caching: {
            expiry: 200,
          },
          services: {
            userservice: {
              type: "http" as "http",
              url: "http://localhost:6666/users",
            },
          },
        },
      },
    },
    authentication: {
      type: "jwt",
      key: "secret", //publicKey or shared secret
    },
  },
};
```

Authentication can also be applied for individual routes.

```ts
// Example 2
module.exports = {
  // parts omitted for brevity
  services: {
    userservice: {
      type: "http" as "http",
      url: "http://localhost:6666/users",
      authentication: {
        type: "jwt",
        key: "secret", //publicKey or shared secret
      },
    },
  },
};
```

If Authentication is applied for all services (as in Example 1) but you specifically want to exclude some services, specify 'none' for the authentication field.

```ts
// Example 3
module.exports = {
  // parts omitted for brevity
  services: {
    messagingservice: {
      type: "http" as "http",
      url: "http://localhost:6667/messages",
      authentication: "none",
    },
  },
};
```

### The JWT Token Field

If the JWT is not passed in the 'Authorization' header, you need to specify how to fetch it from the request.

If it's in a field in the header, use the jwtHeaderField option:

```ts
// Example 4
module.exports = {
  // parts omitted for brevity
  services: {
    userservice: {
      type: "http" as "http",
      url: "http://localhost:6666/users",
      authentication: {
        type: "jwt",
        key: "secret", //publicKey or shared secret
        jwtField: "x-myapp-jwt",
      },
    },
  },
};
```

If you need more control, implement the getJwt function. The following example gets it from the request body.

```ts
// Example 5
module.exports = {
  // parts omitted for brevity
  services: {
    userservice: {
      type: "http" as "http",
      url: "http://localhost:6666/users",
      authentication: {
        type: "jwt",
        key: "secret", //publicKey or shared secret
        getJwt: (request) => request.body.jwt,
      },
    },
  },
};
```

### Customize Token Verification

By default, Prism will simple check the existence of the JWT. In many cases, you want to do more - such as whether certain roles are present. That can be done with the 'verify' hook.

```ts
// Example 6
module.exports = {
  // parts omitted for brevity
  services: {
    userservice: {
      type: "http" as "http",
      url: "http://localhost:6666/users",
      authentication: {
        type: "jwt",
        key: "secret", //publicKey or shared secret
        verify: async (token) => token.role === "chatuser",
      },
    },
  },
};
```

### Custom Error Response

Prism sends HTTP 401 with the Body "Unauthorized." as the response. But this can be changed with the 'errorStatus' and 'errorBody' options. errorBody can also take a JSON object.

```ts
// Example 7
module.exports = {
  // parts omitted for brevity
  services: {
    userservice: {
      type: "http" as "http",
      url: "http://localhost:6666/users",
      authentication: {
        type: "jwt",
        key: "secret", //publicKey or shared secret
        errorStatus: 400,
        error: "You are not allowed to access this page.",
      },
    },
  },
};
```

### Error Logging

The reason why token validation failed can be obtained with the onError hook.

```ts
// Example 8
module.exports = {
  // parts omitted for brevity
  services: {
    userservice: {
      type: "http" as "http",
      url: "http://localhost:6666/users",
      authentication: {
        type: "jwt",
        key: "secret", //publicKey or shared secret
        onError: (error, request) => {
          console.log(error);
        },
      }}}};
```


