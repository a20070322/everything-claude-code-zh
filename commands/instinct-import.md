---
name: instinct-import
description: 从团队成员、Skill Creator 或其他来源导入直觉
command: true
---

# 直觉导入命令

## 实现

使用插件根路径运行直觉 CLI:

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/scripts/instinct-cli.py" import <file-or-url> [--dry-run] [--force] [--min-confidence 0.7]
```

或者如果未设置 `CLAUDE_PLUGIN_ROOT`(手动安装):

```bash
python3 ~/.claude/skills/continuous-learning-v2/scripts/instinct-cli.py import <file-or-url>
```

从以下来源导入直觉:
- 团队成员的导出
- Skill Creator(仓库分析)
- 社区集合
- 以前的机器备份

## 用法

```
/instinct-import team-instincts.yaml
/instinct-import https://github.com/org/repo/instincts.yaml
/instinct-import --from-skill-creator acme/webapp
```

## 操作步骤

1. 获取直觉文件(本地路径或 URL)
2. 解析并验证格式
3. 检查与现有直觉的重复
4. 合并或添加新直觉
5. 保存到 `~/.claude/homunculus/instincts/inherited/`

## 导入过程

```
📥 正在从以下位置导入直觉: team-instincts.yaml
================================================

发现 12 个要导入的直觉。

正在分析冲突...

## 新直觉(8)
这些将被添加:
  ✓ use-zod-validation (confidence: 0.7)
  ✓ prefer-named-exports (confidence: 0.65)
  ✓ test-async-functions (confidence: 0.8)
  ...

## 重复直觉(3)
已有类似的直觉:
  ⚠️ prefer-functional-style
     本地: 0.8 置信度, 12 次观察
     导入: 0.7 置信度
     → 保留本地(更高置信度)

  ⚠️ test-first-workflow
     本地: 0.75 置信度
     导入: 0.9 置信度
     → 更新为导入版本(更高置信度)

## 冲突直觉(1)
这些与本地直觉矛盾:
  ❌ use-classes-for-services
     与以下内容冲突: avoid-classes
     → 跳过(需要手动解决)

---
导入 8 个新,更新 1 个,跳过 3 个?
```

## 合并策略

### 对于重复项
当导入与现有直觉匹配的直觉时:
- **更高置信度获胜**: 保留置信度更高的那个
- **合并证据**: 合并观察计数
- **更新时间戳**: 标记为最近验证

### 对于冲突
当导入与现有直觉矛盾的直觉时:
- **默认跳过**: 不导入冲突的直觉
- **标记供审查**: 将两者都标记为需要注意
- **手动解决**: 用户决定保留哪个

## 来源跟踪

导入的直觉标记为:
```yaml
source: "inherited"
imported_from: "team-instincts.yaml"
imported_at: "2025-01-22T10:30:00Z"
original_source: "session-observation"  # 或 "repo-analysis"
```

## Skill Creator 集成

从 Skill Creator 导入时:

```
/instinct-import --from-skill-creator acme/webapp
```

这将获取从仓库分析生成的直觉:
- 来源: `repo-analysis`
- 更高的初始置信度(0.7+)
- 链接到源仓库

## 标志

- `--dry-run`: 预览而不导入
- `--force`: 即使存在冲突也导入
- `--merge-strategy <higher|local|import>`: 如何处理重复项
- `--from-skill-creator <owner/repo>`: 从 Skill Creator 分析导入
- `--min-confidence <n>`: 仅导入高于阈值的直觉

## 输出

导入后:
```
✅ 导入完成!

已添加: 8 个直觉
已更新: 1 个直觉
已跳过: 3 个直觉(2 个重复,1 个冲突)

新直觉已保存到: ~/.claude/homunculus/instincts/inherited/

运行 /instinct-status 查看所有直觉。
```
