# FaceE 题库（示例）

本仓库是 **纯题库数据**，遵循 FaceE 题库规范 v1：`catalog.json` 位于仓库根，
题目位于 `questions/<id>/`。因此 GitHub 的归档 ZIP 可以**直接安装**：

```text
https://github.com/HBxtzhn/facee-bank/archive/refs/heads/main.zip
```

（归档 ZIP 会多一层 `facee-bank-main/` 目录，App 会自动识别题库根。）

## 结构

```text
catalog.json
questions/
  <id>/
    question.md
    answer.md        可选
    followups.md     可选（面试官追问）
    assets/          可选
```

规范细节见 App 仓库的 `docs/题库规范-v1.md`。生成器见 `packages/bank-spec`。
