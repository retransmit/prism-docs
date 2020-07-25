---
title: HTTPS and CORS
---

To enable HTTPS, use the 'useHttps' option.

```ts
module.exports = {
  http: {
    routes: {
      // omitted for brevity...
    },
  },
  useHttps: {
    key: readFileSync(PATH_TO_KEY),
    cert: readFileSync(PATH_TO_CERT),
  },
};
```

To enable CORS, use the 'cors' option.

```ts
module.exports = {
  http: {
    routes: {
      // omitted for brevity...
    },
  },
  cors: {
    origin: "*",
    allowMethods: "GET,HEAD,PUT,POST,DELETE,PATCH",
  },
};
```

