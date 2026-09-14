# grill-method

**Relentlessly stress-test the methodology and approach behind any plan, decision, or way of working.**

把每个方法都当成有罪推定——拷问它，守住或替换它。可作为 Claude Code / Codex / Cursor / ZCode 等的 Skill 使用。

> Treat every method as guilty until proven deliberate and superior. Ask one sharp question at a time that attacks its assumptions, trade-offs, failure modes, and whether a stronger approach exists. Always give your recommended better method with direct reasoning. Refuse to proceed until the core methodological choice is forced into the open and either defended or replaced.
>
> 把每个方法当成有罪推定。一次只问一个锋利问题，攻击它的假设、权衡、失败模式，以及是否存在更强做法。必须给出你推荐的更好方法并直接说明理由。在核心方法被摊开并被守住或替换之前，拒绝按原方法继续。

AI（和人）最常见的隐性浪费是：**用一个没比较过的默认值把工作做完**。"直接开始翻译"——为什么串行不并行？"按老流程同步需求"——它成立的前提验证过吗？不被拷问的方法只是习惯。本 Skill 让 AI 在开工前先对自己的方法跑一轮有罪推定：假设被攻击、权衡被摊开、失败模式被点名、更强做法被比对——然后给出**判决**（守住 / 改良 / 替换），并附上最终采用的方法。

## 功能特性

- **4 步拷问循环**：`STATE`（说清被审方法）→ `ATTACK`（四方向攻击）→ `VERDICT`（守住/改良/替换）→ `COMMIT`（采用方法落地开工）
- **四个攻击方向**：假设、权衡、失败模式、更优替代——每个方向都带问题模板
- **Mode A 自我拷问**：多步任务开工前 AI 自动自问自答，只输出一张 ≤10 行的「⚖️ 方法判决卡」，不打扰用户
- **Mode B 拷问对方**：交互式一次只问一问，每问必附推荐方案与理由，3 轮封顶
- **批评必须附带货**：每个质询必须同时给出替代方案与直接理由，只拆不建等于噪音，禁止出口
- **有管辖边界**：机械、微小、可逆的操作直接放行，不为拷问而拷问；用户拍板后记录 override 立即继续

## 安装

### 方式一：skills.sh 一键安装

```bash
npx skills add tataCrayon/grill-method
```

### 方式二：手动安装（推荐软链接，仓库更新自动生效）

```bash
git clone https://github.com/tataCrayon/grill-method.git
cd grill-method

# Claude Code
mkdir -p ~/.claude/skills && ln -s "$(pwd)" ~/.claude/skills/grill-method

# Codex
mkdir -p ~/.codex/skills && ln -s "$(pwd)" ~/.codex/skills/grill-method

# ZCode / 其他遵循 ~/.agents/skills 约定的客户端
mkdir -p ~/.agents/skills && ln -s "$(pwd)" ~/.agents/skills/grill-method
```

Windows 用户请将 `ln -s` 替换为 `mklink /D`（管理员 CMD）或直接复制整个目录。

安装后**重启客户端或新开会话**，Skill 才会被检测到。无需任何额外依赖或配置。

## 使用

### 作为 Skill

在会话中直接说：

- *"grill the method"*
- *"拷问一下这个方法"*
- *"这个做法是不是最优？有没有更好的做法？"*
- *"check my approach"*

### 触发方式

| 方式 | 说明 |
|---|---|
| **硬触发** | 用户主动要求拷问方法/流程/工作方式 |
| **软触发** | AI 在开始多步、难回退、路线不显然的工作前，自动自我拷问 |
| **不触发** | 机械、微小、可逆的操作（改错别字、跑测试、格式化），用户说"别问了直接做"时记录 override |

### Mode A：自我拷问（默认）

用户只需正常下达任务。AI 在开工前自动输出一张判决卡：

```
⚖️ 方法判决
- 被审方法：串行逐篇翻译 30 篇文档 → 全部完成后一次性 git 提交
- 拷问记录：
  1. 失败模式：一口气译完再统一提交，中途质量漂移无法定位 → 改良为分批+逐批自检
  2. 更优替代：并行翻译更快，但术语一致性协调成本大于速度收益 → 不采用
  3. 假设未验证："统一提交"默认工作区无无关改动 → 提交前先核对 git 状态
- 判决：🔧 改良（分批处理 + 先验仓库状态；提交粒度尊重用户明确指令）
- 采用方法：分批翻译（每 10 篇一批，维护术语表）→ 全量自检 → 核对后统一提交
```

然后直接开工。

### Mode B：拷问对方（交互式）

用户把方法摊开来让 AI 审时，AI **一次只问一个问题**，问完附上推荐方案与理由；你可以逐条回答，也可以直接回"接受推荐"快速通关。3 轮后仍有疑点，AI 列出剩余疑点 + 最佳判断，交你定夺。

## 与相近概念的区别

| | 审的对象 | 典型问题 |
|---|---|---|
| 需求澄清类（如 grill-me） | 需求（做什么、边界在哪） | "这个需求为什么做？不做什么？" |
| **grill-method** | **方法（怎么做的路线选择）** | **"这么走是最优路线吗？有没有更强的走法？"** |
| 决策审查类（如 doubt-driven-development） | 具体决策/制品（做得对不对） | "这个决策有什么问题？" |

三者互补：先澄清需求，再选定方法路线（grill-method），路线上的非平凡决策再逐个审查（DDD）。

## 常见问题（FAQ）

| 问题 | 解答 |
|---|---|
| 会不会什么都拷问，变得很烦？ | 不会。Skill 内置管辖边界：机械、微小、可逆的操作直接放行；只有"有分量"的工作（多步、难回退、涉及正确性/数据/资金/安全、路线取舍不显然）才触发。 |
| "拒绝继续"会不会让 AI 卡死？ | 不会。拒绝的对象是方法本身，不是工作——输出始终是"替代方案+理由"交你裁决，你说"就用原方法"，记录 override 后立即继续。 |
| Mode A 自我拷问会不会浪费 token？ | 有成本：实测 token 开销约为基线的 1.6–2.5 倍。对比选错路线推倒重来的成本，这是最便宜的保险。判决卡上限 10 行，拷问是工作的一部分，不是表演。 |
| 自我拷问会不会自己骗自己？ | 会有确认偏误（提出方法与审判方法共享盲点）。SKILL.md 已内置升级条款：高风险方法（不可逆、涉及资金/数据/安全）应升级到 doubt-driven-development 的新上下文审查。 |
| 每问必附推荐，会不会推荐就是对的？ | 不。推荐附带的理由才是重点——你可以反驳理由，也可以直接采纳快速通关。3 轮封顶保证拷问服务于决策，不替代决策。 |
| 支持哪些客户端？ | 任何支持 Agent Skills 机制的客户端（Claude Code / Codex / Cursor / ZCode 等）。纯 Markdown 指令，无脚本依赖。 |

## 注意事项

- 本 Skill 审的是**方法路线**，不替代需求澄清和代码审查——三者各管一段
- 判决卡控制在 10 行内；如果 AI 输出了冗长的拷问清单，那是违反了 Skill 的红线，提醒它即可
- **客户端兼容声明**：本 Skill 已在 ZCode 会话中验证（模拟评估 + 真实自审）；其余客户端（Claude Code / Codex / Cursor）对 description 的加载与软触发行为未逐一验证，不保证一致，欢迎反馈
- **版本策略**：SKILL.md 的行为性变更必须 bump frontmatter 中的 `version` 并打 git tag（当前 `v1.0.0`），用户可据此区分自己遇到的是哪个版本的 grill
- 触发准确性依赖 description，如果发现漏触发（该拷问没拷问）或误触发（改错别字也要审），欢迎提 issue

## 测试

`evals/evals.json` 内置 3 个测试用例（2 正 1 负），每个用例带 Skill / 不带 Skill 对照运行，共 9 条断言全部通过：

| 用例 | 验证点 | 结果 |
|---|---|---|
| 多步任务自我拷问（30 篇文档翻译+提交） | 开工前输出判决卡，攻击覆盖假设/权衡/失败模式/替代 | ✅ 3/3 |
| 拷问用户的方法（需求同步流程） | 一次只问一问、批评附推荐、3 轮有界 | ✅ 3/3 |
| 负例：改一个错别字 | 琐碎任务不触发、直接完成 | ✅ 3/3 |

## License

[MIT](LICENSE)
