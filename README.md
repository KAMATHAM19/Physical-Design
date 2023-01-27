# Physical Design using OpenLANE/Sky130
 This repository contains all the information studied and created during the [Advanced Physical Design Using OpenLANE / SKY130 workshop](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/). It is primarily foucused on a complete RTL2GDS flow using the open-soucre flow named OpenLANE. [PICORV32A](https://github.com/YosysHQ/picorv32) RISC-V core design is used for the purpose.
# Table of Contents
* [Introduction To RTL to GDSII Flow]()
* [About Google SkyWater PDK]()
* [Tools Used]()
* [1 – Inception of open-source EDA, OpenLANE and Sky130 PDK]()
       [How to talk to computers]()
       [SoC design and OpenLANE]()
       [Starting RISC-V SoC Reference design]()
       [Get familiar to open-source EDA tools]()
* [2 - Understand importance of good floorplan vs bad floorplan and introduction to library cells]()
      [Chip Floor planning considerations]()
      [Library Binding and Placement]()
      [Cell design and characterization flows]()
      [General timing characterization parameters]()
* [3 - Design and characterize one library cell using Magic Layout tool and ngspice]()
      [Labs for CMOS inverter ngspice simulations]()
      [Inception of Layout – CMOS fabrication process]()
      [Sky130 Tech File Labs]()
* [4 - Pre-layout timing analysis and importance of good clock tree]()
      [Timing modelling using delay tables]()
      [Timing analysis with ideal clocks using openSTA]()
      [Clock tree synthesis TritonCTS and signal integrity]()
      [Timing analysis with real clocks using openSTA]()
* [5 - Final steps for RTL2GDS]()
      [Routing and design rule check (DRC)]()
      [PNR interactive flow tutorial]()
* [Refrences]()
* [Acknowledgement]()

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
| tools | usage |
| --- | --- |
| Yosys | 	Synthesis of RTL Design|
| ABC	 | Mapping of Netlist|
| OpenSTA |	Static Timing Analysis 
|OpenROAD |	Floorplanning, Placement, CTS, Optimization, Routing |
| TritonRoute |	Detailed Routing |
| Magic VLSI |	Layout Tool |
| NGSPICE	SPICE | Extraction and Simulation |
 | SPEF_EXTRACTOR | Generation of SPEF file from DEF file |


# 1 – Inception of open-source EDA, OpenLANE and Sky130 PDK 


<img width="960" alt="1 1" src="https://user-images.githubusercontent.com/64173714/214944909-255ca00a-78aa-43cc-98f8-6c701c297ad7.png">



<img width="464" alt="1 2" src="https://user-images.githubusercontent.com/64173714/214944959-57131442-6928-4999-a67c-d5a54a985c91.png">


<img width="960" alt="1 3" src="https://user-images.githubusercontent.com/64173714/214944999-fd01574b-d9a6-4af4-9692-bed7e52e4a49.png">


<img width="960" alt="1 4 1" src="https://user-images.githubusercontent.com/64173714/214945822-4241db5d-b2e2-413e-b7ff-56cb99b32081.png">
<img width="394" alt="1 4 2" src="https://user-images.githubusercontent.com/64173714/214945868-880c6d5e-ed5e-40a2-a3a2-82a912e7aa4c.png">

<img width="960" alt="1 4 3" src="https://user-images.githubusercontent.com/64173714/214946115-dff3b1f8-dee3-4e51-b5ba-3365aac25eef.png">

1.5.1 - edit


<img width="347" alt="1 5" src="https://user-images.githubusercontent.com/64173714/214946138-df3423c3-9568-463a-85a7-4040dcf9cdbf.png">

# 2 - Understand importance of good floorplan vs bad floorplan and introduction to library cells



```
~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-01_19-20/results/floorplan$ magic -T /Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
<img width="959" alt="magic" src="https://user-images.githubusercontent.com/64173714/215211997-84becccc-887b-4875-bbdf-2714e85544c4.png">
