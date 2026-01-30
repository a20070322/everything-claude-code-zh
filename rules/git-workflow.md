# Git 工作流

## 提交消息格式

```
<type>: <description>

<可选的正文>
```

类型：feat、fix、refactor、docs、test、chore、perf、ci

注意：署名已通过 ~/.claude/settings.json 全局禁用。

## Pull Request 工作流

创建 PR 时：
1. 分析完整提交历史（不仅是最新的提交）
2. 使用 `git diff [base-branch]...HEAD` 查看所有更改
3. 起草全面的 PR 摘要
4. 包含带 TODO 的测试计划
5. 如果是新分支，使用 `-u` 标志推送

## 功能实现工作流

1. **先规划**
   - 使用 **planner** agent 创建实现计划
   - 识别依赖项和风险
   - 分解为多个阶段

2. **TDD 方法**
   - 使用 **tdd-guide** agent
   - 先写测试（RED）
   - 实现以通过测试（GREEN）
   - 重构（IMPROVE）
   - 验证 80%+ 覆盖率

3. **代码审查**
   - 编写代码后立即使用 **code-reviewer** agent
   - 解决 CRITICAL 和 HIGH 问题
   - 尽可能修复 MEDIUM 问题

4. **提交和推送**
   - 详细的提交消息
   - 遵循 conventional commits 格式
