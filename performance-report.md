# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-16 21:21 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261111.196.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/43942602575)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.97s | 10.73s | 1.1× | 1.5% | **PASS** |
| Writes | 2.09s | 3.64s | 1.7× | 1.4% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.72s | 10.88s | 1.0× | 1.3% | **PASS** |
| Writes | 3.46s | 4.30s | 1.2× | 2.3% | **PASS** |
| Autocommit writes | 837.09ms | 2.57s | 3.1× | 7.8% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.06s | 2.16s | 1.0× | 1.5% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.55s | 2.79s | 1.1× | 1.6% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.84s | 2.92s | 1.0× | 1.4% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.53s | 2.86s | 1.1× | 1.4% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 336.36ms | 562.22ms | 1.7× | 1.3% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 580.53ms | 1.04s | 1.8× | 1.6% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 598.63ms | 1.06s | 1.8× | 1.3% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 573.07ms | 973.67ms | 1.7× | 1.1% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 2.22s | 2.19s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.79s | 2.86s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.95s | 2.91s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.76s | 2.92s | 1.1× | 1.3% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 949.68ms | 918.77ms | 1.0× | 13.0% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 922.22ms | 1.15s | 1.3× | 3.8% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 842.99ms | 1.17s | 1.4× | 2.0% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 747.63ms | 1.07s | 1.4× | 2.0% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.12s | 2.20s | 1.0× | 1.2% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.65s | 2.86s | 1.1× | 1.8% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.92s | 2.98s | 1.0× | 1.8% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.71s | 2.94s | 1.1× | 1.7% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 232.22ms | 744.75ms | 3.2× | 40.5% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 195.78ms | 585.87ms | 3.0× | 6.7% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 212.25ms | 623.78ms | 2.9× | 6.2% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 196.84ms | 613.56ms | 3.1× | 9.3% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 19.48ms | 22.43ms | 1.2× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 9.30ms | 9.00ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_sum_range` | 7.71ms | 8.62ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_order_range` | 2.18ms | 2.30ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 2.97ms | 3.16ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.04ms | 3.83ms | 1.3× | 2.1% | PASS |
| mem_reads | `select_random_points` | 8.42ms | 9.01ms | 1.1× | 2.0% | PASS |
| mem_reads | `select_random_ranges` | 2.65ms | 3.15ms | 1.2× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 3.29ms | 3.39ms | 1.0× | 2.9% | PASS |
| mem_reads | `groupby_scan` | 24.87ms | 27.37ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 4.58ms | 6.68ms | 1.5× | 2.6% | PASS |
| mem_reads | `index_join_scan` | 2.76ms | 3.91ms | 1.4× | 2.4% | PASS |
| mem_reads | `types_table_scan` | 911.11ms | 944.25ms | 1.0× | 1.6% | PASS |
| mem_reads | `table_scan` | 973.19ms | 1.02s | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_read_only` | 80.29ms | 89.60ms | 1.1× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 135.99ms | 204.75ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 11.69ms | 21.44ms | 1.8× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 39.65ms | 76.76ms | 1.9× | 2.1% | PASS |
| mem_writes | `oltp_update_non_index` | 27.71ms | 47.42ms | 1.7× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 34.28ms | 58.22ms | 1.7× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 16.97ms | 37.70ms | 2.2× | 1.6% | PASS |
| mem_writes | `types_delete_insert` | 19.40ms | 30.13ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 50.67ms | 85.79ms | 1.7× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 87.55ms | 38.48ms | 0.4× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 16.69ms | 10.73ms | 0.6× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 15.42ms | 10.53ms | 0.7× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.04ms | 2.54ms | 0.8× | 0.8% | PASS |
| file_reads | `oltp_distinct_range` | 3.78ms | 3.37ms | 0.9× | 0.8% | PASS |
| file_reads | `oltp_index_scan` | 10.35ms | 6.04ms | 0.6× | 1.2% | PASS |
| file_reads | `select_random_points` | 16.35ms | 11.02ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_ranges` | 9.79ms | 4.85ms | 0.5× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 10.66ms | 5.36ms | 0.5× | 1.1% | PASS |
| file_reads | `groupby_scan` | 25.61ms | 27.51ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 8.61ms | 7.75ms | 0.9× | 1.4% | PASS |
| file_reads | `index_join_scan` | 3.58ms | 4.06ms | 1.1× | 1.4% | PASS |
| file_reads | `types_table_scan` | 852.72ms | 923.52ms | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 972.08ms | 1.02s | 1.0× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 186.74ms | 116.42ms | 0.6× | 1.7% | PASS |
| file_writes | `oltp_bulk_insert` | 214.94ms | 264.47ms | 1.2× | 14.3% | PASS |
| file_writes | `oltp_insert` | 26.81ms | 37.63ms | 1.4× | 29.4% | PASS |
| file_writes | `oltp_update_index` | 141.65ms | 134.58ms | 1.0× | 8.8% | PASS |
| file_writes | `oltp_update_non_index` | 115.29ms | 96.26ms | 0.8× | 2.1% | PASS |
| file_writes | `oltp_delete_insert` | 126.07ms | 110.24ms | 0.9× | 17.7% | PASS |
| file_writes | `oltp_write_only` | 106.07ms | 79.51ms | 0.7× | 28.4% | PASS |
| file_writes | `types_delete_insert` | 80.04ms | 60.00ms | 0.7× | 11.8% | PASS |
| file_writes | `oltp_read_write` | 138.81ms | 136.08ms | 1.0× | 8.0% | PASS |
| ac_reads | `oltp_point_select` | 41.65ms | 39.94ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 12.30ms | 11.03ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 10.94ms | 10.79ms | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_order_range` | 2.61ms | 2.56ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 3.31ms | 3.36ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_index_scan` | 5.59ms | 5.95ms | 1.1× | 1.0% | PASS |
| ac_reads | `select_random_points` | 11.03ms | 10.74ms | 1.0× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 4.98ms | 4.84ms | 1.0× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 5.85ms | 5.23ms | 0.9× | 1.0% | PASS |
| ac_reads | `groupby_scan` | 25.00ms | 27.38ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 6.21ms | 7.65ms | 1.2× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.17ms | 4.20ms | 1.3× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 850.65ms | 924.54ms | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.02s | 1.03s | 1.0× | 2.4% | PASS |
| ac_reads | `oltp_read_only` | 112.95ms | 113.85ms | 1.0× | 1.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 25.17ms | 86.74ms | 3.4× | 48.9% | PASS |
| ac_writes | `oltp_insert_ac` | 26.23ms | 99.13ms | 3.8× | 37.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.59ms | 109.11ms | 3.8× | 43.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.51ms | 79.17ms | 3.4× | 34.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.76ms | 79.30ms | 3.1× | 25.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 34.03ms | 92.60ms | 2.7× | 45.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 27.74ms | 98.03ms | 3.5× | 37.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 41.19ms | 100.67ms | 2.4× | 45.5% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.04ms | 38.46ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_range_select` | 16.73ms | 14.06ms | 0.8× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 14.99ms | 13.65ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 3.30ms | 3.50ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 4.29ms | 4.61ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.94ms | 6.04ms | 1.5× | 1.7% | PASS |
| mem_reads | `select_random_points` | 21.43ms | 20.70ms | 1.0× | 2.5% | PASS |
| mem_reads | `select_random_ranges` | 3.42ms | 5.28ms | 1.5× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.16ms | 4.50ms | 1.1× | 2.2% | PASS |
| mem_reads | `groupby_scan` | 34.45ms | 35.54ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 10.43ms | 8.94ms | 0.9× | 1.6% | PASS |
| mem_reads | `index_join_scan` | 3.81ms | 5.44ms | 1.4× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.19s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.29s | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_read_only` | 136.82ms | 144.46ms | 1.1× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 233.27ms | 376.34ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 17.55ms | 39.84ms | 2.3× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 64.83ms | 148.65ms | 2.3× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 47.34ms | 91.51ms | 1.9× | 2.2% | PASS |
| mem_writes | `oltp_delete_insert` | 53.05ms | 109.46ms | 2.1× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 27.88ms | 65.88ms | 2.4× | 2.0% | FAIL |
| mem_writes | `types_delete_insert` | 38.05ms | 58.38ms | 1.5× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 98.56ms | 150.21ms | 1.5× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 104.94ms | 56.50ms | 0.5× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 24.18ms | 16.28ms | 0.7× | 2.9% | PASS |
| file_reads | `oltp_sum_range` | 22.61ms | 15.87ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 4.19ms | 3.78ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 5.24ms | 4.90ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 11.06ms | 8.37ms | 0.8× | 1.1% | PASS |
| file_reads | `select_random_points` | 29.98ms | 23.50ms | 0.8× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 10.50ms | 7.15ms | 0.7× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 11.17ms | 6.68ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 35.65ms | 36.08ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 14.70ms | 11.10ms | 0.8× | 1.8% | PASS |
| file_reads | `index_join_scan` | 4.82ms | 5.91ms | 1.2× | 2.2% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.20s | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 1.21s | 1.30s | 1.1× | 0.7% | PASS |
| file_reads | `oltp_read_only` | 241.78ms | 171.50ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 258.85ms | 391.69ms | 1.5× | 0.8% | PASS |
| file_writes | `oltp_insert` | 25.09ms | 47.67ms | 1.9× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 132.79ms | 168.96ms | 1.3× | 10.2% | PASS |
| file_writes | `oltp_update_non_index` | 107.01ms | 108.00ms | 1.0× | 9.0% | PASS |
| file_writes | `oltp_delete_insert` | 96.01ms | 126.07ms | 1.3× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 81.40ms | 79.46ms | 1.0× | 13.2% | PASS |
| file_writes | `types_delete_insert` | 72.31ms | 67.58ms | 0.9× | 2.0% | PASS |
| file_writes | `oltp_read_write` | 148.77ms | 164.50ms | 1.1× | 5.7% | PASS |
| ac_reads | `oltp_point_select` | 58.59ms | 56.39ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 19.91ms | 16.09ms | 0.8× | 2.2% | PASS |
| ac_reads | `oltp_sum_range` | 17.94ms | 15.95ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_order_range` | 3.76ms | 3.78ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.79ms | 4.90ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.53ms | 8.28ms | 1.3× | 1.9% | PASS |
| ac_reads | `select_random_points` | 25.16ms | 23.53ms | 0.9× | 2.6% | PASS |
| ac_reads | `select_random_ranges` | 5.87ms | 7.14ms | 1.2× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 6.66ms | 6.72ms | 1.0× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 35.13ms | 36.13ms | 1.0× | 1.3% | PASS |
| ac_reads | `index_join` | 12.60ms | 11.10ms | 0.9× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 4.34ms | 5.89ms | 1.4× | 1.8% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.20s | 1.1× | 1.0% | PASS |
| ac_reads | `table_scan` | 1.21s | 1.30s | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 172.33ms | 171.23ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.97ms | 58.89ms | 2.7× | 6.7% | PASS |
| ac_writes | `oltp_insert_ac` | 24.16ms | 69.92ms | 2.9× | 6.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.13ms | 83.46ms | 3.2× | 7.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.62ms | 67.72ms | 3.3× | 5.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.92ms | 76.64ms | 3.1× | 5.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.08ms | 76.65ms | 3.2× | 7.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.70ms | 68.91ms | 3.0× | 8.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.20ms | 83.68ms | 2.7× | 6.7% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.25ms | 39.13ms | 1.1× | 2.6% | PASS |
| mem_reads | `oltp_range_select` | 16.55ms | 14.21ms | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_sum_range` | 14.76ms | 13.68ms | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 3.46ms | 3.25ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.39ms | 4.34ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 4.01ms | 6.30ms | 1.6× | 1.5% | PASS |
| mem_reads | `select_random_points` | 21.09ms | 20.96ms | 1.0× | 1.6% | PASS |
| mem_reads | `select_random_ranges` | 3.45ms | 5.41ms | 1.6× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 4.18ms | 4.71ms | 1.1× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 34.35ms | 34.89ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 10.15ms | 9.25ms | 0.9× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 3.80ms | 5.51ms | 1.5× | 1.7% | PASS |
| mem_reads | `types_table_scan` | 1.19s | 1.26s | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.34s | 1.35s | 1.0× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 149.91ms | 143.44ms | 1.0× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 238.80ms | 382.32ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.34ms | 41.22ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 67.33ms | 152.02ms | 2.3× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 48.90ms | 92.21ms | 1.9× | 2.1% | PASS |
| mem_writes | `oltp_delete_insert` | 52.78ms | 109.83ms | 2.1× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 27.97ms | 66.45ms | 2.4× | 1.4% | FAIL |
| mem_writes | `types_delete_insert` | 38.85ms | 58.98ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_read_write` | 105.66ms | 157.27ms | 1.5× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 109.66ms | 57.76ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 24.95ms | 16.15ms | 0.6× | 2.4% | PASS |
| file_reads | `oltp_sum_range` | 23.49ms | 15.77ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_order_range` | 4.44ms | 3.55ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 5.45ms | 4.70ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 11.44ms | 8.32ms | 0.7× | 1.2% | PASS |
| file_reads | `select_random_points` | 30.99ms | 23.92ms | 0.8× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 10.60ms | 7.25ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 11.61ms | 6.74ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 35.91ms | 35.48ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 14.81ms | 11.07ms | 0.7× | 1.7% | PASS |
| file_reads | `index_join_scan` | 4.83ms | 5.85ms | 1.2× | 2.6% | PASS |
| file_reads | `types_table_scan` | 1.11s | 1.22s | 1.1× | 2.6% | PASS |
| file_reads | `table_scan` | 1.29s | 1.33s | 1.0× | 1.4% | PASS |
| file_reads | `oltp_read_only` | 257.95ms | 169.73ms | 0.7× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 258.84ms | 396.06ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 25.91ms | 48.61ms | 1.9× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 103.51ms | 171.66ms | 1.7× | 2.7% | PASS |
| file_writes | `oltp_update_non_index` | 92.86ms | 106.98ms | 1.2× | 9.5% | PASS |
| file_writes | `oltp_delete_insert` | 93.65ms | 127.05ms | 1.4× | 1.9% | PASS |
| file_writes | `oltp_write_only` | 61.74ms | 80.19ms | 1.3× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 65.83ms | 67.11ms | 1.0× | 1.4% | PASS |
| file_writes | `oltp_read_write` | 140.65ms | 167.91ms | 1.2× | 2.5% | PASS |
| ac_reads | `oltp_point_select` | 61.03ms | 58.04ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_range_select` | 20.62ms | 16.28ms | 0.8× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 18.38ms | 15.90ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_order_range` | 3.98ms | 3.54ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 5.04ms | 4.74ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_index_scan` | 6.83ms | 8.43ms | 1.2× | 1.4% | PASS |
| ac_reads | `select_random_points` | 26.53ms | 24.70ms | 0.9× | 3.1% | PASS |
| ac_reads | `select_random_ranges` | 6.00ms | 7.31ms | 1.2× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 7.03ms | 6.78ms | 1.0× | 1.8% | PASS |
| ac_reads | `groupby_scan` | 35.85ms | 35.87ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 13.12ms | 11.52ms | 0.9× | 2.6% | PASS |
| ac_reads | `index_join_scan` | 4.41ms | 6.07ms | 1.4× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.24s | 1.1× | 2.1% | PASS |
| ac_reads | `table_scan` | 1.35s | 1.36s | 1.0× | 0.6% | PASS |
| ac_reads | `oltp_read_only` | 191.83ms | 174.27ms | 0.9× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.54ms | 62.80ms | 2.7× | 6.5% | PASS |
| ac_writes | `oltp_insert_ac` | 26.50ms | 79.29ms | 3.0× | 6.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.29ms | 88.26ms | 3.2× | 5.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.02ms | 71.30ms | 3.1× | 9.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.59ms | 80.52ms | 2.9× | 4.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.28ms | 80.52ms | 3.1× | 6.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.02ms | 73.29ms | 3.1× | 5.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.00ms | 87.80ms | 2.6× | 6.0% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 31.76ms | 39.99ms | 1.3× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 18.81ms | 21.09ms | 1.1× | 2.2% | PASS |
| mem_reads | `oltp_sum_range` | 17.40ms | 20.42ms | 1.2× | 1.2% | PASS |
| mem_reads | `oltp_order_range` | 3.54ms | 3.94ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 4.58ms | 5.12ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 4.35ms | 5.92ms | 1.4× | 2.4% | PASS |
| mem_reads | `select_random_points` | 25.33ms | 30.28ms | 1.2× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 7.43ms | 8.90ms | 1.2× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.15ms | 4.23ms | 1.0× | 1.8% | PASS |
| mem_reads | `groupby_scan` | 37.37ms | 40.83ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 7.93ms | 10.65ms | 1.3× | 2.5% | PASS |
| mem_reads | `index_join_scan` | 3.98ms | 5.41ms | 1.4× | 2.4% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.20s | 1.2× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.17s | 1.29s | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 149.19ms | 172.21ms | 1.2× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 239.05ms | 350.37ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 18.32ms | 35.47ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 63.67ms | 127.01ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 48.78ms | 86.05ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 47.72ms | 97.59ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 25.68ms | 60.19ms | 2.3× | 1.4% | FAIL |
| mem_writes | `types_delete_insert` | 31.30ms | 54.24ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 98.53ms | 162.76ms | 1.7× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 100.67ms | 57.91ms | 0.6× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 26.52ms | 23.04ms | 0.9× | 2.2% | PASS |
| file_reads | `oltp_sum_range` | 24.65ms | 22.32ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 4.40ms | 4.20ms | 1.0× | 2.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.48ms | 5.41ms | 1.0× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 11.26ms | 7.91ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_points` | 34.03ms | 33.49ms | 1.0× | 1.9% | PASS |
| file_reads | `select_random_ranges` | 14.80ms | 10.81ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 11.06ms | 6.27ms | 0.6× | 1.4% | PASS |
| file_reads | `groupby_scan` | 37.68ms | 40.97ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 11.91ms | 11.78ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join_scan` | 4.85ms | 5.74ms | 1.2× | 1.9% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.20s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.18s | 1.29s | 1.1× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 251.16ms | 198.67ms | 0.8× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 252.12ms | 361.42ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 25.27ms | 41.89ms | 1.7× | 2.1% | PASS |
| file_writes | `oltp_update_index` | 93.75ms | 143.15ms | 1.5× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 75.62ms | 99.72ms | 1.3× | 2.7% | PASS |
| file_writes | `oltp_delete_insert` | 75.43ms | 111.03ms | 1.5× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 50.08ms | 71.52ms | 1.4× | 2.4% | PASS |
| file_writes | `types_delete_insert` | 48.51ms | 61.40ms | 1.3× | 1.8% | PASS |
| file_writes | `oltp_read_write` | 126.83ms | 175.32ms | 1.4× | 1.8% | PASS |
| ac_reads | `oltp_point_select` | 55.24ms | 58.00ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 22.61ms | 23.07ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_sum_range` | 20.80ms | 22.52ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 4.13ms | 4.30ms | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_distinct_range` | 5.12ms | 5.47ms | 1.1× | 2.3% | PASS |
| ac_reads | `oltp_index_scan` | 7.07ms | 8.01ms | 1.1× | 1.7% | PASS |
| ac_reads | `select_random_points` | 30.20ms | 34.10ms | 1.1× | 2.7% | PASS |
| ac_reads | `select_random_ranges` | 10.18ms | 10.84ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 6.83ms | 6.35ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 37.76ms | 41.02ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 9.74ms | 12.14ms | 1.2× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 4.57ms | 5.88ms | 1.3× | 1.8% | PASS |
| ac_reads | `types_table_scan` | 1.07s | 1.21s | 1.1× | 1.7% | PASS |
| ac_reads | `table_scan` | 1.23s | 1.30s | 1.1× | 1.9% | PASS |
| ac_reads | `oltp_read_only` | 186.23ms | 199.29ms | 1.1× | 1.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.63ms | 62.09ms | 2.9× | 8.7% | PASS |
| ac_writes | `oltp_insert_ac` | 24.28ms | 79.24ms | 3.3× | 10.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.70ms | 91.52ms | 3.4× | 11.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.47ms | 70.73ms | 3.1× | 7.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.49ms | 78.57ms | 3.3× | 7.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.89ms | 78.78ms | 3.2× | 10.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.57ms | 65.93ms | 3.1× | 11.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.82ms | 86.71ms | 2.7× | 7.8% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 25s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 56.29ms | 130.00ms | 43.3% | 0.8% | PASS |
| `status_dirty_many_tables` | 58.68ms | 130.00ms | 45.1% | 0.7% | PASS |
| `diff_regular_working_one_table` | 52.96ms | 120.00ms | 44.1% | 0.8% | PASS |
| `diff_regular_working_many_tables` | 61.46ms | 140.00ms | 43.9% | 0.6% | PASS |
| `diff_stat_working_many_tables` | 61.82ms | 140.00ms | 44.2% | 0.7% | PASS |
| `diff_schema_working_many_tables` | 62.43ms | 140.00ms | 44.6% | 0.8% | PASS |
| `branch_list_many_branches` | 13.47ms | 35.00ms | 38.5% | 1.6% | PASS |
| `branch_create_delete` | 14.89ms | 40.00ms | 37.2% | 1.2% | PASS |
| `checkout_branch_clean` | 37.62ms | 150.00ms | 25.1% | 1.1% | PASS |
| `merge_data_no_conflicts` | 17.40ms | 50.00ms | 34.8% | 1.3% | PASS |
| `merge_schema_no_conflicts` | 13.19ms | 35.00ms | 37.7% | 1.2% | PASS |
| `merge_data_conflicts` | 19.14ms | 180.00ms | 10.6% | 1.6% | PASS |
| `merge_data_conflicts_with_resolve` | 19.05ms | 180.00ms | 10.6% | 1.3% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
