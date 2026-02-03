# Urban-PLUMBER Single-Site Test (HRLDAS v5.1.1 + Urban-NbS)

This repository provides an **offline-coupled Urban Nature-based Solutions (Urban-NbS) module** integrated into **HRLDAS v5.1.1** for **single-site (column) evaluation** using the **Urban-PLUMBER** dataset.  
Development is based on `module_sf_urban_hrldasv511nbs_20260125.F`, which compiles successfully in offline mode. This repo includes additional modifications to streamline **site-specific configuration** and **forcing preparation** for Urban-PLUMBER experiments.

---

## 1. Purpose of this developer branch

Urban-PLUMBER sites exhibit substantial heterogeneity in **urban fraction**, **canyon morphology**, and **tree geometry**. For offline single-site testing, it is often preferable to **customize parameters per site** (e.g., via separate `URBPARM.TBL` files) rather than enforcing a single global configuration.

In addition, several urban parameters can be handled efficiently in offline point tests without extensive HRLDAS/WRF I/O changes. For example, **urban LAI** (`LAI_TREE`, `LAI_VEG`) and related parameters (e.g., `FVG`) can be treated as **prescribed constants** or **site-specific time-varying inputs** through the forcing pipeline.

---

## 2. Support for time-varying urban LAI forcing

This branch supports time-varying urban leaf area index (LAI) through two newly introduced forcing variables:

- `LAI_TREE` — leaf area index for urban street trees  
- `LAI_VEG`  — leaf area index for urban vegetated ground

These variables are provided through HRLDAS forcing files (generated from `sitename.data`). In this development version, the `LAI_TREE` and `LAI_VEG` values previously read from `URBPARM.TBL` have been **commented out** in the urban module so that **forcing values take precedence**.

### Key modified modules

- `module_hrldas_netcdf_io.F`  
  Connects/assigns forcing stream values (e.g., `forcing_name_LAIT`) to internal model variables.

- `module_NoahMP_hrldas_driver.F`  
  In `READFORC_HRLDAS`, reads the new LAI variables and passes them through the Noah-MP driver (including the urban column).

- `NoahmpIOVarType.F`  
  Allocates and defines the newly added LAI variables.

- `NoahmpIOVarInitMod.F`  
  Sets initial values for the newly added LAI variables.

- `NoahmpUrbanDriverMainMod.F`  
  Passes the new LAI variables into the Noah-MP urban column (ensuring grid/column-level consistency).

> **Note (point test only):** You also need to modify `create_point_data.F` to read and write the new LAI fields into the forcing workflow.

---

## 3. How to run the single-site test

### Step 1 — Configure site parameters
Ensure `URBPARM.TBL` and `namelist.hrldas` are customized for the target site, including:
- simulation start/end time  
- forcing timestep  
- site urban characteristics (urban fraction, canyon morphology, tree geometry, etc.)

### Step 2 — Select the correct site forcing source
In `create_point_data.F`, confirm the correct `site.data` file is opened and parsed for the selected Urban-PLUMBER site.

### Step 3 — Generate forcing and run HRLDAS
1. Generate forcing:
   - Run `./create_forcing.exe`
2. Organize forcing:
   - Move generated `*_LDASIN_DOMAIN1` files into the `input_forcing/` directory
3. Run HRLDAS:
   - Go to `hrldas/run/`
   - Confirm `namelist.hrldas` is properly set
   - Run `./hrldas.exe`

> To save disk space in point tests, restart files can be removed if you do not need restarts for subsequent runs.

---

## 4. Experimental design

Three idealized urban configurations are designed to isolate and quantify the role of **urban NbS** in the urban canopy:

### (1) Dry canyon (baseline)
This configuration corresponds to the **default SLUCM** representation, where the urban canyon is assumed **completely dry**.

### (2) Fully vegetated canyon
Each site is treated as a fully urbanized grid cell:
- set `urban fraction = 1` in `URBPARM.TBL`  
- explicitly represent **trees** and **ground vegetation** within the urban canyon using the **Urban-NbS** module

This experiment isolates the maximum potential influence of urban vegetation under idealized fully-urban conditions.

### (3) Fractional urban canyon
The observed/site-metadata **urban fraction** is explicitly accounted for:
- the **Urban-NbS** module is applied **only to the urban portion** of the grid cell  
- the remaining **non-urban fraction** is simulated using the default **Noah-MP** land surface formulation

This configuration is intended to approximate realistic mixed urban–nonurban grid cells.

---

## Notes / conventions

- This repository is intended for **offline HRLDAS single-site evaluation** using Urban-PLUMBER forcing/metadata workflows.
- Site-specific customization is handled primarily via `URBPARM.TBL`, `namelist.hrldas`, and `site.data` (forcing generation).

---
