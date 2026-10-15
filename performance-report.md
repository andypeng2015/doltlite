# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-15 15:55 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261012.334.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/38750130836)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.53s | 11.09s | 1.1× | 1.4% | **PASS** |
| Writes | 2.25s | 3.74s | 1.7× | 1.2% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.66s | 11.36s | 1.0× | 1.4% | **PASS** |
| Writes | 3.19s | 4.12s | 1.3× | 1.7% | **PASS** |
| Autocommit writes | 868.23ms | 2.63s | 3.0× | 6.3% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.44s | 2.59s | 1.1× | 1.2% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.61s | 2.76s | 1.1× | 1.5% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.91s | 2.91s | 1.0× | 1.8% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.58s | 2.83s | 1.1× | 1.2% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 433.33ms | 727.51ms | 1.7× | 0.8% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 593.03ms | 1.00s | 1.7× | 1.4% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 626.79ms | 1.05s | 1.7× | 2.5% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 594.05ms | 961.70ms | 1.6× | 1.2% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.67s | 2.65s | 1.0× | 1.0% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.83s | 2.81s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.17s | 2.96s | 0.9× | 1.8% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.99s | 2.93s | 1.0× | 1.5% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 587.04ms | 793.59ms | 1.4× | 1.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 962.96ms | 1.12s | 1.2× | 4.0% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 879.00ms | 1.16s | 1.3× | 2.2% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 763.83ms | 1.04s | 1.4× | 1.5% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.52s | 2.65s | 1.1× | 1.1% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.78s | 2.85s | 1.0× | 2.1% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.83s | 2.88s | 1.0× | 2.1% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.70s | 2.91s | 1.1× | 1.1% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 195.27ms | 604.41ms | 3.1× | 5.9% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 223.47ms | 640.54ms | 2.9× | 7.3% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 246.18ms | 764.20ms | 3.1× | 6.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 203.31ms | 624.05ms | 3.1× | 5.3% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.35ms | 29.76ms | 1.3× | 1.1% | PASS |
| mem_reads | `oltp_range_select` | 9.62ms | 11.46ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 9.12ms | 11.15ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 2.53ms | 2.78ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_distinct_range` | 3.67ms | 3.93ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.76ms | 5.06ms | 1.3× | 1.6% | PASS |
| mem_reads | `select_random_points` | 9.10ms | 10.60ms | 1.2× | 2.1% | PASS |
| mem_reads | `select_random_ranges` | 2.82ms | 3.92ms | 1.4× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 4.21ms | 4.18ms | 1.0× | 1.2% | PASS |
| mem_reads | `groupby_scan` | 30.22ms | 33.19ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 5.79ms | 7.74ms | 1.3× | 1.3% | PASS |
| mem_reads | `index_join_scan` | 3.11ms | 4.34ms | 1.4× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 1.18s | 1.22s | 1.0× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 101.62ms | 117.63ms | 1.2× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 183.58ms | 277.00ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 15.32ms | 28.17ms | 1.8× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 48.17ms | 93.58ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 33.46ms | 59.17ms | 1.8× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 43.35ms | 72.84ms | 1.7× | 0.8% | PASS |
| mem_writes | `oltp_write_only` | 20.97ms | 46.41ms | 2.2× | 0.7% | PASS |
| mem_writes | `types_delete_insert` | 23.68ms | 38.22ms | 1.6× | 0.8% | PASS |
| mem_writes | `oltp_read_write` | 64.79ms | 112.12ms | 1.7× | 0.9% | PASS |
| file_reads | `oltp_point_select` | 97.53ms | 49.30ms | 0.5× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 17.08ms | 13.59ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 16.68ms | 13.45ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 3.35ms | 3.10ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 4.49ms | 4.26ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 11.37ms | 7.49ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_points` | 16.71ms | 13.04ms | 0.8× | 1.4% | PASS |
| file_reads | `select_random_ranges` | 10.30ms | 5.94ms | 0.6× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 11.81ms | 6.45ms | 0.5× | 1.0% | PASS |
| file_reads | `groupby_scan` | 31.29ms | 33.71ms | 1.1× | 0.8% | PASS |
| file_reads | `index_join` | 9.95ms | 9.53ms | 1.0× | 1.1% | PASS |
| file_reads | `index_join_scan` | 4.10ms | 4.77ms | 1.2× | 1.3% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.17s | 1.22s | 1.0× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 209.48ms | 147.71ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 196.89ms | 285.87ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 21.67ms | 32.18ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 74.57ms | 104.74ms | 1.4× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 57.17ms | 70.22ms | 1.2× | 1.5% | PASS |
| file_writes | `oltp_delete_insert` | 66.31ms | 82.20ms | 1.2× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 43.18ms | 53.99ms | 1.3× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 38.79ms | 44.35ms | 1.1× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 88.44ms | 120.03ms | 1.4× | 1.2% | PASS |
| ac_reads | `oltp_point_select` | 48.09ms | 49.37ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 12.74ms | 13.65ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 12.10ms | 13.53ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 2.98ms | 3.17ms | 1.1× | 2.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.07ms | 4.31ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.51ms | 7.47ms | 1.1× | 1.5% | PASS |
| ac_reads | `select_random_points` | 12.57ms | 13.15ms | 1.0× | 1.4% | PASS |
| ac_reads | `select_random_ranges` | 5.44ms | 5.96ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.00ms | 6.49ms | 0.9× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 30.74ms | 33.69ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 7.36ms | 9.46ms | 1.3× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 3.55ms | 4.72ms | 1.3× | 1.0% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.22s | 1.0× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 138.74ms | 148.13ms | 1.1× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.62ms | 60.81ms | 2.8× | 6.3% | PASS |
| ac_writes | `oltp_insert_ac` | 24.24ms | 76.61ms | 3.2× | 6.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.44ms | 87.45ms | 3.3× | 5.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.61ms | 69.14ms | 3.1× | 5.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.61ms | 78.75ms | 3.3× | 4.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.78ms | 77.97ms | 3.1× | 5.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.77ms | 70.17ms | 3.1× | 6.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.19ms | 83.51ms | 2.9× | 4.0% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.25ms | 37.96ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 15.81ms | 13.97ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 14.86ms | 13.65ms | 0.9× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 3.13ms | 3.13ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 4.21ms | 4.29ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.89ms | 5.99ms | 1.5× | 1.5% | PASS |
| mem_reads | `select_random_points` | 20.94ms | 20.91ms | 1.0× | 2.0% | PASS |
| mem_reads | `select_random_ranges` | 3.36ms | 5.24ms | 1.6× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 4.24ms | 4.44ms | 1.0× | 0.7% | PASS |
| mem_reads | `groupby_scan` | 34.25ms | 34.50ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 10.27ms | 9.00ms | 0.9× | 1.5% | PASS |
| mem_reads | `index_join_scan` | 3.78ms | 5.56ms | 1.5× | 2.3% | PASS |
| mem_reads | `types_table_scan` | 1.10s | 1.18s | 1.1× | 1.1% | PASS |
| mem_reads | `table_scan` | 1.23s | 1.28s | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_read_only` | 135.84ms | 136.64ms | 1.0× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 244.69ms | 372.32ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 18.22ms | 38.62ms | 2.1× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 64.62ms | 140.98ms | 2.2× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 47.29ms | 85.50ms | 1.8× | 0.9% | PASS |
| mem_writes | `oltp_delete_insert` | 53.66ms | 103.96ms | 1.9× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 27.70ms | 61.74ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 40.27ms | 54.75ms | 1.4× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 96.59ms | 146.01ms | 1.5× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 112.15ms | 58.02ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 25.20ms | 16.38ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 24.41ms | 16.13ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_order_range` | 4.23ms | 3.45ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_distinct_range` | 5.29ms | 4.63ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 11.69ms | 8.50ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 32.12ms | 24.34ms | 0.8× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 11.36ms | 7.39ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 12.21ms | 7.00ms | 0.6× | 1.8% | PASS |
| file_reads | `groupby_scan` | 35.80ms | 35.11ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 14.63ms | 11.52ms | 0.8× | 3.4% | PASS |
| file_reads | `index_join_scan` | 4.69ms | 6.38ms | 1.4× | 3.0% | PASS |
| file_reads | `types_table_scan` | 1.08s | 1.17s | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 1.21s | 1.28s | 1.1× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 246.57ms | 167.49ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 269.80ms | 386.03ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 25.92ms | 45.99ms | 1.8× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 135.22ms | 166.32ms | 1.2× | 5.6% | PASS |
| file_writes | `oltp_update_non_index` | 107.31ms | 103.27ms | 1.0× | 14.0% | PASS |
| file_writes | `oltp_delete_insert` | 99.36ms | 122.09ms | 1.2× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 88.52ms | 74.94ms | 0.8× | 10.4% | PASS |
| file_writes | `types_delete_insert` | 73.73ms | 64.33ms | 0.9× | 2.5% | PASS |
| file_writes | `oltp_read_write` | 163.10ms | 161.95ms | 1.0× | 9.7% | PASS |
| ac_reads | `oltp_point_select` | 62.26ms | 58.10ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 19.96ms | 16.40ms | 0.8× | 3.2% | PASS |
| ac_reads | `oltp_sum_range` | 18.55ms | 16.34ms | 0.9× | 3.6% | PASS |
| ac_reads | `oltp_order_range` | 3.65ms | 3.54ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.82ms | 4.71ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 6.74ms | 8.56ms | 1.3× | 1.8% | PASS |
| ac_reads | `select_random_points` | 26.42ms | 24.58ms | 0.9× | 3.5% | PASS |
| ac_reads | `select_random_ranges` | 6.25ms | 7.41ms | 1.2× | 1.8% | PASS |
| ac_reads | `covering_index_scan` | 7.21ms | 6.93ms | 1.0× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 35.17ms | 35.21ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 12.22ms | 11.48ms | 0.9× | 3.1% | PASS |
| ac_reads | `index_join_scan` | 4.37ms | 6.05ms | 1.4× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 1.10s | 1.18s | 1.1× | 1.3% | PASS |
| ac_reads | `table_scan` | 1.29s | 1.30s | 1.0× | 3.6% | PASS |
| ac_reads | `oltp_read_only` | 185.62ms | 171.48ms | 0.9× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 25.78ms | 65.62ms | 2.5× | 9.3% | PASS |
| ac_writes | `oltp_insert_ac` | 28.40ms | 75.73ms | 2.7× | 8.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.18ms | 90.61ms | 3.2× | 6.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.92ms | 74.48ms | 3.0× | 7.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.59ms | 82.79ms | 2.9× | 6.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.39ms | 83.11ms | 3.0× | 7.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 26.36ms | 79.99ms | 3.0× | 9.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.84ms | 88.22ms | 2.6× | 5.5% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.23ms | 38.13ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 16.31ms | 14.03ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 15.23ms | 13.61ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 3.33ms | 3.22ms | 1.0× | 3.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.42ms | 4.47ms | 1.0× | 3.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.03ms | 6.26ms | 1.6× | 1.7% | PASS |
| mem_reads | `select_random_points` | 21.33ms | 20.83ms | 1.0× | 2.2% | PASS |
| mem_reads | `select_random_ranges` | 3.42ms | 5.29ms | 1.5× | 2.1% | PASS |
| mem_reads | `covering_index_scan` | 4.23ms | 4.46ms | 1.1× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 34.31ms | 34.42ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 10.05ms | 9.31ms | 0.9× | 2.6% | PASS |
| mem_reads | `index_join_scan` | 3.74ms | 5.76ms | 1.5× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 1.22s | 1.25s | 1.0× | 2.6% | PASS |
| mem_reads | `table_scan` | 1.37s | 1.35s | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 157.66ms | 145.50ms | 0.9× | 1.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 248.99ms | 379.26ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 19.23ms | 40.10ms | 2.1× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 68.65ms | 150.20ms | 2.2× | 2.6% | PASS |
| mem_writes | `oltp_update_non_index` | 50.31ms | 87.13ms | 1.7× | 2.9% | PASS |
| mem_writes | `oltp_delete_insert` | 56.08ms | 110.36ms | 2.0× | 2.5% | PASS |
| mem_writes | `oltp_write_only` | 29.77ms | 66.13ms | 2.2× | 2.6% | PASS |
| mem_writes | `types_delete_insert` | 40.53ms | 55.63ms | 1.4× | 2.6% | PASS |
| mem_writes | `oltp_read_write` | 113.22ms | 158.07ms | 1.4× | 2.3% | PASS |
| file_reads | `oltp_point_select` | 117.47ms | 60.32ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 27.21ms | 17.43ms | 0.6× | 2.8% | PASS |
| file_reads | `oltp_sum_range` | 25.42ms | 16.82ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 4.96ms | 4.03ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 6.23ms | 5.21ms | 0.8× | 2.8% | PASS |
| file_reads | `oltp_index_scan` | 12.35ms | 8.81ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 34.09ms | 25.68ms | 0.8× | 2.8% | PASS |
| file_reads | `select_random_ranges` | 11.46ms | 7.57ms | 0.7× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 12.67ms | 7.04ms | 0.6× | 1.1% | PASS |
| file_reads | `groupby_scan` | 37.33ms | 35.89ms | 1.0× | 1.4% | PASS |
| file_reads | `index_join` | 17.74ms | 12.81ms | 0.7× | 3.2% | PASS |
| file_reads | `index_join_scan` | 5.17ms | 6.75ms | 1.3× | 3.0% | PASS |
| file_reads | `types_table_scan` | 1.20s | 1.22s | 1.0× | 1.8% | PASS |
| file_reads | `table_scan` | 1.38s | 1.35s | 1.0× | 1.0% | PASS |
| file_reads | `oltp_read_only` | 271.28ms | 174.48ms | 0.6× | 1.4% | PASS |
| file_writes | `oltp_bulk_insert` | 273.51ms | 394.31ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 28.31ms | 49.34ms | 1.7× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 107.39ms | 171.18ms | 1.6× | 3.4% | PASS |
| file_writes | `oltp_update_non_index` | 90.52ms | 103.77ms | 1.1× | 6.7% | PASS |
| file_writes | `oltp_delete_insert` | 95.10ms | 124.95ms | 1.3× | 2.4% | PASS |
| file_writes | `oltp_write_only` | 62.95ms | 76.60ms | 1.2× | 2.1% | PASS |
| file_writes | `types_delete_insert` | 70.31ms | 65.97ms | 0.9× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 150.91ms | 171.28ms | 1.1× | 2.3% | PASS |
| ac_reads | `oltp_point_select` | 68.85ms | 62.42ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_range_select` | 22.69ms | 17.63ms | 0.8× | 2.6% | PASS |
| ac_reads | `oltp_sum_range` | 20.47ms | 17.12ms | 0.8× | 2.2% | PASS |
| ac_reads | `oltp_order_range` | 4.40ms | 3.81ms | 0.9× | 2.6% | PASS |
| ac_reads | `oltp_distinct_range` | 5.46ms | 5.01ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 7.11ms | 8.72ms | 1.2× | 1.5% | PASS |
| ac_reads | `select_random_points` | 25.57ms | 24.40ms | 1.0× | 2.8% | PASS |
| ac_reads | `select_random_ranges` | 6.43ms | 7.56ms | 1.2× | 1.7% | PASS |
| ac_reads | `covering_index_scan` | 7.66ms | 7.07ms | 0.9× | 2.0% | PASS |
| ac_reads | `groupby_scan` | 36.61ms | 35.77ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 12.65ms | 11.41ms | 0.9× | 2.0% | PASS |
| ac_reads | `index_join_scan` | 4.46ms | 6.31ms | 1.4× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 1.12s | 1.20s | 1.1× | 2.2% | PASS |
| ac_reads | `table_scan` | 1.30s | 1.31s | 1.0× | 2.4% | PASS |
| ac_reads | `oltp_read_only` | 186.45ms | 170.31ms | 0.9× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.70ms | 64.89ms | 2.7× | 3.7% | PASS |
| ac_writes | `oltp_insert_ac` | 27.50ms | 84.04ms | 3.1× | 7.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.80ms | 94.84ms | 3.3× | 7.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 29.04ms | 93.66ms | 3.2× | 6.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 32.99ms | 107.08ms | 3.2× | 6.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 33.40ms | 106.65ms | 3.2× | 6.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 30.84ms | 101.69ms | 3.3× | 10.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 39.89ms | 111.36ms | 2.8× | 6.7% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.93ms | 40.74ms | 1.2× | 1.1% | PASS |
| mem_reads | `oltp_range_select` | 19.59ms | 20.94ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 17.44ms | 20.47ms | 1.2× | 1.2% | PASS |
| mem_reads | `oltp_order_range` | 3.46ms | 3.81ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 4.60ms | 4.99ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.41ms | 5.95ms | 1.3× | 1.5% | PASS |
| mem_reads | `select_random_points` | 27.08ms | 32.23ms | 1.2× | 1.6% | PASS |
| mem_reads | `select_random_ranges` | 7.57ms | 8.95ms | 1.2× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 4.21ms | 4.28ms | 1.0× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 36.09ms | 39.69ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 7.89ms | 10.36ms | 1.3× | 1.4% | PASS |
| mem_reads | `index_join_scan` | 3.84ms | 5.63ms | 1.5× | 2.0% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.18s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 147.42ms | 170.11ms | 1.2× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 252.23ms | 353.50ms | 1.4× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 19.11ms | 35.07ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 65.24ms | 124.17ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 50.16ms | 83.34ms | 1.7× | 1.4% | PASS |
| mem_writes | `oltp_delete_insert` | 48.49ms | 94.28ms | 1.9× | 0.7% | PASS |
| mem_writes | `oltp_write_only` | 26.35ms | 57.53ms | 2.2× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 32.02ms | 52.71ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 100.45ms | 161.10ms | 1.6× | 1.5% | PASS |
| file_reads | `oltp_point_select` | 108.47ms | 60.55ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 27.00ms | 23.34ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 25.00ms | 22.92ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_order_range` | 4.38ms | 4.14ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 5.52ms | 5.35ms | 1.0× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 12.11ms | 8.44ms | 0.7× | 1.1% | PASS |
| file_reads | `select_random_points` | 37.55ms | 36.00ms | 1.0× | 1.4% | PASS |
| file_reads | `select_random_ranges` | 15.70ms | 11.28ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 11.89ms | 6.75ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 37.74ms | 40.04ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 12.35ms | 12.65ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join_scan` | 4.94ms | 6.10ms | 1.2× | 1.7% | PASS |
| file_reads | `types_table_scan` | 1.10s | 1.19s | 1.1× | 1.7% | PASS |
| file_reads | `table_scan` | 1.33s | 1.30s | 1.0× | 3.4% | PASS |
| file_reads | `oltp_read_only` | 262.93ms | 202.59ms | 0.8× | 1.6% | PASS |
| file_writes | `oltp_bulk_insert` | 267.47ms | 364.91ms | 1.4× | 0.8% | PASS |
| file_writes | `oltp_insert` | 26.15ms | 40.78ms | 1.6× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 94.38ms | 137.28ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 75.86ms | 96.66ms | 1.3× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 75.60ms | 107.35ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 50.47ms | 67.79ms | 1.3× | 2.4% | PASS |
| file_writes | `types_delete_insert` | 48.80ms | 59.16ms | 1.2× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 125.09ms | 170.77ms | 1.4× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 57.52ms | 60.85ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 23.01ms | 23.39ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 20.97ms | 23.05ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 3.97ms | 4.16ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 5.13ms | 5.36ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 7.47ms | 8.53ms | 1.1× | 2.0% | PASS |
| ac_reads | `select_random_points` | 32.28ms | 36.76ms | 1.1× | 1.9% | PASS |
| ac_reads | `select_random_ranges` | 10.54ms | 11.38ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.14ms | 6.76ms | 0.9× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 36.78ms | 40.03ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 9.64ms | 12.51ms | 1.3× | 1.2% | PASS |
| ac_reads | `index_join_scan` | 4.34ms | 6.07ms | 1.4× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.18s | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.24s | 1.28s | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_read_only` | 188.17ms | 202.45ms | 1.1× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.52ms | 61.70ms | 2.7× | 6.3% | PASS |
| ac_writes | `oltp_insert_ac` | 24.32ms | 80.10ms | 3.3× | 5.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.00ms | 89.76ms | 3.3× | 4.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.64ms | 72.08ms | 2.9× | 6.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.21ms | 81.11ms | 3.4× | 4.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.99ms | 80.84ms | 3.2× | 5.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.20ms | 70.75ms | 3.0× | 5.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.44ms | 87.71ms | 2.7× | 6.0% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 13s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 96.81ms | 130.00ms | 74.5% | 0.5% | PASS |
| `status_dirty_many_tables` | 100.26ms | 130.00ms | 77.1% | 0.5% | PASS |
| `diff_regular_working_one_table` | 91.79ms | 120.00ms | 76.5% | 0.6% | PASS |
| `diff_regular_working_many_tables` | 105.19ms | 140.00ms | 75.1% | 0.6% | PASS |
| `diff_stat_working_many_tables` | 104.89ms | 140.00ms | 74.9% | 0.7% | PASS |
| `diff_schema_working_many_tables` | 105.07ms | 140.00ms | 75.1% | 0.7% | PASS |
| `branch_list_many_branches` | 23.04ms | 35.00ms | 65.8% | 1.3% | PASS |
| `branch_create_delete` | 25.54ms | 40.00ms | 63.9% | 1.1% | PASS |
| `checkout_branch_clean` | 61.24ms | 150.00ms | 40.8% | 0.9% | PASS |
| `merge_data_no_conflicts` | 28.95ms | 50.00ms | 57.9% | 1.2% | PASS |
| `merge_schema_no_conflicts` | 22.33ms | 35.00ms | 63.8% | 1.4% | PASS |
| `merge_data_conflicts` | 32.70ms | 180.00ms | 18.2% | 0.9% | PASS |
| `merge_data_conflicts_with_resolve` | 32.92ms | 180.00ms | 18.3% | 1.0% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
