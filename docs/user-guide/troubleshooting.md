---
layout: main
title: Troubleshooting
category: User Guide
menu: menu
toc:
    - title: Troubleshooting
      url: "#troubleshooting"
      active: true
    - title: "git clone" Step Fails
      url: "#git-clone-step-fails"
---

Troubleshooting
===============

"git clone" Step Fails
----------------------

```bash
error: xxxx bytes of body are still expected
fetch-pack: unexpected disconnect while reading sideband packet
fatal: early EOF
fatal: fetch-pack: invalid index-pack output
```

This could happen in server with network issues. The network might be too slow. Either diagnose network connection or, 
simply, re-run the Screwdriver pipeline couple more times.

More information can be found in
[this thread](https://stackoverflow.com/questions/66366582/github-unexpected-disconnect-while-reading-sideband-packet)
