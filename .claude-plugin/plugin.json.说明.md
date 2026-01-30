# plugin.json 配置说明（插件配置文件说明）

## 原始配置字段说明

```json
{
  // 插件名称：唯一标识符
  "name": "everything-claude-code",

  // 版本号：遵循语义化版本规范
  "version": "1.2.0",

  // 插件描述：简要说明插件的功能和用途
  "description": "Complete collection of battle-tested Claude Code configs...",

  // 作者信息
  "author": {
    "name": "Affaan Mustafa",      // 作者姓名
    "url": "https://x.com/affaanmustafa"  // 作者主页
  },

  // 项目主页
  "homepage": "https://github.com/affaan-m/everything-claude-code",

  // 代码仓库
  "repository": "https://github.com/affaan-m/everything-claude-code",

  // 开源许可证
  "license": "MIT",

  // 关键词：用于插件搜索和分类
  "keywords": [
    "claude-code",    // Claude Code 相关
    "agents",        // 代理/子代理
    "skills",        // 技能
    "hooks",         // 钩子
    "rules",         // 规则
    "tdd",           // 测试驱动开发
    "code-review",   // 代码审查
    "security",      // 安全
    "workflow",      // 工作流
    "automation",    // 自动化
    "best-practices" // 最佳实践
  ],

  // 技能目录：包含所有技能文件
  "skills": ["./skills/", "./commands/"],

  // 代理列表：所有可用的专业子代理
  "agents": [
    "./agents/architect.md",           // 架构设计专家
    "./agents/build-error-resolver.md", // 构建错误解决专家
    "./agents/code-reviewer.md",       // 代码审查专家
    "./agents/database-reviewer.md",   // 数据库审查专家
    "./agents/doc-updater.md",         // 文档更新专家
    "./agents/e2e-runner.md",          // E2E 测试运行专家
    "./agents/go-build-resolver.md",   // Go 构建错误解决专家
    "./agents/go-reviewer.md",         // Go 代码审查专家
    "./agents/planner.md",             // 功能规划专家
    "./agents/refactor-cleaner.md",    // 重构清理专家
    "./agents/security-reviewer.md",   // 安全审查专家
    "./agents/tdd-guide.md"            // TDD 指导专家
  ]
}
```

## 中文版配置说明

这是一个 **Claude Code 插件**的配置文件，用于打包和分发完整的 Claude Code 配置集合。

### 核心组件

1. **Skills（技能）**
   - 存放位置：`./skills/` 和 `./commands/`
   - 功能：定义可重用的工作流程和最佳实践

2. **Agents（代理/子代理）**
   - 功能：专业化领域的 AI 代理
   - 包括：架构、代码审查、安全、测试、规划等专家

3. **使用方式**
   - 安装：通过 Claude Code 插件市场
   - 自动加载：所有 skills 和 agents 会自动加载
   - 按需调用：Claude 根据任务自动选择合适的组件

## 中文版说明

此配置文件已从英文原版完整复制，保留了所有原始配置。中文翻译项目提供：
- 完整的中文文档
- 配置文件的中文注释说明
- 原创的中文使用指南
