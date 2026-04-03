# SDD — Spec-Driven Development 模板

一套面向 Java 后端项目的 AI 编码协作规范，让 Claude Code / Codex 在开发过程中遵循 Spec 驱动的工作流，而不是随意生成代码。

## 核心理念

> **Code is Cheap, Context is Expensive**
>
> 代码是廉价的消耗品，文档（Spec）才是昂贵的核心资产。没有 Spec，不准写代码。

## 目录结构

```
sdd/
├── CLAUDE.md                        # Claude Code 入口，自动加载所有规则
├── AGENTS.md                        # Codex 入口    
├── agents/
│   ├── spec-reviewer.md             # Spec 合规审查 Agent
│   ├── code-quality-reviewer.md     # 代码质量审查 Agent
├── rules/
│   ├── project-context.md           # 工程上下文（首次使用时由 AI 填充）
│   ├── coding-style.md              # 编码规范
│   ├── security.md                  # 安全红线
│   └── domain-rules.md              # 业务领域约束
├── knowledge/
│   └── index.md                     # 领域知识索引（随实践积累）
└── changes/
    └── templates/
        ├── spec.md                  # 变更规格书模板
        ├── tasks.md                 # 执行计划模板
        ├── test-spec.md             # 单测 Spec 模板
        └── log.md                   # 变更日志模板
```

## 快速接入

### 1. 复制到你的 Java 项目

```
your-java-project/
├── CLAUDE.md       ← 从 sdd/CLAUDE.md 复制过来，修改 @import 路径
├── sdd/            ← 整个 sdd 目录复制过来
└── src/
```

将 `CLAUDE.md` 中的 `@sdd/rules/...` 路径按实际目录结构调整。

### 2. 初始化项目上下文

用 Claude Code 打开项目后执行：

```
/init
```

AI 会自动分析工程结构，填充 `rules/project-context.md`。

### 3. 开始开发

```
/propose 用户登录支持手机号验证码
```

## 命令速查

| 命令 | 用途 |
|------|------|
| `/init` | 初始化工程上下文 |
| `/propose <需求>` | 创建变更提案，生成 spec + tasks |
| `/apply <变更名>` | 按 tasks 逐步执行编码 |
| `/review <变更名>` | 两阶段审查（Spec 合规 + 代码质量） |
| `/fix <变更名>` | Review 后修正迭代 |
| `/test <变更名>` | TDD 生成单测 |
| `/archive <变更名>` | 归档并沉淀知识 |

## 开发工作流

```
/propose  →  /apply  →  /review  →  /fix  →  /test  →  /archive
  需求         编码        审查        修正      测试       归档
```

每个变更对应 `changes/` 下一组文件：

```
changes/
└── your-feature/
    ├── spec.md       # 规格书
    ├── tasks.md      # 执行计划
    ├── test-spec.md  # 单测规格（可选）
    └── log.md        # 变更日志
```

## Superpowers 集成

本规范内置了与 [Claude Code Superpowers](https://github.com/anthropics/claude-code) 的集成，各命令会在关键节点自动调用对应技能：

| 节点 | 技能 |
|------|------|
| `/propose` 开始前 | `superpowers:brainstorming` — 探索需求意图 |
| `/apply` 每个 task 完成后 | `superpowers:verification-before-completion` — 强制展示验证证据 |
| `/review` | `superpowers:requesting-code-review` |
| `/fix` 收到反馈后 | `superpowers:receiving-code-review` |
| `/test` | `superpowers:test-driven-development` |
| 遇到 bug | `superpowers:systematic-debugging` |
| 开新功能分支 | `superpowers:using-git-worktrees` |
| `/archive` 后 | `superpowers:finishing-a-development-branch` |

## 适用于 Codex

将 `agents/copilot-prompt.md` 的内容作为 System Prompt 传入，行为与 Claude Code 保持一致。

## Git 规范

- 禁止直接向 `master` / `main` 分支提交
- 每个 Task 对应一个 commit，格式：`[变更名] 中文简述`
- 禁止自动 push，由开发者手动确认后推送
