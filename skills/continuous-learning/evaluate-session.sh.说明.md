# evaluate-session.sh 脚本说明

## 脚本功能

这是一个 **持续学习 - 会话评估脚本**，在每个会话结束时自动运行，从会话中提取可重用的模式并保存为学到的技能。

## 工作流程

### 1. 触发时机
- 通过 Stop Hook 在会话结束时触发
- 轻量级：只运行一次，不会在每条消息时运行

### 2. 会话长度检查
```bash
MIN_SESSION_LENGTH=10  # 最小会话长度（消息数）
```
- 短于阈值的会话会被跳过
- 避免从简短对话中提取模式

### 3. 模式提取
**检测模式：**
- `error_resolution` - 错误解决方案
- `debugging_techniques` - 调试技巧
- `workarounds` - 变通方法
- `project_specific` - 项目特定模式

**忽略模式：**
- `simple_typos` - 简单拼写错误
- `one_time_fixes` - 一次性修复
- `external_api_issues` - 外部 API 问题

### 4. 保存位置
```bash
~/.claude/skills/learned/
```

## 配置文件

### config.json 位置
```
skills/continuous-learning/config.json
```

### 配置项
```json
{
  "min_session_length": 10,              // 最小会话长度
  "learned_skills_path": "~/.claude/skills/learned/",  // 学习技能保存路径
  "patterns_to_detect": [               // 要检测的模式
    "error_resolution",
    "debugging_techniques",
    "project_specific"
  ],
  "ignore_patterns": [                 // 要忽略的模式
    "simple_typos",
    "one_time_fixes"
  ]
}
```

## Hook 配置

在 `~/.claude/settings.json` 中配置：

```json
{
  "hooks": {
    "Stop": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "~/.claude/skills/continuous-learning/evaluate-session.sh"
      }]
    }]
  }
}
```

## 使用示例

### 正常运行
```
[ContinuousLearning] Session has 25 messages - evaluate for extractable patterns
[ContinuousLearning] Save learned skills to: /Users/user/.claude/skills/learned/
```

### 短会话跳过
```
[ContinuousLearning] Session too short (5 messages), skipping
```

## 输出位置

提取的模式保存为新的技能文件：
```
~/.claude/skills/learned/
├── error-resolution-2025-01-30.md
├── debugging-technique-2025-01-30.md
└── project-specific-pattern.md
```

## 为什么使用 Stop Hook 而非 UserPromptSubmit？

### Stop Hook（推荐）
✅ 会话结束时运行一次（轻量级）
✅ 不增加每条消息的延迟
✅ 可以访问完整会话记录

### UserPromptSubmit
❌ 每条消息时运行（重量级）
❌ 给每条消息增加延迟
❌ 可能影响性能

## 中文版说明

此脚本实现了 **持续学习系统**，自动从你的 Claude Code 会话中提取有价值的模式和解决方案。

### 核心价值

1. **自动积累经验**
   - 从每次会话中学习
   - 自动提取可重用模式
   - 持续改进技能库

2. **团队知识共享**
   - 将个人经验转化为团队资产
   - 其他成员可以复用
   - 减少重复犯错

3. **智能提取**
   - 只提取有价值的模式
   - 忽略临时性和琐碎内容
   - 保存为结构化技能文件

### 使用建议

1. **保持会话质量**
   - 详细描述问题和解决方案
   - 提供足够的上下文
   - 记录调试过程

2. **定期审查学到的技能**
   - 查看 `~/.claude/skills/learned/`
   - 验证提取的准确性
   - 整理和优化技能

3. **团队共享**
   - 将 learned/ 目录添加到项目
   - 通过 Git 分享技能
   - 建立团队技能库

---

**相关文档：**
- [持续学习技能](./SKILL.md)
- [定制指南 - 持续学习](../../docs/定制指南.md)
- [实战案例 - 持续学习](../../docs/实战案例.md)
