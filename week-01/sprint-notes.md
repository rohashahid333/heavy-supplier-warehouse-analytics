# Sprint Notes: Week 1

**Scrum Master:** (your name), working solo
**Sprint goal:** Profile all 12 tables, check how they join, and document data quality issues.

## Tasks
- [x] Load and profile all 12 CSV files (shape, types, nulls, duplicates, stats)
- [x] Check date ranges
- [x] Check relationships between tables (orphan keys)
- [x] Run business-rule checks (order totals, invoices, payments, stock)
- [x] Write findings summary

## Done
- Profiling notebook: `01_data_profiling.ipynb`
- Profile table: `profile_summary.csv`
- Findings: `findings_summary.md`

## Key results
- 12 tables, about 605,000 rows in total, with no orphan keys
- 10 data quality issues logged, most importantly duplicate invoice IDs and stock levels far above max stock

## Blockers
- Stock levels (issue 6) need the Fields Documentation to confirm units.

## Next week
- Data cleaning and validation, starting with issues 1, 2, 6 and 8.
