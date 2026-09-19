## 追问 1：什么情况下可以跳过Rerank？

唯一可跳过的情况: 检索数量极少(3-5个)且索引精度已经很高。生产环境几乎总应使用Rerank。2026年推荐重排序模型: BGE-Reranker-v2-M3(中文效果好开源)、Cohere Rerank 3.5。

## 追问 2：为什么Rerank用Cross-Encoder比Bi-Encoder更精确？

Bi-Encoder分别编码query和doc不能做token级别的交互。Cross-Encoder把query和doc拼一起送进模型，模型同时看到双方内容的每个token，可以做更细粒度的相关性判断。代价是不能预计算，每个(query, doc)对都要过一遍模型。
