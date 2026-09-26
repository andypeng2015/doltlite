# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-26 15:29 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260920.314.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/36246409875)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.82s | 11.09s | 1.0× | 1.3% | **PASS** |
| Writes | 2.14s | 3.53s | 1.6× | 1.4% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.66s | 11.34s | 1.0× | 1.4% | **PASS** |
| Writes | 3.57s | 4.37s | 1.2× | 2.2% | **PASS** |
| Autocommit writes | 850.50ms | 2.64s | 3.1× | 8.2% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.33s | 2.53s | 1.1× | 1.0% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.98s | 2.85s | 1.0× | 2.0% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.61s | 2.77s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.90s | 2.93s | 1.0× | 1.2% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 388.24ms | 644.34ms | 1.7× | 1.3% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 528.84ms | 875.77ms | 1.7× | 2.0% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 591.89ms | 993.69ms | 1.7× | 1.4% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 634.39ms | 1.01s | 1.6× | 1.2% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.46s | 2.58s | 1.0× | 1.3% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.07s | 2.87s | 0.9× | 1.8% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.03s | 2.90s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.09s | 2.99s | 1.0× | 1.5% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 481.56ms | 712.61ms | 1.5× | 2.1% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.43s | 1.45s | 1.0× | 4.3% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 832.67ms | 1.10s | 1.3× | 1.8% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 825.49ms | 1.11s | 1.3× | 1.9% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.39s | 2.57s | 1.1× | 1.5% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.96s | 2.85s | 1.0× | 2.0% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.79s | 2.88s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 3.04s | 3.03s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 138.76ms | 488.88ms | 3.5× | 7.3% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 264.70ms | 799.07ms | 3.0× | 17.5% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 221.58ms | 661.18ms | 3.0× | 8.1% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 225.47ms | 690.23ms | 3.1× | 7.8% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 21.41ms | 25.64ms | 1.2× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 9.20ms | 10.43ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_sum_range` | 8.63ms | 10.43ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 2.39ms | 2.61ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_distinct_range` | 3.11ms | 3.69ms | 1.2× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.40ms | 4.71ms | 1.4× | 1.8% | PASS |
| mem_reads | `select_random_points` | 9.34ms | 10.43ms | 1.1× | 1.3% | PASS |
| mem_reads | `select_random_ranges` | 2.65ms | 3.46ms | 1.3× | 0.9% | PASS |
| mem_reads | `covering_index_scan` | 3.63ms | 3.94ms | 1.1× | 1.0% | PASS |
| mem_reads | `groupby_scan` | 26.73ms | 31.54ms | 1.2× | 0.6% | PASS |
| mem_reads | `index_join` | 4.89ms | 7.74ms | 1.6× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 2.66ms | 4.15ms | 1.6× | 0.9% | PASS |
| mem_reads | `types_table_scan` | 978.26ms | 1.09s | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.16s | 1.22s | 1.0× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 93.65ms | 109.53ms | 1.2× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 157.38ms | 237.02ms | 1.5× | 1.4% | PASS |
| mem_writes | `oltp_insert` | 13.30ms | 24.32ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_update_index` | 45.62ms | 89.99ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 30.91ms | 51.50ms | 1.7× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 39.79ms | 65.96ms | 1.7× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 18.97ms | 40.61ms | 2.1× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 21.51ms | 33.68ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 60.77ms | 101.26ms | 1.7× | 1.3% | PASS |
| file_reads | `oltp_point_select` | 53.71ms | 34.88ms | 0.6× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 13.12ms | 11.92ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 12.75ms | 11.98ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_order_range` | 2.90ms | 2.81ms | 1.0× | 2.8% | PASS |
| file_reads | `oltp_distinct_range` | 3.63ms | 3.90ms | 1.1× | 3.2% | PASS |
| file_reads | `oltp_index_scan` | 7.10ms | 6.12ms | 0.9× | 1.2% | PASS |
| file_reads | `select_random_points` | 13.58ms | 11.87ms | 0.9× | 1.6% | PASS |
| file_reads | `select_random_ranges` | 5.97ms | 4.49ms | 0.8× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 7.23ms | 5.18ms | 0.7× | 1.2% | PASS |
| file_reads | `groupby_scan` | 27.35ms | 31.68ms | 1.2× | 0.7% | PASS |
| file_reads | `index_join` | 6.99ms | 8.80ms | 1.3× | 1.3% | PASS |
| file_reads | `index_join_scan` | 3.13ms | 4.41ms | 1.4× | 1.5% | PASS |
| file_reads | `types_table_scan` | 997.36ms | 1.10s | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 1.17s | 1.22s | 1.0× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 141.35ms | 125.20ms | 0.9× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 167.13ms | 246.34ms | 1.5× | 2.4% | PASS |
| file_writes | `oltp_insert` | 17.96ms | 27.53ms | 1.5× | 3.5% | PASS |
| file_writes | `oltp_update_index` | 62.99ms | 103.91ms | 1.6× | 2.3% | PASS |
| file_writes | `oltp_update_non_index` | 43.94ms | 60.02ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_delete_insert` | 51.71ms | 76.59ms | 1.5× | 1.3% | PASS |
| file_writes | `oltp_write_only` | 31.76ms | 49.05ms | 1.5× | 2.6% | PASS |
| file_writes | `types_delete_insert` | 30.87ms | 38.81ms | 1.3× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 75.21ms | 110.38ms | 1.5× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 31.91ms | 35.26ms | 1.1× | 1.9% | PASS |
| ac_reads | `oltp_range_select` | 10.46ms | 11.72ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 9.92ms | 11.79ms | 1.2× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 2.58ms | 2.78ms | 1.1× | 2.3% | PASS |
| ac_reads | `oltp_distinct_range` | 3.29ms | 3.88ms | 1.2× | 1.8% | PASS |
| ac_reads | `oltp_index_scan` | 4.70ms | 5.96ms | 1.3× | 1.8% | PASS |
| ac_reads | `select_random_points` | 11.05ms | 11.77ms | 1.1× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 3.71ms | 4.46ms | 1.2× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 4.83ms | 5.04ms | 1.0× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 27.03ms | 31.70ms | 1.2× | 0.7% | PASS |
| ac_reads | `index_join` | 5.79ms | 8.74ms | 1.5× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 2.92ms | 4.43ms | 1.5× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 991.72ms | 1.09s | 1.1× | 0.4% | PASS |
| ac_reads | `table_scan` | 1.17s | 1.22s | 1.0× | 0.3% | PASS |
| ac_reads | `oltp_read_only` | 110.52ms | 125.73ms | 1.1× | 0.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 15.87ms | 47.41ms | 3.0× | 7.6% | PASS |
| ac_writes | `oltp_insert_ac` | 16.91ms | 59.51ms | 3.5× | 6.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 19.07ms | 70.57ms | 3.7× | 6.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.66ms | 57.30ms | 3.4× | 7.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 17.81ms | 67.48ms | 3.8× | 7.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 17.15ms | 64.31ms | 3.7× | 9.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 15.08ms | 55.00ms | 3.6× | 7.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 20.20ms | 67.31ms | 3.3× | 5.5% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.21ms | 33.01ms | 0.9× | 2.5% | PASS |
| mem_reads | `oltp_range_select` | 15.80ms | 12.94ms | 0.8× | 1.9% | PASS |
| mem_reads | `oltp_sum_range` | 15.51ms | 12.48ms | 0.8× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 3.12ms | 2.99ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.02ms | 4.01ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.78ms | 5.57ms | 1.5× | 2.7% | PASS |
| mem_reads | `select_random_points` | 22.82ms | 20.38ms | 0.9× | 2.6% | PASS |
| mem_reads | `select_random_ranges` | 3.21ms | 4.72ms | 1.5× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 3.57ms | 4.28ms | 1.2× | 3.2% | PASS |
| mem_reads | `groupby_scan` | 34.00ms | 33.32ms | 1.0× | 0.5% | PASS |
| mem_reads | `index_join` | 11.21ms | 9.51ms | 0.8× | 3.6% | PASS |
| mem_reads | `index_join_scan` | 3.36ms | 5.55ms | 1.6× | 3.1% | PASS |
| mem_reads | `types_table_scan` | 1.22s | 1.21s | 1.0× | 2.0% | PASS |
| mem_reads | `table_scan` | 1.47s | 1.36s | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 134.27ms | 126.29ms | 0.9× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 199.91ms | 300.32ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 15.91ms | 33.57ms | 2.1× | 1.6% | PASS |
| mem_writes | `oltp_update_index` | 65.93ms | 143.40ms | 2.2× | 2.4% | PASS |
| mem_writes | `oltp_update_non_index` | 45.80ms | 77.61ms | 1.7× | 3.0% | PASS |
| mem_writes | `oltp_delete_insert` | 49.25ms | 92.51ms | 1.9× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 25.25ms | 53.85ms | 2.1× | 1.6% | PASS |
| mem_writes | `types_delete_insert` | 36.73ms | 46.88ms | 1.3× | 2.0% | PASS |
| mem_writes | `oltp_read_write` | 90.06ms | 127.65ms | 1.4× | 3.5% | PASS |
| file_reads | `oltp_point_select` | 69.80ms | 41.30ms | 0.6× | 2.0% | PASS |
| file_reads | `oltp_range_select` | 18.80ms | 13.70ms | 0.7× | 2.3% | PASS |
| file_reads | `oltp_sum_range` | 18.56ms | 13.32ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_order_range` | 3.53ms | 3.11ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 4.47ms | 4.14ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 7.43ms | 6.62ms | 0.9× | 1.5% | PASS |
| file_reads | `select_random_points` | 27.07ms | 21.45ms | 0.8× | 3.1% | PASS |
| file_reads | `select_random_ranges` | 6.72ms | 5.72ms | 0.9× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 7.20ms | 5.25ms | 0.7× | 1.2% | PASS |
| file_reads | `groupby_scan` | 34.34ms | 33.13ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 13.38ms | 10.35ms | 0.8× | 2.6% | PASS |
| file_reads | `index_join_scan` | 3.86ms | 5.87ms | 1.5× | 2.3% | PASS |
| file_reads | `types_table_scan` | 1.22s | 1.22s | 1.0× | 1.8% | PASS |
| file_reads | `table_scan` | 1.45s | 1.35s | 0.9× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 188.18ms | 141.45ms | 0.8× | 2.7% | PASS |
| file_writes | `oltp_bulk_insert` | 295.15ms | 387.62ms | 1.3× | 3.6% | PASS |
| file_writes | `oltp_insert` | 33.97ms | 66.30ms | 2.0× | 3.3% | PASS |
| file_writes | `oltp_update_index` | 201.17ms | 243.03ms | 1.2× | 4.9% | PASS |
| file_writes | `oltp_update_non_index` | 175.78ms | 152.79ms | 0.9× | 8.1% | PASS |
| file_writes | `oltp_delete_insert` | 207.34ms | 178.70ms | 0.9× | 3.3% | PASS |
| file_writes | `oltp_write_only` | 139.45ms | 120.95ms | 0.9× | 4.2% | PASS |
| file_writes | `types_delete_insert` | 165.30ms | 101.22ms | 0.6× | 4.6% | PASS |
| file_writes | `oltp_read_write` | 211.68ms | 200.99ms | 0.9× | 4.4% | PASS |
| ac_reads | `oltp_point_select` | 47.93ms | 42.27ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 16.69ms | 13.87ms | 0.8× | 2.4% | PASS |
| ac_reads | `oltp_sum_range` | 16.58ms | 13.56ms | 0.8× | 2.6% | PASS |
| ac_reads | `oltp_order_range` | 3.33ms | 3.14ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_distinct_range` | 4.24ms | 4.14ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 5.23ms | 6.73ms | 1.3× | 1.7% | PASS |
| ac_reads | `select_random_points` | 26.69ms | 22.58ms | 0.8× | 2.8% | PASS |
| ac_reads | `select_random_ranges` | 4.46ms | 5.76ms | 1.3× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 4.85ms | 5.22ms | 1.1× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 34.22ms | 33.46ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 12.77ms | 10.47ms | 0.8× | 3.3% | PASS |
| ac_reads | `index_join_scan` | 3.71ms | 5.98ms | 1.6× | 3.3% | PASS |
| ac_reads | `types_table_scan` | 1.21s | 1.21s | 1.0× | 1.6% | PASS |
| ac_reads | `table_scan` | 1.41s | 1.33s | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_read_only` | 151.48ms | 139.94ms | 0.9× | 3.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 28.38ms | 82.01ms | 2.9× | 14.8% | PASS |
| ac_writes | `oltp_insert_ac` | 35.59ms | 95.93ms | 2.7× | 21.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 31.45ms | 106.64ms | 3.4× | 20.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 28.80ms | 89.65ms | 3.1× | 22.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 30.37ms | 97.97ms | 3.2× | 12.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 33.31ms | 98.26ms | 2.9× | 17.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 33.92ms | 108.86ms | 3.2× | 17.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 42.87ms | 119.75ms | 2.8× | 15.7% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.86ms | 37.92ms | 1.1× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 16.05ms | 14.01ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 14.89ms | 13.56ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 3.19ms | 3.11ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.30ms | 4.23ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.02ms | 6.29ms | 1.6× | 1.4% | PASS |
| mem_reads | `select_random_points` | 21.89ms | 21.16ms | 1.0× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 3.49ms | 5.31ms | 1.5× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.23ms | 4.75ms | 1.1× | 2.2% | PASS |
| mem_reads | `groupby_scan` | 34.62ms | 34.56ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 10.02ms | 9.08ms | 0.9× | 2.2% | PASS |
| mem_reads | `index_join_scan` | 3.68ms | 5.50ms | 1.5× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 1.12s | 1.19s | 1.1× | 1.9% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.29s | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 135.58ms | 135.80ms | 1.0× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 246.51ms | 369.51ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 18.72ms | 38.55ms | 2.1× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 64.06ms | 138.41ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 47.62ms | 84.15ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 52.48ms | 102.70ms | 2.0× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 27.48ms | 61.12ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 37.60ms | 53.27ms | 1.4× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 97.42ms | 145.96ms | 1.5× | 2.2% | PASS |
| file_reads | `oltp_point_select` | 110.30ms | 59.15ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 24.18ms | 16.75ms | 0.7× | 2.2% | PASS |
| file_reads | `oltp_sum_range` | 22.78ms | 16.11ms | 0.7× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 4.15ms | 3.45ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 5.22ms | 4.58ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 11.63ms | 8.79ms | 0.8× | 1.4% | PASS |
| file_reads | `select_random_points` | 31.77ms | 24.98ms | 0.8× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 11.24ms | 7.46ms | 0.7× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.98ms | 6.98ms | 0.6× | 1.3% | PASS |
| file_reads | `groupby_scan` | 36.26ms | 35.16ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 15.46ms | 11.33ms | 0.7× | 2.0% | PASS |
| file_reads | `index_join_scan` | 4.94ms | 5.96ms | 1.2× | 2.9% | PASS |
| file_reads | `types_table_scan` | 1.17s | 1.21s | 1.0× | 1.0% | PASS |
| file_reads | `table_scan` | 1.30s | 1.31s | 1.0× | 0.7% | PASS |
| file_reads | `oltp_read_only` | 265.77ms | 174.17ms | 0.7× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 268.47ms | 385.32ms | 1.4× | 1.2% | PASS |
| file_writes | `oltp_insert` | 26.38ms | 46.41ms | 1.8× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 101.92ms | 158.59ms | 1.6× | 2.0% | PASS |
| file_writes | `oltp_update_non_index` | 94.57ms | 98.11ms | 1.0× | 7.6% | PASS |
| file_writes | `oltp_delete_insert` | 86.75ms | 116.86ms | 1.3× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 57.49ms | 71.86ms | 1.2× | 1.8% | PASS |
| file_writes | `types_delete_insert` | 65.00ms | 62.50ms | 1.0× | 2.2% | PASS |
| file_writes | `oltp_read_write` | 132.10ms | 157.65ms | 1.2× | 1.7% | PASS |
| ac_reads | `oltp_point_select` | 60.67ms | 59.38ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 19.34ms | 16.88ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 18.38ms | 16.18ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_order_range` | 3.81ms | 3.44ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 4.87ms | 4.58ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.87ms | 8.74ms | 1.3× | 1.6% | PASS |
| ac_reads | `select_random_points` | 25.58ms | 24.87ms | 1.0× | 1.8% | PASS |
| ac_reads | `select_random_ranges` | 6.10ms | 7.50ms | 1.2× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 7.01ms | 6.99ms | 1.0× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 35.19ms | 34.95ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 12.43ms | 11.14ms | 0.9× | 1.9% | PASS |
| ac_reads | `index_join_scan` | 4.32ms | 5.91ms | 1.4× | 2.0% | PASS |
| ac_reads | `types_table_scan` | 1.18s | 1.22s | 1.0× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.23s | 1.30s | 1.1× | 2.1% | PASS |
| ac_reads | `oltp_read_only` | 179.96ms | 170.65ms | 0.9× | 1.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 25.22ms | 66.96ms | 2.7× | 8.6% | PASS |
| ac_writes | `oltp_insert_ac` | 27.90ms | 83.77ms | 3.0× | 7.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.15ms | 92.20ms | 3.2× | 7.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.22ms | 76.39ms | 3.2× | 10.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.68ms | 86.07ms | 3.1× | 9.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.01ms | 84.86ms | 3.0× | 5.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.67ms | 81.33ms | 3.2× | 6.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.72ms | 89.60ms | 2.7× | 8.6% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.55ms | 42.50ms | 1.2× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 21.88ms | 21.97ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_sum_range` | 19.25ms | 21.07ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_order_range` | 3.65ms | 3.92ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.80ms | 5.09ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.94ms | 6.58ms | 1.3× | 2.2% | PASS |
| mem_reads | `select_random_points` | 30.33ms | 33.47ms | 1.1× | 1.3% | PASS |
| mem_reads | `select_random_ranges` | 8.15ms | 9.10ms | 1.1× | 0.8% | PASS |
| mem_reads | `covering_index_scan` | 4.28ms | 4.60ms | 1.1× | 2.4% | PASS |
| mem_reads | `groupby_scan` | 37.02ms | 39.80ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 8.28ms | 11.17ms | 1.3× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 4.34ms | 5.82ms | 1.3× | 2.6% | PASS |
| mem_reads | `types_table_scan` | 1.13s | 1.21s | 1.1× | 2.3% | PASS |
| mem_reads | `table_scan` | 1.42s | 1.34s | 0.9× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 165.91ms | 179.84ms | 1.1× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 253.34ms | 361.56ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 19.92ms | 36.13ms | 1.8× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 74.74ms | 139.87ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 56.22ms | 89.21ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 54.70ms | 102.29ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 28.21ms | 59.61ms | 2.1× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 34.42ms | 54.40ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 112.84ms | 169.54ms | 1.5× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 113.51ms | 62.96ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 29.82ms | 23.87ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_sum_range` | 27.95ms | 23.72ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 4.78ms | 4.33ms | 0.9× | 2.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.92ms | 5.49ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 13.06ms | 8.77ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 41.17ms | 38.33ms | 0.9× | 1.2% | PASS |
| file_reads | `select_random_ranges` | 16.39ms | 11.49ms | 0.7× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 12.77ms | 6.93ms | 0.5× | 1.3% | PASS |
| file_reads | `groupby_scan` | 38.63ms | 40.47ms | 1.0× | 0.6% | PASS |
| file_reads | `index_join` | 13.32ms | 12.98ms | 1.0× | 2.1% | PASS |
| file_reads | `index_join_scan` | 5.50ms | 6.17ms | 1.1× | 2.1% | PASS |
| file_reads | `types_table_scan` | 1.08s | 1.20s | 1.1× | 2.0% | PASS |
| file_reads | `table_scan` | 1.41s | 1.33s | 0.9× | 2.6% | PASS |
| file_reads | `oltp_read_only` | 279.95ms | 213.05ms | 0.8× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 269.15ms | 374.04ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 28.17ms | 42.28ms | 1.5× | 2.6% | PASS |
| file_writes | `oltp_update_index` | 102.50ms | 148.72ms | 1.5× | 3.1% | PASS |
| file_writes | `oltp_update_non_index` | 82.91ms | 103.43ms | 1.2× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 87.33ms | 117.39ms | 1.3× | 1.9% | PASS |
| file_writes | `oltp_write_only` | 58.05ms | 73.62ms | 1.3× | 3.1% | PASS |
| file_writes | `types_delete_insert` | 54.40ms | 63.66ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 142.98ms | 183.50ms | 1.3× | 1.5% | PASS |
| ac_reads | `oltp_point_select` | 62.73ms | 63.62ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 24.73ms | 24.33ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_sum_range` | 22.11ms | 23.50ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_order_range` | 4.23ms | 4.35ms | 1.0× | 3.3% | PASS |
| ac_reads | `oltp_distinct_range` | 5.32ms | 5.53ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_index_scan` | 8.00ms | 8.86ms | 1.1× | 2.0% | PASS |
| ac_reads | `select_random_points` | 34.07ms | 37.97ms | 1.1× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 10.97ms | 11.43ms | 1.0× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 7.65ms | 6.90ms | 0.9× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 37.80ms | 40.43ms | 1.1× | 0.5% | PASS |
| ac_reads | `index_join` | 10.85ms | 13.26ms | 1.2× | 2.8% | PASS |
| ac_reads | `index_join_scan` | 4.97ms | 6.30ms | 1.3× | 2.9% | PASS |
| ac_reads | `types_table_scan` | 1.18s | 1.23s | 1.0× | 0.4% | PASS |
| ac_reads | `table_scan` | 1.43s | 1.35s | 0.9× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 196.08ms | 208.09ms | 1.1× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.44ms | 70.13ms | 2.9× | 10.4% | PASS |
| ac_writes | `oltp_insert_ac` | 27.89ms | 87.51ms | 3.1× | 7.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 30.10ms | 95.03ms | 3.2× | 7.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 26.14ms | 78.40ms | 3.0× | 9.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.68ms | 90.03ms | 3.3× | 7.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.06ms | 87.98ms | 3.1× | 7.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.84ms | 82.48ms | 3.2× | 12.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 35.32ms | 98.67ms | 2.8× | 6.8% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 12s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 94.64ms | 130.00ms | 72.8% | 0.6% | PASS |
| `status_dirty_many_tables` | 98.39ms | 130.00ms | 75.7% | 0.4% | PASS |
| `diff_regular_working_one_table` | 91.05ms | 120.00ms | 75.9% | 0.5% | PASS |
| `diff_regular_working_many_tables` | 103.71ms | 140.00ms | 74.1% | 0.7% | PASS |
| `diff_stat_working_many_tables` | 103.81ms | 140.00ms | 74.1% | 0.6% | PASS |
| `diff_schema_working_many_tables` | 104.83ms | 140.00ms | 74.9% | 0.6% | PASS |
| `branch_list_many_branches` | 22.87ms | 35.00ms | 65.3% | 1.1% | PASS |
| `branch_create_delete` | 25.03ms | 40.00ms | 62.6% | 1.1% | PASS |
| `checkout_branch_clean` | 60.38ms | 150.00ms | 40.3% | 0.8% | PASS |
| `merge_data_no_conflicts` | 29.26ms | 50.00ms | 58.5% | 1.4% | PASS |
| `merge_schema_no_conflicts` | 22.50ms | 35.00ms | 64.3% | 1.4% | PASS |
| `merge_data_conflicts` | 33.04ms | 180.00ms | 18.4% | 1.0% | PASS |
| `merge_data_conflicts_with_resolve` | 32.71ms | 180.00ms | 18.2% | 1.1% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
