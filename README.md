<p align="center">
  <strong>idea-to-docs</strong>
</p>
<p align="center">
  A Claude Code Skill that turns product ideas into complete technical documentation suites.
</p>
<p align="center">
  <a href="#english"><img src="https://img.shields.io/badge/lang-English-blue?style=for-the-badge" alt="English"></a>
  <a href="#中文"><img src="https://img.shields.io/badge/lang-中文-red?style=for-the-badge" alt="中文"></a>
</p>

---

<a id="english"></a>

## English

### What It Does

**Turn a product idea into a full technical documentation suite — automatically.**

Imagine handing a product idea to a seasoned tech lead who:

1. **Researches** multiple technical approaches, scores them on 6 dimensions, and presents a side-by-side comparison for you to choose.
2. **Decomposes** your system into well-defined domains (Frontend, Backend, Agent, Database...), draws the interaction graph, and maps every data flow.
3. **Generates** a complete documentation suite — 20-40+ files organized by domain, with numbered implementation order, canonical data models, domain-pair protocols, and built-in cross-review.

All through a **3-phase, multi-agent pipeline** with user approval gates:

```
Phase 1: Research & Compare
  PRD Analysis → N Approach Research (parallel) → Comparison Matrix
  → You pick the winning approach

Phase 2: Architecture Decomposition
  Domain Graph → Domain Internals → Data Models + Protocols
  → You approve the structure

Phase 3: Doc Generation
  Top-Level Docs + DB Schema + API Layer + Domain Docs (all parallel)
  → Structural Validation → Cross-Review & Auto-Fix
```

---

### Concrete Example: AI Dictation App

> "I want to build an AI dictation app — a web frontend for recording, a backend API, and an AI Agent that polishes the transcript for grammar and style."

Running `/idea-to-docs prd-dictation.md` would generate:

#### Generated Directory Structure

```
plans/
│
├── 00-MASTER-PLAN.md                    # System overview, 4-domain map, build order
├── 01-HUMAN-CHECKLIST.md                # Sign up for Deepgram, OpenAI, Supabase; get API keys
├── 02-ARCHITECTURE.md                   # Next.js + FastAPI + LangChain + PostgreSQL
├── 03-architecture-decision.md          # Why FastAPI over Express, why Deepgram over Whisper API
│
├── api/                                 ← CANONICAL SOURCE OF TRUTH
│   ├── 00-SUMMARY.md                    # 4×4 domain interaction matrix
│   │
│   ├── data-models/                     ← One file per business entity
│   │   ├── transcript.md                # Canonical shape: id, userId, rawText, polishedText, lang, duration, status...
│   │   ├── user.md                      # id, email, plan, createdAt...
│   │   ├── polish-job.md                # id, transcriptId, strategy, result, status...
│   │   └── enums.md                     # TranscriptStatus: recording→transcribing→draft→polishing→done
│   │
│   ├── protocols/                       ← One file per interacting domain pair
│   │   ├── frontend--backend.md         # REST: POST /transcripts, PATCH /transcripts/:id/polish, GET /transcripts...
│   │   ├── backend--database.md         # SQL: transcripts table CRUD, polish_jobs table CRUD
│   │   ├── backend--agent.md            # Internal: send raw text → receive polished text + diff
│   │   └── frontend--backend-ws.md      # WebSocket: real-time transcription streaming
│   │
│   └── auth.md                          # Supabase Auth JWT flow, middleware spec
│
├── database-schema/
│   ├── 00-overview.md                   # ER diagram, RLS policies, access pattern matrix
│   ├── 01-user-tables.md                # profiles, settings
│   └── 02-transcript-tables.md          # transcripts, polish_jobs, transcript_segments
│
├── frontend-design/
│   ├── 00-OVERVIEW.md                   # Layer architecture: Pages → Stores → Services → Types
│   ├── 01-project-setup.md              # Next.js 14, Tailwind, shadcn/ui
│   ├── 02-types-and-models.md           # TypeScript interfaces (mirrors api/data-models/)
│   ├── 03-service-layer.md              # API client: createTranscript(), pollTranscript(), requestPolish()...
│   ├── 04-store-layer.md                # Zustand: useTranscriptStore, useRecordingStore
│   ├── 05-auth-flow.md                  # Supabase Auth integration, protected routes
│   ├── 06-ui-components.md              # RecordButton, TranscriptCard, DiffViewer, WaveformDisplay
│   ├── 07-page-dashboard.md             # Transcript list, search, filter by status
│   ├── 08-page-recording.md             # Live recording UI, real-time transcript display
│   └── 09-page-transcript-detail.md     # View raw vs polished, accept/reject edits, re-polish
│
├── backend-design/
│   ├── 00-OVERVIEW.md                   # Layer architecture: Routes → Services → Repos → DB
│   ├── 01-project-setup.md              # FastAPI, Pydantic, SQLAlchemy, Deepgram SDK
│   ├── 02-auth-middleware.md            # JWT verification, Depends() injection
│   ├── 03-database-layer.md             # Repository pattern: TranscriptRepo, PolishJobRepo
│   ├── 04-transcript-endpoints.md       # CRUD for /api/v1/transcripts
│   ├── 05-polish-endpoints.md           # POST /transcripts/:id/polish, GET /polish-jobs/:id
│   ├── 06-deepgram-service.md           # Audio → text via Deepgram streaming API
│   └── 07-deployment.md                 # Docker, Railway/Fly.io config
│
├── agent-architecture/
│   ├── 00-summary.md                    # Polish Agent design philosophy: "preserve voice, fix errors"
│   ├── 01-polish-agent.md               # LangChain agent: grammar fix → style smoothing → format
│   ├── 02-prompt-templates.md           # System prompts per polish strategy (formal, casual, academic)
│   └── 03-diff-engine.md               # Generates word-level diff between raw and polished text
│
└── .working/                            # Intermediate research artifacts (auto-generated)
    ├── step1.1-prd-analysis.md
    ├── step1.2-approach-nextjs-fastapi.md
    ├── step1.2-approach-remix-express.md
    ├── step1.3-comparison.md
    ├── step2.1-domain-graph.md
    ├── step2.2-domain-internals.md
    └── step2.3-protocols-draft.md
```

#### What's Inside `api/data-models/transcript.md` (excerpt)

```markdown
# Data Model: Transcript

## Canonical Definition
| Field         | Type           | Required | Default | Description                    |
|---------------|----------------|----------|---------|--------------------------------|
| id            | uuid           | yes      | auto    | Primary key                    |
| user_id       | uuid           | yes      | -       | Owner                          |
| raw_text      | string         | yes      | ""      | Original transcription         |
| polished_text | string?        | no       | null    | Agent-polished version         |
| language      | string         | yes      | "en"    | ISO 639-1 code                 |
| duration_sec  | integer        | yes      | 0       | Audio duration in seconds      |
| status        | TranscriptStatus | yes    | "draft" | → see enums.md                 |
| created_at    | datetime       | yes      | now()   | Creation timestamp             |

## Field Mapping Table
| Canonical       | Frontend (TS)    | Backend (Py)     | Database (SQL)          |
|-----------------|------------------|------------------|-------------------------|
| id: uuid        | id: string       | id: UUID         | id UUID PRIMARY KEY      |
| user_id: uuid   | userId: string   | user_id: UUID    | user_id UUID NOT NULL    |
| raw_text: string| rawText: string  | raw_text: str    | raw_text TEXT NOT NULL   |
| polished_text   | polishedText?    | polished_text?   | polished_text TEXT       |
| status          | status: Status   | status: Status   | status TEXT CHECK(...)   |
| created_at      | createdAt: string| created_at: datetime | created_at TIMESTAMPTZ |
```

#### What's Inside `api/protocols/frontend--backend.md` (excerpt)

```markdown
# Protocol: Frontend ↔ Backend

Channel: REST (HTTPS)
Base URL: /api/v1

## Endpoints

### POST /transcripts
Auth: Bearer JWT (required)
Request:  CreateTranscriptRequest → see data-models/transcript.md (Frontend repr)
Response: Transcript              → see data-models/transcript.md (Frontend repr)
Status: 201 Created, 401 Unauthorized

### PATCH /transcripts/:id/polish
Auth: Bearer JWT
Request:  { "strategy": PolishStrategy }  → see data-models/enums.md
Response: PolishJob                       → see data-models/polish-job.md
Status: 202 Accepted (async), 404 Not Found

### Data Flow: Request Polish
  Frontend                           Backend                        Agent
     │ PATCH /transcripts/123/polish    │                              │
     │ { strategy: "formal" }           │                              │
     │─────────────────────────────────>│                              │
     │                                  │──> create PolishJob (DB)     │
     │                                  │──> send raw_text + strategy  │
     │                                  │─────────────────────────────>│
     │  202: { jobId, status: "pending" }│                             │
     │<─────────────────────────────────│                              │
     │                                  │    { polished_text, diff }   │
     │                                  │<─────────────────────────────│
     │  (poll GET /polish-jobs/456)      │──> update transcript + job  │
     │─────────────────────────────────>│                              │
     │  200: { status: "done", ... }    │                              │
     │<─────────────────────────────────│                              │
```

---

### Install

One command:

```bash
mkdir -p ~/.claude/skills/idea-to-docs && curl -fsSL https://raw.githubusercontent.com/junyuw2289-svg/idea-to-docs/main/SKILL.md -o ~/.claude/skills/idea-to-docs/SKILL.md
```

Restart Claude Code. Use `/idea-to-docs` as a slash command.

### Usage

```
/idea-to-docs path/to/my-prd.md
/idea-to-docs path/to/my-prd.md --output path/to/output-dir
```

---

<a id="中文"></a>

## 中文

### 它做了什么

**将产品创意一键转化为完整的技术文档体系 —— 全自动。**

想象你把一个产品想法交给一位经验丰富的技术负责人，他会：

1. **调研**多种技术方案，从 6 个维度打分，并排对比供你选择。
2. **拆解**系统为清晰的领域（前端、后端、Agent、数据库……），绘制交互图，梳理每一条跨域数据流。
3. **生成**完整文档集 —— 20-40+ 个文件按领域组织，带编号的实现顺序、规范数据模型、域间协议，内置交叉审查。

整个过程通过 **3 阶段、多代理流水线** 完成，每个关键决策点都有审批门：

```
阶段 1：调研与对比
  PRD 分析 → N 个方案调研（并行）→ 对比矩阵 → 你选定方案

阶段 2：架构拆解
  领域图 → 领域内部结构 → 数据模型 + 协议 → 你审批结构

阶段 3：文档生成
  顶层文档 + DB Schema + API 层 + 各领域文档（全部并行）
  → 结构验证 → 交叉审查 & 自动修复
```

---

### 具体示例：AI 听写 App

> "我想做一个 AI 听写应用 —— Web 前端录音，后端 API，再加一个 AI Agent 帮我做语法和风格润色。"

执行 `/idea-to-docs prd-dictation.md` 后会生成：

#### 生成的目录结构

```
plans/
│
├── 00-MASTER-PLAN.md                    # 系统概览、4 域地图、构建顺序
├── 01-HUMAN-CHECKLIST.md                # 注册 Deepgram、OpenAI、Supabase，获取 API Key
├── 02-ARCHITECTURE.md                   # Next.js + FastAPI + LangChain + PostgreSQL
├── 03-architecture-decision.md          # 为什么选 FastAPI 而非 Express，为什么选 Deepgram
│
├── api/                                 ← 规范数据源（唯一事实来源）
│   ├── 00-SUMMARY.md                    # 4×4 域间交互矩阵
│   ├── data-models/                     ← 每个业务实体一个文件
│   │   ├── transcript.md                # 规范定义：id, userId, rawText, polishedText, lang, duration, status...
│   │   ├── user.md                      # id, email, plan, createdAt...
│   │   ├── polish-job.md                # id, transcriptId, strategy, result, status...
│   │   └── enums.md                     # TranscriptStatus: recording→transcribing→draft→polishing→done
│   ├── protocols/                       ← 每对交互域一个文件
│   │   ├── frontend--backend.md         # REST: POST /transcripts, PATCH .../polish, GET ...
│   │   ├── backend--database.md         # SQL: transcripts 表 CRUD, polish_jobs 表 CRUD
│   │   ├── backend--agent.md            # 内部调用：发送原文 → 收回润色文 + diff
│   │   └── frontend--backend-ws.md      # WebSocket: 实时转写流
│   └── auth.md                          # Supabase Auth JWT 流程
│
├── database-schema/
│   ├── 00-overview.md                   # ER 图、RLS 策略、访问模式矩阵
│   ├── 01-user-tables.md                # profiles, settings
│   └── 02-transcript-tables.md          # transcripts, polish_jobs, transcript_segments
│
├── frontend-design/                     # 按层编号：Pages → Stores → Services → Types
│   ├── 00-OVERVIEW.md
│   ├── 01-project-setup.md              # Next.js 14, Tailwind, shadcn/ui
│   ├── 02-types-and-models.md           # TypeScript 接口（镜像 api/data-models/）
│   ├── 03-service-layer.md              # API 客户端函数
│   ├── 04-store-layer.md                # Zustand 状态管理
│   ├── 05-auth-flow.md                  # Supabase Auth 集成
│   ├── 06-ui-components.md              # RecordButton, TranscriptCard, DiffViewer...
│   ├── 07-page-dashboard.md             # 转写记录列表
│   ├── 08-page-recording.md             # 实时录音界面
│   └── 09-page-transcript-detail.md     # 原文 vs 润色对比，接受/拒绝编辑
│
├── backend-design/                      # 按层编号：Routes → Services → Repos → DB
│   ├── 00-OVERVIEW.md
│   ├── 01-project-setup.md              # FastAPI, Pydantic, SQLAlchemy
│   ├── 02-auth-middleware.md            # JWT 验证
│   ├── 03-database-layer.md             # Repository 模式
│   ├── 04-transcript-endpoints.md       # /api/v1/transcripts CRUD
│   ├── 05-polish-endpoints.md           # 润色相关端点
│   ├── 06-deepgram-service.md           # Deepgram 语音转文字
│   └── 07-deployment.md                 # Docker 部署
│
├── agent-architecture/                  # 基于组件
│   ├── 00-summary.md                    # 设计哲学："保留原声，修正错误"
│   ├── 01-polish-agent.md               # LangChain Agent：语法 → 风格 → 格式
│   ├── 02-prompt-templates.md           # 按润色策略分的系统提示词
│   └── 03-diff-engine.md               # 生成词级 diff
│
└── .working/                            # 中间研究产物
```

#### `api/data-models/transcript.md` 内部长什么样（节选）

```
# 数据模型：Transcript

## 规范定义
| 字段           | 类型             | 必填 | 默认值  | 说明            |
|---------------|------------------|------|---------|----------------|
| id            | uuid             | 是   | auto    | 主键            |
| user_id       | uuid             | 是   | -       | 所有者          |
| raw_text      | string           | 是   | ""      | 原始转写文本     |
| polished_text | string?          | 否   | null    | Agent 润色版本   |
| status        | TranscriptStatus | 是   | "draft" | → 见 enums.md   |

## 字段映射表
| 规范            | 前端 (TS)         | 后端 (Py)          | 数据库 (SQL)             |
|----------------|-------------------|--------------------|--------------------------|
| user_id: uuid  | userId: string    | user_id: UUID      | user_id UUID NOT NULL    |
| raw_text       | rawText: string   | raw_text: str      | raw_text TEXT NOT NULL   |
| polished_text  | polishedText?     | polished_text?     | polished_text TEXT       |
| created_at     | createdAt: string | created_at: datetime| created_at TIMESTAMPTZ  |
```

> 这张表就是跨域一致性的「锚点」—— 前端用 camelCase，后端用 snake_case，数据库用 SQL 类型，但映射关系在这里写得清清楚楚。

---

### 安装

一行命令：

```bash
mkdir -p ~/.claude/skills/idea-to-docs && curl -fsSL https://raw.githubusercontent.com/junyuw2289-svg/idea-to-docs/main/SKILL.md -o ~/.claude/skills/idea-to-docs/SKILL.md
```

重启 Claude Code 后，即可使用 `/idea-to-docs`。

### 使用方式

```
/idea-to-docs path/to/my-prd.md
/idea-to-docs path/to/my-prd.md --output path/to/output-dir
```

---

## License

MIT
