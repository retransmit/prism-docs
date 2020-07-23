---
id: http-request-type
title: Http Request Type
sidebar_label: Http Request
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
