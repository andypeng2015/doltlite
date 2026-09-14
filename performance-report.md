# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-14 17:36 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/34865572975)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.43s | 10.67s | 1.0× | 1.4% | **PASS** |
| Writes | 2.02s | 3.35s | 1.7× | 1.2% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.21s | 10.89s | 1.0× | 1.4% | **PASS** |
| Writes | 3.95s | 4.44s | 1.1× | 3.1% | **PASS** |
| Autocommit writes | 1.76s | 5.72s | 3.3× | 8.5% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.43s | 2.59s | 1.1× | 1.2% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.99s | 2.86s | 1.0× | 1.8% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.67s | 2.73s | 1.0× | 1.2% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.34s | 2.49s | 1.1× | 1.7% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 424.94ms | 716.39ms | 1.7× | 1.0% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 636.85ms | 1.07s | 1.7× | 1.6% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 503.27ms | 830.74ms | 1.7× | 0.8% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 459.41ms | 729.20ms | 1.6× | 1.8% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.67s | 2.65s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.31s | 2.95s | 0.9× | 1.8% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.77s | 2.75s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.46s | 2.55s | 1.0× | 2.0% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 585.10ms | 783.52ms | 1.3× | 1.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 988.89ms | 1.16s | 1.2× | 3.6% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.25s | 1.35s | 1.1× | 2.8% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 1.12s | 1.16s | 1.0× | 17.8% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.51s | 2.65s | 1.1× | 1.1% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 3.12s | 2.94s | 0.9× | 1.5% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.61s | 2.70s | 1.0× | 1.1% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.37s | 2.51s | 1.1× | 2.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 196.23ms | 729.18ms | 3.7× | 5.1% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 234.47ms | 804.95ms | 3.4× | 8.3% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 229.36ms | 872.74ms | 3.8× | 9.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 1.10s | 3.31s | 3.0× | 57.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 22.67ms | 29.23ms | 1.3× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 9.61ms | 11.04ms | 1.1× | 2.4% | PASS |
| mem_reads | `oltp_sum_range` | 8.98ms | 11.02ms | 1.2× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 2.51ms | 2.77ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.57ms | 3.92ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.67ms | 5.04ms | 1.4× | 1.2% | PASS |
| mem_reads | `select_random_points` | 9.23ms | 10.79ms | 1.2× | 2.1% | PASS |
| mem_reads | `select_random_ranges` | 2.85ms | 3.89ms | 1.4× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.15ms | 1.0× | 0.9% | PASS |
| mem_reads | `groupby_scan` | 30.13ms | 32.94ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 5.79ms | 7.68ms | 1.3× | 1.0% | PASS |
| mem_reads | `index_join_scan` | 3.05ms | 4.30ms | 1.4× | 1.3% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.12s | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 1.17s | 1.22s | 1.0× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 100.14ms | 116.67ms | 1.2× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 179.22ms | 269.34ms | 1.5× | 1.5% | PASS |
| mem_writes | `oltp_insert` | 15.04ms | 27.90ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 47.50ms | 93.03ms | 2.0× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 32.57ms | 58.57ms | 1.8× | 0.9% | PASS |
| mem_writes | `oltp_delete_insert` | 42.94ms | 72.34ms | 1.7× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 20.72ms | 46.15ms | 2.2× | 1.1% | PASS |
| mem_writes | `types_delete_insert` | 23.09ms | 37.73ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 63.85ms | 111.32ms | 1.7× | 1.0% | PASS |
| file_reads | `oltp_point_select` | 96.18ms | 48.61ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 17.75ms | 13.21ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 17.13ms | 13.22ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 3.45ms | 3.07ms | 0.9× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.52ms | 4.25ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 11.51ms | 7.32ms | 0.6× | 1.8% | PASS |
| file_reads | `select_random_points` | 17.44ms | 13.00ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 10.31ms | 5.87ms | 0.6× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 11.87ms | 6.32ms | 0.5× | 1.9% | PASS |
| file_reads | `groupby_scan` | 31.34ms | 33.33ms | 1.1× | 0.6% | PASS |
| file_reads | `index_join` | 10.09ms | 9.38ms | 0.9× | 1.6% | PASS |
| file_reads | `index_join_scan` | 4.11ms | 4.75ms | 1.2× | 1.5% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.2% | PASS |
| file_reads | `table_scan` | 1.17s | 1.22s | 1.0× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 207.27ms | 145.72ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 190.85ms | 278.92ms | 1.5× | 0.9% | PASS |
| file_writes | `oltp_insert` | 21.76ms | 31.86ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_update_index` | 74.79ms | 103.95ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 56.51ms | 69.82ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 67.10ms | 81.65ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 45.14ms | 54.30ms | 1.2× | 3.0% | PASS |
| file_writes | `types_delete_insert` | 39.06ms | 43.70ms | 1.1× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 89.89ms | 119.33ms | 1.3× | 1.2% | PASS |
| ac_reads | `oltp_point_select` | 46.75ms | 48.69ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 12.59ms | 13.17ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 11.79ms | 13.26ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_order_range` | 2.89ms | 3.10ms | 1.1× | 1.4% | PASS |
| ac_reads | `oltp_distinct_range` | 3.92ms | 4.23ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.30ms | 7.37ms | 1.2× | 1.1% | PASS |
| ac_reads | `select_random_points` | 12.09ms | 12.99ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_ranges` | 5.32ms | 5.84ms | 1.1× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 6.75ms | 6.25ms | 0.9× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 30.66ms | 33.34ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 7.23ms | 9.38ms | 1.3× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 3.50ms | 4.73ms | 1.3× | 1.7% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.3% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.22s | 1.0× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 136.29ms | 146.37ms | 1.1× | 0.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.75ms | 76.66ms | 3.5× | 3.7% | PASS |
| ac_writes | `oltp_insert_ac` | 24.62ms | 89.65ms | 3.6× | 5.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.72ms | 103.27ms | 3.7× | 5.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.52ms | 86.18ms | 3.8× | 6.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.13ms | 95.45ms | 4.0× | 3.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.41ms | 92.57ms | 3.8× | 5.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.63ms | 85.79ms | 4.0× | 6.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.45ms | 99.59ms | 3.4× | 5.0% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.27ms | 39.94ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 17.62ms | 15.27ms | 0.9× | 2.9% | PASS |
| mem_reads | `oltp_sum_range` | 16.58ms | 14.30ms | 0.9× | 2.2% | PASS |
| mem_reads | `oltp_order_range` | 3.33ms | 3.28ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.43ms | 4.46ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 4.10ms | 6.52ms | 1.6× | 2.1% | PASS |
| mem_reads | `select_random_points` | 23.05ms | 21.81ms | 0.9× | 1.8% | PASS |
| mem_reads | `select_random_ranges` | 3.74ms | 5.42ms | 1.4× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.26ms | 4.91ms | 1.2× | 1.5% | PASS |
| mem_reads | `groupby_scan` | 35.25ms | 34.96ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 10.76ms | 9.99ms | 0.9× | 2.5% | PASS |
| mem_reads | `index_join_scan` | 3.81ms | 5.73ms | 1.5× | 2.2% | PASS |
| mem_reads | `types_table_scan` | 1.26s | 1.22s | 1.0× | 1.5% | PASS |
| mem_reads | `table_scan` | 1.42s | 1.33s | 0.9× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 147.18ms | 144.58ms | 1.0× | 1.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 245.87ms | 383.25ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 18.70ms | 40.47ms | 2.2× | 1.3% | PASS |
| mem_writes | `oltp_update_index` | 72.71ms | 156.54ms | 2.2× | 2.1% | PASS |
| mem_writes | `oltp_update_non_index` | 54.86ms | 93.16ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 60.54ms | 114.94ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 30.66ms | 66.65ms | 2.2× | 1.9% | PASS |
| mem_writes | `types_delete_insert` | 41.55ms | 57.07ms | 1.4× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 111.96ms | 157.28ms | 1.4× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 115.70ms | 60.41ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 25.76ms | 17.49ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 24.74ms | 16.79ms | 0.7× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 4.46ms | 3.70ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 5.60ms | 4.90ms | 0.9× | 2.0% | PASS |
| file_reads | `oltp_index_scan` | 12.08ms | 8.81ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_points` | 34.76ms | 25.66ms | 0.7× | 3.1% | PASS |
| file_reads | `select_random_ranges` | 11.49ms | 7.53ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 12.43ms | 7.06ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 36.83ms | 35.75ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 15.62ms | 11.66ms | 0.7× | 2.3% | PASS |
| file_reads | `index_join_scan` | 4.89ms | 6.23ms | 1.3× | 2.8% | PASS |
| file_reads | `types_table_scan` | 1.30s | 1.23s | 0.9× | 1.0% | PASS |
| file_reads | `table_scan` | 1.44s | 1.33s | 0.9× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 272.10ms | 181.70ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 272.54ms | 396.70ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.58ms | 46.87ms | 1.8× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 135.35ms | 169.18ms | 1.2× | 4.7% | PASS |
| file_writes | `oltp_update_non_index` | 114.52ms | 105.96ms | 0.9× | 12.6% | PASS |
| file_writes | `oltp_delete_insert` | 101.89ms | 125.50ms | 1.2× | 2.5% | PASS |
| file_writes | `oltp_write_only` | 81.69ms | 77.18ms | 0.9× | 12.9% | PASS |
| file_writes | `types_delete_insert` | 77.96ms | 66.94ms | 0.9× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 178.35ms | 168.85ms | 0.9× | 5.3% | PASS |
| ac_reads | `oltp_point_select` | 63.45ms | 59.90ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 20.33ms | 17.31ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 19.49ms | 16.77ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 3.93ms | 3.62ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 4.95ms | 4.85ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 7.07ms | 8.87ms | 1.3× | 1.7% | PASS |
| ac_reads | `select_random_points` | 30.31ms | 26.69ms | 0.9× | 2.4% | PASS |
| ac_reads | `select_random_ranges` | 6.58ms | 7.75ms | 1.2× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 7.56ms | 7.31ms | 1.0× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 36.34ms | 35.95ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 13.63ms | 12.05ms | 0.9× | 2.6% | PASS |
| ac_reads | `index_join_scan` | 4.47ms | 6.39ms | 1.4× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 1.30s | 1.24s | 0.9× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.41s | 1.32s | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_read_only` | 188.65ms | 176.13ms | 0.9× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 26.05ms | 82.39ms | 3.2× | 8.4% | PASS |
| ac_writes | `oltp_insert_ac` | 29.60ms | 100.66ms | 3.4× | 9.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 31.59ms | 112.23ms | 3.6× | 9.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 27.01ms | 93.93ms | 3.5× | 8.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.63ms | 104.84ms | 3.7× | 6.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.87ms | 104.30ms | 3.6× | 6.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 26.40ms | 97.00ms | 3.7× | 8.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 36.32ms | 109.60ms | 3.0× | 6.2% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.87ms | 31.85ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 13.84ms | 12.36ms | 0.9× | 1.0% | PASS |
| mem_reads | `oltp_sum_range` | 13.85ms | 11.92ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_order_range` | 2.99ms | 2.92ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.88ms | 3.98ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.72ms | 5.43ms | 1.5× | 1.5% | PASS |
| mem_reads | `select_random_points` | 21.26ms | 19.80ms | 0.9× | 0.9% | PASS |
| mem_reads | `select_random_ranges` | 3.02ms | 4.69ms | 1.6× | 1.7% | PASS |
| mem_reads | `covering_index_scan` | 3.54ms | 4.12ms | 1.2× | 1.1% | PASS |
| mem_reads | `groupby_scan` | 32.49ms | 32.73ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 10.46ms | 8.76ms | 0.8× | 1.6% | PASS |
| mem_reads | `index_join_scan` | 3.21ms | 5.31ms | 1.7× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 1.12s | 1.18s | 1.1× | 1.3% | PASS |
| mem_reads | `table_scan` | 1.29s | 1.28s | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 121.19ms | 120.98ms | 1.0× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 197.30ms | 290.68ms | 1.5× | 0.4% | PASS |
| mem_writes | `oltp_insert` | 15.76ms | 31.91ms | 2.0× | 0.5% | PASS |
| mem_writes | `oltp_update_index` | 57.66ms | 125.52ms | 2.2× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 42.43ms | 72.05ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 46.96ms | 89.77ms | 1.9× | 0.6% | PASS |
| mem_writes | `oltp_write_only` | 24.07ms | 51.84ms | 2.2× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 33.47ms | 44.77ms | 1.3× | 0.8% | PASS |
| mem_writes | `oltp_read_write` | 85.62ms | 124.22ms | 1.5× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 66.33ms | 40.08ms | 0.6× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 17.40ms | 13.37ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_sum_range` | 17.10ms | 12.83ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.34ms | 3.02ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 4.28ms | 4.04ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 7.21ms | 6.37ms | 0.9× | 1.3% | PASS |
| file_reads | `select_random_points` | 25.04ms | 20.67ms | 0.8× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 6.40ms | 5.63ms | 0.9× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 7.09ms | 5.14ms | 0.7× | 1.2% | PASS |
| file_reads | `groupby_scan` | 33.24ms | 32.92ms | 1.0× | 0.6% | PASS |
| file_reads | `index_join` | 13.09ms | 10.02ms | 0.8× | 1.3% | PASS |
| file_reads | `index_join_scan` | 3.72ms | 5.62ms | 1.5× | 1.4% | PASS |
| file_reads | `types_table_scan` | 1.11s | 1.17s | 1.1× | 1.2% | PASS |
| file_reads | `table_scan` | 1.29s | 1.28s | 1.0× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 169.72ms | 132.96ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 272.57ms | 374.88ms | 1.4× | 3.2% | PASS |
| file_writes | `oltp_insert` | 32.28ms | 62.13ms | 1.9× | 5.5% | PASS |
| file_writes | `oltp_update_index` | 175.63ms | 214.06ms | 1.2× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 147.92ms | 138.92ms | 0.9× | 1.5% | PASS |
| file_writes | `oltp_delete_insert` | 175.93ms | 163.90ms | 0.9× | 2.4% | PASS |
| file_writes | `oltp_write_only` | 125.80ms | 113.12ms | 0.9× | 1.3% | PASS |
| file_writes | `types_delete_insert` | 133.80ms | 92.26ms | 0.7× | 4.3% | PASS |
| file_writes | `oltp_read_write` | 190.38ms | 186.75ms | 1.0× | 5.3% | PASS |
| ac_reads | `oltp_point_select` | 43.16ms | 40.19ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 15.17ms | 13.47ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 15.03ms | 13.09ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_order_range` | 3.17ms | 3.05ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 4.07ms | 4.09ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 5.10ms | 6.49ms | 1.3× | 1.3% | PASS |
| ac_reads | `select_random_points` | 23.11ms | 20.94ms | 0.9× | 1.1% | PASS |
| ac_reads | `select_random_ranges` | 4.21ms | 5.66ms | 1.3× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 4.78ms | 5.17ms | 1.1× | 0.6% | PASS |
| ac_reads | `groupby_scan` | 32.73ms | 32.79ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 11.67ms | 9.78ms | 0.8× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.51ms | 5.57ms | 1.6× | 1.3% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.15s | 1.1× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.25s | 1.27s | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_read_only` | 137.13ms | 132.92ms | 1.0× | 0.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 26.89ms | 97.67ms | 3.6× | 7.8% | PASS |
| ac_writes | `oltp_insert_ac` | 28.37ms | 105.97ms | 3.7× | 6.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.12ms | 118.39ms | 4.1× | 11.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 25.95ms | 103.03ms | 4.0× | 9.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.52ms | 109.53ms | 4.0× | 8.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.99ms | 102.54ms | 3.8× | 8.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 28.26ms | 112.17ms | 4.0× | 17.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 36.25ms | 123.42ms | 3.4× | 10.5% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.48ms | 31.54ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 16.45ms | 17.66ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_sum_range` | 15.45ms | 17.03ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 3.21ms | 3.36ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 4.11ms | 4.38ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.95ms | 5.00ms | 1.3× | 2.3% | PASS |
| mem_reads | `select_random_points` | 25.65ms | 28.76ms | 1.1× | 2.0% | PASS |
| mem_reads | `select_random_ranges` | 6.26ms | 7.27ms | 1.2× | 2.1% | PASS |
| mem_reads | `covering_index_scan` | 3.34ms | 3.49ms | 1.0× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 33.23ms | 36.04ms | 1.1× | 1.4% | PASS |
| mem_reads | `index_join` | 6.84ms | 9.12ms | 1.3× | 1.8% | PASS |
| mem_reads | `index_join_scan` | 3.39ms | 5.05ms | 1.5× | 1.5% | PASS |
| mem_reads | `types_table_scan` | 994.23ms | 1.07s | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.07s | 1.11s | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_read_only` | 121.55ms | 136.34ms | 1.1× | 2.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 186.02ms | 255.36ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 15.26ms | 26.55ms | 1.7× | 3.4% | PASS |
| mem_writes | `oltp_update_index` | 54.53ms | 103.11ms | 1.9× | 1.7% | PASS |
| mem_writes | `oltp_update_non_index` | 40.27ms | 65.82ms | 1.6× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 39.24ms | 73.60ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 21.10ms | 43.63ms | 2.1× | 2.3% | PASS |
| mem_writes | `types_delete_insert` | 24.83ms | 40.08ms | 1.6× | 2.0% | PASS |
| mem_writes | `oltp_read_write` | 78.15ms | 121.05ms | 1.5× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 55.75ms | 37.53ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 19.74ms | 18.79ms | 1.0× | 2.2% | PASS |
| file_reads | `oltp_sum_range` | 19.58ms | 18.69ms | 1.0× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 3.73ms | 3.60ms | 1.0× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 4.65ms | 4.57ms | 1.0× | 2.0% | PASS |
| file_reads | `oltp_index_scan` | 7.16ms | 6.03ms | 0.8× | 2.0% | PASS |
| file_reads | `select_random_points` | 28.60ms | 29.36ms | 1.0× | 1.4% | PASS |
| file_reads | `select_random_ranges` | 9.44ms | 8.01ms | 0.8× | 2.3% | PASS |
| file_reads | `covering_index_scan` | 6.63ms | 4.58ms | 0.7× | 2.3% | PASS |
| file_reads | `groupby_scan` | 33.89ms | 35.56ms | 1.0× | 1.1% | PASS |
| file_reads | `index_join` | 8.64ms | 10.08ms | 1.2× | 2.3% | PASS |
| file_reads | `index_join_scan` | 3.67ms | 4.95ms | 1.3× | 3.0% | PASS |
| file_reads | `types_table_scan` | 975.23ms | 1.06s | 1.1× | 1.1% | PASS |
| file_reads | `table_scan` | 1.10s | 1.15s | 1.0× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 179.34ms | 158.11ms | 0.9× | 1.7% | PASS |
| file_writes | `oltp_bulk_insert` | 277.58ms | 344.57ms | 1.2× | 13.8% | PASS |
| file_writes | `oltp_insert` | 39.04ms | 44.09ms | 1.1× | 33.9% | PASS |
| file_writes | `oltp_update_index` | 155.21ms | 171.81ms | 1.1× | 12.8% | PASS |
| file_writes | `oltp_update_non_index` | 149.36ms | 119.61ms | 0.8× | 17.0% | PASS |
| file_writes | `oltp_delete_insert` | 149.95ms | 128.79ms | 0.9× | 16.9% | PASS |
| file_writes | `oltp_write_only` | 90.33ms | 92.68ms | 1.0× | 33.8% | PASS |
| file_writes | `types_delete_insert` | 78.59ms | 79.98ms | 1.0× | 33.4% | PASS |
| file_writes | `oltp_read_write` | 179.53ms | 175.90ms | 1.0× | 18.5% | PASS |
| ac_reads | `oltp_point_select` | 37.83ms | 39.73ms | 1.1× | 1.9% | PASS |
| ac_reads | `oltp_range_select` | 17.76ms | 19.04ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 16.66ms | 17.81ms | 1.1× | 2.5% | PASS |
| ac_reads | `oltp_order_range` | 3.48ms | 3.52ms | 1.0× | 2.7% | PASS |
| ac_reads | `oltp_distinct_range` | 4.45ms | 4.51ms | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_index_scan` | 5.25ms | 6.01ms | 1.1× | 3.1% | PASS |
| ac_reads | `select_random_points` | 26.21ms | 28.64ms | 1.1× | 2.4% | PASS |
| ac_reads | `select_random_ranges` | 7.45ms | 8.02ms | 1.1× | 3.7% | PASS |
| ac_reads | `covering_index_scan` | 4.41ms | 4.42ms | 1.0× | 3.2% | PASS |
| ac_reads | `groupby_scan` | 31.88ms | 33.92ms | 1.1× | 2.3% | PASS |
| ac_reads | `index_join` | 7.81ms | 10.31ms | 1.3× | 2.3% | PASS |
| ac_reads | `index_join_scan` | 3.59ms | 5.19ms | 1.4× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 956.10ms | 1.04s | 1.1× | 1.4% | PASS |
| ac_reads | `table_scan` | 1.11s | 1.14s | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 141.45ms | 150.90ms | 1.1× | 1.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 101.91ms | 350.17ms | 3.4× | 63.4% | PASS |
| ac_writes | `oltp_insert_ac` | 126.03ms | 420.97ms | 3.3× | 42.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 196.70ms | 521.33ms | 2.7× | 58.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 117.92ms | 305.61ms | 2.6× | 44.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 139.75ms | 343.82ms | 2.5× | 62.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 189.52ms | 594.48ms | 3.1× | 55.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 91.09ms | 378.83ms | 4.2× | 60.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 136.90ms | 399.50ms | 2.9× | 56.3% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 6s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 70.71ms | 130.00ms | 54.4% | 1.5% | PASS |
| `status_dirty_many_tables` | 73.49ms | 130.00ms | 56.5% | 1.6% | PASS |
| `diff_regular_working_one_table` | 65.94ms | 120.00ms | 54.9% | 3.3% | PASS |
| `diff_regular_working_many_tables` | 79.82ms | 140.00ms | 57.0% | 2.9% | PASS |
| `diff_stat_working_many_tables` | 74.92ms | 140.00ms | 53.5% | 1.9% | PASS |
| `diff_schema_working_many_tables` | 76.20ms | 140.00ms | 54.4% | 1.7% | PASS |
| `branch_list_many_branches` | 18.50ms | 35.00ms | 52.9% | 3.9% | PASS |
| `branch_create_delete` | 22.77ms | 40.00ms | 56.9% | 11.1% | PASS |
| `checkout_branch_clean` | 114.98ms | 150.00ms | 76.7% | 18.6% | PASS |
| `merge_data_no_conflicts` | 49.09ms | 50.00ms | 98.2% | 40.2% | PASS |
| `merge_schema_no_conflicts` | 20.21ms | 35.00ms | 57.7% | 5.7% | PASS |
| `merge_data_conflicts` | 25.49ms | 180.00ms | 14.2% | 3.4% | PASS |
| `merge_data_conflicts_with_resolve` | 25.40ms | 180.00ms | 14.1% | 3.0% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
