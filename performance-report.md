# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-09-17 16:02 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/35233193453)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.01s | 9.34s | 1.0× | 1.9% | **PASS** |
| Writes | 1.79s | 2.91s | 1.6× | 1.7% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.82s | 9.53s | 1.0× | 1.5% | **PASS** |
| Writes | 4.45s | 4.14s | 0.9× | 2.4% | **FAIL** |
| Autocommit writes | 3.51s | 10.00s | 2.8× | 7.4% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.62s | 2.74s | 1.0× | 1.0% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.85s | 1.94s | 1.0× | 2.6% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 1.70s | 1.77s | 1.0× | 2.7% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.85s | 2.89s | 1.0× | 1.5% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 436.39ms | 713.11ms | 1.6× | 0.9% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 376.80ms | 606.90ms | 1.6× | 2.0% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 361.63ms | 594.28ms | 1.6× | 2.6% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 618.58ms | 998.60ms | 1.6× | 1.7% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.88s | 2.80s | 1.0× | 1.1% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 1.93s | 1.96s | 1.0× | 2.0% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 1.78s | 1.75s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.22s | 3.02s | 0.9× | 1.5% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 594.03ms | 775.21ms | 1.3× | 1.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 2.05s | 1.33s | 0.6× | 34.2% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 977.52ms | 942.58ms | 1.0× | 4.0% | **FAIL** |
| File-backed | Writes | compositepk | 8 | 55 | 830.43ms | 1.10s | 1.3× | 1.6% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.69s | 2.80s | 1.0× | 1.0% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.84s | 1.96s | 1.1× | 2.2% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 1.66s | 1.75s | 1.1× | 1.5% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.98s | 2.97s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 144.70ms | 572.67ms | 4.0× | 5.0% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 2.84s | 7.88s | 2.8× | 59.4% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 296.98ms | 738.61ms | 2.5× | 8.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 225.63ms | 799.37ms | 3.5× | 6.1% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.89ms | 28.11ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_range_select` | 10.52ms | 10.99ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_sum_range` | 9.67ms | 10.89ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 2.65ms | 2.83ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.73ms | 3.98ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.88ms | 4.84ms | 1.2× | 1.6% | PASS |
| mem_reads | `select_random_points` | 10.21ms | 11.03ms | 1.1× | 2.5% | PASS |
| mem_reads | `select_random_ranges` | 3.01ms | 4.02ms | 1.3× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 4.25ms | 4.19ms | 1.0× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 32.32ms | 35.08ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 5.76ms | 7.50ms | 1.3× | 0.9% | PASS |
| mem_reads | `index_join_scan` | 3.24ms | 4.56ms | 1.4× | 1.0% | PASS |
| mem_reads | `types_table_scan` | 1.12s | 1.19s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.29s | 1.31s | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 102.22ms | 114.06ms | 1.1× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 183.62ms | 264.02ms | 1.4× | 1.7% | PASS |
| mem_writes | `oltp_insert` | 15.64ms | 28.15ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_update_index` | 49.42ms | 95.11ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_non_index` | 34.75ms | 58.76ms | 1.7× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 43.86ms | 72.95ms | 1.7× | 0.6% | PASS |
| mem_writes | `oltp_write_only` | 21.48ms | 48.03ms | 2.2× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 23.99ms | 37.79ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 63.63ms | 108.30ms | 1.7× | 0.8% | PASS |
| file_reads | `oltp_point_select` | 119.13ms | 50.91ms | 0.4× | 0.6% | PASS |
| file_reads | `oltp_range_select` | 19.40ms | 13.45ms | 0.7× | 0.8% | PASS |
| file_reads | `oltp_sum_range` | 18.70ms | 13.18ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 3.49ms | 3.14ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_distinct_range` | 4.61ms | 4.24ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 13.24ms | 7.31ms | 0.6× | 1.1% | PASS |
| file_reads | `select_random_points` | 18.87ms | 13.40ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_ranges` | 12.36ms | 6.29ms | 0.5× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 13.94ms | 6.64ms | 0.5× | 1.0% | PASS |
| file_reads | `groupby_scan` | 32.98ms | 35.29ms | 1.1× | 1.1% | PASS |
| file_reads | `index_join` | 10.85ms | 9.10ms | 0.8× | 0.8% | PASS |
| file_reads | `index_join_scan` | 4.22ms | 4.91ms | 1.2× | 1.6% | PASS |
| file_reads | `types_table_scan` | 1.11s | 1.18s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.26s | 1.30s | 1.0× | 0.3% | PASS |
| file_reads | `oltp_read_only` | 238.36ms | 147.24ms | 0.6× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 195.86ms | 271.43ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 21.84ms | 32.32ms | 1.5× | 2.7% | PASS |
| file_writes | `oltp_update_index` | 77.25ms | 104.20ms | 1.3× | 1.1% | PASS |
| file_writes | `oltp_update_non_index` | 59.35ms | 71.08ms | 1.2× | 1.9% | PASS |
| file_writes | `oltp_delete_insert` | 66.48ms | 80.90ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 44.19ms | 54.82ms | 1.2× | 2.1% | PASS |
| file_writes | `types_delete_insert` | 40.04ms | 43.76ms | 1.1× | 1.2% | PASS |
| file_writes | `oltp_read_write` | 89.02ms | 116.72ms | 1.3× | 1.5% | PASS |
| ac_reads | `oltp_point_select` | 55.17ms | 51.08ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 13.89ms | 13.46ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_sum_range` | 12.89ms | 13.23ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_order_range` | 3.04ms | 3.15ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 4.04ms | 4.23ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 7.13ms | 7.30ms | 1.0× | 1.4% | PASS |
| ac_reads | `select_random_points` | 13.45ms | 13.53ms | 1.0× | 1.1% | PASS |
| ac_reads | `select_random_ranges` | 6.17ms | 6.32ms | 1.0× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 7.60ms | 6.65ms | 0.9× | 1.0% | PASS |
| ac_reads | `groupby_scan` | 32.42ms | 35.26ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 7.66ms | 9.16ms | 1.2× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 3.78ms | 4.94ms | 1.3× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 1.11s | 1.18s | 1.1× | 0.4% | PASS |
| ac_reads | `table_scan` | 1.26s | 1.30s | 1.0× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 148.40ms | 148.03ms | 1.0× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 15.77ms | 58.21ms | 3.7× | 5.5% | PASS |
| ac_writes | `oltp_insert_ac` | 18.14ms | 71.13ms | 3.9× | 5.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 19.38ms | 83.11ms | 4.3× | 5.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.02ms | 66.20ms | 4.1× | 5.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 18.27ms | 75.42ms | 4.1× | 5.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 17.52ms | 73.94ms | 4.2× | 3.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 15.61ms | 64.11ms | 4.1× | 3.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 23.99ms | 80.55ms | 3.4× | 4.1% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.65ms | 23.06ms | 1.0× | 2.6% | PASS |
| mem_reads | `oltp_range_select` | 11.28ms | 9.05ms | 0.8× | 4.8% | PASS |
| mem_reads | `oltp_sum_range` | 11.31ms | 8.83ms | 0.8× | 6.4% | PASS |
| mem_reads | `oltp_order_range` | 2.33ms | 2.08ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_distinct_range` | 2.90ms | 2.86ms | 1.0× | 2.6% | PASS |
| mem_reads | `oltp_index_scan` | 2.74ms | 3.81ms | 1.4× | 2.6% | PASS |
| mem_reads | `select_random_points` | 17.60ms | 15.26ms | 0.9× | 2.6% | PASS |
| mem_reads | `select_random_ranges` | 2.42ms | 3.37ms | 1.4× | 2.6% | PASS |
| mem_reads | `covering_index_scan` | 2.48ms | 2.69ms | 1.1× | 2.4% | PASS |
| mem_reads | `groupby_scan` | 22.05ms | 23.26ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 9.23ms | 6.49ms | 0.7× | 2.9% | PASS |
| mem_reads | `index_join_scan` | 2.56ms | 4.76ms | 1.9× | 4.2% | PASS |
| mem_reads | `types_table_scan` | 758.47ms | 829.13ms | 1.1× | 1.8% | PASS |
| mem_reads | `table_scan` | 887.44ms | 916.63ms | 1.0× | 2.5% | PASS |
| mem_reads | `oltp_read_only` | 89.87ms | 85.89ms | 1.0× | 2.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 140.07ms | 206.64ms | 1.5× | 0.5% | PASS |
| mem_writes | `oltp_insert` | 11.27ms | 23.24ms | 2.1× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 46.28ms | 96.50ms | 2.1× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 32.72ms | 54.55ms | 1.7× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 36.85ms | 65.49ms | 1.8× | 2.4% | PASS |
| mem_writes | `oltp_write_only` | 19.29ms | 37.84ms | 2.0× | 2.1% | PASS |
| mem_writes | `types_delete_insert` | 26.93ms | 32.97ms | 1.2× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 63.39ms | 89.68ms | 1.4× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 46.52ms | 29.05ms | 0.6× | 1.9% | PASS |
| file_reads | `oltp_range_select` | 15.09ms | 10.29ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_sum_range` | 15.06ms | 10.15ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 2.69ms | 2.28ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_distinct_range` | 3.32ms | 2.99ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 5.30ms | 5.00ms | 0.9× | 2.7% | PASS |
| file_reads | `select_random_points` | 21.50ms | 17.43ms | 0.8× | 2.0% | PASS |
| file_reads | `select_random_ranges` | 4.79ms | 4.21ms | 0.9× | 2.0% | PASS |
| file_reads | `covering_index_scan` | 4.86ms | 3.64ms | 0.7× | 4.8% | PASS |
| file_reads | `groupby_scan` | 22.10ms | 23.28ms | 1.1× | 1.4% | PASS |
| file_reads | `index_join` | 10.65ms | 7.55ms | 0.7× | 3.6% | PASS |
| file_reads | `index_join_scan` | 3.06ms | 5.10ms | 1.7× | 2.6% | PASS |
| file_reads | `types_table_scan` | 758.40ms | 825.22ms | 1.1× | 1.9% | PASS |
| file_reads | `table_scan` | 891.45ms | 918.26ms | 1.0× | 4.0% | PASS |
| file_reads | `oltp_read_only` | 128.31ms | 97.37ms | 0.8× | 2.0% | PASS |
| file_writes | `oltp_bulk_insert` | 377.61ms | 353.25ms | 0.9× | 23.0% | PASS |
| file_writes | `oltp_insert` | 96.23ms | 94.66ms | 1.0× | 69.7% | PASS |
| file_writes | `oltp_update_index` | 289.63ms | 191.06ms | 0.7× | 26.3% | PASS |
| file_writes | `oltp_update_non_index` | 278.15ms | 136.11ms | 0.5× | 44.2% | PASS |
| file_writes | `oltp_delete_insert` | 225.06ms | 139.78ms | 0.6× | 28.4% | PASS |
| file_writes | `oltp_write_only` | 224.94ms | 119.70ms | 0.5× | 39.2% | PASS |
| file_writes | `types_delete_insert` | 227.84ms | 126.15ms | 0.6× | 36.0% | PASS |
| file_writes | `oltp_read_write` | 325.59ms | 164.96ms | 0.5× | 32.3% | PASS |
| ac_reads | `oltp_point_select` | 32.02ms | 29.00ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_range_select` | 13.36ms | 10.11ms | 0.8× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 13.38ms | 10.05ms | 0.8× | 1.8% | PASS |
| ac_reads | `oltp_order_range` | 2.59ms | 2.29ms | 0.9× | 2.9% | PASS |
| ac_reads | `oltp_distinct_range` | 3.21ms | 3.04ms | 0.9× | 2.8% | PASS |
| ac_reads | `oltp_index_scan` | 3.85ms | 5.08ms | 1.3× | 3.7% | PASS |
| ac_reads | `select_random_points` | 19.90ms | 17.22ms | 0.9× | 2.7% | PASS |
| ac_reads | `select_random_ranges` | 3.28ms | 4.16ms | 1.3× | 1.7% | PASS |
| ac_reads | `covering_index_scan` | 3.44ms | 3.89ms | 1.1× | 2.2% | PASS |
| ac_reads | `groupby_scan` | 22.22ms | 23.52ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 10.76ms | 7.97ms | 0.7× | 3.4% | PASS |
| ac_reads | `index_join_scan` | 3.04ms | 5.31ms | 1.7× | 4.3% | PASS |
| ac_reads | `types_table_scan` | 758.41ms | 829.10ms | 1.1× | 1.7% | PASS |
| ac_reads | `table_scan` | 845.00ms | 915.08ms | 1.1× | 2.7% | PASS |
| ac_reads | `oltp_read_only` | 104.82ms | 96.39ms | 0.9× | 2.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 268.28ms | 920.60ms | 3.4× | 59.4% | PASS |
| ac_writes | `oltp_insert_ac` | 370.10ms | 935.14ms | 2.5× | 49.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 367.89ms | 1.27s | 3.5× | 56.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 383.37ms | 1.01s | 2.6× | 52.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 322.16ms | 920.23ms | 2.9× | 62.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 395.33ms | 1.04s | 2.6× | 59.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 418.42ms | 943.97ms | 2.3× | 64.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 314.38ms | 848.53ms | 2.7× | 60.7% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.72ms | 23.37ms | 1.0× | 3.7% | PASS |
| mem_reads | `oltp_range_select` | 9.96ms | 8.47ms | 0.9× | 2.8% | PASS |
| mem_reads | `oltp_sum_range` | 9.49ms | 8.49ms | 0.9× | 2.4% | PASS |
| mem_reads | `oltp_order_range` | 2.19ms | 2.01ms | 0.9× | 2.1% | PASS |
| mem_reads | `oltp_distinct_range` | 2.58ms | 2.47ms | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_index_scan` | 2.54ms | 4.21ms | 1.7× | 3.3% | PASS |
| mem_reads | `select_random_points` | 16.01ms | 14.33ms | 0.9× | 4.1% | PASS |
| mem_reads | `select_random_ranges` | 2.37ms | 3.51ms | 1.5× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 2.37ms | 3.31ms | 1.4× | 3.7% | PASS |
| mem_reads | `groupby_scan` | 20.43ms | 20.14ms | 1.0× | 1.3% | PASS |
| mem_reads | `index_join` | 7.72ms | 6.45ms | 0.8× | 6.6% | PASS |
| mem_reads | `index_join_scan` | 2.64ms | 4.42ms | 1.7× | 5.0% | PASS |
| mem_reads | `types_table_scan` | 696.57ms | 739.00ms | 1.1× | 1.7% | PASS |
| mem_reads | `table_scan` | 817.50ms | 851.42ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_read_only` | 80.39ms | 75.65ms | 0.9× | 2.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 140.75ms | 205.22ms | 1.5× | 1.3% | PASS |
| mem_writes | `oltp_insert` | 10.63ms | 22.20ms | 2.1× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 42.93ms | 91.47ms | 2.1× | 4.1% | PASS |
| mem_writes | `oltp_update_non_index` | 34.33ms | 53.69ms | 1.6× | 3.4% | PASS |
| mem_writes | `oltp_delete_insert` | 34.24ms | 66.16ms | 1.9× | 2.6% | PASS |
| mem_writes | `oltp_write_only` | 18.81ms | 40.64ms | 2.2× | 2.2% | PASS |
| mem_writes | `types_delete_insert` | 24.31ms | 32.22ms | 1.3× | 2.9% | PASS |
| mem_writes | `oltp_read_write` | 55.64ms | 82.68ms | 1.5× | 2.5% | PASS |
| file_reads | `oltp_point_select` | 77.78ms | 37.15ms | 0.5× | 1.7% | PASS |
| file_reads | `oltp_range_select` | 15.63ms | 9.84ms | 0.6× | 2.7% | PASS |
| file_reads | `oltp_sum_range` | 14.74ms | 9.68ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 2.79ms | 2.16ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 3.30ms | 2.73ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 8.75ms | 5.90ms | 0.7× | 0.9% | PASS |
| file_reads | `select_random_points` | 20.91ms | 14.95ms | 0.7× | 1.9% | PASS |
| file_reads | `select_random_ranges` | 8.22ms | 5.14ms | 0.6× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 8.71ms | 4.64ms | 0.5× | 2.9% | PASS |
| file_reads | `groupby_scan` | 20.71ms | 20.10ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join` | 10.67ms | 7.25ms | 0.7× | 2.6% | PASS |
| file_reads | `index_join_scan` | 3.21ms | 4.21ms | 1.3× | 1.5% | PASS |
| file_reads | `types_table_scan` | 674.85ms | 719.98ms | 1.1× | 1.3% | PASS |
| file_reads | `table_scan` | 763.74ms | 811.63ms | 1.1× | 1.4% | PASS |
| file_reads | `oltp_read_only` | 149.12ms | 90.60ms | 0.6× | 2.0% | PASS |
| file_writes | `oltp_bulk_insert` | 199.13ms | 258.18ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_insert` | 19.10ms | 44.33ms | 2.3× | 4.8% | FAIL |
| file_writes | `oltp_update_index` | 140.43ms | 158.88ms | 1.1× | 3.5% | PASS |
| file_writes | `oltp_update_non_index` | 120.22ms | 105.49ms | 0.9× | 4.5% | PASS |
| file_writes | `oltp_delete_insert` | 146.42ms | 115.48ms | 0.8× | 3.5% | PASS |
| file_writes | `oltp_write_only` | 109.95ms | 79.54ms | 0.7× | 5.3% | PASS |
| file_writes | `types_delete_insert` | 102.32ms | 60.50ms | 0.6× | 7.7% | PASS |
| file_writes | `oltp_read_write` | 139.94ms | 120.17ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_point_select` | 38.07ms | 35.71ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 10.90ms | 9.54ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_sum_range` | 10.59ms | 9.55ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 2.21ms | 2.09ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 2.70ms | 2.59ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 4.28ms | 5.31ms | 1.2× | 2.3% | PASS |
| ac_reads | `select_random_points` | 15.43ms | 14.21ms | 0.9× | 2.1% | PASS |
| ac_reads | `select_random_ranges` | 3.94ms | 4.83ms | 1.2× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 4.16ms | 4.30ms | 1.0× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 19.40ms | 19.06ms | 1.0× | 1.2% | PASS |
| ac_reads | `index_join` | 8.20ms | 7.12ms | 0.9× | 2.0% | PASS |
| ac_reads | `index_join_scan` | 2.76ms | 4.29ms | 1.6× | 2.0% | PASS |
| ac_reads | `types_table_scan` | 680.57ms | 725.78ms | 1.1× | 1.5% | PASS |
| ac_reads | `table_scan` | 764.42ms | 818.94ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 96.42ms | 91.08ms | 0.9× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 37.53ms | 83.12ms | 2.2× | 12.0% | PASS |
| ac_writes | `oltp_insert_ac` | 39.15ms | 95.66ms | 2.4× | 9.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 39.18ms | 99.81ms | 2.5× | 10.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 34.21ms | 87.71ms | 2.6× | 7.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 37.99ms | 93.38ms | 2.5× | 8.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 37.23ms | 93.70ms | 2.5× | 7.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 31.65ms | 88.05ms | 2.8× | 7.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 40.03ms | 97.20ms | 2.4× | 7.3% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.22ms | 41.44ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 20.87ms | 21.67ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 18.36ms | 20.69ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 3.61ms | 3.86ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 4.71ms | 5.03ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 4.62ms | 6.32ms | 1.4× | 2.1% | PASS |
| mem_reads | `select_random_points` | 28.72ms | 32.79ms | 1.1× | 1.4% | PASS |
| mem_reads | `select_random_ranges` | 7.90ms | 8.95ms | 1.1× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.64ms | 1.1× | 2.8% | PASS |
| mem_reads | `groupby_scan` | 36.90ms | 39.80ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 8.08ms | 10.64ms | 1.3× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 3.98ms | 5.59ms | 1.4× | 2.2% | PASS |
| mem_reads | `types_table_scan` | 1.14s | 1.21s | 1.1× | 1.0% | PASS |
| mem_reads | `table_scan` | 1.38s | 1.31s | 0.9× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 160.67ms | 176.32ms | 1.1× | 1.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 247.44ms | 356.91ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 19.45ms | 35.76ms | 1.8× | 1.8% | PASS |
| mem_writes | `oltp_update_index` | 70.94ms | 135.41ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 53.40ms | 86.92ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_delete_insert` | 52.58ms | 99.44ms | 1.9× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 28.08ms | 60.14ms | 2.1× | 1.8% | PASS |
| mem_writes | `types_delete_insert` | 34.06ms | 54.78ms | 1.6× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 112.63ms | 169.25ms | 1.5× | 1.8% | PASS |
| file_reads | `oltp_point_select` | 112.19ms | 61.63ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 29.16ms | 23.88ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 26.89ms | 23.21ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 4.63ms | 4.19ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_distinct_range` | 5.79ms | 5.39ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 12.80ms | 8.60ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 40.06ms | 37.52ms | 0.9× | 1.9% | PASS |
| file_reads | `select_random_ranges` | 16.13ms | 11.25ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 12.46ms | 6.79ms | 0.5× | 1.7% | PASS |
| file_reads | `groupby_scan` | 38.11ms | 40.34ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 12.91ms | 12.65ms | 1.0× | 1.6% | PASS |
| file_reads | `index_join_scan` | 5.21ms | 6.13ms | 1.2× | 3.6% | PASS |
| file_reads | `types_table_scan` | 1.18s | 1.22s | 1.0× | 0.6% | PASS |
| file_reads | `table_scan` | 1.44s | 1.34s | 0.9× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 284.67ms | 212.90ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 263.17ms | 371.48ms | 1.4× | 1.6% | PASS |
| file_writes | `oltp_insert` | 30.34ms | 42.30ms | 1.4× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 109.18ms | 154.96ms | 1.4× | 3.0% | PASS |
| file_writes | `oltp_update_non_index` | 84.41ms | 102.63ms | 1.2× | 1.2% | PASS |
| file_writes | `oltp_delete_insert` | 86.57ms | 114.10ms | 1.3× | 2.2% | PASS |
| file_writes | `oltp_write_only` | 59.67ms | 71.18ms | 1.2× | 1.6% | PASS |
| file_writes | `types_delete_insert` | 53.40ms | 62.36ms | 1.2× | 1.2% | PASS |
| file_writes | `oltp_read_write` | 143.69ms | 179.02ms | 1.2× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 60.08ms | 61.83ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 23.86ms | 24.05ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_sum_range` | 21.36ms | 23.13ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 4.06ms | 4.18ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_distinct_range` | 5.18ms | 5.39ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 7.54ms | 8.57ms | 1.1× | 1.6% | PASS |
| ac_reads | `select_random_points` | 33.33ms | 36.99ms | 1.1× | 1.6% | PASS |
| ac_reads | `select_random_ranges` | 10.58ms | 11.24ms | 1.1× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 7.20ms | 6.79ms | 0.9× | 2.0% | PASS |
| ac_reads | `groupby_scan` | 37.36ms | 40.25ms | 1.1× | 0.6% | PASS |
| ac_reads | `index_join` | 10.12ms | 12.64ms | 1.2× | 2.3% | PASS |
| ac_reads | `index_join_scan` | 4.61ms | 6.15ms | 1.3× | 3.8% | PASS |
| ac_reads | `types_table_scan` | 1.15s | 1.21s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.41s | 1.31s | 0.9× | 0.6% | PASS |
| ac_reads | `oltp_read_only` | 203.88ms | 209.63ms | 1.0× | 1.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.84ms | 84.09ms | 3.4× | 7.1% | PASS |
| ac_writes | `oltp_insert_ac` | 29.25ms | 104.04ms | 3.6× | 7.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 30.55ms | 110.96ms | 3.6× | 6.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 25.98ms | 93.02ms | 3.6× | 4.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.19ms | 103.24ms | 3.7× | 4.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.43ms | 101.22ms | 3.7× | 5.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.44ms | 92.81ms | 3.8× | 6.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.96ms | 109.97ms | 3.1× | 5.4% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 11s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 93.97ms | 130.00ms | 72.3% | 0.4% | PASS |
| `status_dirty_many_tables` | 97.57ms | 130.00ms | 75.1% | 0.3% | PASS |
| `diff_regular_working_one_table` | 89.52ms | 120.00ms | 74.6% | 0.3% | PASS |
| `diff_regular_working_many_tables` | 102.52ms | 140.00ms | 73.2% | 0.3% | PASS |
| `diff_stat_working_many_tables` | 102.60ms | 140.00ms | 73.3% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 103.11ms | 140.00ms | 73.6% | 0.4% | PASS |
| `branch_list_many_branches` | 22.53ms | 35.00ms | 64.4% | 0.9% | PASS |
| `branch_create_delete` | 24.89ms | 40.00ms | 62.2% | 1.1% | PASS |
| `checkout_branch_clean` | 59.98ms | 150.00ms | 40.0% | 0.7% | PASS |
| `merge_data_no_conflicts` | 28.38ms | 50.00ms | 56.8% | 0.6% | PASS |
| `merge_schema_no_conflicts` | 21.53ms | 35.00ms | 61.5% | 0.7% | PASS |
| `merge_data_conflicts` | 32.22ms | 180.00ms | 17.9% | 0.8% | PASS |
| `merge_data_conflicts_with_resolve` | 31.97ms | 180.00ms | 17.8% | 0.6% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
