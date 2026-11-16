# Agent 1：CPI 发布范围与版本修复（原任务 B）

先读[共同约定](README.md)。任务 ID：`EXT-20261117-B-CPI`。

## 目标与输入

目标：把“2024 年 1–6 月实际发布的 CPI”做成可追溯事件表，分清当期初值、后来季调修订与同日勘误重发。**不重采已经核对正确的全部字段，不研究交易收益。**

审阅基线：`ce0a165e7a1ebce9830c93e9d84e3d979463d3eb`。输入均在仓库根目录：

- `cpi_2024-01_to_06_events.csv`
- `cpi_2024-01_to_06_events.jsonl`
- `cpi_2024-01_to_06_report.md`

现有六行的 CSV/JSONL 25 个原字段一致，UTC 转换一致，24 个 headline/core 环比及同比数值已抽核。已核结果不是免检承诺：仍运行结构与跨格式测试，但不重复无关采集。

## 施工步骤

### 1. 修复窗口，保留旧六行

原文件按统计月份收集了 2–7 月实际发布。本轮按实际发布日期输出：

| 实际发布日期 | 统计期 | 初始发布 UTC（分钟精度） | 处理 |
|---|---|---|---|
| 2024-01-11 | 2023-12 | 13:30 | 新增；从该日初始发布归档核数字 |
| 2024-02-13 | 2024-01 | 13:30 | 保留既有 ID |
| 2024-03-12 | 2024-02 | 12:30 | 保留既有 ID |
| 2024-04-10 | 2024-03 | 12:30 | 保留既有 ID |
| 2024-05-15 | 2024-04 | 12:30 | 保留既有 ID |
| 2024-06-12 | 2024-05 | 12:30 | 保留既有 ID，处理重发版本 |

7 月 11 日的现有记录保留在扩展批，不删除。合并全集应有七个事件 ID，目标验收视图应有六个。

新 ID 建议：`US-CPI-2023-12-release-2024-01-11`。Agent 3 将引用现有 `US-CPI-2024-01-release-2024-02-13`，不要更改它。

官方入口：[2024-01-11 BLS 归档](https://www.bls.gov/news.release/archives/cpi_01112024.htm)、[2024 官方发布日历](https://www.bls.gov/schedule/2024/home.htm)。核对公告头与表格，并保存证据位置。不要从 2 月或当前数据库抄回 1 月当时值：2023 年 12 月季调环比在 1 月首发与 2 月历史表之间已有修订。

### 2. 分离事件时钟与页面版本时钟

增加字段：`availability_mode`、`initial_release_at`、`time_metadata`、`version_type`、`version_available_at`、`revision_affected_fields`、`version_evidence_url`。同时保留当前回查时间、证据位置和缺失原因。各个时间的精度分别按 README 记录；若保留旧 `time_precision`，明确仅描述初始发布时钟，不能代替版本时间的精度。

- 历史公告默认 `HISTORICALLY_DOCUMENTED`，不标成当年已被本系统采集。
- `initial_release_at` 指公告初始发布时间；`version_available_at` 指当前所用版本的可证公开时间，两者不可互相替代。
- `version_type` 可用 `initial / reissued / later_revision / unknown`，并保存依据。
- 多版本细节另交 `release_versions.jsonl`，以 `event_id` 关联；事件表仍一事件一行。

[6 月 12 日归档](https://www.bls.gov/news.release/archives/cpi_06122024.htm)与[7 月 11 日归档](https://www.bls.gov/news.release/archives/cpi_07112024.htm)都含同日重发说明。请逐项读取：6 月涉及 CPI-W 段落，7 月涉及 hospital services 排除说明。没有找到重发时刻就填 null，不能将整篇更正后的页面当成 08:30 初版。

若能确认某个主要数字未受勘误影响，记录字段级证据。若拿不到初版或更正时刻，明确哪些字段只在重发版得到确认。允许保留这个缺口，但不能给整篇文档盖“首版已验证”标签。

### 3. 修订表述，不编造预期

- 修正旧 JSONL 第 1、3 行关于“CPI-U/CPI-W 发布即最终值”的过宽措辞。季调值可能修订，未季调值的通常规则与例外勘误分开说明。
- 事前市场预期继续留空，除非找到可核实的发布前时间戳与来源。禁止用发布后的新闻倒填。
- 新 `retrieval_time_utc` 是真实本次回查时间。保留旧时间时，注明它属于原采集者自述还是执行日志支持。
- 报告中列出旧值、新值、修复原因与证据。保持原文件不动。

## 交付目录与文件

只写 `deliverables/external-info-2026-11-17/agent1-cpi/`：

- `STATUS.md`
- `v1/cpi_events_all.csv` 与 `v1/cpi_events_all.jsonl`：七个事件；加 `in_target_release_window` 区分目标六个与 7 月扩展。
- `v1/release_versions.jsonl`：版本与字段级证据。
- `v1/quality_report.md`：差异、覆盖、缺失及测试结果。
- `v1/manifest.json`：按共同约定记录输入与输出。
- 若写校验脚本，放 `v1/validate.py`，不用安装新平台或接交易账户。

CSV 布尔值、列表/对象及空值如何编码，写入报告；与 JSONL 做规范化后的逐项比较。不要仅比较行数。

## 验收

1. 全集七个不同事件，目标六次发布齐全；旧六个 ID 不变。
2. UTC/DST 与公告头一致；来源只有分钟时不伪装秒级精度。
3. 四项主要数值、单位、统计期逐项有来源；不混用修订后历史值。
4. CSV/JSONL 一致；版本记录可回连事件；缺失不填 0。
5. 首版、重发、后续修订可区分；未知版本时刻明确保留。
6. 原输入不变；提供文件链接、实际提交 SHA、通过项和剩余限制。

历史缺口不要求“100%补全”；要求的是不能让未知版本伪装成当时已知。达到上述结构与标记要求，即可提交 `READY_FOR_REVIEW`。
