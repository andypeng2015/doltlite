# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-23 15:54 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/35873292851)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.56s | 9.70s | 1.0× | 1.5% | **PASS** |
| Writes | 1.87s | 3.08s | 1.6× | 1.2% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.15s | 9.79s | 1.0× | 1.4% | **PASS** |
| Writes | 3.60s | 3.97s | 1.1× | 4.7% | **PASS** |
| Autocommit writes | 893.73ms | 2.62s | 2.9× | 5.4% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.00s | 2.11s | 1.1× | 1.0% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.67s | 2.76s | 1.0× | 2.2% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.77s | 2.92s | 1.1× | 1.4% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.11s | 1.92s | 0.9× | 2.3% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 308.75ms | 495.50ms | 1.6× | 0.9% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 586.38ms | 999.23ms | 1.7× | 1.2% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 593.10ms | 978.53ms | 1.6× | 1.2% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 385.58ms | 603.16ms | 1.6× | 1.9% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.08s | 2.12s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.03s | 2.84s | 0.9× | 1.6% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.01s | 2.94s | 1.0× | 1.0% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.03s | 1.88s | 0.9× | 2.2% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 901.51ms | 876.29ms | 1.0× | 17.6% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 975.40ms | 1.13s | 1.2× | 3.8% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 816.62ms | 1.06s | 1.3× | 1.5% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 902.70ms | 902.46ms | 1.0× | 4.2% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.03s | 2.12s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.96s | 2.86s | 1.0× | 2.1% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.81s | 2.94s | 1.0× | 1.0% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 1.82s | 1.87s | 1.0× | 1.8% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 321.11ms | 938.29ms | 2.9× | 63.0% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 209.71ms | 630.01ms | 3.0× | 5.1% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 153.46ms | 504.50ms | 3.3× | 3.6% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 209.44ms | 548.52ms | 2.6× | 4.6% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 18.31ms | 20.26ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_range_select` | 8.08ms | 8.59ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 7.49ms | 8.30ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 2.13ms | 2.24ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 2.90ms | 3.14ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.02ms | 3.58ms | 1.2× | 1.0% | PASS |
| mem_reads | `select_random_points` | 8.58ms | 8.87ms | 1.0× | 1.5% | PASS |
| mem_reads | `select_random_ranges` | 2.29ms | 2.84ms | 1.2× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 2.93ms | 2.94ms | 1.0× | 0.6% | PASS |
| mem_reads | `groupby_scan` | 24.84ms | 26.38ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 4.43ms | 6.08ms | 1.4× | 0.4% | PASS |
| mem_reads | `index_join_scan` | 2.40ms | 3.58ms | 1.5× | 1.2% | PASS |
| mem_reads | `types_table_scan` | 859.29ms | 913.12ms | 1.1× | 0.3% | PASS |
| mem_reads | `table_scan` | 975.98ms | 1.01s | 1.0× | 0.3% | PASS |
| mem_reads | `oltp_read_only` | 78.34ms | 88.12ms | 1.1× | 0.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 122.54ms | 180.08ms | 1.5× | 0.4% | PASS |
| mem_writes | `oltp_insert` | 10.95ms | 19.21ms | 1.8× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 36.88ms | 68.91ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 24.51ms | 41.21ms | 1.7× | 0.8% | PASS |
| mem_writes | `oltp_delete_insert` | 33.49ms | 50.79ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 15.97ms | 31.94ms | 2.0× | 0.7% | PASS |
| mem_writes | `types_delete_insert` | 17.43ms | 26.07ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_read_write` | 46.98ms | 77.28ms | 1.6× | 1.0% | PASS |
| file_reads | `oltp_point_select` | 43.38ms | 26.07ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 11.09ms | 9.56ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 10.53ms | 9.23ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_order_range` | 2.48ms | 2.42ms | 1.0× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 3.24ms | 3.22ms | 1.0× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 5.72ms | 4.36ms | 0.8× | 1.7% | PASS |
| file_reads | `select_random_points` | 11.48ms | 9.85ms | 0.9× | 1.6% | PASS |
| file_reads | `select_random_ranges` | 4.89ms | 3.52ms | 0.7× | 1.8% | PASS |
| file_reads | `covering_index_scan` | 5.70ms | 3.66ms | 0.6× | 1.4% | PASS |
| file_reads | `groupby_scan` | 24.85ms | 26.06ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 6.00ms | 6.59ms | 1.1× | 1.2% | PASS |
| file_reads | `index_join_scan` | 2.78ms | 3.77ms | 1.4× | 1.2% | PASS |
| file_reads | `types_table_scan` | 860.19ms | 908.70ms | 1.1× | 0.3% | PASS |
| file_reads | `table_scan` | 976.58ms | 1.01s | 1.0× | 0.3% | PASS |
| file_reads | `oltp_read_only` | 114.78ms | 96.23ms | 0.8× | 0.5% | PASS |
| file_writes | `oltp_bulk_insert` | 205.31ms | 255.36ms | 1.2× | 24.0% | PASS |
| file_writes | `oltp_insert` | 23.45ms | 33.68ms | 1.4× | 12.6% | PASS |
| file_writes | `oltp_update_index` | 134.98ms | 124.73ms | 0.9× | 11.6% | PASS |
| file_writes | `oltp_update_non_index` | 119.43ms | 87.34ms | 0.7× | 17.5% | PASS |
| file_writes | `oltp_delete_insert` | 128.12ms | 105.56ms | 0.8× | 17.7% | PASS |
| file_writes | `oltp_write_only` | 85.53ms | 73.42ms | 0.9× | 13.9% | PASS |
| file_writes | `types_delete_insert` | 71.40ms | 65.47ms | 0.9× | 18.1% | PASS |
| file_writes | `oltp_read_write` | 133.28ms | 130.74ms | 1.0× | 18.0% | PASS |
| ac_reads | `oltp_point_select` | 26.07ms | 26.04ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 9.16ms | 9.42ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 8.77ms | 9.12ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 2.28ms | 2.38ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 3.07ms | 3.23ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 4.05ms | 4.36ms | 1.1× | 3.1% | PASS |
| ac_reads | `select_random_points` | 9.77ms | 9.84ms | 1.0× | 2.2% | PASS |
| ac_reads | `select_random_ranges` | 3.22ms | 3.54ms | 1.1× | 1.8% | PASS |
| ac_reads | `covering_index_scan` | 3.99ms | 3.72ms | 0.9× | 2.0% | PASS |
| ac_reads | `groupby_scan` | 24.75ms | 26.05ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 5.15ms | 6.58ms | 1.3× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 2.66ms | 3.78ms | 1.4× | 1.3% | PASS |
| ac_reads | `types_table_scan` | 861.05ms | 908.20ms | 1.1× | 0.3% | PASS |
| ac_reads | `table_scan` | 975.61ms | 1.01s | 1.0× | 0.3% | PASS |
| ac_reads | `oltp_read_only` | 89.45ms | 96.08ms | 1.1× | 0.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 44.45ms | 120.12ms | 2.7× | 54.8% | PASS |
| ac_writes | `oltp_insert_ac` | 35.30ms | 81.29ms | 2.3× | 34.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 60.76ms | 137.80ms | 2.3× | 62.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 47.96ms | 134.82ms | 2.8× | 73.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 39.71ms | 124.85ms | 3.1× | 53.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.64ms | 167.26ms | 5.8× | 71.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 27.31ms | 75.20ms | 2.8× | 65.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 36.97ms | 96.93ms | 2.6× | 63.8% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.13ms | 38.08ms | 1.1× | 2.2% | PASS |
| mem_reads | `oltp_range_select` | 16.67ms | 14.04ms | 0.8× | 2.9% | PASS |
| mem_reads | `oltp_sum_range` | 15.95ms | 13.89ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 3.25ms | 3.12ms | 1.0× | 1.6% | PASS |
| mem_reads | `oltp_distinct_range` | 4.33ms | 4.27ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 4.07ms | 6.24ms | 1.5× | 1.5% | PASS |
| mem_reads | `select_random_points` | 23.56ms | 21.51ms | 0.9× | 4.3% | PASS |
| mem_reads | `select_random_ranges` | 3.67ms | 5.31ms | 1.4× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.68ms | 1.1× | 3.4% | PASS |
| mem_reads | `groupby_scan` | 35.11ms | 34.82ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 10.70ms | 9.37ms | 0.9× | 2.6% | PASS |
| mem_reads | `index_join_scan` | 3.82ms | 5.45ms | 1.4× | 3.0% | PASS |
| mem_reads | `types_table_scan` | 1.16s | 1.19s | 1.0× | 3.6% | PASS |
| mem_reads | `table_scan` | 1.22s | 1.27s | 1.0× | 0.4% | PASS |
| mem_reads | `oltp_read_only` | 132.65ms | 135.83ms | 1.0× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 244.32ms | 370.92ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 18.10ms | 38.48ms | 2.1× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 64.40ms | 140.69ms | 2.2× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 47.06ms | 85.17ms | 1.8× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 52.71ms | 103.36ms | 2.0× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 26.86ms | 60.91ms | 2.3× | 1.0% | PASS |
| mem_writes | `types_delete_insert` | 37.66ms | 54.05ms | 1.4× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 95.27ms | 145.65ms | 1.5× | 1.5% | PASS |
| file_reads | `oltp_point_select` | 111.85ms | 58.47ms | 0.5× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 25.48ms | 16.58ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 24.40ms | 16.50ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 4.44ms | 3.67ms | 0.8× | 2.6% | PASS |
| file_reads | `oltp_distinct_range` | 5.67ms | 4.91ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 12.23ms | 8.72ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 35.11ms | 25.30ms | 0.7× | 3.7% | PASS |
| file_reads | `select_random_ranges` | 11.68ms | 7.51ms | 0.6× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 12.55ms | 6.95ms | 0.6× | 1.4% | PASS |
| file_reads | `groupby_scan` | 36.73ms | 35.60ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 16.18ms | 11.21ms | 0.7× | 2.1% | PASS |
| file_reads | `index_join_scan` | 4.93ms | 5.90ms | 1.2× | 1.5% | PASS |
| file_reads | `types_table_scan` | 1.13s | 1.18s | 1.0× | 2.5% | PASS |
| file_reads | `table_scan` | 1.34s | 1.29s | 1.0× | 2.8% | PASS |
| file_reads | `oltp_read_only` | 256.55ms | 168.48ms | 0.7× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 270.07ms | 387.73ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 26.43ms | 46.53ms | 1.8× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 137.06ms | 165.74ms | 1.2× | 5.2% | PASS |
| file_writes | `oltp_update_non_index` | 115.00ms | 103.31ms | 0.9× | 13.4% | PASS |
| file_writes | `oltp_delete_insert` | 100.64ms | 123.54ms | 1.2× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 83.55ms | 75.60ms | 0.9× | 8.4% | PASS |
| file_writes | `types_delete_insert` | 74.72ms | 64.92ms | 0.9× | 2.4% | PASS |
| file_writes | `oltp_read_write` | 167.94ms | 161.87ms | 1.0× | 8.7% | PASS |
| ac_reads | `oltp_point_select` | 62.54ms | 58.62ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_range_select` | 20.74ms | 16.41ms | 0.8× | 2.3% | PASS |
| ac_reads | `oltp_sum_range` | 19.24ms | 16.30ms | 0.8× | 2.1% | PASS |
| ac_reads | `oltp_order_range` | 3.91ms | 3.61ms | 0.9× | 2.6% | PASS |
| ac_reads | `oltp_distinct_range` | 5.04ms | 4.79ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_index_scan` | 7.06ms | 8.67ms | 1.2× | 1.0% | PASS |
| ac_reads | `select_random_points` | 27.42ms | 25.11ms | 0.9× | 3.1% | PASS |
| ac_reads | `select_random_ranges` | 6.36ms | 7.45ms | 1.2× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 7.30ms | 6.92ms | 0.9× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 35.71ms | 35.24ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 12.97ms | 11.05ms | 0.9× | 2.1% | PASS |
| ac_reads | `index_join_scan` | 4.37ms | 5.99ms | 1.4× | 2.5% | PASS |
| ac_reads | `types_table_scan` | 1.18s | 1.19s | 1.0× | 4.1% | PASS |
| ac_reads | `table_scan` | 1.39s | 1.30s | 0.9× | 3.1% | PASS |
| ac_reads | `oltp_read_only` | 180.37ms | 169.47ms | 0.9× | 1.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.75ms | 63.46ms | 2.7× | 4.1% | PASS |
| ac_writes | `oltp_insert_ac` | 26.58ms | 76.97ms | 2.9× | 6.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.66ms | 90.37ms | 3.3× | 6.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.54ms | 71.19ms | 3.2× | 4.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.04ms | 82.16ms | 3.0× | 4.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.71ms | 81.50ms | 3.2× | 5.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.87ms | 76.32ms | 3.2× | 9.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.55ms | 88.05ms | 2.7× | 4.8% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.77ms | 35.78ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 17.13ms | 13.69ms | 0.8× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 15.75ms | 13.30ms | 0.8× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 3.47ms | 3.21ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.51ms | 4.36ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.04ms | 5.78ms | 1.4× | 2.2% | PASS |
| mem_reads | `select_random_points` | 21.19ms | 19.92ms | 0.9× | 1.5% | PASS |
| mem_reads | `select_random_ranges` | 3.54ms | 5.37ms | 1.5× | 0.9% | PASS |
| mem_reads | `covering_index_scan` | 4.30ms | 4.76ms | 1.1× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 37.05ms | 37.00ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 10.30ms | 8.59ms | 0.8× | 0.9% | PASS |
| mem_reads | `index_join_scan` | 3.81ms | 5.55ms | 1.5× | 1.2% | PASS |
| mem_reads | `types_table_scan` | 1.14s | 1.24s | 1.1× | 1.1% | PASS |
| mem_reads | `table_scan` | 1.33s | 1.39s | 1.0× | 2.9% | PASS |
| mem_reads | `oltp_read_only` | 140.19ms | 132.74ms | 0.9× | 2.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 246.80ms | 351.42ms | 1.4× | 1.7% | PASS |
| mem_writes | `oltp_insert` | 18.91ms | 39.09ms | 2.1× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 67.65ms | 144.43ms | 2.1× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 48.51ms | 83.50ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_delete_insert` | 52.63ms | 104.64ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_write_only` | 28.07ms | 63.37ms | 2.3× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 37.56ms | 52.07ms | 1.4× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 92.96ms | 140.01ms | 1.5× | 0.9% | PASS |
| file_reads | `oltp_point_select` | 131.18ms | 58.65ms | 0.4× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 25.83ms | 16.01ms | 0.6× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 24.70ms | 15.64ms | 0.6× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 4.21ms | 3.50ms | 0.8× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 5.28ms | 4.64ms | 0.9× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 13.51ms | 8.46ms | 0.6× | 1.0% | PASS |
| file_reads | `select_random_points` | 30.36ms | 22.52ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_ranges` | 12.99ms | 7.72ms | 0.6× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 14.01ms | 7.24ms | 0.5× | 0.4% | PASS |
| file_reads | `groupby_scan` | 37.34ms | 37.35ms | 1.0× | 1.2% | PASS |
| file_reads | `index_join` | 15.25ms | 10.81ms | 0.7× | 1.4% | PASS |
| file_reads | `index_join_scan` | 4.78ms | 5.90ms | 1.2× | 1.4% | PASS |
| file_reads | `types_table_scan` | 1.13s | 1.23s | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 1.28s | 1.35s | 1.1× | 0.5% | PASS |
| file_reads | `oltp_read_only` | 276.12ms | 165.54ms | 0.6× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 267.20ms | 362.38ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 25.86ms | 45.59ms | 1.8× | 1.2% | PASS |
| file_writes | `oltp_update_index` | 97.38ms | 155.06ms | 1.6× | 1.7% | PASS |
| file_writes | `oltp_update_non_index` | 92.61ms | 96.68ms | 1.0× | 7.0% | PASS |
| file_writes | `oltp_delete_insert` | 87.80ms | 117.53ms | 1.3× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 57.86ms | 73.02ms | 1.3× | 1.9% | PASS |
| file_writes | `types_delete_insert` | 63.80ms | 61.03ms | 1.0× | 1.1% | PASS |
| file_writes | `oltp_read_write` | 124.11ms | 148.93ms | 1.2× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 67.10ms | 58.75ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 20.54ms | 16.00ms | 0.8× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 19.11ms | 15.66ms | 0.8× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 3.90ms | 3.50ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_distinct_range` | 4.90ms | 4.63ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 7.50ms | 8.47ms | 1.1× | 1.1% | PASS |
| ac_reads | `select_random_points` | 25.04ms | 22.52ms | 0.9× | 1.1% | PASS |
| ac_reads | `select_random_ranges` | 6.80ms | 7.71ms | 1.1× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 7.80ms | 7.25ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 36.95ms | 37.27ms | 1.0× | 1.1% | PASS |
| ac_reads | `index_join` | 12.83ms | 10.76ms | 0.8× | 1.1% | PASS |
| ac_reads | `index_join_scan` | 4.41ms | 5.87ms | 1.3× | 1.0% | PASS |
| ac_reads | `types_table_scan` | 1.13s | 1.23s | 1.1× | 0.4% | PASS |
| ac_reads | `table_scan` | 1.28s | 1.35s | 1.1× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 183.79ms | 165.57ms | 0.9× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 16.83ms | 47.55ms | 2.8× | 3.5% | PASS |
| ac_writes | `oltp_insert_ac` | 19.00ms | 63.84ms | 3.4× | 3.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.08ms | 73.51ms | 3.7× | 2.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.20ms | 57.30ms | 3.5× | 2.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 19.18ms | 66.47ms | 3.5× | 3.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 18.95ms | 65.65ms | 3.5× | 5.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 17.35ms | 57.84ms | 3.3× | 5.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.87ms | 72.34ms | 2.8× | 2.3% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 21.74ms | 24.39ms | 1.1× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 11.75ms | 11.53ms | 1.0× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 11.03ms | 11.63ms | 1.1× | 3.2% | PASS |
| mem_reads | `oltp_order_range` | 2.33ms | 2.31ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 2.84ms | 2.86ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.09ms | 4.15ms | 1.3× | 2.0% | PASS |
| mem_reads | `select_random_points` | 18.02ms | 19.56ms | 1.1× | 5.7% | PASS |
| mem_reads | `select_random_ranges` | 4.66ms | 5.20ms | 1.1× | 2.9% | PASS |
| mem_reads | `covering_index_scan` | 2.43ms | 2.70ms | 1.1× | 5.4% | PASS |
| mem_reads | `groupby_scan` | 21.08ms | 22.12ms | 1.0× | 1.3% | PASS |
| mem_reads | `index_join` | 4.97ms | 6.32ms | 1.3× | 3.6% | PASS |
| mem_reads | `index_join_scan` | 2.83ms | 4.42ms | 1.6× | 3.8% | PASS |
| mem_reads | `types_table_scan` | 827.06ms | 788.07ms | 1.0× | 2.2% | PASS |
| mem_reads | `table_scan` | 1.08s | 914.77ms | 0.9× | 2.1% | PASS |
| mem_reads | `oltp_read_only` | 97.72ms | 97.51ms | 1.0× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 149.68ms | 206.90ms | 1.4× | 1.5% | PASS |
| mem_writes | `oltp_insert` | 11.60ms | 21.35ms | 1.8× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 46.61ms | 87.00ms | 1.9× | 1.9% | PASS |
| mem_writes | `oltp_update_non_index` | 36.31ms | 54.71ms | 1.5× | 2.1% | PASS |
| mem_writes | `oltp_delete_insert` | 34.56ms | 64.43ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 18.75ms | 39.05ms | 2.1× | 2.0% | PASS |
| mem_writes | `types_delete_insert` | 22.59ms | 35.05ms | 1.6× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 65.49ms | 94.66ms | 1.4× | 3.9% | PASS |
| file_reads | `oltp_point_select` | 79.52ms | 39.35ms | 0.5× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 17.92ms | 13.11ms | 0.7× | 2.2% | PASS |
| file_reads | `oltp_sum_range` | 16.77ms | 12.96ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 2.99ms | 2.49ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 3.50ms | 3.02ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_index_scan` | 9.37ms | 5.91ms | 0.6× | 2.3% | PASS |
| file_reads | `select_random_points` | 24.26ms | 21.13ms | 0.9× | 2.5% | PASS |
| file_reads | `select_random_ranges` | 10.62ms | 6.91ms | 0.7× | 1.5% | PASS |
| file_reads | `covering_index_scan` | 8.91ms | 4.74ms | 0.5× | 1.7% | PASS |
| file_reads | `groupby_scan` | 21.95ms | 22.17ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join` | 8.56ms | 8.09ms | 0.9× | 2.7% | PASS |
| file_reads | `index_join_scan` | 3.66ms | 4.37ms | 1.2× | 4.7% | PASS |
| file_reads | `types_table_scan` | 745.54ms | 762.52ms | 1.0× | 2.8% | PASS |
| file_reads | `table_scan` | 906.02ms | 864.05ms | 1.0× | 5.5% | PASS |
| file_reads | `oltp_read_only` | 173.83ms | 113.29ms | 0.7× | 2.3% | PASS |
| file_writes | `oltp_bulk_insert` | 200.44ms | 250.28ms | 1.2× | 3.7% | PASS |
| file_writes | `oltp_insert` | 23.95ms | 34.65ms | 1.4× | 16.4% | PASS |
| file_writes | `oltp_update_index` | 142.24ms | 137.98ms | 1.0× | 5.8% | PASS |
| file_writes | `oltp_update_non_index` | 128.83ms | 99.52ms | 0.8× | 4.1% | PASS |
| file_writes | `oltp_delete_insert` | 124.62ms | 106.98ms | 0.9× | 3.9% | PASS |
| file_writes | `oltp_write_only` | 81.23ms | 79.43ms | 1.0× | 4.1% | PASS |
| file_writes | `types_delete_insert` | 76.27ms | 63.89ms | 0.8× | 5.5% | PASS |
| file_writes | `oltp_read_write` | 125.12ms | 129.73ms | 1.0× | 4.3% | PASS |
| ac_reads | `oltp_point_select` | 40.95ms | 39.55ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_range_select` | 14.50ms | 13.18ms | 0.9× | 2.7% | PASS |
| ac_reads | `oltp_sum_range` | 12.92ms | 13.01ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 2.59ms | 2.46ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_distinct_range` | 3.16ms | 3.01ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 5.48ms | 5.86ms | 1.1× | 1.8% | PASS |
| ac_reads | `select_random_points` | 19.73ms | 20.95ms | 1.1× | 2.2% | PASS |
| ac_reads | `select_random_ranges` | 6.90ms | 6.92ms | 1.0× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 4.98ms | 4.75ms | 1.0× | 1.8% | PASS |
| ac_reads | `groupby_scan` | 21.70ms | 22.41ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 6.85ms | 8.47ms | 1.2× | 3.5% | PASS |
| ac_reads | `index_join_scan` | 3.21ms | 4.36ms | 1.4× | 2.8% | PASS |
| ac_reads | `types_table_scan` | 723.74ms | 762.29ms | 1.1× | 1.6% | PASS |
| ac_reads | `table_scan` | 842.16ms | 854.53ms | 1.0× | 3.3% | PASS |
| ac_reads | `oltp_read_only` | 108.49ms | 110.25ms | 1.0× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.67ms | 56.62ms | 2.5× | 4.0% | PASS |
| ac_writes | `oltp_insert_ac` | 26.71ms | 67.71ms | 2.5× | 7.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.28ms | 75.82ms | 2.7× | 4.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.61ms | 65.59ms | 2.9× | 4.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.68ms | 71.79ms | 2.8× | 4.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.42ms | 72.53ms | 2.7× | 5.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 27.54ms | 61.95ms | 2.2× | 13.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.54ms | 76.52ms | 2.6× | 3.7% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 22s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 106.77ms | 130.00ms | 82.1% | 0.3% | PASS |
| `status_dirty_many_tables` | 110.60ms | 130.00ms | 85.1% | 0.2% | PASS |
| `diff_regular_working_one_table` | 102.22ms | 120.00ms | 85.2% | 0.2% | PASS |
| `diff_regular_working_many_tables` | 116.17ms | 140.00ms | 83.0% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 115.95ms | 140.00ms | 82.8% | 0.6% | PASS |
| `diff_schema_working_many_tables` | 117.20ms | 140.00ms | 83.7% | 0.5% | PASS |
| `branch_list_many_branches` | 24.78ms | 35.00ms | 70.8% | 1.5% | PASS |
| `branch_create_delete` | 26.98ms | 40.00ms | 67.5% | 1.7% | PASS |
| `checkout_branch_clean` | 65.53ms | 150.00ms | 43.7% | 1.0% | PASS |
| `merge_data_no_conflicts` | 31.16ms | 50.00ms | 62.3% | 0.9% | PASS |
| `merge_schema_no_conflicts` | 23.16ms | 35.00ms | 66.2% | 1.5% | PASS |
| `merge_data_conflicts` | 35.24ms | 180.00ms | 19.6% | 1.0% | PASS |
| `merge_data_conflicts_with_resolve` | 35.34ms | 180.00ms | 19.6% | 0.9% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
