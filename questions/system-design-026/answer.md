技术选型决策树:
1. **场景判断**: 简单FAQ?→Naive RAG够用。企业知识库?→Advanced RAG(混合检索+Rerank)。复杂多跳推理?→Agentic RAG
2. **数据规模**: <1万文档→FLAT暴力搜索。百万级→HNSW。千万级→IVF_PQ。十亿级→DiskANN
3. **向量数据库**: 已有PG→pgvector(省新组件)。性能优先→Milvus(分布式/GPU加速)。快速上线→Pinecone(云托管)
4. **Embedding模型**: 中文场景→BGE-M3或Qwen3-Embedding。成本敏感→Gemini Embedding。多模态→Gemini Embedding 2
5. **后端框架**: 已有Spring项目→Spring AI。新项目+复杂Agent→LangChain4j。快速原型→Python+LangChain

**切勿过度设计**: 80%场景Advanced RAG就够了，Agentic RAG的token成本是普通4-8倍。
