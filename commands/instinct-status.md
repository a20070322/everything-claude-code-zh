---
name: instinct-status
description: 显示所有已学习的直觉及其置信度级别
command: true
---

# 直觉状态命令

显示所有已学习的直觉及其置信度分数,按领域分组。

## 实现

使用插件根路径运行直觉 CLI:

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/scripts/instinct-cli.py" status
```

或者如果未设置 `CLAUDE_PLUGIN_ROOT`(手动安装),使用:

```bash
python3 ~/.claude/skills/continuous-learning-v2/scripts/instinct-cli.py status
```

## 用法

```
/instinct-status
/instinct-status --domain code-style
/instinct-status --low-confidence
```

## 操作步骤

1. 从 `~/.claude/homunculus/instincts/personal/` 读取所有直觉文件
2. 从 `~/.claude/homunculus/instincts/inherited/` 读取继承的直觉
3. 按领域分组显示它们并带有置信度条

## 输出格式

```
📊 直觉状态
==================

## 代码风格(4 个直觉)

### prefer-functional-style
触发器: when writing new functions
操作: Use functional patterns over classes
置信度: ████████░░ 80%
来源: session-observation | 最后更新: 2025-01-22

### use-path-aliases
触发器: when importing modules
操作: Use @/ path aliases instead of relative imports
置信度: ██████░░░░ 60%
来源: repo-analysis (github.com/acme/webapp)

## 测试(2 个直觉)

### test-first-workflow
触发器: when adding new functionality
操作: Write test first, then implementation
置信度: █████████░ 90%
来源: session-observation

## 工作流(3 个直觉)

### grep-before-edit
触发器: when modifying code
操作: Search with Grep, confirm with Read, then Edit
置信度: ███████░░░ 70%
来源: session-observation

---
总计: 9 个直觉(4 个个人,5 个继承)
观察器: 运行中(上次分析: 5 分钟前)
```

## 标志

- `--domain <name>`: 按领域过滤(code-style、testing、git 等)
- `--low-confidence`: 仅显示置信度 < 0.5 的直觉
- `--high-confidence`: 仅显示置信度 >= 0.7 的直觉
- `--source <type>`: 按来源过滤(session-observation、repo-analysis、inherited)
- `--json`: 以 JSON 格式输出供程序使用
