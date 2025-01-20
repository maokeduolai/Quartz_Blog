---
title: 美观好用：Obsidian+Quartz+Cloudflare Pages快速博客发布指南
date: 2025-1-20
tags:
    - Obsidian
    - Quartz
    - Cloudflare
---

# 起源

学各种东西的过程中零零散散整理了不少文字，也有一些自己的想法，一直放在本地过段时间就再也想不起来在哪儿了，不如拿出来放在个人站上，还可以与他人讨论。

最开始考虑了Wordpress，但感觉许多配置都无法符合自己的预期，且难以更深度地自定义，于是转向寻找其他方案。考虑到本地的文档资料都是在Obsidian中使用Markdown整理的，因此最好寻找一个可以直接托管Markdown的工具框架，减少额外步骤。

兜兜转转比较了不少工具之后，最后落在了Quartz上。

# Quartz

先来看看开发者自己对Quartz的定义：

>Quartz is a fast, batteries-included static-site generator that transforms Markdown content into fully functional websites. Thousands of students, developers, and teachers are already using Quartz to publish personal notes, websites, and digital gardens to the web.

显然，这正是一个专为Markdown笔记转换为个人站点所制作的工具。如你所见，本站点就是使用Quartz搭建的。除了本站所示的一些功能外，它还有许多额外插件。

本文主要以介绍实际部署为主，对于其他功能与实现细节，可以访问 [Quartz 官方站点](https://quartz.jzhao.xyz/) 自行了解。

接下来，我将逐步介绍实际使用部署的方法。

## 环境要求

Quartz基于Node.js，对于Quartz v4.4.0，需要安装：

- Node.js ≥ v20，建议安装操作系统对应的[v20.9.0](https://nodejs.org/dist/v20.9.0/)版本。常用版本安装包已在下面直接列出：
    - Windows 64位：https://nodejs.org/dist/v20.9.0/node-v20.9.0-x64.msi
    - Windows 32位：https://nodejs.org/dist/v20.9.0/node-v20.9.0-x86.msi
    - MacOS ARM64（M系列）/X64（Intel）：https://nodejs.org/dist/v20.9.0/node-v20.9.0.pkg
- [Git最新版本](https://git-scm.com/downloads)

安装完成后，进入系统自带终端，键入以下命令检查安装是否正确成功：

```bash
# 检查Node版本
node -v  # 输出：v20.9.0

# 检查Git安装是否成功
git -v   # 输出：git version x.xx.x
```

确保安装没问题后，即可进到下一步。

## 克隆仓库与初始化

```bash
# 克隆Quartz远程仓库
git clone https://github.com/jackyzha0/quartz.git

# 进入目录
cd quartz
```

![克隆仓库](https://pic.xulihang.work/2025/01/Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南.webp)

当确认克隆完成后，进入到安装相关依赖的步骤。

这里请先参考 [NPM修改镜像源](../Node.js/NPM修改镜像源.md) 中的方法配置，免除国内网络环境可能导致的问题。

```bash
# 安装相关依赖
npm i
```

![安装相关依赖](https://pic.xulihang.work/2025/01/Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_1.webp)

没有错误提示即为安装成功，可以进入Quartz初始化步骤。

1. 选择初始文档，此处我选择从`空目录`开始。使用上下键选择选项，回车确认。
    ![空目录](https://pic.xulihang.work/2025/01/Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_2.webp)
2. 对于文档中链接的处理方式，直接选择默认的`最短路径`即可。若后续发现链接处理有误，还可以在Quartz根目录的`quartz.config.ts`文件中修改。
    ![链接处理方式](https://pic.xulihang.work/2025/01/Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_3.webp)
3. 当最后呈现图中的提示时，代表我们的初始化操作已完成。
    ![初始化完成](https://pic.xulihang.work/2025/01/Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_4.webp)

## 编写测试首页

现在的Quartz中还没有任何内容，我们需要为我们的个人站先创建一个首页。

在Quartz中，所有内容的创作都是基于Markdown的，因此，首页也是一个`.md`文件。

找到Quartz的`quartz\content`文件夹，里面已经有一个默认的`index.md`首页文件了。打开之后我们就可以开始编辑。

![示例index.md文件](https://pic.xulihang.work/2025/01/Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_5.webp)

这里有一个Quartz中定义的前缀项（两个`---`包围的内容），允许指定笔记文件额外的元数据，不会显示在正文中。

在不安装额外插件的情况下，Quartz原生支持的前缀字段包括以下这些：

- `title`：页面的标题。如果没有提供，Quartz将使用文件名作为标题。
- `description`：用于给搜索引擎爬虫提供的页面概要。
- `permalink`：一个自定义的页面 URL，即使文件路径发生变化，该 URL 也会保持不变。
- `aliases`：此笔记的其他名称。这是一个字符串列表。
- `tags`：此笔记的标签。
- `draft`：控制是否发布该笔记。这是在Quartz中使页面私有化的一种方法。
- `date`：表示笔记发布日期的字符串。通常使用`YYYY-MM-DD`格式。

在这里我建议对一篇准备发布的笔记最少提供`title`、`description`、`tags`与`date`这几个元数据。

再简单修改一下正文，我们就可以测试首页了。

## 本地构建Quartz

以管理员权限启动终端，并来到Quartz根目录下。运行构建命令：

```bash
npx quartz build --serve

# 若要指定运行的端口，可以使用--port
npx quartz build --serve --port 1234
```

![本地构建Quartz](https://pic.xulihang.work/2025/01/Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_6.webp)

看到蓝色那行提示就意味着我们的构建是成功的，此时访问`http://localhost:8080`就能看到首页啦！

![测试首页](https://pic.xulihang.work/2025/01/Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_9.webp)

## 配置Quartz

如前文所述，Quartz是一个可自定义项非常丰富的工具。那么在一开始，我们需要进行哪些简单的配置呢？

打开`quartz`根目录文件夹，找到`quartz.config.ts`即可开始编辑。

- `pageTitle`：站点名称。
- `pageTitleSuffix`：浏览器标题后缀。例如设置为` - 毛可多来`，那么所有浏览器页面标题的末尾就会跟一段这个。
- `locale`：指定站点语言。会影响Quartz界面的语言，根据内容面向对象设置，例如中文就可以设置为`zh-CN`。
- `baseUrl`：指定站点根路径。例如我部署博客的根目录为`blog.xulihang.work`，那么此处就应填入这一串字符；若后期建在github.io等服务上，那么此处还应包含子路径，例如`maokeduolai.github.io/quartz`。
    >[!warning] 此处不应填入任何协议，例如`https://`等

    - Quartz尝试尽可能使用相对路径，但为保险起见，指定站点根路径依然是一个好的选择。
- `defaultDateType`：页面发布日期的来源。可选依据文件的创建时间、修改时间或发布时间，对应`created`、`modified`、`published`三种。
- `ignorePatterns`：按通配符忽略`content`文件夹中的文件。使用数组指定，例如：`["private", "templates", ".obsidian"]`。

余下的各项配置可以不进行调整，保留缺省项即可。

## 添加页面与发布测试

将需要发布的页面Markdown文件复制粘贴到`quartz\content`文件夹中，参考[编写测试首页](#编写测试首页)中的前缀字段规则，检查元数据是否完整。

然后我们就可以在发布前进行最后测试了。

以管理员权限启动终端，并来到Quartz根目录下。运行构建命令：

```bash
npx quartz build --serve
```

检查我们此前进行的各项更改是否均已生效并符合预期，添加的页面是否均已显示。若有所出入，可以更改并再次检查确认。

## 配置Github存储仓库

现在，本地的各项配置测试均已完成，我们可以尝试将仓库同步到自己的Github内了。

>[!info] 本指南不提供详细的Git/Github使用教学，默认大家已有相关基础

根据下图所示的选项创建一个空仓库。

![创建Github存储仓库](https://pic.xulihang.work/2025/01/美观好用：Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南.webp)

>[!warning] 不要使用`README` 、`license`或`.gitignore`文件初始化新仓库。

创建成功后，我们就可以获得图上所示的远程仓库链接。

![远程仓库链接](https://pic.xulihang.work/2025/01/美观好用：Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_1.webp)

在终端内导航到Quartz文件夹根目录，分步执行以下命令，将`REMOTE-URL`替换为你自己的远程仓库链接。

```bash
# 列出所有远程仓库的详细信息
git remote -v

# 你应该会见到以下输出：
origin  https://github.com/jackyzha0/quartz.git (fetch)
origin  https://github.com/jackyzha0/quartz.git (push)
upstream        https://github.com/jackyzha0/quartz.git (fetch)
upstream        https://github.com/jackyzha0/quartz.git (push)
```

```bash
# 将远程仓库链接设置为你自己的远程仓库链接
git remote set-url origin REMOTE-URL

# 如果在之前的输出中，你没有见到upstream及其对应链接，那么请执行下面的命令，方便在Quartz项目更新时自动更新
git remote add upstream https://github.com/jackyzha0/quartz.git
```

然后，执行下面的命令以将内容推送到远程仓库。

```bash
npx quartz sync --no-pull
```

>[!warning] 注意不要使用Git命令自行推送

完成推送后输出的内容应当类似下图所示，若出现其他警告错误，请根据错误信息检查问题并修正。

![推送到远程仓库](https://pic.xulihang.work/2025/01/美观好用：Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_2.webp)

此后每次修改需要更新远程仓库时，仅需通过以下命令完成即可。

```bash
npx quartz sync
```

# Cloudflare Pages

现在终于到了我们将博客页面向全世界发布的时候了。对于大部分使用者来说，并不想花大力气在服务器相关搭建调试上，也不想每年花成百上千来购买服务，因此我们选择了一种经济且简单的方式——Cloudflare Pages。

Cloudflare Pages提供静态网站的托管服务，支持通过 Git 仓库（如 GitHub）直接部署项目，也可以通过本地上传或命令行工具进行部署。

其免费提供每月500次的免费构建、100个自定义域名以及20000个文件（单文件＜25MiB）的存储。简单来说就是一个人用不掉，具体的相关限制可以参见[Limits · Cloudflare Pages docs](https://developers.cloudflare.com/pages/platform/limits/)。

## 配置

1. 访问[Cloudflare Dashboard](https://dash.cloudflare.com/)注册并登录自己的账号。
2. 在左侧菜单列选择Compute(Workers)→Workers和Pages。

    ![Pages](https://pic.xulihang.work/2025/01/美观好用：Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_3.webp)

3. 选择创建Pages并连接到Git。

    ![连接Git](https://pic.xulihang.work/2025/01/美观好用：Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_4.webp)

4. 进行授权验证后，选择对应的存储仓库。

    ![选择存储仓库](https://pic.xulihang.work/2025/01/美观好用：Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_5.webp)

5. 像图中这样填好对应的各项设置后，单击“保存并部署”，Cloudflare Pages就会开始进行部署。在一般情况下，一分钟后就能看到部署网站的效果。

    ![部署](https://pic.xulihang.work/2025/01/美观好用：Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_6.webp)

6. 之后在每次将本地更新同步到远程仓库时，Cloudflare Pages会根据该配置自动重新构建并更新站点。
7. 查看新创建的Pages，可以看到相关的域名。如果有自己的域名，也可以在自定义域中进行添加。

    ![Pages域名](https://pic.xulihang.work/2025/01/美观好用：Obsidian+Quartz+Cloudflare%20Pages快速博客发布指南_8.webp)

关于Cloudflare相关的国内访问速度优化等等优化配置不在此处详述，可以参见互联网上他人的文章折腾。

关于博客图片的问题，待下次有空我再来给大家分享，主要依靠的是Cloudflare的R2对象存储服务，白嫖香香！有能力的也可以尝试一下自己折腾，并不复杂。

本篇快速博客发布指南就到此为止了，希望大家都能拥有一个美观实用的个人博客😊