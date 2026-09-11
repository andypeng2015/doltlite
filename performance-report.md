# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-11 15:18 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/34605906909)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.45s | 10.98s | 1.1× | 1.4% | **PASS** |
| Writes | 2.29s | 3.77s | 1.7× | 1.2% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.60s | 11.30s | 1.0× | 1.5% | **PASS** |
| Writes | 3.18s | 4.13s | 1.3× | 1.8% | **PASS** |
| Autocommit writes | 828.10ms | 2.93s | 3.5× | 5.5% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.43s | 2.58s | 1.1× | 0.9% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.59s | 2.74s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.58s | 2.76s | 1.1× | 2.5% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.86s | 2.89s | 1.0× | 1.5% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 434.99ms | 726.57ms | 1.7× | 0.9% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 591.98ms | 1.00s | 1.7× | 1.2% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 625.89ms | 1.02s | 1.6× | 1.5% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 632.70ms | 1.03s | 1.6× | 1.4% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.66s | 2.65s | 1.0× | 1.8% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.85s | 2.81s | 1.0× | 1.3% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.02s | 2.89s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.06s | 2.96s | 1.0× | 1.3% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 586.41ms | 792.96ms | 1.4× | 1.4% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 924.99ms | 1.11s | 1.2× | 4.2% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 870.56ms | 1.13s | 1.3× | 2.0% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 800.37ms | 1.10s | 1.4× | 1.6% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.51s | 2.65s | 1.1× | 1.6% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.68s | 2.81s | 1.1× | 1.6% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.76s | 2.86s | 1.0× | 3.3% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.97s | 2.97s | 1.0× | 1.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 194.50ms | 709.39ms | 3.6× | 4.1% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 208.70ms | 721.65ms | 3.5× | 5.7% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 214.47ms | 734.23ms | 3.4× | 5.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 210.44ms | 761.33ms | 3.6× | 6.0% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 22.81ms | 29.69ms | 1.3× | 0.9% | PASS |
| mem_reads | `oltp_range_select` | 9.20ms | 11.08ms | 1.2× | 0.9% | PASS |
| mem_reads | `oltp_sum_range` | 8.92ms | 11.13ms | 1.2× | 0.8% | PASS |
| mem_reads | `oltp_order_range` | 2.45ms | 2.77ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 3.52ms | 3.95ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 3.69ms | 5.11ms | 1.4× | 2.1% | PASS |
| mem_reads | `select_random_points` | 8.60ms | 10.59ms | 1.2× | 1.7% | PASS |
| mem_reads | `select_random_ranges` | 2.67ms | 3.86ms | 1.4× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.20ms | 4.14ms | 1.0× | 1.0% | PASS |
| mem_reads | `groupby_scan` | 29.95ms | 32.94ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 5.66ms | 7.76ms | 1.4× | 0.8% | PASS |
| mem_reads | `index_join_scan` | 2.89ms | 4.54ms | 1.6× | 3.3% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 1.17s | 1.22s | 1.0× | 0.3% | PASS |
| mem_reads | `oltp_read_only` | 99.75ms | 117.08ms | 1.2× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 183.31ms | 273.23ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 15.23ms | 27.98ms | 1.8× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 47.52ms | 93.52ms | 2.0× | 0.7% | PASS |
| mem_writes | `oltp_update_non_index` | 33.54ms | 59.73ms | 1.8× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 44.75ms | 74.16ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 21.52ms | 46.98ms | 2.2× | 1.1% | PASS |
| mem_writes | `types_delete_insert` | 23.98ms | 38.20ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 65.14ms | 112.77ms | 1.7× | 0.8% | PASS |
| file_reads | `oltp_point_select` | 97.13ms | 49.57ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 17.70ms | 13.41ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 17.19ms | 13.49ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 3.48ms | 3.26ms | 0.9× | 3.6% | PASS |
| file_reads | `oltp_distinct_range` | 4.58ms | 4.36ms | 1.0× | 3.1% | PASS |
| file_reads | `oltp_index_scan` | 11.34ms | 7.52ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 16.64ms | 13.25ms | 0.8× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 10.22ms | 5.98ms | 0.6× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 12.10ms | 6.47ms | 0.5× | 1.9% | PASS |
| file_reads | `groupby_scan` | 31.46ms | 33.66ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 10.02ms | 9.57ms | 1.0× | 2.5% | PASS |
| file_reads | `index_join_scan` | 4.10ms | 4.94ms | 1.2× | 2.2% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.3% | PASS |
| file_reads | `table_scan` | 1.17s | 1.21s | 1.0× | 0.3% | PASS |
| file_reads | `oltp_read_only` | 208.09ms | 146.99ms | 0.7× | 0.6% | PASS |
| file_writes | `oltp_bulk_insert` | 196.60ms | 283.26ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 21.99ms | 32.26ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_update_index` | 74.10ms | 104.73ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 56.93ms | 70.50ms | 1.2× | 1.4% | PASS |
| file_writes | `oltp_delete_insert` | 66.26ms | 82.76ms | 1.2× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 43.56ms | 54.45ms | 1.3× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 38.68ms | 44.06ms | 1.1× | 1.2% | PASS |
| file_writes | `oltp_read_write` | 88.30ms | 120.93ms | 1.4× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 47.58ms | 49.69ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_range_select` | 12.40ms | 13.54ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_sum_range` | 12.00ms | 13.62ms | 1.1× | 2.1% | PASS |
| ac_reads | `oltp_order_range` | 3.09ms | 3.43ms | 1.1× | 4.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.14ms | 4.43ms | 1.1× | 3.5% | PASS |
| ac_reads | `oltp_index_scan` | 6.72ms | 7.68ms | 1.1× | 2.3% | PASS |
| ac_reads | `select_random_points` | 13.91ms | 14.29ms | 1.0× | 2.1% | PASS |
| ac_reads | `select_random_ranges` | 5.77ms | 6.08ms | 1.1× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 7.37ms | 6.75ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 30.75ms | 33.54ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 7.26ms | 9.50ms | 1.3× | 1.5% | PASS |
| ac_reads | `index_join_scan` | 3.54ms | 4.96ms | 1.4× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.3% | PASS |
| ac_reads | `table_scan` | 1.17s | 1.21s | 1.0× | 0.2% | PASS |
| ac_reads | `oltp_read_only` | 136.30ms | 147.52ms | 1.1× | 0.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.97ms | 74.56ms | 3.4× | 5.6% | PASS |
| ac_writes | `oltp_insert_ac` | 25.31ms | 88.36ms | 3.5× | 4.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.54ms | 99.60ms | 3.9× | 3.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.39ms | 82.47ms | 3.5× | 5.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.62ms | 91.09ms | 3.9× | 3.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.97ms | 92.11ms | 3.8× | 3.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.35ms | 83.53ms | 3.9× | 4.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.34ms | 97.68ms | 3.3× | 2.8% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.96ms | 38.37ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 16.21ms | 14.47ms | 0.9× | 1.9% | PASS |
| mem_reads | `oltp_sum_range` | 15.10ms | 13.78ms | 0.9× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 3.16ms | 3.10ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.28ms | 4.26ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 3.91ms | 6.08ms | 1.6× | 1.1% | PASS |
| mem_reads | `select_random_points` | 21.86ms | 20.97ms | 1.0× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 3.50ms | 5.33ms | 1.5× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.21ms | 4.52ms | 1.1× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 34.42ms | 34.55ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 10.16ms | 8.93ms | 0.9× | 1.8% | PASS |
| mem_reads | `index_join_scan` | 3.69ms | 5.43ms | 1.5× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 1.08s | 1.17s | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 1.21s | 1.27s | 1.0× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 134.76ms | 136.78ms | 1.0× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 244.65ms | 373.43ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 18.15ms | 38.58ms | 2.1× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 64.87ms | 140.26ms | 2.2× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 47.94ms | 85.16ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 53.89ms | 103.48ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 27.59ms | 61.35ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 38.87ms | 53.96ms | 1.4× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 96.02ms | 144.78ms | 1.5× | 1.2% | PASS |
| file_reads | `oltp_point_select` | 110.76ms | 58.74ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 25.22ms | 17.06ms | 0.7× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 24.04ms | 16.31ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.23ms | 3.45ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.33ms | 4.64ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 11.82ms | 8.52ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_points` | 32.42ms | 24.38ms | 0.8× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 11.18ms | 7.48ms | 0.7× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 12.08ms | 6.92ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 35.75ms | 35.00ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 15.43ms | 11.47ms | 0.7× | 2.0% | PASS |
| file_reads | `index_join_scan` | 4.82ms | 6.13ms | 1.3× | 4.4% | PASS |
| file_reads | `types_table_scan` | 1.08s | 1.17s | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 1.22s | 1.27s | 1.0× | 0.7% | PASS |
| file_reads | `oltp_read_only` | 247.52ms | 167.36ms | 0.7× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 269.29ms | 387.96ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 26.02ms | 46.00ms | 1.8× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 128.29ms | 160.53ms | 1.3× | 10.4% | PASS |
| file_writes | `oltp_update_non_index` | 96.56ms | 100.80ms | 1.0× | 6.5% | PASS |
| file_writes | `oltp_delete_insert` | 96.36ms | 119.55ms | 1.2× | 2.0% | PASS |
| file_writes | `oltp_write_only` | 89.86ms | 73.48ms | 0.8× | 7.9% | PASS |
| file_writes | `types_delete_insert` | 72.31ms | 63.21ms | 0.9× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 146.30ms | 158.13ms | 1.1× | 6.3% | PASS |
| ac_reads | `oltp_point_select` | 60.02ms | 58.73ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 19.43ms | 17.18ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 18.27ms | 16.45ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_order_range` | 3.63ms | 3.46ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.68ms | 4.63ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 6.51ms | 8.52ms | 1.3× | 1.4% | PASS |
| ac_reads | `select_random_points` | 25.75ms | 24.61ms | 1.0× | 1.9% | PASS |
| ac_reads | `select_random_ranges` | 6.08ms | 7.50ms | 1.2× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 6.93ms | 6.98ms | 1.0× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 35.01ms | 35.22ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 12.17ms | 11.70ms | 1.0× | 2.6% | PASS |
| ac_reads | `index_join_scan` | 4.15ms | 6.01ms | 1.4× | 3.2% | PASS |
| ac_reads | `types_table_scan` | 1.08s | 1.17s | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.22s | 1.27s | 1.0× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 172.37ms | 167.23ms | 1.0× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.51ms | 76.14ms | 3.1× | 6.0% | PASS |
| ac_writes | `oltp_insert_ac` | 26.49ms | 87.47ms | 3.3× | 4.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.08ms | 101.81ms | 3.6× | 5.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.88ms | 85.53ms | 3.7× | 6.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.02ms | 93.53ms | 3.6× | 6.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.69ms | 92.64ms | 3.6× | 6.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.60ms | 85.58ms | 3.6× | 5.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.43ms | 98.96ms | 3.1× | 3.2% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.73ms | 38.47ms | 1.1× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 18.39ms | 15.05ms | 0.8× | 3.1% | PASS |
| mem_reads | `oltp_sum_range` | 15.48ms | 13.81ms | 0.9× | 2.1% | PASS |
| mem_reads | `oltp_order_range` | 3.37ms | 3.22ms | 1.0× | 2.6% | PASS |
| mem_reads | `oltp_distinct_range` | 4.52ms | 4.41ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.20ms | 6.55ms | 1.6× | 2.9% | PASS |
| mem_reads | `select_random_points` | 24.82ms | 21.95ms | 0.9× | 3.8% | PASS |
| mem_reads | `select_random_ranges` | 3.83ms | 5.53ms | 1.4× | 3.1% | PASS |
| mem_reads | `covering_index_scan` | 4.23ms | 4.64ms | 1.1× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 34.41ms | 34.57ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 11.29ms | 10.05ms | 0.9× | 3.3% | PASS |
| mem_reads | `index_join_scan` | 3.96ms | 5.77ms | 1.5× | 5.0% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 142.41ms | 139.96ms | 1.0× | 2.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 247.93ms | 371.35ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 19.27ms | 39.64ms | 2.1× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 71.38ms | 148.79ms | 2.1× | 2.0% | PASS |
| mem_writes | `oltp_update_non_index` | 49.17ms | 83.53ms | 1.7× | 2.1% | PASS |
| mem_writes | `oltp_delete_insert` | 57.22ms | 107.90ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 29.78ms | 63.12ms | 2.1× | 1.1% | PASS |
| mem_writes | `types_delete_insert` | 38.90ms | 53.04ms | 1.4× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 112.24ms | 151.55ms | 1.4× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 116.36ms | 59.81ms | 0.5× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 25.50ms | 17.00ms | 0.7× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 24.45ms | 16.32ms | 0.7× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 4.29ms | 3.44ms | 0.8× | 2.8% | PASS |
| file_reads | `oltp_distinct_range` | 5.44ms | 4.66ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 12.26ms | 8.89ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 30.62ms | 24.31ms | 0.8× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 11.07ms | 7.46ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.78ms | 6.97ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 35.38ms | 35.02ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 14.67ms | 11.26ms | 0.8× | 2.2% | PASS |
| file_reads | `index_join_scan` | 4.85ms | 6.09ms | 1.3× | 2.7% | PASS |
| file_reads | `types_table_scan` | 1.14s | 1.19s | 1.0× | 2.7% | PASS |
| file_reads | `table_scan` | 1.33s | 1.32s | 1.0× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 263.33ms | 173.85ms | 0.7× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 269.57ms | 384.68ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 25.72ms | 45.85ms | 1.8× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 108.71ms | 165.47ms | 1.5× | 2.3% | PASS |
| file_writes | `oltp_update_non_index` | 91.14ms | 102.52ms | 1.1× | 7.5% | PASS |
| file_writes | `oltp_delete_insert` | 90.91ms | 120.03ms | 1.3× | 1.9% | PASS |
| file_writes | `oltp_write_only` | 58.91ms | 73.07ms | 1.2× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 73.62ms | 66.60ms | 0.9× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 151.98ms | 168.53ms | 1.1× | 2.5% | PASS |
| ac_reads | `oltp_point_select` | 62.16ms | 59.33ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_range_select` | 19.89ms | 17.32ms | 0.9× | 3.7% | PASS |
| ac_reads | `oltp_sum_range` | 19.49ms | 16.86ms | 0.9× | 4.3% | PASS |
| ac_reads | `oltp_order_range` | 4.17ms | 3.67ms | 0.9× | 2.9% | PASS |
| ac_reads | `oltp_distinct_range` | 5.26ms | 4.83ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_index_scan` | 7.04ms | 8.92ms | 1.3× | 3.9% | PASS |
| ac_reads | `select_random_points` | 29.90ms | 26.75ms | 0.9× | 4.3% | PASS |
| ac_reads | `select_random_ranges` | 6.51ms | 7.73ms | 1.2× | 2.6% | PASS |
| ac_reads | `covering_index_scan` | 7.72ms | 7.20ms | 0.9× | 3.4% | PASS |
| ac_reads | `groupby_scan` | 36.38ms | 35.73ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 14.31ms | 11.81ms | 0.8× | 5.8% | PASS |
| ac_reads | `index_join_scan` | 4.44ms | 6.22ms | 1.4× | 4.2% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.21s | 1.0× | 3.3% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 179.50ms | 170.53ms | 1.0× | 1.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 25.07ms | 76.95ms | 3.1× | 7.3% | PASS |
| ac_writes | `oltp_insert_ac` | 25.71ms | 93.74ms | 3.6× | 5.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.01ms | 101.85ms | 3.6× | 5.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.16ms | 84.60ms | 3.5× | 5.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.41ms | 94.65ms | 3.5× | 7.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.18ms | 95.93ms | 3.8× | 5.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.65ms | 85.77ms | 3.3× | 6.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.28ms | 100.74ms | 3.0× | 3.3% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.67ms | 41.59ms | 1.2× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 19.65ms | 21.50ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_sum_range` | 18.15ms | 20.72ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_order_range` | 3.59ms | 3.94ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.77ms | 5.17ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.63ms | 6.39ms | 1.4× | 1.9% | PASS |
| mem_reads | `select_random_points` | 28.81ms | 34.01ms | 1.2× | 2.2% | PASS |
| mem_reads | `select_random_ranges` | 7.90ms | 9.10ms | 1.2× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.25ms | 4.62ms | 1.1× | 2.7% | PASS |
| mem_reads | `groupby_scan` | 36.80ms | 39.95ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 7.99ms | 11.07ms | 1.4× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.90ms | 5.57ms | 1.4× | 1.5% | PASS |
| mem_reads | `types_table_scan` | 1.16s | 1.21s | 1.0× | 1.9% | PASS |
| mem_reads | `table_scan` | 1.37s | 1.30s | 0.9× | 1.8% | PASS |
| mem_reads | `oltp_read_only` | 156.04ms | 177.03ms | 1.1× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 250.49ms | 359.24ms | 1.4× | 1.3% | PASS |
| mem_writes | `oltp_insert` | 19.89ms | 36.60ms | 1.8× | 1.4% | PASS |
| mem_writes | `oltp_update_index` | 72.74ms | 140.69ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 55.46ms | 90.62ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 55.02ms | 104.43ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 29.57ms | 62.75ms | 2.1× | 1.9% | PASS |
| mem_writes | `types_delete_insert` | 35.00ms | 57.07ms | 1.6× | 0.7% | PASS |
| mem_writes | `oltp_read_write` | 114.53ms | 175.03ms | 1.5× | 0.6% | PASS |
| file_reads | `oltp_point_select` | 112.57ms | 63.77ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 29.18ms | 24.38ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_sum_range` | 26.85ms | 23.54ms | 0.9× | 0.7% | PASS |
| file_reads | `oltp_order_range` | 4.59ms | 4.31ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 5.82ms | 5.52ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 12.34ms | 8.52ms | 0.7× | 1.1% | PASS |
| file_reads | `select_random_points` | 37.82ms | 36.84ms | 1.0× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 15.53ms | 11.31ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 11.78ms | 6.60ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 36.98ms | 39.92ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 12.24ms | 12.14ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join_scan` | 4.75ms | 5.85ms | 1.2× | 1.5% | PASS |
| file_reads | `types_table_scan` | 1.08s | 1.19s | 1.1× | 1.6% | PASS |
| file_reads | `table_scan` | 1.40s | 1.32s | 0.9× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 273.77ms | 210.56ms | 0.8× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 266.65ms | 375.22ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 27.28ms | 42.19ms | 1.5× | 2.2% | PASS |
| file_writes | `oltp_update_index` | 102.62ms | 150.69ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 81.17ms | 102.30ms | 1.3× | 1.1% | PASS |
| file_writes | `oltp_delete_insert` | 79.15ms | 111.20ms | 1.4× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 54.48ms | 72.23ms | 1.3× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 51.69ms | 62.00ms | 1.2× | 1.8% | PASS |
| file_writes | `oltp_read_write` | 137.33ms | 182.01ms | 1.3× | 1.3% | PASS |
| ac_reads | `oltp_point_select` | 60.80ms | 63.80ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 23.68ms | 24.33ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_sum_range` | 21.25ms | 23.35ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 4.06ms | 4.32ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 5.11ms | 5.49ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_index_scan` | 7.33ms | 8.48ms | 1.2× | 1.3% | PASS |
| ac_reads | `select_random_points` | 31.99ms | 36.99ms | 1.2× | 1.9% | PASS |
| ac_reads | `select_random_ranges` | 10.65ms | 11.46ms | 1.1× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 7.27ms | 6.84ms | 0.9× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 37.20ms | 40.26ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 9.90ms | 12.74ms | 1.3× | 1.6% | PASS |
| ac_reads | `index_join_scan` | 4.38ms | 5.96ms | 1.4× | 1.7% | PASS |
| ac_reads | `types_table_scan` | 1.14s | 1.20s | 1.0× | 1.3% | PASS |
| ac_reads | `table_scan` | 1.40s | 1.31s | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 199.36ms | 213.31ms | 1.1× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.67ms | 76.57ms | 3.2× | 6.7% | PASS |
| ac_writes | `oltp_insert_ac` | 26.11ms | 96.91ms | 3.7× | 6.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.74ms | 105.75ms | 3.8× | 5.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.49ms | 90.31ms | 3.7× | 4.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.57ms | 99.47ms | 3.7× | 8.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.75ms | 97.61ms | 3.8× | 5.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.19ms | 89.17ms | 3.8× | 8.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.90ms | 105.54ms | 3.2× | 5.5% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 32s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 57.22ms | 130.00ms | 44.0% | 1.4% | PASS |
| `status_dirty_many_tables` | 59.08ms | 130.00ms | 45.4% | 0.6% | PASS |
| `diff_regular_working_one_table` | 54.61ms | 120.00ms | 45.5% | 0.5% | PASS |
| `diff_regular_working_many_tables` | 61.90ms | 140.00ms | 44.2% | 1.0% | PASS |
| `diff_stat_working_many_tables` | 61.92ms | 140.00ms | 44.2% | 1.2% | PASS |
| `diff_schema_working_many_tables` | 62.32ms | 140.00ms | 44.5% | 1.0% | PASS |
| `branch_list_many_branches` | 15.16ms | 35.00ms | 43.3% | 1.5% | PASS |
| `branch_create_delete` | 17.43ms | 40.00ms | 43.6% | 2.2% | PASS |
| `checkout_branch_clean` | 81.52ms | 150.00ms | 54.3% | 1.3% | PASS |
| `merge_data_no_conflicts` | 24.76ms | 50.00ms | 49.5% | 1.0% | PASS |
| `merge_schema_no_conflicts` | 15.02ms | 35.00ms | 42.9% | 1.7% | PASS |
| `merge_data_conflicts` | 21.30ms | 180.00ms | 11.8% | 1.1% | PASS |
| `merge_data_conflicts_with_resolve` | 21.31ms | 180.00ms | 11.8% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
