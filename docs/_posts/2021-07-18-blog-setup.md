---
layout: post
date: 2021/07/18
tags: [blog]
title: 博客搭建
toc: true
---

## 使用github搭建博客

- 使用模块: [参考教程](https://github.com/FeDemo/fedemo.github.io)
- 不使用模板从头搭建: [github官网教程](https://docs.github.com/en/pages)

windows安装ruby环境较麻烦，所以我直接使用的他人的模板且所有测试均在githubpage上进行

## 注意事项
和平时写markdown不同，使用该模块写博客需要注意以下两点:
1. 博客文件存放于`_posts/`目录下，文件名需要以`date-title.d`形式命名，否则无法识别，其中空格使用`-`分隔
2. 似乎需要在markdown文件中加入头部信息
  ```
  ---
  layout: post
  author: qing
  title: your-title
  date: 2021/7/18
  last_modified_at: 2021/07/18
  tags: 
    - blog
    - python
  # tags: [blog, python]
  math: true
  toc: true
  ---
  ```
  - layout不指定则使用默认布局，影响不大可以不写
  - title为博客名，如果不指定将使用文件名作为标题
  - author指定后会在页面中显示作者姓名
  - date可不指定将使用文件名中的日期，日期使用`/`或`-`分割亲测都可以，日期中加不加0都可以
  - last_modified_at最后修改日期可不止定
  - tags分类，多个分类使用md的列表形式或[]指定
  - math支持latex数学公式
  - toc启用大纲
## 总结
- 文件名使用日期+标题命名，日期和标题中空格使用`-`分隔
- 文件不写头部信息可以支持大部分情况，需要更多功能时按照上述添加头部即可
- 实际显示和typora/github预览的可能不太一样，需要根据情况修改
