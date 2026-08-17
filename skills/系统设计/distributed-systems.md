# Distributed Systems — 分布式系统面试

> **When to Use / 使用场景**: 高级工程师系统设计面试，深入分布式系统原理和架构模式。

---

## Key Concepts / 核心概念

### 分布式系统基础

| 概念 | 定义 | 关键点 |
|------|------|--------|
| CAP Theorem | 一致性、可用性、分区容错三选二 | CP/AP 的选择 |
| Consistency Models | 数据一致性级别 | Strong, Causal, Eventual |
| Consensus | 多节点达成一致 | Paxos, Raft |
| Partitioning | 数据分散到多个节点 | Hash, Range, Geographic |
| Replication | 数据在多节点备份 | Leader-Follower, Multi-Leader |
| Failure Modes | 系统可能的故障方式 | Crash, Network Partition, Byzantine |

### 一致性模型详解

```
强一致性 (Strong Consistency):
  - 写入后所有读取立即看到新值
  - 实现: 两阶段提交 (2PC), Paxos, Raft
  - 代价: 高延迟，低可用性
  - 适用: 银行交易、库存系统

因果一致性 (Causal Consistency):
  - 有因果关系的操作保序
  - 无因果关系的操作可以乱序
  - 实现: 向量时钟 (Vector Clocks)
  - 适用: 协作编辑、社交媒体

最终一致性 (Eventual Consistency):
  - 停止写入后，所有副本最终一致
  - 实现: 反熵 (Anti-entropy), 读修复 (Read Repair)
  - 代价: 可能读到旧数据
  - 适用: DNS、CDN、社交动态
```

---

## Step-by-Step Framework / 分步框架

### 共识算法

#### Paxos

```
角色: Proposer, Acceptor, Learner

Phase 1 (Prepare):
  Proposer → Acceptors: Prepare(n)
  Acceptors → Proposer: Promise(n, accepted_value)

Phase 2 (Accept):
  Proposer → Acceptors: Accept(n, value)
  Acceptors → Learners: Accepted(n, value)

适用: 需要强一致性的分布式存储
案例: Google Chubby, Apache ZooKeeper (ZAB 协议)
```

#### Raft (更易理解的共识算法)

```
角色: Leader, Follower, Candidate

1. Leader Election:
   - Follower 超时未收到心跳 → 变为 Candidate
   - Candidate 请求投票 → 获得多数票成为 Leader
   - 任期 (Term) 机制避免脑裂

2. Log Replication:
   - Client 请求 → Leader
   - Leader 追加日志 → 复制到 Followers
   - 多数确认 → 提交 → 响应 Client

3. Safety:
   - 选举限制: 只有包含最新日志的节点才能成为 Leader
   - 提交规则: 只有当前任期的日志才能通过计数提交

适用: etcd, CockroachDB, Consul
```

---

### 微服务架构模式

#### 服务间通信

```
同步通信:
├─ REST API (HTTP/JSON)
│   - 简单、通用
│   - 请求-响应模式
│   - 适合: 查询操作
│
├─ gRPC (HTTP/2 + Protobuf)
│   - 高性能、强类型
│   - 支持双向流
│   - 适合: 内部服务通信
│
└─ GraphQL
    - 客户端按需查询
    - 减少 over-fetching
    - 适合: 前端 BFF

异步通信:
├─ Message Queue (消息队列)
│   - Kafka: 高吞吐、持久化、流处理
│   - RabbitMQ: 灵活路由、多协议
│   - SQS: 托管服务、简单
│
├─ Event Sourcing (事件溯源)
│   - 存储状态变更事件而非状态本身
│   - 可重放、可审计
│
└─ CQRS (命令查询分离)
    - 写模型和读模型分开
    - 各自优化
```

#### 服务发现

```
客户端发现:
  Client → Service Registry → 直接访问服务实例
  案例: Netflix Eureka

服务端发现:
  Client → Load Balancer → Service Registry → 服务实例
  案例: AWS ALB + ECS

注册方式:
├─ 自注册: 服务启动时自己注册 (Eureka)
└─ 第三方注册: 由 Registrator 等工具注册
```

#### 熔断器模式 (Circuit Breaker)

```
状态机:
  Closed → Open → Half-Open → Closed

Closed (正常):
  - 请求正常通过
  - 统计失败次数
  - 失败超过阈值 → 切换到 Open

Open (熔断):
  - 所有请求立即失败 (fail-fast)
  - 不调用下游服务
  - 等待超时时间 → 切换到 Half-Open

Half-Open (半开):
  - 允许少量请求通过
  - 如果成功率高 → Closed
  - 如果仍然失败 → Open

实现: Hystrix, Resilience4j, Istio
```

---

### 分布式存储

#### 数据分片 (Sharding)

```
分片策略:

1. Hash 分片:
   shard_id = hash(key) % num_shards
   优点: 均匀分布
   缺点: 范围查询困难，扩容需重新分片

2. Range 分片:
   按 key 范围分配到不同 shard
   优点: 范围查询高效
   缺点: 可能热点不均

3. Consistent Hashing 一致性哈希:
   - 将 key 和节点映射到哈希环
   - 每个 key 顺时针找到最近的节点
   - 节点增减只影响相邻数据
   - 虚拟节点解决数据不均

4. Geographic 分片:
   - 按用户地理位置分片
   - 数据靠近用户，减少延迟
```

#### 复制策略 (Replication)

```
Leader-Follower (主从):
  - 所有写入通过 Leader
  - Leader 复制到 Followers
  - 读可以从 Follower
  - 案例: MySQL Replication, PostgreSQL Streaming

Multi-Leader (多主):
  - 多个节点可接受写入
  - 需要冲突解决策略
  - 适合: 多数据中心
  - 案例: CouchDB, Cassandra (最终一致)

Leaderless (无主):
  - 任何节点可接受读写
  - Quorum 机制: W + R > N 保证一致性
  - 案例: Amazon DynamoDB, Riak
```

---

## Templates / 设计模板

### 设计 URL Shortener

```
需求:
- 缩短 URL (长 → 短)
- 短 URL 重定向到原 URL
- 1 亿 URL, QPS 1000 读, 100 写

高层设计:
  Client → Load Balancer → App Server
                              │
                    ┌─────────┼─────────┐
                    ↓         ↓         ↓
                  Cache     DB       Analytics
                 (Redis)  (MySQL)   (ClickHouse)

核心流程:
  1. 生成短 URL:
     - Base62 编码自增 ID
     - 或 MD5 取前 7 位 + 冲突检测
     - 存入 DB: (short_url, long_url, created_at, user_id)

  2. 重定向:
     - 查 Cache (Redis)
     - Cache miss → 查 DB → 写入 Cache
     - 301/302 重定向

数据库 Schema:
  urls table:
    - id (bigint, auto-increment)
    - short_code (varchar(7), unique, indexed)
    - long_url (text)
    - user_id (bigint, nullable)
    - created_at (timestamp)
    - expires_at (timestamp, nullable)

扩展:
  - 自定义短 URL
  - 过期机制
  - 访问统计
  - 限流防滥用
```

### 设计 Chat System

```
需求:
- 1 对 1 聊天
- 群聊 (最多 500 人)
- 在线状态
- 消息持久化

高层设计:
  Client ←WebSocket→ Chat Server
                        │
              ┌─────────┼─────────┐
              ↓         ↓         ↓
        Message      Presence    Push
        Service      Service    Notification
           │           │
           ↓           ↓
        Message DB   Redis
       (Cassandra)  (在线状态)

核心组件:
  1. Chat Service: 处理消息收发
  2. Presence Service: 管理在线状态
  3. Push Notification: 离线推送
  4. Message Storage: 消息持久化

消息流程:
  1. 用户 A 发送消息 → Chat Server (WebSocket)
  2. Chat Server → 存入 Message DB
  3. Chat Server → 查询用户 B 的连接状态
  4. 在线 → 通过 WebSocket 推送
  5. 离线 → 发送 Push Notification

消息 ID 生成:
  - Snowflake ID: 时间戳 + 机器 ID + 序列号
  - 保证全局唯一且大致有序

群聊设计:
  - 群消息 fan-out 到群成员的收件箱
  - 大群用 pull 模式（客户端主动拉取）
  - 小群用 push 模式（服务器推送）
```

### 设计 News Feed

```
需求:
- 用户发布动态
- 用户看到关注人的动态
- 按时间/相关性排序

核心设计选择: Push vs Pull

Push (写扩散):
  发布时 → 写入所有 follower 的 feed
  优点: 读快 (直接读自己的 feed)
  缺点: 大 V 发布时扇出大
  适合: follower 数量少的用户

Pull (读扩散):
  读取时 → 拉取所有 followee 的最新动态
  优点: 写快
  缺点: 读时需要聚合排序
  适合: follower 数量大的用户 (大 V)

混合方案 (Hybrid):
  - 普通用户: Push
  - 大 V (>10K followers): Pull
  - 读取时合并 push 和 pull 的结果

数据模型:
  posts table:
    - post_id, user_id, content, media_urls, created_at

  follows table:
    - follower_id, followee_id

  feed_cache (Redis Sorted Set):
    - key: user:{user_id}:feed
    - score: timestamp
    - value: post_id
```

---

## Common Mistakes / 常见错误

1. **忽略网络不可靠**: 分布式系统中网络分区是常态，必须考虑
2. **过度依赖强一致性**: 大部分场景最终一致性就够了
3. **不讨论故障模式**: 主动说 "如果 X 挂了怎么办"
4. **忽略数据量增长**: 1GB 和 1TB 的设计方案完全不同
5. **选择性忽略 CAP**: 必须明确说你选择了哪两项
6. **把微服务当银弹**: 单体可能更适合当前规模
7. **不做容量估算**: 估算决定了分片数量、缓存大小

---

## Pro Tips / 高手技巧

- **先说 trade-off**: 每个决策都说清为什么选 A 不选 B
- **画数据流**: 数据从产生到消费的完整路径
- **提及一致性级别**: "这里我们需要最终一致性，因为..."
- **讨论可扩展性**: "当前方案可以支持 X QPS，如果需要更多..."
- **提及监控**: 日志、指标、告警、分布式追踪
- **了解真实系统**: Kafka、Redis、Cassandra 的架构设计

---

## Practice Questions / 练习题

| 题目 | 核心考点 |
|------|---------|
| Design a Distributed Lock | Consensus, Redis/ZooKeeper |
| Design a Rate Limiter | Token Bucket, Sliding Window, Redis |
| Design a Distributed Cache | Consistent Hashing, Eviction |
| Design a Key-Value Store | Partitioning, Replication, Consistency |
| Design a Notification System | Push/Pull, Priority Queue |
| Design a Video Streaming System | CDN, Transcoding, Adaptive Bitrate |
| Design a Search Autocomplete | Trie, Caching, Ranking |
| Design a Metrics Monitoring System | Time Series DB, Aggregation |

---

> **分布式系统的本质是 trade-off。** 没有完美的方案，只有适合特定场景的方案。面试中展示你理解这些权衡，并能根据需求做出合理选择。
>
> Distributed systems are all about trade-offs. There's no perfect solution, only the right solution for a specific context. Show the interviewer you understand these trade-offs.
