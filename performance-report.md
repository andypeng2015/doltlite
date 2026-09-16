# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-16 15:58 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/35107465240)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.52s | 10.67s | 1.0× | 1.5% | **PASS** |
| Writes | 2.13s | 3.49s | 1.6× | 1.5% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.29s | 10.86s | 1.0× | 1.7% | **PASS** |
| Writes | 3.53s | 4.22s | 1.2× | 2.4% | **PASS** |
| Autocommit writes | 985.14ms | 3.41s | 3.5× | 8.3% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.66s | 2.66s | 1.0× | 1.7% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 3.21s | 2.97s | 0.9× | 6.2% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.07s | 2.21s | 1.1× | 1.3% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.57s | 2.83s | 1.1× | 1.3% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 454.81ms | 756.45ms | 1.7× | 1.9% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 677.68ms | 1.10s | 1.6× | 4.2% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 406.48ms | 664.45ms | 1.6× | 1.4% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 590.25ms | 962.31ms | 1.6× | 0.9% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.76s | 2.68s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.56s | 3.06s | 0.9× | 6.6% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.15s | 2.23s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.82s | 2.89s | 1.0× | 1.5% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 610.56ms | 811.01ms | 1.3× | 2.2% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.06s | 1.22s | 1.2× | 5.6% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.09s | 1.13s | 1.0× | 5.9% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 778.84ms | 1.06s | 1.4× | 1.8% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.67s | 2.71s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 3.35s | 3.04s | 0.9× | 5.5% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.10s | 2.24s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.66s | 2.90s | 1.1× | 1.5% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 227.16ms | 824.61ms | 3.6× | 8.4% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 236.48ms | 852.36ms | 3.6× | 8.1% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 318.77ms | 990.90ms | 3.1× | 27.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 202.73ms | 737.71ms | 3.6× | 4.7% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.25ms | 30.20ms | 1.2× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 10.32ms | 11.30ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_sum_range` | 9.64ms | 11.28ms | 1.2× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 2.70ms | 2.84ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.75ms | 3.98ms | 1.1× | 0.6% | PASS |
| mem_reads | `oltp_index_scan` | 3.96ms | 5.47ms | 1.4× | 1.8% | PASS |
| mem_reads | `select_random_points` | 10.33ms | 11.41ms | 1.1× | 3.0% | PASS |
| mem_reads | `select_random_ranges` | 3.12ms | 4.06ms | 1.3× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.27ms | 4.66ms | 1.1× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 31.03ms | 33.35ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 6.02ms | 8.92ms | 1.5× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.27ms | 4.70ms | 1.4× | 2.7% | PASS |
| mem_reads | `types_table_scan` | 1.14s | 1.15s | 1.0× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.30s | 1.25s | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_read_only` | 111.27ms | 121.38ms | 1.1× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.17ms | 278.73ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 15.48ms | 28.73ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 51.65ms | 101.22ms | 2.0× | 1.9% | PASS |
| mem_writes | `oltp_update_non_index` | 36.06ms | 62.41ms | 1.7× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 47.25ms | 78.05ms | 1.7× | 2.4% | PASS |
| mem_writes | `oltp_write_only` | 23.59ms | 50.56ms | 2.1× | 2.4% | PASS |
| mem_writes | `types_delete_insert` | 24.89ms | 39.38ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_read_write` | 71.72ms | 117.37ms | 1.6× | 2.2% | PASS |
| file_reads | `oltp_point_select` | 100.08ms | 50.57ms | 0.5× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 18.46ms | 13.60ms | 0.7× | 2.3% | PASS |
| file_reads | `oltp_sum_range` | 17.34ms | 13.54ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_order_range` | 3.66ms | 3.35ms | 0.9× | 3.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.68ms | 4.46ms | 1.0× | 3.0% | PASS |
| file_reads | `oltp_index_scan` | 11.71ms | 7.72ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 17.99ms | 13.49ms | 0.7× | 2.4% | PASS |
| file_reads | `select_random_ranges` | 10.46ms | 5.97ms | 0.6× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 12.09ms | 6.64ms | 0.5× | 1.1% | PASS |
| file_reads | `groupby_scan` | 31.59ms | 33.65ms | 1.1× | 1.1% | PASS |
| file_reads | `index_join` | 10.11ms | 9.82ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join_scan` | 4.20ms | 4.96ms | 1.2× | 2.4% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 1.23s | 1.24s | 1.0× | 2.2% | PASS |
| file_reads | `oltp_read_only` | 216.92ms | 148.99ms | 0.7× | 0.6% | PASS |
| file_writes | `oltp_bulk_insert` | 198.26ms | 287.95ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_insert` | 23.42ms | 33.30ms | 1.4× | 2.2% | PASS |
| file_writes | `oltp_update_index` | 81.11ms | 112.36ms | 1.4× | 2.6% | PASS |
| file_writes | `oltp_update_non_index` | 58.91ms | 71.57ms | 1.2× | 2.3% | PASS |
| file_writes | `oltp_delete_insert` | 67.95ms | 83.50ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 45.76ms | 54.56ms | 1.2× | 2.8% | PASS |
| file_writes | `types_delete_insert` | 40.50ms | 45.39ms | 1.1× | 2.2% | PASS |
| file_writes | `oltp_read_write` | 94.65ms | 122.38ms | 1.3× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 48.22ms | 49.82ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 13.26ms | 13.47ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 12.13ms | 13.50ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 3.23ms | 3.28ms | 1.0× | 3.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.27ms | 4.44ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.74ms | 7.72ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_points` | 12.60ms | 13.37ms | 1.1× | 2.5% | PASS |
| ac_reads | `select_random_ranges` | 5.53ms | 6.00ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 7.13ms | 6.69ms | 0.9× | 1.0% | PASS |
| ac_reads | `groupby_scan` | 31.27ms | 33.58ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 7.72ms | 10.04ms | 1.3× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 3.70ms | 5.02ms | 1.4× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 1.13s | 1.15s | 1.0× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.24s | 1.24s | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_read_only` | 145.39ms | 150.34ms | 1.0× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 26.67ms | 89.50ms | 3.4× | 8.5% | PASS |
| ac_writes | `oltp_insert_ac` | 29.91ms | 101.51ms | 3.4× | 9.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.39ms | 113.22ms | 3.9× | 8.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 26.18ms | 96.48ms | 3.7× | 9.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.58ms | 108.94ms | 3.8× | 8.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.33ms | 106.42ms | 3.8× | 8.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.54ms | 97.41ms | 3.8× | 7.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.56ms | 111.13ms | 3.4× | 6.9% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 42.47ms | 41.41ms | 1.0× | 7.5% | PASS |
| mem_reads | `oltp_range_select` | 19.81ms | 15.51ms | 0.8× | 7.1% | PASS |
| mem_reads | `oltp_sum_range` | 17.91ms | 14.93ms | 0.8× | 6.7% | PASS |
| mem_reads | `oltp_order_range` | 3.55ms | 3.31ms | 0.9× | 4.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.65ms | 4.46ms | 1.0× | 2.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.25ms | 6.79ms | 1.6× | 6.6% | PASS |
| mem_reads | `select_random_points` | 29.89ms | 24.03ms | 0.8× | 6.2% | PASS |
| mem_reads | `select_random_ranges` | 3.90ms | 5.58ms | 1.4× | 5.2% | PASS |
| mem_reads | `covering_index_scan` | 4.56ms | 5.13ms | 1.1× | 7.8% | PASS |
| mem_reads | `groupby_scan` | 36.73ms | 36.02ms | 1.0× | 2.8% | PASS |
| mem_reads | `index_join` | 12.93ms | 10.59ms | 0.8× | 11.3% | PASS |
| mem_reads | `index_join_scan` | 4.55ms | 6.27ms | 1.4× | 10.6% | PASS |
| mem_reads | `types_table_scan` | 1.34s | 1.27s | 0.9× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.52s | 1.38s | 0.9× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 166.39ms | 151.50ms | 0.9× | 1.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 243.82ms | 385.49ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 18.88ms | 40.84ms | 2.2× | 2.7% | PASS |
| mem_writes | `oltp_update_index` | 80.87ms | 163.19ms | 2.0× | 5.5% | PASS |
| mem_writes | `oltp_update_non_index` | 59.08ms | 96.75ms | 1.6× | 3.9% | PASS |
| mem_writes | `oltp_delete_insert` | 64.47ms | 119.66ms | 1.9× | 5.7% | PASS |
| mem_writes | `oltp_write_only` | 34.77ms | 68.85ms | 2.0× | 6.4% | PASS |
| mem_writes | `types_delete_insert` | 45.79ms | 60.02ms | 1.3× | 4.5% | PASS |
| mem_writes | `oltp_read_write` | 130.00ms | 167.35ms | 1.3× | 4.0% | PASS |
| file_reads | `oltp_point_select` | 121.33ms | 63.48ms | 0.5× | 2.5% | PASS |
| file_reads | `oltp_range_select` | 29.81ms | 18.36ms | 0.6× | 6.4% | PASS |
| file_reads | `oltp_sum_range` | 28.09ms | 18.11ms | 0.6× | 9.1% | PASS |
| file_reads | `oltp_order_range` | 4.89ms | 3.88ms | 0.8× | 10.3% | PASS |
| file_reads | `oltp_distinct_range` | 6.00ms | 5.05ms | 0.8× | 5.5% | PASS |
| file_reads | `oltp_index_scan` | 12.69ms | 9.23ms | 0.7× | 6.6% | PASS |
| file_reads | `select_random_points` | 41.48ms | 27.29ms | 0.7× | 7.4% | PASS |
| file_reads | `select_random_ranges` | 11.80ms | 8.03ms | 0.7× | 6.6% | PASS |
| file_reads | `covering_index_scan` | 13.02ms | 7.43ms | 0.6× | 6.7% | PASS |
| file_reads | `groupby_scan` | 38.26ms | 36.74ms | 1.0× | 2.4% | PASS |
| file_reads | `index_join` | 18.41ms | 12.69ms | 0.7× | 10.6% | PASS |
| file_reads | `index_join_scan` | 5.58ms | 7.41ms | 1.3× | 15.5% | PASS |
| file_reads | `types_table_scan` | 1.37s | 1.27s | 0.9× | 1.1% | PASS |
| file_reads | `table_scan` | 1.57s | 1.39s | 0.9× | 1.0% | PASS |
| file_reads | `oltp_read_only` | 285.23ms | 188.29ms | 0.7× | 1.7% | PASS |
| file_writes | `oltp_bulk_insert` | 271.57ms | 404.56ms | 1.5× | 1.7% | PASS |
| file_writes | `oltp_insert` | 32.20ms | 49.96ms | 1.6× | 5.3% | PASS |
| file_writes | `oltp_update_index` | 152.63ms | 185.86ms | 1.2× | 10.8% | PASS |
| file_writes | `oltp_update_non_index` | 120.73ms | 111.86ms | 0.9× | 6.5% | PASS |
| file_writes | `oltp_delete_insert` | 120.05ms | 134.87ms | 1.1× | 3.3% | PASS |
| file_writes | `oltp_write_only` | 88.68ms | 81.86ms | 0.9× | 9.8% | PASS |
| file_writes | `types_delete_insert` | 86.01ms | 71.34ms | 0.8× | 3.5% | PASS |
| file_writes | `oltp_read_write` | 183.29ms | 179.38ms | 1.0× | 6.0% | PASS |
| ac_reads | `oltp_point_select` | 68.87ms | 62.63ms | 0.9× | 3.6% | PASS |
| ac_reads | `oltp_range_select` | 23.05ms | 18.16ms | 0.8× | 6.4% | PASS |
| ac_reads | `oltp_sum_range` | 23.02ms | 18.07ms | 0.8× | 9.4% | PASS |
| ac_reads | `oltp_order_range` | 4.32ms | 3.83ms | 0.9× | 8.5% | PASS |
| ac_reads | `oltp_distinct_range` | 5.31ms | 5.09ms | 1.0× | 5.4% | PASS |
| ac_reads | `oltp_index_scan` | 7.41ms | 9.25ms | 1.2× | 5.5% | PASS |
| ac_reads | `select_random_points` | 32.73ms | 27.70ms | 0.8× | 8.1% | PASS |
| ac_reads | `select_random_ranges` | 6.59ms | 7.64ms | 1.2× | 4.5% | PASS |
| ac_reads | `covering_index_scan` | 7.68ms | 7.32ms | 1.0× | 7.1% | PASS |
| ac_reads | `groupby_scan` | 36.97ms | 36.43ms | 1.0× | 1.8% | PASS |
| ac_reads | `index_join` | 15.63ms | 12.69ms | 0.8× | 11.2% | PASS |
| ac_reads | `index_join_scan` | 5.04ms | 7.16ms | 1.4× | 12.4% | PASS |
| ac_reads | `types_table_scan` | 1.36s | 1.26s | 0.9× | 1.0% | PASS |
| ac_reads | `table_scan` | 1.55s | 1.38s | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 205.49ms | 183.91ms | 0.9× | 2.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 26.80ms | 90.66ms | 3.4× | 10.0% | PASS |
| ac_writes | `oltp_insert_ac` | 30.12ms | 105.00ms | 3.5× | 7.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 31.32ms | 118.61ms | 3.8× | 6.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 26.25ms | 99.75ms | 3.8× | 7.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.99ms | 108.58ms | 3.7× | 7.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 29.62ms | 110.82ms | 3.7× | 9.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 27.23ms | 101.78ms | 3.7× | 9.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 36.15ms | 117.15ms | 3.2× | 8.4% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 26.08ms | 25.22ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 10.93ms | 9.91ms | 0.9× | 3.4% | PASS |
| mem_reads | `oltp_sum_range` | 11.08ms | 9.61ms | 0.9× | 2.9% | PASS |
| mem_reads | `oltp_order_range` | 2.44ms | 2.40ms | 1.0× | 1.6% | PASS |
| mem_reads | `oltp_distinct_range` | 3.22ms | 3.27ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.01ms | 3.99ms | 1.3× | 1.8% | PASS |
| mem_reads | `select_random_points` | 16.74ms | 15.61ms | 0.9× | 1.9% | PASS |
| mem_reads | `select_random_ranges` | 2.49ms | 3.71ms | 1.5× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 2.94ms | 3.06ms | 1.0× | 1.0% | PASS |
| mem_reads | `groupby_scan` | 26.83ms | 27.02ms | 1.0× | 1.2% | PASS |
| mem_reads | `index_join` | 8.82ms | 6.91ms | 0.8× | 1.1% | PASS |
| mem_reads | `index_join_scan` | 2.70ms | 4.50ms | 1.7× | 1.1% | PASS |
| mem_reads | `types_table_scan` | 867.64ms | 952.34ms | 1.1× | 0.2% | PASS |
| mem_reads | `table_scan` | 986.14ms | 1.05s | 1.1× | 0.3% | PASS |
| mem_reads | `oltp_read_only` | 98.76ms | 98.44ms | 1.0× | 0.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 165.40ms | 241.80ms | 1.5× | 0.4% | PASS |
| mem_writes | `oltp_insert` | 13.15ms | 26.02ms | 2.0× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 45.74ms | 95.83ms | 2.1× | 1.9% | PASS |
| mem_writes | `oltp_update_non_index` | 32.80ms | 56.74ms | 1.7× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 37.53ms | 69.59ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 20.30ms | 42.75ms | 2.1× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 27.21ms | 35.75ms | 1.3× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 64.36ms | 95.97ms | 1.5× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 51.99ms | 31.55ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 13.61ms | 10.73ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 13.36ms | 10.26ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_order_range` | 2.76ms | 2.49ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 3.55ms | 3.36ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 5.72ms | 4.90ms | 0.9× | 1.6% | PASS |
| file_reads | `select_random_points` | 19.90ms | 16.67ms | 0.8× | 1.2% | PASS |
| file_reads | `select_random_ranges` | 5.24ms | 4.57ms | 0.9× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 5.68ms | 3.83ms | 0.7× | 2.2% | PASS |
| file_reads | `groupby_scan` | 26.99ms | 27.03ms | 1.0× | 0.6% | PASS |
| file_reads | `index_join` | 10.33ms | 7.37ms | 0.7× | 1.8% | PASS |
| file_reads | `index_join_scan` | 2.98ms | 4.64ms | 1.6× | 2.6% | PASS |
| file_reads | `types_table_scan` | 867.85ms | 950.56ms | 1.1× | 0.3% | PASS |
| file_reads | `table_scan` | 985.70ms | 1.05s | 1.1× | 0.3% | PASS |
| file_reads | `oltp_read_only` | 135.49ms | 107.25ms | 0.8× | 0.5% | PASS |
| file_writes | `oltp_bulk_insert` | 233.60ms | 308.50ms | 1.3× | 2.6% | PASS |
| file_writes | `oltp_insert` | 26.53ms | 51.78ms | 2.0× | 7.8% | PASS |
| file_writes | `oltp_update_index` | 152.71ms | 179.67ms | 1.2× | 9.5% | PASS |
| file_writes | `oltp_update_non_index` | 127.04ms | 117.14ms | 0.9× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 160.57ms | 139.61ms | 0.9× | 6.5% | PASS |
| file_writes | `oltp_write_only` | 113.28ms | 97.79ms | 0.9× | 12.1% | PASS |
| file_writes | `types_delete_insert` | 116.08ms | 78.19ms | 0.7× | 5.3% | PASS |
| file_writes | `oltp_read_write` | 158.18ms | 154.71ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_point_select` | 35.23ms | 32.30ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 12.64ms | 11.07ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_sum_range` | 12.35ms | 10.60ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_order_range` | 2.65ms | 2.56ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_distinct_range` | 3.46ms | 3.45ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 4.10ms | 5.02ms | 1.2× | 2.6% | PASS |
| ac_reads | `select_random_points` | 19.01ms | 17.14ms | 0.9× | 1.5% | PASS |
| ac_reads | `select_random_ranges` | 3.49ms | 4.56ms | 1.3× | 1.9% | PASS |
| ac_reads | `covering_index_scan` | 4.00ms | 4.16ms | 1.0× | 2.7% | PASS |
| ac_reads | `groupby_scan` | 27.33ms | 27.37ms | 1.0× | 0.6% | PASS |
| ac_reads | `index_join` | 9.90ms | 7.52ms | 0.8× | 2.4% | PASS |
| ac_reads | `index_join_scan` | 2.96ms | 4.71ms | 1.6× | 1.3% | PASS |
| ac_reads | `types_table_scan` | 867.60ms | 951.13ms | 1.1× | 0.2% | PASS |
| ac_reads | `table_scan` | 986.56ms | 1.05s | 1.1× | 0.3% | PASS |
| ac_reads | `oltp_read_only` | 110.23ms | 107.35ms | 1.0× | 0.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 36.68ms | 104.15ms | 2.8× | 29.7% | PASS |
| ac_writes | `oltp_insert_ac` | 39.04ms | 133.76ms | 3.4× | 39.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 40.52ms | 117.73ms | 2.9× | 24.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 36.35ms | 118.28ms | 3.3× | 43.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 57.24ms | 195.65ms | 3.4× | 57.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 35.16ms | 108.48ms | 3.1× | 19.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 34.45ms | 105.98ms | 3.1× | 20.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 39.33ms | 106.88ms | 2.7× | 14.7% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.53ms | 41.18ms | 1.3× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 19.01ms | 21.40ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 17.41ms | 20.54ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 3.48ms | 3.81ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.61ms | 5.01ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 4.41ms | 5.94ms | 1.3× | 1.7% | PASS |
| mem_reads | `select_random_points` | 27.18ms | 32.46ms | 1.2× | 1.3% | PASS |
| mem_reads | `select_random_ranges` | 7.78ms | 9.04ms | 1.2× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.24ms | 4.39ms | 1.0× | 3.5% | PASS |
| mem_reads | `groupby_scan` | 36.22ms | 39.52ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 8.00ms | 10.33ms | 1.3× | 0.9% | PASS |
| mem_reads | `index_join_scan` | 3.85ms | 5.42ms | 1.4× | 2.6% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 149.84ms | 172.52ms | 1.2× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 250.99ms | 352.08ms | 1.4× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 19.10ms | 35.00ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 64.46ms | 125.00ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_non_index` | 49.72ms | 83.85ms | 1.7× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 48.68ms | 94.18ms | 1.9× | 0.7% | PASS |
| mem_writes | `oltp_write_only` | 26.23ms | 57.50ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 32.26ms | 52.82ms | 1.6× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 98.82ms | 161.88ms | 1.6× | 0.9% | PASS |
| file_reads | `oltp_point_select` | 108.23ms | 61.35ms | 0.6× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 28.04ms | 23.90ms | 0.9× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 25.88ms | 23.17ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 4.49ms | 4.15ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_distinct_range` | 5.63ms | 5.36ms | 1.0× | 1.8% | PASS |
| file_reads | `oltp_index_scan` | 12.10ms | 8.45ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 37.45ms | 36.47ms | 1.0× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 15.78ms | 11.55ms | 0.7× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 12.07ms | 6.64ms | 0.5× | 2.0% | PASS |
| file_reads | `groupby_scan` | 37.30ms | 39.92ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 12.47ms | 12.37ms | 1.0× | 1.8% | PASS |
| file_reads | `index_join_scan` | 4.93ms | 5.87ms | 1.2× | 1.6% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.20s | 1.27s | 1.1× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 262.05ms | 204.78ms | 0.8× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 267.31ms | 365.84ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.47ms | 41.07ms | 1.6× | 2.5% | PASS |
| file_writes | `oltp_update_index` | 98.02ms | 142.11ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 78.50ms | 99.94ms | 1.3× | 2.0% | PASS |
| file_writes | `oltp_delete_insert` | 76.96ms | 107.89ms | 1.4× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 51.86ms | 68.48ms | 1.3× | 2.2% | PASS |
| file_writes | `types_delete_insert` | 49.68ms | 59.74ms | 1.2× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 130.04ms | 174.63ms | 1.3× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 59.41ms | 61.90ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 23.28ms | 23.93ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 21.41ms | 23.25ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 4.03ms | 4.21ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_distinct_range` | 5.11ms | 5.39ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 7.22ms | 8.47ms | 1.2× | 2.1% | PASS |
| ac_reads | `select_random_points` | 31.35ms | 36.33ms | 1.2× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 10.50ms | 11.48ms | 1.1× | 1.7% | PASS |
| ac_reads | `covering_index_scan` | 6.94ms | 6.76ms | 1.0× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 36.77ms | 39.91ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 9.60ms | 12.46ms | 1.3× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 4.48ms | 5.91ms | 1.3× | 1.8% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 186.38ms | 203.88ms | 1.1× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.29ms | 75.70ms | 3.4× | 4.8% | PASS |
| ac_writes | `oltp_insert_ac` | 24.80ms | 92.98ms | 3.7× | 4.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.69ms | 103.84ms | 3.9× | 4.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.48ms | 87.58ms | 3.7× | 4.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.52ms | 93.67ms | 3.8× | 3.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.09ms | 94.45ms | 3.6× | 5.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.37ms | 87.74ms | 3.9× | 5.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.49ms | 101.75ms | 3.1× | 5.0% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 21s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 106.39ms | 130.00ms | 81.8% | 0.3% | PASS |
| `status_dirty_many_tables` | 110.13ms | 130.00ms | 84.7% | 0.2% | PASS |
| `diff_regular_working_one_table` | 101.62ms | 120.00ms | 84.7% | 0.2% | PASS |
| `diff_regular_working_many_tables` | 115.22ms | 140.00ms | 82.3% | 0.2% | PASS |
| `diff_stat_working_many_tables` | 115.18ms | 140.00ms | 82.3% | 0.2% | PASS |
| `diff_schema_working_many_tables` | 116.27ms | 140.00ms | 83.0% | 0.2% | PASS |
| `branch_list_many_branches` | 24.34ms | 35.00ms | 69.5% | 1.0% | PASS |
| `branch_create_delete` | 26.41ms | 40.00ms | 66.0% | 0.7% | PASS |
| `checkout_branch_clean` | 65.18ms | 150.00ms | 43.5% | 0.4% | PASS |
| `merge_data_no_conflicts` | 30.73ms | 50.00ms | 61.5% | 0.5% | PASS |
| `merge_schema_no_conflicts` | 22.76ms | 35.00ms | 65.0% | 0.9% | PASS |
| `merge_data_conflicts` | 34.80ms | 180.00ms | 19.3% | 0.7% | PASS |
| `merge_data_conflicts_with_resolve` | 34.89ms | 180.00ms | 19.4% | 0.7% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
