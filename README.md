# Indonesia SMB/HML Factor Replication

```
 ___ __  __ ___       _  _ __  __ _
/ __|  \/  | _ )     | || |  \/  | |
\__ \ |\/| | _ \  _  | __ | |\/| | |__
|___/_|  |_|___/ (_) |_||_|_|  |_|____|

        Indonesia Equity Factor Construction · IDX 2015-2026
```

```
                     6-portfolio sort, IDX universe
                     ─────────────────────────────

         Small ME              Big ME
        ┌─────────┐          ┌─────────┐
  Low    │   SL    │          │   BL    │    ┐
  BM     │  ░░░░░  │          │  ░░░░░  │    │
        ├─────────┤          ├─────────┤     │  HML =
  Mid    │   SN    │          │   BN    │    │  avg(High) − avg(Low)
  BM     │  ▒▒▒▒▒  │          │  ▒▒▒▒▒  │    │
        ├─────────┤          ├─────────┤     │
  High   │   SH    │          │   BH    │    ┘
  BM     │  ▓▓▓▓▓  │          │  ▓▓▓▓▓  │
        └─────────┘          └─────────┘
             └──────────┬──────────┘
                         │
              SMB = avg(Small) − avg(Big)
```

**Course:** Praktikum Riset Keuangan — Week 4 Assignment
**Author:** Iman Satyo Adi (2306208262) — [github.com/mnstyd](https://github.com/mnstyd)
**Repository:** _(add link once pushed, e.g. github.com/mnstyd/indonesia-smb-hml-replication)_
**Research lead / guide author:** Zaäfri Husodo

## Objective

Replicate the Fama-French (2008, Welch & Goyal-style diagnostic framework) SMB and
HML factor construction methodology using Indonesian equity market (IDX) data, to
build an understanding of size and value factor construction end-to-end: from raw
stock-level total return and market capitalization data, through annual
size/book-to-market portfolio formation, to daily/weekly/monthly SMB and HML factor
series — validated against a set of instructor-supplied benchmark files at every
stage.

Two build routes were completed:

- **Audit route** — recompute SMB/HML from six pre-built size/value portfolios and
  check against the supplied factor files.
- **Full construction route** — build everything from raw stock-level data: clean
  returns and market caps, form annual size/book-to-market portfolios, compute
  value-weighted portfolio returns, derive SMB/HML, and aggregate to weekly/monthly.

> **For grading:** the six required deliverables are collected in
> [`0_For_Grading/`](0_For_Grading/) with a short guide — start there.
> Full detail on every file is below.

> **Start here:** [`1_Core_Deliverables/Replication_Memo_Full_Construction.docx`](1_Core_Deliverables/Replication_Memo_Full_Construction.docx)

---

## Status

| Check | Result |
|---|---|
| Audit route (Daily, Weekly, ALL/SCREENED) | ✅ Pass — max deviation 0.000001pp (tol. 0.000002pp) |
| Audit route (Monthly) | ⚠️ Not checkable — no `IDX_FF3_Monthly` benchmark supplied |
| Entity match (raw panel) | ✅ 901 / 901, matches verified build |
| Return cleaning (±50% cap) | ✅ 56 observations, matches verified build |
| Market cap cleaning (outlier rule) | ✅ 903 adjustments, matches verified build |
| Annual breakpoints, spot-checked years (2014, 2015, 2026) | ✅ Exact match, both variants |
| Annual breakpoints, all 26 formation-year × variant rows | ✅ 24 / 26 exact; **1 open issue** (FY2024, below) |
| Final daily factors vs. benchmark | ✅ corr(SMB) 0.9994–0.99998, corr(HML) 0.997–0.9998 |

### ⚠️ Known open issue — formation year 2024

FY2024's book-to-market breakpoints differ from `IDX_FF3_Annual_Breakpoints.csv` by
up to 0.0032 percentage points (every other year matches to floating-point
precision). This reclassifies 18 of 14,107 firm-year-variant membership labels
(0.13%) and produces a small, visible deviation in daily SMB/HML during the
Jul 2024–Jun 2025 holding period. Diagnosed as a likely boundary-tie or
data-vintage effect — **not root-caused to a single observation**. See memo
Section 4 for full detail. Reported here rather than smoothed over.

---

## Repository structure

```
.
├── README.md
├── 0_For_Grading/                    # ★ the six required deliverables, collected for quick grading
│   ├── GRADING_GUIDE.md
│   ├── Replication_Memo_Full_Construction.docx
│   ├── Built_Annual_Breakpoints.csv
│   ├── Built_Annual_Membership.csv
│   ├── Built_IDX_6_Portfolios_{Daily,Weekly,Monthly}_{ALL,SCREENED}.csv
│   ├── Built_IDX_FF3_{Daily,Weekly,Monthly}_{ALL,SCREENED}.csv
│   └── Validation_Sheet.csv
├── 1_Core_Deliverables/              # same six outputs
│   ├── Replication_Memo_Full_Construction.docx
│   ├── Built_Annual_Breakpoints.csv
│   ├── Built_Annual_Membership.csv
│   ├── Built_IDX_6_Portfolios_{Daily,Weekly,Monthly}_{ALL,SCREENED}.csv
│   ├── Built_IDX_FF3_{Daily,Weekly,Monthly}_{ALL,SCREENED}.csv
│   └── Validation_Sheet.csv
├── 2_Audit_Route/                    # Steps 1-3: rebuild SMB/HML from the 6 portfolios
│   └── Audit_Route_Identity_Check.csv
├── 3_Benchmark_Validation/           # evidence behind every pass/fail in the validation sheet
│   ├── Breakpoints_vs_Benchmark_Comparison.csv
│   ├── Membership_vs_Benchmark_Comparison.csv
│   └── Built_Market_Cap_Adjustment_Log.csv
└── 4_Optional_Sector_Independent_Test/   # NOT a required deliverable, see note below
    ├── Replication_Memo_Sector_FF3_Tests.docx
    └── ...
```

---

## Methodology summary

1. **Reshape raw panel** — 8 tabs of Capital IQ export (market cap + total return,
   2015–2026, 901 entities). Columns located by mnemonic (`SP_ENTITY_ID`,
   `IQ_SECTOR`, `SP_MARKETCAP`, `SP_TOTAL_RETURN`), not fixed position, because one
   tab (`TR_jan21-dec23`) uses a shifted/swapped layout.
2. **Clean** — returns capped at ±50%; market cap cleaned with a rolling-median
   outlier rule (60-session window, [0.1×, 10×] bounds, USD 500,000M absolute cap).
3. **Align** — forward-filled to a 2,792-day class calendar (5 Jan 2015–30 Jul 2026),
   market cap lagged one trading day before use as a portfolio weight.
4. **Common equity & pre-sample anchors** — FH2 book equity 2013–2025; pre-sample
   market-cap anchors for Dec-2013/Jun-2014/Dec-2014 via "latest positive value on
   or before" (the raw sheet's exact-date columns for 31-Dec are blank for every
   entity — a real export gap, not a methodology choice).
5. **Annual formation** — size (median June ME) and value (BM 30th/70th percentile)
   breakpoints computed separately for ALL and SCREENED (≥ USD 10M liquidity)
   universes, for each formation year 2014–2026.
6. **Six portfolios → SMB/HML** — value-weighted daily returns from lagged market
   cap; SMB/HML built from the six portfolios directly at each frequency (never
   averaged from daily to weekly/monthly).

Full step-by-step detail, all formulas, and every diagnostic number are in the memo.

---

## Optional: sector independent test

`4_Optional_Sector_Independent_Test/` holds a supplementary spanning-test analysis
(11 Indonesian sector portfolios regressed on SIMM and FF3, as an out-of-sample
check per the guide's "Interpretation discipline" section) — **not one of the
guide's required deliverables**. Skip this folder if you only need the graded
outputs.

---

## AI-assistance disclosure

Claude (Anthropic) assisted with implementing the construction pipeline in Python,
diagnosing two raw-data issues (the shifted tab layout and the blank pre-sample
anchor columns), running the validation checks, and drafting the memo and this
README. The research design, factor definitions, screening rules, and benchmark
files were authored by Zaäfri Husodo and supplied by the student. All figures come
from the files provided in this exercise — nothing here was estimated or fabricated
to fill a gap.
