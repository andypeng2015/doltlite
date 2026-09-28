# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-28 19:17 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260920.314.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/36460459547)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.40s | 11.01s | 1.1× | 1.2% | **PASS** |
| Writes | 2.09s | 3.49s | 1.7× | 1.2% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.89s | 11.14s | 1.0× | 1.3% | **PASS** |
| Writes | 2.83s | 3.84s | 1.4× | 1.7% | **PASS** |
| Autocommit writes | 716.53ms | 2.24s | 3.1× | 6.7% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.47s | 2.60s | 1.1× | 1.6% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.57s | 2.76s | 1.1× | 1.1% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.49s | 2.75s | 1.1× | 0.9% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.86s | 2.90s | 1.0× | 1.6% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 437.10ms | 734.87ms | 1.7× | 1.5% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 520.04ms | 884.86ms | 1.7× | 1.0% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 517.22ms | 882.15ms | 1.7× | 1.0% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 612.93ms | 986.24ms | 1.6× | 1.5% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.71s | 2.67s | 1.0× | 1.9% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.67s | 2.77s | 1.0× | 1.1% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.60s | 2.77s | 1.1× | 1.1% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.91s | 2.93s | 1.0× | 1.3% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 598.93ms | 804.59ms | 1.3× | 1.8% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 777.22ms | 978.97ms | 1.3× | 5.0% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 648.99ms | 966.80ms | 1.5× | 1.6% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 809.39ms | 1.09s | 1.4× | 2.0% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.56s | 2.67s | 1.0× | 1.9% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.54s | 2.75s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.53s | 2.77s | 1.1× | 1.2% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.79s | 2.94s | 1.1× | 1.7% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 204.26ms | 636.16ms | 3.1× | 7.2% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 142.52ms | 457.52ms | 3.2× | 5.9% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 152.20ms | 494.90ms | 3.3× | 6.4% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 217.55ms | 646.42ms | 3.0× | 6.7% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.55ms | 29.98ms | 1.3× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 10.28ms | 11.67ms | 1.1× | 2.5% | PASS |
| mem_reads | `oltp_sum_range` | 9.50ms | 11.36ms | 1.2× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 2.61ms | 2.85ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 3.68ms | 3.98ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 3.86ms | 5.31ms | 1.4× | 2.1% | PASS |
| mem_reads | `select_random_points` | 9.80ms | 11.37ms | 1.2× | 2.1% | PASS |
| mem_reads | `select_random_ranges` | 2.92ms | 3.97ms | 1.4× | 2.6% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.25ms | 1.0× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 30.47ms | 32.99ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 5.83ms | 8.00ms | 1.4× | 1.8% | PASS |
| mem_reads | `index_join_scan` | 3.25ms | 4.61ms | 1.4× | 2.5% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.23s | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 102.66ms | 118.34ms | 1.2× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 178.67ms | 271.73ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 15.21ms | 28.32ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 49.74ms | 98.06ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 34.53ms | 60.78ms | 1.8× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 45.10ms | 75.16ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_write_only` | 22.12ms | 47.87ms | 2.2× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 24.14ms | 38.44ms | 1.6× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 67.59ms | 114.51ms | 1.7× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 97.03ms | 49.64ms | 0.5× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 18.16ms | 13.94ms | 0.8× | 2.7% | PASS |
| file_reads | `oltp_sum_range` | 17.35ms | 13.65ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 3.70ms | 3.36ms | 0.9× | 3.5% | PASS |
| file_reads | `oltp_distinct_range` | 4.77ms | 4.55ms | 1.0× | 2.7% | PASS |
| file_reads | `oltp_index_scan` | 11.47ms | 7.59ms | 0.7× | 1.9% | PASS |
| file_reads | `select_random_points` | 17.81ms | 13.68ms | 0.8× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 10.41ms | 6.00ms | 0.6× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 11.98ms | 6.48ms | 0.5× | 1.8% | PASS |
| file_reads | `groupby_scan` | 31.87ms | 33.93ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 10.09ms | 9.68ms | 1.0× | 1.9% | PASS |
| file_reads | `index_join_scan` | 4.29ms | 5.00ms | 1.2× | 3.2% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 1.20s | 1.23s | 1.0× | 0.7% | PASS |
| file_reads | `oltp_read_only` | 212.46ms | 148.00ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 191.88ms | 281.92ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_insert` | 22.19ms | 32.59ms | 1.5× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 77.97ms | 108.84ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 58.49ms | 72.32ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 68.92ms | 85.15ms | 1.2× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 45.88ms | 56.10ms | 1.2× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 40.27ms | 44.94ms | 1.1× | 2.0% | PASS |
| file_writes | `oltp_read_write` | 93.34ms | 122.72ms | 1.3× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 47.83ms | 49.63ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 13.44ms | 13.93ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_sum_range` | 12.57ms | 13.66ms | 1.1× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 3.26ms | 3.46ms | 1.1× | 3.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.33ms | 4.52ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.68ms | 7.59ms | 1.1× | 2.0% | PASS |
| ac_reads | `select_random_points` | 13.07ms | 13.64ms | 1.0× | 2.3% | PASS |
| ac_reads | `select_random_ranges` | 5.58ms | 5.96ms | 1.1× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 7.16ms | 6.52ms | 0.9× | 2.7% | PASS |
| ac_reads | `groupby_scan` | 31.47ms | 33.94ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 7.56ms | 9.69ms | 1.3× | 2.0% | PASS |
| ac_reads | `index_join_scan` | 3.77ms | 5.01ms | 1.3× | 3.5% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.23s | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 139.18ms | 147.94ms | 1.1× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.26ms | 65.72ms | 3.0× | 6.4% | PASS |
| ac_writes | `oltp_insert_ac` | 25.57ms | 80.56ms | 3.2× | 7.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.53ms | 92.09ms | 3.3× | 8.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.14ms | 72.34ms | 3.0× | 7.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.47ms | 81.95ms | 3.3× | 7.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.51ms | 82.96ms | 3.3× | 6.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.60ms | 72.22ms | 3.1× | 7.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.17ms | 88.32ms | 2.8× | 6.7% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 30.45ms | 33.49ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 13.73ms | 12.73ms | 0.9× | 0.8% | PASS |
| mem_reads | `oltp_sum_range` | 13.33ms | 12.59ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_order_range` | 2.89ms | 2.91ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.66ms | 4.00ms | 1.1× | 0.6% | PASS |
| mem_reads | `oltp_index_scan` | 3.51ms | 5.54ms | 1.6× | 1.2% | PASS |
| mem_reads | `select_random_points` | 20.79ms | 20.78ms | 1.0× | 2.1% | PASS |
| mem_reads | `select_random_ranges` | 3.08ms | 4.69ms | 1.5× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 3.66ms | 4.26ms | 1.2× | 0.9% | PASS |
| mem_reads | `groupby_scan` | 30.75ms | 33.04ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 9.87ms | 9.20ms | 0.9× | 1.5% | PASS |
| mem_reads | `index_join_scan` | 3.14ms | 5.26ms | 1.7× | 1.5% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.20s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.25s | 1.29s | 1.0× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 125.91ms | 129.90ms | 1.0× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 204.65ms | 320.34ms | 1.6× | 0.6% | PASS |
| mem_writes | `oltp_insert` | 15.84ms | 34.11ms | 2.2× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 60.01ms | 131.13ms | 2.2× | 0.8% | PASS |
| mem_writes | `oltp_update_non_index` | 43.77ms | 74.50ms | 1.7× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 47.76ms | 93.18ms | 2.0× | 0.8% | PASS |
| mem_writes | `oltp_write_only` | 24.31ms | 53.31ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 34.62ms | 47.34ms | 1.4× | 1.1% | PASS |
| mem_writes | `oltp_read_write` | 89.08ms | 130.94ms | 1.5× | 1.3% | PASS |
| file_reads | `oltp_point_select` | 66.11ms | 44.84ms | 0.7× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 18.04ms | 14.41ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_sum_range` | 17.72ms | 14.37ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.37ms | 3.12ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_distinct_range` | 4.17ms | 4.24ms | 1.0× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 7.04ms | 6.93ms | 1.0× | 0.8% | PASS |
| file_reads | `select_random_points` | 25.87ms | 22.60ms | 0.9× | 1.5% | PASS |
| file_reads | `select_random_ranges` | 6.37ms | 5.73ms | 0.9× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 7.04ms | 5.30ms | 0.8× | 1.4% | PASS |
| file_reads | `groupby_scan` | 31.36ms | 33.33ms | 1.1× | 0.4% | PASS |
| file_reads | `index_join` | 12.04ms | 10.01ms | 0.8× | 1.8% | PASS |
| file_reads | `index_join_scan` | 3.62ms | 5.42ms | 1.5× | 1.8% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 1.25s | 1.27s | 1.0× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 176.00ms | 147.33ms | 0.8× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 224.26ms | 335.27ms | 1.5× | 0.9% | PASS |
| file_writes | `oltp_insert` | 21.06ms | 40.49ms | 1.9× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 106.99ms | 146.85ms | 1.4× | 9.8% | PASS |
| file_writes | `oltp_update_non_index` | 99.72ms | 87.91ms | 0.9× | 8.3% | PASS |
| file_writes | `oltp_delete_insert` | 72.51ms | 106.91ms | 1.5× | 0.9% | PASS |
| file_writes | `oltp_write_only` | 58.69ms | 64.53ms | 1.1× | 14.9% | PASS |
| file_writes | `types_delete_insert` | 53.57ms | 55.82ms | 1.0× | 1.1% | PASS |
| file_writes | `oltp_read_write` | 140.42ms | 141.19ms | 1.0× | 8.4% | PASS |
| ac_reads | `oltp_point_select` | 43.00ms | 44.81ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_range_select` | 15.36ms | 14.22ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 14.76ms | 14.09ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_order_range` | 3.09ms | 3.15ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 3.86ms | 4.25ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_index_scan` | 4.75ms | 6.83ms | 1.4× | 1.2% | PASS |
| ac_reads | `select_random_points` | 21.83ms | 22.02ms | 1.0× | 2.1% | PASS |
| ac_reads | `select_random_ranges` | 4.18ms | 5.70ms | 1.4× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 4.83ms | 5.28ms | 1.1× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 30.76ms | 33.16ms | 1.1× | 0.5% | PASS |
| ac_reads | `index_join` | 10.46ms | 9.62ms | 0.9× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 3.38ms | 5.40ms | 1.6× | 1.5% | PASS |
| ac_reads | `types_table_scan` | 1.03s | 1.18s | 1.1× | 1.3% | PASS |
| ac_reads | `table_scan` | 1.21s | 1.26s | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 138.89ms | 144.97ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 16.05ms | 45.06ms | 2.8× | 5.5% | PASS |
| ac_writes | `oltp_insert_ac` | 18.14ms | 55.36ms | 3.1× | 6.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 18.28ms | 65.72ms | 3.6× | 5.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 15.56ms | 50.45ms | 3.2× | 7.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 17.81ms | 59.91ms | 3.4× | 7.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 17.44ms | 60.43ms | 3.5× | 5.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 16.06ms | 55.44ms | 3.5× | 6.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 23.20ms | 65.16ms | 2.8× | 5.1% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.41ms | 34.63ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 13.68ms | 12.83ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_sum_range` | 13.37ms | 12.75ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 2.86ms | 2.92ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.58ms | 4.00ms | 1.1× | 0.6% | PASS |
| mem_reads | `oltp_index_scan` | 3.52ms | 5.59ms | 1.6× | 1.0% | PASS |
| mem_reads | `select_random_points` | 20.43ms | 20.70ms | 1.0× | 1.5% | PASS |
| mem_reads | `select_random_ranges` | 3.02ms | 4.78ms | 1.6× | 0.7% | PASS |
| mem_reads | `covering_index_scan` | 3.65ms | 4.29ms | 1.2× | 0.8% | PASS |
| mem_reads | `groupby_scan` | 30.10ms | 32.97ms | 1.1× | 0.4% | PASS |
| mem_reads | `index_join` | 9.52ms | 9.17ms | 1.0× | 1.2% | PASS |
| mem_reads | `index_join_scan` | 3.06ms | 5.20ms | 1.7× | 1.3% | PASS |
| mem_reads | `types_table_scan` | 1.03s | 1.18s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 0.3% | PASS |
| mem_reads | `oltp_read_only` | 123.98ms | 130.76ms | 1.1× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 207.33ms | 323.09ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_insert` | 16.19ms | 34.18ms | 2.1× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 59.24ms | 128.80ms | 2.2× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 43.35ms | 73.01ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 46.54ms | 92.92ms | 2.0× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 23.95ms | 52.94ms | 2.2× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 33.16ms | 46.81ms | 1.4× | 0.8% | PASS |
| mem_writes | `oltp_read_write` | 87.45ms | 130.41ms | 1.5× | 1.0% | PASS |
| file_reads | `oltp_point_select` | 65.86ms | 44.86ms | 0.7× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 17.25ms | 14.33ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 16.81ms | 14.21ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 3.27ms | 3.09ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 4.02ms | 4.22ms | 1.1× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 6.99ms | 6.90ms | 1.0× | 1.0% | PASS |
| file_reads | `select_random_points` | 24.82ms | 22.39ms | 0.9× | 1.2% | PASS |
| file_reads | `select_random_ranges` | 6.30ms | 5.83ms | 0.9× | 1.6% | PASS |
| file_reads | `covering_index_scan` | 7.03ms | 5.35ms | 0.8× | 1.0% | PASS |
| file_reads | `groupby_scan` | 30.67ms | 33.22ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 11.87ms | 9.96ms | 0.8× | 1.8% | PASS |
| file_reads | `index_join_scan` | 3.55ms | 5.40ms | 1.5× | 1.3% | PASS |
| file_reads | `types_table_scan` | 1.02s | 1.18s | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 0.7% | PASS |
| file_reads | `oltp_read_only` | 173.15ms | 147.32ms | 0.9× | 0.5% | PASS |
| file_writes | `oltp_bulk_insert` | 222.78ms | 335.87ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 21.07ms | 40.93ms | 1.9× | 2.1% | PASS |
| file_writes | `oltp_update_index` | 77.43ms | 142.62ms | 1.8× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 65.61ms | 84.50ms | 1.3× | 8.6% | PASS |
| file_writes | `oltp_delete_insert` | 66.81ms | 104.24ms | 1.6× | 1.2% | PASS |
| file_writes | `oltp_write_only` | 40.73ms | 62.49ms | 1.5× | 1.6% | PASS |
| file_writes | `types_delete_insert` | 48.58ms | 55.72ms | 1.1× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 105.98ms | 140.44ms | 1.3× | 1.5% | PASS |
| ac_reads | `oltp_point_select` | 42.66ms | 44.94ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 14.80ms | 14.29ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 14.45ms | 14.22ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 3.01ms | 3.11ms | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_distinct_range` | 3.77ms | 4.24ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_index_scan` | 4.81ms | 6.88ms | 1.4× | 0.9% | PASS |
| ac_reads | `select_random_points` | 22.10ms | 22.32ms | 1.0× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 4.12ms | 5.86ms | 1.4× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 4.86ms | 5.38ms | 1.1× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 30.30ms | 33.28ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 10.82ms | 10.04ms | 0.9× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 3.34ms | 5.42ms | 1.6× | 1.5% | PASS |
| ac_reads | `types_table_scan` | 1.03s | 1.18s | 1.1× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.21s | 1.28s | 1.1× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 140.32ms | 147.73ms | 1.1× | 0.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.25ms | 46.59ms | 2.7× | 5.2% | PASS |
| ac_writes | `oltp_insert_ac` | 18.73ms | 61.48ms | 3.3× | 6.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.61ms | 71.50ms | 3.5× | 5.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.70ms | 55.94ms | 3.4× | 6.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 19.15ms | 65.72ms | 3.4× | 7.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 18.37ms | 65.07ms | 3.5× | 8.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.27ms | 57.56ms | 3.2× | 8.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 23.11ms | 71.03ms | 3.1× | 4.8% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.94ms | 41.98ms | 1.2× | 2.5% | PASS |
| mem_reads | `oltp_range_select` | 20.54ms | 21.62ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 18.27ms | 20.96ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 3.67ms | 3.92ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.82ms | 5.19ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 4.71ms | 6.46ms | 1.4× | 2.0% | PASS |
| mem_reads | `select_random_points` | 28.59ms | 33.20ms | 1.2× | 1.5% | PASS |
| mem_reads | `select_random_ranges` | 7.83ms | 9.16ms | 1.2× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 4.26ms | 4.51ms | 1.1× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 36.82ms | 40.16ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 8.01ms | 10.53ms | 1.3× | 1.8% | PASS |
| mem_reads | `index_join_scan` | 4.14ms | 5.85ms | 1.4× | 2.4% | PASS |
| mem_reads | `types_table_scan` | 1.14s | 1.21s | 1.1× | 2.0% | PASS |
| mem_reads | `table_scan` | 1.39s | 1.31s | 0.9× | 1.6% | PASS |
| mem_reads | `oltp_read_only` | 153.25ms | 174.14ms | 1.1× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 251.78ms | 354.51ms | 1.4× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 19.22ms | 35.25ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 68.93ms | 131.11ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 52.28ms | 85.66ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_delete_insert` | 51.12ms | 98.19ms | 1.9× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 27.63ms | 59.57ms | 2.2× | 1.8% | PASS |
| mem_writes | `types_delete_insert` | 33.30ms | 53.87ms | 1.6× | 1.9% | PASS |
| mem_writes | `oltp_read_write` | 108.67ms | 168.08ms | 1.5× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 112.41ms | 62.96ms | 0.6× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 29.06ms | 24.18ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 27.11ms | 23.44ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 4.63ms | 4.27ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.73ms | 5.47ms | 1.0× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 12.35ms | 8.67ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 38.85ms | 36.94ms | 1.0× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 16.06ms | 11.58ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 12.06ms | 6.91ms | 0.6× | 2.3% | PASS |
| file_reads | `groupby_scan` | 37.89ms | 40.47ms | 1.1× | 0.8% | PASS |
| file_reads | `index_join` | 12.55ms | 12.62ms | 1.0× | 2.6% | PASS |
| file_reads | `index_join_scan` | 5.11ms | 6.18ms | 1.2× | 2.8% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.18s | 1.1× | 1.1% | PASS |
| file_reads | `table_scan` | 1.26s | 1.29s | 1.0× | 3.3% | PASS |
| file_reads | `oltp_read_only` | 273.06ms | 207.88ms | 0.8× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 267.50ms | 373.67ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 27.48ms | 41.75ms | 1.5× | 2.7% | PASS |
| file_writes | `oltp_update_index` | 105.20ms | 149.80ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 83.00ms | 102.80ms | 1.2× | 1.9% | PASS |
| file_writes | `oltp_delete_insert` | 81.62ms | 113.23ms | 1.4× | 2.9% | PASS |
| file_writes | `oltp_write_only` | 54.22ms | 71.36ms | 1.3× | 2.5% | PASS |
| file_writes | `types_delete_insert` | 52.45ms | 62.15ms | 1.2× | 1.4% | PASS |
| file_writes | `oltp_read_write` | 137.91ms | 178.80ms | 1.3× | 2.2% | PASS |
| ac_reads | `oltp_point_select` | 60.21ms | 62.00ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 23.54ms | 23.94ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_sum_range` | 21.39ms | 23.17ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 4.04ms | 4.25ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 5.17ms | 5.48ms | 1.1× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 7.49ms | 8.65ms | 1.2× | 2.7% | PASS |
| ac_reads | `select_random_points` | 32.66ms | 36.84ms | 1.1× | 1.8% | PASS |
| ac_reads | `select_random_ranges` | 10.65ms | 11.48ms | 1.1× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 7.25ms | 6.86ms | 0.9× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 37.22ms | 40.41ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 10.32ms | 13.05ms | 1.3× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 4.68ms | 6.12ms | 1.3× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 1.09s | 1.19s | 1.1× | 2.1% | PASS |
| ac_reads | `table_scan` | 1.28s | 1.29s | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_read_only` | 193.71ms | 206.53ms | 1.1× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.90ms | 63.58ms | 2.7× | 7.5% | PASS |
| ac_writes | `oltp_insert_ac` | 26.59ms | 81.01ms | 3.0× | 8.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.63ms | 89.77ms | 3.2× | 6.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 25.13ms | 74.08ms | 2.9× | 6.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.74ms | 84.64ms | 3.2× | 6.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.40ms | 82.44ms | 3.0× | 6.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.60ms | 78.25ms | 3.1× | 8.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.56ms | 92.66ms | 2.7× | 5.8% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 11s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 94.44ms | 130.00ms | 72.6% | 0.6% | PASS |
| `status_dirty_many_tables` | 97.76ms | 130.00ms | 75.2% | 0.3% | PASS |
| `diff_regular_working_one_table` | 89.54ms | 120.00ms | 74.6% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 103.49ms | 140.00ms | 73.9% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 102.94ms | 140.00ms | 73.5% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 104.00ms | 140.00ms | 74.3% | 0.4% | PASS |
| `branch_list_many_branches` | 22.56ms | 35.00ms | 64.5% | 1.0% | PASS |
| `branch_create_delete` | 24.66ms | 40.00ms | 61.6% | 1.1% | PASS |
| `checkout_branch_clean` | 59.82ms | 150.00ms | 39.9% | 0.6% | PASS |
| `merge_data_no_conflicts` | 28.50ms | 50.00ms | 57.0% | 1.3% | PASS |
| `merge_schema_no_conflicts` | 21.48ms | 35.00ms | 61.4% | 1.1% | PASS |
| `merge_data_conflicts` | 32.34ms | 180.00ms | 18.0% | 0.9% | PASS |
| `merge_data_conflicts_with_resolve` | 32.12ms | 180.00ms | 17.8% | 0.6% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
