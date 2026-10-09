# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-09 18:02 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261004.327.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/37959140353)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.65s | 11.08s | 1.0× | 1.7% | **PASS** |
| Writes | 2.24s | 3.73s | 1.7× | 1.3% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 11.59s | 11.31s | 1.0× | 1.4% | **PASS** |
| Writes | 3.20s | 4.11s | 1.3× | 1.8% | **PASS** |
| Autocommit writes | 828.03ms | 2.48s | 3.0× | 6.2% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.58s | 2.64s | 1.0× | 2.0% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.67s | 2.79s | 1.0× | 1.7% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.63s | 2.78s | 1.1× | 1.5% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.76s | 2.87s | 1.0× | 1.4% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 444.83ms | 736.08ms | 1.7× | 1.1% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 597.54ms | 1.02s | 1.7× | 1.4% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 605.74ms | 1.00s | 1.7× | 1.4% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 596.31ms | 977.75ms | 1.6× | 1.2% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.74s | 2.68s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.91s | 2.84s | 1.0× | 1.8% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.90s | 2.85s | 1.0× | 1.6% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.03s | 2.94s | 1.0× | 1.4% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 624.30ms | 819.11ms | 1.3× | 1.9% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 964.27ms | 1.14s | 1.2× | 4.1% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 833.20ms | 1.09s | 1.3× | 2.0% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 775.27ms | 1.06s | 1.4× | 1.5% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.57s | 2.68s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.79s | 2.86s | 1.0× | 1.8% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.74s | 2.85s | 1.0× | 1.8% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.85s | 2.94s | 1.0× | 1.2% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 200.00ms | 621.59ms | 3.1× | 6.6% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 210.56ms | 606.57ms | 2.9× | 6.4% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 207.41ms | 618.77ms | 3.0× | 6.4% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 210.06ms | 636.44ms | 3.0× | 5.8% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 25.48ms | 30.53ms | 1.2× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 10.81ms | 11.65ms | 1.1× | 2.5% | PASS |
| mem_reads | `oltp_sum_range` | 10.09ms | 11.30ms | 1.1× | 2.2% | PASS |
| mem_reads | `oltp_order_range` | 2.64ms | 2.85ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.71ms | 3.97ms | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 4.04ms | 5.55ms | 1.4× | 2.8% | PASS |
| mem_reads | `select_random_points` | 11.71ms | 11.54ms | 1.0× | 4.3% | PASS |
| mem_reads | `select_random_ranges` | 3.07ms | 4.01ms | 1.3× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 4.33ms | 4.68ms | 1.1× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 30.85ms | 33.25ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 5.88ms | 8.33ms | 1.4× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.27ms | 4.41ms | 1.4× | 2.2% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.13s | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.29s | 1.26s | 1.0× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 109.58ms | 120.61ms | 1.1× | 2.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 182.90ms | 276.72ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 15.44ms | 28.35ms | 1.8× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 50.35ms | 97.11ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 34.83ms | 60.25ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_delete_insert` | 45.96ms | 75.00ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 22.28ms | 47.43ms | 2.1× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 24.75ms | 38.40ms | 1.6× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 68.32ms | 112.81ms | 1.7× | 1.3% | PASS |
| file_reads | `oltp_point_select` | 98.90ms | 49.38ms | 0.5× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 18.44ms | 13.55ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_sum_range` | 17.66ms | 13.31ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.54ms | 3.17ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 4.56ms | 4.25ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 11.72ms | 7.44ms | 0.6× | 1.2% | PASS |
| file_reads | `select_random_points` | 18.16ms | 13.17ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 10.53ms | 5.98ms | 0.6× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 12.15ms | 6.45ms | 0.5× | 1.0% | PASS |
| file_reads | `groupby_scan` | 31.61ms | 33.34ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 10.40ms | 9.63ms | 0.9× | 1.9% | PASS |
| file_reads | `index_join_scan` | 4.30ms | 4.78ms | 1.1× | 1.4% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.14s | 1.1× | 0.8% | PASS |
| file_reads | `table_scan` | 1.21s | 1.23s | 1.0× | 1.6% | PASS |
| file_reads | `oltp_read_only` | 213.94ms | 147.81ms | 0.7× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 197.67ms | 287.72ms | 1.5× | 1.3% | PASS |
| file_writes | `oltp_insert` | 22.54ms | 32.58ms | 1.4× | 2.0% | PASS |
| file_writes | `oltp_update_index` | 78.63ms | 107.95ms | 1.4× | 1.7% | PASS |
| file_writes | `oltp_update_non_index` | 60.85ms | 72.50ms | 1.2× | 2.0% | PASS |
| file_writes | `oltp_delete_insert` | 71.25ms | 85.25ms | 1.2× | 2.2% | PASS |
| file_writes | `oltp_write_only` | 47.65ms | 56.56ms | 1.2× | 2.0% | PASS |
| file_writes | `types_delete_insert` | 41.93ms | 46.15ms | 1.1× | 1.4% | PASS |
| file_writes | `oltp_read_write` | 103.78ms | 130.38ms | 1.3× | 1.2% | PASS |
| ac_reads | `oltp_point_select` | 51.02ms | 51.46ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 14.10ms | 13.97ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 13.26ms | 13.77ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.11ms | 3.19ms | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_distinct_range` | 4.18ms | 4.33ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_index_scan` | 7.11ms | 7.92ms | 1.1× | 1.2% | PASS |
| ac_reads | `select_random_points` | 14.90ms | 15.10ms | 1.0× | 3.5% | PASS |
| ac_reads | `select_random_ranges` | 5.73ms | 6.06ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.45ms | 6.73ms | 0.9× | 1.8% | PASS |
| ac_reads | `groupby_scan` | 31.38ms | 33.75ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 7.87ms | 10.04ms | 1.3× | 2.4% | PASS |
| ac_reads | `index_join_scan` | 3.82ms | 4.92ms | 1.3× | 3.0% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.13s | 1.1× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.20s | 1.22s | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 146.11ms | 149.56ms | 1.0× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.02ms | 63.04ms | 2.9× | 8.4% | PASS |
| ac_writes | `oltp_insert_ac` | 25.32ms | 77.33ms | 3.1× | 5.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.95ms | 89.68ms | 3.1× | 6.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.89ms | 71.59ms | 3.1× | 8.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.73ms | 80.94ms | 3.3× | 6.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.87ms | 82.20ms | 3.3× | 6.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.82ms | 70.19ms | 3.2× | 5.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.38ms | 86.64ms | 2.9× | 4.4% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.70ms | 39.12ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 16.70ms | 14.25ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 15.53ms | 13.88ms | 0.9× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 3.28ms | 3.18ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.33ms | 4.28ms | 1.0× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 4.00ms | 6.29ms | 1.6× | 1.7% | PASS |
| mem_reads | `select_random_points` | 22.05ms | 21.57ms | 1.0× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 3.69ms | 5.35ms | 1.5× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.30ms | 4.73ms | 1.1× | 2.3% | PASS |
| mem_reads | `groupby_scan` | 34.82ms | 34.65ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 10.51ms | 9.29ms | 0.9× | 2.6% | PASS |
| mem_reads | `index_join_scan` | 3.76ms | 5.39ms | 1.4× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 1.12s | 1.19s | 1.1× | 1.6% | PASS |
| mem_reads | `table_scan` | 1.26s | 1.30s | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_read_only` | 137.51ms | 138.59ms | 1.0× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 245.19ms | 374.11ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 18.26ms | 38.93ms | 2.1× | 1.5% | PASS |
| mem_writes | `oltp_update_index` | 66.24ms | 144.71ms | 2.2× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 48.22ms | 87.19ms | 1.8× | 1.6% | PASS |
| mem_writes | `oltp_delete_insert` | 54.10ms | 105.01ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 28.17ms | 62.43ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 38.75ms | 54.69ms | 1.4× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 98.61ms | 149.41ms | 1.5× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 111.59ms | 59.31ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 24.56ms | 16.77ms | 0.7× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 23.74ms | 16.43ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.20ms | 3.50ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 5.32ms | 4.64ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 11.86ms | 8.72ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 31.17ms | 25.19ms | 0.8× | 2.7% | PASS |
| file_reads | `select_random_ranges` | 11.31ms | 7.42ms | 0.7× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 12.20ms | 6.96ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 35.96ms | 35.21ms | 1.0× | 1.1% | PASS |
| file_reads | `index_join` | 15.05ms | 11.26ms | 0.7× | 3.4% | PASS |
| file_reads | `index_join_scan` | 4.79ms | 5.84ms | 1.2× | 2.6% | PASS |
| file_reads | `types_table_scan` | 1.11s | 1.18s | 1.1× | 1.9% | PASS |
| file_reads | `table_scan` | 1.26s | 1.29s | 1.0× | 2.5% | PASS |
| file_reads | `oltp_read_only` | 252.15ms | 171.01ms | 0.7× | 1.4% | PASS |
| file_writes | `oltp_bulk_insert` | 271.05ms | 389.28ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 26.05ms | 46.48ms | 1.8× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 133.81ms | 165.88ms | 1.2× | 6.3% | PASS |
| file_writes | `oltp_update_non_index` | 99.64ms | 104.24ms | 1.0× | 12.6% | PASS |
| file_writes | `oltp_delete_insert` | 99.89ms | 124.45ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 89.56ms | 76.88ms | 0.9× | 9.7% | PASS |
| file_writes | `types_delete_insert` | 75.30ms | 65.79ms | 0.9× | 1.9% | PASS |
| file_writes | `oltp_read_write` | 168.99ms | 165.70ms | 1.0× | 8.8% | PASS |
| ac_reads | `oltp_point_select` | 61.37ms | 59.38ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_range_select` | 20.05ms | 16.68ms | 0.8× | 2.5% | PASS |
| ac_reads | `oltp_sum_range` | 19.11ms | 16.58ms | 0.9× | 2.2% | PASS |
| ac_reads | `oltp_order_range` | 3.85ms | 3.56ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_distinct_range` | 4.93ms | 4.69ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_index_scan` | 6.91ms | 8.71ms | 1.3× | 1.4% | PASS |
| ac_reads | `select_random_points` | 26.44ms | 25.28ms | 1.0× | 3.0% | PASS |
| ac_reads | `select_random_ranges` | 6.41ms | 7.43ms | 1.2× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 7.30ms | 7.00ms | 1.0× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 35.63ms | 35.17ms | 1.0× | 1.1% | PASS |
| ac_reads | `index_join` | 12.79ms | 11.36ms | 0.9× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 4.32ms | 5.92ms | 1.4× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 1.12s | 1.19s | 1.1× | 1.9% | PASS |
| ac_reads | `table_scan` | 1.28s | 1.30s | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 178.95ms | 171.39ms | 1.0× | 1.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.52ms | 61.14ms | 2.6× | 6.4% | PASS |
| ac_writes | `oltp_insert_ac` | 26.09ms | 73.13ms | 2.8× | 5.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.23ms | 86.67ms | 3.1× | 6.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.78ms | 69.95ms | 3.1× | 5.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.00ms | 78.09ms | 2.9× | 8.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.37ms | 79.49ms | 3.0× | 5.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.90ms | 72.68ms | 3.0× | 10.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.66ms | 85.42ms | 2.6× | 6.7% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 36.22ms | 38.56ms | 1.1× | 2.6% | PASS |
| mem_reads | `oltp_range_select` | 16.79ms | 14.07ms | 0.8× | 2.8% | PASS |
| mem_reads | `oltp_sum_range` | 15.54ms | 13.71ms | 0.9× | 1.8% | PASS |
| mem_reads | `oltp_order_range` | 3.26ms | 3.16ms | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 4.34ms | 4.29ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 4.08ms | 6.38ms | 1.6× | 1.5% | PASS |
| mem_reads | `select_random_points` | 22.53ms | 21.06ms | 0.9× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 3.58ms | 5.36ms | 1.5× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.24ms | 4.79ms | 1.1× | 2.4% | PASS |
| mem_reads | `groupby_scan` | 34.79ms | 34.67ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 10.65ms | 9.51ms | 0.9× | 2.8% | PASS |
| mem_reads | `index_join_scan` | 3.85ms | 5.52ms | 1.4× | 2.2% | PASS |
| mem_reads | `types_table_scan` | 1.10s | 1.19s | 1.1× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.24s | 1.29s | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_read_only` | 139.24ms | 138.03ms | 1.0× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 247.10ms | 371.39ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 18.79ms | 38.77ms | 2.1× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 66.40ms | 139.21ms | 2.1× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 49.78ms | 84.67ms | 1.7× | 2.1% | PASS |
| mem_writes | `oltp_delete_insert` | 54.23ms | 103.58ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 28.55ms | 61.80ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 38.71ms | 53.36ms | 1.4× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 102.17ms | 147.74ms | 1.4× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 113.08ms | 59.06ms | 0.5× | 1.4% | PASS |
| file_reads | `oltp_range_select` | 25.15ms | 16.42ms | 0.7× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 24.00ms | 16.12ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.35ms | 3.48ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 5.46ms | 4.65ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 11.80ms | 8.70ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 33.09ms | 24.50ms | 0.7× | 3.9% | PASS |
| file_reads | `select_random_ranges` | 11.23ms | 7.47ms | 0.7× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 12.14ms | 7.04ms | 0.6× | 2.2% | PASS |
| file_reads | `groupby_scan` | 35.94ms | 35.05ms | 1.0× | 0.6% | PASS |
| file_reads | `index_join` | 15.72ms | 11.33ms | 0.7× | 3.5% | PASS |
| file_reads | `index_join_scan` | 4.97ms | 5.88ms | 1.2× | 3.2% | PASS |
| file_reads | `types_table_scan` | 1.10s | 1.18s | 1.1× | 1.6% | PASS |
| file_reads | `table_scan` | 1.25s | 1.29s | 1.0× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 259.64ms | 171.20ms | 0.7× | 1.6% | PASS |
| file_writes | `oltp_bulk_insert` | 268.32ms | 382.54ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 26.27ms | 45.84ms | 1.7× | 2.0% | PASS |
| file_writes | `oltp_update_index` | 100.65ms | 156.18ms | 1.6× | 2.0% | PASS |
| file_writes | `oltp_update_non_index` | 85.71ms | 98.59ms | 1.2× | 6.2% | PASS |
| file_writes | `oltp_delete_insert` | 89.95ms | 116.93ms | 1.3× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 59.95ms | 72.27ms | 1.2× | 2.1% | PASS |
| file_writes | `types_delete_insert` | 66.30ms | 62.58ms | 0.9× | 2.1% | PASS |
| file_writes | `oltp_read_write` | 136.06ms | 158.70ms | 1.2× | 1.5% | PASS |
| ac_reads | `oltp_point_select` | 62.97ms | 59.17ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 20.49ms | 16.55ms | 0.8× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 18.93ms | 16.14ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_order_range` | 3.83ms | 3.48ms | 0.9× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.93ms | 4.63ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_index_scan` | 7.01ms | 8.76ms | 1.3× | 1.9% | PASS |
| ac_reads | `select_random_points` | 27.26ms | 24.46ms | 0.9× | 3.0% | PASS |
| ac_reads | `select_random_ranges` | 6.26ms | 7.49ms | 1.2× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 7.29ms | 7.01ms | 1.0× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 35.38ms | 35.09ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 12.98ms | 11.30ms | 0.9× | 3.5% | PASS |
| ac_reads | `index_join_scan` | 4.43ms | 5.97ms | 1.3× | 3.1% | PASS |
| ac_reads | `types_table_scan` | 1.10s | 1.18s | 1.1× | 1.4% | PASS |
| ac_reads | `table_scan` | 1.24s | 1.29s | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 181.69ms | 169.97ms | 0.9× | 2.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.65ms | 61.33ms | 2.7× | 7.5% | PASS |
| ac_writes | `oltp_insert_ac` | 26.31ms | 79.48ms | 3.0× | 7.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.57ms | 87.63ms | 3.2× | 6.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.62ms | 71.80ms | 3.2× | 5.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.01ms | 79.93ms | 3.1× | 5.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.30ms | 79.06ms | 3.1× | 6.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 24.72ms | 73.22ms | 3.0× | 7.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.23ms | 86.32ms | 2.7× | 4.9% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.56ms | 41.15ms | 1.2× | 1.9% | PASS |
| mem_reads | `oltp_range_select` | 19.96ms | 21.48ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 18.04ms | 20.78ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 3.59ms | 3.89ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.71ms | 5.13ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 4.53ms | 6.29ms | 1.4× | 1.9% | PASS |
| mem_reads | `select_random_points` | 28.62ms | 32.87ms | 1.1× | 1.5% | PASS |
| mem_reads | `select_random_ranges` | 7.80ms | 8.97ms | 1.1× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 4.19ms | 4.46ms | 1.1× | 2.3% | PASS |
| mem_reads | `groupby_scan` | 36.84ms | 39.85ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 7.96ms | 10.59ms | 1.3× | 1.9% | PASS |
| mem_reads | `index_join_scan` | 3.94ms | 5.66ms | 1.4× | 2.4% | PASS |
| mem_reads | `types_table_scan` | 1.10s | 1.20s | 1.1× | 1.0% | PASS |
| mem_reads | `table_scan` | 1.33s | 1.30s | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 154.09ms | 174.13ms | 1.1× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 245.54ms | 352.87ms | 1.4× | 1.3% | PASS |
| mem_writes | `oltp_insert` | 18.91ms | 35.22ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 66.70ms | 128.95ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 51.03ms | 85.33ms | 1.7× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 49.54ms | 97.74ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 26.91ms | 59.17ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 32.80ms | 53.93ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 104.86ms | 164.53ms | 1.6× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 108.78ms | 60.85ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 27.70ms | 23.59ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 26.30ms | 23.08ms | 0.9× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 4.44ms | 4.19ms | 0.9× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 5.66ms | 5.43ms | 1.0× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 12.29ms | 8.43ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 39.05ms | 36.38ms | 0.9× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 15.88ms | 11.19ms | 0.7× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 11.88ms | 6.72ms | 0.6× | 1.5% | PASS |
| file_reads | `groupby_scan` | 37.80ms | 40.20ms | 1.1× | 0.8% | PASS |
| file_reads | `index_join` | 12.37ms | 12.40ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join_scan` | 4.96ms | 6.00ms | 1.2× | 2.0% | PASS |
| file_reads | `types_table_scan` | 1.11s | 1.20s | 1.1× | 1.4% | PASS |
| file_reads | `table_scan` | 1.34s | 1.30s | 1.0× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 266.47ms | 203.91ms | 0.8× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 260.08ms | 364.37ms | 1.4× | 0.8% | PASS |
| file_writes | `oltp_insert` | 26.10ms | 41.07ms | 1.6× | 1.3% | PASS |
| file_writes | `oltp_update_index` | 97.38ms | 142.34ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 79.10ms | 99.23ms | 1.3× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 78.55ms | 110.16ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 52.15ms | 69.55ms | 1.3× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 50.15ms | 60.89ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 131.78ms | 175.05ms | 1.3× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 58.44ms | 60.56ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 22.71ms | 23.55ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_sum_range` | 20.85ms | 23.02ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.97ms | 4.19ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 5.10ms | 5.44ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 7.43ms | 8.44ms | 1.1× | 1.3% | PASS |
| ac_reads | `select_random_points` | 32.02ms | 36.36ms | 1.1× | 2.1% | PASS |
| ac_reads | `select_random_ranges` | 10.53ms | 11.18ms | 1.1× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 7.11ms | 6.71ms | 0.9× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 37.28ms | 40.31ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 9.80ms | 12.49ms | 1.3× | 1.6% | PASS |
| ac_reads | `index_join_scan` | 4.44ms | 6.00ms | 1.4× | 2.0% | PASS |
| ac_reads | `types_table_scan` | 1.10s | 1.19s | 1.1× | 1.1% | PASS |
| ac_reads | `table_scan` | 1.33s | 1.30s | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 193.59ms | 204.96ms | 1.1× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.94ms | 63.54ms | 2.5× | 5.6% | PASS |
| ac_writes | `oltp_insert_ac` | 25.15ms | 80.73ms | 3.2× | 5.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.49ms | 89.59ms | 3.3× | 5.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 25.84ms | 73.84ms | 2.9× | 8.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.57ms | 83.09ms | 3.2× | 6.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.66ms | 81.89ms | 3.2× | 4.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.00ms | 73.93ms | 3.2× | 8.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.41ms | 89.83ms | 2.8× | 5.9% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 15s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 96.18ms | 130.00ms | 74.0% | 0.6% | PASS |
| `status_dirty_many_tables` | 99.63ms | 130.00ms | 76.6% | 0.9% | PASS |
| `diff_regular_working_one_table` | 90.90ms | 120.00ms | 75.8% | 0.9% | PASS |
| `diff_regular_working_many_tables` | 104.65ms | 140.00ms | 74.8% | 0.8% | PASS |
| `diff_stat_working_many_tables` | 105.13ms | 140.00ms | 75.1% | 0.9% | PASS |
| `diff_schema_working_many_tables` | 106.17ms | 140.00ms | 75.8% | 0.7% | PASS |
| `branch_list_many_branches` | 23.86ms | 35.00ms | 68.2% | 1.8% | PASS |
| `branch_create_delete` | 26.10ms | 40.00ms | 65.3% | 2.1% | PASS |
| `checkout_branch_clean` | 62.56ms | 150.00ms | 41.7% | 1.6% | PASS |
| `merge_data_no_conflicts` | 29.56ms | 50.00ms | 59.1% | 1.9% | PASS |
| `merge_schema_no_conflicts` | 22.77ms | 35.00ms | 65.0% | 3.2% | PASS |
| `merge_data_conflicts` | 33.31ms | 180.00ms | 18.5% | 1.7% | PASS |
| `merge_data_conflicts_with_resolve` | 34.23ms | 180.00ms | 19.0% | 1.9% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
