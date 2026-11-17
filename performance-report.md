# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-11-17 20:11 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261111.196.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/44150678254)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 9.05s | 9.60s | 1.1× | 1.8% | **PASS** |
| Writes | 1.86s | 3.13s | 1.7× | 1.7% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.35s | 9.93s | 1.0× | 1.3% | **PASS** |
| Writes | 4.01s | 4.39s | 1.1× | 2.9% | **PASS** |
| Autocommit writes | 721.62ms | 2.16s | 3.0× | 10.5% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.41s | 2.67s | 1.1× | 1.8% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.22s | 2.29s | 1.0× | 2.4% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.32s | 2.35s | 1.0× | 2.0% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.09s | 2.29s | 1.1× | 1.3% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 455.33ms | 784.93ms | 1.7× | 2.0% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 476.60ms | 803.00ms | 1.7× | 1.9% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 476.42ms | 804.62ms | 1.7× | 2.3% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 451.34ms | 734.62ms | 1.6× | 0.9% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.64s | 2.73s | 1.0× | 1.6% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.74s | 2.43s | 0.9× | 1.3% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.66s | 2.42s | 0.9× | 1.6% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.32s | 2.34s | 1.0× | 1.0% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 590.95ms | 827.53ms | 1.4× | 1.7% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.24s | 1.25s | 1.0× | 2.9% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 1.14s | 1.20s | 1.1× | 5.7% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 1.04s | 1.11s | 1.1× | 3.5% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.52s | 2.74s | 1.1× | 2.3% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.58s | 2.42s | 0.9× | 0.9% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.28s | 2.35s | 1.0× | 1.7% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.17s | 2.34s | 1.1× | 1.0% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 187.46ms | 590.87ms | 3.2× | 5.9% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 162.27ms | 490.67ms | 3.0× | 9.8% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 207.52ms | 538.49ms | 2.6× | 22.1% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 164.37ms | 536.55ms | 3.3× | 15.5% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 24.38ms | 29.93ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_range_select` | 11.39ms | 11.99ms | 1.1× | 3.7% | PASS |
| mem_reads | `oltp_sum_range` | 9.55ms | 11.27ms | 1.2× | 2.4% | PASS |
| mem_reads | `oltp_order_range` | 2.71ms | 2.95ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 3.60ms | 4.07ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 3.84ms | 5.23ms | 1.4× | 1.8% | PASS |
| mem_reads | `select_random_points` | 10.03ms | 10.97ms | 1.1× | 2.7% | PASS |
| mem_reads | `select_random_ranges` | 2.97ms | 3.98ms | 1.3× | 3.0% | PASS |
| mem_reads | `covering_index_scan` | 4.17ms | 4.21ms | 1.0× | 2.1% | PASS |
| mem_reads | `groupby_scan` | 30.43ms | 33.86ms | 1.1× | 0.8% | PASS |
| mem_reads | `index_join` | 5.83ms | 7.94ms | 1.4× | 1.9% | PASS |
| mem_reads | `index_join_scan` | 3.35ms | 4.57ms | 1.4× | 3.0% | PASS |
| mem_reads | `types_table_scan` | 1.03s | 1.16s | 1.1× | 0.9% | PASS |
| mem_reads | `table_scan` | 1.17s | 1.26s | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_read_only` | 105.79ms | 121.34ms | 1.1× | 1.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 176.35ms | 279.93ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 15.02ms | 28.77ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 51.49ms | 104.50ms | 2.0× | 1.7% | PASS |
| mem_writes | `oltp_update_non_index` | 35.69ms | 64.67ms | 1.8× | 2.3% | PASS |
| mem_writes | `oltp_delete_insert` | 48.14ms | 84.40ms | 1.8× | 2.2% | PASS |
| mem_writes | `oltp_write_only` | 23.81ms | 53.49ms | 2.2× | 2.4% | PASS |
| mem_writes | `types_delete_insert` | 26.20ms | 44.00ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 78.63ms | 125.17ms | 1.6× | 2.6% | PASS |
| file_reads | `oltp_point_select` | 95.48ms | 48.88ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 19.34ms | 13.91ms | 0.7× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 17.13ms | 13.63ms | 0.8× | 2.2% | PASS |
| file_reads | `oltp_order_range` | 3.74ms | 3.40ms | 0.9× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.75ms | 4.49ms | 0.9× | 2.9% | PASS |
| file_reads | `oltp_index_scan` | 11.15ms | 7.26ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_points` | 17.77ms | 12.98ms | 0.7× | 3.7% | PASS |
| file_reads | `select_random_ranges` | 9.93ms | 5.80ms | 0.6× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 11.37ms | 6.22ms | 0.5× | 2.1% | PASS |
| file_reads | `groupby_scan` | 31.82ms | 34.61ms | 1.1× | 1.1% | PASS |
| file_reads | `index_join` | 9.72ms | 9.54ms | 1.0× | 1.9% | PASS |
| file_reads | `index_join_scan` | 4.32ms | 4.96ms | 1.1× | 2.4% | PASS |
| file_reads | `types_table_scan` | 1.03s | 1.16s | 1.1× | 1.4% | PASS |
| file_reads | `table_scan` | 1.16s | 1.26s | 1.1× | 1.2% | PASS |
| file_reads | `oltp_read_only` | 207.04ms | 147.84ms | 0.7× | 1.0% | PASS |
| file_writes | `oltp_bulk_insert` | 189.69ms | 287.15ms | 1.5× | 0.8% | PASS |
| file_writes | `oltp_insert` | 21.49ms | 31.77ms | 1.5× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 77.13ms | 114.20ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 57.68ms | 76.40ms | 1.3× | 1.8% | PASS |
| file_writes | `oltp_delete_insert` | 67.40ms | 90.07ms | 1.3× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 44.38ms | 58.47ms | 1.3× | 2.1% | PASS |
| file_writes | `types_delete_insert` | 40.44ms | 44.79ms | 1.1× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 92.74ms | 124.67ms | 1.3× | 2.3% | PASS |
| ac_reads | `oltp_point_select` | 46.78ms | 47.30ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_range_select` | 13.82ms | 13.81ms | 1.0× | 2.3% | PASS |
| ac_reads | `oltp_sum_range` | 12.40ms | 13.58ms | 1.1× | 2.1% | PASS |
| ac_reads | `oltp_order_range` | 3.30ms | 3.38ms | 1.0× | 3.9% | PASS |
| ac_reads | `oltp_distinct_range` | 4.21ms | 4.48ms | 1.1× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.48ms | 7.27ms | 1.1× | 2.5% | PASS |
| ac_reads | `select_random_points` | 13.52ms | 12.98ms | 1.0× | 2.9% | PASS |
| ac_reads | `select_random_ranges` | 5.52ms | 5.82ms | 1.1× | 2.5% | PASS |
| ac_reads | `covering_index_scan` | 7.06ms | 6.37ms | 0.9× | 3.0% | PASS |
| ac_reads | `groupby_scan` | 31.54ms | 34.90ms | 1.1× | 1.2% | PASS |
| ac_reads | `index_join` | 7.39ms | 9.59ms | 1.3× | 2.6% | PASS |
| ac_reads | `index_join_scan` | 3.84ms | 4.96ms | 1.3× | 2.3% | PASS |
| ac_reads | `types_table_scan` | 1.03s | 1.16s | 1.1× | 1.0% | PASS |
| ac_reads | `table_scan` | 1.19s | 1.27s | 1.1× | 2.4% | PASS |
| ac_reads | `oltp_read_only` | 138.84ms | 147.67ms | 1.1× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.29ms | 58.35ms | 2.9× | 4.5% | PASS |
| ac_writes | `oltp_insert_ac` | 24.24ms | 74.22ms | 3.1× | 5.7% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.05ms | 87.71ms | 3.4× | 6.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 22.47ms | 69.62ms | 3.1× | 7.2% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.79ms | 76.04ms | 3.3× | 5.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 22.87ms | 75.50ms | 3.3× | 6.0% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.34ms | 66.85ms | 3.3× | 7.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 28.40ms | 82.59ms | 2.9× | 4.7% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 30.77ms | 29.02ms | 0.9× | 3.0% | PASS |
| mem_reads | `oltp_range_select` | 15.88ms | 11.30ms | 0.7× | 2.5% | PASS |
| mem_reads | `oltp_sum_range` | 14.34ms | 11.28ms | 0.8× | 2.4% | PASS |
| mem_reads | `oltp_order_range` | 2.99ms | 2.63ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 3.74ms | 3.46ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 3.31ms | 5.01ms | 1.5× | 1.4% | PASS |
| mem_reads | `select_random_points` | 21.19ms | 17.31ms | 0.8× | 4.6% | PASS |
| mem_reads | `select_random_ranges` | 2.98ms | 4.28ms | 1.4× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 3.32ms | 3.75ms | 1.1× | 3.6% | PASS |
| mem_reads | `groupby_scan` | 28.52ms | 28.54ms | 1.0× | 0.9% | PASS |
| mem_reads | `index_join` | 9.30ms | 7.33ms | 0.8× | 4.1% | PASS |
| mem_reads | `index_join_scan` | 3.37ms | 4.91ms | 1.5× | 3.2% | PASS |
| mem_reads | `types_table_scan` | 920.40ms | 980.45ms | 1.1× | 1.8% | PASS |
| mem_reads | `table_scan` | 1.04s | 1.07s | 1.0× | 2.2% | PASS |
| mem_reads | `oltp_read_only` | 118.00ms | 105.92ms | 0.9× | 2.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 179.14ms | 279.13ms | 1.6× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 13.91ms | 30.89ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 58.78ms | 123.84ms | 2.1× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 44.41ms | 74.06ms | 1.7× | 2.3% | PASS |
| mem_writes | `oltp_delete_insert` | 45.92ms | 87.87ms | 1.9× | 1.9% | PASS |
| mem_writes | `oltp_write_only` | 23.84ms | 51.48ms | 2.2× | 1.6% | PASS |
| mem_writes | `types_delete_insert` | 32.07ms | 43.96ms | 1.4× | 2.3% | PASS |
| mem_writes | `oltp_read_write` | 78.52ms | 111.79ms | 1.4× | 2.3% | PASS |
| file_reads | `oltp_point_select` | 97.85ms | 45.05ms | 0.5× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 22.29ms | 12.78ms | 0.6× | 1.8% | PASS |
| file_reads | `oltp_sum_range` | 21.00ms | 12.85ms | 0.6× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 3.77ms | 2.83ms | 0.8× | 0.9% | PASS |
| file_reads | `oltp_distinct_range` | 4.56ms | 3.65ms | 0.8× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 10.37ms | 6.72ms | 0.6× | 0.7% | PASS |
| file_reads | `select_random_points` | 26.64ms | 18.83ms | 0.7× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 9.84ms | 5.95ms | 0.6× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 10.60ms | 5.64ms | 0.5× | 0.6% | PASS |
| file_reads | `groupby_scan` | 29.64ms | 28.88ms | 1.0× | 1.2% | PASS |
| file_reads | `index_join` | 13.57ms | 8.99ms | 0.7× | 1.4% | PASS |
| file_reads | `index_join_scan` | 4.13ms | 5.15ms | 1.2× | 1.3% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.03s | 1.0× | 6.6% | PASS |
| file_reads | `table_scan` | 1.24s | 1.12s | 0.9× | 2.9% | PASS |
| file_reads | `oltp_read_only` | 214.78ms | 129.67ms | 0.6× | 1.7% | PASS |
| file_writes | `oltp_bulk_insert` | 260.77ms | 354.35ms | 1.4× | 3.8% | PASS |
| file_writes | `oltp_insert` | 25.71ms | 53.62ms | 2.1× | 4.9% | PASS |
| file_writes | `oltp_update_index` | 176.95ms | 199.67ms | 1.1× | 2.7% | PASS |
| file_writes | `oltp_update_non_index` | 141.95ms | 128.98ms | 0.9× | 0.8% | PASS |
| file_writes | `oltp_delete_insert` | 182.29ms | 154.99ms | 0.9× | 2.8% | PASS |
| file_writes | `oltp_write_only` | 121.85ms | 108.71ms | 0.9× | 0.4% | PASS |
| file_writes | `types_delete_insert` | 144.19ms | 79.18ms | 0.5× | 4.0% | PASS |
| file_writes | `oltp_read_write` | 181.91ms | 172.83ms | 1.0× | 2.9% | PASS |
| ac_reads | `oltp_point_select` | 52.69ms | 46.05ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 17.50ms | 12.85ms | 0.7× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 15.91ms | 12.67ms | 0.8× | 0.8% | PASS |
| ac_reads | `oltp_order_range` | 3.32ms | 2.82ms | 0.8× | 0.8% | PASS |
| ac_reads | `oltp_distinct_range` | 4.06ms | 3.65ms | 0.9× | 0.6% | PASS |
| ac_reads | `oltp_index_scan` | 5.76ms | 6.68ms | 1.2× | 0.8% | PASS |
| ac_reads | `select_random_points` | 20.96ms | 18.61ms | 0.9× | 1.1% | PASS |
| ac_reads | `select_random_ranges` | 5.17ms | 5.94ms | 1.1× | 0.5% | PASS |
| ac_reads | `covering_index_scan` | 5.89ms | 5.60ms | 1.0× | 0.8% | PASS |
| ac_reads | `groupby_scan` | 29.30ms | 28.95ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 11.22ms | 8.90ms | 0.8× | 0.9% | PASS |
| ac_reads | `index_join_scan` | 3.73ms | 5.16ms | 1.4× | 1.2% | PASS |
| ac_reads | `types_table_scan` | 1.02s | 1.01s | 1.0× | 2.8% | PASS |
| ac_reads | `table_scan` | 1.23s | 1.12s | 0.9× | 1.5% | PASS |
| ac_reads | `oltp_read_only` | 151.38ms | 130.84ms | 0.9× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 18.04ms | 47.21ms | 2.6× | 4.5% | PASS |
| ac_writes | `oltp_insert_ac` | 20.47ms | 58.19ms | 2.8× | 8.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.61ms | 73.39ms | 3.6× | 10.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.75ms | 56.29ms | 3.2× | 10.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 20.13ms | 62.20ms | 3.1× | 6.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.73ms | 64.18ms | 3.3× | 9.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 19.57ms | 59.77ms | 3.1× | 19.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 25.96ms | 69.45ms | 2.7× | 10.4% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 28.91ms | 28.57ms | 1.0× | 2.7% | PASS |
| mem_reads | `oltp_range_select` | 14.26ms | 11.06ms | 0.8× | 1.9% | PASS |
| mem_reads | `oltp_sum_range` | 13.41ms | 11.02ms | 0.8× | 1.8% | PASS |
| mem_reads | `oltp_order_range` | 2.89ms | 2.61ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 3.61ms | 3.45ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.26ms | 4.92ms | 1.5× | 2.1% | PASS |
| mem_reads | `select_random_points` | 18.30ms | 16.73ms | 0.9× | 2.3% | PASS |
| mem_reads | `select_random_ranges` | 2.87ms | 4.34ms | 1.5× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 3.32ms | 3.88ms | 1.2× | 2.0% | PASS |
| mem_reads | `groupby_scan` | 28.46ms | 28.67ms | 1.0× | 1.0% | PASS |
| mem_reads | `index_join` | 9.53ms | 8.11ms | 0.9× | 3.1% | PASS |
| mem_reads | `index_join_scan` | 3.29ms | 4.98ms | 1.5× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 963.69ms | 1.01s | 1.1× | 3.1% | PASS |
| mem_reads | `table_scan` | 1.11s | 1.10s | 1.0× | 3.5% | PASS |
| mem_reads | `oltp_read_only` | 118.78ms | 107.37ms | 0.9× | 2.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 178.29ms | 277.89ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 14.07ms | 30.82ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 54.49ms | 119.28ms | 2.2× | 2.3% | PASS |
| mem_writes | `oltp_update_non_index` | 41.32ms | 70.46ms | 1.7× | 2.4% | PASS |
| mem_writes | `oltp_delete_insert` | 44.95ms | 89.11ms | 2.0× | 2.6% | PASS |
| mem_writes | `oltp_write_only` | 23.90ms | 52.90ms | 2.2× | 2.0% | PASS |
| mem_writes | `types_delete_insert` | 33.12ms | 45.54ms | 1.4× | 3.0% | PASS |
| mem_writes | `oltp_read_write` | 86.29ms | 118.63ms | 1.4× | 3.9% | PASS |
| file_reads | `oltp_point_select` | 98.50ms | 45.00ms | 0.5× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 21.82ms | 12.52ms | 0.6× | 3.0% | PASS |
| file_reads | `oltp_sum_range` | 20.74ms | 12.53ms | 0.6× | 1.9% | PASS |
| file_reads | `oltp_order_range` | 3.71ms | 2.81ms | 0.8× | 1.1% | PASS |
| file_reads | `oltp_distinct_range` | 4.47ms | 3.67ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_index_scan` | 10.56ms | 6.76ms | 0.6× | 1.2% | PASS |
| file_reads | `select_random_points` | 27.97ms | 18.95ms | 0.7× | 2.8% | PASS |
| file_reads | `select_random_ranges` | 9.87ms | 6.05ms | 0.6× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 10.65ms | 5.60ms | 0.5× | 0.9% | PASS |
| file_reads | `groupby_scan` | 29.85ms | 28.87ms | 1.0× | 1.1% | PASS |
| file_reads | `index_join` | 13.53ms | 8.90ms | 0.7× | 1.8% | PASS |
| file_reads | `index_join_scan` | 4.14ms | 5.12ms | 1.2× | 2.4% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.03s | 1.0× | 3.8% | PASS |
| file_reads | `table_scan` | 1.15s | 1.10s | 1.0× | 4.6% | PASS |
| file_reads | `oltp_read_only` | 218.96ms | 130.52ms | 0.6× | 1.6% | PASS |
| file_writes | `oltp_bulk_insert` | 241.40ms | 338.54ms | 1.4× | 5.9% | PASS |
| file_writes | `oltp_insert` | 24.72ms | 53.45ms | 2.2× | 3.5% | PASS |
| file_writes | `oltp_update_index` | 160.25ms | 198.40ms | 1.2× | 5.5% | PASS |
| file_writes | `oltp_update_non_index` | 136.94ms | 128.29ms | 0.9× | 5.5% | PASS |
| file_writes | `oltp_delete_insert` | 165.21ms | 144.35ms | 0.9× | 3.1% | PASS |
| file_writes | `oltp_write_only` | 117.29ms | 98.90ms | 0.8× | 9.9% | PASS |
| file_writes | `types_delete_insert` | 118.88ms | 79.07ms | 0.7× | 8.6% | PASS |
| file_writes | `oltp_read_write` | 179.99ms | 160.96ms | 0.9× | 10.1% | PASS |
| ac_reads | `oltp_point_select` | 51.05ms | 45.24ms | 0.9× | 1.9% | PASS |
| ac_reads | `oltp_range_select` | 17.31ms | 12.62ms | 0.7× | 1.8% | PASS |
| ac_reads | `oltp_sum_range` | 15.43ms | 12.41ms | 0.8× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 3.23ms | 2.80ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_distinct_range` | 4.00ms | 3.66ms | 0.9× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 5.81ms | 6.64ms | 1.1× | 1.2% | PASS |
| ac_reads | `select_random_points` | 20.43ms | 17.95ms | 0.9× | 2.1% | PASS |
| ac_reads | `select_random_ranges` | 5.07ms | 5.96ms | 1.2× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 5.92ms | 5.57ms | 0.9× | 0.9% | PASS |
| ac_reads | `groupby_scan` | 28.88ms | 28.71ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 10.92ms | 8.88ms | 0.8× | 1.6% | PASS |
| ac_reads | `index_join_scan` | 3.69ms | 5.03ms | 1.4× | 1.8% | PASS |
| ac_reads | `types_table_scan` | 915.74ms | 984.25ms | 1.1× | 3.7% | PASS |
| ac_reads | `table_scan` | 1.05s | 1.08s | 1.0× | 3.4% | PASS |
| ac_reads | `oltp_read_only` | 149.23ms | 130.59ms | 0.9× | 1.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 25.38ms | 52.02ms | 2.0× | 40.0% | PASS |
| ac_writes | `oltp_insert_ac` | 26.53ms | 66.21ms | 2.5× | 9.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 28.51ms | 78.36ms | 2.7× | 21.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 24.94ms | 64.46ms | 2.6× | 24.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 26.61ms | 68.45ms | 2.6× | 24.2% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.11ms | 72.67ms | 2.8× | 22.3% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.88ms | 59.16ms | 3.1× | 14.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 30.57ms | 77.16ms | 2.5× | 16.5% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 25.36ms | 29.36ms | 1.2× | 1.1% | PASS |
| mem_reads | `oltp_range_select` | 15.19ms | 16.26ms | 1.1× | 1.1% | PASS |
| mem_reads | `oltp_sum_range` | 13.29ms | 15.76ms | 1.2× | 1.3% | PASS |
| mem_reads | `oltp_order_range` | 2.88ms | 3.08ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_distinct_range` | 3.67ms | 3.96ms | 1.1× | 0.7% | PASS |
| mem_reads | `oltp_index_scan` | 3.54ms | 4.48ms | 1.3× | 1.5% | PASS |
| mem_reads | `select_random_points` | 19.59ms | 23.03ms | 1.2× | 1.4% | PASS |
| mem_reads | `select_random_ranges` | 5.73ms | 6.53ms | 1.1× | 1.3% | PASS |
| mem_reads | `covering_index_scan` | 3.29ms | 3.28ms | 1.0× | 1.5% | PASS |
| mem_reads | `groupby_scan` | 28.86ms | 32.34ms | 1.1× | 0.7% | PASS |
| mem_reads | `index_join` | 6.24ms | 7.82ms | 1.3× | 1.4% | PASS |
| mem_reads | `index_join_scan` | 3.24ms | 4.69ms | 1.4× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 858.34ms | 964.43ms | 1.1× | 0.4% | PASS |
| mem_reads | `table_scan` | 987.69ms | 1.05s | 1.1× | 0.5% | PASS |
| mem_reads | `oltp_read_only` | 112.75ms | 127.70ms | 1.1× | 0.7% | PASS |
| mem_writes | `oltp_bulk_insert` | 184.07ms | 260.53ms | 1.4× | 0.5% | PASS |
| mem_writes | `oltp_insert` | 14.44ms | 26.93ms | 1.9× | 0.6% | PASS |
| mem_writes | `oltp_update_index` | 52.38ms | 98.79ms | 1.9× | 0.9% | PASS |
| mem_writes | `oltp_update_non_index` | 40.33ms | 65.93ms | 1.6× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 38.73ms | 75.34ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_write_only` | 21.43ms | 47.15ms | 2.2× | 1.2% | PASS |
| mem_writes | `types_delete_insert` | 25.28ms | 40.63ms | 1.6× | 1.0% | PASS |
| mem_writes | `oltp_read_write` | 74.68ms | 119.33ms | 1.6× | 0.9% | PASS |
| file_reads | `oltp_point_select` | 93.86ms | 45.95ms | 0.5× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 23.26ms | 18.05ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_sum_range` | 20.95ms | 17.72ms | 0.8× | 1.5% | PASS |
| file_reads | `oltp_order_range` | 3.80ms | 3.33ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_distinct_range` | 4.55ms | 4.21ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 10.83ms | 6.58ms | 0.6× | 1.3% | PASS |
| file_reads | `select_random_points` | 27.70ms | 25.18ms | 0.9× | 1.1% | PASS |
| file_reads | `select_random_ranges` | 12.92ms | 8.34ms | 0.6× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 10.59ms | 5.34ms | 0.5× | 1.1% | PASS |
| file_reads | `groupby_scan` | 30.17ms | 32.73ms | 1.1× | 0.9% | PASS |
| file_reads | `index_join` | 10.30ms | 9.54ms | 0.9× | 1.0% | PASS |
| file_reads | `index_join_scan` | 4.15ms | 5.02ms | 1.2× | 1.7% | PASS |
| file_reads | `types_table_scan` | 862.16ms | 961.67ms | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 989.86ms | 1.05s | 1.1× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 212.10ms | 152.31ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 236.28ms | 315.75ms | 1.3× | 4.0% | PASS |
| file_writes | `oltp_insert` | 31.68ms | 44.95ms | 1.4× | 4.2% | PASS |
| file_writes | `oltp_update_index` | 161.43ms | 165.84ms | 1.0× | 2.3% | PASS |
| file_writes | `oltp_update_non_index` | 136.79ms | 117.59ms | 0.9× | 2.9% | PASS |
| file_writes | `oltp_delete_insert` | 133.02ms | 131.87ms | 1.0× | 2.0% | PASS |
| file_writes | `oltp_write_only` | 97.63ms | 93.52ms | 1.0× | 2.2% | PASS |
| file_writes | `types_delete_insert` | 90.08ms | 74.78ms | 0.8× | 13.4% | PASS |
| file_writes | `oltp_read_write` | 150.79ms | 165.47ms | 1.1× | 4.3% | PASS |
| ac_reads | `oltp_point_select` | 46.87ms | 45.80ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_range_select` | 18.40ms | 18.02ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_sum_range` | 16.08ms | 17.55ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_order_range` | 3.35ms | 3.33ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_distinct_range` | 4.13ms | 4.23ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.21ms | 6.63ms | 1.1× | 1.3% | PASS |
| ac_reads | `select_random_points` | 22.84ms | 25.18ms | 1.1× | 1.0% | PASS |
| ac_reads | `select_random_ranges` | 8.28ms | 8.37ms | 1.0× | 1.1% | PASS |
| ac_reads | `covering_index_scan` | 5.87ms | 5.37ms | 0.9× | 1.3% | PASS |
| ac_reads | `groupby_scan` | 29.73ms | 32.70ms | 1.1× | 0.9% | PASS |
| ac_reads | `index_join` | 7.82ms | 9.59ms | 1.2× | 1.0% | PASS |
| ac_reads | `index_join_scan` | 3.70ms | 5.00ms | 1.4× | 1.5% | PASS |
| ac_reads | `types_table_scan` | 863.44ms | 961.53ms | 1.1× | 0.6% | PASS |
| ac_reads | `table_scan` | 990.46ms | 1.05s | 1.1× | 0.6% | PASS |
| ac_reads | `oltp_read_only` | 144.41ms | 152.07ms | 1.1× | 0.8% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 18.30ms | 49.73ms | 2.7× | 10.6% | PASS |
| ac_writes | `oltp_insert_ac` | 21.05ms | 66.53ms | 3.2× | 21.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.45ms | 80.90ms | 3.8× | 18.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 17.70ms | 61.69ms | 3.5× | 13.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 19.98ms | 71.23ms | 3.6× | 10.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.88ms | 66.41ms | 3.3× | 17.2% | PASS |
| ac_writes | `types_delete_insert_ac` | 18.96ms | 65.15ms | 3.4× | 16.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.04ms | 74.90ms | 2.8× | 14.2% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 44s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 67.26ms | 130.00ms | 51.7% | 0.3% | PASS |
| `status_dirty_many_tables` | 69.73ms | 130.00ms | 53.6% | 0.2% | PASS |
| `diff_regular_working_one_table` | 63.38ms | 120.00ms | 52.8% | 0.3% | PASS |
| `diff_regular_working_many_tables` | 73.26ms | 140.00ms | 52.3% | 0.2% | PASS |
| `diff_stat_working_many_tables` | 73.33ms | 140.00ms | 52.4% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 73.84ms | 140.00ms | 52.7% | 0.2% | PASS |
| `branch_list_many_branches` | 15.27ms | 35.00ms | 43.6% | 0.7% | PASS |
| `branch_create_delete` | 22.70ms | 40.00ms | 56.7% | 6.1% | PASS |
| `checkout_branch_clean` | 111.12ms | 150.00ms | 74.1% | 12.6% | PASS |
| `merge_data_no_conflicts` | 28.67ms | 50.00ms | 57.3% | 4.4% | PASS |
| `merge_schema_no_conflicts` | 15.18ms | 35.00ms | 43.4% | 3.2% | PASS |
| `merge_data_conflicts` | 22.49ms | 180.00ms | 12.5% | 0.7% | PASS |
| `merge_data_conflicts_with_resolve` | 22.57ms | 180.00ms | 12.5% | 0.6% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
