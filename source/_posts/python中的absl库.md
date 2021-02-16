---
title: python中的absl库
toc: true
date: 2020-02-25 15:16:27
tags: [python]
categories:
- Python
- 第三方库
---


absl库用来进行命令行命令解析
<!--more-->
absl 库全称是 Abseil Python Common Libraries。它原本是个C++库，后来被迁移到了Python上。

它是创建Python应用的代码集合。这些代码从谷歌自己的Python代码基地中搜集而来，已经过全面的测试并广泛用于生产中。

特点：

简单的应用创建
分布式的命令行标志系统
用户自定义的记录模块，并拥有额外的功能。
拥有测试工具
下面是它的 hello world 样例。我们输入参数：name 代表名字，num_times 代表语句重复次数。name是必填参数，num_times是可选参数，默认值为1.
```python
from absl import app
from absl import flags
 
FLAGS = flags.FLAGS # 用法和TensorFlow的FLAGS类似，具有谷歌独特的风格。
flags.DEFINE_string("name", None, "Your name.")
flags.DEFINE_integer("num_times", 1,
                     "Number of times to print greeting.")
 
# 指定必须输入的参数
flags.mark_flag_as_required("name")
 
def main(argv):
  del argv  # 无用
  for i in range(0, FLAGS.num_times):
    print('Hello, %s!' % FLAGS.name)
 
 
if __name__ == '__main__':
  app.run(main)  # 和tf.app.run()类似
```
