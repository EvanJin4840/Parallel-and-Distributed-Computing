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