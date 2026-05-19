# Message Passing and MPI 

## 1. Fundamental Principles of Message Passing
* **Logical Architecture**: A message-passing platform consists of $p$ processes, each possessing its own exclusive address space.
* **Data Explicitly Managed**: All data must be explicitly partitioned and placed; there is no implicit sharing of memory.
* **Two-Sided Interaction**: Interactions are inherently two-sided; the process holding the data must "send" it, and the process requiring it must "receive" it.
* **SPMD Model**: Most parallel programs utilize the Single Program Multiple Data (SPMD) model, where the same code executes on different processing elements, with behavior branching based on the process ID (rank).
* **Evaluation**: Its strengths include a simple performance model with explicit costs and high portability, though it can be difficult to program for unstructured interactions.


## 2. Communication Protocols and Synchrony
### Blocking, Unbuffered Communication
* **Definition**: A "send" operation does not return until the data has been transferred to the receiver, and a "receive" does not return until the data arrives.
* **Handshaking**: This protocol requires a request-to-send/okay-to-send handshake between processes.
* **Idling & Deadlock**: Processes idle if they do not arrive at the communication point simultaneously, and deadlocks are highly likely if multiple processes try to send at once.

### Blocking, Buffered Communication
* **Definition**: A "send" returns once the data is safely written into a buffer (sender-side or receiver-side), rather than waiting for the partner process to reach the call.
* **Performance Impact**: Larger buffers allow the system to tolerate asynchrony better, but infinite buffering is impossible.
* **Deadlock Risk**: While it avoids deadlocks caused by blocking sends, deadlock is still possible if receive operations are blocked waiting for messages that never come.

### Non-Blocking Communication
* **Definition**: Send and receive operations return immediately after being posted, often before it is safe to access the buffer.
* **Computational Overlap**: The primary advantage is the ability to **overlap communication with useful computation**, maximizing CPU efficiency.
* **Programmer Responsibility**: The programmer must explicitly check for completion using status check operations (e.g., `MPI_Wait` or `MPI_Test`) before modifying the sender buffer or reading the receiver buffer.



## 3. The MPI (Message Passing Interface) Standard
* **Overview**: MPI is a portable, ubiquitous, and high-performance standard library with C and Fortran APIs.
* **Minimal Routine Set**: A fully functional program can be written using only six basic routines: `MPI_Init`, `MPI_Finalize`, `MPI_Comm_size`, `MPI_Comm_rank`, `MPI_Send`, and `MPI_Recv`.
* **Communicators**: Processes are grouped into communication domains called "Communicators" (e.g., `MPI_COMM_WORLD`), which include all processes by default.

## 4. Environment and Basic API
* **Lifecycle Management**:
    * `MPI_Init`: Initializes the environment; must be called before any other MPI routine.
    * `MPI_Finalize`: Performs cleanup; must be called at the end of the program.
* **Inquiry Functions**:
    * `MPI_Comm_size`: Returns the total number of processes in the group.
    * `MPI_Comm_rank`: Returns the index (rank) of the calling process, ranging from $0$ to $size-1$.
* **Point-to-Point Primitives**:
    * `MPI_Send(buf, count, datatype, dest, tag, comm)`: Sends data to a destination.
    * `MPI_Recv(buf, count, datatype, source, tag, comm, status)`: Receives data, potentially using wildcards like `MPI_ANY_SOURCE` or `MPI_ANY_TAG`.

## 5. Primitive Data Types in MPI
| MPI Datatype | C Datatype |
| :--- | :--- |
| **MPI_CHAR** | signed char |
| **MPI_INT** | signed int |
| **MPI_LONG** | signed long int |
| **MPI_FLOAT** | float |
| **MPI_DOUBLE** | double |
| **MPI_BYTE** | 8 bits (raw data) |

# Advanced Message Passing and MPI Techniques

## 1. Deadlock Management and Advanced Primitives
* **Deadlock Pitfalls**: Standard blocking sends may cause deadlocks if both processes initiate a send before a receive in unbuffered modes.
* **Circular Wait Solutions**: Deadlocks in ring-based topologies can be avoided by breaking the circular wait, such as having odd-ranked processes send first while even-ranked processes receive first.
* **Safe Exchange**: `MPI_Sendrecv` combines sending and receiving into a single atomic call, which is guaranteed by MPI to be deadlock-free.
* **Non-blocking Operations**: `MPI_Isend` and `MPI_Irecv` return immediately, allowing the system to overlap communication with useful computation for better performance.

## 2. Collective Communication Operations
* **Definition**: These operations are defined over all processes within a communicator, requiring every process to call the same routine.
* **Data Movement**:
    * **Bcast**: Broadcasts a message from one root process to all others in the group .
    * **Scatter/Gather**: `MPI_Scatter` distributes distinct chunks of data from a root to all processes, while `MPI_Gather` collects those chunks back to the root .
    * **Allgather**: Every process receives a copy of the gathered results, equivalent to a Gather followed by a Bcast.
    * **All-to-All**: Performs personalized communication where each process sends distinct data to every other process, analogous to a matrix transpose .
* **Data Reduction**:
    * **Reduce**: Combines data from all processes using a specified operation (e.g., `MPI_SUM`, `MPI_MAX`, `MPI_MIN`) and stores the result at a target process .
    * **Allreduce**: Distributes the reduction result to every process in the communicator.
    * **Scan**: Performs a prefix scan (inclusive or exclusive) where process $i$ receives the reduction of processes $0$ through $i$.

## 3. Advanced Group and Topology Management
* **Communicator Splitting**: `MPI_Comm_split` partitions an existing communicator into non-overlapping subgroups based on a "color" argument.
* **Virtual Topologies**: 
    * **Cartesian Mesh**: `MPI_Cart_create` maps processes into a multi-dimensional mesh, facilitating regular grid-based computations.
    * **Shift Operations**: `MPI_Cart_shift` determines the source and destination ranks for neighbor communication in the mesh, simplifying code for boundary exchanges.
    * **Sub-topologies**: `MPI_Cart_sub` can partition a high-dimensional mesh into lower-dimensional sub-grids (e.g., extracting rows or columns from a 2D grid).

## 4. One-Sided Communication (Remote Memory Access)
* **Concept**: Decouples data transfer from synchronization; an "origin" process can read or write to a "target" process's memory without explicit pairing.
* **RMA Operations**:
    * **Windows**: `MPI_Win_create` exposes a specific memory region for remote access.
    * **Data Movement**: `MPI_Put` (write), `MPI_Get` (read), and `MPI_Accumulate` (remote update) are the primary non-blocking RMA calls.
* **Synchronization**:
    * **Active Target**: Uses collective `MPI_Win_fence` where all processes participate in completing outstanding operations.
    * **Passive Target**: Uses `MPI_Win_lock` and `MPI_Win_unlock` to allow an origin process to access a target without the target calling any MPI routines.

## 5. MPI Derived Data Types
* **Purpose**: Used to exchange complex data structures or non-contiguous memory layouts that standard primitive types cannot handle .
* **Construction**: `MPI_Type_create_struct` creates a new MPI data type by specifying the count, block lengths, and memory displacements of the members .
* **Usage**: Once created, a type must be committed via `MPI_Type_commit` before it can be used in communication routines like `MPI_Send` or `MPI_Recv`.

# Advanced MPI: Group Management, Topologies, and RMA

## 1. Communicator and Group Management
* **Splitting**: `MPI_Comm_split` partitions a communicator into non-overlapping subgroups based on a "color" key provided by each process.
* **Hierarchical Computation**: This is essential for multi-dimensional or linear algebra operations where tasks are divided into sub-grids .

## 2. Virtual Topologies
* **Cartesian Mesh**: `MPI_Cart_create` organizes processes into a logical multi-dimensional grid, enhancing code readability for mesh-based problems.
* **Shift Operations**: `MPI_Cart_shift` computes the source and destination ranks for shifts in the grid, effectively solving the "circular wait" or boundary exchange logic.
* **Sub-topologies**: `MPI_Cart_sub` allows partitioning a high-dimensional mesh into lower-dimensional units (e.g., extracting rows from a 2D grid).

## 3. One-Sided Communication (RMA)
* **Concept**: Decouples data transfer from synchronization; an origin process can read or modify data on a target process without the target issuing an explicit call.
* **Primitives**:
    * **MPI_Put**: Moves data from local memory to remote memory.
    * **MPI_Get**: Retrieves data from remote memory to local memory.
    * **MPI_Accumulate**: Atomically updates remote memory using local values and an operator.
* **Synchronization Styles**:
    * **Active Target**: Target process participates using collective calls like `MPI_Win_fence`.
    * **Passive Target**: Origin process uses `MPI_Win_lock/unlock` to access the target without target involvement.

## 4. MPI Derived Data Types
* **Structured Data**: Used to exchange complex structures (like C-structs) or non-contiguous memory blocks that standard types cannot handle .
* **Registration**: Requires defining the memory layout via `MPI_Type_create_struct` and committing it with `MPI_Type_commit` before use in communication calls .

## 5. External Libraries and Resources
* **MPI-based Libraries**: Specialized libraries include ScaLAPACK (linear algebra), PETSc (scientific computation), and Trilinos (multi-physics engineering).
* **Official Docs**: Standard documents and tutorials are available via the MPI Forum and Argonne National Laboratory.