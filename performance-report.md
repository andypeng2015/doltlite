# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-12 19:16 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261101.186.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/43272092237)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.02s | 10.77s | 1.1× | 1.6% | **PASS** |
| Writes | 2.07s | 3.58s | 1.7× | 1.3% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.25s | 11.04s | 1.0× | 1.2% | **PASS** |
| Writes | 3.49s | 4.34s | 1.2× | 2.0% | **PASS** |
| Autocommit writes | 719.92ms | 2.24s | 3.1× | 6.9% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.48s | 2.69s | 1.1× | 1.6% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.13s | 2.26s | 1.1× | 1.5% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.86s | 2.95s | 1.0× | 2.3% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.54s | 2.87s | 1.1× | 1.5% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 433.64ms | 759.73ms | 1.8× | 1.4% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 453.62ms | 784.63ms | 1.7× | 1.1% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 596.79ms | 1.05s | 1.8× | 2.2% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 588.53ms | 993.59ms | 1.7× | 1.0% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 2.68s | 2.74s | 1.0× | 1.7% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.35s | 2.30s | 1.0× | 0.7% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.20s | 3.03s | 0.9× | 1.9% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.02s | 2.97s | 1.0× | 1.2% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 600.39ms | 837.41ms | 1.4× | 1.9% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.28s | 1.27s | 1.0× | 5.8% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 827.16ms | 1.13s | 1.4× | 2.1% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 781.80ms | 1.10s | 1.4× | 1.6% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.52s | 2.74s | 1.1× | 1.5% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.25s | 2.33s | 1.0× | 1.0% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 3.03s | 3.05s | 1.0× | 1.7% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.67s | 2.95s | 1.1× | 1.5% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 195.11ms | 598.81ms | 3.1× | 7.3% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 167.70ms | 514.90ms | 3.1× | 13.9% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 156.53ms | 506.03ms | 3.2× | 6.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 200.57ms | 616.83ms | 3.1× | 6.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.03ms | 29.87ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 11.05ms | 11.67ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 9.57ms | 11.33ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 2.74ms | 2.97ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.67ms | 4.12ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 3.93ms | 5.34ms | 1.4× | 2.0% | PASS |
| mem_reads | `select_random_points` | 9.84ms | 10.86ms | 1.1× | 1.6% | PASS |
| mem_reads | `select_random_ranges` | 3.00ms | 3.98ms | 1.3× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.20ms | 4.21ms | 1.0× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 30.43ms | 33.53ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 5.85ms | 7.99ms | 1.4× | 2.6% | PASS |
| mem_reads | `index_join_scan` | 3.27ms | 4.46ms | 1.4× | 1.7% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.16s | 1.1× | 1.0% | PASS |
| mem_reads | `table_scan` | 1.22s | 1.27s | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_read_only` | 109.29ms | 123.62ms | 1.1× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 175.48ms | 279.33ms | 1.6× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 15.04ms | 28.62ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 49.98ms | 101.21ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 34.66ms | 63.69ms | 1.8× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 43.69ms | 78.55ms | 1.8× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 21.77ms | 50.26ms | 2.3× | 1.4% | FAIL |
| mem_writes | `types_delete_insert` | 24.52ms | 41.34ms | 1.7× | 1.9% | PASS |
| mem_writes | `oltp_read_write` | 68.50ms | 116.72ms | 1.7× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 93.77ms | 47.26ms | 0.5× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 18.28ms | 13.64ms | 0.7× | 2.2% | PASS |
| file_reads | `oltp_sum_range` | 17.19ms | 13.30ms | 0.8× | 2.5% | PASS |
| file_reads | `oltp_order_range` | 3.58ms | 3.27ms | 0.9× | 2.3% | PASS |
| file_reads | `oltp_distinct_range` | 4.63ms | 4.44ms | 1.0× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 11.00ms | 7.32ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_points` | 17.21ms | 12.96ms | 0.8× | 2.2% | PASS |
| file_reads | `select_random_ranges` | 10.03ms | 5.82ms | 0.6× | 1.6% | PASS |
| file_reads | `covering_index_scan` | 11.31ms | 6.36ms | 0.6× | 1.4% | PASS |
| file_reads | `groupby_scan` | 31.78ms | 34.19ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 9.87ms | 9.68ms | 1.0× | 1.8% | PASS |
| file_reads | `index_join_scan` | 4.34ms | 4.89ms | 1.1× | 2.9% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.16s | 1.1× | 1.3% | PASS |
| file_reads | `table_scan` | 1.19s | 1.26s | 1.1× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 210.53ms | 148.63ms | 0.7× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 190.27ms | 287.91ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 21.89ms | 32.06ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 81.52ms | 118.32ms | 1.5× | 2.1% | PASS |
| file_writes | `oltp_update_non_index` | 59.57ms | 77.94ms | 1.3× | 2.2% | PASS |
| file_writes | `oltp_delete_insert` | 67.86ms | 91.41ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 45.02ms | 58.87ms | 1.3× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 40.18ms | 45.05ms | 1.1× | 3.0% | PASS |
| file_writes | `oltp_read_write` | 94.08ms | 125.83ms | 1.3× | 1.7% | PASS |
| ac_reads | `oltp_point_select` | 46.92ms | 47.34ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 13.75ms | 13.71ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 12.66ms | 13.45ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 3.34ms | 3.40ms | 1.0× | 3.1% | PASS |
| ac_reads | `oltp_distinct_range` | 4.32ms | 4.56ms | 1.1× | 2.7% | PASS |
| ac_reads | `oltp_index_scan` | 6.69ms | 7.45ms | 1.1× | 1.5% | PASS |
| ac_reads | `select_random_points` | 12.97ms | 12.99ms | 1.0× | 2.3% | PASS |
| ac_reads | `select_random_ranges` | 5.55ms | 5.81ms | 1.0× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 6.99ms | 6.42ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 31.34ms | 34.30ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 7.88ms | 9.99ms | 1.3× | 3.5% | PASS |
| ac_reads | `index_join_scan` | 3.88ms | 4.89ms | 1.3× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 1.04s | 1.16s | 1.1× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.26s | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 142.26ms | 148.59ms | 1.0× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.43ms | 60.45ms | 2.8× | 9.3% | PASS |
| ac_writes | `oltp_insert_ac` | 24.22ms | 74.33ms | 3.1× | 5.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.89ms | 86.29ms | 3.2× | 7.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.17ms | 67.88ms | 3.1× | 6.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.03ms | 79.15ms | 3.3× | 7.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.08ms | 79.11ms | 3.2× | 8.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.69ms | 68.03ms | 3.1× | 9.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.59ms | 83.56ms | 2.8× | 7.3% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 29.50ms | 28.40ms | 1.0× | 3.2% | PASS |
| mem_reads | `oltp_range_select` | 14.84ms | 11.00ms | 0.7× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 12.96ms | 10.79ms | 0.8× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 2.84ms | 2.59ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 3.53ms | 3.40ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.16ms | 4.70ms | 1.5× | 1.8% | PASS |
| mem_reads | `select_random_points` | 17.36ms | 16.16ms | 0.9× | 1.7% | PASS |
| mem_reads | `select_random_ranges` | 2.82ms | 4.17ms | 1.5× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 3.30ms | 3.57ms | 1.1× | 2.7% | PASS |
| mem_reads | `groupby_scan` | 28.24ms | 28.36ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 9.00ms | 7.18ms | 0.8× | 2.2% | PASS |
| mem_reads | `index_join_scan` | 3.13ms | 4.50ms | 1.4× | 1.2% | PASS |
| mem_reads | `types_table_scan` | 886.20ms | 967.32ms | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.01s | 1.06s | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 107.31ms | 102.69ms | 1.0× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 178.93ms | 278.09ms | 1.6× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 13.61ms | 29.91ms | 2.2× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 51.27ms | 114.95ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 38.11ms | 68.95ms | 1.8× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 43.72ms | 85.61ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 23.00ms | 51.38ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 31.41ms | 44.26ms | 1.4× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 73.57ms | 111.48ms | 1.5× | 1.3% | PASS |
| file_reads | `oltp_point_select` | 97.06ms | 44.26ms | 0.5× | 0.6% | PASS |
| file_reads | `oltp_range_select` | 21.44ms | 12.42ms | 0.6× | 0.7% | PASS |
| file_reads | `oltp_sum_range` | 19.79ms | 12.34ms | 0.6× | 0.6% | PASS |
| file_reads | `oltp_order_range` | 3.65ms | 2.78ms | 0.8× | 0.8% | PASS |
| file_reads | `oltp_distinct_range` | 4.45ms | 3.62ms | 0.8× | 0.8% | PASS |
| file_reads | `oltp_index_scan` | 10.35ms | 6.67ms | 0.6× | 1.2% | PASS |
| file_reads | `select_random_points` | 25.43ms | 17.98ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 9.81ms | 5.95ms | 0.6× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 10.58ms | 5.60ms | 0.5× | 0.8% | PASS |
| file_reads | `groupby_scan` | 29.87ms | 28.93ms | 1.0× | 0.5% | PASS |
| file_reads | `index_join` | 13.44ms | 8.79ms | 0.7× | 1.2% | PASS |
| file_reads | `index_join_scan` | 4.08ms | 4.97ms | 1.2× | 0.8% | PASS |
| file_reads | `types_table_scan` | 890.70ms | 966.78ms | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.01s | 1.05s | 1.0× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 208.05ms | 125.78ms | 0.6× | 0.4% | PASS |
| file_writes | `oltp_bulk_insert` | 262.55ms | 353.90ms | 1.3× | 4.3% | PASS |
| file_writes | `oltp_insert` | 33.74ms | 55.08ms | 1.6× | 6.0% | PASS |
| file_writes | `oltp_update_index` | 177.65ms | 203.60ms | 1.1× | 10.1% | PASS |
| file_writes | `oltp_update_non_index` | 155.11ms | 135.09ms | 0.9× | 5.6% | PASS |
| file_writes | `oltp_delete_insert` | 189.87ms | 155.15ms | 0.8× | 1.9% | PASS |
| file_writes | `oltp_write_only` | 127.01ms | 107.12ms | 0.8× | 10.6% | PASS |
| file_writes | `types_delete_insert` | 151.56ms | 86.85ms | 0.6× | 10.6% | PASS |
| file_writes | `oltp_read_write` | 185.04ms | 172.44ms | 0.9× | 5.4% | PASS |
| ac_reads | `oltp_point_select` | 49.25ms | 44.26ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 16.84ms | 12.49ms | 0.7× | 0.8% | PASS |
| ac_reads | `oltp_sum_range` | 15.35ms | 12.42ms | 0.8× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 3.21ms | 2.79ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 3.94ms | 3.62ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 5.74ms | 6.63ms | 1.2× | 0.9% | PASS |
| ac_reads | `select_random_points` | 20.46ms | 17.90ms | 0.9× | 1.1% | PASS |
| ac_reads | `select_random_ranges` | 5.12ms | 5.93ms | 1.2× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 5.86ms | 5.59ms | 1.0× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 29.01ms | 28.82ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 11.06ms | 8.78ms | 0.8× | 1.0% | PASS |
| ac_reads | `index_join_scan` | 3.67ms | 4.99ms | 1.4× | 1.1% | PASS |
| ac_reads | `types_table_scan` | 901.57ms | 974.51ms | 1.1× | 1.4% | PASS |
| ac_reads | `table_scan` | 1.03s | 1.07s | 1.0× | 2.4% | PASS |
| ac_reads | `oltp_read_only` | 148.94ms | 129.28ms | 0.9× | 1.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 18.84ms | 50.18ms | 2.7× | 9.7% | PASS |
| ac_writes | `oltp_insert_ac` | 20.84ms | 62.20ms | 3.0× | 11.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.72ms | 70.98ms | 3.3× | 17.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 18.35ms | 59.85ms | 3.3× | 15.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 21.61ms | 68.70ms | 3.2× | 15.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 20.86ms | 68.44ms | 3.3× | 12.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 19.31ms | 60.15ms | 3.1× | 17.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 26.16ms | 74.40ms | 2.8× | 8.1% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.28ms | 36.05ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 17.18ms | 13.41ms | 0.8× | 2.7% | PASS |
| mem_reads | `oltp_sum_range` | 15.66ms | 13.25ms | 0.8× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 3.50ms | 3.23ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.43ms | 4.31ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 4.03ms | 6.05ms | 1.5× | 2.0% | PASS |
| mem_reads | `select_random_points` | 22.99ms | 20.25ms | 0.9× | 5.2% | PASS |
| mem_reads | `select_random_ranges` | 3.57ms | 5.33ms | 1.5× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 4.29ms | 4.75ms | 1.1× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 36.42ms | 36.50ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 11.15ms | 9.72ms | 0.9× | 3.5% | PASS |
| mem_reads | `index_join_scan` | 3.98ms | 5.78ms | 1.5× | 3.0% | PASS |
| mem_reads | `types_table_scan` | 1.21s | 1.28s | 1.1× | 2.3% | PASS |
| mem_reads | `table_scan` | 1.34s | 1.38s | 1.0× | 2.7% | PASS |
| mem_reads | `oltp_read_only` | 146.29ms | 134.13ms | 0.9× | 2.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 234.93ms | 363.84ms | 1.5× | 0.5% | PASS |
| mem_writes | `oltp_insert` | 18.34ms | 41.74ms | 2.3× | 1.3% | PASS |
| mem_writes | `oltp_update_index` | 68.52ms | 154.68ms | 2.3× | 2.6% | PASS |
| mem_writes | `oltp_update_non_index` | 50.99ms | 92.83ms | 1.8× | 2.2% | PASS |
| mem_writes | `oltp_delete_insert` | 54.19ms | 113.86ms | 2.1× | 2.1% | PASS |
| mem_writes | `oltp_write_only` | 28.83ms | 70.15ms | 2.4× | 2.2% | FAIL |
| mem_writes | `types_delete_insert` | 39.81ms | 58.83ms | 1.5× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 101.17ms | 149.11ms | 1.5× | 2.9% | PASS |
| file_reads | `oltp_point_select` | 126.09ms | 56.98ms | 0.5× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 26.76ms | 15.81ms | 0.6× | 3.5% | PASS |
| file_reads | `oltp_sum_range` | 25.35ms | 15.52ms | 0.6× | 3.1% | PASS |
| file_reads | `oltp_order_range` | 4.35ms | 3.49ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 5.43ms | 4.56ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 13.11ms | 8.28ms | 0.6× | 1.0% | PASS |
| file_reads | `select_random_points` | 32.78ms | 22.85ms | 0.7× | 3.0% | PASS |
| file_reads | `select_random_ranges` | 12.44ms | 7.47ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 13.45ms | 6.94ms | 0.5× | 1.1% | PASS |
| file_reads | `groupby_scan` | 37.44ms | 36.65ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 16.34ms | 11.06ms | 0.7× | 3.0% | PASS |
| file_reads | `index_join_scan` | 5.07ms | 6.15ms | 1.2× | 2.7% | PASS |
| file_reads | `types_table_scan` | 1.25s | 1.28s | 1.0× | 2.5% | PASS |
| file_reads | `table_scan` | 1.36s | 1.38s | 1.0× | 1.6% | PASS |
| file_reads | `oltp_read_only` | 272.32ms | 164.11ms | 0.6× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 255.15ms | 373.81ms | 1.5× | 0.7% | PASS |
| file_writes | `oltp_insert` | 25.15ms | 48.43ms | 1.9× | 2.0% | PASS |
| file_writes | `oltp_update_index` | 100.32ms | 168.07ms | 1.7× | 1.8% | PASS |
| file_writes | `oltp_update_non_index` | 97.49ms | 105.17ms | 1.1× | 9.5% | PASS |
| file_writes | `oltp_delete_insert` | 91.19ms | 128.03ms | 1.4× | 2.2% | PASS |
| file_writes | `oltp_write_only` | 59.82ms | 81.44ms | 1.4× | 2.1% | PASS |
| file_writes | `types_delete_insert` | 66.92ms | 66.08ms | 1.0× | 2.3% | PASS |
| file_writes | `oltp_read_write` | 131.12ms | 159.32ms | 1.2× | 2.7% | PASS |
| ac_reads | `oltp_point_select` | 64.15ms | 57.11ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_range_select` | 21.40ms | 15.95ms | 0.7× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 19.25ms | 15.68ms | 0.8× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.93ms | 3.49ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.87ms | 4.58ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 7.17ms | 8.20ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_points` | 26.22ms | 22.78ms | 0.9× | 2.4% | PASS |
| ac_reads | `select_random_ranges` | 6.42ms | 7.45ms | 1.2× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 7.52ms | 6.91ms | 0.9× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 36.72ms | 36.69ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 13.15ms | 10.99ms | 0.8× | 1.8% | PASS |
| ac_reads | `index_join_scan` | 4.50ms | 6.03ms | 1.3× | 1.6% | PASS |
| ac_reads | `types_table_scan` | 1.24s | 1.29s | 1.0× | 2.8% | PASS |
| ac_reads | `table_scan` | 1.38s | 1.40s | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_read_only` | 190.44ms | 167.16ms | 0.9× | 1.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.82ms | 48.81ms | 2.7× | 5.6% | PASS |
| ac_writes | `oltp_insert_ac` | 19.27ms | 62.56ms | 3.2× | 4.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.27ms | 74.78ms | 3.7× | 6.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.66ms | 56.36ms | 3.4× | 6.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 19.76ms | 67.30ms | 3.4× | 5.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.08ms | 66.66ms | 3.5× | 6.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 17.59ms | 57.47ms | 3.3× | 6.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 26.08ms | 72.09ms | 2.8× | 3.0% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.01ms | 41.48ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 20.81ms | 21.61ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 18.21ms | 20.75ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 3.67ms | 3.98ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 4.66ms | 5.10ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.35ms | 6.00ms | 1.4× | 2.0% | PASS |
| mem_reads | `select_random_points` | 25.44ms | 30.35ms | 1.2× | 1.5% | PASS |
| mem_reads | `select_random_ranges` | 7.54ms | 8.91ms | 1.2× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 4.19ms | 4.46ms | 1.1× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 36.86ms | 40.86ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 7.93ms | 10.47ms | 1.3× | 1.1% | PASS |
| mem_reads | `index_join_scan` | 4.07ms | 5.37ms | 1.3× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.21s | 1.2× | 1.0% | PASS |
| mem_reads | `table_scan` | 1.17s | 1.29s | 1.1× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 151.16ms | 172.74ms | 1.1× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 243.27ms | 357.67ms | 1.5× | 0.6% | PASS |
| mem_writes | `oltp_insert` | 18.66ms | 36.17ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 66.72ms | 132.27ms | 2.0× | 1.9% | PASS |
| mem_writes | `oltp_update_non_index` | 50.37ms | 87.49ms | 1.7× | 1.4% | PASS |
| mem_writes | `oltp_delete_insert` | 48.19ms | 98.27ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 26.29ms | 61.06ms | 2.3× | 0.9% | FAIL |
| mem_writes | `types_delete_insert` | 32.88ms | 55.68ms | 1.7× | 0.9% | PASS |
| mem_writes | `oltp_read_write` | 102.15ms | 164.98ms | 1.6× | 1.5% | PASS |
| file_reads | `oltp_point_select` | 103.77ms | 59.00ms | 0.6× | 1.4% | PASS |
| file_reads | `oltp_range_select` | 27.94ms | 23.40ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_sum_range` | 25.66ms | 22.61ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 4.61ms | 4.24ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.63ms | 5.38ms | 1.0× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 12.05ms | 8.07ms | 0.7× | 1.1% | PASS |
| file_reads | `select_random_points` | 35.63ms | 34.11ms | 1.0× | 1.5% | PASS |
| file_reads | `select_random_ranges` | 15.21ms | 11.01ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 11.82ms | 6.46ms | 0.5× | 1.7% | PASS |
| file_reads | `groupby_scan` | 38.42ms | 41.19ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 12.62ms | 12.17ms | 1.0× | 2.2% | PASS |
| file_reads | `index_join_scan` | 5.15ms | 5.80ms | 1.1× | 1.8% | PASS |
| file_reads | `types_table_scan` | 1.12s | 1.22s | 1.1× | 1.1% | PASS |
| file_reads | `table_scan` | 1.33s | 1.32s | 1.0× | 2.7% | PASS |
| file_reads | `oltp_read_only` | 263.08ms | 203.00ms | 0.8× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 258.02ms | 372.43ms | 1.4× | 1.3% | PASS |
| file_writes | `oltp_insert` | 26.54ms | 42.73ms | 1.6× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 98.40ms | 148.73ms | 1.5× | 2.0% | PASS |
| file_writes | `oltp_update_non_index` | 79.25ms | 102.50ms | 1.3× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 78.41ms | 113.74ms | 1.5× | 1.3% | PASS |
| file_writes | `oltp_write_only` | 52.80ms | 73.48ms | 1.4× | 2.3% | PASS |
| file_writes | `types_delete_insert` | 51.53ms | 63.35ms | 1.2× | 1.3% | PASS |
| file_writes | `oltp_read_write` | 136.85ms | 181.08ms | 1.3× | 1.9% | PASS |
| ac_reads | `oltp_point_select` | 58.13ms | 60.10ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 23.38ms | 23.42ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 20.75ms | 22.66ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 4.05ms | 4.22ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 5.06ms | 5.42ms | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 7.30ms | 8.10ms | 1.1× | 1.6% | PASS |
| ac_reads | `select_random_points` | 30.62ms | 34.57ms | 1.1× | 1.6% | PASS |
| ac_reads | `select_random_ranges` | 10.38ms | 11.06ms | 1.1× | 1.9% | PASS |
| ac_reads | `covering_index_scan` | 7.12ms | 6.48ms | 0.9× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 37.91ms | 41.28ms | 1.1× | 1.1% | PASS |
| ac_reads | `index_join` | 9.98ms | 12.22ms | 1.2× | 2.2% | PASS |
| ac_reads | `index_join_scan` | 4.57ms | 5.82ms | 1.3× | 1.5% | PASS |
| ac_reads | `types_table_scan` | 1.08s | 1.21s | 1.1× | 2.0% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.30s | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_read_only` | 190.97ms | 203.11ms | 1.1× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.00ms | 60.54ms | 2.6× | 5.3% | PASS |
| ac_writes | `oltp_insert_ac` | 24.83ms | 76.94ms | 3.1× | 5.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.28ms | 86.85ms | 3.2× | 6.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.34ms | 69.68ms | 3.0× | 6.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.69ms | 79.08ms | 3.3× | 7.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.79ms | 79.30ms | 3.2× | 6.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.47ms | 77.33ms | 3.4× | 9.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.18ms | 87.11ms | 2.8× | 4.4% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 40s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 67.09ms | 130.00ms | 51.6% | 0.8% | PASS |
| `status_dirty_many_tables` | 69.89ms | 130.00ms | 53.8% | 1.1% | PASS |
| `diff_regular_working_one_table` | 62.80ms | 120.00ms | 52.3% | 1.7% | PASS |
| `diff_regular_working_many_tables` | 73.05ms | 140.00ms | 52.2% | 0.7% | PASS |
| `diff_stat_working_many_tables` | 73.25ms | 140.00ms | 52.3% | 0.9% | PASS |
| `diff_schema_working_many_tables` | 74.52ms | 140.00ms | 53.2% | 1.4% | PASS |
| `branch_list_many_branches` | 16.51ms | 35.00ms | 47.2% | 1.6% | PASS |
| `branch_create_delete` | 19.03ms | 40.00ms | 47.6% | 2.6% | PASS |
| `checkout_branch_clean` | 47.48ms | 150.00ms | 31.7% | 1.9% | PASS |
| `merge_data_no_conflicts` | 21.38ms | 50.00ms | 42.8% | 2.0% | PASS |
| `merge_schema_no_conflicts` | 16.45ms | 35.00ms | 47.0% | 2.1% | PASS |
| `merge_data_conflicts` | 23.94ms | 180.00ms | 13.3% | 1.4% | PASS |
| `merge_data_conflicts_with_resolve` | 23.86ms | 180.00ms | 13.3% | 1.5% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
