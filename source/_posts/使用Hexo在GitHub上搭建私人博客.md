---
title: 使用Hexo在GitHub上搭建私人博客
categories: 
  - 建站运维
tags:
  - Hexo
  - GitHub
date: 2019-03-09 18:43:22
---

> 利用好GitHub Pages，让你免费光速搭建私人博客，[点击查看效果预览](http://zhangchong.xin)

<!-- more -->

# 前言
&emsp;&emsp;一般故事的开始，都要有好多准备工作，在使用Hexo之前，你要做好如下准备：
1. 注册GitHub账号
2. 电脑安装`node.js`、`npm`或`cnpm`
3. 电脑安装git

# GitHub上的操作
## 创建博客仓库
1. 新建仓库`username.github.io`，其中username必须是你的github用户名
2. 在仓库Settings页面找到GitHub Pages页面，Choose a theme并保存
3. 此时访问`username.github.io`，就可以看到你的博客页面了

## 绑定域名
1. 还是在仓库Settings页面找到GitHub Pages页面，Custom domain输入框内输入你的域名
2. 为自己的域名添加DNS解析，一般有两种类型
	- CNAME：将你自己的域名指向另一个域名，也就是`username.github.io`
	- A：将你自己的域名指向一个ip（可以ping `username.github.io`的ip）

# 配置SSH
1. 在git bash窗口中输入命令`cd ~/.ssh`
2. 如果提示`No such file or directory`，则执行命令`h-keygen -t rsa -C "your github email"`
3. 连续按三次回车
4. 在C盘本用户目录文件夹下打开`.ssh\id_rsa.pub`文件，复制所有内容
5. 打开GitHub，点击右上角头像，选择Settings
6. 选择`SSH and GPG keys`，点击`New SSH key`
7. 链贴刚才复制的内容，并取一个名字(随意)

## 测试SSH
```bash
ssh -T git@github.com # 注意邮箱地址不用改
```
&emsp;&emsp;如果提示`Are you sure you want to continue connecting (yes/no)?`，输入yes，然后会看到：`Hi XXX! You've successfully authenticated, but GitHub does not provide shell access.`

&emsp;&emsp;看到这个信息说明SSH已配置成功！此时你还需要配置：
```bash
git config --global user.name "youusername"// 你的github用户名，非昵称
git config --global user.email  "xxx@xx.xxx"// 填写你的github注册邮箱
```

# 使用Hexo
## 安装Hexo
1. 新建你博客的本地文件夹
2. 在文件夹根目录内右键打开git bash窗口
3. 按顺序执行以下三条命令（可用cnpm代替npm可以加速）

```bash
npm install hexo-cli -g

hexo init

npm install
```

&emsp;&emsp;新建完成后，指定文件夹的目录如下：
```text
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

&emsp;&emsp;此时按顺序执行以下两条命令，其中第一条用来生成静态文件，第二条用来启动本地服务器。在浏览器访问`http://localhost:4000/`，可以预览当前博客的效果。
```bash
hexo g

hexo s
```

## 上传到github
&emsp;&emsp;配置<kbd>站点配置文件</kbd>`_config.yml`中有关deploy的部分，其中`username`改为自己的github用户名。
```yml
deploy:
  type: git
  repository: git@github.com:username/username.github.io.git
  branch: master
```
&emsp;&emsp;在博客本地文件夹的`source`子文件夹内，新建`CNAME`文件，注意不要后缀名，文件内容填写你自己绑定github.io的那个域名。

&emsp;&emsp;按顺序执行以下命令：
```bash
npm install hexo-deployer-git --save
hexo g -d
```

## 查看博客
&emsp;&emsp;现在在浏览器打开你自己的域名，就可以看到Hexo为你渲染的博客啦。
> 自定义主题配置，可参考其他教程，也可以download我的github仓库查看（注意在hexo-blog分支下）。[点击访问](https://github.com/chung567115/chung567115.github.io)

## 常用hexo命令
```bash
hexo g -d #一键生成并部署，最常用
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo g #生成静态页面至public目录
hexo s #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo d #部署到GitHub
hexo clean #清理旧文件
```
> 注意新建的博客markdown文件都在`source`文件夹下的`_posts`子文件夹中
