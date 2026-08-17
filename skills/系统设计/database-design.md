# Database Design Interview — 数据库设计面试

> **When to Use / 使用场景**: 数据库 Schema 设计面试，包括选型、建模、索引、分片等。

---

## Key Concepts / 核心概念

### SQL vs NoSQL 选型

| 维度 | SQL (关系型) | NoSQL (非关系型) |
|------|-------------|-----------------|
| 数据结构 | 固定 Schema | 灵活 Schema |
| 扩展方式 | 垂直扩展为主 | 水平扩展为主 |
| 事务 | ACID | BASE (通常) |
| 一致性 | 强一致性 | 最终一致性 (通常) |
| 查询 | SQL, 复杂 JOIN | 简单查询为主 |
| 适用场景 | 复杂关系, 事务 | 高写入, 灵活 Schema |

### 选型决策树

```
数据是否有固定结构？
├─ 是 → 需要复杂查询/JOIN？
│       ├─ 是 → SQL (PostgreSQL, MySQL)
│       └─ 否 → 读多写少？
│               ├─ 是 → 文档型 (MongoDB)
│               └─ 否 → 列族型 (Cassandra)
│
└─ 否 → 数据是键值对？
        ├─ 是 → KV Store (Redis, DynamoDB)
        └─ 否 → 数据有复杂关系？
                ├─ 是 → 图数据库 (Neo4j)
                └─ 否 → 文档型 (MongoDB)
```

---

## Step-by-Step Framework / 分步框架

### 数据库设计 6 步法

```
Step 1: 需求分析
    ↓ 识别实体、属性、关系
Step 2: 概念设计
    ↓ ER 图
Step 3: 逻辑设计
    ↓ Schema 定义
Step 4: 物理设计
    ↓ 索引、分区
Step 5: 优化
    ↓ 反范式化、缓存
Step 6: 扩展
    ↓ 分片、复制
```

---

### Step 1–2: 需求分析与概念设计

#### 实体识别

```
需求: 设计一个电商系统

实体:
  - User (用户)
  - Product (商品)
  - Category (分类)
  - Order (订单)
  - OrderItem (订单项)
  - Review (评价)
  - Cart (购物车)
  - Payment (支付)
  - Shipping (物流)

关系:
  - User 1:N Order (一个用户多个订单)
  - Order N:N Product (通过 OrderItem)
  - Product N:1 Category
  - User 1:N Review
  - Product 1:N Review
  - User 1:1 Cart
  - Cart N:N Product (通过 CartItem)
```

#### ER 图 (文本表示)

```
┌──────────┐     1:N      ┌──────────┐
│  User    │──────────────→│  Order   │
│──────────│              │──────────│
│ id (PK)  │              │ id (PK)  │
│ email    │              │ user_id  │
│ name     │              │ status   │
│ password │              │ total    │
└──────────┘              └────┬─────┘
      │                        │ 1:N
      │ 1:N                    ↓
      ↓                  ┌──────────┐
┌──────────┐              │OrderItem │
│  Review  │              │──────────│
│──────────│              │ id (PK)  │
│ id (PK)  │              │ order_id │
│ user_id  │              │ prod_id  │
│ prod_id  │              │ quantity │
│ rating   │              │ price    │
└──────────┘              └──────────┘
```

---

### Step 3: 逻辑设计 — Schema 定义

#### SQL Schema 模板

```sql
-- 用户表
CREATE TABLE users (
    id          BIGINT PRIMARY KEY AUTO_INCREMENT,
    email       VARCHAR(255) NOT NULL UNIQUE,
    username    VARCHAR(50) NOT NULL UNIQUE,
    password    VARCHAR(255) NOT NULL,  -- bcrypt hash
    full_name   VARCHAR(100),
    avatar_url  VARCHAR(500),
    phone       VARCHAR(20),
    status      ENUM('active', 'inactive', 'banned') DEFAULT 'active',
    created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_email (email),
    INDEX idx_username (username),
    INDEX idx_status_created (status, created_at)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 商品表
CREATE TABLE products (
    id          BIGINT PRIMARY KEY AUTO_INCREMENT,
    name        VARCHAR(200) NOT NULL,
    description TEXT,
    price       DECIMAL(10, 2) NOT NULL,
    stock       INT NOT NULL DEFAULT 0,
    category_id BIGINT NOT NULL,
    images      JSON,  -- ["url1", "url2"]
    status      ENUM('active', 'inactive', 'sold_out') DEFAULT 'active',
    created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(id),
    INDEX idx_category (category_id),
    INDEX idx_price (price),
    INDEX idx_status_created (status, created_at),
    FULLTEXT INDEX ft_name_desc (name, description)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 订单表
CREATE TABLE orders (
    id              BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id         BIGINT NOT NULL,
    order_number    VARCHAR(32) NOT NULL UNIQUE,
    status          ENUM('pending', 'paid', 'shipped', 'delivered', 'cancelled')
                    DEFAULT 'pending',
    total_amount    DECIMAL(12, 2) NOT NULL,
    shipping_address JSON NOT NULL,
    payment_method  VARCHAR(50),
    paid_at         TIMESTAMP NULL,
    shipped_at      TIMESTAMP NULL,
    delivered_at    TIMESTAMP NULL,
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id),
    INDEX idx_user_created (user_id, created_at),
    INDEX idx_status (status),
    INDEX idx_order_number (order_number)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 订单项表
CREATE TABLE order_items (
    id          BIGINT PRIMARY KEY AUTO_INCREMENT,
    order_id    BIGINT NOT NULL,
    product_id  BIGINT NOT NULL,
    quantity    INT NOT NULL,
    unit_price  DECIMAL(10, 2) NOT NULL,  -- 下单时价格快照
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id),
    INDEX idx_order (order_id),
    INDEX idx_product (product_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

#### NoSQL Schema 模板 (MongoDB)

```javascript
// 用户集合
{
  _id: ObjectId,
  email: "user@example.com",
  username: "john",
  profile: {
    full_name: "John Doe",
    avatar_url: "https://...",
    phone: "+1234567890"
  },
  addresses: [
    {
      label: "Home",
      street: "123 Main St",
      city: "San Francisco",
      state: "CA",
      zip: "94102",
      country: "US"
    }
  ],
  status: "active",
  created_at: ISODate,
  updated_at: ISODate
}

// 订单集合 (嵌套订单项)
{
  _id: ObjectId,
  user_id: ObjectId,
  order_number: "ORD-20240115-001",
  items: [
    {
      product_id: ObjectId,
      name: "iPhone 15",      // 冗余：商品名快照
      unit_price: 999.00,
      quantity: 1,
      subtotal: 999.00
    }
  ],
  total: 999.00,
  status: "paid",
  shipping_address: { ... },
  payment: {
    method: "credit_card",
    transaction_id: "txn_abc123",
    paid_at: ISODate
  },
  created_at: ISODate,
  updated_at: ISODate
}
```

---

### Step 4: 物理设计 — 索引策略

#### 索引类型

```
B-Tree 索引 (默认):
  - 适合: =, <, >, <=, >=, BETWEEN, LIKE 'abc%'
  - 不适合: LIKE '%abc', 函数计算
  - 选择性 (Cardinality) 越高越好

Hash 索引:
  - 适合: 精确匹配 =
  - 不适合: 范围查询, 排序
  - 仅 Memory 引擎支持

全文索引 (Full-Text):
  - 适合: 文本搜索
  - MySQL: FULLTEXT INDEX
  - 更好的选择: Elasticsearch

复合索引:
  - 遵循最左前缀原则
  - INDEX(a, b, c) 可用于:
    ✓ WHERE a = 1
    ✓ WHERE a = 1 AND b = 2
    ✓ WHERE a = 1 AND b = 2 AND c = 3
    ✗ WHERE b = 2
    ✗ WHERE c = 3
```

#### 索引设计模板

```sql
-- 查询: 获取用户的最近订单
-- SELECT * FROM orders WHERE user_id = ? ORDER BY created_at DESC LIMIT 20
CREATE INDEX idx_user_created ON orders(user_id, created_at DESC);

-- 查询: 按状态筛选并按时间排序
-- SELECT * FROM orders WHERE status = ? ORDER BY created_at DESC
CREATE INDEX idx_status_created ON orders(status, created_at DESC);

-- 查询: 商品搜索
-- SELECT * FROM products WHERE category_id = ? AND price BETWEEN ? AND ?
CREATE INDEX idx_category_price ON products(category_id, price);

-- 覆盖索引 (Covering Index): 查询所需字段都在索引中
-- SELECT user_id, created_at, total_amount FROM orders WHERE user_id = ?
CREATE INDEX idx_user_covering ON orders(user_id, created_at, total_amount);
```

---

### Step 5: 优化 — 范式化与反范式化

#### 数据库范式

```
1NF (第一范式):
  - 每列原子性，不可再分
  ✗ "地址: 北京市海淀区" (城市和区混在一起)
  ✓ "城市: 北京市", "区: 海淀区"

2NF (第二范式):
  - 满足 1NF
  - 非主属性完全依赖于主键
  ✗ 订单表中存商品名 (只依赖 product_id, 不依赖 order_id)
  ✓ 商品名单独在 product 表

3NF (第三范式):
  - 满足 2NF
  - 非主属性不传递依赖于主键
  ✗ 用户表存城市和城市邮编 (邮编依赖城市，城市依赖用户)
  ✓ 邮编放在城市表
```

#### 反范式化 (Denormalization)

```
场景: 为了查询性能，适度冗余数据

常见策略:
1. 冗余字段
   订单表冗余 username (避免 JOIN)
   代价: 更新用户信息需要同步更新

2. 计数器缓存
   商品表冗余 review_count, avg_rating
   代价: 写入时需要更新计数

3. 预计算
   物化视图、汇总表
   代价: 数据一致性维护

4. 嵌套文档 (NoSQL)
   订单中嵌入订单项
   代价: 数据重复，更新复杂
```

---

### Step 6: 扩展 — 分片与复制

#### 数据库分片 (Sharding)

```
分片键选择原则:
  1. 高基数 (很多不同值)
  2. 查询频率高
  3. 分布均匀

常见分片策略:

User 表 - 按 user_id 分片:
  shard = hash(user_id) % num_shards
  优点: 用户数据本地化
  代价: 跨用户查询困难

Order 表 - 按 user_id 分片:
  同一用户的订单在同一分片
  优点: 用户订单查询高效
  代价: 按时间范围查询需广播

Time-series - 按时间分片:
  每月一个分片
  优点: 时间范围查询高效
  代价: 最新分片可能热点
```

#### 读写分离

```
架构:
  App → Proxy (ProxySQL/MySQL Router)
           │
     ┌─────┴─────┐
     ↓           ↓
  Primary     Replica 1, 2, 3
  (写)        (读)

策略:
  - 写操作 → Primary
  - 读操作 → Replica
  - 读己之写 (Read-your-writes):
    刚写入的数据从 Primary 读
  - 一致性读: 指定从 Primary 读
```

---

## Templates / 设计模板

### Schema 设计模板

```
═══════════════════════════════════════════
  表名: [table_name]
═══════════════════════════════════════════

用途: [这张表存储什么数据]

字段:
  - id: BIGINT PK AUTO_INCREMENT
  - [field_name]: [type] [约束]  -- [说明]
  - ...
  - created_at: TIMESTAMP DEFAULT CURRENT_TIMESTAMP
  - updated_at: TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP

索引:
  - INDEX idx_[name] ([columns])  -- [支持的查询]

外键关系:
  - [table_a].[field] → [table_b].[id]

预估数据量: [行数/天], 总量 [GB/TB]

分片策略: [不分片 / 按某字段分片]

特殊考虑:
  - [软删除? 乐观锁? 审计日志?]
```

### Trade-off 分析模板

```
═══════════════════════════════════════════
  决策: [选择 A 还是 B]
═══════════════════════════════════════════

选项 A: [描述]
  优点:
    - [优点 1]
    - [优点 2]
  缺点:
    - [缺点 1]
    - [缺点 2]

选项 B: [描述]
  优点:
    - [优点 1]
    - [优点 2]
  缺点:
    - [缺点 1]
    - [缺点 2]

我的选择: [A/B]
原因: [为什么在这个场景下选择这个方案]
```

---

## Common Mistakes / 常见错误

1. **过度范式化**: 导致大量 JOIN，查询性能差
2. **不做索引**: 全表扫描在数据量大时不可接受
3. **索引过多**: 每个索引都会降低写入性能
4. **忽略数据增长**: 1 万行和 1 亿行的设计完全不同
5. **用自增 ID 做分片键**: 导致写入热点
6. **不考虑时区**: 用 UTC 存储，展示时转换
7. **密码明文存储**: 必须 bcrypt/scrypt 哈希
8. **忽略软删除**: 物理删除导致数据不可恢复
9. **不设计审计日志**: 谁在什么时候改了什么

---

## Pro Tips / 高手技巧

- **先画 ER 图**: 在纸上画出实体和关系
- **考虑读写比例**: 读多写少 → 缓存 + 读副本；写多 → 分片 + 异步
- **提及 ACID vs BASE**: 根据场景选择一致性级别
- **讨论迁移策略**: Schema 变更如何做到零停机
- **提及 ORM**: TypeORM, SQLAlchemy 的优缺点
- **考虑数据生命周期**: 冷数据归档、TTL 过期
- **提及监控**: 慢查询日志、连接池监控

---

## Practice Questions / 练习题

| 题目 | 核心考点 |
|------|---------|
| Design a URL Shortener DB | 高并发写入, 短码生成 |
| Design Twitter Schema | 关注关系, 时间线, 热点用户 |
| Design Uber Database | 地理索引, 实时位置更新 |
| Design E-commerce Schema | 订单流程, 库存管理, 事务 |
| Design Chat System DB | 消息存储, 已读状态, 群聊 |
| Design a Ticket System | 并发控制, 座位锁定 |
| Design Analytics DB | 时序数据, 聚合查询, OLAP |

---

> **数据库设计的核心是 trade-off。** 范式化 vs 反范式化、一致性 vs 可用性、读优化 vs 写优化 — 没有银弹，只有适合场景的选择。在面试中展示你理解这些权衡。
>
> The core of database design is trade-offs. Normalization vs denormalization, consistency vs availability, read-optimized vs write-optimized — there's no silver bullet, only context-appropriate choices.
