# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-10-18 14:50 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261012.334.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/39131511171)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.31s | 8.56s | 1.0× | 1.7% | **PASS** |
| Writes | 1.61s | 2.63s | 1.6× | 1.7% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.93s | 8.71s | 1.0× | 1.8% | **PASS** |
| Writes | 3.60s | 3.81s | 1.1× | 5.5% | **FAIL** |
| Autocommit writes | 740.44ms | 2.06s | 2.8× | 7.1% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.14s | 2.16s | 1.0× | 1.2% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.62s | 2.77s | 1.1× | 1.6% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 1.68s | 1.77s | 1.1× | 3.0% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 1.87s | 1.85s | 1.0× | 2.3% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 356.37ms | 555.57ms | 1.6× | 1.8% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 520.91ms | 890.83ms | 1.7× | 1.1% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 373.62ms | 610.75ms | 1.6× | 1.7% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 362.33ms | 570.23ms | 1.6× | 2.5% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.26s | 2.18s | 1.0× | 1.0% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.85s | 2.84s | 1.0× | 1.7% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 1.91s | 1.85s | 1.0× | 1.7% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 1.91s | 1.84s | 1.0× | 2.2% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 880.04ms | 862.14ms | 1.0× | 5.1% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 780.87ms | 985.70ms | 1.3× | 4.9% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.02s | 1.03s | 1.0× | 5.8% | **FAIL** |
| File-backed | Writes | compositepk | 8 | 55 | 915.43ms | 926.90ms | 1.0× | 5.8% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.11s | 2.19s | 1.0× | 1.1% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.70s | 2.82s | 1.0× | 1.7% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 1.81s | 1.87s | 1.0× | 2.2% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 1.86s | 1.87s | 1.0× | 2.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 156.97ms | 511.01ms | 3.3× | 13.6% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 146.41ms | 465.04ms | 3.2× | 7.2% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 229.17ms | 533.08ms | 2.3× | 5.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 207.89ms | 550.49ms | 2.6× | 5.6% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 19.26ms | 22.19ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_range_select` | 8.67ms | 8.76ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_sum_range` | 7.95ms | 8.69ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 2.15ms | 2.25ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.01ms | 3.19ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.10ms | 3.83ms | 1.2× | 1.6% | PASS |
| mem_reads | `select_random_points` | 8.36ms | 8.89ms | 1.1× | 1.4% | PASS |
| mem_reads | `select_random_ranges` | 2.45ms | 3.16ms | 1.3× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 3.30ms | 3.24ms | 1.0× | 1.1% | PASS |
| mem_reads | `groupby_scan` | 25.17ms | 27.48ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 4.58ms | 5.99ms | 1.3× | 1.1% | PASS |
| mem_reads | `index_join_scan` | 2.70ms | 3.79ms | 1.4× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 877.37ms | 928.04ms | 1.1× | 1.3% | PASS |
| mem_reads | `table_scan` | 1.09s | 1.04s | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 85.62ms | 91.95ms | 1.1× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 141.87ms | 203.53ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 12.42ms | 21.34ms | 1.7× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 41.51ms | 76.54ms | 1.8× | 1.7% | PASS |
| mem_writes | `oltp_update_non_index` | 29.86ms | 45.25ms | 1.5× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 37.42ms | 57.77ms | 1.5× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 18.49ms | 36.83ms | 2.0× | 1.8% | PASS |
| mem_writes | `types_delete_insert` | 20.08ms | 28.27ms | 1.4× | 1.9% | PASS |
| mem_writes | `oltp_read_write` | 54.73ms | 86.03ms | 1.6× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 93.35ms | 40.31ms | 0.4× | 3.9% | PASS |
| file_reads | `oltp_range_select` | 16.35ms | 10.63ms | 0.7× | 2.4% | PASS |
| file_reads | `oltp_sum_range` | 15.49ms | 10.47ms | 0.7× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 2.96ms | 2.52ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 3.80ms | 3.40ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 10.87ms | 6.08ms | 0.6× | 0.9% | PASS |
| file_reads | `select_random_points` | 16.56ms | 11.08ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 10.03ms | 5.04ms | 0.5× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.22ms | 5.51ms | 0.5× | 1.5% | PASS |
| file_reads | `groupby_scan` | 26.17ms | 27.93ms | 1.1× | 0.8% | PASS |
| file_reads | `index_join` | 8.83ms | 7.77ms | 0.9× | 1.6% | PASS |
| file_reads | `index_join_scan` | 3.55ms | 4.05ms | 1.1× | 1.0% | PASS |
| file_reads | `types_table_scan` | 866.79ms | 921.76ms | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 987.23ms | 1.01s | 1.0× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 186.05ms | 114.49ms | 0.6× | 2.8% | PASS |
| file_writes | `oltp_bulk_insert` | 186.16ms | 249.10ms | 1.3× | 4.3% | PASS |
| file_writes | `oltp_insert` | 27.83ms | 33.13ms | 1.2× | 5.9% | PASS |
| file_writes | `oltp_update_index` | 138.01ms | 127.25ms | 0.9× | 4.6% | PASS |
| file_writes | `oltp_update_non_index` | 114.84ms | 89.68ms | 0.8× | 8.4% | PASS |
| file_writes | `oltp_delete_insert` | 120.95ms | 104.86ms | 0.9× | 5.5% | PASS |
| file_writes | `oltp_write_only` | 90.68ms | 75.22ms | 0.8× | 5.9% | PASS |
| file_writes | `types_delete_insert` | 74.68ms | 57.90ms | 0.8× | 3.7% | PASS |
| file_writes | `oltp_read_write` | 126.90ms | 124.99ms | 1.0× | 3.0% | PASS |
| ac_reads | `oltp_point_select` | 44.08ms | 40.49ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 11.75ms | 10.75ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 10.77ms | 10.53ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_order_range` | 2.56ms | 2.51ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 3.32ms | 3.40ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 5.99ms | 6.12ms | 1.0× | 1.5% | PASS |
| ac_reads | `select_random_points` | 11.46ms | 11.03ms | 1.0× | 1.4% | PASS |
| ac_reads | `select_random_ranges` | 5.04ms | 5.03ms | 1.0× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 6.21ms | 5.49ms | 0.9× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 25.62ms | 27.73ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 6.24ms | 7.63ms | 1.2× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.11ms | 4.07ms | 1.3× | 1.6% | PASS |
| ac_reads | `types_table_scan` | 873.71ms | 925.57ms | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 985.86ms | 1.01s | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 114.92ms | 114.65ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.99ms | 50.38ms | 2.8× | 31.9% | PASS |
| ac_writes | `oltp_insert_ac` | 19.33ms | 65.87ms | 3.4× | 13.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.79ms | 76.47ms | 3.5× | 20.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.86ms | 60.27ms | 3.4× | 37.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 18.80ms | 64.68ms | 3.4× | 7.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.22ms | 63.14ms | 3.3× | 8.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 16.89ms | 56.24ms | 3.3× | 13.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.09ms | 73.97ms | 2.9× | 12.6% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.38ms | 34.41ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 14.43ms | 12.95ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 14.16ms | 12.97ms | 0.9× | 2.7% | PASS |
| mem_reads | `oltp_order_range` | 3.03ms | 2.95ms | 1.0× | 2.5% | PASS |
| mem_reads | `oltp_distinct_range` | 3.80ms | 4.03ms | 1.1× | 2.5% | PASS |
| mem_reads | `oltp_index_scan` | 3.58ms | 5.69ms | 1.6× | 1.0% | PASS |
| mem_reads | `select_random_points` | 21.46ms | 21.16ms | 1.0× | 1.7% | PASS |
| mem_reads | `select_random_ranges` | 3.15ms | 4.75ms | 1.5× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 3.65ms | 4.25ms | 1.2× | 1.5% | PASS |
| mem_reads | `groupby_scan` | 31.23ms | 33.23ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 9.96ms | 9.12ms | 0.9× | 1.8% | PASS |
| mem_reads | `index_join_scan` | 3.18ms | 5.38ms | 1.7× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.20s | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.28s | 1.29s | 1.0× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 129.31ms | 131.88ms | 1.0× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 204.92ms | 323.06ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 16.01ms | 34.00ms | 2.1× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 60.04ms | 132.01ms | 2.2× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 43.73ms | 74.98ms | 1.7× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 48.15ms | 93.88ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 24.61ms | 53.56ms | 2.2× | 1.1% | PASS |
| mem_writes | `types_delete_insert` | 34.61ms | 47.57ms | 1.4× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 88.85ms | 131.75ms | 1.5× | 1.5% | PASS |
| file_reads | `oltp_point_select` | 65.76ms | 44.62ms | 0.7× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 18.15ms | 14.53ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 17.73ms | 14.36ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 3.40ms | 3.19ms | 0.9× | 3.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.15ms | 4.31ms | 1.0× | 2.0% | PASS |
| file_reads | `oltp_index_scan` | 7.01ms | 6.97ms | 1.0× | 1.1% | PASS |
| file_reads | `select_random_points` | 25.88ms | 22.68ms | 0.9× | 2.0% | PASS |
| file_reads | `select_random_ranges` | 6.40ms | 5.77ms | 0.9× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 7.04ms | 5.33ms | 0.8× | 1.4% | PASS |
| file_reads | `groupby_scan` | 31.43ms | 33.45ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 12.34ms | 10.16ms | 0.8× | 2.3% | PASS |
| file_reads | `index_join_scan` | 3.89ms | 6.30ms | 1.6× | 5.5% | PASS |
| file_reads | `types_table_scan` | 1.13s | 1.21s | 1.1× | 1.7% | PASS |
| file_reads | `table_scan` | 1.33s | 1.31s | 1.0× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 178.48ms | 149.02ms | 0.8× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 223.74ms | 337.09ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_insert` | 21.05ms | 40.92ms | 1.9× | 2.0% | PASS |
| file_writes | `oltp_update_index` | 110.04ms | 148.11ms | 1.3× | 7.9% | PASS |
| file_writes | `oltp_update_non_index` | 93.99ms | 87.65ms | 0.9× | 10.6% | PASS |
| file_writes | `oltp_delete_insert` | 72.06ms | 107.22ms | 1.5× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 64.33ms | 64.15ms | 1.0× | 19.8% | PASS |
| file_writes | `types_delete_insert` | 52.66ms | 55.84ms | 1.1× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 143.00ms | 144.73ms | 1.0× | 8.1% | PASS |
| ac_reads | `oltp_point_select` | 44.30ms | 45.66ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_range_select` | 16.82ms | 14.82ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 16.20ms | 14.75ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_order_range` | 3.28ms | 3.26ms | 1.0× | 2.4% | PASS |
| ac_reads | `oltp_distinct_range` | 4.03ms | 4.38ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_index_scan` | 4.96ms | 7.16ms | 1.4× | 1.7% | PASS |
| ac_reads | `select_random_points` | 23.02ms | 22.88ms | 1.0× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 4.27ms | 5.82ms | 1.4× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 4.87ms | 5.35ms | 1.1× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 31.23ms | 33.53ms | 1.1× | 0.5% | PASS |
| ac_reads | `index_join` | 11.01ms | 9.94ms | 0.9× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 3.46ms | 5.59ms | 1.6× | 1.3% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.19s | 1.1× | 1.4% | PASS |
| ac_reads | `table_scan` | 1.32s | 1.30s | 1.0× | 0.6% | PASS |
| ac_reads | `oltp_read_only` | 148.95ms | 151.36ms | 1.0× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.12ms | 46.88ms | 2.7× | 7.1% | PASS |
| ac_writes | `oltp_insert_ac` | 18.11ms | 57.54ms | 3.2× | 7.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 18.31ms | 66.96ms | 3.7× | 7.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.88ms | 52.03ms | 3.1× | 8.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 18.54ms | 59.84ms | 3.2× | 5.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 17.06ms | 60.20ms | 3.5× | 7.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 17.26ms | 54.61ms | 3.2× | 7.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 23.12ms | 66.99ms | 2.9× | 7.3% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 21.63ms | 22.59ms | 1.0× | 3.9% | PASS |
| mem_reads | `oltp_range_select` | 9.76ms | 8.49ms | 0.9× | 5.8% | PASS |
| mem_reads | `oltp_sum_range` | 9.63ms | 8.46ms | 0.9× | 3.0% | PASS |
| mem_reads | `oltp_order_range` | 1.93ms | 1.97ms | 1.0× | 2.6% | PASS |
| mem_reads | `oltp_distinct_range` | 2.53ms | 2.50ms | 1.0× | 4.0% | PASS |
| mem_reads | `oltp_index_scan` | 2.64ms | 4.27ms | 1.6× | 4.1% | PASS |
| mem_reads | `select_random_points` | 17.21ms | 14.92ms | 0.9× | 4.0% | PASS |
| mem_reads | `select_random_ranges` | 2.41ms | 3.59ms | 1.5× | 2.3% | PASS |
| mem_reads | `covering_index_scan` | 2.44ms | 3.05ms | 1.3× | 4.1% | PASS |
| mem_reads | `groupby_scan` | 20.57ms | 20.18ms | 1.0× | 1.3% | PASS |
| mem_reads | `index_join` | 7.96ms | 5.94ms | 0.7× | 3.6% | PASS |
| mem_reads | `index_join_scan` | 2.77ms | 4.57ms | 1.7× | 2.8% | PASS |
| mem_reads | `types_table_scan` | 707.06ms | 753.23ms | 1.1× | 1.2% | PASS |
| mem_reads | `table_scan` | 792.89ms | 839.73ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 76.86ms | 74.16ms | 1.0× | 2.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 146.14ms | 215.35ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 11.28ms | 23.29ms | 2.1× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 42.19ms | 87.81ms | 2.1× | 2.2% | PASS |
| mem_writes | `oltp_update_non_index` | 33.87ms | 54.26ms | 1.6× | 2.1% | PASS |
| mem_writes | `oltp_delete_insert` | 34.47ms | 66.94ms | 1.9× | 1.8% | PASS |
| mem_writes | `oltp_write_only` | 18.90ms | 40.74ms | 2.2× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 25.40ms | 33.83ms | 1.3× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 61.38ms | 88.53ms | 1.4× | 2.8% | PASS |
| file_reads | `oltp_point_select` | 83.87ms | 39.12ms | 0.5× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 16.53ms | 10.28ms | 0.6× | 2.4% | PASS |
| file_reads | `oltp_sum_range` | 16.01ms | 10.19ms | 0.6× | 2.2% | PASS |
| file_reads | `oltp_order_range` | 2.89ms | 2.24ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 3.39ms | 2.77ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 8.81ms | 5.92ms | 0.7× | 2.2% | PASS |
| file_reads | `select_random_points` | 21.33ms | 14.98ms | 0.7× | 3.8% | PASS |
| file_reads | `select_random_ranges` | 8.13ms | 5.12ms | 0.6× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 8.74ms | 4.87ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 21.57ms | 20.26ms | 0.9× | 1.3% | PASS |
| file_reads | `index_join` | 11.35ms | 7.51ms | 0.7× | 3.1% | PASS |
| file_reads | `index_join_scan` | 3.40ms | 4.44ms | 1.3× | 1.5% | PASS |
| file_reads | `types_table_scan` | 713.22ms | 750.51ms | 1.1× | 1.7% | PASS |
| file_reads | `table_scan` | 823.48ms | 870.88ms | 1.1× | 2.5% | PASS |
| file_reads | `oltp_read_only` | 169.27ms | 99.48ms | 0.6× | 3.4% | PASS |
| file_writes | `oltp_bulk_insert` | 208.97ms | 278.25ms | 1.3× | 3.0% | PASS |
| file_writes | `oltp_insert` | 18.89ms | 48.77ms | 2.6× | 8.0% | FAIL |
| file_writes | `oltp_update_index` | 154.49ms | 179.12ms | 1.2× | 4.9% | PASS |
| file_writes | `oltp_update_non_index` | 129.98ms | 114.22ms | 0.9× | 5.4% | PASS |
| file_writes | `oltp_delete_insert` | 153.21ms | 129.15ms | 0.8× | 6.2% | PASS |
| file_writes | `oltp_write_only` | 110.32ms | 85.83ms | 0.8× | 6.5% | PASS |
| file_writes | `types_delete_insert` | 105.30ms | 69.38ms | 0.7× | 8.7% | PASS |
| file_writes | `oltp_read_write` | 142.86ms | 126.85ms | 0.9× | 3.2% | PASS |
| ac_reads | `oltp_point_select` | 41.94ms | 38.18ms | 0.9× | 2.5% | PASS |
| ac_reads | `oltp_range_select` | 12.64ms | 10.07ms | 0.8× | 2.7% | PASS |
| ac_reads | `oltp_sum_range` | 12.59ms | 10.12ms | 0.8× | 2.6% | PASS |
| ac_reads | `oltp_order_range` | 2.58ms | 2.25ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 3.04ms | 2.75ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_index_scan` | 4.93ms | 5.85ms | 1.2× | 2.1% | PASS |
| ac_reads | `select_random_points` | 16.93ms | 14.48ms | 0.9× | 3.9% | PASS |
| ac_reads | `select_random_ranges` | 4.29ms | 5.08ms | 1.2× | 1.8% | PASS |
| ac_reads | `covering_index_scan` | 4.75ms | 4.83ms | 1.0× | 2.6% | PASS |
| ac_reads | `groupby_scan` | 20.98ms | 20.16ms | 1.0× | 1.3% | PASS |
| ac_reads | `index_join` | 9.56ms | 7.56ms | 0.8× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 3.12ms | 4.52ms | 1.4× | 2.3% | PASS |
| ac_reads | `types_table_scan` | 733.86ms | 775.55ms | 1.1× | 2.2% | PASS |
| ac_reads | `table_scan` | 836.91ms | 872.22ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_read_only` | 102.85ms | 94.92ms | 0.9× | 1.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 27.74ms | 55.03ms | 2.0× | 9.5% | PASS |
| ac_writes | `oltp_insert_ac` | 30.09ms | 66.57ms | 2.2× | 3.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.79ms | 72.77ms | 2.4× | 3.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 26.42ms | 63.28ms | 2.4× | 6.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 30.15ms | 69.81ms | 2.3× | 5.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 29.22ms | 68.95ms | 2.4× | 4.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.90ms | 61.78ms | 2.7× | 6.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.85ms | 74.90ms | 2.3× | 4.6% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 22.82ms | 25.92ms | 1.1× | 3.0% | PASS |
| mem_reads | `oltp_range_select` | 12.27ms | 12.06ms | 1.0× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 10.99ms | 11.86ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 2.34ms | 2.32ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_distinct_range` | 2.87ms | 2.87ms | 1.0× | 2.3% | PASS |
| mem_reads | `oltp_index_scan` | 3.15ms | 4.35ms | 1.4× | 2.8% | PASS |
| mem_reads | `select_random_points` | 19.55ms | 21.61ms | 1.1× | 2.5% | PASS |
| mem_reads | `select_random_ranges` | 4.98ms | 5.49ms | 1.1× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 2.58ms | 3.41ms | 1.3× | 2.8% | PASS |
| mem_reads | `groupby_scan` | 21.84ms | 22.69ms | 1.0× | 1.1% | PASS |
| mem_reads | `index_join` | 5.00ms | 7.13ms | 1.4× | 4.5% | PASS |
| mem_reads | `index_join_scan` | 2.86ms | 4.53ms | 1.6× | 2.3% | PASS |
| mem_reads | `types_table_scan` | 720.21ms | 759.43ms | 1.1× | 1.1% | PASS |
| mem_reads | `table_scan` | 945.02ms | 876.70ms | 0.9× | 6.2% | PASS |
| mem_reads | `oltp_read_only` | 91.34ms | 92.22ms | 1.0× | 2.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 149.92ms | 204.60ms | 1.4× | 1.8% | PASS |
| mem_writes | `oltp_insert` | 11.54ms | 20.92ms | 1.8× | 2.3% | PASS |
| mem_writes | `oltp_update_index` | 43.34ms | 80.24ms | 1.9× | 2.7% | PASS |
| mem_writes | `oltp_update_non_index` | 32.71ms | 50.45ms | 1.5× | 3.2% | PASS |
| mem_writes | `oltp_delete_insert` | 30.69ms | 58.20ms | 1.9× | 3.0% | PASS |
| mem_writes | `oltp_write_only` | 17.21ms | 36.26ms | 2.1× | 2.2% | PASS |
| mem_writes | `types_delete_insert` | 20.48ms | 31.88ms | 1.6× | 2.4% | PASS |
| mem_writes | `oltp_read_write` | 56.45ms | 87.67ms | 1.6× | 2.8% | PASS |
| file_reads | `oltp_point_select` | 78.47ms | 38.50ms | 0.5× | 1.9% | PASS |
| file_reads | `oltp_range_select` | 17.57ms | 13.01ms | 0.7× | 2.7% | PASS |
| file_reads | `oltp_sum_range` | 16.62ms | 12.98ms | 0.8× | 2.2% | PASS |
| file_reads | `oltp_order_range` | 3.01ms | 2.47ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 3.56ms | 3.04ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 9.47ms | 5.95ms | 0.6× | 2.2% | PASS |
| file_reads | `select_random_points` | 25.68ms | 22.29ms | 0.9× | 2.9% | PASS |
| file_reads | `select_random_ranges` | 10.80ms | 6.96ms | 0.6× | 1.6% | PASS |
| file_reads | `covering_index_scan` | 8.82ms | 4.72ms | 0.5× | 3.5% | PASS |
| file_reads | `groupby_scan` | 21.58ms | 22.00ms | 1.0× | 1.7% | PASS |
| file_reads | `index_join` | 8.43ms | 7.99ms | 0.9× | 4.2% | PASS |
| file_reads | `index_join_scan` | 3.40ms | 4.15ms | 1.2× | 2.5% | PASS |
| file_reads | `types_table_scan` | 707.36ms | 739.80ms | 1.0× | 1.9% | PASS |
| file_reads | `table_scan` | 831.10ms | 846.99ms | 1.0× | 2.3% | PASS |
| file_reads | `oltp_read_only` | 163.71ms | 109.77ms | 0.7× | 2.3% | PASS |
| file_writes | `oltp_bulk_insert` | 199.11ms | 257.86ms | 1.3× | 4.8% | PASS |
| file_writes | `oltp_insert` | 19.37ms | 31.17ms | 1.6× | 9.9% | PASS |
| file_writes | `oltp_update_index` | 151.16ms | 148.95ms | 1.0× | 6.8% | PASS |
| file_writes | `oltp_update_non_index` | 128.25ms | 102.64ms | 0.8× | 3.4% | PASS |
| file_writes | `oltp_delete_insert` | 125.14ms | 109.11ms | 0.9× | 6.8% | PASS |
| file_writes | `oltp_write_only` | 84.82ms | 78.89ms | 0.9× | 4.5% | PASS |
| file_writes | `types_delete_insert` | 77.60ms | 64.99ms | 0.8× | 10.1% | PASS |
| file_writes | `oltp_read_write` | 129.98ms | 133.28ms | 1.0× | 4.0% | PASS |
| ac_reads | `oltp_point_select` | 39.93ms | 38.89ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_range_select` | 13.62ms | 13.13ms | 1.0× | 3.0% | PASS |
| ac_reads | `oltp_sum_range` | 12.64ms | 12.97ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_order_range` | 2.61ms | 2.46ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 3.12ms | 2.96ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_index_scan` | 5.26ms | 5.63ms | 1.1× | 1.9% | PASS |
| ac_reads | `select_random_points` | 20.37ms | 21.11ms | 1.0× | 3.8% | PASS |
| ac_reads | `select_random_ranges` | 6.81ms | 6.83ms | 1.0× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 5.09ms | 4.88ms | 1.0× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 22.02ms | 22.61ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 6.76ms | 8.06ms | 1.2× | 3.5% | PASS |
| ac_reads | `index_join_scan` | 3.24ms | 4.31ms | 1.3× | 2.9% | PASS |
| ac_reads | `types_table_scan` | 722.21ms | 750.95ms | 1.0× | 2.3% | PASS |
| ac_reads | `table_scan` | 876.40ms | 857.59ms | 1.0× | 6.1% | PASS |
| ac_reads | `oltp_read_only` | 116.44ms | 117.20ms | 1.0× | 3.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.35ms | 56.05ms | 2.5× | 3.5% | PASS |
| ac_writes | `oltp_insert_ac` | 26.51ms | 67.12ms | 2.5× | 6.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.22ms | 73.80ms | 2.6× | 5.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.39ms | 67.64ms | 3.0× | 6.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.81ms | 74.68ms | 2.9× | 5.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.21ms | 72.98ms | 2.8× | 4.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 26.90ms | 62.72ms | 2.3× | 11.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.51ms | 75.51ms | 2.6× | 4.1% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 15s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 96.36ms | 130.00ms | 74.1% | 0.7% | PASS |
| `status_dirty_many_tables` | 99.74ms | 130.00ms | 76.7% | 0.6% | PASS |
| `diff_regular_working_one_table` | 91.46ms | 120.00ms | 76.2% | 0.5% | PASS |
| `diff_regular_working_many_tables` | 105.01ms | 140.00ms | 75.0% | 0.6% | PASS |
| `diff_stat_working_many_tables` | 104.86ms | 140.00ms | 74.9% | 0.5% | PASS |
| `diff_schema_working_many_tables` | 105.97ms | 140.00ms | 75.7% | 0.7% | PASS |
| `branch_list_many_branches` | 23.97ms | 35.00ms | 68.5% | 2.3% | PASS |
| `branch_create_delete` | 26.43ms | 40.00ms | 66.1% | 1.5% | PASS |
| `checkout_branch_clean` | 62.05ms | 150.00ms | 41.4% | 1.1% | PASS |
| `merge_data_no_conflicts` | 30.41ms | 50.00ms | 60.8% | 1.6% | PASS |
| `merge_schema_no_conflicts` | 23.09ms | 35.00ms | 66.0% | 1.5% | PASS |
| `merge_data_conflicts` | 33.94ms | 180.00ms | 18.9% | 1.5% | PASS |
| `merge_data_conflicts_with_resolve` | 34.27ms | 180.00ms | 19.0% | 1.2% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
