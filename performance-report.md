# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-10-30 17:46 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261026.180.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/41087174123)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.28s | 11.21s | 1.1× | 1.8% | **PASS** |
| Writes | 2.20s | 3.86s | 1.8× | 1.3% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.44s | 11.52s | 1.0× | 1.6% | **PASS** |
| Writes | 3.12s | 4.24s | 1.4× | 1.7% | **PASS** |
| Autocommit writes | 794.19ms | 2.44s | 3.1× | 6.6% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.44s | 2.68s | 1.1× | 1.6% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.69s | 2.84s | 1.1× | 1.9% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.61s | 2.83s | 1.1× | 2.1% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.53s | 2.87s | 1.1× | 1.4% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 437.68ms | 758.36ms | 1.7× | 1.5% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 590.43ms | 1.06s | 1.8× | 1.6% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 597.22ms | 1.05s | 1.8× | 1.4% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 571.47ms | 982.35ms | 1.7× | 0.9% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 2.69s | 2.74s | 1.0× | 1.6% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.97s | 2.90s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.01s | 2.95s | 1.0× | 2.0% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.76s | 2.92s | 1.1× | 1.2% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 604.82ms | 843.29ms | 1.4× | 1.7% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 925.30ms | 1.17s | 1.3× | 4.0% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 842.55ms | 1.16s | 1.4× | 2.1% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 749.19ms | 1.07s | 1.4× | 1.6% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.55s | 2.74s | 1.1× | 1.7% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.89s | 2.91s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.76s | 2.92s | 1.1× | 1.6% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.63s | 2.93s | 1.1× | 1.1% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 181.80ms | 573.63ms | 3.2× | 4.5% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 204.11ms | 618.40ms | 3.0× | 7.1% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 201.27ms | 603.96ms | 3.0× | 6.3% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 207.01ms | 647.79ms | 3.1× | 9.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 25.95ms | 30.46ms | 1.2× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 11.62ms | 11.93ms | 1.0× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 9.68ms | 11.32ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 2.72ms | 2.98ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_distinct_range` | 3.68ms | 4.11ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 3.91ms | 5.32ms | 1.4× | 1.8% | PASS |
| mem_reads | `select_random_points` | 10.48ms | 11.16ms | 1.1× | 3.3% | PASS |
| mem_reads | `select_random_ranges` | 3.12ms | 4.01ms | 1.3× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.18ms | 4.29ms | 1.0× | 2.3% | PASS |
| mem_reads | `groupby_scan` | 30.58ms | 33.77ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 5.81ms | 7.97ms | 1.4× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 3.38ms | 4.49ms | 1.3× | 3.2% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.16s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.18s | 1.26s | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 108.42ms | 122.29ms | 1.1× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 176.04ms | 276.95ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 14.99ms | 28.69ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 50.67ms | 100.54ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 34.73ms | 63.80ms | 1.8× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 44.69ms | 78.23ms | 1.8× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 22.04ms | 50.53ms | 2.3× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 24.80ms | 41.52ms | 1.7× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 69.72ms | 118.10ms | 1.7× | 2.5% | PASS |
| file_reads | `oltp_point_select` | 92.91ms | 47.03ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 18.57ms | 13.75ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 17.21ms | 13.35ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 3.81ms | 3.44ms | 0.9× | 3.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.78ms | 4.56ms | 1.0× | 2.2% | PASS |
| file_reads | `oltp_index_scan` | 11.11ms | 7.32ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 18.50ms | 13.12ms | 0.7× | 2.4% | PASS |
| file_reads | `select_random_ranges` | 10.07ms | 5.77ms | 0.6× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.42ms | 6.28ms | 0.5× | 1.7% | PASS |
| file_reads | `groupby_scan` | 32.01ms | 34.47ms | 1.1× | 0.8% | PASS |
| file_reads | `index_join` | 9.78ms | 9.52ms | 1.0× | 1.6% | PASS |
| file_reads | `index_join_scan` | 4.31ms | 4.87ms | 1.1× | 2.4% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.17s | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 1.21s | 1.27s | 1.1× | 1.1% | PASS |
| file_reads | `oltp_read_only` | 211.81ms | 147.89ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 188.95ms | 287.67ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 21.92ms | 32.30ms | 1.5× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 80.27ms | 118.90ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 60.43ms | 78.90ms | 1.3× | 1.3% | PASS |
| file_writes | `oltp_delete_insert` | 69.84ms | 93.32ms | 1.3× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 45.22ms | 59.65ms | 1.3× | 1.9% | PASS |
| file_writes | `types_delete_insert` | 40.67ms | 45.10ms | 1.1× | 2.5% | PASS |
| file_writes | `oltp_read_write` | 97.52ms | 127.46ms | 1.3× | 1.7% | PASS |
| ac_reads | `oltp_point_select` | 48.03ms | 47.34ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_range_select` | 14.58ms | 13.90ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_sum_range` | 13.03ms | 13.54ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.49ms | 3.46ms | 1.0× | 4.6% | PASS |
| ac_reads | `oltp_distinct_range` | 4.42ms | 4.61ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.62ms | 7.37ms | 1.1× | 1.9% | PASS |
| ac_reads | `select_random_points` | 13.32ms | 12.90ms | 1.0× | 2.8% | PASS |
| ac_reads | `select_random_ranges` | 5.60ms | 5.83ms | 1.0× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 7.09ms | 6.35ms | 0.9× | 2.9% | PASS |
| ac_reads | `groupby_scan` | 31.81ms | 34.56ms | 1.1× | 1.3% | PASS |
| ac_reads | `index_join` | 7.75ms | 9.61ms | 1.2× | 2.1% | PASS |
| ac_reads | `index_join_scan` | 3.91ms | 4.93ms | 1.3× | 2.3% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.17s | 1.1× | 1.1% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.26s | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 143.15ms | 148.46ms | 1.0× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.45ms | 57.21ms | 2.9× | 3.8% | PASS |
| ac_writes | `oltp_insert_ac` | 22.72ms | 71.46ms | 3.1× | 4.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.20ms | 83.29ms | 3.3× | 3.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.70ms | 64.89ms | 3.1× | 4.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.14ms | 74.98ms | 3.4× | 3.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.17ms | 74.97ms | 3.2× | 5.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.48ms | 65.30ms | 3.2× | 4.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.94ms | 81.52ms | 2.9× | 4.5% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.94ms | 39.28ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_range_select` | 16.85ms | 14.38ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_sum_range` | 15.15ms | 14.08ms | 0.9× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 3.38ms | 3.28ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_distinct_range` | 4.36ms | 4.38ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 4.00ms | 6.32ms | 1.6× | 1.4% | PASS |
| mem_reads | `select_random_points` | 21.45ms | 21.38ms | 1.0× | 1.3% | PASS |
| mem_reads | `select_random_ranges` | 3.45ms | 5.33ms | 1.5× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 4.20ms | 4.67ms | 1.1× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 34.89ms | 35.06ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 10.36ms | 9.31ms | 0.9× | 2.6% | PASS |
| mem_reads | `index_join_scan` | 3.77ms | 5.46ms | 1.4× | 2.0% | PASS |
| mem_reads | `types_table_scan` | 1.08s | 1.21s | 1.1× | 2.0% | PASS |
| mem_reads | `table_scan` | 1.31s | 1.32s | 1.0× | 3.2% | PASS |
| mem_reads | `oltp_read_only` | 143.81ms | 143.23ms | 1.0× | 2.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 234.90ms | 377.95ms | 1.6× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 17.66ms | 40.20ms | 2.3× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 65.72ms | 154.54ms | 2.4× | 1.9% | FAIL |
| mem_writes | `oltp_update_non_index` | 48.30ms | 93.10ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 54.27ms | 111.36ms | 2.1× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 28.12ms | 68.02ms | 2.4× | 1.6% | FAIL |
| mem_writes | `types_delete_insert` | 38.35ms | 59.51ms | 1.6× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 103.10ms | 157.00ms | 1.5× | 2.4% | PASS |
| file_reads | `oltp_point_select` | 105.87ms | 57.91ms | 0.5× | 1.4% | PASS |
| file_reads | `oltp_range_select` | 24.98ms | 16.37ms | 0.7× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 23.40ms | 16.19ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.36ms | 3.56ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 5.37ms | 4.67ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 11.16ms | 8.34ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 30.41ms | 24.23ms | 0.8× | 1.9% | PASS |
| file_reads | `select_random_ranges` | 10.36ms | 7.13ms | 0.7× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 11.38ms | 6.66ms | 0.6× | 1.1% | PASS |
| file_reads | `groupby_scan` | 35.84ms | 35.40ms | 1.0× | 1.1% | PASS |
| file_reads | `index_join` | 14.86ms | 11.17ms | 0.8× | 2.7% | PASS |
| file_reads | `index_join_scan` | 4.73ms | 5.86ms | 1.2× | 2.2% | PASS |
| file_reads | `types_table_scan` | 1.12s | 1.21s | 1.1× | 2.7% | PASS |
| file_reads | `table_scan` | 1.32s | 1.32s | 1.0× | 2.2% | PASS |
| file_reads | `oltp_read_only` | 249.96ms | 170.69ms | 0.7× | 1.4% | PASS |
| file_writes | `oltp_bulk_insert` | 259.88ms | 395.26ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 25.95ms | 48.27ms | 1.9× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 129.65ms | 174.15ms | 1.3× | 12.2% | PASS |
| file_writes | `oltp_update_non_index` | 102.59ms | 109.23ms | 1.1× | 9.2% | PASS |
| file_writes | `oltp_delete_insert` | 97.21ms | 126.72ms | 1.3× | 1.2% | PASS |
| file_writes | `oltp_write_only` | 89.43ms | 80.96ms | 0.9× | 10.0% | PASS |
| file_writes | `types_delete_insert` | 71.75ms | 68.14ms | 0.9× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 148.84ms | 167.12ms | 1.1× | 6.4% | PASS |
| ac_reads | `oltp_point_select` | 58.70ms | 57.16ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_range_select` | 20.10ms | 16.34ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 18.34ms | 16.07ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 3.88ms | 3.56ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.93ms | 4.73ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.70ms | 8.33ms | 1.2× | 1.5% | PASS |
| ac_reads | `select_random_points` | 25.44ms | 24.36ms | 1.0× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 5.96ms | 7.17ms | 1.2× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 6.94ms | 6.71ms | 1.0× | 1.0% | PASS |
| ac_reads | `groupby_scan` | 35.59ms | 35.48ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 12.59ms | 11.06ms | 0.9× | 2.3% | PASS |
| ac_reads | `index_join_scan` | 4.28ms | 5.94ms | 1.4× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 1.13s | 1.21s | 1.1× | 3.8% | PASS |
| ac_reads | `table_scan` | 1.38s | 1.33s | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_read_only` | 176.48ms | 170.06ms | 1.0× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.46ms | 62.46ms | 2.8× | 5.6% | PASS |
| ac_writes | `oltp_insert_ac` | 26.16ms | 73.86ms | 2.8× | 4.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.57ms | 89.28ms | 3.4× | 5.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.05ms | 70.39ms | 3.2× | 7.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.21ms | 80.22ms | 3.2× | 6.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.11ms | 78.66ms | 3.0× | 7.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.17ms | 75.25ms | 3.2× | 9.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.37ms | 88.27ms | 2.7× | 7.3% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 38.49ms | 40.83ms | 1.1× | 3.5% | PASS |
| mem_reads | `oltp_range_select` | 17.07ms | 14.62ms | 0.9× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 15.59ms | 14.00ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 3.44ms | 3.36ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 4.45ms | 4.46ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 4.18ms | 6.70ms | 1.6× | 1.9% | PASS |
| mem_reads | `select_random_points` | 22.93ms | 21.29ms | 0.9× | 4.2% | PASS |
| mem_reads | `select_random_ranges` | 3.53ms | 5.57ms | 1.6× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.27ms | 5.00ms | 1.2× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 35.33ms | 35.94ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 10.50ms | 9.65ms | 0.9× | 2.2% | PASS |
| mem_reads | `index_join_scan` | 3.76ms | 5.52ms | 1.5× | 2.5% | PASS |
| mem_reads | `types_table_scan` | 1.08s | 1.21s | 1.1× | 1.4% | PASS |
| mem_reads | `table_scan` | 1.23s | 1.31s | 1.1× | 2.6% | PASS |
| mem_reads | `oltp_read_only` | 142.04ms | 142.51ms | 1.0× | 2.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 238.99ms | 378.14ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.32ms | 41.03ms | 2.2× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 65.27ms | 147.84ms | 2.3× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 50.09ms | 92.63ms | 1.8× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 53.83ms | 111.78ms | 2.1× | 1.8% | PASS |
| mem_writes | `oltp_write_only` | 28.01ms | 66.44ms | 2.4× | 1.5% | FAIL |
| mem_writes | `types_delete_insert` | 37.74ms | 58.41ms | 1.5× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 104.97ms | 157.06ms | 1.5× | 2.8% | PASS |
| file_reads | `oltp_point_select` | 108.92ms | 58.34ms | 0.5× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 25.42ms | 16.82ms | 0.7× | 2.6% | PASS |
| file_reads | `oltp_sum_range` | 22.96ms | 16.09ms | 0.7× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 4.46ms | 3.76ms | 0.8× | 2.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.59ms | 4.89ms | 0.9× | 2.0% | PASS |
| file_reads | `oltp_index_scan` | 11.59ms | 8.67ms | 0.7× | 2.1% | PASS |
| file_reads | `select_random_points` | 33.05ms | 25.36ms | 0.8× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 10.63ms | 7.45ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 11.64ms | 6.85ms | 0.6× | 1.6% | PASS |
| file_reads | `groupby_scan` | 37.22ms | 36.67ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 15.60ms | 11.85ms | 0.8× | 2.9% | PASS |
| file_reads | `index_join_scan` | 4.91ms | 6.03ms | 1.2× | 3.0% | PASS |
| file_reads | `types_table_scan` | 1.18s | 1.25s | 1.1× | 1.5% | PASS |
| file_reads | `table_scan` | 1.28s | 1.33s | 1.0× | 1.4% | PASS |
| file_reads | `oltp_read_only` | 256.51ms | 174.44ms | 0.7× | 1.8% | PASS |
| file_writes | `oltp_bulk_insert` | 260.31ms | 393.86ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_insert` | 25.78ms | 48.64ms | 1.9× | 2.2% | PASS |
| file_writes | `oltp_update_index` | 103.84ms | 168.67ms | 1.6× | 2.9% | PASS |
| file_writes | `oltp_update_non_index` | 97.92ms | 105.73ms | 1.1× | 9.0% | PASS |
| file_writes | `oltp_delete_insert` | 90.11ms | 125.34ms | 1.4× | 2.0% | PASS |
| file_writes | `oltp_write_only` | 59.93ms | 78.99ms | 1.3× | 2.4% | PASS |
| file_writes | `types_delete_insert` | 66.79ms | 67.71ms | 1.0× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 137.86ms | 167.13ms | 1.2× | 1.5% | PASS |
| ac_reads | `oltp_point_select` | 61.04ms | 58.30ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_range_select` | 20.87ms | 16.92ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 18.79ms | 16.31ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 4.01ms | 3.69ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 5.01ms | 4.84ms | 1.0× | 2.8% | PASS |
| ac_reads | `oltp_index_scan` | 6.81ms | 8.44ms | 1.2× | 2.1% | PASS |
| ac_reads | `select_random_points` | 25.94ms | 24.14ms | 0.9× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 5.98ms | 7.27ms | 1.2× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 7.00ms | 6.73ms | 1.0× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 36.36ms | 36.43ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 12.75ms | 11.19ms | 0.9× | 2.2% | PASS |
| ac_reads | `index_join_scan` | 4.39ms | 5.89ms | 1.3× | 2.9% | PASS |
| ac_reads | `types_table_scan` | 1.09s | 1.22s | 1.1× | 1.3% | PASS |
| ac_reads | `table_scan` | 1.29s | 1.34s | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_read_only` | 171.74ms | 167.79ms | 1.0× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.20ms | 59.51ms | 2.6× | 6.0% | PASS |
| ac_writes | `oltp_insert_ac` | 24.83ms | 75.98ms | 3.1× | 6.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.09ms | 87.19ms | 3.3× | 6.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.54ms | 68.85ms | 3.2× | 6.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.86ms | 78.61ms | 3.2× | 8.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.08ms | 76.70ms | 3.1× | 7.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.51ms | 71.26ms | 3.0× | 7.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.16ms | 85.86ms | 2.7× | 6.1% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 31.99ms | 40.07ms | 1.3× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 18.99ms | 21.22ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 17.18ms | 20.60ms | 1.2× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 3.56ms | 4.12ms | 1.2× | 1.9% | PASS |
| mem_reads | `oltp_distinct_range` | 4.55ms | 5.29ms | 1.2× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.22ms | 5.78ms | 1.4× | 1.5% | PASS |
| mem_reads | `select_random_points` | 25.49ms | 30.64ms | 1.2× | 2.0% | PASS |
| mem_reads | `select_random_ranges` | 7.61ms | 8.99ms | 1.2× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 4.16ms | 4.38ms | 1.1× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 37.49ms | 41.62ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 7.90ms | 10.93ms | 1.4× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.87ms | 5.50ms | 1.4× | 1.3% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.20s | 1.2× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.17s | 1.29s | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 147.72ms | 176.55ms | 1.2× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 238.75ms | 352.92ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.37ms | 35.82ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 63.75ms | 129.04ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 48.31ms | 86.53ms | 1.8× | 0.8% | PASS |
| mem_writes | `oltp_delete_insert` | 47.57ms | 98.39ms | 2.1× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 25.53ms | 60.96ms | 2.4× | 0.9% | FAIL |
| mem_writes | `types_delete_insert` | 31.22ms | 54.45ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 97.97ms | 164.24ms | 1.7× | 0.9% | PASS |
| file_reads | `oltp_point_select` | 99.23ms | 57.46ms | 0.6× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 26.54ms | 23.09ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 24.99ms | 22.74ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 4.61ms | 4.43ms | 1.0× | 2.5% | PASS |
| file_reads | `oltp_distinct_range` | 5.66ms | 5.65ms | 1.0× | 2.1% | PASS |
| file_reads | `oltp_index_scan` | 11.56ms | 8.00ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 34.41ms | 33.73ms | 1.0× | 1.2% | PASS |
| file_reads | `select_random_ranges` | 14.89ms | 10.93ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 11.22ms | 6.26ms | 0.6× | 2.3% | PASS |
| file_reads | `groupby_scan` | 38.40ms | 41.79ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 12.04ms | 12.05ms | 1.0× | 2.0% | PASS |
| file_reads | `index_join_scan` | 4.87ms | 5.80ms | 1.2× | 1.2% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.20s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.18s | 1.29s | 1.1× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 249.46ms | 203.21ms | 0.8× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 252.03ms | 364.00ms | 1.4× | 1.3% | PASS |
| file_writes | `oltp_insert` | 25.91ms | 42.34ms | 1.6× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 94.30ms | 144.75ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 75.53ms | 100.60ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_delete_insert` | 75.27ms | 111.79ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_write_only` | 51.18ms | 72.48ms | 1.4× | 3.0% | PASS |
| file_writes | `types_delete_insert` | 48.81ms | 61.80ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 126.16ms | 175.81ms | 1.4× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 54.41ms | 57.37ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 21.84ms | 23.20ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 19.94ms | 22.64ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 4.02ms | 4.43ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 5.06ms | 5.62ms | 1.1× | 2.0% | PASS |
| ac_reads | `oltp_index_scan` | 6.83ms | 8.01ms | 1.2× | 1.4% | PASS |
| ac_reads | `select_random_points` | 28.44ms | 33.59ms | 1.2× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 10.14ms | 10.95ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 6.59ms | 6.27ms | 1.0× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 37.66ms | 41.74ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 9.43ms | 12.08ms | 1.3× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 4.33ms | 5.83ms | 1.3× | 1.5% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.20s | 1.1× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.29s | 1.1× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 184.49ms | 204.25ms | 1.1× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.72ms | 63.99ms | 2.8× | 8.4% | PASS |
| ac_writes | `oltp_insert_ac` | 24.79ms | 80.81ms | 3.3× | 9.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.71ms | 90.57ms | 3.3× | 8.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 25.12ms | 73.81ms | 2.9× | 9.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.76ms | 85.16ms | 3.3× | 10.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.77ms | 86.68ms | 3.4× | 10.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.61ms | 75.35ms | 3.5× | 10.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.54ms | 91.41ms | 2.7× | 7.7% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 39s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 59.30ms | 130.00ms | 45.6% | 1.6% | PASS |
| `status_dirty_many_tables` | 62.24ms | 130.00ms | 47.9% | 1.7% | PASS |
| `diff_regular_working_one_table` | 56.48ms | 120.00ms | 47.1% | 1.6% | PASS |
| `diff_regular_working_many_tables` | 64.23ms | 140.00ms | 45.9% | 1.2% | PASS |
| `diff_stat_working_many_tables` | 63.18ms | 140.00ms | 45.1% | 1.6% | PASS |
| `diff_schema_working_many_tables` | 64.25ms | 140.00ms | 45.9% | 1.5% | PASS |
| `branch_list_many_branches` | 15.30ms | 35.00ms | 43.7% | 2.0% | PASS |
| `branch_create_delete` | 17.35ms | 40.00ms | 43.4% | 1.9% | PASS |
| `checkout_branch_clean` | 89.44ms | 150.00ms | 59.6% | 2.7% | PASS |
| `merge_data_no_conflicts` | 23.74ms | 50.00ms | 47.5% | 2.5% | PASS |
| `merge_schema_no_conflicts` | 15.37ms | 35.00ms | 43.9% | 1.7% | PASS |
| `merge_data_conflicts` | 21.75ms | 180.00ms | 12.1% | 1.2% | PASS |
| `merge_data_conflicts_with_resolve` | 22.05ms | 180.00ms | 12.3% | 1.1% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
