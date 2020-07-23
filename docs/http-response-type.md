---
id: http-response-type
title: Http Response
sidebar_label: Http Response
---

```ts
export type HttpResponse = {
  status?: number;
  redirect?: string;
  cookies?: HttpCookie[];
  headers?: IncomingHttpHeaders;
  body?: string | Buffer | BodyObject | Array<any> | undefined;
  contentType?: string;
};
```
