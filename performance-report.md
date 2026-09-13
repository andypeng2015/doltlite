# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-13 15:24 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/34761002175)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.83s | 10.46s | 1.1× | 1.5% | **PASS** |
| Writes | 2.09s | 3.43s | 1.6× | 1.2% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.85s | 10.72s | 1.0× | 1.4% | **PASS** |
| Writes | 3.44s | 4.12s | 1.2× | 2.0% | **PASS** |
| Autocommit writes | 872.59ms | 2.89s | 3.3× | 6.4% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.46s | 2.59s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.59s | 2.74s | 1.1× | 1.6% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.55s | 2.75s | 1.1× | 1.1% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.22s | 2.37s | 1.1× | 2.3% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 442.18ms | 729.57ms | 1.6× | 1.3% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 591.27ms | 998.08ms | 1.7× | 1.3% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 583.79ms | 988.62ms | 1.7× | 1.0% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 475.64ms | 716.41ms | 1.5× | 1.2% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.74s | 2.67s | 1.0× | 1.3% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.85s | 2.82s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.81s | 2.82s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.45s | 2.41s | 1.0× | 1.9% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 610.58ms | 805.76ms | 1.3× | 1.6% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 934.18ms | 1.11s | 1.2× | 4.8% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 826.85ms | 1.09s | 1.3× | 1.6% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 1.07s | 1.12s | 1.0× | 4.7% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.54s | 2.66s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.70s | 2.82s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.67s | 2.83s | 1.1× | 1.1% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.32s | 2.43s | 1.0× | 1.7% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 178.15ms | 672.33ms | 3.8× | 5.5% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 220.43ms | 758.37ms | 3.4× | 6.4% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 216.65ms | 742.38ms | 3.4× | 6.7% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 257.35ms | 716.91ms | 2.8× | 10.3% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.13ms | 30.04ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 10.02ms | 11.13ms | 1.1× | 2.5% | PASS |
| mem_reads | `oltp_sum_range` | 9.40ms | 11.13ms | 1.2× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 2.56ms | 2.80ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 3.64ms | 3.96ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 3.81ms | 5.19ms | 1.4× | 1.4% | PASS |
| mem_reads | `select_random_points` | 9.86ms | 10.91ms | 1.1× | 3.5% | PASS |
| mem_reads | `select_random_ranges` | 2.97ms | 3.97ms | 1.3× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 4.20ms | 4.27ms | 1.0× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 30.27ms | 32.88ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 5.79ms | 7.88ms | 1.4× | 1.0% | PASS |
| mem_reads | `index_join_scan` | 3.19ms | 4.46ms | 1.4× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.12s | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 1.19s | 1.22s | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 102.44ms | 117.62ms | 1.1× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 183.66ms | 275.29ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 15.39ms | 28.14ms | 1.8× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 49.57ms | 94.79ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 34.25ms | 59.52ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 44.67ms | 73.30ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 21.66ms | 46.66ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 24.75ms | 38.62ms | 1.6× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 68.23ms | 113.25ms | 1.7× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 97.71ms | 49.48ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 17.77ms | 13.23ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 17.22ms | 13.34ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 3.38ms | 3.09ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_distinct_range` | 4.50ms | 4.25ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 11.53ms | 7.56ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 17.73ms | 13.15ms | 0.7× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 10.33ms | 5.96ms | 0.6× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.80ms | 6.56ms | 0.6× | 0.9% | PASS |
| file_reads | `groupby_scan` | 31.41ms | 33.34ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 10.07ms | 9.69ms | 1.0× | 1.9% | PASS |
| file_reads | `index_join_scan` | 4.20ms | 4.98ms | 1.2× | 1.8% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 1.23s | 1.23s | 1.0× | 1.6% | PASS |
| file_reads | `oltp_read_only` | 212.00ms | 147.49ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 197.55ms | 285.93ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 22.33ms | 32.49ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 76.64ms | 106.23ms | 1.4× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 60.40ms | 72.06ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 72.64ms | 87.11ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 46.13ms | 55.68ms | 1.2× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 40.73ms | 45.20ms | 1.1× | 1.3% | PASS |
| file_writes | `oltp_read_write` | 94.17ms | 121.06ms | 1.3× | 1.8% | PASS |
| ac_reads | `oltp_point_select` | 48.20ms | 49.89ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 13.03ms | 13.37ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 12.34ms | 13.50ms | 1.1× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 3.08ms | 3.23ms | 1.1× | 2.6% | PASS |
| ac_reads | `oltp_distinct_range` | 4.10ms | 4.40ms | 1.1× | 1.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.44ms | 7.54ms | 1.2× | 1.2% | PASS |
| ac_reads | `select_random_points` | 12.66ms | 13.37ms | 1.1× | 1.8% | PASS |
| ac_reads | `select_random_ranges` | 5.49ms | 5.95ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 6.90ms | 6.54ms | 0.9× | 2.5% | PASS |
| ac_reads | `groupby_scan` | 31.01ms | 33.52ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 7.67ms | 9.88ms | 1.3× | 1.6% | PASS |
| ac_reads | `index_join_scan` | 3.67ms | 5.02ms | 1.4× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.12s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.22s | 1.0× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 139.86ms | 147.32ms | 1.1× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.38ms | 70.13ms | 3.4× | 5.9% | PASS |
| ac_writes | `oltp_insert_ac` | 22.01ms | 81.98ms | 3.7× | 7.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.09ms | 94.37ms | 3.9× | 4.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.39ms | 77.54ms | 3.8× | 5.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.10ms | 87.23ms | 3.9× | 5.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.18ms | 86.68ms | 3.9× | 4.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.18ms | 80.29ms | 4.0× | 8.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 26.83ms | 94.12ms | 3.5× | 5.4% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.83ms | 37.90ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 16.28ms | 14.40ms | 0.9× | 2.6% | PASS |
| mem_reads | `oltp_sum_range` | 15.03ms | 13.61ms | 0.9× | 2.5% | PASS |
| mem_reads | `oltp_order_range` | 3.20ms | 3.12ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_distinct_range` | 4.32ms | 4.27ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_index_scan` | 3.91ms | 5.99ms | 1.5× | 1.5% | PASS |
| mem_reads | `select_random_points` | 21.23ms | 20.80ms | 1.0× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 3.48ms | 5.27ms | 1.5× | 2.0% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.48ms | 1.1× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 34.75ms | 34.57ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 10.28ms | 8.87ms | 0.9× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 3.72ms | 5.45ms | 1.5× | 2.9% | PASS |
| mem_reads | `types_table_scan` | 1.09s | 1.17s | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.22s | 1.27s | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 135.13ms | 136.78ms | 1.0× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 244.35ms | 371.36ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 18.17ms | 38.53ms | 2.1× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 64.95ms | 140.40ms | 2.2× | 2.0% | PASS |
| mem_writes | `oltp_update_non_index` | 48.23ms | 85.48ms | 1.8× | 1.3% | PASS |
| mem_writes | `oltp_delete_insert` | 53.91ms | 103.23ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 27.54ms | 61.02ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 38.64ms | 53.41ms | 1.4× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 95.48ms | 144.66ms | 1.5× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 110.96ms | 59.19ms | 0.5× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 24.29ms | 17.14ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 23.56ms | 16.30ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 4.11ms | 3.45ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.30ms | 4.65ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 11.68ms | 8.58ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 31.16ms | 25.06ms | 0.8× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 11.18ms | 7.43ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 11.94ms | 6.98ms | 0.6× | 1.3% | PASS |
| file_reads | `groupby_scan` | 35.66ms | 35.18ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 14.87ms | 11.43ms | 0.8× | 2.7% | PASS |
| file_reads | `index_join_scan` | 4.85ms | 6.05ms | 1.2× | 4.5% | PASS |
| file_reads | `types_table_scan` | 1.08s | 1.17s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.22s | 1.28s | 1.0× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 249.99ms | 170.20ms | 0.7× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 269.14ms | 385.69ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 26.20ms | 45.76ms | 1.7× | 2.4% | PASS |
| file_writes | `oltp_update_index` | 135.56ms | 160.79ms | 1.2× | 8.7% | PASS |
| file_writes | `oltp_update_non_index` | 100.98ms | 100.97ms | 1.0× | 16.4% | PASS |
| file_writes | `oltp_delete_insert` | 96.71ms | 118.88ms | 1.2× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 88.95ms | 74.06ms | 0.8× | 6.8% | PASS |
| file_writes | `types_delete_insert` | 72.75ms | 63.14ms | 0.9× | 2.7% | PASS |
| file_writes | `oltp_read_write` | 143.89ms | 159.00ms | 1.1× | 7.8% | PASS |
| ac_reads | `oltp_point_select` | 60.97ms | 59.09ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 19.64ms | 17.14ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 18.45ms | 16.53ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 3.65ms | 3.44ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 4.73ms | 4.63ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_index_scan` | 6.63ms | 8.62ms | 1.3× | 1.7% | PASS |
| ac_reads | `select_random_points` | 25.61ms | 24.75ms | 1.0× | 2.4% | PASS |
| ac_reads | `select_random_ranges` | 6.19ms | 7.42ms | 1.2× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 6.96ms | 6.95ms | 1.0× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 35.10ms | 35.10ms | 1.0× | 1.1% | PASS |
| ac_reads | `index_join` | 12.28ms | 11.34ms | 0.9× | 2.1% | PASS |
| ac_reads | `index_join_scan` | 4.31ms | 6.00ms | 1.4× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 1.09s | 1.18s | 1.1× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.22s | 1.27s | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 175.48ms | 168.93ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 25.59ms | 78.45ms | 3.1× | 6.3% | PASS |
| ac_writes | `oltp_insert_ac` | 28.85ms | 92.99ms | 3.2× | 6.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.29ms | 105.64ms | 3.6× | 7.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.46ms | 90.57ms | 3.7× | 6.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.95ms | 98.04ms | 3.6× | 6.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.20ms | 96.90ms | 3.7× | 5.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.68ms | 92.66ms | 3.6× | 7.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.41ms | 103.12ms | 3.1× | 3.7% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.17ms | 38.08ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 15.49ms | 14.47ms | 0.9× | 1.0% | PASS |
| mem_reads | `oltp_sum_range` | 14.67ms | 13.52ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_order_range` | 3.12ms | 3.13ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.25ms | 4.29ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.92ms | 6.09ms | 1.6× | 1.3% | PASS |
| mem_reads | `select_random_points` | 20.50ms | 20.85ms | 1.0× | 1.1% | PASS |
| mem_reads | `select_random_ranges` | 3.44ms | 5.31ms | 1.5× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 4.20ms | 4.59ms | 1.1× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 34.83ms | 34.45ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 9.82ms | 9.22ms | 0.9× | 1.4% | PASS |
| mem_reads | `index_join_scan` | 3.64ms | 5.54ms | 1.5× | 1.2% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 133.27ms | 137.34ms | 1.0× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 245.80ms | 368.89ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.59ms | 38.48ms | 2.1× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 62.61ms | 137.05ms | 2.2× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 46.59ms | 83.09ms | 1.8× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 51.42ms | 102.07ms | 2.0× | 0.7% | PASS |
| mem_writes | `oltp_write_only` | 27.19ms | 61.05ms | 2.2× | 0.8% | PASS |
| mem_writes | `types_delete_insert` | 37.44ms | 53.21ms | 1.4× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 94.14ms | 144.77ms | 1.5× | 1.5% | PASS |
| file_reads | `oltp_point_select` | 109.89ms | 58.50ms | 0.5× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 23.30ms | 16.98ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 22.63ms | 15.91ms | 0.7× | 1.7% | PASS |
| file_reads | `oltp_order_range` | 4.09ms | 3.44ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_distinct_range` | 5.26ms | 4.62ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 11.52ms | 8.55ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_points` | 29.74ms | 23.86ms | 0.8× | 1.7% | PASS |
| file_reads | `select_random_ranges` | 10.94ms | 7.40ms | 0.7× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 11.77ms | 6.92ms | 0.6× | 1.0% | PASS |
| file_reads | `groupby_scan` | 35.76ms | 34.90ms | 1.0× | 0.6% | PASS |
| file_reads | `index_join` | 14.14ms | 11.26ms | 0.8× | 2.8% | PASS |
| file_reads | `index_join_scan` | 4.65ms | 5.89ms | 1.3× | 1.4% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.21s | 1.29s | 1.1× | 1.1% | PASS |
| file_reads | `oltp_read_only` | 249.97ms | 169.21ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 267.76ms | 380.39ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 25.86ms | 45.82ms | 1.8× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 99.52ms | 157.37ms | 1.6× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 97.91ms | 97.61ms | 1.0× | 6.5% | PASS |
| file_writes | `oltp_delete_insert` | 86.29ms | 115.53ms | 1.3× | 1.3% | PASS |
| file_writes | `oltp_write_only` | 57.15ms | 72.09ms | 1.3× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 63.84ms | 62.39ms | 1.0× | 2.0% | PASS |
| file_writes | `oltp_read_write` | 128.52ms | 155.48ms | 1.2× | 1.7% | PASS |
| ac_reads | `oltp_point_select` | 59.95ms | 58.69ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 18.83ms | 16.83ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 18.07ms | 15.92ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 3.76ms | 3.40ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 4.81ms | 4.62ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 6.83ms | 8.53ms | 1.2× | 1.2% | PASS |
| ac_reads | `select_random_points` | 24.83ms | 23.87ms | 1.0× | 1.5% | PASS |
| ac_reads | `select_random_ranges` | 6.17ms | 7.39ms | 1.2× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 7.12ms | 6.94ms | 1.0× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 35.52ms | 34.99ms | 1.0× | 0.6% | PASS |
| ac_reads | `index_join` | 12.04ms | 11.15ms | 0.9× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 4.23ms | 5.89ms | 1.4× | 1.6% | PASS |
| ac_reads | `types_table_scan` | 1.08s | 1.18s | 1.1× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.29s | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 174.87ms | 169.56ms | 1.0× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 25.03ms | 76.75ms | 3.1× | 8.4% | PASS |
| ac_writes | `oltp_insert_ac` | 26.34ms | 94.55ms | 3.6× | 6.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.26ms | 103.70ms | 3.7× | 7.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.32ms | 86.58ms | 3.6× | 5.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.24ms | 95.69ms | 3.5× | 7.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.87ms | 93.75ms | 3.5× | 7.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.16ms | 90.06ms | 3.6× | 6.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.44ms | 101.29ms | 3.0× | 6.1% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 28.41ms | 32.02ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_range_select` | 18.14ms | 17.69ms | 1.0× | 3.7% | PASS |
| mem_reads | `oltp_sum_range` | 15.67ms | 16.84ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 3.22ms | 3.30ms | 1.0× | 2.5% | PASS |
| mem_reads | `oltp_distinct_range` | 4.03ms | 4.17ms | 1.0× | 2.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.99ms | 5.22ms | 1.3× | 3.3% | PASS |
| mem_reads | `select_random_points` | 24.32ms | 26.38ms | 1.1× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 6.54ms | 7.08ms | 1.1× | 2.0% | PASS |
| mem_reads | `covering_index_scan` | 3.49ms | 3.78ms | 1.1× | 2.5% | PASS |
| mem_reads | `groupby_scan` | 32.15ms | 34.41ms | 1.1× | 1.8% | PASS |
| mem_reads | `index_join` | 6.69ms | 8.75ms | 1.3× | 2.9% | PASS |
| mem_reads | `index_join_scan` | 3.42ms | 4.95ms | 1.4× | 2.9% | PASS |
| mem_reads | `types_table_scan` | 909.93ms | 994.41ms | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.04s | 1.08s | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 119.46ms | 131.78ms | 1.1× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 198.04ms | 260.80ms | 1.3× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 15.35ms | 26.04ms | 1.7× | 2.1% | PASS |
| mem_writes | `oltp_update_index` | 53.30ms | 95.62ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 41.49ms | 61.07ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 40.38ms | 71.75ms | 1.8× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 22.15ms | 43.94ms | 2.0× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 26.03ms | 38.71ms | 1.5× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 78.90ms | 118.49ms | 1.5× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 103.01ms | 49.80ms | 0.5× | 1.5% | PASS |
| file_reads | `oltp_range_select` | 24.25ms | 18.68ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 22.92ms | 18.17ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 3.84ms | 3.45ms | 0.9× | 2.7% | PASS |
| file_reads | `oltp_distinct_range` | 4.75ms | 4.39ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 11.74ms | 7.07ms | 0.6× | 1.7% | PASS |
| file_reads | `select_random_points` | 30.68ms | 27.42ms | 0.9× | 2.0% | PASS |
| file_reads | `select_random_ranges` | 14.02ms | 8.85ms | 0.6× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 11.46ms | 5.84ms | 0.5× | 2.2% | PASS |
| file_reads | `groupby_scan` | 31.96ms | 33.78ms | 1.1× | 1.9% | PASS |
| file_reads | `index_join` | 10.92ms | 10.20ms | 0.9× | 2.1% | PASS |
| file_reads | `index_join_scan` | 4.21ms | 5.25ms | 1.2× | 2.4% | PASS |
| file_reads | `types_table_scan` | 909.41ms | 985.62ms | 1.1× | 1.0% | PASS |
| file_reads | `table_scan` | 1.03s | 1.07s | 1.0× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 229.59ms | 157.48ms | 0.7× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 250.27ms | 319.95ms | 1.3× | 3.6% | PASS |
| file_writes | `oltp_insert` | 35.60ms | 45.41ms | 1.3× | 8.9% | PASS |
| file_writes | `oltp_update_index` | 167.24ms | 167.31ms | 1.0× | 5.2% | PASS |
| file_writes | `oltp_update_non_index` | 138.80ms | 116.92ms | 0.8× | 2.8% | PASS |
| file_writes | `oltp_delete_insert` | 135.02ms | 129.70ms | 1.0× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 97.14ms | 91.34ms | 0.9× | 4.2% | PASS |
| file_writes | `types_delete_insert` | 88.17ms | 81.33ms | 0.9× | 10.1% | PASS |
| file_writes | `oltp_read_write` | 156.96ms | 169.39ms | 1.1× | 6.8% | PASS |
| ac_reads | `oltp_point_select` | 53.85ms | 50.92ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_range_select` | 19.99ms | 19.17ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_sum_range` | 18.37ms | 18.49ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_order_range` | 3.54ms | 3.49ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 4.36ms | 4.37ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.88ms | 7.13ms | 1.0× | 1.8% | PASS |
| ac_reads | `select_random_points` | 25.68ms | 27.12ms | 1.1× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 9.08ms | 8.84ms | 1.0× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 6.52ms | 5.89ms | 0.9× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 32.00ms | 34.15ms | 1.1× | 1.9% | PASS |
| ac_reads | `index_join` | 8.40ms | 10.26ms | 1.2× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 3.87ms | 5.27ms | 1.4× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 913.34ms | 992.16ms | 1.1× | 1.2% | PASS |
| ac_reads | `table_scan` | 1.05s | 1.08s | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 164.16ms | 163.20ms | 1.0× | 2.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 32.00ms | 82.20ms | 2.6× | 22.1% | PASS |
| ac_writes | `oltp_insert_ac` | 30.52ms | 85.25ms | 2.8× | 6.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 33.00ms | 95.15ms | 2.9× | 5.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 29.25ms | 81.79ms | 2.8× | 9.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 31.43ms | 91.23ms | 2.9× | 6.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 32.40ms | 92.11ms | 2.8× | 14.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 28.63ms | 83.26ms | 2.9× | 11.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 40.11ms | 105.91ms | 2.6× | 18.3% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 16s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 97.42ms | 130.00ms | 74.9% | 0.9% | PASS |
| `status_dirty_many_tables` | 100.40ms | 130.00ms | 77.2% | 0.7% | PASS |
| `diff_regular_working_one_table` | 91.88ms | 120.00ms | 76.6% | 0.5% | PASS |
| `diff_regular_working_many_tables` | 106.09ms | 140.00ms | 75.8% | 0.7% | PASS |
| `diff_stat_working_many_tables` | 105.53ms | 140.00ms | 75.4% | 0.6% | PASS |
| `diff_schema_working_many_tables` | 106.06ms | 140.00ms | 75.8% | 0.5% | PASS |
| `branch_list_many_branches` | 24.13ms | 35.00ms | 68.9% | 1.4% | PASS |
| `branch_create_delete` | 27.04ms | 40.00ms | 67.6% | 1.6% | PASS |
| `checkout_branch_clean` | 62.89ms | 150.00ms | 41.9% | 1.0% | PASS |
| `merge_data_no_conflicts` | 30.77ms | 50.00ms | 61.5% | 1.6% | PASS |
| `merge_schema_no_conflicts` | 23.50ms | 35.00ms | 67.1% | 1.4% | PASS |
| `merge_data_conflicts` | 34.18ms | 180.00ms | 19.0% | 0.9% | PASS |
| `merge_data_conflicts_with_resolve` | 34.45ms | 180.00ms | 19.1% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
