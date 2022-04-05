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
- An `./input_files` folder, which contains the compilation and the runtime parameters files.
- An `./output_files` folder, where the catalogues will be saved.

### Folder structure

Besides the folders described above, the code also requires you to create a `./simulation` folder, which can be a symlink to the folder containing your simulation results.

```bash 
ln -s /path/to/your/simulation ./simulation
```
#### MASCLET users:

Note that, if using the MASCLET native reader (```FLAG_MASCLET=1```, see [the section on input data](input_data)), the corresponding folder is `./simu_masclet`.

### Compilation-time parameters

Before compiling, it is required to set some compilation-time parameters, which are related to dimensioning of arrays. These parameters are included in the `./input_files/asohf_parameters.dat` file, and are explained one by one below. Each time you change the parameters, you need to [recompile the code](#compilation).

```fortran 
! Level 0 grid 
INTEGER NMAX,NMAY,NMAZ
PARAMETER (NMAX=128,NMAY=128,NMAZ=128)
```
- `NMAX`, `NMAY` and `NMAZ` are the number of grid cells in each direction, in order to dimension the density arrays (must be larger or equal to the actual grid size used, specified in `./input_files/asohf.dat`; see [the parameters file page](set_parameters.md)).
  
```fortran 
! Refinements 
INTEGER NPALEV,NLEVELS
PARAMETER (NPALEV=10000,NLEVELS=4) 
```
- `NPALEV` is the maximum number of AMR patches. This quantity may vary significant depending on your specific user case, so it is advised to run the code with a large value (e.g. `NPALEV=50000`). If it is too small (i.e., when running the code, the maximum number of patches is reached), the code will stop with an error message. If it is too generous, you can consider lowering it to save memory.
- `NLEVELS` is the maximum number of refinement levels. Take into account that your resolution will be <img src="https://render.githubusercontent.com/render/math?math=L/(N_x \cdot 2^\mathrm{NLEVELS})">, with <img src="https://render.githubusercontent.com/render/math?math=L, \, N_x"> the domain length and the number of grid cells. A typical suggestion is to set it to the force resolution of the simulation, since you are not expected to form structures below this scale.

```fortran 
! Maximum patch extension 
INTEGER NAMRX,NAMRY,NAMRZ
PARAMETER (NAMRX=32,NAMRY=32,NAMRZ=32)
```
- `NAMRX`, `NAMRY` and `NAMRZ` are the maximum extension of the AMR patches. The smaller the value, the more modular the AMR structure will be, which could involve less memory usage. 32 or 64 are typically reasonable values.

```fortran
! Total number of particles (all types) 
INTEGER PARTI_READ
PARAMETER (PARTI_READ=128**3)
```
- `PARTI_READ` has to be set to the total number of particles in the simulation. If the number of particles is not constant (e.g., stellar particles are created), set it to the largest value amongst the snapshots you want to analyse. You do not need to use the exact number of particles, but an upper limit.

```fortran 
! Maximum number of tentative halos 
INTEGER MAXNCLUS
PARAMETER (MAXNCLUS=50000)
```
- `MAXNCLUS` is the maximum number of tentative haloes to be found. It is used to allocate some arrays (halo positions, radii, masses, etc.). If you set it to a too low value, the code will stop and tell you to increase it.

```fortran 
! Maximum number of final halos 
INTEGER NMAXNCLUS
PARAMETER (NMAXNCLUS=10000)
```
- `NMAXNCLUS` is the maximum number of real haloes to be found. During the process of halo finding, many of the tentative haloes will be discarded because they may correspond to poor haloes, with too few particles. Thus, you can set this variable to a more stringent value to save memory; while you can set it to the same value as `MAXNCLUS` if memory usage is not a concern in your application.


```fortran 
! Number of bins for computing profiles 
INTEGER NBINS
PARAMETER (NBINS=20) 
```
- `NBINS` is the number of bins used to store the enclosed mass profiles. Internally, these profiles are later used in the substructure finding step. In general, it is not necessary to change this value.

```fortran 
! Number of DM species 
INTEGER N_ESP
PARAMETER (N_ESP=4)
```
- `N_ESP` is the number of DM species. If your simulation contains different mass particles, you can set this variable to the number of different species. If you want to treat to use a kernel size based on local density, or a single kernel for all particles, you can set it to your desired number of especies. 

### Compilation

ASOHF can be compiled either with the GNU (`gfortran`) or with the Intel (`ifort`) Fortran Compilers. The following compilation options work well for us using gfortran-11 and ifort 2018 on several machines:

#### GNU:
```bash
gfortran -O3 -march=native -fopenmp -mcmodel=medium -funroll-all-loops -fprefetch-loop-arrays -mieee-fp -ftree-vectorize particles.f asohf.f -o asohf.x
```

#### Intel:
```bash
ifort -O3 -mcmodel=medium -qopenmp -shared-intel -fp-model consistent -ipo -xHost asohf.f -o asohf.x
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
