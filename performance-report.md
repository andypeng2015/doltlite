# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-09 21:31 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261101.186.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/42688236667)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.32s | 10.70s | 1.0× | 1.4% | **PASS** |
| Writes | 1.99s | 3.40s | 1.7× | 1.3% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.08s | 10.86s | 1.0× | 1.1% | **PASS** |
| Writes | 3.81s | 4.46s | 1.2× | 2.6% | **PASS** |
| Autocommit writes | 863.55ms | 2.76s | 3.2× | 8.9% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.69s | 2.77s | 1.0× | 1.8% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.49s | 2.68s | 1.1× | 0.9% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.62s | 2.83s | 1.1× | 1.7% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.51s | 2.42s | 1.0× | 1.5% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 431.94ms | 755.18ms | 1.7× | 1.5% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 486.50ms | 850.08ms | 1.7× | 0.9% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 597.67ms | 1.04s | 1.7× | 1.8% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 470.76ms | 756.35ms | 1.6× | 1.7% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.86s | 2.82s | 1.0× | 1.0% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.59s | 2.69s | 1.0× | 1.0% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.87s | 2.89s | 1.0× | 1.6% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.77s | 2.47s | 0.9× | 1.7% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 590.67ms | 817.34ms | 1.4× | 1.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.19s | 1.31s | 1.1× | 3.9% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 840.26ms | 1.14s | 1.4× | 2.2% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 1.19s | 1.19s | 1.0× | 15.9% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.67s | 2.82s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.51s | 2.67s | 1.1× | 0.9% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.73s | 2.89s | 1.1× | 1.8% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.51s | 2.44s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 138.98ms | 482.91ms | 3.5× | 5.5% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 229.02ms | 619.48ms | 2.7× | 4.6% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 222.51ms | 676.12ms | 3.0× | 9.9% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 273.04ms | 977.62ms | 3.6× | 60.9% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 25.66ms | 28.55ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 11.67ms | 11.40ms | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_sum_range` | 10.01ms | 11.02ms | 1.1× | 2.4% | PASS |
| mem_reads | `oltp_order_range` | 2.73ms | 2.87ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.74ms | 3.99ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.98ms | 5.14ms | 1.3× | 2.1% | PASS |
| mem_reads | `select_random_points` | 11.49ms | 11.37ms | 1.0× | 4.5% | PASS |
| mem_reads | `select_random_ranges` | 3.31ms | 3.98ms | 1.2× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.23ms | 4.17ms | 1.0× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 31.40ms | 34.81ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 5.70ms | 7.71ms | 1.4× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 3.44ms | 4.75ms | 1.4× | 3.3% | PASS |
| mem_reads | `types_table_scan` | 1.12s | 1.20s | 1.1× | 1.6% | PASS |
| mem_reads | `table_scan` | 1.35s | 1.33s | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_read_only` | 110.97ms | 117.19ms | 1.1× | 1.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 173.49ms | 267.13ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 15.03ms | 29.10ms | 1.9× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 52.77ms | 105.48ms | 2.0× | 1.9% | PASS |
| mem_writes | `oltp_update_non_index` | 36.03ms | 65.60ms | 1.8× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 44.46ms | 79.74ms | 1.8× | 2.2% | PASS |
| mem_writes | `oltp_write_only` | 21.81ms | 52.94ms | 2.4× | 1.5% | FAIL |
| mem_writes | `types_delete_insert` | 24.12ms | 41.61ms | 1.7× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 64.23ms | 113.59ms | 1.8× | 1.8% | PASS |
| file_reads | `oltp_point_select` | 113.78ms | 49.82ms | 0.4× | 0.6% | PASS |
| file_reads | `oltp_range_select` | 21.37ms | 13.68ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_sum_range` | 19.51ms | 13.39ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 3.80ms | 3.17ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.80ms | 4.24ms | 0.9× | 0.7% | PASS |
| file_reads | `oltp_index_scan` | 13.07ms | 7.50ms | 0.6× | 1.1% | PASS |
| file_reads | `select_random_points` | 21.27ms | 13.61ms | 0.6× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 12.24ms | 6.13ms | 0.5× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 13.73ms | 6.69ms | 0.5× | 1.0% | PASS |
| file_reads | `groupby_scan` | 32.67ms | 35.27ms | 1.1× | 0.8% | PASS |
| file_reads | `index_join` | 10.80ms | 9.51ms | 0.9× | 1.6% | PASS |
| file_reads | `index_join_scan` | 4.52ms | 5.06ms | 1.1× | 2.2% | PASS |
| file_reads | `types_table_scan` | 1.10s | 1.19s | 1.1× | 1.0% | PASS |
| file_reads | `table_scan` | 1.26s | 1.31s | 1.0× | 1.7% | PASS |
| file_reads | `oltp_read_only` | 232.74ms | 145.94ms | 0.6× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 185.90ms | 273.78ms | 1.5× | 0.7% | PASS |
| file_writes | `oltp_insert` | 21.47ms | 32.02ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 78.95ms | 115.47ms | 1.5× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 59.88ms | 78.04ms | 1.3× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 69.95ms | 93.33ms | 1.3× | 1.3% | PASS |
| file_writes | `oltp_write_only` | 44.84ms | 60.56ms | 1.4× | 1.8% | PASS |
| file_writes | `types_delete_insert` | 40.64ms | 43.09ms | 1.1× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 89.03ms | 121.05ms | 1.4× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 49.74ms | 48.50ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_range_select` | 13.58ms | 13.33ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 12.26ms | 13.02ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 3.08ms | 3.14ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_distinct_range` | 4.04ms | 4.20ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_index_scan` | 6.73ms | 7.21ms | 1.1× | 1.5% | PASS |
| ac_reads | `select_random_points` | 13.60ms | 13.46ms | 1.0× | 2.1% | PASS |
| ac_reads | `select_random_ranges` | 5.95ms | 6.11ms | 1.0× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.17ms | 6.39ms | 0.9× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 31.46ms | 35.00ms | 1.1× | 0.5% | PASS |
| ac_reads | `index_join` | 7.69ms | 9.58ms | 1.2× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.95ms | 5.07ms | 1.3× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 1.12s | 1.20s | 1.1× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.25s | 1.31s | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 144.84ms | 145.77ms | 1.0× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 15.54ms | 47.71ms | 3.1× | 5.9% | PASS |
| ac_writes | `oltp_insert_ac` | 17.93ms | 60.45ms | 3.4× | 6.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 19.11ms | 72.70ms | 3.8× | 6.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 15.82ms | 53.59ms | 3.4× | 4.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 16.89ms | 63.48ms | 3.8× | 5.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 16.80ms | 63.35ms | 3.8× | 5.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 15.12ms | 53.57ms | 3.5× | 6.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 21.77ms | 68.06ms | 3.1× | 3.3% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 31.79ms | 31.67ms | 1.0× | 0.6% | PASS |
| mem_reads | `oltp_range_select` | 14.60ms | 12.49ms | 0.9× | 0.9% | PASS |
| mem_reads | `oltp_sum_range` | 14.10ms | 11.90ms | 0.8× | 0.9% | PASS |
| mem_reads | `oltp_order_range` | 3.09ms | 2.98ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_distinct_range` | 4.04ms | 3.99ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.65ms | 5.27ms | 1.4× | 1.2% | PASS |
| mem_reads | `select_random_points` | 21.35ms | 19.68ms | 0.9× | 0.9% | PASS |
| mem_reads | `select_random_ranges` | 3.07ms | 4.68ms | 1.5× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 3.45ms | 3.92ms | 1.1× | 1.2% | PASS |
| mem_reads | `groupby_scan` | 34.52ms | 33.56ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 10.73ms | 8.34ms | 0.8× | 1.0% | PASS |
| mem_reads | `index_join_scan` | 3.29ms | 5.25ms | 1.6× | 1.0% | PASS |
| mem_reads | `types_table_scan` | 1.03s | 1.16s | 1.1× | 0.3% | PASS |
| mem_reads | `table_scan` | 1.19s | 1.25s | 1.1× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 122.91ms | 120.71ms | 1.0× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.95ms | 292.09ms | 1.6× | 0.4% | PASS |
| mem_writes | `oltp_insert` | 14.95ms | 32.75ms | 2.2× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 58.53ms | 130.79ms | 2.2× | 0.9% | PASS |
| mem_writes | `oltp_update_non_index` | 41.50ms | 77.14ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_delete_insert` | 47.46ms | 91.53ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 24.28ms | 54.63ms | 2.2× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 33.60ms | 47.34ms | 1.4× | 1.1% | PASS |
| mem_writes | `oltp_read_write` | 81.23ms | 123.81ms | 1.5× | 0.8% | PASS |
| file_reads | `oltp_point_select` | 61.71ms | 37.76ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 18.07ms | 13.38ms | 0.7× | 1.0% | PASS |
| file_reads | `oltp_sum_range` | 17.63ms | 12.89ms | 0.7× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 3.54ms | 3.16ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.48ms | 4.14ms | 0.9× | 0.6% | PASS |
| file_reads | `oltp_index_scan` | 6.93ms | 6.33ms | 0.9× | 1.0% | PASS |
| file_reads | `select_random_points` | 25.50ms | 20.93ms | 0.8× | 0.8% | PASS |
| file_reads | `select_random_ranges` | 6.20ms | 5.42ms | 0.9× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 6.67ms | 4.86ms | 0.7× | 1.0% | PASS |
| file_reads | `groupby_scan` | 35.01ms | 33.62ms | 1.0× | 0.6% | PASS |
| file_reads | `index_join` | 12.82ms | 9.73ms | 0.8× | 0.8% | PASS |
| file_reads | `index_join_scan` | 3.80ms | 5.66ms | 1.5× | 1.0% | PASS |
| file_reads | `types_table_scan` | 1.02s | 1.15s | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.19s | 1.25s | 1.0× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 166.84ms | 129.35ms | 0.8× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 259.48ms | 364.00ms | 1.4× | 4.1% | PASS |
| file_writes | `oltp_insert` | 26.87ms | 58.35ms | 2.2× | 9.4% | PASS |
| file_writes | `oltp_update_index` | 170.83ms | 214.85ms | 1.3× | 3.2% | PASS |
| file_writes | `oltp_update_non_index` | 136.02ms | 136.51ms | 1.0× | 4.4% | PASS |
| file_writes | `oltp_delete_insert` | 171.30ms | 158.73ms | 0.9× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 117.61ms | 111.52ms | 0.9× | 3.8% | PASS |
| file_writes | `types_delete_insert` | 137.67ms | 84.36ms | 0.6× | 8.4% | PASS |
| file_writes | `oltp_read_write` | 170.73ms | 178.66ms | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_point_select` | 41.45ms | 37.77ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 15.84ms | 13.29ms | 0.8× | 0.7% | PASS |
| ac_reads | `oltp_sum_range` | 15.42ms | 12.82ms | 0.8× | 0.9% | PASS |
| ac_reads | `oltp_order_range` | 3.29ms | 3.12ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.24ms | 4.15ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 5.07ms | 6.32ms | 1.2× | 1.5% | PASS |
| ac_reads | `select_random_points` | 23.51ms | 20.95ms | 0.9× | 1.0% | PASS |
| ac_reads | `select_random_ranges` | 4.25ms | 5.43ms | 1.3× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 4.64ms | 4.86ms | 1.0× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 34.59ms | 33.62ms | 1.0× | 0.5% | PASS |
| ac_reads | `index_join` | 11.83ms | 9.57ms | 0.8× | 1.1% | PASS |
| ac_reads | `index_join_scan` | 3.61ms | 5.60ms | 1.6× | 1.2% | PASS |
| ac_reads | `types_table_scan` | 1.02s | 1.15s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.24s | 1.0× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 136.97ms | 129.68ms | 0.9× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 27.29ms | 67.03ms | 2.5× | 4.6% | PASS |
| ac_writes | `oltp_insert_ac` | 28.84ms | 76.29ms | 2.6× | 5.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.46ms | 84.94ms | 2.9× | 4.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 26.55ms | 71.98ms | 2.7× | 4.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.73ms | 79.30ms | 2.8× | 4.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.33ms | 81.67ms | 2.9× | 9.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 27.05ms | 72.99ms | 2.7× | 4.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.76ms | 85.29ms | 2.6× | 3.6% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.18ms | 38.46ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 17.18ms | 14.17ms | 0.8× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 15.37ms | 13.66ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 3.38ms | 3.27ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.34ms | 4.35ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 4.04ms | 6.25ms | 1.5× | 1.7% | PASS |
| mem_reads | `select_random_points` | 23.02ms | 20.73ms | 0.9× | 2.6% | PASS |
| mem_reads | `select_random_ranges` | 3.53ms | 5.34ms | 1.5× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 4.19ms | 4.75ms | 1.1× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 34.69ms | 35.11ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 10.64ms | 9.51ms | 0.9× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.91ms | 5.39ms | 1.4× | 2.3% | PASS |
| mem_reads | `types_table_scan` | 1.08s | 1.21s | 1.1× | 1.3% | PASS |
| mem_reads | `table_scan` | 1.24s | 1.31s | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_read_only` | 142.90ms | 140.89ms | 1.0× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 238.31ms | 374.25ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.22ms | 40.40ms | 2.2× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 66.95ms | 147.67ms | 2.2× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 49.86ms | 90.31ms | 1.8× | 2.3% | PASS |
| mem_writes | `oltp_delete_insert` | 53.85ms | 109.49ms | 2.0× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 28.51ms | 66.12ms | 2.3× | 2.0% | FAIL |
| mem_writes | `types_delete_insert` | 39.17ms | 57.73ms | 1.5× | 1.9% | PASS |
| mem_writes | `oltp_read_write` | 102.80ms | 151.77ms | 1.5× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 107.47ms | 56.28ms | 0.5× | 1.5% | PASS |
| file_reads | `oltp_range_select` | 25.39ms | 16.20ms | 0.6× | 2.3% | PASS |
| file_reads | `oltp_sum_range` | 23.26ms | 15.76ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 4.39ms | 3.57ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_distinct_range` | 5.42ms | 4.68ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 11.57ms | 8.19ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_points` | 31.93ms | 23.52ms | 0.7× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 10.60ms | 7.15ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.67ms | 6.64ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 36.36ms | 35.67ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 15.33ms | 10.99ms | 0.7× | 2.4% | PASS |
| file_reads | `index_join_scan` | 4.98ms | 5.76ms | 1.2× | 2.1% | PASS |
| file_reads | `types_table_scan` | 1.09s | 1.21s | 1.1× | 1.7% | PASS |
| file_reads | `table_scan` | 1.24s | 1.32s | 1.1× | 1.6% | PASS |
| file_reads | `oltp_read_only` | 253.65ms | 168.71ms | 0.7× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 258.60ms | 389.85ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 25.84ms | 48.11ms | 1.9× | 2.0% | PASS |
| file_writes | `oltp_update_index` | 103.80ms | 167.39ms | 1.6× | 2.3% | PASS |
| file_writes | `oltp_update_non_index` | 96.47ms | 104.68ms | 1.1× | 11.2% | PASS |
| file_writes | `oltp_delete_insert` | 90.33ms | 124.86ms | 1.4× | 2.6% | PASS |
| file_writes | `oltp_write_only` | 60.53ms | 78.52ms | 1.3× | 1.9% | PASS |
| file_writes | `types_delete_insert` | 67.31ms | 66.69ms | 1.0× | 2.6% | PASS |
| file_writes | `oltp_read_write` | 137.38ms | 164.14ms | 1.2× | 1.9% | PASS |
| ac_reads | `oltp_point_select` | 60.72ms | 56.63ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_range_select` | 20.68ms | 16.26ms | 0.8× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 18.49ms | 15.91ms | 0.9× | 3.0% | PASS |
| ac_reads | `oltp_order_range` | 3.96ms | 3.61ms | 0.9× | 2.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.93ms | 4.72ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.78ms | 8.20ms | 1.2× | 1.8% | PASS |
| ac_reads | `select_random_points` | 26.59ms | 23.35ms | 0.9× | 2.3% | PASS |
| ac_reads | `select_random_ranges` | 5.92ms | 7.14ms | 1.2× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 7.04ms | 6.62ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 35.48ms | 35.63ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 12.87ms | 11.00ms | 0.9× | 2.3% | PASS |
| ac_reads | `index_join_scan` | 4.42ms | 5.79ms | 1.3× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 1.10s | 1.21s | 1.1× | 1.9% | PASS |
| ac_reads | `table_scan` | 1.24s | 1.32s | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 182.71ms | 168.47ms | 0.9× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 25.05ms | 65.71ms | 2.6× | 8.9% | PASS |
| ac_writes | `oltp_insert_ac` | 28.08ms | 87.30ms | 3.1× | 10.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 30.51ms | 98.83ms | 3.2× | 9.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.33ms | 75.53ms | 3.1× | 8.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.27ms | 89.81ms | 3.3× | 13.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.62ms | 88.49ms | 3.3× | 9.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.70ms | 77.57ms | 3.0× | 10.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.96ms | 92.88ms | 2.7× | 11.7% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 28.71ms | 31.04ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_range_select` | 16.95ms | 16.57ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 14.64ms | 16.16ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 3.09ms | 3.14ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.87ms | 3.98ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.69ms | 4.72ms | 1.3× | 3.0% | PASS |
| mem_reads | `select_random_points` | 21.97ms | 23.93ms | 1.1× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 6.00ms | 6.64ms | 1.1× | 0.8% | PASS |
| mem_reads | `covering_index_scan` | 3.42ms | 3.78ms | 1.1× | 3.6% | PASS |
| mem_reads | `groupby_scan` | 30.08ms | 32.79ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 6.52ms | 9.04ms | 1.4× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.69ms | 4.93ms | 1.3× | 2.5% | PASS |
| mem_reads | `types_table_scan` | 1.00s | 1.02s | 1.0× | 1.6% | PASS |
| mem_reads | `table_scan` | 1.24s | 1.11s | 0.9× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 131.22ms | 135.01ms | 1.0× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 183.81ms | 265.84ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 14.68ms | 27.61ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 55.29ms | 102.54ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 43.91ms | 68.97ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 40.98ms | 77.66ms | 1.9× | 1.8% | PASS |
| mem_writes | `oltp_write_only` | 22.63ms | 48.36ms | 2.1× | 2.1% | PASS |
| mem_writes | `types_delete_insert` | 26.59ms | 41.45ms | 1.6× | 1.9% | PASS |
| mem_writes | `oltp_read_write` | 82.85ms | 123.91ms | 1.5× | 2.4% | PASS |
| file_reads | `oltp_point_select` | 96.26ms | 46.56ms | 0.5× | 2.0% | PASS |
| file_reads | `oltp_range_select` | 24.28ms | 18.25ms | 0.8× | 2.2% | PASS |
| file_reads | `oltp_sum_range` | 21.72ms | 17.75ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 3.90ms | 3.35ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.74ms | 4.23ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 11.00ms | 6.76ms | 0.6× | 0.9% | PASS |
| file_reads | `select_random_points` | 28.13ms | 25.31ms | 0.9× | 1.9% | PASS |
| file_reads | `select_random_ranges` | 13.07ms | 8.46ms | 0.6× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 10.86ms | 5.47ms | 0.5× | 1.9% | PASS |
| file_reads | `groupby_scan` | 31.06ms | 32.93ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 10.61ms | 10.16ms | 1.0× | 3.7% | PASS |
| file_reads | `index_join_scan` | 4.60ms | 5.18ms | 1.1× | 5.1% | PASS |
| file_reads | `types_table_scan` | 1.02s | 1.02s | 1.0× | 0.9% | PASS |
| file_reads | `table_scan` | 1.26s | 1.11s | 0.9× | 2.6% | PASS |
| file_reads | `oltp_read_only` | 224.71ms | 156.19ms | 0.7× | 1.7% | PASS |
| file_writes | `oltp_bulk_insert` | 275.15ms | 337.76ms | 1.2× | 20.2% | PASS |
| file_writes | `oltp_insert` | 31.47ms | 53.71ms | 1.7× | 51.1% | PASS |
| file_writes | `oltp_update_index` | 172.13ms | 173.18ms | 1.0× | 12.3% | PASS |
| file_writes | `oltp_update_non_index` | 161.79ms | 134.52ms | 0.8× | 20.0% | PASS |
| file_writes | `oltp_delete_insert` | 151.72ms | 134.10ms | 0.9× | 18.2% | PASS |
| file_writes | `oltp_write_only` | 103.68ms | 95.50ms | 0.9× | 11.4% | PASS |
| file_writes | `types_delete_insert` | 92.60ms | 81.33ms | 0.9× | 12.6% | PASS |
| file_writes | `oltp_read_write` | 199.74ms | 176.81ms | 0.9× | 13.5% | PASS |
| ac_reads | `oltp_point_select` | 48.71ms | 47.48ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 19.31ms | 18.33ms | 0.9× | 2.2% | PASS |
| ac_reads | `oltp_sum_range` | 16.90ms | 17.88ms | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_order_range` | 3.50ms | 3.34ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.29ms | 4.21ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 6.45ms | 6.85ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_points` | 24.11ms | 25.77ms | 1.1× | 2.9% | PASS |
| ac_reads | `select_random_ranges` | 8.41ms | 8.50ms | 1.0× | 0.7% | PASS |
| ac_reads | `covering_index_scan` | 6.04ms | 5.48ms | 0.9× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 30.22ms | 32.78ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 8.01ms | 9.80ms | 1.2× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 3.77ms | 4.98ms | 1.3× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 950.03ms | 998.59ms | 1.1× | 3.6% | PASS |
| ac_reads | `table_scan` | 1.23s | 1.10s | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_read_only` | 156.94ms | 156.77ms | 1.0× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.80ms | 76.73ms | 3.4× | 44.7% | PASS |
| ac_writes | `oltp_insert_ac` | 33.17ms | 129.81ms | 3.9× | 66.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 36.45ms | 129.16ms | 3.5× | 65.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 38.25ms | 90.94ms | 2.4× | 58.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.29ms | 110.49ms | 3.9× | 54.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 41.44ms | 165.69ms | 4.0× | 63.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 30.71ms | 142.83ms | 4.7× | 66.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 41.92ms | 131.97ms | 3.1× | 54.9% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 45s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 68.83ms | 130.00ms | 52.9% | 0.4% | PASS |
| `status_dirty_many_tables` | 71.59ms | 130.00ms | 55.1% | 0.4% | PASS |
| `diff_regular_working_one_table` | 65.00ms | 120.00ms | 54.2% | 0.5% | PASS |
| `diff_regular_working_many_tables` | 75.30ms | 140.00ms | 53.8% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 75.26ms | 140.00ms | 53.8% | 0.4% | PASS |
| `diff_schema_working_many_tables` | 75.91ms | 140.00ms | 54.2% | 0.4% | PASS |
| `branch_list_many_branches` | 16.90ms | 35.00ms | 48.3% | 2.4% | PASS |
| `branch_create_delete` | 18.53ms | 40.00ms | 46.3% | 1.8% | PASS |
| `checkout_branch_clean` | 92.19ms | 150.00ms | 61.5% | 2.6% | PASS |
| `merge_data_no_conflicts` | 29.18ms | 50.00ms | 58.4% | 6.9% | PASS |
| `merge_schema_no_conflicts` | 16.36ms | 35.00ms | 46.7% | 2.3% | PASS |
| `merge_data_conflicts` | 24.02ms | 180.00ms | 13.3% | 1.3% | PASS |
| `merge_data_conflicts_with_resolve` | 24.11ms | 180.00ms | 13.4% | 1.5% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
