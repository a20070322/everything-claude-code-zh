# Eval 命令

管理 eval（评估）驱动开发工作流。

## 使用方法

`/eval [define|check|report|list] [feature-name]`

## 定义 Eval

`/eval define feature-name`

创建新的 eval 定义：

1. 使用模板创建 `.claude/evals/feature-name.md`：

```markdown
## EVAL: feature-name
创建时间：$(date)

### 能力评估
- [ ] [能力 1 的描述]
- [ ] [能力 2 的描述]

### 回归评估
- [ ] [现有行为 1 仍然有效]
- [ ] [现有行为 2 仍然有效]

### 成功标准
- 能力评估的 pass@3 > 90%
- 回归评估的 pass^3 = 100%
```

2. 提示用户填写具体标准

## 检查 Eval

`/eval check feature-name`

运行功能的评估：

1. 从 `.claude/evals/feature-name.md` 读取 eval 定义
2. 对于每个能力评估：
   - 尝试验证标准
   - 记录 PASS/FAIL
   - 在 `.claude/evals/feature-name.log` 中记录尝试
3. 对于每个回归评估：
   - 运行相关测试
   - 与基准比较
   - 记录 PASS/FAIL
4. 报告当前状态：

```
EVAL CHECK: feature-name
========================
能力：X/Y 通过
回归：X/Y 通过
状态：进行中 / 就绪
```

## Eval 报告

`/eval report feature-name`

生成综合评估报告：

```
EVAL REPORT: feature-name
=========================
生成时间：$(date)

能力评估
----------------
[eval-1]: PASS (pass@1)
[eval-2]: PASS (pass@2) - 需要重试
[eval-3]: FAIL - 见注释

回归评估
----------------
[test-1]: PASS
[test-2]: PASS
[test-3]: PASS

指标
-------
能力 pass@1：67%
能力 pass@3：100%
回归 pass^3：100%

注释
-----
[任何问题、边缘情况或观察结果]

建议
--------------
[可以发布 / 需要改进 / 已阻塞]
```

## 列出 Eval

`/eval list`

显示所有 eval 定义：

```
EVAL DEFINITIONS
================
feature-auth      [3/5 通过] 进行中
feature-search    [5/5 通过] 就绪
feature-export    [0/4 通过] 未开始
```

## 参数

$ARGUMENTS：
- `define <name>` - 创建新的 eval 定义
- `check <name>` - 运行并检查评估
- `report <name>` - 生成完整报告
- `list` - 显示所有评估
- `clean` - 删除旧的 eval 日志（保留最近 10 次运行）
