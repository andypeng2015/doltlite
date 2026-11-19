# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-19 20:06 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261116.202.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/44588471930)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.82s | 9.41s | 1.1× | 1.4% | **PASS** |
| Writes | 1.77s | 3.05s | 1.7× | 1.2% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.54s | 9.60s | 1.0× | 1.6% | **PASS** |
| Writes | 2.94s | 3.68s | 1.3× | 1.9% | **FAIL** |
| Autocommit writes | 673.94ms | 1.98s | 2.9× | 6.1% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.44s | 2.68s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.63s | 2.81s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 1.82s | 1.87s | 1.0× | 2.5% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 1.93s | 2.05s | 1.1× | 0.9% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 425.21ms | 751.58ms | 1.8× | 1.4% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 595.76ms | 1.05s | 1.8× | 1.1% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 385.63ms | 655.52ms | 1.7× | 2.3% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 365.09ms | 592.81ms | 1.6× | 1.0% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.67s | 2.74s | 1.0× | 1.1% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.86s | 2.87s | 1.0× | 1.6% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.02s | 1.93s | 1.0× | 2.0% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 1.99s | 2.06s | 1.0× | 1.1% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 585.70ms | 822.72ms | 1.4× | 1.6% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 930.08ms | 1.16s | 1.3× | 4.1% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 976.21ms | 1.03s | 1.1× | 3.4% | **FAIL** |
| File-backed | Writes | compositepk | 8 | 55 | 452.50ms | 666.23ms | 1.5× | 1.7% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.50s | 2.73s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.80s | 2.89s | 1.0× | 2.0% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 1.77s | 1.84s | 1.0× | 2.1% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.01s | 2.10s | 1.0× | 1.2% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 189.13ms | 589.05ms | 3.1× | 6.4% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 201.66ms | 598.49ms | 3.0× | 7.5% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 201.72ms | 486.43ms | 2.4× | 5.4% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 81.43ms | 304.80ms | 3.7× | 5.6% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.02ms | 29.71ms | 1.2× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 11.40ms | 11.60ms | 1.0× | 2.7% | PASS |
| mem_reads | `oltp_sum_range` | 9.50ms | 11.22ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 2.71ms | 2.95ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.63ms | 4.05ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_index_scan` | 3.81ms | 5.21ms | 1.4× | 2.1% | PASS |
| mem_reads | `select_random_points` | 10.16ms | 10.92ms | 1.1× | 2.8% | PASS |
| mem_reads | `select_random_ranges` | 2.97ms | 3.92ms | 1.3× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.13ms | 4.28ms | 1.0× | 2.2% | PASS |
| mem_reads | `groupby_scan` | 30.70ms | 33.70ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 5.79ms | 8.15ms | 1.4× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 3.25ms | 4.46ms | 1.4× | 2.6% | PASS |
| mem_reads | `types_table_scan` | 1.03s | 1.16s | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.19s | 1.26s | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 106.47ms | 120.66ms | 1.1× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 170.55ms | 271.40ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 14.69ms | 28.22ms | 1.9× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 49.73ms | 102.61ms | 2.1× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 33.80ms | 64.31ms | 1.9× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 43.50ms | 77.91ms | 1.8× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 21.28ms | 49.87ms | 2.3× | 1.2% | FAIL |
| mem_writes | `types_delete_insert` | 23.77ms | 40.65ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 67.89ms | 116.61ms | 1.7× | 1.8% | PASS |
| file_reads | `oltp_point_select` | 92.08ms | 46.43ms | 0.5× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 18.19ms | 13.43ms | 0.7× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 16.61ms | 13.11ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.49ms | 3.17ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_distinct_range` | 4.46ms | 4.31ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 10.97ms | 7.29ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 17.16ms | 12.74ms | 0.7× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 9.95ms | 5.70ms | 0.6× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.20ms | 6.27ms | 0.6× | 1.1% | PASS |
| file_reads | `groupby_scan` | 31.51ms | 34.12ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 9.86ms | 9.74ms | 1.0× | 1.8% | PASS |
| file_reads | `index_join_scan` | 4.20ms | 4.88ms | 1.2× | 2.2% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.17s | 1.1× | 0.8% | PASS |
| file_reads | `table_scan` | 1.19s | 1.26s | 1.1× | 1.1% | PASS |
| file_reads | `oltp_read_only` | 207.07ms | 145.66ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 184.64ms | 281.05ms | 1.5× | 1.3% | PASS |
| file_writes | `oltp_insert` | 21.46ms | 31.44ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 77.35ms | 116.42ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 58.45ms | 76.78ms | 1.3× | 1.4% | PASS |
| file_writes | `oltp_delete_insert` | 66.81ms | 89.83ms | 1.3× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 45.00ms | 58.67ms | 1.3× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 39.10ms | 44.07ms | 1.1× | 2.0% | PASS |
| file_writes | `oltp_read_write` | 92.90ms | 124.47ms | 1.3× | 1.8% | PASS |
| ac_reads | `oltp_point_select` | 45.55ms | 46.34ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 13.72ms | 13.38ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 12.16ms | 13.13ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 3.08ms | 3.17ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.00ms | 4.29ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 6.39ms | 7.22ms | 1.1× | 1.9% | PASS |
| ac_reads | `select_random_points` | 12.49ms | 12.81ms | 1.0× | 1.9% | PASS |
| ac_reads | `select_random_ranges` | 5.32ms | 5.67ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 6.65ms | 6.23ms | 0.9× | 1.8% | PASS |
| ac_reads | `groupby_scan` | 30.80ms | 34.11ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 7.37ms | 9.59ms | 1.3× | 1.9% | PASS |
| ac_reads | `index_join_scan` | 3.67ms | 4.86ms | 1.3× | 2.0% | PASS |
| ac_reads | `types_table_scan` | 1.03s | 1.16s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.26s | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_read_only` | 137.99ms | 145.97ms | 1.1× | 0.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.93ms | 60.08ms | 3.0× | 5.4% | PASS |
| ac_writes | `oltp_insert_ac` | 23.71ms | 72.92ms | 3.1× | 8.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.37ms | 88.26ms | 3.2× | 6.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.50ms | 65.89ms | 3.1× | 6.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.86ms | 76.63ms | 3.4× | 5.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.59ms | 76.09ms | 3.1× | 7.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.73ms | 67.57ms | 3.3× | 5.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.45ms | 81.62ms | 2.9× | 6.6% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.84ms | 38.58ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 17.68ms | 14.20ms | 0.8× | 1.1% | PASS |
| mem_reads | `oltp_sum_range` | 15.71ms | 13.88ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 3.36ms | 3.25ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.34ms | 4.39ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.03ms | 6.21ms | 1.5× | 2.2% | PASS |
| mem_reads | `select_random_points` | 23.31ms | 21.05ms | 0.9× | 2.7% | PASS |
| mem_reads | `select_random_ranges` | 3.55ms | 5.29ms | 1.5× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.58ms | 1.1× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 35.00ms | 35.04ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 10.74ms | 9.28ms | 0.9× | 2.7% | PASS |
| mem_reads | `index_join_scan` | 3.93ms | 5.49ms | 1.4× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 1.08s | 1.20s | 1.1× | 1.0% | PASS |
| mem_reads | `table_scan` | 1.25s | 1.30s | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 144.30ms | 140.62ms | 1.0× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 235.05ms | 378.18ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 17.67ms | 40.15ms | 2.3× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 67.19ms | 152.73ms | 2.3× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 49.72ms | 92.67ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 54.94ms | 110.69ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 28.45ms | 66.87ms | 2.4× | 1.6% | FAIL |
| mem_writes | `types_delete_insert` | 40.13ms | 59.62ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 102.62ms | 152.52ms | 1.5× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 107.45ms | 57.23ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 25.53ms | 16.39ms | 0.6× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 23.66ms | 16.19ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_order_range` | 4.37ms | 3.62ms | 0.8× | 2.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.43ms | 4.76ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 11.25ms | 8.35ms | 0.7× | 1.2% | PASS |
| file_reads | `select_random_points` | 32.00ms | 24.04ms | 0.8× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 10.60ms | 7.18ms | 0.7× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 11.29ms | 6.75ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 36.31ms | 35.66ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 15.32ms | 11.18ms | 0.7× | 1.8% | PASS |
| file_reads | `index_join_scan` | 4.92ms | 5.94ms | 1.2× | 3.0% | PASS |
| file_reads | `types_table_scan` | 1.08s | 1.20s | 1.1× | 0.8% | PASS |
| file_reads | `table_scan` | 1.25s | 1.30s | 1.0× | 1.9% | PASS |
| file_reads | `oltp_read_only` | 250.54ms | 168.30ms | 0.7× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 259.00ms | 393.31ms | 1.5× | 0.9% | PASS |
| file_writes | `oltp_insert` | 25.20ms | 47.69ms | 1.9× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 128.93ms | 172.64ms | 1.3× | 12.5% | PASS |
| file_writes | `oltp_update_non_index` | 98.97ms | 108.90ms | 1.1× | 6.4% | PASS |
| file_writes | `oltp_delete_insert` | 96.90ms | 127.83ms | 1.3× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 94.99ms | 80.70ms | 0.8× | 9.5% | PASS |
| file_writes | `types_delete_insert` | 73.36ms | 68.51ms | 0.9× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 152.72ms | 164.70ms | 1.1× | 7.9% | PASS |
| ac_reads | `oltp_point_select` | 59.67ms | 56.59ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 20.59ms | 16.13ms | 0.8× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 18.54ms | 16.01ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_order_range` | 3.86ms | 3.56ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 4.87ms | 4.71ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_index_scan` | 6.76ms | 8.38ms | 1.2× | 2.1% | PASS |
| ac_reads | `select_random_points` | 27.54ms | 24.38ms | 0.9× | 3.4% | PASS |
| ac_reads | `select_random_ranges` | 6.03ms | 7.19ms | 1.2× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 6.97ms | 6.72ms | 1.0× | 2.0% | PASS |
| ac_reads | `groupby_scan` | 35.70ms | 35.68ms | 1.0× | 1.1% | PASS |
| ac_reads | `index_join` | 12.97ms | 11.18ms | 0.9× | 2.3% | PASS |
| ac_reads | `index_join_scan` | 4.43ms | 6.03ms | 1.4× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 1.11s | 1.21s | 1.1× | 2.0% | PASS |
| ac_reads | `table_scan` | 1.30s | 1.32s | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_read_only` | 182.47ms | 169.75ms | 0.9× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.45ms | 59.40ms | 2.6× | 9.2% | PASS |
| ac_writes | `oltp_insert_ac` | 25.77ms | 70.69ms | 2.7× | 6.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.84ms | 84.59ms | 3.2× | 5.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.31ms | 73.16ms | 3.3× | 8.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.23ms | 79.46ms | 3.0× | 7.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.66ms | 77.61ms | 3.1× | 5.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.02ms | 69.27ms | 3.1× | 8.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.38ms | 84.32ms | 2.7× | 7.7% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 28.56ms | 26.05ms | 0.9× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 11.81ms | 9.49ms | 0.8× | 2.0% | PASS |
| mem_reads | `oltp_sum_range` | 10.85ms | 9.30ms | 0.9× | 2.4% | PASS |
| mem_reads | `oltp_order_range` | 2.28ms | 2.10ms | 0.9× | 3.3% | PASS |
| mem_reads | `oltp_distinct_range` | 2.84ms | 2.62ms | 0.9× | 2.4% | PASS |
| mem_reads | `oltp_index_scan` | 2.82ms | 4.76ms | 1.7× | 2.5% | PASS |
| mem_reads | `select_random_points` | 20.60ms | 16.73ms | 0.8× | 4.2% | PASS |
| mem_reads | `select_random_ranges` | 2.51ms | 3.75ms | 1.5× | 2.2% | PASS |
| mem_reads | `covering_index_scan` | 2.42ms | 3.51ms | 1.5× | 3.3% | PASS |
| mem_reads | `groupby_scan` | 22.67ms | 21.69ms | 1.0× | 1.5% | PASS |
| mem_reads | `index_join` | 9.55ms | 8.71ms | 0.9× | 3.8% | PASS |
| mem_reads | `index_join_scan` | 3.34ms | 5.71ms | 1.7× | 4.1% | PASS |
| mem_reads | `types_table_scan` | 791.05ms | 819.68ms | 1.0× | 2.5% | PASS |
| mem_reads | `table_scan` | 829.12ms | 859.38ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_read_only` | 82.37ms | 76.33ms | 0.9× | 3.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 139.86ms | 214.10ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 10.85ms | 24.48ms | 2.3× | 1.6% | PASS |
| mem_writes | `oltp_update_index` | 44.32ms | 97.14ms | 2.2× | 2.6% | PASS |
| mem_writes | `oltp_update_non_index` | 34.02ms | 57.38ms | 1.7× | 2.1% | PASS |
| mem_writes | `oltp_delete_insert` | 36.56ms | 75.28ms | 2.1× | 1.9% | PASS |
| mem_writes | `oltp_write_only` | 21.14ms | 48.10ms | 2.3× | 2.5% | PASS |
| mem_writes | `types_delete_insert` | 27.95ms | 39.70ms | 1.4× | 2.5% | PASS |
| mem_writes | `oltp_read_write` | 70.94ms | 99.34ms | 1.4× | 2.6% | PASS |
| file_reads | `oltp_point_select` | 81.90ms | 39.40ms | 0.5× | 1.9% | PASS |
| file_reads | `oltp_range_select` | 17.12ms | 10.51ms | 0.6× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 17.20ms | 10.83ms | 0.6× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 3.00ms | 2.30ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_distinct_range` | 3.40ms | 2.74ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 8.67ms | 5.97ms | 0.7× | 2.0% | PASS |
| file_reads | `select_random_points` | 21.79ms | 15.26ms | 0.7× | 5.0% | PASS |
| file_reads | `select_random_ranges` | 7.86ms | 5.10ms | 0.6× | 2.1% | PASS |
| file_reads | `covering_index_scan` | 8.56ms | 4.86ms | 0.6× | 1.4% | PASS |
| file_reads | `groupby_scan` | 22.81ms | 21.24ms | 0.9× | 1.7% | PASS |
| file_reads | `index_join` | 11.59ms | 7.55ms | 0.7× | 3.4% | PASS |
| file_reads | `index_join_scan` | 3.52ms | 4.56ms | 1.3× | 2.8% | PASS |
| file_reads | `types_table_scan` | 724.26ms | 780.61ms | 1.1× | 2.4% | PASS |
| file_reads | `table_scan` | 904.55ms | 910.91ms | 1.0× | 2.0% | PASS |
| file_reads | `oltp_read_only` | 179.46ms | 106.73ms | 0.6× | 2.7% | PASS |
| file_writes | `oltp_bulk_insert` | 209.41ms | 288.65ms | 1.4× | 2.4% | PASS |
| file_writes | `oltp_insert` | 19.00ms | 47.81ms | 2.5× | 2.6% | FAIL |
| file_writes | `oltp_update_index` | 144.50ms | 168.63ms | 1.2× | 3.0% | PASS |
| file_writes | `oltp_update_non_index` | 120.92ms | 108.68ms | 0.9× | 2.8% | PASS |
| file_writes | `oltp_delete_insert` | 143.57ms | 128.57ms | 0.9× | 3.8% | PASS |
| file_writes | `oltp_write_only` | 99.91ms | 88.77ms | 0.9× | 4.6% | PASS |
| file_writes | `types_delete_insert` | 102.16ms | 69.24ms | 0.7× | 7.8% | PASS |
| file_writes | `oltp_read_write` | 136.74ms | 131.12ms | 1.0× | 4.4% | PASS |
| ac_reads | `oltp_point_select` | 40.07ms | 37.52ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_range_select` | 12.08ms | 9.97ms | 0.8× | 2.9% | PASS |
| ac_reads | `oltp_sum_range` | 12.10ms | 10.08ms | 0.8× | 2.5% | PASS |
| ac_reads | `oltp_order_range` | 2.50ms | 2.22ms | 0.9× | 2.2% | PASS |
| ac_reads | `oltp_distinct_range` | 3.04ms | 2.73ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_index_scan` | 5.00ms | 5.90ms | 1.2× | 2.1% | PASS |
| ac_reads | `select_random_points` | 19.72ms | 15.96ms | 0.8× | 5.1% | PASS |
| ac_reads | `select_random_ranges` | 4.37ms | 5.20ms | 1.2× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 5.00ms | 4.95ms | 1.0× | 2.3% | PASS |
| ac_reads | `groupby_scan` | 23.07ms | 21.84ms | 0.9× | 1.3% | PASS |
| ac_reads | `index_join` | 10.64ms | 7.92ms | 0.7× | 4.9% | PASS |
| ac_reads | `index_join_scan` | 3.35ms | 4.63ms | 1.4× | 3.5% | PASS |
| ac_reads | `types_table_scan` | 732.48ms | 778.11ms | 1.1× | 2.1% | PASS |
| ac_reads | `table_scan` | 797.26ms | 842.13ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 101.04ms | 93.87ms | 0.9× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.85ms | 50.09ms | 2.1× | 3.2% | PASS |
| ac_writes | `oltp_insert_ac` | 26.84ms | 60.70ms | 2.3× | 6.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.44ms | 67.91ms | 2.5× | 5.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.14ms | 56.86ms | 2.7× | 5.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.73ms | 64.33ms | 2.4× | 7.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.30ms | 61.16ms | 2.4× | 5.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.58ms | 58.02ms | 2.7× | 6.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.83ms | 67.35ms | 2.3× | 5.4% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 21.35ms | 25.47ms | 1.2× | 0.9% | PASS |
| mem_reads | `oltp_range_select` | 13.96ms | 14.18ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_sum_range` | 13.37ms | 13.56ms | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_order_range` | 2.67ms | 2.74ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.35ms | 3.44ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.27ms | 4.34ms | 1.3× | 1.3% | PASS |
| mem_reads | `select_random_points` | 19.88ms | 23.28ms | 1.2× | 0.8% | PASS |
| mem_reads | `select_random_ranges` | 5.18ms | 6.05ms | 1.2× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 2.61ms | 2.94ms | 1.1× | 1.3% | PASS |
| mem_reads | `groupby_scan` | 25.68ms | 28.01ms | 1.1× | 0.4% | PASS |
| mem_reads | `index_join` | 5.82ms | 7.95ms | 1.4× | 0.9% | PASS |
| mem_reads | `index_join_scan` | 2.92ms | 4.50ms | 1.5× | 1.7% | PASS |
| mem_reads | `types_table_scan` | 799.20ms | 861.26ms | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 914.03ms | 946.13ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 95.83ms | 108.21ms | 1.1× | 0.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 143.75ms | 200.44ms | 1.4× | 0.5% | PASS |
| mem_writes | `oltp_insert` | 11.97ms | 21.09ms | 1.8× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 43.95ms | 83.85ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 31.84ms | 55.05ms | 1.7× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 32.45ms | 61.59ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 18.11ms | 38.38ms | 2.1× | 1.1% | PASS |
| mem_writes | `types_delete_insert` | 20.28ms | 33.16ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 62.74ms | 99.25ms | 1.6× | 1.0% | PASS |
| file_reads | `oltp_point_select` | 42.86ms | 30.15ms | 0.7× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 15.72ms | 14.53ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 15.22ms | 14.03ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 2.85ms | 2.79ms | 1.0× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 3.56ms | 3.52ms | 1.0× | 2.0% | PASS |
| file_reads | `oltp_index_scan` | 5.51ms | 4.90ms | 0.9× | 1.1% | PASS |
| file_reads | `select_random_points` | 21.82ms | 23.78ms | 1.1× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 7.35ms | 6.55ms | 0.9× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 4.79ms | 3.33ms | 0.7× | 1.3% | PASS |
| file_reads | `groupby_scan` | 25.65ms | 27.85ms | 1.1× | 0.6% | PASS |
| file_reads | `index_join` | 7.04ms | 8.12ms | 1.2× | 0.9% | PASS |
| file_reads | `index_join_scan` | 3.21ms | 4.65ms | 1.4× | 1.9% | PASS |
| file_reads | `types_table_scan` | 805.48ms | 861.40ms | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 904.07ms | 941.03ms | 1.0× | 1.8% | PASS |
| file_reads | `oltp_read_only` | 126.70ms | 115.85ms | 0.9× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 152.39ms | 209.74ms | 1.4× | 0.6% | PASS |
| file_writes | `oltp_insert` | 15.27ms | 26.02ms | 1.7× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 57.99ms | 96.87ms | 1.7× | 1.9% | PASS |
| file_writes | `oltp_update_non_index` | 43.88ms | 64.47ms | 1.5× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 46.11ms | 73.10ms | 1.6× | 1.3% | PASS |
| file_writes | `oltp_write_only` | 31.55ms | 47.09ms | 1.5× | 4.9% | PASS |
| file_writes | `types_delete_insert` | 29.21ms | 39.45ms | 1.4× | 2.0% | PASS |
| file_writes | `oltp_read_write` | 76.11ms | 109.48ms | 1.4× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 28.39ms | 30.28ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 14.66ms | 14.71ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_sum_range` | 13.82ms | 14.04ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 2.73ms | 2.77ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 3.35ms | 3.45ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 3.98ms | 4.61ms | 1.2× | 1.9% | PASS |
| ac_reads | `select_random_points` | 20.11ms | 23.42ms | 1.2× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 5.89ms | 6.49ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 3.34ms | 3.31ms | 1.0× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 25.44ms | 27.77ms | 1.1× | 0.4% | PASS |
| ac_reads | `index_join` | 6.31ms | 7.96ms | 1.3× | 1.0% | PASS |
| ac_reads | `index_join_scan` | 3.20ms | 4.62ms | 1.4× | 2.8% | PASS |
| ac_reads | `types_table_scan` | 810.23ms | 869.79ms | 1.1× | 1.1% | PASS |
| ac_reads | `table_scan` | 948.82ms | 967.57ms | 1.0× | 5.2% | PASS |
| ac_reads | `oltp_read_only` | 117.50ms | 121.91ms | 1.0× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 8.74ms | 28.70ms | 3.3× | 7.1% | PASS |
| ac_writes | `oltp_insert_ac` | 9.90ms | 37.90ms | 3.8× | 4.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 10.69ms | 43.52ms | 4.1× | 3.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 9.34ms | 34.45ms | 3.7× | 7.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 10.80ms | 41.96ms | 3.9× | 6.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 9.71ms | 38.77ms | 4.0× | 2.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 8.53ms | 33.56ms | 3.9× | 8.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 13.72ms | 45.92ms | 3.3× | 4.1% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 11s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 102.36ms | 130.00ms | 78.7% | 0.3% | PASS |
| `status_dirty_many_tables` | 106.30ms | 130.00ms | 81.8% | 0.3% | PASS |
| `diff_regular_working_one_table` | 97.69ms | 120.00ms | 81.4% | 0.2% | PASS |
| `diff_regular_working_many_tables` | 111.06ms | 140.00ms | 79.3% | 0.2% | PASS |
| `diff_stat_working_many_tables` | 111.11ms | 140.00ms | 79.4% | 0.2% | PASS |
| `diff_schema_working_many_tables` | 111.72ms | 140.00ms | 79.8% | 0.2% | PASS |
| `branch_list_many_branches` | 20.45ms | 35.00ms | 58.4% | 1.0% | PASS |
| `branch_create_delete` | 22.48ms | 40.00ms | 56.2% | 1.2% | PASS |
| `checkout_branch_clean` | 60.94ms | 150.00ms | 40.6% | 0.7% | PASS |
| `merge_data_no_conflicts` | 27.00ms | 50.00ms | 54.0% | 0.6% | PASS |
| `merge_schema_no_conflicts` | 18.92ms | 35.00ms | 54.1% | 1.3% | PASS |
| `merge_data_conflicts` | 30.81ms | 180.00ms | 17.1% | 0.8% | PASS |
| `merge_data_conflicts_with_resolve` | 30.80ms | 180.00ms | 17.1% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
