```Bash
# http代理
git config --global http.proxy 'socks5://127.0.0.1:7890'

# https代理
git config --global https.proxy 'socks5://127.0.0.1:7890'
```

```Bash
# 取消http代理
git config --global --unset http.proxy

# 取消https代理
git config --global --unset https.proxy
```

```Bash
# 终端代理
set HTTP_PROXY=http://127.0.0.1:1080
set HTTPS_PROXY=http://127.0.0.1:1080
```