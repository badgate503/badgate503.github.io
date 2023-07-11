---
title: Github Pages/Hexo建站指南
date: 2023-07-11 12:13:17
tags: [网页, 前端]
index_img: /img/idxios.png
categories: 技术
---

私人博客是一种很好的知识共享平台。对于CS学生而言，私人博客也可以用于做笔记、整理技术内容等。但在搭建博客的过程中，往往遇到以下两个问题：  
* 主流的阿里云等服务商提供的云服务价格较高
* 用Html/Css/Js编写一个功能完整的博客相当麻烦  

好消息是，我们可以利用Github Pages和Hexo来解决上述问题。
# Get Start
### 安装 Git / Github Desktop
![](https://www.runoob.com/wp-content/uploads/2015/02/f7246b600c338744a9591cd7530fd9f9d62aa0f8.png "Git")  

Git 是一个开源的分布式版本控制系统。在本指南中，他用于将本地的网页内容上传至Github，进而实现对我们网站的更新。这是一份Git Bash的基础教程：[Git简明指南](https://www.runoob.com/manual/git-guide/)。在实际使用过程中，Git Bash容易出现网络不稳定的情况，因此笔者推荐使用Github Desktop作为替代。这款软件有图形界面，且完全可以满足我们的需求。[下载Git](https://git-scm.com/) / [下载Github Desktop](https://desktop.github.com/)  

![](/img/instructsite1.png "Github Desktop")
### 安装 Node.js
Node.js 是能够在服务器端运行 JavaScript 的开放源代码、跨平台执行环境。Hexo 需要使用 Node.js 提供的 npm 工具进行下载。 [下载Node.js](https://nodejs.org/zh-cn/download)
### 安装和使用 Hexo
Hexo 是一种快速、简洁且高效的博客框架，我们将利用 Hexo 直接生成精美的网页，从而避开繁杂的HTML编程工作。
安装完成 Node.js 后，启动命令提示符，输入指令：
```bash
 $ npm install -g hexo-cli
```
![](/img/instructsite2.png "安装界面")  
等待下载完成即可。如果提示网络错误，可通过以下指令改变 npm 镜像源：
```bash
npm config set registry http://registry.cnpmjs.org
```
下载完成后，输入 `hexo help` 检查是否可用。  
接着，在本地新建一个文件夹，用于储存我们的网站内容。在命令行中用 `cd` 指令跳转至该文件夹，执行：
```bash
$ hexo init <folder>
$ npm install
```
Hexo 将会自动创建一系列文件，这些文件是我们网站的基础内容。
### 配置 Github 储存库
登录 Github，创建一个新的储存库并命名为 `[username].github.io` 。其中 `[username]` 必须替换为你的 Github 用户名，创建成功后，使用 Git 或 Git Desktop 将上一步中的文件 `push` 到该储存库中。
### 配置自动部署
`hexo init` 生成的文件并不包含任何网页内容，就像是一份“源代码”，我们需要对其进行“编译”来生成网页文件，而这也就产生了一个问题：以后每次对“源代码”进行修改，都需要重新“编译”才能实现更新，相当麻烦。好在 Github 为我们提供了一种解决方案：Github Actions，它可以自动化、自定义的执行软件开发工作流程。我们只需要在储存库中建立`.github/workflows/pages.yml`，并填入以下内容:
```yml
name: Pages

on:
  push:
    branches:
      - main # default branch

jobs:
  pages:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js 18.x
        uses: actions/setup-node@v2
        with:
          node-version: "18"
      - name: Cache NPM dependencies
        uses: actions/cache@v2
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```
保存后，可以看到 Github 页面的 Action 栏中出现了一个新的 Job，其完成后会显示
![](/img/instructsite3.png)  
接着我们可以发现，储存库多了一个名为 `gh_pages` 的分支，这里面就储存着“编译完成”的网页文件，打开 Settings 栏，选择 Pages - Build and deployment 将 Branch 改为 `gh_pages`，打开 `https://[username].github.io` 即可查看网站。
### 网站操作：主题 / 写作
#### 网站主题
类似于 Steam 的创意工坊，Hexo 有着大量用户贡献的优质主题可供我们直接套用。可以在 Github 上搜索 `hexo theme` 寻找主题，使用 `git clone` 下载主题。  
下载完成后，将主题文件夹拷贝至 `/themes` 目录下，再修改 `_config.yml` 文件:
```yml
theme: themename
```
通过 `git commit` 和 `git push` 更新网站。
#### 写作
Hexo 支持 Markdown 格式文本，通过以下指令
```bash
hexo new [layout] name
```
创建一篇新文章，他将被储存在 `/source/_post` 目录下，可以使用任何编辑器进行编辑。具体写作方式可查阅 [写作](https://hexo.io/zh-cn/docs/writing)
### 自定义域名
尽管我们的网页已经初具雏形，但 `[username].github.io` 并不像是一个“合理”的网址，类似于 `[username].com` 的域名才是我们想要的。  
想要实现自定义域名，我们必须先拥有一个域名。可以在阿里云上购买一个价格较低的域名，例如 `xxxx.top` 的域名往往只需要 ￥9 per year，几乎零成本。购买好域名后，我们需要先在 Github 储存库的根目录和 `/source` 目录下新建一个名为 `CNAME` 的文件，里面填入自己的域名即可。再在阿里云域名控制台中选择“解析”  
![](/img/instructsite4.png)  
添加如下两条记录：  
![](/img/instructsite5.png)  
保存即可，稍等片刻后打开我们的域名即可访问网站。
### CDN加速
由于某些特殊原因，在国内无代理条件下访问按照以上步骤完成的网站速度较为缓慢，图片加载耗时很长，严重影响用户体验。好在 CDN(内容分发网络) 可为我们的网站提供加速，我们将采用 Cloudflare 作为网站 CDN 服务商，他有以下几个优点：
* 免费版足够使用
* 无需备案
* 可为网站提供安全防护  

访问 [Cloudflare官网](https://www.cloudflare.com) 进行注册。注册完成后，点击“添加站点”，输入网站域名，再选择“免费计划”。
![](/img/instructsite6.png)  
接着按照平台提示，在阿里云域名管理中把网站域名的 DNS 修改为 Cloudflare 提供的 DNS 即可。