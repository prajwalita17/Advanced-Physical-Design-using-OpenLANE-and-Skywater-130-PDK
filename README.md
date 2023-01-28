# Advanced-Physical-Design-using-OpenLANE-and-Skywater-130-PDK
This repository contains a step by step procedure to the complete RTL2GDSII flow of PICORV32A RISC-V core design using open-source EDA tool OpenLANE and Google-Skywater’s first manufacturable open source 130nm PDK.
## LAB WORK

### Understanding the Input and Output Files

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
The `sky130_fd_sc_hd` consists of various libraries for different process, voltage and temperature (PVT) corners. For example, `sky130_fd_sc_hd__ss_n40C_1v44.lib` corresponds to a PVT corner have slow(NMOS) slow(PMOS) process, -40^o C temperature and 1.44 V operating temperature.

```
prajwalita17@vsd-pd-workshop-05:~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.ref/sky130_fd_sc_hd$ ls lib -ltr
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

### PicoRV32 design  and Libraries
### Design Preparation
### RTL Synthesis
### Floorplan
### Placement
