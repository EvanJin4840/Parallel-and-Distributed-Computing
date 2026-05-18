# Message Passing and MPI 

## 1. Fundamental Principles of Message Passing
* **Logical Architecture**: A message-passing platform consists of $p$ processes, each possessing its own exclusive address space[cite: 23, 24].
* **Data Explicitly Managed**: All data must be explicitly partitioned and placed; there is no implicit sharing of memory[cite: 25].
* **Two-Sided Interaction**: Interactions are inherently two-sided; the process holding the data must "send" it, and the process requiring it must "receive" it[cite: 25].
* **SPMD Model**: Most parallel programs utilize the Single Program Multiple Data (SPMD) model, where the same code executes on different processing elements, with behavior branching based on the process ID (rank)[cite: 26].
* **Evaluation**: Its strengths include a simple performance model with explicit costs and high portability, though it can be difficult to program for unstructured interactions [cite: 28-35].



## 2. Communication Protocols and Synchrony
### Blocking, Unbuffered Communication
* **Definition**: A "send" operation does not return until the data has been transferred to the receiver, and a "receive" does not return until the data arrives[cite: 39].
* **Handshaking**: This protocol requires a request-to-send/okay-to-send handshake between processes[cite: 74].
* **Idling & Deadlock**: Processes idle if they do not arrive at the communication point simultaneously, and deadlocks are highly likely if multiple processes try to send at once[cite: 44, 45, 75].

### Blocking, Buffered Communication
* **Definition**: A "send" returns once the data is safely written into a buffer (sender-side or receiver-side), rather than waiting for the partner process to reach the call[cite: 79, 80].
* **Performance Impact**: Larger buffers allow the system to tolerate asynchrony better, but infinite buffering is impossible[cite: 114].
* **Deadlock Risk**: While it avoids deadlocks caused by blocking sends, deadlock is still possible if receive operations are blocked waiting for messages that never come[cite: 84, 87].

### Non-Blocking Communication
* **Definition**: Send and receive operations return immediately after being posted, often before it is safe to access the buffer[cite: 118].
* **Computational Overlap**: The primary advantage is the ability to **overlap communication with useful computation**, maximizing CPU efficiency[cite: 124].
* **Programmer Responsibility**: The programmer must explicitly check for completion using status check operations (e.g., `MPI_Wait` or `MPI_Test`) before modifying the sender buffer or reading the receiver buffer[cite: 121, 122].



## 3. The MPI (Message Passing Interface) Standard
* **Overview**: MPI is a portable, ubiquitous, and high-performance standard library with C and Fortran APIs [cite: 173-179].
* **Minimal Routine Set**: A fully functional program can be written using only six basic routines: `MPI_Init`, `MPI_Finalize`, `MPI_Comm_size`, `MPI_Comm_rank`, `MPI_Send`, and `MPI_Recv` [cite: 186, 586-595].
* **Communicators**: Processes are grouped into communication domains called "Communicators" (e.g., `MPI_COMM_WORLD`), which include all processes by default [cite: 612-618].

## 4. Environment and Basic API
* **Lifecycle Management**:
    * `MPI_Init`: Initializes the environment; must be called before any other MPI routine[cite: 600].
    * `MPI_Finalize`: Performs cleanup; must be called at the end of the program [cite: 603-606].
* **Inquiry Functions**:
    * `MPI_Comm_size`: Returns the total number of processes in the group[cite: 621].
    * `MPI_Comm_rank`: Returns the index (rank) of the calling process, ranging from $0$ to $size-1$[cite: 622, 623].
* **Point-to-Point Primitives**:
    * `MPI_Send(buf, count, datatype, dest, tag, comm)`: Sends data to a destination[cite: 651].
    * `MPI_Recv(buf, count, datatype, source, tag, comm, status)`: Receives data, potentially using wildcards like `MPI_ANY_SOURCE` or `MPI_ANY_TAG` [cite: 652-659].

## 5. Primitive Data Types in MPI
| MPI Datatype | C Datatype |
| :--- | :--- |
| **MPI_CHAR** | signed char [cite: 664] |
| **MPI_INT** | signed int [cite: 664] |
| **MPI_LONG** | signed long int [cite: 664] |
| **MPI_FLOAT** | float [cite: 664] |
| **MPI_DOUBLE** | double [cite: 664] |
| **MPI_BYTE** | 8 bits (raw data) [cite: 664] |

# Advanced Message Passing and MPI Techniques

## 1. Deadlock Management and Advanced Primitives
* **Deadlock Pitfalls**: Standard blocking sends may cause deadlocks if both processes initiate a send before a receive in unbuffered modes [cite: 666, 691-692].
* **Circular Wait Solutions**: Deadlocks in ring-based topologies can be avoided by breaking the circular wait, such as having odd-ranked processes send first while even-ranked processes receive first [cite: 708-725].
* **Safe Exchange**: `MPI_Sendrecv` combines sending and receiving into a single atomic call, which is guaranteed by MPI to be deadlock-free [cite: 759-766].
* **Non-blocking Operations**: `MPI_Isend` and `MPI_Irecv` return immediately, allowing the system to overlap communication with useful computation for better performance [cite: 730-732, 738].

## 2. Collective Communication Operations
* **Definition**: These operations are defined over all processes within a communicator, requiring every process to call the same routine [cite: 770-773].
* **Data Movement**:
    * **Bcast**: Broadcasts a message from one root process to all others in the group .
    * **Scatter/Gather**: `MPI_Scatter` distributes distinct chunks of data from a root to all processes, while `MPI_Gather` collects those chunks back to the root .
    * **Allgather**: Every process receives a copy of the gathered results, equivalent to a Gather followed by a Bcast [cite: 958-959].
    * **All-to-All**: Performs personalized communication where each process sends distinct data to every other process, analogous to a matrix transpose .
* **Data Reduction**:
    * **Reduce**: Combines data from all processes using a specified operation (e.g., `MPI_SUM`, `MPI_MAX`, `MPI_MIN`) and stores the result at a target process .
    * **Allreduce**: Distributes the reduction result to every process in the communicator [cite: 854-855].
    * **Scan**: Performs a prefix scan (inclusive or exclusive) where process $i$ receives the reduction of processes $0$ through $i$ [cite: 873-881].

## 3. Advanced Group and Topology Management
* **Communicator Splitting**: `MPI_Comm_split` partitions an existing communicator into non-overlapping subgroups based on a "color" argument [cite: 1007-1012].
* **Virtual Topologies**: 
    * **Cartesian Mesh**: `MPI_Cart_create` maps processes into a multi-dimensional mesh, facilitating regular grid-based computations [cite: 1118-1122].
    * **Shift Operations**: `MPI_Cart_shift` determines the source and destination ranks for neighbor communication in the mesh, simplifying code for boundary exchanges [cite: 1159-1167].
    * **Sub-topologies**: `MPI_Cart_sub` can partition a high-dimensional mesh into lower-dimensional sub-grids (e.g., extracting rows or columns from a 2D grid) [cite: 1227-1228].

## 4. One-Sided Communication (Remote Memory Access)
* **Concept**: Decouples data transfer from synchronization; an "origin" process can read or write to a "target" process's memory without explicit pairing [cite: 1248-1251].
* **RMA Operations**:
    * **Windows**: `MPI_Win_create` exposes a specific memory region for remote access [cite: 1307-1311].
    * **Data Movement**: `MPI_Put` (write), `MPI_Get` (read), and `MPI_Accumulate` (remote update) are the primary non-blocking RMA calls [cite: 1314-1320].
* **Synchronization**:
    * **Active Target**: Uses collective `MPI_Win_fence` where all processes participate in completing outstanding operations [cite: 1323-1327].
    * **Passive Target**: Uses `MPI_Win_lock` and `MPI_Win_unlock` to allow an origin process to access a target without the target calling any MPI routines [cite: 1345-1356].

## 5. MPI Derived Data Types
* **Purpose**: Used to exchange complex data structures or non-contiguous memory layouts that standard primitive types cannot handle .
* **Construction**: `MPI_Type_create_struct` creates a new MPI data type by specifying the count, block lengths, and memory displacements of the members .
* **Usage**: Once created, a type must be committed via `MPI_Type_commit` before it can be used in communication routines like `MPI_Send` or `MPI_Recv`[cite: 1454, 1457].