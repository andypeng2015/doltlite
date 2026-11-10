# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-10 18:46 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261101.186.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/42862498362)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.04s | 9.34s | 1.0× | 1.9% | **PASS** |
| Writes | 1.80s | 3.08s | 1.7× | 1.8% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.69s | 9.50s | 1.0× | 1.7% | **PASS** |
| Writes | 3.96s | 4.32s | 1.1× | 4.0% | **PASS** |
| Autocommit writes | 764.64ms | 2.31s | 3.0× | 9.5% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 1.99s | 2.12s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.55s | 2.79s | 1.1× | 1.6% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.43s | 2.54s | 1.0× | 2.6% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.07s | 1.90s | 0.9× | 2.7% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 334.63ms | 561.53ms | 1.7× | 1.6% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 582.87ms | 1.04s | 1.8× | 1.5% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 513.47ms | 863.52ms | 1.7× | 2.1% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 374.01ms | 619.66ms | 1.7× | 2.7% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.23s | 2.19s | 1.0× | 1.0% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.80s | 2.85s | 1.0× | 1.7% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.70s | 2.59s | 1.0× | 1.8% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 1.97s | 1.87s | 1.0× | 2.0% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 864.65ms | 885.43ms | 1.0× | 5.0% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 929.82ms | 1.15s | 1.2× | 3.3% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.26s | 1.34s | 1.1× | 3.0% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 900.43ms | 947.06ms | 1.1× | 4.1% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.06s | 2.17s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.64s | 2.85s | 1.1× | 1.7% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.64s | 2.70s | 1.0× | 2.0% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 1.95s | 1.93s | 1.0× | 2.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 161.40ms | 558.88ms | 3.5× | 23.8% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 207.59ms | 621.01ms | 3.0× | 5.9% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 184.54ms | 573.63ms | 3.1× | 12.1% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 211.10ms | 559.94ms | 2.7× | 6.9% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 18.50ms | 21.85ms | 1.2× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 8.55ms | 8.74ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_sum_range` | 7.44ms | 8.46ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 2.12ms | 2.25ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 2.92ms | 3.11ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.00ms | 3.75ms | 1.2× | 1.6% | PASS |
| mem_reads | `select_random_points` | 8.14ms | 8.87ms | 1.1× | 2.8% | PASS |
| mem_reads | `select_random_ranges` | 2.51ms | 3.09ms | 1.2× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 3.27ms | 3.12ms | 1.0× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 24.35ms | 26.97ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 4.52ms | 5.93ms | 1.3× | 1.2% | PASS |
| mem_reads | `index_join_scan` | 2.69ms | 3.71ms | 1.4× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 854.78ms | 921.52ms | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 966.68ms | 1.01s | 1.0× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 79.23ms | 88.89ms | 1.1× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 136.38ms | 204.84ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 11.73ms | 21.44ms | 1.8× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 39.21ms | 76.15ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 27.43ms | 47.16ms | 1.7× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 34.21ms | 58.30ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_write_only` | 17.13ms | 38.17ms | 2.2× | 1.8% | PASS |
| mem_writes | `types_delete_insert` | 18.84ms | 30.13ms | 1.6× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 49.70ms | 85.35ms | 1.7× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 86.66ms | 37.89ms | 0.4× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 15.93ms | 10.48ms | 0.7× | 0.8% | PASS |
| file_reads | `oltp_sum_range` | 14.89ms | 10.31ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 2.99ms | 2.52ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 3.72ms | 3.33ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 10.21ms | 5.89ms | 0.6× | 1.1% | PASS |
| file_reads | `select_random_points` | 15.96ms | 10.72ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_ranges` | 9.65ms | 4.85ms | 0.5× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 10.57ms | 5.24ms | 0.5× | 0.7% | PASS |
| file_reads | `groupby_scan` | 25.36ms | 27.29ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 8.56ms | 7.59ms | 0.9× | 1.5% | PASS |
| file_reads | `index_join_scan` | 3.65ms | 4.02ms | 1.1× | 2.5% | PASS |
| file_reads | `types_table_scan` | 872.78ms | 931.46ms | 1.1× | 1.2% | PASS |
| file_reads | `table_scan` | 974.12ms | 1.01s | 1.0× | 1.0% | PASS |
| file_reads | `oltp_read_only` | 176.54ms | 112.18ms | 0.6× | 0.6% | PASS |
| file_writes | `oltp_bulk_insert` | 181.26ms | 253.17ms | 1.4× | 5.5% | PASS |
| file_writes | `oltp_insert` | 27.74ms | 32.35ms | 1.2× | 0.7% | PASS |
| file_writes | `oltp_update_index` | 137.27ms | 134.42ms | 1.0× | 4.5% | PASS |
| file_writes | `oltp_update_non_index` | 111.48ms | 94.35ms | 0.8× | 3.7% | PASS |
| file_writes | `oltp_delete_insert` | 118.98ms | 108.69ms | 0.9× | 6.8% | PASS |
| file_writes | `oltp_write_only` | 90.81ms | 80.22ms | 0.9× | 20.2% | PASS |
| file_writes | `types_delete_insert` | 74.18ms | 56.04ms | 0.8× | 8.6% | PASS |
| file_writes | `oltp_read_write` | 122.92ms | 126.19ms | 1.0× | 4.1% | PASS |
| ac_reads | `oltp_point_select` | 39.49ms | 38.07ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 11.50ms | 10.64ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 10.35ms | 10.38ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_order_range` | 2.57ms | 2.53ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_distinct_range` | 3.29ms | 3.34ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 5.65ms | 5.92ms | 1.0× | 1.7% | PASS |
| ac_reads | `select_random_points` | 11.12ms | 10.71ms | 1.0× | 2.1% | PASS |
| ac_reads | `select_random_ranges` | 4.92ms | 4.85ms | 1.0× | 0.6% | PASS |
| ac_reads | `covering_index_scan` | 5.97ms | 5.26ms | 0.9× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 25.09ms | 27.40ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 6.22ms | 7.66ms | 1.2× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 3.12ms | 4.00ms | 1.3× | 1.8% | PASS |
| ac_reads | `types_table_scan` | 854.92ms | 920.64ms | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 963.14ms | 1.01s | 1.0× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 109.05ms | 112.47ms | 1.0× | 0.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.08ms | 47.47ms | 2.8× | 11.1% | PASS |
| ac_writes | `oltp_insert_ac` | 19.47ms | 61.02ms | 3.1× | 14.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.43ms | 80.27ms | 3.7× | 32.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.32ms | 54.19ms | 3.1× | 11.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 21.45ms | 93.38ms | 4.4× | 41.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 21.05ms | 69.14ms | 3.3× | 17.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.81ms | 68.88ms | 3.7× | 54.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 24.79ms | 84.53ms | 3.4× | 30.0% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.54ms | 38.32ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 16.77ms | 14.03ms | 0.8× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 15.15ms | 13.73ms | 0.9× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 3.27ms | 3.23ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 4.25ms | 4.37ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 3.92ms | 6.01ms | 1.5× | 1.7% | PASS |
| mem_reads | `select_random_points` | 21.72ms | 20.53ms | 0.9× | 3.3% | PASS |
| mem_reads | `select_random_ranges` | 3.47ms | 5.26ms | 1.5× | 2.2% | PASS |
| mem_reads | `covering_index_scan` | 4.18ms | 4.50ms | 1.1× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 34.71ms | 34.82ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 10.40ms | 8.92ms | 0.9× | 1.9% | PASS |
| mem_reads | `index_join_scan` | 3.73ms | 5.24ms | 1.4× | 2.4% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.20s | 1.1× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.29s | 1.1× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 136.12ms | 138.98ms | 1.0× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 235.28ms | 374.81ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 17.62ms | 39.70ms | 2.3× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 64.75ms | 148.20ms | 2.3× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 47.76ms | 91.24ms | 1.9× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 53.58ms | 108.90ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 27.62ms | 65.56ms | 2.4× | 1.3% | FAIL |
| mem_writes | `types_delete_insert` | 38.56ms | 58.59ms | 1.5× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 97.70ms | 149.90ms | 1.5× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 105.18ms | 56.11ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 24.39ms | 16.18ms | 0.7× | 2.8% | PASS |
| file_reads | `oltp_sum_range` | 22.93ms | 16.08ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.35ms | 3.59ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_distinct_range` | 5.34ms | 4.70ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 11.25ms | 8.19ms | 0.7× | 1.9% | PASS |
| file_reads | `select_random_points` | 30.77ms | 23.49ms | 0.8× | 2.0% | PASS |
| file_reads | `select_random_ranges` | 10.61ms | 7.13ms | 0.7× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 11.53ms | 6.68ms | 0.6× | 1.6% | PASS |
| file_reads | `groupby_scan` | 36.04ms | 35.36ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 14.81ms | 10.90ms | 0.7× | 2.9% | PASS |
| file_reads | `index_join_scan` | 4.84ms | 5.76ms | 1.2× | 2.5% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.19s | 1.1× | 0.8% | PASS |
| file_reads | `table_scan` | 1.21s | 1.29s | 1.1× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 244.79ms | 166.84ms | 0.7× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 259.56ms | 390.84ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 25.43ms | 47.38ms | 1.9× | 2.6% | PASS |
| file_writes | `oltp_update_index` | 133.35ms | 169.79ms | 1.3× | 10.2% | PASS |
| file_writes | `oltp_update_non_index` | 105.35ms | 107.69ms | 1.0× | 6.6% | PASS |
| file_writes | `oltp_delete_insert` | 97.17ms | 125.50ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 83.95ms | 79.18ms | 0.9× | 13.3% | PASS |
| file_writes | `types_delete_insert` | 73.42ms | 67.23ms | 0.9× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 151.58ms | 163.02ms | 1.1× | 4.1% | PASS |
| ac_reads | `oltp_point_select` | 58.58ms | 56.39ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 19.89ms | 16.21ms | 0.8× | 2.4% | PASS |
| ac_reads | `oltp_sum_range` | 18.17ms | 15.82ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.90ms | 3.58ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_distinct_range` | 4.91ms | 4.74ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 6.53ms | 8.21ms | 1.3× | 1.7% | PASS |
| ac_reads | `select_random_points` | 25.26ms | 23.43ms | 0.9× | 1.9% | PASS |
| ac_reads | `select_random_ranges` | 5.92ms | 7.12ms | 1.2× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 6.81ms | 6.69ms | 1.0× | 2.0% | PASS |
| ac_reads | `groupby_scan` | 35.59ms | 35.43ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 12.43ms | 10.85ms | 0.9× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 4.30ms | 5.72ms | 1.3× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.20s | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.29s | 1.1× | 0.6% | PASS |
| ac_reads | `oltp_read_only` | 172.20ms | 166.25ms | 1.0× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.28ms | 61.02ms | 2.6× | 9.0% | PASS |
| ac_writes | `oltp_insert_ac` | 26.69ms | 75.80ms | 2.8× | 5.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.52ms | 88.83ms | 3.3× | 4.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.65ms | 70.94ms | 3.1× | 5.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.30ms | 79.82ms | 3.0× | 6.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.80ms | 80.41ms | 3.1× | 6.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.27ms | 77.21ms | 3.2× | 10.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.08ms | 86.99ms | 2.7× | 5.7% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.84ms | 31.61ms | 1.0× | 2.6% | PASS |
| mem_reads | `oltp_range_select` | 16.43ms | 12.11ms | 0.7× | 3.6% | PASS |
| mem_reads | `oltp_sum_range` | 14.98ms | 12.15ms | 0.8× | 3.3% | PASS |
| mem_reads | `oltp_order_range` | 3.16ms | 2.86ms | 0.9× | 2.7% | PASS |
| mem_reads | `oltp_distinct_range` | 4.00ms | 3.75ms | 0.9× | 2.2% | PASS |
| mem_reads | `oltp_index_scan` | 3.51ms | 5.26ms | 1.5× | 2.5% | PASS |
| mem_reads | `select_random_points` | 20.66ms | 17.91ms | 0.9× | 3.1% | PASS |
| mem_reads | `select_random_ranges` | 3.16ms | 4.80ms | 1.5× | 1.7% | PASS |
| mem_reads | `covering_index_scan` | 3.75ms | 4.26ms | 1.1× | 3.2% | PASS |
| mem_reads | `groupby_scan` | 32.87ms | 32.89ms | 1.0× | 2.0% | PASS |
| mem_reads | `index_join` | 10.52ms | 8.31ms | 0.8× | 2.9% | PASS |
| mem_reads | `index_join_scan` | 3.64ms | 5.36ms | 1.5× | 3.0% | PASS |
| mem_reads | `types_table_scan` | 1.00s | 1.09s | 1.1× | 1.4% | PASS |
| mem_reads | `table_scan` | 1.16s | 1.19s | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_read_only` | 126.32ms | 114.35ms | 0.9× | 2.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 193.09ms | 300.21ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_insert` | 15.93ms | 34.56ms | 2.2× | 1.8% | PASS |
| mem_writes | `oltp_update_index` | 61.94ms | 132.59ms | 2.1× | 2.5% | PASS |
| mem_writes | `oltp_update_non_index` | 46.36ms | 76.05ms | 1.6× | 2.6% | PASS |
| mem_writes | `oltp_delete_insert` | 48.18ms | 94.00ms | 2.0× | 2.1% | PASS |
| mem_writes | `oltp_write_only` | 26.33ms | 56.97ms | 2.2× | 2.0% | PASS |
| mem_writes | `types_delete_insert` | 34.57ms | 47.08ms | 1.4× | 2.0% | PASS |
| mem_writes | `oltp_read_write` | 87.06ms | 122.07ms | 1.4× | 2.9% | PASS |
| file_reads | `oltp_point_select` | 106.16ms | 48.52ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 24.15ms | 13.75ms | 0.6× | 2.8% | PASS |
| file_reads | `oltp_sum_range` | 22.54ms | 13.50ms | 0.6× | 2.6% | PASS |
| file_reads | `oltp_order_range` | 4.06ms | 3.08ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.98ms | 4.09ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_index_scan` | 11.62ms | 7.41ms | 0.6× | 1.5% | PASS |
| file_reads | `select_random_points` | 29.86ms | 20.61ms | 0.7× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 11.11ms | 6.76ms | 0.6× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 12.03ms | 6.33ms | 0.5× | 1.8% | PASS |
| file_reads | `groupby_scan` | 32.92ms | 32.21ms | 1.0× | 1.8% | PASS |
| file_reads | `index_join` | 15.03ms | 9.86ms | 0.7× | 2.9% | PASS |
| file_reads | `index_join_scan` | 4.89ms | 5.96ms | 1.2× | 3.0% | PASS |
| file_reads | `types_table_scan` | 1.01s | 1.09s | 1.1× | 2.1% | PASS |
| file_reads | `table_scan` | 1.17s | 1.19s | 1.0× | 1.8% | PASS |
| file_reads | `oltp_read_only` | 239.24ms | 143.42ms | 0.6× | 1.6% | PASS |
| file_writes | `oltp_bulk_insert` | 278.73ms | 382.78ms | 1.4× | 3.9% | PASS |
| file_writes | `oltp_insert` | 32.04ms | 62.25ms | 1.9× | 3.1% | PASS |
| file_writes | `oltp_update_index` | 179.00ms | 213.39ms | 1.2× | 2.9% | PASS |
| file_writes | `oltp_update_non_index` | 148.37ms | 136.62ms | 0.9× | 2.4% | PASS |
| file_writes | `oltp_delete_insert` | 176.02ms | 162.01ms | 0.9× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 125.83ms | 112.57ms | 0.9× | 2.7% | PASS |
| file_writes | `types_delete_insert` | 132.77ms | 89.36ms | 0.7× | 4.0% | PASS |
| file_writes | `oltp_read_write` | 190.74ms | 182.61ms | 1.0× | 6.9% | PASS |
| ac_reads | `oltp_point_select` | 55.09ms | 48.56ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_range_select` | 19.88ms | 14.37ms | 0.7× | 2.9% | PASS |
| ac_reads | `oltp_sum_range` | 17.59ms | 13.85ms | 0.8× | 2.9% | PASS |
| ac_reads | `oltp_order_range` | 3.55ms | 3.04ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 4.40ms | 4.00ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_index_scan` | 6.59ms | 7.54ms | 1.1× | 2.4% | PASS |
| ac_reads | `select_random_points` | 23.85ms | 20.55ms | 0.9× | 3.4% | PASS |
| ac_reads | `select_random_ranges` | 5.74ms | 6.68ms | 1.2× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 6.70ms | 6.27ms | 0.9× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 32.86ms | 32.55ms | 1.0× | 1.5% | PASS |
| ac_reads | `index_join` | 12.80ms | 10.13ms | 0.8× | 4.2% | PASS |
| ac_reads | `index_join_scan` | 4.50ms | 6.18ms | 1.4× | 3.6% | PASS |
| ac_reads | `types_table_scan` | 1.07s | 1.14s | 1.1× | 1.5% | PASS |
| ac_reads | `table_scan` | 1.21s | 1.24s | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 163.32ms | 143.46ms | 0.9× | 2.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.69ms | 67.88ms | 3.0× | 32.3% | PASS |
| ac_writes | `oltp_insert_ac` | 24.05ms | 75.08ms | 3.1× | 13.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.08ms | 79.15ms | 3.3× | 12.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 19.81ms | 64.54ms | 3.3× | 12.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.15ms | 71.08ms | 3.1× | 11.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.45ms | 74.38ms | 3.3× | 12.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.56ms | 62.99ms | 3.1× | 8.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.76ms | 78.52ms | 2.8× | 7.1% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 21.69ms | 24.22ms | 1.1× | 3.4% | PASS |
| mem_reads | `oltp_range_select` | 12.38ms | 12.23ms | 1.0× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 11.47ms | 12.07ms | 1.1× | 4.0% | PASS |
| mem_reads | `oltp_order_range` | 2.43ms | 2.41ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_distinct_range` | 2.93ms | 2.91ms | 1.0× | 1.6% | PASS |
| mem_reads | `oltp_index_scan` | 3.09ms | 4.41ms | 1.4× | 3.8% | PASS |
| mem_reads | `select_random_points` | 18.19ms | 19.96ms | 1.1× | 3.5% | PASS |
| mem_reads | `select_random_ranges` | 4.79ms | 5.29ms | 1.1× | 2.2% | PASS |
| mem_reads | `covering_index_scan` | 2.40ms | 3.17ms | 1.3× | 5.1% | PASS |
| mem_reads | `groupby_scan` | 22.50ms | 23.17ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 5.01ms | 7.41ms | 1.5× | 6.3% | PASS |
| mem_reads | `index_join_scan` | 3.08ms | 4.59ms | 1.5× | 2.7% | PASS |
| mem_reads | `types_table_scan` | 797.23ms | 778.97ms | 1.0× | 3.1% | PASS |
| mem_reads | `table_scan` | 1.07s | 901.54ms | 0.8× | 1.9% | PASS |
| mem_reads | `oltp_read_only` | 95.55ms | 95.46ms | 1.0× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 143.85ms | 207.45ms | 1.4× | 1.5% | PASS |
| mem_writes | `oltp_insert` | 11.31ms | 22.41ms | 2.0× | 2.0% | PASS |
| mem_writes | `oltp_update_index` | 44.93ms | 89.02ms | 2.0× | 3.3% | PASS |
| mem_writes | `oltp_update_non_index` | 35.05ms | 56.69ms | 1.6× | 2.4% | PASS |
| mem_writes | `oltp_delete_insert` | 33.61ms | 67.70ms | 2.0× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 18.71ms | 42.77ms | 2.3× | 3.2% | PASS |
| mem_writes | `types_delete_insert` | 21.96ms | 36.15ms | 1.6× | 3.0% | PASS |
| mem_writes | `oltp_read_write` | 64.59ms | 97.47ms | 1.5× | 5.6% | PASS |
| file_reads | `oltp_point_select` | 76.18ms | 38.74ms | 0.5× | 2.5% | PASS |
| file_reads | `oltp_range_select` | 18.39ms | 13.73ms | 0.7× | 3.0% | PASS |
| file_reads | `oltp_sum_range` | 16.68ms | 13.17ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 3.01ms | 2.52ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 3.55ms | 3.06ms | 0.9× | 2.3% | PASS |
| file_reads | `oltp_index_scan` | 9.34ms | 5.99ms | 0.6× | 1.7% | PASS |
| file_reads | `select_random_points` | 25.05ms | 22.10ms | 0.9× | 3.4% | PASS |
| file_reads | `select_random_ranges` | 10.80ms | 7.07ms | 0.7× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 8.92ms | 4.80ms | 0.5× | 2.0% | PASS |
| file_reads | `groupby_scan` | 23.99ms | 23.98ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join` | 8.73ms | 8.39ms | 1.0× | 3.9% | PASS |
| file_reads | `index_join_scan` | 3.74ms | 4.47ms | 1.2× | 6.4% | PASS |
| file_reads | `types_table_scan` | 783.09ms | 780.11ms | 1.0× | 4.6% | PASS |
| file_reads | `table_scan` | 819.91ms | 837.21ms | 1.0× | 2.0% | PASS |
| file_reads | `oltp_read_only` | 156.53ms | 108.83ms | 0.7× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 194.26ms | 254.79ms | 1.3× | 5.7% | PASS |
| file_writes | `oltp_insert` | 19.46ms | 36.52ms | 1.9× | 12.7% | PASS |
| file_writes | `oltp_update_index` | 146.53ms | 149.54ms | 1.0× | 3.5% | PASS |
| file_writes | `oltp_update_non_index` | 124.85ms | 99.45ms | 0.8× | 2.9% | PASS |
| file_writes | `oltp_delete_insert` | 121.70ms | 111.96ms | 0.9× | 2.7% | PASS |
| file_writes | `oltp_write_only` | 90.70ms | 88.79ms | 1.0× | 3.9% | PASS |
| file_writes | `types_delete_insert` | 72.44ms | 67.89ms | 0.9× | 10.1% | PASS |
| file_writes | `oltp_read_write` | 130.47ms | 138.10ms | 1.1× | 4.2% | PASS |
| ac_reads | `oltp_point_select` | 40.30ms | 38.69ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_range_select` | 14.62ms | 13.49ms | 0.9× | 2.5% | PASS |
| ac_reads | `oltp_sum_range` | 13.16ms | 13.19ms | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_order_range` | 2.67ms | 2.52ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 3.20ms | 3.04ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 5.46ms | 5.75ms | 1.1× | 2.3% | PASS |
| ac_reads | `select_random_points` | 20.82ms | 22.20ms | 1.1× | 4.0% | PASS |
| ac_reads | `select_random_ranges` | 6.82ms | 6.77ms | 1.0× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 5.10ms | 4.77ms | 0.9× | 2.4% | PASS |
| ac_reads | `groupby_scan` | 23.70ms | 24.26ms | 1.0× | 1.6% | PASS |
| ac_reads | `index_join` | 7.23ms | 9.32ms | 1.3× | 5.7% | PASS |
| ac_reads | `index_join_scan` | 3.41ms | 4.71ms | 1.4× | 5.1% | PASS |
| ac_reads | `types_table_scan` | 771.02ms | 786.75ms | 1.0× | 2.3% | PASS |
| ac_reads | `table_scan` | 904.01ms | 874.17ms | 1.0× | 7.9% | PASS |
| ac_reads | `oltp_read_only` | 127.54ms | 121.82ms | 1.0× | 1.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.98ms | 58.13ms | 2.5× | 7.3% | PASS |
| ac_writes | `oltp_insert_ac` | 27.22ms | 72.25ms | 2.7× | 6.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.52ms | 78.88ms | 2.7× | 6.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.54ms | 63.41ms | 2.8× | 4.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.09ms | 72.48ms | 2.8× | 7.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.44ms | 73.54ms | 2.8× | 8.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 26.87ms | 64.45ms | 2.4× | 11.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.45ms | 76.81ms | 2.6× | 6.7% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 51s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 68.27ms | 130.00ms | 52.5% | 0.3% | PASS |
| `status_dirty_many_tables` | 70.35ms | 130.00ms | 54.1% | 0.4% | PASS |
| `diff_regular_working_one_table` | 63.89ms | 120.00ms | 53.2% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 73.74ms | 140.00ms | 52.7% | 0.3% | PASS |
| `diff_stat_working_many_tables` | 74.03ms | 140.00ms | 52.9% | 0.6% | PASS |
| `diff_schema_working_many_tables` | 75.32ms | 140.00ms | 53.8% | 0.4% | PASS |
| `branch_list_many_branches` | 16.48ms | 35.00ms | 47.1% | 1.5% | PASS |
| `branch_create_delete` | 24.34ms | 40.00ms | 60.9% | 5.1% | PASS |
| `checkout_branch_clean` | 100.99ms | 150.00ms | 67.3% | 6.3% | PASS |
| `merge_data_no_conflicts` | 30.78ms | 50.00ms | 61.6% | 5.3% | PASS |
| `merge_schema_no_conflicts` | 16.70ms | 35.00ms | 47.7% | 5.3% | PASS |
| `merge_data_conflicts` | 23.89ms | 180.00ms | 13.3% | 1.8% | PASS |
| `merge_data_conflicts_with_resolve` | 24.23ms | 180.00ms | 13.5% | 1.1% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
