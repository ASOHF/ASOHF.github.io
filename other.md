---
layout: default
title: Other topics
nav_order: 6
---

## Other topics
Here we describe other topics which are not essential, but which may be of interest to the user.

### Domain decomposition
The computational cost of running ASOHF in simulations with a large number of particles can be reduced by using a domain decomposition. The domain decomposition is a way to split the simulation box into smaller boxes, or subdomains, which are then processed independently. Since the wall time taken by ASOHF scales as <img src="https://render.githubusercontent.com/render/math?math=\propto N_\mathrm{part} N_\mathrm{haloes}">, running ASOHF with domain decomposition can decrease the wall time required to run the code in a factor  <img src="https://render.githubusercontent.com/render/math?math=1/d"> if all the subdomains are run sequentially (i.e., one after the other). If instead they are run concurrently (e.g., in a memory distributed system with many nodes), the computating time can be reduced by a factor <img src="https://render.githubusercontent.com/render/math?math=1/d^2">, with _d_ the number of subdomains. 

However, this is only useful in large domains (<img src="https://render.githubusercontent.com/render/math?math=\gtrsim 50 \, \mathrm{Mpc}">). This is due to the fact that the strategy requires an overlap, whose size has to be at least the radius of the largest expected halo, to avoid losing objects in the boundaries of the domains.

The domain decomposition strategy employs the domain selection feature in the [parameters file](set_parameters). Since no communication is needed between the different subdomains, until a final unification step, we have chosen to implement in a simple yet versatile way, which requires no libraries such as MPI.

To automatize the process, we provide a python3 script, `./tools/setup_domdecomp.py`. The user needs to set the sidelengths and left corner of the domain in the code input units, 

```python 
sidelength_x=25000.
sidelength_y=25000.
sidelength_z=25000.
domain_xleft=0.
domain_yleft=0.
domain_zleft=0.
```

the number of divisions of the domain along each direction and the size of this _security boundary_ (setting it to around 4 Mpc is enough for simulations with galaxy clusters; for example),
```python
div_x=2
div_y=2
div_z=2
sec_boundary=2500.
```

and several paths (to the executable, the simulation folder, and other required files to launch ASOHF).
```python 
dd_foldername='domain_{:}_{:}_{:}'
executable='asohf.x'
other_files=['run.sh']
simulation_foldername='simulation'
```

Once the script has been executed, a set of folders named `domain_0_0_0`, `domain_0_0_1`, etc. will have been created, all of them containing the executable, a symbolic link to the simulation folder, and the other files needed to run ASOHF. A `domains.out` plain text file will contain the information about the domain decomposition that is later needed to unify the catalogues. To run automatically the code in all the domains, we provide sample shell scripts in the `./tools` folder of the repository:

- `domdecomp_runall_sequential.sh`: runs the code in all the domains sequentially (one after the other)
- `domdecomp_runall_concurrent_slurm.sh`: sends _d_ jobs to a SLURM queue, each of one corresponding to one of the subdomains, which will be executed either sequentially, concurrently or mixed depending on the capabilities of your cluster. 

After all the domains have finished for a given snapshot (or for many of them), you can run the `merge_domdecomp_catalogues.py` script to merge the results of the different domains into a single file. Again, in this script you need to set the iterations you want to analise, some units, the number of divisions and the overlap length between domains, as well as some flags regarding which outputs of ASOHF you have produced.

The code will read the outputs of all domains, and will unify the catalogues of haloes, stellar haloes and the particles lists (if any), and will write the unified files into `./output_files`.

### Merger tree


