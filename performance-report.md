# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-16 15:43 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261012.334.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/38913220620)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.23s | 11.23s | 1.0× | 2.0% | **PASS** |
| Writes | 2.28s | 3.78s | 1.7× | 1.8% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.98s | 11.42s | 1.0× | 1.7% | **PASS** |
| Writes | 3.23s | 4.14s | 1.3× | 2.1% | **PASS** |
| Autocommit writes | 848.10ms | 2.56s | 3.0× | 6.1% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.63s | 2.65s | 1.0× | 2.9% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.91s | 2.82s | 1.0× | 2.0% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.77s | 2.82s | 1.0× | 1.3% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.92s | 2.93s | 1.0× | 2.0% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 461.71ms | 767.44ms | 1.7× | 2.3% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 614.40ms | 1.03s | 1.7× | 2.1% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 592.14ms | 991.67ms | 1.7× | 1.7% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 607.28ms | 990.16ms | 1.6× | 1.6% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.85s | 2.71s | 0.9× | 1.7% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.17s | 2.90s | 0.9× | 1.8% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.96s | 2.86s | 1.0× | 1.6% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.00s | 2.95s | 1.0× | 1.7% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 618.66ms | 826.48ms | 1.3× | 2.1% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 970.45ms | 1.13s | 1.2× | 4.1% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 847.91ms | 1.11s | 1.3× | 2.1% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 795.25ms | 1.08s | 1.4× | 1.7% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.57s | 2.68s | 1.0× | 2.0% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.96s | 2.89s | 1.0× | 2.1% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.92s | 2.92s | 1.0× | 1.7% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.98s | 2.99s | 1.0× | 1.9% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 198.37ms | 624.98ms | 3.2× | 6.4% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 219.04ms | 645.34ms | 2.9× | 5.8% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 228.86ms | 661.04ms | 2.9× | 9.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 201.83ms | 631.26ms | 3.1× | 5.4% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.86ms | 30.80ms | 1.2× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 11.61ms | 12.08ms | 1.0× | 4.5% | PASS |
| mem_reads | `oltp_sum_range` | 10.19ms | 11.54ms | 1.1× | 3.1% | PASS |
| mem_reads | `oltp_order_range` | 2.80ms | 3.09ms | 1.1× | 3.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.81ms | 4.14ms | 1.1× | 2.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.01ms | 5.50ms | 1.4× | 2.5% | PASS |
| mem_reads | `select_random_points` | 10.64ms | 11.70ms | 1.1× | 4.0% | PASS |
| mem_reads | `select_random_ranges` | 3.13ms | 4.06ms | 1.3× | 2.2% | PASS |
| mem_reads | `covering_index_scan` | 4.39ms | 4.53ms | 1.0× | 3.6% | PASS |
| mem_reads | `groupby_scan` | 31.24ms | 33.44ms | 1.1× | 1.1% | PASS |
| mem_reads | `index_join` | 5.89ms | 8.33ms | 1.4× | 3.1% | PASS |
| mem_reads | `index_join_scan` | 3.28ms | 4.59ms | 1.4× | 4.1% | PASS |
| mem_reads | `types_table_scan` | 1.11s | 1.14s | 1.0× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.29s | 1.25s | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 113.01ms | 123.13ms | 1.1× | 1.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.14ms | 279.40ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 15.63ms | 29.21ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 52.56ms | 102.42ms | 1.9× | 2.7% | PASS |
| mem_writes | `oltp_update_non_index` | 36.93ms | 63.75ms | 1.7× | 2.4% | PASS |
| mem_writes | `oltp_delete_insert` | 47.46ms | 79.04ms | 1.7× | 2.2% | PASS |
| mem_writes | `oltp_write_only` | 23.41ms | 50.78ms | 2.2× | 2.5% | PASS |
| mem_writes | `types_delete_insert` | 25.56ms | 39.98ms | 1.6× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 76.01ms | 122.86ms | 1.6× | 2.7% | PASS |
| file_reads | `oltp_point_select` | 100.78ms | 50.95ms | 0.5× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 20.42ms | 14.76ms | 0.7× | 3.0% | PASS |
| file_reads | `oltp_sum_range` | 18.83ms | 14.14ms | 0.8× | 2.5% | PASS |
| file_reads | `oltp_order_range` | 4.20ms | 3.79ms | 0.9× | 3.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.21ms | 4.97ms | 1.0× | 3.9% | PASS |
| file_reads | `oltp_index_scan` | 11.95ms | 7.83ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 18.72ms | 14.04ms | 0.8× | 2.8% | PASS |
| file_reads | `select_random_ranges` | 10.76ms | 6.13ms | 0.6× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 12.57ms | 6.79ms | 0.5× | 1.7% | PASS |
| file_reads | `groupby_scan` | 32.93ms | 34.34ms | 1.0× | 1.2% | PASS |
| file_reads | `index_join` | 10.40ms | 10.16ms | 1.0× | 2.2% | PASS |
| file_reads | `index_join_scan` | 4.51ms | 5.12ms | 1.1× | 2.6% | PASS |
| file_reads | `types_table_scan` | 1.10s | 1.14s | 1.0× | 1.1% | PASS |
| file_reads | `table_scan` | 1.28s | 1.24s | 1.0× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 223.85ms | 151.55ms | 0.7× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 197.13ms | 288.19ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 22.44ms | 33.13ms | 1.5× | 1.3% | PASS |
| file_writes | `oltp_update_index` | 80.15ms | 111.95ms | 1.4× | 2.1% | PASS |
| file_writes | `oltp_update_non_index` | 61.66ms | 74.62ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_delete_insert` | 70.64ms | 86.74ms | 1.2× | 3.1% | PASS |
| file_writes | `oltp_write_only` | 46.80ms | 57.63ms | 1.2× | 2.7% | PASS |
| file_writes | `types_delete_insert` | 41.26ms | 45.81ms | 1.1× | 2.3% | PASS |
| file_writes | `oltp_read_write` | 98.57ms | 128.41ms | 1.3× | 2.1% | PASS |
| ac_reads | `oltp_point_select` | 50.08ms | 50.45ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 14.70ms | 14.42ms | 1.0× | 3.4% | PASS |
| ac_reads | `oltp_sum_range` | 13.43ms | 14.08ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_order_range` | 3.67ms | 3.77ms | 1.0× | 4.1% | PASS |
| ac_reads | `oltp_distinct_range` | 4.74ms | 4.89ms | 1.0× | 4.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.94ms | 7.82ms | 1.1× | 1.3% | PASS |
| ac_reads | `select_random_points` | 13.64ms | 13.99ms | 1.0× | 3.5% | PASS |
| ac_reads | `select_random_ranges` | 5.82ms | 6.14ms | 1.1× | 1.8% | PASS |
| ac_reads | `covering_index_scan` | 7.67ms | 6.77ms | 0.9× | 2.0% | PASS |
| ac_reads | `groupby_scan` | 32.10ms | 34.19ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 7.85ms | 10.11ms | 1.3× | 2.1% | PASS |
| ac_reads | `index_join_scan` | 3.94ms | 5.18ms | 1.3× | 2.8% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.23s | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 145.04ms | 149.82ms | 1.0× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.83ms | 62.59ms | 2.7× | 6.2% | PASS |
| ac_writes | `oltp_insert_ac` | 24.35ms | 78.50ms | 3.2× | 5.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.52ms | 89.80ms | 3.4× | 6.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.01ms | 69.87ms | 3.2× | 5.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.77ms | 83.06ms | 3.4× | 5.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.70ms | 80.97ms | 3.3× | 7.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.64ms | 73.44ms | 3.4× | 8.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.55ms | 86.77ms | 2.8× | 7.6% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.31ms | 38.84ms | 1.0× | 2.6% | PASS |
| mem_reads | `oltp_range_select` | 17.96ms | 14.26ms | 0.8× | 2.6% | PASS |
| mem_reads | `oltp_sum_range` | 16.17ms | 14.06ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 3.37ms | 3.19ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.45ms | 4.33ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 4.08ms | 6.39ms | 1.6× | 1.6% | PASS |
| mem_reads | `select_random_points` | 23.57ms | 21.68ms | 0.9× | 3.1% | PASS |
| mem_reads | `select_random_ranges` | 3.63ms | 5.33ms | 1.5× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 4.20ms | 4.79ms | 1.1× | 1.8% | PASS |
| mem_reads | `groupby_scan` | 35.36ms | 34.86ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 10.95ms | 9.80ms | 0.9× | 2.6% | PASS |
| mem_reads | `index_join_scan` | 3.87ms | 5.64ms | 1.5× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 1.20s | 1.21s | 1.0× | 3.1% | PASS |
| mem_reads | `table_scan` | 1.39s | 1.31s | 0.9× | 2.0% | PASS |
| mem_reads | `oltp_read_only` | 148.12ms | 140.85ms | 1.0× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 240.27ms | 372.27ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 18.12ms | 39.15ms | 2.2× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 67.41ms | 146.08ms | 2.2× | 1.9% | PASS |
| mem_writes | `oltp_update_non_index` | 51.03ms | 88.88ms | 1.7× | 2.1% | PASS |
| mem_writes | `oltp_delete_insert` | 56.70ms | 107.86ms | 1.9× | 2.2% | PASS |
| mem_writes | `oltp_write_only` | 29.38ms | 64.35ms | 2.2× | 2.1% | PASS |
| mem_writes | `types_delete_insert` | 41.36ms | 55.94ms | 1.4× | 2.2% | PASS |
| mem_writes | `oltp_read_write` | 110.12ms | 152.44ms | 1.4× | 2.8% | PASS |
| file_reads | `oltp_point_select` | 114.82ms | 58.69ms | 0.5× | 1.5% | PASS |
| file_reads | `oltp_range_select` | 26.10ms | 16.67ms | 0.6× | 2.4% | PASS |
| file_reads | `oltp_sum_range` | 25.04ms | 16.52ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.36ms | 3.51ms | 0.8× | 2.8% | PASS |
| file_reads | `oltp_distinct_range` | 5.50ms | 4.68ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 12.03ms | 8.68ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_points` | 33.80ms | 25.07ms | 0.7× | 2.8% | PASS |
| file_reads | `select_random_ranges` | 11.35ms | 7.39ms | 0.7× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 12.06ms | 6.90ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 36.36ms | 35.38ms | 1.0× | 1.1% | PASS |
| file_reads | `index_join` | 15.96ms | 11.41ms | 0.7× | 2.7% | PASS |
| file_reads | `index_join_scan` | 5.03ms | 6.20ms | 1.2× | 3.2% | PASS |
| file_reads | `types_table_scan` | 1.23s | 1.22s | 1.0× | 2.2% | PASS |
| file_reads | `table_scan` | 1.38s | 1.31s | 1.0× | 2.0% | PASS |
| file_reads | `oltp_read_only` | 261.02ms | 170.22ms | 0.7× | 1.5% | PASS |
| file_writes | `oltp_bulk_insert` | 265.49ms | 384.99ms | 1.5× | 0.8% | PASS |
| file_writes | `oltp_insert` | 26.27ms | 46.29ms | 1.8× | 2.0% | PASS |
| file_writes | `oltp_update_index` | 141.38ms | 169.09ms | 1.2× | 5.7% | PASS |
| file_writes | `oltp_update_non_index` | 115.46ms | 104.95ms | 0.9× | 14.2% | PASS |
| file_writes | `oltp_delete_insert` | 101.40ms | 124.25ms | 1.2× | 2.4% | PASS |
| file_writes | `oltp_write_only` | 90.35ms | 75.91ms | 0.8× | 11.7% | PASS |
| file_writes | `types_delete_insert` | 74.85ms | 64.92ms | 0.9× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 155.25ms | 161.94ms | 1.0× | 7.6% | PASS |
| ac_reads | `oltp_point_select` | 61.62ms | 58.20ms | 0.9× | 2.2% | PASS |
| ac_reads | `oltp_range_select` | 20.29ms | 16.50ms | 0.8× | 2.3% | PASS |
| ac_reads | `oltp_sum_range` | 18.98ms | 16.25ms | 0.9× | 2.2% | PASS |
| ac_reads | `oltp_order_range` | 3.88ms | 3.54ms | 0.9× | 2.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.90ms | 4.68ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.90ms | 8.53ms | 1.2× | 1.5% | PASS |
| ac_reads | `select_random_points` | 26.30ms | 24.60ms | 0.9× | 3.8% | PASS |
| ac_reads | `select_random_ranges` | 6.30ms | 7.38ms | 1.2× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 7.17ms | 6.96ms | 1.0× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 35.71ms | 35.37ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 12.97ms | 11.39ms | 0.9× | 2.6% | PASS |
| ac_reads | `index_join_scan` | 4.47ms | 6.22ms | 1.4× | 2.2% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.20s | 1.0× | 2.1% | PASS |
| ac_reads | `table_scan` | 1.39s | 1.32s | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 184.12ms | 169.87ms | 0.9× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.86ms | 65.22ms | 2.6× | 5.8% | PASS |
| ac_writes | `oltp_insert_ac` | 27.61ms | 77.18ms | 2.8× | 3.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.92ms | 92.65ms | 3.2× | 5.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.75ms | 74.78ms | 3.1× | 6.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.45ms | 84.72ms | 3.1× | 6.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.79ms | 83.44ms | 3.1× | 5.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.30ms | 76.95ms | 3.0× | 5.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.37ms | 90.39ms | 2.6× | 6.4% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.82ms | 38.43ms | 1.0× | 2.5% | PASS |
| mem_reads | `oltp_range_select` | 16.70ms | 14.09ms | 0.8× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 15.86ms | 13.76ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 3.55ms | 3.14ms | 0.9× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 4.62ms | 4.36ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 4.17ms | 6.51ms | 1.6× | 1.1% | PASS |
| mem_reads | `select_random_points` | 24.25ms | 21.61ms | 0.9× | 3.6% | PASS |
| mem_reads | `select_random_ranges` | 3.64ms | 5.38ms | 1.5× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.30ms | 4.90ms | 1.1× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 35.43ms | 35.10ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 11.62ms | 10.22ms | 0.9× | 2.7% | PASS |
| mem_reads | `index_join_scan` | 4.07ms | 5.81ms | 1.4× | 3.8% | PASS |
| mem_reads | `types_table_scan` | 1.17s | 1.22s | 1.0× | 1.2% | PASS |
| mem_reads | `table_scan` | 1.30s | 1.30s | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_read_only` | 137.47ms | 135.83ms | 1.0× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 246.20ms | 370.95ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 19.00ms | 39.28ms | 2.1× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 65.22ms | 138.25ms | 2.1× | 2.1% | PASS |
| mem_writes | `oltp_update_non_index` | 47.20ms | 83.11ms | 1.8× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 50.99ms | 100.50ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 27.36ms | 60.49ms | 2.2× | 1.7% | PASS |
| mem_writes | `types_delete_insert` | 39.11ms | 53.58ms | 1.4× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 97.06ms | 145.50ms | 1.5× | 3.0% | PASS |
| file_reads | `oltp_point_select` | 115.86ms | 60.39ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 25.09ms | 16.59ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 24.38ms | 16.09ms | 0.7× | 1.7% | PASS |
| file_reads | `oltp_order_range` | 4.62ms | 3.53ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.63ms | 4.71ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 11.80ms | 8.63ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 32.66ms | 24.71ms | 0.8× | 3.3% | PASS |
| file_reads | `select_random_ranges` | 11.30ms | 7.44ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 12.45ms | 6.97ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 36.68ms | 35.42ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 15.89ms | 11.22ms | 0.7× | 2.7% | PASS |
| file_reads | `index_join_scan` | 5.02ms | 5.87ms | 1.2× | 2.9% | PASS |
| file_reads | `types_table_scan` | 1.14s | 1.20s | 1.1× | 1.6% | PASS |
| file_reads | `table_scan` | 1.26s | 1.29s | 1.0× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 263.55ms | 170.25ms | 0.6× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 268.76ms | 384.24ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 27.07ms | 46.74ms | 1.7× | 2.4% | PASS |
| file_writes | `oltp_update_index` | 102.48ms | 158.29ms | 1.5× | 2.0% | PASS |
| file_writes | `oltp_update_non_index` | 88.78ms | 100.36ms | 1.1× | 5.6% | PASS |
| file_writes | `oltp_delete_insert` | 93.47ms | 120.23ms | 1.3× | 2.3% | PASS |
| file_writes | `oltp_write_only` | 59.81ms | 72.18ms | 1.2× | 1.8% | PASS |
| file_writes | `types_delete_insert` | 67.97ms | 63.51ms | 0.9× | 2.4% | PASS |
| file_writes | `oltp_read_write` | 139.57ms | 160.67ms | 1.2× | 1.7% | PASS |
| ac_reads | `oltp_point_select` | 63.75ms | 59.86ms | 0.9× | 2.3% | PASS |
| ac_reads | `oltp_range_select` | 20.63ms | 17.07ms | 0.8× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 19.29ms | 16.36ms | 0.8× | 2.1% | PASS |
| ac_reads | `oltp_order_range` | 4.16ms | 3.53ms | 0.8× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 5.26ms | 4.75ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 6.98ms | 8.70ms | 1.2× | 1.5% | PASS |
| ac_reads | `select_random_points` | 27.60ms | 24.67ms | 0.9× | 2.1% | PASS |
| ac_reads | `select_random_ranges` | 6.33ms | 7.46ms | 1.2× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 7.37ms | 6.92ms | 0.9× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 35.68ms | 35.16ms | 1.0× | 0.5% | PASS |
| ac_reads | `index_join` | 12.99ms | 11.22ms | 0.9× | 4.5% | PASS |
| ac_reads | `index_join_scan` | 4.63ms | 6.08ms | 1.3× | 3.8% | PASS |
| ac_reads | `types_table_scan` | 1.15s | 1.20s | 1.0× | 1.8% | PASS |
| ac_reads | `table_scan` | 1.35s | 1.34s | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 201.82ms | 177.28ms | 0.9× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 26.53ms | 65.95ms | 2.5× | 10.3% | PASS |
| ac_writes | `oltp_insert_ac` | 28.09ms | 81.94ms | 2.9× | 11.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.67ms | 90.61ms | 3.2× | 8.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 26.04ms | 77.67ms | 3.0× | 9.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.83ms | 86.77ms | 3.0× | 10.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.33ms | 85.76ms | 3.1× | 6.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 27.27ms | 79.23ms | 2.9× | 10.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 36.10ms | 93.12ms | 2.6× | 7.7% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.39ms | 42.75ms | 1.2× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 20.91ms | 21.58ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_sum_range` | 18.23ms | 20.71ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 3.55ms | 3.88ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_distinct_range` | 4.68ms | 5.02ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 4.53ms | 6.11ms | 1.4× | 2.8% | PASS |
| mem_reads | `select_random_points` | 28.32ms | 32.94ms | 1.2× | 2.0% | PASS |
| mem_reads | `select_random_ranges` | 7.80ms | 9.03ms | 1.2× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.25ms | 4.54ms | 1.1× | 3.0% | PASS |
| mem_reads | `groupby_scan` | 37.01ms | 39.94ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 8.02ms | 10.71ms | 1.3× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 3.98ms | 5.76ms | 1.4× | 3.0% | PASS |
| mem_reads | `types_table_scan` | 1.17s | 1.22s | 1.0× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.42s | 1.33s | 0.9× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 160.69ms | 177.11ms | 1.1× | 1.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 251.40ms | 358.08ms | 1.4× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 19.61ms | 35.93ms | 1.8× | 1.4% | PASS |
| mem_writes | `oltp_update_index` | 69.81ms | 134.01ms | 1.9× | 1.7% | PASS |
| mem_writes | `oltp_update_non_index` | 53.88ms | 87.64ms | 1.6× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 50.08ms | 98.15ms | 2.0× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 26.86ms | 58.35ms | 2.2× | 2.0% | PASS |
| mem_writes | `types_delete_insert` | 32.76ms | 53.39ms | 1.6× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 102.89ms | 164.61ms | 1.6× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 108.96ms | 61.71ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 28.35ms | 23.57ms | 0.8× | 2.5% | PASS |
| file_reads | `oltp_sum_range` | 26.54ms | 23.30ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.44ms | 4.20ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.62ms | 5.36ms | 1.0× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 12.27ms | 8.63ms | 0.7× | 2.1% | PASS |
| file_reads | `select_random_points` | 38.62ms | 36.76ms | 1.0× | 2.3% | PASS |
| file_reads | `select_random_ranges` | 15.66ms | 11.52ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.81ms | 6.81ms | 0.6× | 1.9% | PASS |
| file_reads | `groupby_scan` | 37.48ms | 40.08ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 12.76ms | 12.93ms | 1.0× | 1.7% | PASS |
| file_reads | `index_join_scan` | 5.17ms | 6.20ms | 1.2× | 2.9% | PASS |
| file_reads | `types_table_scan` | 1.11s | 1.20s | 1.1× | 1.7% | PASS |
| file_reads | `table_scan` | 1.30s | 1.30s | 1.0× | 2.4% | PASS |
| file_reads | `oltp_read_only` | 270.37ms | 206.61ms | 0.8× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 266.27ms | 369.23ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.14ms | 41.02ms | 1.6× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 99.33ms | 143.26ms | 1.4× | 2.1% | PASS |
| file_writes | `oltp_update_non_index` | 79.94ms | 100.43ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_delete_insert` | 78.29ms | 109.49ms | 1.4× | 2.5% | PASS |
| file_writes | `oltp_write_only` | 51.82ms | 69.53ms | 1.3× | 2.2% | PASS |
| file_writes | `types_delete_insert` | 53.05ms | 62.34ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 140.40ms | 183.71ms | 1.3× | 1.8% | PASS |
| ac_reads | `oltp_point_select` | 59.63ms | 62.41ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 23.88ms | 23.74ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 21.57ms | 23.47ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 4.09ms | 4.22ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 5.23ms | 5.43ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_index_scan` | 7.59ms | 8.79ms | 1.2× | 2.9% | PASS |
| ac_reads | `select_random_points` | 33.61ms | 37.87ms | 1.1× | 3.0% | PASS |
| ac_reads | `select_random_ranges` | 10.84ms | 11.74ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 7.36ms | 6.96ms | 0.9× | 2.5% | PASS |
| ac_reads | `groupby_scan` | 37.37ms | 40.36ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 10.07ms | 12.79ms | 1.3× | 2.9% | PASS |
| ac_reads | `index_join_scan` | 4.67ms | 6.40ms | 1.4× | 3.6% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.22s | 1.0× | 1.0% | PASS |
| ac_reads | `table_scan` | 1.39s | 1.32s | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 196.56ms | 206.21ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.76ms | 61.85ms | 2.8× | 5.2% | PASS |
| ac_writes | `oltp_insert_ac` | 24.62ms | 79.15ms | 3.2× | 4.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.60ms | 90.20ms | 3.3× | 4.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.46ms | 72.86ms | 3.1× | 5.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.82ms | 82.53ms | 3.3× | 6.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.22ms | 83.08ms | 3.3× | 5.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.91ms | 71.89ms | 3.3× | 6.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.44ms | 89.71ms | 2.8× | 4.7% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 12s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 96.01ms | 130.00ms | 73.9% | 0.5% | PASS |
| `status_dirty_many_tables` | 98.85ms | 130.00ms | 76.0% | 0.5% | PASS |
| `diff_regular_working_one_table` | 91.49ms | 120.00ms | 76.2% | 0.5% | PASS |
| `diff_regular_working_many_tables` | 104.13ms | 140.00ms | 74.4% | 0.5% | PASS |
| `diff_stat_working_many_tables` | 103.43ms | 140.00ms | 73.9% | 0.5% | PASS |
| `diff_schema_working_many_tables` | 104.89ms | 140.00ms | 74.9% | 0.5% | PASS |
| `branch_list_many_branches` | 22.97ms | 35.00ms | 65.6% | 1.3% | PASS |
| `branch_create_delete` | 24.97ms | 40.00ms | 62.4% | 1.2% | PASS |
| `checkout_branch_clean` | 60.63ms | 150.00ms | 40.4% | 0.7% | PASS |
| `merge_data_no_conflicts` | 28.58ms | 50.00ms | 57.2% | 1.1% | PASS |
| `merge_schema_no_conflicts` | 21.63ms | 35.00ms | 61.8% | 1.2% | PASS |
| `merge_data_conflicts` | 32.51ms | 180.00ms | 18.1% | 0.9% | PASS |
| `merge_data_conflicts_with_resolve` | 32.45ms | 180.00ms | 18.0% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
