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
