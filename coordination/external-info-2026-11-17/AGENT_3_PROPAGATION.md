# Agent 3：两个固定事件的传播与更正案例（原任务 C）

先读[共同约定](README.md)。任务 ID：`EXT-20261117-C-PROPAGATION`。

## 目标

用两个历史事件验证：能否把原始公告、转载、独立确认、新增事实、否认和更正分开。**不根据事后行情选案例，不计算胜率，不把案例当作统计结论。**

基线提交 `ce0a165e7a1ebce9830c93e9d84e3d979463d3eb` 未找到这两份成果。开工先检查 `main`、`muse-intel` 与已知交付路径；已有成果就复用、修补，注明准确分支和提交，不重复生成互相矛盾的版本。

## 固定案例 1：2024-02-13 美国 CPI

共享事件 ID：`US-CPI-2024-01-release-2024-02-13`。

官方入口：[BLS 2024-02-13 CPI 归档](https://www.bls.gov/news.release/archives/cpi_02132024.htm)。统计期为 2024 年 1 月，公告时钟为 08:30 ET / 13:30 UTC，分钟精度。

这条记录已经在仓库根 `cpi_2024-01_to_06_events.jsonl`，因此不用等待 Agent 1 新增 1 月发布。数字沿用可核的原公告与共享事件 ID，版本问题单独注明，不私自重建 B 的事件表。

按时间线寻找并记录：

- 原公告及可证公开时间。
- 本次检索找到的早期报道、转载与来源根关系。
- 哪些报道只是复述数字，哪些增加新事实，哪些只是观点。
- 是否有更正；未找到时写“在所列检索范围内未找到”，不得捏造更正。

不需要为达到文章数去收大量同根转载。如果证据不足以比较传播先后，交可证部分和缺口。网页当前更新时间不能冒充初次发稿时间。

## 固定案例 2：2024-01-09 SEC 账号虚假 ETF 消息

事件 ID：`SEC-X-FALSE-ETF-2024-01-09`。

官方事后入口：[SEC 2024-01-12 说明](https://www.sec.gov/newsroom/speeches-statements/gensler-x-account)。该说明回顾 1 月 9 日未经授权帖文、否认与 SEC 后续声明。

核验说明中的约 16:11、16:26、16:42 Eastern 时间节点（对应 21:11、21:26、21:42 UTC；保留原文的约数含义）。这些只是查证入口，执行者须记录实际找到的原文与精度，不能因为任务书列了时间便视为独立证据。

分别建档：

1. 未经授权账号发布的虚假批准消息：记录当时声称什么，不在最初节点附上后来调查得到的全部真相。
2. 否认/账号遭入侵说明：单列可证发布时间和来源。
3. SEC 后续更正与后续调查：形成新版本或新事实，不倒填到虚假消息的最初时刻。

优先找当时帖子、同期官方说明或可核的历史捕获。原帖不可得时，可用官方事后说明建立 `HISTORICALLY_DOCUMENTED` 时间线，并标其本身是 1 月 12 日材料。不能把事后回顾当成 1 月 9 日系统已收到的信息。

转载中“引用 SEC”不等于它确实保存了原帖；截图、网页存档、文章转述分别标证据类型。没有独立验证的精确秒数不写。

## 每条记录保留什么

`case_observations.jsonl` 至少包含：`case_id, event_id, observation_id, source_url, source_name, root_source_id, relation_to_root, source_published_at, source_updated_at, collector_seen_at, extraction_completed_at, time_metadata, original_time_text, evidence_locator, availability_mode, evidence_origin, retrieved_at, raw_evidence_ref, missing_reason`。time_metadata 按 README 分别描述每个时间的精度、时区及证据。

`relation_to_root` 可用 `original / syndication / independent_confirmation / commentary / correction / unknown`。内容与时间证据不足时，用 unknown；不要根据域名数量机械认定独立传播。

`claim_versions.jsonl` 使用共用字段：`event_id, claim_id, version_id, claim_text, source_observation_ids, status_asof, changes_kind, previous_version_id, analyst_interpretation`。声明“被批准”与后续“该帖未经授权”分开版本，保留谁在何时说了什么。

`collection_receipt.json` 记本次查询、语言、时间范围、来源范围、结果限制/分页、失败、实际执行起止时间。若没有历史采集日志，就不补历史 run。

时间线报告必须区分：事件发生时刻、资料自身发表时刻、本次回查时刻。找到最早一条不是证明全网首发；“没有找到”也不是证明从未传播。

## 交付与验收

只写 `deliverables/external-info-2026-11-17/agent3-propagation/`：

- `STATUS.md`
- `v1/case_observations.jsonl`
- `v1/claim_versions.jsonl`
- `v1/collection_receipt.json`
- `v1/cases_report.md`：每案例一条可读时间线、事实新增/复述/更正对照、覆盖限制。
- `v1/manifest.json`

如果不能输出 JSON，允许同字段 MD 固定表，并说明替代格式。不是为了凑行数而制造观察。

验收要求：两个指定案例都覆盖；CPI event_id 与 B 一致；每项时间与事实能回到证据位置；UTC、时区与精度明确；观察 ID 唯一且事实关联有效；后见真相不倒填；原始文件不改动。

如原帖已删或网页不可访问，保留具体失败与可完成部分，仍可提交结构验收。完整传播机制的后续研究能否开始，由覆盖范围决定。本轮不要求先证明传播越广就更盈利，也不自动开启更大采集任务。
