---
description: 强制执行测试驱动开发工作流。首先搭建接口、生成测试,然后实现最小代码以通过。确保 80%+ 的覆盖率。
---

# TDD 命令

此命令调用 **tdd-guide** 代理强制执行测试驱动开发方法论。

## 此命令的功能

1. **搭建接口** - 首先定义类型/接口
2. **首先生成测试** - 编写失败的测试(红色)
3. **实现最小代码** - 编写刚好足够的代码以通过(绿色)
4. **重构** - 在保持测试绿色的同时改进代码(重构)
5. **验证覆盖率** - 确保 80%+ 的测试覆盖率

## 使用场景

在以下情况使用 `/tdd`:
- 实现新功能
- 添加新函数/组件
- 修复错误(首先编写重现错误的测试)
- 重构现有代码
- 构建关键业务逻辑

## 工作原理

tdd-guide 代理将:

1. **定义接口** 用于输入/输出
2. **编写将失败的测试**(因为代码尚不存在)
3. **运行测试** 并验证它们因正确原因而失败
4. **编写最小实现** 使测试通过
5. **运行测试** 并验证它们通过
6. **重构** 代码同时保持测试绿色
7. **检查覆盖率** 如果低于 80% 则添加更多测试

## TDD 循环

```
红色 → 绿色 → 重构 → 重复

红色:      编写失败的测试
绿色:    编写最小代码以通过
重构: 改进代码,保持测试通过
重复:   下一个功能/场景
```

## 使用示例

```
User: /tdd 我需要一个函数来计算市场流动性分数

Agent (tdd-guide):
# TDD 会话: 市场流动性分数计算器

## 步骤 1: 定义接口(搭建)

```typescript
// lib/liquidity.ts
export interface MarketData {
  totalVolume: number
  bidAskSpread: number
  activeTraders: number
  lastTradeTime: Date
}

export function calculateLiquidityScore(market: MarketData): number {
  // TODO: Implementation
  throw new Error('Not implemented')
}
```

## 步骤 2: 编写失败的测试(红色)

```typescript
// lib/liquidity.test.ts
import { calculateLiquidityScore } from './liquidity'

describe('calculateLiquidityScore', () => {
  it('should return high score for liquid market', () => {
    const market = {
      totalVolume: 100000,
      bidAskSpread: 0.01,
      activeTraders: 500,
      lastTradeTime: new Date()
    }

    const score = calculateLiquidityScore(market)

    expect(score).toBeGreaterThan(80)
    expect(score).toBeLessThanOrEqual(100)
  })

  it('should return low score for illiquid market', () => {
    const market = {
      totalVolume: 100,
      bidAskSpread: 0.5,
      activeTraders: 2,
      lastTradeTime: new Date(Date.now() - 86400000) // 1 天前
    }

    const score = calculateLiquidityScore(market)

    expect(score).toBeLessThan(30)
    expect(score).toBeGreaterThanOrEqual(0)
  })

  it('should handle edge case: zero volume', () => {
    const market = {
      totalVolume: 0,
      bidAskSpread: 0,
      activeTraders: 0,
      lastTradeTime: new Date()
    }

    const score = calculateLiquidityScore(market)

    expect(score).toBe(0)
  })
})
```

## 步骤 3: 运行测试 - 验证失败

```bash
npm test lib/liquidity.test.ts

FAIL lib/liquidity.test.ts
  ✕ should return high score for liquid market (2 ms)
    Error: Not implemented

1 test failed, 0 passed
```

✅ 测试按预期失败。准备实现。

## 步骤 4: 实现最小代码(绿色)

```typescript
// lib/liquidity.ts
export function calculateLiquidityScore(market: MarketData): number {
  // 处理零交易量边界情况
  if (market.totalVolume === 0) {
    return 0
  }

  // 计算组件分数(0-100 比例)
  const volumeScore = Math.min(market.totalVolume / 1000, 100)
  const spreadScore = Math.max(100 - (market.bidAskSpread * 1000), 0)
  const traderScore = Math.min(market.activeTraders / 10, 100)

  // 最近活动奖励
  const hoursSinceLastTrade = (Date.now() - market.lastTradeTime.getTime()) / (1000 * 60 * 60)
  const recencyScore = Math.max(100 - (hoursSinceLastTrade * 10), 0)

  // 加权平均
  const score = (
    volumeScore * 0.4 +
    spreadScore * 0.3 +
    traderScore * 0.2 +
    recencyScore * 0.1
  )

  return Math.min(Math.max(score, 0), 100) // 限制在 0-100
}
```

## 步骤 5: 运行测试 - 验证通过

```bash
npm test lib/liquidity.test.ts

PASS lib/liquidity.test.ts
  ✓ should return high score for liquid market (3 ms)
  ✓ should return low score for illiquid market (2 ms)
  ✓ should handle edge case: zero volume (1 ms)

3 tests passed
```

✅ 所有测试通过!

## 步骤 6: 重构(改进)

```typescript
// lib/liquidity.ts - 使用常量和更好的可读性重构
const WEIGHTS = {
  VOLUME: 0.4,
  SPREAD: 0.3,
  TRADERS: 0.2,
  RECENCY: 0.1,
} as const

const SCALE_FACTORS = {
  VOLUME: 1000,
  SPREAD: 1000,
  TRADERS: 10,
  RECENCY_PENALTY: 10,
} as const

function clamp(value: number, min: number, max: number): number {
  return Math.min(Math.max(value, min), max)
}

export function calculateLiquidityScore(market: MarketData): number {
  if (market.totalVolume === 0) return 0

  const volumeScore = Math.min(market.totalVolume / SCALE_FACTORS.VOLUME, 100)
  const spreadScore = clamp(100 - (market.bidAskSpread * SCALE_FACTORS.SPREAD), 0, 100)
  const traderScore = Math.min(market.activeTraders / SCALE_FACTORS.TRADERS, 100)

  const hoursSinceLastTrade = (Date.now() - market.lastTradeTime.getTime()) / (1000 * 60 * 60)
  const recencyScore = clamp(100 - (hoursSinceLastTrade * SCALE_FACTORS.RECENCY_PENALTY), 0, 100)

  const weightedScore =
    volumeScore * WEIGHTS.VOLUME +
    spreadScore * WEIGHTS.SPREAD +
    traderScore * WEIGHTS.TRADERS +
    recencyScore * WEIGHTS.RECENCY

  return clamp(weightedScore, 0, 100)
}
```

## 步骤 7: 验证测试仍然通过

```bash
npm test lib/liquidity.test.ts

PASS lib/liquidity.test.ts
  ✓ should return high score for liquid market (3 ms)
  ✓ should return low score for illiquid market (2 ms)
  ✓ should handle edge case: zero volume (1 ms)

3 tests passed
```

✅ 重构完成,测试仍然通过!

## 步骤 8: 检查覆盖率

```bash
npm test -- --coverage lib/liquidity.test.ts

File           | % Stmts | % Branch | % Funcs | % Lines
---------------|---------|----------|---------|--------
liquidity.ts   |   100   |   100    |   100   |   100

覆盖率: 100% ✅ (目标: 80%)
```

✅ TDD 会话完成!
```

## TDD 最佳实践

**应该:**
- ✅ 首先编写测试,在任何实现之前
- ✅ 运行测试并在实现前验证它们失败
- ✅ 编写最小代码以使测试通过
- ✅ 仅在测试变绿后才重构
- ✅ 添加边界情况和错误场景
- ✅ 目标 80%+ 覆盖率(关键代码 100%)

**不应该:**
- ❌ 在测试之前编写实现
- ❌ 跳过每次更改后运行测试
- ❌ 一次编写太多代码
- ❌ 忽略失败的测试
- ❌ 测试实现细节(测试行为)
- ❌ 模拟所有内容(更喜欢集成测试)

## 要包含的测试类型

**单元测试**(函数级):
- 正常路径场景
- 边界情况(空、null、最大值)
- 错误条件
- 边界值

**集成测试**(组件级):
- API 端点
- 数据库操作
- 外部服务调用
- 带有 hooks 的 React 组件

**E2E 测试**(使用 `/e2e` 命令):
- 关键用户流程
- 多步骤流程
- 全栈集成

## 覆盖率要求

- **所有代码最低 80%**
- **100% 要求**用于:
  - 财务计算
  - 身份验证逻辑
  - 安全关键代码
  - 核心业务逻辑

## 重要说明

**强制**: 测试必须在实现之前编写。TDD 循环是:

1. **红色** - 编写失败的测试
2. **绿色** - 实现以通过
3. **重构** - 改进代码

永远不要跳过红色阶段。永远不要在测试之前编写代码。

## 与其他命令的集成

- 首先使用 `/plan` 了解要构建什么
- 使用 `/tdd` 通过测试实现
- 如果出现构建错误,使用 `/build-and-fix`
- 使用 `/code-review` 审查实现
- 使用 `/test-coverage` 验证覆盖率

## 相关代理

此命令调用位于以下位置的 `tdd-guide` 代理:
`~/.claude/agents/tdd-guide.md`

并且可以参考位于以下位置的 `tdd-workflow` 技能:
`~/.claude/skills/tdd-workflow/`
