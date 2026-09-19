## 追问 1：项目里为什么选pgvector而不是Milvus？

(如果简历写了pgvector) 复用PostgreSQL不需要额外部署和维护独立向量数据库，省资源和运维成本。数据量不大的情况下pgvector的HNSW索引够用。4C8G资源有限时优先选pgvector(个人项目最省资源)。

## 追问 2：生产RAG系统你最关注哪三个指标？

1) Faithfulness(忠实度)——生成内容是否忠实于检索文档防幻觉; 2) Context Recall(上下文召回率)——相关文档是否被检索到防遗漏; 3) 端到端延迟——检索延迟+Rerank延迟+LLM生成延迟，用户等不了太久。用RAGAS框架评估，线上采样5-10%流量实时监控。
