# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-06 17:44 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260927.320.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/37493206663)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.64s | 8.97s | 1.0× | 1.6% | **PASS** |
| Writes | 1.77s | 2.83s | 1.6× | 1.5% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.77s | 9.26s | 0.9× | 1.3% | **PASS** |
| Writes | 4.04s | 4.16s | 1.0× | 5.1% | **PASS** |
| Autocommit writes | 856.19ms | 2.65s | 3.1× | 12.3% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.03s | 2.13s | 1.0× | 1.5% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.75s | 2.80s | 1.0× | 1.6% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.12s | 2.24s | 1.1× | 1.1% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 1.74s | 1.80s | 1.0× | 2.4% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 349.85ms | 541.11ms | 1.5× | 1.5% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 607.31ms | 1.01s | 1.7× | 1.6% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 462.18ms | 727.93ms | 1.6× | 1.2% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 351.24ms | 550.42ms | 1.6× | 1.5% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.29s | 2.21s | 1.0× | 1.0% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.18s | 2.92s | 0.9× | 1.5% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.36s | 2.29s | 1.0× | 1.0% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 1.93s | 1.85s | 1.0× | 1.7% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 954.87ms | 887.04ms | 0.9× | 12.6% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.01s | 1.17s | 1.2× | 3.6% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.18s | 1.18s | 1.0× | 4.1% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 899.16ms | 915.45ms | 1.0× | 4.3% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.15s | 2.20s | 1.0× | 1.2% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.76s | 2.85s | 1.0× | 1.7% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.21s | 2.30s | 1.0× | 0.9% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 1.81s | 1.86s | 1.0× | 2.1% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 233.30ms | 828.29ms | 3.6× | 47.7% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 222.09ms | 660.38ms | 3.0× | 6.4% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 191.35ms | 610.41ms | 3.2× | 19.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 209.45ms | 548.84ms | 2.6× | 5.8% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 19.93ms | 22.55ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_range_select` | 8.90ms | 8.89ms | 1.0× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 8.12ms | 8.81ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 2.16ms | 2.28ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.02ms | 3.17ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.18ms | 4.10ms | 1.3× | 1.8% | PASS |
| mem_reads | `select_random_points` | 9.01ms | 9.33ms | 1.0× | 2.5% | PASS |
| mem_reads | `select_random_ranges` | 2.54ms | 3.25ms | 1.3× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 3.33ms | 3.49ms | 1.0× | 1.8% | PASS |
| mem_reads | `groupby_scan` | 25.74ms | 27.65ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 4.61ms | 6.34ms | 1.4× | 2.6% | PASS |
| mem_reads | `index_join_scan` | 2.63ms | 3.74ms | 1.4× | 1.3% | PASS |
| mem_reads | `types_table_scan` | 864.91ms | 923.36ms | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 991.47ms | 1.01s | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 83.41ms | 90.76ms | 1.1× | 1.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 140.95ms | 200.80ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 12.21ms | 20.93ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_update_index` | 40.21ms | 72.78ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 28.98ms | 43.99ms | 1.5× | 2.3% | PASS |
| mem_writes | `oltp_delete_insert` | 35.86ms | 54.70ms | 1.5× | 1.9% | PASS |
| mem_writes | `oltp_write_only` | 17.70ms | 35.12ms | 2.0× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 19.71ms | 27.35ms | 1.4× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 54.22ms | 85.44ms | 1.6× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 95.23ms | 40.78ms | 0.4× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 16.92ms | 10.78ms | 0.6× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 15.96ms | 10.70ms | 0.7× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 3.04ms | 2.53ms | 0.8× | 0.6% | PASS |
| file_reads | `oltp_distinct_range` | 3.85ms | 3.41ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 11.06ms | 6.19ms | 0.6× | 0.8% | PASS |
| file_reads | `select_random_points` | 17.09ms | 11.40ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_ranges` | 10.15ms | 5.08ms | 0.5× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.37ms | 5.57ms | 0.5× | 0.8% | PASS |
| file_reads | `groupby_scan` | 26.72ms | 28.07ms | 1.1× | 1.1% | PASS |
| file_reads | `index_join` | 8.99ms | 7.96ms | 0.9× | 1.6% | PASS |
| file_reads | `index_join_scan` | 3.61ms | 4.21ms | 1.2× | 2.1% | PASS |
| file_reads | `types_table_scan` | 873.62ms | 926.45ms | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 1.00s | 1.03s | 1.0× | 1.9% | PASS |
| file_reads | `oltp_read_only` | 189.44ms | 115.89ms | 0.6× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 202.09ms | 251.07ms | 1.2× | 10.0% | PASS |
| file_writes | `oltp_insert` | 27.87ms | 35.02ms | 1.3× | 14.8% | PASS |
| file_writes | `oltp_update_index` | 147.23ms | 131.40ms | 0.9× | 16.5% | PASS |
| file_writes | `oltp_update_non_index` | 114.81ms | 90.48ms | 0.8× | 7.9% | PASS |
| file_writes | `oltp_delete_insert` | 121.04ms | 105.14ms | 0.9× | 10.5% | PASS |
| file_writes | `oltp_write_only` | 88.92ms | 75.05ms | 0.8× | 5.1% | PASS |
| file_writes | `types_delete_insert` | 80.06ms | 65.87ms | 0.8× | 19.6% | PASS |
| file_writes | `oltp_read_write` | 172.84ms | 133.02ms | 0.8× | 30.0% | PASS |
| ac_reads | `oltp_point_select` | 44.22ms | 40.38ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 11.83ms | 10.76ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 10.86ms | 10.67ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 2.57ms | 2.52ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_distinct_range` | 3.33ms | 3.41ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 5.94ms | 6.10ms | 1.0× | 1.0% | PASS |
| ac_reads | `select_random_points` | 11.59ms | 11.09ms | 1.0× | 1.6% | PASS |
| ac_reads | `select_random_ranges` | 5.13ms | 5.05ms | 1.0× | 0.6% | PASS |
| ac_reads | `covering_index_scan` | 6.27ms | 5.52ms | 0.9× | 0.7% | PASS |
| ac_reads | `groupby_scan` | 25.87ms | 27.83ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 6.33ms | 7.78ms | 1.2× | 1.2% | PASS |
| ac_reads | `index_join_scan` | 3.13ms | 4.12ms | 1.3× | 1.5% | PASS |
| ac_reads | `types_table_scan` | 873.71ms | 923.59ms | 1.1× | 1.2% | PASS |
| ac_reads | `table_scan` | 1.02s | 1.03s | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_read_only` | 119.45ms | 116.02ms | 1.0× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.03ms | 60.99ms | 3.0× | 70.1% | PASS |
| ac_writes | `oltp_insert_ac` | 29.18ms | 78.25ms | 2.7× | 46.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 35.96ms | 144.12ms | 4.0× | 43.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 31.69ms | 106.10ms | 3.3× | 63.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 41.88ms | 168.91ms | 4.0× | 73.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.00ms | 104.52ms | 4.2× | 48.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 19.55ms | 79.64ms | 4.1× | 34.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 30.02ms | 85.75ms | 2.9× | 39.7% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.25ms | 38.32ms | 1.1× | 2.6% | PASS |
| mem_reads | `oltp_range_select` | 17.75ms | 14.26ms | 0.8× | 1.9% | PASS |
| mem_reads | `oltp_sum_range` | 16.55ms | 14.11ms | 0.9× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 3.38ms | 3.18ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.44ms | 4.31ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.09ms | 6.40ms | 1.6× | 1.4% | PASS |
| mem_reads | `select_random_points` | 24.38ms | 21.59ms | 0.9× | 2.9% | PASS |
| mem_reads | `select_random_ranges` | 3.73ms | 5.37ms | 1.4× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 4.24ms | 4.84ms | 1.1× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 35.55ms | 34.91ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 11.12ms | 9.78ms | 0.9× | 2.9% | PASS |
| mem_reads | `index_join_scan` | 3.98ms | 5.52ms | 1.4× | 2.6% | PASS |
| mem_reads | `types_table_scan` | 1.22s | 1.22s | 1.0× | 1.5% | PASS |
| mem_reads | `table_scan` | 1.22s | 1.28s | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 144.13ms | 138.69ms | 1.0× | 2.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 245.01ms | 370.88ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 18.22ms | 38.68ms | 2.1× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 65.59ms | 142.51ms | 2.2× | 2.0% | PASS |
| mem_writes | `oltp_update_non_index` | 50.38ms | 87.51ms | 1.7× | 2.1% | PASS |
| mem_writes | `oltp_delete_insert` | 55.62ms | 105.61ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 28.54ms | 62.64ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 40.45ms | 54.72ms | 1.4× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 103.50ms | 150.56ms | 1.5× | 2.3% | PASS |
| file_reads | `oltp_point_select` | 111.19ms | 60.04ms | 0.5× | 1.4% | PASS |
| file_reads | `oltp_range_select` | 24.46ms | 16.87ms | 0.7× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 23.83ms | 16.77ms | 0.7× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 4.11ms | 3.48ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 5.25ms | 4.64ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 11.93ms | 8.75ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 31.49ms | 25.11ms | 0.8× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 11.10ms | 7.56ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 11.83ms | 7.04ms | 0.6× | 1.1% | PASS |
| file_reads | `groupby_scan` | 35.35ms | 35.22ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 14.65ms | 11.62ms | 0.8× | 1.6% | PASS |
| file_reads | `index_join_scan` | 4.71ms | 6.23ms | 1.3× | 2.7% | PASS |
| file_reads | `types_table_scan` | 1.23s | 1.22s | 1.0× | 2.8% | PASS |
| file_reads | `table_scan` | 1.38s | 1.31s | 1.0× | 1.5% | PASS |
| file_reads | `oltp_read_only` | 279.44ms | 181.96ms | 0.7× | 0.6% | PASS |
| file_writes | `oltp_bulk_insert` | 274.35ms | 396.50ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 28.10ms | 47.67ms | 1.7× | 2.1% | PASS |
| file_writes | `oltp_update_index` | 133.99ms | 179.71ms | 1.3× | 7.4% | PASS |
| file_writes | `oltp_update_non_index` | 125.74ms | 107.68ms | 0.9× | 6.3% | PASS |
| file_writes | `oltp_delete_insert` | 106.31ms | 128.02ms | 1.2× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 86.42ms | 78.53ms | 0.9× | 8.7% | PASS |
| file_writes | `types_delete_insert` | 77.86ms | 66.78ms | 0.9× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 180.94ms | 169.52ms | 0.9× | 5.2% | PASS |
| ac_reads | `oltp_point_select` | 66.57ms | 61.17ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 21.55ms | 16.68ms | 0.8× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 20.41ms | 16.68ms | 0.8× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.87ms | 3.50ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.92ms | 4.67ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 7.15ms | 8.68ms | 1.2× | 1.0% | PASS |
| ac_reads | `select_random_points` | 25.91ms | 24.79ms | 1.0× | 4.5% | PASS |
| ac_reads | `select_random_ranges` | 6.06ms | 7.46ms | 1.2× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 6.89ms | 6.97ms | 1.0× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 34.94ms | 35.10ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 13.08ms | 11.59ms | 0.9× | 3.7% | PASS |
| ac_reads | `index_join_scan` | 4.33ms | 6.20ms | 1.4× | 5.9% | PASS |
| ac_reads | `types_table_scan` | 1.08s | 1.17s | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 1.26s | 1.29s | 1.0× | 3.3% | PASS |
| ac_reads | `oltp_read_only` | 194.89ms | 178.53ms | 0.9× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 25.17ms | 66.06ms | 2.6× | 6.7% | PASS |
| ac_writes | `oltp_insert_ac` | 27.51ms | 78.98ms | 2.9× | 5.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.75ms | 94.89ms | 3.3× | 6.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.91ms | 76.19ms | 3.1× | 7.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.98ms | 87.91ms | 3.1× | 5.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.56ms | 88.72ms | 3.2× | 6.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 26.57ms | 77.94ms | 2.9× | 9.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.64ms | 89.69ms | 2.7× | 3.5% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.75ms | 28.11ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 13.76ms | 10.92ms | 0.8× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 12.46ms | 10.58ms | 0.8× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 2.74ms | 2.55ms | 0.9× | 1.6% | PASS |
| mem_reads | `oltp_distinct_range` | 3.57ms | 3.42ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.22ms | 4.64ms | 1.4× | 1.1% | PASS |
| mem_reads | `select_random_points` | 17.06ms | 16.10ms | 0.9× | 1.7% | PASS |
| mem_reads | `select_random_ranges` | 2.79ms | 4.29ms | 1.5× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 3.33ms | 3.59ms | 1.1× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 28.86ms | 28.65ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 8.64ms | 7.16ms | 0.8× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.12ms | 4.79ms | 1.5× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 884.03ms | 957.05ms | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 996.40ms | 1.05s | 1.1× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 108.11ms | 102.72ms | 1.0× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 188.04ms | 266.51ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 14.59ms | 28.13ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 51.28ms | 104.12ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 38.77ms | 61.35ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 42.06ms | 77.88ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 22.54ms | 45.97ms | 2.0× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 30.21ms | 37.83ms | 1.3× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 74.69ms | 106.14ms | 1.4× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 103.17ms | 46.11ms | 0.4× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 21.56ms | 12.75ms | 0.6× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 20.36ms | 12.50ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.58ms | 2.81ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_distinct_range` | 4.43ms | 3.67ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 10.93ms | 6.85ms | 0.6× | 0.9% | PASS |
| file_reads | `select_random_points` | 25.21ms | 17.96ms | 0.7× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 10.35ms | 6.15ms | 0.6× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 11.16ms | 5.83ms | 0.5× | 0.7% | PASS |
| file_reads | `groupby_scan` | 29.70ms | 29.06ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join` | 13.21ms | 8.89ms | 0.7× | 1.2% | PASS |
| file_reads | `index_join_scan` | 4.02ms | 5.00ms | 1.2× | 1.7% | PASS |
| file_reads | `types_table_scan` | 886.69ms | 951.87ms | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 998.90ms | 1.05s | 1.1× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 217.39ms | 128.98ms | 0.6× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 256.93ms | 331.38ms | 1.3× | 3.5% | PASS |
| file_writes | `oltp_insert` | 32.36ms | 54.91ms | 1.7× | 9.0% | PASS |
| file_writes | `oltp_update_index` | 164.82ms | 183.66ms | 1.1× | 2.4% | PASS |
| file_writes | `oltp_update_non_index` | 140.58ms | 121.42ms | 0.9× | 5.4% | PASS |
| file_writes | `oltp_delete_insert` | 164.90ms | 145.73ms | 0.9× | 3.7% | PASS |
| file_writes | `oltp_write_only` | 118.40ms | 99.43ms | 0.8× | 3.9% | PASS |
| file_writes | `types_delete_insert` | 124.27ms | 83.29ms | 0.7× | 5.2% | PASS |
| file_writes | `oltp_read_write` | 172.74ms | 161.26ms | 0.9× | 4.3% | PASS |
| ac_reads | `oltp_point_select` | 53.10ms | 46.04ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 16.86ms | 12.77ms | 0.8× | 0.8% | PASS |
| ac_reads | `oltp_sum_range` | 15.57ms | 12.48ms | 0.8× | 1.0% | PASS |
| ac_reads | `oltp_order_range` | 3.20ms | 2.81ms | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_distinct_range` | 3.98ms | 3.68ms | 0.9× | 0.6% | PASS |
| ac_reads | `oltp_index_scan` | 6.13ms | 6.88ms | 1.1× | 1.0% | PASS |
| ac_reads | `select_random_points` | 20.52ms | 18.06ms | 0.9× | 1.5% | PASS |
| ac_reads | `select_random_ranges` | 5.43ms | 6.15ms | 1.1× | 0.4% | PASS |
| ac_reads | `covering_index_scan` | 6.25ms | 5.81ms | 0.9× | 0.8% | PASS |
| ac_reads | `groupby_scan` | 29.36ms | 29.13ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 10.91ms | 8.89ms | 0.8× | 1.2% | PASS |
| ac_reads | `index_join_scan` | 3.69ms | 5.03ms | 1.4× | 1.3% | PASS |
| ac_reads | `types_table_scan` | 887.95ms | 953.24ms | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 998.78ms | 1.06s | 1.1× | 0.6% | PASS |
| ac_reads | `oltp_read_only` | 144.86ms | 129.08ms | 0.9× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.23ms | 54.13ms | 2.5× | 15.0% | PASS |
| ac_writes | `oltp_insert_ac` | 23.01ms | 79.11ms | 3.4× | 21.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.14ms | 78.17ms | 3.2× | 17.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 19.26ms | 61.26ms | 3.2× | 18.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.68ms | 73.85ms | 3.1× | 19.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.85ms | 91.05ms | 3.5× | 27.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.33ms | 68.96ms | 3.1× | 16.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.85ms | 103.87ms | 3.3× | 42.3% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 21.62ms | 24.11ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_range_select` | 11.72ms | 11.62ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_sum_range` | 9.99ms | 10.98ms | 1.1× | 2.6% | PASS |
| mem_reads | `oltp_order_range` | 2.27ms | 2.24ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_distinct_range` | 2.90ms | 2.87ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_index_scan` | 3.22ms | 4.58ms | 1.4× | 4.9% | PASS |
| mem_reads | `select_random_points` | 19.60ms | 21.29ms | 1.1× | 2.9% | PASS |
| mem_reads | `select_random_ranges` | 4.74ms | 5.27ms | 1.1× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 2.35ms | 2.59ms | 1.1× | 5.8% | PASS |
| mem_reads | `groupby_scan` | 20.27ms | 21.21ms | 1.0× | 1.2% | PASS |
| mem_reads | `index_join` | 4.94ms | 6.91ms | 1.4× | 4.9% | PASS |
| mem_reads | `index_join_scan` | 3.02ms | 4.76ms | 1.6× | 3.4% | PASS |
| mem_reads | `types_table_scan` | 764.31ms | 771.96ms | 1.0× | 3.8% | PASS |
| mem_reads | `table_scan` | 794.78ms | 820.12ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_read_only` | 77.72ms | 87.45ms | 1.1× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 148.35ms | 196.30ms | 1.3× | 1.4% | PASS |
| mem_writes | `oltp_insert` | 11.37ms | 20.32ms | 1.8× | 1.6% | PASS |
| mem_writes | `oltp_update_index` | 39.73ms | 74.02ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 31.20ms | 49.64ms | 1.6× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 29.87ms | 56.99ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 16.55ms | 35.96ms | 2.2× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 20.30ms | 30.81ms | 1.5× | 2.8% | PASS |
| mem_writes | `oltp_read_write` | 53.87ms | 86.39ms | 1.6× | 1.3% | PASS |
| file_reads | `oltp_point_select` | 77.86ms | 38.11ms | 0.5× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 17.15ms | 12.95ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 16.11ms | 12.67ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 2.87ms | 2.40ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 3.41ms | 2.95ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 8.96ms | 5.17ms | 0.6× | 1.8% | PASS |
| file_reads | `select_random_points` | 24.28ms | 21.39ms | 0.9× | 2.4% | PASS |
| file_reads | `select_random_ranges` | 10.68ms | 6.90ms | 0.6× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 8.93ms | 4.77ms | 0.5× | 2.0% | PASS |
| file_reads | `groupby_scan` | 21.73ms | 22.20ms | 1.0× | 1.6% | PASS |
| file_reads | `index_join` | 8.41ms | 7.99ms | 1.0× | 3.6% | PASS |
| file_reads | `index_join_scan` | 3.62ms | 4.36ms | 1.2× | 4.0% | PASS |
| file_reads | `types_table_scan` | 735.40ms | 758.42ms | 1.0× | 1.7% | PASS |
| file_reads | `table_scan` | 828.15ms | 835.16ms | 1.0× | 3.8% | PASS |
| file_reads | `oltp_read_only` | 164.20ms | 110.13ms | 0.7× | 2.1% | PASS |
| file_writes | `oltp_bulk_insert` | 195.32ms | 244.59ms | 1.3× | 4.3% | PASS |
| file_writes | `oltp_insert` | 22.02ms | 34.83ms | 1.6× | 14.7% | PASS |
| file_writes | `oltp_update_index` | 151.36ms | 149.38ms | 1.0× | 4.2% | PASS |
| file_writes | `oltp_update_non_index` | 124.13ms | 99.40ms | 0.8× | 4.4% | PASS |
| file_writes | `oltp_delete_insert` | 121.36ms | 109.59ms | 0.9× | 4.3% | PASS |
| file_writes | `oltp_write_only` | 83.28ms | 79.35ms | 1.0× | 2.6% | PASS |
| file_writes | `types_delete_insert` | 80.04ms | 68.97ms | 0.9× | 8.5% | PASS |
| file_writes | `oltp_read_write` | 121.66ms | 129.35ms | 1.1× | 2.8% | PASS |
| ac_reads | `oltp_point_select` | 40.04ms | 39.08ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_range_select` | 12.99ms | 12.95ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 12.52ms | 13.02ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_order_range` | 2.62ms | 2.48ms | 0.9× | 2.4% | PASS |
| ac_reads | `oltp_distinct_range` | 3.15ms | 3.03ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_index_scan` | 5.65ms | 5.98ms | 1.1× | 2.4% | PASS |
| ac_reads | `select_random_points` | 21.28ms | 21.77ms | 1.0× | 2.6% | PASS |
| ac_reads | `select_random_ranges` | 6.97ms | 6.97ms | 1.0× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 5.19ms | 4.87ms | 0.9× | 2.7% | PASS |
| ac_reads | `groupby_scan` | 22.17ms | 22.97ms | 1.0× | 1.6% | PASS |
| ac_reads | `index_join` | 6.69ms | 8.12ms | 1.2× | 2.8% | PASS |
| ac_reads | `index_join_scan` | 3.41ms | 5.07ms | 1.5× | 9.2% | PASS |
| ac_reads | `types_table_scan` | 756.90ms | 772.34ms | 1.0× | 4.1% | PASS |
| ac_reads | `table_scan` | 805.49ms | 828.45ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_read_only` | 108.35ms | 110.88ms | 1.0× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.98ms | 59.21ms | 2.6× | 5.6% | PASS |
| ac_writes | `oltp_insert_ac` | 26.84ms | 69.82ms | 2.6× | 5.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.34ms | 76.59ms | 2.7× | 6.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.25ms | 62.99ms | 2.8× | 2.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.05ms | 72.00ms | 2.8× | 7.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.86ms | 71.46ms | 2.7× | 4.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 26.84ms | 60.49ms | 2.3× | 9.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.29ms | 76.28ms | 2.6× | 6.0% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 34s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 55.40ms | 130.00ms | 42.6% | 0.9% | PASS |
| `status_dirty_many_tables` | 58.15ms | 130.00ms | 44.7% | 1.1% | PASS |
| `diff_regular_working_one_table` | 53.10ms | 120.00ms | 44.3% | 1.6% | PASS |
| `diff_regular_working_many_tables` | 61.26ms | 140.00ms | 43.8% | 1.1% | PASS |
| `diff_stat_working_many_tables` | 60.61ms | 140.00ms | 43.3% | 0.6% | PASS |
| `diff_schema_working_many_tables` | 61.74ms | 140.00ms | 44.1% | 1.3% | PASS |
| `branch_list_many_branches` | 15.31ms | 35.00ms | 43.7% | 4.9% | PASS |
| `branch_create_delete` | 17.09ms | 40.00ms | 42.7% | 2.3% | PASS |
| `checkout_branch_clean` | 86.07ms | 150.00ms | 57.4% | 3.8% | PASS |
| `merge_data_no_conflicts` | 24.54ms | 50.00ms | 49.1% | 2.6% | PASS |
| `merge_schema_no_conflicts` | 15.49ms | 35.00ms | 44.3% | 2.1% | PASS |
| `merge_data_conflicts` | 20.12ms | 180.00ms | 11.2% | 1.3% | PASS |
| `merge_data_conflicts_with_resolve` | 19.96ms | 180.00ms | 11.1% | 0.9% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
