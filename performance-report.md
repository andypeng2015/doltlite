# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-29 17:39 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260920.314.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/36594888523)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.43s | 11.04s | 1.1× | 1.4% | **PASS** |
| Writes | 2.23s | 3.68s | 1.6× | 1.1% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 12.45s | 11.58s | 0.9× | 1.2% | **PASS** |
| Writes | 3.27s | 4.13s | 1.3× | 1.6% | **PASS** |
| Autocommit writes | 840.99ms | 2.51s | 3.0× | 5.4% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.43s | 2.59s | 1.1× | 0.9% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.69s | 2.77s | 1.0× | 1.9% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.54s | 2.73s | 1.1× | 1.7% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.78s | 2.96s | 1.1× | 1.1% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 433.08ms | 724.28ms | 1.7× | 0.9% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 616.61ms | 1.02s | 1.7× | 1.4% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 581.47ms | 976.70ms | 1.7× | 1.2% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 599.78ms | 958.07ms | 1.6× | 0.7% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.72s | 2.67s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.37s | 2.94s | 0.9× | 1.7% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.79s | 2.80s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.58s | 3.18s | 0.9× | 0.8% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 609.05ms | 807.82ms | 1.3× | 2.1% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 996.12ms | 1.15s | 1.2× | 4.3% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 831.39ms | 1.07s | 1.3× | 1.5% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 833.17ms | 1.11s | 1.3× | 1.4% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.54s | 2.66s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.74s | 2.82s | 1.0× | 1.8% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.63s | 2.80s | 1.1× | 1.5% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 3.44s | 3.21s | 0.9× | 1.1% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 207.01ms | 638.18ms | 3.1× | 6.5% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 209.33ms | 614.84ms | 2.9× | 5.4% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 258.10ms | 709.65ms | 2.7× | 5.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 166.55ms | 545.21ms | 3.3× | 4.4% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.12ms | 29.68ms | 1.3× | 0.9% | PASS |
| mem_reads | `oltp_range_select` | 9.50ms | 11.48ms | 1.2× | 0.8% | PASS |
| mem_reads | `oltp_sum_range` | 9.51ms | 11.27ms | 1.2× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 2.60ms | 2.81ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 3.70ms | 3.94ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.94ms | 5.37ms | 1.4× | 1.3% | PASS |
| mem_reads | `select_random_points` | 10.20ms | 11.05ms | 1.1× | 2.0% | PASS |
| mem_reads | `select_random_ranges` | 2.99ms | 3.99ms | 1.3× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.31ms | 1.0× | 2.2% | PASS |
| mem_reads | `groupby_scan` | 29.89ms | 32.82ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 5.64ms | 7.75ms | 1.4× | 0.7% | PASS |
| mem_reads | `index_join_scan` | 2.85ms | 4.32ms | 1.5× | 1.4% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.3% | PASS |
| mem_reads | `table_scan` | 1.17s | 1.22s | 1.0× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 99.76ms | 116.92ms | 1.2× | 0.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 183.38ms | 274.29ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 15.34ms | 27.93ms | 1.8× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 48.90ms | 94.55ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 34.17ms | 59.69ms | 1.7× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 42.81ms | 72.26ms | 1.7× | 0.7% | PASS |
| mem_writes | `oltp_write_only` | 20.72ms | 46.23ms | 2.2× | 0.8% | PASS |
| mem_writes | `types_delete_insert` | 23.38ms | 37.71ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_read_write` | 64.38ms | 111.63ms | 1.7× | 0.8% | PASS |
| file_reads | `oltp_point_select` | 98.11ms | 49.21ms | 0.5× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 17.99ms | 13.59ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 17.26ms | 13.28ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.50ms | 3.10ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 4.55ms | 4.24ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_index_scan` | 11.88ms | 7.62ms | 0.6× | 1.4% | PASS |
| file_reads | `select_random_points` | 18.06ms | 13.12ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_ranges` | 10.52ms | 5.97ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 12.05ms | 6.36ms | 0.5× | 2.0% | PASS |
| file_reads | `groupby_scan` | 31.38ms | 33.31ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 10.34ms | 9.72ms | 0.9× | 1.7% | PASS |
| file_reads | `index_join_scan` | 4.21ms | 4.74ms | 1.1× | 1.6% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 1.20s | 1.22s | 1.0× | 1.5% | PASS |
| file_reads | `oltp_read_only` | 215.48ms | 148.41ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 196.60ms | 287.90ms | 1.5× | 1.7% | PASS |
| file_writes | `oltp_insert` | 22.27ms | 32.25ms | 1.4× | 2.2% | PASS |
| file_writes | `oltp_update_index` | 77.46ms | 106.53ms | 1.4× | 1.9% | PASS |
| file_writes | `oltp_update_non_index` | 59.62ms | 71.96ms | 1.2× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 70.21ms | 83.79ms | 1.2× | 2.4% | PASS |
| file_writes | `oltp_write_only` | 45.66ms | 55.34ms | 1.2× | 2.9% | PASS |
| file_writes | `types_delete_insert` | 40.76ms | 45.20ms | 1.1× | 2.2% | PASS |
| file_writes | `oltp_read_write` | 96.46ms | 124.84ms | 1.3× | 1.5% | PASS |
| ac_reads | `oltp_point_select` | 49.77ms | 50.29ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 13.44ms | 13.82ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 12.43ms | 13.40ms | 1.1× | 1.4% | PASS |
| ac_reads | `oltp_order_range` | 2.90ms | 3.11ms | 1.1× | 2.7% | PASS |
| ac_reads | `oltp_distinct_range` | 3.96ms | 4.21ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 6.58ms | 7.54ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_points` | 12.70ms | 13.12ms | 1.0× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 5.58ms | 6.00ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 6.97ms | 6.53ms | 0.9× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 30.85ms | 33.36ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 7.55ms | 9.58ms | 1.3× | 2.7% | PASS |
| ac_reads | `index_join_scan` | 3.58ms | 4.72ms | 1.3× | 1.4% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.19s | 1.22s | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 142.76ms | 148.92ms | 1.0× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.66ms | 65.27ms | 2.9× | 5.9% | PASS |
| ac_writes | `oltp_insert_ac` | 26.62ms | 79.93ms | 3.0× | 6.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.90ms | 89.79ms | 3.3× | 5.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.29ms | 72.55ms | 3.1× | 6.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.99ms | 82.82ms | 3.3× | 6.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.77ms | 81.67ms | 3.2× | 5.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.58ms | 77.88ms | 3.2× | 8.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.21ms | 88.28ms | 2.7× | 6.4% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.12ms | 38.57ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 17.44ms | 14.26ms | 0.8× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 16.33ms | 14.00ms | 0.9× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 3.31ms | 3.17ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 4.37ms | 4.31ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_index_scan` | 4.06ms | 6.36ms | 1.6× | 1.9% | PASS |
| mem_reads | `select_random_points` | 24.12ms | 21.78ms | 0.9× | 2.8% | PASS |
| mem_reads | `select_random_ranges` | 3.75ms | 5.40ms | 1.4× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.21ms | 4.79ms | 1.1× | 1.1% | PASS |
| mem_reads | `groupby_scan` | 35.42ms | 34.99ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 11.12ms | 9.40ms | 0.8× | 1.9% | PASS |
| mem_reads | `index_join_scan` | 3.91ms | 5.56ms | 1.4× | 2.8% | PASS |
| mem_reads | `types_table_scan` | 1.12s | 1.18s | 1.1× | 1.7% | PASS |
| mem_reads | `table_scan` | 1.26s | 1.28s | 1.0× | 2.0% | PASS |
| mem_reads | `oltp_read_only` | 137.93ms | 136.92ms | 1.0× | 1.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 244.30ms | 372.02ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 18.29ms | 38.94ms | 2.1× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 66.89ms | 142.01ms | 2.1× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 51.19ms | 87.30ms | 1.7× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 57.90ms | 108.28ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 30.19ms | 63.84ms | 2.1× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 41.69ms | 55.44ms | 1.3× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 106.15ms | 150.48ms | 1.4× | 2.3% | PASS |
| file_reads | `oltp_point_select` | 114.98ms | 59.09ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 26.34ms | 16.63ms | 0.6× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 25.09ms | 16.46ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_order_range` | 4.61ms | 3.66ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.64ms | 4.80ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 12.27ms | 8.76ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 35.99ms | 25.80ms | 0.7× | 3.6% | PASS |
| file_reads | `select_random_ranges` | 11.51ms | 7.49ms | 0.7× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 12.23ms | 7.04ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 36.83ms | 35.58ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 16.55ms | 11.38ms | 0.7× | 2.0% | PASS |
| file_reads | `index_join_scan` | 5.23ms | 6.22ms | 1.2× | 2.1% | PASS |
| file_reads | `types_table_scan` | 1.29s | 1.22s | 0.9× | 2.4% | PASS |
| file_reads | `table_scan` | 1.50s | 1.34s | 0.9× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 269.10ms | 173.45ms | 0.6× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 271.71ms | 393.30ms | 1.4× | 0.8% | PASS |
| file_writes | `oltp_insert` | 26.81ms | 47.01ms | 1.8× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 136.82ms | 167.31ms | 1.2× | 6.9% | PASS |
| file_writes | `oltp_update_non_index` | 118.32ms | 105.85ms | 0.9× | 10.3% | PASS |
| file_writes | `oltp_delete_insert` | 102.64ms | 125.74ms | 1.2× | 1.1% | PASS |
| file_writes | `oltp_write_only` | 87.29ms | 76.92ms | 0.9× | 7.6% | PASS |
| file_writes | `types_delete_insert` | 77.14ms | 65.97ms | 0.9× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 175.40ms | 164.56ms | 0.9× | 7.5% | PASS |
| ac_reads | `oltp_point_select` | 63.51ms | 58.80ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 22.01ms | 16.60ms | 0.8× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 19.11ms | 16.28ms | 0.9× | 2.3% | PASS |
| ac_reads | `oltp_order_range` | 4.17ms | 3.65ms | 0.9× | 3.0% | PASS |
| ac_reads | `oltp_distinct_range` | 5.03ms | 4.78ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 6.79ms | 8.54ms | 1.3× | 1.9% | PASS |
| ac_reads | `select_random_points` | 26.97ms | 24.89ms | 0.9× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 6.27ms | 7.45ms | 1.2× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 7.24ms | 6.98ms | 1.0× | 2.6% | PASS |
| ac_reads | `groupby_scan` | 35.57ms | 35.40ms | 1.0× | 0.6% | PASS |
| ac_reads | `index_join` | 12.94ms | 11.05ms | 0.9× | 2.0% | PASS |
| ac_reads | `index_join_scan` | 4.32ms | 5.86ms | 1.4× | 1.8% | PASS |
| ac_reads | `types_table_scan` | 1.10s | 1.18s | 1.1× | 1.0% | PASS |
| ac_reads | `table_scan` | 1.25s | 1.28s | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_read_only` | 176.58ms | 166.48ms | 0.9× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.56ms | 61.87ms | 2.6× | 4.5% | PASS |
| ac_writes | `oltp_insert_ac` | 25.83ms | 74.47ms | 2.9× | 5.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.36ms | 87.74ms | 3.2× | 5.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.84ms | 70.30ms | 3.1× | 5.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.64ms | 79.68ms | 3.0× | 5.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.09ms | 80.14ms | 3.1× | 6.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.37ms | 73.52ms | 3.0× | 8.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.64ms | 87.13ms | 2.7× | 4.8% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.01ms | 37.45ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 15.76ms | 13.81ms | 0.9× | 2.2% | PASS |
| mem_reads | `oltp_sum_range` | 15.05ms | 13.52ms | 0.9× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 3.11ms | 3.16ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_distinct_range` | 4.20ms | 4.31ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.88ms | 6.04ms | 1.6× | 1.8% | PASS |
| mem_reads | `select_random_points` | 20.70ms | 20.57ms | 1.0× | 3.5% | PASS |
| mem_reads | `select_random_ranges` | 3.34ms | 5.23ms | 1.6× | 2.0% | PASS |
| mem_reads | `covering_index_scan` | 4.16ms | 4.46ms | 1.1× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 34.20ms | 34.47ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 10.01ms | 8.88ms | 0.9× | 1.9% | PASS |
| mem_reads | `index_join_scan` | 3.66ms | 5.25ms | 1.4× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.19s | 1.27s | 1.1× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 133.10ms | 135.64ms | 1.0× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 240.98ms | 364.24ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.39ms | 38.04ms | 2.1× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 62.47ms | 135.39ms | 2.2× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 47.24ms | 82.89ms | 1.8× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 52.49ms | 101.07ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 27.29ms | 60.25ms | 2.2× | 1.1% | PASS |
| mem_writes | `types_delete_insert` | 37.68ms | 52.31ms | 1.4× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 94.93ms | 142.51ms | 1.5× | 1.2% | PASS |
| file_reads | `oltp_point_select` | 108.82ms | 58.43ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 23.57ms | 16.24ms | 0.7× | 2.4% | PASS |
| file_reads | `oltp_sum_range` | 23.09ms | 16.02ms | 0.7× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 4.16ms | 3.47ms | 0.8× | 2.4% | PASS |
| file_reads | `oltp_distinct_range` | 5.33ms | 4.69ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 11.58ms | 8.57ms | 0.7× | 1.1% | PASS |
| file_reads | `select_random_points` | 30.12ms | 23.90ms | 0.8× | 2.8% | PASS |
| file_reads | `select_random_ranges` | 10.91ms | 7.40ms | 0.7× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 11.69ms | 6.86ms | 0.6× | 1.9% | PASS |
| file_reads | `groupby_scan` | 35.27ms | 35.00ms | 1.0× | 0.6% | PASS |
| file_reads | `index_join` | 14.57ms | 11.12ms | 0.8× | 2.3% | PASS |
| file_reads | `index_join_scan` | 4.77ms | 5.70ms | 1.2× | 2.1% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.19s | 1.27s | 1.1× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 246.57ms | 167.50ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 260.92ms | 374.53ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 27.74ms | 45.10ms | 1.6× | 1.4% | PASS |
| file_writes | `oltp_update_index` | 101.45ms | 152.33ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 87.53ms | 96.51ms | 1.1× | 5.5% | PASS |
| file_writes | `oltp_delete_insert` | 90.69ms | 115.15ms | 1.3× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 63.64ms | 71.13ms | 1.1× | 2.6% | PASS |
| file_writes | `types_delete_insert` | 65.45ms | 61.07ms | 0.9× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 133.97ms | 154.25ms | 1.2× | 1.3% | PASS |
| ac_reads | `oltp_point_select` | 59.36ms | 58.02ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 18.98ms | 16.36ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_sum_range` | 18.15ms | 16.09ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.73ms | 3.52ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 4.79ms | 4.71ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.56ms | 8.64ms | 1.3× | 2.6% | PASS |
| ac_reads | `select_random_points` | 25.02ms | 24.00ms | 1.0× | 2.3% | PASS |
| ac_reads | `select_random_ranges` | 5.96ms | 7.37ms | 1.2× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 6.78ms | 6.91ms | 1.0× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 34.80ms | 35.01ms | 1.0× | 0.5% | PASS |
| ac_reads | `index_join` | 12.07ms | 11.08ms | 0.9× | 2.7% | PASS |
| ac_reads | `index_join_scan` | 4.22ms | 5.70ms | 1.4× | 2.2% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.17s | 1.1× | 0.3% | PASS |
| ac_reads | `table_scan` | 1.19s | 1.27s | 1.1× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 174.90ms | 168.04ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 30.04ms | 73.96ms | 2.5× | 4.4% | PASS |
| ac_writes | `oltp_insert_ac` | 31.62ms | 91.30ms | 2.9× | 5.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 33.06ms | 98.61ms | 3.0× | 6.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 29.05ms | 80.72ms | 2.8× | 6.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 32.32ms | 92.30ms | 2.9× | 5.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 32.74ms | 90.08ms | 2.8× | 4.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 30.43ms | 84.35ms | 2.8× | 7.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 38.84ms | 98.32ms | 2.5× | 4.7% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.81ms | 38.55ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_range_select` | 20.03ms | 20.98ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_sum_range` | 18.25ms | 20.35ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 3.75ms | 3.96ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 4.88ms | 5.11ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.65ms | 5.93ms | 1.3× | 1.5% | PASS |
| mem_reads | `select_random_points` | 27.92ms | 31.21ms | 1.1× | 0.9% | PASS |
| mem_reads | `select_random_ranges` | 7.70ms | 8.49ms | 1.1× | 0.8% | PASS |
| mem_reads | `covering_index_scan` | 4.29ms | 4.40ms | 1.0× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 39.13ms | 42.42ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 8.02ms | 10.47ms | 1.3× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 4.16ms | 6.04ms | 1.5× | 1.5% | PASS |
| mem_reads | `types_table_scan` | 1.14s | 1.24s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.31s | 1.35s | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 150.03ms | 165.71ms | 1.1× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 248.49ms | 337.97ms | 1.4× | 1.4% | PASS |
| mem_writes | `oltp_insert` | 19.39ms | 35.10ms | 1.8× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 67.92ms | 129.99ms | 1.9× | 0.7% | PASS |
| mem_writes | `oltp_update_non_index` | 52.88ms | 83.92ms | 1.6× | 0.7% | PASS |
| mem_writes | `oltp_delete_insert` | 51.07ms | 99.23ms | 1.9× | 0.5% | PASS |
| mem_writes | `oltp_write_only` | 28.07ms | 61.24ms | 2.2× | 0.7% | PASS |
| mem_writes | `types_delete_insert` | 33.01ms | 53.46ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 98.94ms | 157.16ms | 1.6× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 130.33ms | 62.11ms | 0.5× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 30.43ms | 23.30ms | 0.8× | 0.7% | PASS |
| file_reads | `oltp_sum_range` | 28.63ms | 22.80ms | 0.8× | 0.7% | PASS |
| file_reads | `oltp_order_range` | 4.85ms | 4.24ms | 0.9× | 0.8% | PASS |
| file_reads | `oltp_distinct_range` | 5.93ms | 5.36ms | 0.9× | 0.7% | PASS |
| file_reads | `oltp_index_scan` | 14.68ms | 8.47ms | 0.6× | 1.1% | PASS |
| file_reads | `select_random_points` | 38.18ms | 33.46ms | 0.9× | 0.8% | PASS |
| file_reads | `select_random_ranges` | 17.60ms | 10.89ms | 0.6× | 0.6% | PASS |
| file_reads | `covering_index_scan` | 14.43ms | 6.78ms | 0.5× | 1.1% | PASS |
| file_reads | `groupby_scan` | 39.85ms | 42.46ms | 1.1× | 1.1% | PASS |
| file_reads | `index_join` | 13.46ms | 12.34ms | 0.9× | 1.8% | PASS |
| file_reads | `index_join_scan` | 5.27ms | 6.47ms | 1.2× | 1.5% | PASS |
| file_reads | `types_table_scan` | 1.27s | 1.28s | 1.0× | 2.9% | PASS |
| file_reads | `table_scan` | 1.66s | 1.45s | 0.9× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 309.41ms | 210.88ms | 0.7× | 0.5% | PASS |
| file_writes | `oltp_bulk_insert` | 266.20ms | 361.40ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_insert` | 27.65ms | 43.12ms | 1.6× | 2.1% | PASS |
| file_writes | `oltp_update_index` | 110.91ms | 158.46ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 87.36ms | 105.70ms | 1.2× | 0.9% | PASS |
| file_writes | `oltp_delete_insert` | 86.65ms | 119.29ms | 1.4× | 1.3% | PASS |
| file_writes | `oltp_write_only` | 57.38ms | 75.86ms | 1.3× | 2.4% | PASS |
| file_writes | `types_delete_insert` | 54.97ms | 63.15ms | 1.1× | 1.4% | PASS |
| file_writes | `oltp_read_write` | 142.05ms | 181.71ms | 1.3× | 0.9% | PASS |
| ac_reads | `oltp_point_select` | 69.17ms | 65.03ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 25.72ms | 24.45ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 23.30ms | 23.58ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 4.33ms | 4.31ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 5.39ms | 5.45ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 8.59ms | 9.24ms | 1.1× | 2.2% | PASS |
| ac_reads | `select_random_points` | 34.07ms | 36.52ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_ranges` | 11.38ms | 11.21ms | 1.0× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 8.26ms | 7.50ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 39.95ms | 43.11ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 10.62ms | 13.58ms | 1.3× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 4.83ms | 7.16ms | 1.5× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 1.33s | 1.30s | 1.0× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.65s | 1.45s | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 215.33ms | 210.40ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 18.08ms | 51.49ms | 2.8× | 3.4% | PASS |
| ac_writes | `oltp_insert_ac` | 20.22ms | 69.44ms | 3.4× | 3.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 22.79ms | 80.12ms | 3.5× | 5.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 18.15ms | 61.31ms | 3.4× | 4.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 19.89ms | 70.90ms | 3.6× | 3.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 21.01ms | 71.38ms | 3.4× | 4.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 17.73ms | 60.22ms | 3.4× | 6.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.69ms | 80.36ms | 2.8× | 5.4% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 23s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 100.29ms | 130.00ms | 77.1% | 0.4% | PASS |
| `status_dirty_many_tables` | 104.32ms | 130.00ms | 80.2% | 0.5% | PASS |
| `diff_regular_working_one_table` | 95.63ms | 120.00ms | 79.7% | 0.5% | PASS |
| `diff_regular_working_many_tables` | 109.94ms | 140.00ms | 78.5% | 0.6% | PASS |
| `diff_stat_working_many_tables` | 110.74ms | 140.00ms | 79.1% | 0.4% | PASS |
| `diff_schema_working_many_tables` | 111.78ms | 140.00ms | 79.8% | 0.5% | PASS |
| `branch_list_many_branches` | 25.64ms | 35.00ms | 73.3% | 1.0% | PASS |
| `branch_create_delete` | 28.14ms | 40.00ms | 70.3% | 1.4% | PASS |
| `checkout_branch_clean` | 65.62ms | 150.00ms | 43.7% | 0.8% | PASS |
| `merge_data_no_conflicts` | 32.34ms | 50.00ms | 64.7% | 1.1% | PASS |
| `merge_schema_no_conflicts` | 24.83ms | 35.00ms | 70.9% | 1.4% | PASS |
| `merge_data_conflicts` | 36.15ms | 180.00ms | 20.1% | 0.7% | PASS |
| `merge_data_conflicts_with_resolve` | 36.35ms | 180.00ms | 20.2% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
