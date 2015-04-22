---
layout: post
category: 论文总结
title: Efficient Coflow Scheduling with Varys
tags: [论文,网络]
tagline: by wubin
---

数据并行的应用会同时产生多个并行的数据流，目前没有在应用需求的层面上来优化这个网络。所以作者在本文中主要是解决两个问题：

1. Decreasing communication time of data-intensive jobs
2. Guaranteeing predictable communication time

<!--more-->

作者提出了Varys这个系统来实现上面的两个目标。这个系统可以让data-intensive frameworks使用coflow。收益如下（EC2 deployments and trace-driven simulations）：

1. Communication stages complete up to 3.16X faster on average
2. Up to 2X more coflows meet their deadlines using Varys in comparison to per-flow mechanisms
3. Moreover, Varys outperforms non-preemptive coflow schedulers by more than 5X

Jobs通常会产生多个coflow，这些coflow的大小都不一样，这包括两个方面：

1. 不同Coflow所包含的flow的数量不一样
2. 每个最小单位的flow的大小也不尽相同

像FIFO或其变种这些简单的调度机制，虽然对于去中心化的实现非常有益，但是在本文所提到的环境中表现却不佳，因为一个大的coflow的到来会导致许多小的coflow无法满足它们的deadline。同时shortest或smallest first方法也是不可行的，虽然它解决了大多数的调度问题，但是效率不够高，因为coflow间的调度与单纯的flow层次上的调度不一样，coflow涉及到多条并行的flow。

目前提到的job-specific communication requirements通常有：

1. Minimizing completion time
2. Meeting a deadline

这就需要我们优化coflow的完成时间（CCT），因为CCT的减少同时也会使相应的job的完成时间缩减。

具体的一些限制会导致我们认为必须通过FIFO的方式来调度coflow，one by one，但是这样会导致无法实现中心化，因为调度者有时候需要实现flow的抢占和速率的重计算。

本文中，作者研究了对于任意的coflow的coflow间的调度问题，着重两个目标：

1. Improving application-level performance by minimizing CCTs
2. Guaranteeing predictable completions within coflow deadlines

作者证实无论是这两个目标或是提出有效的启发式方法，都是NP-hard的。所以他提出一种coflow的调度方法和一个flow-level的速率分配算法，通过只在有coflow到来或完成的时候进行重新安排来使coflow的中心化调度变得可行。

由于一些耦合约束的存在，coflow的瓶颈节点决定了它的完成时间。作者提出了Smallest-Effective-Bottleneck-First（SEBF）方法，贪婪地基于coflow的瓶颈完成时间来调度coflow。然后使用Minimum-Allocation-for-Desired-Duration（MADD）算法来为该coflow的每一条flow分配速率。MADD背后的key idea就是减慢coflow中的所有flows来match花费时间最长的那条flow的完成时间。结果，同一时间并存的其它coflows能够make progress，最终平均的CCT会减小。
为了保证coflow的完成时间，作者使用的接入控制。例如，不允许任何不干扰别人就无法满足自己deadline的coflow进入。一旦准入coflow，就使用MADD来在该coflow的deadline之前完成该coflow的所有子flow，使用最小带宽来保证完成时间。

Varys这个系统提供了简单的API，让数据并行的框架能够以coflows的方式表达出它们的通信需求，而该框架只需做非常少的修改即可。用户编写的Jobs则无需任何修改就可以使用coflow。

Varys系统的实验环境：

Deploy Varys on a 100-machine EC2 cluster and evaluate it by replaying production traces from Facebook. 收益如下：

1. Varys improved CCTs both on average (up to 3.16X) and at high percentiles (3.84X at the 95th percentile) in comparison to per-flow fair sharing. Hence, end-to-end completion times of jobs, specially the communication-heavy ones, decreased.
2. The aggregate network utilization remained the same, and there was no starvation.
3. In trace-driven simulations, Varys is 3.66X better than fair sharing, 5.53X better than per-flow prioritization, and 5.65X better than FIFO schedulers.
4. Moreover, in EC2 experiments (simulations), Varys allowed up to 2X (1.44X) more coflows to meet their deadlines in comparison to per-flow schemes; it marginally outperformed resource reservation mechanisms as well.
