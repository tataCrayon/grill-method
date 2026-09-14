# grill-method

**Relentlessly stress-test the methodology and approach behind any plan, decision, or way of working.**
**把每个方法都当成有罪推定——拷问它，守住或替换它。**

> Treat every method as guilty until proven deliberate and superior. Ask one sharp question at a time that attacks its assumptions, trade-offs, failure modes, and whether a stronger approach exists. Always give your recommended better method with direct reasoning. Refuse to proceed until the core methodological choice is forced into the open and either defended or replaced.
>
> 把每个方法当成有罪推定。一次只问一个锋利问题，攻击它的假设、权衡、失败模式，以及是否存在更强做法。必须给出你推荐的更好方法并直接说明理由。在核心方法被摊开并被守住或替换之前，拒绝按原方法继续。

这是一个 [Agent Skill](https://agentskills.io)（兼容 Claude Code / ZCode 等 Agent 的 Skill 机制），让 AI 在工作时对**方法论本身**进行严格质询——不是审需求，不是审代码对错，而是审**"这么走是不是最优路线"**。

---

## 为什么需要它

AI（和人）最常见的隐性浪费是：**用一个没比较过的默认值把工作做完**。

- "直接开始翻译" ——为什么串行不并行？为什么这样提交？
- "按老流程同步需求" ——它成立的前提验证过吗？坏了多久能发现？
- "先跑通再说" ——没比较过的默认值混进交付物，就是债。

不被拷问的方法只是习惯。本 Skill 让 AI 在开工前先对自己的方法跑一轮有罪推定：假设被攻击、权衡被摊开、失败模式被点名、更强做法被比对——然后给出**判决**（守住 / 改良 / 替换），并附上最终采用的方法。

## 工作方式

### 拷问循环（4 步）

```
STATE   — 一句话说清被审方法 + 它成立的前提 + 成功长什么样
ATTACK  — 沿四个方向发问；交互模式下一次只问一问，问完附上你的推荐
VERDICT — 逐项判决：守住 / 改良 / 替换
COMMIT  — 写下最终采用的方法 + 一句话幸存理由，然后开工
```

### 四个攻击方向

| 方向 | 问题模板 |
|---|---|
| 假设 | 这个方法成立的前提是什么？哪些前提没验证过？ |
| 权衡 | 它牺牲了什么（速度/质量/可维护性/覆盖面）？是主动选的，还是默认继承的？ |
| 失败模式 | 它在实践中最常怎么坏？坏的最早信号是什么？ |
| 更优替代 | 已知更强的做法是什么？不用它的理由站得住吗？ |

### 两种模式

- **Mode A：自我拷问**（默认）——AI 在开始"有分量"的工作（多步、难回退、路线不显然）之前，自己问自己答，只输出一张 ≤10 行的 **⚖️ 方法判决卡**，然后直接开工，不打扰用户。
- **Mode B：拷问对方**（交互式）——用户把方法摊开来让 AI 审时，**一次只问一个问题**，每问必附推荐方案与理由，**3 轮封顶**。

### 关键设计原则

| 原则 | 说明 |
|---|---|
| 批评必须附带货 | 每个质询必须同时给出推荐的替代方法与直接理由。只拆不建等于噪音，禁止出口。 |
| 拒绝的是方法，不是工作 | "拒绝继续"指拒绝按原方法推进，输出始终是"替代方案+理由"交用户裁决；用户拍板后记录 override 立即继续。 |
| 有管辖边界 | 机械、微小、可逆的操作直接放行；用户说"别问了，直接做"则记录 override。 |
| 判决必须落地 | 每轮循环以"采用方法"收尾，禁止以"值得思考"收尾。 |
| 攻击方法不攻击人 | 判决永远针对方法，不针对提出方法的人。 |

完整指令见 [SKILL.md](SKILL.md)。

## 与相近概念的区别

| | 审的对象 | 典型问题 |
|---|---|---|
| 需求澄清类 Skill（如 grill-me） | 需求（做什么、边界在哪） | "这个需求为什么做？不做什么？" |
| **grill-method** | **方法（怎么做的路线选择）** | **"这么走是最优路线吗？有没有更强的走法？"** |
| 决策审查类 Skill（如 doubt-driven-development） | 具体决策/制品（做得对不对） | "这个决策有什么问题？" |

三者互补：先澄清需求，再选定方法路线（grill-method），路线上的非平凡决策再逐个审查（DDD）。

## 安装

把 `SKILL.md` 放入你的 Agent 的全局技能目录，或项目级技能目录：

```bash
# 全局（对所有项目生效）
git clone https://github.com/tataCrayon/grill-method.git
cp grill-method/SKILL.md ~/.claude/skills/grill-method/SKILL.md   # Claude Code
# 或 ~/.agents/skills/grill-method/SKILL.md                        # ZCode 等按各自约定
```

无需额外依赖。重启会话后，说 "grill the method"、"拷问一下这个方法"、"这个做法是不是最优" 即可硬触发；AI 也会在多步任务开工前自动自我拷问（软触发）。

## 评估

`evals/evals.json` 内置 3 个测试用例（2 正 1 负），每个用例带 Skill / 不带 Skill 对照运行：

| 用例 | 验证点 | 结果 |
|---|---|---|
| 多步任务自我拷问（30 篇文档翻译+提交） | 开工前输出判决卡，攻击覆盖假设/权衡/失败模式/替代 | ✅ 3/3 断言通过 |
| 拷问用户的方法（需求同步流程） | 一次只问一问、批评附推荐、3 轮有界 | ✅ 3/3 断言通过 |
| 负例：改一个错别字 | 琐碎任务不触发、直接完成 | ✅ 3/3 断言通过 |

已知代价：带 Skill 时 token 开销约为基线的 1.6–2.5 倍——这是方法审查本身的成本，对比选错路线推倒重来的成本，是最便宜的保险。

## License

MIT
