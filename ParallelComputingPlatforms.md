## Summary of Parallel Computing Platforms

### 1. Platform Comparison
* **SIMD:** Efficient for regular structures (media, FFT), lower hardware overhead, but less flexible.
* **MIMD:** Versatile and uses off-the-shelf components, but requires more memory for separate OS/programs.
* **SIMT:** Used in modern GPUs, featuring independent thread scheduling (e.g., NVIDIA Volta) to prevent deadlocks.

### 2. Memory System Performance
* **The Rate Limiter:** Memory latency often bottlenecks peak processor performance. For a 1GHz processor, 100ns DRAM latency can reduce performance from 1 GFLOPS to 5 MFLOPS.
* **Locality:** Crucial for performance.
    * **Spatial Reuse:** Utilizing multiple words in a single fetched cache line.
    * **Temporal Reuse:** Re-accessing the same data multiple times while it's in cache.

### 3. Latency Hiding Techniques
* **Multithreading:** Switching between threads every cycle to keep the processor busy while waiting for memory requests.
* **Prefetching:** Loading data into the cache before it is explicitly requested by the program.
* **Trade-offs:** Both techniques address latency but significantly increase bandwidth requirements and data footprints, potentially leading to bandwidth-bound performance.