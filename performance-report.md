# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-10-01 19:22 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260927.320.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/36893478347)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.41s | 9.71s | 1.0× | 2.0% | **PASS** |
| Writes | 1.89s | 3.10s | 1.6× | 1.6% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.28s | 9.95s | 1.0× | 1.8% | **PASS** |
| Writes | 3.56s | 4.09s | 1.1× | 3.0% | **FAIL** |
| Autocommit writes | 828.18ms | 2.37s | 2.9× | 5.3% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.10s | 2.21s | 1.1× | 4.1% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.85s | 1.86s | 1.0× | 2.8% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.57s | 2.74s | 1.1× | 1.6% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.89s | 2.90s | 1.0× | 1.5% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 316.50ms | 509.06ms | 1.6× | 3.5% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 393.82ms | 638.75ms | 1.6× | 2.6% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 588.34ms | 985.53ms | 1.7× | 1.4% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 588.32ms | 964.48ms | 1.6× | 1.0% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.18s | 2.23s | 1.0× | 3.6% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.18s | 1.94s | 0.9× | 1.9% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.81s | 2.81s | 1.0× | 1.3% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.10s | 2.97s | 1.0× | 1.4% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 816.28ms | 840.36ms | 1.0× | 3.4% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.12s | 1.07s | 1.0× | 5.0% | **FAIL** |
| File-backed | Writes | blobpk | 8 | 55 | 808.88ms | 1.08s | 1.3× | 1.6% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 812.96ms | 1.10s | 1.4× | 2.1% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.09s | 2.18s | 1.0× | 3.0% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.82s | 1.85s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.66s | 2.81s | 1.1× | 1.5% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.82s | 2.94s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 221.72ms | 605.05ms | 2.7× | 4.9% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 186.18ms | 496.44ms | 2.7× | 4.0% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 208.95ms | 616.03ms | 2.9× | 5.3% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 211.34ms | 654.48ms | 3.1× | 6.1% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 18.46ms | 20.57ms | 1.1× | 5.1% | PASS |
| mem_reads | `oltp_range_select` | 7.89ms | 8.58ms | 1.1× | 5.4% | PASS |
| mem_reads | `oltp_sum_range` | 7.09ms | 8.07ms | 1.1× | 4.4% | PASS |
| mem_reads | `oltp_order_range` | 2.02ms | 2.21ms | 1.1× | 2.7% | PASS |
| mem_reads | `oltp_distinct_range` | 2.86ms | 3.09ms | 1.1× | 3.6% | PASS |
| mem_reads | `oltp_index_scan` | 2.88ms | 3.49ms | 1.2× | 4.1% | PASS |
| mem_reads | `select_random_points` | 8.21ms | 8.76ms | 1.1× | 4.8% | PASS |
| mem_reads | `select_random_ranges` | 2.30ms | 2.85ms | 1.2× | 3.7% | PASS |
| mem_reads | `covering_index_scan` | 2.98ms | 3.04ms | 1.0× | 4.5% | PASS |
| mem_reads | `groupby_scan` | 25.57ms | 26.95ms | 1.1× | 3.0% | PASS |
| mem_reads | `index_join` | 4.55ms | 6.29ms | 1.4× | 4.7% | PASS |
| mem_reads | `index_join_scan` | 2.35ms | 3.51ms | 1.5× | 4.6% | PASS |
| mem_reads | `types_table_scan` | 900.00ms | 952.17ms | 1.1× | 1.3% | PASS |
| mem_reads | `table_scan` | 1.03s | 1.07s | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_read_only` | 82.71ms | 93.14ms | 1.1× | 3.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 128.58ms | 188.27ms | 1.5× | 2.9% | PASS |
| mem_writes | `oltp_insert` | 11.07ms | 20.17ms | 1.8× | 5.1% | PASS |
| mem_writes | `oltp_update_index` | 37.87ms | 70.96ms | 1.9× | 3.1% | PASS |
| mem_writes | `oltp_update_non_index` | 24.78ms | 41.83ms | 1.7× | 4.6% | PASS |
| mem_writes | `oltp_delete_insert` | 33.49ms | 50.86ms | 1.5× | 3.3% | PASS |
| mem_writes | `oltp_write_only` | 15.54ms | 32.00ms | 2.1× | 4.3% | PASS |
| mem_writes | `types_delete_insert` | 17.53ms | 26.16ms | 1.5× | 3.0% | PASS |
| mem_writes | `oltp_read_write` | 47.63ms | 78.81ms | 1.7× | 3.8% | PASS |
| file_reads | `oltp_point_select` | 44.36ms | 26.85ms | 0.6× | 3.7% | PASS |
| file_reads | `oltp_range_select` | 10.88ms | 9.41ms | 0.9× | 5.4% | PASS |
| file_reads | `oltp_sum_range` | 9.94ms | 9.12ms | 0.9× | 4.8% | PASS |
| file_reads | `oltp_order_range` | 2.43ms | 2.38ms | 1.0× | 3.6% | PASS |
| file_reads | `oltp_distinct_range` | 3.22ms | 3.29ms | 1.0× | 3.6% | PASS |
| file_reads | `oltp_index_scan` | 5.74ms | 4.36ms | 0.8× | 3.2% | PASS |
| file_reads | `select_random_points` | 11.99ms | 10.43ms | 0.9× | 6.1% | PASS |
| file_reads | `select_random_ranges` | 4.97ms | 3.60ms | 0.7× | 5.8% | PASS |
| file_reads | `covering_index_scan` | 5.73ms | 3.68ms | 0.6× | 2.9% | PASS |
| file_reads | `groupby_scan` | 24.81ms | 26.30ms | 1.1× | 3.5% | PASS |
| file_reads | `index_join` | 5.96ms | 6.63ms | 1.1× | 4.0% | PASS |
| file_reads | `index_join_scan` | 2.76ms | 3.67ms | 1.3× | 6.4% | PASS |
| file_reads | `types_table_scan` | 913.47ms | 968.44ms | 1.1× | 1.9% | PASS |
| file_reads | `table_scan` | 1.02s | 1.05s | 1.0× | 2.0% | PASS |
| file_reads | `oltp_read_only` | 117.66ms | 98.88ms | 0.8× | 2.3% | PASS |
| file_writes | `oltp_bulk_insert` | 174.15ms | 238.09ms | 1.4× | 3.4% | PASS |
| file_writes | `oltp_insert` | 23.47ms | 33.59ms | 1.4× | 5.9% | PASS |
| file_writes | `oltp_update_index` | 129.03ms | 127.41ms | 1.0× | 3.0% | PASS |
| file_writes | `oltp_update_non_index` | 105.33ms | 87.40ms | 0.8× | 3.1% | PASS |
| file_writes | `oltp_delete_insert` | 112.51ms | 102.34ms | 0.9× | 3.7% | PASS |
| file_writes | `oltp_write_only` | 83.63ms | 74.43ms | 0.9× | 4.0% | PASS |
| file_writes | `types_delete_insert` | 71.79ms | 56.58ms | 0.8× | 3.4% | PASS |
| file_writes | `oltp_read_write` | 116.37ms | 120.50ms | 1.0× | 3.3% | PASS |
| ac_reads | `oltp_point_select` | 26.14ms | 25.98ms | 1.0× | 3.0% | PASS |
| ac_reads | `oltp_range_select` | 9.33ms | 9.68ms | 1.0× | 4.5% | PASS |
| ac_reads | `oltp_sum_range` | 9.03ms | 9.36ms | 1.0× | 4.1% | PASS |
| ac_reads | `oltp_order_range` | 2.31ms | 2.42ms | 1.0× | 2.9% | PASS |
| ac_reads | `oltp_distinct_range` | 3.09ms | 3.24ms | 1.1× | 2.4% | PASS |
| ac_reads | `oltp_index_scan` | 4.04ms | 4.42ms | 1.1× | 2.1% | PASS |
| ac_reads | `select_random_points` | 10.02ms | 10.06ms | 1.0× | 2.8% | PASS |
| ac_reads | `select_random_ranges` | 3.28ms | 3.60ms | 1.1× | 5.7% | PASS |
| ac_reads | `covering_index_scan` | 3.98ms | 3.77ms | 0.9× | 5.1% | PASS |
| ac_reads | `groupby_scan` | 24.81ms | 26.37ms | 1.1× | 4.6% | PASS |
| ac_reads | `index_join` | 5.14ms | 6.68ms | 1.3× | 4.3% | PASS |
| ac_reads | `index_join_scan` | 2.68ms | 3.81ms | 1.4× | 5.6% | PASS |
| ac_reads | `types_table_scan` | 889.67ms | 936.20ms | 1.1× | 1.0% | PASS |
| ac_reads | `table_scan` | 1.00s | 1.04s | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_read_only` | 90.94ms | 97.48ms | 1.1× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 26.32ms | 66.47ms | 2.5× | 5.7% | PASS |
| ac_writes | `oltp_insert_ac` | 28.54ms | 75.49ms | 2.6× | 4.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.76ms | 81.24ms | 2.8× | 5.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 26.13ms | 69.69ms | 2.7× | 4.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.96ms | 79.62ms | 2.8× | 5.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.93ms | 78.32ms | 2.8× | 2.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.91ms | 72.56ms | 2.8× | 5.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 30.18ms | 81.67ms | 2.7× | 3.6% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 28.52ms | 25.38ms | 0.9× | 4.3% | PASS |
| mem_reads | `oltp_range_select` | 12.12ms | 9.42ms | 0.8× | 3.1% | PASS |
| mem_reads | `oltp_sum_range` | 11.16ms | 9.29ms | 0.8× | 2.6% | PASS |
| mem_reads | `oltp_order_range` | 2.41ms | 2.09ms | 0.9× | 2.0% | PASS |
| mem_reads | `oltp_distinct_range` | 2.97ms | 2.64ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 2.84ms | 4.62ms | 1.6× | 1.9% | PASS |
| mem_reads | `select_random_points` | 17.82ms | 15.19ms | 0.9× | 4.3% | PASS |
| mem_reads | `select_random_ranges` | 2.66ms | 3.71ms | 1.4× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 2.60ms | 3.54ms | 1.4× | 3.9% | PASS |
| mem_reads | `groupby_scan` | 22.88ms | 21.30ms | 0.9× | 1.2% | PASS |
| mem_reads | `index_join` | 9.00ms | 7.30ms | 0.8× | 2.8% | PASS |
| mem_reads | `index_join_scan` | 2.93ms | 4.75ms | 1.6× | 4.9% | PASS |
| mem_reads | `types_table_scan` | 728.81ms | 774.08ms | 1.1× | 1.4% | PASS |
| mem_reads | `table_scan` | 909.64ms | 895.40ms | 1.0× | 3.3% | PASS |
| mem_reads | `oltp_read_only` | 89.88ms | 79.26ms | 0.9× | 3.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 149.25ms | 221.18ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 11.32ms | 24.45ms | 2.2× | 2.1% | PASS |
| mem_writes | `oltp_update_index` | 51.65ms | 106.59ms | 2.1× | 2.7% | PASS |
| mem_writes | `oltp_update_non_index` | 36.57ms | 57.15ms | 1.6× | 3.1% | PASS |
| mem_writes | `oltp_delete_insert` | 35.63ms | 65.76ms | 1.8× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 18.48ms | 39.69ms | 2.1× | 2.5% | PASS |
| mem_writes | `types_delete_insert` | 26.23ms | 34.08ms | 1.3× | 2.9% | PASS |
| mem_writes | `oltp_read_write` | 64.71ms | 89.85ms | 1.4× | 4.3% | PASS |
| file_reads | `oltp_point_select` | 87.62ms | 40.35ms | 0.5× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 19.34ms | 11.32ms | 0.6× | 3.3% | PASS |
| file_reads | `oltp_sum_range` | 18.81ms | 11.30ms | 0.6× | 3.6% | PASS |
| file_reads | `oltp_order_range` | 3.22ms | 2.38ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 3.69ms | 2.88ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 9.07ms | 5.91ms | 0.7× | 2.9% | PASS |
| file_reads | `select_random_points` | 21.62ms | 15.09ms | 0.7× | 0.9% | PASS |
| file_reads | `select_random_ranges` | 8.49ms | 5.19ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 8.82ms | 4.75ms | 0.5× | 2.7% | PASS |
| file_reads | `groupby_scan` | 21.62ms | 20.07ms | 0.9× | 1.3% | PASS |
| file_reads | `index_join` | 11.71ms | 7.65ms | 0.7× | 1.6% | PASS |
| file_reads | `index_join_scan` | 3.42ms | 4.50ms | 1.3× | 1.9% | PASS |
| file_reads | `types_table_scan` | 792.87ms | 778.79ms | 1.0× | 3.8% | PASS |
| file_reads | `table_scan` | 985.46ms | 921.93ms | 0.9× | 4.0% | PASS |
| file_reads | `oltp_read_only` | 189.15ms | 106.75ms | 0.6× | 1.9% | PASS |
| file_writes | `oltp_bulk_insert` | 222.23ms | 288.85ms | 1.3× | 4.5% | PASS |
| file_writes | `oltp_insert` | 19.91ms | 48.74ms | 2.4× | 9.9% | FAIL |
| file_writes | `oltp_update_index` | 167.21ms | 179.80ms | 1.1× | 3.2% | PASS |
| file_writes | `oltp_update_non_index` | 140.22ms | 113.80ms | 0.8× | 6.9% | PASS |
| file_writes | `oltp_delete_insert` | 171.76ms | 137.06ms | 0.8× | 5.6% | PASS |
| file_writes | `oltp_write_only` | 111.55ms | 89.05ms | 0.8× | 1.9% | PASS |
| file_writes | `types_delete_insert` | 137.06ms | 69.71ms | 0.5× | 11.1% | PASS |
| file_writes | `oltp_read_write` | 151.00ms | 138.67ms | 0.9× | 3.4% | PASS |
| ac_reads | `oltp_point_select` | 42.86ms | 38.16ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 12.59ms | 10.23ms | 0.8× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 12.70ms | 10.43ms | 0.8× | 1.4% | PASS |
| ac_reads | `oltp_order_range` | 2.72ms | 2.35ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 3.26ms | 2.82ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_index_scan` | 4.83ms | 5.66ms | 1.2× | 1.8% | PASS |
| ac_reads | `select_random_points` | 17.70ms | 15.22ms | 0.9× | 0.7% | PASS |
| ac_reads | `select_random_ranges` | 4.67ms | 5.27ms | 1.1× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 4.96ms | 4.94ms | 1.0× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 22.32ms | 20.91ms | 0.9× | 1.2% | PASS |
| ac_reads | `index_join` | 10.29ms | 7.81ms | 0.8× | 1.5% | PASS |
| ac_reads | `index_join_scan` | 3.24ms | 4.62ms | 1.4× | 1.1% | PASS |
| ac_reads | `types_table_scan` | 748.67ms | 785.78ms | 1.0× | 2.0% | PASS |
| ac_reads | `table_scan` | 815.85ms | 839.34ms | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_read_only` | 109.83ms | 97.90ms | 0.9× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.18ms | 53.70ms | 2.4× | 5.7% | PASS |
| ac_writes | `oltp_insert_ac` | 23.82ms | 61.24ms | 2.6× | 6.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.13ms | 69.30ms | 2.9× | 3.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.87ms | 57.40ms | 2.8× | 3.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.56ms | 64.13ms | 2.7× | 4.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.54ms | 62.90ms | 2.8× | 2.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.51ms | 57.27ms | 2.7× | 5.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.57ms | 70.51ms | 2.6× | 3.3% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.96ms | 37.98ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 15.67ms | 13.83ms | 0.9× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 14.78ms | 13.46ms | 0.9× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 3.12ms | 3.13ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 4.27ms | 4.25ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_index_scan` | 3.94ms | 6.08ms | 1.5× | 1.9% | PASS |
| mem_reads | `select_random_points` | 20.86ms | 20.60ms | 1.0× | 2.6% | PASS |
| mem_reads | `select_random_ranges` | 3.44ms | 5.27ms | 1.5× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 4.24ms | 4.54ms | 1.1× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 34.16ms | 34.64ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 9.98ms | 8.96ms | 0.9× | 2.2% | PASS |
| mem_reads | `index_join_scan` | 3.71ms | 5.32ms | 1.4× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.21s | 1.27s | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 135.23ms | 136.19ms | 1.0× | 2.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 247.27ms | 369.40ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 18.73ms | 38.36ms | 2.0× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 63.49ms | 136.08ms | 2.1× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 47.08ms | 83.20ms | 1.8× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 52.23ms | 101.51ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 27.65ms | 60.85ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 37.28ms | 52.50ms | 1.4× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 94.61ms | 143.64ms | 1.5× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 110.36ms | 58.85ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 23.35ms | 16.25ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 23.21ms | 15.96ms | 0.7× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 4.15ms | 3.44ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_distinct_range` | 5.26ms | 4.59ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 11.64ms | 8.67ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 29.91ms | 24.11ms | 0.8× | 2.4% | PASS |
| file_reads | `select_random_ranges` | 11.05ms | 7.47ms | 0.7× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.83ms | 6.97ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 35.36ms | 35.03ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 14.58ms | 10.96ms | 0.8× | 2.5% | PASS |
| file_reads | `index_join_scan` | 4.81ms | 5.72ms | 1.2× | 3.0% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.21s | 1.27s | 1.1× | 1.0% | PASS |
| file_reads | `oltp_read_only` | 249.92ms | 167.89ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 268.61ms | 381.94ms | 1.4× | 0.8% | PASS |
| file_writes | `oltp_insert` | 25.61ms | 45.68ms | 1.8× | 2.2% | PASS |
| file_writes | `oltp_update_index` | 95.72ms | 153.22ms | 1.6× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 84.62ms | 96.69ms | 1.1× | 8.1% | PASS |
| file_writes | `oltp_delete_insert` | 86.52ms | 115.37ms | 1.3× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 57.28ms | 71.59ms | 1.2× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 63.60ms | 61.64ms | 1.0× | 2.4% | PASS |
| file_writes | `oltp_read_write` | 126.92ms | 155.22ms | 1.2× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 60.01ms | 58.85ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 19.18ms | 16.29ms | 0.8× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 18.43ms | 15.96ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_order_range` | 3.74ms | 3.43ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.82ms | 4.58ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.76ms | 8.66ms | 1.3× | 1.9% | PASS |
| ac_reads | `select_random_points` | 25.08ms | 23.91ms | 1.0× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 6.14ms | 7.49ms | 1.2× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 7.02ms | 6.97ms | 1.0× | 2.0% | PASS |
| ac_reads | `groupby_scan` | 34.90ms | 35.15ms | 1.0× | 0.6% | PASS |
| ac_reads | `index_join` | 12.25ms | 10.99ms | 0.9× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 4.28ms | 5.74ms | 1.3× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.21s | 1.27s | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_read_only` | 175.93ms | 168.44ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.41ms | 62.55ms | 2.7× | 5.3% | PASS |
| ac_writes | `oltp_insert_ac` | 25.75ms | 77.83ms | 3.0× | 4.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.49ms | 88.35ms | 3.2× | 6.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.03ms | 71.92ms | 3.1× | 3.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.25ms | 80.25ms | 3.1× | 5.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.42ms | 78.47ms | 3.1× | 4.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.08ms | 71.68ms | 3.0× | 6.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.52ms | 84.98ms | 2.5× | 5.2% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.31ms | 41.67ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_range_select` | 20.24ms | 21.26ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_sum_range` | 18.36ms | 20.91ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 3.62ms | 3.89ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.74ms | 5.05ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 4.61ms | 6.27ms | 1.4× | 1.5% | PASS |
| mem_reads | `select_random_points` | 27.88ms | 32.40ms | 1.2× | 2.6% | PASS |
| mem_reads | `select_random_ranges` | 7.47ms | 8.97ms | 1.2× | 2.1% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.30ms | 1.0× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 36.04ms | 39.60ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 7.88ms | 10.37ms | 1.3× | 1.5% | PASS |
| mem_reads | `index_join_scan` | 3.82ms | 5.46ms | 1.4× | 2.5% | PASS |
| mem_reads | `types_table_scan` | 1.16s | 1.21s | 1.0× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.39s | 1.31s | 0.9× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 159.83ms | 176.09ms | 1.1× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 251.50ms | 355.50ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 19.09ms | 34.95ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 64.29ms | 125.03ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 48.73ms | 83.67ms | 1.7× | 0.8% | PASS |
| mem_writes | `oltp_delete_insert` | 48.30ms | 94.32ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 26.69ms | 58.45ms | 2.2× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 31.96ms | 52.81ms | 1.7× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 97.74ms | 159.73ms | 1.6× | 0.9% | PASS |
| file_reads | `oltp_point_select` | 108.45ms | 60.97ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 26.12ms | 23.32ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 25.17ms | 23.12ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 4.40ms | 4.16ms | 0.9× | 2.4% | PASS |
| file_reads | `oltp_distinct_range` | 5.63ms | 5.38ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 12.26ms | 8.47ms | 0.7× | 1.2% | PASS |
| file_reads | `select_random_points` | 37.71ms | 35.98ms | 1.0× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 15.89ms | 11.53ms | 0.7× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 11.84ms | 6.69ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 37.78ms | 40.23ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 12.33ms | 12.54ms | 1.0× | 1.6% | PASS |
| file_reads | `index_join_scan` | 5.00ms | 5.98ms | 1.2× | 2.3% | PASS |
| file_reads | `types_table_scan` | 1.15s | 1.21s | 1.1× | 1.3% | PASS |
| file_reads | `table_scan` | 1.36s | 1.31s | 1.0× | 1.7% | PASS |
| file_reads | `oltp_read_only` | 281.19ms | 210.71ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 267.08ms | 374.65ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 27.80ms | 42.45ms | 1.5× | 2.6% | PASS |
| file_writes | `oltp_update_index` | 109.60ms | 154.33ms | 1.4× | 2.2% | PASS |
| file_writes | `oltp_update_non_index` | 83.72ms | 104.57ms | 1.2× | 2.0% | PASS |
| file_writes | `oltp_delete_insert` | 82.54ms | 113.72ms | 1.4× | 2.0% | PASS |
| file_writes | `oltp_write_only` | 53.08ms | 70.39ms | 1.3× | 3.1% | PASS |
| file_writes | `types_delete_insert` | 51.15ms | 61.54ms | 1.2× | 2.4% | PASS |
| file_writes | `oltp_read_write` | 137.99ms | 178.61ms | 1.3× | 1.8% | PASS |
| ac_reads | `oltp_point_select` | 61.14ms | 62.84ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 23.93ms | 24.09ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 21.44ms | 23.50ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 4.04ms | 4.18ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 5.16ms | 5.38ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 7.69ms | 8.86ms | 1.2× | 1.0% | PASS |
| ac_reads | `select_random_points` | 33.72ms | 37.92ms | 1.1× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 10.73ms | 11.49ms | 1.1× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 7.27ms | 6.78ms | 0.9× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 37.41ms | 40.26ms | 1.1× | 0.6% | PASS |
| ac_reads | `index_join` | 9.71ms | 12.51ms | 1.3× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 4.42ms | 5.99ms | 1.4× | 3.0% | PASS |
| ac_reads | `types_table_scan` | 1.10s | 1.20s | 1.1× | 1.9% | PASS |
| ac_reads | `table_scan` | 1.30s | 1.29s | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_read_only` | 196.87ms | 205.31ms | 1.0× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.95ms | 65.22ms | 2.7× | 6.9% | PASS |
| ac_writes | `oltp_insert_ac` | 25.87ms | 81.47ms | 3.1× | 5.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.22ms | 90.23ms | 3.3× | 5.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.38ms | 72.88ms | 3.0× | 5.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.10ms | 88.79ms | 3.3× | 6.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.31ms | 83.33ms | 3.3× | 6.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.99ms | 78.91ms | 3.3× | 11.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.52ms | 93.66ms | 2.8× | 6.1% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 54s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 71.22ms | 130.00ms | 54.8% | 0.4% | PASS |
| `status_dirty_many_tables` | 73.57ms | 130.00ms | 56.6% | 0.2% | PASS |
| `diff_regular_working_one_table` | 66.92ms | 120.00ms | 55.8% | 0.2% | PASS |
| `diff_regular_working_many_tables` | 77.44ms | 140.00ms | 55.3% | 0.2% | PASS |
| `diff_stat_working_many_tables` | 77.55ms | 140.00ms | 55.4% | 0.2% | PASS |
| `diff_schema_working_many_tables` | 78.21ms | 140.00ms | 55.9% | 0.2% | PASS |
| `branch_list_many_branches` | 18.18ms | 35.00ms | 52.0% | 0.8% | PASS |
| `branch_create_delete` | 27.14ms | 40.00ms | 67.8% | 1.0% | PASS |
| `checkout_branch_clean` | 112.32ms | 150.00ms | 74.9% | 9.6% | PASS |
| `merge_data_no_conflicts` | 32.79ms | 50.00ms | 65.6% | 1.0% | PASS |
| `merge_schema_no_conflicts` | 17.64ms | 35.00ms | 50.4% | 1.2% | PASS |
| `merge_data_conflicts` | 25.68ms | 180.00ms | 14.3% | 0.6% | PASS |
| `merge_data_conflicts_with_resolve` | 25.75ms | 180.00ms | 14.3% | 0.4% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
