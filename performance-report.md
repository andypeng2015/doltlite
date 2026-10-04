# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-10-04 16:30 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260927.320.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/37211048602)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 8.73s | 9.26s | 1.1× | 1.7% | **PASS** |
| Writes | 1.84s | 3.01s | 1.6× | 1.6% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.70s | 9.52s | 1.0× | 1.9% | **PASS** |
| Writes | 3.45s | 3.98s | 1.2× | 3.0% | **FAIL** |
| Autocommit writes | 889.90ms | 2.63s | 3.0× | 5.4% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 1.63s | 1.70s | 1.0× | 2.0% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 1.84s | 1.94s | 1.1× | 2.0% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.71s | 2.80s | 1.0× | 1.7% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.55s | 2.82s | 1.1× | 1.2% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 265.75ms | 431.43ms | 1.6× | 2.0% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 380.17ms | 609.89ms | 1.6× | 1.7% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 601.70ms | 1.01s | 1.7× | 1.7% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 589.44ms | 958.95ms | 1.6× | 1.0% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 1.83s | 1.75s | 1.0× | 2.1% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 1.90s | 1.96s | 1.0× | 2.4% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.97s | 2.87s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.99s | 2.94s | 1.0× | 1.6% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 753.28ms | 733.69ms | 1.0× | 4.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.04s | 1.04s | 1.0× | 6.4% | **FAIL** |
| File-backed | Writes | blobpk | 8 | 55 | 833.88ms | 1.10s | 1.3× | 1.9% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 820.17ms | 1.11s | 1.4× | 1.9% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 1.68s | 1.72s | 1.0× | 1.9% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 1.87s | 1.96s | 1.0× | 2.0% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.79s | 2.87s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 3.04s | 3.00s | 1.0× | 2.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 179.64ms | 522.38ms | 2.9× | 4.6% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 270.28ms | 740.72ms | 2.7× | 4.7% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 208.75ms | 622.88ms | 3.0× | 5.9% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 231.22ms | 739.86ms | 3.2× | 8.2% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 15.77ms | 18.82ms | 1.2× | 2.4% | PASS |
| mem_reads | `oltp_range_select` | 6.23ms | 6.56ms | 1.1× | 4.3% | PASS |
| mem_reads | `oltp_sum_range` | 5.95ms | 6.82ms | 1.1× | 2.2% | PASS |
| mem_reads | `oltp_order_range` | 1.63ms | 1.69ms | 1.0× | 2.9% | PASS |
| mem_reads | `oltp_distinct_range` | 2.16ms | 2.24ms | 1.0× | 1.9% | PASS |
| mem_reads | `oltp_index_scan` | 2.35ms | 3.02ms | 1.3× | 2.0% | PASS |
| mem_reads | `select_random_points` | 7.10ms | 7.71ms | 1.1× | 8.8% | PASS |
| mem_reads | `select_random_ranges` | 1.88ms | 2.52ms | 1.3× | 3.4% | PASS |
| mem_reads | `covering_index_scan` | 2.42ms | 2.44ms | 1.0× | 1.2% | PASS |
| mem_reads | `groupby_scan` | 17.15ms | 18.39ms | 1.1× | 1.4% | PASS |
| mem_reads | `index_join` | 3.52ms | 4.60ms | 1.3× | 1.6% | PASS |
| mem_reads | `index_join_scan` | 1.76ms | 3.43ms | 2.0× | 3.7% | PASS |
| mem_reads | `types_table_scan` | 707.33ms | 738.42ms | 1.0× | 1.9% | PASS |
| mem_reads | `table_scan` | 797.14ms | 818.93ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_read_only` | 54.99ms | 62.38ms | 1.1× | 1.9% | PASS |
| mem_writes | `oltp_bulk_insert` | 108.38ms | 160.41ms | 1.5× | 1.3% | PASS |
| mem_writes | `oltp_insert` | 9.27ms | 16.60ms | 1.8× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 30.29ms | 57.55ms | 1.9× | 2.1% | PASS |
| mem_writes | `oltp_update_non_index` | 22.08ms | 35.97ms | 1.6× | 2.5% | PASS |
| mem_writes | `oltp_delete_insert` | 27.35ms | 44.22ms | 1.6× | 1.9% | PASS |
| mem_writes | `oltp_write_only` | 14.12ms | 29.91ms | 2.1× | 2.0% | PASS |
| mem_writes | `types_delete_insert` | 16.19ms | 23.47ms | 1.4× | 2.6% | PASS |
| mem_writes | `oltp_read_write` | 38.09ms | 63.31ms | 1.7× | 1.8% | PASS |
| file_reads | `oltp_point_select` | 76.35ms | 35.94ms | 0.5× | 1.7% | PASS |
| file_reads | `oltp_range_select` | 13.25ms | 8.70ms | 0.7× | 2.1% | PASS |
| file_reads | `oltp_sum_range` | 12.46ms | 8.68ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 2.32ms | 1.95ms | 0.8× | 2.1% | PASS |
| file_reads | `oltp_distinct_range` | 2.84ms | 2.49ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_index_scan` | 8.82ms | 5.13ms | 0.6× | 3.2% | PASS |
| file_reads | `select_random_points` | 13.85ms | 10.07ms | 0.7× | 2.4% | PASS |
| file_reads | `select_random_ranges` | 7.84ms | 4.15ms | 0.5× | 1.6% | PASS |
| file_reads | `covering_index_scan` | 8.72ms | 4.13ms | 0.5× | 2.1% | PASS |
| file_reads | `groupby_scan` | 18.05ms | 18.63ms | 1.0× | 2.0% | PASS |
| file_reads | `index_join` | 7.17ms | 6.81ms | 1.0× | 3.5% | PASS |
| file_reads | `index_join_scan` | 3.11ms | 3.98ms | 1.3× | 6.0% | PASS |
| file_reads | `types_table_scan` | 727.23ms | 747.19ms | 1.0× | 3.0% | PASS |
| file_reads | `table_scan` | 793.71ms | 808.53ms | 1.0× | 1.6% | PASS |
| file_reads | `oltp_read_only` | 138.56ms | 85.40ms | 0.6× | 1.7% | PASS |
| file_writes | `oltp_bulk_insert` | 149.03ms | 207.57ms | 1.4× | 4.1% | PASS |
| file_writes | `oltp_insert` | 19.67ms | 28.54ms | 1.5× | 1.0% | PASS |
| file_writes | `oltp_update_index` | 122.11ms | 108.65ms | 0.9× | 4.9% | PASS |
| file_writes | `oltp_update_non_index` | 100.46ms | 83.02ms | 0.8× | 5.4% | PASS |
| file_writes | `oltp_delete_insert` | 110.63ms | 98.29ms | 0.9× | 1.2% | PASS |
| file_writes | `oltp_write_only` | 80.57ms | 59.04ms | 0.7× | 3.3% | PASS |
| file_writes | `types_delete_insert` | 60.50ms | 49.62ms | 0.8× | 18.2% | PASS |
| file_writes | `oltp_read_write` | 110.31ms | 98.95ms | 0.9× | 6.9% | PASS |
| ac_reads | `oltp_point_select` | 37.70ms | 37.43ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 8.21ms | 8.25ms | 1.0× | 4.0% | PASS |
| ac_reads | `oltp_sum_range` | 7.61ms | 8.22ms | 1.1× | 3.3% | PASS |
| ac_reads | `oltp_order_range` | 1.77ms | 1.84ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 2.26ms | 2.36ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_index_scan` | 4.64ms | 5.00ms | 1.1× | 1.8% | PASS |
| ac_reads | `select_random_points` | 10.08ms | 10.34ms | 1.0× | 2.9% | PASS |
| ac_reads | `select_random_ranges` | 4.05ms | 4.21ms | 1.0× | 1.8% | PASS |
| ac_reads | `covering_index_scan` | 4.81ms | 4.37ms | 0.9× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 17.46ms | 18.52ms | 1.1× | 1.2% | PASS |
| ac_reads | `index_join` | 4.74ms | 5.93ms | 1.3× | 3.7% | PASS |
| ac_reads | `index_join_scan` | 2.64ms | 3.90ms | 1.5× | 5.9% | PASS |
| ac_reads | `types_table_scan` | 693.57ms | 714.60ms | 1.0× | 1.5% | PASS |
| ac_reads | `table_scan` | 800.19ms | 813.25ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_read_only` | 82.55ms | 83.31ms | 1.0× | 2.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.98ms | 54.05ms | 2.6× | 4.9% | PASS |
| ac_writes | `oltp_insert_ac` | 22.25ms | 63.33ms | 2.8× | 3.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.01ms | 72.06ms | 3.0× | 3.8% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.12ms | 61.77ms | 2.9× | 4.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 21.98ms | 66.13ms | 3.0× | 5.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.77ms | 68.68ms | 3.0× | 5.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.58ms | 65.27ms | 3.2× | 5.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.95ms | 71.08ms | 2.7× | 4.2% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.93ms | 23.31ms | 1.0× | 1.6% | PASS |
| mem_reads | `oltp_range_select` | 11.95ms | 9.45ms | 0.8× | 1.4% | PASS |
| mem_reads | `oltp_sum_range` | 11.84ms | 8.86ms | 0.7× | 3.3% | PASS |
| mem_reads | `oltp_order_range` | 2.35ms | 2.11ms | 0.9× | 2.9% | PASS |
| mem_reads | `oltp_distinct_range` | 2.92ms | 2.85ms | 1.0× | 2.0% | PASS |
| mem_reads | `oltp_index_scan` | 2.58ms | 3.64ms | 1.4× | 2.9% | PASS |
| mem_reads | `select_random_points` | 17.28ms | 15.24ms | 0.9× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 2.45ms | 3.49ms | 1.4× | 3.1% | PASS |
| mem_reads | `covering_index_scan` | 2.49ms | 2.75ms | 1.1× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 22.16ms | 23.45ms | 1.1× | 1.2% | PASS |
| mem_reads | `index_join` | 9.46ms | 6.64ms | 0.7× | 2.4% | PASS |
| mem_reads | `index_join_scan` | 2.74ms | 4.96ms | 1.8× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 763.08ms | 829.64ms | 1.1× | 1.7% | PASS |
| mem_reads | `table_scan` | 876.54ms | 919.83ms | 1.0× | 2.4% | PASS |
| mem_reads | `oltp_read_only` | 86.96ms | 83.77ms | 1.0× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 139.42ms | 204.21ms | 1.5× | 0.5% | PASS |
| mem_writes | `oltp_insert` | 11.15ms | 23.00ms | 2.1× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 46.31ms | 96.61ms | 2.1× | 2.0% | PASS |
| mem_writes | `oltp_update_non_index` | 33.66ms | 55.51ms | 1.6× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 37.67ms | 66.55ms | 1.8× | 1.7% | PASS |
| mem_writes | `oltp_write_only` | 20.15ms | 39.34ms | 2.0× | 1.7% | PASS |
| mem_writes | `types_delete_insert` | 27.64ms | 33.91ms | 1.2× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 64.17ms | 90.75ms | 1.4× | 2.5% | PASS |
| file_reads | `oltp_point_select` | 46.15ms | 28.54ms | 0.6× | 2.2% | PASS |
| file_reads | `oltp_range_select` | 14.60ms | 10.23ms | 0.7× | 2.7% | PASS |
| file_reads | `oltp_sum_range` | 14.77ms | 10.07ms | 0.7× | 1.2% | PASS |
| file_reads | `oltp_order_range` | 2.72ms | 2.32ms | 0.9× | 2.8% | PASS |
| file_reads | `oltp_distinct_range` | 3.30ms | 3.01ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_index_scan` | 5.20ms | 5.00ms | 1.0× | 2.9% | PASS |
| file_reads | `select_random_points` | 20.93ms | 17.15ms | 0.8× | 2.0% | PASS |
| file_reads | `select_random_ranges` | 4.69ms | 4.14ms | 0.9× | 2.4% | PASS |
| file_reads | `covering_index_scan` | 4.83ms | 3.71ms | 0.8× | 5.3% | PASS |
| file_reads | `groupby_scan` | 22.35ms | 23.56ms | 1.1× | 1.1% | PASS |
| file_reads | `index_join` | 10.91ms | 7.88ms | 0.7× | 4.4% | PASS |
| file_reads | `index_join_scan` | 3.06ms | 5.18ms | 1.7× | 3.1% | PASS |
| file_reads | `types_table_scan` | 753.70ms | 825.56ms | 1.1× | 2.1% | PASS |
| file_reads | `table_scan` | 871.36ms | 921.22ms | 1.1× | 3.0% | PASS |
| file_reads | `oltp_read_only` | 123.08ms | 95.10ms | 0.8× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 211.99ms | 278.52ms | 1.3× | 3.0% | PASS |
| file_writes | `oltp_insert` | 19.77ms | 45.85ms | 2.3× | 8.7% | FAIL |
| file_writes | `oltp_update_index` | 153.70ms | 180.31ms | 1.2× | 6.1% | PASS |
| file_writes | `oltp_update_non_index` | 127.02ms | 109.61ms | 0.9× | 4.3% | PASS |
| file_writes | `oltp_delete_insert` | 156.51ms | 129.98ms | 0.8× | 4.2% | PASS |
| file_writes | `oltp_write_only` | 103.59ms | 85.64ms | 0.8× | 7.0% | PASS |
| file_writes | `types_delete_insert` | 121.21ms | 68.42ms | 0.6× | 8.9% | PASS |
| file_writes | `oltp_read_write` | 150.25ms | 138.90ms | 0.9× | 6.6% | PASS |
| ac_reads | `oltp_point_select` | 30.77ms | 27.75ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_range_select` | 12.78ms | 10.09ms | 0.8× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 13.40ms | 10.10ms | 0.8× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 2.49ms | 2.25ms | 0.9× | 3.3% | PASS |
| ac_reads | `oltp_distinct_range` | 3.19ms | 3.04ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_index_scan` | 3.87ms | 5.19ms | 1.3× | 2.5% | PASS |
| ac_reads | `select_random_points` | 19.52ms | 17.20ms | 0.9× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 3.31ms | 4.24ms | 1.3× | 1.6% | PASS |
| ac_reads | `covering_index_scan` | 3.38ms | 3.73ms | 1.1× | 3.9% | PASS |
| ac_reads | `groupby_scan` | 22.12ms | 23.47ms | 1.1× | 0.8% | PASS |
| ac_reads | `index_join` | 10.49ms | 7.76ms | 0.7× | 4.5% | PASS |
| ac_reads | `index_join_scan` | 3.01ms | 5.28ms | 1.8× | 4.4% | PASS |
| ac_reads | `types_table_scan` | 758.53ms | 824.63ms | 1.1× | 1.8% | PASS |
| ac_reads | `table_scan` | 883.98ms | 920.05ms | 1.0× | 3.3% | PASS |
| ac_reads | `oltp_read_only` | 103.59ms | 97.23ms | 0.9× | 1.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 32.82ms | 83.13ms | 2.5× | 3.9% | PASS |
| ac_writes | `oltp_insert_ac` | 34.41ms | 91.77ms | 2.7× | 5.3% | PASS |
| ac_writes | `oltp_update_index_ac` | 35.08ms | 101.55ms | 2.9× | 4.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 31.79ms | 89.83ms | 2.8× | 5.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 34.03ms | 97.06ms | 2.9× | 6.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 33.97ms | 94.44ms | 2.8× | 6.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 31.58ms | 87.51ms | 2.8× | 3.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 36.60ms | 95.43ms | 2.6× | 3.2% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 35.52ms | 38.55ms | 1.1× | 2.0% | PASS |
| mem_reads | `oltp_range_select` | 16.44ms | 14.02ms | 0.9× | 2.5% | PASS |
| mem_reads | `oltp_sum_range` | 15.40ms | 13.64ms | 0.9× | 1.9% | PASS |
| mem_reads | `oltp_order_range` | 3.24ms | 3.12ms | 1.0× | 1.7% | PASS |
| mem_reads | `oltp_distinct_range` | 4.32ms | 4.25ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.02ms | 6.27ms | 1.6× | 1.7% | PASS |
| mem_reads | `select_random_points` | 21.57ms | 20.98ms | 1.0× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 3.50ms | 5.32ms | 1.5× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 4.23ms | 4.79ms | 1.1× | 1.3% | PASS |
| mem_reads | `groupby_scan` | 34.64ms | 34.67ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 10.14ms | 9.43ms | 0.9× | 2.4% | PASS |
| mem_reads | `index_join_scan` | 3.76ms | 5.40ms | 1.4× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 1.13s | 1.20s | 1.1× | 1.5% | PASS |
| mem_reads | `table_scan` | 1.28s | 1.30s | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_read_only` | 142.69ms | 138.88ms | 1.0× | 2.2% | PASS |
| mem_writes | `oltp_bulk_insert` | 247.36ms | 371.71ms | 1.5× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 18.80ms | 39.12ms | 2.1× | 1.2% | PASS |
| mem_writes | `oltp_update_index` | 65.78ms | 140.96ms | 2.1× | 2.0% | PASS |
| mem_writes | `oltp_update_non_index` | 48.93ms | 85.95ms | 1.8× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 53.21ms | 104.98ms | 2.0× | 1.5% | PASS |
| mem_writes | `oltp_write_only` | 28.11ms | 62.72ms | 2.2× | 1.8% | PASS |
| mem_writes | `types_delete_insert` | 38.14ms | 53.85ms | 1.4× | 1.6% | PASS |
| mem_writes | `oltp_read_write` | 101.36ms | 148.13ms | 1.5× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 112.57ms | 59.15ms | 0.5× | 1.6% | PASS |
| file_reads | `oltp_range_select` | 24.88ms | 16.31ms | 0.7× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 23.76ms | 16.04ms | 0.7× | 2.3% | PASS |
| file_reads | `oltp_order_range` | 4.35ms | 3.43ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_distinct_range` | 5.44ms | 4.58ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 11.98ms | 8.56ms | 0.7× | 1.2% | PASS |
| file_reads | `select_random_points` | 31.69ms | 24.12ms | 0.8× | 2.5% | PASS |
| file_reads | `select_random_ranges` | 11.24ms | 7.49ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 12.21ms | 6.95ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 36.13ms | 35.20ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 15.18ms | 10.93ms | 0.7× | 1.7% | PASS |
| file_reads | `index_join_scan` | 4.90ms | 5.79ms | 1.2× | 2.3% | PASS |
| file_reads | `types_table_scan` | 1.14s | 1.20s | 1.1× | 1.2% | PASS |
| file_reads | `table_scan` | 1.28s | 1.30s | 1.0× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 259.47ms | 170.68ms | 0.7× | 1.3% | PASS |
| file_writes | `oltp_bulk_insert` | 268.69ms | 385.36ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 26.30ms | 46.13ms | 1.8× | 2.1% | PASS |
| file_writes | `oltp_update_index` | 100.17ms | 157.49ms | 1.6× | 2.5% | PASS |
| file_writes | `oltp_update_non_index` | 87.94ms | 98.62ms | 1.1× | 8.1% | PASS |
| file_writes | `oltp_delete_insert` | 89.94ms | 119.33ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_write_only` | 59.15ms | 72.50ms | 1.2× | 1.8% | PASS |
| file_writes | `types_delete_insert` | 65.06ms | 63.79ms | 1.0× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 136.63ms | 158.72ms | 1.2× | 2.8% | PASS |
| ac_reads | `oltp_point_select` | 61.66ms | 59.44ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_range_select` | 19.60ms | 16.32ms | 0.8× | 1.5% | PASS |
| ac_reads | `oltp_sum_range` | 18.45ms | 16.05ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.82ms | 3.43ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_distinct_range` | 4.87ms | 4.58ms | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_index_scan` | 6.89ms | 8.54ms | 1.2× | 1.2% | PASS |
| ac_reads | `select_random_points` | 25.29ms | 23.89ms | 0.9× | 1.9% | PASS |
| ac_reads | `select_random_ranges` | 6.21ms | 7.48ms | 1.2× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 7.18ms | 6.96ms | 1.0× | 1.2% | PASS |
| ac_reads | `groupby_scan` | 35.22ms | 35.05ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 12.40ms | 10.97ms | 0.9× | 2.2% | PASS |
| ac_reads | `index_join_scan` | 4.34ms | 5.75ms | 1.3× | 2.7% | PASS |
| ac_reads | `types_table_scan` | 1.12s | 1.19s | 1.1× | 1.5% | PASS |
| ac_reads | `table_scan` | 1.28s | 1.30s | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_read_only` | 183.55ms | 169.77ms | 0.9× | 1.4% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.36ms | 63.57ms | 2.7× | 6.4% | PASS |
| ac_writes | `oltp_insert_ac` | 25.02ms | 77.67ms | 3.1× | 5.2% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.66ms | 87.00ms | 3.3× | 5.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.78ms | 72.08ms | 3.2× | 6.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 25.72ms | 80.34ms | 3.1× | 5.3% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.04ms | 80.97ms | 3.0× | 7.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.55ms | 73.50ms | 2.9× | 7.0% | PASS |
| ac_writes | `oltp_read_write_ac` | 32.62ms | 87.75ms | 2.7× | 4.7% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.67ms | 41.24ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 19.67ms | 21.19ms | 1.1× | 2.1% | PASS |
| mem_reads | `oltp_sum_range` | 18.05ms | 20.83ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 3.55ms | 3.87ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_distinct_range` | 4.69ms | 5.06ms | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_index_scan` | 4.42ms | 6.03ms | 1.4× | 1.5% | PASS |
| mem_reads | `select_random_points` | 26.27ms | 32.02ms | 1.2× | 1.1% | PASS |
| mem_reads | `select_random_ranges` | 7.24ms | 8.91ms | 1.2× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 4.23ms | 4.24ms | 1.0× | 1.1% | PASS |
| mem_reads | `groupby_scan` | 35.81ms | 39.45ms | 1.1× | 0.6% | PASS |
| mem_reads | `index_join` | 7.91ms | 10.23ms | 1.3× | 1.2% | PASS |
| mem_reads | `index_join_scan` | 3.62ms | 5.40ms | 1.5× | 2.2% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.18s | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 1.18s | 1.27s | 1.1× | 0.3% | PASS |
| mem_reads | `oltp_read_only` | 146.81ms | 170.45ms | 1.2× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 250.96ms | 351.63ms | 1.4× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 19.09ms | 34.99ms | 1.8× | 0.7% | PASS |
| mem_writes | `oltp_update_index` | 64.98ms | 124.70ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_non_index` | 49.49ms | 83.41ms | 1.7× | 1.3% | PASS |
| mem_writes | `oltp_delete_insert` | 48.12ms | 93.93ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 26.16ms | 57.39ms | 2.2× | 1.6% | PASS |
| mem_writes | `types_delete_insert` | 31.57ms | 52.59ms | 1.7× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 99.07ms | 160.30ms | 1.6× | 0.9% | PASS |
| file_reads | `oltp_point_select` | 108.90ms | 60.68ms | 0.6× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 26.89ms | 23.46ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_sum_range` | 25.66ms | 22.85ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 4.43ms | 4.23ms | 1.0× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 5.85ms | 5.58ms | 1.0× | 2.4% | PASS |
| file_reads | `oltp_index_scan` | 12.58ms | 8.68ms | 0.7× | 1.6% | PASS |
| file_reads | `select_random_points` | 39.76ms | 37.81ms | 1.0× | 2.3% | PASS |
| file_reads | `select_random_ranges` | 16.16ms | 11.47ms | 0.7× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 12.16ms | 6.78ms | 0.6× | 1.4% | PASS |
| file_reads | `groupby_scan` | 38.51ms | 40.71ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 12.85ms | 12.81ms | 1.0× | 2.5% | PASS |
| file_reads | `index_join_scan` | 5.07ms | 6.16ms | 1.2× | 3.8% | PASS |
| file_reads | `types_table_scan` | 1.09s | 1.20s | 1.1× | 1.4% | PASS |
| file_reads | `table_scan` | 1.32s | 1.29s | 1.0× | 2.1% | PASS |
| file_reads | `oltp_read_only` | 275.93ms | 207.70ms | 0.8× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 270.02ms | 380.85ms | 1.4× | 1.2% | PASS |
| file_writes | `oltp_insert` | 29.62ms | 43.73ms | 1.5× | 4.5% | PASS |
| file_writes | `oltp_update_index` | 107.95ms | 152.50ms | 1.4× | 2.0% | PASS |
| file_writes | `oltp_update_non_index` | 83.44ms | 103.00ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_delete_insert` | 84.49ms | 116.13ms | 1.4× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 56.56ms | 73.19ms | 1.3× | 3.0% | PASS |
| file_writes | `types_delete_insert` | 53.00ms | 62.81ms | 1.2× | 1.8% | PASS |
| file_writes | `oltp_read_write` | 135.08ms | 177.27ms | 1.3× | 1.7% | PASS |
| ac_reads | `oltp_point_select` | 61.27ms | 62.74ms | 1.0× | 1.4% | PASS |
| ac_reads | `oltp_range_select` | 23.87ms | 23.83ms | 1.0× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 21.41ms | 23.29ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 4.12ms | 4.28ms | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_distinct_range` | 5.66ms | 5.95ms | 1.1× | 6.1% | PASS |
| ac_reads | `oltp_index_scan` | 8.16ms | 9.09ms | 1.1× | 2.9% | PASS |
| ac_reads | `select_random_points` | 35.15ms | 39.19ms | 1.1× | 3.3% | PASS |
| ac_reads | `select_random_ranges` | 11.29ms | 11.88ms | 1.1× | 2.3% | PASS |
| ac_reads | `covering_index_scan` | 8.38ms | 7.49ms | 0.9× | 4.6% | PASS |
| ac_reads | `groupby_scan` | 38.55ms | 41.28ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 10.98ms | 13.59ms | 1.2× | 4.9% | PASS |
| ac_reads | `index_join_scan` | 4.73ms | 6.44ms | 1.4× | 6.3% | PASS |
| ac_reads | `types_table_scan` | 1.17s | 1.21s | 1.0× | 1.1% | PASS |
| ac_reads | `table_scan` | 1.43s | 1.32s | 0.9× | 1.4% | PASS |
| ac_reads | `oltp_read_only` | 204.31ms | 212.14ms | 1.0× | 1.0% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 26.94ms | 76.47ms | 2.8× | 9.7% | PASS |
| ac_writes | `oltp_insert_ac` | 28.56ms | 92.72ms | 3.2× | 7.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 31.06ms | 103.71ms | 3.3× | 8.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 26.08ms | 84.67ms | 3.2× | 10.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.04ms | 96.84ms | 3.5× | 9.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 27.95ms | 93.83ms | 3.4× | 6.8% | PASS |
| ac_writes | `types_delete_insert_ac` | 25.69ms | 86.73ms | 3.4× | 8.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 36.90ms | 104.88ms | 2.8× | 8.2% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 21s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 106.32ms | 130.00ms | 81.8% | 0.3% | PASS |
| `status_dirty_many_tables` | 110.26ms | 130.00ms | 84.8% | 0.2% | PASS |
| `diff_regular_working_one_table` | 101.54ms | 120.00ms | 84.6% | 0.2% | PASS |
| `diff_regular_working_many_tables` | 115.06ms | 140.00ms | 82.2% | 0.2% | PASS |
| `diff_stat_working_many_tables` | 115.11ms | 140.00ms | 82.2% | 0.2% | PASS |
| `diff_schema_working_many_tables` | 116.31ms | 140.00ms | 83.1% | 0.2% | PASS |
| `branch_list_many_branches` | 24.43ms | 35.00ms | 69.8% | 1.1% | PASS |
| `branch_create_delete` | 26.11ms | 40.00ms | 65.3% | 1.0% | PASS |
| `checkout_branch_clean` | 64.99ms | 150.00ms | 43.3% | 0.5% | PASS |
| `merge_data_no_conflicts` | 30.58ms | 50.00ms | 61.2% | 1.0% | PASS |
| `merge_schema_no_conflicts` | 23.11ms | 35.00ms | 66.0% | 1.9% | PASS |
| `merge_data_conflicts` | 34.96ms | 180.00ms | 19.4% | 0.7% | PASS |
| `merge_data_conflicts_with_resolve` | 34.88ms | 180.00ms | 19.4% | 0.6% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
