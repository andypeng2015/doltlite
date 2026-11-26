# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-26 20:50 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261122.9.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/45881907681)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.55s | 9.77s | 1.0× | 1.7% | **PASS** |
| Writes | 1.88s | 3.18s | 1.7× | 1.2% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.33s | 9.90s | 1.0× | 1.1% | **PASS** |
| Writes | 4.02s | 4.41s | 1.1× | 3.8% | **PASS** |
| Autocommit writes | 680.22ms | 2.15s | 3.2× | 11.2% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.16s | 2.22s | 1.0× | 1.5% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.68s | 2.83s | 1.1× | 2.2% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.39s | 2.37s | 1.0× | 1.6% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.32s | 2.35s | 1.0× | 1.7% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 333.85ms | 566.45ms | 1.7× | 1.0% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 634.63ms | 1.09s | 1.7× | 1.6% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 452.64ms | 777.95ms | 1.7× | 1.5% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 458.46ms | 746.88ms | 1.6× | 1.1% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.30s | 2.24s | 1.0× | 1.0% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.08s | 2.92s | 0.9× | 2.1% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.56s | 2.38s | 0.9× | 1.1% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.39s | 2.36s | 1.0× | 1.0% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 849.12ms | 878.36ms | 1.0× | 2.4% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 963.71ms | 1.18s | 1.2× | 5.3% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.17s | 1.23s | 1.1× | 5.5% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 1.04s | 1.11s | 1.1× | 3.8% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.05s | 2.22s | 1.1× | 0.9% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 3.24s | 3.06s | 0.9× | 2.8% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.27s | 2.33s | 1.0× | 1.1% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.39s | 2.39s | 1.0× | 1.0% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 147.37ms | 485.76ms | 3.3× | 12.2% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 200.46ms | 601.31ms | 3.0× | 7.0% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 167.43ms | 520.18ms | 3.1× | 12.3% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 164.96ms | 542.75ms | 3.3× | 15.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 20.96ms | 23.45ms | 1.1× | 2.2% | PASS |
| mem_reads | `oltp_range_select` | 10.04ms | 9.07ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 8.59ms | 8.78ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 2.24ms | 2.34ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.01ms | 3.19ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.23ms | 4.17ms | 1.3× | 1.6% | PASS |
| mem_reads | `select_random_points` | 10.31ms | 10.11ms | 1.0× | 3.8% | PASS |
| mem_reads | `select_random_ranges` | 2.81ms | 3.20ms | 1.1× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 3.29ms | 3.44ms | 1.0× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 24.93ms | 27.33ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 4.63ms | 6.57ms | 1.4× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 2.99ms | 4.07ms | 1.4× | 4.1% | PASS |
| mem_reads | `types_table_scan` | 908.51ms | 964.94ms | 1.1× | 1.4% | PASS |
| mem_reads | `table_scan` | 1.07s | 1.05s | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_read_only` | 87.83ms | 92.67ms | 1.1× | 1.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 134.96ms | 205.98ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 11.71ms | 21.64ms | 1.8× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 39.58ms | 77.80ms | 2.0× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 28.41ms | 48.59ms | 1.7× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 34.00ms | 58.66ms | 1.7× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 17.07ms | 38.41ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 18.78ms | 29.98ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 49.34ms | 85.39ms | 1.7× | 0.7% | PASS |
| file_reads | `oltp_point_select` | 86.37ms | 38.15ms | 0.4× | 0.5% | PASS |
| file_reads | `oltp_range_select` | 16.07ms | 10.55ms | 0.7× | 0.9% | PASS |
| file_reads | `oltp_sum_range` | 14.99ms | 10.38ms | 0.7× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 3.03ms | 2.52ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 3.77ms | 3.38ms | 0.9× | 0.8% | PASS |
| file_reads | `oltp_index_scan` | 10.20ms | 5.87ms | 0.6× | 0.9% | PASS |
| file_reads | `select_random_points` | 15.74ms | 10.94ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_ranges` | 9.63ms | 4.83ms | 0.5× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 10.54ms | 5.15ms | 0.5× | 0.9% | PASS |
| file_reads | `groupby_scan` | 25.27ms | 27.37ms | 1.1× | 0.6% | PASS |
| file_reads | `index_join` | 8.59ms | 7.51ms | 0.9× | 1.1% | PASS |
| file_reads | `index_join_scan` | 3.57ms | 4.11ms | 1.2× | 2.1% | PASS |
| file_reads | `types_table_scan` | 848.07ms | 943.85ms | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.07s | 1.05s | 1.0× | 2.6% | PASS |
| file_reads | `oltp_read_only` | 180.59ms | 113.27ms | 0.6× | 1.6% | PASS |
| file_writes | `oltp_bulk_insert` | 178.35ms | 248.90ms | 1.4× | 2.8% | PASS |
| file_writes | `oltp_insert` | 27.81ms | 32.55ms | 1.2× | 4.9% | PASS |
| file_writes | `oltp_update_index` | 135.30ms | 134.61ms | 1.0× | 2.8% | PASS |
| file_writes | `oltp_update_non_index` | 110.72ms | 94.51ms | 0.9× | 1.4% | PASS |
| file_writes | `oltp_delete_insert` | 116.85ms | 108.82ms | 0.9× | 1.3% | PASS |
| file_writes | `oltp_write_only` | 86.65ms | 78.24ms | 0.9× | 2.1% | PASS |
| file_writes | `types_delete_insert` | 72.84ms | 55.97ms | 0.8× | 5.5% | PASS |
| file_writes | `oltp_read_write` | 120.59ms | 124.77ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 39.35ms | 38.22ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_range_select` | 11.37ms | 10.58ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 10.15ms | 10.36ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 2.56ms | 2.53ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 3.32ms | 3.38ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 5.64ms | 5.93ms | 1.1× | 0.9% | PASS |
| ac_reads | `select_random_points` | 11.79ms | 11.10ms | 0.9× | 3.8% | PASS |
| ac_reads | `select_random_ranges` | 5.04ms | 4.87ms | 1.0× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 5.86ms | 5.25ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 24.91ms | 27.42ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 6.23ms | 7.74ms | 1.2× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 3.19ms | 4.20ms | 1.3× | 2.2% | PASS |
| ac_reads | `types_table_scan` | 847.58ms | 945.24ms | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 962.95ms | 1.03s | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 114.25ms | 113.98ms | 1.0× | 2.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.40ms | 52.95ms | 3.0× | 33.3% | PASS |
| ac_writes | `oltp_insert_ac` | 19.20ms | 70.16ms | 3.7× | 26.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.25ms | 71.62ms | 3.5× | 13.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.17ms | 54.03ms | 3.3× | 12.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 17.98ms | 62.07ms | 3.5× | 6.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 18.15ms | 58.71ms | 3.2× | 8.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 16.12ms | 51.35ms | 3.2× | 11.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 22.10ms | 64.87ms | 2.9× | 7.1% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 38.60ms | 39.18ms | 1.0× | 2.8% | PASS |
| mem_reads | `oltp_range_select` | 18.96ms | 14.76ms | 0.8× | 3.2% | PASS |
| mem_reads | `oltp_sum_range` | 15.35ms | 13.68ms | 0.9× | 2.1% | PASS |
| mem_reads | `oltp_order_range` | 3.35ms | 3.35ms | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.55ms | 4.59ms | 1.0× | 2.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.03ms | 6.19ms | 1.5× | 1.7% | PASS |
| mem_reads | `select_random_points` | 23.67ms | 21.42ms | 0.9× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 3.56ms | 5.34ms | 1.5× | 2.1% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.86ms | 1.2× | 1.3% | PASS |
| mem_reads | `groupby_scan` | 36.33ms | 35.86ms | 1.0× | 1.4% | PASS |
| mem_reads | `index_join` | 11.41ms | 10.00ms | 0.9× | 3.0% | PASS |
| mem_reads | `index_join_scan` | 3.97ms | 5.53ms | 1.4× | 3.5% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.20s | 1.1× | 2.0% | PASS |
| mem_reads | `table_scan` | 1.28s | 1.32s | 1.0× | 5.3% | PASS |
| mem_reads | `oltp_read_only` | 155.87ms | 144.80ms | 0.9× | 1.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 234.81ms | 381.89ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 17.91ms | 41.32ms | 2.3× | 0.9% | FAIL |
| mem_writes | `oltp_update_index` | 73.41ms | 161.88ms | 2.2× | 1.9% | PASS |
| mem_writes | `oltp_update_non_index` | 54.11ms | 95.33ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 59.72ms | 116.73ms | 2.0× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 32.10ms | 71.22ms | 2.2× | 1.9% | PASS |
| mem_writes | `types_delete_insert` | 43.12ms | 62.20ms | 1.4× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 119.46ms | 162.73ms | 1.4× | 1.8% | PASS |
| file_reads | `oltp_point_select` | 110.31ms | 57.85ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 27.92ms | 16.96ms | 0.6× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 25.78ms | 16.78ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 5.06ms | 4.20ms | 0.8× | 3.3% | PASS |
| file_reads | `oltp_distinct_range` | 6.13ms | 5.18ms | 0.8× | 2.6% | PASS |
| file_reads | `oltp_index_scan` | 11.64ms | 8.46ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 32.70ms | 24.48ms | 0.7× | 2.5% | PASS |
| file_reads | `select_random_ranges` | 10.75ms | 7.31ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 12.10ms | 6.81ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 37.32ms | 36.00ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 16.19ms | 11.43ms | 0.7× | 2.5% | PASS |
| file_reads | `index_join_scan` | 5.07ms | 6.03ms | 1.2× | 2.6% | PASS |
| file_reads | `types_table_scan` | 1.13s | 1.22s | 1.1× | 4.3% | PASS |
| file_reads | `table_scan` | 1.38s | 1.33s | 1.0× | 3.4% | PASS |
| file_reads | `oltp_read_only` | 262.66ms | 172.28ms | 0.7× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 260.76ms | 396.68ms | 1.5× | 1.3% | PASS |
| file_writes | `oltp_insert` | 25.74ms | 48.06ms | 1.9× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 137.90ms | 173.88ms | 1.3× | 7.1% | PASS |
| file_writes | `oltp_update_non_index` | 98.64ms | 109.24ms | 1.1× | 8.7% | PASS |
| file_writes | `oltp_delete_insert` | 99.52ms | 127.61ms | 1.3× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 89.30ms | 81.63ms | 0.9× | 8.3% | PASS |
| file_writes | `types_delete_insert` | 76.64ms | 70.57ms | 0.9× | 3.5% | PASS |
| file_writes | `oltp_read_write` | 175.21ms | 177.06ms | 1.0× | 8.1% | PASS |
| ac_reads | `oltp_point_select` | 64.92ms | 59.65ms | 0.9× | 2.9% | PASS |
| ac_reads | `oltp_range_select` | 21.58ms | 16.80ms | 0.8× | 2.8% | PASS |
| ac_reads | `oltp_sum_range` | 19.88ms | 16.43ms | 0.8× | 3.3% | PASS |
| ac_reads | `oltp_order_range` | 4.20ms | 3.81ms | 0.9× | 2.5% | PASS |
| ac_reads | `oltp_distinct_range` | 5.45ms | 5.09ms | 0.9× | 3.1% | PASS |
| ac_reads | `oltp_index_scan` | 7.08ms | 8.74ms | 1.2× | 3.6% | PASS |
| ac_reads | `select_random_points` | 28.50ms | 26.86ms | 0.9× | 7.3% | PASS |
| ac_reads | `select_random_ranges` | 6.21ms | 7.30ms | 1.2× | 2.5% | PASS |
| ac_reads | `covering_index_scan` | 7.65ms | 6.96ms | 0.9× | 2.6% | PASS |
| ac_reads | `groupby_scan` | 37.54ms | 36.83ms | 1.0× | 1.6% | PASS |
| ac_reads | `index_join` | 14.78ms | 12.72ms | 0.9× | 6.0% | PASS |
| ac_reads | `index_join_scan` | 5.09ms | 6.76ms | 1.3× | 7.7% | PASS |
| ac_reads | `types_table_scan` | 1.31s | 1.28s | 1.0× | 1.3% | PASS |
| ac_reads | `table_scan` | 1.50s | 1.39s | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 206.12ms | 186.60ms | 0.9× | 2.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.38ms | 58.89ms | 2.6× | 7.5% | PASS |
| ac_writes | `oltp_insert_ac` | 26.24ms | 72.89ms | 2.8× | 5.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.60ms | 91.00ms | 3.3× | 8.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.68ms | 66.91ms | 3.1× | 6.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.40ms | 76.20ms | 3.1× | 5.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.15ms | 79.25ms | 3.3× | 7.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.32ms | 71.19ms | 3.2× | 8.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.70ms | 84.98ms | 2.7× | 6.9% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 28.81ms | 28.35ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 14.35ms | 11.02ms | 0.8× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 12.74ms | 10.69ms | 0.8× | 1.0% | PASS |
| mem_reads | `oltp_order_range` | 2.81ms | 2.60ms | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.52ms | 3.46ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.23ms | 4.90ms | 1.5× | 1.9% | PASS |
| mem_reads | `select_random_points` | 18.01ms | 16.21ms | 0.9× | 3.2% | PASS |
| mem_reads | `select_random_ranges` | 2.78ms | 4.25ms | 1.5× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 3.29ms | 3.65ms | 1.1× | 3.4% | PASS |
| mem_reads | `groupby_scan` | 28.20ms | 28.53ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 8.91ms | 7.47ms | 0.8× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 3.18ms | 4.73ms | 1.5× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 990.50ms | 1.02s | 1.0× | 2.4% | PASS |
| mem_reads | `table_scan` | 1.15s | 1.12s | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 120.65ms | 108.95ms | 0.9× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 177.97ms | 277.51ms | 1.6× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 14.12ms | 30.86ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 52.23ms | 114.40ms | 2.2× | 1.7% | PASS |
| mem_writes | `oltp_update_non_index` | 39.80ms | 67.91ms | 1.7× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 42.33ms | 84.73ms | 2.0× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 22.34ms | 50.73ms | 2.3× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 29.69ms | 42.02ms | 1.4× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 74.16ms | 109.78ms | 1.5× | 2.3% | PASS |
| file_reads | `oltp_point_select` | 95.91ms | 44.15ms | 0.5× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 20.96ms | 12.48ms | 0.6× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 19.49ms | 12.24ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.56ms | 2.82ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_distinct_range` | 4.35ms | 3.68ms | 0.8× | 0.6% | PASS |
| file_reads | `oltp_index_scan` | 10.36ms | 6.64ms | 0.6× | 0.6% | PASS |
| file_reads | `select_random_points` | 24.57ms | 17.66ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_ranges` | 9.65ms | 5.94ms | 0.6× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 10.57ms | 5.55ms | 0.5× | 0.7% | PASS |
| file_reads | `groupby_scan` | 29.05ms | 28.79ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 13.31ms | 8.83ms | 0.7× | 1.9% | PASS |
| file_reads | `index_join_scan` | 4.15ms | 5.29ms | 1.3× | 3.2% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.03s | 1.0× | 1.1% | PASS |
| file_reads | `table_scan` | 1.04s | 1.06s | 1.0× | 2.7% | PASS |
| file_reads | `oltp_read_only` | 211.28ms | 128.33ms | 0.6× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 245.06ms | 344.94ms | 1.4× | 4.0% | PASS |
| file_writes | `oltp_insert` | 31.20ms | 54.10ms | 1.7× | 6.6% | PASS |
| file_writes | `oltp_update_index` | 162.97ms | 191.25ms | 1.2× | 2.8% | PASS |
| file_writes | `oltp_update_non_index` | 138.53ms | 127.06ms | 0.9× | 3.6% | PASS |
| file_writes | `oltp_delete_insert` | 164.14ms | 153.22ms | 0.9× | 8.8% | PASS |
| file_writes | `oltp_write_only` | 118.55ms | 106.69ms | 0.9× | 4.3% | PASS |
| file_writes | `types_delete_insert` | 122.93ms | 84.79ms | 0.7× | 8.7% | PASS |
| file_writes | `oltp_read_write` | 183.34ms | 168.78ms | 0.9× | 11.4% | PASS |
| ac_reads | `oltp_point_select` | 49.43ms | 44.44ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 16.55ms | 12.50ms | 0.8× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 15.00ms | 12.26ms | 0.8× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 3.18ms | 2.81ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 3.92ms | 3.69ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 5.73ms | 6.62ms | 1.2× | 0.9% | PASS |
| ac_reads | `select_random_points` | 20.36ms | 17.86ms | 0.9× | 2.1% | PASS |
| ac_reads | `select_random_ranges` | 5.01ms | 5.92ms | 1.2× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 5.92ms | 5.54ms | 0.9× | 0.6% | PASS |
| ac_reads | `groupby_scan` | 28.51ms | 28.68ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 10.73ms | 8.76ms | 0.8× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.65ms | 4.92ms | 1.3× | 1.1% | PASS |
| ac_reads | `types_table_scan` | 926.16ms | 983.92ms | 1.1× | 2.8% | PASS |
| ac_reads | `table_scan` | 1.03s | 1.07s | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 142.13ms | 128.50ms | 0.9× | 0.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.38ms | 48.90ms | 2.5× | 12.7% | PASS |
| ac_writes | `oltp_insert_ac` | 21.38ms | 63.06ms | 2.9× | 13.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.44ms | 74.45ms | 3.5× | 10.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 19.98ms | 69.91ms | 3.5× | 52.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 20.17ms | 66.74ms | 3.3× | 11.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 20.81ms | 66.61ms | 3.2× | 10.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.90ms | 61.76ms | 3.3× | 13.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.36ms | 68.76ms | 2.7× | 6.9% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 26.79ms | 30.06ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 15.91ms | 16.41ms | 1.0× | 2.0% | PASS |
| mem_reads | `oltp_sum_range` | 14.07ms | 16.13ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 2.99ms | 3.13ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.76ms | 3.99ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.75ms | 4.95ms | 1.3× | 1.3% | PASS |
| mem_reads | `select_random_points` | 20.68ms | 23.65ms | 1.1× | 1.7% | PASS |
| mem_reads | `select_random_ranges` | 5.94ms | 6.57ms | 1.1× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 3.31ms | 3.56ms | 1.1× | 3.3% | PASS |
| mem_reads | `groupby_scan` | 29.52ms | 32.59ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 6.24ms | 8.22ms | 1.3× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 3.31ms | 4.66ms | 1.4× | 2.2% | PASS |
| mem_reads | `types_table_scan` | 905.61ms | 982.92ms | 1.1× | 1.8% | PASS |
| mem_reads | `table_scan` | 1.16s | 1.08s | 0.9× | 2.2% | PASS |
| mem_reads | `oltp_read_only` | 119.44ms | 130.67ms | 1.1× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.35ms | 263.59ms | 1.4× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 14.62ms | 27.52ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 54.87ms | 103.82ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 41.70ms | 67.38ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 38.43ms | 75.20ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 21.50ms | 47.43ms | 2.2× | 1.1% | PASS |
| mem_writes | `types_delete_insert` | 25.56ms | 40.85ms | 1.6× | 2.0% | PASS |
| mem_writes | `oltp_read_write` | 77.44ms | 121.10ms | 1.6× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 94.11ms | 46.09ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 23.34ms | 18.20ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 20.85ms | 17.62ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 3.77ms | 3.33ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.55ms | 4.18ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 10.88ms | 6.54ms | 0.6× | 1.0% | PASS |
| file_reads | `select_random_points` | 27.93ms | 25.52ms | 0.9× | 1.1% | PASS |
| file_reads | `select_random_ranges` | 13.06ms | 8.41ms | 0.6× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 10.62ms | 5.38ms | 0.5× | 0.9% | PASS |
| file_reads | `groupby_scan` | 30.21ms | 32.72ms | 1.1× | 0.5% | PASS |
| file_reads | `index_join` | 10.28ms | 9.58ms | 0.9× | 0.8% | PASS |
| file_reads | `index_join_scan` | 4.15ms | 4.97ms | 1.2× | 1.6% | PASS |
| file_reads | `types_table_scan` | 892.50ms | 976.60ms | 1.1× | 1.3% | PASS |
| file_reads | `table_scan` | 1.03s | 1.05s | 1.0× | 3.0% | PASS |
| file_reads | `oltp_read_only` | 213.35ms | 152.04ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 232.53ms | 315.50ms | 1.4× | 1.8% | PASS |
| file_writes | `oltp_insert` | 31.66ms | 44.95ms | 1.4× | 5.2% | PASS |
| file_writes | `oltp_update_index` | 163.18ms | 165.53ms | 1.0× | 2.8% | PASS |
| file_writes | `oltp_update_non_index` | 136.77ms | 117.44ms | 0.9× | 3.2% | PASS |
| file_writes | `oltp_delete_insert` | 134.78ms | 131.90ms | 1.0× | 4.1% | PASS |
| file_writes | `oltp_write_only` | 98.72ms | 93.58ms | 0.9× | 3.9% | PASS |
| file_writes | `types_delete_insert` | 88.31ms | 73.85ms | 0.8× | 6.6% | PASS |
| file_writes | `oltp_read_write` | 152.92ms | 169.38ms | 1.1× | 3.6% | PASS |
| ac_reads | `oltp_point_select` | 46.81ms | 45.77ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 17.97ms | 18.02ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_sum_range` | 16.09ms | 17.63ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_order_range` | 3.34ms | 3.32ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_distinct_range` | 4.09ms | 4.18ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.17ms | 6.52ms | 1.1× | 0.7% | PASS |
| ac_reads | `select_random_points` | 23.05ms | 25.43ms | 1.1× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 8.25ms | 8.40ms | 1.0× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 5.93ms | 5.43ms | 0.9× | 1.0% | PASS |
| ac_reads | `groupby_scan` | 29.73ms | 32.73ms | 1.1× | 1.2% | PASS |
| ac_reads | `index_join` | 7.84ms | 9.46ms | 1.2× | 1.2% | PASS |
| ac_reads | `index_join_scan` | 3.67ms | 4.91ms | 1.3× | 1.1% | PASS |
| ac_reads | `types_table_scan` | 891.51ms | 973.81ms | 1.1× | 1.8% | PASS |
| ac_reads | `table_scan` | 1.17s | 1.08s | 0.9× | 3.6% | PASS |
| ac_reads | `oltp_read_only` | 151.15ms | 154.15ms | 1.0× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.16ms | 55.02ms | 2.9× | 19.1% | PASS |
| ac_writes | `oltp_insert_ac` | 20.34ms | 68.91ms | 3.4× | 11.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 22.27ms | 77.85ms | 3.5× | 23.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 18.96ms | 62.44ms | 3.3× | 17.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 20.27ms | 72.49ms | 3.6× | 13.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.99ms | 68.95ms | 3.4× | 12.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.57ms | 63.25ms | 3.4× | 21.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.40ms | 73.83ms | 2.9× | 10.9% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 21s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 54.61ms | 130.00ms | 42.0% | 0.7% | PASS |
| `status_dirty_many_tables` | 57.51ms | 130.00ms | 44.2% | 0.9% | PASS |
| `diff_regular_working_one_table` | 51.85ms | 120.00ms | 43.2% | 0.9% | PASS |
| `diff_regular_working_many_tables` | 59.40ms | 140.00ms | 42.4% | 0.9% | PASS |
| `diff_stat_working_many_tables` | 59.55ms | 140.00ms | 42.5% | 1.0% | PASS |
| `diff_schema_working_many_tables` | 59.80ms | 140.00ms | 42.7% | 0.6% | PASS |
| `branch_list_many_branches` | 12.51ms | 35.00ms | 35.7% | 1.2% | PASS |
| `branch_create_delete` | 13.88ms | 40.00ms | 34.7% | 1.1% | PASS |
| `checkout_branch_clean` | 36.44ms | 150.00ms | 24.3% | 2.7% | PASS |
| `merge_data_no_conflicts` | 16.04ms | 50.00ms | 32.1% | 0.9% | PASS |
| `merge_schema_no_conflicts` | 12.17ms | 35.00ms | 34.8% | 1.6% | PASS |
| `merge_data_conflicts` | 18.10ms | 180.00ms | 10.1% | 0.7% | PASS |
| `merge_data_conflicts_with_resolve` | 18.23ms | 180.00ms | 10.1% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
