# 触发实验记录：总纲分诊 → 岗位承办 → 复核标注

- 实验日期：2026-09-14
- 实验宿主：ZCode（Windows 10.0.28000，skill 装载目录 `C:\Users\Admin\.zcode\skills\`）
- 被测对象：`legal-dept/SKILL.md`、`roles/{contract-counsel,employment-counsel,dispute-counsel}/SKILL.md`
- 实验目的：验证"唯一入口分诊 → 岗位承办 → 复核标注"链路是否真的走得通（圆桌三席共同认定的 crux：跨平台路由可移植性）
- 结论：**部分走通**。静态链路（文件、指针、词表、结构）全部走通；**宿主自动触发未能实测**，原因见失败点 F1。失败点共 3 项，均为环境/宿主条件限制，非套装设计缺陷。

---

## 一、实验环境实测

### 1.1 宿主 skill 装载机制

命令与输出：

```
$ ls ~/.zcode/skills/
animate/  emil-design-eng/  find-animation-opportunities/  gongzuoliuhen/
improve-animations/  lieflat-charts@  review-animations/  zcode-collab/
```

```
$ for d in .zcode .zcode/skills .claude .claude/skills skills .agents; do
    [ -e "$d" ] && echo "存在 $d" || echo "不存在 $d"; done
不存在 .zcode
不存在 .zcode/skills
不存在 .claude
不存在 .claude/skills
不存在 skills
不存在 .agents
```

```
$ grep -q "Legal Dept" ~/.zcode/cli/config.json && echo 已登记 || echo 未登记
未登记
```

实测结论：本宿主只从**全局目录** `~/.zcode/skills/` 发现 skill；仓库内不存在项目级 skill 目录约定；本仓库未被登记进宿主配置。因此**本次实验期间本套装并未被宿主加载**，自动触发无从发生。

### 1.2 名称冲突实测

```
$ for n in legal-dept contract-counsel employment-counsel dispute-counsel; do
    ls ~/.zcode/skills/ ~/.claude/skills/ | grep -x "$n" || echo "无冲突: $n"; done
无冲突: legal-dept
无冲突: contract-counsel
无冲突: employment-counsel
无冲突: dispute-counsel
```

实测结论：4 个 name 与宿主既有 skill（`brainstorming`、`tabbit`、`animate`、`zcode-collab`、`gongzuoliuhen` 等）**无重名**，`legal-` 前缀 / `-counsel` 后缀策略生效。

邻近功能重叠（非重名，仅记录）：宿主已装 `gongzuoliuhen`，描述为"帮助劳动者把钉钉/企业微信/飞书的工作记录抢收并固化成劳动仲裁可用的证据包"。该 skill 为**劳动者侧**取证，本套装 `employment-counsel` / `dispute-counsel` 为**企业侧**法务，立场相反、不构成冲突；但两者触发词均含"劳动仲裁/证据"，存在同一句话触发两者的可能，需在二期观察。

### 1.3 frontmatter 可解析性实测

用 PyYAML 对 4 个 SKILL.md 做真实解析（非目测）：

```
$ python -c "import yaml, io; ..."   # 解析首尾 --- 之间的 YAML
PASS legal-dept/SKILL.md            | name=legal-dept          | desc_len=428 | 触发词数=83
PASS roles\contract-counsel\SKILL.md   | name=contract-counsel    | desc_len=193 | 触发词数=22
PASS roles\dispute-counsel\SKILL.md    | name=dispute-counsel     | desc_len=187 | 触发词数=30
PASS roles\employment-counsel\SKILL.md | name=employment-counsel  | desc_len=206 | 触发词数=26
```

格式与宿主既有 skill 一致（`name` + `description`，`---` 包围），无 BOM、行尾统一 LF（CR 字节数 = 0）。

---

## 二、链路实测：总纲分诊 → 岗位承办 → 复核标注

### 2.1 触发词覆盖实测（解决"唯一入口"是否真能收到全部请求）

关键风险：若总纲 description 触发词不覆盖岗位触发词，用户说"审合同"时总纲不触发，链路第一跳即断。

实测（修复前）：

```
contract-counsel   触发词=22 总纲未覆盖=20   # 仅覆盖 2/22
employment-counsel 触发词=26 总纲未覆盖=21   # 仅覆盖 5/26
dispute-counsel    触发词=30 总纲未覆盖=24   # 仅覆盖 6/30
```

判定为**真实缺陷**：总纲作为唯一入口却收不到"审合同""起诉""加班费"等最常用的口语触发词，唯一入口会退化成孤立卡片（正是圆桌指出的风险）。

修复：把三岗触发词全部并入总纲 description。修复后复测：

```
总纲触发词数: 83
contract-counsel   岗=22 总纲未覆盖=0 全覆盖
employment-counsel 岗=26 总纲未覆盖=0 全覆盖
dispute-counsel    岗=30 总纲未覆盖=0 全覆盖
```

结论：总纲触发词**全覆盖**三岗触发词，第一跳具备可触发性（静态可验证部分）。

### 2.2 链路逐跳落地实测

以一件模拟事项走完全链：某公司拟解除一名工作 3 年的员工，未提前通知。

```
[跳1] 接待：总纲第1段必问项数 = 5 项
[跳2] 定级：锚点库存在 = YES；本件命中锚点 = H2 涉及劳动关系单方解除
[跳3] 路由：总纲映射到 employment-counsel = 2 处引用；目标文件 = 存在
[跳4] 承办：该岗七段 = 8 段；本岗红线清单 = 存在
[跳5] 复核：H2 -> 高 -> 总监复核；标注词 = 未经复核
[跳6] 归档：台账模板 = 存在
[跳7] 免责：尾部固定项 = 2 处
```

逐跳结论：接待（必问项）→ 定级（锚点 H2 命中）→ 路由（映射表指向真实文件）→ 承办（七段式 + 本岗清单）→ 复核（高 → 总监复核 → 未复核标注词）→ 归档（台账模板）→ 免责，**七跳全部落到真实文件与真实词表**，无断链、无悬空指针。

### 2.3 指针解析实测

```
$ grep -rho "shared/[a-z/-]*\.md" legal-dept roles industries README.md | tr -d '\r' | sort -u |
  while read -r p; do [ -f "$p" ] && echo "OK     $p" || echo "BROKEN $p"; done
OK     shared/checklists/contract-redlines.md
OK     shared/checklists/employment-redlines.md
OK     shared/checklists/evidence-preservation.md
OK     shared/risk-framework.md
OK     shared/templates/intake-log.md
OK     shared/templates/legal-opinion.md
OK     shared/templates/referral-form.md
OK     shared/templates/review-opinion.md
```

反向检查（shared 库是否有文件无人引用）：无输出，即 8 个共享文件全部被引用，无孤儿文件。

注：首轮测试曾报 8 个 BROKEN，经查是**测试方法缺陷**而非套装问题：Git Bash 的 `grep -o` 在管道输出时给每行补了 `\r`，导致 `[ -f "$p" ]` 因路径尾部带 CR 而判假。加 `tr -d '\r'` 后全部 OK。已用 Python 二进制读复核：全部 16 个文件 CR 字节数为 0（纯 LF，无 BOM），CR 并非来自文件。

### 2.4 降级路径实测（总纲不可用时）

```
$ grep -n "未经分诊" roles/*/SKILL.md
roles/contract-counsel/SKILL.md:3    （description 内声明）
roles/contract-counsel/SKILL.md:13   （正文醒目引用块）
...（三岗结构一致）
```

三岗均在 description 与正文第 13 行（标题下方引用块）双处声明"独立调用时自标未经分诊"，满足"显式降级、不得静默"要求。

### 2.5 复核标注词表一致性实测

```
$ grep -ho "已过总监复核\|已过简复核\|未经复核\|本件为低风险，未过总监复核" \
    shared/templates/*.md legal-dept/SKILL.md | sort -u
已过总监复核
已过简复核
未经复核
本件为低风险，未过总监复核
```

实测发现并修复：模板原写"低风险未过复核"，与总纲的"本件为低风险，未过总监复核"不一致，已统一为总纲措辞（复核后 `grep "低风险未过复核"` 残留 = 1，即无命中）。

---

## 三、法规真实性硬步骤实测

### 3.1 联网能力探测

```
$ curl -s -o /dev/null -w "HTTP=%{http_code} time=%{time_total}s" https://flk.npc.gov.cn
HTTP=200 time=1.035162s
```

用户侧核实工作流中列出的官方渠道可达性实测：

```
https://flk.npc.gov.cn        -> HTTP 200
https://www.gov.cn            -> HTTP 200
https://www.mohrss.gov.cn     -> HTTP 200
https://www.gsxt.gov.cn       -> HTTP 521   ← 未能正常响应
https://wenshu.court.gov.cn   -> HTTP 200
https://rmfyalk.court.gov.cn  -> HTTP 200
```

### 3.2 正文抓取能力实测

成功案例（证明 HTML 正文抓取可行）：

```
$ curl -L "https://www.gov.cn/guoqing/2021-10/29/content_5647633.htm" -o _tmp_law.html
HTTP=200 size=73600
$ python -c "去标签后纯文本长度"
纯文本长度: 12933
含「劳动合同法」: False
片段: 中华人民共和国著作权法__中国政府网
```

即：可正常抓取并去标签得到法律全文（本例为《著作权法》全文），抓取链路可用。

### 3.3 定向核验未完成（失败点）

尝试核验《劳动合同法》现行有效版本，各路径结果：

```
# 路径1：flk 检索 API
POST https://flk.npc.gov.cn/api/search   -> HTTP 405 Not Allowed
POST https://flk.npc.gov.cn/api/detail   -> HTTP 405 Not Allowed
GET  https://flk.npc.gov.cn/api/list     -> 返回前端 SPA 页面，非 JSON

# 路径2：gov.cn 政策库检索 API（接口本身可用）
GET https://sousuo.www.gov.cn/search-gov/data?t=zhengcelibrary_gw&q=劳动合同法
-> HTTP 200，totalCount=1097，但 listVO 返回的是
   《即时配送行业高质量发展指导意见》《住房公积金管理条例》等国务院文件，
   非法律全文，且无"现行有效/已废止"效力字段（status=None）

GET ...&t=zhengcelibrary&searchfield=title&q=中华人民共和国劳动合同法
-> HTTP 200，totalCount=0

# 路径3：npc.gov.cn 法律全文页
GET http://www.npc.gov.cn/zgrdw/npc/xinwen/2012-12/28/content_1748082.htm
-> HTTP 200，去标签后 6230 字符，不含「劳动合同法」，未取到目标正文

# 路径4：搜索引擎定位
GET https://www.bing.com/search?q=...site:gov.cn
-> HTTP 200，命中 3 个 gov.cn 链接，但均为工信部/公安部备案查询页，非目标
```

结论：**宿主具备网络能力，但不具备"给定法条名称即返回现行有效全文"的现成能力**。要完成一次合规的法条核验，需要人工在浏览器中检索后提供来源，或由具备 WebSearch/WebFetch 能力的宿主执行。本套装对此的处理方式正确——`legal-dept/SKILL.md` 第 4 段规定无检索能力时产出整体降级并声明"法条未经核验"，本次实验即属该情形，**降级路径被真实触发并记录在案**。

---

## 四、失败点清单

| 编号 | 失败点 | 影响 | 性质 | 处置 |
| --- | --- | --- | --- | --- |
| F1 | 宿主只从全局目录 `~/.zcode/skills/` 装载 skill，本仓库未登记，实验期间套装未被加载，**自动触发行为无法实测** | 无法验证"用户自然说出需求即命中总纲" | 环境限制（需部署到宿主 skill 目录并重启宿主） | 已在 README 第三节写明 4 种挂载方式与显式调用路径；建议部署后由用户侧复测 |
| F2 | 无 WebSearch/WebFetch 工具，仅有 Bash+curl；flk 官方检索接口返回 405，gov.cn 检索接口返回无关结果且无"效力状态"字段 | 无法完成"引用法条前联网核验"的端到端实测 | 能力限制（非套装缺陷） | 已触发并验证降级路径：产出须声明"法条未经核验"；建议二期接入检索能力后复测 |
| F3 | `https://www.gsxt.gov.cn` 返回 HTTP 521 | 用户按核实指引查主体资格时该渠道可能不通 | 第三方站点状态 | 保留该渠道（企业信用公示系统为权威渠道），用户侧如遇不通可改用属地市场监管局官网 |

### 未构成失败但需记录的观察

- 总纲 description 长 428 字符（83 个触发词）。部分宿主对 description 有长度截断或索引上限，若实测发现触发不稳，可按岗拆分触发词或改用显式调用。此为**待观察项**，非失败点。
- `gongzuoliuhen`（劳动者侧劳动仲裁取证）与本套装 `dispute-counsel`（企业侧）触发词存在交集，可能同句双触发。首期不处理，二期观察。

---

## 五、结论

1. **静态链路完全走通**：接待 → 定级 → 路由 → 承办 → 复核 → 归档 → 免责，七跳全部落到真实文件、真实锚点、真实词表；8 个共享文件指针全部可解析且无孤儿；三岗触发词被总纲 100% 覆盖；三岗七段式结构完整（各 8 段：第 0 段硬规则 + 七段骨架）；复核标注词表在总纲与 4 个模板间一致。
2. **动态链路（宿主自动触发）未能实测**，原因 F1 为环境限制。按圆桌决议"总纲写显式调用路径、岗位可独立工作并自标未经分诊"的要求，本套装已提供不依赖自动触发的兜底路径，因此 F1 不构成链路阻断。
3. **法规核验硬步骤**因 F2 在本宿主真实触发降级路径，降级声明机制按设计生效。
4. 实验过程中发现并修复 2 处真实缺陷：总纲触发词覆盖不足（修复前最低仅覆盖 2/22）、复核标注词表与模板不一致。二者均已复测通过。

**下一步建议**：将本套装部署到宿主 skill 目录（或按 README 第三节的挂载方式接入）后重启宿主，由用户用自然语言说出"帮我看看这份合同"等 3 至 5 句典型话术，复测自动触发命中率，补记本文件。

---

## 六、二期复测（2026-09-14 追记）：全局目录装载 + 九 skill 加载验证

- 安装位置：`C:\Users\Admin\.agents\skills\`（ZCode 主力 skill 目录，原 36 个，已备份至 `.work/skills-backup-20260914`）
- 安装方式：`legal-dept/`（总纲 + shared + industries 副本）+ 8 岗平铺为直接子目录（`contract-counsel` 等）——宿主 Skill 工具要求 skill 为直接子目录，`roles/` 嵌套层实测不可加载（`Skill not found: contract-counsel`），已改为平铺。平铺为宿主适配形态，主仓库仍为唯一事实来源。
- 资源基准与同步：岗位 SKILL.md 内 `shared/...` 引用以仓库根为基准，平铺后以各岗目录内副本为基准；已给 8 个岗目录各补 `shared/` + `industries/` 副本（只动安装区，不动仓库），9/9 引用可达已验证。主仓库更新后须重新同步副本；本节“可达”仅指文件存在与路径可解析，不等同于专项内容完整或自动触发有效。

### 6.1 Skill 工具加载实测

| skill | 加载 | 说明 |
|---|---|---|
| `contract-counsel` | 通过 | 全文加载，第 0–7 段完整 |
| `legal-dept` | 通过 | 全文加载，路由表已含 6 业务岗 |
| `employment-counsel` | 通过 | 全文加载 |
| `ip-counsel` | 通过 | 全文加载 |

结论：**宿主 Skill 工具可加载本套装 skill**（F1 部分消除：加载机制走通；自动触发仍待用户自然话术复测）。

### 6.2 关键机制程序化核验（四文件）

| 文件 | 待核实 | 未经分诊 | 未经复核 | 法条未经核验 | 免责 |
|---|---|---|---|---|---|
| contract-counsel | 4 | 2 | —（岗位级，复核门在总纲/总监岗） | 1 | 2 |
| employment-counsel | 4 | 2 | — | 1 | 2 |
| ip-counsel | 4 | 2 | — | 1 | 2 |
| legal-dept | 4 | 1 | 4 | 1 | 2 |

### 6.3 安装区引用可达性（四 skill × 五文件）

`contract-counsel` / `employment-counsel` / `ip-counsel` / `legal-dept` 各自目录下 `shared/risk-framework.md`、`shared/templates/review-opinion.md`、`shared/templates/referral-form.md`、`shared/checklists/evidence-preservation.md`、`industries/README.md` 20/20 全部可达。

### 6.4 遗留事项

- 精确词表覆盖属于静态检查，不作为自动触发命中率的结论；自动触发仍需真实会话复测。
- 自动触发（用户自然话术命中率）仍未实测：Skill 工具为显式调用，本次验证的是"可加载 + 内容完整"，自然语言自动路由需在宿主真实会话中由用户复测
- F2（法条联网核验）、F3（gsxt 521）维持原结论不变
- 待观察项更新：总纲 description 已从 428 字符扩至约 180 触发词，截断风险上升；若触发不稳，优先改用显式调用
