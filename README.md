
**Minimal example:**
```tcl
#%Module1.0
proc ModulesHelp { } {
    puts stderr "\tThis module sets up the environment for SWAT+"
    puts stderr "\tVersion 2025.03 built with Intel compilers on Lengau."
    puts stderr "\n\tDependencies:"
    puts stderr "\t- Intel Parallel Studio XE 2018 Update 2"
    puts stderr "\n"
}
module-whatis "Name: SWAT+"
module-whatis "Version: 2025.03"
module load chpc/parallel_studio_xe/18.0.2/2018.2.046
set install_root "/home/apps/chpc/earth/swatplus-2025.03"
set exec_path "$install_root/exec"
prepend-path PATH $exec_path
setenv SWATPLUS_ROOT $install_root
setenv SWATPLUS_EXEC $exec_path
setenv FC "ifort"
setenv FCFLAGS "-O3 -xHost"
```

**Load the module:**
```bash
module use /apps/chpc/scripts/modules
module load earth/swatplus/2025.03
```

---

## Usage

After loading the module, you can run SWAT+ from anywhere:
```bash
swat
```

---

## PBS Pro Job Script Example

```bash
#!/bin/bash
#PBS -N swatplus
#PBS -l walltime=12:00:00
#PBS -l select=1:ncpus=1:mpiprocs=1
#PBS -q normal
#PBS -P PROJ_ID

cd $PBS_O_WORKDIR
module load earth/swatplus/2025.03
swat
```

---

## Troubleshooting
- **swat: command not found**
    - Ensure the module is loaded (`module list` should show `earth/swatplus/2025.03`).
    - Check that `/home/apps/chpc/earth/swatplus-2025.03/exec` is in your `$PATH`.
    - Confirm the `swat` executable exists in the `exec` directory and is executable.
- **Module load errors**
    - Remove any `if { [module-info mode] ... }` blocks from your module file if you see Tcl errors.
    - Use the `ModulesHelp` section for help text and job script examples.
- **Conflicting environments**
    - Deactivate any conda or virtual environments before loading the module.

---

## References
- [SWAT+ Official Website](https://swat.tamu.edu/)
- [swat_hpc GitHub Repository](https://github.com/changliao1025/swat_hpc)
- [PBS Pro Documentation](https://www.altair.com/pbs-works/)

---

**Maintainer:** Mthetho Vuyo Sovara
