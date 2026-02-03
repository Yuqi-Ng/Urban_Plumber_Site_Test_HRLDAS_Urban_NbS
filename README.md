# HRLDAS_Urban_NbS Development

This repository contains the offline-coupled Urban Nature-based Solutions (Urban-NbS) module integrated into HRLDAS v5.1.1. The development is based on the Single-Layer Urban Canopy Model (SLUCM; `module_sf_urban.F`) and aims to improve the representation of urban vegetation processes in offline HRLDAS simulations.

## 1. Urban Trees and Vegetated Ground in SLUCM

A physics-based representation of a single row of street trees and vegetated ground surfaces has been implemented within the SLUCM framework. The enhanced urban canopy model explicitly accounts for:

- Radiative transfer processes associated with urban trees (e.g., shading and transmission)
- Hydrological processes over vegetated ground, including interception and evapotranspiration
- Coupled interactions between vegetation, impervious surfaces, and the urban canyon atmosphere

These developments substantially improve the simulation of urban surface energy and water fluxes, particularly under warm-season and heatwave conditions.

## 2. Support for Urban LAI as Input Forcing

The model supports time-varying urban leaf area index (LAI) through two newly introduced variables:

- `LAI_TREE`: leaf area index for urban street trees  
- `LAI_VEG`: leaf area index for urban vegetated ground  

These variables can be prescribed using either:

- Static values from lookup tables (`.TBL` files), consistent with the default SLUCM configuration, or  
- Dynamic input forcing provided through HRLDAS forcing files, enabling time-varying vegetation phenology and scenario-based experiments.

This design allows flexible control of urban vegetation properties in offline HRLDAS simulations.

## Model Framework

- Land surface driver: HRLDAS v5.1.1 (offline mode)  
- Urban canopy model: Modified SLUCM (`module_sf_urban.F`)  
- Coupling strategy: Offline coupling via HRLDAS I/O and forcing framework  

## Status

The modified code compiles and runs successfully in offline HRLDAS mode. Ongoing work focuses on systematic evaluation using Urban-PLUMBER sites and further refinement of urban vegetation parameterizations.
