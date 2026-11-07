# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-07 17:25 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261101.186.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/42388233759)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.25s | 10.86s | 1.1× | 1.1% | **PASS** |
| Writes | 2.01s | 3.48s | 1.7× | 1.1% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.82s | 10.97s | 1.0× | 1.1% | **PASS** |
| Writes | 3.74s | 4.51s | 1.2× | 1.6% | **PASS** |
| Autocommit writes | 893.87ms | 2.61s | 2.9× | 5.2% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.50s | 2.56s | 1.0× | 1.0% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.83s | 2.88s | 1.0× | 2.2% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.38s | 2.56s | 1.1× | 0.9% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.55s | 2.86s | 1.1× | 1.3% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 361.53ms | 619.49ms | 1.7× | 0.9% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 596.22ms | 1.06s | 1.8× | 1.5% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 465.22ms | 807.60ms | 1.7× | 1.1% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 582.60ms | 989.14ms | 1.7× | 1.2% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 2.48s | 2.53s | 1.0× | 1.0% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.05s | 2.91s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.50s | 2.60s | 1.0× | 0.9% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.78s | 2.93s | 1.1× | 1.3% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 859.23ms | 956.74ms | 1.1× | 2.2% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 964.51ms | 1.18s | 1.2× | 6.2% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.16s | 1.30s | 1.1× | 1.8% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 754.64ms | 1.08s | 1.4× | 1.3% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.43s | 2.54s | 1.0× | 1.0% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.69s | 2.87s | 1.1× | 2.2% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.43s | 2.60s | 1.1× | 1.1% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.77s | 2.96s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 253.74ms | 705.73ms | 2.8× | 3.7% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 202.13ms | 612.44ms | 3.0× | 7.9% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 245.75ms | 708.21ms | 2.9× | 4.2% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 192.26ms | 584.45ms | 3.0× | 5.6% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 21.56ms | 24.28ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_range_select` | 9.70ms | 10.41ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 8.69ms | 9.76ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_order_range` | 2.50ms | 2.66ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.36ms | 3.65ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.51ms | 4.33ms | 1.2× | 1.5% | PASS |
| mem_reads | `select_random_points` | 10.01ms | 10.55ms | 1.1× | 1.2% | PASS |
| mem_reads | `select_random_ranges` | 2.80ms | 3.38ms | 1.2× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 3.42ms | 3.54ms | 1.0× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 30.68ms | 32.41ms | 1.1× | 0.4% | PASS |
| mem_reads | `index_join` | 5.16ms | 7.41ms | 1.4× | 1.2% | PASS |
| mem_reads | `index_join_scan` | 2.86ms | 4.19ms | 1.5× | 1.0% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.11s | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.26s | 1.23s | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 95.94ms | 105.33ms | 1.1× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 139.30ms | 216.59ms | 1.6× | 0.4% | PASS |
| mem_writes | `oltp_insert` | 12.59ms | 23.29ms | 1.8× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 45.24ms | 89.45ms | 2.0× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 29.15ms | 52.89ms | 1.8× | 0.8% | PASS |
| mem_writes | `oltp_delete_insert` | 38.91ms | 64.80ms | 1.7× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 18.67ms | 41.23ms | 2.2× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 20.53ms | 33.26ms | 1.6× | 0.8% | PASS |
| mem_writes | `oltp_read_write` | 57.15ms | 97.97ms | 1.7× | 1.0% | PASS |
| file_reads | `oltp_point_select` | 50.50ms | 30.30ms | 0.6× | 0.5% | PASS |
| file_reads | `oltp_range_select` | 13.13ms | 11.17ms | 0.9× | 0.8% | PASS |
| file_reads | `oltp_sum_range` | 12.21ms | 10.64ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 2.94ms | 2.84ms | 1.0× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 3.83ms | 3.80ms | 1.0× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 6.75ms | 5.33ms | 0.8× | 1.7% | PASS |
| file_reads | `select_random_points` | 13.71ms | 11.57ms | 0.8× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 5.92ms | 4.06ms | 0.7× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 6.64ms | 4.46ms | 0.7× | 1.2% | PASS |
| file_reads | `groupby_scan` | 31.05ms | 32.47ms | 1.0× | 0.5% | PASS |
| file_reads | `index_join` | 7.12ms | 8.06ms | 1.1× | 1.2% | PASS |
| file_reads | `index_join_scan` | 3.31ms | 4.40ms | 1.3× | 1.3% | PASS |
| file_reads | `types_table_scan` | 1.02s | 1.09s | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 1.17s | 1.20s | 1.0× | 0.7% | PASS |
| file_reads | `oltp_read_only` | 137.71ms | 114.00ms | 0.8× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 180.74ms | 264.33ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_insert` | 20.11ms | 28.74ms | 1.4× | 2.0% | PASS |
| file_writes | `oltp_update_index` | 141.48ms | 150.78ms | 1.1× | 1.3% | PASS |
| file_writes | `oltp_update_non_index` | 111.66ms | 99.35ms | 0.9× | 2.3% | PASS |
| file_writes | `oltp_delete_insert` | 122.97ms | 116.25ms | 0.9× | 0.9% | PASS |
| file_writes | `oltp_write_only` | 89.85ms | 88.38ms | 1.0× | 9.5% | PASS |
| file_writes | `types_delete_insert` | 69.93ms | 65.13ms | 0.9× | 2.4% | PASS |
| file_writes | `oltp_read_write` | 122.50ms | 143.78ms | 1.2× | 2.5% | PASS |
| ac_reads | `oltp_point_select` | 30.51ms | 30.29ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 10.91ms | 11.14ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_sum_range` | 10.28ms | 10.66ms | 1.0× | 0.6% | PASS |
| ac_reads | `oltp_order_range` | 2.72ms | 2.82ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 3.62ms | 3.80ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 4.85ms | 5.32ms | 1.1× | 1.3% | PASS |
| ac_reads | `select_random_points` | 11.67ms | 11.46ms | 1.0× | 1.5% | PASS |
| ac_reads | `select_random_ranges` | 4.01ms | 4.06ms | 1.0× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 4.60ms | 4.46ms | 1.0× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 30.86ms | 32.49ms | 1.1× | 0.3% | PASS |
| ac_reads | `index_join` | 6.15ms | 8.06ms | 1.3× | 0.9% | PASS |
| ac_reads | `index_join_scan` | 3.14ms | 4.39ms | 1.4× | 1.2% | PASS |
| ac_reads | `types_table_scan` | 1.01s | 1.09s | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 1.19s | 1.20s | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 109.53ms | 114.68ms | 1.0× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 29.44ms | 76.23ms | 2.6× | 3.6% | PASS |
| ac_writes | `oltp_insert_ac` | 31.59ms | 88.54ms | 2.8× | 3.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 34.14ms | 97.03ms | 2.8× | 3.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 29.52ms | 82.68ms | 2.8× | 4.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 30.94ms | 91.46ms | 3.0× | 3.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 31.27ms | 88.73ms | 2.8× | 3.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 32.08ms | 84.72ms | 2.6× | 7.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.75ms | 96.35ms | 2.8× | 4.7% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.02ms | 38.52ms | 1.1× | 2.5% | PASS |
| mem_reads | `oltp_range_select` | 17.35ms | 14.30ms | 0.8× | 3.0% | PASS |
| mem_reads | `oltp_sum_range` | 15.79ms | 13.79ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 3.42ms | 3.26ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.46ms | 4.38ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.01ms | 6.33ms | 1.6× | 1.6% | PASS |
| mem_reads | `select_random_points` | 23.43ms | 21.06ms | 0.9× | 4.3% | PASS |
| mem_reads | `select_random_ranges` | 3.64ms | 5.35ms | 1.5× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 4.21ms | 4.80ms | 1.1× | 2.2% | PASS |
| mem_reads | `groupby_scan` | 35.39ms | 35.23ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 10.99ms | 9.67ms | 0.9× | 2.8% | PASS |
| mem_reads | `index_join_scan` | 3.89ms | 5.54ms | 1.4× | 2.6% | PASS |
| mem_reads | `types_table_scan` | 1.12s | 1.22s | 1.1× | 2.6% | PASS |
| mem_reads | `table_scan` | 1.38s | 1.35s | 1.0× | 3.3% | PASS |
| mem_reads | `oltp_read_only` | 158.14ms | 146.75ms | 0.9× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 236.88ms | 385.77ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 17.70ms | 40.44ms | 2.3× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 65.79ms | 149.83ms | 2.3× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 49.52ms | 93.43ms | 1.9× | 2.4% | PASS |
| mem_writes | `oltp_delete_insert` | 58.54ms | 116.27ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 29.54ms | 68.40ms | 2.3× | 2.4% | FAIL |
| mem_writes | `types_delete_insert` | 39.07ms | 58.74ms | 1.5× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 99.20ms | 150.85ms | 1.5× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 107.38ms | 56.90ms | 0.5× | 1.7% | PASS |
| file_reads | `oltp_range_select` | 26.65ms | 16.58ms | 0.6× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 24.58ms | 16.16ms | 0.7× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 4.53ms | 3.56ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 5.62ms | 4.70ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 11.47ms | 8.37ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 30.97ms | 23.86ms | 0.8× | 3.1% | PASS |
| file_reads | `select_random_ranges` | 10.66ms | 7.16ms | 0.7× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 11.64ms | 6.72ms | 0.6× | 1.9% | PASS |
| file_reads | `groupby_scan` | 36.33ms | 35.58ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 15.64ms | 11.06ms | 0.7× | 2.8% | PASS |
| file_reads | `index_join_scan` | 4.98ms | 5.88ms | 1.2× | 2.8% | PASS |
| file_reads | `types_table_scan` | 1.15s | 1.22s | 1.1× | 1.4% | PASS |
| file_reads | `table_scan` | 1.35s | 1.32s | 1.0× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 256.41ms | 170.37ms | 0.7× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 263.95ms | 398.39ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_insert` | 25.68ms | 48.05ms | 1.9× | 2.8% | PASS |
| file_writes | `oltp_update_index` | 135.33ms | 171.98ms | 1.3× | 10.4% | PASS |
| file_writes | `oltp_update_non_index` | 104.46ms | 110.22ms | 1.1× | 13.3% | PASS |
| file_writes | `oltp_delete_insert` | 100.94ms | 128.99ms | 1.3× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 87.44ms | 81.81ms | 0.9× | 11.7% | PASS |
| file_writes | `types_delete_insert` | 76.48ms | 69.40ms | 0.9× | 1.8% | PASS |
| file_writes | `oltp_read_write` | 170.24ms | 171.14ms | 1.0× | 9.6% | PASS |
| ac_reads | `oltp_point_select` | 60.48ms | 56.93ms | 0.9× | 2.6% | PASS |
| ac_reads | `oltp_range_select` | 19.87ms | 16.32ms | 0.8× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 18.39ms | 15.95ms | 0.9× | 2.7% | PASS |
| ac_reads | `oltp_order_range` | 4.01ms | 3.60ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_distinct_range` | 4.89ms | 4.69ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_index_scan` | 6.36ms | 8.29ms | 1.3× | 2.2% | PASS |
| ac_reads | `select_random_points` | 25.96ms | 23.85ms | 0.9× | 3.7% | PASS |
| ac_reads | `select_random_ranges` | 5.97ms | 7.13ms | 1.2× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 6.93ms | 6.70ms | 1.0× | 2.2% | PASS |
| ac_reads | `groupby_scan` | 35.78ms | 35.55ms | 1.0× | 1.1% | PASS |
| ac_reads | `index_join` | 12.49ms | 11.01ms | 0.9× | 3.1% | PASS |
| ac_reads | `index_join_scan` | 4.30ms | 5.89ms | 1.4× | 2.2% | PASS |
| ac_reads | `types_table_scan` | 1.09s | 1.21s | 1.1× | 2.3% | PASS |
| ac_reads | `table_scan` | 1.23s | 1.30s | 1.1× | 2.2% | PASS |
| ac_reads | `oltp_read_only` | 174.37ms | 166.58ms | 1.0× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.97ms | 60.90ms | 2.7× | 6.9% | PASS |
| ac_writes | `oltp_insert_ac` | 26.50ms | 75.10ms | 2.8× | 7.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.85ms | 88.30ms | 3.4× | 7.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.10ms | 72.55ms | 3.1× | 7.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.08ms | 78.75ms | 3.0× | 9.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.95ms | 77.14ms | 3.2× | 9.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.50ms | 75.31ms | 3.3× | 11.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.18ms | 84.39ms | 2.7× | 8.0% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 29.71ms | 29.65ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_range_select` | 13.04ms | 11.60ms | 0.9× | 0.8% | PASS |
| mem_reads | `oltp_sum_range` | 12.56ms | 11.12ms | 0.9× | 1.0% | PASS |
| mem_reads | `oltp_order_range` | 2.84ms | 2.77ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.71ms | 3.74ms | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.42ms | 4.83ms | 1.4× | 1.0% | PASS |
| mem_reads | `select_random_points` | 19.47ms | 18.09ms | 0.9× | 0.9% | PASS |
| mem_reads | `select_random_ranges` | 2.78ms | 4.29ms | 1.5× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 3.32ms | 3.60ms | 1.1× | 1.3% | PASS |
| mem_reads | `groupby_scan` | 32.08ms | 32.05ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 9.99ms | 7.94ms | 0.8× | 1.0% | PASS |
| mem_reads | `index_join_scan` | 3.06ms | 4.95ms | 1.6× | 1.1% | PASS |
| mem_reads | `types_table_scan` | 984.52ms | 1.10s | 1.1× | 0.2% | PASS |
| mem_reads | `table_scan` | 1.14s | 1.21s | 1.1× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 115.88ms | 114.83ms | 1.0× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 181.93ms | 277.81ms | 1.5× | 0.5% | PASS |
| mem_writes | `oltp_insert` | 15.09ms | 32.28ms | 2.1× | 1.4% | PASS |
| mem_writes | `oltp_update_index` | 54.70ms | 122.55ms | 2.2× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 38.97ms | 71.21ms | 1.8× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 43.79ms | 87.04ms | 2.0× | 0.8% | PASS |
| mem_writes | `oltp_write_only` | 23.02ms | 52.96ms | 2.3× | 1.1% | FAIL |
| mem_writes | `types_delete_insert` | 31.08ms | 45.12ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_read_write` | 76.65ms | 118.62ms | 1.5× | 0.8% | PASS |
| file_reads | `oltp_point_select` | 58.91ms | 36.36ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 16.55ms | 12.66ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_sum_range` | 15.90ms | 12.27ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_order_range` | 3.29ms | 2.99ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 4.20ms | 3.94ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 6.67ms | 6.03ms | 0.9× | 0.8% | PASS |
| file_reads | `select_random_points` | 23.75ms | 20.05ms | 0.8× | 0.8% | PASS |
| file_reads | `select_random_ranges` | 5.84ms | 5.22ms | 0.9× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 6.41ms | 4.68ms | 0.7× | 0.9% | PASS |
| file_reads | `groupby_scan` | 32.95ms | 32.54ms | 1.0× | 0.5% | PASS |
| file_reads | `index_join` | 12.28ms | 9.37ms | 0.8× | 1.0% | PASS |
| file_reads | `index_join_scan` | 3.59ms | 5.40ms | 1.5× | 1.4% | PASS |
| file_reads | `types_table_scan` | 1.00s | 1.11s | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 1.16s | 1.21s | 1.0× | 0.7% | PASS |
| file_reads | `oltp_read_only` | 155.91ms | 123.34ms | 0.8× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 254.73ms | 350.24ms | 1.4× | 2.3% | PASS |
| file_writes | `oltp_insert` | 29.83ms | 58.31ms | 2.0× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 163.88ms | 208.39ms | 1.3× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 145.81ms | 133.53ms | 0.9× | 1.3% | PASS |
| file_writes | `oltp_delete_insert` | 162.56ms | 159.97ms | 1.0× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 111.36ms | 113.24ms | 1.0× | 6.4% | PASS |
| file_writes | `types_delete_insert` | 118.30ms | 92.64ms | 0.8× | 2.4% | PASS |
| file_writes | `oltp_read_write` | 177.06ms | 181.70ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 40.32ms | 36.99ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 14.79ms | 12.89ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 14.26ms | 12.45ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.10ms | 2.99ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_distinct_range` | 3.97ms | 3.92ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 4.71ms | 5.89ms | 1.3× | 1.5% | PASS |
| ac_reads | `select_random_points` | 21.58ms | 19.79ms | 0.9× | 0.9% | PASS |
| ac_reads | `select_random_ranges` | 3.94ms | 5.22ms | 1.3× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 4.48ms | 4.70ms | 1.0× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 32.79ms | 32.61ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 11.34ms | 9.29ms | 0.8× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 3.44ms | 5.36ms | 1.6× | 1.3% | PASS |
| ac_reads | `types_table_scan` | 995.54ms | 1.11s | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.15s | 1.21s | 1.1× | 0.6% | PASS |
| ac_reads | `oltp_read_only` | 129.24ms | 123.25ms | 1.0× | 0.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 29.24ms | 73.02ms | 2.5× | 4.0% | PASS |
| ac_writes | `oltp_insert_ac` | 31.04ms | 87.13ms | 2.8× | 2.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 31.57ms | 100.98ms | 3.2× | 4.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 28.15ms | 81.54ms | 2.9× | 4.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 30.91ms | 91.02ms | 2.9× | 5.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 30.38ms | 91.22ms | 3.0× | 4.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 29.55ms | 87.05ms | 2.9× | 7.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.91ms | 96.27ms | 2.8× | 4.1% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.04ms | 39.94ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 19.40ms | 21.49ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_sum_range` | 17.29ms | 20.77ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 3.58ms | 3.97ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 4.57ms | 5.08ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.44ms | 5.87ms | 1.3× | 1.3% | PASS |
| mem_reads | `select_random_points` | 25.19ms | 30.39ms | 1.2× | 1.6% | PASS |
| mem_reads | `select_random_ranges` | 7.48ms | 8.89ms | 1.2× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 4.20ms | 4.26ms | 1.0× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 36.65ms | 40.78ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 7.93ms | 10.23ms | 1.3× | 1.1% | PASS |
| mem_reads | `index_join_scan` | 3.94ms | 5.38ms | 1.4× | 2.3% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.20s | 1.2× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.19s | 1.29s | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_read_only` | 150.88ms | 173.67ms | 1.2× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 242.90ms | 356.44ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 18.60ms | 36.09ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 65.18ms | 129.22ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 49.48ms | 87.41ms | 1.8× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 48.17ms | 98.84ms | 2.1× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 26.26ms | 61.61ms | 2.3× | 1.2% | FAIL |
| mem_writes | `types_delete_insert` | 31.83ms | 54.95ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 100.19ms | 164.57ms | 1.6× | 1.3% | PASS |
| file_reads | `oltp_point_select` | 102.33ms | 58.31ms | 0.6× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 26.66ms | 23.43ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 25.24ms | 22.85ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.42ms | 4.24ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_distinct_range` | 5.50ms | 5.41ms | 1.0× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 11.45ms | 8.00ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 34.62ms | 33.69ms | 1.0× | 1.9% | PASS |
| file_reads | `select_random_ranges` | 14.91ms | 10.92ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.21ms | 6.39ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 37.72ms | 41.16ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 11.96ms | 12.06ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join_scan` | 4.92ms | 5.82ms | 1.2× | 2.1% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.20s | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 1.19s | 1.29s | 1.1× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 256.13ms | 201.77ms | 0.8× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 255.94ms | 368.19ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 25.37ms | 42.10ms | 1.7× | 1.2% | PASS |
| file_writes | `oltp_update_index` | 94.44ms | 145.73ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 76.14ms | 100.25ms | 1.3× | 1.3% | PASS |
| file_writes | `oltp_delete_insert` | 75.61ms | 112.23ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 50.31ms | 71.85ms | 1.4× | 1.3% | PASS |
| file_writes | `types_delete_insert` | 49.17ms | 62.35ms | 1.3× | 1.4% | PASS |
| file_writes | `oltp_read_write` | 127.67ms | 176.81ms | 1.4× | 0.9% | PASS |
| ac_reads | `oltp_point_select` | 55.88ms | 58.64ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 22.44ms | 23.55ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 20.23ms | 22.87ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 4.02ms | 4.25ms | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_distinct_range` | 5.06ms | 5.41ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 7.28ms | 8.21ms | 1.1× | 1.6% | PASS |
| ac_reads | `select_random_points` | 30.15ms | 34.80ms | 1.2× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 10.23ms | 10.96ms | 1.1× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 6.92ms | 6.46ms | 0.9× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 37.73ms | 41.51ms | 1.1× | 1.2% | PASS |
| ac_reads | `index_join` | 9.76ms | 12.38ms | 1.3× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 4.50ms | 5.91ms | 1.3× | 1.2% | PASS |
| ac_reads | `types_table_scan` | 1.08s | 1.21s | 1.1× | 2.1% | PASS |
| ac_reads | `table_scan` | 1.29s | 1.31s | 1.0× | 3.8% | PASS |
| ac_reads | `oltp_read_only` | 193.05ms | 205.40ms | 1.1× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.57ms | 58.28ms | 2.7× | 5.4% | PASS |
| ac_writes | `oltp_insert_ac` | 23.36ms | 74.78ms | 3.2× | 4.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.68ms | 82.64ms | 3.2× | 4.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.39ms | 67.67ms | 3.0× | 6.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.19ms | 75.57ms | 3.3× | 5.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.59ms | 75.87ms | 3.1× | 7.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.98ms | 65.86ms | 3.1× | 5.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 30.49ms | 83.78ms | 2.7× | 5.8% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 59s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 89.88ms | 130.00ms | 69.1% | 0.3% | PASS |
| `status_dirty_many_tables` | 93.44ms | 130.00ms | 71.9% | 0.4% | PASS |
| `diff_regular_working_one_table` | 85.39ms | 120.00ms | 71.2% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 98.16ms | 140.00ms | 70.1% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 98.23ms | 140.00ms | 70.2% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 98.69ms | 140.00ms | 70.5% | 0.3% | PASS |
| `branch_list_many_branches` | 18.60ms | 35.00ms | 53.1% | 0.9% | PASS |
| `branch_create_delete` | 20.91ms | 40.00ms | 52.3% | 1.1% | PASS |
| `checkout_branch_clean` | 56.02ms | 150.00ms | 37.3% | 0.8% | PASS |
| `merge_data_no_conflicts` | 25.60ms | 50.00ms | 51.2% | 1.5% | PASS |
| `merge_schema_no_conflicts` | 18.21ms | 35.00ms | 52.0% | 1.7% | PASS |
| `merge_data_conflicts` | 27.78ms | 180.00ms | 15.4% | 1.0% | PASS |
| `merge_data_conflicts_with_resolve` | 27.72ms | 180.00ms | 15.4% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
