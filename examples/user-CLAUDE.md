# 用户级 CLAUDE.md 示例

这是一个用户级 CLAUDE.md 文件示例。放在 `~/.claude/CLAUDE.md`。

用户级配置在所有项目中全局应用。用于：
- 个人编码偏好
- 始终希望执行的通用规则
- 指向模块化规则的链接

---

## 核心理念

你是 Claude Code。我使用专门的 agents 和 skills 来处理复杂任务。

**关键原则：**
1. **Agent 优先**：将复杂工作委托给专门的 agents
2. **并行执行**：尽可能使用 Task 工具与多个 agents 并行
3. **先计划后执行**：对复杂操作使用 Plan Mode
4. **测试驱动**：实现前先编写测试
5. **安全优先**：永不妥协安全性

---

## 模块化规则

详细指南位于 `~/.claude/rules/`：

| 规则文件 | 内容 |
|-----------|----------|
| security.md | 安全检查、密钥管理 |
| coding-style.md | 不可变性、文件组织、错误处理 |
| testing.md | TDD 工作流、80% 覆盖率要求 |
| git-workflow.md | 提交格式、PR 工作流 |
| agents.md | Agent 编排、何时使用哪个 agent |
| patterns.md | API 响应、仓储模式 |
| performance.md | 模型选择、上下文管理 |
| hooks.md | Hooks 系统 |

---

## 可用 Agents

位于 `~/.claude/agents/`：

| Agent | 用途 |
|-------|---------|
| planner | 功能实现规划 |
| architect | 系统设计和架构 |
| tdd-guide | 测试驱动开发 |
| code-reviewer | 代码质量/安全审查 |
| security-reviewer | 安全漏洞分析 |
| build-error-resolver | 构建错误解决 |
| e2e-runner | Playwright E2E 测试 |
| refactor-cleaner | 死代码清理 |
| doc-updater | 文档更新 |

---

## 个人偏好

### 隐私
- 始终编辑日志；永不粘贴密钥（API keys/tokens/passwords/JWTs）
- 分享前审查输出 - 删除任何敏感数据

### 代码风格
- 代码、注释或文档中不使用表情符号
- 优先使用不可变性 - 永不修改对象或数组
- 多个小文件优于少量大文件
- 典型 200-400 行，每文件最多 800 行

### Git
- 约定式提交：`feat:`、`fix:`、`refactor:`、`docs:`、`test:`
- 提交前始终在本地测试
- 小而专注的提交

### 测试
- TDD：先编写测试
- 最低 80% 覆盖率
- 关键流程使用单元 + 集成 + E2E 测试

---

## 编辑器集成

我使用 Zed 作为主编辑器：
- Agent Panel 用于文件跟踪
- CMD+Shift+R 打开命令面板
- 启用 Vim 模式

---

## 成功指标

当以下情况时，你是成功的：
- 所有测试通过（80%+ 覆盖率）
- 无安全漏洞
- 代码可读且可维护
- 满足用户需求

---

**理念**：Agent 优先设计、并行执行、行动前计划、代码前测试、始终安全。
