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

## 附录（2026-09-16）：E10 劳动三书蒸馏落盘（劳动 M 节 / 误判教训三书续编 / 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与 D1–D8、E9 各附录的任何结论。
> 蒸馏口径（硬）：书籍内容只提炼方法经验；**不搬原文整句**；**不写法条编号与具体数字**（草稿中照引的条号、金额与门槛一律改写为规则口径）；每条与既有清单逐条比对，已覆盖的不重复收录；生产文件不逐条署名，署名统一落在本附录。

### 一、来源署名（三书）

| # | 书名 | 作者 / 版本 | 采用的什么（中文重写转述，未搬原句） | 落盘位置 |
| --- | --- | --- | --- | --- |
| ① | 《HR必备法律工具书（第二版）》 | 洪桂彬 | 任职全周期补充条目（信息与背景核查、员工沟通工具与账号、离职文书与送达、退休返聘资格核定与工龄承继）与劳动用工类误判教训 | `shared/checklists/employment-redlines.md` 新增 M 节；`shared/cases/practice-lessons.md` 第三节（15 条） |
| ② | 《HR赋能：劳动人事争议预防与处理指南》 | 刘继承 | 同上类型的补充条目与误判教训 | `shared/checklists/employment-redlines.md` 新增 M 节；`shared/cases/practice-lessons.md` 第四节（8 条） |
| ③ | 《劳动合同法实务操作与案例精解》 | 王桦宇 | 同上类型的补充条目与误判教训 | `shared/checklists/employment-redlines.md` 新增 M 节；`shared/cases/practice-lessons.md` 第五节（7 条） |

- 落盘内容均为**要点转述与方法提炼**，未复制三书原文整句；书中照引的法条条号、具体金额与门槛数字一律**未移植**，涉及门槛之处改写为规则口径并注明按属地人社与司法口径核实。
- 去重口径：M 节条目只收 A–H、L 节未覆盖事项，逐条比对后列为未覆盖的有：入职信息收集与使用边界、录用通知的撤销与生效要件、背景调查的授权时点与证明标准、入职采集信息与电子账号管理、用工资料保管期限、文书的送达方式与顺序、员工言论与集体诉求处置、离职证明的记载口径、退休与返聘资格核定、工龄承继与补偿基数、竞业限制的启动决定与范围匹配，共 11 条。
- 去重核对中被判为**已有条目覆盖、故未收录**的典型项（对应既有条号）：试用期期限与重复约定（A2）、岗位与工作地点约定的明确性（A4）、未签书面合同与其时效衔接（L8）、超龄与特殊身份用工的关系定性（L7）、解除与终止文书的送达可举证（D7）。

### 二、本轮落盘改动（3 个生产文件）

| # | 文件 | 落盘内容 | 性质 |
| --- | --- | --- | --- |
| ① | `shared/checklists/employment-redlines.md` | 文末**新增 M 节「任职全周期补充」**（M1–M8、M10–M12 共 11 条，每条「检查＋标准立场」两段式；编号沿用原号，M9 经复核与 D7 同一事项已整条删除，故不存在 M9）；A–H、L 既有条目与节首说明**一字未动** | 生产文件；纯新增（含 1 条删除） |
| ② | `shared/cases/practice-lessons.md` | 追加三书误判教训 30 条（洪桂彬 15＋刘继承 8＋王桦宇 7），每条注明书名＋章节目；其中 6 条（第 53、54、56、60、68、84 行）在本轮复核后按转述口径改写，以消除与草稿的句级逐字重合；条目数与出处格式未变；头部版本、取材口径、性质声明与一、二节**一字未动**；文末免责声明移入新节之后 | 生产文件；纯追加 |
| ③ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 生产文件改动数：**3 个**（①②③），全部落在简报允许清单内。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件与三份草稿（`.work/distill-outbox/04-hong.md`、`05-liu.md`、`06-wang7.md`）**只读未改**；`shared/templates/legal-opinion.md` 未改；`industries/` 下全部文件未改；合同红线清单未改；F 盘其他项目未改。
- **未提交、未推送**，改动全部留在工作区。

### 三、验收命令与真实输出（逐条实跑，2026-09-16）

> 为守「不搬原文整句」口径，验收 4 的样本正文**不在本文件内引用**，只登记字符数、样本在草稿中已确认存在、以及在工作区（排除 `.work/`、`.git/`）中的命中数。

**验收 1（劳动 M 节存在、条目 ≤12（本轮改正为 ≤11）、每条配标准立场、为 A–H＋L 未覆盖项）**

```powershell
Select-String -Path 'shared/checklists/employment-redlines.md' -Pattern '^## M'
$all = Get-Content 'shared/checklists/employment-redlines.md'
$start = ($all | Select-String '^## M').LineNumber
$m = $all[($start-1)..($all.Count-1)]
"M 节条目数: $((($m | Select-String '^- M\d').Count))"
"M 节标准立场数: $((($m | Select-String '标准立场').Count))"
"M 节法条编号命中: $((($m | Select-String '第[0-9]+条|第[0-9]+款|第[0-9]+项|法释').Count))"
"M 节百分比与金额命中: $((($m | Select-String '%|百分之|\d+元|\d+万元').Count))"
```

真实输出：

```
shared\checklists\employment-redlines.md:132: ## M. 任职全周期补充（A–H、L 未覆盖项）
M 节起始行: 132; 全文行数: 157
M 节条目数(^- M\d): 11
M 节标准立场数: 11
M 节法条编号命中: 0
M 节百分比与金额命中: 0
```

> **本轮修正（E10 复核后）**：上表三行数值为删去 M9 后的复跑结果（原记录为「全文行数 159／条目数 12／标准立场数 12」）。M9 原条目为「解除与终止文书的送达生效」，与既有 D7「送达与通知方式是否可举证（本人签收、EMS 内件品名、电子送达约定）」属同一事项，按去重口径不该收录，已整条删除；删除后条目编号保留 M10–M12 原号（不重编号，以免与本节其他记录及后续引用错位），因此 M 节编号存在空档，非漏条。复跑 `Select-String -Path 'shared/checklists/employment-redlines.md' -Pattern 'M9'` 命中数为 0。

A–H＋L 未覆盖性抽查（3 条，人工判定）：

```
抽查 M1 入职信息收集与使用边界 / M6 送达方式与顺序 / M11 工龄承继与补偿口径
A–L 段（第 1–131 行）内检索（本轮复跑）：
  '背景调查' 命中 0 次；'账号' 命中 0 次；'离职证明' 命中 0 次；'工龄' 命中 0 次；'个人信息' 命中 0 次
  '送达' 命中 5 次，均为制度公示送达（C 节）、员工手册签收（C4）、合同送达地址约定（A 节）、
    解除通知送达（D7）与二倍工资举证（L8）语境，
    与 M6 的「直接送达 / 邮寄送达 / 公告张贴的顺序与留痕」不同一 → 判为未覆盖
```

> **本轮新增复核项**：M9「解除与终止文书的送达生效」与 D7 属同一事项（D7 检查项为「送达与通知方式是否可举证（本人签收、EMS 内件品名、电子送达约定）」，其标准立场已写明「送达方式、送达地址与生效时点须在制度与合同中事先约定并逐次留痕……送达不到位的，不得径行认定解除生效」），故整条删除，不再计为 M 节条目。

**验收 2（教训追加 ≤30 条、每条有出处书名）**

```powershell
Select-String -Path 'shared/cases/practice-lessons.md' -Pattern '洪桂彬|刘继承|王桦宇'
(Select-String -Path 'shared/cases/practice-lessons.md' -Pattern '^- 误以为').Count
```

真实输出：

```
shared\cases\practice-lessons.md:47: ## 三、洪桂彬《HR必备法律工具书（第二版）》（15 条）
shared\cases\practice-lessons.md:65: ## 四、刘继承《HR赋能：劳动人事争议预防与处理指南》（8 条）
shared\cases\practice-lessons.md:76: ## 五、《劳动合同法实务操作与案例精解》（王桦宇）（7 条）
误以为条目总数: 60        （基线 30 ＋ 本批 30 ＝ 60；本批新增 15＋8＋7＝30，未超 ≤30）
```

- 每条出处落到书名＋章节：第三节逐条以「（…·…）」收尾、按书内主题归并，第四、五节同样逐条带章节目；三节条数分别实测 15／8／7。

**验收 3（E10 附录存在于验证记录，含三书署名）**

```powershell
Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'E10'
```

真实输出（判定依据以附录标题行存在为准）：

```
本批落盘前：全文未出现 'E10' 字样。
本批落盘后：行2012: ## 附录（2026-09-16）：E10 劳动三书蒸馏落盘（劳动 M 节 / 误判教训三书续编 / 验证记录）
其余命中来自本附录自身（署名表、验收 3 小节与命令行）。自指计数不作为独立证据，
复跑时总数随本附录条目增减而变，故不把总数作为验收判据。
```

三书署名：见本附录第一节表（《HR必备法律工具书（第二版）》／洪桂彬；《HR赋能：劳动人事争议预防与处理指南》／刘继承；《劳动合同法实务操作与案例精解》／王桦宇）。

**验收 4（无原文整句复制抽查；无新增法条编号与具体数字）**

```powershell
# 样本先在草稿中确认存在，再在工作区（排除 .work/、.git/，实测 37 个文本文件）检索
Select-String -Path '.work/distill-outbox/04-hong.md' -SimpleMatch $s1
Select-String -Path '.work/distill-outbox/05-liu.md'   -SimpleMatch $s2
Select-String -Path '.work/distill-outbox/06-wang7.md' -SimpleMatch $s3
$files | Select-String -SimpleMatch $sample
```

真实输出：

```
S1 src=04-hong.md 教训段   len=75  草稿命中=1  工作区 hits=0  命中文件=[]
S2 src=05-liu.md  教训段   len=45  草稿命中=1  工作区 hits=0  命中文件=[]
S3 src=06-wang7.md 教训段  len=60  草稿命中=1  工作区 hits=0  命中文件=[]
```

新增行法条编号与具体数字（对 `git diff -U0` 的新增行检索）：

```
shared/checklists/employment-redlines.md  新增行=27（含 M9 删除后重排；相对基线无删除行以外的改动）  法条编号=0  具体数字=0
shared/cases/practice-lessons.md          新增行=39  法条编号=0  具体数字=0
docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md 新增行=200（其中正文 135 行）
                                                正文法条编号=0  正文金额/百分比=0
```

- 上表为**本轮复核改正后**的复跑值（原记录：redlines 29 行、本文件 144 行／正文 123 行）；redlines 由 29 变 27 系 M9 两行删除所致，本文件增加系新增「三之二」小节所致。
- 法条编号与金额/百分比的两处命中均落在**命令行字符串**内（验收 1 代码块中的 `Select-String '第[0-9]+条…'` 与 `'%|百分之|\d+元|\d+万元'`），非正文引用，正文计数为 0。

- 口径说明：`practice-lessons.md` 首次检索时唯一命中项为第五节的**书名**《劳动合同法实务操作与案例精解》（书名中的法律名称），非条号引用；为免与条号引用混淆，已把该节标题改为例行格式「《书名》（作者）」，改后法条编号命中为 0。验证记录自身新增行中的命中项仅为命令字符串与口径说明行，正文计数为 0。

改动范围与基线：

```
git diff --name-only →
  docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
  shared/cases/practice-lessons.md
  shared/checklists/employment-redlines.md
git status --porcelain →
  ?? .workbuddy-ai/   （任务开始前既有，未动）
git rev-parse --short HEAD → 64c5578（会话内未提交，基线未变）
```

- 禁止触碰文件核对：`outputs/external-review-39abd04.md`、`shared/templates/legal-opinion.md`、三份草稿、`industries/` 与合同红线清单的 `git diff` 命中数均为 0。
- **边界**：以上为**静态检查**（关键短语检索与行数比对），不是全文查重；抽查为抽样，不能据此断言「工作区绝对不含草稿或书中的任何片段」。样本清单本身未写入仓库，以免样本自身构成命中。

### 三之二、E10 复核后的改正（3 处）

> 触发：E10 落盘后经复核，认定三处问题成立——①`shared/checklists/employment-redlines.md` 的 M9 与既有条目 D7 属同一事项（违反「已覆盖项不收录」的去重口径）；②`shared/cases/practice-lessons.md` 第三节、第四节有三条与草稿存在句级逐字重合；③本附录原「去重口径」段所列未收录项与实际落盘内容不符。以下记录本轮改正与复跑证据（②按同一判据对 30 条新条目全量扩查后，共改写 6 行：53、54、56、60、68、84 行）。

**① M9 整条删除（同事项重复）**

- 删除对象：`- M9 解除与终止文书的送达生效：…` 及其「标准立场」子行（共 2 行）。
- 同一事项依据：既有 `- D7 送达与通知方式是否可举证（本人签收、EMS 内件品名、电子送达约定）。` 及其标准立场「解除通知与相关文书的送达方式、送达地址与生效时点须在制度与合同中事先约定并逐次留痕……送达不到位的，不得径行认定解除生效」已完整覆盖 M9 的检查点与立场。
- 复跑证据：
  - `Select-String -Path 'shared/checklists/employment-redlines.md' -Pattern 'M9'` → 命中 0。
  - M 节条目数 `^- M\d` → 11（原 12）；标准立场 11；全文行数 157（原 159）。
  - 编号保留 M10–M12 原号，M 节编号存在空档（缺 M9），系删除所致，非漏条。

**② 教训条目改写（消除与草稿的句级逐字重合）**

- 改写对象与判据（判据＝把条目正文去掉「误以为」前缀与末尾出处后，与三份草稿全文（同样去掉标点空白）求最长公共子串；阈值 ≥20 字）：

```
practice-lessons.md 行号   改写前最长公共子串   命中草稿            改写后最长公共子串
行 53（洪·离职证明）              <20 字（未达阈值）  04-hong.md          0（<20 字）
行 54（洪·试用期次数）            21 字              04-hong.md           0（<20 字）
行 56（洪·绩效考核审查）          69 字              04-hong.md           0（<20 字）
行 60（洪·收购方人员转移）        30 字              04-hong.md           0（<20 字）
行 68（刘·不辞而别）              33 字              05-liu.md            0（<20 字）
行 84（王·高管离职）              20 字              06-wang7.md          0（<20 字）
```

  - 行 68 改写前的 33 字重合段为「法律上没有这种解除方式应按旷工并达到严重违纪标准后作出解除」（含复核所指尾段）；行 84 改写前的 20 字重合段为「解除反而使企业面临违法解除与损害赔偿风险」。两行改写后与草稿的最长公共子串均为 0。
  - 复跑命令（逐行求最长公共子串）：对 `shared/cases/practice-lessons.md` 第 53、54、56、60、68、84 行与 `.work/distill-outbox/04-hong.md`、`05-liu.md`、`06-wang7.md` 三份草稿比对，6 行的最长公共子串均 <20 字（实测均为 0）。
  - 全量扩查残留：对 30 条新条目逐行比对后，仍有 4 行的最长公共子串达到或超过 20 字（行 52「…在公司微信群里发通报可以顺带公示材料——通报与催告函载明员工」30 字、行 58「法定节假日加班可以用排班补休抵掉——补休不能抵扣法定节假日」29 字、行 73「兼职出事故两家都不担责——两单位均未缴工伤保险」23 字、行 74「同一法院对超龄用工性质的裁判标准是一致的——」22 字）。这 4 行不属复核所指的三条，本轮**未改写**，作为已知残留登记备查。
  - 行 53 改写前未达 20 字阈值，仍按同一口径改写（原句含「记载评价性内容……侵害个人信息与再就业权」的成段对应），一并登记。
  - 条目总数不变：仍为 30 条（洪 15／刘 8／王 7），各节标题条数未变。

**③ 去重口径段修正**

- 原文写「如个人信息最小必要与泄密补救、录用通知的要约性质、体检与传染病携带者拒录、违规下载软件、在职竞业限制与报告义务、试用期工资下限、岗位与工作地点约定的明确性、二倍工资时效、超龄用工与退休年龄档次、农民工工资台账等，M 节不再重复」。
- 不符之处：①举例中的「个人信息最小必要」「录用通知的要约性质」「超龄用工与退休年龄档次」实际已以 M1／M2／M10 的形态收录进 M 节，原句却把它们写成「M 节不再重复」，与落盘实际相反；②举例中的「岗位与工作地点约定的明确性」由既有 A4 覆盖、「二倍工资时效」由既有 L8 覆盖、「超龄用工性质」由既有 L7 覆盖，原句将它们与 M 节的新增项混列，指向的既有条号也未写明。
- 改正后：改为「M 节条目只收 A–H、L 节未覆盖事项，逐条比对后列为未覆盖的有……共 11 条」，并另立一行列明「被判为已有条目覆盖、故未收录」的典型项及其既有条号（试用期期限与重复约定→A2；岗位与工作地点约定的明确性→A4；未签书面合同与其时效衔接→L8；超龄与特殊身份用工的关系定性→L7；解除与终止文书的送达可举证→D7）；「M 节不再重复」的表述已删除，不再与 M1／M2／M10 的落盘事实冲突。

### 四、本附录边界

- 本附录记录的是**文本与结构落盘 + 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（M 节是否会被真的加载、教训选编是否会被真的对照使用，均需宿主实测）。
- **未做**：三书全部章节的通读（本批只落草稿已覆盖的部分）；书中内容在中国法下的正确性复核（本批只做口径提炼与去重，未对实体结论作评价）；条号、金额与门槛数字的联网核验（按硬口径不移植，故无核验对象）；三书版本更替后的内容变化核对。
- 草稿只读未改；本轮改动全部留在工作区，未提交、未推送。
- **本轮复核改正（见三之二）**：M9 删除、教训 6 行改写、本节去重口径段修正，均已完成并复跑验证；未做的是该小节中登记的 4 行残留句级重合的改写（不属复核所指问题），以及第二轮独立复核（由复核方另行进行）。

## 附录（2026-09-16）：E11 建工两书蒸馏落盘（施工合同清单 / 结算验收与安全清单 / 误判教训续编 / 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与 D1–D8、E9、E10 各附录的任何结论。
> 蒸馏口径（硬）：书籍内容只提炼方法经验；**不搬原文整句**；**不写任何法条编号与具体数字**（草稿中照引的条号、案号、金额与门槛一律改写为规则口径）；每条与既有清单逐条比对，已覆盖的不重复收录；生产文件不逐条署名，署名统一落在本附录。

### 一、来源署名（两书）

| # | 书名 | 作者 | 采用的什么（中文重写转述，未搬原句） | 落盘位置 |
| --- | --- | --- | --- | --- |
| ① | 《法院审理建设工程案件观点集成（第二版）》 | 朱树英 | 施工合同审查的补充检查项（联合体实际履行、发包人主体识别、变更与索赔指令、内部承包实质认定）与建工类误判教训 | `industries/construction/checklists/construction-contract.md`、`shared/cases/practice-lessons.md` 第六节 |
| ② | 《建设工程合同注释书》 | 史智军 | 结算、验收与优先受偿的补充检查项（债权转让、以房抵顶与债务加入、验收认定口径、保修期起算）与建工类误判教训 | `industries/construction/checklists/completion-settlement-safety.md`、`shared/cases/practice-lessons.md` 第七节 |

- 落盘内容均为**要点转述与方法提炼**；书中照引的法条条号、司法解释编号、案号与具体金额、比例、期限门槛一律**未移植**，涉及门槛之处改写为规则口径。
- **「未复制原文整句」的实测范围与更正**：本批只做了抽样比对，未逐条比对，故**不能**断言「未复制两书原文整句」这一全称结论。实测范围：① 三个样本整句（长度 18／20／16 字）在工作区（排除 `.work/`、`.git/`、`.workbuddy-ai/`）命中数均为 0；② 本批新增 17 条检查项与 20 条教训逐条对两份草稿求**最长公共子串**（方法见第五节之四）。**实测发现有 1 条未改写的新增检查项（安全生产节「分包相对方的安全生产条件与资质」条）与 `07-guandian.md` 的一条草稿存在 24 字连续重合**，达到或超过 20 字阈值；另有 1 条新增检查项改写前与 `08-zhushi.md` 第 151 条存在 37 字连续重合（本轮已改写，见第五节之四）。故正确表述为：**除下述已写明者外，本批文字为转述改写**，而不是「不存在任何整句复制」。
- 草稿去向：`.work/distill-outbox/07-guandian.md`（三分类合计七百余条）与 `.work/distill-outbox/08-zhushi.md`（三分类合计八百余条）为**只读输入**，本批未修改。
- **署名自我更正（本轮实跑发现）**：本节首版把 ① 的书名误写为同一作者的《墨斗匠心定经纬：建设工程疑难案件办案思路与执业技巧》（该书名见 `.work/distill-inbox/weread-exporter/output/最高人民法院建设工程施工合同司法解释（二）：理解适用与实务指南.md` 第 3436 行的他人引注）。经回查草稿来源：`.work/distill-outbox/07-guandian.md` 由其 22 个分片合并而成，分片头部逐片标注来源为**朱树英《法院审理建设工程案件观点集成（第二版）》**（如 `.work/distill-frag/guandian-p1.md` 第 3 行、`guandian-p6.md`／`p7.md`／`p8.md`／`p10.md`／`p17.md` 首行）。故 ① 的书名已更正为《法院审理建设工程案件观点集成（第二版）》，`shared/cases/practice-lessons.md` 第六节标题与逐条出处同步更正；`08-zhushi.md` 自带署名，② 无更正。
- **逐条出处口径更正（同轮）**：第六节条目首版按推测的章节目署名，回查分片后确认其中数条的章节归属无法从现有材料准确判定，故第六节条目统一**只署书名**，不虚构章节目；第七节草稿有明确章节结构（检查项／标准立场／误判教训三类），出处口径由章节目统一改为书名，理由同为不虚构章节号。
- 采集面说明：本批只收与施工合同、竣工结算与验收、误判教训三类落点直接对应的条目；草稿中「标准立场」类条目未单独落盘——既有清单与红线清单已采用「检查项＋标准立场」两段式的位置有限，本批不为此新开节。

### 二、去重口径（已覆盖项不收录）

- **检查项去重**：新增项只收既有清单未覆盖事项。核对范围为 `industries/construction/checklists/` 五个清单（施工合同、竣工结算与安全生产、招投标合规、签证索赔时效表、农民工工资）与 `shared/checklists/` 的合同红线清单、招投标通用清单。
- **被判定为已有条目覆盖、故本批未收录的典型项及覆盖位置**：付款时间无约定或约定不明时的顺序推定（`claim-limitation.md` 期限外说明与结算相关条目已有）、专家辅助人与鉴定意见质证（既定级与证据口径已有）、农民工工资支付程序（本包 `migrant-wage.md` 已有）、招标程序时效（`claim-limitation.md` 与 `bidding-compliance.md` 已有）、优先受偿权行使期限上限与起算（`claim-limitation.md` 第 7 项已有）。
- **误判教训去重**：`shared/cases/practice-lessons.md` 第五至第七节收建工类教训，与既有第一至第五节（合同、劳动类）逐条比对，无同事项重复；同一本书内语义相近的条目已合并为一条（合并处见本附录第五节）。

### 三、本轮落盘改动（3 个生产文件）

| # | 文件 | 落盘内容 | 性质 |
| --- | --- | --- | --- |
| ① | `industries/construction/checklists/construction-contract.md` | 文末新增施工合同补充检查项若干条（联合体实际履行与对外表现、付款对象锁定合同相对方、变更与结算文件签署权限、撤场交接与已完工程量盘点、多份互斥合同文本的比对与履行证据、履约保证金与支付担保、承包范围与包干对象、材料机械范围划归、劳务分包资质与主体结构自行完成、质保金扣留与返还），每条冠「（未覆盖项）」；既有条目与免责声明**一字未动** | 生产文件；纯新增 |
| ② | `industries/construction/checklists/completion-settlement-safety.md` | 「结算与优先受偿」节新增债权转让与以房抵顶、债务加入、主合同解除后的担保责任三条；「竣工验收与备案」节新增验收合格认定依据、验收前提条件、保修期起算与两期制度区分三条；「安全生产」节新增分包相对方安全生产条件与资质核验一条；各条冠「（未覆盖项）」；既有条目**一字未动** | 生产文件；纯新增 |
| ③ | `shared/cases/practice-lessons.md` | 第五节之后新增第六节（朱树英，建工误判教训）与第七节（史智军，建工误判教训），合计不超过简报上限，每条以「（章·节）」收尾并注明书名；头部版本、取材口径、性质声明与第一至第五节**一字未动**；文末免责声明位置未变 | 生产文件；纯追加 |

- 第 4 个文件 `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` 为本附录，属验证记录，不计入生产文件。
- 生产文件改动数：**3 个**（①②③），全部落在简报允许清单内。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件与两份草稿（`.work/distill-outbox/07-guandian.md`、`08-zhushi.md`）**只读未改**；`shared/templates/legal-opinion.md` 未改；`industries/construction/` 下的 `regulations.md`、`SKILL.md`、`claim-limitation.md`、`bidding-compliance.md`、`migrant-wage.md` 未改；`shared/checklists/contract-redlines.md`、`employment-redlines.md` 未改；F 盘其他项目未改。
- **未提交、未推送**，改动全部留在工作区。

### 四、验收命令与真实输出（逐条实跑，2026-09-16）

> 为守「不搬原文整句」口径，验收四的样本正文**不在本文件内引用**，只登记样本在草稿中已确认存在、以及在工作区（排除 `.work/`、`.git/`）中的命中数。

**验收 1（施工合同清单新增项不超上限、为未覆盖项）**

```powershell
Select-String -Path 'industries/construction/checklists/construction-contract.md' -Pattern '（未覆盖项）'
```

真实输出：

```
industries\construction\checklists\construction-contract.md:15: - [ ] （未覆盖项）联合体投标的：…
（第 15–24 行连续，共 10 处）
```

结论：**通过**。新增项为文件第 15–24 行共 10 处，未超简报上限；既有 10 条与免责声明行号不变。

未覆盖性抽查（3 条，人工判定，判据为在既有清单全文检索该事项的关键词，检索池排除本批新增行，实测 398 行）：

```
抽查「付款对象锁定合同相对方」：检索 '付款对象|收款人|项目经理付款' → 0 命中 → 未覆盖
抽查「变更与结算文件签署权限」：检索 '签署权限|有权签署|授权签署' → 0 命中（claim-limitation 只写时限，不写签署权限）→ 未覆盖
抽查「多份互斥合同文本的比对」：检索 '两份合同|多份合同|价款互斥|文本比对' → 0 命中 → 未覆盖
补记：「付款对象」在 migrant-wage.md 第 3、5 行出现的「专用账户」属农民工工资专户语境，与工程款付款对象不同一
```

**验收 2（结算清单新增项不超上限、为未覆盖项）**

```powershell
Select-String -Path 'industries/construction/checklists/completion-settlement-safety.md' -Pattern '（未覆盖项）'
```

真实输出：

```
industries\construction\checklists\completion-settlement-safety.md:11: - [ ] （未覆盖项）工程款债权转让的：…
:12: - [ ] （未覆盖项）以房抵顶工程款或以承债方式结算的：…
:13: - [ ] （未覆盖项）发包人一并主张担保责任的：…
:20: - [ ] （未覆盖项）竣工验收合格的认定以建设单位出具的竣工验收报告为依据，…
:21: - [ ] （未覆盖项）竣工验收前提条件是否一并核对：…
:22: - [ ] （未覆盖项）保修期起算点是否按竣工验收合格之日逐项核定，…
:31: - [ ] （未覆盖项）专业分包与劳务分包相对方的安全生产条件与相应资质是否核验；…
（共 7 处）
```

> **快照口径说明**：以上快照保留首次检索时的文字。其中 `:20:` 行**该行其后已改写**，见第五节之四；`:31:` 行其后亦已改写，同见第五节之四。

结论：**通过**。新增项共 7 处（结算节 3、验收节 3、安全节 1），未超简报上限；三节既有条目行号不变。

未覆盖性抽查（2 条，人工判定）：

```
抽查「以房抵顶工程款的过户与交付」：检索 '以房抵债|抵顶工程款|过户' → 1 命中，落在 contract-redlines.md 第 206 行
  「名义股东处分代持股权应分负担行为与处分行为两层…」（股权代持语境），与工程款以房抵顶不同一 → 判为未覆盖
抽查「分包相对方安全生产条件与资质」：安全生产节既有四项均在许可证、持证在岗、交底留痕、事故处置范围内，
  检索 '分包单位安全协议' 命中 1 处（既有第 23 行），但该项只及于安全协议签署，
  不含相对方安全生产条件与资质核验 → 与新增项不同一 → 判为未覆盖
```

**验收 3（教训追加不超上限、每条有出处书名）**

```powershell
Select-String -Path 'shared/cases/practice-lessons.md' -Pattern '朱树英|史智军'
```

真实输出：

```
shared\cases\practice-lessons.md:86: ## 六、朱树英《法院审理建设工程案件观点集成（第二版）》
shared\cases\practice-lessons.md:99: ## 七、史智军《建设工程合同注释书》
（其余命中为验收口径说明所需，不另列）
```

条目计数（脚本实测）：

```
shared/cases/practice-lessons.md  总行数 114
全部「- 误以为」条目数: 80      （基线 60 ＋ 本批 20 ＝ 80）
第六节条目数: 10   第七节条目数: 10   本批新增合计: 20（未超简报上限）
```

结论：**通过**。两节各 10 条、合计 20 条，每条行内以书名收尾（第六节 10 条、第七节 10 条，出处均为书名）。

节标题口径说明：第六、七节标题**不挂条数后缀**，与第一至第五节标题体例（《书名》（n 条））不同——本批硬口径为「不写具体数字」，故新节标题只留书名与作者；既有五节标题**一字未动**。

出处口径说明：第六节条目统一**只署书名**（分片上只标注了书与部分章节号，逐条章节目无法从现有材料准确判定，不虚构）；第七节条目同样只署书名（草稿的三分类结构与书面章节号不对应）。两节的出处均满足「注明出处只到书名」的口径。

**验收 4（E11 附录在验证记录，含两书署名；无原文整句复制；无新增法条编号与具体数字；基线未变；未提交推送）**

```powershell
Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'E11'
```

真实输出（判定依据以附录标题行存在为准）：

```
本批落盘前：全文未出现 'E11' 字样。
本批落盘后： ## 附录（2026-09-16）：E11 建工两书蒸馏落盘（施工合同清单 / 结算验收与安全清单 / 误判教训续编 / 验证记录）
其余命中来自本附录自身（署名表、验收小节与命令行）。自指计数不作为独立证据，复跑时总数随本附录条目增减而变，
故不把总数作为验收判据。
```

两书署名见本附录第一节表（《法院审理建设工程案件观点集成（第二版）》／朱树英；《建设工程合同注释书》／史智军）。

无原文整句复制抽查（样本先在草稿中确认存在，再在工作区排除 `.work/`、`.git/`、`.workbuddy-ai/` 后检索，实测扫描文本文件 36 个）：

```powershell
Select-String -Path '.work/distill-outbox/07-guandian.md' -SimpleMatch $s2
Select-String -Path '.work/distill-outbox/08-zhushi.md'   -SimpleMatch $s1
$files | Select-String -SimpleMatch $sample
```

真实输出：

```
S1 src=08-zhushi.md 检查项段   草稿命中=1   len=18   工作区 hits=0   命中文件=[]
S2 src=07-guandian.md 误判教训段 草稿命中=2   len=20   工作区 hits=0   命中文件=[]
S3 src=08-zhushi.md 误判教训段  草稿命中=1   len=16   工作区 hits=0   命中文件=[]
```

补充：句级重合度自检（判据＝把本批新增 20 条教训去掉前缀与末尾出处后，与两份草稿全文去掉标点空白求最长公共子串）：

```
本批 20 条与草稿的最长公共子串：1–17 字，最大 17 字，达到或超过 20 字者 0 条
首次自检时有 3 条达到或超过阈值（21／22／24 字，均已按同一判据改写，改写后分别为 9／9／17 字）
```

> **范围更正（复核后）**：以上只覆盖本批 20 条教训。自检范围已扩大到本批全部新增文字（17 条检查项 ＋ 20 条教训），复跑结果与本次发现的修正见**第五节之四**。

新增行法条编号与具体数字（对 `git diff -U0` 的新增行检索）：

```
industries/construction/checklists/construction-contract.md          新增行=10   条号/期限命中=0   数字与百分号命中=0
industries/construction/checklists/completion-settlement-safety.md   新增行=7    条号/期限命中=0   数字与百分号命中=0
shared/cases/practice-lessons.md                                     新增行=26   条号/期限命中=0   数字与百分号命中=0
```

- 三处计数均为 0；本批新增文字中不含「第×条／第×款／法释」类条号引用，也不含阿拉伯数字、金额、比例与期限门槛。
- 检索口径：条号与期限数字用 `第[0-9一二三四五六七八九十]+条|第[0-9]+款|[0-9]+天|[0-9]+个月|法释|解释[一二]`；数字与百分号用 `[0-9]|%|百分之`。
- 首次检索时 `practice-lessons.md` 命中两条，均为新增节标题的条数后缀；已删除该后缀（见验收 3 的节标题口径说明），复跑后为 0。
- `practice-lessons.md` 新增行的检索未命中既有章节中的书名（第一至第五节书名中的法律名称不在本批新增行内）。

改动范围与基线：

```
git diff --name-only →
  docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
  industries/construction/checklists/completion-settlement-safety.md
  industries/construction/checklists/construction-contract.md
  shared/cases/practice-lessons.md
git status --porcelain →
   M docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
   M industries/construction/checklists/completion-settlement-safety.md
   M industries/construction/checklists/construction-contract.md
   M shared/cases/practice-lessons.md
  ?? .workbuddy-ai/   （任务开始前既有，未动）
git rev-parse --short HEAD → 5ca6dfa（会话内未提交，基线未变）
```

结论：**通过**。改动集中在允许清单的 4 个文件内，基线未变，未提交、未推送。

- **边界**：以上为**静态检查**（关键短语检索、条目计数与行数比对），不是全文查重；抽查为抽样，不能据此断言「工作区绝对不含草稿或书中的任何片段」。样本清单本身未写入仓库，以免样本自身构成命中。

### 五、条目合并与改写登记（同一事项合并 + 句级重合改写 + 署名更正，非漏条）

**一、同一事项合并（合并前的内容已整体并入合并后的条目，未丢信息）**

- `practice-lessons.md` 第七节中两组条目经合并后计数：优先受偿权的行使期限起算与行使方式合为一条（同一事项的两个侧面）；隐蔽工程未通知检查的责任与缺陷责任期、保修期的区分合为一条（同属质量责任与期限认定的一处口径）。
- `practice-lessons.md` 第六节中，联合体实际履行的认定与发包人主体识别合为一条（同一节的相邻口径，避免同事项拆条）。
- 合并后两节各 10 条，条目为无序号连排条目，不存在编号空档。

**二、句级重合改写（自查发现后按同一判据改写）**

自检发现下列三条与草稿的最长公共子串达到或超过阈值，已按「不搬原文整句」口径改写：

```
条目（改写前）                                    改写前 LCS   改写后 LCS
史智军·优先受偿权放弃的效力（贷款关系是否落地）        21 字        9 字
史智军·固定价与鉴定范围                            22 字        9 字
史智军·项目经理与表见代理·对外行为的归属             24 字       10 字
```

> **口径说明（第二轮复核后补记）**：上表「改写后 LCS」栏第三行原登记 17 字，经复核提出后复跑更正为 **10 字**。复跑口径：被测行去掉末尾出处署名后，与两份草稿**去空白与标点**求最长公共子串——同一口径下三行复跑值为 **7／11／10 字**；第三行若含出处书名并保留标点则为 14 字，两种口径均低于 20 字阈值。第一、二行原登记 9／9 字系首次自检口径，本轮未改。

改写未改变条目主张与出处标注，两节条数不变（各 10 条）。改写后本批 20 条与两份草稿的最长公共子串区间为 1–17 字，无一条达到阈值。

**三、署名与出处更正（见本附录第一节两条自我更正说明）**

- 书名更正：① 由《墨斗匠心定经纬：建设工程疑难案件办案思路与执业技巧》改为《法院审理建设工程案件观点集成（第二版）》——依分片头部逐片标注的来源。
- 出处粒度更正：第六、七节条目由「章节目」统一改为「只到书名」。
- 文件性质复核：`shared/cases/practice-lessons.md` 本次为**纯追加**，`git diff --shortstat` 实测「1 file changed, 26 insertions(+)」，删除行数为 0；第 1–85 行（第一至第五节、节标题与头部声明）与基线 `5ca6dfa` 逐行比对差异数为 0，文末免责声明两行位置未变。

**四、句级重合自检范围扩大与复跑（复核后改正）**

- **改了什么**：原先的句级重合自检只覆盖本批 **20 条教训**；本节所述复核指出该范围不足，自检范围已扩大到本批**全部新增文字＝17 条检查项 ＋ 20 条教训**，并复跑登记结果。
- **判据（一致）**：被测文字与两份草稿分别去掉空白与标点后，求**连续**相同字符串的最大长度（最长公共子串）；达到或超过 20 字即判为需要改写。该判据与复核所指口径一致（连续子串），与「整句在全文中的命中数」（只有当被测句与草稿某句逐字相同才会命中）是两个不同量，本节两者都登记。
- **复跑结果（2026-09-16，本机实跑）**：

```
17 条新增检查项 最长公共子串：5–24 字
  最小 5 字（施工合同清单·变更与签证条）
  最大 24 字（安全生产节·分包相对方安全生产条件与资质条，首次复跑时未改写；该条其后已改写，见本节末「第二轮复核后已改」）
  ≥20 字者 1 条；其余 16 条为 5–15 字
20 条新增教训 最长公共子串：8–18 字，≥20 字者 0 条
  与前次登记一致（前次区间 1–17 字；本次按「全行含出处」口径计，最大值出现在含出处书名的最长条目上）
```

- **本次发现的真问题（已改）**：结算清单「竣工验收与备案」节新增的**验收认定口径**条，其改写前文字与 `08-zhushi.md` 第 151 条存在 **34** 字连续重合，是本批唯一达到阈值、且本轮获授权改写的条目；改写后与 `08-zhushi.md` 第 151 条的最长公共子串为 **6** 字（与两份草稿比对的最大值同为 6 字，即 `验收是否合格` 六字，属该条主张的核心术语，无法再降）。语义保留——验收是否合格的认定依据是建设单位一方出具的验收报告、办不办备案不影响该认定；并保留「未经发包人同意由承包人单方组织的验收不产生该效力」的后半句。
- **第二轮复核后已改（原「未改」登记作废）**：上面「最大 24 字」的那 1 条（安全生产节「分包相对方安全生产条件与资质」条，落在结算清单第 31 行）**已按同一判据（<20 字）改写**。改写前该行对 `07-guandian.md` 为 **24 字**（保留标点口径，即复核所指的 24 字；去标点口径为 23 字），改写后为 **8 字**；对两份草稿的最大值由 24 字降至 **9 字**（落在 `08-zhushi.md`，片段「专业分包与劳务分包」，属该行前半句的既有术语）。语义保留——把工程发包给安全生产条件不达标或不具备相应资质的主体施工的，一旦发生生产安全事故，发包人可能要对事故损害承担连带的赔偿责任。本次只改该行后半句，条目数、行号与其余文字未动，本批其余条目未再改写。
- **第二轮复跑（2026-09-16，上述改写后，本机实跑）**：

```
17 条新增检查项 最长公共子串：5–12 字（施工合同清单 5–12 字、结算验收与安全清单 6–12 字），≥20 字者 0 条
20 条新增教训 最长公共子串：7–17 字，≥20 字者 0 条
口径：被测行去掉末尾出处署名后，与两份草稿去空白与标点求最长公共子串（同本节判据）；若只去空白不去标点，上列区间为 5–15 字与 7–18 字
```

- **方法学更正（本轮实跑发现，须与上节同读）**：把「最长公共子串」与「最长公共子序列」混用会得出完全不同的数。实测同一条检查项对同一份草稿：最长公共**子串** 7 字，而最长公共**子序列** 85 字——后者的匹配字符散布在草稿全文约 3.9 万字的范围内（实测命中位置从第 2 字跨到第 39318 字），并非「一句原文被搬走」。本文件此前与本节所用的 20 字阈值，口径是**连续子串**；子序列数只作附记，不作为判据。

### 六、本附录边界

- 本附录记录的是**文本与结构落盘 + 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（新增检查项是否会被真的加载、教训选编是否会被真的对照使用，均需宿主实测）。
- **未做**：两书全部章节的通读（本批只落草稿已覆盖的部分）；书中内容在中国法下的正确性复核（本批只做口径提炼与去重，未对实体结论作评价）；条号、金额与门槛数字的联网核验（按硬口径不移植，故无核验对象）；两书版本更替后的内容变化核对；「标准立场」类草稿条目的单独落盘（见本附录第一节采集面说明）。
- 草稿只读未改；本轮改动全部留在工作区，未提交、未推送。
- **本轮复核改正（见第五节之四）**：结算清单「竣工验收与备案」节那条改写（34 字连续重合 → 6 字）、第一节「未复制原文整句」全称断言的更正（改为「抽样比对」并写明实测范围与本次发现），均已完成并复跑；**未改**的是第五节之四登记的那 1 条 24 字连续重合项（不属本次复核所指问题），以及由复核方另行进行的第二轮独立复核。

## 附录（2026-09-16）：E12 建工九书蒸馏落盘（施工合同清单 / 结算验收与安全清单 / 招投标合规清单 / 误判教训续编 / 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与 D1–D8、E9、E10、E11 各附录的任何结论。
> 蒸馏口径（硬）：书籍内容只提炼方法经验与判断口径；**不搬原文整句**；**不写任何法条编号与具体数字**（九份草稿中照引的条号、司法解释编号、案号、金额、比例与期限门槛一律改写为规则口径）；每条与既有清单逐条比对，已覆盖的不重复收录；生产文件不逐条署名，署名统一落在本附录。

### 一、来源署名（九书）

| # | 书名 | 作者 | 采用的什么（中文重写转述，未搬原句） | 落盘位置 |
| --- | --- | --- | --- | --- |
| ① | 《法院审理建设工程案件观点集成（第二版）》 | 朱树英 | 总承包风险分担与价格形式、调价边界，以及程序层面的补充教训 | `industries/construction/checklists/construction-contract.md`、`shared/cases/practice-lessons.md` 第八节 |
| ② | 《建设工程合同注释书》 | 史智军 | 鉴定意见的可分采信、以物抵债与新债清偿 | `industries/construction/checklists/completion-settlement-safety.md`、`shared/cases/practice-lessons.md` 第九节 |
| ③ | 《建设工程法律实务》 | 李舒 | 分包与劳务的界分、挂靠与内部承包的识别、借支冲抵的判断要素 | `shared/cases/practice-lessons.md` 第十节 |
| ④ | 《施工方追索建设工程款裁判规则与实务指引》 | 梅俊 | 索赔失权的前置判断、未完工程比例结算、代位权次债权确定 | `shared/cases/practice-lessons.md` 第十一节 |
| ⑤ | 《工程总承包（EPC／DB）争议解决实战攻略》 | 张旗等 | 总承包与施工承包在价款确定上的差异、内部承包与挂靠识别、行政审计约定的主体与范围 | `shared/cases/practice-lessons.md` 第十二节 |

| ⑥ | 《工程总承包项目运作实务》 | 王洋等 | 招标控制价与暂估价的处理、设计优化与变更的区分、送审结算的申报风险 | `shared/cases/practice-lessons.md` 第十三节 |
| ⑦ | 《工程总承包（DB/EPC）项目安全生产法务指引》 | 李宏远等 | 总承包模式下的安全生产责任分配、安全文明措施费的专款专用 | `industries/construction/checklists/completion-settlement-safety.md`、`shared/cases/practice-lessons.md` 第十四节 |
| ⑧ | 《建设工程总承包项目招标与投标操作实务》 | 陈津生等 | 投标保函与送达、否决条款的集中表述、评标专家人数的计法 | `industries/construction/checklists/bidding-compliance.md`、`shared/cases/practice-lessons.md` 第十五节 |
| ⑨ | 《建设工程施工合同案件裁判规则解析》 | 谢勇 | 中标通知书到达即本约成立与拒签一方按违约责任处理、中标无效即承诺无效而合同未成立、接收未验收工程而认定竣工日期的适用条件 | `shared/cases/practice-lessons.md` 第九节（第 126、127 行）、`industries/construction/checklists/completion-settlement-safety.md` 第 33 行 |

> **第⑨行署名更正**：本表初稿该行曾误署为张旗等名下，并把草稿内的「十六」分篇号误当作书名（该书名在九份草稿、碎片与书单中均**不存在**，本轮已从本附录署名表与 `shared/cases/practice-lessons.md` 中删除）。经复核，该书实为谢勇《建设工程施工合同案件裁判规则解析》（草稿 `13-xieyong.md` 第 1 行明载「# 谢勇《建设工程施工合同案件裁判规则解析》要点合并」），书单（`.work/weread-booklist.md` 第 17、98 行）所列亦为该书，故按实际来源改署谢勇。谢勇书在本批的落盘位置即上表本行所示：教训第九节两条（第 126、127 行）与结算清单第 33 条——**保留在其实出处位置**，第十六节解散后不再有指向该节的落盘位置。
>
> **第 126 行行内署名与来源不一致（原登记「未改」，本轮已订正）**：该条来源确为谢勇草稿（`13-xieyong.md` 第 72–73 行；`08-zhushi.md` 无对应项），但该条行内署名原写作「（史智军《建设工程合同注释书》）」，与来源不符；该项上轮按「不碰其他任何内容」未改动、只作登记。本轮（E12 附录同步订正）已把该条行内署名改为「（谢勇《建设工程施工合同案件裁判规则解析》）」，与上表第⑨行所列落盘位置一致。

**来源核实（逐条回草稿核对，含本轮裁定后的归属与订正项）**：`shared/cases/practice-lessons.md` 与结算清单中涉及第⑨行及原第十六节 5 条的条目逐条回草稿核对，结论如下——

| 条目 | 原署 | 核实依据 | 处置（含本轮裁定执行结果） |
| --- | --- | --- | --- |
| 教训第 126 行（列第九节） | 史智军《建设工程合同注释书》 | 草稿 `13-xieyong.md` 第 72–73 行（「竣工日期认定查三条路径……未经竣工验收发包人擅自使用，以转移占有建设工程之日」「审查『发包人擅自使用』必须同时核实其是否实际使用：仅接收而未使用、仍保留原状的，不得以转移占有之日为竣工日期」），与第 295 行同义；`08-zhushi.md` 无对应项（该草稿 227、228 两条讲的是使用面积占比与未完工移交，均不含本句） | 来源确为**谢勇**；该条行内署名原为「史智军《建设工程合同注释书》」，本轮已订正为「谢勇《建设工程施工合同案件裁判规则解析》」，见上注 |
| 结算清单第 33 条（`completion-settlement-safety.md` 第 33 行） | 史智军《建设工程合同注释书》 | 同上 `13-xieyong.md` 第 72–73 行，与清单该条逐点对应（含「承包人擅自撤场后迫于无奈接收」同例，见 `13-xieyong.md` 第 295 行） | 来源属**谢勇**；清单为生产文件、按本批硬口径不逐条署名，故只在第一节表与本表登记 |
| 教训原第 179 行（第十六节） | （不存在的书名，初稿误署张旗等） | `13-xieyong.md` 第 17 行（「到达即承诺生效、本约成立，不因未在 30 日内另签合同书而否定合同关系」）、第 132 行（「中标通知书到达中标人时本约成立，一方不履行的应承担违约责任，而非缔约过失责任」）、第 247–248 行（误判教训段，驳「合同不成立只可主张缔约过失」与「中标通知书为预约」两说）。`12-epc-bid.md` 第 391、393 行亦及合同成立与拒签后果，但**不含**「违约责任而非缔约过失」的区分，故本条的判别性来源是谢勇草稿 | 改署**谢勇**；第十六节解散后并入**第九节**（现第 127 行） |
| 教训原第 181 行（第十六节） | （不存在的书名，初稿误署张旗等） | `09-lishu.md` 第 248 行（「招标程序瑕疵未对中标结果造成实质性影响且可补救的，不导致中标及施工合同无效，该『实质性影响』由主张无效一方举证」）、第 24 行（同题检查项）；谢勇草稿无此项 | 改署**李舒**；第十六节解散后并入**第十节**（现第 136 行） |
| 教训原第 180 行（第十六节） | （不存在的书名，初稿误署张旗等） | `12-epc-run.md` 第 142 行（「联合体对外承担连带责任，内部约定只在成员之间生效，不对外免除任何成员依法对招标人的连带责任」）、第 134 行、第 223 行、第 237 行（误判教训段）；该草稿即王洋等《工程总承包项目运作实务》的来源稿（见第三节表⑥）。**更正**：初稿记「只见于 `13-epc-dispute.md` 第 142 行与 `12-epc-run.md` 第 142 行」，实测 `13-epc-dispute.md` 第 142 行为定额套用争议（不涉联合体连带），该草稿全文亦无「招标人」字样，故张旗等草稿**不是**本条来源 | 改署**王洋等**；归入 **EPC 相关节＝第十三节**（现第 168 行），非第十二节 |
| 教训原第 182 行（第十六节） | （不存在的书名，初稿误署张旗等） | `09-lishu.md` 第 245 行（「邀请招标是法定招标方式之一，必须招标项目以邀标确定承包人即视为履行了招投标程序；未备案属行政管理性规定，不影响中标与合同效力」）、第 19 行（同题检查项）；`13-epc-dispute.md`、`12-epc-run.md`、`13-xieyong.md` 三稿均无「邀请招标」字样 | 改署**李舒**；并入**第十节**（现第 137 行） |
| 教训原第 183 行（第十六节） | （不存在的书名，初稿误署张旗等） | 三段实测：`09-lishu.md` 第 343 行（「施工合同无效不影响结算协议的独立效力」）、`08-zhushi.md` 第 186、299、320 行（「结算协议具有独立性，施工合同的效力不影响结算协议的效力」）、`10-meijun.md` 第 265 行（「结算协议独立于施工合同，施工合同无效不影响结算协议效力」——三段中与条目字面最近） | 归入**第十节**（李舒，现第 138 行），并在条目末尾**注明**「同题亦见史智军《建设工程合同注释书》与梅俊《施工方追索建设工程款裁判规则与实务指引》」（未拆条，以保总数 46 条不变） |

> **本轮裁定与执行（2026-09-16，取代原「遗留未结」登记）**：复核方裁定**解散第十六节**（其节标题所指书名在草稿、碎片与书单中均不存在），该节 5 条按上表逐条改署并重新归属：179→第九节（谢勇）、180→第十三节（王洋等）、181→第十节（李舒）、182→第十节（李舒）、183→第十节（李舒，附同题注）。执行结果照实测登记——`shared/cases/practice-lessons.md` 现存**八至十五共 8 节**，节标题中不再出现该虚构书名（全文命中 0），教训总数 **46 条不变**（八 6、九 5、十 8、十一 7、十二 7、十三 7、十四 2、十五 4）。谢勇书因在文中无独立节，其条目落在第九节内（第 126、127 行），故第九节含史智军 3 条＋谢勇 2 条（第 126、127 行），第十节含李舒 8 条。

- 落盘内容均为**要点转述与方法提炼**；九份草稿中照引的法条条号、司法解释编号、地方文件号、案号与具体金额、比例、期限门槛一律**未移植**，涉及门槛之处改写为规则口径。
- **本表两处书名初稿笔误，本批按草稿与书单更正**：第⑤行《工程总承包（EPC／DB）争议解决实战攻略》漏「实战」二字；第⑧行应为陈津生《建设工程总承包项目招标与投标操作实务》（`12-epc-bid.md` 源自该书，草稿分片 `epc-bid-p1/p3/p6/p7.md` 自带来源行），初稿书名有误。
- **「未复制原文整句」的实测范围与口径（承接 E11 的更正，不重复全称断言）**：本批做了两级实测——① 抽样：4 条草稿长句在工作区命中数为 0；② 逐条：把 `shared/cases/practice-lessons.md` 第 112–180 行的**全部 46 条新增条目**（去掉条目末尾出处署名后）与九份草稿逐条求**最长连续子串**（方法见第四节之四之二）。复跑实测：**最大连续重合 21 字，达到或超过 20 字者 7 条**，7 条的重合片段全部是**草稿文件内残留的书名串**（《工程总承包（EPC／DB）争议解决实战攻略》），非正文搬运；**除书名串外的正文最大连续重合 19 字，达到或超过 20 字者 0 条**。故本批的准确表述是：**本批新增教训的正文与九份草稿的最长连续重合在阈值以下（实测最大值 19 字）**，而不是「不存在任何整句复制」；也不得据此断言工作区绝对不含草稿或书中的任何片段。
- 草稿去向：九份合并稿（`.work/distill-outbox/07-guandian.md`、`08-zhushi.md`、`09-lishu.md`、`10-meijun.md`、`13-epc-dispute.md`、`12-epc-run.md`、`11-epc-safe.md`、`12-epc-bid.md`、`13-xieyong.md`）为**只读输入**，本批未修改。

### 二、去重口径（已覆盖项不收录）

- **检查项去重**：新增项只收既有清单未覆盖事项。核对范围为 `industries/construction/checklists/` 五个清单（施工合同、竣工结算与安全生产、招投标合规、签证索赔时效表、农民工工资）与 `shared/checklists/` 的合同红线清单、招投标通用清单。
- **被判定为已有条目覆盖、故本批未收录的典型项及覆盖位置**：索赔与变更签证的时限与失权（`claim-limitation.md` 六类期限表第 1–4 项已有）、优先受偿权行使期限与起算（`claim-limitation.md` 第 7 项与结算清单既有条目已有）、竣工验收与备案程序（结算清单「竣工验收与备案」节已有）、质保金扣留比例与返还（施工合同清单与结算清单已有）、资质与安全生产许可证有效期（施工合同清单第 1 条、招投标合规清单第 5 条已有）、工程款债权转让与以房抵顶（结算清单已有）、付款时间无约定或约定不明时的顺序推定（`claim-limitation.md` 期限外说明已有）、工程总承包的发包范围边界与调价公式（施工合同清单第 14 条已有该指针，本批不重复出条，只把边界细节并入新增条）。
- **误判教训去重**：`shared/cases/practice-lessons.md` 第六、七节已收朱树英与史智军两书的建工教训各 10 条；本批八、九节续收**未收录事项**共 11 条（第八节朱树英 6 条；第九节 5 条＝史智军 3 条＋谢勇 2 条——第 126 行行内署名本轮订正后该条归属谢勇，与第 127 行列同节；谢勇书在本批无独立节，其条目落在第九节内，来源经复核改署，见第一节第⑨行署名更正及表下注），第六、七节已覆盖的联合体实际履行、发包人主体识别、优先受偿权主体与放弃效力、审计约定推断、开工日期认定、保修期与缺陷责任期区分等，本批不再重复出条。第十至十五节为新增六书（李舒、梅俊、张旗等、王洋等、李宏远等、陈津生等），与既有第一至第九节逐条比对无同事项重复；183 条同题亦见史智军、梅俊两书草稿，为守「不重复出条」未拆条，只在条目末尾注明。

### 三、本轮落盘改动（4 个生产文件）

| # | 文件 | 落盘内容 | 性质 |
| --- | --- | --- | --- |
| ① | `industries/construction/checklists/construction-contract.md` | 文末新增 12 条（工程总承包价格形式与包干边界、发包人应承担的风险项、设计采购施工三部分的计价与责任边界、发包范围与调价公式、设计与施工资质及设计牵头的管控安排、设备材料采购与进场验收责任、工期变更与安全费用同步、联合体与项目部对外行为约束、税务条款与合同整体性、进度计划与工期风险、调试试运行与性能考核、合同价款与竣工结算费用词区分），位于第 25–36 行；每条冠「（未覆盖项）」；既有 10 条与免责声明**一字未动** | 生产文件；纯新增 |
| ② | `industries/construction/checklists/completion-settlement-safety.md` | 本批新增 **13 条**（第 14–22、32–34、44 行；第 43 行为 E11 既有条目移位而来，不计入本批），按节为：「结算与优先受偿」节 9 条（分项计价与固定总价部分不再重算、质保金与工程款分项处理、合同无效或解除下质量责任与保修义务分项认定、优先受偿权适用的法律口径与付款安排变更、行政审计约定的主体与范围、验收适用规则按工程类型、未交接未完工的原因归属举证、结算报告后再确认工程量的调整、按比例折算前的完工比例书面确认）；「竣工验收与备案」节 3 条（第 32–34 行：专业工程与专项验收齐备、交付使用与竣工日期、总承包两项质量责任）；「安全生产」节 1 条（总承包模式下安全生产责任分配）。各条冠「（未覆盖项）」；既有条目**一字未动** | 生产文件；纯新增 |
| ③ | `industries/construction/checklists/bidding-compliance.md` | 文末新增 8 条（工程总承包方式的适用类型、发包时点、资质组合、发包人要求与功能描述、价格形式与风险分担、限价与评标办法配套、联合体投标核查、特定专业工程的前置手续与法定门槛），每条冠「（未覆盖项）」；既有 8 条与免责声明**一字未动** | 生产文件；纯新增 |
| ④ | `shared/cases/practice-lessons.md` | 第七节之后新增第八至十五节共 8 节、合计 **46 条**九书误判教训（第 112–180 行），每条以书名收尾；原第十六节（其节标题所指书名不存在）已按本轮裁定解散，5 条按实际来源改署并并入第九、十、十三节（见第一节表下表注）；既有第一至第七节、头部版本与取材口径**一字未动**；文末免责声明位置未变 | 生产文件；纯追加 ＋ 归属更正 |

- 第 5 个文件 `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` 为本附录，属验证记录，不计入生产文件。
- 生产文件改动数：**4 个**（①②③④），全部落在简报允许清单内。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件与九份草稿**只读未改**；`shared/templates/legal-opinion.md` 未改；`industries/construction/` 下的 `regulations.md`、`SKILL.md`、`claim-limitation.md`、`migrant-wage.md` 未改；`shared/checklists/contract-redlines.md`、`employment-redlines.md`、`evidence-preservation.md`、`shared/checklists/bidding-compliance.md` 未改；F 盘其他项目未改。
- **未提交、未推送**，改动全部留在工作区。

### 四、验收命令与真实输出（逐条实跑，2026-09-16）

> 为守「不搬原文整句」口径，验收四之二的样本正文**不在本文件内引用**，只登记样本在草稿中已确认存在、以及在工作区（排除 `.work/`、`.git/`）中的命中数。

**验收 1（三份清单新增条数合规 12／10／8，均为未覆盖项）**

```powershell
Select-String -Path 'industries/construction/checklists/construction-contract.md' -Pattern '（未覆盖项）'
Select-String -Path 'industries/construction/checklists/completion-settlement-safety.md' -Pattern '（未覆盖项）'
Select-String -Path 'industries/construction/checklists/bidding-compliance.md' -Pattern '（未覆盖项）'
```

真实输出：

```
construction-contract.md            未覆盖项合计 22 条，行号 15–36
  （E11 既有 10 条＝第 15–24 行；本批新增 12 条＝第 25–36 行）
completion-settlement-safety.md     未覆盖项合计 20 条，行号 11–22、29–34、43–44
  （E11 既有 7 条＝第 11–13、29–31、43 行；本批新增 13 条＝第 14–22、32–34、44 行）
bidding-compliance.md               未覆盖项合计 8 条，行号 11–18（本批全部为新增）
```

`git diff --numstat` 逐文件新增行数：

```
9	0	industries/construction/checklists/bidding-compliance.md
13	0	industries/construction/checklists/completion-settlement-safety.md
12	0	industries/construction/checklists/construction-contract.md
70	0	shared/cases/practice-lessons.md
```

结论：**通过**。bidding-compliance.md 新增 9 行＝8 条检查项 ＋ 1 个段末空行，对应 8 条；completion-settlement-safety.md 新增 13 行**全部为检查项行**，对应 13 条（本表初稿按「10 条 ＋ 3 个节间空行」记，属误记，已按实测改为 13 条）；construction-contract.md 新增 12 行正好对应 12 条；practice-lessons.md 新增 70 行＝46 条条目 ＋ 8 个节标题 ＋ 16 个空行（本轮解散第十六节并重排归属后复测；上轮记的 73 行＝9 个节标题版，已作废）。三份文件的既有条目与免责声明行号不变，四个生产文件 `git diff --numstat` 删除行数均为 0（practice-lessons.md 本轮为条目移位与改署，仍无删除行——即净新增 70 行全部落在文件末尾的第八至十五节）。

未覆盖性抽查（3 条，人工判定；检索池为三份本轮改动清单 ＋ `claim-limitation.md`）：

```
抽查「调试、试运行、性能考核与验收的关系」：
  在三份改动清单与 claim-limitation.md 内检索 '试运行|性能考核|试车|性能达标'
  → 只命中本批新增两项：construction-contract.md 第 35 行、completion-settlement-safety.md 第 34 行；
  既有条目无一涉及性能考核与验收条件的关系
  → 判为未覆盖
抽查「设计、采购、施工三部分税务条款分别约定」：
  在三份改动清单与 claim-limitation.md 内检索 '税'
  → 只命中本批新增条 construction-contract.md 第 33 行；其余各清单既有条目 0 处
  → 判为未覆盖
抽查「发包范围边界与调价公式到可计算程度」：
  在三份改动清单与 claim-limitation.md 内检索 '调价公式'
  → 命中本批新增条 construction-contract.md 第 28 行，以及施工合同清单既有第 14 行（只给出该指针、
  未展开可计算程度）
  → 判为未覆盖
```

**验收 2（教训追加不超上限、每条有出处书名）**

```powershell
Select-String -Path 'shared/cases/practice-lessons.md' -Pattern '朱树英|史智军|唐青林|梅俊|谢勇|王洋|李宏远|陈津生|张旗'
(Select-String -Path 'shared/cases/practice-lessons.md' -Pattern '^- 误以为').Count
(Select-String -Path 'shared/cases/practice-lessons.md' -Pattern '^- 误把').Count
(Select-String -Path 'shared/cases/practice-lessons.md' -Pattern '^- ').Count
```

真实输出（2026-09-16 复跑）：

```
出处词命中行数: 68    行号范围 86–180
  （86–97 为既有第六节 11 行；99–110 为既有第七节 11 行；112–180 为本批第八至十五节 46 行，含 8 个节标题）
  （上轮记 70，本轮复跑为 68，差额已对齐：解散第十六节去掉 5 行命中＝该节标题 1 行 ＋ 以「张旗等」收尾的
  条目 4 行（原第 181 行以「李舒」收尾，本就不在该检索式内，故该节 5 条只贡献 4 行）；本轮新增命中 3 行＝
  第 127 行（「谢勇」）、第 138 行（条目末尾同题注含「史智军」「梅俊」）、第 168 行（「王洋等」）；
  70 － 5 ＋ 3 ＝ 68。上轮「首轮 71 与复跑 70 差 1 行未定位」的悬记随本次重算一并作废。
  本轮第 126 行行内署名由「史智军」订正为「谢勇」，两词均在本检索式内，该行原已计入命中，故命中行数仍为 68。）
'^- 误以为' 条目数: 123
'^- 误把'   条目数: 3
'^- '       行合计: 133 ＝ 条目 126 ＋ 文件头 7 行说明（第 3–9 行：版本、知识截至日期、用途、取材口径、
            性质声明、与锚点案例库的区分、过期预警）
本批新增条目: 46    E11 基线条目: 80    80 ＋ 46 ＝ 126 ＝ 一级条目行合计
```

**「条目合计 133」的更正（本轮重算发现）**：上轮把 `^- ` 的行合计 133 直接记为「条目合计」，未扣除文件头第 3–9 行的 7 行说明，故与「80 ＋ 46 ＝ 126」自体不一致。实测：**条目 126 条**（＝各节条目数之和，见下），`^- ` 行合计 133 行＝126 条 ＋ 头部 7 行。上轮末句「三个前缀模式（123 ＋ 3）与条目总数 133 现已互相对齐」不成立（123 ＋ 3 ＝ 126，恰为条目数），已按实测改写。

各节条目数（脚本实测，本轮归属更正与第 126 行署名订正后）：

```
第六节 朱树英 10   第七节 史智军 10                    （E11 既有，未动；两节合计 20）
第八节 朱树英 6    第九节 史智军 3 ＋ 谢勇 2 ＝ 5       第十节 李舒 5 ＋ 3 ＝ 8
第十一节 梅俊 7    第十二节 张旗等 7   第十三节 王洋等 6 ＋ 1 ＝ 7
第十四节 李宏远等 2  第十五节 陈津生等 4
（本批新增八节，合计 46；第十六节已解散，其 5 条并入第九、十、十三节）
```

**本批计数漏计原因（如实登记）**：本表初稿的 43 条系用 `^- 误以为` 单一前缀检索得出，漏掉了 3 条以「误把」开头的条目（本轮重排后为 `shared/cases/practice-lessons.md` 第 136、165、179 行）；同时初稿的分节计数把第十三节记为 5（本轮实测 6，并入第 180 条后为 7）、第十五节记为 3（实测 4）、第十六节记为 4（实测 5，该节现已解散）。

结论：**通过**。追加 46 条，超出简报字面「≤30 条」限值；该项已由复核方裁定接受（见本节末「上限口径说明」与第六节「本轮裁决登记」②），每条行内以书名收尾。

上限口径说明：本批九书共 771＋803＋561＋366＋176＋231＋319＋429＋302 ＝ 3958 条草稿条目，简报给的「≤30 条」为**每条书的建议采集量级**而非九书合计数；本批按「一书一节、每节只收该书与既有条目均未覆盖的事项」采集，本轮归属更正后实测 46 条落在**八节**内（谢勇书 2 条并入第九节），单节最多 8 条（第十节李舒），单书最多 8 条（李舒）。超出字面限值的处理口径与教训同批一致，已由复核方裁定接受，理由见第六节「本轮裁决登记」②。

**验收 3（E12 附录在验证记录，含九书署名）**

```powershell
Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'E12'
```

真实输出（判定依据以附录标题行存在为准）：

```
本批落盘前：全文未出现 'E12' 字样。
本批落盘后： ## 附录（2026-09-16）：E12 建工九书蒸馏落盘（施工合同清单 / 结算验收与安全清单 / 招投标合规清单 / 误判教训续编 / 验证记录）
其余命中来自本附录自身（署名表、验收小节与命令行）。自指计数不作为独立证据，
复跑时总数随本附录条目增减而变，故不把总数作为验收判据。
```

九书署名见本附录第一节表（九行，逐行给出书名、作者与落盘位置）。

**验收 4（无原文整句复制；无新增法条编号与具体数字）**

之四之一 —— 抽样样本在工作区的命中数（样本先在草稿中确认存在，再在工作区排除 `.work/`、`.git/`、`.workbuddy-ai/` 后检索）：

```
S1 src=12-epc-bid.md 检查项段     草稿命中=1   len=22   工作区 hits=0   命中文件=[]
S2 src=10-meijun.md 标准立场段    草稿命中=1   len=25   工作区 hits=0   命中文件=[]
S3 src=13-xieyong.md 误判教训段   草稿命中=1   len=21   工作区 hits=0   命中文件=[]
S4 src=11-epc-safe.md 检查项段    草稿命中=1   len=20   工作区 hits=0   命中文件=[]
```

之四之二 —— 逐条句级重合自检（判据：被测新增条目去掉末尾出处署名后的正文，与九份草稿**两侧同口径**处理——目标侧去空白与标点，草稿侧同时剔除标题块、来源／覆盖说明与统计表格（后者含书名串）——求**连续**相同字符串的最大长度；达到或超过 20 字判为需要改写）：

```
本批被测条目：shared/cases/practice-lessons.md 第 112–180 行的全部 46 条新增教训（本轮归属更正后行号重排，条目正文未改；仅第 138 行条目在末尾署名括号内追加同题注，按本判据「先去掉条目末尾的出处署名括号」在比对前即被剔除，故下述数值不变，本轮未重跑）
复跑实测：
  两侧同口径（去出处署名 × 去草稿标题块与来源说明与表格）：
    最大值 21 字；达到或超过 20 字者 7 条
    7 条的 21 字重合片段全部是草稿文件内残留的书名串「张旗等《工程总承包（EPC／DB）争议解决实战攻略》」
    （非正文搬运）
  除书名串外的正文口径：
    最大值 19 字；达到或超过 20 字者 0 条
    最大值 19 字出现在第 136 行，片段为「合同无效该实质性影响由主张无效一方举证」（与草稿 09-lishu.md 重合）
三条「误把」句式逐条实测（去署名口径；行号为本轮重排后）：
  第 165 行（上轮已按裁决整句改写，本轮未动）: 8 字   src=12-epc-run.md  片段=发包人要求或工程
  第 179 行（未改，原样复测）                : 10 字  src=12-epc-bid.md  片段=招标人代表不计入专家
  第 136 行（未改，原样复测）                : 19 字  src=09-lishu.md    片段=合同无效该实质性影响由主张无效一方举证
  → 三条均低于 20 字阈值，无需再改写
第 165 行（上轮行号 161）改写前基线（留证）：去署名口径 50 字，片段起始于「误把设计优化当变更报费用……」，
  来源 12-epc-run.md 第 233 行（该行去掉引号与标点后为 49 字连续串）
首轮脚本（`.work/tmp-final-check.ps1`，第一轮执行方所留）在上轮改写后原地复跑，记录为：
  被测生产文件新增正文行 = 79（上轮值；该脚本为临时工具、已删，本轮未复跑，按记录照录）
  全局最大 LCS = 19 字
  达阈值（>=20）行数 = 0
  → 与本节「除书名串外的正文口径：最大值 19 字、达阈值 0 条」一致，两条独立路径互证。
  据此更正本表初稿「含署名口径最大值 24 字、达阈值 16 条」的记法：该 16 条与 24 字来自把
  草稿侧书名串一并计入的算法，不属正文搬运，故正文口径的准确实测为 19 字、0 条。
口径：先去掉条目末尾的出处署名括号，再归一化空白与标点后求连续相同字符串的最大长度；
      最长公共子串与最长公共子序列是两个不同量，本判据只认连续子串；子序列数散布在
      草稿全文范围内，不构成「一句原文被搬走」的证据，故不作为判据（承接 E11 第五节之四的方法学更正）。
      书名串不构成正文搬运，但草稿侧未剔除时会使计数虚高，故本次改为两侧同口径复测。
      复算脚本属临时工具，为守本附录「样本与脚本不写入仓库」的口径已在收尾时删除；
      本节数值可用同口径重算复核（判据与归一化规则已在上文写明）。
```

之四之三 —— 新增行法条编号与具体数字（对 `git diff -U0` 的新增行检索）：

```
新增行总数: 104（四个生产文件 `git diff --numstat` 新增行合计 9 ＋ 13 ＋ 12 ＋ 70；不含本附录自身的行。
上轮记 107 含 practice-lessons.md 的 73 行，本轮解散第十六节后该文件为 70 行）
条号/期限类命中（第×条|第×款|第×项|×天|×个月|×日|法释|解释[一二]|司法解释）: 0
阿拉伯数字与百分号命中（[0-9]|%|百分之）: 0
金额单位命中（万元|亿元|元整）: 0
```

- 三处计数均为 0；本批新增文字中不含「第×条／第×款／法释」类条号引用，也不含阿拉伯数字、金额、比例与期限门槛。

改动范围与基线：

```
git diff --name-only →
  docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
  industries/construction/checklists/bidding-compliance.md
  industries/construction/checklists/completion-settlement-safety.md
  industries/construction/checklists/construction-contract.md
  shared/cases/practice-lessons.md
git status --porcelain →
   M docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
   M industries/construction/checklists/bidding-compliance.md
   M industries/construction/checklists/completion-settlement-safety.md
   M industries/construction/checklists/construction-contract.md
   M shared/cases/practice-lessons.md
  ?? .workbuddy-ai/   （任务开始前既有，未动）
git rev-parse --short HEAD → 5b30c1e（会话内未提交，基线未变）
```

结论：**通过**。改动集中在允许清单的 5 个文件内，基线未变，未提交、未推送。

- **边界**：以上为**静态检查**（关键短语检索、条目计数、行数比对与最长公共子串计算），不是全文查重；抽样为抽样，不能据此断言「工作区绝对不含草稿或书中的任何片段」。样本清单与复算脚本本身未写入仓库，以免样本自身构成命中。

### 五、本附录边界

- 本附录记录的是**文本与结构落盘 + 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（新增检查项是否会被真的加载、教训选编是否会被真的对照使用，均需宿主实测）。
- **未做**：九书的全部章节通读（本批只落九份合并稿已覆盖的部分）；书中内容在中国法下的正确性复核（本批只做口径提炼与去重，未对实体结论作评价）；条号、金额与门槛数字的联网核验（按硬口径不移植，故无核验对象）；九书版本更替后的内容变化核对；「标准立场」类草稿条目的单独落盘（既有清单与红线清单已采用「检查项＋标准立场」两段式的位置有限，本批不为此新开节）。
- **本批上限口径已获裁定**：简报所给「教训追加 ≤30 条」本批实测为 **46 条**（第二节初稿曾按 43 条记，漏计原因见第六节），结算清单新增 13 条亦超过三节各自 ≤10 条的合计口径。两项均未自行删减，理由与建议见第四节验收 2 的「上限口径说明」，并已由复核方裁定接受（见第六节「本轮裁决登记」）。
- 草稿只读未改；本轮改动全部留在工作区，未提交、未推送。

### 六、本轮裁决登记与复核更正汇总（2026-09-16）

**（一）两处裁决登记**

① **裁决一：结算清单 13 条接受**。结算清单（`industries/construction/checklists/completion-settlement-safety.md`）本批实测新增 13 条，超出简报「三节新增 ≤10 条」的字面限值；复核方裁定**接受 13 条**，上限按教训条目同例放宽，本附录如实登记——第二节验收 1 的计数与结论、第三节表②、第四节验收 1 均已按实测 13 条改写，初稿「10 条 ＋ 3 个节间空行」的记法作废。

② **裁决二：教训总数按 46 条接受**。教训（`shared/cases/practice-lessons.md`）本批实测新增 46 条，超出简报「≤30 条」的字面限值；复核方裁定**按 46 条接受**，理由是初稿的 43 条漏计了 3 条「误把」句式条目（漏计原因见下（二））。第三节表④、第四节验收 2、第五节边界均按 46 条改写。

**（二）本批两处计数错漏的登记与更正**

- **漏计 3 条「误把」条目**：初稿用 `^- 误以为` 单一前缀检索计数，得 123 条、本批 43 条；该模式**不匹配**以「误把」开头的 3 条（本轮重排后为 `shared/cases/practice-lessons.md` 第 136、165、179 行）。补上 `^- 误把` 3 条后为 126 条，与各节条目数之和及「80 ＋ 46」完全对齐（`^- ` 行合计 133 另含文件头 7 行说明，见第四节验收 2 的更正），故本批新增为 46 条、基线 80 条。
- **分节计数三处误记**：初稿将第十三节记为 5、第十五节记为 3、第十六节记为 4；本轮归属更正后实测为第八节 6、第九节 5、第十节 8、第十一节 7、第十二节 7、第十三节 7、第十四节 2、第十五节 4（第十六节已解散）。「单书最多 7 条」的表述亦不准（实测单节最多 8 条＝第十节李舒）。均已按脚本实测改正。
- **结算清单新增条数误记为 10**：`git diff --numstat` 该文件新增 13 行且**全部为检查项行**（无节间空行），故为 13 条，已改正。

**（三）本轮其它核实与更正**

- **第⑨行署名更正**（误署作者＋把分篇号当书名）：改署谢勇《建设工程施工合同案件裁判规则解析》。依据与逐条核实见第一节表下注。
- **第⑤、⑧行书名更正**：⑤ 补「实战」二字；⑧ 改为陈津生《建设工程总承包项目招标与投标操作实务》。
- **正文出处按实际来源改署与重新归属（本轮执行）**：教训第 179 行改署谢勇、并入第九节（现第 127 行）；第 181 行改署李舒、并入第十节（现第 136 行）；第 182 行改署李舒、并入第十节（现第 137 行）；第 183 行并入第十节（现第 138 行）并在条目末尾注明同题亦见史智军、梅俊两书草稿；第 180 行改署王洋等、并入第十三节（现第 168 行）。结算清单第 33 条的来源同属谢勇，但清单为生产文件、按本批硬口径不逐条署名，故只在第一节表与本节登记，**未在清单正文加署名**。
- **第 126 行的行内署名已订正（承接上轮「未改」登记）**：上轮本项曾记「教训第 126 行由史智军改署谢勇」，当时**与文件实况不符**——该条行内署名当时仍为「（史智军《建设工程合同注释书》）」，上轮复核改为如实登记「未改」。本轮据同一实测来源（`13-xieyong.md` 第 72–73、295 行；`08-zhushi.md` 无对应项）**已执行订正**：该条行内署名现为「（谢勇《建设工程施工合同案件裁判规则解析》）」，结算清单第 33 条（同源）仍按硬口径不在清单正文署名。上轮与本节更正登记、第一节表下注与表内本行处置栏均已同步改写。
- **教训第 165 行（上轮行号 161）整句改写**：改写前与草稿 `12-epc-run.md` 第 233 行（去引号与标点后 49 字连续串）重合 50 字（去署名口径），已整句改写为「误把设计优化当作变更去报费用——那属于承包人自己的管理范畴……」，复跑（去署名口径）降至 **8 字**。三条「误把」句式已全部纳入复跑，另两条为 10 字与 19 字，均低于阈值（见第四节之四之二）。
- **原登记的不自洽已按本轮裁定处理**：第十六节（其节标题所指书在草稿、碎片与书单中均不存在）已解散，该书名在 `shared/cases/practice-lessons.md` 中命中数为 **0**；该节 5 条按实际来源改署并归入第九、十、十三节，教训总数保持 **46 条**，八个分节计数为 6／5／8／7／7／7／2／4。**残留**：第九节内含谢勇书 2 条（第 126、127 行，前者行内署名本轮已订正；该节标题所指为史智军书），系谢勇书在本批无独立节所致；谢勇书落盘位置已在第一节表写明。
- **未做**：本节只登记本轮修复所涉事项；不涉及对实体法律结论的评价，也未重做九书的通读核对。

**（四）本轮裁定执行登记（第十六节解散与 5 条重新归属）**

裁定内容：解散 `shared/cases/practice-lessons.md` 第十六节（其节标题所指书名在草稿、碎片与书单中均不存在）；该节 5 条按实际来源改署并重新归属；教训总数保持 46 条；同步更新本附录第一节署名表与各处计数。

| 条目 | 改后署名 | 归入 | 现行号 | 核实证据（草稿） |
| --- | --- | --- | --- | --- |
| 原第 179 行 | 谢勇《建设工程施工合同案件裁判规则解析》 | 第九节 | 127 | `13-xieyong.md` 第 17、132、247–248 行 |
| 原第 180 行 | 王洋等《工程总承包项目运作实务》 | 第十三节（EPC 相关节） | 168 | `12-epc-run.md` 第 142、134、223、237 行 |
| 原第 181 行 | 李舒《建设工程法律实务》 | 第十节 | 136 | `09-lishu.md` 第 248、24 行 |
| 原第 182 行 | 李舒《建设工程法律实务》 | 第十节 | 137 | `09-lishu.md` 第 245、19 行 |
| 原第 183 行 | 李舒《建设工程法律实务》（末尾注明同题亦见史智军、梅俊两书草稿） | 第十节 | 138 | `09-lishu.md` 第 343 行、`08-zhushi.md` 第 186／299／320 行、`10-meijun.md` 第 265 行 |

实测证据（2026-09-16 实跑）：

```
Select-String -Path 'shared/cases/practice-lessons.md' -Pattern '项目招投标与合同管理' → 0 命中
  （检索式取原第十六节节标题中那本书名的判别性片段，以免把该不存在书名再写进本附录）
Select-String -Path 'shared/cases/practice-lessons.md' -Pattern '十六、'              → 0 命中
分节条目数（脚本实测）: 八 6  九 5  十 8  十一 7  十二 7  十三 7  十四 2  十五 4  → 合计 46
'^- ' 行合计 133 ＝ 条目 126（E11 基线 80 ＋ 本批 46）＋ 文件头 7 行说明（第 3–9 行）
git diff --numstat -- shared/cases/practice-lessons.md → 70	0（纯新增，无删除行）
git rev-parse --short HEAD → 5b30c1e（未提交、未推送）
```

- 5 条条目正文**逐字未改**（仅第 183 行在末尾署名括号内追加同题注），故第四节之四之二的句级重合实测值不受本轮影响、未重跑。
- **校正一处上轮记录**：原「遗留未结」曾称 `13-epc-dispute.md` 第 142 行含第 180 行的对应内容；本轮实测该行为定额套用争议（不涉联合体连带责任），该草稿全文亦无「招标人」字样，故第 180 行的来源只记 `12-epc-run.md`（王洋等书），不记张旗等书。

---

## 附录（2026-09-16）：E13 争议六书蒸馏落盘（争议岗庭审与举证实务段 / 证据清单举证与质证节 / 误判教训续编 / 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与 D1–D8、E9、E10、E11、E12 各附录的任何结论。
> 蒸馏口径（硬）：书籍内容只提炼方法经验与判断口径；**不搬原文整句**；**不写任何法条编号与具体数字**（六份草稿中照引的条号、司法解释编号、案号、金额、比例与期限门槛一律改写为规则口径）；每条与既有内容逐条比对，已覆盖的不重复收录；**生产文件不逐条署名**（教训选编按既有体例以书名收尾），署名统一落在本附录。

### 一、来源署名（六书）

| # | 书名 | 作者／来源 | 采用的什么（中文重写转述，未搬原句） | 落盘位置 |
| --- | --- | --- | --- | --- |
| ① | 《开庭：法官思维与庭审实务》 | 上海市一中院 | 出庭准备与授权手续清点、二审举证与逾期举证、二审质证与争议焦点异议 | `roles/dispute-counsel/SKILL.md` 第 4.7 段；`shared/cases/practice-lessons.md` 第十六节 |
| ② | 《诉讼有方》 | （草稿未标作者） | 阅卷与出意见的先后、焦点核对、文书与证据目录的起草顺序、调解与庭后言行的自认风险 | `shared/cases/practice-lessons.md` 第十七节 |
| ③ | 《民事诉讼证据运用与实务技巧》 | 王新平 | 质证的两层结构与真实性表态、质证与辩论的分工、利害关系的归属、公证证言与出庭义务 | `shared/checklists/evidence-preservation.md` G 节；`shared/cases/practice-lessons.md` 第十八节 |
| ④ | 《证据法学：原则、规则、案例》 | 易延友 | 关联性与可采性的区分、品格证据的排除理由、作证资格与可信度、保管链条的弹性 | `shared/cases/practice-lessons.md` 第十九节 |
| ⑤ | 《诉讼可视化》 | 蒋勇 | 时间轴的纵向对比、线条含义唯一、无关信息删减、图表立场与篡改事实之别 | `shared/cases/practice-lessons.md` 第二十节 |
| ⑥ | 《合同纠纷证明责任研究》 | 王国征等 | 「谁主张谁举证」的定位、抗辩与否认的分野、证明责任不随行为转移、缺席不等于自认 | `roles/dispute-counsel/SKILL.md` 第 4.7 段；`shared/checklists/evidence-preservation.md` G 节；`shared/cases/practice-lessons.md` 第二十一节 |

> **书目归属依六份草稿自述**：`16-kaiting.md` 第 8 行（仅 P1 自带书目「上海市一中院《开庭：法官思维与庭审实务》」，P2–P4 未标注，本附录按该稿自述署名）、`18-wangxinping.md` 第 6 行（书目依 `.work/weread-booklist.md` 补注为王新平《民事诉讼证据运用与实务技巧》）、`19-yiyanyou.md` 第 1 行、`20-keshihua.md` 第 1 行、`21-zhengmingzeren.md` 第 8 行（仅 P1 自带书目「王国征等《合同纠纷证明责任研究》」）。
> `17-susongyoufang.md` 第 13 行自述：四片均未标注所出书目、作者与章次，「诉讼有方」仅由碎片文件名与 P2 标题体现。故该节**只署书名、未代拟作者**，属如实登记而非遗漏。

### 二、去重口径（已覆盖项不收录）

- **比对池**：`roles/dispute-counsel/SKILL.md` 全文（第 0、1、2、3、4、4.6、5、6、7 段）；`shared/checklists/evidence-preservation.md` 的 A–F 节；`shared/cases/practice-lessons.md` 的第一至十五节。另核对了 `shared/checklists/contract-redlines.md`、`shared/checklists/employment-redlines.md`、`shared/templates/referral-form.md` 中涉证据与举证责任的条目。
- **被判定为已有条目覆盖、故本批未收录的典型项及覆盖位置**：原件与原始载体优先（清单 A2 已有）、保留上下文与完整性（A3 已有）、合法取得与非法取证（A5 已有）、电子数据导出原始数据与校验值（D2、D5 已有）、金额重大时优先公证或申请鉴定（D6 已有）、时效与程序期限登记（E 节已有）、取证方式可能违法的自查（争议岗第 2 段已有）、只谈实体不谈证据与举证责任分配的失败模式（第 2 段已有）、管辖异议与保全等程序性风险（第 2 段与第 6 段已有）、未签书面劳动合同的举证责任与时效衔接（`employment-redlines.md` L8 已有）、鉴定材料未经质证不得照采（教训第九节建工条目已有，与本事不同类）。
- **边界邻近项的如实登记（判为不重复，口径不同）**：`evidence-preservation.md` 既有「无法确认」出现在文件头第 5 行说明中，指**法条现行有效性**无法确认，与本批 G 节「对证据真实性表态为无法确认」不同义；`practice-lessons.md` 既有「质证」出现在第九节建工条目，指**鉴定材料未经质证**，与本批的质证阶段手法不同事。
- 三个生产文件的既有文字**一字未动**（`git diff --numstat` 删除行均为 0），改动全部为新增行。

### 三、本轮落盘改动（3 个生产文件 ＋ 本附录）

| # | 文件 | 落盘内容 | 性质 |
| --- | --- | --- | --- |
| ① | `roles/dispute-counsel/SKILL.md` | 第 4.6 段之后、第 5 段之前新增「第 4.7 段 · 庭审与举证实务（本条只加手法，不改第 4 段次序）」，**15 条**手法条目（出庭准备清单化、按争点预判发问、举证责任先定性质、否认与抗辩分开、本证先达标、质证的两层结构、异议要具体、逾期举证按次序判断、二审举证围绕上诉请求、争议焦点当场核对、法庭调查与辩论分工、发问的目的、调解让步与自认分开、可视化呈现、自认风险提示）；新增第 82–99 行，第 5 段标题顺延至第 100 行 | 生产文件；纯新增 |
| ② | `shared/checklists/evidence-preservation.md` | 文末新增「## G. 举证与质证」节，**10 条**检查项（质证两层结构、真实性三种表态、复印件附条件表态、提交原件确有困难的核对、即时通讯记录截屏要素、聊天对象身份固定、单位证明材料形式要件、证人出庭书面申请、举证期限与逾期后果、鉴定意见与评估报告提交前核对）；新增第 63–74 行（节标题第 63 行，条目第 65–74 行） | 生产文件；纯新增 |
| ③ | `shared/cases/practice-lessons.md` | 第十五节之后新增第十六至二十一节（六书）共 **24 条**误判教训（每书 4 条），第 182–223 行，每条以书名收尾；既有第一至十五节、头部版本与取材口径、文末免责声明（现第 224–226 行）**一字未动** | 生产文件；纯追加 |
| ④ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 验证记录，不计入生产文件 |

- 生产文件改动数：**3 个**（①②③），全部落在简报允许清单内；第 4 个允许清单文件即本附录（④）。
- **禁止触碰文件核对**：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件与六份合并稿草稿**只读未改**；`shared/templates/legal-opinion.md` 未改；`industries/` 全目录未改；`shared/checklists/contract-redlines.md`、`shared/checklists/employment-redlines.md` 未改；F 盘其他项目未改。
- **未提交、未推送**，改动全部留在工作区。

### 四、验收命令与真实输出（逐条实跑，2026-09-16）

**验收 1（争议岗新增不超过 15 条，为未覆盖项）**

```powershell
Select-String -Path 'roles/dispute-counsel/SKILL.md' -Pattern '^## 第 4\.7 段'
git diff --numstat -- roles/dispute-counsel/SKILL.md
```

真实输出：

```
'^## 第 4.7 段' 命中 1 行 → 行 82：## 第 4.7 段 · 庭审与举证实务（本条只加手法，不改第 4 段次序）
该段行范围 82–99（下一段标题「## 第 5 段 · 交付物规范」在第 100 行）
段内条目数 15（以 '- **' 起首的行，实测 15 行）
git diff --numstat → 18	0
  18 行 = 段标题 1 ＋ 空行 2 ＋ 条目 15；删除行 0
```

未覆盖性抽查（4 条，人工判定；检索池为该文件第 1–81 行的既有内容）：

```
'质证'     既有 0 处 → 新增段 2 处
'争议焦点' 既有 0 处 → 新增段 1 处
'逾期举证' 既有 0 处 → 新增段 1 处
'抗辩'     既有 0 处 → 新增段 1 处
→ 四条均判为未覆盖（新增段另有 '证明责任' 3 处、'否认' 1 处、'时间轴' 1 处，既有内容均为 0 处）
```

结论：**通过**。新增 15 条，处于简报「≤15 条」上限之内。

**验收 2（证据清单新增不超过 10 条，为未覆盖项）**

```powershell
Select-String -Path 'shared/checklists/evidence-preservation.md' -Pattern '^## G\.'
git diff --numstat -- shared/checklists/evidence-preservation.md
```

真实输出：

```
'^## G\.' 命中 1 行 → 行 63：## G. 举证与质证
G 节行范围 63–74；条目 10 行（第 65–74 行；首条行 65「质证意见分两层给……」，末条行 74「提交鉴定意见或评估报告前先核对……」）
git diff --numstat → 13	0
  13 行 = 节标题 1 ＋ 空行 2 ＋ 条目 10；删除行 0
```

未覆盖性抽查（2 条，人工判定；检索池为 A–F 节）：

```
'昵称'   既有 A–F 0 处 → G 节 2 处
'群成员' 既有 A–F 0 处 → G 节 1 处
→ 两条均判为未覆盖（G 节另有 '检材'、'基准日'、'失权'、'附条件' 各 1 处，A–F 均为 0 处）
```

结论：**通过**。新增 10 条，处于简报「≤10 条」上限之内。

**验收 3（教训追加不超过 24 条，每条有出处书名）**

```powershell
Select-String -Path 'shared/cases/practice-lessons.md' -Pattern '开庭|诉讼有方|王新平|易延友|可视化|证明责任'
git diff --numstat -- shared/cases/practice-lessons.md
```

真实输出：

```
新增 24 条条目中，各出处关键词命中条目数均为 4：
  开庭 4   诉讼有方 4   王新平 4   易延友 4   可视化 4   证明责任 4
  （合计 24 条，条条有书名收尾；另有 6 个新增节标题命中同批关键词）
分节条目数（脚本实测）：十六 4、十七 4、十八 4、十九 4、二十 4、二十一 4 → 合计 24
新增节标题行号：十六 第 182 行、二十一 第 217 行；新增内容止于第 223 行
git diff --numstat → 42	0
  42 行 = 节标题 6 ＋ 条目 24 ＋ 空行 12；删除行 0
既有内容可比项（第一至十五节）中：'开庭' 0 处、'诉讼有方' 0 处、'王新平' 0 处、'易延友' 0 处、
  '诉讼可视化' 0 处、'证明责任' 0 处、'品格证据' 0 处；'质证' 1 处（第九节建工条目，不同事）
```

结论：**通过**。追加 24 条，处于简报「≤24 条」上限之内，每条以书名收尾。

**验收 4（E13 附录在验证记录，含六书署名）**

```powershell
Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'E13'
```

真实输出（判定依据以附录标题行存在为准）：

```
本批落盘前（对基线 e70b00f 版本检索）：命中 1 行 —— 为 D8 附录表中的 16 位 blob 哈希串
  `219AB813A5E13D10` 内恰好含该三字符，与本次任务无关；故本项**不以「落盘前命中 0」作判据**（初稿曾如此记，已更正）。
本批落盘后（本附录含本节在内写完后的复跑）：命中 6 行，判定依据为**附录标题行**：
  行 2794：## 附录（2026-09-16）：E13 争议六书蒸馏落盘（争议岗庭审与举证实务段 / 证据清单举证与质证节 / 误判教训续编 / 验证记录）
  命中行号 496、2794、2913、2916、2923、2925 —— 除 496（哈希串）与 2794（附录标题）外，其余四行（2913、2916、2923、2925）
  均为本附录自身对本项验收的记载（小标题、命令行、哈希串引用与标题复述）。
自指计数不作为独立证据，复跑时总数随本附录条目增减而变，故不把总数作为验收判据。
```

六书署名见本附录第一节表（六行，逐行给出书名、作者／来源与落盘位置）；无作者的 `17-susongyoufang.md` 一节已在表下注明系草稿未标作者、未代拟。

**验收 5（无原文整句复制；无新增法条编号与具体数字；基线未变；未提交推送）**

之五之一 —— 抽样样本在工作区的命中数（样本先在草稿中确认存在，再在工作区排除 `.work/`、`.git/`、`.workbuddy-ai/` 后检索；依 E12 口径，样本正文不在本文件内引用）：

```
S1 src=16-kaiting.md 检查项段      草稿命中=1  len=66   工作区 hits=0   命中文件=[]
S2 src=18-wangxinping.md 标准立场段 草稿命中=1  len=41   工作区 hits=0   命中文件=[]
S3 src=19-yiyanyou.md 标准立场段    草稿命中=1  len=34   工作区 hits=0   命中文件=[]
S4 src=21-zhengmingzeren.md 检查项段 草稿命中=1  len=52   工作区 hits=0   命中文件=[]
```

之五之二 —— 逐条句级重合自检（判据：被测新增条目去掉末尾出处署名后的正文，与六份草稿**两侧同口径**处理——去空白与标点——求**连续**相同字符串的最大长度；达到或超过 20 字判为需要改写）：

```
本批被测条目：shared/cases/practice-lessons.md 第 182–223 行的全部 24 条新增教训
复跑实测：最大值 13 字；达到或超过 20 字者 0 条
  最大 13 字的三条分别为「还须对证明力有无与大小表态」（18-wangxinping）、
  「偏见混淆争点即使有证明力也」（19-yiyanyou）、「告首次举证须达到高度可能性」（21-zhengmingzeren），
  均为句内短语，非整句搬运
```

之五之三 —— 新增行法条编号与具体数字（对 `git diff -U0` 的新增行检索，三个生产文件）：

```
新增行总数: 18 ＋ 13 ＋ 42 ＝ 73（不含本附录自身的行）
条号命中（第×条|第×款|第×项|法释|解释[一二]|司法解释第）: 0 / 0 / 0
阿拉伯数字与百分号命中（[0-9]|%|百分之）: 1 / 0 / 0
  唯一命中为 SKILL.md 新增段的**节号标题**「## 第 4.7 段 · 庭审与举证实务（本条只加手法，不改第 4 段次序）」
  ——该「4.7」承接本岗文既有「第 4.6 段」体例，是文档段号，不是法条编号，也不是案件、金额、
  比例或期限门槛数字；**条目正文与条目内容的数字命中为 0**。
金额单位命中（万元|亿元|元整）: 0 / 0 / 0
```

之五之四 —— 改动范围与基线（写本附录之前与之后的两次实测）：

```
写本附录之前  git diff --name-only →
  roles/dispute-counsel/SKILL.md
  shared/cases/practice-lessons.md
  shared/checklists/evidence-preservation.md
（此时本附录尚未落盘，故不在 diff 中）
git status --porcelain →
   M roles/dispute-counsel/SKILL.md
   M shared/cases/practice-lessons.md
   M shared/checklists/evidence-preservation.md
  ?? .workbuddy-ai/   （任务开始前既有，未动）
git log -1 --format=%H → e70b00f272b95714c5689cea28cc6434ffeb8690（＝ e70b00f，与简报所给基线一致；会话内未提交，基线未变）
```

写本附录之后的完整复跑（含本附录本身）见紧接本节之后的「落盘后复跑」小节。

**落盘后复跑（2026-09-16，本附录已写入后的最终实测）**

```powershell
git diff --name-only
git status --porcelain
git diff --numstat
git log -1 --format=%H
```

真实输出：

```
git diff --name-only →
  docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
  roles/dispute-counsel/SKILL.md
  shared/cases/practice-lessons.md
  shared/checklists/evidence-preservation.md
  → 四个文件与简报允许清单逐一对应，清单外文件一个未动
git status --porcelain →
   M docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
   M roles/dispute-counsel/SKILL.md
   M shared/cases/practice-lessons.md
   M shared/checklists/evidence-preservation.md
  ?? .workbuddy-ai/   （任务开始前既有，未动）
git diff --numstat →
  196	0	docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md   （本附录，不计入生产文件新增行计数）
   18	0	roles/dispute-counsel/SKILL.md
   42	0	shared/cases/practice-lessons.md
   13	0	shared/checklists/evidence-preservation.md
  → 四个文件的删除行均为 0，即既有文字一字未动，改动全为新增行
git log -1 --format=%H → e70b00f272b95714c5689cea28cc6434ffeb8690（＝ e70b00f）
  → 基线未变，未提交、未推送
```

结论：**通过**。改动集中在允许清单的 4 个文件内，基线未变，未提交、未推送。

### 五、本附录边界

- 本附录记录的是**文本与结构落盘 ＋ 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（新增手法条目是否会被真的加载、教训选编是否会被真的对照使用，均需宿主实测）。
- **未做**：六书的全部章节通读（本批只落六份合并稿已覆盖的部分，六稿自述的缺口——`19-yiyanyou.md` 第 9–12 行的中间章节缺失、`18-wangxinping.md` 第 13 行的附录与后记无条目——未补做）；书中内容在中国法下的正确性复核（本批只做口径提炼与去重，未对实体结论作评价）；条号、金额与门槛数字的联网核验（按硬口径不移植，故无核验对象）；书中「标准立场」类草稿条目的单独落盘（既有岗位文与清单采用的是「手法＋检查项」两段式位置，本批不为此新开节）。
- 之五之二为**句级连续重合**实测，不是全文查重；抽样为抽样，不能据此断言「工作区绝对不含草稿或书中的任何片段」。
- 六份草稿为**只读输入**，本批未修改；本轮改动全部留在工作区，未提交、未推送。

## 附录（2026-09-16）：E14 建工尾巴两书蒸馏落盘（施工合同清单 / 结算验收与安全清单 / 误判教训续编 / 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与 D1–D8、E9、E10、E11、E12、E13 各附录的任何结论。
> 蒸馏口径（硬）：书籍内容只提炼方法经验与判断口径；**不搬原文整句**；**不写任何法条编号与具体数字**（两份合并稿中照引的条号、司法解释编号、案号、金额、比例与期限门槛一律改写为规则口径）；每条与既有内容逐条比对，已覆盖的不重复收录；**生产文件不逐条署名**（教训选编按既有体例以书名收尾），署名统一落在本附录。

### 一、来源署名（两书）

| # | 书名 | 作者／来源 | 采用的什么（中文重写转述，未搬原句） | 落盘位置 |
| --- | --- | --- | --- | --- |
| ① | 《建设工程纠纷办案手册》 | 北京大成 | 黑白合同的效力层次与中标无效的结算后果、规划审批与资质两类效力补正节点之分、固定价漏项按包干对象与图纸发放分配过错、无效合同参照范围与据实结算之别、通用条款的默示结算不能简单推论、挂靠人与债权受让人的优先受偿权口径分歧 | `industries/construction/checklists/construction-contract.md`；`shared/cases/practice-lessons.md` 第二十二节 |
| ② | 《最高人民法院建设工程施工合同司法解释（二）：理解适用与实务指南》 | 朱树英、曹珊 | 黑白合同的效力有无之别与串通投标证据、开工日期认定的实质要件、质保金与保修费用及缺陷责任期与保修期之分、结算协议的终局性边界、鉴定材料瑕疵的对应影响范围、优先受偿权主体与转让口径 | `industries/construction/checklists/construction-contract.md`；`industries/construction/checklists/completion-settlement-safety.md`；`shared/cases/practice-lessons.md` 第二十三节 |

> **书目归属依两份合并稿自述**：`.work/distill-outbox/14-dacheng.md` 第 7、9、11 行（该书稿自述 P1、P3、P5 三片自带该书名，其余片未标注，本附录按该稿自述署名，未代拟作者以外的信息）；`.work/distill-outbox/15-js2-qa.md` 第 8 行（仅 P3 自带书名与作者，P1、P2、P4、P5、P6、P7 六片未标注所出书目与章次，本附录按该稿该处自述署名，**未据其余片段另行推断**）。
> 两稿自述的**碎片级缺口**（14-dacheng 的 P2、P4、P6 无来源与覆盖说明；15-js2-qa 的六片无书目与章次标注）**本批未补做**，见第五节边界。

### 二、去重口径（已覆盖项不收录）

- **比对池**：`industries/construction/checklists/construction-contract.md` 全文（含既有「未覆盖项」全部条目）；`industries/construction/checklists/completion-settlement-safety.md` 三节（结算与优先受偿、竣工验收与备案、安全生产）；`shared/cases/practice-lessons.md` 第一至二十一节。另核对了 `industries/construction/checklists/bidding-compliance.md`、`claim-limitation.md`、`shared/checklists/contract-redlines.md` 与 `industries/construction/regulations.md` 中涉建工效力、结算与优先受偿的条目。
- **被判定为已有条目覆盖、故本批未收录的典型项及覆盖位置**：质保金扣留比例与返还期限的核对（施工合同清单既有「未覆盖项」条目已有）、同一工程两份及以上合同文本的文本比对与差异固定（施工合同清单既有条目已有）、以房抵款的过户交付与付款承诺措辞（结算清单既有「未覆盖项」条目已有）、以审计或第三方审价结论结算的约定主体与范围要件（结算清单既有两条已有）、合同无效或解除情形下质量责任与保修义务的分项认定（结算清单既有条目已有）、未竣工未验收即申请结算的原因归属举证（结算清单既有条目已有）、结算协议独立于施工合同故合同无效不影响按协议付款（教训第十节已有，且`李舒`条已标注同题亦见其他两书）、实际施工人不享有优先受偿权与代位权次债权金额的「查不清与本可查明」之分（教训第七、十一节已有）、黑白合同与备案中标合同规则的适用范围（`bidding-compliance.md` 与教训既有条目已有）、施工合同的效力识别是否为管理性或效力性强制规定（教训第一节已有）。
- **边界邻近项的如实登记（判为不重复，口径不同）**：结算清单既有「以行使期限与起算点主张优先受偿权前先核对适用的法律口径与起算规则」与本次新增的「债权转让后受让人是否享有优先受偿权」不同事——前者管行使期限与起算点，后者管权利主体随转让是否移转。施工合同清单既有「工期变更与安全生产费用是否同步」与本次新增的开工日期实质要件不同事——前者管工期与安全费用的联动，后者管开工日期以何为准。
- 三个生产文件的既有文字**一字未动**（`git diff --numstat` 删除行均为 0），改动全部为新增行。

### 三、本轮落盘改动（3 个生产文件 ＋ 本附录）

| # | 文件 | 落盘内容 | 性质 |
| --- | --- | --- | --- |
| ① | `industries/construction/checklists/construction-contract.md` | 文末既有「未覆盖项」条目之后新增 **8 条**检查项（多份合同的效力逐份审查与中标无效的结算后果、中标前后另订协议的分段判断、招投标文件与四项实质性内容的比对、规划审批手续与两类效力补正节点、设计变更等难以预见变化的另行协议、造价咨询意见的受约束表示与结算金额协议之分、结算协议效力独立判断与总表的性质、鉴定材料的质证与移交程序）；既有第 1–36 行**一字未动** | 生产文件；纯新增 |
| ② | `industries/construction/checklists/completion-settlement-safety.md` | 「## 结算与优先受偿」节末既有「未覆盖项」条目之后新增 **8 条**检查项（默示结算条款的两项要件、默示结算条款所在的文本层级与被排除情形、商票未兑付是否计为已付、结算协议后的保留条款、债权转让后受让人的优先受偿权、工程未完工时质保金起算点、质量维修费用抗辩的形式核对、代位权的转分包层级）；「竣工验收与备案」「安全生产」两节**一字未动** | 生产文件；纯新增 |
| ③ | `shared/cases/practice-lessons.md` | 第二十一节之后新增第二十二、二十三两节（两书）共 **16 条**误判教训（每书 8 条），每条以书名收尾；既有第一至二十一节、头部版本与取材口径、文末免责声明**一字未动** | 生产文件；纯追加 |
| ④ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 验证记录，不计入生产文件 |

- 生产文件改动数：**3 个**（①②③），全部落在简报允许清单内；第 4 个允许清单文件即本附录（④）。
- **禁止触碰文件核对**：`outputs/external-review-39abd04.md` 未改；`.work/` 下历史文件与两份合并稿草稿**只读未改**；`shared/templates/legal-opinion.md` 未改；`industries/construction/regulations.md`、`industries/construction/SKILL.md` 与本包其余文件未改；合同／劳动／争议三岗与两份红线清单未改；F 盘其他项目未改。
- **未提交、未推送**，改动全部留在工作区。

### 四、验收命令与真实输出（逐条实跑，2026-09-16）

**验收 1（施工合同清单新增不超过 8 条，为未覆盖项）**

```powershell
git diff --numstat -- industries/construction/checklists/construction-contract.md
Select-String -Path 'industries/construction/checklists/construction-contract.md' -Pattern '^- \[ \] （未覆盖项）' | Measure-Object
```

真实输出：

```
git diff --numstat → 8	0
  8 行 = 新增条目 8 条；删除行 0（既有文字一字未动）
新增条目行号（脚本实测）：第 37、38、39、40、41、42、43、44 行
  首条＝第 37 行「存在多份合同文本时，效力须逐份分别审查……」，
  末条＝第 44 行「移交造价鉴定前，逐份核对鉴定材料……」
既有「未覆盖项」条目在落盘前为 22 条，落盘后为 30 条（该文件清单条目总数落盘前 32 条、落盘后 40 条）
```

未覆盖性抽查（3 条，人工判定；检索池为该文件落盘前第 1–36 行）：

```
'中标是否有效'（新增第 1 条要点）        既有 0 处 → 新增 1 处
'竣工前'（新增第 4 条效力补正节点要点）  既有 0 处 → 新增 1 处
'质证'（新增第 8 条鉴定材料要点）        既有 0 处 → 新增 1 处
→ 三条均判为未覆盖（新增段另有 '串通投标' 1 处、'结算金额协议' 1 处、'权利义务保留' 1 处，
  既有第 1–36 行均为 0 处）
```

结论：**通过**。新增 8 条，处于简报「≤8 条」上限之内。

**验收 2（结算清单新增不超过 8 条，为未覆盖项）**

```powershell
git diff --numstat -- industries/construction/checklists/completion-settlement-safety.md
```

真实输出：

```
git diff --numstat → 8	0
  8 行 = 新增条目 8 条，全部落在「## 结算与优先受偿」节末尾；删除行 0
新增条目行号（脚本实测）：第 23、24、25、26、27、28、29、30 行，逐行均为 '- [ ] （未覆盖项）' 起首
  → 该节新增段行内条目数 8，与新增行数一致（无空行夹带）
该文件「未覆盖项」条目落盘前为 20 条、落盘后为 28 条；清单条目总数落盘前 30 条、落盘后 38 条
「## 竣工验收与备案」「## 安全生产」两节的行号与内容未发生变化（落盘后该两节分别起于第 32 行与第 44 行）
```

未覆盖性抽查（2 条，人工判定；检索池为该文件落盘前三节全文）：

```
'默示结算'（新增第 1、2 条要点）   既有 0 处 → 新增 2 处
'代位权'（新增第 8 条要点）        既有 0 处 → 新增 1 处
→ 两条均判为未覆盖（新增段另有 '背书转让' 1 处、'保留条款' 1 处、'脱离承包人占有管理' 1 处，
  既有三节均为 0 处）
```

结论：**通过**。新增 8 条，处于简报「≤8 条」上限之内。

**验收 3（教训追加不超过 16 条，每条有出处书名）**

```powershell
Select-String -Path 'shared/cases/practice-lessons.md' -Pattern '大成|司法解释' 
git diff --numstat -- shared/cases/practice-lessons.md
```

真实输出：

```
Select-String -Pattern '大成|司法解释' 在 shared/cases/practice-lessons.md 命中 18 行，构成如下：
  新增条目行 16 行（落盘后第 226–233 行、第 237–244 行），其中收尾署「大成《建设工程纠纷办案手册》」8 条、
    署「朱树英、曹珊《最高人民法院建设工程施工合同司法解释（二）：理解适用与实务指南》」8 条 → 条条有出处书名
  新增节标题 2 行（第二十二节、第二十三节）
  → 16 ＋ 2 ＝ 18 行，与本项「每条有出处书名」的判定一致
分节条目数（脚本实测）：二十二 8、二十三 8 → 合计 16
新增节标题行号：二十二 第 224 行、二十三 第 235 行；新增条目止于第 244 行
git diff --numstat → 22	0
  22 行 = 节标题 2 ＋ 空行 4 ＋ 条目 16；删除行 0（既有文字一字未动）
既有内容可比项（第一至二十一节）中：'大成' 0 处、'司法解释（二）' 0 处
```

结论：**通过**。追加 16 条，处于简报「≤16 条」上限之内，每条以书名收尾。

**验收 4（E14 附录在验证记录，含两书署名）**

```powershell
Select-String -Path 'docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md' -Pattern 'E14'
```

真实输出（判定依据以附录标题行存在为准）：

```
本批落盘后复跑：命中行含本附录标题行 ——
  ## 附录（2026-09-16）：E14 建工尾巴两书蒸馏落盘（施工合同清单 / 结算验收与安全清单 / 误判教训续编 / 验证记录）
命中其余各行均为本附录自身对本项验收的记载（小标题、命令行与标题复述）；
自指计数不作为独立证据，复跑时总数随本附录条目增减而变，故不把命中总数作为验收判据。
```

两书署名见本附录第一节表（两行，逐行给出书名、作者／来源与落盘位置）；两稿的片段级未标注已在表下如实注明，未代拟。

**验收 5（无原文整句复制；无新增法条编号与具体数字；基线未变；未提交推送）**

之五之一 —— 抽样样本在工作区的命中数（样本先在草稿中确认存在，再在工作区排除 `.work/`、`.git/`、`.workbuddy-ai/` 后检索；依 E12、E13 口径，样本正文不在本文件内引用）：

```
S1 src=14-dacheng.md 检查项段（第 38 行）  草稿命中=1  len=60  工作区 hits=0  命中文件=[]
S2 src=14-dacheng.md 教训段（第 220 行）   草稿命中=1  len=44  工作区 hits=0  命中文件=[]
S3 src=15-js2-qa.md  教训段（第 197 行）   草稿命中=1  len=60  工作区 hits=0  命中文件=[]
（工作区文本文件实扫 36 个，口径同上）
```

之五之二 —— 逐条句级重合自检（判据：被测新增条目去掉末尾出处署名后的正文，与两份草稿**两侧同口径**处理——去空白与标点——求**连续**相同字符串的最大长度；达到或超过 20 字判为需要改写）：

```
本批被测条目：三个生产文件的全部 32 条新增条目（逐条去掉末尾出处署名与「（未覆盖项）」标记后与草稿比对）
  industries/construction/checklists/construction-contract.md 第 37–44 行 8 条
  industries/construction/checklists/completion-settlement-safety.md 第 23–30 行 8 条
  shared/cases/practice-lessons.md 第 226–233、237–244 行 16 条新增教训
复跑实测：最大值 19 字；达到或超过 20 字者 0 条。分文件最大值（逐条取两稿中的较大者）：
  施工合同清单 8 条 → 最大 19 字
  结算清单 8 条     → 最大 19 字
  教训新增 16 条    → 最大 17 字
  最大 19 字的命中来自施工合同清单第 4 条，连续串为「故意不办的属阻挠条件成就合同应认定有效」——
  系跨句拼接的短语串（前半承接草稿的「阻挠条件成就」表述、后半为本批自撰的结论句），非整句搬运
```

之五之三 —— 新增行法条编号与具体数字（对 `git diff -U0` 的新增行检索，三个生产文件）：

```
新增行总数: 8 ＋ 8 ＋ 22 ＝ 38（不含本附录自身的行）
条号命中（第×条|第×款|第×项|法释|司法解释第|解释[一二]第）: 0 / 0 / 0
阿拉伯数字与百分号命中（[0-9]|%|百分之）: 0 / 0 / 0
金额单位命中（万元|亿元|元整）: 0 / 0 / 0
  → 三个生产文件的 38 行新增行内无任何数字命中；新增行内容全部为规则口径表述
```
之五之四 —— 改动范围与基线（写本附录之前与之后的两次实测）：

```
写本附录之前  git diff --name-only →
  industries/construction/checklists/completion-settlement-safety.md
  industries/construction/checklists/construction-contract.md
  shared/cases/practice-lessons.md
（此时本附录尚未落盘，故不在 diff 中）
git status --porcelain →
   M industries/construction/checklists/completion-settlement-safety.md
   M industries/construction/checklists/construction-contract.md
   M shared/cases/practice-lessons.md
  ?? .workbuddy-ai/   （任务开始前既有，未动）
git log -1 --format=%H → e40babd1fb614f4db779e8fc54c55f2c2fd98247（＝ e40babd，与简报所给基线一致；会话内未提交，基线未变）
```

写本附录之后的完整复跑（含本附录本身）见紧接本节之后的「落盘后复跑」小节。

### 三之一、本批自查中发现并已自行纠正的五处

> 以下五处为**本批执行过程中自查发现并当场改正**的记录，改正前的中间状态未留在工作区（最终落盘文件以本节所述改正后为准）。

1. **计数口径修正（8 处「行数」改写为「条数」）**：初稿把 `git diff --numstat` 的新增行数与新增条目数混用，例如把结算清单的 `8 0` 写成「新增条目 8 行」、把教训的 `22 0` 写成「条目 22 条」。实际构成见本节各验收块：结算清单 8 行＝条目 8 条（无空行夹带）；教训 22 行＝节标题 2 ＋ 空行 4 ＋ 条目 16，**净增条目为 16 条**。
2. **结算清单漏收一条（计数由 7 补齐为 8）**：一次落盘时其中一条检查项未写入，实测 `git diff --numstat` 为 `7 0`、与「新增 8 条」不符。补齐后复测为 `8 0`、条目行号为第 23–30 行连续 8 行，方与本附录所述一致。
3. **一条教训超出上限（计数由 17 降为 16）**：追加教训时多写 1 条（共 17 条），超出简报「≤16 条」上限。删去其中一条与既有第七节、第十节内容重复者（该条同时涉实际施工人的优先受偿权与债权转让后受让人的权利，两处既有条目已各有覆盖），复测为 16 条、每书 8 条。
4. **一条检查项与草稿的连续重合达到判定阈值（由 31 字改写至 19 字）**：结算清单中一条涉质量维修费用抗辩的条目，与草稿的连续重合达 31 字，超出「达到 20 字即改写」的判据。改写后全批 32 条的最大连续重合为 19 字、达到或超过 20 字者为 0 条。
5. **一处表述失准（改为纯规则口径）**：结算清单中质保金起算点一条，初稿写作「满缺陷责任期」，与草稿所述「未约定的自该日起满两年」并非同一口径（缺陷责任期的长度不固定）。改写为不含任何门槛数字的规则口径表述。

**落盘后复跑（2026-09-16，本附录已写入后的最终实测）**

```powershell
git diff --name-only
git status --porcelain
git diff --numstat
git log -1 --format=%H
```

真实输出：

```
git diff --name-only →
  docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
  industries/construction/checklists/completion-settlement-safety.md
  industries/construction/checklists/construction-contract.md
  shared/cases/practice-lessons.md
  → 四个文件与简报允许清单逐一对应，清单外文件一个未动
git diff --numstat →
  236	0	docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md   （本附录，不计入生产文件新增行计数）
    8	0	industries/construction/checklists/completion-settlement-safety.md
    8	0	industries/construction/checklists/construction-contract.md
   22	0	shared/cases/practice-lessons.md
  → 四个文件的删除行均为 0，即既有文字一字未动，改动全为新增行
git log -1 --format=%H → e40babd1fb614f4db779e8fc54c55f2c2fd98247（＝ e40babd）
  → 基线未变，未提交、未推送
```

结论：**通过**。改动集中在允许清单的 4 个文件内，基线未变，未提交、未推送。

### 五、本附录边界

- 本附录记录的是**文本与结构落盘 ＋ 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（新增检查项是否会被真的逐项过、教训选编是否会被真的对照使用，均需宿主实测）。
- **未做**：两书的全部章节通读（本批只落两份合并稿已覆盖的部分）；两份合并稿自述的**缺口未补**——`14-dacheng.md` 第 13 行的 P2、P4、P6 无来源与覆盖说明、`15-js2-qa.md` 第 8 行的六片无书目与章次标注，均照稿如实登记而未作推断补写；两书「标准立场」类草稿条目的单独落盘（既有清单与教训选编采用的是「检查项＋教训」两段式位置，本批不为此新开节）；书中内容在中国法下的正确性复核（本批只做口径提炼与去重，未对实体结论作评价）；条号、金额与门槛数字的联网核验（按硬口径不移植，故无核验对象）。
- 之五之二为**句级连续重合**实测，不是全文查重；抽样为抽样，不能据此断言「工作区绝对不含草稿或书中的任何片段」。
- 两份合并稿为**只读输入**，本批未修改；本轮改动全部留在工作区，未提交、未推送。

## 附录（2026-09-16）：E15 治理知产六书蒸馏落盘（治理岗脚手架 / 知产岗脚手架 / 误判教训续编 / 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述各附录的任何结论。
> 口径（硬）：只收现有内容未覆盖项（逐条比对既有第 2 段失败模式、第 4 段脚手架与教训选编）；**不写法条编号与具体数字**，草稿条号与门槛一律改写为规则口径；不搬原文整句，全部为中文重写；注释出处只到书名。

### 一、本轮取材六书（署名）

| # | 书名 | 本批取材稿 | 落盘去向 |
| --- | --- | --- | --- |
| ① | 朱锦清《公司法学（上）》 | `.work/distill-outbox/22-gsf-shang.md`（398 条） | 治理岗第 4 段脚手架（详见下表） |
| ② | 朱锦清《公司法学（下）》 | `.work/distill-outbox/23-gsf-xia.md`（439 条） | 治理岗第 4 段脚手架 |
| ③ | 朱慈蕴《新公司法条文精解》 | `.work/distill-outbox/24-xgsf.md`（349 条） | 治理岗第 4 段脚手架 ＋ 教训选编第二十四节 |
| ④ | 唐青林《商业秘密案件裁判规则》 | `.work/distill-outbox/25-mimi-caipan.md`（322 条） | 知产岗第 4 段脚手架 ＋ 教训选编第二十五节 |
| ⑤ | 王俊林等《商业秘密保护实务及案例精解》 | `.work/distill-outbox/27-mimi-baohu.md`（233 条） | 知产岗第 4 段脚手架 ＋ 教训选编第二十六节 |
| ⑥ | 章建勤《商业秘密维权指南》 | `.work/distill-outbox/26-mimi-weiquan.md`（230 条） | 知产岗第 4 段脚手架 ＋ 教训选编第二十七节 |

**署名风险（必须写明）**：第⑥书的**书名取自草稿内容特征反推，草稿本身未署名**。`26-mimi-weiquan.md` 第 36 行自述「P1～P8 八个碎片均未声明所覆盖的书目、章节、规则号或案例编号范围」（逐片复核 `mimi-weiquan-p1`～`p8` 首行，确无来源行），文中亦无书名出现。该稿主题（密点撰写、举证责任转移、同一性比对、刑民界分）与微信读书总单第 11 批「章建勤《商业秘密维权指南》」一致，故据以署名；**此项属推断，非草稿载明**。第①至⑤书均有草稿或碎片来源行直接载明书名（②见 `23-gsf-xia.md` 第 10–17 行；③见 `xgsf-p1.md` 第 1 行；④见 `25-mimi-caipan.md` 第 36 行 P1 声明；⑤见 `mimi-baohu-p2.md` 第 3 行）。

### 二、本轮落盘改动（4 个文件，全部在允许清单内）

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `roles/governance-counsel/SKILL.md` | 第 4 段脚手架新增第 8–19 条（12 条：义务与责任主体、出资与加速到期及失权程序、减资与债权人保护、合并分立与重大资产处分定性、清算与退出、登记与名册效力、程序与计票、决议瑕疵定性、无权代表双轨规则、登记瑕疵不免责、知情权与商业秘密交叉、前置材料留痕）；版本 v1.0→v1.1，知识截至日期 2026-09-14→2026-09-16 | 生产文件；只加条，未改既有 1–7 条与各段结构 |
| ② | `roles/ip-counsel/SKILL.md` | 第 4 段脚手架新增第 8–12 条（5 条：客体与载体、三性与密点固定、保密措施、步骤与顺序、举证与鉴定）；版本 v1.0→v1.1，知识截至日期 2026-09-14→2026-09-16 | 生产文件；只加条，未改既有 1–7 条与各段结构 |
| ③ | `shared/cases/practice-lessons.md` | 追加第二十四～二十七节，共 24 条误判教训（六书，其中③④⑤⑥书各 6 条），每条末注书名 | 生产文件；纯追加，未改既有二十三节一字 |
| ④ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 新增条数：治理岗 **12 条**（限额 ≤12）、知产岗 **5 条**（限额 ≤12）、教训 **24 条**（限额 ≤24）。
- **未提交、未推送**，改动全部留在工作区。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md`、`.work/` 下全部历史文件与草稿、`shared/templates/legal-opinion.md`、`industries/` 未改。

### 三、六书口径去向说明（为何教训只落在四书）

三家书（③④⑤⑥）的误判教训逐条比对既有选编后落盘；两家书（①②朱锦清上/下）的误判教训经比对**全部被既有条目或新增条目覆盖**（同题已见教训选编第二十四节与新增脚手架各条），故不重复落盘——落盘位置记：朱锦清两书的治理口径已进入治理岗第 4 段第 8–19 条，其教训与既有选编同题条目重复，按「只收未覆盖项」不另出条目。六书署名仍按第一节表格统一登记。

### 四、边界

- 本附录记录的是**文本与结构落盘 ＋ 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（新增脚手架条目是否会被真的按序执行、教训是否会被真的对照使用，均需宿主实测）。
- **未做**：六书全本通读（只读六份合并稿）；六稿自述缺口未补——`25-mimi-caipan.md` 第 36–37 行（P2–P10 无覆盖声明、P1 末例未完整）、`26-mimi-weiquan.md` 第 36 行（八片均无来源声明）、`27-mimi-baohu.md` 第 36–39 行（P1/P5/P6 无覆盖声明、原书前两章无碎片覆盖）、`24-xgsf.md` 第 2 行（三个条段无碎片声明覆盖）均照稿登记，未作推断补写；六书「标准立场」类草稿条目的单独落盘（既有结构采用「检查项＋教训」两段式位置，不为此新开节）；书中内容在中国法下的实体正确性复核；条号、金额与门槛数字的联网核验（按硬口径不移植，故无核验对象）。
- 六份合并稿与 `.work/` 下历史文件为**只读输入**，本批未修改。

## 附录（2026-09-16）：E16 劳动剩余七书蒸馏落盘（劳动 N 节 / 误判教训续编 / 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述各附录的任何结论。
> 口径（硬）：只收现有内容未覆盖项（逐条比对既有 A–H、L、M 三节与教训选编一至二十七节）；**不写法条编号与具体数字**，草稿条号与门槛一律改写为规则口径；不搬原文整句，全部为中文重写；注释出处只到书名。

### 一、本轮取材七书（署名）

| # | 书名 | 本批取材稿 | 落盘去向 |
| --- | --- | --- | --- |
| ① | 洪桂彬《HR全流程法律指南》 | `.work/distill-outbox/33-honghegui.md`（258 条） | 劳动红线清单 N 节 ＋ 教训选编第二十八节 |
| ② | 陈轶凡、董润青《HR全流程法律顾问：速查速用全书》 | `.work/distill-outbox/34-sucha.md`（464 条） | 劳动红线清单 N 节 ＋ 教训选编第二十九节 |
| ③ | 周丽霞《HR全程法律顾问：企业人力资源管理高效工作指南（增订4版）》 | `.work/distill-outbox/35-zhoulixia.md`（493 条） | 劳动红线清单 N 节 ＋ 教训选编第三十节 |
| ④ | 《劳动争议仲裁诉讼实战宝典》 | `.work/distill-outbox/30-baodian.md`（183 条） | 劳动红线清单 N 节 ＋ 教训选编第三十一节 |
| ⑤ | 梁镇邦《劳动争议实务案例解析与要点剖析》 | `.work/distill-outbox/31-liangzhenbang.md`（166 条） | 劳动红线清单 N 节 ＋ 教训选编第三十二节 |
| ⑥ | 刘秋苏《劳动争议案件35个胜诉策略及实务解析》 | `.work/distill-outbox/32-liuqiusu.md`（247 条） | 劳动红线清单 N 节 ＋ 教训选编第三十三节 |
| ⑦ | 肖胜方《劳动合同法下的人力资源管理流程再造（增订4版）》 | `.work/distill-outbox/36-xiaoshengfang.md`（280 条） | 劳动红线清单 N 节 ＋ 教训选编第三十四节 |

**署名风险（必须写明）**：第④书《劳动争议仲裁诉讼实战宝典》的**书名取自草稿内容特征与碎片来源行反推，草稿本身未给出作者**。`30-baodian.md` 第 5 行仅声明「书目名取自 R110 一片声明的来源（《劳动争议仲裁诉讼实战宝典》）」，未载作者、出版社；该稿六个碎片（`rest-p110`～`rest-p115`）的其余五片亦无来源行（见该稿第 30–32 行缺口说明）。故本轮落盘在书名字段只写书名，**不署作者**；此项属草稿载明的书名、但无作者信息，非本批推断补写。第①②③⑤⑥⑦书均有草稿来源行直接载明书名，②之作者（陈轶凡、董润青）见 `34-sucha.md` 第 9 行。

**其他署名口径**：第⑥书书名含「35个」这一数字，属书名本身而非条款门槛数字，照书名登记。

### 二、本轮落盘改动（3 个文件，全部在允许清单内）

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `shared/checklists/employment-redlines.md` | 新增 N 节「解除、辞职与争议程序补充（A–H、L、M 未覆盖项）」，含 N1–N12 共 12 条（解除通知的理由表述与追加空间、辞职文书性质甄别、支付令应对、特殊工时审批与协商两道手续、考勤方式与工时制度匹配、工资支付记录要素与保管、最低工资公示与兜底约定、医疗期满另行安置、一事不再罚、外包管理边界与退件处理、录用条件书面告知签收、一裁终局与执行时限管理） | 生产文件；只追加一节，未改 A–H、L、M 既有条目一字，未改版本号与知识截至日期 |
| ② | `shared/cases/practice-lessons.md` | 追加第二十八～三十四节，共 30 条误判教训（七书分别 4／3／6／3／6／3／5 条），每条末注书名 | 生产文件；纯追加，未改既有二十七节一字 |
| ③ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 新增条数：劳动 N 节 **12 条**（限额 ≤12）、教训 **30 条**（限额 ≤30）。
- **未提交、未推送**，改动全部留在工作区；基线提交仍为 `a4815de`。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md`、`.work/` 下全部历史文件与七份草稿、`shared/templates/legal-opinion.md`、`industries/`、合同红线清单、其他岗位文件均未改。

### 三、七书口径去向说明

- 七书的**检查项**类条目统一取用为 N 节 12 条（跨书合并同题、只留未覆盖项），未逐书分配。
- 七书的**误判教训**类条目按书落节，七书均有落盘（4／3／6／3／6／3／5）。
- 七书的**标准立场**类条目本批**未单独落盘**：既有结构采用「检查项＋教训」两段式位置，门槛、期限与比例类口径按硬口径不移植，故其可移植部分已并入 N 节各条的「标准立场」与「底线」表述，不另开节。
- **同题未落盘记录**：以下七书条目经逐条比对，已被既有内容覆盖，按「只收未覆盖项」不另出条目——①「规章制度公示不宜以张贴公告为唯一方式」（既有教训选编第三节同题）、①「法定节假日加班不得以补休替代」（既有教训选编第三节同题）、①「员工猝死后补办社保参保不属基金支付范围的新发生费用」（既有教训选编第三节同题）、①「入职环节以同业竞争为由取消录用构成就业歧视」（既有教训选编第三节同题）；②「劳动合同缺必备条款不等于合同无效」（既有教训选编第四节同题）、②「工伤与第三人侵权竞合下的追偿口径」（既有教训选编第四节同题）；③「员工口头表示不续订即可免付补偿须书面固定」（既有教训选编第二十二节治理类同题「默示结算须书面一致约定」的取证逻辑）；④「和解协议不能排除对方提起仲裁」（既有教训选编第二十九节新增条已收，本批不再重复）。

### 四、边界

- 本附录记录的是**文本与结构落盘 ＋ 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（N 节条目是否会被真的逐项核对、教训是否会被真的对照使用，均需宿主实测）。
- **未做**：七书全本通读（只读七份合并稿）；七稿自述缺口未补——`30-baodian.md` 第 30–32 行（仅一片声明章节范围、分片序列无法还原）、`31-liangzhenbang.md` 第 28–29 行（分片序号缺失、两片范围重叠）、`32-liuqiusu.md` 第 32–33 行（六片无来源行）、`33-honghegui.md` 第 20–21 行（无页码与章节号、图表未体现）、`34-sucha.md` 第 42–44 行（多片未声明章节、两片专题重叠）、`35-zhoulixia.md` 第 34–37 行（五片无来源行、两片范围重叠、分片序列无法还原）、`36-xiaoshengfang.md` 第 6 行（三片未标注对应章节）均照稿登记，未作推断补写；七书标准立场类条目的单独落盘；书中内容在中国法下的实体正确性复核；条号、金额与门槛数字的联网核验（按硬口径不移植，故无核验对象）。
- 七份合并稿与 `.work/` 下历史文件为**只读输入**，本批未修改。

## 附录（2026-09-16）：E17 劳动第二批六书蒸馏落盘（劳动 O 节 / 误判教训续编 / 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述各附录的任何结论。
> 口径（硬）：只收现有内容未覆盖项（逐条比对既有 A–H、L、M、N 五节与教训选编一至三十四节）；**不写法条编号与具体数字**，草稿条号与门槛一律改写为规则口径；不搬原文整句，全部为中文重写；注释出处只到书名。

### 一、本轮取材六书（署名）

| # | 书名 | 本批取材稿 | 落盘去向 |
| --- | --- | --- | --- |
| ① | 温必潜《工伤保险待遇纠纷案例精选》 | `.work/distill-outbox/39-wenbiqian.md`（188 条） | 劳动红线清单 O 节 ＋ 教训选编第三十五节 |
| ② | 王勤伟《工伤认定典型案例解析与实务指南》 | `.work/distill-outbox/40-gongshang.md`（216 条） | 劳动红线清单 O 节 ＋ 教训选编第三十六节 |
| ③ | 《最高人民法院新劳动争议司法解释（二）实务指南》 | `.work/distill-outbox/41-js2zhidao.md`（130 条） | 劳动红线清单 O 节 ＋ 教训选编第三十七节 |
| ④ | 商云《解决劳动争议三步法》 | `.work/distill-outbox/37-shangyun.md`（232 条） | 劳动红线清单 O 节 ＋ 教训选编第三十八节 |
| ⑤ | 陈豪《调岗调薪及经济性裁员实操指南》 | `.work/distill-outbox/38-chenhao.md`（93 条） | 劳动红线清单 O 节 ＋ 教训选编第三十九节 |
| ⑥ | 娄秋琴《这样做HR最有效》 | `.work/distill-outbox/42-louqiuxin.md`（446 条） | 劳动红线清单 O 节 ＋ 教训选编第四十节 |

**署名风险（必须写明）**：第③书《最高人民法院新劳动争议司法解释（二）实务指南》的**书名取自草稿中碎片自带来源行的转述，草稿未写全称与作者**。`41-js2zhidao.md` 第 9 行载明「仅 R195 自带来源说明——《最高人民法院新劳动争议司法解释（二）实务指南》第一至六条」，另三片（R196–R198）无来源行，该稿第 9–11 行已把分片与条号缺口照稿登记。故本轮落盘在书名字段只写该转述书名，**不署作者**。另需并列提示：该稿文件名 `41-js2zhidao` 与本项目既有的 `15-js2-qa`（**建设工程**施工合同司法解释（二））并非同一解释，该稿第 14 行已自述此点，署名**不依文件名**、依 R195 的来源行。第⑥书《这样做HR最有效》的书名亦取自草稿内容特征——`42-louqiuxin.md` 第 5 行自述「书目名取自 R208、R210 两片声明的来源」，非草稿开篇载明；第①②④⑤书均有草稿封面行直接载明书名与作者。

### 二、本轮落盘改动（3 个文件，全部在允许清单内）

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `shared/checklists/employment-redlines.md` | 新增 O 节「工伤认定与工伤待遇补充（A–H、L、M、N 未覆盖项）」，含 O1–O12 共 12 条（认定前提性事实与主体资格、违法转包分包挂靠与多重用工的责任主体、「三工」要件与举证分配、上下班途中四要件、视同工伤链条与排除情形、申报时限起算与超期例外、认定程序与相关行政行为可诉性、未参保与补缴后的支付主体分层、第三人侵权与工伤竞合、赔偿协议效力审查、认定鉴定与行政民事程序分工、工伤职工岗位与待遇衔接），体例同 N 节（条目 ＋ 标准立场） | 生产文件；只追加一节，未改 A–H、L、M、N 既有条目一字，未改版本号与知识截至日期 |
| ② | `shared/cases/practice-lessons.md` | 追加第三十五～四十节，共 30 条误判教训（六书分别 5／5／5／5／6／4 条），每条末注书名 | 生产文件；纯追加，未改既有三十四节一字 |
| ③ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 新增条数：劳动 O 节 **12 条**（限额 ≤12）、教训 **30 条**（限额 ≤30）。
- **未提交、未推送**，改动全部留在工作区；基线提交仍为 `7cb0ac7`。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md`、`.work/` 下全部历史文件与六份草稿（含本次只读的 37／38／39／40／41／42 六稿）、`shared/templates/legal-opinion.md`、`industries/`、合同红线清单、其他岗位文件均未改。

### 三、六书口径去向说明

- 六书的**检查项**类条目统一取用为 O 节 12 条（跨书合并同题、只留未覆盖项），未逐书分配；工伤认定与待遇主题优先，其余主题只在 A–H、L、M、N 确实未覆盖时才收入。
- 六书的**误判教训**类条目按书落节，六书均有落盘（5／5／5／5／6／4）。
- 六书的**标准立场**类条目本批**未单独落盘**：既有结构采用「检查项＋教训」两段式位置，门槛、期限与比例类口径按硬口径不移植，故其可移植部分已并入 O 节各条的「标准立场」与「底线」表述，不另开节。
- **跨书归并登记**：第②书草稿中「仲裁裁决确认的事实劳动关系经当事人部分起诉后不发生法律效力」一条，经比对与第③书同题条目属同一判断口径，按「只收未覆盖项」**只保留一条**，实际落点为 **O 节 O1**（检查项「仲裁裁决确认的劳动关系的生效状态」及该条标准立场），未落在教训选编第三十七节，故不占教训分书计数；第②书该条未重复落盘。六书误判教训分书计数仍为上表与上条所列 **5／5／5／5／6／4 条（合计 30 条）**。

### 四、边界

- 本附录记录的是**文本与结构落盘 ＋ 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（O 节条目是否会被真的逐项核对、教训是否会被真的对照使用，均需宿主实测）。
- **未做**：六书全本通读（只读六份合并稿）；六稿自述缺口未补——`40-gongshang.md` 第 9–10 行（R179 与 R180 自述区间之间的案例与章节不在碎片内、R181–R185 未声明覆盖区间、是否整页缺失无法从碎片内部核实）、`41-js2zhidao.md` 第 9–11 行（R196–R198 三片未标注所出书目与章次）、`37-shangyun.md` 第 6 行（R200、R203、R204 未标注对应章节）、`38-chenhao.md` 第 6 行（R206 未标注对应章节与页码）、`42-louqiuxin.md` 第 5–7 行（书目名取自碎片来源行、合并范围外另有四段编号空档无碎片）均照稿登记，未作推断补写；六书标准立场类条目的单独落盘；书中内容在中国法下的实体正确性复核（含第③书所涉新解释施行后与旧解释的衔接口径）；条号、金额与门槛数字的联网核验（按硬口径不移植，故无核验对象）。
- 六份合并稿与 `.work/` 下历史文件为**只读输入**，本批未修改（本次另经工作区只读检索引用六稿，未写入其中任何一份）。

## 附录（2026-09-16）：E18 合规仲裁四书蒸馏落盘（合规岗脚手架 / 争议岗仲裁实务 / 误判教训续编 / 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述各附录的任何结论。
> 口径（硬）：只收现有内容未覆盖项（逐条比对既有各岗 SKILL 段落、清单与教训选编一至四十节）；**不写法条编号与具体数字**，草稿条号与门槛一律改写为规则口径；不搬原文整句，全部为中文重写；注释出处只到书名。

### 一、本轮取材四书（署名）

| # | 书名 | 本批取材稿 | 落盘去向 |
| --- | --- | --- | --- |
| ① | 孙巍《中国商事仲裁法律与实务》 | `.work/distill-outbox/28-sunwei.md`（414 条） | 合规岗第 2 段自查清单 ＋ 教训选编第四十一节（6 条） |
| ② | 许杰《国际商事仲裁实务》 | `.work/distill-outbox/29-xujie.md`（190 条） | 争议岗第 4.6 段仲裁实务 ＋ 教训选编第四十二节（4 条） |
| ③ | 丁晓东《个人信息保护：原理与实践》 | `.work/distill-outbox/44-dingxiaodong.md`（182 条） | 合规岗第 2 段自查清单 ＋ 教训选编第四十三节（4 条） |
| ④ | 刘新宇《个人信息保护法重点解读与案例解析》 | `.work/distill-outbox/45-liuxinyu.md`（88 条） | 合规岗第 2 段自查清单 ＋ 教训选编第四十四节（6 条） |

**署名来源说明**：四份合并稿均在开篇「覆盖与缺口」段载明书目名及作者，取自碎片自带的来源行（`28-sunwei.md` 第 5 行取自 R77、R82；`29-xujie.md` 第 3 行；`44-dingxiaodong.md` 第 5 行取自 R23、R24；`45-liuxinyu.md` 第 5 行取自 R30、R31），四稿均未声明完整覆盖区间，落盘时按稿登记缺口，未作推断补写。

### 二、本轮落盘改动（4 个文件，全部在允许清单内）

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `roles/compliance-counsel/SKILL.md` | 第 2 段末尾新增 6 条（仲裁条款设计类 3 条：条款三要件与机构可确定、非涉外交易约定境外仲裁的效力与相容性、临时措施与保全落点；个人信息合规类 3 条：委托处理协助义务与跨境提供告知同意与泄露通知、两项评估不得互替与留存、员工信息与留痕核实），体例同该段既有条目 | 生产文件；只追加条目，未改既有条目、未改版本号与知识截至日期 |
| ② | `roles/dispute-counsel/SKILL.md` | 第 4.6 段新增第 12–16 条共 5 条（基础合同实体法与仲裁协议实体法之分、境外缺席裁决的承认执行与应诉加反请求、临时救济两条并行通道、程序异议失权与形式核阅、证据规则版本锁定与冲突适用） | 生产文件；只追加条目，未改既有条目、未改第 4 段次序 |
| ③ | `shared/cases/practice-lessons.md` | 追加第四十一～四十四节，共 20 条误判教训（四书分别 6／4／4／6 条），每条末注书名 | 生产文件；纯追加，未改既有四十节一字 |
| ④ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 新增条数：合规岗 **6 条**（限额 ≤10）、争议岗 **5 条**（限额 ≤6）、教训 **20 条**（限额 ≤20）。
- **未提交、未推送**，改动全部留在工作区；基线提交仍为 `ec4a4e9`。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md`、`.work/` 下全部历史文件与四份草稿（含本次只读的 28／29／44／45 四稿）、`shared/templates/legal-opinion.md`、`industries/`、各岗红线清单、其他岗位文件均未改。

### 三、四书口径去向说明

- 四书的**检查项**类条目只取「既有各岗 SKILL 段落、清单与教训选编确实未覆盖」的部分：仲裁条款设计类入合规岗（合规审查场景下的条款与程序层核对），国际仲裁与商事仲裁实务类入争议岗；同一主题跨书合并后只保留一条，不逐书分配。
- 四书的**误判教训**类条目按书落节，四书均有落盘（6／4／4／6，合计 20 条）。
- 四书的**标准立场**类条目本批**未单独落盘**：既有结构采用「检查项＋教训」两段式位置，条号、门槛与比例类口径按硬口径不移植，可移植部分已并入上述两岗新增条目的表述中，不另开节。
- **去重口径说明**：四稿中与既有条目同题或已被覆盖的部分（仲裁协议独立性与效力扩张、仲裁时效抗辩与中断、撤销与不予执行两程序衔接、仲裁中保全的管辖分流、送达与电子送达、书证提出命令与证据质证、含国际仲裁证据规则的披露门槛、个人信息处理的告知同意与最小必要、跨境提供的法定路径与单独同意、被遗忘权与可携带权的边界等）一律**不收录**，不计入本轮条数；新增条目的取舍只以「既有内容是否已经写过同一判断口径」为唯一标准。

### 四、边界

- 本附录记录的是**文本与结构落盘 ＋ 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（新增条目是否会被真的逐项核对、教训是否会被真的对照使用，均需宿主实测）。
- **未做**：四书全本通读（只读四份合并稿）；四稿自述缺口未补——`28-sunwei.md` 第 42–44 行（R74、R75、R76、R78、R79、R80、R81、R83、R84 九片未声明覆盖区间）、`29-xujie.md` 第 35–37 行（R169、R170、R172 三片未声明覆盖区间，六片间存在内容跳跃）、`44-dingxiaodong.md` 第 25–27 行（R25、R27 未标注来源行）、`45-liuxinyu.md` 第 24–25 行（R29 未声明来源，R30 与 R31 声明区间存在重叠）均照稿登记，未作推断补写；四书标准立场类条目的单独落盘；四书所涉境外法域规则在中国法下的实体正确性复核；条号、期限与比例数字的联网核验（按硬口径不移植，故无核验对象）。
- 四份合并稿与 `.work/` 下历史文件为**只读输入**，本批未修改。

## 附录（2026-09-17）：E19 合同审查思维蒸馏落盘（合同岗审查思维段 / 误判教训续编 / 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述各附录的任何结论。
> 口径（硬）：只收现有内容未覆盖项（逐条比对合同岗 SKILL 第 0–7 段与第 4.5、4.6 段、合同红线清单 A–L 各节、教训选编一至四十四节）；**不写法条编号与具体数字**，草稿条号与门槛一律改写为规则口径；不搬原文整句，全部为中文重写；注释出处只到书名。

### 一、本轮取材（署名）

| # | 书名 | 本批取材稿 | 落盘去向 |
| --- | --- | --- | --- |
| ① | 《合同审查思维体系与实务技能》第3版 | `.work/distill-outbox/43-hetong.md`（417 条；分片 R136–R143，去重后仍 417 条、删除 0 条） | 合同岗第 4.7 段（10 条）＋ 教训选编第四十五节（8 条） |

**署名风险（必须写明）**：本轮书名的**唯一来源是本次任务简报的标题**，生产文件与教训选编中的书名均据此登记。取材稿 `43-hetong.md` 开篇「覆盖与缺口」段**未载来源行、未载作者与出版社**，只声明本次归并的八个分片（`rest-p136.md`～`rest-p143.md`）全部到齐；该项目 `.work/distill-frag/` 下的八份分片亦无来源行，唯 `rest-p142.md` 第 3 行有一行「来源：建设工程施工合同（专用条款逐条审查）、出资合同、股权转让合同、股权代持合同、合伙合同。」——该行是**合同类型**说明而非书目信息。故落盘在书名字段只写书名与版次，**不署作者**；此项属简报载明、草稿无据，非本批推断补写。

### 二、本轮落盘改动（3 个文件，全部在允许清单内）

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `roles/contract-counsel/SKILL.md` | 新增第 4.7 段「合同审查思维要点（E19 蒸馏；只加思维口径，不改第 4 段与第 4.5、4.6 段的次序）」，含第 1–10 条共 10 条（立场与尺度先定、顺序不能倒、商业目的落到条款、结构漏项与周延性一并查、开放表述收口、效力与责任分开判断、先定交付形态再落到文本、商务取舍归委托人、AI 只作工具、示范文本类合同的填空审查） | 生产文件；只追加一段，未改第 0–7 段与第 4.5、4.6 段既有条目一字，第 4 段第 8／9 条编号与位置未动，未改版本号与知识截至日期 |
| ② | `shared/cases/practice-lessons.md` | 追加第四十五节，共 8 条误判教训，每条末注书名 | 生产文件；纯追加，未改既有四十四节一字 |
| ③ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 新增条数：合同岗 **10 条**（限额 ≤10）、教训 **8 条**（限额 ≤8）。
- **未提交、未推送**，改动全部留在工作区；基线提交仍为 `c823dd7`。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md`、`.work/` 下全部历史文件与本次只读的 `43-hetong.md`（含 `rest-p136`～`rest-p143` 八份分片）、`shared/templates/legal-opinion.md`、`industries/`、合同红线清单、其他岗位文件均未改。

### 三、单书口径去向说明

- 本书**检查项**类条目（194 条）只取「既有内容确实未覆盖」的审查思维与手法部分，落为第 4.7 段 10 条；同类条目跨分片合并同题后只保留一条，未逐分片分配。交易模式提炼、结构漏项、周延性、开放表述收口、交付形态、商务与法律分工、AI 辅助边界等属通用审查思维；施工合同专用条款必填／选填、以「执行通用合同条款」代替约定、协议书与专用条款互为一致等属示范文本类合同的填空手法，合并为第 10 条，**草稿所列的具体条款编号一律未迁移**。
- 本书**标准立场**类条目（112 条）本批**未单独落盘**：既有结构采用「检查项＋教训」两段式位置，条号、门槛与比例类口径按硬口径不移植，可移植部分已并入第 4.7 段各条与第四十五节各条的表述，不另开节。
- 本书**误判教训**类条目（111 条）落第四十五节 8 条，只取既有教训选编一至四十四节未写过同一判断口径者（立场颠倒、谈判地位不高的强改无用功、直接逐条改被文本牵着走、交易模式错位、非签约主体连带责任承诺、让委托人承诺不可控事项、发票不能单独证明交货与付款、普通与有限合伙人竞业与自我交易方向相反）。
- **被判定为已有条目覆盖、故本批未收录的典型项及覆盖位置**：
  - 仲裁条款要件化（仲裁合意唯一、仲裁地与机构分离、要件缺失按可能无效）——合同岗第 4.6 段与合同红线清单 F1 已有；
  - 争议解决不得「或裁或审」、管辖须唯一并写到地域与级别、不得违反级别管辖与专属管辖——合同红线清单 F1 已有；
  - 保证人主体资格、保证方式、保证范围与保证期间、主合同变更与债权债务流转对保证的影响、最高额保证要素——合同红线清单 L1–L5 已有；
  - 保理构成要件与资质、应收账款可受让性、转让通知与追索权清算——合同红线清单 L6–L8 已有；
  - 委托事务范围、费用与任意解除权不得以特约排除——合同红线清单 L9 已有；
  - 合伙份额转让的同意门槛与合伙清算顺位——合同红线清单 L10 已有；
  - 股权代持的内部性、处分限制与显名化手续——合同红线清单 L11 已有；
  - 保密信息正反结合界定与期限双情形、技术合同按争议内容定性、涉税发票条款——合同红线清单 L12 与 E 节 E3／E4 已有；
  - 相对方存续与资质核查、签约人授权范围、前置审批与内部决议——合同岗第 4 段第 6 条与合同红线清单 A1–A4 已有；
  - 违约金过分高于损失可被调减、融资租赁不得同时主张全部租金与解除取回——教训选编第一节已有；
  - 涉诉、执行、失信与行政处罚查询作为履约能力预判——合同岗第 4 段第 6 条与合同红线清单 A1 已有；
  - 融资租赁租赁物适格性与所有权登记、租赁物维修义务归属、留购与残值、借款利息与逾期费用的合规上限、债权转让可转让性三查——教训选编第一节与合同红线清单 B／C 节已有。
- **同题不同角度、按未覆盖收录的登记**：教训选编第一节有「从给付单证未交导致买受人无法过户、合同目的不能实现可被解除」（买卖合同项下的从给付义务），与本批第四十五节「发票不能单独证明交货与付款」（证据作用而非义务违反）并非同一判断口径，故按未覆盖收录；合同红线清单 L2 的「增信文件是保证还是债务加入」与本批未采用的草稿「保证书未区分保证责任与债务承担」属同一口径，该草稿条目**未收录**。

### 四、边界

- 本附录记录的是**文本与结构落盘 ＋ 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（新增条目是否会被真的逐项核对、教训是否会被真的对照使用，均需宿主实测）。
- **未做**：本书全本通读（只读合并稿 `43-hetong.md`）；该稿自述缺口未补——`.work/distill-outbox/43-hetong.md` 第 34–38 行（分片编号不连续：`rest-p*` 实存 p1—p31〔无 p32、p33〕、p74—p84、p110—p135、p136—p143、p168—p185、p195—p218，本稿只归并 p136—p143，不代表合同审查类全量条目；同稿另载初稿两处误删已补录、R142 多一行「来源」说明、与既有 01—42 号产出编号连续）均照稿登记，未作推断补写；本书标准立场类条目的单独落盘；书中内容在中国法下的实体正确性复核；条号、期限与比例数字的联网核验（按硬口径不移植，故无核验对象）。
- `43-hetong.md` 与 `.work/` 下历史文件（含 `rest-p136`～`rest-p143` 八份分片）为**只读输入**，本批未修改。

## 附录（2026-09-17）：E21 立场库第一批（合同）落盘（新设 shared/positions/ ＋ 合同岗指针 ＋ 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述各附录的任何结论。
> 口径（硬）：只收合同红线清单 A–L 与合同岗 SKILL 现有口径**未覆盖**的项（逐条比对）；**不写法条编号与具体数字**（门槛、期限、比例、倍数一律改写为规则口径）；不搬原文整句，全部为中文重写；出处只到书名。
> 本批为立场库**第一批**，后批依次为劳动、建工、争议（另行简报），本批不涉及。

### 一、本轮取材（署名）

| # | 书名 | 本批取材稿 | 落盘去向 |
| --- | --- | --- | --- |
| ① | 《合同审查精要与实务指南（第二版）》 | `.work/distill-outbox/01-leiting.md`【标准立场】节（只读） | `shared/positions/contract-positions.md` 各节，末注书名 |
| ② | 《合同审查思维体系与实务技能（第3版）》 | `.work/distill-outbox/43-hetong.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |

**署名风险（必须写明）**：①的书名来自本次任务简报标题，取材稿 `01-leiting.md` 第 1 行的标题载明作者与版次；②的书名来源同前一批（E19 附录第一节已登记：取材稿 `43-hetong.md` 开篇「覆盖与缺口」段未载来源行、未载作者与出版社）。故落盘在书名字段**只写书名与版次，不署作者、不注条号**。

### 二、本轮落盘改动（3 个文件，全部在允许清单内）

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `shared/positions/contract-positions.md` | **新设文件**：合同立场库，共 44 条（限额 ≤50），按十一节分主题（买卖与交付、借款与利息、租赁与融资租赁、建设工程、技术合同、知识产权许可、保理、仓储与委托、合伙与股权代持、涉外与涉税、通用），每条固定「应然＋底线＋出处」三段，行内署名只到书名 | 生产文件；新设目录 `shared/positions/` 下的第一个文件 |
| ② | `roles/contract-counsel/SKILL.md` | 第 3 段「相关检查点见」列表下新增 1 行指针，指向立场库 | 生产文件；`git diff` 为 1 insertion，第 0–7 段与第 4.5、4.6、4.7 段既有条目一字未动，版本号与知识截至日期未改 |
| ③ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- **新设目录说明**：`shared/positions/` 为本次新建目录，用于存放**立场库**（应然标准＋可退让底线两段式口径）。与既有文件的分工：`shared/checklists/` 存必查项与处理方向（检查点是否命中），`shared/positions/` 存该事项上我方主张到什么程度、退到哪里为止（主张强度）。两者不互相替代：立场库条目一律先与合同红线清单 A–L、合同岗 SKILL 各段逐条比对，已有同一判断口径的不收录。
- **未提交、未推送**，改动全部留在工作区；基线提交仍为 `7b055ad`（执行前实测）。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md`、`.work/` 下全部历史文件与本次只读的两份取材稿、合同红线清单、其他岗位文件、既有条目文字与编号均未改。

### 三、去重口径说明

- **唯一的收录标准**：合同红线清单 A–L 与合同岗 SKILL 各段是否已经写过同一判断口径。已覆盖的一律不收录。
- 本批**被判定为已有条目覆盖、故未收录的典型项及覆盖位置**：
  - 责任上限与间接损失排除、兜底赔偿禁止、单方解释权与单方变更权、不可抗力与情势变更、维权成本承担——红线清单 C 节 C1–C6 已有；
  - 合同成立／生效／无效三分与生效条件、签署授权、前置审批、内部决议——红线清单 A 节 A2–A5 与合同岗第 4.7 段第 6 条已有；
  - 履行期限、验收流程与逾期后果、默示放弃权利、通知与异议期限——红线清单 B2、D4、D5 已有；其中「各类期限不得短于发现事实所需」在立场库第 4 条以**质量与检验期限按瑕疵类型分别设定**的角度保留（与 D4 的通用期限口径不同题）；
  - 送达地址与电子送达、管辖唯一、或裁或审、仲裁地与机构分离、仲裁要件缺失——红线清单 F1–F4 与合同岗第 4.6 段已有；
  - 附件效力顺序、口头说明不得覆盖书面约定、多语种优先版本——红线清单 G1–G3 已有；
  - 保密信息正反结合界定与期限双情形、技术合同按争议内容定性、涉税发票条款四项、纳税人身份前置——红线清单 L12 与 E 节 E3／E4 已有；
  - 保证人主体资格、保证方式与共同保证、保证范围与期间、主合同变更与债权债务流转、最高额保证要素——红线清单 L1–L5 已有；
  - 保理构成要件与持牌资质、应收账款不可受让类型、追索权清算义务与中登网查询顺位——红线清单 L6、L8 已有；**转让通知的凭证与留痕要求（红线 L7 已写明「转让通知是否写明主体、方式、凭证清单与送达留痕」及其标准立场与底线）、两种模式的超收归属（红线 L8 已写明「有追索权……剩余必须返还债权人」「无追索权……超收差额归保理人」）均属已有条目覆盖，未收录**；立场库第 31 条只保留红线未写的虚构应收账款与审慎义务边界这一判断口径；
  - 委托事务范围与费用、任意解除权不得以特约排除——红线清单 L9 已有；
  - 合伙份额转让同意门槛与合伙清算顺位——红线清单 L10 已有；
  - 股权代持内部性与显名化手续——红线清单 L11 已有；
  - 相对方存续与资质核查、签约人授权范围、履约能力公开查询——合同岗第 4 段第 6 条与红线清单 A1、A3 已有；
  - 违约金过分高于损失可被调减、违约金以补偿为主、商务取舍归委托人——合同岗第 4.7 段第 8 条与教训选编已有；立场库第 5 条只取「违约形态与救济方式须对应」这一未写过的口径（与合同岗第 4.7 段第 7 条重复的「不接受以一句『依法承担违约责任』概括全部义务」半句已删去，对应项按未覆盖口径登记）；
  - 融资租赁不得同时主张全部租金与解除取回——教训选编已有；立场库第 18 条以「两条清算路径分别写清并写明选择机制」的应然写法保留，语义侧重不同；
  - 保理登记与应收账款质押的顺位规则、保理专户金钱质押——红线清单 L8 已有，未收录。

### 四、边界

- 本附录记录的是**文本与结构落盘 ＋ 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（新增条目是否会被真的逐项引用、指针是否会被真的读取，均需宿主实测）。
- **未做**：两本原书全本通读（只读两份合并稿的【标准立场】节）；`.work/distill-outbox/01-leiting.md` 与 `43-hetong.md` 自述缺口未补——`01-leiting.md` 第 20–24 行（〔P12〕第 20 章 PPP 项目合同仅有标题与内容概览、正文未入本片；〔P3〕〔P12〕原书所附条款示例均为图片不可读；〔P8〕与〔P7〕〔P9〕的重叠部分未重复收录）、`43-hetong.md` 第 34–38 行（分片编号不连续，本稿只归并 p136—p143，不代表合同审查类全量条目；另载初稿两处误删已补录、R142 多一行「来源」说明、与既有 01—42 号产出编号连续）均照稿登记，未作推断补写；两稿【标准立场】节条目的全量落盘（本批按未覆盖口径精选，未逐条分配）；草稿所涉法条编号、门槛、期限与比例数字的联网核验（按硬口径不移植，故无核验对象）；书中内容在中国法下的实体正确性复核；后批劳动、建工、争议三个立场库文件（另行简报，本批未建）。
- `01-leiting.md`、`43-hetong.md` 与 `.work/` 下历史文件为**只读输入**，本批未修改。

## 附录（2026-09-17）：E22 吴汉东《知识产权总论》蒸馏落盘（知产岗脚手架 / 误判教训续编 / 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述各附录的任何结论。
> 口径（硬）：只收现有内容未覆盖项（逐条比对 E15 已落的知产岗 5 条与既有失败模式、教训选编一至四十五节）；**不写法条编号与具体数字**；不搬原文整句，全部为中文重写；出处只到书名，本附录统一署名，不在生产文件逐条署名（教训选编按该文件体例在行末注书名）。

### 一、本轮取材（署名）

| # | 书名 | 本批取材稿 | 落盘去向 |
| --- | --- | --- | --- |
| ① | 吴汉东《知识产权总论》 | `.work/distill-outbox/46-wuhandong.md`（294 条：检查项 87／标准立场 126／误判教训 81；只读） | `roles/ip-counsel/SKILL.md` 第 4 段第 13–22 条 ＋ `shared/cases/practice-lessons.md` 第四十六节 |

**署名风险（必须写明）**：书名取自取材稿自身声明——`46-wuhandong.md` 第 5 行与第 30 行均载明书目名来自该稿分片之一的来源声明（作者与书名）。**该稿九个分片中仅一片声明了所覆盖的书目范围，其余八片未声明来源、书目章节或分片序号**（该稿第 30 行自述），故本次署名只到「书名＋作者」，不代表全部 294 条均出自该书同一章节范围。

### 二、本轮落盘改动（3 个文件，全部在允许清单内）

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `roles/ip-counsel/SKILL.md` | 第 4 段脚手架新增第 13–22 条（10 条：客体定性与非物质性、客体归位、权利归属取得路径、利用方式与限制的效力层次、穷竭与平行进口、救济请求清单、归责原则、权利滥用与竞争法定位、公共领域出口、国际保护原则） | 生产文件；只加条，未改既有 1–12 条与各段结构；版本号与知识截至日期未改 |
| ② | `shared/cases/practice-lessons.md` | 追加第四十六节，共 10 条误判教训，每条末注书名 | 生产文件；纯追加，未改既有四十五节一字 |
| ③ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- 新增条数：知产岗 **10 条**（限额 ≤10）、教训 **10 条**（限额 ≤10）。
- **未提交、未推送**，改动全部留在工作区；基线提交 `9135dc8`（执行前实测）。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md`、`.work/` 下全部历史文件与取材稿、`shared/templates/legal-opinion.md`、`industries/`、红线清单、其他岗位文件、既有条目文字均未改。

### 三、去重口径说明

- **唯一的收录标准**：知产岗第 2 段失败模式与第 4 段既有 1–12 条、教训选编一至四十五节是否已写过同一判断口径。已覆盖的一律不收录。
- **标准立场 126 条的去向**：该稿「标准立场」类条目未单独落盘。既有结构采用「脚手架（怎么做）＋教训（哪里错）」两段式，无「立场」位置；且知产岗第 4 段与教训选编的对应口径已能承载其可执行部分，故按「只收未覆盖项」不另开节、不另建文件。
- 本批**被判定为已有条目覆盖、故未收录的典型项及覆盖位置**：
  - 密点固定、保密措施、三性举证、鉴定与许可类型决定起诉资格——知产岗第 4 段第 9–12 条（E15 已落）已有；
  - 权利状态核查、无效与撤销风险、在先权利冲突——知产岗第 2 段失败模式已有；故本批只保留「在先权利并不当然导致在后权利无效」这一反向口径；
  - 被诉信息与密点的同一性比对、员工知识经验技能的归属区分——知产岗第 4 段第 9、10 条与教训选编第二十五节已有；
  - 权属证据、侵权证据、损失与获利证据的固定路径——知产岗第 4 段第 5 条与证据清单已有；
  - 商业秘密与竞业限制的交叉、保密义务为法定义务——教训选编第二十五、二十六节已有；
  - 专利保护范围解释与权利要求稳定性——知产岗第 2 段失败模式已有，本批未重复。
- **本批有意不落的项**：取材稿中带条号、门槛、期限、比例与年份的条目（如各公约条号、最不发达国家保护推迟年份、生物资源与专利占比、市场支配地位的份额门槛、保护期年限、反不正当竞争法法定类型计数等），按硬口径不移植；涉及地理标志、遗传资源、传统知识、文化主权等国际法与政策类条目，超出本岗「权利取得、维持、行使、许可与归属处分」的范围，本轮未收。

### 四、本附录边界

- 本附录记录的是**文本与结构落盘 ＋ 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（新增脚手架条目是否会被真的按序执行、教训是否会被真的对照使用，均需宿主实测）。
- **未做**：原书全本通读（只读一份合并稿）；取材稿自述缺口未补——`46-wuhandong.md` 第 30–33 行（8 片无来源声明、九片无页码或章节边界、条号与法规名称照引未核、未新增条目）与第 26 行（四组语义相近但非逐字重复的条目并列保留）均照稿登记，未作推断补写；「标准立场」126 条的全量落盘（按未覆盖口径精选）；草稿所涉法条编号、门槛与年份数字的联网核验（按硬口径不移植，故无核验对象）；书中内容在中国法下的实体正确性复核。
- 取材稿与 `.work/` 下历史文件为**只读输入**，本批未修改。

## 附录（2026-09-17）：E23 立场库第二批（劳动）落盘（新设 `shared/positions/labor-positions.md` ＋ 劳动岗指针 ＋ 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述各附录的任何结论。
> 口径（硬）：只收劳动红线清单 A–H 与 L、M、N、O 各节、劳动岗 SKILL 现有口径**未覆盖**的项（逐条比对）；**不写法条编号与具体数字**（门槛、期限、比例、倍数一律改写为规则口径）；不搬原文整句，全部为中文重写，改写判据承接 E18 确立的**连续重合 ≤10 字**（去标点口径，书名署名行除外）；出处只到书名。
> 本批为立场库**第二批**，第一批（合同）见上一附录，后批依次为建工、争议（另行简报），本批不涉及。

### 一、本轮取材（署名）

| # | 书名 | 本批取材稿 | 落盘去向 |
| --- | --- | --- | --- |
| ① | 洪桂彬《HR必备法律工具书（第二版）》 | `.work/distill-outbox/04-hong.md`【标准立场】节（只读） | `shared/positions/labor-positions.md` 各节，末注书名 |
| ② | 刘继承《HR赋能：劳动人事争议预防与处理指南》 | `.work/distill-outbox/05-liu.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ③ | 王桦宇《劳动合同法实务操作与案例精解（增订7版）》 | `.work/distill-outbox/06-wang7.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ④ | 《劳动争议仲裁诉讼实战宝典》 | `.work/distill-outbox/30-baodian.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑤ | 梁镇邦《劳动争议实务案例解析与要点剖析》 | `.work/distill-outbox/31-liangzhenbang.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑥ | 刘秋苏《劳动争议案件35个胜诉策略及实务解析》 | `.work/distill-outbox/32-liuqiusu.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑦ | 洪桂彬《HR全流程法律指南》 | `.work/distill-outbox/33-honghegui.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑧ | 陈轶凡、董润青《HR全流程法律顾问：速查速用全书》 | `.work/distill-outbox/34-sucha.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑨ | 周丽霞《HR全程法律顾问：企业人力资源管理高效工作指南（增订4版）》 | `.work/distill-outbox/35-zhoulixia.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑩ | 肖胜方《劳动合同法下的人力资源管理流程再造（增订4版）》 | `.work/distill-outbox/36-xiaoshengfang.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑪ | 商云《解决劳动争议三步法》 | `.work/distill-outbox/37-shangyun.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑫ | 陈豪《调岗调薪及经济性裁员实操指南》 | `.work/distill-outbox/38-chenhao.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑬ | 温必潜《工伤保险待遇纠纷案例精选》 | `.work/distill-outbox/39-wenbiqian.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑭ | 王勤伟《工伤认定典型案例解析与实务指南》 | `.work/distill-outbox/40-gongshang.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑮ | 《最高人民法院新劳动争议司法解释（二）实务指南》 | `.work/distill-outbox/41-js2zhidao.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑯ | 娄秋琴《这样做HR最有效》 | `.work/distill-outbox/42-louqiuxin.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |

**署名来源与风险（必须写明）**：上表书名的来源分三类，落盘时**只写书名与版次，不署作者、不注条号**（⑧除外——该稿开篇自载两位作者，故随稿登记；①与⑦为同一作者的两本书，随稿分列）。
- 稿内**自带来源行**（书名＋作者）：`05-liu.md` 第 1 行、`06-wang7.md` 第 1 行与第 5 行、`32-liuqiusu.md` 第 5 行（取自 R122）、`33-honghegui.md` 第 1 行、`34-sucha.md` 第 9 行（取自 R6）、`35-zhoulixia.md` 第 5 行、`36-xiaoshengfang.md` 第 5 行、`37-shangyun.md` 第 5 行、`38-chenhao.md` 第 5 行、`39-wenbiqian.md` 第 5 行、`40-gongshang.md` 第 8 行、`42-louqiuxin.md` 第 5 行（取自 R208、R210）。
- 稿内**部分分片声明**：`31-liangzhenbang.md` 第 5 行（书目名取自 R117，R118、R119 亦声明同一书目）；`30-baodian.md` 第 5 行（书目名取自 R110 一片声明的来源）；`41-js2zhidao.md` 第 9 行（仅 R195 自带来源说明，R196、R197、R198 三片未标注书目与章次）。
- 稿内**未载来源行**：`04-hong.md`（该稿第 1 行只有「洪桂彬《HR必备法律工具书（第二版）》蒸馏」，作者与书名随稿载明，无出版社信息）。
- 三稿（`30-baodian.md`、`31-liangzhenbang.md`、`41-js2zhidao.md`）的书名均只由**部分分片**声明，不代表该稿全部条目均出自该书同一章节范围；`41-js2zhidao.md` 所载书名含「（二）」字样，与既有建工类同名司法解释（一）并非同一文件，此处照稿登记、不作推断。

### 二、本轮落盘改动（3 个文件，全部在允许清单内）

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `shared/positions/labor-positions.md` | **新设文件**：劳动立场库，共 50 条（限额 ≤50），按十四节分主题（招录与入职、订立与文本、试用期、无固定期限与续订、变更调岗与调薪、绩效与不胜任、规章制度、工时与休假、工资与报酬、解除与终止、服务期与竞业、社保与工伤、特殊用工与主体、关系中止与举证），每条固定「应然＋底线＋出处」三段，条目序号用中文数字连续编排，行内署名只到书名 | 生产文件；`shared/positions/` 目录下的第二个文件 |
| ② | `roles/employment-counsel/SKILL.md` | 第 3 段「相关检查点见」列表下新增 1 行指针，指向立场库 | 生产文件；`git diff --numstat` 为 `1 0`（1 insertion），第 0–7 段既有条目一字未动，版本号与知识截至日期未改 |
| ③ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- **与合同立场库的体例差异（如实登记）**：合同批按「一、买卖与交付」等**十一个主题编号节**编排（`##` 级节标题带序号），条目号在节内以 `### 1.` 起、跨节连续至 44；本批沿用同一两段式与末注书名的体例，但 `##` 级改用「招录与入职」等**十四个不带序号的大类**，条目序号以 `### 一、` 至 `### 五十、` 逐条编号，便于按条号引用。两库的引用口径一致（产出中写「按劳动立场库第〔条号〕条」并同时引用既有检查点编号）。
- **未提交、未推送**，改动全部留在工作区；基线提交 `704b648`（执行前实测 `git rev-parse --short HEAD`）。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md`、`.work/` 下全部历史文件与本次只读的 16 份取材稿、劳动红线清单、其他岗位文件、既有条目文字与编号均未改。

### 三、去重口径说明

- **唯一的收录标准**：劳动红线清单 A–H 与 L、M、N、O 各节、劳动岗 SKILL 各段是否已经写过同一判断口径。已覆盖的一律不收录。
- 本批**被判定为已有条目覆盖、故未收录的典型项及覆盖位置**：
  - 竞业限制的人员范围、补偿标准与支付周期、期限地域与业务范围、违约金可执行性、保密与竞业的分野——红线清单 E 节 E1–E5 已有；
  - 入职个人信息收集与授权边界、背景调查的授权与时点、录用通知的生效要件与发出前人选核实、撤销前与本人核实及凭未核实材料撤销、员工沟通工具与账号清单——红线清单 M 节 M1–M4 已有（本库第 3 条只取「承诺期限、报到条件与违约责任、已入职后不按撤销处理」这一未写过的角度）；
  - 解除通知的理由固定与事后追加、辞职文书的性质甄别、支付令的书面异议、一事不再罚、录用条件的书面告知与签收——红线清单 N 节 N1、N2、N3、N9、N11 已有；
  - 工伤认定的前提事实与主体资格、三工要件与举证分配、上下班途中四要件、视同工伤链条与排除情形、申报时限起算、认定程序可诉性、未参保的支付主体分层、第三人侵权竞合、工伤赔偿协议效力、行政与民事程序分工、工伤职工岗位与待遇衔接——红线清单 O 节 O1–O12 已有；
  - 派遣的比例与岗位性质、派遣单位资质与合同、义务与责任划分、派遣工保密与竞业、双重劳动关系与兼职、非全日制工时结算与年休假、超龄与特殊身份用工定性、未签书面合同的举证台账与时效——红线清单 L 节 L1–L8 已有；
  - 用工登记与合同订立时点、试用期期限与次数、押金证件与违约金、岗位地点内容与单方调岗兜底、特殊用工形式的责任划分、工资构成与发放日、加班认定与包薪制、特殊工时审批、法定假期待遇、拖欠克扣工资、规章制度的民主程序与公示、违纪情形与兜底条款、处分依据与罚款体罚、手册签收、解除理由的法定性、工会通知程序、补偿与赔偿的核算、代通知金、禁止解除情形、协商解除协议的条款、送达与可举证、社保公积金缴纳、自愿放弃协议、工伤申报时限与待遇路径、商业险理赔、群体性事项定级与集体协商、经济性裁员程序——红线清单 A–H 各节与劳动岗 SKILL 第 4 段第 3–8 条已有；
  - 本库第 50 条「举证责任的分配」与劳动岗 SKILL 第 4 段第 7 条的**证据方案**口径不同题：后者管「现有证据够不够、缺什么、如何合法固定」（取证动作），前者管「处分、除名、解除、减少报酬与工龄计算等决定由用人单位负举证责任，举证不能即承担不利后果」（举证责任归属与后果），故按未覆盖收录。
- **同题不同角度、按未覆盖收录的登记**：
  - 本库第 20 条只取「集团或母公司制度须经各主体各自程序转化、集团制度不得直接适用于下属企业」这一未写过的口径（制度的**适用主体范围**）；该条的「内容合法、程序履行、公示送达三项齐备」与「以员工『应当知道』推定公示」两侧，因红线清单 C1 已就民主程序与公示告知写明同一判断口径（含不接受以「员工应当知道」推定公示），故本库不再重复、已删去；
  - 红线清单 N4 已有「特殊工时制的审批与协商两道手续」，本库第 23 条只取「按批准周期统计总工时并与标准工时对照、工时制度与考勤结算口径对应写明」这一未写过的核算口径，不重复审批要件；
  - 红线清单 N5 已有「考勤方式与所行工时制度匹配」，本库第 23 条的对应半句与该条重合，故第 23 条只保留「结算口径」侧、不另立「考勤匹配」条目。
- **本批有意不落的项**：取材稿中带条号、门槛、期限、比例、倍数与地方口径列举的条目（如经济补偿与竞业补偿的比例与倍数、医疗期与停工留薪期的月数档、裁员的年限与比例门槛、非全日制的工时上限与结算周期、竞业限制期限上限、社保缴费基数上下限、失业保险金领取月数、地方性裁判分歧的逐地列举等），按硬口径不移植；工伤认定与待遇计算类中纯属事实认定与证据取舍的条目（如具体案例的时间点、路线与因果关系判断），超出「主张强度」范围，本轮未收；地方口径分歧类条目（同一问题在不同地区结论相反者）一律不落盘，改由「属地口径核实」提示承载。

### 四、本批自验实测（静态）

判据承接 E18：被测条目去掉末尾出处署名行后的正文，与草稿**两侧同口径**处理（去空白与标点，仅保留汉字、字母与数字），求**连续**相同字符串的最大长度；**达到或超过 10 字判为需要改写**。

```
被测条目：shared/positions/labor-positions.md 全部 50 条（每条＝「应然」行＋「底线」行，出处行不计入）
比对草稿：.work/distill-outbox/ 下 47 份 md（十六份取材稿全文，另 31 份既有合并稿作扩查池）
实测结果：最大连续重合 9 字，≥10 字者 0 条
          （首次自检最大 20 字、≥11 字者 13 条，已按同一判据逐条改写后复跑至 9 字）
条目计数：50 条（限额 ≤50）
三要素：应然／底线／出处齐备 50／50
```

```
新增行法条编号与具体数字（立场库正文 214 行〔条目区非空行，删减后实测〕＋既有文件新增 69 行）：
条号/款/项命中: 0
阿拉伯数字命中（立场库正文）: 0
百分号命中: 0
金额单位命中: 0
门槛/期限/倍数类汉字数字命中: 0
（既有文件新增行中唯一命中「三人」二字，出自「三工要件」一词，非门槛或期限）
```

```
改动范围与基线：
git diff --name-only →
  docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
  roles/employment-counsel/SKILL.md
git status --porcelain →
   M docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
   M roles/employment-counsel/SKILL.md
  ?? .workbuddy-ai/            （任务开始前既有，未动）
  ?? shared/positions/labor-positions.md   （本批新设）
git rev-parse --short HEAD → 704b648（会话内未提交，基线未变）
roles/employment-counsel/SKILL.md 的 git diff --numstat → 1  0
```

### 五、边界

- 本附录记录的是**文本与结构落盘 ＋ 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（新增条目是否会被真的逐项引用、指针是否会被真的读取，均需宿主实测）。
- **未做**：十六本原书全本通读（只读十六份合并稿的【标准立场】节）；十六稿自述缺口未补——`04-hong.md` 第 1 行只有标题行载明作者与书名，无独立来源行与出版社信息；`30-baodian.md` 第 5 行与 `31-liangzhenbang.md` 第 5 行的书名仅由部分分片声明；`40-gongshang.md` 第 10 行载明 R179 与 R180 之间的案例区间不在本批碎片内、R181–R185 未声明覆盖区间且是否整页缺失无法从碎片内部核实；`41-js2zhidao.md` 第 9 行载明仅 R195 自带来源说明、R196–R198 三片未标注所出书目与章次；`36-xiaoshengfang.md` 第 6 行载明 R127–R133 未标注对应章节；`37-shangyun.md` 第 6 行载明 R200、R203、R204 未标注对应章节；`38-chenhao.md` 第 6 行载明 R206 未标注对应章节与页码；`39-wenbiqian.md` 第 6 行载明 R174、R176、R177 未自述范围——均照稿登记，未作推断补写；【标准立场】节条目的全量落盘（本批按未覆盖口径精选，十六稿【标准立场】合计实测 1985 条中收录 50 条）；草稿所涉法条编号、门槛、期限与比例数字的联网核验（按硬口径不移植，故无核验对象）；书中内容在中国法下的实体正确性复核；后批建工、争议两个立场库文件（另行简报，本批未建）。
- 十六份取材稿与 `.work/` 下历史文件为**只读输入**，本批未修改。


## 附录（2026-09-17）：E24 立场库第三批（建工）落盘（新设 `shared/positions/construction-positions.md` ＋ 建工包指针 ＋ 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述各附录的任何结论。
> 口径（硬）：只收建工三清单（施工合同审查清单、竣工结算与安全生产清单、招投标合规清单）与建工包 SKILL 现有口径**未覆盖**的项（逐条比对）；**不写法条编号与具体数字**（门槛、期限、比例、倍数一律改写为规则口径；条目序号用中文数字）；不搬原文整句，全部为中文重写，改写判据承接 E18 确立的**连续重合 ≤10 字**（去标点口径，书名署名行除外）；出处只到书名。
> 本批为立场库**第三批**，前两批（合同、劳动）见前述两附录，后批争议（另行简报），本批不涉及。

### 一、本轮取材（署名）

| # | 书名 | 本批取材稿 | 落盘去向 |
| --- | --- | --- | --- |
| ① | 朱树英《法院审理建设工程案件观点集成（第二版）》 | `.work/distill-outbox/07-guandian.md`【标准立场】节（只读） | `shared/positions/construction-positions.md` 各节，末注书名 |
| ② | 史智军《建设工程合同注释书》 | `.work/distill-outbox/08-zhushi.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ③ | 李舒《建设工程法律实务》 | `.work/distill-outbox/09-lishu.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ④ | 梅俊《施工方追索建设工程款裁判规则与实务指引》 | `.work/distill-outbox/10-meijun.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑤ | 李宏远等《工程总承包（DB/EPC）项目安全生产法务指引》 | `.work/distill-outbox/11-epc-safe.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑥ | 陈津生《建设工程总承包项目招标与投标操作实务》 | `.work/distill-outbox/12-epc-bid.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑦ | 王洋、曹珊《工程总承包项目运作实务》 | `.work/distill-outbox/12-epc-run.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑧ | 张旗等《工程总承包（EPC／DB）争议解决实战攻略》 | `.work/distill-outbox/13-epc-dispute.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑨ | 谢勇《建设工程施工合同案件裁判规则解析》 | `.work/distill-outbox/13-xieyong.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑩ | 北京大成《建设工程纠纷办案手册》 | `.work/distill-outbox/14-dacheng.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑪ | 朱树英、曹珊《最高人民法院建设工程施工合同司法解释（二）：理解适用与实务指南》 | `.work/distill-outbox/15-js2-qa.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |

**署名来源与风险（必须写明）**：上表书名的来源分两类，落盘时**只写书名与版次，不注条号**（作者随书名一并登记，与合同、劳动两批的「不署作者」口径不同——本批书名与作者的对应关系已由 E12 与 E14 两附录逐条核实并更正，本批沿用该已核实的对应关系，未另行推断）。
- 稿内**自带来源行**：`13-xieyong.md` 第 1 行（「# 谢勇《建设工程施工合同案件裁判规则解析》要点合并」）、`13-epc-dispute.md` 第 1 行（「# 蒸馏合并 · 张旗等《工程总承包（EPC／DB）争议解决实战攻略》……」）、`14-dacheng.md` 第 7、9、11 行（该稿自述 P1、P3、P5 三片自带该书名）、`15-js2-qa.md` 第 8 行（仅 P3 自带书名与作者）、`10-meijun.md` 第 3 行、`11-epc-safe.md` 第 3 行。
- 稿内**自带书目但未载作者、作者取自分片来源行**：`12-epc-run.md` 第 12、15 行的覆盖表载明《工程总承包项目运作实务》第二至三章与第五至六章，作者依分片来源行（`.work/distill-frag/epc-run-p2.md` 第 3 行、`epc-run-p7.md` 第 3 行「来源：王洋、曹珊《工程总承包项目运作实务》……」）登记为王洋、曹珊。
- 稿内**未载来源行、书目依分片来源行与 E12／E14 两附录已核实的对应关系登记**：`07-guandian.md`（该稿由 22 个分片合并而成，分片头部逐片标注来源；E12 附录第一节已更正为朱树英《法院审理建设工程案件观点集成（第二版）》）、`08-zhushi.md`（第 1 行标题载明作者与书名）、`09-lishu.md`（第 1 行标题载明作者与书名）、`12-epc-bid.md`（该稿第 1 行标题未载书目，书目依分片来源行 `.work/distill-frag/epc-bid-p3.md` 第 3 行「来源：陈津生《建设工程总承包项目招标与投标操作实务》……」登记，与 E12 附录的更正一致）。
- **同名书不得混用**：`15-js2-qa.md` 所载书名含「（二）」字样，与 E23 批次的 `41-js2zhidao.md`（**劳动争议**司法解释（二）实务指南）并非同一本书，本批照稿登记、不作推断；两稿文件名相近（`15-js2-qa` 与 `41-js2zhidao`），署名**不依文件名**。
- 各稿自述的**碎片级缺口**（`07-guandian.md` P3 分片在第 2.5 节裁判要旨处截断；`08-zhushi.md` 其余 13 片未自带覆盖说明；`09-lishu.md` P2、P4—P13、P16—P17 无片内题注；`10-meijun.md` 八个碎片未标注所出章次；`11-epc-safe.md` 八片中仅 P7 自带来源说明；`12-epc-bid.md` 六个碎片未标注来源章节；`13-epc-dispute.md` P1、P2 未自带来源／覆盖说明；`14-dacheng.md` P2、P4、P6 无来源与覆盖说明；`15-js2-qa.md` 六片无书目与章次标注）**本批未补做**，见第五节边界。

### 二、本轮落盘改动（3 个文件，全部在允许清单内）

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `shared/positions/construction-positions.md` | **新设文件**：建工立场库，共 41 条（限额 ≤50），按八节分主题（工程定性与管辖、效力与合同主体、造价与计价、工期、质量验收与保修、付款交付与附随义务、安全生产、争议解决方式），每条固定「应然＋底线＋出处」三段，条目序号用中文数字连续编排，行内署名只到书名 | 生产文件；`shared/positions/` 目录下的第三个文件 |
| ② | `industries/construction/SKILL.md` | 第 3 段「岗位加载指引」列表内新增 1 行指针，指向立场库 | 生产文件；`git diff --numstat` 为 `1 0`（1 insertion），第 1–4 段与第 5 段既有条目一字未动，版本号与知识截至日期未改 |
| ③ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- **与合同、劳动两批的体例差异（如实登记）**：合同批按十一个主题编号节编排（`##` 级节标题带序号，条目号在节内以 `### 1.` 起、跨节连续）；劳动批的 `##` 级用不带序号的大类、条目序号以中文数字逐条编号。本批沿用**劳动批**的体例：`##` 级为不带序号的大类，条目号以 `### 一、` 至 `### 四十一、` 逐条编号。三库的引用口径一致（产出中写「按建工立场库第〔条号〕条」并同时引用既有检查点编号与行业包锚点）。
- **未提交、未推送**，改动全部留在工作区；基线提交 `7191a69`（执行前实测 `git rev-parse --short HEAD`，实测值与简报所记一致）。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md`、`.work/` 下全部历史文件与本次只读的 11 份取材稿、建工三清单与 `claim-limitation.md`、`migrant-wage.md`、`regulations.md`、其他岗位文件、既有条目文字与编号均未改。

### 三、去重口径说明

- **唯一的收录标准**：建工三清单（施工合同审查清单、竣工结算与安全生产清单、招投标合规清单）与建工包 SKILL 各段是否已经写过同一判断口径；另核对了 `claim-limitation.md`（签证索赔时效表）与 `migrant-wage.md`。已覆盖的一律不收录。
- **去重为程序化实测，非人工印象**：把 41 条条目的正文（「应然」行＋「底线」行，出处行不计入）与上述六个文件的全文分别做**最长连续重合**测算（去标点口径），实测**最大 9 字，达到或超过 10 字者 0 条**。
- 本批**因该实测与覆盖范围核对被判定为不宜收录、故整条换掉的三条（原稿已改，如实登记）**：
  - 原第「验收适用规则按工程类型分别确定」条与结算清单既有条目存在 **28 字**连续重合（片段「阶段并有试运营期要求铁路分静态动态初步安全评估与正式验收」），该口径结算清单「竣工验收与备案」节已完整写明公路／铁路／机场三类的验收阶段划分，属已覆盖项，已整条替换；
  - 原第「质量鉴定事项的提法」条与建工包 SKILL 第 1 段「不覆盖：工程造价鉴定、工程质量鉴定……」的口径相抵，且质量鉴定事项本身超出本包覆盖范围，已整条替换；
  - 原「解除后预期利润的主张强度」条与取材稿存在 **12 字**连续重合（片段「区间酌情支持而非全额支持」），改写至 9 字后复核发现该条实质内容落在企业管理费与预期利润的**支持比例区间**上，与本批「带比例、倍数者不移植」的硬口径不符，故最终整条替换为「局部瑕疵与整体不合格的界分」（该口径经检索确认建工三清单与 SKILL 全文均无命中，属未覆盖项）。
- 本批**被判定为已有条目覆盖、故未收录的典型项及覆盖位置**：
  - 联合体投标的核查（共同投标协议、资质按较低者确定、资格预审后成员不得增减更换、牵头人授权范围）——招投标合规清单「未覆盖项」已有；本库第 九、四十一 条只取「对外连带与内部约定的效力」与「分拆签署的风险」两个未写过的角度；
  - 工程总承包的价格形式与包干边界、风险分担条款应列明的发包人风险项、设计与施工的双资质或联合体要求——施工合同清单「未覆盖项」已有（第 25、26、29 行）；本库第 十二 条只取「招标人作为合同起草人的缺陷责任」这一未写过的角度；
  - 承包范围条款以施工图还是工程量清单为准、包干对象与漏项风险归属——施工合同清单第 21 行已有；本库第 十三 条只取「未完工程的折算路径」这一未写过的角度；
  - 变更、签证与结算文件的签署权限及授权留档——施工合同清单第 17 行已有；本库第 十五 条只取「项目经理与项目部的行为定性（职务行为或表见代理）」这一未写过的角度；
  - 结算协议的效力独立判断、默示结算条款的两项要件与所在文本层级、审计或第三方审价结论结算的约定主体与范围要件、商票未兑付是否计入已付——施工合同清单第 42–43 行与结算清单第 18、23、24、25 行已有；
  - 质保金扣留比例与上限、返还期限与条件、缺陷责任期长短、以房抵顶的过户与付款承诺措辞、质量维修费用抗辩的形式核对、代位权的转分包层级、未竣工未验收即申请结算的原因归属——结算清单第 11–15、20、24、29、30 行已有；
  - 工期变更与安全生产费用是否同步、安全防护与文明施工措施费单独列项并按实计取、安全生产许可证与持证在岗、安全技术交底与隐患排查留痕、分包单位安全协议、事故报告与现场保护、专业分包与劳务分包相对方的安全生产条件核验、总承包模式下的安全生产责任分配——施工合同清单第 31 行与结算清单「安全生产」节第 48–52 行已有；
  - 专用账户、总包代发、工资保证金——`migrant-wage.md` 与行业包锚点 I-3 已有，本库未收录；
  - 索赔意向通知、索赔报告、签证确认、竣工结算、质保金返还与优先受偿权行使期限等**具体时限**——`claim-limitation.md` 已有，本库按硬口径不写数字，仅保留期限**性质**（诉讼时效与除斥期间之分）这一未写过的角度（第二十八条）；
  - 未经验收擅自使用、擅自使用后的质量责任范围、验收是否合格的认定依据——结算清单第 37、41 行与 SKILL 锚点 I-5 已有；
  - 施工许可证不影响合同效力、规划审批手续欠缺的效力与补正节点、资质类的效力补正节点、多份合同的效力逐份审查与中标无效的结算后果——施工合同清单第 37、40 行与 `bidding-compliance.md` 已有（本库第 四、五、六 条只取「折价补偿范围」「不可参照事项」「损失分担与利息性质」三个未写过的角度，效力本身不在本库重复）。
- **本批有意不落的项**：取材稿中带条号、门槛、期限、比例、倍数与地方口径列举的条目（如质保金比例上限、缺陷责任期与保修期月数与年数档、投标保证金比例、履约保函比例、评标权重百分比、安全生产费用计提比例、事故等级与罚款幅度、优先受偿权期限月数、工期压缩幅度上限、不平衡报价调整幅度、企业管理费与预期利润的支持比例区间、显失公平的公平度区间等），按硬口径不移植；纯程序法与证据法条目（管辖异议程序、级别管辖标的额、先行判决要件、反诉受理原则、保全错误的赔偿责任与损失范围、一事不再理与重复起诉的识别、第三人撤销之诉的主体、鉴定费负担的救济途径等），超出「主张强度」范围，本轮未收；地方口径分歧类条目（同一问题在不同地区结论相反者，如优先受偿权能否随债权转让、受让人是否享有、挂靠人能否突破合同相对性）一律不落盘，改由「属地口径核实」提示承载。

### 四、本批自验实测（静态）

判据承接 E18／E23：被测条目去掉末尾出处署名行后的正文，与草稿**两侧同口径**处理（去空白与标点，仅保留汉字、字母与数字），求**连续**相同字符串的最大长度；**达到或超过 10 字判为需要改写**。

```
被测条目：shared/positions/construction-positions.md 全部 41 条（每条＝「应然」行＋「底线」行，出处行不计入）
比对草稿：.work/distill-outbox/ 下 11 份取材稿全文
实测结果：最大连续重合 9 字，≥10 字者 0 条
          （并列最大值 9 字见于多条，片段如「约定的权利义务内容」「工程所在地法院管辖」「价款的约定折价补偿」，
            均出自 07-guandian.md 与 08-zhushi.md，属各该条主张的核心术语，无法再降）
          （首次自检最大 37 字、≥10 字者 39 条，已按同一判据逐条改写、并替换三条不宜收录的条目后复跑至 9 字）
条目计数：41 条（限额 ≤50）
三要素：应然／底线／出处齐备 41／41
```

```
对建工三清单的去重实测（同一判据，比对池为六个文件的全文）：
比对池：construction-contract.md（3158 字符）、completion-settlement-safety.md（2885 字符）、
        bidding-compliance.md（1174 字符）、claim-limitation.md（2972 字符）、
        migrant-wage.md（336 字符）、industries/construction/SKILL.md（3051 字符）
实测结果：最大连续重合 9 字，≥10 字者 0 条
          （片段「审计或财政评审结论」，出自 construction-contract.md）
```

```
新增行法条编号与具体数字（立场库正文 82 行〔应然 41 ＋ 底线 41〕）：
条号/款/项命中: 0
阿拉伯数字命中: 0
百分号命中: 0
金额单位命中: 0
门槛/期限/倍数类汉字数字命中: 0
（宽口径扫描另命中「一份」「一家」「三层」三处，均系量词与层次表述，非门槛、期限或倍数，不计入）
（出处行 41 行为书名署名行，按硬口径豁免，不计入上述统计）
```

```
改动范围与基线：
git diff --name-only →
  docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
  industries/construction/SKILL.md
git status --porcelain →
   M docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
   M industries/construction/SKILL.md
  ?? .workbuddy-ai/            （任务开始前既有，未动）
  ?? shared/positions/construction-positions.md   （本批新设）
git rev-parse --short HEAD → 7191a69（会话内未提交，基线未变，实测值）
industries/construction/SKILL.md 的 git diff --numstat → 1  0
Select-String 'E24' docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md → 有命中（本附录标题行）
```

### 五、边界

- 本附录记录的是**文本与结构落盘 ＋ 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（新增条目是否会被真的逐项引用、指针是否会被真的读取，均需宿主实测）。
- **未做**：十一本原书全本通读（只读十一份合并稿的【标准立场】节）；十一稿自述缺口未补——`07-guandian.md` 第 12 行载明 P3 分片在第 2.5 节裁判要旨处截断、展开部分未含在内；`08-zhushi.md` 第 13 行载明其余 13 片未自带覆盖说明；`09-lishu.md` 第 6 行载明 P2、P4—P13、P16—P17 无片内题注；`10-meijun.md` 第 9 行载明八个碎片未标注所出章次；`11-epc-safe.md` 第 22 行载明八片中仅 P7 自带来源说明；`12-epc-bid.md` 第 15 行载明 P1、P2、P4、P5、P8、P9 六片未标注来源章节、第 18 行载明 P3 自述为「分片之三」故第 5、6 章覆盖可能不完整、第 19 行载明 P6 自述存在自相矛盾处（照原文保留）；`12-epc-run.md` 第 20–27 行载明六类主题未覆盖；`13-epc-dispute.md` 第 7–8 行载明 P1、P2 未自带来源／覆盖说明；`13-xieyong.md` 第 8 行载明各片未标注页码以外的原始章节缺失信息；`14-dacheng.md` 第 13 行载明 P2、P4、P6 无来源与覆盖说明；`15-js2-qa.md` 第 8 行载明六片无书目与章次标注——均照稿登记，未作推断补写；【标准立场】节条目的全量落盘（本批按未覆盖口径精选，十一稿【标准立场】合计实测 1744 条中收录 41 条）；草稿所涉法条编号、门槛、期限与比例数字的联网核验（按硬口径不移植，故无核验对象）；书中内容在中国法下的实体正确性复核；后批争议立场库文件（另行简报，本批未建）。
- 十一份取材稿与 `.work/` 下历史文件为**只读输入**，本批未修改。

## 附录（2026-09-17）：E25 立场库第四批（争议）落盘（新设 `shared/positions/dispute-positions.md` ＋ 争议岗指针 ＋ 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述各附录的任何结论。
> 口径（硬）：只收争议岗 SKILL 各段与证据固定清单（`shared/checklists/evidence-preservation.md`）现有口径**未覆盖**的项（逐条比对）；**不写法条编号与具体数字**（门槛、期限、比例、倍数一律改写为规则口径；条目序号用中文数字）；不搬原文整句，全部为中文重写，改写判据承接 E18 确立的**连续重合 ≤10 字**（去标点口径，书名署名行除外）；出处只到书名。
> 本批为立场库**第四批**，前三批（合同、劳动、建工）见前述三附录；四批合计建成 `shared/positions/` 目录下的四个文件。

### 一、本轮取材（署名）

| # | 书名 | 本批取材稿 | 落盘去向 |
| --- | --- | --- | --- |
| ① | 上海市一中院《开庭：法官思维与庭审实务》 | `.work/distill-outbox/16-kaiting.md`【标准立场】节（只读） | `shared/positions/dispute-positions.md` 各节，末注书名 |
| ② | 《诉讼有方》 | `.work/distill-outbox/17-susongyoufang.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ③ | 王新平《民事诉讼证据运用与实务技巧》 | `.work/distill-outbox/18-wangxinping.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ④ | 易延友《证据法学：原则、规则、案例》 | `.work/distill-outbox/19-yiyanyou.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑤ | 蒋勇《诉讼可视化》 | `.work/distill-outbox/20-keshihua.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑥ | 王国征等《合同纠纷证明责任研究》 | `.work/distill-outbox/21-zhengmingzeren.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑦ | 孙巍《中国商事仲裁法律与实务》 | `.work/distill-outbox/28-sunwei.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ⑧ | 许杰《国际商事仲裁实务》 | `.work/distill-outbox/29-xujie.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |

**署名来源与风险（必须写明）**：上表书名的来源分两类，落盘时**只写书名与版次，不注条号**（作者随书名一并登记）。
- 稿内**自带来源行**：`16-kaiting.md` 第 8 行（仅 P1 自带书目「上海市一中院《开庭：法官思维与庭审实务》」，P2、P3、P4 未标注，本批按该稿自述署名）、`19-yiyanyou.md` 第 1 行、`20-keshihua.md` 第 1 行、`21-zhengmingzeren.md` 第 8 行（仅 P1 自带书目「王国征等《合同纠纷证明责任研究》」）、`28-sunwei.md` 第 3 行、`29-xujie.md` 第 1 行。
- 稿内**未载来源行、书目依 E18 附录已核实的对应关系登记**：`18-wangxinping.md`（书目依 `.work/weread-booklist.md` 补注，见 E18 附录第一节第 ③ 项）。
- **只署书名、未代拟作者**：`17-susongyoufang.md` 第 13 行自述四片均未标注所出书目、作者与章次，「诉讼有方」仅由碎片文件名与 P2 标题体现；本批沿用 E18 的处理，**只署书名、不代拟作者**，属如实登记而非遗漏。
- **同名书不得混用**：`21-zhengmingzeren.md` 与 `18-wangxinping.md` 均涉证明责任，但分属两本书（前者为合同纠纷证明责任研究，后者为民事诉讼证据运用与实务技巧），落盘时按稿分别署名，未作合并。
- 各稿自述的**碎片级缺口**（`16-kaiting.md` 第 8 行 P2、P3、P4 未标注所出书目与章次；`17-susongyoufang.md` 第 13 行四片均未标注书目、作者与章次；`19-yiyanyou.md` 第 5 行六片未自带覆盖说明；`21-zhengmingzeren.md` 第 8 行 P2、P3、P4 未标注所出书目与章次；`28-sunwei.md` 第 42–44 行九片未声明覆盖区间；`29-xujie.md` 第 35–37 行三片未声明覆盖区间、六片间存在内容跳跃）**本批未补做**，见第五节边界。

### 二、本轮落盘改动（3 个文件，全部在允许清单内）

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `shared/positions/dispute-positions.md` | **新设文件**：争议立场库，共 50 条（限额 ≤50），按十一节分主题（主管与管辖、仲裁协议与效力、仲裁程序、裁决救济与执行、准据法与跨境取证、诉讼程序、举证责任与证明标准、证据资格与质证、证人、鉴定与专家、图表与呈现），每条固定「应然＋底线＋出处」三段，条目序号用中文数字连续编排，行内署名只到书名 | 生产文件；`shared/positions/` 目录下的第四个文件 |
| ② | `roles/dispute-counsel/SKILL.md` | 第 3 段「相关检查点见」列表内新增 1 行指针，指向立场库 | 生产文件；`git diff --numstat` 为 `1 0`（1 insertion），第 0–2 段、第 4 段、第 4.6 段、第 4.7 段与第 5–7 段既有条目一字未动，版本号与知识截至日期未改 |
| ③ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- **与前三批的体例差异（如实登记）**：本批沿用**劳动、建工两批**的体例：`##` 级为不带序号的大类，条目号以 `### 一、` 至 `### 五十、` 逐条编号。四库的引用口径一致（产出中写「按争议立场库第〔条号〕条」并同时引用既有检查点编号与风险锚点）。
- **未提交、未推送**，改动全部留在工作区；基线提交 `70b7cec`（执行前实测 `git rev-parse --short HEAD`，实测值与简报所记一致）。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md`、`.work/` 下全部历史文件与本次只读的 8 份取材稿、证据固定清单、合同红线清单、其他岗位文件、既有条目文字与编号均未改。

### 三、去重口径说明

- **唯一的收录标准**：争议岗 SKILL 各段（第 0 段三条硬规则、第 1 段分诊、第 2 段失败模式、第 3 段法律框架、第 4 段分析脚手架、第 4.6 段仲裁程序与裁决后救济、第 4.7 段庭审与举证实务、第 5–7 段交付物与红线）与证据固定清单 A–G 节是否已经写过同一判断口径；已覆盖的一律不收录。
- **去重为程序化实测，非人工印象**：把 50 条条目的正文（「应然」行＋「底线」行，出处行不计入）与上述两个文件的全文分别做**最长连续重合**测算（去标点口径），实测：对争议岗 SKILL **最大 8 字、≥10 字者 0 条**（最大值片段「线条与颜色的含义」）；对证据固定清单**最大 6 字、≥10 字者 0 条**（最大值片段「他人合法权益」）。
- 本批**因与争议岗 SKILL 或证据清单重复、故未收录的典型项及覆盖位置**：
  - 质证的两层结构（先可采性后证明力）、真实性三种表态、只答「三性无异议」视为质证不完整、复印件举证的原件困难核对——证据清单 G 节与争议岗 SKILL 第 4.7 段已有；
  - 逾期举证的次序判断（是否责令说明理由→理由是否成立→故意或重大过失→是否关乎基本事实）——争议岗 SKILL 第 4.7 段已有；
  - 举证责任的法定性、主张责任与证明责任之分、否认与抗辩分开、本证先达标后举证负担才转移——争议岗 SKILL 第 4.7 段已有；本库第 二十九、三十三 条只取「结果责任不随庭审推进移动」「民间借贷的先合意后交付与抗辩／否认分野」两个未写过的角度；
  - 证据清单化与「名称＋形式＋来源＋取得时间＋存放位置＋证明目的」六项登记、原始载体优先、完整性与可验证性、合法取得——证据清单 A、D 节已有；
  - 即时通讯记录截屏要素、聊天对象身份固定须另取实名关联材料、单位证明材料形式要件、证人出庭申请的内容要件——证据清单 G 节已有；本库第 四十四、四十五 条只取「利害关系证言的补强规则」与「公证证言不免除出庭义务」两个未写过的角度；
  - 鉴定意见与评估报告的检材质证、鉴定事项限于专门性事实、报告载明评估基准日——证据清单 G 节已有；本库第 四十七 条只取「程序违法的两档区分」这一未写过的角度；
  - 仲裁程序节点链、仲裁员选定机制、撤销与不予执行两条救济路径的受理法院与衔接、阶段与地位共同决定产出物类型、立案前七维完备性检查——争议岗 SKILL 第 4.6 段已有；本库只取该段未写的角度（管辖决定权归属、保全决定权归属与转递、证据保全申请主体、机构不得代设举证期限、仲裁员披露的分层标准、仲裁地与开庭地之分、书面审理条件、时效抗辩不得主动适用、释明边界、紧急裁令效力）；
  - 出庭准备清单化、按争点预判发问、争议焦点当场核对、法庭调查与辩论分工、调解环节让步与自认分开、可视化服务于阅读者——争议岗 SKILL 第 4.7 段已有；本库第 四十九、五十 条只取「呈送对象的取舍与客观性」「要件事实与证据在图上的呼应」两个未写过的角度；
  - 不可抗力与情势变更的通知义务与举证责任、管辖与仲裁机构须明确唯一、仲裁地写到城市一级、机构与仲裁地为两个独立选择、送达条款覆盖仲裁文书、分层争议解决的前置步骤与出口——合同红线清单 F 节已有，本库未重复；
  - 出庭代理、刑事辩护、专业鉴定、异地执行、涉税事项的转介、以及不代拟诉讼／仲裁文书——争议岗 SKILL 第 0 段第 3 条、第 5 段与第 7 段红线已有，本库未重复。
- **本批有意不落的项**：取材稿中带条号、门槛、期限、比例、倍数与地方口径列举的条目（如裁决期限的月数与天数档、申请撤销与不予执行的期限档、涉外保全管辖的级别划分、仲裁费预交比例、披露请求的时间跨度上限、第三方资助的实缴资本门槛、死亡推定的年限档、多数机构规则的具体条号等），按硬口径不移植；纯境外程序法规则与外国证据法条文（美国《联邦证据规则》各条的分项、CISG 各条编号、IBA 与布拉格规则的条款号、ICC 与贸法会规则的条款号等），超出「主张强度」范围，本轮未收，改由「按受理机构与仲裁地现行规则核实」提示承载；地方口径分歧类条目（同一问题在不同法院结论相反者，如仲裁保全的级别管辖、行为保全是否以情况紧急为要件）一律不落盘，改由「属地口径核实」提示承载；纯学理争议条目（证据法学的学科框架之争、客观真实论与法律真实论之争、证据种类是否应为提示性规范等）不属主张强度口径，本轮未收。

### 四、本批自验实测（静态）

判据承接 E18／E23／E24：被测条目去掉末尾出处署名行后的正文，与草稿**两侧同口径**处理（去空白与标点，仅保留汉字、字母与数字），求**连续**相同字符串的最大长度；**达到或超过 10 字判为需要改写**。

```
被测条目：shared/positions/dispute-positions.md 全部 50 条（每条＝「应然」行＋「底线」行，出处行不计入）
比对草稿：.work/distill-outbox/ 下 8 份取材稿全文（16-kaiting、17-susongyoufang、18-wangxinping、
          19-yiyanyou、20-keshihua、21-zhengmingzeren、28-sunwei、29-xujie）
实测结果：最大连续重合 9 字，≥10 字者 0 条
          （并列最大值 9 字见于 11 条，片段如「结合鉴定意见归纳争」「无效可撤销效力待定」「方同意放弃审级利益」，
            属各该条主张的核心术语，无法再降）
          （改写过程如实登记：初稿自检最大 16 字、≥10 字者 13 条；首轮逐条改写后降至最大 10 字、≥10 字者 2 条；
            补入图表两条后一度回升至最大 18 字、≥10 字者 2 条；再经两轮改写复跑至最大 9 字、超标 0 条）
条目计数：50 条（限额 ≤50）
三要素：应然／底线／出处齐备 50／50
```

```
对争议岗 SKILL 与证据固定清单的去重实测（同一判据，比对池为两个文件的全文）：
比对池：roles/dispute-counsel/SKILL.md（7530 字符）、shared/checklists/evidence-preservation.md（2298 字符）
实测结果：对 SKILL 最大连续重合 8 字、≥10 字者 0 条（片段「线条与颜色的含义」）
          对证据清单最大连续重合 6 字、≥10 字者 0 条（片段「他人合法权益」）
```

```
新增行法条编号与具体数字（立场库正文 100 行〔应然 50 ＋ 底线 50〕）：
条号/款/项命中: 0
阿拉伯数字命中: 0
百分号命中: 0
金额单位命中: 0
门槛/期限/倍数类汉字数字命中: 0
（宽口径扫描另命中「成立」「达成」「当成」「再次」「日常」「上限」等 26 处，
  逐处核对上下文均为词素而非门槛、期限或倍数；「上限」一处系「以仲裁请求为上限」的范围表述，
  不含具体数值，不计入）
（出处行 50 行为书名署名行，按硬口径豁免，不计入上述统计）
```

```
改动范围与基线：
git diff --name-only →
  docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
  roles/dispute-counsel/SKILL.md
git status --porcelain →
   M docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
   M roles/dispute-counsel/SKILL.md
  ?? .workbuddy-ai/            （任务开始前既有，未动）
  ?? shared/positions/dispute-positions.md   （本批新设）
git rev-parse --short HEAD → 70b7cec（会话内未提交，基线未变，实测值）
roles/dispute-counsel/SKILL.md 的 git diff --numstat → 1  0
Select-String 'E25' docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md → 有命中（本附录标题行）
```

### 五、边界

- 本附录记录的是**文本与结构落盘 ＋ 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（新增条目是否会被真的逐项引用、指针是否会被真的读取，均需宿主实测）。
- **未做**：八本原书全本通读（只读八份合并稿的【标准立场】节）；八稿自述缺口未补——`16-kaiting.md` 第 8 行载明仅 P1 自带来源说明、P2—P4 未标注所出书目与章次；`17-susongyoufang.md` 第 13 行载明四片均未标注所出书目、作者与章次；`19-yiyanyou.md` 第 5 行载明六片未自带覆盖说明；`21-zhengmingzeren.md` 第 8 行载明仅 P1 自带来源说明、P2—P4 未标注所出书目与章次；`28-sunwei.md` 第 42–44 行载明九片未声明覆盖区间；`29-xujie.md` 第 35–37 行载明三片未声明覆盖区间、六片间存在明显内容跳跃——均照稿登记，未作推断补写；【标准立场】节条目的全量落盘（本批按未覆盖口径精选，八稿【标准立场】合计实测 652 条中收录 50 条）；草稿所涉法条编号、门槛、期限与比例数字的联网核验（按硬口径不移植，故无核验对象）；书中内容在中国法下的实体正确性复核；八稿【检查项】【误判教训】两类的落盘（不在本批取材范围）。
- 八份取材稿与 `.work/` 下历史文件为**只读输入**，本批未修改。

---

## 附录（2026-09-17）：民法典释义核验底本 · 生产文件条号引用存在性核验（只核验，不改写）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述各附录的任何结论。
> 口径（硬）：只做条号存在性与编章归属核验；**不评价释义观点**；**不搬运释义原文整句**（本附录引底本一律只给行号，不给整句）；底本无载明的一律记「底本未载明」，**不推断**。

### 一、底本、对象与结论口径

- 底本（只读，本批未修改）：`.work/distill-inbox/weread-exporter/output/中华人民共和国民法典释义及适用指南.md`，实测 5,299,863 字节 / 24,272 行（`Get-Content -ReadCount 0` 计；行尾换行另计）。底本第 24 行载明该法典由七个编构成、条文总量为 1260 条。
- 核验对象（**只读比对，未改一字**）：`roles/`、`shared/`、`industries/` 下全部 md，共 32 个文件。
- 结论四值（前三值即简报要求的三态）：

| 结论 | 判据 | 附随要求 |
| --- | --- | --- |
| **存在** | 底本有该条号，且生产文件所述编章归属与底本一致 | 必附底本行号 |
| **不存在** | 底本无该条号 | 必附检索式与命中数 |
| **存疑** | 底本有该条号，但生产文件所述编章归属或要点不能确证 | 必附底本位置 |
| **底本未载明** | 引用对象不是民法典（司法解释／行政法规／部门规章／其他法律），本底本作为民法典释义书不含其条文 | 必附负向检索留痕 |

- 底本自述体量核验：以行首「第X条」为条头计，底本实测 **1203** 个条头，最大条号 1260、最小 1，**无重复条头**；其余 57 条无独立条头、仅在其他条的释义中被交叉引用（实测这 57 条全文均有 ≥1 处出现，**1–1260 全部可达**）。

### 二、清单计数（脚本实测）

```
扫描文件数                = 32
「第X条」形式原始命中     = 67   （含「第N–M条」区间式 1 条、「第X、Y条」并列式 1 条）
减：段内自指（非法律引用） = 9    （见 §五，逐条给依据）
核验清单条数              = 58
```

脚本口径：对 32 个文件逐行全匹配，先取区间式 `第N–M条`、并列式 `第X、Y条`，再取单式 `第[0-9一二三四五六七八九十百千零两]+条`（含全角／半角空格变体），三者不重复计数。技能内部「第 4 段第 9 条」一类段内自指不计入法条引用。

### 三、核验清单（58 条，逐条结论）

| # | 位置（文件:行） | 条号 | 引用对象 | 结论 | 底本位置／检索留痕 |
| --- | --- | --- | --- | --- | --- |
| 1 | industries/construction/SKILL.md:26 | 第41条 | 解释一第41条 | 底本未载明 | §四-N1 |
| 2 | industries/construction/SKILL.md:26 | 第21条 | 解释二第21条 | 底本未载明 | §四-N1 |
| 3 | industries/construction/SKILL.md:37 | 第一条 | 解释二第一条 | 底本未载明 | §四-N1 |
| 4 | industries/construction/SKILL.md:38 | 第二条 | 解释二第二条 | 底本未载明 | §四-N1 |
| 5 | industries/construction/SKILL.md:39 | 第三条 | 解释二第三条 | 底本未载明 | §四-N1 |
| 6 | industries/construction/SKILL.md:40 | 第四条 | 解释二第四条 | 底本未载明 | §四-N1 |
| 7 | industries/construction/SKILL.md:41 | 第五条 | 解释二第五条 | 底本未载明 | §四-N1 |
| 8 | industries/construction/SKILL.md:42 | 第六条 | 解释二第六条 | 底本未载明 | §四-N1 |
| 9 | industries/construction/SKILL.md:43 | 第七条 | 解释二第七条 | 底本未载明 | §四-N1 |
| 10 | industries/construction/SKILL.md:44 | 第八条 | 解释二第八条 | 底本未载明 | §四-N1 |
| 11 | industries/construction/SKILL.md:45 | 第九条 | 解释二第九条 | 底本未载明 | §四-N1 |
| 12 | industries/construction/SKILL.md:46 | 第十条 | 解释二第十条 | 底本未载明 | §四-N1 |
| 13 | industries/construction/SKILL.md:47 | 第十一条 | 解释二第十一条 | 底本未载明 | §四-N1 |
| 14 | industries/construction/SKILL.md:48 | 第十二条 | 解释二第十二条 | 底本未载明 | §四-N1 |
| 15 | industries/construction/SKILL.md:49 | 第十三条 | 解释二第十三条 | 底本未载明 | §四-N1 |
| 16 | industries/construction/SKILL.md:50 | 第十四条 | 解释二第十四条 | 底本未载明 | §四-N1 |
| 17 | industries/construction/SKILL.md:51 | 第十五条 | 解释二第十五条 | 底本未载明 | §四-N1 |
| 18 | industries/construction/SKILL.md:52 | 第十六条 | 解释二第十六条 | 底本未载明 | §四-N1 |
| 19 | industries/construction/SKILL.md:53 | 第十七条 | 解释二第十七条 | 底本未载明 | §四-N1 |
| 20 | industries/construction/SKILL.md:54 | 第十八条 | 解释二第十八条 | 底本未载明 | §四-N1 |
| 21 | industries/construction/SKILL.md:55 | 第十九条 | 解释二第十九条 | 底本未载明 | §四-N1 |
| 22 | industries/construction/SKILL.md:56 | 第二十条 | 解释二第二十条 | 底本未载明 | §四-N1 |
| 23 | industries/construction/SKILL.md:57 | 第二十一条 | 解释二第二十一条 | 底本未载明 | §四-N1 |
| 24 | industries/construction/SKILL.md:58 | 第二十二条 | 解释二第二十二条 | 底本未载明 | §四-N1 |
| 25 | industries/construction/SKILL.md:59 | 第二十三条 | 解释二第二十三条 | 底本未载明 | §四-N1 |
| 26 | industries/construction/checklists/claim-limitation.md:14 | 第10条 | 解释一第10条 | 底本未载明 | §四-N1 |
| 27 | industries/construction/checklists/claim-limitation.md:14 | 第20条 | 解释一第20条 | 底本未载明 | §四-N1 |
| 28 | industries/construction/checklists/claim-limitation.md:15 | 第21条 | 解释一第21条 | 底本未载明 | §四-N1 |
| 29 | industries/construction/checklists/claim-limitation.md:16 | 第二条 | 质量保证金管理办法第二条 | 底本未载明 | §四-N5 |
| 30 | industries/construction/checklists/claim-limitation.md:16 | 第十一条 | 质量保证金管理办法第十一条 | 底本未载明 | §四-N5 |
| 31 | industries/construction/checklists/claim-limitation.md:16 | 第17条 | 解释一第17条 | 底本未载明 | §四-N1 |
| 32 | industries/construction/checklists/claim-limitation.md:17 | 第41条 | 解释一第41条 | 底本未载明 | §四-N1 |
| 33 | industries/construction/checklists/claim-limitation.md:17 | 第21条 | 解释二第21条 | 底本未载明 | §四-N1 |
| 34 | industries/construction/checklists/claim-limitation.md:18 | 第四十条 | 建设工程质量管理条例第四十条 | 底本未载明 | §四-N4 |
| 35 | industries/construction/checklists/claim-limitation.md:36 | 第10条 | 解释一第10条 | 底本未载明 | §四-N1 |
| 36 | industries/construction/checklists/claim-limitation.md:36 | 第17条 | 解释一第17条 | 底本未载明 | §四-N1 |
| 37 | industries/construction/checklists/claim-limitation.md:36 | 第20条 | 解释一第20条 | 底本未载明 | §四-N1 |
| 38 | industries/construction/checklists/claim-limitation.md:36 | 第21条 | 解释一第21条 | 底本未载明 | §四-N1 |
| 39 | industries/construction/checklists/claim-limitation.md:36 | 第41条 | 解释一第41条 | 底本未载明 | §四-N1 |
| 40 | industries/construction/checklists/claim-limitation.md:37 | 第13条 | 解释二第13条 | 底本未载明 | §四-N1 |
| 41 | industries/construction/checklists/claim-limitation.md:37 | 第21条 | 解释二第21条 | 底本未载明 | §四-N1 |
| 42 | industries/construction/checklists/claim-limitation.md:40 | 第二条 | 质量保证金管理办法第二条 | 底本未载明 | §四-N5 |
| 43 | industries/construction/checklists/claim-limitation.md:40 | 第十一条 | 质量保证金管理办法第十一条 | 底本未载明 | §四-N5 |
| 44 | industries/construction/checklists/claim-limitation.md:41 | 第四十条 | 建设工程质量管理条例第四十条 | 底本未载明 | §四-N4 |
| 45 | industries/construction/checklists/claim-limitation.md:41 | 第四十条 | 建设工程质量管理条例第四十条 | 底本未载明 | §四-N4 |
| 46 | industries/construction/checklists/completion-settlement-safety.md:8 | 第41条 | 解释一第41条 | 底本未载明 | §四-N1 |
| 47 | industries/construction/checklists/completion-settlement-safety.md:8 | 第21条 | 解释二第21条 | 底本未载明 | §四-N1 |
| 48 | industries/construction/checklists/completion-settlement-safety.md:10 | 第13条 | 解释二第13条 | 底本未载明 | §四-N1 |
| 49 | industries/construction/checklists/construction-contract.md:10 | 第13条 | 解释二第13条 | 底本未载明 | §四-N1 |
| 50 | industries/construction/regulations.md:9 | 第788–808条 | 民法典第三编第十八章建设工程合同 | **存在** | 底本 L9001（第三编 合同）、L11665（第二分编 典型合同）、L15011（第十八章 建设工程合同）、L15014（章首载明本章共二十一条）、L15016–L15396（788 至 808 共 21 个条头逐条在位）、L15415（第十九章 运输合同，起于 809）；编章归属与生产文件一致 |
| 51 | industries/construction/regulations.md:9 | 第七百八十八条 | 民法典第七百八十八条 | **存在** | 底本 L15016（条头在位）；编章归属：第三编第十八章，与生产文件一致 |
| 52 | industries/construction/regulations.md:10 | 第八百零七条 | 民法典第八百零七条 | **存在** | 底本 L15356（条头在位）、L15358（条文主旨行）；编章归属：第三编第十八章建设工程合同，与生产文件一致 |
| 53 | industries/construction/regulations.md:10 | 第四十一条 | 解释一第41条 | 底本未载明 | §四-N1 |
| 54 | industries/construction/regulations.md:10 | 第二十一条 | 解释二第21条 | 底本未载明 | §四-N1 |
| 55 | industries/construction/regulations.md:11 | 第八十五条 | 建筑法第八十五条 | 底本未载明 | §四-N2 |
| 56 | industries/construction/regulations.md:12 | 第六十八条 | 招标投标法第六十八条 | 底本未载明 | §四-N3 |
| 57 | industries/construction/regulations.md:18 | 第二十八条 | 工程总承包管理办法第二十八条 | 底本未载明 | §四-N6 |
| 58 | industries/construction/regulations.md:19 | 第二十三条 | 解释二第二十三条 | 底本未载明 | §四-N1 |

### 四、底本未载明的负向检索留痕（非推断）

| 标签 | 引用对象 | 底本检索式 | 命中数 | 命中位置与判读 |
| --- | --- | --- | --- | --- |
| N1 | 法释〔2020〕25号（解释一）／法释〔2026〕12号（解释二） | `法释` / `解释二` / `解释一` | 0 / 0 / 2 | 底本全文无「法释」字样、无「解释二」字样；2 处「解释一」经逐处核对均为词素（L412 系「该司法解释一方面」这一表述，L2892 系合同解释方法论述中的词素），非司法解释简称。底本另出现「司法解释第X条」11 处（L3906、L3994×2、L11756×2、L11790、L11852、L11910、L11926、L12436×2），指向民法通则意见、诉讼时效规定、买卖合同解释等他项司法解释，**均非解释一／解释二条号**。L15112 一处提及建设工程施工合同司法解释书名，同段内仅有「第799条」「第61条」两个条号，**未给该解释条号** |
| N2 | 建筑法 | `建筑法第[条号]条` | 1 | 仅 L15112「建筑法第61条」。另有转引式 2 处（L5624、L5742 的「该法第39条第2款」「第40条」）。**无第八十五条** |
| N3 | 招标投标法 | `招标投标法第[条号]条` | 0 | 底本提及招标投标法共 3 处，分布于 L12468（1 处）、L15054（2 处），均为「招标投标的方式／买卖」表述，**均无条号**。**无第六十八条** |
| N4 | 建设工程质量管理条例 | `质量管理条例第[条号]条` | 0 | 底本 5 处提及该条例（L12132、L15186、L15232、L15242、L15254），均无条号。**无第四十条** |
| N5 | 建设工程质量保证金管理办法 | `建设工程质量保证金管理办法` | 0 | 底本全文无该办法字样。连带检索：`缺陷责任期` 0、`质量保证金` 0、`保修金` 0 |
| N6 | 房屋建筑和市政基础设施项目工程总承包管理办法 | `工程总承包管理办法` | 0 | 底本全文无该办法字样 |

### 五、排除项：段内自指 9 条（非法律条号引用）

| # | 位置（文件:行） | 条号 | 判定依据 |
| --- | --- | --- | --- |
| E1 | roles/contract-counsel/SKILL.md:60 | 第9条 | 同句自注「本条之前先做第 9 条」，指第 4 段第 9 项 |
| E2 | roles/contract-counsel/SKILL.md:61 | 第8条 | 「范围裁定（新增工作／量增）见第 8 条」，指第 4 段第 8 项 |
| E3 | roles/contract-counsel/SKILL.md:70 | 第2条 | 「用于界定第 2 条审查范围」，指第 4 段第 2 项 |
| E4 | roles/contract-counsel/SKILL.md:85 | 第1–3条 | 「第 1–3 条产出的条目落《审查意见书》」，指第 4.5 段第 1–3 项 |
| E5 | roles/contract-counsel/SKILL.md:89 | 第9、10条 | 「两者交付物与产出顺序不同（见第 9、10 条）」，指第 4.6 段第 9、10 项 |
| E6 | roles/contract-counsel/SKILL.md:150 | 第7条 | 「第 4 段第 7 条的「让步序列」」 |
| E7 | roles/contract-counsel/SKILL.md:150 | 第8条 | 「与第 8 条的「范围裁定五档 + 重大性测试」」 |
| E8 | roles/dispute-counsel/SKILL.md:77 | 第3条 | 「本条只适用于第 0 段第 3 条与第 6 段列明的转介情形」 |
| E9 | shared/templates/org-profile.md:55 | 第9条 | 「见 `roles/contract-counsel/SKILL.md` 第 4 段第 9 条」 |

### 六、附注：未计入 58 条清单的条号书写形式

`industries/construction/regulations.md:9` 状态备注含裸条号列举「（788、791、799、802等）」，书写形式不是「第X条」，脚本未计入清单。逐条回底本核验：

| 裸条号 | 底本位置 | 结论 |
| --- | --- | --- |
| 788 | L15016 | 存在（第三编第十八章） |
| 791 | L15060 | 存在（第三编第十八章） |
| 799 | L15218 | 存在（第三编第十八章） |
| 802 | L15278 | 存在（第三编第十八章） |

### 七、超出三态的备注（1 条，不计入三态统计，只记录不改写）

- `industries/construction/regulations.md:10` 要点栏「行使期限有法定限制，逾期失权」写在民法典第八百零七条行内。底本 L15390 把优先受偿权的行使期限列为立法中的**争议问题**，即民法典本条**未规定**该期限（底本 L15390 所在段落标题即「优先受偿权的行使期限」）；底本全文检索 `十八个月` 命中 **0**。故该「法定限制」不能归于民法典第八百零七条本身；其来源即该行状态备注自述的法释〔2020〕25号第四十一条／法释〔2026〕12号第二十一条，二者底本均未载明（§四-N1）。
- 按简报口径，**只记录，不改写既有条目**（发现错误另开修复任务）。

### 八、三态分布（实测）

```
核验清单条数 = 58
  存在         =  3    （#50 #51 #52，全部为民法典条号）
  不存在       =  0
  存疑         =  0    （本轮无「底本有条号但编章归属或要点不能确证」的条目）
  底本未载明   = 55
      其中 司法解释（解释一 13 ＋ 解释二 32）           = 45
           行政法规（建设工程质量管理条例）             =  3
           部门规章／部门规范性文件（质量保证金办法 4 ＋
                     工程总承包管理办法 1）              =  5
           其他法律（建筑法 1 ＋ 招标投标法 1）          =  2
另：排除项（段内自指）9 条；附注裸条号 4 条（均存在）；超三态备注 1 条。
```

### 九、边界

- 本附录只做**条号存在性与编章归属**核验，**不评价释义观点**，不证明生产文件所引条文内容在中国法下的实体正确性。
- 底本为 2020 年 7 月出版的民法典释义书，**不含司法解释、行政法规、部门规章与其他法律条文**，故 58 条清单中 55 条只能记「底本未载明」——这不是「不存在」，也不构成对它们的否定；它们仍需各自法源核验。
- 底本 57 条无独立条头（仅被交叉引用）。本轮清单内无条号落在这些条上；若后续引用落在其上，需换底本。
- 本轮「不存在」0 条、「存疑」0 条，**不等于生产文件全部引用已核验通过**：已确证存在的仅 3 条（民法典 788–808 区间、788、807），其余 55 条待各自法源核验。
- **未做**：生产文件既有文字的任何改写（按简报只追加本附录）；底本全本通读；司法解释、行政法规、部门规章、其他法律条号的联网核验；释义观点与生产文件要点的实体比对。

---

## 附录（2026-09-17）：E26 立场库第五批（治理）落盘（新设 `shared/positions/governance-positions.md` ＋ 治理岗指针 ＋ 验证记录）

> 本附录为**追加记录**，不改变前文第 1–5 节、两处补记与前述各附录的任何结论。
> 口径（硬）：只收治理岗 SKILL 各段现有口径**未覆盖**的项（逐条比对）；**不写法条编号与具体数字**（门槛、期限、比例、倍数一律改写为规则口径；条目序号用中文数字）；不搬原文整句，全部为中文重写，改写判据承接 E18 确立的**连续重合 ≤10 字**（去标点口径，书名署名行除外）；出处只到书名。
> 本批为立场库**第五批**，前四批（合同、劳动、建工、争议）见前述四附录；五批合计建成 `shared/positions/` 目录下的五个文件。

### 一、本轮取材（署名）

| # | 书名 | 本批取材稿 | 落盘去向 |
| --- | --- | --- | --- |
| ① | 朱锦清《公司法学（上）》 | `.work/distill-outbox/22-gsf-shang.md`【标准立场】节（只读） | `shared/positions/governance-positions.md` 各节，末注书名 |
| ② | 朱锦清《公司法学（下）》 | `.work/distill-outbox/23-gsf-xia.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |
| ③ | 朱慈蕴《新公司法条文精解》 | `.work/distill-outbox/24-xgsf.md`【标准立场】节（只读） | 同文件对应条目，末注书名 |

**署名来源与风险（必须写明）**：上表书名与作者的对应关系承接 **E15 附录第一节**已核实的登记，本批未另行推断。
- 稿内**自带来源行**：`23-gsf-xia.md` 第 9–13 行（P2、P4、P5、P10、P13 五片自带「朱锦清《公司法学（下）》」书名与章次）；`24-xgsf.md` 第 1 行标题载明书名，其碎片 `xgsf-p1.md` 第 3 行、`xgsf-p2.md` 第 3 行、`xgsf-p4.md` 第 3 行、`xgsf-p7.md` 第 3 行亦自带「朱慈蕴《新公司法条文精解》」书名。
- 稿内**未载来源行、书目依 E15 附录已核实的对应关系登记**：`22-gsf-shang.md`（该稿第 5、35 行仅自述由 12 个分片归并，未载书目；12 个分片逐片复核首行，均无来源行，文中亦无书名出现。书目依 E15 附录第一节第 ① 项登记）。
- **本批未做**：三稿书目与作者的重新核实（承接 E15 已核实的对应关系）；`24-xgsf.md` 未声明覆盖条段的补做。
- 各稿自述的**碎片级缺口**（`22-gsf-shang.md` 第 31–33 行载明无逐字重复可删、语义相近条目已保留；`23-gsf-xia.md` 第 8 行载明 P1、P3、P6、P7、P8、P9、P11、P12 八片未标注所出书目、章次与案例编号；`24-xgsf.md` 第 28–29 行载明 P3、P5、P6、P8 四片未声明所覆盖的法条范围、另有三个条段无碎片声明覆盖）**本批未补做**，见第五节边界。

### 二、本轮落盘改动（3 个文件，全部在允许清单内）

| # | 文件 | 改动内容 | 性质 |
| --- | --- | --- | --- |
| ① | `shared/positions/governance-positions.md` | **新设文件**：治理立场库，共 50 条（限额 ≤50），按十三节分主题（公司类型与章程自治、设立与发起人、出资与资本、股权转让与股东资格、股东权利与知情权、股东会与决议、董事会与董监高、关联交易公司机会与竞业、控股股东与少数派保护、合并分立与重大变更、分配减资与公积金、解散清算与退出、登记公示与责任），每条固定「应然＋底线＋出处」三段，条目序号用中文数字连续编排，行内署名只到书名 | 生产文件；`shared/positions/` 目录下的第五个文件 |
| ② | `roles/governance-counsel/SKILL.md` | 第 3 段「相关检查点见」列表内新增 1 行指针，指向立场库 | 生产文件；`git diff --numstat` 为 `1 0`（1 insertion），第 0–2 段、第 4 段与第 5–7 段既有条目一字未动，版本号与知识截至日期未改 |
| ③ | `docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md` | 本附录 | 追加记录，不改前文 |

- **与前述各批的体例差异（如实登记）**：本批沿用**劳动、建工、争议三批**的体例：`##` 级为不带序号的大类，条目号以 `### 一、` 至 `### 五十、` 逐条编号。五库的引用口径一致（产出中写「按治理立场库第〔条号〕条」并同时引用既有检查点编号与风险锚点）。
- **未提交、未推送**，改动全部留在工作区；基线提交 `74f39c5`（执行前实测 `git rev-parse --short HEAD`，实测值与简报所记一致）。
- 禁止触碰文件核对：`outputs/external-review-39abd04.md`、`.work/` 下全部历史文件与本次只读的 3 份取材稿、合同红线清单、证据固定清单、其他岗位文件、既有条目文字与编号均未改。

### 三、去重口径说明

- **唯一的收录标准**：治理岗 SKILL 各段（第 0 段三条硬规则、第 1 段分诊、第 2 段失败模式、第 3 段法律框架、第 4 段分析脚手架第 1–19 条、第 5–7 段交付物与红线）是否已经写过同一判断口径；已覆盖的一律不收录。
- **去重为程序化实测，非人工印象**：把 50 条条目的正文（「应然」行＋「底线」行，出处行不计入）与治理岗 SKILL 全文做**最长连续重合**测算（去标点口径），实测**最大 8 字、≥10 字者 0 条**（最大值片段「以股东会决议直接」，属该条主张的核心术语，无法再降）。
- 本批**因与治理岗 SKILL 重复、故未收录的典型项及覆盖位置**：
  - 决议不成立、无效与可撤销的三分与各自救济、时限——治理岗 SKILL 第 4 段第 15 条已有；
  - 章程或股东会意定限制不得对抗善意相对人、法定限制可对抗且由相对人自证已作合理审查——第 4 段第 16 条已有；
  - 出资义务的约定性与法定性、公司不能清偿到期债务时未届期股东可能被要求提前缴纳、请求只能针对未实缴股东——第 4 段第 9 条已有；
  - 催缴与失权程序（须事先催缴并留出宽限期、不得以股东会决议直接除名）——第 4 段第 9 条已有；
  - 形式减资与实质减资之分、免除股东未届期出资义务须走完整债权人保护程序、违法减资的赔偿责任主体与董监高的责任前提——第 4 段第 10 条已有（本库第 四十四 条只取「形式减资可否超亏损额」这一未写过的角度）；
  - 合并、分立与重大资产处分的交易性质判断、合并分立后债务由存续或新设公司承继、主张合并无效或解散公司的事实要件——第 4 段第 11 条已有；
  - 清算义务人、清算组通知与公告义务、清算方案确认与报告备案、未经确认的清算方案致损的赔偿、强制退出各类处置的法律后果、简易注销后股东承诺不实的责任、清算期间法人资格未消灭——第 4 段第 12 条已有；
  - 登记住所的公信力、股东名册的推定与对抗效力、公司依名册履行通知送达即可免责、登记机关的股权登记属对抗要件而非取得要件、股权转让通知义务履行状态不影响转让合同效力——第 4 段第 13 条已有；
  - 董事会与股东会决议的计算基数、未出席者计入相应基数、监事召集股东会的前置提议环节、决议解散后再请求判决解散缺乏依据——第 4 段第 14 条已有；
  - 董监高忠实义务与勤勉义务分别核对事实要件、一般过失不升格为违反忠实义务、对第三人的个人责任限于故意或重大过失、控股股东与实际控制人及事实董事一并核对——第 4 段第 8 条已有（本库第 二十六、二十七、二十九 条只取「注意义务的客观尺子」「监督义务的下限」「异议登记与免责」三个未写过的角度）；
  - 公司形式变更、股权变更、登记事项未及时变更等瑕疵不构成拒担既有债务的理由——第 4 段第 17 条已有；
  - 股东知情权事项须同步核对商业秘密与第三方信息、股东退出事项须同步核对回购请求权触发情形与异议表示的对应关系、代持与冒名登记对主体资格认定的影响及个案差异——第 4 段第 18 条已有（本库第 十六、十七、十八 条只取「查阅权的分层」「两类查阅的举证分配」「正当目的的边界」三个未写过的角度）；
  - 各类前置材料留痕（会议通知、会议记录、决议附页、章程对照表、债权人通知与公告留痕、清算文件）——第 4 段第 19 条已有；
  - 通知期限、召集主体、提案方式、表决比例与回避表决的逐项核对、混淆股东会与董事会权限、忽略章程或股东协议对股权转让的限制、投融资只审估值而忽略清算优先与回购触发与反稀释与一票否决与董事会席位、股权代持风险未提示、减资合并分立忽略债权人保护程序、忽略工商变更与章程备案的材料时限与签字要求——第 2 段失败模式已有；
  - 不查章程与登记状态就按法定默认规则出方案（章程另有约定优先适用）——第 2 段失败模式与第 4 段第 2 条已有；
  - 伪造签名、倒签决议、补签日期、虚构会议记录、隐瞒代持与抽逃出资与虚假出资与虚假登记、规避外资准入与国资监管与经营者集中申报、代办或提交虚假工商登记材料、承诺决议效力判断结论、无检索能力时以记忆冒充已核验法条、输出量化风险分或颜色词等级——第 7 段红线与第 0 段三条硬规则已有；
  - 国资、外资准入、经营者集中申报、境外架构安排、股东知情权与决议效力与股东代表诉讼争议、股权激励用工侧安排的转介与协同——第 6 段升级条件已有。
- **本批有意不落的项**：取材稿中带条号、门槛、期限、比例、倍数与地方口径列举的条目（如出资加速到期的具体条件档、失权宽限期的日数、审计委员会与监事会的表决比例、股东会与董事会的召集通知天数、简易合并的持股比例门槛、股东提案的持股比例与市值与持股期限与提案字数与提前提交天数、要约收购的持股比例与有效期天数与增减幅度、强制收购人存入款项的比例、独立董事连续任职年限与兼任家数上限、高管报酬披露的表格项目数、异议股东评估权的行使期限与利息计算、派生诉讼的持股门槛与和解审批的时限档等），按硬口径不移植；纯境外程序法与外国公司法规则（美国各州公司法、特拉华法典、新泽西商事公司法、威斯康星成文法、证券交易法与威廉法案各条、规则 14a-8 与规则 23.1 等），超出「主张强度」范围，本轮未收，改由「按注册地与交易所现行规则核实」提示承载；纯学理争议条目（公司第一性与法律第二性的先后、法定资本三原则是否形同虚设、合并的法律性质诸说、抽逃出资的规范重心之争、类别股类型法定与类型自由之争、授权资本制与法定资本制的进化趋势等）不属主张强度口径，本轮未收；证券发行与上市公司信息披露、要约收购与投票代理权争夺的监管细节（强制公开的适用范围、形式审查与实质审查、无行动信与证交委职员答复的效力、要约收购的中立立场与目标股东知情权保护、代理权征集费用报销的司法态度等）属合规岗与证券监管口径，超出本岗范围，本轮未收。

### 四、本批自验实测（静态）

判据承接 E18／E23／E24／E25：被测条目去掉末尾出处署名行后的正文，与草稿**两侧同口径**处理（去空白与标点，仅保留汉字、字母与数字），求**连续**相同字符串的最大长度；**达到或超过 10 字判为需要改写**。

```
被测条目：shared/positions/governance-positions.md 全部 50 条（每条＝「应然」行＋「底线」行，出处行不计入）
比对草稿：.work/distill-outbox/ 下 3 份取材稿全文（22-gsf-shang、23-gsf-xia、24-xgsf）
比对池字符数（去标点口径）：22-gsf-shang 21347、23-gsf-xia 29362、24-xgsf 21834
实测结果：最大连续重合 9 字，≥10 字者 0 条
          （并列最大值 9 字见于 5 条，片段如「变更登记即再次转让」「形成的民事法律关系」
            「以不懂业务经验不足」「股东协议与章程同属」「依章程或股东会决议」，
            属各该条主张的核心术语，无法再降）
          （改写过程如实登记：初稿自检最大 19 字、≥10 字者 9 条；首轮逐条改写后降至最大 12 字、≥10 字者 2 条；
            再经一轮改写复跑至最大 9 字、超标 0 条）
条目计数：50 条（限额 ≤50）
三要素：应然／底线／出处齐备 50／50
```

```
对治理岗 SKILL 的去重实测（同一判据，比对池为 SKILL 全文）：
比对池：roles/governance-counsel/SKILL.md（4563 字符）
实测结果：最大连续重合 8 字、≥10 字者 0 条（片段「以股东会决议直接」）
```

```
新增行法条编号与具体数字（立场库正文 100 行〔应然 50 ＋ 底线 50〕）：
条号/款/项命中: 0
阿拉伯数字命中: 0
百分号命中: 0
金额单位命中: 0
门槛/期限/倍数类汉字数字命中: 0
（宽口径扫描另命中「一」「两」「三」共 76 处，逐处核对上下文均为词素而非门槛、期限或倍数，
  如「一成立」「两份」「三样」「一边」「一旦」；正则初筛曾命中「一成」1 处，核对系「一成立就……」
  的「一＋成」跨词素误配，非门槛数值，不计入）
（出处行 50 行为书名署名行，按硬口径豁免，不计入上述统计）
```

```
改动范围与基线：
git diff --name-only →
  docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
  roles/governance-counsel/SKILL.md
git status --porcelain →
   M docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md
   M roles/governance-counsel/SKILL.md
  ?? .workbuddy-ai/            （任务开始前既有，未动）
  ?? shared/positions/governance-positions.md   （本批新设）
git rev-parse --short HEAD → 74f39c5（会话内未提交，基线未变，实测值）
roles/governance-counsel/SKILL.md 的 git diff --numstat → 1  0
Select-String 'E26' docs/review/EXTERNAL-REVIEW-FIX-VERIFICATION.md → 有命中（本附录标题行）
```

### 五、边界

- 本附录记录的是**文本与结构落盘 ＋ 静态自验**，与第 0 节同一口径：不证明宿主在真实会话中会据此产生预期行为（新增条目是否会被真的逐项引用、指针是否会被真的读取，均需宿主实测）。
- **未做**：三本原书全本通读（只读三份合并稿的【标准立场】节）；三稿自述缺口未补——`22-gsf-shang.md` 第 5 行载明由 12 个分片归并、12 片均无来源行；`23-gsf-xia.md` 第 8 行载明 P1、P3、P6、P7、P8、P9、P11、P12 八片未标注所出书目、章次与案例编号；`24-xgsf.md` 第 28–29 行载明 P3、P5、P6、P8 四片未声明所覆盖的法条范围、三个条段无碎片声明覆盖——均照稿登记，未作推断补写；【标准立场】节条目的全量落盘（本批按未覆盖口径精选，三稿【标准立场】合计实测 452 条中收录 50 条）；草稿所涉法条编号、门槛、期限与比例数字的联网核验（按硬口径不移植，故无核验对象）；书中内容在中国法下的实体正确性复核；三稿【检查项】【误判教训】两类的落盘（不在本批取材范围）。
- 三份取材稿与 `.work/` 下历史文件为**只读输入**，本批未修改。
