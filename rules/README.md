# Rules 快速参考

本目录包含 Everything Claude Code 的所有核心规则文件。这些规则定义了在使用 Claude Code 时应该始终遵循的最佳实践。

## 📋 规则文件列表

### 1. [agents.md](agents.md) - Agent 编排
**何时委托给专业 Agent**
- 可用 Agent 列表（planner、architect、tdd-guide 等）
- 立即使用 Agent 的场景
- 并行任务执行策略
- 多视角分析方法

**关键要点：** 复杂任务立即使用对应的 Agent，独立操作必须并行执行

### 2. [coding-style.md](coding-style.md) - 代码风格
**编写高质量代码的标准**
- 不可变性原则（CRITICAL）
- 文件组织策略
- 错误处理规范
- 输入验证要求
- 代码质量检查清单

**关键要点：** 绝不修改原有对象，保持文件短小专注（<800 行）

### 3. [git-workflow.md](git-workflow.md) - Git 工作流
**版本控制最佳实践**
- 提交消息格式（Conventional Commits）
- Pull Request 工作流
- 功能实现流程（规划 → TDD → 审查 → 提交）

**关键要点：** 遵循 feat/fix/refactor 格式，PR 包含完整测试计划

### 4. [hooks.md](hooks.md) - Hooks 系统
**自动化工作流配置**
- PreToolUse/PostToolUse/Stop Hook 类型
- 当前活跃的 Hooks
- 自动接受权限配置
- TodoWrite 最佳实践

**关键要点：** Hooks 提供自动化，谨慎使用自动接受权限

### 5. [patterns.md](patterns.md) - 常用模式
**标准代码模式**
- API 响应格式
- 自定义 Hooks 模式
- 仓储模式
- 骨架项目策略

**关键要点：** 使用经过验证的模式，搜索骨架项目作为起点

### 6. [performance.md](performance.md) - 性能优化
**模型选择和资源管理**
- 模型选择策略（Haiku 4.5 vs Sonnet 4.5 vs Opus 4.5）
- 上下文窗口管理
- Ultrathink + 计划模式
- 构建故障排除

**关键要点：** 根据任务复杂度选择合适的模型，避免上下文窗口最后 20%

### 7. [security.md](security.md) - 安全指南
**强制安全检查**
- 提交前安全检查清单
- 密钥管理规范
- 安全响应协议

**关键要点：** 绝不硬编码密钥，所有输入必须验证，错误消息不泄露敏感信息

### 8. [testing.md](testing.md) - 测试要求
**测试驱动开发标准**
- 最低 80% 测试覆盖率
- 测试类型（单元、集成、E2E）
- TDD 工作流（RED → GREEN → IMPROVE）
- 测试故障排除

**关键要点：** 先写测试，测试驱动开发，达到 80%+ 覆盖率

## 🎯 使用建议

### 新手入门顺序

1. **第一步：** 阅读 [coding-style.md](coding-style.md) - 理解代码质量标准
2. **第二步：** 阅读 [testing.md](testing.md) - 掌握 TDD 方法
3. **第三步：** 阅读 [git-workflow.md](git-workflow.md) - 学习 Git 最佳实践
4. **第四步：** 阅读 [security.md](security.md) - 了解安全要求

### 进阶使用顺序

5. **第五步：** 阅读 [agents.md](agents.md) - 学会使用专业 Agent
6. **第六步：** 阅读 [performance.md](performance.md) - 优化模型选择
7. **第七步：** 阅读 [hooks.md](hooks.md) - 配置自动化工作流
8. **第八步：** 阅读 [patterns.md](patterns.md) - 掌握常用模式

### 日常参考

- **编写代码前：** coding-style.md、testing.md
- **提交前：** security.md、git-workflow.md
- **复杂任务：** agents.md、performance.md
- **配置优化：** hooks.md、patterns.md

## 📊 规则优先级

### 🔴 关键（CRITICAL）
- **不可变性**（coding-style.md）- 绝不修改原有对象
- **80% 测试覆盖率**（testing.md）- 强制要求
- **安全检查**（security.md）- 提交前必须通过

### 🟡 重要（HIGH）
- **TDD 工作流**（testing.md）- 先写测试
- **代码质量**（coding-style.md）- 保持高内聚低耦合
- **并行执行**（agents.md）- 独立操作必须并行

### 🟢 推荐（MEDIUM）
- **模型选择**（performance.md）- 根据任务复杂度
- **Hooks 配置**（hooks.md）- 自动化工作流
- **模式使用**（patterns.md）- 遵循最佳实践

## 🔗 相关文档

- **贡献指南：** [CONTRIBUTING.md](../CONTRIBUTING.md)
- **主文档：** [README.zh-CN.md](../../README.zh-CN.md)
- **精简指南：** [the-shortform-guide.md](../the-shortform-guide.md)
- **详细指南：** [the-longform-guide.md](../the-longform-guide.md)

## 💡 快速提示

### 遇到问题时？

- **构建失败？** → performance.md - 构建故障排除
- **测试失败？** → testing.md - 测试故障排除
- **安全顾虑？** → security.md - 安全响应协议
- **性能问题？** → performance.md - 模型选择策略
- **代码审查？** → agents.md - 使用 code-reviewer agent

### 常见陷阱

❌ **不要：**
- 修改函数参数或对象属性
- 硬编码密钥和密码
- 写代码后再写测试
- 顺序执行独立任务
- 忽略安全检查清单

✅ **应该：**
- 创建新对象（展开运算符）
- 使用环境变量
- 先写测试（TDD）
- 并行执行独立任务
- 每次提交前检查安全

---

**维护者：** Everything Claude Code 中文翻译团队
**最后更新：** 2025-01-30
**版本：** 1.0.0
