# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-09-27 16:20 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260920.314.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/36327182066)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.95s | 10.15s | 1.0× | 2.2% | **PASS** |
| Writes | 1.95s | 3.09s | 1.6× | 1.8% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.14s | 9.64s | 1.0× | 1.8% | **PASS** |
| Writes | 3.96s | 4.12s | 1.0× | 4.3% | **FAIL** |
| Autocommit writes | 777.10ms | 2.27s | 2.9× | 6.6% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.68s | 2.67s | 1.0× | 1.8% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.02s | 1.95s | 1.0× | 2.9% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.12s | 2.24s | 1.1× | 1.7% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 3.13s | 3.29s | 1.1× | 2.6% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 452.14ms | 755.60ms | 1.7× | 1.4% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 389.19ms | 628.58ms | 1.6× | 2.7% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 463.43ms | 728.42ms | 1.6× | 1.2% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 641.62ms | 977.42ms | 1.5× | 1.9% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.90s | 2.72s | 0.9× | 1.5% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.15s | 1.97s | 0.9× | 2.1% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.41s | 2.33s | 1.0× | 0.9% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.68s | 2.62s | 1.0× | 5.0% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 610.86ms | 817.26ms | 1.3× | 2.2% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.13s | 1.05s | 0.9× | 5.1% | **FAIL** |
| File-backed | Writes | blobpk | 8 | 55 | 1.14s | 1.14s | 1.0× | 7.8% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 1.09s | 1.11s | 1.0× | 4.1% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.68s | 2.70s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.91s | 1.87s | 1.0× | 2.4% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.21s | 2.30s | 1.0× | 0.9% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.43s | 2.53s | 1.0× | 1.1% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 201.70ms | 630.65ms | 3.1× | 6.5% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 183.31ms | 491.39ms | 2.7× | 4.8% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 211.50ms | 579.77ms | 2.7× | 25.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 180.59ms | 572.60ms | 3.2× | 8.7% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.81ms | 30.37ms | 1.2× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 10.74ms | 11.68ms | 1.1× | 2.2% | PASS |
| mem_reads | `oltp_sum_range` | 9.97ms | 11.41ms | 1.1× | 2.5% | PASS |
| mem_reads | `oltp_order_range` | 2.66ms | 2.84ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.69ms | 3.94ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.02ms | 5.62ms | 1.4× | 2.8% | PASS |
| mem_reads | `select_random_points` | 10.56ms | 11.31ms | 1.1× | 3.0% | PASS |
| mem_reads | `select_random_ranges` | 3.09ms | 4.08ms | 1.3× | 1.7% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.54ms | 1.1× | 2.2% | PASS |
| mem_reads | `groupby_scan` | 30.83ms | 33.20ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 5.91ms | 8.38ms | 1.4× | 2.5% | PASS |
| mem_reads | `index_join_scan` | 3.24ms | 4.51ms | 1.4× | 3.5% | PASS |
| mem_reads | `types_table_scan` | 1.14s | 1.16s | 1.0× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.31s | 1.25s | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 110.54ms | 121.58ms | 1.1× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.43ms | 280.18ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 15.53ms | 28.80ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 50.60ms | 100.04ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 36.50ms | 62.75ms | 1.7× | 2.5% | PASS |
| mem_writes | `oltp_delete_insert` | 46.41ms | 77.44ms | 1.7× | 2.3% | PASS |
| mem_writes | `oltp_write_only` | 22.86ms | 49.46ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 25.47ms | 39.85ms | 1.6× | 2.0% | PASS |
| mem_writes | `oltp_read_write` | 70.36ms | 117.08ms | 1.7× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 101.70ms | 51.15ms | 0.5× | 1.4% | PASS |
| file_reads | `oltp_range_select` | 18.89ms | 13.88ms | 0.7× | 2.3% | PASS |
| file_reads | `oltp_sum_range` | 17.92ms | 13.52ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_order_range` | 3.58ms | 3.15ms | 0.9× | 2.7% | PASS |
| file_reads | `oltp_distinct_range` | 4.69ms | 4.30ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_index_scan` | 11.92ms | 7.64ms | 0.6× | 1.7% | PASS |
| file_reads | `select_random_points` | 18.78ms | 13.50ms | 0.7× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 10.78ms | 6.09ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 12.22ms | 6.70ms | 0.5× | 1.6% | PASS |
| file_reads | `groupby_scan` | 31.90ms | 33.55ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 10.36ms | 9.71ms | 0.9× | 1.4% | PASS |
| file_reads | `index_join_scan` | 4.26ms | 4.89ms | 1.1× | 2.6% | PASS |
| file_reads | `types_table_scan` | 1.12s | 1.15s | 1.0× | 1.0% | PASS |
| file_reads | `table_scan` | 1.30s | 1.25s | 1.0× | 1.5% | PASS |
| file_reads | `oltp_read_only` | 219.47ms | 150.39ms | 0.7× | 0.4% | PASS |
| file_writes | `oltp_bulk_insert` | 197.69ms | 289.35ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 22.45ms | 32.69ms | 1.5× | 2.5% | PASS |
| file_writes | `oltp_update_index` | 78.77ms | 110.59ms | 1.4× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 59.24ms | 72.02ms | 1.2× | 2.3% | PASS |
| file_writes | `oltp_delete_insert` | 69.38ms | 85.28ms | 1.2× | 2.2% | PASS |
| file_writes | `oltp_write_only` | 45.08ms | 55.62ms | 1.2× | 1.5% | PASS |
| file_writes | `types_delete_insert` | 41.11ms | 45.71ms | 1.1× | 2.3% | PASS |
| file_writes | `oltp_read_write` | 97.14ms | 126.00ms | 1.3× | 2.6% | PASS |
| ac_reads | `oltp_point_select` | 49.78ms | 50.43ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 13.47ms | 13.69ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 12.62ms | 13.50ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.06ms | 3.14ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_distinct_range` | 4.09ms | 4.25ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.81ms | 7.62ms | 1.1× | 1.6% | PASS |
| ac_reads | `select_random_points` | 13.21ms | 13.45ms | 1.0× | 2.6% | PASS |
| ac_reads | `select_random_ranges` | 5.71ms | 6.07ms | 1.1× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 7.18ms | 6.63ms | 0.9× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 31.17ms | 33.49ms | 1.1× | 1.2% | PASS |
| ac_reads | `index_join` | 7.63ms | 9.76ms | 1.3× | 2.1% | PASS |
| ac_reads | `index_join_scan` | 3.69ms | 4.79ms | 1.3× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 1.10s | 1.14s | 1.0× | 1.5% | PASS |
| ac_reads | `table_scan` | 1.28s | 1.24s | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_read_only` | 143.78ms | 149.84ms | 1.0× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.55ms | 63.21ms | 2.8× | 6.6% | PASS |
| ac_writes | `oltp_insert_ac` | 24.66ms | 76.59ms | 3.1× | 6.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.96ms | 92.29ms | 3.3× | 7.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.21ms | 72.79ms | 3.1× | 7.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.47ms | 82.64ms | 3.4× | 4.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.37ms | 81.56ms | 3.2× | 6.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.95ms | 73.35ms | 3.2× | 6.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 30.54ms | 88.23ms | 2.9× | 5.4% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 25.44ms | 23.68ms | 0.9× | 3.7% | PASS |
| mem_reads | `oltp_range_select` | 10.99ms | 8.75ms | 0.8× | 3.0% | PASS |
| mem_reads | `oltp_sum_range` | 11.04ms | 8.98ms | 0.8× | 2.1% | PASS |
| mem_reads | `oltp_order_range` | 2.26ms | 2.02ms | 0.9× | 1.9% | PASS |
| mem_reads | `oltp_distinct_range` | 2.80ms | 2.53ms | 0.9× | 1.7% | PASS |
| mem_reads | `oltp_index_scan` | 2.69ms | 4.37ms | 1.6× | 1.7% | PASS |
| mem_reads | `select_random_points` | 19.04ms | 15.26ms | 0.8× | 2.9% | PASS |
| mem_reads | `select_random_ranges` | 2.65ms | 3.68ms | 1.4× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 2.45ms | 3.35ms | 1.4× | 2.9% | PASS |
| mem_reads | `groupby_scan` | 21.30ms | 20.06ms | 0.9× | 1.5% | PASS |
| mem_reads | `index_join` | 8.81ms | 7.30ms | 0.8× | 3.6% | PASS |
| mem_reads | `index_join_scan` | 3.02ms | 5.10ms | 1.7× | 4.0% | PASS |
| mem_reads | `types_table_scan` | 821.29ms | 819.91ms | 1.0× | 4.6% | PASS |
| mem_reads | `table_scan` | 984.09ms | 939.22ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_read_only` | 100.23ms | 82.79ms | 0.8× | 2.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 144.49ms | 218.21ms | 1.5× | 1.8% | PASS |
| mem_writes | `oltp_insert` | 11.00ms | 23.60ms | 2.1× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 46.41ms | 95.21ms | 2.1× | 3.7% | PASS |
| mem_writes | `oltp_update_non_index` | 35.27ms | 55.15ms | 1.6× | 3.2% | PASS |
| mem_writes | `oltp_delete_insert` | 36.48ms | 68.49ms | 1.9× | 2.1% | PASS |
| mem_writes | `oltp_write_only` | 19.59ms | 41.07ms | 2.1× | 2.7% | PASS |
| mem_writes | `types_delete_insert` | 27.86ms | 35.69ms | 1.3× | 2.6% | PASS |
| mem_writes | `oltp_read_write` | 68.10ms | 91.16ms | 1.3× | 4.1% | PASS |
| file_reads | `oltp_point_select` | 88.25ms | 40.48ms | 0.5× | 1.9% | PASS |
| file_reads | `oltp_range_select` | 17.54ms | 10.55ms | 0.6× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 17.01ms | 10.51ms | 0.6× | 3.2% | PASS |
| file_reads | `oltp_order_range` | 3.01ms | 2.26ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 3.47ms | 2.68ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_index_scan` | 8.57ms | 5.75ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_points` | 22.27ms | 15.06ms | 0.7× | 3.2% | PASS |
| file_reads | `select_random_ranges` | 8.37ms | 5.14ms | 0.6× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 8.42ms | 4.77ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 21.09ms | 19.86ms | 0.9× | 2.3% | PASS |
| file_reads | `index_join` | 11.51ms | 7.52ms | 0.7× | 3.4% | PASS |
| file_reads | `index_join_scan` | 3.42ms | 4.45ms | 1.3× | 2.3% | PASS |
| file_reads | `types_table_scan` | 750.31ms | 769.16ms | 1.0× | 3.6% | PASS |
| file_reads | `table_scan` | 1.01s | 962.61ms | 1.0× | 2.0% | PASS |
| file_reads | `oltp_read_only` | 182.72ms | 104.84ms | 0.6× | 2.6% | PASS |
| file_writes | `oltp_bulk_insert` | 220.83ms | 279.63ms | 1.3× | 5.5% | PASS |
| file_writes | `oltp_insert` | 20.03ms | 46.12ms | 2.3× | 6.9% | FAIL |
| file_writes | `oltp_update_index` | 162.35ms | 176.66ms | 1.1× | 4.7% | PASS |
| file_writes | `oltp_update_non_index` | 139.32ms | 109.77ms | 0.8× | 5.6% | PASS |
| file_writes | `oltp_delete_insert` | 172.59ms | 132.38ms | 0.8× | 3.2% | PASS |
| file_writes | `oltp_write_only` | 114.36ms | 92.57ms | 0.8× | 3.4% | PASS |
| file_writes | `types_delete_insert` | 138.40ms | 70.11ms | 0.5× | 7.1% | PASS |
| file_writes | `oltp_read_write` | 161.92ms | 141.46ms | 0.9× | 4.4% | PASS |
| ac_reads | `oltp_point_select` | 43.55ms | 37.43ms | 0.9× | 2.5% | PASS |
| ac_reads | `oltp_range_select` | 13.51ms | 10.24ms | 0.8× | 2.2% | PASS |
| ac_reads | `oltp_sum_range` | 13.26ms | 10.42ms | 0.8× | 3.1% | PASS |
| ac_reads | `oltp_order_range` | 2.64ms | 2.22ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 3.12ms | 2.70ms | 0.9× | 2.2% | PASS |
| ac_reads | `oltp_index_scan` | 4.94ms | 5.83ms | 1.2× | 2.2% | PASS |
| ac_reads | `select_random_points` | 17.82ms | 14.70ms | 0.8× | 2.4% | PASS |
| ac_reads | `select_random_ranges` | 4.40ms | 4.97ms | 1.1× | 1.8% | PASS |
| ac_reads | `covering_index_scan` | 4.74ms | 4.76ms | 1.0× | 2.6% | PASS |
| ac_reads | `groupby_scan` | 21.45ms | 20.36ms | 0.9× | 2.3% | PASS |
| ac_reads | `index_join` | 10.14ms | 7.73ms | 0.8× | 3.2% | PASS |
| ac_reads | `index_join_scan` | 3.19ms | 4.54ms | 1.4× | 1.7% | PASS |
| ac_reads | `types_table_scan` | 794.31ms | 784.80ms | 1.0× | 3.7% | PASS |
| ac_reads | `table_scan` | 861.72ms | 859.33ms | 1.0× | 3.2% | PASS |
| ac_reads | `oltp_read_only` | 112.65ms | 97.19ms | 0.9× | 3.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.28ms | 51.08ms | 2.4× | 3.2% | PASS |
| ac_writes | `oltp_insert_ac` | 23.19ms | 59.52ms | 2.6× | 4.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 23.76ms | 70.00ms | 2.9× | 5.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.26ms | 56.32ms | 2.8× | 5.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.48ms | 65.53ms | 2.8× | 5.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.26ms | 62.82ms | 2.8× | 4.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.60ms | 57.92ms | 2.7× | 5.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.49ms | 68.20ms | 2.5× | 4.1% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 28.42ms | 28.16ms | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_range_select` | 13.93ms | 10.84ms | 0.8× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 12.56ms | 10.61ms | 0.8× | 2.5% | PASS |
| mem_reads | `oltp_order_range` | 2.72ms | 2.54ms | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.56ms | 3.43ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.23ms | 4.69ms | 1.5× | 1.4% | PASS |
| mem_reads | `select_random_points` | 17.08ms | 15.96ms | 0.9× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 2.81ms | 4.25ms | 1.5× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 3.33ms | 3.67ms | 1.1× | 3.9% | PASS |
| mem_reads | `groupby_scan` | 29.06ms | 28.70ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 8.60ms | 7.13ms | 0.8× | 2.4% | PASS |
| mem_reads | `index_join_scan` | 3.12ms | 4.78ms | 1.5× | 2.4% | PASS |
| mem_reads | `types_table_scan` | 888.16ms | 961.95ms | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 995.48ms | 1.05s | 1.1× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 108.38ms | 103.70ms | 1.0× | 1.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 188.25ms | 267.21ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 14.52ms | 28.05ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 50.72ms | 102.95ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 38.41ms | 61.18ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_delete_insert` | 41.45ms | 76.91ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 22.41ms | 45.73ms | 2.0× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 30.22ms | 38.15ms | 1.3× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 77.45ms | 108.24ms | 1.4× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 104.11ms | 46.29ms | 0.4× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 22.05ms | 12.79ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_sum_range` | 20.35ms | 12.52ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.62ms | 2.80ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 4.43ms | 3.69ms | 0.8× | 0.6% | PASS |
| file_reads | `oltp_index_scan` | 10.99ms | 6.78ms | 0.6× | 0.6% | PASS |
| file_reads | `select_random_points` | 25.19ms | 17.85ms | 0.7× | 1.1% | PASS |
| file_reads | `select_random_ranges` | 10.32ms | 6.13ms | 0.6× | 0.6% | PASS |
| file_reads | `covering_index_scan` | 11.23ms | 5.80ms | 0.5× | 0.6% | PASS |
| file_reads | `groupby_scan` | 29.94ms | 29.21ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 13.31ms | 8.88ms | 0.7× | 1.3% | PASS |
| file_reads | `index_join_scan` | 4.05ms | 5.07ms | 1.3× | 1.8% | PASS |
| file_reads | `types_table_scan` | 888.02ms | 954.08ms | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 1.04s | 1.08s | 1.0× | 2.1% | PASS |
| file_reads | `oltp_read_only` | 222.52ms | 131.17ms | 0.6× | 1.7% | PASS |
| file_writes | `oltp_bulk_insert` | 258.56ms | 338.17ms | 1.3× | 7.2% | PASS |
| file_writes | `oltp_insert` | 28.81ms | 48.85ms | 1.7× | 3.6% | PASS |
| file_writes | `oltp_update_index` | 155.19ms | 185.02ms | 1.2× | 4.2% | PASS |
| file_writes | `oltp_update_non_index` | 135.44ms | 118.73ms | 0.9× | 9.4% | PASS |
| file_writes | `oltp_delete_insert` | 170.42ms | 139.20ms | 0.8× | 20.8% | PASS |
| file_writes | `oltp_write_only` | 109.93ms | 91.11ms | 0.8× | 12.0% | PASS |
| file_writes | `types_delete_insert` | 117.76ms | 69.64ms | 0.6× | 8.4% | PASS |
| file_writes | `oltp_read_write` | 159.73ms | 148.97ms | 0.9× | 6.4% | PASS |
| ac_reads | `oltp_point_select` | 52.66ms | 46.11ms | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_range_select` | 16.88ms | 12.79ms | 0.8× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 15.27ms | 12.47ms | 0.8× | 0.8% | PASS |
| ac_reads | `oltp_order_range` | 3.17ms | 2.80ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_distinct_range` | 3.93ms | 3.69ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.06ms | 6.83ms | 1.1× | 1.1% | PASS |
| ac_reads | `select_random_points` | 19.88ms | 17.75ms | 0.9× | 0.6% | PASS |
| ac_reads | `select_random_ranges` | 5.37ms | 6.15ms | 1.1× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 6.26ms | 5.85ms | 0.9× | 0.8% | PASS |
| ac_reads | `groupby_scan` | 29.54ms | 29.31ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 11.24ms | 9.19ms | 0.8× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.63ms | 5.23ms | 1.4× | 1.4% | PASS |
| ac_reads | `types_table_scan` | 884.54ms | 952.98ms | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 1.01s | 1.06s | 1.1× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 143.56ms | 129.61ms | 0.9× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.15ms | 51.66ms | 2.1× | 15.7% | PASS |
| ac_writes | `oltp_insert_ac` | 27.10ms | 67.20ms | 2.5× | 18.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.37ms | 82.24ms | 3.0× | 26.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.85ms | 60.73ms | 2.7× | 19.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 29.67ms | 81.86ms | 2.8× | 31.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.02ms | 90.07ms | 3.2× | 44.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.98ms | 66.36ms | 3.2× | 26.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.37ms | 79.66ms | 2.5× | 23.5% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.56ms | 42.54ms | 1.2× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 22.24ms | 24.57ms | 1.1× | 5.0% | PASS |
| mem_reads | `oltp_sum_range` | 19.97ms | 22.20ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 4.00ms | 4.19ms | 1.0× | 3.5% | PASS |
| mem_reads | `oltp_distinct_range` | 5.06ms | 5.36ms | 1.1× | 4.5% | PASS |
| mem_reads | `oltp_index_scan` | 4.88ms | 6.17ms | 1.3× | 2.6% | PASS |
| mem_reads | `select_random_points` | 28.58ms | 32.32ms | 1.1× | 5.3% | PASS |
| mem_reads | `select_random_ranges` | 7.89ms | 8.77ms | 1.1× | 6.4% | PASS |
| mem_reads | `covering_index_scan` | 4.68ms | 4.66ms | 1.0× | 4.5% | PASS |
| mem_reads | `groupby_scan` | 43.73ms | 47.32ms | 1.1× | 2.2% | PASS |
| mem_reads | `index_join` | 8.87ms | 11.57ms | 1.3× | 2.5% | PASS |
| mem_reads | `index_join_scan` | 4.68ms | 6.96ms | 1.5× | 2.6% | PASS |
| mem_reads | `types_table_scan` | 1.28s | 1.38s | 1.1× | 1.6% | PASS |
| mem_reads | `table_scan` | 1.49s | 1.51s | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_read_only` | 169.75ms | 189.03ms | 1.1× | 2.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 273.77ms | 366.13ms | 1.3× | 1.4% | PASS |
| mem_writes | `oltp_insert` | 21.49ms | 37.47ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_update_index` | 74.84ms | 135.86ms | 1.8× | 1.9% | PASS |
| mem_writes | `oltp_update_non_index` | 58.51ms | 85.45ms | 1.5× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 56.41ms | 102.43ms | 1.8× | 1.9% | PASS |
| mem_writes | `oltp_write_only` | 31.00ms | 62.14ms | 2.0× | 2.4% | PASS |
| mem_writes | `types_delete_insert` | 35.15ms | 51.76ms | 1.5× | 1.9% | PASS |
| mem_writes | `oltp_read_write` | 90.45ms | 136.18ms | 1.5× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 107.30ms | 51.60ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 25.30ms | 19.39ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_sum_range` | 24.66ms | 19.45ms | 0.8× | 5.0% | PASS |
| file_reads | `oltp_order_range` | 4.51ms | 3.88ms | 0.9× | 5.1% | PASS |
| file_reads | `oltp_distinct_range` | 5.75ms | 5.24ms | 0.9× | 9.4% | PASS |
| file_reads | `oltp_index_scan` | 13.99ms | 8.32ms | 0.6× | 5.4% | PASS |
| file_reads | `select_random_points` | 40.01ms | 35.81ms | 0.9× | 6.1% | PASS |
| file_reads | `select_random_ranges` | 17.98ms | 11.46ms | 0.6× | 6.6% | PASS |
| file_reads | `covering_index_scan` | 15.20ms | 7.71ms | 0.5× | 5.5% | PASS |
| file_reads | `groupby_scan` | 41.87ms | 45.15ms | 1.1× | 3.7% | PASS |
| file_reads | `index_join` | 13.45ms | 12.78ms | 1.0× | 2.6% | PASS |
| file_reads | `index_join_scan` | 4.98ms | 6.20ms | 1.2× | 4.2% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.15s | 1.1× | 2.5% | PASS |
| file_reads | `table_scan` | 1.06s | 1.08s | 1.0× | 1.8% | PASS |
| file_reads | `oltp_read_only` | 252.68ms | 169.99ms | 0.7× | 5.9% | PASS |
| file_writes | `oltp_bulk_insert` | 261.37ms | 321.67ms | 1.2× | 5.0% | PASS |
| file_writes | `oltp_insert` | 35.46ms | 46.94ms | 1.3× | 10.0% | PASS |
| file_writes | `oltp_update_index` | 164.91ms | 161.46ms | 1.0× | 3.3% | PASS |
| file_writes | `oltp_update_non_index` | 143.67ms | 119.94ms | 0.8× | 4.8% | PASS |
| file_writes | `oltp_delete_insert` | 136.73ms | 131.55ms | 1.0× | 2.4% | PASS |
| file_writes | `oltp_write_only` | 98.66ms | 91.52ms | 0.9× | 2.5% | PASS |
| file_writes | `types_delete_insert` | 88.41ms | 73.76ms | 0.8× | 7.2% | PASS |
| file_writes | `oltp_read_write` | 156.57ms | 165.63ms | 1.1× | 2.4% | PASS |
| ac_reads | `oltp_point_select` | 51.60ms | 48.80ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 19.21ms | 18.41ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 17.75ms | 17.77ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_order_range` | 3.45ms | 3.38ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.26ms | 4.28ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 6.68ms | 6.95ms | 1.0× | 1.1% | PASS |
| ac_reads | `select_random_points` | 25.11ms | 26.53ms | 1.1× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 8.79ms | 8.62ms | 1.0× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 6.30ms | 5.75ms | 0.9× | 0.8% | PASS |
| ac_reads | `groupby_scan` | 30.89ms | 32.80ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 8.06ms | 9.79ms | 1.2× | 1.0% | PASS |
| ac_reads | `index_join_scan` | 3.73ms | 5.09ms | 1.4× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 1.02s | 1.09s | 1.1× | 1.5% | PASS |
| ac_reads | `table_scan` | 1.07s | 1.09s | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 159.38ms | 159.67ms | 1.0× | 3.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 18.76ms | 52.35ms | 2.8× | 10.0% | PASS |
| ac_writes | `oltp_insert_ac` | 19.97ms | 63.92ms | 3.2× | 6.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 22.58ms | 75.85ms | 3.4× | 8.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 18.36ms | 58.08ms | 3.2× | 10.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 20.16ms | 66.06ms | 3.3× | 8.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.68ms | 78.46ms | 3.3× | 6.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.96ms | 75.16ms | 3.4× | 6.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 35.13ms | 102.71ms | 2.9× | 9.6% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 13s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 94.89ms | 130.00ms | 73.0% | 0.4% | PASS |
| `status_dirty_many_tables` | 98.63ms | 130.00ms | 75.9% | 0.4% | PASS |
| `diff_regular_working_one_table` | 90.71ms | 120.00ms | 75.6% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 103.70ms | 140.00ms | 74.1% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 103.29ms | 140.00ms | 73.8% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 104.41ms | 140.00ms | 74.6% | 0.3% | PASS |
| `branch_list_many_branches` | 23.06ms | 35.00ms | 65.9% | 1.2% | PASS |
| `branch_create_delete` | 25.22ms | 40.00ms | 63.0% | 1.0% | PASS |
| `checkout_branch_clean` | 61.07ms | 150.00ms | 40.7% | 1.2% | PASS |
| `merge_data_no_conflicts` | 29.07ms | 50.00ms | 58.1% | 1.0% | PASS |
| `merge_schema_no_conflicts` | 22.35ms | 35.00ms | 63.9% | 2.1% | PASS |
| `merge_data_conflicts` | 32.72ms | 180.00ms | 18.2% | 0.8% | PASS |
| `merge_data_conflicts_with_resolve` | 32.67ms | 180.00ms | 18.1% | 0.6% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
