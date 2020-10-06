---
layout:     post
title:      test
subtitle:   Sincronia：Near-Optimal Network Design for Coflows阅读记录
date:       2020-10-06
author:     DR
header-img: img/desk2.jpg
catalog: true
tags:
    - Blog
---

> 第一次这样记录论文，估计会很糟糕。
> 
> 

# 前言
忽然一个想法是通过博客这样的形式来记录自己的一些点点滴滴，可以看到自己的成长轨迹，也希望自己能够不断坚持下去。如果存在相关问题有兴趣的话欢迎戳我进行讨论。本篇论文中了18年的Sigcomm，论文的链接如下所示：  
[Sincronia](https://dl.acm.org/citation.cfm?id=3230569)  

## 全文概述
简单来说，个人觉得这篇文章其实是有点偏理论的，主要也是提出了一个算法，未真正涉及到系统方面的具体实现。其实本文主要是针对coflow进行的一个调度问题，因为本文证明了只要给定一个合适的coflow是顺序，那么一定可以在传输层以下实现一个最优的结果，这个最优的CCT能够保证在4x optimal，因此本文所做的所有的工作主要集中在coflow的调度上，即如何才能得到一个非常合理的coflow顺序。

## 该文章的主要贡献
这篇文章提出了sincronia，一个针对coflow优化的数据中心设计，解决了之前的coflow都未能解决的一些问题，之前存在的问题主要如下:  
1. 以往都是对flow进行调度，但是随着Hadoop，spark等分布式应用的兴起，对传统的flow进行调度已经不能和当前情况下的性能目标进行匹配，因此之前对flow的调度在这种情况下都不行了。<br>
2. 之前对于coflow的处理方法是针对每一个coflow的到达都进行更新，但是在大规模分布式应用中，如果存在成千上百的coflow的话，那么就不行了，因此本文也进行了一部分优化。本文主要做了以下工作：<br>
1. Time is divided into epochs;<br>
2. In each epoch, a subset of unfinished coflows are selected and “ordered” using a simple greedy algorithm;<br>
3. Each host independently sets a priority for its flows (based on the corresponding coflow’s ordering), and offloads the flow to underlying priority-enabled transport mechanism;<br>
4. Coflows that arrive between epoch boundaries are greedily scheduled for work conservation.<br>
本文在实现这些工作的基础上是证明了一个理论，这个理论是只要能够保证coflow的顺序合理，那么一定可以通过传输层实现最优的情况，即4x 的近似最优。

## 回答一些读论文过程中的问题

### 1.该文章中解决的核心问题是什么？以及面临的难题是什么？

本文解决的核心问题即为关于coflow的调度问题，对于以往来说，关于coflow的调度问题是一个NP hard的问题，现有的一些成果都不能够很好的解决实际过程中遇到的问题，并且都需要对传输层进行一系列的更改，比如varys需要对传输层进行per-flow的rate的调整才能够保证其算法得到一个较好的性能，而本文在应用层面很好的去实现了coflow的调度，将coflow的调度和flow的分配进行解耦。  

本文提出的算法是基于本文进行的一个理论的证明，该理论即为在将数据中心网络抽象为一个大交换机的情况下，只要对给定的coflow的顺序能够保证最优，那么一定存在一个机制能使得平均的CCT在4x的最优时间内，证明说实话没看懂，没证出来小于等于4倍，倒是证出来一个5倍，如果有人能够证出来5倍的话，希望能email进行联系，后期如果需要进行细节的分析的话可能还会去再证一遍。

### 2.该核心问题的解决对业内带来了哪些好处和坏处？

本文通过证明以及算法得出coflow的调度与per-flow的rate调度无关，将coflow和flow进行解耦，从而只需要关注coflow的调度问题，而不需要去管传输层以下的相关处理，从而对一个大的问题进行了简化，对于per-flow的处理可以采取其他方案，同时使得本文的方案更加具有合理性。但是本文是再一个大的交换机下进行实现，其在真实条件下得到的结果是否合理以及是否存在缺陷尚未进行实际证明，如果理论存在问题那么后续的一系列方法则都无效。

### 3.本文是否还有不完善的地方，是否还有可改进的空间？

首先介绍以下本文作者觉得不完善的地方:  
1. 最好和最差性能存在差距，如何弥补性能差距  
2. 是否需要提出更好竞争比（competitive ratio）的Coflow调度方法。竞争比指的是在线算法性能和离线算法性能的比例，最坏情况比最优情况，所以必须是有限的  
3. 中心控制器需要的计算任务量到底有多少  
4. 本文方法没有考虑网络里面路径路由，所以将coflow调度与路由结合会不会有更好的性能  
5. 本文方法假设coflow信息都是已知的，如果coflow信息未知，接近最优算法如何设计  
6. 本文算法设计时以优化平均Coflow加权完成时间为性能指标，是否可以将其扩展到其他指标：deadline敏感的coflow，最小化尾部coflow完成时间等。  
个人认为不完善以及可以改进的地方：  
1. 文中也提到一个问题，就是有些应用不关心coflow完成时间，关注的是flow的完成时间。对于coflow调度和flow调度并存问题，本文提到使用权值来控制，但是如何加权值本文提到超出了本文范围，所以如何通过权值协调flow与coflow？
2. 本文实现的是一个偏理论的算法，在实际情况中是否存在实际意义也有待商却。