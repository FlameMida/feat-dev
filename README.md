# Feature Dev CN

完整的功能开发工作流插件 - 7阶段系统化开发流程，包含代码探索、架构设计、实施和质量审查。

**中文版** | 融合 ultrathink 深度分析 | MCP 工具增强 | 语言无关

## 特性

- **7 阶段工作流**: 需求理解 → 代码探索 → 澄清问题 → 架构设计 → 实施 → 质量审查 → 总结
- **3 个专门化 Agent**: code-explorer、code-architect、code-reviewer
- **ultrathink 深度分析**: 在关键阶段使用 Sequential Thinking 进行深度思考
- **MCP 工具增强**: 集成 context7、exa、sequential-thinking 等 MCP 服务
- **并行 Agent 执行**: 代码探索和质量审查阶段并行执行多个 agent
- **语言无关**: 适用于任何编程语言和项目结构
- **中文输出**: 全程中文沟通

## MCP 工具集成

本插件集成以下 MCP 服务器以增强开发流程：

| MCP 服务器 | 用途 | 使用阶段 |
|-----------|------|---------|
| **context7** | 获取最新库文档和 API 参考 | 代码探索、架构设计、实施 |
| **exa** | 网页搜索最佳实践和代码示例 | 需求理解、架构设计、质量审查 |
| **sequential-thinking** | 深度结构化思考（ultrathink） | 需求理解、架构设计 |

### MCP 配置

插件包含 `.mcp.json` 配置文件，定义了推荐的 MCP 服务器配置：

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp@latest"],
      "env": {
        "CONTEXT7_API_KEY": "${CONTEXT7_API_KEY}"
      }
    },
    "exa": {
      "command": "npx",
      "args": ["-y", "exa-mcp-server"],
      "env": {
        "EXA_API_KEY": "${EXA_API_KEY}"
      }
    },
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"]
    }
  }
}
```

### 环境变量配置

使用 MCP 服务需要设置相应的 API Key：

```bash
# 在 ~/.zshrc 或 ~/.bashrc 中添加
export CONTEXT7_API_KEY="your-context7-api-key"
export EXA_API_KEY="your-exa-api-key"
```

获取 API Key:
- Context7: https://context7.com/
- Exa: https://exa.ai/

### MCP 依赖管理

#### 如果您已安装相同的 MCP

**重要提示**：如果您已经在全局配置（`~/.claude/config.json`）中安装了本插件使用的 MCP（如 context7、exa），**无需担心冲突**。

Claude Code 使用**作用域优先级机制**自动处理：
- ✅ 您的全局配置**优先级最高**
- ✅ 插件的 MCP 配置会被自动跳过
- ✅ 不会重复启动相同的 MCP 服务
- ✅ 插件功能完全正常

#### 配置方式

**方式 1：使用插件提供的配置（推荐）**

插件的 `.mcp.json` 已包含完整配置，安装插件后自动生效。无需任何手动配置。

**方式 2：使用已有的全局配置**

如果您已在 `~/.claude/config.json` 中配置了这些 MCP：
- ✅ **无需重复配置**
- ✅ Claude Code 会自动使用您的全局配置（优先级更高）
- ✅ 插件功能完全正常

**方式 3：混合使用**

您可以灵活搭配：
- 在全局配置中使用自定义的 context7 配置
- 在插件中使用 exa 和 sequential-thinking
- Claude Code 会智能选择正确的 MCP

#### 检查 MCP 配置状态

运行以下命令检查当前 MCP 配置状态：
```bash
/check-mcp
```

该命令会显示：
- 哪些 MCP 已在全局配置
- 哪些 MCP 来自插件
- 实际正在使用的配置
- 配置建议和优化提示

#### 常见问题

**Q: 我已经安装了 context7，还需要额外配置吗？**
A: 不需要。您的全局配置会自动优先于插件配置，不会产生冲突。

**Q: 如何知道正在使用哪个 MCP 配置？**
A: 运行 `/check-mcp` 命令查看详细状态，包括配置来源和优先级信息。

**Q: 可以禁用插件的某个 MCP 吗？**
A: 通常不必要。Claude Code 的作用域机制会自动处理优先级。如果您的全局配置中已有该 MCP，它会自动被使用。

**Q: 会话开始时看到 MCP 检查提示是什么？**
A: 插件会在会话开始时自动检测 MCP 配置状态，并提供友好提示。这有助于确保最佳体验。

## 安装

### 方式 1: 从 GitHub 安装

```bash
# 添加为 marketplace
/plugin marketplace add https://github.com/FlameMida/feat-dev

# 安装插件
/plugin install feat-dev@GodV-plugins
```

### 方式 2: 本地安装

将此目录放置在 `~/.claude/plugins/repos/feat-dev/`

## 使用方法

### 使用 /feat-dev 命令

```bash
/feat-dev 实现用户认证功能
```

或者只输入命令，按提示描述功能：

```bash
/feat-dev
```

### 自动触发

当你提出以下类型的需求时，会自动使用此工作流：
- "我需要实现一个 XXX 功能"
- "帮我开发 XXX 模块"
- "需要添加 XXX 特性"

## 专门化 Agents

| Agent | 颜色 | 模型 | 用途 |
|-------|------|------|------|
| **code-explorer** | 黄色 | Sonnet | 深度分析代码库，追踪执行路径 |
| **code-architect** | 绿色 | Opus | 设计架构蓝图，制定实施方案 |
| **code-reviewer** | 红色 | Sonnet | 代码审查，识别 bug 和规范问题 |

## 7 阶段工作流

```
阶段 1: 需求理解 (Discovery)
    ↓ [可选 ultrathink]
阶段 2: 代码库探索 (Codebase Exploration)
    ↓ [并行 2-3 个 code-explorer]
阶段 3: 澄清问题 (Clarifying Questions)
    ↓ [AskUserQuestion]
阶段 4: 架构设计 (Architecture Design)
    ↓ [必须 ultrathink + code-architect]
阶段 5: 实施 (Implementation)
    ↓ [等待用户确认]
阶段 6: 质量审查 (Quality Review)
    ↓ [并行 3 个 code-reviewer]
阶段 7: 总结 (Summary)
```

## 目录结构

```
feat-dev/
├── .claude-plugin/
│   ├── plugin.json         # 插件元数据（含 SessionStart hook）
│   └── marketplace.json    # Marketplace 配置
├── .mcp.json               # MCP 服务器配置
├── agents/
│   ├── code-explorer.md    # 代码探索 agent
│   ├── code-architect.md   # 架构设计 agent
│   └── code-reviewer.md    # 代码审查 agent
├── commands/
│   └── check-mcp.md        # /check-mcp 命令 - 检查 MCP 配置状态
├── scripts/
│   └── check-mcp-setup.sh  # SessionStart hook - 会话启动检查
├── skills/
│   └── skill.md            # Skill 定义（自动触发）
├── CHANGELOG.md            # 版本更新日志
└── README.md               # 项目说明文档
```

## 与官方 feat-dev 的区别

| 特性 | 官方 feat-dev | feat-dev |
|------|-----------------|----------------|
| 语言 | 英文 | 中文 |
| ultrathink | 无 | 融合 Sequential Thinking |
| MCP 工具 | 无 | 集成 context7、exa |
| 模型配置 | 固定 Sonnet | 可配置（Sonnet/Opus） |
| 自动触发 | 无 | 支持 |

## 更新日志

查看 [CHANGELOG.md](./CHANGELOG.md) 了解详细的版本更新历史。

**最新版本**：v1.1.0 (2025-12-17)
- ✨ 添加 SessionStart hook 自动检查 MCP 配置
- ✨ 新增 `/check-mcp` 命令查看配置详情
- 🔧 智能 MCP 冲突检测和处理机制

## 许可证

MIT License

## 作者

FlameMida

## 贡献

欢迎提交 Issue 和 Pull Request！
