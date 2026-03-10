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

