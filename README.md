# Physical Design using OpenLANE/Sky130
## This repository contains all the information studied and created during the [Advanced Physical Design Using OpenLANE / SKY130 workshop](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/). It is primarily foucused on a complete RTL2GDS flow using the open-soucre flow named OpenLANE. [PICORV32A](https://github.com/YosysHQ/picorv32) RISC-V core design is used for the purpose.

# Table of Contents
* ## Introduction To RTL to GDSII Flow
* ## About Google SkyWater PDK
* ## Tools Used
* ## 1 – Inception of open-source EDA, OpenLANE and Sky130 PDK    
       How to talk to computers
      . SoC design and OpenLANE
      . Starting RISC-V SoC Reference design
      . Get familiar to open-source EDA tools
## 2 - Understand importance of good floorplan vs bad floorplan and introduction to library cells
      Chip Floor planning considerations
      Library Binding and Placement
      Cell design and characterization flows
      General timing characterization parameters
## 3 - Design and characterize one library cell using Magic Layout tool and ngspice
      Labs for CMOS inverter ngspice simulations
      Inception of Layout – CMOS fabrication process
      Sky130 Tech File Labs
## 4 - Pre-layout timing analysis and importance of good clock tree
      Timing modelling using delay tables
      Timing analysis with ideal clocks using openSTA
      Clock tree synthesis TritonCTS and signal integrity
      Timing analysis with real clocks using openSTA
## 5 - Final steps for RTL2GDS
      Routing and design rule check (DRC)
      PNR interactive flow tutorial
## Refrences
## Acknowledgement

# Introduction To RTL to GDSII Flow

RTL to GDSII Flow refers to the all the steps involved in converting a logical Register Transfer Level(RTL) Design to a fabrication ready GDSII format. GDSII is a database file format which is an industry standard for data exchange of IC layout artwork. The RTL to GSDII flow consists of following steps:

* RTL Synthesis
* Static Timing Analysis(STA)
* Design for Testability(DFT)
* Floorplanning
* Placement
* Clock Tree Synthesis(CTS)
* Routing
* GDSII Streaming

All the steps are further discussed in details in the repository.

# About Google SkyWater PDK
Google and SkyWater Technology Foundry in collaboration have released a completely open-source Process Design Kit(PDK) in May, 2020. The current release target to a SKY130 (i.e. 130 nm) process node is available as SkyWater Open Source PDK. The PDK provides Physical VLSI Designer with a wide range of flexibility in design choices. All the designs and simulations listed in this repository are carried out using the same SkyWater Open Source PDK.

# Tools Used

     | Name of Tool| Application / Usage|
     | Yosys|Synthesis of RTL Design|
     | ABC|Mapping of Netlist|
     |OpenSTA| Static Timing Analysis|
     |OpenROAD|Floorplanning, Placement, CTS, Optimization, Routing|
     |TritonRoute|Detailed Routing|
     |Magic VLSI|Layout Tool|
     |NGSPICE|SPICE Extraction and Simulation|
     |SPEF_EXTRACTOR|Generation of SPEF file from DEF file|

# 1 – Inception of open-source EDA, OpenLANE and Sky130 PDK  


