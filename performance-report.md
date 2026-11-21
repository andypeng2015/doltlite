# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-21 18:27 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261116.202.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/44927048340)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.59s | 11.34s | 1.1× | 1.5% | **PASS** |
| Writes | 2.19s | 3.82s | 1.7× | 1.4% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.58s | 11.57s | 1.0× | 1.6% | **PASS** |
| Writes | 3.09s | 4.18s | 1.4× | 1.6% | **PASS** |
| Autocommit writes | 720.09ms | 2.23s | 3.1× | 5.5% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.41s | 2.67s | 1.1× | 1.7% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.53s | 2.79s | 1.1× | 1.3% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.88s | 2.96s | 1.0× | 2.0% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.78s | 2.92s | 1.1× | 1.4% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 429.22ms | 750.48ms | 1.7× | 1.4% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 571.89ms | 1.04s | 1.8× | 1.5% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 582.15ms | 1.02s | 1.7× | 1.5% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 602.34ms | 1.02s | 1.7× | 1.3% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 2.75s | 2.76s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.82s | 2.87s | 1.0× | 1.9% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.01s | 2.95s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.00s | 2.99s | 1.0× | 2.0% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 592.02ms | 819.55ms | 1.4× | 1.7% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 920.06ms | 1.16s | 1.3× | 3.8% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 813.87ms | 1.11s | 1.4× | 1.4% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 768.52ms | 1.09s | 1.4× | 1.5% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.54s | 2.74s | 1.1× | 1.6% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.79s | 2.90s | 1.0× | 2.0% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.85s | 2.97s | 1.0× | 1.3% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.94s | 3.00s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 194.09ms | 603.89ms | 3.1× | 7.6% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 185.88ms | 556.90ms | 3.0× | 4.6% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 149.93ms | 481.90ms | 3.2× | 4.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 190.18ms | 582.71ms | 3.1× | 6.4% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.81ms | 29.40ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 10.93ms | 11.48ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 9.48ms | 11.11ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 2.65ms | 2.93ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 3.61ms | 4.08ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.81ms | 5.23ms | 1.4× | 2.7% | PASS |
| mem_reads | `select_random_points` | 10.12ms | 10.89ms | 1.1× | 3.7% | PASS |
| mem_reads | `select_random_ranges` | 2.98ms | 3.89ms | 1.3× | 2.5% | PASS |
| mem_reads | `covering_index_scan` | 4.12ms | 4.16ms | 1.0× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 30.81ms | 33.48ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 5.69ms | 7.78ms | 1.4× | 1.8% | PASS |
| mem_reads | `index_join_scan` | 3.23ms | 4.43ms | 1.4× | 2.4% | PASS |
| mem_reads | `types_table_scan` | 1.03s | 1.16s | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.16s | 1.26s | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 104.16ms | 120.88ms | 1.2× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 170.48ms | 271.76ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 14.65ms | 27.99ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 49.62ms | 100.72ms | 2.0× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 34.73ms | 63.75ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 44.48ms | 78.53ms | 1.8× | 1.9% | PASS |
| mem_writes | `oltp_write_only` | 21.47ms | 49.63ms | 2.3× | 1.2% | FAIL |
| mem_writes | `types_delete_insert` | 24.19ms | 41.03ms | 1.7× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 69.60ms | 117.08ms | 1.7× | 1.8% | PASS |
| file_reads | `oltp_point_select` | 93.29ms | 47.26ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 18.98ms | 13.64ms | 0.7× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 17.48ms | 13.35ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 3.58ms | 3.22ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 4.58ms | 4.37ms | 1.0× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 11.28ms | 7.41ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 18.83ms | 12.95ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_ranges` | 10.12ms | 5.78ms | 0.6× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 11.49ms | 6.34ms | 0.6× | 2.5% | PASS |
| file_reads | `groupby_scan` | 32.15ms | 34.07ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 9.78ms | 9.41ms | 1.0× | 2.5% | PASS |
| file_reads | `index_join_scan` | 4.28ms | 4.84ms | 1.1× | 3.1% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.17s | 1.1× | 1.5% | PASS |
| file_reads | `table_scan` | 1.25s | 1.28s | 1.0× | 1.0% | PASS |
| file_reads | `oltp_read_only` | 208.95ms | 149.49ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 184.22ms | 281.65ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_insert` | 21.77ms | 31.57ms | 1.5× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 77.18ms | 112.64ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 58.53ms | 75.05ms | 1.3× | 2.2% | PASS |
| file_writes | `oltp_delete_insert` | 67.19ms | 89.22ms | 1.3× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 45.00ms | 57.89ms | 1.3× | 1.6% | PASS |
| file_writes | `types_delete_insert` | 39.93ms | 44.18ms | 1.1× | 1.8% | PASS |
| file_writes | `oltp_read_write` | 98.20ms | 127.36ms | 1.3× | 1.7% | PASS |
| ac_reads | `oltp_point_select` | 46.04ms | 46.89ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 13.75ms | 13.50ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 12.47ms | 13.30ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.12ms | 3.23ms | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.07ms | 4.36ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.61ms | 7.41ms | 1.1× | 1.7% | PASS |
| ac_reads | `select_random_points` | 13.57ms | 12.91ms | 1.0× | 2.8% | PASS |
| ac_reads | `select_random_ranges` | 5.39ms | 5.72ms | 1.1× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 6.80ms | 6.30ms | 0.9× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 31.41ms | 33.99ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 7.23ms | 9.54ms | 1.3× | 2.1% | PASS |
| ac_reads | `index_join_scan` | 3.76ms | 4.85ms | 1.3× | 2.7% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.17s | 1.1× | 1.4% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.27s | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_read_only` | 139.20ms | 147.60ms | 1.1× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.54ms | 60.09ms | 2.8× | 5.4% | PASS |
| ac_writes | `oltp_insert_ac` | 24.14ms | 74.60ms | 3.1× | 6.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.58ms | 88.92ms | 3.3× | 7.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.83ms | 69.79ms | 3.2× | 9.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.51ms | 79.94ms | 3.4× | 9.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.51ms | 78.89ms | 3.1× | 7.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.19ms | 68.03ms | 3.2× | 9.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.80ms | 83.64ms | 2.8× | 5.7% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.02ms | 38.69ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_range_select` | 16.10ms | 14.43ms | 0.9× | 2.6% | PASS |
| mem_reads | `oltp_sum_range` | 14.23ms | 14.17ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 3.12ms | 3.27ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.13ms | 4.43ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.78ms | 6.04ms | 1.6× | 1.3% | PASS |
| mem_reads | `select_random_points` | 19.70ms | 20.60ms | 1.0× | 1.3% | PASS |
| mem_reads | `select_random_ranges` | 3.19ms | 5.26ms | 1.6× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 4.16ms | 4.46ms | 1.1× | 1.2% | PASS |
| mem_reads | `groupby_scan` | 34.13ms | 35.53ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 9.74ms | 8.81ms | 0.9× | 1.4% | PASS |
| mem_reads | `index_join_scan` | 3.49ms | 5.67ms | 1.6× | 3.4% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.19s | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.19s | 1.29s | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 139.13ms | 143.10ms | 1.0× | 1.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 234.41ms | 375.85ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 17.46ms | 39.82ms | 2.3× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 63.68ms | 148.28ms | 2.3× | 1.7% | FAIL |
| mem_writes | `oltp_update_non_index` | 46.70ms | 90.75ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 52.62ms | 109.11ms | 2.1× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 27.13ms | 65.36ms | 2.4× | 1.6% | FAIL |
| mem_writes | `types_delete_insert` | 36.73ms | 57.95ms | 1.6× | 2.0% | PASS |
| mem_writes | `oltp_read_write` | 93.16ms | 149.01ms | 1.6× | 1.2% | PASS |
| file_reads | `oltp_point_select` | 103.52ms | 56.50ms | 0.5× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 24.59ms | 16.51ms | 0.7× | 3.4% | PASS |
| file_reads | `oltp_sum_range` | 22.84ms | 16.27ms | 0.7× | 2.8% | PASS |
| file_reads | `oltp_order_range` | 4.30ms | 3.55ms | 0.8× | 2.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.34ms | 4.69ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 11.27ms | 8.04ms | 0.7× | 1.9% | PASS |
| file_reads | `select_random_points` | 30.11ms | 23.70ms | 0.8× | 3.7% | PASS |
| file_reads | `select_random_ranges` | 10.48ms | 7.12ms | 0.7× | 1.6% | PASS |
| file_reads | `covering_index_scan` | 11.57ms | 6.76ms | 0.6× | 1.6% | PASS |
| file_reads | `groupby_scan` | 36.31ms | 36.18ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 14.85ms | 10.29ms | 0.7× | 3.8% | PASS |
| file_reads | `index_join_scan` | 4.81ms | 5.96ms | 1.2× | 3.8% | PASS |
| file_reads | `types_table_scan` | 1.10s | 1.21s | 1.1× | 2.7% | PASS |
| file_reads | `table_scan` | 1.19s | 1.29s | 1.1× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 250.36ms | 172.16ms | 0.7× | 1.4% | PASS |
| file_writes | `oltp_bulk_insert` | 258.89ms | 394.58ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 25.50ms | 48.51ms | 1.9× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 124.25ms | 171.89ms | 1.4× | 11.6% | PASS |
| file_writes | `oltp_update_non_index` | 103.24ms | 108.84ms | 1.1× | 9.4% | PASS |
| file_writes | `oltp_delete_insert` | 95.49ms | 126.62ms | 1.3× | 1.9% | PASS |
| file_writes | `oltp_write_only` | 89.26ms | 80.17ms | 0.9× | 10.5% | PASS |
| file_writes | `types_delete_insert` | 71.25ms | 67.22ms | 0.9× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 152.18ms | 162.69ms | 1.1× | 5.7% | PASS |
| ac_reads | `oltp_point_select` | 58.80ms | 56.93ms | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_range_select` | 20.79ms | 16.63ms | 0.8× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 18.79ms | 16.66ms | 0.9× | 2.2% | PASS |
| ac_reads | `oltp_order_range` | 3.94ms | 3.62ms | 0.9× | 2.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.93ms | 4.79ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.82ms | 8.54ms | 1.3× | 1.9% | PASS |
| ac_reads | `select_random_points` | 24.17ms | 23.88ms | 1.0× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 5.80ms | 7.14ms | 1.2× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 6.74ms | 6.66ms | 1.0× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 35.57ms | 36.22ms | 1.0× | 1.2% | PASS |
| ac_reads | `index_join` | 11.85ms | 10.21ms | 0.9× | 2.0% | PASS |
| ac_reads | `index_join_scan` | 4.36ms | 5.97ms | 1.4× | 2.8% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.20s | 1.1× | 1.3% | PASS |
| ac_reads | `table_scan` | 1.34s | 1.33s | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_read_only` | 182.29ms | 172.89ms | 0.9× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.23ms | 56.00ms | 2.6× | 4.6% | PASS |
| ac_writes | `oltp_insert_ac` | 23.30ms | 67.25ms | 2.9× | 3.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.30ms | 80.55ms | 3.3× | 3.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.73ms | 64.62ms | 3.1× | 4.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.91ms | 73.80ms | 3.1× | 5.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.32ms | 71.25ms | 3.2× | 4.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.98ms | 66.38ms | 3.2× | 6.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.11ms | 77.06ms | 2.6× | 4.7% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 38.12ms | 36.35ms | 1.0× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 18.89ms | 13.89ms | 0.7× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 16.61ms | 13.68ms | 0.8× | 1.8% | PASS |
| mem_reads | `oltp_order_range` | 3.53ms | 3.26ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.44ms | 4.32ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.06ms | 5.97ms | 1.5× | 2.3% | PASS |
| mem_reads | `select_random_points` | 24.87ms | 20.89ms | 0.8× | 3.9% | PASS |
| mem_reads | `select_random_ranges` | 3.58ms | 5.36ms | 1.5× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.28ms | 4.68ms | 1.1× | 2.2% | PASS |
| mem_reads | `groupby_scan` | 36.22ms | 36.55ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 11.34ms | 9.54ms | 0.8× | 3.6% | PASS |
| mem_reads | `index_join_scan` | 3.99ms | 5.62ms | 1.4× | 3.6% | PASS |
| mem_reads | `types_table_scan` | 1.21s | 1.28s | 1.1× | 2.2% | PASS |
| mem_reads | `table_scan` | 1.35s | 1.39s | 1.0× | 1.6% | PASS |
| mem_reads | `oltp_read_only` | 139.55ms | 131.59ms | 0.9× | 2.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 233.73ms | 357.17ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 18.21ms | 40.87ms | 2.2× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 65.59ms | 148.43ms | 2.3× | 1.9% | PASS |
| mem_writes | `oltp_update_non_index` | 48.26ms | 89.48ms | 1.9× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 52.57ms | 110.59ms | 2.1× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 27.85ms | 68.17ms | 2.4× | 1.6% | FAIL |
| mem_writes | `types_delete_insert` | 37.78ms | 57.26ms | 1.5× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 98.16ms | 145.30ms | 1.5× | 2.8% | PASS |
| file_reads | `oltp_point_select` | 125.01ms | 56.69ms | 0.5× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 27.72ms | 15.65ms | 0.6× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 25.66ms | 15.61ms | 0.6× | 2.7% | PASS |
| file_reads | `oltp_order_range` | 4.48ms | 3.50ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 5.48ms | 4.59ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 13.17ms | 8.14ms | 0.6× | 1.1% | PASS |
| file_reads | `select_random_points` | 33.51ms | 22.68ms | 0.7× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 12.42ms | 7.45ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 13.58ms | 6.91ms | 0.5× | 1.2% | PASS |
| file_reads | `groupby_scan` | 37.48ms | 36.79ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 16.75ms | 10.96ms | 0.7× | 2.6% | PASS |
| file_reads | `index_join_scan` | 5.07ms | 5.90ms | 1.2× | 2.5% | PASS |
| file_reads | `types_table_scan` | 1.14s | 1.24s | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 1.27s | 1.35s | 1.1× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 268.43ms | 162.74ms | 0.6× | 1.4% | PASS |
| file_writes | `oltp_bulk_insert` | 254.24ms | 368.35ms | 1.4× | 0.7% | PASS |
| file_writes | `oltp_insert` | 25.16ms | 47.60ms | 1.9× | 1.2% | PASS |
| file_writes | `oltp_update_index` | 98.78ms | 164.32ms | 1.7× | 2.4% | PASS |
| file_writes | `oltp_update_non_index` | 91.16ms | 102.17ms | 1.1× | 10.5% | PASS |
| file_writes | `oltp_delete_insert` | 88.08ms | 123.96ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_write_only` | 57.52ms | 79.10ms | 1.4× | 1.4% | PASS |
| file_writes | `types_delete_insert` | 65.95ms | 65.22ms | 1.0× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 132.97ms | 157.59ms | 1.2× | 1.9% | PASS |
| ac_reads | `oltp_point_select` | 63.76ms | 56.50ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 22.03ms | 16.05ms | 0.7× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 19.57ms | 15.73ms | 0.8× | 1.8% | PASS |
| ac_reads | `oltp_order_range` | 3.92ms | 3.50ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 4.83ms | 4.58ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 7.16ms | 8.12ms | 1.1× | 1.1% | PASS |
| ac_reads | `select_random_points` | 26.19ms | 22.22ms | 0.8× | 2.4% | PASS |
| ac_reads | `select_random_ranges` | 6.33ms | 7.41ms | 1.2× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 7.30ms | 6.84ms | 0.9× | 0.8% | PASS |
| ac_reads | `groupby_scan` | 36.22ms | 36.66ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 12.70ms | 10.77ms | 0.8× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 4.42ms | 5.94ms | 1.3× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 1.14s | 1.24s | 1.1× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.30s | 1.37s | 1.1× | 2.8% | PASS |
| ac_reads | `oltp_read_only` | 191.04ms | 165.75ms | 0.9× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 16.12ms | 44.93ms | 2.8× | 3.4% | PASS |
| ac_writes | `oltp_insert_ac` | 19.18ms | 61.31ms | 3.2× | 4.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.10ms | 71.58ms | 3.6× | 5.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 15.18ms | 53.61ms | 3.5× | 3.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 18.68ms | 63.66ms | 3.4× | 4.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 18.73ms | 62.69ms | 3.3× | 3.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 16.20ms | 54.62ms | 3.4× | 5.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.74ms | 69.50ms | 2.7× | 3.5% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.53ms | 40.84ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 20.32ms | 21.77ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_sum_range` | 17.96ms | 21.06ms | 1.2× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 3.68ms | 3.99ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.65ms | 5.15ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 4.57ms | 6.24ms | 1.4× | 1.4% | PASS |
| mem_reads | `select_random_points` | 26.96ms | 31.80ms | 1.2× | 2.9% | PASS |
| mem_reads | `select_random_ranges` | 7.95ms | 9.21ms | 1.2× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.38ms | 4.81ms | 1.1× | 3.0% | PASS |
| mem_reads | `groupby_scan` | 37.37ms | 40.61ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 7.84ms | 10.59ms | 1.4× | 1.5% | PASS |
| mem_reads | `index_join_scan` | 3.61ms | 5.53ms | 1.5× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.21s | 1.1× | 1.3% | PASS |
| mem_reads | `table_scan` | 1.40s | 1.34s | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_read_only` | 154.50ms | 175.76ms | 1.1× | 1.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 243.59ms | 358.61ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.73ms | 36.63ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 66.04ms | 134.05ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 52.02ms | 89.47ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 53.64ms | 108.11ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 26.55ms | 62.40ms | 2.3× | 1.4% | FAIL |
| mem_writes | `types_delete_insert` | 33.03ms | 55.98ms | 1.7× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 108.74ms | 171.63ms | 1.6× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 106.62ms | 61.14ms | 0.6× | 1.8% | PASS |
| file_reads | `oltp_range_select` | 27.58ms | 23.68ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 26.15ms | 23.21ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_order_range` | 4.70ms | 4.34ms | 0.9× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 5.76ms | 5.48ms | 1.0× | 2.4% | PASS |
| file_reads | `oltp_index_scan` | 11.38ms | 7.88ms | 0.7× | 2.3% | PASS |
| file_reads | `select_random_points` | 34.58ms | 33.73ms | 1.0× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 15.28ms | 11.12ms | 0.7× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 11.41ms | 6.47ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 38.83ms | 41.16ms | 1.1× | 1.1% | PASS |
| file_reads | `index_join` | 12.32ms | 12.42ms | 1.0× | 1.8% | PASS |
| file_reads | `index_join_scan` | 5.00ms | 5.81ms | 1.2× | 2.3% | PASS |
| file_reads | `types_table_scan` | 1.11s | 1.22s | 1.1× | 2.0% | PASS |
| file_reads | `table_scan` | 1.33s | 1.32s | 1.0× | 2.8% | PASS |
| file_reads | `oltp_read_only` | 263.31ms | 203.87ms | 0.8× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 257.74ms | 370.69ms | 1.4× | 1.3% | PASS |
| file_writes | `oltp_insert` | 25.60ms | 42.44ms | 1.7× | 1.4% | PASS |
| file_writes | `oltp_update_index` | 95.23ms | 146.66ms | 1.5× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 75.41ms | 100.92ms | 1.3× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 75.55ms | 112.61ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 52.70ms | 74.78ms | 1.4× | 2.5% | PASS |
| file_writes | `types_delete_insert` | 51.26ms | 63.75ms | 1.2× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 135.05ms | 181.99ms | 1.3× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 58.29ms | 59.53ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 24.06ms | 24.03ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 20.96ms | 22.92ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 4.17ms | 4.28ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 5.23ms | 5.50ms | 1.1× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 7.56ms | 8.34ms | 1.1× | 2.0% | PASS |
| ac_reads | `select_random_points` | 29.61ms | 34.00ms | 1.1× | 1.6% | PASS |
| ac_reads | `select_random_ranges` | 10.51ms | 11.27ms | 1.1× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 7.17ms | 6.53ms | 0.9× | 2.3% | PASS |
| ac_reads | `groupby_scan` | 37.74ms | 40.77ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 9.87ms | 12.36ms | 1.3× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 4.58ms | 5.94ms | 1.3× | 3.8% | PASS |
| ac_reads | `types_table_scan` | 1.14s | 1.23s | 1.1× | 1.9% | PASS |
| ac_reads | `table_scan` | 1.40s | 1.33s | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 186.34ms | 201.11ms | 1.1× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.28ms | 57.08ms | 2.8× | 5.9% | PASS |
| ac_writes | `oltp_insert_ac` | 23.30ms | 72.88ms | 3.1× | 5.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.32ms | 82.27ms | 3.2× | 6.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.59ms | 67.41ms | 3.1× | 9.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.29ms | 75.32ms | 3.2× | 6.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.97ms | 77.69ms | 3.1× | 9.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.44ms | 67.78ms | 3.2× | 8.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.99ms | 82.28ms | 2.7× | 4.7% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 47s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 68.98ms | 130.00ms | 53.1% | 0.5% | PASS |
| `status_dirty_many_tables` | 71.37ms | 130.00ms | 54.9% | 0.4% | PASS |
| `diff_regular_working_one_table` | 65.50ms | 120.00ms | 54.6% | 0.7% | PASS |
| `diff_regular_working_many_tables` | 75.29ms | 140.00ms | 53.8% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 75.39ms | 140.00ms | 53.8% | 0.6% | PASS |
| `diff_schema_working_many_tables` | 76.10ms | 140.00ms | 54.4% | 0.7% | PASS |
| `branch_list_many_branches` | 16.46ms | 35.00ms | 47.0% | 1.4% | PASS |
| `branch_create_delete` | 23.84ms | 40.00ms | 59.6% | 4.0% | PASS |
| `checkout_branch_clean` | 102.80ms | 150.00ms | 68.5% | 6.4% | PASS |
| `merge_data_no_conflicts` | 30.57ms | 50.00ms | 61.1% | 4.2% | PASS |
| `merge_schema_no_conflicts` | 16.51ms | 35.00ms | 47.2% | 2.4% | PASS |
| `merge_data_conflicts` | 24.14ms | 180.00ms | 13.4% | 1.6% | PASS |
| `merge_data_conflicts_with_resolve` | 24.26ms | 180.00ms | 13.5% | 1.5% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
