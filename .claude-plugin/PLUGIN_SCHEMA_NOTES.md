# 插件清单架构说明

本文档记录了 Claude Code 插件清单验证器的**未文档化但强制执行的约束条件**。

这些规则基于实际的安装失败案例、验证器行为以及与已知可用插件的比较。
它们的存在是为了防止静默破坏和反复出现的回归问题。

如果你要编辑 `.claude-plugin/plugin.json`，请先阅读本文。

---

## 摘要（请先阅读本节）

Claude 插件清单验证器是**严格且有明确偏好的**。
它强制执行的规则并未在公开的架构参考中完整文档化。

最常见的失败模式是：

> 清单看起来合理，但验证器拒绝它并给出模糊的错误信息，如
> `agents: Invalid input`

本文档将解释原因。

---

## 必需字段

### `version`（必需）

验证器要求必须有 `version` 字段，即使某些示例中省略了它。

如果缺少该字段，在市场安装或 CLI 验证期间可能会失败。

示例：

```json
{
  "version": "1.1.0"
}
```

---

## 字段形状规则

以下字段**必须始终为数组**：

* `agents`
* `commands`
* `skills`
* `hooks`（如果存在）

即使只有一个条目，**也不接受字符串**。

### 无效

```json
{
  "agents": "./agents"
}
```

### 有效

```json
{
  "agents": ["./agents/planner.md"]
}
```

这适用于所有组件路径字段。

---

## 路径解析规则（关键）

### Agents 必须使用显式文件路径

验证器**不接受 `agents` 的目录路径**。

即使是以下格式也会失败：

```json
{
  "agents": ["./agents/"]
}
```

相反，你必须显式枚举 agent 文件：

```json
{
  "agents": [
    "./agents/planner.md",
    "./agents/architect.md",
    "./agents/code-reviewer.md"
  ]
}
```

这是验证错误最常见的原因。

### Commands 和 Skills

* `commands` 和 `skills` 仅在包装为数组时才接受目录路径
* 显式文件路径是最安全且最面向未来的做法

---

## 验证器行为说明

* `claude plugin validate` 比某些市场预览更严格
* 如果路径不明确，本地验证可能通过但安装时失败
* 错误信息通常是通用的（`Invalid input`），不指示根本原因
* 跨平台安装（尤其是 Windows）对路径假设的容错性较低

假设验证器是严格且按字面理解执行的。

---

## `hooks` 字段：请勿添加

> ⚠️ **关键：** 请勿在 `plugin.json` 中添加 `"hooks"` 字段。这由回归测试强制执行。

### 为什么这很重要

Claude Code v2.1+ 会按照约定**自动加载**任何已安装插件的 `hooks/hooks.json`。如果你在 `plugin.json` 中也声明它，会出现：

```
Duplicate hooks file detected: ./hooks/hooks.json resolves to already-loaded file.
The standard hooks/hooks.json is loaded automatically, so manifest.hooks should
only reference additional hook files.
```

### 反复无常的历史

这导致本仓库中反复出现修复/回退周期：

| 提交 | 操作 | 触发原因 |
|--------|--------|---------|
| `22ad036` | 添加 hooks | 用户报告"hooks 未加载" |
| `a7bc5f2` | 移除 hooks | 用户报告"重复 hooks 错误"(#52) |
| `779085e` | 添加 hooks | 用户报告"agents 未加载"(#88) |
| `e3a1306` | 移除 hooks | 用户报告"重复 hooks 错误"(#103) |

**根本原因：** Claude Code CLI 在版本之间改变了行为：
- v2.1 之前：需要显式 `hooks` 声明
- v2.1+：按约定自动加载，重复时报错

### 当前规则（由测试强制执行）

`tests/hooks/hooks.test.js` 中的测试 `plugin.json does NOT have explicit hooks declaration` 可防止此问题被重新引入。

**如果你要添加额外的 hook 文件**（非 `hooks/hooks.json`），那些文件可以声明。但标准的 `hooks/hooks.json` 绝不能声明。

---

## 已知反模式

以下形式看起来正确但会被拒绝：

* 字符串值而非数组
* `agents` 使用目录数组
* 缺少 `version`
* 依赖推断路径
* 假设市场行为与本地验证一致
* **添加 `"hooks": "./hooks/hooks.json"`** - 按约定自动加载，导致重复错误

避免耍小聪明。要显式明确。

---

## 最小可用示例

```json
{
  "version": "1.1.0",
  "agents": [
    "./agents/planner.md",
    "./agents/code-reviewer.md"
  ],
  "commands": ["./commands/"],
  "skills": ["./skills/"]
}
```

此结构已通过 Claude 插件验证器验证。

**重要：** 注意这里**没有** `"hooks"` 字段。`hooks/hooks.json` 文件按约定自动加载。显式添加它会导致重复错误。

---

## 给贡献者的建议

在提交涉及 `plugin.json` 的更改之前：

1. 对 agents 使用显式文件路径
2. 确保所有组件字段都是数组
3. 包含 `version`
4. 运行：

```bash
claude plugin validate .claude-plugin/plugin.json
```

如果有疑问，选择详尽而非便捷。

---

## 为什么存在此文件

此仓库被广泛分叉并用作参考实现。

在此记录验证器的怪癖：

* 防止重复问题
* 减少贡献者的挫败感
* 随着生态系统发展保持插件稳定性

如果验证器发生变化，请先更新本文档。
