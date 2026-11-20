# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-11-20 19:32 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261116.88.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/44787920034)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.71s | 8.76s | 1.0× | 1.8% | **PASS** |
| Writes | 1.67s | 2.80s | 1.7× | 1.3% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.41s | 8.89s | 0.9× | 1.2% | **PASS** |
| Writes | 4.32s | 4.36s | 1.0× | 4.5% | **PASS** |
| Autocommit writes | 849.72ms | 2.62s | 3.1× | 10.8% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 1.98s | 2.12s | 1.1× | 1.1% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.49s | 2.38s | 1.0× | 2.5% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.07s | 2.23s | 1.1× | 0.9% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.18s | 2.03s | 0.9× | 2.9% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 335.07ms | 562.42ms | 1.7× | 1.1% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 490.35ms | 807.29ms | 1.6× | 2.0% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 443.65ms | 762.68ms | 1.7× | 0.8% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 398.08ms | 665.41ms | 1.7× | 2.8% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.19s | 2.17s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.92s | 2.51s | 0.9× | 1.1% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.33s | 2.30s | 1.0× | 0.8% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 1.96s | 1.92s | 1.0× | 2.0% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 851.06ms | 884.60ms | 1.0× | 2.1% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.42s | 1.32s | 0.9× | 16.7% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.16s | 1.22s | 1.1× | 5.6% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 892.39ms | 935.65ms | 1.0× | 4.2% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.05s | 2.18s | 1.1× | 1.0% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.50s | 2.40s | 1.0× | 1.3% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.16s | 2.29s | 1.1× | 0.8% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 1.84s | 1.91s | 1.0× | 2.5% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 143.54ms | 451.43ms | 3.1× | 5.9% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 343.56ms | 1.10s | 3.2× | 56.4% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 175.53ms | 546.24ms | 3.1× | 18.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 187.08ms | 520.46ms | 2.8× | 5.9% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 18.39ms | 21.89ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 8.63ms | 8.82ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_sum_range` | 7.47ms | 8.45ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 2.11ms | 2.26ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_distinct_range` | 2.93ms | 3.12ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.01ms | 3.81ms | 1.3× | 1.7% | PASS |
| mem_reads | `select_random_points` | 8.10ms | 8.94ms | 1.1× | 1.9% | PASS |
| mem_reads | `select_random_ranges` | 2.53ms | 3.07ms | 1.2× | 1.7% | PASS |
| mem_reads | `covering_index_scan` | 3.26ms | 3.14ms | 1.0× | 1.8% | PASS |
| mem_reads | `groupby_scan` | 24.35ms | 27.08ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 4.52ms | 5.93ms | 1.3× | 1.1% | PASS |
| mem_reads | `index_join_scan` | 2.65ms | 3.76ms | 1.4× | 0.9% | PASS |
| mem_reads | `types_table_scan` | 850.80ms | 919.99ms | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 959.86ms | 1.01s | 1.0× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 79.06ms | 89.26ms | 1.1× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 134.93ms | 203.87ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 11.64ms | 21.50ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 39.67ms | 76.51ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 27.93ms | 47.46ms | 1.7× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 34.54ms | 58.67ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 17.18ms | 38.18ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 18.87ms | 30.21ms | 1.6× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 50.31ms | 86.02ms | 1.7× | 1.3% | PASS |
| file_reads | `oltp_point_select` | 86.58ms | 38.16ms | 0.4× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 15.87ms | 10.60ms | 0.7× | 2.2% | PASS |
| file_reads | `oltp_sum_range` | 14.60ms | 10.29ms | 0.7× | 1.7% | PASS |
| file_reads | `oltp_order_range` | 2.95ms | 2.53ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 3.75ms | 3.36ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 10.23ms | 5.93ms | 0.6× | 1.1% | PASS |
| file_reads | `select_random_points` | 15.66ms | 10.80ms | 0.7× | 2.0% | PASS |
| file_reads | `select_random_ranges` | 9.70ms | 4.86ms | 0.5× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 10.40ms | 5.25ms | 0.5× | 1.0% | PASS |
| file_reads | `groupby_scan` | 25.25ms | 27.36ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 8.38ms | 7.54ms | 0.9× | 1.2% | PASS |
| file_reads | `index_join_scan` | 3.50ms | 4.09ms | 1.2× | 1.6% | PASS |
| file_reads | `types_table_scan` | 851.32ms | 921.57ms | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 959.12ms | 1.01s | 1.1× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 176.36ms | 112.14ms | 0.6× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 178.18ms | 250.55ms | 1.4× | 2.0% | PASS |
| file_writes | `oltp_insert` | 27.72ms | 32.91ms | 1.2× | 3.1% | PASS |
| file_writes | `oltp_update_index` | 137.12ms | 134.54ms | 1.0× | 1.8% | PASS |
| file_writes | `oltp_update_non_index` | 110.73ms | 96.00ms | 0.9× | 3.4% | PASS |
| file_writes | `oltp_delete_insert` | 116.90ms | 110.40ms | 0.9× | 1.9% | PASS |
| file_writes | `oltp_write_only` | 86.67ms | 78.21ms | 0.9× | 2.3% | PASS |
| file_writes | `types_delete_insert` | 72.94ms | 55.99ms | 0.8× | 3.9% | PASS |
| file_writes | `oltp_read_write` | 120.80ms | 125.99ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 39.52ms | 38.06ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 11.38ms | 10.63ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_sum_range` | 10.27ms | 10.31ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 2.58ms | 2.52ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_distinct_range` | 3.30ms | 3.35ms | 1.0× | 0.6% | PASS |
| ac_reads | `oltp_index_scan` | 5.56ms | 5.96ms | 1.1× | 1.2% | PASS |
| ac_reads | `select_random_points` | 11.07ms | 10.84ms | 1.0× | 1.1% | PASS |
| ac_reads | `select_random_ranges` | 5.01ms | 4.87ms | 1.0× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 5.83ms | 5.27ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 24.87ms | 27.41ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 6.12ms | 7.68ms | 1.3× | 1.2% | PASS |
| ac_reads | `index_join_scan` | 3.15ms | 4.20ms | 1.3× | 1.7% | PASS |
| ac_reads | `types_table_scan` | 849.69ms | 921.91ms | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 959.84ms | 1.01s | 1.1× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 110.49ms | 112.75ms | 1.0× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 16.11ms | 45.02ms | 2.8× | 6.5% | PASS |
| ac_writes | `oltp_insert_ac` | 17.96ms | 55.65ms | 3.1× | 5.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 19.39ms | 65.78ms | 3.4× | 5.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.05ms | 50.27ms | 3.1× | 7.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 17.83ms | 59.05ms | 3.3× | 5.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 18.07ms | 60.77ms | 3.4× | 6.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 16.14ms | 51.95ms | 3.2× | 7.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 21.98ms | 62.94ms | 2.9× | 3.9% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.10ms | 29.51ms | 0.9× | 3.2% | PASS |
| mem_reads | `oltp_range_select` | 16.60ms | 11.28ms | 0.7× | 2.5% | PASS |
| mem_reads | `oltp_sum_range` | 14.51ms | 11.34ms | 0.8× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 2.99ms | 2.64ms | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.73ms | 3.48ms | 0.9× | 1.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.32ms | 5.03ms | 1.5× | 2.4% | PASS |
| mem_reads | `select_random_points` | 21.79ms | 17.32ms | 0.8× | 4.5% | PASS |
| mem_reads | `select_random_ranges` | 3.00ms | 4.30ms | 1.4× | 2.6% | PASS |
| mem_reads | `covering_index_scan` | 3.35ms | 3.97ms | 1.2× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 29.70ms | 29.15ms | 1.0× | 1.1% | PASS |
| mem_reads | `index_join` | 9.79ms | 7.64ms | 0.8× | 3.4% | PASS |
| mem_reads | `index_join_scan` | 3.35ms | 4.87ms | 1.5× | 3.0% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.05s | 1.0× | 3.4% | PASS |
| mem_reads | `table_scan` | 1.16s | 1.10s | 0.9× | 4.0% | PASS |
| mem_reads | `oltp_read_only` | 118.58ms | 105.11ms | 0.9× | 2.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 178.74ms | 279.88ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 13.70ms | 30.14ms | 2.2× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 53.67ms | 116.01ms | 2.2× | 2.1% | PASS |
| mem_writes | `oltp_update_non_index` | 41.70ms | 70.68ms | 1.7× | 2.9% | PASS |
| mem_writes | `oltp_delete_insert` | 46.12ms | 88.36ms | 1.9× | 1.8% | PASS |
| mem_writes | `oltp_write_only` | 23.80ms | 52.22ms | 2.2× | 2.0% | PASS |
| mem_writes | `types_delete_insert` | 34.17ms | 46.92ms | 1.4× | 2.5% | PASS |
| mem_writes | `oltp_read_write` | 98.43ms | 123.08ms | 1.3× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 103.10ms | 47.50ms | 0.5× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 24.27ms | 13.44ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_sum_range` | 22.24ms | 13.54ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 3.78ms | 2.85ms | 0.8× | 0.7% | PASS |
| file_reads | `oltp_distinct_range` | 4.56ms | 3.68ms | 0.8× | 0.7% | PASS |
| file_reads | `oltp_index_scan` | 10.62ms | 6.89ms | 0.6× | 1.0% | PASS |
| file_reads | `select_random_points` | 29.84ms | 19.61ms | 0.7× | 2.5% | PASS |
| file_reads | `select_random_ranges` | 9.99ms | 6.06ms | 0.6× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 10.81ms | 5.75ms | 0.5× | 1.2% | PASS |
| file_reads | `groupby_scan` | 30.48ms | 29.24ms | 1.0× | 1.2% | PASS |
| file_reads | `index_join` | 14.13ms | 9.11ms | 0.6× | 3.6% | PASS |
| file_reads | `index_join_scan` | 4.20ms | 5.28ms | 1.3× | 1.8% | PASS |
| file_reads | `types_table_scan` | 1.14s | 1.07s | 0.9× | 0.7% | PASS |
| file_reads | `table_scan` | 1.29s | 1.14s | 0.9× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 226.53ms | 134.62ms | 0.6× | 1.5% | PASS |
| file_writes | `oltp_bulk_insert` | 269.87ms | 369.38ms | 1.4× | 6.6% | PASS |
| file_writes | `oltp_insert` | 29.00ms | 57.41ms | 2.0× | 35.3% | PASS |
| file_writes | `oltp_update_index` | 187.14ms | 209.22ms | 1.1× | 5.6% | PASS |
| file_writes | `oltp_update_non_index` | 196.95ms | 139.60ms | 0.7× | 25.0% | PASS |
| file_writes | `oltp_delete_insert` | 216.81ms | 162.44ms | 0.7× | 18.6% | PASS |
| file_writes | `oltp_write_only` | 145.87ms | 115.30ms | 0.8× | 17.5% | PASS |
| file_writes | `types_delete_insert` | 157.43ms | 89.80ms | 0.6× | 16.0% | PASS |
| file_writes | `oltp_read_write` | 214.15ms | 178.12ms | 0.8× | 11.0% | PASS |
| ac_reads | `oltp_point_select` | 54.44ms | 47.03ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 19.03ms | 13.30ms | 0.7× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 17.37ms | 13.53ms | 0.8× | 1.8% | PASS |
| ac_reads | `oltp_order_range` | 3.32ms | 2.86ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.05ms | 3.67ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 5.89ms | 6.83ms | 1.2× | 1.1% | PASS |
| ac_reads | `select_random_points` | 23.71ms | 19.44ms | 0.8× | 2.8% | PASS |
| ac_reads | `select_random_ranges` | 5.24ms | 6.03ms | 1.2× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 6.05ms | 5.77ms | 1.0× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 29.99ms | 29.51ms | 1.0× | 0.5% | PASS |
| ac_reads | `index_join` | 11.67ms | 9.15ms | 0.8× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.72ms | 5.10ms | 1.4× | 1.2% | PASS |
| ac_reads | `types_table_scan` | 986.95ms | 1.01s | 1.0× | 4.4% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.09s | 0.9× | 4.8% | PASS |
| ac_reads | `oltp_read_only` | 147.34ms | 129.44ms | 0.9× | 3.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 33.97ms | 92.49ms | 2.7× | 51.6% | PASS |
| ac_writes | `oltp_insert_ac` | 44.02ms | 123.87ms | 2.8× | 59.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 50.96ms | 202.75ms | 4.0× | 55.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 52.88ms | 175.64ms | 3.3× | 55.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 37.22ms | 118.09ms | 3.2× | 57.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 30.58ms | 125.86ms | 4.1× | 49.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 45.61ms | 124.44ms | 2.7× | 57.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 48.31ms | 137.50ms | 2.8× | 74.7% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.56ms | 28.59ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 13.45ms | 11.07ms | 0.8× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 11.65ms | 10.64ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 2.64ms | 2.50ms | 0.9× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.43ms | 3.37ms | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.10ms | 4.51ms | 1.5× | 0.9% | PASS |
| mem_reads | `select_random_points` | 16.31ms | 15.74ms | 1.0× | 1.5% | PASS |
| mem_reads | `select_random_ranges` | 2.68ms | 4.10ms | 1.5× | 0.8% | PASS |
| mem_reads | `covering_index_scan` | 3.28ms | 3.48ms | 1.1× | 1.1% | PASS |
| mem_reads | `groupby_scan` | 27.67ms | 28.29ms | 1.0× | 0.4% | PASS |
| mem_reads | `index_join` | 8.43ms | 7.00ms | 0.8× | 1.6% | PASS |
| mem_reads | `index_join_scan` | 3.05ms | 4.73ms | 1.5× | 1.2% | PASS |
| mem_reads | `types_table_scan` | 867.88ms | 960.30ms | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 969.57ms | 1.04s | 1.1× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 104.33ms | 102.60ms | 1.0× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 179.34ms | 274.06ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 14.01ms | 30.02ms | 2.1× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 49.51ms | 109.42ms | 2.2× | 0.7% | PASS |
| mem_writes | `oltp_update_non_index` | 37.37ms | 65.91ms | 1.8× | 0.8% | PASS |
| mem_writes | `oltp_delete_insert` | 40.98ms | 82.37ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 21.86ms | 50.13ms | 2.3× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 29.14ms | 41.89ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 71.45ms | 108.89ms | 1.5× | 0.8% | PASS |
| file_reads | `oltp_point_select` | 95.42ms | 44.16ms | 0.5× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 20.70ms | 12.43ms | 0.6× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 19.20ms | 12.28ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 3.53ms | 2.78ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 4.31ms | 3.63ms | 0.8× | 0.7% | PASS |
| file_reads | `oltp_index_scan` | 10.28ms | 6.56ms | 0.6× | 0.7% | PASS |
| file_reads | `select_random_points` | 24.02ms | 17.49ms | 0.7× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 9.61ms | 5.89ms | 0.6× | 0.6% | PASS |
| file_reads | `covering_index_scan` | 10.49ms | 5.52ms | 0.5× | 0.6% | PASS |
| file_reads | `groupby_scan` | 28.66ms | 28.58ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 12.69ms | 8.67ms | 0.7× | 0.8% | PASS |
| file_reads | `index_join_scan` | 3.95ms | 4.88ms | 1.2× | 1.2% | PASS |
| file_reads | `types_table_scan` | 903.39ms | 972.65ms | 1.1× | 1.9% | PASS |
| file_reads | `table_scan` | 975.15ms | 1.04s | 1.1× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 204.58ms | 125.99ms | 0.6× | 0.5% | PASS |
| file_writes | `oltp_bulk_insert` | 243.95ms | 339.29ms | 1.4× | 3.1% | PASS |
| file_writes | `oltp_insert` | 31.18ms | 55.89ms | 1.8× | 6.3% | PASS |
| file_writes | `oltp_update_index` | 173.41ms | 195.65ms | 1.1× | 14.1% | PASS |
| file_writes | `oltp_update_non_index` | 138.56ms | 126.88ms | 0.9× | 6.4% | PASS |
| file_writes | `oltp_delete_insert` | 163.02ms | 149.12ms | 0.9× | 2.6% | PASS |
| file_writes | `oltp_write_only` | 116.57ms | 104.63ms | 0.9× | 5.0% | PASS |
| file_writes | `types_delete_insert` | 123.08ms | 83.02ms | 0.7× | 6.9% | PASS |
| file_writes | `oltp_read_write` | 170.54ms | 164.26ms | 1.0× | 3.9% | PASS |
| ac_reads | `oltp_point_select` | 48.22ms | 44.25ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 16.22ms | 12.46ms | 0.8× | 0.8% | PASS |
| ac_reads | `oltp_sum_range` | 14.71ms | 12.37ms | 0.8× | 0.8% | PASS |
| ac_reads | `oltp_order_range` | 3.17ms | 2.79ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 3.93ms | 3.65ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 5.78ms | 6.72ms | 1.2× | 2.5% | PASS |
| ac_reads | `select_random_points` | 19.41ms | 17.54ms | 0.9× | 1.1% | PASS |
| ac_reads | `select_random_ranges` | 4.98ms | 5.90ms | 1.2× | 0.6% | PASS |
| ac_reads | `covering_index_scan` | 5.87ms | 5.54ms | 0.9× | 0.6% | PASS |
| ac_reads | `groupby_scan` | 28.46ms | 28.63ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 10.51ms | 8.74ms | 0.8× | 0.7% | PASS |
| ac_reads | `index_join_scan` | 3.61ms | 4.92ms | 1.4× | 0.9% | PASS |
| ac_reads | `types_table_scan` | 877.57ms | 961.06ms | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 980.23ms | 1.05s | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_read_only` | 137.04ms | 125.89ms | 0.9× | 0.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.84ms | 53.00ms | 2.7× | 17.0% | PASS |
| ac_writes | `oltp_insert_ac` | 21.12ms | 64.75ms | 3.1× | 14.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.92ms | 79.41ms | 3.6× | 20.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 18.47ms | 62.33ms | 3.4× | 13.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 20.73ms | 69.01ms | 3.3× | 13.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.91ms | 74.55ms | 3.0× | 31.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.58ms | 62.45ms | 3.0× | 19.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.96ms | 80.75ms | 2.9× | 24.5% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 22.59ms | 25.15ms | 1.1× | 3.5% | PASS |
| mem_reads | `oltp_range_select` | 13.08ms | 12.75ms | 1.0× | 3.4% | PASS |
| mem_reads | `oltp_sum_range` | 11.59ms | 12.51ms | 1.1× | 3.3% | PASS |
| mem_reads | `oltp_order_range` | 2.52ms | 2.49ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_distinct_range` | 3.16ms | 3.11ms | 1.0× | 2.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.50ms | 4.97ms | 1.4× | 2.9% | PASS |
| mem_reads | `select_random_points` | 20.39ms | 22.51ms | 1.1× | 3.2% | PASS |
| mem_reads | `select_random_ranges` | 5.29ms | 5.85ms | 1.1× | 2.5% | PASS |
| mem_reads | `covering_index_scan` | 2.49ms | 2.54ms | 1.0× | 2.9% | PASS |
| mem_reads | `groupby_scan` | 23.22ms | 23.62ms | 1.0× | 2.2% | PASS |
| mem_reads | `index_join` | 5.12ms | 6.37ms | 1.2× | 2.8% | PASS |
| mem_reads | `index_join_scan` | 2.99ms | 4.64ms | 1.6× | 7.9% | PASS |
| mem_reads | `types_table_scan` | 811.04ms | 813.20ms | 1.0× | 3.7% | PASS |
| mem_reads | `table_scan` | 1.15s | 989.14ms | 0.9× | 2.2% | PASS |
| mem_reads | `oltp_read_only` | 104.31ms | 103.23ms | 1.0× | 2.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 153.26ms | 220.57ms | 1.4× | 1.9% | PASS |
| mem_writes | `oltp_insert` | 12.25ms | 23.89ms | 1.9× | 2.7% | PASS |
| mem_writes | `oltp_update_index` | 50.86ms | 100.01ms | 2.0× | 2.7% | PASS |
| mem_writes | `oltp_update_non_index` | 38.53ms | 62.20ms | 1.6× | 2.9% | PASS |
| mem_writes | `oltp_delete_insert` | 37.02ms | 73.87ms | 2.0× | 3.2% | PASS |
| mem_writes | `oltp_write_only` | 21.02ms | 47.22ms | 2.2× | 3.5% | PASS |
| mem_writes | `types_delete_insert` | 24.84ms | 41.39ms | 1.7× | 2.8% | PASS |
| mem_writes | `oltp_read_write` | 60.31ms | 96.26ms | 1.6× | 2.3% | PASS |
| file_reads | `oltp_point_select` | 78.15ms | 39.58ms | 0.5× | 1.5% | PASS |
| file_reads | `oltp_range_select` | 17.86ms | 13.65ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 16.57ms | 13.30ms | 0.8× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 3.08ms | 2.54ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 3.62ms | 3.09ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 9.16ms | 5.53ms | 0.6× | 3.3% | PASS |
| file_reads | `select_random_points` | 23.15ms | 21.05ms | 0.9× | 2.4% | PASS |
| file_reads | `select_random_ranges` | 10.49ms | 6.83ms | 0.7× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 8.69ms | 4.34ms | 0.5× | 3.3% | PASS |
| file_reads | `groupby_scan` | 24.03ms | 24.05ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join` | 8.62ms | 7.64ms | 0.9× | 3.3% | PASS |
| file_reads | `index_join_scan` | 3.68ms | 4.40ms | 1.2× | 4.0% | PASS |
| file_reads | `types_table_scan` | 746.56ms | 782.03ms | 1.0× | 1.8% | PASS |
| file_reads | `table_scan` | 846.57ms | 876.95ms | 1.0× | 2.0% | PASS |
| file_reads | `oltp_read_only` | 164.09ms | 111.97ms | 0.7× | 1.5% | PASS |
| file_writes | `oltp_bulk_insert` | 200.13ms | 259.78ms | 1.3× | 3.0% | PASS |
| file_writes | `oltp_insert` | 19.56ms | 33.99ms | 1.7× | 14.1% | PASS |
| file_writes | `oltp_update_index` | 143.64ms | 145.11ms | 1.0× | 2.1% | PASS |
| file_writes | `oltp_update_non_index` | 120.76ms | 99.72ms | 0.8× | 1.9% | PASS |
| file_writes | `oltp_delete_insert` | 111.51ms | 109.72ms | 1.0× | 4.4% | PASS |
| file_writes | `oltp_write_only` | 87.26ms | 88.86ms | 1.0× | 5.7% | PASS |
| file_writes | `types_delete_insert` | 80.15ms | 59.47ms | 0.7× | 4.6% | PASS |
| file_writes | `oltp_read_write` | 129.39ms | 139.01ms | 1.1× | 4.0% | PASS |
| ac_reads | `oltp_point_select` | 39.74ms | 39.31ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_range_select` | 14.03ms | 13.79ms | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_sum_range` | 13.37ms | 13.68ms | 1.0× | 2.4% | PASS |
| ac_reads | `oltp_order_range` | 2.65ms | 2.56ms | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_distinct_range` | 3.24ms | 3.09ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 5.22ms | 5.62ms | 1.1× | 4.5% | PASS |
| ac_reads | `select_random_points` | 19.48ms | 20.82ms | 1.1× | 3.0% | PASS |
| ac_reads | `select_random_ranges` | 7.17ms | 7.15ms | 1.0× | 2.1% | PASS |
| ac_reads | `covering_index_scan` | 5.20ms | 4.90ms | 0.9× | 3.2% | PASS |
| ac_reads | `groupby_scan` | 23.86ms | 24.00ms | 1.0× | 1.9% | PASS |
| ac_reads | `index_join` | 6.70ms | 8.19ms | 1.2× | 3.4% | PASS |
| ac_reads | `index_join_scan` | 3.27ms | 4.45ms | 1.4× | 4.0% | PASS |
| ac_reads | `types_table_scan` | 739.98ms | 772.98ms | 1.0× | 2.0% | PASS |
| ac_reads | `table_scan` | 838.23ms | 872.11ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_read_only` | 117.44ms | 116.71ms | 1.0× | 2.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.26ms | 55.73ms | 2.6× | 7.4% | PASS |
| ac_writes | `oltp_insert_ac` | 24.64ms | 73.02ms | 3.0× | 8.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.70ms | 72.52ms | 2.8× | 5.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.85ms | 60.53ms | 2.9× | 7.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.11ms | 64.46ms | 2.8× | 3.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.61ms | 65.56ms | 2.9× | 4.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.43ms | 57.81ms | 2.6× | 6.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 26.48ms | 70.83ms | 2.7× | 5.3% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 15s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 103.60ms | 130.00ms | 79.7% | 0.3% | PASS |
| `status_dirty_many_tables` | 107.97ms | 130.00ms | 83.1% | 0.5% | PASS |
| `diff_regular_working_one_table` | 99.42ms | 120.00ms | 82.8% | 0.3% | PASS |
| `diff_regular_working_many_tables` | 112.91ms | 140.00ms | 80.6% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 112.70ms | 140.00ms | 80.5% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 113.16ms | 140.00ms | 80.8% | 0.4% | PASS |
| `branch_list_many_branches` | 21.28ms | 35.00ms | 60.8% | 2.4% | PASS |
| `branch_create_delete` | 23.70ms | 40.00ms | 59.3% | 2.8% | PASS |
| `checkout_branch_clean` | 62.96ms | 150.00ms | 42.0% | 0.7% | PASS |
| `merge_data_no_conflicts` | 27.96ms | 50.00ms | 55.9% | 1.6% | PASS |
| `merge_schema_no_conflicts` | 20.03ms | 35.00ms | 57.2% | 2.3% | PASS |
| `merge_data_conflicts` | 32.22ms | 180.00ms | 17.9% | 1.2% | PASS |
| `merge_data_conflicts_with_resolve` | 32.68ms | 180.00ms | 18.2% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
