# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-03 15:58 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260927.320.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/37129393240)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.15s | 10.66s | 1.1× | 1.6% | **PASS** |
| Writes | 2.02s | 3.31s | 1.6× | 1.3% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.90s | 10.82s | 1.0× | 1.5% | **PASS** |
| Writes | 3.77s | 4.40s | 1.2× | 2.2% | **PASS** |
| Autocommit writes | 777.69ms | 2.36s | 3.0× | 6.2% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.30s | 2.40s | 1.0× | 6.1% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.49s | 2.72s | 1.1× | 1.3% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.51s | 2.63s | 1.0× | 1.2% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.85s | 2.91s | 1.0× | 1.7% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 393.64ms | 605.62ms | 1.5× | 3.7% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 513.10ms | 877.93ms | 1.7× | 1.2% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 493.36ms | 818.07ms | 1.7× | 1.1% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 620.41ms | 1.01s | 1.6× | 1.5% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.49s | 2.42s | 1.0× | 3.4% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.58s | 2.73s | 1.1× | 1.4% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.68s | 2.69s | 1.0× | 1.3% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.15s | 2.98s | 0.9× | 1.5% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 973.43ms | 1.01s | 1.0× | 5.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 750.13ms | 970.26ms | 1.3× | 5.3% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.24s | 1.33s | 1.1× | 1.8% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 805.08ms | 1.09s | 1.4× | 1.8% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.38s | 2.50s | 1.0× | 6.9% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.51s | 2.73s | 1.1× | 1.4% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.60s | 2.69s | 1.0× | 1.3% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.96s | 2.97s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 173.92ms | 545.63ms | 3.1× | 9.8% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 145.57ms | 483.55ms | 3.3× | 6.3% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 251.17ms | 694.60ms | 2.8× | 4.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 207.03ms | 635.56ms | 3.1× | 5.8% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 22.91ms | 25.90ms | 1.1× | 6.1% | PASS |
| mem_reads | `oltp_range_select` | 13.30ms | 14.76ms | 1.1× | 20.4% | PASS |
| mem_reads | `oltp_sum_range` | 10.36ms | 14.29ms | 1.4× | 21.3% | PASS |
| mem_reads | `oltp_order_range` | 2.96ms | 3.04ms | 1.0× | 15.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.84ms | 4.04ms | 1.1× | 7.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.53ms | 4.46ms | 1.3× | 5.4% | PASS |
| mem_reads | `select_random_points` | 9.94ms | 10.61ms | 1.1× | 4.1% | PASS |
| mem_reads | `select_random_ranges` | 2.84ms | 3.62ms | 1.3× | 5.3% | PASS |
| mem_reads | `covering_index_scan` | 3.61ms | 3.70ms | 1.0× | 7.3% | PASS |
| mem_reads | `groupby_scan` | 30.99ms | 36.96ms | 1.2× | 6.2% | PASS |
| mem_reads | `index_join` | 5.14ms | 7.28ms | 1.4× | 7.2% | PASS |
| mem_reads | `index_join_scan` | 3.32ms | 5.18ms | 1.6× | 6.0% | PASS |
| mem_reads | `types_table_scan` | 978.47ms | 1.03s | 1.0× | 3.2% | PASS |
| mem_reads | `table_scan` | 1.11s | 1.13s | 1.0× | 3.0% | PASS |
| mem_reads | `oltp_read_only` | 99.56ms | 113.95ms | 1.1× | 4.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 161.00ms | 233.80ms | 1.5× | 4.5% | PASS |
| mem_writes | `oltp_insert` | 13.42ms | 23.56ms | 1.8× | 6.0% | PASS |
| mem_writes | `oltp_update_index` | 42.29ms | 75.95ms | 1.8× | 3.6% | PASS |
| mem_writes | `oltp_update_non_index` | 29.92ms | 44.94ms | 1.5× | 2.7% | PASS |
| mem_writes | `oltp_delete_insert` | 36.97ms | 56.74ms | 1.5× | 2.2% | PASS |
| mem_writes | `oltp_write_only` | 19.15ms | 38.32ms | 2.0× | 3.1% | PASS |
| mem_writes | `types_delete_insert` | 22.32ms | 30.62ms | 1.4× | 3.7% | PASS |
| mem_writes | `oltp_read_write` | 68.57ms | 101.69ms | 1.5× | 4.5% | PASS |
| file_reads | `oltp_point_select` | 101.94ms | 46.67ms | 0.5× | 5.2% | PASS |
| file_reads | `oltp_range_select` | 19.23ms | 12.53ms | 0.7× | 14.4% | PASS |
| file_reads | `oltp_sum_range` | 17.06ms | 11.88ms | 0.7× | 5.8% | PASS |
| file_reads | `oltp_order_range` | 3.07ms | 2.56ms | 0.8× | 2.4% | PASS |
| file_reads | `oltp_distinct_range` | 3.80ms | 3.41ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 10.90ms | 6.07ms | 0.6× | 1.7% | PASS |
| file_reads | `select_random_points` | 16.83ms | 11.27ms | 0.7× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 10.06ms | 5.03ms | 0.5× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.30ms | 5.59ms | 0.5× | 1.7% | PASS |
| file_reads | `groupby_scan` | 32.17ms | 33.03ms | 1.0× | 10.1% | PASS |
| file_reads | `index_join` | 10.51ms | 9.59ms | 0.9× | 7.0% | PASS |
| file_reads | `index_join_scan` | 3.95ms | 4.70ms | 1.2× | 7.4% | PASS |
| file_reads | `types_table_scan` | 900.40ms | 944.42ms | 1.0× | 1.5% | PASS |
| file_reads | `table_scan` | 1.11s | 1.16s | 1.0× | 3.4% | PASS |
| file_reads | `oltp_read_only` | 237.17ms | 162.98ms | 0.7× | 7.6% | PASS |
| file_writes | `oltp_bulk_insert` | 220.08ms | 293.55ms | 1.3× | 5.2% | PASS |
| file_writes | `oltp_insert` | 33.50ms | 40.81ms | 1.2× | 6.7% | PASS |
| file_writes | `oltp_update_index` | 153.68ms | 153.42ms | 1.0× | 3.8% | PASS |
| file_writes | `oltp_update_non_index` | 124.93ms | 102.79ms | 0.8× | 5.3% | PASS |
| file_writes | `oltp_delete_insert` | 129.44ms | 120.77ms | 0.9× | 3.4% | PASS |
| file_writes | `oltp_write_only` | 93.57ms | 84.43ms | 0.9× | 6.5% | PASS |
| file_writes | `types_delete_insert` | 78.65ms | 62.52ms | 0.8× | 8.0% | PASS |
| file_writes | `oltp_read_write` | 139.57ms | 149.26ms | 1.1× | 5.6% | PASS |
| ac_reads | `oltp_point_select` | 50.26ms | 45.95ms | 0.9× | 7.6% | PASS |
| ac_reads | `oltp_range_select` | 19.02ms | 17.92ms | 0.9× | 23.5% | PASS |
| ac_reads | `oltp_sum_range` | 14.97ms | 16.40ms | 1.1× | 14.8% | PASS |
| ac_reads | `oltp_order_range` | 3.32ms | 3.32ms | 1.0× | 10.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.11ms | 4.49ms | 1.1× | 12.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.78ms | 7.03ms | 1.0× | 4.9% | PASS |
| ac_reads | `select_random_points` | 13.66ms | 13.08ms | 1.0× | 4.9% | PASS |
| ac_reads | `select_random_ranges` | 5.98ms | 5.83ms | 1.0× | 6.9% | PASS |
| ac_reads | `covering_index_scan` | 7.45ms | 6.57ms | 0.9× | 6.6% | PASS |
| ac_reads | `groupby_scan` | 32.03ms | 37.25ms | 1.2× | 6.1% | PASS |
| ac_reads | `index_join` | 7.12ms | 9.57ms | 1.3× | 4.6% | PASS |
| ac_reads | `index_join_scan` | 3.80ms | 5.55ms | 1.5× | 7.1% | PASS |
| ac_reads | `types_table_scan` | 966.94ms | 1.03s | 1.1× | 2.9% | PASS |
| ac_reads | `table_scan` | 1.10s | 1.14s | 1.0× | 3.3% | PASS |
| ac_reads | `oltp_read_only` | 143.42ms | 156.79ms | 1.1× | 7.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.75ms | 55.72ms | 2.8× | 15.4% | PASS |
| ac_writes | `oltp_insert_ac` | 21.44ms | 67.70ms | 3.2× | 9.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.70ms | 79.37ms | 3.2× | 9.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 19.65ms | 63.31ms | 3.2× | 14.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 21.55ms | 71.44ms | 3.3× | 9.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 21.78ms | 71.27ms | 3.3× | 15.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.36ms | 61.06ms | 3.3× | 10.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 26.70ms | 75.77ms | 2.8× | 8.4% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 31.26ms | 34.01ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 14.05ms | 12.78ms | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_sum_range` | 13.77ms | 12.78ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_order_range` | 2.93ms | 2.94ms | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_distinct_range` | 3.69ms | 4.03ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.50ms | 5.44ms | 1.6× | 1.3% | PASS |
| mem_reads | `select_random_points` | 19.97ms | 20.30ms | 1.0× | 1.5% | PASS |
| mem_reads | `select_random_ranges` | 3.07ms | 4.64ms | 1.5× | 0.8% | PASS |
| mem_reads | `covering_index_scan` | 3.64ms | 4.07ms | 1.1× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 30.66ms | 32.93ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 9.64ms | 8.74ms | 0.9× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 3.14ms | 5.17ms | 1.6× | 1.3% | PASS |
| mem_reads | `types_table_scan` | 1.00s | 1.17s | 1.2× | 1.6% | PASS |
| mem_reads | `table_scan` | 1.22s | 1.27s | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_read_only` | 129.28ms | 131.74ms | 1.0× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 204.74ms | 322.62ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 15.84ms | 33.79ms | 2.1× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 58.26ms | 128.24ms | 2.2× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 42.77ms | 73.58ms | 1.7× | 1.4% | PASS |
| mem_writes | `oltp_delete_insert` | 46.80ms | 91.32ms | 2.0× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 23.95ms | 52.34ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 33.94ms | 46.88ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 86.80ms | 129.17ms | 1.5× | 1.2% | PASS |
| file_reads | `oltp_point_select` | 65.06ms | 44.39ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 17.71ms | 14.28ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 17.30ms | 14.17ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 3.36ms | 3.15ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.15ms | 4.29ms | 1.0× | 2.6% | PASS |
| file_reads | `oltp_index_scan` | 6.93ms | 6.89ms | 1.0× | 1.5% | PASS |
| file_reads | `select_random_points` | 24.79ms | 22.21ms | 0.9× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 6.34ms | 5.75ms | 0.9× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 7.04ms | 5.30ms | 0.8× | 1.4% | PASS |
| file_reads | `groupby_scan` | 31.22ms | 33.30ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 11.65ms | 9.83ms | 0.8× | 1.5% | PASS |
| file_reads | `index_join_scan` | 3.59ms | 5.41ms | 1.5× | 1.5% | PASS |
| file_reads | `types_table_scan` | 1.02s | 1.16s | 1.1× | 1.2% | PASS |
| file_reads | `table_scan` | 1.18s | 1.25s | 1.1× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 172.07ms | 145.16ms | 0.8× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 223.24ms | 334.19ms | 1.5× | 0.7% | PASS |
| file_writes | `oltp_insert` | 20.71ms | 40.05ms | 1.9× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 106.35ms | 147.02ms | 1.4× | 8.7% | PASS |
| file_writes | `oltp_update_non_index` | 96.96ms | 88.05ms | 0.9× | 14.6% | PASS |
| file_writes | `oltp_delete_insert` | 71.60ms | 105.08ms | 1.5× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 57.75ms | 63.16ms | 1.1× | 9.6% | PASS |
| file_writes | `types_delete_insert` | 52.30ms | 54.97ms | 1.1× | 1.8% | PASS |
| file_writes | `oltp_read_write` | 121.23ms | 137.73ms | 1.1× | 10.5% | PASS |
| ac_reads | `oltp_point_select` | 41.71ms | 43.87ms | 1.1× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 15.27ms | 14.24ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 14.86ms | 14.11ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 3.10ms | 3.16ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 3.88ms | 4.25ms | 1.1× | 2.2% | PASS |
| ac_reads | `oltp_index_scan` | 4.79ms | 6.86ms | 1.4× | 1.4% | PASS |
| ac_reads | `select_random_points` | 22.25ms | 22.10ms | 1.0× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 4.20ms | 5.72ms | 1.4× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 4.82ms | 5.26ms | 1.1× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 30.84ms | 33.27ms | 1.1× | 0.6% | PASS |
| ac_reads | `index_join` | 10.83ms | 9.96ms | 0.9× | 2.0% | PASS |
| ac_reads | `index_join_scan` | 3.40ms | 5.52ms | 1.6× | 1.6% | PASS |
| ac_reads | `types_table_scan` | 993.46ms | 1.16s | 1.2× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.22s | 1.26s | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 139.43ms | 145.09ms | 1.0× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 16.82ms | 46.89ms | 2.8× | 6.2% | PASS |
| ac_writes | `oltp_insert_ac` | 18.60ms | 57.93ms | 3.1× | 6.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 18.79ms | 68.71ms | 3.7× | 4.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.21ms | 53.59ms | 3.3× | 6.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 17.71ms | 62.48ms | 3.5× | 6.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 18.27ms | 66.17ms | 3.6× | 6.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 16.81ms | 57.64ms | 3.4× | 6.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 22.37ms | 70.15ms | 3.1× | 6.5% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 31.50ms | 30.84ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_range_select` | 13.48ms | 12.12ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_sum_range` | 13.24ms | 11.58ms | 0.9× | 1.0% | PASS |
| mem_reads | `oltp_order_range` | 2.94ms | 2.89ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.83ms | 3.90ms | 1.0× | 1.6% | PASS |
| mem_reads | `oltp_index_scan` | 3.66ms | 5.24ms | 1.4× | 1.5% | PASS |
| mem_reads | `select_random_points` | 21.80ms | 20.25ms | 0.9× | 2.0% | PASS |
| mem_reads | `select_random_ranges` | 2.94ms | 4.57ms | 1.6× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 3.44ms | 3.91ms | 1.1× | 2.3% | PASS |
| mem_reads | `groupby_scan` | 31.88ms | 32.42ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 10.26ms | 8.47ms | 0.8× | 2.2% | PASS |
| mem_reads | `index_join_scan` | 3.14ms | 5.14ms | 1.6× | 1.2% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.13s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.21s | 1.24s | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 119.23ms | 118.56ms | 1.0× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 196.17ms | 289.26ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 15.61ms | 31.52ms | 2.0× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 56.94ms | 123.47ms | 2.2× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 42.08ms | 72.66ms | 1.7× | 1.4% | PASS |
| mem_writes | `oltp_delete_insert` | 45.33ms | 86.28ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 23.55ms | 50.86ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 32.64ms | 43.71ms | 1.3× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 81.04ms | 120.30ms | 1.5× | 1.0% | PASS |
| file_reads | `oltp_point_select` | 64.47ms | 38.96ms | 0.6× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 16.95ms | 13.12ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_sum_range` | 17.00ms | 12.72ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 3.36ms | 3.02ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 4.29ms | 4.06ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 7.11ms | 6.34ms | 0.9× | 1.3% | PASS |
| file_reads | `select_random_points` | 24.47ms | 20.41ms | 0.8× | 1.4% | PASS |
| file_reads | `select_random_ranges` | 6.42ms | 5.62ms | 0.9× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 6.99ms | 5.09ms | 0.7× | 1.5% | PASS |
| file_reads | `groupby_scan` | 32.59ms | 32.34ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 12.68ms | 9.75ms | 0.8× | 1.7% | PASS |
| file_reads | `index_join_scan` | 3.69ms | 5.58ms | 1.5× | 1.9% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.12s | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 1.27s | 1.28s | 1.0× | 1.8% | PASS |
| file_reads | `oltp_read_only` | 170.07ms | 132.15ms | 0.8× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 268.85ms | 359.60ms | 1.3× | 1.1% | PASS |
| file_writes | `oltp_insert` | 31.40ms | 61.23ms | 2.0× | 5.0% | PASS |
| file_writes | `oltp_update_index` | 175.52ms | 213.29ms | 1.2× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 147.15ms | 138.30ms | 0.9× | 1.1% | PASS |
| file_writes | `oltp_delete_insert` | 176.19ms | 163.57ms | 0.9× | 2.2% | PASS |
| file_writes | `oltp_write_only` | 125.35ms | 112.39ms | 0.9× | 1.4% | PASS |
| file_writes | `types_delete_insert` | 133.31ms | 93.19ms | 0.7× | 3.4% | PASS |
| file_writes | `oltp_read_write` | 184.51ms | 187.77ms | 1.0× | 3.8% | PASS |
| ac_reads | `oltp_point_select` | 42.23ms | 39.55ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 14.73ms | 13.17ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 14.61ms | 12.77ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 3.14ms | 3.04ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.06ms | 4.06ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 5.09ms | 6.52ms | 1.3× | 1.6% | PASS |
| ac_reads | `select_random_points` | 22.86ms | 20.65ms | 0.9× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 4.12ms | 5.57ms | 1.4× | 1.9% | PASS |
| ac_reads | `covering_index_scan` | 4.69ms | 5.07ms | 1.1× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 32.64ms | 32.66ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 11.75ms | 9.82ms | 0.8× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 3.53ms | 5.56ms | 1.6× | 1.0% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.13s | 1.1× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.25s | 1.27s | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_read_only` | 136.54ms | 132.71ms | 1.0× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 30.68ms | 76.47ms | 2.5× | 4.7% | PASS |
| ac_writes | `oltp_insert_ac` | 31.28ms | 86.58ms | 2.8× | 5.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 32.35ms | 93.95ms | 2.9× | 4.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 29.68ms | 80.58ms | 2.7× | 4.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 31.58ms | 90.11ms | 2.9× | 4.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 31.21ms | 89.66ms | 2.9× | 4.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 29.75ms | 84.17ms | 2.8× | 5.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.65ms | 93.09ms | 2.7× | 5.3% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.32ms | 42.34ms | 1.2× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 21.27ms | 22.06ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 18.83ms | 21.15ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 3.63ms | 4.04ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 4.74ms | 5.23ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.72ms | 6.57ms | 1.4× | 2.5% | PASS |
| mem_reads | `select_random_points` | 30.12ms | 34.10ms | 1.1× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 7.82ms | 9.05ms | 1.2× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.23ms | 4.46ms | 1.1× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 36.87ms | 39.81ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 8.15ms | 11.01ms | 1.4× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 3.97ms | 5.47ms | 1.4× | 2.8% | PASS |
| mem_reads | `types_table_scan` | 1.11s | 1.21s | 1.1× | 1.6% | PASS |
| mem_reads | `table_scan` | 1.39s | 1.32s | 0.9× | 2.4% | PASS |
| mem_reads | `oltp_read_only` | 162.55ms | 181.51ms | 1.1× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 254.70ms | 365.13ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 20.09ms | 36.44ms | 1.8× | 1.4% | PASS |
| mem_writes | `oltp_update_index` | 72.76ms | 141.12ms | 1.9× | 2.0% | PASS |
| mem_writes | `oltp_update_non_index` | 54.52ms | 89.50ms | 1.6× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 50.78ms | 98.47ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 27.56ms | 59.86ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 34.46ms | 54.51ms | 1.6× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 105.53ms | 165.36ms | 1.6× | 2.7% | PASS |
| file_reads | `oltp_point_select` | 109.24ms | 61.01ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 27.95ms | 23.64ms | 0.8× | 2.3% | PASS |
| file_reads | `oltp_sum_range` | 26.41ms | 23.05ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_order_range` | 4.46ms | 4.28ms | 1.0× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 5.60ms | 5.49ms | 1.0× | 2.0% | PASS |
| file_reads | `oltp_index_scan` | 12.25ms | 8.48ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 38.20ms | 36.72ms | 1.0× | 1.5% | PASS |
| file_reads | `select_random_ranges` | 15.89ms | 11.43ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 11.79ms | 6.69ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 37.94ms | 40.27ms | 1.1× | 0.8% | PASS |
| file_reads | `index_join` | 12.61ms | 12.56ms | 1.0× | 1.8% | PASS |
| file_reads | `index_join_scan` | 5.30ms | 6.20ms | 1.2× | 4.0% | PASS |
| file_reads | `types_table_scan` | 1.13s | 1.20s | 1.1× | 2.4% | PASS |
| file_reads | `table_scan` | 1.43s | 1.33s | 0.9× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 278.99ms | 213.36ms | 0.8× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 269.04ms | 372.96ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 27.89ms | 42.55ms | 1.5× | 2.3% | PASS |
| file_writes | `oltp_update_index` | 101.83ms | 148.99ms | 1.5× | 1.7% | PASS |
| file_writes | `oltp_update_non_index` | 80.95ms | 101.94ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_delete_insert` | 81.04ms | 112.25ms | 1.4× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 53.57ms | 70.88ms | 1.3× | 1.9% | PASS |
| file_writes | `types_delete_insert` | 52.65ms | 62.55ms | 1.2× | 1.3% | PASS |
| file_writes | `oltp_read_write` | 138.10ms | 182.16ms | 1.3× | 1.8% | PASS |
| ac_reads | `oltp_point_select` | 60.01ms | 62.28ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 23.67ms | 23.97ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 21.49ms | 23.42ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 4.03ms | 4.36ms | 1.1× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 5.14ms | 5.57ms | 1.1× | 0.7% | PASS |
| ac_reads | `oltp_index_scan` | 7.66ms | 8.86ms | 1.2× | 1.7% | PASS |
| ac_reads | `select_random_points` | 33.83ms | 39.23ms | 1.2× | 2.8% | PASS |
| ac_reads | `select_random_ranges` | 10.95ms | 11.65ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.23ms | 6.78ms | 0.9× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 37.19ms | 40.11ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 10.00ms | 12.61ms | 1.3× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 4.54ms | 5.98ms | 1.3× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 1.15s | 1.20s | 1.0× | 1.9% | PASS |
| ac_reads | `table_scan` | 1.39s | 1.31s | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_read_only` | 192.92ms | 207.84ms | 1.1× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.92ms | 63.78ms | 2.8× | 6.6% | PASS |
| ac_writes | `oltp_insert_ac` | 24.63ms | 80.44ms | 3.3× | 3.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.29ms | 90.49ms | 3.3× | 5.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 25.06ms | 72.59ms | 2.9× | 9.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.73ms | 81.01ms | 3.3× | 6.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.19ms | 82.80ms | 3.2× | 5.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.61ms | 73.57ms | 3.3× | 6.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.59ms | 90.88ms | 2.7× | 5.4% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 22s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 106.37ms | 130.00ms | 81.8% | 0.2% | PASS |
| `status_dirty_many_tables` | 110.37ms | 130.00ms | 84.9% | 0.2% | PASS |
| `diff_regular_working_one_table` | 101.71ms | 120.00ms | 84.8% | 0.2% | PASS |
| `diff_regular_working_many_tables` | 115.38ms | 140.00ms | 82.4% | 0.2% | PASS |
| `diff_stat_working_many_tables` | 115.38ms | 140.00ms | 82.4% | 0.2% | PASS |
| `diff_schema_working_many_tables` | 116.39ms | 140.00ms | 83.1% | 0.3% | PASS |
| `branch_list_many_branches` | 24.43ms | 35.00ms | 69.8% | 0.9% | PASS |
| `branch_create_delete` | 27.02ms | 40.00ms | 67.6% | 1.3% | PASS |
| `checkout_branch_clean` | 65.69ms | 150.00ms | 43.8% | 0.5% | PASS |
| `merge_data_no_conflicts` | 31.40ms | 50.00ms | 62.8% | 1.2% | PASS |
| `merge_schema_no_conflicts` | 23.64ms | 35.00ms | 67.5% | 1.4% | PASS |
| `merge_data_conflicts` | 35.26ms | 180.00ms | 19.6% | 1.3% | PASS |
| `merge_data_conflicts_with_resolve` | 35.90ms | 180.00ms | 19.9% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
