# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-17 14:43 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261012.334.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/39035122252)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.76s | 11.01s | 1.0× | 1.2% | **PASS** |
| Writes | 2.19s | 3.62s | 1.7× | 1.1% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.52s | 11.20s | 1.0× | 1.2% | **PASS** |
| Writes | 3.43s | 4.23s | 1.2× | 1.8% | **PASS** |
| Autocommit writes | 861.79ms | 2.52s | 2.9× | 5.2% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.43s | 2.53s | 1.0× | 0.9% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 3.01s | 2.88s | 1.0× | 1.2% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.54s | 2.73s | 1.1× | 1.6% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.78s | 2.87s | 1.0× | 1.6% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 371.31ms | 601.66ms | 1.6× | 0.8% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 630.87ms | 1.06s | 1.7× | 1.7% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 584.57ms | 991.32ms | 1.7× | 1.3% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 600.00ms | 974.90ms | 1.6× | 1.0% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.52s | 2.55s | 1.0× | 0.9% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.27s | 2.93s | 0.9× | 1.1% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.81s | 2.81s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.92s | 2.92s | 1.0× | 1.4% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 866.18ms | 915.75ms | 1.1× | 1.6% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 962.78ms | 1.15s | 1.2× | 4.2% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 818.07ms | 1.09s | 1.3× | 1.8% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 782.64ms | 1.07s | 1.4× | 1.8% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.46s | 2.55s | 1.0× | 0.9% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 3.10s | 2.93s | 0.9× | 1.5% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.63s | 2.80s | 1.1× | 1.5% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.94s | 2.97s | 1.0× | 1.8% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 247.37ms | 674.69ms | 2.7× | 4.3% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 205.50ms | 616.96ms | 3.0× | 4.7% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 204.56ms | 602.86ms | 2.9× | 5.5% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 204.37ms | 622.46ms | 3.0× | 6.5% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 21.94ms | 24.37ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_range_select` | 9.55ms | 10.30ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_sum_range` | 8.77ms | 9.91ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_order_range` | 2.50ms | 2.66ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.40ms | 3.72ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 3.61ms | 4.37ms | 1.2× | 1.3% | PASS |
| mem_reads | `select_random_points` | 10.03ms | 10.55ms | 1.1× | 0.7% | PASS |
| mem_reads | `select_random_ranges` | 2.70ms | 3.44ms | 1.3× | 0.9% | PASS |
| mem_reads | `covering_index_scan` | 3.51ms | 3.54ms | 1.0× | 0.7% | PASS |
| mem_reads | `groupby_scan` | 29.87ms | 32.01ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 5.23ms | 7.22ms | 1.4× | 0.9% | PASS |
| mem_reads | `index_join_scan` | 2.84ms | 4.16ms | 1.5× | 1.1% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.10s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.21s | 1.0× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 93.52ms | 105.35ms | 1.1× | 0.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 146.97ms | 217.14ms | 1.5× | 0.3% | PASS |
| mem_writes | `oltp_insert` | 13.10ms | 23.25ms | 1.8× | 0.5% | PASS |
| mem_writes | `oltp_update_index` | 44.51ms | 84.46ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 29.71ms | 50.05ms | 1.7× | 0.8% | PASS |
| mem_writes | `oltp_delete_insert` | 39.92ms | 61.47ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_write_only` | 18.84ms | 38.65ms | 2.1× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 20.87ms | 31.50ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_read_write` | 57.40ms | 95.15ms | 1.7× | 0.5% | PASS |
| file_reads | `oltp_point_select` | 54.13ms | 32.30ms | 0.6× | 0.5% | PASS |
| file_reads | `oltp_range_select` | 13.18ms | 11.38ms | 0.9× | 0.7% | PASS |
| file_reads | `oltp_sum_range` | 12.39ms | 11.01ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 2.91ms | 2.80ms | 1.0× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 3.85ms | 3.80ms | 1.0× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 7.03ms | 5.47ms | 0.8× | 1.0% | PASS |
| file_reads | `select_random_points` | 14.03ms | 11.87ms | 0.8× | 1.3% | PASS |
| file_reads | `select_random_ranges` | 6.00ms | 4.28ms | 0.7× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 6.99ms | 4.68ms | 0.7× | 1.2% | PASS |
| file_reads | `groupby_scan` | 29.98ms | 31.32ms | 1.0× | 0.5% | PASS |
| file_reads | `index_join` | 7.25ms | 8.33ms | 1.1× | 1.3% | PASS |
| file_reads | `index_join_scan` | 3.28ms | 4.46ms | 1.4× | 1.3% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.09s | 1.1× | 0.3% | PASS |
| file_reads | `table_scan` | 1.18s | 1.21s | 1.0× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 140.71ms | 116.74ms | 0.8× | 0.5% | PASS |
| file_writes | `oltp_bulk_insert` | 190.74ms | 263.06ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_insert` | 26.02ms | 34.39ms | 1.3× | 5.5% | PASS |
| file_writes | `oltp_update_index` | 141.43ms | 139.89ms | 1.0× | 1.0% | PASS |
| file_writes | `oltp_update_non_index` | 110.78ms | 89.61ms | 0.8× | 0.4% | PASS |
| file_writes | `oltp_delete_insert` | 121.28ms | 109.76ms | 0.9× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 83.64ms | 79.50ms | 1.0× | 7.0% | PASS |
| file_writes | `types_delete_insert` | 70.55ms | 59.99ms | 0.9× | 3.5% | PASS |
| file_writes | `oltp_read_write` | 121.74ms | 139.54ms | 1.1× | 0.4% | PASS |
| ac_reads | `oltp_point_select` | 31.43ms | 32.23ms | 1.0× | 0.6% | PASS |
| ac_reads | `oltp_range_select` | 10.82ms | 11.35ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_sum_range` | 10.32ms | 11.01ms | 1.1× | 0.7% | PASS |
| ac_reads | `oltp_order_range` | 2.67ms | 2.82ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_distinct_range` | 3.59ms | 3.79ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 4.96ms | 5.58ms | 1.1× | 0.9% | PASS |
| ac_reads | `select_random_points` | 11.87ms | 11.87ms | 1.0× | 0.7% | PASS |
| ac_reads | `select_random_ranges` | 3.87ms | 4.29ms | 1.1× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 4.76ms | 4.74ms | 1.0× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 29.74ms | 31.35ms | 1.1× | 0.5% | PASS |
| ac_reads | `index_join` | 6.23ms | 8.49ms | 1.4× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.11ms | 4.49ms | 1.4× | 1.7% | PASS |
| ac_reads | `types_table_scan` | 1.03s | 1.09s | 1.1× | 0.3% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.22s | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 107.97ms | 116.62ms | 1.1× | 0.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 28.29ms | 71.76ms | 2.5× | 3.7% | PASS |
| ac_writes | `oltp_insert_ac` | 31.11ms | 86.06ms | 2.8× | 4.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 34.03ms | 93.39ms | 2.7× | 5.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 28.35ms | 77.31ms | 2.7× | 3.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 30.23ms | 88.49ms | 2.9× | 5.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 30.19ms | 84.24ms | 2.8× | 3.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 30.84ms | 81.54ms | 2.6× | 6.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.33ms | 91.90ms | 2.7× | 3.6% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.58ms | 39.36ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 17.41ms | 14.37ms | 0.8× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 16.07ms | 14.10ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_order_range` | 3.29ms | 3.22ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.39ms | 4.38ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 4.09ms | 6.51ms | 1.6× | 1.8% | PASS |
| mem_reads | `select_random_points` | 24.09ms | 22.08ms | 0.9× | 3.1% | PASS |
| mem_reads | `select_random_ranges` | 3.65ms | 5.38ms | 1.5× | 1.7% | PASS |
| mem_reads | `covering_index_scan` | 4.26ms | 4.92ms | 1.2× | 1.1% | PASS |
| mem_reads | `groupby_scan` | 36.00ms | 35.16ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 11.07ms | 9.98ms | 0.9× | 2.6% | PASS |
| mem_reads | `index_join_scan` | 3.82ms | 5.67ms | 1.5× | 2.6% | PASS |
| mem_reads | `types_table_scan` | 1.27s | 1.23s | 1.0× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.42s | 1.34s | 0.9× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 152.89ms | 145.86ms | 1.0× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 245.16ms | 380.97ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 18.61ms | 40.23ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 72.26ms | 153.71ms | 2.1× | 2.2% | PASS |
| mem_writes | `oltp_update_non_index` | 52.92ms | 90.71ms | 1.7× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 58.52ms | 111.12ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 29.70ms | 65.14ms | 2.2× | 2.1% | PASS |
| mem_writes | `types_delete_insert` | 41.71ms | 57.16ms | 1.4× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 111.99ms | 157.24ms | 1.4× | 1.3% | PASS |
| file_reads | `oltp_point_select` | 116.81ms | 59.86ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 25.60ms | 16.53ms | 0.6× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 24.62ms | 16.37ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 4.29ms | 3.51ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_distinct_range` | 5.38ms | 4.67ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 12.09ms | 8.67ms | 0.7× | 1.1% | PASS |
| file_reads | `select_random_points` | 34.70ms | 25.51ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 11.42ms | 7.44ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 12.33ms | 7.00ms | 0.6× | 0.9% | PASS |
| file_reads | `groupby_scan` | 36.95ms | 35.46ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 16.18ms | 11.50ms | 0.7× | 2.9% | PASS |
| file_reads | `index_join_scan` | 4.92ms | 6.19ms | 1.3× | 2.5% | PASS |
| file_reads | `types_table_scan` | 1.25s | 1.22s | 1.0× | 1.1% | PASS |
| file_reads | `table_scan` | 1.45s | 1.33s | 0.9× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 269.47ms | 177.34ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 269.95ms | 395.32ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 26.75ms | 47.24ms | 1.8× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 131.49ms | 172.19ms | 1.3× | 6.6% | PASS |
| file_writes | `oltp_update_non_index` | 108.89ms | 105.72ms | 1.0× | 14.2% | PASS |
| file_writes | `oltp_delete_insert` | 101.53ms | 125.60ms | 1.2× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 87.06ms | 75.97ms | 0.9× | 10.6% | PASS |
| file_writes | `types_delete_insert` | 73.19ms | 64.81ms | 0.9× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 163.92ms | 165.17ms | 1.0× | 11.2% | PASS |
| ac_reads | `oltp_point_select` | 62.36ms | 58.40ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 19.53ms | 16.21ms | 0.8× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 18.75ms | 16.22ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.72ms | 3.49ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 4.81ms | 4.68ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 6.92ms | 8.60ms | 1.2× | 1.7% | PASS |
| ac_reads | `select_random_points` | 26.94ms | 24.54ms | 0.9× | 3.0% | PASS |
| ac_reads | `select_random_ranges` | 6.33ms | 7.37ms | 1.2× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 7.19ms | 6.96ms | 1.0× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 35.95ms | 35.28ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 12.55ms | 10.94ms | 0.9× | 1.9% | PASS |
| ac_reads | `index_join_scan` | 4.28ms | 5.88ms | 1.4× | 3.0% | PASS |
| ac_reads | `types_table_scan` | 1.26s | 1.22s | 1.0× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.44s | 1.33s | 0.9× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 190.09ms | 175.84ms | 0.9× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.99ms | 61.85ms | 2.7× | 4.2% | PASS |
| ac_writes | `oltp_insert_ac` | 25.98ms | 75.45ms | 2.9× | 4.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.20ms | 89.73ms | 3.3× | 4.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.76ms | 71.32ms | 3.1× | 4.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.57ms | 80.64ms | 3.2× | 4.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.38ms | 80.34ms | 3.2× | 4.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.68ms | 71.68ms | 3.0× | 7.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.96ms | 85.95ms | 2.7× | 4.8% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.95ms | 38.83ms | 1.1× | 2.2% | PASS |
| mem_reads | `oltp_range_select` | 16.69ms | 14.08ms | 0.8× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 15.52ms | 13.69ms | 0.9× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 3.26ms | 3.13ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.31ms | 4.23ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.90ms | 6.01ms | 1.5× | 1.1% | PASS |
| mem_reads | `select_random_points` | 19.60ms | 20.24ms | 1.0× | 1.8% | PASS |
| mem_reads | `select_random_ranges` | 3.31ms | 5.25ms | 1.6× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.20ms | 4.46ms | 1.1× | 1.2% | PASS |
| mem_reads | `groupby_scan` | 34.17ms | 34.28ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 9.72ms | 8.79ms | 0.9× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 3.54ms | 5.35ms | 1.5× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.17s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.18s | 1.27s | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 134.60ms | 135.02ms | 1.0× | 1.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 246.09ms | 375.37ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 18.61ms | 38.51ms | 2.1× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 61.56ms | 134.67ms | 2.2× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 47.05ms | 83.46ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 52.81ms | 103.41ms | 2.0× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 26.95ms | 60.24ms | 2.2× | 1.1% | PASS |
| mem_writes | `types_delete_insert` | 36.66ms | 52.05ms | 1.4× | 1.9% | PASS |
| mem_writes | `oltp_read_write` | 94.85ms | 143.62ms | 1.5× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 113.58ms | 59.30ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 23.33ms | 16.39ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_sum_range` | 22.80ms | 16.01ms | 0.7× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 4.16ms | 3.46ms | 0.8× | 2.3% | PASS |
| file_reads | `oltp_distinct_range` | 5.22ms | 4.54ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 11.49ms | 8.45ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 29.75ms | 24.00ms | 0.8× | 1.7% | PASS |
| file_reads | `select_random_ranges` | 10.93ms | 7.45ms | 0.7× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 11.83ms | 6.95ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 35.40ms | 34.92ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 14.30ms | 11.07ms | 0.8× | 2.1% | PASS |
| file_reads | `index_join_scan` | 4.78ms | 5.88ms | 1.2× | 2.6% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 251.95ms | 166.59ms | 0.7× | 1.4% | PASS |
| file_writes | `oltp_bulk_insert` | 267.18ms | 386.40ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 25.69ms | 45.92ms | 1.8× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 98.06ms | 154.00ms | 1.6× | 1.7% | PASS |
| file_writes | `oltp_update_non_index` | 90.64ms | 97.95ms | 1.1× | 8.7% | PASS |
| file_writes | `oltp_delete_insert` | 87.04ms | 116.38ms | 1.3× | 2.0% | PASS |
| file_writes | `oltp_write_only` | 57.48ms | 71.36ms | 1.2× | 2.4% | PASS |
| file_writes | `types_delete_insert` | 64.02ms | 61.90ms | 1.0× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 127.96ms | 154.99ms | 1.2× | 1.7% | PASS |
| ac_reads | `oltp_point_select` | 60.09ms | 58.71ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 19.26ms | 16.34ms | 0.8× | 1.5% | PASS |
| ac_reads | `oltp_sum_range` | 18.24ms | 15.85ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 3.77ms | 3.46ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.91ms | 4.65ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 6.67ms | 8.57ms | 1.3× | 1.5% | PASS |
| ac_reads | `select_random_points` | 24.76ms | 24.18ms | 1.0× | 3.3% | PASS |
| ac_reads | `select_random_ranges` | 6.10ms | 7.44ms | 1.2× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 7.01ms | 7.01ms | 1.0× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 35.37ms | 35.18ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 12.08ms | 11.12ms | 0.9× | 2.3% | PASS |
| ac_reads | `index_join_scan` | 4.24ms | 5.97ms | 1.4× | 3.2% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.17s | 1.1× | 0.4% | PASS |
| ac_reads | `table_scan` | 1.19s | 1.27s | 1.1× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 175.22ms | 166.17ms | 0.9× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.06ms | 60.47ms | 2.6× | 5.3% | PASS |
| ac_writes | `oltp_insert_ac` | 25.73ms | 77.01ms | 3.0× | 5.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.96ms | 84.63ms | 3.3× | 5.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.78ms | 69.95ms | 3.1× | 6.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.86ms | 79.30ms | 3.1× | 6.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.27ms | 77.11ms | 3.1× | 5.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.18ms | 70.06ms | 2.9× | 8.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.72ms | 84.31ms | 2.7× | 4.3% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.32ms | 41.51ms | 1.2× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 20.42ms | 21.25ms | 1.0× | 2.5% | PASS |
| mem_reads | `oltp_sum_range` | 18.25ms | 20.89ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 3.61ms | 3.86ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.73ms | 5.03ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 4.63ms | 6.43ms | 1.4× | 1.6% | PASS |
| mem_reads | `select_random_points` | 29.65ms | 33.83ms | 1.1× | 2.1% | PASS |
| mem_reads | `select_random_ranges` | 7.93ms | 9.04ms | 1.1× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.23ms | 4.42ms | 1.0× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 36.69ms | 39.87ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 7.94ms | 10.48ms | 1.3× | 1.6% | PASS |
| mem_reads | `index_join_scan` | 3.99ms | 5.54ms | 1.4× | 2.4% | PASS |
| mem_reads | `types_table_scan` | 1.10s | 1.20s | 1.1× | 1.1% | PASS |
| mem_reads | `table_scan` | 1.35s | 1.30s | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_read_only` | 158.57ms | 174.22ms | 1.1× | 1.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 252.02ms | 356.33ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 19.20ms | 35.43ms | 1.8× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 66.77ms | 128.29ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_non_index` | 51.61ms | 85.07ms | 1.6× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 49.61ms | 95.53ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 27.05ms | 58.73ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 33.63ms | 54.08ms | 1.6× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 100.12ms | 161.44ms | 1.6× | 1.0% | PASS |
| file_reads | `oltp_point_select` | 110.14ms | 61.16ms | 0.6× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 27.85ms | 23.38ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 25.74ms | 23.15ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 4.45ms | 4.25ms | 1.0× | 2.5% | PASS |
| file_reads | `oltp_distinct_range` | 5.69ms | 5.45ms | 1.0× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 12.34ms | 8.57ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_points` | 39.21ms | 37.22ms | 0.9× | 1.9% | PASS |
| file_reads | `select_random_ranges` | 16.14ms | 11.41ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 12.41ms | 6.87ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 38.20ms | 40.60ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 12.46ms | 12.58ms | 1.0× | 1.4% | PASS |
| file_reads | `index_join_scan` | 5.08ms | 6.08ms | 1.2× | 2.8% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.19s | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 1.27s | 1.29s | 1.0× | 3.4% | PASS |
| file_reads | `oltp_read_only` | 270.56ms | 204.97ms | 0.8× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 266.71ms | 369.56ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.31ms | 41.26ms | 1.6× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 97.50ms | 143.18ms | 1.5× | 1.7% | PASS |
| file_writes | `oltp_update_non_index` | 79.20ms | 100.12ms | 1.3× | 1.9% | PASS |
| file_writes | `oltp_delete_insert` | 77.79ms | 110.08ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 51.82ms | 70.03ms | 1.4× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 50.43ms | 60.88ms | 1.2× | 2.2% | PASS |
| file_writes | `oltp_read_write` | 132.87ms | 178.25ms | 1.3× | 2.4% | PASS |
| ac_reads | `oltp_point_select` | 59.61ms | 61.54ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 23.05ms | 23.57ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 20.97ms | 23.29ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 4.07ms | 4.26ms | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_distinct_range` | 5.18ms | 5.43ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_index_scan` | 7.30ms | 8.59ms | 1.2× | 1.8% | PASS |
| ac_reads | `select_random_points` | 31.52ms | 37.07ms | 1.2× | 1.8% | PASS |
| ac_reads | `select_random_ranges` | 10.51ms | 11.29ms | 1.1× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 7.16ms | 6.79ms | 0.9× | 2.0% | PASS |
| ac_reads | `groupby_scan` | 37.03ms | 40.13ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 9.91ms | 12.63ms | 1.3× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 4.43ms | 5.99ms | 1.4× | 2.7% | PASS |
| ac_reads | `types_table_scan` | 1.12s | 1.20s | 1.1× | 1.8% | PASS |
| ac_reads | `table_scan` | 1.39s | 1.32s | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_read_only` | 204.50ms | 211.57ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.59ms | 62.98ms | 2.7× | 8.8% | PASS |
| ac_writes | `oltp_insert_ac` | 24.84ms | 80.14ms | 3.2× | 6.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.50ms | 88.50ms | 3.2× | 5.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.34ms | 69.47ms | 3.0× | 7.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.26ms | 79.60ms | 3.3× | 5.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.09ms | 81.09ms | 3.2× | 5.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.93ms | 71.06ms | 3.1× | 7.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.81ms | 89.63ms | 2.7× | 6.8% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 14s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 96.26ms | 130.00ms | 74.0% | 0.8% | PASS |
| `status_dirty_many_tables` | 99.50ms | 130.00ms | 76.5% | 0.7% | PASS |
| `diff_regular_working_one_table` | 90.85ms | 120.00ms | 75.7% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 104.64ms | 140.00ms | 74.7% | 0.6% | PASS |
| `diff_stat_working_many_tables` | 104.78ms | 140.00ms | 74.8% | 0.9% | PASS |
| `diff_schema_working_many_tables` | 105.26ms | 140.00ms | 75.2% | 0.6% | PASS |
| `branch_list_many_branches` | 23.90ms | 35.00ms | 68.3% | 3.3% | PASS |
| `branch_create_delete` | 25.60ms | 40.00ms | 64.0% | 1.6% | PASS |
| `checkout_branch_clean` | 62.50ms | 150.00ms | 41.7% | 1.0% | PASS |
| `merge_data_no_conflicts` | 30.27ms | 50.00ms | 60.5% | 2.2% | PASS |
| `merge_schema_no_conflicts` | 23.04ms | 35.00ms | 65.8% | 1.8% | PASS |
| `merge_data_conflicts` | 33.62ms | 180.00ms | 18.7% | 1.2% | PASS |
| `merge_data_conflicts_with_resolve` | 33.12ms | 180.00ms | 18.4% | 1.4% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
