# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-07 18:16 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260927.320.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/37654800664)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 7.64s | 7.94s | 1.0× | 2.4% | **PASS** |
| Writes | 1.60s | 2.60s | 1.6× | 2.1% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.79s | 8.35s | 1.0× | 2.0% | **PASS** |
| Writes | 3.64s | 3.78s | 1.0× | 4.4% | **PASS** |
| Autocommit writes | 922.70ms | 2.44s | 2.6× | 6.5% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 1.55s | 1.62s | 1.0× | 2.4% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.72s | 1.77s | 1.0× | 2.8% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 1.61s | 1.68s | 1.0× | 2.3% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.77s | 2.87s | 1.0× | 2.0% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 266.16ms | 427.38ms | 1.6× | 2.1% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 371.12ms | 605.34ms | 1.6× | 3.3% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 356.71ms | 579.27ms | 1.6× | 2.2% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 608.53ms | 987.66ms | 1.6× | 1.5% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 1.87s | 1.76s | 0.9× | 2.1% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 1.90s | 1.82s | 1.0× | 2.3% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 1.84s | 1.78s | 1.0× | 2.0% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 3.18s | 2.99s | 0.9× | 1.4% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 752.38ms | 721.91ms | 1.0× | 3.4% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.10s | 1.03s | 0.9× | 4.6% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 966.88ms | 941.80ms | 1.0× | 6.7% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 822.52ms | 1.08s | 1.3× | 2.1% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 1.68s | 1.71s | 1.0× | 1.8% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.02s | 1.96s | 1.0× | 2.4% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 1.75s | 1.79s | 1.0× | 2.1% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.80s | 2.95s | 1.1× | 1.7% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 189.15ms | 544.79ms | 2.9× | 5.8% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 188.09ms | 501.15ms | 2.7× | 5.4% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 224.72ms | 523.31ms | 2.3× | 6.2% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 320.73ms | 874.14ms | 2.7× | 8.7% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 16.79ms | 19.80ms | 1.2× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 6.56ms | 6.74ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 6.07ms | 6.94ms | 1.1× | 2.7% | PASS |
| mem_reads | `oltp_order_range` | 1.69ms | 1.78ms | 1.1× | 2.4% | PASS |
| mem_reads | `oltp_distinct_range` | 2.21ms | 2.32ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_index_scan` | 2.58ms | 3.50ms | 1.4× | 3.4% | PASS |
| mem_reads | `select_random_points` | 8.49ms | 9.13ms | 1.1× | 4.2% | PASS |
| mem_reads | `select_random_ranges` | 2.06ms | 2.62ms | 1.3× | 2.6% | PASS |
| mem_reads | `covering_index_scan` | 2.43ms | 2.64ms | 1.1× | 3.4% | PASS |
| mem_reads | `groupby_scan` | 17.28ms | 18.37ms | 1.1× | 2.0% | PASS |
| mem_reads | `index_join` | 3.48ms | 4.99ms | 1.4× | 4.8% | PASS |
| mem_reads | `index_join_scan` | 2.29ms | 3.56ms | 1.6× | 5.7% | PASS |
| mem_reads | `types_table_scan` | 664.78ms | 698.20ms | 1.1× | 0.9% | PASS |
| mem_reads | `table_scan` | 758.45ms | 777.36ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_read_only` | 53.07ms | 60.30ms | 1.1× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 103.22ms | 151.46ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 8.77ms | 15.74ms | 1.8× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 28.54ms | 54.00ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 21.42ms | 35.08ms | 1.6× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 26.73ms | 43.60ms | 1.6× | 2.4% | PASS |
| mem_writes | `oltp_write_only` | 13.71ms | 28.67ms | 2.1× | 3.0% | PASS |
| mem_writes | `types_delete_insert` | 15.73ms | 23.32ms | 1.5× | 3.6% | PASS |
| mem_writes | `oltp_read_write` | 48.05ms | 75.50ms | 1.6× | 3.8% | PASS |
| file_reads | `oltp_point_select` | 77.13ms | 36.51ms | 0.5× | 1.7% | PASS |
| file_reads | `oltp_range_select` | 13.85ms | 9.28ms | 0.7× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 13.43ms | 9.34ms | 0.7× | 2.2% | PASS |
| file_reads | `oltp_order_range` | 2.42ms | 2.04ms | 0.8× | 2.3% | PASS |
| file_reads | `oltp_distinct_range` | 2.88ms | 2.52ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 9.05ms | 5.53ms | 0.6× | 1.7% | PASS |
| file_reads | `select_random_points` | 15.28ms | 11.18ms | 0.7× | 3.2% | PASS |
| file_reads | `select_random_ranges` | 8.05ms | 4.26ms | 0.5× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 9.03ms | 4.76ms | 0.5× | 2.6% | PASS |
| file_reads | `groupby_scan` | 19.31ms | 19.44ms | 1.0× | 2.1% | PASS |
| file_reads | `index_join` | 7.51ms | 7.30ms | 1.0× | 5.3% | PASS |
| file_reads | `index_join_scan` | 3.10ms | 3.91ms | 1.3× | 4.4% | PASS |
| file_reads | `types_table_scan` | 700.00ms | 725.55ms | 1.0× | 1.4% | PASS |
| file_reads | `table_scan` | 835.50ms | 823.38ms | 1.0× | 3.0% | PASS |
| file_reads | `oltp_read_only` | 151.82ms | 93.80ms | 0.6× | 1.7% | PASS |
| file_writes | `oltp_bulk_insert` | 149.90ms | 208.21ms | 1.4× | 3.4% | PASS |
| file_writes | `oltp_insert` | 19.82ms | 28.73ms | 1.4× | 2.6% | PASS |
| file_writes | `oltp_update_index` | 121.60ms | 109.64ms | 0.9× | 3.3% | PASS |
| file_writes | `oltp_update_non_index` | 100.86ms | 78.42ms | 0.8× | 0.5% | PASS |
| file_writes | `oltp_delete_insert` | 105.86ms | 88.89ms | 0.8× | 3.4% | PASS |
| file_writes | `oltp_write_only` | 80.83ms | 60.46ms | 0.7× | 7.9% | PASS |
| file_writes | `types_delete_insert` | 62.97ms | 48.60ms | 0.8× | 12.2% | PASS |
| file_writes | `oltp_read_write` | 110.54ms | 98.97ms | 0.9× | 8.7% | PASS |
| ac_reads | `oltp_point_select` | 34.24ms | 34.02ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 8.62ms | 8.39ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_sum_range` | 8.07ms | 8.39ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 1.93ms | 1.94ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 2.46ms | 2.47ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 4.86ms | 5.25ms | 1.1× | 1.8% | PASS |
| ac_reads | `select_random_points` | 9.68ms | 10.45ms | 1.1× | 4.4% | PASS |
| ac_reads | `select_random_ranges` | 3.97ms | 4.12ms | 1.0× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 4.83ms | 4.42ms | 0.9× | 3.6% | PASS |
| ac_reads | `groupby_scan` | 17.82ms | 18.80ms | 1.1× | 1.5% | PASS |
| ac_reads | `index_join` | 4.82ms | 6.18ms | 1.3× | 4.1% | PASS |
| ac_reads | `index_join_scan` | 2.54ms | 3.72ms | 1.5× | 4.4% | PASS |
| ac_reads | `types_table_scan` | 686.95ms | 709.20ms | 1.0× | 1.0% | PASS |
| ac_reads | `table_scan` | 794.62ms | 805.69ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_read_only` | 90.28ms | 89.57ms | 1.0× | 2.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 22.49ms | 56.41ms | 2.5× | 7.2% | PASS |
| ac_writes | `oltp_insert_ac` | 24.07ms | 69.37ms | 2.9× | 5.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.02ms | 75.43ms | 3.0× | 6.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.31ms | 62.92ms | 2.8× | 7.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.74ms | 70.76ms | 3.0× | 4.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.00ms | 70.00ms | 2.9× | 4.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.77ms | 66.38ms | 3.0× | 5.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.75ms | 73.52ms | 2.9× | 4.8% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 26.47ms | 24.86ms | 0.9× | 2.8% | PASS |
| mem_reads | `oltp_range_select` | 12.08ms | 9.47ms | 0.8× | 2.6% | PASS |
| mem_reads | `oltp_sum_range` | 12.26ms | 9.90ms | 0.8× | 3.2% | PASS |
| mem_reads | `oltp_order_range` | 2.51ms | 2.16ms | 0.9× | 2.2% | PASS |
| mem_reads | `oltp_distinct_range` | 2.92ms | 2.65ms | 0.9× | 2.6% | PASS |
| mem_reads | `oltp_index_scan` | 2.79ms | 4.40ms | 1.6× | 3.1% | PASS |
| mem_reads | `select_random_points` | 18.20ms | 15.47ms | 0.9× | 3.0% | PASS |
| mem_reads | `select_random_ranges` | 2.54ms | 3.65ms | 1.4× | 3.1% | PASS |
| mem_reads | `covering_index_scan` | 2.51ms | 3.06ms | 1.2× | 5.2% | PASS |
| mem_reads | `groupby_scan` | 22.39ms | 20.87ms | 0.9× | 1.2% | PASS |
| mem_reads | `index_join` | 8.56ms | 6.37ms | 0.7× | 6.3% | PASS |
| mem_reads | `index_join_scan` | 2.92ms | 4.81ms | 1.6× | 2.9% | PASS |
| mem_reads | `types_table_scan` | 726.65ms | 774.56ms | 1.1× | 1.1% | PASS |
| mem_reads | `table_scan` | 793.86ms | 815.54ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 78.67ms | 72.25ms | 0.9× | 2.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 137.82ms | 204.99ms | 1.5× | 2.3% | PASS |
| mem_writes | `oltp_insert` | 10.66ms | 22.23ms | 2.1× | 2.1% | PASS |
| mem_writes | `oltp_update_index` | 44.26ms | 91.34ms | 2.1× | 4.0% | PASS |
| mem_writes | `oltp_update_non_index` | 34.12ms | 55.17ms | 1.6× | 2.5% | PASS |
| mem_writes | `oltp_delete_insert` | 38.77ms | 74.49ms | 1.9× | 4.3% | PASS |
| mem_writes | `oltp_write_only` | 18.86ms | 39.35ms | 2.1× | 3.6% | PASS |
| mem_writes | `types_delete_insert` | 25.22ms | 32.61ms | 1.3× | 3.2% | PASS |
| mem_writes | `oltp_read_write` | 61.41ms | 85.15ms | 1.4× | 3.5% | PASS |
| file_reads | `oltp_point_select` | 80.77ms | 37.42ms | 0.5× | 1.9% | PASS |
| file_reads | `oltp_range_select` | 15.88ms | 9.90ms | 0.6× | 2.3% | PASS |
| file_reads | `oltp_sum_range` | 15.60ms | 9.78ms | 0.6× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 2.90ms | 2.15ms | 0.7× | 2.3% | PASS |
| file_reads | `oltp_distinct_range` | 3.78ms | 2.90ms | 0.8× | 4.2% | PASS |
| file_reads | `oltp_index_scan` | 8.48ms | 5.51ms | 0.6× | 4.1% | PASS |
| file_reads | `select_random_points` | 20.99ms | 14.72ms | 0.7× | 2.1% | PASS |
| file_reads | `select_random_ranges` | 8.35ms | 5.08ms | 0.6× | 1.8% | PASS |
| file_reads | `covering_index_scan` | 8.64ms | 4.50ms | 0.5× | 2.7% | PASS |
| file_reads | `groupby_scan` | 21.94ms | 20.32ms | 0.9× | 1.7% | PASS |
| file_reads | `index_join` | 11.66ms | 7.70ms | 0.7× | 4.8% | PASS |
| file_reads | `index_join_scan` | 3.36ms | 4.42ms | 1.3× | 2.0% | PASS |
| file_reads | `types_table_scan` | 691.43ms | 730.30ms | 1.1× | 1.4% | PASS |
| file_reads | `table_scan` | 841.97ms | 869.30ms | 1.0× | 2.7% | PASS |
| file_reads | `oltp_read_only` | 161.94ms | 95.37ms | 0.6× | 3.4% | PASS |
| file_writes | `oltp_bulk_insert` | 220.55ms | 283.17ms | 1.3× | 4.8% | PASS |
| file_writes | `oltp_insert` | 20.01ms | 43.97ms | 2.2× | 9.9% | PASS |
| file_writes | `oltp_update_index` | 152.41ms | 166.19ms | 1.1× | 5.4% | PASS |
| file_writes | `oltp_update_non_index` | 131.48ms | 109.55ms | 0.8× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 175.61ms | 139.27ms | 0.8× | 4.3% | PASS |
| file_writes | `oltp_write_only` | 114.10ms | 89.67ms | 0.8× | 3.4% | PASS |
| file_writes | `types_delete_insert` | 132.69ms | 69.83ms | 0.5× | 11.0% | PASS |
| file_writes | `oltp_read_write` | 151.69ms | 131.77ms | 0.9× | 4.5% | PASS |
| ac_reads | `oltp_point_select` | 45.83ms | 39.76ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_range_select` | 13.84ms | 10.59ms | 0.8× | 2.8% | PASS |
| ac_reads | `oltp_sum_range` | 13.79ms | 10.77ms | 0.8× | 2.0% | PASS |
| ac_reads | `oltp_order_range` | 2.75ms | 2.38ms | 0.9× | 2.7% | PASS |
| ac_reads | `oltp_distinct_range` | 3.36ms | 2.92ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_index_scan` | 5.13ms | 5.95ms | 1.2× | 1.1% | PASS |
| ac_reads | `select_random_points` | 18.68ms | 15.45ms | 0.8× | 2.8% | PASS |
| ac_reads | `select_random_ranges` | 4.66ms | 5.20ms | 1.1× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 4.93ms | 4.91ms | 1.0× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 21.02ms | 20.10ms | 1.0× | 1.8% | PASS |
| ac_reads | `index_join` | 9.42ms | 7.34ms | 0.8× | 3.1% | PASS |
| ac_reads | `index_join_scan` | 3.27ms | 4.66ms | 1.4× | 2.8% | PASS |
| ac_reads | `types_table_scan` | 839.21ms | 836.75ms | 1.0× | 2.8% | PASS |
| ac_reads | `table_scan` | 912.62ms | 897.20ms | 1.0× | 2.4% | PASS |
| ac_reads | `oltp_read_only` | 117.69ms | 99.28ms | 0.8× | 2.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.22ms | 51.01ms | 2.4× | 3.7% | PASS |
| ac_writes | `oltp_insert_ac` | 24.08ms | 60.29ms | 2.5× | 4.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.32ms | 70.12ms | 2.9× | 3.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.32ms | 58.14ms | 2.7× | 6.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 23.20ms | 64.59ms | 2.8× | 6.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.95ms | 63.94ms | 2.8× | 6.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.94ms | 59.74ms | 2.7× | 7.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.07ms | 73.33ms | 2.5× | 4.4% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 22.40ms | 22.68ms | 1.0× | 3.0% | PASS |
| mem_reads | `oltp_range_select` | 9.71ms | 8.46ms | 0.9× | 3.8% | PASS |
| mem_reads | `oltp_sum_range` | 9.35ms | 8.48ms | 0.9× | 4.4% | PASS |
| mem_reads | `oltp_order_range` | 2.10ms | 1.99ms | 0.9× | 2.2% | PASS |
| mem_reads | `oltp_distinct_range` | 2.63ms | 2.52ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_index_scan` | 2.61ms | 4.11ms | 1.6× | 2.6% | PASS |
| mem_reads | `select_random_points` | 14.40ms | 13.30ms | 0.9× | 5.1% | PASS |
| mem_reads | `select_random_ranges` | 2.15ms | 3.34ms | 1.6× | 3.6% | PASS |
| mem_reads | `covering_index_scan` | 2.31ms | 2.59ms | 1.1× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 18.99ms | 18.77ms | 1.0× | 1.6% | PASS |
| mem_reads | `index_join` | 6.88ms | 5.16ms | 0.7× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 2.03ms | 4.07ms | 2.0× | 2.6% | PASS |
| mem_reads | `types_table_scan` | 678.45ms | 720.18ms | 1.1× | 0.9% | PASS |
| mem_reads | `table_scan` | 763.41ms | 791.95ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_read_only` | 71.13ms | 69.56ms | 1.0× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 136.95ms | 198.02ms | 1.4× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 10.55ms | 21.72ms | 2.1× | 1.3% | PASS |
| mem_writes | `oltp_update_index` | 40.66ms | 85.76ms | 2.1× | 2.9% | PASS |
| mem_writes | `oltp_update_non_index` | 31.17ms | 51.10ms | 1.6× | 2.5% | PASS |
| mem_writes | `oltp_delete_insert` | 32.55ms | 62.99ms | 1.9× | 2.0% | PASS |
| mem_writes | `oltp_write_only` | 18.27ms | 38.51ms | 2.1× | 2.5% | PASS |
| mem_writes | `types_delete_insert` | 25.48ms | 33.79ms | 1.3× | 2.0% | PASS |
| mem_writes | `oltp_read_write` | 61.08ms | 87.38ms | 1.4× | 2.9% | PASS |
| file_reads | `oltp_point_select` | 80.39ms | 37.92ms | 0.5× | 1.7% | PASS |
| file_reads | `oltp_range_select` | 15.77ms | 9.89ms | 0.6× | 2.3% | PASS |
| file_reads | `oltp_sum_range` | 15.20ms | 9.83ms | 0.6× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 2.82ms | 2.21ms | 0.8× | 3.0% | PASS |
| file_reads | `oltp_distinct_range` | 3.22ms | 2.66ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 8.61ms | 5.69ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 20.66ms | 14.67ms | 0.7× | 3.5% | PASS |
| file_reads | `select_random_ranges` | 8.34ms | 5.15ms | 0.6× | 1.3% | PASS |
| file_reads | `covering_index_scan` | 8.72ms | 4.60ms | 0.5× | 3.5% | PASS |
| file_reads | `groupby_scan` | 20.62ms | 19.72ms | 1.0× | 2.0% | PASS |
| file_reads | `index_join` | 11.02ms | 7.41ms | 0.7× | 3.5% | PASS |
| file_reads | `index_join_scan` | 3.28ms | 4.28ms | 1.3× | 1.7% | PASS |
| file_reads | `types_table_scan` | 698.53ms | 742.12ms | 1.1× | 2.0% | PASS |
| file_reads | `table_scan` | 789.23ms | 822.42ms | 1.0× | 2.0% | PASS |
| file_reads | `oltp_read_only` | 154.16ms | 93.98ms | 0.6× | 2.4% | PASS |
| file_writes | `oltp_bulk_insert` | 199.40ms | 261.08ms | 1.3× | 4.6% | PASS |
| file_writes | `oltp_insert` | 19.21ms | 39.06ms | 2.0× | 11.0% | PASS |
| file_writes | `oltp_update_index` | 140.84ms | 159.47ms | 1.1× | 5.2% | PASS |
| file_writes | `oltp_update_non_index` | 120.27ms | 101.12ms | 0.8× | 5.5% | PASS |
| file_writes | `oltp_delete_insert` | 140.89ms | 119.56ms | 0.8× | 8.8% | PASS |
| file_writes | `oltp_write_only` | 104.15ms | 79.42ms | 0.8× | 7.8% | PASS |
| file_writes | `types_delete_insert` | 101.80ms | 59.91ms | 0.6× | 8.7% | PASS |
| file_writes | `oltp_read_write` | 140.33ms | 122.18ms | 0.9× | 5.5% | PASS |
| ac_reads | `oltp_point_select` | 38.74ms | 37.07ms | 1.0× | 1.6% | PASS |
| ac_reads | `oltp_range_select` | 11.63ms | 9.69ms | 0.8× | 2.9% | PASS |
| ac_reads | `oltp_sum_range` | 11.25ms | 9.76ms | 0.9× | 3.5% | PASS |
| ac_reads | `oltp_order_range` | 2.24ms | 2.12ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_distinct_range` | 2.74ms | 2.63ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_index_scan` | 4.31ms | 5.37ms | 1.2× | 2.1% | PASS |
| ac_reads | `select_random_points` | 15.25ms | 14.10ms | 0.9× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 4.01ms | 4.90ms | 1.2× | 1.7% | PASS |
| ac_reads | `covering_index_scan` | 4.25ms | 4.40ms | 1.0× | 3.0% | PASS |
| ac_reads | `groupby_scan` | 20.15ms | 19.30ms | 1.0× | 2.3% | PASS |
| ac_reads | `index_join` | 8.85ms | 7.14ms | 0.8× | 4.9% | PASS |
| ac_reads | `index_join_scan` | 2.77ms | 4.26ms | 1.5× | 1.6% | PASS |
| ac_reads | `types_table_scan` | 693.56ms | 733.16ms | 1.1× | 1.7% | PASS |
| ac_reads | `table_scan` | 830.55ms | 840.81ms | 1.0× | 2.8% | PASS |
| ac_reads | `oltp_read_only` | 100.19ms | 92.31ms | 0.9× | 2.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 27.25ms | 52.76ms | 1.9× | 5.6% | PASS |
| ac_writes | `oltp_insert_ac` | 29.46ms | 67.21ms | 2.3× | 6.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 30.02ms | 72.33ms | 2.4× | 5.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 25.56ms | 62.16ms | 2.4× | 7.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 29.21ms | 68.07ms | 2.3× | 6.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 28.26ms | 66.74ms | 2.4× | 6.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.71ms | 60.68ms | 2.7× | 7.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.26ms | 73.36ms | 2.3× | 8.4% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.66ms | 41.62ms | 1.2× | 2.1% | PASS |
| mem_reads | `oltp_range_select` | 20.50ms | 21.36ms | 1.0× | 2.0% | PASS |
| mem_reads | `oltp_sum_range` | 18.00ms | 20.60ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 3.54ms | 3.85ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.69ms | 5.00ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.50ms | 6.05ms | 1.3× | 2.4% | PASS |
| mem_reads | `select_random_points` | 28.22ms | 32.79ms | 1.2× | 2.2% | PASS |
| mem_reads | `select_random_ranges` | 7.75ms | 8.98ms | 1.2× | 1.5% | PASS |
| mem_reads | `covering_index_scan` | 4.18ms | 4.29ms | 1.0× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 36.48ms | 39.79ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 7.94ms | 10.49ms | 1.3× | 3.1% | PASS |
| mem_reads | `index_join_scan` | 3.90ms | 5.57ms | 1.4× | 2.8% | PASS |
| mem_reads | `types_table_scan` | 1.11s | 1.20s | 1.1× | 2.6% | PASS |
| mem_reads | `table_scan` | 1.34s | 1.30s | 1.0× | 2.8% | PASS |
| mem_reads | `oltp_read_only` | 153.83ms | 171.64ms | 1.1× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 248.23ms | 353.21ms | 1.4× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 19.10ms | 35.41ms | 1.9× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 69.12ms | 132.28ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_update_non_index` | 53.98ms | 87.68ms | 1.6× | 1.4% | PASS |
| mem_writes | `oltp_delete_insert` | 51.55ms | 99.63ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 27.48ms | 59.74ms | 2.2× | 1.4% | PASS |
| mem_writes | `types_delete_insert` | 33.60ms | 54.34ms | 1.6× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 105.46ms | 165.38ms | 1.6× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 109.40ms | 61.28ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_range_select` | 27.88ms | 23.30ms | 0.8× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 26.37ms | 22.96ms | 0.9× | 2.1% | PASS |
| file_reads | `oltp_order_range` | 4.57ms | 4.22ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_distinct_range` | 5.72ms | 5.39ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_index_scan` | 12.27ms | 8.42ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 38.75ms | 36.43ms | 0.9× | 1.5% | PASS |
| file_reads | `select_random_ranges` | 15.87ms | 11.19ms | 0.7× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 12.25ms | 6.79ms | 0.6× | 1.9% | PASS |
| file_reads | `groupby_scan` | 38.31ms | 40.60ms | 1.1× | 0.7% | PASS |
| file_reads | `index_join` | 12.61ms | 12.54ms | 1.0× | 2.3% | PASS |
| file_reads | `index_join_scan` | 5.08ms | 6.21ms | 1.2× | 2.6% | PASS |
| file_reads | `types_table_scan` | 1.18s | 1.22s | 1.0× | 0.6% | PASS |
| file_reads | `table_scan` | 1.43s | 1.33s | 0.9× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 271.53ms | 206.38ms | 0.8× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 263.16ms | 366.45ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 29.18ms | 41.28ms | 1.4× | 1.9% | PASS |
| file_writes | `oltp_update_index` | 103.33ms | 148.00ms | 1.4× | 2.2% | PASS |
| file_writes | `oltp_update_non_index` | 85.21ms | 102.27ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 86.41ms | 113.99ms | 1.3× | 2.7% | PASS |
| file_writes | `oltp_write_only` | 60.23ms | 70.71ms | 1.2× | 2.4% | PASS |
| file_writes | `types_delete_insert` | 52.80ms | 61.09ms | 1.2× | 2.1% | PASS |
| file_writes | `oltp_read_write` | 142.20ms | 178.15ms | 1.3× | 2.5% | PASS |
| ac_reads | `oltp_point_select` | 60.26ms | 62.77ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 23.89ms | 23.84ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_sum_range` | 21.37ms | 23.06ms | 1.1× | 2.0% | PASS |
| ac_reads | `oltp_order_range` | 4.04ms | 4.27ms | 1.1× | 2.2% | PASS |
| ac_reads | `oltp_distinct_range` | 5.19ms | 5.43ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 7.44ms | 8.63ms | 1.2× | 1.3% | PASS |
| ac_reads | `select_random_points` | 32.42ms | 36.73ms | 1.1× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 10.61ms | 11.22ms | 1.1× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 7.01ms | 6.68ms | 1.0× | 2.0% | PASS |
| ac_reads | `groupby_scan` | 37.31ms | 40.16ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 9.84ms | 12.55ms | 1.3× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 4.39ms | 5.95ms | 1.4× | 1.6% | PASS |
| ac_reads | `types_table_scan` | 1.13s | 1.21s | 1.1× | 1.8% | PASS |
| ac_reads | `table_scan` | 1.25s | 1.29s | 1.0× | 3.9% | PASS |
| ac_reads | `oltp_read_only` | 193.18ms | 203.93ms | 1.1× | 1.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 37.76ms | 94.21ms | 2.5× | 10.1% | PASS |
| ac_writes | `oltp_insert_ac` | 39.64ms | 109.93ms | 2.8× | 7.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 41.54ms | 123.93ms | 3.0× | 11.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 40.01ms | 106.76ms | 2.7× | 11.5% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 37.91ms | 109.69ms | 2.9× | 6.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 39.89ms | 108.10ms | 2.7× | 7.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 36.62ms | 102.70ms | 2.8× | 11.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 47.35ms | 118.83ms | 2.5× | 7.3% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 13s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 95.92ms | 130.00ms | 73.8% | 0.4% | PASS |
| `status_dirty_many_tables` | 98.91ms | 130.00ms | 76.1% | 0.3% | PASS |
| `diff_regular_working_one_table` | 90.48ms | 120.00ms | 75.4% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 103.64ms | 140.00ms | 74.0% | 0.3% | PASS |
| `diff_stat_working_many_tables` | 103.59ms | 140.00ms | 74.0% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 104.78ms | 140.00ms | 74.8% | 0.4% | PASS |
| `branch_list_many_branches` | 23.07ms | 35.00ms | 65.9% | 0.7% | PASS |
| `branch_create_delete` | 25.22ms | 40.00ms | 63.1% | 1.1% | PASS |
| `checkout_branch_clean` | 60.71ms | 150.00ms | 40.5% | 0.7% | PASS |
| `merge_data_no_conflicts` | 28.98ms | 50.00ms | 58.0% | 1.0% | PASS |
| `merge_schema_no_conflicts` | 22.09ms | 35.00ms | 63.1% | 1.4% | PASS |
| `merge_data_conflicts` | 32.65ms | 180.00ms | 18.1% | 0.8% | PASS |
| `merge_data_conflicts_with_resolve` | 32.67ms | 180.00ms | 18.2% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
