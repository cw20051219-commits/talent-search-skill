# talent-search-skill

为 AI / 量化岗位做**全网人才寻访**的 Agent Skill —— 输入人才画像或 JD，产出经过交叉验证的 **Excel 候选清单**与完整**检索记录**。

适用于 Claude Code、WorkBuddy 及任何支持 SKILL.md 规范的 AI Agent 宿主。

## 为什么值得用

1. **四通道并行检索**：学术（论文/课题组/竞赛）、GitHub（开源贡献者）、公司（团队页/技术博客）、社区（知乎/LinkedIn/脉脉公开页）四路线索同时撒网
2. **交叉验证纪律**：每位候选人必须经过至少一遍一手来源验证（单轮验证，必需流程）；关键岗位可启用**五遍验证模式**——学术、GitHub、职业平台、公司一手来源、个人主页五个独立维度互相印证，矛盾时以一手来源为准
3. **宁缺毋滥**：凑不齐一手来源核实的信息一律标「待核实 / 未获取」，禁止编造；每条关键信息在检索记录中可追溯到来源 URL
4. **零依赖 Excel 产出**：内置 `gen_excel.mjs`（Node ≥ 18，无需安装任何依赖），固定 9 列：姓名 / 学历 / 实习工作经历 / 个人主页 / GitHub / 一作论文 / 大学实验室经历 / 匹配亮点 / 备注（CV 与联系方式）
5. **候选人库（越用越厚）**：每轮检索结果沉淀到 `talent-research/_db/candidates.jsonl`，下轮同方向任务先查库做增量更新——90 天内验证过的直接复用、作废过的不再重复挖掘。配套 `db_upsert.mjs` 提供 upsert / search / stats 三个零依赖命令
6. **经验自动沉淀（自我改进）**：每轮任务结束自动复盘，把源有效性、作废模式、网络坑、流程改进追加到 `talent-research/_lessons/` 月度文件，下轮开工先读最近两月经验并调整检索策略——skill 在使用中持续进化
7. **学历门槛判定**：内置 C9 / 港校 / 海外院校分级对照表与知名实验室判定标准

## 工作流程

```
画像 / JD
   │
   ▼
第 0 步  解析画像 → 查候选人库（增量复用）→ 读最近两月经验
   │
   ▼
第 1 步  四通道并行检索 ──→ 8–15 条候选线索卡片
   │
   ▼
第 2 步  汇总去重 → 交叉验证 → 回写候选人库
   │        ├─ 单轮验证（必需）：一手来源核实学历/现职/联系方式
   │        └─ 五遍验证（可选）：学术/GitHub/职业平台/公司/个人主页
   │
   ▼
第 3 步  产出 candidates-YYYYMMDD.xlsx + 检索记录.md
   │
   ▼
第 4 步  复盘沉淀 ──→ 经验追加到 _lessons/，下轮开工复用
```

## 安装

**Claude Code 用户**（个人级，全局可用）：

```bash
git clone https://github.com/cw20051219-commits/talent-search-skill ~/.claude/skills/talent-search
```

或项目级（仅当前项目可用）：

```bash
git clone https://github.com/cw20051219-commits/talent-search-skill .claude/skills/talent-search
```

**WorkBuddy 用户**：

```bash
git clone https://github.com/cw20051219-commits/talent-search-skill ~/.workbuddy/skills/talent-search
```

**其他 Agent 宿主**：把本文件夹复制到你的宿主对应的 skills 目录即可（skill 格式为通用的 `SKILL.md` + frontmatter 规范）。

依赖：Node.js ≥ 18（仅用于生成 Excel 与维护候选人库，零第三方依赖）。

## 使用

装好后直接对话触发，例如：

> 帮我找 LLM 预训练数据方向的候选人：博士 2021–2024 届，C9 或海外院校，现在在国内工作，目标 20 人。

或提供对标画像 / JD：

> 这是我们的对标人物画像（examples/对标画像.example.md 格式），按这个找类似的人。

Agent 会自动进入 skill 流程；也可以参考 `examples/` 下的对标画像与 candidates.json 示例手工构造输入。

## 仓库结构

```
talent-search-skill/
├── SKILL.md                    # 主指令：五步流程（含查库/回写/复盘）+ 验证纪律 + 产出规范
├── references/
│   ├── source-playbook.md      # 四通道检索策略、联系方式获取规则
│   ├── school-list.md          # C9/港校/海外院校分级、知名实验室判定
│   └── lessons-template.md     # 复盘经验条目格式模板（四类条目）
├── scripts/
│   ├── gen_excel.mjs           # 零依赖 xlsx 生成器（固定 9 列）
│   └── db_upsert.mjs           # 零依赖候选人库维护（upsert/search/stats）
└── examples/
    ├── 对标画像.example.md      # 对标画像三段式模板（人物虚构）
    ├── candidates.example.json # Excel 输入数据结构示例
    └── db-candidates.example.jsonl # 候选人库 schema 示例（人物虚构）
```

## 合规声明

- 本 skill 只收集**公开信息**（GitHub profile 邮箱、个人主页邮箱、论文通讯邮箱、LinkedIn 公开页），不买数据、不爬付费数据库、不进封闭群组
- 产出的候选人信息涉及个人信息，使用者应确保用途合法合规（如正当招聘需求），并遵守所在司法辖区的个人信息保护法律法规（如《中华人民共和国个人信息保护法》、GDPR 等）
- 使用者自行承担使用本工具的全部合规责任

## License

[MIT](./LICENSE)
