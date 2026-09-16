# FaceE 题库

程序员技术面试题库，供 [FaceE](https://github.com/HBxtzhn/facee) App 离线使用。

- **300 题** · 11 个分类 · 13 个标签
- 分类：Java 基础 / Java 集合 / Java 并发 / JVM / Spring / MySQL / Redis / 计算机网络 / 操作系统 / 分布式系统 / 系统设计
- 格式：遵循 [题库规范 v1](https://github.com/HBxtzhn/facee/blob/main/docs/题库规范-v1.md)

## 安装到 App

App 默认题库地址已指向本仓库（无需手动填写）：

```text
https://github.com/HBxtzhn/facee-bank/archive/refs/heads/main.zip
```

也可以在 Release 里下载打包好的 `question-bank.zip`（catalog.json 在包根）。

## 目录结构

```text
catalog.json                    题库元数据（分类/标签/题目列表）
questions/<id>/question.md      题面
questions/<id>/answer.md        参考答案
questions/<id>/followups.md     面试官追问（可选）
questions/<id>/assets/          本地图片（可选）
```

## 许可与署名

⚠️ 本仓库包含两部分内容，许可不同：

- **原创部分**（结构、元数据、工具约定）：**CC-BY-4.0**
- **改编自 [JavaGuide](https://github.com/Snailclimb/JavaGuide) 的题目与答案**：**Apache-2.0**
  （版权归 JavaGuide 作者所有；我们做了拆分、标题规范化、正文精简、移除推广话术等修改）

使用前请阅读 [`NOTICE`](NOTICE) 与 [`sources.md`](sources.md)。
