# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-18 19:54 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261111.196.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/44368191228)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.02s | 10.64s | 1.1× | 1.4% | **PASS** |
| Writes | 2.08s | 3.61s | 1.7× | 1.3% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.03s | 10.90s | 1.0× | 1.4% | **PASS** |
| Writes | 3.41s | 4.25s | 1.2× | 2.1% | **PASS** |
| Autocommit writes | 805.28ms | 2.50s | 3.1× | 7.7% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.48s | 2.68s | 1.1× | 1.6% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.91s | 2.89s | 1.0× | 1.9% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.52s | 2.78s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.11s | 2.29s | 1.1× | 1.0% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 445.90ms | 775.75ms | 1.7× | 1.3% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 608.82ms | 1.07s | 1.8× | 2.1% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 577.64ms | 1.02s | 1.8× | 1.2% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 450.79ms | 735.66ms | 1.6× | 1.0% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.72s | 2.75s | 1.0× | 1.3% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.15s | 2.95s | 0.9× | 1.6% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.82s | 2.86s | 1.0× | 1.7% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.34s | 2.35s | 1.0× | 0.9% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 594.10ms | 832.31ms | 1.4× | 1.7% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 947.11ms | 1.17s | 1.2× | 4.9% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 807.72ms | 1.13s | 1.4× | 1.6% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 1.06s | 1.11s | 1.1× | 6.1% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.60s | 2.76s | 1.1× | 1.4% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 3.10s | 2.99s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.58s | 2.83s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.19s | 2.35s | 1.1× | 0.9% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 192.30ms | 594.00ms | 3.1× | 6.4% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 212.78ms | 628.37ms | 3.0× | 8.3% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 200.81ms | 593.15ms | 3.0× | 5.4% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 199.38ms | 685.03ms | 3.4× | 32.9% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.76ms | 30.28ms | 1.2× | 2.3% | PASS |
| mem_reads | `oltp_range_select` | 11.15ms | 11.71ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_sum_range` | 9.61ms | 11.35ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 2.74ms | 2.97ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.65ms | 4.06ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.90ms | 5.43ms | 1.4× | 1.9% | PASS |
| mem_reads | `select_random_points` | 9.74ms | 10.83ms | 1.1× | 2.1% | PASS |
| mem_reads | `select_random_ranges` | 3.09ms | 3.98ms | 1.3× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.16ms | 4.40ms | 1.1× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 30.17ms | 33.45ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 5.82ms | 8.24ms | 1.4× | 2.6% | PASS |
| mem_reads | `index_join_scan` | 3.23ms | 4.35ms | 1.3× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.16s | 1.1× | 1.9% | PASS |
| mem_reads | `table_scan` | 1.21s | 1.27s | 1.1× | 2.5% | PASS |
| mem_reads | `oltp_read_only` | 109.36ms | 122.51ms | 1.1× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 176.42ms | 277.96ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 15.15ms | 29.06ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 51.48ms | 106.23ms | 2.1× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 36.10ms | 66.03ms | 1.8× | 1.3% | PASS |
| mem_writes | `oltp_delete_insert` | 46.82ms | 81.97ms | 1.8× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 22.24ms | 51.60ms | 2.3× | 1.3% | FAIL |
| mem_writes | `types_delete_insert` | 25.01ms | 42.35ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 72.69ms | 120.54ms | 1.7× | 1.8% | PASS |
| file_reads | `oltp_point_select` | 95.94ms | 48.78ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 19.43ms | 14.01ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 17.67ms | 13.46ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 3.63ms | 3.23ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 4.62ms | 4.36ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 11.49ms | 7.57ms | 0.7× | 1.1% | PASS |
| file_reads | `select_random_points` | 17.95ms | 13.07ms | 0.7× | 2.2% | PASS |
| file_reads | `select_random_ranges` | 10.16ms | 5.78ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 11.63ms | 6.45ms | 0.6× | 1.0% | PASS |
| file_reads | `groupby_scan` | 31.71ms | 34.14ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 10.15ms | 9.72ms | 1.0× | 2.1% | PASS |
| file_reads | `index_join_scan` | 4.22ms | 4.82ms | 1.1× | 1.9% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.16s | 1.1× | 1.2% | PASS |
| file_reads | `table_scan` | 1.22s | 1.27s | 1.0× | 2.1% | PASS |
| file_reads | `oltp_read_only` | 212.68ms | 148.94ms | 0.7× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 189.35ms | 289.71ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_insert` | 21.63ms | 31.86ms | 1.5× | 2.1% | PASS |
| file_writes | `oltp_update_index` | 75.77ms | 113.28ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 58.23ms | 76.19ms | 1.3× | 1.3% | PASS |
| file_writes | `oltp_delete_insert` | 67.81ms | 91.18ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 45.71ms | 59.03ms | 1.3× | 2.1% | PASS |
| file_writes | `types_delete_insert` | 40.41ms | 44.80ms | 1.1× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 95.19ms | 126.26ms | 1.3× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 46.58ms | 47.75ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 13.84ms | 13.65ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 12.32ms | 13.30ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 3.14ms | 3.20ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.04ms | 4.36ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.47ms | 7.37ms | 1.1× | 1.3% | PASS |
| ac_reads | `select_random_points` | 12.58ms | 12.79ms | 1.0× | 1.8% | PASS |
| ac_reads | `select_random_ranges` | 5.50ms | 5.79ms | 1.1× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 6.86ms | 6.41ms | 0.9× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 31.08ms | 34.06ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 7.51ms | 9.56ms | 1.3× | 1.5% | PASS |
| ac_reads | `index_join_scan` | 3.75ms | 4.87ms | 1.3× | 2.3% | PASS |
| ac_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 1.6% | PASS |
| ac_reads | `table_scan` | 1.23s | 1.28s | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_read_only` | 142.20ms | 148.90ms | 1.0× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.02ms | 59.27ms | 2.8× | 6.2% | PASS |
| ac_writes | `oltp_insert_ac` | 23.96ms | 73.26ms | 3.1× | 6.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.82ms | 85.98ms | 3.2× | 6.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.40ms | 67.40ms | 3.0× | 6.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.44ms | 77.55ms | 3.3× | 5.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.44ms | 77.90ms | 3.2× | 8.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.67ms | 67.95ms | 3.3× | 7.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.55ms | 84.69ms | 2.9× | 5.8% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.56ms | 38.92ms | 1.1× | 3.1% | PASS |
| mem_reads | `oltp_range_select` | 17.00ms | 14.28ms | 0.8× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 15.29ms | 13.83ms | 0.9× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 3.38ms | 3.28ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.37ms | 4.39ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.08ms | 6.46ms | 1.6× | 2.3% | PASS |
| mem_reads | `select_random_points` | 24.19ms | 21.87ms | 0.9× | 3.4% | PASS |
| mem_reads | `select_random_ranges` | 3.58ms | 5.36ms | 1.5× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.25ms | 4.92ms | 1.2× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 35.25ms | 35.37ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 11.16ms | 10.02ms | 0.9× | 4.4% | PASS |
| mem_reads | `index_join_scan` | 3.86ms | 5.62ms | 1.5× | 3.4% | PASS |
| mem_reads | `types_table_scan` | 1.21s | 1.24s | 1.0× | 2.5% | PASS |
| mem_reads | `table_scan` | 1.39s | 1.34s | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_read_only` | 151.40ms | 145.05ms | 1.0× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 235.65ms | 380.20ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 17.73ms | 40.68ms | 2.3× | 1.5% | PASS |
| mem_writes | `oltp_update_index` | 67.95ms | 155.23ms | 2.3× | 2.3% | PASS |
| mem_writes | `oltp_update_non_index` | 51.37ms | 95.03ms | 1.8× | 2.4% | PASS |
| mem_writes | `oltp_delete_insert` | 55.81ms | 113.25ms | 2.0× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 28.84ms | 68.41ms | 2.4× | 1.9% | FAIL |
| mem_writes | `types_delete_insert` | 39.87ms | 60.54ms | 1.5× | 2.2% | PASS |
| mem_writes | `oltp_read_write` | 111.60ms | 159.02ms | 1.4× | 2.4% | PASS |
| file_reads | `oltp_point_select` | 110.24ms | 57.80ms | 0.5× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 25.94ms | 16.68ms | 0.6× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 24.00ms | 16.23ms | 0.7× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 4.42ms | 3.59ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_distinct_range` | 5.51ms | 4.75ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 11.60ms | 8.40ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_points` | 33.48ms | 24.49ms | 0.7× | 3.8% | PASS |
| file_reads | `select_random_ranges` | 10.82ms | 7.29ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 11.89ms | 6.86ms | 0.6× | 1.8% | PASS |
| file_reads | `groupby_scan` | 36.69ms | 35.90ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 16.03ms | 11.59ms | 0.7× | 2.8% | PASS |
| file_reads | `index_join_scan` | 4.95ms | 6.29ms | 1.3× | 2.6% | PASS |
| file_reads | `types_table_scan` | 1.27s | 1.25s | 1.0× | 0.8% | PASS |
| file_reads | `table_scan` | 1.33s | 1.32s | 1.0× | 2.1% | PASS |
| file_reads | `oltp_read_only` | 254.70ms | 169.02ms | 0.7× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 259.32ms | 393.93ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 25.53ms | 48.24ms | 1.9× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 131.43ms | 172.00ms | 1.3× | 11.7% | PASS |
| file_writes | `oltp_update_non_index` | 106.09ms | 111.06ms | 1.0× | 12.0% | PASS |
| file_writes | `oltp_delete_insert` | 100.22ms | 129.27ms | 1.3× | 2.3% | PASS |
| file_writes | `oltp_write_only` | 90.60ms | 80.69ms | 0.9× | 9.3% | PASS |
| file_writes | `types_delete_insert` | 74.20ms | 69.16ms | 0.9× | 1.8% | PASS |
| file_writes | `oltp_read_write` | 159.73ms | 169.83ms | 1.1× | 7.5% | PASS |
| ac_reads | `oltp_point_select` | 59.94ms | 57.11ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_range_select` | 20.28ms | 16.48ms | 0.8× | 2.4% | PASS |
| ac_reads | `oltp_sum_range` | 18.63ms | 15.96ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.86ms | 3.56ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.88ms | 4.70ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 6.73ms | 8.32ms | 1.2× | 1.4% | PASS |
| ac_reads | `select_random_points` | 25.82ms | 23.76ms | 0.9× | 3.4% | PASS |
| ac_reads | `select_random_ranges` | 5.97ms | 7.16ms | 1.2× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 6.94ms | 6.67ms | 1.0× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 35.62ms | 35.63ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 12.94ms | 11.25ms | 0.9× | 1.9% | PASS |
| ac_reads | `index_join_scan` | 4.34ms | 6.04ms | 1.4× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 1.26s | 1.26s | 1.0× | 1.5% | PASS |
| ac_reads | `table_scan` | 1.44s | 1.36s | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 192.32ms | 175.53ms | 0.9× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.10ms | 61.49ms | 2.7× | 8.5% | PASS |
| ac_writes | `oltp_insert_ac` | 27.40ms | 75.53ms | 2.8× | 8.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.53ms | 91.94ms | 3.3× | 5.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.48ms | 72.91ms | 3.0× | 8.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.38ms | 81.68ms | 3.1× | 6.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.02ms | 84.58ms | 3.0× | 8.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.57ms | 73.71ms | 3.1× | 8.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.31ms | 86.53ms | 2.7× | 4.5% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.60ms | 38.05ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 16.24ms | 13.77ms | 0.8× | 1.3% | PASS |
| mem_reads | `oltp_sum_range` | 14.36ms | 13.37ms | 0.9× | 0.9% | PASS |
| mem_reads | `oltp_order_range` | 3.32ms | 3.22ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.25ms | 4.35ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.90ms | 5.97ms | 1.5× | 2.2% | PASS |
| mem_reads | `select_random_points` | 19.96ms | 20.34ms | 1.0× | 1.6% | PASS |
| mem_reads | `select_random_ranges` | 3.24ms | 5.22ms | 1.6× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.42ms | 1.1× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 33.73ms | 34.70ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 10.04ms | 9.13ms | 0.9× | 1.8% | PASS |
| mem_reads | `index_join_scan` | 3.60ms | 5.22ms | 1.5× | 2.7% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.19s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.30s | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_read_only` | 133.44ms | 137.88ms | 1.0× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 237.56ms | 371.10ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.06ms | 39.92ms | 2.2× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 63.70ms | 145.99ms | 2.3× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 46.40ms | 88.45ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 51.30ms | 107.00ms | 2.1× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 26.96ms | 64.42ms | 2.4× | 1.3% | FAIL |
| mem_writes | `types_delete_insert` | 37.11ms | 56.66ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_read_write` | 96.56ms | 149.55ms | 1.5× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 105.73ms | 56.33ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 23.66ms | 15.75ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 22.35ms | 15.37ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 4.11ms | 3.51ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 5.09ms | 4.62ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 10.94ms | 8.11ms | 0.7× | 1.1% | PASS |
| file_reads | `select_random_points` | 28.40ms | 23.21ms | 0.8× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 10.31ms | 7.07ms | 0.7× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 11.22ms | 6.55ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 34.71ms | 35.18ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 14.10ms | 10.77ms | 0.8× | 2.1% | PASS |
| file_reads | `index_join_scan` | 4.62ms | 5.64ms | 1.2× | 2.2% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.20s | 1.1× | 1.8% | PASS |
| file_reads | `table_scan` | 1.23s | 1.30s | 1.1× | 2.7% | PASS |
| file_reads | `oltp_read_only` | 244.74ms | 165.76ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 257.13ms | 387.50ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 25.29ms | 47.55ms | 1.9× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 97.06ms | 162.90ms | 1.7× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 89.91ms | 103.97ms | 1.2× | 11.0% | PASS |
| file_writes | `oltp_delete_insert` | 88.43ms | 123.11ms | 1.4× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 57.62ms | 77.34ms | 1.3× | 1.3% | PASS |
| file_writes | `types_delete_insert` | 63.54ms | 65.67ms | 1.0× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 128.75ms | 161.59ms | 1.3× | 1.9% | PASS |
| ac_reads | `oltp_point_select` | 58.10ms | 55.98ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 19.48ms | 15.73ms | 0.8× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 17.55ms | 15.38ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.76ms | 3.47ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.74ms | 4.64ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.67ms | 8.16ms | 1.2× | 1.3% | PASS |
| ac_reads | `select_random_points` | 24.20ms | 23.49ms | 1.0× | 2.2% | PASS |
| ac_reads | `select_random_ranges` | 5.72ms | 7.10ms | 1.2× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 6.73ms | 6.58ms | 1.0× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 34.47ms | 35.17ms | 1.0× | 0.4% | PASS |
| ac_reads | `index_join` | 12.03ms | 10.80ms | 0.9× | 2.3% | PASS |
| ac_reads | `index_join_scan` | 4.18ms | 5.65ms | 1.4× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 1.04s | 1.19s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.17s | 1.29s | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 168.07ms | 164.86ms | 1.0× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.83ms | 57.95ms | 2.7× | 5.5% | PASS |
| ac_writes | `oltp_insert_ac` | 24.23ms | 73.42ms | 3.0× | 4.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.60ms | 83.67ms | 3.1× | 5.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.77ms | 68.41ms | 2.9× | 7.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.70ms | 76.00ms | 3.1× | 6.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.60ms | 77.78ms | 3.2× | 4.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.33ms | 71.23ms | 3.1× | 8.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.75ms | 84.69ms | 2.7× | 5.3% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 25.52ms | 29.19ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_range_select` | 15.62ms | 16.20ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_sum_range` | 13.59ms | 15.70ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 2.93ms | 3.08ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.73ms | 3.95ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.60ms | 4.48ms | 1.2× | 1.2% | PASS |
| mem_reads | `select_random_points` | 19.86ms | 23.06ms | 1.2× | 1.1% | PASS |
| mem_reads | `select_random_ranges` | 5.75ms | 6.51ms | 1.1× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 3.29ms | 3.20ms | 1.0× | 1.5% | PASS |
| mem_reads | `groupby_scan` | 29.01ms | 32.34ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 6.22ms | 7.77ms | 1.2× | 1.5% | PASS |
| mem_reads | `index_join_scan` | 3.31ms | 4.55ms | 1.4× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 860.82ms | 965.29ms | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 998.20ms | 1.05s | 1.1× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 114.44ms | 127.83ms | 1.1× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 183.76ms | 260.45ms | 1.4× | 0.6% | PASS |
| mem_writes | `oltp_insert` | 14.42ms | 26.95ms | 1.9× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 52.15ms | 99.62ms | 1.9× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 40.09ms | 65.75ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_delete_insert` | 38.81ms | 75.47ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 21.36ms | 47.16ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 25.25ms | 40.48ms | 1.6× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 74.94ms | 119.77ms | 1.6× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 94.29ms | 46.09ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 23.20ms | 18.10ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_sum_range` | 21.08ms | 17.60ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.78ms | 3.32ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 4.59ms | 4.19ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 10.83ms | 6.62ms | 0.6× | 1.4% | PASS |
| file_reads | `select_random_points` | 27.68ms | 25.27ms | 0.9× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 13.02ms | 8.40ms | 0.6× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 10.60ms | 5.40ms | 0.5× | 0.9% | PASS |
| file_reads | `groupby_scan` | 30.15ms | 32.65ms | 1.1× | 0.8% | PASS |
| file_reads | `index_join` | 10.26ms | 9.58ms | 0.9× | 1.7% | PASS |
| file_reads | `index_join_scan` | 4.13ms | 4.92ms | 1.2× | 1.8% | PASS |
| file_reads | `types_table_scan` | 867.71ms | 965.59ms | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 1.00s | 1.05s | 1.0× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 215.78ms | 151.88ms | 0.7× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 236.30ms | 317.09ms | 1.3× | 3.5% | PASS |
| file_writes | `oltp_insert` | 33.53ms | 46.28ms | 1.4× | 17.1% | PASS |
| file_writes | `oltp_update_index` | 163.21ms | 165.17ms | 1.0× | 2.5% | PASS |
| file_writes | `oltp_update_non_index` | 136.78ms | 115.48ms | 0.8× | 2.5% | PASS |
| file_writes | `oltp_delete_insert` | 133.23ms | 131.37ms | 1.0× | 3.1% | PASS |
| file_writes | `oltp_write_only` | 105.43ms | 93.59ms | 0.9× | 8.6% | PASS |
| file_writes | `types_delete_insert` | 88.26ms | 73.28ms | 0.8× | 8.6% | PASS |
| file_writes | `oltp_read_write` | 159.40ms | 167.41ms | 1.1× | 13.2% | PASS |
| ac_reads | `oltp_point_select` | 46.96ms | 46.14ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 18.49ms | 18.07ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 16.36ms | 17.57ms | 1.1× | 0.8% | PASS |
| ac_reads | `oltp_order_range` | 3.37ms | 3.32ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.13ms | 4.19ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.24ms | 6.62ms | 1.1× | 1.2% | PASS |
| ac_reads | `select_random_points` | 22.82ms | 25.04ms | 1.1× | 0.9% | PASS |
| ac_reads | `select_random_ranges` | 8.29ms | 8.39ms | 1.0× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 5.91ms | 5.43ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 29.66ms | 32.62ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 7.89ms | 9.55ms | 1.2× | 0.8% | PASS |
| ac_reads | `index_join_scan` | 3.70ms | 4.90ms | 1.3× | 1.0% | PASS |
| ac_reads | `types_table_scan` | 868.05ms | 965.56ms | 1.1× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.00s | 1.05s | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 146.26ms | 152.37ms | 1.0× | 0.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.66ms | 64.90ms | 3.3× | 39.7% | PASS |
| ac_writes | `oltp_insert_ac` | 21.42ms | 70.24ms | 3.3× | 34.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.78ms | 79.19ms | 3.6× | 18.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.26ms | 75.58ms | 3.2× | 48.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 19.83ms | 68.25ms | 3.4× | 18.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.64ms | 88.04ms | 3.9× | 22.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.91ms | 72.82ms | 3.9× | 31.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 51.88ms | 166.00ms | 3.2× | 74.4% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 20s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 54.15ms | 130.00ms | 41.7% | 0.5% | PASS |
| `status_dirty_many_tables` | 56.27ms | 130.00ms | 43.3% | 0.5% | PASS |
| `diff_regular_working_one_table` | 51.02ms | 120.00ms | 42.5% | 0.5% | PASS |
| `diff_regular_working_many_tables` | 58.84ms | 140.00ms | 42.0% | 0.5% | PASS |
| `diff_stat_working_many_tables` | 58.94ms | 140.00ms | 42.1% | 0.6% | PASS |
| `diff_schema_working_many_tables` | 59.71ms | 140.00ms | 42.6% | 0.5% | PASS |
| `branch_list_many_branches` | 12.49ms | 35.00ms | 35.7% | 0.9% | PASS |
| `branch_create_delete` | 13.88ms | 40.00ms | 34.7% | 1.1% | PASS |
| `checkout_branch_clean` | 35.72ms | 150.00ms | 23.8% | 2.1% | PASS |
| `merge_data_no_conflicts` | 16.24ms | 50.00ms | 32.5% | 1.1% | PASS |
| `merge_schema_no_conflicts` | 12.33ms | 35.00ms | 35.2% | 1.2% | PASS |
| `merge_data_conflicts` | 18.05ms | 180.00ms | 10.0% | 0.7% | PASS |
| `merge_data_conflicts_with_resolve` | 18.01ms | 180.00ms | 10.0% | 0.6% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
