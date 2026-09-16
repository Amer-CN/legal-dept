<p align="center"><img src="https://capsule-render.vercel.app/api?type=waving&color=0:1B2A4E,100:2E5AAC&height=170&section=header&text=legal-dept&fontSize=54&fontColor=ffffff&animation=fadeIn" width="100%"></p>

<div align="center">

#### 企业法务部 AI 技能套装：从分诊定级到复核归档的完整法务工作流

**覆盖：部门总纲 × 8 岗位 × 四级风险定级 × 模板清单 × 行业包**

[![Typing SVG](https://readme-typing-svg.demolab.com?font=Fira+Code&size=20&pause=1200&color=1B2A4E&center=true&vCenter=true&width=620&lines=%E5%94%AF%E4%B8%80%E5%85%A5%E5%8F%A3%EF%BC%8C%E5%88%86%E8%AF%8A%E5%AE%9A%E7%BA%A7%EF%BC%8C%E5%A4%8D%E6%A0%B8%E5%BD%92%E6%A1%A3;8%E5%B2%97%E9%BD%90%E5%85%A8%EF%BC%8C%E7%BA%AF%E6%96%87%E6%9C%AC%E8%B7%A8%E5%B9%B3%E5%8F%B0%E5%8F%AF%E7%A7%BB%E6%A4%8D;%E5%9B%9B%E7%BA%A7%E9%A3%8E%E9%99%A9%E5%AE%9A%E7%BA%A7%EF%BC%8C%E5%8F%AA%E8%AE%B2%E7%90%86%E7%94%B1%E4%B8%8D%E6%89%93%E5%88%86)](https://git.io/typing-svg)

[![Version](https://img.shields.io/badge/Version-v1.0-1B2A4E?style=for-the-badge)](#-八维护与更新)
[![岗位编制](https://img.shields.io/badge/岗位编制-8岗-3B82F6?style=for-the-badge)](#-一部门定位)
[![风险定级](https://img.shields.io/badge/风险定级-四级-10B981?style=for-the-badge)](#-四风险标尺全套装唯一)
[![AgentSkills](https://img.shields.io/badge/AgentSkills-Standard-8B5CF6?style=for-the-badge)](https://agentskills.io)

</div>

---

面向企业客户的**部门制中文法务 skill 套装**：不是孤立的单卡片工具，而是「唯一入口（部门总纲）→ 分诊定级 → 岗位承办 → 汇办 → 复核强制门 → 归档台账」的完整流程。

> **版本 v1.0** · 知识截至 **2026-09-14** · 超 6 个月未复核，所有 skill 产出头部自动追加「知识可能过期，请复核」
>
> 更新归属：挂载方的法务部维护人（挂载时在此指定责任人姓名或岗位；未指定时默认为使用者本人）

## ✨ 一、部门定位

- **目标用户**：需要法务支持的企业（法务外包视角），尤其是没有专职法务团队的成长型企业。
- **硬约束**：**纯文本 markdown，跨平台可移植**，不依赖任何单一客户端的私有能力；不做量化风险打分，只做四级风险定级 + 判定理由。
- **定位说明**：这是法务部的**辅助工作流**，不是执业律师的替代品；对外正式出具法律意见、出庭、刑事辩护等仍须执业律师完成。
- **编制：8 岗齐全**——合同、劳动用工、争议解决、知识产权、公司治理、合规六个业务岗 + 法务前台（受理分诊兜底）+ 法务总监（复核把关升级）。

| 类别 | 岗位 | Skill ID |
|------|------|----------|
| 部门总纲（唯一入口） | 法务部 | `legal-dept` |
| 业务岗 | 合同法务岗 | `contract-counsel` |
| 业务岗 | 劳动用工岗 | `employment-counsel` |
| 业务岗 | 争议解决岗 | `dispute-counsel` |
| 业务岗 | 知识产权岗 | `ip-counsel` |
| 业务岗 | 公司治理岗 | `governance-counsel` |
| 业务岗 | 合规岗 | `compliance-counsel` |
| 受理分诊兜底 | 法务前台岗 | `legal-front-desk` |
| 复核把关升级 | 法务总监岗 | `legal-director` |

**工作流程**：

```
接待 → 分诊 → 定级 → 路由 → 岗位承办 → 汇办 → 复核强制门 → 归档台账
```

## 🗂️ 二、目录结构

<details>
<summary><b>目录树</b>（点击展开）</summary>

```
Legal Dept/
├── legal-dept/SKILL.md            # 部门总纲（唯一入口）：接待→分诊→定级→路由→汇办→复核门→归档
├── roles/
│   ├── contract-counsel/SKILL.md  # 合同法务岗
│   ├── employment-counsel/SKILL.md# 劳动用工岗
│   ├── dispute-counsel/SKILL.md   # 争议解决岗
│   ├── ip-counsel/SKILL.md        # 知识产权岗
│   ├── governance-counsel/SKILL.md# 公司治理岗
│   ├── compliance-counsel/SKILL.md# 合规岗
│   ├── legal-front-desk/SKILL.md  # 法务前台岗（受理分诊，无总纲环境时为兜底入口）
│   └── legal-director/SKILL.md    # 法务总监岗（复核强制门，升级与转介决策）
├── shared/
│   ├── risk-framework.md          # 四级风险等级（低/中/高/极高）+ 判定锚点
│   ├── templates/
│   │   ├── legal-opinion.md       # 法律意见书模板
│   │   ├── review-opinion.md      # 审查意见书模板
│   │   ├── referral-form.md       # 案件转介单模板（含敏感信息最小化提示）
│   │   └── intake-log.md          # 受理记录 / 案件台账模板
│   │   └── org-profile.md         # 组织基线与决策权限档案模板
│   └── checklists/
│       ├── contract-redlines.md   # 合同红线清单
│       ├── employment-redlines.md # 劳动红线清单
│       ├── evidence-preservation.md # 证据固定清单
│       └── bidding-compliance.md  # 招投标程序合规通用清单（跨行业；建工特有细节见 industries/construction/checklists/）
├── industries/README.md           # 行业包插槽规范
│   └── construction/              # 建筑工程行业包 construction v1.0
├── docs/
│   ├── trigger-experiment.md      # 触发实验记录
│   └── skill-quality-gate.md      # 技能自检门（维护用检查表，非运行时）
│   └── superpowers/specs/2026-09-14-legal-dept-design.md  # 设计文档
└── README.md
```

</details>

## 📦 三、挂载方式（跨平台）

本套装不依赖任何客户端私有机制，只需让宿主能读到这些 markdown 文件。按宿主能力选择：

| 宿主能力 | 做法 |
|----------|------|
| 支持目录式 skill | 把 `legal-dept/`、`roles/`、`shared/`、`industries/` 四个目录整体放入宿主的 skill / 规则 / 知识目录。宿主按 `SKILL.md` 的 frontmatter `name` 识别（`legal-dept`、`contract-counsel`、`employment-counsel`、`dispute-counsel`、`ip-counsel`、`governance-counsel`、`compliance-counsel`、`legal-front-desk`、`legal-director`） |
| 只支持单文件规则 | 把 `legal-dept/SKILL.md` 整体粘贴为规则或系统提示；岗位 skill 按需追加粘贴。`shared/` 下的模板与清单按需以文件路径引用或一并粘贴 |
| 支持项目级知识库 | 把本仓库根目录挂为知识库/工作目录，让宿主按路径检索。此时所有 `shared/...` 相对路径引用可直接生效 |
| 显式调用（不依赖自动触发） | 任何宿主下，用户说出下列任一表述即视为显式调用（见下方） |

**显式调用**：

- 总纲：`legal-dept`、`用法务部`、`走法务流程`、`按法务部流程处理`
- 岗位：`contract-counsel`、`employment-counsel`、`dispute-counsel`、`ip-counsel`、`governance-counsel`、`compliance-counsel`、`legal-front-desk`、`legal-director`，或中文「合同法务岗 / 劳动用工岗 / 争议解决岗 / 知识产权岗 / 公司治理岗 / 合规岗 / 法务前台 / 法务总监」
- 独立调用岗位时，岗位产出头部必须自标「未经分诊」（显式降级，不静默）。

### 3.1 平铺部署与资源同步规则

- 平铺部署：宿主要求 skill 为直接子目录时，把 `legal-dept/` 与 8 个岗位目录平铺到宿主 skill 目录；每个岗位目录内附带 `shared/` 与 `industries/` 副本。平铺只改变装载形态，不改变职责与流程。
- 资源基准：仓库形态下，所有 `shared/...`、`industries/...` 引用以套装根目录为基准；平铺部署形态下，以各 skill 目录内附带的副本为基准。两处内容必须一致。
- 同步规则：主仓库为唯一事实来源；平铺副本、粘贴规则等均为派生物。主仓库更新后必须重新同步部署副本；发现不一致时以主仓库为准。

### 3.2 命名冲突处理规则

- 本套装 name 统一加 `legal-` 前缀（总纲）或 `-counsel` 后缀（岗位），降低与用户既有 skill 重名的概率。
- 与用户既有 skill 重名时，以本套装目录路径为准，并在调用时显式写明路径（如 `roles/contract-counsel/SKILL.md`）。
- 如宿主按 frontmatter `name` 唯一索引且无法区分，仅改目录名无效：须同步修改对应 SKILL.md 的 `name`，并同步更新套装内对该 name 的文字引用；或先卸载同名 skill。不建议在未同步引用的情况下改名。

## 📏 四、风险标尺（全套装唯一）

只使用 **低 / 中 / 高 / 极高** 四级，判定锚点见 `shared/risk-framework.md`。

| 等级 | 落盘术语 |
|------|----------|
| 🟢 低 | 低 |
| 🟡 中 | 中 |
| 🟠 高 | 高 |
| 🔴 极高 | 极高 |

- 禁用一切 0-100 量化打分。
- 禁用颜色词落盘（「绿/黄/红」仅为分诊口头语，落盘一律回写四级术语）。
- 定级必须写出「判定锚点编号 + 一句话事实」，禁止只写等级。

## 🔒 五、三条贯穿硬规则

| # | 规则 | 说明 |
|---|------|------|
| 1 | **来源锁定** | 引用法条写明法律名称 + 条文编号 + 关键原文；记忆来源标「待核实」并给出官方核验渠道 |
| 2 | **免责声明** | 所有产出尾部固定携带「本文件由 AI 生成，不构成法律意见，不能替代执业律师的正式意见」 |
| 3 | **能力圈外转介** | 刑事辩护、出庭代理、税务筹划、专业鉴定、异地执行等，出具《案件转介单》，不硬答 |

## 🔍 六、法规真实性硬步骤

- 有检索能力的宿主：引用法条前必须联网核验现行有效版本，并写明核验日期。
- 无检索能力的宿主：产出整体降级，头部显著声明「法条未经核验」，并逐条标注「待核实」。
- 用户侧核实渠道见 `legal-dept/SKILL.md` 第 7 段（用户侧核实工作流）。

## 🧩 七、已挂载行业包

| 包名 | 版本 | 知识截至日期 | 责任人 |
| --- | --- | --- | --- |
| construction | v1.0 | 2026-09-14 | 挂载方指定（默认使用者本人） |

挂载规范见 `industries/README.md`。

## 🛠️ 八、维护与更新

- 责任人：由挂载方指定（见文首「更新归属」）。责任人为空时，默认使用者本人负责。
- 复核周期：至少每 6 个月复核一次法规相关内容与清单；监管规则发生重大变化时立即复核。
- 过期处理：知识截至日期超 6 个月未复核，所有产出头部自动追加「知识可能过期，请复核」；未复核期间引用法条一律标「待核实」。
- 台账与留痕：受理与办理记录见 `shared/templates/intake-log.md`；高与极高风险事项须留存交付物原文与复核意见。

## ⚖️ 九、免责

> 本套装及其全部产出物由 AI 生成，不构成法律意见，不能替代执业律师的正式意见。
> 最终决策与签署前请经执业律师审阅。
> 使用本套装即表示理解：法律适用具有个案性，法条与地方执行口径会变化，
> 使用前须按本套装要求完成核验，因未核验导致的后果由使用者承担。

<div align="center">

---

Made by [@Amer-CN](https://github.com/Amer-CN)

*部门制法务工作流，仅供参考，正式口径请以执业律师意见为准。*

</div>
