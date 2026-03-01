# idea-to-docs

**Turn a product idea into a full technical documentation suite — automatically.**

> A Claude Code Skill that orchestrates a swarm of AI agents to transform your PRD (or even a rough idea) into production-grade, domain-based technical documentation — complete with architecture decisions, canonical data models, cross-domain protocols, and per-domain implementation specs.

---

## What It Does

Imagine handing a product idea to a seasoned tech lead who:

1. **Researches** multiple technical approaches, scores them on 6 dimensions, and presents a side-by-side comparison for you to choose from.
2. **Decomposes** your system into well-defined domains (Frontend, Backend, Agent, Database...), draws the interaction graph, and identifies every data flow between them.
3. **Generates** a complete documentation suite — 20-40+ files organized by domain, with numbered implementation order, canonical data models, domain-pair protocols, and cross-review validation.

All of this happens through a **3-phase, multi-agent pipeline** with user approval gates at every critical decision point:

```
Phase 1: Research & Compare
  - PRD Analysis → N Approach Research (parallel) → Comparison Matrix
  - You pick the winning approach

Phase 2: Architecture Decomposition
  - Domain Graph → Domain Internals → Data Models + Protocols
  - You approve the structure

Phase 3: Doc Generation
  - Top-Level Docs + DB Schema + API Layer + Domain Docs (parallel)
  - Structural Validation → Cross-Review & Auto-Fix
```

### Output Structure

```
plans/
├── 00-MASTER-PLAN.md              # System overview + reading guide
├── 01-HUMAN-CHECKLIST.md          # SaaS signups, API keys, env setup
├── 02-ARCHITECTURE.md             # Tech stack + design decisions
├── 03-architecture-decision.md    # ADR with approach comparison
├── api/                           # Canonical source of truth
│   ├── 00-SUMMARY.md              # N x N domain interaction matrix
│   ├── data-models/               # One file per business entity
│   └── protocols/                 # One file per domain pair
├── database-schema/               # Table groups + access patterns
├── frontend-design/               # Numbered files by layer
├── backend-design/                # Numbered files by layer
├── agent-architecture/            # Component-based docs
└── .working/                      # Intermediate research artifacts
```

Each domain directory is **self-contained** — an AI agent (or a developer) can read just one domain + its connected protocols and start implementing immediately.

---

## Usage

```
/idea-to-docs path/to/my-prd.md
/idea-to-docs path/to/my-prd.md --output path/to/output-dir
```

---

## Install

One command — paste it into your terminal:

```bash
mkdir -p ~/.claude/skills/idea-to-docs && curl -fsSL https://raw.githubusercontent.com/junyuw2289-svg/idea-to-docs/main/SKILL.md -o ~/.claude/skills/idea-to-docs/SKILL.md
```

After installation, restart Claude Code. You'll see `/idea-to-docs` available as a slash command.

---

---

# idea-to-docs

**将产品创意一键转化为完整的技术文档体系 —— 全自动。**

> 一个 Claude Code Skill，通过编排多个 AI 子代理，把你的 PRD（甚至一个粗略的想法）转化为生产级的、按领域组织的技术文档集 —— 涵盖架构决策、规范数据模型、跨域协议、以及逐域的实现规格说明。

---

## 它做了什么

想象你把一个产品想法交给了一位经验丰富的技术负责人，他会：

1. **调研**多种技术方案，从 6 个维度打分，生成并排对比表供你选择。
2. **拆解**系统为清晰的领域（前端、后端、Agent、数据库……），绘制交互图，识别所有跨域数据流。
3. **生成**完整的文档集 —— 20-40+ 个文件按领域组织，带编号的实现顺序、规范数据模型、域间协议，以及交叉审查验证。

整个过程通过 **3 阶段、多代理流水线** 完成，每个关键决策点都有用户审批门：

```
阶段 1：调研与对比
  - PRD 分析 → N 个方案调研（并行）→ 对比矩阵
  - 你选定方案

阶段 2：架构拆解
  - 领域图 → 领域内部结构 → 数据模型 + 协议
  - 你审批结构

阶段 3：文档生成
  - 顶层文档 + 数据库 Schema + API 层 + 各领域文档（并行）
  - 结构验证 → 交叉审查 & 自动修复
```

### 输出结构

```
plans/
├── 00-MASTER-PLAN.md              # 系统概览 + 阅读指南
├── 01-HUMAN-CHECKLIST.md          # SaaS 注册、API Key、环境配置
├── 02-ARCHITECTURE.md             # 技术栈 + 设计决策
├── 03-architecture-decision.md    # 方案对比 ADR
├── api/                           # 规范数据源（唯一事实来源）
│   ├── 00-SUMMARY.md              # N x N 域间交互矩阵
│   ├── data-models/               # 每个业务实体一个文件
│   └── protocols/                 # 每对交互域一个文件
├── database-schema/               # 表分组 + 访问模式
├── frontend-design/               # 按层编号的文件
├── backend-design/                # 按层编号的文件
├── agent-architecture/            # 基于组件的文档
└── .working/                      # 中间研究产物
```

每个领域目录都是**自包含的** —— AI 代理（或开发者）只需阅读一个领域目录 + 关联的协议文件即可开始实现。

---

## 使用方式

```
/idea-to-docs path/to/my-prd.md
/idea-to-docs path/to/my-prd.md --output path/to/output-dir
```

---

## 安装

一行命令，粘贴到终端即可：

```bash
mkdir -p ~/.claude/skills/idea-to-docs && curl -fsSL https://raw.githubusercontent.com/junyuw2289-svg/idea-to-docs/main/SKILL.md -o ~/.claude/skills/idea-to-docs/SKILL.md
```

安装后重启 Claude Code，即可使用 `/idea-to-docs` 斜杠命令。

---

## License

MIT
