# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-27 20:38 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261116.202.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/46054374791)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.21s | 8.53s | 1.0× | 2.1% | **PASS** |
| Writes | 1.62s | 2.74s | 1.7× | 1.8% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.32s | 8.82s | 0.9× | 2.1% | **PASS** |
| Writes | 3.69s | 3.99s | 1.1× | 3.7% | **FAIL** |
| Autocommit writes | 789.85ms | 2.19s | 2.8× | 7.4% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.45s | 2.67s | 1.1× | 1.6% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.43s | 2.37s | 1.0× | 2.8% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 1.69s | 1.77s | 1.0× | 2.1% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 1.64s | 1.71s | 1.0× | 2.2% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 432.63ms | 752.25ms | 1.7× | 1.4% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 483.10ms | 801.74ms | 1.7× | 2.3% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 354.39ms | 612.14ms | 1.7× | 1.9% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 351.03ms | 576.70ms | 1.6× | 2.1% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.60s | 2.71s | 1.0× | 1.6% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.74s | 2.44s | 0.9× | 1.4% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 1.80s | 1.76s | 1.0× | 2.1% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.17s | 1.91s | 0.9× | 1.9% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 577.08ms | 812.70ms | 1.4× | 1.6% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.24s | 1.24s | 1.0× | 6.5% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 970.80ms | 987.47ms | 1.0× | 4.3% | **FAIL** |
| File-backed | Writes | compositepk | 8 | 55 | 899.36ms | 953.02ms | 1.1× | 4.2% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.45s | 2.71s | 1.1× | 1.4% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.80s | 2.53s | 0.9× | 0.9% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 1.70s | 1.76s | 1.0× | 2.4% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 1.78s | 1.82s | 1.0× | 2.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 176.39ms | 562.57ms | 3.2× | 6.6% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 199.30ms | 611.54ms | 3.1× | 28.0% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 226.69ms | 520.01ms | 2.3× | 8.3% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 187.47ms | 500.27ms | 2.7× | 4.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.32ms | 29.40ms | 1.3× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 10.36ms | 11.46ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 8.98ms | 11.04ms | 1.2× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 2.59ms | 2.90ms | 1.1× | 1.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.56ms | 4.06ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.68ms | 4.99ms | 1.4× | 1.9% | PASS |
| mem_reads | `select_random_points` | 9.11ms | 10.53ms | 1.2× | 1.8% | PASS |
| mem_reads | `select_random_ranges` | 2.79ms | 3.87ms | 1.4× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 4.16ms | 4.12ms | 1.0× | 1.2% | PASS |
| mem_reads | `groupby_scan` | 30.14ms | 33.14ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 5.69ms | 7.76ms | 1.4× | 1.6% | PASS |
| mem_reads | `index_join_scan` | 3.01ms | 4.44ms | 1.5× | 5.8% | PASS |
| mem_reads | `types_table_scan` | 1.03s | 1.16s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.26s | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 108.18ms | 121.47ms | 1.1× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 175.74ms | 275.94ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 14.90ms | 28.43ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 49.92ms | 101.15ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 34.99ms | 63.75ms | 1.8× | 1.3% | PASS |
| mem_writes | `oltp_delete_insert` | 43.06ms | 77.31ms | 1.8× | 0.8% | PASS |
| mem_writes | `oltp_write_only` | 20.62ms | 48.47ms | 2.4× | 1.5% | FAIL |
| mem_writes | `types_delete_insert` | 24.27ms | 40.69ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 69.13ms | 116.52ms | 1.7× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 92.95ms | 47.23ms | 0.5× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 18.54ms | 13.54ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 16.61ms | 13.30ms | 0.8× | 2.2% | PASS |
| file_reads | `oltp_order_range` | 3.42ms | 3.21ms | 0.9× | 2.2% | PASS |
| file_reads | `oltp_distinct_range` | 4.41ms | 4.38ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 10.75ms | 7.11ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_points` | 16.36ms | 12.60ms | 0.8× | 2.9% | PASS |
| file_reads | `select_random_ranges` | 9.65ms | 5.73ms | 0.6× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 11.20ms | 6.10ms | 0.5× | 2.2% | PASS |
| file_reads | `groupby_scan` | 31.17ms | 33.69ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 9.66ms | 9.46ms | 1.0× | 2.2% | PASS |
| file_reads | `index_join_scan` | 4.16ms | 4.86ms | 1.2× | 3.4% | PASS |
| file_reads | `types_table_scan` | 1.02s | 1.16s | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.15s | 1.25s | 1.1× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 201.59ms | 146.33ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 188.09ms | 283.40ms | 1.5× | 1.4% | PASS |
| file_writes | `oltp_insert` | 21.37ms | 31.28ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 74.02ms | 111.46ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 56.87ms | 75.30ms | 1.3× | 1.2% | PASS |
| file_writes | `oltp_delete_insert` | 65.72ms | 89.10ms | 1.4× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 42.87ms | 57.02ms | 1.3× | 2.7% | PASS |
| file_writes | `types_delete_insert` | 38.92ms | 43.21ms | 1.1× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 89.21ms | 121.93ms | 1.4× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 45.10ms | 47.08ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 12.80ms | 13.48ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 11.64ms | 13.30ms | 1.1× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 2.96ms | 3.21ms | 1.1× | 2.9% | PASS |
| ac_reads | `oltp_distinct_range` | 3.92ms | 4.37ms | 1.1× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.11ms | 7.13ms | 1.2× | 1.4% | PASS |
| ac_reads | `select_random_points` | 11.74ms | 12.82ms | 1.1× | 2.4% | PASS |
| ac_reads | `select_random_ranges` | 5.14ms | 5.74ms | 1.1× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 6.53ms | 6.05ms | 0.9× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 30.66ms | 33.73ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 7.26ms | 9.37ms | 1.3× | 2.0% | PASS |
| ac_reads | `index_join_scan` | 3.69ms | 4.82ms | 1.3× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 1.02s | 1.16s | 1.1× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.15s | 1.25s | 1.1× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 136.71ms | 146.10ms | 1.1× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.82ms | 57.83ms | 2.9× | 7.7% | PASS |
| ac_writes | `oltp_insert_ac` | 21.83ms | 69.49ms | 3.2× | 7.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.60ms | 80.93ms | 3.3× | 5.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.73ms | 65.25ms | 3.1× | 7.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 21.70ms | 72.22ms | 3.3× | 6.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.38ms | 74.05ms | 3.3× | 6.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.94ms | 64.62ms | 3.4× | 5.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 26.39ms | 78.18ms | 3.0× | 5.1% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.19ms | 29.01ms | 0.9× | 3.5% | PASS |
| mem_reads | `oltp_range_select` | 16.08ms | 11.33ms | 0.7× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 13.81ms | 11.11ms | 0.8× | 2.8% | PASS |
| mem_reads | `oltp_order_range` | 2.93ms | 2.65ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 3.68ms | 3.49ms | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_index_scan` | 3.30ms | 4.96ms | 1.5× | 1.7% | PASS |
| mem_reads | `select_random_points` | 19.02ms | 16.71ms | 0.9× | 4.4% | PASS |
| mem_reads | `select_random_ranges` | 2.94ms | 4.26ms | 1.4× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 3.35ms | 3.85ms | 1.1× | 2.4% | PASS |
| mem_reads | `groupby_scan` | 29.27ms | 28.92ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 9.99ms | 7.77ms | 0.8× | 3.1% | PASS |
| mem_reads | `index_join_scan` | 3.46ms | 4.91ms | 1.4× | 4.3% | PASS |
| mem_reads | `types_table_scan` | 996.13ms | 1.02s | 1.0× | 5.1% | PASS |
| mem_reads | `table_scan` | 1.17s | 1.12s | 1.0× | 3.3% | PASS |
| mem_reads | `oltp_read_only` | 120.41ms | 107.73ms | 0.9× | 2.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 179.51ms | 276.83ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 13.99ms | 30.95ms | 2.2× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 58.27ms | 122.05ms | 2.1× | 3.5% | PASS |
| mem_writes | `oltp_update_non_index` | 43.73ms | 72.04ms | 1.6× | 3.6% | PASS |
| mem_writes | `oltp_delete_insert` | 47.17ms | 88.43ms | 1.9× | 2.2% | PASS |
| mem_writes | `oltp_write_only` | 24.37ms | 52.60ms | 2.2× | 1.7% | PASS |
| mem_writes | `types_delete_insert` | 33.80ms | 44.96ms | 1.3× | 2.5% | PASS |
| mem_writes | `oltp_read_write` | 82.25ms | 113.88ms | 1.4× | 3.4% | PASS |
| file_reads | `oltp_point_select` | 98.81ms | 44.74ms | 0.5× | 1.4% | PASS |
| file_reads | `oltp_range_select` | 22.68ms | 12.67ms | 0.6× | 2.2% | PASS |
| file_reads | `oltp_sum_range` | 21.33ms | 12.72ms | 0.6× | 2.2% | PASS |
| file_reads | `oltp_order_range` | 3.73ms | 2.86ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_distinct_range` | 4.51ms | 3.69ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 10.50ms | 6.74ms | 0.6× | 1.0% | PASS |
| file_reads | `select_random_points` | 28.20ms | 18.65ms | 0.7× | 3.8% | PASS |
| file_reads | `select_random_ranges` | 9.89ms | 5.97ms | 0.6× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 10.68ms | 5.66ms | 0.5× | 1.1% | PASS |
| file_reads | `groupby_scan` | 29.85ms | 29.11ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 14.03ms | 9.03ms | 0.6× | 4.3% | PASS |
| file_reads | `index_join_scan` | 4.21ms | 5.20ms | 1.2× | 2.2% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.04s | 1.0× | 4.5% | PASS |
| file_reads | `table_scan` | 1.18s | 1.11s | 0.9× | 3.1% | PASS |
| file_reads | `oltp_read_only` | 228.86ms | 133.38ms | 0.6× | 1.4% | PASS |
| file_writes | `oltp_bulk_insert` | 270.12ms | 347.49ms | 1.3× | 8.2% | PASS |
| file_writes | `oltp_insert` | 25.71ms | 54.37ms | 2.1× | 14.1% | PASS |
| file_writes | `oltp_update_index` | 178.64ms | 198.71ms | 1.1× | 5.9% | PASS |
| file_writes | `oltp_update_non_index` | 144.16ms | 128.54ms | 0.9× | 3.0% | PASS |
| file_writes | `oltp_delete_insert` | 182.94ms | 157.87ms | 0.9× | 2.7% | PASS |
| file_writes | `oltp_write_only` | 121.20ms | 108.13ms | 0.9× | 7.1% | PASS |
| file_writes | `types_delete_insert` | 140.75ms | 78.99ms | 0.6× | 11.8% | PASS |
| file_writes | `oltp_read_write` | 180.42ms | 167.70ms | 0.9× | 4.9% | PASS |
| ac_reads | `oltp_point_select` | 49.14ms | 44.37ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 17.11ms | 12.53ms | 0.7× | 0.9% | PASS |
| ac_reads | `oltp_sum_range` | 15.43ms | 12.52ms | 0.8× | 1.0% | PASS |
| ac_reads | `oltp_order_range` | 3.25ms | 2.85ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 4.02ms | 3.69ms | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_index_scan` | 5.81ms | 6.79ms | 1.2× | 1.0% | PASS |
| ac_reads | `select_random_points` | 22.17ms | 18.61ms | 0.8× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 5.20ms | 5.95ms | 1.1× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 5.95ms | 5.63ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 28.86ms | 28.82ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 10.92ms | 8.73ms | 0.8× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.66ms | 5.02ms | 1.4× | 1.0% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.08s | 0.9× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.30s | 1.17s | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 160.22ms | 135.74ms | 0.8× | 4.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.67ms | 59.96ms | 2.6× | 25.3% | PASS |
| ac_writes | `oltp_insert_ac` | 23.06ms | 67.52ms | 2.9× | 17.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 32.50ms | 91.91ms | 2.8× | 35.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.04ms | 66.18ms | 3.3× | 26.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.00ms | 77.25ms | 3.2× | 23.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.83ms | 87.77ms | 3.5× | 30.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.45ms | 74.51ms | 3.2× | 33.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.75ms | 86.45ms | 3.0× | 33.4% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.78ms | 22.83ms | 1.0× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 10.58ms | 8.70ms | 0.8× | 2.8% | PASS |
| mem_reads | `oltp_sum_range` | 9.99ms | 8.62ms | 0.9× | 2.7% | PASS |
| mem_reads | `oltp_order_range` | 2.18ms | 2.02ms | 0.9× | 1.9% | PASS |
| mem_reads | `oltp_distinct_range` | 2.68ms | 2.57ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 2.60ms | 4.20ms | 1.6× | 3.0% | PASS |
| mem_reads | `select_random_points` | 16.87ms | 14.63ms | 0.9× | 3.7% | PASS |
| mem_reads | `select_random_ranges` | 2.21ms | 3.28ms | 1.5× | 3.4% | PASS |
| mem_reads | `covering_index_scan` | 2.29ms | 2.60ms | 1.1× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 20.20ms | 20.92ms | 1.0× | 1.8% | PASS |
| mem_reads | `index_join` | 7.41ms | 5.34ms | 0.7× | 2.2% | PASS |
| mem_reads | `index_join_scan` | 2.23ms | 4.25ms | 1.9× | 4.3% | PASS |
| mem_reads | `types_table_scan` | 710.65ms | 755.74ms | 1.1× | 1.3% | PASS |
| mem_reads | `table_scan` | 804.28ms | 846.46ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_read_only` | 73.20ms | 71.14ms | 1.0× | 2.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 135.90ms | 207.56ms | 1.5× | 1.5% | PASS |
| mem_writes | `oltp_insert` | 10.66ms | 23.93ms | 2.2× | 1.4% | PASS |
| mem_writes | `oltp_update_index` | 41.73ms | 91.83ms | 2.2× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 34.18ms | 56.89ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 36.02ms | 73.34ms | 2.0× | 3.1% | PASS |
| mem_writes | `oltp_write_only` | 17.83ms | 40.94ms | 2.3× | 2.1% | PASS |
| mem_writes | `types_delete_insert` | 24.64ms | 34.74ms | 1.4× | 2.7% | PASS |
| mem_writes | `oltp_read_write` | 53.44ms | 82.92ms | 1.6× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 74.09ms | 35.57ms | 0.5× | 2.1% | PASS |
| file_reads | `oltp_range_select` | 15.12ms | 9.55ms | 0.6× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 14.60ms | 9.47ms | 0.6× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 2.68ms | 2.13ms | 0.8× | 2.5% | PASS |
| file_reads | `oltp_distinct_range` | 3.20ms | 2.65ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 8.11ms | 5.22ms | 0.6× | 2.1% | PASS |
| file_reads | `select_random_points` | 19.70ms | 14.08ms | 0.7× | 3.3% | PASS |
| file_reads | `select_random_ranges` | 7.40ms | 4.79ms | 0.6× | 2.4% | PASS |
| file_reads | `covering_index_scan` | 8.01ms | 4.49ms | 0.6× | 2.7% | PASS |
| file_reads | `groupby_scan` | 21.92ms | 21.45ms | 1.0× | 2.1% | PASS |
| file_reads | `index_join` | 10.76ms | 6.63ms | 0.6× | 4.9% | PASS |
| file_reads | `index_join_scan` | 3.37ms | 4.39ms | 1.3× | 3.7% | PASS |
| file_reads | `types_table_scan` | 685.82ms | 727.91ms | 1.1× | 1.6% | PASS |
| file_reads | `table_scan` | 779.26ms | 817.90ms | 1.0× | 1.4% | PASS |
| file_reads | `oltp_read_only` | 150.44ms | 93.78ms | 0.6× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 203.78ms | 271.42ms | 1.3× | 5.1% | PASS |
| file_writes | `oltp_insert` | 19.50ms | 47.43ms | 2.4× | 7.7% | FAIL |
| file_writes | `oltp_update_index` | 140.50ms | 165.52ms | 1.2× | 4.3% | PASS |
| file_writes | `oltp_update_non_index` | 120.22ms | 108.25ms | 0.9× | 2.8% | PASS |
| file_writes | `oltp_delete_insert` | 142.48ms | 119.24ms | 0.8× | 2.4% | PASS |
| file_writes | `oltp_write_only` | 100.07ms | 78.76ms | 0.8× | 1.6% | PASS |
| file_writes | `types_delete_insert` | 101.72ms | 68.79ms | 0.7× | 10.9% | PASS |
| file_writes | `oltp_read_write` | 142.53ms | 128.06ms | 0.9× | 4.4% | PASS |
| ac_reads | `oltp_point_select` | 38.48ms | 34.67ms | 0.9× | 2.4% | PASS |
| ac_reads | `oltp_range_select` | 11.57ms | 9.45ms | 0.8× | 4.0% | PASS |
| ac_reads | `oltp_sum_range` | 10.99ms | 9.38ms | 0.9× | 2.4% | PASS |
| ac_reads | `oltp_order_range` | 2.37ms | 2.13ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 2.85ms | 2.65ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 4.31ms | 5.19ms | 1.2× | 3.1% | PASS |
| ac_reads | `select_random_points` | 15.76ms | 13.77ms | 0.9× | 3.1% | PASS |
| ac_reads | `select_random_ranges` | 3.87ms | 4.68ms | 1.2× | 1.8% | PASS |
| ac_reads | `covering_index_scan` | 4.09ms | 4.14ms | 1.0× | 2.3% | PASS |
| ac_reads | `groupby_scan` | 20.68ms | 20.81ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 8.40ms | 6.44ms | 0.8× | 4.4% | PASS |
| ac_reads | `index_join_scan` | 2.93ms | 4.16ms | 1.4× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 690.26ms | 725.64ms | 1.1× | 1.6% | PASS |
| ac_reads | `table_scan` | 782.34ms | 821.27ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_read_only` | 106.06ms | 95.82ms | 0.9× | 2.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 28.35ms | 57.54ms | 2.0× | 12.5% | PASS |
| ac_writes | `oltp_insert_ac` | 29.96ms | 63.29ms | 2.1× | 7.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.84ms | 70.90ms | 2.4× | 7.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 25.20ms | 59.15ms | 2.3× | 6.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 29.14ms | 65.97ms | 2.3× | 4.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.64ms | 66.14ms | 2.3× | 9.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.24ms | 64.04ms | 2.8× | 8.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.31ms | 72.97ms | 2.3× | 10.2% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 21.36ms | 23.78ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_range_select` | 11.15ms | 11.25ms | 1.0× | 3.2% | PASS |
| mem_reads | `oltp_sum_range` | 10.13ms | 10.88ms | 1.1× | 2.2% | PASS |
| mem_reads | `oltp_order_range` | 2.23ms | 2.21ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_distinct_range` | 2.80ms | 2.72ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_index_scan` | 2.99ms | 4.10ms | 1.4× | 4.3% | PASS |
| mem_reads | `select_random_points` | 16.78ms | 19.11ms | 1.1× | 2.1% | PASS |
| mem_reads | `select_random_ranges` | 4.45ms | 4.95ms | 1.1× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 2.27ms | 2.85ms | 1.3× | 5.2% | PASS |
| mem_reads | `groupby_scan` | 21.52ms | 21.96ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 4.71ms | 6.34ms | 1.3× | 7.4% | PASS |
| mem_reads | `index_join_scan` | 2.90ms | 4.36ms | 1.5× | 3.8% | PASS |
| mem_reads | `types_table_scan` | 693.68ms | 721.48ms | 1.0× | 2.5% | PASS |
| mem_reads | `table_scan` | 763.91ms | 793.46ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_read_only` | 76.03ms | 82.91ms | 1.1× | 2.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 140.41ms | 195.38ms | 1.4× | 1.5% | PASS |
| mem_writes | `oltp_insert` | 10.81ms | 21.27ms | 2.0× | 2.0% | PASS |
| mem_writes | `oltp_update_index` | 40.35ms | 77.85ms | 1.9× | 2.9% | PASS |
| mem_writes | `oltp_update_non_index` | 31.57ms | 52.30ms | 1.7× | 2.2% | PASS |
| mem_writes | `oltp_delete_insert` | 30.76ms | 61.82ms | 2.0× | 2.5% | PASS |
| mem_writes | `oltp_write_only` | 17.45ms | 40.02ms | 2.3× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 21.25ms | 35.61ms | 1.7× | 2.0% | PASS |
| mem_writes | `oltp_read_write` | 58.42ms | 92.46ms | 1.6× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 73.48ms | 37.38ms | 0.5× | 2.2% | PASS |
| file_reads | `oltp_range_select` | 17.05ms | 12.88ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 15.74ms | 12.34ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 2.84ms | 2.37ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_distinct_range` | 3.31ms | 2.86ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 8.59ms | 5.58ms | 0.6× | 3.1% | PASS |
| file_reads | `select_random_points` | 22.70ms | 20.21ms | 0.9× | 4.5% | PASS |
| file_reads | `select_random_ranges` | 10.09ms | 6.74ms | 0.7× | 1.9% | PASS |
| file_reads | `covering_index_scan` | 8.34ms | 4.56ms | 0.5× | 1.7% | PASS |
| file_reads | `groupby_scan` | 23.72ms | 24.01ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join` | 8.85ms | 8.80ms | 1.0× | 5.5% | PASS |
| file_reads | `index_join_scan` | 3.79ms | 5.04ms | 1.3× | 11.5% | PASS |
| file_reads | `types_table_scan` | 849.61ms | 802.60ms | 0.9× | 2.8% | PASS |
| file_reads | `table_scan` | 953.61ms | 849.21ms | 0.9× | 5.5% | PASS |
| file_reads | `oltp_read_only` | 172.12ms | 116.32ms | 0.7× | 1.6% | PASS |
| file_writes | `oltp_bulk_insert` | 189.99ms | 249.10ms | 1.3× | 3.1% | PASS |
| file_writes | `oltp_insert` | 19.95ms | 32.94ms | 1.7× | 10.7% | PASS |
| file_writes | `oltp_update_index` | 143.88ms | 145.99ms | 1.0× | 4.6% | PASS |
| file_writes | `oltp_update_non_index` | 120.63ms | 101.48ms | 0.8× | 2.6% | PASS |
| file_writes | `oltp_delete_insert` | 114.35ms | 119.03ms | 1.0× | 4.4% | PASS |
| file_writes | `oltp_write_only` | 90.26ms | 88.86ms | 1.0× | 4.0% | PASS |
| file_writes | `types_delete_insert` | 79.82ms | 61.69ms | 0.8× | 5.5% | PASS |
| file_writes | `oltp_read_write` | 140.47ms | 153.94ms | 1.1× | 3.4% | PASS |
| ac_reads | `oltp_point_select` | 41.92ms | 40.66ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_range_select` | 15.10ms | 14.06ms | 0.9× | 2.5% | PASS |
| ac_reads | `oltp_sum_range` | 13.50ms | 13.49ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_order_range` | 2.76ms | 2.61ms | 0.9× | 2.3% | PASS |
| ac_reads | `oltp_distinct_range` | 3.29ms | 3.14ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 5.69ms | 6.03ms | 1.1× | 2.6% | PASS |
| ac_reads | `select_random_points` | 20.32ms | 21.92ms | 1.1× | 4.2% | PASS |
| ac_reads | `select_random_ranges` | 6.87ms | 6.88ms | 1.0× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 4.79ms | 4.52ms | 0.9× | 2.4% | PASS |
| ac_reads | `groupby_scan` | 23.17ms | 23.42ms | 1.0× | 1.9% | PASS |
| ac_reads | `index_join` | 6.91ms | 8.27ms | 1.2× | 4.3% | PASS |
| ac_reads | `index_join_scan` | 3.42ms | 4.44ms | 1.3× | 5.5% | PASS |
| ac_reads | `types_table_scan` | 725.85ms | 744.01ms | 1.0× | 2.2% | PASS |
| ac_reads | `table_scan` | 795.66ms | 820.56ms | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_read_only` | 109.12ms | 108.17ms | 1.0× | 2.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.40ms | 49.55ms | 2.4× | 4.4% | PASS |
| ac_writes | `oltp_insert_ac` | 23.89ms | 63.70ms | 2.7× | 5.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.83ms | 70.44ms | 2.7× | 3.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.08ms | 58.26ms | 2.8× | 4.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.79ms | 65.11ms | 2.9× | 3.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.67ms | 64.79ms | 2.9× | 5.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.28ms | 59.03ms | 2.4× | 11.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 26.53ms | 69.40ms | 2.6× | 2.9% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 59s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 72.73ms | 130.00ms | 55.9% | 0.4% | PASS |
| `status_dirty_many_tables` | 76.07ms | 130.00ms | 58.5% | 0.3% | PASS |
| `diff_regular_working_one_table` | 68.58ms | 120.00ms | 57.1% | 0.5% | PASS |
| `diff_regular_working_many_tables` | 80.78ms | 140.00ms | 57.7% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 80.62ms | 140.00ms | 57.6% | 0.4% | PASS |
| `diff_schema_working_many_tables` | 81.47ms | 140.00ms | 58.2% | 0.4% | PASS |
| `branch_list_many_branches` | 18.68ms | 35.00ms | 53.4% | 1.0% | PASS |
| `branch_create_delete` | 28.52ms | 40.00ms | 71.3% | 2.4% | PASS |
| `checkout_branch_clean` | 107.03ms | 150.00ms | 71.4% | 1.5% | PASS |
| `merge_data_no_conflicts` | 34.24ms | 50.00ms | 68.5% | 3.1% | PASS |
| `merge_schema_no_conflicts` | 19.59ms | 35.00ms | 56.0% | 1.3% | PASS |
| `merge_data_conflicts` | 26.48ms | 180.00ms | 14.7% | 1.1% | PASS |
| `merge_data_conflicts_with_resolve` | 26.69ms | 180.00ms | 14.8% | 1.0% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
