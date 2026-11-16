# 美国 CPI 发布事件清单：首批字段验收（统计月份 2024-01 至 2024-06）

本批次按**统计月份**定义范围，覆盖2024年1月至6月六个连续CPI统计期；每行的**实际公告日期**是次月BLS公告日，不是统计月份本身。此次只验收外部公告时钟与原始公告字段，不查看或解释公告后的BTC/ETH及其他市场行情，也不据行情筛选样本。

## 结论与范围

六个统计期均找到BLS归档原文和BLS 2024年官方预定日历，未发现实际公告日晚于预定日的延期。六次原文均给出“8:30 a.m. (ET)”禁运/发布时间证据，但没有秒，因此UTC只精确到分钟：2024年2月13日为EST（UTC−05:00），其余五次为America/New_York夏令时EDT（UTC−04:00）。原文的“embargoed until”是本数据集采用的公告时钟证据；它不被扩写为没有证据支持的秒级“published at”。

| 统计期 | 实际公告日 | 原文时刻与UTC | headline（原始公告） | core：all items less food and energy | 版本/变更记录 |
|---|---|---|---|---|---|
| [2024-01](https://www.bls.gov/news.release/archives/cpi_02132024.htm) | 2024-02-13 | 8:30 a.m. ET；[13:30 UTC](https://www.bls.gov/news.release/archives/cpi_02132024.htm) | 季调环比 +0.3%；未季调12个月 +3.1%；指数 308.417 | 季调环比 +0.4%；未季调12个月 +3.9%；指数 313.623 | 未发现延期 |
| [2024-02](https://www.bls.gov/news.release/archives/cpi_03122024.htm) | 2024-03-12 | 8:30 a.m. ET；[12:30 UTC](https://www.bls.gov/news.release/archives/cpi_03122024.htm) | 季调环比 +0.4%；未季调12个月 +3.2% | 季调环比 +0.4%；未季调12个月 +3.8% | 未发现延期 |
| [2024-03](https://www.bls.gov/news.release/archives/cpi_04102024.htm) | 2024-04-10 | 8:30 a.m. ET；[12:30 UTC](https://www.bls.gov/news.release/archives/cpi_04102024.htm) | 季调环比 +0.4%；未季调12个月 +3.5%；指数 312.332 | 季调环比 +0.4%；未季调12个月 +3.8% | 未发现延期 |
| [2024-04](https://www.bls.gov/news.release/archives/cpi_05152024.htm) | 2024-05-15 | 8:30 a.m. ET；[12:30 UTC](https://www.bls.gov/news.release/archives/cpi_05152024.htm) | 季调环比 +0.3%；未季调12个月 +3.4%；指数 313.548 | 季调环比 +0.3%；未季调12个月 +3.6%；指数 317.978 | 未发现延期；本批次直接使用HTML原文，未把PDF作为独立证据 |
| [2024-05](https://www.bls.gov/news.release/archives/cpi_06122024.htm) | 2024-06-12 | 8:30 a.m. ET；[12:30 UTC](https://www.bls.gov/news.release/archives/cpi_06122024.htm) | 季调环比 0.0%；未季调12个月 +3.3%；指数 314.069 | 季调环比 +0.2%；未季调12个月 +3.4%；指数 318.629 | 未发现延期；公告同日重发以更正CPI-W段落错误，保留重发版本记录 |
| [2024-06](https://www.bls.gov/news.release/archives/cpi_07112024.htm) | 2024-07-11 | 8:30 a.m. ET；[12:30 UTC](https://www.bls.gov/news.release/archives/cpi_07112024.htm) | 季调环比 −0.1%；未季调12个月 +3.0% | 季调环比 +0.1%；未季调12个月 +3.3%；指数 319.003 | 未发现延期；公告同日重发并移除不符合发布标准的住院医院服务数据 |

表内月度变动均明确保留BLS原文的季节调整口径；12个月变动均保留未季调口径。所谓“core”仅作为研究字段标签，原文名称是 **all items less food and energy**，不是另造一个BLS独立指数。指数基期和表格单位见各自原文及PDF（可访问时已列入数据文件）。

## 字段与证据规则

每个事件均有唯一 `event_id`，并分开存储 `statistics_month`、`actual_release_date`、原始文字时刻、America/New_York时区状态、UTC时刻、时刻证据位置、预定日期、延期记录、headline/core数字与单位、版本关系、抓取时间和访问范围。CSV把数组字段用 ` | ` 连接；JSONL保留数组结构。

原始公告入口使用BLS归档HTML，能访问的事件同时保留官方PDF链接。BLS归档总页明确警示历史新闻稿数据可能在后续发布中修订；因此本批数据只抄录各次归档公告文字，没有用今天数据库值替换。2024年5月统计期对应的2024-06-12公告和2024年6月统计期对应的2024-07-11公告都保留了BLS同日重发说明，而没有将其隐藏在普通“修订”字段中。

共识预期字段全部留空。原因不是把预期视为不存在，而是本批次没有纳入同时满足“发布前可用、带明确时间/日期、单位清楚、可回溯原始来源”的来源；没有用发布后的报道补填。

## 遗漏、访问失败与边界

在本批次定义的六个统计月份中，没有发现缺少BLS原文或官方预定日历的事件，因而没有事件级访问失败。存在两项证据范围差异：2024年4月统计期本次直接读取HTML原文，未把PDF作为独立证据；2024年6月统计期也以可访问的HTML重发版本为主要证据。它们已分别写入 `failures_or_unknowns`，不伪称已完成同等PDF核验。

本报告没有扩展2022-04-01至2026-09-30全量事件，也没有加入FOMC。若后续扩展，仍应按同一规则先区分统计月份、预定日期和实际公告时钟，并把FOMC声明、记者会、纪要作为独立事件家族；本批次不将它们混入CPI样本。

## 文件

- 机器可读JSONL：`cpi_2024-01_to_06_events.jsonl`
- 机器可读CSV：`cpi_2024-01_to_06_events.csv`

## References

[1]: https://www.bls.gov/bls/news-release/cpi.htm "BLS Consumer Price Index Archived News Releases"
[2]: https://www.bls.gov/schedule/2024/home.htm "BLS Schedule of Selected Releases for 2024"
[3]: https://www.bls.gov/news.release/archives/cpi_02132024.htm "CPI January 2024 archived release"
[4]: https://www.bls.gov/news.release/archives/cpi_03122024.htm "CPI February 2024 archived release"
[5]: https://www.bls.gov/news.release/archives/cpi_04102024.htm "CPI March 2024 archived release"
[6]: https://www.bls.gov/news.release/archives/cpi_05152024.htm "CPI April 2024 archived release"
[7]: https://www.bls.gov/news.release/archives/cpi_06122024.htm "CPI May 2024 archived release"
[8]: https://www.bls.gov/news.release/archives/cpi_07112024.htm "CPI June 2024 archived release"
