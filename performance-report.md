# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-04 19:01 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261026.180.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/41877338890)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.07s | 9.52s | 1.0× | 1.6% | **PASS** |
| Writes | 1.84s | 3.14s | 1.7× | 1.1% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.01s | 9.74s | 1.0× | 1.1% | **PASS** |
| Writes | 4.06s | 4.44s | 1.1× | 4.3% | **PASS** |
| Autocommit writes | 739.54ms | 2.37s | 3.2× | 11.2% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 1.99s | 2.13s | 1.1× | 1.6% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.79s | 2.84s | 1.0× | 2.1% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.08s | 2.23s | 1.1× | 1.1% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.22s | 2.33s | 1.1× | 1.5% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 332.33ms | 557.36ms | 1.7× | 1.0% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 598.58ms | 1.06s | 1.8× | 1.6% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 444.75ms | 764.68ms | 1.7× | 0.8% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 460.69ms | 756.44ms | 1.6× | 1.4% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.18s | 2.16s | 1.0× | 1.3% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.05s | 2.92s | 1.0× | 1.8% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.32s | 2.28s | 1.0× | 0.7% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.47s | 2.38s | 1.0× | 1.1% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 845.32ms | 869.56ms | 1.0× | 2.6% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 972.24ms | 1.21s | 1.2× | 4.3% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.15s | 1.21s | 1.1× | 2.7% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 1.09s | 1.15s | 1.1× | 13.8% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.04s | 2.17s | 1.1× | 1.0% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 3.02s | 2.95s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.33s | 2.37s | 1.0× | 0.9% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.18s | 2.36s | 1.1× | 1.0% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 150.93ms | 474.59ms | 3.1× | 12.4% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 206.82ms | 608.88ms | 2.9× | 7.6% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 157.27ms | 473.66ms | 3.0× | 7.9% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 224.53ms | 815.26ms | 3.6× | 45.1% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 18.95ms | 21.92ms | 1.2× | 2.2% | PASS |
| mem_reads | `oltp_range_select` | 9.35ms | 8.94ms | 1.0× | 3.0% | PASS |
| mem_reads | `oltp_sum_range` | 7.71ms | 8.51ms | 1.1× | 2.9% | PASS |
| mem_reads | `oltp_order_range` | 2.15ms | 2.26ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_distinct_range` | 2.95ms | 3.11ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.03ms | 3.83ms | 1.3× | 2.4% | PASS |
| mem_reads | `select_random_points` | 8.44ms | 8.83ms | 1.0× | 3.1% | PASS |
| mem_reads | `select_random_ranges` | 2.52ms | 3.10ms | 1.2× | 2.2% | PASS |
| mem_reads | `covering_index_scan` | 3.27ms | 3.12ms | 1.0× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 24.36ms | 26.95ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 4.53ms | 5.88ms | 1.3× | 1.3% | PASS |
| mem_reads | `index_join_scan` | 2.74ms | 3.77ms | 1.4× | 2.3% | PASS |
| mem_reads | `types_table_scan` | 865.10ms | 928.28ms | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 955.59ms | 1.01s | 1.1× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 79.26ms | 88.53ms | 1.1× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 135.85ms | 203.50ms | 1.5× | 0.6% | PASS |
| mem_writes | `oltp_insert` | 11.63ms | 21.28ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 39.08ms | 75.59ms | 1.9× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 27.46ms | 47.44ms | 1.7× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 34.06ms | 57.87ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 16.79ms | 37.75ms | 2.2× | 1.0% | PASS |
| mem_writes | `types_delete_insert` | 18.64ms | 29.81ms | 1.6× | 0.8% | PASS |
| mem_writes | `oltp_read_write` | 48.83ms | 84.13ms | 1.7× | 1.0% | PASS |
| file_reads | `oltp_point_select` | 84.73ms | 37.38ms | 0.4× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 15.51ms | 10.47ms | 0.7× | 1.1% | PASS |
| file_reads | `oltp_sum_range` | 14.44ms | 10.26ms | 0.7× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 2.99ms | 2.50ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_distinct_range` | 3.71ms | 3.33ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 9.96ms | 5.87ms | 0.6× | 1.6% | PASS |
| file_reads | `select_random_points` | 15.34ms | 10.57ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 9.34ms | 4.77ms | 0.5× | 1.6% | PASS |
| file_reads | `covering_index_scan` | 10.32ms | 5.10ms | 0.5× | 1.6% | PASS |
| file_reads | `groupby_scan` | 25.27ms | 27.24ms | 1.1× | 0.6% | PASS |
| file_reads | `index_join` | 8.24ms | 7.37ms | 0.9× | 1.5% | PASS |
| file_reads | `index_join_scan` | 3.48ms | 4.02ms | 1.2× | 1.3% | PASS |
| file_reads | `types_table_scan` | 845.28ms | 917.58ms | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 952.87ms | 1.01s | 1.1× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 173.58ms | 110.76ms | 0.6× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 177.39ms | 246.68ms | 1.4× | 2.7% | PASS |
| file_writes | `oltp_insert` | 27.78ms | 32.35ms | 1.2× | 2.6% | PASS |
| file_writes | `oltp_update_index` | 135.27ms | 132.69ms | 1.0× | 9.4% | PASS |
| file_writes | `oltp_update_non_index` | 110.59ms | 94.32ms | 0.9× | 2.3% | PASS |
| file_writes | `oltp_delete_insert` | 116.76ms | 108.43ms | 0.9× | 3.7% | PASS |
| file_writes | `oltp_write_only` | 86.69ms | 76.28ms | 0.9× | 2.2% | PASS |
| file_writes | `types_delete_insert` | 72.08ms | 55.99ms | 0.8× | 10.0% | PASS |
| file_writes | `oltp_read_write` | 118.74ms | 122.83ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 38.95ms | 37.29ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_range_select` | 11.22ms | 10.42ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_sum_range` | 9.97ms | 10.24ms | 1.0× | 0.6% | PASS |
| ac_reads | `oltp_order_range` | 2.56ms | 2.50ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_distinct_range` | 3.29ms | 3.32ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 5.56ms | 5.91ms | 1.1× | 0.7% | PASS |
| ac_reads | `select_random_points` | 11.03ms | 10.62ms | 1.0× | 1.1% | PASS |
| ac_reads | `select_random_ranges` | 4.87ms | 4.77ms | 1.0× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 5.79ms | 5.15ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 24.86ms | 27.27ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 6.02ms | 7.43ms | 1.2× | 1.1% | PASS |
| ac_reads | `index_join_scan` | 3.09ms | 4.01ms | 1.3× | 1.0% | PASS |
| ac_reads | `types_table_scan` | 845.82ms | 920.88ms | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 956.06ms | 1.01s | 1.1× | 0.6% | PASS |
| ac_reads | `oltp_read_only` | 109.88ms | 110.86ms | 1.0× | 0.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 16.88ms | 45.67ms | 2.7× | 14.2% | PASS |
| ac_writes | `oltp_insert_ac` | 18.34ms | 57.67ms | 3.1× | 6.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.59ms | 71.25ms | 3.3× | 12.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.59ms | 54.42ms | 3.1× | 13.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 18.65ms | 62.81ms | 3.4× | 17.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.29ms | 62.99ms | 3.3× | 11.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 16.11ms | 55.79ms | 3.5× | 12.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 22.48ms | 63.98ms | 2.8× | 5.7% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.86ms | 39.64ms | 1.0× | 2.8% | PASS |
| mem_reads | `oltp_range_select` | 17.72ms | 14.55ms | 0.8× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 15.80ms | 14.08ms | 0.9× | 2.1% | PASS |
| mem_reads | `oltp_order_range` | 3.42ms | 3.26ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_distinct_range` | 4.41ms | 4.42ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 4.05ms | 6.37ms | 1.6× | 2.3% | PASS |
| mem_reads | `select_random_points` | 24.02ms | 21.84ms | 0.9× | 3.5% | PASS |
| mem_reads | `select_random_ranges` | 3.63ms | 5.35ms | 1.5× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 4.21ms | 4.79ms | 1.1× | 2.3% | PASS |
| mem_reads | `groupby_scan` | 34.84ms | 35.43ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 10.37ms | 9.19ms | 0.9× | 3.0% | PASS |
| mem_reads | `index_join_scan` | 3.77ms | 5.39ms | 1.4× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 1.14s | 1.21s | 1.1× | 2.2% | PASS |
| mem_reads | `table_scan` | 1.34s | 1.32s | 1.0× | 2.0% | PASS |
| mem_reads | `oltp_read_only` | 148.83ms | 142.81ms | 1.0× | 2.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 235.93ms | 378.50ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 17.67ms | 40.26ms | 2.3× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 66.62ms | 150.88ms | 2.3× | 1.7% | PASS |
| mem_writes | `oltp_update_non_index` | 50.68ms | 94.20ms | 1.9× | 2.4% | PASS |
| mem_writes | `oltp_delete_insert` | 56.53ms | 112.70ms | 2.0× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 29.20ms | 68.29ms | 2.3× | 1.1% | FAIL |
| mem_writes | `types_delete_insert` | 39.47ms | 59.46ms | 1.5× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 102.48ms | 153.93ms | 1.5× | 3.2% | PASS |
| file_reads | `oltp_point_select` | 104.20ms | 55.98ms | 0.5× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 24.13ms | 16.23ms | 0.7× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 22.61ms | 15.83ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.32ms | 3.58ms | 0.8× | 2.7% | PASS |
| file_reads | `oltp_distinct_range` | 5.34ms | 4.69ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 11.17ms | 8.23ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_points` | 30.38ms | 23.67ms | 0.8× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 10.38ms | 7.09ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.32ms | 6.69ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 35.99ms | 35.89ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 14.86ms | 11.13ms | 0.7× | 2.3% | PASS |
| file_reads | `index_join_scan` | 4.74ms | 5.81ms | 1.2× | 2.5% | PASS |
| file_reads | `types_table_scan` | 1.12s | 1.22s | 1.1× | 3.7% | PASS |
| file_reads | `table_scan` | 1.40s | 1.34s | 1.0× | 3.2% | PASS |
| file_reads | `oltp_read_only` | 254.56ms | 170.77ms | 0.7× | 1.5% | PASS |
| file_writes | `oltp_bulk_insert` | 262.37ms | 403.73ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.37ms | 49.91ms | 1.9× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 140.78ms | 183.44ms | 1.3× | 6.3% | PASS |
| file_writes | `oltp_update_non_index` | 102.22ms | 111.77ms | 1.1× | 12.6% | PASS |
| file_writes | `oltp_delete_insert` | 100.66ms | 131.56ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 91.52ms | 82.64ms | 0.9× | 9.5% | PASS |
| file_writes | `types_delete_insert` | 77.04ms | 70.73ms | 0.9× | 2.3% | PASS |
| file_writes | `oltp_read_write` | 171.30ms | 171.74ms | 1.0× | 9.7% | PASS |
| ac_reads | `oltp_point_select` | 60.34ms | 57.15ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 20.32ms | 16.39ms | 0.8× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 18.82ms | 16.32ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.89ms | 3.54ms | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.89ms | 4.71ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.74ms | 8.38ms | 1.2× | 1.4% | PASS |
| ac_reads | `select_random_points` | 26.70ms | 24.55ms | 0.9× | 2.8% | PASS |
| ac_reads | `select_random_ranges` | 6.02ms | 7.21ms | 1.2× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 6.95ms | 6.75ms | 1.0× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 35.97ms | 36.09ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 13.07ms | 11.27ms | 0.9× | 2.3% | PASS |
| ac_reads | `index_join_scan` | 4.30ms | 6.00ms | 1.4× | 2.3% | PASS |
| ac_reads | `types_table_scan` | 1.25s | 1.25s | 1.0× | 1.6% | PASS |
| ac_reads | `table_scan` | 1.38s | 1.34s | 1.0× | 3.3% | PASS |
| ac_reads | `oltp_read_only` | 181.47ms | 169.32ms | 0.9× | 2.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.92ms | 62.20ms | 2.7× | 7.0% | PASS |
| ac_writes | `oltp_insert_ac` | 26.37ms | 73.47ms | 2.8× | 7.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.23ms | 89.20ms | 3.3× | 6.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.22ms | 69.75ms | 3.3× | 7.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.61ms | 79.19ms | 3.1× | 7.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.29ms | 80.03ms | 3.0× | 8.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.07ms | 68.75ms | 2.9× | 11.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 33.11ms | 86.29ms | 2.6× | 8.8% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 29.34ms | 28.07ms | 1.0× | 3.1% | PASS |
| mem_reads | `oltp_range_select` | 13.85ms | 10.67ms | 0.8× | 2.4% | PASS |
| mem_reads | `oltp_sum_range` | 11.77ms | 10.41ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 2.76ms | 2.56ms | 0.9× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.52ms | 3.41ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.11ms | 4.47ms | 1.4× | 1.0% | PASS |
| mem_reads | `select_random_points` | 16.32ms | 15.83ms | 1.0× | 1.4% | PASS |
| mem_reads | `select_random_ranges` | 2.72ms | 4.15ms | 1.5× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 3.30ms | 3.40ms | 1.0× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 27.78ms | 28.34ms | 1.0× | 0.4% | PASS |
| mem_reads | `index_join` | 8.44ms | 6.91ms | 0.8× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 3.06ms | 4.73ms | 1.5× | 3.6% | PASS |
| mem_reads | `types_table_scan` | 869.31ms | 958.93ms | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 977.36ms | 1.04s | 1.1× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 105.33ms | 102.56ms | 1.0× | 0.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 177.63ms | 274.86ms | 1.5× | 0.6% | PASS |
| mem_writes | `oltp_insert` | 13.95ms | 30.09ms | 2.2× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 50.27ms | 110.78ms | 2.2× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 37.82ms | 65.90ms | 1.7× | 1.0% | PASS |
| mem_writes | `oltp_delete_insert` | 41.17ms | 82.13ms | 2.0× | 0.8% | PASS |
| mem_writes | `oltp_write_only` | 22.04ms | 49.89ms | 2.3× | 0.8% | PASS |
| mem_writes | `types_delete_insert` | 29.32ms | 41.96ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_read_write` | 72.54ms | 109.07ms | 1.5× | 0.8% | PASS |
| file_reads | `oltp_point_select` | 95.69ms | 44.03ms | 0.5× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 20.84ms | 12.36ms | 0.6× | 0.7% | PASS |
| file_reads | `oltp_sum_range` | 19.35ms | 12.10ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 3.60ms | 2.82ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_distinct_range` | 4.36ms | 3.68ms | 0.8× | 0.7% | PASS |
| file_reads | `oltp_index_scan` | 10.29ms | 6.55ms | 0.6× | 0.7% | PASS |
| file_reads | `select_random_points` | 24.20ms | 17.59ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_ranges` | 9.66ms | 5.89ms | 0.6× | 0.6% | PASS |
| file_reads | `covering_index_scan` | 10.55ms | 5.50ms | 0.5× | 0.8% | PASS |
| file_reads | `groupby_scan` | 28.80ms | 28.62ms | 1.0× | 0.4% | PASS |
| file_reads | `index_join` | 12.76ms | 8.65ms | 0.7× | 1.1% | PASS |
| file_reads | `index_join_scan` | 4.03ms | 4.87ms | 1.2× | 1.1% | PASS |
| file_reads | `types_table_scan` | 878.47ms | 961.02ms | 1.1× | 0.3% | PASS |
| file_reads | `table_scan` | 988.63ms | 1.04s | 1.1× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 205.77ms | 126.50ms | 0.6× | 0.5% | PASS |
| file_writes | `oltp_bulk_insert` | 242.78ms | 338.99ms | 1.4× | 1.9% | PASS |
| file_writes | `oltp_insert` | 31.20ms | 55.13ms | 1.8× | 5.0% | PASS |
| file_writes | `oltp_update_index` | 162.77ms | 190.81ms | 1.2× | 2.4% | PASS |
| file_writes | `oltp_update_non_index` | 136.71ms | 127.23ms | 0.9× | 3.1% | PASS |
| file_writes | `oltp_delete_insert` | 162.96ms | 150.30ms | 0.9× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 116.45ms | 104.67ms | 0.9× | 1.8% | PASS |
| file_writes | `types_delete_insert` | 123.14ms | 83.12ms | 0.7× | 11.3% | PASS |
| file_writes | `oltp_read_write` | 172.46ms | 164.30ms | 1.0× | 3.3% | PASS |
| ac_reads | `oltp_point_select` | 48.26ms | 43.94ms | 0.9× | 0.5% | PASS |
| ac_reads | `oltp_range_select` | 16.28ms | 12.40ms | 0.8× | 0.8% | PASS |
| ac_reads | `oltp_sum_range` | 15.02ms | 12.20ms | 0.8× | 1.4% | PASS |
| ac_reads | `oltp_order_range` | 3.17ms | 2.83ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_distinct_range` | 3.91ms | 3.67ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 5.75ms | 6.58ms | 1.1× | 0.8% | PASS |
| ac_reads | `select_random_points` | 19.64ms | 17.73ms | 0.9× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 5.00ms | 5.92ms | 1.2× | 0.7% | PASS |
| ac_reads | `covering_index_scan` | 5.88ms | 5.50ms | 0.9× | 0.7% | PASS |
| ac_reads | `groupby_scan` | 28.45ms | 28.66ms | 1.0× | 0.4% | PASS |
| ac_reads | `index_join` | 10.70ms | 8.69ms | 0.8× | 1.6% | PASS |
| ac_reads | `index_join_scan` | 3.66ms | 4.96ms | 1.4× | 1.7% | PASS |
| ac_reads | `types_table_scan` | 949.87ms | 1.01s | 1.1× | 5.4% | PASS |
| ac_reads | `table_scan` | 1.08s | 1.08s | 1.0× | 5.6% | PASS |
| ac_reads | `oltp_read_only` | 139.88ms | 127.05ms | 0.9× | 1.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.86ms | 46.70ms | 2.6× | 11.7% | PASS |
| ac_writes | `oltp_insert_ac` | 20.04ms | 59.14ms | 3.0× | 9.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.62ms | 66.61ms | 3.2× | 6.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.92ms | 54.99ms | 3.2× | 7.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 19.68ms | 61.85ms | 3.1× | 7.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.38ms | 62.01ms | 3.2× | 7.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 17.94ms | 56.11ms | 3.1× | 11.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 24.84ms | 66.26ms | 2.7× | 8.0% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 26.31ms | 29.72ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 16.00ms | 16.41ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 13.88ms | 15.98ms | 1.2× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 2.95ms | 3.12ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.64ms | 3.95ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_index_scan` | 3.56ms | 4.36ms | 1.2× | 1.5% | PASS |
| mem_reads | `select_random_points` | 19.69ms | 23.07ms | 1.2× | 1.7% | PASS |
| mem_reads | `select_random_ranges` | 5.76ms | 6.50ms | 1.1× | 1.4% | PASS |
| mem_reads | `covering_index_scan` | 3.30ms | 3.38ms | 1.0× | 2.9% | PASS |
| mem_reads | `groupby_scan` | 29.29ms | 32.47ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 6.21ms | 8.09ms | 1.3× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.32ms | 4.77ms | 1.4× | 1.5% | PASS |
| mem_reads | `types_table_scan` | 899.77ms | 986.42ms | 1.1× | 2.9% | PASS |
| mem_reads | `table_scan` | 1.07s | 1.06s | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_read_only` | 116.22ms | 129.04ms | 1.1× | 1.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 183.72ms | 264.58ms | 1.4× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 14.55ms | 27.64ms | 1.9× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 53.80ms | 103.97ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 41.62ms | 67.56ms | 1.6× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 39.80ms | 78.14ms | 2.0× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 22.02ms | 49.10ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 26.17ms | 41.84ms | 1.6× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 79.02ms | 123.61ms | 1.6× | 2.0% | PASS |
| file_reads | `oltp_point_select` | 94.90ms | 46.49ms | 0.5× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 23.22ms | 18.11ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_sum_range` | 21.20ms | 17.82ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 3.78ms | 3.34ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 4.59ms | 4.23ms | 0.9× | 0.8% | PASS |
| file_reads | `oltp_index_scan` | 10.90ms | 6.74ms | 0.6× | 1.0% | PASS |
| file_reads | `select_random_points` | 28.09ms | 25.49ms | 0.9× | 1.2% | PASS |
| file_reads | `select_random_ranges` | 13.00ms | 8.41ms | 0.6× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 10.61ms | 5.47ms | 0.5× | 1.0% | PASS |
| file_reads | `groupby_scan` | 30.28ms | 32.69ms | 1.1× | 0.8% | PASS |
| file_reads | `index_join` | 10.18ms | 9.57ms | 0.9× | 1.2% | PASS |
| file_reads | `index_join_scan` | 4.13ms | 4.95ms | 1.2× | 1.2% | PASS |
| file_reads | `types_table_scan` | 867.02ms | 963.97ms | 1.1× | 0.8% | PASS |
| file_reads | `table_scan` | 1.12s | 1.07s | 1.0× | 4.8% | PASS |
| file_reads | `oltp_read_only` | 225.83ms | 159.74ms | 0.7× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 250.66ms | 325.21ms | 1.3× | 11.7% | PASS |
| file_writes | `oltp_insert` | 32.22ms | 47.75ms | 1.5× | 19.4% | PASS |
| file_writes | `oltp_update_index` | 169.41ms | 170.62ms | 1.0× | 10.3% | PASS |
| file_writes | `oltp_update_non_index` | 140.59ms | 121.46ms | 0.9× | 10.9% | PASS |
| file_writes | `oltp_delete_insert` | 140.95ms | 136.89ms | 1.0× | 19.5% | PASS |
| file_writes | `oltp_write_only` | 107.27ms | 95.66ms | 0.9× | 20.0% | PASS |
| file_writes | `types_delete_insert` | 93.66ms | 83.16ms | 0.9× | 15.8% | PASS |
| file_writes | `oltp_read_write` | 158.25ms | 168.28ms | 1.1× | 6.1% | PASS |
| ac_reads | `oltp_point_select` | 47.15ms | 46.49ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 18.36ms | 18.11ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 16.10ms | 17.70ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_order_range` | 3.31ms | 3.33ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 4.11ms | 4.20ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.22ms | 6.70ms | 1.1× | 0.9% | PASS |
| ac_reads | `select_random_points` | 23.26ms | 25.50ms | 1.1× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 8.26ms | 8.40ms | 1.0× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 5.91ms | 5.44ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 29.75ms | 32.70ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 7.95ms | 9.70ms | 1.2× | 1.5% | PASS |
| ac_reads | `index_join_scan` | 3.76ms | 5.04ms | 1.3× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 883.19ms | 980.44ms | 1.1× | 1.9% | PASS |
| ac_reads | `table_scan` | 982.52ms | 1.04s | 1.1× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 143.35ms | 151.43ms | 1.1× | 0.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.94ms | 71.76ms | 3.0× | 37.7% | PASS |
| ac_writes | `oltp_insert_ac` | 23.88ms | 94.88ms | 4.0× | 40.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 34.57ms | 122.93ms | 3.6× | 56.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.03ms | 68.42ms | 3.1× | 27.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 29.43ms | 96.41ms | 3.3× | 61.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.24ms | 125.47ms | 5.2× | 70.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 29.38ms | 136.49ms | 4.6× | 49.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 37.05ms | 98.89ms | 2.7× | 31.8% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 32s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 57.07ms | 130.00ms | 43.9% | 0.5% | PASS |
| `status_dirty_many_tables` | 59.73ms | 130.00ms | 45.9% | 1.0% | PASS |
| `diff_regular_working_one_table` | 55.02ms | 120.00ms | 45.8% | 1.3% | PASS |
| `diff_regular_working_many_tables` | 61.44ms | 140.00ms | 43.9% | 0.7% | PASS |
| `diff_stat_working_many_tables` | 61.73ms | 140.00ms | 44.1% | 0.6% | PASS |
| `diff_schema_working_many_tables` | 62.66ms | 140.00ms | 44.8% | 1.7% | PASS |
| `branch_list_many_branches` | 13.85ms | 35.00ms | 39.6% | 1.4% | PASS |
| `branch_create_delete` | 15.87ms | 40.00ms | 39.7% | 2.6% | PASS |
| `checkout_branch_clean` | 99.87ms | 150.00ms | 66.6% | 12.8% | PASS |
| `merge_data_no_conflicts` | 25.16ms | 50.00ms | 50.3% | 1.9% | PASS |
| `merge_schema_no_conflicts` | 13.80ms | 35.00ms | 39.4% | 2.2% | PASS |
| `merge_data_conflicts` | 20.01ms | 180.00ms | 11.1% | 1.5% | PASS |
| `merge_data_conflicts_with_resolve` | 19.91ms | 180.00ms | 11.1% | 1.4% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
