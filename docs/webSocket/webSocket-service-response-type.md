---
title: WebSocketServiceResponse
---

```ts
export type WebSocketServiceResponseBase = {
  id: string;
  service: string;
};

export type WebSocketServiceMessageResponse = {
  type: "message";
  message: string;
} & WebSocketServiceResponseBase;

export type WebSocketServiceDropResponse = {
  type: "drop";
  message?: string;
} & WebSocketServiceResponseBase;

export type WebSocketServiceResponse =
  | WebSocketServiceMessageResponse
  | WebSocketServiceDropResponse;
```
