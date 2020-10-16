---
layout:     post
title:      RDMA学习规划
subtitle:   摘抄自别处的RDMA学习规划和问题思考
date:       2020-10-16
author:     DR
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - RDMA
---

>RDMA学习三部曲
# 1  概览阶段

建议考虑这些问题：

1.      为什么需要RDMA技术？RDMA解决了什么问题？

2.      IB、iWarp、RoCE三种技术有何区别？各自优缺点是什么？

3.      IB、RoCEv1、RoCEv2报文构成有何区别？

4.      IB网络结构是什么样的？RoCE网络结构有何区别？

5.      IB协议层次结构包括哪些层？



# 2  协议阶段
RoCE的大部分特性基于IB协议，RoCE自有特性在如下RoCE和RoCEv2附加文件中给出。

《IB Specification Vol 1-Release-1.3-2015-03-03.pdf》

《Annex16_RoCE.pdf》

《Annex17_RoCEv2.pdf》

协议分20章，共1842页。第3~8章以及第9章的内容是理解整个RoCE协议的基础，请大家重视。



## 2.1  IB协议1和3章
《IB Specification Vol 1-Release-1.3-2015-03-03.pdf》第1、3章


建议考虑这些问题：

1.      基本概念的理解：QP、CQ、WR、CA、HCA、TCA、PD等。

2.      网络结构中的组件有哪些？分别完成什么功能？

3.      面向连接的业务和数据报业务的区别？

4.      IB支持的服务类型有哪些？有什么区别和联系？

5.      IB中的keys用来做什么？有哪些keys？

6.      什么是verbs？
 
##  2.2   IB协议4-8章
《IB Specification Vol 1-Release-1.3-2015-03-03.pdf》第4-8章

建议考虑这些问题：

1.      VL和SL的关系？

2.      SGID, DGID的使用？

3.      ICRC和VCRC分别保护哪一些域段？

4.      一个典型RC链接，RDMA Write报文头结构、Send报文头结构、ACK报文头结构？

5.      一个典型的RDMA write切片方法，以及报文头添加方法？

6.      报文头中Reserved字段在发送侧和接收侧的要求？

7.      AETH中的syndrome的不同含义以及如何使用？


## 2.3  IB协议9章
《IB Specification Vol 1-Release-1.3-2015-03-03.pdf》第9章

建议考虑这些问题：

1.      UC, UD, RC三种业务的异同，分别支持哪一些operation？

2.      PSN是如何使用的，在发送Read命令中PSN里面是如何处理的？

3.      MSN是如何使用的？

4.      为何会接收到重复（duplicate）报文？接收到重复报文如何处理？

5.      链路上丢ack的处理，request和response侧的处理？

6.      链路上丢req的处理，request和response侧的处理？

7.      联合响应的好处是什么？

8.      超时重传？丢包重传？rnr重传？

 

## 2.4      IB协议10-11章
《IB Specification Vol 1-Release-1.3-2015-03-03.pdf》第10-11章


建议考虑这些问题：

1.      MR和MW的区别？

2.      VA和ZBVA？

3.      MR是如何注册的？

4.      MR如何进行无效？

5.      本地操作有哪些？

6.      post_send 做了什么工作？

7.      RoCE的异常时如何处理的？
 

## 2.5   IB附录
《Annex16_RoCE.pdf》

《Annex17_RoCEv2.pdf》

《IB协议Annex16_RoCE.docx》

《IB协议Annex17_RoCEv2_UDP.docx》

建议考虑这些问题：

1.      RoCE在IB基础上做了什么改变？

2.      RoCEv2相对RoCEv1做了什么改变？

# 3  逻辑实现阶段
## 3.1   RoCEE UM

建议考虑这些问题：

1.      RoCEE内部包括哪些模块？各自功能是什么？

2.      是否理解feature list的每句描述？

3.      RoCEE目前支持的流控方式有哪些？

4.      多级寻址是如何实现的？

5.      各种表项QPC/CQC/SRQC/EQC/MPT等的含义，以及存放在什么地方？

6.      VLAN是什么？

7.      doorbell和mailbox是什么？是如何执行和处理的？

8.      硬件是如何处理上报中断的？

9.      目前RoCEE的性能指标有哪些？目前达到什么样的指标？

## 3.2   RoCEE数据结构与寄存器

建议考虑这些问题：

1.       数据结构中，目前有哪些主要的数据类？

2.       QPC的数据结构分为几大类？

3.       数据结构中如何看，哪些是硬件维护，哪些是软件维护？

4.       QPC中域段，多级流水如何进行维护和更新？

5.       自己所在的模块会用到哪些数据结构？

6.       寄存器表单分哪几个页签，有何区别？

