# AgentTeacher

Concept-teaching skill: explains a technical concept through a fixed six-part structure — intuition → example (runnable or pseudocode) → walkthrough → trap → pointers → test questions.

## 启动前

- 个人/全局规则可放在仓库外；本文件只记录 AgentTeacher 项目内的 Claude Code 入口和维护规则。
- 仓库地图、Working Rules、Current Risk Areas、Verification、Release Flow 全在 `AGENTS.md`。
- skill 主入口看 `SKILL.md`；教学法看 `references/teaching-method.md`；教学代码风格看 `references/cod·-style-for-teaching.md`；概念→语言映射看 `references/concept-to-language.md`。

## 常用命令

```bash
bash scripts/package-skill.sh                  # 打包 dist/agent-teacher.zip
```

项目没有构建/渲染流水线 —— 输出是对话里的 markdown。没有专门的 test runner；验证靠在真实 prompt 上跑一遍 skill 并检查产出。

## 项目独有硬规则

- 六层骨架 L1–L6（intuition → example → walkthrough → trap → pointers → test questions）是契约。不要加 L7，不要删层。某一层在特定概念上感觉冗余时，写一行带过，不要省略。
- Mode A (runnable) / Mode B (pseudocode) 二选一。Mode B 自动写 sidecar 文件到 `/tmp/<slug>-pseudocode.<ext>`；Mode A 只在用户明确要求时写文件。
- 伪代码必须是真 Python/PyTorch 语法 + 每个形状变化的 shape 注释。**禁止**自然语言伪代码（`FOR each token DO ...`）。
- L6 是"测试题"，不是"理解检查"。给 hint 不给答案，题目本身要有具体性（"如果 reward 全相等会发生什么"而不是"你理解了吗"）。**不要 frame 成面试场景**，不要写"interview"字样。
- **Enrichments E1–E8** 是按概念性质有条件触发的，**不是默认全开**。决策表在 `references/enrichments.md`。硬上限 5 个/lesson，硬下限 0 个（大多数概念跑 plain 六层就够）。enrichment 框架以 DL 为主调，但 E2/E3/E4/E5/E7/E8 也适用于数据结构、分布式协议、复杂算法。**语言特性**（闭包、generator、channel）一律不上 enrichment。
- Mode B 有三种 flavor：DL（shape 注释）、协议（参与方状态 + 消息箭头）、算法/数据结构（invariant + concrete trace）。E2（state tracking）在三种 flavor 里是同一原则、不同载体。
- 改 `SKILL.md` 时同步更新 `references/teaching-method.md` 的对应 playbook —— SKILL.md 是契约，references 是带例子的展开，两者必须一致。
- 不打包 `evals/` / `.claude-plugin/` / `dist/` 到 release zip。Exclude 列表在 `scripts/package-skill.sh`。
- 改 `scripts/package-skill.sh` 或新增任何要进 zip 的文件后，刷新并检查 `dist/agent-teacher.zip`，确认新文件确实被 `git add` 过（包脚本基于 `git ls-files`，未追踪的文件会静默消失）。
- 不提交一次性 review 报告或诊断快照；稳定规则沉淀到 `AGENTS.md`、`CLAUDE.md`、`SKILL.md` 或 `references/`，其余丢弃。
