# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-02 17:35 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260927.320.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/37030377481)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.94s | 10.46s | 1.1× | 1.5% | **PASS** |
| Writes | 2.08s | 3.40s | 1.6× | 1.2% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.05s | 10.76s | 1.0× | 1.4% | **PASS** |
| Writes | 3.46s | 4.14s | 1.2× | 2.2% | **PASS** |
| Autocommit writes | 861.89ms | 2.57s | 3.0× | 6.1% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.49s | 2.61s | 1.0× | 1.7% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.73s | 2.79s | 1.0× | 1.8% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.08s | 2.22s | 1.1× | 0.8% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.63s | 2.84s | 1.1× | 1.5% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 454.98ms | 745.21ms | 1.6× | 1.2% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 608.61ms | 1.02s | 1.7× | 1.4% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 410.27ms | 665.92ms | 1.6× | 1.1% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 607.51ms | 972.60ms | 1.6× | 1.6% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.93s | 2.75s | 0.9× | 1.4% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.02s | 2.85s | 0.9× | 1.7% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.16s | 2.24s | 1.0× | 1.0% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.94s | 2.92s | 1.0× | 1.5% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 632.39ms | 832.68ms | 1.3× | 2.1% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 980.42ms | 1.13s | 1.2× | 4.1% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.07s | 1.12s | 1.0× | 2.7% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 783.16ms | 1.06s | 1.4× | 1.6% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.70s | 2.72s | 1.0× | 1.3% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.89s | 2.86s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.11s | 2.24s | 1.1× | 0.9% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.69s | 2.90s | 1.1× | 1.4% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 213.90ms | 643.99ms | 3.0× | 7.9% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 235.57ms | 721.08ms | 3.1× | 9.9% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 209.43ms | 572.99ms | 2.7× | 5.1% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 202.99ms | 633.35ms | 3.1× | 4.9% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 26.17ms | 31.05ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 11.31ms | 11.81ms | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_sum_range` | 10.54ms | 11.57ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 2.72ms | 2.85ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 3.75ms | 3.99ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.07ms | 5.67ms | 1.4× | 2.1% | PASS |
| mem_reads | `select_random_points` | 11.28ms | 11.33ms | 1.0× | 3.7% | PASS |
| mem_reads | `select_random_ranges` | 3.11ms | 4.02ms | 1.3× | 1.7% | PASS |
| mem_reads | `covering_index_scan` | 4.23ms | 4.47ms | 1.1× | 2.3% | PASS |
| mem_reads | `groupby_scan` | 30.87ms | 33.14ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 5.88ms | 8.35ms | 1.4× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 3.44ms | 4.68ms | 1.4× | 3.1% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.13s | 1.1× | 1.2% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.23s | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 105.61ms | 118.90ms | 1.1× | 1.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 182.49ms | 276.79ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 15.38ms | 28.27ms | 1.8× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 51.58ms | 98.05ms | 1.9× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 35.83ms | 60.53ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 46.47ms | 75.41ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 22.67ms | 47.84ms | 2.1× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 25.68ms | 39.47ms | 1.5× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 74.87ms | 118.85ms | 1.6× | 1.5% | PASS |
| file_reads | `oltp_point_select` | 101.12ms | 50.63ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 19.16ms | 13.87ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 18.43ms | 13.80ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 3.63ms | 3.20ms | 0.9× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.69ms | 4.29ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 12.14ms | 7.98ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 19.91ms | 13.71ms | 0.7× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 10.67ms | 6.04ms | 0.6× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.98ms | 6.69ms | 0.6× | 2.3% | PASS |
| file_reads | `groupby_scan` | 31.74ms | 33.41ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 10.63ms | 10.04ms | 0.9× | 2.9% | PASS |
| file_reads | `index_join_scan` | 4.63ms | 5.10ms | 1.1× | 2.3% | PASS |
| file_reads | `types_table_scan` | 1.14s | 1.16s | 1.0× | 0.3% | PASS |
| file_reads | `table_scan` | 1.31s | 1.27s | 1.0× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 224.37ms | 156.67ms | 0.7× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 196.46ms | 289.88ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_insert` | 22.81ms | 32.73ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_update_index` | 84.22ms | 113.59ms | 1.3× | 2.2% | PASS |
| file_writes | `oltp_update_non_index` | 62.97ms | 74.55ms | 1.2× | 2.5% | PASS |
| file_writes | `oltp_delete_insert` | 72.49ms | 88.29ms | 1.2× | 2.3% | PASS |
| file_writes | `oltp_write_only` | 48.34ms | 57.98ms | 1.2× | 2.8% | PASS |
| file_writes | `types_delete_insert` | 42.34ms | 46.13ms | 1.1× | 1.4% | PASS |
| file_writes | `oltp_read_write` | 102.76ms | 129.53ms | 1.3× | 1.9% | PASS |
| ac_reads | `oltp_point_select` | 51.16ms | 52.22ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 14.42ms | 13.99ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 13.21ms | 13.69ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 3.12ms | 3.16ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.16ms | 4.32ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 7.11ms | 8.09ms | 1.1× | 1.5% | PASS |
| ac_reads | `select_random_points` | 14.88ms | 14.19ms | 1.0× | 3.9% | PASS |
| ac_reads | `select_random_ranges` | 5.78ms | 6.07ms | 1.0× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 7.42ms | 6.78ms | 0.9× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 31.40ms | 33.64ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 8.20ms | 10.34ms | 1.3× | 2.1% | PASS |
| ac_reads | `index_join_scan` | 3.93ms | 5.03ms | 1.3× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 1.09s | 1.14s | 1.0× | 1.3% | PASS |
| ac_reads | `table_scan` | 1.29s | 1.25s | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 146.41ms | 150.73ms | 1.0× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.77ms | 65.19ms | 2.7× | 7.5% | PASS |
| ac_writes | `oltp_insert_ac` | 25.81ms | 78.13ms | 3.0× | 5.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.30ms | 90.57ms | 3.2× | 6.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.74ms | 74.39ms | 3.0× | 9.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.53ms | 84.84ms | 3.2× | 8.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.28ms | 81.92ms | 3.0× | 4.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.24ms | 74.48ms | 3.2× | 8.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.22ms | 94.47ms | 2.8× | 9.6% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.64ms | 38.41ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 16.56ms | 14.14ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 15.48ms | 13.83ms | 0.9× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 3.25ms | 3.15ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.36ms | 4.27ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 4.04ms | 6.29ms | 1.6× | 1.3% | PASS |
| mem_reads | `select_random_points` | 22.44ms | 21.38ms | 1.0× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 3.68ms | 5.33ms | 1.5× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 4.21ms | 4.66ms | 1.1× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 34.94ms | 34.77ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 10.47ms | 9.25ms | 0.9× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 3.80ms | 5.42ms | 1.4× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 1.14s | 1.19s | 1.0× | 1.8% | PASS |
| mem_reads | `table_scan` | 1.29s | 1.29s | 1.0× | 2.0% | PASS |
| mem_reads | `oltp_read_only` | 143.72ms | 139.23ms | 1.0× | 1.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 244.15ms | 373.65ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.28ms | 39.16ms | 2.1× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 67.41ms | 145.08ms | 2.2× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 49.36ms | 86.87ms | 1.8× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 55.15ms | 106.39ms | 1.9× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 28.79ms | 63.20ms | 2.2× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 40.05ms | 55.34ms | 1.4× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 105.43ms | 150.25ms | 1.4× | 1.8% | PASS |
| file_reads | `oltp_point_select` | 111.65ms | 57.70ms | 0.5× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 24.43ms | 16.30ms | 0.7× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 23.41ms | 16.06ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 4.24ms | 3.48ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.27ms | 4.63ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 11.86ms | 8.59ms | 0.7× | 1.0% | PASS |
| file_reads | `select_random_points` | 32.64ms | 24.94ms | 0.8× | 3.3% | PASS |
| file_reads | `select_random_ranges` | 11.37ms | 7.39ms | 0.6× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 12.16ms | 6.92ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 35.90ms | 35.15ms | 1.0× | 0.6% | PASS |
| file_reads | `index_join` | 15.25ms | 11.05ms | 0.7× | 1.7% | PASS |
| file_reads | `index_join_scan` | 4.84ms | 5.89ms | 1.2× | 2.3% | PASS |
| file_reads | `types_table_scan` | 1.14s | 1.19s | 1.0× | 1.7% | PASS |
| file_reads | `table_scan` | 1.33s | 1.30s | 1.0× | 3.5% | PASS |
| file_reads | `oltp_read_only` | 251.72ms | 167.17ms | 0.7× | 1.4% | PASS |
| file_writes | `oltp_bulk_insert` | 271.19ms | 388.26ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.41ms | 46.48ms | 1.8× | 2.2% | PASS |
| file_writes | `oltp_update_index` | 135.32ms | 163.56ms | 1.2× | 5.1% | PASS |
| file_writes | `oltp_update_non_index` | 105.72ms | 103.45ms | 1.0× | 14.0% | PASS |
| file_writes | `oltp_delete_insert` | 102.62ms | 123.77ms | 1.2× | 2.2% | PASS |
| file_writes | `oltp_write_only` | 88.62ms | 75.83ms | 0.9× | 9.1% | PASS |
| file_writes | `types_delete_insert` | 77.65ms | 66.22ms | 0.9× | 3.2% | PASS |
| file_writes | `oltp_read_write` | 172.91ms | 165.35ms | 1.0× | 8.4% | PASS |
| ac_reads | `oltp_point_select` | 64.40ms | 59.45ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 20.09ms | 16.46ms | 0.8× | 2.2% | PASS |
| ac_reads | `oltp_sum_range` | 19.38ms | 16.46ms | 0.8× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.82ms | 3.50ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.88ms | 4.62ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.99ms | 8.62ms | 1.2× | 1.6% | PASS |
| ac_reads | `select_random_points` | 26.59ms | 24.52ms | 0.9× | 2.6% | PASS |
| ac_reads | `select_random_ranges` | 6.38ms | 7.39ms | 1.2× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 7.23ms | 6.94ms | 1.0× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 35.35ms | 35.24ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 12.81ms | 11.03ms | 0.9× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 4.35ms | 5.89ms | 1.4× | 1.8% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.19s | 1.0× | 1.6% | PASS |
| ac_reads | `table_scan` | 1.33s | 1.29s | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 180.09ms | 168.22ms | 0.9× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 25.42ms | 70.06ms | 2.8× | 8.5% | PASS |
| ac_writes | `oltp_insert_ac` | 29.56ms | 89.67ms | 3.0× | 9.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 32.18ms | 107.37ms | 3.3× | 11.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 25.19ms | 84.24ms | 3.3× | 7.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 29.20ms | 91.30ms | 3.1× | 11.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.74ms | 93.06ms | 3.2× | 8.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 27.69ms | 85.66ms | 3.1× | 12.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 37.60ms | 99.73ms | 2.7× | 10.2% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 26.57ms | 26.15ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_range_select` | 11.64ms | 10.39ms | 0.9× | 0.8% | PASS |
| mem_reads | `oltp_sum_range` | 11.46ms | 9.99ms | 0.9× | 0.7% | PASS |
| mem_reads | `oltp_order_range` | 2.54ms | 2.52ms | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_distinct_range` | 3.30ms | 3.40ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 3.14ms | 4.42ms | 1.4× | 1.3% | PASS |
| mem_reads | `select_random_points` | 17.47ms | 16.44ms | 0.9× | 1.2% | PASS |
| mem_reads | `select_random_ranges` | 2.54ms | 3.98ms | 1.6× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 2.94ms | 3.26ms | 1.1× | 2.7% | PASS |
| mem_reads | `groupby_scan` | 27.02ms | 27.13ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 8.89ms | 6.89ms | 0.8× | 0.9% | PASS |
| mem_reads | `index_join_scan` | 2.70ms | 4.47ms | 1.7× | 1.2% | PASS |
| mem_reads | `types_table_scan` | 870.69ms | 951.47ms | 1.1× | 0.3% | PASS |
| mem_reads | `table_scan` | 989.65ms | 1.05s | 1.1× | 0.2% | PASS |
| mem_reads | `oltp_read_only` | 98.67ms | 98.28ms | 1.0× | 0.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 165.40ms | 239.86ms | 1.5× | 0.4% | PASS |
| mem_writes | `oltp_insert` | 13.18ms | 26.12ms | 2.0× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 47.10ms | 98.31ms | 2.1× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 34.18ms | 57.80ms | 1.7× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 37.59ms | 70.02ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 19.73ms | 41.09ms | 2.1× | 1.9% | PASS |
| mem_writes | `types_delete_insert` | 27.14ms | 35.42ms | 1.3× | 1.1% | PASS |
| mem_writes | `oltp_read_write` | 65.95ms | 97.30ms | 1.5× | 1.0% | PASS |
| file_reads | `oltp_point_select` | 52.63ms | 32.20ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 14.51ms | 11.26ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_sum_range` | 14.36ms | 10.89ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 2.88ms | 2.63ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 3.62ms | 3.44ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 5.88ms | 5.27ms | 0.9× | 3.2% | PASS |
| file_reads | `select_random_points` | 20.55ms | 17.30ms | 0.8× | 1.1% | PASS |
| file_reads | `select_random_ranges` | 5.33ms | 4.73ms | 0.9× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 5.75ms | 4.05ms | 0.7× | 2.9% | PASS |
| file_reads | `groupby_scan` | 27.52ms | 27.38ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 10.70ms | 8.08ms | 0.8× | 2.9% | PASS |
| file_reads | `index_join_scan` | 3.15ms | 4.83ms | 1.5× | 1.3% | PASS |
| file_reads | `types_table_scan` | 869.21ms | 948.25ms | 1.1× | 0.3% | PASS |
| file_reads | `table_scan` | 987.08ms | 1.05s | 1.1× | 0.3% | PASS |
| file_reads | `oltp_read_only` | 135.22ms | 107.06ms | 0.8× | 0.5% | PASS |
| file_writes | `oltp_bulk_insert` | 231.81ms | 305.66ms | 1.3× | 1.3% | PASS |
| file_writes | `oltp_insert` | 26.56ms | 51.54ms | 1.9× | 2.4% | PASS |
| file_writes | `oltp_update_index` | 151.83ms | 181.58ms | 1.2× | 4.6% | PASS |
| file_writes | `oltp_update_non_index` | 126.44ms | 116.63ms | 0.9× | 2.2% | PASS |
| file_writes | `oltp_delete_insert` | 151.77ms | 136.71ms | 0.9× | 3.9% | PASS |
| file_writes | `oltp_write_only` | 108.37ms | 95.63ms | 0.9× | 3.1% | PASS |
| file_writes | `types_delete_insert` | 115.38ms | 77.66ms | 0.7× | 6.6% | PASS |
| file_writes | `oltp_read_write` | 155.66ms | 153.06ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 35.04ms | 32.22ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 12.71ms | 11.18ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_sum_range` | 12.53ms | 10.77ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_order_range` | 2.73ms | 2.60ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 3.48ms | 3.46ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 4.17ms | 5.17ms | 1.2× | 1.8% | PASS |
| ac_reads | `select_random_points` | 19.04ms | 17.40ms | 0.9× | 1.6% | PASS |
| ac_reads | `select_random_ranges` | 3.57ms | 4.72ms | 1.3× | 1.7% | PASS |
| ac_reads | `covering_index_scan` | 4.01ms | 4.15ms | 1.0× | 2.5% | PASS |
| ac_reads | `groupby_scan` | 27.41ms | 27.32ms | 1.0× | 0.4% | PASS |
| ac_reads | `index_join` | 10.01ms | 7.90ms | 0.8× | 2.8% | PASS |
| ac_reads | `index_join_scan` | 3.05ms | 4.83ms | 1.6× | 1.3% | PASS |
| ac_reads | `types_table_scan` | 869.05ms | 949.02ms | 1.1× | 0.2% | PASS |
| ac_reads | `table_scan` | 988.87ms | 1.05s | 1.1× | 0.3% | PASS |
| ac_reads | `oltp_read_only` | 110.35ms | 107.11ms | 1.0× | 0.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.78ms | 61.87ms | 2.5× | 3.1% | PASS |
| ac_writes | `oltp_insert_ac` | 26.33ms | 71.50ms | 2.7× | 6.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.55ms | 79.03ms | 3.0× | 5.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.57ms | 68.22ms | 2.8× | 5.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.32ms | 74.51ms | 2.8× | 4.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.82ms | 73.16ms | 2.8× | 5.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.08ms | 69.02ms | 2.8× | 5.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.98ms | 75.69ms | 2.5× | 4.3% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.10ms | 42.56ms | 1.2× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 21.00ms | 21.52ms | 1.0× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 18.90ms | 20.97ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 3.67ms | 3.93ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.80ms | 5.16ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_index_scan` | 4.83ms | 6.62ms | 1.4× | 2.7% | PASS |
| mem_reads | `select_random_points` | 30.76ms | 34.31ms | 1.1× | 2.1% | PASS |
| mem_reads | `select_random_ranges` | 8.24ms | 9.13ms | 1.1× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 4.32ms | 4.75ms | 1.1× | 2.8% | PASS |
| mem_reads | `groupby_scan` | 37.60ms | 40.14ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 8.14ms | 11.03ms | 1.4× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 4.17ms | 5.66ms | 1.4× | 2.8% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.19s | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.23s | 1.28s | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 151.10ms | 172.07ms | 1.1× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 252.50ms | 354.80ms | 1.4× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 19.20ms | 35.30ms | 1.8× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 66.75ms | 125.90ms | 1.9× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 51.78ms | 84.14ms | 1.6× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 50.03ms | 96.18ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 27.11ms | 58.34ms | 2.2× | 1.9% | PASS |
| mem_writes | `types_delete_insert` | 33.72ms | 53.38ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 106.43ms | 164.56ms | 1.5× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 110.66ms | 61.19ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 28.39ms | 23.34ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 26.54ms | 22.92ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 4.50ms | 4.15ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 5.68ms | 5.42ms | 1.0× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 12.57ms | 8.41ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 38.13ms | 36.28ms | 1.0× | 1.9% | PASS |
| file_reads | `select_random_ranges` | 15.96ms | 11.23ms | 0.7× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 12.26ms | 6.70ms | 0.5× | 1.6% | PASS |
| file_reads | `groupby_scan` | 38.38ms | 40.28ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 12.88ms | 12.71ms | 1.0× | 2.2% | PASS |
| file_reads | `index_join_scan` | 5.09ms | 5.98ms | 1.2× | 2.5% | PASS |
| file_reads | `types_table_scan` | 1.08s | 1.19s | 1.1× | 1.3% | PASS |
| file_reads | `table_scan` | 1.28s | 1.29s | 1.0× | 2.6% | PASS |
| file_reads | `oltp_read_only` | 265.53ms | 203.74ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 266.82ms | 368.10ms | 1.4× | 1.3% | PASS |
| file_writes | `oltp_insert` | 26.74ms | 41.08ms | 1.5× | 2.1% | PASS |
| file_writes | `oltp_update_index` | 98.31ms | 140.98ms | 1.4× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 78.82ms | 98.41ms | 1.2× | 1.4% | PASS |
| file_writes | `oltp_delete_insert` | 78.23ms | 109.06ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 52.64ms | 68.73ms | 1.3× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 51.26ms | 60.58ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 130.34ms | 173.38ms | 1.3× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 59.07ms | 60.82ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 23.24ms | 23.45ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 20.88ms | 22.98ms | 1.1× | 1.4% | PASS |
| ac_reads | `oltp_order_range` | 3.92ms | 4.18ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 5.07ms | 5.45ms | 1.1× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 7.22ms | 8.55ms | 1.2× | 2.0% | PASS |
| ac_reads | `select_random_points` | 31.78ms | 36.08ms | 1.1× | 1.9% | PASS |
| ac_reads | `select_random_ranges` | 10.47ms | 11.29ms | 1.1× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 6.92ms | 6.73ms | 1.0× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 37.51ms | 40.27ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 9.60ms | 12.33ms | 1.3× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 4.38ms | 5.97ms | 1.4× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.18s | 1.1× | 1.0% | PASS |
| ac_reads | `table_scan` | 1.21s | 1.28s | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 192.39ms | 204.82ms | 1.1× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.30ms | 63.62ms | 2.7× | 5.0% | PASS |
| ac_writes | `oltp_insert_ac` | 25.34ms | 80.45ms | 3.2× | 4.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.12ms | 89.42ms | 3.3× | 3.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.75ms | 72.48ms | 3.2× | 6.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.20ms | 83.19ms | 3.3× | 3.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.89ms | 81.21ms | 3.3× | 5.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.16ms | 73.59ms | 3.3× | 6.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.24ms | 89.39ms | 2.8× | 4.7% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 22s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 106.84ms | 130.00ms | 82.2% | 0.4% | PASS |
| `status_dirty_many_tables` | 110.76ms | 130.00ms | 85.2% | 0.4% | PASS |
| `diff_regular_working_one_table` | 102.13ms | 120.00ms | 85.1% | 0.3% | PASS |
| `diff_regular_working_many_tables` | 115.68ms | 140.00ms | 82.6% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 115.58ms | 140.00ms | 82.6% | 0.4% | PASS |
| `diff_schema_working_many_tables` | 116.43ms | 140.00ms | 83.2% | 0.3% | PASS |
| `branch_list_many_branches` | 24.47ms | 35.00ms | 69.9% | 1.1% | PASS |
| `branch_create_delete` | 26.73ms | 40.00ms | 66.8% | 1.1% | PASS |
| `checkout_branch_clean` | 65.52ms | 150.00ms | 43.7% | 0.8% | PASS |
| `merge_data_no_conflicts` | 31.03ms | 50.00ms | 62.1% | 1.5% | PASS |
| `merge_schema_no_conflicts` | 22.84ms | 35.00ms | 65.3% | 1.1% | PASS |
| `merge_data_conflicts` | 35.15ms | 180.00ms | 19.5% | 1.1% | PASS |
| `merge_data_conflicts_with_resolve` | 35.08ms | 180.00ms | 19.5% | 0.9% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
