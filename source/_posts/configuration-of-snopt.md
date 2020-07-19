---
title: Configuration of SNOPT
date: 2020-04-22 11:53:19
tags: 
  - Optimization
categories: Programming
---

SNOPT (**S**parse **N**onlinear **OPT**imizer) is a general-purpose system for constrained optimization, which requires a license to be executed.
It minimizes a linear or nonlinear function subject to bounds on the variables and sparse linear or nonlinear constraints.
It is suitable for large-scale linear and quadratic programming and for linearly constrained optimization, as well as for general nonlinear programs.

# Simple Tutorial in 2D

## Apply the license

UCSD offers evaluations versions (for 3 months) of optimization libraries **SNOPT7** and **SQOPT7** for macOS, Windows and Linux systems. [Click here](https://ccom.ucsd.edu/~optimizers/downloads/#) to apply the license.

## Donwload libraries

After receiving the email from USCD, we can click the link approached to the email to download the libraries in different versions. As I would like to use the C++ version, `libsnopt7_cpp.so` has to be downloaded. Besides,
the C++ interface is compiled from the [snopt-interface github repository](https://github.com/snopt/snopt-interface). For Linux and MacOS, a C++ compiler must be used to link programs to the libraries above. For example,
if we would like to compile and execute a C++ example named `myProb.cpp`:

```bash
>> g++ -O -I$SNOPT/include -c myProb.cpp -o myProb.o
>> g++ -O myProb.o -o myProb -L$SNOPT/lib -lsnopt_cpp
```

# Step by Step Tutorial

Actually, as for a beginner, we need to do some extra works to compile and execute the program successfully. Here I will provide a step-by-step tutorial to install and configure the **SNOPT**:
1. Apply the license.
2. Download [snopt-interface github repository](https://github.com/snopt/snopt-interface) and extract in the `$HOME` folder. Then we can rename the folder to **SNOPT**.
3. Enter to the folder and create folders named **lib** and **lic** in it.
```bash
>> cd SNOPT
>> mkdir lib lic
```
4. Download the license approached to the UCSD email and move it to the **lic** folder.
5. Download the library `libsnopt7_cpp.so` in Linux version from the links approached to the UCSD email and move it into the **lib** folder.
6. Edit the `~/.bashrc` file as follow:
```bash
>> export SNOPT_LICENSE=$HOME/SNOPT/lic/snopt7.lic
>> export LD_LIBRARY_PATH=$HOME/SNOPT/lib
>> export SNOPT=$HOME/SNOPT
```
7. Test if we configure the environment successfully by compiling and executing the routines in **cppexamples** folder.
```bash
>> cd SNOPT/cppexamples
>> g++ -O -I$SNOPT/include -c sntoya.cpp -o sntoya.o
>> g++ -O sntoya.o -o sntoya -L$SNOPT/lib -lsnopt7_cpp
```

{% note info %}
  If utilizing shared libraries `.dylib/.so`, the location of the library files must be added to `LD_LIBRARY_PATH` on Linux.
  If the error `libgfortran.so.4` required occurs, it means that gcc-7 and gfortran7 are required to be installed to meet the dependencies.
{% endnote %}

- Install gfortran7
```bash
>> sudo add-apt-repository ppa:jonathonf/gcc-7.1
>> sudo apt-get update
>> sudo apt-get install gcc-7 g++-7 gfortran-7
```

In the end, after compiling the linking the `sntoya.cpp`, we get an executable file `sntoya`. If no error throws while we execute it, it means the we configure **SNOPT** successfully. 

# References

- [SNOPT7 Reference Guide](https://ccom.ucsd.edu/~optimizers/docs/snopt/index.html)
- [User guide for SNOPT7](https://web.stanford.edu/group/SOL/snopt.htm)
