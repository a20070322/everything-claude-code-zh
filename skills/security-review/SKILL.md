---
name: security-review
description: 在添加身份验证、处理用户输入、使用密钥、创建 API 端点或实现支付/敏感功能时使用此技能。提供全面的安全检查清单和模式。
---

# 安全审查技能

此技能确保所有代码遵循安全最佳实践并识别潜在漏洞。

## 何时激活

- 实现身份验证或授权
- 处理用户输入或文件上传
- 创建新的 API 端点
- 使用密钥或凭证
- 实现支付功能
- 存储或传输敏感数据
- 集成第三方 API

## 安全检查清单

### 1. 密钥管理

#### ❌ 永远不要这样做
```typescript
const apiKey = "sk-proj-xxxxx"  // 硬编码的密钥
const dbPassword = "password123" // 在源代码中
```

#### ✅ 始终这样做
```typescript
const apiKey = process.env.OPENAI_API_KEY
const dbUrl = process.env.DATABASE_URL

// 验证密钥是否存在
if (!apiKey) {
  throw new Error('OPENAI_API_KEY not configured')
}
```

#### 验证步骤
- [ ] 没有硬编码的 API 密钥、令牌或密码
- [ ] 所有密钥都在环境变量中
- [ ] `.env.local` 在 .gitignore 中
- [ ] git 历史记录中没有密钥
- [ ] 生产环境密钥在托管平台（Vercel、Railway）

### 2. 输入验证

#### 始终验证用户输入
```typescript
import { z } from 'zod'

// 定义验证模式
const CreateUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(100),
  age: z.number().int().min(0).max(150)
})

// 在处理前验证
export async function createUser(input: unknown) {
  try {
    const validated = CreateUserSchema.parse(input)
    return await db.users.create(validated)
  } catch (error) {
    if (error instanceof z.ZodError) {
      return { success: false, errors: error.errors }
    }
    throw error
  }
}
```

#### 文件上传验证
```typescript
function validateFileUpload(file: File) {
  // 大小检查（最大 5MB）
  const maxSize = 5 * 1024 * 1024
  if (file.size > maxSize) {
    throw new Error('文件过大（最大 5MB）')
  }

  // 类型检查
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif']
  if (!allowedTypes.includes(file.type)) {
    throw new Error('无效的文件类型')
  }

  // 扩展名检查
  const allowedExtensions = ['.jpg', '.jpeg', '.png', '.gif']
  const extension = file.name.toLowerCase().match(/\.[^.]+$/)?.[0]
  if (!extension || !allowedExtensions.includes(extension)) {
    throw new Error('无效的文件扩展名')
  }

  return true
}
```

#### 验证步骤
- [ ] 所有用户输入都使用模式验证
- [ ] 文件上传受限（大小、类型、扩展名）
- [ ] 查询中没有直接使用用户输入
- [ ] 白名单验证（而非黑名单）
- [ ] 错误消息不泄露敏感信息

### 3. SQL 注入预防

#### ❌ 永远不要拼接 SQL
```typescript
// 危险 - SQL 注入漏洞
const query = `SELECT * FROM users WHERE email = '${userEmail}'`
await db.query(query)
```

#### ✅ 始终使用参数化查询
```typescript
// 安全 - 参数化查询
const { data } = await supabase
  .from('users')
  .select('*')
  .eq('email', userEmail)

// 或使用原始 SQL
await db.query(
  'SELECT * FROM users WHERE email = $1',
  [userEmail]
)
```

#### 验证步骤
- [ ] 所有数据库查询都使用参数化查询
- [ ] SQL 中没有字符串拼接
- [ ] 正确使用 ORM/查询构建器
- [ ] Supabase 查询已正确清理

### 4. 身份验证与授权

#### JWT Token 处理
```typescript
// ❌ 错误：localStorage（易受 XSS 攻击）
localStorage.setItem('token', token)

// ✅ 正确：httpOnly cookies
res.setHeader('Set-Cookie',
  `token=${token}; HttpOnly; Secure; SameSite=Strict; Max-Age=3600`)
```

#### 授权检查
```typescript
export async function deleteUser(userId: string, requesterId: string) {
  // 始终先验证授权
  const requester = await db.users.findUnique({
    where: { id: requesterId }
  })

  if (requester.role !== 'admin') {
    return NextResponse.json(
      { error: 'Unauthorized' },
      { status: 403 }
    )
  }

  // 继续删除
  await db.users.delete({ where: { id: userId } })
}
```

#### 行级安全（Supabase）
```sql
-- 在所有表上启用 RLS
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

-- 用户只能查看自己的数据
CREATE POLICY "Users view own data"
  ON users FOR SELECT
  USING (auth.uid() = id);

-- 用户只能更新自己的数据
CREATE POLICY "Users update own data"
  ON users FOR UPDATE
  USING (auth.uid() = id);
```

#### 验证步骤
- [ ] Token 存储在 httpOnly cookies 中（而非 localStorage）
- [ ] 敏感操作前的授权检查
- [ ] Supabase 中启用行级安全
- [ ] 实现基于角色的访问控制
- [ ] 会话管理安全

### 5. XSS 预防

#### 清理 HTML
```typescript
import DOMPurify from 'isomorphic-dompurify'

// 始终清理用户提供的 HTML
function renderUserContent(html: string) {
  const clean = DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'p'],
    ALLOWED_ATTR: []
  })
  return <div dangerouslySetInnerHTML={{ __html: clean }} />
}
```

#### 内容安全策略
```typescript
// next.config.js
const securityHeaders = [
  {
    key: 'Content-Security-Policy',
    value: `
      default-src 'self';
      script-src 'self' 'unsafe-eval' 'unsafe-inline';
      style-src 'self' 'unsafe-inline';
      img-src 'self' data: https:;
      font-src 'self';
      connect-src 'self' https://api.example.com;
    `.replace(/\s{2,}/g, ' ').trim()
  }
]
```

#### 验证步骤
- [ ] 用户提供的 HTML 已清理
- [ ] CSP 头已配置
- [ ] 没有未经验证的动态内容渲染
- [ ] 使用 React 内置的 XSS 保护

### 6. CSRF 保护

#### CSRF Token
```typescript
import { csrf } from '@/lib/csrf'

export async function POST(request: Request) {
  const token = request.headers.get('X-CSRF-Token')

  if (!csrf.verify(token)) {
    return NextResponse.json(
      { error: 'Invalid CSRF token' },
      { status: 403 }
    )
  }

  // 处理请求
}
```

#### SameSite Cookies
```typescript
res.setHeader('Set-Cookie',
  `session=${sessionId}; HttpOnly; Secure; SameSite=Strict`)
```

#### 验证步骤
- [ ] 状态更改操作上有 CSRF token
- [ ] 所有 cookies 设置 SameSite=Strict
- [ ] 实现双提交 cookie 模式

### 7. 速率限制

#### API 速率限制
```typescript
import rateLimit from 'express-rate-limit'

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 分钟
  max: 100, // 每个窗口 100 次请求
  message: 'Too many requests'
})

// 应用到路由
app.use('/api/', limiter)
```

#### 昂贵操作的严格限制
```typescript
// 搜索的严格速率限制
const searchLimiter = rateLimit({
  windowMs: 60 * 1000, // 1 分钟
  max: 10, // 每分钟 10 次请求
  message: 'Too many search requests'
})

app.use('/api/search', searchLimiter)
```

#### 验证步骤
- [ ] 所有 API 端点都有速率限制
- [ ] 昂贵操作有更严格的限制
- [ ] 基于 IP 的速率限制
- [ ] 基于用户的速率限制（已认证）

### 8. 敏感数据暴露

#### 日志记录
```typescript
// ❌ 错误：记录敏感数据
console.log('User login:', { email, password })
console.log('Payment:', { cardNumber, cvv })

// ✅ 正确：编辑敏感数据
console.log('User login:', { email, userId })
console.log('Payment:', { last4: card.last4, userId })
```

#### 错误消息
```typescript
// ❌ 错误：暴露内部细节
catch (error) {
  return NextResponse.json(
    { error: error.message, stack: error.stack },
    { status: 500 }
  )
}

// ✅ 正确：通用错误消息
catch (error) {
  console.error('Internal error:', error)
  return NextResponse.json(
    { error: 'An error occurred. Please try again.' },
    { status: 500 }
  )
}
```

#### 验证步骤
- [ ] 日志中没有密码、令牌或密钥
- [ ] 用户看到的错误消息是通用的
- [ ] 详细错误仅出现在服务器日志中
- [ ] 不向用户暴露堆栈跟踪

### 9. 区块链安全（Solana）

#### 钱包验证
```typescript
import { verify } from '@solana/web3.js'

async function verifyWalletOwnership(
  publicKey: string,
  signature: string,
  message: string
) {
  try {
    const isValid = verify(
      Buffer.from(message),
      Buffer.from(signature, 'base64'),
      Buffer.from(publicKey, 'base64')
    )
    return isValid
  } catch (error) {
    return false
  }
}
```

#### 交易验证
```typescript
async function verifyTransaction(transaction: Transaction) {
  // 验证接收者
  if (transaction.to !== expectedRecipient) {
    throw new Error('Invalid recipient')
  }

  // 验证金额
  if (transaction.amount > maxAmount) {
    throw new Error('Amount exceeds limit')
  }

  // 验证用户有足够的余额
  const balance = await getBalance(transaction.from)
  if (balance < transaction.amount) {
    throw new Error('Insufficient balance')
  }

  return true
}
```

#### 验证步骤
- [ ] 钱包签名已验证
- [ ] 交易详情已验证
- [ ] 交易前的余额检查
- [ ] 没有盲目的交易签名

### 10. 依赖安全

#### 定期更新
```bash
# 检查漏洞
npm audit

# 自动修复可修复的问题
npm audit fix

# 更新依赖
npm update

# 检查过时的包
npm outdated
```

#### 锁文件
```bash
# 始终提交锁文件
git add package-lock.json

# 在 CI/CD 中使用以实现可重现构建
npm ci  # 而不是 npm install
```

#### 验证步骤
- [ ] 依赖项是最新的
- [ ] 没有已知漏洞（npm audit clean）
- [ ] 锁文件已提交
- [ ] GitHub 上启用 Dependabot
- [ ] 定期安全更新

## 安全测试

### 自动化安全测试
```typescript
// 测试身份验证
test('requires authentication', async () => {
  const response = await fetch('/api/protected')
  expect(response.status).toBe(401)
})

// 测试授权
test('requires admin role', async () => {
  const response = await fetch('/api/admin', {
    headers: { Authorization: `Bearer ${userToken}` }
  })
  expect(response.status).toBe(403)
})

// 测试输入验证
test('rejects invalid input', async () => {
  const response = await fetch('/api/users', {
    method: 'POST',
    body: JSON.stringify({ email: 'not-an-email' })
  })
  expect(response.status).toBe(400)
})

// 测试速率限制
test('enforces rate limits', async () => {
  const requests = Array(101).fill(null).map(() =>
    fetch('/api/endpoint')
  )

  const responses = await Promise.all(requests)
  const tooManyRequests = responses.filter(r => r.status === 429)

  expect(tooManyRequests.length).toBeGreaterThan(0)
})
```

## 部署前安全检查清单

在任何生产部署之前：

- [ ] **密钥**：没有硬编码密钥，所有都在环境变量中
- [ ] **输入验证**：所有用户输入都经过验证
- [ ] **SQL 注入**：所有查询都是参数化的
- [ ] **XSS**：用户内容已清理
- [ ] **CSRF**：保护已启用
- [ ] **身份验证**：正确的 token 处理
- [ ] **授权**：角色检查已到位
- [ ] **速率限制**：所有端点都已启用
- [ ] **HTTPS**：生产环境中强制执行
- [ ] **安全头**：CSP、X-Frame-Options 已配置
- [ ] **错误处理**：错误中没有敏感数据
- [ ] **日志记录**：没有记录敏感数据
- [ ] **依赖项**：最新，没有漏洞
- [ ] **行级安全**：Supabase 中已启用
- [ ] **CORS**：正确配置
- [ ] **文件上传**：已验证（大小、类型）
- [ ] **钱包签名**：已验证（如果是区块链）

## 资源

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Next.js 安全](https://nextjs.org/docs/security)
- [Supabase 安全](https://supabase.com/docs/guides/auth)
- [Web Security Academy](https://portswigger.net/web-security)

---

**记住**：安全不是可选的。一个漏洞可能会危及整个平台。如有疑问，宁可谨慎。
