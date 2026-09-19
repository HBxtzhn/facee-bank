## 追问 1：RAG系统的性能瓶颈排序？

1) LLM推理延迟(>80%端到端时间); 2) Embedding计算(本地跑模型需GPU); 3) 文档解析(大PDF OCR可能几十秒); 4) 数据库查询(有索引情况下不是瓶颈); 5) 向量检索(Milvus毫秒级不是瓶颈)。

## 追问 2：如果要支持用户把整个GitHub仓库变成知识库怎么做？

1) JGit克隆仓库到临时目录; 2) 文件遍历+过滤(排除.gitignore/二进制，留.java/.md/.py等); 3) 代码感知分块——按函数/类边界切割而非简单字符分块; 4) 结构化上下文增强——chunk metadata带file_path/package_name/class_name/function_name; 5) 检索时先metadata过滤再语义搜索。难点: 中型仓库数百万token、代码文档混合需不同策略、版本更新需增量更新(记录文件hash只处理changed files)。
