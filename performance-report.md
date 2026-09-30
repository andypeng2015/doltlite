# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-30 17:33 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260927.320.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/36741203167)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.85s | 10.44s | 1.1× | 1.0% | **PASS** |
| Writes | 2.02s | 3.31s | 1.6× | 1.0% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.64s | 10.63s | 1.0× | 1.2% | **PASS** |
| Writes | 3.25s | 3.95s | 1.2× | 2.0% | **PASS** |
| Autocommit writes | 747.03ms | 2.19s | 2.9× | 6.5% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.64s | 2.65s | 1.0× | 2.0% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.53s | 2.73s | 1.1× | 1.0% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.10s | 2.23s | 1.1× | 0.7% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.57s | 2.83s | 1.1× | 1.3% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 453.58ms | 749.90ms | 1.7× | 1.5% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 519.35ms | 883.24ms | 1.7× | 1.1% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 454.52ms | 718.45ms | 1.6× | 0.7% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 594.89ms | 962.76ms | 1.6× | 1.1% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.75s | 2.67s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.67s | 2.77s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.35s | 2.29s | 1.0× | 0.8% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.88s | 2.90s | 1.0× | 1.3% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 601.18ms | 797.96ms | 1.3× | 1.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 761.76ms | 973.39ms | 1.3× | 3.8% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.11s | 1.12s | 1.0× | 4.0% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 785.94ms | 1.06s | 1.4× | 2.0% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.65s | 2.69s | 1.0× | 1.8% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.56s | 2.75s | 1.1× | 1.0% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.19s | 2.29s | 1.0× | 0.6% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.69s | 2.90s | 1.1× | 1.4% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 205.32ms | 625.56ms | 3.0× | 6.3% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 143.02ms | 459.54ms | 3.2× | 6.8% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 200.10ms | 489.17ms | 2.4× | 7.3% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 198.59ms | 610.78ms | 3.1× | 5.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 25.14ms | 30.42ms | 1.2× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 10.66ms | 11.67ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 10.06ms | 11.46ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 2.62ms | 2.83ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.71ms | 3.96ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 4.04ms | 5.66ms | 1.4× | 2.0% | PASS |
| mem_reads | `select_random_points` | 11.04ms | 11.50ms | 1.0× | 4.4% | PASS |
| mem_reads | `select_random_ranges` | 3.13ms | 4.05ms | 1.3× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.26ms | 4.56ms | 1.1× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 31.02ms | 33.23ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 5.95ms | 8.63ms | 1.5× | 3.1% | PASS |
| mem_reads | `index_join_scan` | 3.30ms | 4.54ms | 1.4× | 2.0% | PASS |
| mem_reads | `types_table_scan` | 1.13s | 1.15s | 1.0× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.29s | 1.25s | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 108.76ms | 121.18ms | 1.1× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 183.28ms | 276.66ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 15.55ms | 28.66ms | 1.8× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 51.72ms | 100.39ms | 1.9× | 1.7% | PASS |
| mem_writes | `oltp_update_non_index` | 36.59ms | 62.25ms | 1.7× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 46.64ms | 76.07ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 22.58ms | 48.23ms | 2.1× | 1.8% | PASS |
| mem_writes | `types_delete_insert` | 25.05ms | 39.38ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 72.16ms | 118.26ms | 1.6× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 100.59ms | 50.86ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 18.64ms | 13.87ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 17.86ms | 13.64ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_order_range` | 3.58ms | 3.17ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.65ms | 4.37ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 11.88ms | 7.76ms | 0.7× | 1.2% | PASS |
| file_reads | `select_random_points` | 18.97ms | 13.68ms | 0.7× | 3.3% | PASS |
| file_reads | `select_random_ranges` | 10.66ms | 6.03ms | 0.6× | 1.0% | PASS |
| file_reads | `covering_index_scan` | 12.45ms | 6.76ms | 0.5× | 1.4% | PASS |
| file_reads | `groupby_scan` | 32.05ms | 33.59ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 10.34ms | 9.92ms | 1.0× | 2.3% | PASS |
| file_reads | `index_join_scan` | 4.28ms | 4.84ms | 1.1× | 2.3% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.13s | 1.1× | 1.2% | PASS |
| file_reads | `table_scan` | 1.22s | 1.23s | 1.0× | 1.7% | PASS |
| file_reads | `oltp_read_only` | 212.22ms | 148.34ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 196.22ms | 284.62ms | 1.5× | 0.7% | PASS |
| file_writes | `oltp_insert` | 22.29ms | 32.47ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_update_index` | 76.01ms | 104.77ms | 1.4× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 58.79ms | 70.80ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_delete_insert` | 70.41ms | 84.74ms | 1.2× | 2.3% | PASS |
| file_writes | `oltp_write_only` | 46.07ms | 55.67ms | 1.2× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 40.24ms | 44.38ms | 1.1× | 1.4% | PASS |
| file_writes | `oltp_read_write` | 91.15ms | 120.51ms | 1.3× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 48.07ms | 49.35ms | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 13.09ms | 13.68ms | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_sum_range` | 12.51ms | 13.62ms | 1.1× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 2.99ms | 3.15ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.07ms | 4.32ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_index_scan` | 6.78ms | 7.76ms | 1.1× | 1.8% | PASS |
| ac_reads | `select_random_points` | 13.39ms | 13.50ms | 1.0× | 3.8% | PASS |
| ac_reads | `select_random_ranges` | 5.69ms | 6.01ms | 1.1× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 7.28ms | 6.74ms | 0.9× | 1.6% | PASS |
| ac_reads | `groupby_scan` | 31.31ms | 33.55ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 7.88ms | 10.19ms | 1.3× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 3.90ms | 5.02ms | 1.3× | 3.5% | PASS |
| ac_reads | `types_table_scan` | 1.12s | 1.15s | 1.0× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.23s | 1.23s | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_read_only` | 139.80ms | 148.49ms | 1.1× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.35ms | 63.53ms | 2.8× | 4.6% | PASS |
| ac_writes | `oltp_insert_ac` | 26.08ms | 77.11ms | 3.0× | 6.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.72ms | 88.49ms | 3.2× | 6.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.94ms | 70.87ms | 3.1× | 3.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.44ms | 80.61ms | 3.2× | 5.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.75ms | 80.73ms | 3.1× | 8.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.70ms | 73.12ms | 3.1× | 8.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.34ms | 91.10ms | 2.9× | 8.3% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 31.60ms | 34.20ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 14.11ms | 12.80ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 13.47ms | 12.62ms | 0.9× | 1.0% | PASS |
| mem_reads | `oltp_order_range` | 2.91ms | 2.92ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_distinct_range` | 3.67ms | 4.00ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.48ms | 5.47ms | 1.6× | 0.9% | PASS |
| mem_reads | `select_random_points` | 19.71ms | 20.19ms | 1.0× | 1.0% | PASS |
| mem_reads | `select_random_ranges` | 3.07ms | 4.64ms | 1.5× | 0.7% | PASS |
| mem_reads | `covering_index_scan` | 3.63ms | 4.14ms | 1.1× | 1.3% | PASS |
| mem_reads | `groupby_scan` | 30.68ms | 33.01ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 9.56ms | 8.81ms | 0.9× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 3.10ms | 5.13ms | 1.7× | 1.3% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.18s | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.23s | 1.27s | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_read_only` | 124.49ms | 128.90ms | 1.0× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 204.30ms | 319.85ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 15.84ms | 33.78ms | 2.1× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 58.98ms | 129.59ms | 2.2× | 1.7% | PASS |
| mem_writes | `oltp_update_non_index` | 43.91ms | 74.47ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_delete_insert` | 48.18ms | 93.80ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_write_only` | 24.65ms | 53.89ms | 2.2× | 0.8% | PASS |
| mem_writes | `types_delete_insert` | 34.66ms | 47.43ms | 1.4× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 88.83ms | 130.43ms | 1.5× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 65.81ms | 44.33ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 18.08ms | 14.35ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 17.61ms | 14.22ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 3.33ms | 3.11ms | 0.9× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.12ms | 4.24ms | 1.0× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 6.96ms | 6.87ms | 1.0× | 1.2% | PASS |
| file_reads | `select_random_points` | 25.11ms | 22.29ms | 0.9× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 6.38ms | 5.70ms | 0.9× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 7.02ms | 5.32ms | 0.8× | 1.1% | PASS |
| file_reads | `groupby_scan` | 31.42ms | 33.26ms | 1.1× | 0.6% | PASS |
| file_reads | `index_join` | 11.71ms | 9.82ms | 0.8× | 1.5% | PASS |
| file_reads | `index_join_scan` | 3.60ms | 5.42ms | 1.5× | 1.3% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.18s | 1.1× | 1.0% | PASS |
| file_reads | `table_scan` | 1.25s | 1.27s | 1.0× | 0.8% | PASS |
| file_reads | `oltp_read_only` | 175.17ms | 146.69ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 223.55ms | 335.74ms | 1.5× | 1.4% | PASS |
| file_writes | `oltp_insert` | 20.91ms | 40.26ms | 1.9× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 103.25ms | 146.65ms | 1.4× | 5.8% | PASS |
| file_writes | `oltp_update_non_index` | 97.20ms | 86.04ms | 0.9× | 8.3% | PASS |
| file_writes | `oltp_delete_insert` | 71.20ms | 104.49ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_write_only` | 63.68ms | 62.25ms | 1.0× | 13.5% | PASS |
| file_writes | `types_delete_insert` | 53.77ms | 56.06ms | 1.0× | 1.3% | PASS |
| file_writes | `oltp_read_write` | 128.20ms | 141.90ms | 1.1× | 11.7% | PASS |
| ac_reads | `oltp_point_select` | 41.96ms | 44.18ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 15.28ms | 14.27ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_sum_range` | 14.81ms | 14.11ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 3.11ms | 3.14ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_distinct_range` | 3.87ms | 4.26ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_index_scan` | 4.82ms | 6.91ms | 1.4× | 1.0% | PASS |
| ac_reads | `select_random_points` | 22.20ms | 22.14ms | 1.0× | 1.6% | PASS |
| ac_reads | `select_random_ranges` | 4.19ms | 5.73ms | 1.4× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 4.86ms | 5.29ms | 1.1× | 1.0% | PASS |
| ac_reads | `groupby_scan` | 31.04ms | 33.41ms | 1.1× | 0.5% | PASS |
| ac_reads | `index_join` | 10.89ms | 9.93ms | 0.9× | 1.5% | PASS |
| ac_reads | `index_join_scan` | 3.38ms | 5.42ms | 1.6× | 1.2% | PASS |
| ac_reads | `types_table_scan` | 1.02s | 1.17s | 1.1× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.24s | 1.27s | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_read_only` | 139.64ms | 145.07ms | 1.0× | 0.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.00ms | 45.10ms | 2.7× | 8.5% | PASS |
| ac_writes | `oltp_insert_ac` | 18.25ms | 56.55ms | 3.1× | 3.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 18.89ms | 66.33ms | 3.5× | 6.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 15.23ms | 51.67ms | 3.4× | 7.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 18.41ms | 59.87ms | 3.3× | 5.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 17.35ms | 59.81ms | 3.4× | 7.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 15.95ms | 54.54ms | 3.4× | 6.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 21.94ms | 65.66ms | 3.0× | 6.8% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 27.07ms | 27.68ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_range_select` | 13.08ms | 10.65ms | 0.8× | 1.0% | PASS |
| mem_reads | `oltp_sum_range` | 12.09ms | 10.31ms | 0.9× | 0.9% | PASS |
| mem_reads | `oltp_order_range` | 2.71ms | 2.51ms | 0.9× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 3.55ms | 3.40ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.17ms | 4.49ms | 1.4× | 0.7% | PASS |
| mem_reads | `select_random_points` | 16.38ms | 15.51ms | 0.9× | 0.5% | PASS |
| mem_reads | `select_random_ranges` | 2.75ms | 4.16ms | 1.5× | 0.7% | PASS |
| mem_reads | `covering_index_scan` | 3.34ms | 3.45ms | 1.0× | 0.7% | PASS |
| mem_reads | `groupby_scan` | 28.43ms | 28.36ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 8.40ms | 6.85ms | 0.8× | 0.8% | PASS |
| mem_reads | `index_join_scan` | 3.02ms | 4.61ms | 1.5× | 0.7% | PASS |
| mem_reads | `types_table_scan` | 880.95ms | 957.34ms | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 989.53ms | 1.05s | 1.1× | 0.3% | PASS |
| mem_reads | `oltp_read_only` | 106.37ms | 102.32ms | 1.0× | 0.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 188.79ms | 266.10ms | 1.4× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 14.55ms | 27.78ms | 1.9× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 49.98ms | 101.74ms | 2.0× | 0.6% | PASS |
| mem_writes | `oltp_update_non_index` | 37.47ms | 60.12ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 40.82ms | 75.89ms | 1.9× | 0.7% | PASS |
| mem_writes | `oltp_write_only` | 21.94ms | 44.91ms | 2.0× | 0.6% | PASS |
| mem_writes | `types_delete_insert` | 29.02ms | 37.05ms | 1.3× | 0.9% | PASS |
| mem_writes | `oltp_read_write` | 71.96ms | 104.87ms | 1.5× | 0.7% | PASS |
| file_reads | `oltp_point_select` | 102.63ms | 46.12ms | 0.4× | 0.6% | PASS |
| file_reads | `oltp_range_select` | 21.10ms | 12.68ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_sum_range` | 20.06ms | 12.39ms | 0.6× | 0.8% | PASS |
| file_reads | `oltp_order_range` | 3.56ms | 2.79ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_distinct_range` | 4.39ms | 3.66ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 10.89ms | 6.78ms | 0.6× | 0.8% | PASS |
| file_reads | `select_random_points` | 24.85ms | 17.75ms | 0.7× | 0.9% | PASS |
| file_reads | `select_random_ranges` | 10.30ms | 6.12ms | 0.6× | 0.5% | PASS |
| file_reads | `covering_index_scan` | 11.19ms | 5.77ms | 0.5× | 0.7% | PASS |
| file_reads | `groupby_scan` | 29.36ms | 28.96ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 12.97ms | 8.79ms | 0.7× | 0.8% | PASS |
| file_reads | `index_join_scan` | 3.97ms | 5.00ms | 1.3× | 1.3% | PASS |
| file_reads | `types_table_scan` | 885.21ms | 953.01ms | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 992.28ms | 1.05s | 1.1× | 0.3% | PASS |
| file_reads | `oltp_read_only` | 214.93ms | 128.32ms | 0.6× | 0.5% | PASS |
| file_writes | `oltp_bulk_insert` | 251.70ms | 325.14ms | 1.3× | 3.8% | PASS |
| file_writes | `oltp_insert` | 25.08ms | 48.81ms | 1.9× | 6.1% | PASS |
| file_writes | `oltp_update_index` | 150.38ms | 181.18ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 129.84ms | 118.63ms | 0.9× | 4.3% | PASS |
| file_writes | `oltp_delete_insert` | 157.32ms | 129.16ms | 0.8× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 109.94ms | 89.02ms | 0.8× | 2.5% | PASS |
| file_writes | `types_delete_insert` | 111.34ms | 72.39ms | 0.7× | 6.1% | PASS |
| file_writes | `oltp_read_write` | 169.50ms | 152.35ms | 0.9× | 4.1% | PASS |
| ac_reads | `oltp_point_select` | 52.30ms | 45.95ms | 0.9× | 0.6% | PASS |
| ac_reads | `oltp_range_select` | 16.42ms | 12.65ms | 0.8× | 0.6% | PASS |
| ac_reads | `oltp_sum_range` | 15.17ms | 12.37ms | 0.8× | 0.8% | PASS |
| ac_reads | `oltp_order_range` | 3.14ms | 2.78ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 3.94ms | 3.67ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.03ms | 6.76ms | 1.1× | 0.6% | PASS |
| ac_reads | `select_random_points` | 19.87ms | 17.78ms | 0.9× | 0.3% | PASS |
| ac_reads | `select_random_ranges` | 5.35ms | 6.17ms | 1.2× | 0.6% | PASS |
| ac_reads | `covering_index_scan` | 6.22ms | 5.77ms | 0.9× | 0.6% | PASS |
| ac_reads | `groupby_scan` | 29.03ms | 29.10ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 10.53ms | 8.75ms | 0.8× | 0.6% | PASS |
| ac_reads | `index_join_scan` | 3.57ms | 4.99ms | 1.4× | 0.7% | PASS |
| ac_reads | `types_table_scan` | 883.23ms | 953.86ms | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 991.61ms | 1.05s | 1.1× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 142.63ms | 128.43ms | 0.9× | 0.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.84ms | 47.14ms | 2.0× | 7.3% | PASS |
| ac_writes | `oltp_insert_ac` | 26.46ms | 61.76ms | 2.3× | 7.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.83ms | 72.29ms | 2.7× | 7.3% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.16ms | 55.28ms | 2.5× | 7.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.39ms | 65.25ms | 2.5× | 6.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 25.44ms | 61.84ms | 2.4× | 10.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.34ms | 57.20ms | 3.1× | 6.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 30.64ms | 68.40ms | 2.2× | 7.1% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.93ms | 40.81ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_range_select` | 18.96ms | 20.91ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 17.43ms | 20.37ms | 1.2× | 1.8% | PASS |
| mem_reads | `oltp_order_range` | 3.42ms | 3.81ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 4.61ms | 5.00ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_index_scan` | 4.36ms | 5.92ms | 1.4× | 2.3% | PASS |
| mem_reads | `select_random_points` | 27.03ms | 32.03ms | 1.2× | 1.1% | PASS |
| mem_reads | `select_random_ranges` | 7.48ms | 8.88ms | 1.2× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.24ms | 1.0× | 1.0% | PASS |
| mem_reads | `groupby_scan` | 36.29ms | 39.68ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 7.92ms | 10.12ms | 1.3× | 1.4% | PASS |
| mem_reads | `index_join_scan` | 3.82ms | 5.39ms | 1.4× | 2.5% | PASS |
| mem_reads | `types_table_scan` | 1.06s | 1.19s | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 1.20s | 1.27s | 1.1× | 0.3% | PASS |
| mem_reads | `oltp_read_only` | 147.02ms | 169.69ms | 1.2× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 251.03ms | 353.62ms | 1.4× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 19.18ms | 35.16ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 65.38ms | 124.72ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_non_index` | 49.96ms | 82.95ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 48.80ms | 94.60ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 26.68ms | 57.41ms | 2.2× | 1.3% | PASS |
| mem_writes | `types_delete_insert` | 33.49ms | 54.07ms | 1.6× | 1.9% | PASS |
| mem_writes | `oltp_read_write` | 100.36ms | 160.25ms | 1.6× | 1.3% | PASS |
| file_reads | `oltp_point_select` | 109.62ms | 60.72ms | 0.6× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 28.19ms | 23.26ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 26.66ms | 23.07ms | 0.9× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 4.68ms | 4.29ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 5.79ms | 5.43ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 12.62ms | 8.38ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 38.42ms | 36.17ms | 0.9× | 1.3% | PASS |
| file_reads | `select_random_ranges` | 16.04ms | 11.24ms | 0.7× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 12.31ms | 6.63ms | 0.5× | 1.6% | PASS |
| file_reads | `groupby_scan` | 38.33ms | 40.37ms | 1.1× | 1.2% | PASS |
| file_reads | `index_join` | 12.77ms | 12.12ms | 0.9× | 1.2% | PASS |
| file_reads | `index_join_scan` | 5.09ms | 5.87ms | 1.2× | 1.8% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.18s | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 1.25s | 1.28s | 1.0× | 1.7% | PASS |
| file_reads | `oltp_read_only` | 264.37ms | 202.64ms | 0.8× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 266.80ms | 367.18ms | 1.4× | 0.6% | PASS |
| file_writes | `oltp_insert` | 26.41ms | 41.25ms | 1.6× | 1.2% | PASS |
| file_writes | `oltp_update_index` | 97.89ms | 140.15ms | 1.4× | 1.9% | PASS |
| file_writes | `oltp_update_non_index` | 79.05ms | 98.90ms | 1.3× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 78.81ms | 108.70ms | 1.4× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 53.28ms | 69.26ms | 1.3× | 2.6% | PASS |
| file_writes | `types_delete_insert` | 51.40ms | 60.61ms | 1.2× | 2.2% | PASS |
| file_writes | `oltp_read_write` | 132.31ms | 176.79ms | 1.3× | 2.2% | PASS |
| ac_reads | `oltp_point_select` | 58.94ms | 61.43ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 22.72ms | 23.21ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 21.03ms | 22.97ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.96ms | 4.24ms | 1.1× | 2.0% | PASS |
| ac_reads | `oltp_distinct_range` | 5.05ms | 5.41ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_index_scan` | 7.13ms | 8.37ms | 1.2× | 1.6% | PASS |
| ac_reads | `select_random_points` | 31.39ms | 36.22ms | 1.2× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 10.43ms | 11.23ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 6.91ms | 6.51ms | 0.9× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 36.80ms | 40.22ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 9.54ms | 12.07ms | 1.3× | 1.2% | PASS |
| ac_reads | `index_join_scan` | 4.38ms | 5.87ms | 1.3× | 1.4% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.5% | PASS |
| ac_reads | `table_scan` | 1.23s | 1.28s | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_read_only` | 188.28ms | 201.73ms | 1.1× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.64ms | 60.73ms | 2.6× | 6.5% | PASS |
| ac_writes | `oltp_insert_ac` | 23.99ms | 76.42ms | 3.2× | 3.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.11ms | 85.34ms | 3.3× | 5.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.88ms | 70.23ms | 2.9× | 6.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.16ms | 79.72ms | 3.3× | 4.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.24ms | 79.86ms | 3.3× | 4.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.56ms | 72.39ms | 3.4× | 5.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.00ms | 86.08ms | 2.8× | 4.6% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 10s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 93.91ms | 130.00ms | 72.2% | 0.3% | PASS |
| `status_dirty_many_tables` | 97.43ms | 130.00ms | 74.9% | 0.2% | PASS |
| `diff_regular_working_one_table` | 89.23ms | 120.00ms | 74.4% | 0.3% | PASS |
| `diff_regular_working_many_tables` | 102.43ms | 140.00ms | 73.2% | 0.2% | PASS |
| `diff_stat_working_many_tables` | 102.44ms | 140.00ms | 73.2% | 0.4% | PASS |
| `diff_schema_working_many_tables` | 103.43ms | 140.00ms | 73.9% | 0.3% | PASS |
| `branch_list_many_branches` | 22.41ms | 35.00ms | 64.0% | 0.7% | PASS |
| `branch_create_delete` | 24.52ms | 40.00ms | 61.3% | 1.2% | PASS |
| `checkout_branch_clean` | 59.55ms | 150.00ms | 39.7% | 0.6% | PASS |
| `merge_data_no_conflicts` | 28.18ms | 50.00ms | 56.4% | 0.9% | PASS |
| `merge_schema_no_conflicts` | 21.30ms | 35.00ms | 60.9% | 1.0% | PASS |
| `merge_data_conflicts` | 31.68ms | 180.00ms | 17.6% | 0.6% | PASS |
| `merge_data_conflicts_with_resolve` | 31.79ms | 180.00ms | 17.7% | 0.5% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
