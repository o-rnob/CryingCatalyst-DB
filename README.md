![CryingCatalyst logo — night-moon owl artwork](https://github.com/o-rnob/CryingCatalyst-DB/raw/main/logo.png)

# CryingCatalyst — Bangladesh's Open-Source Macroeconomic Indicator Database

[#cryingcatalyst](#cryingcatalyst)

**A Consolidated Macroeconomic Indicator Database for Bangladesh — by Ow1nomics**

![License: MIT](https://img.shields.io/badge/license-MIT-green)
![SQLite](https://img.shields.io/badge/database-SQLite3-003B57)
![Bangladesh Macroeconomics](https://img.shields.io/badge/coverage-Bangladesh%20Macro%20Data-orange)
![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20macOS%20%7C%20Windows%20%7C%20Android-informational)

CryingCatalyst is a consolidated, queryable SQLite database of **Bangladesh macroeconomic indicators** — GDP growth, inflation, foreign exchange reserves, the exchange rate, remittances, the policy rate, population, and unemployment — pulled together from **Bangladesh Bank, the Bangladesh Bureau of Statistics (BBS), the Ministry of Finance, NBR, the World Bank, the IMF, the ADB, EPB, and BIDA** into one queryable schema. It is the macroeconomic companion to [KnightBase](https://github.com/o-rnob/Knightbase-DB), which covers Dhaka Stock Exchange price history.

If you are searching for **Bangladesh GDP growth data**, a **Bangladesh inflation and CPI database**, **BDT/USD exchange rate history**, **Bangladesh foreign exchange reserves data**, or an **open-source Bangladesh macroeconomic dataset**, this repository is built for exactly that use case.

---

## Table of contents

[#table-of-contents](#table-of-contents)

- [What this is](#what-this-is)
- [Quick stats](#quick-stats)
- [Getting the database](#getting-the-database)
- [How to use CryingCatalyst](#how-to-use-cryingcatalyst)
  - [Linux](#linux)
  - [macOS](#macos)
  - [Windows](#windows)
  - [Android](#android)
  - [Using CryingCatalyst with Python / pandas](#using-cryingcatalyst-with-python--pandas)
  - [Using CryingCatalyst with an AI assistant](#using-cryingcatalyst-with-an-ai-assistant)
- [Schema](#schema)
- [Notes on data quality and interpretation](#notes-on-data-quality-and-interpretation)
- [Sources & attribution](#sources--attribution)
- [Citation](#citation)
- [A note on the name](#a-note-on-the-name)

---

## What this is

[#what-this-is](#what-this-is)

`crying_catalyst.db` is a single SQLite file organizing Bangladesh's public macroeconomic data into a normalized, time-series-friendly schema: **categories** (a two-level taxonomy, e.g. "External Sector" → "Foreign Exchange Reserves"), **series** (individual named indicators with unit, frequency, and definition), **observations** (the actual time-series values), and **sources** (where each series originates, with URL and access method). Every observation carries a `value_type` — `actual`, `provisional`, `estimate`, or `forecast` — so you always know how much to trust a given number, and a free-text `notes` field capturing the exact provenance of that data point.

## Quick stats

[#quick-stats](#quick-stats)

|                          |                                                                                          |
| ------------------------ | ------------------------------------------------------------------------------------------ |
| Categories               | 22 (9 top-level, 13 sub-categories)                                                       |
| Series                   | 12 named macroeconomic indicators                                                          |
| Observations             | 581 time-series data points                                                                |
| Sources                  | 10 (Bangladesh Bank, BBS, MoF, NBR, World Bank, IMF, ADB, EPB, BIDA, CEIC/Trading Economics) |
| Coverage span            | 1960 → 2027 (varies by series — see [Schema](#schema) for per-series ranges)               |
| Value types tracked      | `actual`, `provisional`, `estimate`, `forecast`                                            |

---

## Getting the database

[#getting-the-database](#getting-the-database)

Clone the repository, or download `crying_catalyst.db` directly from the repo's file listing:

```bash
git clone https://github.com/o-rnob/CryingCatalyst-DB.git
cd CryingCatalyst-DB
```

The database ships as a single portable `.db` file — no server, no external dependencies, and no installation of a database engine beyond the standard SQLite3 library, which most operating systems already include.

---

## How to use CryingCatalyst

[#how-to-use-cryingcatalyst](#how-to-use-cryingcatalyst)

CryingCatalyst is a **standard SQLite3 file**, so it works anywhere SQLite runs — desktop, laptop, server, or mobile. Pick your platform below.

### Linux

[#linux](#linux)

Most distributions ship with SQLite3 pre-installed. If it isn't available, install it with your package manager:

```bash
# Debian / Ubuntu
sudo apt update && sudo apt install sqlite3

# Fedora
sudo dnf install sqlite

# Arch
sudo pacman -S sqlite
```

Open the database and run a query:

```bash
sqlite3 crying_catalyst.db
sqlite> SELECT period, value, value_type FROM observations
   ...> JOIN series USING(series_id)
   ...> WHERE code = 'BD_CPI_INFLATION' ORDER BY period DESC LIMIT 10;
sqlite> .quit
```

For a graphical interface, install **DB Browser for SQLite**:

```bash
sudo apt install sqlitebrowser   # Debian/Ubuntu
sudo dnf install sqlitebrowser   # Fedora
```

### macOS

[#macos](#macos)

macOS includes SQLite3 by default. Open a terminal and run:

```bash
sqlite3 crying_catalyst.db
sqlite> SELECT code, name, unit, frequency FROM series;
sqlite> .quit
```

If you prefer a newer version or a GUI, install via Homebrew:

```bash
brew install sqlite3
brew install --cask db-browser-for-sqlite
```

### Windows

[#windows](#windows)

**Option 1 — SQLite command-line tools:**

1. Download the `sqlite-tools` ZIP for Windows from [sqlite.org/download.html](https://sqlite.org/download.html).
2. Extract it and add the folder to your `PATH`, or run `sqlite3.exe` directly from the extracted folder.
3. Open the database:

```powershell
sqlite3.exe crying_catalyst.db
sqlite> SELECT period, value FROM observations
   ...> JOIN series USING(series_id)
   ...> WHERE code = 'BD_GDP_GROWTH_REAL' ORDER BY period;
sqlite> .quit
```

**Option 2 — DB Browser for SQLite (recommended for beginners):**
Download the installer from [sqlitebrowser.org](https://sqlitebrowser.org), install it, then open `crying_catalyst.db` via **File → Open Database**. This gives you a spreadsheet-style view of every table plus a built-in query editor — no command line required.

**Option 3 — Windows Subsystem for Linux (WSL):**
If you have WSL installed, follow the [Linux instructions](#linux) inside your WSL distribution for the full command-line experience.

### Android

[#android](#android)

CryingCatalyst can be queried directly on an Android device using **Termux**, a free terminal emulator available on F-Droid and the Play Store:

1. Install [Termux](https://f-droid.org/en/packages/com.termux/).
2. Install SQLite and Git inside Termux:

```bash
pkg update && pkg install sqlite git
```

3. Clone the repository and query the database:

```bash
git clone https://github.com/o-rnob/CryingCatalyst-DB.git
cd CryingCatalyst-DB
sqlite3 crying_catalyst.db
sqlite> SELECT COUNT(*) FROM observations;
sqlite> .quit
```

For a no-code option, apps like **DB Browser** or generic **SQLite Viewer** apps on the Play Store can open `crying_catalyst.db` for browsing and simple queries without a terminal.

### Using CryingCatalyst with Python / pandas

[#using-cryingcatalyst-with-python--pandas](#using-cryingcatalyst-with-python--pandas)

Python's standard library includes SQLite support, making CryingCatalyst ideal for time-series analysis and forecasting workflows:

```python
import sqlite3
import pandas as pd

conn = sqlite3.connect("crying_catalyst.db")

df = pd.read_sql_query(
    """
    SELECT o.period, o.period_date, o.value, o.value_type
    FROM observations o
    JOIN series s ON s.series_id = o.series_id
    WHERE s.code = 'BD_FX_RESERVES'
    ORDER BY o.period_date
    """,
    conn
)

print(df.tail())
conn.close()
```

This works identically on Linux, macOS, and Windows, and on Android via Termux with `pip install pandas`.

### Using CryingCatalyst with an AI assistant

[#using-cryingcatalyst-with-an-ai-assistant](#using-cryingcatalyst-with-an-ai-assistant)

Because CryingCatalyst is a plain, self-contained SQLite file with a fully normalized schema and explicit `definition` text on every series, it is well suited to being queried by AI coding assistants and LLM agents rather than only by hand-written SQL:

**Claude Code / Cursor / other coding agents with file access:**
Point the agent at the cloned repository and ask it directly, e.g. *"Open crying_catalyst.db and plot Bangladesh's CPI inflation since 2015."* Coding agents with a sandboxed shell can run `sqlite3` or Python against the file the same way a human would, following the platform instructions above.

**Claude, ChatGPT, or other chat assistants with file upload / code execution:**
Upload `crying_catalyst.db` directly into a conversation that supports file analysis or code execution, then ask natural-language questions such as *"What's Bangladesh's most recent forecasted GDP growth rate, and who forecasted it?"* or *"Compare BBS fiscal-year GDP growth to the World Bank calendar-year series."* The assistant can write and run the underlying SQL for you.

**Model Context Protocol (MCP):**
CryingCatalyst works out of the box with any MCP-compatible **SQLite server** (for example, the official `mcp-server-sqlite` reference server). Point the server at the local `crying_catalyst.db` path, connect it to an MCP-aware client, and query the database using natural language while retaining full auditability of the underlying SQL.

**Retrieval-augmented generation (RAG) and analytics pipelines:**
Every series carries an explicit `definition`, `unit`, `frequency`, and `fiscal_year_basis` flag, and every observation carries a `value_type` and provenance `notes` — this makes CryingCatalyst straightforward to wire into LangChain's `SQLDatabase` toolkit, LlamaIndex's SQL query engine, or any custom text-to-SQL pipeline, since an LLM can reason about data reliability directly from the schema rather than guessing.

> **Tip for AI-assisted queries:** always ask the assistant to check a series' `fiscal_year_basis` flag and `value_type` before comparing it to another series — this database deliberately keeps fiscal-year and calendar-year versions of the same underlying concept (e.g. GDP growth) as separate series rather than silently reconciling them. See [Notes on data quality and interpretation](#notes-on-data-quality-and-interpretation) below.

---

## Schema

[#schema](#schema)

### `categories`

[#categories](#categories)

A two-level taxonomy for organizing series. Top-level categories (`parent_id` is `NULL`) group related sub-categories.

| column       | type    | notes                                          |
| ------------ | ------- | ----------------------------------------------- |
| category_id  | INTEGER | primary key                                     |
| parent_id    | INTEGER | NULL for top-level categories                   |
| name         | TEXT    | category name                                   |
| description  | TEXT    | what the category covers                        |

**Top-level categories:** National Accounts · Prices & Inflation · External Sector · Monetary & Financial · Fiscal Sector · Labour & Demographics · Sectoral Real Economy · Social & Development · Econometric / Derived Series

**Currently populated sub-categories** (12 series live under these today; the remaining top-level categories and sub-categories are scaffolded in the schema for future series):

Real GDP Growth · Nominal GDP · Foreign Exchange Reserves · Exchange Rate · Remittances · Policy Rate · Population · Unemployment Rate

### `series`

[#series](#series)

12 named macroeconomic indicators, each with a stable `code`, unit, frequency, and an explicit `definition` describing exactly what is measured and on what basis.

| code                       | name                                                        | unit    | frequency    | fiscal-year basis | observations | period range              |
| --------------------------- | ------------------------------------------------------------ | ------- | ------------ | :----------------: | :-----------: | ---------------------------- |
| `BD_GDP_GROWTH_REAL`         | Real GDP Growth Rate (BBS)                                    | %       | fiscal_year  | ✔                   | 21            | FY2003-04 → FY2027           |
| `BD_GDP_GROWTH_REAL_WB`      | Real GDP Growth Rate (World Bank, calendar year)               | %       | annual       | —                   | 65            | 1961 → 2025                  |
| `BD_GDP_NOMINAL_USD`         | Nominal GDP (current US$)                                      | USD bn  | annual       | —                   | 65            | 1960 → 2026                  |
| `BD_CPI_INFLATION`           | CPI Inflation (avg, YoY)                                       | %       | monthly      | —                   | 41            | 1987 → 2026-05               |
| `BD_FX_RESERVES`             | Gross Foreign Exchange Reserves                                | USD bn  | monthly      | —                   | 33            | 2021-12 → FY2023-24P         |
| `BD_EXCHANGE_RATE_USD`       | Exchange Rate (BDT per USD)                                    | BDT/USD | monthly      | —                   | 51            | FY1973-74 → FY2023-24P       |
| `BD_REMITTANCES`             | Inward Remittances                                             | USD bn  | monthly      | —                   | 50            | 2026-03 → FY2023-24          |
| `BD_POLICY_REPO_RATE`        | Policy Repo Rate                                               | %       | monthly      | —                   | 15            | 2020-04-01 → 2026-07-01      |
| `BD_BANK_RATE`               | Bank Rate (classical, BB)                                      | %       | fiscal_year  | ✔                   | 73            | 2024-01 → FY2023-24P         |
| `BD_CALL_MONEY_RATE`         | Interbank Call Money Market Rate (weighted avg borrowing rate) | %       | monthly      | —                   | 66            | 2009 → 2026-07               |
| `BD_POPULATION_TOTAL`        | Total Population                                               | persons | annual       | —                   | 66            | 1960 → 2026                  |
| `BD_UNEMPLOYMENT_RATE`       | Unemployment Rate (modeled ILO estimate)                       | %       | annual       | —                   | 35            | 1991 → 2025                  |

Every `series` row also carries a `source_id` (see `sources` below) and a `last_updated` timestamp.

### `observations`

[#observations](#observations)

581 time-series data points, one row per `(series_id, period)`.

| column       | type    | notes                                                                 |
| ------------ | ------- | ----------------------------------------------------------------------- |
| obs_id       | INTEGER | primary key                                                             |
| series_id    | INTEGER | foreign key → `series`                                                  |
| period       | TEXT    | the native period label as published (e.g. `FY2025`, `2026-05`, `2026`)|
| period_date  | TEXT    | ISO date, generally the period's end date, for chronological sorting   |
| value        | REAL    | the observed value, in the series' stated unit                         |
| value_type   | TEXT    | one of `actual`, `provisional`, `estimate`, `forecast`                 |
| notes        | TEXT    | free-text provenance — which release, which report, any caveats        |

**Always check `value_type` before treating a number as final** — `forecast` and `estimate` rows are included deliberately (e.g. ADB Asian Development Outlook forecasts, IMF estimates) because they're useful for forward-looking analysis, but they are not the same as a confirmed `actual` outturn.

### `sources`

[#sources](#sources)

10 institutional sources, each with a name, abbreviation, URL, and access method.

| abbreviation | name                                          | access method                              |
| ------------- | ----------------------------------------------- | --------------------------------------------- |
| BB            | Bangladesh Bank                                 | bulk download / Excel / open data portal      |
| BBS           | Bangladesh Bureau of Statistics                 | manual/report + bulk download                 |
| MoF           | Ministry of Finance, Bangladesh                 | manual/report                                 |
| NBR           | National Board of Revenue                       | manual/report                                 |
| WB            | World Bank                                      | API (no key required)                         |
| IMF           | International Monetary Fund                     | API / bulk download                           |
| ADB           | Asian Development Bank                          | manual/report + Key Indicators database        |
| EPB           | Export Promotion Bureau                         | manual/report                                 |
| BIDA          | Bangladesh Investment Development Authority     | manual/report                                 |
| CEIC/TE       | CEIC / Trading Economics (aggregators)          | API (paid) / scrape                           |

---

## Notes on data quality and interpretation

[#notes-on-data-quality-and-interpretation](#notes-on-data-quality-and-interpretation)

**Fiscal-year and calendar-year GDP growth are kept as two separate series, deliberately.** `BD_GDP_GROWTH_REAL` is BBS's own fiscal-year-basis series (Bangladesh's fiscal year runs July–June); `BD_GDP_GROWTH_REAL_WB` is the World Bank's calendar-year WDI series. They will not match for the same nominal year, and this database does not attempt to reconcile them — query both and compare explicitly if you need to.

**The policy rate story is split across three related-but-distinct series.** `BD_POLICY_REPO_RATE` is Bangladesh Bank's modern policy repo rate (data from 2020 onward). `BD_BANK_RATE` is BB's older classical Bank Rate, which historically anchored the discount window before the repo rate was introduced. `BD_CALL_MONEY_RATE` is the market-determined interbank call money rate, included as a proxy for policy-rate history precisely because a single clean modern repo-rate time series covering earlier years was not available in BB's published files. Don't average these together — pick the one that matches what you're actually trying to measure.

**`value_type` matters more than it looks.** Several series (notably `BD_GDP_GROWTH_REAL` and `BD_FX_RESERVES`) mix `actual`, `provisional`, and `forecast` rows in the same series. A provisional BBS estimate for FY2024 and an ADB forecast for FY2027 are fundamentally different kinds of numbers sitting in the same column — always read `value_type` and `notes` alongside `value`.

**Series periods are not always in a single consistent format.** Some series report period labels as fiscal years (`FY2025`), some as bare calendar years (`2026`), and some as year-month (`2026-05`). Use `period_date` (an ISO date) for chronological sorting and cross-series joins rather than parsing `period` directly.

**This is a smaller, denser companion dataset, not a bulk macro dump.** At 12 series and 581 observations, CryingCatalyst prioritizes a clean, well-documented core of the indicators most commonly needed for Bangladesh macro analysis (growth, inflation, reserves, exchange rate, remittances, rates, population, unemployment) over exhaustively mirroring every series Bangladesh Bank or BBS publishes. The `categories` table is scaffolded with additional top-level and sub-categories (Fiscal Sector, Sectoral Real Economy, Social & Development, Econometric/Derived Series, and more) for series that may be added in future releases.

---

## Sources & attribution

[#sources--attribution](#sources--attribution)

| Source                                            | Abbreviation | What it provides                                                      | Access method                             |
| --------------------------------------------------- | ------------- | ------------------------------------------------------------------------ | --------------------------------------------- |
| [Bangladesh Bank](https://www.bb.org.bd/en/index.php/econdata/index) | BB | Monetary Policy Statements, Major Economic Indicators, FX reserves, exchange rate, policy/bank/call money rates, remittances | bulk download / Excel / open data portal |
| [Bangladesh Bureau of Statistics](http://www.bbs.gov.bd) | BBS | GDP (national accounts), CPI/inflation, labour force survey, HIES, census | manual/report + bulk download |
| [Ministry of Finance, Bangladesh](https://mof.gov.bd) | MoF | Budget documents, Bangladesh Economic Review, fiscal data, public debt | manual/report |
| [National Board of Revenue](https://nbr.gov.bd) | NBR | Tax revenue collection data | manual/report |
| [World Bank](https://data.worldbank.org/country/bangladesh) | WB | GDP, growth, population, unemployment (WDI) | API (no key required) |
| [International Monetary Fund](https://www.imf.org/en/Countries/BGD) | IMF | Macro estimates, reserves, external sector data | API / bulk download |
| [Asian Development Bank](https://www.adb.org/where-we-work/bangladesh/economy) | ADB | Growth forecasts, Key Indicators database | manual/report + Key Indicators database |
| [Export Promotion Bureau](http://epb.gov.bd) | EPB | Trade and export data | manual/report |
| [Bangladesh Investment Development Authority](https://bida.gov.bd) | BIDA | Investment data | manual/report |
| [CEIC / Trading Economics](https://tradingeconomics.com/bangladesh/indicators) | CEIC/TE | Aggregated indicator cross-checks | API (paid) / scrape |

This repository's **code and schema** are MIT licensed (see `LICENSE`). The **underlying data** originates from the third parties above — verify their individual terms before redistributing the data itself at scale.

---

## Citation

[#citation](#citation)

See `CITATION.cff`. In short:

```
Ornob, K. M. Miad Hassan (Ow1nomics). (2026). CryingCatalyst: A Consolidated
Macroeconomic Indicator Database for Bangladesh.
https://github.com/o-rnob/CryingCatalyst-DB
```

---

## A note on the name

[#a-note-on-the-name](#a-note-on-the-name)

CryingCatalyst pairs with [KnightBase](https://github.com/o-rnob/Knightbase-DB): where KnightBase tracks what individual securities on the Dhaka Stock Exchange actually did, CryingCatalyst tracks the macroeconomic backdrop — growth, prices, reserves, and rates — that ultimately drives those securities. It is offered as a good-faith, actively-documented dataset rather than a claim of being the single most comprehensive Bangladesh macro dataset available; where a number is provisional, estimated, or forecasted rather than confirmed, that is marked explicitly in `value_type` rather than left for the reader to guess.
