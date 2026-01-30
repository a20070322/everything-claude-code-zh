# hooks.json 配置说明（Hooks 自动化配置说明）

## 概述

此配置文件定义了 **Claude Code Hooks 自动化系统**，在各种事件触发时自动执行特定命令。

## Hook 类型说明

### 1. PreToolUse（工具使用前）

在执行工具前触发，用于验证、阻止或提醒操作。

#### Hook 1: 阻止在 tmux 外运行开发服务器
```json
{
  "matcher": "tool == \"Bash\" && tool_input.command matches \"(npm run dev|pnpm( run)? dev|yarn dev|bun run dev)\"",
  "description": "阻止在 tmux 外运行开发服务器 - 确保日志可访问"
}
```
**功能：** 强制在 tmux 会话中运行开发服务器，以便记录和查看日志。

#### Hook 2: 提醒在 tmux 中运行长时间命令
```json
{
  "matcher": "测试、安装、构建等长时间命令",
  "description": "提醒使用 tmux 以保持会话持久化"
}
```

#### Hook 3: Git Push 前提醒审查
```json
{
  "matcher": "git push",
  "description": "推送前提醒审查更改"
}
```

#### Hook 4: 阻止创建随机文档文件
```json
{
  "matcher": "Write .md 或 .txt 文件（排除 README 等标准文档）",
  "description": "阻止创建不必要的文档文件 - 保持文档集中"
}
```

### 2. PreCompact（压缩前）

在上下文压缩前触发，用于保存状态。

#### Hook: 保存状态
```json
{
  "matcher": "*",
  "description": "在上下文压缩前保存状态"
}
```
**功能：** 在会话压缩前保存重要状态，避免丢失关键信息。

### 3. SessionStart（会话开始）

新会话开始时触发。

#### Hook: 加载上下文
```json
{
  "matcher": "*",
  "description": "在新会话时加载先前的上下文并检测包管理器"
}
```

### 4. PostToolUse（工具使用后）

工具执行后触发，用于自动化后处理。

#### Hook 1: 记录 PR 创建
```json
{
  "matcher": "gh pr create",
  "description": "创建 PR 后记录 URL 并提供审查命令"
}
```

#### Hook 2: 构建后异步分析
```json
{
  "matcher": "npm run build|pnpm build|yarn build",
  "async": true,
  "description": "异步 Hook 示例：构建分析（在后台运行，不阻塞）"
}
```

#### Hook 3: 自动格式化代码
```json
{
  "matcher": "Edit .ts/.tsx/.js/.jsx 文件",
  "command": "npx prettier --write",
  "description": "编辑后自动使用 Prettier 格式化 JS/TS 文件"
}
```

#### Hook 4: TypeScript 类型检查
```json
{
  "matcher": "Edit .ts/.tsx 文件",
  "description": "编辑 .ts/.tsx 文件后进行 TypeScript 检查"
}
```

#### Hook 5: 警告 console.log
```json
{
  "matcher": "Edit .ts/.tsx/.js/.jsx 文件",
  "description": "编辑后警告 console.log 语句"
}
```

### 5. Stop（响应完成时）

Claude 完成响应后触发。

#### Hook 1: 检查 console.log
```json
{
  "matcher": "*",
  "description": "每次响应后检查修改文件中的 console.log"
}
```

### 6. SessionEnd（会话结束时）

会话结束时触发。

#### Hook 1: 持久化会话状态
```json
{
  "matcher": "*",
  "description": "会话结束时持久化状态"
}
```

#### Hook 2: 评估会话模式
```json
{
  "matcher": "*",
  "description": "评估会话以提取可重用模式（持续学习）"
}
```

## 使用建议

### 对于开发者

1. **理解每个 Hook 的作用**
   - 阅读描述了解触发条件
   - 理解 Hook 执行的操作

2. **自定义 Hooks**
   - 根据项目需求修改 `matcher`
   - 调整命令逻辑
   - 添加新的 Hook 规则

3. **调试 Hooks**
   - Hook 命令出错时会阻止操作
   - 检查命令输出获取错误信息
   - 使用 `echo` 调试复杂命令

### 调整配置

#### 禁用某个 Hook

注释掉或删除对应的 Hook 配置块。

#### 修改触发条件

调整 `matcher` 字段，使用：
- `tool == "ToolName"` - 匹配特定工具
- `tool_input.command matches "pattern"` - 匹配命令模式
- `tool_input.file_path matches "pattern"` - 匹配文件路径

#### 自定义命令

修改 `command` 字段，可以：
- 运行 Shell 脚本
- 调用 Node.js 脚本
- 显示提示信息
- 执行验证逻辑

## 常见问题

### Q: Hook 阻止了我的操作怎么办？

A:
1. 检查终端输出的错误信息
2. 理解 Hook 的目的（如：要求在 tmux 中运行）
3. 按照提示操作（如：创建 tmux 会话）
4. 如果确实需要禁用，临时注释掉该 Hook

### Q: 如何添加新的 Hook？

A: 在对应的 Hook 类型下添加新的配置块：
```json
{
  "matcher": "你的匹配规则",
  "hooks": [
    {
      "type": "command",
      "command": "你的命令"
    }
  ],
  "description": "Hook 说明"
}
```

### Q: Hook 命令执行失败？

A:
1. 检查命令语法是否正确
2. 验证环境变量是否正确设置
3. 测试命令是否能独立运行
4. 查看 Hook 输出获取详细错误信息

## 最佳实践

1. **渐进式采用**
   - 开始时只启用关键 Hooks
   - 逐步添加更多自动化
   - 根据团队反馈调整

2. **团队同步**
   - 将 hooks.json 提交到版本控制
   - 团队成员使用相同配置
   - 定期审查和更新 Hooks

3. **性能考虑**
   - Hook 命令应快速执行
   - 使用 `async: true` 避免阻塞
   - 设置合理的 `timeout` 限制

4. **安全性**
   - 验证命令输入，避免注入攻击
   - 不要在 Hook 中硬编码敏感信息
   - 审查修改文件的 Hooks

## 中文版说明

此配置已从英文原版完整复制，保留了所有原始自动化逻辑。中文项目提供：
- 完整的中文使用说明
- Hook 配置的详细解释
- 自定义和调优指南

---

**相关文档：**
- [Hooks 最佳实践](../../docs/定制指南.md)
- [自动化工作流](../../docs/实战案例.md)
