---
layout: default
---
## **Tutorials**

We provide two tutorials for MPI-SV. We first use MPI-SV to analyze two motivation programs wherein the verification properties are **deadlock freedom** and the one written in **Linear Temporal Logic (LTL)**, respectively. Then, we use MPI-SV to verify a real-world MPI program. **Suppose MPI-SV is installed at **&lt;mpisv-dir&gt;**.

## [](#header-2)**The Motivation Examples**

We have two motivation examples. The first one is for demostrating deadlock freedomv verification; the second one is for demostrating the verification of LTL temporal property. 

The source code of the example for **deadlock freedom** verification is as follows. The source code file is located at **&lt;mpisv-dir&gt;/examples/demo.c**.

```c
#include <stdio.h>
#include "mpi.h"

int main(int argc, char **argv) {
    int nprocs = -1;
    int rank = -1;
    char processor_name[128];
    int namelen = 128;

    MPI_Status status;
    MPI_Request req;

    /* init */
    MPI_Init(&argc, &argv);
    MPI_Comm_size(MPI_COMM_WORLD, &nprocs);
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    MPI_Get_processor_name(processor_name, &namelen);
    printf("(%d) is alive on %s\n", rank, processor_name);
    fflush(stdout);

    if (rank == 0) {
        char c;
        klee_make_symbolic(&c, sizeof(c), "c");
        int v1, v2;
        if (c != 'a') {
            MPI_Recv(&v1, 1, MPI_INT, 1, 0, MPI_COMM_WORLD, &status);
        } else {
            MPI_Irecv(&v1, 1, MPI_INT, MPI_ANY_SOURCE, 0, MPI_COMM_WORLD, &req);
        }
        MPI_Recv(&v2, 1, MPI_INT, 3, 0, MPI_COMM_WORLD, &status);
    } else {
        MPI_Isend(&rank, 1, MPI_INT, 0, 0, MPI_COMM_WORLD, &req);
    }

    MPI_Finalize();

    return 0;
}
```

The first process (i.e., the one with rank 0, denoted by P0) gets an input character **c** first. Then, P0 will receive v1's value from P1 if **c** is **not** equal to **'a'**; otherwise, P0 will use a non-blocking wildcard receive to receive the value. Finally, P0 receives v2's value from P3. For the other processes, each sends its rank value to P0 and terminates. Suppose that we run this MPI program in four process. An error will happen if **c** is equal to **'a'** and the MPI_Irecv receives the message from P3. Then, the last blocking receive blocks P0 because the message from P3 is already received by the MPI\_Irecv, which results in a deadlock, i.e., the program does not terminate but cannot progress.

We can use MPI-SV to verify this program. First, we use **mpisvcc** to compile the program.

```
cd <mpisv-dir>/examples
mpisvcc demo.c -o demo.bc
```

Then, we can use **mpisv** to verify the program in 4 processes.

```
mpisv 4 demo.bc
```

The last three output messages should be as follows.
```
MPI-SV: totally 4 iterations
MPI-SV: find a violation in the 4 iterations
Different Pcs: 2
```

It indicates that MPI-SV finds the deadlock at the 4th iteration. MPI-SV outputs the execution contexts of the four processes as follows.

```
E1212 22:53:22.854312     7 ExecutionState.cpp:643] ***********************************************************
I1212 22:53:22.854315     7 ExecutionState.cpp:645] Call:    MPI_Recv(/test/demo.c,30)
I1212 22:53:22.854317     7 ExecutionState.cpp:645] Call:    usermain(/home/zbchen/mpisv/src/MPISV/AzequiaMPI.llvm/azqmpi/src/private/main.c,74)
I1212 22:53:22.854319     7 ExecutionState.cpp:645] Call:    node_main__(/home/zbchen/mpisv/src/MPISV/AzequiaMPI.llvm/idsp/blk/impl/thr.c,228)
E1212 22:53:22.854322     7 ExecutionState.cpp:649] Call:    wrapper
E1212 22:53:22.854329     7 ExecutionState.cpp:650] ***********************************************************
E1212 22:53:22.854331     7 ExecutionState.cpp:643] ***********************************************************
I1212 22:53:22.854333     7 ExecutionState.cpp:645] Call:    MPI_Finalize(/test/demo.c,35)
I1212 22:53:22.854336     7 ExecutionState.cpp:645] Call:    usermain(/home/zbchen/mpisv/src/MPISV/AzequiaMPI.llvm/azqmpi/src/private/main.c,74)
I1212 22:53:22.854338     7 ExecutionState.cpp:645] Call:    node_main__(/home/zbchen/mpisv/src/MPISV/AzequiaMPI.llvm/idsp/blk/impl/thr.c,228)
E1212 22:53:22.854341     7 ExecutionState.cpp:649] Call:    wrapper
E1212 22:53:22.854343     7 ExecutionState.cpp:650] ***********************************************************
E1212 22:53:22.854346     7 ExecutionState.cpp:643] ***********************************************************
I1212 22:53:22.854347     7 ExecutionState.cpp:645] Call:    MPI_Finalize(/test/demo.c,35)
I1212 22:53:22.854351     7 ExecutionState.cpp:645] Call:    usermain(/home/zbchen/mpisv/src/MPISV/AzequiaMPI.llvm/azqmpi/src/private/main.c,74)
I1212 22:53:22.854352     7 ExecutionState.cpp:645] Call:    node_main__(/home/zbchen/mpisv/src/MPISV/AzequiaMPI.llvm/idsp/blk/impl/thr.c,228)
E1212 22:53:22.854355     7 ExecutionState.cpp:649] Call:    wrapper
E1212 22:53:22.854357     7 ExecutionState.cpp:650] ***********************************************************
E1212 22:53:22.854359     7 ExecutionState.cpp:643] ***********************************************************
I1212 22:53:22.854362     7 ExecutionState.cpp:645] Call:    MPI_Finalize(/test/demo.c,35)
I1212 22:53:22.854364     7 ExecutionState.cpp:645] Call:    usermain(/home/zbchen/mpisv/src/MPISV/AzequiaMPI.llvm/azqmpi/src/private/main.c,74)
I1212 22:53:22.854367     7 ExecutionState.cpp:645] Call:    node_main__(/home/zbchen/mpisv/src/MPISV/AzequiaMPI.llvm/idsp/blk/impl/thr.c,228)
E1212 22:53:22.854369     7 ExecutionState.cpp:649] Call:    wrapper
E1212 22:53:22.854372     7 ExecutionState.cpp:650] ***********************************************************
```

So, we can see that the first process blocks at the Recv operation and the other 3 processes terminate. 

Then, we enable the model checking-based boosting in **mpisv** to verify the program in 4 processes.

```
mpisv 4 -wild-opt -use-directeddfs-search demo.bc
```

The last three output messages should be as follows.
```
MPI-SV: totally 2 iterations
MPI-SV: find a violation in the 2 iterations
Different Pcs: 2
```

It indicates that MPI-SV **only** needs 2 iterations to find the deadlock by using model checking-based boosting.

***

The source code of the example for **Linear Temporal Logic (LTL)** temporal property verification is as follows. The source file is located at **&lt;mpisv-dir&gt;/examples/demo-ltl.c**.
```c
#include <stdio.h>
#include "mpi.h"

int main(int argc, char **argv) {
    int nprocs = -1;
    int rank = -1;
    char processor_name[128];
    int namelen = 128;

    MPI_Status status;
    MPI_Request req;

    /* init */
    MPI_Init(&argc, &argv);
    MPI_Comm_size(MPI_COMM_WORLD, &nprocs);
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    MPI_Get_processor_name(processor_name, &namelen);
    printf("(%d) is alive on %s\n", rank, processor_name);
    fflush(stdout);

    if (rank == 0) {
        int v1;
        MPI_Recv(&v1, 1, MPI_INT, 1, 0, MPI_COMM_WORLD, &status);
    } else if (rank == 1) {
        MPI_Isend(&rank, 1, MPI_INT, 0, 0, MPI_COMM_WORLD, &req);
    } else if (rank == 2) {
        int v1;
        MPI_Recv(&v1, 1, MPI_INT, 3, 0, MPI_COMM_WORLD, &status);
    } else if (rank == 3) {
        MPI_Isend(&rank, 1, MPI_INT, 2, 0, MPI_COMM_WORLD, &req);
    }

    MPI_Finalize();
    printf("(%d) Finished normally ---------------\n", rank);

    return 0;
}
```

As the program depicted, P0 and P2 receive v1's value from P1 and P3, respectively. Meanwhile, P1 sends its rank value to P0; P3 sends its rank value to P2. 

The file of the **Linear Temporal Logic (LTL)** property is located at **&lt;mpisv-dir&gt;/examples/ltl**. The content is as follows:

```
p1 = Recv(0,1)
p2 = Recv(2,3)
U ! p2 p1
```
We use the propositions **p1** and **p2** to represent the operation **Recv(0,1)** (i.e., P0 recives a message from P1 with blocking) and **Recv(2,3)** (i.e., 3rewP2 recives a message from P3 with blocking), respectively. And the LTL property we wanna verify is **U ! p2 p1**, that is, **! p2** has to hold at least until **p1** becomes true. This property does not hold since there is no relation between the occurence of **p1** and **p2**. The format of LTL property file can be found in [**Manual**](manual).

We can use MPI-SV to verify this program. First, we use **mpisvcc** to compile the program.

```
cd <mpisv-dir>/examples
mpisvcc demo-ltl.c -o demo-ltl.bc
```

Then, we can use **mpisv** to verify the program.

```
mpisv 4 -wild-opt -use-directeddfs-search -ltl-property=$(pwd)/ltl demo-ltl.bc 
```

The output messages should be as follows.

```
...
I0110 16:31:31.862882    25 analysis.cpp:123] =======================================================
Assertion: P() deadlockfree
********Verification Result********
The Assertion (P() deadlockfree) is VALID.

********Verification Setting********
Admissible Behavior: All
Search Engine: First Witness Trace using Depth First Search
System Abstraction: False


********Verification Statistics********
Visited States:10
Total Transitions:13
Time Used:0.0389141s
Estimated Memory Used:8560.64KB


=======================================================
Assertion: P() |= (!"D3_0?0" U "D1_0?0")
********Verification Result********
The Assertion (P() |= (!"D3_0?0" U "D1_0?0")) is NOT valid.
A counterexample is presented as follows.
<init -> D3_0!0 -> D3_0?0 -> D1_0!0 -> D1_0?0 -> terminate>

********Verification Setting********
Admissible Behavior: All
Search Engine: Loop Existence Checking - The negation of the LTL formula is a safety property!
System Abstraction: False
Fairness: no fairness


********Verification Statistics********
Visited States:6
Total Transitions:7
Time Used:0.0031145s
Estimated Memory Used:8548.352KB





MPI-SV: totally 1 iterations
MPI-SV: find a violation in the 1 iterations
Different Pcs: 1
```

It indicates that MPI-SV finds the deadlock at the 1th iteration and reports the counter-example.

However, if we use pure symbolic execution to verify this propety by the following command.
```
mpisv 4 -ltl-property=$(pwd)/ltl demo-ltl.bc 
``` 
The expected result is as follows.
```
...
MPI-SV: totally 1 iterations
No Violation detected by MPI-SV
Different Pcs: 1

```
MPI-SV will report that no violation is detected. It indicates that pure symbolic execution fails to detect the violation. Hence, with model-checking based boosting, pure symbolic execution cannot verify the temporal properties in LTL.


## [](#header-2)**A real-world image manipulation MPI program**

This program is an image manipulation program. When running the program, there is a master process and several worker processes. The worker processes are manipulating different parts of the input image in parallel and send the results to the master process, which then merge the results to get the new image. The program (image_mani.c) is in the **examples** directory of MPI-SV, and there is also an input image file whose name is **dot.bmp**.

First, we modify the input file name inside the program at Line 325. Change the file name to the absolute file name of **dot.bmp**.

Then, we compile the program using **mpisvcc**.
```
cd <mpisv-dir>/examples
mpisvcc image_mani.c -o image_mani.bc
```

After generating the IR file, we use **mpisv** to verify the program in 6 processes.
```
mpisv 6 image_mani.bc -unsafe
```
After a while, when MPI-SV terminates, the last three output messages should be as follows.
```
MPI-SV: totally 96 iterations
No Violation detected by MPI-SV
Different Pcs: 4
```
It means MPI-SV needs to explore 96 paths to verify the program is deadlock-free. 


Then, we enable the model checking-based boosting in **mpisv** to verify the program also in 6 processes.
```
mpisv 6 -wild-opt -use-directeddfs-search image_mani.bc -unsafe
```
The last three output messages should be as follows.
```
MPI-SV: totally 96 iterations
No Violation detected by MPI-SV
Different Pcs: 4
```
MPI-SV with model checking-based boosting only needs 4 paths to finish the verification. The verification time is also much less than that of pure symbolic execution. So, the synergy between symbolic execution and model checking is the key for MPI-SV's scalability.


Please feel free to contact us if you have any problem.

*   <font color="#0000FF" size="4">mpi.symbolic.verifier@gmail.com</font>
