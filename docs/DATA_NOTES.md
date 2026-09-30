# 数据口径与常见问题 / Data Notes & FAQ

## 要准备多大硬盘？

按当前产品实测口径：

| 范围 | 压缩态体量 |
|---|---:|
| 2025 全年 | 约 **869 GB** |
| 2026 年至当前统计时点 | 约 **903 GB** |
| 2017 至今全历史 | 约 **4.95 TB** |
| 全历史交易日 | **2,361** 个 |

数据量随市场扩容明显增长：2017 年单日约 0.69 GB，2026 年单日约 5.19 GB，约相差 7 倍。

> 这些数字都是**压缩态**。单日全部解压成 CSV 后体量约为压缩包的 **8.6 倍**。更合理的使用方式是“用哪天下哪天、算完即删”；如果长期留存，可考虑转成 parquet，产品实测单日约 12 GB。

## 覆盖多少标的？

沪深全市场。产品实测 `2026-06-12` 当日共 **7,744 只**标的。

## 十档快照多久一笔？

连续竞价阶段约 **3 秒一笔**。  
`600519.SH / 2026-06-12` 实测 4,998 笔快照，其中相邻时间差 4,985 次为 3.0 秒。

逐笔成交和逐笔委托才是一笔一条的事件流，时间戳实际精度为 **10 毫秒**。

## 包含集合竞价吗？

包含。

- 09:15 开始记录开盘集合竞价；
- 09:25 撮合成交在逐笔成交表中；
- 09:30 进入连续竞价；
- 14:57–15:00 收盘集合竞价也在；
- 竞价阶段快照实测约 9 秒一笔。

如果只需要每日竞价汇总字段，则属于另一套单独的“集合竞价”数据产品。

## 沪市逐笔委托从哪年开始？

- 深市：2017 年起三张表均有；
- 沪市：逐笔委托从 **2021-07-26** 起；此前沪市只有十档快照与逐笔成交。

## 价格为什么看起来大 10000 倍？

三张表所有价格列都是整数，单位为 **万分之一元**。

~~~text
12790000 ÷ 10000 = 1279.00 元
~~~

不做换算通常不会报错，但会让价格/金额尺度错误。

## 为什么重建委托簿要区分沪深撤单？

因为撤单记录位置不同：

~~~text
沪市：逐笔委托表 → 委托类型 = D
深市：逐笔成交表 → 成交代码 = C
~~~

只处理一种口径会造成另一市场的重建委托簿出现明显偏差。

## 为什么有些列一直是 0？

字段并非都适用于普通股票：

- IOPV：主要用于 ETF 等基金品种；
- 不加权指数、品种总数、上涨/下跌/持平品种数：主要用于指数品种；
- 普通股票这些字段为 0，不代表文件损坏或数据缺失。

## CSV 为什么默认 UTF-8 读不开？

原始 CSV 编码为 **GB18030**。另外三个 CSV 的表头行末尾都带一个空字段名，pandas 读取时可能出现 `Unnamed` 列，按需删除即可。

Python 示例：

~~~python
import pandas as pd

df = pd.read_csv("行情.csv", encoding="gb18030")
df = df.loc[:, ~df.columns.str.startswith("Unnamed")]
~~~

## 新交易日需要重新要分享链接吗？

不需要。新增交易日会继续出现在原分享目录中，使用原链接即可获取后续更新。

---

## English summary

Key operational notes:

- Full compressed archive: about **4.95 TB** across 2,361 trading days in the current measured scope.
- 2025 alone: about **869 GB** compressed.
- Quote snapshots: about **3 seconds** apart during continuous auction; tick events have **10 ms** timestamp precision.
- Raw CSV encoding: **GB18030**.
- All price columns use integer units of **1/10,000 CNY**.
- Shanghai and Shenzhen use different cancellation encodings/locations.
