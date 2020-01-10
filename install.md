---
layout: default
---

## **Manual for Installing MPI-SV**

Now, MPI-SV can only support the 64-bit binary installation. **MPI-SV requires the OS to be Ubuntu 14.04 64 bit OS**. If you just want to have a try of MPI-SV, we recommend to use the docker image. Please refer to [**Manual for the Docker Image**](dockerManual) for how to install and use MPI-SV's docker image.


The binary installation steps are as follows.

## [](#header-2)**(1). Install Dependency Libraries**

```
sudo apt-get update
sudo apt-get install flex bison build-essential git libgdiplus 
```

## [](#header-2)**(2). Install LLVM&Clang&Compiler-rt 3.1**

We need to download the source code of LLVM&Clang&Compiler-rt 3.1 and manually compile them.

Suppose the source and installation directory for LLVM&Clang&Compiler-rt is **&lt;Clang_Dir&gt;**. First, we need to install binutils.
```
cd <Clang_Dir>
mkdir binutils-install
wget https://ftp.gnu.org/gnu/binutils/binutils-2.24.tar.gz
tar -xvf binutils-2.24.tar.gz
cd binutils-2.24
./configure --prefix=<Clang_Dir>/binutils-install --enable-gold --enable-plugins
make
make install
```

Then, we download and compile LLVM&Clang&Compiler-rt 3.1. The compiling step will take a long time.

Source code downloading step:
```
cd <Clang_Dir>
wget http://llvm.org/releases/3.1/llvm-3.1.src.tar.gz
tar -xvf llvm-3.1.src.tar.gz
cd llvm-3.1.src/tools
wget http://llvm.org/releases/3.1/clang-3.1.src.tar.gz
tar -xvf clang-3.1.src.tar.gz
mv clang-3.1.src clang
cd ../projects
wget http://llvm.org/releases/3.1/compiler-rt-3.1.src.tar.gz
tar -xvf compiler-rt-3.1.src.tar.gz
mv compiler-rt-3.1.src compiler-rt
cd ../../
```
Compiling step:
```
cd <Clang_Dir>
mkdir llvm-build
mkdir llvm-install
cd llvm-build
../llvm-3.1.src/configure --enable-optimized --enable-assertions --with-binutils-include=<Clang_Dir>/binutils-install/include --prefix
=<Clang_Dir>/llvm-install
sudo cp /usr/include/x86_64-linux-gnu/c++/4.8/bits/* /usr/include/c++/4.8/bits/
make
make install
```

**The following step in compiling is important to fix a problem in compiling compiler-rt.**
```
sudo cp /usr/include/x86_64-linux-gnu/c++/4.8/bits/* /usr/include/c++/4.8/bits/
```

**The following installation step may fail due to LLVM's document generation. Please retry the command until it succeeds**
```
make install
```

**After successfully compiling and installing LLVM&Clang&Compiler-rt, we need to add Clang's installation directory (&lt;Clang_Dir&gt;/llvm-install/bin) into the system PATH envorinment variable. If you do not know how to add a directory to PATH, please refer to this [post](https://hackprogramming.com/2-ways-to-permanently-set-path-variable-in-ubuntu/)**


## [](#header-2)**(3). Install Mono 2.10.8.1**

We need to install Mono to run PAT.
```
wget https://download.mono-project.com/sources/mono/mono-2.10.8.1.tar.gz
tar -xvf mono-2.10.8.1.tar.gz
cd mono-2.10.8.1
./configure
make
sudo make install
```

## [](#header-2)**(4). Install PAT 3.4.0**

Please download PAT from its [website](https://pat.comp.nus.edu.sg/). The version that we use is **3.4.0**.

After downloading, please move PAT to your **home directory**, and change PAT's directory name to **pat**. 

Now, we suppose that **PAT3.Concole.exe** exists in **~/pat** directory.

## [](#header-2)**(5). Install MPI-SV**

Last, we download MPI-SV from our repo and install.

```
cd <Install_Dir>
git clone https://github.com/mpi-sv/mpi-sv.git mpi-sv
cd mpi-sv
./install.sh
source ~/.bashrc
```

**After successfully installing MPI-SV, MPI-SV's installation directory (&lt;Install_Dir&gt;/mpi-sv) is already added into the system PATH envorinment variable. Then, the two scripts, i.e., mpisv and mpisvcc, can be invoked at any directory.**

## [](#header-2)**Contacts**

Please feel free to contact us if you have any problem.

*   <font color="#0000FF" size="4">mpi.symbolic.verifier@gmail.com</font>
