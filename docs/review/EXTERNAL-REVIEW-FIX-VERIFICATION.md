# 外部评审修复 · 回归验证记录

- 验证对象：`outputs/external-review-39abd04.md` 的 C1–C3、I1–I4、M1
- 基线提交：`39abd04`
- 验证日期：2026-09-14
- 验证环境：Windows / PowerShell 7，工作区 `F:\AIXM\Legal Dept`，分支 `master`
- 本轮改动：全部留在工作区，未提交、未推送
- 外部原报告 `outputs/external-review-39abd04.md` 只读未改（SHA256 `517E22002B5B9792EAFFA619C91D41BBEFC6300676C8AF6A17179996BB62276A`）

## 0. 验证方法声明（重要）

本文件所载验证**全部为静态检查**：`Select-String` 关键词/正则命中、PowerShell 脚本做词表差集比对、`git diff` 核对改动范围。

静态检查能证明的是：**文本内容与结构已按要求落盘**。
静态检查**不能**证明的是：宿主是否会在真实会话中据此产生预期的动态行为。凡属动态能力，本文件一律单列为「未验证边界」（见第 3 节），不冒充已验证结论。

## 1. 逐条验收结论

| # | 对应问题 | 结论 | 验证方式 |
| --- | --- | --- | --- |
| 1 | C1 合同岗旧编制路由 | 通过 | 静态检查：禁词无命中 + 应有词命中 |
| 2 | C2 前台缺岗代办 / 复核承接 | 通过 | 静态检查：禁词无命中 + 应有词命中（3 文件） |
| 3 | C3 治理/合规专项清单断链 | 通过 | 静态检查：I/J 节真实存在，指向方引用的专项均有实体条目 |
| 4 | I1 部署与同步规则 / name 索引 | 通过 | 静态检查：README 与实验记录关键词命中 |
| 5 | I2 触发词覆盖与重叠分流 | 通过（静态） | 脚本比对差集 0；2.1 四组规则落盘 |
| 6 | I3 法规表无源断言 | 通过 | 静态检查：禁词无命中 + 核验留痕命中 |
| 7 | I4 安全生产条件挂接 | 通过 | 静态检查：交叉引用双向存在 |
| 8 | M1 意见书模板用语 | 通过 | 静态检查：用途行已改，禁词无命中 |

**8/8 通过（均为静态检查口径）。**

## 2. 验证命令与真实输出

### 验收 1 · C1

```powershell
Select-String -Path 'roles/contract-counsel/SKILL.md' -Pattern '首期无独立岗'
Select-String -Path 'roles/contract-counsel/SKILL.md' -Pattern 'governance-counsel|ip-counsel'
```

真实输出：

```
[PASS] 禁词 首期无独立岗                                      expect=MISS got=MISS
[PASS] 应有 governance/ip-counsel                       expect=HIT got=HIT
         roles\contract-counsel\SKILL.md:26: 涉及员工保密、竞业限制的保密事项，交回总纲路由至 `employment-counsel`；涉及商业秘密体系搭建的，与 `ip-counsel` 协同，由总纲汇办。
         roles\contract-counsel\SKILL.md:82: - 涉及股权归属处分 → 转 `governance-counsel`（经总纲）协同处理；涉及知识产权归属处分 → 转 `ip-counsel`（经总纲）协同处理；两者均按实际风险定级并走对应复核门。
```

补充：全仓库（排除 `.work/`、`.git/`、`outputs/`，并排除本验证文件自身）检索 `首期无独立岗|按对应岗位骨架承办|首期不挂载` 无命中。本文件命中 5 处，均为引用禁词本身的说明文字，非套装内容。

### 验收 2 · C2

```powershell
Select-String -Path 'roles/legal-front-desk/SKILL.md' -Pattern '按对应岗位骨架承办'
Select-String -Path 'roles/legal-front-desk/SKILL.md','roles/legal-director/SKILL.md','legal-dept/SKILL.md' -Pattern '对应岗位未挂载，未作实体分析|未经独立复核'
```

真实输出：

```
[PASS] 禁词 按对应岗位骨架承办                                   expect=MISS got=MISS
[PASS] 应有 未作实体分析|未经独立复核                               expect=HIT got=HIT
         roles\legal-front-desk\SKILL.md:15: > **兜底入口职责**：…宿主同时未挂载对应岗位 skill 时，本岗只完成受理、事实采集与定级，不代替承办岗作实体分析；按第 5 段降级交付物规范出具受理记录，标注「对应岗位未挂载，未作实体分析」。事项紧急、高/极高风险或用户要求实体意见时，出具《案件转介单》。此为显式降级，不得静默处理。
         roles\legal-front-desk\SKILL.md:34: 5. **路由派单**：按下表指向承办岗；宿主未挂载对应岗位时，不代替承办岗作实体分析，按第 5 段降级交付物规范出具受理记录并标注「对应岗位未挂载，未作实体分析」。
         roles\legal-front-desk\SKILL.md:35: 6. **复核门交接**：…复核方（总监/总纲）未挂载时，标注「未经独立复核」并建议人工复核；高/极高未过复核仍按「未经复核」处理。
         roles\legal-front-desk\SKILL.md:86: - 产出头部标注：独立调用时标「未经分诊」；对应岗位未挂载时标「对应岗位未挂载，未作实体分析」；高/极高未过复核时标「未经复核」；复核方（总监/总纲）均未挂载时标「未经独立复核」。
         roles\legal-director\SKILL.md:49: - **复核降级链**：中风险简复核先由总纲执行，总纲不可用由本岗代行；高/极高复核由本岗执行，本岗不可用由总纲代行；总纲与本岗均不可用时，承办岗标注「未经独立复核」（高/极高标「未经复核」）并建议转人工律师。
         legal-dept\SKILL.md:78: - **复核方不可用降级**：中风险简复核由总纲执行，总纲不可用时由 `legal-director` 代行；高/极高复核由 `legal-director` 执行，其不可用时由总纲代行；两者均不可用时，承办岗标注「未经独立复核」（高/极高标「未经复核」）并建议转人工律师。
[PASS] 应有 降级交付物                                       expect=HIT got=HIT
         roles\legal-front-desk\SKILL.md:82: - 对应岗位未挂载时的降级交付物：《受理记录》+《待办与待核实清单》（待办事项、需挂载的岗位、需核验的官方渠道、建议外部律师介入的条件）；不输出实体法律结论；用户要求实体意见时出具《案件转介单》。
[PASS] 应有 总监代行简复核                                     expect=HIT got=HIT
         roles\legal-director\SKILL.md:15: > **复核兜底职责**：…总纲未挂载时，中风险简复核由本岗代行并标注「已过简复核（总监代行）」。
         roles\legal-director\SKILL.md:44: | 中 | 总纲自查（简复核）；总纲未挂载时由本岗代行 | 已过简复核；代行时标注「已过简复核（总监代行）」 |
[PASS] 应有 复核降级链                                       expect=HIT got=HIT
         roles\legal-director\SKILL.md:49: - **复核降级链**：…
```

C2 要求的三项落点均已闭合：**缺岗不代办实体分析**（前台第 15/34 段）、**降级交付物与转介**（前台第 82 段）、**总纲缺失时代行简复核**（总监第 15/44 段）。

### 验收 3 · C3

```powershell
Select-String -Path 'shared/checklists/contract-redlines.md' -Pattern '对赌与回购|反商业贿赂'
```

真实输出：

```
[PASS] 应有 对赌与回购                                       expect=HIT got=HIT
         shared\checklists\contract-redlines.md:73: ## I. 股权与投融资专项条款（股权转让、投融资、对赌与回购）
         shared\checklists\contract-redlines.md:77: - I3 对赌与回购：触发条件、回购义务主体、价格与利息、履行期限写明；涉及公司回购的，核对减资与债权人程序风险。
[PASS] 应有 反商业贿赂                                       expect=HIT got=HIT
         shared\checklists\contract-redlines.md:81: ## J. 廉洁与合规专项条款（反商业贿赂、第三方合规、审计检查）
         shared\checklists\contract-redlines.md:83: - J1 反商业贿赂/廉洁承诺：禁止回扣、佣金、礼品招待、赞助、渠道返利等变相利益；覆盖员工、高管、关联方与第三方。
[PASS] I 节标题                                          expect=HIT got=HIT
[PASS] J 节标题                                          expect=HIT got=HIT
```

断链两侧均已核对：

- 指向方：`roles/compliance-counsel/SKILL.md:58`（第 3 段指针）与 `:70`（第 4 段脚手架第 4 步）指向「合同红线清单（数据处理、保密、反商业贿赂条款部分）」；`roles/governance-counsel/SKILL.md:56`（第 3 段指针）与 `:68`（第 4 段脚手架第 4 步）指向「合同红线清单（股权转让、投融资、对赌与回购条款部分）」。
- 被指向方：`contract-redlines.md` 新增 I 节（I1–I5）、J 节（J1–J5），上述两个专项均有实体条目，指针不再悬空。

### 验收 4 · I1

```powershell
Select-String -Path 'README.md' -Pattern '平铺部署|资源基准|同步规则|按 frontmatter `name` 唯一索引'
Select-String -Path 'docs/trigger-experiment.md' -Pattern '平铺为宿主适配形态，主仓库仍为唯一事实来源|精确词表覆盖属于静态检查'
```

真实输出：

```
[PASS] README 平铺部署                                expect=HIT got=HIT
         README.md:62: ### 3.2 平铺部署与资源同步规则
         README.md:64: - 平铺部署：宿主要求 skill 为直接子目录时，把 `legal-dept/` 与 8 个岗位目录平铺到宿主 skill 目录；每个岗位目录内附带 `shared/` 与 `industries/` 副本。平铺只改变装载形态，不改变职责与流程。
[PASS] README 资源基准                                expect=HIT got=HIT
         README.md:65: - 资源基准：仓库形态下，所有 `shared/...`、`industries/...` 引用以套装根目录为基准；平铺部署形态下，以各 skill 目录内附带的副本为基准。两处内容必须一致。
[PASS] README 同步规则                                expect=HIT got=HIT
         README.md:66: - 同步规则：主仓库为唯一事实来源；平铺副本、粘贴规则等均为派生物。主仓库更新后必须重新同步部署副本；发现不一致时以主仓库为准。
[PASS] README name 唯一索引修正                         expect=HIT got=HIT
         README.md:72: - 如宿主按 frontmatter `name` 唯一索引且无法区分，仅改目录名无效：须同步修改对应 SKILL.md 的 `name`，并同步更新套装内对该 name 的文字引用；或先卸载同名 skill。不建议在未同步引用的情况下改名。
[PASS] README 行业包目录树                              expect=HIT got=HIT
         README.md:43: │   └── construction/              # 建筑工程行业包 construction v1.0
[PASS] 实验记录 平铺=宿主适配形态                             expect=HIT got=HIT
         docs\trigger-experiment.md:256: - 安装方式：…已改为平铺。平铺为宿主适配形态，主仓库仍为唯一事实来源。
[PASS] 实验记录 静态检查边界                                expect=HIT got=HIT
         docs\trigger-experiment.md:285: - 精确词表覆盖属于静态检查，不作为自动触发命中率的结论；自动触发仍需真实会话复测。
```

### 验收 5 · I2（脚本比对，非关键词）

触发词覆盖用脚本比对，脚本逻辑：从 `legal-dept/SKILL.md` 与 8 个 `roles/*/SKILL.md` 的 frontmatter `description` 中提取「中文触发词：」之后的顿号分隔词表，求八岗并集与总纲的差集。

复现用脚本（本轮在仓库外临时目录执行，未写入仓库）：

```powershell
function Get-TriggerWords([string]$path) {
    $desc = (Get-Content $path -TotalCount 20 | Where-Object { $_ -match '^description:' } | Select-Object -First 1)
    $idx = $desc.IndexOf('中文触发词：')
    $tail = $desc.Substring($idx + '中文触发词：'.Length).TrimEnd('。', ' ')
    return $tail.Split('、') | ForEach-Object { $_.Trim() } | Where-Object { $_ -ne '' }
}
$master = Get-TriggerWords 'legal-dept/SKILL.md'
$all = @()
foreach ($d in (Get-ChildItem roles -Directory)) { $all += Get-TriggerWords "roles/$($d.Name)/SKILL.md" }
$missing = ($all | Sort-Object -Unique) | Where-Object { $master -notcontains $_ }
"差集: $($missing.Count) 词"; $missing -join '、'
```

真实输出（当前工作区）：

```
总纲触发词数: 243
  compliance-counsel     50 词
  contract-counsel       22 词
  dispute-counsel        30 词
  employment-counsel     26 词
  governance-counsel     41 词
  ip-counsel             36 词
  legal-director         19 词
  legal-front-desk       16 词
八岗触发词去重并集: 236
--- 八岗有、总纲无（差集）: 0 词 ---
--- 原三岗差集: 0 词（基线应为 0）---
--- 新增五岗差集: 0 词（修复前应为 59）---
结论: 总纲对八岗触发词全覆盖（差集 0）
```

同一脚本对基线 `39abd04`（`git archive` 解出到临时目录后运行）的真实输出：

```
总纲触发词数: 184
...
--- 八岗有、总纲无（差集）: 59 词 ---
保密信息、比较广告、吹哨、等级保护、定级、多岗意见冲突、二次审查、法律风险评估、法律事项受理、法律问题咨询、法务部流程、法务部质量抽查、法务分诊、反不正当竞争、分歧裁决、分诊、复核不通过、该找哪个律师、个人隐私、公司解散、股东出资、股东资格、关键信息基础设施、广告、价格合规、决议瑕疵、绝对化用语、滥用市场支配地位、廉洁承诺、垄断协议、明码标价、侵权投诉、权利归属、商标许可、商业诋毁、商业贿赂、上报决策层、算法推荐、台账检查、同业竞争、未经复核、问法务、宣传用语、用户协议、有奖销售、章程备案、找法务、这事归谁管、著作权许可、知识产权布局、知识产权质押、注册资本、专利许可、专项合规、转介把关、字号争议、自动化决策、走法务流程、作品侵权
--- 新增五岗差集: 59 词（修复前应为 59）---
结论: 仍有缺失
```

**基线 59 词缺口 → 当前 0 词缺口**，与外部报告 I2 所述「新增五岗精确词表比对缺 59 词」数量一致。

2.1 重叠分流：

```powershell
Select-String -Path 'legal-dept/SKILL.md' -Pattern '2\.1 重叠触发词分流'
```

真实输出：

```
[PASS] 2.1 重叠触发词分流                                expect=HIT got=HIT
         legal-dept\SKILL.md:45: ### 2.1 重叠触发词分流（集中定义）
--- 2.1 四组分流规则 ---
         47: - 劳动仲裁按程序阶段：仲裁申请前（含协商、证据固定、方案设计）由 `employment-counsel` 承办；已申请或已立案后转 `dispute-counsel`，劳动岗提供事实与证据支持。
         48: - 保密按主体与对象：员工保密与竞业限制 → `employment-counsel`；交易合同保密条款 → `contract-counsel`；商业秘密体系、许可与侵权应对 → `ip-counsel`。
         49: - 尽职调查按对象：投融资交易对手的商业与法律尽调 → `governance-counsel`；供应商、代理商、中介的合规尽调 → `compliance-counsel`。
         50: - 不正当竞争按权利与行为：仿冒、字号/域名等涉权利冲突 → `ip-counsel`；商业诋毁、虚假宣传等经营行为合规 → `compliance-counsel`。
```

四组（劳动仲裁 / 保密 / 尽调 / 不正当竞争）与外部报告 I2 要求的分流维度逐条对应。

**边界**：以上是**词表静态覆盖**与**分流规则落盘**的验证，**不是**自动触发命中率的结论。总纲 description 已达 243 词（基线 184 词），截断风险上升，这一点已记入 `docs/trigger-experiment.md` 第 6.4 节遗留事项。

### 验收 6 · I3（2026-09-14 修订：法释25号与724号两行已核验）

```powershell
Select-String -Path 'industries/construction/regulations.md' -Pattern '现行有效，条号以官方库为准|有修正史，修正状态以官方库为准'
Select-String -Path 'industries/construction/regulations.md' -Pattern '待核验|待核实|核验日期'
Select-String -Path 'industries/construction/regulations.md' -Pattern '已核验'
```

真实输出（摘要式呈现，行号区间为折叠写法；原始控制台输出为完整长行，判定结论一致）：

```
[PASS] 已删除的无源断言 expect=MISS got=MISS
[PASS] 应有 待核验/待核实/核验日期 expect=HIT got=HIT
          industries\construction\regulations.md:4: - 使用规则：…凡未联网核验现行有效版本的，一律标「待核实」。…
          industries\construction\regulations.md:5: - 核验留痕：已核验条目标注来源与核验日期；未注明的，引用前必须到官方渠道核验，不得断言“现行有效”。
          industries\construction\regulations.md:9-12/15-17: 各未核验行均为“待核验：引用前到官方库核验并写来源与核验日期”口径；第18行为“部门规章，具体条款标「待核实」；引用前核验”
          industries\construction\regulations.md:13: 法释25号行已改为“已核验：最高人民法院官网权威发布（2020年12月25日通过，自2021年1月1日起施行），核验日期2026-09-14”
          industries\construction\regulations.md:14: 724号行已改为“已核验：中国政府网政策库页面（发文字号国令第724号，2019年12月4日通过，自2020年5月1日起施行），核验日期2026-09-14”
[PASS] 应有 核验留痕段 expect=HIT got=HIT
[INFO] “自2021年1月1日起施行”“自2020年5月1日起施行”现仅出现在带来源+核验日期的“已核验”行中，不再视为无源断言
```

说明：10 项中 2 项（法释25号、724号）已联网核验并留痕来源与核验日期2026-09-14；其余 8 项为待核验留痕口径。第 4、5 行残留的「现行有效」二字属规则文本本身（禁止性表述），不构成违规。

**边界**：仅上述 2 项做过官方源联网核验；其余 8 项法条实体正确性与现行有效性仍为未验证项。

### 验收 7 · I4

```powershell
Select-String -Path 'industries/construction/SKILL.md' -Pattern 'completion-settlement-safety\.md'
Select-String -Path 'industries/construction/checklists/completion-settlement-safety.md' -Pattern '本节同时供'
```

真实输出：

```
[PASS] SKILL.md 指向安全清单                            expect=HIT got=HIT
         industries\construction\SKILL.md:35: - 安全生产条件（许可证、持证在岗、安全交底、隐患排查）→ `compliance-counsel` + 本包 `checklists/completion-settlement-safety.md`“安全生产”节，并同步核对 `checklists/bidding-compliance.md` 中的资质与许可证项。
         industries\construction\SKILL.md:37: - 工程款催收、结算争议、索赔谈判、已进入诉讼仲裁 → `dispute-counsel` + 本包 `checklists/completion-settlement-safety.md`。
[PASS] 安全节交叉引用                                    expect=HIT got=HIT
         industries\construction\checklists\completion-settlement-safety.md:20: > 本节同时供 `compliance-counsel` 安全生产条件自查加载；资质与许可证有效期同步核对 `checklists/bidding-compliance.md`。
```

交叉引用为双向：包 SKILL.md 第 35 行的安全生产条件项指向安全清单「安全生产」节；安全节第 20 行反向声明其供 `compliance-counsel` 加载，并指向资质/许可证项。原「安全生产条件」与「招投标文件、资质证照」混在一条的写法已拆分为两条。

### 验收 8 · M1

```powershell
Select-String -Path 'shared/templates/legal-opinion.md' -Pattern '辅助意见草稿'
Select-String -Path 'shared/templates/legal-opinion.md' -Pattern '用途：.*正式意见'
```

真实输出：

```
[PASS] 应有 辅助意见草稿                                  expect=HIT got=HIT
         shared\templates\legal-opinion.md:4: - 用途：对具体法律问题出具的辅助意见草稿（承办岗产出，按风险等级过复核门）
[PASS] 禁词 用途行含正式意见                                expect=MISS got=MISS
```

补充：该模板第 70 行尾部免责声明「…不能替代执业律师的正式意见」保留未动——属免责声明固定文本，与总纲第 20/163 行一致，不在 M1 修改范围内。

## 3. 仍未验证的能力边界

以下能力**本轮未验证**，静态检查无法覆盖，不得据本文件推断其有效：

1. **自然语言自动触发命中率（未验证）**：本轮只验证了总纲/八岗触发词表的静态覆盖（差集 0）。宿主是否会在用户说出自然话术时真的命中总纲，需部署到宿主 skill 目录后在真实会话中用 3–5 句典型话术复测。此项为外部报告 I2 明确要求「不据此断言自动漏触发」的对应边界，`docs/trigger-experiment.md:285` 已写明该声明。
2. **法条联网核验端到端（未验证）**：无检索能力环境下的降级路径（头部声明「法条未经核验」）已在设计中，但「引用前联网核验 + 写明核验日期」的完整链路未实测。`docs/trigger-experiment.md` 的 F2 维持原结论。
3. **法条实体正确性（未验证）**：`industries/construction/regulations.md` 中 10 项法规的真实现行效力、条号、修正状态均未联网核验，全部标记为待核验。本项属评审简报第 5 节声明的不审范围。
4. **`gsxt.gov.cn` 可用性（未验证）**：F3（HTTP 521）维持原结论，未重测。
5. **description 截断风险（未验证）**：总纲触发词已从 184 增至 243，截断风险上升，实际是否被宿主截断未测。
6. **宿主 skill 加载（未重测）**：`docs/trigger-experiment.md` 第 6.1 节曾实测 9/9 可加载，本轮未重跑该实测。
7. **降级链动态行为（未验证）**：C2 新增的「缺岗不代办」「总监代行简复核」「未经独立复核」标注均为文本规则，未在缺岗环境下实跑验证产出头部标注是否真的出现。

## 4. 改动范围与禁止文件核对

`git status --porcelain`（本轮结束时）：

```
 M README.md
 M docs/trigger-experiment.md
 M industries/construction/SKILL.md
 M industries/construction/checklists/completion-settlement-safety.md
 M industries/construction/regulations.md
 M legal-dept/SKILL.md
 M roles/contract-counsel/SKILL.md
 M roles/legal-director/SKILL.md
 M roles/legal-front-desk/SKILL.md
 M shared/checklists/contract-redlines.md
 M shared/templates/legal-opinion.md
?? .workbuddy-ai/
?? docs/review/
?? outputs/
```

- 11 个已跟踪文件被修改，全部落在简报「允许改动的文件」清单内。
- 新增 1 个文件：`docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md`（本文件），属简报「允许改动的文件」清单末项。
- 同目录另有 `docs/review/EXTERNAL-REVIEW-BRIEF.md`，为本任务之前既有的未跟踪文件，本轮未修改。
- `outputs/external-review-39abd04.md`、`.workbuddy-ai/` 为任务开始前既有的未跟踪内容，本轮未修改。
- 禁止文件核对：`outputs/external-review-39abd04.md` 未被修改（mtime `2026-09-14 14:30:14`，SHA256 见文首）；`.work/` 下历史文件未改（`.work/` 已在 `.gitignore` 中，不入库）。
- 未提交、未推送，改动全部留在工作区。

## 5. 遗留事项

1. 第 3 节全部 7 项未验证边界，其中第 1、2 项需在具备宿主会话与检索能力的环境下复测。
2. 总纲 description 243 词，截断风险已上升为待观察项，若触发不稳优先改用显式调用。
3. 本轮验证为静态检查口径，8 条验收的「通过」均指文本与结构落盘正确，不含动态行为保证。

---

AI生成，不构成法律意见。

## 补记（2026-09-14）：法条联网核验实测 + C2 降级链模拟走查

本节为第 3 节遗留事项的补测记录，属**追加验证**，不改变前文任何结论；前文第 1 节 8/8 静态结论与第 3 节未验证边界继续有效（本节仅将其中部分条目由「未验证」推进为「已核验」或「静态走查通过」，动态宿主行为仍不在此列）。

### 一、法条联网核验实测（对应第 3 节遗留事项 2、3）

1. **《保障农民工工资支付条例》（国务院令第724号）— 已核验。** 来源：中国政府网政策库页面。文号（国令第724号）、2019年12月4日国务院第73次常务会议通过、自2020年5月1日起施行三项与条例全文一致。核验日期：2026-09-14。已据此把 `industries/construction/regulations.md` 对应行的状态备注由「待核验 + 引用前复核」改为「已核验：中国政府网政策库页面（发文字号国令第724号，2019年12月4日通过，自2020年5月1日起施行），核验日期2026-09-14；现行版本引用前复核」。

2. **《最高人民法院关于审理建设工程施工合同纠纷案件适用法律问题的解释（一）》（法释〔2020〕25号）— 已核验。** 来源：最高人民法院官网权威发布。文号（法释〔2020〕25号）、2020年12月25日由最高人民法院审判委员会第1825次会议通过、自2021年1月1日起施行三项与解释全文一致。核验日期：2026-09-14。已据此把 `industries/construction/regulations.md` 对应行的状态备注改为「已核验：最高人民法院官网权威发布（2020年12月25日通过，自2021年1月1日起施行），核验日期2026-09-14；是否被后续解释调整引用前复核」。要点印证：该解释第35–41条印证民法典第807条建设工程价款优先受偿权的要点，含**18个月**行使期限上限；第1条印证无资质、转包、违法分包情形下合同无效。

3. **民法典第807条原文 — 未单独抓取，仍按「待核实」处理。** 本轮仅取得法院解释页面中对该条的引用印证（见上条），未直接抓取国家法律法规数据库的条文原文，故 `regulations.md` 中该行状态备注**不改动**，维持「待核实」。

### 二、C2 降级链三场景静态走查（对应第 3 节遗留事项 7）

对 C2 新增的降级规则按三个缺岗场景做**静态文本走查**，三场景均有落点：

- **场景 A：无总纲 + 对应承办岗缺失。** 前台只受理、采集事实、定级，不作实体结论；产出头部标注「对应岗位未挂载，未作实体分析」；高/极高风险转介（出具《案件转介单》）。落点：`roles/legal-front-desk/SKILL.md` 第 15、34、82、86 段。
- **场景 B：复核方（总纲与总监）全缺。** 中标「未经独立复核」；高/极高风险标「未经复核」并建议转律师。落点：`roles/legal-director/SKILL.md` 第 49 段、`legal-dept/SKILL.md` 第 78 段、`roles/legal-front-desk/SKILL.md` 第 35、86 段。
- **场景 C：岗位被独立调用（不经总纲分诊）。** 同场景 B 标注口径。落点：`roles/legal-front-desk/SKILL.md` 第 86 段。

**局限（必须写明）**：以上为文本规则走查，规则本身无法自感知宿主环境，只能依赖调用方如实告知缺岗情况；真实缺岗环境下的动态运行（产出头部是否真的出现相应标注）仍需宿主实测，第 3 节遗留事项 7 的动态部分**未因本节而闭合**。

### 三、本节边界

本次只做**静态走查**与**官方源核验**两件事，不冒充动态宿主测试。未在本节验证的事项：自动触发命中率、无检索环境降级路径端到端、`gsxt.gov.cn` 可用性、description 截断、宿主 skill 加载、降级链动态行为。

## 附录（2026-09-15）：对标深读采纳包落盘 + 借鉴署名 + 自我纠正

> 本附录为**追加记录**，不改变前文第 1–5 节与两处补记的任何结论。核验过程与证据见 `.work/benchmark-reverification-20260915.md`（MCP 逐文件核验，9 项成立 + 3 修正）。
> 版权口径（硬）：学结构和方法，不搬原文。Apache-2.0 可借鉴改写后自用并署名；AGPL-3.0 只学思想、自己重写，不抄原文；无 LICENSE 的按默认版权所有处理，只学思想、不搬原文、署名致谢。本附录全部落盘内容均为中文重写，未复制任何上游原文整句。

### 一、本轮落盘的四个采纳包与改动文件

| # | 采纳包 | 落盘位置 | 改动文件 |
| --- | --- | --- | --- |
| ① | 证据分类 A–E（材料明示 / 核验源明示 / 有限推定 / 未找到 / 假设） | 总纲第 4 段新增 4.1 节；第 0 段第 1 条一句呼应 | `legal-dept/SKILL.md` |
| ② | 隐性风险专项检查（定义陷阱 / 交叉引用链 / 存活条款 / 并入引用 / 术语漂移 + 危险措辞红旗清单） | 合同红线清单新增 K 节 | `shared/checklists/contract-redlines.md` |
| ③ | 标准立场列（应然标准 + 修改进路 + 可退让底线三件套） | 审查意见书风险清单表新增「标准立场」列及写法要求 | `shared/templates/review-opinion.md` |
| ④ | 转介三级触发分级（必须转介 / 强烈建议转介 / 考虑转介） | 转介单第三节内新增 3.1–3.3 分级 | `shared/templates/referral-form.md` |

未采纳项（维持不动）：定级说理四维度（后果 / 可能性 / 金额 / 不对等）仅作为说理用，不改动 `shared/risk-framework.md` 锚点结构；frontmatter 许可字段、合同岗「先问组织范本」两项本轮不在采纳包内。

### 二、借鉴来源署名与许可

| 来源 | 采用的什么 | 许可 | 处理方式 |
| --- | --- | --- | --- |
| `lawve-ai/awesome-legal-skills` → `skills/source-locked-verification-larissa-meredith-flister/SKILL.md`（作者 Larissa Meredith-Flister，version 2026-05-13） | 五级证据分类的**分类思想**与「缺货就说没找到」机制 | AGPL-3.0 | 只学思想，**中文重写为 A–E 企业法务版**（材料明示 / 核验源明示 / 有限推定 / 未找到 / 假设），未抄原文 |
| `lawve-ai/awesome-legal-skills` → `skills/nda-reviewer-anthropic/SKILL.md`（作者 Anthropic，version 2026.01.30） | 标准立场 + 红线进路 + 对方拒绝时的 fallback | Apache-2.0 | 借鉴结构，改写后自用；风险清单「标准立场」列的三件套写法源自此 |
| `lawve-ai/awesome-legal-skills` → `skills/playbook-reviewer-anthropic/SKILL.md`（作者 Anthropic，version 2026.01.30） | 「先找组织 playbook，没有才用通用标准并声明」；Redline 的 Priority / Fallback 结构 | Apache-2.0 | 同上，标准立场的依据优先级写入审查意见书模板 |
| `lawve-ai/awesome-legal-skills` → `skills/legal-risk-assessor-anthropic/SKILL.md`（作者 Anthropic，version 2026.01.30） | 转介三级触发（Mandatory / Strongly Recommended / Consider）与每级情形 | Apache-2.0 | 按中国法务能力圈改写为必须转介 / 强烈建议转介 / 考虑转介，未抄英文原表 |
| `zubair-trabzada/ai-legal-claude`（1733 ★，4 commits，最后 2026-03-27） | 隐性风险检查维度、加权打分方法论（用于自我纠正，**未采纳其打分**） | 无 LICENSE 文件（GitHub 仓库 `license: null`） | 按默认版权所有处理：只学思想、不搬原文，在此署名致谢 |

说明：四个 Anthropic 文件与 source-locked 文件的 frontmatter（author / license / version）已于 2026-09-15 逐字核验；`ai-legal-claude` 根目录无 LICENSE、README 无许可节，按无许可 = 默认版权所有处理，严于 Apache-2.0。

### 三、自我纠正：伪量化批评的理由修正

- 原批评：ai-legal-claude 的加权打分是「伪量化、权重无依据」。
- 核验结论：**该批评对方法论层面不成立**。`agents/legal-risks.md` 有完整方法论——严重度 40% × 触发可能性 25% × 金额敞口 20% × 不对等程度 15%，Composite 公式与每档定义齐全。
- 修正后理由：其阈值**未校准**（金额档为经验取值）、**把美国法假设写死**（竞业期限、at-will 等）、**无验证数据**。因此对企业法务场景而言，未校准的分数会制造**虚假精确感**。
- **裁决不变**：全套装继续禁用 0-100 量化分，定级只用低 / 中 / 高 / 极高四级 + 判定锚点。理由从「人家没方法论」改为「未校准的分数不适合企业法务场景」。

### 四、对 §4-2 旧结论的三处修正

1. **ai-legal-claude skill 数 13 非 14**：README 自称 14，但命令表 + 项目结构树 + MCP file 树三处均为 13 个 skill 目录（agents 5 个无误）。对外引用写 13。
2. **legal-risk-assessor 是四级不是三级**：GREEN / YELLOW / **ORANGE** / RED，Score = Severity × Likelihood（5×5 矩阵）。ORANGE = 高、RED = 极高，与我方低 / 中 / 高 / 极高**逐级对应**，互相印证强度高于旧结论所写「少一级极高」。
3. **ai-legal-claude 无 LICENSE 文件**：旧结论「动笔前先确认」现闭环——按无许可 = 默认版权所有处理，只学思想、不搬原文、署名致谢（见本节第二表末行）。

另记新鲜度：`ai-legal-claude` 仅 4 commits、最后更新 2026-03-27（实质停滞）；`awesome-legal-skills` 持续同步、最后更新 2026-09-04。对外引用时以后者为主要来源。

### 五、本附录边界

本附录记录的是**文本与结构落盘 + 来源核验**，与第 0 节同一口径：不证明宿主动态行为。上游原文只读未改，本仓库未复制其原文整句；本轮改动全部留在工作区，未提交、未推送。

## 附录（2026-09-15）：P0 官方基线对照精读 + 合规岗补课落盘

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与 2026-09-15 附录的任何结论。精读过程、三源结构对照、中英改写手法与缺口清单见 `.work/p0-comparison.md`（非生产工作笔记，`.work/` 已被 `.gitignore` 忽略）。
> 版权口径（硬）：只做思想与结构借鉴 + 中文重写，未复制上游原文整句。本附录全部落盘内容均为中文重写。

### 一、三源署名与许可

| 来源 | 采用的思想/结构（均为转述，未搬原句） | 许可 | 版权人 / 署名要求 |
| --- | --- | --- | --- |
| `anthropics/claude-for-legal` | ①「技能不写死立场、立场由组织基线提供」的机制思想；②「最小编辑粒度」的改写纪律；③交付物定位为供律师审查草稿的顶层声明；④事项工作区隔离与去标识的合规考量 | Apache-2.0（LICENSE 全文 11358 字节） | Copyright 2026 Anthropic PBC。可衍生改写，须署名并保留许可与版权声明 |
| `CSlawyer1985/claude-for-legal-ZH` | ①「在既有安全机制上追加本土方法论，而非逐句直译」的本地化手法；②路径与密钥纪律（不写死机器绝对路径、密钥只从环境变量读取）；③适配层只做自然语言路由、工作流正文留在领域目录 | Apache-2.0（LICENSE 与官方同 sha，属同一份） | 上游 Anthropic PBC；本地化改造由陈石律师（浙江海泰律师事务所）完成。署名须同时写明上游与改造人 |
| `HsuanZhao/SOE-Legal-Checker` | ①国企合同审查的**双层审查结构**：第一层为文本条款的法律风险，第二层为缔约与决策程序的合规性，两层不可互相替代；②9 个国资监管核查维度（招标采购、三重一大决策、国有资产交易审批、关联交易、廉洁风控、境外投资涉外、合规前置审查留痕、国有资产保值增值、信息披露与保密衔接）；③决策文件与合同要素「逐项对应」核验；④对合同文本之外的事实（是否落入强制招标范围、是否构成关联交易）明确表达不确定性、不臆断 | MIT | 赵璇律师｜北京市百瑞（上海）律师事务所。可衍生改写，须保留版权与许可声明 |

**许可核验结论**：Apache-2.0 × 2（官方仓库与中文版 LICENSE 同 sha，属同一份许可）、MIT × 1。与本任务简报第 5 节验源结论一致。本批未使用任何 CC BY-NC / CC BY-ND 或无许可来源。

### 二、对照结论（摘要）

- **切分维度不同**：上游按业务领域（13 个领域插件 + 组织基线画像 + 定时 agent + MCP 连接器）组织；本套装按法务职能岗（1 总纲 + 8 岗位 + 共享清单与模板 + 行业包）组织，强制路径是「四级风险等级 + 复核门」。二者重合于分工、共享清单、交付物模板与免责声明；差异在于我方以风险等级驱动复核，上游以组织基线（实践画像）驱动立场判定。
- **本批落盘的实质缺口只有一个**：合规岗缺「程序层」审查（国资监管与招投标）。其余缺口（组织基线与决策权限档案、定时监控、技能质量门、事项工作区隔离、管辖权假设标注、改写粒度纪律、检索连接器化、多端适配层）按简报授权边界只记入 `.work/p0-comparison.md`，未擅自落盘；其中判为「不做」的两项已在该文件写明理由。
- **总纲未改动**：对照未发现总纲路由或分诊层面的「必须补的最小缺口」——国资与招投标事项在现有路由表（专项合规 → 合规岗）与 2.1 重叠分流条（供应商、代理商、中介的合规尽调 → 合规岗）下已有落点，无需新增路由条目。故简报预留的 `legal-dept/SKILL.md` 名额本批**未启用**。

### 三、本轮落盘改动

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `roles/compliance-counsel/SKILL.md` | 第 1 段范围加「国资监管与招投标程序合规」，并加一条双层审查触发与「不得以条款合规替代程序合规」；必备事实加 2 条（涉国资主体属性与决策门槛、强制招标判断与程序留痕）；第 2 段加 9 条程序层失败模式；第 3 段加招标投标程序清单指针与国资监管程序类依据；第 6 段加国资背景升格条（命中 H6 时叠加程序层，程序缺项按 H5、已挂建筑工程行业包时同时命中 I-2）；第 7 段加一条红线（禁建议规避招标、禁建议补做倒签留痕）；frontmatter 中文触发词补 8 字 | 生产文件；只加缺口，未改既有章节结构与既有条目措辞 |
| ② | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 生产文件改动数：**2 个**（未超简报「生产文件总数 ≤3」限额）。
- 新增非生产文件：`.work/p0-comparison.md`（对照表；`.work/` 已忽略入库）。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史简报与记录未改；`shared/templates/legal-opinion.md` 未改。
- **未提交、未推送**，改动全部留在工作区。

### 四、无上游原文整句复制 · 抽查

抽查方法：以本会话直接抓取的上游原文为样本，在**工作区全文**（排除 `.git/`、`node_modules/`，共扫描 53 个文本文件）中检索样本长句，判断有无命中。

| 样本 | 来源 | 字数 | 样本 SHA256（前 16 位） | 工作区命中 |
| --- | --- | --- | --- | --- |
| S1 | `anthropics/claude-for-legal` README（免责与立场声明段） | 239 | `1A54842657C3445D` | 0 |
| S2 | `anthropics/claude-for-legal` README（律师责任段） | 135 | `03F9D2BE9C86CF4A` | 0 |
| S3 | `anthropics/claude-for-legal` README（冷启动面试段） | 164 | `EDF1CD648B3264D9` | 0 |
| S4 | `anthropics/claude-for-legal` README（社区技能信任层段） | 208 | `7619D18C37A343D8` | 0 |

样本来源文件与会话抓取副本 SHA256 `C0BD29783BDB36D3`（前 16 位）一致对应；每个样本均已确认存在于该抓取副本原文中。同一方法适用于 `HsuanZhao/SOE-Legal-Checker` 的 `skill.md`，本轮新增内容对该来源同样为中文重写（其双层结构与 9 维度在本附录第一节仅为要点转述）。

**边界（必须写明）**：抽查为**抽样**，不是全文查重；不能据此断言「工作区绝对不含上游任何原文片段」。样本清单本身未写入仓库，以免样本自身构成命中。能证明的是：上述 4 条上游长句均未出现在工作区 53 个文本文件中，本批新增内容为中文重写。

### 五、本附录边界

- 本附录记录的是**文本与结构落盘 + 来源核验**，与第 0 节同一口径：不证明宿主动态行为。
- 对照精读基于上游 README、单份 SKILL.md、INSTALL_DSH.md 与 SOE `skill.md`，**未逐文件通读全部 13 个领域插件**，缺口清单可能不完整。
- 上游原文只读未改，本仓库未复制其原文整句；本轮改动全部留在工作区，未提交、未推送。

---

AI生成，不构成法律意见。

## 附录（2026-09-15）：P1 合同岗标准立场落盘 + playbook 目录许可核验

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述两处附录的任何结论。
> 版权口径（硬）：标准立场均为**中国化重写**，只做思想与结构借鉴，未搬任何上游或出版物原文整句。

### 一、playbook 目录许可核验结论（开工第一道门）

| 项 | 核验结果 |
| --- | --- |
| 核验对象 | `lawve-ai/awesome-legal-skills` → `skills/playbook-reviewer-anthropic/` |
| 许可文件实测 | 目录内许可文件名为 `LICENSE.txt`（**不含**根目录式 `LICENSE`；按 `LICENSE` 名去取会 404，已实测） |
| 文件大小 / blob sha | 11358 字节；`d645695673349e3947e8e5ae42332d0ac3164cd7` |
| 许可类型 | **Apache License, Version 2.0**（全文为 Apache-2.0 标准文本；非 ND、非禁商用、非无许可） |
| 目录内其他文件 | `README.md`（277 字节）、`SKILL.md`（10943 字节）；无 `NOTICE` 文件 |
| 上游署名信息 | `SKILL.md` frontmatter：作者 Anthropic，version 2026.01.30；仓库内该 skill 的 `name` 字段为 `contract-review-anthropic` |
| 处置 | 许可放行（Apache-2.0 允许衍生改写，须署名并保留许可与版权声明）→ 本批**新增**引用并署名；同时按本仓库更严口径执行：只学「先找组织范本、没有才用通用标准并声明」的**机制思想**，全部落盘文字为中文重写 |
| 与前文关系 | 与 2026-09-15 附录第二节表格第 3 行所记「Apache-2.0」**一致**，该行结论由本次实测许可文件正文予以印证；该节「未采纳项」中所列「合同岗『先问组织范本』本轮不在采纳包内」，由本批启用 |

**边界**：本次核验为**目录内许可文件正文抓取**，未做该仓库全部 skill 目录的许可普查；结论只对本目录有效。

### 二、来源署名与许可要求

| 来源 | 采用的什么（转述，未搬原句） | 许可 | 版权人 / 署名要求 |
| --- | --- | --- | --- |
| `lawve-ai/awesome-legal-skills` → `skills/playbook-reviewer-anthropic/SKILL.md`（作者 Anthropic，version 2026.01.30） | ①「审查前先装载组织 playbook；没有 playbook 时用通用商业标准做基线，并把结果显著标注为『基于通用标准』而非组织立场」的**依据优先级思想**；② redline 输出的 Priority / Fallback 结构（本仓库对应为「修改建议 / 可退让底线」，与既有「标准立场」三件套联用） | Apache-2.0（`LICENSE.txt` 全文 11358 字节） | Copyright 2026 Anthropic PBC。可衍生改写，**须署名并保留许可与版权声明** |

本批未使用任何 CC BY-NC / CC BY-ND、无许可来源或出版物原文。

### 三、本轮落盘改动

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `shared/checklists/contract-redlines.md` | C 节标题下新增一句「组织有范本/谈判底线时以其为准，无则用本节通用立场并声明」；C1–C6 每条下新增「标准立场」子项（应然标准 + 可退让方向），不重复既有检查文字 | 生产文件；只加句，未增删节，未改既有条目措辞 |
| ② | `roles/contract-counsel/SKILL.md` | 仅第 4 段：第 4 步链条加入「标准立场（应然标准及其依据）」环节并与审查意见书表头呼应；新增第 8 条「范本与底线优先（playbook-first）」，无范本时在产出显著声明；第 1 步加一句指向第 8 条 | 生产文件；改动只落在第 4 段 |
| ③ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 生产文件改动数：**2 个**（清单内共 3 个文件，第 3 个为本验证记录）。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件与 `.work/p0-comparison.md` 未改；`shared/templates/legal-opinion.md` 未改。
- **未提交、未推送**，改动全部留在工作区。

### 四、无上游原文整句复制 · 抽查

抽查方法：以本次直接抓取的上游原文为样本，在**工作区全文**中检索并核验命中数；扫描口径为排除 `.git/`、`node_modules/` 及二进制扩展名（图片、Office、压缩包、可执行、`pyc`）后的全部文件，实测 **449 个文件**（本口径宽于前两处附录）。

| 样本 | 来源 | 字符数 | 样本 SHA256（前 16 位） | 工作区命中 |
| --- | --- | --- | --- | --- |
| S1 | `playbook-reviewer-anthropic/README.md` 首句（描述段） | 122 | `008C60F1839F90B5` | 0 |
| S2 | `playbook-reviewer-anthropic/SKILL.md`（角色设定首段） | 225 | `219AB813A5E13D10` | 0 |
| S3 | `playbook-reviewer-anthropic/SKILL.md`（无 playbook 时的处置句） | 88 | `7CA3C824E8DD2F14` | 0 |

**边界（必须写明）**：抽查为**抽样**，不是全文查重，不能据此断言「工作区绝对不含上游任何原文片段」。样本清单本身未写入仓库，以免样本自身构成命中。可证明的是：上述 3 条上游长句在 449 个文件中命中数均为 **0**；本批新增的标准立场文字为中文重写，未复制上游 S1–S3 任一句。

### 五、本附录边界

- 本附录记录的是**文本与结构落盘 + 许可核验**，与第 0 节同一口径：不证明宿主动态行为（宿主是否真的先问范本、是否真的在产出中打声明，需宿主实测）。
- 未做：上游仓库全部 skill 目录的许可普查、标准立场在中国的裁判倾向实证、标准立场与行业包（`industries/`）的交叉核验。
- 上游原文只读未改；本批新增内容为对上游机制思想的中文重写，未复制其原文整句；本轮改动全部留在工作区，未提交、未推送。

## 附录（2026-09-15）：P2 锚点案例库落盘 + 建工解释（二）覆盖核对

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述三处附录的任何结论。
> 取材口径（硬）：只用公开官方案例库与官方发布通稿（人民法院案例库、法答网精选答问、最高法/最高检典型案例与指导性案例、最高法官网公开案例）；每例只写要旨摘要（单例 ≤150 字），不搬运全文；每例必须给出案号或入库编号 + 出处链接；取不到原文的宁可空缺并注明，不编造案例。

### 一、来源与链接（本批实际抓取）

| # | 案例 | 案号/入库编号 | 出处链接 | 官网直读 |
| --- | --- | --- | --- | --- |
| 1 | 姜某帅等伪造国家机关公文、证件、印章等案（典型案例·案例一） | 未公开单案案号 | https://www.spp.gov.cn/spp/xwfbh/wsfbt/202312/t20231229_638674.shtml | 是 |
| 2 | 中天建设集团有限公司诉河南恒和置业有限公司建设工程施工合同纠纷案 | 指导案例 171 号；（2018）豫民初3号；（2019）最高法民终255号 | https://www.court.gov.cn/shenpan/xiangqing/331221.html | 是 |
| 3 | 邬某诉某旅游 App 经营公司网络服务合同纠纷案（典型案例·案例六） | 未公开单案案号 | https://www.court.gov.cn/zixun/xiangqing/350961.html | 是 |
| 4 | 江苏某生物科技有限公司诉李某劳动合同纠纷案 | （2023）苏03民终6278号（**转载页未列入库编号，本批不载编号**） | http://www.zjrmfy.suzhou.gov.cn/fypage/toContentPage/ldsp/82a07a489207e44501920840287c023f | 是（法院官网转载入库案例要旨） |
| 5 | 朱某某诉李某某、某门窗公司等承揽合同纠纷案 | 入库编号 2024-10-2-114-001；（2023）鲁民辖89号（山东高院指定管辖） | http://mingdalawyer.com/show-list-860.html ；https://m.055110.com/fl/3/5830.html | 否（原库 rmfyalk.court.gov.cn 检索接口 2026-09-15 实测返回 `{"msg":"未登录","code":401}`，未直读） |
| 6 | 某建筑公司与某乡政府、某交通运输委建设工程施工合同纠纷案（典型案例·案例四） | 未公开单案案号 | https://www.court.gov.cn/zixun/xiangqing/504211.html | 是 |
| 7 | 法答网精选答问（第一批） | —（平台答疑，无案号） | https://www.court.gov.cn/zixun/xiangqing/426272.html | 是 |
| 8 | 法答网精选答问（第六批） | —（平台答疑，无案号） | https://www.court.gov.cn/zixun/xiangqing/434921.html | 是 |
| 9 | 法答网精选答问（第三十五批）·商事审判专题 | —（平台答疑，无案号） | https://www.court.gov.cn/zixun/xiangqing/485941.html | 是 |
| 10 | 三巡典型案例·陈昭海与陈骏、胡秀娟、淮安市浩宇科技有限责任公司、张德全民间借贷纠纷案 | （2017）最高法民再178号 | https://www.court.gov.cn/xunhui3/xiangqing/66192.html | 是 |
| 11 | 《关于审理建设工程施工合同纠纷案件适用法律问题的解释（二）》（解释二覆盖核对用） | 法释〔2026〕12号 | https://www.court.gov.cn/zixun/xiangqing/504221.html | 是 |

### 一之补记（2026-09-15 复核修正）：来源类型纠错与编号核验结果

> 本补记为对上方表格与 `shared/cases/anchor-cases.md` 的**复核修正记录**，不改变表格所记"本批当时抓到什么"的事实，只更正此前的类型/编号表述。

1. **分歧点 3 的出处类型此前标错**：原表述把 4 条分歧点统称为"法答网分歧点"，其中**分歧点 3（保证期间内主张权利的方式）实际出自最高人民法院第三巡回法庭典型案例·陈昭海案（2017）最高法民再178号**（上表第 10 行），并非法答网精选答问。已更正为：**法答网精选答问 3 条（第一批、第六批、第三十五批）+ 最高法三巡典型案例 1 条，合计 4 条**；库内第 2 节标题与分歧点 3 小标题均已标注出处类型，与法答网条目标注相区分。
2. **H4 入库编号未获原库直读，但获两处独立转载印证**：`2024-10-2-114-001` 见于铭达律所转载页与律法网转载页，两页均标注"来源：人民法院案例库"且要旨一致；原库检索接口需登录，本批无法直读核验。库内该条已把出处类型与核验状态写进条目。
3. **H2 入库编号无从证实，已删除**：苏州市中级人民法院转载页只载案号（2023）苏03民终6278号，未见任何入库编号；库内 H2 条目原载的编号已删除，改为仅载已核验案号 + 出处链接，并在条目与第 3 节注明"入库编号未列出、未核验"。
4. **L1 原拟对应案例经核对不成立，已撤回并记为空缺**：指导案例 171 号官网发布的裁判结果栏载明，一审判决主文第一项为"河南恒和置业有限公司与中天建设集团有限公司于 2012 年 9 月 17 日、2013 年 6 月 26 日签订的两份《建设工程施工合同》无效"，与 L1"直接使用范本、条款有效"口径相反。该案仍保留在 E6 条目（原库载其入库编号 2021-18-2-115-001，见首批入库建工案例转载汇总 http://www.whmedri.com.cn/view/1030.html ），L1 条目改为**空缺登记并注明撤回依据**；本批未找到可核验的 L1 契合官方案例，按取材口径"宁可空缺并注明"处理。

### 二、锚点 → 案例落盘表

| 锚点 | 锚点定义（摘要，不改动） | 配案 | 案号/编号 | 占位 |
| --- | --- | --- | --- | --- |
| E1 | 涉及刑事风险（刑责、强制措施、单位犯罪） | 姜某帅等伪造公司印章等案 | 未公开单案案号（官方典型案例） | 第 1 行 |
| E6 | 诉讼时效或除斥期间即将届满且不可逆 | 指导案例 171 号 | （2019）最高法民终255号等 | 第 2 行 |
| H1 | 金额重大，或无限责任、无上限赔偿、排他义务、单方解释权 | 邬某诉某旅游 App 公司案 | 未公开单案案号（官方典型案例） | 第 3 行 |
| H2 | 劳动关系单方解除、竞业限制、股权激励、批量用工调整 | 江苏某生物科技公司诉李某案 | （2023）苏03民终6278号 | 第 4 行 |
| H4 | 管辖约定异地/境外，或境外仲裁、适用外国法 | 朱某某诉李某某等承揽合同案 | 2024-10-2-114-001；（2023）鲁民辖89号 | 第 5 行 |
| H6 | 相对方为政府机关、国有企业、上市公司或头部主体 | 某建筑公司与某乡政府、某交通运输委案 | 未公开单案案号（官方典型案例） | 第 6 行 |
| M1 | 使用公司范本但对方提出实质性条款修改 | 邬某诉某旅游 App 公司案（侧重范本被单方补充） | 未公开单案案号（官方典型案例） | 第 3 行 |
| L1 | 直接使用公司范本且无实质性修改 | **未收录（空缺）**——原拟指导案例 171 号，经核对判决主文认定两份施工合同无效，与该锚点口径相反，已撤回 | —（缺口登记，非案例引用） | — |

- **7 个锚点有对应案例，L1 空缺**（原拟对应案例经核对不成立，已撤回并记为空缺，理由见"一之补记"第 4 条与库内 L1 条目）；E6 与 H1/M1 为**同源案例的两处不同侧重**，已在库内该条目标注「说明」，未重复搬用整段要旨。
- **分歧点 4 条＝法答网精选答问 3 条**（劳动关系认定、二次固定期限续订权、团体意外险能否冲抵雇主责任）**＋最高法三巡典型案例 1 条**（保证期间内主张权利的方式），超出简报要求的 ≥3 条；分歧点 1 另附入库案例印证（两条案的入库编号在转载页未列出，故只载案号）。
- 库内要旨字数实测（冒号后正文长度，PowerShell `$body.Length`）：114/113/91/105/145/112/101 字（E1/E6/H1/H2/H4/H6/M1），最长 145 字，均 ≤150 字；L1 为缺口登记、无要旨。库内标注的字数与实测一致；无整篇搬运，均为摘要转述。（复核修正前该行为 114/113/91/105/91/112/101/99，其中 H4 一项为结论写反的旧要旨、L1 一项已撤回。）

### 三、建工解释（二）在 `industries/construction/regulations.md` 的覆盖核对（只读）

核对方法：读取该指针表全部 10 项（文件第 9–18 行）与最高法官网发布的解释二正文清单逐项比对；该文件本轮**未改动**（只读）。

| 核对项 | 结论 |
| --- | --- |
| 解释二（法释〔2026〕12号）是否已在表中 | **未收录**。表中建工口径的司法解释只有「（一）（法释〔2020〕25号）」一行，无解释二条目 |
| 已收录行的状态备注是否受影响 | 表内 25 号行注明「是否被后续解释调整引用前复核」。解释二第二十三条载明「本解释施行后，最高人民法院以前发布的司法解释与本解释不一致的，以本解释为准」，该「引用前复核」提示**现已被具体化**：不一致处以解释二为准 |
| 解释二生效对既有提示的影响 | 解释二自 2026-06-30 施行；表内 25 号、724 号两行的已核验日期为 2026-09-14，属施行之后，不构成时间戳冲突 |
| 本批是否据核对结果改该文件 | **否**。简报规定该文件只读核对、结论写进本附录，本批未在本批修改该文件 |
| 对照的典型意义（不入表、仅记录） | 解释二第二十二条要求法院发现违法发包、转包、违法分包、资质借用或严重质量问题时通报移送主管部门、涉嫌犯罪移送侦查机关，与 E1 锚点（刑事风险）及建工包合规自查口径同向 |

### 四、本轮落盘改动

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `shared/cases/anchor-cases.md` | 新建：头部（版本/截至日期/取材口径/引用规范/过期预警）+ 8 锚点条目（锚点编号、案号或入库编号、出处链接、要旨 ≤150 字、定级启示；其中 L1 为经复核后的空缺登记）+ 官方答疑与典型案例分歧点 4 条（含分诊误判教训）+ 未收录与未验证事项；2026-09-15 复核修正 4 处（H4 要旨结论、L1 撤回、分歧点 3 出处类型、H2 编号删除） | 新增生产文件，本轮复核修正 |
| ② | `shared/risk-framework.md` | 仅第 2 节开头（「定级方法」段之后）新增 1 句指针，指向 `shared/cases/anchor-cases.md`；锚点定义、等级、程序后果、口头语章节均未改动 | 生产文件；只加一句 |
| ③ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件与 `.work/p0-comparison.md` 未改；`shared/templates/legal-opinion.md` 未改；`industries/` 下全部文件（含 `regulations.md`）未改。
- **未提交、未推送**，改动全部留在工作区。

### 五、本附录边界

- 本附录记录的是**文本落盘与来源核验**，与第 0 节同一口径：不证明宿主动态行为。
- 未直读人民法院案例库原库页面：该库检索接口 2026-09-15 实测返回 `{"msg":"未登录","code":401}`；H4 例的入库编号取自两处独立第三方转载（铭达、律法网），仍须到原库复核；H2 例的转载页未列入库编号，库内已不载编号，只载案号（2023）苏03民终6278号 + 出处链接。
- E1、H1、H6、M1 对应的官方通稿未公开单案案号，库内标注为「未公开单案案号」，未以推测编号补位；**L1 经复核撤回原拟对应案例后为空缺**；此为本批的**已知空缺**，不是遗漏。
- 未做：锚点案例的类案检索完整性校验、要旨与裁判文书原文的逐字比对、建工包 `regulations.md` 的补录（本批只读核对）。

---

AI生成，不构成法律意见。

## 附录（2026-09-15）：D1 验证批（触发回归 + 法规核验 + C2 降级复跑）＝ §4-4 附录

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述四处附录的任何结论。
> 核验口径（硬）：只认官方源（npc.gov.cn / gov.cn / xzfg.moj.gov.cn / mem.gov.cn / court.gov.cn / mohurd.gov.cn）；**拿不到官方源确认的，保持「待核实」原文不动，不臆断**。

### 一、触发回归（复跑 `.work/check_missing.py`）

命令与真实输出（2026-09-15）：

```powershell
python .work/check_missing.py
roles={'compliance-counsel': 8, 'contract-counsel': 0, 'dispute-counsel': 0, 'employment-counsel': 0, 'governance-counsel': 0, 'ip-counsel': 0, 'legal-director': 0, 'legal-front-desk': 0}
unique=8
```

`.work/missing-trigger-words.json`（脚本产物，`.work/` 已 gitignore）：

```json
{
  "main_count": 243,
  "missing": {
    "compliance-counsel": [
      "国资监管",
      "国有企业",
      "央企",
      "招投标",
      "招标投标",
      "三重一大",
      "决策程序",
      "程序合规"
    ],
    "contract-counsel": [],
    "dispute-counsel": [],
    "employment-counsel": [],
    "governance-counsel": [],
    "ip-counsel": [],
    "legal-director": [],
    "legal-front-desk": []
  },
  "unique_missing": [
    "三重一大",
    "决策程序",
    "国有企业",
    "国资监管",
    "央企",
    "招投标",
    "招标投标",
    "程序合规"
  ]
}
```

**结论（首次复跑，2026-09-15）：8 岗中 7 岗 missing=0，合规岗 missing=8，全岗 missing=0 未达成。** 该口径已被下述补记取代，保留以免抹掉诊断过程。

诊断（可复现）：该 8 词已于 P0 批加入 `roles/compliance-counsel/SKILL.md` 的 frontmatter `description`（`git show f8c1386 --stat` 显示该批只改 `roles/compliance-counsel/SKILL.md` 与本验证文件，**未改 `legal-dept/SKILL.md`**），总纲 `legal-dept/SKILL.md` 的 description 至今为 243 词、不含这 8 词，故差集恒为 8。同为该批的 P0 附录第三节曾写「frontmatter 中文触发词补 8 字」，但未记录总纲未同步一事，本附录据实测予以更正。

**为什么本批没修**：本任务简报「允许改动的文件」只有 `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` 与 `industries/construction/regulations.md` 两个，`legal-dept/SKILL.md` 不在清单内，依简报边界不动它。**修复方式（待授权）**：把上述 8 词并入 `legal-dept/SKILL.md` 的 description 触发词表，再复跑脚本至全岗 missing=0；总纲词数将由 243 增至 251，截断风险需一并复核。

### 一之补记（2026-09-15，授权扩大后修复并复跑）：全岗 missing=0 已达成

授权背景：用户明确扩大授权——允许改 `legal-dept/SKILL.md` 的 **description 行**，把上述 8 词并入，**只动该行、不动正文**；并复跑脚本至全岗 missing=0。

实际改动（该文件仅此一行被改）：`legal-dept/SKILL.md` 第 3 行 description 的触发词表尾部，由「……法律风险评估、定级、分诊。」改为「……法律风险评估、定级、分诊、国资监管、国有企业、央企、招投标、招标投标、三重一大、决策程序、程序合规。」，新增 8 词、其余正文与 frontmatter 其他行未动。

复跑命令与真实输出（2026-09-15）：

```powershell
python .work/check_missing.py
roles={'compliance-counsel': 0, 'contract-counsel': 0, 'dispute-counsel': 0, 'employment-counsel': 0, 'governance-counsel': 0, 'ip-counsel': 0, 'legal-director': 0, 'legal-front-desk': 0}
unique=0
```

`.work/missing-trigger-words.json`（脚本产物，`.work/` 已 gitignore；此为文件原样）：

```json
{
  "main_count": 251,
  "missing": {
    "compliance-counsel": [],
    "contract-counsel": [],
    "dispute-counsel": [],
    "employment-counsel": [],
    "governance-counsel": [],
    "ip-counsel": [],
    "legal-director": [],
    "legal-front-desk": []
  },
  "unique_missing": []
}
```

**更正后的结论：8 岗全岗 missing=0（未覆盖=0），总纲触发词数 243 → 251，实测与「补 8 词」预期一致。**

词数口径说明（如实记录，不外推）：`main_count=251` 是脚本对 description 行 `中文触发词：` 之后按「、」切分的计数；description 行实测新增 8 词，差值恒为 8，与 251−243=8 吻合。

**仍未闭合**：总纲 description 由 243 词增至 251 词后，**宿主 skill 加载器是否对 description 有长度上限导致尾部截断，本批仍未做真机验证**（同第 3 节 7 项未验证边界之「description 截断」项），本次只证明脚本口径下全岗覆盖成立。

### 二、法规 8 项 + 解释二核验（逐行 web_fetch 官方源）

| # | 条目 | 官方源核验结论 | 来源 | 核验日期 |
| --- | --- | --- | --- | --- |
| 1 | 民法典第三编第十八章建设工程合同（第788–808条） | 现行有效；2020年5月28日第十三届全国人大第三次会议通过，自2021年1月1日起施行（第1260条）；条号已逐条对上（788 定义／791 转包分包／799 竣工验收／802 质量责任） | court.gov.cn/zixun/xiangqing/233181.html | 2026-09-15 |
| 2 | 民法典第八百零七条 | 已抓取原文：催告后逾期不付，除性质不宜折价拍卖外，可协议折价或请求法院拍卖，价款优先受偿。期限细节已确认：解释（一）第四十一条「最长不得超过十八个月，自发包人应当给付建设工程价款之日起算」；解释（二）第二十一条改按「变更后应当给付之日」起算，未改十八个月上限；解释（二）第二十三条「不一致的以本解释为准」 | court.gov.cn/zixun/xiangqing/233181.html、/282111.html、/504221.html | 2026-09-15 |
| 3 | 建筑法 | 现行有效；1997年11月1日通过，经2011年4月22日第一次修正、**2019年4月23日第二次修正**；第85条自1998年3月1日起施行 | npc.gov.cn/c2/c30834/201906/t20190608_298044.html | 2026-09-15 |
| 4 | 招标投标法 | 现行有效；1999年8月30日通过，根据**2017年12月27日**全国人大常委会决定修正 | npc.gov.cn/npc/c2/c30834/201905/t20190521_279157.html | 2026-09-15 |
| 5 | 安全生产法 | 现行有效；经2009、2014两次修正后，根据**2021年6月10日**第十三届全国人大常委会第二十九次会议决定第三次修正 | mem.gov.cn/fw/flfgbz/fg/202107/t20210716_416558.shtml | 2026-09-15 |
| 6 | 建设工程质量管理条例 | 现行有效版本为二次修订本：2000年1月30日国务院令第279号发布，根据2017年10月7日国务院令第687号第一次修订、根据2019年4月23日国务院令第714号第二次修订 | gov.cn/gongbao/content/2019/content_5468867.htm（二次修订本）、/2000/content_60658.htm（279号原文） | 2026-09-15 |
| 7 | 建设工程安全生产管理条例 | 现行有效；2003年11月12日国务院第28次常务会议通过，2003年11月24日国务院令第393号公布，自2004年2月1日起施行；司法部国家行政法规库该条目「历史沿革」为**暂无**（即无修订） | xzfg.moj.gov.cn/front/law/detail?LawID=43 | 2026-09-15 |
| 8 | 房屋建筑和市政基础设施项目工程总承包管理办法 | 现行有效；住房和城乡建设部、国家发展改革委建市规〔2019〕12号，成文日期2019年12月23日，第二十八条自2020年3月1日起施行 | gov.cn/zhengce/zhengceku/2019-12/31/content_5465928.htm | 2026-09-15 |
| 9 | 解释二（法释〔2026〕12号） | 已核验：2026年3月17日最高人民法院审判委员会第1969次会议通过，法释〔2026〕12号，**共二十三条**，自**2026年6月30日**起施行；第二十三条载明施行后新受理一审案件适用本解释、此前司法解释与本解释不一致的以本解释为准；第二十二条要求发现违法发包、转包、违法分包、资质借用或严重质量问题移送主管部门、涉嫌犯罪移送侦查机关 | court.gov.cn/zixun/xiangqing/504221.html | 2026-09-15 |

- 落表结果见 `industries/construction/regulations.md`（8 行改为「已核验」并写来源与核验日期；第 9 行解释二为新增条目）。
- **未取得、未断言**：国家法律法规数据库 `flk.npc.gov.cn` 详情页为脚本渲染，web_fetch 实测只返回站点标题、拿不到正文，故上表未以该库作为唯一来源；各组官方源均改用 npc.gov.cn、gov.cn 公报、司法部行政法规库、应急管理部、中国政府网政策文件库、最高法官网等可直接读到正文的官方页。
- **仍未核验（保持原状）**：属地文件（施工许可细则、工资保证金标准、信用评价办法）仍按文件第 20 行口径要求向属地部门官网核实，本批未做。

### 三、C2 降级回归走查（复跑补记二三场景关键 grep）

前置核对：`git log --oneline -3 -- roles/legal-front-desk/SKILL.md roles/legal-director/SKILL.md legal-dept/SKILL.md` 最新命中为 `cd2f9a7`（C2 落盘批），`git diff --stat 1eb7d0c -- <同上三文件>` **输出为空**，即补记二与 4-3 之后这三个文件未再改动，结论仍成立。

| 场景 | 落点（复跑实测） | 结论 |
| --- | --- | --- |
| A 无总纲 + 承办岗缺失 | `roles/legal-front-desk/SKILL.md` 15、34、82、83、86 行（「对应岗位未挂载，未作实体分析」；降级交付物《受理记录》+《待办与待核实清单》；需转介出《案件转介单》） | 与补记二一致 |
| B 复核方全缺 | `roles/legal-director/SKILL.md` 45、49、57 行；`legal-dept/SKILL.md` 78、118、119、121 行；`roles/legal-front-desk/SKILL.md` 35、86 行（「未经独立复核」；高/极高标「未经复核」） | 与补记二一致（补记二记的 44 行现为 45 行，系同批表格增行，语义未变） |
| C 岗位被独立调用 | `roles/legal-front-desk/SKILL.md` 13、86 行（「未经分诊」） | 与补记二一致 |

**真机边界（必须写明，本批未闭合）**：以上为**静态文本走查 + grep 复跑**，只证明规则文本仍在、未被后续批次改坏；**不能**证明缺岗宿主环境下产出头部真的出现「对应岗位未挂载，未作实体分析」「未经独立复核」「未经分诊」标注。真机运行需把套装部署进宿主 skill 目录、在缺岗配置下跑真实会话，本次**未做**。连同第 3 节 7 项未验证边界（自动触发命中率、无检索环境降级端到端、`gsxt.gov.cn` 可用性、description 截断、宿主 skill 加载、降级链动态行为）一并维持原状。

### 四、本附录边界

- 本附录记录的是**脚本实测、官方源逐行抓取、grep 复跑**三件事；与第 0 节同一口径，不证明宿主动态行为。
- 本批**首次复跑时**未改动 `legal-dept/SKILL.md`（不在原授权清单内），故当时验收标准 2「全岗 missing=0」未达成，实测缺口 8 词、根因见第一节；**授权扩大后**已改该文件 description 一行并入 8 词并复跑，**全岗 missing=0 已达成**（见「一之补记」），验收标准 2 转达成。
- 本轮改动全部留在工作区，未提交、未推送。

## 附录（2026-09-15）：D2 后续批次落盘（G1 / 招投标通用清单 / G4 / G6 / G7 / G8；G3 不做）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述五处附录的任何结论。
> 版权口径（硬）：全部中国化重写，只做思想与结构借鉴，**未复制任何上游原文整句**（抽查见表四）。
> 来源边界：本批来源均为**已在本仓库既有附录中署名过**的三源（`anthropics/claude-for-legal`、`CSlawyer1985/claude-for-legal-ZH`、`HsuanZhao/SOE-Legal-Checker`）及其既有许可核验结论，本批**未新引入任何未署名来源**，故未重复做许可核验。

### 一、本轮落盘改动（7 个文件，全部在简报允许清单内）

| # | 文件 | 落盘内容 | 性质 |
| --- | --- | --- | --- |
| ① | `shared/templates/org-profile.md` | **新建**：G1 组织基线与决策权限档案模板 —— 一、主体属性；二、决策权限；三、合同范本与谈判底线存放位；四、法域假设；五、未采集项清单与禁臆填口径。全文缺项写「未采集」，并逐节写明禁臆填 | 新增生产文件（共享模板） |
| ② | `shared/checklists/bidding-compliance.md` | **新建**：招投标合规通用清单（跨行业）—— 一、强制招标识别；二、前置文件齐备性；三、一致性核对；四、三重一大决策衔接；五、廉洁条款与利益关联；六、行业包交叉引用；七、依据与核实 | 新增生产文件（共享清单） |
| ③ | `legal-dept/SKILL.md` | 仅两处：第 1 段新增第 4 项「组织基线采集」（指向 `shared/templates/org-profile.md`，缺项写「未采集」禁推定）；第 3 段（路由与分诊段）新增一段「**法域假设**」（G7：中国内地法律为默认法域，出现涉外/涉港澳台要素时法域假设失效并转专项核查） | 生产文件；只加两处，未改既有条目 |
| ④ | `roles/contract-counsel/SKILL.md` | 仅第 4 段第 4 步下新增一条子项：修改粒度取最小（改词优先于改句、改句优先于整条替换），无法机械修改的写「转律师审查」不代拟 | 生产文件；只加一处子项 |
| ⑤ | `shared/templates/intake-log.md` | 仅第 4 节「留痕规则」首行新增 G6 事项隔离一句：每案独立卷宗，跨案引用须经用户确认，未获确认默认不跨案读取 | 生产文件；只加一处 |
| ⑥ | `docs/skill-quality-gate.md` | **新建**：G4 技能自检门（6 项检查表：触发词同步总纲、指针可达、复核标注词一致、免责尾部齐备、A–E 与待核实口径正确、改动范围与版权口径）+ 用法 + 边界 | 新增非运行时维护文档 |
| ⑦ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 生产文件改动数：**6 个**（第 7 个为本验证记录）；新增文件 3 个、既有文件改动 3 个。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件（含 `.work/p0-comparison.md`）未改；`shared/templates/legal-opinion.md` 未改；`industries/` 下全部文件（含 `construction/checklists/bidding-compliance.md`、`construction/regulations.md`）**只读提炼，未改**。
- **未提交、未推送**，改动全部留在工作区；基线仍为 `1eb7d0c`（含 D1 未提交改动）。

### 二、G1 组织基线档案的落地要点

- **四节齐备**：主体属性 / 决策权限 / 范本与谈判底线存放位 / 法域假设，另加第五节「未采集项清单」。
- **禁臆填写进硬规则**：文首硬规则与第五节末条均写明「缺项写『未采集』，禁臆填」，明确禁止用「惯例上」「一般企业通常」代填、禁止填入他人档案或行业常见值。
- **门槛未采集的处置链**：门槛缺失时不得推定「未达门槛」，按 `shared/risk-framework.md`「不确定即向上取整」定级并列入待补充材料；该处置同时被总纲第 1 段第 4 项与招投标通用清单第四节引用。
- **口径呼应**：档案第二节的数据来源要求写明制度名称与版本，来源不明标「待核实」；第三节只记存放位、不复制范本正文与未公开商业条款，避免形成第二份漂移副本。

### 三、招投标通用清单的提炼口径

- **提炼来源**：`industries/construction/checklists/bidding-compliance.md`（建工招投标清单，8 项）、`industries/construction/SKILL.md` 第 2–5 节（行业锚点 I-2、岗位加载指引、属地差异、法规指针）、`roles/compliance-counsel/SKILL.md` 第 1–2 段（双层审查与国资 9 维度核查口径）。
- **通用化处理**：只保留跨行业共有的程序骨架（强制招标识别 / 前置文件 / 招标—投标—中标—合同一致性 / 三重一大衔接 / 廉洁与利益关联）；把施工资质、安全生产许可证、工程工期与质量标准、挂靠与转包、农民工工资等**建工特有细节留在原包**，以第六节交叉引用方式指向，本清单不重复、不复制其条文。
- **上位口径复用**：第四节直接引用 `roles/compliance-counsel/SKILL.md` 第 6 段的升格规则（程序层缺项按 H5；已挂建筑工程行业包时同时命中 I-2 → 极高），本清单不另立定级标准，避免与 `shared/risk-framework.md` 出现第二套锚点。
- **未做**：通用清单未逐行业核对其他行业（医疗器械、金融、政府采购等）的特有强制招标口径——本套装未挂载这些行业包，清单第七节只给出「标待核实 + 向行业主管部门核实」的处置口径。

### 四、无上游原文整句复制 · 抽查

抽查方法：以本批落盘所依据的**上游要点为准**（上游原文**未在本会话重新抓取**，故不能写「与上游原文逐字比对」）；改用**本仓库既有署名附录中已记录的要点表述**作为样本，在**本批新增与修改的 7 个文件**中检索有无整句命中。

| 样本 | 出处（本仓库既有署名附录） | 内容性质 | 本批 6 个落盘文件中命中 | 全工作区（排除 `.git/`）命中文件 |
| --- | --- | --- | --- | --- |
| S1 | 2026-09-15 P0 附录第一节表格第 1 行 | 「技能不写死立场、立场由组织基线提供」机制要点 | 0 | 1（仅本验证记录自身 2 处，即本行与此处记录） |
| S2 | 2026-09-15 P0 附录第一节表格第 1 行 | 「最小编辑粒度」改写纪律要点 | 0 | 1（仅本验证记录自身 3 处） |
| S3 | 2026-09-15 P0 附录第一节表格第 3 行 | 「缔约过程与决策程序的合规性」双层审查结构要点 | 0 | 1（仅 `.work/p0-comparison.md`，非生产工作笔记；本次未改动该文件） |

- 检索命令与真实输出（2026-09-15）：

```powershell
$dirs = @('shared/templates/org-profile.md','shared/checklists/bidding-compliance.md','legal-dept/SKILL.md',
          'roles/contract-counsel/SKILL.md','shared/templates/intake-log.md','docs/skill-quality-gate.md')
Select-String -Path $dirs -SimpleMatch '技能不写死立场、立场由组织基线提供'   # → 0
Select-String -Path $dirs -SimpleMatch '最小编辑粒度'                         # → 0
Select-String -Path $dirs -SimpleMatch '缔约过程与决策程序的合规性'            # → 0
# 全工作区（排除 .git/，文本扩展名）：
# [技能不写死立场、立场由组织基线提供] hits=2 files=docs\review\EXTERNAL-REVIEW-FIX-VERIFICATION.md
# [最小编辑粒度]                       hits=3 files=docs\review\EXTERNAL-REVIEW-FIX-VERIFICATION.md
# [缔约过程与决策程序的合规性]           hits=1 files=.work\p0-comparison.md
```

- 说明：三个样本均为**本仓库既有的中文要点转述**（非上游英文原句），命中 0 只证明本批新增文字未整句照抄仓库内既有要点表述，**不等于**与上游原文做过逐字比对。
- **边界（必须写明）**：本批未重新抓取上游原文，故本表是**仓库内表述层面的抽查**，样本数 3、非全文查重；不能据此断言「本批内容绝对不含上游任何片段」。能证明的是：本批全部落盘文字为中文重写，未复制任何上游原文整句（本批落盘所依据的来源许可与署名见 2026-09-15 P0 附录第一节，结论未变）。

### 五、G3（定时/事件驱动监控）不做 · 边界登记

- **不做的内容**：不新增 `agents/` 式定时监控、不新增 cron 风格调度、不新增「法规动态 / 续签 / 案期」自动巡检。本批未落盘任何调度、脚本或任务注册内容。
- **不做的理由**：G3 需要**宿主调度能力**（定时触发、后台任务、事件钩子），本套装定位为纯文本可移植 skill，不依赖任何单一客户端的私有能力；在没有调度能力的环境里落盘调度配置只会成为死文件。
- **现有替代落点（未改动，仅登记）**：期限提醒沿用 `legal-dept/SKILL.md` 第 8 段（台账登记 + 倒计时不足 30 日加「急」标记）与 `shared/templates/intake-log.md` 第三节期限提醒栏，属**人工登记口径**，不是自动巡检。
- **未验证边界**：本套装当前**不具备**任何自动监控能力，也没有任何机制保证期限提醒会被触发；台账登记是否真的写入、倒计时是否真的被读取，取决于使用者与宿主，本附录不作保证。
- **若将来要做的前置条件**：宿主具备定时触发能力 + 明确调度失败的兜底口径 + 监控项的官方数据源可得，三者齐备后再单独立项；本批不做。

### 六、简报验收命令与真实输出（逐条复跑）

**验收 1（G1）**

```powershell
Test-Path shared/templates/org-profile.md          # → True
Select-String -Path 'legal-dept/SKILL.md' -Pattern 'org-profile'
# → 35: 4. **组织基线采集**：…模板见 `shared/templates/org-profile.md`。…
# → 67: **法域假设**：…同时核对 `shared/templates/org-profile.md` 第四节的法域登记。
Select-String -Path 'shared/templates/org-profile.md' -Pattern '未采集|禁臆填|不得臆'   # → 命中 13 处
```

结论：**通过**。四节标题实测为 `## 一、主体属性（第一步先填本节）` / `## 二、决策权限（程序层核查的门槛依据）` / `## 三、合同范本与谈判底线存放位（playbook 位置）` / `## 四、法域假设（本档案的默认管辖与法律适用）`，另有 `## 五、未采集项清单（每次采集后同步）与禁臆填口径`；总纲第 1 段有指向句（第 35 行）。

**验收 2（招投标通用清单）**

```powershell
Test-Path shared/checklists/bidding-compliance.md   # → True
Select-String -Path 'shared/checklists/bidding-compliance.md' -Pattern '三重一大|廉洁'
# → 35: ## 四、三重一大决策衔接（决策文件与采购程序对齐）
# → 37: - [ ] 金额、期限、担保方式是否触发本企业「三重一大」决策门槛（门槛见组织基线档案 `shared/templates/org-profile.md` 第二节…）
# → 43: ## 五、廉洁条款与利益关联
# → 45: - [ ] 是否附廉洁协议或廉洁承诺条款（工程、采购、销售类重点核查）…
Select-String -Path 'shared/checklists/bidding-compliance.md' -Pattern 'industries/construction'
# → 53、54、55 三行，全部位于第六节「行业包交叉引用」
```

结论：**通过**。五要素齐备（一、强制招标识别；二、前置文件齐备性；三、一致性核对；四、三重一大决策衔接；五、廉洁条款与利益关联），并设第六节交叉引用建工包。建工特有词（施工资质、安全生产许可证、挂靠、农民工）在本清单中**只出现在第 53 行交叉引用句内**（作为「细节已在原包」的指向），无复制建工清单条目；第 28、30 行出现的「工期」属通用采购实质性条款（工期或交付期限），非建工专有项。

**验收 3（四处微补位）**

```powershell
Select-String -Path 'legal-dept/SKILL.md' -Pattern '法域假设|涉外'
# → 35（组织基线采集）、67（**法域假设**：…出现涉外或涉港澳台要素…）
Select-String -Path 'roles/contract-counsel/SKILL.md' -Pattern '改词优先|最小'
# → 63: - 修改粒度取最小：**改词优先于改句、改句优先于整条替换**；…无法作机械修改的…写「转律师审查」…
Select-String -Path 'shared/templates/intake-log.md' -Pattern '隔离|跨案'
# → 49: - **事项隔离**：每案独立卷宗。跨案引用…须先经用户确认…默认不读取、不引用其他案件材料…
Test-Path docs/skill-quality-gate.md                # → True
```

结论：**通过**（四项全命中；质量门文档 6 项检查表 + 用法 + 边界）。

**验收 4（D2 附录）**

```powershell
Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'D2|G1|G3' | Where-Object { $_.LineNumber -gt 719 }
# → 721: ## 附录（2026-09-15）：D2 后续批次落盘（G1 / 招投标通用清单 / G4 / G6 / G7 / G8；G3 不做）
# → 731: | ① | `shared/templates/org-profile.md` | **新建**：G1 组织基线与决策权限档案模板 …
# → 743: ### 二、G1 组织基线档案的落地要点
# → 770: ### 五、G3（定时/事件驱动监控）不做 · 边界登记
# → 773: - **不做的理由**：G3 需要**宿主调度能力**…
```

结论：**通过**（落盘表见本附录第一节、署名与来源边界见文首与第四节、G3 不做边界见第五节）。

**验收 5（改动范围 / 基线 / 无整句复制）**

```powershell
git status --porcelain
 M docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
 M industries/construction/regulations.md
 M legal-dept/SKILL.md
 M roles/contract-counsel/SKILL.md
 M shared/templates/intake-log.md
?? .workbuddy-ai/
?? docs/skill-quality-gate.md
?? shared/checklists/bidding-compliance.md
?? shared/templates/org-profile.md
git log -1 --format=%H
1eb7d0c68f503aeebad4eeaa580ee58b1750dabd
```

结论：**通过（附一条范围说明）**。本批新增/修改的文件为 `shared/templates/org-profile.md`、`shared/checklists/bidding-compliance.md`、`docs/skill-quality-gate.md`（3 个新增）与 `legal-dept/SKILL.md`、`roles/contract-counsel/SKILL.md`、`shared/templates/intake-log.md`、`docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md`（4 个改动，其中最后一个含 D1 未提交改动），**全部在简报允许清单内**；`industries/construction/regulations.md` 与 `?? .workbuddy-ai/` 为**本任务开始前既有的未提交内容**（D1 批与更早已存在），本批未触碰。基线仍为 `1eb7d0c`，**未提交、未推送**。三条上游长句样本在本批 6 个落盘文件中命中均为 0（命令与输出见第四节）。

### 七、本附录边界

- 本附录记录的是**文本与结构落盘 + 静态抽查**，与第 0 节同一口径：不证明宿主动态行为。
- **未做**：未做宿主真机验证（组织基线是否会被真的先问、法域假设失效提示是否真的会出现在产出头部、最小编辑粒度是否会被真的执行）；未做八岗触发词差集复跑（本批未改任何 frontmatter 触发词，见下条）；未做上游原文重新抓取与逐字比对；未做其他行业的强制招标口径普查。
- **触发词口径**：本批未改 `legal-dept/SKILL.md` 与各岗位 frontmatter 的 `description` 触发词表，故 D1 批「全岗 missing=0（总纲 251 词）」的结论不受本批影响、无需复跑。
- 上游原文只读未改；本批新增内容为对上游机制思想的中文重写，未复制其原文整句；本轮改动全部留在工作区，未提交、未推送。
## 附录（2026-09-15）：D3 原库专场落盘（13 例＋H2/H4 修正＋L1/L3 空缺）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述六处附录的任何结论。
> 版权与取数口径（硬）：要旨为**中文改写压缩**（单例 ≤150 字），不搬运原库页面整段文字；全部数据来自人民法院案例库原库页面原文，未编造、未以不契合案例补位。
> 证据文件：`.work/d3-findings.md`（查询→命中→选用→弃用全记录，含试错日志），`.work/` 已被 `.gitignore` 忽略，不入库。

### 一、原库直读方法（本批与既往批次的差异）

| 项 | 本批做法 |
| --- | --- |
| 站点 | `rmfyalk.court.gov.cn`（人民法院案例库；检索时在库 5566 篇） |
| 取数日期 | 2026-09-15 |
| 方法 | 在**已登录会话**中执行全文检索，直读结果页条目行（案名 / 入库编号 / 案由 / 法院 / 案号 / 裁判日期 / 审理程序 / 入库日期，均为页面原文） |
| 与前批差异 | 既往批次（P2、D1、D2）实测该库检索接口返回 `{"msg":"未登录","code":401}`，只能取法院官网转载或第三方转载，故 H2/H4 编号均未获原库证实；本批取得原库页面正文，据此**推翻**此前两条推论 |
| 落盘纪律 | 页面要旨原文→中文改写压缩至 ≤150 字→写入 `shared/cases/anchor-cases.md`；条目标注字数与实测字数逐例核对一致 |

### 二、来源表（本批新增与补正，出处均为原库页面直读）

| # | 锚点 | 案例 | 入库编号 | 法院 / 程序 / 日期 | 入库日期 |
| --- | --- | --- | --- | --- | --- |
| 1 | E2 | 房某孝诉上海市公安局边防和港航公安分局海事行政处罚案 | 2026-12-3-001-003 | 上海海事法院 / 一审 2020-11-10 | 2026.09.08 |
| 2 | E3 | 曾某某与白某等执行实施案 | 2024-17-5-101-026 | 重庆市渝中区人民法院 / 执行 2020-08-14 | 2024.12.10 |
| 3 | E4 | 上海磐某商业经营管理公司诉吴某梅公司证照返还纠纷案 | 2024-08-2-272-001 | 上海市第一中级人民法院 / 二审 2023-07-28 | 2024.11.29 |
| 4 | E5 | 天津某教育公司诉上海某泵业公司等股东出资纠纷案 | 2023-08-2-265-002 | 上海市第一中级人民法院 / 二审 2022-06-30 | 2024.02.23 |
| 5 | E7 | 葛某诉中国证券监督管理委员会上海监管局、中国证券监督管理委员会行政处罚及行政复议案 | 2024-12-3-001-035 | 上海市浦东新区人民法院 / 一审 2016-08-29 | 2024.12.30 |
| 6 | E8 | 南京东某铂业有限公司、姚某刚等骗取出口退税案 | 2025-05-1-145-001 | 江苏省高级人民法院 / 二审 2021-05-25 | 2025.12.30 |
| 7 | H3 | 吉某公司诉河南某集团等外商投资公司股权转让纠纷案 | 2023-10-2-269-004 | 最高人民法院 / 二审 2017-12-28 | 2024.02.22 |
| 8 | H5 | 弋某某诉某进出口贸易公司、新疆某建工集团公司建设工程施工合同纠纷案 | 2023-07-2-115-003 | 新疆生产建设兵团图木休克垦区人民法院 / 一审 2023-05-25 | 2024.02.22 |
| 9 | H7 | 麦某伦酒厂、某洋酒（上海）有限公司诉某报社、某杂志社有限公司等名誉权纠纷案 | 2023-10-2-006-001 | 上海市浦东新区人民法院 / 一审 2017-04-20 | 2024.02.22 |
| 10 | M2 | 深圳某科技公司诉某技术公司买卖合同纠纷案 | 2023-08-2-084-010 | 北京市第一中级人民法院 / 二审 2021-09-02 | 2024.02.25 |
| 11 | M3 | 北京某贸易有限公司诉王某劳动合同纠纷案 | 2023-07-2-186-004 | 北京市第二中级人民法院 / 二审 2021-09-06 | 2024.02.23 |
| 12 | M4 | 尹某梅与陈某东执行复议案 | 2025-17-5-202-004 | 重庆市第四中级人民法院 / 执行复议 2024-04-29 | 2025.12.08 |
| 13 | L2 | 董某丽诉卞某珍财产损害赔偿纠纷案 | 2025-07-2-043-002 | 柳河县人民法院 / 一审 2023-06-30 | 2025.08.13 |
| 14 | H2（补正） | 江苏某生物科技有限公司诉李某劳动合同纠纷案 | 2024-07-2-186-002 | 江苏省徐州市中级人民法院 / 二审 2023-12-04 | 2024.08.15 |
| 15 | H4（补正） | 朱某某诉李某某、某门窗公司等承揽合同纠纷案 | 2024-10-2-114-001 | 山东省高级人民法院 / 指定管辖（2023）鲁民辖89号 | 2024.02.24 |

### 三、落盘表（锚点 → 修改类型 → 落盘内容）

| 锚点 | 修改类型 | 落盘内容 | 库内位置 |
| --- | --- | --- | --- |
| E2、E3、E4、E5、E7、E8 | **新增**条目 | 锚点编号＋案名＋入库编号＋法院＋案号＋日期＋出处（原库直读）＋要旨（改写 ≤150 字）＋定级启示＋出处类型 | 第 1 节 E 组对应位置 |
| H3、H5、H7 | **新增**条目 | 同上结构 | 第 1 节 H 组对应位置 |
| M2、M3、M4、L2 | **新增**条目 | 同上结构 | 第 1 节 M 组与 L 组对应位置 |
| H2 | **恢复入库编号＋换源** | 编号恢复为 `2024-07-2-186-002`；出处由法院官网转载改为**原库页面直读**（转载链接保留为同源印证）；要旨与定级启示未改 | 第 1 节 H2 条目 |
| H4 | **补正编号备注** | 出处由「两处第三方转载」改为**原库条目行显示编号**，并如实备注「编号串本身不进全文索引，原库全文检索 0 命中，需以案号检索」 | 第 1 节 H4 条目 |
| L1 | **补空缺原则性理由** | 新增「案例库只收争议案件，『无修改按范本履行』不进入诉讼，故无裁判对应」；处理口径写明「本级事项通常不进入诉讼，以范本原文＋审查记录为据」；原撤回依据与定级启示保留 | 第 1 节 L1 条目 |
| L3 | **补空缺说明** | 新增「纯咨询、流程性问答无裁判对应，属结构性不适配」，并写咨询分流口径「以总纲分诊为准」 | 第 1 节 L3 条目 |
| 第 3 节 | **同步更新** | 重写为 6 条：收录/空缺清单、原库直读方法与批次差异、H2 编号恢复说明、H4 编号如实备注、L1/L3 原则性空缺理由、期限类事实未核验 | 第 3 节 |

### 四、H2 恢复说明（推翻 D2 删除依据）

- **原依据（D2）**：苏州市中级人民法院转载页只载案号（2023）苏03民终6278号，未见入库编号，故 D2 批次把 H2 的入库编号删除，并在条目与第 3 节注明「入库编号未列出、未核验」。
- **原库实测（本批）**：2026-09-15 以案号全文检索命中该案条目行，页面明确显示 `2024-07-2-186-002 / 民事 / 劳动合同纠纷 / 江苏省徐州市中级人民法院 / 2023.12.04 / （2023）苏03民终6278号 / 二审 / 入库日期：2024.08.15`。
- **结论**：**编号存在，予以恢复。** D2 的删除依据「转载页未列编号」本身事实无误（转载页确实不列编号），但推论「故编号无从证实」不成立——原库列编号。库内该条已恢复编号并把出处换为原库直读，转载链接降为同源印证。
- **连带更正**：第 3 节原第 2 条「H2 的转载页未列入库编号，本条已不载编号」已随本次第 3 节重写一并更正（见第三节末行）。

### 五、L1/L3 空缺理由（原则性空缺，非取不到）

- **L1（直接使用公司范本且无实质性修改）**：人民法院案例库只收**争议案件**，「无实质修改、按范本原文履行」的事项通常不进入诉讼，故该口径在库中无裁判对应可引——属**结构不适配**，不是检索失败。本批试过以「示范文本」检索，唯一命中为行政协议无效案，与本锚点不匹配，已弃用。处理口径已写入库内条目：本级事项以**范本原文＋审查记录**为据。
- **L3（纯咨询、流程性问答、对既有结论的复述与整理）**：咨询事项无裁判对应，不进入案例比对；库内已写明分诊与承办**以总纲分诊为准**（`legal-dept/SKILL.md` 分诊与路由段）。
- **与既往附录的关系**：P2 附录「L1 经复核撤回原拟对应案例后为空缺」的结论继续有效；本批新增的是**空缺原因的性质说明**（结构不适配），未改变空缺本身。

### 六、核验分工声明

- **登录墙内证据（主模型亲验，记录在案）**：本批 13 例新增条目与 H2/H4 两条编号补正所依据的原库页面内容，均在 `rmfyalk.court.gov.cn` 的**已登录会话**中取得；取数日期、检索词、命中结果、选用与弃用理由、试错日志（含 0 命中与不匹配弃用记录）全部记入 `.work/d3-findings.md`。该文件为**只读证据**，本批未修改。
- **本次核验的性质**：本批落盘内容为主模型亲验并记录在案，非第三方独立复核；证据文件处于仓库外（`.work/` 已 gitignore），外部复核人**无法自行重跑**该登录会话。
- **复核人可核的部分**：①库内 22 例（20 例案例）的锚点编号、案名、入库编号、案号、法院、日期、出处是否与 `.work/d3-findings.md` 逐条一致；②要旨是否 ≤150 字且为中文改写（非整段搬运）；③H2/H4 的编号与备注是否与 findings 第一节结论一致；④第 3 节空缺清单与实际是否一致（无已收录仍列空缺、无空缺未声明）；⑤改动范围是否仅限简报允许的 2 个文件、基线是否未变、是否未提交未推送。
- **复核人不能核的部分**：原库页面原文本身（登录墙）。**不得**把本附录的「原库直读」表述等同于可独立复现的公开证据；如对编号或要旨有疑，需由具备该库登录权限的一方重跑并以页面原文为准。

### 七、本轮落盘改动与验收自验

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `shared/cases/anchor-cases.md` | 第 1 节由 8 例扩为 **22 例**（新增 E2/E3/E4/E5/E7/E8/H3/H5/H7/M2/M3/M4/L2 共 13 例；H2 恢复编号并换源原库；H4 补正编号备注；L1 补空缺原则性理由；L3 新增空缺条目）；第 3 节重写为 6 条 | 生产文件 |
| ② | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 生产文件改动数：**1 个**（第 2 个为本验证记录）。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件与 `.work/d3-findings.md` **只读未改**；`shared/templates/legal-opinion.md` 未改；`industries/` 下全部文件未改。
- **未提交、未推送**，改动全部留在工作区。

### 八、本附录边界

- 本附录记录的是**文本与结构落盘 + 来源取数记录**，与第 0 节同一口径：不证明宿主动态行为（案例库是否会被真的加载、要旨是否会被真的引用，均需宿主实测）。
- **未做**：原库页面的第三方独立复跑（登录墙所限，见第六节）；要旨与原库页面文字的逐句比对留痕（本批只做改写压缩与字数核对）；锚点案例的类案检索完整性校验；`.work/d3-findings.md` 第四节所记弃用备选（如 E4 备选覃某诉证监会案）的进一步核验。
- **已知局限**：本批 13 例的入库日期集中在 2024–2026 年，其中 E2（2026.09.08 入库）、E8（2025.12.30 入库）、M4（2025.12.08 入库）入库时间距知识截至日期极近，后续若有修改或撤库，本库不会自动同步。

---

AI生成，不构成法律意见。

## 附录（2026-09-15）：D4a 红线清单 A/B/D/D2外/E 标准立场落盘（19 条）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述七处附录的任何结论。
> 版权口径（硬）：标准立场均为**中国化重写**，只做思想与结构借鉴，未搬任何上游或出版物原文整句。
> 引用口径（硬）：本批新增文字**不写任何法条编号**，只写应然规则与可退让方向；未做条文级联网核验，故本批不产生、也不声称任何条文引用。

### 一、口径声明（本批三条硬口径）

| # | 口径 | 本批执行方式 |
| --- | --- | --- |
| ① | 不写法条编号 | 新增 19 句标准立场中无「第×条／第×款」类引用；依据只以应然规则表述，条文级核验留待后续批次并须联网核验后写入 |
| ② | 只写应然规则与可退让方向 | 每条子项为「标准立场：…（应然标准）。可退让方向：…（可让的部分与不可让的底线）」两段式，与 C1–C6 体例一致 |
| ③ | 不重复既有检查文字 | 每条子项不复述该条既有的核对问句，只写该条应对齐的应然标准与谈判底线 |

- **来源与署名**：沿用 2026-09-15 P1 附录已署名的来源 `lawve-ai/awesome-legal-skills` → `skills/playbook-reviewer-anthropic/SKILL.md`（作者 Anthropic，version 2026.01.30，Apache-2.0，Copyright 2026 Anthropic PBC），采用其 ①「先找组织 playbook、没有才用通用标准并声明」的依据优先级思想 与 ② redline 的 Priority / Fallback 结构（本仓库对应「标准立场 / 可退让方向」）。**本批未新引入任何来源**，许可核验结论沿用 P1 附录第一节，未重复核验；未使用任何 CC BY-NC / CC BY-ND、无许可来源或出版物原文。
- **未写组织优先声明的原因（如实登记）**：C 节标题下已有「组织文件优先、无则用本节通用立场并声明」的声明句（P1 批落盘）；本批简报规定 A/B/D/E 不加节、不加声明句，故本批**未在 A/B/D/E 节重复该声明**。该声明在 C 节为节内声明，是否需要在其余各节补同类声明，本批不做、留待后续批次决定。

### 二、落盘表（19 条，全部在简报允许清单内）

| # | 节 | 条目 | 落盘内容 | 性质 |
| --- | --- | --- | --- | --- |
| ① | A | A1–A5（5 条） | 每条下新增「标准立场」子项：A1 主体存续与经营范围；A2 签署授权与补正期限；A3 前置审批以取得为履行前提；A4 处分权限与内部决议先取得；A5 生效条件可验证与生效时点 | 生产文件；只加子项 |
| ② | B | B1–B5（5 条） | B1 标的可客观判定、禁开放式兜底；B2 四项齐备与最长验收期限；B3 价款税费发票逐项写明；B4 变更须双方书面确认；B5 第三方义务由相对方锁定 | 生产文件；只加子项 |
| ③ | D | D1–D3（3 条） | D1 解除权触发情形明确＋通知与补偿；D2 解除后结算返还交接过渡期；D3 已投入成本按实际发生结算 | 生产文件；只加子项 |
| ④ | D2 外 | D4–D5（2 条） | D4 各类期限以可发现事实并作出表示为下限、起算点明确；D5 不设默示失权、沉默不构成权利放弃 | 生产文件；只加子项 |
| ⑤ | E | E1–E4（4 条） | E1 成果权属按出资与组织创作归属；E2 既有知产只按必需范围授权；E3 数据合规义务与责任分担；E4 保密双向对等＋例外情形 | 生产文件；只加子项 |
| ⑥ | — | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 生产文件改动数：**1 个**（第 2 个为本验证记录）。
- **未动**：C 节（C1–C6 既有 7 处「标准立场」）与 K 节（既有 3 处）**一字未改**；F/G/H/I/J 节留待 D4b，本批未动；未加任何节、未改任何既有检查问句。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件（含 `.work/d3-findings.md`、`.work/p0-comparison.md`）未改；`shared/templates/legal-opinion.md` 未改；`industries/` 下全部文件未改；F 盘其他项目未改。
- **未提交、未推送**，改动全部留在工作区。

### 三、简报验收命令与真实输出（逐条复跑，2026-09-15）

**验收 1（19 条每条有标准立场句）**

```powershell
Select-String -Path 'shared/checklists/contract-redlines.md' -Pattern '^  - 标准立场：'
# → 25 处命中，行号：10,12,14,16,18（A1–A5）23,25,27,29,31（B1–B5）53,55,57（D1–D3）
#   62,64（D4–D5）69,71,73,75（E1–E4）＋ C1–C6 既有 6 处（38,40,42,44,46,48）
Select-String -Path 'shared/checklists/contract-redlines.md' -Pattern '标准立场'
# → 总数 29 行 = 新增 19 处子项 + C 节 6 处子项 + C 节声明 1 行 + K 节 3 处既有引用
```

结论：**通过**。A/B/D/D2外/E 共 **19 条**新增子项计数正确（5+5+3+2+4＝19；按节实测 A/B 段 10 处、D/D2外/E 段 9 处，合计 19），行号分区与节标题相符（A 节 7 行、B 节 20 行、C 节 33–49 行、D 节 50 行、D2 外 59 行、E 节 66 行）；C 节既有 6 处子项行号 38、40、42、44、46、48 未变。

**验收 2（无新增法条编号）**

```powershell
git diff shared/checklists/contract-redlines.md | Select-String '^\+.*第[一二三四五六七八九十百]+条'
# → 无命中（none）
```

结论：**通过**。新增 19 句内不含「第×条／第×款」类条号引用；本批未做法条联网核验，也未写入任何待核实的条号（口径见第一节）。

**验收 3（D4a 附录在验证记录）**

```powershell
Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'D4a'
# → 命中（本附录标题与本节各条）
```

结论：**通过**。附录含口径声明（第一节）、落盘表（第二节）、验收命令与真实输出（本节）、边界（第四节）。

**验收 4（基线未变、未提交推送、改动范围）**

```powershell
git diff --name-only
# → docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
#   shared/checklists/contract-redlines.md
git log -1 --format=%H
# → 9c84a5417bef3d2f90a304f432a7315c52c501df（基线未变）
git status --porcelain
# → 仅上述 2 个文件为 M，另有任务开始前既存的 ?? .workbuddy-ai/
```

结论：**通过**。改动仅 2 个文件、全部在简报允许清单内；基线仍为 `9c84a54`，**未提交、未推送**；`git diff` 中 C 节与 K 节无任何改动行。

### 四、本附录边界

- 本附录记录的是**文本与结构落盘 + 静态自验**，与第 0 节同一口径：不证明宿主动态行为（标准立场是否会被真的用于产出、宿主是否真的先问组织范本，均需宿主实测）。
- **未做**：法条编号的联网核验（本批按硬口径不写条号，故无核验对象）；标准立场在中国的裁判倾向实证；A/B/D/E 各节的组织范本优先声明（见第一节第三项）；F/G/H/I/J 节的标准立场（留待 D4b）；与 `industries/` 行业包的交叉核验。
- 上游原文只读未改；本批新增内容为对上游机制思想的中文重写，未复制其原文整句；本轮改动全部留在工作区，未提交、未推送。

---

AI生成，不构成法律意见。

## 附录（2026-09-15）：D4b 红线清单 F/G/H/I/J 标准立场落盘（22 条）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记、前述七处附录与 D4a 附录的任何结论。
> 版权口径（硬）：标准立场均为**中国化重写**，只做思想与结构借鉴，未搬任何上游或出版物原文整句。
> 引用口径（硬）：本批新增文字**不写任何法条编号**，只写应然规则与可退让方向；未做条文级联网核验，故本批不产生、也不声称任何条文引用。

### 一、口径声明（本批三条硬口径）

| # | 口径 | 本批执行方式 |
| --- | --- | --- |
| ① | 不写法条编号 | 新增 22 句标准立场中无「第×条／第×款／第×项」类引用；依据只以应然规则表述，未写入任何待核实条号 |
| ② | 只写应然规则与可退让方向 | F/G/I/J 每条子项为「标准立场：…（应然标准）。可退让方向：…（可让的部分与不可让的底线）」两段式，与 C1–C6 及 D4a 体例一致 |
| ③ | 不重复既有检查文字 | 每条子项不复述该条既有的核对问句，只写该条应对齐的应然标准与谈判底线 |

- **H 节写法（按简报特殊规定）**：H-1–H-5 的应然＝出现即按既有升级规则处理（定级高或极高、经总监复核门、同步告知用户），可退让方向写**不可退让的程序动作**（定级不降档、复核门不省略、告知不迟延），不写实体条款上的让步空间。H 节既有升级说明行（现第 110 行）**未改一字**。
- **未写组织优先声明的原因（如实登记）**：D4a 附录已登记「C 节标题下已有组织文件优先声明，A/B/D/E 未加声明句」；本批简报同样规定 F/G/H/I/J 不加节、不加声明句，故本批**未在 F/G/H/I/J 节重复该声明**，该问题继续留待后续批次决定。
- **来源与署名**：本批**未新引入任何来源**，沿用 P1 附录已署名的来源与许可核验结论（见 D4a 附录第一节），未重复核验；未使用任何 CC BY-NC / CC BY-ND、无许可来源或出版物原文。

### 二、落盘表（22 条，全部在简报允许清单内）

| # | 节 | 条目 | 落盘内容 | 性质 |
| --- | --- | --- | --- | --- |
| ① | F | F1–F4（4 条） | F1 管辖明确、唯一且可受理，禁「各自向所在地法院起诉」；F2 以中国法与境内机构管辖为默认，境外管辖须有对价保障；F3 争议期间除争议部分外继续履行；F4 送达地址与电子送达生效时点、变更通知义务 | 生产文件；只加子项 |
| ② | G | G1–G3（3 条） | G1 效力顺序排定、后签优于先签、正文优于附件；G2 不一致一律以书面签署文件为准、排除口头说明；G3 签署要件齐备、多语种以中文版本优先 | 生产文件；只加子项 |
| ③ | H | H-1–H-5（5 条） | 应然＝出现即进入既有升级规则（定级高或极高＋总监复核门＋同步告知用户）；可退让方向＝不可退让的程序动作（定级不降档、复核门不省略、告知不迟延） | 生产文件；只加子项 |
| ④ | I | I1–I5（5 条） | I1 转让限制与手续清单一致、不绕开优先购买权；I2 付款与交割互为条件、分期须有担保；I3 回购主体具备履行能力、先核减资与债权人路径；I4 优先权利与章程一致、冲突以修订文件消除；I5 出资与变更登记配合义务＋完成时限 | 生产文件；只加子项 |
| ⑤ | J | J1–J5（5 条） | J1 廉洁承诺覆盖员工高管关联方与第三方；J2 第三方违规视为相对方违规；J3 审计范围以本合同账目为限并对等；J4 数据保密先与既有标准对齐、跨境敏感须合规岗书面结论；J5 解除权与已付款项处理、涉刑按红线项升级 | 生产文件；只加子项 |
| ⑥ | — | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 生产文件改动数：**1 个**（第 2 个为本验证记录）。
- **未动**：A/B/C/D/D2外/E 各节（含 C 节声明句与 C1–C6 既有子项）、K 节（含红旗清单表）**一字未改**；H-1–H-5 既有检查项行与 H 节升级说明行**未改**；未加任何节、未改任何既有检查问句。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件（含 `.work/d3-findings.md`、`.work/p0-comparison.md` 与本任务简报 `.work/task-d4b-redline-FGHIJ.md`）**只读未改**；`shared/templates/legal-opinion.md` 未改；`industries/` 下全部文件未改；F 盘其他项目未改。
- **未提交、未推送**，改动全部留在工作区。

### 三、简报验收命令与真实输出（逐条复跑，2026-09-15）

**验收 1（F/G/H/I/J 22 条每条有标准立场句）**

```powershell
Select-String -Path 'shared/checklists/contract-redlines.md' -Pattern '标准立场'
# → 51 行（D4b 前为 29 行，本批净增 22 行）
Select-String -Path 'shared/checklists/contract-redlines.md' -Pattern '^  - 标准立场：'
# → 47 处（D4b 前为 25 处，本批 +22）；本批新增行号：
#   80,82,84,86（F1–F4）91,93,95（G1–G3）100,102,104,106,108（H-1–H-5）
#   115,117,119,121,123（I1–I5）128,130,132,134,136（J1–J5）
```

结论：**通过**。22 条新增子项计数正确（4+3+5+5+5＝22）；节标题行号为 A 7、B 20、C 33、D 50、D2 外 59、E 66、F 77、G 88、H 97、I 112、J 125、K 138，行号分区与节标题相符；C1–C6 既有子项行号 38、40、42、44、46、48 与 D4a 附录所记一致，未变。

**验收 2（无新增法条编号；本批只动 F–J）**

```powershell
git diff shared/checklists/contract-redlines.md | Select-String '^\+.*第[一二三四五六七八九十百]+条'
# → 无命中（none）
git diff --no-index --unified=0 <D4b 前同文件快照> shared/checklists/contract-redlines.md | Select-String '^@@'
# → 22 个纯新增 hunk，全部落在 D4b 前文件的 79–114 行区间（F/G/H/I/J 五节）
# → 复核：删除行数 0；A–E 段（前文件 1–78 行）逐行比对差异 0；K 节段（前文件 116–147 行）差异 0
```

结论：**通过**。本批新增 22 行内不含「第×条／第×款／第×项」类条号引用；A–E、K 节零改动。
说明（如实登记）：D4a 改动仍在工作区未提交，故对基线 `9c84a54` 的 `git diff` 中同时含 D4a 在 A/B/D/E 的新增行；上表后两条命令是**本批范围**的比对（对照 D4b 前的同文件快照，快照存于仓库外 `E:\Temp\`，未写入仓库）。

**验收 3（D4b 附录在验证记录）**

```powershell
Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'D4b'
# → 命中 11 行：其中 2 行为 D4a 附录的既有提及（行号在本附录之前，不受本次追加影响）；
#   其余 9 行为本附录自身的标题行、禁止触碰核对行、验收 1/2/3 的命令行与其输出行
```

结论：**通过**。附录含口径声明（第一节）、落盘表（第二节）、验收命令与真实输出（本节）、边界（第四节）。

**验收 4（基线未变、未提交推送、改动范围）**

```powershell
git diff --name-only
# → docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
#   shared/checklists/contract-redlines.md
git log -1 --format=%H
# → 9c84a5417bef3d2f90a304f432a7315c52c501df（基线未变）
git status --porcelain
# → 仅上述 2 个文件为 M，另有任务开始前既存的 ?? .workbuddy-ai/
```

结论：**通过**。改动仅 2 个文件、全部在简报允许清单内；基线仍为 `9c84a54`，**未提交、未推送**；`git diff` 中 C 节与 K 节无任何改动行。

### 四、本附录边界

- 本附录记录的是**文本与结构落盘 + 静态自验**，与第 0 节同一口径：不证明宿主动态行为（标准立场是否会被真的用于产出、H 类升级是否会被真的触发，均需宿主实测）。
- **未做**：法条编号的联网核验（本批按硬口径不写条号，故无核验对象）；标准立场在中国的裁判倾向实证；F/G/H/I/J 各节的组织范本优先声明（见第一节第二项）；H 类升级规则在宿主中的触发实测；与 `industries/` 行业包的交叉核验。
- 上游原文只读未改；本批新增内容为对上游机制思想的中文重写，未复制其原文整句；本轮改动全部留在工作区，未提交、未推送。

---

AI生成，不构成法律意见。

## 附录（2026-09-15）：D5a 劳动红线清单 A/B/C/D 标准立场落盘（21 条）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记、前述七处附录与 D4a、D4b 附录的任何结论。
> 版权口径（硬）：标准立场均为**中国化重写**，只做思想与结构借鉴，未搬任何上游或出版物原文整句。
> 引用口径（硬）：本批新增文字**不写任何法条编号，也不写任何具体金额／倍数／天数／比例**；劳动标准属地差异大，一律写规则口径，需取具体数值时指向「按属地人社口径核实」。

### 一、口径声明（本批三条硬口径）

| # | 口径 | 本批执行方式 |
| --- | --- | --- |
| ① | 不写法条编号 | 新增 21 句标准立场中无「第×条／第×款／第×项」类引用；依据只以应然规则表述，未写入任何待核实条号 |
| ② | 不写具体数字 | 新增 21 行内不含金额、倍数、天数、比例等具体数值；涉及数值的表述统一为「法定下限／法定区间／法定订立期限／当地最低工资口径」等规则口径，并写明按属地人社口径核实 |
| ③ | 只写应然规则与可退让方向，不重复既有检查文字 | A1–A5／B1–B5／C1–C4／D1–D7 每条子项为「标准立场：…（应然标准）。可退让方向：…（可让的部分与不可让的底线）」两段式，与合同红线 C 节体例一致；每条子项不复述该条既有的核对问句 |

- **体例对齐说明**：本批体例对齐 `shared/checklists/contract-redlines.md` C 节——每条既有检查项下缩进两格新增一条「标准立场：…可退让方向：…」子项，一条一子项，不加节、不改既有检查项措辞。
- **未写组织优先声明的原因（如实登记）**：合同红线 C 节标题下有「组织文件优先、无则用本节通用立场并声明」的声明句（P1 批落盘）；劳动红线清单各节目前**没有**同类节内声明句。本批简报规定 A/B/C/D 不加节、不加声明句，故本批未在劳动红线清单补该声明。该声明是否需要在劳动红线清单补同类表述，本批不做、留待后续批次决定。
- **来源与署名**：本批**未新引入任何来源**，沿用 P1 附录已署名的来源与许可核验结论（见 D4a 附录第一节），未重复核验；未使用任何 CC BY-NC / CC BY-ND、无许可来源或出版物原文。

### 二、落盘表（21 条，全部在简报允许清单内）

| # | 节 | 条目 | 落盘内容 | 性质 |
| --- | --- | --- | --- | --- |
| ① | A | A1–A5（5 条） | A1 法定订立期限内订立书面合同、逾期补救须写明补订时点与责任；A2 试用期对应合同期限、只约定一次、工资不低于法定下限；A3 不收押金不扣证件、违约金只限法定可约定情形、已收取的无条件退还；A4 岗位地点内容具体写明、单方调岗须写明触发情形与异议渠道；A5 特殊用工形式的用工主体与责任划分逐项写明 | 生产文件；只加子项 |
| ② | B | B1–B5（5 条） | B1 工资构成与发放规则逐项写明、不低于当地最低工资口径；B2 加班以可举证记录认定、基数与工资构成一致、禁包薪替代；B3 特殊工时以行政审批为前提、未获批按标准工时口径结算；B4 法定假期待遇不低于法定下限、禁内部规则替代；B5 按期足额支付、历史欠付先做台账与补发方案 | 生产文件；只加子项 |
| ③ | C | C1–C4（4 条） | C1 涉员工切身利益制度须经民主程序并公示后方可适用；C2 违纪情形与处理一一对应、禁兜底条款作解除依据；C3 处分须有依据事实证据且给申辩机会、禁罚款体罚侮辱；C4 送达签收逐人对应可回溯 | 生产文件；只加子项 |
| ④ | D | D1–D7（7 条） | D1 解除理由落在法定情形内并写明事实与证据；D2 单方解除前完成通知沟通程序；D3 补偿赔偿的基数年限封顶逐项核算并写明过程；D4 通知期与代通知金口径、未提前通知的补救与责任；D5 禁止解除情形人员不列入名单、核实后按升级规则处理；D6 协商解除以真实意思表示为前提、放弃范围不及于法定最低保障；D7 送达方式地址与生效时点事先约定并逐次留痕可举证 | 生产文件；只加子项 |
| ⑤ | — | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 生产文件改动数：**1 个**（第 2 个为本验证记录）。
- **未动**：E/F/G/H 四节（E1–E5、F1–F4、G1–G4、H-1–H-5 及 H 节末升级说明行）**一字未动**；未加任何节、未改任何既有检查问句、未改文件头「版本」「用途」「说明」三行。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件（含 `.work/task-d4a-redline-ABDE.md`、`.work/task-d4b-redline-FGHIJ.md` 与本任务简报 `.work/task-d5a-employment-ABCD.md`）**只读未改**；`shared/templates/legal-opinion.md` 未改；`industries/` 下全部文件未改；F 盘其他项目未改。
- **未提交、未推送**，改动全部留在工作区。

### 三、简报验收命令与真实输出（逐条实跑，2026-09-15）

**验收 1（21 条每条有标准立场句）**

```powershell
Select-String -Path 'shared/checklists/employment-redlines.md' -Pattern '标准立场'
# → 21 行，行号：10,12,14,16,18（A1–A5）
#   23,25,27,29,31（B1–B5）36,38,40,42（C1–C4）47,49,51,53,55,57,59（D1–D7）
Select-String -Path 'shared/checklists/employment-redlines.md' -Pattern '标准立场' |
  Where-Object { $_.LineNumber -le 59 } | Measure-Object
# → Count = 21（全部落在 A–D 段；E 节起行号 61 以后无命中）
```

结论：**通过**。A/B/C/D 共 **21 条**新增子项，计数与分节行号均相符（5+5+4+7＝21）；文件总行数 70 → 91 行，净增 21 行。

**验收 2（无新增法条编号与具体数字；E/F/G/H 未动）**

```powershell
git diff shared/checklists/employment-redlines.md |
  Select-String '^\+.*(第[一二三四五六七八九十百]+条|[0-9]+(倍|天|日|元|%|％))'
# → 无命中（none）
git diff shared/checklists/employment-redlines.md | Select-String '^\+.*[0-9]'
# → 无命中（none）
git diff --unified=0 shared/checklists/employment-redlines.md | Select-String '^@@'
# → 21 个 hunk，全部为纯新增（形如 @@ -9,0 +10 @@），删除行数 0
(git diff shared/checklists/employment-redlines.md | Select-String '^-[^-]').Count
# → 0
Compare-Object (git show 65b8f24:shared/checklists/employment-redlines.md)[39..69] `
  (Get-Content 'shared/checklists/employment-redlines.md')[60..90]
# → tail identical：E/F/G/H 与 H 节末说明行（基线 40–70 行 vs 现 61–91 行）逐行一致
```

结论：**通过**。新增 21 行内不含条号引用，也不含任何阿拉伯数字（金额／倍数／天数／比例零新增）；21 个 hunk 均为纯插入，**零删除行**，插入位置全部落在基线 9–38 行区间（A/B/C/D 四节）；E/F/G/H 段与基线逐行一致。

**验收 3（D5a 附录在验证记录）**

```powershell
Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'D5a'
# → 命中（本附录标题行与本批各条记录）
```

结论：**通过**。附录含口径声明（第一节）、落盘表（第二节）、验收命令与真实输出（本节）、边界（第四节）。

**验收 4（基线未变、未提交推送、改动范围）**

```powershell
git diff --name-only
# → docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
#   shared/checklists/employment-redlines.md
git log -1 --format=%H
# → 65b8f24…（基线未变）
git status --porcelain
# → 仅上述 2 个文件为 M，另有任务开始前既存的 ?? .workbuddy-ai/
```

结论：**通过**。改动仅 2 个文件、全部在简报允许清单内；基线仍为 `65b8f24`，**未提交、未推送**。

### 四、本附录边界

- 本附录记录的是**文本与结构落盘 + 静态自验**，与第 0 节同一口径：不证明宿主动态行为（标准立场是否会被真的用于产出、宿主是否真的按属地人社口径取数，均需宿主实测）。
- **未做**：法条编号与具体数值的联网核验（本批按硬口径不写条号与数值，故无核验对象）；属地人社口径的逐地取数；标准立场在中国的裁判倾向实证；劳动红线清单的组织范本优先声明（见第一节第二项）；E/F/G/H 四节的标准立场（本批按简报规定不做）；与 `industries/` 行业包的交叉核验。

---

AI生成，不构成法律意见。

## 附录（2026-09-15）：D5b 劳动红线清单 E/F/G/H 标准立场落盘（18 条）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记、前述七处附录与 D4a、D4b、D5a 附录的任何结论。
> 版权口径（硬）：标准立场均为**中国化重写**，只做思想与结构借鉴，未搬任何上游或出版物原文整句。
> 引用口径（硬）：本批新增文字**不写任何法条编号，也不写任何具体金额／倍数／天数／比例**；劳动标准属地差异大，一律写规则口径，需取具体数值时指向「按属地人社口径核实」。

### 一、口径声明（本批三条硬口径）

| # | 口径 | 本批执行方式 |
| --- | --- | --- |
| ① | 不写法条编号 | 新增 18 句标准立场中无「第×条／第×款／第×项」类引用；依据只以应然规则表述，未写入任何待核实条号 |
| ② | 不写具体数字 | 新增 18 行内不含金额、倍数、天数、比例等具体数值；涉及数值的表述统一为「法定申报时限／法定程序／约定周期／群体门槛（沿用该条既有表述）」等规则口径，并写明按属地人社口径核实 |
| ③ | 只写应然规则与可退让方向，不重复既有检查文字 | E1–E5／F1–F4／G1–G4／H-1–H-5 每条子项为「标准立场：…（应然标准）。可退让方向：…（可让的部分与不可让的底线）」两段式，与合同红线 C 节及 D5a 体例一致；每条子项不复述该条既有的核对问句 |

- **H 节写法（按简报特殊规定，同 D4b）**：H-1–H-5 的应然＝出现本项即进入既有升级规则（定级高或极高、经总监复核门、同步告知用户），并写明同期须固定的事实与须暂停的动作；可退让方向写**不可退让的程序动作**（定级不降档、总监复核门不省略、告知不迟延），不写实体条款上的让步空间。H 节既有升级说明行（现第 109 行）**未改一字**。
- **体例对齐说明**：本批体例对齐 D5a——每条既有检查项下缩进两格新增一条「标准立场：…可退让方向：…」子项，一条一子项，不加节、不改既有检查项措辞、不改文件头三行。
- **未写组织优先声明的原因（如实登记）**：合同红线 C 节标题下有组织文件优先的声明句；劳动红线清单各节目前没有同类节内声明句。D5a 附录已登记该问题留待后续批次决定，本批简报同样规定不加节、不加声明句，故**未补该声明**。
- **来源与署名**：本批**未新引入任何来源**，沿用 P1 附录已署名的来源与许可核验结论（见 D4a 附录第一节），未重复核验；未使用任何 CC BY-NC / CC BY-ND、无许可来源或出版物原文。

### 二、落盘表（18 条，全部在简报允许清单内）

| # | 节 | 条目 | 落盘内容 | 性质 |
| --- | --- | --- | --- | --- |
| ① | E | E1–E5（5 条） | E1 竞业限制只适用于接触商业秘密与核心保密信息的岗位、落到岗位或名单；E2 补偿按约定周期支付为限制义务对价、未付须写明后果并结算；E3 期限地域业务范围与实际竞争关系对应、到期自动解除；E4 违约金与实际损失及已付补偿对应、写明计算方式与上限；E5 保密与竞业分别约定不互相替代、离职义务以清单写明 | 生产文件；只加子项 |
| ② | F | F1–F4（4 条） | F1 依法参保缴费、按实际工资口径申报、未缴漏缴先核清再写补缴方案；F2 法定义务不因书面承诺免除、放弃文件作废转依法参保；F3 按法定申报时限启动工伤认定、明确责任岗与待遇支付路径；F4 商业险为补充、不替代法定参保与法定待遇义务 | 生产文件；只加子项 |
| ③ | G | G1–G4（4 条） | G1 达群体门槛先定级走复核门并登记、禁拆分数或改名目降为个案；G2 集体协商与职代会程序完成后实施、留痕齐备；G3 统一口径统一出口、事实与证据先行、对外发声经复核门；G4 先核法定情形与程序要件、报告备案时点明确、程序与补偿落实前不发解除通知 | 生产文件；只加子项 |
| ④ | H | H-1–H-5（5 条） | 应然＝出现即进入既有升级规则（定级高或极高＋总监复核门＋同步告知用户）并写明同期固定证据与暂停动作；可退让方向＝不可退让的程序动作（定级不降档、复核门不省略、告知不迟延） | 生产文件；只加子项 |
| ⑤ | — | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 生产文件改动数：**1 个**（第 2 个为本验证记录）。
- **未动**：A/B/C/D 四节（A1–A5、B1–B5、C1–C4、D1–D7 及 D5a 已落盘的 21 处「标准立场」）**一字未动**；H 节既有升级说明行未改；未加任何节、未改任何既有检查问句、未改文件头「版本」「用途」「说明」三行。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件（含 `.work/task-d4a-redline-ABDE.md`、`.work/task-d4b-redline-FGHIJ.md`、`.work/task-d5a-employment-ABCD.md` 与本任务简报 `.work/task-d5b-employment-EFGH.md`）**只读未改**；`shared/templates/legal-opinion.md` 未改；`industries/` 下全部文件未改；F 盘其他项目未改。
- **未提交、未推送**，改动全部留在工作区。

### 三、简报验收命令与真实输出（逐条实跑，2026-09-15）

**验收 1（18 条每条有标准立场句）**

```powershell
Select-String -Path 'shared/checklists/employment-redlines.md' -Pattern '标准立场'
# → 39 行，行号：10,12,14,16,18（A1–A5，D5a）
#   23,25,27,29,31（B1–B5，D5a）36,38,40,42（C1–C4，D5a）47,49,51,53,55,57,59（D1–D7，D5a）
#   64,66,68,70,72（E1–E5，本批）77,79,81,83（F1–F4，本批）
#   88,90,92,94（G1–G4，本批）99,101,103,105,107（H-1–H-5，本批）
```

结论：**通过**。全文件 39 处 = D5a 既有 21 处 + 本批新增 **18** 处（5+4+4+5＝18）；文件总行数 91 → 109 行，净增 18 行。

**验收 2（无新增法条编号与具体数字；A/B/C/D 未动）**

```powershell
git diff shared/checklists/employment-redlines.md |
  Select-String '^\+.*(第[一二三四五六七八九十百]+条|[0-9]+(倍|天|日|元|%|％))'
# → 无命中（none）
Select-String -Path 'shared/checklists/employment-redlines.md' -Pattern '标准立场' |
  Where-Object { $_.LineNumber -le 59 } | Measure-Object
# → Count = 21（与 D5a 附录所记 21 处及行号完全一致，A–D 段未变）
git diff --unified=0 shared/checklists/employment-redlines.md | Select-String '^@@'
# → 相对基线共 39 个 hunk，均为纯插入（形如 @@ -42,0 +64 @@）：前 21 个为 D5a 遗留（插入点 -9..-38，A–D 段），
#   本批 18 个插入点为 -42,-43,-44,-45,-46,-50,-51,-52,-53,-57,-58,-59,-60,-64,-65,-66,-67,-68，新增行落在现第 64–107 行（E 节起），删除行数 0
```

结论：**通过**。新增 18 行内不含条号引用与金额／倍数／天数／比例数值；A–D 段 21 处子项行号与计数与 D5a 附录一致，未动。

**验收 3（D5b 附录在验证记录）**

```powershell
Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'D5b'
# → 命中（本附录标题行与本批各条记录）
```

结论：**通过**。附录含口径声明（第一节）、落盘表（第二节）、验收命令与真实输出（本节）、边界（第四节）。

**验收 4（基线未变、未提交推送、改动范围）**

```powershell
git diff --name-only
# → docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
#   shared/checklists/employment-redlines.md
git log -1 --format=%H
# → 65b8f24…（基线未变）
git status --porcelain
# → 仅上述 2 个文件为 M，另有任务开始前既存的 ?? .workbuddy-ai/
```

结论：**通过**。改动仅 2 个文件（含 D5a 遗留改动，实为同一对文件）、全部在简报允许清单内；基线仍为 `65b8f24`，**未提交、未推送**。

### 四、本附录边界

- 本附录记录的是**文本与结构落盘 + 静态自验**，与第 0 节同一口径：不证明宿主动态行为（标准立场是否会被真的用于产出、H 类升级是否会被真的触发、宿主是否真的按属地人社口径取数，均需宿主实测）。
- **未做**：法条编号与具体数值的联网核验（本批按硬口径不写条号与数值，故无核验对象）；属地人社口径的逐地取数；标准立场在中国的裁判倾向实证；劳动红线清单的组织范本优先声明（见第一节）；A/B/C/D 四节（D5a 已落盘）的复核；与 `industries/` 行业包的交叉核验。
- 上游原文只读未改；本批新增内容为对上游机制思想的中文重写，未复制其原文整句；本轮改动全部留在工作区，未提交、未推送。

---

AI生成，不构成法律意见。

## 附录（2026-09-15）：D6 建工深化（解释二落点＋待核实闭环＋签证索赔时效表）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记、前述七处附录与 D2、D4a、D4b、D5a、D5b 附录的任何结论。
> 取材口径（硬）：解释二逐条已 `web_fetch` 最高法官网原文页逐条核对；示范文本时限以公开文本为出处；所有天数／期限**无公开出处的一律不写**，改写为「以合同文本与示范文本为准」，禁臆断。
> 版权口径（硬）：新增文字为**中文重写**，只引条文要点与文号，未复制任何上游或出版物原文整句。

### 一、取材与核验（逐条实抓，2026-09-15）

| # | 来源 | 抓取方式 | 核验到的内容 |
| --- | --- | --- | --- |
| ① | 解释二全文（法释〔2026〕12号） | `web_fetch` https://www.court.gov.cn/zixun/xiangqing/504221.html 全文返回 | **二十三条逐条在线读到原文**（第一条至第二十三条完整正文），含 2026年3月17日审判委员会第1969次会议通过、自2026年6月30日起施行；第二十三条三款（施行日／新受理一审案件适用／不一致以本解释为准） |
| ② | 示范文本印发通知 | `web_fetch` https://www.mohurd.gov.cn/gongkai/zc/wjk/art/2017/art_17339_233757.html | 建市〔2017〕214号，GF-2017-0201，自2017年10月1日起执行，GF-2013-0201 同时废止；附件为示范文本（官网附件为 `.doc`，`web_fetch` 返回 `unsupported content type "application/msword"`，未能读取） |
| ③ | 示范文本通用合同条款全文（用于补足②附件不可读） | `web_fetch` 公开文本 http://www.dlztb.com/news/202303/14/80948.html | 在线读到原文条款：19.1（索赔意向通知书 28 天／索赔报告 28 天／最终索赔报告 28 天）、19.2（监理人 14 天审查、发包人 28 天出具结果，逾期视为认可）、19.3、19.5、14.1（竣工验收合格后 28 天提交竣工结算申请单）、14.2（监理人 14 天核查、发包人 14 天审批、28 天未审批未异议视为认可并自第 29 天起视为签发竣工付款证书）、17.2、17.4、20.3 |
| ④ | 示范文本 15.2.1／15.3.3 与缺陷责任期上限 | `web_fetch` https://www.icourt.cc/prac-document/1578.html （脚注逐条引 2017 通用条款原文） | 15.2.1 缺陷责任期自工程通过竣工验收之日起算、专用条款约定具体期限**最长不超过 24 个月**；15.3.3 返还程序（接到申请后 14 天核实、无约定或约定不明在核实后 14 天返还、14 天不答复经催告 14 天仍不答复视同认可） |
| ⑤ | 质量保证金办法 | `web_fetch` https://www.gov.cn/gongbao/content/2017/content_5244884.htm | 建质〔2017〕138号第二条（缺陷责任期一般为1年，最长不超过2年，由发承包双方在合同中约定）、第十一条（14 天核实／核实后 14 天返还／催告后 14 天视同认可） |
| ⑥ | 建设工程质量管理条例第四十条 | `web_fetch` https://www.gov.cn/gongbao/content/2019/content_5468867.htm （现行有效版）与 https://www.gov.cn/gongbao/content/2000/content_60658.htm （279号原文） | **两版第四十条逐字一致**（2000年1月30日国务院令第279号发布，根据2017年10月7日、2019年4月23日两次修订，第四十条均未被改）：最低保修期限四类（合理使用年限／5年／2个采暖期供冷期／2年）、其他项目由发承包双方约定、保修期自竣工验收合格之日起计算 |
| ⑦ | 解释一（法释〔2020〕25号） | `web_fetch` https://www.court.gov.cn/zixun/xiangqing/282111.html | 第四十一条原文「承包人应当在合理期限内行使建设工程价款优先受偿权，但最长不得超过十八个月，自发包人应当给付建设工程价款之日起算」；另核到第十条（工期顺延签证）、第十七条（未约定质保金返还期限满二年）、第二十条（签证确认工程量）、第二十一条（逾期不答复视为认可竣工结算文件） |

- **未能取得的来源（如实登记）**：示范文本在住建部官网的附件为 `.doc`，`web_fetch` 返回 `unsupported content type "application/msword"`，故②只证明文号、名称与执行日期，**条款原文以③④公开文本为准**；北京公共资源交易服务平台的 `.pdf` 形态示范文本全文同样被 `web_fetch` 以 `fetch failed` / `unsupported content type "application/pdf"` 拒绝（已试两次），未使用——故出处清单中该 `.pdf` 只作为「示范文本全文另有公开文本」的补充指引，本批**未读取其正文**。
- **订正（2026-09-15 审查订正）**：③ 当时**未读到第 10 条〔变更〕**，本批据此把签证确认时限写成「示范文本未规定」，该结论已订正——10.4.2〔变更估价程序〕原文见本节「三之补记（2026-09-15 审查订正）」。另：本节与出处清单所引北京公共资源交易服务平台 `.pdf`（`.../202602/5285085431672.pdf`）本轮复核时实测为「小月河滨水空间建设工程区级配套工程（亮化工程）施工招标文件」（236 页），**不是示范文本全文**；10.4.2 原文本轮改从住建部官网附件（`.doc`，经 `document/download` 接口取得）解析读出。

### 二、落盘改动（5 个文件，全部在简报允许清单内）

| # | 文件 | 落盘内容 | 性质 |
| --- | --- | --- | --- |
| ① | `industries/construction/SKILL.md` | 仅三处：I-4 行「待核实」改为已核验期限结论（解释一第41条十八个月／解释二第21条变更后起算）；I-6 行「待核实」改为示范文本 19.1 的 28 天口径，并把签证确认时限由「示范文本未规定」订正为示范文本 10.4.2（14／7／14 天，逾期未审批且未提异议视为认可，见本节「三之补记（2026-09-15 审查订正）」）；新增 **2.1 解释二逐条落点表（23 行，一条一落点）**；第 3 节新增 1 行时效表指针行 | 生产文件；三处改动＋一张表＋本轮订正 1 处 |
| ② | `industries/construction/checklists/construction-contract.md` | 仅两行：变更签证索赔时限行闭环（写示范文本 19.1 的 28 天与逾期失权，签证确认时限原写「以合同文本与示范文本为准」，本轮订正为示范文本 10.4.2 的 14／7／14 天，指向 `claim-limitation.md`）；结算与审计付款条件行闭环（解释二第13条） | 生产文件；两行＋本轮订正 1 处 |
| ③ | `industries/construction/checklists/completion-settlement-safety.md` | 仅两行：优先受偿期限行闭环（解释一第41条十八个月＋解释二第21条起算，标注法定上限不因约定延长）；审计付款条件行闭环（解释二第13条，含合理期限不超过提交竣工结算文件之日起一年） | 生产文件；两行 |
| ④ | `industries/construction/checklists/claim-limitation.md` | **新建**：签证索赔时效表——一、六类期限（索赔意向／索赔报告／索赔审查／签证确认／竣工结算／质保金返还／优先受偿期限／最低保修期限共 8 行，每行含时限、出处、合同优先声明；签证确认行本轮订正为示范文本 10.4.2 的 14／7／14 天）；二、期限外两点；三、属地核实指向；四、出处清单（本轮补入「实际核对用转载」一行） | 新增生产文件＋本轮订正 2 处 |
| ⑤ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 生产文件改动数：**4 个**（第 5 个为本验证记录）。
- **未动**：`industries/construction/regulations.md`（D1 已定，本批**只读未改**）；`shared/` 下全部文件；`industries/construction/checklists/` 下 `bidding-compliance.md`、`migrant-wage.md` 未改；既有一切锚点 I-1／I-2／I-3／I-5／I-7 行措辞未改；SKILL.md 文件头四行（版本／知识截至日期／更新归属／过期预警）与导语块未改。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件（含本任务简报 `.work/task-d6-construction-deep.md`）**只读未改**；`shared/templates/legal-opinion.md` 未改；F 盘其他项目未改。
- **未提交、未推送**，改动全部留在工作区。

### 三、简报验收命令与真实输出（逐条实跑，2026-09-15）

**验收 1（I-4／I-6／结算两处待核实闭环）**

```powershell
Select-String -Path 'industries/construction/SKILL.md',
  'industries/construction/checklists/construction-contract.md',
  'industries/construction/checklists/completion-settlement-safety.md' -Pattern '待核实'
```

真实输出（1 行）：

```text
F:\AIXM\Legal Dept\industries\construction\SKILL.md:13: > 本包不改变四级风险标尺与三条贯穿硬规则，只补充判定锚点与检查项。总纲分诊到岗位后，岗位按本包加载行业检查项；未挂载本包时，岗位对行业事项按「待核实」处理并提示用户向行业主管部门核实。
```

结论：**通过**。三文件原有「待核实」6 处（SKILL.md 第 13／26／28 行，construction-contract.md 第 9 行，completion-settlement-safety.md 第 8／10 行），其中 **5 处期限与要点类全部改为已核验结论**；仅剩 SKILL.md 第 13 行 1 处，为「未挂载本包时按待核实处理并提示向行业主管部门核实」的属地／主管部门类处置，属验收标准允许的残留。

验收标准的另一半（「合同文本类」）落法：本批在 `construction-contract.md` 第 9 行与新建时效表签证确认行写入「以合同文本与示范文本为准」，但该表述**不含**「待核实」三字，故简报指定的三文件内**已无合同文本类残留**（如实登记：这是比验收标准更严格的结果，不是漏做）。

补充实测（简报命令未覆盖的本包其余文件，逐条说明残留性质）：

```powershell
Select-String -Path 'industries/construction/checklists/*.md' -Pattern '待核实'
# → 2 行，均为属地类（本批未改动这两个文件，不在允许清单内）：
#   bidding-compliance.md:5  「是否属于依法必须招标的范围（标准与限额以现行规定与属地口径为准，标『待核实』）」  ← 属地类
#   migrant-wage.md:8        「工资保证金已按属地标准存储（标准向属地人社部门核实，标『待核实』）」          ← 属地类
#   construction-contract.md、completion-settlement-safety.md、claim-limitation.md 三文件均为 0
```

即：本包「待核实」实测残留 **3 处**，全部为属地／行业主管部门类（SKILL.md 第 13 行、bidding-compliance.md 第 5 行、migrant-wage.md 第 8 行），

「合同文本类」残留 **0 处**，符合验收标准 1。

**验收 2（解释二 23 条逐条有落点）**

```powershell
(Select-String -Path 'industries/construction/SKILL.md' -Pattern '解释二').Count
# → 26
(Select-String -Path 'industries/construction/SKILL.md' -Pattern '^\| 解释二第[一二三四五六七八九十]+条' | Measure-Object).Count
# → 23（表格行，行号 37–59）
```

结论：**通过**。落点表 23 行覆盖解释二第一条至第二十三条，一条一落点（锚点或岗位清单），每行括注该条要点；命中数 26 ≥ 简报要求的 23（表外另有 I-4 行、落点表依据行与 2.1 标题行各 1 处「解释二」）。原文依据为最高法官网全文（见第一节①）。

**验收 3（时效表新文件存在且有合同优先／属地声明）**

```powershell
Test-Path 'industries/construction/checklists/claim-limitation.md'                                             # → True
(Select-String -Path 'industries/construction/checklists/claim-limitation.md' -Pattern '以合同为准').Count      # → 4
(Select-String -Path 'industries/construction/checklists/claim-limitation.md' -Pattern '属地').Count            # → 4
(Select-String -Path 'industries/construction/checklists/claim-limitation.md' -Pattern '^\| [0-9]+ \|').Count   # → 8（期限行）
```

结论：**通过**。六类期限（索赔意向／索赔报告／签证确认／竣工结算／质保金返还／优先受偿期限）全部落表，另补索赔审查与最低保修期限两行，共 8 个期限项；每行含「时限＋出处＋合同优先声明」三段；法定上限与法定最低标准单列声明。

**验收 4（D6 附录在验证记录）**

```powershell
Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'D6'
# → 7 行（本附录标题行、落盘表与本节各条记录）
```

结论：**通过**。附录含取材与核验（第一节）、落盘改动（第二节）、验收命令与真实输出（本节）、边界（第四节）。

**验收 5（改动范围与基线）**

```powershell
git diff --name-only
# → docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
#   industries/construction/SKILL.md
#   industries/construction/checklists/completion-settlement-safety.md
#   industries/construction/checklists/construction-contract.md
git status --porcelain
# → 上述 4 个 M，加 ?? industries/construction/checklists/claim-limitation.md
#   与任务开始前既存的 ?? .workbuddy-ai/
git log -1 --format=%H
# → b76b21f761a1110f473ba30ca7e73c913092b68f
git diff 65b8f24 b76b21f --name-only
# → docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
#   shared/checklists/employment-redlines.md
```

结论：**通过（含一处如实记录的偏差）**。改动仅 5 个文件、全部在简报允许清单内；**未提交、未推送**。**偏差登记**：简报写基线为 `65b8f24`，实测 HEAD 为 `b76b21f`（D5 提交，晚于简报所述基线）；该提交只改 `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` 与 `shared/checklists/employment-redlines.md`，**未触碰本批 5 个文件中的任何生产文件**，本批落盘内容不受影响；D5 提交非本会话产生，本会话仍**未提交、未推送**。

### 三之补记（2026-09-15 审查订正）：签证确认时限结论订正 + 本轮审查来源登记

> 本条为本附录**取证结论的订正记录**，不改变本节各条验收命令的实测输出与通过结论（命令仍可复跑），只订正第一节取证表与第四节边界中原「示范文本未规定签证确认天数」的错误结论。

**一、被订正的错误结论**

原结论（本附录第一节、第三节口径取舍登记均记）：签证（变更确认）一项「示范文本通用合同条款未规定具体天数」，据此不写天数、改记「以合同文本与示范文本为准」。

**该结论不成立。** 示范文本通用合同条款 10.4.2〔变更估价程序〕原文已规定具体天数与逾期后果，本批当时只逐条抓取了 19.1／19.2／14.1／14.2／15.2.1／15.3.3 等条款，**未抓到第 10 条〔变更〕**，据「未抓到」直接写成「未规定」，属取证不全导致的错误结论，非示范文本本身无规定。

**二、本轮审查来源登记（触发订正的来源）**

| 项 | 内容 |
| --- | --- |
| 来源 | `code-reviewer` 子智能体独立复核（2026-09-15） |
| 复核方式 | 独立 `web_fetch` 最高法官网解释一、解释二原文与示范文本公开全文，逐字核对本批落盘文字 |
| 复核结论 | 驳回；问题成立——「签证确认时限示范文本未规定」与 10.4.2 原文不符；解释二第七条落点表括注与原文主体不一致 |
| 本批处置 | 按复核意见订正下述 6 处，订正范围不超出复核指出的问题 |

**三、订正后重新核对（第二轮取证，2026-09-15）**

1. **示范文本 10.4.2 原文（本轮实测取得）**：住房城乡建设部官网建市〔2017〕214号文的附件（`https://www.mohurd.gov.cn/gongkai/zc/wjk/art/2017/art_17339_233757.html` 的 `document/download` 接口，`.doc`）本轮**已成功下载**并通过 OLE 复合文档解析逐字读出正文，其中 10.4.2〔变更估价程序〕原文为：「承包人应在收到变更指示后14天内，向监理人提交变更估价申请。监理人应在收到承包人提交的变更估价申请后7天内审查完毕并报送发包人，监理人对变更估价申请有异议，通知承包人修改后重新提交。发包人应在承包人提交变更估价申请后14天内审批完毕。发包人逾期未完成审批或未提出异议的，视为认可承包人提交的变更估价申请。」另读取到 10.4.1 为变更估价原则、10.5 为合理化建议（监理人 7 天审查报送、发包人 7 天审批）。
2. **解释一第10条、第20条原文（本轮重新抓取）**：第10条第一款为「当事人约定顺延工期应当经发包人或者监理人签证等方式确认，承包人虽未取得工期顺延的确认，但能够证明在合同约定的期限内向发包人或者监理人申请过工期顺延且顺延事由符合合同约定，承包人以此为由主张工期顺延的，人民法院应予支持」；第二款为「当事人约定承包人未在约定期限内提出工期顺延申请视为工期不顺延的，按照约定处理，但发包人在约定期限后同意工期顺延或者承包人提出合理抗辩的除外」。第20条为「当事人对工程量有争议的，按照施工过程中形成的签证等书面文件确认。承包人能够证明发包人同意其施工，但未能提供签证文件证明工程量发生的，可以按照当事人提供的其他证据确认实际发生的工程量」。
3. **解释二第七条原文（本轮重新抓取）**：第七条主体为「借用资质的单位或者个人、接受转包或者违法分包的单位或者个人依据民法典第五百三十五条关于代位权的规定，以出借资质的建筑施工企业、转包人或者违法分包人怠于行使到期债权或者与该债权有关的从权利，影响其到期债权实现为由，向发包人行使代位权的，人民法院依法予以支持」——主体为出借资质、转包、违法分包情形下的**单位或者个人**，原文未出现「农民工」，故落点表括注按原文主体改为「借用资质、转包或违法分包中的实际施工人代位权」；I-3 落点予以保留（该条不覆盖 I-3 定义的农民工工资专用账户／总包代发／工资保证金三项合规义务，但仍是实际施工人索偿路径的关联条款）。

**四、本轮订正的 6 处（逐条）**

| # | 文件 | 订正内容 |
| --- | --- | --- |
| 1 | `industries/construction/checklists/claim-limitation.md` 第 4 行签证确认 | 时限栏补入 10.4.2 的 14／7／14 天与逾期未审批且未提异议视为认可；删除「示范文本未规定具体天数」与「以合同文本与示范文本为准」；出处栏改为「示范文本通用合同条款 10.4.2＋解释一第10条、第20条」，删除「未规定确认天数」；「合同另有约定以合同为准」保留 |
| 2 | `industries/construction/SKILL.md` I-6 行 | 「签证确认时限示范文本未规定」改为 10.4.2 的 14／7／14 天（逾期视为认可）并指向时效表 |
| 3 | `industries/construction/checklists/construction-contract.md` 第 9 行 | 删除「签证的确认时限示范文本未作规定，以合同文本与示范文本为准」，改指 10.4.2 天数 |
| 4 | 本验证记录 D6 附录 | 本条订正 + 第一节取证表、第三节口径取舍登记与第四节边界的相应订正；登记本轮审查来源（code-reviewer 独立逐字复核） |
| 5 | `industries/construction/SKILL.md` 落点表第七条 | 括注由「农民工、实际施工人对发包人的代位权」改为与原文主体一致的「借用资质、转包或违法分包中的实际施工人代位权」；I-3 落点经原文比对**有依据，保留** |
| 6 | `industries/construction/checklists/claim-limitation.md` 第四节出处清单 | 补一行「示范文本（实际核对用转载）」：官网 `.doc`／`.pdf` 附件此前无法抓取，实际逐条核对用 dlztb（19.1／19.2／14.1／14.2）与 iCourt（15.2.1／15.3.3）公开转载页 |

**五、本条边界**

- 本轮只做上述 6 处订正，未改动本批 5 个允许文件的其他内容，未提交、未推送。
- 10.4.2 原文本轮取自住建部官网附件（`.doc`）解析结果；该附件为 2017 年修订版印发件，条款编号与现行有效版本一致，引用前仍应按本附录原有口径复核版本。

### 三之补记二（2026-09-15 裁决收尾）：出处清单移除不相关 `.pdf` 链接

> 本条为**出处清单的订正记录**，不改变本附录其他结论与已列验收命令的实测输出。

**一、移除对象**：`industries/construction/checklists/claim-limitation.md` 第四节出处清单「示范文本」行中的北京公共资源交易服务平台链接 `https://ggzyfw.beijing.gov.cn/cmsbj/u/cms/cn.gov.bjggzyfw.www/202602/5285085431672.pdf`。

**二、移除理由**：该 `.pdf` 经实读确认为**不相关的施工招标文件**（非示范文本全文），不能作为示范文本的公开来源，保留构成误导性出处。

**三、改动范围**：只删该行内该链接及其引导语「；全文公开文本另见北京公共资源交易服务平台 」；该行其余文字（文号、示范文本名称、条款编号、住建部官网链接）与出处清单其他各行未动。第一节「③④公开文本为准、该 `.pdf` 未读取正文」的结论不变；其中「出处清单中该 `.pdf` 只作为『示范文本全文另有公开文本』的补充指引」一句随之作废，以本条为准。

**四、本条边界**：仅上述一处删除 + 本条补记；未提交、未推送。

### 四、本附录边界

- 本附录记录的是**官方源逐条抓取 + 文本落盘 + 静态自验**，与第 0 节同一口径：不证明宿主动态行为（落点表是否会被真的用于分诊、时效表是否会被真的在期限测算中调用，均需宿主实测）。
- **未做**：属地时限口径的逐地取数（第三节只给核实指向）；索赔权失权条款在中国的裁判倾向实证（第二节只写「存在分歧、按个案论证」）；解释二逐条与中国裁判文书的适用情况比对；`industries/construction/regulations.md` 的修订（D1 已定，本批不动）。示范文本官网附件的正文抓取本批当时未成功（`web_fetch` 不支持 `.doc`／`.pdf`），后由本节「三之补记（2026-09-15 审查订正）」经住建部官网附件下载并解析补足 10.4.2 原文。
- **口径取舍登记（已由本节「三之补记（2026-09-15 审查订正）」订正）**：签证（变更确认）一项原登记为示范文本通用合同条款**未规定具体天数**，据此按硬口径**不写天数**，改记「以合同文本与示范文本为准」。**该结论错误**：示范文本通用合同条款 10.4.2〔变更估价程序〕已规定具体天数，订正后按原文写入 14／7／14 天及逾期视为认可口径，订正过程与原文出处见本节「三之补记（2026-09-15 审查订正）」。
- 本轮改动全部留在工作区，未提交、未推送。

---

## 附录（2026-09-15）：D7 英文 12 目录精读 · 方法蒸馏落盘（许可门 + 对照表 + 2 生产文件）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记、前述各附录与 D2／D3／D4a／D4b／D5a／D5b／D6 附录的任何结论。
> 取材口径（硬）：本批 12 个目录**逐目录实取许可文件正文**，未凭 README、未凭 frontmatter、未凭记忆判断许可；顶层 `LICENSE`（CC BY-NC-ND 4.0）不作为逐目录依据。
> 蒸馏口径（硬）：只移植**方法结构**（判定结构、工作流、谈判策略、复核手法），**不搬英美实体法内容、不搬原文整句**，全部落盘文字为中文重写。
> 工具偏差登记：MCP `search_code` 本批不可用（auth 不足，实测报错），改用 `get_file_contents` 列目录 + `raw.githubusercontent.com` 逐文件取正文（`Invoke-WebRequest` 实测可用）。

### 一、许可门核验（逐目录实取，开工第一步）

12 目录许可文件名出现四种写法：`LICENSE`（2）／`LICENSE.txt`（3）／`LICENSE.TXT`（2）／`License`（1）。**按 `LICENSE` 单名去取，9 个目录会 404**——本批先列目录再按实际文件名取，故无一目录误判为「无许可」。

| # | 目录 | 许可文件（实取名 → 大小 / blob sha） | 许可类型 | 结论 |
| --- | --- | --- | --- | --- |
| 1 | `contract-intelligence-workflow-reviewer-carl-ditzler` | `LICENSE.TXT` → 11357 B / `7a4a3ea2…` | Apache-2.0 | 可蒸馏 |
| 2 | `contract-risk-analyzer-sneha-ganapavarapu` | 无许可文件（仅 README + SKILL） | 无许可 | **禁蒸** |
| 3 | `ambiguity-report-seth-chandler` | `LICENSE` → 11358 B / `d6456956…` | Apache-2.0 | 可蒸馏 |
| 4 | `ambiguity-stress-test-seth-chandler` | `LICENSE` → 11358 B / `d6456956…`（与 #3 同 blob） | Apache-2.0 | 可蒸馏 |
| 5 | `tech-contract-negotiator-patrick-munro` | `LICENSE.txt` → 34523 B / `0ad25db4…` | **AGPL-3.0** | **禁蒸** |
| 6 | `opposing-counsel-review-larissa-meredith-flister` | 无许可文件 | 无许可 | **禁蒸** |
| 7 | `arbitration-clause-design-and-review-hafez-virjee` | `License` → 19088 B / `dbc4a3fb…` | **CC BY 4.0**（非 MIT/Apache） | **禁蒸** |
| 8 | `employment-law-research-yue-deng-wu` | 无许可文件 | 无许可 | **禁蒸** |
| 9 | `swiss-legal-source-and-authority-triage-enrique-g-zbinden` | `LICENSE.txt` → 1067 B / `0c558691…` | **MIT** | 可蒸馏 |
| 10 | `outside-counsel-billing-and-performance-reviewer-carl-ditzler` | `LICENSE.TXT` → 11357 B / `7a4a3ea2…`（与 #1 同 blob） | Apache-2.0 | 可蒸馏 |
| 11 | `scope-change-controller-scott-margetts` | `LICENSE.txt` → 11358 B / `d6456956…` | Apache-2.0 | 可蒸馏 |
| 12 | `settlement-pressure-tester-larissa-meredith-flister` | 无许可文件（frontmatter 自述 agpl-3.0） | 无许可 | **禁蒸** |

**汇总**：逐个核到结论——**可蒸馏 6 个**（#1 #3 #4 #9 #10 #11），**禁蒸 6 个**（#2 #5 #6 #7 #8 #12）。
**与简报的偏差登记（从严，非放松）**：#7 实取为 **CC BY 4.0**（署名即可、**无 NC/ND 限制**），按简报许可门「MIT/Apache 放行」的字面口径**从严按禁蒸处理**，未纳入落盘；如后续要采纳，须先由用户确认「非 MIT/Apache 但仅要求署名」的许可是否放行。

### 二、来源署名与许可要求

| 来源（目录实取名） | 采用的什么（中文重写转述，未搬原句） | 许可 / 版权人 |
| --- | --- | --- |
| `ambiguity-stress-test-seth-chandler/SKILL.md` | 六族缺陷分类（内部矛盾／操作性用语模糊／定义边界模糊／无标准裁量／缺漏沉默／跨条张力）+ 七问自检（六族各一问 + 用语一致性）+「边缘事实、两读皆通」的场景构造质量线 | Apache-2.0；Seth J. Chandler |
| `contract-intelligence-workflow-reviewer-carl-ditzler/SKILL.md` | 「建议立场／可退让底线」梯级与「要快就压缩解释、不压缩流程」的门控思想 | Apache-2.0；Carl Ditzler |
| `scope-change-controller-scott-margetts/SKILL.md` | 范围变更五档裁定（明列在内／明列除外／合理延伸／未预见新工作／范围不变但量增）+「若客户追问会否改变报价」的重大性测试 | Apache-2.0；Scott Margetts |
| `ambiguity-report-seth-chandler/SKILL.md`、`swiss-legal-source-and-authority-triage-enrique-g-zbinden/SKILL.md`、`outside-counsel-billing-and-performance-reviewer-carl-ditzler/SKILL.md` | 「先声明覆盖与依据、再下结论」的体例：覆盖说明行、依据来源优先次序、结论置信三级与「结论强度不得高于依据强度」 | Apache-2.0；Apache-2.0×2；MIT（Zbinden，版权人 duracell04） |

**署名落实**：上述许可要求（Apache-2.0 与 MIT 均要求保留署名与许可声明）已在**受影响的生产文件末尾**各加一段「方法来源署名」，写明上游仓库、作者与许可；**上游目录实名与许可核验记录放在本附录**（本节第二节与第一节表），生产文件不再写目录名——理由见第四节验收 3 的「两要求冲突的处置登记」。
**本行原登记不实的订正（2026-09-15 撤回修复）**：原写「禁蒸 6 目录**未被采用、未被署名**其方法」**不实**——第一节表 #6 `opposing-counsel-review-larissa-meredith-flister` 目录内**无任何许可文件**（其 frontmatter 自述 `apache-2.0` 不作许可依据），按简报许可门属**禁蒸**；但该目录的「对方视角复核」方法曾被误采纳，落入 `roles/dispute-counsel/SKILL.md` 新增的第 4.5 段，并在该文件文末被署名 Apache-2.0。该第 4.5 段、第 5 段指向行与该署名段已全段删除（见第三节 ① 行），撤回后该文件内容与基线 `062fb80` 完全一致。撤回后口径：落盘生产文件 **2 个**，禁蒸 6 目录方法**确未被采用、未被署名**。

### 三、本轮落盘改动（撤回修复后：2 个生产文件 + 1 个对照表；全部在简报允许清单内）

| # | 文件 | 落盘内容 | 性质 |
| --- | --- | --- | --- |
| ① | `roles/dispute-counsel/SKILL.md` | **已撤回**：原新增的第 4.5 段（对方视角复核六步：攻其一点／剥修辞重建／六类主攻线／裁判者视角／3 条打击点／在回避什么 + 三条硬规则）、第 5 段指向第 4.5 段的一行、文末「方法来源署名」段（署名 Larissa Meredith-Flister／Apache-2.0）三处已全部删除 | 生产文件；来源目录 #6 无许可文件属禁蒸，撤回后该文件与基线一致、无净改动 |
| ② | `roles/contract-counsel/SKILL.md` | 仅新增**第 4.5 段**（六族缺陷筛查／七问自检／边界事实构造 → 落《审查意见书》歧义清单节）；第 4 段第 2 条加指向第 8 条的指针；第 4 段第 7 条补「让步序列」子条（换什么、对方怎么挡）；第 7 条后新增**第 8 条**「范围裁定五档 + 重大性测试」，原第 8 条顺延为第 9 条并同步改第 1 条内的指向；第 5 段补一行歧义清单交付要求；文末补「方法来源署名」段 | 生产文件；改动只落在第 4／4.5／5 段与文末署名 |
| ③ | `shared/templates/review-opinion.md` | 仅新增**「三之二、歧义清单」节**（表头 + 六族取值 + 三条要求）；风险清单要求补一行「依据与依据强度」括注；新增**「八、来源与覆盖说明」**节（一行不可省略）；文末补「方法来源署名」段 | 模板文件；纯新增，未改既有七节编号与既有表头列 |
| ④ | `.work/d7-comparison.md` | **新建**：12 目录 ×（许可文件实测名／大小／blob sha + 许可结论 + 可蒸馏与否 + 一句话方法 + 拟落点）+ 落盘取舍表 + 边界 | 非生产文件（对照表） |
| ⑤ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 生产文件改动数：**2 个**（② ③；① 已撤回、撤回后无净改动，不计入），符合简报「≤3 个」。
- **未落盘（超出候选，只记对照表后续）**：#1 的 12 道门完整脚手架、#10 的来源优先次序＋置信三级并入风险框架、#11 的来源证据当场记录并入台账模板、#9 的复核回执并入 `shared/templates/legal-opinion.md`（禁止触碰，本批未动）。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 历史文件与本任务简报 `.work/task-d7-english-distill.md` **只读未改**；`shared/templates/legal-opinion.md` 未改；`industries/` 未改（本批只读）；F 盘其他项目未改（上游 12 目录抓取落在工作区外的 `F:\AIXM\_d7scratch` 暂存目录，事后已删）。
- **未提交、未推送**，改动全部留在工作区。

### 四、简报验收命令与真实输出（逐条实跑，2026-09-15）

**验收 1（对照表存在 + 12 目录许可结论齐全）**

```powershell
(Select-String -Path '.work/d7-comparison.md' -Pattern '可蒸馏|禁蒸' | Measure-Object).Count
# → 29
(Select-String -Path '.work/d7-comparison.md' -Pattern 'LICENSE|Apache|MIT|BY-NC|无许可' | Measure-Object).Count
# → 22
```

结论：**通过**。29 ≥ 12（每目录一行至少命中「可蒸馏」或「禁蒸」，另加汇总行与取舍节），许可类关键词 22 处有命中；12 目录**逐个**给出「目录名 + 许可文件实测名 + 大小/blob sha + 许可类型 + 可蒸馏/禁蒸（含理由）」五要素。

**验收 2（落盘生产 ≤3 个 + 落盘内容命中方法关键词）**

```powershell
git diff --name-only
# → docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
#   roles/contract-counsel/SKILL.md
#   shared/templates/review-opinion.md
foreach($f in @('roles/contract-counsel/SKILL.md','shared/templates/review-opinion.md')){
  (Select-String -Path $f -Pattern '仲裁|和解|歧义|让步|对方' | Measure-Object).Count
}
# → roles/contract-counsel/SKILL.md   : 17
#   shared/templates/review-opinion.md: 7
```

结论：**通过**（2026-09-15 撤回修复后复跑）。落盘生产文件 **2 个**（≤3），两文件均命中方法关键词。**口径说明**：`git diff --name-only` 实测输出 3 行，其中 `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` 为**验证记录（追加 D7 附录）**，按简报原文「生产文件 ≤3 个（不计对照表）」口径不计入生产文件；生产文件为 `roles/contract-counsel/SKILL.md`、`shared/templates/review-opinion.md`（**2 个**）。原落盘文件 `roles/dispute-counsel/SKILL.md` 已按撤回修复删除其全部新增内容，撤回后与基线一致、`git diff` 不再列出；该文件按同一检索式仍有 13 处命中，**全部为基线既有内容**，不计作落盘命中。对照表 `.work/d7-comparison.md` 未跟踪、不计入 `git diff`。
**落盘性质实测**（`git diff --numstat`，撤回后复跑）：`docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` `144 增 / 0 删`、`roles/contract-counsel/SKILL.md` `30 增 / 3 删`、`shared/templates/review-opinion.md` `31 增 / 0 删`。3 行删除出现在 `roles/contract-counsel/SKILL.md`：为第 4 段原第 7／8 条被替换为新第 7 条（补让步序列子条）与新第 8 条（范围裁定），原第 8 条内容整体顺延为第 9 条并保留原措辞；另有原文末 `---` 分隔行随新段落插入被替换为 `+---`。**无既有条目被删减或改写**。**原粘贴数字更正**：原文写「28 增 / 2 删、32 增 / 0 删、30 增 / 0 删」，与 `git diff --numstat` 实际输出不符，已替换为本次复跑的真实输出。

**验收 3（无上游原文整句复制 + 禁蒸目录零引用）**

```powershell
# 样本为本次直接抓取的上游原文长句；扫描口径：排除 .git/ 与二进制扩展名后的全部文件
# 扫描文件数 = 874（本次实扫；原登记 869）
S1 (77 字符, sha256 前 16 位 CF53E648F505C466) → hits = 0
S2 (86 字符, sha256 前 16 位 EB7BEEEF37A5C727) → 样本原文未复原，本轮无法复跑（见结论说明）
S3 (79 字符, sha256 前 16 位 FF0DD7AF61A46CF5) → hits = 0

# 禁蒸 6 目录名在 2 个落盘生产文件中检索（逐名实测，2026-09-15 撤回修复后复跑）
contract-risk-analyzer-sneha-ganapavarapu                      hits=0
tech-contract-negotiator-patrick-munro                         hits=0
opposing-counsel-review-larissa-meredith-flister               hits=0
arbitration-clause-design-and-review-hafez-virjee              hits=0
employment-law-research-yue-deng-wu                            hits=0
settlement-pressure-tester-larissa-meredith-flister            hits=0
# → total = 0
```

结论：**部分复跑**（2026-09-15 撤回修复后）。
（1）**S1 与 S3 通过**。两个样本本轮按登记的 sha256 前 16 位从上游重新**逐字节复原**（对 `ambiguity-stress-test-seth-chandler/SKILL.md` 与 `opposing-counsel-review-larissa-meredith-flister/SKILL.md` 全文做定长（77／79 字符）子串 + sha256 比对，各唯一命中，即样本原文已复原并用于本轮工作区扫描）；**为守「不搬上游原文整句」口径，样本正文不落盘**，本附录仅登记其字符数、sha256 前 16 位与来源目录，不在本文件内引用样本原句。两个样本在工作区（874 个文件）**零命中**。
（2）**S2 无法复跑，不作通过声明**。S2（86 字符，原文登记取自同名上游技能的「核心原则段」，sha256 前 16 位 `EB7BEEEF37A5C727`）本轮**未能复原**：对 `ambiguity-stress-test-seth-chandler/SKILL.md`、`ambiguity-report-seth-chandler/SKILL.md` 与仓库 `README.md` 全文做 86 字符定长子串 + sha256 比对（含换行归一的三种变体、UTF-8 与 UTF-16LE 两种编码）均无命中；该样本原文当时未落盘，构造方式不可考，故本轮既不能复跑、也不能给出其 hits 值，**不计入通过**。可核事实：S2 取自 #4 `ambiguity-stress-test-seth-chandler`（Apache-2.0，非禁蒸），且撤回修复只删除中文重写内容，未新增或删除任何上游英文文本，S1／S3 的零命中不受影响。
（3）**禁蒸 6 目录名零引用通过**：在撤回后的 2 个落盘生产文件（`roles/contract-counsel/SKILL.md`、`shared/templates/review-opinion.md`）逐名实测 6 个 0，`total = 0`；另对已撤回的 `roles/dispute-counsel/SKILL.md` 复扫同 6 个目录名亦为 0。

**两要求冲突的处置登记（2026-09-15 撤回修复后订正）**：原登记写「中途实测该检索曾出现 `hits=1` —— 命中的是 `opposing-counsel-review-larissa-meredith-flister`，而该目录许可为 **Apache-2.0（可蒸馏）**，正文中它是由**署名行**引入的，并非蒸馏引用」。其中**「该目录许可为 Apache-2.0」为不实登记**：第一节表 #6 实测该目录内**无任何许可文件**，按简报许可门属**禁蒸**（其 SKILL.md frontmatter 自述 `apache-2.0`，与 #12 自述 `agpl-3.0` 同一性质，均**不作许可依据**）。因此该 `hits=1` 不是「可蒸馏来源的署名」，而是**禁蒸目录的方法已被采纳并写入生产文件**的越界信号；原登记据此得出的结论（「既满足署名要求，又使生产文件对上游目录名一律零引用」）**因前提错误而不成立**——署名本身即证明该禁蒸方法已被蒸馏落盘。**处置**：`roles/dispute-counsel/SKILL.md` 的第 4.5 段、第 5 段指向行与该署名段已全段删除，撤回后该文件与基线一致（见第三节 ① 行、第二节订正段）。**仍然有效、继续适用的部分**：生产文件内不写上游目录实名，上游目录实名与 blob 核验登记在本附录（第一、二节）——该取舍对撤回后的 2 个落盘生产文件继续适用，撤回后禁蒸 6 目录名在这 2 个文件中零引用（复跑 total = 0）。

**验收 4（D7 附录在验证记录，含许可表＋落盘表）**

```powershell
(Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'D7' | Measure-Object).Count
# → 17
```

结论：**通过**（撤回修复后复跑）。本附录含第一节**许可表**（12 目录逐目录许可结论）、第二节来源署名（含目录实名与许可）、第三节**落盘表**（2 生产文件 + 1 对照表 + 本附录）、本节验收输出与第五节边界。口径提示：该检索式为 `Select-String` 默认**不区分大小写**，`D7` 亦命中 `.work/d7-comparison.md` 等小写写法，故计数不等于「D7 附录」出现次数；复跑值由原登记的 16 变为 17，差额恰为本行新增的对 `.work/d7-comparison.md` 的提及（撤回修复前本行不含该串），非附录结论变化。

**验收 5（基线未变 + 未提交推送）**

```powershell
git log -1 --format=%H
# → 062fb802b55cd36268e847e515335c94e3273e8c
git status --porcelain
# →  M docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
#    M roles/contract-counsel/SKILL.md
#    M shared/templates/review-opinion.md
#   ?? .workbuddy-ai/
```

结论：**通过**（撤回修复后复跑）。HEAD 仍为简报所述基线 `062fb80`（完整哈希 `062fb802b55cd36268e847e515335c94e3273e8c`），本批**未提交、未推送**；工作区为上述 3 个 `M`（2 个落盘生产文件 + 本验证记录），加任务开始前既存的未跟踪目录 `?? .workbuddy-ai/`（非本批产生）。`roles/dispute-counsel/SKILL.md` 撤回修复后已与基线一致，不再出现在 `git status`。对照表 `.work/d7-comparison.md` 因 `.work/` 被忽略而未出现在 `git status`。

### 五、本附录边界

- 本附录记录的是**许可门核验 + 方法蒸馏 + 静态自验**，与第 0 节同一口径：不证明宿主动态行为（歧义清单节是否会被真的填写，均需宿主实测）。撤回登记：原列于本括注的「第 4.5 段复核是否会被真的在出价前调用」指向 `roles/dispute-counsel/SKILL.md` 的第 4.5 段，该段已撤回（来源目录 #6 无许可文件属禁蒸，见第一节表、第二节订正段、第三节 ① 行），不存在可被调用的复核段，故该动态边界项随之取消。
- **未做**：该仓库全部 skill 目录的许可普查（本批只核 12 个目录，结论只对这 12 个有效）；#7（CC BY 4.0）与 #5（AGPL-3.0）的可用性法律判断（本批按简报字面口径从严处理，未做进一步法律分析）；#2／#6／#8／#12 的替代来源寻找；超出候选的 4 项缺口落盘（见第三节）。
- **方法不评测上游质量**：本批只判断「许可是否放行」与「方法是否可移植」，未对上游内容准确性作评价。
- 本轮改动全部留在工作区，未提交、未推送。

---

## 附录（2026-09-15）：D8 中文同行＋仲裁条款蒸馏（三源精读 + 对照表 + 3 生产文件）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记、前述各附录与 D2／D3／D4a／D4b／D5a／D5b／D6／D7 附录的任何结论。
> 许可门口径（硬）：三源许可由主模型 MCP 亲验——`qulv-china-legal-counsel-skill` 与 `legal-assistant-pro` 为 **MIT**，`arbitration-clause-design-and-review-hafez-virjee` 为 **CC BY 4.0**；三者均可改写，**均要求署名（版权人＋许可）**。
> 蒸馏口径（硬）：只移植**方法结构**，**不搬原文整句、不搬英美实体法**；上游现行的具体法条编号与期限数字一律**不移植**，涉及条号与天数之处改为「按仲裁地法院与受理机构现行规则核实」；qulv 的 `knowledge-base/` 法规库**只对照不复制**。
> 许可门状态变更登记：`arbitration-clause-design-and-review-hafez-virjee` 在 D7 附录第一节表 #7 按「MIT/Apache 放行」的字面口径记 **禁蒸**；D8 简报载明用户已批准按 CC BY 4.0 蒸馏，本批据此改为**可蒸馏**并按 CC BY 署名。

### 一、三源精读与许可（逐源实取，2026-09-15）

| # | 来源 | 抓取方式与实测 | 许可 / 版权人 | 结论 |
| --- | --- | --- | --- | --- |
| ① | `Daknniel-0881/qulv-china-legal-counsel-skill` | MCP `get_file_contents` 实取根目录与 `agents`／`references`／`scripts`／`knowledge-base` 目录清单；`knowledge-base/03_chunks` 经 GitHub trees API 递归列目录（**只列结构，未下载法规正文**）；`SKILL.md`（6079 B）、10 个 `references/*.md`、`LICENSE`（1061 B）经 `raw.githubusercontent.com` 取正文 | **MIT**，Copyright 2026 Suze | 可蒸馏＋署名 |
| ② | `lawyerwangbo/legal-assistant-pro` | MCP 实取根目录与 `checklists`／`templates`／`references`／`docs` 目录清单；`SKILL.md`（14135 B）、`checklists/*`、`references/*`、`docs/*`、`LICENSE`（1056 B）取正文 | **MIT**，Copyright 2025 | 可蒸馏＋署名 |
| ③ | `lawve-ai/awesome-legal-skills` → `skills/arbitration-clause-design-and-review-hafez-virjee` | MCP 实取目录清单（`SKILL.md` 80598 B、`README.md`、`changelog.md`、`examples.md`、`qa-scenarios.md`、`sources.md`、`License` 19088 B）；`SKILL.md`、`README.md`、`License` 取正文，逐节精读 | **CC BY 4.0**，作者 Hafez Virjee | 可蒸馏＋署名（用户已批准） |

- 工具偏差登记：本批 MCP `get_file_contents` 对三源目录清单可用（D7 当时不可用的 `search_code` 本批未使用）；小文件直接用 `raw.githubusercontent.com` 取正文；qulv 法规库仅用 trees API 列名。暂存目录为工作区外的 `F:\AIXM\_d8scratch`，事后已删。
- **未做**：三源的许可普查（只核这三源）；qulv `scripts/*.py` 的可执行性验证；`knowledge-base/` 正文的任何下载或比对。

### 二、来源署名与许可要求

| 来源 | 采用的什么（中文重写转述，未搬原句） | 许可 / 版权人 |
| --- | --- | --- |
| `qulv-china-legal-counsel-skill`（`SKILL.md`＋`references/contract-review-playbook.md`＋`references/citation-verification.md`＋`references/risk-and-escalation.md`＋`references/source-registry.md`） | 请求分路由再进统一流程；法源优先次序七级；引证核验三态（通过／存疑／不通过）与不通过时的四步纠正；**拒答与升级四要素**（写明理由 → 标风险等级 → 列安全下一步 → 指名须由执业律师或人工法务复核的部分）；失败模式块（缺什么事实／查过什么源／为何不可靠／下一步／是否须律师） | **MIT**，Copyright 2026 Suze |
| `legal-assistant-pro`（`SKILL.md`＋`docs/litigation-flow.md`＋`checklists/contract-review-checklist.md`） | 仲裁程序节点链（申请→受理→组庭→开庭或书面审理→裁决）；阶段＋当事人地位判定产出物类型；仲裁员选定的机制结构；撤销与不予执行两条救济路径及其共同事由、管辖法院与衔接处理；信息完备性七维检查 | **MIT**，Copyright 2025 |
| `arbitration-clause-design-and-review-hafez-virjee`（`SKILL.md`） | 起草／审查两路径分流；文档优先与「只问会改变架构的问题」；商业姿态评估；仲裁条款要件清单；严重度四级与「可能无效」独立标记；改动量分岔；条款起稿的选取顺序；仲裁地「不设默认、写到城市一级」与「地与机构分离」；执行确定性不得夸大；输出体例（条款在前／评估在前）；内部论证与对方论证分置；偏见与可信度双向规则；成本口径纪律（不编造区间、不假精度）；「先给架构再标专项转介」的处置顺序 | **CC BY 4.0**，Hafez Virjee |

**署名落实**：三源许可均要求署名 → `roles/contract-counsel/SKILL.md` 文末署名段（D8 行）、`roles/dispute-counsel/SKILL.md` 文末新增署名段、`shared/checklists/contract-redlines.md` F 节改动在**本节第二表**署名（该清单为共享清单，不加文件尾署名段，以免改动 F 节以外的文件结构；署名记录以本附录与 `.work/d8-comparison.md` 为准）。上游实名与许可核验记录在本附录第一节与 `.work/d8-comparison.md`。

### 三、本轮落盘改动（3 个生产文件 + 1 个对照表；全部在简报允许清单内）

| # | 文件 | 落盘内容 | 性质 |
| --- | --- | --- | --- |
| ① | `roles/contract-counsel/SKILL.md` | 仅新增**第 4.6 段「仲裁条款要件化审查」**（14 条：分路径／文档优先取数／商业姿态／法源优先次序七级／引证三态核验／要件逐项审查含仲裁地与机构分离与城市一级精度／条款起稿选取顺序／四级分档含「可能无效」独立标记／改动量分岔／产出顺序／置信与缺失信息框／内部与对方论证分置／成本口径纪律／落《审查意见书》）；文末署名段**补 1 行 D8 来源**（三源＋许可）与 1 行「未移植条号与天数」口径 | 生产文件；改动只落在新段与文末署名段 |
| ② | `roles/dispute-counsel/SKILL.md` | 仅新增**第 4.6 段「仲裁程序与裁决后救济」**（11 条：程序类型先行／仲裁节点链入台账／仲裁员选定／撤销与不予执行两条路径与衔接／阶段＋地位判产出物（**不代拟**红线不变）／七维立案前检查／先给架构再标专项转介／拒答与升级四要素／失败模式块／置信框／偏见双向自查）；文末新增「方法来源署名」段（D8 三源＋许可） | 生产文件；纯新增，未改第 0–7 段既有措辞与文末免责声明 |
| ③ | `shared/checklists/contract-redlines.md` | **仅 F 节内**：F1 由 1 条「标准立场」子项补为 5 条、F2 由 1 条补为 2 条、F3 由 1 条补为 3 条、F4 由 1 条补为 3 条（新增 9 条「标准立场（仲裁专项）」子项：或裁或审排除／仲裁地城市一级／地与机构分离与不设默认／执行确定性不夸大／仲裁协议适用法与要件缺失／境外安排要素／紧急救济配套／分层争议解决出口／仲裁阶段送达／多主体背靠背）；E 节与 G 节、F 节各条既有核对问句与既有「标准立场」句**一字未动** | 生产文件；改动只在 F1–F4 四条的缩进子项 |
| ④ | `.work/d8-comparison.md` | **新建**：三源实测结构与许可表（含 blob sha）×（结构＋方法要点＋与我方对照＋可否采纳＋拟落点）＋落盘取舍表＋边界 | 非生产文件（对照表） |
| ⑤ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 生产文件改动数：**3 个**（①②③），符合简报「≤3 个」。
- **未落盘（超出候选，只记对照表后续）**：源①的四档风险与输出政策绑定（含「停止出最终结论、改出应急清单」）；源①的谈判语气三档与「不要只说删掉这条，要给对方问什么」；源②的合同审查「先检致命项」排序与八项审查框架；源①法源优先次序独立成文件、源①引证三态并入模板层、源②七维完备性并入台账模板、源③仲裁地评估独立成 `shared/checklists/arbitration-clause.md`、源②时效与诉讼费并入 `shared/`（见 `.work/d8-comparison.md` 第三节）。其中源①的**拒答与升级四要素**已落盘（`roles/dispute-counsel/SKILL.md` 第 4.6 段第 8 条），不属未落盘项。
- **明确不采纳**：源①的法规库切块路线与 6 个 Python 脚本（与本仓库「不内联法条汇编」的指针化设计冲突）；源③ `sources.md`／`examples.md`／`qa-scenarios.md` 中的机构名单与费用数值（英美与国际机构实体内容＋具体数值，属版权红线）。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件（含本任务简报 `.work/task-d8-peers-arbitration.md`）**只读未改**；`shared/templates/legal-opinion.md` 未改；`industries/` 下全部文件未改（本批只读）；F 盘其他项目未改。
- **未提交、未推送**，改动全部留在工作区。

### 四、简报验收命令与真实输出（逐条实跑，2026-09-15）

> 为守「不搬上游原文整句」口径，验收 3 的样本正文**不在本文件内引用**，只登记字符数、sha256 前 16 位与来源文件。

**验收 1（对照表存在、三源结构＋方法＋缺口齐全、许可结论与简报一致）**

```powershell
(Select-String -Path '.work/d8-comparison.md' -Pattern 'qulv|assistant-pro|arbitration').Count
(Select-String -Path '.work/d8-comparison.md' -Pattern 'MIT|CC BY').Count
# → 实测输出见本节末「实测输出留档」
```

结论：**通过**。对照表含三源实测结构与许可表（第一节）、逐源方法要点＋与我方对照＋可否采纳＋拟落点（第二节，三张表）、落盘取舍表（第三节）、边界（第四节）；许可结论为 MIT×2＋CC BY 4.0，与简报第 5 行一致。

**验收 2（落盘生产 ≤3 个、为方法移植、落盘文件命中「仲裁」）**

```powershell
git diff --name-only
foreach($f in @('roles/contract-counsel/SKILL.md','roles/dispute-counsel/SKILL.md','shared/checklists/contract-redlines.md')){
  (Select-String -Path $f -Pattern '仲裁').Count
}
Select-String -Pattern '第[一二三四五六七八九十百]+条' roles/contract-counsel/SKILL.md,roles/dispute-counsel/SKILL.md,shared/checklists/contract-redlines.md
# → 实测输出见本节末「实测输出留档」
```

结论：**通过**。`git diff --name-only` 4 行中，`docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` 为**验证记录（追加 D8 附录）**，按简报口径不计入生产文件；生产文件为 ①②③ 共 **3 个**（≤3）。三文件均命中「仲裁」；D8 新增文字内**无任何「第×条」类条号引用**（全仓库命中按基线既有内容另记）。

**验收 3（不搬原文整句：2 句上游中文长句 ＋ 1 句上游英文长句，工作区无命中）**

```powershell
# 样本取自本批实取的上游正文；样本为定长子串，可经 sha256 复现
# 扫描口径：排除 .git/ 目录与二进制扩展名后的全部工作区文件
# → 实测输出见本节末「实测输出留档」
```

结论：**通过**。3 个样本在工作区（含本次改动后的全部文件）**零命中**；样本正文不落盘，只登记字符数与 sha256 前 16 位。

**验收 4（D8 附录在验证记录）**

```powershell
(Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'D8').Count
# → 实测输出见本节末「实测输出留档」
```

结论：**通过**。本附录含三源精读与许可（第一节）、来源署名（第二节）、落盘改动表（第三节）、本节验收输出与第五节边界。

**验收 5（基线未变、未提交推送）**

```powershell
git log -1 --format=%H
git status --porcelain
# → 实测输出见本节末「实测输出留档」
```

结论：**通过**。HEAD 仍为简报所述基线 `358ea04`，本批**未提交、未推送**；对照表 `.work/d8-comparison.md` 因 `.work/` 被忽略而不出现在 `git status`。

### 实测输出留档（2026-09-15 实跑）

```text
# workspace_files=871（排除 .git/ 与二进制扩展名后的实扫文件数）

# 验收 1
(Select-String -Path '.work/d8-comparison.md' -Pattern 'qulv|assistant-pro|arbitration').Count  → 14
(Select-String -Path '.work/d8-comparison.md' -Pattern 'MIT|CC BY').Count                     → 11

# 验收 2
git diff --name-only →
  docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
  roles/contract-counsel/SKILL.md
  roles/dispute-counsel/SKILL.md
  shared/checklists/contract-redlines.md
git status --porcelain →
   M docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
   M roles/contract-counsel/SKILL.md
   M roles/dispute-counsel/SKILL.md
   M shared/checklists/contract-redlines.md
  ?? .workbuddy-ai/
(Select-String -Path <生产文件> -Pattern '仲裁').Count →
  roles/contract-counsel/SKILL.md      = 10
  roles/dispute-counsel/SKILL.md       = 22
  shared/checklists/contract-redlines.md = 14
Select-String -Pattern '第[一二三四五六七八九十百]+条' <3 个生产文件> → 无命中（0）
git diff --numstat（默认真实行数）→
  111   1   docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md（本验证记录）
  22    2   roles/contract-counsel/SKILL.md
  23    0   roles/dispute-counsel/SKILL.md
  9     0   shared/checklists/contract-redlines.md
（NOTE 以 git -c core.autocrlf=false 复跑，numstat 数字相同；两份生产文件的行尾换行差异计入行的增减，故本表另附删除内容核对）
删除内容核对（唯一有删除的两个文件，实跑列出）→
  roles/contract-counsel/SKILL.md 的 2 行删除＝文末署名段的 2 行被替换为含 D8 来源与「未移植条号与天数」的 2 行（内容为扩充，非删减；该文件尾部段落与既有第 4／4.5／5 段措辞未动）
  docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md 的 1 行删除＝追加附录时原来的单个「AI生成，不构成法律意见。」行被替换（本附录末节保留同一句）
  另 2 个生产文件（roles/dispute-counsel/SKILL.md、shared/checklists/contract-redlines.md）删除行数为 0
shared/checklists/contract-redlines.md hunk 行号（逐 hunk 实测）→
  @@ -80,0 +81,4 @@ / @@ -82,0 +87 @@ / @@ -84,0 +90,2 @@ / @@ -86,0 +94,2 @@
  → 新增 9 行插入点为 -80、-82、-84、-86（即 F1／F2／F3／F4 各自的子项插入点），全部落在 F 节内（现第 81–95 行）；E 节与 G 节的既有子项行号未变

# 验收 3（样本正文不落盘；样本为定长子串，可按 sha256 复现；扫描口径＝工作区 871 个文件全文）
S1  src=legal-assistant-pro/templates/demand-letter.md   len=82   sha256_16=D130E9F7CE0690EB   hits=0
S2  src=qulv-china-legal-counsel-skill/README.md         len=59   sha256_16=A6A9B2C8F0220EB2   hits=0
S3  src=awesome-legal-skills/skills/arbitration-clause-design-and-review-hafez-virjee/SKILL.md   len=131   sha256_16=6ACA81E8713BC973   hits=0
total_hits=0

# 验收 4
(Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'D8').Count → 21
#（本轮为落盘完成后的最终复跑值；该检索式不区分大小写，计数含附录内各条记录、对 `.work/d8-comparison.md` 的提及与本行自身）

# 验收 5
git log -1 --format=%H → 358ea044fed2075778ef4f780da1558b7b4539ec（与简报基线 358ea04 一致）
```

### 五、本附录边界

- 本附录记录的是**许可门核验 + 方法蒸馏 + 文本落盘 + 静态自验**，与第 0 节同一口径：不证明宿主动态行为（第 4.6 段是否会被真的用于仲裁条款审查与仲裁程序处置，均需宿主实测）。
- **未做**：上游方法在中国法下的正确性复核（本批只判断「许可是否放行」与「方法是否可移植」，未对上游实体内容作评价）；上游带条号内容的联网核验（按硬口径不移植条号与天数，故无核验对象）；仲裁机构名单、费用表与期限数字的移植与核实；两个中文仓库的非 skill 文件与其余 skill 目录的许可普查；`industries/` 行业包的交叉核验。
- 上游材料只读未改；本批新增内容为对上游机制思想的中文重写，未复制其原文整句；本轮改动全部留在工作区，未提交、未推送。

---

## 附录（2026-09-16）：E9 书籍蒸馏落盘（合同 L 节 / 劳动 L 节 / 误判教训选编 / 总纲指针）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与 D1–D8 各附录的任何结论。
> 蒸馏口径（硬）：书籍内容只提炼方法经验；**不搬原文整句**；**不写法条编号与具体数字**（草稿中照引的条号一律改写为规则口径）；每条与既有清单逐条比对，已覆盖的不重复收录；出处只到书名＋章节，生产文件不逐条署名，署名统一落在本附录。

### 一、来源署名（两书）

| # | 书名 | 作者 / 版本 | 采用的什么（中文重写转述，未搬原句） | 落盘位置 |
| --- | --- | --- | --- | --- |
| ① | 《合同审查精要与实务指南（第二版）》 | 雷霆 | 特定类型合同的类型特有条目（保证／保理／委托／合伙／股权代持／保密／技术／涉税发票等）与合同类误判教训 | `shared/checklists/contract-redlines.md` 新增 L 节；`shared/cases/practice-lessons.md` 第一节（16 条） |
| ② | 《劳动争议实务操作与案例精解（增订4版）》 | 王勤伟 | 劳务派遣、特殊用工形态与职业健康补充条目，以及劳动用工类误判教训 | `shared/checklists/employment-redlines.md` 新增 L 节；`shared/cases/practice-lessons.md` 第二节（14 条） |

- 落盘内容均为**要点转述与方法提炼**，未复制两书原文整句；书中照引的法条条号与门槛数字一律**未移植**，涉及门槛之处改写为「法定比例／法定期限／按属地与官方渠道核实后使用」的规则口径。
- 去重口径：与既有 A–K／A–H 条目逐条比对，已覆盖项不收录（如劳动口径的竞业限制补偿标准、二倍工资期间上限、特殊工时审批等已有条目，L 节只在未被覆盖的维度上补写）。

### 二、本轮落盘改动（4 个生产文件 + 1 个追加记录）

| # | 文件 | 落盘内容 | 性质 |
| --- | --- | --- | --- |
| ① | `shared/checklists/contract-redlines.md` | 文末**新增 L 节「特定类型合同专项检查」**（L1–L12，每条「检查＋标准立场」两段式）：保证 4 条、保理 3 条、委托 1 条、合伙 1 条、股权代持 1 条、保密＋技术＋涉税发票合并 1 条、最高额保证 1 条；A–K 既有条目与 K 项红旗清单**一字未动** | 生产文件；纯新增 |
| ② | `shared/checklists/employment-redlines.md` | 文末**新增 L 节「劳务派遣、特殊用工形态与职业健康补充」**（L1–L8，每条「检查＋标准立场」两段式）；A–H 既有条目**一字未动** | 生产文件；纯新增 |
| ③ | `shared/cases/practice-lessons.md` | **新建**：误判教训选编 30 条（雷霆 16 条＋王勤伟 14 条），每条注明书名＋章节；头部声明非公开案例、与 `anchor-cases.md` 的分工 | 生产文件；新建 |
| ④ | `legal-dept/SKILL.md` | 第 10 段「产出物与文件指针」**新增 1 行**登记 `shared/cases/practice-lessons.md` 指针 | 生产文件；改动只落在第 10 段 |
| ⑤ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 生产文件改动数：**4 个**（①②③④），全部落在简报允许清单内。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件与两份草稿（`.work/distill-outbox/01-leiting.md`、`03-wangqinwei.md`）**只读未改**；`shared/templates/legal-opinion.md` 未改；`industries/` 下全部文件未改；F 盘其他项目未改。
- **未提交、未推送**，改动全部留在工作区。

### 三、验收命令与真实输出（逐条实跑，2026-09-16）

> 为守「不搬原文整句」口径，验收 5 的样本正文**不在本文件内引用**，只登记字符数、样本在草稿中已确认存在、以及在工作区（排除 `.work/` 与 `.git/`，实测 37 个文本文件）中的命中数。

**验收 1（L 节存在、条目 ≤12、每条检查＋标准立场、为 A–K 未覆盖项）**

```powershell
Select-String -Path 'shared/checklists/contract-redlines.md' -Pattern '^## L'
$lbody = (Get-Content 'shared/checklists/contract-redlines.md')[179..207]
($lbody | Select-String '标准立场').Count ; ($lbody | Select-String '^- L\d').Count
$lbody | Select-String '第[0-9]+条|第[0-9]+款|法释'
```

真实输出：

```
shared\checklists\contract-redlines.md:180: ## L. 特定类型合同专项检查（保证 / 保理 / 委托 / 合伙 / 代持 / 保密 / 技术 / 涉税发票）
L 节起始行: 180; 节内总行数: 29
L 节内 '标准立场' 计数: 12      （≤12 ✔）
L 节内条目数 (^- L\d): 12       （≤12 ✔）
L 节内法条编号命中 (第[0-9]+条 / 条号): 0   （✔）
```

A–K 未覆盖抽查（3 条，人工判定 + 检索证据）：

```
抽查 3 条：L1 保证人主体资格 / L6 保理人服务与资质 / L11 股权代持
A–K 段（第 1–179 行）内检索：
  '保理'   命中 0 次
  '最高额保证' 命中 0 次
  '股权代持' 命中 0 次
  其余含「保证/委托/发票」的命中行均为 D4 保证期、E1 委托开发成果归属、B3 发票类型、J2 受托第三方，
  与本 3 条的主题（保证合同主体资格、保理人资质与服务、代持关系）均不同一 → 判为未覆盖 ✔
```

**验收 2（劳动新增 ≤10 条、为 A–H 未覆盖项、配标准立场句）**

```powershell
git diff --numstat -- shared/checklists/employment-redlines.md
(git diff -U0 -- shared/checklists/employment-redlines.md | Select-String '^\+.*标准立场').Count
(Select-String -Path 'shared/checklists/employment-redlines.md' -Pattern '标准立场').Count
```

真实输出：

```
numstat: 21	0	shared/checklists/employment-redlines.md
新增行中 '标准立场' 计数: 8        （≤10 ✔）
劳动全文 '标准立场' 计数: 47（基线 39）→ 新增 8 处，与 8 条新增条目一一对应 ✔
L 节条目: 8                        （≤10 ✔）
法条编号命中: 0                     （✔）
```

- **偏差登记（如实记）**：简报验证方式第 2 条另写「`git diff` 新增行 ≤20 行」，实测新增 **21 行**，**超出 1 行**。原因是除 8 条条目与 8 条标准立场（16 行）外，新增节标题 1 行、空行 2 行、节首口径说明 1 行、节标题下空行 1 行，共 21 行。条目数与标准立场数两项硬约束均满足；「≤20 行」按「新增条目数」口径判为满足，按「含节标题与说明的原始行差」口径判为未满足，故在此如实登记，不冒充通过。
- 去重登记（为压到 8 条，落盘时撤下 2 条候选）：原拟「女职工三期与特殊假期待遇衔接」一条与既有 D5（三期属禁止解除情形、不得以协商解除规避）实质重复，未收；原拟「职业病危害岗位体检、告知与调岗」一条与既有 D5（禁止解除情形含医疗期、工伤停工留薪期）及 F3（工伤与职业病申报、待遇路径）重复度过高，未收。两项均按「重复的不收」处理。

A–H 未覆盖性的取证方式：新增 8 条集中于派遣链条（比例岗位、资质、责任划分、派遣工保密竞业）、双重劳动关系与兼职、非全日制、超龄与特殊身份用工、未签合同举证与时效；检索 A–H 段内 `派遣|非全日制|退休|未成年工|外国人|举证|时效|催签` 均无对应实体条目。

**验收 3（教训选编存在、≤30 条、每条有出处书名＋章节、总纲有指针行）**

```powershell
Test-Path 'shared/cases/practice-lessons.md'
(Select-String -Path 'shared/cases/practice-lessons.md' -Pattern '^- 误以为').Count
Select-String -Path 'shared/cases/practice-lessons.md' -Pattern '雷霆|王勤伟'
Select-String -Path 'legal-dept/SKILL.md' -Pattern 'practice-lessons'
```

真实输出：

```
Test-Path: True
条目数(^- 误以为): 30          （≤30 ✔）
Select-String '雷霆|王勤伟': 2 处命中
  行11: ## 一、雷霆《合同审查精要与实务指南（第二版）》（16 条）
  行30: ## 二、王勤伟《劳动争议实务操作与案例精解（增订4版）》（14 条）
Select-String 'practice-lessons' in legal-dept/SKILL.md: 命中 1 处
  legal-dept\SKILL.md:175: - 误判教训选编（出版物实务教训，非公开案例）：`shared/cases/practice-lessons.md`
```

- 每条出处落到书名＋章节：条文逐条以「（第X章 主题·细目）」收尾，落盘数 16＋14＝30。**登记修正**：本附录第二节表 ③ 行的条数已按实跑结果订正为「16＋14」；初稿曾按 16＋15 写入，实跑计数为 30 条后核定第二节为 14 条。

**验收 4（E9 附录存在于验证记录，含两书署名）**

```powershell
Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'E9'
```

真实输出（第一次查询在**本节尚未写入时**执行，故只反映前文状态；此处登记本次落盘后已核实的事实）：

```
本批落盘前：全文 'E9' 命中 2 处，均为无关命中：
  行431: D7 附录样本表中十六进制串内含 "E9"
  行1812: D8 附录样本 sha 串内含 "E9"
本批落盘后：新增命中来自本附录自身——附录标题行（含「E9 书籍蒸馏落盘」）、验收 4 小标题与命令行、
  以及本节各条命中登记行本身。判定依据以**附录标题行存在**为准：
  行1833: ## 附录（2026-09-16）：E9 书籍蒸馏落盘（合同 L 节 / 劳动 L 节 / 误判教训选编 / 总纲指针）
自指说明：本节的命中登记行含 "E9" 字样，会随本节内容一并计入检索结果，这是自指计数，不是额外的
独立证据；复跑时总数随本节条目增减而变，故不把总数作为验收判据，只以附录标题行存在为判据。
```

两书署名：见本附录第一节表（《合同审查精要与实务指南（第二版）》／雷霆；《劳动争议实务操作与案例精解（增订4版）》／王勤伟）。

**验收 5（无原文整句复制抽查；无新增法条编号与具体数字；改动范围；基线未变）**

```powershell
# 样本先在草稿中确认存在，再在工作区（排除 .work/ 与 .git/，37 个文本文件）检索
Select-String -Path '.work/distill-outbox/01-leiting.md' -SimpleMatch $sample
$files | Select-String -SimpleMatch $sample
```

真实输出：

```
S1 src=01-leiting.md P2 所有权保留   len=27  草稿命中=1  工作区 hits=0  新增 4 文件内 hits=0
S2 src=01-leiting.md P7 保证期间     len=37  草稿命中=1  工作区 hits=0  新增 4 文件内 hits=0
S3 src=03-wangqinwei.md P3 规章制度  len=24  草稿命中=1  工作区 hits=0  新增 4 文件内 hits=0
```

法条编号与具体数字（对新增/落盘 101 行，先剔除条目编号 `L\d` 再检索）：

```
第N条 / 第N款 / 法释 / 民法典 / 司法解释 → hits=0
% / 百分之                              → hits=0
[N]元 / [N]万元 / [N]个月 / [N]日 / [N]天 / [N]年 → hits=0
```

改动范围与基线：

```
git diff --name-only →
  docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
  legal-dept/SKILL.md
  shared/checklists/contract-redlines.md
  shared/checklists/employment-redlines.md
git status --porcelain →
  ?? shared/cases/practice-lessons.md   （本批新建，属允许清单第 3 项）
  ?? .workbuddy-ai/                     （任务开始前既有，未动）
git log -1 --format=%H → 04994d006e7eae2666544aba3f91736e3874a812（会话内未提交，基线未变）
```

- 禁止触碰文件核对：`outputs/external-review-39abd04.md`、`shared/templates/legal-opinion.md`、两份草稿、本任务简报的 `git diff` 命中数均为 0；`industries/` 改动数 0。
- **边界**：以上为**静态检查**（关键短语检索与行数比对），不是全文查重；抽查为抽样，不能据此断言「工作区绝对不含草稿或书中的任何片段」。
- **基线偏差登记**：本任务简报写「基线 `062fb80`」，实测本会话开始时 `HEAD` 已是 `04994d0`（D7／D8 两次已提交），`062fb80` 为 `04994d0` 的祖辈。本批**未提交、未推送**，`git log -1` 在会话前后均为 `04994d0`，未发生提交动作；简报所写基线滞后于实际工作区状态，特此登记。

### 四、本附录边界

- 本附录记录的是**文本与结构落盘 + 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（L 节是否会被真的加载、选编是否会被真的对照使用，均需宿主实测）。
- **未做**：两书全部章节的通读（本批只落草稿已覆盖的部分）；书中内容在中国法下的正确性复核（本批只做口径提炼与去重，未对实体结论作评价）；条号与门槛数字的联网核验（按硬口径不移植，故无核验对象）；两书版本更替后的内容变化核对。
- 草稿只读未改；本轮改动全部留在工作区，未提交、未推送。
