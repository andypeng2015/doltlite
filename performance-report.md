# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-14 17:29 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261111.196.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/43612880982)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.33s | 9.72s | 1.0× | 1.9% | **PASS** |
| Writes | 1.89s | 3.26s | 1.7× | 1.5% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.21s | 9.97s | 1.0× | 1.7% | **PASS** |
| Writes | 3.66s | 4.25s | 1.2× | 2.9% | **PASS** |
| Autocommit writes | 781.48ms | 2.41s | 3.1× | 5.7% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 1.60s | 1.66s | 1.0× | 2.3% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.38s | 2.34s | 1.0× | 2.2% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.63s | 2.82s | 1.1× | 2.2% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.71s | 2.90s | 1.1× | 1.5% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 257.10ms | 437.72ms | 1.7× | 1.7% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 457.70ms | 792.68ms | 1.7× | 1.5% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 579.77ms | 1.02s | 1.8× | 1.8% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 593.90ms | 1.01s | 1.7× | 1.3% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 1.96s | 1.79s | 0.9× | 2.1% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.56s | 2.37s | 0.9× | 1.2% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.75s | 2.84s | 1.0× | 2.3% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.94s | 2.96s | 1.0× | 1.7% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 736.38ms | 739.65ms | 1.0× | 4.3% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.34s | 1.29s | 1.0× | 17.4% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 815.80ms | 1.13s | 1.4× | 2.2% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 767.79ms | 1.10s | 1.4× | 1.4% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 1.83s | 1.77s | 1.0× | 2.2% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.49s | 2.40s | 1.0× | 1.2% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.61s | 2.84s | 1.1× | 3.1% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.68s | 2.94s | 1.1× | 1.4% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 165.45ms | 470.74ms | 2.8× | 4.7% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 232.62ms | 769.75ms | 3.3× | 38.6% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 196.56ms | 585.27ms | 3.0× | 5.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 186.85ms | 589.02ms | 3.2× | 5.4% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 13.32ms | 16.27ms | 1.2× | 4.3% | PASS |
| mem_reads | `oltp_range_select` | 5.61ms | 6.22ms | 1.1× | 4.4% | PASS |
| mem_reads | `oltp_sum_range` | 5.42ms | 6.32ms | 1.2× | 3.9% | PASS |
| mem_reads | `oltp_order_range` | 1.53ms | 1.67ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_distinct_range` | 2.14ms | 2.22ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_index_scan` | 2.42ms | 3.06ms | 1.3× | 2.9% | PASS |
| mem_reads | `select_random_points` | 6.22ms | 7.68ms | 1.2× | 6.0% | PASS |
| mem_reads | `select_random_ranges` | 1.89ms | 2.45ms | 1.3× | 4.5% | PASS |
| mem_reads | `covering_index_scan` | 2.33ms | 2.35ms | 1.0× | 1.2% | PASS |
| mem_reads | `groupby_scan` | 18.20ms | 19.14ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 3.55ms | 4.64ms | 1.3× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 1.82ms | 3.56ms | 2.0× | 2.5% | PASS |
| mem_reads | `types_table_scan` | 694.70ms | 716.30ms | 1.0× | 1.2% | PASS |
| mem_reads | `table_scan` | 783.72ms | 801.93ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 59.46ms | 63.96ms | 1.1× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 105.34ms | 160.76ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 8.92ms | 16.73ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 30.40ms | 59.45ms | 2.0× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 21.46ms | 36.88ms | 1.7× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 25.72ms | 45.72ms | 1.8× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 12.75ms | 30.46ms | 2.4× | 2.3% | FAIL |
| mem_writes | `types_delete_insert` | 15.70ms | 24.59ms | 1.6× | 4.7% | PASS |
| mem_writes | `oltp_read_write` | 36.80ms | 63.12ms | 1.7× | 3.9% | PASS |
| file_reads | `oltp_point_select` | 68.10ms | 31.36ms | 0.5× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 11.77ms | 7.93ms | 0.7× | 2.7% | PASS |
| file_reads | `oltp_sum_range` | 11.43ms | 8.02ms | 0.7× | 2.2% | PASS |
| file_reads | `oltp_order_range` | 2.27ms | 1.88ms | 0.8× | 2.7% | PASS |
| file_reads | `oltp_distinct_range` | 2.81ms | 2.40ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 7.98ms | 5.01ms | 0.6× | 3.9% | PASS |
| file_reads | `select_random_points` | 13.77ms | 10.04ms | 0.7× | 3.3% | PASS |
| file_reads | `select_random_ranges` | 7.39ms | 3.88ms | 0.5× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 8.20ms | 4.40ms | 0.5× | 1.7% | PASS |
| file_reads | `groupby_scan` | 19.61ms | 19.48ms | 1.0× | 2.1% | PASS |
| file_reads | `index_join` | 7.46ms | 7.40ms | 1.0× | 4.0% | PASS |
| file_reads | `index_join_scan` | 3.42ms | 4.64ms | 1.4× | 7.4% | PASS |
| file_reads | `types_table_scan` | 738.96ms | 744.26ms | 1.0× | 1.9% | PASS |
| file_reads | `table_scan` | 914.08ms | 848.79ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 141.88ms | 87.11ms | 0.6× | 1.5% | PASS |
| file_writes | `oltp_bulk_insert` | 140.49ms | 200.82ms | 1.4× | 4.5% | PASS |
| file_writes | `oltp_insert` | 19.76ms | 28.11ms | 1.4× | 2.0% | PASS |
| file_writes | `oltp_update_index` | 129.38ms | 118.44ms | 0.9× | 4.6% | PASS |
| file_writes | `oltp_update_non_index` | 101.15ms | 78.64ms | 0.8× | 1.9% | PASS |
| file_writes | `oltp_delete_insert` | 107.08ms | 98.18ms | 0.9× | 5.7% | PASS |
| file_writes | `oltp_write_only` | 73.00ms | 68.52ms | 0.9× | 3.6% | PASS |
| file_writes | `types_delete_insert` | 62.03ms | 47.02ms | 0.8× | 11.8% | PASS |
| file_writes | `oltp_read_write` | 103.47ms | 99.93ms | 1.0× | 4.1% | PASS |
| ac_reads | `oltp_point_select` | 32.54ms | 31.75ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_range_select` | 8.67ms | 8.12ms | 0.9× | 2.5% | PASS |
| ac_reads | `oltp_sum_range` | 8.43ms | 8.25ms | 1.0× | 3.0% | PASS |
| ac_reads | `oltp_order_range` | 2.00ms | 1.95ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 2.54ms | 2.49ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 4.84ms | 5.24ms | 1.1× | 2.9% | PASS |
| ac_reads | `select_random_points` | 11.16ms | 11.01ms | 1.0× | 4.0% | PASS |
| ac_reads | `select_random_ranges` | 4.19ms | 4.16ms | 1.0× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 5.10ms | 4.74ms | 0.9× | 2.0% | PASS |
| ac_reads | `groupby_scan` | 20.00ms | 20.20ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 5.67ms | 8.06ms | 1.4× | 5.8% | PASS |
| ac_reads | `index_join_scan` | 2.86ms | 4.34ms | 1.5× | 9.9% | PASS |
| ac_reads | `types_table_scan` | 726.87ms | 732.08ms | 1.0× | 2.2% | PASS |
| ac_reads | `table_scan` | 902.87ms | 838.79ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 88.20ms | 85.88ms | 1.0× | 2.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.02ms | 47.59ms | 2.5× | 5.6% | PASS |
| ac_writes | `oltp_insert_ac` | 21.25ms | 56.90ms | 2.7× | 3.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.93ms | 65.23ms | 3.0× | 4.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 19.10ms | 54.05ms | 2.8× | 4.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 20.30ms | 59.34ms | 2.9× | 3.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 20.67ms | 62.18ms | 3.0× | 5.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.78ms | 60.35ms | 3.2× | 6.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 24.41ms | 65.09ms | 2.7× | 4.2% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.36ms | 28.11ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 13.92ms | 10.98ms | 0.8× | 2.2% | PASS |
| mem_reads | `oltp_sum_range` | 12.90ms | 10.89ms | 0.8× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 2.80ms | 2.59ms | 0.9× | 2.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.56ms | 3.42ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 3.22ms | 4.79ms | 1.5× | 3.5% | PASS |
| mem_reads | `select_random_points` | 18.63ms | 16.37ms | 0.9× | 3.6% | PASS |
| mem_reads | `select_random_ranges` | 2.88ms | 4.26ms | 1.5× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 3.31ms | 3.66ms | 1.1× | 5.3% | PASS |
| mem_reads | `groupby_scan` | 29.80ms | 29.16ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 9.47ms | 7.80ms | 0.8× | 3.5% | PASS |
| mem_reads | `index_join_scan` | 3.33ms | 5.07ms | 1.5× | 3.2% | PASS |
| mem_reads | `types_table_scan` | 905.42ms | 978.51ms | 1.1× | 1.9% | PASS |
| mem_reads | `table_scan` | 1.22s | 1.13s | 0.9× | 2.2% | PASS |
| mem_reads | `oltp_read_only` | 125.81ms | 109.23ms | 0.9× | 3.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 179.40ms | 284.26ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 13.83ms | 31.17ms | 2.3× | 1.6% | PASS |
| mem_writes | `oltp_update_index` | 51.68ms | 114.47ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 39.84ms | 70.01ms | 1.8× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 44.11ms | 86.09ms | 2.0× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 23.14ms | 51.59ms | 2.2× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 31.29ms | 44.03ms | 1.4× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 74.42ms | 111.04ms | 1.5× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 97.43ms | 45.36ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 23.55ms | 13.10ms | 0.6× | 3.1% | PASS |
| file_reads | `oltp_sum_range` | 21.46ms | 13.14ms | 0.6× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 3.75ms | 2.86ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.58ms | 3.70ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 10.56ms | 6.90ms | 0.7× | 0.9% | PASS |
| file_reads | `select_random_points` | 26.01ms | 18.25ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_ranges` | 9.84ms | 5.99ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 10.62ms | 5.67ms | 0.5× | 0.8% | PASS |
| file_reads | `groupby_scan` | 30.44ms | 29.28ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 13.67ms | 8.93ms | 0.7× | 1.9% | PASS |
| file_reads | `index_join_scan` | 4.11ms | 5.09ms | 1.2× | 1.2% | PASS |
| file_reads | `types_table_scan` | 921.17ms | 978.85ms | 1.1× | 2.9% | PASS |
| file_reads | `table_scan` | 1.16s | 1.11s | 1.0× | 3.0% | PASS |
| file_reads | `oltp_read_only` | 221.32ms | 131.23ms | 0.6× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 268.00ms | 359.02ms | 1.3× | 14.7% | PASS |
| file_writes | `oltp_insert` | 24.65ms | 53.73ms | 2.2× | 17.3% | PASS |
| file_writes | `oltp_update_index` | 187.05ms | 206.41ms | 1.1× | 18.9% | PASS |
| file_writes | `oltp_update_non_index` | 171.02ms | 133.65ms | 0.8× | 22.4% | PASS |
| file_writes | `oltp_delete_insert` | 200.20ms | 158.81ms | 0.8× | 7.9% | PASS |
| file_writes | `oltp_write_only` | 141.05ms | 112.98ms | 0.8× | 27.7% | PASS |
| file_writes | `types_delete_insert` | 162.15ms | 96.95ms | 0.6× | 17.5% | PASS |
| file_writes | `oltp_read_write` | 190.35ms | 168.81ms | 0.9× | 7.4% | PASS |
| ac_reads | `oltp_point_select` | 50.42ms | 45.14ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 17.52ms | 12.71ms | 0.7× | 1.7% | PASS |
| ac_reads | `oltp_sum_range` | 15.89ms | 12.72ms | 0.8× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 3.27ms | 2.83ms | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_distinct_range` | 4.02ms | 3.65ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 5.80ms | 6.73ms | 1.2× | 0.7% | PASS |
| ac_reads | `select_random_points` | 21.64ms | 18.53ms | 0.9× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 5.13ms | 5.98ms | 1.2× | 0.6% | PASS |
| ac_reads | `covering_index_scan` | 5.87ms | 5.65ms | 1.0× | 0.7% | PASS |
| ac_reads | `groupby_scan` | 29.72ms | 28.90ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 11.29ms | 8.90ms | 0.8× | 1.2% | PASS |
| ac_reads | `index_join_scan` | 3.78ms | 5.19ms | 1.4× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 938.51ms | 994.49ms | 1.1× | 3.0% | PASS |
| ac_reads | `table_scan` | 1.22s | 1.12s | 0.9× | 2.2% | PASS |
| ac_reads | `oltp_read_only` | 152.75ms | 132.01ms | 0.9× | 2.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.30ms | 67.24ms | 2.9× | 32.6% | PASS |
| ac_writes | `oltp_insert_ac` | 28.46ms | 94.09ms | 3.3× | 34.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 37.74ms | 106.22ms | 2.8× | 44.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 32.37ms | 104.74ms | 3.2× | 57.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.43ms | 109.16ms | 3.8× | 45.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.04ms | 96.80ms | 3.7× | 32.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.07ms | 89.74ms | 3.9× | 39.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.20ms | 101.75ms | 3.1× | 37.3% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.38ms | 38.08ms | 1.1× | 2.2% | PASS |
| mem_reads | `oltp_range_select` | 16.80ms | 14.21ms | 0.8× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 15.35ms | 13.61ms | 0.9× | 2.8% | PASS |
| mem_reads | `oltp_order_range` | 3.30ms | 3.25ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_distinct_range` | 4.26ms | 4.33ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 4.00ms | 6.30ms | 1.6× | 3.2% | PASS |
| mem_reads | `select_random_points` | 23.75ms | 21.10ms | 0.9× | 5.0% | PASS |
| mem_reads | `select_random_ranges` | 3.52ms | 5.37ms | 1.5× | 2.6% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.83ms | 1.2× | 2.5% | PASS |
| mem_reads | `groupby_scan` | 35.47ms | 35.18ms | 1.0× | 1.2% | PASS |
| mem_reads | `index_join` | 10.92ms | 9.66ms | 0.9× | 4.2% | PASS |
| mem_reads | `index_join_scan` | 4.03ms | 5.63ms | 1.4× | 4.8% | PASS |
| mem_reads | `types_table_scan` | 1.15s | 1.23s | 1.1× | 1.7% | PASS |
| mem_reads | `table_scan` | 1.18s | 1.30s | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_read_only` | 137.05ms | 138.43ms | 1.0× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 233.80ms | 369.24ms | 1.6× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 17.92ms | 39.81ms | 2.2× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 64.29ms | 144.12ms | 2.2× | 1.9% | PASS |
| mem_writes | `oltp_update_non_index` | 47.85ms | 87.89ms | 1.8× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 52.45ms | 106.41ms | 2.0× | 1.7% | PASS |
| mem_writes | `oltp_write_only` | 27.91ms | 64.67ms | 2.3× | 2.9% | FAIL |
| mem_writes | `types_delete_insert` | 38.87ms | 57.15ms | 1.5× | 2.5% | PASS |
| mem_writes | `oltp_read_write` | 96.67ms | 148.30ms | 1.5× | 2.2% | PASS |
| file_reads | `oltp_point_select` | 103.81ms | 55.88ms | 0.5× | 1.8% | PASS |
| file_reads | `oltp_range_select` | 24.42ms | 16.56ms | 0.7× | 3.6% | PASS |
| file_reads | `oltp_sum_range` | 23.61ms | 15.82ms | 0.7× | 3.5% | PASS |
| file_reads | `oltp_order_range` | 4.46ms | 3.66ms | 0.8× | 4.6% | PASS |
| file_reads | `oltp_distinct_range` | 5.46ms | 4.86ms | 0.9× | 3.6% | PASS |
| file_reads | `oltp_index_scan` | 11.01ms | 8.33ms | 0.8× | 2.3% | PASS |
| file_reads | `select_random_points` | 30.23ms | 23.85ms | 0.8× | 3.1% | PASS |
| file_reads | `select_random_ranges` | 10.31ms | 7.09ms | 0.7× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 11.11ms | 6.68ms | 0.6× | 1.9% | PASS |
| file_reads | `groupby_scan` | 36.46ms | 35.72ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join` | 14.71ms | 11.22ms | 0.8× | 3.7% | PASS |
| file_reads | `index_join_scan` | 4.88ms | 5.88ms | 1.2× | 4.9% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.19s | 1.1× | 1.0% | PASS |
| file_reads | `table_scan` | 1.18s | 1.29s | 1.1× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 244.62ms | 165.27ms | 0.7× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 254.33ms | 383.86ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 25.28ms | 47.41ms | 1.9× | 1.3% | PASS |
| file_writes | `oltp_update_index` | 98.38ms | 162.77ms | 1.7× | 2.0% | PASS |
| file_writes | `oltp_update_non_index` | 91.57ms | 103.19ms | 1.1× | 9.2% | PASS |
| file_writes | `oltp_delete_insert` | 87.73ms | 122.47ms | 1.4× | 2.0% | PASS |
| file_writes | `oltp_write_only` | 58.31ms | 76.94ms | 1.3× | 2.3% | PASS |
| file_writes | `types_delete_insert` | 64.90ms | 66.01ms | 1.0× | 2.4% | PASS |
| file_writes | `oltp_read_write` | 135.31ms | 162.78ms | 1.2× | 3.4% | PASS |
| ac_reads | `oltp_point_select` | 58.44ms | 56.22ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_range_select` | 20.49ms | 16.53ms | 0.8× | 3.1% | PASS |
| ac_reads | `oltp_sum_range` | 18.34ms | 16.00ms | 0.9× | 5.2% | PASS |
| ac_reads | `oltp_order_range` | 4.05ms | 3.75ms | 0.9× | 6.1% | PASS |
| ac_reads | `oltp_distinct_range` | 4.95ms | 4.80ms | 1.0× | 4.2% | PASS |
| ac_reads | `oltp_index_scan` | 6.52ms | 8.33ms | 1.3× | 3.3% | PASS |
| ac_reads | `select_random_points` | 25.86ms | 24.41ms | 0.9× | 4.7% | PASS |
| ac_reads | `select_random_ranges` | 5.90ms | 7.18ms | 1.2× | 2.5% | PASS |
| ac_reads | `covering_index_scan` | 6.75ms | 6.64ms | 1.0× | 3.1% | PASS |
| ac_reads | `groupby_scan` | 35.46ms | 35.45ms | 1.0× | 1.1% | PASS |
| ac_reads | `index_join` | 12.39ms | 11.13ms | 0.9× | 3.2% | PASS |
| ac_reads | `index_join_scan` | 4.39ms | 5.88ms | 1.3× | 4.1% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.19s | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.29s | 1.1× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 174.92ms | 166.49ms | 1.0× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.68ms | 57.57ms | 2.7× | 4.5% | PASS |
| ac_writes | `oltp_insert_ac` | 24.67ms | 74.27ms | 3.0× | 5.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.34ms | 84.19ms | 3.2× | 7.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.96ms | 66.59ms | 3.2× | 5.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.34ms | 77.32ms | 3.1× | 5.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.01ms | 75.46ms | 3.1× | 6.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.95ms | 67.92ms | 3.0× | 8.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 30.63ms | 81.95ms | 2.7× | 3.8% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.18ms | 41.11ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 20.12ms | 21.66ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 17.94ms | 20.82ms | 1.2× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 3.68ms | 3.99ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.69ms | 5.15ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 4.53ms | 6.22ms | 1.4× | 2.5% | PASS |
| mem_reads | `select_random_points` | 26.12ms | 30.66ms | 1.2× | 1.6% | PASS |
| mem_reads | `select_random_ranges` | 7.76ms | 9.05ms | 1.2× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.21ms | 4.44ms | 1.1× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 37.00ms | 40.45ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 7.92ms | 10.78ms | 1.4× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 3.98ms | 5.63ms | 1.4× | 2.4% | PASS |
| mem_reads | `types_table_scan` | 1.08s | 1.21s | 1.1× | 1.0% | PASS |
| mem_reads | `table_scan` | 1.30s | 1.31s | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 156.13ms | 176.04ms | 1.1× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 243.27ms | 358.78ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 18.64ms | 36.41ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 67.11ms | 135.10ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 51.26ms | 89.72ms | 1.8× | 1.4% | PASS |
| mem_writes | `oltp_delete_insert` | 49.44ms | 101.67ms | 2.1× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 26.89ms | 62.81ms | 2.3× | 1.7% | FAIL |
| mem_writes | `types_delete_insert` | 32.83ms | 56.40ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 104.46ms | 168.89ms | 1.6× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 103.48ms | 59.48ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 27.74ms | 23.68ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 25.80ms | 23.01ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 4.68ms | 4.33ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 5.81ms | 5.58ms | 1.0× | 2.0% | PASS |
| file_reads | `oltp_index_scan` | 11.92ms | 8.24ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 35.62ms | 34.07ms | 1.0× | 1.7% | PASS |
| file_reads | `select_random_ranges` | 15.25ms | 11.11ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.67ms | 6.51ms | 0.6× | 1.1% | PASS |
| file_reads | `groupby_scan` | 38.52ms | 40.90ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 12.31ms | 12.42ms | 1.0× | 1.7% | PASS |
| file_reads | `index_join_scan` | 5.04ms | 5.96ms | 1.2× | 1.7% | PASS |
| file_reads | `types_table_scan` | 1.08s | 1.21s | 1.1× | 1.5% | PASS |
| file_reads | `table_scan` | 1.30s | 1.31s | 1.0× | 1.8% | PASS |
| file_reads | `oltp_read_only` | 260.38ms | 204.04ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 257.72ms | 370.82ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 25.70ms | 42.56ms | 1.7× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 96.97ms | 148.94ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 77.80ms | 103.23ms | 1.3× | 2.4% | PASS |
| file_writes | `oltp_delete_insert` | 77.26ms | 114.17ms | 1.5× | 1.3% | PASS |
| file_writes | `oltp_write_only` | 51.36ms | 73.31ms | 1.4× | 1.2% | PASS |
| file_writes | `types_delete_insert` | 50.41ms | 62.91ms | 1.2× | 1.8% | PASS |
| file_writes | `oltp_read_write` | 130.56ms | 179.16ms | 1.4× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 56.05ms | 59.07ms | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 22.38ms | 23.50ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 20.29ms | 22.63ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 4.00ms | 4.24ms | 1.1× | 1.4% | PASS |
| ac_reads | `oltp_distinct_range` | 5.06ms | 5.42ms | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 7.06ms | 8.14ms | 1.2× | 1.8% | PASS |
| ac_reads | `select_random_points` | 28.92ms | 33.40ms | 1.2× | 1.6% | PASS |
| ac_reads | `select_random_ranges` | 10.17ms | 11.06ms | 1.1× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 6.78ms | 6.44ms | 1.0× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 37.59ms | 40.65ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 9.50ms | 12.23ms | 1.3× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 4.43ms | 5.86ms | 1.3× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.21s | 1.1× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.22s | 1.30s | 1.1× | 2.3% | PASS |
| ac_reads | `oltp_read_only` | 186.91ms | 201.47ms | 1.1× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.48ms | 56.99ms | 2.8× | 5.7% | PASS |
| ac_writes | `oltp_insert_ac` | 23.04ms | 73.46ms | 3.2× | 4.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.13ms | 83.95ms | 3.3× | 5.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.47ms | 67.46ms | 3.0× | 7.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.95ms | 76.40ms | 3.3× | 5.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.98ms | 75.85ms | 3.3× | 4.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.21ms | 71.11ms | 3.5× | 9.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.59ms | 83.81ms | 2.8× | 5.2% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 29s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 56.34ms | 130.00ms | 43.3% | 1.3% | PASS |
| `status_dirty_many_tables` | 58.76ms | 130.00ms | 45.2% | 0.8% | PASS |
| `diff_regular_working_one_table` | 54.19ms | 120.00ms | 45.2% | 0.6% | PASS |
| `diff_regular_working_many_tables` | 61.20ms | 140.00ms | 43.7% | 0.8% | PASS |
| `diff_stat_working_many_tables` | 60.90ms | 140.00ms | 43.5% | 1.1% | PASS |
| `diff_schema_working_many_tables` | 62.08ms | 140.00ms | 44.3% | 0.8% | PASS |
| `branch_list_many_branches` | 13.43ms | 35.00ms | 38.4% | 1.1% | PASS |
| `branch_create_delete` | 15.23ms | 40.00ms | 38.1% | 1.5% | PASS |
| `checkout_branch_clean` | 98.34ms | 150.00ms | 65.6% | 10.5% | PASS |
| `merge_data_no_conflicts` | 25.67ms | 50.00ms | 51.3% | 2.5% | PASS |
| `merge_schema_no_conflicts` | 13.43ms | 35.00ms | 38.4% | 2.6% | PASS |
| `merge_data_conflicts` | 18.28ms | 180.00ms | 10.2% | 1.0% | PASS |
| `merge_data_conflicts_with_resolve` | 18.51ms | 180.00ms | 10.3% | 1.4% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
