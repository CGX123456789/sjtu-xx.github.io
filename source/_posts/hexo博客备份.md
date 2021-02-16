---
title: hexo博客备份
toc: true
date: 2019-12-09 15:19:03
tags: [hexo]
categories: 
- 其他工具
---

# 备份步骤
<!--more-->
1. 在博客仓库创建分支hexo（或其他）
2. 设置hexo为默认分支
3. 创建`.gitignore`文件
文件内容：
```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```
4.上传至github
```shell
git add .
git commit -m "blabla"
git push origin hexo
```
5. hexo g -d 发布博客

## 恢复步骤
1. `git clone https://github.com/sjtu-xx/sjtu-xx.github.io.gito`
2. 在克隆的文件夹下输入`npm install hexo-cli`,`npm install`,`npm install hexo-deployer-git`

