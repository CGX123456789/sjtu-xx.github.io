---
title: markdown流程图
toc: true
mathjax: true
date: 2020-12-27 10:26:49
tags: markdown
categories:
- 其他工具
---

平常使用markdown写博客，最近发现markdown可以画流程图，稍微总结一下。

<!--more-->
## hexo支持

hexo默认不支持markdown中的流程图渲染，需要安装：`npm install --save hexo-filter-flowchart`



## 一个简单的流程图

```
···flow
st=>start: Start
op=>operation: Your Operation
cond=>condition: Yes or No?
e=>end
st->op->cond
cond(yes)->e
cond(no)->op
···
```

```flow
st=>start: Start
op=>operation: Your Operation
cond=>condition: Yes or No?
e=>end
st->op->cond
cond(yes)->e
cond(no)->op
```



## 稍微复杂的流程图

```
···flow
st=>start: Start:>http://www.google.com[blank]
e=>end:>http://www.google.com
op1=>operation: My Operation
sub1=>subroutine: My Subroutine
cond=>condition: Yes
or No?:>http://www.google.com
io=>inputoutput: catch something...

st->op1->cond
cond(yes)->io->e
# 指定位置
cond(no)->sub1(right)->op1
···
```

```flow
st=>start: Start:>http://www.google.com[blank]
e=>end:>http://www.google.com
op1=>operation: My Operation
sub1=>subroutine: My Subroutine
cond=>condition: Yes or No?:>http://www.google.com
io=>inputoutput: catch something...

st->op1->cond
cond(yes)->io->e
cond(no)->sub1(right)->op1
```

## 流程图中的模块类型

| 操作模块    | 说明       |
| ----------- | ---------- |
| start       | 开始       |
| end         | 结束       |
| operation   | 普通操作块 |
| condition   | 判断块     |
| subroutine  | 子任务块   |
| inputoutput | 输入输出块 |
