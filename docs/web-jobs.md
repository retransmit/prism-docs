---
id: web-jobs
title: Web Jobs
sidebar_label: Web Jobs
---

Web Jobs are urls that Prism will call at regular intervals. It might help you clean up data, create reports etc.

Here's a simple configuration which calls 'http://localhost:6666/cleanup' every 50 seconds.

```ts
module.exports = {
  webJobs: {
    cleanupusers: {
      type: "periodic",
      url: "http://localhost:6666/cleanup",
      interval: 50,
    },
  },
};
```

You could of course put this behind a Prism route to balance load.

```ts
module.exports = {
  webJobs: {
    createreports: {
      type: "periodic",
      url: "http://prism.example.com/cronjobs",
      interval: 50,
    },
  },
  http: {
    "/cronjobs": {
      GET: {
        services: {
          reportservice: {
            type: "http",
            url: ["http://server1.example.com/createreports", "http://server2.example.com/createreports"],
          }}}}}};
```
