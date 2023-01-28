# Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK
This repository contains a step by step procedure to the complete RTL2GDSII flow of PICORV32A RISC-V core design using open-source EDA tool OpenLANE and Google-Skywater’s first manufacturable open source 130nm PDK.
## LAB WORK

### Understanding the Input and Output Files
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
 ### Design Synthesis and Results
 
   The first step in OpenLANE flow is RTL Synthesis of the design loaded. This is done using the following command.
   
    run_synthesis
From the report post-synthesis, we get the following statistics.

```bash
%run_synthesis
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
     .
     .
     .
     sky130_fd_sc_hd__buf_1       1656
     sky130_fd_sc_hd__buf_2          8
     sky130_fd_sc_hd__conb_1        42
     sky130_fd_sc_hd__dfxtp_2     1613
     sky130_fd_sc_hd__inv_2       1615
     sky130_fd_sc_hd__mux2_1      1224
     .
     .
     .
   Chip area for module '\picorv32a': 147712.918400
     .
     .
     .
[INFO]: Changing netlist from 0 to /openLANE_flow/designs/picorv32a/runs/26-01_10-26/results/synthesis/picorv32a.synthesis.v
[INFO]: Running Static Timing Analysis...
[INFO]: current step index: 2
     .
     .
[INFO]: Synthesis was successful‌
```
#### Calculation of Flop ratio

From the report we can see that
Number of cells = 14876

Number of flip flops (sky130_fd_sc_hd__dfxtp_2) = 1613

Flip flop ratio =1613/14876 = 10.84 %

### Synthesis Variables

| Variable      | Description                                                   |
|---------------|---------------------------------------------------------------|
| `LIB_SYNTH` | The library used for synthesis by yosys. <br> (Default: `$::env(PDK_ROOT)/$::env(PDK)/libs.ref/$::env(STD_CELL_LIBRARY)/lib/sky130_fd_sc_hd__tt_025C_1v80.lib`)|
| `SYNTH_BIN` | The yosys binary used in the flow. <br> (Default: `yosys`) |
| `SYNTH_DRIVING_CELL`  | The cell to drive the input ports. <br>(Default: `sky130_fd_sc_hd__inv_8`)|
| `SYNTH_DRIVING_CELL_PIN`  | The name of the SYNTH_DRIVING_CELL output pin. <br>(Default: `Y`)|
| `SYNTH_CAP_LOAD` | The capacitive load on the output ports in femtofarads. <br> (Default: `17.65` ff)|
| `SYNTH_MAX_FANOUT`  | The max load that the output ports can drive. <br> (Default: `5` cells) |
| `SYNTH_MAX_TRAN` | The max transition time (slew) from high to low or low to high on cell inputs in ns. Used in synthesis <br> (Default: Calculated at runtime as `10%` of the provided clock period, unless this exceeds a set DEFAULT_MAX_TRAN, in which case it will be used as is). |
| `SYNTH_STRATEGY` | Strategies for abc logic synthesis and technology mapping <br> Possible values are `DELAY/AREA 0-3/0-2`; the first part refers to the optimization target of the synthesis strategy (area vs. delay) and the second one is an index. <br> (Default: `AREA 0`)|
| `SYNTH_BUFFERING` | Enables abc cell buffering <br> Enabled = 1, Disabled = 0 <br> (Default: `1`)|
| `SYNTH_SIZING` | Enables abc cell sizing (instead of buffering) <br> Enabled = 1, Disabled = 0 <br> (Default: `0`)|
| `SYNTH_READ_BLACKBOX_LIB` | A flag that enable reading the full(untrimmed) liberty file as a blackbox for synthesis. Please note that this is not used in technology mapping. This should only be used when trying to preserve gate instances in the rtl of the design.  <br> Enabled = 1, Disabled = 0 <br> (Default: `0`)|
| `SYNTH_NO_FLAT` | A flag that disables flattening the hierarchy during synthesis, only flattening it after synthesis, mapping and optimizations. <br> Enabled = 1, Disabled = 0 <br> (Default: `0`)|
| `SYNTH_SHARE_RESOURCES` | A flag that enables yosys to reduce the number of cells by determining shareable resources and merging them. <br> Enabled = 1, Disabled = 0 <br> (Default: `1`)|
| `SYNTH_ADDER_TYPE` | Adder type to which the $add and $sub operators are mapped to. <br> Possible values are `YOSYS/FA/RCA/CSA`; where `YOSYS` refers to using Yosys internal adder definition, `FA` refers to full-adder structure, `RCA` refers to ripple carry adder structure, and `CSA` refers to carry select adder. <br> (Default: `YOSYS`)|
| `LIB_SLOWEST` | Points to the lib file, corresponding to the slowest corner, for max delay calculation during STA. <br> (Default: `$::env(PDK_ROOT)/$::env(PDK)/libs.ref/$::env(STD_CELL_LIBRARY)/lib/sky130_fd_sc_hd__ff_n40C_1v95.lib`) |
| `LIB_FASTEST` | Points to the lib file, corresponding to the fastest corner, for min delay calculation during STA. <br> (Default: `$::env(PDK_ROOT)/$::env(PDK)/libs.ref/$::env(STD_CELL_LIBRARY)/lib/sky130_fd_sc_hd__ss_100C_1v60.lib`) |
| `LIB_TYPICAL` | Library used for typical delay calculation during STA. <br> (Default`LIB_SYNTH`) |
| `CLOCK_BUFFER_FANOUT` | Fanout of clock tree buffers. <br> (Default: `16`) |
| `ROOT_CLK_BUFFER` | Root clock buffer of the clock tree. <br> (Default: `sky130_fd_sc_hd__clkbuf_16`) |
| `CLK_BUFFER` | Clock buffer used for inner nodes of the clock tree. <br> (Default: `sky130_fd_sc_hd__clkbuf_4`) |
| `CLK_BUFFER_INPUT` | Input pin of the clock tree buffer. <br> (Default: `A`) |
| `CLK_BUFFER_OUTPUT` | Output pin of the clock tree buffer. <br> (Default: `X`) |
| `BASE_SDC_FILE` | Specifies the base sdc file to source before running Static Timing Analysis. <br> (Default: `$::env(OPENLANE_ROOT)/scripts/base.sdc`) |
| `VERILOG_INCLUDE_DIRS` | Specifies the verilog includes directories. <br> Optional. |
| `SYNTH_FLAT_TOP` | Specifies whether or not the top level should be flattened during elaboration. 1 = True, 0= False <br> Default: `0`. |
| `IO_PCT` | Specifies the percentage of the clock period used in the input/output delays. Ranges from 0 to 1.0. <br> (Default: `0.2`) |

## Floorplanning
Once the synthesis step is done, the next step is floorplan. 
                
```bash
%run_floorplan
[INFO]: PDN generation was successful.
[INFO]: Changing layout from /openLANE_flow/designs/picorv32a/runs/26-01_11-22/results/floorplan/picorv32a.floorplan.def to /openLANE_flow/designs/picorv32a/runs/26-01_11-22/tmp/floorplan/7-pdn.def

```
```bash
‌‌prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-01_11-22/results/floorplan$ magic -T /home/kunalg123/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
<div align="center">
<img width="534" alt="magic" src="https://user-images.githubusercontent.com/104830557/214843831-d8d3eb80-52ae-431b-8c9c-5673a6629b07.png">
<img width="486" alt="tcon" src="https://user-images.githubusercontent.com/104830557/214843881-6e307a0d-fc51-451c-aae6-e7b5b3e05b4f.png">
</div>
### PicoRV32 design  and Libraries
### Design Preparation
### RTL Synthesis
### Floorplan
### Placement
