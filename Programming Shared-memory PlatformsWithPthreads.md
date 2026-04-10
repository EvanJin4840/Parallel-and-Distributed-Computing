# Summary of Pthreads Programming

### 1. Introduction to Pthreads
* **Definition**: A standard C language API (POSIX Threads) for parallel programming on shared-memory platforms[cite: 1, 31].
* **Significance**: Serves as the foundation for higher-level threading libraries (e.g., TBB, Boost) and runtime systems (e.g., OpenMP)[cite: 38, 40, 41].
* **Efficiency**: Thread creation and management are significantly faster and more efficient than process creation via `fork()`[cite: 45, 46, 47].

### 2. Thread Lifecycle Management
* **Creation**: `pthread_create` asynchronously starts a new thread with specified attributes like stack size and scheduling policy[cite: 51, 58, 60, 65].
* **Synchronization/Termination**: `pthread_join` suspends the calling thread until the target thread terminates.
* **Termination Methods**: Threads can exit via return, `pthread_exit()`, `pthread_cancel()`, or process termination[cite: 83, 84, 85, 87, 89].

### 3. Synchronization Primitives
* **Mutex Locks**: Enforce mutual exclusion to prevent data races by allowing only one thread to access a critical section at a time.
* **Condition Variables**: Allow threads to block until a specific predicate becomes true. It is critical to re-evaluate the predicate in a `while` loop due to potential **spurious wakeups**[cite: 384, 400, 510, 511].
* **Reader-Writer Locks**: Optimize performance for scenarios with frequent reads and infrequent writes by allowing concurrent read access[cite: 513, 514, 518].

### 4. Advanced Concepts
* **Thread-Specific Data (TSD)**: Enables threads to maintain individual states using specific keys (`pthread_key_create`), avoiding shared variable indexing conflicts[cite: 526, 527, 534].
* **False Sharing**: Mentioned in the context of performance; can be avoided by using local accumulators within thread functions[cite: 158].