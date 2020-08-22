---
layout: default
---

<!--
![framework](./picture/framework.png)
 -->
Message Passing Interface (MPI) is the current *de facto* standard for developing the parallel applications in high-performance computing. However, developing MPI programs is challenging due to the non-determinism caused by parallel execution and complex programming features such as non-deterministic communications and asynchrony. 

**MPI-SV** is an **automatic** symbolic verifier for verifying MPI **C** programs. **MPI-SV** supports the verification of **non-blocking** MPI programs. **MPI-SV** can verify the properties in Linear Temporal Logic (LTL). 

The technique implemented by **MPI-SV** is presented in [[1]](#jump1). The technique combines symbolic execution and model checking in a synergistic manner to enlarge the scope of verifiable properties and improve the scalability of verification.


* * *

*   [**Introduction**](intro)

*   [**Installation**](install)

*   [**Manual**](manual)

*   [**Tutorial**](tutorials)

*   [**Docker Image**](https://hub.docker.com/u/mpisv)

*   [**Manual for the Docker Image**](dockerManual)

*   [**Binary Github Repo**](https://github.com/mpi-sv/mpi-sv)

*   [**Source Code Github Repo**](https://github.com/mpi-sv/mpi-sv-src)

* * *


# [](#header-1)**Contacts**

Please feel free to contact us if you have any problem.

*   <font color="#0000FF" size="4">mpi.symbolic.verifier@gmail.com</font>

* * *
<span id="jump1">[1]</span>. Hengbiao Yu, Zhenbang Chen, Xianjin Fu, Ji Wang, Zhendong Su, Jun Sun, Chun Huang, Wei Dong. Symbolic Verification of Message Passing Interface Programs, in 42nd IEEE/ACM International Conference on Software Engineering (ICSE 2020). ([PDF](https://zbchen.github.io/Papers_files/icse-2020-preprint.pdf), [Video](https://www.youtube.com/watch?v=WdR7X3GMRGA))

<span id="jump2">[2]</span>. Zhenbang Chen, Hengbiao Yu, Xianjin Fu, Ji Wang. MPI-SV: A Symbolic Verifier for MPI Programs, in 42nd IEEE/ACM International Conference on Software Engineering (ICSE 2020), Demo track. ([PDF](http://zbchen.github.io/Papers_files/icse-2020-demo.pdf), [Video](https://www.youtube.com/watch?v=VmribkqzrlI))

* * *
