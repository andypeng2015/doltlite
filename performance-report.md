# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-10-31 17:01 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261026.180.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/41214941003)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.68s | 10.22s | 1.1× | 1.5% | **PASS** |
| Writes | 2.04s | 3.50s | 1.7× | 1.2% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.43s | 10.47s | 1.0× | 1.6% | **PASS** |
| Writes | 3.28s | 4.11s | 1.3× | 1.9% | **FAIL** |
| Autocommit writes | 770.60ms | 2.30s | 3.0× | 7.1% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.52s | 2.69s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.78s | 1.78s | 1.0× | 3.1% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.62s | 2.83s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.77s | 2.92s | 1.1× | 1.6% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 441.31ms | 763.32ms | 1.7× | 1.1% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 418.36ms | 699.61ms | 1.7× | 1.9% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 596.41ms | 1.04s | 1.7× | 1.3% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 582.74ms | 997.58ms | 1.7× | 1.2% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 2.82s | 2.77s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.02s | 1.89s | 0.9× | 2.0% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.80s | 2.86s | 1.0× | 1.3% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.80s | 2.94s | 1.1× | 1.5% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 597.64ms | 830.66ms | 1.4× | 1.7% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.11s | 1.07s | 1.0× | 5.0% | **FAIL** |
| File-backed | Writes | blobpk | 8 | 55 | 822.91ms | 1.13s | 1.4× | 1.9% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 755.41ms | 1.08s | 1.4× | 1.4% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.67s | 2.76s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.89s | 1.86s | 1.0× | 2.1% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.63s | 2.86s | 1.1× | 1.5% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.81s | 2.97s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 191.29ms | 611.07ms | 3.2× | 7.1% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 184.90ms | 486.12ms | 2.6× | 6.3% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 206.90ms | 612.41ms | 3.0× | 7.8% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 187.50ms | 588.24ms | 3.1× | 7.1% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.70ms | 29.63ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 11.80ms | 11.71ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_sum_range` | 9.89ms | 11.24ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 2.74ms | 3.00ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.69ms | 4.12ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.89ms | 5.28ms | 1.4× | 1.6% | PASS |
| mem_reads | `select_random_points` | 10.93ms | 10.94ms | 1.0× | 2.1% | PASS |
| mem_reads | `select_random_ranges` | 3.10ms | 3.95ms | 1.3× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.13ms | 4.33ms | 1.0× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 30.74ms | 34.33ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 5.84ms | 8.11ms | 1.4× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 3.43ms | 4.50ms | 1.3× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.17s | 1.1× | 1.5% | PASS |
| mem_reads | `table_scan` | 1.23s | 1.27s | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 110.13ms | 123.27ms | 1.1× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 171.04ms | 274.46ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 14.78ms | 28.32ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 51.26ms | 102.35ms | 2.0× | 0.9% | PASS |
| mem_writes | `oltp_update_non_index` | 35.75ms | 64.22ms | 1.8× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 45.26ms | 79.19ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 22.27ms | 51.29ms | 2.3× | 1.1% | FAIL |
| mem_writes | `types_delete_insert` | 25.42ms | 42.10ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 75.53ms | 121.38ms | 1.6× | 1.8% | PASS |
| file_reads | `oltp_point_select` | 93.02ms | 47.37ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 19.42ms | 13.84ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 17.61ms | 13.59ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 3.75ms | 3.39ms | 0.9× | 2.7% | PASS |
| file_reads | `oltp_distinct_range` | 4.81ms | 4.52ms | 0.9× | 2.8% | PASS |
| file_reads | `oltp_index_scan` | 11.05ms | 7.36ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 18.55ms | 12.99ms | 0.7× | 2.4% | PASS |
| file_reads | `select_random_ranges` | 10.04ms | 5.72ms | 0.6× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 11.35ms | 6.28ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 31.92ms | 34.98ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 10.14ms | 9.71ms | 1.0× | 2.1% | PASS |
| file_reads | `index_join_scan` | 4.42ms | 4.97ms | 1.1× | 2.6% | PASS |
| file_reads | `types_table_scan` | 1.11s | 1.18s | 1.1× | 2.0% | PASS |
| file_reads | `table_scan` | 1.27s | 1.28s | 1.0× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 209.40ms | 147.95ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 184.35ms | 281.23ms | 1.5× | 0.8% | PASS |
| file_writes | `oltp_insert` | 21.79ms | 31.82ms | 1.5× | 2.3% | PASS |
| file_writes | `oltp_update_index` | 81.44ms | 119.67ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 60.66ms | 77.54ms | 1.3× | 1.4% | PASS |
| file_writes | `oltp_delete_insert` | 67.91ms | 90.89ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 45.01ms | 58.81ms | 1.3× | 1.8% | PASS |
| file_writes | `types_delete_insert` | 40.38ms | 44.50ms | 1.1× | 2.6% | PASS |
| file_writes | `oltp_read_write` | 96.11ms | 126.19ms | 1.3× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 46.59ms | 46.47ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 14.33ms | 13.53ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_sum_range` | 12.59ms | 13.12ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 3.15ms | 3.27ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.14ms | 4.43ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 6.50ms | 7.28ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_points` | 13.69ms | 12.82ms | 0.9× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 5.44ms | 5.70ms | 1.0× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 6.85ms | 6.26ms | 0.9× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 31.15ms | 34.65ms | 1.1× | 0.6% | PASS |
| ac_reads | `index_join` | 7.54ms | 9.55ms | 1.3× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 3.83ms | 4.84ms | 1.3× | 2.0% | PASS |
| ac_reads | `types_table_scan` | 1.09s | 1.17s | 1.1× | 2.0% | PASS |
| ac_reads | `table_scan` | 1.28s | 1.28s | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 146.35ms | 152.01ms | 1.0× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.90ms | 60.82ms | 2.9× | 6.3% | PASS |
| ac_writes | `oltp_insert_ac` | 23.11ms | 74.89ms | 3.2× | 5.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.70ms | 90.03ms | 3.4× | 7.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.06ms | 69.89ms | 3.2× | 8.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.93ms | 79.30ms | 3.3× | 6.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.19ms | 79.25ms | 3.3× | 7.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.76ms | 70.61ms | 3.4× | 9.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.63ms | 86.29ms | 2.9× | 7.6% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.14ms | 23.03ms | 1.0× | 2.7% | PASS |
| mem_reads | `oltp_range_select` | 10.85ms | 8.59ms | 0.8× | 3.5% | PASS |
| mem_reads | `oltp_sum_range` | 11.11ms | 8.94ms | 0.8× | 3.1% | PASS |
| mem_reads | `oltp_order_range` | 2.35ms | 2.08ms | 0.9× | 2.6% | PASS |
| mem_reads | `oltp_distinct_range` | 2.82ms | 2.51ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 2.45ms | 3.79ms | 1.5× | 3.7% | PASS |
| mem_reads | `select_random_points` | 15.11ms | 13.52ms | 0.9× | 5.7% | PASS |
| mem_reads | `select_random_ranges` | 2.33ms | 3.31ms | 1.4× | 4.3% | PASS |
| mem_reads | `covering_index_scan` | 2.28ms | 2.83ms | 1.2× | 2.5% | PASS |
| mem_reads | `groupby_scan` | 21.74ms | 20.75ms | 1.0× | 1.3% | PASS |
| mem_reads | `index_join` | 7.97ms | 5.66ms | 0.7× | 4.1% | PASS |
| mem_reads | `index_join_scan` | 3.05ms | 5.15ms | 1.7× | 5.2% | PASS |
| mem_reads | `types_table_scan` | 685.98ms | 728.47ms | 1.1× | 1.4% | PASS |
| mem_reads | `table_scan` | 888.37ms | 867.32ms | 1.0× | 3.1% | PASS |
| mem_reads | `oltp_read_only` | 95.31ms | 81.89ms | 0.9× | 3.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 138.32ms | 222.39ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 10.59ms | 25.44ms | 2.4× | 1.9% | FAIL |
| mem_writes | `oltp_update_index` | 50.06ms | 107.07ms | 2.1× | 2.4% | PASS |
| mem_writes | `oltp_update_non_index` | 41.30ms | 65.60ms | 1.6× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 43.07ms | 81.99ms | 1.9× | 2.1% | PASS |
| mem_writes | `oltp_write_only` | 22.25ms | 49.03ms | 2.2× | 2.2% | PASS |
| mem_writes | `types_delete_insert` | 30.49ms | 41.75ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 82.28ms | 106.33ms | 1.3× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 84.88ms | 40.10ms | 0.5× | 1.7% | PASS |
| file_reads | `oltp_range_select` | 18.68ms | 11.12ms | 0.6× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 18.08ms | 11.17ms | 0.6× | 2.0% | PASS |
| file_reads | `oltp_order_range` | 2.99ms | 2.29ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 3.49ms | 2.76ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 8.21ms | 5.54ms | 0.7× | 3.5% | PASS |
| file_reads | `select_random_points` | 20.69ms | 14.39ms | 0.7× | 3.0% | PASS |
| file_reads | `select_random_ranges` | 7.40ms | 4.77ms | 0.6× | 1.8% | PASS |
| file_reads | `covering_index_scan` | 7.77ms | 4.24ms | 0.5× | 2.9% | PASS |
| file_reads | `groupby_scan` | 22.03ms | 20.35ms | 0.9× | 1.7% | PASS |
| file_reads | `index_join` | 11.69ms | 7.70ms | 0.7× | 3.2% | PASS |
| file_reads | `index_join_scan` | 3.58ms | 4.79ms | 1.3× | 2.0% | PASS |
| file_reads | `types_table_scan` | 832.25ms | 823.78ms | 1.0× | 1.1% | PASS |
| file_reads | `table_scan` | 817.96ms | 848.62ms | 1.0× | 4.1% | PASS |
| file_reads | `oltp_read_only` | 155.96ms | 93.19ms | 0.6× | 3.1% | PASS |
| file_writes | `oltp_bulk_insert` | 215.78ms | 285.82ms | 1.3× | 4.5% | PASS |
| file_writes | `oltp_insert` | 19.94ms | 47.83ms | 2.4× | 6.6% | FAIL |
| file_writes | `oltp_update_index` | 158.06ms | 170.33ms | 1.1× | 3.4% | PASS |
| file_writes | `oltp_update_non_index` | 132.08ms | 113.74ms | 0.9× | 3.6% | PASS |
| file_writes | `oltp_delete_insert` | 172.75ms | 140.28ms | 0.8× | 5.5% | PASS |
| file_writes | `oltp_write_only` | 114.07ms | 98.98ms | 0.9× | 2.9% | PASS |
| file_writes | `types_delete_insert` | 139.79ms | 70.53ms | 0.5× | 10.8% | PASS |
| file_writes | `oltp_read_write` | 153.72ms | 139.84ms | 0.9× | 6.9% | PASS |
| ac_reads | `oltp_point_select` | 40.23ms | 35.45ms | 0.9× | 3.4% | PASS |
| ac_reads | `oltp_range_select` | 12.96ms | 9.87ms | 0.8× | 4.7% | PASS |
| ac_reads | `oltp_sum_range` | 12.67ms | 10.08ms | 0.8× | 3.4% | PASS |
| ac_reads | `oltp_order_range` | 2.66ms | 2.25ms | 0.8× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 3.22ms | 2.74ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 4.73ms | 5.67ms | 1.2× | 1.3% | PASS |
| ac_reads | `select_random_points` | 19.03ms | 15.24ms | 0.8× | 4.2% | PASS |
| ac_reads | `select_random_ranges` | 4.40ms | 4.96ms | 1.1× | 1.8% | PASS |
| ac_reads | `covering_index_scan` | 4.79ms | 4.82ms | 1.0× | 2.0% | PASS |
| ac_reads | `groupby_scan` | 22.94ms | 21.42ms | 0.9× | 1.7% | PASS |
| ac_reads | `index_join` | 9.87ms | 7.70ms | 0.8× | 2.0% | PASS |
| ac_reads | `index_join_scan` | 3.10ms | 4.48ms | 1.4× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 723.98ms | 755.35ms | 1.0× | 2.6% | PASS |
| ac_reads | `table_scan` | 912.43ms | 882.43ms | 1.0× | 2.8% | PASS |
| ac_reads | `oltp_read_only` | 109.53ms | 94.04ms | 0.9× | 2.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.44ms | 50.57ms | 2.4× | 4.2% | PASS |
| ac_writes | `oltp_insert_ac` | 23.63ms | 60.24ms | 2.5× | 6.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.08ms | 68.05ms | 2.8× | 7.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.01ms | 59.06ms | 2.8× | 7.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.66ms | 60.48ms | 2.7× | 5.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.69ms | 60.43ms | 2.7× | 4.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.40ms | 58.74ms | 2.7× | 8.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.00ms | 68.56ms | 2.4× | 3.5% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.50ms | 38.67ms | 1.1× | 2.2% | PASS |
| mem_reads | `oltp_range_select` | 16.89ms | 14.07ms | 0.8× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 14.96ms | 13.60ms | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 3.34ms | 3.29ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.31ms | 4.46ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.95ms | 6.13ms | 1.6× | 1.4% | PASS |
| mem_reads | `select_random_points` | 22.50ms | 20.93ms | 0.9× | 2.9% | PASS |
| mem_reads | `select_random_ranges` | 3.45ms | 5.34ms | 1.5× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.21ms | 4.70ms | 1.1× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 35.01ms | 35.53ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 10.77ms | 9.72ms | 0.9× | 1.3% | PASS |
| mem_reads | `index_join_scan` | 3.91ms | 5.44ms | 1.4× | 2.0% | PASS |
| mem_reads | `types_table_scan` | 1.08s | 1.21s | 1.1× | 1.3% | PASS |
| mem_reads | `table_scan` | 1.24s | 1.32s | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_read_only` | 144.18ms | 142.53ms | 1.0× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 238.80ms | 376.47ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 18.26ms | 40.54ms | 2.2× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 66.36ms | 148.60ms | 2.2× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 49.33ms | 89.51ms | 1.8× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 53.13ms | 108.80ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 28.31ms | 65.86ms | 2.3× | 1.7% | FAIL |
| mem_writes | `types_delete_insert` | 38.56ms | 57.74ms | 1.5× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 103.68ms | 152.41ms | 1.5× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 106.11ms | 55.80ms | 0.5× | 1.5% | PASS |
| file_reads | `oltp_range_select` | 24.50ms | 15.92ms | 0.6× | 2.3% | PASS |
| file_reads | `oltp_sum_range` | 23.03ms | 15.58ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 4.23ms | 3.53ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 5.27ms | 4.73ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 11.17ms | 8.16ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_points` | 30.24ms | 23.90ms | 0.8× | 2.3% | PASS |
| file_reads | `select_random_ranges` | 10.47ms | 7.09ms | 0.7× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 11.27ms | 6.57ms | 0.6× | 1.3% | PASS |
| file_reads | `groupby_scan` | 36.33ms | 35.89ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 14.62ms | 11.03ms | 0.8× | 2.4% | PASS |
| file_reads | `index_join_scan` | 4.87ms | 5.84ms | 1.2× | 1.7% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.20s | 1.1× | 1.0% | PASS |
| file_reads | `table_scan` | 1.20s | 1.30s | 1.1× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 247.84ms | 168.76ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 258.63ms | 388.19ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 25.21ms | 47.32ms | 1.9× | 1.2% | PASS |
| file_writes | `oltp_update_index` | 98.86ms | 162.37ms | 1.6× | 1.3% | PASS |
| file_writes | `oltp_update_non_index` | 93.71ms | 103.13ms | 1.1× | 10.3% | PASS |
| file_writes | `oltp_delete_insert` | 88.20ms | 122.35ms | 1.4× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 58.31ms | 77.59ms | 1.3× | 2.1% | PASS |
| file_writes | `types_delete_insert` | 64.78ms | 65.68ms | 1.0× | 2.1% | PASS |
| file_writes | `oltp_read_write` | 135.20ms | 162.70ms | 1.2× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 58.43ms | 56.05ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 19.84ms | 15.92ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 18.04ms | 15.58ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 3.71ms | 3.52ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_distinct_range` | 4.68ms | 4.71ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.58ms | 8.18ms | 1.2× | 1.8% | PASS |
| ac_reads | `select_random_points` | 25.23ms | 23.93ms | 0.9× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 5.80ms | 7.09ms | 1.2× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 6.67ms | 6.58ms | 1.0× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 35.56ms | 35.90ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 12.39ms | 10.98ms | 0.9× | 2.4% | PASS |
| ac_reads | `index_join_scan` | 4.28ms | 5.79ms | 1.4× | 2.2% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.20s | 1.1× | 1.0% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.30s | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 176.33ms | 167.84ms | 1.0× | 1.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.26ms | 62.11ms | 2.7× | 8.0% | PASS |
| ac_writes | `oltp_insert_ac` | 25.84ms | 77.80ms | 3.0× | 5.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.12ms | 87.75ms | 3.2× | 7.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.41ms | 69.04ms | 3.1× | 8.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.41ms | 79.82ms | 3.0× | 8.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.51ms | 78.02ms | 3.2× | 6.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.43ms | 71.99ms | 2.9× | 7.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.93ms | 85.87ms | 2.6× | 6.3% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.32ms | 40.53ms | 1.3× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 19.35ms | 21.30ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_sum_range` | 17.33ms | 20.61ms | 1.2× | 1.2% | PASS |
| mem_reads | `oltp_order_range` | 3.65ms | 3.97ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_distinct_range` | 4.71ms | 5.13ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.57ms | 6.21ms | 1.4× | 2.0% | PASS |
| mem_reads | `select_random_points` | 26.52ms | 31.13ms | 1.2× | 1.6% | PASS |
| mem_reads | `select_random_ranges` | 7.63ms | 9.02ms | 1.2× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.20ms | 4.53ms | 1.1× | 2.6% | PASS |
| mem_reads | `groupby_scan` | 37.15ms | 40.89ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 7.97ms | 10.80ms | 1.4× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 4.00ms | 5.56ms | 1.4× | 2.2% | PASS |
| mem_reads | `types_table_scan` | 1.11s | 1.22s | 1.1× | 2.3% | PASS |
| mem_reads | `table_scan` | 1.33s | 1.32s | 1.0× | 2.7% | PASS |
| mem_reads | `oltp_read_only` | 156.64ms | 176.64ms | 1.1× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 243.23ms | 361.36ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 18.61ms | 36.13ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 64.84ms | 129.79ms | 2.0× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 49.67ms | 87.95ms | 1.8× | 1.4% | PASS |
| mem_writes | `oltp_delete_insert` | 48.20ms | 99.68ms | 2.1× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 26.16ms | 61.66ms | 2.4× | 1.4% | FAIL |
| mem_writes | `types_delete_insert` | 31.85ms | 55.33ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 100.17ms | 165.68ms | 1.7× | 1.0% | PASS |
| file_reads | `oltp_point_select` | 100.97ms | 58.93ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 27.66ms | 23.35ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 25.05ms | 22.75ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.57ms | 4.27ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 5.62ms | 5.44ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 11.46ms | 8.13ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 33.99ms | 33.78ms | 1.0× | 1.4% | PASS |
| file_reads | `select_random_ranges` | 14.87ms | 10.98ms | 0.7× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 10.99ms | 6.34ms | 0.6× | 1.1% | PASS |
| file_reads | `groupby_scan` | 37.93ms | 41.06ms | 1.1× | 0.8% | PASS |
| file_reads | `index_join` | 11.95ms | 12.07ms | 1.0× | 1.4% | PASS |
| file_reads | `index_join_scan` | 4.84ms | 5.76ms | 1.2× | 2.0% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.21s | 1.2× | 1.1% | PASS |
| file_reads | `table_scan` | 1.20s | 1.30s | 1.1× | 2.5% | PASS |
| file_reads | `oltp_read_only` | 258.20ms | 204.45ms | 0.8× | 1.5% | PASS |
| file_writes | `oltp_bulk_insert` | 257.68ms | 369.62ms | 1.4× | 1.3% | PASS |
| file_writes | `oltp_insert` | 25.59ms | 42.48ms | 1.7× | 1.1% | PASS |
| file_writes | `oltp_update_index` | 94.51ms | 144.96ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 75.59ms | 101.42ms | 1.3× | 1.5% | PASS |
| file_writes | `oltp_delete_insert` | 75.72ms | 113.00ms | 1.5× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 50.96ms | 72.72ms | 1.4× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 48.89ms | 62.23ms | 1.3× | 2.2% | PASS |
| file_writes | `oltp_read_write` | 126.46ms | 176.76ms | 1.4× | 1.0% | PASS |
| ac_reads | `oltp_point_select` | 55.52ms | 59.05ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 22.45ms | 23.29ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_sum_range` | 19.98ms | 22.73ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 4.03ms | 4.22ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 5.04ms | 5.40ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_index_scan` | 6.97ms | 8.02ms | 1.2× | 1.4% | PASS |
| ac_reads | `select_random_points` | 28.55ms | 33.94ms | 1.2× | 1.8% | PASS |
| ac_reads | `select_random_ranges` | 10.15ms | 10.97ms | 1.1× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 6.66ms | 6.36ms | 1.0× | 1.8% | PASS |
| ac_reads | `groupby_scan` | 37.27ms | 41.01ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 9.60ms | 12.14ms | 1.3× | 1.5% | PASS |
| ac_reads | `index_join_scan` | 4.38ms | 5.78ms | 1.3× | 2.0% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.21s | 1.1× | 1.3% | PASS |
| ac_reads | `table_scan` | 1.34s | 1.32s | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_read_only` | 194.41ms | 207.27ms | 1.1× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.25ms | 56.63ms | 2.8× | 7.8% | PASS |
| ac_writes | `oltp_insert_ac` | 22.51ms | 74.85ms | 3.3× | 6.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.69ms | 87.18ms | 3.4× | 5.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.08ms | 66.64ms | 3.0× | 9.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.48ms | 74.90ms | 3.2× | 6.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.56ms | 76.44ms | 3.2× | 7.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.02ms | 67.58ms | 3.4× | 8.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.90ms | 84.02ms | 2.8× | 4.7% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 12s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 102.67ms | 130.00ms | 79.0% | 0.2% | PASS |
| `status_dirty_many_tables` | 106.42ms | 130.00ms | 81.9% | 0.2% | PASS |
| `diff_regular_working_one_table` | 97.75ms | 120.00ms | 81.5% | 0.2% | PASS |
| `diff_regular_working_many_tables` | 111.13ms | 140.00ms | 79.4% | 0.2% | PASS |
| `diff_stat_working_many_tables` | 111.46ms | 140.00ms | 79.6% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 112.19ms | 140.00ms | 80.1% | 0.3% | PASS |
| `branch_list_many_branches` | 20.87ms | 35.00ms | 59.6% | 1.5% | PASS |
| `branch_create_delete` | 22.72ms | 40.00ms | 56.8% | 1.2% | PASS |
| `checkout_branch_clean` | 61.29ms | 150.00ms | 40.9% | 0.4% | PASS |
| `merge_data_no_conflicts` | 27.26ms | 50.00ms | 54.5% | 1.4% | PASS |
| `merge_schema_no_conflicts` | 19.32ms | 35.00ms | 55.2% | 1.1% | PASS |
| `merge_data_conflicts` | 31.27ms | 180.00ms | 17.4% | 1.0% | PASS |
| `merge_data_conflicts_with_resolve` | 31.18ms | 180.00ms | 17.3% | 0.5% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
