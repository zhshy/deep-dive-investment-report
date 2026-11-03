# deep-dive-investment-report

> 深度投研报告方法论 — 一个把「该不该买」做成**每数字带口径、每判断带证伪条件、最后落成可执行仓位规则**的技能包。

适用对象：WorkBuddy / Claude 类 AI 助手的 Skill 目录。版本 **V1.01**。

---

## 它解决什么问题

多数"投资分析"停在观点层面：跌了多少、估值几分位、所以值得买。这份方法论换了个问法：

**不回答"跌到哪买"，而回答"什么证据出现了，才配得上多少仓位"。**

产物是一份 7–9 章的万字级研究报告，外加一份能每周照着执行的监测协议。

## 七条铁律

| # | 铁律 | 一句话 |
|---|---|---|
| 1 | 口径先行 | 同一指标多口径并列，不择优、不混用 |
| 2 | 参照系决定含义 | 便宜只在参照系内成立，至少给三个 |
| 3 | 归因优于描述 | 变化必须分解到贡献占比，各项之和 = 100% |
| 4 | 双层结论 | 已实现层与前瞻层分开下结论，允许相反 |
| 5 | 每个判断配证伪锚点 | 写不出证伪条件 = 结论不成立 |
| 6 | 主观概率不配仓位话语权 | 设计判断必须被折扣系数约束 |
| 7 | 框架匹配使用者纪律 | 结构与执行习惯冲突即失效 |

## 工作流

```
Phase 0  立题      口语问题 → 可证伪命题
Phase 1  估值坐标  双口径分位 / 极值空间 / PE-PB-ROE 三角 / ERP / 比价 / 横截面
Phase 2  归因分解  ln P = ln E + ln PE，跌因拆成贡献占比
Phase 3  传导链    需求源头 → 现金流转向 → 定价范式切换 → 标的层体检
Phase 4  判别      8 项判据双层加权 → 风险归属 → 剔除测试 → 案例校准 → 分歧点表态
Phase 5  情景定价  三情景映射 / 赔率双口径 / 隐含预期反推 / 敏感性矩阵 / 脆弱假设
Phase 6  节奏      三层底（估值/价格/时间）+ 历史破位样本统计
Phase 7  框架重建  风险预算定仓位 / 逻辑条件 C1–Cn / 三折扣触发矩阵 / 逻辑失效止损
Phase 8  成稿      章节张力衔接 / 来源标注 / 57 项自检
```

## 目录结构

```
.
├── SKILL.md                              技能主文件（触发条件 + 七条铁律 + Phase 0–8）
├── references/
│   ├── 01-caliber-and-valuation.md       口径体系、分位、ERP、PE-PB-ROE 三角、六个误用
│   ├── 02-attribution-and-discrimination.md  对数分解、双口径冲突处理、加权判据、剔除测试
│   ├── 03-scenario-engine.md             情景参数、赔率双口径、隐含预期反推、敏感性矩阵
│   ├── 04-execution-framework.md          风险预算推仓位、分档、三折扣、失效条件、监测协议
│   ├── 05-writing-patterns.md            章节张力、纠偏句式、诚实声明、七种退化模式
│   ├── 06-submission-checklist.md        提交前 57 项自检
│   └── 07-case-anatomy.md                参考样板逐章解剖（含可迁移手法清单）
├── scripts/
│   └── calc.py                           七个子命令的计算器（纯标准库，无依赖）
└── assets/
    └── report-skeleton.md                报告骨架模板，可直接复制改写
```

## 计算器

全部用 Python 标准库实现，无第三方依赖。中文输出，结果可直接粘进报告。

```bash
# 跌幅对数分解（输出贡献占比，校验 dlnE + dlnPE = dlnP）
python3 scripts/calc.py decompose --p0 4371.99 --pe0 55.145 --p1 2413.63 --pe1 26.908
# → 估值压缩 +120.8% / 盈利 −20.8%，判定"纯估值杀"

# 加权判据（双层得分、风险归属、剔除测试）
python3 scripts/calc.py score --file judgments.json

# 三情景（概率加权期望 + 赔率双口径 + 隐含预期反推）
python3 scripts/calc.py scenario --cur 2413.63 --pe-now 26.91 --file scenarios.json

# 敏感性矩阵（对角线锚定当前价格，自带校验）
python3 scripts/calc.py sensitivity --cur 2413.63 --pe-now 26.91 \
    --growths -0.20,-0.10,0,0.12,0.22 --pes 20,24,26.91,32,39

# 仓位三折扣
python3 scripts/calc.py position --incr 2 --kt 0.5 --kl 0.5 --kv 1.0

# 组合风险贡献与等效单一标的仓位
python3 scripts/calc.py risk --w1 0.12 --s1 0.36 --w2 0.10 --s2 0.39 --rho 0.86

# 反推市场隐含 PE
python3 scripts/calc.py implied --cur 2413.63 --pe-now 26.91 --g 0.12
```

`score` 的 JSON 格式（`layer` 用于双层分组，`weight` 合计应为 1）：

```json
[
  {"name": "现金流质量", "value": "0.35–0.40", "score": -1, "weight": 0.20, "layer": "前瞻层"},
  {"name": "盈利预期方向", "value": "近3月上修+5.30%", "score": 1, "weight": 0.15, "layer": "已实现层"}
]
```

## 安装

把本仓库整个目录放进技能目录即可：

```bash
git clone https://github.com/zhshy/deep-dive-investment-report.git \
  ~/.workbuddy/skills/deep-dive-investment-report
```

## 设计原则（为什么这样写）

1. **限制比规则更有用**。写"不要用固定百分比止损"比写"记得风控"有效，因为前者可判定。
2. **矛盾不抹平**。三个"便宜"指向不同参照系时，保留矛盾比强行统一更有信息量。
3. **自我限定增强可信度**。明确列出"只有两项经过统计检验/其余是设计判断"，比声称全都可靠更站得住。
4. **每个数字可复算**。表格合计必须等于列内之和；派生百分比只能用同一套权重再算一遍——这是版本 1.01 修订的重点，详见 `references/02` 的「算术自洽性」专节。

## 版本

见 [CHANGELOG.md](CHANGELOG.md)。当前 **V1.01**。

## 许可

MIT，见 [LICENSE](LICENSE)。

---

**免责声明**：本仓库是研究方法与写作工具的集合，不构成任何投资建议。所有案例数字仅用于演示方法，不构成对任何标的的判断。投资有风险，决策请自行承担。
