# Physical Design using OpenLANE/Sky130
 This repository contains all the information studied and created during the [Advanced Physical Design Using OpenLANE / SKY130 workshop](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/). It is primarily foucused on a complete RTL2GDS flow using the open-soucre flow named OpenLANE. [PICORV32A](https://github.com/YosysHQ/picorv32) RISC-V core design is used for the purpose.
# Table of Contents
* [Introduction To RTL to GDSII Flow](#introduction-to-rtl-to-gdsii-flow)
* [About Google SkyWater PDK](#about-google-skywater-pdk)
* [Tools Used](#)
* [1 – Inception of open-source EDA, OpenLANE and Sky130 PDK](#)
   - [How to talk to computers](#)
   - [SoC design and OpenLANE](#)
   - [Starting RISC-V SoC Reference design](#)
   - [Get familiar to open-source EDA tools](#)
* [2 - Understand importance of good floorplan vs bad floorplan and introduction to library cells](#)
   - [Chip Floor planning considerations](#)
   - [Library Binding and Placement](#)
   - [Cell design and characterization flows](#)
   - [General timing characterization parameters](#)
* [3 - Design and characterize one library cell using Magic Layout tool and ngspice](#)
   - [Labs for CMOS inverter ngspice simulations](#)
   - [Inception of Layout – CMOS fabrication process](#)
   - [Sky130 Tech File Labs](#)
* [4 - Pre-layout timing analysis and importance of good clock tree](#)
    - [Timing modelling using delay tables](#)
    - [Timing analysis with ideal clocks using openSTA](#)
    - [Clock tree synthesis TritonCTS and signal integrity](#)
    - [Timing analysis with real clocks using openSTA](#)
* [5 - Final steps for RTL2GDS](#)
    - [Routing and design rule check (DRC)](#)
    - [PNR interactive flow tutorial](#)
* [Refrences](#)
* [Acknowledgement](#)

<a name="introduction-to-rtl-to-gdsii-flow"></a>
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

<a name="about-google-skywater-pdk"></a>
# About Google SkyWater PDK
Google and SkyWater Technology Foundry in collaboration have released a completely open-source Process Design Kit(PDK) in May, 2020. The current release target to a SKY130 (i.e. 130 nm) process node is available as SkyWater Open Source PDK. The PDK provides Physical VLSI Designer with a wide range of flexibility in design choices. All the designs and simulations listed in this repository are carried out using the same SkyWater Open Source PDK.

<a name="about-google-skywater-pdk"></a>
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

<a name="about-google-skywater-pdk"></a>
# 1 – Inception of open-source EDA, OpenLANE and Sky130 PDK 

<a name="about-google-skywater-pdk"></a>
<img width="960" alt="1 1" src="https://user-images.githubusercontent.com/64173714/214944909-255ca00a-78aa-43cc-98f8-6c701c297ad7.png">


<img width="960" alt="1 2" src="https://user-images.githubusercontent.com/64173714/215261326-176160e1-e1c7-4dae-98eb-cb6686d01e46.png">




<img width="960" alt="1 3" src="https://user-images.githubusercontent.com/64173714/214944999-fd01574b-d9a6-4af4-9692-bed7e52e4a49.png">


<img width="960" alt="1 4 1" src="https://user-images.githubusercontent.com/64173714/214945822-4241db5d-b2e2-413e-b7ff-56cb99b32081.png"> - <img width="960" alt="1 4 2" src="https://user-images.githubusercontent.com/64173714/215261392-a170e3bf-30e5-43b1-9892-70e30dd15024.png">


<img width="960" alt="1 4 3" src="https://user-images.githubusercontent.com/64173714/214946115-dff3b1f8-dee3-4e51-b5ba-3365aac25eef.png">

1.5.1 - edit


<img width="347" alt="1 5" src="https://user-images.githubusercontent.com/64173714/214946138-df3423c3-9568-463a-85a7-4040dcf9cdbf.png">


<a name="about-google-skywater-pdk"></a>
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

<a name="about-google-skywater-pdk"></a>
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
* SPICE3 file created from sky130_inv.ext - technology: sky130A

.option scale=0.01u
.include ./libs/pshort.lib
.include ./libs/nshort.lib

* .subckt sky130_inv A Y VPWR VGND
M0 Y A VGND VGND nshort_model.0 ad=1435 pd=152 as=1365 ps=148 w=35 l=23
M1 Y A VPWR VPWR pshort_model.0 ad=1443 pd=152 as=1517 ps=156 w=37 l=23
C0 A VPWR 0.08fF
C1 Y VPWR 0.08fF
C2 A Y 0.02fF
C3 Y VGND 0.18fF
C4 VPWR VGND 0.74fF
* .ends

* Power supply 
VDD VPWR 0 3.3V 
VSS VGND 0 0V 

* Input Signal
Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)

* Simulation Control
.tran 1n 20n
.control
run
.endc
.end
```
<img width="960" alt="3 0" src="https://user-images.githubusercontent.com/64173714/215286609-f27e665b-6d2e-4769-bbc8-1af19275347c.png">


<img width="960" alt="t" src="https://user-images.githubusercontent.com/64173714/215286642-0a0bdfcf-2078-4b72-9234-6d7afe3d4a7e.png">


<img width="316" alt="rise" src="https://user-images.githubusercontent.com/64173714/215319377-e139cefc-a69f-4a70-96f1-9db85e05fc89.png">
<img width="307" alt="fall transi" src="https://user-images.githubusercontent.com/64173714/215319388-8c9b7de9-1e7a-4ed1-8563-925744289767.png">
<img width="308" alt="cell rise de" src="https://user-images.githubusercontent.com/64173714/215319395-2eac9a92-c094-4e40-bec6-e8fde0e86fb8.png">
<img width="277" alt="cll fall delay" src="https://user-images.githubusercontent.com/64173714/215319398-47e2f33e-82e5-4527-83cc-5721f3be5836.png">

# 4 - Pre-layout timing analysis and importance of good clock tree

~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign$ cd ../../pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd/

error
```
Design
set ::env(DESIGN_NAME) "picorv32a"

set ::env(VERILOG_FILES) "./designs/picorv32a/src/picorv32a.v"
set ::env(SDC_FILE) "./designs/picorv32a/src/picorv32a.sdc"
set ::env(CLOCK_PERIOD) "5.000"
set ::env(CLOCK_PORT) "clk"

set ::env(CLOCK_NET) $::env(CLOCK_PORT)

set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]

set ::env(FP_CORE_UTIL) 65
set ::env(FP_IO_VMETAL) 4
set ::env(FP_IO_HMETAL) 3

set filename $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl
if { [file exists $filename] == 1} {
source $filename
}
```
```
    set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
  
    add_lefs -src $lefs 
```

<img width="951" alt="5 1" src="https://user-images.githubusercontent.com/64173714/215319500-f3e895e5-cb8c-4a45-ad82-c21ac34d86c9.png">

<img width="960" alt="image" src="https://user-images.githubusercontent.com/64173714/215330222-4e0922a0-9da9-40dc-b2bb-71f9f984efdb.png">

```
set_cmd_units -time ns -capacitance pF -current mA -voltage V -resistance kOhm -distance um
read_liberty -max /home/venkykamatham1998/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib
read_liberty -min /home/venkykamatham1998/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib
read_verilog /home/venkykamatham1998/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/my_run1/results/synthesis/picorv32a.synthesis.v
link_design picorv32a
read_sdc /home/venkykamatham1998/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/my_base.sdc
report_checks -path_delay min_max -fields {slew trans net cap input_pin}
report_tns
report_wns
```
~              
% echo $::env(LIB_SYNTH_COMPLETE)
/openLANE_flow/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib
% echo $::env(LIB_TYPICAL)
/openLANE_flow/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib
% echo $::env(CURRENT_DEF)
/openLANE_flow/designs/picorv32a/runs/my_run1/results/cts/picorv32a.cts.def
% echo $::env(CTS_MAX_CAP)
1.53169
% echo $::env(CTS_CLK_BUFFER_LIST)
sky130_fd_sc_hd__clkbuf_1 sky130_fd_sc_hd__clkbuf_2 sky130_fd_sc_hd__clkbuf_4 sky130_fd_sc_hd__clkbuf_8
% echo $::env(CTS_ROOT_BUFFER)
sky130_fd_sc_hd__clkbuf_16

read_lef /openLANE_flow/designs/picorv32a/runs/my_run1/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/my_run1/results/cts/picorv32a.cts.def
read_verilog /openLANE_flow/designs/picorv32a/runs/my_run1/results/synthesis/picorv32a.synthesis_cts.v
 read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
report_checks -path_delay min_max -format full_clock_expanded -digits 4


