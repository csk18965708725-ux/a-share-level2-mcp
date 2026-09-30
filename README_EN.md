<p align="center">
  <a href="./README.md">简体中文</a> · <b>English</b>
</p>

<p align="center">
  <img src="./assets/l2-cover.jpg" width="900" alt="A-share Level-2 historical data">
</p>

<h1 align="center">A-Share Level-2 Tick Data · 10-Level Quotes · Tick Orders · Tick Trades</h1>

<p align="center">Shanghai & Shenzhen Level-2 raw market data · daily 7z packages · selective extraction · AI / MCP extension concept</p>

<p align="center">
  <img alt="Level-2" src="https://img.shields.io/badge/Data-Level--2-2563eb">
  <img alt="Coverage" src="https://img.shields.io/badge/A--Shares-2017--Latest-16a34a">
  <img alt="Archive" src="https://img.shields.io/badge/Archive-4.95TB%20compressed-7c3aed">
  <img alt="CSV" src="https://img.shields.io/badge/Format-CSV-0f766e">
  <img alt="MCP" src="https://img.shields.io/badge/AI%20%2F%20MCP-Extensible-f97316">
</p>

## Overview

The product separates three historical data streams instead of flattening everything into bars:

| File | What it contains | Typical use |
|---|---|---|
| 行情.csv | 10 bid levels + 10 ask levels and sizes, trade/turnover statistics and daily price fields | order-book depth, intraday replay, feature engineering |
| 逐笔委托.csv | event time, order identifiers, side code, price and quantity | order-flow and order-event research |
| 逐笔成交.csv | execution time, trade ID, price, quantity and buy/sell related sequence fields | trade-flow and execution analysis |

The repository publishes only documentation and tiny excerpts of real samples. It does **not** publish the full commercial archive or internal production logic.

## Product example: 600519.SH · 2026-06-12

| Table | Measured records | Columns |
|---|---:|---:|
| 10-level quote snapshots | **4,998** | **66** |
| Tick trades | **39,738** | **12** |
| Tick orders | **85,278** | **10** |

Continuous-auction quote snapshots are approximately **3 seconds apart**. Tick orders and tick trades are event streams whose timestamp field is written as `HHMMSSmmm`, with **10 ms actual precision**.

All price columns use integer units of **1/10,000 CNY**. For example, `12790000 = CNY 1279.00`.

See [docs/FIELDS.md](./docs/FIELDS.md), [docs/SAMPLES.md](./docs/SAMPLES.md), and [docs/DATA_NOTES.md](./docs/DATA_NOTES.md).

## Downloadable repository sample

The local source material includes a real sample for **159934.SZ on 2026-07-10**:

| File | Full-day rows | Source file size | Main fields |
|---|---:|---:|---:|
| Market snapshots | 4,835 | ~1.75 MB | 66 |
| Order-by-order entrusts | 53,996 | ~3.27 MB | 10 |
| Trade-by-trade executions | 64,132 | ~4.35 MB | 12 |

See [docs/FIELDS.md](./docs/FIELDS.md) and [samples/159934.SZ](./samples/159934.SZ/).

<p align="center"><img src="./assets/l2-depth-10levels.jpg" width="900" alt="10-level order book snapshot"></p>
<p align="center"><img src="./assets/l2-event-stream.jpg" width="900" alt="order and trade event streams"></p>

## Product scope and measured scale

| Item | Current product scope |
|---|---|
| History | **2017 to present** |
| Market | Shanghai + Shenzhen Level-2 raw market data |
| Measured coverage | **7,744 instruments on 2026-06-12** |
| Core tables | 3: quote snapshots / tick trades / tick orders |
| Packaging | one 7z per trading day; per-symbol folders; 3 CSV files per symbol |
| Raw CSV encoding | **GB18030** |
| 2025 compressed archive | about **869 GB** |
| Full 2017-present archive | about **4.95 TB / 2,361 trading days** |

Shenzhen provides all three tables from 2017. Shanghai tick-order data starts from **2021-07-26**; earlier Shanghai history contains 10-level snapshots and tick trades.

## Important data conventions

| Convention | Meaning |
|---|---|
| Price scale | integer / 10000 = CNY |
| Tick timestamp | `HHMMSSmmm`, actual **10 ms** precision |
| Quote snapshots | about **3 s** apart during continuous auction |
| Auction coverage | starts at **09:15**, includes opening and closing auctions |
| Shanghai cancels | tick-order table, `order_type = D` |
| Shenzhen cancels | tick-trade table, `trade_code = C` |
| CSV encoding | **GB18030**, with one trailing empty column in the header |
| Special fields | IOPV mainly applies to funds; index statistics may be zero for ordinary stocks |

These conventions matter when replaying market microstructure or rebuilding an order book. See [docs/DATA_NOTES.md](./docs/DATA_NOTES.md).

## Selective extraction tool

The bundled netdisk archive extractor (v1.1.0) retrieves only required files from Baidu Netdisk online archives by trading date, security code and target filename. It also supports multiple rules and Excel batch templates.

See [docs/TOOL_GUIDE.md](./docs/TOOL_GUIDE.md).

## AI / MCP extension concept

The existing structured extraction workflow can be **wrapped as an MCP tool** in a future AI workflow. A user could simply ask:

~~~text
Get the 10-level market data for 000001 on 2024-05-20.
~~~

~~~text
Extract the trade-by-trade execution file for 600519 on 2023-12-08.
~~~

An AI agent could parse date, security and data type, call the extractor, return the target CSV and continue with downstream research.

**This repository describes MCP as an extension concept. It does not claim that a production MCP server is already shipped here.**

See [docs/MCP_USE_CASES.md](./docs/MCP_USE_CASES.md).

## Contact

**QQ: 2565473796**  
Please mention your purpose when adding the contact, e.g. **Level-2 data / GitHub / data inquiry**.

**Official account: 代码炒家阿凯**

<table>
  <tr>
    <td align="center" width="50%">
      <img src="./assets/qq-qrcode.png" width="300" alt="QQ QR code"><br>
      <b>QQ: 2565473796</b>
    </td>
    <td align="center" width="50%">
      <img src="./assets/wechat-qrcode.png" width="300" alt="Official account QR code"><br>
      <b>Official account: 代码炒家阿凯</b>
    </td>
  </tr>
</table>

## Disclaimer

This repository is a product showcase and documentation repository. It does not disclose the full archive, internal acquisition/cleaning logic, algorithms, scripts, credentials, activation details or other sensitive implementation information. The data is intended for research, development, learning and historical replay; nothing here is investment advice.
97