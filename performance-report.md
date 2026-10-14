# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-10-14 16:09 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20261012.334.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/38582016977)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.12s | 9.56s | 1.0× | 1.8% | **PASS** |
| Writes | 1.91s | 3.08s | 1.6× | 1.5% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.08s | 9.81s | 1.0× | 1.5% | **PASS** |
| Writes | 3.77s | 4.13s | 1.1× | 2.8% | **PASS** |
| Autocommit writes | 871.10ms | 2.83s | 3.2× | 6.7% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.51s | 2.61s | 1.0× | 1.6% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.26s | 2.28s | 1.0× | 1.8% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 1.78s | 1.84s | 1.0× | 3.0% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.56s | 2.83s | 1.1× | 1.5% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 445.26ms | 741.67ms | 1.7× | 1.5% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 481.58ms | 750.07ms | 1.6× | 1.6% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 385.08ms | 631.36ms | 1.6× | 2.8% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 593.43ms | 957.68ms | 1.6× | 1.3% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.75s | 2.67s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.49s | 2.32s | 0.9× | 1.0% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.00s | 1.91s | 1.0× | 2.4% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.83s | 2.90s | 1.0× | 1.5% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 608.38ms | 806.43ms | 1.3× | 1.8% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.37s | 1.24s | 0.9× | 14.6% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.02s | 1.04s | 1.0× | 5.0% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 766.24ms | 1.05s | 1.4× | 1.3% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.62s | 2.68s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.30s | 2.32s | 1.0× | 1.1% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.12s | 2.10s | 1.0× | 2.4% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.68s | 2.90s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 212.92ms | 675.20ms | 3.2× | 6.7% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 251.70ms | 1.02s | 4.1× | 55.6% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 210.56ms | 529.28ms | 2.5× | 6.3% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 195.91ms | 601.05ms | 3.1× | 5.4% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.18ms | 30.34ms | 1.3× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 10.15ms | 11.55ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_sum_range` | 9.61ms | 11.26ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 2.60ms | 2.81ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.67ms | 3.96ms | 1.1× | 0.9% | PASS |
| mem_reads | `oltp_index_scan` | 3.93ms | 5.31ms | 1.4× | 2.0% | PASS |
| mem_reads | `select_random_points` | 9.98ms | 10.94ms | 1.1× | 2.7% | PASS |
| mem_reads | `select_random_ranges` | 3.02ms | 3.99ms | 1.3× | 1.9% | PASS |
| mem_reads | `covering_index_scan` | 4.24ms | 4.35ms | 1.0× | 1.8% | PASS |
| mem_reads | `groupby_scan` | 30.62ms | 33.08ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 5.82ms | 7.92ms | 1.4× | 1.8% | PASS |
| mem_reads | `index_join_scan` | 3.20ms | 4.49ms | 1.4× | 2.0% | PASS |
| mem_reads | `types_table_scan` | 1.07s | 1.13s | 1.1× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.22s | 1.23s | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_read_only` | 104.97ms | 118.87ms | 1.1× | 1.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.01ms | 277.30ms | 1.5× | 1.2% | PASS |
| mem_writes | `oltp_insert` | 15.49ms | 28.60ms | 1.8× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 50.45ms | 97.65ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 35.05ms | 60.77ms | 1.7× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 45.28ms | 75.40ms | 1.7× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 22.22ms | 47.97ms | 2.2× | 2.1% | PASS |
| mem_writes | `types_delete_insert` | 24.61ms | 38.89ms | 1.6× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 68.14ms | 115.10ms | 1.7× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 100.00ms | 49.86ms | 0.5× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 18.28ms | 13.63ms | 0.7× | 1.7% | PASS |
| file_reads | `oltp_sum_range` | 17.80ms | 13.44ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 3.59ms | 3.20ms | 0.9× | 2.7% | PASS |
| file_reads | `oltp_distinct_range` | 4.66ms | 4.33ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_index_scan` | 11.99ms | 7.58ms | 0.6× | 1.4% | PASS |
| file_reads | `select_random_points` | 18.21ms | 13.21ms | 0.7× | 2.0% | PASS |
| file_reads | `select_random_ranges` | 10.65ms | 5.99ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 12.24ms | 6.57ms | 0.5× | 1.5% | PASS |
| file_reads | `groupby_scan` | 32.02ms | 33.61ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 10.39ms | 9.65ms | 0.9× | 2.1% | PASS |
| file_reads | `index_join_scan` | 4.23ms | 4.82ms | 1.1× | 1.7% | PASS |
| file_reads | `types_table_scan` | 1.08s | 1.13s | 1.1× | 1.3% | PASS |
| file_reads | `table_scan` | 1.21s | 1.23s | 1.0× | 1.1% | PASS |
| file_reads | `oltp_read_only` | 217.60ms | 149.18ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 197.21ms | 286.93ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_insert` | 23.03ms | 32.86ms | 1.4× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 78.42ms | 108.19ms | 1.4× | 2.7% | PASS |
| file_writes | `oltp_update_non_index` | 59.76ms | 71.55ms | 1.2× | 2.0% | PASS |
| file_writes | `oltp_delete_insert` | 69.36ms | 84.19ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 45.91ms | 55.48ms | 1.2× | 2.4% | PASS |
| file_writes | `types_delete_insert` | 40.83ms | 45.16ms | 1.1× | 2.3% | PASS |
| file_writes | `oltp_read_write` | 93.87ms | 122.06ms | 1.3× | 1.5% | PASS |
| ac_reads | `oltp_point_select` | 48.90ms | 49.56ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_range_select` | 13.10ms | 13.68ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 12.41ms | 13.49ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 3.05ms | 3.17ms | 1.0× | 2.2% | PASS |
| ac_reads | `oltp_distinct_range` | 4.07ms | 4.30ms | 1.1× | 1.6% | PASS |
| ac_reads | `oltp_index_scan` | 6.76ms | 7.60ms | 1.1× | 1.4% | PASS |
| ac_reads | `select_random_points` | 12.89ms | 13.27ms | 1.0× | 0.8% | PASS |
| ac_reads | `select_random_ranges` | 5.63ms | 5.98ms | 1.1× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 7.25ms | 6.71ms | 0.9× | 1.5% | PASS |
| ac_reads | `groupby_scan` | 31.27ms | 33.69ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 7.64ms | 9.69ms | 1.3× | 1.9% | PASS |
| ac_reads | `index_join_scan` | 3.69ms | 4.88ms | 1.3× | 1.5% | PASS |
| ac_reads | `types_table_scan` | 1.09s | 1.14s | 1.0× | 1.5% | PASS |
| ac_reads | `table_scan` | 1.23s | 1.23s | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 141.63ms | 148.92ms | 1.1× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.56ms | 67.94ms | 2.9× | 7.6% | PASS |
| ac_writes | `oltp_insert_ac` | 26.32ms | 83.42ms | 3.2× | 5.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.42ms | 96.13ms | 3.3× | 7.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.91ms | 77.27ms | 3.1× | 5.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.65ms | 88.16ms | 3.3× | 7.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.71ms | 86.97ms | 3.3× | 6.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.01ms | 78.72ms | 3.4× | 5.8% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.35ms | 96.59ms | 3.0× | 8.3% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.44ms | 30.21ms | 0.9× | 3.7% | PASS |
| mem_reads | `oltp_range_select` | 16.52ms | 11.58ms | 0.7× | 2.7% | PASS |
| mem_reads | `oltp_sum_range` | 13.58ms | 10.91ms | 0.8× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 2.91ms | 2.60ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.74ms | 3.45ms | 0.9× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.29ms | 4.89ms | 1.5× | 1.2% | PASS |
| mem_reads | `select_random_points` | 19.12ms | 16.60ms | 0.9× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 3.04ms | 4.32ms | 1.4× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 3.37ms | 3.77ms | 1.1× | 3.2% | PASS |
| mem_reads | `groupby_scan` | 29.68ms | 29.07ms | 1.0× | 0.8% | PASS |
| mem_reads | `index_join` | 8.88ms | 7.11ms | 0.8× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 3.16ms | 4.79ms | 1.5× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 909.12ms | 966.65ms | 1.1× | 1.1% | PASS |
| mem_reads | `table_scan` | 1.09s | 1.08s | 1.0× | 3.2% | PASS |
| mem_reads | `oltp_read_only` | 120.85ms | 105.86ms | 0.9× | 2.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 191.05ms | 269.93ms | 1.4× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 14.41ms | 28.50ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 53.63ms | 109.59ms | 2.0× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 41.73ms | 64.57ms | 1.5× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 45.49ms | 81.33ms | 1.8× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 23.48ms | 47.64ms | 2.0× | 2.2% | PASS |
| mem_writes | `types_delete_insert` | 31.85ms | 38.85ms | 1.2× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 79.94ms | 109.66ms | 1.4× | 2.9% | PASS |
| file_reads | `oltp_point_select` | 104.76ms | 46.19ms | 0.4× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 22.68ms | 12.81ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_sum_range` | 21.21ms | 12.74ms | 0.6× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.77ms | 2.81ms | 0.7× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.61ms | 3.70ms | 0.8× | 0.7% | PASS |
| file_reads | `oltp_index_scan` | 11.02ms | 6.84ms | 0.6× | 0.4% | PASS |
| file_reads | `select_random_points` | 26.38ms | 18.18ms | 0.7× | 1.1% | PASS |
| file_reads | `select_random_ranges` | 10.56ms | 6.13ms | 0.6× | 0.5% | PASS |
| file_reads | `covering_index_scan` | 11.27ms | 5.84ms | 0.5× | 0.7% | PASS |
| file_reads | `groupby_scan` | 30.27ms | 29.39ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join` | 13.53ms | 8.92ms | 0.7× | 1.0% | PASS |
| file_reads | `index_join_scan` | 4.10ms | 5.08ms | 1.2× | 0.8% | PASS |
| file_reads | `types_table_scan` | 933.28ms | 968.22ms | 1.0× | 2.0% | PASS |
| file_reads | `table_scan` | 1.07s | 1.06s | 1.0× | 2.2% | PASS |
| file_reads | `oltp_read_only` | 223.16ms | 129.43ms | 0.6× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 280.15ms | 347.75ms | 1.2× | 7.7% | PASS |
| file_writes | `oltp_insert` | 27.84ms | 49.14ms | 1.8× | 20.5% | PASS |
| file_writes | `oltp_update_index` | 194.23ms | 198.62ms | 1.0× | 10.2% | PASS |
| file_writes | `oltp_update_non_index` | 181.48ms | 123.99ms | 0.7× | 16.8% | PASS |
| file_writes | `oltp_delete_insert` | 203.10ms | 159.13ms | 0.8× | 15.5% | PASS |
| file_writes | `oltp_write_only` | 127.18ms | 103.94ms | 0.8× | 13.7% | PASS |
| file_writes | `types_delete_insert` | 173.70ms | 91.11ms | 0.5× | 25.5% | PASS |
| file_writes | `oltp_read_write` | 187.13ms | 168.23ms | 0.9× | 9.3% | PASS |
| ac_reads | `oltp_point_select` | 54.86ms | 46.44ms | 0.8× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 18.11ms | 12.90ms | 0.7× | 1.4% | PASS |
| ac_reads | `oltp_sum_range` | 16.51ms | 12.89ms | 0.8× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.36ms | 2.82ms | 0.8× | 1.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.17ms | 3.70ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 6.16ms | 6.90ms | 1.1× | 0.9% | PASS |
| ac_reads | `select_random_points` | 21.75ms | 18.45ms | 0.8× | 1.8% | PASS |
| ac_reads | `select_random_ranges` | 5.67ms | 6.17ms | 1.1× | 0.7% | PASS |
| ac_reads | `covering_index_scan` | 6.30ms | 5.87ms | 0.9× | 0.7% | PASS |
| ac_reads | `groupby_scan` | 29.93ms | 29.38ms | 1.0× | 0.9% | PASS |
| ac_reads | `index_join` | 11.21ms | 8.96ms | 0.8× | 0.9% | PASS |
| ac_reads | `index_join_scan` | 3.68ms | 5.04ms | 1.4× | 0.8% | PASS |
| ac_reads | `types_table_scan` | 913.22ms | 960.88ms | 1.1× | 1.4% | PASS |
| ac_reads | `table_scan` | 1.06s | 1.07s | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_read_only` | 148.11ms | 129.60ms | 0.9× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.67ms | 78.79ms | 3.3× | 52.8% | PASS |
| ac_writes | `oltp_insert_ac` | 28.22ms | 124.84ms | 4.4× | 44.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 35.47ms | 152.22ms | 4.3× | 61.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 26.52ms | 149.98ms | 5.7× | 72.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 43.09ms | 119.66ms | 2.8× | 49.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.92ms | 158.39ms | 5.7× | 53.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 31.93ms | 105.15ms | 3.3× | 77.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 34.88ms | 134.41ms | 3.9× | 58.1% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 22.88ms | 23.38ms | 1.0× | 3.7% | PASS |
| mem_reads | `oltp_range_select` | 10.33ms | 8.69ms | 0.8× | 2.6% | PASS |
| mem_reads | `oltp_sum_range` | 9.67ms | 8.52ms | 0.9× | 2.6% | PASS |
| mem_reads | `oltp_order_range` | 1.92ms | 1.92ms | 1.0× | 3.0% | PASS |
| mem_reads | `oltp_distinct_range` | 2.34ms | 2.36ms | 1.0× | 3.0% | PASS |
| mem_reads | `oltp_index_scan` | 2.30ms | 3.55ms | 1.5× | 2.7% | PASS |
| mem_reads | `select_random_points` | 12.18ms | 12.45ms | 1.0× | 3.7% | PASS |
| mem_reads | `select_random_ranges` | 2.39ms | 3.52ms | 1.5× | 4.0% | PASS |
| mem_reads | `covering_index_scan` | 2.40ms | 2.86ms | 1.2× | 3.6% | PASS |
| mem_reads | `groupby_scan` | 20.16ms | 19.59ms | 1.0× | 1.8% | PASS |
| mem_reads | `index_join` | 7.68ms | 5.55ms | 0.7× | 5.2% | PASS |
| mem_reads | `index_join_scan` | 2.80ms | 4.79ms | 1.7× | 4.6% | PASS |
| mem_reads | `types_table_scan` | 778.03ms | 817.55ms | 1.1× | 1.8% | PASS |
| mem_reads | `table_scan` | 831.17ms | 852.54ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_read_only` | 78.26ms | 73.90ms | 0.9× | 2.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 143.51ms | 211.89ms | 1.5× | 1.8% | PASS |
| mem_writes | `oltp_insert` | 11.51ms | 24.15ms | 2.1× | 1.4% | PASS |
| mem_writes | `oltp_update_index` | 50.44ms | 105.11ms | 2.1× | 3.3% | PASS |
| mem_writes | `oltp_update_non_index` | 33.30ms | 53.46ms | 1.6× | 2.4% | PASS |
| mem_writes | `oltp_delete_insert` | 35.60ms | 68.98ms | 1.9× | 3.2% | PASS |
| mem_writes | `oltp_write_only` | 19.92ms | 42.67ms | 2.1× | 2.4% | PASS |
| mem_writes | `types_delete_insert` | 26.49ms | 34.59ms | 1.3× | 3.8% | PASS |
| mem_writes | `oltp_read_write` | 64.30ms | 90.52ms | 1.4× | 3.8% | PASS |
| file_reads | `oltp_point_select` | 82.34ms | 38.85ms | 0.5× | 3.0% | PASS |
| file_reads | `oltp_range_select` | 17.01ms | 10.49ms | 0.6× | 2.7% | PASS |
| file_reads | `oltp_sum_range` | 17.83ms | 11.37ms | 0.6× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 3.08ms | 2.40ms | 0.8× | 2.6% | PASS |
| file_reads | `oltp_distinct_range` | 3.46ms | 2.83ms | 0.8× | 3.2% | PASS |
| file_reads | `oltp_index_scan` | 8.94ms | 6.09ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 21.74ms | 15.12ms | 0.7× | 4.0% | PASS |
| file_reads | `select_random_ranges` | 8.24ms | 5.19ms | 0.6× | 1.6% | PASS |
| file_reads | `covering_index_scan` | 9.07ms | 5.11ms | 0.6× | 2.4% | PASS |
| file_reads | `groupby_scan` | 22.53ms | 20.83ms | 0.9× | 1.5% | PASS |
| file_reads | `index_join` | 11.40ms | 7.55ms | 0.7× | 2.7% | PASS |
| file_reads | `index_join_scan` | 3.46ms | 4.57ms | 1.3× | 2.1% | PASS |
| file_reads | `types_table_scan` | 763.06ms | 794.78ms | 1.0× | 2.3% | PASS |
| file_reads | `table_scan` | 856.46ms | 880.94ms | 1.0× | 2.7% | PASS |
| file_reads | `oltp_read_only` | 173.81ms | 103.40ms | 0.6× | 2.2% | PASS |
| file_writes | `oltp_bulk_insert` | 219.44ms | 280.78ms | 1.3× | 3.3% | PASS |
| file_writes | `oltp_insert` | 21.88ms | 48.64ms | 2.2× | 11.1% | PASS |
| file_writes | `oltp_update_index` | 151.15ms | 172.96ms | 1.1× | 4.2% | PASS |
| file_writes | `oltp_update_non_index` | 130.77ms | 118.18ms | 0.9× | 5.8% | PASS |
| file_writes | `oltp_delete_insert` | 150.39ms | 129.43ms | 0.9× | 6.1% | PASS |
| file_writes | `oltp_write_only` | 101.05ms | 89.23ms | 0.9× | 2.9% | PASS |
| file_writes | `types_delete_insert` | 105.80ms | 69.54ms | 0.7× | 6.7% | PASS |
| file_writes | `oltp_read_write` | 141.12ms | 129.53ms | 0.9× | 3.7% | PASS |
| ac_reads | `oltp_point_select` | 43.81ms | 39.65ms | 0.9× | 2.4% | PASS |
| ac_reads | `oltp_range_select` | 12.64ms | 10.41ms | 0.8× | 2.7% | PASS |
| ac_reads | `oltp_sum_range` | 11.97ms | 10.33ms | 0.9× | 3.5% | PASS |
| ac_reads | `oltp_order_range` | 2.64ms | 2.32ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 3.11ms | 2.84ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_index_scan` | 5.21ms | 6.14ms | 1.2× | 1.3% | PASS |
| ac_reads | `select_random_points` | 18.48ms | 15.43ms | 0.8× | 4.7% | PASS |
| ac_reads | `select_random_ranges` | 4.51ms | 5.27ms | 1.2× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 4.99ms | 4.95ms | 1.0× | 1.8% | PASS |
| ac_reads | `groupby_scan` | 21.48ms | 20.27ms | 0.9× | 1.5% | PASS |
| ac_reads | `index_join` | 9.98ms | 7.71ms | 0.8× | 4.4% | PASS |
| ac_reads | `index_join_scan` | 3.18ms | 4.62ms | 1.5× | 2.8% | PASS |
| ac_reads | `types_table_scan` | 825.23ms | 848.99ms | 1.0× | 2.5% | PASS |
| ac_reads | `table_scan` | 1.01s | 1.01s | 1.0× | 2.9% | PASS |
| ac_reads | `oltp_read_only` | 137.82ms | 119.68ms | 0.9× | 1.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 24.61ms | 54.37ms | 2.2× | 6.2% | PASS |
| ac_writes | `oltp_insert_ac` | 28.15ms | 67.76ms | 2.4× | 6.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.68ms | 74.90ms | 2.6× | 4.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.09ms | 61.93ms | 2.8× | 6.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 27.61ms | 68.36ms | 2.5× | 8.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.63ms | 69.43ms | 2.6× | 7.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 23.16ms | 63.41ms | 2.7× | 7.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.63ms | 69.14ms | 2.3× | 4.4% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.13ms | 41.21ms | 1.2× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 20.19ms | 21.01ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_sum_range` | 17.68ms | 20.39ms | 1.2× | 1.8% | PASS |
| mem_reads | `oltp_order_range` | 3.44ms | 3.88ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.60ms | 5.09ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_index_scan` | 4.48ms | 6.02ms | 1.3× | 1.7% | PASS |
| mem_reads | `select_random_points` | 27.77ms | 32.28ms | 1.2× | 1.7% | PASS |
| mem_reads | `select_random_ranges` | 7.52ms | 8.91ms | 1.2× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.27ms | 1.0× | 1.5% | PASS |
| mem_reads | `groupby_scan` | 36.32ms | 39.61ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 7.91ms | 10.23ms | 1.3× | 1.5% | PASS |
| mem_reads | `index_join_scan` | 3.50ms | 5.43ms | 1.6× | 2.3% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 1.19s | 1.28s | 1.1× | 0.8% | PASS |
| mem_reads | `oltp_read_only` | 144.40ms | 170.87ms | 1.2× | 0.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 250.55ms | 352.43ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 19.15ms | 34.71ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 64.08ms | 123.81ms | 1.9× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 52.31ms | 84.48ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 49.24ms | 93.82ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 26.45ms | 57.23ms | 2.2× | 1.5% | PASS |
| mem_writes | `types_delete_insert` | 32.72ms | 52.07ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_read_write` | 98.93ms | 159.12ms | 1.6× | 1.9% | PASS |
| file_reads | `oltp_point_select` | 108.17ms | 61.03ms | 0.6× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 26.51ms | 23.28ms | 0.9× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 25.46ms | 22.80ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 4.41ms | 4.21ms | 1.0× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 5.55ms | 5.44ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 12.04ms | 8.33ms | 0.7× | 1.3% | PASS |
| file_reads | `select_random_points` | 37.17ms | 35.77ms | 1.0× | 1.9% | PASS |
| file_reads | `select_random_ranges` | 15.53ms | 11.36ms | 0.7× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 11.76ms | 6.66ms | 0.6× | 1.6% | PASS |
| file_reads | `groupby_scan` | 37.11ms | 39.96ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 12.17ms | 12.25ms | 1.0× | 1.3% | PASS |
| file_reads | `index_join_scan` | 4.95ms | 5.88ms | 1.2× | 1.9% | PASS |
| file_reads | `types_table_scan` | 1.06s | 1.18s | 1.1× | 0.4% | PASS |
| file_reads | `table_scan` | 1.22s | 1.28s | 1.1× | 0.6% | PASS |
| file_reads | `oltp_read_only` | 259.74ms | 203.23ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 266.50ms | 363.81ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 25.82ms | 40.57ms | 1.6× | 1.1% | PASS |
| file_writes | `oltp_update_index` | 93.48ms | 138.24ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 78.12ms | 97.67ms | 1.3× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 75.88ms | 106.35ms | 1.4× | 1.2% | PASS |
| file_writes | `oltp_write_only` | 49.74ms | 67.38ms | 1.4× | 1.6% | PASS |
| file_writes | `types_delete_insert` | 50.33ms | 59.99ms | 1.2× | 1.4% | PASS |
| file_writes | `oltp_read_write` | 126.38ms | 171.42ms | 1.4× | 1.2% | PASS |
| ac_reads | `oltp_point_select` | 58.02ms | 61.15ms | 1.1× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 22.07ms | 23.34ms | 1.1× | 2.2% | PASS |
| ac_reads | `oltp_sum_range` | 20.38ms | 22.79ms | 1.1× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 3.80ms | 4.20ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.88ms | 5.44ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.97ms | 8.38ms | 1.2× | 1.7% | PASS |
| ac_reads | `select_random_points` | 29.95ms | 36.03ms | 1.2× | 1.3% | PASS |
| ac_reads | `select_random_ranges` | 9.96ms | 11.36ms | 1.1× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 6.72ms | 6.46ms | 1.0× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 36.68ms | 40.01ms | 1.1× | 1.1% | PASS |
| ac_reads | `index_join` | 9.54ms | 12.36ms | 1.3× | 1.6% | PASS |
| ac_reads | `index_join_scan` | 4.42ms | 5.87ms | 1.3× | 2.1% | PASS |
| ac_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.4% | PASS |
| ac_reads | `table_scan` | 1.23s | 1.28s | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_read_only` | 183.22ms | 203.55ms | 1.1× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.31ms | 58.95ms | 2.8× | 2.8% | PASS |
| ac_writes | `oltp_insert_ac` | 23.38ms | 76.15ms | 3.3× | 5.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 27.07ms | 86.28ms | 3.2× | 5.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.34ms | 70.31ms | 3.1× | 5.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.71ms | 77.51ms | 3.1× | 5.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.06ms | 77.79ms | 3.2× | 5.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.03ms | 68.59ms | 3.1× | 7.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.00ms | 85.48ms | 2.8× | 5.6% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 11s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 95.51ms | 130.00ms | 73.5% | 0.7% | PASS |
| `status_dirty_many_tables` | 98.81ms | 130.00ms | 76.0% | 0.6% | PASS |
| `diff_regular_working_one_table` | 90.72ms | 120.00ms | 75.6% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 103.88ms | 140.00ms | 74.2% | 0.4% | PASS |
| `diff_stat_working_many_tables` | 103.49ms | 140.00ms | 73.9% | 0.4% | PASS |
| `diff_schema_working_many_tables` | 104.08ms | 140.00ms | 74.3% | 0.3% | PASS |
| `branch_list_many_branches` | 22.57ms | 35.00ms | 64.5% | 1.1% | PASS |
| `branch_create_delete` | 25.02ms | 40.00ms | 62.6% | 1.1% | PASS |
| `checkout_branch_clean` | 60.89ms | 150.00ms | 40.6% | 0.9% | PASS |
| `merge_data_no_conflicts` | 28.61ms | 50.00ms | 57.2% | 1.2% | PASS |
| `merge_schema_no_conflicts` | 21.77ms | 35.00ms | 62.2% | 1.9% | PASS |
| `merge_data_conflicts` | 32.13ms | 180.00ms | 17.8% | 1.0% | PASS |
| `merge_data_conflicts_with_resolve` | 32.07ms | 180.00ms | 17.8% | 0.5% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
