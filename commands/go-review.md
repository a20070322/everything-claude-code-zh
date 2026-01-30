---
description: 全面的 Go 代码审查,包括惯用模式、并发安全、错误处理和安全。调用 go-reviewer 代理。
---

# Go 代码审查

此命令调用 **go-reviewer** 代理进行全面的 Go 特定代码审查。

## 此命令的功能

1. **识别 Go 更改**: 通过 `git diff` 查找修改的 `.go` 文件
2. **运行静态分析**: 执行 `go vet`、`staticcheck` 和 `golangci-lint`
3. **安全扫描**: 检查 SQL 注入、命令注入、竞态条件
4. **并发审查**: 分析 goroutine 安全性、通道使用、互斥锁模式
5. **惯用 Go 检查**: 验证代码遵循 Go 约定和最佳实践
6. **生成报告**: 按严重程度分类问题

## 使用场景

在以下情况使用 `/go-review`:
- 编写或修改 Go 代码后
- 提交 Go 更改之前
- 审查包含 Go 代码的 PR
- 加入新的 Go 代码库
- 学习惯用的 Go 模式

## 审查类别

### 关键(必须修复)
- SQL/命令注入漏洞
- 没有同步的竞态条件
- Goroutine 泄漏
- 硬编码凭证
- 不安全的指针使用
- 关键路径中忽略的错误

### 高(应该修复)
- 缺少带上下文的错误包装
- 使用 panic 而不是错误返回
- 上下文未传播
- 导致死锁的无缓冲通道
- 接口未满足错误
- 缺少互斥锁保护

### 中(考虑)
- 非惯用代码模式
- 导出缺少 godoc 注释
- 低效的字符串连接
- 切片未预分配
- 未使用表驱动测试

## 运行的自动检查

```bash
# 静态分析
go vet ./...

# 高级检查(如果已安装)
staticcheck ./...
golangci-lint run

# 竞态检测
go build -race ./...

# 安全漏洞
govulncheck ./...
```

## 使用示例

```text
User: /go-review

Agent:
# Go 代码审查报告

## 已审查的文件
- internal/handler/user.go (已修改)
- internal/service/auth.go (已修改)

## 静态分析结果
✓ go vet: 没有问题
✓ staticcheck: 没有问题

## 发现的问题

[关键] 竞态条件
文件: internal/service/auth.go:45
问题: 共享映射在没有同步的情况下被访问
```go
var cache = map[string]*Session{}  // 并发访问!

func GetSession(id string) *Session {
    return cache[id]  // 竞态条件
}
```
修复: 使用 sync.RWMutex 或 sync.Map
```go
var (
    cache   = map[string]*Session{}
    cacheMu sync.RWMutex
)

func GetSession(id string) *Session {
    cacheMu.RLock()
    defer cacheMu.RUnlock()
    return cache[id]
}
```

[高] 缺少错误上下文
文件: internal/handler/user.go:28
问题: 返回错误时没有上下文
```go
return err  // 没有上下文
```
修复: 使用上下文包装
```go
return fmt.Errorf("get user %s: %w", userID, err)
```

## 摘要
- 关键: 1
- 高: 1
- 中: 0

建议: ❌ 阻止合并,直到修复关键问题
```

## 批准标准

| 状态 | 条件 |
|--------|-----------|
| ✅ 批准 | 没有关键或高问题 |
| ⚠️ 警告 | 仅中问题(谨慎合并) |
| ❌ 阻止 | 发现关键或高问题 |

## 与其他命令的集成

- 首先使用 `/go-test` 确保测试通过
- 如果出现构建错误,使用 `/go-build`
- 在提交前使用 `/go-review`
- 对于非 Go 特定问题,使用 `/code-review`

## 相关

- 代理: `agents/go-reviewer.md`
- 技能: `skills/golang-patterns/`、`skills/golang-testing/`
