# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-12 14:33 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/34695203928)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.46s | 10.97s | 1.0× | 1.3% | **PASS** |
| Writes | 2.21s | 3.68s | 1.7× | 1.1% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.22s | 11.18s | 1.0× | 1.2% | **PASS** |
| Writes | 3.12s | 4.05s | 1.3× | 1.7% | **PASS** |
| Autocommit writes | 833.15ms | 2.96s | 3.6× | 5.5% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.45s | 2.59s | 1.1× | 1.3% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.63s | 2.76s | 1.0× | 1.4% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.53s | 2.73s | 1.1× | 1.2% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.85s | 2.90s | 1.0× | 1.3% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 439.58ms | 727.93ms | 1.7× | 1.2% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 589.86ms | 995.74ms | 1.7× | 1.1% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 584.20ms | 981.80ms | 1.7× | 1.0% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 598.17ms | 973.26ms | 1.6× | 0.9% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.68s | 2.66s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.89s | 2.82s | 1.0× | 1.3% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.79s | 2.79s | 1.0× | 1.0% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.86s | 2.90s | 1.0× | 1.3% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 590.29ms | 791.04ms | 1.3× | 1.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 920.00ms | 1.10s | 1.2× | 3.2% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 812.48ms | 1.07s | 1.3× | 1.6% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 800.51ms | 1.08s | 1.4× | 1.9% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.51s | 2.65s | 1.1× | 1.2% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.73s | 2.82s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.63s | 2.79s | 1.1× | 1.2% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.79s | 2.92s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 198.49ms | 728.92ms | 3.7× | 6.0% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 208.38ms | 725.27ms | 3.5× | 5.0% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 211.78ms | 750.66ms | 3.5× | 4.5% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 214.51ms | 754.42ms | 3.5× | 6.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.24ms | 29.83ms | 1.2× | 2.8% | PASS |
| mem_reads | `oltp_range_select` | 10.06ms | 11.18ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_sum_range` | 9.54ms | 11.17ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 2.53ms | 2.78ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.53ms | 3.92ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.66ms | 5.03ms | 1.4× | 1.5% | PASS |
| mem_reads | `select_random_points` | 9.02ms | 10.62ms | 1.2× | 3.2% | PASS |
| mem_reads | `select_random_ranges` | 2.78ms | 3.90ms | 1.4× | 2.4% | PASS |
| mem_reads | `covering_index_scan` | 4.20ms | 4.16ms | 1.0× | 0.6% | PASS |
| mem_reads | `groupby_scan` | 30.01ms | 32.87ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 5.74ms | 7.81ms | 1.4× | 1.0% | PASS |
| mem_reads | `index_join_scan` | 2.99ms | 4.32ms | 1.4× | 2.0% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 1.18s | 1.23s | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 102.89ms | 117.43ms | 1.1× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.63ms | 275.32ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 15.34ms | 28.09ms | 1.8× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 48.24ms | 93.99ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 34.59ms | 59.94ms | 1.7× | 1.4% | PASS |
| mem_writes | `oltp_delete_insert` | 44.60ms | 73.19ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 21.47ms | 46.42ms | 2.2× | 1.1% | PASS |
| mem_writes | `types_delete_insert` | 24.77ms | 38.52ms | 1.6× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 65.93ms | 112.47ms | 1.7× | 1.8% | PASS |
| file_reads | `oltp_point_select` | 97.73ms | 49.51ms | 0.5× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 17.37ms | 13.34ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 17.06ms | 13.39ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 3.46ms | 3.21ms | 0.9× | 3.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.49ms | 4.29ms | 1.0× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 11.47ms | 7.56ms | 0.7× | 1.2% | PASS |
| file_reads | `select_random_points` | 17.15ms | 13.05ms | 0.8× | 3.0% | PASS |
| file_reads | `select_random_ranges` | 10.35ms | 5.97ms | 0.6× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.90ms | 6.57ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 31.29ms | 33.47ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 9.94ms | 9.63ms | 1.0× | 1.2% | PASS |
| file_reads | `index_join_scan` | 4.12ms | 4.75ms | 1.2× | 1.8% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.18s | 1.22s | 1.0× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 210.37ms | 147.29ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 196.79ms | 283.35ms | 1.4× | 0.8% | PASS |
| file_writes | `oltp_insert` | 21.74ms | 32.03ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 75.88ms | 105.68ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 57.33ms | 70.34ms | 1.2× | 1.2% | PASS |
| file_writes | `oltp_delete_insert` | 66.60ms | 82.18ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 43.76ms | 53.88ms | 1.2× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 38.77ms | 44.08ms | 1.1× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 89.43ms | 119.50ms | 1.3× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 48.28ms | 49.63ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 12.55ms | 13.35ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 11.93ms | 13.42ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 2.93ms | 3.15ms | 1.1× | 2.2% | PASS |
| ac_reads | `oltp_distinct_range` | 3.95ms | 4.28ms | 1.1× | 2.0% | PASS |
| ac_reads | `oltp_index_scan` | 6.41ms | 7.45ms | 1.2× | 2.0% | PASS |
| ac_reads | `select_random_points` | 12.21ms | 13.02ms | 1.1× | 1.9% | PASS |
| ac_reads | `select_random_ranges` | 5.41ms | 5.94ms | 1.1× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 6.90ms | 6.46ms | 0.9× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 30.56ms | 33.40ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 7.26ms | 9.49ms | 1.3× | 1.1% | PASS |
| ac_reads | `index_join_scan` | 3.57ms | 4.75ms | 1.3× | 1.2% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.4% | PASS |
| ac_reads | `table_scan` | 1.17s | 1.22s | 1.0× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 137.16ms | 147.21ms | 1.1× | 0.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.86ms | 77.05ms | 3.5× | 5.6% | PASS |
| ac_writes | `oltp_insert_ac` | 25.24ms | 91.20ms | 3.6× | 7.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.00ms | 98.81ms | 3.7× | 5.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.06ms | 85.47ms | 3.9× | 6.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.61ms | 95.99ms | 3.9× | 6.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.43ms | 94.29ms | 3.9× | 5.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.79ms | 85.72ms | 3.9× | 5.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.50ms | 100.39ms | 3.2× | 7.2% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.24ms | 37.58ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 16.90ms | 14.46ms | 0.9× | 3.2% | PASS |
| mem_reads | `oltp_sum_range` | 15.41ms | 13.63ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 3.19ms | 3.13ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.29ms | 4.29ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.94ms | 6.05ms | 1.5× | 1.6% | PASS |
| mem_reads | `select_random_points` | 22.48ms | 20.95ms | 0.9× | 2.0% | PASS |
| mem_reads | `select_random_ranges` | 3.55ms | 5.23ms | 1.5× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.50ms | 1.1× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 34.69ms | 34.59ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 10.48ms | 9.14ms | 0.9× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.79ms | 5.51ms | 1.5× | 2.2% | PASS |
| mem_reads | `types_table_scan` | 1.10s | 1.18s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.24s | 1.28s | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 138.50ms | 136.99ms | 1.0× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 238.89ms | 366.21ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 17.97ms | 38.33ms | 2.1× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 65.22ms | 140.96ms | 2.2× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 48.35ms | 85.54ms | 1.8× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 54.28ms | 103.74ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 27.98ms | 61.55ms | 2.2× | 1.0% | PASS |
| mem_writes | `types_delete_insert` | 39.05ms | 53.79ms | 1.4× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 98.11ms | 145.62ms | 1.5× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 110.30ms | 58.24ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 24.72ms | 16.96ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 23.61ms | 16.30ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 4.14ms | 3.51ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 5.30ms | 4.71ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 11.61ms | 8.54ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_points` | 31.36ms | 24.18ms | 0.8× | 2.5% | PASS |
| file_reads | `select_random_ranges` | 11.05ms | 7.33ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.68ms | 6.87ms | 0.6× | 1.1% | PASS |
| file_reads | `groupby_scan` | 35.57ms | 35.11ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 14.81ms | 11.33ms | 0.8× | 2.4% | PASS |
| file_reads | `index_join_scan` | 4.79ms | 6.01ms | 1.3× | 3.0% | PASS |
| file_reads | `types_table_scan` | 1.10s | 1.18s | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 1.25s | 1.28s | 1.0× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 249.79ms | 167.71ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 264.35ms | 380.20ms | 1.4× | 0.6% | PASS |
| file_writes | `oltp_insert` | 25.41ms | 45.76ms | 1.8× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 128.19ms | 160.02ms | 1.2× | 11.2% | PASS |
| file_writes | `oltp_update_non_index` | 98.52ms | 100.51ms | 1.0× | 7.7% | PASS |
| file_writes | `oltp_delete_insert` | 97.19ms | 119.32ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 89.32ms | 73.69ms | 0.8× | 13.0% | PASS |
| file_writes | `types_delete_insert` | 71.31ms | 63.10ms | 0.9× | 1.0% | PASS |
| file_writes | `oltp_read_write` | 145.71ms | 157.73ms | 1.1× | 4.6% | PASS |
| ac_reads | `oltp_point_select` | 60.75ms | 57.89ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 20.42ms | 16.82ms | 0.8× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 18.93ms | 16.25ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 3.75ms | 3.53ms | 0.9× | 2.6% | PASS |
| ac_reads | `oltp_distinct_range` | 4.83ms | 4.70ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 6.65ms | 8.52ms | 1.3× | 1.9% | PASS |
| ac_reads | `select_random_points` | 26.44ms | 24.23ms | 0.9× | 2.8% | PASS |
| ac_reads | `select_random_ranges` | 6.17ms | 7.32ms | 1.2× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 6.94ms | 6.87ms | 1.0× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 35.21ms | 35.22ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 12.51ms | 11.23ms | 0.9× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 4.28ms | 6.00ms | 1.4× | 1.7% | PASS |
| ac_reads | `types_table_scan` | 1.10s | 1.17s | 1.1× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.25s | 1.28s | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 177.21ms | 168.01ms | 0.9× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.11ms | 77.22ms | 3.2× | 5.2% | PASS |
| ac_writes | `oltp_insert_ac` | 26.27ms | 88.43ms | 3.4× | 8.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.76ms | 102.86ms | 3.8× | 3.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.02ms | 84.13ms | 3.5× | 6.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.79ms | 93.06ms | 3.6× | 4.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.30ms | 92.78ms | 3.7× | 4.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.34ms | 86.24ms | 3.5× | 8.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.77ms | 100.57ms | 3.2× | 4.3% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.83ms | 37.54ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_range_select` | 15.07ms | 14.24ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_sum_range` | 14.51ms | 13.42ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_order_range` | 3.09ms | 3.06ms | 1.0× | 1.6% | PASS |
| mem_reads | `oltp_distinct_range` | 4.19ms | 4.24ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.92ms | 5.96ms | 1.5× | 1.1% | PASS |
| mem_reads | `select_random_points` | 19.99ms | 20.22ms | 1.0× | 1.2% | PASS |
| mem_reads | `select_random_ranges` | 3.40ms | 5.21ms | 1.5× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.44ms | 1.1× | 1.8% | PASS |
| mem_reads | `groupby_scan` | 34.49ms | 34.46ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 9.64ms | 8.79ms | 0.9× | 1.4% | PASS |
| mem_reads | `index_join_scan` | 3.57ms | 5.18ms | 1.5× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 0.3% | PASS |
| mem_reads | `table_scan` | 1.19s | 1.27s | 1.1× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 132.10ms | 135.32ms | 1.0× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 248.40ms | 367.33ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 18.72ms | 38.10ms | 2.0× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 62.26ms | 135.02ms | 2.2× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 46.32ms | 83.19ms | 1.8× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 51.58ms | 101.92ms | 2.0× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 26.76ms | 60.27ms | 2.3× | 1.1% | PASS |
| mem_writes | `types_delete_insert` | 36.87ms | 52.36ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_read_write` | 93.29ms | 143.61ms | 1.5× | 1.5% | PASS |
| file_reads | `oltp_point_select` | 109.99ms | 57.70ms | 0.5× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 23.73ms | 16.58ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_sum_range` | 22.95ms | 15.97ms | 0.7× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 4.27ms | 3.41ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_distinct_range` | 5.36ms | 4.59ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 11.87ms | 8.49ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 30.80ms | 23.62ms | 0.8× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 11.16ms | 7.36ms | 0.7× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 12.07ms | 6.87ms | 0.6× | 1.1% | PASS |
| file_reads | `groupby_scan` | 35.83ms | 34.80ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 14.85ms | 10.82ms | 0.7× | 3.9% | PASS |
| file_reads | `index_join_scan` | 4.72ms | 5.65ms | 1.2× | 1.5% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.16s | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.19s | 1.27s | 1.1× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 247.78ms | 166.97ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 268.52ms | 379.10ms | 1.4× | 0.8% | PASS |
| file_writes | `oltp_insert` | 26.04ms | 45.27ms | 1.7× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 96.79ms | 151.64ms | 1.6× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 85.51ms | 96.07ms | 1.1× | 6.7% | PASS |
| file_writes | `oltp_delete_insert` | 86.90ms | 114.72ms | 1.3× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 57.74ms | 70.38ms | 1.2× | 1.9% | PASS |
| file_writes | `types_delete_insert` | 63.76ms | 61.16ms | 1.0× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 127.21ms | 154.71ms | 1.2× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 59.58ms | 57.88ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 18.65ms | 16.63ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 17.57ms | 15.99ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 3.71ms | 3.39ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.78ms | 4.59ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.66ms | 8.48ms | 1.3× | 2.0% | PASS |
| ac_reads | `select_random_points` | 24.50ms | 23.66ms | 1.0× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 6.03ms | 7.33ms | 1.2× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 6.91ms | 6.83ms | 1.0× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 35.13ms | 34.96ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 11.87ms | 10.75ms | 0.9× | 1.5% | PASS |
| ac_reads | `index_join_scan` | 4.14ms | 5.63ms | 1.4× | 2.0% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.16s | 1.1× | 0.4% | PASS |
| ac_reads | `table_scan` | 1.19s | 1.27s | 1.1× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 173.69ms | 167.06ms | 1.0× | 0.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.02ms | 79.24ms | 3.3× | 4.1% | PASS |
| ac_writes | `oltp_insert_ac` | 26.94ms | 93.67ms | 3.5× | 4.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.29ms | 104.06ms | 3.8× | 4.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.51ms | 88.25ms | 3.8× | 5.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.58ms | 97.33ms | 3.7× | 5.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.98ms | 95.34ms | 3.7× | 4.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.71ms | 90.35ms | 3.7× | 5.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.75ms | 102.41ms | 3.1× | 4.1% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.79ms | 42.81ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 20.05ms | 21.55ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_sum_range` | 17.71ms | 20.59ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 3.54ms | 3.83ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.65ms | 5.04ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.60ms | 6.24ms | 1.4× | 1.7% | PASS |
| mem_reads | `select_random_points` | 28.58ms | 32.87ms | 1.2× | 1.8% | PASS |
| mem_reads | `select_random_ranges` | 7.97ms | 9.07ms | 1.1× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.32ms | 4.79ms | 1.1× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 36.95ms | 39.99ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 8.18ms | 11.42ms | 1.4× | 1.3% | PASS |
| mem_reads | `index_join_scan` | 3.98ms | 5.70ms | 1.4× | 3.7% | PASS |
| mem_reads | `types_table_scan` | 1.13s | 1.21s | 1.1× | 2.2% | PASS |
| mem_reads | `table_scan` | 1.38s | 1.31s | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 154.12ms | 173.62ms | 1.1× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 250.78ms | 354.86ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 19.07ms | 35.23ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 67.35ms | 129.58ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 51.42ms | 85.33ms | 1.7× | 0.9% | PASS |
| mem_writes | `oltp_delete_insert` | 49.29ms | 95.75ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 26.39ms | 57.72ms | 2.2× | 0.7% | PASS |
| mem_writes | `types_delete_insert` | 32.29ms | 53.28ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 101.58ms | 161.51ms | 1.6× | 1.4% | PASS |
| file_reads | `oltp_point_select` | 108.44ms | 60.53ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 27.51ms | 23.48ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 26.08ms | 22.85ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 4.37ms | 4.10ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.62ms | 5.37ms | 1.0× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 12.56ms | 8.52ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 39.22ms | 37.20ms | 0.9× | 1.5% | PASS |
| file_reads | `select_random_ranges` | 15.99ms | 11.29ms | 0.7× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 12.18ms | 6.83ms | 0.6× | 1.3% | PASS |
| file_reads | `groupby_scan` | 37.87ms | 40.13ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 12.27ms | 12.41ms | 1.0× | 1.4% | PASS |
| file_reads | `index_join_scan` | 4.90ms | 5.80ms | 1.2× | 2.8% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.24s | 1.28s | 1.0× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 262.88ms | 201.82ms | 0.8× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 266.50ms | 366.26ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 27.19ms | 42.03ms | 1.5× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 102.33ms | 147.99ms | 1.4× | 1.9% | PASS |
| file_writes | `oltp_update_non_index` | 82.07ms | 102.96ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_delete_insert` | 82.00ms | 113.77ms | 1.4× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 52.88ms | 70.85ms | 1.3× | 2.1% | PASS |
| file_writes | `types_delete_insert` | 50.92ms | 61.73ms | 1.2× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 136.62ms | 178.91ms | 1.3× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 58.81ms | 60.84ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 22.99ms | 23.68ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 20.78ms | 22.78ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 3.90ms | 4.10ms | 1.1× | 2.1% | PASS |
| ac_reads | `oltp_distinct_range` | 4.97ms | 5.35ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 7.30ms | 8.38ms | 1.1× | 1.8% | PASS |
| ac_reads | `select_random_points` | 31.79ms | 36.21ms | 1.1× | 1.6% | PASS |
| ac_reads | `select_random_ranges` | 10.57ms | 11.20ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.08ms | 6.71ms | 0.9× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 36.45ms | 39.91ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 9.67ms | 12.25ms | 1.3× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 4.34ms | 5.82ms | 1.3× | 1.4% | PASS |
| ac_reads | `types_table_scan` | 1.10s | 1.19s | 1.1× | 1.9% | PASS |
| ac_reads | `table_scan` | 1.28s | 1.29s | 1.0× | 4.3% | PASS |
| ac_reads | `oltp_read_only` | 193.80ms | 204.57ms | 1.1× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.16ms | 77.96ms | 3.2× | 7.8% | PASS |
| ac_writes | `oltp_insert_ac` | 25.92ms | 93.34ms | 3.6× | 5.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.27ms | 103.32ms | 3.7× | 4.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.60ms | 87.81ms | 3.6× | 5.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.31ms | 97.30ms | 3.7× | 6.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.87ms | 97.80ms | 3.6× | 7.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.45ms | 91.91ms | 3.8× | 7.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.93ms | 104.98ms | 3.1× | 5.4% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 13s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 95.35ms | 130.00ms | 73.3% | 0.7% | PASS |
| `status_dirty_many_tables` | 98.36ms | 130.00ms | 75.7% | 0.5% | PASS |
| `diff_regular_working_one_table` | 90.94ms | 120.00ms | 75.8% | 0.8% | PASS |
| `diff_regular_working_many_tables` | 104.53ms | 140.00ms | 74.7% | 0.6% | PASS |
| `diff_stat_working_many_tables` | 104.61ms | 140.00ms | 74.7% | 0.6% | PASS |
| `diff_schema_working_many_tables` | 105.34ms | 140.00ms | 75.2% | 0.6% | PASS |
| `branch_list_many_branches` | 24.26ms | 35.00ms | 69.3% | 1.7% | PASS |
| `branch_create_delete` | 26.91ms | 40.00ms | 67.3% | 1.7% | PASS |
| `checkout_branch_clean` | 62.77ms | 150.00ms | 41.9% | 0.8% | PASS |
| `merge_data_no_conflicts` | 30.23ms | 50.00ms | 60.5% | 1.5% | PASS |
| `merge_schema_no_conflicts` | 22.55ms | 35.00ms | 64.4% | 1.9% | PASS |
| `merge_data_conflicts` | 32.81ms | 180.00ms | 18.2% | 2.1% | PASS |
| `merge_data_conflicts_with_resolve` | 32.34ms | 180.00ms | 18.0% | 1.2% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
