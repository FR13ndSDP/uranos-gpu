# References

De Vanna, F., Avanzi, F., Cogo, M., Sandrin, S., Bettencourt, M., Picano, F., Benini, E. (2022) URANOS: a GPU accelerated Navier-Stokes solver for compressible wall-bounded flows, under review Computer Physics Communications.



# Compiling

URANOS requires (1) a Fortran compiler and (2) an MPI library. For the GPU version, the NVIDIA compiler. A Makefile with
predefined settings is available to facilitate compiling.
Different compiling modes can be selected by changing "make" options. The following is the syntax

```
make -j comp="option1" mode="option2"
```

`comp` currently supports these choices:
- `gnu`: GNU compiler
- `gnuch`: GNU compiler in CH version
- `intel`: INTEL compiler
- `pgi`: NVIDIA-PGI compiler

`"option2"` specifies the compiler options. Each compiler has dedicated options

Example:

```
make -j comp=intel model=debug
```

compiles the code with the intel compiler using the debugging flags.

# Running

URANOS can be executed with a standard MPI launcher, e.g. `mpirun`.
In addition two files have to specified:
* `file.dat`: file defining the physical and numerical setup of the simulation, to be customized
according to the desired needs. Examples of input.dat files are available in the `tests` folder.
* `restart.bin`: only required for restart a simulation from previous results (optional)

Thus, to run a simulation without restart, type, e.g.:
```
mpirun -np "number_of_procs" ./Uranos.exe path/to/file.dat
```
if you want to restart a the simulation from previous data just type:
```
mpirun -np "number_of_procs" ./Uranos.exe path/to/file.dat path/to/restart.bin
```

# Interpreting the file.dat file

`xmin` defines the x-left boundary of the domain

`xmax` defines the x-right boundary of the domain

`ymin` defines the y-left boundary of the domain

`ymax` defines the y-right boundary of the domain

`zmin` defines the z-left boundary of the domain

`zmax` defines the z-right boundary of the domain


`gridpoint_x` specifies the path/to/the/gridX/file

`gridpoint_y` specifies the path/to/the/gridY/file

`gridpoint_z` specifies the path/to/the/gridZ/file

`uniform` grid is readely available without requiring an external grid file

`dims` specifies the problem dimensions and could 2 or 3

`tmax` is the total simulation time (in code units). 

`itmax` is the maximum number of iterations

`nx`, `ny`, `nz` are the number of point along the X, Y, Z coordinates. 
Those must be consistent with input grids.

`cart_dims(1)`, `cart_dims(2)`, `cart_dims(3)` specify the number of procs along the
X, Y, and Z directions. Some problems (channel flow and boundary layer) split the domain only along X and Z, but not along Y. 

`CFL` is the Courant-Friedrichs-Lewy parameter. The implemented Runge-Kutta method is stable up to CFL = 1. For safaty margin keep less than 0.8. 

`Dt` is a fixed time step is you dont want adaptive time-stepping (logical\_CFL must be .false.)

`bc(1)` = boundary condition at the x-left side

`bc(2)` = boundary condition at the x-right side

`bc(3)` = boundary condition at the y-left side

`bc(4)` = boundary condition at the y-right side

`bc(5)` = boundary condition at the z-left side

`bc(6)` = boundary condition at the z-right side

bc\_module.f90 provide a list of several boundary conditions

`sponge(1)` activate a sponge zone for the x-left side
`sponge(2)` activate a sponge zone for the x-right side
`sponge(3)` activate a sponge zone for the y-left side
`sponge(4)` activate a sponge zone for the x-right side
`sponge(5)` activate a sponge zone for the z-left side
`sponge(6)` activate a sponge zone for the x-right side

`Trat` fix the wall-to-adiabatic temperature ratio (i.e., `Trat = 1` the wall is adibatic, `Trat < 1` the wall is cold, `Trat > 1` the wall is hot)

`inflow_profile` select the inflow profile (look at `inflow_module.f90`)
`smooth_inflow` provides a time gradually increasing inflow condition
`turb_inflow` adds turbulence at the inflow

`Reynolds` is the Reynolds number of the flow
`Mach` is the Mach number of the flow
`Prandtl` is the Mach number of the flow

`logical_CFL` is `.true.` for adaptive time-stepping, `.false.` for static time-step

`scheme` select the convective scheme among: 
- `energy_preserving`, central energy-stable scheme, not to be used with shocked flows
- `hybrid_wenoEP` WENO scheme hybridized with energy preserving
- `hybrid_tenoEP` TENO scheme hybridized with energy preserving
- `hybrid_tenoaEP` TENO-A scheme hybrided with energy preserving

`sensor` defines the shock-sensor among:
- `density` shock sensor based on density gradient
- `density_jump` shock sensor based on density jump in cell
- `ducros` shock sensor based on a modified version of Ducros sensor

`fd_order` defines the order of accurary of central schemes (2,4,6). Default 6
`weno_order` defines the order of accurary of weno schemes (3,5,7). Default 5

`LES` activates the SGS model
`sgs_model` select the  SGS model among: 
- `WALE`
- `Smagorinsky`
- `sigma_model`

`viscous` activates viscous flux computation

`itout` defines the output iteration of restart.bin files
`StOut` defines the output iteration of statistics.bin files
`StFlg` activates the output of statistics

`ic` defines the initial condition (see `src/ic_module.f90`)

`output_file_name` defines the prefix  of all output files

`data_dir` specifies the output directory






