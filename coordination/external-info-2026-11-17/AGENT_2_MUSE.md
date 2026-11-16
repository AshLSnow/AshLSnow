# Agent 2：Muse 来源观察、事实版本与采集回执（原任务 A）

先读[共同约定](README.md)。任务 ID：`EXT-20261117-A-MUSE`。

## 目标与范围

保留现有早报与专题阅读方式，补一层机器可读证据。先从已有资料整理 **5–10 条来源观察**，覆盖下列案例。不重建整个平台，不提高采集频率，不为凑条数重复抓同一来源。

输入基线：`ce0a165e7a1ebce9830c93e9d84e3d979463d3eb` 下的 `intel/`。重点：

- `binance-hack-nk-2026-11-01.md`
- `arbitrum-snapshot-correction-2026-11-14.md`
- `huang-testimony-agreed-2026-11-15.md`
- `daily-briefing-2026-11-16.md`

大专题无需全部重读。先按 URL、时间、金额、更正关键词定位，再读上下文。不要把重复轮询当成新增消息。

## 三类记录

优先交 JSONL/JSON；工具不支持时，允许三个固定字段的 MD 表，但在报告说明替代格式，字段不能省略。未知字段保留 null 与原因。

### 来源观察 `source_observations.jsonl`

最小字段：`observation_id, source_url, root_source_id, content_version_id, content_hash, source_published_at, source_updated_at, collector_seen_at, extraction_completed_at, time_metadata, availability_mode, evidence_origin, raw_evidence_ref, evidence_locator, catalogued_at, missing_reason`。time_metadata 按 README 分别描述每个时间的精度、时区及证据。

保留原始时间文字。`root_source_id` 不知道就留空。没有历史正文/响应时，不能虚构历史 content_hash；可以计算现存 MD 的哈希，但另注明它是本次整理对象。

### 事实版本 `claim_versions.jsonl`

最小字段：`event_id, claim_id, version_id, claim_text, units, source_observation_ids, status_asof, changes_kind, previous_version_id, analyst_interpretation`。

同一项事实使用稳定 `claim_id`。金额改错、信息新增、状态正常演变分开。尽量一条记录一个可核断言，避免把事实与“因此主导了行情”放同一条。

### 采集回执 `collection_receipt.json`

最小字段：`run_id, queries, source_scope, language, limits, pagination, errors, last_successful_poll_at, started_at, finished_at, raw_response_available`。

历史 run 与本次整理 run 分开。没有历史工具日志，不得按 MD 推造当年查询、结果数、返回正文或成功时刻。当前做了哪些检查、有没有实际访问来源，可以真实记录。

## 必做的三个例子

### 1. Elliptic 更新时间与迟发现

基线专题约第 5629、5638、5653 行描述：2026-11-16 09:59 UTC 仍见旧版；页面标新更新时间 09:40 UTC；11:59 UTC 观察到新版。

入口：[Elliptic 专题](https://www.elliptic.co/insights/how-north-korea-is-laundering-the-6-4-billion-stolen-from-binance/)。本任务引用的是旧 MD 的记录，页面是否仍可获取须据实际访问报告。

将上述三个时间分别建档。若没有原始响应，两个观测时刻均标 `document_assertion`。来源标注 09:40 不表示采集者 09:40 已知；先前读旧、随后读新也不能直接证明“上一轮之后才发布、并非迟发现”。在新报告中降低这一断言的强度，原始文件保持不动。

本次检索无法确定真正首次公开时刻时，明确留空。可列缓存/索引等备选解释，但不能当作已经查明的原因。

### 2. “2.8 亿美元”改为“28 亿美元”

基线专题约第 5658 行有纠错。建立旧断言→纠正断言的关联；单位、数量级明确写出。若尚未找到源页面原文，标明这是 MD 内部纠错链，不升级成已独立核实的金额。

基线中“2.8亿美元”共 42 次文本出现，含纠错引用，不能计成 42 条独立事实。同一 Elliptic URL 反复出现也不能作为传播广度。

### 3. Arbitrum “未发现”与“尚未发生”

保留原先“未看到发起”的状态，以及 11 月 14 日补查发现更早已启动的纠正链。

- [Arbitrum 官方论坛提案](https://forum.arbitrum.foundation/t/constitutional-aip-approve-release-of-frozen-eth-and-arb/31655)
- [旧文引用的聚合报道](https://cryptoscan24.com/articles/crypto-news-today-12-november-2026)

尝试找直接 Snapshot `proposal_id` 与 URL；主页不是具体提案证据。找不到就交具体检索范围与缺口，不伪造 ID。论坛可支持提案内容，不自动支持投票实时数值；转载聚合文章不等于独立验证。

可加第四例：`huang-testimony-agreed-2026-11-15.md` 中后来“已同意”与先前“截至当时未见回复”。后续状态更新不必然说明旧状态错误，用它检查 `updates` 与 `corrects`。

## 去重与验收

- 相同 URL/内容的重复轮询：保留查询历史或 last_seen，不累加独立报道。
- 不同 URL 的同根转载：保留传播入口，根来源只算一次；未知根源不强行合并。
- 内容改动：新建 content_version；同一个事实变更：关联 previous_version。
- 人物关键词、top-k 检索只代表该采集范围。没有返回消息，不等于全市场无消息。
- JSONL 可解析、观察 ID 唯一、事实引用的观察均存在、版本链无断链/循环。
- 报告展示至少一条完整旧→新链、一条重复轮询去重、一条时间不确定的记录。

## 输出

只写 `deliverables/external-info-2026-11-17/agent2-muse/`：

- `STATUS.md`
- `v1/source_observations.jsonl`
- `v1/claim_versions.jsonl`
- `v1/collection_receipt.json`
- `v1/quality_report.md`：案例覆盖、时间证据等级、去重前后口径、未知项、下一批建议。
- `v1/manifest.json`

不上传 Muse 私有账号、浏览器信息、密钥或私人原始会话。公共资料只留必要短摘录与定位；不复制大段受版权保护全文。本轮验收结构与追溯关系，不要求把每条新闻核验到无任何未知。
