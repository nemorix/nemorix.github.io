---
title: hugo+github博客搭建
date: 2023-12-07 10:44:49
lastmod: 2023-12-07 16:57:30
draft: true
tags:
  - 环境配置
categories:
  - Hugo
author: nemorix
comment: true
toc: true
contentCopyright: <a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>
reward: false
mathjax: true
---
之前一直想搞个个人博客，但是自己维护服务器+域名又很麻烦，也不想直接用知乎博客园之类的现成网站（处于属于是一颗想折腾又不想折腾的矛盾叠加态），然后就一直搁置了。最近心血来潮，加上忘性有点大，得找个地方存一下日常的想法，以供备忘，于是乎个人博客提上了日程。

打开搜索引擎，搜索建立个人博客，hugo+白嫖GitHub.io映入眼帘。hugo是Go语言开发的静态博客系统，环境配置比较简单，可以直接将md文件发布到博客上；github提供仓库的page服务，只需创建一个githubID.github.io的仓库，并且配置好page，就可以白嫖域名了，nice！并且github的action可以支持自动发布博客，只需配置好workflow即可。以下是具体流程。
## hugo配置
首先便是在本地配置好hugo环境。由于我用的主题使用scss，所以需要安装hugo-extended。
1.[下载Git](https://git-scm.com/downloads)
2.[下载golang](https://go.dev/dl/  “All releases - The Go Programming )
3.[下载hugo-extended](https://gohugo.io/installation/windows/)没有二进制版本，官网推荐是通过包管理工具安装，也可以自行从源代码构建。
4.验证hugo是否安装成功：  
~~~
hugo version
~~~
出现下列样式便是成功了：
~~~
hugo v0.120.4-f11bca5fec2ebb3a02727fb2a5cfb08da96fd9df+extended windows/amd64 BuildDate=2023-11-08T11:18:07Z VendorInfo=gohugoio
~~~
然后正式开始，新建一个文件夹作为博客的工作区。进入这个文件夹，在这个文件夹下新建项目：
~~~
hugo new site ./
~~~
文件结构如下：
![](https://raw.githubusercontent.com/nemorix/blog-img/main/hugo-file-structure.png)
初始化当前目录中的空 Git 存储库：
~~~
git init
~~~
下一步就是安装主题，在[https://themes.gohugo.io/](https://link.zhihu.com/?target=https%3A//themes.gohugo.io/) 里找一个你喜欢的，然后主题的下载页面里找到安装方法，一般格式是：
~~~
git submodule add <github.theme-name.git themes/theme-name>
~~~
如果能在`<themename>`文件夹内找到`exampleSite`文件夹，可以把里面的文件复制到`<blog>`文件夹里，省去自己配置博客样式的步骤（新手推荐）。

注意！现在的`<blog>`文件夹里的默认配置文件是`hugo.toml`，而一般的`exampleSite`里的配置文件一般是`config.toml`和`config.yaml`，而一个网站只能有一个配置文件，且这三种文件名都是可以的。所以复制的时候记得把原来的`hugo.toml`配置文件删掉。

然后就可以开始本地部署了：
~~~
hugo server
~~~
没有报错就可以在`http://localhost:1313`看到本地部署的博客了。
## 把博客部署到GitHub
先创建新存储库。填上`Repository name`那一栏，格式为`<name>.github.io`。如果你想让你的博客网址就是`<name>.github.io`，则`<name>`不能是任意名字，必须是你的**github用户名**。然后点击`Create repository`即可，返回`<blog>`文件夹里，把这个本地库连接到远端库。在此之前需要先配置github远端连接服务。
### GitHub远程连接配置
本地Git仓库和GitHub仓库之间的传输是通过SSH加密传输的，所以需要先配置ssh key，这里默认你已经生成rsa公钥和私钥了，如果没有可以查询相关教程。在GitHub的账户页面打开`Account settings–SSH Keys`页面：
![](https://raw.githubusercontent.com/nemorix/blog-img/main/github-ssh.png)
点击`New SSH Key`，title随意，key填写id_rsa.pub（可以用文本编辑器打开）的全部内容。然后验证是否成功，在终端里输入下面的命令：
~~~
ssh -T git@github.com
~~~
出现下列样式便是成功了：
~~~
Hi nemorix! You've successfully authenticated, but GitHub does not provide shell access.
~~~
### 本地仓库连接到GitHub仓库
之前配置好了github远程连接，于是我们将本地的博客仓库连接到我们的GitHub博客仓库：
~~~
git remote add origin https://github.com/<name>/<name>.github.io.git
~~~
把这些修改后的文件添加到本地库，并标记上`commit message`：
~~~
git add .
git commit -m "commit message"
git branch -M main 
#这一步可选，因为后面用到的github workflow默认分支是main，也可在后面改github workflow 配置文件
git push -u origin main
~~~
最后是进入`https://github.com/<name>/<name>.github.io`，点击上方栏的`Settings`，然后点击左方栏的`Pages`，在`Build and deployment`里的`Source`中选择`Github Actions`，在下面找到`Hugo`，点击`Configure`，如果前面没有改branch，可以在这一步的配置文件里改。在新界面点击右侧的绿色按钮的`Commit changes...`。

找不到`Hugo`就去`browse all workflows`里找。

部署好后就可以在`Pages`页面看到`Your site is live at ...`，点击`Visit site`就可以访问博客网站。建议每次修改博客先在本地跑一遍`hugo server`看看构建是否成功，省去提交后构建失败还要重新提交的麻烦（血泪教训）。
后续写博客和发布博客可以用obsidian+vscode，配置好插件就只用敲几条命令。

### 补充

特殊网络环境，git配置代理：
```
git config --global http.proxy http://127.0.0.1:7890

git config --global https.proxy https://127.0.0.1:7890

git config --global --unset http.proxy

git config --global --unset https.proxy
```
obsidian和vscode插件的配置单开一篇博客（TODO）。

上传博客时又遇到一个问题，文章不渲染！~~开始我以为时github网络问题看，因为发布page后需要一段时间响应变化，但是一会儿过去还是没有文章，不过action显示构建是成功的。于是开始本地调试，发现修改md文件hugo后台会显示文件修改，但是前端就是不渲染，见鬼了。又怀疑是md yaml头的问题，删删改改还是没起作用，再怀疑是obsidian语法的问题，检查一遍没有特殊语法，见鬼了。但是之前的模板文章可以渲染，于是复制能渲染的yaml头，乐，可以渲染，于是一条一条校对格式，校对完还是渲染不了，只能复制yaml头来渲染，这样子可不行，于是再次比对，发现端倪：只有文章的创建时间和修改时间不一致，~~搜了一下发现hugo不能渲染未来的文章，淦，经典问题。在toml配置文件里加一句：
```
buildFuture=true
```
解决。
### 参考
[在github.io用hugo部署个人博客，2023新教程 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/649542248)
[Github——git本地仓库建立与远程连接（最详细清晰版本！附简化步骤与常见错误）_github本地仓库创建-CSDN博客](https://blog.csdn.net/qq_29493173/article/details/113094143)
