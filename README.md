# SWAT+ on Lengau Cluster (CHPC South Africa) 🖥️🌍  

**Optimized workflows for running SWAT+ (Soil & Water Assessment Tool Plus) on the Centre for High-Performance Computing’s (CHPC) Lengau cluster.** This repository provides PBS Pro job scripts, configuration templates, and utilities for efficient large-scale watershed simulations.  

---

## Table of Contents  
1. [Key Features](#key-features-star)  
2. [Prerequisites](#prerequisites-warning)  
3. [Setup Guide](#setup-guide-gear)  
4. [Job Submission](#job-submission-with-pbs-pro-rocket)  
5. [Output Management](#output-management-file_folder)  
6. [Troubleshooting](#troubleshooting-mag)  
7. [References](#references-book)  
8. [License](#license-page_facing_up)  
9. [Maintainer](#maintainer)  

---

## Key Features :star:  
- **PBS Pro-Optimized Scripts**: Job scripts for multi-node SWAT+ runs  
- **Resource Scaling**: Templates for `select`, `mpiprocs`, and `walltime` directives  
- **Automated Post-Processing**: Python utilities for result aggregation  
- **Logging**: Integrated stdout/stderr capture for debugging  

---

## Prerequisites :warning:  
1. **Lengau Cluster Account**: Access granted by CHPC South Africa  
2. **Basic Knowledge**:  
   - PBS Pro commands (`qsub`, `qstat`, `qdel`)  
   - Linux shell navigation  
   - SWAT+ input file structure  

---

## Setup Guide :gear:  
### 1. Load SWAT+ Module
```bash
module use /apps/chpc/scripts/modules
module load earth/swatplus/2025.03
```
---

## 2. Required Input Files 📂
Watershed Configuration Files
   - ```main.sf```: Main input file (defines simulation period, output options, etc.)
   - ```watershed.sf```: Watershed structure (subbasins, HRUs, routing)
   - ```project.ini``` or ```project.json```: Project metadata and paths

Database Files
- ```swatplus_datasets.sqlite```: Core parameter database (soils, crops, fertilizers, etc.)
- ```project_db.sqlite```: Project-specific database (created during setup)

Climate/Weather Data
- ```precip.pcp```: Precipitation data
- ```temp.tmp```: Temperature data
- ```humidity.hmd```: Relative humidity
(*Additional: solar.slr, wind.wnd if required*)

Management Files
- ```management.sch```: Crop rotation schedules
- ```tillage.til```: Tillage operations
- ```fertilizer.frt```: Fertilizer applications

Spatial Data (Optional but recommended)
- Digital Elevation Model (DEM)
- Land Use/Land Cover (LULC) raster
- Soil type raster

---

## 3. Project Structure
SWAT+ Watershed Project Structure

```text
my_watershed/
├── input/
│   ├── main.sf
│   ├── watershed.sf
│   ├── project.ini
│   ├── swatplus_datasets.sqlite
│   ├── precip.pcp
│   ├── temp.tmp
│   ├── management/
│   │   ├── management.sch
│   │   ├── tillage.til
│   └── spatial/          # Optional rasters
│       ├── dem.tif
│       ├── landuse.tif
├── run_swatplus.pbs      # PBS Pro job script
└── output/               # Auto-created during run
```
---

## 4. Configuration Notes
Database Setup
- Generate swatplus_datasets.sqlite using SWAT+ Editor
- Validate parameter units match your watershed

Climate Data Formatting
Example ```precip.pcp``` header:
```text
nbyr   tstep   lat   lon   elev
30     1       -29.6  24.5  1200
19800101 0.0
19800102 5.2
```

Path Consistency
In ```project.ini```:
```ini
[files]
watershed = input/watershed.sf
database = input/swatplus_datasets.sqlite
```

Lengau-Specific Tips
   - Store large datasets in ```/lustre/<project>```
   - Compress text files with ```gzip``` before transfer
   - Use ```rsync``` for efficient data transfers:
     ```bash
     rsync -avz ./input/ user@lengau.chpc.ac.za:/path/to/project/input/
     ```
---

## 2. Job Submission with PBS Pro :rocket: 
PBS Script (```run_swatplus.pbs```)
```bash
#!/bin/bash -l  
#PBS -A <project>                  # CHPC project account (REQUIRED)
#PBS -l select=4:mpiprocs=24       # 4 nodes, 24 cores/node  
#PBS -l walltime=06:00:00          # Max runtime  
#PBS -N swatplus_sim               # Job name  
#PBS -j oe                         # Merge stdout/stderr  
#PBS -m e                          # Email on job end  
#PBS -M <email>@domain.com  

# Load modules
module use /apps/chpc/scripts/modules
module load earth/swatplus/2025.03

# Navigate to project directory  
cd $PBS_O_WORKDIR  

# Run SWAT+ (MPI)  
mpirun -np 96 swatplus_exe 
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

Got issues, submit a CHPC Helpdesk ticket: https://users.chpc.ac.za/helpdesk/tickets/submit/
