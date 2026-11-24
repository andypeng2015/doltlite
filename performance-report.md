# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-24 21:31 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261116.202.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/45479181153)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 7.38s | 7.70s | 1.0× | 2.1% | **PASS** |
| Writes | 1.47s | 2.45s | 1.7× | 2.0% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.16s | 7.98s | 1.0× | 1.8% | **PASS** |
| Writes | 3.44s | 3.68s | 1.1× | 4.8% | **FAIL** |
| Autocommit writes | 636.75ms | 1.78s | 2.8× | 7.0% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.04s | 2.14s | 1.0× | 2.0% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.80s | 1.81s | 1.0× | 3.0% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 1.61s | 1.70s | 1.1× | 3.3% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 1.93s | 2.05s | 1.1× | 1.0% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 348.39ms | 571.35ms | 1.6× | 2.0% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 376.13ms | 635.88ms | 1.7× | 2.3% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 365.74ms | 623.27ms | 1.7× | 2.7% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 375.72ms | 618.19ms | 1.6× | 0.8% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.20s | 2.18s | 1.0× | 0.8% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.03s | 1.90s | 0.9× | 2.0% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 1.93s | 1.84s | 1.0× | 2.4% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 1.99s | 2.07s | 1.0× | 1.3% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 868.45ms | 896.43ms | 1.0× | 3.9% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.09s | 1.07s | 1.0× | 5.4% | **FAIL** |
| File-backed | Writes | blobpk | 8 | 55 | 1.02s | 1.04s | 1.0× | 5.2% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 463.50ms | 680.27ms | 1.5× | 2.3% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.06s | 2.18s | 1.1× | 1.0% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.82s | 1.84s | 1.0× | 2.3% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 1.79s | 1.80s | 1.0× | 2.7% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 1.98s | 2.07s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 151.24ms | 480.36ms | 3.2× | 13.7% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 186.48ms | 497.64ms | 2.7× | 7.0% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 217.21ms | 493.67ms | 2.3× | 6.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 81.83ms | 308.39ms | 3.8× | 5.8% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 19.34ms | 22.16ms | 1.1× | 2.7% | PASS |
| mem_reads | `oltp_range_select` | 8.92ms | 8.81ms | 1.0× | 2.0% | PASS |
| mem_reads | `oltp_sum_range` | 7.50ms | 8.48ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 2.21ms | 2.32ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 2.99ms | 3.16ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.05ms | 3.81ms | 1.3× | 1.8% | PASS |
| mem_reads | `select_random_points` | 8.52ms | 8.99ms | 1.1× | 4.2% | PASS |
| mem_reads | `select_random_ranges` | 2.62ms | 3.13ms | 1.2× | 2.1% | PASS |
| mem_reads | `covering_index_scan` | 3.28ms | 3.18ms | 1.0× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 24.37ms | 27.01ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 4.54ms | 6.08ms | 1.3× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 2.74ms | 3.93ms | 1.4× | 3.2% | PASS |
| mem_reads | `types_table_scan` | 858.45ms | 924.21ms | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.01s | 1.02s | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_read_only` | 79.71ms | 89.47ms | 1.1× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 135.94ms | 204.61ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 11.78ms | 21.66ms | 1.8× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 41.69ms | 79.14ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 29.53ms | 48.56ms | 1.6× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 35.48ms | 59.40ms | 1.7× | 2.3% | PASS |
| mem_writes | `oltp_write_only` | 17.89ms | 38.80ms | 2.2× | 2.1% | PASS |
| mem_writes | `types_delete_insert` | 20.02ms | 30.53ms | 1.5× | 2.6% | PASS |
| mem_writes | `oltp_read_write` | 56.06ms | 88.65ms | 1.6× | 2.3% | PASS |
| file_reads | `oltp_point_select` | 87.96ms | 38.45ms | 0.4× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 16.75ms | 10.66ms | 0.6× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 15.60ms | 10.46ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_order_range` | 3.07ms | 2.56ms | 0.8× | 0.7% | PASS |
| file_reads | `oltp_distinct_range` | 3.80ms | 3.38ms | 0.9× | 0.7% | PASS |
| file_reads | `oltp_index_scan` | 10.32ms | 5.98ms | 0.6× | 0.8% | PASS |
| file_reads | `select_random_points` | 16.03ms | 10.86ms | 0.7× | 0.7% | PASS |
| file_reads | `select_random_ranges` | 9.63ms | 4.86ms | 0.5× | 0.5% | PASS |
| file_reads | `covering_index_scan` | 10.59ms | 5.31ms | 0.5× | 0.8% | PASS |
| file_reads | `groupby_scan` | 25.59ms | 27.49ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 8.61ms | 7.64ms | 0.9× | 1.2% | PASS |
| file_reads | `index_join_scan` | 3.56ms | 4.08ms | 1.1× | 1.8% | PASS |
| file_reads | `types_table_scan` | 852.02ms | 921.05ms | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 960.85ms | 1.01s | 1.1× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 178.75ms | 112.82ms | 0.6× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 180.80ms | 252.46ms | 1.4× | 2.4% | PASS |
| file_writes | `oltp_insert` | 28.42ms | 34.21ms | 1.2× | 4.9% | PASS |
| file_writes | `oltp_update_index` | 139.12ms | 140.23ms | 1.0× | 2.4% | PASS |
| file_writes | `oltp_update_non_index` | 116.77ms | 97.73ms | 0.8× | 5.4% | PASS |
| file_writes | `oltp_delete_insert` | 121.09ms | 112.64ms | 0.9× | 2.0% | PASS |
| file_writes | `oltp_write_only` | 88.78ms | 78.64ms | 0.9× | 7.4% | PASS |
| file_writes | `types_delete_insert` | 72.69ms | 55.94ms | 0.8× | 5.8% | PASS |
| file_writes | `oltp_read_write` | 120.78ms | 124.57ms | 1.0× | 3.0% | PASS |
| ac_reads | `oltp_point_select` | 39.79ms | 38.16ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 11.64ms | 10.55ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 10.27ms | 10.32ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 2.58ms | 2.54ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_distinct_range` | 3.28ms | 3.37ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 5.65ms | 5.94ms | 1.1× | 0.9% | PASS |
| ac_reads | `select_random_points` | 10.99ms | 10.70ms | 1.0× | 1.0% | PASS |
| ac_reads | `select_random_ranges` | 4.91ms | 4.83ms | 1.0× | 0.5% | PASS |
| ac_reads | `covering_index_scan` | 5.90ms | 5.26ms | 0.9× | 1.0% | PASS |
| ac_reads | `groupby_scan` | 24.99ms | 27.42ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 6.24ms | 7.67ms | 1.2× | 1.6% | PASS |
| ac_reads | `index_join_scan` | 3.17ms | 4.23ms | 1.3× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 851.60ms | 923.10ms | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 970.17ms | 1.01s | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 112.36ms | 113.40ms | 1.0× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.02ms | 48.76ms | 2.9× | 13.6% | PASS |
| ac_writes | `oltp_insert_ac` | 19.33ms | 61.42ms | 3.2× | 30.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.21ms | 68.15ms | 3.4× | 11.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.58ms | 56.03ms | 3.2× | 17.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 19.05ms | 60.87ms | 3.2× | 10.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 18.68ms | 60.11ms | 3.2× | 6.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 16.35ms | 57.57ms | 3.5× | 14.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 23.01ms | 67.44ms | 2.9× | 13.8% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 21.61ms | 21.97ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 9.88ms | 8.36ms | 0.8× | 3.0% | PASS |
| mem_reads | `oltp_sum_range` | 9.91ms | 8.66ms | 0.9× | 3.9% | PASS |
| mem_reads | `oltp_order_range` | 2.15ms | 1.98ms | 0.9× | 1.8% | PASS |
| mem_reads | `oltp_distinct_range` | 2.69ms | 2.48ms | 0.9× | 3.3% | PASS |
| mem_reads | `oltp_index_scan` | 2.58ms | 3.95ms | 1.5× | 3.3% | PASS |
| mem_reads | `select_random_points` | 14.94ms | 13.42ms | 0.9× | 3.7% | PASS |
| mem_reads | `select_random_ranges` | 2.24ms | 3.26ms | 1.5× | 2.1% | PASS |
| mem_reads | `covering_index_scan` | 2.32ms | 2.67ms | 1.1× | 2.3% | PASS |
| mem_reads | `groupby_scan` | 22.15ms | 20.87ms | 0.9× | 1.6% | PASS |
| mem_reads | `index_join` | 8.06ms | 5.63ms | 0.7× | 4.5% | PASS |
| mem_reads | `index_join_scan` | 2.95ms | 4.72ms | 1.6× | 3.8% | PASS |
| mem_reads | `types_table_scan` | 727.88ms | 762.83ms | 1.0× | 2.0% | PASS |
| mem_reads | `table_scan` | 888.30ms | 878.03ms | 1.0× | 2.5% | PASS |
| mem_reads | `oltp_read_only` | 84.31ms | 74.87ms | 0.9× | 3.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 137.80ms | 212.22ms | 1.5× | 2.3% | PASS |
| mem_writes | `oltp_insert` | 10.51ms | 24.43ms | 2.3× | 1.9% | FAIL |
| mem_writes | `oltp_update_index` | 42.14ms | 91.21ms | 2.2× | 2.5% | PASS |
| mem_writes | `oltp_update_non_index` | 33.25ms | 57.76ms | 1.7× | 2.3% | PASS |
| mem_writes | `oltp_delete_insert` | 37.25ms | 72.94ms | 2.0× | 1.9% | PASS |
| mem_writes | `oltp_write_only` | 19.81ms | 43.85ms | 2.2× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 26.59ms | 38.43ms | 1.4× | 3.0% | PASS |
| mem_writes | `oltp_read_write` | 68.79ms | 95.05ms | 1.4× | 4.4% | PASS |
| file_reads | `oltp_point_select` | 81.80ms | 38.12ms | 0.5× | 2.6% | PASS |
| file_reads | `oltp_range_select` | 17.47ms | 10.35ms | 0.6× | 2.5% | PASS |
| file_reads | `oltp_sum_range` | 16.26ms | 10.30ms | 0.6× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 3.03ms | 2.28ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 3.53ms | 2.75ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 8.34ms | 5.45ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 21.75ms | 15.19ms | 0.7× | 2.3% | PASS |
| file_reads | `select_random_ranges` | 8.14ms | 5.09ms | 0.6× | 2.1% | PASS |
| file_reads | `covering_index_scan` | 8.29ms | 4.71ms | 0.6× | 2.0% | PASS |
| file_reads | `groupby_scan` | 23.47ms | 21.43ms | 0.9× | 1.9% | PASS |
| file_reads | `index_join` | 11.27ms | 6.96ms | 0.6× | 5.9% | PASS |
| file_reads | `index_join_scan` | 3.47ms | 4.53ms | 1.3× | 1.8% | PASS |
| file_reads | `types_table_scan` | 716.60ms | 756.01ms | 1.1× | 2.0% | PASS |
| file_reads | `table_scan` | 953.19ms | 919.24ms | 1.0× | 4.1% | PASS |
| file_reads | `oltp_read_only` | 156.09ms | 94.51ms | 0.6× | 1.9% | PASS |
| file_writes | `oltp_bulk_insert` | 219.25ms | 285.68ms | 1.3× | 4.9% | PASS |
| file_writes | `oltp_insert` | 19.95ms | 47.75ms | 2.4× | 6.8% | FAIL |
| file_writes | `oltp_update_index` | 161.38ms | 179.43ms | 1.1× | 5.9% | PASS |
| file_writes | `oltp_update_non_index` | 131.54ms | 117.09ms | 0.9× | 3.3% | PASS |
| file_writes | `oltp_delete_insert` | 167.47ms | 134.18ms | 0.8× | 4.1% | PASS |
| file_writes | `oltp_write_only` | 111.27ms | 98.01ms | 0.9× | 4.9% | PASS |
| file_writes | `types_delete_insert` | 133.47ms | 69.71ms | 0.5× | 9.2% | PASS |
| file_writes | `oltp_read_write` | 145.09ms | 138.08ms | 1.0× | 6.3% | PASS |
| ac_reads | `oltp_point_select` | 39.71ms | 36.17ms | 0.9× | 2.3% | PASS |
| ac_reads | `oltp_range_select` | 12.59ms | 9.87ms | 0.8× | 2.7% | PASS |
| ac_reads | `oltp_sum_range` | 12.62ms | 10.03ms | 0.8× | 2.3% | PASS |
| ac_reads | `oltp_order_range` | 2.72ms | 2.26ms | 0.8× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 3.26ms | 2.77ms | 0.8× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 4.74ms | 5.66ms | 1.2× | 2.1% | PASS |
| ac_reads | `select_random_points` | 17.88ms | 15.35ms | 0.9× | 3.1% | PASS |
| ac_reads | `select_random_ranges` | 4.32ms | 4.85ms | 1.1× | 1.7% | PASS |
| ac_reads | `covering_index_scan` | 4.64ms | 4.62ms | 1.0× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 21.91ms | 20.46ms | 0.9× | 1.7% | PASS |
| ac_reads | `index_join` | 9.33ms | 7.18ms | 0.8× | 4.9% | PASS |
| ac_reads | `index_join_scan` | 3.15ms | 4.51ms | 1.4× | 2.7% | PASS |
| ac_reads | `types_table_scan` | 731.60ms | 763.03ms | 1.0× | 3.6% | PASS |
| ac_reads | `table_scan` | 844.76ms | 863.55ms | 1.0× | 4.0% | PASS |
| ac_reads | `oltp_read_only` | 107.41ms | 94.60ms | 0.9× | 2.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.72ms | 53.76ms | 2.5× | 6.2% | PASS |
| ac_writes | `oltp_insert_ac` | 23.77ms | 59.94ms | 2.5× | 6.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 23.86ms | 69.20ms | 2.9× | 7.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.80ms | 57.58ms | 2.8× | 8.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.60ms | 64.32ms | 2.7× | 7.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.21ms | 64.17ms | 2.8× | 5.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.07ms | 59.86ms | 2.7× | 8.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.45ms | 68.81ms | 2.5× | 6.0% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 20.22ms | 20.88ms | 1.0× | 3.7% | PASS |
| mem_reads | `oltp_range_select` | 8.76ms | 7.86ms | 0.9× | 3.9% | PASS |
| mem_reads | `oltp_sum_range` | 8.28ms | 7.83ms | 0.9× | 4.9% | PASS |
| mem_reads | `oltp_order_range` | 2.06ms | 1.92ms | 0.9× | 2.8% | PASS |
| mem_reads | `oltp_distinct_range` | 2.49ms | 2.35ms | 0.9× | 3.3% | PASS |
| mem_reads | `oltp_index_scan` | 2.45ms | 3.81ms | 1.6× | 2.6% | PASS |
| mem_reads | `select_random_points` | 14.98ms | 13.48ms | 0.9× | 2.7% | PASS |
| mem_reads | `select_random_ranges` | 2.22ms | 3.36ms | 1.5× | 3.5% | PASS |
| mem_reads | `covering_index_scan` | 2.27ms | 2.76ms | 1.2× | 4.9% | PASS |
| mem_reads | `groupby_scan` | 20.79ms | 20.15ms | 1.0× | 2.2% | PASS |
| mem_reads | `index_join` | 7.60ms | 5.61ms | 0.7× | 5.5% | PASS |
| mem_reads | `index_join_scan` | 2.50ms | 4.21ms | 1.7× | 8.0% | PASS |
| mem_reads | `types_table_scan` | 681.93ms | 730.00ms | 1.1× | 2.2% | PASS |
| mem_reads | `table_scan` | 762.21ms | 803.42ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_read_only` | 73.53ms | 69.93ms | 1.0× | 1.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 135.45ms | 205.88ms | 1.5× | 1.7% | PASS |
| mem_writes | `oltp_insert` | 10.39ms | 23.47ms | 2.3× | 1.6% | PASS |
| mem_writes | `oltp_update_index` | 41.65ms | 92.21ms | 2.2× | 2.4% | PASS |
| mem_writes | `oltp_update_non_index` | 33.35ms | 56.93ms | 1.7× | 3.7% | PASS |
| mem_writes | `oltp_delete_insert` | 35.10ms | 72.17ms | 2.1× | 4.1% | PASS |
| mem_writes | `oltp_write_only` | 18.64ms | 42.88ms | 2.3× | 2.1% | PASS |
| mem_writes | `types_delete_insert` | 25.55ms | 36.73ms | 1.4× | 2.9% | PASS |
| mem_writes | `oltp_read_write` | 65.60ms | 93.02ms | 1.4× | 4.2% | PASS |
| file_reads | `oltp_point_select` | 79.38ms | 37.51ms | 0.5× | 2.4% | PASS |
| file_reads | `oltp_range_select` | 17.77ms | 10.80ms | 0.6× | 2.3% | PASS |
| file_reads | `oltp_sum_range` | 17.48ms | 11.24ms | 0.6× | 2.6% | PASS |
| file_reads | `oltp_order_range` | 2.91ms | 2.28ms | 0.8× | 2.6% | PASS |
| file_reads | `oltp_distinct_range` | 3.30ms | 2.66ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_index_scan` | 8.54ms | 5.87ms | 0.7× | 2.5% | PASS |
| file_reads | `select_random_points` | 23.52ms | 15.80ms | 0.7× | 4.7% | PASS |
| file_reads | `select_random_ranges` | 7.58ms | 4.87ms | 0.6× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 8.13ms | 4.57ms | 0.6× | 2.3% | PASS |
| file_reads | `groupby_scan` | 21.86ms | 20.50ms | 0.9× | 1.9% | PASS |
| file_reads | `index_join` | 10.99ms | 7.25ms | 0.7× | 2.5% | PASS |
| file_reads | `index_join_scan` | 3.34ms | 4.34ms | 1.3× | 2.1% | PASS |
| file_reads | `types_table_scan` | 710.34ms | 747.26ms | 1.1× | 2.4% | PASS |
| file_reads | `table_scan` | 851.45ms | 865.02ms | 1.0× | 2.9% | PASS |
| file_reads | `oltp_read_only` | 164.69ms | 97.44ms | 0.6× | 5.1% | PASS |
| file_writes | `oltp_bulk_insert` | 202.54ms | 272.55ms | 1.3× | 4.6% | PASS |
| file_writes | `oltp_insert` | 22.61ms | 47.44ms | 2.1× | 13.1% | PASS |
| file_writes | `oltp_update_index` | 145.23ms | 177.27ms | 1.2× | 5.0% | PASS |
| file_writes | `oltp_update_non_index` | 129.55ms | 117.81ms | 0.9× | 5.1% | PASS |
| file_writes | `oltp_delete_insert` | 149.97ms | 128.68ms | 0.9× | 5.3% | PASS |
| file_writes | `oltp_write_only` | 109.76ms | 88.50ms | 0.8× | 2.8% | PASS |
| file_writes | `types_delete_insert` | 110.69ms | 68.86ms | 0.6× | 7.9% | PASS |
| file_writes | `oltp_read_write` | 150.65ms | 134.19ms | 0.9× | 5.9% | PASS |
| ac_reads | `oltp_point_select` | 41.24ms | 36.20ms | 0.9× | 2.9% | PASS |
| ac_reads | `oltp_range_select` | 12.07ms | 9.52ms | 0.8× | 2.2% | PASS |
| ac_reads | `oltp_sum_range` | 11.96ms | 9.94ms | 0.8× | 2.1% | PASS |
| ac_reads | `oltp_order_range` | 2.50ms | 2.21ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 2.88ms | 2.66ms | 0.9× | 2.2% | PASS |
| ac_reads | `oltp_index_scan` | 4.70ms | 5.56ms | 1.2× | 2.7% | PASS |
| ac_reads | `select_random_points` | 16.67ms | 14.53ms | 0.9× | 3.3% | PASS |
| ac_reads | `select_random_ranges` | 4.08ms | 4.83ms | 1.2× | 2.7% | PASS |
| ac_reads | `covering_index_scan` | 4.61ms | 4.58ms | 1.0× | 2.4% | PASS |
| ac_reads | `groupby_scan` | 21.46ms | 20.42ms | 1.0× | 1.4% | PASS |
| ac_reads | `index_join` | 9.40ms | 7.30ms | 0.8× | 4.0% | PASS |
| ac_reads | `index_join_scan` | 3.06ms | 4.34ms | 1.4× | 2.9% | PASS |
| ac_reads | `types_table_scan` | 700.59ms | 737.04ms | 1.1× | 1.3% | PASS |
| ac_reads | `table_scan` | 849.84ms | 851.50ms | 1.0× | 3.0% | PASS |
| ac_reads | `oltp_read_only` | 102.96ms | 94.21ms | 0.9× | 2.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 26.50ms | 50.48ms | 1.9× | 5.7% | PASS |
| ac_writes | `oltp_insert_ac` | 28.95ms | 62.40ms | 2.2× | 6.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.99ms | 71.78ms | 2.5× | 11.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.57ms | 56.85ms | 2.3× | 6.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.19ms | 64.38ms | 2.3× | 12.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.77ms | 62.86ms | 2.3× | 5.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.39ms | 56.91ms | 2.7× | 7.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 30.86ms | 68.01ms | 2.2× | 7.1% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 21.39ms | 25.52ms | 1.2× | 0.7% | PASS |
| mem_reads | `oltp_range_select` | 14.15ms | 14.18ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_sum_range` | 13.62ms | 13.62ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_order_range` | 2.67ms | 2.73ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 3.33ms | 3.42ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.20ms | 4.24ms | 1.3× | 1.2% | PASS |
| mem_reads | `select_random_points` | 19.22ms | 22.73ms | 1.2× | 0.8% | PASS |
| mem_reads | `select_random_ranges` | 5.13ms | 5.98ms | 1.2× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 2.58ms | 2.85ms | 1.1× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 25.52ms | 27.77ms | 1.1× | 0.3% | PASS |
| mem_reads | `index_join` | 5.73ms | 7.77ms | 1.4× | 1.0% | PASS |
| mem_reads | `index_join_scan` | 2.88ms | 4.42ms | 1.5× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 799.32ms | 862.52ms | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 905.18ms | 940.14ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 101.59ms | 110.08ms | 1.1× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 143.67ms | 202.88ms | 1.4× | 0.3% | PASS |
| mem_writes | `oltp_insert` | 12.06ms | 22.00ms | 1.8× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 46.17ms | 89.59ms | 1.9× | 0.6% | PASS |
| mem_writes | `oltp_update_non_index` | 33.81ms | 58.79ms | 1.7× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 34.27ms | 66.42ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 18.70ms | 40.16ms | 2.1× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 21.04ms | 34.42ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 65.99ms | 103.92ms | 1.6× | 0.8% | PASS |
| file_reads | `oltp_point_select` | 43.06ms | 30.50ms | 0.7× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 15.85ms | 14.53ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 15.49ms | 14.11ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 2.86ms | 2.79ms | 1.0× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 3.55ms | 3.51ms | 1.0× | 1.8% | PASS |
| file_reads | `oltp_index_scan` | 5.47ms | 4.90ms | 0.9× | 1.2% | PASS |
| file_reads | `select_random_points` | 21.90ms | 24.02ms | 1.1× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 7.40ms | 6.59ms | 0.9× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 4.79ms | 3.38ms | 0.7× | 1.5% | PASS |
| file_reads | `groupby_scan` | 25.70ms | 27.86ms | 1.1× | 0.5% | PASS |
| file_reads | `index_join` | 7.04ms | 8.48ms | 1.2× | 1.1% | PASS |
| file_reads | `index_join_scan` | 3.30ms | 4.81ms | 1.5× | 2.5% | PASS |
| file_reads | `types_table_scan` | 803.04ms | 863.60ms | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 900.22ms | 940.67ms | 1.0× | 1.9% | PASS |
| file_reads | `oltp_read_only` | 131.47ms | 116.49ms | 0.9× | 1.8% | PASS |
| file_writes | `oltp_bulk_insert` | 152.84ms | 210.62ms | 1.4× | 0.7% | PASS |
| file_writes | `oltp_insert` | 15.42ms | 26.53ms | 1.7× | 2.4% | PASS |
| file_writes | `oltp_update_index` | 60.29ms | 98.75ms | 1.6× | 2.1% | PASS |
| file_writes | `oltp_update_non_index` | 46.06ms | 66.27ms | 1.4× | 2.4% | PASS |
| file_writes | `oltp_delete_insert` | 48.42ms | 75.74ms | 1.6× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 31.52ms | 49.13ms | 1.6× | 2.9% | PASS |
| file_writes | `types_delete_insert` | 30.60ms | 40.96ms | 1.3× | 3.5% | PASS |
| file_writes | `oltp_read_write` | 78.34ms | 112.28ms | 1.4× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 28.69ms | 30.80ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 14.98ms | 14.77ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 14.71ms | 14.25ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_order_range` | 2.75ms | 2.84ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_distinct_range` | 3.47ms | 3.55ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 4.28ms | 5.09ms | 1.2× | 2.2% | PASS |
| ac_reads | `select_random_points` | 21.07ms | 24.62ms | 1.2× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 6.09ms | 6.73ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 3.44ms | 3.52ms | 1.0× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 25.61ms | 27.88ms | 1.1× | 0.4% | PASS |
| ac_reads | `index_join` | 6.41ms | 8.30ms | 1.3× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 3.24ms | 4.74ms | 1.5× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 802.24ms | 862.99ms | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 931.69ms | 941.00ms | 1.0× | 2.7% | PASS |
| ac_reads | `oltp_read_only` | 110.60ms | 115.88ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 9.03ms | 29.14ms | 3.2× | 5.9% | PASS |
| ac_writes | `oltp_insert_ac` | 10.12ms | 39.65ms | 3.9× | 6.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 11.04ms | 44.90ms | 4.1× | 5.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 9.16ms | 34.58ms | 3.8× | 4.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 9.96ms | 40.89ms | 4.1× | 4.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 10.10ms | 39.62ms | 3.9× | 4.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 8.83ms | 34.80ms | 3.9× | 8.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 13.59ms | 44.82ms | 3.3× | 7.9% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 27s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 51.34ms | 130.00ms | 39.5% | 0.7% | PASS |
| `status_dirty_many_tables` | 61.03ms | 130.00ms | 46.9% | 1.6% | PASS |
| `diff_regular_working_one_table` | 48.39ms | 120.00ms | 40.3% | 0.8% | PASS |
| `diff_regular_working_many_tables` | 56.22ms | 140.00ms | 40.2% | 1.2% | PASS |
| `diff_stat_working_many_tables` | 55.74ms | 140.00ms | 39.8% | 0.8% | PASS |
| `diff_schema_working_many_tables` | 56.58ms | 140.00ms | 40.4% | 0.7% | PASS |
| `branch_list_many_branches` | 12.21ms | 35.00ms | 34.9% | 1.3% | PASS |
| `branch_create_delete` | 23.00ms | 40.00ms | 57.5% | 2.4% | PASS |
| `checkout_branch_clean` | 92.60ms | 150.00ms | 61.7% | 4.6% | PASS |
| `merge_data_no_conflicts` | 26.93ms | 50.00ms | 53.9% | 1.4% | PASS |
| `merge_schema_no_conflicts` | 13.42ms | 35.00ms | 38.3% | 1.3% | PASS |
| `merge_data_conflicts` | 17.45ms | 180.00ms | 9.7% | 0.6% | PASS |
| `merge_data_conflicts_with_resolve` | 17.28ms | 180.00ms | 9.6% | 0.5% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
