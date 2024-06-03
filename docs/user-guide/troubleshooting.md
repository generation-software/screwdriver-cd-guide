---
layout: main
title: Troubleshooting
category: User Guide
menu: menu
toc:
    - title: Troubleshooting
      url: "#troubleshooting"
      active: true
    - title: git clone Step Fails
      url: "#git-clone-step-fails"
    - title: Republishing Template or Command Fail with 403
      url: "#republishing-template-or-command-fail-with-403"
---

Troubleshooting
===============

"git clone" Step Fails
----------------------

```console
error: xxxx bytes of body are still expected
fetch-pack: unexpected disconnect while reading sideband packet
fatal: early EOF
fatal: fetch-pack: invalid index-pack output
```

This could happen in server with network issues. The network might be too slow. Either diagnose network connection or, 
simply, re-run the Screwdriver pipeline couple more times.

More information can be found in
[this thread](https://stackoverflow.com/questions/66366582/github-unexpected-disconnect-while-reading-sideband-packet)

Republishing Template or Command Fail with 403
----------------------------------------------

```console
09:53:35 $ template-publish --tag latest
09:53:35 Error: 403 Reason "Not allowed to publish this template"
09:53:35     at throwError (/usr/local/lib/node_modules/screwdriver-template-main/node_modules/screwdriver-request/index.js:14:17)
09:53:35     at /usr/local/lib/node_modules/screwdriver-template-main/node_modules/screwdriver-request/index.js:62:28
09:53:35     at process.processTicksAndRejections (node:internal/process/task_queues:95:5) {
09:53:35   statusCode: 403,
09:53:35   code: 'ERR_NON_2XX_3XX_RESPONSE'
09:53:35 }
```

Simply delete the template or command that has already been published and try again.
