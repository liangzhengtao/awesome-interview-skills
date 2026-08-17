# API Design Interview — API 设计面试

> **When to Use / 使用场景**: API 设计面试，需要设计清晰、可扩展、易用的 API 接口。

---

## Key Concepts / 核心概念

### API 风格对比

| 特性 | REST | GraphQL | gRPC |
|------|------|---------|------|
| 协议 | HTTP/1.1 | HTTP | HTTP/2 |
| 数据格式 | JSON | JSON | Protobuf (binary) |
| 类型系统 | 无 | Schema | Proto 文件 |
| Over-fetching | 常见 | 无 | 无 |
| Under-fetching | 常见 | 无 | 无 |
| 缓存 | 容易 | 困难 | 困难 |
| 学习曲线 | 低 | 中 | 中 |
| 适用场景 | 公开 API | 复杂前端 | 内部微服务 |

### RESTful 设计原则

```
1. 资源导向 (Resource-Oriented)
   - 每个 URL 代表一个资源
   - 使用名词而非动词
   ✓ /users/123
   ✗ /getUser?id=123

2. HTTP 方法语义化
   - GET    → 读取资源
   - POST   → 创建资源
   - PUT    → 替换资源（全量更新）
   - PATCH  → 部分更新
   - DELETE → 删除资源

3. 状态码标准化
   200 OK, 201 Created, 204 No Content
   400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found
   409 Conflict, 429 Too Many Requests
   500 Internal Server Error, 503 Service Unavailable

4. 无状态 (Stateless)
   - 每个请求包含所有必要信息
   - 不在服务器存储客户端状态
```

---

## Step-by-Step Framework / 分步框架

### API 设计 4 步法

```
Step 1: 识别资源 (Resources)
    ↓
Step 2: 定义端点 (Endpoints)
    ↓
Step 3: 设计数据模型 (Data Model)
    ↓
Step 4: 处理非功能需求 (NFRs)
```

---

### Step 1: 识别资源

```
从需求中提取名词 → 这就是你的资源

例: 设计 Twitter API
  - 用户 (User)
  - 推文 (Tweet)
  - 关注关系 (Follow)
  - 点赞 (Like)
  - 回复 (Reply)
  - 转发 (Retweet)
```

### Step 2: 定义端点

#### RESTful URL 设计规范

```
资源列表:
  GET    /api/v1/users           → 获取用户列表
  POST   /api/v1/users           → 创建用户

单个资源:
  GET    /api/v1/users/{id}      → 获取用户详情
  PUT    /api/v1/users/{id}      → 替换用户
  PATCH  /api/v1/users/{id}      → 部分更新用户
  DELETE /api/v1/users/{id}      → 删除用户

子资源:
  GET    /api/v1/users/{id}/tweets  → 获取用户的推文
  POST   /api/v1/users/{id}/tweets  → 用户发布推文

嵌套资源 (最多两层):
  ✓ /api/v1/users/123/tweets
  ✗ /api/v1/users/123/tweets/456/likes/789
  → 改为: GET /api/v1/likes/789

操作 (非 CRUD):
  POST   /api/v1/tweets/{id}/like     → 点赞
  POST   /api/v1/tweets/{id}/retweet  → 转发
  POST   /api/v1/auth/login           → 登录
  POST   /api/v1/auth/logout          → 登出
```

#### 查询参数规范

```
过滤 (Filter):
  GET /api/v1/tweets?user_id=123&language=en

排序 (Sort):
  GET /api/v1/tweets?sort=-created_at
  GET /api/v1/tweets?sort=like_count,-created_at
  (- 前缀表示降序)

分页 (Pagination):
  # 偏移分页
  GET /api/v1/tweets?offset=0&limit=20

  # 游标分页 (推荐)
  GET /api/v1/tweets?cursor=eyJpZCI6MTIzfQ&limit=20
  响应: { "data": [...], "next_cursor": "...", "has_more": true }

字段选择 (Field Selection):
  GET /api/v1/users/123?fields=id,name,email
```

---

### Step 3: 设计数据模型

#### 请求/响应模板

```json
// 创建资源 - 请求
POST /api/v1/tweets
{
  "content": "Hello, world!",
  "media_ids": ["media_123"],
  "reply_to": null
}

// 成功响应 - 201 Created
{
  "data": {
    "id": "tweet_abc123",
    "content": "Hello, world!",
    "author": {
      "id": "user_456",
      "username": "john"
    },
    "like_count": 0,
    "retweet_count": 0,
    "created_at": "2024-01-15T10:30:00Z"
  },
  "meta": {
    "request_id": "req_xyz789"
  }
}

// 错误响应 - 400 Bad Request
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request body",
    "details": [
      {
        "field": "content",
        "issue": "Content exceeds 280 character limit"
      }
    ]
  },
  "meta": {
    "request_id": "req_xyz789"
  }
}

// 列表响应
{
  "data": [...],
  "pagination": {
    "total": 1500,
    "limit": 20,
    "offset": 0,
    "has_more": true
  }
}
```

---

### Step 4: 处理非功能需求

#### 认证与授权

```
认证方式:
├─ API Key: 简单，适合服务端调用
├─ OAuth 2.0: 标准，适合第三方应用
├─ JWT: 无状态，适合微服务
└─ Session: 有状态，适合传统 Web

最佳实践:
  Authorization: Bearer <token>
  X-API-Key: <api-key>

API Key vs OAuth 2.0:
  - 内部服务: API Key 或 mTLS
  - 第三方开发者: OAuth 2.0 + API Key
  - 用户级权限: OAuth 2.0 + JWT
```

#### 限流 (Rate Limiting)

```
算法:
├─ Token Bucket: 允许突发流量
│   - 桶容量: 100 tokens
│   - 填充速率: 10 tokens/sec
│   - 每个请求消耗 1 token
│
├─ Sliding Window: 更平滑
│   - 窗口: 1 分钟
│   - 限制: 100 请求/窗口
│
└─ Fixed Window: 简单
    - 每分钟重置计数

响应头:
  X-RateLimit-Limit: 100
  X-RateLimit-Remaining: 95
  X-RateLimit-Reset: 1705312800

超限响应: 429 Too Many Requests
  Retry-After: 60
```

#### 版本控制

```
方式 1: URL 路径 (推荐)
  GET /api/v1/users
  GET /api/v2/users

方式 2: 请求头
  Accept: application/vnd.myapp.v2+json

方式 3: 查询参数
  GET /api/users?version=2

版本策略:
  - 保持向后兼容
  - 新功能添加字段（不删不改）
  - Breaking change → 新版本
  - 至少维护最近 2 个版本
  - 设置弃用时间表和通知
```

---

## Templates / 设计模板

### 完整 API 设计模板: 设计 Twitter

```
═══════════════════════════════════════════
  1. 识别资源
═══════════════════════════════════════════
- User (用户)
- Tweet (推文)
- Follow (关注)
- Like (点赞)

═══════════════════════════════════════════
  2. 定义端点
═══════════════════════════════════════════

用户:
  POST   /api/v1/users                注册
  GET    /api/v1/users/{id}           获取用户
  PATCH  /api/v1/users/{id}           更新用户
  GET    /api/v1/users/{id}/followers 获取粉丝
  GET    /api/v1/users/{id}/following 获取关注

推文:
  POST   /api/v1/tweets               发推文
  GET    /api/v1/tweets/{id}          获取推文
  DELETE /api/v1/tweets/{id}          删除推文
  GET    /api/v1/tweets/{id}/replies  获取回复

互动:
  POST   /api/v1/tweets/{id}/like     点赞
  DELETE /api/v1/tweets/{id}/like     取消点赞
  POST   /api/v1/tweets/{id}/retweet  转发
  DELETE /api/v1/tweets/{id}/retweet  取消转发

时间线:
  GET    /api/v1/feed                 获取信息流
  GET    /api/v1/search/tweets        搜索推文

═══════════════════════════════════════════
  3. 数据模型
═══════════════════════════════════════════

User:
  - id: string
  - username: string
  - display_name: string
  - bio: string
  - avatar_url: string
  - follower_count: integer
  - following_count: integer
  - created_at: datetime

Tweet:
  - id: string
  - content: string (max 280 chars)
  - author: User (embedded)
  - media: Media[]
  - like_count: integer
  - retweet_count: integer
  - reply_count: integer
  - reply_to: string | null
  - created_at: datetime

═══════════════════════════════════════════
  4. 非功能需求
═══════════════════════════════════════════

认证: OAuth 2.0 + JWT
限流: 1000 请求/小时 (普通), 10000 (Pro)
版本: /api/v1, /api/v2
分页: 游标分页
缓存: ETag + Cache-Control
```

### 端点规格模板

```yaml
# OpenAPI 3.0 格式
paths:
  /api/v1/tweets:
    post:
      summary: 发布推文
      tags: [Tweets]
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required: [content]
              properties:
                content:
                  type: string
                  maxLength: 280
                media_ids:
                  type: array
                  items:
                    type: string
                  maxItems: 4
                reply_to:
                  type: string
                  nullable: true
      responses:
        '201':
          description: 创建成功
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Tweet'
        '400':
          description: 请求无效
        '401':
          description: 未认证
        '429':
          description: 限流
```

---

## Common Mistakes / 常见错误

1. **URL 使用动词**: `POST /api/createUser` → 应该是 `POST /api/v1/users`
2. **忽略版本控制**: API 一旦发布就有消费者，必须考虑向后兼容
3. **返回不一致的错误格式**: 所有错误用统一格式
4. **不做分页**: 列表接口必须分页，否则数据量大时会崩溃
5. **忽略限流**: 没有限流的 API 容易被滥用
6. **过度嵌套**: URL 层级不超过 2 层
7. **不考虑幂等性**: PUT/DELETE 应该幂等，POST 创建用 idempotency key
8. **返回 200 + 错误信息**: 用正确的 HTTP 状态码

---

## Pro Tips / 高手技巧

- **先设计消费者需要什么**: 从客户端需求反推 API
- **用 OpenAPI/Swagger 规范**: 面试中画表或写 YAML
- **提及 HATEOAS**: 高级 REST 概念，加印象分
- **讨论长轮询 vs WebSocket vs SSE**: 实时通信方案
- **提及 API Gateway**: Kong, AWS API Gateway 的作用
- **考虑批量操作**: `POST /api/v1/tweets/batch` 减少请求数
- **GraphQL 的 N+1 问题**: 用 DataLoader 解决

---

## Practice Questions / 练习题

| 题目 | 核心考点 |
|------|---------|
| Design Twitter API | CRUD, 时间线, 关注关系 |
| Design a Payment API | 幂等性, 状态机, 安全性 |
| Design an E-commerce API | 订单流程, 库存, 并发 |
| Design a File Storage API | 分片上传, 大文件, CDN |
| Design a Calendar API | 重复事件, 时区, 提醒 |
| Design a Maps API | 地理查询, 路径规划, 缓存 |

---

> **API 是系统的契约。** 好的 API 设计考虑了消费者的使用体验、系统的可维护性和未来的可扩展性。在面试中展示你理解这些维度。
>
> An API is a contract. Good API design considers consumer experience, system maintainability, and future extensibility. Show the interviewer you understand all these dimensions.
