---
title: Http Request Type
---

```ts
type HttpRequest = {
  path: string;
  method: HttpMethods;
  params?: {
    [key: string]: string;
  };
  query?: {
    [key: string]: string;
  };
  body?: string | Buffer | BodyObject | Array<any> | undefined;
  headers?: HttpHeaders;
  remoteAddress: string | undefined;
  remotePort: number | undefined;
};
```
