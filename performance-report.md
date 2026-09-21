# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-21 17:34 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/35622569207)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.23s | 10.54s | 1.0× | 2.0% | **PASS** |
| Writes | 2.07s | 3.38s | 1.6× | 1.7% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.25s | 10.71s | 1.0× | 1.7% | **PASS** |
| Writes | 4.37s | 4.34s | 1.0× | 2.2% | **PASS** |
| Autocommit writes | 3.11s | 8.55s | 2.7× | 6.6% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.65s | 2.75s | 1.0× | 2.7% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.83s | 1.94s | 1.1× | 2.4% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.80s | 2.83s | 1.0× | 1.2% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.95s | 3.02s | 1.0× | 1.7% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 495.59ms | 811.53ms | 1.6× | 2.4% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 368.54ms | 593.60ms | 1.6× | 2.3% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 616.66ms | 1.02s | 1.7× | 1.7% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 593.27ms | 947.90ms | 1.6× | 1.2% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.91s | 2.76s | 0.9× | 2.0% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 1.89s | 1.94s | 1.0× | 2.5% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.06s | 2.90s | 0.9× | 1.4% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.40s | 3.11s | 0.9× | 1.4% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 668.09ms | 889.99ms | 1.3× | 2.6% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 2.03s | 1.27s | 0.6× | 36.8% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 863.58ms | 1.11s | 1.3× | 1.8% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 807.36ms | 1.07s | 1.3× | 1.5% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.84s | 2.86s | 1.0× | 2.6% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.85s | 1.95s | 1.1× | 2.7% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.88s | 2.89s | 1.0× | 1.8% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 3.09s | 3.09s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 206.54ms | 773.54ms | 3.7× | 4.2% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 2.53s | 6.39s | 2.5× | 50.5% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 212.23ms | 753.09ms | 3.5× | 7.4% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 164.44ms | 629.73ms | 3.8× | 5.5% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.73ms | 33.42ms | 1.2× | 2.7% | PASS |
| mem_reads | `oltp_range_select` | 13.44ms | 13.33ms | 1.0× | 3.8% | PASS |
| mem_reads | `oltp_sum_range` | 12.10ms | 13.68ms | 1.1× | 6.4% | PASS |
| mem_reads | `oltp_order_range` | 3.12ms | 3.46ms | 1.1× | 3.0% | PASS |
| mem_reads | `oltp_distinct_range` | 4.28ms | 4.67ms | 1.1× | 2.7% | PASS |
| mem_reads | `oltp_index_scan` | 4.41ms | 6.25ms | 1.4× | 3.2% | PASS |
| mem_reads | `select_random_points` | 12.71ms | 13.12ms | 1.0× | 6.3% | PASS |
| mem_reads | `select_random_ranges` | 3.49ms | 4.45ms | 1.3× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 4.79ms | 5.16ms | 1.1× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 34.13ms | 36.02ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 6.41ms | 9.50ms | 1.5× | 2.8% | PASS |
| mem_reads | `index_join_scan` | 3.77ms | 5.33ms | 1.4× | 6.9% | PASS |
| mem_reads | `types_table_scan` | 1.11s | 1.18s | 1.1× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.29s | 1.29s | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_read_only` | 113.14ms | 126.56ms | 1.1× | 1.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 188.79ms | 285.06ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 16.44ms | 30.91ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_update_index` | 57.42ms | 111.29ms | 1.9× | 2.9% | PASS |
| mem_writes | `oltp_update_non_index` | 40.58ms | 67.75ms | 1.7× | 2.9% | PASS |
| mem_writes | `oltp_delete_insert` | 53.08ms | 86.00ms | 1.6× | 2.4% | PASS |
| mem_writes | `oltp_write_only` | 25.75ms | 53.82ms | 2.1× | 2.5% | PASS |
| mem_writes | `types_delete_insert` | 28.00ms | 42.69ms | 1.5× | 2.5% | PASS |
| mem_writes | `oltp_read_write` | 85.55ms | 134.02ms | 1.6× | 2.4% | PASS |
| file_reads | `oltp_point_select` | 106.01ms | 54.97ms | 0.5× | 1.5% | PASS |
| file_reads | `oltp_range_select` | 22.28ms | 15.35ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 19.94ms | 15.06ms | 0.8× | 2.5% | PASS |
| file_reads | `oltp_order_range` | 4.45ms | 4.04ms | 0.9× | 4.9% | PASS |
| file_reads | `oltp_distinct_range` | 5.66ms | 5.16ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 13.33ms | 8.85ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 21.74ms | 16.63ms | 0.8× | 4.1% | PASS |
| file_reads | `select_random_ranges` | 11.87ms | 6.77ms | 0.6× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 13.84ms | 7.46ms | 0.5× | 2.3% | PASS |
| file_reads | `groupby_scan` | 35.93ms | 37.30ms | 1.0× | 1.6% | PASS |
| file_reads | `index_join` | 11.83ms | 11.50ms | 1.0× | 3.5% | PASS |
| file_reads | `index_join_scan` | 4.72ms | 5.44ms | 1.2× | 4.0% | PASS |
| file_reads | `types_table_scan` | 1.11s | 1.14s | 1.0× | 1.7% | PASS |
| file_reads | `table_scan` | 1.29s | 1.27s | 1.0× | 3.1% | PASS |
| file_reads | `oltp_read_only` | 233.23ms | 160.91ms | 0.7× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 212.95ms | 312.63ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_insert` | 24.52ms | 36.14ms | 1.5× | 2.6% | PASS |
| file_writes | `oltp_update_index` | 84.41ms | 117.81ms | 1.4× | 2.4% | PASS |
| file_writes | `oltp_update_non_index` | 65.19ms | 78.16ms | 1.2× | 2.1% | PASS |
| file_writes | `oltp_delete_insert` | 76.74ms | 94.73ms | 1.2× | 2.7% | PASS |
| file_writes | `oltp_write_only` | 52.71ms | 65.27ms | 1.2× | 2.9% | PASS |
| file_writes | `types_delete_insert` | 44.91ms | 49.55ms | 1.1× | 3.2% | PASS |
| file_writes | `oltp_read_write` | 106.66ms | 135.70ms | 1.3× | 2.9% | PASS |
| ac_reads | `oltp_point_select` | 49.65ms | 50.91ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_range_select` | 15.28ms | 14.88ms | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_sum_range` | 13.30ms | 14.19ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_order_range` | 3.94ms | 3.94ms | 1.0× | 5.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.96ms | 5.03ms | 1.0× | 5.2% | PASS |
| ac_reads | `oltp_index_scan` | 7.13ms | 7.98ms | 1.1× | 2.9% | PASS |
| ac_reads | `select_random_points` | 15.37ms | 15.14ms | 1.0× | 4.5% | PASS |
| ac_reads | `select_random_ranges` | 6.09ms | 6.32ms | 1.0× | 2.4% | PASS |
| ac_reads | `covering_index_scan` | 8.04ms | 6.95ms | 0.9× | 2.0% | PASS |
| ac_reads | `groupby_scan` | 34.31ms | 36.35ms | 1.1× | 1.4% | PASS |
| ac_reads | `index_join` | 8.83ms | 11.38ms | 1.3× | 2.6% | PASS |
| ac_reads | `index_join_scan` | 4.51ms | 5.88ms | 1.3× | 5.1% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.21s | 1.0× | 2.5% | PASS |
| ac_reads | `table_scan` | 1.33s | 1.30s | 1.0× | 3.1% | PASS |
| ac_reads | `oltp_read_only` | 168.78ms | 171.96ms | 1.0× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.96ms | 83.09ms | 3.5× | 5.8% | PASS |
| ac_writes | `oltp_insert_ac` | 26.18ms | 98.36ms | 3.8× | 6.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.62ms | 107.67ms | 3.9× | 3.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.97ms | 85.66ms | 3.7× | 3.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.65ms | 97.41ms | 4.0× | 3.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.13ms | 100.69ms | 3.9× | 4.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.91ms | 92.41ms | 4.0× | 6.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.13ms | 108.24ms | 3.4× | 4.0% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.97ms | 23.14ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 11.91ms | 9.23ms | 0.8× | 3.1% | PASS |
| mem_reads | `oltp_sum_range` | 12.07ms | 9.14ms | 0.8× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 2.37ms | 2.14ms | 0.9× | 2.5% | PASS |
| mem_reads | `oltp_distinct_range` | 3.02ms | 2.92ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_index_scan` | 2.79ms | 4.05ms | 1.5× | 3.3% | PASS |
| mem_reads | `select_random_points` | 17.91ms | 16.19ms | 0.9× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 2.51ms | 3.62ms | 1.4× | 2.9% | PASS |
| mem_reads | `covering_index_scan` | 2.53ms | 2.76ms | 1.1× | 2.8% | PASS |
| mem_reads | `groupby_scan` | 22.16ms | 23.48ms | 1.1× | 1.6% | PASS |
| mem_reads | `index_join` | 9.16ms | 6.49ms | 0.7× | 2.6% | PASS |
| mem_reads | `index_join_scan` | 2.66ms | 4.83ms | 1.8× | 2.8% | PASS |
| mem_reads | `types_table_scan` | 756.68ms | 834.28ms | 1.1× | 1.2% | PASS |
| mem_reads | `table_scan` | 874.43ms | 909.59ms | 1.0× | 2.1% | PASS |
| mem_reads | `oltp_read_only` | 87.30ms | 84.04ms | 1.0× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 141.02ms | 205.55ms | 1.5× | 1.4% | PASS |
| mem_writes | `oltp_insert` | 11.18ms | 22.93ms | 2.1× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 43.83ms | 91.39ms | 2.1× | 2.4% | PASS |
| mem_writes | `oltp_update_non_index` | 30.33ms | 52.96ms | 1.7× | 2.5% | PASS |
| mem_writes | `oltp_delete_insert` | 35.42ms | 63.78ms | 1.8× | 2.5% | PASS |
| mem_writes | `oltp_write_only` | 19.55ms | 38.14ms | 2.0× | 1.8% | PASS |
| mem_writes | `types_delete_insert` | 26.73ms | 32.44ms | 1.2× | 2.5% | PASS |
| mem_writes | `oltp_read_write` | 60.49ms | 86.41ms | 1.4× | 2.2% | PASS |
| file_reads | `oltp_point_select` | 45.62ms | 28.09ms | 0.6× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 13.96ms | 9.95ms | 0.7× | 3.1% | PASS |
| file_reads | `oltp_sum_range` | 14.33ms | 9.89ms | 0.7× | 2.5% | PASS |
| file_reads | `oltp_order_range` | 2.73ms | 2.30ms | 0.8× | 2.7% | PASS |
| file_reads | `oltp_distinct_range` | 3.36ms | 3.04ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 5.19ms | 4.95ms | 1.0× | 4.8% | PASS |
| file_reads | `select_random_points` | 20.33ms | 16.59ms | 0.8× | 2.9% | PASS |
| file_reads | `select_random_ranges` | 4.71ms | 4.07ms | 0.9× | 2.5% | PASS |
| file_reads | `covering_index_scan` | 4.84ms | 3.70ms | 0.8× | 6.3% | PASS |
| file_reads | `groupby_scan` | 22.26ms | 23.42ms | 1.1× | 1.3% | PASS |
| file_reads | `index_join` | 10.80ms | 7.88ms | 0.7× | 3.4% | PASS |
| file_reads | `index_join_scan` | 2.63ms | 4.29ms | 1.6× | 4.7% | PASS |
| file_reads | `types_table_scan` | 748.91ms | 819.39ms | 1.1× | 1.0% | PASS |
| file_reads | `table_scan` | 869.78ms | 907.82ms | 1.0× | 2.1% | PASS |
| file_reads | `oltp_read_only` | 118.31ms | 92.07ms | 0.8× | 1.4% | PASS |
| file_writes | `oltp_bulk_insert` | 270.88ms | 325.24ms | 1.2× | 17.8% | PASS |
| file_writes | `oltp_insert` | 77.88ms | 68.87ms | 0.9× | 75.4% | PASS |
| file_writes | `oltp_update_index` | 381.86ms | 201.92ms | 0.5× | 36.1% | PASS |
| file_writes | `oltp_update_non_index` | 318.00ms | 138.46ms | 0.4× | 42.6% | PASS |
| file_writes | `oltp_delete_insert` | 260.97ms | 147.45ms | 0.6× | 29.2% | PASS |
| file_writes | `oltp_write_only` | 244.63ms | 113.86ms | 0.5× | 48.5% | PASS |
| file_writes | `types_delete_insert` | 214.19ms | 99.82ms | 0.5× | 34.7% | PASS |
| file_writes | `oltp_read_write` | 264.76ms | 173.94ms | 0.7× | 37.6% | PASS |
| ac_reads | `oltp_point_select` | 31.24ms | 28.60ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 12.87ms | 10.01ms | 0.8× | 2.9% | PASS |
| ac_reads | `oltp_sum_range` | 12.71ms | 9.65ms | 0.8× | 2.7% | PASS |
| ac_reads | `oltp_order_range` | 2.44ms | 2.14ms | 0.9× | 2.9% | PASS |
| ac_reads | `oltp_distinct_range` | 2.99ms | 2.88ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 3.47ms | 4.34ms | 1.3× | 3.2% | PASS |
| ac_reads | `select_random_points` | 17.72ms | 15.30ms | 0.9× | 3.1% | PASS |
| ac_reads | `select_random_ranges` | 3.10ms | 3.85ms | 1.2× | 2.9% | PASS |
| ac_reads | `covering_index_scan` | 3.19ms | 3.20ms | 1.0× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 21.52ms | 23.02ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 9.47ms | 6.85ms | 0.7× | 3.7% | PASS |
| ac_reads | `index_join_scan` | 3.12ms | 5.49ms | 1.8× | 4.9% | PASS |
| ac_reads | `types_table_scan` | 751.36ms | 832.11ms | 1.1× | 1.5% | PASS |
| ac_reads | `table_scan` | 872.35ms | 908.72ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_read_only` | 98.54ms | 93.18ms | 0.9× | 1.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 271.70ms | 787.39ms | 2.9× | 50.2% | PASS |
| ac_writes | `oltp_insert_ac` | 287.46ms | 676.59ms | 2.4× | 68.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 314.67ms | 891.29ms | 2.8× | 60.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 265.21ms | 666.60ms | 2.5× | 45.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 365.35ms | 823.37ms | 2.3× | 50.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 285.14ms | 728.25ms | 2.6× | 50.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 378.97ms | 972.12ms | 2.6× | 56.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 362.73ms | 846.64ms | 2.3× | 49.9% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.30ms | 39.43ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 17.03ms | 14.85ms | 0.9× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 15.65ms | 13.83ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 3.25ms | 3.15ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.33ms | 4.31ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 4.08ms | 6.45ms | 1.6× | 1.6% | PASS |
| mem_reads | `select_random_points` | 22.74ms | 21.38ms | 0.9× | 2.6% | PASS |
| mem_reads | `select_random_ranges` | 3.57ms | 5.36ms | 1.5× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.86ms | 1.2× | 0.9% | PASS |
| mem_reads | `groupby_scan` | 34.83ms | 34.72ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 10.43ms | 9.71ms | 0.9× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.76ms | 5.46ms | 1.5× | 3.0% | PASS |
| mem_reads | `types_table_scan` | 1.17s | 1.21s | 1.0× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.32s | 1.32s | 1.0× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 149.92ms | 141.03ms | 0.9× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 247.13ms | 372.86ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 19.04ms | 39.65ms | 2.1× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 68.76ms | 147.29ms | 2.1× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 51.72ms | 87.25ms | 1.7× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 54.85ms | 105.99ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 28.87ms | 63.22ms | 2.2× | 1.8% | PASS |
| mem_writes | `types_delete_insert` | 40.01ms | 54.58ms | 1.4× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 106.28ms | 152.31ms | 1.4× | 2.7% | PASS |
| file_reads | `oltp_point_select` | 116.50ms | 60.31ms | 0.5× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 25.84ms | 17.16ms | 0.7× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 24.50ms | 16.32ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.41ms | 3.48ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 5.50ms | 4.68ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 12.12ms | 8.74ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 34.90ms | 24.79ms | 0.7× | 3.8% | PASS |
| file_reads | `select_random_ranges` | 11.36ms | 7.50ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 12.28ms | 6.99ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 36.34ms | 35.28ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 16.19ms | 11.22ms | 0.7× | 2.5% | PASS |
| file_reads | `index_join_scan` | 5.09ms | 5.87ms | 1.2× | 2.5% | PASS |
| file_reads | `types_table_scan` | 1.17s | 1.21s | 1.0× | 0.6% | PASS |
| file_reads | `table_scan` | 1.32s | 1.31s | 1.0× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 267.37ms | 173.38ms | 0.6× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 269.10ms | 385.82ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.60ms | 46.60ms | 1.8× | 1.4% | PASS |
| file_writes | `oltp_update_index` | 106.04ms | 160.39ms | 1.5× | 1.9% | PASS |
| file_writes | `oltp_update_non_index` | 94.57ms | 100.31ms | 1.1× | 9.3% | PASS |
| file_writes | `oltp_delete_insert` | 93.50ms | 119.62ms | 1.3× | 2.0% | PASS |
| file_writes | `oltp_write_only` | 60.96ms | 73.42ms | 1.2× | 1.9% | PASS |
| file_writes | `types_delete_insert` | 68.07ms | 63.93ms | 0.9× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 144.75ms | 163.72ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_point_select` | 65.19ms | 60.11ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_range_select` | 20.93ms | 17.21ms | 0.8× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 19.29ms | 16.27ms | 0.8× | 2.1% | PASS |
| ac_reads | `oltp_order_range` | 3.92ms | 3.52ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.96ms | 4.69ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 7.04ms | 8.70ms | 1.2× | 1.9% | PASS |
| ac_reads | `select_random_points` | 28.59ms | 24.62ms | 0.9× | 2.5% | PASS |
| ac_reads | `select_random_ranges` | 6.36ms | 7.43ms | 1.2× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 7.41ms | 6.99ms | 0.9× | 2.2% | PASS |
| ac_reads | `groupby_scan` | 35.55ms | 35.07ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 13.47ms | 11.09ms | 0.8× | 1.9% | PASS |
| ac_reads | `index_join_scan` | 4.49ms | 5.80ms | 1.3× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.20s | 1.0× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.31s | 1.31s | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 179.34ms | 169.32ms | 0.9× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.07ms | 76.03ms | 3.3× | 4.2% | PASS |
| ac_writes | `oltp_insert_ac` | 26.81ms | 95.51ms | 3.6× | 9.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.34ms | 104.09ms | 3.7× | 7.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.69ms | 88.85ms | 3.7× | 7.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.57ms | 100.75ms | 3.7× | 5.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.68ms | 97.18ms | 3.8× | 7.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.34ms | 90.22ms | 3.7× | 7.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.73ms | 100.46ms | 3.1× | 7.7% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.03ms | 38.31ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 20.18ms | 20.92ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 17.96ms | 20.02ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 3.68ms | 3.91ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 4.79ms | 5.08ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.55ms | 5.75ms | 1.3× | 2.0% | PASS |
| mem_reads | `select_random_points` | 27.81ms | 30.50ms | 1.1× | 1.9% | PASS |
| mem_reads | `select_random_ranges` | 7.47ms | 8.36ms | 1.1× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 4.30ms | 4.37ms | 1.0× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 38.85ms | 41.92ms | 1.1× | 1.4% | PASS |
| mem_reads | `index_join` | 7.86ms | 10.14ms | 1.3× | 1.1% | PASS |
| mem_reads | `index_join_scan` | 4.03ms | 5.85ms | 1.5× | 2.0% | PASS |
| mem_reads | `types_table_scan` | 1.20s | 1.27s | 1.1× | 3.0% | PASS |
| mem_reads | `table_scan` | 1.42s | 1.38s | 1.0× | 4.6% | PASS |
| mem_reads | `oltp_read_only` | 157.96ms | 168.79ms | 1.1× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 248.86ms | 340.60ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 19.32ms | 35.12ms | 1.8× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 66.64ms | 126.95ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 51.85ms | 82.01ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 49.84ms | 96.64ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 27.16ms | 59.50ms | 2.2× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 32.16ms | 52.26ms | 1.6× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 97.44ms | 154.82ms | 1.6× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 129.77ms | 61.33ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 30.24ms | 23.16ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 28.93ms | 22.62ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_order_range` | 4.78ms | 4.20ms | 0.9× | 0.8% | PASS |
| file_reads | `oltp_distinct_range` | 5.92ms | 5.37ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 14.59ms | 8.48ms | 0.6× | 1.9% | PASS |
| file_reads | `select_random_points` | 38.31ms | 33.20ms | 0.9× | 2.0% | PASS |
| file_reads | `select_random_ranges` | 17.38ms | 10.84ms | 0.6× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 14.41ms | 7.06ms | 0.5× | 1.1% | PASS |
| file_reads | `groupby_scan` | 40.20ms | 42.33ms | 1.1× | 1.3% | PASS |
| file_reads | `index_join` | 13.32ms | 12.25ms | 0.9× | 2.1% | PASS |
| file_reads | `index_join_scan` | 5.18ms | 6.22ms | 1.2× | 2.0% | PASS |
| file_reads | `types_table_scan` | 1.19s | 1.25s | 1.0× | 2.3% | PASS |
| file_reads | `table_scan` | 1.56s | 1.42s | 0.9× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 304.27ms | 206.88ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 263.64ms | 355.23ms | 1.3× | 1.1% | PASS |
| file_writes | `oltp_insert` | 27.29ms | 42.15ms | 1.5× | 2.3% | PASS |
| file_writes | `oltp_update_index` | 104.58ms | 148.82ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 81.72ms | 97.97ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 81.98ms | 112.65ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 54.53ms | 72.50ms | 1.3× | 1.9% | PASS |
| file_writes | `types_delete_insert` | 53.92ms | 61.70ms | 1.1× | 1.2% | PASS |
| file_writes | `oltp_read_write` | 139.70ms | 177.29ms | 1.3× | 1.7% | PASS |
| ac_reads | `oltp_point_select` | 66.35ms | 62.63ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 24.03ms | 23.27ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_sum_range` | 22.20ms | 22.61ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 4.16ms | 4.19ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_distinct_range` | 5.27ms | 5.36ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 8.01ms | 8.39ms | 1.0× | 1.5% | PASS |
| ac_reads | `select_random_points` | 31.07ms | 33.00ms | 1.1× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 10.83ms | 10.82ms | 1.0× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 7.84ms | 7.03ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 39.25ms | 42.14ms | 1.1× | 1.1% | PASS |
| ac_reads | `index_join` | 10.10ms | 12.32ms | 1.2× | 1.6% | PASS |
| ac_reads | `index_join_scan` | 4.66ms | 6.43ms | 1.4× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 1.18s | 1.25s | 1.1× | 2.1% | PASS |
| ac_reads | `table_scan` | 1.47s | 1.40s | 0.9× | 4.2% | PASS |
| ac_reads | `oltp_read_only` | 201.41ms | 202.45ms | 1.0× | 1.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.84ms | 61.84ms | 3.5× | 6.6% | PASS |
| ac_writes | `oltp_insert_ac` | 20.85ms | 80.10ms | 3.8× | 5.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.42ms | 90.51ms | 4.2× | 4.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 18.80ms | 72.68ms | 3.9× | 6.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 19.16ms | 80.73ms | 4.2× | 4.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.75ms | 80.86ms | 4.1× | 5.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 17.89ms | 72.34ms | 4.0× | 8.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.73ms | 90.67ms | 3.2× | 5.1% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 16s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 96.44ms | 130.00ms | 74.2% | 0.9% | PASS |
| `status_dirty_many_tables` | 100.30ms | 130.00ms | 77.2% | 0.6% | PASS |
| `diff_regular_working_one_table` | 91.33ms | 120.00ms | 76.1% | 0.6% | PASS |
| `diff_regular_working_many_tables` | 105.19ms | 140.00ms | 75.1% | 0.5% | PASS |
| `diff_stat_working_many_tables` | 104.52ms | 140.00ms | 74.7% | 0.6% | PASS |
| `diff_schema_working_many_tables` | 106.00ms | 140.00ms | 75.7% | 0.6% | PASS |
| `branch_list_many_branches` | 23.36ms | 35.00ms | 66.7% | 1.3% | PASS |
| `branch_create_delete` | 26.44ms | 40.00ms | 66.1% | 1.8% | PASS |
| `checkout_branch_clean` | 63.30ms | 150.00ms | 42.2% | 1.7% | PASS |
| `merge_data_no_conflicts` | 30.59ms | 50.00ms | 61.2% | 2.3% | PASS |
| `merge_schema_no_conflicts` | 23.17ms | 35.00ms | 66.2% | 1.9% | PASS |
| `merge_data_conflicts` | 33.16ms | 180.00ms | 18.4% | 1.4% | PASS |
| `merge_data_conflicts_with_resolve` | 33.45ms | 180.00ms | 18.6% | 1.7% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
