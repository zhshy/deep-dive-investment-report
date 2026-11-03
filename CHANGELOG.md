# Changelog

本项目的所有重要变更都记录在此文件。

格式参考 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.1.0/)，版本号采用 `1.0x` 递增。

## [1.01] - 2026-11-03

首个发布版本。

### Added

- `SKILL.md`：七条铁律 + Phase 0–8 工作流 + 交付物规格 + 资源索引
- `references/01-caliber-and-valuation.md`：五维口径体系、双口径分位、极值参照点、PE-PB-ROE 三角反推、ERP 与股息-国债、比价与横截面、六个常见误用
- `references/02-attribution-and-discrimination.md`：对数分解公式与参考实现、双口径冲突处理、加权判据表设计规则、风险归属分解、剔除测试、历史案例校准法、结构性反证构造法
- `references/03-scenario-engine.md`：情景参数设定、点位映射公式、概率加权期望、赔率双口径、乐观情景内部拆解、隐含预期反推、5×5 敏感性矩阵、三个脆弱假设、领跑指标
- `references/04-execution-framework.md`：风险预算推仓位（相关系数 → 组合风险 → 等效仓位 → 硬上限）、六档结构、价格档资格门槛、三折扣系数与触发矩阵、三层次逻辑失效止损、旧框架对照、周度监测协议、诚实声明模板
- `references/05-writing-patterns.md`：标题自带结论、章末张力四种模板、开篇三句话、段落四步结构、三步纠偏句式、并列呈现不可裁决状态、七种退化模式
- `references/06-submission-checklist.md`：提交前 57 项自检清单
- `references/07-case-anatomy.md`：参考样板逐章功能表、关键数字、十二条可迁移手法、样板自身遗留问题
- `scripts/calc.py`：`decompose` / `score` / `scenario` / `sensitivity` / `position` / `risk` / `implied` 七个子命令，纯标准库
- `assets/report-skeleton.md`：八章报告骨架模板

### Fixed

- **算术自洽性**：针对参考样板第五章"表内加权得分之和（−0.20）与正文所述合计（−0.679）不一致"的问题，新增专节规定：表格合计行必须等于列内之和；双层结论禁止混用「原始权重加总」与「层内归一化」两套尺度；派生百分比只能用同一套权重再算一遍。清单第 31–33 项强制复算。
- **隐含 PE 口径**：情景表的隐含 PE 改为可按各情景自身增速折算至前瞻盈利口径（`scenario` 子命令支持 JSON 中的 `g` 字段），避免"用当前盈利口径报前瞻 PE"的混淆。
- **负数参数解析**：`sensitivity` 的 `--growths` 支持以负号开头的取值，无需写成 `--growths=` 形式。

### Known limitations

- 情景概率为主观判断，未做历史频率或期权隐含分布校验
- 加权判据的阈值多属设计判断，未做历史分布校准，报告中须显式标注
- 历史破位样本统计受样本量限制（n≈9），应表述为数量级参考
