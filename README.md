# Everything Claude Code 中文版 🇨🇳

> 原项目：[affaan-m/everything-claude-code](https://github.com/affaan-m/everything-claude-code)
>
> **The complete collection of Claude Code configs from an Anthropic hackathon winner.**
>
> 生产环境级别的代理、技能、钩子、命令、规则和 MCP 配置，经过 10+ 个月的密集日常使用演进而来。

---

## 📖 项目说明

这是 **Everything Claude Code** 项目的完整中文翻译版本。

本项目提供了一套经过实战验证的 Claude Code 配置集合，包括：

- 🔧 **16 个技能（Skills）** - 编码标准、设计模式、工作流
- ⚡ **23 个命令（Commands）** - 开发、测试、审查、规划
- 🤖 **12 个代理（Agents）** - 专业领域专家
- 🪝 **完整的钩子（Hooks）** - 自动化工作流
- 📜 **8 个规则集（Rules）** - 编码规范和最佳实践

---

## ✨ 特性

- ✅ **实战验证** - 10+ 个月的真实产品开发经验
- ✅ **生产就绪** - 可直接用于生产环境
- ✅ **完整覆盖** - Claude Code 所有功能的配置
- ✅ **持续进化** - 包含持续学习和自动改进机制
- ✅ **全中文文档** - 完整的中文翻译和使用指南

---

## 🚀 快速开始

### 安装

```bash
# 1. 克隆本仓库
git clone https://github.com/your-username/everything-claude-code-zh.git

# 2. 进入项目目录
cd everything-claude-code-zh

# 3. 安装为 Claude Code 插件
claude plugin install . --scope user
```

### 使用

安装后，所有技能、命令和代理将自动可用。

**常用命令：**
```bash
/plan              # 规划新功能
/learn             # 从会话中提取可重用模式
/code-review       # 代码审查
/verify            # 验证更改
/refactor-clean    # 清理重构
```

---

## 📚 文档导航

### 指南文档

- [短篇指南](docs/translated/the-shortform-guide.md) - 快速入门，必读 ⭐
- [长篇指南](docs/translated/the-longform-guide.md) - 深入理解，进阶必读
- [快速开始](docs/快速开始.md) - 5 分钟上手教程
- [组件详解](docs/组件详解.md) - Skills、Commands、Agents 详解
- [实战案例](docs/实战案例.md) - 实际应用场景
- [定制指南](docs/定制指南.md) - 根据项目调整配置

### 核心组件

#### 📂 Skills（技能）
- [编码标准](translated/skills/coding-standards.md) - TypeScript/JavaScript 编码规范
- [前端模式](translated/skills/frontend-patterns.md) - React/TypeScript 前端模式
- [后端模式](translated/skills/backend-patterns.md) - Node.js 后端模式
- [Go 模式](translated/skills/golang-patterns.md) - Go 语言特定模式
- [TDD 工作流](translated/skills/tdd-workflow.md) - 测试驱动开发
- [安全审查](translated/skills/security-review.md) - 安全最佳实践

#### ⚡ Commands（命令）
- [规划](translated/commands/plan.md) - 功能规划和分解
- [学习](translated/commands/learn.md) - 提取可重用模式
- [代码审查](translated/commands/code-review.md) - 全面代码审查
- [验证](translated/commands/verify.md) - 验证代码更改
- [重构清理](translated/commands/refactor-clean.md) - 清理和重构代码

#### 🤖 Agents（代理）
- [规划者](translated/agents/planner.md) - 复杂功能规划专家
- [架构师](translated/agents/architect.md) - 系统架构设计专家
- [代码审查员](translated/agents/code-reviewer.md) - 代码质量审查专家
- [数据库审查员](translated/agents/database-reviewer.md) - 数据库优化专家
- [安全审查员](translated/agents/security-reviewer.md) - 安全漏洞审查专家

#### 🪝 Hooks（钩子）
- [Hooks 配置](translated/hooks/hooks.json) - 完整的钩子配置和说明

---

## 🌟 核心功能

### 1. 持续学习机制

自动从会话中提取可重用模式并转化为技能：

```bash
# 解决问题后
/learn

# AI 会分析会话，提取模式，保存为技能
```

### 2. Instinct 状态管理

保存和恢复工作状态：

```bash
# 保存当前状态
/instinct-export

# 恢复之前的状态
/instinct-import

# 查看当前状态
/instinct-status
```

### 3. 自动化质量保障

- ✅ 自动代码格式化（Prettier）
- ✅ 自动 TypeScript 类型检查
- ✅ 自动 console.log 检测
- ✅ Git 提交前提醒
- ✅ 危险操作拦截

### 4. 分层代理体系

```
规划层（Planner, Architect）
    ↓
执行层（领域专家）
    ↓
清理层（Refactor Cleaner, Build Resolver）
```

---

## 📖 使用指南

### 新手入门

1. **阅读短篇指南** - 理解核心概念
2. **尝试常用命令** - 体验核心功能
3. **查看组件详解** - 深入了解各个组件

### 进阶使用

1. **阅读长篇指南** - 掌握高级特性
2. **定制配置** - 根据项目调整
3. **创建自己的技能** - 积累团队经验

### 团队协作

1. **配置同步** - 统一团队配置
2. **技能共享** - 建立团队知识库
3. **Code Review** - 标准化审查流程

---

## 🛠️ 定制和扩展

### 根据项目调整

1. **选择需要的技能** - 删除不相关的语言/框架
2. **定制命令** - 添加项目特定命令
3. **调整 Hooks** - 符合团队工作流

### 添加自己的内容

```bash
# 添加技能
mkdir -p skills/my-skill
# 创建 SKILL.md 文件

# 添加命令
# 在 commands/ 目录创建 .md 文件

# 添加代理
# 在 agents/ 目录创建 .md 文件
```

详见 [定制指南](docs/定制指南.md)

---

## 📊 项目结构

```
everything-claude-code-zh/
├── README.md                    # 本文件
├── docs/                        # 中文文档
│   ├── 快速开始.md
│   ├── 组件详解.md
│   ├── 实战案例.md
│   └── 定制指南.md
├── translated/                  # 翻译文件
│   ├── skills/                 # 翻译的技能
│   ├── commands/               # 翻译的命令
│   ├── agents/                 # 翻译的代理
│   ├── hooks/                  # 翻译的钩子
│   ├── rules/                  # 翻译的规则
│   └── guides/                 # 翻译的指南
└── original/                    # 原项目链接
    └── link -> https://github.com/affaan-m/everything-claude-code
```

---

## 🤝 贡献

欢迎贡献！

**贡献方式：**
1. 改进翻译质量
2. 添加中文示例
3. 编写使用教程
4. 分享定制经验

**贡献流程：**
1. Fork 本仓库
2. 创建分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

---

## 📄 许可证

原项目采用 **MIT 许可证**。

本翻译版本遵循原项目的 MIT 许可证。

**原项目作者：** [Affaan Mustafa](https://x.com/affaanmustafa)
**原项目地址：** https://github.com/affaan-m/everything-claude-code

---

## 🙏 致谢

- 感谢 [Affaan Mustafa](https://github.com/affaan-m) 创建了这个优秀的项目
- 感谢 Anthropic 团队开发了 Claude Code
- 感谢所有贡献者和使用者

---

## 📞 联系方式

- **问题反馈：** [GitHub Issues](https://github.com/your-username/everything-claude-code-zh/issues)
- **功能建议：** [GitHub Discussions](https://github.com/your-username/everything-claude-code-zh/discussions)

---

## 🌟 Star History

如果这个项目对你有帮助，请给个 Star ⭐

[![Star History Chart](https://api.star-history.com/svg?repos=affaan-m/everything-claude-code&type=Date)](https://star-history.com/#affaan-m/everything-claude-code&Date)

---

**最后更新：** 2025-01-30
**翻译状态：** 进行中 🔄
**完成度：** 0% (0/133 文件)

---

<div align="center">

**Made with ❤️ by Chinese Claude Code Community**

</div>
