# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-15 16:02 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/34981415072)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.52s | 11.39s | 1.0× | 1.6% | **PASS** |
| Writes | 2.32s | 3.80s | 1.6× | 1.6% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 12.54s | 11.63s | 0.9× | 1.5% | **PASS** |
| Writes | 3.27s | 4.13s | 1.3× | 2.0% | **PASS** |
| Autocommit writes | 800.88ms | 2.89s | 3.6× | 5.6% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.58s | 2.63s | 1.0× | 1.5% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 3.07s | 2.89s | 0.9× | 2.3% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.79s | 2.84s | 1.0× | 1.4% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 3.08s | 3.03s | 1.0× | 1.7% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 460.35ms | 754.85ms | 1.6× | 1.7% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 634.28ms | 1.05s | 1.6× | 2.0% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 622.40ms | 1.02s | 1.6× | 1.4% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 605.88ms | 976.72ms | 1.6× | 1.4% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.76s | 2.68s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.36s | 2.94s | 0.9× | 1.8% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.10s | 2.93s | 0.9× | 1.3% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.32s | 3.09s | 0.9× | 1.4% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 621.68ms | 821.11ms | 1.3× | 2.4% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.01s | 1.16s | 1.1× | 3.7% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 853.79ms | 1.11s | 1.3× | 1.8% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 783.69ms | 1.05s | 1.3× | 1.4% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.71s | 2.73s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.94s | 2.89s | 1.0× | 2.9% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.85s | 2.88s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 3.20s | 3.11s | 1.0× | 1.0% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 210.10ms | 774.48ms | 3.7× | 6.2% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 229.58ms | 795.44ms | 3.5× | 7.8% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 211.65ms | 733.08ms | 3.5× | 5.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 149.54ms | 584.55ms | 3.9× | 4.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 25.28ms | 30.34ms | 1.2× | 2.0% | PASS |
| mem_reads | `oltp_range_select` | 11.03ms | 11.34ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 10.29ms | 11.42ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 2.66ms | 2.84ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.74ms | 4.04ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 4.03ms | 5.60ms | 1.4× | 2.3% | PASS |
| mem_reads | `select_random_points` | 11.28ms | 11.22ms | 1.0× | 3.6% | PASS |
| mem_reads | `select_random_ranges` | 3.11ms | 4.02ms | 1.3× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.21ms | 4.27ms | 1.0× | 1.0% | PASS |
| mem_reads | `groupby_scan` | 30.76ms | 33.14ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 5.82ms | 8.06ms | 1.4× | 1.6% | PASS |
| mem_reads | `index_join_scan` | 3.32ms | 4.53ms | 1.4× | 2.5% | PASS |
| mem_reads | `types_table_scan` | 1.11s | 1.14s | 1.0× | 1.6% | PASS |
| mem_reads | `table_scan` | 1.25s | 1.24s | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 107.95ms | 119.52ms | 1.1× | 2.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.77ms | 277.29ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 15.48ms | 28.79ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 53.74ms | 104.65ms | 1.9× | 1.9% | PASS |
| mem_writes | `oltp_update_non_index` | 36.84ms | 62.02ms | 1.7× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 47.02ms | 76.30ms | 1.6× | 1.8% | PASS |
| mem_writes | `oltp_write_only` | 23.36ms | 49.51ms | 2.1× | 2.0% | PASS |
| mem_writes | `types_delete_insert` | 25.64ms | 39.55ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 73.48ms | 116.74ms | 1.6× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 99.19ms | 49.62ms | 0.5× | 0.5% | PASS |
| file_reads | `oltp_range_select` | 18.54ms | 13.35ms | 0.7× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 17.65ms | 13.48ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 3.52ms | 3.16ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_distinct_range` | 4.53ms | 4.31ms | 1.0× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 11.55ms | 7.58ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 18.90ms | 13.35ms | 0.7× | 2.8% | PASS |
| file_reads | `select_random_ranges` | 10.46ms | 5.95ms | 0.6× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 11.73ms | 6.44ms | 0.5× | 1.5% | PASS |
| file_reads | `groupby_scan` | 31.50ms | 33.51ms | 1.1× | 0.6% | PASS |
| file_reads | `index_join` | 9.94ms | 9.61ms | 1.0× | 1.9% | PASS |
| file_reads | `index_join_scan` | 4.48ms | 4.96ms | 1.1× | 2.4% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.8% | PASS |
| file_reads | `table_scan` | 1.23s | 1.24s | 1.0× | 1.5% | PASS |
| file_reads | `oltp_read_only` | 223.12ms | 152.01ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 198.21ms | 290.84ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 22.77ms | 32.89ms | 1.4× | 3.1% | PASS |
| file_writes | `oltp_update_index` | 80.36ms | 109.97ms | 1.4× | 2.4% | PASS |
| file_writes | `oltp_update_non_index` | 60.78ms | 72.41ms | 1.2× | 2.2% | PASS |
| file_writes | `oltp_delete_insert` | 69.61ms | 85.08ms | 1.2× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 47.49ms | 57.74ms | 1.2× | 3.0% | PASS |
| file_writes | `types_delete_insert` | 41.70ms | 45.68ms | 1.1× | 2.5% | PASS |
| file_writes | `oltp_read_write` | 100.77ms | 126.51ms | 1.3× | 2.3% | PASS |
| ac_reads | `oltp_point_select` | 49.49ms | 49.76ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 13.81ms | 13.45ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 12.98ms | 13.55ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_order_range` | 3.08ms | 3.18ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_distinct_range` | 4.10ms | 4.33ms | 1.1× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.75ms | 7.64ms | 1.1× | 2.3% | PASS |
| ac_reads | `select_random_points` | 13.65ms | 13.28ms | 1.0× | 3.6% | PASS |
| ac_reads | `select_random_ranges` | 5.54ms | 5.93ms | 1.1× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 6.88ms | 6.49ms | 0.9× | 2.3% | PASS |
| ac_reads | `groupby_scan` | 31.12ms | 33.46ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 7.39ms | 9.66ms | 1.3× | 1.9% | PASS |
| ac_reads | `index_join_scan` | 3.74ms | 4.90ms | 1.3× | 1.6% | PASS |
| ac_reads | `types_table_scan` | 1.09s | 1.15s | 1.0× | 1.4% | PASS |
| ac_reads | `table_scan` | 1.31s | 1.26s | 1.0× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 151.82ms | 155.46ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.95ms | 82.07ms | 3.4× | 7.6% | PASS |
| ac_writes | `oltp_insert_ac` | 25.46ms | 95.78ms | 3.8× | 6.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.69ms | 110.70ms | 3.9× | 4.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.47ms | 90.24ms | 3.7× | 8.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.49ms | 100.46ms | 3.8× | 7.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.71ms | 103.63ms | 3.7× | 6.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.07ms | 89.11ms | 4.0× | 5.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.26ms | 102.48ms | 3.3× | 5.5% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 39.04ms | 39.67ms | 1.0× | 2.8% | PASS |
| mem_reads | `oltp_range_select` | 17.80ms | 14.81ms | 0.8× | 3.4% | PASS |
| mem_reads | `oltp_sum_range` | 16.92ms | 14.19ms | 0.8× | 3.2% | PASS |
| mem_reads | `oltp_order_range` | 3.43ms | 3.24ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.51ms | 4.35ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_index_scan` | 4.12ms | 6.42ms | 1.6× | 1.9% | PASS |
| mem_reads | `select_random_points` | 26.65ms | 22.23ms | 0.8× | 4.6% | PASS |
| mem_reads | `select_random_ranges` | 3.86ms | 5.45ms | 1.4× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.41ms | 4.97ms | 1.1× | 2.4% | PASS |
| mem_reads | `groupby_scan` | 36.33ms | 35.06ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 11.72ms | 10.35ms | 0.9× | 3.4% | PASS |
| mem_reads | `index_join_scan` | 4.09ms | 5.95ms | 1.5× | 3.3% | PASS |
| mem_reads | `types_table_scan` | 1.29s | 1.24s | 1.0× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.44s | 1.34s | 0.9× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 161.71ms | 145.31ms | 0.9× | 1.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 244.88ms | 380.01ms | 1.6× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 18.37ms | 39.56ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 69.83ms | 147.03ms | 2.1× | 2.7% | PASS |
| mem_writes | `oltp_update_non_index` | 53.62ms | 90.43ms | 1.7× | 2.8% | PASS |
| mem_writes | `oltp_delete_insert` | 60.42ms | 111.89ms | 1.9× | 1.9% | PASS |
| mem_writes | `oltp_write_only` | 29.77ms | 64.61ms | 2.2× | 2.0% | PASS |
| mem_writes | `types_delete_insert` | 42.66ms | 57.49ms | 1.3× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 114.73ms | 155.08ms | 1.4× | 2.5% | PASS |
| file_reads | `oltp_point_select` | 115.62ms | 59.23ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 26.98ms | 17.21ms | 0.6× | 2.5% | PASS |
| file_reads | `oltp_sum_range` | 26.07ms | 16.76ms | 0.6× | 2.8% | PASS |
| file_reads | `oltp_order_range` | 4.89ms | 3.96ms | 0.8× | 4.4% | PASS |
| file_reads | `oltp_distinct_range` | 5.98ms | 5.07ms | 0.8× | 3.9% | PASS |
| file_reads | `oltp_index_scan` | 12.39ms | 8.79ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 37.78ms | 26.28ms | 0.7× | 3.1% | PASS |
| file_reads | `select_random_ranges` | 11.68ms | 7.59ms | 0.6× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 12.83ms | 7.10ms | 0.6× | 1.8% | PASS |
| file_reads | `groupby_scan` | 38.04ms | 35.93ms | 0.9× | 1.4% | PASS |
| file_reads | `index_join` | 17.00ms | 11.80ms | 0.7× | 2.6% | PASS |
| file_reads | `index_join_scan` | 5.21ms | 6.51ms | 1.2× | 3.1% | PASS |
| file_reads | `types_table_scan` | 1.30s | 1.22s | 0.9× | 1.7% | PASS |
| file_reads | `table_scan` | 1.47s | 1.33s | 0.9× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 278.38ms | 175.87ms | 0.6× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 272.06ms | 393.52ms | 1.4× | 0.8% | PASS |
| file_writes | `oltp_insert` | 26.97ms | 47.39ms | 1.8× | 2.6% | PASS |
| file_writes | `oltp_update_index` | 131.53ms | 168.92ms | 1.3× | 7.9% | PASS |
| file_writes | `oltp_update_non_index` | 126.06ms | 107.42ms | 0.9× | 8.0% | PASS |
| file_writes | `oltp_delete_insert` | 103.91ms | 126.52ms | 1.2× | 2.0% | PASS |
| file_writes | `oltp_write_only` | 88.94ms | 77.04ms | 0.9× | 10.5% | PASS |
| file_writes | `types_delete_insert` | 76.52ms | 66.61ms | 0.9× | 2.9% | PASS |
| file_writes | `oltp_read_write` | 181.41ms | 167.91ms | 0.9× | 4.5% | PASS |
| ac_reads | `oltp_point_select` | 64.69ms | 59.92ms | 0.9× | 2.3% | PASS |
| ac_reads | `oltp_range_select` | 22.73ms | 17.38ms | 0.8× | 3.3% | PASS |
| ac_reads | `oltp_sum_range` | 20.94ms | 16.90ms | 0.8× | 2.5% | PASS |
| ac_reads | `oltp_order_range` | 4.41ms | 3.97ms | 0.9× | 6.4% | PASS |
| ac_reads | `oltp_distinct_range` | 5.53ms | 5.04ms | 0.9× | 5.5% | PASS |
| ac_reads | `oltp_index_scan` | 7.29ms | 8.78ms | 1.2× | 1.5% | PASS |
| ac_reads | `select_random_points` | 29.73ms | 25.77ms | 0.9× | 4.5% | PASS |
| ac_reads | `select_random_ranges` | 6.63ms | 7.57ms | 1.1× | 1.9% | PASS |
| ac_reads | `covering_index_scan` | 7.91ms | 7.09ms | 0.9× | 2.9% | PASS |
| ac_reads | `groupby_scan` | 37.13ms | 35.93ms | 1.0× | 1.2% | PASS |
| ac_reads | `index_join` | 13.61ms | 11.61ms | 0.9× | 2.7% | PASS |
| ac_reads | `index_join_scan` | 4.52ms | 6.23ms | 1.4× | 3.0% | PASS |
| ac_reads | `types_table_scan` | 1.14s | 1.19s | 1.0× | 3.1% | PASS |
| ac_reads | `table_scan` | 1.38s | 1.32s | 1.0× | 2.7% | PASS |
| ac_reads | `oltp_read_only` | 189.18ms | 171.61ms | 0.9× | 3.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 25.20ms | 80.41ms | 3.2× | 7.3% | PASS |
| ac_writes | `oltp_insert_ac` | 28.42ms | 95.11ms | 3.3× | 7.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.11ms | 111.06ms | 3.8× | 8.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.97ms | 93.36ms | 3.7× | 7.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.99ms | 101.25ms | 3.5× | 8.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.37ms | 102.77ms | 3.6× | 7.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 27.74ms | 97.17ms | 3.5× | 9.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 36.77ms | 114.31ms | 3.1× | 5.7% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.42ms | 37.65ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 16.89ms | 15.08ms | 0.9× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 15.81ms | 13.64ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 3.24ms | 3.12ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 4.23ms | 4.27ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.00ms | 6.08ms | 1.5× | 1.8% | PASS |
| mem_reads | `select_random_points` | 22.38ms | 20.68ms | 0.9× | 2.2% | PASS |
| mem_reads | `select_random_ranges` | 3.63ms | 5.28ms | 1.5× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.24ms | 4.78ms | 1.1× | 1.2% | PASS |
| mem_reads | `groupby_scan` | 34.90ms | 35.18ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 10.21ms | 9.12ms | 0.9× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 3.93ms | 5.42ms | 1.4× | 2.3% | PASS |
| mem_reads | `types_table_scan` | 1.18s | 1.22s | 1.0× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.31s | 1.32s | 1.0× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 143.14ms | 139.69ms | 1.0× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 247.84ms | 376.87ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 19.35ms | 39.78ms | 2.1× | 1.3% | PASS |
| mem_writes | `oltp_update_index` | 72.02ms | 148.14ms | 2.1× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 52.53ms | 87.11ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 55.16ms | 105.06ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 28.88ms | 62.55ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 39.98ms | 54.30ms | 1.4× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 106.65ms | 149.33ms | 1.4× | 2.7% | PASS |
| file_reads | `oltp_point_select` | 114.38ms | 59.35ms | 0.5× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 25.44ms | 17.43ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 24.37ms | 16.12ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 4.34ms | 3.44ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.43ms | 4.64ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 12.00ms | 8.54ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 33.25ms | 24.04ms | 0.7× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 11.20ms | 7.36ms | 0.7× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 12.08ms | 6.86ms | 0.6× | 1.6% | PASS |
| file_reads | `groupby_scan` | 36.05ms | 35.69ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 15.46ms | 11.04ms | 0.7× | 2.3% | PASS |
| file_reads | `index_join_scan` | 4.98ms | 5.68ms | 1.1× | 2.3% | PASS |
| file_reads | `types_table_scan` | 1.19s | 1.21s | 1.0× | 0.9% | PASS |
| file_reads | `table_scan` | 1.35s | 1.34s | 1.0× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 265.79ms | 173.75ms | 0.7× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 268.70ms | 385.49ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 26.38ms | 46.12ms | 1.7× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 98.89ms | 155.86ms | 1.6× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 93.66ms | 100.39ms | 1.1× | 8.7% | PASS |
| file_writes | `oltp_delete_insert` | 93.86ms | 120.33ms | 1.3× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 61.23ms | 73.77ms | 1.2× | 1.4% | PASS |
| file_writes | `types_delete_insert` | 68.93ms | 64.29ms | 0.9× | 2.0% | PASS |
| file_writes | `oltp_read_write` | 142.15ms | 162.32ms | 1.1× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 62.49ms | 58.22ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 20.04ms | 17.16ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 18.81ms | 15.89ms | 0.8× | 0.9% | PASS |
| ac_reads | `oltp_order_range` | 3.84ms | 3.42ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.87ms | 4.64ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.64ms | 8.46ms | 1.3× | 1.4% | PASS |
| ac_reads | `select_random_points` | 26.54ms | 24.00ms | 0.9× | 3.5% | PASS |
| ac_reads | `select_random_ranges` | 6.15ms | 7.36ms | 1.2× | 1.9% | PASS |
| ac_reads | `covering_index_scan` | 7.18ms | 6.86ms | 1.0× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 35.48ms | 35.80ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 12.63ms | 10.94ms | 0.9× | 2.1% | PASS |
| ac_reads | `index_join_scan` | 4.37ms | 5.69ms | 1.3× | 1.5% | PASS |
| ac_reads | `types_table_scan` | 1.14s | 1.19s | 1.0× | 1.8% | PASS |
| ac_reads | `table_scan` | 1.31s | 1.32s | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 187.53ms | 172.61ms | 0.9× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.86ms | 75.55ms | 3.2× | 4.8% | PASS |
| ac_writes | `oltp_insert_ac` | 25.45ms | 91.51ms | 3.6× | 3.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.21ms | 101.56ms | 3.6× | 5.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.22ms | 85.53ms | 3.5× | 5.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.30ms | 95.73ms | 3.6× | 3.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.45ms | 93.68ms | 3.7× | 4.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.50ms | 89.44ms | 3.5× | 5.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.66ms | 100.07ms | 3.1× | 5.3% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.44ms | 38.62ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 20.23ms | 20.95ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_sum_range` | 18.22ms | 20.29ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_order_range` | 3.76ms | 3.96ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.82ms | 5.13ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.68ms | 6.13ms | 1.3× | 1.9% | PASS |
| mem_reads | `select_random_points` | 28.45ms | 31.49ms | 1.1× | 2.1% | PASS |
| mem_reads | `select_random_ranges` | 7.48ms | 8.39ms | 1.1× | 0.8% | PASS |
| mem_reads | `covering_index_scan` | 4.34ms | 4.67ms | 1.1× | 2.5% | PASS |
| mem_reads | `groupby_scan` | 39.29ms | 42.25ms | 1.1× | 1.4% | PASS |
| mem_reads | `index_join` | 7.92ms | 11.00ms | 1.4× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 4.05ms | 5.92ms | 1.5× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 1.21s | 1.26s | 1.0× | 2.5% | PASS |
| mem_reads | `table_scan` | 1.53s | 1.40s | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_read_only` | 158.69ms | 170.13ms | 1.1× | 1.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 248.55ms | 345.15ms | 1.4× | 1.4% | PASS |
| mem_writes | `oltp_insert` | 19.50ms | 36.12ms | 1.9× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 70.69ms | 135.90ms | 1.9× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 54.05ms | 86.03ms | 1.6× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 50.86ms | 99.64ms | 2.0× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 27.71ms | 60.90ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 32.91ms | 53.51ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 101.61ms | 159.47ms | 1.6× | 1.8% | PASS |
| file_reads | `oltp_point_select` | 131.18ms | 62.00ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 30.47ms | 23.30ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 28.84ms | 22.74ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 4.71ms | 4.20ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.75ms | 5.36ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 14.44ms | 8.42ms | 0.6× | 1.8% | PASS |
| file_reads | `select_random_points` | 38.11ms | 33.57ms | 0.9× | 1.6% | PASS |
| file_reads | `select_random_ranges` | 17.36ms | 10.84ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 14.27ms | 7.08ms | 0.5× | 1.0% | PASS |
| file_reads | `groupby_scan` | 40.15ms | 42.30ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 13.21ms | 12.37ms | 0.9× | 1.4% | PASS |
| file_reads | `index_join_scan` | 5.11ms | 6.11ms | 1.2× | 1.6% | PASS |
| file_reads | `types_table_scan` | 1.21s | 1.26s | 1.0× | 2.0% | PASS |
| file_reads | `table_scan` | 1.47s | 1.39s | 0.9× | 3.1% | PASS |
| file_reads | `oltp_read_only` | 296.41ms | 202.94ms | 0.7× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 264.19ms | 351.61ms | 1.3× | 1.3% | PASS |
| file_writes | `oltp_insert` | 26.35ms | 41.43ms | 1.6× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 100.05ms | 143.81ms | 1.4× | 1.3% | PASS |
| file_writes | `oltp_update_non_index` | 81.01ms | 98.46ms | 1.2× | 1.4% | PASS |
| file_writes | `oltp_delete_insert` | 79.79ms | 111.48ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 51.40ms | 70.75ms | 1.4× | 2.5% | PASS |
| file_writes | `types_delete_insert` | 50.90ms | 59.37ms | 1.2× | 1.4% | PASS |
| file_writes | `oltp_read_write` | 130.01ms | 170.29ms | 1.3× | 1.7% | PASS |
| ac_reads | `oltp_point_select` | 66.03ms | 62.39ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 24.30ms | 23.47ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_sum_range` | 21.85ms | 22.64ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 4.19ms | 4.20ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 5.24ms | 5.36ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_index_scan` | 8.21ms | 8.53ms | 1.0× | 1.1% | PASS |
| ac_reads | `select_random_points` | 31.75ms | 33.97ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_ranges` | 10.91ms | 10.86ms | 1.0× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 7.99ms | 7.13ms | 0.9× | 1.0% | PASS |
| ac_reads | `groupby_scan` | 39.38ms | 42.35ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 10.08ms | 12.41ms | 1.2× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 4.52ms | 6.17ms | 1.4× | 1.0% | PASS |
| ac_reads | `types_table_scan` | 1.24s | 1.26s | 1.0× | 2.2% | PASS |
| ac_reads | `table_scan` | 1.53s | 1.40s | 0.9× | 2.2% | PASS |
| ac_reads | `oltp_read_only` | 203.25ms | 202.26ms | 1.0× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 16.12ms | 56.31ms | 3.5× | 4.1% | PASS |
| ac_writes | `oltp_insert_ac` | 18.41ms | 73.44ms | 4.0× | 3.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.08ms | 86.46ms | 4.3× | 3.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.32ms | 66.70ms | 4.1× | 4.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 18.11ms | 77.86ms | 4.3× | 4.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 18.92ms | 75.96ms | 4.0× | 4.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 15.70ms | 64.36ms | 4.1× | 6.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.88ms | 83.46ms | 3.2× | 2.9% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 12s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 94.91ms | 130.00ms | 73.0% | 0.4% | PASS |
| `status_dirty_many_tables` | 98.56ms | 130.00ms | 75.8% | 0.4% | PASS |
| `diff_regular_working_one_table` | 90.36ms | 120.00ms | 75.3% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 103.67ms | 140.00ms | 74.1% | 0.3% | PASS |
| `diff_stat_working_many_tables` | 103.74ms | 140.00ms | 74.1% | 0.4% | PASS |
| `diff_schema_working_many_tables` | 104.96ms | 140.00ms | 75.0% | 0.4% | PASS |
| `branch_list_many_branches` | 23.17ms | 35.00ms | 66.2% | 1.2% | PASS |
| `branch_create_delete` | 25.53ms | 40.00ms | 63.8% | 1.6% | PASS |
| `checkout_branch_clean` | 60.88ms | 150.00ms | 40.6% | 0.8% | PASS |
| `merge_data_no_conflicts` | 29.44ms | 50.00ms | 58.9% | 1.3% | PASS |
| `merge_schema_no_conflicts` | 22.27ms | 35.00ms | 63.6% | 1.5% | PASS |
| `merge_data_conflicts` | 33.01ms | 180.00ms | 18.3% | 1.6% | PASS |
| `merge_data_conflicts_with_resolve` | 33.05ms | 180.00ms | 18.4% | 1.1% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
