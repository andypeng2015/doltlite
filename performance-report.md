# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-11-08 17:56 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261101.186.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/42498484510)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.73s | 8.64s | 1.0× | 1.9% | **PASS** |
| Writes | 1.64s | 2.77s | 1.7× | 1.4% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.42s | 8.81s | 0.9× | 1.2% | **PASS** |
| Writes | 4.33s | 4.35s | 1.0× | 8.2% | **PASS** |
| Autocommit writes | 844.48ms | 2.61s | 3.1× | 30.6% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 1.97s | 2.12s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.47s | 2.33s | 0.9× | 1.8% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.28s | 2.33s | 1.0× | 2.2% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.00s | 1.87s | 0.9× | 2.4% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 332.99ms | 559.97ms | 1.7× | 0.9% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 457.13ms | 782.83ms | 1.7× | 1.5% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 472.88ms | 798.76ms | 1.7× | 2.5% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 381.49ms | 625.81ms | 1.6× | 1.9% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.19s | 2.17s | 1.0× | 0.8% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.67s | 2.37s | 0.9× | 1.1% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.69s | 2.46s | 0.9× | 1.4% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 1.87s | 1.81s | 1.0× | 1.7% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 880.97ms | 890.17ms | 1.0× | 8.4% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.32s | 1.29s | 1.0× | 8.0% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.24s | 1.26s | 1.0× | 11.8% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 882.87ms | 912.88ms | 1.0× | 3.3% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.17s | 2.22s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.74s | 2.44s | 0.9× | 1.1% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.42s | 2.40s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 1.81s | 1.87s | 1.0× | 3.2% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 174.80ms | 584.35ms | 3.3× | 27.6% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 195.70ms | 674.34ms | 3.4× | 31.6% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 286.57ms | 813.72ms | 2.8× | 50.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 187.41ms | 540.35ms | 2.9× | 5.3% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 18.23ms | 21.50ms | 1.2× | 1.1% | PASS |
| mem_reads | `oltp_range_select` | 8.45ms | 8.75ms | 1.0× | 2.0% | PASS |
| mem_reads | `oltp_sum_range` | 7.52ms | 8.47ms | 1.1× | 2.6% | PASS |
| mem_reads | `oltp_order_range` | 2.11ms | 2.25ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 2.90ms | 3.11ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.07ms | 3.84ms | 1.2× | 1.9% | PASS |
| mem_reads | `select_random_points` | 8.54ms | 8.96ms | 1.0× | 3.4% | PASS |
| mem_reads | `select_random_ranges` | 2.59ms | 3.10ms | 1.2× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 3.27ms | 3.18ms | 1.0× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 24.27ms | 27.02ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 4.49ms | 5.85ms | 1.3× | 0.8% | PASS |
| mem_reads | `index_join_scan` | 2.64ms | 3.72ms | 1.4× | 2.0% | PASS |
| mem_reads | `types_table_scan` | 849.25ms | 921.75ms | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 956.58ms | 1.01s | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 78.60ms | 88.66ms | 1.1× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 135.59ms | 205.24ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 11.63ms | 21.38ms | 1.8× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 38.95ms | 75.38ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 27.12ms | 47.06ms | 1.7× | 0.9% | PASS |
| mem_writes | `oltp_delete_insert` | 34.45ms | 58.05ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 16.88ms | 37.90ms | 2.2× | 0.8% | PASS |
| mem_writes | `types_delete_insert` | 18.83ms | 30.00ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_read_write` | 49.54ms | 84.97ms | 1.7× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 86.30ms | 38.03ms | 0.4× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 15.77ms | 10.45ms | 0.7× | 0.8% | PASS |
| file_reads | `oltp_sum_range` | 14.62ms | 10.20ms | 0.7× | 0.7% | PASS |
| file_reads | `oltp_order_range` | 2.97ms | 2.50ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_distinct_range` | 3.71ms | 3.33ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 10.18ms | 5.80ms | 0.6× | 1.0% | PASS |
| file_reads | `select_random_points` | 15.70ms | 10.63ms | 0.7× | 0.8% | PASS |
| file_reads | `select_random_ranges` | 9.48ms | 4.84ms | 0.5× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 10.52ms | 5.16ms | 0.5× | 0.9% | PASS |
| file_reads | `groupby_scan` | 25.20ms | 27.24ms | 1.1× | 0.6% | PASS |
| file_reads | `index_join` | 8.46ms | 7.39ms | 0.9× | 1.1% | PASS |
| file_reads | `index_join_scan` | 3.54ms | 4.01ms | 1.1× | 1.7% | PASS |
| file_reads | `types_table_scan` | 848.23ms | 921.44ms | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 955.48ms | 1.01s | 1.1× | 0.3% | PASS |
| file_reads | `oltp_read_only` | 176.81ms | 112.18ms | 0.6× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 182.07ms | 251.21ms | 1.4× | 4.4% | PASS |
| file_writes | `oltp_insert` | 29.67ms | 33.49ms | 1.1× | 11.6% | PASS |
| file_writes | `oltp_update_index` | 139.17ms | 134.61ms | 1.0× | 7.9% | PASS |
| file_writes | `oltp_update_non_index` | 114.59ms | 94.61ms | 0.8× | 9.5% | PASS |
| file_writes | `oltp_delete_insert` | 119.33ms | 109.10ms | 0.9× | 5.5% | PASS |
| file_writes | `oltp_write_only` | 90.86ms | 79.51ms | 0.9× | 9.3% | PASS |
| file_writes | `types_delete_insert` | 76.45ms | 57.93ms | 0.8× | 8.9% | PASS |
| file_writes | `oltp_read_write` | 128.83ms | 129.71ms | 1.0× | 3.2% | PASS |
| ac_reads | `oltp_point_select` | 40.69ms | 38.90ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 11.28ms | 10.54ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 10.51ms | 10.37ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 2.59ms | 2.52ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 3.30ms | 3.34ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_index_scan` | 5.72ms | 6.04ms | 1.1× | 1.5% | PASS |
| ac_reads | `select_random_points` | 12.09ms | 11.39ms | 0.9× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 4.95ms | 4.87ms | 1.0× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 5.98ms | 5.34ms | 0.9× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 25.15ms | 27.52ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 6.25ms | 7.88ms | 1.3× | 2.0% | PASS |
| ac_reads | `index_join_scan` | 3.19ms | 4.11ms | 1.3× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 930.05ms | 955.68ms | 1.0× | 1.5% | PASS |
| ac_reads | `table_scan` | 998.40ms | 1.02s | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_read_only` | 110.60ms | 112.57ms | 1.0× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.88ms | 54.95ms | 2.6× | 41.2% | PASS |
| ac_writes | `oltp_insert_ac` | 22.91ms | 78.25ms | 3.4× | 45.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.70ms | 78.44ms | 3.6× | 18.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 18.83ms | 64.79ms | 3.4× | 27.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.08ms | 80.55ms | 3.6× | 27.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.26ms | 70.81ms | 3.2× | 19.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.94ms | 69.35ms | 3.7× | 27.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.20ms | 87.20ms | 3.2× | 33.4% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.52ms | 27.71ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 15.24ms | 10.79ms | 0.7× | 3.9% | PASS |
| mem_reads | `oltp_sum_range` | 13.92ms | 11.13ms | 0.8× | 2.1% | PASS |
| mem_reads | `oltp_order_range` | 3.00ms | 2.64ms | 0.9× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.78ms | 3.49ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 3.25ms | 5.00ms | 1.5× | 1.9% | PASS |
| mem_reads | `select_random_points` | 19.20ms | 16.53ms | 0.9× | 2.5% | PASS |
| mem_reads | `select_random_ranges` | 2.91ms | 4.26ms | 1.5× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 3.31ms | 3.80ms | 1.1× | 1.8% | PASS |
| mem_reads | `groupby_scan` | 28.97ms | 28.61ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 9.09ms | 7.22ms | 0.8× | 2.6% | PASS |
| mem_reads | `index_join_scan` | 3.24ms | 4.68ms | 1.4× | 1.5% | PASS |
| mem_reads | `types_table_scan` | 916.66ms | 978.46ms | 1.1× | 1.5% | PASS |
| mem_reads | `table_scan` | 1.31s | 1.12s | 0.9× | 1.9% | PASS |
| mem_reads | `oltp_read_only` | 113.55ms | 103.83ms | 0.9× | 3.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 178.49ms | 279.09ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 13.69ms | 30.25ms | 2.2× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 52.43ms | 115.23ms | 2.2× | 1.7% | PASS |
| mem_writes | `oltp_update_non_index` | 39.48ms | 69.40ms | 1.8× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 43.81ms | 84.69ms | 1.9× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 22.26ms | 50.31ms | 2.3× | 0.8% | PASS |
| mem_writes | `types_delete_insert` | 30.63ms | 43.45ms | 1.4× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 76.34ms | 110.41ms | 1.4× | 2.3% | PASS |
| file_reads | `oltp_point_select` | 96.89ms | 44.25ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 22.48ms | 12.57ms | 0.6× | 1.3% | PASS |
| file_reads | `oltp_sum_range` | 20.47ms | 12.48ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_order_range` | 3.78ms | 2.82ms | 0.7× | 1.1% | PASS |
| file_reads | `oltp_distinct_range` | 4.57ms | 3.65ms | 0.8× | 0.8% | PASS |
| file_reads | `oltp_index_scan` | 10.35ms | 6.63ms | 0.6× | 0.9% | PASS |
| file_reads | `select_random_points` | 25.64ms | 18.09ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_ranges` | 9.78ms | 5.92ms | 0.6× | 0.5% | PASS |
| file_reads | `covering_index_scan` | 10.61ms | 5.62ms | 0.5× | 0.7% | PASS |
| file_reads | `groupby_scan` | 30.08ms | 28.93ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 13.41ms | 8.83ms | 0.7× | 1.2% | PASS |
| file_reads | `index_join_scan` | 4.14ms | 5.07ms | 1.2× | 1.2% | PASS |
| file_reads | `types_table_scan` | 962.07ms | 991.14ms | 1.0× | 3.1% | PASS |
| file_reads | `table_scan` | 1.24s | 1.10s | 0.9× | 4.9% | PASS |
| file_reads | `oltp_read_only` | 221.11ms | 130.03ms | 0.6× | 1.7% | PASS |
| file_writes | `oltp_bulk_insert` | 262.16ms | 353.39ms | 1.3× | 6.0% | PASS |
| file_writes | `oltp_insert` | 33.82ms | 56.35ms | 1.7× | 10.0% | PASS |
| file_writes | `oltp_update_index` | 182.34ms | 209.59ms | 1.1× | 4.3% | PASS |
| file_writes | `oltp_update_non_index` | 165.26ms | 138.64ms | 0.8× | 7.8% | PASS |
| file_writes | `oltp_delete_insert` | 199.58ms | 159.21ms | 0.8× | 8.8% | PASS |
| file_writes | `oltp_write_only` | 131.16ms | 110.24ms | 0.8× | 9.9% | PASS |
| file_writes | `types_delete_insert` | 155.69ms | 88.19ms | 0.6× | 8.2% | PASS |
| file_writes | `oltp_read_write` | 193.40ms | 170.46ms | 0.9× | 3.8% | PASS |
| ac_reads | `oltp_point_select` | 49.38ms | 44.33ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 18.67ms | 12.77ms | 0.7× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 16.52ms | 12.82ms | 0.8× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 3.40ms | 2.84ms | 0.8× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.15ms | 3.67ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 5.80ms | 6.70ms | 1.2× | 1.1% | PASS |
| ac_reads | `select_random_points` | 20.93ms | 18.17ms | 0.9× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 5.15ms | 5.92ms | 1.1× | 0.7% | PASS |
| ac_reads | `covering_index_scan` | 5.91ms | 5.62ms | 1.0× | 0.8% | PASS |
| ac_reads | `groupby_scan` | 29.60ms | 28.94ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 11.41ms | 8.96ms | 0.8× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.77ms | 5.26ms | 1.4× | 2.0% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.02s | 1.0× | 5.5% | PASS |
| ac_reads | `table_scan` | 1.35s | 1.13s | 0.8× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 161.81ms | 134.19ms | 0.8× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.60ms | 62.46ms | 3.0× | 30.0% | PASS |
| ac_writes | `oltp_insert_ac` | 27.93ms | 76.17ms | 2.7× | 29.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 23.70ms | 87.14ms | 3.7× | 31.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 19.71ms | 61.14ms | 3.1× | 22.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.71ms | 102.12ms | 3.7× | 64.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.18ms | 112.40ms | 4.5× | 31.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.49ms | 81.82ms | 3.8× | 36.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.38ms | 91.08ms | 3.1× | 42.5% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 30.65ms | 29.05ms | 0.9× | 5.3% | PASS |
| mem_reads | `oltp_range_select` | 14.68ms | 10.90ms | 0.7× | 5.2% | PASS |
| mem_reads | `oltp_sum_range` | 12.63ms | 10.73ms | 0.8× | 3.5% | PASS |
| mem_reads | `oltp_order_range` | 2.81ms | 2.58ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 3.54ms | 3.40ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.20ms | 4.77ms | 1.5× | 2.1% | PASS |
| mem_reads | `select_random_points` | 19.40ms | 16.93ms | 0.9× | 4.7% | PASS |
| mem_reads | `select_random_ranges` | 2.90ms | 4.38ms | 1.5× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 3.41ms | 4.00ms | 1.2× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 28.72ms | 28.59ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 8.97ms | 7.35ms | 0.8× | 3.8% | PASS |
| mem_reads | `index_join_scan` | 3.24ms | 4.79ms | 1.5× | 3.2% | PASS |
| mem_reads | `types_table_scan` | 919.43ms | 984.00ms | 1.1× | 2.1% | PASS |
| mem_reads | `table_scan` | 1.10s | 1.11s | 1.0× | 4.6% | PASS |
| mem_reads | `oltp_read_only` | 126.36ms | 109.68ms | 0.9× | 2.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 178.18ms | 279.44ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 14.21ms | 30.93ms | 2.2× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 57.80ms | 122.78ms | 2.1× | 2.8% | PASS |
| mem_writes | `oltp_update_non_index` | 44.71ms | 73.37ms | 1.6× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 44.06ms | 85.34ms | 1.9× | 2.2% | PASS |
| mem_writes | `oltp_write_only` | 24.08ms | 51.85ms | 2.2× | 2.7% | PASS |
| mem_writes | `types_delete_insert` | 31.87ms | 43.92ms | 1.4× | 2.7% | PASS |
| mem_writes | `oltp_read_write` | 77.96ms | 111.12ms | 1.4× | 3.6% | PASS |
| file_reads | `oltp_point_select` | 96.75ms | 44.86ms | 0.5× | 1.4% | PASS |
| file_reads | `oltp_range_select` | 21.30ms | 12.57ms | 0.6× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 19.80ms | 12.41ms | 0.6× | 1.4% | PASS |
| file_reads | `oltp_order_range` | 3.64ms | 2.81ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.42ms | 3.66ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 10.38ms | 6.74ms | 0.6× | 1.3% | PASS |
| file_reads | `select_random_points` | 25.17ms | 18.00ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_ranges` | 9.72ms | 6.03ms | 0.6× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 10.69ms | 5.62ms | 0.5× | 1.2% | PASS |
| file_reads | `groupby_scan` | 29.91ms | 29.01ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 14.33ms | 9.33ms | 0.7× | 2.9% | PASS |
| file_reads | `index_join_scan` | 4.23ms | 5.35ms | 1.3× | 2.7% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.05s | 1.0× | 1.0% | PASS |
| file_reads | `table_scan` | 1.16s | 1.12s | 1.0× | 2.2% | PASS |
| file_reads | `oltp_read_only` | 216.63ms | 130.20ms | 0.6× | 2.0% | PASS |
| file_writes | `oltp_bulk_insert` | 261.90ms | 354.11ms | 1.4× | 6.7% | PASS |
| file_writes | `oltp_insert` | 35.01ms | 56.02ms | 1.6× | 9.1% | PASS |
| file_writes | `oltp_update_index` | 177.67ms | 196.15ms | 1.1× | 11.6% | PASS |
| file_writes | `oltp_update_non_index` | 156.54ms | 136.00ms | 0.9× | 19.1% | PASS |
| file_writes | `oltp_delete_insert` | 167.37ms | 151.11ms | 0.9× | 8.1% | PASS |
| file_writes | `oltp_write_only` | 118.58ms | 106.62ms | 0.9× | 15.6% | PASS |
| file_writes | `types_delete_insert` | 129.02ms | 85.98ms | 0.7× | 12.6% | PASS |
| file_writes | `oltp_read_write` | 195.01ms | 174.13ms | 0.9× | 12.0% | PASS |
| ac_reads | `oltp_point_select` | 50.80ms | 45.44ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_range_select` | 17.24ms | 12.69ms | 0.7× | 2.4% | PASS |
| ac_reads | `oltp_sum_range` | 15.34ms | 12.54ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.22ms | 2.82ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 3.98ms | 3.65ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 5.82ms | 6.74ms | 1.2× | 1.2% | PASS |
| ac_reads | `select_random_points` | 21.52ms | 18.59ms | 0.9× | 3.4% | PASS |
| ac_reads | `select_random_ranges` | 5.10ms | 6.05ms | 1.2× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 5.97ms | 5.65ms | 0.9× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 29.12ms | 28.94ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 11.16ms | 9.04ms | 0.8× | 2.9% | PASS |
| ac_reads | `index_join_scan` | 3.77ms | 5.31ms | 1.4× | 3.8% | PASS |
| ac_reads | `types_table_scan` | 942.55ms | 995.65ms | 1.1× | 3.3% | PASS |
| ac_reads | `table_scan` | 1.15s | 1.12s | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_read_only` | 149.04ms | 130.40ms | 0.9× | 2.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.50ms | 74.43ms | 3.3× | 44.3% | PASS |
| ac_writes | `oltp_insert_ac` | 37.08ms | 103.98ms | 2.8× | 52.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.02ms | 111.81ms | 4.0× | 42.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 32.01ms | 113.23ms | 3.5× | 60.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 31.78ms | 87.31ms | 2.7× | 41.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 31.42ms | 102.12ms | 3.3× | 49.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.38ms | 103.85ms | 4.1× | 74.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 78.38ms | 117.00ms | 1.5× | 54.1% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.16ms | 25.54ms | 1.1× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 12.47ms | 12.70ms | 1.0× | 2.8% | PASS |
| mem_reads | `oltp_sum_range` | 12.02ms | 12.12ms | 1.0× | 2.5% | PASS |
| mem_reads | `oltp_order_range` | 2.43ms | 2.40ms | 1.0× | 1.6% | PASS |
| mem_reads | `oltp_distinct_range` | 2.93ms | 2.88ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_index_scan` | 3.17ms | 4.13ms | 1.3× | 3.1% | PASS |
| mem_reads | `select_random_points` | 17.34ms | 19.86ms | 1.1× | 1.9% | PASS |
| mem_reads | `select_random_ranges` | 4.82ms | 5.27ms | 1.1× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 2.40ms | 2.65ms | 1.1× | 6.1% | PASS |
| mem_reads | `groupby_scan` | 22.22ms | 22.86ms | 1.0× | 1.9% | PASS |
| mem_reads | `index_join` | 4.93ms | 6.76ms | 1.4× | 7.9% | PASS |
| mem_reads | `index_join_scan` | 3.08ms | 4.76ms | 1.5× | 3.4% | PASS |
| mem_reads | `types_table_scan` | 702.21ms | 734.07ms | 1.0× | 2.9% | PASS |
| mem_reads | `table_scan` | 1.08s | 909.36ms | 0.8× | 1.9% | PASS |
| mem_reads | `oltp_read_only` | 102.22ms | 101.60ms | 1.0× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 148.74ms | 212.08ms | 1.4× | 1.3% | PASS |
| mem_writes | `oltp_insert` | 11.42ms | 22.38ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 45.90ms | 89.56ms | 2.0× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 35.99ms | 58.50ms | 1.6× | 1.4% | PASS |
| mem_writes | `oltp_delete_insert` | 32.83ms | 66.12ms | 2.0× | 2.4% | PASS |
| mem_writes | `oltp_write_only` | 18.66ms | 42.87ms | 2.3× | 2.3% | PASS |
| mem_writes | `types_delete_insert` | 21.61ms | 35.56ms | 1.6× | 1.9% | PASS |
| mem_writes | `oltp_read_write` | 66.34ms | 98.74ms | 1.5× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 78.79ms | 40.76ms | 0.5× | 2.1% | PASS |
| file_reads | `oltp_range_select` | 17.76ms | 13.43ms | 0.8× | 2.4% | PASS |
| file_reads | `oltp_sum_range` | 16.36ms | 12.51ms | 0.8× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 2.81ms | 2.34ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 3.35ms | 2.86ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 8.85ms | 5.11ms | 0.6× | 1.0% | PASS |
| file_reads | `select_random_points` | 22.40ms | 19.93ms | 0.9× | 1.3% | PASS |
| file_reads | `select_random_ranges` | 10.33ms | 6.59ms | 0.6× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 8.55ms | 4.04ms | 0.5× | 1.9% | PASS |
| file_reads | `groupby_scan` | 23.27ms | 23.49ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join` | 8.34ms | 7.88ms | 0.9× | 3.8% | PASS |
| file_reads | `index_join_scan` | 3.66ms | 4.38ms | 1.2× | 4.8% | PASS |
| file_reads | `types_table_scan` | 705.78ms | 737.48ms | 1.0× | 1.9% | PASS |
| file_reads | `table_scan` | 803.42ms | 818.83ms | 1.0× | 1.7% | PASS |
| file_reads | `oltp_read_only` | 156.03ms | 108.12ms | 0.7× | 1.6% | PASS |
| file_writes | `oltp_bulk_insert` | 190.06ms | 248.92ms | 1.3× | 2.6% | PASS |
| file_writes | `oltp_insert` | 19.55ms | 32.58ms | 1.7× | 12.8% | PASS |
| file_writes | `oltp_update_index` | 141.15ms | 136.20ms | 1.0× | 3.5% | PASS |
| file_writes | `oltp_update_non_index` | 120.64ms | 98.93ms | 0.8× | 2.2% | PASS |
| file_writes | `oltp_delete_insert` | 111.07ms | 104.75ms | 0.9× | 3.0% | PASS |
| file_writes | `oltp_write_only` | 83.00ms | 82.98ms | 1.0× | 2.2% | PASS |
| file_writes | `types_delete_insert` | 76.78ms | 59.42ms | 0.8× | 8.3% | PASS |
| file_writes | `oltp_read_write` | 140.62ms | 149.09ms | 1.1× | 4.0% | PASS |
| ac_reads | `oltp_point_select` | 42.77ms | 42.35ms | 1.0× | 3.4% | PASS |
| ac_reads | `oltp_range_select` | 14.73ms | 14.23ms | 1.0× | 5.2% | PASS |
| ac_reads | `oltp_sum_range` | 15.15ms | 14.31ms | 0.9× | 6.8% | PASS |
| ac_reads | `oltp_order_range` | 2.75ms | 2.60ms | 0.9× | 3.5% | PASS |
| ac_reads | `oltp_distinct_range` | 3.29ms | 3.14ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_index_scan` | 5.83ms | 6.30ms | 1.1× | 2.6% | PASS |
| ac_reads | `select_random_points` | 21.37ms | 23.03ms | 1.1× | 5.8% | PASS |
| ac_reads | `select_random_ranges` | 7.10ms | 7.10ms | 1.0× | 3.2% | PASS |
| ac_reads | `covering_index_scan` | 5.06ms | 4.83ms | 1.0× | 3.0% | PASS |
| ac_reads | `groupby_scan` | 22.75ms | 23.56ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 6.18ms | 7.20ms | 1.2× | 3.6% | PASS |
| ac_reads | `index_join_scan` | 3.05ms | 4.25ms | 1.4× | 3.7% | PASS |
| ac_reads | `types_table_scan` | 721.82ms | 756.17ms | 1.0× | 1.0% | PASS |
| ac_reads | `table_scan` | 828.85ms | 849.01ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 105.36ms | 108.56ms | 1.0× | 0.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.87ms | 49.34ms | 2.5× | 3.3% | PASS |
| ac_writes | `oltp_insert_ac` | 23.78ms | 63.80ms | 2.7× | 5.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.15ms | 73.03ms | 2.8× | 6.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.76ms | 67.22ms | 3.2× | 5.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.00ms | 76.43ms | 3.3× | 8.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.81ms | 68.20ms | 3.0× | 3.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.79ms | 66.97ms | 2.8× | 14.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.25ms | 75.36ms | 2.8× | 4.2% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 41s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 68.39ms | 130.00ms | 52.6% | 0.8% | PASS |
| `status_dirty_many_tables` | 70.68ms | 130.00ms | 54.4% | 0.7% | PASS |
| `diff_regular_working_one_table` | 63.48ms | 120.00ms | 52.9% | 0.8% | PASS |
| `diff_regular_working_many_tables` | 73.82ms | 140.00ms | 52.7% | 0.5% | PASS |
| `diff_stat_working_many_tables` | 73.81ms | 140.00ms | 52.7% | 0.5% | PASS |
| `diff_schema_working_many_tables` | 74.52ms | 140.00ms | 53.2% | 0.6% | PASS |
| `branch_list_many_branches` | 16.93ms | 35.00ms | 48.4% | 1.7% | PASS |
| `branch_create_delete` | 19.37ms | 40.00ms | 48.4% | 1.7% | PASS |
| `checkout_branch_clean` | 47.24ms | 150.00ms | 31.5% | 1.2% | PASS |
| `merge_data_no_conflicts` | 21.85ms | 50.00ms | 43.7% | 1.4% | PASS |
| `merge_schema_no_conflicts` | 16.88ms | 35.00ms | 48.2% | 1.7% | PASS |
| `merge_data_conflicts` | 24.50ms | 180.00ms | 13.6% | 1.7% | PASS |
| `merge_data_conflicts_with_resolve` | 24.77ms | 180.00ms | 13.8% | 1.3% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
