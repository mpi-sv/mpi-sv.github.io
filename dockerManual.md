---
layout: default
---
## Manual for Installing and Running the Docker Image

## [](#header-2)**(1). Install and start docker**

We suppose that you have installed the **docker** environment; otherwise, please go to [this link](https://docs.docker.com/install/) for help. 

## [](#header-2)**(2). Download MPI-SV's docker image**

```
docker pull mpisv/mpi-sv
```

If the image downloading succeeds, you can use the following command to have a check.

```
docker images
```

You should see that an image named **mpisv/mpi-sv** exists.

## [](#header-2)**(3). Run the test**

You can run the docker image to have a try.

The following command compiles the demo program in [tutorial](tutorial). 

```
docker run -v $(pwd):/test mpisv/mpi-sv mpisvcc /root/mpi-sv/examples/demo.c -o /test/demo.bc
```

If this command succeeds, you should find **demo.bc** in your current directory.

The following command uses MPI-SV to verify the demo program in [tutorial](tutorial) in 4 processes. 

```
docker run -v $(pwd):/test mpisv/mpi-sv mpisv 4 /test/demo.bc
```

## [](#header-2)**(4). Check the results**

If everything is okay, you will see many output messages and find the following messages in the last.

```
MPI-SV: totally 4 iterations
MPI-SV: find a violation in the 4 iterations
Different Pcs: 2
```

## [](#header-2)**Contacts**

Please feel free to contact us if you have any problem.

*   <font color="#0000FF" size="4">mpi.symbolic.verifier@gmail.com</font>
