# Offer Negotiation — Offer 谈判策略

> **When to Use / 使用场景**: 拿到 offer 后进行薪资谈判, 争取最优的补偿方案。

---

## Key Concepts / 核心概念

### 总包 (Total Compensation) 构成

| 组成部分 | 英文 | 说明 | 占比 |
|---------|------|------|------|
| 基本工资 | Base Salary | 固定月薪/年薪 | 40-60% |
| 签字费 | Sign-on Bonus | 入职一次性奖金 | 5-15% |
| 股票/期权 | Equity/RSU | 分期 vest 的股权 | 20-40% |
| 年度奖金 | Annual Bonus | 绩效奖金 (百分比) | 10-20% |
| 福利 | Benefits | 医保、退休金等 | 隐性 |

### 总包计算公式

```
Year 1 TC = Base + Sign-on + (RSU / Vest Period) + (Base × Bonus %)
Year 2+ TC = Base + (RSU / Vest Period) + (Base × Bonus %)

例:
Base: $200K
Sign-on: $50K (Year 1 only)
RSU: $400K / 4 years = $100K/year
Bonus: 15% = $30K

Year 1 TC = $200K + $50K + $100K + $30K = $380K
Year 2+ TC = $200K + $100K + $30K = $330K
4 年平均 TC = ($380K + $330K × 3) / 4 = $342.5K
```

### 市场薪资参考

```
数据来源:
- levels.fyi (最准确的科技公司薪资数据)
- Glassdoor
- Blind 匿名社区
- LinkedIn Salary Insights
- Paysa, Compensation.com

中国:
- 脉脉 匿名分享
- offershow.com
- 牛客网

⚠️ 注意:
- 地区差异很大 (湾区 vs 西雅图 vs 纽约 vs 远程)
- 级别对应差异 (Google L5 = Meta E5 = Amazon L6)
- 年份差异 (市场波动)
```

---

## Step-by-Step Framework / 谈判步骤

### Phase 1: 信息收集 (面试前)

```
□ 确定目标薪资范围 (min, target, max)
□ 研究目标公司的薪资水平 (levels.fyi)
□ 了解自己的 leverage (其他 offer? 市场稀缺度?)
□ 准备好 "walk away number" (低于这个数不接受)
```

### Phase 2: 初次报价 (拿到 offer)

```
策略:
1. 收到口头 offer 时:
   - 不要立刻答应或拒绝
   - 说: "Thank you for the offer! I'm very excited about this
     opportunity. I'd like to take a day or two to review the
     details. Could you send me the written offer?"

2. 收到书面 offer 后:
   - 仔细检查每一项 (base, RSU, sign-on, bonus, benefits)
   - 计算总包 (TC)
   - 对比市场数据

3. 回复时间:
   - 一般给 3-5 个工作日
   - 如果需要更多时间: "I'm very interested, but I'd like a
     few more days to make sure I'm making the right decision.
     Is [日期] acceptable?"
```

### Phase 3: 谈判

```
谈判原则:
1. 永远不要先报价 (让对方先出价)
2. 如果被问期望薪资, 给范围而非具体数字
3. 基于数据谈判, 不基于需求
4. 谈总包, 不只谈 base
5. 保持专业和积极
6. 书面确认所有承诺
```

---

## Templates / 谈判脚本模板

### 当被问到期望薪资时

```
❌ 不好的回答:
"I'm looking for around $200K." (报低了, 你亏了; 报高了, 可能被淘汰)

✅ 好的回答:
"Based on my research and experience, I'm looking for a total
compensation in the range of [range]. That said, I'm flexible
and more interested in the right opportunity. What's the budget
for this role?"

或者:
"I'd prefer to understand the full scope of the role and
compensation structure first. Could you share the salary range
for this position?"
```

### Counter-offer 邮件模板

```
Subject: Re: Offer for [Position] - [Your Name]

Dear [Hiring Manager / Recruiter],

Thank you again for the offer for the [Position] role at [Company].
I'm genuinely excited about the opportunity to join the team and
contribute to [specific project/mission].

After carefully reviewing the offer, I'd like to discuss the
compensation package:

Base Salary:
The offered base of $[X] is below the market rate for this level
in [location]. Based on my research (levels.fyi, Glassdoor) and
[Y years] of experience in [domain], I believe a base of $[Y]
would be more appropriate.

Equity:
The RSU grant of $[X] over 4 years results in $[X/4] per year.
Given the typical equity component for [level] at [Company], I'd
like to discuss increasing this to $[Y].

Sign-on Bonus:
I appreciate the sign-on bonus. However, I'd be leaving behind
[unvested RSU/annual bonus] at my current company valued at
approximately $[X]. An increased sign-on bonus of $[Y] would
help offset this.

I want to emphasize that I'm very enthusiastic about this role
and [Company]'s mission. I believe we can find a package that
reflects the value I'll bring to the team.

I'm happy to discuss this over the phone at your convenience.

Best regards,
[Your Name]
```

### 电话谈判脚本

```
开场:
"Hi [Recruiter], thanks for making time. I've had a chance to
review the offer in detail, and I'm really excited about [Company].
I do have a few points I'd like to discuss."

提出 counter:
"Looking at the total compensation, I noticed [specific area].
Based on [market data/my experience/current compensation],
I was hoping we could adjust [specific component] to [target]."

解释原因:
"This is based on [data point 1] and [data point 2]. I also have
[competing offer / current equity vesting] that I'd be leaving
on the table."

保持积极:
"I want to reiterate that I'm very excited about this role.
I'm confident we can work something out that reflects the value
I'll bring."

如果被拒绝:
"I understand there are constraints. Are there other components
we could explore? For example, [sign-on bonus / RSU refresh /
flexible work / title / review timeline]?"
```

### 使用竞争 offer

```
"Before I make my final decision, I want to be transparent that
I have another offer from [Company B] at [approximate TC, if
comfortable sharing]. While I prefer [Company A] because of
[specific reasons], the compensation difference is significant.
Is there flexibility to close the gap?"

⚠️ 注意:
- 不要撒谎 (offer 可能被验证)
- 不需要透露具体公司名
- 态度是 "我希望来这里, 但需要数字合理"
```

---

## 谈判清单

### 可以谈的项目

```
💰 薪资相关:
  □ Base Salary 基本工资
  □ Equity/RSU 股票数量
  □ Sign-on Bonus 签字费
  □ Annual Bonus 年度奖金百分比
  □ Relocation 搬迁补贴

📋 非薪资:
  □ Title 职位头衔
  □ Remote/Hybrid 远程工作
  □ Start Date 入职日期
  □ Vacation 天假
  □ Review Timeline 提前 review (6 个月 vs 1 年)
  □ RSU Refresh Grant 新增股票授予
  □ Education Budget 培训预算
  □ Immigration Sponsorship 签证支持
```

---

## Common Mistakes / 常见错误

1. **接受第一个报价**: 几乎所有 offer 都有谈判空间
2. **先报自己的期望**: 让对方先出价
3. **只谈 base salary**: 总包 (TC) 才是真正重要的
4. **用个人需求做理由**: "我需要更多因为房贷" → 用市场数据
5. **撒谎说有其他 offer**: 可能被查证, 毁掉 offer
6. **太 aggressive**: 保持专业, 这是你未来同事
7. **不书面确认**: 口头承诺要写入 offer letter
8. **给 ultimatum**: "给我 X 否则我不来" → 没人喜欢被威胁
9. **太快接受**: 至少等 24 小时
10. **不谈判 sign-on**: 签字费通常最容易谈

---

## Pro Tips / 高手技巧

- **多 offer 是最强 leverage**: 同时面多家公司, 拿到多个 offer
- **Timing matters**: 如果面试表现特别好, 谈判空间更大
- **谈 level 而不只是钱**: 如果能从 L4 谈到 L5, 长期收益更大
- **6 个月 review**: 如果无法提高 TC, 争取 6 个月后 review 的机会
- **RSU refresh**: 入职后每年的新增 RSU 也是可以谈的
- **Relocation**: 搬家费可以谈到很高, 尤其大公司
- **保持关系**: 无论结果如何, 保持专业, 行业很小

---

## Practice / 练习清单

```
□ 研究目标公司薪资数据 (levels.fyi)
□ 计算自己的 BATNA (最佳替代方案)
□ 准备 counter-offer 邮件
□ 和朋友模拟电话谈判
□ 准备好 walk away number
□ 列出所有可谈的项目
□ 准备好 2-3 个谈判要点
```

---

> **谈判不是对抗, 是合作。** 你和 recruiter 的共同目标是让你加入公司。保持积极, 用数据说话, 展示你的价值。
>
> Negotiation is not adversarial — it's collaborative. You and the recruiter share the same goal: getting you to join. Stay positive, use data, and demonstrate your value.
