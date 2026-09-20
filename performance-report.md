# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-20 15:16 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/35514116852)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.49s | 10.65s | 1.0× | 1.3% | **PASS** |
| Writes | 2.17s | 3.52s | 1.6× | 1.3% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.19s | 10.76s | 1.0× | 1.3% | **PASS** |
| Writes | 3.49s | 4.10s | 1.2× | 2.1% | **PASS** |
| Autocommit writes | 982.96ms | 3.24s | 3.3× | 6.2% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.48s | 2.60s | 1.0× | 1.2% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 3.10s | 2.93s | 0.9× | 2.6% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.76s | 2.82s | 1.0× | 1.5% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.14s | 2.30s | 1.1× | 1.0% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 435.04ms | 729.16ms | 1.7× | 1.2% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 663.62ms | 1.08s | 1.6× | 2.5% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 608.30ms | 1.01s | 1.7× | 2.3% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 461.33ms | 702.84ms | 1.5× | 0.8% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.70s | 2.66s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 3.12s | 2.88s | 0.9× | 1.9% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.98s | 2.87s | 1.0× | 1.3% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.38s | 2.35s | 1.0× | 0.9% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 620.33ms | 793.79ms | 1.3× | 1.9% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 991.19ms | 1.14s | 1.2× | 4.8% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 830.02ms | 1.09s | 1.3× | 1.9% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 1.04s | 1.07s | 1.0× | 2.9% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.54s | 2.66s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.94s | 2.87s | 1.0× | 2.2% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.69s | 2.82s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.22s | 2.35s | 1.1× | 0.6% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 307.87ms | 1.07s | 3.5× | 7.6% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 216.42ms | 738.29ms | 3.4× | 5.4% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 218.32ms | 757.87ms | 3.5× | 6.2% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 240.35ms | 671.71ms | 2.8× | 6.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.69ms | 29.59ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_range_select` | 10.21ms | 11.10ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 9.62ms | 11.18ms | 1.2× | 1.8% | PASS |
| mem_reads | `oltp_order_range` | 2.59ms | 2.80ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.66ms | 3.95ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.82ms | 5.21ms | 1.4× | 1.4% | PASS |
| mem_reads | `select_random_points` | 9.84ms | 10.96ms | 1.1× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 2.96ms | 3.95ms | 1.3× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.16ms | 4.27ms | 1.0× | 0.9% | PASS |
| mem_reads | `groupby_scan` | 30.36ms | 32.96ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 5.74ms | 7.92ms | 1.4× | 0.9% | PASS |
| mem_reads | `index_join_scan` | 3.23ms | 4.39ms | 1.4× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.13s | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.23s | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_read_only` | 104.36ms | 117.82ms | 1.1× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 179.70ms | 271.52ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 15.16ms | 28.17ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 49.03ms | 95.48ms | 1.9× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 34.24ms | 60.41ms | 1.8× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 45.21ms | 75.42ms | 1.7× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 21.80ms | 47.62ms | 2.2× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 23.93ms | 38.25ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_read_write` | 65.95ms | 112.28ms | 1.7× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 97.09ms | 48.73ms | 0.5× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 17.91ms | 13.30ms | 0.7× | 1.1% | PASS |
| file_reads | `oltp_sum_range` | 17.39ms | 13.29ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 3.51ms | 3.19ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 4.53ms | 4.28ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 11.64ms | 7.47ms | 0.6× | 1.6% | PASS |
| file_reads | `select_random_points` | 17.98ms | 13.08ms | 0.7× | 1.9% | PASS |
| file_reads | `select_random_ranges` | 10.41ms | 5.88ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 11.96ms | 6.45ms | 0.5× | 1.4% | PASS |
| file_reads | `groupby_scan` | 31.48ms | 33.41ms | 1.1× | 0.4% | PASS |
| file_reads | `index_join` | 10.24ms | 9.63ms | 0.9× | 2.0% | PASS |
| file_reads | `index_join_scan` | 4.19ms | 4.78ms | 1.1× | 1.7% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.19s | 1.22s | 1.0× | 0.7% | PASS |
| file_reads | `oltp_read_only` | 211.41ms | 146.39ms | 0.7× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 193.13ms | 280.71ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 25.03ms | 32.28ms | 1.3× | 1.5% | PASS |
| file_writes | `oltp_update_index` | 78.64ms | 106.24ms | 1.4× | 2.0% | PASS |
| file_writes | `oltp_update_non_index` | 62.59ms | 71.48ms | 1.1× | 2.0% | PASS |
| file_writes | `oltp_delete_insert` | 71.87ms | 83.28ms | 1.2× | 2.3% | PASS |
| file_writes | `oltp_write_only` | 50.34ms | 54.86ms | 1.1× | 1.8% | PASS |
| file_writes | `types_delete_insert` | 41.20ms | 44.37ms | 1.1× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 97.52ms | 120.57ms | 1.2× | 1.9% | PASS |
| ac_reads | `oltp_point_select` | 47.52ms | 48.92ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 13.00ms | 13.28ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 12.15ms | 13.28ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 3.05ms | 3.20ms | 1.1× | 2.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.08ms | 4.34ms | 1.1× | 2.2% | PASS |
| ac_reads | `oltp_index_scan` | 6.54ms | 7.50ms | 1.1× | 1.6% | PASS |
| ac_reads | `select_random_points` | 12.79ms | 13.14ms | 1.0× | 2.3% | PASS |
| ac_reads | `select_random_ranges` | 5.43ms | 5.88ms | 1.1× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 6.87ms | 6.46ms | 0.9× | 2.1% | PASS |
| ac_reads | `groupby_scan` | 30.91ms | 33.44ms | 1.1× | 0.5% | PASS |
| ac_reads | `index_join` | 7.34ms | 9.60ms | 1.3× | 1.9% | PASS |
| ac_reads | `index_join_scan` | 3.62ms | 4.78ms | 1.3× | 1.7% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 1.19s | 1.22s | 1.0× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 138.03ms | 146.63ms | 1.1× | 0.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 36.58ms | 119.47ms | 3.3× | 8.7% | PASS |
| ac_writes | `oltp_insert_ac` | 38.37ms | 130.91ms | 3.4× | 9.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 40.14ms | 148.20ms | 3.7× | 7.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 36.87ms | 131.23ms | 3.6× | 7.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 40.03ms | 142.51ms | 3.6× | 5.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 37.01ms | 132.69ms | 3.6× | 5.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 35.51ms | 125.78ms | 3.5× | 7.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 43.37ms | 139.69ms | 3.2× | 8.9% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.57ms | 39.29ms | 1.0× | 3.5% | PASS |
| mem_reads | `oltp_range_select` | 17.65ms | 14.90ms | 0.8× | 2.7% | PASS |
| mem_reads | `oltp_sum_range` | 16.29ms | 14.12ms | 0.9× | 2.6% | PASS |
| mem_reads | `oltp_order_range` | 3.59ms | 3.27ms | 0.9× | 2.7% | PASS |
| mem_reads | `oltp_distinct_range` | 4.48ms | 4.33ms | 1.0× | 1.5% | PASS |
| mem_reads | `oltp_index_scan` | 4.14ms | 6.50ms | 1.6× | 3.1% | PASS |
| mem_reads | `select_random_points` | 24.57ms | 22.02ms | 0.9× | 3.9% | PASS |
| mem_reads | `select_random_ranges` | 3.69ms | 5.41ms | 1.5× | 2.2% | PASS |
| mem_reads | `covering_index_scan` | 4.28ms | 4.90ms | 1.1× | 1.5% | PASS |
| mem_reads | `groupby_scan` | 35.59ms | 35.14ms | 1.0× | 1.1% | PASS |
| mem_reads | `index_join` | 11.04ms | 9.90ms | 0.9× | 3.4% | PASS |
| mem_reads | `index_join_scan` | 3.95ms | 5.55ms | 1.4× | 5.0% | PASS |
| mem_reads | `types_table_scan` | 1.29s | 1.24s | 1.0× | 1.4% | PASS |
| mem_reads | `table_scan` | 1.49s | 1.38s | 0.9× | 0.9% | PASS |
| mem_reads | `oltp_read_only` | 152.70ms | 142.39ms | 0.9× | 2.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 247.39ms | 379.33ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 18.54ms | 39.74ms | 2.1× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 72.46ms | 151.46ms | 2.1× | 2.5% | PASS |
| mem_writes | `oltp_update_non_index` | 52.85ms | 90.32ms | 1.7× | 2.6% | PASS |
| mem_writes | `oltp_delete_insert` | 59.05ms | 110.50ms | 1.9× | 3.3% | PASS |
| mem_writes | `oltp_write_only` | 35.61ms | 71.31ms | 2.0× | 3.0% | PASS |
| mem_writes | `types_delete_insert` | 46.70ms | 61.98ms | 1.3× | 2.0% | PASS |
| mem_writes | `oltp_read_write` | 131.03ms | 174.87ms | 1.3× | 2.5% | PASS |
| file_reads | `oltp_point_select` | 114.62ms | 59.21ms | 0.5× | 1.5% | PASS |
| file_reads | `oltp_range_select` | 25.96ms | 16.94ms | 0.7× | 2.9% | PASS |
| file_reads | `oltp_sum_range` | 24.52ms | 16.28ms | 0.7× | 2.6% | PASS |
| file_reads | `oltp_order_range` | 4.30ms | 3.51ms | 0.8× | 2.9% | PASS |
| file_reads | `oltp_distinct_range` | 5.45ms | 4.66ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 12.01ms | 8.56ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 33.09ms | 24.53ms | 0.7× | 2.5% | PASS |
| file_reads | `select_random_ranges` | 11.33ms | 7.37ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 12.12ms | 6.95ms | 0.6× | 1.8% | PASS |
| file_reads | `groupby_scan` | 36.19ms | 35.33ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 15.55ms | 11.03ms | 0.7× | 2.4% | PASS |
| file_reads | `index_join_scan` | 4.96ms | 5.84ms | 1.2× | 3.5% | PASS |
| file_reads | `types_table_scan` | 1.17s | 1.20s | 1.0× | 1.8% | PASS |
| file_reads | `table_scan` | 1.39s | 1.31s | 0.9× | 1.6% | PASS |
| file_reads | `oltp_read_only` | 269.25ms | 174.75ms | 0.6× | 1.9% | PASS |
| file_writes | `oltp_bulk_insert` | 271.41ms | 392.63ms | 1.4× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.84ms | 46.98ms | 1.8× | 2.1% | PASS |
| file_writes | `oltp_update_index` | 135.72ms | 169.43ms | 1.2× | 7.0% | PASS |
| file_writes | `oltp_update_non_index` | 117.69ms | 103.80ms | 0.9× | 9.5% | PASS |
| file_writes | `oltp_delete_insert` | 101.43ms | 123.61ms | 1.2× | 1.9% | PASS |
| file_writes | `oltp_write_only` | 84.38ms | 75.24ms | 0.9× | 9.6% | PASS |
| file_writes | `types_delete_insert` | 76.56ms | 65.03ms | 0.8× | 2.6% | PASS |
| file_writes | `oltp_read_write` | 177.16ms | 163.49ms | 0.9× | 7.2% | PASS |
| ac_reads | `oltp_point_select` | 63.37ms | 59.42ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 20.99ms | 17.20ms | 0.8× | 2.9% | PASS |
| ac_reads | `oltp_sum_range` | 19.38ms | 16.38ms | 0.8× | 2.1% | PASS |
| ac_reads | `oltp_order_range` | 3.84ms | 3.47ms | 0.9× | 2.7% | PASS |
| ac_reads | `oltp_distinct_range` | 4.89ms | 4.67ms | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_index_scan` | 7.06ms | 8.68ms | 1.2× | 2.3% | PASS |
| ac_reads | `select_random_points` | 28.64ms | 25.57ms | 0.9× | 4.5% | PASS |
| ac_reads | `select_random_ranges` | 6.40ms | 7.46ms | 1.2× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 7.21ms | 6.98ms | 1.0× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 35.67ms | 35.39ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 13.26ms | 11.31ms | 0.9× | 4.1% | PASS |
| ac_reads | `index_join_scan` | 4.39ms | 5.93ms | 1.4× | 4.5% | PASS |
| ac_reads | `types_table_scan` | 1.18s | 1.20s | 1.0× | 1.7% | PASS |
| ac_reads | `table_scan` | 1.36s | 1.30s | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 186.10ms | 171.14ms | 0.9× | 2.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.72ms | 76.86ms | 3.2× | 3.0% | PASS |
| ac_writes | `oltp_insert_ac` | 26.52ms | 88.08ms | 3.3× | 5.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.20ms | 101.09ms | 3.7× | 3.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.47ms | 86.29ms | 3.7× | 4.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.45ms | 94.20ms | 3.4× | 6.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.36ms | 95.51ms | 3.5× | 6.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 26.41ms | 94.10ms | 3.6× | 5.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.30ms | 102.16ms | 3.0× | 6.4% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 37.90ms | 38.89ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 16.86ms | 14.56ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 16.29ms | 13.93ms | 0.9× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 3.26ms | 3.13ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.39ms | 4.32ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 4.18ms | 6.40ms | 1.5× | 1.9% | PASS |
| mem_reads | `select_random_points` | 23.75ms | 21.50ms | 0.9× | 3.6% | PASS |
| mem_reads | `select_random_ranges` | 3.61ms | 5.36ms | 1.5× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.27ms | 4.83ms | 1.1× | 1.5% | PASS |
| mem_reads | `groupby_scan` | 35.30ms | 34.75ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 10.93ms | 9.79ms | 0.9× | 3.6% | PASS |
| mem_reads | `index_join_scan` | 3.86ms | 5.51ms | 1.4× | 2.0% | PASS |
| mem_reads | `types_table_scan` | 1.17s | 1.22s | 1.0× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.28s | 1.30s | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_read_only` | 144.92ms | 140.68ms | 1.0× | 1.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 246.77ms | 373.92ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 18.92ms | 39.01ms | 2.1× | 1.4% | PASS |
| mem_writes | `oltp_update_index` | 66.98ms | 142.71ms | 2.1× | 2.8% | PASS |
| mem_writes | `oltp_update_non_index` | 50.67ms | 86.00ms | 1.7× | 2.4% | PASS |
| mem_writes | `oltp_delete_insert` | 56.10ms | 106.66ms | 1.9× | 3.0% | PASS |
| mem_writes | `oltp_write_only` | 28.90ms | 62.38ms | 2.2× | 2.4% | PASS |
| mem_writes | `types_delete_insert` | 39.27ms | 53.92ms | 1.4× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 100.70ms | 148.02ms | 1.5× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 114.06ms | 59.07ms | 0.5× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 24.89ms | 16.86ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 24.06ms | 16.05ms | 0.7× | 1.7% | PASS |
| file_reads | `oltp_order_range` | 4.34ms | 3.45ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 5.43ms | 4.65ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 11.76ms | 8.47ms | 0.7× | 1.1% | PASS |
| file_reads | `select_random_points` | 32.38ms | 24.28ms | 0.8× | 3.4% | PASS |
| file_reads | `select_random_ranges` | 11.31ms | 7.37ms | 0.7× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 12.10ms | 6.93ms | 0.6× | 1.0% | PASS |
| file_reads | `groupby_scan` | 36.31ms | 35.07ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 15.02ms | 10.84ms | 0.7× | 1.7% | PASS |
| file_reads | `index_join_scan` | 4.95ms | 5.81ms | 1.2× | 2.4% | PASS |
| file_reads | `types_table_scan` | 1.12s | 1.19s | 1.1× | 2.1% | PASS |
| file_reads | `table_scan` | 1.31s | 1.31s | 1.0× | 1.0% | PASS |
| file_reads | `oltp_read_only` | 252.07ms | 168.57ms | 0.7× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 268.49ms | 384.16ms | 1.4× | 1.2% | PASS |
| file_writes | `oltp_insert` | 26.15ms | 46.02ms | 1.8× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 100.64ms | 157.08ms | 1.6× | 1.7% | PASS |
| file_writes | `oltp_update_non_index` | 90.19ms | 98.72ms | 1.1× | 7.6% | PASS |
| file_writes | `oltp_delete_insert` | 90.60ms | 118.05ms | 1.3× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 58.96ms | 72.17ms | 1.2× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 65.29ms | 63.01ms | 1.0× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 129.70ms | 155.28ms | 1.2× | 2.1% | PASS |
| ac_reads | `oltp_point_select` | 60.95ms | 58.60ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_range_select` | 19.82ms | 16.77ms | 0.8× | 1.5% | PASS |
| ac_reads | `oltp_sum_range` | 18.93ms | 15.93ms | 0.8× | 1.5% | PASS |
| ac_reads | `oltp_order_range` | 3.82ms | 3.42ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_distinct_range` | 4.92ms | 4.64ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.83ms | 8.53ms | 1.2× | 1.4% | PASS |
| ac_reads | `select_random_points` | 25.62ms | 23.98ms | 0.9× | 2.7% | PASS |
| ac_reads | `select_random_ranges` | 6.17ms | 7.37ms | 1.2× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 7.14ms | 6.87ms | 1.0× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 35.82ms | 35.09ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 12.78ms | 10.97ms | 0.9× | 2.1% | PASS |
| ac_reads | `index_join_scan` | 4.29ms | 5.77ms | 1.3× | 1.9% | PASS |
| ac_reads | `types_table_scan` | 1.09s | 1.17s | 1.1× | 1.4% | PASS |
| ac_reads | `table_scan` | 1.22s | 1.28s | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_read_only` | 179.33ms | 169.41ms | 0.9× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.56ms | 78.91ms | 3.2× | 5.7% | PASS |
| ac_writes | `oltp_insert_ac` | 26.32ms | 93.73ms | 3.6× | 6.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.14ms | 104.17ms | 3.6× | 8.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.97ms | 85.50ms | 3.6× | 7.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.74ms | 97.94ms | 3.5× | 6.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.91ms | 98.47ms | 3.7× | 4.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.20ms | 93.38ms | 3.7× | 6.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.48ms | 105.78ms | 3.1× | 4.7% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 25.75ms | 29.35ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_range_select` | 15.31ms | 16.05ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_sum_range` | 13.95ms | 15.39ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 2.90ms | 3.03ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.75ms | 3.92ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.60ms | 4.40ms | 1.2× | 1.1% | PASS |
| mem_reads | `select_random_points` | 21.12ms | 23.59ms | 1.1× | 1.2% | PASS |
| mem_reads | `select_random_ranges` | 5.88ms | 6.45ms | 1.1× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 3.34ms | 3.47ms | 1.0× | 2.2% | PASS |
| mem_reads | `groupby_scan` | 30.57ms | 32.69ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 6.20ms | 7.86ms | 1.3× | 1.3% | PASS |
| mem_reads | `index_join_scan` | 3.21ms | 4.67ms | 1.5× | 2.3% | PASS |
| mem_reads | `types_table_scan` | 878.74ms | 961.74ms | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.01s | 1.06s | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 116.56ms | 128.60ms | 1.1× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 193.70ms | 256.50ms | 1.3× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 15.06ms | 25.61ms | 1.7× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 51.58ms | 93.52ms | 1.8× | 0.6% | PASS |
| mem_writes | `oltp_update_non_index` | 40.16ms | 60.28ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_delete_insert` | 39.14ms | 70.65ms | 1.8× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 21.04ms | 42.33ms | 2.0× | 0.7% | PASS |
| mem_writes | `types_delete_insert` | 25.04ms | 37.76ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 75.61ms | 116.18ms | 1.5× | 0.6% | PASS |
| file_reads | `oltp_point_select` | 100.96ms | 47.90ms | 0.5× | 0.5% | PASS |
| file_reads | `oltp_range_select` | 23.33ms | 18.23ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 22.11ms | 17.51ms | 0.8× | 0.8% | PASS |
| file_reads | `oltp_order_range` | 3.69ms | 3.33ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_distinct_range` | 4.59ms | 4.23ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 11.31ms | 6.85ms | 0.6× | 0.9% | PASS |
| file_reads | `select_random_points` | 29.21ms | 25.85ms | 0.9× | 0.9% | PASS |
| file_reads | `select_random_ranges` | 13.56ms | 8.56ms | 0.6× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 11.19ms | 5.61ms | 0.5× | 0.8% | PASS |
| file_reads | `groupby_scan` | 30.91ms | 32.98ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 10.37ms | 9.63ms | 0.9× | 0.9% | PASS |
| file_reads | `index_join_scan` | 4.03ms | 4.85ms | 1.2× | 0.8% | PASS |
| file_reads | `types_table_scan` | 879.36ms | 955.68ms | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.01s | 1.05s | 1.0× | 0.3% | PASS |
| file_reads | `oltp_read_only` | 224.81ms | 154.23ms | 0.7× | 0.6% | PASS |
| file_writes | `oltp_bulk_insert` | 246.29ms | 311.68ms | 1.3× | 2.1% | PASS |
| file_writes | `oltp_insert` | 33.56ms | 43.38ms | 1.3× | 6.1% | PASS |
| file_writes | `oltp_update_index` | 161.14ms | 157.07ms | 1.0× | 2.3% | PASS |
| file_writes | `oltp_update_non_index` | 136.60ms | 112.14ms | 0.8× | 3.3% | PASS |
| file_writes | `oltp_delete_insert` | 132.96ms | 127.12ms | 1.0× | 2.4% | PASS |
| file_writes | `oltp_write_only` | 96.24ms | 87.62ms | 0.9× | 4.0% | PASS |
| file_writes | `types_delete_insert` | 86.20ms | 71.64ms | 0.8× | 7.2% | PASS |
| file_writes | `oltp_read_write` | 150.59ms | 163.00ms | 1.1× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 50.64ms | 47.94ms | 0.9× | 0.6% | PASS |
| ac_reads | `oltp_range_select` | 18.42ms | 18.19ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_sum_range` | 17.04ms | 17.59ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_order_range` | 3.33ms | 3.33ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.15ms | 4.22ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 6.52ms | 6.86ms | 1.1× | 0.6% | PASS |
| ac_reads | `select_random_points` | 24.48ms | 25.90ms | 1.1× | 0.5% | PASS |
| ac_reads | `select_random_ranges` | 8.63ms | 8.56ms | 1.0× | 0.7% | PASS |
| ac_reads | `covering_index_scan` | 6.24ms | 5.60ms | 0.9× | 0.6% | PASS |
| ac_reads | `groupby_scan` | 30.66ms | 32.92ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 7.92ms | 9.64ms | 1.2× | 0.6% | PASS |
| ac_reads | `index_join_scan` | 3.63ms | 4.86ms | 1.3× | 1.0% | PASS |
| ac_reads | `types_table_scan` | 878.92ms | 954.99ms | 1.1× | 0.4% | PASS |
| ac_reads | `table_scan` | 1.01s | 1.05s | 1.0× | 0.3% | PASS |
| ac_reads | `oltp_read_only` | 152.31ms | 154.62ms | 1.0× | 0.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 27.90ms | 70.47ms | 2.5× | 6.4% | PASS |
| ac_writes | `oltp_insert_ac` | 29.70ms | 84.28ms | 2.8× | 5.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 31.53ms | 92.32ms | 2.9× | 5.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 28.29ms | 80.67ms | 2.9× | 8.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 29.84ms | 88.04ms | 3.0× | 7.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 29.85ms | 86.55ms | 2.9× | 6.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 27.38ms | 76.69ms | 2.8× | 5.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 35.86ms | 92.68ms | 2.6× | 6.2% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 12s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 95.06ms | 130.00ms | 73.1% | 0.3% | PASS |
| `status_dirty_many_tables` | 98.68ms | 130.00ms | 75.9% | 0.3% | PASS |
| `diff_regular_working_one_table` | 90.57ms | 120.00ms | 75.5% | 0.3% | PASS |
| `diff_regular_working_many_tables` | 103.89ms | 140.00ms | 74.2% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 103.80ms | 140.00ms | 74.1% | 0.5% | PASS |
| `diff_schema_working_many_tables` | 104.87ms | 140.00ms | 74.9% | 0.4% | PASS |
| `branch_list_many_branches` | 23.14ms | 35.00ms | 66.1% | 1.2% | PASS |
| `branch_create_delete` | 26.03ms | 40.00ms | 65.1% | 1.4% | PASS |
| `checkout_branch_clean` | 61.54ms | 150.00ms | 41.0% | 0.7% | PASS |
| `merge_data_no_conflicts` | 29.32ms | 50.00ms | 58.6% | 1.5% | PASS |
| `merge_schema_no_conflicts` | 22.21ms | 35.00ms | 63.5% | 1.5% | PASS |
| `merge_data_conflicts` | 32.39ms | 180.00ms | 18.0% | 0.9% | PASS |
| `merge_data_conflicts_with_resolve` | 32.64ms | 180.00ms | 18.1% | 1.1% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
