# FaceE 题库

一份**开源的程序员面试题库**，475 题，覆盖 Java 后端常见方向，配合 [FaceE](https://github.com/HBxtzhn/facee) 离线刷题。

## 怎么用

在 FaceE 的「我的」页把题库地址填成下面这个（App 默认已指向这里）：

```
https://github.com/HBxtzhn/facee-bank/archive/refs/heads/main.zip
```

也可以在 [Releases](https://github.com/HBxtzhn/facee-bank/releases/latest) 下载打包好的 `question-bank.zip`。

## 包含什么

| 分类 | 题数 |
|---|---:|
| Java 基础 | 29 |
| Java 集合 | 30 |
| Java 并发 | 31 |
| JVM | 69 |
| Spring | 30 |
| Spring Cloud | 53 |
| MySQL | 32 |
| Redis | 77 |
| 计算机网络 | 28 |
| 操作系统 | 28 |
| 分布式系统 | 32 |
| 系统设计 | 26 |
| AI 应用与 RAG | 10 |

合计 **475 题**（简单 184 / 中等 229 / 困难 62）。

## 自己维护一份题库

本仓库是**纯数据**，没有构建步骤：`catalog.json` + `questions/<id>/`，改完提交即可。
格式见 [题库规范 v1](https://github.com/HBxtzhn/facee/blob/main/docs/题库规范-v1.md)。
不想手写可以用 FaceE 仓库里的 `packages/bank-spec`，能在本地生成并校验一份合格的题库包。

## 来源与许可

- 题目与答案**改编自** [JavaGuide](https://github.com/Snailclimb/JavaGuide)（Apache-2.0）：已附带其许可证文本（`LICENSE-Apache-2.0`）并声明改动（`NOTICE`）
- v1.2.0 新增题目（JVM / Redis / Spring Cloud / AI 应用与 RAG 等）：改编自**作者自整理面试笔记**，已做脱敏与去个性化处理
- 结构、元数据等原创部分：**CC-BY-4.0**
- 评估过但未采用的来源及原因：见 [`sources.md`](sources.md)
