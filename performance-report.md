# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-08 18:16 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261004.327.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/37811560516)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.25s | 8.45s | 1.0× | 1.9% | **PASS** |
| Writes | 1.64s | 2.64s | 1.6× | 1.4% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.37s | 8.74s | 0.9× | 1.4% | **PASS** |
| Writes | 3.79s | 3.93s | 1.0× | 3.8% | **PASS** |
| Autocommit writes | 751.85ms | 2.19s | 2.9× | 6.5% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.61s | 2.64s | 1.0× | 1.3% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.75s | 1.80s | 1.0× | 2.5% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.14s | 2.26s | 1.1× | 1.7% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 1.75s | 1.75s | 1.0× | 2.2% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 448.30ms | 748.73ms | 1.7× | 1.4% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 374.99ms | 602.27ms | 1.6× | 2.0% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 465.81ms | 738.10ms | 1.6× | 1.3% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 346.57ms | 546.01ms | 1.6× | 1.5% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.80s | 2.70s | 1.0× | 1.1% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.16s | 1.91s | 0.9× | 2.0% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.38s | 2.31s | 1.0× | 0.9% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.03s | 1.82s | 0.9× | 1.6% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 611.81ms | 816.56ms | 1.3× | 1.7% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.09s | 1.04s | 0.9× | 4.7% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.22s | 1.22s | 1.0× | 8.1% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 868.37ms | 865.67ms | 1.0× | 3.7% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.66s | 2.70s | 1.0× | 1.2% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.77s | 1.83s | 1.0× | 2.1% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.36s | 2.38s | 1.0× | 1.0% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 1.75s | 1.77s | 1.0× | 1.9% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 211.62ms | 643.98ms | 3.0× | 6.7% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 187.55ms | 507.96ms | 2.7× | 5.8% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 178.03ms | 561.73ms | 3.2× | 19.9% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 174.65ms | 475.17ms | 2.7× | 4.4% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.67ms | 30.49ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_range_select` | 10.32ms | 11.63ms | 1.1× | 1.9% | PASS |
| mem_reads | `oltp_sum_range` | 9.67ms | 11.40ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 2.64ms | 2.85ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.70ms | 3.98ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.96ms | 5.46ms | 1.4× | 1.7% | PASS |
| mem_reads | `select_random_points` | 10.20ms | 11.17ms | 1.1× | 2.6% | PASS |
| mem_reads | `select_random_ranges` | 3.03ms | 4.01ms | 1.3× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 4.26ms | 4.56ms | 1.1× | 2.4% | PASS |
| mem_reads | `groupby_scan` | 30.99ms | 33.15ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 5.83ms | 8.49ms | 1.5× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 3.20ms | 4.47ms | 1.4× | 1.2% | PASS |
| mem_reads | `types_table_scan` | 1.12s | 1.14s | 1.0× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.27s | 1.25s | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 108.35ms | 121.02ms | 1.1× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.06ms | 278.31ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 15.60ms | 28.92ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 51.12ms | 99.63ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 35.62ms | 62.19ms | 1.7× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 46.13ms | 76.47ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 22.18ms | 48.19ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 24.51ms | 38.92ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_read_write` | 69.09ms | 116.11ms | 1.7× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 98.86ms | 49.87ms | 0.5× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 18.12ms | 13.68ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 17.44ms | 13.51ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 3.48ms | 3.13ms | 0.9× | 2.2% | PASS |
| file_reads | `oltp_distinct_range` | 4.57ms | 4.28ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 11.77ms | 7.69ms | 0.7× | 1.2% | PASS |
| file_reads | `select_random_points` | 18.20ms | 13.33ms | 0.7× | 2.3% | PASS |
| file_reads | `select_random_ranges` | 10.52ms | 5.98ms | 0.6× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 12.11ms | 6.66ms | 0.5× | 1.1% | PASS |
| file_reads | `groupby_scan` | 31.92ms | 33.50ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 10.25ms | 9.75ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join_scan` | 4.22ms | 4.82ms | 1.1× | 1.4% | PASS |
| file_reads | `types_table_scan` | 1.08s | 1.13s | 1.0× | 0.9% | PASS |
| file_reads | `table_scan` | 1.26s | 1.25s | 1.0× | 1.0% | PASS |
| file_reads | `oltp_read_only` | 217.23ms | 150.22ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 197.01ms | 287.26ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 22.61ms | 32.88ms | 1.5× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 79.82ms | 111.51ms | 1.4× | 1.9% | PASS |
| file_writes | `oltp_update_non_index` | 59.66ms | 72.57ms | 1.2× | 1.5% | PASS |
| file_writes | `oltp_delete_insert` | 70.32ms | 85.41ms | 1.2× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 46.30ms | 56.01ms | 1.2× | 2.2% | PASS |
| file_writes | `types_delete_insert` | 40.70ms | 45.36ms | 1.1× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 95.39ms | 125.56ms | 1.3× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 48.98ms | 50.01ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 13.17ms | 13.70ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 12.43ms | 13.54ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.01ms | 3.10ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_distinct_range` | 4.08ms | 4.26ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.69ms | 7.62ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_points` | 12.79ms | 13.08ms | 1.0× | 1.4% | PASS |
| ac_reads | `select_random_ranges` | 5.62ms | 5.99ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.13ms | 6.65ms | 0.9× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 31.38ms | 33.61ms | 1.1× | 0.5% | PASS |
| ac_reads | `index_join` | 7.60ms | 9.75ms | 1.3× | 1.5% | PASS |
| ac_reads | `index_join_scan` | 3.68ms | 4.81ms | 1.3× | 1.5% | PASS |
| ac_reads | `types_table_scan` | 1.10s | 1.14s | 1.0× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.25s | 1.24s | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 143.46ms | 150.51ms | 1.0× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.91ms | 66.65ms | 2.9× | 7.7% | PASS |
| ac_writes | `oltp_insert_ac` | 26.85ms | 80.23ms | 3.0× | 6.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.18ms | 91.50ms | 3.1× | 6.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.21ms | 74.00ms | 3.1× | 4.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.31ms | 83.64ms | 3.2× | 5.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.56ms | 83.59ms | 3.1× | 7.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.36ms | 74.70ms | 3.1× | 6.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.23ms | 89.68ms | 2.9× | 4.7% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 25.74ms | 23.81ms | 0.9× | 2.5% | PASS |
| mem_reads | `oltp_range_select` | 11.72ms | 9.03ms | 0.8× | 1.3% | PASS |
| mem_reads | `oltp_sum_range` | 11.59ms | 9.29ms | 0.8× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 2.41ms | 2.12ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 2.93ms | 2.60ms | 0.9× | 2.1% | PASS |
| mem_reads | `oltp_index_scan` | 2.50ms | 3.81ms | 1.5× | 3.4% | PASS |
| mem_reads | `select_random_points` | 16.35ms | 13.92ms | 0.9× | 2.7% | PASS |
| mem_reads | `select_random_ranges` | 2.47ms | 3.45ms | 1.4× | 2.4% | PASS |
| mem_reads | `covering_index_scan` | 2.34ms | 2.94ms | 1.3× | 5.1% | PASS |
| mem_reads | `groupby_scan` | 20.40ms | 19.62ms | 1.0× | 1.9% | PASS |
| mem_reads | `index_join` | 7.74ms | 5.58ms | 0.7× | 5.8% | PASS |
| mem_reads | `index_join_scan` | 2.20ms | 4.12ms | 1.9× | 3.2% | PASS |
| mem_reads | `types_table_scan` | 711.93ms | 755.42ms | 1.1× | 2.4% | PASS |
| mem_reads | `table_scan` | 851.26ms | 865.52ms | 1.0× | 3.3% | PASS |
| mem_reads | `oltp_read_only` | 82.47ms | 74.87ms | 0.9× | 2.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 142.19ms | 209.61ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 10.42ms | 22.01ms | 2.1× | 1.7% | PASS |
| mem_writes | `oltp_update_index` | 44.64ms | 91.07ms | 2.0× | 3.3% | PASS |
| mem_writes | `oltp_update_non_index` | 35.52ms | 54.29ms | 1.5× | 3.2% | PASS |
| mem_writes | `oltp_delete_insert` | 35.84ms | 65.98ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 19.17ms | 40.00ms | 2.1× | 2.2% | PASS |
| mem_writes | `types_delete_insert` | 26.59ms | 33.78ms | 1.3× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 60.62ms | 85.54ms | 1.4× | 3.8% | PASS |
| file_reads | `oltp_point_select` | 82.35ms | 38.56ms | 0.5× | 2.4% | PASS |
| file_reads | `oltp_range_select` | 17.07ms | 10.47ms | 0.6× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 16.08ms | 10.27ms | 0.6× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 2.91ms | 2.21ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 3.39ms | 2.71ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 8.46ms | 5.76ms | 0.7× | 2.6% | PASS |
| file_reads | `select_random_points` | 21.38ms | 14.96ms | 0.7× | 2.3% | PASS |
| file_reads | `select_random_ranges` | 8.17ms | 5.12ms | 0.6× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 8.54ms | 4.83ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 22.32ms | 20.41ms | 0.9× | 1.5% | PASS |
| file_reads | `index_join` | 11.78ms | 7.64ms | 0.6× | 2.4% | PASS |
| file_reads | `index_join_scan` | 3.41ms | 4.53ms | 1.3× | 2.1% | PASS |
| file_reads | `types_table_scan` | 795.66ms | 775.98ms | 1.0× | 2.8% | PASS |
| file_reads | `table_scan` | 974.88ms | 906.94ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_read_only` | 180.01ms | 102.68ms | 0.6× | 1.5% | PASS |
| file_writes | `oltp_bulk_insert` | 211.91ms | 278.52ms | 1.3× | 3.6% | PASS |
| file_writes | `oltp_insert` | 19.85ms | 40.13ms | 2.0× | 11.4% | PASS |
| file_writes | `oltp_update_index` | 161.63ms | 172.16ms | 1.1× | 5.3% | PASS |
| file_writes | `oltp_update_non_index` | 138.13ms | 113.17ms | 0.8× | 4.4% | PASS |
| file_writes | `oltp_delete_insert` | 171.70ms | 134.06ms | 0.8× | 4.9% | PASS |
| file_writes | `oltp_write_only` | 111.93ms | 89.08ms | 0.8× | 2.4% | PASS |
| file_writes | `types_delete_insert` | 131.50ms | 69.65ms | 0.5× | 6.2% | PASS |
| file_writes | `oltp_read_write` | 143.38ms | 138.45ms | 1.0× | 3.1% | PASS |
| ac_reads | `oltp_point_select` | 42.03ms | 38.45ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_range_select` | 13.23ms | 10.34ms | 0.8× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 12.86ms | 10.36ms | 0.8× | 3.3% | PASS |
| ac_reads | `oltp_order_range` | 2.55ms | 2.23ms | 0.9× | 2.7% | PASS |
| ac_reads | `oltp_distinct_range` | 3.09ms | 2.72ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 4.91ms | 5.78ms | 1.2× | 2.1% | PASS |
| ac_reads | `select_random_points` | 17.32ms | 14.68ms | 0.8× | 3.7% | PASS |
| ac_reads | `select_random_ranges` | 4.24ms | 5.03ms | 1.2× | 1.7% | PASS |
| ac_reads | `covering_index_scan` | 4.30ms | 4.40ms | 1.0× | 2.8% | PASS |
| ac_reads | `groupby_scan` | 20.58ms | 19.70ms | 1.0× | 1.9% | PASS |
| ac_reads | `index_join` | 9.34ms | 7.32ms | 0.8× | 4.2% | PASS |
| ac_reads | `index_join_scan` | 2.99ms | 4.34ms | 1.4× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 716.77ms | 759.06ms | 1.1× | 1.2% | PASS |
| ac_reads | `table_scan` | 815.27ms | 849.74ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_read_only` | 104.08ms | 94.67ms | 0.9× | 2.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.94ms | 53.64ms | 2.4× | 5.3% | PASS |
| ac_writes | `oltp_insert_ac` | 22.95ms | 58.85ms | 2.6× | 4.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.31ms | 73.25ms | 3.0× | 5.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.73ms | 60.23ms | 2.8× | 6.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.60ms | 66.77ms | 2.8× | 7.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.02ms | 65.09ms | 2.8× | 7.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.42ms | 58.84ms | 2.6× | 4.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.58ms | 71.28ms | 2.6× | 7.5% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 28.61ms | 28.51ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 14.07ms | 10.97ms | 0.8× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 12.79ms | 10.62ms | 0.8× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 2.83ms | 2.58ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.57ms | 3.42ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.22ms | 4.72ms | 1.5× | 1.9% | PASS |
| mem_reads | `select_random_points` | 17.31ms | 16.02ms | 0.9× | 2.9% | PASS |
| mem_reads | `select_random_ranges` | 2.81ms | 4.28ms | 1.5× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 3.33ms | 3.84ms | 1.2× | 2.6% | PASS |
| mem_reads | `groupby_scan` | 28.89ms | 28.75ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 8.58ms | 7.26ms | 0.8× | 2.5% | PASS |
| mem_reads | `index_join_scan` | 3.09ms | 4.77ms | 1.5× | 1.7% | PASS |
| mem_reads | `types_table_scan` | 888.46ms | 964.60ms | 1.1× | 1.1% | PASS |
| mem_reads | `table_scan` | 1.01s | 1.06s | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 110.73ms | 103.97ms | 0.9× | 1.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 189.04ms | 267.94ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 14.55ms | 28.47ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 51.51ms | 107.12ms | 2.1× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 39.39ms | 62.85ms | 1.6× | 1.4% | PASS |
| mem_writes | `oltp_delete_insert` | 41.52ms | 77.35ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 22.08ms | 45.91ms | 2.1× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 30.07ms | 38.33ms | 1.3× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 77.64ms | 110.12ms | 1.4× | 2.4% | PASS |
| file_reads | `oltp_point_select` | 105.32ms | 46.81ms | 0.4× | 0.6% | PASS |
| file_reads | `oltp_range_select` | 21.96ms | 12.75ms | 0.6× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 20.62ms | 12.45ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_order_range` | 3.64ms | 2.79ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.42ms | 3.67ms | 0.8× | 0.7% | PASS |
| file_reads | `oltp_index_scan` | 10.99ms | 6.80ms | 0.6× | 0.6% | PASS |
| file_reads | `select_random_points` | 25.88ms | 18.13ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_ranges` | 10.41ms | 6.15ms | 0.6× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.28ms | 5.82ms | 0.5× | 0.6% | PASS |
| file_reads | `groupby_scan` | 29.92ms | 29.14ms | 1.0× | 1.2% | PASS |
| file_reads | `index_join` | 13.27ms | 8.86ms | 0.7× | 1.0% | PASS |
| file_reads | `index_join_scan` | 4.01ms | 5.06ms | 1.3× | 1.1% | PASS |
| file_reads | `types_table_scan` | 905.32ms | 962.82ms | 1.1× | 1.0% | PASS |
| file_reads | `table_scan` | 1.00s | 1.06s | 1.1× | 1.0% | PASS |
| file_reads | `oltp_read_only` | 215.38ms | 129.01ms | 0.6× | 0.5% | PASS |
| file_writes | `oltp_bulk_insert` | 261.71ms | 333.26ms | 1.3× | 6.0% | PASS |
| file_writes | `oltp_insert` | 33.18ms | 54.19ms | 1.6× | 8.2% | PASS |
| file_writes | `oltp_update_index` | 164.79ms | 186.14ms | 1.1× | 3.0% | PASS |
| file_writes | `oltp_update_non_index` | 166.46ms | 133.45ms | 0.8× | 24.1% | PASS |
| file_writes | `oltp_delete_insert` | 175.14ms | 161.57ms | 0.9× | 12.8% | PASS |
| file_writes | `oltp_write_only` | 118.67ms | 99.28ms | 0.8× | 22.1% | PASS |
| file_writes | `types_delete_insert` | 123.28ms | 81.46ms | 0.7× | 8.0% | PASS |
| file_writes | `oltp_read_write` | 176.61ms | 167.34ms | 0.9× | 6.8% | PASS |
| ac_reads | `oltp_point_select` | 53.92ms | 47.05ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 16.86ms | 12.77ms | 0.8× | 0.9% | PASS |
| ac_reads | `oltp_sum_range` | 15.29ms | 12.39ms | 0.8× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 3.15ms | 2.80ms | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_distinct_range` | 3.93ms | 3.66ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 6.09ms | 6.84ms | 1.1× | 0.8% | PASS |
| ac_reads | `select_random_points` | 20.80ms | 18.28ms | 0.9× | 1.6% | PASS |
| ac_reads | `select_random_ranges` | 5.40ms | 6.16ms | 1.1× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 6.21ms | 5.78ms | 0.9× | 0.5% | PASS |
| ac_reads | `groupby_scan` | 29.22ms | 29.02ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 10.89ms | 8.90ms | 0.8× | 1.0% | PASS |
| ac_reads | `index_join_scan` | 3.59ms | 5.07ms | 1.4× | 1.2% | PASS |
| ac_reads | `types_table_scan` | 975.16ms | 999.07ms | 1.0× | 3.5% | PASS |
| ac_reads | `table_scan` | 1.06s | 1.09s | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_read_only` | 151.55ms | 132.47ms | 0.9× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.50ms | 51.75ms | 2.7× | 15.4% | PASS |
| ac_writes | `oltp_insert_ac` | 22.11ms | 68.77ms | 3.1× | 22.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 22.41ms | 74.61ms | 3.3× | 17.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.92ms | 57.12ms | 3.2× | 11.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.01ms | 71.98ms | 3.1× | 17.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.39ms | 77.62ms | 3.1× | 31.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.23ms | 71.49ms | 3.5× | 45.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.46ms | 88.39ms | 3.2× | 32.8% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 22.37ms | 24.55ms | 1.1× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 12.20ms | 11.71ms | 1.0× | 3.4% | PASS |
| mem_reads | `oltp_sum_range` | 10.95ms | 11.54ms | 1.1× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 2.15ms | 2.11ms | 1.0× | 2.1% | PASS |
| mem_reads | `oltp_distinct_range` | 2.59ms | 2.61ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_index_scan` | 2.79ms | 3.54ms | 1.3× | 3.6% | PASS |
| mem_reads | `select_random_points` | 17.00ms | 18.53ms | 1.1× | 2.2% | PASS |
| mem_reads | `select_random_ranges` | 4.41ms | 4.85ms | 1.1× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 2.32ms | 2.52ms | 1.1× | 2.2% | PASS |
| mem_reads | `groupby_scan` | 20.17ms | 20.99ms | 1.0× | 1.5% | PASS |
| mem_reads | `index_join` | 4.69ms | 6.06ms | 1.3× | 3.0% | PASS |
| mem_reads | `index_join_scan` | 2.79ms | 4.34ms | 1.6× | 3.1% | PASS |
| mem_reads | `types_table_scan` | 676.78ms | 717.36ms | 1.1× | 1.5% | PASS |
| mem_reads | `table_scan` | 882.72ms | 831.43ms | 0.9× | 6.0% | PASS |
| mem_reads | `oltp_read_only` | 85.91ms | 87.19ms | 1.0× | 2.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 143.31ms | 197.34ms | 1.4× | 1.5% | PASS |
| mem_writes | `oltp_insert` | 11.40ms | 20.42ms | 1.8× | 2.1% | PASS |
| mem_writes | `oltp_update_index` | 41.31ms | 76.77ms | 1.9× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 30.83ms | 48.22ms | 1.6× | 2.2% | PASS |
| mem_writes | `oltp_delete_insert` | 29.24ms | 54.40ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 16.11ms | 33.83ms | 2.1× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 19.93ms | 30.92ms | 1.6× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 54.44ms | 84.10ms | 1.5× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 75.53ms | 37.50ms | 0.5× | 1.4% | PASS |
| file_reads | `oltp_range_select` | 17.05ms | 12.44ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 15.84ms | 12.31ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 2.87ms | 2.41ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_distinct_range` | 3.41ms | 2.96ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 8.87ms | 5.47ms | 0.6× | 2.6% | PASS |
| file_reads | `select_random_points` | 22.61ms | 19.85ms | 0.9× | 1.6% | PASS |
| file_reads | `select_random_ranges` | 10.05ms | 6.49ms | 0.6× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 8.42ms | 4.53ms | 0.5× | 2.1% | PASS |
| file_reads | `groupby_scan` | 20.88ms | 21.21ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 8.21ms | 7.89ms | 1.0× | 3.7% | PASS |
| file_reads | `index_join_scan` | 3.42ms | 4.18ms | 1.2× | 4.3% | PASS |
| file_reads | `types_table_scan` | 739.12ms | 740.43ms | 1.0× | 3.0% | PASS |
| file_reads | `table_scan` | 934.93ms | 832.73ms | 0.9× | 3.8% | PASS |
| file_reads | `oltp_read_only` | 160.68ms | 105.32ms | 0.7× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 190.21ms | 243.26ms | 1.3× | 2.2% | PASS |
| file_writes | `oltp_insert` | 19.48ms | 29.67ms | 1.5× | 5.2% | PASS |
| file_writes | `oltp_update_index` | 141.32ms | 134.35ms | 1.0× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 120.79ms | 95.61ms | 0.8× | 3.9% | PASS |
| file_writes | `oltp_delete_insert` | 111.20ms | 99.88ms | 0.9× | 2.5% | PASS |
| file_writes | `oltp_write_only` | 82.98ms | 79.11ms | 1.0× | 5.3% | PASS |
| file_writes | `types_delete_insert` | 73.69ms | 54.43ms | 0.7× | 4.9% | PASS |
| file_writes | `oltp_read_write` | 128.70ms | 129.36ms | 1.0× | 3.5% | PASS |
| ac_reads | `oltp_point_select` | 38.76ms | 37.62ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_range_select` | 13.22ms | 12.55ms | 0.9× | 2.4% | PASS |
| ac_reads | `oltp_sum_range` | 12.19ms | 12.43ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 2.52ms | 2.36ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_distinct_range` | 2.98ms | 2.83ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 5.31ms | 5.69ms | 1.1× | 1.9% | PASS |
| ac_reads | `select_random_points` | 19.79ms | 20.62ms | 1.0× | 2.3% | PASS |
| ac_reads | `select_random_ranges` | 6.53ms | 6.61ms | 1.0× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 4.92ms | 4.66ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 21.38ms | 22.11ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 6.34ms | 7.82ms | 1.2× | 3.0% | PASS |
| ac_reads | `index_join_scan` | 3.15ms | 4.10ms | 1.3× | 5.1% | PASS |
| ac_reads | `types_table_scan` | 679.23ms | 711.97ms | 1.0× | 0.8% | PASS |
| ac_reads | `table_scan` | 826.06ms | 810.85ms | 1.0× | 2.8% | PASS |
| ac_reads | `oltp_read_only` | 110.05ms | 107.86ms | 1.0× | 2.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 18.80ms | 48.18ms | 2.6× | 3.6% | PASS |
| ac_writes | `oltp_insert_ac` | 22.81ms | 58.95ms | 2.6× | 4.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.53ms | 67.01ms | 2.7× | 5.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 19.27ms | 54.74ms | 2.8× | 3.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.13ms | 62.68ms | 2.8× | 4.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.06ms | 61.51ms | 2.8× | 3.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.03ms | 53.93ms | 2.6× | 6.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 24.04ms | 68.16ms | 2.8× | 5.1% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 21s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 106.32ms | 130.00ms | 81.8% | 0.2% | PASS |
| `status_dirty_many_tables` | 110.33ms | 130.00ms | 84.9% | 0.2% | PASS |
| `diff_regular_working_one_table` | 101.73ms | 120.00ms | 84.8% | 0.2% | PASS |
| `diff_regular_working_many_tables` | 115.31ms | 140.00ms | 82.4% | 0.2% | PASS |
| `diff_stat_working_many_tables` | 115.17ms | 140.00ms | 82.3% | 0.2% | PASS |
| `diff_schema_working_many_tables` | 116.44ms | 140.00ms | 83.2% | 0.3% | PASS |
| `branch_list_many_branches` | 24.61ms | 35.00ms | 70.3% | 0.8% | PASS |
| `branch_create_delete` | 26.71ms | 40.00ms | 66.8% | 0.8% | PASS |
| `checkout_branch_clean` | 64.89ms | 150.00ms | 43.3% | 0.4% | PASS |
| `merge_data_no_conflicts` | 30.93ms | 50.00ms | 61.9% | 0.5% | PASS |
| `merge_schema_no_conflicts` | 22.64ms | 35.00ms | 64.7% | 0.9% | PASS |
| `merge_data_conflicts` | 34.96ms | 180.00ms | 19.4% | 0.7% | PASS |
| `merge_data_conflicts_with_resolve` | 35.13ms | 180.00ms | 19.5% | 0.5% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
