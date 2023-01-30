# Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK
This repository contains the learnings from Advanced Physical Design Using OpenLANE / SKY130 workshop. It is primarily foucused on a complete RTL2GDS flow of PICORV32A RISC-V core designwith the help of open-source EDA tool OpenLANE and Google-SkyWater’s first manufacturable open source 130nm process design kit (pdk). This repository consolidates the lab activities done as a part of the 5-day workshop.

## Course Contents
### Day1 – Inception of open-source EDA, OpenLANE and Sky130 PDK

- How to talk to computers
- SoC design and OpenLANE
- Starting RISC-V SoC Reference design
- Get familiar to open-source EDA tools
- [LAB- DAY 1](https://github.com/prajwalita17/Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK/edit/main/README.md#day-1)

### Day 2 - Understand importance of good floorplan vs bad floorplan and introduction to library cells

- Chip Floor planning considerations
- Library Binding and Placement
- Cell design and characterization flows
- General timing characterization parameters
- [LAB - DAY 2](https://github.com/prajwalita17/Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK/edit/main/README.md#day-2)

### Day 3 - Design and characterize one library cell using Magic Layout tool and ngspice

- Labs for CMOS inverter ngspice simulations
- Inception of Layout – CMOS fabrication process
- Sky130 Tech File Labs
- [LAB - DAY 3](https://github.com/prajwalita17/Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK/edit/main/README.md#day-3)

### Day 4 - Pre-layout timing analysis and importance of good clock tree

- Timing modelling using delay tables
- Timing analysis with ideal clocks using openSTA
- Clock tree synthesis TritonCTS and signal integrity
- Timing analysis with real clocks using openSTA
- [LAB - DAY 4](https://github.com/prajwalita17/Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK/edit/main/README.md#day-4)
- 
### Day 5 - Final steps for RTL2GDS

- Routing and design rule check (DRC)
- PNR interactive flow tutorial of what this project does and who it's for
- [LAB - DAY 5](https://github.com/prajwalita17/Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK/edit/main/README.md#day-5)


------------------------------------------------------------------------------------------
### Inception of open-source EDA, OpenLANE and Sky130 PDK
------------------------------------------------------------------------------

**Open source EDA (Electronic Design Automation)** refers to a set of software tools that are freely available and can be used for designing and analyzing electronic systems. These tools include schematic capture, simulation, and PCB layout, and they can be used to create designs for a wide range of applications, such as microcontrollers, FPGAs, and RF circuits. Open source EDA tools are typically developed by a community of users and developers, who share their knowledge and collaborate on new features and improvements.
Some popular open source VLSI EDA tools include:
- **Verilog-Perl**: A set of Perl scripts that can be used to simulate and analyze Verilog designs.
- **Icarus Verilog**: A free Verilog simulation and synthesis tool.
- **OpenSTA**: An open source static timing analysis tool.
- **Yosys**: An open source synthesis tool for RTL designs.
- **Magic**: A popular open source layout editor for IC design.
- **OpenSCAD**: A 3D modeling tool used for designing IC packages and other mechanical parts.

**OpenLANE (Open Logic and Analog Network Engine)** is an open-source electronic design automation (EDA) tool that is used for the design and verification of digital and mixed-signal integrated circuits (ICs). OpenLANE is used for the following purposes:
- **RTL synthesis**: OpenLANE can convert high-level hardware descriptions written in hardware description languages (HDLs) such as Verilog and VHDL into lower-level gate-level netlists.
- **Physical Design**: OpenLANE can take a gate-level netlist and optimize it for physical layout, including floorplanning, placement, and routing.
- **Verification**: OpenLANE includes a variety of verification capabilities, including logic equivalence checking, formal verification, and simulation.
- **Power and performance optimization**: OpenLANE can analyze and optimize a design for power and performance, including power-grid analysis, power-aware synthesis, and power-aware placement.
- **Analog and mixed-signal design**: OpenLANE includes support for the design and verification of analog and mixed-signal circuits, including SPICE-level simulation and layout.

 OpenLANE flow consists of several stages. By default all the flow steps are run in sequence. Each stage may consist of multiple sub-stages. OpenLANE can also be run interactively as shown here.

1. Synthesis
      1. `yosys` - Performs RTL synthesis
      2. `abc` - Performs technology mapping
      3. `OpenSTA` - Pefroms static timing analysis on the resulting netlist to generate timing reports
  2. Floorplan and PDN
      1. `init_fp` - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
      2. `ioplacer` - Places the macro input and output ports
      3. `pdn` - Generates the power distribution network
      4. `tapcell` - Inserts welltap and decap cells in the floorplan
  3. Placement
      1. `RePLace` - Performs global placement
      2. `Resizer` - Performs optional optimizations on the design
      3. `OpenPhySyn` - Performs timing optimizations on the design
      4. `OpenDP` - Perfroms detailed placement to legalize the globally placed components
  4. CTS
      1. `TritonCTS` - Synthesizes the clock distribution network (the clock tree)
  5. Routing
      1. `FastRoute` - Performs global routing to generate a guide file for the detailed router
      2. `TritonRoute` - Performs detailed routing
      3. `SPEF-Extractor` - Performs SPEF extraction
  6. GDSII Generation
      1. `Magic` - Streams out the final GDSII layout file from the routed def
  7. Checks
      1. `Magic` - Performs DRC Checks & Antenna Checks
      2. `Netgen` - Performs LVS Checks

**SKY130 PDK (Process Design Kit)** is a set of design and verification tools and components that is specifically developed and optimized for the SKY130 130nm CMOS process technology from TSMC (Taiwan Semiconductor Manufacturing Company). The PDK provides a library of standard cells and other components that are pre-verified and optimized for the SKY130 process, as well as a set of design and verification tools such as layout and extraction tools, simulation models, and testbenches.
The PDK provides various components and tools that are needed for the design of integrated circuits, such as :
- **Standard cell library**: A pre-verified library of standard cells, including logic gates, flip-flops, and other components, that are optimized for the SKY130 process.
- **Layout and extraction tools**: Tools for creating and verifying layouts of digital and mixed-signal circuits, including parasitic extraction and power-grid analysis.
- **Simulation models**: Behavioral and Spice-level simulation models of the standard cells and other components in the library, as well as a library of behavioral models for analog and mixed-signal components.
- **Testbenches and verification suites**: Testbenches and other verification tools for verifying the functionality and timing of digital and mixed-signal circuits.-
- **Design guidelines and best practices**: Guidelines and best practices for designing and verifying circuits for the SKY130 process.
Overall, PDKs are used to simplify the design process of integrated circuits by providing a set of pre-verified components and tools that are optimized for a specific process technology, thus allowing designers to focus on the functional and architectural aspects of their designs.

**RISC-V PicoRV32** is an open-source, **32-bit RISC-V** processor core that is designed for use in FPGA (Field-Programmable Gate Array) and ASIC (Application-Specific Integrated Circuit) designs. It is a small and simple core that is intended for use in embedded systems and other low-power, low-cost applications.
PicoRV32 is designed based on the RISC-V instruction set architecture (ISA), which is a free and open standard for computer processors. The PicoRV32 core implements the RV32IMC instruction set, which includes 32 general-purpose registers, 32-bit instructions, and support for integer and control instructions.
PicoRV32 is also a small and simple core, it is designed to have a small area and a small memory footprint, and it can be used in resource-constrained designs. It is also intended to be easily integrated into other designs, with a minimal number of external interfaces and a simple bus interface.
It is customizable and configurable, it can be easily adapted to different use cases and applications. It includes support for various features such as interrupts, debug interfaces, and memory protection.
<div align="center">
![RISCV](https://user-images.githubusercontent.com/104830557/214847962-d95e6d88-59a5-4fa8-9c8c-a4ad8a752c68.jpeg)
</div>

## RTL2GDS OpenLANE Flow

<div align="center">
![image](https://user-images.githubusercontent.com/104830557/214769763-47f149b8-d74b-4f58-966f-b114785f6814.png)
</div>

-----------------------------------------------------------------------------------------------------------------------------------
### DAY 1
### Understanding the File Structure and Synthesis
-----------------------------------------------------------------------------------------------------------------------------------
All the Process Design Kits(PDKs) are listed under the pdks/ directory. We use Sky130A PDK for this design. There are other open-source PDKs and related files are also available in the pdk/ directory. The location of the PDK directory is given of $PDK_ROOT variable.
```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir$ ls
openlane  pdks
```
The contents of `openlane/` files get modified or updated at each stage of the flow.

```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane$ ls -ltr
total 132
-rw-r--r--  1 prajwalita17 prajwalita17  5514 Jan 24 07:48 conf.py
-rwxr-xr-x  1 prajwalita17 prajwalita17   966 Jan 24 07:48 clean_runs.tcl
-rw-r--r--  1 prajwalita17 prajwalita17 25509 Jan 24 07:48 README.md
-rw-r--r--  1 prajwalita17 prajwalita17  7273 Jan 24 07:48 Makefile
-rw-r--r--  1 prajwalita17 prajwalita17 11350 Jan 24 07:48 LICENSE
-rw-r--r--  1 prajwalita17 prajwalita17  1285 Jan 24 07:48 CONTRIBUTING.md
-rw-r--r--  1 prajwalita17 prajwalita17   709 Jan 24 07:48 AUTHORS.md
drwxr-xr-x  5 prajwalita17 prajwalita17  4096 Jan 24 07:48 docker_build
drwxr-xr-x 44 prajwalita17 prajwalita17  4096 Jan 24 07:48 designs
-rwxr-xr-x  1 prajwalita17 prajwalita17  6519 Jan 24 07:48 flow.tcl
drwxr-xr-x  5 prajwalita17 prajwalita17  4096 Jan 24 07:48 docs
-rw-r--r--  1 prajwalita17 prajwalita17 20787 Jan 24 07:48 run_designs.py
-rw-r--r--  1 prajwalita17 prajwalita17  7898 Jan 24 07:48 report_generation_wrapper.py
drwxr-xr-x  3 prajwalita17 prajwalita17  4096 Jan 24 07:48 regression_results
drwxr-xr-x 15 prajwalita17 prajwalita17  4096 Jan 24 07:48 scripts
drwxr-xr-x  2 prajwalita17 prajwalita17  4096 Jan 26 19:37 configuration
```
The `design` contains many opensource RTL designs. PicoRV32a has been used as our reference design for RTL2GDS OpenLANE flow. We use `sky130A_sky130_fd_sc_hd__` as our default library.

```
‌‌prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a$ ls -ltr
total 32
-rw-r--r-- 1 prajwalita17 prajwalita17  209 Jan 24 07:48 sky130A_sky130_fd_sc_ms_config.tcl
-rw-r--r-- 1 prajwalita17 prajwalita17  209 Jan 24 07:48 sky130A_sky130_fd_sc_ls_config.tcl
-rw-r--r-- 1 prajwalita17 prajwalita17  209 Jan 24 07:48 sky130A_sky130_fd_sc_hs_config.tcl
-rw-r--r-- 1 prajwalita17 prajwalita17  209 Jan 24 07:48 sky130A_sky130_fd_sc_hdll_config.tcl
-rwxr-xr-x 1 prajwalita17 prajwalita17  209 Jan 24 07:48 sky130A_sky130_fd_sc_hd_config.tcl
-rwxr-xr-x 1 prajwalita17 prajwalita17  444 Jan 24 07:48 config.tcl
drwxr-xr-x 6 prajwalita17 prajwalita17 4096 Jan 26 16:52 runs
drwxr-xr-x 2 prajwalita17 prajwalita17 4096 Jan 28 10:54 src
```
The `config.tcl` shows the values set for some important variables. The `sky130A_sky130_fd_sc_hd_config.tcl` has a higher priority as compared to `config.tcl` file. Hence the values for variables in `sky130A_sky130_fd_sc_hd_config.tcl` will overwrite those in the `config.tcl`. For example, the `CLOCK_PERIOD` used in the flow will be `12ns` instead of `5 ns`.
```
‌‌‌prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a$ less config.tcl

# Design
set ::env(DESIGN_NAME) "picorv32a"
set ::env(VERILOG_FILES) "./designs/picorv32a/src/picorv32a.v"
set ::env(SDC_FILE) "./designs/picorv32a/src/picorv32a.sdc"
set ::env(CLOCK_PERIOD) "5.000"
set ::env(CLOCK_PORT) "clk"
set ::env(CLOCK_NET) $::env(CLOCK_PORT)
set filename $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl
if { [file exists $filename] == 1} {
        source $filename
}
```
```
‌‌prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a$ less sky130A_sky130_fd_sc_hd_config.tcl

# SCL Configs
set ::env(GLB_RT_ADJUSTMENT) 0.1

set ::env(SYNTH_MAX_FANOUT) 6
set ::env(CLOCK_PERIOD) "12"
set ::env(FP_CORE_UTIL) 35
set ::env(PL_TARGET_DENSITY) [ expr ($::env(FP_CORE_UTIL)+5) / 100.0 ]
```
```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.ref/sky130_fd_sc_hd$ ls -ltr
total 104
drwxr-xr-x 2 root root  4096 Jun 28  2021 verilog
drwxr-xr-x 2 root root  4096 Jun 28  2021 techlef
drwxr-xr-x 2 root root  4096 Jun 28  2021 spice
drwxr-xr-x 2 root root 36864 Jun 28  2021 maglef
drwxr-xr-x 2 root root  4096 Jun 28  2021 lib
drwxr-xr-x 2 root root  4096 Jun 28  2021 lef
drwxr-xr-x 2 root root  4096 Jun 28  2021 gds
drwxr-xr-x 2 root root  4096 Jun 28  2021 doc
drwxr-xr-x 2 root root  4096 Jun 28  2021 cdl
drwxr-xr-x 2 root root 36864 Jun 28  2021 mag
```
The `sky130_fd_sc_hd` consists of various libraries for different process, voltage and temperature (PVT) corners. For example, `sky130_fd_sc_hd__ss_n40C_1v44.lib` corresponds to a PVT corner have slow(NMOS) slow(PMOS) process, $-40^o$ C temperature and 1.44 V operating temperature.
```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.ref/sky130_fd_sc_hd/lib$ ls -ltr
total 447328
-rwxr-xr-x 1 root root 12839325 Jun 28  2021 sky130_fd_sc_hd__tt_100C_1v80.lib
-rwxr-xr-x 1 root root 12840502 Jun 28  2021 sky130_fd_sc_hd__tt_025C_1v80.lib
-rwxr-xr-x 1 root root 12838573 Jun 28  2021 sky130_fd_sc_hd__ss_n40C_1v76.lib
-rwxr-xr-x 1 root root 67866428 Jun 28  2021 sky130_fd_sc_hd__ss_n40C_1v60_ccsnoise.lib
-rwxr-xr-x 1 root root 67865927 Jun 28  2021 sky130_fd_sc_hd__ss_n40C_1v60.lib
-rwxr-xr-x 1 root root 12841363 Jun 28  2021 sky130_fd_sc_hd__ss_n40C_1v44.lib
-rwxr-xr-x 1 root root 12844330 Jun 28  2021 sky130_fd_sc_hd__ss_n40C_1v40.lib
-rwxr-xr-x 1 root root 12843147 Jun 28  2021 sky130_fd_sc_hd__ss_n40C_1v35.lib
-rwxr-xr-x 1 root root 12837196 Jun 28  2021 sky130_fd_sc_hd__ss_n40C_1v28.lib
-rwxr-xr-x 1 root root 12844125 Jun 28  2021 sky130_fd_sc_hd__ss_100C_1v60.lib
-rwxr-xr-x 1 root root 12845948 Jun 28  2021 sky130_fd_sc_hd__ss_100C_1v40.lib
-rwxr-xr-x 1 root root 71255751 Jun 28  2021 sky130_fd_sc_hd__ff_n40C_1v95_ccsnoise.lib
-rwxr-xr-x 1 root root 71255250 Jun 28  2021 sky130_fd_sc_hd__ff_n40C_1v95.lib
-rwxr-xr-x 1 root root 12843851 Jun 28  2021 sky130_fd_sc_hd__ff_n40C_1v76.lib
-rwxr-xr-x 1 root root 12842973 Jun 28  2021 sky130_fd_sc_hd__ff_n40C_1v65.lib
-rwxr-xr-x 1 root root 12842102 Jun 28  2021 sky130_fd_sc_hd__ff_n40C_1v56.lib
-rwxr-xr-x 1 root root 12841550 Jun 28  2021 sky130_fd_sc_hd__ff_100C_1v95.lib
-rwxr-xr-x 1 root root 12840659 Jun 28  2021 sky130_fd_sc_hd__ff_100C_1v65.lib
```

Once we understand the file structure and libraries, let's now move to OpenLANE PD flow.

### OpenLANE Initialization
   For invoking OpenLANE in Linux Ubuntu, we should run the docker everytime we use OpenLANE. This is done by typing `docker` as shown.
   
   A custom shell script or commands can be generated to make the task simpler.
   - To invoke OpenLANE run the `./flow.tcl` script.
   - OpenLANE supports two modes of operation: interactive and autonomous.
   - To use interactive mode use `-interactive` switch with `./flow.tcl`
The interactive switch lets us do a step by step process. So we will be able to see the changes in the design and output files at each stage.

```
prajwalita17@vsd-pd-workshop-05:/home/kunalg123/Desktop/work/tools/openlane_working_dir/openlane$ docker
bash-4.2$ ./flow.tcl -interactive
[INFO]: 
	___   ____   ___  ____   _       ____  ____     ___
	/   \ |    \ /  _]|    \ | |     /    ||    \   /  _]
	|     ||  o  )  [_ |  _  || |    |  o  ||  _  | /  [_
	|  O  ||   _/    _]|  |  || |___ |     ||  |  ||    _]
	|     ||  | |   [_ |  |  ||     ||  _  ||  |  ||   [_
	\___/ |__| |_____||__|__||_____||__|__||__|__||_____|


[INFO]: Version: v0.21
[INFO]: Running interactively
%
```
    
 ### Design Preparation
 
   The next step after invoking OpenLANE is to import the openlane package of required version. This is done using following command. Here 0.9 is the required version of OpenLANE.
 
    package require openlane 0.9
   The next step is to prepare our design for the OpenLANE flow. This is done using following command:   
  
    prep -design <design-name>
    
 During the design preparation the technology LEF and cell LEF files are merged together to obtain a `merged.lef` file. The LEF file contains information like the layer information, set of design rules, information about each standard cell which is required for place and route. 
 
```bash  
%package require openlane 0.9
0.9
% prep -design picorv32a
[INFO]: Using design configuration at /openLANE_flow/designs/picorv32a/config.tcl
[INFO]: Sourcing Configurations from /openLANE_flow/designs/picorv32a/config.tcl
[INFO]: PDKs root directory: /home/kunalg123/Desktop/work/tools/openlane_working_dir/pdks
[INFO]: PDK: sky130A
[INFO]: Setting PDKPATH to /home/kunalg123/Desktop/work/tools/openlane_working_dir/pdks/sky130A
[INFO]: Standard Cell Library: sky130_fd_sc_hd
[INFO]: Sourcing Configurations from /openLANE_flow/designs/picorv32a/config.tcl
[INFO]: Current run directory is /openLANE_flow/designs/picorv32a/runs/28-01_10-00
[INFO]: Preparing LEF Files
[INFO]: Extracting the number of available metal layers from /home/kunalg123/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.ref/sky130_fd_sc_hd/techlef/sky130_fd_sc_hd.tlef
[INFO]: The number of available metal layers is 6
[INFO]: The available metal layers are li1 met1 met2 met3 met4 met5
[INFO]: Merging LEF Files...
mergeLef.py : Merging LEFs
sky130_ef_sc_hd__decap_12.lef: SITEs matched found: 0
sky130_ef_sc_hd__decap_12.lef: MACROs matched found: 1
sky130_fd_sc_hd.lef: SITEs matched found: 0
sky130_fd_sc_hd.lef: MACROs matched found: 437
sky130_ef_sc_hd__fill_12.lef: SITEs matched found: 0
sky130_ef_sc_hd__fill_12.lef: MACROs matched found: 1
sky130_ef_sc_hd__fakediode_2.lef: SITEs matched found: 0
sky130_ef_sc_hd__fakediode_2.lef: MACROs matched found: 1
mergeLef.py : Merging LEFs complete
[INFO]: Trimming Liberty...
[INFO]: Generating Exclude List...
[INFO]: Storing configs into config.tcl ...
[INFO]: Preparation complete

```
The `config.tcl` present inside the `picorv32a/runs/28-01_10-00` contains the default values or values set for variables during the run. The content of the config file is as shown.

```
‌prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/28-01_10-00$ less config.tcl 

# Run configs
set ::env(PDK_ROOT) "/home/kunalg123/Desktop/work/tools/openlane_working_dir/pdks"
set ::env(BASE_SDC_FILE) "/openLANE_flow/scripts/base.sdc"
set ::env(BOTTOM_MARGIN_MULT) "4"
set ::env(CARRY_SELECT_ADDER_MAP) "/home/kunalg123/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd/csa_map.v"
set ::env(CELLS_LEF) "/openLANE_flow/designs/picorv32a/runs/28-01_10-00/tmp/merged.lef"
set ::env(CELLS_LEF_UNPADDED) "/openLANE_flow/designs/picorv32a/runs/28-01_10-00/tmp/merged_unpadded.lef"
set ::env(CELL_CLK_PORT) "CLK"
set ::env(CELL_PAD) "4"
set ::env(CELL_PAD_EXCLUDE) "sky130_fd_sc_hd__tap* sky130_fd_sc_hd__decap* sky130_fd_sc_hd__fill*"
set ::env(CHECK_ASSIGN_STATEMENTS) "0"
set ::env(CHECK_UNMAPPED_CELLS) "1"
set ::env(CLK_BUFFER) "sky130_fd_sc_hd__clkbuf_4"
set ::env(CLK_BUFFER_INPUT) "A"
set ::env(CLK_BUFFER_OUTPUT) "X"
set ::env(CLOCK_BUFFER_FANOUT) "16"
set ::env(CLOCK_NET) "clk"
set ::env(CLOCK_PERIOD) "12"
set ::env(CLOCK_PORT) "clk"
set ::env(CLOCK_TREE_SYNTH) "1"
set ::env(CONFIGS) "/openLANE_flow/configuration/placement.tcl /openLANE_flow/configuration/lvs.tcl /openLANE_flow/configuration/floorplan.tcl /openLANE_flow/configuration/cts.tcl /openLANE_flow/configuration/general.tcl /openLANE_flow/configuration/synthesis.tcl /openLANE_flow/configuration/routing.tcl /openLANE_flow/configuration/checkers.tcl"
set ::env(CTS_CLK_BUFFER_LIST) "sky130_fd_sc_hd__clkbuf_1 sky130_fd_sc_hd__clkbuf_2 sky130_fd_sc_hd__clkbuf_4 sky130_fd_sc_hd__clkbuf_8"
set ::env(CTS_MAX_CAP) "1.53169"
set ::env(CTS_REPORT_TIMING) "1"
set ::env(CTS_ROOT_BUFFER) "sky130_fd_sc_hd__clkbuf_16"
set ::env(CTS_SINK_CLUSTERING_MAX_DIAMETER) "50"
set ::env(CTS_SINK_CLUSTERING_SIZE) "20"
set ::env(CTS_SQR_CAP) "0.258e-3"
set ::env(CTS_SQR_RES) "0.125"
set ::env(CTS_TARGET_SKEW) "200"
set ::env(CTS_TECH_DIR) "N/A"
```
Once the design preparation is complete, `runs/` folder would be created inside the `picorv32a` folder as shown. The contents inside the picorv32/runs/28-01_10-00/tmp will be required by OpenLANE at various stages. The `merged.lef` in the `tmp` folder is the LEF file created during the design preparation. This LEF file contains the tech and cell LEF information.

<div align="center">
<img width="735" alt="CELL LEF AND TECH LEF" src="https://user-images.githubusercontent.com/104830557/215262938-95fb2ef0-9aea-49d4-a48b-81e8a71863a3.png">
</div>

```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/28-01_10-00/tmp$ ls -ltr
total 23212
-rwxr-xr-x 1 prajwalita17 prajwalita17      202 Jun 28  2021 tracks_copy.info
-rw-r--r-- 1 prajwalita17 prajwalita17  2264912 Jan 28 15:30 merged.lef
-rw-r--r-- 1 prajwalita17 prajwalita17       33 Jan 28 15:30 met_layers_list.txt
-rw-r--r-- 1 prajwalita17 prajwalita17  2264912 Jan 28 15:30 merged_unpadded.lef
-rwxr-xr-x 1 prajwalita17 prajwalita17     6975 Jan 28 15:30 trimmed.lib.exclude.list
-rw-r--r-- 1 prajwalita17 prajwalita17  6291198 Jan 28 15:30 trimmed.lib
drwxr-xr-x 2 prajwalita17 prajwalita17     4096 Jan 28 15:30 routing
drwxr-xr-x 2 prajwalita17 prajwalita17     4096 Jan 28 15:30 placement
drwxr-xr-x 2 prajwalita17 prajwalita17     4096 Jan 28 15:30 magic
drwxr-xr-x 2 prajwalita17 prajwalita17     4096 Jan 28 15:30 lvs
drwxr-xr-x 2 prajwalita17 prajwalita17     4096 Jan 28 15:30 klayout
drwxr-xr-x 2 prajwalita17 prajwalita17     4096 Jan 28 15:30 floorplan
drwxr-xr-x 2 prajwalita17 prajwalita17     4096 Jan 28 15:30 cvc
drwxr-xr-x 2 prajwalita17 prajwalita17     4096 Jan 28 15:30 cts
-rw-r--r-- 1 prajwalita17 prajwalita17 12892372 Jan 28 15:49 sky130_fd_sc_hd__tt_025C_1v80.no_pg.lib
drwxr-xr-x 2 prajwalita17 prajwalita17     4096 Jan 28 15:49 synthesis
```
### Design Synthesis and Results
 
The next step in OpenLANE flow is RTL Synthesis of the design(picorv32a) loaded. This is done using the following command.
   `run_synthesis`
   
Yosys is a framework for Verilog RTL synthesis. It currently has extensive Verilog-2005 support and provides a basic set of synthesis algorithms for various application domains. ABC maps the cells in the netlist to the library.

After completion of synthesis, we get the following statistics.

```bash
%run_synthesis
 28. Printing statistics.
=== picorv32a ===

   Number of wires:              14596
   Number of wire bits:          14978
   Number of public wires:        1565
   Number of public wire bits:    1947
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:              14876
     sky130_fd_sc_hd__a2111o_2       1
     sky130_fd_sc_hd__a211o_2       35
     sky130_fd_sc_hd__a211oi_2      60
     sky130_fd_sc_hd__a21bo_2      149
     sky130_fd_sc_hd__a21boi_2       8
     sky130_fd_sc_hd__a21o_2        57
     sky130_fd_sc_hd__a21oi_2      244
     sky130_fd_sc_hd__a221o_2       86
     sky130_fd_sc_hd__a22o_2      1013
     sky130_fd_sc_hd__a2bb2o_2    1748
     sky130_fd_sc_hd__a2bb2oi_2     81
     sky130_fd_sc_hd__a311o_2        2
     sky130_fd_sc_hd__a31o_2        49
     sky130_fd_sc_hd__a31oi_2        7
     sky130_fd_sc_hd__a32o_2        46
     sky130_fd_sc_hd__a41o_2         1
     sky130_fd_sc_hd__and2_2       157
     sky130_fd_sc_hd__and3_2        58
     sky130_fd_sc_hd__and4_2       345
     sky130_fd_sc_hd__and4b_2        1
     sky130_fd_sc_hd__buf_1       1655
     sky130_fd_sc_hd__buf_2          9
     sky130_fd_sc_hd__bufinv_8      17
     sky130_fd_sc_hd__conb_1        42
     sky130_fd_sc_hd__dfxtp_2     1613
     sky130_fd_sc_hd__inv_12        32
     sky130_fd_sc_hd__inv_2       1546
     sky130_fd_sc_hd__inv_4          4
     sky130_fd_sc_hd__inv_6          7
     sky130_fd_sc_hd__inv_8          9
     sky130_fd_sc_hd__mux2_1      1224
     sky130_fd_sc_hd__mux2_2         2
     sky130_fd_sc_hd__mux4_1       221
     sky130_fd_sc_hd__nand2_2       78
     sky130_fd_sc_hd__nor2_2       524
     sky130_fd_sc_hd__nor2b_2        1
     sky130_fd_sc_hd__nor3_2        42
     sky130_fd_sc_hd__nor4_2         1
     sky130_fd_sc_hd__o2111a_2       2
     sky130_fd_sc_hd__o211a_2       69
     sky130_fd_sc_hd__o211ai_2       6
     sky130_fd_sc_hd__o21a_2        54
     sky130_fd_sc_hd__o21ai_2      141
     sky130_fd_sc_hd__o21ba_2      209
     sky130_fd_sc_hd__o21bai_2       1
     sky130_fd_sc_hd__o221a_2      203
     sky130_fd_sc_hd__o221a_4        1
     sky130_fd_sc_hd__o221ai_2       7
     sky130_fd_sc_hd__o22a_2      1312
     sky130_fd_sc_hd__o22ai_2       59
     sky130_fd_sc_hd__o2bb2a_2     119
     sky130_fd_sc_hd__o2bb2ai_2     92
     sky130_fd_sc_hd__o311a_2        8
     sky130_fd_sc_hd__o31a_2        19
     sky130_fd_sc_hd__o31ai_2        1
     sky130_fd_sc_hd__o32a_2       109
     sky130_fd_sc_hd__o41a_2         2
     sky130_fd_sc_hd__or2_2       1000
     sky130_fd_sc_hd__or2_4         88
     sky130_fd_sc_hd__or2b_2        25
     sky130_fd_sc_hd__or3_2         65
     sky130_fd_sc_hd__or3_4          3
     sky130_fd_sc_hd__or3b_2         5
     sky130_fd_sc_hd__or4_2         90
     sky130_fd_sc_hd__or4_4          3
     sky130_fd_sc_hd__or4b_2         6
     sky130_fd_sc_hd__or4bb_2        2

   Chip area for module '\picorv32a': 148708.873600
     .
[INFO]: Changing netlist from 0 to /openLANE_flow/designs/picorv32a/runs/26-01_10-26/results/synthesis/picorv32a.synthesis.v
[INFO]: Running Static Timing Analysis...
[INFO]: current step index: 2
     .
[INFO]: Setting load to: 0.01765
set_load  $cap_load [all_outputs]
tns -3258.36
wns -24.91
[INFO]: Synthesis was successful‌
```
Static timing analysis at synthesis stage gives shows a total negative slack of `-3258.36` and worst negative slack of `-24.91`.

```
Assignment 1: Calculation of Flop ratio

From the report we can see that
Number of cells = 14876
Number of flip flops (sky130_fd_sc_hd__dfxtp_2) = 1613

Flip flop ratio =1613/14876 = 10.84 %
```
-----------------------------------------------------------------------------------------------------------------------------------
### DAY 2
### Floorplanning
-----------------------------------------------------------------------------------------------------------------------------------
Once the synthesis step is done, the next step is floorplan. Before `run_floorplan` let us look at the list of some of the variables required for the floorplan step and their default values.
The `floorplan.tcl` file contains the default values of variables required for the floorplan. For example the core utilization ratio `FP_CORE_UTIL` can be seen as 50%. 

```
‌‌prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/configuration$ less floorplan.tcl 
```
### Floorplan defaults
```
set ::env(FP_IO_VMETAL) 3
set ::env(FP_IO_HMETAL) 4

set ::env(FP_SIZING) relative
set ::env(FP_CORE_UTIL) 50
set ::env(FP_CORE_MARGIN) 0
set ::env(FP_ASPECT_RATIO) 1

set ::env(FP_PDN_VOFFSET) 16.32
set ::env(FP_PDN_VPITCH) 153.6
set ::env(FP_PDN_HOFFSET) 16.65
set ::env(FP_PDN_HPITCH) 153.18

set ::env(FP_PDN_AUTO_ADJUST) 1

set ::env(FP_PDN_CORE_RING) 0
set ::env(FP_PDN_ENABLE_RAILS) 1

set ::env(FP_PDN_CHECK_NODES) 1

set ::env(FP_IO_MODE) 1; # 0 matching mode - 1 random equidistant mode
set ::env(FP_IO_HLENGTH) 4
set ::env(FP_IO_VLENGTH) 4
set ::env(FP_IO_VEXTEND) -1
set ::env(FP_IO_HEXTEND) -1
set ::env(FP_IO_VTHICKNESS_MULT) 2
set ::env(FP_IO_HTHICKNESS_MULT) 2

set ::env(BOTTOM_MARGIN_MULT) 4
set ::env(TOP_MARGIN_MULT) 4
set ::env(LEFT_MARGIN_MULT) 12
set ::env(RIGHT_MARGIN_MULT) 12

set ::env(FP_HORIZONTAL_HALO) 10
set ::env(FP_VERTICAL_HALO) $::env(FP_HORIZONTAL_HALO)
```
The values in `floorplan.tcl` will be overwritten by the values set by `picorv32a/config.tcl` and `picorv32a/sky130_fd_sc_hd_config.tcl` files. 
`picorv32a/sky130_fd_sc_hd_config.tcl` has the highest priority and hence it's important to make sure this file has the intended values for the variables.
Once we make sure the variables for floorplan are set, we run the floorplan using the `run_floorplan` command.
                
```
%run_floorplan
[INFO]: PDN generation was successful.
[INFO]: Changing layout from /openLANE_flow/designs/picorv32a/runs/28-01_10-00/results/floorplan/picorv32a.floorplan.def to /openLANE_flow/designs/picorv32a/runs/28-01_10-00/tmp/floorplan/7-pdn.def
```
The contents of `4-ioPlacer.log` can be seen here.
```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/28-01_10-00/logs/floorplan$ vim 4-ioPlacer.log 

OpenROAD 0.9.0 1415572a73
This program is licensed under the BSD-3 license. See the LICENSE file for details.
Components of this program may be licensed under more restrictive licenses which must be honored.
Notice 0: Reading LEF file:  /openLANE_flow/designs/picorv32a/runs/28-01_10-00/tmp/merged.lef
Notice 0:     Created 13 technology layers
Notice 0:     Created 25 technology vias
Notice 0:     Created 440 library cells
Notice 0: Finished LEF file:  /openLANE_flow/designs/picorv32a/runs/28-01_10-00/tmp/merged.lef
Notice 0:
Reading DEF file: /openLANE_flow/designs/picorv32a/runs/28-01_10-00/tmp/floorplan/3-verilog2def_openroad.def
Notice 0: Design: picorv32a
Notice 0:     Created 409 pins.
Notice 0:     Created 14876 components and 115597 component-terminals.
Notice 0:     Created 14978 nets and 56051 connections.
Notice 0: Finished DEF file: /openLANE_flow/designs/picorv32a/runs/28-01_10-00/tmp/floorplan/3-verilog2def_openroad.def
#Macro blocks found: 0
Using 5u default boundaries offset
Random pin placement
RandomMode Even
```
After floorplan, `picorv32a.floorplan.def` gets created in the `runs/28-01_10-00/results/floorplan` folder as shown.
```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/28-01_10-00/results/floorplan$ ls -ltr
total 2636
lrwxrwxrwx 1 prajwalita17 prajwalita17      29 Jan 28 17:45 merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
-rw-r--r-- 1 prajwalita17 prajwalita17 2438886 Jan 28 17:47 picorv32a.floorplan.def
-rw-r--r-- 1 prajwalita17 prajwalita17  254571 Jan 28 17:47 picorv32a.floorplan.def.png
```
The partial content of `picorv32a.floorplan.def` is as shown. The DEF file gets updated at each step of the flow while LEF remains the same.

```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/28-01_10-00/results/floorplan$ less picorv32a.floorplan.def

VERSION 5.8 ;
DIVIDERCHAR "/" ;
BUSBITCHARS "[]" ;
DESIGN picorv32a ;
UNITS DISTANCE MICRONS 1000 ;
DIEAREA ( 0 0 ) ( 662870 673590 ) ;
ROW ROW_0 unithd 5520 10880 FS DO 1417 BY 1 STEP 460 0 ;
ROW ROW_1 unithd 5520 13600 N DO 1417 BY 1 STEP 460 0 ;
ROW ROW_2 unithd 5520 16320 FS DO 1417 BY 1 STEP 460 0 ;
ROW ROW_3 unithd 5520 19040 N DO 1417 BY 1 STEP 460 0 ;
ROW ROW_4 unithd 5520 21760 FS DO 1417 BY 1 STEP 460 0 ;
ROW ROW_5 unithd 5520 24480 N DO 1417 BY 1 STEP 460 0 ;
ROW ROW_6 unithd 5520 27200 FS DO 1417 BY 1 STEP 460 0 ;
ROW ROW_7 unithd 5520 29920 N DO 1417 BY 1 STEP 460 0 ;
.
.
.
```
```
Assignment 2: Find the die area.
Die area = 662.870 * 673.590
	 = 446502.6033 sq. microns
```
Let us view the floorplan in magic using the following command. 
For this we need the `tech file`, `LEf file` and `DEF file`. 
`~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech` is the location of the `tech file`. `~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-01_11-22/tmp/merged.lef` is the location of the `LEF file`.
`~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-01_11-22/results/floorplan` is the location of the `DEF file`.

```
‌‌prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-01_11-22/results/floorplan$ magic -T /home/kunalg123/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
The figure below shows the magic layout of the floor plan. Presently the layout consists of IO pins and the decap cells. The unplaced standard cells can be seen in the lower left corner. The details of any selected cell can be obtained by typing `what` in `tkcon 2.3 Main` console. For example, The selected pin `trace_data[26]` belongs to metal 3 layer as shown in the console.

<div align="center">
<img width="534" alt="magic" src="https://user-images.githubusercontent.com/104830557/214843831-d8d3eb80-52ae-431b-8c9c-5673a6629b07.png">
</div>
<div align="center">
<img width="800" alt="what_tkcon" src="https://user-images.githubusercontent.com/104830557/215285493-d365c166-e381-4559-bb79-762bd2860f0d.png">
</div>
<div align="center">
<img width="602" alt="unplaced cells" src="https://user-images.githubusercontent.com/104830557/215285581-c7d9f8eb-344c-4363-8f36-f9ddc84df41d.png">
</div>

-----------------------------------------------------------------------------------------------------------------------------------
### DAY 3
### Standard Cell Design and Characterization
-----------------------------------------------------------------------------------------------------------------------------------

**Cell characterization** is the process of measuring the electrical characteristics of a digital logic cell. This includes measuring the cell's performance parameters such as delay, power consumption, and noise margins, under different conditions such as different input patterns and process variations. The results of cell characterization are used to create a library of cell models that can be used in the design and simulation of digital circuits.

During the characterization process, the cell's input and output waveforms are measured using testbenches and the results are used to create a model that describes the cell's behavior under different conditions. The model is then used to predict the cell's performance in the overall circuit design, allowing designers to optimize the circuit's performance and ensure that it meets the required specifications. Cell characterization is an important step in the VLSI design flow, as it allows designers to create accurate models of the cells that will be used in the final circuit, and thus can help to improve the yield and reliability of the final product.

The focus of this section of LAB work is to design a standard cell (inverter) spice deck and characterize the cell for various W/L ratio and output load capacitance. 

We use the inverter design from `vsdstdcelldesign.git`. The command to clone and add the sky130A.tech file to vsdstdcell design folder is as shown.
```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane$git clone https://github.com/nickson-jose/vsdstdcelldesign.git
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic$ cp sky130A.tech ../../../../openlane/vsdstdcelldesign/
‌prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign$ ls -ltr
total 180
-rw-rw-r-- 1 prajwalita17 prajwalita17  13525 Jan 29 00:07 README.md
-rw-rw-r-- 1 prajwalita17 prajwalita17  11357 Jan 29 00:07 LICENSE
drwxrwxr-x 2 prajwalita17 prajwalita17   4096 Jan 29 00:07 Images
drwxrwxr-x 2 prajwalita17 prajwalita17   4096 Jan 29 00:07 extras
drwxrwxr-x 2 prajwalita17 prajwalita17   4096 Jan 29 00:07 libs
-rw-rw-r-- 1 prajwalita17 prajwalita17   2716 Jan 29 00:07 sky130_inv.mag
-rwxr-xr-x 1 prajwalita17 prajwalita17 136710 Jan 29 00:11 sky130A.tech
```
`sky130_inv.mag` is the magic layout of the inverter. Command to open the `sky130_inv.mag` inverter circuit in MAGIC is shown.
```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign$ magic -T sky130.tech sky130_inv.mag 
```
<div align="center">
<img width="170" alt="vsdstdcell" src="https://user-images.githubusercontent.com/104830557/215286280-5d2e9b10-8de3-4136-ae65-d1496fc1a72f.png">
</div>

`.spice` file is extracted from the `.mag` file using `extract all`, `ext2spice cthresh 0 rthresh 0` and 'ext2spice` commands in `tkcon 2.3 Main` console as shown.

<div align="center">
<img width="737" alt="spiceextract" src="https://user-images.githubusercontent.com/104830557/215286745-8feb5cfa-fd97-43a0-8702-fed85d8e96c2.png">
</div>

The `extract all`, `ext2spice cthresh 0 rthresh 0` and `ext2spice` commands creates two files `sky130_inv.ext` and `sky130_inv.spice` in vsdstdcelldesign folder. 
The extracted `sky130_inv.spice` contains the connectivity information of the mosfets and the parasitic capacitances. The extracted spice file is then modified to include the model files, input voltage values and transient/dc analysis commands as shown.

```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign$ vim sky130_inv.spice
```
<div align="center">
![wp24](https://user-images.githubusercontent.com/104830557/215353087-02ba5fcd-f20c-46c3-a4a1-28f30b8462d4.png)
</div>

```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign$ ngspice sky130_inv.spice
```
```
Circuit: * spice3 file created from sky130_inv.ext - technology: sky130a

Scale set
Doing analysis at TEMP = 27.000000 and TNOM = 27.000000

Warning: va: no DC value, transient time 0 value used

Initial Transient Solution
--------------------------
Node                                   Voltage
----                                   -------
y                                          3.3
a                                            0
vgnd                                         0
vpwr                                       3.3
va#branch                                    0
vss#branch                         3.32412e-12
vdd#branch                        -3.32413e-12

No. of Data Rows : 145
ngspice 1 -> plot y vs time a
```
![wp24 - Paint 30-01-2023 02_01_54](https://user-images.githubusercontent.com/104830557/215354534-504f8b98-f861-4168-9190-930fa728d76b.png)

![Snipping Tool 30-01-2023 01_59_52](https://user-images.githubusercontent.com/104830557/215354157-fadc120a-cffd-41a4-be3c-46b3fe972ba6.png)



To characerize the cell, we find the following parameters for various W/L ratio or NMOS and PMOS.
- Rise Time
- Fall Time
- Cell Rise Delay
- Cell Fall Delay

Let us calculate these parameters for inv cell with output capacitance of 2fF. Running ngspice simulations and measuring the parameters for inverter circuit for the following two cases, we get the following results.
- $W_n$ = 2 $W_p$ 
- $W_n$ = 3 $W_p$

| Parameter     | Inverter circuit with $W_n$ = 2 $W_p$|  Inverter circuit with $W_n$ = 3 $W_p$              |
|---------------|--------------------------------------|-------------------------|
|Rise Time | 14.57 ps|151.34 ps|
| Fall Time |43.39 ps|40.86 ps|
| Cell Rise Delay  | 121.62 ps|125.74 ps|
| Cell Fall Delay  | 25.74 ps|16.78 ps|

![image](https://user-images.githubusercontent.com/104830557/215378257-1fcc687a-4c39-4687-a5a4-102cfde07df4.png)


<div align="center">
![68 183 188 194 - Remote Desktop Connection 29-01-2023 10_36_22](https://user-images.githubusercontent.com/104830557/215354384-d77f5d3f-43a7-47fe-96d0-5d02f3a99015.png)
</div>

Similarly, running ngspice simulations and measuring the parameters for inverter circuit for the following two cases, we get the following results.
- $W_p$ = 2 $W_n$
- $W_p$ = 4 $W_n$

| Parameter     | Inverter circuit with $W_p$ = 2 $W_n$|           Inverter circuit with $W_p$ = 4 $W_n$              |
|---------------|--------------------------------------|-------------------------|
|Rise Time | 147.69 ps|48.93 ps|
| Fall Time |43.63 ps|56.87 ps|
| Cell Rise Delay  | 121.29 ps|38.21 ps|
| Cell Fall Delay  | 24.8 ps|70 ps|

Running ngspice simulations and measuring the parameters for inverter circuit for two different output load capacitances while keeping $W_p$ = 2 $W_n$, we get the following results.

| Parameter     | Inverter circuit with $W_p$ = 2 $W_n$ and output load capacitance 2 fF|  Inverter circuit with $W_p$ = 2 $W_n$ and output load capacitance 0.2 fF  |
|---------------|--------------------------------------|-------------------------|
|Rise Time | 147.69 ps|48 ps|
| Fall Time |43.63 ps|31.34 ps|
| Cell Rise Delay  | 121.29 ps|28.025 ps|
| Cell Fall Delay  | 24.8 ps|34.4 ps|

-----------------------------------------------------------------------------------------------------------------------------------
### DAY 4
### Extraction of LEF of Custom Cell and Placement
-----------------------------------------------------------------------------------------------------------------------------------

### Extracting LEF file from MAGIC file

- The horizontal and vertical routing grid must intersect on the input and output ports (A and Y)
- The width of the standard cell must be odd multiples of x pitch of the PR boundary.
- The height of the standard cell is defined by the y pitch of the PR boundary. The hieght of all the standard cells in a design is same.

![image](https://user-images.githubusercontent.com/104830557/215379125-8dfe3131-16dd-42c9-8fde-cc64d5d3e3aa.png)


The inverter design can be saved with a custom name in the `tkcon 2.3 Main` console as shown. The custom cell would be identied as `sky_130vsdinv` after placement.
`save sky_130vsdinv.mag`
`lef write` saves `sky_130vsdinv.lef` file in the vsdstdcelldesign folder.

Copy `my_base.sdc` `from vsdstdcell/extras` to `openlane/designs/picorv32a/src`. 
`vsdstdcelldesign/libs/sky130_fd_sc_hd__typical.lib` contains the `sky_130vsdinv cell` as shown. 

<div align="center">
![image](https://user-images.githubusercontent.com/104830557/215379125-8dfe3131-16dd-42c9-8fde-cc64d5d3e3aa.png)

</div>

copy the `libs/sky130_fd_sc_hd__typical.lib`. `sky130_fd_sc_hd__fast.lib`. `sky130_fd_sc_hd__slow.lib` to `src` folder. Contents of `src` folder will be 

<div align="center">
![src](https://user-images.githubusercontent.com/104830557/215357103-46b0213e-1419-404d-84c2-5c2ffece6535.png)
	</div>
	
We then modify the config.tcl file by adding these lines to existing config.tcl file.

```
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_MIN) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_MAX) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```

We now run the complete flow starting from invoking `docker` to `routing`. The flow will now include the custom cell `sky_130vsdinv`

Steps for the complete flow.
- `prajwalita17@vsd-pd-workshop-05:/home/kunalg123/Desktop/work/tools/openlane_working_dir/openlane$ docker`
- `bash-4.2$ ./flow.tcl -interative`
- `% package require openlane 0.9`
- `% prep -design picorv32a`
- `% set lefs [glob $::env(DESIGN_DIR)/src/*.lef]`
- `% add_lefs -src $lefs`
- `% run_synthesis`

The statitics after synthesis show that there are 1537 instances of the custom cell `sky_130vsdinv` in the design.

<div align="center">
![stat](https://user-images.githubusercontent.com/104830557/215358688-19fbc2b4-930b-464e-9169-71372f8a0ce2.png)
</div>

```
set ::env(SYNTH_STRATEGY) 0
set ::env(SYNTH_BUFFERING) 0
set ::env(SYNTH_SIZING) 0
```
The negative slack can be fixed by setting appropriate values of variables. Reducing maximum fanout or replacing cells with lower drive strength can be replaced with those having higher drive strength. These changed may have a negative impact on area. 

- `% init_floorplan`

- `% place_io`

- `% global_placement_or`

- `% detailed_placement`

- `% tap_decap_or`

- `% detailed_placement`


<div align="center">
![68 183 188 194 - Remote Desktop Connection 29-01-2023 23_13_22](https://user-images.githubusercontent.com/104830557/215358749-10273a4f-9e80-4e74-b6ca-b30b22089d96.png)
	</div>
	
We can see the cell by zooming by pressing `z`. We can also see the custom cell in the cell manager.

<div align="center">
![68 183 188 194 - Remote Desktop Connection 29-01-2023 23_23_55](https://user-images.githubusercontent.com/104830557/215358876-e8695a65-94d3-4ae3-bfbb-6d6e9d77d463.png)
	</div>
	
The expanded version of the custom cell is as shown.

<div align="center">
![68 183 188 194 - Remote Desktop Connection 29-01-2023 23_31_28](https://user-images.githubusercontent.com/104830557/215358883-da163cd4-a859-404a-80ed-976d456cebea.png)
	</div>
	

-----------------------------------------------------------------------------------------------------------------------------------
### DAY 5
### Clock Tree Synthesis and Routing
-----------------------------------------------------------------------------------------------------------------------------------

We continue the flow by running the following commands.
`% gen_pdn`

- `% run_cts`

- `% run_routing`

-------------------------------------------
### Acknowledgements
-------------------------------------------

[Kunal Ghosh](https://github.com/kunalg123)

[Nikson Jose](https://github.com/nickson-jose)



