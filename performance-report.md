# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-24 15:54 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260920.314.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/36011796740)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.90s | 10.12s | 1.0× | 1.5% | **PASS** |
| Writes | 2.01s | 3.29s | 1.6× | 1.5% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.83s | 10.34s | 1.0× | 1.4% | **PASS** |
| Writes | 3.27s | 3.88s | 1.2× | 2.8% | **PASS** |
| Autocommit writes | 799.63ms | 2.41s | 3.0× | 5.9% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.61s | 2.74s | 1.1× | 1.1% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.79s | 2.80s | 1.0× | 1.7% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.80s | 2.84s | 1.0× | 1.8% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 1.71s | 1.74s | 1.0× | 1.7% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 443.96ms | 720.33ms | 1.6× | 1.2% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 616.87ms | 1.03s | 1.7× | 1.6% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 611.64ms | 1.02s | 1.7× | 1.6% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 337.37ms | 526.66ms | 1.6× | 1.8% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.99s | 2.84s | 0.9× | 1.1% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.95s | 2.84s | 1.0× | 1.3% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.08s | 2.91s | 0.9× | 1.6% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 1.80s | 1.75s | 1.0× | 1.5% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 594.61ms | 777.76ms | 1.3× | 1.6% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 942.45ms | 1.12s | 1.2× | 3.0% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 855.92ms | 1.12s | 1.3× | 2.9% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 875.00ms | 865.61ms | 1.0× | 5.7% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.93s | 2.89s | 1.0× | 1.2% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.78s | 2.83s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.92s | 2.91s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 1.71s | 1.78s | 1.0× | 2.2% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 145.81ms | 499.04ms | 3.4× | 5.1% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 209.15ms | 616.19ms | 2.9× | 4.9% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 238.71ms | 746.86ms | 3.1× | 11.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 205.97ms | 547.57ms | 2.7× | 6.0% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 25.07ms | 28.62ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 10.85ms | 11.21ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_sum_range` | 9.54ms | 10.88ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 2.66ms | 2.84ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.71ms | 3.97ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.88ms | 4.75ms | 1.2× | 1.0% | PASS |
| mem_reads | `select_random_points` | 10.08ms | 10.68ms | 1.1× | 1.2% | PASS |
| mem_reads | `select_random_ranges` | 3.00ms | 3.98ms | 1.3× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 4.27ms | 4.14ms | 1.0× | 1.2% | PASS |
| mem_reads | `groupby_scan` | 32.22ms | 35.10ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 5.71ms | 7.53ms | 1.3× | 1.0% | PASS |
| mem_reads | `index_join_scan` | 3.26ms | 4.55ms | 1.4× | 1.2% | PASS |
| mem_reads | `types_table_scan` | 1.12s | 1.19s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.27s | 1.30s | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 102.71ms | 114.45ms | 1.1× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 182.57ms | 263.80ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 15.66ms | 28.29ms | 1.8× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 50.01ms | 96.01ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 35.41ms | 59.35ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 44.16ms | 73.53ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 21.89ms | 48.41ms | 2.2× | 1.0% | PASS |
| mem_writes | `types_delete_insert` | 24.38ms | 38.02ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 69.89ms | 112.93ms | 1.6× | 2.9% | PASS |
| file_reads | `oltp_point_select` | 120.05ms | 51.71ms | 0.4× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 20.58ms | 13.64ms | 0.7× | 3.4% | PASS |
| file_reads | `oltp_sum_range` | 19.10ms | 13.36ms | 0.7× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 3.55ms | 3.16ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 4.62ms | 4.25ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 13.46ms | 7.54ms | 0.6× | 1.2% | PASS |
| file_reads | `select_random_points` | 19.65ms | 13.62ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_ranges` | 12.57ms | 6.37ms | 0.5× | 0.5% | PASS |
| file_reads | `covering_index_scan` | 14.16ms | 6.90ms | 0.5× | 1.1% | PASS |
| file_reads | `groupby_scan` | 33.53ms | 35.57ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 11.19ms | 9.67ms | 0.9× | 1.0% | PASS |
| file_reads | `index_join_scan` | 4.37ms | 4.92ms | 1.1× | 1.3% | PASS |
| file_reads | `types_table_scan` | 1.14s | 1.20s | 1.1× | 1.1% | PASS |
| file_reads | `table_scan` | 1.33s | 1.32s | 1.0× | 1.0% | PASS |
| file_reads | `oltp_read_only` | 244.76ms | 149.46ms | 0.6× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 194.33ms | 272.97ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 22.02ms | 32.08ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 77.80ms | 104.38ms | 1.3× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 59.81ms | 70.74ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 68.85ms | 82.17ms | 1.2× | 2.4% | PASS |
| file_writes | `oltp_write_only` | 44.04ms | 55.53ms | 1.3× | 1.6% | PASS |
| file_writes | `types_delete_insert` | 40.97ms | 44.08ms | 1.1× | 1.8% | PASS |
| file_writes | `oltp_read_write` | 86.80ms | 115.80ms | 1.3× | 1.1% | PASS |
| ac_reads | `oltp_point_select` | 55.68ms | 51.51ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 14.33ms | 13.56ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 13.13ms | 13.40ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 3.14ms | 3.17ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 4.14ms | 4.26ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_index_scan` | 7.49ms | 7.61ms | 1.0× | 1.0% | PASS |
| ac_reads | `select_random_points` | 14.38ms | 13.84ms | 1.0× | 2.6% | PASS |
| ac_reads | `select_random_ranges` | 6.39ms | 6.43ms | 1.0× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 7.95ms | 6.94ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 32.97ms | 35.57ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 8.00ms | 9.56ms | 1.2× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 3.87ms | 5.01ms | 1.3× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 1.20s | 1.22s | 1.0× | 1.2% | PASS |
| ac_reads | `table_scan` | 1.41s | 1.35s | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 152.74ms | 149.91ms | 1.0× | 2.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 15.97ms | 47.56ms | 3.0× | 5.4% | PASS |
| ac_writes | `oltp_insert_ac` | 17.75ms | 60.91ms | 3.4× | 4.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.36ms | 74.45ms | 3.7× | 5.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.23ms | 55.86ms | 3.4× | 6.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 18.12ms | 66.97ms | 3.7× | 4.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 17.69ms | 65.20ms | 3.7× | 3.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 16.28ms | 55.64ms | 3.4× | 7.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 23.41ms | 72.44ms | 3.1× | 3.6% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 39.05ms | 39.91ms | 1.0× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 18.43ms | 14.25ms | 0.8× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 16.18ms | 13.87ms | 0.9× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 3.41ms | 3.15ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.53ms | 4.27ms | 0.9× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 4.08ms | 6.41ms | 1.6× | 2.3% | PASS |
| mem_reads | `select_random_points` | 24.41ms | 21.49ms | 0.9× | 2.5% | PASS |
| mem_reads | `select_random_ranges` | 3.75ms | 5.38ms | 1.4× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.27ms | 4.86ms | 1.1× | 1.5% | PASS |
| mem_reads | `groupby_scan` | 35.75ms | 35.07ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 11.55ms | 9.96ms | 0.9× | 3.9% | PASS |
| mem_reads | `index_join_scan` | 4.02ms | 5.55ms | 1.4× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 1.18s | 1.20s | 1.0× | 2.4% | PASS |
| mem_reads | `table_scan` | 1.29s | 1.30s | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_read_only` | 146.90ms | 137.64ms | 0.9× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 246.30ms | 374.49ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 18.44ms | 39.17ms | 2.1× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 68.30ms | 145.94ms | 2.1× | 2.0% | PASS |
| mem_writes | `oltp_update_non_index` | 51.21ms | 87.82ms | 1.7× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 58.14ms | 109.28ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 29.84ms | 63.90ms | 2.1× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 41.73ms | 55.96ms | 1.3× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 102.93ms | 149.21ms | 1.4× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 114.19ms | 58.83ms | 0.5× | 1.4% | PASS |
| file_reads | `oltp_range_select` | 26.50ms | 16.36ms | 0.6× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 24.90ms | 16.27ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 4.40ms | 3.45ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 5.49ms | 4.60ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 11.99ms | 8.55ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_points` | 32.86ms | 24.98ms | 0.8× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 11.31ms | 7.46ms | 0.7× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.87ms | 7.00ms | 0.6× | 1.8% | PASS |
| file_reads | `groupby_scan` | 36.20ms | 35.23ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 15.57ms | 11.09ms | 0.7× | 1.8% | PASS |
| file_reads | `index_join_scan` | 4.88ms | 6.17ms | 1.3× | 3.2% | PASS |
| file_reads | `types_table_scan` | 1.12s | 1.19s | 1.1× | 1.2% | PASS |
| file_reads | `table_scan` | 1.27s | 1.29s | 1.0× | 1.1% | PASS |
| file_reads | `oltp_read_only` | 260.26ms | 169.12ms | 0.6× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 271.65ms | 387.96ms | 1.4× | 1.2% | PASS |
| file_writes | `oltp_insert` | 26.28ms | 46.18ms | 1.8× | 2.4% | PASS |
| file_writes | `oltp_update_index` | 138.44ms | 162.34ms | 1.2× | 3.7% | PASS |
| file_writes | `oltp_update_non_index` | 102.98ms | 102.19ms | 1.0× | 11.0% | PASS |
| file_writes | `oltp_delete_insert` | 97.48ms | 121.27ms | 1.2× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 83.75ms | 75.04ms | 0.9× | 10.7% | PASS |
| file_writes | `types_delete_insert` | 73.28ms | 64.71ms | 0.9× | 1.8% | PASS |
| file_writes | `oltp_read_write` | 148.60ms | 159.98ms | 1.1× | 5.8% | PASS |
| ac_reads | `oltp_point_select` | 62.10ms | 58.62ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 21.04ms | 16.31ms | 0.8× | 1.5% | PASS |
| ac_reads | `oltp_sum_range` | 19.11ms | 16.09ms | 0.8× | 0.8% | PASS |
| ac_reads | `oltp_order_range` | 3.85ms | 3.45ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 4.94ms | 4.59ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.87ms | 8.54ms | 1.2× | 1.6% | PASS |
| ac_reads | `select_random_points` | 27.18ms | 24.63ms | 0.9× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 6.32ms | 7.43ms | 1.2× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 7.11ms | 6.99ms | 1.0× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 35.55ms | 35.10ms | 1.0× | 0.6% | PASS |
| ac_reads | `index_join` | 12.93ms | 11.10ms | 0.9× | 2.4% | PASS |
| ac_reads | `index_join_scan` | 4.35ms | 5.88ms | 1.4× | 2.7% | PASS |
| ac_reads | `types_table_scan` | 1.11s | 1.18s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.27s | 1.29s | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 185.83ms | 168.91ms | 0.9× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.73ms | 62.86ms | 2.6× | 5.4% | PASS |
| ac_writes | `oltp_insert_ac` | 26.83ms | 75.86ms | 2.8× | 4.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.11ms | 87.45ms | 3.2× | 3.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.20ms | 71.06ms | 3.1× | 6.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.88ms | 80.03ms | 3.1× | 4.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.71ms | 79.70ms | 3.1× | 4.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.20ms | 72.43ms | 3.0× | 6.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.49ms | 86.77ms | 2.7× | 5.3% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.37ms | 38.81ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 16.84ms | 14.28ms | 0.8× | 2.8% | PASS |
| mem_reads | `oltp_sum_range` | 15.88ms | 13.83ms | 0.9× | 2.1% | PASS |
| mem_reads | `oltp_order_range` | 3.29ms | 3.14ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_distinct_range` | 4.35ms | 4.31ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.08ms | 6.39ms | 1.6× | 1.8% | PASS |
| mem_reads | `select_random_points` | 23.51ms | 21.84ms | 0.9× | 3.4% | PASS |
| mem_reads | `select_random_ranges` | 3.58ms | 5.32ms | 1.5× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.24ms | 4.86ms | 1.1× | 2.3% | PASS |
| mem_reads | `groupby_scan` | 35.06ms | 35.19ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 10.75ms | 9.93ms | 0.9× | 3.5% | PASS |
| mem_reads | `index_join_scan` | 3.80ms | 5.56ms | 1.5× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 1.17s | 1.21s | 1.0× | 1.5% | PASS |
| mem_reads | `table_scan` | 1.32s | 1.32s | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 147.54ms | 141.34ms | 1.0× | 1.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 241.99ms | 370.32ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 18.72ms | 39.33ms | 2.1× | 1.5% | PASS |
| mem_writes | `oltp_update_index` | 67.95ms | 145.98ms | 2.1× | 2.3% | PASS |
| mem_writes | `oltp_update_non_index` | 50.73ms | 86.44ms | 1.7× | 3.0% | PASS |
| mem_writes | `oltp_delete_insert` | 54.72ms | 107.44ms | 2.0× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 29.20ms | 64.00ms | 2.2× | 1.7% | PASS |
| mem_writes | `types_delete_insert` | 39.47ms | 54.61ms | 1.4× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 108.86ms | 152.33ms | 1.4× | 2.4% | PASS |
| file_reads | `oltp_point_select` | 113.45ms | 59.00ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 25.59ms | 16.77ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 24.37ms | 16.14ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_order_range` | 4.53ms | 3.48ms | 0.8× | 2.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.53ms | 4.69ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 11.93ms | 8.60ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 33.03ms | 25.03ms | 0.8× | 3.0% | PASS |
| file_reads | `select_random_ranges` | 11.24ms | 7.44ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 12.21ms | 6.93ms | 0.6× | 1.0% | PASS |
| file_reads | `groupby_scan` | 36.48ms | 35.76ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 15.54ms | 11.34ms | 0.7× | 1.9% | PASS |
| file_reads | `index_join_scan` | 4.93ms | 6.04ms | 1.2× | 2.4% | PASS |
| file_reads | `types_table_scan` | 1.18s | 1.22s | 1.0× | 1.6% | PASS |
| file_reads | `table_scan` | 1.33s | 1.32s | 1.0× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 265.10ms | 172.69ms | 0.7× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 265.37ms | 382.55ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_insert` | 26.43ms | 46.27ms | 1.8× | 2.8% | PASS |
| file_writes | `oltp_update_index` | 104.10ms | 163.96ms | 1.6× | 2.9% | PASS |
| file_writes | `oltp_update_non_index` | 95.05ms | 100.68ms | 1.1× | 9.8% | PASS |
| file_writes | `oltp_delete_insert` | 93.76ms | 121.55ms | 1.3× | 2.7% | PASS |
| file_writes | `oltp_write_only` | 61.41ms | 74.64ms | 1.2× | 2.8% | PASS |
| file_writes | `types_delete_insert` | 68.95ms | 65.12ms | 0.9× | 2.9% | PASS |
| file_writes | `oltp_read_write` | 140.85ms | 163.58ms | 1.2× | 2.9% | PASS |
| ac_reads | `oltp_point_select` | 62.91ms | 59.35ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 20.53ms | 16.79ms | 0.8× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 18.78ms | 16.32ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_order_range` | 3.96ms | 3.49ms | 0.9× | 2.2% | PASS |
| ac_reads | `oltp_distinct_range` | 5.02ms | 4.70ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.87ms | 8.58ms | 1.2× | 1.1% | PASS |
| ac_reads | `select_random_points` | 27.16ms | 24.98ms | 0.9× | 2.9% | PASS |
| ac_reads | `select_random_ranges` | 6.21ms | 7.37ms | 1.2× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 7.19ms | 6.92ms | 1.0× | 1.0% | PASS |
| ac_reads | `groupby_scan` | 35.66ms | 35.64ms | 1.0× | 0.6% | PASS |
| ac_reads | `index_join` | 13.05ms | 11.37ms | 0.9× | 2.2% | PASS |
| ac_reads | `index_join_scan` | 4.36ms | 6.03ms | 1.4× | 1.8% | PASS |
| ac_reads | `types_table_scan` | 1.18s | 1.21s | 1.0× | 1.4% | PASS |
| ac_reads | `table_scan` | 1.33s | 1.32s | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 189.97ms | 173.41ms | 0.9× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 26.28ms | 72.14ms | 2.7× | 10.5% | PASS |
| ac_writes | `oltp_insert_ac` | 29.57ms | 95.82ms | 3.2× | 6.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 31.94ms | 104.13ms | 3.3× | 11.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 25.47ms | 82.32ms | 3.2× | 8.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 30.68ms | 102.04ms | 3.3× | 12.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 29.92ms | 98.27ms | 3.3× | 12.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 27.29ms | 89.97ms | 3.3× | 10.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 37.57ms | 102.18ms | 2.7× | 11.7% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 21.59ms | 23.57ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 11.77ms | 11.45ms | 1.0× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 10.27ms | 10.88ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 2.22ms | 2.20ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 2.70ms | 2.69ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_index_scan` | 2.98ms | 4.03ms | 1.4× | 1.7% | PASS |
| mem_reads | `select_random_points` | 18.49ms | 19.29ms | 1.0× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 4.57ms | 5.01ms | 1.1× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 2.33ms | 2.83ms | 1.2× | 1.5% | PASS |
| mem_reads | `groupby_scan` | 20.46ms | 21.35ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 4.79ms | 6.75ms | 1.4× | 3.7% | PASS |
| mem_reads | `index_join_scan` | 2.85ms | 4.47ms | 1.6× | 2.8% | PASS |
| mem_reads | `types_table_scan` | 748.40ms | 738.62ms | 1.0× | 3.6% | PASS |
| mem_reads | `table_scan` | 782.96ms | 805.64ms | 1.0× | 2.1% | PASS |
| mem_reads | `oltp_read_only` | 73.94ms | 82.71ms | 1.1× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 142.03ms | 188.92ms | 1.3× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 10.81ms | 19.30ms | 1.8× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 39.47ms | 71.93ms | 1.8× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 30.16ms | 47.69ms | 1.6× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 27.91ms | 53.50ms | 1.9× | 1.7% | PASS |
| mem_writes | `oltp_write_only` | 15.20ms | 33.24ms | 2.2× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 18.97ms | 29.33ms | 1.5× | 2.3% | PASS |
| mem_writes | `oltp_read_write` | 52.82ms | 82.74ms | 1.6× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 75.03ms | 37.16ms | 0.5× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 17.27ms | 12.69ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 16.01ms | 12.48ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 2.81ms | 2.38ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 3.32ms | 2.87ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 9.03ms | 5.70ms | 0.6× | 1.9% | PASS |
| file_reads | `select_random_points` | 23.15ms | 19.97ms | 0.9× | 2.0% | PASS |
| file_reads | `select_random_ranges` | 9.94ms | 6.38ms | 0.6× | 0.5% | PASS |
| file_reads | `covering_index_scan` | 8.26ms | 3.91ms | 0.5× | 1.5% | PASS |
| file_reads | `groupby_scan` | 20.34ms | 21.00ms | 1.0× | 1.2% | PASS |
| file_reads | `index_join` | 7.95ms | 7.05ms | 0.9× | 2.5% | PASS |
| file_reads | `index_join_scan` | 3.28ms | 4.08ms | 1.2× | 4.0% | PASS |
| file_reads | `types_table_scan` | 676.31ms | 711.99ms | 1.1× | 1.0% | PASS |
| file_reads | `table_scan` | 774.40ms | 798.14ms | 1.0× | 1.5% | PASS |
| file_reads | `oltp_read_only` | 154.66ms | 104.38ms | 0.7× | 1.4% | PASS |
| file_writes | `oltp_bulk_insert` | 196.13ms | 240.50ms | 1.2× | 5.5% | PASS |
| file_writes | `oltp_insert` | 19.62ms | 29.36ms | 1.5× | 8.0% | PASS |
| file_writes | `oltp_update_index` | 140.81ms | 129.18ms | 0.9× | 1.9% | PASS |
| file_writes | `oltp_update_non_index` | 121.04ms | 96.94ms | 0.8× | 6.3% | PASS |
| file_writes | `oltp_delete_insert` | 120.45ms | 99.68ms | 0.8× | 3.5% | PASS |
| file_writes | `oltp_write_only` | 82.44ms | 78.91ms | 1.0× | 5.9% | PASS |
| file_writes | `types_delete_insert` | 73.48ms | 62.16ms | 0.8× | 6.3% | PASS |
| file_writes | `oltp_read_write` | 121.03ms | 128.87ms | 1.1× | 3.6% | PASS |
| ac_reads | `oltp_point_select` | 37.93ms | 37.60ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 12.66ms | 12.57ms | 1.0× | 3.3% | PASS |
| ac_reads | `oltp_sum_range` | 12.03ms | 12.41ms | 1.0× | 2.7% | PASS |
| ac_reads | `oltp_order_range` | 2.52ms | 2.38ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 3.01ms | 2.86ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_index_scan` | 4.89ms | 5.36ms | 1.1× | 3.9% | PASS |
| ac_reads | `select_random_points` | 19.57ms | 20.18ms | 1.0× | 3.8% | PASS |
| ac_reads | `select_random_ranges` | 6.43ms | 6.53ms | 1.0× | 2.2% | PASS |
| ac_reads | `covering_index_scan` | 4.33ms | 4.07ms | 0.9× | 2.9% | PASS |
| ac_reads | `groupby_scan` | 20.32ms | 21.38ms | 1.1× | 1.9% | PASS |
| ac_reads | `index_join` | 6.01ms | 7.08ms | 1.2× | 3.4% | PASS |
| ac_reads | `index_join_scan` | 2.85ms | 4.14ms | 1.5× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 685.89ms | 720.69ms | 1.1× | 1.3% | PASS |
| ac_reads | `table_scan` | 786.10ms | 810.61ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 105.91ms | 107.76ms | 1.0× | 1.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.63ms | 57.03ms | 2.5× | 4.9% | PASS |
| ac_writes | `oltp_insert_ac` | 26.41ms | 68.59ms | 2.6× | 4.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.07ms | 75.77ms | 2.7× | 6.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.95ms | 64.81ms | 3.0× | 5.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.56ms | 71.27ms | 2.8× | 5.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.22ms | 72.31ms | 2.8× | 7.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 26.71ms | 63.50ms | 2.4× | 10.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.42ms | 74.29ms | 2.6× | 6.1% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 13s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 71.11ms | 218.40ms | 32.6% | 0.2% | PASS |
| `status_dirty_many_tables` | 73.57ms | 218.40ms | 33.7% | 0.2% | PASS |
| `diff_regular_working_one_table` | 66.93ms | 201.60ms | 33.2% | 0.2% | PASS |
| `diff_regular_working_many_tables` | 77.56ms | 235.20ms | 33.0% | 0.2% | PASS |
| `diff_stat_working_many_tables` | 77.41ms | 235.20ms | 32.9% | 0.2% | PASS |
| `diff_schema_working_many_tables` | 78.26ms | 235.20ms | 33.3% | 0.3% | PASS |
| `branch_list_many_branches` | 18.17ms | 58.80ms | 30.9% | 0.8% | PASS |
| `branch_create_delete` | 27.45ms | 67.20ms | 40.9% | 13.1% | PASS |
| `checkout_branch_clean` | 117.92ms | 252.00ms | 46.8% | 15.3% | PASS |
| `merge_data_no_conflicts` | 54.32ms | 84.00ms | 64.7% | 43.7% | PASS |
| `merge_schema_no_conflicts` | 18.46ms | 58.80ms | 31.4% | 4.2% | PASS |
| `merge_data_conflicts` | 25.99ms | 302.40ms | 8.6% | 1.0% | PASS |
| `merge_data_conflicts_with_resolve` | 25.75ms | 302.40ms | 8.5% | 1.3% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
