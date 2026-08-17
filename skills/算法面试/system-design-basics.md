# System Design Basics — 系统设计基础

> **When to Use / 使用场景**: 准备系统设计面试，从零掌握可扩展系统的核心概念。

---

## Key Concepts / 核心概念

### 系统设计面试概览

| 方面 | 说明 |
|------|------|
| 时长 | 45-60 分钟 |
| 考察能力 | 架构设计、权衡取舍、沟通能力 |
| 适合级别 | Mid-Senior+ 工程师 |
| 评估维度 | 可扩展性、可靠性、可用性、效率 |

### 核心概念速查表

| 概念 | 定义 | 关键指标 |
|------|------|---------|
| Scalability 可扩展性 | 系统处理增长负载的能力 | QPS, TPS |
| Availability 可用性 | 系统正常运行时间的百分比 | 99.9% = 8.76h/年 downtime |
| Reliability 可靠性 | 系统在给定条件下正确运行的概率 | MTBF, MTTR |
| Latency 延迟 | 请求到响应的时间 | p50, p95, p99 |
| Throughput 吞吐量 | 单位时间处理的请求数 | QPS, TPS |
| Consistency 一致性 | 所有节点看到相同数据 | Strong, Eventual |

---

## Step-by-Step Framework / 系统设计答题框架

### 4 步框架

```
Step 1: 需求澄清 (5 min)
    ↓
Step 2: 高层设计 (10 min)
    ↓
Step 3: 深入设计 (20 min)
    ↓
Step 4: 权衡与扩展 (10 min)
```

---

### Step 1: 需求澄清 (Requirements Clarification)

#### 功能性需求 (Functional Requirements)

```
□ 用户能做什么？
□ 核心功能是什么？
□ 数据模型是什么？
□ API 是什么样的？
```

#### 非功能性需求 (Non-Functional Requirements)

```
□ 日活用户量 (DAU)?
□ 峰值 QPS?
□ 数据量级？
□ 延迟要求？
□ 一致性 vs 可用性？
□ 读写比例？
```

#### 话术模板

```
"在开始设计之前，我想确认几个问题：
1. 我们需要支持多少日活用户？
2. 峰值 QPS 大约是多少？
3. 数据需要存储多长时间？
4. 一致性要求是什么？最终一致还是强一致？
5. 是否需要考虑国际化/多地区部署？"
```

---

### Step 2: 高层设计 (High-Level Design)

#### 标准架构模板

```
Client (客户端)
    │
    ↓
CDN (内容分发网络)
    │
    ↓
Load Balancer (负载均衡)
    │
    ├── Web Server 1
    ├── Web Server 2
    └── Web Server N
         │
    ┌────┴────┐
    ↓         ↓
Cache     Application
(Redis)    Server
    │         │
    ↓         ↓
Database   Message Queue
(Primary)  (Kafka)
    │         │
    ↓         ↓
Replica   Worker Nodes
```

#### 话术模板

```
"这是我的高层设计：
 - 客户端通过 CDN 访问系统
 - Load Balancer 将请求分发到多个 Web Server
 - 热点数据缓存在 Redis 中
 - 主数据库用 [SQL/NoSQL]，通过主从复制提高可用性
 - 异步任务通过消息队列处理

让我画个图..."
```

---

### Step 3: 深入设计 (Deep Dive)

根据面试官的兴趣，选择 2-3 个核心组件深入：

#### 数据库设计

```
选型决策：
├─ 需要复杂查询/事务？ → SQL (PostgreSQL, MySQL)
├─ 高写入/简单查询？ → NoSQL (Cassandra, DynamoDB)
├─ 图关系查询？ → Graph DB (Neo4j)
├─ 全文搜索？ → Elasticsearch
└─ 时序数据？ → InfluxDB, TimescaleDB
```

#### 缓存策略

```
Cache-Aside (旁路缓存):
  1. 先查缓存
  2. 缓存未命中 → 查数据库
  3. 将结果写入缓存
  4. 返回结果

缓存失效策略:
├─ TTL (Time-To-Live): 设置过期时间
├─ LRU (Least Recently Used): 淘汰最久未使用
├─ Write-Through: 写入同时更新缓存
└─ Write-Behind: 异步写入缓存

缓存穿透 (Cache Penetration):
  → 缓存和数据库都没有的数据
  → 解决: 布隆过滤器 / 缓存空值

缓存击穿 (Cache Breakdown):
  → 热点 key 过期瞬间大量请求
  → 解决: 互斥锁 / 永不过期

缓存雪崩 (Cache Avalanche):
  → 大量 key 同时过期
  → 解决: TTL 加随机偏移
```

---

### Step 4: 权衡与扩展 (Trade-offs & Scale)

#### CAP 定理

```
分布式系统最多同时满足以下三项中的两项：

C (Consistency 一致性): 所有节点同一时刻看到相同数据
A (Availability 可用性): 每个请求都能收到响应
P (Partition Tolerance 分区容错): 网络分区时系统仍能运行

实际选择：
├─ CP: 放弃可用性 → 银行系统 (ZooKeeper, HBase)
├─ AP: 放弃一致性 → 社交媒体 (Cassandra, DynamoDB)
└─ CA: 不存在（网络分区不可避免）
```

#### 扩展策略

```
垂直扩展 (Scale Up):
  - 增加单机资源 (CPU, RAM, SSD)
  - 简单但有上限

水平扩展 (Scale Out):
  - 增加更多机器
  - 需要负载均衡 + 数据分片
  - 几乎无限扩展

数据库扩展:
├─ 读写分离: Primary 写, Replica 读
├─ 分库分表 (Sharding): 按 user_id 分片
└─ 引入缓存: 减少数据库压力
```

---

## Templates / 设计模板

### 估算法模板 (Back-of-the-Envelope Estimation)

```
已知条件:
- DAU = 10M (一千万日活)
- 每用户每天 10 次操作
- 读写比 = 100:1

计算:
- 日请求总量 = 10M × 10 = 100M 请求/天
- QPS = 100M / 86400 ≈ 1160 QPS
- 峰值 QPS = 2 × QPS ≈ 2320 QPS
- 写 QPS = 1160 / 101 ≈ 11.5 QPS
- 读 QPS = 1160 - 11.5 ≈ 1148 QPS

存储估算:
- 每条记录 1KB
- 日新增数据 = 100M × 1KB = 100GB/天
- 3 年数据 = 100GB × 365 × 3 ≈ 110TB
```

### 系统设计回答模板

```
═══════════════════════════════════════════
  需求澄清 (5 min)
═══════════════════════════════════════════
- 功能需求: [列出 2-3 个核心功能]
- 非功能需求: [DAU, QPS, 延迟, 一致性]
- 估算: [存储, 带宽, QPS]

═══════════════════════════════════════════
  高层设计 (10 min)
═══════════════════════════════════════════
[画出架构图]
[列出核心组件及其职责]
[定义 API 接口]

═══════════════════════════════════════════
  深入设计 (20 min)
═══════════════════════════════════════════
[选 2-3 个核心组件深入]
[数据库 schema]
[缓存策略]
[消息队列设计]

═══════════════════════════════════════════
  权衡与扩展 (10 min)
═══════════════════════════════════════════
[讨论 trade-offs]
[瓶颈和解决方案]
[监控和报警]
[未来扩展方向]
```

---

## 负载均衡策略

```
Round Robin 轮询:
  - 请求依次分配到每台服务器
  - 简单，不考虑服务器负载

Weighted Round Robin 加权轮询:
  - 根据服务器性能分配权重
  - 适合异构服务器

Least Connections 最少连接:
  - 分配给当前连接数最少的服务器
  - 适合长连接场景

IP Hash IP 哈希:
  - 根据客户端 IP 分配到固定服务器
  - 适合需要会话保持的场景

Consistent Hashing 一致性哈希:
  - 缓存和分片的标准方案
  - 节点增减时只影响少量数据
```

---

## Common Mistakes / 常见错误

1. **一上来就画图**: 先问清楚需求，否则方向跑偏
2. **只画图不解释**: 每个组件都说清楚为什么需要
3. **忽略非功能需求**: QPS、延迟、一致性是必考的
4. **过度设计**: 面试不是生产环境，不要加不需要的组件
5. **不做估算**: 估算 QPS 和存储量决定了架构选择
6. **忽略故障场景**: 主动讨论 "如果 X 挂了怎么办"
7. **照搬真实系统**: 面试有时间限制，聚焦核心组件

---

## Pro Tips / 高手技巧

- **先说需求再画图**: 每 5 分钟 check 一次面试官的反馈
- **从简单开始**: 先画单机版，再逐步加分布式组件
- **画图时自言自语**: 让面试官了解你的思考过程
- **主动讨论 trade-off**: "我选择 X 而不是 Y，因为..."
- **准备 2-3 个设计案例**: URL shortener, Chat system, News feed
- **估算要快**: 不需要精确到个位数，量级正确就行
- **关注数据流**: 数据从哪来、怎么处理、存到哪里
- **提及监控**: Prometheus + Grafana, ELK, 分布式追踪

---

## Practice Questions / 练习题

### 经典系统设计题

| 难度 | 题目 | 核心考点 |
|------|------|---------|
| ★★☆ | Design a URL Shortener | Hash, DB, Cache |
| ★★☆ | Design a Pastebin | Object Storage, TTL |
| ★★★ | Design Instagram/Twitter | Feed, Fan-out, CDN |
| ★★★ | Design a Chat System | WebSocket, Message Queue |
| ★★★ | Design a News Feed | Ranking, Push/Pull |
| ★★★ | Design YouTube | Video Processing, CDN |
| ★★★★ | Design Google Search | Crawling, Indexing, Ranking |
| ★★★★ | Design a Distributed Cache | Consistent Hashing, Eviction |
| ★★★★ | Design Uber/Lyft | Geospatial, Real-time Matching |

---

> **系统设计没有标准答案。** 面试官考察的是你的思考过程和权衡能力。清晰地表达你的设计思路，解释为什么做出某个选择，以及这个选择的 trade-off 是什么。
>
> There is no "right answer" in system design. The interviewer evaluates your thought process and ability to make trade-offs. Clearly articulate WHY you make each choice and WHAT the trade-offs are.
