---
title: Hexo博客搭建指南
date: 2022-01-09 22:15:49
tags: tutorial
---
# 导语
　　博客可以为你提供一个你纯粹个人的想法和心得，或是一个分享自己写的文章或与记录自己心情的平台。可是想要搭建一个属于自己的博客却并不简单，高昂的
服务器租用费用让人望而却步，同时第三方博客平台的广告也让你无法忍受。难道真的没有一个完美的解决办法吗？下面有请我介绍：**Hexo**
![Blog](https://www.webnode.com/blog/wp-content/uploads/2019/04/blog2.png)
<!--more-->

---
### 什么是 Hexo
　　Hexo 是一个快速，简单，免费且功能强大的静态博客框架，他支持主题，插件，同时因为出色的可扩展性与稳定性成为了最佳静态博客框架的不二之选。
![Hexo](https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcRFNpdmy5aj7A1RRHtZNOT6UZee0IkxQk7v-FLazheL2_4WaWJo&usqp=CAU)
### 什么是静态博客？
　　静态博客是指不带有任何需要数据库和动态生成网页的程序（举个栗子：PHP）的博客，与动态博客对应，各有优缺点。
### 为什么要使用静态博客？动态博客他不香吗？
　　诸位可能知道 WordPress，Typecho 等博客框架，难道他们不好用吗？首先，动态博客都是指能够动态生成网页内容的博客，但这类博客几乎全部都需要网页服务器带 PHP 解析和数据库，需要一个完整的服务器环境才能够搭建。而正如导语所说，不是每个人都有足够的钱购买价格高昂的服务器，自然动态博客就不适合了。
### 那话说回来，静态博客难道就不需要服务器了吗？
　　~~你话真多~~静态博客确实需要服务器，但因为静态博客本身不占用什么资源，于是有很多免费稳定的静态网页服务可以供我们使用，因此就不需要额外购买服务器了。
# 配置教程
　　下面就来介绍一下如何搭建一个博客。本篇文章以 Ubuntu 为操作环境，Windows，macOS 请自寻具体步骤。
## 1.安装 Hexo 前的环境配置
### 1.安装 Git
　　由于我们这里要用的是各大编程社区自带的静态网站服务，自然 Git 也必不可少了。要想安装 Git，请输入
```bash
sudo apt update && sudo apt-get install git
```
等待进度条走完之后输入``git version``来确保是否安装成功
![Git](https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=2616741985,4147970822&fm=26&gp=0.jpg)
### 2.安装 NodeJS
　　Hexo 依赖于 NodeJS 环境，所以在使用 Hexo 之前得先把 NodeJS 装上。由于 Ubuntu 内自带的源并不是最新 NodeJS，很可能会让 Hexo 出现问题，于是我们先要给 NodeJS 加个源：
```bash
sudo apt update && sudo apt-get install software-properties-common curl
curl -sL https://deb.nodesource.com/setup_13.x | sudo -E bash -
sudo apt-get install nodejs
```
安装完成后输入``node -v``确保安装成功。
![NodeJS](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1591407813151&di=4fec905d9eed3a80d96ddefe69da98fb&imgtype=0&src=http%3A%2F%2Fimg2.imgtn.bdimg.com%2Fit%2Fu%3D2829942742%2C1822803091%26fm%3D214%26gp%3D0.jpg)
## 2.开始安装 Hexo
　　恭喜你！你已经安装好了 Hexo 所需要的前置软件包了，接下来可以开始着手安装 Hexo 了。
0.在安装之前，我建议建一个叫做 blog 的文件夹用来存放博客文件以方便管理博客文件。这里请输入``mkdir blog && cd blog``来建立并切换到 blog 文件夹。
1.输入``npm i -g hexo && npm install hexo-deployer-git --save``来开始 Hexo 的安装。等待进度条走完之后输入``hexo -v``来查看 Hexo 是否安装成功
2.输入``hexo init``来初始化 blog 文件夹，初始化完成之后打开所在的文件夹可以看到以下文件：
![file](https://i.niupic.com/images/2020/06/05/8dJA.jpg)
theme 是主题文件夹，source 是博文存放处，_config.yml 是 Hexo 配置文件，其他的基本上不用动，接下来我们就可以开始配置 Hexo 了。
## 3.配置 Hexo
### 1.善用 GitHub Pages
　　之前我提到的静态网页服务器就是 GitHub Pages 了。GitHub Pages 是一个由 GitHub 推出的静态网页展示服务。这东西实际上是给 GitHub 项目做官网展示啥的，不过也没说不能用来搭博客吧...🤔
PS：国内也有类似于 GitHub Pages 的服务，这里只是拿 GitHub Pages 做演示而已。只要你足够聪明也可以按照教程来把博客传到 Gitee 上去。
1. 首先请先访问[GitHub](https://github.com/)，有账号的登录，没账号的注册。
2. 创建一个新的 repository（仓库），名字为[你的 GitHub 用户名].github.io。
3. 回到 Ubuntu 中，输入：
```bash
git config --global user.name "[你的 Github 用户名]"
git config --global user.email "[你的 Github 注册邮箱]"
```
4. 创建并读取 SSH 密钥，输入：
```bash
ssh-keygen -t rsa -C [你的 Github 注册邮箱]
cat id_rsa.pub
```
5. 把 ssh 放入 GitHub 中（[图片来源](https://www.cnblogs.com/visugar/p/6821777.html)）
![settings](https://visugar.oss-cn-shenzhen.aliyuncs.com/article/setuphexo/settings.png)![ssh-key](https://visugar.oss-cn-shenzhen.aliyuncs.com/article/setuphexo/ssh-key.png)
点击"New SSH key"，Title 随便填，key 就填刚才 cat 获取的。添加完之后输入``ssh -T git@github.com``来确保是否能正确连接。
6. 使用一个自己喜欢的文本编辑器（例如 nano，vim）编辑 _config.yml，找到最下面 delopy 的那一段，把它替换为以下的内容：
```
deploy:
  type: git
  repo: https://github.com/[你的 Github 用户名]/[你的 Github 用户名].github.io.git
  branch: master
```
保存，回到 shell 中。
7. 接下来就可以上传到 GitHub Pages 了，请输入``hexo cl && hexo g && hexo d``来自动生成博客文件并上传至 Github Pages，deploy 的过程中要输入你 GitHub 账号的用户名和密码。接下来你只需要访问 http://[你的 Github 用户名].github.io 就能看见你的博客啦！新建文章请使用``hexo new '[文章名]'``，然后你就可以在 source/_posts 路径下看到你创建的文章啦，编辑完成之后按照前面说的方式自动生成博客文件并上传至 GitHub Pages，在浏览器刷新就能看到你的文章了。