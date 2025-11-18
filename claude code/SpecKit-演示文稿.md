# SpecKit 演示文稿

## 使用 Claude Code 进行规范化功能开发的最佳实践

---

## 目录

1. [SpecKit 简介](#speckit-简介)
2. [核心概念](#核心概念)
3. [完整工作流程](#完整工作流程)
4. [最佳实践](#最佳实践)
5. [常见问题解决方案](#常见问题解决方案)
6. [实战案例](#实战案例)
7. [相关资源](#相关资源)

---

## SpecKit 简介

### 什么是 SpecKit?

SpecKit 是一套集成在 Claude Code 中的功能开发规范化工具链，通过一系列 slash 命令帮助开发者：

- 📋 **规范化需求**: 从自然语言描述生成结构化的功能规格说明
- 🏗️ **标准化设计**: 生成技术方案、数据模型和 API 契约
- ✅ **自动化任务**: 生成依赖排序的可执行任务列表
- 🤖 **智能实施**: 自动执行任务并生成代码

### 为什么需要 SpecKit?

**传统开发痛点:**
- 需求理解不一致，返工率高
- 设计文档缺失或过时
- 任务拆分不合理，依赖关系混乱
- 文档与代码脱节

**SpecKit 的价值:**
- ✅ 确保需求明确且可测试
- ✅ 强制分离 WHAT（需求）和 HOW（实现）
- ✅ 自动生成结构化文档
- ✅ 支持增量开发和独立测试
- ✅ 文档即代码，同步更新

---

## 核心概念

### 三层架构

```
┌─────────────────────────────────────────────────────┐
│ 1. Specification Layer (需求层)                      │
│    /speckit.specify - 生成 spec.md                   │
│    定义 WHAT 和 WHY，不涉及技术实现                    │
└─────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────┐
│ 2. Planning Layer (设计层)                           │
│    /speckit.plan - 生成 plan.md, contracts/, etc.   │
│    定义 HOW，包括技术栈、架构、API 设计                 │
└─────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────┐
│ 3. Execution Layer (执行层)                          │
│    /speckit.tasks - 生成 tasks.md                    │
│    /speckit.implement - 执行任务并生成代码             │
└─────────────────────────────────────────────────────┘
```

### 核心命令

| 命令 | 用途 | 输入 | 输出 |
|------|------|------|------|
| `/speckit.specify` | 创建功能规格 | 自然语言描述 | `spec.md` + 分支 |
| `/speckit.clarify` | 需求澄清 | 现有 spec | 澄清问题列表 |
| `/speckit.plan` | 技术设计 | spec.md | `plan.md`, `data-model.md`, `contracts/` |
| `/speckit.tasks` | 生成任务 | plan + spec | `tasks.md` (可执行任务列表) |
| `/speckit.implement` | 自动实施 | tasks.md | 代码实现 |
| `/speckit.analyze` | 质量分析 | 所有文档 | 一致性报告 |
| `/speckit.constitution` | 项目约定 | - | `constitution.md` |

### 文件结构

```
project-root/
├── .specify/
│   ├── memory/
│   │   └── constitution.md         # 项目开发约定
│   ├── templates/                  # 文档模板
│   └── scripts/                    # 自动化脚本
├── .claude/
│   └── commands/                   # SpecKit 命令定义
└── specs/
    └── 001-feature-name/           # 功能目录(自动生成)
        ├── spec.md                 # 需求规格
        ├── plan.md                 # 技术方案
        ├── tasks.md                # 任务列表
        ├── data-model.md           # 数据模型
        ├── research.md             # 技术调研
        ├── contracts/              # API 契约
        │   └── api-spec.yaml
        └── checklists/             # 质量检查清单
            └── requirements.md
```

---

## 完整工作流程

### 第一步: 创建功能规格 (`/speckit.specify`)

**命令:**
```bash
/speckit.specify 添加用户认证功能，支持邮箱密码登录和 OAuth2
```

**执行流程:**
1. 自动生成分支名称 (如 `001-user-auth`)
2. 检查是否存在同名功能分支
3. 创建新分支并切换
4. 生成 `specs/001-user-auth/spec.md`
5. 填充用户场景、验收标准、功能需求
6. 运行质量检查 (生成 `checklists/requirements.md`)
7. 如有 `[NEEDS CLARIFICATION]` 标记，提出最多 3 个澄清问题

**spec.md 包含内容:**
- ✅ 用户场景 (按优先级 P1, P2, P3 排序)
- ✅ 每个场景的独立测试标准
- ✅ Given-When-Then 验收条件
- ✅ 功能需求 (FR-001, FR-002...)
- ✅ 成功标准 (可度量、技术无关)
- ✅ 边界情况处理
- ❌ 不包含任何技术实现细节

**最佳实践:**
- 描述要具体，包含关键业务规则
- 如果需求复杂，分多个优先级的用户故事
- 每个用户故事应该可以独立实现和测试

### 第二步: 需求澄清 (`/speckit.clarify`) [可选]

**使用场景:**
- spec.md 中有 `[NEEDS CLARIFICATION]` 标记
- 发现需求理解有歧义
- 需要补充边界情况

**命令:**
```bash
/speckit.clarify
```

**执行流程:**
1. 读取当前分支的 spec.md
2. 识别所有 `[NEEDS CLARIFICATION]` 标记
3. 为每个标记生成结构化问题 (带选项表格)
4. 等待用户回答
5. 更新 spec.md，移除所有标记

**最佳实践:**
- 尽量在 specify 阶段完成，避免多次澄清
- 回答时选择具体选项 (A/B/C) 或提供自定义答案
- 澄清后再运行 `/speckit.plan`

### 第三步: 技术设计 (`/speckit.plan`)

**命令:**
```bash
/speckit.plan
```

**执行流程:**

**Phase 0 - 调研与决策:**
1. 读取 spec.md 和 constitution.md
2. 识别技术选型需求
3. 生成 `research.md` 记录:
   - 技术选型决策 (框架、库、工具)
   - 选择理由
   - 考虑的替代方案

**Phase 1 - 设计与契约:**
1. 生成 `data-model.md`:
   - 核心实体定义
   - 字段、关系、验证规则
   - 状态转换逻辑
2. 生成 `contracts/` 目录:
   - API 端点定义 (OpenAPI/GraphQL)
   - 请求/响应格式
   - 错误代码说明
3. 生成 `quickstart.md`:
   - 快速测试指南
   - 示例 API 调用
4. 更新 Agent Context:
   - 运行 `.specify/scripts/bash/update-agent-context.sh`
   - 记录新技术栈到 CLAUDE.md

**plan.md 包含内容:**
- ✅ 技术栈选择 (语言、框架、数据库)
- ✅ 项目结构 (目录、模块划分)
- ✅ 架构模式 (MVC、微服务、分层架构)
- ✅ 第三方集成 (OAuth 提供商、支付网关)
- ✅ 宪法合规检查 (符合 constitution.md 原则)

**最佳实践:**
- 技术选型要考虑现有项目栈
- 明确标注架构决策的理由
- 契约设计要符合 RESTful/GraphQL 规范

### 第四步: 生成任务 (`/speckit.tasks`)

**命令:**
```bash
/speckit.tasks
```

**执行流程:**
1. 读取 plan.md, spec.md, data-model.md, contracts/
2. 按用户故事优先级组织任务
3. 生成依赖排序的任务列表
4. 标记可并行任务 `[P]`
5. 为每个用户故事生成独立测试标准
6. 输出 `tasks.md`

**tasks.md 结构:**
```markdown
## Phase 1: Setup (项目初始化)
- [ ] T001 创建项目结构
- [ ] T002 [P] 配置开发环境
- [ ] T003 [P] 安装依赖包

## Phase 2: Foundational (基础设施)
- [ ] T004 实现数据库连接层
- [ ] T005 创建日志工具

## Phase 3: User Story 1 (P1) - 邮箱密码登录
- [ ] T006 [P] [US1] 创建 User 模型 (src/models/user.py)
- [ ] T007 [US1] 实现 AuthService (src/services/auth.py)
- [ ] T008 [US1] 创建登录 API 端点 (src/api/auth.py)
- [ ] T009 [US1] 集成测试

## Phase 4: User Story 2 (P2) - OAuth2 集成
- [ ] T010 [P] [US2] 实现 OAuth2 客户端 (src/oauth/client.py)
...

## Dependencies
- Phase 2 必须在 Phase 3+ 之前完成
- User Story 1-3 可以独立并行开发
```

**任务格式规则:**
```
- [ ] [TaskID] [P] [Story] 描述 + 文件路径

- [ ]      : Markdown 复选框 (必需)
[TaskID]   : 任务编号 T001, T002... (必需)
[P]        : 可并行标记 (可选，不同文件且无依赖)
[Story]    : 用户故事标记 [US1], [US2]... (仅用户故事阶段)
描述       : 清晰的动作 + 具体文件路径 (必需)
```

**最佳实践:**
- 任务粒度适中 (1-4 小时完成)
- 明确标注文件路径
- 优先完成 P1 用户故事 (MVP)
- 利用并行任务 `[P]` 提高效率

### 第五步: 自动实施 (`/speckit.implement`)

**命令:**
```bash
/speckit.implement
```

**执行流程:**
1. 读取 tasks.md
2. 按顺序执行每个任务:
   - 读取相关设计文档
   - 生成代码实现
   - 运行测试 (如果有)
   - 标记任务完成 `[x]`
3. 遇到错误时暂停，等待修复
4. 所有任务完成后生成实施报告

**监控进度:**
- Claude Code 会实时更新 tasks.md 中的复选框
- 可以随时中断并稍后继续

**最佳实践:**
- 第一次运行建议只实施 Phase 1-2 和一个用户故事
- 验证生成代码质量后再继续
- 保持 Git 提交频率 (每完成一个 Phase)

### 第六步: 质量分析 (`/speckit.analyze`) [推荐]

**命令:**
```bash
/speckit.analyze
```

**执行流程:**
1. 交叉验证 spec.md, plan.md, tasks.md
2. 检查一致性:
   - spec 中的用户故事是否都有对应任务?
   - plan 中的技术选型是否都被使用?
   - tasks 中的文件路径是否符合 plan 的结构?
3. 生成分析报告

**最佳实践:**
- 在运行 `/speckit.implement` 之前执行
- 发现问题时回退修复文档

---

## 最佳实践

### 1. Constitution 优先原则

**什么是 Constitution?**

Constitution (宪法文档) 是项目级别的开发约定和原则，存储在 `.specify/memory/constitution.md`。

**核心内容:**
```markdown
## Core Principles
1. 所有文档必须使用简体中文撰写
2. 用户故事、验收标准、技术方案全部用中文
3. 代码变量名、函数名保持英文，但注释必须中文
4. 严禁在文档中出现全英文段落

### I. Automation-First Architecture
每个功能必须优先考虑自动化...

### II. Session Persistence & Recovery
会话管理必须可靠且透明...
```

**为什么重要?**
- ✅ 确保团队遵循统一的开发规范
- ✅ 自动集成到 plan.md 的合规检查
- ✅ 新成员快速了解项目约定

**最佳实践:**
- 项目开始时运行 `/speckit.constitution` 创建宪法
- 定义 3-7 个核心原则 (不要太多)
- 包含强制要求 (MUST) 和理由 (Rationale)
- 定期审查和更新 (使用版本号)

**宪法示例:**
```markdown
### III. API-First Integration (API 优先集成)

所有外部系统交互必须使用 API 端点。
授权令牌必须从真实浏览器请求中捕获并在 API 调用中复用。

**Rationale**: API 提供更快、更可靠的数据访问。
从浏览器捕获令牌确保认证兼容性。
```

### 2. 用户故事优先级策略

**P1 (Must Have) - MVP 核心:**
- 系统最基本的价值
- 必须在第一版发布
- 示例: "用户可以使用邮箱密码登录"

**P2 (Should Have) - 重要增强:**
- 显著提升用户体验
- 可以延后但应该尽快实现
- 示例: "用户可以通过 Google OAuth 登录"

**P3 (Nice to Have) - 可选优化:**
- 锦上添花的功能
- 可以在后续迭代中实现
- 示例: "用户可以查看登录历史记录"

**独立测试标准:**

每个用户故事必须包含独立测试描述:
```markdown
### User Story 1 - 邮箱密码登录 (Priority: P1)

**Independent Test**:
仅实现此功能后，用户即可通过邮箱和密码完成登录，
并看到个人主页，无需依赖其他登录方式。

**Acceptance Scenarios**:
1. **Given** 用户在登录页面，**When** 输入正确的邮箱和密码，
   **Then** 成功登录并跳转到主页
2. **Given** 用户输入错误密码，**When** 点击登录，
   **Then** 显示错误提示且停留在登录页
```

### 3. 避免技术泄漏

**Spec.md 中禁止出现的内容:**
- ❌ 技术栈名称 (React, PostgreSQL, Redis)
- ❌ 架构模式 (微服务、MVC、REST)
- ❌ 具体 API 端点 (/api/v1/users)
- ❌ 数据库表设计
- ❌ 代码结构 (src/models/user.py)

**正确的需求描述:**
- ✅ "系统必须在 2 秒内完成登录验证"
- ✅ "用户必须能够重置密码"
- ✅ "系统必须支持 1000 并发用户"
- ✅ "错误信息必须用户友好且可操作"

**Plan.md 中应该包含的内容:**
- ✅ 技术栈: Node.js 20, Express, PostgreSQL 15
- ✅ 架构: 三层架构 (Controller-Service-Repository)
- ✅ API 设计: RESTful 风格
- ✅ 安全: JWT token + bcrypt 密码哈希

### 4. 渐进式开发

**推荐流程:**

```
第一次迭代: MVP (只实现 P1)
├─ /speckit.specify "核心功能"
├─ /speckit.plan
├─ /speckit.tasks
├─ /speckit.implement (只运行 P1 任务)
└─ 测试、部署、收集反馈

第二次迭代: 增强 (添加 P2)
├─ 基于反馈更新 spec.md
├─ /speckit.plan (更新技术方案)
├─ /speckit.tasks (生成 P2 任务)
└─ /speckit.implement (只运行 P2 任务)

第三次迭代: 优化 (添加 P3)
└─ ...
```

**优势:**
- ✅ 快速验证核心价值
- ✅ 及早发现设计问题
- ✅ 降低风险 (避免大量返工)
- ✅ 持续交付价值

### 5. 任务并行化

**识别可并行任务:**

使用 `[P]` 标记的条件:
- ✅ 操作不同文件
- ✅ 没有依赖未完成的任务
- ✅ 不需要共享状态

**示例:**
```markdown
## Phase 3: User Story 1
- [ ] T006 [P] [US1] 创建 User 模型 (src/models/user.py)
- [ ] T007 [P] [US1] 创建 Auth 工具类 (src/utils/auth.py)
- [ ] T008 [US1] 实现 AuthService (src/services/auth.py)
       ↑ 依赖 T006 和 T007，不能并行
```

**执行策略:**
- 手动模式: 同时打开多个 Claude Code 会话，分别执行 `[P]` 任务
- 自动模式: `/speckit.implement` 会智能调度并行任务

### 6. 质量检查清单

**Requirements Checklist** (自动生成):

在 `/speckit.specify` 后自动生成 `checklists/requirements.md`:
```markdown
## Content Quality
- [ ] 无实现细节 (语言、框架、API)
- [ ] 聚焦用户价值和业务需求
- [ ] 为非技术干系人撰写

## Requirement Completeness
- [ ] 无 [NEEDS CLARIFICATION] 标记残留
- [ ] 需求可测试且无歧义
- [ ] 成功标准可度量
- [ ] 所有验收场景已定义
```

**最佳实践:**
- `/speckit.specify` 完成后立即审查清单
- 所有项目都打勾后再运行 `/speckit.plan`
- 如有未通过项，更新 spec.md 后重新检查

---

## 常见问题解决方案

### 问题 1: 如何让 Claude Code 使用中文输出文档?

**问题描述:**
运行 `/speckit.specify` 后生成的 spec.md 全是英文，但希望团队使用中文文档。

**解决方案 (三层配置):**

#### 方法 1: 全局配置 (推荐，适用所有项目)

编辑 `~/.claude/CLAUDE.md`:
```markdown
- Always reply in Chinese
- 所有文档(spec.md、plan.md、tasks.md、README、代码注释)必须使用简体中文撰写
- 用户故事、验收标准、技术方案全部用中文
- 代码变量名、函数名、类名保持英文(行业惯例)，但所有注释必须是中文
- 严禁在任何文档中出现全英文段落，除非是代码片段或专有名词
```

#### 方法 2: 项目级配置 (适用单个项目)

编辑 `<project-root>/CLAUDE.md`:
```markdown
# CLAUDE.md

## Language Requirements
- 所有 SpecKit 生成的文档(spec.md、plan.md、tasks.md)必须使用简体中文
- 用户故事、验收标准、功能需求、技术方案全部用中文
- 代码变量名、函数名保持英文，注释使用中文
```

#### 方法 3: Constitution 配置 (最强约束)

运行 `/speckit.constitution` 并添加语言原则:
```markdown
## Core Principles

### 0. 语言规范 (Language Standards)

1. 所有文档(包括 specs.md、plan.md、tasks、README、代码注释)必须使用简体中文撰写。
2. 用户故事、验收标准、技术方案、任务描述全部用中文。
3. 代码变量名、函数名、类名保持英文(行业惯例)，但所有注释必须是中文。
4. 如果用户用中文提问，AI 必须用中文回复。
5. 严禁在任何文档中出现全英文段落，除非是代码片段或专有名词。

**Rationale**: 确保团队成员理解文档，降低沟通成本。
中英文混合的代码符合国际开源项目惯例。
```

**验证配置生效:**
```bash
# 重新运行命令测试
/speckit.specify 添加数据导出功能

# 检查生成的 spec.md 是否为中文
```

**优先级:** Constitution > 项目 CLAUDE.md > 全局 ~/.claude/CLAUDE.md

### 问题 2: spec.md 中出现过多 [NEEDS CLARIFICATION] 标记

**问题描述:**
生成的 spec.md 中有 10+ 个 `[NEEDS CLARIFICATION]` 标记，逐个澄清太耗时。

**解决方案:**

#### 策略 1: 提供更详细的功能描述

**差的描述 (会产生很多标记):**
```bash
/speckit.specify 添加用户管理功能
```

**好的描述 (减少歧义):**
```bash
/speckit.specify 添加用户管理功能，包括：
1. 管理员可以查看所有用户列表（分页，每页50条）
2. 管理员可以禁用/启用用户账号
3. 管理员可以重置用户密码
4. 普通用户只能查看和编辑自己的个人信息
5. 操作日志需要记录（谁在什么时间做了什么操作）
```

#### 策略 2: 接受合理默认值

SpecKit 会在以下情况自动使用行业标准默认值:
- ✅ 数据保留策略 → 行业标准
- ✅ 性能目标 → Web/移动应用标准预期
- ✅ 错误处理 → 用户友好提示 + 适当降级
- ✅ 认证方式 → 标准 Session/OAuth2

**查看 spec.md 的 Assumptions 部分**，确认默认值是否合理。

#### 策略 3: 最多 3 个澄清问题限制

SpecKit 设计上最多生成 3 个 `[NEEDS CLARIFICATION]` 标记，优先级:
1. **Scope (范围)** - 影响功能边界
2. **Security/Privacy (安全/隐私)** - 法律/财务影响
3. **User Experience (用户体验)** - 核心交互流程
4. ~~Technical Details (技术细节)~~ - 延后到 plan.md 决策

如果超过 3 个，低优先级的会使用默认值并记录到 Assumptions。

### 问题 3: tasks.md 生成的任务顺序不合理

**问题描述:**
生成的任务中，后面的任务依赖前面未完成的任务，但没有明确标注。

**解决方案:**

#### 手动调整 tasks.md

Tasks.md 是 Markdown 文件，可以手动编辑:

**调整前 (依赖关系不清):**
```markdown
## Phase 3: User Story 1
- [ ] T008 [US1] 实现 AuthService (src/services/auth.py)
- [ ] T006 [P] [US1] 创建 User 模型 (src/models/user.py)
- [ ] T007 [P] [US1] 创建 Auth 工具类 (src/utils/auth.py)
```

**调整后 (依赖清晰):**
```markdown
## Phase 3: User Story 1
- [ ] T006 [P] [US1] 创建 User 模型 (src/models/user.py)
- [ ] T007 [P] [US1] 创建 Auth 工具类 (src/utils/auth.py)
- [ ] T008 [US1] 实现 AuthService (src/services/auth.py)
      ↑ 依赖: T006, T007
```

#### 添加 Dependencies 部分

在 tasks.md 中补充依赖说明:
```markdown
## Dependencies

### Phase Level
- Phase 2 (Foundational) → 必须在 Phase 3+ 之前完成
- Phase 3-5 (User Stories) → 可以独立并行开发

### Task Level
- T008 依赖 T006, T007 (需要 User 模型和 Auth 工具)
- T015 依赖 T008 (API 端点需要 Service 层)
```

#### 重新生成 tasks.md

如果问题严重:
```bash
# 1. 备份现有 tasks.md
mv specs/001-feature/tasks.md specs/001-feature/tasks.md.bak

# 2. 更新 plan.md，明确架构分层
# 编辑 plan.md，清晰定义模块依赖关系

# 3. 重新生成
/speckit.tasks
```

### 问题 4: `/speckit.plan` 生成的技术栈与现有项目不一致

**问题描述:**
现有项目使用 Node.js + PostgreSQL，但 plan.md 建议使用 Python + MongoDB。

**解决方案:**

#### 方法 1: 在 constitution.md 中声明技术栈

编辑 `.specify/memory/constitution.md`:
```markdown
## Core Principles

### VIII. 技术栈约束 (Technology Stack Constraints)

所有新功能必须使用以下技术栈:
- **后端**: Node.js 20+ with TypeScript
- **Web 框架**: Express.js 4.x
- **数据库**: PostgreSQL 15+
- **ORM**: Prisma 5.x
- **认证**: JWT with jsonwebtoken library
- **测试**: Jest + Supertest

**Rationale**: 保持技术栈一致性，降低维护成本，
复用现有基础设施和团队技能。

**例外情况**: 性能瓶颈或特殊需求可申请使用替代技术，
需在 plan.md 中详细说明理由。
```

#### 方法 2: 在项目 CLAUDE.md 中声明

编辑 `<project-root>/CLAUDE.md`:
```markdown
## Active Technologies
- Node.js 20.19.1 (JavaScript ES6+ with async/await)
- Express.js 4.18.2 (Web framework)
- PostgreSQL 15.3 (Primary database)
- Prisma 5.7.1 (ORM)
- JWT authentication (jsonwebtoken 9.0.2)

## Technology Constraints
- 所有 API 必须使用 Express.js 路由
- 数据访问必须通过 Prisma ORM
- 禁止直接使用 SQL 查询，除非性能优化需要
- 新功能不得引入新的数据库系统
```

#### 方法 3: 在 `/speckit.plan` 时提供上下文

```bash
/speckit.plan 使用现有技术栈: Node.js 20 + Express + PostgreSQL + Prisma
```

### 问题 5: 如何在多人协作时使用 SpecKit?

**问题描述:**
团队有多个成员，如何避免冲突和重复工作?

**解决方案:**

#### 工作流程

```
产品经理/Tech Lead:
1. /speckit.specify "功能描述"
2. /speckit.clarify (澄清需求)
3. Review spec.md 并获得团队批准
4. /speckit.plan
5. /speckit.tasks
6. 将功能分支推送到远程仓库

开发者 A (负责 P1):
1. git checkout 001-feature-name
2. git pull
3. 在 tasks.md 中标记认领: T006-T010 (@developer-a)
4. /speckit.implement (只执行 T006-T010)
5. Git commit + push

开发者 B (负责 P2):
1. git checkout 001-feature-name
2. git pull
3. 在 tasks.md 中标记认领: T011-T015 (@developer-b)
4. /speckit.implement (只执行 T011-T015)
5. Git commit + push

集成测试:
1. 合并所有实现
2. 运行完整测试套件
3. 创建 Pull Request
```

#### 任务认领标记

编辑 tasks.md:
```markdown
## Phase 3: User Story 1 (@developer-a, ETA: 2025-11-20)
- [ ] T006 [P] [US1] 创建 User 模型 (src/models/user.py) @alice
- [ ] T007 [P] [US1] 创建 Auth 工具类 (src/utils/auth.py) @alice
- [ ] T008 [US1] 实现 AuthService (src/services/auth.py) @alice

## Phase 4: User Story 2 (@developer-b, ETA: 2025-11-22)
- [ ] T009 [P] [US2] 实现 OAuth2 客户端 @bob
- [ ] T010 [US2] OAuth2 回调处理 @bob
```

#### Git 分支策略

```bash
# 主功能分支 (由 /speckit.specify 创建)
001-feature-name

# 子功能分支 (开发者创建)
001-feature-name-us1-auth  (User Story 1)
001-feature-name-us2-oauth (User Story 2)

# 工作流程
git checkout 001-feature-name
git pull
git checkout -b 001-feature-name-us1-auth
# 开发 US1
git push origin 001-feature-name-us1-auth
# 创建 PR: 001-feature-name-us1-auth → 001-feature-name
```

### 问题 6: `/speckit.implement` 生成的代码质量不符合预期

**问题描述:**
自动生成的代码缺少错误处理、日志、或不符合项目代码风格。

**解决方案:**

#### 方法 1: 在 constitution.md 中定义代码标准

```markdown
## Quality Standards

### Code Quality Requirements
- 所有函数必须包含中文 JSDoc 注释
- 必须使用 try-catch 包裹外部调用 (数据库、API)
- 必须使用项目统一的 Logger 工具记录操作日志
- 必须验证所有用户输入 (使用 Joi 或 Zod)
- 禁止硬编码配置，必须从环境变量读取

### Error Handling Standards
- 业务错误: 返回 4xx 状态码 + 用户友好错误消息
- 系统错误: 返回 500 + 通用错误消息，详细信息记录日志
- 错误响应格式: { success: false, error: { code, message, details } }

### Logging Requirements
- 所有 API 请求必须记录: [Task N] Method URL - User ID
- 所有数据库操作必须记录执行时间
- 错误必须记录完整堆栈: logger.error('描述', { error, context })
```

#### 方法 2: 提供代码示例

在 `plan.md` 或 `quickstart.md` 中添加示例:
```markdown
## Code Examples

### 标准 API 端点模板
\`\`\`javascript
/**
 * 用户登录接口
 * @route POST /api/auth/login
 */
async function login(req, res) {
  try {
    // 1. 参数验证
    const { email, password } = validateLoginInput(req.body);

    // 2. 业务逻辑
    const result = await authService.login(email, password);

    // 3. 日志记录
    logger.info(`[User Login] ${email} logged in successfully`);

    // 4. 返回响应
    return res.json({ success: true, data: result });

  } catch (error) {
    logger.error('[User Login] Failed', { error, email: req.body.email });

    if (error instanceof ValidationError) {
      return res.status(400).json({
        success: false,
        error: { code: 'INVALID_INPUT', message: error.message }
      });
    }

    return res.status(500).json({
      success: false,
      error: { code: 'INTERNAL_ERROR', message: '登录失败，请稍后重试' }
    });
  }
}
\`\`\`
```

#### 方法 3: 分阶段实施 + Code Review

```bash
# 第一轮: 生成基础实现
/speckit.implement

# 第二轮: 人工 Code Review
# 检查生成代码，添加缺失的错误处理、日志、注释

# 第三轮: 补充测试
# 根据生成代码编写单元测试和集成测试

# 第四轮: 重构优化
# 提取公共逻辑、优化性能
```

---

## 实战案例

### 案例 1: 从零开始创建 "仪表盘错误处理器" 功能

**背景:**
需要开发一个自动化工具，从 UEM 仪表盘获取 JavaScript 错误，解析 source map，归属责任人，导出 XLSX 报告。

#### 第一步: 创建规格

```bash
/speckit.specify 仪表盘错误处理器
从 UEM 自定义仪表盘自动获取 JavaScript 错误数据，
通过 source map 解析错误堆栈还原到原始代码位置，
使用 git blame 归属到具体责任人，
最终生成包含所有权信息的 XLSX 报告。

核心功能:
1. 使用 Playwright 自动登录 UEM 系统并捕获授权令牌
2. 调用 searchChartData API 获取错误列表
3. 下载并解析 source map 文件将压缩代码位置映射到原始代码
4. 对 node_modules 中的错误，通过 git blame package.json 找到引入者
5. 生成 XLSX 报告，包含: 错误消息、堆栈、责任人、邮箱、提交哈希等
6. 支持命令行参数: boardId、时间范围、是否包含 git blame
```

**生成的 spec.md 结构:**
```markdown
## User Scenarios & Testing

### User Story 1 - 自动获取错误数据 (Priority: P1)
用户运行命令后，系统自动登录 UEM，导航到指定仪表盘，
调用 API 获取错误列表并保存为 JSON。

**Acceptance Scenarios**:
1. **Given** 用户提供有效的 boardId，**When** 运行命令，
   **Then** 成功获取错误数据并保存到 output/ 目录

### User Story 2 - Source Map 解析 (Priority: P1)
系统下载错误堆栈中引用的 source map 文件，
将压缩代码位置 (bundle.js:1:12345) 映射到原始代码 (src/utils/helper.js:42:10)。

### User Story 3 - Git Blame 归属 (Priority: P2)
对于项目源码错误，使用 git blame 找到最后修改该行代码的作者；
对于 node_modules 错误，git blame package.json 找到引入依赖的人。

### User Story 4 - XLSX 报告生成 (Priority: P1)
系统将处理后的错误数据导出为 XLSX 文件，包含 11 列:
错误消息、原始位置、映射位置、文件路径、行号、列号、
责任人、邮箱、提交哈希、提交信息、提交时间。

## Success Criteria
- SC-001: 处理 100 条错误的完整流程在 5 分钟内完成
- SC-002: Source map 映射成功率 > 90%
- SC-003: node_modules 错误的责任人归属成功率 > 95%
- SC-004: XLSX 报告可在 Excel/WPS 中正常打开，所有列宽自动适配
```

#### 第二步: 技术设计

```bash
/speckit.plan
```

**生成的 plan.md 关键内容:**
```markdown
## Technology Stack
- **Runtime**: Node.js 20.19.1 (JavaScript ES6+)
- **Browser Automation**: Playwright 1.56.1
- **HTTP Client**: axios 1.13.1
- **Source Map**: source-map 0.7.6 (Mozilla 官方库)
- **XLSX**: xlsx 0.18.5 (SheetJS)
- **Git**: child_process.execSync 调用 git blame

## Architecture
三层架构 + Service 模式:
- Core Layer: QRLoginCore, UEMNavigator (浏览器自动化)
- Service Layer: UEMApiClient, ErrorExporter, GitBlameService,
                PackageAttributionService (业务逻辑)
- Script Layer: process-errors-from-dashboard.js (CLI 入口)

## Project Structure
src/
├── core/
│   ├── QRLoginCore.js         # 登录编排
│   └── UEMNavigator.js        # UEM 导航 + 令牌捕获
├── services/
│   ├── UEMApiClient.js        # API 调用
│   ├── ErrorExporter.js       # JSON/XLSX 导出
│   ├── GitBlameService.js     # Git blame 归属
│   └── PackageAttributionService.js  # node_modules 归属
└── utils/
    └── logger.js              # 日志工具
```

#### 第三步: 生成任务

```bash
/speckit.tasks
```

**生成的 tasks.md (简化版):**
```markdown
## Phase 1: Setup
- [ ] T001 验证 Node.js 版本 >= 20
- [ ] T002 [P] 安装依赖: npm install
- [ ] T003 [P] 安装 Playwright: npx playwright install chromium

## Phase 2: Foundational
- [ ] T004 创建配置系统 (config/default.json)
- [ ] T005 实现 Logger 工具 (src/utils/logger.js)

## Phase 3: User Story 1 - 自动获取错误数据 (P1)
- [ ] T006 [P] [US1] 实现 QRLoginCore (src/core/QRLoginCore.js)
- [ ] T007 [P] [US1] 实现 UEMNavigator (src/core/UEMNavigator.js)
- [ ] T008 [US1] 实现 UEMApiClient (src/services/UEMApiClient.js)
- [ ] T009 [US1] 创建 CLI 脚本 (process-errors-from-dashboard.js)

## Phase 4: User Story 2 - Source Map 解析 (P1)
- [ ] T010 [US2] 实现 source map 下载逻辑 (在 process script 中)
- [ ] T011 [US2] 实现 source map 解析逻辑 (使用 source-map 库)

## Phase 5: User Story 3 - Git Blame 归属 (P2)
- [ ] T012 [P] [US3] 实现 GitBlameService (src/services/GitBlameService.js)
- [ ] T013 [P] [US3] 实现 PackageAttributionService (src/services/PackageAttributionService.js)
- [ ] T014 [US3] 集成到主流程

## Phase 6: User Story 4 - XLSX 报告生成 (P1)
- [ ] T015 [US4] 实现 ErrorExporter.exportToXLSX (src/services/ErrorExporter.js)
- [ ] T016 [US4] 集成 git blame 信息到 XLSX

## Phase 7: Polish
- [ ] T017 添加命令行参数解析
- [ ] T018 添加错误重试逻辑
- [ ] T019 优化日志输出格式
```

#### 第四步: 执行实施

```bash
/speckit.implement
```

**实施过程:**
1. Claude Code 按顺序执行每个任务
2. 读取 plan.md 理解架构设计
3. 读取 data-model.md 理解数据结构
4. 生成代码并保存到指定路径
5. 自动标记完成的任务 `[x]`

**最终输出:**
```
✅ T001-T019 全部完成
📁 生成文件:
   - src/core/QRLoginCore.js (234 行)
   - src/core/UEMNavigator.js (156 行)
   - src/services/UEMApiClient.js (89 行)
   - src/services/ErrorExporter.js (187 行)
   - src/services/GitBlameService.js (123 行)
   - src/services/PackageAttributionService.js (145 行)
   - process-errors-from-dashboard.js (267 行)

📊 代码统计:
   - 总计: 1201 行代码
   - 注释覆盖率: 87%
   - 中文注释: 100%
```

#### 第五步: 验证和测试

```bash
# 运行功能测试
npm run process-dashboard -- --boardId 16510 --days 7

# 检查输出
ls -lh output/
# dashboard-ownership-report_2025-11-18T10-30-00.xlsx (245 KB)
# dashboard-errors-with-ownership_2025-11-18T10-30-00.json (512 KB)
```

**成果:**
- ✅ 成功处理 247 条错误
- ✅ Source map 映射成功率: 94.3%
- ✅ node_modules 归属成功率: 97.6%
- ✅ 总耗时: 3 分 42 秒
- ✅ 生成的 XLSX 在 Excel 中正常打开，列宽自适应

---

### 案例 2: 优化现有功能 "添加 Author 过滤"

**背景:**
现有 XLSX 报告包含所有错误，需要添加 `--filter-author` 参数，只导出指定责任人的错误。

#### 快速开发流程

```bash
# 1. 创建功能规格
/speckit.specify 为 XLSX 导出添加 --filter-author 参数
用户可以通过 --filter-author "张三" 参数只导出责任人为"张三"的错误行。
支持多个作者: --filter-author "张三,李四"
支持模糊匹配: --filter-author "张" (匹配所有姓张的)
如果没有匹配的行，显示警告但仍生成空 XLSX 文件

# 2. 技术设计 (使用现有技术栈)
/speckit.plan 基于现有 ErrorExporter.js 和 CLI 参数系统

# 3. 生成任务
/speckit.tasks

# 4. 手动实施 (任务简单，不需要 /speckit.implement)
# 编辑 process-errors-from-dashboard.js 添加参数解析
# 编辑 ErrorExporter.js 添加过滤逻辑

# 5. 测试
npm run process-dashboard -- --boardId 16510 --days 7 --filter-author "张三"
```

**生成的 tasks.md:**
```markdown
## Phase 1: Setup
✅ (跳过，使用现有环境)

## Phase 2: Implementation
- [ ] T001 [P] 在 CLI 脚本中添加 --filter-author 参数解析
- [ ] T002 [P] 在 ErrorExporter.exportToXLSX 中添加过滤逻辑
- [ ] T003 添加单元测试 (可选)

## Phase 3: Testing
- [ ] T004 测试单个作者过滤
- [ ] T005 测试多个作者过滤
- [ ] T006 测试模糊匹配
- [ ] T007 测试空结果情况
```

**实施时间:** 约 30 分钟完成

---

## 相关资源

### 官方文档

- **Claude Code 官方文档**: https://code.claude.com/docs/en/
- **Claude Code 文档地图**: https://code.claude.com/docs/en/claude_code_docs_map.md
- **SpecKit 命令参考**: https://code.claude.com/docs/en/slash-commands
- **自定义 Slash 命令**: https://code.claude.com/docs/en/custom-commands

### 配置文件参考

- **CLAUDE.md 指南**: https://code.claude.com/docs/en/claude-md
- **全局配置路径**: `~/.claude/CLAUDE.md`
- **项目配置路径**: `<project-root>/CLAUDE.md`
- **SpecKit 模板路径**: `<project-root>/.specify/templates/`

### 相关技术文档

- **Markdown 规范**: https://commonmark.org/
- **Given-When-Then 格式**: https://martinfowler.com/bliki/GivenWhenThen.html
- **用户故事最佳实践**: https://www.agilealliance.org/glossary/user-stories/
- **OpenAPI 规范**: https://swagger.io/specification/
- **Semantic Versioning**: https://semver.org/

### 社区与支持

- **Claude Code GitHub Issues**: https://github.com/anthropics/claude-code/issues
- **反馈渠道**: 在 Claude Code 中输入 `/help` 查看
- **最佳实践分享**: (待社区建立)

### 本项目示例

- **Constitution 示例**: `.specify/memory/constitution.md` (本项目)
- **完整功能案例**: `specs/001-dashboard-error-processor/` (本项目)
- **任务模板**: `.specify/templates/tasks-template.md` (本项目)

---

## 附录: 快速参考卡

### SpecKit 命令速查

| 命令 | 快捷记忆 | 典型场景 |
|------|---------|---------|
| `/speckit.specify <描述>` | 需求规格化 | 开始新功能，创建分支和 spec.md |
| `/speckit.clarify` | 需求澄清 | spec 中有 [NEEDS CLARIFICATION] |
| `/speckit.plan` | 技术方案 | spec 完成，开始设计 |
| `/speckit.tasks` | 任务分解 | plan 完成，准备开发 |
| `/speckit.implement` | 自动实施 | 执行 tasks.md 中的任务 |
| `/speckit.analyze` | 质量检查 | 验证文档一致性 |
| `/speckit.constitution` | 项目约定 | 初始化项目规范 |
| `/speckit.checklist` | 自定义清单 | 生成特定检查清单 |

### 文档层级关系

```
Constitution (项目约定)
    ↓ 约束
Spec.md (WHAT - 需求)
    ↓ 指导
Plan.md (HOW - 设计)
    ↓ 指导
Tasks.md (STEPS - 任务)
    ↓ 执行
Code (实现)
```

### 配置优先级

```
CLI 参数
    ↓ 覆盖
Constitution.md
    ↓ 覆盖
项目 CLAUDE.md
    ↓ 覆盖
全局 ~/.claude/CLAUDE.md
    ↓ 覆盖
SpecKit 默认行为
```

### 质量检查清单

**Spec.md 完成标准:**
- [ ] 无 [NEEDS CLARIFICATION] 标记
- [ ] 所有用户故事有优先级 (P1/P2/P3)
- [ ] 每个故事有独立测试标准
- [ ] 成功标准可度量且技术无关
- [ ] 无技术实现细节泄漏

**Plan.md 完成标准:**
- [ ] 技术栈明确且符合 constitution
- [ ] 项目结构清晰
- [ ] API 契约已定义 (contracts/)
- [ ] 数据模型已设计 (data-model.md)
- [ ] 通过宪法合规检查

**Tasks.md 完成标准:**
- [ ] 所有任务遵循 checklist 格式
- [ ] 任务按用户故事组织
- [ ] 依赖关系明确
- [ ] 并行任务已标记 [P]
- [ ] 每个任务有具体文件路径

---

**演示文稿版本**: v1.0.0
**创建日期**: 2025-11-18
**适用 SpecKit 版本**: Claude Code 最新版
**维护者**: 根据实际项目经验持续更新

---

## 结语

SpecKit 不仅是一套工具，更是一种**结构化思维方式**:

1. **先想清楚要什么** (Spec) - 避免后期返工
2. **再设计怎么做** (Plan) - 确保技术可行
3. **最后拆解执行** (Tasks) - 降低实施风险

**核心理念**:

> "Good software starts with good requirements."
> "优秀的软件始于优秀的需求。"

通过 SpecKit，让 Claude Code 不仅是代码生成器，更是你的**软件工程助手**。

---

**需要帮助?**
- 输入 `/help` 查看 Claude Code 帮助
- 访问 https://github.com/anthropics/claude-code/issues 报告问题
- 查看本文档 [常见问题解决方案](#常见问题解决方案) 章节
