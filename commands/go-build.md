---
description: 逐步修复 Go 构建错误、go vet 警告和 linter 问题。调用 go-build-resolver 代理进行最小化、精确的修复。
---

# Go 构建和修复

此命令调用 **go-build-resolver** 代理以最小化更改逐步修复 Go 构建错误。

## 此命令的功能

1. **运行诊断**: 执行 `go build`、`go vet`、`staticcheck`
2. **解析错误**: 按文件分组并按严重程度排序
3. **逐步修复**: 一次修复一个错误
4. **验证每个修复**: 每次更改后重新运行构建
5. **报告摘要**: 显示已修复和剩余的内容

## 使用场景

在以下情况使用 `/go-build`:
- `go build ./...` 失败并显示错误
- `go vet ./...` 报告问题
- `golangci-lint run` 显示警告
- 模块依赖项损坏
- 拉取破坏构建的更改后

## 运行的诊断命令

```bash
# 主要构建检查
go build ./...

# 静态分析
go vet ./...

# 扩展检查(如果可用)
staticcheck ./...
golangci-lint run

# 模块问题
go mod verify
go mod tidy -v
```

## 示例会话

```text
User: /go-build

Agent:
# Go 构建解析

## 初始诊断

```bash
$ go build ./...
internal/service/user.go:25:15: undefined: UserRepository
internal/handler/api.go:42:9: cannot use x (type string) as type int
internal/handler/api.go:58:2: missing return at end of function
```

发现错误: 3 个

## 修复 1: 未定义的标识符

文件: internal/service/user.go:25
错误: undefined: UserRepository
原因: 缺少导入

```go
// 添加导入
import "project/internal/repository"

// 更改
var repo UserRepository
// 为
var repo repository.UserRepository
```

```bash
$ go build ./...
# 剩余 2 个错误
```

## 修复 2: 类型不匹配

文件: internal/handler/api.go:42
错误: cannot use x (type string) as type int

```go
// 更改
count := params.Get("count")
// 为
countStr := params.Get("count")
count, _ := strconv.Atoi(countStr)
```

```bash
$ go build ./...
# 剩余 1 个错误
```

## 修复 3: 缺少返回

文件: internal/handler/api.go:58
错误: missing return at end of function

```go
func GetUser(id string) (*User, error) {
    if id == "" {
        return nil, ErrInvalidID
    }
    user := findUser(id)
    // 添加缺少的返回
    return user, nil
}
```

```bash
$ go build ./...
# 构建成功!
```

## 最终验证

```bash
$ go vet ./...
# 没有问题

$ go test ./...
ok      project/internal/service   0.015s
ok      project/internal/handler   0.023s
```

## 摘要

| 指标 | 计数 |
|--------|-------|
| 已修复的构建错误 | 3 |
| 已修复的 vet 警告 | 0 |
| 已修改的文件 | 2 |
| 剩余问题 | 0 |

构建状态: ✅ 成功
```

## 常见错误修复

| 错误 | 典型修复 |
|-------|-------------|
| `undefined: X` | 添加导入或修复拼写错误 |
| `cannot use X as Y` | 类型转换或修复赋值 |
| `missing return` | 添加返回语句 |
| `X does not implement Y` | 添加缺失的方法 |
| `import cycle` | 重组包结构 |
| `declared but not used` | 删除或使用变量 |
| `cannot find package` | `go get` 或 `go mod tidy` |

## 修复策略

1. **先修复构建错误** - 代码必须能够编译
2. **其次修复 vet 警告** - 修复可疑的构造
3. **第三修复 lint 警告** - 样式和最佳实践
4. **一次修复一个** - 验证每个更改
5. **最小化更改** - 不要重构,只需修复

## 停止条件

代理将在以下情况停止并报告:
- 同一错误在 3 次尝试后仍然存在
- 修复引入更多错误
- 需要架构更改
- 缺少外部依赖项

## 相关命令

- `/go-test` - 构建成功后运行测试
- `/go-review` - 审查代码质量
- `/verify` - 完整验证循环

## 相关

- 代理: `agents/go-build-resolver.md`
- 技能: `skills/golang-patterns/`
