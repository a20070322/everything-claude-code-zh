# Orchestrate 命令

用于复杂任务的顺序代理工作流。

## 使用方法

`/orchestrate [workflow-type] [task-description]`

## 工作流类型

### feature
完整功能实施工作流：
```
planner -> tdd-guide -> code-reviewer -> security-reviewer
```

### bugfix
Bug 调查和修复工作流：
```
explorer -> tdd-guide -> code-reviewer
```

### refactor
安全重构工作流：
```
architect -> code-reviewer -> tdd-guide
```

### security
安全重点审查：
```
security-reviewer -> code-reviewer -> architect
```

## 执行模式

对于工作流中的每个代理：

1. **调用代理**，传入上一个代理的上下文
2. **收集输出**作为结构化交接文档
3. **传递给链中的下一个代理**
4. **汇总结果**到最终报告

## 交接文档格式

在代理之间创建交接文档：

```markdown
## HANDOFF: [previous-agent] -> [next-agent]

### 上下文
[已完成工作的摘要]

### 发现
[关键发现或决策]

### 修改的文件
[接触的文件列表]

### 未解决的问题
[下一个代理未解决的项目]

### 建议
[建议的下一步]
```

## 示例：功能工作流

```
/orchestrate feature "Add user authentication"
```

执行：

1. **Planner 代理**
   - 分析需求
   - 创建实施计划
   - 识别依赖关系
   - 输出：`HANDOFF: planner -> tdd-guide`

2. **TDD Guide 代理**
   - 读取 planner 交接
   - 先编写测试
   - 实施以通过测试
   - 输出：`HANDOFF: tdd-guide -> code-reviewer`

3. **Code Reviewer 代理**
   - 审查实施
   - 检查问题
   - 建议改进
   - 输出：`HANDOFF: code-reviewer -> security-reviewer`

4. **Security Reviewer 代理**
   - 安全审计
   - 漏洞检查
   - 最终批准
   - 输出：最终报告

## 最终报告格式

```
ORCHESTRATION REPORT
====================
工作流：feature
任务：Add user authentication
代理：planner -> tdd-guide -> code-reviewer -> security-reviewer

摘要
-------
[一段摘要]

代理输出
-------------
Planner: [摘要]
TDD Guide: [摘要]
Code Reviewer: [摘要]
Security Reviewer: [摘要]

更改的文件
-------------
[列出所有修改的文件]

测试结果
------------
[测试通过/失败摘要]

安全状态
---------------
[安全发现]

建议
--------------
[可以发布 / 需要改进 / 已阻塞]
```

## 并行执行

对于独立检查，并行运行代理：

```markdown
### 并行阶段
同时运行：
- code-reviewer（质量）
- security-reviewer（安全）
- architect（设计）

### 合并结果
将输出合并到单个报告
```

## 参数

$ARGUMENTS：
- `feature <description>` - 完整功能工作流
- `bugfix <description>` - Bug 修复工作流
- `refactor <description>` - 重构工作流
- `security <description>` - 安全审查工作流
- `custom <agents> <description>` - 自定义代理序列

## 自定义工作流示例

```
/orchestrate custom "architect,tdd-guide,code-reviewer" "Redesign caching layer"
```

## 提示

1. 对于复杂功能，**从 planner 开始**
2. 在合并之前**始终包含 code-reviewer**
3. 对于身份验证/支付/个人信息，**使用 security-reviewer**
4. **保持交接简洁** - 专注于下一个代理需要的内容
5. 如有必要，**在代理之间运行验证**
