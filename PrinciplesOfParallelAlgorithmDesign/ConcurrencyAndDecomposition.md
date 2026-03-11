### Parallel Algorithm Overview

- Definition: A recipe to solve a problem using multiple processors.
Typical Design Steps:
- Identify concurrent pieces of work.
- Partition and map work onto processors.
- Distribute input, output, and intermediate data.
- Coordinate access to shared data to avoid conflicts.
- Use synchronization to ensure the proper order of work.

### Task Decomposition & Dependency

- Decomposition: The process of dividing work into smaller tasks that can execute at the same time.

- Task Dependency DAG (Directed Acyclic Graph):
    - Nodes: Represent individual tasks.
    - Edges: Represent control dependencies (which task must finish before another starts)

### Key Performance Metrics

- Critical Path: The longest weighted path of dependent tasks in the DAG. This determines the shortest possible execution time.

- Degree of Concurrency: The number of tasks that can be executed simultaneously at any given time.

Granularity:

* Fine-grained: Many small tasks; high concurrency but high overhead.
* Coarse-grained: Fewer, larger tasks; lower overhead but potentially less concurrency.

### Decomposition Techniques

##### A. Recursive Decomposition
- Used for Divide-and-Conquer problems.
- A problem is broken into sub-problems, which are solved concurrently.
- Examples: Quicksort, finding the minimum of a list.

##### B. Data Decomposition
* Tasks are partitioned based on the data they handle.
* Types of Data Partitioning:
    - Output Data: Each task computes a specific portion of the result (e.g., a specific row in matrix multiplication).
    - Input Data: Each task processes a specific portion of the input (e.g., counting frequencies in a partitioned string).
    - Intermediate Data: Partitioning data created during middle stages of a calculation.