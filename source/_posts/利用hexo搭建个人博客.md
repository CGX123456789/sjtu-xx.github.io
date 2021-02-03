---
title: 利用hexo搭建个人博客
date: 2019-11-18 23:35:47
toc: true
tags: [hexo,icarus]
categories: 
- 其他工具
---
## 基于hexo搭建个人博客

1.安装nodejs

2.添加淘宝源

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

<!-- more -->

3.安装hexo

```
cnpm install -g hexo-cli
```

4.创建空文件夹

5.切换到空文件夹

6.生成

```
sudo hexo init
```

7.新建github仓库

仓库名必须为  用户名.github.io

8.

cnpm install --save hexo-deployer-git

9.设置`_config.yml`

配置(repo为仓库地址，使用ssh可能会提示权限错误，更改https地址后，删除./deploygit文件夹，重新部署。)

```
deploy:
  type: git
  repo: https://github.com/sjtu-xx/sjtu-xx.github.io.git 
  branch: master
```

```
hexo d  #部署
```

10.更换主题

将主题下载到theme文件夹下，然后更改_config.yml中的theme文件

```
hexo clean
hexo generate
hexo deploy
```

11.添加gitalk评论系统
- 新建一个仓库如blog_comment,在设置中进行授权。settings-》Developer settings - > OAuth Apps -》新建
- Application name 与仓库名称一致，主页url和回调url一致，为主页地址。
- 在icarus的config文件中添加：
```
comment:
    # Name of the comment plugin
    type: gitalk
    owner: sjtu-xx         # (required) GitHub user name
    repo: blog_comment   # (required) GitHub repository name
    client_id: xxxxxxx # (required) OAuth application client id
    client_secret: xxxxxxxxx  # (required) OAuth application client secret
    admin: sjtu-xx
```


遇到的问题：

**插入图片：**

https://yanyinhong.github.io/2017/05/02/How-to-insert-image-in-hexo-post/

**icarus主题详细配置：**

[https://cloudy-liu.github.io/2019/06/23/Hexo%E4%B8%BB%E9%A2%98%E8%BF%81%E7%A7%BB%E5%88%B0icarus/](https://cloudy-liu.github.io/2019/06/23/Hexo主题迁移到icarus/)

**家里的网络在deploy时，连接github显示timeout错误**
通过连接手机运营商网络可以上传
连接学校的vpn也可以成功上传

## hexo数学公式
原生hexo并不支持数学公式，需要安装插件 mathJax。mathJax 是一款运行于浏览器中的开源数学符号渲染引擎，使用 mathJax 可以方便的在浏览器中嵌入数学公式。mathJax 使用网络字体产生高质量的排版，因此可适应各种分辨率，它的显示是基于文本的而非图片，因此显示效果更好。这些公式可以被搜索引擎使用，因此公式里的符号一样可以被搜索引擎检索到。

1.安装
`$ npm install hexo-math --save`

2.配置
在站点yml文件中添加
```
math:
  engine: 'mathjax' # or 'katex'
  mathjax:
    # src: custom_mathjax_source
    config:
      # MathJax config
```

在文档开头加入：
`mathjax: true`