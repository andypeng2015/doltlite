# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-23 23:09 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261116.202.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/45271429978)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.88s | 10.64s | 1.1× | 1.5% | **PASS** |
| Writes | 2.11s | 3.62s | 1.7× | 1.6% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.88s | 10.93s | 1.0× | 1.6% | **PASS** |
| Writes | 3.49s | 4.35s | 1.2× | 2.1% | **PASS** |
| Autocommit writes | 753.03ms | 2.29s | 3.0× | 7.0% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.51s | 2.70s | 1.1× | 2.0% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.12s | 2.24s | 1.1× | 0.9% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.50s | 2.79s | 1.1× | 1.7% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.74s | 2.91s | 1.1× | 1.5% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 437.27ms | 763.54ms | 1.7× | 1.3% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 498.38ms | 821.82ms | 1.6× | 2.2% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 581.53ms | 1.02s | 1.8× | 1.6% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 593.86ms | 1.01s | 1.7× | 1.9% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 2.77s | 2.78s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.41s | 2.32s | 1.0× | 1.1% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.76s | 2.86s | 1.0× | 1.7% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.94s | 2.97s | 1.0× | 2.1% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 622.45ms | 863.64ms | 1.4× | 1.9% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.29s | 1.26s | 1.0× | 4.0% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 815.38ms | 1.13s | 1.4× | 1.8% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 771.42ms | 1.10s | 1.4× | 2.1% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.67s | 2.79s | 1.0× | 1.9% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.19s | 2.29s | 1.0× | 0.9% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.60s | 2.86s | 1.1× | 1.9% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.77s | 2.97s | 1.1× | 1.8% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 192.57ms | 606.62ms | 3.2× | 8.0% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 157.21ms | 474.85ms | 3.0× | 8.1% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 201.61ms | 602.59ms | 3.0× | 6.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 201.65ms | 605.97ms | 3.0× | 5.7% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 25.25ms | 30.49ms | 1.2× | 2.3% | PASS |
| mem_reads | `oltp_range_select` | 11.40ms | 11.79ms | 1.0× | 2.0% | PASS |
| mem_reads | `oltp_sum_range` | 9.60ms | 11.33ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 2.71ms | 2.97ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_distinct_range` | 3.70ms | 4.12ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.91ms | 5.42ms | 1.4× | 2.6% | PASS |
| mem_reads | `select_random_points` | 9.82ms | 11.01ms | 1.1× | 2.2% | PASS |
| mem_reads | `select_random_ranges` | 3.11ms | 4.02ms | 1.3× | 2.0% | PASS |
| mem_reads | `covering_index_scan` | 4.18ms | 4.39ms | 1.1× | 2.3% | PASS |
| mem_reads | `groupby_scan` | 30.66ms | 33.40ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 5.79ms | 8.16ms | 1.4× | 1.5% | PASS |
| mem_reads | `index_join_scan` | 3.22ms | 4.52ms | 1.4× | 2.4% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.17s | 1.1× | 1.4% | PASS |
| mem_reads | `table_scan` | 1.24s | 1.28s | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_read_only` | 112.62ms | 124.50ms | 1.1× | 3.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 175.74ms | 278.50ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 14.94ms | 28.56ms | 1.9× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 49.72ms | 101.43ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 34.32ms | 63.87ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 43.50ms | 78.17ms | 1.8× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 21.45ms | 50.18ms | 2.3× | 1.2% | FAIL |
| mem_writes | `types_delete_insert` | 24.39ms | 41.53ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 73.20ms | 121.30ms | 1.7× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 94.02ms | 47.95ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 18.37ms | 13.83ms | 0.8× | 2.2% | PASS |
| file_reads | `oltp_sum_range` | 17.07ms | 13.54ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_order_range` | 3.62ms | 3.33ms | 0.9× | 2.7% | PASS |
| file_reads | `oltp_distinct_range` | 4.67ms | 4.45ms | 1.0× | 3.1% | PASS |
| file_reads | `oltp_index_scan` | 11.45ms | 7.58ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 18.46ms | 13.24ms | 0.7× | 1.9% | PASS |
| file_reads | `select_random_ranges` | 10.08ms | 5.84ms | 0.6× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.27ms | 6.37ms | 0.6× | 1.1% | PASS |
| file_reads | `groupby_scan` | 31.52ms | 33.83ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 9.82ms | 9.64ms | 1.0× | 1.2% | PASS |
| file_reads | `index_join_scan` | 4.23ms | 5.03ms | 1.2× | 2.3% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.17s | 1.1× | 1.2% | PASS |
| file_reads | `table_scan` | 1.28s | 1.29s | 1.0× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 215.16ms | 151.03ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 189.38ms | 291.51ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 22.72ms | 32.54ms | 1.4× | 2.1% | PASS |
| file_writes | `oltp_update_index` | 82.59ms | 123.37ms | 1.5× | 2.3% | PASS |
| file_writes | `oltp_update_non_index` | 61.30ms | 79.90ms | 1.3× | 1.9% | PASS |
| file_writes | `oltp_delete_insert` | 71.02ms | 94.57ms | 1.3× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 48.00ms | 61.30ms | 1.3× | 2.5% | PASS |
| file_writes | `types_delete_insert` | 42.07ms | 46.36ms | 1.1× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 105.36ms | 134.10ms | 1.3× | 1.5% | PASS |
| ac_reads | `oltp_point_select` | 48.69ms | 49.39ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 14.94ms | 14.12ms | 0.9× | 2.8% | PASS |
| ac_reads | `oltp_sum_range` | 12.92ms | 13.82ms | 1.1× | 2.4% | PASS |
| ac_reads | `oltp_order_range` | 3.25ms | 3.29ms | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.24ms | 4.46ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_index_scan` | 6.79ms | 7.57ms | 1.1× | 2.4% | PASS |
| ac_reads | `select_random_points` | 13.95ms | 13.59ms | 1.0× | 2.4% | PASS |
| ac_reads | `select_random_ranges` | 5.65ms | 5.90ms | 1.0× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 7.02ms | 6.55ms | 0.9× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 31.49ms | 34.12ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 7.80ms | 10.07ms | 1.3× | 2.4% | PASS |
| ac_reads | `index_join_scan` | 3.79ms | 5.15ms | 1.4× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 1.11s | 1.19s | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.26s | 1.29s | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 140.63ms | 147.61ms | 1.0× | 1.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.11ms | 59.29ms | 2.9× | 5.1% | PASS |
| ac_writes | `oltp_insert_ac` | 25.26ms | 78.15ms | 3.1× | 10.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.65ms | 88.66ms | 3.3× | 8.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.88ms | 68.03ms | 3.1× | 7.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.58ms | 79.68ms | 3.2× | 7.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.58ms | 79.05ms | 3.2× | 8.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.29ms | 69.68ms | 3.3× | 8.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.21ms | 84.09ms | 3.0× | 4.4% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.16ms | 28.37ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 13.61ms | 10.94ms | 0.8× | 1.0% | PASS |
| mem_reads | `oltp_sum_range` | 12.16ms | 10.90ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 2.68ms | 2.51ms | 0.9× | 0.8% | PASS |
| mem_reads | `oltp_distinct_range` | 3.48ms | 3.36ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.10ms | 4.61ms | 1.5× | 1.1% | PASS |
| mem_reads | `select_random_points` | 17.30ms | 16.36ms | 0.9× | 0.9% | PASS |
| mem_reads | `select_random_ranges` | 2.76ms | 4.12ms | 1.5× | 0.9% | PASS |
| mem_reads | `covering_index_scan` | 3.29ms | 3.46ms | 1.1× | 1.0% | PASS |
| mem_reads | `groupby_scan` | 28.00ms | 28.43ms | 1.0× | 0.5% | PASS |
| mem_reads | `index_join` | 8.72ms | 6.96ms | 0.8× | 0.7% | PASS |
| mem_reads | `index_join_scan` | 3.18ms | 4.96ms | 1.6× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 887.40ms | 964.62ms | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 998.73ms | 1.05s | 1.0× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 106.58ms | 103.52ms | 1.0× | 0.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 177.84ms | 277.43ms | 1.6× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 13.68ms | 30.28ms | 2.2× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 55.30ms | 119.30ms | 2.2× | 2.1% | PASS |
| mem_writes | `oltp_update_non_index` | 46.64ms | 75.49ms | 1.6× | 2.3% | PASS |
| mem_writes | `oltp_delete_insert` | 46.92ms | 90.12ms | 1.9× | 3.2% | PASS |
| mem_writes | `oltp_write_only` | 25.10ms | 54.59ms | 2.2× | 2.6% | PASS |
| mem_writes | `types_delete_insert` | 35.01ms | 47.53ms | 1.4× | 2.2% | PASS |
| mem_writes | `oltp_read_write` | 97.89ms | 127.08ms | 1.3× | 1.5% | PASS |
| file_reads | `oltp_point_select` | 99.34ms | 45.42ms | 0.5× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 23.01ms | 12.77ms | 0.6× | 3.4% | PASS |
| file_reads | `oltp_sum_range` | 22.04ms | 13.31ms | 0.6× | 1.7% | PASS |
| file_reads | `oltp_order_range` | 3.62ms | 2.79ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_distinct_range` | 4.37ms | 3.62ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 10.32ms | 6.71ms | 0.6× | 1.1% | PASS |
| file_reads | `select_random_points` | 26.20ms | 18.37ms | 0.7× | 3.6% | PASS |
| file_reads | `select_random_ranges` | 9.77ms | 5.93ms | 0.6× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 10.51ms | 5.55ms | 0.5× | 0.8% | PASS |
| file_reads | `groupby_scan` | 29.03ms | 28.72ms | 1.0× | 0.5% | PASS |
| file_reads | `index_join` | 13.00ms | 8.72ms | 0.7× | 1.2% | PASS |
| file_reads | `index_join_scan` | 4.06ms | 5.04ms | 1.2× | 1.5% | PASS |
| file_reads | `types_table_scan` | 897.37ms | 970.34ms | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 1.05s | 1.07s | 1.0× | 3.5% | PASS |
| file_reads | `oltp_read_only` | 207.09ms | 126.90ms | 0.6× | 0.5% | PASS |
| file_writes | `oltp_bulk_insert` | 260.43ms | 349.18ms | 1.3× | 4.0% | PASS |
| file_writes | `oltp_insert` | 33.77ms | 54.70ms | 1.6× | 5.3% | PASS |
| file_writes | `oltp_update_index` | 177.44ms | 202.94ms | 1.1× | 3.1% | PASS |
| file_writes | `oltp_update_non_index` | 153.04ms | 134.62ms | 0.9× | 2.1% | PASS |
| file_writes | `oltp_delete_insert` | 189.80ms | 155.28ms | 0.8× | 4.1% | PASS |
| file_writes | `oltp_write_only` | 139.19ms | 108.78ms | 0.8× | 24.3% | PASS |
| file_writes | `types_delete_insert` | 149.59ms | 86.62ms | 0.6× | 6.9% | PASS |
| file_writes | `oltp_read_write` | 182.18ms | 168.74ms | 0.9× | 2.8% | PASS |
| ac_reads | `oltp_point_select` | 50.11ms | 45.08ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 16.76ms | 12.57ms | 0.8× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 15.23ms | 12.55ms | 0.8× | 0.7% | PASS |
| ac_reads | `oltp_order_range` | 3.21ms | 2.79ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_distinct_range` | 3.93ms | 3.63ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 5.69ms | 6.68ms | 1.2× | 0.9% | PASS |
| ac_reads | `select_random_points` | 20.50ms | 18.07ms | 0.9× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 5.11ms | 5.92ms | 1.2× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 5.89ms | 5.56ms | 0.9× | 1.0% | PASS |
| ac_reads | `groupby_scan` | 28.84ms | 28.82ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 11.04ms | 8.82ms | 0.8× | 1.9% | PASS |
| ac_reads | `index_join_scan` | 3.66ms | 5.05ms | 1.4× | 2.0% | PASS |
| ac_reads | `types_table_scan` | 882.18ms | 961.03ms | 1.1× | 0.4% | PASS |
| ac_reads | `table_scan` | 996.76ms | 1.04s | 1.0× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 138.36ms | 127.01ms | 0.9× | 0.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.78ms | 48.47ms | 2.7× | 7.7% | PASS |
| ac_writes | `oltp_insert_ac` | 20.05ms | 58.76ms | 2.9× | 8.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.17ms | 67.45ms | 3.3× | 9.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.91ms | 54.46ms | 3.2× | 8.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 19.20ms | 60.88ms | 3.2× | 6.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.08ms | 58.08ms | 3.0× | 5.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.61ms | 61.08ms | 3.3× | 16.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.41ms | 65.65ms | 2.6× | 5.6% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.04ms | 38.44ms | 1.1× | 2.9% | PASS |
| mem_reads | `oltp_range_select` | 16.91ms | 14.23ms | 0.8× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 15.05ms | 13.69ms | 0.9× | 2.2% | PASS |
| mem_reads | `oltp_order_range` | 3.29ms | 3.25ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.26ms | 4.37ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 3.95ms | 6.14ms | 1.6× | 1.8% | PASS |
| mem_reads | `select_random_points` | 21.46ms | 20.65ms | 1.0× | 2.7% | PASS |
| mem_reads | `select_random_ranges` | 3.40ms | 5.28ms | 1.6× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.47ms | 1.1× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 34.48ms | 34.85ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 9.92ms | 9.10ms | 0.9× | 1.8% | PASS |
| mem_reads | `index_join_scan` | 3.70ms | 5.33ms | 1.4× | 2.0% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.19s | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.17s | 1.30s | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_read_only` | 138.48ms | 139.66ms | 1.0× | 1.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 239.12ms | 373.06ms | 1.6× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 18.17ms | 40.00ms | 2.2× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 63.27ms | 145.53ms | 2.3× | 1.6% | FAIL |
| mem_writes | `oltp_update_non_index` | 47.23ms | 88.03ms | 1.9× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 52.24ms | 107.13ms | 2.1× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 27.31ms | 65.02ms | 2.4× | 0.7% | FAIL |
| mem_writes | `types_delete_insert` | 37.66ms | 57.09ms | 1.5× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 96.52ms | 147.96ms | 1.5× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 105.22ms | 56.70ms | 0.5× | 1.5% | PASS |
| file_reads | `oltp_range_select` | 24.89ms | 16.41ms | 0.7× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 22.64ms | 15.78ms | 0.7× | 2.7% | PASS |
| file_reads | `oltp_order_range` | 4.30ms | 3.53ms | 0.8× | 2.3% | PASS |
| file_reads | `oltp_distinct_range` | 5.29ms | 4.69ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 11.35ms | 8.33ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 30.65ms | 23.60ms | 0.8× | 2.3% | PASS |
| file_reads | `select_random_ranges` | 10.47ms | 7.15ms | 0.7× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.52ms | 6.66ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 35.70ms | 35.26ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 14.76ms | 10.96ms | 0.7× | 2.7% | PASS |
| file_reads | `index_join_scan` | 4.77ms | 5.69ms | 1.2× | 2.3% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.20s | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 1.18s | 1.30s | 1.1× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 243.65ms | 166.53ms | 0.7× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 258.31ms | 387.76ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 25.63ms | 47.39ms | 1.8× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 97.42ms | 162.95ms | 1.7× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 92.71ms | 103.59ms | 1.1× | 10.9% | PASS |
| file_writes | `oltp_delete_insert` | 88.25ms | 122.43ms | 1.4× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 58.74ms | 77.21ms | 1.3× | 2.6% | PASS |
| file_writes | `types_delete_insert` | 64.40ms | 65.49ms | 1.0× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 129.91ms | 160.80ms | 1.2× | 1.8% | PASS |
| ac_reads | `oltp_point_select` | 57.94ms | 56.54ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 19.98ms | 16.43ms | 0.8× | 2.8% | PASS |
| ac_reads | `oltp_sum_range` | 18.06ms | 15.87ms | 0.9× | 2.5% | PASS |
| ac_reads | `oltp_order_range` | 3.82ms | 3.58ms | 0.9× | 2.4% | PASS |
| ac_reads | `oltp_distinct_range` | 4.77ms | 4.71ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.46ms | 8.32ms | 1.3× | 1.4% | PASS |
| ac_reads | `select_random_points` | 25.03ms | 23.60ms | 0.9× | 2.9% | PASS |
| ac_reads | `select_random_ranges` | 5.78ms | 7.13ms | 1.2× | 2.0% | PASS |
| ac_reads | `covering_index_scan` | 6.71ms | 6.67ms | 1.0× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 35.13ms | 35.42ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 12.00ms | 10.98ms | 0.9× | 2.0% | PASS |
| ac_reads | `index_join_scan` | 4.29ms | 5.76ms | 1.3× | 3.1% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.20s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.30s | 1.1× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 174.00ms | 166.78ms | 1.0× | 1.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.23ms | 60.21ms | 2.7× | 6.9% | PASS |
| ac_writes | `oltp_insert_ac` | 26.19ms | 77.07ms | 2.9× | 6.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.93ms | 85.35ms | 3.2× | 6.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.69ms | 69.05ms | 3.0× | 7.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.46ms | 78.92ms | 3.1× | 7.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.10ms | 77.62ms | 3.2× | 6.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.90ms | 72.05ms | 3.1× | 8.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.11ms | 82.33ms | 2.6× | 5.7% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.38ms | 41.53ms | 1.2× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 19.87ms | 21.79ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 17.64ms | 20.96ms | 1.2× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 3.65ms | 4.00ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.65ms | 5.16ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.49ms | 6.10ms | 1.4× | 1.3% | PASS |
| mem_reads | `select_random_points` | 25.73ms | 30.92ms | 1.2× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 7.66ms | 9.13ms | 1.2× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.20ms | 4.46ms | 1.1× | 2.2% | PASS |
| mem_reads | `groupby_scan` | 36.93ms | 40.70ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 7.94ms | 10.80ms | 1.4× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 3.96ms | 5.52ms | 1.4× | 1.7% | PASS |
| mem_reads | `types_table_scan` | 1.08s | 1.22s | 1.1× | 1.8% | PASS |
| mem_reads | `table_scan` | 1.33s | 1.32s | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_read_only` | 155.10ms | 177.27ms | 1.1× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 243.12ms | 361.73ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.55ms | 36.41ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 66.81ms | 134.34ms | 2.0× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 51.08ms | 89.83ms | 1.8× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 49.47ms | 101.45ms | 2.1× | 1.9% | PASS |
| mem_writes | `oltp_write_only` | 26.54ms | 62.47ms | 2.4× | 1.7% | FAIL |
| mem_writes | `types_delete_insert` | 32.71ms | 55.85ms | 1.7× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 105.58ms | 170.44ms | 1.6× | 2.7% | PASS |
| file_reads | `oltp_point_select` | 103.33ms | 59.04ms | 0.6× | 2.1% | PASS |
| file_reads | `oltp_range_select` | 27.56ms | 23.73ms | 0.9× | 2.3% | PASS |
| file_reads | `oltp_sum_range` | 25.59ms | 23.05ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 4.65ms | 4.38ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_distinct_range` | 5.78ms | 5.56ms | 1.0× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 11.79ms | 8.22ms | 0.7× | 2.3% | PASS |
| file_reads | `select_random_points` | 35.74ms | 34.27ms | 1.0× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 15.16ms | 11.23ms | 0.7× | 2.1% | PASS |
| file_reads | `covering_index_scan` | 11.26ms | 6.48ms | 0.6× | 2.0% | PASS |
| file_reads | `groupby_scan` | 38.32ms | 41.26ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 12.37ms | 12.53ms | 1.0× | 3.2% | PASS |
| file_reads | `index_join_scan` | 5.03ms | 5.94ms | 1.2× | 2.4% | PASS |
| file_reads | `types_table_scan` | 1.09s | 1.22s | 1.1× | 2.5% | PASS |
| file_reads | `table_scan` | 1.29s | 1.31s | 1.0× | 3.7% | PASS |
| file_reads | `oltp_read_only` | 260.87ms | 204.44ms | 0.8× | 1.6% | PASS |
| file_writes | `oltp_bulk_insert` | 257.77ms | 373.04ms | 1.4× | 1.6% | PASS |
| file_writes | `oltp_insert` | 25.82ms | 42.68ms | 1.7× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 96.84ms | 148.36ms | 1.5× | 2.3% | PASS |
| file_writes | `oltp_update_non_index` | 77.68ms | 103.17ms | 1.3× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 78.28ms | 115.25ms | 1.5× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 51.81ms | 73.41ms | 1.4× | 2.6% | PASS |
| file_writes | `types_delete_insert` | 49.84ms | 63.17ms | 1.3× | 2.3% | PASS |
| file_writes | `oltp_read_write` | 133.38ms | 180.67ms | 1.4× | 2.7% | PASS |
| ac_reads | `oltp_point_select` | 56.49ms | 58.82ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 22.93ms | 23.64ms | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_sum_range` | 20.82ms | 23.25ms | 1.1× | 2.4% | PASS |
| ac_reads | `oltp_order_range` | 4.19ms | 4.41ms | 1.1× | 2.2% | PASS |
| ac_reads | `oltp_distinct_range` | 5.21ms | 5.54ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_index_scan` | 7.26ms | 8.21ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_points` | 29.39ms | 34.00ms | 1.2× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 10.29ms | 11.11ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 6.91ms | 6.47ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 37.71ms | 41.22ms | 1.1× | 1.5% | PASS |
| ac_reads | `index_join` | 9.80ms | 12.40ms | 1.3× | 2.2% | PASS |
| ac_reads | `index_join_scan` | 4.51ms | 6.02ms | 1.3× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.22s | 1.1× | 1.7% | PASS |
| ac_reads | `table_scan` | 1.30s | 1.31s | 1.0× | 4.0% | PASS |
| ac_reads | `oltp_read_only` | 191.09ms | 204.07ms | 1.1× | 1.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.29ms | 60.79ms | 2.7× | 5.8% | PASS |
| ac_writes | `oltp_insert_ac` | 25.60ms | 79.76ms | 3.1× | 5.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.00ms | 85.70ms | 3.2× | 5.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.75ms | 69.04ms | 2.9× | 6.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.57ms | 78.03ms | 3.2× | 7.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.85ms | 77.20ms | 3.2× | 3.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.31ms | 68.34ms | 3.1× | 7.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.28ms | 87.10ms | 2.7× | 5.2% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 45s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 67.78ms | 130.00ms | 52.1% | 0.3% | PASS |
| `status_dirty_many_tables` | 70.91ms | 130.00ms | 54.5% | 0.5% | PASS |
| `diff_regular_working_one_table` | 64.82ms | 120.00ms | 54.0% | 1.0% | PASS |
| `diff_regular_working_many_tables` | 73.98ms | 140.00ms | 52.8% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 73.75ms | 140.00ms | 52.7% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 74.29ms | 140.00ms | 53.1% | 0.2% | PASS |
| `branch_list_many_branches` | 15.35ms | 35.00ms | 43.9% | 0.7% | PASS |
| `branch_create_delete` | 22.70ms | 40.00ms | 56.7% | 3.1% | PASS |
| `checkout_branch_clean` | 96.00ms | 150.00ms | 64.0% | 2.9% | PASS |
| `merge_data_no_conflicts` | 29.63ms | 50.00ms | 59.3% | 11.1% | PASS |
| `merge_schema_no_conflicts` | 15.00ms | 35.00ms | 42.9% | 1.1% | PASS |
| `merge_data_conflicts` | 22.60ms | 180.00ms | 12.6% | 0.8% | PASS |
| `merge_data_conflicts_with_resolve` | 22.62ms | 180.00ms | 12.6% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
