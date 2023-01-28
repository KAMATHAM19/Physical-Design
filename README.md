# Physical Design using OpenLANE/Sky130
 This repository contains all the information studied and created during the [Advanced Physical Design Using OpenLANE / SKY130 workshop](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/). It is primarily foucused on a complete RTL2GDS flow using the open-soucre flow named OpenLANE. [PICORV32A](https://github.com/YosysHQ/picorv32) RISC-V core design is used for the purpose.
# Table of Contents
* [Introduction To RTL to GDSII Flow]()
* [About Google SkyWater PDK]()
* [Tools Used]()
* [1 – Inception of open-source EDA, OpenLANE and Sky130 PDK]()
 * [How to talk to computers]()
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


<img width="960" alt="1 2" src="https://user-images.githubusercontent.com/64173714/215261326-176160e1-e1c7-4dae-98eb-cb6686d01e46.png">




<img width="960" alt="1 3" src="https://user-images.githubusercontent.com/64173714/214944999-fd01574b-d9a6-4af4-9692-bed7e52e4a49.png">


<img width="960" alt="1 4 1" src="https://user-images.githubusercontent.com/64173714/214945822-4241db5d-b2e2-413e-b7ff-56cb99b32081.png"> - <img width="960" alt="1 4 2" src="https://user-images.githubusercontent.com/64173714/215261392-a170e3bf-30e5-43b1-9892-70e30dd15024.png">


<img width="960" alt="1 4 3" src="https://user-images.githubusercontent.com/64173714/214946115-dff3b1f8-dee3-4e51-b5ba-3365aac25eef.png">

1.5.1 - edit


<img width="347" alt="1 5" src="https://user-images.githubusercontent.com/64173714/214946138-df3423c3-9568-463a-85a7-4040dcf9cdbf.png">

# 2 - Understand importance of good floorplan vs bad floorplan and introduction to library cells

command
<img width="953" alt="2 1" src="https://user-images.githubusercontent.com/64173714/215262087-ce4be0c1-be55-4835-9bb3-f12ae759249d.png">


```
 magic -T <location of techfile> lef read <loction of lef file> def read <location of floorplan def file>
 ```

```
~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-01_19-20/results/floorplan$

magic -T /home/venkykamatham1998/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &

```

<img width="959" alt="magic" src="https://user-images.githubusercontent.com/64173714/215261151-64df2adf-2cbd-45a1-8988-626328ad414e.png">

command
<img width="960" alt="p1" src="https://user-images.githubusercontent.com/64173714/215262016-617f0a68-ae1b-4670-8428-8e6e46931cc3.png">


```
 magic -T <location of techfile> lef read <loction of lef file> def read <location of placement def file>
 
```

```
~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-01_19-20/results/placement$

magic -T /home/venkykamatham1998/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &

```

<img width="956" alt="placement" src="https://user-images.githubusercontent.com/64173714/215261639-6099a3f9-0480-434b-ae2f-da66f1ac7be9.png">

# 3 - Design and characterize one library cell using Magic Layout tool and ngspice

L0 - ioPlacer - to changes I/O pins and place around the core

```
% set ::env(FP_IO_MODE) 2
2
% run_floorplan
```


<img width="960" alt="reset" src="https://user-images.githubusercontent.com/64173714/215265586-4619331d-3f95-4573-9765-2c9d07226e55.png">

```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git

```
cp sky130A.tech /home/venkykamatham1998/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/

 magic -T sky130A.tech sky130_inv.mag &


<img width="956" alt="image" src="https://user-images.githubusercontent.com/64173714/215268906-9ec922be-924e-447f-8758-a8d5eb418157.png">

16 mask process 
``` [](https://www.vlsisystemdesign.com/wp-content/uploads/2017/07/16-mask-process.pdf)  ```



<img width="959" alt="3 3" src="https://user-images.githubusercontent.com/64173714/215276740-9f6fd45c-da8c-476c-a30d-2a38baef0d08.png">



<img width="960" alt="3 4" src="https://user-images.githubusercontent.com/64173714/215278218-907c745b-a97a-4d7c-add9-a114193a6694.png">

<img width="960" alt="spice 1" src="https://user-images.githubusercontent.com/64173714/215278228-8ab8f44d-e61c-4da2-8699-f3011548a675.png">

```
i* SPICE3 file created from sky130_inv.ext - technology: sky130A

.option scale=0.01u
.include ./libs/pshort.lib
.include ./libs/nshort.lib

//.subckt sky130_inv A Y VPWR VGND
M1000 Y A VGND VGND pshort_model.0 ad=1443 pd=152 as=1517 ps=156 w=37 l=23
M1001 Y A VPWR VPWR nshort_model.0 ad=1435 pd=152 as=1365 ps=148 w=35 l=23
VDD VPWR 0 3.3V
VSS VGND 0 0V
Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)
C0 A Y 0.05fF
C1 Y VPWR 0.11fF
C2 A VPWR 0.07fF
C3 Y 0 0.24fF
C4 VPWR 0 0.59fF
// .ends

.tran 1n 20n

.control
run
.endc
.end
    
```
