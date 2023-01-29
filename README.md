# Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK
This repository contains a step by step procedure to the complete RTL2GDSII flow of PICORV32A RISC-V core design using open-source EDA tool OpenLANE and Google-Skywater’s first manufacturable open source 130nm PDK.
### LAB WORK
### DAY 1
#### - [Understanding the File Structure](https://github.com/prajwalita17/Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK/edit/main/README.md#understanding-the-file-structure)
#### - [OpenLANE Initialization](https://github.com/prajwalita17/Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK/edit/main/README.md#openlane-initialization)
#### - [Design Preparation](https://github.com/prajwalita17/Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK/edit/main/README.md#design-preparation)
#### - [Design Synthesis and Results](https://github.com/prajwalita17/Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK/edit/main/README.md#design-synthesis-and-results)

### DAY 2
#### - [Understanding the Floorplan variables and settings]()
#### - [Floorplan]()
#### - [Floorplan in MAGIC]()
#### - [Design Synthesis and Results](https://github.com/prajwalita17/Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK/edit/main/README.md#design-synthesis-and-results)
-----------------------------------------------------------------------------------------------------------------------------------
## DAY 1
## Understanding the File Structure
-----------------------------------------------------------------------------------------------------------------------------------
All the Process Design Kits(PDKs) are listed under the pdks/ directory. We use Sky130A PDK for this design. There are other open-source PDKs and related files are also available in the pdk/ directory. The location of the PDK directory is given of $PDK_ROOT variable.
```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir$ ls
openlane  pdks
```
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
The `config.tcl` show the values set for some important variables. The `sky130A_sky130_fd_sc_hd_config.tcl` has a higher priority as compared to `config.tcl` file. Hence the values for variables in `sky130A_sky130_fd_sc_hd_config.tcl` will overwrite those in the `config.tcl`. For example, the `CLOCK_PERIOD` used in the flow will be `12ns` instead of `5 ns`.
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
Once the synthesis step is done, the next step is floorplan. Before `run_floorplan` let us look at the list variables rof equired for the floorplan step and their default values.
| Variable      | Description                                                   |
|---------------|---------------------------------------------------------------|
| `FP_CORE_UTIL`  | The core utilization percentage. <br> (Default: `50` percent)|
| `FP_ASPECT_RATIO`  | The core's aspect ratio (height / width). <br> (Default: `1`)|
| `FP_SIZING`  | Whether to use relative sizing by making use of `FP_CORE_UTIL` or absolute one using `DIE_AREA`. <br> (Default: `"relative"` - accepts "absolute" as well)|
| `DIE_AREA`  | Specific die area to be used in floorplanning. Specified as a 4-corner rectangle. Units in mm <br> (Default: unset)|
| `FP_IO_HMETAL`  | The metal layer on which to place the io pins horizontally (top and bottom of the die). <br>(Default: `4`)|
| `FP_IO_VMETAL`  | The metal layer on which to place the io pins vertically (sides of the die) <br> (Default: `3`)|
| `FP_IO_MODE`  | Decides the mode of the random IO placement option. 0=matching mode, 1=random equidistant mode <br> (Default: `1`)|
| `FP_WELLTAP_CELL`  | The name of the welltap cell during welltap insertion. |
| `FP_ENDCAP_CELL`  | The name of the endcap cell during endcap insertion. |
| `FP_PDN_VOFFSET`  | The offset of the vertical power stripes on the metal layer 4 in the power distribution network <br> (Default: `16.32`) |
| `FP_PDN_VPITCH`  | The pitch of the vertical power stripes on the metal layer 4 in the power distribution network <br> (Default: `153.6`) |
| `FP_PDN_HOFFSET`  | The offset of the horizontal power stripes on the metal layer 5 in the power distribution network <br> (Default: `16.65`) |
| `FP_PDN_HPITCH`  | The pitch of the horizontal power stripes on the metal layer 5 in the power distribution network <br> (Default: `153.18`) |
| `FP_PDN_AUTO_ADJUST` | Decides whether or not the flow should attempt to re-adjust the power grid, in order for it to fit inside the core area of the design, if needed. <br> 1=enabled, 0 =disabled (Default: `1`) |
| `FP_TAPCELL_DIST`  | The horizontal distance between two tapcell columns <br> (Default: `14`) |
| `FP_IO_VEXTEND`  |  Extends the vertical io pins outside of the die by the specified units<br> (Default: `-1` Disabled) |
| `FP_IO_HEXTEND`  |  Extends the horizontal io pins outside of the die by the specified units<br> (Default: `-1` Disabled) |
| `FP_IO_VLENGTH`  | The length of the vertical IOs in microns. <br> (Default: `4`) |
| `FP_IO_HLENGTH`  | The length of the horizontal IOs in microns. <br> (Default: `4`) |
| `FP_IO_VTHICKNESS_MULT`  | A multiplier for vertical pin thickness. Base thickness is the pins layer minwidth <br> (Default: `2`) |
| `FP_IO_HTHICKNESS_MULT`  | A multiplier for horizontal pin thickness. Base thickness is the pins layer minwidth <br> (Default: `2`) |
| `BOTTOM_MARGIN_MULT`     | The core margin, in multiples of site heights, from the bottom boundary. <br> (Default: `4`) |
| `TOP_MARGIN_MULT`        | The core margin, in multiples of site heights, from the top boundary. <br> (Default: `4`) |
| `LEFT_MARGIN_MULT`       | The core margin, in multiples of site widths, from the left boundary.  <br> (Default: `12`) |
| `RIGHT_MARGIN_MULT`      | The core margin, in multiples of site widths, from the right boundary.   <br> (Default: `12`) |
| `FP_PDN_CORE_RING` | Enables adding a core ring around the design. More details on the control variables in the pdk configurations documentation. 0=Disable 1=Enable. <br> (Default: `0`) |
| `FP_PDN_ENABLE_RAILS` | Enables the creation of rails in the power grid. 0=Disable 1=Enable. <br> (Default: `1`) |
| `FP_PDN_CHECK_NODES` | Enables checking for unconnected nodes in the power grid. 0=Disable 1=Enable. <br> (Default: `1`) |
| `FP_HORIZONTAL_HALO` | Sets the horizontal halo around the tap and decap cells. The value provided is in microns. <br> Default: `10` |
| `FP_VERTICAL_HALO` | Sets the vertical halo around the tap and decap cells. The value provided is in microns. <br> Default: set to the value of `FP_HORIZONTAL_HALO` |
| `DESIGN_IS_CORE` | Controls the layers used in the power grid. Depending on whether the design is the core of the chip or a macro inside the core. 1=Is a Core, 0=Is a Macro <br> (Default: `1`)|
| `FP_PIN_ORDER_CFG` | Points to the pin order configuration file to set the pins in specific directions (S, W, E, N). Check this [file][0] as an example. If not set, then the IO pins will be placed based on one of the other methods depending on the rest of the configurations. <br> (Default: NONE)|
| `FP_CONTEXT_DEF` | Points to the parent DEF file that includes this macro/design and uses this DEF file to determine the best locations for the pins. It must be used with `FP_CONTEXT_LEF`, otherwise it's considered non-existing. If not set, then the IO pins will be placed based on one of the other methods depending on the rest of the configurations. <br> (Default: NONE)|
| `FP_CONTEXT_LEF` | Points to the parent LEF file that includes this macro/design and uses this LEF file to determine the best locations for the pins. It must be used with `FP_CONTEXT_DEF`, otherwise it's considered non-existing. If not set, then the IO pins will be placed based on one of the other methods depending on the rest of the configurations. <br> (Default: NONE)|
| `FP_DEF_TEMPLATE` | Points to the DEF file to be used as a template when running `apply_def_template`. This will be used to exctract pin names, locations, shapes -excluding power and ground pins- as well as the die area and replicate all this information in the `CURRENT_DEF`. |
| `VDD_NETS` | Specifies the power nets/pins to be used when creating the power grid for the design. |
| `GND_NETS` | Specifies the ground nets/pins to be used when creating the power grid for the design. |
| `SYNTH_USE_PG_PINS_DEFINES` | Specifies the power guard used in the verilog source code to specify the power and ground pins. This is used to automatically extract `VDD_NETS` and `GND_NET` variables from the verilog, with the assumption that they will be order `inout vdd1, inout gnd1, inout vdd2, inout gnd2, ...`. |

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
                
```bash
%run_floorplan
[INFO]: PDN generation was successful.
[INFO]: Changing layout from /openLANE_flow/designs/picorv32a/runs/28-01_10-00/results/floorplan/picorv32a.floorplan.def to /openLANE_flow/designs/picorv32a/runs/28-01_10-00/tmp/floorplan/7-pdn.def

```
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
The partial content of `picorv32a.floorplan.def` is as shown. 

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
For this we need the `tech file`, `LEf file` and `DEF file`. `/home/kunalg123/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech` is the location tech file. `/home/kunalg123/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-01_11-22/tmp/merged.lef` is the location of the LEF file and DEF file is present in the floorplan folder.
```
‌‌prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-01_11-22/results/floorplan$ magic -T /home/kunalg123/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
<div align="center">
<img width="534" alt="magic" src="https://user-images.githubusercontent.com/104830557/214843831-d8d3eb80-52ae-431b-8c9c-5673a6629b07.png">
<img width="486" alt="tcon" src="https://user-images.githubusercontent.com/104830557/214843881-6e307a0d-fc51-451c-aae6-e7b5b3e05b4f.png">
</div>
<div align="center">
<img width="605" alt="magiczoom" src="https://user-images.githubusercontent.com/104830557/215268066-2757b4aa-91d7-4ede-afff-7340895dbab3.png">
</div>
<div align="center">
<img width="800" alt="what_tkcon" src="https://user-images.githubusercontent.com/104830557/215285493-d365c166-e381-4559-bb79-762bd2860f0d.png">
</div>
<div align="center">
<img width="603" alt="decap cells" src="https://user-images.githubusercontent.com/104830557/215285574-30b2d7c9-bf22-4ac6-b307-6630c45b1cdf.png">
</div>
<div align="center">
<img width="602" alt="unplaced cells" src="https://user-images.githubusercontent.com/104830557/215285581-c7d9f8eb-344c-4363-8f36-f9ddc84df41d.png">
</div>

### Day 3
## Cell Characterization
**Cell characterization** is the process of measuring the electrical characteristics of a digital logic cell. This includes measuring the cell's performance parameters such as delay, power consumption, and noise margins, under different conditions such as different input patterns and process variations. The results of cell characterization are used to create a library of cell models that can be used in the design and simulation of digital circuits.

During the characterization process, the cell's input and output waveforms are measured using testbenches and the results are used to create a model that describes the cell's behavior under different conditions. The model is then used to predict the cell's performance in the overall circuit design, allowing designers to optimize the circuit's performance and ensure that it meets the required specifications. Cell characterization is an important step in the VLSI design flow, as it allows designers to create accurate models of the cells that will be used in the final circuit, and thus can help to improve the yield and reliability of the final product.
```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane$git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```
```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic$ cp sky130A.tech ../../../../openlane/vsdstdcelldesign/
```
```
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
```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign$ magic -T sky130.tech sky130_inv.mag 
```
<div align="center">
<img width="170" alt="vsdstdcell" src="https://user-images.githubusercontent.com/104830557/215286280-5d2e9b10-8de3-4136-ae65-d1496fc1a72f.png">
</div>

<div align="center">
<img width="737" alt="spiceextract" src="https://user-images.githubusercontent.com/104830557/215286745-8feb5cfa-fd97-43a0-8702-fed85d8e96c2.png">
</div>

```‌
‌prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign$ ls -ltr
total 188
-rw-rw-r-- 1 prajwalita17 prajwalita17  13525 Jan 29 00:07 README.md
-rw-rw-r-- 1 prajwalita17 prajwalita17  11357 Jan 29 00:07 LICENSE
drwxrwxr-x 2 prajwalita17 prajwalita17   4096 Jan 29 00:07 Images
drwxrwxr-x 2 prajwalita17 prajwalita17   4096 Jan 29 00:07 extras
drwxrwxr-x 2 prajwalita17 prajwalita17   4096 Jan 29 00:07 libs
-rw-rw-r-- 1 prajwalita17 prajwalita17   2716 Jan 29 00:07 sky130_inv.mag
-rwxr-xr-x 1 prajwalita17 prajwalita17 136710 Jan 29 00:11 sky130A.tech
-rw-rw-r-- 1 prajwalita17 prajwalita17   1425 Jan 29 00:40 sky130_inv.ext
-rw-rw-r-- 1 prajwalita17 prajwalita17    353 Jan 29 00:41 sky130_inv.spice
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
ngspice 1 -> 
```

```
* SPICE3 file created from sky130_inv.ext - technology: sky130A
  
.option scale=0.01u
.include ./libs/pshort.lib
.include ./libs/nshort.lib

//.subckt sky130_inv A Y VPWR VGND
M0 Y A VGND VGND nshort_model.0 ad=1435 pd=152 as=1365 ps=148 w=35 l=23
M1 Y A VPWR VPWR pshort_model.0 ad=1443 pd=152 as=1517 ps=156 w=17 l=23
VDD VPWR 0 3.3V
VSS VGND 0 0V
Va A VGND PULSE(0 3.3V 0 0.1ns 0.1ns 2ns 4ns)


C0 A Y 0.05fF
C1 VPWR Y 0.11fF
C2 VPWR A 0.07fF
C3 Y VGND 0.24fF
C4 VPWR VGND 0.59fF
//.ends
.tran 1n 20n
.control
run
.endc
.end
```

```‌
ngspice 1 -> plot y vs time a
```

<div align="center">
<img width="299" alt="inv tran" src="https://user-images.githubusercontent.com/104830557/215303389-c5b686ea-202b-47cf-84ab-7bae3bae4db6.png">
</div>


```
* SPICE3 file created from sky130_inv.ext - technology: sky130A
  
.option scale=0.01u
.include ./libs/pshort.lib
.include ./libs/nshort.lib

//.subckt sky130_inv A Y VPWR VGND
M0 Y A VGND VGND nshort_model.0 ad=1435 pd=152 as=1365 ps=148 w=35 l=23
M1 Y A VPWR VPWR pshort_model.0 ad=1443 pd=152 as=1517 ps=156 w=17 l=23
VDD VPWR 0 3.3V
VSS VGND 0 0V
Va A VGND PULSE(0 3.3V 0 0.1ns 0.1ns 2ns 4ns)


C0 A Y 0.05fF
C1 VPWR Y 0.11fF
C2 VPWR A 0.07fF
C3 Y VGND 2fF
C4 VPWR VGND 0.59fF
//.ends
.tran 1n 20n
.control
run
.endc
.end
```

```‌
ngspice 1 -> plot y vs time a
```

<div align="center">
<img width="302" alt="inv tran with cl 2fF" src="https://user-images.githubusercontent.com/104830557/215303687-d9f4cb79-5f8d-450e-b3fb-cf0f7062e1dc.png">
</div>

To characerize the cell, we find the following parameters.
- Rise time - The time taken for the output to rise from 20% to 80% of its final value.
- Fall time - The time taken for the output to fall from 20% to 80% of its final value.
- Rise cell delay - The 
- Fall cell delay

Let us calculate these parameters for inv cell with output capacitance of 2fF.

Running ngspice simulations and measuring the parameters for inverter circuit for the two cases a. $W_p$ = 2 $W_n$ and b. $W_p$ = 3 $W_n$, we get the following results.
| Parameter     | Inverter circuit with $W_p$ = 2 $W_n$|           Inverter circuit with $W_p$ = 3 $W_n$              |
|---------------|--------------------------------------|-------------------------|
| | The core utilization percentage. <br> (Default: `50` percent)|
| `FP_ASPECT_RATIO`  | The core's aspect ratio (height / width). <br> (Default: `1`)|
| `FP_SIZING`  | Whether to use relative sizing by making use of `FP_CORE_UTIL` or absolute one using `DIE_AREA`. <br> (Default: `"relative"` - accepts "absolute" as well)|
| `DIE_AREA`  | Specific die area to be used in floorplanning. Specified as a 4-corner rectangle. Units in mm <br> (Default: unset)|



What is the effect of increase in W/L ratio on fall cell delay ?

An increase in the width-to-length (W/L) ratio of a MOSFET transistor typically results in a decrease in the fall time of the cell delay. This is because a larger W/L ratio leads to a larger transconductance, which in turn leads to a faster rate of change in the drain current, and thus a faster fall time. However, it is important to note that there are other factors that can affect the fall time of a cell, such as the parasitic capacitances and the resistance of the interconnects, and these factors should also be considered when designing a circuit.

What is the effect of increase in W/L ratio on rise cell delay ?
An increase in the width-to-length (W/L) ratio of a MOSFET transistor will typically result in an increase in the rise time of the cell delay. This is because a larger W/L ratio leads to a larger parasitic drain-to-source capacitance, which must be charged up before a significant increase in the drain current can occur. Therefore, the larger parasitic capacitance leads to a slower rate of change in the drain current, and thus a slower rise time. However, as with fall time, it is important to note that other factors, such as the resistance of the interconnects, can affect the rise time of a cell, so these should also be considered when designing a circuit.

What is the effect of increase in W/L ratio on rise time ?
An increase in the width-to-length (W/L) ratio of a MOSFET transistor will typically result in a decrease in the rise time. This is because a larger W/L ratio leads to a larger transconductance, which in turn leads to a faster rate of change in the drain current. This results in a faster rise time since it takes less time for the gate-source voltage to charge up the gate-drain capacitance. Additionally, as the W/L ratio increases, the drain-source resistance decreases resulting in a faster current flow and thus faster rise time. However, it is important to keep in mind that other factors, such as the parasitic capacitances and the resistance of the interconnects, can also affect the rise time, so these should also be considered when designing a circuit.

What is the effect of increase in W/L ratio on fall time ?
An increase in the width-to-length (W/L) ratio of a MOSFET transistor will typically result in a decrease in the fall time. This is because a larger W/L ratio leads to a larger transconductance, which in turn leads to a faster rate of change in the drain current. This results in a faster fall time since it takes less time for the drain current to decrease when the gate-source voltage is decreased. Additionally, as the W/L ratio increases, the drain-source resistance decreases resulting in a faster current flow and thus faster fall time. However, it is important to keep in mind that other factors, such as the parasitic capacitances and the resistance of the interconnects, can also affect the fall time, so these should also be considered when designing a circuit.






