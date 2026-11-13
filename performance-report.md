# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-13 18:50 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261111.196.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/43471525952)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.15s | 10.79s | 1.1× | 1.3% | **PASS** |
| Writes | 1.96s | 3.37s | 1.7× | 1.3% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.49s | 10.86s | 1.0× | 1.3% | **PASS** |
| Writes | 3.20s | 4.11s | 1.3× | 2.0% | **PASS** |
| Autocommit writes | 735.94ms | 2.27s | 3.1× | 6.4% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.25s | 2.47s | 1.1× | 1.1% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.54s | 2.73s | 1.1× | 1.1% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.44s | 2.62s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.93s | 2.97s | 1.0× | 1.8% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 368.97ms | 622.38ms | 1.7× | 1.4% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 516.74ms | 894.87ms | 1.7× | 1.2% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 474.15ms | 826.50ms | 1.7× | 1.1% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 602.89ms | 1.03s | 1.7× | 1.6% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 2.34s | 2.50s | 1.1× | 1.2% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.64s | 2.75s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.51s | 2.61s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.99s | 3.00s | 1.0× | 1.9% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 470.07ms | 703.60ms | 1.5× | 1.4% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 774.56ms | 985.90ms | 1.3× | 4.1% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.16s | 1.29s | 1.1× | 2.6% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 794.47ms | 1.12s | 1.4× | 2.0% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.23s | 2.48s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.63s | 2.80s | 1.1× | 1.5% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.44s | 2.61s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 3.03s | 3.04s | 1.0× | 2.4% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 124.97ms | 441.91ms | 3.5× | 6.5% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 157.43ms | 512.28ms | 3.3× | 7.1% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 252.96ms | 706.32ms | 2.8× | 4.7% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 200.57ms | 611.58ms | 3.0× | 6.7% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 20.81ms | 24.10ms | 1.2× | 1.0% | PASS |
| mem_reads | `oltp_range_select` | 8.99ms | 9.84ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_sum_range` | 8.68ms | 9.72ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 2.35ms | 2.57ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.15ms | 3.45ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.37ms | 4.47ms | 1.3× | 1.4% | PASS |
| mem_reads | `select_random_points` | 9.08ms | 10.02ms | 1.1× | 1.5% | PASS |
| mem_reads | `select_random_ranges` | 2.69ms | 3.27ms | 1.2× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 3.58ms | 3.61ms | 1.0× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 27.71ms | 30.27ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 4.93ms | 7.11ms | 1.4× | 1.4% | PASS |
| mem_reads | `index_join_scan` | 2.69ms | 4.08ms | 1.5× | 1.1% | PASS |
| mem_reads | `types_table_scan` | 934.50ms | 1.06s | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.12s | 1.19s | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 92.55ms | 103.45ms | 1.1× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 150.54ms | 225.86ms | 1.5× | 1.7% | PASS |
| mem_writes | `oltp_insert` | 12.91ms | 23.84ms | 1.8× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 43.06ms | 85.37ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 28.62ms | 50.38ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 37.30ms | 64.52ms | 1.7× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 18.02ms | 40.51ms | 2.2× | 1.1% | PASS |
| mem_writes | `types_delete_insert` | 20.46ms | 33.82ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 58.06ms | 98.10ms | 1.7× | 1.5% | PASS |
| file_reads | `oltp_point_select` | 48.91ms | 32.13ms | 0.7× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 11.88ms | 10.83ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_sum_range` | 11.56ms | 10.70ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 2.68ms | 2.72ms | 1.0× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 3.50ms | 3.65ms | 1.0× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 6.30ms | 5.48ms | 0.9× | 1.8% | PASS |
| file_reads | `select_random_points` | 12.13ms | 10.93ms | 0.9× | 1.7% | PASS |
| file_reads | `select_random_ranges` | 5.44ms | 4.01ms | 0.7× | 1.8% | PASS |
| file_reads | `covering_index_scan` | 6.51ms | 4.44ms | 0.7× | 2.3% | PASS |
| file_reads | `groupby_scan` | 28.15ms | 30.54ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 6.72ms | 7.92ms | 1.2× | 1.1% | PASS |
| file_reads | `index_join_scan` | 3.13ms | 4.25ms | 1.4× | 1.2% | PASS |
| file_reads | `types_table_scan` | 947.12ms | 1.06s | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 1.11s | 1.19s | 1.1× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 134.78ms | 116.73ms | 0.9× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 161.42ms | 236.73ms | 1.5× | 2.0% | PASS |
| file_writes | `oltp_insert` | 17.49ms | 26.73ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 60.89ms | 103.50ms | 1.7× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 43.89ms | 63.61ms | 1.4× | 1.3% | PASS |
| file_writes | `oltp_delete_insert` | 51.32ms | 79.46ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_write_only` | 31.38ms | 49.24ms | 1.6× | 1.4% | PASS |
| file_writes | `types_delete_insert` | 30.39ms | 37.11ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 73.30ms | 107.22ms | 1.5× | 0.9% | PASS |
| ac_reads | `oltp_point_select` | 30.65ms | 32.47ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 10.31ms | 10.86ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_sum_range` | 9.91ms | 10.80ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 2.55ms | 2.73ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 3.34ms | 3.62ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 4.49ms | 5.49ms | 1.2× | 1.5% | PASS |
| ac_reads | `select_random_points` | 10.33ms | 10.92ms | 1.1× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 3.66ms | 4.02ms | 1.1× | 1.9% | PASS |
| ac_reads | `covering_index_scan` | 4.68ms | 4.50ms | 1.0× | 2.3% | PASS |
| ac_reads | `groupby_scan` | 27.90ms | 30.47ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 5.78ms | 7.94ms | 1.4× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 2.96ms | 4.24ms | 1.4× | 1.6% | PASS |
| ac_reads | `types_table_scan` | 942.24ms | 1.06s | 1.1× | 1.1% | PASS |
| ac_reads | `table_scan` | 1.07s | 1.17s | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 103.80ms | 114.73ms | 1.1× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 13.96ms | 42.64ms | 3.1× | 6.6% | PASS |
| ac_writes | `oltp_insert_ac` | 16.07ms | 55.71ms | 3.5× | 4.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 16.77ms | 63.47ms | 3.8× | 6.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 14.15ms | 49.38ms | 3.5× | 6.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 15.19ms | 60.14ms | 4.0× | 6.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 15.66ms | 58.67ms | 3.7× | 7.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 14.06ms | 48.97ms | 3.5× | 6.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 19.12ms | 62.94ms | 3.3× | 6.0% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 31.18ms | 32.54ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_range_select` | 14.48ms | 12.12ms | 0.8× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 14.04ms | 12.20ms | 0.9× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 2.94ms | 2.85ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_distinct_range` | 3.76ms | 3.76ms | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.51ms | 5.41ms | 1.5× | 0.7% | PASS |
| mem_reads | `select_random_points` | 20.10ms | 19.87ms | 1.0× | 1.8% | PASS |
| mem_reads | `select_random_ranges` | 2.98ms | 4.51ms | 1.5× | 0.9% | PASS |
| mem_reads | `covering_index_scan` | 3.58ms | 4.00ms | 1.1× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 32.17ms | 31.81ms | 1.0× | 0.5% | PASS |
| mem_reads | `index_join` | 9.70ms | 8.48ms | 0.9× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 3.15ms | 5.02ms | 1.6× | 1.1% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.18s | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.23s | 1.29s | 1.0× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 130.80ms | 126.15ms | 1.0× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 194.53ms | 306.44ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 15.52ms | 34.94ms | 2.3× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 61.32ms | 138.78ms | 2.3× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 44.34ms | 78.59ms | 1.8× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 48.13ms | 96.96ms | 2.0× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 24.70ms | 56.60ms | 2.3× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 34.64ms | 48.95ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 93.56ms | 133.60ms | 1.4× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 62.75ms | 41.27ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 18.13ms | 13.21ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 17.78ms | 13.37ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 3.34ms | 3.00ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 4.17ms | 3.90ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 6.79ms | 6.49ms | 1.0× | 1.3% | PASS |
| file_reads | `select_random_points` | 26.80ms | 22.25ms | 0.8× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 6.11ms | 5.38ms | 0.9× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 6.72ms | 4.90ms | 0.7× | 1.0% | PASS |
| file_reads | `groupby_scan` | 32.99ms | 32.21ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 11.94ms | 9.50ms | 0.8× | 2.1% | PASS |
| file_reads | `index_join_scan` | 3.67ms | 5.35ms | 1.5× | 1.5% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.18s | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 1.23s | 1.27s | 1.0× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 173.63ms | 137.17ms | 0.8× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 213.13ms | 317.88ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_insert` | 20.51ms | 40.50ms | 2.0× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 110.70ms | 150.12ms | 1.4× | 6.5% | PASS |
| file_writes | `oltp_update_non_index` | 91.49ms | 90.77ms | 1.0× | 15.9% | PASS |
| file_writes | `oltp_delete_insert` | 76.67ms | 112.45ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 63.69ms | 67.79ms | 1.1× | 13.4% | PASS |
| file_writes | `types_delete_insert` | 56.12ms | 58.29ms | 1.0× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 142.25ms | 148.11ms | 1.0× | 10.2% | PASS |
| ac_reads | `oltp_point_select` | 44.56ms | 42.30ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 16.78ms | 13.76ms | 0.8× | 1.7% | PASS |
| ac_reads | `oltp_sum_range` | 15.89ms | 13.59ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 3.20ms | 3.04ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_distinct_range` | 3.98ms | 3.92ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 4.94ms | 6.64ms | 1.3× | 1.5% | PASS |
| ac_reads | `select_random_points` | 26.88ms | 23.58ms | 0.9× | 2.1% | PASS |
| ac_reads | `select_random_ranges` | 4.19ms | 5.46ms | 1.3× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 4.88ms | 5.03ms | 1.0× | 1.8% | PASS |
| ac_reads | `groupby_scan` | 33.13ms | 32.54ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 12.39ms | 10.36ms | 0.8× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 3.59ms | 5.92ms | 1.6× | 3.1% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.19s | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.26s | 1.30s | 1.0× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 151.15ms | 143.19ms | 0.9× | 0.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.77ms | 51.17ms | 2.9× | 6.9% | PASS |
| ac_writes | `oltp_insert_ac` | 20.88ms | 62.99ms | 3.0× | 7.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.66ms | 74.43ms | 3.6× | 7.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.68ms | 57.98ms | 3.5× | 6.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 20.53ms | 67.40ms | 3.3× | 6.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 18.89ms | 66.97ms | 3.5× | 8.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 17.95ms | 59.28ms | 3.3× | 9.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 24.06ms | 72.06ms | 3.0× | 5.3% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 30.07ms | 30.19ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_range_select` | 13.27ms | 11.82ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_sum_range` | 12.73ms | 11.34ms | 0.9× | 1.8% | PASS |
| mem_reads | `oltp_order_range` | 2.89ms | 2.84ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.75ms | 3.78ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.47ms | 5.05ms | 1.5× | 1.6% | PASS |
| mem_reads | `select_random_points` | 19.78ms | 18.79ms | 1.0× | 1.4% | PASS |
| mem_reads | `select_random_ranges` | 2.82ms | 4.46ms | 1.6× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 3.37ms | 3.81ms | 1.1× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 32.88ms | 32.93ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 10.18ms | 8.20ms | 0.8× | 1.9% | PASS |
| mem_reads | `index_join_scan` | 3.15ms | 5.13ms | 1.6× | 1.7% | PASS |
| mem_reads | `types_table_scan` | 1.00s | 1.13s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.18s | 1.23s | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_read_only` | 119.61ms | 118.75ms | 1.0× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.21ms | 281.94ms | 1.5× | 0.6% | PASS |
| mem_writes | `oltp_insert` | 14.96ms | 32.42ms | 2.2× | 1.4% | PASS |
| mem_writes | `oltp_update_index` | 55.87ms | 127.41ms | 2.3× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 40.02ms | 73.50ms | 1.8× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 44.61ms | 89.45ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 23.05ms | 53.21ms | 2.3× | 1.1% | FAIL |
| mem_writes | `types_delete_insert` | 31.79ms | 45.77ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_read_write` | 79.64ms | 122.81ms | 1.5× | 1.2% | PASS |
| file_reads | `oltp_point_select` | 60.18ms | 37.11ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 16.97ms | 12.89ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 16.21ms | 12.42ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 3.33ms | 3.04ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 4.24ms | 3.99ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_index_scan` | 6.78ms | 6.14ms | 0.9× | 1.6% | PASS |
| file_reads | `select_random_points` | 24.44ms | 20.42ms | 0.8× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 5.91ms | 5.28ms | 0.9× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 6.53ms | 4.78ms | 0.7× | 1.3% | PASS |
| file_reads | `groupby_scan` | 33.29ms | 32.83ms | 1.0× | 0.6% | PASS |
| file_reads | `index_join` | 12.29ms | 9.49ms | 0.8× | 1.0% | PASS |
| file_reads | `index_join_scan` | 3.58ms | 5.39ms | 1.5× | 1.6% | PASS |
| file_reads | `types_table_scan` | 1.00s | 1.12s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.16s | 1.22s | 1.1× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 160.79ms | 125.82ms | 0.8× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 257.20ms | 353.33ms | 1.4× | 2.4% | PASS |
| file_writes | `oltp_insert` | 29.84ms | 58.41ms | 2.0× | 9.5% | PASS |
| file_writes | `oltp_update_index` | 163.14ms | 206.84ms | 1.3× | 2.2% | PASS |
| file_writes | `oltp_update_non_index` | 145.12ms | 133.39ms | 0.9× | 2.7% | PASS |
| file_writes | `oltp_delete_insert` | 162.11ms | 159.97ms | 1.0× | 3.0% | PASS |
| file_writes | `oltp_write_only` | 111.17ms | 112.42ms | 1.0× | 5.4% | PASS |
| file_writes | `types_delete_insert` | 118.42ms | 91.67ms | 0.8× | 2.3% | PASS |
| file_writes | `oltp_read_write` | 174.85ms | 178.68ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_point_select` | 39.57ms | 36.33ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 14.70ms | 12.74ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 14.12ms | 12.39ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.14ms | 3.04ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.07ms | 4.03ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 4.87ms | 6.06ms | 1.2× | 2.1% | PASS |
| ac_reads | `select_random_points` | 22.09ms | 20.33ms | 0.9× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 3.99ms | 5.31ms | 1.3× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 4.59ms | 4.78ms | 1.0× | 1.8% | PASS |
| ac_reads | `groupby_scan` | 33.13ms | 33.09ms | 1.0× | 1.1% | PASS |
| ac_reads | `index_join` | 11.45ms | 9.46ms | 0.8× | 1.1% | PASS |
| ac_reads | `index_join_scan` | 3.46ms | 5.42ms | 1.6× | 1.5% | PASS |
| ac_reads | `types_table_scan` | 1.00s | 1.12s | 1.1× | 0.3% | PASS |
| ac_reads | `table_scan` | 1.15s | 1.21s | 1.1× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 131.10ms | 125.68ms | 1.0× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 30.96ms | 74.56ms | 2.4× | 4.1% | PASS |
| ac_writes | `oltp_insert_ac` | 32.36ms | 89.76ms | 2.8× | 4.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 32.21ms | 98.01ms | 3.0× | 5.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 28.90ms | 82.37ms | 2.8× | 4.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 31.66ms | 92.06ms | 2.9× | 5.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 30.94ms | 90.57ms | 2.9× | 4.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 30.28ms | 82.13ms | 2.7× | 8.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 35.65ms | 96.86ms | 2.7× | 4.6% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.99ms | 41.51ms | 1.2× | 2.2% | PASS |
| mem_reads | `oltp_range_select` | 21.74ms | 22.20ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 18.50ms | 21.11ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_order_range` | 3.78ms | 4.45ms | 1.2× | 1.8% | PASS |
| mem_reads | `oltp_distinct_range` | 4.88ms | 5.71ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_index_scan` | 4.82ms | 6.56ms | 1.4× | 2.7% | PASS |
| mem_reads | `select_random_points` | 29.02ms | 33.20ms | 1.1× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 8.03ms | 9.20ms | 1.1× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 4.37ms | 4.85ms | 1.1× | 3.8% | PASS |
| mem_reads | `groupby_scan` | 38.10ms | 41.94ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 8.43ms | 11.82ms | 1.4× | 2.5% | PASS |
| mem_reads | `index_join_scan` | 4.50ms | 5.98ms | 1.3× | 4.3% | PASS |
| mem_reads | `types_table_scan` | 1.17s | 1.23s | 1.1× | 1.3% | PASS |
| mem_reads | `table_scan` | 1.42s | 1.34s | 0.9× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 163.48ms | 188.19ms | 1.2× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 244.40ms | 365.70ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 18.92ms | 36.88ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_update_index` | 69.22ms | 141.79ms | 2.0× | 2.9% | PASS |
| mem_writes | `oltp_update_non_index` | 51.98ms | 89.97ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 50.57ms | 103.73ms | 2.1× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 28.09ms | 64.78ms | 2.3× | 1.8% | FAIL |
| mem_writes | `types_delete_insert` | 33.12ms | 56.30ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 106.60ms | 170.96ms | 1.6× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 105.26ms | 60.04ms | 0.6× | 1.8% | PASS |
| file_reads | `oltp_range_select` | 30.44ms | 24.25ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 27.16ms | 23.73ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 5.20ms | 4.92ms | 0.9× | 2.8% | PASS |
| file_reads | `oltp_distinct_range` | 6.30ms | 6.14ms | 1.0× | 3.6% | PASS |
| file_reads | `oltp_index_scan` | 11.95ms | 8.35ms | 0.7× | 1.9% | PASS |
| file_reads | `select_random_points` | 37.27ms | 35.66ms | 1.0× | 1.4% | PASS |
| file_reads | `select_random_ranges` | 15.38ms | 11.12ms | 0.7× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 11.78ms | 6.56ms | 0.6× | 1.9% | PASS |
| file_reads | `groupby_scan` | 39.47ms | 42.55ms | 1.1× | 1.4% | PASS |
| file_reads | `index_join` | 12.66ms | 12.59ms | 1.0× | 3.0% | PASS |
| file_reads | `index_join_scan` | 5.63ms | 6.19ms | 1.1× | 4.2% | PASS |
| file_reads | `types_table_scan` | 1.16s | 1.23s | 1.1× | 2.5% | PASS |
| file_reads | `table_scan` | 1.25s | 1.31s | 1.0× | 3.8% | PASS |
| file_reads | `oltp_read_only` | 266.96ms | 213.70ms | 0.8× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 258.35ms | 372.97ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.26ms | 43.16ms | 1.6× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 100.24ms | 153.39ms | 1.5× | 2.0% | PASS |
| file_writes | `oltp_update_non_index` | 81.33ms | 106.14ms | 1.3× | 2.0% | PASS |
| file_writes | `oltp_delete_insert` | 80.85ms | 120.34ms | 1.5× | 2.7% | PASS |
| file_writes | `oltp_write_only` | 53.26ms | 75.39ms | 1.4× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 50.77ms | 63.84ms | 1.3× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 143.44ms | 189.10ms | 1.3× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 57.65ms | 61.07ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 24.98ms | 24.65ms | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_sum_range` | 22.30ms | 23.84ms | 1.1× | 2.4% | PASS |
| ac_reads | `oltp_order_range` | 4.90ms | 5.03ms | 1.0× | 3.6% | PASS |
| ac_reads | `oltp_distinct_range` | 6.02ms | 6.21ms | 1.0× | 2.7% | PASS |
| ac_reads | `oltp_index_scan` | 7.71ms | 8.51ms | 1.1× | 3.0% | PASS |
| ac_reads | `select_random_points` | 31.80ms | 35.97ms | 1.1× | 3.1% | PASS |
| ac_reads | `select_random_ranges` | 10.80ms | 11.35ms | 1.1× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 7.62ms | 6.70ms | 0.9× | 2.7% | PASS |
| ac_reads | `groupby_scan` | 38.97ms | 42.43ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 10.69ms | 12.86ms | 1.2× | 2.8% | PASS |
| ac_reads | `index_join_scan` | 5.08ms | 6.22ms | 1.2× | 3.6% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.24s | 1.1× | 1.2% | PASS |
| ac_reads | `table_scan` | 1.42s | 1.34s | 0.9× | 2.4% | PASS |
| ac_reads | `oltp_read_only` | 202.51ms | 220.78ms | 1.1× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.40ms | 60.25ms | 2.7× | 4.9% | PASS |
| ac_writes | `oltp_insert_ac` | 24.88ms | 76.99ms | 3.1× | 6.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.26ms | 88.51ms | 3.2× | 7.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.42ms | 70.68ms | 3.0× | 7.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.15ms | 81.21ms | 3.2× | 6.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.55ms | 78.61ms | 3.2× | 5.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.91ms | 69.31ms | 3.3× | 7.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.00ms | 86.01ms | 2.7× | 8.0% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 52s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 68.14ms | 130.00ms | 52.4% | 0.6% | PASS |
| `status_dirty_many_tables` | 71.16ms | 130.00ms | 54.7% | 0.6% | PASS |
| `diff_regular_working_one_table` | 65.27ms | 120.00ms | 54.4% | 0.5% | PASS |
| `diff_regular_working_many_tables` | 74.22ms | 140.00ms | 53.0% | 0.6% | PASS |
| `diff_stat_working_many_tables` | 74.33ms | 140.00ms | 53.1% | 0.6% | PASS |
| `diff_schema_working_many_tables` | 74.67ms | 140.00ms | 53.3% | 0.6% | PASS |
| `branch_list_many_branches` | 19.05ms | 35.00ms | 54.4% | 4.4% | PASS |
| `branch_create_delete` | 21.54ms | 40.00ms | 53.9% | 3.1% | PASS |
| `checkout_branch_clean` | 46.31ms | 150.00ms | 30.9% | 1.6% | PASS |
| `merge_data_no_conflicts` | 24.37ms | 50.00ms | 48.7% | 3.8% | PASS |
| `merge_schema_no_conflicts` | 18.95ms | 35.00ms | 54.2% | 5.0% | PASS |
| `merge_data_conflicts` | 25.76ms | 180.00ms | 14.3% | 2.9% | PASS |
| `merge_data_conflicts_with_resolve` | 25.59ms | 180.00ms | 14.2% | 3.0% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
