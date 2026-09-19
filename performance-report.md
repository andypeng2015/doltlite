# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-19 14:52 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/35445324476)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.92s | 10.37s | 1.0× | 1.5% | **PASS** |
| Writes | 2.01s | 3.30s | 1.6× | 1.3% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.66s | 10.54s | 1.0× | 1.3% | **PASS** |
| Writes | 3.59s | 4.06s | 1.1× | 2.9% | **PASS** |
| Autocommit writes | 1.63s | 5.34s | 3.3× | 6.3% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.68s | 2.69s | 1.0× | 1.9% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.94s | 2.96s | 1.0× | 1.5% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 1.77s | 1.91s | 1.1× | 1.9% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.54s | 2.81s | 1.1× | 1.2% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 475.46ms | 775.35ms | 1.6× | 3.3% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 608.41ms | 1.01s | 1.7× | 1.1% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 357.62ms | 582.15ms | 1.6× | 1.2% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 566.85ms | 938.34ms | 1.7× | 1.3% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.91s | 2.75s | 0.9× | 1.7% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.10s | 2.96s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 1.87s | 1.94s | 1.0× | 2.0% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.78s | 2.88s | 1.0× | 0.9% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 645.90ms | 846.24ms | 1.3× | 2.9% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 970.69ms | 1.10s | 1.1× | 4.7% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.23s | 1.08s | 0.9× | 26.5% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 735.02ms | 1.03s | 1.4× | 1.3% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.75s | 2.75s | 1.0× | 1.8% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 3.26s | 3.11s | 1.0× | 2.0% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 1.83s | 1.94s | 1.1× | 2.3% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.61s | 2.86s | 1.1× | 1.2% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 208.05ms | 769.09ms | 3.7× | 6.1% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 162.47ms | 605.78ms | 3.7× | 5.4% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 1.07s | 3.29s | 3.1× | 50.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 191.58ms | 680.67ms | 3.6× | 5.5% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.80ms | 29.92ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 10.77ms | 11.27ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_sum_range` | 9.98ms | 11.34ms | 1.1× | 2.4% | PASS |
| mem_reads | `oltp_order_range` | 2.63ms | 2.84ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.72ms | 3.99ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 3.98ms | 5.43ms | 1.4× | 3.1% | PASS |
| mem_reads | `select_random_points` | 10.67ms | 11.07ms | 1.0× | 5.4% | PASS |
| mem_reads | `select_random_ranges` | 3.06ms | 3.99ms | 1.3× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 4.24ms | 4.39ms | 1.0× | 2.6% | PASS |
| mem_reads | `groupby_scan` | 31.09ms | 33.71ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 6.22ms | 8.99ms | 1.4× | 2.7% | PASS |
| mem_reads | `index_join_scan` | 3.61ms | 4.73ms | 1.3× | 2.6% | PASS |
| mem_reads | `types_table_scan` | 1.15s | 1.17s | 1.0× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.30s | 1.26s | 1.0× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 116.01ms | 125.14ms | 1.1× | 1.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 185.24ms | 282.36ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 15.96ms | 29.40ms | 1.8× | 2.0% | PASS |
| mem_writes | `oltp_update_index` | 57.62ms | 106.64ms | 1.9× | 3.8% | PASS |
| mem_writes | `oltp_update_non_index` | 39.03ms | 64.86ms | 1.7× | 2.8% | PASS |
| mem_writes | `oltp_delete_insert` | 49.00ms | 78.74ms | 1.6× | 3.9% | PASS |
| mem_writes | `oltp_write_only` | 24.10ms | 50.58ms | 2.1× | 4.2% | PASS |
| mem_writes | `types_delete_insert` | 26.46ms | 40.31ms | 1.5× | 2.5% | PASS |
| mem_writes | `oltp_read_write` | 78.06ms | 122.45ms | 1.6× | 3.9% | PASS |
| file_reads | `oltp_point_select` | 100.87ms | 51.54ms | 0.5× | 1.4% | PASS |
| file_reads | `oltp_range_select` | 19.68ms | 13.77ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 18.59ms | 13.82ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 3.62ms | 3.17ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 4.71ms | 4.37ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 12.06ms | 7.94ms | 0.7× | 2.9% | PASS |
| file_reads | `select_random_points` | 19.64ms | 13.86ms | 0.7× | 5.5% | PASS |
| file_reads | `select_random_ranges` | 10.60ms | 6.07ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 12.29ms | 6.74ms | 0.5× | 2.7% | PASS |
| file_reads | `groupby_scan` | 32.15ms | 33.94ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 10.88ms | 10.28ms | 0.9× | 5.5% | PASS |
| file_reads | `index_join_scan` | 4.52ms | 4.94ms | 1.1× | 3.4% | PASS |
| file_reads | `types_table_scan` | 1.12s | 1.15s | 1.0× | 0.7% | PASS |
| file_reads | `table_scan` | 1.31s | 1.27s | 1.0× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 226.54ms | 157.62ms | 0.7× | 1.7% | PASS |
| file_writes | `oltp_bulk_insert` | 199.50ms | 293.18ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 24.03ms | 33.89ms | 1.4× | 4.5% | PASS |
| file_writes | `oltp_update_index` | 86.02ms | 116.92ms | 1.4× | 4.5% | PASS |
| file_writes | `oltp_update_non_index` | 64.00ms | 75.66ms | 1.2× | 2.2% | PASS |
| file_writes | `oltp_delete_insert` | 74.60ms | 89.75ms | 1.2× | 3.7% | PASS |
| file_writes | `oltp_write_only` | 49.45ms | 59.61ms | 1.2× | 2.8% | PASS |
| file_writes | `types_delete_insert` | 43.43ms | 47.21ms | 1.1× | 2.4% | PASS |
| file_writes | `oltp_read_write` | 104.87ms | 130.03ms | 1.2× | 3.0% | PASS |
| ac_reads | `oltp_point_select` | 50.88ms | 51.43ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 14.58ms | 13.84ms | 0.9× | 2.6% | PASS |
| ac_reads | `oltp_sum_range` | 13.39ms | 14.05ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.17ms | 3.24ms | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_distinct_range` | 4.19ms | 4.38ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_index_scan` | 7.10ms | 8.01ms | 1.1× | 2.2% | PASS |
| ac_reads | `select_random_points` | 14.52ms | 14.09ms | 1.0× | 4.6% | PASS |
| ac_reads | `select_random_ranges` | 5.73ms | 6.11ms | 1.1× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 7.61ms | 6.86ms | 0.9× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 31.64ms | 34.15ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 8.16ms | 10.34ms | 1.3× | 3.1% | PASS |
| ac_reads | `index_join_scan` | 3.91ms | 4.99ms | 1.3× | 3.5% | PASS |
| ac_reads | `types_table_scan` | 1.13s | 1.16s | 1.0× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.30s | 1.26s | 1.0× | 0.6% | PASS |
| ac_reads | `oltp_read_only` | 150.58ms | 154.24ms | 1.0× | 1.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.49ms | 84.04ms | 3.4× | 7.6% | PASS |
| ac_writes | `oltp_insert_ac` | 26.56ms | 95.89ms | 3.6× | 6.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.79ms | 105.91ms | 4.0× | 5.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.59ms | 89.70ms | 3.8× | 6.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.23ms | 98.72ms | 3.8× | 6.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.72ms | 99.55ms | 3.9× | 5.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.13ms | 89.89ms | 3.9× | 6.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.54ms | 105.40ms | 3.3× | 4.3% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.17ms | 35.95ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 17.06ms | 13.66ms | 0.8× | 1.2% | PASS |
| mem_reads | `oltp_sum_range` | 15.88ms | 13.39ms | 0.8× | 0.9% | PASS |
| mem_reads | `oltp_order_range` | 3.50ms | 3.23ms | 0.9× | 0.8% | PASS |
| mem_reads | `oltp_distinct_range` | 4.56ms | 4.35ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.05ms | 5.87ms | 1.4× | 1.2% | PASS |
| mem_reads | `select_random_points` | 23.00ms | 20.34ms | 0.9× | 1.9% | PASS |
| mem_reads | `select_random_ranges` | 3.71ms | 5.32ms | 1.4× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.31ms | 4.66ms | 1.1× | 2.3% | PASS |
| mem_reads | `groupby_scan` | 37.59ms | 37.01ms | 1.0× | 1.5% | PASS |
| mem_reads | `index_join` | 11.13ms | 9.22ms | 0.8× | 3.3% | PASS |
| mem_reads | `index_join_scan` | 3.99ms | 5.67ms | 1.4× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 1.21s | 1.27s | 1.0× | 1.8% | PASS |
| mem_reads | `table_scan` | 1.41s | 1.40s | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_read_only` | 147.38ms | 135.58ms | 0.9× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 245.73ms | 354.36ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 18.51ms | 39.08ms | 2.1× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 71.03ms | 153.18ms | 2.2× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 53.60ms | 90.36ms | 1.7× | 2.2% | PASS |
| mem_writes | `oltp_delete_insert` | 55.43ms | 107.02ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 28.69ms | 64.36ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 39.47ms | 54.00ms | 1.4× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 95.95ms | 142.71ms | 1.5× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 135.48ms | 59.86ms | 0.4× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 28.47ms | 16.45ms | 0.6× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 26.86ms | 16.22ms | 0.6× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 4.49ms | 3.52ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 5.60ms | 4.67ms | 0.8× | 2.2% | PASS |
| file_reads | `oltp_index_scan` | 13.86ms | 8.56ms | 0.6× | 1.6% | PASS |
| file_reads | `select_random_points` | 32.96ms | 22.94ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_ranges` | 13.45ms | 7.82ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 14.25ms | 7.23ms | 0.5× | 1.5% | PASS |
| file_reads | `groupby_scan` | 37.74ms | 37.19ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join` | 16.48ms | 10.87ms | 0.7× | 3.0% | PASS |
| file_reads | `index_join_scan` | 4.98ms | 6.01ms | 1.2× | 2.5% | PASS |
| file_reads | `types_table_scan` | 1.17s | 1.24s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.32s | 1.36s | 1.0× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 279.13ms | 165.89ms | 0.6× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 272.56ms | 369.36ms | 1.4× | 1.2% | PASS |
| file_writes | `oltp_insert` | 26.23ms | 45.77ms | 1.7× | 2.6% | PASS |
| file_writes | `oltp_update_index` | 135.11ms | 163.08ms | 1.2× | 6.7% | PASS |
| file_writes | `oltp_update_non_index` | 122.41ms | 102.63ms | 0.8× | 12.4% | PASS |
| file_writes | `oltp_delete_insert` | 99.78ms | 124.00ms | 1.2× | 2.3% | PASS |
| file_writes | `oltp_write_only` | 89.47ms | 75.94ms | 0.8× | 8.2% | PASS |
| file_writes | `types_delete_insert` | 75.72ms | 63.37ms | 0.8× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 149.41ms | 154.84ms | 1.0× | 7.6% | PASS |
| ac_reads | `oltp_point_select` | 67.84ms | 58.98ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 20.81ms | 16.23ms | 0.8× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 19.50ms | 15.92ms | 0.8× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 3.88ms | 3.51ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_distinct_range` | 4.98ms | 4.66ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 7.57ms | 8.59ms | 1.1× | 1.9% | PASS |
| ac_reads | `select_random_points` | 26.95ms | 23.24ms | 0.9× | 3.3% | PASS |
| ac_reads | `select_random_ranges` | 7.07ms | 7.78ms | 1.1× | 2.0% | PASS |
| ac_reads | `covering_index_scan` | 7.87ms | 7.27ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 37.48ms | 37.46ms | 1.0× | 1.7% | PASS |
| ac_reads | `index_join` | 13.41ms | 11.04ms | 0.8× | 3.3% | PASS |
| ac_reads | `index_join_scan` | 4.56ms | 6.22ms | 1.4× | 2.7% | PASS |
| ac_reads | `types_table_scan` | 1.29s | 1.29s | 1.0× | 4.4% | PASS |
| ac_reads | `table_scan` | 1.56s | 1.45s | 0.9× | 3.8% | PASS |
| ac_reads | `oltp_read_only` | 198.76ms | 172.25ms | 0.9× | 2.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.30ms | 60.23ms | 3.5× | 4.9% | PASS |
| ac_writes | `oltp_insert_ac` | 19.84ms | 72.00ms | 3.6× | 4.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 22.43ms | 88.91ms | 4.0× | 8.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.63ms | 69.55ms | 3.9× | 5.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 19.62ms | 78.59ms | 4.0× | 6.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.51ms | 77.77ms | 4.0× | 5.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.70ms | 71.59ms | 3.8× | 9.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.44ms | 87.12ms | 3.2× | 5.6% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 22.53ms | 22.25ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 10.61ms | 8.86ms | 0.8× | 3.8% | PASS |
| mem_reads | `oltp_sum_range` | 10.64ms | 8.64ms | 0.8× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 2.22ms | 2.07ms | 0.9× | 2.2% | PASS |
| mem_reads | `oltp_distinct_range` | 2.75ms | 2.79ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_index_scan` | 2.62ms | 3.58ms | 1.4× | 2.9% | PASS |
| mem_reads | `select_random_points` | 15.88ms | 14.50ms | 0.9× | 2.7% | PASS |
| mem_reads | `select_random_ranges` | 2.22ms | 3.35ms | 1.5× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 2.47ms | 2.70ms | 1.1× | 2.7% | PASS |
| mem_reads | `groupby_scan` | 21.25ms | 23.01ms | 1.1× | 1.2% | PASS |
| mem_reads | `index_join` | 8.72ms | 6.32ms | 0.7× | 1.4% | PASS |
| mem_reads | `index_join_scan` | 2.51ms | 4.66ms | 1.9× | 3.4% | PASS |
| mem_reads | `types_table_scan` | 741.44ms | 817.95ms | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 837.67ms | 906.97ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_read_only` | 83.73ms | 82.27ms | 1.0× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 135.76ms | 199.48ms | 1.5× | 0.5% | PASS |
| mem_writes | `oltp_insert` | 11.02ms | 22.38ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 41.10ms | 86.87ms | 2.1× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 30.11ms | 50.57ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 34.18ms | 62.79ms | 1.8× | 1.7% | PASS |
| mem_writes | `oltp_write_only` | 18.24ms | 37.02ms | 2.0× | 2.6% | PASS |
| mem_writes | `types_delete_insert` | 25.18ms | 32.38ms | 1.3× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 62.03ms | 90.64ms | 1.5× | 0.9% | PASS |
| file_reads | `oltp_point_select` | 45.44ms | 28.59ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 13.33ms | 9.95ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 13.56ms | 9.86ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 2.57ms | 2.26ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 3.17ms | 2.98ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 5.03ms | 4.64ms | 0.9× | 3.2% | PASS |
| file_reads | `select_random_points` | 19.28ms | 16.38ms | 0.8× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 4.51ms | 4.07ms | 0.9× | 2.0% | PASS |
| file_reads | `covering_index_scan` | 4.80ms | 3.66ms | 0.8× | 5.6% | PASS |
| file_reads | `groupby_scan` | 21.49ms | 23.31ms | 1.1× | 1.1% | PASS |
| file_reads | `index_join` | 10.82ms | 7.73ms | 0.7× | 3.2% | PASS |
| file_reads | `index_join_scan` | 2.98ms | 5.04ms | 1.7× | 3.2% | PASS |
| file_reads | `types_table_scan` | 746.34ms | 819.12ms | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 858.25ms | 911.87ms | 1.1× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 118.09ms | 92.33ms | 0.8× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 229.35ms | 275.47ms | 1.2× | 14.1% | PASS |
| file_writes | `oltp_insert` | 43.93ms | 56.06ms | 1.3× | 58.0% | PASS |
| file_writes | `oltp_update_index` | 193.66ms | 182.30ms | 0.9× | 27.7% | PASS |
| file_writes | `oltp_update_non_index` | 139.24ms | 109.53ms | 0.8× | 25.4% | PASS |
| file_writes | `oltp_delete_insert` | 190.40ms | 137.47ms | 0.7× | 23.3% | PASS |
| file_writes | `oltp_write_only` | 169.07ms | 99.81ms | 0.6× | 42.3% | PASS |
| file_writes | `types_delete_insert` | 118.03ms | 81.54ms | 0.7× | 33.1% | PASS |
| file_writes | `oltp_read_write` | 150.57ms | 142.42ms | 0.9× | 20.5% | PASS |
| ac_reads | `oltp_point_select` | 29.65ms | 27.80ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 11.73ms | 9.66ms | 0.8× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 11.90ms | 9.62ms | 0.8× | 2.5% | PASS |
| ac_reads | `oltp_order_range` | 2.43ms | 2.23ms | 0.9× | 2.5% | PASS |
| ac_reads | `oltp_distinct_range` | 3.02ms | 2.97ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 3.75ms | 4.86ms | 1.3× | 3.1% | PASS |
| ac_reads | `select_random_points` | 18.06ms | 16.27ms | 0.9× | 2.3% | PASS |
| ac_reads | `select_random_ranges` | 3.06ms | 4.10ms | 1.3× | 2.5% | PASS |
| ac_reads | `covering_index_scan` | 3.34ms | 3.62ms | 1.1× | 4.1% | PASS |
| ac_reads | `groupby_scan` | 21.41ms | 23.23ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 10.20ms | 7.64ms | 0.7× | 3.6% | PASS |
| ac_reads | `index_join_scan` | 2.96ms | 5.11ms | 1.7× | 3.9% | PASS |
| ac_reads | `types_table_scan` | 749.88ms | 823.47ms | 1.1× | 0.9% | PASS |
| ac_reads | `table_scan` | 858.22ms | 908.62ms | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 98.22ms | 94.52ms | 1.0× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 99.86ms | 228.40ms | 2.3× | 47.7% | PASS |
| ac_writes | `oltp_insert_ac` | 194.56ms | 498.74ms | 2.6× | 48.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 140.39ms | 523.13ms | 3.7× | 55.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 119.50ms | 365.50ms | 3.1× | 49.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 85.84ms | 444.33ms | 5.2× | 67.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 128.34ms | 325.19ms | 2.5× | 52.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 98.31ms | 294.01ms | 3.0× | 57.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 199.13ms | 606.58ms | 3.0× | 45.7% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 30.83ms | 40.10ms | 1.3× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 17.65ms | 21.19ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_sum_range` | 16.52ms | 20.25ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 3.23ms | 3.75ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 4.35ms | 4.97ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.06ms | 5.76ms | 1.4× | 1.5% | PASS |
| mem_reads | `select_random_points` | 25.50ms | 31.56ms | 1.2× | 1.3% | PASS |
| mem_reads | `select_random_ranges` | 7.02ms | 8.72ms | 1.2× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.15ms | 4.16ms | 1.0× | 1.0% | PASS |
| mem_reads | `groupby_scan` | 36.01ms | 39.09ms | 1.1× | 1.2% | PASS |
| mem_reads | `index_join` | 7.67ms | 9.98ms | 1.3× | 1.6% | PASS |
| mem_reads | `index_join_scan` | 3.44ms | 5.37ms | 1.6× | 1.1% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.18s | 1.27s | 1.1× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 142.50ms | 169.33ms | 1.2× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 243.01ms | 343.76ms | 1.4× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 18.62ms | 34.42ms | 1.8× | 1.4% | PASS |
| mem_writes | `oltp_update_index` | 61.84ms | 121.41ms | 2.0× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 46.96ms | 81.46ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 46.32ms | 92.47ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 24.75ms | 56.16ms | 2.3× | 1.6% | PASS |
| mem_writes | `types_delete_insert` | 30.57ms | 51.69ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 94.79ms | 156.98ms | 1.7× | 1.0% | PASS |
| file_reads | `oltp_point_select` | 104.79ms | 60.30ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 25.61ms | 23.80ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_sum_range` | 24.36ms | 22.86ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_order_range` | 4.14ms | 4.09ms | 1.0× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 5.31ms | 5.32ms | 1.0× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 11.64ms | 8.26ms | 0.7× | 1.0% | PASS |
| file_reads | `select_random_points` | 35.25ms | 35.73ms | 1.0× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 14.96ms | 11.21ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 11.54ms | 6.50ms | 0.6× | 1.3% | PASS |
| file_reads | `groupby_scan` | 37.06ms | 39.90ms | 1.1× | 0.6% | PASS |
| file_reads | `index_join` | 12.08ms | 12.09ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join_scan` | 4.66ms | 5.93ms | 1.3× | 1.7% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.3% | PASS |
| file_reads | `table_scan` | 1.19s | 1.27s | 1.1× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 252.84ms | 199.93ms | 0.8× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 259.50ms | 356.44ms | 1.4× | 0.6% | PASS |
| file_writes | `oltp_insert` | 25.38ms | 40.18ms | 1.6× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 90.11ms | 135.55ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_update_non_index` | 72.50ms | 95.79ms | 1.3× | 1.2% | PASS |
| file_writes | `oltp_delete_insert` | 72.88ms | 105.63ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 47.83ms | 66.74ms | 1.4× | 0.9% | PASS |
| file_writes | `types_delete_insert` | 46.99ms | 58.20ms | 1.2× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 119.82ms | 167.51ms | 1.4× | 1.3% | PASS |
| ac_reads | `oltp_point_select` | 55.82ms | 60.31ms | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 21.09ms | 23.74ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 19.65ms | 22.90ms | 1.2× | 1.4% | PASS |
| ac_reads | `oltp_order_range` | 3.68ms | 4.09ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.81ms | 5.30ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.82ms | 8.26ms | 1.2× | 1.4% | PASS |
| ac_reads | `select_random_points` | 29.97ms | 35.67ms | 1.2× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 10.00ms | 11.22ms | 1.1× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 6.70ms | 6.53ms | 1.0× | 0.8% | PASS |
| ac_reads | `groupby_scan` | 36.69ms | 39.85ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 9.44ms | 12.07ms | 1.3× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 4.20ms | 5.86ms | 1.4× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 1.04s | 1.16s | 1.1× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.26s | 1.1× | 0.3% | PASS |
| ac_reads | `oltp_read_only` | 180.33ms | 199.23ms | 1.1× | 0.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.30ms | 70.12ms | 3.1× | 6.5% | PASS |
| ac_writes | `oltp_insert_ac` | 23.06ms | 85.11ms | 3.7× | 3.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.38ms | 96.10ms | 3.8× | 4.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.19ms | 78.92ms | 3.6× | 6.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.68ms | 88.56ms | 3.6× | 5.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.88ms | 87.41ms | 3.7× | 5.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.65ms | 79.21ms | 3.8× | 6.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.45ms | 95.27ms | 3.2× | 3.2% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 10s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 93.88ms | 130.00ms | 72.2% | 0.5% | PASS |
| `status_dirty_many_tables` | 97.49ms | 130.00ms | 75.0% | 0.3% | PASS |
| `diff_regular_working_one_table` | 89.55ms | 120.00ms | 74.6% | 0.5% | PASS |
| `diff_regular_working_many_tables` | 102.82ms | 140.00ms | 73.4% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 102.57ms | 140.00ms | 73.3% | 0.4% | PASS |
| `diff_schema_working_many_tables` | 103.38ms | 140.00ms | 73.8% | 0.4% | PASS |
| `branch_list_many_branches` | 22.40ms | 35.00ms | 64.0% | 0.8% | PASS |
| `branch_create_delete` | 25.07ms | 40.00ms | 62.7% | 1.4% | PASS |
| `checkout_branch_clean` | 59.69ms | 150.00ms | 39.8% | 0.7% | PASS |
| `merge_data_no_conflicts` | 28.41ms | 50.00ms | 56.8% | 0.9% | PASS |
| `merge_schema_no_conflicts` | 21.81ms | 35.00ms | 62.3% | 1.7% | PASS |
| `merge_data_conflicts` | 31.97ms | 180.00ms | 17.8% | 0.8% | PASS |
| `merge_data_conflicts_with_resolve` | 32.51ms | 180.00ms | 18.1% | 1.3% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
