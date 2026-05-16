# GitHub-Proxy

一个轻量级 GitHub 文件及 API 加速代理,使用 **Cloudflare Workers** 提供代理能力,**GitHub Pages** 托管前端页面。

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Cloudflare Workers](https://img.shields.io/badge/Cloudflare-Workers-F38020?logo=cloudflare&logoColor=white)
![GitHub Pages](https://img.shields.io/badge/GitHub-Pages-181717?logo=github&logoColor=white)

## ✨ 功能特点

- 🚀 加速访问 GitHub 文件、源码、Release、Gist
- ☁️ 支持 GitHub API 代理
- 🌐 可选 jsDelivr CDN 镜像加速分支文件
- 🎨 简洁优雅的前端界面(苹果风格毛玻璃设计)
- 🆓 全程免费(基于 Cloudflare Workers 免费额度)
- 🔒 支持白名单机制,可限制可代理的路径

## 📁 项目结构

```
Github-Proxy/
├── README.md          # 项目说明
├── workers.js         # Cloudflare Workers 代理脚本
└── gh-proxy/          # GitHub Pages 静态页面目录
    ├── index.html     # 主页面
    ├── 404.html       # 404 错误页
    └── favicon.ico    # 站点图标
```

## 📦 支持的链接类型

| 类型 | 示例 |
| --- | --- |
| 分支源码 | `https://github.com/user/repo/archive/main.zip` |
| Release 源码 | `https://github.com/user/repo/archive/v1.0.0.tar.gz` |
| Release 文件 | `https://github.com/user/repo/releases/download/v1.0.0/example.zip` |
| Commit / Blob 文件 | `https://github.com/user/repo/blob/abc123/filename` |
| Raw 文件 | `https://raw.githubusercontent.com/user/repo/branch/file` |
| Gist | `https://gist.githubusercontent.com/user/abc123/raw/script.py` |
| GitHub API | `https://api.github.com/repos/user/repo` |

## 🚀 部署指南

本项目分为两部分,需要分别部署:

### 一、前端页面(GitHub Pages)

1. Fork 或克隆本仓库到你的 GitHub 账号下
2. 进入仓库的 **Settings → Pages**
3. **Source** 选择 `Deploy from a branch`,分支选 `main`,目录选 `/ (root)`
4. 保存后等待几分钟,前端页面将部署在:

   ```
   https://你的用户名.github.io/仓库名/gh-proxy/
   ```

   > 注:由于静态文件放在 `gh-proxy/` 子目录下,访问路径需带上 `/gh-proxy/`。
   > 如果想让 URL 更简洁(例如 `https://你的用户名.github.io/gh-proxy/`),
   > 可以把仓库直接命名为 `gh-proxy`,并将 `gh-proxy/` 里的三个文件移到仓库根目录。

需要上传到 GitHub 的文件:
- `gh-proxy/index.html` — 主页面
- `gh-proxy/404.html` — 404 错误页
- `gh-proxy/favicon.ico` — 站点图标

### 二、代理后端(Cloudflare Workers)

1. 注册并登录 [Cloudflare](https://dash.cloudflare.com/)
2. 进入 **Workers & Pages → Create → Create Worker**
3. 为 Worker 命名(例如 `gh-proxy`),点击 **Deploy**
4. 进入 Worker 后点击 **Edit code**,把仓库里的 `workers.js` 全部内容复制粘贴到编辑器
5. **重要:** 修改 `workers.js` 第 6 行的 `ASSET_URL`,使其指向你 GitHub Pages 上 `gh-proxy/` 目录的实际地址:

   ```js
   const ASSET_URL = 'https://你的用户名.github.io/仓库名/gh-proxy/'
   ```

   > 末尾的 `/` 不能漏。如果你把静态文件移到了仓库根目录,则去掉路径末尾的 `gh-proxy/`。

6. 点击 **Save and Deploy** 完成部署
7. 在 Worker 的 **Settings → Triggers** 中可以绑定自定义域名(可选)

### 三、(可选)绑定自定义域名

Cloudflare 默认分配的 `*.workers.dev` 域名在中国大陆可能无法访问,建议:

- 把域名 DNS 托管到 Cloudflare
- 在 Worker 的 **Triggers → Custom Domains** 添加自己的域名

## ⚙️ 配置说明

`workers.js` 顶部的可配置项:

```js
const ASSET_URL = 'https://你的用户名.github.io/仓库名/gh-proxy/'  // 前端页面地址(注意末尾斜杠)
const PREFIX = '/'                                       // 路由前缀
const Config = {
    jsdelivr: 0   // 是否对分支文件启用 jsDelivr 镜像,0=关闭,1=开启
}
const whiteList = []  // 路径白名单,e.g. ['/username/'] 仅代理含该字符的路径
```

## 🧑‍💻 使用方法

部署完成后,有两种使用方式:

**1. 通过前端页面**

打开你的代理首页,将 GitHub 链接粘贴到输入框,点击加速即可。

**2. 直接拼接链接**

在原 GitHub 链接前加上你的代理域名:

```
原链接:    https://github.com/user/repo/archive/main.zip
加速后:    https://你的代理域名/https://github.com/user/repo/archive/main.zip
```

## ❓ 常见问题

**Q: 为什么不能只用 GitHub Pages?**
A: GitHub Pages 只能托管静态文件,无法执行服务端 JavaScript。`workers.js` 必须在 Cloudflare Workers 上运行才能完成代理。

**Q: Cloudflare Workers 免费版够用吗?**
A: 免费版每天 10 万次请求,个人使用绰绰有余。

**Q: 大文件能代理吗?**
A: Cloudflare Workers 免费版单次请求有 100MB 限制,超过的建议开启 jsDelivr 镜像或自行寻找其它方案。

## 📄 License

MIT

## 🙏 致谢

本项目参考并借鉴了以下优秀开源项目,在此一并致谢:

- [hunshcn/gh-proxy](https://github.com/hunshcn/gh-proxy) 
- [EtherDream/jsproxy](https://github.com/EtherDream/jsproxy)
- [cmliu/CF-Workers-GitHub](https://github.com/cmliu/CF-Workers-GitHub)
- [Geekertao/CF-GitHub-Proxy](https://github.com/Geekertao/CF-GitHub-Proxy)
- [Cloudflare Workers](https://workers.cloudflare.com/)
- [GitHub Pages](https://pages.github.com/)