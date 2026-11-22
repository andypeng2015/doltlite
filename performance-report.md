# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-22 18:51 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261116.202.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/45048557701)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.51s | 9.05s | 1.1× | 1.4% | **PASS** |
| Writes | 1.70s | 2.88s | 1.7× | 1.1% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.14s | 9.20s | 1.0× | 1.4% | **PASS** |
| Writes | 2.58s | 3.36s | 1.3× | 1.7% | **PASS** |
| Autocommit writes | 529.11ms | 1.72s | 3.3× | 5.9% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.00s | 2.13s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.90s | 2.02s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.66s | 2.83s | 1.1× | 1.5% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 1.95s | 2.07s | 1.1× | 1.0% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 338.89ms | 564.60ms | 1.7× | 1.6% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 381.21ms | 651.37ms | 1.7× | 1.2% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 603.88ms | 1.05s | 1.7× | 1.3% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 371.40ms | 611.66ms | 1.6× | 0.7% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.21s | 2.18s | 1.0× | 0.9% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.00s | 2.04s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.90s | 2.90s | 1.0× | 1.8% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.03s | 2.08s | 1.0× | 1.1% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 813.58ms | 841.32ms | 1.0× | 3.0% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 492.05ms | 709.67ms | 1.4× | 1.7% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 819.93ms | 1.13s | 1.4× | 2.0% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 452.76ms | 674.17ms | 1.5× | 1.5% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.06s | 2.18s | 1.1× | 1.0% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.93s | 2.02s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.86s | 2.94s | 1.0× | 1.8% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 1.97s | 2.07s | 1.1× | 1.0% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 155.27ms | 510.23ms | 3.3× | 8.4% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 81.28ms | 281.26ms | 3.5× | 4.4% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 212.29ms | 635.00ms | 3.0× | 7.4% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 80.28ms | 295.23ms | 3.7× | 4.6% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 18.96ms | 22.02ms | 1.2× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 9.35ms | 8.88ms | 0.9× | 2.7% | PASS |
| mem_reads | `oltp_sum_range` | 7.88ms | 8.60ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 2.19ms | 2.30ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 2.96ms | 3.14ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.15ms | 4.03ms | 1.3× | 2.3% | PASS |
| mem_reads | `select_random_points` | 9.54ms | 9.14ms | 1.0× | 4.1% | PASS |
| mem_reads | `select_random_ranges` | 2.70ms | 3.18ms | 1.2× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 3.29ms | 3.34ms | 1.0× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 24.76ms | 27.24ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 4.61ms | 6.28ms | 1.4× | 1.4% | PASS |
| mem_reads | `index_join_scan` | 2.72ms | 3.91ms | 1.4× | 2.5% | PASS |
| mem_reads | `types_table_scan` | 856.24ms | 924.05ms | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 968.42ms | 1.01s | 1.0× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 81.10ms | 89.63ms | 1.1× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 135.29ms | 205.65ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 11.66ms | 21.54ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 39.87ms | 76.38ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 28.10ms | 47.53ms | 1.7× | 2.0% | PASS |
| mem_writes | `oltp_delete_insert` | 34.84ms | 58.49ms | 1.7× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 17.61ms | 38.40ms | 2.2× | 1.7% | PASS |
| mem_writes | `types_delete_insert` | 19.48ms | 30.19ms | 1.6× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 52.04ms | 86.42ms | 1.7× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 86.88ms | 38.10ms | 0.4× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 16.00ms | 10.55ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 14.72ms | 10.41ms | 0.7× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 2.94ms | 2.52ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 3.71ms | 3.35ms | 0.9× | 0.8% | PASS |
| file_reads | `oltp_index_scan` | 10.15ms | 5.92ms | 0.6× | 0.8% | PASS |
| file_reads | `select_random_points` | 15.55ms | 10.65ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_ranges` | 9.47ms | 4.84ms | 0.5× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 10.40ms | 5.25ms | 0.5× | 0.9% | PASS |
| file_reads | `groupby_scan` | 25.17ms | 27.32ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 8.36ms | 7.56ms | 0.9× | 1.4% | PASS |
| file_reads | `index_join_scan` | 3.51ms | 4.06ms | 1.2× | 2.5% | PASS |
| file_reads | `types_table_scan` | 853.51ms | 924.71ms | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 969.75ms | 1.01s | 1.0× | 0.9% | PASS |
| file_reads | `oltp_read_only` | 178.59ms | 112.81ms | 0.6× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 173.58ms | 248.50ms | 1.4× | 2.7% | PASS |
| file_writes | `oltp_insert` | 19.73ms | 28.35ms | 1.4× | 3.3% | PASS |
| file_writes | `oltp_update_index` | 136.19ms | 123.74ms | 0.9× | 0.6% | PASS |
| file_writes | `oltp_update_non_index` | 105.81ms | 92.87ms | 0.9× | 0.3% | PASS |
| file_writes | `oltp_delete_insert` | 111.04ms | 105.95ms | 1.0× | 7.9% | PASS |
| file_writes | `oltp_write_only` | 85.92ms | 73.06ms | 0.9× | 5.3% | PASS |
| file_writes | `types_delete_insert` | 60.64ms | 50.19ms | 0.8× | 5.3% | PASS |
| file_writes | `oltp_read_write` | 120.67ms | 118.67ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_point_select` | 39.40ms | 38.00ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 11.36ms | 10.60ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 10.15ms | 10.37ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 2.58ms | 2.52ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_distinct_range` | 3.30ms | 3.34ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_index_scan` | 5.59ms | 5.91ms | 1.1× | 0.9% | PASS |
| ac_reads | `select_random_points` | 11.02ms | 10.68ms | 1.0× | 1.1% | PASS |
| ac_reads | `select_random_ranges` | 4.94ms | 4.85ms | 1.0× | 0.6% | PASS |
| ac_reads | `covering_index_scan` | 5.86ms | 5.26ms | 0.9× | 1.0% | PASS |
| ac_reads | `groupby_scan` | 24.87ms | 27.38ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 6.12ms | 7.62ms | 1.2× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.11ms | 4.17ms | 1.3× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 850.84ms | 924.75ms | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 969.31ms | 1.01s | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 111.24ms | 112.61ms | 1.0× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 16.90ms | 48.33ms | 2.9× | 3.6% | PASS |
| ac_writes | `oltp_insert_ac` | 20.05ms | 60.73ms | 3.0× | 8.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.35ms | 74.38ms | 3.5× | 6.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.28ms | 57.52ms | 3.3× | 8.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 18.86ms | 66.58ms | 3.5× | 10.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 18.76ms | 65.31ms | 3.5× | 7.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 17.71ms | 64.72ms | 3.7× | 11.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 24.36ms | 72.66ms | 3.0× | 13.3% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.74ms | 23.51ms | 1.0× | 2.3% | PASS |
| mem_reads | `oltp_range_select` | 11.87ms | 9.54ms | 0.8× | 2.0% | PASS |
| mem_reads | `oltp_sum_range` | 11.79ms | 9.27ms | 0.8× | 2.5% | PASS |
| mem_reads | `oltp_order_range` | 2.43ms | 2.24ms | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.12ms | 2.96ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 2.87ms | 4.42ms | 1.5× | 1.4% | PASS |
| mem_reads | `select_random_points` | 18.61ms | 17.01ms | 0.9× | 0.9% | PASS |
| mem_reads | `select_random_ranges` | 2.41ms | 3.67ms | 1.5× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 2.59ms | 2.99ms | 1.2× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 24.59ms | 24.59ms | 1.0× | 0.5% | PASS |
| mem_reads | `index_join` | 9.62ms | 7.09ms | 0.7× | 2.0% | PASS |
| mem_reads | `index_join_scan` | 2.86ms | 4.95ms | 1.7× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 793.82ms | 859.90ms | 1.1× | 0.9% | PASS |
| mem_reads | `table_scan` | 893.77ms | 950.91ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 96.45ms | 92.39ms | 1.0× | 2.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 138.93ms | 215.52ms | 1.6× | 0.5% | PASS |
| mem_writes | `oltp_insert` | 11.14ms | 24.82ms | 2.2× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 47.72ms | 103.78ms | 2.2× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 34.30ms | 60.60ms | 1.8× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 39.12ms | 74.03ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 19.75ms | 42.41ms | 2.1× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 26.09ms | 36.12ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 64.15ms | 94.08ms | 1.5× | 1.3% | PASS |
| file_reads | `oltp_point_select` | 46.11ms | 29.07ms | 0.6× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 13.80ms | 10.07ms | 0.7× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 14.21ms | 10.13ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 2.73ms | 2.36ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_distinct_range` | 3.36ms | 3.03ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 5.13ms | 4.97ms | 1.0× | 1.3% | PASS |
| file_reads | `select_random_points` | 21.69ms | 18.12ms | 0.8× | 1.6% | PASS |
| file_reads | `select_random_ranges` | 4.62ms | 4.15ms | 0.9× | 1.8% | PASS |
| file_reads | `covering_index_scan` | 4.81ms | 3.66ms | 0.8× | 2.1% | PASS |
| file_reads | `groupby_scan` | 24.71ms | 24.65ms | 1.0× | 0.4% | PASS |
| file_reads | `index_join` | 10.74ms | 7.44ms | 0.7× | 1.7% | PASS |
| file_reads | `index_join_scan` | 3.13ms | 5.04ms | 1.6× | 2.8% | PASS |
| file_reads | `types_table_scan` | 792.88ms | 860.16ms | 1.1× | 1.1% | PASS |
| file_reads | `table_scan` | 923.43ms | 960.77ms | 1.0× | 3.0% | PASS |
| file_reads | `oltp_read_only` | 129.47ms | 99.88ms | 0.8× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 152.10ms | 224.24ms | 1.5× | 0.5% | PASS |
| file_writes | `oltp_insert` | 14.69ms | 29.25ms | 2.0× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 63.23ms | 110.06ms | 1.7× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 50.15ms | 70.64ms | 1.4× | 1.9% | PASS |
| file_writes | `oltp_delete_insert` | 57.37ms | 81.40ms | 1.4× | 2.0% | PASS |
| file_writes | `oltp_write_only` | 34.66ms | 50.55ms | 1.5× | 1.6% | PASS |
| file_writes | `types_delete_insert` | 41.73ms | 41.88ms | 1.0× | 2.0% | PASS |
| file_writes | `oltp_read_write` | 78.11ms | 101.65ms | 1.3× | 1.7% | PASS |
| ac_reads | `oltp_point_select` | 31.37ms | 28.83ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 12.94ms | 10.24ms | 0.8× | 1.5% | PASS |
| ac_reads | `oltp_sum_range` | 12.69ms | 9.90ms | 0.8× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 2.57ms | 2.29ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 3.16ms | 2.96ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 3.65ms | 4.56ms | 1.3× | 2.0% | PASS |
| ac_reads | `select_random_points` | 18.44ms | 16.39ms | 0.9× | 3.2% | PASS |
| ac_reads | `select_random_ranges` | 3.07ms | 3.87ms | 1.3× | 2.7% | PASS |
| ac_reads | `covering_index_scan` | 3.30ms | 3.30ms | 1.0× | 0.7% | PASS |
| ac_reads | `groupby_scan` | 24.08ms | 24.20ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 9.43ms | 6.75ms | 0.7× | 2.3% | PASS |
| ac_reads | `index_join_scan` | 2.82ms | 4.43ms | 1.6× | 3.5% | PASS |
| ac_reads | `types_table_scan` | 791.88ms | 857.17ms | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 905.98ms | 946.25ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_read_only` | 102.42ms | 95.53ms | 0.9× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 9.07ms | 27.49ms | 3.0× | 6.1% | PASS |
| ac_writes | `oltp_insert_ac` | 10.16ms | 35.14ms | 3.5× | 4.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 10.48ms | 41.53ms | 4.0× | 4.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 8.81ms | 30.93ms | 3.5× | 4.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 10.03ms | 37.10ms | 3.7× | 3.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 9.82ms | 35.66ms | 3.6× | 4.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 9.22ms | 31.17ms | 3.4× | 4.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 13.68ms | 42.24ms | 3.1× | 4.6% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.88ms | 38.37ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 17.91ms | 14.32ms | 0.8× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 15.39ms | 13.63ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 3.38ms | 3.24ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.36ms | 4.41ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.01ms | 6.20ms | 1.5× | 1.2% | PASS |
| mem_reads | `select_random_points` | 22.17ms | 20.53ms | 0.9× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 3.43ms | 5.29ms | 1.5× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.61ms | 1.1× | 2.2% | PASS |
| mem_reads | `groupby_scan` | 34.56ms | 35.00ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 10.30ms | 9.05ms | 0.9× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.75ms | 5.32ms | 1.4× | 3.2% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.21s | 1.1× | 1.8% | PASS |
| mem_reads | `table_scan` | 1.28s | 1.32s | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_read_only` | 143.75ms | 140.72ms | 1.0× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 238.48ms | 378.36ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.20ms | 40.54ms | 2.2× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 68.61ms | 152.41ms | 2.2× | 2.0% | PASS |
| mem_writes | `oltp_update_non_index` | 50.34ms | 91.05ms | 1.8× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 54.17ms | 110.13ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 28.34ms | 66.62ms | 2.4× | 0.8% | FAIL |
| mem_writes | `types_delete_insert` | 38.79ms | 57.25ms | 1.5× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 106.96ms | 155.00ms | 1.4× | 2.2% | PASS |
| file_reads | `oltp_point_select` | 107.42ms | 56.92ms | 0.5× | 1.5% | PASS |
| file_reads | `oltp_range_select` | 25.61ms | 16.24ms | 0.6× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 22.91ms | 15.50ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.36ms | 3.56ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.39ms | 4.71ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 11.33ms | 8.25ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_points` | 30.97ms | 23.40ms | 0.8× | 3.0% | PASS |
| file_reads | `select_random_ranges` | 10.55ms | 7.13ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.63ms | 6.64ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 36.34ms | 35.64ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 15.40ms | 11.02ms | 0.7× | 2.5% | PASS |
| file_reads | `index_join_scan` | 4.89ms | 5.73ms | 1.2× | 2.2% | PASS |
| file_reads | `types_table_scan` | 1.12s | 1.23s | 1.1× | 1.8% | PASS |
| file_reads | `table_scan` | 1.25s | 1.31s | 1.0× | 3.1% | PASS |
| file_reads | `oltp_read_only` | 239.85ms | 166.32ms | 0.7× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 257.85ms | 387.36ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.01ms | 47.93ms | 1.8× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 103.49ms | 168.08ms | 1.6× | 2.6% | PASS |
| file_writes | `oltp_update_non_index` | 90.46ms | 104.37ms | 1.2× | 8.6% | PASS |
| file_writes | `oltp_delete_insert` | 89.92ms | 124.24ms | 1.4× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 59.13ms | 78.14ms | 1.3× | 2.1% | PASS |
| file_writes | `types_delete_insert` | 63.57ms | 65.24ms | 1.0× | 2.9% | PASS |
| file_writes | `oltp_read_write` | 129.49ms | 159.55ms | 1.2× | 1.9% | PASS |
| ac_reads | `oltp_point_select` | 57.38ms | 56.28ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 19.80ms | 16.16ms | 0.8× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 17.53ms | 15.53ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_order_range` | 3.82ms | 3.52ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_distinct_range` | 4.79ms | 4.70ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.54ms | 8.19ms | 1.3× | 1.8% | PASS |
| ac_reads | `select_random_points` | 24.35ms | 23.15ms | 1.0× | 3.0% | PASS |
| ac_reads | `select_random_ranges` | 5.79ms | 7.14ms | 1.2× | 1.8% | PASS |
| ac_reads | `covering_index_scan` | 6.81ms | 6.55ms | 1.0× | 1.8% | PASS |
| ac_reads | `groupby_scan` | 35.06ms | 35.49ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 12.11ms | 10.98ms | 0.9× | 3.0% | PASS |
| ac_reads | `index_join_scan` | 4.26ms | 5.77ms | 1.4× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 1.11s | 1.21s | 1.1× | 3.0% | PASS |
| ac_reads | `table_scan` | 1.36s | 1.36s | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_read_only` | 190.90ms | 175.24ms | 0.9× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.57ms | 62.90ms | 2.7× | 9.1% | PASS |
| ac_writes | `oltp_insert_ac` | 26.76ms | 79.59ms | 3.0× | 5.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.75ms | 91.22ms | 3.3× | 7.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.01ms | 75.03ms | 3.1× | 7.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.61ms | 84.83ms | 3.1× | 9.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.13ms | 80.63ms | 3.1× | 6.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.06ms | 74.93ms | 3.1× | 7.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.38ms | 85.87ms | 2.7× | 7.8% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 21.47ms | 25.68ms | 1.2× | 0.9% | PASS |
| mem_reads | `oltp_range_select` | 14.16ms | 14.18ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_sum_range` | 13.60ms | 13.62ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_order_range` | 2.69ms | 2.74ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.34ms | 3.44ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.25ms | 4.38ms | 1.3× | 1.6% | PASS |
| mem_reads | `select_random_points` | 19.97ms | 23.59ms | 1.2× | 1.0% | PASS |
| mem_reads | `select_random_ranges` | 5.22ms | 6.02ms | 1.2× | 0.8% | PASS |
| mem_reads | `covering_index_scan` | 2.58ms | 3.00ms | 1.2× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 25.59ms | 27.90ms | 1.1× | 0.4% | PASS |
| mem_reads | `index_join` | 5.79ms | 8.04ms | 1.4× | 1.0% | PASS |
| mem_reads | `index_join_scan` | 2.95ms | 4.64ms | 1.6× | 1.5% | PASS |
| mem_reads | `types_table_scan` | 797.67ms | 874.17ms | 1.1× | 2.5% | PASS |
| mem_reads | `table_scan` | 926.01ms | 951.28ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_read_only` | 103.64ms | 111.17ms | 1.1× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 143.47ms | 202.39ms | 1.4× | 0.3% | PASS |
| mem_writes | `oltp_insert` | 12.00ms | 22.01ms | 1.8× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 45.98ms | 89.07ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 33.30ms | 58.00ms | 1.7× | 0.8% | PASS |
| mem_writes | `oltp_delete_insert` | 34.19ms | 66.45ms | 1.9× | 0.6% | PASS |
| mem_writes | `oltp_write_only` | 18.28ms | 38.96ms | 2.1× | 0.7% | PASS |
| mem_writes | `types_delete_insert` | 20.67ms | 33.95ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_read_write` | 63.50ms | 100.83ms | 1.6× | 0.7% | PASS |
| file_reads | `oltp_point_select` | 43.33ms | 30.78ms | 0.7× | 0.6% | PASS |
| file_reads | `oltp_range_select` | 15.76ms | 14.58ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_sum_range` | 15.45ms | 14.15ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 2.90ms | 2.80ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_distinct_range` | 3.57ms | 3.51ms | 1.0× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 5.48ms | 4.94ms | 0.9× | 1.9% | PASS |
| file_reads | `select_random_points` | 21.83ms | 23.89ms | 1.1× | 0.9% | PASS |
| file_reads | `select_random_ranges` | 7.45ms | 6.67ms | 0.9× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 4.82ms | 3.45ms | 0.7× | 1.4% | PASS |
| file_reads | `groupby_scan` | 25.91ms | 28.00ms | 1.1× | 0.4% | PASS |
| file_reads | `index_join` | 6.98ms | 8.24ms | 1.2× | 1.7% | PASS |
| file_reads | `index_join_scan` | 3.22ms | 4.65ms | 1.4× | 1.8% | PASS |
| file_reads | `types_table_scan` | 810.75ms | 872.70ms | 1.1× | 0.9% | PASS |
| file_reads | `table_scan` | 930.21ms | 947.86ms | 1.0× | 3.2% | PASS |
| file_reads | `oltp_read_only` | 129.66ms | 116.68ms | 0.9× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 153.26ms | 211.73ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 15.10ms | 26.10ms | 1.7× | 1.3% | PASS |
| file_writes | `oltp_update_index` | 58.96ms | 98.06ms | 1.7× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 44.31ms | 65.54ms | 1.5× | 1.9% | PASS |
| file_writes | `oltp_delete_insert` | 45.21ms | 72.94ms | 1.6× | 1.1% | PASS |
| file_writes | `oltp_write_only` | 29.50ms | 47.56ms | 1.6× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 29.15ms | 40.28ms | 1.4× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 77.26ms | 111.94ms | 1.4× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 28.36ms | 30.32ms | 1.1× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 14.88ms | 14.73ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_sum_range` | 14.14ms | 14.12ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 2.81ms | 2.80ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 3.49ms | 3.53ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 4.16ms | 4.90ms | 1.2× | 2.3% | PASS |
| ac_reads | `select_random_points` | 20.44ms | 23.96ms | 1.2× | 0.8% | PASS |
| ac_reads | `select_random_ranges` | 5.98ms | 6.59ms | 1.1× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 3.36ms | 3.39ms | 1.0× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 25.57ms | 27.91ms | 1.1× | 0.5% | PASS |
| ac_reads | `index_join` | 6.28ms | 8.05ms | 1.3× | 1.2% | PASS |
| ac_reads | `index_join_scan` | 3.14ms | 4.61ms | 1.5× | 1.8% | PASS |
| ac_reads | `types_table_scan` | 803.57ms | 869.54ms | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 927.85ms | 943.89ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 107.00ms | 115.65ms | 1.1× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 8.72ms | 26.78ms | 3.1× | 5.6% | PASS |
| ac_writes | `oltp_insert_ac` | 10.13ms | 37.88ms | 3.7× | 5.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 10.74ms | 43.71ms | 4.1× | 3.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 9.16ms | 33.29ms | 3.6× | 4.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 9.57ms | 38.31ms | 4.0× | 4.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 9.94ms | 38.77ms | 3.9× | 4.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 8.57ms | 32.47ms | 3.8× | 7.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 13.44ms | 44.02ms | 3.3× | 2.4% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 1s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 90.40ms | 130.00ms | 69.5% | 0.3% | PASS |
| `status_dirty_many_tables` | 94.19ms | 130.00ms | 72.5% | 0.5% | PASS |
| `diff_regular_working_one_table` | 85.78ms | 120.00ms | 71.5% | 0.3% | PASS |
| `diff_regular_working_many_tables` | 98.58ms | 140.00ms | 70.4% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 98.64ms | 140.00ms | 70.5% | 0.4% | PASS |
| `diff_schema_working_many_tables` | 100.17ms | 140.00ms | 71.6% | 0.7% | PASS |
| `branch_list_many_branches` | 19.16ms | 35.00ms | 54.7% | 1.2% | PASS |
| `branch_create_delete` | 21.62ms | 40.00ms | 54.0% | 1.8% | PASS |
| `checkout_branch_clean` | 56.77ms | 150.00ms | 37.8% | 0.8% | PASS |
| `merge_data_no_conflicts` | 25.68ms | 50.00ms | 51.4% | 1.5% | PASS |
| `merge_schema_no_conflicts` | 19.00ms | 35.00ms | 54.3% | 1.8% | PASS |
| `merge_data_conflicts` | 28.11ms | 180.00ms | 15.6% | 1.1% | PASS |
| `merge_data_conflicts_with_resolve` | 28.58ms | 180.00ms | 15.9% | 1.3% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
