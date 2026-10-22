# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-10-22 16:15 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261018.339.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/39797093540)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.11s | 10.50s | 1.0× | 1.3% | **PASS** |
| Writes | 2.07s | 3.46s | 1.7× | 1.2% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.10s | 10.74s | 1.0× | 1.3% | **PASS** |
| Writes | 3.51s | 4.16s | 1.2× | 2.3% | **PASS** |
| Autocommit writes | 888.76ms | 2.84s | 3.2× | 6.5% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.45s | 2.59s | 1.1× | 1.2% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.30s | 2.62s | 1.1× | 1.0% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.92s | 2.91s | 1.0× | 1.9% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.44s | 2.38s | 1.0× | 1.8% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 447.87ms | 774.93ms | 1.7× | 1.2% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 496.58ms | 845.55ms | 1.7× | 1.0% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 646.42ms | 1.11s | 1.7× | 1.5% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 479.93ms | 726.56ms | 1.5× | 1.4% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.69s | 2.66s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.40s | 2.65s | 1.1× | 1.2% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.15s | 2.96s | 0.9× | 1.9% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.86s | 2.47s | 0.9× | 1.0% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 603.04ms | 843.81ms | 1.4× | 1.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 752.48ms | 937.75ms | 1.2× | 3.7% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 863.52ms | 1.19s | 1.4× | 2.2% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 1.29s | 1.19s | 0.9× | 24.5% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.53s | 2.65s | 1.0× | 1.1% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.33s | 2.65s | 1.1× | 1.2% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.89s | 2.90s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.46s | 2.43s | 1.0× | 1.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 190.21ms | 598.49ms | 3.1× | 7.0% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 163.89ms | 487.32ms | 3.0× | 5.5% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 195.16ms | 587.71ms | 3.0× | 5.5% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 339.50ms | 1.16s | 3.4× | 60.3% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.91ms | 29.88ms | 1.2× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 9.82ms | 11.39ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_sum_range` | 9.34ms | 11.15ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 2.55ms | 2.78ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.64ms | 3.93ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.80ms | 5.15ms | 1.4× | 1.2% | PASS |
| mem_reads | `select_random_points` | 9.68ms | 10.62ms | 1.1× | 2.5% | PASS |
| mem_reads | `select_random_ranges` | 2.89ms | 3.91ms | 1.4× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.25ms | 1.0× | 1.3% | PASS |
| mem_reads | `groupby_scan` | 30.70ms | 33.13ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 5.72ms | 7.85ms | 1.4× | 1.0% | PASS |
| mem_reads | `index_join_scan` | 3.09ms | 4.31ms | 1.4× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.18s | 1.22s | 1.0× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 102.85ms | 117.70ms | 1.1× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 185.23ms | 286.27ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 15.48ms | 29.43ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 49.97ms | 101.86ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 34.99ms | 64.81ms | 1.9× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 46.06ms | 80.03ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 22.79ms | 52.55ms | 2.3× | 1.2% | FAIL |
| mem_writes | `types_delete_insert` | 25.37ms | 42.38ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 67.98ms | 117.60ms | 1.7× | 1.2% | PASS |
| file_reads | `oltp_point_select` | 93.99ms | 47.55ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 17.42ms | 13.35ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 16.73ms | 13.24ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 3.44ms | 3.11ms | 0.9× | 2.4% | PASS |
| file_reads | `oltp_distinct_range` | 4.49ms | 4.23ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 11.12ms | 7.31ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_points` | 17.42ms | 12.81ms | 0.7× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 10.05ms | 5.77ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 11.47ms | 6.37ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 31.53ms | 33.33ms | 1.1× | 0.8% | PASS |
| file_reads | `index_join` | 9.78ms | 9.38ms | 1.0× | 1.4% | PASS |
| file_reads | `index_join_scan` | 4.11ms | 4.71ms | 1.1× | 2.2% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.20s | 1.23s | 1.0× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 206.71ms | 145.71ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 199.31ms | 295.52ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 22.47ms | 32.90ms | 1.5× | 1.4% | PASS |
| file_writes | `oltp_update_index` | 77.54ms | 117.20ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 58.07ms | 74.00ms | 1.3× | 1.3% | PASS |
| file_writes | `oltp_delete_insert` | 68.74ms | 92.42ms | 1.3× | 1.3% | PASS |
| file_writes | `oltp_write_only` | 44.87ms | 60.04ms | 1.3× | 2.2% | PASS |
| file_writes | `types_delete_insert` | 39.92ms | 44.99ms | 1.1× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 92.12ms | 126.73ms | 1.4× | 1.8% | PASS |
| ac_reads | `oltp_point_select` | 47.15ms | 47.83ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 13.00ms | 13.40ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 12.17ms | 13.26ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_order_range` | 3.04ms | 3.11ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_distinct_range` | 4.09ms | 4.27ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 6.59ms | 7.41ms | 1.1× | 1.0% | PASS |
| ac_reads | `select_random_points` | 12.65ms | 12.87ms | 1.0× | 2.2% | PASS |
| ac_reads | `select_random_ranges` | 5.44ms | 5.79ms | 1.1× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 6.95ms | 6.38ms | 0.9× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 30.95ms | 33.34ms | 1.1× | 0.5% | PASS |
| ac_reads | `index_join` | 7.38ms | 9.46ms | 1.3× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 3.63ms | 4.76ms | 1.3× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.4% | PASS |
| ac_reads | `table_scan` | 1.19s | 1.22s | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 138.72ms | 145.50ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.01ms | 62.53ms | 3.0× | 7.5% | PASS |
| ac_writes | `oltp_insert_ac` | 24.08ms | 73.78ms | 3.1× | 7.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.00ms | 85.76ms | 3.3× | 7.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.10ms | 68.06ms | 3.2× | 5.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.24ms | 77.92ms | 3.2× | 6.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.08ms | 76.24ms | 3.2× | 8.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.59ms | 66.39ms | 3.2× | 7.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.11ms | 87.82ms | 3.0× | 6.1% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 29.74ms | 32.94ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_range_select` | 13.64ms | 12.59ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_sum_range` | 13.13ms | 12.50ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 2.87ms | 2.89ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.63ms | 3.98ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.45ms | 5.23ms | 1.5× | 1.3% | PASS |
| mem_reads | `select_random_points` | 19.07ms | 19.72ms | 1.0× | 1.1% | PASS |
| mem_reads | `select_random_ranges` | 3.03ms | 4.58ms | 1.5× | 0.9% | PASS |
| mem_reads | `covering_index_scan` | 3.63ms | 3.86ms | 1.1× | 1.0% | PASS |
| mem_reads | `groupby_scan` | 30.22ms | 32.75ms | 1.1× | 0.4% | PASS |
| mem_reads | `index_join` | 9.26ms | 8.07ms | 0.9× | 1.0% | PASS |
| mem_reads | `index_join_scan` | 3.05ms | 4.99ms | 1.6× | 1.2% | PASS |
| mem_reads | `types_table_scan` | 952.00ms | 1.13s | 1.2× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.10s | 1.22s | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 115.25ms | 123.75ms | 1.1× | 0.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 204.66ms | 315.47ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 15.77ms | 32.94ms | 2.1× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 55.78ms | 121.31ms | 2.2× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 40.46ms | 70.57ms | 1.7× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 45.42ms | 87.66ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 23.08ms | 50.24ms | 2.2× | 0.7% | PASS |
| mem_writes | `types_delete_insert` | 32.10ms | 44.90ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 79.32ms | 122.46ms | 1.5× | 1.2% | PASS |
| file_reads | `oltp_point_select` | 61.84ms | 43.28ms | 0.7× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 16.82ms | 14.12ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 16.36ms | 14.00ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.26ms | 3.17ms | 1.0× | 3.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.07ms | 4.27ms | 1.0× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 6.76ms | 6.75ms | 1.0× | 1.5% | PASS |
| file_reads | `select_random_points` | 22.96ms | 21.51ms | 0.9× | 1.2% | PASS |
| file_reads | `select_random_ranges` | 6.22ms | 5.70ms | 0.9× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 6.86ms | 5.13ms | 0.7× | 1.3% | PASS |
| file_reads | `groupby_scan` | 30.35ms | 32.99ms | 1.1× | 0.6% | PASS |
| file_reads | `index_join` | 11.04ms | 9.36ms | 0.8× | 1.5% | PASS |
| file_reads | `index_join_scan` | 3.50ms | 5.28ms | 1.5× | 1.5% | PASS |
| file_reads | `types_table_scan` | 953.94ms | 1.13s | 1.2× | 0.5% | PASS |
| file_reads | `table_scan` | 1.09s | 1.22s | 1.1× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 163.27ms | 140.72ms | 0.9× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 222.52ms | 328.61ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 23.90ms | 39.15ms | 1.6× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 103.30ms | 137.17ms | 1.3× | 7.6% | PASS |
| file_writes | `oltp_update_non_index` | 98.76ms | 82.94ms | 0.8× | 11.4% | PASS |
| file_writes | `oltp_delete_insert` | 77.26ms | 101.70ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 54.80ms | 60.62ms | 1.1× | 5.1% | PASS |
| file_writes | `types_delete_insert` | 55.46ms | 53.68ms | 1.0× | 2.4% | PASS |
| file_writes | `oltp_read_write` | 116.48ms | 133.89ms | 1.1× | 7.9% | PASS |
| ac_reads | `oltp_point_select` | 40.16ms | 43.22ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 14.81ms | 14.15ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 14.34ms | 13.96ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 3.06ms | 3.16ms | 1.0× | 3.0% | PASS |
| ac_reads | `oltp_distinct_range` | 3.88ms | 4.27ms | 1.1× | 2.7% | PASS |
| ac_reads | `oltp_index_scan` | 4.60ms | 6.72ms | 1.5× | 2.2% | PASS |
| ac_reads | `select_random_points` | 20.93ms | 21.51ms | 1.0× | 0.9% | PASS |
| ac_reads | `select_random_ranges` | 4.08ms | 5.66ms | 1.4× | 1.9% | PASS |
| ac_reads | `covering_index_scan` | 4.68ms | 5.11ms | 1.1× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 30.47ms | 33.04ms | 1.1× | 0.6% | PASS |
| ac_reads | `index_join` | 10.16ms | 9.34ms | 0.9× | 1.5% | PASS |
| ac_reads | `index_join_scan` | 3.35ms | 5.31ms | 1.6× | 1.3% | PASS |
| ac_reads | `types_table_scan` | 953.89ms | 1.13s | 1.2× | 0.6% | PASS |
| ac_reads | `table_scan` | 1.09s | 1.22s | 1.1× | 0.6% | PASS |
| ac_reads | `oltp_read_only` | 130.73ms | 140.23ms | 1.1× | 0.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 18.82ms | 48.41ms | 2.6× | 4.6% | PASS |
| ac_writes | `oltp_insert_ac` | 20.75ms | 58.39ms | 2.8× | 5.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.14ms | 69.45ms | 3.3× | 4.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 18.54ms | 55.97ms | 3.0× | 6.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 20.00ms | 63.51ms | 3.2× | 4.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 20.25ms | 63.78ms | 3.2× | 6.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 19.14ms | 57.61ms | 3.0× | 6.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.24ms | 70.19ms | 2.8× | 5.1% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 41.17ms | 40.91ms | 1.0× | 2.6% | PASS |
| mem_reads | `oltp_range_select` | 18.43ms | 14.75ms | 0.8× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 16.83ms | 14.18ms | 0.8× | 2.4% | PASS |
| mem_reads | `oltp_order_range` | 3.38ms | 3.26ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.43ms | 4.41ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 4.26ms | 6.62ms | 1.6× | 1.8% | PASS |
| mem_reads | `select_random_points` | 24.29ms | 22.70ms | 0.9× | 4.8% | PASS |
| mem_reads | `select_random_ranges` | 3.70ms | 5.54ms | 1.5× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 4.58ms | 5.24ms | 1.1× | 2.4% | PASS |
| mem_reads | `groupby_scan` | 35.68ms | 35.49ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 12.12ms | 11.01ms | 0.9× | 4.3% | PASS |
| mem_reads | `index_join_scan` | 3.94ms | 6.09ms | 1.5× | 3.9% | PASS |
| mem_reads | `types_table_scan` | 1.22s | 1.24s | 1.0× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.37s | 1.35s | 1.0× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 157.69ms | 146.96ms | 0.9× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 248.37ms | 392.39ms | 1.6× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 19.62ms | 43.48ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 76.02ms | 165.85ms | 2.2× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 54.72ms | 95.67ms | 1.7× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 59.07ms | 119.01ms | 2.0× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 31.45ms | 71.14ms | 2.3× | 1.6% | PASS |
| mem_writes | `types_delete_insert` | 42.11ms | 61.42ms | 1.5× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 115.06ms | 162.66ms | 1.4× | 2.2% | PASS |
| file_reads | `oltp_point_select` | 114.09ms | 59.18ms | 0.5× | 1.5% | PASS |
| file_reads | `oltp_range_select` | 26.13ms | 16.85ms | 0.6× | 2.3% | PASS |
| file_reads | `oltp_sum_range` | 24.74ms | 16.18ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.58ms | 3.54ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 5.64ms | 4.76ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_index_scan` | 12.01ms | 8.76ms | 0.7× | 1.9% | PASS |
| file_reads | `select_random_points` | 35.92ms | 25.93ms | 0.7× | 3.5% | PASS |
| file_reads | `select_random_ranges` | 11.15ms | 7.49ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 12.28ms | 7.04ms | 0.6× | 2.2% | PASS |
| file_reads | `groupby_scan` | 36.73ms | 35.65ms | 1.0× | 1.1% | PASS |
| file_reads | `index_join` | 16.26ms | 11.58ms | 0.7× | 3.4% | PASS |
| file_reads | `index_join_scan` | 5.08ms | 6.29ms | 1.2× | 4.4% | PASS |
| file_reads | `types_table_scan` | 1.23s | 1.24s | 1.0× | 0.8% | PASS |
| file_reads | `table_scan` | 1.35s | 1.34s | 1.0× | 0.7% | PASS |
| file_reads | `oltp_read_only` | 264.66ms | 173.73ms | 0.7× | 1.6% | PASS |
| file_writes | `oltp_bulk_insert` | 271.56ms | 404.42ms | 1.5× | 0.9% | PASS |
| file_writes | `oltp_insert` | 27.03ms | 50.34ms | 1.9× | 2.1% | PASS |
| file_writes | `oltp_update_index` | 107.72ms | 177.24ms | 1.6× | 2.4% | PASS |
| file_writes | `oltp_update_non_index` | 88.31ms | 109.44ms | 1.2× | 5.6% | PASS |
| file_writes | `oltp_delete_insert` | 94.93ms | 130.89ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 62.22ms | 82.25ms | 1.3× | 2.1% | PASS |
| file_writes | `types_delete_insert` | 69.47ms | 69.12ms | 1.0× | 2.9% | PASS |
| file_writes | `oltp_read_write` | 142.29ms | 167.84ms | 1.2× | 2.3% | PASS |
| ac_reads | `oltp_point_select` | 61.24ms | 57.17ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_range_select` | 19.93ms | 16.19ms | 0.8× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 18.75ms | 15.83ms | 0.8× | 2.0% | PASS |
| ac_reads | `oltp_order_range` | 3.91ms | 3.48ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.97ms | 4.63ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 6.81ms | 8.35ms | 1.2× | 2.0% | PASS |
| ac_reads | `select_random_points` | 26.18ms | 24.12ms | 0.9× | 3.3% | PASS |
| ac_reads | `select_random_ranges` | 6.11ms | 7.23ms | 1.2× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 7.08ms | 6.80ms | 1.0× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 35.32ms | 35.17ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 12.86ms | 11.01ms | 0.9× | 2.3% | PASS |
| ac_reads | `index_join_scan` | 4.27ms | 5.85ms | 1.4× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.22s | 1.0× | 1.4% | PASS |
| ac_reads | `table_scan` | 1.32s | 1.32s | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_read_only` | 187.34ms | 170.26ms | 0.9× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.62ms | 56.94ms | 2.6× | 4.3% | PASS |
| ac_writes | `oltp_insert_ac` | 24.36ms | 73.90ms | 3.0× | 5.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.80ms | 86.02ms | 3.3× | 5.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.56ms | 67.29ms | 3.1× | 6.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.13ms | 78.05ms | 3.1× | 6.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.26ms | 75.61ms | 3.1× | 5.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.10ms | 67.23ms | 3.0× | 7.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 30.34ms | 82.68ms | 2.7× | 4.9% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.15ms | 30.45ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 15.64ms | 16.26ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 14.29ms | 15.71ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 2.97ms | 3.10ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.84ms | 4.01ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.78ms | 4.91ms | 1.3× | 2.3% | PASS |
| mem_reads | `select_random_points` | 22.98ms | 25.46ms | 1.1× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 6.27ms | 6.80ms | 1.1× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 3.38ms | 3.90ms | 1.2× | 4.6% | PASS |
| mem_reads | `groupby_scan` | 31.03ms | 32.92ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 6.33ms | 8.63ms | 1.4× | 2.2% | PASS |
| mem_reads | `index_join_scan` | 3.32ms | 4.75ms | 1.4× | 1.5% | PASS |
| mem_reads | `types_table_scan` | 920.43ms | 983.90ms | 1.1× | 2.1% | PASS |
| mem_reads | `table_scan` | 1.25s | 1.10s | 0.9× | 2.5% | PASS |
| mem_reads | `oltp_read_only` | 125.93ms | 133.82ms | 1.1× | 1.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 194.64ms | 258.69ms | 1.3× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 15.05ms | 25.92ms | 1.7× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 53.80ms | 97.31ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 41.95ms | 62.03ms | 1.5× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 41.13ms | 74.34ms | 1.8× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 22.78ms | 45.63ms | 2.0× | 1.9% | PASS |
| mem_writes | `types_delete_insert` | 27.19ms | 40.00ms | 1.5× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 83.38ms | 122.63ms | 1.5× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 102.94ms | 49.21ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 24.28ms | 18.38ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 23.32ms | 17.93ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.90ms | 3.36ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_distinct_range` | 4.75ms | 4.28ms | 0.9× | 0.7% | PASS |
| file_reads | `oltp_index_scan` | 11.69ms | 7.01ms | 0.6× | 0.9% | PASS |
| file_reads | `select_random_points` | 31.56ms | 27.73ms | 0.9× | 1.3% | PASS |
| file_reads | `select_random_ranges` | 13.94ms | 8.71ms | 0.6× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 11.33ms | 5.73ms | 0.5× | 0.7% | PASS |
| file_reads | `groupby_scan` | 31.98ms | 33.22ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 10.71ms | 10.04ms | 0.9× | 1.4% | PASS |
| file_reads | `index_join_scan` | 4.17ms | 5.06ms | 1.2× | 1.0% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.01s | 1.0× | 0.9% | PASS |
| file_reads | `table_scan` | 1.31s | 1.11s | 0.8× | 2.1% | PASS |
| file_reads | `oltp_read_only` | 236.87ms | 159.68ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 254.19ms | 329.73ms | 1.3× | 8.8% | PASS |
| file_writes | `oltp_insert` | 43.22ms | 59.44ms | 1.4× | 56.6% | PASS |
| file_writes | `oltp_update_index` | 192.01ms | 186.56ms | 1.0× | 26.8% | PASS |
| file_writes | `oltp_update_non_index` | 206.92ms | 125.68ms | 0.6× | 31.0% | PASS |
| file_writes | `oltp_delete_insert` | 163.13ms | 142.73ms | 0.9× | 22.2% | PASS |
| file_writes | `oltp_write_only` | 132.94ms | 91.48ms | 0.7× | 29.7% | PASS |
| file_writes | `types_delete_insert` | 96.21ms | 82.13ms | 0.9× | 22.2% | PASS |
| file_writes | `oltp_read_write` | 203.41ms | 173.58ms | 0.9× | 15.3% | PASS |
| ac_reads | `oltp_point_select` | 52.81ms | 49.69ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 19.94ms | 18.69ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 18.18ms | 18.05ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 3.48ms | 3.40ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.33ms | 4.32ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.88ms | 7.28ms | 1.1× | 1.2% | PASS |
| ac_reads | `select_random_points` | 27.34ms | 28.86ms | 1.1× | 1.5% | PASS |
| ac_reads | `select_random_ranges` | 9.06ms | 8.84ms | 1.0× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 6.42ms | 5.87ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 31.59ms | 33.47ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 8.19ms | 10.23ms | 1.2× | 1.6% | PASS |
| ac_reads | `index_join_scan` | 3.77ms | 5.24ms | 1.4× | 2.0% | PASS |
| ac_reads | `types_table_scan` | 1.03s | 1.01s | 1.0× | 2.3% | PASS |
| ac_reads | `table_scan` | 1.08s | 1.07s | 1.0× | 2.7% | PASS |
| ac_reads | `oltp_read_only` | 159.63ms | 157.93ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 46.20ms | 112.03ms | 2.4× | 82.4% | PASS |
| ac_writes | `oltp_insert_ac` | 32.23ms | 174.69ms | 5.4× | 62.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 40.24ms | 147.75ms | 3.7× | 53.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 62.82ms | 203.69ms | 3.2× | 55.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 49.63ms | 175.90ms | 3.5× | 62.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 49.23ms | 136.04ms | 2.8× | 58.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.26ms | 105.91ms | 4.6× | 67.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 35.88ms | 108.60ms | 3.0× | 36.3% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 14s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 95.61ms | 130.00ms | 73.5% | 0.5% | PASS |
| `status_dirty_many_tables` | 99.49ms | 130.00ms | 76.5% | 0.5% | PASS |
| `diff_regular_working_one_table` | 90.53ms | 120.00ms | 75.4% | 0.5% | PASS |
| `diff_regular_working_many_tables` | 103.92ms | 140.00ms | 74.2% | 0.3% | PASS |
| `diff_stat_working_many_tables` | 104.08ms | 140.00ms | 74.3% | 0.4% | PASS |
| `diff_schema_working_many_tables` | 105.57ms | 140.00ms | 75.4% | 0.5% | PASS |
| `branch_list_many_branches` | 23.30ms | 35.00ms | 66.6% | 0.7% | PASS |
| `branch_create_delete` | 25.70ms | 40.00ms | 64.3% | 1.4% | PASS |
| `checkout_branch_clean` | 61.65ms | 150.00ms | 41.1% | 0.7% | PASS |
| `merge_data_no_conflicts` | 29.34ms | 50.00ms | 58.7% | 0.9% | PASS |
| `merge_schema_no_conflicts` | 22.39ms | 35.00ms | 64.0% | 1.2% | PASS |
| `merge_data_conflicts` | 33.10ms | 180.00ms | 18.4% | 0.8% | PASS |
| `merge_data_conflicts_with_resolve` | 33.10ms | 180.00ms | 18.4% | 1.0% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
