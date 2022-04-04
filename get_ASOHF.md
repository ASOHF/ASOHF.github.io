---
layout: default
title: Getting ASOHF
nav_order: 2
---

## Getting ASOHF

This section describes the required operations to download ASOHF, setting the required folder structure, compiling it and running it.

### Source download

The easiest way to download the code is to directly clone [the repository](https://github.com/dvallesp/ASOHF). If you happen to not have git in your system, you can find [some nice tutorials](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) on how to install it, or you could just download the source directly from the GitHub repository page.

To get the code, just run the following command:

```bash
git clone https://github.com/dvallesp/ASOHF
```

This will create an ASOHF folder in your working directory, inside of which you fill find:

- The source code of ASOHF.
- An `input_files` folder, which contains the compilation and the runtime parameters files.
- An `output_files` folder, where the catalogues will be saved.

### Folder structure

Besides the folders described above, the code also requires you to create a `simulation` folder, which can be a symlink to the folder containing your simulation results.

```bash 
ln -s /path/to/your/simulation ./simulation
```
<div class="code-example" markdown="1">MASCLET users {: .label .label-green }</div>

Note that, if using the MASCLET native reader (FLAG_MASCLET=1, see [the section on input data](input_data)), the corresponding folder is `simu_masclet`.

### Compilation

ASOHF can be compiled either with the GNU (`gfortran`) or with the Intel (`ifort`) Fortran Compilers. The following compilation options work well for us using gfortran-11 and ifort 2018 on several machines:

GNU:
```bash
gfortran -O3 -march=native -fopenmp -mcmodel=medium -funroll-all-loops -fprefetch-loop-arrays -mieee-fp -ftree-vectorize particles.f asohf.f -o asohf.x
```

Intel:
```bash
ifort -O3 -mcmodel=medium -qopenmp -shared-intel -fp-model consistent -ipo -xHost asohf.f -o asohf_sa.x
```

### Running

For running ASOHF, you may want to use a shell script containing all the necessary environment variables. An example is served below:

```bash
>> cat run.sh

#!/bin/bash

export OMP_NUM_THREADS=24 # number of threads
export OMP_STACKSIZE=3000m # size of the stack
export OMP_PROC_BIND=true # avoid switching threads
export OMP_WAIT_POLICY=active
export KMP_BLOCKTIME=1000000 #very large number

ulimit -c 0 # avoid core dumps

./asohf.x > asohf.out

```
