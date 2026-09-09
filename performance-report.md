# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-09 15:28 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/34359552590)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.79s | 9.36s | 1.1× | 1.9% | **PASS** |
| Writes | 1.76s | 2.85s | 1.6× | 1.7% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.52s | 9.66s | 1.0× | 1.7% | **PASS** |
| Writes | 5.02s | 4.59s | 0.9× | 21.3% | **PASS** |
| Autocommit writes | 3.69s | 10.15s | 2.8× | 53.9% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.01s | 2.11s | 1.1× | 1.3% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.86s | 1.96s | 1.0× | 2.2% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.29s | 2.45s | 1.1× | 3.0% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.62s | 2.84s | 1.1× | 1.6% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 316.22ms | 510.00ms | 1.6× | 1.7% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 383.97ms | 621.23ms | 1.6× | 1.8% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 452.88ms | 740.69ms | 1.6× | 2.1% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 605.30ms | 975.61ms | 1.6× | 1.1% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.10s | 2.14s | 1.0× | 1.8% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 1.97s | 2.00s | 1.0× | 1.8% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.49s | 2.59s | 1.0× | 1.6% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.97s | 2.93s | 1.0× | 1.6% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 1.08s | 928.45ms | 0.9× | 22.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.81s | 1.27s | 0.7× | 34.7% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.35s | 1.31s | 1.0× | 22.9% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 790.05ms | 1.08s | 1.4× | 2.1% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.05s | 2.14s | 1.0× | 1.8% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.90s | 1.99s | 1.0× | 2.0% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.35s | 2.50s | 1.1× | 1.6% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.93s | 2.97s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 585.72ms | 1.70s | 2.9× | 47.4% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 1.86s | 4.78s | 2.6× | 65.0% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 1.03s | 2.89s | 2.8× | 60.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 215.90ms | 772.37ms | 3.6× | 6.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 18.79ms | 20.89ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 8.59ms | 8.82ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 7.82ms | 8.66ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 2.14ms | 2.25ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 2.92ms | 3.17ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.08ms | 3.70ms | 1.2× | 1.3% | PASS |
| mem_reads | `select_random_points` | 8.87ms | 9.30ms | 1.0× | 1.7% | PASS |
| mem_reads | `select_random_ranges` | 2.34ms | 2.92ms | 1.3× | 1.7% | PASS |
| mem_reads | `covering_index_scan` | 2.94ms | 2.95ms | 1.0× | 0.9% | PASS |
| mem_reads | `groupby_scan` | 25.09ms | 26.52ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 4.46ms | 6.17ms | 1.4× | 1.3% | PASS |
| mem_reads | `index_join_scan` | 2.45ms | 3.66ms | 1.5× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 858.24ms | 916.06ms | 1.1× | 0.3% | PASS |
| mem_reads | `table_scan` | 979.01ms | 1.01s | 1.0× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 79.30ms | 88.93ms | 1.1× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 123.90ms | 183.80ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 11.14ms | 19.74ms | 1.8× | 1.7% | PASS |
| mem_writes | `oltp_update_index` | 38.95ms | 71.94ms | 1.8× | 2.0% | PASS |
| mem_writes | `oltp_update_non_index` | 25.45ms | 42.96ms | 1.7× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 34.37ms | 52.26ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 16.27ms | 32.88ms | 2.0× | 1.6% | PASS |
| mem_writes | `types_delete_insert` | 17.63ms | 26.52ms | 1.5× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 48.52ms | 79.91ms | 1.6× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 44.24ms | 26.77ms | 0.6× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 11.56ms | 9.75ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 11.05ms | 9.49ms | 0.9× | 2.2% | PASS |
| file_reads | `oltp_order_range` | 2.57ms | 2.46ms | 1.0× | 2.2% | PASS |
| file_reads | `oltp_distinct_range` | 3.37ms | 3.35ms | 1.0× | 3.1% | PASS |
| file_reads | `oltp_index_scan` | 5.93ms | 4.77ms | 0.8× | 2.6% | PASS |
| file_reads | `select_random_points` | 12.00ms | 10.33ms | 0.9× | 1.1% | PASS |
| file_reads | `select_random_ranges` | 5.12ms | 3.69ms | 0.7× | 2.7% | PASS |
| file_reads | `covering_index_scan` | 5.87ms | 3.97ms | 0.7× | 3.6% | PASS |
| file_reads | `groupby_scan` | 25.68ms | 26.65ms | 1.0× | 1.2% | PASS |
| file_reads | `index_join` | 6.20ms | 7.19ms | 1.2× | 1.8% | PASS |
| file_reads | `index_join_scan` | 2.85ms | 3.89ms | 1.4× | 1.8% | PASS |
| file_reads | `types_table_scan` | 862.70ms | 913.67ms | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 981.06ms | 1.02s | 1.0× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 117.86ms | 99.14ms | 0.8× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 212.27ms | 252.74ms | 1.2× | 21.7% | PASS |
| file_writes | `oltp_insert` | 38.06ms | 38.53ms | 1.0× | 47.8% | PASS |
| file_writes | `oltp_update_index` | 167.47ms | 149.57ms | 0.9× | 23.3% | PASS |
| file_writes | `oltp_update_non_index` | 138.38ms | 104.33ms | 0.8× | 20.2% | PASS |
| file_writes | `oltp_delete_insert` | 152.19ms | 103.82ms | 0.7× | 20.2% | PASS |
| file_writes | `oltp_write_only` | 154.28ms | 87.92ms | 0.6× | 48.3% | PASS |
| file_writes | `types_delete_insert` | 85.33ms | 66.81ms | 0.8× | 41.0% | PASS |
| file_writes | `oltp_read_write` | 127.84ms | 124.73ms | 1.0× | 7.3% | PASS |
| ac_reads | `oltp_point_select` | 27.52ms | 27.57ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_range_select` | 9.86ms | 9.97ms | 1.0× | 2.4% | PASS |
| ac_reads | `oltp_sum_range` | 9.43ms | 9.78ms | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_order_range` | 2.41ms | 2.48ms | 1.0× | 2.4% | PASS |
| ac_reads | `oltp_distinct_range` | 3.16ms | 3.28ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 4.31ms | 4.79ms | 1.1× | 2.1% | PASS |
| ac_reads | `select_random_points` | 10.33ms | 10.37ms | 1.0× | 1.4% | PASS |
| ac_reads | `select_random_ranges` | 3.36ms | 3.66ms | 1.1× | 2.1% | PASS |
| ac_reads | `covering_index_scan` | 4.08ms | 3.98ms | 1.0× | 2.6% | PASS |
| ac_reads | `groupby_scan` | 25.43ms | 26.52ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 5.33ms | 7.09ms | 1.3× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 2.72ms | 3.88ms | 1.4× | 1.1% | PASS |
| ac_reads | `types_table_scan` | 862.78ms | 912.29ms | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 983.82ms | 1.01s | 1.0× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 91.55ms | 97.59ms | 1.1× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 60.51ms | 165.93ms | 2.7× | 44.3% | PASS |
| ac_writes | `oltp_insert_ac` | 69.82ms | 185.00ms | 2.6× | 48.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 78.93ms | 212.67ms | 2.7× | 60.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 57.66ms | 161.92ms | 2.8× | 37.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 56.94ms | 209.35ms | 3.7× | 31.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 88.53ms | 261.34ms | 3.0× | 54.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 85.78ms | 256.88ms | 3.0× | 70.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 87.55ms | 250.33ms | 2.9× | 46.6% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.16ms | 23.39ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 12.09ms | 9.39ms | 0.8× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 12.24ms | 9.18ms | 0.8× | 0.9% | PASS |
| mem_reads | `oltp_order_range` | 2.36ms | 2.15ms | 0.9× | 2.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.01ms | 2.91ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 2.77ms | 3.93ms | 1.4× | 2.3% | PASS |
| mem_reads | `select_random_points` | 17.74ms | 15.69ms | 0.9× | 2.0% | PASS |
| mem_reads | `select_random_ranges` | 2.46ms | 3.53ms | 1.4× | 3.2% | PASS |
| mem_reads | `covering_index_scan` | 2.48ms | 2.82ms | 1.1× | 2.5% | PASS |
| mem_reads | `groupby_scan` | 22.20ms | 23.54ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 9.42ms | 6.72ms | 0.7× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 2.75ms | 5.04ms | 1.8× | 3.5% | PASS |
| mem_reads | `types_table_scan` | 765.37ms | 838.43ms | 1.1× | 2.2% | PASS |
| mem_reads | `table_scan` | 891.92ms | 920.73ms | 1.0× | 3.4% | PASS |
| mem_reads | `oltp_read_only` | 93.20ms | 88.66ms | 1.0× | 2.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 142.13ms | 209.43ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 11.44ms | 23.88ms | 2.1× | 1.8% | PASS |
| mem_writes | `oltp_update_index` | 46.71ms | 98.95ms | 2.1× | 2.4% | PASS |
| mem_writes | `oltp_update_non_index` | 34.19ms | 56.91ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 38.32ms | 68.69ms | 1.8× | 2.1% | PASS |
| mem_writes | `oltp_write_only` | 20.11ms | 39.55ms | 2.0× | 1.9% | PASS |
| mem_writes | `types_delete_insert` | 27.37ms | 33.56ms | 1.2× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 63.71ms | 90.25ms | 1.4× | 1.2% | PASS |
| file_reads | `oltp_point_select` | 46.37ms | 29.02ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 14.84ms | 10.22ms | 0.7× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 15.09ms | 10.18ms | 0.7× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 2.77ms | 2.33ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 3.42ms | 3.07ms | 0.9× | 2.0% | PASS |
| file_reads | `oltp_index_scan` | 5.49ms | 5.31ms | 1.0× | 1.8% | PASS |
| file_reads | `select_random_points` | 22.39ms | 17.98ms | 0.8× | 1.7% | PASS |
| file_reads | `select_random_ranges` | 4.89ms | 4.31ms | 0.9× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 5.08ms | 3.96ms | 0.8× | 2.0% | PASS |
| file_reads | `groupby_scan` | 23.00ms | 24.13ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 11.66ms | 8.25ms | 0.7× | 2.8% | PASS |
| file_reads | `index_join_scan` | 3.13ms | 5.34ms | 1.7× | 1.9% | PASS |
| file_reads | `types_table_scan` | 769.61ms | 834.28ms | 1.1× | 2.2% | PASS |
| file_reads | `table_scan` | 907.55ms | 938.26ms | 1.0× | 3.6% | PASS |
| file_reads | `oltp_read_only` | 131.95ms | 99.51ms | 0.8× | 2.1% | PASS |
| file_writes | `oltp_bulk_insert` | 323.50ms | 313.62ms | 1.0× | 26.3% | PASS |
| file_writes | `oltp_insert` | 93.25ms | 84.47ms | 0.9× | 78.8% | PASS |
| file_writes | `oltp_update_index` | 190.92ms | 188.48ms | 1.0× | 18.5% | PASS |
| file_writes | `oltp_update_non_index` | 289.46ms | 130.94ms | 0.5× | 37.1% | PASS |
| file_writes | `oltp_delete_insert` | 174.14ms | 144.09ms | 0.8× | 10.8% | PASS |
| file_writes | `oltp_write_only` | 251.72ms | 114.61ms | 0.5× | 34.8% | PASS |
| file_writes | `types_delete_insert` | 198.48ms | 125.00ms | 0.6× | 42.8% | PASS |
| file_writes | `oltp_read_write` | 286.82ms | 173.22ms | 0.6× | 34.5% | PASS |
| ac_reads | `oltp_point_select` | 32.46ms | 29.31ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 13.38ms | 10.28ms | 0.8× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 13.64ms | 10.16ms | 0.7× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 2.62ms | 2.34ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_distinct_range` | 3.26ms | 3.09ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_index_scan` | 3.96ms | 5.28ms | 1.3× | 2.2% | PASS |
| ac_reads | `select_random_points` | 21.06ms | 18.09ms | 0.9× | 2.1% | PASS |
| ac_reads | `select_random_ranges` | 3.39ms | 4.37ms | 1.3× | 1.9% | PASS |
| ac_reads | `covering_index_scan` | 3.55ms | 4.00ms | 1.1× | 2.0% | PASS |
| ac_reads | `groupby_scan` | 22.77ms | 24.19ms | 1.1× | 1.2% | PASS |
| ac_reads | `index_join` | 10.93ms | 8.00ms | 0.7× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 3.08ms | 5.36ms | 1.7× | 3.4% | PASS |
| ac_reads | `types_table_scan` | 768.70ms | 835.88ms | 1.1× | 2.6% | PASS |
| ac_reads | `table_scan` | 888.26ms | 930.35ms | 1.0× | 3.4% | PASS |
| ac_reads | `oltp_read_only` | 106.39ms | 99.08ms | 0.9× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 208.97ms | 408.83ms | 2.0× | 62.8% | PASS |
| ac_writes | `oltp_insert_ac` | 128.22ms | 455.97ms | 3.6× | 73.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 212.87ms | 447.77ms | 2.1× | 68.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 291.69ms | 745.21ms | 2.6× | 56.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 210.51ms | 470.71ms | 2.2× | 65.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 225.35ms | 798.34ms | 3.5× | 69.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 263.13ms | 795.22ms | 3.0× | 64.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 318.48ms | 657.28ms | 2.1× | 47.2% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 29.18ms | 28.64ms | 1.0× | 2.3% | PASS |
| mem_reads | `oltp_range_select` | 12.71ms | 11.40ms | 0.9× | 2.6% | PASS |
| mem_reads | `oltp_sum_range` | 12.49ms | 10.96ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 2.77ms | 2.69ms | 1.0× | 4.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.65ms | 3.69ms | 1.0× | 3.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.46ms | 4.78ms | 1.4× | 4.3% | PASS |
| mem_reads | `select_random_points` | 18.69ms | 17.69ms | 0.9× | 3.0% | PASS |
| mem_reads | `select_random_ranges` | 2.80ms | 4.26ms | 1.5× | 2.7% | PASS |
| mem_reads | `covering_index_scan` | 3.30ms | 3.55ms | 1.1× | 4.1% | PASS |
| mem_reads | `groupby_scan` | 30.38ms | 30.46ms | 1.0× | 3.0% | PASS |
| mem_reads | `index_join` | 9.82ms | 7.78ms | 0.8× | 3.1% | PASS |
| mem_reads | `index_join_scan` | 3.03ms | 4.95ms | 1.6× | 3.7% | PASS |
| mem_reads | `types_table_scan` | 969.06ms | 1.06s | 1.1× | 1.5% | PASS |
| mem_reads | `table_scan` | 1.08s | 1.15s | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 109.38ms | 109.55ms | 1.0× | 2.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 182.23ms | 264.85ms | 1.5× | 1.3% | PASS |
| mem_writes | `oltp_insert` | 14.31ms | 28.94ms | 2.0× | 2.5% | PASS |
| mem_writes | `oltp_update_index` | 52.19ms | 109.86ms | 2.1× | 2.2% | PASS |
| mem_writes | `oltp_update_non_index` | 37.51ms | 63.91ms | 1.7× | 2.2% | PASS |
| mem_writes | `oltp_delete_insert` | 41.84ms | 78.32ms | 1.9× | 1.9% | PASS |
| mem_writes | `oltp_write_only` | 21.79ms | 46.20ms | 2.1× | 2.3% | PASS |
| mem_writes | `types_delete_insert` | 29.52ms | 39.18ms | 1.3× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 73.50ms | 109.44ms | 1.5× | 1.3% | PASS |
| file_reads | `oltp_point_select` | 58.52ms | 35.66ms | 0.6× | 2.1% | PASS |
| file_reads | `oltp_range_select` | 15.84ms | 12.32ms | 0.8× | 2.4% | PASS |
| file_reads | `oltp_sum_range` | 16.06ms | 12.19ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 3.26ms | 2.95ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_distinct_range` | 4.19ms | 4.02ms | 1.0× | 2.6% | PASS |
| file_reads | `oltp_index_scan` | 6.82ms | 6.16ms | 0.9× | 1.6% | PASS |
| file_reads | `select_random_points` | 23.67ms | 19.86ms | 0.8× | 1.2% | PASS |
| file_reads | `select_random_ranges` | 6.14ms | 5.46ms | 0.9× | 2.0% | PASS |
| file_reads | `covering_index_scan` | 6.78ms | 4.95ms | 0.7× | 2.8% | PASS |
| file_reads | `groupby_scan` | 32.10ms | 31.98ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join` | 12.26ms | 9.43ms | 0.8× | 1.2% | PASS |
| file_reads | `index_join_scan` | 3.64ms | 5.53ms | 1.5× | 2.0% | PASS |
| file_reads | `types_table_scan` | 1.02s | 1.11s | 1.1× | 1.2% | PASS |
| file_reads | `table_scan` | 1.13s | 1.20s | 1.1× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 158.69ms | 125.47ms | 0.8× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 272.72ms | 359.01ms | 1.3× | 17.6% | PASS |
| file_writes | `oltp_insert` | 29.60ms | 55.99ms | 1.9× | 34.0% | PASS |
| file_writes | `oltp_update_index` | 201.68ms | 207.59ms | 1.0× | 27.8% | PASS |
| file_writes | `oltp_update_non_index` | 163.23ms | 141.69ms | 0.9× | 21.0% | PASS |
| file_writes | `oltp_delete_insert` | 203.25ms | 161.65ms | 0.8× | 21.6% | PASS |
| file_writes | `oltp_write_only` | 120.64ms | 103.53ms | 0.9× | 24.3% | PASS |
| file_writes | `types_delete_insert` | 129.97ms | 93.95ms | 0.7× | 21.1% | PASS |
| file_writes | `oltp_read_write` | 228.86ms | 185.79ms | 0.8× | 27.7% | PASS |
| ac_reads | `oltp_point_select` | 41.18ms | 38.00ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_range_select` | 14.66ms | 13.01ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 14.49ms | 12.68ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 3.09ms | 3.00ms | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.00ms | 3.96ms | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_index_scan` | 4.86ms | 6.10ms | 1.3× | 1.3% | PASS |
| ac_reads | `select_random_points` | 21.89ms | 19.89ms | 0.9× | 1.4% | PASS |
| ac_reads | `select_random_ranges` | 4.02ms | 5.32ms | 1.3× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 4.66ms | 4.95ms | 1.1× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 31.69ms | 31.87ms | 1.0× | 1.8% | PASS |
| ac_reads | `index_join` | 11.45ms | 9.52ms | 0.8× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 3.44ms | 5.46ms | 1.6× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 978.53ms | 1.07s | 1.1× | 1.5% | PASS |
| ac_reads | `table_scan` | 1.09s | 1.16s | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 123.26ms | 119.52ms | 1.0× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 108.34ms | 303.30ms | 2.8× | 53.0% | PASS |
| ac_writes | `oltp_insert_ac` | 128.47ms | 401.05ms | 3.1× | 61.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 102.40ms | 373.21ms | 3.6× | 51.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 118.66ms | 348.20ms | 2.9× | 66.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 173.37ms | 437.88ms | 2.5× | 74.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 106.65ms | 394.44ms | 3.7× | 63.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 95.02ms | 256.46ms | 2.7× | 58.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 194.90ms | 380.06ms | 1.9× | 55.3% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.58ms | 41.42ms | 1.2× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 20.40ms | 21.49ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 17.85ms | 20.55ms | 1.2× | 1.0% | PASS |
| mem_reads | `oltp_order_range` | 3.58ms | 3.83ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_distinct_range` | 4.68ms | 5.02ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 4.48ms | 6.05ms | 1.4× | 1.3% | PASS |
| mem_reads | `select_random_points` | 28.67ms | 32.64ms | 1.1× | 1.5% | PASS |
| mem_reads | `select_random_ranges` | 7.71ms | 8.98ms | 1.2× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.28ms | 4.72ms | 1.1× | 1.8% | PASS |
| mem_reads | `groupby_scan` | 37.01ms | 39.92ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 7.96ms | 10.56ms | 1.3× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.84ms | 5.70ms | 1.5× | 2.6% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.18s | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.23s | 1.28s | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_read_only` | 154.82ms | 172.29ms | 1.1× | 2.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 252.00ms | 355.53ms | 1.4× | 1.3% | PASS |
| mem_writes | `oltp_insert` | 19.16ms | 35.42ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 67.66ms | 128.34ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 53.80ms | 87.31ms | 1.6× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 49.84ms | 96.16ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 26.69ms | 57.85ms | 2.2× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 32.85ms | 53.05ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 103.30ms | 161.96ms | 1.6× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 110.00ms | 60.76ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 28.76ms | 23.74ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 26.40ms | 22.90ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 4.56ms | 4.17ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 5.70ms | 5.36ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 12.27ms | 8.39ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 38.25ms | 35.92ms | 0.9× | 1.7% | PASS |
| file_reads | `select_random_ranges` | 16.02ms | 11.21ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.89ms | 6.59ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 37.27ms | 40.03ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 12.58ms | 12.46ms | 1.0× | 2.2% | PASS |
| file_reads | `index_join_scan` | 5.09ms | 6.10ms | 1.2× | 2.1% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.19s | 1.1× | 1.4% | PASS |
| file_reads | `table_scan` | 1.31s | 1.30s | 1.0× | 4.1% | PASS |
| file_reads | `oltp_read_only` | 273.49ms | 204.07ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 266.68ms | 371.64ms | 1.4× | 1.2% | PASS |
| file_writes | `oltp_insert` | 27.05ms | 41.36ms | 1.5× | 2.3% | PASS |
| file_writes | `oltp_update_index` | 97.74ms | 143.43ms | 1.5× | 3.3% | PASS |
| file_writes | `oltp_update_non_index` | 80.28ms | 100.53ms | 1.3× | 1.9% | PASS |
| file_writes | `oltp_delete_insert` | 80.69ms | 111.85ms | 1.4× | 2.5% | PASS |
| file_writes | `oltp_write_only` | 53.18ms | 70.36ms | 1.3× | 2.4% | PASS |
| file_writes | `types_delete_insert` | 51.28ms | 60.96ms | 1.2× | 1.3% | PASS |
| file_writes | `oltp_read_write` | 133.15ms | 175.00ms | 1.3× | 1.3% | PASS |
| ac_reads | `oltp_point_select` | 60.07ms | 61.50ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 23.97ms | 23.91ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 21.27ms | 22.95ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 4.11ms | 4.21ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 5.22ms | 5.42ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 7.65ms | 8.65ms | 1.1× | 1.6% | PASS |
| ac_reads | `select_random_points` | 33.46ms | 36.52ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_ranges` | 10.79ms | 11.37ms | 1.1× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 7.27ms | 6.84ms | 0.9× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 37.34ms | 40.18ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 10.11ms | 12.68ms | 1.3× | 2.2% | PASS |
| ac_reads | `index_join_scan` | 4.52ms | 6.13ms | 1.4× | 2.3% | PASS |
| ac_reads | `types_table_scan` | 1.09s | 1.19s | 1.1× | 1.5% | PASS |
| ac_reads | `table_scan` | 1.41s | 1.33s | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 199.88ms | 205.78ms | 1.0× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.29ms | 79.97ms | 3.3× | 7.9% | PASS |
| ac_writes | `oltp_insert_ac` | 26.27ms | 96.17ms | 3.7× | 5.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.28ms | 105.72ms | 3.9× | 4.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.58ms | 90.99ms | 3.7× | 7.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.94ms | 98.99ms | 3.7× | 5.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.56ms | 101.11ms | 3.7× | 5.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.45ms | 91.80ms | 3.8× | 10.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.52ms | 107.63ms | 3.1× | 6.4% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 21s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 106.30ms | 130.00ms | 81.8% | 0.2% | PASS |
| `status_dirty_many_tables` | 110.22ms | 130.00ms | 84.8% | 0.2% | PASS |
| `diff_regular_working_one_table` | 101.65ms | 120.00ms | 84.7% | 0.2% | PASS |
| `diff_regular_working_many_tables` | 115.24ms | 140.00ms | 82.3% | 0.2% | PASS |
| `diff_stat_working_many_tables` | 115.19ms | 140.00ms | 82.3% | 0.2% | PASS |
| `diff_schema_working_many_tables` | 116.40ms | 140.00ms | 83.1% | 0.2% | PASS |
| `branch_list_many_branches` | 24.29ms | 35.00ms | 69.4% | 0.8% | PASS |
| `branch_create_delete` | 26.41ms | 40.00ms | 66.0% | 0.8% | PASS |
| `checkout_branch_clean` | 64.81ms | 150.00ms | 43.2% | 0.5% | PASS |
| `merge_data_no_conflicts` | 30.43ms | 50.00ms | 60.9% | 0.7% | PASS |
| `merge_schema_no_conflicts` | 22.58ms | 35.00ms | 64.5% | 0.7% | PASS |
| `merge_data_conflicts` | 34.49ms | 180.00ms | 19.2% | 0.5% | PASS |
| `merge_data_conflicts_with_resolve` | 34.60ms | 180.00ms | 19.2% | 0.5% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
