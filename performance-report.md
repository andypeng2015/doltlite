# DoltLite Performance Report

> Nightly result: **FAIL**
>
> Generated: 2026-11-06 18:24 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu26 20261101.186.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/42256373581)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.10s | 10.74s | 1.1× | 1.7% | **PASS** |
| Writes | 2.07s | 3.58s | 1.7× | 1.3% | **FAIL** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.94s | 10.96s | 1.0× | 1.3% | **PASS** |
| Writes | 3.38s | 4.29s | 1.3× | 1.6% | **PASS** |
| Autocommit writes | 755.05ms | 2.30s | 3.0× | 6.4% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.42s | 2.67s | 1.1× | 1.5% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.51s | 2.37s | 0.9× | 2.3% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.48s | 2.78s | 1.1× | 1.7% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.68s | 2.92s | 1.1× | 1.7% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 424.46ms | 749.07ms | 1.8× | 1.5% | **FAIL** |
| In-memory | Writes | textpk | 8 | 55 | 478.89ms | 808.09ms | 1.7× | 1.6% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 574.23ms | 1.01s | 1.8× | 1.2% | **FAIL** |
| In-memory | Writes | compositepk | 8 | 55 | 595.29ms | 1.01s | 1.7× | 1.2% | **FAIL** |
| File-backed | Reads | int | 15 | 55 | 2.84s | 2.78s | 1.0× | 1.3% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.55s | 2.37s | 0.9× | 1.0% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 2.73s | 2.85s | 1.0× | 1.7% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.83s | 2.96s | 1.0× | 1.6% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 590.01ms | 830.30ms | 1.4× | 1.5% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 1.22s | 1.25s | 1.0× | 5.2% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 795.69ms | 1.12s | 1.4× | 1.6% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 769.08ms | 1.09s | 1.4× | 1.5% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.51s | 2.73s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.46s | 2.39s | 1.0× | 0.9% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 2.58s | 2.85s | 1.1× | 2.1% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.63s | 2.95s | 1.1× | 1.3% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 199.81ms | 606.80ms | 3.0× | 7.5% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 170.91ms | 530.37ms | 3.1× | 16.1% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 197.35ms | 589.96ms | 3.0× | 5.0% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 186.97ms | 574.73ms | 3.1× | 4.3% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 23.99ms | 29.77ms | 1.2× | 1.5% | PASS |
| mem_reads | `oltp_range_select` | 10.94ms | 11.62ms | 1.1× | 1.3% | PASS |
| mem_reads | `oltp_sum_range` | 9.47ms | 11.17ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_order_range` | 2.74ms | 2.94ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 3.67ms | 4.08ms | 1.1× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 3.83ms | 5.30ms | 1.4× | 1.6% | PASS |
| mem_reads | `select_random_points` | 9.94ms | 10.89ms | 1.1× | 3.6% | PASS |
| mem_reads | `select_random_ranges` | 3.02ms | 3.94ms | 1.3× | 2.9% | PASS |
| mem_reads | `covering_index_scan` | 4.13ms | 4.36ms | 1.1× | 3.0% | PASS |
| mem_reads | `groupby_scan` | 30.35ms | 33.90ms | 1.1× | 1.0% | PASS |
| mem_reads | `index_join` | 5.69ms | 7.98ms | 1.4× | 1.6% | PASS |
| mem_reads | `index_join_scan` | 3.01ms | 4.47ms | 1.5× | 3.7% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.16s | 1.1× | 1.0% | PASS |
| mem_reads | `table_scan` | 1.16s | 1.26s | 1.1× | 1.5% | PASS |
| mem_reads | `oltp_read_only` | 110.19ms | 122.72ms | 1.1× | 1.4% | PASS |
| mem_writes | `oltp_bulk_insert` | 170.79ms | 272.59ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 14.65ms | 28.08ms | 1.9× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 48.32ms | 99.22ms | 2.1× | 1.7% | PASS |
| mem_writes | `oltp_update_non_index` | 32.83ms | 62.23ms | 1.9× | 1.4% | PASS |
| mem_writes | `oltp_delete_insert` | 43.08ms | 77.45ms | 1.8× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 21.44ms | 49.93ms | 2.3× | 1.6% | FAIL |
| mem_writes | `types_delete_insert` | 23.60ms | 40.94ms | 1.7× | 1.7% | PASS |
| mem_writes | `oltp_read_write` | 69.75ms | 118.63ms | 1.7× | 1.8% | PASS |
| file_reads | `oltp_point_select` | 92.50ms | 46.70ms | 0.5× | 0.9% | PASS |
| file_reads | `oltp_range_select` | 18.48ms | 13.59ms | 0.7× | 1.1% | PASS |
| file_reads | `oltp_sum_range` | 16.92ms | 13.20ms | 0.8× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 3.67ms | 3.27ms | 0.9× | 1.7% | PASS |
| file_reads | `oltp_distinct_range` | 4.65ms | 4.38ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 11.27ms | 7.20ms | 0.6× | 1.3% | PASS |
| file_reads | `select_random_points` | 17.59ms | 12.83ms | 0.7× | 1.4% | PASS |
| file_reads | `select_random_ranges` | 10.05ms | 5.71ms | 0.6× | 0.6% | PASS |
| file_reads | `covering_index_scan` | 11.47ms | 6.33ms | 0.6× | 1.2% | PASS |
| file_reads | `groupby_scan` | 31.61ms | 34.34ms | 1.1× | 0.8% | PASS |
| file_reads | `index_join` | 10.02ms | 9.63ms | 1.0× | 1.7% | PASS |
| file_reads | `index_join_scan` | 4.28ms | 4.87ms | 1.1× | 1.6% | PASS |
| file_reads | `types_table_scan` | 1.12s | 1.19s | 1.1× | 0.6% | PASS |
| file_reads | `table_scan` | 1.27s | 1.28s | 1.0× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 213.46ms | 149.33ms | 0.7× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 184.25ms | 285.03ms | 1.5× | 1.2% | PASS |
| file_writes | `oltp_insert` | 21.96ms | 31.83ms | 1.4× | 3.0% | PASS |
| file_writes | `oltp_update_index` | 79.00ms | 117.72ms | 1.5× | 1.4% | PASS |
| file_writes | `oltp_update_non_index` | 59.92ms | 77.86ms | 1.3× | 1.7% | PASS |
| file_writes | `oltp_delete_insert` | 68.20ms | 91.89ms | 1.3× | 1.4% | PASS |
| file_writes | `oltp_write_only` | 44.41ms | 57.70ms | 1.3× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 39.13ms | 43.32ms | 1.1× | 1.5% | PASS |
| file_writes | `oltp_read_write` | 93.15ms | 124.95ms | 1.3× | 1.6% | PASS |
| ac_reads | `oltp_point_select` | 45.38ms | 46.20ms | 1.0× | 1.1% | PASS |
| ac_reads | `oltp_range_select` | 13.36ms | 13.61ms | 1.0× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 11.52ms | 13.14ms | 1.1× | 1.8% | PASS |
| ac_reads | `oltp_order_range` | 3.05ms | 3.19ms | 1.0× | 2.6% | PASS |
| ac_reads | `oltp_distinct_range` | 4.09ms | 4.37ms | 1.1× | 2.2% | PASS |
| ac_reads | `oltp_index_scan` | 6.09ms | 7.02ms | 1.2× | 2.1% | PASS |
| ac_reads | `select_random_points` | 11.54ms | 12.53ms | 1.1× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 5.00ms | 5.62ms | 1.1× | 1.0% | PASS |
| ac_reads | `covering_index_scan` | 6.42ms | 5.92ms | 0.9× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 30.40ms | 34.11ms | 1.1× | 0.7% | PASS |
| ac_reads | `index_join` | 7.12ms | 9.06ms | 1.3× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.56ms | 4.75ms | 1.3× | 2.4% | PASS |
| ac_reads | `types_table_scan` | 1.03s | 1.16s | 1.1× | 0.7% | PASS |
| ac_reads | `table_scan` | 1.19s | 1.26s | 1.1× | 2.7% | PASS |
| ac_reads | `oltp_read_only` | 137.73ms | 146.16ms | 1.1× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 21.85ms | 62.15ms | 2.8× | 8.1% | PASS |
| ac_writes | `oltp_insert_ac` | 24.28ms | 74.47ms | 3.1× | 7.5% | PASS |
| ac_writes | `oltp_update_index_ac` | 26.59ms | 85.41ms | 3.2× | 6.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 23.98ms | 70.14ms | 2.9× | 8.4% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.81ms | 78.76ms | 3.2× | 6.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.56ms | 76.51ms | 3.1× | 5.6% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.57ms | 70.03ms | 3.1× | 13.3% | PASS |
| ac_writes | `oltp_read_write_ac` | 31.16ms | 89.35ms | 2.9× | 7.5% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 30.32ms | 28.98ms | 1.0× | 2.9% | PASS |
| mem_reads | `oltp_range_select` | 15.14ms | 11.19ms | 0.7× | 2.7% | PASS |
| mem_reads | `oltp_sum_range` | 13.29ms | 11.04ms | 0.8× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 2.84ms | 2.61ms | 0.9× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 3.60ms | 3.44ms | 1.0× | 0.8% | PASS |
| mem_reads | `oltp_index_scan` | 3.23ms | 4.84ms | 1.5× | 1.1% | PASS |
| mem_reads | `select_random_points` | 19.27ms | 16.57ms | 0.9× | 3.7% | PASS |
| mem_reads | `select_random_ranges` | 2.90ms | 4.30ms | 1.5× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 3.32ms | 3.79ms | 1.1× | 1.6% | PASS |
| mem_reads | `groupby_scan` | 29.18ms | 28.87ms | 1.0× | 1.1% | PASS |
| mem_reads | `index_join` | 9.87ms | 7.88ms | 0.8× | 4.1% | PASS |
| mem_reads | `index_join_scan` | 3.36ms | 4.86ms | 1.4× | 2.5% | PASS |
| mem_reads | `types_table_scan` | 1.05s | 1.02s | 1.0× | 2.8% | PASS |
| mem_reads | `table_scan` | 1.21s | 1.12s | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_read_only` | 121.82ms | 108.21ms | 0.9× | 2.6% | PASS |
| mem_writes | `oltp_bulk_insert` | 179.49ms | 282.27ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_insert` | 13.81ms | 30.79ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 56.52ms | 121.90ms | 2.2× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 42.27ms | 71.58ms | 1.7× | 2.3% | PASS |
| mem_writes | `oltp_delete_insert` | 45.90ms | 87.91ms | 1.9× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 23.61ms | 52.63ms | 2.2× | 1.8% | PASS |
| mem_writes | `types_delete_insert` | 32.39ms | 45.16ms | 1.4× | 2.3% | PASS |
| mem_writes | `oltp_read_write` | 84.89ms | 115.85ms | 1.4× | 2.1% | PASS |
| file_reads | `oltp_point_select` | 99.27ms | 45.14ms | 0.5× | 1.3% | PASS |
| file_reads | `oltp_range_select` | 22.54ms | 12.77ms | 0.6× | 1.6% | PASS |
| file_reads | `oltp_sum_range` | 21.31ms | 12.98ms | 0.6× | 1.0% | PASS |
| file_reads | `oltp_order_range` | 3.71ms | 2.86ms | 0.8× | 0.8% | PASS |
| file_reads | `oltp_distinct_range` | 4.50ms | 3.67ms | 0.8× | 0.6% | PASS |
| file_reads | `oltp_index_scan` | 10.52ms | 6.74ms | 0.6× | 0.9% | PASS |
| file_reads | `select_random_points` | 27.63ms | 18.72ms | 0.7× | 1.9% | PASS |
| file_reads | `select_random_ranges` | 9.86ms | 5.96ms | 0.6× | 0.7% | PASS |
| file_reads | `covering_index_scan` | 10.66ms | 5.66ms | 0.5× | 0.5% | PASS |
| file_reads | `groupby_scan` | 29.65ms | 28.97ms | 1.0× | 0.7% | PASS |
| file_reads | `index_join` | 13.38ms | 8.80ms | 0.7× | 1.1% | PASS |
| file_reads | `index_join_scan` | 4.08ms | 5.01ms | 1.2× | 1.4% | PASS |
| file_reads | `types_table_scan` | 947.21ms | 992.54ms | 1.0× | 1.0% | PASS |
| file_reads | `table_scan` | 1.13s | 1.09s | 1.0× | 1.4% | PASS |
| file_reads | `oltp_read_only` | 214.83ms | 128.81ms | 0.6× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 259.27ms | 350.17ms | 1.4× | 6.9% | PASS |
| file_writes | `oltp_insert` | 26.70ms | 54.62ms | 2.0× | 4.6% | PASS |
| file_writes | `oltp_update_index` | 171.07ms | 198.66ms | 1.2× | 1.7% | PASS |
| file_writes | `oltp_update_non_index` | 143.74ms | 131.66ms | 0.9× | 3.7% | PASS |
| file_writes | `oltp_delete_insert` | 181.29ms | 158.11ms | 0.9× | 6.4% | PASS |
| file_writes | `oltp_write_only` | 121.00ms | 108.18ms | 0.9× | 4.7% | PASS |
| file_writes | `types_delete_insert` | 140.40ms | 79.06ms | 0.6× | 6.2% | PASS |
| file_writes | `oltp_read_write` | 180.90ms | 168.09ms | 0.9× | 5.7% | PASS |
| ac_reads | `oltp_point_select` | 50.38ms | 44.81ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 17.40ms | 12.68ms | 0.7× | 0.7% | PASS |
| ac_reads | `oltp_sum_range` | 15.58ms | 12.58ms | 0.8× | 0.9% | PASS |
| ac_reads | `oltp_order_range` | 3.22ms | 2.83ms | 0.9× | 0.6% | PASS |
| ac_reads | `oltp_distinct_range` | 3.98ms | 3.66ms | 0.9× | 0.8% | PASS |
| ac_reads | `oltp_index_scan` | 5.75ms | 6.65ms | 1.2× | 1.0% | PASS |
| ac_reads | `select_random_points` | 21.35ms | 18.24ms | 0.9× | 1.7% | PASS |
| ac_reads | `select_random_ranges` | 5.12ms | 5.89ms | 1.2× | 0.7% | PASS |
| ac_reads | `covering_index_scan` | 5.87ms | 5.57ms | 0.9× | 0.6% | PASS |
| ac_reads | `groupby_scan` | 28.69ms | 28.74ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 10.88ms | 8.67ms | 0.8× | 0.9% | PASS |
| ac_reads | `index_join_scan` | 3.67ms | 4.97ms | 1.4× | 0.7% | PASS |
| ac_reads | `types_table_scan` | 915.09ms | 981.75ms | 1.1× | 1.4% | PASS |
| ac_reads | `table_scan` | 1.22s | 1.12s | 0.9× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 151.03ms | 131.28ms | 0.9× | 1.1% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 19.22ms | 51.19ms | 2.7× | 14.3% | PASS |
| ac_writes | `oltp_insert_ac` | 20.84ms | 63.64ms | 3.1× | 15.6% | PASS |
| ac_writes | `oltp_update_index_ac` | 21.90ms | 77.00ms | 3.5× | 20.9% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 19.44ms | 62.07ms | 3.2× | 21.7% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.57ms | 75.74ms | 3.4× | 33.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 20.63ms | 69.20ms | 3.4× | 16.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 19.74ms | 57.99ms | 2.9× | 12.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 26.57ms | 73.56ms | 2.8× | 11.5% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.87ms | 37.88ms | 1.1× | 1.8% | PASS |
| mem_reads | `oltp_range_select` | 16.53ms | 14.01ms | 0.8× | 1.3% | PASS |
| mem_reads | `oltp_sum_range` | 14.54ms | 13.79ms | 0.9× | 2.3% | PASS |
| mem_reads | `oltp_order_range` | 3.27ms | 3.52ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_distinct_range` | 4.25ms | 4.59ms | 1.1× | 1.6% | PASS |
| mem_reads | `oltp_index_scan` | 3.85ms | 6.03ms | 1.6× | 1.7% | PASS |
| mem_reads | `select_random_points` | 20.97ms | 20.61ms | 1.0× | 1.9% | PASS |
| mem_reads | `select_random_ranges` | 3.26ms | 5.30ms | 1.6× | 2.3% | PASS |
| mem_reads | `covering_index_scan` | 4.12ms | 4.45ms | 1.1× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 34.16ms | 35.29ms | 1.0× | 0.7% | PASS |
| mem_reads | `index_join` | 10.06ms | 8.98ms | 0.9× | 2.3% | PASS |
| mem_reads | `index_join_scan` | 3.69ms | 5.44ms | 1.5× | 2.9% | PASS |
| mem_reads | `types_table_scan` | 1.04s | 1.19s | 1.1× | 0.5% | PASS |
| mem_reads | `table_scan` | 1.16s | 1.29s | 1.1× | 0.6% | PASS |
| mem_reads | `oltp_read_only` | 135.89ms | 143.32ms | 1.1× | 1.3% | PASS |
| mem_writes | `oltp_bulk_insert` | 232.87ms | 365.15ms | 1.6× | 1.1% | PASS |
| mem_writes | `oltp_insert` | 17.86ms | 39.49ms | 2.2× | 1.0% | PASS |
| mem_writes | `oltp_update_index` | 63.39ms | 145.10ms | 2.3× | 1.4% | PASS |
| mem_writes | `oltp_update_non_index` | 46.80ms | 88.07ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 52.02ms | 106.61ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 27.40ms | 64.80ms | 2.4× | 1.6% | FAIL |
| mem_writes | `types_delete_insert` | 38.18ms | 57.11ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_read_write` | 95.72ms | 147.20ms | 1.5× | 1.6% | PASS |
| file_reads | `oltp_point_select` | 103.44ms | 56.09ms | 0.5× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 24.11ms | 16.23ms | 0.7× | 2.2% | PASS |
| file_reads | `oltp_sum_range` | 22.54ms | 15.96ms | 0.7× | 2.2% | PASS |
| file_reads | `oltp_order_range` | 4.33ms | 3.83ms | 0.9× | 3.0% | PASS |
| file_reads | `oltp_distinct_range` | 5.36ms | 4.98ms | 0.9× | 3.2% | PASS |
| file_reads | `oltp_index_scan` | 10.93ms | 8.23ms | 0.8× | 1.6% | PASS |
| file_reads | `select_random_points` | 30.00ms | 23.76ms | 0.8× | 2.0% | PASS |
| file_reads | `select_random_ranges` | 10.24ms | 7.18ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 11.14ms | 6.62ms | 0.6× | 1.7% | PASS |
| file_reads | `groupby_scan` | 35.59ms | 36.03ms | 1.0× | 1.0% | PASS |
| file_reads | `index_join` | 14.44ms | 11.09ms | 0.8× | 2.1% | PASS |
| file_reads | `index_join_scan` | 4.79ms | 5.93ms | 1.2× | 2.0% | PASS |
| file_reads | `types_table_scan` | 1.04s | 1.19s | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 1.17s | 1.29s | 1.1× | 0.7% | PASS |
| file_reads | `oltp_read_only` | 239.48ms | 170.25ms | 0.7× | 0.8% | PASS |
| file_writes | `oltp_bulk_insert` | 251.96ms | 380.11ms | 1.5× | 1.3% | PASS |
| file_writes | `oltp_insert` | 25.09ms | 46.99ms | 1.9× | 1.7% | PASS |
| file_writes | `oltp_update_index` | 96.30ms | 162.36ms | 1.7× | 1.5% | PASS |
| file_writes | `oltp_update_non_index` | 84.75ms | 103.35ms | 1.2× | 9.9% | PASS |
| file_writes | `oltp_delete_insert` | 87.39ms | 122.26ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_write_only` | 58.14ms | 77.24ms | 1.3× | 1.7% | PASS |
| file_writes | `types_delete_insert` | 63.39ms | 65.35ms | 1.0× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 128.66ms | 159.39ms | 1.2× | 1.0% | PASS |
| ac_reads | `oltp_point_select` | 57.43ms | 56.08ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 20.17ms | 16.14ms | 0.8× | 1.9% | PASS |
| ac_reads | `oltp_sum_range` | 17.95ms | 15.97ms | 0.9× | 2.2% | PASS |
| ac_reads | `oltp_order_range` | 3.81ms | 3.81ms | 1.0× | 2.7% | PASS |
| ac_reads | `oltp_distinct_range` | 4.80ms | 4.93ms | 1.0× | 2.1% | PASS |
| ac_reads | `oltp_index_scan` | 6.36ms | 8.12ms | 1.3× | 2.2% | PASS |
| ac_reads | `select_random_points` | 25.18ms | 23.58ms | 0.9× | 2.6% | PASS |
| ac_reads | `select_random_ranges` | 5.67ms | 7.14ms | 1.3× | 1.2% | PASS |
| ac_reads | `covering_index_scan` | 6.70ms | 6.56ms | 1.0× | 2.8% | PASS |
| ac_reads | `groupby_scan` | 35.03ms | 35.90ms | 1.0× | 1.4% | PASS |
| ac_reads | `index_join` | 12.17ms | 11.03ms | 0.9× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 4.33ms | 6.01ms | 1.4× | 3.2% | PASS |
| ac_reads | `types_table_scan` | 1.04s | 1.19s | 1.1× | 0.4% | PASS |
| ac_reads | `table_scan` | 1.17s | 1.29s | 1.1× | 0.5% | PASS |
| ac_reads | `oltp_read_only` | 172.88ms | 170.06ms | 1.0× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 23.55ms | 58.41ms | 2.5× | 4.0% | PASS |
| ac_writes | `oltp_insert_ac` | 25.13ms | 74.17ms | 3.0× | 5.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.76ms | 84.14ms | 3.3× | 3.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.22ms | 67.30ms | 3.2× | 3.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 24.55ms | 76.31ms | 3.1× | 5.1% | PASS |
| ac_writes | `oltp_write_only_ac` | 24.18ms | 75.64ms | 3.1× | 5.1% | PASS |
| ac_writes | `types_delete_insert_ac` | 22.35ms | 72.02ms | 3.2× | 7.9% | PASS |
| ac_writes | `oltp_read_write_ac` | 30.61ms | 81.98ms | 2.7× | 5.0% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 32.77ms | 40.60ms | 1.2× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 19.61ms | 21.28ms | 1.1× | 1.7% | PASS |
| mem_reads | `oltp_sum_range` | 17.57ms | 20.66ms | 1.2× | 1.7% | PASS |
| mem_reads | `oltp_order_range` | 3.62ms | 4.29ms | 1.2× | 1.2% | PASS |
| mem_reads | `oltp_distinct_range` | 4.64ms | 5.47ms | 1.2× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 4.53ms | 6.13ms | 1.4× | 1.9% | PASS |
| mem_reads | `select_random_points` | 26.06ms | 30.79ms | 1.2× | 2.0% | PASS |
| mem_reads | `select_random_ranges` | 7.67ms | 9.00ms | 1.2× | 1.0% | PASS |
| mem_reads | `covering_index_scan` | 4.19ms | 4.39ms | 1.0× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 37.44ms | 41.27ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 8.12ms | 11.47ms | 1.4× | 1.9% | PASS |
| mem_reads | `index_join_scan` | 4.07ms | 5.74ms | 1.4× | 2.1% | PASS |
| mem_reads | `types_table_scan` | 1.13s | 1.23s | 1.1× | 2.3% | PASS |
| mem_reads | `table_scan` | 1.22s | 1.30s | 1.1× | 3.0% | PASS |
| mem_reads | `oltp_read_only` | 155.80ms | 182.19ms | 1.2× | 1.0% | PASS |
| mem_writes | `oltp_bulk_insert` | 244.25ms | 360.75ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_insert` | 18.62ms | 36.39ms | 2.0× | 0.5% | PASS |
| mem_writes | `oltp_update_index` | 67.90ms | 134.37ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_update_non_index` | 51.00ms | 89.39ms | 1.8× | 1.2% | PASS |
| mem_writes | `oltp_delete_insert` | 49.11ms | 100.58ms | 2.0× | 1.4% | PASS |
| mem_writes | `oltp_write_only` | 26.84ms | 61.87ms | 2.3× | 1.2% | FAIL |
| mem_writes | `types_delete_insert` | 33.00ms | 56.89ms | 1.7× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 104.57ms | 168.25ms | 1.6× | 1.2% | PASS |
| file_reads | `oltp_point_select` | 103.83ms | 59.76ms | 0.6× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 27.14ms | 23.36ms | 0.9× | 2.2% | PASS |
| file_reads | `oltp_sum_range` | 25.34ms | 22.86ms | 0.9× | 1.8% | PASS |
| file_reads | `oltp_order_range` | 4.50ms | 4.61ms | 1.0× | 1.9% | PASS |
| file_reads | `oltp_distinct_range` | 5.64ms | 5.79ms | 1.0× | 1.8% | PASS |
| file_reads | `oltp_index_scan` | 11.78ms | 8.29ms | 0.7× | 1.5% | PASS |
| file_reads | `select_random_points` | 34.56ms | 34.20ms | 1.0× | 1.7% | PASS |
| file_reads | `select_random_ranges` | 15.14ms | 11.14ms | 0.7× | 1.6% | PASS |
| file_reads | `covering_index_scan` | 11.26ms | 6.49ms | 0.6× | 1.0% | PASS |
| file_reads | `groupby_scan` | 38.61ms | 41.56ms | 1.1× | 1.1% | PASS |
| file_reads | `index_join` | 12.27ms | 12.49ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join_scan` | 4.98ms | 5.89ms | 1.2× | 1.9% | PASS |
| file_reads | `types_table_scan` | 1.07s | 1.21s | 1.1× | 1.2% | PASS |
| file_reads | `table_scan` | 1.22s | 1.30s | 1.1× | 2.6% | PASS |
| file_reads | `oltp_read_only` | 255.59ms | 208.94ms | 0.8× | 1.2% | PASS |
| file_writes | `oltp_bulk_insert` | 257.27ms | 369.80ms | 1.4× | 1.2% | PASS |
| file_writes | `oltp_insert` | 25.41ms | 42.16ms | 1.7× | 1.4% | PASS |
| file_writes | `oltp_update_index` | 95.95ms | 146.43ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_update_non_index` | 78.04ms | 103.97ms | 1.3× | 2.0% | PASS |
| file_writes | `oltp_delete_insert` | 74.65ms | 111.18ms | 1.5× | 1.6% | PASS |
| file_writes | `oltp_write_only` | 51.83ms | 73.71ms | 1.4× | 1.3% | PASS |
| file_writes | `types_delete_insert` | 51.86ms | 65.01ms | 1.3× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 134.08ms | 180.51ms | 1.3× | 1.5% | PASS |
| ac_reads | `oltp_point_select` | 56.01ms | 59.55ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_range_select` | 23.38ms | 23.34ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_sum_range` | 20.94ms | 23.04ms | 1.1× | 1.7% | PASS |
| ac_reads | `oltp_order_range` | 4.14ms | 4.60ms | 1.1× | 1.3% | PASS |
| ac_reads | `oltp_distinct_range` | 5.04ms | 5.75ms | 1.1× | 1.2% | PASS |
| ac_reads | `oltp_index_scan` | 6.76ms | 8.05ms | 1.2× | 1.2% | PASS |
| ac_reads | `select_random_points` | 28.88ms | 33.92ms | 1.2× | 2.0% | PASS |
| ac_reads | `select_random_ranges` | 10.30ms | 11.14ms | 1.1× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 6.77ms | 6.43ms | 0.9× | 1.7% | PASS |
| ac_reads | `groupby_scan` | 37.79ms | 41.46ms | 1.1× | 1.0% | PASS |
| ac_reads | `index_join` | 9.58ms | 12.16ms | 1.3× | 1.7% | PASS |
| ac_reads | `index_join_scan` | 4.39ms | 5.98ms | 1.4× | 2.3% | PASS |
| ac_reads | `types_table_scan` | 1.04s | 1.20s | 1.2× | 0.8% | PASS |
| ac_reads | `table_scan` | 1.19s | 1.30s | 1.1× | 1.1% | PASS |
| ac_reads | `oltp_read_only` | 188.99ms | 210.30ms | 1.1× | 1.9% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 20.63ms | 56.62ms | 2.7× | 3.8% | PASS |
| ac_writes | `oltp_insert_ac` | 22.90ms | 72.83ms | 3.2× | 4.0% | PASS |
| ac_writes | `oltp_update_index_ac` | 25.74ms | 83.53ms | 3.2× | 6.0% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 21.17ms | 65.50ms | 3.1× | 4.0% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 22.71ms | 74.53ms | 3.3× | 4.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 23.43ms | 74.89ms | 3.2× | 3.5% | PASS |
| ac_writes | `types_delete_insert_ac` | 20.42ms | 65.11ms | 3.2× | 6.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 29.98ms | 81.73ms | 2.7× | 5.5% | PASS |

</details>

</details>

## Version-control latency

Wall time: 2m 0s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 90.03ms | 130.00ms | 69.3% | 0.3% | PASS |
| `status_dirty_many_tables` | 93.71ms | 130.00ms | 72.1% | 0.4% | PASS |
| `diff_regular_working_one_table` | 85.59ms | 120.00ms | 71.3% | 0.4% | PASS |
| `diff_regular_working_many_tables` | 98.55ms | 140.00ms | 70.4% | 0.3% | PASS |
| `diff_stat_working_many_tables` | 98.32ms | 140.00ms | 70.2% | 0.3% | PASS |
| `diff_schema_working_many_tables` | 99.27ms | 140.00ms | 70.9% | 0.3% | PASS |
| `branch_list_many_branches` | 18.49ms | 35.00ms | 52.8% | 0.8% | PASS |
| `branch_create_delete` | 20.86ms | 40.00ms | 52.1% | 1.0% | PASS |
| `checkout_branch_clean` | 55.69ms | 150.00ms | 37.1% | 0.9% | PASS |
| `merge_data_no_conflicts` | 24.75ms | 50.00ms | 49.5% | 0.8% | PASS |
| `merge_schema_no_conflicts` | 18.00ms | 35.00ms | 51.4% | 1.3% | PASS |
| `merge_data_conflicts` | 27.90ms | 180.00ms | 15.5% | 0.8% | PASS |
| `merge_data_conflicts_with_resolve` | 27.68ms | 180.00ms | 15.4% | 0.8% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
