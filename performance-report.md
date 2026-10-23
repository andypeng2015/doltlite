# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-10-23 15:53 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261012.163.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/39968776648)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.96s | 9.46s | 1.1× | 1.6% | **PASS** |
| Writes | 1.86s | 3.04s | 1.6× | 1.4% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.77s | 9.63s | 1.0× | 1.4% | **PASS** |
| Writes | 4.03s | 4.20s | 1.0× | 3.4% | **FAIL** |
| Autocommit writes | 920.14ms | 2.88s | 3.1× | 7.5% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.45s | 2.59s | 1.1× | 1.5% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.75s | 1.77s | 1.0× | 3.3% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.16s | 2.26s | 1.0× | 1.6% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.60s | 2.84s | 1.1× | 1.4% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 441.04ms | 735.16ms | 1.7× | 1.3% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 364.41ms | 601.07ms | 1.6× | 2.4% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 464.00ms | 735.19ms | 1.6× | 1.5% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 593.92ms | 965.61ms | 1.6× | 1.1% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.68s | 2.65s | 1.0× | 1.7% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 1.86s | 1.77s | 0.9× | 1.7% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.40s | 2.32s | 1.0× | 0.9% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.83s | 2.90s | 1.0× | 1.4% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 615.25ms | 811.92ms | 1.3× | 1.8% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.12s | 1.05s | 0.9× | 6.3% | **FAIL** |
| File-backed | Writes | blobpk | 8 | 55 | 1.51s | 1.29s | 0.9× | 31.7% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 785.77ms | 1.04s | 1.3× | 1.2% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.55s | 2.67s | 1.0× | 1.7% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.85s | 1.84s | 1.0× | 3.1% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.25s | 2.32s | 1.0× | 1.2% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.66s | 2.90s | 1.1× | 0.9% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 198.25ms | 630.32ms | 3.2× | 8.8% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 187.13ms | 503.38ms | 2.7× | 5.9% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 342.73ms | 1.16s | 3.4× | 59.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 192.02ms | 584.19ms | 3.0× | 6.6% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.45ms | 30.58ms | 1.3× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 10.51ms | 12.65ms | 1.2× | 2.5% | PASS |
| mem_reads | `oltp_sum_range` | 9.71ms | 11.32ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 2.64ms | 2.83ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.71ms | 4.01ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.00ms | 5.58ms | 1.4× | 1.6% | PASS |
| mem_reads | `select_random_points` | 10.03ms | 11.07ms | 1.1× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 3.03ms | 4.02ms | 1.3× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.21ms | 4.28ms | 1.0× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 30.65ms | 32.97ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 5.84ms | 8.04ms | 1.4× | 1.5% | PASS |
| mem_reads | `index_join_scan` | 3.17ms | 4.40ms | 1.4× | 2.2% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.12s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.17s | 1.22s | 1.0× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 105.54ms | 120.35ms | 1.1× | 1.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.01ms | 277.91ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 15.64ms | 28.97ms | 1.9× | 1.4% | PASS |
| mem_writes | `oltp_update_index` | 50.27ms | 95.95ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 33.84ms | 59.39ms | 1.8× | 1.3% | PASS |
| mem_writes | `oltp_delete_insert` | 44.38ms | 73.55ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 21.31ms | 46.40ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 24.24ms | 38.14ms | 1.6× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 67.36ms | 114.85ms | 1.7× | 1.3% | PASS |
| file_reads | `oltp_point_select` | 97.79ms | 49.76ms | 0.5× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 17.90ms | 14.52ms | 0.8× | 2.5% | PASS |
| file_reads | `oltp_sum_range` | 17.29ms | 13.33ms | 0.8× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 3.48ms | 3.10ms | 0.9× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.55ms | 4.25ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 11.71ms | 7.57ms | 0.6× | 1.7% | PASS |
| file_reads | `select_random_points` | 18.01ms | 13.13ms | 0.7× | 2.2% | PASS |
| file_reads | `select_random_ranges` | 10.51ms | 5.99ms | 0.6× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 11.91ms | 6.55ms | 0.6× | 1.9% | PASS |
| file_reads | `groupby_scan` | 31.65ms | 33.39ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 10.05ms | 9.44ms | 0.9× | 2.3% | PASS |
| file_reads | `index_join_scan` | 4.22ms | 4.76ms | 1.1× | 2.6% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.12s | 1.1× | 0.3% | PASS |
| file_reads | `table_scan` | 1.18s | 1.21s | 1.0× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 209.60ms | 147.93ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 196.53ms | 284.62ms | 1.4× | 0.8% | PASS |
| file_writes | `oltp_insert` | 22.20ms | 32.43ms | 1.5× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 77.90ms | 105.94ms | 1.4× | 2.2% | PASS |
| file_writes | `oltp_update_non_index` | 59.87ms | 73.05ms | 1.2× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 71.44ms | 86.73ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 46.69ms | 57.48ms | 1.2× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 42.47ms | 46.08ms | 1.1× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 98.14ms | 125.58ms | 1.3× | 2.4% | PASS |
| ac_reads | `oltp_point_select` | 50.39ms | 51.21ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 13.88ms | 14.88ms | 1.1× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 12.88ms | 13.59ms | 1.1× | 2.3% | PASS |
| ac_reads | `oltp_order_range` | 3.03ms | 3.13ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.06ms | 4.31ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.62ms | 7.57ms | 1.1× | 2.7% | PASS |
| ac_reads | `select_random_points` | 12.72ms | 13.16ms | 1.0× | 4.4% | PASS |
| ac_reads | `select_random_ranges` | 5.52ms | 5.98ms | 1.1× | 2.0% | PASS |
| ac_reads | `covering_index_scan` | 6.93ms | 6.40ms | 0.9× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 30.98ms | 33.33ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 7.44ms | 9.50ms | 1.3× | 2.1% | PASS |
| ac_reads | `index_join_scan` | 3.61ms | 4.75ms | 1.3× | 1.6% | PASS |
| ac_reads | `types_table_scan` | 1.07s | 1.13s | 1.1× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.22s | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_read_only` | 139.77ms | 149.54ms | 1.1× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.39ms | 64.85ms | 2.9× | 8.3% | PASS |
| ac_writes | `oltp_insert_ac` | 24.76ms | 75.73ms | 3.1× | 9.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.25ms | 87.72ms | 3.3× | 5.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.99ms | 70.91ms | 3.0× | 11.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.59ms | 90.67ms | 3.8× | 7.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.85ms | 80.15ms | 3.2× | 9.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.69ms | 70.34ms | 3.2× | 9.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 30.74ms | 89.95ms | 2.9× | 6.3% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.93ms | 22.84ms | 1.0× | 3.0% | PASS |
| mem_reads | `oltp_range_select` | 10.86ms | 8.62ms | 0.8× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 10.19ms | 8.64ms | 0.8× | 3.5% | PASS |
| mem_reads | `oltp_order_range` | 2.17ms | 1.94ms | 0.9× | 4.0% | PASS |
| mem_reads | `oltp_distinct_range` | 2.60ms | 2.45ms | 0.9× | 3.7% | PASS |
| mem_reads | `oltp_index_scan` | 2.51ms | 3.80ms | 1.5× | 3.0% | PASS |
| mem_reads | `select_random_points` | 14.28ms | 13.21ms | 0.9× | 5.4% | PASS |
| mem_reads | `select_random_ranges` | 2.29ms | 3.35ms | 1.5× | 3.3% | PASS |
| mem_reads | `covering_index_scan` | 2.32ms | 2.66ms | 1.1× | 2.3% | PASS |
| mem_reads | `groupby_scan` | 19.93ms | 19.07ms | 1.0× | 1.5% | PASS |
| mem_reads | `index_join` | 7.83ms | 5.43ms | 0.7× | 4.0% | PASS |
| mem_reads | `index_join_scan` | 2.39ms | 4.33ms | 1.8× | 7.7% | PASS |
| mem_reads | `types_table_scan` | 700.59ms | 734.52ms | 1.0× | 2.2% | PASS |
| mem_reads | `table_scan` | 856.11ms | 859.84ms | 1.0× | 3.2% | PASS |
| mem_reads | `oltp_read_only` | 88.02ms | 76.76ms | 0.9× | 5.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 138.46ms | 207.95ms | 1.5× | 1.4% | PASS |
| mem_writes | `oltp_insert` | 10.51ms | 22.67ms | 2.2× | 2.0% | PASS |
| mem_writes | `oltp_update_index` | 47.92ms | 98.17ms | 2.0× | 2.8% | PASS |
| mem_writes | `oltp_update_non_index` | 33.47ms | 52.98ms | 1.6× | 2.8% | PASS |
| mem_writes | `oltp_delete_insert` | 35.44ms | 65.82ms | 1.9× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 19.28ms | 39.71ms | 2.1× | 1.7% | PASS |
| mem_writes | `types_delete_insert` | 25.81ms | 33.54ms | 1.3× | 3.0% | PASS |
| mem_writes | `oltp_read_write` | 53.52ms | 80.24ms | 1.5× | 3.3% | PASS |
| file_reads | `oltp_point_select` | 79.33ms | 36.90ms | 0.5× | 2.5% | PASS |
| file_reads | `oltp_range_select` | 16.39ms | 9.88ms | 0.6× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 16.02ms | 10.00ms | 0.6× | 2.7% | PASS |
| file_reads | `oltp_order_range` | 3.08ms | 2.27ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 3.49ms | 2.71ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 8.76ms | 5.89ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 22.30ms | 15.18ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_ranges` | 8.31ms | 5.12ms | 0.6× | 1.9% | PASS |
| file_reads | `covering_index_scan` | 8.80ms | 4.91ms | 0.6× | 2.0% | PASS |
| file_reads | `groupby_scan` | 21.65ms | 20.13ms | 0.9× | 2.6% | PASS |
| file_reads | `index_join` | 11.27ms | 7.39ms | 0.7× | 1.6% | PASS |
| file_reads | `index_join_scan` | 3.38ms | 4.42ms | 1.3× | 1.5% | PASS |
| file_reads | `types_table_scan` | 707.99ms | 742.81ms | 1.0× | 1.4% | PASS |
| file_reads | `table_scan` | 791.79ms | 806.53ms | 1.0× | 1.5% | PASS |
| file_reads | `oltp_read_only` | 158.60ms | 92.38ms | 0.6× | 3.5% | PASS |
| file_writes | `oltp_bulk_insert` | 220.29ms | 289.57ms | 1.3× | 7.2% | PASS |
| file_writes | `oltp_insert` | 19.76ms | 47.24ms | 2.4× | 10.4% | FAIL |
| file_writes | `oltp_update_index` | 161.01ms | 174.29ms | 1.1× | 6.2% | PASS |
| file_writes | `oltp_update_non_index` | 140.47ms | 110.87ms | 0.8× | 6.4% | PASS |
| file_writes | `oltp_delete_insert` | 171.00ms | 128.90ms | 0.8× | 4.8% | PASS |
| file_writes | `oltp_write_only` | 111.22ms | 89.64ms | 0.8× | 4.2% | PASS |
| file_writes | `types_delete_insert` | 132.28ms | 69.65ms | 0.5× | 11.1% | PASS |
| file_writes | `oltp_read_write` | 160.47ms | 138.82ms | 0.9× | 5.3% | PASS |
| ac_reads | `oltp_point_select` | 42.27ms | 37.23ms | 0.9× | 3.4% | PASS |
| ac_reads | `oltp_range_select` | 12.62ms | 9.95ms | 0.8× | 4.1% | PASS |
| ac_reads | `oltp_sum_range` | 12.40ms | 10.15ms | 0.8× | 4.0% | PASS |
| ac_reads | `oltp_order_range` | 2.62ms | 2.17ms | 0.8× | 2.5% | PASS |
| ac_reads | `oltp_distinct_range` | 3.11ms | 2.67ms | 0.9× | 3.1% | PASS |
| ac_reads | `oltp_index_scan` | 4.90ms | 5.80ms | 1.2× | 2.3% | PASS |
| ac_reads | `select_random_points` | 17.53ms | 14.89ms | 0.8× | 3.9% | PASS |
| ac_reads | `select_random_ranges` | 4.43ms | 5.02ms | 1.1× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 4.71ms | 4.69ms | 1.0× | 3.1% | PASS |
| ac_reads | `groupby_scan` | 21.17ms | 20.14ms | 1.0× | 1.8% | PASS |
| ac_reads | `index_join` | 9.61ms | 7.46ms | 0.8× | 3.7% | PASS |
| ac_reads | `index_join_scan` | 2.98ms | 4.37ms | 1.5× | 2.7% | PASS |
| ac_reads | `types_table_scan` | 715.05ms | 749.77ms | 1.0× | 2.6% | PASS |
| ac_reads | `table_scan` | 878.13ms | 864.08ms | 1.0× | 3.1% | PASS |
| ac_reads | `oltp_read_only` | 117.00ms | 98.22ms | 0.8× | 3.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.48ms | 52.35ms | 2.4× | 5.6% | PASS |
| ac_writes | `oltp_insert_ac` | 23.86ms | 61.59ms | 2.6× | 5.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.46ms | 70.16ms | 2.9× | 5.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.76ms | 59.15ms | 2.8× | 6.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.87ms | 64.97ms | 2.7× | 5.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.26ms | 66.68ms | 2.9× | 7.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.10ms | 59.06ms | 2.7× | 7.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.33ms | 69.43ms | 2.5× | 7.2% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.99ms | 28.34ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 13.74ms | 10.93ms | 0.8× | 2.4% | PASS |
| mem_reads | `oltp_sum_range` | 12.55ms | 10.53ms | 0.8× | 1.6% | PASS |
| mem_reads | `oltp_order_range` | 2.76ms | 2.57ms | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.60ms | 3.46ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.25ms | 4.76ms | 1.5× | 1.7% | PASS |
| mem_reads | `select_random_points` | 17.18ms | 16.10ms | 0.9× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 2.79ms | 4.30ms | 1.5× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 3.35ms | 3.72ms | 1.1× | 2.5% | PASS |
| mem_reads | `groupby_scan` | 29.10ms | 28.94ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 8.73ms | 7.35ms | 0.8× | 3.4% | PASS |
| mem_reads | `index_join_scan` | 3.17ms | 4.96ms | 1.6× | 2.0% | PASS |
| mem_reads | `types_table_scan` | 895.24ms | 965.11ms | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.02s | 1.06s | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 109.36ms | 104.28ms | 1.0× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 188.66ms | 267.87ms | 1.4× | 1.4% | PASS |
| mem_writes | `oltp_insert` | 14.59ms | 28.45ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_update_index` | 51.50ms | 105.92ms | 2.1× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 38.98ms | 61.88ms | 1.6× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 42.19ms | 78.94ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 22.67ms | 46.23ms | 2.0× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 30.14ms | 38.30ms | 1.3× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 75.25ms | 107.60ms | 1.4× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 103.87ms | 46.46ms | 0.4× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 21.74ms | 12.86ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_sum_range` | 20.73ms | 12.52ms | 0.6× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 3.64ms | 2.80ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.44ms | 3.69ms | 0.8× | 0.7% | PASS |
| file_reads | `oltp_index_scan` | 11.06ms | 6.89ms | 0.6× | 0.6% | PASS |
| file_reads | `select_random_points` | 25.53ms | 18.14ms | 0.7× | 0.8% | PASS |
| file_reads | `select_random_ranges` | 10.39ms | 6.25ms | 0.6× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 11.28ms | 5.85ms | 0.5× | 0.7% | PASS |
| file_reads | `groupby_scan` | 29.87ms | 29.35ms | 1.0× | 1.4% | PASS |
| file_reads | `index_join` | 13.32ms | 8.98ms | 0.7× | 1.3% | PASS |
| file_reads | `index_join_scan` | 4.07ms | 5.08ms | 1.2× | 1.0% | PASS |
| file_reads | `types_table_scan` | 900.06ms | 958.33ms | 1.1× | 0.8% | PASS |
| file_reads | `table_scan` | 1.03s | 1.07s | 1.0× | 1.0% | PASS |
| file_reads | `oltp_read_only` | 218.85ms | 130.42ms | 0.6× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 275.78ms | 347.22ms | 1.3× | 14.7% | PASS |
| file_writes | `oltp_insert` | 36.65ms | 59.46ms | 1.6× | 44.9% | PASS |
| file_writes | `oltp_update_index` | 242.00ms | 209.75ms | 0.9× | 40.7% | PASS |
| file_writes | `oltp_update_non_index` | 200.54ms | 140.54ms | 0.7× | 31.9% | PASS |
| file_writes | `oltp_delete_insert` | 234.88ms | 161.92ms | 0.7× | 32.9% | PASS |
| file_writes | `oltp_write_only` | 141.65ms | 102.59ms | 0.7× | 26.6% | PASS |
| file_writes | `types_delete_insert` | 133.89ms | 92.14ms | 0.7× | 26.2% | PASS |
| file_writes | `oltp_read_write` | 247.28ms | 176.84ms | 0.7× | 31.5% | PASS |
| ac_reads | `oltp_point_select` | 53.12ms | 46.78ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 16.99ms | 12.95ms | 0.8× | 1.0% | PASS |
| ac_reads | `oltp_sum_range` | 15.53ms | 12.53ms | 0.8× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 3.17ms | 2.81ms | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_distinct_range` | 3.94ms | 3.69ms | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_index_scan` | 6.11ms | 6.91ms | 1.1× | 0.7% | PASS |
| ac_reads | `select_random_points` | 20.38ms | 18.15ms | 0.9× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 5.43ms | 6.21ms | 1.1× | 0.5% | PASS |
| ac_reads | `covering_index_scan` | 6.25ms | 5.83ms | 0.9× | 0.6% | PASS |
| ac_reads | `groupby_scan` | 29.33ms | 29.34ms | 1.0× | 1.2% | PASS |
| ac_reads | `index_join` | 10.88ms | 8.99ms | 0.8× | 1.4% | PASS |
| ac_reads | `index_join_scan` | 3.66ms | 5.13ms | 1.4× | 1.0% | PASS |
| ac_reads | `types_table_scan` | 905.46ms | 960.76ms | 1.1× | 1.3% | PASS |
| ac_reads | `table_scan` | 1.03s | 1.07s | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 145.74ms | 130.78ms | 0.9× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 49.68ms | 138.67ms | 2.8× | 63.7% | PASS |
| ac_writes | `oltp_insert_ac` | 46.27ms | 176.91ms | 3.8× | 71.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 54.37ms | 161.15ms | 3.0× | 48.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 25.00ms | 79.05ms | 3.2× | 43.6% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 31.14ms | 125.60ms | 4.0× | 66.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 51.38ms | 188.00ms | 3.7× | 54.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 39.54ms | 105.92ms | 2.7× | 52.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 45.34ms | 184.86ms | 4.1× | 73.6% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.77ms | 40.75ms | 1.2× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 19.84ms | 21.04ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 17.72ms | 20.53ms | 1.2× | 1.2% | PASS |
| mem_reads | `oltp_order_range` | 3.58ms | 3.84ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.74ms | 5.03ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.53ms | 6.14ms | 1.4× | 1.6% | PASS |
| mem_reads | `select_random_points` | 27.83ms | 32.59ms | 1.2× | 1.4% | PASS |
| mem_reads | `select_random_ranges` | 7.72ms | 8.97ms | 1.2× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.35ms | 1.0× | 2.6% | PASS |
| mem_reads | `groupby_scan` | 36.35ms | 39.64ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 7.96ms | 10.56ms | 1.3× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 3.96ms | 5.71ms | 1.4× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.19s | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.22s | 1.28s | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_read_only` | 151.56ms | 170.87ms | 1.1× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 246.93ms | 349.70ms | 1.4× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 18.90ms | 35.17ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 65.63ms | 125.75ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_non_index` | 50.84ms | 83.87ms | 1.6× | 1.4% | PASS |
| mem_writes | `oltp_delete_insert` | 49.19ms | 96.15ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 26.99ms | 58.80ms | 2.2× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 32.52ms | 53.27ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_read_write` | 102.92ms | 162.91ms | 1.6× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 108.17ms | 60.36ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 27.36ms | 23.24ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 25.81ms | 22.82ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_order_range` | 4.39ms | 4.18ms | 1.0× | 2.4% | PASS |
| file_reads | `oltp_distinct_range` | 5.61ms | 5.40ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 12.04ms | 8.40ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 37.43ms | 35.94ms | 1.0× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 15.66ms | 11.25ms | 0.7× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 11.75ms | 6.64ms | 0.6× | 1.4% | PASS |
| file_reads | `groupby_scan` | 37.45ms | 39.92ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 12.26ms | 12.29ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join_scan` | 4.90ms | 6.06ms | 1.2× | 1.9% | PASS |
| file_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 1.21s | 1.28s | 1.1× | 0.7% | PASS |
| file_reads | `oltp_read_only` | 263.17ms | 201.05ms | 0.8× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 261.53ms | 360.60ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 28.57ms | 40.84ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 97.25ms | 141.29ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 80.57ms | 97.23ms | 1.2× | 1.0% | PASS |
| file_writes | `oltp_delete_insert` | 79.98ms | 107.54ms | 1.3× | 1.2% | PASS |
| file_writes | `oltp_write_only` | 56.22ms | 67.94ms | 1.2× | 2.6% | PASS |
| file_writes | `types_delete_insert` | 49.94ms | 58.72ms | 1.2× | 1.0% | PASS |
| file_writes | `oltp_read_write` | 131.71ms | 170.76ms | 1.3× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 57.38ms | 60.32ms | 1.1× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 22.43ms | 23.30ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 20.64ms | 22.85ms | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_order_range` | 3.97ms | 4.23ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 5.13ms | 5.41ms | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 7.04ms | 8.41ms | 1.2× | 1.8% | PASS |
| ac_reads | `select_random_points` | 30.52ms | 35.98ms | 1.2× | 0.9% | PASS |
| ac_reads | `select_random_ranges` | 10.29ms | 11.22ms | 1.1× | 0.7% | PASS |
| ac_reads | `covering_index_scan` | 6.79ms | 6.58ms | 1.0× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 37.14ms | 39.96ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 9.87ms | 12.61ms | 1.3× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 4.45ms | 6.11ms | 1.4× | 1.3% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.18s | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.28s | 1.1× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 183.74ms | 199.39ms | 1.1× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.68ms | 58.62ms | 2.8× | 6.4% | PASS |
| ac_writes | `oltp_insert_ac` | 24.37ms | 74.41ms | 3.1× | 8.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.98ms | 82.49ms | 3.2× | 5.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.51ms | 67.44ms | 3.0× | 7.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.00ms | 75.54ms | 3.1× | 6.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.59ms | 75.57ms | 3.2× | 6.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.91ms | 66.92ms | 3.1× | 9.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.99ms | 83.19ms | 2.9× | 3.5% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 45s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 64.11ms | 130.00ms | 49.3% | 1.2% | PASS |
| `status_dirty_many_tables` | 66.87ms | 130.00ms | 51.4% | 1.0% | PASS |
| `diff_regular_working_one_table` | 60.47ms | 120.00ms | 50.4% | 0.8% | PASS |
| `diff_regular_working_many_tables` | 68.41ms | 140.00ms | 48.9% | 1.0% | PASS |
| `diff_stat_working_many_tables` | 68.48ms | 140.00ms | 48.9% | 0.8% | PASS |
| `diff_schema_working_many_tables` | 68.94ms | 140.00ms | 49.2% | 1.0% | PASS |
| `branch_list_many_branches` | 18.00ms | 35.00ms | 51.4% | 1.4% | PASS |
| `branch_create_delete` | 20.31ms | 40.00ms | 50.8% | 2.2% | PASS |
| `checkout_branch_clean` | 92.48ms | 150.00ms | 61.7% | 7.1% | PASS |
| `merge_data_no_conflicts` | 31.36ms | 50.00ms | 62.7% | 2.1% | PASS |
| `merge_schema_no_conflicts` | 17.91ms | 35.00ms | 51.2% | 1.8% | PASS |
| `merge_data_conflicts` | 25.22ms | 180.00ms | 14.0% | 1.1% | PASS |
| `merge_data_conflicts_with_resolve` | 25.01ms | 180.00ms | 13.9% | 1.1% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
