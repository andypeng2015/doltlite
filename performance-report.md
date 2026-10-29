# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-10-29 17:53 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261026.180.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/40910631949)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.30s | 10.14s | 1.1× | 1.7% | **PASS** |
| Writes | 1.97s | 3.42s | 1.7× | 1.6% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.43s | 10.54s | 1.0× | 1.6% | **PASS** |
| Writes | 3.32s | 4.17s | 1.3× | 1.8% | **FAIL** |
| Autocommit writes | 750.11ms | 2.23s | 3.0× | 5.3% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.44s | 2.68s | 1.1× | 1.6% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.76s | 1.80s | 1.0× | 2.9% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.59s | 2.81s | 1.1× | 2.2% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.51s | 2.86s | 1.1× | 1.1% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 432.00ms | 757.41ms | 1.8× | 1.5% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 372.74ms | 635.57ms | 1.7× | 2.6% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 577.92ms | 1.03s | 1.8× | 1.4% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 585.81ms | 995.59ms | 1.7× | 1.4% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 2.66s | 2.72s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.17s | 2.03s | 0.9× | 2.5% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.76s | 2.85s | 1.0× | 1.7% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.84s | 2.94s | 1.0× | 1.5% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 592.49ms | 824.01ms | 1.4× | 1.4% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.13s | 1.11s | 1.0× | 4.9% | **FAIL** |
| File-backed | Writes | blobpk | 8 | 55 | 834.60ms | 1.15s | 1.4× | 2.0% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 765.68ms | 1.09s | 1.4× | 1.5% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.60s | 2.76s | 1.1× | 1.6% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.81s | 1.85s | 1.0× | 2.1% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.60s | 2.85s | 1.1× | 2.0% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.65s | 2.94s | 1.1× | 1.5% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 185.25ms | 578.23ms | 3.1× | 5.5% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 181.07ms | 482.45ms | 2.7× | 5.6% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 196.91ms | 588.43ms | 3.0× | 5.2% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 186.88ms | 577.64ms | 3.1× | 5.5% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.64ms | 29.90ms | 1.2× | 2.2% | PASS |
| mem_reads | `oltp_range_select` | 11.18ms | 11.74ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 9.54ms | 11.33ms | 1.2× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 2.71ms | 2.97ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.66ms | 4.05ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.89ms | 5.36ms | 1.4× | 1.6% | PASS |
| mem_reads | `select_random_points` | 10.06ms | 10.85ms | 1.1× | 1.7% | PASS |
| mem_reads | `select_random_ranges` | 3.04ms | 3.98ms | 1.3× | 1.7% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.32ms | 1.0× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 30.47ms | 33.76ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 5.75ms | 8.08ms | 1.4× | 2.2% | PASS |
| mem_reads | `index_join_scan` | 3.24ms | 4.41ms | 1.4× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.16s | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.18s | 1.26s | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 104.29ms | 120.99ms | 1.2× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 175.05ms | 276.22ms | 1.6× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 14.88ms | 28.50ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 49.18ms | 101.51ms | 2.1× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 34.39ms | 64.17ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 43.98ms | 77.86ms | 1.8× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 21.19ms | 49.67ms | 2.3× | 1.8% | FAIL |
| mem_writes | `types_delete_insert` | 24.24ms | 41.17ms | 1.7× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 69.09ms | 118.31ms | 1.7× | 2.6% | PASS |
| file_reads | `oltp_point_select` | 92.02ms | 46.95ms | 0.5× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 18.21ms | 13.53ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 16.96ms | 13.19ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 3.60ms | 3.21ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 4.54ms | 4.29ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 11.14ms | 7.18ms | 0.6× | 1.4% | PASS |
| file_reads | `select_random_points` | 17.64ms | 12.75ms | 0.7× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 10.01ms | 5.72ms | 0.6× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 11.44ms | 6.17ms | 0.5× | 1.4% | PASS |
| file_reads | `groupby_scan` | 31.50ms | 34.09ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 9.91ms | 9.25ms | 0.9× | 2.3% | PASS |
| file_reads | `index_join_scan` | 4.20ms | 4.71ms | 1.1× | 1.7% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.16s | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.18s | 1.26s | 1.1× | 1.0% | PASS |
| file_reads | `oltp_read_only` | 206.14ms | 147.57ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 189.67ms | 286.51ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 21.73ms | 31.94ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 77.39ms | 114.48ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_update_non_index` | 57.44ms | 75.27ms | 1.3× | 1.5% | PASS |
| file_writes | `oltp_delete_insert` | 66.96ms | 89.09ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 44.49ms | 57.59ms | 1.3× | 1.3% | PASS |
| file_writes | `types_delete_insert` | 39.97ms | 43.76ms | 1.1× | 1.4% | PASS |
| file_writes | `oltp_read_write` | 94.83ms | 125.37ms | 1.3× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 46.53ms | 47.14ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 13.65ms | 13.51ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_sum_range` | 12.32ms | 13.19ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.14ms | 3.22ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.08ms | 4.33ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 6.46ms | 7.27ms | 1.1× | 1.2% | PASS |
| ac_reads | `select_random_points` | 12.63ms | 12.77ms | 1.0× | 2.3% | PASS |
| ac_reads | `select_random_ranges` | 5.45ms | 5.71ms | 1.0× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 6.76ms | 6.24ms | 0.9× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 30.92ms | 34.14ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 7.44ms | 9.53ms | 1.3× | 1.9% | PASS |
| ac_reads | `index_join_scan` | 3.70ms | 4.80ms | 1.3× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 1.08s | 1.18s | 1.1× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.22s | 1.27s | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 140.53ms | 148.23ms | 1.1× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.35ms | 57.75ms | 2.8× | 4.2% | PASS |
| ac_writes | `oltp_insert_ac` | 23.18ms | 72.96ms | 3.1× | 5.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.94ms | 83.05ms | 3.2× | 5.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.11ms | 65.65ms | 3.1× | 7.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.68ms | 75.26ms | 3.2× | 5.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.79ms | 73.69ms | 3.2× | 5.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.06ms | 68.16ms | 3.4× | 6.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.15ms | 81.70ms | 2.9× | 3.8% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.49ms | 25.29ms | 0.9× | 3.8% | PASS |
| mem_reads | `oltp_range_select` | 12.45ms | 9.42ms | 0.8× | 3.6% | PASS |
| mem_reads | `oltp_sum_range` | 11.92ms | 9.55ms | 0.8× | 1.8% | PASS |
| mem_reads | `oltp_order_range` | 2.46ms | 2.17ms | 0.9× | 2.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.05ms | 2.67ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 2.81ms | 4.75ms | 1.7× | 3.1% | PASS |
| mem_reads | `select_random_points` | 19.63ms | 15.84ms | 0.8× | 3.8% | PASS |
| mem_reads | `select_random_ranges` | 2.56ms | 3.62ms | 1.4× | 2.7% | PASS |
| mem_reads | `covering_index_scan` | 2.37ms | 3.08ms | 1.3× | 5.1% | PASS |
| mem_reads | `groupby_scan` | 22.53ms | 21.08ms | 0.9× | 1.9% | PASS |
| mem_reads | `index_join` | 8.12ms | 5.44ms | 0.7× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 2.83ms | 4.60ms | 1.6× | 3.4% | PASS |
| mem_reads | `types_table_scan` | 709.94ms | 742.87ms | 1.0× | 1.9% | PASS |
| mem_reads | `table_scan` | 845.22ms | 867.14ms | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_read_only` | 90.67ms | 78.08ms | 0.9× | 3.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 138.27ms | 216.26ms | 1.6× | 1.5% | PASS |
| mem_writes | `oltp_insert` | 10.62ms | 24.80ms | 2.3× | 1.4% | FAIL |
| mem_writes | `oltp_update_index` | 44.87ms | 98.22ms | 2.2× | 2.6% | PASS |
| mem_writes | `oltp_update_non_index` | 32.16ms | 55.82ms | 1.7× | 2.6% | PASS |
| mem_writes | `oltp_delete_insert` | 34.16ms | 66.94ms | 2.0× | 1.7% | PASS |
| mem_writes | `oltp_write_only` | 19.65ms | 43.62ms | 2.2× | 2.7% | PASS |
| mem_writes | `types_delete_insert` | 26.05ms | 36.70ms | 1.4× | 3.7% | PASS |
| mem_writes | `oltp_read_write` | 66.97ms | 93.20ms | 1.4× | 5.0% | PASS |
| file_reads | `oltp_point_select` | 80.91ms | 37.98ms | 0.5× | 2.5% | PASS |
| file_reads | `oltp_range_select` | 17.43ms | 10.42ms | 0.6× | 3.2% | PASS |
| file_reads | `oltp_sum_range` | 17.14ms | 10.41ms | 0.6× | 2.8% | PASS |
| file_reads | `oltp_order_range` | 3.16ms | 2.38ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_distinct_range` | 3.73ms | 2.86ms | 0.8× | 2.3% | PASS |
| file_reads | `oltp_index_scan` | 8.61ms | 6.04ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 22.52ms | 15.15ms | 0.7× | 3.9% | PASS |
| file_reads | `select_random_ranges` | 7.78ms | 4.96ms | 0.6× | 1.9% | PASS |
| file_reads | `covering_index_scan` | 8.31ms | 4.80ms | 0.6× | 1.4% | PASS |
| file_reads | `groupby_scan` | 23.16ms | 21.18ms | 0.9× | 2.0% | PASS |
| file_reads | `index_join` | 11.15ms | 7.02ms | 0.6× | 6.2% | PASS |
| file_reads | `index_join_scan` | 3.50ms | 4.65ms | 1.3× | 4.7% | PASS |
| file_reads | `types_table_scan` | 860.83ms | 870.35ms | 1.0× | 1.4% | PASS |
| file_reads | `table_scan` | 921.74ms | 926.76ms | 1.0× | 4.2% | PASS |
| file_reads | `oltp_read_only` | 182.40ms | 107.28ms | 0.6× | 3.5% | PASS |
| file_writes | `oltp_bulk_insert` | 219.34ms | 294.05ms | 1.3× | 3.6% | PASS |
| file_writes | `oltp_insert` | 20.26ms | 48.51ms | 2.4× | 5.9% | FAIL |
| file_writes | `oltp_update_index` | 168.18ms | 189.47ms | 1.1× | 7.3% | PASS |
| file_writes | `oltp_update_non_index` | 141.26ms | 119.36ms | 0.8× | 4.8% | PASS |
| file_writes | `oltp_delete_insert` | 172.65ms | 147.81ms | 0.9× | 4.5% | PASS |
| file_writes | `oltp_write_only` | 114.83ms | 92.38ms | 0.8× | 3.1% | PASS |
| file_writes | `types_delete_insert` | 132.53ms | 78.39ms | 0.6× | 9.2% | PASS |
| file_writes | `oltp_read_write` | 161.84ms | 142.19ms | 0.9× | 4.9% | PASS |
| ac_reads | `oltp_point_select` | 40.38ms | 36.23ms | 0.9× | 3.3% | PASS |
| ac_reads | `oltp_range_select` | 11.88ms | 9.61ms | 0.8× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 11.49ms | 9.46ms | 0.8× | 2.1% | PASS |
| ac_reads | `oltp_order_range` | 2.43ms | 2.12ms | 0.9× | 2.5% | PASS |
| ac_reads | `oltp_distinct_range` | 3.13ms | 2.73ms | 0.9× | 3.1% | PASS |
| ac_reads | `oltp_index_scan` | 4.81ms | 5.73ms | 1.2× | 1.7% | PASS |
| ac_reads | `select_random_points` | 18.20ms | 14.99ms | 0.8× | 2.5% | PASS |
| ac_reads | `select_random_ranges` | 4.43ms | 5.01ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 4.68ms | 4.74ms | 1.0× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 22.82ms | 21.01ms | 0.9× | 2.9% | PASS |
| ac_reads | `index_join` | 9.12ms | 6.61ms | 0.7× | 3.5% | PASS |
| ac_reads | `index_join_scan` | 2.99ms | 4.33ms | 1.4× | 1.8% | PASS |
| ac_reads | `types_table_scan` | 711.59ms | 761.64ms | 1.1× | 2.1% | PASS |
| ac_reads | `table_scan` | 859.64ms | 874.92ms | 1.0× | 3.1% | PASS |
| ac_reads | `oltp_read_only` | 102.69ms | 93.45ms | 0.9× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.51ms | 51.77ms | 2.4× | 4.9% | PASS |
| ac_writes | `oltp_insert_ac` | 23.54ms | 58.90ms | 2.5× | 7.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 23.59ms | 69.37ms | 2.9× | 5.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.28ms | 55.45ms | 2.7× | 4.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.78ms | 62.87ms | 2.8× | 7.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 21.69ms | 58.83ms | 2.7× | 3.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.41ms | 58.90ms | 2.8× | 8.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 26.28ms | 66.37ms | 2.5× | 5.8% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.11ms | 38.01ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 15.52ms | 14.25ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 14.18ms | 13.59ms | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_order_range` | 3.24ms | 3.25ms | 1.0× | 2.0% | PASS |
| mem_reads | `oltp_distinct_range` | 4.20ms | 4.40ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 3.85ms | 5.98ms | 1.6× | 2.3% | PASS |
| mem_reads | `select_random_points` | 20.02ms | 20.85ms | 1.0× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 3.29ms | 5.35ms | 1.6× | 2.8% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.67ms | 1.1× | 3.0% | PASS |
| mem_reads | `groupby_scan` | 35.01ms | 34.89ms | 1.0× | 1.2% | PASS |
| mem_reads | `index_join` | 9.87ms | 8.88ms | 0.9× | 1.9% | PASS |
| mem_reads | `index_join_scan` | 3.69ms | 5.48ms | 1.5× | 2.9% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.20s | 1.1× | 1.7% | PASS |
| mem_reads | `table_scan` | 1.23s | 1.31s | 1.1× | 2.5% | PASS |
| mem_reads | `oltp_read_only` | 133.62ms | 138.82ms | 1.0× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 238.50ms | 375.53ms | 1.6× | 0.6% | PASS |
| mem_writes | `oltp_insert` | 18.35ms | 40.89ms | 2.2× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 65.61ms | 149.45ms | 2.3× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 46.02ms | 88.04ms | 1.9× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 50.93ms | 105.80ms | 2.1× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 26.08ms | 63.59ms | 2.4× | 1.0% | FAIL |
| mem_writes | `types_delete_insert` | 36.53ms | 55.84ms | 1.5× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 95.91ms | 148.47ms | 1.5× | 2.2% | PASS |
| file_reads | `oltp_point_select` | 103.68ms | 56.00ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 25.45ms | 16.85ms | 0.7× | 2.8% | PASS |
| file_reads | `oltp_sum_range` | 23.48ms | 16.13ms | 0.7× | 2.5% | PASS |
| file_reads | `oltp_order_range` | 4.66ms | 3.84ms | 0.8× | 2.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.59ms | 4.92ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_index_scan` | 11.38ms | 8.20ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 29.76ms | 23.87ms | 0.8× | 2.4% | PASS |
| file_reads | `select_random_ranges` | 10.38ms | 7.16ms | 0.7× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 11.65ms | 6.59ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 36.56ms | 35.91ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 14.94ms | 11.06ms | 0.7× | 3.1% | PASS |
| file_reads | `index_join_scan` | 4.85ms | 5.92ms | 1.2× | 3.5% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.19s | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 1.18s | 1.29s | 1.1× | 1.4% | PASS |
| file_reads | `oltp_read_only` | 247.78ms | 168.71ms | 0.7× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 257.40ms | 388.00ms | 1.5× | 0.9% | PASS |
| file_writes | `oltp_insert` | 25.52ms | 47.54ms | 1.9× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 97.60ms | 162.55ms | 1.7× | 1.8% | PASS |
| file_writes | `oltp_update_non_index` | 91.39ms | 104.85ms | 1.1× | 11.7% | PASS |
| file_writes | `oltp_delete_insert` | 90.12ms | 124.48ms | 1.4× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 60.38ms | 79.22ms | 1.3× | 2.5% | PASS |
| file_writes | `types_delete_insert` | 64.83ms | 66.70ms | 1.0× | 2.2% | PASS |
| file_writes | `oltp_read_write` | 147.36ms | 172.54ms | 1.2× | 2.9% | PASS |
| ac_reads | `oltp_point_select` | 61.69ms | 57.14ms | 0.9× | 3.4% | PASS |
| ac_reads | `oltp_range_select` | 20.10ms | 16.50ms | 0.8× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 18.83ms | 16.09ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_order_range` | 4.10ms | 3.81ms | 0.9× | 2.4% | PASS |
| ac_reads | `oltp_distinct_range` | 5.15ms | 4.97ms | 1.0× | 3.2% | PASS |
| ac_reads | `oltp_index_scan` | 6.38ms | 8.19ms | 1.3× | 2.0% | PASS |
| ac_reads | `select_random_points` | 24.26ms | 24.01ms | 1.0× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 5.61ms | 7.13ms | 1.3× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 6.90ms | 6.68ms | 1.0× | 2.5% | PASS |
| ac_reads | `groupby_scan` | 35.91ms | 35.95ms | 1.0× | 1.1% | PASS |
| ac_reads | `index_join` | 12.02ms | 11.29ms | 0.9× | 3.1% | PASS |
| ac_reads | `index_join_scan` | 4.35ms | 5.96ms | 1.4× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.19s | 1.1× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.17s | 1.29s | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 177.99ms | 169.87ms | 1.0× | 2.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.68ms | 58.70ms | 2.7× | 5.1% | PASS |
| ac_writes | `oltp_insert_ac` | 24.36ms | 72.81ms | 3.0× | 4.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.03ms | 85.19ms | 3.4× | 4.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.50ms | 67.33ms | 3.0× | 6.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.48ms | 78.43ms | 3.1× | 5.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.98ms | 75.64ms | 3.0× | 5.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.01ms | 69.57ms | 3.2× | 7.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 30.87ms | 80.76ms | 2.6× | 3.2% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 31.34ms | 39.99ms | 1.3× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 18.44ms | 21.16ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_sum_range` | 16.94ms | 20.50ms | 1.2× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 3.47ms | 3.90ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.46ms | 5.05ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 4.15ms | 5.75ms | 1.4× | 1.5% | PASS |
| mem_reads | `select_random_points` | 24.19ms | 30.32ms | 1.3× | 1.0% | PASS |
| mem_reads | `select_random_ranges` | 7.11ms | 8.77ms | 1.2× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.18ms | 4.17ms | 1.0× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 36.53ms | 40.21ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 7.78ms | 10.20ms | 1.3× | 1.2% | PASS |
| mem_reads | `index_join_scan` | 3.64ms | 5.55ms | 1.5× | 2.5% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.20s | 1.2× | 0.4% | PASS |
| mem_reads | `table_scan` | 1.16s | 1.29s | 1.1× | 0.3% | PASS |
| mem_reads | `oltp_read_only` | 147.34ms | 172.07ms | 1.2× | 0.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 242.69ms | 354.21ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.54ms | 35.86ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 63.63ms | 128.07ms | 2.0× | 0.9% | PASS |
| mem_writes | `oltp_update_non_index` | 50.55ms | 88.37ms | 1.7× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 50.10ms | 102.60ms | 2.0× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 27.20ms | 63.67ms | 2.3× | 1.6% | FAIL |
| mem_writes | `types_delete_insert` | 31.86ms | 55.44ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 101.24ms | 167.36ms | 1.7× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 101.18ms | 58.14ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_range_select` | 27.01ms | 23.23ms | 0.9× | 2.5% | PASS |
| file_reads | `oltp_sum_range` | 24.74ms | 22.70ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 4.42ms | 4.20ms | 1.0× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 5.48ms | 5.35ms | 1.0× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 11.29ms | 7.99ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 34.00ms | 34.15ms | 1.0× | 2.3% | PASS |
| file_reads | `select_random_ranges` | 14.54ms | 10.86ms | 0.7× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 11.10ms | 6.18ms | 0.6× | 2.2% | PASS |
| file_reads | `groupby_scan` | 37.83ms | 40.65ms | 1.1× | 1.3% | PASS |
| file_reads | `index_join` | 11.86ms | 11.94ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join_scan` | 4.75ms | 5.96ms | 1.3× | 2.5% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.20s | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 1.25s | 1.30s | 1.0× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 259.87ms | 201.41ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 257.31ms | 370.45ms | 1.4× | 1.2% | PASS |
| file_writes | `oltp_insert` | 25.53ms | 42.62ms | 1.7× | 1.0% | PASS |
| file_writes | `oltp_update_index` | 97.03ms | 148.25ms | 1.5× | 1.9% | PASS |
| file_writes | `oltp_update_non_index` | 77.43ms | 102.64ms | 1.3× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 76.66ms | 113.47ms | 1.5× | 1.3% | PASS |
| file_writes | `oltp_write_only` | 51.23ms | 73.17ms | 1.4× | 1.6% | PASS |
| file_writes | `types_delete_insert` | 49.38ms | 63.03ms | 1.3× | 2.0% | PASS |
| file_writes | `oltp_read_write` | 131.10ms | 178.72ms | 1.4× | 1.5% | PASS |
| ac_reads | `oltp_point_select` | 56.60ms | 58.23ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 23.34ms | 23.30ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 20.75ms | 22.75ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.96ms | 4.20ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.95ms | 5.35ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_index_scan` | 6.79ms | 7.96ms | 1.2× | 2.3% | PASS |
| ac_reads | `select_random_points` | 28.29ms | 33.93ms | 1.2× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 9.88ms | 10.95ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 6.50ms | 6.27ms | 1.0× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 37.34ms | 40.67ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 9.32ms | 12.09ms | 1.3× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 4.34ms | 5.92ms | 1.4× | 2.0% | PASS |
| ac_reads | `types_table_scan` | 1.07s | 1.21s | 1.1× | 1.5% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.29s | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 185.37ms | 199.86ms | 1.1× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.48ms | 56.74ms | 2.6× | 5.3% | PASS |
| ac_writes | `oltp_insert_ac` | 23.82ms | 73.04ms | 3.1× | 5.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.91ms | 84.52ms | 3.4× | 5.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.87ms | 66.75ms | 3.2× | 6.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 21.77ms | 75.68ms | 3.5× | 5.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.52ms | 74.09ms | 3.1× | 5.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.83ms | 64.65ms | 3.1× | 7.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.67ms | 82.18ms | 2.8× | 3.7% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 5s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 92.48ms | 130.00ms | 71.1% | 0.3% | PASS |
| `status_dirty_many_tables` | 95.89ms | 130.00ms | 73.8% | 0.3% | PASS |
| `diff_regular_working_one_table` | 87.35ms | 120.00ms | 72.8% | 0.3% | PASS |
| `diff_regular_working_many_tables` | 100.12ms | 140.00ms | 71.5% | 0.5% | PASS |
| `diff_stat_working_many_tables` | 100.75ms | 140.00ms | 72.0% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 101.34ms | 140.00ms | 72.4% | 0.3% | PASS |
| `branch_list_many_branches` | 20.14ms | 35.00ms | 57.6% | 1.7% | PASS |
| `branch_create_delete` | 22.68ms | 40.00ms | 56.7% | 0.9% | PASS |
| `checkout_branch_clean` | 57.91ms | 150.00ms | 38.6% | 0.7% | PASS |
| `merge_data_no_conflicts` | 27.04ms | 50.00ms | 54.1% | 1.3% | PASS |
| `merge_schema_no_conflicts` | 19.75ms | 35.00ms | 56.4% | 1.4% | PASS |
| `merge_data_conflicts` | 30.36ms | 180.00ms | 16.9% | 1.0% | PASS |
| `merge_data_conflicts_with_resolve` | 29.97ms | 180.00ms | 16.6% | 1.4% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
