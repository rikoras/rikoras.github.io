---
title: >-
  TCAD'23 Integrated ARM big.Little-Mali Pipeline for High-Throughput CNN
  Inference
top: false
toc: true
mathjax: true
date: 2024-08-20 16:33:15
password:
summary:
categories: 
tags: 
  - chip
  - inference
  - heterogeneous
---

## 关键词

SoC，CPU/GPU，Pipeline，CNN Inference

## 感兴趣的问题

1. 异构并行的性能瓶颈在哪？

2. 访存架构是什么？

3. 如何分配任务负载？

## 介绍

![图片](./1.png)

典型的手机系统，大小核CPU+多核GPU

![](2.png)

对于嵌入式系统，CPU和GPU的网络推理能力相当

共同推理可以起一个新名字，multi-component inference。

主要的问题在于非对称的处理器做multi-component inference的时候可能出现性能下降。

### ***引文***

此处援引了**High-throughput cnn inference on embedded arm big. little multicore processors**。性能下降主要指以下被称为kernel-level并行的情况：

![](3.png)

B和s代表大小核。对于都在一个簇内的多核系统，这种策略效果works，但是可能是由于缓存一致性等原因，产生了大量的memory traffic，导致引入更多核心时反而造成性能下降。

![](4.png)

甚至，几乎以任何比例分割两者的kernel负载比，都不如单独使用大核。

![](5.png)

引文后面设计了一个在大小核之间使用流水并行的系统，搜索空间包括层的切分点和流水线段数，Arithmetic的cost model。搜索策略是一个相对简单的启发式搜索。

回到本文的改进，和引文思路是类似的，但是扩展为big-Little-GPU三段。

主要解决的问题：

> we are the rst to introduce an **open-source** integrated three-stage pipeline design, called PipeAll, with the big CPU cluster, Little CPU cluster, and **GPU** as its stages.

## 架构

![](6.png)

很简明的流水线（

由于流水线切分几乎不可能平均，所以流水线要先存数据到buffer里。
