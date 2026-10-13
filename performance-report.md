# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-13 18:37 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261004.327.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/38435586762)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.58s | 10.01s | 1.0× | 1.6% | **PASS** |
| Writes | 1.90s | 3.06s | 1.6× | 1.2% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.55s | 10.33s | 1.0× | 1.4% | **PASS** |
| Writes | 3.75s | 4.18s | 1.1× | 2.9% | **PASS** |
| Autocommit writes | 794.80ms | 2.41s | 3.0× | 7.0% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.50s | 2.62s | 1.0× | 1.4% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.37s | 2.31s | 1.0× | 3.1% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.17s | 2.32s | 1.1× | 1.7% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.54s | 2.76s | 1.1× | 0.9% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 452.42ms | 747.90ms | 1.7× | 1.8% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 487.87ms | 749.85ms | 1.5× | 2.1% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 436.46ms | 721.12ms | 1.7× | 1.1% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 519.50ms | 845.22ms | 1.6× | 1.0% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.87s | 2.72s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.66s | 2.36s | 0.9× | 1.7% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.39s | 2.46s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.64s | 2.79s | 1.1× | 1.2% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 623.28ms | 824.75ms | 1.3× | 2.7% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.45s | 1.28s | 0.9× | 17.0% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.06s | 1.16s | 1.1× | 5.9% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 615.74ms | 914.24ms | 1.5× | 1.3% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.70s | 2.73s | 1.0× | 1.6% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.49s | 2.37s | 1.0× | 1.3% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.33s | 2.45s | 1.1× | 2.3% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.62s | 2.81s | 1.1× | 1.0% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 208.38ms | 645.74ms | 3.1× | 7.6% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 190.30ms | 670.41ms | 3.5× | 27.3% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 253.40ms | 620.70ms | 2.4× | 5.6% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 142.72ms | 474.01ms | 3.3× | 6.3% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.95ms | 30.08ms | 1.3× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 10.31ms | 11.54ms | 1.1× | 3.5% | PASS |
| mem_reads | `oltp_sum_range` | 9.69ms | 11.28ms | 1.2× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 2.62ms | 2.83ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 3.67ms | 3.98ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.94ms | 5.34ms | 1.4× | 1.0% | PASS |
| mem_reads | `select_random_points` | 10.75ms | 11.07ms | 1.0× | 1.9% | PASS |
| mem_reads | `select_random_ranges` | 2.96ms | 3.96ms | 1.3× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 4.21ms | 4.29ms | 1.0× | 1.4% | PASS |
| mem_reads | `groupby_scan` | 30.56ms | 33.01ms | 1.1× | 0.5% | PASS |
| mem_reads | `index_join` | 5.80ms | 7.88ms | 1.4× | 1.1% | PASS |
| mem_reads | `index_join_scan` | 3.28ms | 4.56ms | 1.4× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.13s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.21s | 1.24s | 1.0× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 106.27ms | 119.81ms | 1.1× | 1.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.15ms | 278.27ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 15.54ms | 28.63ms | 1.8× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 50.85ms | 99.37ms | 2.0× | 2.4% | PASS |
| mem_writes | `oltp_update_non_index` | 35.95ms | 61.70ms | 1.7× | 2.5% | PASS |
| mem_writes | `oltp_delete_insert` | 45.83ms | 75.97ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 22.42ms | 48.31ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 25.16ms | 39.03ms | 1.6× | 2.1% | PASS |
| mem_writes | `oltp_read_write` | 72.51ms | 116.62ms | 1.6× | 2.5% | PASS |
| file_reads | `oltp_point_select` | 99.92ms | 49.98ms | 0.5× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 18.82ms | 13.80ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 17.97ms | 13.59ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 3.55ms | 3.18ms | 0.9× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.66ms | 4.36ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_index_scan` | 12.03ms | 7.80ms | 0.6× | 1.2% | PASS |
| file_reads | `select_random_points` | 19.97ms | 14.07ms | 0.7× | 3.6% | PASS |
| file_reads | `select_random_ranges` | 10.73ms | 6.06ms | 0.6× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 12.11ms | 6.73ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 31.94ms | 33.53ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 10.35ms | 9.92ms | 1.0× | 2.2% | PASS |
| file_reads | `index_join_scan` | 4.39ms | 4.99ms | 1.1× | 3.1% | PASS |
| file_reads | `types_table_scan` | 1.13s | 1.15s | 1.0× | 1.1% | PASS |
| file_reads | `table_scan` | 1.27s | 1.25s | 1.0× | 1.5% | PASS |
| file_reads | `oltp_read_only` | 219.04ms | 150.71ms | 0.7× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 197.41ms | 288.58ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_insert` | 22.55ms | 32.79ms | 1.5× | 2.5% | PASS |
| file_writes | `oltp_update_index` | 80.29ms | 110.25ms | 1.4× | 2.4% | PASS |
| file_writes | `oltp_update_non_index` | 61.76ms | 73.41ms | 1.2× | 2.9% | PASS |
| file_writes | `oltp_delete_insert` | 72.58ms | 89.34ms | 1.2× | 2.5% | PASS |
| file_writes | `oltp_write_only` | 46.90ms | 57.14ms | 1.2× | 2.8% | PASS |
| file_writes | `types_delete_insert` | 42.53ms | 46.23ms | 1.1× | 2.8% | PASS |
| file_writes | `oltp_read_write` | 99.26ms | 127.01ms | 1.3× | 2.8% | PASS |
| ac_reads | `oltp_point_select` | 50.54ms | 50.67ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 14.10ms | 13.85ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 12.95ms | 13.68ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.08ms | 3.15ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 4.11ms | 4.32ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 7.01ms | 7.83ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_points` | 14.22ms | 13.90ms | 1.0× | 3.5% | PASS |
| ac_reads | `select_random_ranges` | 5.74ms | 6.08ms | 1.1× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 7.26ms | 6.71ms | 0.9× | 2.9% | PASS |
| ac_reads | `groupby_scan` | 31.21ms | 33.45ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 7.75ms | 9.98ms | 1.3× | 2.3% | PASS |
| ac_reads | `index_join_scan` | 3.85ms | 5.03ms | 1.3× | 3.0% | PASS |
| ac_reads | `types_table_scan` | 1.11s | 1.14s | 1.0× | 1.5% | PASS |
| ac_reads | `table_scan` | 1.28s | 1.27s | 1.0× | 0.6% | PASS |
| ac_reads | `oltp_read_only` | 150.61ms | 154.65ms | 1.0× | 0.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.81ms | 64.66ms | 2.8× | 7.0% | PASS |
| ac_writes | `oltp_insert_ac` | 26.26ms | 81.05ms | 3.1× | 9.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.59ms | 94.33ms | 3.3× | 6.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.48ms | 74.12ms | 3.2× | 10.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.02ms | 86.37ms | 3.2× | 8.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.62ms | 82.56ms | 3.4× | 4.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.91ms | 73.76ms | 3.1× | 11.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.69ms | 88.89ms | 2.8× | 6.6% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 31.91ms | 28.89ms | 0.9× | 3.8% | PASS |
| mem_reads | `oltp_range_select` | 14.94ms | 10.88ms | 0.7× | 3.1% | PASS |
| mem_reads | `oltp_sum_range` | 13.45ms | 10.68ms | 0.8× | 3.1% | PASS |
| mem_reads | `oltp_order_range` | 2.85ms | 2.57ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 3.67ms | 3.44ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 3.29ms | 4.76ms | 1.4× | 2.3% | PASS |
| mem_reads | `select_random_points` | 20.05ms | 16.67ms | 0.8× | 3.1% | PASS |
| mem_reads | `select_random_ranges` | 3.03ms | 4.30ms | 1.4× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 3.35ms | 3.92ms | 1.2× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 29.78ms | 29.29ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 9.21ms | 7.33ms | 0.8× | 3.9% | PASS |
| mem_reads | `index_join_scan` | 3.27ms | 4.69ms | 1.4× | 3.1% | PASS |
| mem_reads | `types_table_scan` | 967.10ms | 985.27ms | 1.0× | 4.3% | PASS |
| mem_reads | `table_scan` | 1.14s | 1.09s | 1.0× | 3.3% | PASS |
| mem_reads | `oltp_read_only` | 117.70ms | 105.31ms | 0.9× | 1.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 190.81ms | 271.06ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 14.49ms | 28.71ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_update_index` | 55.03ms | 109.79ms | 2.0× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 42.78ms | 64.31ms | 1.5× | 3.0% | PASS |
| mem_writes | `oltp_delete_insert` | 45.09ms | 80.15ms | 1.8× | 1.9% | PASS |
| mem_writes | `oltp_write_only` | 23.36ms | 46.66ms | 2.0× | 2.3% | PASS |
| mem_writes | `types_delete_insert` | 32.40ms | 38.91ms | 1.2× | 2.3% | PASS |
| mem_writes | `oltp_read_write` | 83.92ms | 110.25ms | 1.3× | 2.5% | PASS |
| file_reads | `oltp_point_select` | 106.45ms | 46.57ms | 0.4× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 22.66ms | 12.69ms | 0.6× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 21.74ms | 12.91ms | 0.6× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 3.73ms | 2.82ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.56ms | 3.70ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_index_scan` | 11.15ms | 6.91ms | 0.6× | 1.8% | PASS |
| file_reads | `select_random_points` | 28.70ms | 18.96ms | 0.7× | 4.8% | PASS |
| file_reads | `select_random_ranges` | 10.66ms | 6.18ms | 0.6× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.36ms | 5.90ms | 0.5× | 1.2% | PASS |
| file_reads | `groupby_scan` | 30.79ms | 30.04ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 14.40ms | 9.10ms | 0.6× | 2.6% | PASS |
| file_reads | `index_join_scan` | 4.10ms | 5.08ms | 1.2× | 2.1% | PASS |
| file_reads | `types_table_scan` | 1.02s | 990.41ms | 1.0× | 4.0% | PASS |
| file_reads | `table_scan` | 1.15s | 1.08s | 0.9× | 4.9% | PASS |
| file_reads | `oltp_read_only` | 223.17ms | 129.60ms | 0.6× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 292.06ms | 348.58ms | 1.2× | 8.4% | PASS |
| file_writes | `oltp_insert` | 36.58ms | 63.08ms | 1.7× | 20.5% | PASS |
| file_writes | `oltp_update_index` | 222.72ms | 209.89ms | 0.9× | 17.3% | PASS |
| file_writes | `oltp_update_non_index` | 174.50ms | 135.82ms | 0.8× | 16.7% | PASS |
| file_writes | `oltp_delete_insert` | 215.24ms | 156.55ms | 0.7× | 21.5% | PASS |
| file_writes | `oltp_write_only` | 138.06ms | 102.22ms | 0.7× | 9.5% | PASS |
| file_writes | `types_delete_insert` | 165.83ms | 93.82ms | 0.6× | 17.3% | PASS |
| file_writes | `oltp_read_write` | 204.94ms | 171.69ms | 0.8× | 12.8% | PASS |
| ac_reads | `oltp_point_select` | 55.52ms | 46.34ms | 0.8× | 2.8% | PASS |
| ac_reads | `oltp_range_select` | 18.11ms | 12.90ms | 0.7× | 2.4% | PASS |
| ac_reads | `oltp_sum_range` | 16.74ms | 13.01ms | 0.8× | 2.4% | PASS |
| ac_reads | `oltp_order_range` | 3.26ms | 2.81ms | 0.9× | 0.7% | PASS |
| ac_reads | `oltp_distinct_range` | 4.07ms | 3.70ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.21ms | 6.96ms | 1.1× | 1.3% | PASS |
| ac_reads | `select_random_points` | 22.61ms | 18.68ms | 0.8× | 2.2% | PASS |
| ac_reads | `select_random_ranges` | 5.60ms | 6.14ms | 1.1× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 6.25ms | 5.85ms | 0.9× | 0.7% | PASS |
| ac_reads | `groupby_scan` | 29.57ms | 29.53ms | 1.0× | 1.0% | PASS |
| ac_reads | `index_join` | 11.19ms | 8.86ms | 0.8× | 1.1% | PASS |
| ac_reads | `index_join_scan` | 3.68ms | 5.04ms | 1.4× | 1.1% | PASS |
| ac_reads | `types_table_scan` | 966.09ms | 975.41ms | 1.0× | 3.7% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.10s | 0.9× | 2.6% | PASS |
| ac_reads | `oltp_read_only` | 157.32ms | 132.04ms | 0.8× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 28.79ms | 145.86ms | 5.1× | 75.8% | PASS |
| ac_writes | `oltp_insert_ac` | 21.83ms | 66.80ms | 3.1× | 24.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 23.14ms | 77.74ms | 3.4× | 24.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 18.84ms | 64.60ms | 3.4× | 27.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.85ms | 89.75ms | 3.8× | 27.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.18ms | 77.07ms | 3.5× | 29.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 19.35ms | 61.19ms | 3.2× | 13.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.32ms | 87.39ms | 2.7× | 37.1% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 28.59ms | 27.98ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 12.43ms | 10.92ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 12.25ms | 10.73ms | 0.9× | 2.9% | PASS |
| mem_reads | `oltp_order_range` | 2.65ms | 2.58ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.67ms | 3.73ms | 1.0× | 2.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.49ms | 4.97ms | 1.4× | 2.1% | PASS |
| mem_reads | `select_random_points` | 19.51ms | 18.46ms | 0.9× | 1.2% | PASS |
| mem_reads | `select_random_ranges` | 2.83ms | 4.37ms | 1.5× | 2.1% | PASS |
| mem_reads | `covering_index_scan` | 3.08ms | 3.57ms | 1.2× | 3.6% | PASS |
| mem_reads | `groupby_scan` | 28.23ms | 28.34ms | 1.0× | 1.2% | PASS |
| mem_reads | `index_join` | 9.44ms | 7.53ms | 0.8× | 1.6% | PASS |
| mem_reads | `index_join_scan` | 2.85ms | 4.80ms | 1.7× | 1.7% | PASS |
| mem_reads | `types_table_scan` | 910.45ms | 995.06ms | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.03s | 1.09s | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_read_only` | 105.61ms | 105.50ms | 1.0× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 173.07ms | 255.04ms | 1.5× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 13.68ms | 27.58ms | 2.0× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 50.20ms | 108.22ms | 2.2× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 36.93ms | 62.48ms | 1.7× | 1.1% | PASS |
| mem_writes | `oltp_delete_insert` | 41.11ms | 77.56ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 21.27ms | 44.95ms | 2.1× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 28.82ms | 38.44ms | 1.3× | 1.1% | PASS |
| mem_writes | `oltp_read_write` | 71.38ms | 106.83ms | 1.5× | 0.8% | PASS |
| file_reads | `oltp_point_select` | 55.48ms | 34.02ms | 0.6× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 15.32ms | 11.74ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_sum_range` | 15.21ms | 11.37ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_order_range` | 3.01ms | 2.72ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 3.80ms | 3.60ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 6.23ms | 5.70ms | 0.9× | 1.8% | PASS |
| file_reads | `select_random_points` | 22.19ms | 18.57ms | 0.8× | 1.5% | PASS |
| file_reads | `select_random_ranges` | 5.56ms | 4.96ms | 0.9× | 1.9% | PASS |
| file_reads | `covering_index_scan` | 6.03ms | 4.55ms | 0.8× | 2.7% | PASS |
| file_reads | `groupby_scan` | 29.10ms | 28.80ms | 1.0× | 1.5% | PASS |
| file_reads | `index_join` | 11.79ms | 9.20ms | 0.8× | 2.1% | PASS |
| file_reads | `index_join_scan` | 3.48ms | 5.28ms | 1.5× | 2.8% | PASS |
| file_reads | `types_table_scan` | 946.05ms | 1.03s | 1.1× | 0.7% | PASS |
| file_reads | `table_scan` | 1.11s | 1.16s | 1.0× | 1.5% | PASS |
| file_reads | `oltp_read_only` | 156.85ms | 122.72ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 246.33ms | 329.94ms | 1.3× | 2.7% | PASS |
| file_writes | `oltp_insert` | 22.17ms | 49.45ms | 2.2× | 12.2% | PASS |
| file_writes | `oltp_update_index` | 147.41ms | 197.97ms | 1.3× | 4.5% | PASS |
| file_writes | `oltp_update_non_index` | 124.67ms | 121.12ms | 1.0× | 6.7% | PASS |
| file_writes | `oltp_delete_insert` | 151.29ms | 140.19ms | 0.9× | 5.9% | PASS |
| file_writes | `oltp_write_only` | 101.20ms | 90.23ms | 0.9× | 8.9% | PASS |
| file_writes | `types_delete_insert` | 110.77ms | 76.00ms | 0.7× | 6.0% | PASS |
| file_writes | `oltp_read_write` | 160.54ms | 150.39ms | 0.9× | 5.9% | PASS |
| ac_reads | `oltp_point_select` | 37.69ms | 34.79ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 13.64ms | 11.92ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 13.42ms | 11.46ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 2.86ms | 2.73ms | 1.0× | 3.3% | PASS |
| ac_reads | `oltp_distinct_range` | 3.65ms | 3.62ms | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_index_scan` | 4.62ms | 5.85ms | 1.3× | 3.3% | PASS |
| ac_reads | `select_random_points` | 20.81ms | 18.91ms | 0.9× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 3.76ms | 5.06ms | 1.3× | 2.7% | PASS |
| ac_reads | `covering_index_scan` | 4.20ms | 4.51ms | 1.1× | 2.6% | PASS |
| ac_reads | `groupby_scan` | 29.18ms | 29.13ms | 1.0× | 2.3% | PASS |
| ac_reads | `index_join` | 11.12ms | 9.18ms | 0.8× | 2.6% | PASS |
| ac_reads | `index_join_scan` | 3.36ms | 5.32ms | 1.6× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 960.38ms | 1.04s | 1.1× | 0.9% | PASS |
| ac_reads | `table_scan` | 1.09s | 1.15s | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 124.51ms | 119.69ms | 1.0× | 1.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 30.78ms | 65.36ms | 2.1× | 5.4% | PASS |
| ac_writes | `oltp_insert_ac` | 33.11ms | 79.45ms | 2.4× | 5.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 33.32ms | 83.97ms | 2.5× | 4.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 28.30ms | 71.99ms | 2.5× | 5.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 33.01ms | 82.33ms | 2.5× | 5.8% | PASS |
| ac_writes | `oltp_write_only_ac` | 32.35ms | 79.42ms | 2.5× | 6.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 26.54ms | 72.52ms | 2.7× | 7.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 35.99ms | 85.64ms | 2.4× | 7.8% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 30.93ms | 37.54ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_range_select` | 16.78ms | 20.01ms | 1.2× | 0.9% | PASS |
| mem_reads | `oltp_sum_range` | 16.04ms | 19.85ms | 1.2× | 0.9% | PASS |
| mem_reads | `oltp_order_range` | 3.14ms | 3.63ms | 1.2× | 0.7% | PASS |
| mem_reads | `oltp_distinct_range` | 3.94ms | 4.77ms | 1.2× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 4.01ms | 5.73ms | 1.4× | 1.8% | PASS |
| mem_reads | `select_random_points` | 25.80ms | 30.75ms | 1.2× | 1.8% | PASS |
| mem_reads | `select_random_ranges` | 6.55ms | 8.22ms | 1.3× | 0.9% | PASS |
| mem_reads | `covering_index_scan` | 3.64ms | 4.04ms | 1.1× | 1.5% | PASS |
| mem_reads | `groupby_scan` | 32.00ms | 38.18ms | 1.2× | 0.4% | PASS |
| mem_reads | `index_join` | 6.81ms | 10.21ms | 1.5× | 0.9% | PASS |
| mem_reads | `index_join_scan` | 3.28ms | 5.13ms | 1.6× | 0.9% | PASS |
| mem_reads | `types_table_scan` | 1.02s | 1.16s | 1.1× | 1.0% | PASS |
| mem_reads | `table_scan` | 1.24s | 1.26s | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_read_only` | 128.75ms | 157.12ms | 1.2× | 0.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 213.40ms | 305.40ms | 1.4× | 2.0% | PASS |
| mem_writes | `oltp_insert` | 16.68ms | 30.42ms | 1.8× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 59.75ms | 113.61ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 44.67ms | 71.86ms | 1.6× | 0.8% | PASS |
| mem_writes | `oltp_delete_insert` | 43.55ms | 84.43ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_write_only` | 23.24ms | 49.91ms | 2.1× | 0.7% | PASS |
| mem_writes | `types_delete_insert` | 28.27ms | 46.50ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 89.94ms | 143.10ms | 1.6× | 1.2% | PASS |
| file_reads | `oltp_point_select` | 62.13ms | 47.67ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 19.92ms | 21.24ms | 1.1× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 19.22ms | 21.05ms | 1.1× | 0.9% | PASS |
| file_reads | `oltp_order_range` | 3.56ms | 3.82ms | 1.1× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 4.36ms | 4.92ms | 1.1× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 7.29ms | 6.88ms | 0.9× | 1.8% | PASS |
| file_reads | `select_random_points` | 29.51ms | 32.04ms | 1.1× | 1.0% | PASS |
| file_reads | `select_random_ranges` | 10.14ms | 9.55ms | 0.9× | 0.8% | PASS |
| file_reads | `covering_index_scan` | 6.93ms | 5.15ms | 0.7× | 1.5% | PASS |
| file_reads | `groupby_scan` | 32.26ms | 38.34ms | 1.2× | 0.7% | PASS |
| file_reads | `index_join` | 8.78ms | 10.94ms | 1.2× | 1.1% | PASS |
| file_reads | `index_join_scan` | 3.76ms | 5.34ms | 1.4× | 1.6% | PASS |
| file_reads | `types_table_scan` | 1.02s | 1.15s | 1.1× | 1.7% | PASS |
| file_reads | `table_scan` | 1.23s | 1.26s | 1.0× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 176.66ms | 174.19ms | 1.0× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 226.00ms | 315.04ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_insert` | 21.00ms | 35.59ms | 1.7× | 1.2% | PASS |
| file_writes | `oltp_update_index` | 76.21ms | 125.56ms | 1.6× | 1.0% | PASS |
| file_writes | `oltp_update_non_index` | 59.27ms | 83.34ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_delete_insert` | 58.29ms | 94.06ms | 1.6× | 0.9% | PASS |
| file_writes | `oltp_write_only` | 36.12ms | 58.52ms | 1.6× | 1.4% | PASS |
| file_writes | `types_delete_insert` | 37.52ms | 51.47ms | 1.4× | 1.3% | PASS |
| file_writes | `oltp_read_write` | 101.32ms | 150.66ms | 1.5× | 0.9% | PASS |
| ac_reads | `oltp_point_select` | 39.66ms | 47.68ms | 1.2× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 17.58ms | 21.28ms | 1.2× | 1.0% | PASS |
| ac_reads | `oltp_sum_range` | 16.78ms | 21.10ms | 1.3× | 1.1% | PASS |
| ac_reads | `oltp_order_range` | 3.29ms | 3.83ms | 1.2× | 1.1% | PASS |
| ac_reads | `oltp_distinct_range` | 4.07ms | 4.99ms | 1.2× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 5.22ms | 6.90ms | 1.3× | 1.3% | PASS |
| ac_reads | `select_random_points` | 27.21ms | 32.39ms | 1.2× | 0.9% | PASS |
| ac_reads | `select_random_ranges` | 7.76ms | 9.61ms | 1.2× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 4.83ms | 5.14ms | 1.1× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 32.16ms | 38.49ms | 1.2× | 0.7% | PASS |
| ac_reads | `index_join` | 7.71ms | 11.09ms | 1.4× | 1.0% | PASS |
| ac_reads | `index_join_scan` | 3.55ms | 5.40ms | 1.5× | 1.4% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.16s | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 1.26s | 1.27s | 1.0× | 0.9% | PASS |
| ac_reads | `oltp_read_only` | 146.07ms | 176.26ms | 1.2× | 0.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 16.85ms | 45.63ms | 2.7× | 6.8% | PASS |
| ac_writes | `oltp_insert_ac` | 17.76ms | 59.83ms | 3.4× | 6.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 18.83ms | 69.11ms | 3.7× | 6.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.21ms | 53.89ms | 3.3× | 6.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 16.99ms | 61.66ms | 3.6× | 7.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 17.74ms | 61.99ms | 3.5× | 5.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 15.63ms | 53.78ms | 3.4× | 7.5% | PASS |
| ac_writes | `oltp_read_write_ac` | 22.70ms | 68.13ms | 3.0× | 3.2% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 12s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 94.81ms | 130.00ms | 72.9% | 0.5% | PASS |
| `status_dirty_many_tables` | 98.78ms | 130.00ms | 76.0% | 0.4% | PASS |
| `diff_regular_working_one_table` | 90.54ms | 120.00ms | 75.5% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 103.66ms | 140.00ms | 74.0% | 0.3% | PASS |
| `diff_stat_working_many_tables` | 103.90ms | 140.00ms | 74.2% | 0.4% | PASS |
| `diff_schema_working_many_tables` | 105.17ms | 140.00ms | 75.1% | 0.6% | PASS |
| `branch_list_many_branches` | 22.70ms | 35.00ms | 64.9% | 1.1% | PASS |
| `branch_create_delete` | 25.04ms | 40.00ms | 62.6% | 1.1% | PASS |
| `checkout_branch_clean` | 61.18ms | 150.00ms | 40.8% | 0.8% | PASS |
| `merge_data_no_conflicts` | 29.25ms | 50.00ms | 58.5% | 1.3% | PASS |
| `merge_schema_no_conflicts` | 21.94ms | 35.00ms | 62.7% | 1.9% | PASS |
| `merge_data_conflicts` | 32.34ms | 180.00ms | 18.0% | 0.8% | PASS |
| `merge_data_conflicts_with_resolve` | 32.54ms | 180.00ms | 18.1% | 1.2% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
