# DoltLite Performance Report

> Nightly result: **PASS**
>
> Generated: 2026-09-22 15:42 UTC
>
> Commit: [`5fb9dd8656357052aa7d23f23bdc74b94435352b`](https://github.com/dolthub/doltlite/commit/5fb9dd8656357052aa7d23f23bdc74b94435352b)
>
> Runner: ubuntu24 20260907.300.1
>
> [GitHub Actions run](https://github.com/andypeng2015/doltlite/actions/runs/35738051495)

This report compares optimized DoltLite against stock SQLite on the same GitHub-hosted runner. Baseline and candidate execution order alternates on each repetition. Reported timings are medians. Paired-ratio noise is the median absolute deviation of the paired DoltLite/SQLite ratios, expressed as a percentage.

## SQL workload summary

The primary view aggregates all key shapes and compares DoltLite with SQLite by storage mode and operation class.

### In-memory

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.04s | 10.58s | 1.1× | 1.3% | **PASS** |
| Writes | 2.04s | 3.33s | 1.6× | 1.0% | **PASS** |

### File-backed

| Operation | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---:|---:|---:|---:|---|
| Reads | 10.90s | 10.76s | 1.0× | 1.4% | **PASS** |
| Writes | 3.22s | 3.94s | 1.2× | 1.8% | **PASS** |
| Autocommit writes | 821.81ms | 2.80s | 3.4× | 5.7% | **PASS** |

The absolute ceiling is 2.3× per ordinary workload and 1.9× for a section average. Durable autocommit writes use 6.0× and 5.0× ceilings respectively.

<details>
<summary>Key-shape and individual-workload breakdown</summary>

The integer, text, blob, and composite primary-key runs verify that performance holds across key shapes.

| Storage | Operation | Key shape | Workloads | Samples/workload | SQLite median total | DoltLite median total | Ratio | Paired-ratio noise | Result |
|---|---|---|---:|---:|---:|---:|---:|---:|---|
| In-memory | Reads | int | 15 | 55 | 2.00s | 2.11s | 1.1× | 1.2% | **PASS** |
| In-memory | Reads | textpk | 15 | 55 | 2.60s | 2.75s | 1.1× | 1.3% | **PASS** |
| In-memory | Reads | blobpk | 15 | 55 | 2.95s | 2.98s | 1.0× | 1.9% | **PASS** |
| In-memory | Reads | compositepk | 15 | 55 | 2.49s | 2.75s | 1.1× | 1.0% | **PASS** |
| In-memory | Writes | int | 8 | 55 | 309.72ms | 495.96ms | 1.6× | 0.8% | **PASS** |
| In-memory | Writes | textpk | 8 | 55 | 590.66ms | 1.01s | 1.7× | 1.1% | **PASS** |
| In-memory | Writes | blobpk | 8 | 55 | 627.25ms | 995.86ms | 1.6× | 1.8% | **PASS** |
| In-memory | Writes | compositepk | 8 | 55 | 517.08ms | 836.84ms | 1.6× | 0.9% | **PASS** |
| File-backed | Reads | int | 15 | 55 | 2.08s | 2.12s | 1.0× | 1.2% | **PASS** |
| File-backed | Reads | textpk | 15 | 55 | 2.99s | 2.84s | 1.0× | 1.6% | **PASS** |
| File-backed | Reads | blobpk | 15 | 55 | 3.19s | 3.01s | 0.9× | 1.5% | **PASS** |
| File-backed | Reads | compositepk | 15 | 55 | 2.63s | 2.79s | 1.1× | 1.4% | **PASS** |
| File-backed | Writes | int | 8 | 55 | 807.69ms | 826.18ms | 1.0× | 2.0% | **PASS** |
| File-backed | Writes | textpk | 8 | 55 | 938.26ms | 1.12s | 1.2× | 4.4% | **PASS** |
| File-backed | Writes | blobpk | 8 | 55 | 851.27ms | 1.08s | 1.3× | 2.1% | **PASS** |
| File-backed | Writes | compositepk | 8 | 55 | 620.39ms | 916.47ms | 1.5× | 1.2% | **PASS** |
| File-backed | Autocommit reads | int | 15 | 55 | 2.03s | 2.12s | 1.0× | 1.0% | **PASS** |
| File-backed | Autocommit reads | textpk | 15 | 55 | 2.74s | 2.84s | 1.0× | 1.4% | **PASS** |
| File-backed | Autocommit reads | blobpk | 15 | 55 | 3.00s | 3.02s | 1.0× | 1.5% | **PASS** |
| File-backed | Autocommit reads | compositepk | 15 | 55 | 2.56s | 2.79s | 1.1× | 1.1% | **PASS** |
| File-backed | Autocommit writes | int | 8 | 55 | 298.72ms | 851.26ms | 2.8× | 5.8% | **PASS** |
| File-backed | Autocommit writes | textpk | 8 | 55 | 225.63ms | 798.44ms | 3.5× | 6.6% | **PASS** |
| File-backed | Autocommit writes | blobpk | 8 | 55 | 158.27ms | 592.14ms | 3.7× | 5.2% | **PASS** |
| File-backed | Autocommit writes | compositepk | 8 | 55 | 139.19ms | 561.23ms | 4.0× | 5.3% | **PASS** |

<details>
<summary>int workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 17.69ms | 19.70ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 7.89ms | 8.39ms | 1.1× | 3.3% | PASS |
| mem_reads | `oltp_sum_range` | 7.24ms | 8.06ms | 1.1× | 4.3% | PASS |
| mem_reads | `oltp_order_range` | 2.05ms | 2.19ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_distinct_range` | 2.81ms | 3.08ms | 1.1× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 2.91ms | 3.48ms | 1.2× | 1.6% | PASS |
| mem_reads | `select_random_points` | 7.84ms | 8.39ms | 1.1× | 4.2% | PASS |
| mem_reads | `select_random_ranges` | 2.15ms | 2.75ms | 1.3× | 1.8% | PASS |
| mem_reads | `covering_index_scan` | 2.92ms | 2.92ms | 1.0× | 0.8% | PASS |
| mem_reads | `groupby_scan` | 24.42ms | 26.12ms | 1.1× | 0.9% | PASS |
| mem_reads | `index_join` | 4.40ms | 5.99ms | 1.4× | 0.7% | PASS |
| mem_reads | `index_join_scan` | 2.35ms | 3.50ms | 1.5× | 1.6% | PASS |
| mem_reads | `types_table_scan` | 857.49ms | 914.18ms | 1.1× | 0.2% | PASS |
| mem_reads | `table_scan` | 976.28ms | 1.01s | 1.0× | 0.3% | PASS |
| mem_reads | `oltp_read_only` | 78.82ms | 88.30ms | 1.1× | 0.5% | PASS |
| mem_writes | `oltp_bulk_insert` | 122.68ms | 180.02ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 10.92ms | 19.08ms | 1.7× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 36.92ms | 68.72ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_update_non_index` | 24.50ms | 41.27ms | 1.7× | 0.8% | PASS |
| mem_writes | `oltp_delete_insert` | 33.44ms | 50.78ms | 1.5× | 0.9% | PASS |
| mem_writes | `oltp_write_only` | 16.15ms | 32.24ms | 2.0× | 1.0% | PASS |
| mem_writes | `types_delete_insert` | 17.63ms | 26.12ms | 1.5× | 0.8% | PASS |
| mem_writes | `oltp_read_write` | 47.48ms | 77.72ms | 1.6× | 0.8% | PASS |
| file_reads | `oltp_point_select` | 43.79ms | 26.28ms | 0.6× | 0.6% | PASS |
| file_reads | `oltp_range_select` | 11.19ms | 9.58ms | 0.9× | 0.6% | PASS |
| file_reads | `oltp_sum_range` | 10.49ms | 9.33ms | 0.9× | 1.4% | PASS |
| file_reads | `oltp_order_range` | 2.51ms | 2.44ms | 1.0× | 1.2% | PASS |
| file_reads | `oltp_distinct_range` | 3.30ms | 3.28ms | 1.0× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 5.83ms | 4.51ms | 0.8× | 1.9% | PASS |
| file_reads | `select_random_points` | 11.54ms | 10.00ms | 0.9× | 1.3% | PASS |
| file_reads | `select_random_ranges` | 4.91ms | 3.50ms | 0.7× | 1.4% | PASS |
| file_reads | `covering_index_scan` | 5.72ms | 3.74ms | 0.7× | 1.9% | PASS |
| file_reads | `groupby_scan` | 25.27ms | 26.19ms | 1.0× | 0.5% | PASS |
| file_reads | `index_join` | 6.10ms | 7.00ms | 1.1× | 1.3% | PASS |
| file_reads | `index_join_scan` | 2.83ms | 3.90ms | 1.4× | 1.2% | PASS |
| file_reads | `types_table_scan` | 859.16ms | 909.43ms | 1.1× | 0.2% | PASS |
| file_reads | `table_scan` | 976.71ms | 1.01s | 1.0× | 0.4% | PASS |
| file_reads | `oltp_read_only` | 114.70ms | 96.09ms | 0.8× | 0.4% | PASS |
| file_writes | `oltp_bulk_insert` | 168.03ms | 229.00ms | 1.4× | 0.9% | PASS |
| file_writes | `oltp_insert` | 23.28ms | 33.38ms | 1.4× | 5.1% | PASS |
| file_writes | `oltp_update_index` | 129.09ms | 125.45ms | 1.0× | 1.3% | PASS |
| file_writes | `oltp_update_non_index` | 105.33ms | 88.04ms | 0.8× | 0.8% | PASS |
| file_writes | `oltp_delete_insert` | 111.65ms | 100.53ms | 0.9× | 2.9% | PASS |
| file_writes | `oltp_write_only` | 83.38ms | 73.57ms | 0.9× | 1.4% | PASS |
| file_writes | `types_delete_insert` | 71.58ms | 56.41ms | 0.8× | 2.5% | PASS |
| file_writes | `oltp_read_write` | 115.36ms | 119.81ms | 1.0× | 2.9% | PASS |
| ac_reads | `oltp_point_select` | 26.05ms | 26.11ms | 1.0× | 0.7% | PASS |
| ac_reads | `oltp_range_select` | 9.30ms | 9.56ms | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_sum_range` | 8.92ms | 9.25ms | 1.0× | 1.2% | PASS |
| ac_reads | `oltp_order_range` | 2.32ms | 2.43ms | 1.0× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 3.11ms | 3.27ms | 1.1× | 1.0% | PASS |
| ac_reads | `oltp_index_scan` | 4.11ms | 4.51ms | 1.1× | 2.2% | PASS |
| ac_reads | `select_random_points` | 10.09ms | 10.12ms | 1.0× | 1.4% | PASS |
| ac_reads | `select_random_ranges` | 3.27ms | 3.63ms | 1.1× | 2.2% | PASS |
| ac_reads | `covering_index_scan` | 4.00ms | 3.79ms | 0.9× | 1.9% | PASS |
| ac_reads | `groupby_scan` | 25.04ms | 26.10ms | 1.0× | 0.4% | PASS |
| ac_reads | `index_join` | 5.20ms | 6.73ms | 1.3× | 1.9% | PASS |
| ac_reads | `index_join_scan` | 2.69ms | 3.80ms | 1.4× | 1.0% | PASS |
| ac_reads | `types_table_scan` | 859.25ms | 909.88ms | 1.1× | 0.2% | PASS |
| ac_reads | `table_scan` | 973.95ms | 1.01s | 1.0× | 0.3% | PASS |
| ac_reads | `oltp_read_only` | 88.65ms | 95.59ms | 1.1× | 0.6% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 35.78ms | 95.22ms | 2.7× | 4.6% | PASS |
| ac_writes | `oltp_insert_ac` | 36.84ms | 106.07ms | 2.9× | 6.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 38.59ms | 111.78ms | 2.9× | 5.6% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 36.20ms | 104.41ms | 2.9× | 6.8% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 36.86ms | 108.27ms | 2.9× | 6.7% | PASS |
| ac_writes | `oltp_write_only_ac` | 37.93ms | 108.45ms | 2.9× | 5.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 36.24ms | 102.77ms | 2.8× | 5.4% | PASS |
| ac_writes | `oltp_read_write_ac` | 40.27ms | 114.30ms | 2.8× | 5.5% | PASS |

</details>

<details>
<summary>textpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 33.95ms | 38.23ms | 1.1× | 1.4% | PASS |
| mem_reads | `oltp_range_select` | 15.50ms | 14.55ms | 0.9× | 1.3% | PASS |
| mem_reads | `oltp_sum_range` | 15.30ms | 13.85ms | 0.9× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 3.26ms | 3.15ms | 1.0× | 0.9% | PASS |
| mem_reads | `oltp_distinct_range` | 4.37ms | 4.28ms | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_index_scan` | 3.94ms | 6.12ms | 1.6× | 1.7% | PASS |
| mem_reads | `select_random_points` | 20.74ms | 21.11ms | 1.0× | 1.4% | PASS |
| mem_reads | `select_random_ranges` | 3.41ms | 5.30ms | 1.6× | 1.6% | PASS |
| mem_reads | `covering_index_scan` | 4.22ms | 4.45ms | 1.1× | 1.2% | PASS |
| mem_reads | `groupby_scan` | 34.06ms | 34.71ms | 1.0× | 0.6% | PASS |
| mem_reads | `index_join` | 10.12ms | 9.15ms | 0.9× | 2.7% | PASS |
| mem_reads | `index_join_scan` | 3.57ms | 5.58ms | 1.6× | 1.8% | PASS |
| mem_reads | `types_table_scan` | 1.08s | 1.17s | 1.1× | 0.6% | PASS |
| mem_reads | `table_scan` | 1.23s | 1.28s | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_read_only` | 133.25ms | 136.97ms | 1.0× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 244.27ms | 370.76ms | 1.5× | 0.7% | PASS |
| mem_writes | `oltp_insert` | 18.17ms | 38.65ms | 2.1× | 0.8% | PASS |
| mem_writes | `oltp_update_index` | 64.21ms | 141.89ms | 2.2× | 0.9% | PASS |
| mem_writes | `oltp_update_non_index` | 47.32ms | 86.53ms | 1.8× | 1.9% | PASS |
| mem_writes | `oltp_delete_insert` | 53.57ms | 104.63ms | 2.0× | 1.3% | PASS |
| mem_writes | `oltp_write_only` | 27.11ms | 61.45ms | 2.3× | 1.0% | PASS |
| mem_writes | `types_delete_insert` | 38.16ms | 54.60ms | 1.4× | 1.5% | PASS |
| mem_writes | `oltp_read_write` | 97.85ms | 147.48ms | 1.5× | 1.2% | PASS |
| file_reads | `oltp_point_select` | 111.15ms | 57.91ms | 0.5× | 0.7% | PASS |
| file_reads | `oltp_range_select` | 24.65ms | 16.89ms | 0.7× | 1.5% | PASS |
| file_reads | `oltp_sum_range` | 23.54ms | 16.11ms | 0.7× | 1.7% | PASS |
| file_reads | `oltp_order_range` | 4.22ms | 3.46ms | 0.8× | 2.3% | PASS |
| file_reads | `oltp_distinct_range` | 5.41ms | 4.64ms | 0.9× | 1.0% | PASS |
| file_reads | `oltp_index_scan` | 11.91ms | 8.46ms | 0.7× | 1.7% | PASS |
| file_reads | `select_random_points` | 31.23ms | 24.19ms | 0.8× | 1.8% | PASS |
| file_reads | `select_random_ranges` | 11.23ms | 7.37ms | 0.7× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 12.08ms | 6.87ms | 0.6× | 1.4% | PASS |
| file_reads | `groupby_scan` | 35.58ms | 35.34ms | 1.0× | 0.9% | PASS |
| file_reads | `index_join` | 15.13ms | 11.23ms | 0.7× | 2.0% | PASS |
| file_reads | `index_join_scan` | 4.78ms | 6.08ms | 1.3× | 1.6% | PASS |
| file_reads | `types_table_scan` | 1.08s | 1.17s | 1.1× | 0.5% | PASS |
| file_reads | `table_scan` | 1.36s | 1.30s | 1.0× | 3.0% | PASS |
| file_reads | `oltp_read_only` | 255.50ms | 168.83ms | 0.7× | 2.0% | PASS |
| file_writes | `oltp_bulk_insert` | 270.95ms | 385.52ms | 1.4× | 1.0% | PASS |
| file_writes | `oltp_insert` | 26.42ms | 46.02ms | 1.7× | 2.7% | PASS |
| file_writes | `oltp_update_index` | 134.95ms | 162.69ms | 1.2× | 6.7% | PASS |
| file_writes | `oltp_update_non_index` | 104.29ms | 102.42ms | 1.0× | 15.4% | PASS |
| file_writes | `oltp_delete_insert` | 99.26ms | 121.86ms | 1.2× | 1.9% | PASS |
| file_writes | `oltp_write_only` | 85.00ms | 75.72ms | 0.9× | 10.5% | PASS |
| file_writes | `types_delete_insert` | 73.28ms | 64.26ms | 0.9× | 1.8% | PASS |
| file_writes | `oltp_read_write` | 144.11ms | 160.00ms | 1.1× | 6.2% | PASS |
| ac_reads | `oltp_point_select` | 60.23ms | 58.00ms | 1.0× | 0.8% | PASS |
| ac_reads | `oltp_range_select` | 19.42ms | 16.76ms | 0.9× | 2.0% | PASS |
| ac_reads | `oltp_sum_range` | 18.47ms | 16.24ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_order_range` | 3.76ms | 3.46ms | 0.9× | 1.7% | PASS |
| ac_reads | `oltp_distinct_range` | 4.83ms | 4.64ms | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 6.82ms | 8.47ms | 1.2× | 1.4% | PASS |
| ac_reads | `select_random_points` | 25.62ms | 24.29ms | 0.9× | 1.5% | PASS |
| ac_reads | `select_random_ranges` | 6.27ms | 7.38ms | 1.2× | 1.5% | PASS |
| ac_reads | `covering_index_scan` | 7.02ms | 6.83ms | 1.0× | 1.1% | PASS |
| ac_reads | `groupby_scan` | 35.21ms | 35.35ms | 1.0× | 0.7% | PASS |
| ac_reads | `index_join` | 12.34ms | 11.18ms | 0.9× | 2.5% | PASS |
| ac_reads | `index_join_scan` | 4.18ms | 6.08ms | 1.5× | 2.6% | PASS |
| ac_reads | `types_table_scan` | 1.12s | 1.19s | 1.1× | 2.3% | PASS |
| ac_reads | `table_scan` | 1.24s | 1.29s | 1.0× | 1.3% | PASS |
| ac_reads | `oltp_read_only` | 173.11ms | 167.81ms | 1.0× | 1.3% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 25.25ms | 84.89ms | 3.4× | 7.4% | PASS |
| ac_writes | `oltp_insert_ac` | 29.57ms | 96.51ms | 3.3× | 6.8% | PASS |
| ac_writes | `oltp_update_index_ac` | 29.09ms | 111.53ms | 3.8× | 4.7% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 25.28ms | 93.71ms | 3.7× | 6.3% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 28.11ms | 104.13ms | 3.7× | 5.9% | PASS |
| ac_writes | `oltp_write_only_ac` | 26.89ms | 101.62ms | 3.8× | 8.9% | PASS |
| ac_writes | `types_delete_insert_ac` | 26.41ms | 95.91ms | 3.6× | 8.1% | PASS |
| ac_writes | `oltp_read_write_ac` | 35.03ms | 110.12ms | 3.1× | 6.3% | PASS |

</details>

<details>
<summary>blobpk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 38.33ms | 35.94ms | 0.9× | 2.5% | PASS |
| mem_reads | `oltp_range_select` | 18.43ms | 13.83ms | 0.8× | 2.3% | PASS |
| mem_reads | `oltp_sum_range` | 16.61ms | 13.36ms | 0.8× | 2.0% | PASS |
| mem_reads | `oltp_order_range` | 3.52ms | 3.23ms | 0.9× | 1.1% | PASS |
| mem_reads | `oltp_distinct_range` | 4.52ms | 4.35ms | 1.0× | 1.0% | PASS |
| mem_reads | `oltp_index_scan` | 4.10ms | 5.88ms | 1.4× | 1.8% | PASS |
| mem_reads | `select_random_points` | 22.84ms | 19.84ms | 0.9× | 2.6% | PASS |
| mem_reads | `select_random_ranges` | 3.60ms | 5.35ms | 1.5× | 1.2% | PASS |
| mem_reads | `covering_index_scan` | 4.33ms | 4.87ms | 1.1× | 1.7% | PASS |
| mem_reads | `groupby_scan` | 37.38ms | 36.53ms | 1.0× | 1.6% | PASS |
| mem_reads | `index_join` | 11.27ms | 9.56ms | 0.8× | 3.0% | PASS |
| mem_reads | `index_join_scan` | 4.04ms | 5.83ms | 1.4× | 1.9% | PASS |
| mem_reads | `types_table_scan` | 1.21s | 1.27s | 1.1× | 2.0% | PASS |
| mem_reads | `table_scan` | 1.42s | 1.41s | 1.0× | 1.2% | PASS |
| mem_reads | `oltp_read_only` | 149.01ms | 134.75ms | 0.9× | 2.1% | PASS |
| mem_writes | `oltp_bulk_insert` | 247.53ms | 352.21ms | 1.4× | 1.0% | PASS |
| mem_writes | `oltp_insert` | 19.20ms | 39.12ms | 2.0× | 1.1% | PASS |
| mem_writes | `oltp_update_index` | 70.21ms | 145.67ms | 2.1× | 1.8% | PASS |
| mem_writes | `oltp_update_non_index` | 54.14ms | 85.78ms | 1.6× | 1.8% | PASS |
| mem_writes | `oltp_delete_insert` | 56.23ms | 107.11ms | 1.9× | 1.6% | PASS |
| mem_writes | `oltp_write_only` | 29.90ms | 65.60ms | 2.2× | 2.2% | PASS |
| mem_writes | `types_delete_insert` | 41.37ms | 53.91ms | 1.3× | 1.8% | PASS |
| mem_writes | `oltp_read_write` | 108.66ms | 146.45ms | 1.3× | 2.5% | PASS |
| file_reads | `oltp_point_select` | 136.40ms | 59.85ms | 0.4× | 1.2% | PASS |
| file_reads | `oltp_range_select` | 28.40ms | 16.24ms | 0.6× | 2.0% | PASS |
| file_reads | `oltp_sum_range` | 26.31ms | 15.80ms | 0.6× | 1.6% | PASS |
| file_reads | `oltp_order_range` | 4.50ms | 3.52ms | 0.8× | 2.0% | PASS |
| file_reads | `oltp_distinct_range` | 5.54ms | 4.62ms | 0.8× | 1.2% | PASS |
| file_reads | `oltp_index_scan` | 13.95ms | 8.54ms | 0.6× | 1.2% | PASS |
| file_reads | `select_random_points` | 33.96ms | 22.92ms | 0.7× | 2.8% | PASS |
| file_reads | `select_random_ranges` | 13.30ms | 7.76ms | 0.6× | 0.6% | PASS |
| file_reads | `covering_index_scan` | 14.37ms | 7.28ms | 0.5× | 1.4% | PASS |
| file_reads | `groupby_scan` | 38.78ms | 37.16ms | 1.0× | 1.2% | PASS |
| file_reads | `index_join` | 16.71ms | 11.08ms | 0.7× | 2.7% | PASS |
| file_reads | `index_join_scan` | 5.08ms | 6.07ms | 1.2× | 1.8% | PASS |
| file_reads | `types_table_scan` | 1.19s | 1.25s | 1.0× | 1.7% | PASS |
| file_reads | `table_scan` | 1.38s | 1.39s | 1.0× | 1.5% | PASS |
| file_reads | `oltp_read_only` | 288.56ms | 168.55ms | 0.6× | 0.9% | PASS |
| file_writes | `oltp_bulk_insert` | 270.62ms | 364.83ms | 1.3× | 1.1% | PASS |
| file_writes | `oltp_insert` | 26.64ms | 46.27ms | 1.7× | 1.8% | PASS |
| file_writes | `oltp_update_index` | 102.60ms | 158.89ms | 1.5× | 2.1% | PASS |
| file_writes | `oltp_update_non_index` | 91.51ms | 98.46ms | 1.1× | 9.2% | PASS |
| file_writes | `oltp_delete_insert` | 96.14ms | 121.60ms | 1.3× | 2.1% | PASS |
| file_writes | `oltp_write_only` | 61.81ms | 74.34ms | 1.2× | 2.7% | PASS |
| file_writes | `types_delete_insert` | 68.17ms | 61.88ms | 0.9× | 1.7% | PASS |
| file_writes | `oltp_read_write` | 133.79ms | 153.39ms | 1.1× | 2.8% | PASS |
| ac_reads | `oltp_point_select` | 69.79ms | 59.56ms | 0.9× | 2.1% | PASS |
| ac_reads | `oltp_range_select` | 21.70ms | 16.25ms | 0.7× | 1.6% | PASS |
| ac_reads | `oltp_sum_range` | 19.86ms | 15.79ms | 0.8× | 1.9% | PASS |
| ac_reads | `oltp_order_range` | 3.91ms | 3.52ms | 0.9× | 1.3% | PASS |
| ac_reads | `oltp_distinct_range` | 4.92ms | 4.62ms | 0.9× | 0.9% | PASS |
| ac_reads | `oltp_index_scan` | 7.66ms | 8.55ms | 1.1× | 1.0% | PASS |
| ac_reads | `select_random_points` | 27.13ms | 22.66ms | 0.8× | 3.4% | PASS |
| ac_reads | `select_random_ranges` | 6.90ms | 7.77ms | 1.1× | 0.9% | PASS |
| ac_reads | `covering_index_scan` | 7.96ms | 7.28ms | 0.9× | 0.8% | PASS |
| ac_reads | `groupby_scan` | 37.72ms | 37.13ms | 1.0× | 1.4% | PASS |
| ac_reads | `index_join` | 13.62ms | 11.04ms | 0.8× | 2.1% | PASS |
| ac_reads | `index_join_scan` | 4.58ms | 6.18ms | 1.3× | 1.8% | PASS |
| ac_reads | `types_table_scan` | 1.18s | 1.24s | 1.1× | 1.5% | PASS |
| ac_reads | `table_scan` | 1.40s | 1.41s | 1.0× | 2.9% | PASS |
| ac_reads | `oltp_read_only` | 191.81ms | 168.34ms | 0.9× | 1.2% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 17.23ms | 57.82ms | 3.4× | 4.9% | PASS |
| ac_writes | `oltp_insert_ac` | 19.76ms | 75.21ms | 3.8× | 5.1% | PASS |
| ac_writes | `oltp_update_index_ac` | 20.30ms | 85.75ms | 4.2× | 6.5% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.54ms | 66.57ms | 4.0× | 3.9% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 19.98ms | 78.01ms | 3.9× | 5.5% | PASS |
| ac_writes | `oltp_write_only_ac` | 19.25ms | 76.81ms | 4.0× | 5.4% | PASS |
| ac_writes | `types_delete_insert_ac` | 17.64ms | 70.21ms | 4.0× | 5.7% | PASS |
| ac_writes | `oltp_read_write_ac` | 27.58ms | 81.76ms | 3.0× | 2.9% | PASS |

</details>

<details>
<summary>compositepk workload details</summary>

| Section | Workload | SQLite median | DoltLite median | Ratio | Paired-ratio noise | Result |
|---|---|---:|---:|---:|---:|---|
| mem_reads | `oltp_point_select` | 29.21ms | 36.27ms | 1.2× | 1.2% | PASS |
| mem_reads | `oltp_range_select` | 16.00ms | 19.86ms | 1.2× | 1.1% | PASS |
| mem_reads | `oltp_sum_range` | 15.28ms | 19.47ms | 1.3× | 0.9% | PASS |
| mem_reads | `oltp_order_range` | 3.11ms | 3.60ms | 1.2× | 0.6% | PASS |
| mem_reads | `oltp_distinct_range` | 3.90ms | 4.71ms | 1.2× | 0.6% | PASS |
| mem_reads | `oltp_index_scan` | 3.93ms | 5.49ms | 1.4× | 1.3% | PASS |
| mem_reads | `select_random_points` | 25.61ms | 30.38ms | 1.2× | 1.0% | PASS |
| mem_reads | `select_random_ranges` | 6.51ms | 8.19ms | 1.3× | 0.7% | PASS |
| mem_reads | `covering_index_scan` | 3.64ms | 3.88ms | 1.1× | 1.9% | PASS |
| mem_reads | `groupby_scan` | 31.79ms | 38.15ms | 1.2× | 0.5% | PASS |
| mem_reads | `index_join` | 6.80ms | 9.72ms | 1.4× | 1.7% | PASS |
| mem_reads | `index_join_scan` | 3.28ms | 5.11ms | 1.6× | 0.9% | PASS |
| mem_reads | `types_table_scan` | 989.40ms | 1.15s | 1.2× | 1.0% | PASS |
| mem_reads | `table_scan` | 1.22s | 1.26s | 1.0× | 1.4% | PASS |
| mem_reads | `oltp_read_only` | 129.22ms | 157.30ms | 1.2× | 0.8% | PASS |
| mem_writes | `oltp_bulk_insert` | 216.38ms | 305.55ms | 1.4× | 1.3% | PASS |
| mem_writes | `oltp_insert` | 16.63ms | 30.35ms | 1.8× | 0.9% | PASS |
| mem_writes | `oltp_update_index` | 59.77ms | 112.99ms | 1.9× | 1.2% | PASS |
| mem_writes | `oltp_update_non_index` | 44.54ms | 71.89ms | 1.6× | 0.9% | PASS |
| mem_writes | `oltp_delete_insert` | 43.14ms | 82.59ms | 1.9× | 0.8% | PASS |
| mem_writes | `oltp_write_only` | 22.91ms | 48.80ms | 2.1× | 0.9% | PASS |
| mem_writes | `types_delete_insert` | 27.55ms | 45.25ms | 1.6× | 0.8% | PASS |
| mem_writes | `oltp_read_write` | 86.16ms | 139.41ms | 1.6× | 1.1% | PASS |
| file_reads | `oltp_point_select` | 61.82ms | 46.54ms | 0.8× | 1.7% | PASS |
| file_reads | `oltp_range_select` | 19.58ms | 21.03ms | 1.1× | 1.4% | PASS |
| file_reads | `oltp_sum_range` | 18.85ms | 20.86ms | 1.1× | 1.1% | PASS |
| file_reads | `oltp_order_range` | 3.52ms | 3.80ms | 1.1× | 2.2% | PASS |
| file_reads | `oltp_distinct_range` | 4.32ms | 4.92ms | 1.1× | 1.4% | PASS |
| file_reads | `oltp_index_scan` | 7.33ms | 6.82ms | 0.9× | 1.4% | PASS |
| file_reads | `select_random_points` | 29.48ms | 32.06ms | 1.1× | 1.2% | PASS |
| file_reads | `select_random_ranges` | 10.12ms | 9.53ms | 0.9× | 1.1% | PASS |
| file_reads | `covering_index_scan` | 6.88ms | 5.08ms | 0.7× | 1.8% | PASS |
| file_reads | `groupby_scan` | 32.24ms | 38.28ms | 1.2× | 0.6% | PASS |
| file_reads | `index_join` | 8.79ms | 10.92ms | 1.2× | 1.2% | PASS |
| file_reads | `index_join_scan` | 3.75ms | 5.34ms | 1.4× | 1.5% | PASS |
| file_reads | `types_table_scan` | 1.00s | 1.15s | 1.1× | 1.4% | PASS |
| file_reads | `table_scan` | 1.24s | 1.26s | 1.0× | 1.3% | PASS |
| file_reads | `oltp_read_only` | 179.74ms | 174.90ms | 1.0× | 0.7% | PASS |
| file_writes | `oltp_bulk_insert` | 229.10ms | 318.05ms | 1.4× | 1.2% | PASS |
| file_writes | `oltp_insert` | 21.20ms | 35.97ms | 1.7× | 1.2% | PASS |
| file_writes | `oltp_update_index` | 76.42ms | 124.18ms | 1.6× | 1.1% | PASS |
| file_writes | `oltp_update_non_index` | 59.31ms | 82.90ms | 1.4× | 1.5% | PASS |
| file_writes | `oltp_delete_insert` | 58.42ms | 94.21ms | 1.6× | 1.1% | PASS |
| file_writes | `oltp_write_only` | 36.08ms | 58.34ms | 1.6× | 1.9% | PASS |
| file_writes | `types_delete_insert` | 37.73ms | 52.13ms | 1.4× | 1.6% | PASS |
| file_writes | `oltp_read_write` | 102.14ms | 150.70ms | 1.5× | 1.2% | PASS |
| ac_reads | `oltp_point_select` | 39.74ms | 47.16ms | 1.2× | 0.9% | PASS |
| ac_reads | `oltp_range_select` | 17.33ms | 21.20ms | 1.2× | 0.9% | PASS |
| ac_reads | `oltp_sum_range` | 16.59ms | 21.01ms | 1.3× | 0.9% | PASS |
| ac_reads | `oltp_order_range` | 3.29ms | 3.83ms | 1.2× | 1.5% | PASS |
| ac_reads | `oltp_distinct_range` | 4.07ms | 4.97ms | 1.2× | 1.3% | PASS |
| ac_reads | `oltp_index_scan` | 5.22ms | 6.88ms | 1.3× | 1.3% | PASS |
| ac_reads | `select_random_points` | 26.88ms | 32.13ms | 1.2× | 1.2% | PASS |
| ac_reads | `select_random_ranges` | 7.74ms | 9.56ms | 1.2× | 0.8% | PASS |
| ac_reads | `covering_index_scan` | 4.82ms | 5.13ms | 1.1× | 1.4% | PASS |
| ac_reads | `groupby_scan` | 32.06ms | 38.53ms | 1.2× | 0.5% | PASS |
| ac_reads | `index_join` | 7.65ms | 10.88ms | 1.4× | 1.3% | PASS |
| ac_reads | `index_join_scan` | 3.52ms | 5.30ms | 1.5× | 1.1% | PASS |
| ac_reads | `types_table_scan` | 998.42ms | 1.15s | 1.1× | 1.2% | PASS |
| ac_reads | `table_scan` | 1.24s | 1.26s | 1.0× | 1.0% | PASS |
| ac_reads | `oltp_read_only` | 145.11ms | 175.80ms | 1.2× | 0.7% | PASS |
| ac_writes | `oltp_bulk_insert_ac` | 15.45ms | 56.36ms | 3.6× | 5.0% | PASS |
| ac_writes | `oltp_insert_ac` | 17.27ms | 70.56ms | 4.1× | 5.9% | PASS |
| ac_writes | `oltp_update_index_ac` | 18.80ms | 81.18ms | 4.3× | 5.1% | PASS |
| ac_writes | `oltp_update_non_index_ac` | 16.11ms | 64.18ms | 4.0× | 4.1% | PASS |
| ac_writes | `oltp_delete_insert_ac` | 16.95ms | 72.73ms | 4.3× | 5.6% | PASS |
| ac_writes | `oltp_write_only_ac` | 17.43ms | 72.22ms | 4.1× | 5.7% | PASS |
| ac_writes | `types_delete_insert_ac` | 15.32ms | 64.83ms | 4.2× | 7.6% | PASS |
| ac_writes | `oltp_read_write_ac` | 21.86ms | 79.18ms | 3.6× | 4.7% | PASS |

</details>

</details>

## Version-control latency

Wall time: 1m 42s. Samples per benchmark: 101.

| Benchmark | Median | Ceiling | Ceiling used | MAD | Result |
|---|---:|---:|---:|---:|---|
| `status_clean_many_tables` | 63.03ms | 130.00ms | 48.5% | 1.6% | PASS |
| `status_dirty_many_tables` | 64.85ms | 130.00ms | 49.9% | 2.2% | PASS |
| `diff_regular_working_one_table` | 58.44ms | 120.00ms | 48.7% | 1.8% | PASS |
| `diff_regular_working_many_tables` | 67.06ms | 140.00ms | 47.9% | 1.7% | PASS |
| `diff_stat_working_many_tables` | 67.63ms | 140.00ms | 48.3% | 2.6% | PASS |
| `diff_schema_working_many_tables` | 67.81ms | 140.00ms | 48.4% | 2.7% | PASS |
| `branch_list_many_branches` | 18.12ms | 35.00ms | 51.8% | 3.4% | PASS |
| `branch_create_delete` | 20.45ms | 40.00ms | 51.1% | 4.0% | PASS |
| `checkout_branch_clean` | 89.51ms | 150.00ms | 59.7% | 3.1% | PASS |
| `merge_data_no_conflicts` | 30.45ms | 50.00ms | 60.9% | 18.0% | PASS |
| `merge_schema_no_conflicts` | 16.37ms | 35.00ms | 46.8% | 2.8% | PASS |
| `merge_data_conflicts` | 23.70ms | 180.00ms | 13.2% | 2.3% | PASS |
| `merge_data_conflicts_with_resolve` | 23.85ms | 180.00ms | 13.2% | 3.3% | PASS |

Version-control ceiling result: **PASS**.

## Reproducing

The workload definitions live in `test/sysbench_compare*.sh` and `test/vc_perf_ceiling.sh`. The nightly workflow retains the complete raw samples and generated reports as Actions artifacts for 30 days.
