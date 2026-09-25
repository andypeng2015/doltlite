# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-25 16:16 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260920.314.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/36149203329)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.46s | 11.53s | 1.0× | 1.8% | **PASS** |
| Writes | 2.29s | 3.75s | 1.6× | 1.9% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 12.27s | 11.67s | 1.0× | 1.6% | **PASS** |
| Writes | 3.22s | 4.07s | 1.3× | 1.9% | **PASS** |
| Autocommit writes | 871.04ms | 2.63s | 3.0× | 6.4% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.57s | 2.63s | 1.0× | 2.1% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.96s | 2.96s | 1.0× | 1.7% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 3.04s | 3.02s | 1.0× | 1.7% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.89s | 2.92s | 1.0× | 1.6% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 454.25ms | 747.78ms | 1.6× | 2.4% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 610.77ms | 1.00s | 1.6× | 1.5% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 611.62ms | 1.00s | 1.6× | 2.2% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 615.46ms | 1.00s | 1.6× | 1.8% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.83s | 2.71s | 1.0× | 2.1% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.10s | 2.97s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.22s | 3.02s | 0.9× | 1.7% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.12s | 2.98s | 1.0× | 1.6% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 626.90ms | 823.05ms | 1.3× | 2.9% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 940.28ms | 1.09s | 1.2× | 3.4% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 832.58ms | 1.08s | 1.3× | 1.8% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 819.01ms | 1.08s | 1.3× | 1.8% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.68s | 2.72s | 1.0× | 1.9% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 3.01s | 3.00s | 1.0× | 1.2% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.95s | 2.99s | 1.0× | 1.1% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.90s | 2.96s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 223.32ms | 713.07ms | 3.2× | 9.2% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 158.01ms | 501.90ms | 3.2× | 5.1% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 156.71ms | 505.38ms | 3.2× | 4.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 333.00ms | 908.75ms | 2.7× | 7.5% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 25.16ms | 30.43ms | 1.2× | 2.7% | PASS |
| mem_reads | `oltp_range_select` | 10.85ms | 11.70ms | 1.1× | 3.7% | PASS |
| mem_reads | `oltp_sum_range` | 10.02ms | 11.41ms | 1.1× | 2.2% | PASS |
| mem_reads | `oltp_order_range` | 2.68ms | 2.85ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.70ms | 3.95ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 3.96ms | 5.38ms | 1.4× | 2.1% | PASS |
| mem_reads | `select_random_points` | 10.68ms | 11.20ms | 1.0× | 3.3% | PASS |
| mem_reads | `select_random_ranges` | 3.09ms | 4.03ms | 1.3× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 4.25ms | 4.34ms | 1.0× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 30.80ms | 33.12ms | 1.1× | 1.1% | PASS |
| mem_reads | `index_join` | 5.86ms | 8.05ms | 1.4× | 2.7% | PASS |
| mem_reads | `index_join_scan` | 3.28ms | 4.63ms | 1.4× | 3.6% | PASS |
| mem_reads | `types_table_scan` | 1.09s | 1.14s | 1.0× | 1.3% | PASS |
| mem_reads | `table_scan` | 1.26s | 1.24s | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_read_only` | 107.32ms | 119.88ms | 1.1× | 2.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 185.24ms | 276.68ms | 1.5× | 1.6% | PASS |
| mem_writes | `oltp_insert` | 15.50ms | 28.74ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 51.72ms | 99.83ms | 1.9× | 2.5% | PASS |
| mem_writes | `oltp_update_non_index` | 36.61ms | 61.95ms | 1.7× | 2.8% | PASS |
| mem_writes | `oltp_delete_insert` | 45.80ms | 76.00ms | 1.7× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 22.56ms | 48.50ms | 2.1× | 2.2% | PASS |
| mem_writes | `types_delete_insert` | 25.70ms | 39.18ms | 1.5× | 2.9% | PASS |
| mem_writes | `oltp_read_write` | 71.12ms | 116.91ms | 1.6× | 3.5% | PASS |
| file_reads | `oltp_point_select` | 99.92ms | 50.23ms | 0.5× | 1.4% | PASS |
| file_reads | `oltp_range_select` | 18.91ms | 13.94ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 18.05ms | 13.62ms | 0.8× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 3.62ms | 3.16ms | 0.9× | 3.1% | PASS |
| file_reads | `oltp_distinct_range` | 4.64ms | 4.37ms | 0.9× | 3.0% | PASS |
| file_reads | `oltp_index_scan` | 11.90ms | 7.74ms | 0.7× | 2.3% | PASS |
| file_reads | `select_random_points` | 18.93ms | 13.77ms | 0.7× | 2.9% | PASS |
| file_reads | `select_random_ranges` | 10.67ms | 6.03ms | 0.6× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 12.28ms | 6.76ms | 0.6× | 2.1% | PASS |
| file_reads | `groupby_scan` | 32.16ms | 33.77ms | 1.0× | 1.7% | PASS |
| file_reads | `index_join` | 10.26ms | 9.99ms | 1.0× | 2.1% | PASS |
| file_reads | `index_join_scan` | 4.34ms | 5.05ms | 1.2× | 2.8% | PASS |
| file_reads | `types_table_scan` | 1.09s | 1.14s | 1.0× | 1.4% | PASS |
| file_reads | `table_scan` | 1.27s | 1.25s | 1.0× | 1.5% | PASS |
| file_reads | `oltp_read_only` | 221.74ms | 151.00ms | 0.7× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 199.67ms | 289.05ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_insert` | 22.92ms | 32.83ms | 1.4× | 2.3% | PASS |
| file_writes | `oltp_update_index` | 80.51ms | 109.91ms | 1.4× | 3.2% | PASS |
| file_writes | `oltp_update_non_index` | 60.72ms | 72.41ms | 1.2× | 2.9% | PASS |
| file_writes | `oltp_delete_insert` | 73.00ms | 87.92ms | 1.2× | 2.9% | PASS |
| file_writes | `oltp_write_only` | 48.12ms | 57.83ms | 1.2× | 3.4% | PASS |
| file_writes | `types_delete_insert` | 41.61ms | 46.45ms | 1.1× | 2.9% | PASS |
| file_writes | `oltp_read_write` | 100.37ms | 126.65ms | 1.3× | 3.1% | PASS |
| ac_reads | `oltp_point_select` | 49.91ms | 50.94ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 13.91ms | 13.97ms | 1.0× | 3.9% | PASS |
| ac_reads | `oltp_sum_range` | 12.91ms | 13.65ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_order_range` | 3.08ms | 3.19ms | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.11ms | 4.38ms | 1.1× | 2.0% | PASS |
| ac_reads | `oltp_index_scan` | 6.88ms | 7.81ms | 1.1× | 1.7% | PASS |
| ac_reads | `select_random_points` | 13.82ms | 13.68ms | 1.0× | 4.1% | PASS |
| ac_reads | `select_random_ranges` | 5.75ms | 6.09ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 7.29ms | 6.75ms | 0.9× | 2.4% | PASS |
| ac_reads | `groupby_scan` | 31.47ms | 34.01ms | 1.1× | 1.1% | PASS |
| ac_reads | `index_join` | 7.86ms | 10.17ms | 1.3× | 3.3% | PASS |
| ac_reads | `index_join_scan` | 3.85ms | 5.07ms | 1.3× | 3.7% | PASS |
| ac_reads | `types_table_scan` | 1.11s | 1.15s | 1.0× | 1.0% | PASS |
| ac_reads | `table_scan` | 1.26s | 1.25s | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_read_only` | 145.93ms | 152.65ms | 1.0× | 1.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.72ms | 73.85ms | 3.0× | 10.8% | PASS |
| ac_writes | `oltp_insert_ac` | 28.04ms | 88.35ms | 3.2× | 9.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.92ms | 102.26ms | 3.4× | 9.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 26.32ms | 80.89ms | 3.1× | 7.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.20ms | 92.13ms | 3.3× | 9.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.82ms | 91.81ms | 3.3× | 8.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.72ms | 85.94ms | 3.3× | 7.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.58ms | 97.83ms | 3.0× | 9.6% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.13ms | 35.71ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 18.21ms | 13.95ms | 0.8× | 2.4% | PASS |
| mem_reads | `oltp_sum_range` | 16.93ms | 13.59ms | 0.8× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 3.55ms | 3.23ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.57ms | 4.33ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 4.08ms | 5.90ms | 1.4× | 1.3% | PASS |
| mem_reads | `select_random_points` | 22.93ms | 20.24ms | 0.9× | 2.5% | PASS |
| mem_reads | `select_random_ranges` | 3.69ms | 5.34ms | 1.4× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 4.32ms | 4.84ms | 1.1× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 37.67ms | 37.16ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 11.37ms | 9.15ms | 0.8× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 4.04ms | 5.78ms | 1.4× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 1.20s | 1.26s | 1.1× | 2.3% | PASS |
| mem_reads | `table_scan` | 1.45s | 1.41s | 1.0× | 2.0% | PASS |
| mem_reads | `oltp_read_only` | 143.39ms | 132.52ms | 0.9× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 244.49ms | 355.05ms | 1.5× | 0.5% | PASS |
| mem_writes | `oltp_insert` | 18.45ms | 39.26ms | 2.1× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 69.68ms | 150.42ms | 2.2× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 51.72ms | 87.54ms | 1.7× | 2.4% | PASS |
| mem_writes | `oltp_delete_insert` | 56.82ms | 108.23ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 29.05ms | 64.90ms | 2.2× | 1.0% | PASS |
| mem_writes | `types_delete_insert` | 40.04ms | 53.92ms | 1.3× | 2.0% | PASS |
| mem_writes | `oltp_read_write` | 100.52ms | 144.74ms | 1.4× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 134.48ms | 59.04ms | 0.4× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 27.32ms | 16.25ms | 0.6× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 25.45ms | 15.95ms | 0.6× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 4.48ms | 3.53ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 5.61ms | 4.64ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 13.82ms | 8.54ms | 0.6× | 0.9% | PASS |
| file_reads | `select_random_points` | 32.77ms | 22.93ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_ranges` | 13.28ms | 7.73ms | 0.6× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 14.06ms | 7.26ms | 0.5× | 1.2% | PASS |
| file_reads | `groupby_scan` | 38.03ms | 37.38ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 16.11ms | 10.94ms | 0.7× | 1.6% | PASS |
| file_reads | `index_join_scan` | 4.98ms | 6.06ms | 1.2× | 1.8% | PASS |
| file_reads | `types_table_scan` | 1.17s | 1.24s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.32s | 1.37s | 1.0× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 280.66ms | 165.68ms | 0.6× | 0.6% | PASS |
| file_writes | `oltp_bulk_insert` | 271.13ms | 367.65ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_insert` | 26.00ms | 45.54ms | 1.8× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 131.15ms | 163.89ms | 1.2× | 7.7% | PASS |
| file_writes | `oltp_update_non_index` | 101.37ms | 100.97ms | 1.0× | 11.3% | PASS |
| file_writes | `oltp_delete_insert` | 98.60ms | 122.15ms | 1.2× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 91.39ms | 75.40ms | 0.8× | 13.5% | PASS |
| file_writes | `types_delete_insert` | 75.30ms | 62.62ms | 0.8× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 145.35ms | 154.66ms | 1.1× | 4.9% | PASS |
| ac_reads | `oltp_point_select` | 68.33ms | 59.19ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 21.61ms | 16.30ms | 0.8× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 20.18ms | 16.06ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 4.00ms | 3.53ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_distinct_range` | 5.04ms | 4.64ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 7.65ms | 8.64ms | 1.1× | 0.9% | PASS |
| ac_reads | `select_random_points` | 27.11ms | 23.32ms | 0.9× | 1.6% | PASS |
| ac_reads | `select_random_ranges` | 7.00ms | 7.81ms | 1.1× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 7.95ms | 7.33ms | 0.9× | 0.6% | PASS |
| ac_reads | `groupby_scan` | 37.85ms | 37.67ms | 1.0× | 1.2% | PASS |
| ac_reads | `index_join` | 13.65ms | 11.00ms | 0.8× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 4.53ms | 6.09ms | 1.3× | 1.6% | PASS |
| ac_reads | `types_table_scan` | 1.23s | 1.25s | 1.0× | 2.3% | PASS |
| ac_reads | `table_scan` | 1.37s | 1.38s | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_read_only` | 188.32ms | 166.01ms | 0.9× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.42ms | 47.75ms | 2.7× | 4.3% | PASS |
| ac_writes | `oltp_insert_ac` | 19.41ms | 59.14ms | 3.0× | 4.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.12ms | 75.51ms | 3.6× | 5.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.25ms | 56.66ms | 3.3× | 5.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 20.20ms | 66.88ms | 3.3× | 5.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.44ms | 65.67ms | 3.4× | 5.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 17.00ms | 58.49ms | 3.4× | 5.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 26.17ms | 71.80ms | 2.7× | 3.6% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.75ms | 35.79ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 17.34ms | 13.71ms | 0.8× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 15.93ms | 13.34ms | 0.8× | 1.8% | PASS |
| mem_reads | `oltp_order_range` | 3.50ms | 3.21ms | 0.9× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 4.50ms | 4.34ms | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 4.07ms | 6.00ms | 1.5× | 1.7% | PASS |
| mem_reads | `select_random_points` | 21.52ms | 20.09ms | 0.9× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 3.51ms | 5.36ms | 1.5× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 4.29ms | 4.75ms | 1.1× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 37.25ms | 37.16ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 10.92ms | 9.51ms | 0.9× | 3.2% | PASS |
| mem_reads | `index_join_scan` | 3.94ms | 5.85ms | 1.5× | 2.7% | PASS |
| mem_reads | `types_table_scan` | 1.28s | 1.30s | 1.0× | 1.9% | PASS |
| mem_reads | `table_scan` | 1.45s | 1.43s | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 147.07ms | 135.40ms | 0.9× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 246.28ms | 353.18ms | 1.4× | 1.7% | PASS |
| mem_writes | `oltp_insert` | 18.94ms | 39.18ms | 2.1× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 68.90ms | 146.43ms | 2.1× | 2.2% | PASS |
| mem_writes | `oltp_update_non_index` | 51.07ms | 85.73ms | 1.7× | 2.2% | PASS |
| mem_writes | `oltp_delete_insert` | 54.74ms | 108.62ms | 2.0× | 2.2% | PASS |
| mem_writes | `oltp_write_only` | 29.64ms | 66.01ms | 2.2× | 1.8% | PASS |
| mem_writes | `types_delete_insert` | 39.68ms | 54.17ms | 1.4× | 2.2% | PASS |
| mem_writes | `oltp_read_write` | 102.38ms | 148.39ms | 1.4× | 2.9% | PASS |
| file_reads | `oltp_point_select` | 134.43ms | 59.49ms | 0.4× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 28.16ms | 16.16ms | 0.6× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 27.08ms | 16.03ms | 0.6× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 4.47ms | 3.50ms | 0.8× | 2.4% | PASS |
| file_reads | `oltp_distinct_range` | 5.59ms | 4.63ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 14.00ms | 8.57ms | 0.6× | 1.2% | PASS |
| file_reads | `select_random_points` | 33.35ms | 23.11ms | 0.7× | 3.0% | PASS |
| file_reads | `select_random_ranges` | 13.31ms | 7.82ms | 0.6× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 14.39ms | 7.32ms | 0.5× | 0.8% | PASS |
| file_reads | `groupby_scan` | 38.50ms | 37.51ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join` | 16.44ms | 11.02ms | 0.7× | 2.5% | PASS |
| file_reads | `index_join_scan` | 5.03ms | 6.02ms | 1.2× | 2.0% | PASS |
| file_reads | `types_table_scan` | 1.22s | 1.26s | 1.0× | 1.7% | PASS |
| file_reads | `table_scan` | 1.38s | 1.39s | 1.0× | 1.9% | PASS |
| file_reads | `oltp_read_only` | 286.90ms | 168.80ms | 0.6× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 267.88ms | 364.00ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 25.52ms | 45.66ms | 1.8× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 100.11ms | 157.97ms | 1.6× | 1.3% | PASS |
| file_writes | `oltp_update_non_index` | 88.54ms | 97.41ms | 1.1× | 8.8% | PASS |
| file_writes | `oltp_delete_insert` | 90.07ms | 118.97ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 58.29ms | 73.46ms | 1.3× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 66.66ms | 61.59ms | 0.9× | 2.0% | PASS |
| file_writes | `oltp_read_write` | 135.50ms | 156.91ms | 1.2× | 1.9% | PASS |
| ac_reads | `oltp_point_select` | 68.82ms | 59.75ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 21.11ms | 16.06ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 19.69ms | 15.90ms | 0.8× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.94ms | 3.51ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_distinct_range` | 4.98ms | 4.61ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 7.64ms | 8.58ms | 1.1× | 0.8% | PASS |
| ac_reads | `select_random_points` | 26.90ms | 23.53ms | 0.9× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 6.88ms | 7.80ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.91ms | 7.29ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 37.77ms | 37.48ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 13.35ms | 10.95ms | 0.8× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 4.48ms | 5.98ms | 1.3× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 1.19s | 1.25s | 1.0× | 1.2% | PASS |
| ac_reads | `table_scan` | 1.34s | 1.38s | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_read_only` | 189.11ms | 167.36ms | 0.9× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 16.52ms | 47.26ms | 2.9× | 4.4% | PASS |
| ac_writes | `oltp_insert_ac` | 19.64ms | 64.70ms | 3.3× | 4.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.91ms | 73.29ms | 3.5× | 3.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.17ms | 56.03ms | 3.3× | 4.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 19.42ms | 66.46ms | 3.4× | 4.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.22ms | 66.27ms | 3.4× | 5.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 17.26ms | 60.02ms | 3.5× | 8.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 26.57ms | 71.35ms | 2.7× | 5.6% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.52ms | 42.81ms | 1.2× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 20.90ms | 21.73ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 18.78ms | 21.16ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 3.69ms | 3.88ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.79ms | 5.09ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 4.71ms | 6.56ms | 1.4× | 2.2% | PASS |
| mem_reads | `select_random_points` | 29.40ms | 34.07ms | 1.2× | 1.9% | PASS |
| mem_reads | `select_random_ranges` | 8.10ms | 9.26ms | 1.1× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.32ms | 4.83ms | 1.1× | 2.7% | PASS |
| mem_reads | `groupby_scan` | 36.91ms | 39.99ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 8.10ms | 11.33ms | 1.4× | 2.9% | PASS |
| mem_reads | `index_join_scan` | 4.01ms | 5.74ms | 1.4× | 3.0% | PASS |
| mem_reads | `types_table_scan` | 1.16s | 1.22s | 1.0× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.39s | 1.32s | 0.9× | 0.9% | PASS |
| mem_reads | `oltp_read_only` | 160.03ms | 176.24ms | 1.1× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 252.16ms | 360.99ms | 1.4× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 19.38ms | 35.82ms | 1.8× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 69.26ms | 133.10ms | 1.9× | 2.4% | PASS |
| mem_writes | `oltp_update_non_index` | 52.57ms | 87.06ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 51.18ms | 99.78ms | 1.9× | 2.1% | PASS |
| mem_writes | `oltp_write_only` | 27.98ms | 60.34ms | 2.2× | 1.6% | PASS |
| mem_writes | `types_delete_insert` | 33.88ms | 55.13ms | 1.6× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 109.05ms | 168.99ms | 1.5× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 111.75ms | 62.70ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 28.84ms | 23.82ms | 0.8× | 2.7% | PASS |
| file_reads | `oltp_sum_range` | 27.07ms | 23.54ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 4.59ms | 4.19ms | 0.9× | 2.4% | PASS |
| file_reads | `oltp_distinct_range` | 5.75ms | 5.39ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 12.71ms | 8.65ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 39.34ms | 37.62ms | 1.0× | 3.0% | PASS |
| file_reads | `select_random_ranges` | 16.23ms | 11.49ms | 0.7× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 12.34ms | 6.88ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 37.80ms | 40.41ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 12.69ms | 12.68ms | 1.0× | 2.5% | PASS |
| file_reads | `index_join_scan` | 4.92ms | 5.98ms | 1.2× | 3.0% | PASS |
| file_reads | `types_table_scan` | 1.14s | 1.21s | 1.1× | 1.6% | PASS |
| file_reads | `table_scan` | 1.39s | 1.31s | 0.9× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 272.79ms | 207.91ms | 0.8× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 267.79ms | 368.60ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 29.68ms | 41.73ms | 1.4× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 99.41ms | 143.77ms | 1.4× | 1.9% | PASS |
| file_writes | `oltp_update_non_index` | 83.34ms | 100.31ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_delete_insert` | 83.66ms | 111.52ms | 1.3× | 2.7% | PASS |
| file_writes | `oltp_write_only` | 59.09ms | 70.11ms | 1.2× | 1.9% | PASS |
| file_writes | `types_delete_insert` | 53.16ms | 61.69ms | 1.2× | 2.5% | PASS |
| file_writes | `oltp_read_write` | 142.89ms | 179.38ms | 1.3× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 60.20ms | 62.96ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 23.15ms | 23.66ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 21.02ms | 23.22ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 4.05ms | 4.17ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 5.14ms | 5.37ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 7.52ms | 8.58ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_points` | 32.36ms | 37.31ms | 1.2× | 1.9% | PASS |
| ac_reads | `select_random_ranges` | 10.66ms | 11.39ms | 1.1× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 7.21ms | 6.84ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 36.97ms | 40.05ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 9.86ms | 12.56ms | 1.3× | 1.9% | PASS |
| ac_reads | `index_join_scan` | 4.41ms | 5.99ms | 1.4× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 1.11s | 1.20s | 1.1× | 2.4% | PASS |
| ac_reads | `table_scan` | 1.37s | 1.31s | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_read_only` | 197.53ms | 207.31ms | 1.0× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 41.02ms | 102.55ms | 2.5× | 6.7% | PASS |
| ac_writes | `oltp_insert_ac` | 41.94ms | 114.71ms | 2.7× | 8.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 42.37ms | 122.76ms | 2.9× | 11.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 38.64ms | 103.42ms | 2.7× | 7.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 35.78ms | 108.92ms | 3.0× | 7.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 42.41ms | 117.13ms | 2.8× | 6.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 41.14ms | 113.22ms | 2.8× | 9.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 49.69ms | 126.04ms | 2.5× | 6.4% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 23s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 107.05ms | 130.00ms | 82.3% | 0.3% | PASS |
| `status_dirty_many_tables` | 111.21ms | 130.00ms | 85.5% | 0.4% | PASS |
| `diff_regular_working_one_table` | 102.67ms | 120.00ms | 85.6% | 0.3% | PASS |
| `diff_regular_working_many_tables` | 115.92ms | 140.00ms | 82.8% | 0.2% | PASS |
| `diff_stat_working_many_tables` | 115.86ms | 140.00ms | 82.8% | 0.2% | PASS |
| `diff_schema_working_many_tables` | 117.03ms | 140.00ms | 83.6% | 0.3% | PASS |
| `branch_list_many_branches` | 24.02ms | 35.00ms | 68.6% | 0.9% | PASS |
| `branch_create_delete` | 26.22ms | 40.00ms | 65.5% | 1.0% | PASS |
| `checkout_branch_clean` | 64.80ms | 150.00ms | 43.2% | 0.6% | PASS |
| `merge_data_no_conflicts` | 30.53ms | 50.00ms | 61.1% | 0.9% | PASS |
| `merge_schema_no_conflicts` | 22.76ms | 35.00ms | 65.0% | 1.1% | PASS |
| `merge_data_conflicts` | 35.55ms | 180.00ms | 19.7% | 1.1% | PASS |
| `merge_data_conflicts_with_resolve` | 35.50ms | 180.00ms | 19.7% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
