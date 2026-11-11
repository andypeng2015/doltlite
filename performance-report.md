# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-11 18:54 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261101.186.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/43067311978)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.46s | 8.97s | 1.1× | 1.7% | **PASS** |
| Writes | 1.75s | 2.97s | 1.7× | 1.8% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.29s | 9.17s | 1.0× | 1.4% | **PASS** |
| Writes | 3.95s | 4.23s | 1.1× | 3.6% | **PASS** |
| Autocommit writes | 759.20ms | 2.27s | 3.0× | 6.2% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.03s | 2.14s | 1.1× | 1.5% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.13s | 2.24s | 1.1× | 1.3% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.51s | 2.79s | 1.1× | 1.7% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 1.79s | 1.79s | 1.0× | 2.9% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 338.24ms | 569.10ms | 1.7× | 1.3% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 471.86ms | 805.07ms | 1.7× | 1.6% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 591.74ms | 1.03s | 1.7× | 1.8% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 349.07ms | 564.51ms | 1.6× | 2.3% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.22s | 2.19s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.40s | 2.31s | 1.0× | 0.9% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.83s | 2.88s | 1.0× | 1.6% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 1.85s | 1.79s | 1.0× | 2.3% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 811.10ms | 845.92ms | 1.0× | 3.2% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.41s | 1.31s | 0.9× | 11.5% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 814.16ms | 1.13s | 1.4× | 1.5% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 908.25ms | 941.01ms | 1.0× | 3.6% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.06s | 2.19s | 1.1× | 1.1% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.30s | 2.33s | 1.0× | 0.9% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.80s | 2.92s | 1.0× | 1.8% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 1.71s | 1.78s | 1.0× | 2.8% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 151.82ms | 498.71ms | 3.3× | 9.5% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 208.20ms | 677.65ms | 3.3× | 45.8% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 201.27ms | 593.87ms | 3.0× | 5.4% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 197.91ms | 503.29ms | 2.5× | 5.0% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 18.86ms | 22.21ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 8.88ms | 8.92ms | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_sum_range` | 7.59ms | 8.54ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 2.16ms | 2.28ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 2.92ms | 3.13ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.09ms | 3.86ms | 1.3× | 1.7% | PASS |
| mem_reads | `select_random_points` | 8.51ms | 9.08ms | 1.1× | 2.1% | PASS |
| mem_reads | `select_random_ranges` | 2.60ms | 3.16ms | 1.2× | 2.2% | PASS |
| mem_reads | `covering_index_scan` | 3.28ms | 3.17ms | 1.0× | 1.3% | PASS |
| mem_reads | `groupby_scan` | 24.62ms | 27.08ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 4.64ms | 6.09ms | 1.3× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 2.76ms | 4.01ms | 1.5× | 1.7% | PASS |
| mem_reads | `types_table_scan` | 868.00ms | 932.48ms | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 989.03ms | 1.02s | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 83.24ms | 90.89ms | 1.1× | 1.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 136.63ms | 207.41ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 11.80ms | 21.67ms | 1.8× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 39.59ms | 77.23ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 27.92ms | 48.14ms | 1.7× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 34.69ms | 59.15ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 17.54ms | 38.69ms | 2.2× | 1.8% | PASS |
| mem_writes | `types_delete_insert` | 19.11ms | 30.48ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 50.96ms | 86.34ms | 1.7× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 86.77ms | 38.26ms | 0.4× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 15.92ms | 10.70ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 14.91ms | 10.44ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_order_range` | 3.00ms | 2.53ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_distinct_range` | 3.77ms | 3.35ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 10.24ms | 5.98ms | 0.6× | 1.3% | PASS |
| file_reads | `select_random_points` | 15.86ms | 10.86ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_ranges` | 9.61ms | 4.90ms | 0.5× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 10.55ms | 5.28ms | 0.5× | 0.9% | PASS |
| file_reads | `groupby_scan` | 25.38ms | 27.36ms | 1.1× | 0.6% | PASS |
| file_reads | `index_join` | 8.54ms | 7.61ms | 0.9× | 1.8% | PASS |
| file_reads | `index_join_scan` | 3.58ms | 4.21ms | 1.2× | 2.0% | PASS |
| file_reads | `types_table_scan` | 855.18ms | 926.50ms | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 973.47ms | 1.02s | 1.0× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 178.97ms | 112.93ms | 0.6× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 170.19ms | 247.87ms | 1.5× | 2.4% | PASS |
| file_writes | `oltp_insert` | 19.68ms | 27.97ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_update_index` | 137.75ms | 128.06ms | 0.9× | 4.7% | PASS |
| file_writes | `oltp_update_non_index` | 106.78ms | 96.65ms | 0.9× | 4.0% | PASS |
| file_writes | `oltp_delete_insert` | 110.98ms | 107.93ms | 1.0× | 2.5% | PASS |
| file_writes | `oltp_write_only` | 80.71ms | 69.18ms | 0.9× | 3.1% | PASS |
| file_writes | `types_delete_insert` | 64.71ms | 50.09ms | 0.8× | 7.5% | PASS |
| file_writes | `oltp_read_write` | 120.30ms | 118.16ms | 1.0× | 3.4% | PASS |
| ac_reads | `oltp_point_select` | 39.29ms | 38.43ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 11.56ms | 10.73ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 10.21ms | 10.36ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 2.59ms | 2.53ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_distinct_range` | 3.29ms | 3.37ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 5.65ms | 6.00ms | 1.1× | 1.5% | PASS |
| ac_reads | `select_random_points` | 11.12ms | 10.82ms | 1.0× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 4.96ms | 4.92ms | 1.0× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 5.89ms | 5.31ms | 0.9× | 0.7% | PASS |
| ac_reads | `groupby_scan` | 24.94ms | 27.34ms | 1.1× | 0.5% | PASS |
| ac_reads | `index_join` | 6.22ms | 7.72ms | 1.2× | 1.6% | PASS |
| ac_reads | `index_join_scan` | 3.16ms | 4.21ms | 1.3× | 2.0% | PASS |
| ac_reads | `types_table_scan` | 854.12ms | 926.01ms | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 970.82ms | 1.02s | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 111.08ms | 113.22ms | 1.0× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.17ms | 47.02ms | 2.7× | 6.5% | PASS |
| ac_writes | `oltp_insert_ac` | 19.15ms | 61.95ms | 3.2× | 6.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.36ms | 70.38ms | 3.5× | 10.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.23ms | 56.35ms | 3.3× | 10.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 18.89ms | 67.50ms | 3.6× | 12.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.10ms | 65.21ms | 3.4× | 9.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 16.85ms | 60.13ms | 3.6× | 9.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 23.07ms | 70.16ms | 3.0× | 6.0% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.92ms | 28.31ms | 1.0× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 13.86ms | 10.77ms | 0.8× | 1.2% | PASS |
| mem_reads | `oltp_sum_range` | 12.23ms | 10.77ms | 0.9× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 2.90ms | 2.56ms | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.60ms | 3.39ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.12ms | 4.59ms | 1.5× | 1.4% | PASS |
| mem_reads | `select_random_points` | 17.19ms | 16.49ms | 1.0× | 0.9% | PASS |
| mem_reads | `select_random_ranges` | 2.81ms | 4.12ms | 1.5× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 3.31ms | 3.49ms | 1.1× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 28.18ms | 28.32ms | 1.0× | 0.5% | PASS |
| mem_reads | `index_join` | 8.76ms | 6.87ms | 0.8× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 3.31ms | 4.62ms | 1.4× | 2.9% | PASS |
| mem_reads | `types_table_scan` | 888.54ms | 967.73ms | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 993.90ms | 1.05s | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 115.68ms | 104.27ms | 0.9× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 180.22ms | 281.64ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 14.01ms | 31.44ms | 2.2× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 57.97ms | 122.99ms | 2.1× | 2.1% | PASS |
| mem_writes | `oltp_update_non_index` | 43.37ms | 72.71ms | 1.7× | 2.7% | PASS |
| mem_writes | `oltp_delete_insert` | 47.53ms | 89.41ms | 1.9× | 2.4% | PASS |
| mem_writes | `oltp_write_only` | 23.99ms | 52.63ms | 2.2× | 3.1% | PASS |
| mem_writes | `types_delete_insert` | 30.59ms | 43.45ms | 1.4× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 74.18ms | 110.79ms | 1.5× | 0.9% | PASS |
| file_reads | `oltp_point_select` | 96.49ms | 44.23ms | 0.5× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 21.47ms | 12.41ms | 0.6× | 0.8% | PASS |
| file_reads | `oltp_sum_range` | 19.85ms | 12.39ms | 0.6× | 0.6% | PASS |
| file_reads | `oltp_order_range` | 3.67ms | 2.79ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.46ms | 3.63ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 10.35ms | 6.64ms | 0.6× | 0.6% | PASS |
| file_reads | `select_random_points` | 25.87ms | 18.43ms | 0.7× | 1.2% | PASS |
| file_reads | `select_random_ranges` | 9.80ms | 5.95ms | 0.6× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 10.59ms | 5.61ms | 0.5× | 1.0% | PASS |
| file_reads | `groupby_scan` | 29.36ms | 28.77ms | 1.0× | 0.6% | PASS |
| file_reads | `index_join` | 13.24ms | 8.73ms | 0.7× | 0.9% | PASS |
| file_reads | `index_join_scan` | 4.11ms | 5.01ms | 1.2× | 0.9% | PASS |
| file_reads | `types_table_scan` | 891.06ms | 966.13ms | 1.1× | 1.0% | PASS |
| file_reads | `table_scan` | 1.05s | 1.06s | 1.0× | 3.1% | PASS |
| file_reads | `oltp_read_only` | 209.04ms | 126.33ms | 0.6× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 292.35ms | 360.71ms | 1.2× | 10.6% | PASS |
| file_writes | `oltp_insert` | 30.77ms | 57.36ms | 1.9× | 5.3% | PASS |
| file_writes | `oltp_update_index` | 184.62ms | 208.35ms | 1.1× | 10.2% | PASS |
| file_writes | `oltp_update_non_index` | 205.14ms | 144.48ms | 0.7× | 31.0% | PASS |
| file_writes | `oltp_delete_insert` | 213.97ms | 160.22ms | 0.7× | 20.7% | PASS |
| file_writes | `oltp_write_only` | 137.76ms | 110.16ms | 0.8× | 7.8% | PASS |
| file_writes | `types_delete_insert` | 157.67ms | 100.01ms | 0.6× | 14.6% | PASS |
| file_writes | `oltp_read_write` | 189.36ms | 169.91ms | 0.9× | 12.3% | PASS |
| ac_reads | `oltp_point_select` | 49.07ms | 44.30ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 17.06ms | 12.50ms | 0.7× | 0.9% | PASS |
| ac_reads | `oltp_sum_range` | 15.56ms | 12.53ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.27ms | 2.80ms | 0.9× | 0.6% | PASS |
| ac_reads | `oltp_distinct_range` | 4.02ms | 3.62ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 5.77ms | 6.63ms | 1.1× | 0.7% | PASS |
| ac_reads | `select_random_points` | 20.74ms | 18.23ms | 0.9× | 1.1% | PASS |
| ac_reads | `select_random_ranges` | 5.17ms | 5.97ms | 1.2× | 0.7% | PASS |
| ac_reads | `covering_index_scan` | 5.93ms | 5.63ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 29.02ms | 28.77ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 11.13ms | 8.80ms | 0.8× | 1.2% | PASS |
| ac_reads | `index_join_scan` | 3.69ms | 5.00ms | 1.4× | 0.7% | PASS |
| ac_reads | `types_table_scan` | 890.10ms | 969.01ms | 1.1× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.09s | 1.08s | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_read_only` | 146.65ms | 127.58ms | 0.9× | 1.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.75ms | 56.33ms | 2.5× | 21.7% | PASS |
| ac_writes | `oltp_insert_ac` | 28.20ms | 128.74ms | 4.6× | 59.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.23ms | 99.30ms | 3.6× | 46.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 28.06ms | 73.67ms | 2.6× | 69.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.87ms | 84.23ms | 3.4× | 45.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.33ms | 88.53ms | 3.6× | 45.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.78ms | 70.58ms | 2.7× | 47.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 26.98ms | 76.29ms | 2.8× | 24.6% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.99ms | 37.98ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 16.51ms | 14.16ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 15.07ms | 13.66ms | 0.9× | 2.6% | PASS |
| mem_reads | `oltp_order_range` | 3.33ms | 3.26ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_distinct_range` | 4.25ms | 4.38ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 3.93ms | 6.03ms | 1.5× | 1.4% | PASS |
| mem_reads | `select_random_points` | 22.43ms | 20.36ms | 0.9× | 2.8% | PASS |
| mem_reads | `select_random_ranges` | 3.42ms | 5.32ms | 1.6× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 4.20ms | 4.61ms | 1.1× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 35.49ms | 35.89ms | 1.0× | 1.4% | PASS |
| mem_reads | `index_join` | 10.66ms | 9.28ms | 0.9× | 1.9% | PASS |
| mem_reads | `index_join_scan` | 3.80ms | 5.40ms | 1.4× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.19s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.18s | 1.29s | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 136.49ms | 139.57ms | 1.0× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 238.90ms | 373.40ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 18.22ms | 39.97ms | 2.2× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 64.64ms | 145.32ms | 2.2× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 49.41ms | 89.93ms | 1.8× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 53.39ms | 108.26ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 28.37ms | 66.43ms | 2.3× | 1.7% | FAIL |
| mem_writes | `types_delete_insert` | 39.21ms | 58.29ms | 1.5× | 2.0% | PASS |
| mem_writes | `oltp_read_write` | 99.61ms | 151.09ms | 1.5× | 2.3% | PASS |
| file_reads | `oltp_point_select` | 105.83ms | 56.17ms | 0.5× | 1.5% | PASS |
| file_reads | `oltp_range_select` | 25.01ms | 16.39ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 23.16ms | 15.92ms | 0.7× | 2.2% | PASS |
| file_reads | `oltp_order_range` | 4.45ms | 3.69ms | 0.8× | 3.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.41ms | 4.81ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_index_scan` | 11.39ms | 8.17ms | 0.7× | 1.0% | PASS |
| file_reads | `select_random_points` | 30.80ms | 23.23ms | 0.8× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 10.51ms | 7.18ms | 0.7× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.69ms | 6.74ms | 0.6× | 2.1% | PASS |
| file_reads | `groupby_scan` | 37.17ms | 36.57ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join` | 16.36ms | 11.69ms | 0.7× | 4.7% | PASS |
| file_reads | `index_join_scan` | 5.31ms | 6.15ms | 1.2× | 2.8% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.21s | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 1.22s | 1.31s | 1.1× | 1.6% | PASS |
| file_reads | `oltp_read_only` | 248.77ms | 168.77ms | 0.7× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 257.21ms | 389.32ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 25.79ms | 47.40ms | 1.8× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 98.61ms | 162.01ms | 1.6× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 91.09ms | 103.59ms | 1.1× | 10.6% | PASS |
| file_writes | `oltp_delete_insert` | 87.73ms | 122.35ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 59.60ms | 77.48ms | 1.3× | 1.8% | PASS |
| file_writes | `types_delete_insert` | 64.45ms | 65.88ms | 1.0× | 1.4% | PASS |
| file_writes | `oltp_read_write` | 129.67ms | 161.06ms | 1.2× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 58.24ms | 55.60ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_range_select` | 19.87ms | 16.27ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 17.97ms | 15.88ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.84ms | 3.65ms | 0.9× | 2.7% | PASS |
| ac_reads | `oltp_distinct_range` | 4.79ms | 4.73ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.53ms | 8.21ms | 1.3× | 2.1% | PASS |
| ac_reads | `select_random_points` | 25.75ms | 23.64ms | 0.9× | 2.3% | PASS |
| ac_reads | `select_random_ranges` | 6.07ms | 7.27ms | 1.2× | 1.7% | PASS |
| ac_reads | `covering_index_scan` | 7.44ms | 6.80ms | 0.9× | 2.2% | PASS |
| ac_reads | `groupby_scan` | 37.16ms | 37.04ms | 1.0× | 1.4% | PASS |
| ac_reads | `index_join` | 13.01ms | 11.29ms | 0.9× | 3.3% | PASS |
| ac_reads | `index_join_scan` | 4.47ms | 5.93ms | 1.3× | 3.4% | PASS |
| ac_reads | `types_table_scan` | 1.11s | 1.22s | 1.1× | 1.7% | PASS |
| ac_reads | `table_scan` | 1.30s | 1.33s | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_read_only` | 182.95ms | 171.93ms | 0.9× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.27ms | 57.80ms | 2.6× | 5.3% | PASS |
| ac_writes | `oltp_insert_ac` | 25.18ms | 74.63ms | 3.0× | 5.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.92ms | 87.18ms | 3.2× | 5.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.04ms | 68.06ms | 3.1× | 5.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.17ms | 78.89ms | 3.1× | 5.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.54ms | 76.56ms | 3.0× | 5.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.38ms | 67.76ms | 3.0× | 5.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.77ms | 83.00ms | 2.6× | 5.0% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 22.98ms | 24.93ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_range_select` | 12.94ms | 12.61ms | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_sum_range` | 11.34ms | 11.79ms | 1.0× | 3.0% | PASS |
| mem_reads | `oltp_order_range` | 2.32ms | 2.28ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_distinct_range` | 2.91ms | 2.82ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_index_scan` | 3.08ms | 4.24ms | 1.4× | 2.9% | PASS |
| mem_reads | `select_random_points` | 16.46ms | 18.95ms | 1.2× | 3.0% | PASS |
| mem_reads | `select_random_ranges` | 4.58ms | 5.03ms | 1.1× | 1.7% | PASS |
| mem_reads | `covering_index_scan` | 2.35ms | 2.81ms | 1.2× | 6.9% | PASS |
| mem_reads | `groupby_scan` | 23.89ms | 22.64ms | 0.9× | 1.9% | PASS |
| mem_reads | `index_join` | 4.85ms | 6.15ms | 1.3× | 3.4% | PASS |
| mem_reads | `index_join_scan` | 3.00ms | 4.54ms | 1.5× | 4.6% | PASS |
| mem_reads | `types_table_scan` | 715.33ms | 743.08ms | 1.0× | 1.7% | PASS |
| mem_reads | `table_scan` | 883.10ms | 843.25ms | 1.0× | 3.6% | PASS |
| mem_reads | `oltp_read_only` | 83.73ms | 88.59ms | 1.1× | 3.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 143.07ms | 196.97ms | 1.4× | 1.8% | PASS |
| mem_writes | `oltp_insert` | 10.79ms | 20.51ms | 1.9× | 2.1% | PASS |
| mem_writes | `oltp_update_index` | 40.30ms | 76.72ms | 1.9× | 2.7% | PASS |
| mem_writes | `oltp_update_non_index` | 30.62ms | 51.01ms | 1.7× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 29.21ms | 58.35ms | 2.0× | 1.8% | PASS |
| mem_writes | `oltp_write_only` | 17.00ms | 38.45ms | 2.3× | 2.5% | PASS |
| mem_writes | `types_delete_insert` | 20.39ms | 32.28ms | 1.6× | 3.0% | PASS |
| mem_writes | `oltp_read_write` | 57.68ms | 90.23ms | 1.6× | 2.7% | PASS |
| file_reads | `oltp_point_select` | 73.49ms | 37.07ms | 0.5× | 2.0% | PASS |
| file_reads | `oltp_range_select` | 17.18ms | 13.01ms | 0.8× | 2.8% | PASS |
| file_reads | `oltp_sum_range` | 16.29ms | 12.87ms | 0.8× | 2.9% | PASS |
| file_reads | `oltp_order_range` | 2.97ms | 2.49ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 3.56ms | 3.02ms | 0.8× | 2.3% | PASS |
| file_reads | `oltp_index_scan` | 9.18ms | 5.88ms | 0.6× | 2.8% | PASS |
| file_reads | `select_random_points` | 24.28ms | 21.16ms | 0.9× | 3.3% | PASS |
| file_reads | `select_random_ranges` | 10.49ms | 6.89ms | 0.7× | 2.3% | PASS |
| file_reads | `covering_index_scan` | 8.56ms | 4.61ms | 0.5× | 2.3% | PASS |
| file_reads | `groupby_scan` | 24.89ms | 23.43ms | 0.9× | 1.8% | PASS |
| file_reads | `index_join` | 8.27ms | 8.04ms | 1.0× | 4.5% | PASS |
| file_reads | `index_join_scan` | 3.59ms | 4.28ms | 1.2× | 6.0% | PASS |
| file_reads | `types_table_scan` | 691.89ms | 719.33ms | 1.0× | 2.0% | PASS |
| file_reads | `table_scan` | 797.21ms | 825.13ms | 1.0× | 2.1% | PASS |
| file_reads | `oltp_read_only` | 155.19ms | 105.87ms | 0.7× | 1.6% | PASS |
| file_writes | `oltp_bulk_insert` | 189.63ms | 245.68ms | 1.3× | 3.5% | PASS |
| file_writes | `oltp_insert` | 19.48ms | 32.48ms | 1.7× | 12.3% | PASS |
| file_writes | `oltp_update_index` | 140.96ms | 139.09ms | 1.0× | 3.5% | PASS |
| file_writes | `oltp_update_non_index` | 121.26ms | 99.11ms | 0.8× | 3.0% | PASS |
| file_writes | `oltp_delete_insert` | 125.85ms | 117.73ms | 0.9× | 2.5% | PASS |
| file_writes | `oltp_write_only` | 91.00ms | 89.54ms | 1.0× | 3.8% | PASS |
| file_writes | `types_delete_insert` | 79.85ms | 69.41ms | 0.9× | 8.3% | PASS |
| file_writes | `oltp_read_write` | 140.21ms | 147.96ms | 1.1× | 7.4% | PASS |
| ac_reads | `oltp_point_select` | 41.47ms | 41.41ms | 1.0× | 4.0% | PASS |
| ac_reads | `oltp_range_select` | 14.79ms | 14.05ms | 0.9× | 5.3% | PASS |
| ac_reads | `oltp_sum_range` | 11.98ms | 12.73ms | 1.1× | 3.7% | PASS |
| ac_reads | `oltp_order_range` | 2.39ms | 2.37ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_distinct_range` | 2.96ms | 2.89ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 4.66ms | 5.01ms | 1.1× | 2.4% | PASS |
| ac_reads | `select_random_points` | 18.38ms | 20.05ms | 1.1× | 3.1% | PASS |
| ac_reads | `select_random_ranges` | 6.51ms | 6.65ms | 1.0× | 2.9% | PASS |
| ac_reads | `covering_index_scan` | 4.61ms | 4.43ms | 1.0× | 3.7% | PASS |
| ac_reads | `groupby_scan` | 24.12ms | 22.90ms | 0.9× | 2.2% | PASS |
| ac_reads | `index_join` | 6.48ms | 7.93ms | 1.2× | 3.8% | PASS |
| ac_reads | `index_join_scan` | 3.18ms | 4.25ms | 1.3× | 2.8% | PASS |
| ac_reads | `types_table_scan` | 690.84ms | 719.06ms | 1.0× | 1.8% | PASS |
| ac_reads | `table_scan` | 778.07ms | 809.62ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_read_only` | 102.44ms | 104.31ms | 1.0× | 2.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.86ms | 53.79ms | 2.5× | 4.7% | PASS |
| ac_writes | `oltp_insert_ac` | 25.51ms | 64.09ms | 2.5× | 4.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.90ms | 70.00ms | 2.6× | 5.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.32ms | 58.47ms | 2.7× | 5.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.64ms | 65.50ms | 2.7× | 3.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.13ms | 65.50ms | 2.6× | 5.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.30ms | 56.08ms | 2.2× | 10.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.24ms | 69.85ms | 2.6× | 4.2% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 6s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 91.74ms | 130.00ms | 70.6% | 0.5% | PASS |
| `status_dirty_many_tables` | 95.65ms | 130.00ms | 73.6% | 0.5% | PASS |
| `diff_regular_working_one_table` | 87.30ms | 120.00ms | 72.8% | 0.5% | PASS |
| `diff_regular_working_many_tables` | 100.84ms | 140.00ms | 72.0% | 0.6% | PASS |
| `diff_stat_working_many_tables` | 100.11ms | 140.00ms | 71.5% | 0.5% | PASS |
| `diff_schema_working_many_tables` | 101.47ms | 140.00ms | 72.5% | 0.4% | PASS |
| `branch_list_many_branches` | 20.73ms | 35.00ms | 59.2% | 1.5% | PASS |
| `branch_create_delete` | 23.30ms | 40.00ms | 58.3% | 1.4% | PASS |
| `checkout_branch_clean` | 58.92ms | 150.00ms | 39.3% | 0.9% | PASS |
| `merge_data_no_conflicts` | 27.34ms | 50.00ms | 54.7% | 1.3% | PASS |
| `merge_schema_no_conflicts` | 20.06ms | 35.00ms | 57.3% | 1.3% | PASS |
| `merge_data_conflicts` | 30.29ms | 180.00ms | 16.8% | 1.1% | PASS |
| `merge_data_conflicts_with_resolve` | 30.40ms | 180.00ms | 16.9% | 1.1% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
