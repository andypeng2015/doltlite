# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-11 17:28 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261004.327.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/38153107893)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.87s | 10.17s | 1.0× | 1.9% | **PASS** |
| Writes | 2.09s | 3.44s | 1.6× | 1.7% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.77s | 10.36s | 1.0× | 1.7% | **PASS** |
| Writes | 3.33s | 4.03s | 1.2× | 1.8% | **PASS** |
| Autocommit writes | 821.05ms | 2.48s | 3.0× | 6.2% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 1.59s | 1.65s | 1.0× | 2.7% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.86s | 2.82s | 1.0× | 1.9% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.84s | 2.87s | 1.0× | 2.1% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.57s | 2.83s | 1.1× | 1.1% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 254.84ms | 414.80ms | 1.6× | 2.0% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 602.12ms | 1.01s | 1.7× | 1.8% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 635.07ms | 1.04s | 1.6× | 1.9% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 596.53ms | 976.86ms | 1.6× | 1.2% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 1.77s | 1.68s | 0.9× | 1.9% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.03s | 2.87s | 0.9× | 1.9% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.08s | 2.91s | 0.9× | 1.6% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.88s | 2.91s | 1.0× | 1.6% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 741.10ms | 719.98ms | 1.0× | 3.9% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 955.93ms | 1.13s | 1.2× | 4.7% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 863.96ms | 1.12s | 1.3× | 1.7% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 767.73ms | 1.05s | 1.4× | 1.5% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 1.78s | 1.75s | 1.0× | 2.1% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.86s | 2.86s | 1.0× | 1.7% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.98s | 2.95s | 1.0× | 1.7% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 3.00s | 2.99s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 174.63ms | 513.40ms | 2.9× | 4.5% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 217.27ms | 660.27ms | 3.0× | 6.8% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 223.16ms | 672.72ms | 3.0× | 5.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 205.99ms | 638.09ms | 3.1× | 7.1% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 13.01ms | 16.98ms | 1.3× | 3.5% | PASS |
| mem_reads | `oltp_range_select` | 5.52ms | 6.38ms | 1.2× | 5.5% | PASS |
| mem_reads | `oltp_sum_range` | 5.31ms | 6.54ms | 1.2× | 3.4% | PASS |
| mem_reads | `oltp_order_range` | 1.50ms | 1.61ms | 1.1× | 2.7% | PASS |
| mem_reads | `oltp_distinct_range` | 1.96ms | 2.13ms | 1.1× | 2.6% | PASS |
| mem_reads | `oltp_index_scan` | 2.16ms | 2.88ms | 1.3× | 2.8% | PASS |
| mem_reads | `select_random_points` | 6.92ms | 7.60ms | 1.1× | 5.7% | PASS |
| mem_reads | `select_random_ranges` | 1.79ms | 2.39ms | 1.3× | 4.5% | PASS |
| mem_reads | `covering_index_scan` | 2.29ms | 2.31ms | 1.0× | 1.1% | PASS |
| mem_reads | `groupby_scan` | 16.41ms | 17.60ms | 1.1× | 1.4% | PASS |
| mem_reads | `index_join` | 3.37ms | 4.47ms | 1.3× | 1.9% | PASS |
| mem_reads | `index_join_scan` | 1.71ms | 3.49ms | 2.0× | 2.8% | PASS |
| mem_reads | `types_table_scan` | 692.01ms | 722.62ms | 1.0× | 1.3% | PASS |
| mem_reads | `table_scan` | 775.54ms | 790.35ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 56.33ms | 63.27ms | 1.1× | 1.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 103.22ms | 151.36ms | 1.5× | 1.8% | PASS |
| mem_writes | `oltp_insert` | 9.09ms | 16.28ms | 1.8× | 1.7% | PASS |
| mem_writes | `oltp_update_index` | 30.96ms | 57.59ms | 1.9× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 21.62ms | 36.05ms | 1.7× | 2.6% | PASS |
| mem_writes | `oltp_delete_insert` | 27.90ms | 45.01ms | 1.6× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 13.36ms | 28.62ms | 2.1× | 3.2% | PASS |
| mem_writes | `types_delete_insert` | 14.49ms | 21.48ms | 1.5× | 4.2% | PASS |
| mem_writes | `oltp_read_write` | 34.19ms | 58.41ms | 1.7× | 2.3% | PASS |
| file_reads | `oltp_point_select` | 69.28ms | 32.09ms | 0.5× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 11.61ms | 7.78ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 11.18ms | 7.88ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 2.19ms | 1.86ms | 0.8× | 3.1% | PASS |
| file_reads | `oltp_distinct_range` | 2.66ms | 2.35ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 8.31ms | 5.00ms | 0.6× | 3.3% | PASS |
| file_reads | `select_random_points` | 13.37ms | 9.99ms | 0.7× | 3.1% | PASS |
| file_reads | `select_random_ranges` | 7.62ms | 4.06ms | 0.5× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 8.57ms | 4.48ms | 0.5× | 3.5% | PASS |
| file_reads | `groupby_scan` | 17.48ms | 18.10ms | 1.0× | 1.4% | PASS |
| file_reads | `index_join` | 6.76ms | 6.54ms | 1.0× | 5.2% | PASS |
| file_reads | `index_join_scan` | 2.92ms | 3.83ms | 1.3× | 5.1% | PASS |
| file_reads | `types_table_scan` | 673.74ms | 692.87ms | 1.0× | 1.4% | PASS |
| file_reads | `table_scan` | 793.23ms | 795.64ms | 1.0× | 2.2% | PASS |
| file_reads | `oltp_read_only` | 140.00ms | 86.32ms | 0.6× | 1.6% | PASS |
| file_writes | `oltp_bulk_insert` | 140.57ms | 198.97ms | 1.4× | 1.9% | PASS |
| file_writes | `oltp_insert` | 19.82ms | 28.70ms | 1.4× | 5.0% | PASS |
| file_writes | `oltp_update_index` | 121.36ms | 108.67ms | 0.9× | 6.5% | PASS |
| file_writes | `oltp_update_non_index` | 100.92ms | 78.73ms | 0.8× | 0.6% | PASS |
| file_writes | `oltp_delete_insert` | 103.06ms | 89.00ms | 0.9× | 2.9% | PASS |
| file_writes | `oltp_write_only` | 80.98ms | 62.40ms | 0.8× | 2.7% | PASS |
| file_writes | `types_delete_insert` | 63.18ms | 47.95ms | 0.8× | 10.2% | PASS |
| file_writes | `oltp_read_write` | 111.21ms | 105.56ms | 0.9× | 4.9% | PASS |
| ac_reads | `oltp_point_select` | 36.18ms | 35.79ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 8.62ms | 8.40ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 8.13ms | 8.44ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_order_range` | 2.01ms | 1.98ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 2.41ms | 2.50ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 4.88ms | 5.37ms | 1.1× | 1.7% | PASS |
| ac_reads | `select_random_points` | 10.26ms | 10.93ms | 1.1× | 5.4% | PASS |
| ac_reads | `select_random_ranges` | 4.03ms | 4.10ms | 1.0× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 4.83ms | 4.57ms | 0.9× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 17.01ms | 18.00ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 4.98ms | 6.76ms | 1.4× | 5.9% | PASS |
| ac_reads | `index_join_scan` | 2.58ms | 3.81ms | 1.5× | 4.7% | PASS |
| ac_reads | `types_table_scan` | 710.00ms | 732.36ms | 1.0× | 1.8% | PASS |
| ac_reads | `table_scan` | 877.24ms | 821.96ms | 0.9× | 2.3% | PASS |
| ac_reads | `oltp_read_only` | 87.12ms | 85.95ms | 1.0× | 2.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.34ms | 54.90ms | 2.6× | 4.8% | PASS |
| ac_writes | `oltp_insert_ac` | 21.64ms | 61.23ms | 2.8× | 3.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 22.76ms | 71.05ms | 3.1× | 4.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.42ms | 58.86ms | 2.9× | 3.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.03ms | 66.66ms | 3.0× | 4.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 21.56ms | 65.43ms | 3.0× | 3.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 19.99ms | 64.86ms | 3.2× | 7.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 24.91ms | 70.41ms | 2.8× | 4.9% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.52ms | 39.06ms | 1.0× | 4.0% | PASS |
| mem_reads | `oltp_range_select` | 18.09ms | 14.30ms | 0.8× | 2.2% | PASS |
| mem_reads | `oltp_sum_range` | 16.92ms | 14.24ms | 0.8× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 3.37ms | 3.18ms | 0.9× | 1.8% | PASS |
| mem_reads | `oltp_distinct_range` | 4.40ms | 4.30ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 4.06ms | 6.26ms | 1.5× | 2.0% | PASS |
| mem_reads | `select_random_points` | 23.02ms | 21.39ms | 0.9× | 3.3% | PASS |
| mem_reads | `select_random_ranges` | 3.63ms | 5.30ms | 1.5× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.61ms | 1.1× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 34.92ms | 34.77ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 10.63ms | 9.32ms | 0.9× | 4.0% | PASS |
| mem_reads | `index_join_scan` | 3.74ms | 5.35ms | 1.4× | 2.5% | PASS |
| mem_reads | `types_table_scan` | 1.13s | 1.19s | 1.1× | 1.5% | PASS |
| mem_reads | `table_scan` | 1.41s | 1.32s | 0.9× | 2.2% | PASS |
| mem_reads | `oltp_read_only` | 150.90ms | 142.06ms | 0.9× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 245.89ms | 375.43ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 18.36ms | 39.11ms | 2.1× | 1.3% | PASS |
| mem_writes | `oltp_update_index` | 65.88ms | 143.13ms | 2.2× | 2.1% | PASS |
| mem_writes | `oltp_update_non_index` | 49.07ms | 86.55ms | 1.8× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 54.97ms | 105.55ms | 1.9× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 28.40ms | 62.04ms | 2.2× | 1.6% | PASS |
| mem_writes | `types_delete_insert` | 39.36ms | 54.48ms | 1.4× | 2.2% | PASS |
| mem_writes | `oltp_read_write` | 100.19ms | 147.58ms | 1.5× | 3.3% | PASS |
| file_reads | `oltp_point_select` | 112.19ms | 58.35ms | 0.5× | 1.7% | PASS |
| file_reads | `oltp_range_select` | 25.16ms | 16.32ms | 0.6× | 2.7% | PASS |
| file_reads | `oltp_sum_range` | 24.15ms | 16.17ms | 0.7× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 4.27ms | 3.48ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 5.37ms | 4.62ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 11.74ms | 8.52ms | 0.7× | 2.0% | PASS |
| file_reads | `select_random_points` | 31.66ms | 24.51ms | 0.8× | 3.0% | PASS |
| file_reads | `select_random_ranges` | 11.16ms | 7.36ms | 0.7× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 11.92ms | 6.92ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 35.85ms | 35.16ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 15.08ms | 10.97ms | 0.7× | 2.7% | PASS |
| file_reads | `index_join_scan` | 4.84ms | 5.78ms | 1.2× | 3.2% | PASS |
| file_reads | `types_table_scan` | 1.14s | 1.19s | 1.0× | 1.8% | PASS |
| file_reads | `table_scan` | 1.35s | 1.31s | 1.0× | 4.3% | PASS |
| file_reads | `oltp_read_only` | 259.77ms | 170.99ms | 0.7× | 1.9% | PASS |
| file_writes | `oltp_bulk_insert` | 272.50ms | 390.49ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 27.18ms | 46.87ms | 1.7× | 3.9% | PASS |
| file_writes | `oltp_update_index` | 136.95ms | 169.49ms | 1.2× | 5.5% | PASS |
| file_writes | `oltp_update_non_index` | 99.43ms | 102.41ms | 1.0× | 12.1% | PASS |
| file_writes | `oltp_delete_insert` | 98.49ms | 121.06ms | 1.2× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 82.46ms | 74.58ms | 0.9× | 9.4% | PASS |
| file_writes | `types_delete_insert` | 74.86ms | 64.09ms | 0.9× | 2.2% | PASS |
| file_writes | `oltp_read_write` | 164.05ms | 161.69ms | 1.0× | 10.9% | PASS |
| ac_reads | `oltp_point_select` | 62.25ms | 58.34ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 20.22ms | 16.34ms | 0.8× | 2.3% | PASS |
| ac_reads | `oltp_sum_range` | 18.95ms | 16.30ms | 0.9× | 2.3% | PASS |
| ac_reads | `oltp_order_range` | 3.78ms | 3.46ms | 0.9× | 2.4% | PASS |
| ac_reads | `oltp_distinct_range` | 4.82ms | 4.61ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.91ms | 8.49ms | 1.2× | 1.7% | PASS |
| ac_reads | `select_random_points` | 26.64ms | 24.48ms | 0.9× | 3.2% | PASS |
| ac_reads | `select_random_ranges` | 6.31ms | 7.37ms | 1.2× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.17ms | 6.90ms | 1.0× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 35.16ms | 35.04ms | 1.0× | 0.6% | PASS |
| ac_reads | `index_join` | 12.75ms | 10.95ms | 0.9× | 2.8% | PASS |
| ac_reads | `index_join_scan` | 4.31ms | 5.84ms | 1.4× | 3.2% | PASS |
| ac_reads | `types_table_scan` | 1.14s | 1.19s | 1.0× | 1.2% | PASS |
| ac_reads | `table_scan` | 1.33s | 1.30s | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_read_only` | 187.03ms | 171.15ms | 0.9× | 1.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.17ms | 67.57ms | 2.8× | 7.2% | PASS |
| ac_writes | `oltp_insert_ac` | 27.56ms | 77.86ms | 2.8× | 6.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.90ms | 91.94ms | 3.3× | 5.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.85ms | 77.17ms | 3.1× | 7.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.61ms | 84.57ms | 3.2× | 6.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.36ms | 86.00ms | 3.1× | 6.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.84ms | 81.09ms | 3.3× | 11.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.98ms | 94.07ms | 2.8× | 6.8% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.30ms | 39.09ms | 1.0× | 2.9% | PASS |
| mem_reads | `oltp_range_select` | 17.21ms | 14.19ms | 0.8× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 16.17ms | 13.83ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 3.31ms | 3.17ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_distinct_range` | 4.38ms | 4.34ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_index_scan` | 4.11ms | 6.42ms | 1.6× | 2.2% | PASS |
| mem_reads | `select_random_points` | 23.64ms | 21.43ms | 0.9× | 3.8% | PASS |
| mem_reads | `select_random_ranges` | 3.65ms | 5.37ms | 1.5× | 2.7% | PASS |
| mem_reads | `covering_index_scan` | 4.29ms | 4.88ms | 1.1× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 35.89ms | 35.14ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 11.02ms | 9.96ms | 0.9× | 2.8% | PASS |
| mem_reads | `index_join_scan` | 3.89ms | 5.58ms | 1.4× | 2.4% | PASS |
| mem_reads | `types_table_scan` | 1.18s | 1.23s | 1.0× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.35s | 1.33s | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 149.03ms | 142.48ms | 1.0× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 249.43ms | 377.01ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 19.25ms | 39.93ms | 2.1× | 1.3% | PASS |
| mem_writes | `oltp_update_index` | 70.17ms | 146.34ms | 2.1× | 1.7% | PASS |
| mem_writes | `oltp_update_non_index` | 51.65ms | 87.37ms | 1.7× | 2.3% | PASS |
| mem_writes | `oltp_delete_insert` | 56.56ms | 107.54ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 30.69ms | 65.27ms | 2.1× | 2.2% | PASS |
| mem_writes | `types_delete_insert` | 42.59ms | 56.45ms | 1.3× | 2.0% | PASS |
| mem_writes | `oltp_read_write` | 114.73ms | 155.69ms | 1.4× | 2.5% | PASS |
| file_reads | `oltp_point_select` | 117.83ms | 60.21ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 26.40ms | 16.91ms | 0.6× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 24.78ms | 16.29ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_order_range` | 4.46ms | 3.48ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_distinct_range` | 5.53ms | 4.65ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 12.12ms | 8.70ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 33.70ms | 24.95ms | 0.7× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 11.42ms | 7.50ms | 0.7× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 12.27ms | 7.01ms | 0.6× | 0.9% | PASS |
| file_reads | `groupby_scan` | 37.37ms | 35.81ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 16.19ms | 11.53ms | 0.7× | 3.5% | PASS |
| file_reads | `index_join_scan` | 5.03ms | 5.93ms | 1.2× | 3.8% | PASS |
| file_reads | `types_table_scan` | 1.17s | 1.21s | 1.0× | 1.3% | PASS |
| file_reads | `table_scan` | 1.33s | 1.33s | 1.0× | 1.0% | PASS |
| file_reads | `oltp_read_only` | 268.21ms | 173.15ms | 0.6× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 273.05ms | 389.72ms | 1.4× | 1.2% | PASS |
| file_writes | `oltp_insert` | 26.69ms | 46.78ms | 1.8× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 105.96ms | 161.87ms | 1.5× | 2.0% | PASS |
| file_writes | `oltp_update_non_index` | 91.77ms | 101.17ms | 1.1× | 8.8% | PASS |
| file_writes | `oltp_delete_insert` | 93.94ms | 120.33ms | 1.3× | 1.3% | PASS |
| file_writes | `oltp_write_only` | 61.54ms | 74.20ms | 1.2× | 1.8% | PASS |
| file_writes | `types_delete_insert` | 68.04ms | 64.65ms | 1.0× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 142.98ms | 164.04ms | 1.1× | 2.2% | PASS |
| ac_reads | `oltp_point_select` | 63.10ms | 59.91ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_range_select` | 20.16ms | 16.63ms | 0.8× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 18.98ms | 16.27ms | 0.9× | 2.4% | PASS |
| ac_reads | `oltp_order_range` | 3.87ms | 3.47ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_distinct_range` | 4.94ms | 4.66ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_index_scan` | 7.02ms | 8.69ms | 1.2× | 1.5% | PASS |
| ac_reads | `select_random_points` | 27.34ms | 24.89ms | 0.9× | 3.4% | PASS |
| ac_reads | `select_random_ranges` | 6.30ms | 7.45ms | 1.2× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 7.30ms | 7.02ms | 1.0× | 2.3% | PASS |
| ac_reads | `groupby_scan` | 36.47ms | 35.60ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 13.29ms | 11.53ms | 0.9× | 3.8% | PASS |
| ac_reads | `index_join_scan` | 4.42ms | 5.97ms | 1.4× | 3.2% | PASS |
| ac_reads | `types_table_scan` | 1.20s | 1.22s | 1.0× | 1.2% | PASS |
| ac_reads | `table_scan` | 1.38s | 1.35s | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 190.52ms | 174.69ms | 0.9× | 1.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 25.13ms | 66.34ms | 2.6× | 4.9% | PASS |
| ac_writes | `oltp_insert_ac` | 27.38ms | 84.44ms | 3.1× | 5.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.71ms | 94.41ms | 3.2× | 5.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.89ms | 76.65ms | 3.1× | 6.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.86ms | 85.70ms | 3.1× | 5.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.88ms | 86.16ms | 3.1× | 5.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.54ms | 84.81ms | 3.3× | 9.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.77ms | 94.22ms | 2.7× | 5.9% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.09ms | 41.24ms | 1.3× | 0.8% | PASS |
| mem_reads | `oltp_range_select` | 18.88ms | 21.10ms | 1.1× | 1.9% | PASS |
| mem_reads | `oltp_sum_range` | 17.78ms | 20.52ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 3.65ms | 3.85ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_distinct_range` | 4.78ms | 5.02ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 4.52ms | 6.14ms | 1.4× | 1.2% | PASS |
| mem_reads | `select_random_points` | 27.45ms | 32.69ms | 1.2× | 1.5% | PASS |
| mem_reads | `select_random_ranges` | 7.75ms | 9.08ms | 1.2× | 0.8% | PASS |
| mem_reads | `covering_index_scan` | 4.23ms | 4.57ms | 1.1× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 36.29ms | 39.66ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 7.90ms | 10.35ms | 1.3× | 1.0% | PASS |
| mem_reads | `index_join_scan` | 3.85ms | 5.62ms | 1.5× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_read_only` | 150.22ms | 171.67ms | 1.1× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 251.97ms | 357.76ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 19.23ms | 35.56ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 66.75ms | 130.82ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 50.07ms | 83.83ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 49.12ms | 95.50ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 26.16ms | 57.34ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 32.91ms | 53.81ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 100.33ms | 162.24ms | 1.6× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 109.21ms | 61.25ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 27.20ms | 23.28ms | 0.9× | 2.4% | PASS |
| file_reads | `oltp_sum_range` | 25.29ms | 22.83ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_order_range` | 4.45ms | 4.12ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_distinct_range` | 5.62ms | 5.34ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 12.23ms | 8.44ms | 0.7× | 2.1% | PASS |
| file_reads | `select_random_points` | 36.95ms | 36.25ms | 1.0× | 1.7% | PASS |
| file_reads | `select_random_ranges` | 15.55ms | 11.19ms | 0.7× | 1.6% | PASS |
| file_reads | `covering_index_scan` | 11.78ms | 6.68ms | 0.6× | 1.4% | PASS |
| file_reads | `groupby_scan` | 37.16ms | 39.95ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 12.21ms | 12.36ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join_scan` | 4.82ms | 5.97ms | 1.2× | 2.2% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.27s | 1.29s | 1.0× | 4.0% | PASS |
| file_reads | `oltp_read_only` | 262.02ms | 201.48ms | 0.8× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 266.80ms | 365.24ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 25.94ms | 40.87ms | 1.6× | 1.4% | PASS |
| file_writes | `oltp_update_index` | 95.37ms | 139.29ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 76.83ms | 97.71ms | 1.3× | 1.5% | PASS |
| file_writes | `oltp_delete_insert` | 75.80ms | 108.02ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 50.00ms | 69.27ms | 1.4× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 49.20ms | 59.71ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 127.78ms | 174.31ms | 1.4× | 1.8% | PASS |
| ac_reads | `oltp_point_select` | 58.11ms | 61.50ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 21.93ms | 23.30ms | 1.1× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 20.31ms | 22.80ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 4.03ms | 4.17ms | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_distinct_range` | 5.13ms | 5.39ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 7.48ms | 8.73ms | 1.2× | 2.8% | PASS |
| ac_reads | `select_random_points` | 34.96ms | 39.65ms | 1.1× | 1.9% | PASS |
| ac_reads | `select_random_ranges` | 10.74ms | 11.42ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 7.16ms | 6.80ms | 1.0× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 37.11ms | 40.09ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 9.82ms | 12.55ms | 1.3× | 2.0% | PASS |
| ac_reads | `index_join_scan` | 4.46ms | 6.03ms | 1.4× | 2.9% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.22s | 1.0× | 2.0% | PASS |
| ac_reads | `table_scan` | 1.40s | 1.33s | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 198.20ms | 205.99ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.61ms | 62.44ms | 2.8× | 6.5% | PASS |
| ac_writes | `oltp_insert_ac` | 25.50ms | 80.56ms | 3.2× | 7.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.59ms | 90.11ms | 3.3× | 8.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.08ms | 73.03ms | 3.2× | 5.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.18ms | 84.67ms | 3.2× | 8.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.83ms | 83.11ms | 3.2× | 7.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.07ms | 72.22ms | 3.3× | 5.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.12ms | 91.94ms | 2.8× | 6.9% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 56s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 71.89ms | 130.00ms | 55.3% | 0.5% | PASS |
| `status_dirty_many_tables` | 74.07ms | 130.00ms | 57.0% | 0.3% | PASS |
| `diff_regular_working_one_table` | 67.36ms | 120.00ms | 56.1% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 77.94ms | 140.00ms | 55.7% | 0.2% | PASS |
| `diff_stat_working_many_tables` | 78.07ms | 140.00ms | 55.8% | 0.2% | PASS |
| `diff_schema_working_many_tables` | 78.91ms | 140.00ms | 56.4% | 0.3% | PASS |
| `branch_list_many_branches` | 18.86ms | 35.00ms | 53.9% | 2.1% | PASS |
| `branch_create_delete` | 22.41ms | 40.00ms | 56.0% | 10.9% | PASS |
| `checkout_branch_clean` | 95.88ms | 150.00ms | 63.9% | 3.2% | PASS |
| `merge_data_no_conflicts` | 31.29ms | 50.00ms | 62.6% | 2.2% | PASS |
| `merge_schema_no_conflicts` | 18.57ms | 35.00ms | 53.0% | 4.7% | PASS |
| `merge_data_conflicts` | 25.88ms | 180.00ms | 14.4% | 0.8% | PASS |
| `merge_data_conflicts_with_resolve` | 26.61ms | 180.00ms | 14.8% | 1.7% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
