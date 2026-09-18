# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-18 15:20 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/35351923259)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.88s | 9.21s | 1.0× | 1.4% | **PASS** |
| Writes | 1.80s | 2.87s | 1.6× | 1.4% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.57s | 9.40s | 1.0× | 1.3% | **PASS** |
| Writes | 4.75s | 4.48s | 0.9× | 6.7% | **PASS** |
| Autocommit writes | 3.97s | 10.86s | 2.7× | 16.3% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.04s | 2.14s | 1.0× | 1.5% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.17s | 2.26s | 1.0× | 1.0% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 1.80s | 1.92s | 1.1× | 2.1% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.86s | 2.90s | 1.0× | 1.5% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 348.13ms | 542.62ms | 1.6× | 1.4% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 469.15ms | 744.08ms | 1.6× | 1.1% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 356.59ms | 580.84ms | 1.6× | 1.9% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 625.04ms | 1.00s | 1.6× | 1.5% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.34s | 2.22s | 0.9× | 1.1% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.42s | 2.31s | 1.0× | 1.1% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 1.89s | 1.95s | 1.0× | 2.6% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.92s | 2.92s | 1.0× | 1.4% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 918.22ms | 886.17ms | 1.0× | 8.3% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.34s | 1.26s | 0.9× | 5.3% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.70s | 1.26s | 0.7× | 38.6% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 793.18ms | 1.07s | 1.4× | 2.1% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.12s | 2.19s | 1.0× | 1.1% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.28s | 2.32s | 1.0× | 5.0% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 1.85s | 1.96s | 1.1× | 1.9% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.93s | 2.96s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 261.95ms | 764.94ms | 2.9× | 19.7% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 309.91ms | 869.95ms | 2.8× | 14.3% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 3.16s | 8.37s | 2.7× | 47.4% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 237.11ms | 855.13ms | 3.6× | 9.0% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 19.71ms | 22.56ms | 1.1× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 8.87ms | 8.84ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 7.91ms | 8.76ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 2.15ms | 2.27ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 2.98ms | 3.15ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.10ms | 3.85ms | 1.2× | 1.6% | PASS |
| mem_reads | `select_random_points` | 8.48ms | 9.10ms | 1.1× | 2.0% | PASS |
| mem_reads | `select_random_ranges` | 2.44ms | 3.16ms | 1.3× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 3.31ms | 3.28ms | 1.0× | 1.8% | PASS |
| mem_reads | `groupby_scan` | 25.24ms | 27.39ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 4.56ms | 6.09ms | 1.3× | 1.2% | PASS |
| mem_reads | `index_join_scan` | 2.66ms | 3.80ms | 1.4× | 1.7% | PASS |
| mem_reads | `types_table_scan` | 871.43ms | 927.11ms | 1.1× | 1.0% | PASS |
| mem_reads | `table_scan` | 997.24ms | 1.02s | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_read_only` | 80.61ms | 89.71ms | 1.1× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 140.47ms | 201.75ms | 1.4× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 12.21ms | 20.92ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 40.70ms | 73.91ms | 1.8× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 29.40ms | 45.14ms | 1.5× | 1.4% | PASS |
| mem_writes | `oltp_delete_insert` | 36.47ms | 55.12ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 17.78ms | 35.27ms | 2.0× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 19.23ms | 27.24ms | 1.4× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 51.88ms | 83.27ms | 1.6× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 93.02ms | 39.96ms | 0.4× | 0.5% | PASS |
| file_reads | `oltp_range_select` | 16.62ms | 10.67ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_sum_range` | 15.60ms | 10.60ms | 0.7× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.01ms | 2.51ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_distinct_range` | 3.82ms | 3.39ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 10.86ms | 6.08ms | 0.6× | 0.9% | PASS |
| file_reads | `select_random_points` | 16.41ms | 10.99ms | 0.7× | 1.1% | PASS |
| file_reads | `select_random_ranges` | 9.98ms | 5.02ms | 0.5× | 0.6% | PASS |
| file_reads | `covering_index_scan` | 11.20ms | 5.44ms | 0.5× | 1.3% | PASS |
| file_reads | `groupby_scan` | 26.24ms | 27.67ms | 1.1× | 1.3% | PASS |
| file_reads | `index_join` | 8.88ms | 7.70ms | 0.9× | 1.1% | PASS |
| file_reads | `index_join_scan` | 3.62ms | 4.14ms | 1.1× | 1.0% | PASS |
| file_reads | `types_table_scan` | 875.62ms | 928.82ms | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 1.05s | 1.04s | 1.0× | 2.8% | PASS |
| file_reads | `oltp_read_only` | 193.83ms | 117.25ms | 0.6× | 1.5% | PASS |
| file_writes | `oltp_bulk_insert` | 189.42ms | 252.71ms | 1.3× | 4.7% | PASS |
| file_writes | `oltp_insert` | 29.70ms | 35.53ms | 1.2× | 8.3% | PASS |
| file_writes | `oltp_update_index` | 140.64ms | 141.39ms | 1.0× | 8.3% | PASS |
| file_writes | `oltp_update_non_index` | 116.81ms | 92.76ms | 0.8× | 6.2% | PASS |
| file_writes | `oltp_delete_insert` | 139.75ms | 111.38ms | 0.8× | 31.3% | PASS |
| file_writes | `oltp_write_only` | 104.72ms | 75.08ms | 0.7× | 19.9% | PASS |
| file_writes | `types_delete_insert` | 74.48ms | 56.01ms | 0.8× | 8.9% | PASS |
| file_writes | `oltp_read_write` | 122.70ms | 121.31ms | 1.0× | 6.0% | PASS |
| ac_reads | `oltp_point_select` | 42.99ms | 39.99ms | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_range_select` | 11.78ms | 10.68ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 10.71ms | 10.59ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 2.56ms | 2.52ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_distinct_range` | 3.33ms | 3.39ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 5.90ms | 6.05ms | 1.0× | 0.9% | PASS |
| ac_reads | `select_random_points` | 11.41ms | 10.91ms | 1.0× | 1.5% | PASS |
| ac_reads | `select_random_ranges` | 5.05ms | 5.01ms | 1.0× | 0.6% | PASS |
| ac_reads | `covering_index_scan` | 6.19ms | 5.45ms | 0.9× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 25.43ms | 27.54ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 6.30ms | 7.61ms | 1.2× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.16ms | 4.14ms | 1.3× | 1.4% | PASS |
| ac_reads | `types_table_scan` | 866.80ms | 922.99ms | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 996.47ms | 1.02s | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 117.09ms | 115.25ms | 1.0× | 0.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 31.35ms | 85.27ms | 2.7× | 26.0% | PASS |
| ac_writes | `oltp_insert_ac` | 31.67ms | 89.10ms | 2.8× | 11.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 33.13ms | 98.29ms | 3.0× | 9.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 30.84ms | 87.84ms | 2.8× | 12.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 32.46ms | 106.63ms | 3.3× | 35.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 34.09ms | 97.58ms | 2.9× | 29.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 30.61ms | 92.76ms | 3.0× | 23.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 37.81ms | 107.48ms | 2.8× | 16.4% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 28.03ms | 28.08ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_range_select` | 13.46ms | 10.72ms | 0.8× | 0.9% | PASS |
| mem_reads | `oltp_sum_range` | 12.28ms | 10.46ms | 0.9× | 0.7% | PASS |
| mem_reads | `oltp_order_range` | 2.74ms | 2.51ms | 0.9× | 0.6% | PASS |
| mem_reads | `oltp_distinct_range` | 3.60ms | 3.38ms | 0.9× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.20ms | 4.50ms | 1.4× | 0.9% | PASS |
| mem_reads | `select_random_points` | 17.50ms | 15.83ms | 0.9× | 1.2% | PASS |
| mem_reads | `select_random_ranges` | 2.85ms | 4.16ms | 1.5× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 3.32ms | 3.49ms | 1.0× | 1.1% | PASS |
| mem_reads | `groupby_scan` | 28.85ms | 28.65ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 8.77ms | 6.77ms | 0.8× | 1.3% | PASS |
| mem_reads | `index_join_scan` | 3.08ms | 4.52ms | 1.5× | 1.0% | PASS |
| mem_reads | `types_table_scan` | 899.75ms | 962.93ms | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.04s | 1.07s | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_read_only` | 109.91ms | 103.74ms | 0.9× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 190.22ms | 271.16ms | 1.4× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 14.34ms | 28.41ms | 2.0× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 52.62ms | 108.57ms | 2.1× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 39.91ms | 63.36ms | 1.6× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 43.50ms | 78.92ms | 1.8× | 0.8% | PASS |
| mem_writes | `oltp_write_only` | 22.71ms | 46.12ms | 2.0× | 1.0% | PASS |
| mem_writes | `types_delete_insert` | 30.67ms | 39.07ms | 1.3× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 75.17ms | 108.45ms | 1.4× | 1.5% | PASS |
| file_reads | `oltp_point_select` | 104.27ms | 46.57ms | 0.4× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 21.96ms | 12.74ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_sum_range` | 20.55ms | 12.58ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_order_range` | 3.69ms | 2.81ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_distinct_range` | 4.52ms | 3.72ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 11.05ms | 6.87ms | 0.6× | 1.3% | PASS |
| file_reads | `select_random_points` | 26.31ms | 18.09ms | 0.7× | 1.1% | PASS |
| file_reads | `select_random_ranges` | 10.53ms | 6.14ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 11.24ms | 5.84ms | 0.5× | 0.9% | PASS |
| file_reads | `groupby_scan` | 29.89ms | 29.25ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join` | 13.46ms | 8.80ms | 0.7× | 1.4% | PASS |
| file_reads | `index_join_scan` | 4.11ms | 4.99ms | 1.2× | 2.0% | PASS |
| file_reads | `types_table_scan` | 904.02ms | 958.09ms | 1.1× | 0.8% | PASS |
| file_reads | `table_scan` | 1.04s | 1.06s | 1.0× | 1.1% | PASS |
| file_reads | `oltp_read_only` | 221.92ms | 131.78ms | 0.6× | 1.5% | PASS |
| file_writes | `oltp_bulk_insert` | 290.24ms | 350.50ms | 1.2× | 7.3% | PASS |
| file_writes | `oltp_insert` | 35.89ms | 55.52ms | 1.5× | 7.8% | PASS |
| file_writes | `oltp_update_index` | 181.47ms | 202.17ms | 1.1× | 4.0% | PASS |
| file_writes | `oltp_update_non_index` | 161.10ms | 132.82ms | 0.8× | 4.9% | PASS |
| file_writes | `oltp_delete_insert` | 195.95ms | 155.14ms | 0.8× | 4.0% | PASS |
| file_writes | `oltp_write_only` | 131.28ms | 105.69ms | 0.8× | 5.8% | PASS |
| file_writes | `types_delete_insert` | 156.34ms | 88.75ms | 0.6× | 9.5% | PASS |
| file_writes | `oltp_read_write` | 184.09ms | 167.18ms | 0.9× | 3.4% | PASS |
| ac_reads | `oltp_point_select` | 55.71ms | 47.30ms | 0.8× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 18.24ms | 13.84ms | 0.8× | 4.7% | PASS |
| ac_reads | `oltp_sum_range` | 16.59ms | 13.53ms | 0.8× | 5.0% | PASS |
| ac_reads | `oltp_order_range` | 3.34ms | 3.10ms | 0.9× | 7.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.39ms | 4.30ms | 1.0× | 9.6% | PASS |
| ac_reads | `oltp_index_scan` | 6.93ms | 7.72ms | 1.1× | 7.5% | PASS |
| ac_reads | `select_random_points` | 22.31ms | 19.33ms | 0.9× | 4.9% | PASS |
| ac_reads | `select_random_ranges` | 6.04ms | 6.83ms | 1.1× | 6.9% | PASS |
| ac_reads | `covering_index_scan` | 6.77ms | 6.65ms | 1.0× | 7.8% | PASS |
| ac_reads | `groupby_scan` | 30.27ms | 30.63ms | 1.0× | 3.0% | PASS |
| ac_reads | `index_join` | 12.12ms | 9.70ms | 0.8× | 5.7% | PASS |
| ac_reads | `index_join_scan` | 3.98ms | 5.79ms | 1.5× | 7.7% | PASS |
| ac_reads | `types_table_scan` | 902.56ms | 957.42ms | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.04s | 1.06s | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 148.36ms | 130.57ms | 0.9× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 35.68ms | 93.95ms | 2.6× | 24.4% | PASS |
| ac_writes | `oltp_insert_ac` | 39.99ms | 101.33ms | 2.5× | 12.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 40.00ms | 119.28ms | 3.0× | 11.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 33.98ms | 104.51ms | 3.1× | 11.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 39.49ms | 112.43ms | 2.8× | 16.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 37.69ms | 111.87ms | 3.0× | 23.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 36.46ms | 114.44ms | 3.1× | 16.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 46.61ms | 112.15ms | 2.4× | 9.6% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 22.93ms | 22.39ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 10.56ms | 8.95ms | 0.8× | 3.4% | PASS |
| mem_reads | `oltp_sum_range` | 10.96ms | 8.69ms | 0.8× | 2.6% | PASS |
| mem_reads | `oltp_order_range` | 2.23ms | 2.07ms | 0.9× | 2.0% | PASS |
| mem_reads | `oltp_distinct_range` | 2.81ms | 2.83ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_index_scan` | 2.71ms | 3.81ms | 1.4× | 2.3% | PASS |
| mem_reads | `select_random_points` | 16.41ms | 15.04ms | 0.9× | 2.7% | PASS |
| mem_reads | `select_random_ranges` | 2.22ms | 3.41ms | 1.5× | 2.5% | PASS |
| mem_reads | `covering_index_scan` | 2.48ms | 2.71ms | 1.1× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 21.36ms | 23.03ms | 1.1× | 1.1% | PASS |
| mem_reads | `index_join` | 9.11ms | 6.55ms | 0.7× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 2.60ms | 4.73ms | 1.8× | 3.4% | PASS |
| mem_reads | `types_table_scan` | 746.28ms | 822.16ms | 1.1× | 1.4% | PASS |
| mem_reads | `table_scan` | 863.03ms | 911.36ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_read_only` | 84.50ms | 83.70ms | 1.0× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 136.06ms | 200.91ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 11.10ms | 22.62ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 41.81ms | 88.27ms | 2.1× | 2.4% | PASS |
| mem_writes | `oltp_update_non_index` | 30.35ms | 51.44ms | 1.7× | 2.1% | PASS |
| mem_writes | `oltp_delete_insert` | 34.53ms | 63.30ms | 1.8× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 18.55ms | 37.13ms | 2.0× | 2.2% | PASS |
| mem_writes | `types_delete_insert` | 24.76ms | 31.44ms | 1.3× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 59.42ms | 85.73ms | 1.4× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 45.35ms | 28.26ms | 0.6× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 13.73ms | 10.01ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_sum_range` | 13.62ms | 9.83ms | 0.7× | 2.8% | PASS |
| file_reads | `oltp_order_range` | 2.58ms | 2.24ms | 0.9× | 2.8% | PASS |
| file_reads | `oltp_distinct_range` | 3.12ms | 2.96ms | 0.9× | 2.3% | PASS |
| file_reads | `oltp_index_scan` | 5.12ms | 4.76ms | 0.9× | 3.6% | PASS |
| file_reads | `select_random_points` | 19.63ms | 16.52ms | 0.8× | 2.3% | PASS |
| file_reads | `select_random_ranges` | 4.55ms | 4.08ms | 0.9× | 3.0% | PASS |
| file_reads | `covering_index_scan` | 4.80ms | 3.62ms | 0.8× | 4.4% | PASS |
| file_reads | `groupby_scan` | 21.64ms | 23.34ms | 1.1× | 0.5% | PASS |
| file_reads | `index_join` | 10.61ms | 7.62ms | 0.7× | 3.4% | PASS |
| file_reads | `index_join_scan` | 3.01ms | 5.07ms | 1.7× | 3.6% | PASS |
| file_reads | `types_table_scan` | 747.87ms | 819.26ms | 1.1× | 1.0% | PASS |
| file_reads | `table_scan` | 867.70ms | 913.42ms | 1.1× | 1.7% | PASS |
| file_reads | `oltp_read_only` | 122.38ms | 95.94ms | 0.8× | 2.6% | PASS |
| file_writes | `oltp_bulk_insert` | 331.34ms | 329.69ms | 1.0× | 34.1% | PASS |
| file_writes | `oltp_insert` | 53.97ms | 65.06ms | 1.2× | 62.5% | PASS |
| file_writes | `oltp_update_index` | 217.78ms | 201.15ms | 0.9× | 26.8% | PASS |
| file_writes | `oltp_update_non_index` | 238.86ms | 135.61ms | 0.6× | 39.7% | PASS |
| file_writes | `oltp_delete_insert` | 252.81ms | 147.49ms | 0.6× | 47.4% | PASS |
| file_writes | `oltp_write_only` | 182.72ms | 129.16ms | 0.7× | 42.7% | PASS |
| file_writes | `types_delete_insert` | 165.32ms | 92.22ms | 0.6× | 37.3% | PASS |
| file_writes | `oltp_read_write` | 261.58ms | 163.06ms | 0.6× | 37.5% | PASS |
| ac_reads | `oltp_point_select` | 31.12ms | 29.47ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_range_select` | 12.28ms | 10.08ms | 0.8× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 12.37ms | 9.94ms | 0.8× | 1.8% | PASS |
| ac_reads | `oltp_order_range` | 2.49ms | 2.27ms | 0.9× | 2.7% | PASS |
| ac_reads | `oltp_distinct_range` | 3.08ms | 3.01ms | 1.0× | 2.7% | PASS |
| ac_reads | `oltp_index_scan` | 3.89ms | 5.14ms | 1.3× | 2.3% | PASS |
| ac_reads | `select_random_points` | 19.26ms | 17.16ms | 0.9× | 2.2% | PASS |
| ac_reads | `select_random_ranges` | 3.17ms | 4.27ms | 1.3× | 1.8% | PASS |
| ac_reads | `covering_index_scan` | 3.47ms | 3.89ms | 1.1× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 21.71ms | 23.59ms | 1.1× | 1.3% | PASS |
| ac_reads | `index_join` | 10.56ms | 7.95ms | 0.8× | 2.8% | PASS |
| ac_reads | `index_join_scan` | 2.98ms | 5.11ms | 1.7× | 3.2% | PASS |
| ac_reads | `types_table_scan` | 756.25ms | 825.74ms | 1.1× | 1.2% | PASS |
| ac_reads | `table_scan` | 872.39ms | 914.29ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_read_only` | 98.26ms | 94.60ms | 1.0× | 2.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 351.31ms | 929.77ms | 2.6× | 57.5% | PASS |
| ac_writes | `oltp_insert_ac` | 313.06ms | 999.98ms | 3.2× | 40.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 356.15ms | 981.38ms | 2.8× | 43.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 431.52ms | 1.23s | 2.9× | 49.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 419.91ms | 1.19s | 2.8× | 58.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 545.78ms | 1.22s | 2.2× | 53.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 293.77ms | 758.20ms | 2.6× | 42.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 446.05ms | 1.06s | 2.4× | 45.2% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.29ms | 41.65ms | 1.2× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 19.96ms | 21.60ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 18.14ms | 20.65ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 3.63ms | 3.86ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.79ms | 5.06ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.59ms | 6.17ms | 1.3× | 1.7% | PASS |
| mem_reads | `select_random_points` | 28.54ms | 32.77ms | 1.1× | 2.0% | PASS |
| mem_reads | `select_random_ranges` | 7.84ms | 9.02ms | 1.2× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.25ms | 4.39ms | 1.0× | 2.5% | PASS |
| mem_reads | `groupby_scan` | 36.55ms | 39.74ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 7.99ms | 10.66ms | 1.3× | 2.2% | PASS |
| mem_reads | `index_join_scan` | 4.04ms | 5.67ms | 1.4× | 3.2% | PASS |
| mem_reads | `types_table_scan` | 1.14s | 1.21s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.39s | 1.31s | 0.9× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 162.28ms | 176.50ms | 1.1× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 253.31ms | 360.97ms | 1.4× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 19.55ms | 36.11ms | 1.8× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 70.67ms | 134.18ms | 1.9× | 1.7% | PASS |
| mem_writes | `oltp_update_non_index` | 53.92ms | 87.41ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 52.09ms | 100.21ms | 1.9× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 28.26ms | 60.33ms | 2.1× | 2.0% | PASS |
| mem_writes | `types_delete_insert` | 34.09ms | 55.09ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 113.15ms | 169.84ms | 1.5× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 112.11ms | 63.01ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 29.34ms | 24.21ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 27.14ms | 23.33ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 4.70ms | 4.27ms | 0.9× | 2.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.90ms | 5.51ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 12.66ms | 8.74ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 40.40ms | 37.08ms | 0.9× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 16.15ms | 11.41ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 12.34ms | 6.95ms | 0.6× | 1.6% | PASS |
| file_reads | `groupby_scan` | 38.06ms | 40.30ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 12.43ms | 12.65ms | 1.0× | 1.4% | PASS |
| file_reads | `index_join_scan` | 4.98ms | 6.03ms | 1.2× | 1.9% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.19s | 1.1× | 1.0% | PASS |
| file_reads | `table_scan` | 1.26s | 1.29s | 1.0× | 2.4% | PASS |
| file_reads | `oltp_read_only` | 270.34ms | 204.75ms | 0.8× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 268.92ms | 370.46ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_insert` | 26.57ms | 41.45ms | 1.6× | 2.7% | PASS |
| file_writes | `oltp_update_index` | 100.48ms | 142.77ms | 1.4× | 1.7% | PASS |
| file_writes | `oltp_update_non_index` | 80.66ms | 100.17ms | 1.2× | 2.1% | PASS |
| file_writes | `oltp_delete_insert` | 79.39ms | 109.79ms | 1.4× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 52.03ms | 69.92ms | 1.3× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 51.91ms | 61.75ms | 1.2× | 2.8% | PASS |
| file_writes | `oltp_read_write` | 133.21ms | 175.79ms | 1.3× | 1.8% | PASS |
| ac_reads | `oltp_point_select` | 59.41ms | 61.95ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 23.12ms | 24.03ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_sum_range` | 21.03ms | 23.05ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 4.15ms | 4.28ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 5.24ms | 5.45ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 7.61ms | 8.73ms | 1.1× | 1.3% | PASS |
| ac_reads | `select_random_points` | 32.69ms | 36.55ms | 1.1× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 10.78ms | 11.40ms | 1.1× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 7.32ms | 6.89ms | 0.9× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 37.25ms | 40.30ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 10.03ms | 12.75ms | 1.3× | 2.0% | PASS |
| ac_reads | `index_join_scan` | 4.50ms | 6.10ms | 1.4× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 1.14s | 1.20s | 1.1× | 1.5% | PASS |
| ac_reads | `table_scan` | 1.37s | 1.31s | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 197.11ms | 207.02ms | 1.1× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 28.85ms | 90.52ms | 3.1× | 10.4% | PASS |
| ac_writes | `oltp_insert_ac` | 30.50ms | 112.26ms | 3.7× | 8.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 31.54ms | 119.52ms | 3.8× | 8.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 26.73ms | 99.11ms | 3.7× | 8.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.91ms | 108.48ms | 3.8× | 11.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 29.21ms | 109.46ms | 3.7× | 8.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.78ms | 100.10ms | 3.9× | 11.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 35.60ms | 115.67ms | 3.2× | 9.6% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 1s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 56.29ms | 650.00ms | 8.7% | 0.9% | PASS |
| `status_dirty_many_tables` | 58.37ms | 650.00ms | 9.0% | 0.9% | PASS |
| `diff_regular_working_one_table` | 52.90ms | 600.00ms | 8.8% | 0.9% | PASS |
| `diff_regular_working_many_tables` | 61.68ms | 700.00ms | 8.8% | 0.6% | PASS |
| `diff_stat_working_many_tables` | 61.58ms | 700.00ms | 8.8% | 0.7% | PASS |
| `diff_schema_working_many_tables` | 62.27ms | 700.00ms | 8.9% | 0.6% | PASS |
| `branch_list_many_branches` | 15.44ms | 175.00ms | 8.8% | 2.0% | PASS |
| `branch_create_delete` | 38.33ms | 200.00ms | 19.2% | 51.3% | PASS |
| `checkout_branch_clean` | 112.96ms | 750.00ms | 15.1% | 23.2% | PASS |
| `merge_data_no_conflicts` | 34.69ms | 250.00ms | 13.9% | 31.9% | PASS |
| `merge_schema_no_conflicts` | 30.22ms | 175.00ms | 17.3% | 43.6% | PASS |
| `merge_data_conflicts` | 20.64ms | 900.00ms | 2.3% | 1.1% | PASS |
| `merge_data_conflicts_with_resolve` | 20.52ms | 900.00ms | 2.3% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
