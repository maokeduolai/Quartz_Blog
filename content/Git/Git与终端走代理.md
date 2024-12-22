---
title: Git与终端走代理
date: 2024-12-15
tags:
    - Git
    - Proxy
---

```bash
# http代理
git config --global http.proxy 'socks5://127.0.0.1:7890'

# https代理
git config --global https.proxy 'socks5://127.0.0.1:7890'
```

```bash
# 取消http代理
git config --global --unset http.proxy

# 取消https代理
git config --global --unset https.proxy
```

```bash
# 终端代理
set HTTP_PROXY=http://127.0.0.1:1080
set HTTPS_PROXY=http://127.0.0.1:1080
```