---
title: "Hugo搭建博客简单教程"
date: 2019-10-02T12:54:09+08:00
draft: false
---

# ***分享使用hugo搭建个人博客的简单教程（个人总结）***
*****
## 下载并安装hugo
* 从[官网](https://gohugo.io/getting-started/installing)下载
* 配置环境变量
*****
## Create a new site
* `hugo new site sitaddress`(sitaddress这里填写github用户名.gutihub.io-generator)
*****
## Add a theme
* 在当前目录下
 ```
git init
git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
echo 'theme = "ananke"' >> config.toml
```

到[这里](https://themes.gohugo.io/)下载更多主题
*****
## Add Some Content
* `hugo new posts/my-first-post.md`

在posts目录中找到相应文件进行编辑 完成后draft状态改为false，修改标题时间
*****
## Start the Hugo server
* `hugo server -D`

Navigate to your new site at [你的博客预览](http://localhost:1313/).
*****
## Customize the Theme
* 在`config.toml`中
* 修改`title`命名标题
* 修改`baseURL`定义你的域名
* 想看更多的定制方法，请看[这里](https://gohugo.io/themes/customizing/)
*****
## Build static pages
* `hugo`

完成后生成`public`目录
*****
## 上传至github
* 先创建`.gitignore`,在其中加入`public`目录
* 进入`pulic`目录 执行`git add`, `git commit`提交代码
* 在github中创建以项目目录为名称(去掉-generator)的仓库
* 执行`git remote add`以及`git push`操作
*****
## 域名购买与应用
* 了解即可
## 备份
* 创建新的仓库 名称最好用项目目录，当前目录下执行git remote add以及git push操作
* 此时处于public上层目录
* add提交的文件 出现错误提示时尽量删除不必要的.git目录