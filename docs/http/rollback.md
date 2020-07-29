---
title: Rollback
---

When Prism is aggregating responses from multiple backend services, it is possible that one of them might fail. When that happens, Prism can notify the other services that they might need to rollback the last operation. For instance, if an HTTP POST request to /users calls userservice and accountservice and accountservice fails after userservice has already succeeded, it might be desirable to undo the changes in userservice for consistency.

This can be done with the rollback function. Let's take an example.

```ts
module.exports = {
  http: {
    routes: {
      "/users": {
        POST: {
          services: {
            userservice: {
              type: "http" as "http",
              url: "http://localhost:6666/users",
              rollback: req => ({
                ...req,
                method: "DELETE",
                path: `http://localhost:6666/users/${req.body.userid}`
              })
            },
            accountservice: {
              type: "http" as "http",
              url: "http://localhost:6667/accounts",
            },
          }}}}}};
```

The rollback function should take the original request and return a new request to be sent to the backend service so that it call rollback previously changes. In the example above, if the call to accountservice failed (but userservice succeeded) Prism will send an HTTP DELETE to the '/users/remove' url (inside userservice). 


