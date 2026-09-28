# For Grading

Iman Satyo Adi (2306208262) — Praktikum Riset Keuangan, Week 4 Assignment

This folder contains the six required deliverables only, matching the assignment
guide's "Expected submissions" table. Everything else in the repository (audit
route detail, benchmark comparison files, the optional sector-level test) is
supporting evidence, kept one level up for reference but not required reading.

**Start with `Replication_Memo_Full_Construction.docx`** — it explains the method,
every deviation from the reference workflow, and one known open issue (formation
year 2024, Section 4), and points to the specific files below for each claim.

| File | What it is |
|---|---|
| `Replication_Memo_Full_Construction.docx` | Method, deviations, limitations, results — read this first |
| `Built_Annual_Breakpoints.csv` | Size/BM breakpoints by formation year (2014–2026) × variant (ALL/SCREENED) |
| `Built_Annual_Membership.csv` | Firm-level portfolio assignment (SL/SN/SH/BL/BN/BH) by formation year |
| `Built_IDX_6_Portfolios_{Daily,Weekly,Monthly}_{ALL,SCREENED}.csv` | The six value-weighted size/BM portfolio return series (6 files) |
| `Built_IDX_FF3_{Daily,Weekly,Monthly}_{ALL,SCREENED}.csv` | Final SMB and HML factor series (6 files) |
| `Validation_Sheet.csv` | Pass/fail result of every quality-control check specified in the guide |

## Headline results

- 901/901 entities matched from the raw stock panel
- 56/56 return observations capped, 903/903 market-cap adjustments — both match the guide's verified-build totals exactly
- Annual breakpoints match the benchmark exactly in 24 of 26 formation-year × variant combinations (2014, 2015, 2026 spot-checks in the memo all exact); formation year 2024 has a small, disclosed deviation (memo Section 4)
- Final daily factors correlate 0.9994–0.99998 (SMB) and 0.997–0.9998 (HML) with the benchmark files
