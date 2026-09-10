# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-10 15:21 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/34484554644)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.48s | 10.81s | 1.0× | 1.5% | **PASS** |
| Writes | 2.10s | 3.42s | 1.6× | 1.3% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.40s | 11.02s | 1.0× | 1.3% | **PASS** |
| Writes | 3.38s | 4.02s | 1.2× | 1.5% | **PASS** |
| Autocommit writes | 848.62ms | 2.89s | 3.4× | 6.6% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.60s | 2.74s | 1.1× | 1.2% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 3.01s | 2.97s | 1.0× | 1.8% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.74s | 2.81s | 1.0× | 1.8% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.13s | 2.29s | 1.1× | 1.5% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 446.07ms | 724.58ms | 1.6× | 1.4% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 618.43ms | 1.01s | 1.6× | 1.5% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 613.71ms | 1.02s | 1.7× | 1.6% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 423.41ms | 664.57ms | 1.6× | 1.1% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.90s | 2.81s | 1.0× | 1.1% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.30s | 3.04s | 0.9× | 1.1% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.98s | 2.87s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.22s | 2.31s | 1.0× | 1.4% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 588.34ms | 772.28ms | 1.3× | 1.3% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.01s | 1.12s | 1.1× | 3.5% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 810.81ms | 1.08s | 1.3× | 1.5% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 977.94ms | 1.05s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.71s | 2.81s | 1.0× | 1.1% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 3.29s | 3.10s | 0.9× | 1.8% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.92s | 2.92s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.21s | 2.36s | 1.1× | 1.6% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 153.63ms | 607.52ms | 4.0× | 6.5% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 173.70ms | 634.68ms | 3.7× | 6.4% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 211.49ms | 724.56ms | 3.4× | 4.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 309.80ms | 926.33ms | 3.0× | 19.4% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.67ms | 28.32ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 10.91ms | 11.11ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_sum_range` | 9.93ms | 11.01ms | 1.1× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 2.70ms | 2.86ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.70ms | 3.95ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.81ms | 4.68ms | 1.2× | 1.2% | PASS |
| mem_reads | `select_random_points` | 10.05ms | 10.70ms | 1.1× | 1.5% | PASS |
| mem_reads | `select_random_ranges` | 2.97ms | 3.89ms | 1.3× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.26ms | 4.11ms | 1.0× | 1.0% | PASS |
| mem_reads | `groupby_scan` | 32.02ms | 34.86ms | 1.1× | 1.2% | PASS |
| mem_reads | `index_join` | 5.72ms | 7.52ms | 1.3× | 1.2% | PASS |
| mem_reads | `index_join_scan` | 3.33ms | 4.72ms | 1.4× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 1.13s | 1.19s | 1.1× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.26s | 1.30s | 1.0× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 102.14ms | 113.97ms | 1.1× | 0.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 182.87ms | 264.12ms | 1.4× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 15.80ms | 28.43ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_update_index` | 49.87ms | 96.19ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 35.78ms | 59.53ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 46.44ms | 76.13ms | 1.6× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 23.29ms | 50.37ms | 2.2× | 2.3% | PASS |
| mem_writes | `types_delete_insert` | 24.16ms | 37.80ms | 1.6× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 67.86ms | 112.00ms | 1.7× | 2.3% | PASS |
| file_reads | `oltp_point_select` | 121.45ms | 51.60ms | 0.4× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 21.22ms | 13.64ms | 0.6× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 19.78ms | 13.41ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 3.71ms | 3.17ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.75ms | 4.26ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 13.40ms | 7.45ms | 0.6× | 1.2% | PASS |
| file_reads | `select_random_points` | 19.33ms | 13.63ms | 0.7× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 12.42ms | 6.31ms | 0.5× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 13.97ms | 6.64ms | 0.5× | 1.4% | PASS |
| file_reads | `groupby_scan` | 32.90ms | 35.17ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 10.81ms | 9.10ms | 0.8× | 1.1% | PASS |
| file_reads | `index_join_scan` | 4.29ms | 5.09ms | 1.2× | 1.6% | PASS |
| file_reads | `types_table_scan` | 1.12s | 1.19s | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 1.26s | 1.30s | 1.0× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 239.20ms | 147.51ms | 0.6× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 195.17ms | 272.47ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 21.83ms | 32.00ms | 1.5× | 1.3% | PASS |
| file_writes | `oltp_update_index` | 76.86ms | 104.73ms | 1.4× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 58.94ms | 69.96ms | 1.2× | 1.3% | PASS |
| file_writes | `oltp_delete_insert` | 66.90ms | 80.73ms | 1.2× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 43.20ms | 54.27ms | 1.3× | 1.8% | PASS |
| file_writes | `types_delete_insert` | 39.34ms | 43.25ms | 1.1× | 1.3% | PASS |
| file_writes | `oltp_read_write` | 86.09ms | 114.86ms | 1.3× | 1.8% | PASS |
| ac_reads | `oltp_point_select` | 54.82ms | 50.96ms | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_range_select` | 13.72ms | 13.55ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 12.61ms | 13.28ms | 1.1× | 0.8% | PASS |
| ac_reads | `oltp_order_range` | 3.00ms | 3.13ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 4.02ms | 4.24ms | 1.1× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 7.10ms | 7.29ms | 1.0× | 1.1% | PASS |
| ac_reads | `select_random_points` | 13.27ms | 13.47ms | 1.0× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 6.08ms | 6.30ms | 1.0× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 7.58ms | 6.59ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 32.24ms | 35.16ms | 1.1× | 1.2% | PASS |
| ac_reads | `index_join` | 7.60ms | 9.24ms | 1.2× | 0.9% | PASS |
| ac_reads | `index_join_scan` | 3.75ms | 5.03ms | 1.3× | 1.2% | PASS |
| ac_reads | `types_table_scan` | 1.12s | 1.18s | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.27s | 1.31s | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 151.35ms | 147.77ms | 1.0× | 2.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.30ms | 65.01ms | 3.8× | 6.2% | PASS |
| ac_writes | `oltp_insert_ac` | 20.39ms | 80.19ms | 3.9× | 8.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.25ms | 89.07ms | 4.2× | 7.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.19ms | 67.60ms | 3.9× | 5.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 17.96ms | 77.26ms | 4.3× | 7.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.05ms | 76.57ms | 4.0× | 6.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 15.28ms | 63.62ms | 4.2× | 3.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.21ms | 88.20ms | 3.5× | 5.7% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.54ms | 36.07ms | 1.0× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 18.24ms | 13.94ms | 0.8× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 16.73ms | 13.89ms | 0.8× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 3.59ms | 3.30ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.64ms | 4.43ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 4.20ms | 6.29ms | 1.5× | 1.8% | PASS |
| mem_reads | `select_random_points` | 24.56ms | 21.28ms | 0.9× | 3.3% | PASS |
| mem_reads | `select_random_ranges` | 3.79ms | 5.49ms | 1.4× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.30ms | 4.70ms | 1.1× | 2.4% | PASS |
| mem_reads | `groupby_scan` | 37.40ms | 37.04ms | 1.0× | 1.4% | PASS |
| mem_reads | `index_join` | 11.54ms | 9.39ms | 0.8× | 3.4% | PASS |
| mem_reads | `index_join_scan` | 3.98ms | 6.09ms | 1.5× | 1.4% | PASS |
| mem_reads | `types_table_scan` | 1.19s | 1.26s | 1.1× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.50s | 1.42s | 0.9× | 4.1% | PASS |
| mem_reads | `oltp_read_only` | 149.31ms | 135.21ms | 0.9× | 2.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 245.80ms | 355.49ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 18.66ms | 39.47ms | 2.1× | 1.5% | PASS |
| mem_writes | `oltp_update_index` | 70.17ms | 150.95ms | 2.2× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 53.06ms | 88.60ms | 1.7× | 2.1% | PASS |
| mem_writes | `oltp_delete_insert` | 58.22ms | 110.73ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 30.04ms | 66.18ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 40.56ms | 54.38ms | 1.3× | 1.1% | PASS |
| mem_writes | `oltp_read_write` | 101.93ms | 145.93ms | 1.4× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 135.16ms | 59.09ms | 0.4× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 28.36ms | 16.34ms | 0.6× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 26.90ms | 16.38ms | 0.6× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 4.63ms | 3.57ms | 0.8× | 0.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.73ms | 4.73ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 14.10ms | 8.73ms | 0.6× | 0.6% | PASS |
| file_reads | `select_random_points` | 34.72ms | 23.86ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 13.48ms | 7.79ms | 0.6× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 14.39ms | 7.34ms | 0.5× | 0.8% | PASS |
| file_reads | `groupby_scan` | 38.74ms | 37.68ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 17.15ms | 11.28ms | 0.7× | 2.0% | PASS |
| file_reads | `index_join_scan` | 5.17ms | 6.54ms | 1.3× | 1.7% | PASS |
| file_reads | `types_table_scan` | 1.23s | 1.26s | 1.0× | 2.2% | PASS |
| file_reads | `table_scan` | 1.45s | 1.41s | 1.0× | 3.0% | PASS |
| file_reads | `oltp_read_only` | 285.72ms | 167.56ms | 0.6× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 273.25ms | 370.88ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_insert` | 26.56ms | 46.85ms | 1.8× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 137.42ms | 169.20ms | 1.2× | 4.9% | PASS |
| file_writes | `oltp_update_non_index` | 122.12ms | 103.33ms | 0.8× | 8.3% | PASS |
| file_writes | `oltp_delete_insert` | 102.19ms | 125.59ms | 1.2× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 87.39ms | 79.01ms | 0.9× | 11.9% | PASS |
| file_writes | `types_delete_insert` | 78.90ms | 64.86ms | 0.8× | 2.0% | PASS |
| file_writes | `oltp_read_write` | 177.49ms | 161.49ms | 0.9× | 5.7% | PASS |
| ac_reads | `oltp_point_select` | 71.38ms | 59.96ms | 0.8× | 2.4% | PASS |
| ac_reads | `oltp_range_select` | 22.49ms | 16.43ms | 0.7× | 2.3% | PASS |
| ac_reads | `oltp_sum_range` | 20.12ms | 16.23ms | 0.8× | 1.8% | PASS |
| ac_reads | `oltp_order_range` | 4.04ms | 3.57ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_distinct_range` | 5.10ms | 4.72ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 7.84ms | 8.84ms | 1.1× | 0.9% | PASS |
| ac_reads | `select_random_points` | 30.44ms | 24.61ms | 0.8× | 2.7% | PASS |
| ac_reads | `select_random_ranges` | 7.15ms | 7.92ms | 1.1× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 7.99ms | 7.39ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 38.08ms | 37.61ms | 1.0× | 1.7% | PASS |
| ac_reads | `index_join` | 14.28ms | 11.49ms | 0.8× | 2.3% | PASS |
| ac_reads | `index_join_scan` | 4.73ms | 6.80ms | 1.4× | 2.8% | PASS |
| ac_reads | `types_table_scan` | 1.30s | 1.29s | 1.0× | 2.4% | PASS |
| ac_reads | `table_scan` | 1.56s | 1.44s | 0.9× | 2.8% | PASS |
| ac_reads | `oltp_read_only` | 193.92ms | 168.29ms | 0.9× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.93ms | 61.98ms | 3.5× | 4.9% | PASS |
| ac_writes | `oltp_insert_ac` | 21.00ms | 74.06ms | 3.5× | 5.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 22.36ms | 90.34ms | 4.0× | 7.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 19.70ms | 73.55ms | 3.7× | 6.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.43ms | 83.64ms | 3.7× | 6.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.04ms | 85.14ms | 3.9× | 7.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.32ms | 78.43ms | 3.9× | 7.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.91ms | 87.53ms | 3.1× | 5.1% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.17ms | 38.59ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_range_select` | 16.53ms | 14.53ms | 0.9× | 3.5% | PASS |
| mem_reads | `oltp_sum_range` | 15.39ms | 13.70ms | 0.9× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 3.23ms | 3.14ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.36ms | 4.34ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.15ms | 6.44ms | 1.6× | 1.8% | PASS |
| mem_reads | `select_random_points` | 22.95ms | 21.59ms | 0.9× | 2.9% | PASS |
| mem_reads | `select_random_ranges` | 3.62ms | 5.35ms | 1.5× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.27ms | 4.88ms | 1.1× | 1.8% | PASS |
| mem_reads | `groupby_scan` | 35.02ms | 34.84ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 10.98ms | 9.94ms | 0.9× | 2.9% | PASS |
| mem_reads | `index_join_scan` | 3.81ms | 5.51ms | 1.4× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 1.13s | 1.19s | 1.1× | 1.8% | PASS |
| mem_reads | `table_scan` | 1.30s | 1.32s | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_read_only` | 143.42ms | 140.43ms | 1.0× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 249.01ms | 372.40ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 19.03ms | 39.33ms | 2.1× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 66.98ms | 143.94ms | 2.1× | 1.7% | PASS |
| mem_writes | `oltp_update_non_index` | 50.38ms | 86.90ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 54.70ms | 106.14ms | 1.9× | 1.7% | PASS |
| mem_writes | `oltp_write_only` | 29.92ms | 64.46ms | 2.2× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 39.80ms | 55.35ms | 1.4× | 1.9% | PASS |
| mem_writes | `oltp_read_write` | 103.89ms | 149.73ms | 1.4× | 1.5% | PASS |
| file_reads | `oltp_point_select` | 115.51ms | 59.83ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 25.32ms | 16.99ms | 0.7× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 23.91ms | 16.18ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 4.37ms | 3.47ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 5.48ms | 4.67ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_index_scan` | 11.99ms | 8.64ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 33.16ms | 24.57ms | 0.7× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 11.34ms | 7.45ms | 0.7× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 12.26ms | 6.97ms | 0.6× | 1.4% | PASS |
| file_reads | `groupby_scan` | 36.13ms | 35.25ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 15.91ms | 11.22ms | 0.7× | 2.1% | PASS |
| file_reads | `index_join_scan` | 4.94ms | 5.99ms | 1.2× | 2.9% | PASS |
| file_reads | `types_table_scan` | 1.14s | 1.20s | 1.0× | 2.1% | PASS |
| file_reads | `table_scan` | 1.28s | 1.31s | 1.0× | 1.5% | PASS |
| file_reads | `oltp_read_only` | 255.94ms | 169.62ms | 0.7× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 268.97ms | 380.29ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 25.82ms | 45.54ms | 1.8× | 1.4% | PASS |
| file_writes | `oltp_update_index` | 94.53ms | 151.74ms | 1.6× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 88.17ms | 96.47ms | 1.1× | 9.8% | PASS |
| file_writes | `oltp_delete_insert` | 84.58ms | 114.75ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_write_only` | 55.70ms | 70.60ms | 1.3× | 1.5% | PASS |
| file_writes | `types_delete_insert` | 62.58ms | 61.61ms | 1.0× | 2.0% | PASS |
| file_writes | `oltp_read_write` | 130.46ms | 156.65ms | 1.2× | 2.3% | PASS |
| ac_reads | `oltp_point_select` | 61.31ms | 58.73ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_range_select` | 19.05ms | 16.81ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 18.32ms | 15.91ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_order_range` | 3.80ms | 3.43ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 4.90ms | 4.67ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 6.89ms | 8.54ms | 1.2× | 1.8% | PASS |
| ac_reads | `select_random_points` | 25.36ms | 23.94ms | 0.9× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 6.22ms | 7.38ms | 1.2× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.18ms | 6.92ms | 1.0× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 35.12ms | 35.05ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 12.59ms | 11.06ms | 0.9× | 2.7% | PASS |
| ac_reads | `index_join_scan` | 4.36ms | 5.92ms | 1.4× | 1.7% | PASS |
| ac_reads | `types_table_scan` | 1.19s | 1.22s | 1.0× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.34s | 1.33s | 1.0× | 0.6% | PASS |
| ac_reads | `oltp_read_only` | 190.94ms | 174.76ms | 0.9× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.76ms | 75.97ms | 3.2× | 4.7% | PASS |
| ac_writes | `oltp_insert_ac` | 26.89ms | 92.61ms | 3.4× | 6.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.44ms | 102.13ms | 3.6× | 5.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.80ms | 85.48ms | 3.6× | 5.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.01ms | 92.10ms | 3.5× | 4.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.94ms | 92.16ms | 3.6× | 4.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.10ms | 86.23ms | 3.6× | 6.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.56ms | 97.87ms | 3.0× | 4.4% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.72ms | 28.03ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_range_select` | 14.79ms | 15.89ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 14.13ms | 15.50ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 2.93ms | 3.03ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_distinct_range` | 3.69ms | 3.91ms | 1.1× | 2.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.56ms | 4.32ms | 1.2× | 2.0% | PASS |
| mem_reads | `select_random_points` | 22.41ms | 25.24ms | 1.1× | 1.5% | PASS |
| mem_reads | `select_random_ranges` | 5.62ms | 6.45ms | 1.1× | 2.0% | PASS |
| mem_reads | `covering_index_scan` | 3.02ms | 3.06ms | 1.0× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 29.75ms | 32.09ms | 1.1× | 1.2% | PASS |
| mem_reads | `index_join` | 6.24ms | 8.09ms | 1.3× | 1.8% | PASS |
| mem_reads | `index_join_scan` | 3.04ms | 4.52ms | 1.5× | 1.5% | PASS |
| mem_reads | `types_table_scan` | 881.02ms | 961.12ms | 1.1× | 0.3% | PASS |
| mem_reads | `table_scan` | 1.01s | 1.05s | 1.0× | 0.3% | PASS |
| mem_reads | `oltp_read_only` | 111.12ms | 124.30ms | 1.1× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 174.09ms | 236.05ms | 1.4× | 0.6% | PASS |
| mem_writes | `oltp_insert` | 14.21ms | 24.39ms | 1.7× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 49.26ms | 91.87ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 36.01ms | 58.51ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 36.51ms | 67.18ms | 1.8× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 19.55ms | 40.22ms | 2.1× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 22.82ms | 36.28ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_read_write` | 70.97ms | 110.06ms | 1.6× | 0.8% | PASS |
| file_reads | `oltp_point_select` | 50.62ms | 34.30ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 17.79ms | 17.15ms | 1.0× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 17.06ms | 16.49ms | 1.0× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 3.29ms | 3.15ms | 1.0× | 2.5% | PASS |
| file_reads | `oltp_distinct_range` | 4.09ms | 4.02ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 6.43ms | 5.39ms | 0.8× | 2.7% | PASS |
| file_reads | `select_random_points` | 25.77ms | 26.42ms | 1.0× | 1.4% | PASS |
| file_reads | `select_random_ranges` | 8.60ms | 7.33ms | 0.9× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 5.84ms | 3.99ms | 0.7× | 3.1% | PASS |
| file_reads | `groupby_scan` | 29.98ms | 31.63ms | 1.1× | 1.2% | PASS |
| file_reads | `index_join` | 7.86ms | 9.04ms | 1.2× | 2.4% | PASS |
| file_reads | `index_join_scan` | 3.44ms | 4.80ms | 1.4× | 1.8% | PASS |
| file_reads | `types_table_scan` | 880.48ms | 957.36ms | 1.1× | 0.2% | PASS |
| file_reads | `table_scan` | 1.01s | 1.05s | 1.0× | 0.2% | PASS |
| file_reads | `oltp_read_only` | 148.86ms | 133.53ms | 0.9× | 0.6% | PASS |
| file_writes | `oltp_bulk_insert` | 227.54ms | 291.90ms | 1.3× | 2.4% | PASS |
| file_writes | `oltp_insert` | 28.39ms | 43.74ms | 1.5× | 3.1% | PASS |
| file_writes | `oltp_update_index` | 151.88ms | 156.68ms | 1.0× | 1.1% | PASS |
| file_writes | `oltp_update_non_index` | 127.15ms | 111.64ms | 0.9× | 0.9% | PASS |
| file_writes | `oltp_delete_insert` | 125.92ms | 124.55ms | 1.0× | 1.2% | PASS |
| file_writes | `oltp_write_only` | 91.31ms | 87.85ms | 1.0× | 1.1% | PASS |
| file_writes | `types_delete_insert` | 83.44ms | 71.27ms | 0.9× | 6.1% | PASS |
| file_writes | `oltp_read_write` | 142.30ms | 158.41ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_point_select` | 32.62ms | 34.30ms | 1.1× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 15.87ms | 17.10ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 15.32ms | 16.44ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.12ms | 3.16ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 3.90ms | 4.00ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_index_scan` | 4.71ms | 5.29ms | 1.1× | 2.5% | PASS |
| ac_reads | `select_random_points` | 24.02ms | 26.48ms | 1.1× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 6.76ms | 7.28ms | 1.1× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 4.07ms | 3.93ms | 1.0× | 2.7% | PASS |
| ac_reads | `groupby_scan` | 29.76ms | 31.57ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 7.02ms | 9.04ms | 1.3× | 2.4% | PASS |
| ac_reads | `index_join_scan` | 3.32ms | 4.79ms | 1.4× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 901.71ms | 983.36ms | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 1.03s | 1.08s | 1.0× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 126.19ms | 137.36ms | 1.1× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 35.83ms | 96.15ms | 2.7× | 14.6% | PASS |
| ac_writes | `oltp_insert_ac` | 42.25ms | 121.27ms | 2.9× | 31.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 37.82ms | 124.59ms | 3.3× | 14.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 40.02ms | 116.13ms | 2.9× | 16.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 36.94ms | 121.78ms | 3.3× | 30.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 37.06ms | 104.01ms | 2.8× | 12.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 36.14ms | 117.76ms | 3.3× | 28.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 43.73ms | 124.65ms | 2.9× | 22.4% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 12s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 94.69ms | 130.00ms | 72.8% | 0.5% | PASS |
| `status_dirty_many_tables` | 98.48ms | 130.00ms | 75.8% | 0.4% | PASS |
| `diff_regular_working_one_table` | 90.34ms | 120.00ms | 75.3% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 103.55ms | 140.00ms | 74.0% | 0.5% | PASS |
| `diff_stat_working_many_tables` | 103.69ms | 140.00ms | 74.1% | 0.5% | PASS |
| `diff_schema_working_many_tables` | 104.83ms | 140.00ms | 74.9% | 0.4% | PASS |
| `branch_list_many_branches` | 22.81ms | 35.00ms | 65.2% | 1.5% | PASS |
| `branch_create_delete` | 25.35ms | 40.00ms | 63.4% | 1.0% | PASS |
| `checkout_branch_clean` | 61.40ms | 150.00ms | 40.9% | 1.0% | PASS |
| `merge_data_no_conflicts` | 29.53ms | 50.00ms | 59.1% | 2.3% | PASS |
| `merge_schema_no_conflicts` | 22.02ms | 35.00ms | 62.9% | 1.4% | PASS |
| `merge_data_conflicts` | 32.76ms | 180.00ms | 18.2% | 1.6% | PASS |
| `merge_data_conflicts_with_resolve` | 33.03ms | 180.00ms | 18.3% | 1.2% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
