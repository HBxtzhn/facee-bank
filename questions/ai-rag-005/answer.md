2026年MTEB排行榜Top模型:
| 排名 | 模型 | MTEB分 | 维度 | 特点 |
|------|------|--------|------|------|
| 1 | Qwen3-Embedding-8B | 70.6 | 4096 | 多语言开源Apache 2.0 |
| 3 | gte-Qwen3-8B | 68.1 | 4096 | 阿里GTE系列 |
| 6 | OpenAI text-embedding-3-large | 64.6 | 3072 | 生态完善 |
| 8 | BGE-M3 | 63.2 | 1024 | 100+语言568M参数 |

选型建议:
- 中文极致性能 + 自部署 → BGE-M3或Qwen3-Embedding-8B
- 成本敏感 + 大规模 → Gemini Embedding($0.008/1M token)
- 边缘低延迟 → Qwen3-Embedding-0.6B(600M参数)
- 需要多模态 → Gemini Embedding 2或Cohere Embed v4
