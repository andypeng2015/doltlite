# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-03 18:48 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261026.180.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/41682129592)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.78s | 10.30s | 1.1× | 2.2% | **PASS** |
| Writes | 1.98s | 3.41s | 1.7× | 1.9% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.46s | 10.51s | 1.0× | 1.9% | **PASS** |
| Writes | 3.35s | 4.20s | 1.3× | 2.1% | **FAIL** |
| Autocommit writes | 762.61ms | 2.29s | 3.0× | 6.6% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.55s | 2.71s | 1.1× | 1.8% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.76s | 1.80s | 1.0× | 3.1% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.63s | 2.85s | 1.1× | 2.5% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.84s | 2.94s | 1.0× | 1.6% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 438.83ms | 765.55ms | 1.7× | 1.8% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 352.99ms | 607.06ms | 1.7× | 2.0% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 598.31ms | 1.04s | 1.7× | 2.6% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 586.49ms | 996.69ms | 1.7× | 1.2% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 2.81s | 2.80s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 1.94s | 1.86s | 1.0× | 2.1% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.91s | 2.91s | 1.0× | 2.3% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.82s | 2.95s | 1.0× | 1.6% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 611.96ms | 851.52ms | 1.4× | 1.7% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.10s | 1.08s | 1.0× | 4.3% | **FAIL** |
| File-backed | Writes | blobpk | 8 | 55 | 828.60ms | 1.14s | 1.4× | 2.6% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 805.85ms | 1.13s | 1.4× | 1.4% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.66s | 2.80s | 1.1× | 1.5% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.90s | 1.89s | 1.0× | 2.3% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.68s | 2.87s | 1.1× | 1.8% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.71s | 2.96s | 1.1× | 1.8% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 185.94ms | 584.42ms | 3.1× | 8.1% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 189.77ms | 503.92ms | 2.7× | 6.0% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 189.59ms | 587.49ms | 3.1× | 6.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 197.31ms | 609.61ms | 3.1× | 5.7% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.54ms | 30.22ms | 1.2× | 2.2% | PASS |
| mem_reads | `oltp_range_select` | 11.62ms | 11.79ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_sum_range` | 9.76ms | 11.37ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 2.75ms | 2.98ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_distinct_range` | 3.73ms | 4.09ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.96ms | 5.57ms | 1.4× | 2.1% | PASS |
| mem_reads | `select_random_points` | 10.71ms | 11.31ms | 1.1× | 3.2% | PASS |
| mem_reads | `select_random_ranges` | 3.12ms | 4.04ms | 1.3× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 4.19ms | 4.49ms | 1.1× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 30.59ms | 33.87ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 5.78ms | 8.34ms | 1.4× | 2.5% | PASS |
| mem_reads | `index_join_scan` | 3.33ms | 4.49ms | 1.3× | 3.4% | PASS |
| mem_reads | `types_table_scan` | 1.08s | 1.18s | 1.1× | 1.2% | PASS |
| mem_reads | `table_scan` | 1.24s | 1.28s | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_read_only` | 108.98ms | 122.62ms | 1.1× | 1.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 175.84ms | 277.90ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 15.03ms | 28.95ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 50.83ms | 103.08ms | 2.0× | 2.4% | PASS |
| mem_writes | `oltp_update_non_index` | 35.47ms | 64.88ms | 1.8× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 44.09ms | 78.86ms | 1.8× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 21.68ms | 50.92ms | 2.3× | 1.5% | FAIL |
| mem_writes | `types_delete_insert` | 24.80ms | 41.91ms | 1.7× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 71.08ms | 119.05ms | 1.7× | 2.5% | PASS |
| file_reads | `oltp_point_select` | 92.80ms | 47.95ms | 0.5× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 18.60ms | 13.68ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 17.05ms | 13.17ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 3.59ms | 3.20ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 4.56ms | 4.31ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 11.05ms | 7.37ms | 0.7× | 1.9% | PASS |
| file_reads | `select_random_points` | 17.88ms | 12.87ms | 0.7× | 2.5% | PASS |
| file_reads | `select_random_ranges` | 9.92ms | 5.76ms | 0.6× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 11.33ms | 6.39ms | 0.6× | 1.1% | PASS |
| file_reads | `groupby_scan` | 31.56ms | 34.17ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 9.88ms | 9.79ms | 1.0× | 1.9% | PASS |
| file_reads | `index_join_scan` | 4.24ms | 4.81ms | 1.1× | 2.2% | PASS |
| file_reads | `types_table_scan` | 1.10s | 1.19s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.26s | 1.29s | 1.0× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 214.53ms | 152.51ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 189.74ms | 289.73ms | 1.5× | 0.9% | PASS |
| file_writes | `oltp_insert` | 22.27ms | 32.34ms | 1.5× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 80.54ms | 120.76ms | 1.5× | 2.0% | PASS |
| file_writes | `oltp_update_non_index` | 61.01ms | 78.89ms | 1.3× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 69.56ms | 93.33ms | 1.3× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 46.57ms | 60.07ms | 1.3× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 41.26ms | 45.65ms | 1.1× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 101.00ms | 130.74ms | 1.3× | 1.3% | PASS |
| ac_reads | `oltp_point_select` | 47.63ms | 48.24ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 14.50ms | 13.73ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 12.65ms | 13.39ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.15ms | 3.22ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.11ms | 4.32ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 6.60ms | 7.48ms | 1.1× | 1.9% | PASS |
| ac_reads | `select_random_points` | 13.37ms | 13.26ms | 1.0× | 3.1% | PASS |
| ac_reads | `select_random_ranges` | 5.58ms | 5.85ms | 1.0× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 7.07ms | 6.49ms | 0.9× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 31.15ms | 34.38ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 7.74ms | 10.03ms | 1.3× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 3.79ms | 4.98ms | 1.3× | 3.2% | PASS |
| ac_reads | `types_table_scan` | 1.10s | 1.19s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.26s | 1.29s | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 146.93ms | 152.96ms | 1.0× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.14ms | 60.09ms | 3.0× | 12.8% | PASS |
| ac_writes | `oltp_insert_ac` | 24.98ms | 70.62ms | 2.8× | 7.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.94ms | 81.64ms | 3.1× | 7.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.58ms | 66.58ms | 3.2× | 9.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.24ms | 77.16ms | 3.5× | 6.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.31ms | 78.83ms | 3.5× | 7.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.71ms | 69.26ms | 3.3× | 8.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.04ms | 80.24ms | 2.8× | 8.5% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.35ms | 23.24ms | 1.0× | 3.1% | PASS |
| mem_reads | `oltp_range_select` | 12.38ms | 8.66ms | 0.7× | 3.0% | PASS |
| mem_reads | `oltp_sum_range` | 10.74ms | 8.85ms | 0.8× | 2.9% | PASS |
| mem_reads | `oltp_order_range` | 2.37ms | 1.99ms | 0.8× | 2.3% | PASS |
| mem_reads | `oltp_distinct_range` | 2.91ms | 2.51ms | 0.9× | 1.9% | PASS |
| mem_reads | `oltp_index_scan` | 2.60ms | 4.09ms | 1.6× | 3.3% | PASS |
| mem_reads | `select_random_points` | 16.84ms | 14.12ms | 0.8× | 3.6% | PASS |
| mem_reads | `select_random_ranges` | 2.52ms | 3.51ms | 1.4× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 2.34ms | 3.05ms | 1.3× | 3.3% | PASS |
| mem_reads | `groupby_scan` | 22.93ms | 21.27ms | 0.9× | 1.9% | PASS |
| mem_reads | `index_join` | 8.50ms | 6.58ms | 0.8× | 8.4% | PASS |
| mem_reads | `index_join_scan` | 2.84ms | 4.39ms | 1.5× | 6.1% | PASS |
| mem_reads | `types_table_scan` | 718.28ms | 752.76ms | 1.0× | 2.6% | PASS |
| mem_reads | `table_scan` | 841.07ms | 866.61ms | 1.0× | 3.1% | PASS |
| mem_reads | `oltp_read_only` | 85.27ms | 74.58ms | 0.9× | 3.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 134.99ms | 211.80ms | 1.6× | 2.1% | PASS |
| mem_writes | `oltp_insert` | 10.22ms | 23.50ms | 2.3× | 1.9% | FAIL |
| mem_writes | `oltp_update_index` | 41.62ms | 90.93ms | 2.2× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 31.50ms | 54.61ms | 1.7× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 33.43ms | 65.73ms | 2.0× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 17.96ms | 40.96ms | 2.3× | 2.0% | PASS |
| mem_writes | `types_delete_insert` | 24.74ms | 35.58ms | 1.4× | 3.6% | PASS |
| mem_writes | `oltp_read_write` | 58.53ms | 83.95ms | 1.4× | 2.9% | PASS |
| file_reads | `oltp_point_select` | 74.69ms | 35.92ms | 0.5× | 2.2% | PASS |
| file_reads | `oltp_range_select` | 17.73ms | 9.80ms | 0.6× | 2.7% | PASS |
| file_reads | `oltp_sum_range` | 16.14ms | 9.96ms | 0.6× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 3.03ms | 2.21ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_distinct_range` | 3.50ms | 2.67ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_index_scan` | 8.11ms | 5.65ms | 0.7× | 2.5% | PASS |
| file_reads | `select_random_points` | 20.95ms | 14.88ms | 0.7× | 2.9% | PASS |
| file_reads | `select_random_ranges` | 7.70ms | 4.93ms | 0.6× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 8.08ms | 4.65ms | 0.6× | 2.0% | PASS |
| file_reads | `groupby_scan` | 23.04ms | 21.25ms | 0.9× | 2.2% | PASS |
| file_reads | `index_join` | 11.42ms | 7.25ms | 0.6× | 6.1% | PASS |
| file_reads | `index_join_scan` | 3.50ms | 4.59ms | 1.3× | 1.8% | PASS |
| file_reads | `types_table_scan` | 710.33ms | 758.35ms | 1.1× | 1.9% | PASS |
| file_reads | `table_scan` | 855.10ms | 878.95ms | 1.0× | 2.5% | PASS |
| file_reads | `oltp_read_only` | 171.97ms | 98.60ms | 0.6× | 1.7% | PASS |
| file_writes | `oltp_bulk_insert` | 212.13ms | 280.01ms | 1.3× | 3.9% | PASS |
| file_writes | `oltp_insert` | 19.95ms | 48.11ms | 2.4× | 8.0% | FAIL |
| file_writes | `oltp_update_index` | 152.03ms | 179.01ms | 1.2× | 4.6% | PASS |
| file_writes | `oltp_update_non_index` | 139.18ms | 118.74ms | 0.9× | 4.3% | PASS |
| file_writes | `oltp_delete_insert` | 172.04ms | 139.02ms | 0.8× | 2.8% | PASS |
| file_writes | `oltp_write_only` | 115.54ms | 98.86ms | 0.9× | 4.3% | PASS |
| file_writes | `types_delete_insert` | 131.96ms | 69.34ms | 0.5× | 7.1% | PASS |
| file_writes | `oltp_read_write` | 160.68ms | 144.10ms | 0.9× | 2.8% | PASS |
| ac_reads | `oltp_point_select` | 41.45ms | 37.25ms | 0.9× | 2.3% | PASS |
| ac_reads | `oltp_range_select` | 15.19ms | 10.34ms | 0.7× | 2.3% | PASS |
| ac_reads | `oltp_sum_range` | 12.88ms | 10.23ms | 0.8× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 2.77ms | 2.23ms | 0.8× | 2.2% | PASS |
| ac_reads | `oltp_distinct_range` | 3.24ms | 2.71ms | 0.8× | 2.0% | PASS |
| ac_reads | `oltp_index_scan` | 4.71ms | 5.70ms | 1.2× | 2.2% | PASS |
| ac_reads | `select_random_points` | 18.66ms | 15.38ms | 0.8× | 3.8% | PASS |
| ac_reads | `select_random_ranges` | 4.25ms | 4.86ms | 1.1× | 2.2% | PASS |
| ac_reads | `covering_index_scan` | 4.49ms | 4.52ms | 1.0× | 1.8% | PASS |
| ac_reads | `groupby_scan` | 22.27ms | 20.85ms | 0.9× | 2.2% | PASS |
| ac_reads | `index_join` | 9.21ms | 6.93ms | 0.8× | 5.0% | PASS |
| ac_reads | `index_join_scan` | 3.20ms | 4.59ms | 1.4× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 752.96ms | 796.86ms | 1.1× | 2.9% | PASS |
| ac_reads | `table_scan` | 895.06ms | 867.30ms | 1.0× | 3.1% | PASS |
| ac_reads | `oltp_read_only` | 113.57ms | 95.31ms | 0.8× | 3.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.17ms | 53.56ms | 2.4× | 4.9% | PASS |
| ac_writes | `oltp_insert_ac` | 24.15ms | 61.67ms | 2.6× | 7.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.61ms | 69.53ms | 2.8× | 5.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.05ms | 57.28ms | 2.7× | 4.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.75ms | 66.91ms | 2.8× | 6.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.41ms | 65.62ms | 2.8× | 5.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.03ms | 60.13ms | 2.7× | 6.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.60ms | 69.21ms | 2.4× | 6.5% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.14ms | 39.12ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_range_select` | 17.59ms | 14.51ms | 0.8× | 3.3% | PASS |
| mem_reads | `oltp_sum_range` | 15.75ms | 13.86ms | 0.9× | 2.9% | PASS |
| mem_reads | `oltp_order_range` | 3.45ms | 3.25ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 4.41ms | 4.40ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_index_scan` | 4.07ms | 6.46ms | 1.6× | 3.9% | PASS |
| mem_reads | `select_random_points` | 22.90ms | 20.93ms | 0.9× | 3.4% | PASS |
| mem_reads | `select_random_ranges` | 3.40ms | 5.33ms | 1.6× | 2.3% | PASS |
| mem_reads | `covering_index_scan` | 4.20ms | 4.66ms | 1.1× | 3.2% | PASS |
| mem_reads | `groupby_scan` | 35.20ms | 35.66ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 10.86ms | 9.90ms | 0.9× | 2.2% | PASS |
| mem_reads | `index_join_scan` | 3.86ms | 5.37ms | 1.4× | 2.6% | PASS |
| mem_reads | `types_table_scan` | 1.08s | 1.22s | 1.1× | 2.2% | PASS |
| mem_reads | `table_scan` | 1.23s | 1.32s | 1.1× | 2.5% | PASS |
| mem_reads | `oltp_read_only` | 149.97ms | 142.20ms | 0.9× | 2.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 239.42ms | 375.88ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.45ms | 40.89ms | 2.2× | 2.2% | PASS |
| mem_writes | `oltp_update_index` | 65.84ms | 146.50ms | 2.2× | 1.9% | PASS |
| mem_writes | `oltp_update_non_index` | 49.80ms | 90.20ms | 1.8× | 3.4% | PASS |
| mem_writes | `oltp_delete_insert` | 54.55ms | 110.50ms | 2.0× | 2.6% | PASS |
| mem_writes | `oltp_write_only` | 28.17ms | 65.62ms | 2.3× | 2.6% | FAIL |
| mem_writes | `types_delete_insert` | 38.82ms | 57.27ms | 1.5× | 2.8% | PASS |
| mem_writes | `oltp_read_write` | 103.26ms | 155.68ms | 1.5× | 2.6% | PASS |
| file_reads | `oltp_point_select` | 106.32ms | 56.07ms | 0.5× | 1.7% | PASS |
| file_reads | `oltp_range_select` | 24.95ms | 16.19ms | 0.6× | 2.8% | PASS |
| file_reads | `oltp_sum_range` | 22.52ms | 15.56ms | 0.7× | 2.5% | PASS |
| file_reads | `oltp_order_range` | 4.38ms | 3.51ms | 0.8× | 3.1% | PASS |
| file_reads | `oltp_distinct_range` | 5.34ms | 4.66ms | 0.9× | 2.4% | PASS |
| file_reads | `oltp_index_scan` | 11.33ms | 8.27ms | 0.7× | 2.3% | PASS |
| file_reads | `select_random_points` | 31.80ms | 23.62ms | 0.7× | 4.0% | PASS |
| file_reads | `select_random_ranges` | 10.54ms | 7.12ms | 0.7× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 11.30ms | 6.66ms | 0.6× | 2.1% | PASS |
| file_reads | `groupby_scan` | 36.20ms | 35.82ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 15.18ms | 11.07ms | 0.7× | 2.6% | PASS |
| file_reads | `index_join_scan` | 4.86ms | 5.86ms | 1.2× | 1.7% | PASS |
| file_reads | `types_table_scan` | 1.11s | 1.22s | 1.1× | 1.9% | PASS |
| file_reads | `table_scan` | 1.26s | 1.32s | 1.0× | 3.1% | PASS |
| file_reads | `oltp_read_only` | 252.83ms | 169.42ms | 0.7× | 2.0% | PASS |
| file_writes | `oltp_bulk_insert` | 258.65ms | 390.06ms | 1.5× | 1.3% | PASS |
| file_writes | `oltp_insert` | 25.55ms | 47.68ms | 1.9× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 101.23ms | 166.21ms | 1.6× | 3.2% | PASS |
| file_writes | `oltp_update_non_index` | 92.33ms | 104.14ms | 1.1× | 9.0% | PASS |
| file_writes | `oltp_delete_insert` | 91.01ms | 124.33ms | 1.4× | 2.3% | PASS |
| file_writes | `oltp_write_only` | 60.06ms | 78.19ms | 1.3× | 2.6% | PASS |
| file_writes | `types_delete_insert` | 66.48ms | 66.08ms | 1.0× | 2.6% | PASS |
| file_writes | `oltp_read_write` | 133.29ms | 166.27ms | 1.2× | 2.7% | PASS |
| ac_reads | `oltp_point_select` | 58.64ms | 55.86ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_range_select` | 20.92ms | 16.32ms | 0.8× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 18.99ms | 15.75ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.97ms | 3.57ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.94ms | 4.70ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_index_scan` | 6.74ms | 8.21ms | 1.2× | 1.9% | PASS |
| ac_reads | `select_random_points` | 26.54ms | 23.72ms | 0.9× | 3.0% | PASS |
| ac_reads | `select_random_ranges` | 5.86ms | 7.11ms | 1.2× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 6.95ms | 6.63ms | 1.0× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 35.60ms | 35.69ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 12.61ms | 11.03ms | 0.9× | 2.4% | PASS |
| ac_reads | `index_join_scan` | 4.36ms | 5.77ms | 1.3× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 1.08s | 1.20s | 1.1× | 1.5% | PASS |
| ac_reads | `table_scan` | 1.22s | 1.31s | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_read_only` | 178.40ms | 166.78ms | 0.9× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.08ms | 59.13ms | 2.8× | 8.4% | PASS |
| ac_writes | `oltp_insert_ac` | 23.72ms | 73.57ms | 3.1× | 6.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.79ms | 83.26ms | 3.4× | 6.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.92ms | 67.33ms | 3.2× | 6.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.78ms | 75.48ms | 3.2× | 6.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.35ms | 74.99ms | 3.2× | 6.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.30ms | 71.42ms | 3.2× | 7.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.64ms | 82.31ms | 2.8× | 5.9% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.41ms | 41.71ms | 1.2× | 2.8% | PASS |
| mem_reads | `oltp_range_select` | 20.08ms | 21.41ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 18.08ms | 20.72ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 3.66ms | 3.95ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.69ms | 5.16ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 4.58ms | 6.26ms | 1.4× | 2.2% | PASS |
| mem_reads | `select_random_points` | 26.79ms | 31.37ms | 1.2× | 2.6% | PASS |
| mem_reads | `select_random_ranges` | 8.04ms | 9.27ms | 1.2× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.26ms | 4.82ms | 1.1× | 4.2% | PASS |
| mem_reads | `groupby_scan` | 37.62ms | 40.67ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 8.00ms | 10.48ms | 1.3× | 1.4% | PASS |
| mem_reads | `index_join_scan` | 3.82ms | 5.70ms | 1.5× | 3.4% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.21s | 1.1× | 1.5% | PASS |
| mem_reads | `table_scan` | 1.44s | 1.35s | 0.9× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 168.74ms | 183.49ms | 1.1× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 244.51ms | 357.06ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.60ms | 36.08ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 64.59ms | 129.84ms | 2.0× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 49.62ms | 87.91ms | 1.8× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 49.30ms | 100.79ms | 2.0× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 26.99ms | 62.70ms | 2.3× | 1.2% | FAIL |
| mem_writes | `types_delete_insert` | 32.75ms | 56.24ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 100.14ms | 166.08ms | 1.7× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 102.09ms | 58.94ms | 0.6× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 27.55ms | 23.26ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 24.62ms | 22.87ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 4.47ms | 4.25ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_distinct_range` | 5.57ms | 5.44ms | 1.0× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 11.40ms | 8.11ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 34.63ms | 34.09ms | 1.0× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 15.37ms | 11.14ms | 0.7× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 11.44ms | 6.62ms | 0.6× | 1.3% | PASS |
| file_reads | `groupby_scan` | 39.60ms | 41.45ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 12.48ms | 12.67ms | 1.0× | 2.3% | PASS |
| file_reads | `index_join_scan` | 5.12ms | 5.94ms | 1.2× | 2.5% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.22s | 1.1× | 2.4% | PASS |
| file_reads | `table_scan` | 1.20s | 1.29s | 1.1× | 1.9% | PASS |
| file_reads | `oltp_read_only` | 249.69ms | 201.09ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 257.85ms | 369.36ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 26.62ms | 43.68ms | 1.6× | 2.7% | PASS |
| file_writes | `oltp_update_index` | 107.07ms | 161.64ms | 1.5× | 1.8% | PASS |
| file_writes | `oltp_update_non_index` | 82.68ms | 108.00ms | 1.3× | 1.4% | PASS |
| file_writes | `oltp_delete_insert` | 82.37ms | 119.75ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 54.58ms | 76.19ms | 1.4× | 2.2% | PASS |
| file_writes | `types_delete_insert` | 52.55ms | 65.51ms | 1.2× | 1.2% | PASS |
| file_writes | `oltp_read_write` | 142.13ms | 187.47ms | 1.3× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 58.86ms | 60.53ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 22.23ms | 23.22ms | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_sum_range` | 20.12ms | 22.81ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 4.03ms | 4.29ms | 1.1× | 2.2% | PASS |
| ac_reads | `oltp_distinct_range` | 5.29ms | 5.54ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 7.54ms | 8.45ms | 1.1× | 2.4% | PASS |
| ac_reads | `select_random_points` | 28.98ms | 34.57ms | 1.2× | 3.2% | PASS |
| ac_reads | `select_random_ranges` | 9.97ms | 10.99ms | 1.1× | 1.8% | PASS |
| ac_reads | `covering_index_scan` | 6.80ms | 6.38ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 38.09ms | 40.85ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 9.82ms | 12.39ms | 1.3× | 2.4% | PASS |
| ac_reads | `index_join_scan` | 4.52ms | 5.93ms | 1.3× | 2.9% | PASS |
| ac_reads | `types_table_scan` | 1.12s | 1.23s | 1.1× | 3.8% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.29s | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 190.57ms | 202.76ms | 1.1× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.50ms | 60.47ms | 2.7× | 5.3% | PASS |
| ac_writes | `oltp_insert_ac` | 24.11ms | 77.44ms | 3.2× | 7.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.49ms | 88.99ms | 3.4× | 8.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.46ms | 68.36ms | 3.0× | 5.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.07ms | 78.52ms | 3.3× | 6.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.92ms | 77.89ms | 3.1× | 5.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.37ms | 71.64ms | 3.4× | 8.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.40ms | 86.30ms | 2.7× | 5.1% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 43s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 68.94ms | 130.00ms | 53.0% | 1.0% | PASS |
| `status_dirty_many_tables` | 71.40ms | 130.00ms | 54.9% | 0.8% | PASS |
| `diff_regular_working_one_table` | 63.61ms | 120.00ms | 53.0% | 0.8% | PASS |
| `diff_regular_working_many_tables` | 74.94ms | 140.00ms | 53.5% | 0.8% | PASS |
| `diff_stat_working_many_tables` | 75.39ms | 140.00ms | 53.9% | 0.7% | PASS |
| `diff_schema_working_many_tables` | 75.89ms | 140.00ms | 54.2% | 0.8% | PASS |
| `branch_list_many_branches` | 17.38ms | 35.00ms | 49.7% | 2.6% | PASS |
| `branch_create_delete` | 19.80ms | 40.00ms | 49.5% | 1.8% | PASS |
| `checkout_branch_clean` | 47.44ms | 150.00ms | 31.6% | 0.9% | PASS |
| `merge_data_no_conflicts` | 22.39ms | 50.00ms | 44.8% | 1.4% | PASS |
| `merge_schema_no_conflicts` | 17.42ms | 35.00ms | 49.8% | 1.8% | PASS |
| `merge_data_conflicts` | 25.07ms | 180.00ms | 13.9% | 1.6% | PASS |
| `merge_data_conflicts_with_resolve` | 25.30ms | 180.00ms | 14.1% | 2.1% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
