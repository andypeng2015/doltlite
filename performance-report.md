# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-01 17:48 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261026.180.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/41325235013)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.25s | 10.82s | 1.1× | 1.8% | **PASS** |
| Writes | 2.15s | 3.66s | 1.7× | 1.6% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.36s | 11.08s | 1.0× | 1.5% | **PASS** |
| Writes | 3.46s | 4.34s | 1.3× | 2.0% | **PASS** |
| Autocommit writes | 735.86ms | 2.28s | 3.1× | 7.4% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.41s | 2.67s | 1.1× | 1.2% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.25s | 2.30s | 1.0× | 2.1% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.73s | 2.89s | 1.1× | 2.1% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.87s | 2.96s | 1.0× | 1.7% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 429.98ms | 756.58ms | 1.8× | 1.2% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 480.94ms | 807.89ms | 1.7× | 1.9% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 625.16ms | 1.07s | 1.7× | 2.2% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 613.11ms | 1.03s | 1.7× | 1.6% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.70s | 2.74s | 1.0× | 1.6% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.68s | 2.40s | 0.9× | 1.0% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.92s | 2.94s | 1.0× | 1.8% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.07s | 3.00s | 1.0× | 1.5% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 589.11ms | 828.83ms | 1.4× | 1.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.24s | 1.24s | 1.0× | 4.6% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 855.02ms | 1.16s | 1.4× | 2.5% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 777.13ms | 1.10s | 1.4× | 1.8% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.49s | 2.73s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.41s | 2.37s | 1.0× | 0.9% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.77s | 2.95s | 1.1× | 2.1% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 3.00s | 3.03s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 181.81ms | 580.79ms | 3.2× | 5.2% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 164.55ms | 494.77ms | 3.0× | 12.8% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 187.86ms | 576.40ms | 3.1× | 7.2% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 201.64ms | 629.36ms | 3.1× | 7.0% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.68ms | 29.80ms | 1.3× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 10.75ms | 11.57ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_sum_range` | 9.21ms | 11.21ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 2.69ms | 2.93ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 3.63ms | 4.05ms | 1.1× | 0.6% | PASS |
| mem_reads | `oltp_index_scan` | 3.80ms | 5.15ms | 1.4× | 1.9% | PASS |
| mem_reads | `select_random_points` | 9.62ms | 10.86ms | 1.1× | 1.8% | PASS |
| mem_reads | `select_random_ranges` | 2.97ms | 3.94ms | 1.3× | 2.1% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.21ms | 1.0× | 1.2% | PASS |
| mem_reads | `groupby_scan` | 30.30ms | 33.39ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 5.78ms | 7.84ms | 1.4× | 1.3% | PASS |
| mem_reads | `index_join_scan` | 3.21ms | 4.40ms | 1.4× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.16s | 1.1× | 1.1% | PASS |
| mem_reads | `table_scan` | 1.16s | 1.26s | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 104.63ms | 120.99ms | 1.2× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 175.43ms | 277.46ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 14.96ms | 28.57ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 49.47ms | 101.21ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 33.79ms | 63.70ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_delete_insert` | 43.24ms | 77.69ms | 1.8× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 21.58ms | 50.66ms | 2.3× | 1.3% | FAIL |
| mem_writes | `types_delete_insert` | 24.13ms | 41.13ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 67.38ms | 116.16ms | 1.7× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 91.44ms | 46.75ms | 0.5× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 17.84ms | 13.54ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 16.54ms | 13.17ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_order_range` | 3.54ms | 3.27ms | 0.9× | 2.3% | PASS |
| file_reads | `oltp_distinct_range` | 4.53ms | 4.36ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 10.92ms | 7.25ms | 0.7× | 2.2% | PASS |
| file_reads | `select_random_points` | 17.07ms | 12.76ms | 0.7× | 2.8% | PASS |
| file_reads | `select_random_ranges` | 9.74ms | 5.72ms | 0.6× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 11.19ms | 6.28ms | 0.6× | 1.4% | PASS |
| file_reads | `groupby_scan` | 31.40ms | 33.93ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 9.72ms | 9.48ms | 1.0× | 1.7% | PASS |
| file_reads | `index_join_scan` | 4.16ms | 4.89ms | 1.2× | 2.3% | PASS |
| file_reads | `types_table_scan` | 1.03s | 1.16s | 1.1× | 0.8% | PASS |
| file_reads | `table_scan` | 1.23s | 1.27s | 1.0× | 1.7% | PASS |
| file_reads | `oltp_read_only` | 210.26ms | 148.06ms | 0.7× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 189.84ms | 287.59ms | 1.5× | 0.8% | PASS |
| file_writes | `oltp_insert` | 21.88ms | 32.14ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 76.88ms | 115.00ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 57.97ms | 76.31ms | 1.3× | 1.1% | PASS |
| file_writes | `oltp_delete_insert` | 66.39ms | 90.37ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_write_only` | 44.05ms | 58.10ms | 1.3× | 2.3% | PASS |
| file_writes | `types_delete_insert` | 39.16ms | 44.18ms | 1.1× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 92.95ms | 125.13ms | 1.3× | 2.4% | PASS |
| ac_reads | `oltp_point_select` | 46.47ms | 46.73ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 13.64ms | 13.54ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 12.02ms | 13.19ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 3.18ms | 3.25ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.12ms | 4.36ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 6.39ms | 7.23ms | 1.1× | 1.3% | PASS |
| ac_reads | `select_random_points` | 12.57ms | 12.77ms | 1.0× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 5.38ms | 5.75ms | 1.1× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 6.76ms | 6.25ms | 0.9× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 30.99ms | 33.94ms | 1.1× | 0.6% | PASS |
| ac_reads | `index_join` | 7.41ms | 9.48ms | 1.3× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 3.67ms | 4.82ms | 1.3× | 1.6% | PASS |
| ac_reads | `types_table_scan` | 1.03s | 1.16s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.16s | 1.26s | 1.1× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 140.45ms | 147.74ms | 1.1× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.98ms | 59.68ms | 3.0× | 6.1% | PASS |
| ac_writes | `oltp_insert_ac` | 22.96ms | 73.36ms | 3.2× | 7.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.28ms | 83.90ms | 3.5× | 3.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.25ms | 65.07ms | 3.2× | 4.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.41ms | 76.89ms | 3.3× | 7.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.52ms | 75.26ms | 3.3× | 4.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 19.81ms | 65.29ms | 3.3× | 5.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.60ms | 81.34ms | 2.8× | 5.1% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 30.77ms | 28.63ms | 0.9× | 2.6% | PASS |
| mem_reads | `oltp_range_select` | 15.80ms | 11.36ms | 0.7× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 13.55ms | 11.03ms | 0.8× | 2.8% | PASS |
| mem_reads | `oltp_order_range` | 2.88ms | 2.66ms | 0.9× | 1.6% | PASS |
| mem_reads | `oltp_distinct_range` | 3.70ms | 3.53ms | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.33ms | 4.97ms | 1.5× | 1.7% | PASS |
| mem_reads | `select_random_points` | 21.07ms | 16.98ms | 0.8× | 4.2% | PASS |
| mem_reads | `select_random_ranges` | 2.95ms | 4.32ms | 1.5× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 3.34ms | 3.90ms | 1.2× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 29.30ms | 29.01ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 9.88ms | 7.71ms | 0.8× | 3.3% | PASS |
| mem_reads | `index_join_scan` | 3.54ms | 4.98ms | 1.4× | 2.6% | PASS |
| mem_reads | `types_table_scan` | 913.06ms | 980.18ms | 1.1× | 1.3% | PASS |
| mem_reads | `table_scan` | 1.08s | 1.09s | 1.0× | 3.5% | PASS |
| mem_reads | `oltp_read_only` | 109.85ms | 104.55ms | 1.0× | 2.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 178.72ms | 282.58ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 13.90ms | 31.20ms | 2.2× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 54.88ms | 119.66ms | 2.2× | 3.0% | PASS |
| mem_writes | `oltp_update_non_index` | 43.23ms | 71.93ms | 1.7× | 2.4% | PASS |
| mem_writes | `oltp_delete_insert` | 46.63ms | 88.27ms | 1.9× | 2.1% | PASS |
| mem_writes | `oltp_write_only` | 23.95ms | 52.73ms | 2.2× | 1.7% | PASS |
| mem_writes | `types_delete_insert` | 33.60ms | 45.77ms | 1.4× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 86.01ms | 115.75ms | 1.3× | 4.2% | PASS |
| file_reads | `oltp_point_select` | 97.99ms | 44.51ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 22.76ms | 12.55ms | 0.6× | 2.9% | PASS |
| file_reads | `oltp_sum_range` | 20.21ms | 12.50ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.67ms | 2.86ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.45ms | 3.70ms | 0.8× | 0.8% | PASS |
| file_reads | `oltp_index_scan` | 10.26ms | 6.61ms | 0.6× | 0.7% | PASS |
| file_reads | `select_random_points` | 24.96ms | 17.79ms | 0.7× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 9.70ms | 5.89ms | 0.6× | 0.6% | PASS |
| file_reads | `covering_index_scan` | 10.49ms | 5.56ms | 0.5× | 0.8% | PASS |
| file_reads | `groupby_scan` | 29.41ms | 28.86ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 13.24ms | 8.75ms | 0.7× | 1.2% | PASS |
| file_reads | `index_join_scan` | 4.09ms | 5.00ms | 1.2× | 1.8% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.01s | 1.0× | 4.8% | PASS |
| file_reads | `table_scan` | 1.16s | 1.10s | 0.9× | 2.7% | PASS |
| file_reads | `oltp_read_only` | 207.16ms | 127.31ms | 0.6× | 0.5% | PASS |
| file_writes | `oltp_bulk_insert` | 258.80ms | 349.74ms | 1.4× | 4.4% | PASS |
| file_writes | `oltp_insert` | 25.25ms | 55.46ms | 2.2× | 19.4% | PASS |
| file_writes | `oltp_update_index` | 180.10ms | 199.56ms | 1.1× | 4.8% | PASS |
| file_writes | `oltp_update_non_index` | 141.85ms | 129.14ms | 0.9× | 2.0% | PASS |
| file_writes | `oltp_delete_insert` | 183.06ms | 153.99ms | 0.8× | 3.6% | PASS |
| file_writes | `oltp_write_only` | 112.10ms | 108.44ms | 1.0× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 159.27ms | 79.32ms | 0.5× | 15.4% | PASS |
| file_writes | `oltp_read_write` | 181.78ms | 168.83ms | 0.9× | 7.5% | PASS |
| ac_reads | `oltp_point_select` | 51.10ms | 44.93ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 17.40ms | 12.60ms | 0.7× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 15.64ms | 12.58ms | 0.8× | 1.0% | PASS |
| ac_reads | `oltp_order_range` | 3.28ms | 2.87ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 4.00ms | 3.71ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 5.69ms | 6.59ms | 1.2× | 0.6% | PASS |
| ac_reads | `select_random_points` | 20.97ms | 17.96ms | 0.9× | 2.3% | PASS |
| ac_reads | `select_random_ranges` | 5.15ms | 5.90ms | 1.1× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 5.89ms | 5.56ms | 0.9× | 0.5% | PASS |
| ac_reads | `groupby_scan` | 29.20ms | 29.00ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 11.08ms | 8.74ms | 0.8× | 0.9% | PASS |
| ac_reads | `index_join_scan` | 3.67ms | 4.94ms | 1.3× | 0.9% | PASS |
| ac_reads | `types_table_scan` | 892.69ms | 965.79ms | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.12s | 0.9× | 4.6% | PASS |
| ac_reads | `oltp_read_only` | 152.12ms | 131.96ms | 0.9× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 18.56ms | 47.63ms | 2.6× | 10.5% | PASS |
| ac_writes | `oltp_insert_ac` | 20.88ms | 57.05ms | 2.7× | 12.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.03ms | 68.73ms | 3.3× | 10.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.80ms | 58.22ms | 3.3× | 13.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 20.57ms | 63.48ms | 3.1× | 11.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 21.08ms | 66.34ms | 3.1× | 16.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 19.00ms | 61.51ms | 3.2× | 13.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.63ms | 71.83ms | 2.8× | 18.1% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.61ms | 38.74ms | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_range_select` | 17.87ms | 14.24ms | 0.8× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 16.29ms | 14.20ms | 0.9× | 2.6% | PASS |
| mem_reads | `oltp_order_range` | 3.45ms | 3.39ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_distinct_range` | 4.41ms | 4.54ms | 1.0× | 2.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.07ms | 6.29ms | 1.5× | 2.1% | PASS |
| mem_reads | `select_random_points` | 24.09ms | 21.28ms | 0.9× | 3.9% | PASS |
| mem_reads | `select_random_ranges` | 3.58ms | 5.39ms | 1.5× | 2.1% | PASS |
| mem_reads | `covering_index_scan` | 4.21ms | 4.85ms | 1.2× | 2.8% | PASS |
| mem_reads | `groupby_scan` | 35.21ms | 36.17ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 10.91ms | 9.69ms | 0.9× | 3.3% | PASS |
| mem_reads | `index_join_scan` | 4.16ms | 5.63ms | 1.4× | 4.3% | PASS |
| mem_reads | `types_table_scan` | 1.10s | 1.23s | 1.1× | 1.5% | PASS |
| mem_reads | `table_scan` | 1.31s | 1.36s | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 147.81ms | 143.99ms | 1.0× | 2.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 239.03ms | 374.68ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 18.49ms | 41.20ms | 2.2× | 1.3% | PASS |
| mem_writes | `oltp_update_index` | 71.13ms | 156.13ms | 2.2× | 3.1% | PASS |
| mem_writes | `oltp_update_non_index` | 51.59ms | 91.32ms | 1.8× | 2.3% | PASS |
| mem_writes | `oltp_delete_insert` | 55.84ms | 111.45ms | 2.0× | 2.6% | PASS |
| mem_writes | `oltp_write_only` | 30.61ms | 68.64ms | 2.2× | 2.3% | PASS |
| mem_writes | `types_delete_insert` | 40.85ms | 58.60ms | 1.4× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 117.61ms | 163.48ms | 1.4× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 111.04ms | 57.29ms | 0.5× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 26.25ms | 16.41ms | 0.6× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 24.22ms | 16.25ms | 0.7× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 4.57ms | 3.73ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.59ms | 4.87ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 11.41ms | 8.31ms | 0.7× | 2.0% | PASS |
| file_reads | `select_random_points` | 32.93ms | 24.16ms | 0.7× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 10.61ms | 7.17ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.82ms | 6.73ms | 0.6× | 2.5% | PASS |
| file_reads | `groupby_scan` | 36.82ms | 36.72ms | 1.0× | 1.2% | PASS |
| file_reads | `index_join` | 15.88ms | 11.41ms | 0.7× | 4.6% | PASS |
| file_reads | `index_join_scan` | 5.18ms | 6.04ms | 1.2× | 3.8% | PASS |
| file_reads | `types_table_scan` | 1.10s | 1.23s | 1.1× | 1.6% | PASS |
| file_reads | `table_scan` | 1.27s | 1.34s | 1.1× | 1.4% | PASS |
| file_reads | `oltp_read_only` | 258.87ms | 173.30ms | 0.7× | 1.8% | PASS |
| file_writes | `oltp_bulk_insert` | 260.15ms | 389.48ms | 1.5× | 0.9% | PASS |
| file_writes | `oltp_insert` | 26.67ms | 48.91ms | 1.8× | 3.1% | PASS |
| file_writes | `oltp_update_index` | 105.90ms | 169.99ms | 1.6× | 2.9% | PASS |
| file_writes | `oltp_update_non_index` | 91.88ms | 107.33ms | 1.2× | 6.3% | PASS |
| file_writes | `oltp_delete_insert` | 95.10ms | 127.19ms | 1.3× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 62.44ms | 79.21ms | 1.3× | 2.6% | PASS |
| file_writes | `types_delete_insert` | 67.92ms | 67.98ms | 1.0× | 2.4% | PASS |
| file_writes | `oltp_read_write` | 144.95ms | 171.38ms | 1.2× | 2.2% | PASS |
| ac_reads | `oltp_point_select` | 60.98ms | 56.32ms | 0.9× | 2.2% | PASS |
| ac_reads | `oltp_range_select` | 20.98ms | 16.28ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 19.22ms | 16.23ms | 0.8× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 4.08ms | 3.73ms | 0.9× | 2.3% | PASS |
| ac_reads | `oltp_distinct_range` | 5.08ms | 4.89ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.90ms | 8.36ms | 1.2× | 2.3% | PASS |
| ac_reads | `select_random_points` | 27.30ms | 23.77ms | 0.9× | 4.0% | PASS |
| ac_reads | `select_random_ranges` | 6.02ms | 7.15ms | 1.2× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 7.36ms | 6.72ms | 0.9× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 36.09ms | 36.81ms | 1.0× | 1.1% | PASS |
| ac_reads | `index_join` | 13.56ms | 11.35ms | 0.8× | 4.5% | PASS |
| ac_reads | `index_join_scan` | 4.66ms | 6.00ms | 1.3× | 3.1% | PASS |
| ac_reads | `types_table_scan` | 1.11s | 1.23s | 1.1× | 1.8% | PASS |
| ac_reads | `table_scan` | 1.26s | 1.35s | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_read_only` | 188.34ms | 174.16ms | 0.9× | 1.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.36ms | 56.45ms | 2.8× | 7.7% | PASS |
| ac_writes | `oltp_insert_ac` | 23.36ms | 72.47ms | 3.1× | 5.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.08ms | 82.89ms | 3.3× | 6.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.48ms | 65.08ms | 3.2× | 7.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.27ms | 75.33ms | 3.1× | 7.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.40ms | 73.18ms | 3.1× | 7.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.94ms | 69.21ms | 3.3× | 9.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.96ms | 81.79ms | 2.7× | 4.6% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.25ms | 43.14ms | 1.2× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 21.96ms | 22.47ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_sum_range` | 19.10ms | 21.60ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 3.81ms | 4.17ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.82ms | 5.29ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.99ms | 6.82ms | 1.4× | 2.5% | PASS |
| mem_reads | `select_random_points` | 29.10ms | 33.82ms | 1.2× | 1.9% | PASS |
| mem_reads | `select_random_ranges` | 8.16ms | 9.41ms | 1.2× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.44ms | 4.85ms | 1.1× | 2.9% | PASS |
| mem_reads | `groupby_scan` | 37.81ms | 41.77ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 8.45ms | 11.95ms | 1.4× | 2.8% | PASS |
| mem_reads | `index_join_scan` | 4.39ms | 5.84ms | 1.3× | 4.6% | PASS |
| mem_reads | `types_table_scan` | 1.16s | 1.24s | 1.1× | 1.0% | PASS |
| mem_reads | `table_scan` | 1.37s | 1.33s | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_read_only` | 160.40ms | 179.86ms | 1.1× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 244.62ms | 361.03ms | 1.5× | 1.4% | PASS |
| mem_writes | `oltp_insert` | 18.77ms | 36.65ms | 2.0× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 69.88ms | 138.40ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 54.15ms | 92.67ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 51.45ms | 104.37ms | 2.0× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 28.23ms | 64.79ms | 2.3× | 2.1% | PASS |
| mem_writes | `types_delete_insert` | 34.15ms | 58.08ms | 1.7× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 111.85ms | 174.11ms | 1.6× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 104.68ms | 59.71ms | 0.6× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 29.01ms | 23.81ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_sum_range` | 26.31ms | 23.18ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 4.78ms | 4.39ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_distinct_range` | 5.91ms | 5.55ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 12.21ms | 8.33ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 37.11ms | 35.41ms | 1.0× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 15.45ms | 11.29ms | 0.7× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 11.92ms | 6.58ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 39.02ms | 41.99ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 13.10ms | 12.95ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join_scan` | 5.21ms | 6.02ms | 1.2× | 2.3% | PASS |
| file_reads | `types_table_scan` | 1.09s | 1.22s | 1.1× | 2.1% | PASS |
| file_reads | `table_scan` | 1.40s | 1.34s | 1.0× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 268.58ms | 208.02ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 257.85ms | 373.31ms | 1.4× | 1.2% | PASS |
| file_writes | `oltp_insert` | 25.98ms | 42.78ms | 1.6× | 2.0% | PASS |
| file_writes | `oltp_update_index` | 99.44ms | 150.88ms | 1.5× | 1.7% | PASS |
| file_writes | `oltp_update_non_index` | 79.72ms | 104.34ms | 1.3× | 1.9% | PASS |
| file_writes | `oltp_delete_insert` | 78.57ms | 114.62ms | 1.5× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 52.53ms | 74.28ms | 1.4× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 51.29ms | 63.87ms | 1.2× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 131.74ms | 180.06ms | 1.4× | 1.7% | PASS |
| ac_reads | `oltp_point_select` | 57.44ms | 59.07ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 23.73ms | 23.65ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 21.15ms | 23.14ms | 1.1× | 2.0% | PASS |
| ac_reads | `oltp_order_range` | 4.28ms | 4.46ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 5.28ms | 5.56ms | 1.1× | 2.0% | PASS |
| ac_reads | `oltp_index_scan` | 7.36ms | 8.22ms | 1.1× | 1.9% | PASS |
| ac_reads | `select_random_points` | 30.38ms | 34.84ms | 1.1× | 1.8% | PASS |
| ac_reads | `select_random_ranges` | 10.48ms | 11.19ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 7.06ms | 6.45ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 38.04ms | 41.89ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 10.21ms | 12.57ms | 1.2× | 1.6% | PASS |
| ac_reads | `index_join_scan` | 4.67ms | 5.98ms | 1.3× | 3.4% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.24s | 1.1× | 1.0% | PASS |
| ac_reads | `table_scan` | 1.41s | 1.34s | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 197.75ms | 209.11ms | 1.1× | 0.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.13ms | 62.80ms | 2.8× | 5.9% | PASS |
| ac_writes | `oltp_insert_ac` | 25.43ms | 79.64ms | 3.1× | 8.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.33ms | 91.01ms | 3.3× | 6.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.21ms | 71.80ms | 3.2× | 7.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.53ms | 80.84ms | 3.4× | 5.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.72ms | 79.31ms | 3.2× | 4.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.41ms | 72.16ms | 3.2× | 8.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.88ms | 91.80ms | 2.7× | 7.6% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 0s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 89.44ms | 130.00ms | 68.8% | 0.5% | PASS |
| `status_dirty_many_tables` | 92.78ms | 130.00ms | 71.4% | 0.3% | PASS |
| `diff_regular_working_one_table` | 84.84ms | 120.00ms | 70.7% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 98.30ms | 140.00ms | 70.2% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 98.70ms | 140.00ms | 70.5% | 0.4% | PASS |
| `diff_schema_working_many_tables` | 98.37ms | 140.00ms | 70.3% | 0.4% | PASS |
| `branch_list_many_branches` | 18.39ms | 35.00ms | 52.5% | 0.8% | PASS |
| `branch_create_delete` | 21.06ms | 40.00ms | 52.7% | 1.4% | PASS |
| `checkout_branch_clean` | 55.80ms | 150.00ms | 37.2% | 1.0% | PASS |
| `merge_data_no_conflicts` | 25.05ms | 50.00ms | 50.1% | 1.1% | PASS |
| `merge_schema_no_conflicts` | 18.32ms | 35.00ms | 52.3% | 1.6% | PASS |
| `merge_data_conflicts` | 27.44ms | 180.00ms | 15.2% | 0.6% | PASS |
| `merge_data_conflicts_with_resolve` | 27.45ms | 180.00ms | 15.2% | 0.4% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
