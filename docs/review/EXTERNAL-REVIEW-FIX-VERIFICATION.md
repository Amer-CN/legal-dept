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
