# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-25 21:05 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261122.9.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/45685794931)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.29s | 8.62s | 1.0× | 1.6% | **PASS** |
| Writes | 1.62s | 2.77s | 1.7× | 1.2% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.00s | 8.85s | 1.0× | 1.5% | **PASS** |
| Writes | 2.66s | 3.35s | 1.3× | 1.7% | **PASS** |
| Autocommit writes | 560.63ms | 1.74s | 3.1× | 5.4% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 1.68s | 1.69s | 1.0× | 2.7% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.79s | 2.86s | 1.0× | 2.2% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 1.92s | 2.02s | 1.1× | 1.2% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 1.91s | 2.05s | 1.1× | 0.9% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 281.30ms | 478.36ms | 1.7× | 2.7% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 595.32ms | 1.06s | 1.8× | 1.6% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 375.97ms | 639.92ms | 1.7× | 1.1% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 365.49ms | 596.92ms | 1.6× | 0.9% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 1.80s | 1.73s | 1.0× | 2.3% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.11s | 2.97s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.09s | 2.09s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.00s | 2.07s | 1.0× | 1.2% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 741.33ms | 756.84ms | 1.0× | 3.7% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 981.77ms | 1.19s | 1.2× | 2.7% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 487.00ms | 732.82ms | 1.5× | 1.7% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 445.51ms | 663.42ms | 1.5× | 1.5% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 1.70s | 1.73s | 1.0× | 2.4% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.78s | 2.90s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 1.94s | 2.03s | 1.0× | 1.3% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 1.96s | 2.07s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 174.54ms | 486.27ms | 2.8× | 3.7% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 203.22ms | 596.48ms | 2.9× | 6.2% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 83.89ms | 297.95ms | 3.6× | 5.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 98.97ms | 355.78ms | 3.6× | 7.8% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 15.97ms | 18.45ms | 1.2× | 3.0% | PASS |
| mem_reads | `oltp_range_select` | 6.69ms | 6.74ms | 1.0× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 5.84ms | 6.63ms | 1.1× | 2.7% | PASS |
| mem_reads | `oltp_order_range` | 1.68ms | 1.73ms | 1.0× | 3.0% | PASS |
| mem_reads | `oltp_distinct_range` | 2.28ms | 2.52ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_index_scan` | 2.55ms | 3.12ms | 1.2× | 4.9% | PASS |
| mem_reads | `select_random_points` | 8.09ms | 8.77ms | 1.1× | 3.7% | PASS |
| mem_reads | `select_random_ranges` | 2.11ms | 2.53ms | 1.2× | 2.4% | PASS |
| mem_reads | `covering_index_scan` | 2.37ms | 2.45ms | 1.0× | 2.3% | PASS |
| mem_reads | `groupby_scan` | 18.74ms | 21.19ms | 1.1× | 1.1% | PASS |
| mem_reads | `index_join` | 3.62ms | 4.92ms | 1.4× | 3.8% | PASS |
| mem_reads | `index_join_scan` | 2.48ms | 3.79ms | 1.5× | 3.3% | PASS |
| mem_reads | `types_table_scan` | 701.60ms | 722.80ms | 1.0× | 1.5% | PASS |
| mem_reads | `table_scan` | 840.48ms | 814.20ms | 1.0× | 2.9% | PASS |
| mem_reads | `oltp_read_only` | 60.77ms | 66.49ms | 1.1× | 2.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 105.45ms | 161.34ms | 1.5× | 1.5% | PASS |
| mem_writes | `oltp_insert` | 9.21ms | 17.69ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_update_index` | 34.78ms | 69.36ms | 2.0× | 3.2% | PASS |
| mem_writes | `oltp_update_non_index` | 24.68ms | 42.11ms | 1.7× | 2.8% | PASS |
| mem_writes | `oltp_delete_insert` | 29.52ms | 52.87ms | 1.8× | 2.6% | PASS |
| mem_writes | `oltp_write_only` | 14.69ms | 33.90ms | 2.3× | 2.8% | FAIL |
| mem_writes | `types_delete_insert` | 17.16ms | 27.28ms | 1.6× | 3.1% | PASS |
| mem_writes | `oltp_read_write` | 45.81ms | 73.81ms | 1.6× | 2.6% | PASS |
| file_reads | `oltp_point_select` | 70.83ms | 33.33ms | 0.5× | 2.3% | PASS |
| file_reads | `oltp_range_select` | 13.33ms | 8.81ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 12.42ms | 8.67ms | 0.7× | 4.0% | PASS |
| file_reads | `oltp_order_range` | 2.43ms | 2.02ms | 0.8× | 2.6% | PASS |
| file_reads | `oltp_distinct_range` | 2.96ms | 2.73ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 8.69ms | 5.44ms | 0.6× | 2.4% | PASS |
| file_reads | `select_random_points` | 14.70ms | 10.86ms | 0.7× | 3.0% | PASS |
| file_reads | `select_random_ranges` | 7.51ms | 3.97ms | 0.5× | 2.2% | PASS |
| file_reads | `covering_index_scan` | 8.24ms | 4.24ms | 0.5× | 5.3% | PASS |
| file_reads | `groupby_scan` | 19.15ms | 21.07ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 6.85ms | 5.76ms | 0.8× | 4.2% | PASS |
| file_reads | `index_join_scan` | 3.06ms | 3.84ms | 1.3× | 3.6% | PASS |
| file_reads | `types_table_scan` | 704.00ms | 725.44ms | 1.0× | 0.7% | PASS |
| file_reads | `table_scan` | 793.24ms | 803.58ms | 1.0× | 1.8% | PASS |
| file_reads | `oltp_read_only` | 135.06ms | 86.48ms | 0.6× | 2.2% | PASS |
| file_writes | `oltp_bulk_insert` | 145.64ms | 208.23ms | 1.4× | 3.6% | PASS |
| file_writes | `oltp_insert` | 19.76ms | 27.87ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_update_index` | 126.93ms | 118.04ms | 0.9× | 4.9% | PASS |
| file_writes | `oltp_update_non_index` | 101.25ms | 78.55ms | 0.8× | 1.9% | PASS |
| file_writes | `oltp_delete_insert` | 107.85ms | 98.06ms | 0.9× | 4.5% | PASS |
| file_writes | `oltp_write_only` | 73.90ms | 68.33ms | 0.9× | 3.5% | PASS |
| file_writes | `types_delete_insert` | 61.09ms | 49.50ms | 0.8× | 3.9% | PASS |
| file_writes | `oltp_read_write` | 104.92ms | 108.27ms | 1.0× | 5.6% | PASS |
| ac_reads | `oltp_point_select` | 34.71ms | 33.75ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_range_select` | 9.43ms | 8.65ms | 0.9× | 2.8% | PASS |
| ac_reads | `oltp_sum_range` | 8.78ms | 8.68ms | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_order_range` | 2.10ms | 2.06ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 2.62ms | 2.74ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 5.07ms | 5.36ms | 1.1× | 2.8% | PASS |
| ac_reads | `select_random_points` | 10.86ms | 10.81ms | 1.0× | 3.8% | PASS |
| ac_reads | `select_random_ranges` | 4.17ms | 4.10ms | 1.0× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 4.91ms | 4.53ms | 0.9× | 2.4% | PASS |
| ac_reads | `groupby_scan` | 19.41ms | 21.58ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 5.20ms | 6.56ms | 1.3× | 5.8% | PASS |
| ac_reads | `index_join_scan` | 2.87ms | 4.07ms | 1.4× | 3.0% | PASS |
| ac_reads | `types_table_scan` | 700.21ms | 726.56ms | 1.0× | 1.3% | PASS |
| ac_reads | `table_scan` | 805.90ms | 806.23ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 85.12ms | 86.22ms | 1.0× | 2.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.46ms | 49.21ms | 2.5× | 3.5% | PASS |
| ac_writes | `oltp_insert_ac` | 21.50ms | 57.84ms | 2.7× | 3.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 23.24ms | 67.85ms | 2.9× | 3.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.01ms | 56.83ms | 2.7× | 2.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.29ms | 63.68ms | 2.9× | 4.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.34ms | 63.00ms | 2.8× | 3.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.04ms | 63.05ms | 3.1× | 4.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 24.67ms | 64.81ms | 2.6× | 2.9% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.38ms | 38.78ms | 1.1× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 17.60ms | 14.29ms | 0.8× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 15.45ms | 13.88ms | 0.9× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 3.39ms | 3.33ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 4.33ms | 4.43ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_index_scan` | 4.03ms | 6.40ms | 1.6× | 2.2% | PASS |
| mem_reads | `select_random_points` | 23.50ms | 21.03ms | 0.9× | 3.6% | PASS |
| mem_reads | `select_random_ranges` | 3.52ms | 5.32ms | 1.5× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.73ms | 1.1× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 35.06ms | 34.99ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 10.88ms | 9.67ms | 0.9× | 3.8% | PASS |
| mem_reads | `index_join_scan` | 3.88ms | 5.55ms | 1.4× | 2.3% | PASS |
| mem_reads | `types_table_scan` | 1.13s | 1.23s | 1.1× | 2.4% | PASS |
| mem_reads | `table_scan` | 1.34s | 1.33s | 1.0× | 2.5% | PASS |
| mem_reads | `oltp_read_only` | 148.08ms | 143.82ms | 1.0× | 1.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 234.89ms | 380.35ms | 1.6× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 17.63ms | 40.54ms | 2.3× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 67.00ms | 151.73ms | 2.3× | 2.2% | PASS |
| mem_writes | `oltp_update_non_index` | 50.56ms | 93.61ms | 1.9× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 54.74ms | 110.69ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 28.17ms | 66.62ms | 2.4× | 1.6% | FAIL |
| mem_writes | `types_delete_insert` | 38.81ms | 59.01ms | 1.5× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 103.51ms | 153.67ms | 1.5× | 2.2% | PASS |
| file_reads | `oltp_point_select` | 107.85ms | 57.34ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 24.91ms | 16.41ms | 0.7× | 2.3% | PASS |
| file_reads | `oltp_sum_range` | 23.22ms | 16.03ms | 0.7× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 4.37ms | 3.60ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 5.34ms | 4.72ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 11.46ms | 8.38ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 32.45ms | 24.39ms | 0.8× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 10.65ms | 7.17ms | 0.7× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.73ms | 6.77ms | 0.6× | 1.1% | PASS |
| file_reads | `groupby_scan` | 36.43ms | 35.63ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 15.48ms | 11.30ms | 0.7× | 1.3% | PASS |
| file_reads | `index_join_scan` | 4.92ms | 6.02ms | 1.2× | 2.8% | PASS |
| file_reads | `types_table_scan` | 1.14s | 1.23s | 1.1× | 3.2% | PASS |
| file_reads | `table_scan` | 1.42s | 1.36s | 1.0× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 259.86ms | 177.12ms | 0.7× | 0.6% | PASS |
| file_writes | `oltp_bulk_insert` | 260.94ms | 401.35ms | 1.5× | 0.9% | PASS |
| file_writes | `oltp_insert` | 26.49ms | 49.33ms | 1.9× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 143.35ms | 180.46ms | 1.3× | 3.8% | PASS |
| file_writes | `oltp_update_non_index` | 121.70ms | 112.62ms | 0.9× | 15.8% | PASS |
| file_writes | `oltp_delete_insert` | 103.10ms | 131.64ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 89.78ms | 81.33ms | 0.9× | 7.6% | PASS |
| file_writes | `types_delete_insert` | 74.77ms | 68.77ms | 0.9× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 161.64ms | 166.69ms | 1.0× | 8.5% | PASS |
| ac_reads | `oltp_point_select` | 61.43ms | 57.90ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_range_select` | 20.75ms | 16.45ms | 0.8× | 1.7% | PASS |
| ac_reads | `oltp_sum_range` | 18.59ms | 16.13ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.84ms | 3.59ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_distinct_range` | 4.79ms | 4.74ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.72ms | 8.35ms | 1.2× | 1.4% | PASS |
| ac_reads | `select_random_points` | 26.46ms | 24.24ms | 0.9× | 3.9% | PASS |
| ac_reads | `select_random_ranges` | 5.95ms | 7.16ms | 1.2× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 6.91ms | 6.67ms | 1.0× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 35.47ms | 35.37ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 12.89ms | 11.19ms | 0.9× | 2.4% | PASS |
| ac_reads | `index_join_scan` | 4.37ms | 5.91ms | 1.4× | 2.3% | PASS |
| ac_reads | `types_table_scan` | 1.11s | 1.22s | 1.1× | 1.0% | PASS |
| ac_reads | `table_scan` | 1.28s | 1.31s | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 180.69ms | 170.61ms | 0.9× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.80ms | 60.96ms | 2.7× | 7.5% | PASS |
| ac_writes | `oltp_insert_ac` | 25.44ms | 72.06ms | 2.8× | 5.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.90ms | 85.03ms | 3.2× | 6.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.16ms | 69.55ms | 3.1× | 5.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.98ms | 77.37ms | 3.1× | 5.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.27ms | 77.41ms | 3.1× | 7.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.60ms | 71.10ms | 3.0× | 10.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.07ms | 83.00ms | 2.6× | 4.9% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.83ms | 24.21ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_range_select` | 11.47ms | 9.63ms | 0.8× | 1.0% | PASS |
| mem_reads | `oltp_sum_range` | 11.47ms | 9.44ms | 0.8× | 0.8% | PASS |
| mem_reads | `oltp_order_range` | 2.39ms | 2.30ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.05ms | 3.01ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 2.86ms | 4.56ms | 1.6× | 1.4% | PASS |
| mem_reads | `select_random_points` | 17.88ms | 17.12ms | 1.0× | 1.1% | PASS |
| mem_reads | `select_random_ranges` | 2.33ms | 3.84ms | 1.6× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 2.61ms | 3.29ms | 1.3× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 24.25ms | 24.82ms | 1.0× | 0.5% | PASS |
| mem_reads | `index_join` | 9.50ms | 7.26ms | 0.8× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 2.75ms | 4.79ms | 1.7× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 789.59ms | 856.31ms | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 913.89ms | 957.16ms | 1.0× | 3.6% | PASS |
| mem_reads | `oltp_read_only` | 97.16ms | 94.59ms | 1.0× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 138.78ms | 212.47ms | 1.5× | 0.5% | PASS |
| mem_writes | `oltp_insert` | 11.38ms | 24.57ms | 2.2× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 45.40ms | 98.58ms | 2.2× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 32.48ms | 57.55ms | 1.8× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 36.03ms | 68.88ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 19.59ms | 42.03ms | 2.1× | 1.1% | PASS |
| mem_writes | `types_delete_insert` | 26.00ms | 36.49ms | 1.4× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 66.31ms | 99.35ms | 1.5× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 47.00ms | 29.77ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 13.94ms | 10.44ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 13.51ms | 10.06ms | 0.7× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 2.66ms | 2.37ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 3.27ms | 3.07ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 5.08ms | 4.96ms | 1.0× | 1.5% | PASS |
| file_reads | `select_random_points` | 20.52ms | 17.77ms | 0.9× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 4.52ms | 4.19ms | 0.9× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 4.80ms | 3.60ms | 0.7× | 1.9% | PASS |
| file_reads | `groupby_scan` | 24.18ms | 24.59ms | 1.0× | 0.4% | PASS |
| file_reads | `index_join` | 10.66ms | 7.65ms | 0.7× | 2.4% | PASS |
| file_reads | `index_join_scan` | 3.08ms | 5.08ms | 1.7× | 2.6% | PASS |
| file_reads | `types_table_scan` | 818.24ms | 869.95ms | 1.1× | 1.4% | PASS |
| file_reads | `table_scan` | 984.45ms | 993.47ms | 1.0× | 7.9% | PASS |
| file_reads | `oltp_read_only` | 134.84ms | 103.69ms | 0.8× | 1.4% | PASS |
| file_writes | `oltp_bulk_insert` | 151.16ms | 224.69ms | 1.5× | 0.7% | PASS |
| file_writes | `oltp_insert` | 15.10ms | 30.86ms | 2.0× | 1.3% | PASS |
| file_writes | `oltp_update_index` | 63.99ms | 118.68ms | 1.9× | 1.8% | PASS |
| file_writes | `oltp_update_non_index` | 47.48ms | 69.94ms | 1.5× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 54.93ms | 84.26ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 34.64ms | 53.12ms | 1.5× | 1.4% | PASS |
| file_writes | `types_delete_insert` | 38.48ms | 43.50ms | 1.1× | 2.2% | PASS |
| file_writes | `oltp_read_write` | 81.22ms | 107.77ms | 1.3× | 1.9% | PASS |
| ac_reads | `oltp_point_select` | 31.96ms | 29.74ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 12.72ms | 10.42ms | 0.8× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 12.67ms | 10.24ms | 0.8× | 1.4% | PASS |
| ac_reads | `oltp_order_range` | 2.58ms | 2.37ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_distinct_range` | 3.18ms | 3.06ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 3.85ms | 5.01ms | 1.3× | 2.5% | PASS |
| ac_reads | `select_random_points` | 19.15ms | 17.52ms | 0.9× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 3.12ms | 4.14ms | 1.3× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 3.34ms | 3.50ms | 1.0× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 24.01ms | 24.54ms | 1.0× | 0.5% | PASS |
| ac_reads | `index_join` | 10.15ms | 7.40ms | 0.7× | 3.1% | PASS |
| ac_reads | `index_join_scan` | 2.99ms | 4.84ms | 1.6× | 3.1% | PASS |
| ac_reads | `types_table_scan` | 795.13ms | 856.72ms | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 910.76ms | 948.40ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 104.08ms | 98.70ms | 0.9× | 0.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 9.33ms | 29.30ms | 3.1× | 5.1% | PASS |
| ac_writes | `oltp_insert_ac` | 10.40ms | 37.98ms | 3.7× | 2.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 10.83ms | 42.98ms | 4.0× | 3.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 9.10ms | 33.23ms | 3.7× | 4.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 10.52ms | 38.65ms | 3.7× | 6.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 10.34ms | 38.96ms | 3.8× | 5.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 9.58ms | 34.42ms | 3.6× | 7.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 13.78ms | 42.43ms | 3.1× | 4.9% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 20.97ms | 25.15ms | 1.2× | 0.9% | PASS |
| mem_reads | `oltp_range_select` | 13.54ms | 13.88ms | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_sum_range` | 13.03ms | 13.35ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_order_range` | 2.63ms | 2.71ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.29ms | 3.40ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.21ms | 4.29ms | 1.3× | 1.4% | PASS |
| mem_reads | `select_random_points` | 19.01ms | 22.37ms | 1.2× | 0.8% | PASS |
| mem_reads | `select_random_ranges` | 5.03ms | 5.86ms | 1.2× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 2.57ms | 2.69ms | 1.0× | 2.8% | PASS |
| mem_reads | `groupby_scan` | 25.33ms | 27.82ms | 1.1× | 0.4% | PASS |
| mem_reads | `index_join` | 5.72ms | 7.75ms | 1.4× | 1.2% | PASS |
| mem_reads | `index_join_scan` | 2.90ms | 4.47ms | 1.5× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 796.05ms | 863.37ms | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 903.85ms | 941.08ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_read_only` | 97.22ms | 108.38ms | 1.1× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 143.27ms | 200.67ms | 1.4× | 0.3% | PASS |
| mem_writes | `oltp_insert` | 11.96ms | 21.40ms | 1.8× | 0.5% | PASS |
| mem_writes | `oltp_update_index` | 44.46ms | 85.31ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 31.63ms | 55.06ms | 1.7× | 0.9% | PASS |
| mem_writes | `oltp_delete_insert` | 32.66ms | 62.50ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 17.83ms | 38.00ms | 2.1× | 1.0% | PASS |
| mem_writes | `types_delete_insert` | 20.41ms | 33.55ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_read_write` | 63.27ms | 100.42ms | 1.6× | 0.9% | PASS |
| file_reads | `oltp_point_select` | 42.70ms | 30.45ms | 0.7× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 15.62ms | 14.51ms | 0.9× | 0.7% | PASS |
| file_reads | `oltp_sum_range` | 15.26ms | 14.07ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 2.86ms | 2.78ms | 1.0× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 3.50ms | 3.47ms | 1.0× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 5.41ms | 4.79ms | 0.9× | 1.4% | PASS |
| file_reads | `select_random_points` | 21.46ms | 23.40ms | 1.1× | 0.6% | PASS |
| file_reads | `select_random_ranges` | 7.33ms | 6.54ms | 0.9× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 4.78ms | 3.32ms | 0.7× | 1.3% | PASS |
| file_reads | `groupby_scan` | 25.66ms | 27.91ms | 1.1× | 0.5% | PASS |
| file_reads | `index_join` | 6.99ms | 8.10ms | 1.2× | 1.0% | PASS |
| file_reads | `index_join_scan` | 3.21ms | 4.60ms | 1.4× | 2.1% | PASS |
| file_reads | `types_table_scan` | 803.67ms | 866.50ms | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 913.22ms | 940.75ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_read_only` | 126.95ms | 116.08ms | 0.9× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 152.25ms | 209.72ms | 1.4× | 0.6% | PASS |
| file_writes | `oltp_insert` | 15.17ms | 26.17ms | 1.7× | 1.4% | PASS |
| file_writes | `oltp_update_index` | 59.31ms | 99.12ms | 1.7× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 43.99ms | 65.39ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 45.37ms | 72.97ms | 1.6× | 1.2% | PASS |
| file_writes | `oltp_write_only` | 28.24ms | 45.58ms | 1.6× | 1.4% | PASS |
| file_writes | `types_delete_insert` | 28.40ms | 38.42ms | 1.4× | 2.0% | PASS |
| file_writes | `oltp_read_write` | 72.78ms | 106.04ms | 1.5× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 27.76ms | 29.77ms | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 13.90ms | 14.38ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 13.52ms | 13.93ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 2.69ms | 2.76ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 3.38ms | 3.47ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 4.03ms | 4.73ms | 1.2× | 2.4% | PASS |
| ac_reads | `select_random_points` | 20.73ms | 24.09ms | 1.2× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 6.02ms | 6.67ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 3.39ms | 3.44ms | 1.0× | 2.6% | PASS |
| ac_reads | `groupby_scan` | 25.76ms | 28.04ms | 1.1× | 0.4% | PASS |
| ac_reads | `index_join` | 6.34ms | 8.13ms | 1.3× | 1.1% | PASS |
| ac_reads | `index_join_scan` | 3.10ms | 4.57ms | 1.5× | 3.8% | PASS |
| ac_reads | `types_table_scan` | 800.98ms | 865.00ms | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 920.27ms | 942.92ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_read_only` | 108.12ms | 116.26ms | 1.1× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 10.44ms | 32.24ms | 3.1× | 7.1% | PASS |
| ac_writes | `oltp_insert_ac` | 10.98ms | 41.91ms | 3.8× | 9.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 11.98ms | 46.56ms | 3.9× | 6.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 9.59ms | 37.20ms | 3.9× | 7.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 15.32ms | 52.91ms | 3.5× | 15.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 13.47ms | 50.40ms | 3.7× | 6.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 10.66ms | 41.38ms | 3.9× | 12.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 16.54ms | 53.19ms | 3.2× | 8.3% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 23s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 55.18ms | 130.00ms | 42.4% | 1.1% | PASS |
| `status_dirty_many_tables` | 57.83ms | 130.00ms | 44.5% | 1.1% | PASS |
| `diff_regular_working_one_table` | 52.69ms | 120.00ms | 43.9% | 1.1% | PASS |
| `diff_regular_working_many_tables` | 61.05ms | 140.00ms | 43.6% | 1.1% | PASS |
| `diff_stat_working_many_tables` | 61.24ms | 140.00ms | 43.7% | 0.9% | PASS |
| `diff_schema_working_many_tables` | 61.77ms | 140.00ms | 44.1% | 0.9% | PASS |
| `branch_list_many_branches` | 12.91ms | 35.00ms | 36.9% | 1.5% | PASS |
| `branch_create_delete` | 14.79ms | 40.00ms | 37.0% | 2.0% | PASS |
| `checkout_branch_clean` | 37.76ms | 150.00ms | 25.2% | 2.8% | PASS |
| `merge_data_no_conflicts` | 17.02ms | 50.00ms | 34.0% | 2.6% | PASS |
| `merge_schema_no_conflicts` | 12.80ms | 35.00ms | 36.6% | 2.1% | PASS |
| `merge_data_conflicts` | 18.62ms | 180.00ms | 10.3% | 1.3% | PASS |
| `merge_data_conflicts_with_resolve` | 18.63ms | 180.00ms | 10.3% | 1.1% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
