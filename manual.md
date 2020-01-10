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
*   `-ltl-property=<filename>`: This argument inputs the extra LTL property to be verified. The default property is deadlock-freedom. The &lt;filename&gt; is the absolute path of the LTL property file. The format of the LTL property is explained in the following.

## [](#header-2)**(3). The format of the input LTL property file**

MPI-SV verifies deadlock-freedom property in default. If one wants to verify other kinds of property, you can use `-ltl-property=<filename>` to input a LTL property file. Until now, MPI-SV only supports the LTL property of message passing operations. The format of the LTL property file is as follows. 
```
<p>+
<f>
```
There are two parts. The first part defines the events of atomic propositions. Each atomic proposition &lt;p&gt; is defined as follows, where &lt;op&gt; can only be one of the four message passing operators. Please refer to our [paper](#jump1) for the meanings of these four operations. Each atomic proposition defines a message passing between two processes using an operation. The first number defines the rank of the processs where the operation is in. The second number defines the rank of the target process of a send operation or the source process of a receive operation.
```
<p> ::= 'p'[1-9][0-9]* = <op>([0-9]+,[0-9]+)
<op> ::= Recv | Irecv | Ssend | Isend
```
The second part defines an LTL property &lt;f&gt; in the following format. Please refer to this [link](https://en.wikipedia.org/wiki/Linear_temporal_logic) for the semantics of LTL.	
```
Propositional operators

<f> ::=	't'	/* true */
<f> ::=	'f'	/* false */
<f> ::=	'p'[1-9][0-9]*	/* proposition */
<f> ::=	'!' <f>	/* negation */
<f> ::=	'|' <f> <f>	/* disjunction */
<f> ::=	'&' <f> <f>	/* conjunction */
<f> ::=	'i' <f> <f>	/* implication: "i <f1> <f2>" is short-hand for "| ! <f1> <f2>" */
<f> ::=	'e' <f> <f>	/* equivalence */
<f> ::=	'^' <f> <f>	/* exclusive disjunction (xor) */
<f> ::=	[ \t\n\r\v\f] <f>	/* white space is ignored */
<f> ::=	<f> [ \t\n\r\v\f]	/* white space is ignored */

Temporal operators

<f> ::=	'X' <f>	/* next */
<f> ::=	'F' <f>	/* finally, eventually */
<f> ::=	'G' <f>	/* globally, henceforth */
<f> ::=	'U' <f> <f>	/* until */
<f> ::=	'V' <f> <f>	/* release */
```
The following gives an example of LTL property file.
```
p1 = Recv(0,1)
p2 = Recv(2,3)
U ! p2 p1
```
There are two atomic propositions: **p1** and **p2**. **p1** represents that process 0 receives a message from process 1 in the blocking manner. Similarly, **p2** represents that process 2 receives a message from process 3 in the blocking manner. The LTL property is **U ! p2 p1**. It requires that **p1** must become true at some point and before that point **p2** cannot be true, i.e., process 0 must receive a message from process 1 and before that process 2 cannot receive a message from process 3. 

Please feel free to contact us if you have any problem.

*   <font color="#0000FF" size="4">mpi.symbolic.verifier@gmail.com</font>

* * *
<span id="jump1">[1]</span>. Hengbiao Yu, Zhenbang Chen, Xianjin Fu, Ji Wang, Zhendong Su, Jun Sun, Chun Huang, Wei Dong. Symbolic Verification of Message Passing Interface Programs, in 42nd IEEE/ACM International Conference on Software Engineering (ICSE 2020), to appear.

* * *

