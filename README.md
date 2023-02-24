# Physical Design using OpenLANE/Sky130

During the [Advanced Physical Design workshop using OpenLANE and SKY130](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/), I learned about the entire Physical Design process. This training focused on an RTL2GDSII flow using the open-source flow OpenLANE, with the [PICORV32A](https://github.com/YosysHQ/picorv32) RISC-V core design as an example. I learned about chip floorplanning, library cell design and characterization, pre-layout timing analysis, and the final steps of the RTL2GDSII flow.This repository contains all of the information and resources I have acquired during the workshop.

# Table of Contents
* [Tools Used](#tools-used)
* 1 – Inception of open-source EDA, OpenLANE and Sky130 PDK
   - How to talk to computers
      - IC Terminologies
      - Introduction to RISC-V
      - Software applications to hardware
   - SoC design and OpenLANE
   - Simplified RTL to GDSII Flow
       - About Openlane flow
   - Get familiar to open-source EDA tools
* 2 - Understand importance of good floorplan vs bad floorplan and introduction to library cells
   - Chip Floor planning considerations
   - Library Binding and Placement
   - Cell design and characterization flows
   - General timing characterization parameters
* 3 - Design and characterize one library cell using Magic Layout tool and ngspice
   - Labs for CMOS inverter ngspice simulations
   - Inception of Layout – CMOS fabrication process
   - Sky130 Tech File Labs
* 4 - Pre-layout timing analysis and importance of good clock tree
    - Timing modelling using delay tables
    - Timing analysis with ideal clocks using openSTA
    - Clock tree synthesis TritonCTS and signal integrity
    - Timing analysis with real clocks using openSTA
* 5 - Final steps for RTL2GDS
    - Routing and design rule check (DRC)
    -  PNR interactive flow tutorial
* Acknowledgement

<a name="tools-used"></a>
# Tools Used
| Tool | Descriptions |
| --- | --- |
| Yosys | 	Synthesis of RTL Design|
| ABC	 | Mapping of Netlist|
| OpenSTA |	Static Timing Analysis |
| Magic VLSI |	Layout Tool |
| NGSPICE	SPICE | Extraction and Simulation |
|OpenROAD |	Floorplanning, Placement, CTS, Optimization, Routing |
| TritonRoute |	Detailed Routing |
| SPEF_EXTRACTOR | Generation of SPEF file from DEF file |

<a name="1-inception-of-open-source-eda-openlane-and-sky130-pdk"></a>
# 1 – Inception of open-source EDA, OpenLANE and Sky130 PDK 

<a name="how-to-talk-computers-ic-terminologies"></a>
## How to talk to computers 
### IC Terminologies
In RTL2GDS physical design, there are various terms encountered. A few key terms are:

<img width="541" alt="1" src="https://user-images.githubusercontent.com/64173714/215434903-d1b261d0-044d-4649-9502-4dba67ab1fe4.png">

* Package: a material that houses the semiconductor device and protects it from damage. An example is QFN-48 (Quad Flat No-Leads) with 48 pins.
* Chip: sits in the center of the package and connects to the package pins through wire bonding. It contains components like pad, core, interconnects, etc.
* Pads: connect internal signals from the core of the IC to the external pins of the chip. They are organized as a Pad Frame and include input, output, power supply, and ground pads.
* Die: the block of semiconducting material used to build functional circuits, the entire size of the chip. 
* Core: contains all the logic units (gates, muxes, etc.) to execute instructions and produce output.
The core can have two types of blocks: Foundry IP blocks (e.g. ADC, DAC, PLL, SRAM) designed by foundries, and Macro blocks (e.g. RISC-V SOC, SPI) pure digital logic blocks.


<img width="535" alt="2" src="https://user-images.githubusercontent.com/64173714/215442738-27656f59-dc42-4fd5-8205-55c3e3e5c8c2.png">

### Introduction to RISC-V
RISC-V (Reduced Instruction Set Computing) is an open-source Instruction Set Architecture (ISA) for computer processors. RISC-V provides a clean and simple ISA, allowing for efficient implementations of processors, while also providing flexibility for custom extensions. The open-source nature of RISC-V allows for easy collaboration and customization, enabling the development of diverse, specialized processors for a wide range of applications. This has led to a growing number of companies and institutions adopting RISC-V for their processors, contributing to its growing popularity and success.


![IMG-8769](https://user-images.githubusercontent.com/64173714/215443267-3402518b-6809-4ca5-a753-1d2737d006da.jpg)

### Software applications to hardware
When we run a program on a computer, it goes through several steps to become executable by the hardware. Firstly, the program written in a high level language (such as C) is compiled into assembly language, which is a representation of the program in a language that is specific to the hardware. The assembly language is then converted into machine language, which consists of binary code that can be executed by the hardware.

The system software, which includes the Operating System (OS), Compiler, and Assembler, plays a crucial role in executing the program. The OS handles low-level functions such as input/output operations and memory management. The compiler compiles the program into assembly language, and the assembler then converts the assembly language into machine language. 

Finally, the binary code is executed on the chip layout. The interface between the chip layout and the machine language is achieved through RTL (register transfer level) code. This code provides a description of the digital logic that is used to implement the desired behavior in hardware.

## SoC design and OpenLANE

<img width="593" alt="3" src="https://user-images.githubusercontent.com/64173714/215445891-5f004c86-c8c8-4ba8-bdce-976a7e8f7f68.png">

To design a digital ASIC with open source, three open source components are needed: 
* RTL Designs (found on github.com, librecores.org, and opencores.org)
* EDA Tools (such as OpenROAD, OpenLANE, and QFlow), 
* PDK (Process Design Kit) - The PDK is provided by Google and Skywater, and it is a 130nm Production PDK. The PDK acts as a bridge between the designer and the fab and contains important information such as cell libraries, IO libraries, and design rules (DRC, LVS, etc.).

## Simplified RTL to GDSII Flow

![IMG-8761](https://user-images.githubusercontent.com/64173714/215446489-bdfd9f74-92c4-40db-bb70-744d06a18289.jpg)

In the process of digital ASIC design
* Synthesis is the step where the RTL code is transformed into a gate level netlist composed of components from a standard cell library. 
* Floor planning and power planning involves allocating the silicon area and creating a robust power distribution network. The power network often employs thicker upper metal layers to reduce resistance and IR drop problems.
* Placement is a two-part process, starting with global placement to find the optimal positions for cells which may not be legal, followed by detailed placement to determine the actual legal positions of cells.
* Clock tree synthesis is the distribution of clocks to all flip flops, often structured as a tree such as an H-tree or X-tree.
* Routing involves connecting cells together using horizontal and vertical wires. The router uses information from the PDK such as thickness, pitch, width, and vias for each metal layer, with Sky130 defining 6 routing layers for both global and detailed routing.
* Before sign-off, verification is crucial and includes physical verification such as DRC and LVS, and timing verification. Design Rule Checking (DRC) ensures the final layout complies with all design rules, Layout versus Schematic (LVS) checks if the final layout matches the gate level netlist from the synthesis phase, and timing verification confirms that timing constraints are met.

### About Openlane flow
![IMG-8762](https://user-images.githubusercontent.com/64173714/215446664-5d9da8cd-d538-4c7e-9585-98f393586e6d.jpg)
OpenLANE is a flow that employs various open-source tools to design digital circuits from RTL to GDSII. It features the striVe family of open everything SoCs (Open PDK, Open EDA, Open RTL) and utilizes tools such as Yosys, OpenROAD, Magic, Netgen, and SPEF_Extraction. The flow has two modes of operation: Autonomous and Interactive, and is specifically optimized for the SKYWater 130nm open PDK.

The flow starts with RTL synthesis, where the RTL code is processed by Yosys and translated into a logic circuit using generic components. The circuit can then be optimized and mapped to a standard cell library using the ABC tool, guided by various ABC scripts that implement different synthesis strategies.

OpenLANE also includes design exploration utilities that allow the designer to test different configurations of the design and generate reports that show the number of layout violations. The flow also includes Static Timing Analysis performed by OpenSTA, as well as optional testing steps such as DFT (Scan Insertion, ATPG, Test Pattern Compaction, Fault Coverage, and Fault Simulation).

The physical implementation phase is handled by the OpenROAD application, which performs PnR (FP+PP, Placement, Optimization, CTS, and Routing). The TritonRoute tool is used for detailed routing, and a Logic Equivalence Check (LEC) is performed to ensure the optimized circuit still has equivalent functionality. The fake antenna insertion step is also included to address antenna rule violations, with the Magic tool being used for antenna checking and NetGen for circuit extraction.

Finally, the sign-off stage includes STA, DRC, and LVS, with interconnect RC extraction and further STA performed by OpenSTA, and DRC and LVS performed by the Magic tool.

<a name="about-google-skywater-pdk"></a>

## Get familiar to open-source EDA tools

* Getting starting with working directory and openlane
` cd work/tools/openlane_working_dir/openlane/ `
* to invoke the tool type `docker` to start the docker containter

<img width="960" alt="1 1" src="https://user-images.githubusercontent.com/64173714/214944909-255ca00a-78aa-43cc-98f8-6c701c297ad7.png">

* Open the OpenLane in interactive mode `./flow.tcl -interactive` and Set the package required by OpenLane `package require openlane 0.9`

<img width="960" alt="1 2" src="https://user-images.githubusercontent.com/64173714/215261326-176160e1-e1c7-4dae-98eb-cb6686d01e46.png">

* select the design `cd designs/picorv32a`

<img width="960" alt="1 3" src="https://user-images.githubusercontent.com/64173714/214944999-fd01574b-d9a6-4af4-9692-bed7e52e4a49.png">

* Prepare the design `prep -design picorv32a`


<img width="960" alt="1 4 1" src="https://user-images.githubusercontent.com/64173714/214945822-4241db5d-b2e2-413e-b7ff-56cb99b32081.png"> 
<img width="960" alt="1 4 2" src="https://user-images.githubusercontent.com/64173714/215261392-a170e3bf-30e5-43b1-9892-70e30dd15024.png">

* checking whether a merged file is created in the folder

<img width="960" alt="1 4 3" src="https://user-images.githubusercontent.com/64173714/214946115-dff3b1f8-dee3-4e51-b5ba-3365aac25eef.png">

* Run the synthesis `run_synthesis`

<img width="347" alt="1 5" src="https://user-images.githubusercontent.com/64173714/214946138-df3423c3-9568-463a-85a7-4040dcf9cdbf.png">

* View the synthesis statistics

<img width="960" alt="1 5 1" src="https://user-images.githubusercontent.com/64173714/215454952-d39c22e8-8706-4853-8c1d-c35dd94a9e04.png">

* To calculate the flop ratio 
 ` flop ration = no of d flip flops/total no of cells = 1613/18036 = 0.089 = 89% `
 
 
<a name="about-google-skywater-pdk"></a>
# 2 - Understand importance of good floorplan vs bad floorplan and introduction to library cells

### LABS 
* floorplan in openlane `run_floorplan`

<img width="953" alt="2 1" src="https://user-images.githubusercontent.com/64173714/215262087-ce4be0c1-be55-4835-9bb3-f12ae759249d.png">

The `picorv32a.floorplan.def` file was generated in the `./results/floorplan` directory by this command.

* We use the Magic tool to view the floorplan, and we need three files to do so:
   * Magic Tech file : sky130A.tech
   * LEF file : merged.lef
   * Def file of floorplan : picorv32a.floorplan.def

```
 magic -T <location of techfile> lef read <loction of lef file> def read <location of floorplan def file>
 ```
```
~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-01_19-20/results/floorplan$
magic -T /home/venkykamatham1998/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
The above command can be used to view the floorplan in the Magic tool.

<img width="959" alt="magic" src="https://user-images.githubusercontent.com/64173714/215261151-64df2adf-2cbd-45a1-8988-626328ad414e.png">

* placement in openlane `run_placement`

<img width="960" alt="p1" src="https://user-images.githubusercontent.com/64173714/215262016-617f0a68-ae1b-4670-8428-8e6e46931cc3.png">

The `picorv32a.placement.def` file was generated in the `./results/placement` directory by this command.
```
 magic -T <location of techfile> lef read <loction of lef file> def read <location of placement def file>
 
```

```
~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-01_19-20/results/placement$
magic -T /home/venkykamatham1998/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &

```

<img width="956" alt="placement" src="https://user-images.githubusercontent.com/64173714/215261639-6099a3f9-0480-434b-ae2f-da66f1ac7be9.png">

### Standard Cell Design
The typical standard cell design flow consists of 3 elements :
1. Inputs - PDKs , DRC & LVS rules, SPICE models, library & user-defined specs
2. Design Steps - Circuit design, layout design, characterization
3. Outputs - CDL(circuit description lanuage), GDSII, LEF, extracted spice netlist(.crc)
<a name="about-google-skywater-pdk"></a>
# 3 - Design and characterize one library cell using Magic Layout tool and ngspice

### Labs
#### ioPlacer - to changes I/O pins and place around the core

```
% set ::env(FP_IO_MODE) 2
2
% run_floorplan
```
To see how I/O pins are aligned after changing the value in ioPlacer 

<img width="960" alt="reset" src="https://user-images.githubusercontent.com/64173714/215265586-4619331d-3f95-4573-9765-2c9d07226e55.png">

Spice Simulations (Pre-Layout)
The spice simulations mainly consist of :
1. Spice deck - component connectivity, component values, identify nodes, name nodes
2. NGspice introduction
3. Static behaviour evaluation 

## SPICE simulation lab for CMOS inverter

* At first we need to git clone the standard cells and command is :
```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git

```
* After cloning the repo, navigate to the vsdstdcelldesign directory and use magic to view the sky130_inv.mag file.
* Before opening we need magic tech file and copy the tech file in vsdstdcelldesign directority
```
     cp sky130A.tech /home/venkykamatham1998/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/
```
 * To view the layout use this command 
 ```
     magic -T sky130A.tech sky130_inv.mag &
```
<img width="956" alt="image" src="https://user-images.githubusercontent.com/64173714/215268906-9ec922be-924e-447f-8758-a8d5eb418157.png">

16 mask CMOS fabrication process  
``` 
https://www.vlsisystemdesign.com/wp-content/uploads/2017/07/16-mask-process.pdf

```
* To view the layout press `s` for select, `z` for zoom and in `tkconl` type what cammand to see the mask layers 

<img width="959" alt="3 3" src="https://user-images.githubusercontent.com/64173714/215276740-9f6fd45c-da8c-476c-a30d-2a38baef0d08.png">

* We should extract the parasitics and characterise the design. We open the tkcon window and execute the following commands:
```
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```
The extracted file 

<img width="960" alt="3 4" src="https://user-images.githubusercontent.com/64173714/215278218-907c745b-a97a-4d7c-add9-a114193a6694.png">

<img width="960" alt="spice 1" src="https://user-images.githubusercontent.com/64173714/215278228-8ab8f44d-e61c-4da2-8699-f3011548a675.png">

We then modify the spice file so that we can plot a transient response:
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
* Open the spice file by typing `ngspice sky130A_inv.spice` 
* Generate a graph using `plot y vs time a` 

<img width="960" alt="3 0" src="https://user-images.githubusercontent.com/64173714/215286609-f27e665b-6d2e-4769-bbc8-1af19275347c.png">
<img width="960" alt="t" src="https://user-images.githubusercontent.com/64173714/215286642-0a0bdfcf-2078-4b72-9234-6d7afe3d4a7e.png">
* From the transient response, we will now characterise the cell's slew rate and propagation delay

   * rise transiton - time taken by output waveform to transit from 20%(0.66) to 80%(2.64) of VDD(3.3 max value) = 2.19945 - 2.15722 = 0.03728 ns
   
<img width="316" alt="rise" src="https://user-images.githubusercontent.com/64173714/215319377-e139cefc-a69f-4a70-96f1-9db85e05fc89.png">

   * fall transition - time taken by output waveform to transit from 80% (2.64) to 20% (0.66) of VDD = 4.06716 - 4.0394 = 0.02766 ns
   
<img width="307" alt="fall transi" src="https://user-images.githubusercontent.com/64173714/215319388-8c9b7de9-1e7a-4ed1-8563-925744289767.png">

   *  rise cell delay - The difference between the time when output as well as input is at 50% (1.65) i.e falling at 50% of output is rising = 2.18132 - 2.14945 = 0.03187 ns
   
<img width="308" alt="cell rise de" src="https://user-images.githubusercontent.com/64173714/215319395-2eac9a92-c094-4e40-bec6-e8fde0e86fb8.png">

   *  fall cell delay - The difference between the time when output as well as input is at 50% (1.65) i.e falling at 50% of input is rising = 4.059292 - 4.04958 = 0.009712 ns 
   
<img width="277" alt="cll fall delay" src="https://user-images.githubusercontent.com/64173714/215319398-47e2f33e-82e5-4527-83cc-5721f3be5836.png">

# 4 - Pre-layout timing analysis and importance of good clock tree

## LAB

* OpenLANE is a tool for place and route, which places cells in a design. When using this tool, the full information contained in the .mag file, which includes boundry,power and ground, logic, etc., is not necessary. Instead, only the essential information, such as the placement boundary, power and ground rails, and input and output ports, is required. This information is contained in lef files, which protect the IP(intellectual property). 
* The goal is to extract the lef file from the .mag file and use it to replace the standard cells in the design. There are guidelines to follow when creating a standard cell set for use in place and route
   * I/p and O/p port must lies on the intersection of the vertical and horizontal tracks
   * Width oof the std cell should be in the odd ultiples of the track pitch
   * Height should be odd multiples of vertical track pitch
   
  * To run the previous flow follow this command
 ```
   prep -design picorv32a -tag
 
 ```
 * In these directory `/pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd/` we can find tracks
 ```
 ~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign$ cd ../../pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd/
 
 ```
 * tpye command ` less tracks.info`
 ```
 less tracks.info
 
 ```
 <img width="956" alt="4 1" src="https://user-images.githubusercontent.com/64173714/215493780-f81c412d-a0eb-49a8-b4a7-ce6e5fd2cd34.png">
 
 * open the layout from vsdstdcelldesign directory
 
 ``` 
    magic -T sky130.tech sky130_inv.mag
 
 ```
In the tkon terminal, use the grid command to compare the tracks' information

<img width="960" alt="4 2" src="https://user-images.githubusercontent.com/64173714/215494843-c069b012-c948-4dd9-9164-d43678a4d5cc.png">

The three guidelines are clearly shown in the illustration in this image

<img width="958" alt="4 3" src="https://user-images.githubusercontent.com/64173714/215496521-f3f79ae8-e302-4155-8946-244049d897dd.png">

* The information from the .mag file will be extracted into a LEF file, which will contain essential details for placement and routing, such as cell dimensions, port information, and specific properties. The first step in creating the LEF file is to clearly define the port definitions, including the class and intended use of each port
* Save the mag file and relaunch the magic tool. Then run the command below to create the lef file
```
    lef write
```
<img width="960" alt="4 4" src="https://user-images.githubusercontent.com/64173714/215499686-c0f38477-bf00-46ad-be90-145cd216e0cb.png">

<img width="960" alt="4 4 1" src="https://user-images.githubusercontent.com/64173714/215499715-a9e30d63-c15f-4df4-bcc4-010159a9972f.png">

* The prior command creates a.lef file. Now, use the following command to copy the lef file into the picrorv32a directory.
```
      cp <path to the file> <path to the target location>
```
* Add the folowing to config.tcl inside the picorv32a
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
* Connect the newly created lef file to the OpenLANE flow using
```
    set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
  
    add_lefs -src $lefs 

```
<img width="951" alt="5 1" src="https://user-images.githubusercontent.com/64173714/215319500-f3e895e5-cb8c-4a45-ad82-c21ac34d86c9.png">

<img width="947" alt="5 2" src="https://user-images.githubusercontent.com/64173714/215505144-b1013341-2dde-421d-99d0-c341b1ed6313.png">

* run synthesis - `run_synthesis`

<img width="960" alt="5 3" src="https://user-images.githubusercontent.com/64173714/215505008-c5f76b67-bd49-4f83-9936-966dc3f67656.png">

* Change a few variables to reduce the negative slack. We will now change the variables "on the flight". To see the current value of the variables before changing them, use echo $::env(SYNTH STRATEGY):
```
% echo $::env(SYNTH_STRATEGY)
AREA 0
% set ::env(SYNTH_STRATEGY) 1
% echo $::env(SYNTH_BUFFERING)
1
% echo $::env(SYNTH_SIZING)
0
% set ::env(SYNTH_SIZING) 1
% echo $::env(SYNTH_DRIVING_CELL)
sky130_fd_sc_hd__inv_8
```
* run floorplan
since the openlane is new version it doesn't support `run_floorplan` command we need to use alternatives commands as follows
```
  init_floorplan
  place_io
  global_placement_or
  detailed_placement
  tap_decap_or
  detailed_placement
  gen_pdn
  run_cts
  run_routing
```
* Open the def file via magic
 
<img width="944" alt="5 5" src="https://user-images.githubusercontent.com/64173714/215507673-812bf29b-787f-43ca-b309-20a950e69b6a.png">

<img width="833" alt="5 6" src="https://user-images.githubusercontent.com/64173714/215507731-94531ee9-3635-4418-aa21-07a112a95191.png">

<img width="956" alt="5 7" src="https://user-images.githubusercontent.com/64173714/215507913-56c041d2-7165-4693-9de0-a7f6b055886f.png">

* Now, using the command, we perform a post-synthesis analysis in OpenSTA
```
  sta pre_sta.conf
```
<img width="960" alt="5 9" src="https://user-images.githubusercontent.com/64173714/215510847-d229a2f2-45fd-46cb-a9e2-f0057411f95c.png">

# Acknowledgement
* Kunal Ghosh - Co-founder of VSD 
* Nickson Jose - Instructor
