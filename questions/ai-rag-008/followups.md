## 追问 1：Agent设计模式有哪些？ReAct为什么最经典？

五种主流: 1) ReAct(思考→行动循环)——通用单Agent最经典面试必会; 2) Plan & Execute(先全局规划再执行)——步骤固定任务; 3) Multi-Agent(子Agent协作)——复杂任务拆解; 4) Reflection(自我评估迭代修正)——高输出质量; 5) Tool-first RAG(检索决定行动)——知识密集型。ReAct最经典因为是目前大多数单Agent系统的基础模式。

## 追问 2：Multi-Agent中Orchestrator和Worker之间通信和传统微服务有什么区别？

Multi-Agent的Orchestrator和Worker之间的通信、背压、重试、幂等性——这些恰恰是传统分布式系统的老知识。Kafka、Spring Cloud Stream在这里依然好使。掌握分布式系统的后端工程师转型Agent有天然优势。
