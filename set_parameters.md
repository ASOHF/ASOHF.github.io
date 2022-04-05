---
layout: default
title: Setting the parameters
nav_order: 4
---

## Setting the parameters of ASOHF

In this section, we describe the run-time parameters of ASOHF, which are set in the `./input_files/asohf.dat` file. These can be changed after [compilation of the code](get_ASOHF#compilation).

### General parameters block

These parameters refer to the general behaviour of the code.

```
Files: first, last, every -------------------------------------------->
0,0,50
```
- Iterations to analyse. It is assumed that the outputs have been saved each `every` iteration. If instead, outputs have been saved at a pace that does not correspond to constant interval of iterations, it is straightforward to change the main loop of the code (look in the `asohf.f` file for the `DO IFI2=1, NFILE2` line). 

```
Cells per direction (NX,NY,NZ) --------------------------------------->
128,128,128
```
- Number of grid cells in each direction. These grid sizes should be smaller or equal than the [compilation parameters `NMAY`, `NMAZ` and `NMAX`](get_ASOHF#compilation-time-parameters).

```
DM particles (all levels) -------------------------------------------->
2097152
```
- Number of DM particles in the simulation. This number should be smaller than the [compilation parameter `PARTI_READ`](get_ASOHF#compilation-time-parameters).

```
Hubble constant (h), omega matter, fraction of DM to total mass ------>
0.678,0.31,0.845
```
- The Hubble dimensionless constant (<img src="https://render.githubusercontent.com/render/math?math=h\equiv H_0/(100\,\mathrm{km}\,\mathrm{s}^{-1}\,\mathrm{Mpc}^{-1})">), the matter density parameter (<img src="https://render.githubusercontent.com/render/math?math=\Omega_m=\rho_B(z=0)/\rho_\mathrm{crit}(z=0)">) and the fraction of DM to total mass (<img src="https://render.githubusercontent.com/render/math?math=f_\mathrm{DM}\equiv 1 - \Omega_b / \Omega_m">).

```
Max box sidelength (in input length units) --------------------------->
40.0
```
- The largest of the box side lengths, in the input length units.

```
Parallel(=1),serial(=0) / Number of processors ----------------------->
1,24
```
- Either ASOHF is run in serial mode (=0) or in parallel (=1) and the number of processors used.

```
Reading flags: IS_MASCLET (=0, no; =1, yes), MASCLET_GRID ------------>
0,0
```
- Generally, set these two parameters to 0.
#### MASCLET users
- If reading data from MASCLET, set the first parameter to 1
   - If using MASCLET grid (not recommended), set the second parameter to 1; if building the grid from the particle distribution (recommended), set it to 0.

```
Output flags: grid_asohf,density,haloes_grids,subs_grids,subs_part --->
0,0,0,0,0
```
- Output intermediate files for debugging purposes (=0, no; =1, yes):
    - ASOHF grid file 
    - ASOHF density interpolation 
    - List of haloes pre-identified within the grid
    - List of subhaloes pre-identified within the grid 
    - List of substructures identified using particles

```
Input units: MASS (Msun; <0 for Msun/h), LENGTH (cMpc; <0 for cMpc/h),
 SPEED (km/s), ALPHA (v_input = a^alpha dx/dt; 1 is peculiar vel.) --->
9.1717e18,1.0,299792.458,1.0
```
- Units of the input data:
    - Input unit of mass in solar masses; if negative, the input mass is assumed to be given in Msun/h.
    - Input unit of length in comoving Mpc; if negative, the input length is assumed to be given in cMpc/h.
    - Input unit of speed in km/s.
    - Since different codes use different velocity variables, specify the exponent <img src="https://render.githubusercontent.com/render/math?math=\alpha"> such that <img src="https://render.githubusercontent.com/render/math?math=\mathbf{v}_\mathrm{input} = a(t)^\alpha \frac{\mathrm{d}\mathbf{x}}{\mathrm{d}t}">, with a(t) the scale factor of the given cosmology and x the comoving position. For example, with <img src="https://render.githubusercontent.com/render/math?math=\alpha=1">, the input velocity is the usual peculiar velocity.
#### MASCLET users:
This line can be ignored, as the reader will automatically take care of MASCLET units.

```
Input domain (in input length units; x1,x2,y1,y2,z1,z2) -------------->
-20.0,20.0,-20.0,20.0,-20.0,20.0
```
- Specify the input domain of the simulation, with the x, y and z left and right corners of the domain.

### Domain decompose block

```
Keep only particles inside a given domain (=0, no; =1, yes) ---------->
0
```
- Set this parameter to 1 if you want to analyse only particles inside a given subdomain.

```
Domain to keep particles (in input length units; x1,x2,y1,y2,z1,z2) -->
0.,0.,0.,0.,0.,0.
```
- If the above parameter is set to 1, specify the volume where you want to keep the particles.

### Mesh creation parameters block

```
Levels for the mesh (stand-alone) ------------------------------------>
4
```
```
PARCHLIM(=0 no limit patches/level,>0 limit) ------------------------->
0
```
```
Max num of patches per level(needs PARCHLIM>0) ----------------------->
1000
```
```
Refinement threshold (num. part.), refinable fraction to extend ------>
3,0.05
```
```
Minimum patch size (child cells) ------------------------------------->
14
```
```
Base grid refinement border, AMR grids refinement border ------------->
4,0
```
```
Allow for addition overlap (to avoid losing signal) in the mesh ------>
0
```
```
Density interpolation kernel (1=linear, 2=quadratic) ----------------->
2
```
```
Variable for mesh halo finding: 1(dm), 2(dm+stars) ------------------->
1
```
```
Kernel level for stars (if VAR=2) ------------------------------------>
5
```
```
Particle especies (0=there are different mass particles, 1=equal mass
 particles, use local density, 2=equal mass particles, do nothing) --->
1
```

### Halo finding parameters block

```
Max. reach around halos (cMpc), excluded cells in boundaries --------->
4.0,1
```
```
Minimum fraction of shared volume to merge (in grid search) ---------->
0.6
```
```
FLAG_WDM (=1 write DM particles, =0 no) ------------------------------>
1
```
```
Search for substructure (=1 yes, =0 no) ------------------------------>
0
```
```
Compute energies (=1 yes, =0 no), max num of part. for direct sum ---->
0,4000
```
```
Minimum number of particles per halo --------------------------------->
25
```


### Stellar halo finding parameters block

```
Look for stellar haloes (=1 yes, =0 no) ------------------------------>
0
```
```
Minimum number of stellar particles per stellar halo ----------------->
25
```
```
Cut stellar halo if density increases more than factor from min ------>
5.0
```
```
Cut stellar halo if radial distance of consecutive stars > (ckpc) ---->
10.0
```
```
Cut stellar halo if rho_* falls below this factor of rho_B ----------->
1.0
```
