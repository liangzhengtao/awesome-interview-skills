# GitHub Portfolio — GitHub 个人品牌优化

> **When to Use / 使用场景**: 打造开发者个人品牌, 让 GitHub 成为你的第二张简历。

---

## Key Concepts / 核心概念

### 为什么 GitHub 很重要

```
面试官看 GitHub 的 3 秒法则:
1. 看 Profile README → 第一印象
2. 看 Pinned Repos → 你的最佳作品
3. 看 Contribution Graph → 你的持续度

GitHub 是你的:
✅ 代码能力证明
✅ 技术品味展示
✅ 开源贡献记录
✅ 持续学习的证据
```

### GitHub Profile 评估维度

| 维度 | 权重 | 说明 |
|------|------|------|
| Profile README | ★★★★★ | 个人品牌的入口 |
| Pinned Repos | ★★★★★ | 展示最佳项目 |
| Contribution Graph | ★★★☆☆ | 持续编码的证据 |
| Project Documentation | ★★★★☆ | README 质量 |
| Open Source Contributions | ★★★★☆ | 社区参与度 |
| Commit Messages | ★★☆☆☆ | 代码规范 |

---

## Step-by-Step Framework / 分步框架

### 1. Profile README 优化

#### 创建方法

```bash
# 创建一个与用户名同名的仓库
# 例: 用户名是 octocat, 创建仓库 octocat/octocat
# 在仓库根目录创建 README.md, 它会显示在你的 Profile 页面
```

#### Profile README 模板

```markdown
# Hi, I'm [Your Name] 👋

## 🚀 About Me

- 🔭 I'm currently working on [current project/technology]
- 🌱 I'm currently learning [new skill/technology]
- 👯 I'm looking to collaborate on [type of project]
- 💬 Ask me about [your expertise areas]
- 📫 How to reach me: [email/twitter/linkedin]
- ⚡ Fun fact: [something interesting about you]

## 🛠️ Tech Stack

**Languages:**
![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![Go](https://img.shields.io/badge/Go-00ADD8?style=flat&logo=go&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=flat&logo=typescript&logoColor=white)

**Frameworks & Libraries:**
![React](https://img.shields.io/badge/React-61DAFB?style=flat&logo=react&logoColor=black)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat&logo=fastapi&logoColor=white)

**Databases:**
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=flat&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat&logo=redis&logoColor=white)

**Cloud & DevOps:**
![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat&logo=amazon-aws&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat&logo=kubernetes&logoColor=white)

## 📊 GitHub Stats

![Your GitHub stats](https://github-readme-stats.vercel.app/api?username=YOUR_USERNAME&show_icons=true&theme=radical)

![Top Languages](https://github-readme-stats.vercel.app/api/top-langs/?username=YOUR_USERNAME&layout=compact&theme=radical)

## 🏆 Featured Projects

| Project | Description | Tech Stack | Stars |
|---------|-------------|------------|-------|
| [Project 1](link) | One-line description | Python, FastAPI | ⭐ X |
| [Project 2](link) | One-line description | Go, gRPC | ⭐ X |
| [Project 3](link) | One-line description | TypeScript, React | ⭐ X |

## 📝 Latest Blog Posts

<!-- BLOG-POST-LIST:START -->
- [Blog Post Title 1](link)
- [Blog Post Title 2](link)
<!-- BLOG-POST-LIST:END -->

## 📫 Connect With Me

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=flat&logo=linkedin&logoColor=white)](linkedin-url)
[![Twitter](https://img.shields.io/badge/Twitter-1DA1F2?style=flat&logo=twitter&logoColor=white)](twitter-url)
[![Blog](https://img.shields.io/badge/Blog-FF5722?style=flat&logo=blogger&logoColor=white)](blog-url)

---
⭐ From [YourName](https://github.com/YourName)
```

---

### 2. Project README 模板

每个 pinned 仓库都应该有一个高质量的 README:

```markdown
# Project Name

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)]()
[![Version](https://img.shields.io/badge/version-1.0.0-blue)]()

> One-line description of what this project does and why it matters.

## 🎯 Problem

What problem does this solve? Why does it matter?

## ✨ Features

- Feature 1: Description
- Feature 2: Description
- Feature 3: Description

## 🚀 Quick Start

### Prerequisites
- Python 3.9+
- Docker (optional)

### Installation

```bash
# Clone the repo
git clone https://github.com/liangzhengtao/project-name.git
cd project-name

# Install dependencies
pip install -r requirements.txt

# Run
python main.py
```

### Docker

```bash
docker build -t project-name .
docker run -p 8000:8000 project-name
```

## 📖 Usage

```python
from project import Client

client = Client(api_key="your-key")
result = client.do_something("input")
print(result)
```

## 🏗️ Architecture

```
┌──────────┐     ┌──────────┐     ┌──────────┐
│  Client  │────→│  Server  │────→│ Database │
└──────────┘     └──────────┘     └──────────┘
```

## 📊 Performance

| Metric | Value |
|--------|-------|
| Latency | < 50ms |
| Throughput | 10K QPS |
| Uptime | 99.9% |

## 🧪 Testing

```bash
# Run tests
pytest tests/ -v

# With coverage
pytest tests/ --cov=src --cov-report=html
```

## 🤝 Contributing

Contributions are welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

## 📄 License

This project is licensed under the MIT License - see [LICENSE](LICENSE) file.

## 🙏 Acknowledgments

- [Library/Person] for [contribution]
```

---

### 3. Contribution Graph 优化

```
保持活跃:
- 每天至少一个 commit (不需要是大项目)
- 可以用 GitHub Actions 自动化一些任务
- 参与开源项目的 issue 和 PR

⚠️ 注意:
- 不要为了绿格子而做无意义的 commit
- 质量 > 数量
- 间隔几个月没活动不是大问题, 持续性更看重趋势
```

---

### 4. Pinned Repos 策略

```
最佳实践:
1. Pin 6 个仓库 (GitHub 允许最多 6 个)
2. 确保每个 repo 都有:
   ✅ 清晰的 README
   ✅ 有 demo/截图
   ✅ 代码质量高
   ✅ 有 stars

3. 项目多样性:
   - 1-2 个完整项目 (展示全栈能力)
   - 1 个技术深度项目 (展示专业能力)
   - 1 个开源贡献 (展示社区参与)
   - 1 个有趣的个人项目 (展示创造力)

4. 不要 pin:
   ❌ fork 的项目 (除非你有大量贡献)
   ❌ 课程作业 (太简单)
   ❌ 空仓库
   ❌ 一年多没更新的项目
```

---

## Templates / 项目展示模板

### 项目展示卡片模板

```
Project Name: [名称]
One-liner: [一句话描述]
Problem: [解决什么问题]
My Role: [你的角色: solo developer / tech lead / contributor]
Tech Stack: [技术栈]
Impact: [量化影响: users, stars, downloads]
Demo: [在线演示链接]
```

### 项目分类建议

```
📌 展示技术广度:
  - Full-stack Web App (React + Node.js/Python)
  - CLI Tool (Go/Rust)
  - Mobile App (React Native/Flutter)

📌 展示技术深度:
  - Database engine / 编译器 / 解释器
  - 分布式系统 (Raft 实现等)
  - 机器学习 pipeline

📌 展示实际问题解决:
  - 开源工具 / 库
  - 自动化脚本
  - 开发者工具

📌 展示创意:
  - Game
  - Art/Visualization
  - AI 应用
```

---

## Common Mistakes / 常见错误

1. **Profile 没有 README**: 这是最容易优化的, 也是最容易忽略的
2. **README 只有一行**: 没有安装说明、没有截图、没有文档
3. **代码没有注释**: 关键逻辑需要注释
4. **Commit message 写 "fix" 或 "update"**: 用 conventional commits
5. **项目全是 Hello World**: 至少有几个有实际价值的项目
6. **不写测试**: 有测试的项目看起来更专业
7. **不维护**: 半年没更新的项目让人怀疑你是否活跃
8. **Copy-paste 项目**: 改自模板的项目一眼就能看出来
9. **忽略 Issues/PRs**: 别人提的 issue 你从来不回复
10. **Profile 太花哨**: 保持专业, 不要放太多 emoji 和 GIF

---

## Pro Tips / 高手技巧

- **给项目加 demo**: 部署到 Vercel/Railway/Render, 让人直接看到效果
- **录 GIF 演示**: README 中放操作演示 GIF
- **写技术博客**: 用 GitHub Pages 或链接到你的博客
- **参与 Hacktoberfest**: 每年 10 月的开源活动, 积累贡献
- **用 GitHub Actions**: 展示你的 CI/CD 能力
- **创建 awesome lists**: 整理资源列表, 容易获得 stars
- **Star 和 fork 好项目**: 展示你的技术品味
- **写 changelog**: 版本更新记录展示你的专业度

---

## Commit Message 规范

```
格式: <type>(<scope>): <description>

Types:
  feat:     新功能
  fix:      修复 bug
  docs:     文档
  style:    代码格式 (不影响逻辑)
  refactor: 重构
  test:     测试
  chore:    构建/工具

示例:
  feat(auth): add OAuth2 login with Google
  fix(api): resolve race condition in order processing
  docs(readme): add installation guide for Windows
  perf(db): optimize query with composite index

好处:
  - 自动生成 changelog
  - git log 更清晰
  - 展示你的专业度
```

---

## Practice / 练习清单

```
Week 1: 基础设置
  □ 创建 Profile README
  □ 选择 6 个 pinned repos
  □ 为每个 repo 更新 README

Week 2: 项目提升
  □ 为最佳项目添加 demo
  □ 添加测试
  □ 添加 CI/CD (GitHub Actions)

Week 3: 内容优化
  □ 写技术博客或项目文档
  □ 参与一个开源项目 (提 PR 或 issue)
  □ 更新 LinkedIn 与 GitHub 同步

Week 4: 持续维护
  □ 设定每天 commit 的习惯
  □ 关注有趣的开源项目
  □ 开始下一个个人项目
```

---

> **GitHub 是你的技术名片。** 在面试官搜索你名字的那一刻, 你的 GitHub 就在替你说话。让它说的是: "这是一个认真对待代码的人。"
>
> Your GitHub is your technical business card. When an interviewer searches your name, your GitHub speaks for you. Make sure it says: "This person takes code seriously."
