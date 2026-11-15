# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-15 17:49 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261111.196.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/43735767703)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.26s | 8.84s | 1.1× | 1.6% | **PASS** |
| Writes | 1.64s | 2.75s | 1.7× | 1.3% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.87s | 8.93s | 1.0× | 1.3% | **PASS** |
| Writes | 3.74s | 3.98s | 1.1× | 4.8% | **FAIL** |
| Autocommit writes | 710.93ms | 2.04s | 2.9× | 6.9% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.12s | 2.16s | 1.0× | 2.4% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.28s | 2.60s | 1.1× | 1.1% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 1.74s | 1.77s | 1.0× | 2.4% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.13s | 2.30s | 1.1× | 1.6% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 352.39ms | 574.14ms | 1.6× | 1.9% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 487.09ms | 847.58ms | 1.7× | 1.1% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 349.88ms | 601.20ms | 1.7× | 2.3% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 446.67ms | 731.67ms | 1.6× | 0.8% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.27s | 2.20s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.39s | 2.63s | 1.1× | 1.2% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 1.77s | 1.72s | 1.0× | 2.1% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.44s | 2.38s | 1.0× | 1.1% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 988.20ms | 908.35ms | 0.9× | 19.2% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 728.62ms | 941.07ms | 1.3× | 5.6% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 992.48ms | 1.01s | 1.0× | 4.0% | **FAIL** |
| File-backed | Writes | compositepk | 8 | 55 | 1.03s | 1.11s | 1.1× | 2.9% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.08s | 2.19s | 1.1× | 1.1% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.46s | 2.68s | 1.1× | 1.1% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 1.68s | 1.73s | 1.0× | 1.9% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.21s | 2.36s | 1.1× | 0.9% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 208.10ms | 644.00ms | 3.1× | 37.6% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 139.89ms | 442.92ms | 3.2× | 6.5% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 215.42ms | 482.75ms | 2.2× | 6.2% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 147.51ms | 468.16ms | 3.2× | 6.3% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 20.52ms | 22.43ms | 1.1× | 3.6% | PASS |
| mem_reads | `oltp_range_select` | 9.14ms | 8.86ms | 1.0× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 8.28ms | 8.65ms | 1.0× | 4.3% | PASS |
| mem_reads | `oltp_order_range` | 2.22ms | 2.29ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.02ms | 3.15ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.20ms | 4.17ms | 1.3× | 2.5% | PASS |
| mem_reads | `select_random_points` | 9.91ms | 9.09ms | 0.9× | 4.3% | PASS |
| mem_reads | `select_random_ranges` | 2.81ms | 3.18ms | 1.1× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 3.29ms | 3.35ms | 1.0× | 2.3% | PASS |
| mem_reads | `groupby_scan` | 24.99ms | 27.24ms | 1.1× | 1.1% | PASS |
| mem_reads | `index_join` | 4.61ms | 6.29ms | 1.4× | 3.1% | PASS |
| mem_reads | `index_join_scan` | 3.05ms | 3.95ms | 1.3× | 3.5% | PASS |
| mem_reads | `types_table_scan` | 877.97ms | 936.57ms | 1.1× | 1.1% | PASS |
| mem_reads | `table_scan` | 1.06s | 1.03s | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_read_only` | 85.20ms | 90.16ms | 1.1× | 2.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 135.81ms | 206.03ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 11.76ms | 21.79ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 43.20ms | 80.38ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 30.53ms | 48.70ms | 1.6× | 2.3% | PASS |
| mem_writes | `oltp_delete_insert` | 37.39ms | 60.85ms | 1.6× | 1.9% | PASS |
| mem_writes | `oltp_write_only` | 18.65ms | 39.43ms | 2.1× | 2.0% | PASS |
| mem_writes | `types_delete_insert` | 20.15ms | 30.71ms | 1.5× | 3.2% | PASS |
| mem_writes | `oltp_read_write` | 54.91ms | 86.23ms | 1.6× | 4.2% | PASS |
| file_reads | `oltp_point_select` | 88.95ms | 38.69ms | 0.4× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 16.59ms | 10.64ms | 0.6× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 15.29ms | 10.42ms | 0.7× | 2.5% | PASS |
| file_reads | `oltp_order_range` | 3.08ms | 2.53ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 3.82ms | 3.35ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 10.34ms | 5.99ms | 0.6× | 1.1% | PASS |
| file_reads | `select_random_points` | 16.90ms | 10.92ms | 0.6× | 4.2% | PASS |
| file_reads | `select_random_ranges` | 9.72ms | 4.91ms | 0.5× | 0.6% | PASS |
| file_reads | `covering_index_scan` | 10.75ms | 5.32ms | 0.5× | 1.6% | PASS |
| file_reads | `groupby_scan` | 25.87ms | 27.41ms | 1.1× | 1.1% | PASS |
| file_reads | `index_join` | 8.59ms | 7.65ms | 0.9× | 1.4% | PASS |
| file_reads | `index_join_scan` | 3.62ms | 4.13ms | 1.1× | 2.7% | PASS |
| file_reads | `types_table_scan` | 862.16ms | 927.01ms | 1.1× | 1.1% | PASS |
| file_reads | `table_scan` | 1.01s | 1.03s | 1.0× | 1.7% | PASS |
| file_reads | `oltp_read_only` | 183.95ms | 113.91ms | 0.6× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 190.75ms | 252.70ms | 1.3× | 10.6% | PASS |
| file_writes | `oltp_insert` | 28.33ms | 33.78ms | 1.2× | 21.6% | PASS |
| file_writes | `oltp_update_index` | 140.41ms | 136.73ms | 1.0× | 10.7% | PASS |
| file_writes | `oltp_update_non_index` | 124.78ms | 98.15ms | 0.8× | 16.8% | PASS |
| file_writes | `oltp_delete_insert` | 123.93ms | 112.50ms | 0.9× | 14.6% | PASS |
| file_writes | `oltp_write_only` | 117.45ms | 80.12ms | 0.7× | 29.4% | PASS |
| file_writes | `types_delete_insert` | 87.15ms | 64.30ms | 0.7× | 28.2% | PASS |
| file_writes | `oltp_read_write` | 175.40ms | 130.09ms | 0.7× | 28.8% | PASS |
| ac_reads | `oltp_point_select` | 40.05ms | 38.29ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 11.86ms | 10.63ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 10.63ms | 10.42ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 2.65ms | 2.54ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 3.36ms | 3.35ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 5.68ms | 5.93ms | 1.0× | 1.0% | PASS |
| ac_reads | `select_random_points` | 11.33ms | 10.90ms | 1.0× | 1.8% | PASS |
| ac_reads | `select_random_ranges` | 5.07ms | 4.88ms | 1.0× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 5.86ms | 5.23ms | 0.9× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 25.00ms | 27.30ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 6.17ms | 7.56ms | 1.2× | 1.2% | PASS |
| ac_reads | `index_join_scan` | 3.17ms | 4.20ms | 1.3× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 855.92ms | 923.81ms | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 976.69ms | 1.02s | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_read_only` | 117.32ms | 114.20ms | 1.0× | 2.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 30.66ms | 76.58ms | 2.5× | 74.3% | PASS |
| ac_writes | `oltp_insert_ac` | 30.85ms | 86.87ms | 2.8× | 38.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 23.57ms | 85.49ms | 3.6× | 29.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.57ms | 68.45ms | 3.0× | 31.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 20.58ms | 74.36ms | 3.6× | 19.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.27ms | 81.40ms | 3.0× | 36.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.26ms | 71.85ms | 3.0× | 63.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.35ms | 98.98ms | 3.5× | 47.9% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 30.70ms | 32.43ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 14.42ms | 12.05ms | 0.8× | 1.3% | PASS |
| mem_reads | `oltp_sum_range` | 13.83ms | 12.08ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_order_range` | 2.88ms | 2.82ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.74ms | 3.71ms | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.48ms | 5.26ms | 1.5× | 1.3% | PASS |
| mem_reads | `select_random_points` | 19.02ms | 19.16ms | 1.0× | 1.3% | PASS |
| mem_reads | `select_random_ranges` | 2.94ms | 4.43ms | 1.5× | 0.7% | PASS |
| mem_reads | `covering_index_scan` | 3.56ms | 3.75ms | 1.1× | 1.8% | PASS |
| mem_reads | `groupby_scan` | 31.66ms | 31.46ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 9.33ms | 7.88ms | 0.8× | 1.1% | PASS |
| mem_reads | `index_join_scan` | 3.08ms | 4.88ms | 1.6× | 1.1% | PASS |
| mem_reads | `types_table_scan` | 943.88ms | 1.12s | 1.2× | 1.1% | PASS |
| mem_reads | `table_scan` | 1.08s | 1.22s | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 118.03ms | 117.93ms | 1.0× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 193.71ms | 298.60ms | 1.5× | 1.3% | PASS |
| mem_writes | `oltp_insert` | 15.19ms | 33.55ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 54.98ms | 124.11ms | 2.3× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 39.33ms | 72.93ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 45.41ms | 92.05ms | 2.0× | 0.8% | PASS |
| mem_writes | `oltp_write_only` | 23.39ms | 54.08ms | 2.3× | 1.0% | FAIL |
| mem_writes | `types_delete_insert` | 32.50ms | 47.03ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 82.59ms | 125.23ms | 1.5× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 59.41ms | 40.04ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 17.30ms | 12.96ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 16.91ms | 13.16ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 3.27ms | 2.99ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.07ms | 3.88ms | 1.0× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 6.37ms | 6.27ms | 1.0× | 1.2% | PASS |
| file_reads | `select_random_points` | 22.98ms | 20.62ms | 0.9× | 1.3% | PASS |
| file_reads | `select_random_ranges` | 5.84ms | 5.26ms | 0.9× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 6.50ms | 4.80ms | 0.7× | 1.3% | PASS |
| file_reads | `groupby_scan` | 32.14ms | 31.82ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 11.04ms | 9.00ms | 0.8× | 1.7% | PASS |
| file_reads | `index_join_scan` | 3.57ms | 5.16ms | 1.4× | 1.3% | PASS |
| file_reads | `types_table_scan` | 939.82ms | 1.12s | 1.2× | 0.7% | PASS |
| file_reads | `table_scan` | 1.09s | 1.22s | 1.1× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 164.68ms | 132.56ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 213.90ms | 313.94ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_insert` | 19.94ms | 39.56ms | 2.0× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 110.64ms | 143.13ms | 1.3× | 9.5% | PASS |
| file_writes | `oltp_update_non_index` | 73.77ms | 88.57ms | 1.2× | 21.5% | PASS |
| file_writes | `oltp_delete_insert` | 69.54ms | 105.12ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 67.14ms | 63.68ms | 0.9× | 10.2% | PASS |
| file_writes | `types_delete_insert` | 50.91ms | 54.20ms | 1.1× | 1.2% | PASS |
| file_writes | `oltp_read_write` | 122.78ms | 132.87ms | 1.1× | 9.9% | PASS |
| ac_reads | `oltp_point_select` | 39.03ms | 39.78ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 15.26ms | 12.98ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 14.90ms | 13.12ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_order_range` | 3.10ms | 2.98ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_distinct_range` | 3.92ms | 3.88ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 4.72ms | 6.35ms | 1.3× | 1.8% | PASS |
| ac_reads | `select_random_points` | 21.38ms | 20.76ms | 1.0× | 1.9% | PASS |
| ac_reads | `select_random_ranges` | 4.06ms | 5.25ms | 1.3× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 4.73ms | 4.82ms | 1.0× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 32.02ms | 31.93ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 10.40ms | 9.07ms | 0.9× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 3.40ms | 5.18ms | 1.5× | 1.3% | PASS |
| ac_reads | `types_table_scan` | 954.82ms | 1.13s | 1.2× | 1.0% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.26s | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_read_only` | 139.51ms | 135.24ms | 1.0× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 16.62ms | 44.41ms | 2.7× | 6.5% | PASS |
| ac_writes | `oltp_insert_ac` | 17.89ms | 54.27ms | 3.0× | 6.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 18.66ms | 65.94ms | 3.5× | 7.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 15.41ms | 50.20ms | 3.3× | 5.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 16.97ms | 57.25ms | 3.4× | 4.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 17.07ms | 56.61ms | 3.3× | 6.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 15.76ms | 52.44ms | 3.3× | 8.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 21.53ms | 61.79ms | 2.9× | 5.3% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.89ms | 23.05ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 10.20ms | 8.34ms | 0.8× | 3.6% | PASS |
| mem_reads | `oltp_sum_range` | 9.76ms | 8.44ms | 0.9× | 2.9% | PASS |
| mem_reads | `oltp_order_range` | 2.16ms | 1.99ms | 0.9× | 2.0% | PASS |
| mem_reads | `oltp_distinct_range` | 2.72ms | 2.52ms | 0.9× | 1.6% | PASS |
| mem_reads | `oltp_index_scan` | 2.61ms | 4.28ms | 1.6× | 2.3% | PASS |
| mem_reads | `select_random_points` | 14.96ms | 13.79ms | 0.9× | 4.6% | PASS |
| mem_reads | `select_random_ranges` | 2.18ms | 3.34ms | 1.5× | 4.5% | PASS |
| mem_reads | `covering_index_scan` | 2.35ms | 2.76ms | 1.2× | 4.4% | PASS |
| mem_reads | `groupby_scan` | 20.63ms | 20.32ms | 1.0× | 1.5% | PASS |
| mem_reads | `index_join` | 7.27ms | 5.28ms | 0.7× | 1.9% | PASS |
| mem_reads | `index_join_scan` | 2.60ms | 4.25ms | 1.6× | 7.0% | PASS |
| mem_reads | `types_table_scan` | 682.27ms | 725.05ms | 1.1× | 1.3% | PASS |
| mem_reads | `table_scan` | 865.93ms | 873.44ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_read_only` | 87.94ms | 77.64ms | 0.9× | 3.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 133.19ms | 203.99ms | 1.5× | 1.3% | PASS |
| mem_writes | `oltp_insert` | 10.34ms | 23.32ms | 2.3× | 1.8% | PASS |
| mem_writes | `oltp_update_index` | 40.75ms | 88.06ms | 2.2× | 2.9% | PASS |
| mem_writes | `oltp_update_non_index` | 30.90ms | 53.16ms | 1.7× | 2.4% | PASS |
| mem_writes | `oltp_delete_insert` | 33.29ms | 67.71ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 18.40ms | 42.62ms | 2.3× | 2.4% | FAIL |
| mem_writes | `types_delete_insert` | 24.88ms | 35.85ms | 1.4× | 2.2% | PASS |
| mem_writes | `oltp_read_write` | 58.13ms | 86.50ms | 1.5× | 2.8% | PASS |
| file_reads | `oltp_point_select` | 75.87ms | 36.27ms | 0.5× | 2.3% | PASS |
| file_reads | `oltp_range_select` | 15.37ms | 9.72ms | 0.6× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 15.38ms | 9.85ms | 0.6× | 2.7% | PASS |
| file_reads | `oltp_order_range` | 2.74ms | 2.15ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 3.15ms | 2.60ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 7.87ms | 5.28ms | 0.7× | 3.3% | PASS |
| file_reads | `select_random_points` | 19.10ms | 13.79ms | 0.7× | 3.3% | PASS |
| file_reads | `select_random_ranges` | 7.37ms | 4.66ms | 0.6× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 7.87ms | 4.18ms | 0.5× | 2.4% | PASS |
| file_reads | `groupby_scan` | 22.32ms | 20.82ms | 0.9× | 2.1% | PASS |
| file_reads | `index_join` | 11.07ms | 6.97ms | 0.6× | 3.9% | PASS |
| file_reads | `index_join_scan` | 3.36ms | 4.34ms | 1.3× | 2.9% | PASS |
| file_reads | `types_table_scan` | 671.25ms | 710.51ms | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 761.98ms | 803.68ms | 1.1× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 146.55ms | 88.72ms | 0.6× | 1.5% | PASS |
| file_writes | `oltp_bulk_insert` | 200.00ms | 269.90ms | 1.3× | 2.3% | PASS |
| file_writes | `oltp_insert` | 19.05ms | 47.90ms | 2.5× | 1.7% | FAIL |
| file_writes | `oltp_update_index` | 141.16ms | 169.40ms | 1.2× | 0.5% | PASS |
| file_writes | `oltp_update_non_index` | 123.81ms | 113.52ms | 0.9× | 5.2% | PASS |
| file_writes | `oltp_delete_insert` | 151.07ms | 124.42ms | 0.8× | 3.5% | PASS |
| file_writes | `oltp_write_only` | 110.68ms | 89.00ms | 0.8× | 4.4% | PASS |
| file_writes | `types_delete_insert` | 104.90ms | 69.44ms | 0.7× | 6.2% | PASS |
| file_writes | `oltp_read_write` | 141.81ms | 129.01ms | 0.9× | 6.5% | PASS |
| ac_reads | `oltp_point_select` | 39.40ms | 35.67ms | 0.9× | 2.7% | PASS |
| ac_reads | `oltp_range_select` | 12.19ms | 9.63ms | 0.8× | 4.0% | PASS |
| ac_reads | `oltp_sum_range` | 11.75ms | 9.76ms | 0.8× | 4.1% | PASS |
| ac_reads | `oltp_order_range` | 2.50ms | 2.22ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 2.98ms | 2.71ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 4.65ms | 5.62ms | 1.2× | 1.8% | PASS |
| ac_reads | `select_random_points` | 17.07ms | 14.84ms | 0.9× | 4.5% | PASS |
| ac_reads | `select_random_ranges` | 4.21ms | 4.97ms | 1.2× | 1.9% | PASS |
| ac_reads | `covering_index_scan` | 4.37ms | 4.50ms | 1.0× | 2.2% | PASS |
| ac_reads | `groupby_scan` | 22.22ms | 21.27ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 9.10ms | 6.95ms | 0.8× | 4.2% | PASS |
| ac_reads | `index_join_scan` | 3.07ms | 4.36ms | 1.4× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 684.15ms | 722.22ms | 1.1× | 1.3% | PASS |
| ac_reads | `table_scan` | 762.09ms | 801.09ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 98.31ms | 89.15ms | 0.9× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 26.28ms | 49.69ms | 1.9× | 5.7% | PASS |
| ac_writes | `oltp_insert_ac` | 28.82ms | 62.20ms | 2.2× | 7.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.79ms | 68.11ms | 2.4× | 4.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.27ms | 55.94ms | 2.3× | 6.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.00ms | 61.88ms | 2.2× | 3.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.60ms | 62.25ms | 2.3× | 4.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.80ms | 56.18ms | 2.7× | 8.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 30.85ms | 66.51ms | 2.2× | 8.8% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.27ms | 29.62ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 16.69ms | 16.43ms | 1.0× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 13.93ms | 16.13ms | 1.2× | 2.9% | PASS |
| mem_reads | `oltp_order_range` | 2.88ms | 3.05ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.69ms | 3.95ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.62ms | 4.63ms | 1.3× | 2.5% | PASS |
| mem_reads | `select_random_points` | 20.23ms | 23.19ms | 1.1× | 2.0% | PASS |
| mem_reads | `select_random_ranges` | 5.83ms | 6.51ms | 1.1× | 2.1% | PASS |
| mem_reads | `covering_index_scan` | 3.28ms | 3.20ms | 1.0× | 1.3% | PASS |
| mem_reads | `groupby_scan` | 28.88ms | 32.39ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 6.18ms | 7.90ms | 1.3× | 1.2% | PASS |
| mem_reads | `index_join_scan` | 3.26ms | 4.89ms | 1.5× | 2.5% | PASS |
| mem_reads | `types_table_scan` | 861.11ms | 964.64ms | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.02s | 1.05s | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_read_only` | 112.32ms | 128.16ms | 1.1× | 0.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.20ms | 259.97ms | 1.4× | 0.6% | PASS |
| mem_writes | `oltp_insert` | 14.44ms | 26.84ms | 1.9× | 0.5% | PASS |
| mem_writes | `oltp_update_index` | 51.52ms | 98.28ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 39.28ms | 64.78ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 38.13ms | 74.32ms | 1.9× | 0.5% | PASS |
| mem_writes | `oltp_write_only` | 20.95ms | 46.56ms | 2.2× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 24.73ms | 40.21ms | 1.6× | 0.8% | PASS |
| mem_writes | `oltp_read_write` | 73.41ms | 120.71ms | 1.6× | 0.7% | PASS |
| file_reads | `oltp_point_select` | 93.64ms | 46.17ms | 0.5× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 23.07ms | 18.10ms | 0.8× | 0.8% | PASS |
| file_reads | `oltp_sum_range` | 20.87ms | 17.88ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_order_range` | 3.75ms | 3.32ms | 0.9× | 0.7% | PASS |
| file_reads | `oltp_distinct_range` | 4.56ms | 4.20ms | 0.9× | 0.8% | PASS |
| file_reads | `oltp_index_scan` | 10.84ms | 6.63ms | 0.6× | 1.1% | PASS |
| file_reads | `select_random_points` | 28.07ms | 25.33ms | 0.9× | 1.4% | PASS |
| file_reads | `select_random_ranges` | 13.07ms | 8.52ms | 0.7× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 10.73ms | 5.48ms | 0.5× | 1.0% | PASS |
| file_reads | `groupby_scan` | 30.45ms | 32.97ms | 1.1× | 1.2% | PASS |
| file_reads | `index_join` | 10.16ms | 9.62ms | 0.9× | 1.4% | PASS |
| file_reads | `index_join_scan` | 4.12ms | 4.97ms | 1.2× | 2.1% | PASS |
| file_reads | `types_table_scan` | 931.31ms | 988.77ms | 1.1× | 2.3% | PASS |
| file_reads | `table_scan` | 1.04s | 1.05s | 1.0× | 3.0% | PASS |
| file_reads | `oltp_read_only` | 215.89ms | 153.15ms | 0.7× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 234.55ms | 319.19ms | 1.4× | 3.0% | PASS |
| file_writes | `oltp_insert` | 33.58ms | 45.53ms | 1.4× | 8.0% | PASS |
| file_writes | `oltp_update_index` | 165.10ms | 167.46ms | 1.0× | 3.0% | PASS |
| file_writes | `oltp_update_non_index` | 135.58ms | 117.38ms | 0.9× | 2.1% | PASS |
| file_writes | `oltp_delete_insert` | 132.85ms | 131.17ms | 1.0× | 2.9% | PASS |
| file_writes | `oltp_write_only` | 96.65ms | 93.32ms | 1.0× | 2.9% | PASS |
| file_writes | `types_delete_insert` | 86.36ms | 73.29ms | 0.8× | 2.7% | PASS |
| file_writes | `oltp_read_write` | 149.55ms | 167.32ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 46.63ms | 46.08ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 18.17ms | 18.10ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_sum_range` | 16.18ms | 17.96ms | 1.1× | 0.6% | PASS |
| ac_reads | `oltp_order_range` | 3.36ms | 3.34ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 4.14ms | 4.24ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.21ms | 6.63ms | 1.1× | 1.1% | PASS |
| ac_reads | `select_random_points` | 22.77ms | 25.34ms | 1.1× | 0.9% | PASS |
| ac_reads | `select_random_ranges` | 8.22ms | 8.49ms | 1.0× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 5.92ms | 5.43ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 29.74ms | 32.83ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 7.96ms | 9.80ms | 1.2× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 3.79ms | 5.02ms | 1.3× | 2.8% | PASS |
| ac_reads | `types_table_scan` | 884.08ms | 972.12ms | 1.1× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.00s | 1.05s | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 145.21ms | 153.05ms | 1.1× | 0.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 16.41ms | 45.81ms | 2.8× | 7.2% | PASS |
| ac_writes | `oltp_insert_ac` | 18.99ms | 61.70ms | 3.2× | 10.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 19.93ms | 69.47ms | 3.5× | 6.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.28ms | 52.37ms | 3.2× | 4.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 18.13ms | 59.08ms | 3.3× | 5.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 18.50ms | 60.39ms | 3.3× | 6.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 15.87ms | 54.23ms | 3.4× | 8.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 23.41ms | 65.10ms | 2.8× | 4.1% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 52s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 71.03ms | 130.00ms | 54.6% | 0.6% | PASS |
| `status_dirty_many_tables` | 74.15ms | 130.00ms | 57.0% | 0.6% | PASS |
| `diff_regular_working_one_table` | 65.79ms | 120.00ms | 54.8% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 77.65ms | 140.00ms | 55.5% | 0.5% | PASS |
| `diff_stat_working_many_tables` | 77.36ms | 140.00ms | 55.3% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 78.01ms | 140.00ms | 55.7% | 0.5% | PASS |
| `branch_list_many_branches` | 17.31ms | 35.00ms | 49.5% | 1.0% | PASS |
| `branch_create_delete` | 20.17ms | 40.00ms | 50.4% | 2.0% | PASS |
| `checkout_branch_clean` | 106.41ms | 150.00ms | 70.9% | 6.7% | PASS |
| `merge_data_no_conflicts` | 31.93ms | 50.00ms | 63.9% | 1.3% | PASS |
| `merge_schema_no_conflicts` | 17.49ms | 35.00ms | 50.0% | 1.7% | PASS |
| `merge_data_conflicts` | 24.13ms | 180.00ms | 13.4% | 1.3% | PASS |
| `merge_data_conflicts_with_resolve` | 24.48ms | 180.00ms | 13.6% | 1.5% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
