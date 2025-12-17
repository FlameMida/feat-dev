---
name: feat-dev
description: 【自动触发】完整的功能开发工作流 - 7阶段系统化开发流程，包含代码探索、架构设计、实施和质量审查。融合 ultrathink 深度分析、MCP 工具增强和并行 agent 执行（全程中文）
---

# 功能开发技能 (Feature Development Skill)

本技能提供完整的 7 阶段功能开发工作流，融合了深度分析（ultrathink）、MCP 工具增强和专门化 agents（code-explorer、code-architect、code-reviewer）的并行执行能力。

## MCP 工具集成（可选）

本技能优先使用 MCP 工具增强开发流程，但在 MCP 不可用时会**自动降级**使用传统工具。

### 🎯 重要原则

**所有功能在无 MCP 环境下完全可用**。Skill 会自动检测 MCP 可用性并智能降级。

### MCP 工具与降级策略

| MCP 工具 | 用途 | 降级方案 | 使用阶段 |
|---------|------|---------|---------|
| **context7** | 获取最新库文档和 API 参考 | WebSearch + 项目依赖分析 | 阶段 2、4、5 |
| **exa** | 高质量网页搜索和代码示例 | WebSearch | 阶段 1、4、6 |
| **sequential-thinking** | 深度结构化思考（ultrathink） | EnterPlanMode + 思维链分析 | 阶段 1、4 |

### 工具调用策略

**优先尝试 MCP 工具**：
- 如果 MCP 工具可用，优先使用以获得最佳体验
- 如果 MCP 工具调用失败或不可用，立即切换到降级方案
- **不要因为 MCP 不可用而中断工作流**

**降级时的替代逻辑**：
```
尝试 MCP 工具
  ↓
成功 → 继续使用 MCP
  ↓
失败 → 切换到降级方案
  ↓
继续工作流（功能完整）
```

## 何时使用 (When to Use)

在以下情况下使用本技能：
- 开始新功能实施
- 复杂的代码修改或重构
- 需要设计 API、数据库架构或服务架构
- 涉及多个模块的功能开发
- 需要代码审查的实施任务

## 如何触发 (How to Trigger)

### 方式 1: 自动触发
当你提出以下类型的需求时，Claude 应该会自动使用此技能：
- "我需要实现一个 XXX 功能"
- "帮我开发 XXX 模块"
- "需要添加 XXX 特性"
- "开发一个完整的 XXX 系统"

### 方式 2: 手动触发
```bash
/feat-dev [功能描述]
```

## 专门化 Agents

本技能定义了三个专门化 agents，存放在 `agents/` 目录下：

| Agent | 文件 | 用途 | 阶段 |
|-------|------|------|------|
| **code-explorer** | `agents/code-explorer.md` | 深度分析代码库，追踪执行路径 | 阶段 2 |
| **code-architect** | `agents/code-architect.md` | 设计架构蓝图，制定实施方案 | 阶段 4 |
| **code-reviewer** | `agents/code-reviewer.md` | 代码审查，识别 bug 和规范问题 | 阶段 6 |

**注意**：每个 agent 文件的 YAML frontmatter 中已定义推荐的 `model`，调用时请使用该配置。

### 如何调用 Agents

使用 **Task 工具** 调用这些 agents，model 参数从 agent 文件的 YAML frontmatter 中读取：

```
Task tool:
- subagent_type: general-purpose
- model: [从 agent 文件 YAML 中的 model 字段读取]
- prompt: [读取对应 agent markdown 文件的内容作为系统提示] + [具体任务描述]
- run_in_background: true (并行执行时)
```

---

## 7 阶段工作流 (7-Phase Workflow)

### 阶段 1: 需求理解 (Discovery)

**目标**: 全面理解用户需求

**根据需求复杂度决定是否使用 ultrathink**：

仔细阅读用户的需求并识别：
- **核心功能**: 请求的主要特性/能力是什么？
- **业务实体**: 涉及哪些数据模型或领域对象？
- **API 端点**: 需要暴露哪些接口？
- **业务规则**: 适用哪些约束、验证或特殊逻辑？
- **集成点**: 需要与哪些现有系统集成？

**MCP 工具使用**：
- 🔍 **优先尝试 exa.web_search_exa**: 搜索类似产品/功能的实现案例，了解行业最佳实践
  - **降级方案**：使用 WebSearch 搜索相关案例和最佳实践
- 🧠 **复杂需求时尝试 sequential-thinking**: ultrathink 进行深度分析
  - **降级方案**：在响应中使用详细的思维链分析（Chain of Thought）

**如果 MCP 工具不可用**，不要中断工作流，立即使用降级方案继续。

**何时使用 ultrathink**：
- ✅ 需求涉及多个模块或系统的集成
- ✅ 需求包含复杂的业务逻辑或工作流
- ✅ 需求描述模糊或不完整
- ✅ 需求之间存在依赖关系或冲突

### 阶段 2: 代码库探索 (Codebase Exploration)

**目标**: 深入理解现有代码库

**首要任务**: 查找并阅读项目根目录下的 **CLAUDE.md** 文件。

**MCP 工具使用**：
- 📚 **优先尝试 context7.get-library-docs**: 获取项目依赖库的最新文档
  - **降级方案**：
    1. 使用 WebSearch 搜索官方文档
    2. 使用 Grep 搜索项目依赖文件（package.json、go.mod、requirements.txt 等）
    3. 使用 Read 阅读已安装的库文件或 README
- 🔍 **优先尝试 exa.get_code_context_exa**: 搜索特定框架/库的代码示例
  - **降级方案**：使用 WebSearch 搜索代码示例和使用教程

**降级方案示例**：
```
# context7 不可用时
1. WebSearch: "Gin framework middleware documentation 2025"
2. Grep: "go.mod" 查找依赖版本
3. Read: node_modules/library/README.md 或 vendor/library/

# exa 不可用时
WebSearch: "Next.js app router authentication example code"
```

**如果 MCP 工具不可用**，不要中断工作流，立即使用降级方案继续。

**并行启动 2-3 个 code-explorer agents**（model 参考 agent 文件配置）：

```
并行执行 Task 工具:

Agent 1 - 探索相关实体和数据模型:
- subagent_type: general-purpose
- prompt: "[code-explorer.md 内容]\n\n任务：探索与 [功能] 相关的实体定义和数据模型"
- run_in_background: true

Agent 2 - 探索服务层和业务逻辑:
- subagent_type: general-purpose
- prompt: "[code-explorer.md 内容]\n\n任务：探索与 [功能] 相关的服务层实现和业务逻辑"
- run_in_background: true

Agent 3 - 探索 API 层和控制器:
- subagent_type: general-purpose
- prompt: "[code-explorer.md 内容]\n\n任务：探索与 [功能] 相关的 API 端点和控制器"
- run_in_background: true
```

**汇总探索结果**：
- 收集所有 agent 的报告
- 整合发现的模式和组件
- 列出必读文件清单

### 阶段 3: 澄清问题 (Clarifying Questions)

**目标**: 填补需求空白

**重要**: 对任何不清楚、模糊、有歧义的地方，必须主动使用 **AskUserQuestion 工具** 向用户提问。

常见澄清点：
- 模糊或规格不足的需求
- 多个有效实施方法之间的选择
- 业务规则细节或边缘情况处理
- 与现有功能的关系
- 技术选型或架构决策

**示例**:
```
AskUserQuestion:
- questions: [
    {
      question: "用户认证应该使用哪种方式？",
      header: "认证方式",
      options: [
        {label: "JWT Token", description: "无状态，适合分布式"},
        {label: "Session", description: "传统方式，需要存储"}
      ],
      multiSelect: false
    }
  ]
```

### 阶段 4: 架构设计 (Architecture Design)

**目标**: 设计实施方案

**MCP 工具使用**：
- 🧠 **必须尝试 sequential-thinking**: ultrathink 进行深度架构分析
  - **降级方案**：
    1. 使用 EnterPlanMode 进入规划模式
    2. 在响应中使用详细的思维链分析
    3. 系统性地分解问题并逐步设计
- 🔍 **优先尝试 exa.web_search_exa**: 搜索架构模式和设计最佳实践
  - **降级方案**：使用 WebSearch 搜索架构模式和最佳实践
- 📚 **优先尝试 context7.get-library-docs**: 获取框架的架构指南
  - **降级方案**：使用 WebSearch 搜索框架官方架构文档

**降级方案示例**：
```
# sequential-thinking 不可用时
使用 EnterPlanMode 或在响应中详细分析：
1. 分析需求组件
2. 设计数据结构（遵循 CLAUDE.md 规范）
3. 设计 API 端点（遵循 CLAUDE.md 规范）
4. 设计服务层架构
5. 识别风险和边缘情况
6. 规划实施步骤

# exa 不可用时
WebSearch: "microservices authentication architecture patterns 2025"

# context7 不可用时
WebSearch: "Gin framework architecture best practices official documentation"
```

**如果 MCP 工具不可用**，不要中断工作流，立即使用降级方案继续。

**必须使用 ultrathink** 进行深度架构分析：

使用 **mcp__sequential-thinking__sequentialthinking 工具**：
1. 分析需求组件
2. 设计数据结构（遵循 CLAUDE.md 规范）
3. 设计 API 端点（遵循 CLAUDE.md 规范）
4. 设计服务层架构
5. 识别风险和边缘情况
6. 规划实施步骤

**可选：启动 code-architect agent** 获取详细蓝图（model 参考 agent 文件配置）：

```
Task tool:
- subagent_type: general-purpose
- prompt: "[code-architect.md 内容]\n\n任务：为 [功能] 设计详细的架构蓝图"
```

**展示架构方案**：
- 数据库设计（Entity 定义）
- API 端点设计
- 服务层设计
- 实施步骤

**等待用户确认**: "这个架构方案看起来如何？我可以开始实施了吗？"

### 阶段 5: 实施 (Implementation)

**目标**: 按计划实施功能

**使用模型**: Sonnet（编码实现的最佳性价比选择）

**前置条件**: 用户已确认架构方案

**MCP 工具使用**：
- 📚 **优先尝试 context7.get-library-docs**: 实时查询 API 文档，确保使用最新语法
  - **降级方案**：
    1. 使用 WebSearch 搜索官方 API 文档
    2. 使用 Grep 搜索项目中已有的类似实现
    3. 使用 Read 阅读库的 README 或示例代码
- 🔍 **优先尝试 exa.get_code_context_exa**: 搜索特定功能的实现示例
  - **降级方案**：使用 WebSearch 搜索实现示例和教程

**降级方案示例**：
```
# context7 不可用时
1. WebSearch: "GORM associations documentation latest version"
2. Grep: "HasMany\|BelongsTo" 查找项目中的关联示例
3. Read: vendor/gorm.io/gorm/README.md

# exa 不可用时
WebSearch: "GORM many-to-many relationship example Go code"
```

**如果 MCP 工具不可用**，不要中断工作流，立即使用降级方案继续。

**实施顺序**（按阶段 4 的计划）：
1. 创建实体和数据库迁移
2. 实施数据访问层
3. 实施服务层业务逻辑
4. 创建请求/响应结构
5. 实施控制器
6. 注册路由
7. 添加验证和错误处理

**实施原则**：
- 严格遵循 CLAUDE.md 规范
- 遵循代码库现有模式
- 每完成一个模块及时测试
- 保持代码简洁，避免过度设计
- 使用 Sonnet 模型进行代码生成（HumanEval 93.7%）

### 阶段 6: 质量审查 (Quality Review)

**目标**: 确保代码质量

**MCP 工具使用**：
- 🔍 **优先尝试 exa.web_search_exa**: 搜索已知安全漏洞和常见 bug 模式
  - **降级方案**：使用 WebSearch 搜索安全漏洞和 bug 模式

**降级方案示例**：
```
# exa 不可用时
WebSearch: "SQL injection prevention Go GORM 2025 security best practices"
WebSearch: "JWT security vulnerabilities common mistakes 2025"
```

**如果 MCP 工具不可用**，不要中断工作流，立即使用降级方案继续。

**并行启动 3 个 code-reviewer agents**（model 参考 agent 文件配置）：

```
并行执行 Task 工具:

Reviewer 1 - Bug 和逻辑错误:
- subagent_type: general-purpose
- prompt: "[code-reviewer.md 内容]\n\n审查焦点：Bug 和逻辑错误\n审查范围：[本次实施的文件]"
- run_in_background: true

Reviewer 2 - 代码风格和质量:
- subagent_type: general-purpose
- prompt: "[code-reviewer.md 内容]\n\n审查焦点：代码风格和质量\n审查范围：[本次实施的文件]"
- run_in_background: true

Reviewer 3 - 项目规范遵循:
- subagent_type: general-purpose
- prompt: "[code-reviewer.md 内容]\n\n审查焦点：项目规范遵循\n审查范围：[本次实施的文件]"
- run_in_background: true
```

**处理审查结果**：
- 汇总所有 reviewer 的发现
- 修复置信度 ≥80 的问题
- 记录暂不处理的问题及原因

### 阶段 7: 总结 (Summary)

**目标**: 总结和文档化

输出内容：
1. **变更摘要**: 实现了什么功能
2. **修改文件列表**: 所有新增和修改的文件
3. **API 变更**: 新增的 API 端点
4. **数据库变更**: 新增的表或字段
5. **后续步骤**: 建议的测试计划、部署注意事项、生成相应的CHANGELOG、生成相应的OpenAPI文档

---

## 输出格式模板

```markdown
## 🎯 阶段 1: 需求理解
- [需求要点列表]

## 🔍 阶段 2: 代码库探索结果
- **CLAUDE.md 规范**: [关键规范要点]
- **相关组件**: [发现的相关代码]
- **使用的模式**: [识别的设计模式]

## ❓ 阶段 3: 澄清问题
[澄清结果或"无需澄清"]

## 🏗️ 阶段 4: 架构设计

### 数据库设计
[Entity 定义]

### API 端点
[端点列表]

### 服务层设计
[服务方法和逻辑流程]

### 实施步骤
[编号步骤列表]

## ✅ 准备好实施了吗？
这个方案看起来如何？我可以开始实施了吗？

---（用户确认后继续）---

## 🔨 阶段 5: 实施
[实施过程和代码]

## 🔍 阶段 6: 质量审查
### Bug 审查
[Reviewer 1 结果]

### 代码质量
[Reviewer 2 结果]

### 规范遵循
[Reviewer 3 结果]

### 修复记录
[已修复的问题]

## 📋 阶段 7: 总结

### 变更摘要
- [实现的功能]

### 修改文件
- `path/to/file1.go` - [变更描述]
- `path/to/file2.go` - [变更描述]

### 新增 API
| 方法 | 路径 | 描述 |
|------|------|------|

### 后续建议
- [测试建议]
- [部署注意事项]
- [生成CHANGELOG]
- [生成OpenAPI文档]
```

---

## 重要原则

1. **全程使用中文**: 与用户的所有沟通必须使用中文
2. **严格遵循 CLAUDE.md**: 必须阅读并遵守项目规范
3. **主动提问**: 不清楚的地方必须澄清
4. **善用 ultrathink**: 复杂分析必须使用 Sequential Thinking
5. **并行执行**: 探索和审查阶段并行启动多个 agents
6. **等待确认**: 架构设计确认后才开始实施
7. **质量把关**: 实施后必须进行代码审查

