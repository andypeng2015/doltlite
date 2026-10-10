# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-10 17:06 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261004.327.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/38063925327)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.64s | 10.03s | 1.0× | 1.5% | **PASS** |
| Writes | 1.98s | 3.27s | 1.6× | 1.4% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.40s | 10.18s | 1.0× | 1.5% | **PASS** |
| Writes | 3.58s | 4.14s | 1.2× | 3.0% | **PASS** |
| Autocommit writes | 870.89ms | 2.46s | 2.8× | 5.8% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 1.63s | 1.68s | 1.0× | 2.6% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.67s | 2.77s | 1.0× | 1.9% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.55s | 2.70s | 1.1× | 1.0% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.79s | 2.88s | 1.0× | 1.4% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 270.17ms | 442.74ms | 1.6× | 2.1% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 600.15ms | 1.01s | 1.7× | 1.8% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 498.79ms | 827.03ms | 1.7× | 0.8% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 611.05ms | 984.69ms | 1.6× | 1.5% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 1.77s | 1.67s | 0.9× | 2.4% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.93s | 2.84s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.67s | 2.72s | 1.0× | 0.9% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.04s | 2.95s | 1.0× | 1.5% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 735.22ms | 712.70ms | 1.0× | 4.6% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 942.05ms | 1.12s | 1.2× | 3.5% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.11s | 1.24s | 1.1× | 3.8% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 790.18ms | 1.08s | 1.4× | 1.6% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 1.67s | 1.68s | 1.0× | 2.2% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.76s | 2.83s | 1.0× | 1.3% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.59s | 2.72s | 1.1× | 0.9% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.86s | 2.95s | 1.0× | 1.1% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 173.24ms | 508.57ms | 2.9× | 4.7% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 205.19ms | 612.90ms | 3.0× | 5.3% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 276.31ms | 684.19ms | 2.5× | 5.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 216.15ms | 650.76ms | 3.0× | 6.7% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 15.76ms | 18.44ms | 1.2× | 3.5% | PASS |
| mem_reads | `oltp_range_select` | 6.40ms | 6.64ms | 1.0× | 3.3% | PASS |
| mem_reads | `oltp_sum_range` | 6.06ms | 6.76ms | 1.1× | 2.4% | PASS |
| mem_reads | `oltp_order_range` | 1.66ms | 1.73ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_distinct_range` | 2.15ms | 2.26ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_index_scan` | 2.50ms | 3.23ms | 1.3× | 3.1% | PASS |
| mem_reads | `select_random_points` | 8.28ms | 9.06ms | 1.1× | 5.3% | PASS |
| mem_reads | `select_random_ranges` | 2.05ms | 2.56ms | 1.2× | 1.7% | PASS |
| mem_reads | `covering_index_scan` | 2.38ms | 2.47ms | 1.0× | 2.2% | PASS |
| mem_reads | `groupby_scan` | 17.47ms | 18.39ms | 1.1× | 1.4% | PASS |
| mem_reads | `index_join` | 3.58ms | 4.82ms | 1.3× | 3.1% | PASS |
| mem_reads | `index_join_scan` | 2.46ms | 3.85ms | 1.6× | 4.5% | PASS |
| mem_reads | `types_table_scan` | 684.42ms | 715.68ms | 1.0× | 1.7% | PASS |
| mem_reads | `table_scan` | 818.36ms | 819.16ms | 1.0× | 2.7% | PASS |
| mem_reads | `oltp_read_only` | 60.01ms | 64.72ms | 1.1× | 2.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 107.81ms | 159.85ms | 1.5× | 1.4% | PASS |
| mem_writes | `oltp_insert` | 8.99ms | 16.55ms | 1.8× | 1.3% | PASS |
| mem_writes | `oltp_update_index` | 31.64ms | 60.94ms | 1.9× | 3.1% | PASS |
| mem_writes | `oltp_update_non_index` | 22.80ms | 37.71ms | 1.7× | 2.3% | PASS |
| mem_writes | `oltp_delete_insert` | 27.98ms | 45.78ms | 1.6× | 2.5% | PASS |
| mem_writes | `oltp_write_only` | 14.40ms | 31.32ms | 2.2× | 1.9% | PASS |
| mem_writes | `types_delete_insert` | 16.39ms | 24.17ms | 1.5× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 40.16ms | 66.42ms | 1.7× | 3.6% | PASS |
| file_reads | `oltp_point_select` | 72.57ms | 33.98ms | 0.5× | 1.9% | PASS |
| file_reads | `oltp_range_select` | 12.66ms | 8.41ms | 0.7× | 2.5% | PASS |
| file_reads | `oltp_sum_range` | 12.00ms | 8.36ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 2.33ms | 1.96ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 2.83ms | 2.48ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 8.60ms | 5.16ms | 0.6× | 2.7% | PASS |
| file_reads | `select_random_points` | 14.10ms | 10.53ms | 0.7× | 3.9% | PASS |
| file_reads | `select_random_ranges` | 7.70ms | 4.04ms | 0.5× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 8.52ms | 4.50ms | 0.5× | 1.7% | PASS |
| file_reads | `groupby_scan` | 17.70ms | 18.28ms | 1.0× | 2.6% | PASS |
| file_reads | `index_join` | 6.91ms | 6.41ms | 0.9× | 5.0% | PASS |
| file_reads | `index_join_scan` | 2.99ms | 3.72ms | 1.2× | 3.9% | PASS |
| file_reads | `types_table_scan` | 683.30ms | 697.65ms | 1.0× | 1.7% | PASS |
| file_reads | `table_scan` | 778.50ms | 782.87ms | 1.0× | 2.6% | PASS |
| file_reads | `oltp_read_only` | 139.48ms | 84.30ms | 0.6× | 2.4% | PASS |
| file_writes | `oltp_bulk_insert` | 140.79ms | 198.63ms | 1.4× | 3.4% | PASS |
| file_writes | `oltp_insert` | 19.82ms | 28.84ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_update_index` | 126.36ms | 109.83ms | 0.9× | 5.4% | PASS |
| file_writes | `oltp_update_non_index` | 101.27ms | 85.72ms | 0.8× | 5.1% | PASS |
| file_writes | `oltp_delete_insert` | 103.46ms | 89.07ms | 0.9× | 4.1% | PASS |
| file_writes | `oltp_write_only` | 81.55ms | 65.54ms | 0.8× | 6.6% | PASS |
| file_writes | `types_delete_insert` | 60.95ms | 44.79ms | 0.7× | 9.3% | PASS |
| file_writes | `oltp_read_write` | 101.00ms | 90.28ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 32.00ms | 32.25ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_range_select` | 8.26ms | 7.98ms | 1.0× | 3.4% | PASS |
| ac_reads | `oltp_sum_range` | 7.96ms | 8.08ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_order_range` | 1.87ms | 1.87ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 2.36ms | 2.38ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_index_scan` | 4.54ms | 4.97ms | 1.1× | 1.8% | PASS |
| ac_reads | `select_random_points` | 9.18ms | 9.91ms | 1.1× | 2.9% | PASS |
| ac_reads | `select_random_ranges` | 3.91ms | 3.99ms | 1.0× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 4.66ms | 4.38ms | 0.9× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 17.14ms | 18.03ms | 1.1× | 1.6% | PASS |
| ac_reads | `index_join` | 4.92ms | 6.19ms | 1.3× | 5.9% | PASS |
| ac_reads | `index_join_scan` | 2.58ms | 3.68ms | 1.4× | 4.7% | PASS |
| ac_reads | `types_table_scan` | 700.26ms | 710.11ms | 1.0× | 2.4% | PASS |
| ac_reads | `table_scan` | 794.41ms | 787.66ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_read_only` | 79.32ms | 82.44ms | 1.0× | 2.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.90ms | 51.71ms | 2.6× | 3.1% | PASS |
| ac_writes | `oltp_insert_ac` | 21.89ms | 61.39ms | 2.8× | 6.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 22.81ms | 70.90ms | 3.1× | 3.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.54ms | 58.26ms | 2.8× | 3.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 21.33ms | 66.19ms | 3.1× | 4.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 21.60ms | 63.89ms | 3.0× | 4.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.96ms | 65.25ms | 3.1× | 11.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 24.20ms | 70.98ms | 2.9× | 6.7% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.38ms | 38.43ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_range_select` | 16.97ms | 14.20ms | 0.8× | 3.0% | PASS |
| mem_reads | `oltp_sum_range` | 15.58ms | 13.76ms | 0.9× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 3.27ms | 3.23ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 4.37ms | 4.33ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.02ms | 6.17ms | 1.5× | 1.2% | PASS |
| mem_reads | `select_random_points` | 21.78ms | 21.03ms | 1.0× | 1.9% | PASS |
| mem_reads | `select_random_ranges` | 3.64ms | 5.34ms | 1.5× | 2.2% | PASS |
| mem_reads | `covering_index_scan` | 4.23ms | 4.57ms | 1.1× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 35.00ms | 34.63ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 10.50ms | 9.12ms | 0.9× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 3.85ms | 5.45ms | 1.4× | 3.1% | PASS |
| mem_reads | `types_table_scan` | 1.11s | 1.18s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.26s | 1.29s | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 140.70ms | 139.25ms | 1.0× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 243.29ms | 372.44ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 18.20ms | 38.97ms | 2.1× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 67.05ms | 143.34ms | 2.1× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 49.43ms | 86.67ms | 1.8× | 2.6% | PASS |
| mem_writes | `oltp_delete_insert` | 55.42ms | 105.43ms | 1.9× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 27.97ms | 62.28ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 38.68ms | 54.48ms | 1.4× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 100.11ms | 147.41ms | 1.5× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 111.42ms | 58.29ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 25.05ms | 16.42ms | 0.7× | 2.2% | PASS |
| file_reads | `oltp_sum_range` | 24.01ms | 16.12ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 4.19ms | 3.49ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 5.30ms | 4.65ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 11.76ms | 8.60ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 31.73ms | 24.59ms | 0.8× | 2.8% | PASS |
| file_reads | `select_random_ranges` | 11.23ms | 7.37ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 12.02ms | 6.90ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 36.02ms | 35.09ms | 1.0× | 1.1% | PASS |
| file_reads | `index_join` | 15.22ms | 10.98ms | 0.7× | 2.1% | PASS |
| file_reads | `index_join_scan` | 4.87ms | 5.97ms | 1.2× | 2.4% | PASS |
| file_reads | `types_table_scan` | 1.11s | 1.18s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.27s | 1.29s | 1.0× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 253.76ms | 170.29ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 269.53ms | 388.12ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.04ms | 45.99ms | 1.8× | 1.3% | PASS |
| file_writes | `oltp_update_index` | 138.85ms | 161.43ms | 1.2× | 5.3% | PASS |
| file_writes | `oltp_update_non_index` | 108.94ms | 101.82ms | 0.9× | 14.3% | PASS |
| file_writes | `oltp_delete_insert` | 97.52ms | 120.01ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 81.65ms | 74.65ms | 0.9× | 8.5% | PASS |
| file_writes | `types_delete_insert` | 73.25ms | 63.88ms | 0.9× | 1.8% | PASS |
| file_writes | `oltp_read_write` | 146.27ms | 159.46ms | 1.1× | 5.2% | PASS |
| ac_reads | `oltp_point_select` | 60.93ms | 58.09ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 20.08ms | 16.38ms | 0.8× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 19.05ms | 16.09ms | 0.8× | 2.7% | PASS |
| ac_reads | `oltp_order_range` | 3.74ms | 3.51ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.81ms | 4.66ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 6.85ms | 8.60ms | 1.3× | 1.1% | PASS |
| ac_reads | `select_random_points` | 25.59ms | 24.61ms | 1.0× | 3.1% | PASS |
| ac_reads | `select_random_ranges` | 6.30ms | 7.39ms | 1.2× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.10ms | 6.93ms | 1.0× | 1.8% | PASS |
| ac_reads | `groupby_scan` | 35.24ms | 35.09ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 12.75ms | 11.09ms | 0.9× | 2.2% | PASS |
| ac_reads | `index_join_scan` | 4.30ms | 5.97ms | 1.4× | 1.3% | PASS |
| ac_reads | `types_table_scan` | 1.11s | 1.18s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.27s | 1.29s | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 179.11ms | 169.57ms | 0.9× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.51ms | 62.42ms | 2.7× | 3.9% | PASS |
| ac_writes | `oltp_insert_ac` | 25.62ms | 74.15ms | 2.9× | 5.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.70ms | 88.08ms | 3.3× | 5.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.02ms | 71.37ms | 3.0× | 7.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.22ms | 80.22ms | 3.2× | 5.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.27ms | 80.22ms | 3.2× | 6.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.46ms | 71.67ms | 3.1× | 4.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.40ms | 84.76ms | 2.7× | 5.3% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.10ms | 31.35ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_range_select` | 13.67ms | 12.31ms | 0.9× | 0.9% | PASS |
| mem_reads | `oltp_sum_range` | 13.62ms | 11.85ms | 0.9× | 0.9% | PASS |
| mem_reads | `oltp_order_range` | 3.00ms | 2.93ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.91ms | 3.97ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 3.73ms | 5.27ms | 1.4× | 0.9% | PASS |
| mem_reads | `select_random_points` | 20.94ms | 19.77ms | 0.9× | 1.0% | PASS |
| mem_reads | `select_random_ranges` | 3.03ms | 4.70ms | 1.6× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 3.55ms | 4.08ms | 1.1× | 1.3% | PASS |
| mem_reads | `groupby_scan` | 32.70ms | 32.94ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 10.47ms | 8.67ms | 0.8× | 1.3% | PASS |
| mem_reads | `index_join_scan` | 3.24ms | 5.27ms | 1.6× | 1.2% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.16s | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.22s | 1.28s | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 122.66ms | 121.86ms | 1.0× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 198.98ms | 292.05ms | 1.5× | 0.6% | PASS |
| mem_writes | `oltp_insert` | 15.86ms | 31.98ms | 2.0× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 57.09ms | 124.10ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 41.77ms | 71.81ms | 1.7× | 0.9% | PASS |
| mem_writes | `oltp_delete_insert` | 46.47ms | 88.72ms | 1.9× | 0.7% | PASS |
| mem_writes | `oltp_write_only` | 23.86ms | 51.31ms | 2.2× | 0.7% | PASS |
| mem_writes | `types_delete_insert` | 32.87ms | 44.22ms | 1.3× | 0.8% | PASS |
| mem_writes | `oltp_read_write` | 81.87ms | 122.85ms | 1.5× | 1.2% | PASS |
| file_reads | `oltp_point_select` | 63.56ms | 38.66ms | 0.6× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 17.14ms | 13.29ms | 0.8× | 0.7% | PASS |
| file_reads | `oltp_sum_range` | 17.04ms | 12.80ms | 0.8× | 0.5% | PASS |
| file_reads | `oltp_order_range` | 3.39ms | 3.06ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.30ms | 4.08ms | 0.9× | 0.7% | PASS |
| file_reads | `oltp_index_scan` | 7.06ms | 6.32ms | 0.9× | 0.9% | PASS |
| file_reads | `select_random_points` | 24.61ms | 20.57ms | 0.8× | 0.9% | PASS |
| file_reads | `select_random_ranges` | 6.29ms | 5.56ms | 0.9× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 6.91ms | 5.09ms | 0.7× | 0.9% | PASS |
| file_reads | `groupby_scan` | 33.08ms | 32.83ms | 1.0× | 0.5% | PASS |
| file_reads | `index_join` | 12.80ms | 9.99ms | 0.8× | 1.6% | PASS |
| file_reads | `index_join_scan` | 3.67ms | 5.52ms | 1.5× | 1.1% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.15s | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 1.23s | 1.28s | 1.0× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 170.54ms | 133.18ms | 0.8× | 0.6% | PASS |
| file_writes | `oltp_bulk_insert` | 259.68ms | 349.51ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_insert` | 29.10ms | 49.23ms | 1.7× | 3.9% | PASS |
| file_writes | `oltp_update_index` | 150.86ms | 199.94ms | 1.3× | 2.5% | PASS |
| file_writes | `oltp_update_non_index` | 127.18ms | 131.18ms | 1.0× | 4.3% | PASS |
| file_writes | `oltp_delete_insert` | 154.05ms | 148.76ms | 1.0× | 3.7% | PASS |
| file_writes | `oltp_write_only` | 109.77ms | 99.51ms | 0.9× | 7.9% | PASS |
| file_writes | `types_delete_insert` | 110.97ms | 88.23ms | 0.8× | 5.8% | PASS |
| file_writes | `oltp_read_write` | 168.50ms | 169.72ms | 1.0× | 3.4% | PASS |
| ac_reads | `oltp_point_select` | 42.55ms | 39.03ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 15.03ms | 13.42ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_sum_range` | 14.89ms | 12.86ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_order_range` | 3.17ms | 3.06ms | 1.0× | 0.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.11ms | 4.09ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_index_scan` | 5.06ms | 6.36ms | 1.3× | 1.1% | PASS |
| ac_reads | `select_random_points` | 22.84ms | 20.73ms | 0.9× | 1.0% | PASS |
| ac_reads | `select_random_ranges` | 4.23ms | 5.55ms | 1.3× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 4.79ms | 5.04ms | 1.1× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 32.74ms | 32.88ms | 1.0× | 0.6% | PASS |
| ac_reads | `index_join` | 11.68ms | 9.74ms | 0.8× | 1.2% | PASS |
| ac_reads | `index_join_scan` | 3.50ms | 5.50ms | 1.6× | 0.9% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.15s | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.23s | 1.28s | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 136.95ms | 132.59ms | 1.0× | 0.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 34.04ms | 74.19ms | 2.2× | 7.0% | PASS |
| ac_writes | `oltp_insert_ac` | 36.14ms | 88.14ms | 2.4× | 5.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 36.24ms | 92.93ms | 2.6× | 5.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 30.66ms | 79.42ms | 2.6× | 4.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 35.10ms | 88.71ms | 2.5× | 5.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 34.80ms | 87.55ms | 2.5× | 6.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 29.96ms | 80.22ms | 2.7× | 6.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 39.37ms | 93.03ms | 2.4× | 4.4% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.03ms | 41.05ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 19.99ms | 21.02ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 18.04ms | 20.56ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 3.51ms | 3.83ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_distinct_range` | 4.67ms | 5.02ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.61ms | 6.25ms | 1.4× | 1.7% | PASS |
| mem_reads | `select_random_points` | 29.17ms | 33.14ms | 1.1× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 7.88ms | 9.01ms | 1.1× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 4.26ms | 4.61ms | 1.1× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 36.76ms | 39.77ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 8.06ms | 11.00ms | 1.4× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 3.98ms | 5.62ms | 1.4× | 2.2% | PASS |
| mem_reads | `types_table_scan` | 1.12s | 1.21s | 1.1× | 1.0% | PASS |
| mem_reads | `table_scan` | 1.34s | 1.30s | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_read_only` | 155.09ms | 173.92ms | 1.1× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 251.05ms | 355.48ms | 1.4× | 0.6% | PASS |
| mem_writes | `oltp_insert` | 19.16ms | 35.40ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 67.41ms | 128.77ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 51.98ms | 85.19ms | 1.6× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 50.58ms | 97.50ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 27.45ms | 58.97ms | 2.1× | 1.6% | PASS |
| mem_writes | `types_delete_insert` | 33.79ms | 54.21ms | 1.6× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 109.64ms | 169.16ms | 1.5× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 109.85ms | 61.31ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 27.68ms | 23.18ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 26.15ms | 22.85ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 4.45ms | 4.13ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.68ms | 5.35ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 12.81ms | 8.71ms | 0.7× | 1.0% | PASS |
| file_reads | `select_random_points` | 38.32ms | 36.44ms | 1.0× | 1.6% | PASS |
| file_reads | `select_random_ranges` | 16.12ms | 11.23ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 12.36ms | 6.89ms | 0.6× | 1.3% | PASS |
| file_reads | `groupby_scan` | 37.72ms | 40.21ms | 1.1× | 0.8% | PASS |
| file_reads | `index_join` | 12.66ms | 12.57ms | 1.0× | 1.6% | PASS |
| file_reads | `index_join_scan` | 5.12ms | 6.05ms | 1.2× | 2.8% | PASS |
| file_reads | `types_table_scan` | 1.15s | 1.21s | 1.1× | 1.6% | PASS |
| file_reads | `table_scan` | 1.31s | 1.30s | 1.0× | 1.5% | PASS |
| file_reads | `oltp_read_only` | 272.02ms | 205.29ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 265.65ms | 371.11ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 26.64ms | 41.28ms | 1.5× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 97.69ms | 141.56ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 79.81ms | 100.28ms | 1.3× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 79.74ms | 110.75ms | 1.4× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 52.92ms | 70.30ms | 1.3× | 2.6% | PASS |
| file_writes | `types_delete_insert` | 51.73ms | 61.69ms | 1.2× | 1.3% | PASS |
| file_writes | `oltp_read_write` | 136.00ms | 178.05ms | 1.3× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 60.12ms | 62.52ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 23.16ms | 23.44ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 20.96ms | 22.91ms | 1.1× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 3.95ms | 4.14ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 5.06ms | 5.33ms | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 7.37ms | 8.49ms | 1.2× | 1.1% | PASS |
| ac_reads | `select_random_points` | 32.22ms | 36.72ms | 1.1× | 1.8% | PASS |
| ac_reads | `select_random_ranges` | 10.62ms | 11.22ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.31ms | 6.86ms | 0.9× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 37.31ms | 40.24ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 9.93ms | 12.50ms | 1.3× | 2.1% | PASS |
| ac_reads | `index_join_scan` | 4.54ms | 6.01ms | 1.3× | 2.2% | PASS |
| ac_reads | `types_table_scan` | 1.16s | 1.22s | 1.0× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.29s | 1.29s | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_read_only` | 191.31ms | 203.29ms | 1.1× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.21ms | 63.51ms | 2.6× | 5.6% | PASS |
| ac_writes | `oltp_insert_ac` | 26.11ms | 82.36ms | 3.2× | 5.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.31ms | 90.85ms | 3.2× | 7.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 25.20ms | 76.06ms | 3.0× | 8.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.20ms | 84.13ms | 3.2× | 7.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.85ms | 83.24ms | 3.1× | 5.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.64ms | 76.86ms | 3.1× | 11.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.62ms | 93.76ms | 2.7× | 6.2% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 11s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 94.92ms | 130.00ms | 73.0% | 0.3% | PASS |
| `status_dirty_many_tables` | 98.47ms | 130.00ms | 75.8% | 0.3% | PASS |
| `diff_regular_working_one_table` | 90.15ms | 120.00ms | 75.1% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 103.49ms | 140.00ms | 73.9% | 0.3% | PASS |
| `diff_stat_working_many_tables` | 103.55ms | 140.00ms | 74.0% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 104.24ms | 140.00ms | 74.5% | 0.4% | PASS |
| `branch_list_many_branches` | 23.06ms | 35.00ms | 65.9% | 1.1% | PASS |
| `branch_create_delete` | 25.31ms | 40.00ms | 63.3% | 1.3% | PASS |
| `checkout_branch_clean` | 60.75ms | 150.00ms | 40.5% | 1.0% | PASS |
| `merge_data_no_conflicts` | 29.09ms | 50.00ms | 58.2% | 1.0% | PASS |
| `merge_schema_no_conflicts` | 22.02ms | 35.00ms | 62.9% | 1.2% | PASS |
| `merge_data_conflicts` | 32.85ms | 180.00ms | 18.3% | 1.1% | PASS |
| `merge_data_conflicts_with_resolve` | 32.59ms | 180.00ms | 18.1% | 0.9% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
