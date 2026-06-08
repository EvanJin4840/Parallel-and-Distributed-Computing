## 1. Introduction & Foundations

* **Group Communication Goal:** Accelerate interaction patterns within a group of processes.
* **Approach:** Collective communication operations built from pairwise point-to-point communication.
* **Benefits:** Reduces development cost / effort and optimizes execution performance by leveraging the target hardware architecture.

### Core Architectural Assumptions
* **Constant Time:** Communication time between nodes is independent of physical distance.
* **Bidirectional:** Two nodes can send and receive messages to each other simultaneously.
* **Single-ported:** A node can send only one message and receive only one message per step.

---

## 2. One-to-All Broadcast & All-to-One Reduction

* **One-to-All Broadcast:** A single root process distributes $M$ units of data to all other processes.
* **All-to-One Reduction:** Each process contributes $M$ units of data; data is combined using an associative operator (e.g., sum, min, max) and stored at a single target process.

### Algorithmic Approaches & Costs (Hypercube Example)
* **Naïve Ring:** Requires $p - 1$ stages as the source sequentially sends data to each node.
* **Recursive Doubling:** Halves the target distance at each step, spreading data exponentially.
    * **Time Complexity (Hypercube):** $$T = (t_s + t_w \cdot m) \log p$$
      *(where $t_s$ = startup time, $t_w$ = single-word transfer time, $m$ = message size, $p$ = number of nodes)*
* **Mesh Networks (2D Mesh):** Executed in two phases: first row-wise, then column-wise concurrently.

---

## 3. All-to-All Broadcast & Reduction

* **All-to-All Broadcast:** Every process acts as both a source and destination, broadcasting its own $m$-word message to all others.
* **All-to-All Reduction:** Every process participates in a reduction, and every process receives a copy of the final combined result.

### Cost Analysis Comparison
* **Ring Network:** Requires $p - 1$ steps where nodes continuously merge incoming data and forward it to the next neighbor.
  $$T = (t_s + t_w \cdot m) (p - 1)$$
* **Mesh Network:** Row-wise broadcast (Phase 1) followed by column-wise broadcast of the merged results (Phase 2).
  $$T = 2 t_s (\sqrt{p} - 1) + t_w \cdot m (p - 1)$$
