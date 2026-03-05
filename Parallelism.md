### The Main Theme: Parallelism

- The core objective of this course is to understand Parallelism, which is the ability to execute different parts of a computation at the same time to solve large problems faster.

- Parallelism vs. Concurrency: These are not the same. Concurrency is about managing multiple tasks that can run in any order (often by switching between them on a single processor), whereas Parallelism requires multiple processing resources to actually execute tasks at the same physical time.

- Thread-Level Parallelism (TLP): The main focus of this class is on TLP, where a "thread" is a sequence of instructions managed by the operating system or a runtime system.

### Decomposition Techniques

- Decomposition is the process of dividing a large computation into smaller tasks that can run concurrently.
- Recursive Decomposition: Used for "divide-and-conquer" problems like Quicksort, where a problem is repeatedly split into sub-problems until they reach a manageable size.
- Data Decomposition: Tasks are divided based on the data they process, such as partitioning the input data, the output data, or intermediate stages of a calculation.
- Exploratory Decomposition: Used for searching solution spaces, such as solving a 15-puzzle or game playing, where the search space is partitioned and explored concurrently.
- Speculative Decomposition: Tasks are scheduled even if it is not yet certain they are needed (e.g., branches in a switch statement)