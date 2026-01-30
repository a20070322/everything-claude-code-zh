---
name: skill-create
description: 分析本地 git 历史以提取编码模式并生成 SKILL.md 文件。Skill Creator GitHub App 的本地版本。
allowed_tools: ["Bash", "Read", "Write", "Grep", "Glob"]
---

# /skill-create - 本地技能生成

分析您仓库的 git 历史以提取编码模式并生成 SKILL.md 文件,教 Claude 您团队的实践。

## 用法

```bash
/skill-create                    # 分析当前仓库
/skill-create --commits 100      # 分析最近 100 次提交
/skill-create --output ./skills  # 自定义输出目录
/skill-create --instincts        # 同时为 continuous-learning-v2 生成直觉
```

## 功能

1. **解析 Git 历史** - 分析提交、文件更改和模式
2. **检测模式** - 识别重复的工作流和约定
3. **生成 SKILL.md** - 创建有效的 Claude Code 技能文件
4. **可选创建直觉** - 用于 continuous-learning-v2 系统

## 分析步骤

### 步骤 1: 收集 Git 数据

```bash
# 获取最近的提交和文件更改
git log --oneline -n ${COMMITS:-200} --name-only --pretty=format:"%H|%s|%ad" --date=short

# 获取按文件的提交频率
git log --oneline -n 200 --name-only | grep -v "^$" | grep -v "^[a-f0-9]" | sort | uniq -c | sort -rn | head -20

# 获取提交消息模式
git log --oneline -n 200 | cut -d' ' -f2- | head -50
```

### 步骤 2: 检测模式

查找这些模式类型:

| 模式 | 检测方法 |
|---------|-----------------|
| **提交约定** | 提交消息的正则表达式(feat:、fix:、chore:) |
| **文件协同更改** | 总是一起更改的文件 |
| **工作流序列** | 重复的文件更改模式 |
| **架构** | 文件夹结构和命名约定 |
| **测试模式** | 测试文件位置、命名、覆盖率 |

### 步骤 3: 生成 SKILL.md

输出格式:

```markdown
---
name: {repo-name}-patterns
description: Coding patterns extracted from {repo-name}
version: 1.0.0
source: local-git-analysis
analyzed_commits: {count}
---

# {Repo Name} Patterns

## Commit Conventions
{detected commit message patterns}

## Code Architecture
{detected folder structure and organization}

## Workflows
{detected repeating file change patterns}

## Testing Patterns
{detected test conventions}
```

### 步骤 4: 生成直觉(如果使用 --instincts)

用于 continuous-learning-v2 集成:

```yaml
---
id: {repo}-commit-convention
trigger: "when writing a commit message"
confidence: 0.8
domain: git
source: local-repo-analysis
---

# Use Conventional Commits

## Action
Prefix commits with: feat:, fix:, chore:, docs:, test:, refactor:

## Evidence
- Analyzed {n} commits
- {percentage}% follow conventional commit format
```

## 示例输出

在 TypeScript 项目上运行 `/skill-create` 可能产生:

```markdown
---
name: my-app-patterns
description: Coding patterns from my-app repository
version: 1.0.0
source: local-git-analysis
analyzed_commits: 150
---

# My App Patterns

## Commit Conventions

This project uses **conventional commits**:
- `feat:` - New features
- `fix:` - Bug fixes
- `chore:` - Maintenance tasks
- `docs:` - Documentation updates

## Code Architecture

```
src/
├── components/     # React components (PascalCase.tsx)
├── hooks/          # Custom hooks (use*.ts)
├── utils/          # Utility functions
├── types/          # TypeScript type definitions
└── services/       # API and external services
```

## Workflows

### Adding a New Component
1. Create `src/components/ComponentName.tsx`
2. Add tests in `src/components/__tests__/ComponentName.test.tsx`
3. Export from `src/components/index.ts`

### Database Migration
1. Modify `src/db/schema.ts`
2. Run `pnpm db:generate`
3. Run `pnpm db:migrate`

## Testing Patterns

- Test files: `__tests__/` directories or `.test.ts` suffix
- Coverage target: 80%+
- Framework: Vitest
```

## GitHub App 集成

对于高级功能(10k+ 提交、团队共享、自动 PR),使用 [Skill Creator GitHub App](https://github.com/apps/skill-creator):

- 安装: [github.com/apps/skill-creator](https://github.com/apps/skill-creator)
- 在任何问题上评论 `/skill-creator analyze`
- 接收包含生成技能的 PR

## 相关命令

- `/instinct-import` - 导入生成的直觉
- `/instinct-status` - 查看已学习的直觉
- `/evolve` - 将直觉聚类到技能/代理

---

*属于 [Everything Claude Code](https://github.com/affaan-m/everything-claude-code) 的一部分*
