---
layout: post
title: "Python extend() 和append() 方法差异"
author: "JT"
header-img: "img/post-bg-halting.jpg"
header-mask: 0.4
mathjax: true
tags:
  - python
  - 编程语言
---


extend() 和 append() 方法都是python 语言 列表类型中的方法，这两个方法功能相似，但是在处理多个列表时，处理方式和结果是不同的

-   append() 方法是将append 的对象整体作为一个元素添加到原list 中

    假设存在两个列表a 和b

    ```
    >>> a = [1,2,3]
    >>> b = [4,5,[6,7]]
    >>> a.append(b)
    >>> a
    [1,2,3,[4,5,[6,7]]]
    ```

-   extend() 方法则是将 extend的对象中的每一个元素都添加到原list中

    ```
    >>> a = [1,2,3]
    >>> b = [4,5,[6,7]]
    >>> a.extend(b)
    >>> a
    [1,2,3,4,5,[6,7]]
    ```