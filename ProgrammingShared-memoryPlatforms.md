# With OpenMP

OpenMP Basics: A directive-based API for shared-memory parallel programming that utilizes compiler pragmas, runtime libraries, and environment variables.

Execution Model: Follows the Fork-Join model, where a master thread forks into a team of worker threads for parallel regions and joins back to resume sequential execution.

- Directives & Clauses:
    - parallel: Creates a thread team.
    - for, sections: Worksharing constructs to distribute tasks among threads.
    - shared, private, firstprivate: Data scoping clauses to manage memory access between threads.

* Synchronization:

    - barrier: Synchronizes all threads.
    - critical: Ensures mutual exclusion for a specific block.
    - reduction: Combines private variables into a single global result.

* Scheduling: Methods like static, dynamic, and guided determine how loop iterations are mapped to threads at compile or runtime.

* Tasks: Introduced in OpenMP 3.0 to handle irregular parallelism, such as recursive functions (e.g., Fibonacci) or unbounded loops.

* Optimization: Improving performance by minimizing thread creation overhead (avoiding redundant fork-joins), parallelizing at higher loop levels, and reducing synchronization points.