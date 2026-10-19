# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-19 18:06 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261012.334.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/39286661526)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.07s | 10.37s | 1.0× | 1.4% | **PASS** |
| Writes | 1.94s | 3.18s | 1.6× | 1.1% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.48s | 10.40s | 1.0× | 1.1% | **PASS** |
| Writes | 3.77s | 4.32s | 1.1× | 1.5% | **PASS** |
| Autocommit writes | 738.99ms | 2.25s | 3.0× | 6.5% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.58s | 2.63s | 1.0× | 1.7% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.34s | 2.31s | 1.0× | 1.6% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.55s | 2.63s | 1.0× | 0.9% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.60s | 2.79s | 1.1× | 1.1% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 457.11ms | 761.29ms | 1.7× | 1.6% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 472.81ms | 743.14ms | 1.6× | 1.1% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 483.56ms | 799.59ms | 1.7× | 0.8% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 524.45ms | 879.13ms | 1.7× | 1.3% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.81s | 2.69s | 1.0× | 1.7% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.49s | 2.32s | 0.9× | 0.9% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.53s | 2.60s | 1.0× | 0.8% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.65s | 2.79s | 1.1× | 1.3% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 607.18ms | 812.38ms | 1.3× | 1.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.31s | 1.24s | 0.9× | 3.6% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.22s | 1.31s | 1.1× | 1.7% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 631.95ms | 957.33ms | 1.5× | 1.4% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.63s | 2.69s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.34s | 2.32s | 1.0× | 1.0% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.45s | 2.59s | 1.1× | 0.8% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.50s | 2.77s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 190.07ms | 598.80ms | 3.2× | 5.3% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 171.17ms | 515.83ms | 3.0× | 13.0% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 244.70ms | 683.54ms | 2.8× | 4.7% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 133.05ms | 448.79ms | 3.4× | 7.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.74ms | 30.66ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 10.48ms | 11.58ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 9.95ms | 11.30ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_order_range` | 2.64ms | 2.84ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.71ms | 3.95ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.98ms | 5.38ms | 1.4× | 1.7% | PASS |
| mem_reads | `select_random_points` | 10.33ms | 11.45ms | 1.1× | 2.8% | PASS |
| mem_reads | `select_random_ranges` | 3.08ms | 4.01ms | 1.3× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 4.23ms | 4.35ms | 1.0× | 2.2% | PASS |
| mem_reads | `groupby_scan` | 30.79ms | 33.05ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 5.86ms | 8.35ms | 1.4× | 2.9% | PASS |
| mem_reads | `index_join_scan` | 3.26ms | 4.49ms | 1.4× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 1.10s | 1.14s | 1.0× | 1.4% | PASS |
| mem_reads | `table_scan` | 1.26s | 1.24s | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 111.17ms | 121.86ms | 1.1× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 183.99ms | 280.71ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 15.62ms | 29.09ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 53.20ms | 103.30ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 36.72ms | 63.29ms | 1.7× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 47.91ms | 78.28ms | 1.6× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 23.25ms | 49.99ms | 2.1× | 1.9% | PASS |
| mem_writes | `types_delete_insert` | 25.51ms | 39.98ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 70.92ms | 116.66ms | 1.6× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 99.75ms | 49.94ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 18.26ms | 13.79ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 17.80ms | 13.55ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 3.68ms | 3.30ms | 0.9× | 2.8% | PASS |
| file_reads | `oltp_distinct_range` | 4.71ms | 4.39ms | 0.9× | 2.2% | PASS |
| file_reads | `oltp_index_scan` | 11.94ms | 7.79ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 18.60ms | 13.74ms | 0.7× | 2.3% | PASS |
| file_reads | `select_random_ranges` | 10.79ms | 6.04ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 12.18ms | 6.67ms | 0.5× | 1.7% | PASS |
| file_reads | `groupby_scan` | 31.93ms | 33.60ms | 1.1× | 1.1% | PASS |
| file_reads | `index_join` | 10.19ms | 9.96ms | 1.0× | 2.1% | PASS |
| file_reads | `index_join_scan` | 4.27ms | 4.96ms | 1.2× | 2.2% | PASS |
| file_reads | `types_table_scan` | 1.10s | 1.14s | 1.0× | 1.0% | PASS |
| file_reads | `table_scan` | 1.25s | 1.24s | 1.0× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 219.47ms | 148.48ms | 0.7× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 196.43ms | 287.37ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_insert` | 22.39ms | 32.60ms | 1.5× | 2.4% | PASS |
| file_writes | `oltp_update_index` | 78.89ms | 110.32ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 59.99ms | 72.89ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 69.31ms | 84.69ms | 1.2× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 45.28ms | 55.78ms | 1.2× | 2.3% | PASS |
| file_writes | `types_delete_insert` | 40.40ms | 45.41ms | 1.1× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 94.50ms | 123.33ms | 1.3× | 1.5% | PASS |
| ac_reads | `oltp_point_select` | 49.37ms | 50.03ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 13.54ms | 13.73ms | 1.0× | 2.4% | PASS |
| ac_reads | `oltp_sum_range` | 12.71ms | 13.48ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.11ms | 3.21ms | 1.0× | 3.0% | PASS |
| ac_reads | `oltp_distinct_range` | 4.16ms | 4.35ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.92ms | 7.77ms | 1.1× | 1.5% | PASS |
| ac_reads | `select_random_points` | 13.35ms | 13.72ms | 1.0× | 2.4% | PASS |
| ac_reads | `select_random_ranges` | 5.77ms | 6.07ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 7.31ms | 6.71ms | 0.9× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 31.45ms | 33.67ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 7.71ms | 9.98ms | 1.3× | 1.9% | PASS |
| ac_reads | `index_join_scan` | 3.71ms | 4.94ms | 1.3× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 1.08s | 1.13s | 1.1× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.24s | 1.24s | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 145.25ms | 148.88ms | 1.0× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.61ms | 62.12ms | 2.9× | 5.4% | PASS |
| ac_writes | `oltp_insert_ac` | 23.59ms | 74.80ms | 3.2× | 7.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.63ms | 86.70ms | 3.4× | 5.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.64ms | 68.12ms | 3.0× | 7.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.92ms | 77.06ms | 3.4× | 3.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.73ms | 78.86ms | 3.3× | 5.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.77ms | 68.40ms | 3.3× | 4.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.18ms | 82.73ms | 2.8× | 5.7% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 29.21ms | 28.19ms | 1.0× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 13.83ms | 10.87ms | 0.8× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 12.52ms | 10.66ms | 0.9× | 1.0% | PASS |
| mem_reads | `oltp_order_range` | 2.82ms | 2.56ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 3.66ms | 3.44ms | 0.9× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.26ms | 4.80ms | 1.5× | 2.2% | PASS |
| mem_reads | `select_random_points` | 18.72ms | 16.45ms | 0.9× | 3.0% | PASS |
| mem_reads | `select_random_ranges` | 2.96ms | 4.28ms | 1.4× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 3.34ms | 3.88ms | 1.2× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 29.18ms | 28.97ms | 1.0× | 1.1% | PASS |
| mem_reads | `index_join` | 9.05ms | 7.25ms | 0.8× | 2.6% | PASS |
| mem_reads | `index_join_scan` | 3.11ms | 4.57ms | 1.5× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 981.33ms | 999.37ms | 1.0× | 4.7% | PASS |
| mem_reads | `table_scan` | 1.12s | 1.08s | 1.0× | 4.8% | PASS |
| mem_reads | `oltp_read_only` | 109.56ms | 102.89ms | 0.9× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 190.55ms | 269.33ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 14.31ms | 28.24ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 52.74ms | 108.54ms | 2.1× | 0.8% | PASS |
| mem_writes | `oltp_update_non_index` | 41.24ms | 64.12ms | 1.6× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 44.83ms | 81.34ms | 1.8× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 23.20ms | 47.11ms | 2.0× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 31.09ms | 38.26ms | 1.2× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 74.86ms | 106.19ms | 1.4× | 0.8% | PASS |
| file_reads | `oltp_point_select` | 103.95ms | 45.52ms | 0.4× | 0.4% | PASS |
| file_reads | `oltp_range_select` | 22.02ms | 12.63ms | 0.6× | 0.6% | PASS |
| file_reads | `oltp_sum_range` | 20.54ms | 12.51ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_order_range` | 3.68ms | 2.79ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.52ms | 3.68ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 11.02ms | 6.78ms | 0.6× | 0.5% | PASS |
| file_reads | `select_random_points` | 26.34ms | 17.93ms | 0.7× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 10.53ms | 6.09ms | 0.6× | 0.5% | PASS |
| file_reads | `covering_index_scan` | 11.23ms | 5.80ms | 0.5× | 0.6% | PASS |
| file_reads | `groupby_scan` | 29.78ms | 29.07ms | 1.0× | 1.2% | PASS |
| file_reads | `index_join` | 13.50ms | 8.68ms | 0.6× | 0.7% | PASS |
| file_reads | `index_join_scan` | 4.06ms | 4.97ms | 1.2× | 0.9% | PASS |
| file_reads | `types_table_scan` | 931.95ms | 965.62ms | 1.0× | 1.8% | PASS |
| file_reads | `table_scan` | 1.07s | 1.07s | 1.0× | 2.8% | PASS |
| file_reads | `oltp_read_only` | 229.87ms | 131.99ms | 0.6× | 0.6% | PASS |
| file_writes | `oltp_bulk_insert` | 274.68ms | 348.32ms | 1.3× | 3.8% | PASS |
| file_writes | `oltp_insert` | 33.84ms | 53.92ms | 1.6× | 6.4% | PASS |
| file_writes | `oltp_update_index` | 182.66ms | 201.68ms | 1.1× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 157.23ms | 129.86ms | 0.8× | 3.9% | PASS |
| file_writes | `oltp_delete_insert` | 193.61ms | 154.03ms | 0.8× | 2.0% | PASS |
| file_writes | `oltp_write_only` | 129.38ms | 105.64ms | 0.8× | 3.0% | PASS |
| file_writes | `types_delete_insert` | 151.80ms | 81.99ms | 0.5× | 3.8% | PASS |
| file_writes | `oltp_read_write` | 185.25ms | 165.65ms | 0.9× | 3.4% | PASS |
| ac_reads | `oltp_point_select` | 54.30ms | 45.96ms | 0.8× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 17.41ms | 12.76ms | 0.7× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 16.07ms | 12.67ms | 0.8× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 3.25ms | 2.80ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.05ms | 3.67ms | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_index_scan` | 6.12ms | 6.80ms | 1.1× | 0.9% | PASS |
| ac_reads | `select_random_points` | 21.92ms | 18.35ms | 0.8× | 1.5% | PASS |
| ac_reads | `select_random_ranges` | 5.59ms | 6.11ms | 1.1× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 6.24ms | 5.82ms | 0.9× | 0.8% | PASS |
| ac_reads | `groupby_scan` | 29.68ms | 29.39ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 11.43ms | 8.97ms | 0.8× | 1.5% | PASS |
| ac_reads | `index_join_scan` | 3.66ms | 4.98ms | 1.4× | 0.8% | PASS |
| ac_reads | `types_table_scan` | 908.53ms | 958.34ms | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.10s | 1.07s | 1.0× | 2.8% | PASS |
| ac_reads | `oltp_read_only` | 148.29ms | 129.07ms | 0.9× | 1.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.29ms | 51.65ms | 2.7× | 10.1% | PASS |
| ac_writes | `oltp_insert_ac` | 21.49ms | 59.76ms | 2.8× | 9.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.57ms | 76.56ms | 3.6× | 20.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 18.54ms | 61.80ms | 3.3× | 13.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 21.55ms | 67.50ms | 3.1× | 12.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 21.28ms | 63.62ms | 3.0× | 13.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.02ms | 59.97ms | 3.0× | 12.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.45ms | 74.98ms | 2.7× | 21.5% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 30.89ms | 30.23ms | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_range_select` | 13.20ms | 11.80ms | 0.9× | 0.8% | PASS |
| mem_reads | `oltp_sum_range` | 13.06ms | 11.41ms | 0.9× | 0.8% | PASS |
| mem_reads | `oltp_order_range` | 2.85ms | 2.79ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.74ms | 3.81ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 3.58ms | 4.98ms | 1.4× | 2.0% | PASS |
| mem_reads | `select_random_points` | 19.79ms | 18.61ms | 0.9× | 0.6% | PASS |
| mem_reads | `select_random_ranges` | 2.92ms | 4.53ms | 1.6× | 0.7% | PASS |
| mem_reads | `covering_index_scan` | 3.42ms | 3.77ms | 1.1× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 31.43ms | 31.79ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 10.18ms | 8.40ms | 0.8× | 1.4% | PASS |
| mem_reads | `index_join_scan` | 3.14ms | 5.17ms | 1.6× | 0.9% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.12s | 1.1× | 1.1% | PASS |
| mem_reads | `table_scan` | 1.26s | 1.26s | 1.0× | 2.9% | PASS |
| mem_reads | `oltp_read_only` | 118.75ms | 117.66ms | 1.0× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 192.62ms | 284.25ms | 1.5× | 0.3% | PASS |
| mem_writes | `oltp_insert` | 15.40ms | 30.96ms | 2.0× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 54.93ms | 118.30ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 40.48ms | 69.32ms | 1.7× | 0.8% | PASS |
| mem_writes | `oltp_delete_insert` | 45.13ms | 85.75ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_write_only` | 23.31ms | 49.91ms | 2.1× | 1.0% | PASS |
| mem_writes | `types_delete_insert` | 32.04ms | 42.86ms | 1.3× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 79.66ms | 118.24ms | 1.5× | 0.8% | PASS |
| file_reads | `oltp_point_select` | 63.63ms | 38.61ms | 0.6× | 0.6% | PASS |
| file_reads | `oltp_range_select` | 16.82ms | 12.97ms | 0.8× | 0.6% | PASS |
| file_reads | `oltp_sum_range` | 16.57ms | 12.54ms | 0.8× | 0.7% | PASS |
| file_reads | `oltp_order_range` | 3.27ms | 2.96ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_distinct_range` | 4.19ms | 3.96ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 7.02ms | 6.24ms | 0.9× | 0.8% | PASS |
| file_reads | `select_random_points` | 24.39ms | 20.22ms | 0.8× | 0.9% | PASS |
| file_reads | `select_random_ranges` | 6.29ms | 5.49ms | 0.9× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 6.88ms | 5.01ms | 0.7× | 1.1% | PASS |
| file_reads | `groupby_scan` | 32.03ms | 31.81ms | 1.0× | 0.4% | PASS |
| file_reads | `index_join` | 12.35ms | 9.50ms | 0.8× | 0.9% | PASS |
| file_reads | `index_join_scan` | 3.59ms | 5.42ms | 1.5× | 1.2% | PASS |
| file_reads | `types_table_scan` | 1.02s | 1.10s | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.16s | 1.22s | 1.1× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 162.30ms | 127.28ms | 0.8× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 266.86ms | 364.11ms | 1.4× | 3.5% | PASS |
| file_writes | `oltp_insert` | 31.30ms | 60.97ms | 1.9× | 3.8% | PASS |
| file_writes | `oltp_update_index` | 173.44ms | 210.40ms | 1.2× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 145.12ms | 135.26ms | 0.9× | 0.9% | PASS |
| file_writes | `oltp_delete_insert` | 173.01ms | 159.59ms | 0.9× | 0.9% | PASS |
| file_writes | `oltp_write_only` | 123.55ms | 110.47ms | 0.9× | 1.0% | PASS |
| file_writes | `types_delete_insert` | 130.60ms | 88.80ms | 0.7× | 7.6% | PASS |
| file_writes | `oltp_read_write` | 179.25ms | 177.39ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_point_select` | 40.55ms | 38.25ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 14.49ms | 12.96ms | 0.9× | 0.6% | PASS |
| ac_reads | `oltp_sum_range` | 14.25ms | 12.47ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_order_range` | 3.05ms | 2.95ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 3.93ms | 3.93ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 4.88ms | 6.23ms | 1.3× | 1.0% | PASS |
| ac_reads | `select_random_points` | 21.83ms | 19.97ms | 0.9× | 1.1% | PASS |
| ac_reads | `select_random_ranges` | 4.06ms | 5.47ms | 1.3× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 4.63ms | 4.95ms | 1.1× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 31.55ms | 31.67ms | 1.0× | 0.4% | PASS |
| ac_reads | `index_join` | 11.30ms | 9.49ms | 0.8× | 1.1% | PASS |
| ac_reads | `index_join_scan` | 3.41ms | 5.39ms | 1.6× | 1.1% | PASS |
| ac_reads | `types_table_scan` | 1.01s | 1.10s | 1.1× | 0.2% | PASS |
| ac_reads | `table_scan` | 1.15s | 1.21s | 1.1× | 0.3% | PASS |
| ac_reads | `oltp_read_only` | 129.07ms | 127.11ms | 1.0× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 29.00ms | 72.63ms | 2.5× | 3.8% | PASS |
| ac_writes | `oltp_insert_ac` | 30.59ms | 85.60ms | 2.8× | 6.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 31.13ms | 93.23ms | 3.0× | 4.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 28.52ms | 80.88ms | 2.8× | 3.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 30.74ms | 88.10ms | 2.9× | 4.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 30.42ms | 86.83ms | 2.9× | 2.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 29.30ms | 82.12ms | 2.8× | 5.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 35.00ms | 94.15ms | 2.7× | 6.5% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 30.20ms | 37.45ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 16.29ms | 19.87ms | 1.2× | 1.2% | PASS |
| mem_reads | `oltp_sum_range` | 15.59ms | 19.57ms | 1.3× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 3.11ms | 3.62ms | 1.2× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.92ms | 4.76ms | 1.2× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.00ms | 5.75ms | 1.4× | 1.4% | PASS |
| mem_reads | `select_random_points` | 25.99ms | 30.79ms | 1.2× | 1.3% | PASS |
| mem_reads | `select_random_ranges` | 6.53ms | 8.19ms | 1.3× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 3.64ms | 4.07ms | 1.1× | 2.7% | PASS |
| mem_reads | `groupby_scan` | 31.88ms | 38.28ms | 1.2× | 0.5% | PASS |
| mem_reads | `index_join` | 6.80ms | 10.02ms | 1.5× | 1.3% | PASS |
| mem_reads | `index_join_scan` | 3.28ms | 5.15ms | 1.6× | 1.5% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.16s | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.27s | 1.28s | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 130.91ms | 159.22ms | 1.2× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 217.34ms | 313.14ms | 1.4× | 0.6% | PASS |
| mem_writes | `oltp_insert` | 16.90ms | 31.85ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 60.96ms | 122.21ms | 2.0× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 45.63ms | 76.39ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_delete_insert` | 44.25ms | 89.86ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 23.46ms | 53.68ms | 2.3× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 28.07ms | 47.13ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 87.84ms | 144.87ms | 1.6× | 1.3% | PASS |
| file_reads | `oltp_point_select` | 58.64ms | 44.04ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 19.31ms | 20.74ms | 1.1× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 18.52ms | 20.53ms | 1.1× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 3.49ms | 3.76ms | 1.1× | 1.3% | PASS |
| file_reads | `oltp_distinct_range` | 4.30ms | 4.88ms | 1.1× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 6.90ms | 6.48ms | 0.9× | 2.0% | PASS |
| file_reads | `select_random_points` | 29.06ms | 31.71ms | 1.1× | 1.5% | PASS |
| file_reads | `select_random_ranges` | 9.79ms | 9.20ms | 0.9× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 6.67ms | 4.91ms | 0.7× | 1.9% | PASS |
| file_reads | `groupby_scan` | 32.38ms | 38.34ms | 1.2× | 0.7% | PASS |
| file_reads | `index_join` | 8.66ms | 10.73ms | 1.2× | 1.4% | PASS |
| file_reads | `index_join_scan` | 3.75ms | 5.29ms | 1.4× | 1.2% | PASS |
| file_reads | `types_table_scan` | 1.03s | 1.15s | 1.1× | 1.1% | PASS |
| file_reads | `table_scan` | 1.25s | 1.26s | 1.0× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 174.41ms | 171.19ms | 1.0× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 230.52ms | 325.32ms | 1.4× | 0.7% | PASS |
| file_writes | `oltp_insert` | 21.89ms | 37.88ms | 1.7× | 1.4% | PASS |
| file_writes | `oltp_update_index` | 79.79ms | 134.89ms | 1.7× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 61.34ms | 87.30ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_delete_insert` | 60.41ms | 100.31ms | 1.7× | 0.8% | PASS |
| file_writes | `oltp_write_only` | 37.28ms | 63.15ms | 1.7× | 1.5% | PASS |
| file_writes | `types_delete_insert` | 38.78ms | 54.30ms | 1.4× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 101.93ms | 154.18ms | 1.5× | 1.0% | PASS |
| ac_reads | `oltp_point_select` | 38.95ms | 43.93ms | 1.1× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 17.34ms | 20.75ms | 1.2× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 16.71ms | 20.60ms | 1.2× | 0.9% | PASS |
| ac_reads | `oltp_order_range` | 3.31ms | 3.77ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.09ms | 4.89ms | 1.2× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 5.18ms | 6.51ms | 1.3× | 1.8% | PASS |
| ac_reads | `select_random_points` | 26.79ms | 31.42ms | 1.2× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 7.72ms | 9.14ms | 1.2× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 4.80ms | 4.86ms | 1.0× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 31.94ms | 38.28ms | 1.2× | 0.6% | PASS |
| ac_reads | `index_join` | 7.62ms | 10.60ms | 1.4× | 1.5% | PASS |
| ac_reads | `index_join_scan` | 3.55ms | 5.32ms | 1.5× | 1.3% | PASS |
| ac_reads | `types_table_scan` | 995.67ms | 1.15s | 1.2× | 1.2% | PASS |
| ac_reads | `table_scan` | 1.19s | 1.25s | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 145.27ms | 170.82ms | 1.2× | 0.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 14.54ms | 41.47ms | 2.9× | 5.4% | PASS |
| ac_writes | `oltp_insert_ac` | 16.11ms | 56.92ms | 3.5× | 7.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 17.89ms | 67.51ms | 3.8× | 8.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 15.47ms | 50.82ms | 3.3× | 7.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 16.36ms | 58.65ms | 3.6× | 6.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 16.93ms | 59.06ms | 3.5× | 7.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 14.75ms | 50.00ms | 3.4× | 9.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 20.99ms | 64.36ms | 3.1× | 4.9% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 9s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 93.84ms | 130.00ms | 72.2% | 0.3% | PASS |
| `status_dirty_many_tables` | 97.32ms | 130.00ms | 74.9% | 0.2% | PASS |
| `diff_regular_working_one_table` | 89.05ms | 120.00ms | 74.2% | 0.3% | PASS |
| `diff_regular_working_many_tables` | 102.51ms | 140.00ms | 73.2% | 0.3% | PASS |
| `diff_stat_working_many_tables` | 102.41ms | 140.00ms | 73.2% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 103.19ms | 140.00ms | 73.7% | 0.3% | PASS |
| `branch_list_many_branches` | 22.33ms | 35.00ms | 63.8% | 1.0% | PASS |
| `branch_create_delete` | 24.39ms | 40.00ms | 61.0% | 1.0% | PASS |
| `checkout_branch_clean` | 59.30ms | 150.00ms | 39.5% | 0.5% | PASS |
| `merge_data_no_conflicts` | 28.09ms | 50.00ms | 56.2% | 0.7% | PASS |
| `merge_schema_no_conflicts` | 21.34ms | 35.00ms | 61.0% | 1.0% | PASS |
| `merge_data_conflicts` | 31.88ms | 180.00ms | 17.7% | 0.8% | PASS |
| `merge_data_conflicts_with_resolve` | 31.77ms | 180.00ms | 17.7% | 0.5% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
