# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-10-20 16:02 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261012.334.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/39439064727)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.04s | 9.29s | 1.0× | 2.2% | **PASS** |
| Writes | 1.93s | 3.27s | 1.7× | 2.0% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.04s | 9.56s | 1.0× | 2.1% | **PASS** |
| Writes | 3.57s | 4.13s | 1.2× | 3.3% | **PASS** |
| Autocommit writes | 790.47ms | 2.25s | 2.8× | 5.0% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 1.69s | 1.74s | 1.0× | 2.3% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.77s | 2.80s | 1.0× | 1.9% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 1.85s | 1.88s | 1.0× | 2.4% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.74s | 2.88s | 1.1× | 1.9% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 288.32ms | 497.19ms | 1.7× | 1.8% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 617.18ms | 1.03s | 1.7× | 1.9% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 410.57ms | 701.97ms | 1.7× | 3.0% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 616.64ms | 1.04s | 1.7× | 1.7% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 1.83s | 1.76s | 1.0× | 2.2% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.02s | 2.86s | 0.9× | 2.2% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.04s | 1.94s | 1.0× | 2.6% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.15s | 2.99s | 1.0× | 1.5% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 732.05ms | 753.77ms | 1.0× | 4.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 991.35ms | 1.14s | 1.1× | 4.0% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.04s | 1.11s | 1.1× | 5.4% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 800.38ms | 1.13s | 1.4× | 1.6% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 1.71s | 1.76s | 1.0× | 2.2% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 3.04s | 2.92s | 1.0× | 1.7% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.09s | 2.07s | 1.0× | 2.1% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.98s | 2.98s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 175.65ms | 495.46ms | 2.8× | 3.9% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 220.06ms | 644.03ms | 2.9× | 7.3% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 204.03ms | 504.45ms | 2.5× | 6.5% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 190.74ms | 603.60ms | 3.2× | 4.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 16.45ms | 19.15ms | 1.2× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 6.50ms | 6.86ms | 1.1× | 2.7% | PASS |
| mem_reads | `oltp_sum_range` | 6.08ms | 6.89ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 1.71ms | 1.79ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 2.22ms | 2.35ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_index_scan` | 2.58ms | 3.31ms | 1.3× | 4.6% | PASS |
| mem_reads | `select_random_points` | 7.94ms | 8.49ms | 1.1× | 3.6% | PASS |
| mem_reads | `select_random_ranges` | 2.03ms | 2.57ms | 1.3× | 2.2% | PASS |
| mem_reads | `covering_index_scan` | 2.47ms | 2.48ms | 1.0× | 1.3% | PASS |
| mem_reads | `groupby_scan` | 17.50ms | 18.78ms | 1.1× | 1.3% | PASS |
| mem_reads | `index_join` | 3.64ms | 4.87ms | 1.3× | 4.5% | PASS |
| mem_reads | `index_join_scan` | 2.42ms | 3.78ms | 1.6× | 3.8% | PASS |
| mem_reads | `types_table_scan` | 722.25ms | 753.27ms | 1.0× | 1.3% | PASS |
| mem_reads | `table_scan` | 832.79ms | 837.98ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_read_only` | 59.71ms | 66.33ms | 1.1× | 2.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 110.58ms | 169.93ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 9.62ms | 18.35ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 34.02ms | 68.78ms | 2.0× | 2.8% | PASS |
| mem_writes | `oltp_update_non_index` | 25.06ms | 43.76ms | 1.7× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 31.81ms | 56.99ms | 1.8× | 1.7% | PASS |
| mem_writes | `oltp_write_only` | 15.95ms | 37.14ms | 2.3× | 1.5% | FAIL |
| mem_writes | `types_delete_insert` | 17.82ms | 28.65ms | 1.6× | 1.9% | PASS |
| mem_writes | `oltp_read_write` | 43.44ms | 73.60ms | 1.7× | 3.7% | PASS |
| file_reads | `oltp_point_select` | 70.64ms | 33.61ms | 0.5× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 12.53ms | 8.39ms | 0.7× | 2.2% | PASS |
| file_reads | `oltp_sum_range` | 12.30ms | 8.55ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 2.38ms | 1.99ms | 0.8× | 2.8% | PASS |
| file_reads | `oltp_distinct_range` | 2.78ms | 2.47ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 8.05ms | 4.67ms | 0.6× | 3.6% | PASS |
| file_reads | `select_random_points` | 12.81ms | 9.33ms | 0.7× | 2.4% | PASS |
| file_reads | `select_random_ranges` | 7.21ms | 3.85ms | 0.5× | 2.3% | PASS |
| file_reads | `covering_index_scan` | 8.18ms | 3.91ms | 0.5× | 2.3% | PASS |
| file_reads | `groupby_scan` | 17.93ms | 18.70ms | 1.0× | 1.1% | PASS |
| file_reads | `index_join` | 6.79ms | 6.18ms | 0.9× | 6.4% | PASS |
| file_reads | `index_join_scan` | 3.08ms | 3.90ms | 1.3× | 3.7% | PASS |
| file_reads | `types_table_scan` | 723.11ms | 745.88ms | 1.0× | 1.3% | PASS |
| file_reads | `table_scan` | 805.47ms | 821.28ms | 1.0× | 1.7% | PASS |
| file_reads | `oltp_read_only` | 135.60ms | 85.42ms | 0.6× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 148.79ms | 208.26ms | 1.4× | 4.1% | PASS |
| file_writes | `oltp_insert` | 19.80ms | 28.13ms | 1.4× | 0.8% | PASS |
| file_writes | `oltp_update_index` | 122.90ms | 118.31ms | 1.0× | 4.9% | PASS |
| file_writes | `oltp_update_non_index` | 100.94ms | 78.50ms | 0.8× | 2.7% | PASS |
| file_writes | `oltp_delete_insert` | 106.65ms | 98.23ms | 0.9× | 5.9% | PASS |
| file_writes | `oltp_write_only` | 71.26ms | 68.19ms | 1.0× | 3.1% | PASS |
| file_writes | `types_delete_insert` | 60.72ms | 49.83ms | 0.8× | 6.7% | PASS |
| file_writes | `oltp_read_write` | 101.00ms | 104.33ms | 1.0× | 6.9% | PASS |
| ac_reads | `oltp_point_select` | 34.01ms | 33.26ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_range_select` | 9.05ms | 8.53ms | 0.9× | 2.5% | PASS |
| ac_reads | `oltp_sum_range` | 8.42ms | 8.47ms | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_order_range` | 1.98ms | 1.94ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 2.47ms | 2.46ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 4.55ms | 4.84ms | 1.1× | 2.6% | PASS |
| ac_reads | `select_random_points` | 10.05ms | 10.39ms | 1.0× | 4.1% | PASS |
| ac_reads | `select_random_ranges` | 4.03ms | 4.09ms | 1.0× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 4.93ms | 4.55ms | 0.9× | 2.6% | PASS |
| ac_reads | `groupby_scan` | 18.04ms | 19.05ms | 1.1× | 1.7% | PASS |
| ac_reads | `index_join` | 5.22ms | 6.57ms | 1.3× | 4.2% | PASS |
| ac_reads | `index_join_scan` | 2.57ms | 3.85ms | 1.5× | 5.2% | PASS |
| ac_reads | `types_table_scan` | 706.56ms | 733.74ms | 1.0× | 1.0% | PASS |
| ac_reads | `table_scan` | 810.14ms | 826.46ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_read_only` | 90.13ms | 89.51ms | 1.0× | 2.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.33ms | 51.05ms | 2.5× | 4.4% | PASS |
| ac_writes | `oltp_insert_ac` | 22.15ms | 60.18ms | 2.7× | 2.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 23.34ms | 69.13ms | 3.0× | 4.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.16ms | 55.62ms | 2.8× | 3.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 21.66ms | 63.98ms | 3.0× | 4.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.21ms | 63.21ms | 2.8× | 3.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.36ms | 63.73ms | 3.1× | 3.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.43ms | 68.55ms | 2.7× | 3.9% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.92ms | 38.92ms | 1.0× | 2.8% | PASS |
| mem_reads | `oltp_range_select` | 18.49ms | 14.56ms | 0.8× | 1.9% | PASS |
| mem_reads | `oltp_sum_range` | 16.59ms | 14.15ms | 0.9× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 3.35ms | 3.16ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.44ms | 4.32ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 4.14ms | 6.43ms | 1.6× | 1.5% | PASS |
| mem_reads | `select_random_points` | 24.83ms | 21.61ms | 0.9× | 3.7% | PASS |
| mem_reads | `select_random_ranges` | 3.71ms | 5.39ms | 1.5× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 4.24ms | 4.87ms | 1.1× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 35.51ms | 34.90ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 11.27ms | 10.03ms | 0.9× | 4.0% | PASS |
| mem_reads | `index_join_scan` | 4.03ms | 5.74ms | 1.4× | 3.0% | PASS |
| mem_reads | `types_table_scan` | 1.17s | 1.20s | 1.0× | 3.9% | PASS |
| mem_reads | `table_scan` | 1.29s | 1.29s | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_read_only` | 144.21ms | 138.63ms | 1.0× | 2.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 245.33ms | 376.83ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 18.70ms | 40.17ms | 2.1× | 1.4% | PASS |
| mem_writes | `oltp_update_index` | 69.54ms | 146.80ms | 2.1× | 2.1% | PASS |
| mem_writes | `oltp_update_non_index` | 50.66ms | 86.98ms | 1.7× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 56.88ms | 106.67ms | 1.9× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 28.82ms | 62.60ms | 2.2× | 2.1% | PASS |
| mem_writes | `types_delete_insert` | 41.76ms | 55.87ms | 1.3× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 105.50ms | 151.87ms | 1.4× | 2.8% | PASS |
| file_reads | `oltp_point_select` | 114.30ms | 58.90ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 25.43ms | 16.55ms | 0.7× | 2.8% | PASS |
| file_reads | `oltp_sum_range` | 24.79ms | 16.58ms | 0.7× | 2.2% | PASS |
| file_reads | `oltp_order_range` | 4.47ms | 3.61ms | 0.8× | 2.6% | PASS |
| file_reads | `oltp_distinct_range` | 5.44ms | 4.74ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_index_scan` | 11.94ms | 8.69ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_points` | 32.66ms | 24.45ms | 0.7× | 3.8% | PASS |
| file_reads | `select_random_ranges` | 11.31ms | 7.48ms | 0.7× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 12.33ms | 7.06ms | 0.6× | 2.3% | PASS |
| file_reads | `groupby_scan` | 36.77ms | 35.68ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 16.39ms | 11.62ms | 0.7× | 3.3% | PASS |
| file_reads | `index_join_scan` | 5.14ms | 6.29ms | 1.2× | 3.4% | PASS |
| file_reads | `types_table_scan` | 1.12s | 1.19s | 1.1× | 2.0% | PASS |
| file_reads | `table_scan` | 1.34s | 1.31s | 1.0× | 3.0% | PASS |
| file_reads | `oltp_read_only` | 256.38ms | 168.80ms | 0.7× | 1.7% | PASS |
| file_writes | `oltp_bulk_insert` | 270.27ms | 387.92ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 25.96ms | 46.01ms | 1.8× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 132.52ms | 164.14ms | 1.2× | 6.3% | PASS |
| file_writes | `oltp_update_non_index` | 117.82ms | 104.67ms | 0.9× | 8.6% | PASS |
| file_writes | `oltp_delete_insert` | 101.73ms | 124.99ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 91.02ms | 76.73ms | 0.8× | 8.9% | PASS |
| file_writes | `types_delete_insert` | 76.22ms | 66.20ms | 0.9× | 1.3% | PASS |
| file_writes | `oltp_read_write` | 175.81ms | 167.88ms | 1.0× | 6.6% | PASS |
| ac_reads | `oltp_point_select` | 63.03ms | 58.84ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_range_select` | 20.37ms | 16.54ms | 0.8× | 2.4% | PASS |
| ac_reads | `oltp_sum_range` | 19.23ms | 16.49ms | 0.9× | 2.3% | PASS |
| ac_reads | `oltp_order_range` | 3.89ms | 3.52ms | 0.9× | 2.6% | PASS |
| ac_reads | `oltp_distinct_range` | 4.89ms | 4.70ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 6.96ms | 8.65ms | 1.2× | 2.2% | PASS |
| ac_reads | `select_random_points` | 26.83ms | 24.42ms | 0.9× | 3.5% | PASS |
| ac_reads | `select_random_ranges` | 6.38ms | 7.47ms | 1.2× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.32ms | 7.00ms | 1.0× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 35.54ms | 35.28ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 13.00ms | 11.23ms | 0.9× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 4.37ms | 6.05ms | 1.4× | 1.6% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.21s | 1.0× | 5.4% | PASS |
| ac_reads | `table_scan` | 1.47s | 1.34s | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_read_only` | 194.91ms | 176.39ms | 0.9× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.74ms | 66.80ms | 2.7× | 7.2% | PASS |
| ac_writes | `oltp_insert_ac` | 27.13ms | 77.95ms | 2.9× | 8.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.05ms | 91.10ms | 3.2× | 7.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.72ms | 74.64ms | 3.0× | 6.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.19ms | 83.56ms | 3.0× | 6.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.21ms | 81.75ms | 3.0× | 7.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.36ms | 76.30ms | 3.0× | 8.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.67ms | 91.92ms | 2.7× | 6.2% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 26.17ms | 24.55ms | 0.9× | 5.1% | PASS |
| mem_reads | `oltp_range_select` | 11.00ms | 9.19ms | 0.8× | 3.2% | PASS |
| mem_reads | `oltp_sum_range` | 10.84ms | 9.25ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 2.27ms | 2.09ms | 0.9× | 1.7% | PASS |
| mem_reads | `oltp_distinct_range` | 2.73ms | 2.74ms | 1.0× | 2.0% | PASS |
| mem_reads | `oltp_index_scan` | 2.67ms | 4.08ms | 1.5× | 3.4% | PASS |
| mem_reads | `select_random_points` | 16.22ms | 14.37ms | 0.9× | 3.1% | PASS |
| mem_reads | `select_random_ranges` | 2.52ms | 3.65ms | 1.4× | 2.0% | PASS |
| mem_reads | `covering_index_scan` | 2.54ms | 3.53ms | 1.4× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 21.38ms | 22.54ms | 1.1× | 1.2% | PASS |
| mem_reads | `index_join` | 7.97ms | 5.95ms | 0.7× | 3.9% | PASS |
| mem_reads | `index_join_scan` | 2.71ms | 4.57ms | 1.7× | 4.1% | PASS |
| mem_reads | `types_table_scan` | 715.97ms | 759.95ms | 1.1× | 1.3% | PASS |
| mem_reads | `table_scan` | 926.41ms | 924.89ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_read_only` | 100.48ms | 89.02ms | 0.9× | 3.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 150.78ms | 232.13ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 12.00ms | 27.59ms | 2.3× | 2.6% | PASS |
| mem_writes | `oltp_update_index` | 54.90ms | 118.12ms | 2.2× | 3.2% | PASS |
| mem_writes | `oltp_update_non_index` | 38.78ms | 63.17ms | 1.6× | 5.8% | PASS |
| mem_writes | `oltp_delete_insert` | 37.44ms | 77.01ms | 2.1× | 3.1% | PASS |
| mem_writes | `oltp_write_only` | 20.76ms | 47.40ms | 2.3× | 2.8% | PASS |
| mem_writes | `types_delete_insert` | 28.20ms | 39.58ms | 1.4× | 2.9% | PASS |
| mem_writes | `oltp_read_write` | 67.70ms | 96.96ms | 1.4× | 4.6% | PASS |
| file_reads | `oltp_point_select` | 82.59ms | 39.70ms | 0.5× | 2.1% | PASS |
| file_reads | `oltp_range_select` | 17.05ms | 10.61ms | 0.6× | 3.6% | PASS |
| file_reads | `oltp_sum_range` | 16.18ms | 10.35ms | 0.6× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 2.93ms | 2.24ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 3.45ms | 3.00ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 8.72ms | 5.99ms | 0.7× | 2.6% | PASS |
| file_reads | `select_random_points` | 23.55ms | 16.26ms | 0.7× | 5.5% | PASS |
| file_reads | `select_random_ranges` | 8.14ms | 5.22ms | 0.6× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 8.70ms | 4.99ms | 0.6× | 2.3% | PASS |
| file_reads | `groupby_scan` | 22.44ms | 22.96ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join` | 12.05ms | 7.96ms | 0.7× | 5.6% | PASS |
| file_reads | `index_join_scan` | 3.54ms | 4.50ms | 1.3× | 3.5% | PASS |
| file_reads | `types_table_scan` | 726.18ms | 763.77ms | 1.1× | 2.9% | PASS |
| file_reads | `table_scan` | 924.71ms | 934.89ms | 1.0× | 3.6% | PASS |
| file_reads | `oltp_read_only` | 181.29ms | 110.42ms | 0.6× | 2.8% | PASS |
| file_writes | `oltp_bulk_insert` | 219.94ms | 299.97ms | 1.4× | 3.8% | PASS |
| file_writes | `oltp_insert` | 21.18ms | 47.90ms | 2.3× | 9.6% | PASS |
| file_writes | `oltp_update_index` | 153.33ms | 185.19ms | 1.2× | 6.5% | PASS |
| file_writes | `oltp_update_non_index` | 130.90ms | 119.41ms | 0.9× | 3.6% | PASS |
| file_writes | `oltp_delete_insert` | 153.92ms | 139.99ms | 0.9× | 4.3% | PASS |
| file_writes | `oltp_write_only` | 101.61ms | 98.91ms | 1.0× | 2.4% | PASS |
| file_writes | `types_delete_insert` | 111.78ms | 76.07ms | 0.7× | 8.3% | PASS |
| file_writes | `oltp_read_write` | 151.13ms | 143.90ms | 1.0× | 6.6% | PASS |
| ac_reads | `oltp_point_select` | 44.53ms | 38.48ms | 0.9× | 4.2% | PASS |
| ac_reads | `oltp_range_select` | 13.46ms | 10.62ms | 0.8× | 3.4% | PASS |
| ac_reads | `oltp_sum_range` | 13.36ms | 10.58ms | 0.8× | 4.0% | PASS |
| ac_reads | `oltp_order_range` | 2.60ms | 2.23ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 3.07ms | 2.95ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 5.10ms | 5.93ms | 1.2× | 2.2% | PASS |
| ac_reads | `select_random_points` | 21.29ms | 16.59ms | 0.8× | 4.3% | PASS |
| ac_reads | `select_random_ranges` | 4.51ms | 5.15ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 5.11ms | 4.98ms | 1.0× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 22.52ms | 23.45ms | 1.0× | 1.2% | PASS |
| ac_reads | `index_join` | 11.18ms | 8.69ms | 0.8× | 4.9% | PASS |
| ac_reads | `index_join_scan` | 3.54ms | 5.19ms | 1.5× | 6.1% | PASS |
| ac_reads | `types_table_scan` | 829.02ms | 850.39ms | 1.0× | 1.8% | PASS |
| ac_reads | `table_scan` | 977.24ms | 974.26ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 132.71ms | 112.73ms | 0.8× | 1.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.14ms | 52.99ms | 2.2× | 6.3% | PASS |
| ac_writes | `oltp_insert_ac` | 27.38ms | 63.85ms | 2.3× | 7.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.74ms | 71.41ms | 2.6× | 8.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.15ms | 59.39ms | 2.8× | 4.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.06ms | 65.84ms | 2.4× | 7.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.49ms | 63.34ms | 2.5× | 6.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.79ms | 59.10ms | 2.7× | 6.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.27ms | 68.52ms | 2.3× | 4.2% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.62ms | 41.52ms | 1.2× | 2.0% | PASS |
| mem_reads | `oltp_range_select` | 20.24ms | 21.37ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_sum_range` | 18.43ms | 20.81ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_order_range` | 3.56ms | 3.93ms | 1.1× | 1.9% | PASS |
| mem_reads | `oltp_distinct_range` | 4.71ms | 5.12ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 4.65ms | 6.30ms | 1.4× | 2.2% | PASS |
| mem_reads | `select_random_points` | 29.13ms | 33.49ms | 1.1× | 1.7% | PASS |
| mem_reads | `select_random_ranges` | 7.95ms | 9.13ms | 1.1× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.27ms | 4.63ms | 1.1× | 3.3% | PASS |
| mem_reads | `groupby_scan` | 36.77ms | 39.96ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 8.09ms | 10.65ms | 1.3× | 2.4% | PASS |
| mem_reads | `index_join_scan` | 4.06ms | 5.71ms | 1.4× | 4.0% | PASS |
| mem_reads | `types_table_scan` | 1.12s | 1.21s | 1.1× | 2.4% | PASS |
| mem_reads | `table_scan` | 1.29s | 1.29s | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_read_only` | 156.00ms | 175.55ms | 1.1× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 254.56ms | 370.11ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 19.61ms | 38.09ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 69.74ms | 140.79ms | 2.0× | 2.1% | PASS |
| mem_writes | `oltp_update_non_index` | 53.45ms | 92.01ms | 1.7× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 52.27ms | 105.88ms | 2.0× | 2.2% | PASS |
| mem_writes | `oltp_write_only` | 27.93ms | 64.84ms | 2.3× | 2.2% | FAIL |
| mem_writes | `types_delete_insert` | 34.49ms | 57.67ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 104.60ms | 170.24ms | 1.6× | 1.5% | PASS |
| file_reads | `oltp_point_select` | 105.85ms | 59.72ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 28.13ms | 23.52ms | 0.8× | 2.3% | PASS |
| file_reads | `oltp_sum_range` | 26.72ms | 23.14ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 4.60ms | 4.25ms | 0.9× | 2.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.72ms | 5.46ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 12.08ms | 8.40ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 38.85ms | 36.53ms | 0.9× | 1.6% | PASS |
| file_reads | `select_random_ranges` | 15.70ms | 11.30ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 11.94ms | 6.71ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 37.92ms | 40.45ms | 1.1× | 0.6% | PASS |
| file_reads | `index_join` | 12.85ms | 12.73ms | 1.0× | 2.2% | PASS |
| file_reads | `index_join_scan` | 5.08ms | 6.11ms | 1.2× | 3.3% | PASS |
| file_reads | `types_table_scan` | 1.17s | 1.22s | 1.0× | 0.5% | PASS |
| file_reads | `table_scan` | 1.40s | 1.32s | 0.9× | 1.1% | PASS |
| file_reads | `oltp_read_only` | 272.03ms | 208.47ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 270.81ms | 385.35ms | 1.4× | 0.8% | PASS |
| file_writes | `oltp_insert` | 27.32ms | 44.41ms | 1.6× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 99.18ms | 152.42ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 79.14ms | 104.61ms | 1.3× | 2.3% | PASS |
| file_writes | `oltp_delete_insert` | 79.17ms | 116.51ms | 1.5× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 53.58ms | 76.74ms | 1.4× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 53.35ms | 66.69ms | 1.3× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 137.84ms | 183.92ms | 1.3× | 1.8% | PASS |
| ac_reads | `oltp_point_select` | 58.74ms | 59.61ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 22.73ms | 23.36ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 21.00ms | 22.85ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 4.09ms | 4.23ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 5.22ms | 5.45ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 7.58ms | 8.46ms | 1.1× | 1.7% | PASS |
| ac_reads | `select_random_points` | 33.00ms | 36.59ms | 1.1× | 1.5% | PASS |
| ac_reads | `select_random_ranges` | 10.68ms | 11.31ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 7.27ms | 6.66ms | 0.9× | 2.4% | PASS |
| ac_reads | `groupby_scan` | 37.53ms | 40.39ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 10.09ms | 12.48ms | 1.2× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 4.76ms | 6.14ms | 1.3× | 3.1% | PASS |
| ac_reads | `types_table_scan` | 1.16s | 1.22s | 1.0× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.40s | 1.32s | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 198.00ms | 205.98ms | 1.0× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.74ms | 58.12ms | 2.8× | 3.1% | PASS |
| ac_writes | `oltp_insert_ac` | 23.24ms | 76.10ms | 3.3× | 4.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.95ms | 86.91ms | 3.3× | 4.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.57ms | 68.59ms | 3.2× | 5.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.37ms | 79.16ms | 3.4× | 5.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.54ms | 78.41ms | 3.3× | 4.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.71ms | 69.20ms | 3.3× | 4.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.62ms | 87.11ms | 2.8× | 4.2% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 53s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 73.21ms | 130.00ms | 56.3% | 0.8% | PASS |
| `status_dirty_many_tables` | 75.96ms | 130.00ms | 58.4% | 0.5% | PASS |
| `diff_regular_working_one_table` | 68.30ms | 120.00ms | 56.9% | 0.7% | PASS |
| `diff_regular_working_many_tables` | 80.18ms | 140.00ms | 57.3% | 0.8% | PASS |
| `diff_stat_working_many_tables` | 79.98ms | 140.00ms | 57.1% | 0.6% | PASS |
| `diff_schema_working_many_tables` | 80.53ms | 140.00ms | 57.5% | 0.8% | PASS |
| `branch_list_many_branches` | 20.99ms | 35.00ms | 60.0% | 1.1% | PASS |
| `branch_create_delete` | 23.02ms | 40.00ms | 57.6% | 2.4% | PASS |
| `checkout_branch_clean` | 51.63ms | 150.00ms | 34.4% | 1.4% | PASS |
| `merge_data_no_conflicts` | 25.64ms | 50.00ms | 51.3% | 1.4% | PASS |
| `merge_schema_no_conflicts` | 20.41ms | 35.00ms | 58.3% | 1.8% | PASS |
| `merge_data_conflicts` | 28.44ms | 180.00ms | 15.8% | 1.5% | PASS |
| `merge_data_conflicts_with_resolve` | 28.62ms | 180.00ms | 15.9% | 1.5% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
