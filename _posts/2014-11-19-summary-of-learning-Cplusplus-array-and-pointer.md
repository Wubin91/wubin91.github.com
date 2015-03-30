---
layout: post
title: 数组和指针
category: C++学习总结
tagline: by wubin
tag: [C++]
---

在用下标访问元素时，vector使用vector::size_type作为下标的类型，string使用string::size_type，而数组大小和下数组下标的正确类型则是和bitset一样，为size_t。

当我们在编程时没有检查数组下标，并且引用了越出数组或其他类似数据结构边界的元素时，会导致“缓冲区溢出”，这是由数组越界导致的。

