# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-12 20:50 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261004.327.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/38296172743)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.35s | 8.51s | 1.0× | 2.2% | **PASS** |
| Writes | 1.71s | 2.73s | 1.6× | 2.1% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.37s | 8.78s | 0.9× | 1.7% | **PASS** |
| Writes | 3.99s | 4.04s | 1.0× | 6.3% | **PASS** |
| Autocommit writes | 824.95ms | 2.58s | 3.1× | 7.3% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 1.56s | 1.64s | 1.0× | 2.5% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.58s | 2.40s | 0.9× | 2.2% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.58s | 2.74s | 1.1× | 1.8% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 1.63s | 1.74s | 1.1× | 2.2% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 249.97ms | 406.05ms | 1.6× | 2.3% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 508.41ms | 781.07ms | 1.5× | 2.0% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 584.37ms | 976.66ms | 1.7× | 1.2% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 363.17ms | 568.96ms | 1.6× | 2.3% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 1.79s | 1.70s | 1.0× | 1.7% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.83s | 2.44s | 0.9× | 1.5% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.82s | 2.81s | 1.0× | 1.7% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 1.92s | 1.83s | 0.9× | 2.1% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 716.40ms | 699.87ms | 1.0× | 3.9% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.54s | 1.35s | 0.9× | 15.4% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 823.00ms | 1.08s | 1.3× | 2.3% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 902.10ms | 908.48ms | 1.0× | 7.3% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 1.61s | 1.66s | 1.0× | 2.1% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.45s | 2.37s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.65s | 2.81s | 1.1× | 1.9% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.23s | 1.99s | 0.9× | 2.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 165.60ms | 475.64ms | 2.9× | 3.6% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 241.43ms | 881.17ms | 3.6× | 51.9% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 233.40ms | 711.70ms | 3.0× | 9.9% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 184.52ms | 511.90ms | 2.8× | 5.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 15.23ms | 17.77ms | 1.2× | 3.0% | PASS |
| mem_reads | `oltp_range_select` | 6.35ms | 6.68ms | 1.1× | 2.8% | PASS |
| mem_reads | `oltp_sum_range` | 5.94ms | 6.58ms | 1.1× | 2.7% | PASS |
| mem_reads | `oltp_order_range` | 1.60ms | 1.65ms | 1.0× | 2.8% | PASS |
| mem_reads | `oltp_distinct_range` | 2.11ms | 2.22ms | 1.1× | 2.7% | PASS |
| mem_reads | `oltp_index_scan` | 2.55ms | 3.30ms | 1.3× | 2.4% | PASS |
| mem_reads | `select_random_points` | 7.65ms | 8.42ms | 1.1× | 3.6% | PASS |
| mem_reads | `select_random_ranges` | 2.07ms | 2.62ms | 1.3× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 2.42ms | 2.59ms | 1.1× | 2.5% | PASS |
| mem_reads | `groupby_scan` | 16.47ms | 17.55ms | 1.1× | 2.2% | PASS |
| mem_reads | `index_join` | 3.46ms | 4.53ms | 1.3× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 2.29ms | 3.48ms | 1.5× | 4.3% | PASS |
| mem_reads | `types_table_scan` | 683.11ms | 718.05ms | 1.1× | 1.2% | PASS |
| mem_reads | `table_scan` | 759.06ms | 783.49ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 53.73ms | 60.18ms | 1.1× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 102.71ms | 149.83ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 8.87ms | 15.88ms | 1.8× | 1.9% | PASS |
| mem_writes | `oltp_update_index` | 29.59ms | 55.41ms | 1.9× | 2.3% | PASS |
| mem_writes | `oltp_update_non_index` | 20.77ms | 34.67ms | 1.7× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 25.93ms | 42.31ms | 1.6× | 2.8% | PASS |
| mem_writes | `oltp_write_only` | 12.56ms | 27.38ms | 2.2× | 2.4% | PASS |
| mem_writes | `types_delete_insert` | 14.50ms | 21.51ms | 1.5× | 4.0% | PASS |
| mem_writes | `oltp_read_write` | 35.03ms | 59.06ms | 1.7× | 2.3% | PASS |
| file_reads | `oltp_point_select` | 69.18ms | 32.15ms | 0.5× | 1.7% | PASS |
| file_reads | `oltp_range_select` | 11.90ms | 8.00ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 11.28ms | 7.86ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 2.18ms | 1.83ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 2.70ms | 2.37ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 8.33ms | 4.99ms | 0.6× | 3.5% | PASS |
| file_reads | `select_random_points` | 13.23ms | 9.86ms | 0.7× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 7.48ms | 3.97ms | 0.5× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 8.81ms | 4.65ms | 0.5× | 1.7% | PASS |
| file_reads | `groupby_scan` | 18.45ms | 18.95ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 7.04ms | 6.44ms | 0.9× | 2.6% | PASS |
| file_reads | `index_join_scan` | 2.95ms | 3.83ms | 1.3× | 4.2% | PASS |
| file_reads | `types_table_scan` | 680.82ms | 702.83ms | 1.0× | 1.4% | PASS |
| file_reads | `table_scan` | 801.79ms | 808.19ms | 1.0× | 2.1% | PASS |
| file_reads | `oltp_read_only` | 144.75ms | 88.57ms | 0.6× | 1.5% | PASS |
| file_writes | `oltp_bulk_insert` | 144.29ms | 201.10ms | 1.4× | 3.2% | PASS |
| file_writes | `oltp_insert` | 19.85ms | 28.93ms | 1.5× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 121.33ms | 103.23ms | 0.9× | 3.3% | PASS |
| file_writes | `oltp_update_non_index` | 100.74ms | 69.81ms | 0.7× | 2.0% | PASS |
| file_writes | `oltp_delete_insert` | 101.40ms | 88.91ms | 0.9× | 4.5% | PASS |
| file_writes | `oltp_write_only` | 71.72ms | 59.75ms | 0.8× | 5.8% | PASS |
| file_writes | `types_delete_insert` | 61.72ms | 49.49ms | 0.8× | 9.9% | PASS |
| file_writes | `oltp_read_write` | 95.36ms | 98.66ms | 1.0× | 7.2% | PASS |
| ac_reads | `oltp_point_select` | 33.11ms | 32.91ms | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_range_select` | 8.20ms | 8.13ms | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_sum_range` | 7.86ms | 8.06ms | 1.0× | 2.8% | PASS |
| ac_reads | `oltp_order_range` | 1.95ms | 1.93ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_distinct_range` | 2.42ms | 2.45ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_index_scan` | 4.83ms | 5.21ms | 1.1× | 1.5% | PASS |
| ac_reads | `select_random_points` | 10.88ms | 11.06ms | 1.0× | 2.9% | PASS |
| ac_reads | `select_random_ranges` | 4.10ms | 4.18ms | 1.0× | 1.7% | PASS |
| ac_reads | `covering_index_scan` | 4.84ms | 4.53ms | 0.9× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 17.24ms | 18.10ms | 1.0× | 1.6% | PASS |
| ac_reads | `index_join` | 5.02ms | 6.50ms | 1.3× | 3.1% | PASS |
| ac_reads | `index_join_scan` | 2.57ms | 3.71ms | 1.4× | 4.0% | PASS |
| ac_reads | `types_table_scan` | 671.11ms | 696.10ms | 1.0× | 1.3% | PASS |
| ac_reads | `table_scan` | 757.15ms | 775.48ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 81.12ms | 82.00ms | 1.0× | 1.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.14ms | 48.66ms | 2.5× | 5.2% | PASS |
| ac_writes | `oltp_insert_ac` | 21.00ms | 59.33ms | 2.8× | 6.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 22.29ms | 65.84ms | 3.0× | 3.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 19.17ms | 52.96ms | 2.8× | 3.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 20.43ms | 62.29ms | 3.0× | 2.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 21.00ms | 60.94ms | 2.9× | 2.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 19.07ms | 59.62ms | 3.1× | 3.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 23.50ms | 66.00ms | 2.8× | 4.2% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.00ms | 30.20ms | 0.9× | 3.7% | PASS |
| mem_reads | `oltp_range_select` | 15.61ms | 11.40ms | 0.7× | 2.8% | PASS |
| mem_reads | `oltp_sum_range` | 13.69ms | 11.10ms | 0.8× | 3.2% | PASS |
| mem_reads | `oltp_order_range` | 2.89ms | 2.61ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.73ms | 3.48ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.35ms | 4.98ms | 1.5× | 1.7% | PASS |
| mem_reads | `select_random_points` | 19.69ms | 16.77ms | 0.9× | 4.5% | PASS |
| mem_reads | `select_random_ranges` | 3.06ms | 4.32ms | 1.4× | 2.2% | PASS |
| mem_reads | `covering_index_scan` | 3.38ms | 3.88ms | 1.1× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 29.76ms | 28.98ms | 1.0× | 1.1% | PASS |
| mem_reads | `index_join` | 9.83ms | 7.65ms | 0.8× | 2.8% | PASS |
| mem_reads | `index_join_scan` | 3.37ms | 4.88ms | 1.4× | 2.8% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.03s | 1.0× | 2.9% | PASS |
| mem_reads | `table_scan` | 1.24s | 1.13s | 0.9× | 1.6% | PASS |
| mem_reads | `oltp_read_only` | 131.26ms | 111.81ms | 0.9× | 2.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 191.13ms | 274.74ms | 1.4× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 14.62ms | 29.48ms | 2.0× | 1.6% | PASS |
| mem_writes | `oltp_update_index` | 58.88ms | 116.14ms | 2.0× | 2.6% | PASS |
| mem_writes | `oltp_update_non_index` | 45.52ms | 67.10ms | 1.5× | 2.8% | PASS |
| mem_writes | `oltp_delete_insert` | 47.48ms | 85.28ms | 1.8× | 1.7% | PASS |
| mem_writes | `oltp_write_only` | 25.51ms | 50.09ms | 2.0× | 1.6% | PASS |
| mem_writes | `types_delete_insert` | 35.67ms | 41.77ms | 1.2× | 2.2% | PASS |
| mem_writes | `oltp_read_write` | 89.61ms | 116.47ms | 1.3× | 2.5% | PASS |
| file_reads | `oltp_point_select` | 111.35ms | 48.59ms | 0.4× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 24.67ms | 13.30ms | 0.5× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 22.64ms | 13.24ms | 0.6× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 3.78ms | 2.83ms | 0.7× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 4.62ms | 3.74ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 11.31ms | 7.03ms | 0.6× | 1.3% | PASS |
| file_reads | `select_random_points` | 31.36ms | 19.97ms | 0.6× | 2.9% | PASS |
| file_reads | `select_random_ranges` | 10.78ms | 6.18ms | 0.6× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 11.50ms | 5.96ms | 0.5× | 1.0% | PASS |
| file_reads | `groupby_scan` | 31.16ms | 29.68ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 14.94ms | 9.32ms | 0.6× | 2.9% | PASS |
| file_reads | `index_join_scan` | 4.22ms | 5.41ms | 1.3× | 3.5% | PASS |
| file_reads | `types_table_scan` | 1.04s | 999.53ms | 1.0× | 5.0% | PASS |
| file_reads | `table_scan` | 1.27s | 1.14s | 0.9× | 2.1% | PASS |
| file_reads | `oltp_read_only` | 239.23ms | 135.70ms | 0.6× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 313.56ms | 372.09ms | 1.2× | 10.8% | PASS |
| file_writes | `oltp_insert` | 35.87ms | 57.58ms | 1.6× | 10.5% | PASS |
| file_writes | `oltp_update_index` | 210.19ms | 219.49ms | 1.0× | 8.6% | PASS |
| file_writes | `oltp_update_non_index` | 193.17ms | 137.83ms | 0.7× | 30.3% | PASS |
| file_writes | `oltp_delete_insert` | 227.54ms | 160.62ms | 0.7× | 15.5% | PASS |
| file_writes | `oltp_write_only` | 147.28ms | 119.50ms | 0.8× | 18.4% | PASS |
| file_writes | `types_delete_insert` | 184.45ms | 107.93ms | 0.6× | 20.1% | PASS |
| file_writes | `oltp_read_write` | 232.23ms | 175.91ms | 0.8× | 15.3% | PASS |
| ac_reads | `oltp_point_select` | 54.18ms | 46.10ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 17.22ms | 12.72ms | 0.7× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 15.89ms | 12.63ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.27ms | 2.82ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.08ms | 3.73ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 6.13ms | 6.86ms | 1.1× | 1.0% | PASS |
| ac_reads | `select_random_points` | 22.41ms | 18.79ms | 0.8× | 3.1% | PASS |
| ac_reads | `select_random_ranges` | 5.64ms | 6.13ms | 1.1× | 0.7% | PASS |
| ac_reads | `covering_index_scan` | 6.28ms | 5.84ms | 0.9× | 0.8% | PASS |
| ac_reads | `groupby_scan` | 29.68ms | 29.18ms | 1.0× | 1.2% | PASS |
| ac_reads | `index_join` | 11.51ms | 8.99ms | 0.8× | 1.2% | PASS |
| ac_reads | `index_join_scan` | 3.67ms | 5.08ms | 1.4× | 1.5% | PASS |
| ac_reads | `types_table_scan` | 946.07ms | 972.51ms | 1.0× | 2.7% | PASS |
| ac_reads | `table_scan` | 1.17s | 1.10s | 0.9× | 3.2% | PASS |
| ac_reads | `oltp_read_only` | 156.67ms | 133.00ms | 0.8× | 2.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 32.75ms | 82.98ms | 2.5× | 55.6% | PASS |
| ac_writes | `oltp_insert_ac` | 29.70ms | 113.70ms | 3.8× | 40.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 39.37ms | 145.52ms | 3.7× | 53.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.56ms | 117.38ms | 5.4× | 31.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 33.32ms | 118.92ms | 3.6× | 70.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.07ms | 106.90ms | 3.8× | 50.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.14ms | 72.97ms | 3.5× | 44.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 35.52ms | 122.81ms | 3.5× | 57.4% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.15ms | 37.14ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 15.05ms | 13.81ms | 0.9× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 14.79ms | 13.31ms | 0.9× | 3.6% | PASS |
| mem_reads | `oltp_order_range` | 3.19ms | 3.09ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 4.32ms | 4.25ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.95ms | 6.07ms | 1.5× | 1.8% | PASS |
| mem_reads | `select_random_points` | 21.72ms | 20.46ms | 0.9× | 4.6% | PASS |
| mem_reads | `select_random_ranges` | 3.37ms | 5.20ms | 1.5× | 3.3% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.41ms | 1.1× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 34.02ms | 34.33ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 10.19ms | 8.93ms | 0.9× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.64ms | 5.23ms | 1.4× | 3.4% | PASS |
| mem_reads | `types_table_scan` | 1.08s | 1.17s | 1.1× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.21s | 1.28s | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_read_only` | 136.35ms | 134.60ms | 1.0× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 241.12ms | 363.66ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 18.41ms | 38.02ms | 2.1× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 63.17ms | 134.83ms | 2.1× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 47.87ms | 82.57ms | 1.7× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 52.24ms | 101.29ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 27.62ms | 60.63ms | 2.2× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 38.02ms | 52.43ms | 1.4× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 95.93ms | 143.22ms | 1.5× | 1.2% | PASS |
| file_reads | `oltp_point_select` | 109.43ms | 57.61ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 24.71ms | 16.55ms | 0.7× | 3.0% | PASS |
| file_reads | `oltp_sum_range` | 23.72ms | 16.15ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 4.41ms | 3.62ms | 0.8× | 3.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.51ms | 4.73ms | 0.9× | 2.4% | PASS |
| file_reads | `oltp_index_scan` | 11.53ms | 8.53ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 30.43ms | 23.95ms | 0.8× | 2.4% | PASS |
| file_reads | `select_random_ranges` | 10.90ms | 7.37ms | 0.7× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 11.71ms | 6.91ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 35.69ms | 35.23ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 14.59ms | 11.06ms | 0.8× | 2.5% | PASS |
| file_reads | `index_join_scan` | 4.78ms | 5.93ms | 1.2× | 3.5% | PASS |
| file_reads | `types_table_scan` | 1.08s | 1.17s | 1.1× | 1.1% | PASS |
| file_reads | `table_scan` | 1.21s | 1.27s | 1.1× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 252.21ms | 166.65ms | 0.7× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 264.58ms | 376.59ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.25ms | 45.87ms | 1.7× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 99.37ms | 155.50ms | 1.6× | 2.7% | PASS |
| file_writes | `oltp_update_non_index` | 91.51ms | 98.00ms | 1.1× | 9.1% | PASS |
| file_writes | `oltp_delete_insert` | 88.79ms | 117.05ms | 1.3× | 1.9% | PASS |
| file_writes | `oltp_write_only` | 59.76ms | 72.32ms | 1.2× | 4.8% | PASS |
| file_writes | `types_delete_insert` | 63.84ms | 62.37ms | 1.0× | 2.6% | PASS |
| file_writes | `oltp_read_write` | 128.89ms | 155.96ms | 1.2× | 1.7% | PASS |
| ac_reads | `oltp_point_select` | 59.33ms | 57.87ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 19.65ms | 16.59ms | 0.8× | 2.2% | PASS |
| ac_reads | `oltp_sum_range` | 18.30ms | 16.18ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 4.02ms | 3.64ms | 0.9× | 5.7% | PASS |
| ac_reads | `oltp_distinct_range` | 5.00ms | 4.76ms | 1.0× | 3.6% | PASS |
| ac_reads | `oltp_index_scan` | 6.54ms | 8.52ms | 1.3× | 3.0% | PASS |
| ac_reads | `select_random_points` | 24.10ms | 24.18ms | 1.0× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 5.88ms | 7.38ms | 1.3× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 6.81ms | 6.86ms | 1.0× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 34.67ms | 34.96ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 11.59ms | 11.20ms | 1.0× | 3.0% | PASS |
| ac_reads | `index_join_scan` | 4.16ms | 6.00ms | 1.4× | 3.9% | PASS |
| ac_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 174.99ms | 166.26ms | 1.0× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 26.80ms | 73.48ms | 2.7× | 7.8% | PASS |
| ac_writes | `oltp_insert_ac` | 31.13ms | 95.93ms | 3.1× | 12.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 31.03ms | 99.33ms | 3.2× | 11.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.51ms | 77.56ms | 3.2× | 12.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.37ms | 88.82ms | 3.1× | 11.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.59ms | 94.31ms | 3.3× | 8.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 26.81ms | 83.20ms | 3.1× | 8.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 36.16ms | 99.08ms | 2.7× | 8.6% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 18.13ms | 21.90ms | 1.2× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 9.38ms | 10.60ms | 1.1× | 3.4% | PASS |
| mem_reads | `oltp_sum_range` | 8.51ms | 10.35ms | 1.2× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 1.91ms | 2.10ms | 1.1× | 2.5% | PASS |
| mem_reads | `oltp_distinct_range` | 2.45ms | 2.62ms | 1.1× | 3.4% | PASS |
| mem_reads | `oltp_index_scan` | 2.60ms | 3.42ms | 1.3× | 3.7% | PASS |
| mem_reads | `select_random_points` | 14.59ms | 17.90ms | 1.2× | 2.2% | PASS |
| mem_reads | `select_random_ranges` | 3.90ms | 4.86ms | 1.2× | 3.3% | PASS |
| mem_reads | `covering_index_scan` | 2.36ms | 2.39ms | 1.0× | 1.8% | PASS |
| mem_reads | `groupby_scan` | 19.80ms | 21.62ms | 1.1× | 2.2% | PASS |
| mem_reads | `index_join` | 4.60ms | 5.67ms | 1.2× | 2.5% | PASS |
| mem_reads | `index_join_scan` | 2.75ms | 4.35ms | 1.6× | 10.8% | PASS |
| mem_reads | `types_table_scan` | 677.48ms | 723.90ms | 1.1× | 1.3% | PASS |
| mem_reads | `table_scan` | 783.68ms | 818.60ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_read_only` | 77.07ms | 85.91ms | 1.1× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 146.70ms | 196.59ms | 1.3× | 2.0% | PASS |
| mem_writes | `oltp_insert` | 11.27ms | 20.27ms | 1.8× | 2.2% | PASS |
| mem_writes | `oltp_update_index` | 43.31ms | 80.93ms | 1.9× | 3.3% | PASS |
| mem_writes | `oltp_update_non_index` | 33.05ms | 50.40ms | 1.5× | 2.2% | PASS |
| mem_writes | `oltp_delete_insert` | 31.56ms | 60.33ms | 1.9× | 2.1% | PASS |
| mem_writes | `oltp_write_only` | 17.41ms | 36.67ms | 2.1× | 2.3% | PASS |
| mem_writes | `types_delete_insert` | 21.02ms | 32.53ms | 1.5× | 2.9% | PASS |
| mem_writes | `oltp_read_write` | 58.84ms | 91.23ms | 1.6× | 4.1% | PASS |
| file_reads | `oltp_point_select` | 77.90ms | 38.80ms | 0.5× | 2.7% | PASS |
| file_reads | `oltp_range_select` | 16.65ms | 12.40ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 15.85ms | 12.46ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_order_range` | 2.84ms | 2.33ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_distinct_range` | 3.38ms | 2.89ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 9.21ms | 5.71ms | 0.6× | 1.6% | PASS |
| file_reads | `select_random_points` | 23.26ms | 20.59ms | 0.9× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 10.34ms | 6.66ms | 0.6× | 1.6% | PASS |
| file_reads | `covering_index_scan` | 8.50ms | 4.44ms | 0.5× | 4.2% | PASS |
| file_reads | `groupby_scan` | 21.26ms | 21.48ms | 1.0× | 1.7% | PASS |
| file_reads | `index_join` | 8.36ms | 7.93ms | 0.9× | 3.5% | PASS |
| file_reads | `index_join_scan` | 3.54ms | 4.20ms | 1.2× | 6.3% | PASS |
| file_reads | `types_table_scan` | 727.25ms | 735.51ms | 1.0× | 4.2% | PASS |
| file_reads | `table_scan` | 838.03ms | 843.69ms | 1.0× | 2.7% | PASS |
| file_reads | `oltp_read_only` | 158.42ms | 106.41ms | 0.7× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 198.31ms | 249.55ms | 1.3× | 3.6% | PASS |
| file_writes | `oltp_insert` | 19.34ms | 31.71ms | 1.6× | 10.2% | PASS |
| file_writes | `oltp_update_index` | 148.13ms | 139.92ms | 0.9× | 6.8% | PASS |
| file_writes | `oltp_update_non_index` | 120.55ms | 99.30ms | 0.8× | 4.3% | PASS |
| file_writes | `oltp_delete_insert` | 120.88ms | 112.50ms | 0.9× | 7.7% | PASS |
| file_writes | `oltp_write_only` | 94.21ms | 86.40ms | 0.9× | 8.6% | PASS |
| file_writes | `types_delete_insert` | 79.88ms | 59.89ms | 0.7× | 12.4% | PASS |
| file_writes | `oltp_read_write` | 120.81ms | 129.21ms | 1.1× | 2.7% | PASS |
| ac_reads | `oltp_point_select` | 39.01ms | 38.45ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 13.10ms | 12.77ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 11.93ms | 12.60ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 2.40ms | 2.38ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_distinct_range` | 2.94ms | 2.93ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 4.79ms | 5.22ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_points` | 18.09ms | 19.80ms | 1.1× | 2.7% | PASS |
| ac_reads | `select_random_ranges` | 6.45ms | 6.51ms | 1.0× | 2.3% | PASS |
| ac_reads | `covering_index_scan` | 4.91ms | 4.68ms | 1.0× | 2.3% | PASS |
| ac_reads | `groupby_scan` | 22.57ms | 23.05ms | 1.0× | 1.5% | PASS |
| ac_reads | `index_join` | 7.48ms | 9.61ms | 1.3× | 4.7% | PASS |
| ac_reads | `index_join_scan` | 3.62ms | 5.37ms | 1.5× | 8.8% | PASS |
| ac_reads | `types_table_scan` | 853.58ms | 801.53ms | 0.9× | 2.9% | PASS |
| ac_reads | `table_scan` | 1.10s | 918.11ms | 0.8× | 2.5% | PASS |
| ac_reads | `oltp_read_only` | 132.72ms | 126.72ms | 1.0× | 2.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.76ms | 55.25ms | 2.5× | 5.5% | PASS |
| ac_writes | `oltp_insert_ac` | 24.05ms | 65.52ms | 2.7× | 4.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.59ms | 73.52ms | 2.9× | 3.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.64ms | 58.96ms | 2.9× | 6.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.45ms | 66.83ms | 3.0× | 4.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.65ms | 65.93ms | 2.9× | 5.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.72ms | 55.74ms | 2.6× | 6.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.65ms | 70.15ms | 2.7× | 5.2% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 46s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 65.03ms | 130.00ms | 50.0% | 0.7% | PASS |
| `status_dirty_many_tables` | 67.66ms | 130.00ms | 52.0% | 0.7% | PASS |
| `diff_regular_working_one_table` | 61.54ms | 120.00ms | 51.3% | 0.8% | PASS |
| `diff_regular_working_many_tables` | 70.96ms | 140.00ms | 50.7% | 0.6% | PASS |
| `diff_stat_working_many_tables` | 71.03ms | 140.00ms | 50.7% | 0.5% | PASS |
| `diff_schema_working_many_tables` | 71.93ms | 140.00ms | 51.4% | 0.6% | PASS |
| `branch_list_many_branches` | 17.82ms | 35.00ms | 50.9% | 1.7% | PASS |
| `branch_create_delete` | 20.15ms | 40.00ms | 50.4% | 2.3% | PASS |
| `checkout_branch_clean` | 88.34ms | 150.00ms | 58.9% | 2.9% | PASS |
| `merge_data_no_conflicts` | 30.93ms | 50.00ms | 61.9% | 2.0% | PASS |
| `merge_schema_no_conflicts` | 17.87ms | 35.00ms | 51.0% | 2.2% | PASS |
| `merge_data_conflicts` | 23.79ms | 180.00ms | 13.2% | 1.3% | PASS |
| `merge_data_conflicts_with_resolve` | 23.70ms | 180.00ms | 13.2% | 1.1% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
