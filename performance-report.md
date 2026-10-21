# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-21 16:09 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261012.334.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/39618011200)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.62s | 9.67s | 1.0× | 1.5% | **PASS** |
| Writes | 1.94s | 3.07s | 1.6× | 1.6% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.66s | 9.88s | 0.9× | 1.2% | **PASS** |
| Writes | 4.20s | 4.37s | 1.0× | 8.8% | **PASS** |
| Autocommit writes | 848.35ms | 2.64s | 3.1× | 19.8% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.57s | 2.63s | 1.0× | 1.5% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.60s | 2.41s | 0.9× | 1.8% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.30s | 2.33s | 1.0× | 2.6% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.15s | 2.30s | 1.1× | 1.0% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 461.73ms | 763.54ms | 1.7× | 1.8% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 524.66ms | 787.57ms | 1.5× | 1.9% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 488.86ms | 760.01ms | 1.6× | 2.3% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 468.37ms | 757.91ms | 1.6× | 0.8% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.83s | 2.70s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.85s | 2.44s | 0.9× | 1.4% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.49s | 2.35s | 0.9× | 1.2% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.50s | 2.39s | 1.0× | 0.8% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 615.61ms | 821.35ms | 1.3× | 1.8% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.39s | 1.29s | 0.9× | 10.3% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.17s | 1.17s | 1.0× | 13.6% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 1.02s | 1.09s | 1.1× | 3.1% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.55s | 2.66s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.59s | 2.42s | 0.9× | 1.6% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.22s | 2.31s | 1.0× | 1.0% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.25s | 2.35s | 1.0× | 0.9% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 202.60ms | 615.17ms | 3.0× | 7.2% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 214.97ms | 746.57ms | 3.5× | 40.4% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 218.18ms | 658.37ms | 3.0× | 32.3% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 212.60ms | 617.21ms | 2.9× | 17.3% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.89ms | 30.41ms | 1.2× | 2.9% | PASS |
| mem_reads | `oltp_range_select` | 10.35ms | 11.48ms | 1.1× | 4.4% | PASS |
| mem_reads | `oltp_sum_range` | 9.53ms | 11.21ms | 1.2× | 2.5% | PASS |
| mem_reads | `oltp_order_range` | 2.58ms | 2.80ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 3.69ms | 3.95ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_index_scan` | 3.84ms | 5.20ms | 1.4× | 1.0% | PASS |
| mem_reads | `select_random_points` | 10.31ms | 10.96ms | 1.1× | 4.2% | PASS |
| mem_reads | `select_random_ranges` | 3.15ms | 4.02ms | 1.3× | 1.7% | PASS |
| mem_reads | `covering_index_scan` | 4.31ms | 4.65ms | 1.1× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 31.29ms | 33.26ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 5.86ms | 8.42ms | 1.4× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.50ms | 4.63ms | 1.3× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 1.13s | 1.15s | 1.0× | 1.5% | PASS |
| mem_reads | `table_scan` | 1.22s | 1.23s | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 105.55ms | 119.10ms | 1.1× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 183.82ms | 278.52ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 15.63ms | 28.83ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_update_index` | 53.12ms | 104.29ms | 2.0× | 2.5% | PASS |
| mem_writes | `oltp_update_non_index` | 38.54ms | 64.95ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 48.11ms | 78.57ms | 1.6× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 23.30ms | 49.50ms | 2.1× | 2.1% | PASS |
| mem_writes | `types_delete_insert` | 25.72ms | 39.58ms | 1.5× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 73.50ms | 119.30ms | 1.6× | 2.5% | PASS |
| file_reads | `oltp_point_select` | 100.65ms | 50.50ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 18.94ms | 13.90ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 18.20ms | 13.74ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.70ms | 3.29ms | 0.9× | 2.6% | PASS |
| file_reads | `oltp_distinct_range` | 4.84ms | 4.43ms | 0.9× | 2.0% | PASS |
| file_reads | `oltp_index_scan` | 11.54ms | 7.52ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 18.12ms | 13.16ms | 0.7× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 10.29ms | 5.97ms | 0.6× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.91ms | 6.54ms | 0.5× | 1.5% | PASS |
| file_reads | `groupby_scan` | 32.08ms | 33.70ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 10.36ms | 10.03ms | 1.0× | 2.6% | PASS |
| file_reads | `index_join_scan` | 4.30ms | 4.86ms | 1.1× | 2.0% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.13s | 1.1× | 1.1% | PASS |
| file_reads | `table_scan` | 1.30s | 1.25s | 1.0× | 1.1% | PASS |
| file_reads | `oltp_read_only` | 217.06ms | 151.43ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 196.66ms | 286.54ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 22.48ms | 32.82ms | 1.5× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 81.25ms | 114.81ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 60.62ms | 73.23ms | 1.2× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 68.75ms | 84.34ms | 1.2× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 46.88ms | 57.75ms | 1.2× | 2.4% | PASS |
| file_writes | `types_delete_insert` | 41.57ms | 45.36ms | 1.1× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 97.39ms | 126.51ms | 1.3× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 50.67ms | 51.11ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 13.82ms | 13.97ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 12.99ms | 13.81ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.21ms | 3.31ms | 1.0× | 3.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.19ms | 4.33ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 7.06ms | 7.87ms | 1.1× | 1.6% | PASS |
| ac_reads | `select_random_points` | 13.95ms | 13.84ms | 1.0× | 3.1% | PASS |
| ac_reads | `select_random_ranges` | 5.70ms | 6.02ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.42ms | 6.71ms | 0.9× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 31.46ms | 33.53ms | 1.1× | 0.6% | PASS |
| ac_reads | `index_join` | 7.66ms | 9.92ms | 1.3× | 4.0% | PASS |
| ac_reads | `index_join_scan` | 3.79ms | 4.87ms | 1.3× | 2.2% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 1.19s | 1.22s | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 141.10ms | 149.49ms | 1.1× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.25ms | 62.63ms | 2.7× | 8.1% | PASS |
| ac_writes | `oltp_insert_ac` | 25.66ms | 77.14ms | 3.0× | 7.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.16ms | 89.49ms | 3.3× | 7.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.89ms | 71.66ms | 3.0× | 6.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.40ms | 79.38ms | 3.1× | 7.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.50ms | 79.93ms | 3.0× | 4.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.55ms | 69.98ms | 3.2× | 8.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.19ms | 84.96ms | 2.9× | 4.9% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.10ms | 28.99ms | 0.9× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 16.01ms | 11.18ms | 0.7× | 2.0% | PASS |
| mem_reads | `oltp_sum_range` | 14.09ms | 10.99ms | 0.8× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 2.94ms | 2.59ms | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.74ms | 3.47ms | 0.9× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.41ms | 5.03ms | 1.5× | 1.4% | PASS |
| mem_reads | `select_random_points` | 22.53ms | 17.05ms | 0.8× | 4.1% | PASS |
| mem_reads | `select_random_ranges` | 3.12ms | 4.35ms | 1.4× | 1.7% | PASS |
| mem_reads | `covering_index_scan` | 3.37ms | 3.99ms | 1.2× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 30.15ms | 29.26ms | 1.0× | 1.1% | PASS |
| mem_reads | `index_join` | 10.09ms | 7.85ms | 0.8× | 3.0% | PASS |
| mem_reads | `index_join_scan` | 3.50ms | 5.01ms | 1.4× | 3.6% | PASS |
| mem_reads | `types_table_scan` | 1.08s | 1.03s | 1.0× | 3.9% | PASS |
| mem_reads | `table_scan` | 1.25s | 1.14s | 0.9× | 1.8% | PASS |
| mem_reads | `oltp_read_only` | 129.23ms | 108.41ms | 0.8× | 2.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 190.97ms | 276.43ms | 1.4× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 14.77ms | 29.52ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 63.71ms | 121.80ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 48.27ms | 68.09ms | 1.4× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 49.23ms | 83.65ms | 1.7× | 2.3% | PASS |
| mem_writes | `oltp_write_only` | 25.96ms | 49.40ms | 1.9× | 2.5% | PASS |
| mem_writes | `types_delete_insert` | 35.88ms | 40.77ms | 1.1× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 95.88ms | 117.91ms | 1.2× | 2.2% | PASS |
| file_reads | `oltp_point_select` | 110.73ms | 48.16ms | 0.4× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 24.63ms | 13.43ms | 0.5× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 22.43ms | 13.29ms | 0.6× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 3.81ms | 2.85ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 4.66ms | 3.76ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 11.22ms | 7.06ms | 0.6× | 1.1% | PASS |
| file_reads | `select_random_points` | 30.07ms | 19.35ms | 0.6× | 3.4% | PASS |
| file_reads | `select_random_ranges` | 10.67ms | 6.20ms | 0.6× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 11.35ms | 5.95ms | 0.5× | 0.6% | PASS |
| file_reads | `groupby_scan` | 30.78ms | 29.71ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join` | 14.64ms | 9.14ms | 0.6× | 2.7% | PASS |
| file_reads | `index_join_scan` | 4.20ms | 5.21ms | 1.2× | 1.9% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.01s | 0.9× | 5.1% | PASS |
| file_reads | `table_scan` | 1.26s | 1.13s | 0.9× | 2.3% | PASS |
| file_reads | `oltp_read_only` | 233.91ms | 133.17ms | 0.6× | 1.4% | PASS |
| file_writes | `oltp_bulk_insert` | 279.98ms | 356.67ms | 1.3× | 8.9% | PASS |
| file_writes | `oltp_insert` | 35.81ms | 57.30ms | 1.6× | 11.1% | PASS |
| file_writes | `oltp_update_index` | 211.33ms | 220.67ms | 1.0× | 19.6% | PASS |
| file_writes | `oltp_update_non_index` | 161.22ms | 131.36ms | 0.8× | 9.5% | PASS |
| file_writes | `oltp_delete_insert` | 197.85ms | 155.73ms | 0.8× | 13.4% | PASS |
| file_writes | `oltp_write_only` | 143.15ms | 105.53ms | 0.7× | 8.9% | PASS |
| file_writes | `types_delete_insert` | 159.68ms | 86.85ms | 0.5× | 15.6% | PASS |
| file_writes | `oltp_read_write` | 203.72ms | 173.79ms | 0.9× | 8.7% | PASS |
| ac_reads | `oltp_point_select` | 57.80ms | 47.59ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_range_select` | 18.48ms | 13.14ms | 0.7× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 16.52ms | 13.01ms | 0.8× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.35ms | 2.84ms | 0.8× | 1.1% | PASS |
| ac_reads | `oltp_distinct_range` | 4.15ms | 3.75ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 6.22ms | 7.00ms | 1.1× | 1.0% | PASS |
| ac_reads | `select_random_points` | 23.19ms | 19.16ms | 0.8× | 2.2% | PASS |
| ac_reads | `select_random_ranges` | 5.69ms | 6.18ms | 1.1× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 6.31ms | 5.95ms | 0.9× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 30.03ms | 29.61ms | 1.0× | 1.1% | PASS |
| ac_reads | `index_join` | 11.82ms | 9.23ms | 0.8× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 3.77ms | 5.30ms | 1.4× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 1.03s | 1.00s | 1.0× | 4.2% | PASS |
| ac_reads | `table_scan` | 1.22s | 1.12s | 0.9× | 3.9% | PASS |
| ac_reads | `oltp_read_only` | 155.16ms | 132.04ms | 0.9× | 1.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 30.27ms | 122.67ms | 4.1× | 75.3% | PASS |
| ac_writes | `oltp_insert_ac` | 27.02ms | 98.20ms | 3.6× | 59.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.46ms | 97.60ms | 3.3× | 34.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.18ms | 86.82ms | 3.7× | 43.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.20ms | 93.00ms | 4.0× | 27.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.52ms | 85.42ms | 3.3× | 25.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 26.40ms | 80.81ms | 3.1× | 36.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.92ms | 82.05ms | 2.7× | 56.1% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.92ms | 28.30ms | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_range_select` | 13.86ms | 10.89ms | 0.8× | 3.8% | PASS |
| mem_reads | `oltp_sum_range` | 12.68ms | 10.57ms | 0.8× | 2.6% | PASS |
| mem_reads | `oltp_order_range` | 2.81ms | 2.57ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.60ms | 3.44ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.27ms | 4.87ms | 1.5× | 1.6% | PASS |
| mem_reads | `select_random_points` | 17.38ms | 15.80ms | 0.9× | 3.2% | PASS |
| mem_reads | `select_random_ranges` | 2.79ms | 4.28ms | 1.5× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 3.31ms | 3.66ms | 1.1× | 3.1% | PASS |
| mem_reads | `groupby_scan` | 28.63ms | 28.60ms | 1.0× | 1.2% | PASS |
| mem_reads | `index_join` | 8.83ms | 7.46ms | 0.8× | 3.2% | PASS |
| mem_reads | `index_join_scan` | 3.21ms | 4.91ms | 1.5× | 3.3% | PASS |
| mem_reads | `types_table_scan` | 960.23ms | 997.98ms | 1.0× | 3.7% | PASS |
| mem_reads | `table_scan` | 1.09s | 1.10s | 1.0× | 2.5% | PASS |
| mem_reads | `oltp_read_only` | 117.22ms | 106.38ms | 0.9× | 4.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 189.42ms | 273.17ms | 1.4× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 14.74ms | 28.84ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_update_index` | 54.59ms | 109.69ms | 2.0× | 2.4% | PASS |
| mem_writes | `oltp_update_non_index` | 40.97ms | 63.67ms | 1.6× | 2.6% | PASS |
| mem_writes | `oltp_delete_insert` | 42.98ms | 79.13ms | 1.8× | 2.3% | PASS |
| mem_writes | `oltp_write_only` | 22.53ms | 45.96ms | 2.0× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 33.16ms | 41.15ms | 1.2× | 2.3% | PASS |
| mem_writes | `oltp_read_write` | 90.48ms | 118.39ms | 1.3× | 2.3% | PASS |
| file_reads | `oltp_point_select` | 108.17ms | 48.44ms | 0.4× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 23.64ms | 13.33ms | 0.6× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 21.82ms | 12.99ms | 0.6× | 2.0% | PASS |
| file_reads | `oltp_order_range` | 3.67ms | 2.82ms | 0.8× | 0.8% | PASS |
| file_reads | `oltp_distinct_range` | 4.43ms | 3.68ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 10.99ms | 6.86ms | 0.6× | 0.7% | PASS |
| file_reads | `select_random_points` | 25.09ms | 17.90ms | 0.7× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 10.36ms | 6.13ms | 0.6× | 0.5% | PASS |
| file_reads | `covering_index_scan` | 11.20ms | 5.79ms | 0.5× | 0.7% | PASS |
| file_reads | `groupby_scan` | 29.48ms | 28.85ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join` | 13.21ms | 8.84ms | 0.7× | 2.3% | PASS |
| file_reads | `index_join_scan` | 4.05ms | 5.03ms | 1.2× | 1.7% | PASS |
| file_reads | `types_table_scan` | 998.77ms | 1.01s | 1.0× | 2.8% | PASS |
| file_reads | `table_scan` | 1.00s | 1.05s | 1.1× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 217.47ms | 129.47ms | 0.6× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 251.63ms | 329.56ms | 1.3× | 3.8% | PASS |
| file_writes | `oltp_insert` | 28.91ms | 50.78ms | 1.8× | 18.6% | PASS |
| file_writes | `oltp_update_index` | 170.29ms | 202.81ms | 1.2× | 24.8% | PASS |
| file_writes | `oltp_update_non_index` | 136.21ms | 120.00ms | 0.9× | 10.4% | PASS |
| file_writes | `oltp_delete_insert` | 162.23ms | 139.19ms | 0.9× | 10.7% | PASS |
| file_writes | `oltp_write_only` | 117.48ms | 90.28ms | 0.8× | 16.5% | PASS |
| file_writes | `types_delete_insert` | 131.31ms | 79.84ms | 0.6× | 16.5% | PASS |
| file_writes | `oltp_read_write` | 170.33ms | 159.59ms | 0.9× | 9.2% | PASS |
| ac_reads | `oltp_point_select` | 52.52ms | 46.29ms | 0.9× | 0.6% | PASS |
| ac_reads | `oltp_range_select` | 17.20ms | 12.91ms | 0.8× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 15.65ms | 12.57ms | 0.8× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 3.16ms | 2.81ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 3.91ms | 3.68ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 6.08ms | 6.88ms | 1.1× | 0.6% | PASS |
| ac_reads | `select_random_points` | 19.96ms | 17.98ms | 0.9× | 0.9% | PASS |
| ac_reads | `select_random_ranges` | 5.40ms | 6.12ms | 1.1× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 6.28ms | 5.83ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 29.51ms | 29.08ms | 1.0× | 1.1% | PASS |
| ac_reads | `index_join` | 10.71ms | 8.84ms | 0.8× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.61ms | 5.00ms | 1.4× | 1.0% | PASS |
| ac_reads | `types_table_scan` | 880.29ms | 950.55ms | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.02s | 1.07s | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_read_only` | 146.82ms | 131.87ms | 0.9× | 2.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.92ms | 56.67ms | 2.3× | 14.5% | PASS |
| ac_writes | `oltp_insert_ac` | 27.92ms | 71.92ms | 2.6× | 23.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 30.88ms | 103.88ms | 3.4× | 41.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.35ms | 76.87ms | 3.2× | 58.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.53ms | 109.93ms | 3.9× | 47.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.82ms | 77.72ms | 2.8× | 22.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.71ms | 65.36ms | 3.2× | 23.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.05ms | 96.02ms | 2.9× | 41.4% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 26.23ms | 29.86ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_range_select` | 15.44ms | 16.29ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_sum_range` | 14.08ms | 15.69ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 2.94ms | 3.09ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.77ms | 3.98ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.63ms | 4.47ms | 1.2× | 1.4% | PASS |
| mem_reads | `select_random_points` | 21.53ms | 24.17ms | 1.1× | 0.7% | PASS |
| mem_reads | `select_random_ranges` | 5.82ms | 6.55ms | 1.1× | 0.8% | PASS |
| mem_reads | `covering_index_scan` | 3.33ms | 3.31ms | 1.0× | 1.5% | PASS |
| mem_reads | `groupby_scan` | 30.40ms | 32.54ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 6.22ms | 7.93ms | 1.3× | 0.8% | PASS |
| mem_reads | `index_join_scan` | 3.25ms | 4.77ms | 1.5× | 1.1% | PASS |
| mem_reads | `types_table_scan` | 881.07ms | 963.85ms | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.02s | 1.06s | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 116.74ms | 128.92ms | 1.1× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 194.82ms | 267.71ms | 1.4× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 15.07ms | 27.99ms | 1.9× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 53.34ms | 103.76ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 41.14ms | 67.18ms | 1.6× | 0.7% | PASS |
| mem_writes | `oltp_delete_insert` | 39.61ms | 77.80ms | 2.0× | 0.7% | PASS |
| mem_writes | `oltp_write_only` | 21.85ms | 49.11ms | 2.2× | 0.7% | PASS |
| mem_writes | `types_delete_insert` | 25.66ms | 41.37ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_read_write` | 76.86ms | 123.00ms | 1.6× | 0.9% | PASS |
| file_reads | `oltp_point_select` | 95.20ms | 46.43ms | 0.5× | 0.6% | PASS |
| file_reads | `oltp_range_select` | 23.37ms | 18.09ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_sum_range` | 21.88ms | 17.52ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_order_range` | 3.80ms | 3.32ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.67ms | 4.22ms | 0.9× | 0.5% | PASS |
| file_reads | `oltp_index_scan` | 10.98ms | 6.68ms | 0.6× | 0.8% | PASS |
| file_reads | `select_random_points` | 29.98ms | 26.78ms | 0.9× | 0.7% | PASS |
| file_reads | `select_random_ranges` | 13.21ms | 8.46ms | 0.6× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 10.71ms | 5.52ms | 0.5× | 0.7% | PASS |
| file_reads | `groupby_scan` | 31.62ms | 33.03ms | 1.0× | 1.2% | PASS |
| file_reads | `index_join` | 10.31ms | 9.67ms | 0.9× | 0.8% | PASS |
| file_reads | `index_join_scan` | 4.21ms | 5.04ms | 1.2× | 1.4% | PASS |
| file_reads | `types_table_scan` | 898.73ms | 972.23ms | 1.1× | 1.2% | PASS |
| file_reads | `table_scan` | 1.12s | 1.08s | 1.0× | 3.1% | PASS |
| file_reads | `oltp_read_only` | 220.94ms | 153.97ms | 0.7× | 0.6% | PASS |
| file_writes | `oltp_bulk_insert` | 244.91ms | 317.28ms | 1.3× | 3.2% | PASS |
| file_writes | `oltp_insert` | 29.40ms | 43.15ms | 1.5× | 15.2% | PASS |
| file_writes | `oltp_update_index` | 156.80ms | 164.99ms | 1.1× | 3.1% | PASS |
| file_writes | `oltp_update_non_index` | 136.33ms | 114.62ms | 0.8× | 0.7% | PASS |
| file_writes | `oltp_delete_insert` | 131.41ms | 120.47ms | 0.9× | 1.3% | PASS |
| file_writes | `oltp_write_only` | 91.31ms | 89.62ms | 1.0× | 0.8% | PASS |
| file_writes | `types_delete_insert` | 80.67ms | 70.22ms | 0.9× | 11.5% | PASS |
| file_writes | `oltp_read_write` | 153.15ms | 170.57ms | 1.1× | 5.4% | PASS |
| ac_reads | `oltp_point_select` | 50.63ms | 47.40ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 19.24ms | 18.19ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 17.24ms | 17.60ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_order_range` | 3.36ms | 3.31ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.18ms | 4.22ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 6.37ms | 6.62ms | 1.0× | 0.9% | PASS |
| ac_reads | `select_random_points` | 24.61ms | 26.07ms | 1.1× | 0.8% | PASS |
| ac_reads | `select_random_ranges` | 8.51ms | 8.41ms | 1.0× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 6.10ms | 5.46ms | 0.9× | 0.7% | PASS |
| ac_reads | `groupby_scan` | 30.71ms | 32.90ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 7.95ms | 9.66ms | 1.2× | 1.0% | PASS |
| ac_reads | `index_join_scan` | 3.73ms | 4.98ms | 1.3× | 0.9% | PASS |
| ac_reads | `types_table_scan` | 888.28ms | 960.15ms | 1.1× | 0.4% | PASS |
| ac_reads | `table_scan` | 1.03s | 1.06s | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 151.80ms | 152.04ms | 1.0× | 0.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.72ms | 55.77ms | 2.7× | 14.5% | PASS |
| ac_writes | `oltp_insert_ac` | 25.09ms | 73.89ms | 2.9× | 18.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.68ms | 84.88ms | 3.2× | 17.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.33ms | 64.03ms | 3.1× | 14.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 40.73ms | 110.25ms | 2.7× | 18.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.38ms | 80.95ms | 3.2× | 15.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.90ms | 65.15ms | 2.6× | 21.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.77ms | 82.29ms | 2.9× | 17.3% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 2s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 72.14ms | 130.00ms | 55.5% | 0.5% | PASS |
| `status_dirty_many_tables` | 75.05ms | 130.00ms | 57.7% | 0.3% | PASS |
| `diff_regular_working_one_table` | 67.63ms | 120.00ms | 56.4% | 0.7% | PASS |
| `diff_regular_working_many_tables` | 77.97ms | 140.00ms | 55.7% | 0.3% | PASS |
| `diff_stat_working_many_tables` | 77.98ms | 140.00ms | 55.7% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 78.59ms | 140.00ms | 56.1% | 0.2% | PASS |
| `branch_list_many_branches` | 18.72ms | 35.00ms | 53.5% | 0.9% | PASS |
| `branch_create_delete` | 26.73ms | 40.00ms | 66.8% | 7.4% | PASS |
| `checkout_branch_clean` | 118.73ms | 150.00ms | 79.2% | 17.3% | PASS |
| `merge_data_no_conflicts` | 30.93ms | 50.00ms | 61.9% | 3.3% | PASS |
| `merge_schema_no_conflicts` | 18.83ms | 35.00ms | 53.8% | 1.7% | PASS |
| `merge_data_conflicts` | 26.69ms | 180.00ms | 14.8% | 1.1% | PASS |
| `merge_data_conflicts_with_resolve` | 25.88ms | 180.00ms | 14.4% | 0.4% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
