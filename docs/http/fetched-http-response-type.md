---
title: Fetched Http Response
---

This is a wrapper around the data fetched from a backend service. The response returned by the service is available under the 'response' property. That's usually what you're interested in.

```ts
// Output of processMessage()
export type FetchedHttpResponse = {
  type: "http" | "redis";
  id: string;
  service: string;
  time: number;
  route: string;
  path: string;
  method: HttpMethods;
  response: HttpResponse;
  stage: number | undefined;
};
```
