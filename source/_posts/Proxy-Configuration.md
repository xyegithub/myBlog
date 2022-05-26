---
title: Proxy Configuration
top: false
cover: false
toc: true
date: 2022-05-26 11:24:29
password:
summary:
description: Proxy Configuration
categories:
  - Programming
  - Proxy
tags:
  - Proxy
---

# In WSL

## Use the Windows proxy in WSL1

```bash
export http_proxy=socks://127.0.0.1:10808
export https_proxy=socks://127.0.0.1:10808
```

Then verify the setting with `curl google.com`.

When `http(s)_proxy` is set you can see the log in your proxy on Windows when
you run `curl google.com`. Be careful about the ip, the port `10808` which is
defined in your windows proxy app, and `socks` is the protocol which also
defined in your windows proxy app.
