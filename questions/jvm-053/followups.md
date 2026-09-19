## 追问 1：为什么只有ParNew能和CMS搭配？Parallel Scavenge不行吗？

答：因为CMS需要新生代收集器实现一些特定的接口来配合，比如Card Table（卡表）的处理、并发标记时的同步等。

ParNew本质上就是Serial的多线程版本，代码结构和Serial高度相似，改造成本低，所以Sun当年专门改造了ParNew来适配CMS。

Parallel Scavenge是独立设计的吞吐量优先收集器，框架和CMS不搭，改造工作量大，而且设计目标也不一样——吞吐量优先的新生代配低延迟老年代本身就很别扭，所以一直没做适配。
