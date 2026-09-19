## 追问 1：Agentic RAG的token成本比普通RAG高多少？

普通RAG一次检索+一次生成。Agentic RAG需要多轮推理，每轮"再检索"都意味额外LLM调用和向量检索，总token消耗约普通RAG的4-8倍。生产环境中要设置最大检索轮数(通常2-3轮)防止无限循环。

## 追问 2：RAG从Naive到Agentic经历了哪几代？

第一代Naive RAG→检索+生成; 第二代Advanced RAG→+Query改写+混合检索+Rerank+Chunk优化; 第三代Modular RAG→模块化可插拔+路由分支(Spring AI 1.x); 第四代Agentic RAG→LLM自主决策检索策略多轮迭代。目前大多数生产系统Advanced RAG就够用。
