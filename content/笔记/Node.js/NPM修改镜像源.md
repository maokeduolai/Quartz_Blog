---
date: 2024-12-14
---

在执行 npm 命令时，默认会从 npm 官方镜像源获取软件包信息。由于官方镜像源位于国外，国内用户在下载和安装软件包时可能会遇到网络延迟问题。

使用以下命令查看当前使用的镜像源：

```bash
npm config get registry
```

>[!tip] 默认源地址在国外，从国内访问的速度肯定比较慢

## 如何修改镜像源

阿里旗下维护着一个完整的 npm 镜像源 [npmmirror 镜像站](https://npmmirror.com/)

1. 临时修改

```bash
npm install 软件名 --registry https://registry.npmmirror.com
```

2. 全局修改

```bash
npm config set registry https://registry.npmmirror.com
```

3. 使用第三方软件快速修改、切换 npm 镜像源 [nrm -- NPM Registry Manager](https://github.com/Pana/nrm)。nrm 不仅可以快速切换镜像源，还可以测试自己网络访问不同源的速度

```bash
# 安装 nrm
npm install -g nrm
```

```bash
# 列出当前可用的所有镜像源
> nrm ls
  npm ---------- https://registry.npmjs.org/
  yarn --------- https://registry.yarnpkg.com/
  tencent ------ https://mirrors.tencent.com/npm/
  cnpm --------- https://r.cnpmjs.org/
* taobao ------- https://registry.npmmirror.com/
  npmMirror ---- https://skimdb.npmjs.com/registry/
  huawei ------- https://repo.huaweicloud.com/repository/npm/
```

```bash
# 使用淘宝镜像源
nrm use taobao
```

```bash
# 测试访问速度
nrm test taobao
```

>[!info] 更多用法请查看 [nrm](https://github.com/Pana/nrm) GitHub

>[!quote]
>本文修改转载自：[NPM 国内加速，修改镜像源 | Sun .P.M](https://blog.fassr.com/src/npm/NPM%20%E5%9B%BD%E5%86%85%E5%8A%A0%E9%80%9F%EF%BC%8C%E4%BF%AE%E6%94%B9%E9%95%9C%E5%83%8F%E6%BA%90.html)