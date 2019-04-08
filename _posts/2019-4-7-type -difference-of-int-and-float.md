---
layout: post
title:  "int类型与float类型的表数范围"
categories: Java
tags: java 
author: fidemyuan
---

## 区别
int类型表示的范围跟float浮点类型虽然都占4位但数据存储结构不一样，表示的数的范围不一样，

## 关系

为从关系上来看属于想交的关系，部分的int类型的常量都能赋值给float类型的变量，但是一旦超过一定的范围就会溢出。

小于2的24方+1的数，float类型也可以表示，但是超过就不行。

