---
layout: default
title: Input data
nav_order: 3
---

## Input format

In this section, we describe the different ways of loading your particle data into ASOHF. In particular, we describe the general reader format, and how to modify it and create your custom reader subroutine.

### The general reader format

The general reader (subroutine `READ_PARTICLES_GENERAL` in the `reader.f` source file) assumes the data is contained in the file `./simulation/particlesXXXXX` where `XXXXX` is the iteration number. The file is a binary file with the following format, record by record:

- Redshift of the snapshot (single-precision real)
- Number of DM particles (integer), $N_{DM}$
- X position of the DM particles ($N_{DM}$ single-precision reals)
- Y position of the DM particles ($N_{DM}$ single-precision reals)
- Z position of the DM particles ($N_{DM}$ single-precision reals)
- Velocity component X of the DM particles ($N_{DM}$ single-precision reals)
- Velocity component Y of the DM particles ($N_{DM}$ single-precision reals)
- Velocity component Z of the DM particles ($N_{DM}$ single-precision reals)
- Mass of the DM particles ($N_{DM}$ single-precision reals)
- Unique ID of the DM particles ($N_{DM}$ integers)

If there are stars in the simulation (and it is so indicated in the parameters file; see [the section on parameters](set_parameters)), the following data is also read:

- Number of stellar particles (integer), $N_\mathrm{stellar}$
- X position of the stellar particles ($N_\mathrm{stellar}$ single-precision reals)
- Y position of the stellar particles ($N_\mathrm{stellar}$ single-precision reals)
- Z position of the stellar particles ($N_\mathrm{stellar}$ single-precision reals)
- Velocity component X of the stellar particles ($N_\mathrm{stellar}$ single-precision reals)
- Velocity component Y of the stellar particles ($N_\mathrm{stellar}$ single-precision reals)
- Velocity component Z of the stellar particles ($N_\mathrm{stellar}$ single-precision reals)
- Mass of the stellar particles ($N_\mathrm{stellar}$ single-precision reals)
- Unique ID of the stellar particles ($N_\mathrm{stellar}$ integers)

The units of length, velocity and mass can be freely specified by the user (see [the section on parameters](set_parameters)).

### Creating your custom reader subroutine
