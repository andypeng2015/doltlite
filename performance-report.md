# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-10-05 20:28 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260927.320.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/37358157537)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.88s | 10.29s | 1.0× | 1.9% | **PASS** |
| Writes | 2.05s | 3.37s | 1.6× | 1.7% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.43s | 10.40s | 1.0× | 1.8% | **PASS** |
| Writes | 3.24s | 3.97s | 1.2× | 1.9% | **FAIL** |
| Autocommit writes | 891.25ms | 2.63s | 3.0× | 5.9% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.59s | 2.64s | 1.0× | 2.7% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.84s | 1.96s | 1.1× | 2.8% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.86s | 2.86s | 1.0× | 2.0% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.59s | 2.83s | 1.1× | 1.0% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 452.92ms | 754.52ms | 1.7× | 4.1% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 378.58ms | 607.67ms | 1.6× | 1.9% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 627.73ms | 1.04s | 1.7× | 1.7% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 593.42ms | 961.49ms | 1.6× | 1.0% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.83s | 2.70s | 1.0× | 2.2% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 1.92s | 1.96s | 1.0× | 1.9% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.84s | 2.84s | 1.0× | 2.1% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.84s | 2.90s | 1.0× | 1.3% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 609.31ms | 813.19ms | 1.3× | 3.0% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.04s | 1.01s | 1.0× | 4.1% | **FAIL** |
| File-backed | Writes | blobpk | 8 | 55 | 818.26ms | 1.10s | 1.3× | 1.5% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 771.71ms | 1.05s | 1.4× | 1.4% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.69s | 2.71s | 1.0× | 1.9% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.83s | 1.93s | 1.1× | 2.4% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.65s | 2.84s | 1.1× | 1.7% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.67s | 2.90s | 1.1× | 1.1% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 203.81ms | 638.83ms | 3.1× | 6.9% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 276.20ms | 747.76ms | 2.7× | 4.4% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 205.12ms | 620.73ms | 3.0× | 5.1% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 206.12ms | 622.10ms | 3.0× | 5.8% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.77ms | 30.59ms | 1.2× | 2.7% | PASS |
| mem_reads | `oltp_range_select` | 10.76ms | 11.66ms | 1.1× | 4.2% | PASS |
| mem_reads | `oltp_sum_range` | 9.72ms | 11.31ms | 1.2× | 2.5% | PASS |
| mem_reads | `oltp_order_range` | 2.66ms | 2.82ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_distinct_range` | 3.72ms | 3.95ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_index_scan` | 3.98ms | 5.54ms | 1.4× | 4.5% | PASS |
| mem_reads | `select_random_points` | 10.39ms | 11.20ms | 1.1× | 6.4% | PASS |
| mem_reads | `select_random_ranges` | 3.04ms | 4.04ms | 1.3× | 2.4% | PASS |
| mem_reads | `covering_index_scan` | 4.23ms | 4.43ms | 1.0× | 4.7% | PASS |
| mem_reads | `groupby_scan` | 30.97ms | 33.09ms | 1.1× | 1.3% | PASS |
| mem_reads | `index_join` | 5.85ms | 8.26ms | 1.4× | 4.0% | PASS |
| mem_reads | `index_join_scan` | 3.19ms | 4.46ms | 1.4× | 3.7% | PASS |
| mem_reads | `types_table_scan` | 1.09s | 1.14s | 1.0× | 1.5% | PASS |
| mem_reads | `table_scan` | 1.27s | 1.25s | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_read_only` | 109.40ms | 121.02ms | 1.1× | 3.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.00ms | 279.60ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 15.58ms | 28.89ms | 1.9× | 2.6% | PASS |
| mem_writes | `oltp_update_index` | 51.37ms | 101.48ms | 2.0× | 6.1% | PASS |
| mem_writes | `oltp_update_non_index` | 36.57ms | 62.29ms | 1.7× | 4.3% | PASS |
| mem_writes | `oltp_delete_insert` | 47.20ms | 77.03ms | 1.6× | 7.0% | PASS |
| mem_writes | `oltp_write_only` | 22.81ms | 49.30ms | 2.2× | 3.9% | PASS |
| mem_writes | `types_delete_insert` | 24.99ms | 39.54ms | 1.6× | 2.6% | PASS |
| mem_writes | `oltp_read_write` | 70.40ms | 116.39ms | 1.7× | 4.4% | PASS |
| file_reads | `oltp_point_select` | 100.21ms | 50.30ms | 0.5× | 1.4% | PASS |
| file_reads | `oltp_range_select` | 18.96ms | 13.85ms | 0.7× | 3.7% | PASS |
| file_reads | `oltp_sum_range` | 17.76ms | 13.67ms | 0.8× | 3.0% | PASS |
| file_reads | `oltp_order_range` | 3.58ms | 3.14ms | 0.9× | 2.2% | PASS |
| file_reads | `oltp_distinct_range` | 4.65ms | 4.25ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 11.93ms | 7.80ms | 0.7× | 3.4% | PASS |
| file_reads | `select_random_points` | 19.01ms | 13.46ms | 0.7× | 5.4% | PASS |
| file_reads | `select_random_ranges` | 10.58ms | 6.03ms | 0.6× | 1.8% | PASS |
| file_reads | `covering_index_scan` | 12.22ms | 6.68ms | 0.5× | 2.5% | PASS |
| file_reads | `groupby_scan` | 31.98ms | 33.49ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 10.38ms | 9.90ms | 1.0× | 3.1% | PASS |
| file_reads | `index_join_scan` | 4.25ms | 4.89ms | 1.2× | 3.8% | PASS |
| file_reads | `types_table_scan` | 1.10s | 1.14s | 1.0× | 0.8% | PASS |
| file_reads | `table_scan` | 1.27s | 1.24s | 1.0× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 217.91ms | 149.64ms | 0.7× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 196.28ms | 287.83ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 22.33ms | 32.91ms | 1.5× | 2.4% | PASS |
| file_writes | `oltp_update_index` | 79.18ms | 109.53ms | 1.4× | 3.0% | PASS |
| file_writes | `oltp_update_non_index` | 60.81ms | 73.01ms | 1.2× | 3.9% | PASS |
| file_writes | `oltp_delete_insert` | 70.21ms | 85.33ms | 1.2× | 3.0% | PASS |
| file_writes | `oltp_write_only` | 46.38ms | 56.76ms | 1.2× | 4.4% | PASS |
| file_writes | `types_delete_insert` | 41.31ms | 45.86ms | 1.1× | 3.6% | PASS |
| file_writes | `oltp_read_write` | 92.82ms | 121.94ms | 1.3× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 48.38ms | 49.47ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 13.01ms | 13.56ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 12.27ms | 13.34ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 3.03ms | 3.09ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_distinct_range` | 4.11ms | 4.20ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.78ms | 7.67ms | 1.1× | 3.8% | PASS |
| ac_reads | `select_random_points` | 13.35ms | 13.50ms | 1.0× | 7.0% | PASS |
| ac_reads | `select_random_ranges` | 5.64ms | 6.01ms | 1.1× | 1.9% | PASS |
| ac_reads | `covering_index_scan` | 7.19ms | 6.63ms | 0.9× | 2.9% | PASS |
| ac_reads | `groupby_scan` | 31.35ms | 33.48ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 7.62ms | 9.86ms | 1.3× | 4.4% | PASS |
| ac_reads | `index_join_scan` | 3.67ms | 4.88ms | 1.3× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 1.11s | 1.14s | 1.0× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.28s | 1.25s | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 148.32ms | 150.82ms | 1.0× | 1.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.86ms | 64.42ms | 2.8× | 5.9% | PASS |
| ac_writes | `oltp_insert_ac` | 24.56ms | 78.03ms | 3.2× | 8.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.89ms | 93.16ms | 3.3× | 7.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.86ms | 71.56ms | 3.1× | 5.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.50ms | 83.12ms | 3.3× | 6.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.66ms | 82.28ms | 3.1× | 9.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.36ms | 78.36ms | 3.5× | 7.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.12ms | 87.89ms | 2.8× | 6.4% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.76ms | 22.70ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 11.96ms | 9.35ms | 0.8× | 3.5% | PASS |
| mem_reads | `oltp_sum_range` | 11.86ms | 8.88ms | 0.7× | 3.8% | PASS |
| mem_reads | `oltp_order_range` | 2.34ms | 2.10ms | 0.9× | 3.8% | PASS |
| mem_reads | `oltp_distinct_range` | 3.00ms | 2.91ms | 1.0× | 2.1% | PASS |
| mem_reads | `oltp_index_scan` | 2.79ms | 3.90ms | 1.4× | 3.1% | PASS |
| mem_reads | `select_random_points` | 18.21ms | 16.14ms | 0.9× | 1.9% | PASS |
| mem_reads | `select_random_ranges` | 2.47ms | 3.59ms | 1.5× | 2.5% | PASS |
| mem_reads | `covering_index_scan` | 2.51ms | 2.87ms | 1.1× | 2.9% | PASS |
| mem_reads | `groupby_scan` | 22.26ms | 23.51ms | 1.1× | 1.4% | PASS |
| mem_reads | `index_join` | 9.50ms | 6.63ms | 0.7× | 1.9% | PASS |
| mem_reads | `index_join_scan` | 2.71ms | 4.84ms | 1.8× | 3.6% | PASS |
| mem_reads | `types_table_scan` | 759.27ms | 843.85ms | 1.1× | 3.2% | PASS |
| mem_reads | `table_scan` | 882.63ms | 927.61ms | 1.1× | 2.8% | PASS |
| mem_reads | `oltp_read_only` | 87.61ms | 84.94ms | 1.0× | 2.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 139.88ms | 205.55ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 11.21ms | 22.89ms | 2.0× | 1.7% | PASS |
| mem_writes | `oltp_update_index` | 44.95ms | 93.38ms | 2.1× | 2.3% | PASS |
| mem_writes | `oltp_update_non_index` | 33.35ms | 54.91ms | 1.6× | 2.1% | PASS |
| mem_writes | `oltp_delete_insert` | 37.74ms | 66.52ms | 1.8× | 2.4% | PASS |
| mem_writes | `oltp_write_only` | 19.83ms | 38.46ms | 1.9× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 27.44ms | 33.66ms | 1.2× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 64.18ms | 92.30ms | 1.4× | 1.8% | PASS |
| file_reads | `oltp_point_select` | 47.12ms | 29.17ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 14.96ms | 10.38ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 14.90ms | 10.04ms | 0.7× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 2.69ms | 2.29ms | 0.9× | 2.8% | PASS |
| file_reads | `oltp_distinct_range` | 3.36ms | 3.04ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 5.36ms | 5.18ms | 1.0× | 2.6% | PASS |
| file_reads | `select_random_points` | 21.73ms | 17.51ms | 0.8× | 1.9% | PASS |
| file_reads | `select_random_ranges` | 4.84ms | 4.32ms | 0.9× | 1.8% | PASS |
| file_reads | `covering_index_scan` | 5.03ms | 3.92ms | 0.8× | 1.8% | PASS |
| file_reads | `groupby_scan` | 22.44ms | 23.68ms | 1.1× | 1.1% | PASS |
| file_reads | `index_join` | 11.30ms | 7.98ms | 0.7× | 3.4% | PASS |
| file_reads | `index_join_scan` | 3.06ms | 5.13ms | 1.7× | 3.8% | PASS |
| file_reads | `types_table_scan` | 760.92ms | 826.04ms | 1.1× | 2.2% | PASS |
| file_reads | `table_scan` | 875.40ms | 917.89ms | 1.0× | 2.8% | PASS |
| file_reads | `oltp_read_only` | 123.19ms | 95.29ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 218.69ms | 275.09ms | 1.3× | 3.8% | PASS |
| file_writes | `oltp_insert` | 19.76ms | 47.26ms | 2.4× | 7.3% | FAIL |
| file_writes | `oltp_update_index` | 151.38ms | 173.62ms | 1.1× | 4.5% | PASS |
| file_writes | `oltp_update_non_index` | 124.96ms | 109.84ms | 0.9× | 3.3% | PASS |
| file_writes | `oltp_delete_insert` | 155.65ms | 129.78ms | 0.8× | 3.0% | PASS |
| file_writes | `oltp_write_only` | 101.95ms | 80.00ms | 0.8× | 0.9% | PASS |
| file_writes | `types_delete_insert` | 121.51ms | 63.97ms | 0.5× | 13.0% | PASS |
| file_writes | `oltp_read_write` | 141.99ms | 129.50ms | 0.9× | 4.8% | PASS |
| ac_reads | `oltp_point_select` | 28.73ms | 26.64ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_range_select` | 10.38ms | 9.16ms | 0.9× | 6.8% | PASS |
| ac_reads | `oltp_sum_range` | 10.54ms | 8.93ms | 0.8× | 5.1% | PASS |
| ac_reads | `oltp_order_range` | 2.17ms | 2.04ms | 0.9× | 3.1% | PASS |
| ac_reads | `oltp_distinct_range` | 2.88ms | 2.80ms | 1.0× | 3.0% | PASS |
| ac_reads | `oltp_index_scan` | 3.22ms | 4.04ms | 1.3× | 2.4% | PASS |
| ac_reads | `select_random_points` | 16.97ms | 14.77ms | 0.9× | 2.5% | PASS |
| ac_reads | `select_random_ranges` | 2.95ms | 3.65ms | 1.2× | 2.1% | PASS |
| ac_reads | `covering_index_scan` | 3.15ms | 3.12ms | 1.0× | 1.0% | PASS |
| ac_reads | `groupby_scan` | 21.36ms | 22.94ms | 1.1× | 1.1% | PASS |
| ac_reads | `index_join` | 8.62ms | 6.54ms | 0.8× | 2.9% | PASS |
| ac_reads | `index_join_scan` | 2.49ms | 4.26ms | 1.7× | 4.9% | PASS |
| ac_reads | `types_table_scan` | 750.07ms | 818.43ms | 1.1× | 1.3% | PASS |
| ac_reads | `table_scan` | 866.62ms | 912.72ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_read_only` | 96.10ms | 91.12ms | 0.9× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 32.83ms | 81.42ms | 2.5× | 6.3% | PASS |
| ac_writes | `oltp_insert_ac` | 34.89ms | 91.62ms | 2.6× | 4.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 34.48ms | 100.91ms | 2.9× | 6.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 32.51ms | 88.87ms | 2.7× | 4.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 34.95ms | 96.01ms | 2.7× | 4.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 33.90ms | 95.62ms | 2.8× | 4.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 33.80ms | 92.66ms | 2.7× | 4.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 38.84ms | 100.65ms | 2.6× | 5.8% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.03ms | 38.89ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 17.00ms | 14.53ms | 0.9× | 2.6% | PASS |
| mem_reads | `oltp_sum_range` | 15.55ms | 14.01ms | 0.9× | 2.2% | PASS |
| mem_reads | `oltp_order_range` | 3.28ms | 3.23ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_distinct_range` | 4.35ms | 4.35ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 4.00ms | 6.28ms | 1.6× | 2.0% | PASS |
| mem_reads | `select_random_points` | 21.45ms | 21.05ms | 1.0× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 3.54ms | 5.35ms | 1.5× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.25ms | 4.76ms | 1.1× | 2.4% | PASS |
| mem_reads | `groupby_scan` | 33.64ms | 34.31ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 9.91ms | 8.88ms | 0.9× | 2.8% | PASS |
| mem_reads | `index_join_scan` | 3.88ms | 5.84ms | 1.5× | 2.9% | PASS |
| mem_reads | `types_table_scan` | 1.22s | 1.23s | 1.0× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.35s | 1.33s | 1.0× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 134.05ms | 136.79ms | 1.0× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 247.94ms | 373.77ms | 1.5× | 0.5% | PASS |
| mem_writes | `oltp_insert` | 19.06ms | 40.13ms | 2.1× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 69.47ms | 149.47ms | 2.2× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 51.48ms | 89.09ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 56.81ms | 108.70ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 31.11ms | 66.91ms | 2.2× | 2.0% | PASS |
| mem_writes | `types_delete_insert` | 41.37ms | 56.26ms | 1.4× | 2.4% | PASS |
| mem_writes | `oltp_read_write` | 110.50ms | 157.23ms | 1.4× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 111.51ms | 59.95ms | 0.5× | 2.1% | PASS |
| file_reads | `oltp_range_select` | 25.58ms | 17.99ms | 0.7× | 2.4% | PASS |
| file_reads | `oltp_sum_range` | 23.55ms | 16.64ms | 0.7× | 2.2% | PASS |
| file_reads | `oltp_order_range` | 4.41ms | 3.68ms | 0.8× | 3.8% | PASS |
| file_reads | `oltp_distinct_range` | 5.51ms | 4.85ms | 0.9× | 3.0% | PASS |
| file_reads | `oltp_index_scan` | 11.95ms | 8.75ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 30.83ms | 24.36ms | 0.8× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 11.41ms | 7.73ms | 0.7× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 12.39ms | 7.04ms | 0.6× | 1.6% | PASS |
| file_reads | `groupby_scan` | 36.92ms | 36.00ms | 1.0× | 1.6% | PASS |
| file_reads | `index_join` | 14.90ms | 11.24ms | 0.8× | 3.3% | PASS |
| file_reads | `index_join_scan` | 4.89ms | 6.12ms | 1.3× | 4.2% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 1.23s | 1.30s | 1.1× | 1.9% | PASS |
| file_reads | `oltp_read_only` | 253.14ms | 172.28ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 269.56ms | 384.10ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.02ms | 46.60ms | 1.8× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 95.18ms | 154.51ms | 1.6× | 1.3% | PASS |
| file_writes | `oltp_update_non_index` | 92.08ms | 97.97ms | 1.1× | 8.6% | PASS |
| file_writes | `oltp_delete_insert` | 86.53ms | 117.28ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 56.74ms | 72.61ms | 1.3× | 2.2% | PASS |
| file_writes | `types_delete_insert` | 63.34ms | 61.64ms | 1.0× | 1.8% | PASS |
| file_writes | `oltp_read_write` | 128.81ms | 160.48ms | 1.2× | 1.3% | PASS |
| ac_reads | `oltp_point_select` | 59.62ms | 59.81ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 20.00ms | 17.10ms | 0.9× | 3.6% | PASS |
| ac_reads | `oltp_sum_range` | 17.87ms | 16.10ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.87ms | 3.64ms | 0.9× | 2.4% | PASS |
| ac_reads | `oltp_distinct_range` | 5.79ms | 5.54ms | 1.0× | 4.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.61ms | 8.68ms | 1.3× | 1.7% | PASS |
| ac_reads | `select_random_points` | 23.98ms | 24.51ms | 1.0× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 6.00ms | 7.51ms | 1.3× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 7.26ms | 7.07ms | 1.0× | 1.8% | PASS |
| ac_reads | `groupby_scan` | 35.94ms | 36.08ms | 1.0× | 1.1% | PASS |
| ac_reads | `index_join` | 12.52ms | 11.82ms | 0.9× | 3.1% | PASS |
| ac_reads | `index_join_scan` | 4.24ms | 6.02ms | 1.4× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 1.07s | 1.18s | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 178.00ms | 173.28ms | 1.0× | 0.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.05ms | 62.85ms | 2.7× | 4.0% | PASS |
| ac_writes | `oltp_insert_ac` | 24.80ms | 78.60ms | 3.2× | 6.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.50ms | 89.79ms | 3.4× | 4.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.95ms | 70.91ms | 3.1× | 5.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.41ms | 80.36ms | 3.2× | 5.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.50ms | 79.98ms | 3.0× | 6.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.24ms | 71.20ms | 3.1× | 6.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.68ms | 87.04ms | 2.7× | 5.1% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.84ms | 40.70ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 19.29ms | 21.03ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_sum_range` | 17.54ms | 20.61ms | 1.2× | 0.9% | PASS |
| mem_reads | `oltp_order_range` | 3.46ms | 3.83ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.62ms | 5.01ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.38ms | 5.95ms | 1.4× | 1.4% | PASS |
| mem_reads | `select_random_points` | 27.19ms | 32.24ms | 1.2× | 1.0% | PASS |
| mem_reads | `select_random_ranges` | 7.60ms | 9.05ms | 1.2× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.25ms | 4.32ms | 1.0× | 1.3% | PASS |
| mem_reads | `groupby_scan` | 36.31ms | 39.53ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 7.96ms | 10.38ms | 1.3× | 0.9% | PASS |
| mem_reads | `index_join_scan` | 3.85ms | 5.45ms | 1.4× | 1.4% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.18s | 1.1× | 0.3% | PASS |
| mem_reads | `table_scan` | 1.21s | 1.28s | 1.1× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 148.49ms | 171.03ms | 1.2× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 251.51ms | 352.46ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 19.21ms | 34.92ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 65.00ms | 125.30ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_non_index` | 50.11ms | 83.70ms | 1.7× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 48.94ms | 94.53ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 26.30ms | 57.47ms | 2.2× | 1.1% | PASS |
| mem_writes | `types_delete_insert` | 32.14ms | 52.61ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_read_write` | 100.20ms | 160.50ms | 1.6× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 109.00ms | 60.99ms | 0.6× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 27.33ms | 23.44ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 25.89ms | 23.09ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 4.35ms | 4.15ms | 1.0× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 5.58ms | 5.37ms | 1.0× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 12.17ms | 8.51ms | 0.7× | 2.3% | PASS |
| file_reads | `select_random_points` | 37.76ms | 36.23ms | 1.0× | 1.3% | PASS |
| file_reads | `select_random_ranges` | 15.87ms | 11.42ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.79ms | 6.72ms | 0.6× | 1.1% | PASS |
| file_reads | `groupby_scan` | 37.30ms | 40.09ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 12.34ms | 12.44ms | 1.0× | 1.4% | PASS |
| file_reads | `index_join_scan` | 4.93ms | 5.92ms | 1.2× | 2.3% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.18s | 1.1× | 0.3% | PASS |
| file_reads | `table_scan` | 1.21s | 1.28s | 1.1× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 264.25ms | 203.17ms | 0.8× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 267.44ms | 364.53ms | 1.4× | 0.8% | PASS |
| file_writes | `oltp_insert` | 26.14ms | 40.76ms | 1.6× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 95.26ms | 138.45ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_update_non_index` | 77.19ms | 97.47ms | 1.3× | 1.3% | PASS |
| file_writes | `oltp_delete_insert` | 76.58ms | 107.68ms | 1.4× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 50.98ms | 68.15ms | 1.3× | 1.9% | PASS |
| file_writes | `types_delete_insert` | 50.16ms | 59.73ms | 1.2× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 127.97ms | 171.27ms | 1.3× | 1.3% | PASS |
| ac_reads | `oltp_point_select` | 58.05ms | 61.21ms | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 22.38ms | 23.58ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 20.52ms | 23.04ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 3.84ms | 4.15ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 5.00ms | 5.38ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 7.07ms | 8.53ms | 1.2× | 2.0% | PASS |
| ac_reads | `select_random_points` | 31.28ms | 36.30ms | 1.2× | 1.4% | PASS |
| ac_reads | `select_random_ranges` | 10.43ms | 11.41ms | 1.1× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 6.84ms | 6.75ms | 1.0× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 36.68ms | 40.03ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 9.57ms | 12.53ms | 1.3× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 4.34ms | 5.97ms | 1.4× | 2.3% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.18s | 1.1× | 0.3% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 0.6% | PASS |
| ac_reads | `oltp_read_only` | 187.71ms | 202.84ms | 1.1× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.16ms | 61.25ms | 2.6× | 5.7% | PASS |
| ac_writes | `oltp_insert_ac` | 24.45ms | 78.42ms | 3.2× | 6.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.29ms | 88.17ms | 3.1× | 5.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.03ms | 71.69ms | 3.0× | 5.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.28ms | 80.89ms | 3.2× | 5.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.68ms | 80.46ms | 3.1× | 6.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.80ms | 70.30ms | 3.2× | 6.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.44ms | 90.92ms | 2.7× | 3.7% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 47s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 65.49ms | 130.00ms | 50.4% | 2.1% | PASS |
| `status_dirty_many_tables` | 68.02ms | 130.00ms | 52.3% | 1.4% | PASS |
| `diff_regular_working_one_table` | 59.58ms | 120.00ms | 49.6% | 1.2% | PASS |
| `diff_regular_working_many_tables` | 69.74ms | 140.00ms | 49.8% | 1.5% | PASS |
| `diff_stat_working_many_tables` | 69.82ms | 140.00ms | 49.9% | 1.7% | PASS |
| `diff_schema_working_many_tables` | 70.91ms | 140.00ms | 50.7% | 1.8% | PASS |
| `branch_list_many_branches` | 17.57ms | 35.00ms | 50.2% | 3.0% | PASS |
| `branch_create_delete` | 26.66ms | 40.00ms | 66.7% | 2.7% | PASS |
| `checkout_branch_clean` | 103.10ms | 150.00ms | 68.7% | 6.6% | PASS |
| `merge_data_no_conflicts` | 32.17ms | 50.00ms | 64.3% | 2.5% | PASS |
| `merge_schema_no_conflicts` | 18.18ms | 35.00ms | 51.9% | 3.9% | PASS |
| `merge_data_conflicts` | 24.51ms | 180.00ms | 13.6% | 2.9% | PASS |
| `merge_data_conflicts_with_resolve` | 24.64ms | 180.00ms | 13.7% | 2.5% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
