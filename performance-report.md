# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-05 19:04 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261101.186.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/42075643572)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.45s | 11.16s | 1.1× | 1.4% | **PASS** |
| Writes | 2.07s | 3.60s | 1.7× | 1.4% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.73s | 11.21s | 1.0× | 1.5% | **PASS** |
| Writes | 2.80s | 3.90s | 1.4× | 1.6% | **PASS** |
| Autocommit writes | 663.67ms | 2.12s | 3.2× | 6.2% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.42s | 2.69s | 1.1× | 1.7% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.56s | 2.75s | 1.1× | 1.1% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.45s | 2.72s | 1.1× | 1.1% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 3.01s | 3.01s | 1.0× | 2.6% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 428.97ms | 751.70ms | 1.8× | 1.4% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 511.65ms | 890.52ms | 1.7× | 1.0% | **FAIL** |
| In-memory | Writes | blobpk | 8 | 55 | 505.84ms | 881.35ms | 1.7× | 1.1% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 618.74ms | 1.07s | 1.7× | 2.5% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 2.64s | 2.74s | 1.0× | 1.5% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.66s | 2.78s | 1.0× | 1.4% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.46s | 2.68s | 1.1× | 1.3% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.97s | 3.01s | 1.0× | 2.4% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 588.75ms | 822.73ms | 1.4× | 1.6% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 798.80ms | 997.45ms | 1.2× | 3.6% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 633.65ms | 958.27ms | 1.5× | 1.4% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 776.38ms | 1.12s | 1.4× | 2.1% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.51s | 2.75s | 1.1× | 1.5% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.60s | 2.79s | 1.1× | 1.4% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.45s | 2.71s | 1.1× | 1.2% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.77s | 2.98s | 1.1× | 2.4% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 176.30ms | 557.92ms | 3.2× | 5.8% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 157.37ms | 518.06ms | 3.3× | 8.2% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 143.42ms | 468.99ms | 3.3× | 6.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 186.58ms | 575.75ms | 3.1× | 5.4% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.92ms | 29.95ms | 1.3× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 11.17ms | 11.69ms | 1.0× | 1.8% | PASS |
| mem_reads | `oltp_sum_range` | 9.47ms | 11.21ms | 1.2× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 2.69ms | 2.93ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.65ms | 4.03ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.85ms | 5.16ms | 1.3× | 1.9% | PASS |
| mem_reads | `select_random_points` | 9.85ms | 10.97ms | 1.1× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 3.05ms | 3.97ms | 1.3× | 2.0% | PASS |
| mem_reads | `covering_index_scan` | 4.16ms | 4.21ms | 1.0× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 30.21ms | 33.30ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 5.79ms | 7.89ms | 1.4× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 3.26ms | 4.55ms | 1.4× | 2.5% | PASS |
| mem_reads | `types_table_scan` | 1.03s | 1.17s | 1.1× | 0.8% | PASS |
| mem_reads | `table_scan` | 1.17s | 1.27s | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 105.94ms | 121.37ms | 1.1× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 175.42ms | 275.83ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 14.92ms | 28.39ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 49.30ms | 99.52ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 33.99ms | 63.71ms | 1.9× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 42.86ms | 77.15ms | 1.8× | 1.8% | PASS |
| mem_writes | `oltp_write_only` | 21.08ms | 49.70ms | 2.4× | 1.3% | FAIL |
| mem_writes | `types_delete_insert` | 23.97ms | 41.04ms | 1.7× | 1.4% | PASS |
| mem_writes | `oltp_read_write` | 67.42ms | 116.37ms | 1.7× | 1.5% | PASS |
| file_reads | `oltp_point_select` | 92.86ms | 47.26ms | 0.5× | 0.8% | PASS |
| file_reads | `oltp_range_select` | 18.23ms | 13.52ms | 0.7× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 16.73ms | 13.18ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 3.55ms | 3.16ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_distinct_range` | 4.52ms | 4.31ms | 1.0× | 1.3% | PASS |
| file_reads | `oltp_index_scan` | 11.12ms | 7.21ms | 0.6× | 1.5% | PASS |
| file_reads | `select_random_points` | 17.24ms | 12.75ms | 0.7× | 2.0% | PASS |
| file_reads | `select_random_ranges` | 10.03ms | 5.78ms | 0.6× | 1.2% | PASS |
| file_reads | `covering_index_scan` | 11.43ms | 6.28ms | 0.5× | 1.8% | PASS |
| file_reads | `groupby_scan` | 31.26ms | 33.78ms | 1.1× | 0.5% | PASS |
| file_reads | `index_join` | 9.80ms | 9.50ms | 1.0× | 2.1% | PASS |
| file_reads | `index_join_scan` | 4.18ms | 4.91ms | 1.2× | 1.9% | PASS |
| file_reads | `types_table_scan` | 1.03s | 1.17s | 1.1× | 0.8% | PASS |
| file_reads | `table_scan` | 1.17s | 1.27s | 1.1× | 1.1% | PASS |
| file_reads | `oltp_read_only` | 205.93ms | 146.91ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 189.19ms | 285.52ms | 1.5× | 1.3% | PASS |
| file_writes | `oltp_insert` | 21.56ms | 31.70ms | 1.5× | 1.3% | PASS |
| file_writes | `oltp_update_index` | 76.42ms | 113.71ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 57.54ms | 75.67ms | 1.3× | 1.6% | PASS |
| file_writes | `oltp_delete_insert` | 67.07ms | 89.83ms | 1.3× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 44.37ms | 57.90ms | 1.3× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 39.69ms | 44.30ms | 1.1× | 2.4% | PASS |
| file_writes | `oltp_read_write` | 92.91ms | 124.10ms | 1.3× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 46.25ms | 47.16ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 13.80ms | 13.54ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 12.19ms | 13.22ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 3.08ms | 3.16ms | 1.0× | 1.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.02ms | 4.30ms | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.46ms | 7.29ms | 1.1× | 1.9% | PASS |
| ac_reads | `select_random_points` | 12.56ms | 12.77ms | 1.0× | 2.3% | PASS |
| ac_reads | `select_random_ranges` | 5.42ms | 5.79ms | 1.1× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 6.82ms | 6.27ms | 0.9× | 1.8% | PASS |
| ac_reads | `groupby_scan` | 30.82ms | 33.74ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 7.45ms | 9.53ms | 1.3× | 2.2% | PASS |
| ac_reads | `index_join_scan` | 3.67ms | 4.92ms | 1.3× | 1.6% | PASS |
| ac_reads | `types_table_scan` | 1.03s | 1.17s | 1.1× | 1.2% | PASS |
| ac_reads | `table_scan` | 1.18s | 1.27s | 1.1× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 138.34ms | 147.16ms | 1.1× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.64ms | 55.47ms | 2.8× | 5.9% | PASS |
| ac_writes | `oltp_insert_ac` | 21.52ms | 70.30ms | 3.3× | 6.4% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.27ms | 80.03ms | 3.3× | 4.4% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.39ms | 63.56ms | 3.1× | 6.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 21.91ms | 72.40ms | 3.3× | 4.4% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.01ms | 70.86ms | 3.2× | 4.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 19.48ms | 67.30ms | 3.5× | 7.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.08ms | 78.00ms | 2.9× | 5.7% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.64ms | 33.37ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_range_select` | 15.09ms | 12.40ms | 0.8× | 1.1% | PASS |
| mem_reads | `oltp_sum_range` | 14.66ms | 12.56ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 2.96ms | 2.88ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_distinct_range` | 3.80ms | 3.79ms | 1.0× | 0.6% | PASS |
| mem_reads | `oltp_index_scan` | 3.56ms | 5.54ms | 1.6× | 1.1% | PASS |
| mem_reads | `select_random_points` | 21.75ms | 20.61ms | 0.9× | 1.9% | PASS |
| mem_reads | `select_random_ranges` | 3.02ms | 4.57ms | 1.5× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 3.60ms | 4.13ms | 1.1× | 1.1% | PASS |
| mem_reads | `groupby_scan` | 32.35ms | 32.02ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 10.30ms | 9.10ms | 0.9× | 2.1% | PASS |
| mem_reads | `index_join_scan` | 3.22ms | 5.25ms | 1.6× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.18s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.24s | 1.30s | 1.0× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 133.84ms | 128.24ms | 1.0× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 193.02ms | 305.68ms | 1.6× | 1.3% | PASS |
| mem_writes | `oltp_insert` | 15.35ms | 34.71ms | 2.3× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 59.57ms | 136.31ms | 2.3× | 1.6% | PASS |
| mem_writes | `oltp_update_non_index` | 44.25ms | 78.34ms | 1.8× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 48.33ms | 97.26ms | 2.0× | 1.2% | PASS |
| mem_writes | `oltp_write_only` | 24.59ms | 56.66ms | 2.3× | 0.8% | FAIL |
| mem_writes | `types_delete_insert` | 34.88ms | 49.29ms | 1.4× | 0.8% | PASS |
| mem_writes | `oltp_read_write` | 91.66ms | 132.27ms | 1.4× | 0.9% | PASS |
| file_reads | `oltp_point_select` | 63.01ms | 41.39ms | 0.7× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 18.52ms | 13.43ms | 0.7× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 18.05ms | 13.60ms | 0.8× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 3.40ms | 3.04ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.24ms | 3.95ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 6.83ms | 6.49ms | 1.0× | 1.1% | PASS |
| file_reads | `select_random_points` | 25.95ms | 21.89ms | 0.8× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 6.06ms | 5.33ms | 0.9× | 1.7% | PASS |
| file_reads | `covering_index_scan` | 6.71ms | 4.91ms | 0.7× | 1.4% | PASS |
| file_reads | `groupby_scan` | 33.04ms | 32.29ms | 1.0× | 0.8% | PASS |
| file_reads | `index_join` | 12.51ms | 9.77ms | 0.8× | 1.9% | PASS |
| file_reads | `index_join_scan` | 3.71ms | 5.57ms | 1.5× | 3.2% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.18s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.24s | 1.29s | 1.0× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 179.40ms | 142.34ms | 0.8× | 0.6% | PASS |
| file_writes | `oltp_bulk_insert` | 215.76ms | 323.49ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_insert` | 21.21ms | 42.14ms | 2.0× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 117.27ms | 157.16ms | 1.3× | 6.6% | PASS |
| file_writes | `oltp_update_non_index` | 102.39ms | 92.53ms | 0.9× | 14.0% | PASS |
| file_writes | `oltp_delete_insert` | 74.11ms | 110.62ms | 1.5× | 1.1% | PASS |
| file_writes | `oltp_write_only` | 62.27ms | 68.24ms | 1.1× | 5.4% | PASS |
| file_writes | `types_delete_insert` | 55.72ms | 58.04ms | 1.0× | 1.2% | PASS |
| file_writes | `oltp_read_write` | 150.09ms | 145.24ms | 1.0× | 5.6% | PASS |
| ac_reads | `oltp_point_select` | 42.98ms | 41.50ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_range_select` | 16.36ms | 13.52ms | 0.8× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 15.91ms | 13.66ms | 0.9× | 1.6% | PASS |
| ac_reads | `oltp_order_range` | 3.19ms | 3.03ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_distinct_range` | 4.03ms | 3.98ms | 1.0× | 1.7% | PASS |
| ac_reads | `oltp_index_scan` | 4.86ms | 6.49ms | 1.3× | 1.2% | PASS |
| ac_reads | `select_random_points` | 24.40ms | 22.42ms | 0.9× | 1.8% | PASS |
| ac_reads | `select_random_ranges` | 4.17ms | 5.38ms | 1.3× | 1.3% | PASS |
| ac_reads | `covering_index_scan` | 4.81ms | 4.96ms | 1.0× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 32.73ms | 32.31ms | 1.0× | 0.8% | PASS |
| ac_reads | `index_join` | 11.45ms | 9.82ms | 0.9× | 2.0% | PASS |
| ac_reads | `index_join_scan` | 3.49ms | 5.58ms | 1.6× | 2.7% | PASS |
| ac_reads | `types_table_scan` | 1.04s | 1.19s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.25s | 1.30s | 1.0× | 0.4% | PASS |
| ac_reads | `oltp_read_only` | 148.27ms | 140.50ms | 0.9× | 0.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.10ms | 48.79ms | 2.9× | 9.8% | PASS |
| ac_writes | `oltp_insert_ac` | 19.79ms | 64.40ms | 3.3× | 8.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.13ms | 76.93ms | 3.6× | 9.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.64ms | 58.64ms | 3.3× | 7.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 20.07ms | 66.34ms | 3.3× | 7.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.34ms | 67.51ms | 3.5× | 10.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 17.26ms | 61.75ms | 3.6× | 8.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.05ms | 73.70ms | 2.9× | 8.1% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.11ms | 33.68ms | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 14.01ms | 12.19ms | 0.9× | 1.5% | PASS |
| mem_reads | `oltp_sum_range` | 13.34ms | 12.08ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_order_range` | 2.86ms | 2.84ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.67ms | 3.76ms | 1.0× | 1.1% | PASS |
| mem_reads | `oltp_index_scan` | 3.50ms | 5.46ms | 1.6× | 1.2% | PASS |
| mem_reads | `select_random_points` | 20.62ms | 20.10ms | 1.0× | 1.9% | PASS |
| mem_reads | `select_random_ranges` | 2.97ms | 4.56ms | 1.5× | 1.1% | PASS |
| mem_reads | `covering_index_scan` | 3.60ms | 4.12ms | 1.1× | 1.0% | PASS |
| mem_reads | `groupby_scan` | 31.72ms | 31.87ms | 1.0× | 0.5% | PASS |
| mem_reads | `index_join` | 10.03ms | 9.29ms | 0.9× | 2.2% | PASS |
| mem_reads | `index_join_scan` | 3.11ms | 5.20ms | 1.7× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 1.01s | 1.17s | 1.2× | 0.7% | PASS |
| mem_reads | `table_scan` | 1.17s | 1.28s | 1.1× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 123.35ms | 124.08ms | 1.0× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 195.43ms | 305.88ms | 1.6× | 1.4% | PASS |
| mem_writes | `oltp_insert` | 15.79ms | 34.63ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 58.58ms | 133.39ms | 2.3× | 1.0% | PASS |
| mem_writes | `oltp_update_non_index` | 42.64ms | 76.41ms | 1.8× | 1.5% | PASS |
| mem_writes | `oltp_delete_insert` | 45.69ms | 95.05ms | 2.1× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 24.00ms | 55.85ms | 2.3× | 1.0% | FAIL |
| mem_writes | `types_delete_insert` | 33.31ms | 48.26ms | 1.4× | 1.2% | PASS |
| mem_writes | `oltp_read_write` | 90.41ms | 131.89ms | 1.5× | 1.7% | PASS |
| file_reads | `oltp_point_select` | 63.55ms | 42.16ms | 0.7× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 17.55ms | 13.30ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_sum_range` | 17.12ms | 13.34ms | 0.8× | 1.3% | PASS |
| file_reads | `oltp_order_range` | 3.27ms | 2.98ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.08ms | 3.90ms | 1.0× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 6.64ms | 6.33ms | 1.0× | 1.4% | PASS |
| file_reads | `select_random_points` | 24.02ms | 21.18ms | 0.9× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 5.91ms | 5.30ms | 0.9× | 0.9% | PASS |
| file_reads | `covering_index_scan` | 6.60ms | 4.84ms | 0.7× | 1.2% | PASS |
| file_reads | `groupby_scan` | 32.02ms | 31.90ms | 1.0× | 0.5% | PASS |
| file_reads | `index_join` | 11.85ms | 9.56ms | 0.8× | 2.0% | PASS |
| file_reads | `index_join_scan` | 3.52ms | 5.18ms | 1.5× | 1.6% | PASS |
| file_reads | `types_table_scan` | 987.60ms | 1.14s | 1.2× | 1.0% | PASS |
| file_reads | `table_scan` | 1.12s | 1.24s | 1.1× | 2.0% | PASS |
| file_reads | `oltp_read_only` | 165.41ms | 134.52ms | 0.8× | 1.1% | PASS |
| file_writes | `oltp_bulk_insert` | 212.74ms | 317.71ms | 1.5× | 1.5% | PASS |
| file_writes | `oltp_insert` | 20.27ms | 40.28ms | 2.0× | 1.3% | PASS |
| file_writes | `oltp_update_index` | 75.83ms | 144.61ms | 1.9× | 1.2% | PASS |
| file_writes | `oltp_update_non_index` | 64.55ms | 87.78ms | 1.4× | 9.3% | PASS |
| file_writes | `oltp_delete_insert` | 66.21ms | 106.77ms | 1.6× | 0.8% | PASS |
| file_writes | `oltp_write_only` | 41.11ms | 65.62ms | 1.6× | 1.3% | PASS |
| file_writes | `types_delete_insert` | 47.75ms | 55.74ms | 1.2× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 105.20ms | 139.75ms | 1.3× | 1.4% | PASS |
| ac_reads | `oltp_point_select` | 41.51ms | 41.63ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 15.17ms | 13.13ms | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_sum_range` | 14.73ms | 13.20ms | 0.9× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 3.03ms | 2.97ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 3.85ms | 3.88ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 4.74ms | 6.35ms | 1.3× | 1.4% | PASS |
| ac_reads | `select_random_points` | 21.48ms | 21.05ms | 1.0× | 1.5% | PASS |
| ac_reads | `select_random_ranges` | 4.01ms | 5.29ms | 1.3× | 1.4% | PASS |
| ac_reads | `covering_index_scan` | 4.77ms | 4.87ms | 1.0× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 31.70ms | 31.99ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 10.75ms | 9.41ms | 0.9× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 3.37ms | 5.15ms | 1.5× | 1.5% | PASS |
| ac_reads | `types_table_scan` | 994.64ms | 1.15s | 1.2× | 1.4% | PASS |
| ac_reads | `table_scan` | 1.16s | 1.27s | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_read_only` | 138.88ms | 136.60ms | 1.0× | 1.5% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 16.79ms | 45.68ms | 2.7× | 7.1% | PASS |
| ac_writes | `oltp_insert_ac` | 17.72ms | 59.27ms | 3.3× | 4.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 19.40ms | 66.13ms | 3.4× | 5.2% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.15ms | 53.58ms | 3.3× | 5.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 17.88ms | 62.02ms | 3.5× | 6.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 17.77ms | 61.58ms | 3.5× | 8.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 16.10ms | 54.28ms | 3.4× | 6.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 21.60ms | 66.44ms | 3.1× | 5.0% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 34.49ms | 42.20ms | 1.2× | 2.2% | PASS |
| mem_reads | `oltp_range_select` | 20.55ms | 22.28ms | 1.1× | 2.6% | PASS |
| mem_reads | `oltp_sum_range` | 18.09ms | 21.23ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_order_range` | 3.83ms | 4.31ms | 1.1× | 3.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.85ms | 5.43ms | 1.1× | 2.3% | PASS |
| mem_reads | `oltp_index_scan` | 4.67ms | 6.64ms | 1.4× | 3.7% | PASS |
| mem_reads | `select_random_points` | 27.75ms | 32.66ms | 1.2× | 2.4% | PASS |
| mem_reads | `select_random_ranges` | 8.08ms | 9.37ms | 1.2× | 2.4% | PASS |
| mem_reads | `covering_index_scan` | 4.24ms | 4.74ms | 1.1× | 4.7% | PASS |
| mem_reads | `groupby_scan` | 38.39ms | 41.88ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 8.20ms | 11.44ms | 1.4× | 5.1% | PASS |
| mem_reads | `index_join_scan` | 4.22ms | 6.16ms | 1.5× | 4.0% | PASS |
| mem_reads | `types_table_scan` | 1.13s | 1.23s | 1.1× | 3.3% | PASS |
| mem_reads | `table_scan` | 1.53s | 1.38s | 0.9× | 2.6% | PASS |
| mem_reads | `oltp_read_only` | 176.76ms | 191.61ms | 1.1× | 3.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 246.30ms | 382.69ms | 1.6× | 3.1% | PASS |
| mem_writes | `oltp_insert` | 19.46ms | 38.03ms | 2.0× | 2.6% | PASS |
| mem_writes | `oltp_update_index` | 75.45ms | 153.69ms | 2.0× | 4.6% | PASS |
| mem_writes | `oltp_update_non_index` | 55.56ms | 94.88ms | 1.7× | 2.9% | PASS |
| mem_writes | `oltp_delete_insert` | 50.24ms | 103.09ms | 2.1× | 1.7% | PASS |
| mem_writes | `oltp_write_only` | 28.12ms | 65.45ms | 2.3× | 2.2% | FAIL |
| mem_writes | `types_delete_insert` | 34.27ms | 58.95ms | 1.7× | 2.2% | PASS |
| mem_writes | `oltp_read_write` | 109.33ms | 178.12ms | 1.6× | 2.4% | PASS |
| file_reads | `oltp_point_select` | 105.15ms | 61.03ms | 0.6× | 2.2% | PASS |
| file_reads | `oltp_range_select` | 29.57ms | 24.79ms | 0.8× | 4.3% | PASS |
| file_reads | `oltp_sum_range` | 27.24ms | 24.17ms | 0.9× | 2.4% | PASS |
| file_reads | `oltp_order_range` | 5.16ms | 4.78ms | 0.9× | 2.5% | PASS |
| file_reads | `oltp_distinct_range` | 6.29ms | 5.95ms | 0.9× | 1.6% | PASS |
| file_reads | `oltp_index_scan` | 12.44ms | 8.84ms | 0.7× | 2.4% | PASS |
| file_reads | `select_random_points` | 38.36ms | 37.40ms | 1.0× | 2.6% | PASS |
| file_reads | `select_random_ranges` | 15.97ms | 11.69ms | 0.7× | 2.3% | PASS |
| file_reads | `covering_index_scan` | 12.00ms | 6.85ms | 0.6× | 2.5% | PASS |
| file_reads | `groupby_scan` | 39.87ms | 42.37ms | 1.1× | 1.0% | PASS |
| file_reads | `index_join` | 13.21ms | 13.55ms | 1.0× | 4.2% | PASS |
| file_reads | `index_join_scan` | 5.55ms | 6.81ms | 1.2× | 6.7% | PASS |
| file_reads | `types_table_scan` | 1.17s | 1.24s | 1.1× | 4.6% | PASS |
| file_reads | `table_scan` | 1.22s | 1.31s | 1.1× | 2.4% | PASS |
| file_reads | `oltp_read_only` | 265.31ms | 207.64ms | 0.8× | 1.4% | PASS |
| file_writes | `oltp_bulk_insert` | 259.43ms | 375.46ms | 1.4× | 1.3% | PASS |
| file_writes | `oltp_insert` | 26.05ms | 43.98ms | 1.7× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 99.87ms | 154.27ms | 1.5× | 2.2% | PASS |
| file_writes | `oltp_update_non_index` | 79.04ms | 105.78ms | 1.3× | 2.5% | PASS |
| file_writes | `oltp_delete_insert` | 77.83ms | 117.42ms | 1.5× | 1.8% | PASS |
| file_writes | `oltp_write_only` | 52.36ms | 76.36ms | 1.5× | 3.0% | PASS |
| file_writes | `types_delete_insert` | 50.66ms | 64.78ms | 1.3× | 2.5% | PASS |
| file_writes | `oltp_read_write` | 131.15ms | 184.40ms | 1.4× | 2.0% | PASS |
| ac_reads | `oltp_point_select` | 57.55ms | 59.71ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_range_select` | 24.70ms | 24.32ms | 1.0× | 2.5% | PASS |
| ac_reads | `oltp_sum_range` | 21.70ms | 23.62ms | 1.1× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 4.51ms | 4.67ms | 1.0× | 2.8% | PASS |
| ac_reads | `oltp_distinct_range` | 5.53ms | 5.76ms | 1.0× | 2.4% | PASS |
| ac_reads | `oltp_index_scan` | 7.58ms | 8.46ms | 1.1× | 2.4% | PASS |
| ac_reads | `select_random_points` | 31.46ms | 36.32ms | 1.2× | 2.5% | PASS |
| ac_reads | `select_random_ranges` | 10.87ms | 11.45ms | 1.1× | 2.0% | PASS |
| ac_reads | `covering_index_scan` | 7.17ms | 6.62ms | 0.9× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 38.56ms | 41.88ms | 1.1× | 1.1% | PASS |
| ac_reads | `index_join` | 10.24ms | 12.82ms | 1.3× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 4.80ms | 6.33ms | 1.3× | 3.1% | PASS |
| ac_reads | `types_table_scan` | 1.06s | 1.21s | 1.1× | 1.6% | PASS |
| ac_reads | `table_scan` | 1.28s | 1.32s | 1.0× | 3.5% | PASS |
| ac_reads | `oltp_read_only` | 199.17ms | 209.75ms | 1.1× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.67ms | 55.83ms | 2.7× | 5.3% | PASS |
| ac_writes | `oltp_insert_ac` | 22.57ms | 74.04ms | 3.3× | 5.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 24.88ms | 81.41ms | 3.3× | 5.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 20.60ms | 64.44ms | 3.1× | 4.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.76ms | 76.11ms | 3.3× | 5.0% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.90ms | 76.09ms | 3.3× | 9.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 21.16ms | 64.95ms | 3.1× | 8.2% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.04ms | 82.89ms | 2.7× | 5.7% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 33s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 60.05ms | 130.00ms | 46.2% | 1.2% | PASS |
| `status_dirty_many_tables` | 61.38ms | 130.00ms | 47.2% | 1.3% | PASS |
| `diff_regular_working_one_table` | 56.80ms | 120.00ms | 47.3% | 2.4% | PASS |
| `diff_regular_working_many_tables` | 63.15ms | 140.00ms | 45.1% | 1.5% | PASS |
| `diff_stat_working_many_tables` | 63.91ms | 140.00ms | 45.6% | 2.5% | PASS |
| `diff_schema_working_many_tables` | 61.83ms | 140.00ms | 44.2% | 1.2% | PASS |
| `branch_list_many_branches` | 14.81ms | 35.00ms | 42.3% | 4.3% | PASS |
| `branch_create_delete` | 16.34ms | 40.00ms | 40.9% | 3.6% | PASS |
| `checkout_branch_clean` | 84.86ms | 150.00ms | 56.6% | 3.4% | PASS |
| `merge_data_no_conflicts` | 25.78ms | 50.00ms | 51.6% | 2.3% | PASS |
| `merge_schema_no_conflicts` | 13.56ms | 35.00ms | 38.8% | 1.8% | PASS |
| `merge_data_conflicts` | 19.87ms | 180.00ms | 11.0% | 1.6% | PASS |
| `merge_data_conflicts_with_resolve` | 19.98ms | 180.00ms | 11.1% | 1.9% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
