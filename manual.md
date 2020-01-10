---
layout: default
---
## **Manual for MPI-SV**

Here we give a brief manual for MPI-SV. There are two steps for using MPI-SV. The first part is to compile the program into LLVM IR. The second part is to use MPI-SV to verify the IR program.

## [](#header-2)**(1). Compile MPI programs**

We provide a compiler script **mpisvcc** to compile MPI program. This script wraps **Clang** and adds the different include directories of MPI head files in default. Besides, **mpisvcc** makes **Clang** to emit IR in default. The basic usage of **mpisvcc** is as follows.
```
mpisvcc <filename> <clang-args>*
```
&lt;clang-args&gt;* is the list **Clang** arguments, which can be shown by the following command.
```
mpisvcc --help
```

The following is an example of using **mpisvcc** to compile a file (file.c) to produce the IR file (file.bc).
```
mpisvcc file.c -o file.bc
```

Absolutely, **mpisvcc** can also be used in a build system to compile a program having multiple files.

## [](#header-2)**(2). Verify the MPI IR programs**


After getting the IR program, we can use **mpisv** to verify the IR program. Deadlock freedom is the default property. The basic usage of **mpisv** is as follows.
```
mpisv <#Procs> <mpisv-args>* <ir-filename> <program-args>*
```
&lt;#Procs&gt; is the number processes. &lt;mpisv-args&gt;* is the list of MPI-SV's arguments. &lt;ir-filename&gt; is the file name of the IR file. &lt;program-args&gt;* is the list of the analyzed program's arguments. MPI-SV's arguments (most are inherited from KLEE) can be shown by the following command.
```
mpisv 0 --help
```

The following is an example of using **mpisv** to verify an IR file (file.bc) in 4 processes (deadlock freedom).
```
mpisv 4 file.bc
```

The following are some important arguments of MPI-SV.

*   `-wild-opt -use-directeddfs-search`: Controls the usage of model-checking. MPI-SV provides a pure symbolic execution with DFS in default; hence, add this argument to use the synergy between symbolic execution and model checking. The following is an example of using **mpisv** to verify (deadlock freedom) an IR file (file.bc) in 8 processes with model checking-based boosting.
```
mpisv 8 -wild-opt -use-directeddfs-search file.bc
```
*   `-output-dir`: This argument specifies the output directory of verification. The default one is the directory containing the program.
*   `-max-time`: This argument specifies the maximum time of verification in second.
*   `-unsafe`: This argument should appear in the last of the command line if the program needs to operate files.

Please feel free to contact us if you have any problem.

*   <font color="#0000FF" size="4">mpi.symbolic.verifier@gmail.com</font>
