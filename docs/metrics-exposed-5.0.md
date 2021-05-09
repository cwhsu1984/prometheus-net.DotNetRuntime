﻿# `.net 5.0` metrics

Each subheading details the metrics produced by calling builder methods with the specified `CaptureLevel`.

## Default metrics

Metrics that are included by default, regardless of what stats collectors are enabled.

| Name                            | Type      | Description                                                                                                                 | Labels                                                                                            |
| ------------------------------- | --------- | --------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| `dotnet_build_info`             | `Gauge`   | Build information about prometheus\-net.DotNetRuntime and the environment                                                   | `version`, `target_framework`, `runtime_version`, `os_version`, `process_architecture`, `gc_mode` |
| `dotnet_internal_recycle_count` | `Counter` | prometheus\-net.DotNetRuntime internal metric. Counts the number of times the underlying event listeners have been recycled |                                                                                                   |
| `process_cpu_count`             | `Gauge`   | The number of processor cores available to this process.                                                                    |                                                                                                   |

## `.WithJitStats()`

Include metrics summarizing the volume of methods being compiled  
            by the Just\-In\-Time compiler.

### `CaptureLevel.Counters`

| Name                      | Type      | Description                                          | Labels |
| ------------------------- | --------- | ---------------------------------------------------- | ------ |
| `dotnet_jit_il_bytes`     | `Gauge`   | Total bytes of IL compiled by the JIT compiler       |        |
| `dotnet_jit_method_total` | `Counter` | Total number of methods compiled by the JIT compiler |        |

### `CaptureLevel.Verbose`

Includes metrics generated by `CaptureLevel.Counters` plus:

| Name                              | Type      | Description                                                                                       | Labels    |
| --------------------------------- | --------- | ------------------------------------------------------------------------------------------------- | --------- |
| `dotnet_jit_cpu_ratio`            | `Gauge`   | The amount of total CPU time consumed spent JIT'ing                                               |           |
| `dotnet_jit_method_total`         | `Counter` | Total number of methods compiled by the JIT compiler, broken down by compilation for dynamic code | `dynamic` |
| `dotnet_jit_method_seconds_total` | `Counter` | Total number of seconds spent in the JIT compiler, broken down by compilation for dynamic code    | `dynamic` |

## `.WithGcStats()`

Include metrics recording the frequency and duration of garbage collections\/ pauses, heap sizes and  
            volume of allocations.

### `CaptureLevel.Counters`

| Name                                     | Type      | Description                                                                                    | Labels          |
| ---------------------------------------- | --------- | ---------------------------------------------------------------------------------------------- | --------------- |
| `dotnet_gc_allocated_bytes_total`        | `Counter` | The total number of bytes allocated on the managed heap                                        |                 |
| `dotnet_gc_collection_count_total`       | `Counter` | Counts the number of garbage collections that have occurred, broken down by generation number. | `gc_generation` |
| `dotnet_gc_memory_total_available_bytes` | `Gauge`   | The upper limit on the amount of physical memory .NET can allocate to                          |                 |
| `dotnet_gc_pause_ratio`                  | `Gauge`   | The percentage of time the process spent paused for garbage collection                         |                 |
| `dotnet_gc_heap_size_bytes`              | `Gauge`   | The current size of all heaps (only updated after a garbage collection)                        | `gc_generation` |

### `CaptureLevel.Informational`

Includes metrics generated by `CaptureLevel.Counters` plus:

| Name                                  | Type        | Description                                                                                                                      | Labels                       |
| ------------------------------------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------- | ---------------------------- |
| `dotnet_gc_pause_seconds`             | `Histogram` | The amount of time execution was paused for garbage collection                                                                   |                              |
| `dotnet_gc_cpu_ratio`                 | `Gauge`     | The percentage of process CPU time spent running garbage collections                                                             |                              |
| `dotnet_gc_collection_count_total`    | `Counter`   | Counts the number of garbage collections that have occurred, broken down by generation number and the reason for the collection. | `gc_generation`, `gc_reason` |
| `dotnet_gc_collection_seconds`        | `Histogram` | The amount of time spent running garbage collections                                                                             | `gc_generation`, `gc_type`   |
| `dotnet_gc_finalization_queue_length` | `Gauge`     | The number of objects waiting to be finalized                                                                                    |                              |
| `dotnet_gc_pinned_objects`            | `Gauge`     | The number of pinned objects                                                                                                     |                              |

### `CaptureLevel.Verbose`

Includes metrics generated by `CaptureLevel.Counters`, `CaptureLevel.Informational` plus:

| Name                              | Type      | Description                                             | Labels    |
| --------------------------------- | --------- | ------------------------------------------------------- | --------- |
| `dotnet_gc_allocated_bytes_total` | `Counter` | The total number of bytes allocated on the managed heap | `gc_heap` |

## `.WithExceptionStats()`

Include metrics that measure the number of exceptions thrown.

### `CaptureLevel.Counters`

| Name                      | Type      | Description                | Labels |
| ------------------------- | --------- | -------------------------- | ------ |
| `dotnet_exceptions_total` | `Counter` | Count of exceptions thrown |        |

### `CaptureLevel.Errors`

Includes metrics generated by `CaptureLevel.Counters` plus:

| Name                      | Type      | Description                                     | Labels |
| ------------------------- | --------- | ----------------------------------------------- | ------ |
| `dotnet_exceptions_total` | `Counter` | Count of exceptions thrown, broken down by type | `type` |

## `.WithContentionStats()`

Include metrics around volume of locks contended.

### `CaptureLevel.Counters`

| Name                      | Type      | Description                   | Labels |
| ------------------------- | --------- | ----------------------------- | ------ |
| `dotnet_contention_total` | `Counter` | The number of locks contended |        |

### `CaptureLevel.Informational`

Includes metrics generated by `CaptureLevel.Counters` plus:

| Name                              | Type      | Description                                     | Labels |
| --------------------------------- | --------- | ----------------------------------------------- | ------ |
| `dotnet_contention_seconds_total` | `Counter` | The total amount of time spent contending locks |        |

## `.WithThreadPoolStats()`

Include metrics around the size of the worker and IO thread pools and reasons  
            for worker thread pool changes.

### `CaptureLevel.Counters`

| Name                                 | Type        | Description                                                                                                                   | Labels |
| ------------------------------------ | ----------- | ----------------------------------------------------------------------------------------------------------------------------- | ------ |
| `dotnet_threadpool_queue_length`     | `Histogram` | Measures the queue length of the thread pool. Values greater than 0 indicate a backlog of work for the threadpool to process. |        |
| `dotnet_threadpool_timer_count`      | `Gauge`     | The number of timers active                                                                                                   |        |
| `dotnet_threadpool_throughput_total` | `Counter`   | The total number of work items that have finished execution in the thread pool                                                |        |
| `dotnet_threadpool_num_threads`      | `Gauge`     | The number of active threads in the thread pool                                                                               |        |

### `CaptureLevel.Informational`

Includes metrics generated by `CaptureLevel.Counters` plus:

| Name                                  | Type      | Description                                                                                       | Labels              |
| ------------------------------------- | --------- | ------------------------------------------------------------------------------------------------- | ------------------- |
| `dotnet_threadpool_io_num_threads`    | `Gauge`   | The number of active threads in the IO thread pool                                                |                     |
| `dotnet_threadpool_adjustments_total` | `Counter` | The total number of changes made to the size of the thread pool, labeled by the reason for change | `adjustment_reason` |