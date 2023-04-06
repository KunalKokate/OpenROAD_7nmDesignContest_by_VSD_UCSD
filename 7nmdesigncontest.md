# Timing Improvement for CTS Stage and Overall RTL to GDSII flow on ASAP7 PDK
### Aim: 
To demonstrate fastest runtime for overall RTL to GDSII flow and improve the timing parameters below by making changes in CTS script:
1.	Overall Performance Elapsed Runtime
2.	CPU User Time
3.	CTS Elapsed Seconds taken to run CTS Stage
All the parameters to look are highlighted in bold.
#### Description: 
This repository reflects the default and updated performance runtimes of current designs without any changes made. I plan to improve the overall runtime for a design by modifying the Clock Tree Synthesis TCL script.  
The idea is to modify the OpenROAD scripts for improvement of CTS timings. To do so, I have updated the scripts/cts.tcl file to improve the Overall Performance Elapsed Runtime, CPU User Time, and CTS Elapsed Seconds to run CTS Stage

Quick link to refer the [documentation](https://github.com/KunalKokate/OpenROAD-flow-scripts/tree/7nmcontest/flow/Contest%20Documentation) of the same in PDF format.

Operating System - On VSD Cloud Platform

        kunalkokate03@openroad-on-cloud-04:~$ cat /etc/os-release 
        NAME="Ubuntu"
        VERSION="18.04.6 LTS (Bionic Beaver)"
        ID=ubuntu
        ID_LIKE=debian
        PRETTY_NAME="Ubuntu 18.04.6 LTS"
        VERSION_ID="18.04"
        HOME_URL="https://www.ubuntu.com/"
        SUPPORT_URL="https://help.ubuntu.com/"
        BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
        PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
        VERSION_CODENAME=bionic
        UBUNTU_CODENAME=bionic

### Steps taken: 
* Login to VSD Cloud Platform
* Open Terminal >> cd
* Create a new directory >> mkdir `contest` >> git clone `<forked repo from Original OpenROAD flow scripts>` (https://github.com/KunalKokate/OpenROAD-flow-scripts)  
* Navigate to OpenROAD-flow-scripts/flow/scripts
* To edit >> Run >> gedit `cts.tcl`
* Updated the process user settings code `if` loop with `foreach` loop to simplify and optimize the code to see overall runtime improvement.

### Before: During Test 1  
![image](https://user-images.githubusercontent.com/42606968/228985743-7df111e7-dd3e-42c9-9c80-560e88d7819c.png)  
**Code:** 

    if { [info exists ::env(SETUP_SLACK_MARGIN)] && $::env(SETUP_SLACK_MARGIN) > 0.0} {
      puts "Setup slack margin $::env(SETUP_SLACK_MARGIN)"
      append additional_args " -setup_margin $::env(SETUP_SLACK_MARGIN)"
    }
    if { [info exists ::env(HOLD_SLACK_MARGIN)] && $::env(HOLD_SLACK_MARGIN) > 0.0} {
      puts "Hold slack margin $::env(HOLD_SLACK_MARGIN)"
      append additional_args " -hold_margin $::env(HOLD_SLACK_MARGIN)"
    }

### After: During Test 2  
![image](https://user-images.githubusercontent.com/42606968/228985750-8e35df62-3348-4bb9-bf5c-84bc8edad963.png)  
**Code:**

    foreach var {SETUP_SLACK_MARGIN HOLD_SLACK_MARGIN} {
      if {[info exists ::env($var)] && $::env($var) > 0.0} {
        puts "${var} slack margin $::env($var)"
        append additional_args " -${var:l}_margin $::env($var)"
      }
    }

This code uses a foreach loop to iterate over the environment variables `SETUP_SLACK_MARGIN` and `HOLD_SLACK_MARGIN`. Inside the loop, it checks if the variables exist and their values are greater than `0.0`. If they do, it prints a message and appends their values to the `additional_args` variable using the append command. 
> Note that ${var:l} converts the first letter of the variable name to lowercase and appends the string _margin to it.

This code eliminates the need for separate if statements, and thus simplifies the code.

* Save the file.
* And proceed to run all designs as shown below.
---
# Test cases for three designs: GCD, IBEX and RISCV32i
## 1. GCD design
### TEST 1: Runtimes of the default GCD Design
#### Overall Performance time
`Elapsed time: 0:01.94` [h:]min:sec. `CPU time: user 1.55` sys 0.12 (86%). Peak memory: 322560KB.  
cp results/asap7/gcd/base/6_1_merged.gds results/asap7/gcd/base/6_final.gds  

    Log                       Elapsed seconds  
    3_4_resizer                      204  
    2_3_tdms_place                   126  
    6_1_merge                        154  
    5_2_TritonRoute                 2601  
    3_1_place_gp_skip_io             160  
    2_4_mplace                       136  
    1_1_yosys                        335
    5_1_fastroute                    154
    3_5_opendp                       147
    3_3_place_gp                     298
    2_2_floorplan_io                 156
    6_report                         381
    2_5_tapcell                      136
    2_1_floorplan                    153
    3_2_place_iop                    194
    4_1_cts                          381 <-- To be improved.
    4_2_cts_fillcell                 140
    2_6_pdn                          161

 ![image](https://user-images.githubusercontent.com/42606968/228982365-89686e5b-9042-462f-94fc-1d83fafaff06.png)

#### Timing report from 4_1_cts.log
`Elapsed time: 0:06.21`[h:]min:sec. `CPU time: user 5.47` sys 0.06 (88%). Peak memory: 196980KB.

### TEST 2: Runtimes after making changes to CTS TCL script in ORFS  
#### Overall Performance time
`Elapsed time: 0:01.61`[h:]min:sec. `CPU time: user 1.25` sys 0.12 (85%). Peak memory: 322660KB.
cp results/asap7/gcd/base/6_1_merged.gds results/asap7/gcd/base/6_final.gds

        Log                       Elapsed seconds
        3_4_resizer                      148
        2_3_tdms_place                   112
        6_1_merge                        121
        5_2_TritonRoute                 2313
        3_1_place_gp_skip_io             120
        2_4_mplace                       118
        1_1_yosys                        246
        5_1_fastroute                    131
        3_5_opendp                       128
        3_3_place_gp                     182
        2_2_floorplan_io                 125
        6_report                         414
        2_5_tapcell                      117
        2_1_floorplan                    143
        3_2_place_iop                    139
        4_1_cts                          283 <-- CTS Stage Elapsed Seconds time taken to run CTS Stage for GCD Design, found to be improved.
        4_2_cts_fillcell                 118
        2_6_pdn                          122
        
![image](https://user-images.githubusercontent.com/42606968/228986912-c820ba87-938a-4a9a-b59e-146c522b9c5f.png)  

#### Timing report from 4_1_cts.log
`Elapsed time: 0:04.43`[h:]min:sec. `CPU time: user 4.36` sys 0.06 (99%). Peak memory: 197336KB.


## 2. IBEX Design
### TEST 1: Runtimes of the default IBEX Design
#### Overall Performance Runtime

`Elapsed time: 0:03.58`[h:]min:sec. `CPU time: user 3.37` sys 0.15 (98%). Peak memory: 477116KB.

    Log                       Elapsed seconds
    3_4_resizer                     1202
    2_3_tdms_place                   131
    6_1_merge                        238
    5_2_TritonRoute                41126
    3_1_place_gp_skip_io             519
    2_4_mplace                       133
    1_1_yosys                       6099
    5_1_fastroute                   2293
    3_5_opendp                       517
    3_3_place_gp                    4052
    2_2_floorplan_io                 150
    6_report                        4290
    2_5_tapcell                      137
    2_1_floorplan                    262
    3_2_place_iop                    133
    4_1_cts                         6983 <--- to be improved
    4_2_cts_fillcell                 123
    2_6_pdn                          133

![image](https://user-images.githubusercontent.com/42606968/228983391-7e695508-09df-4bea-bdcb-d663800ffdf4.png)

#### Timing report from 4_1_cts.log
`Elapsed time: 1:56.23`[h:]min:sec. `CPU time: user 116.06` sys 0.17 (100%). Peak memory: 296996KB.

### TEST 2: Runtimes after making changes to CTS TCL script in ORFS
#### Overall Performance Runtime
`Elapsed time: 0:03.07`[h:]min:sec. `CPU time: user 2.94` sys 0.13 (100%). Peak memory: 477272KB.
cp results/asap7/ibex/base/6_1_merged.gds results/asap7/ibex/base/6_final.gds

        Log                       Elapsed seconds
        3_4_resizer                     1041
        2_3_tdms_place                   131
        6_1_merge                        187
        5_2_TritonRoute                37067
        3_1_place_gp_skip_io             494
        2_4_mplace                       126
        1_1_yosys                       5764
        5_1_fastroute                   2020
        3_5_opendp                       459
        3_3_place_gp                    3874
        2_2_floorplan_io                 127
        6_report                        3717
        2_5_tapcell                      122
        2_1_floorplan                    303
        3_2_place_iop                    126
        4_1_cts                         5613 <-- CTS Stage Elapsed Seconds time taken to run CTS Stage for IBEX Design, found to be improved.
        4_2_cts_fillcell                 146
        2_6_pdn                          150

![image](https://user-images.githubusercontent.com/42606968/228986866-5fd72459-b14e-4610-a775-940fcd8e479c.png)  

#### Timing report from 4_1_cts.log
`Elapsed time: 1:33.33`[h:]min:sec. `CPU time: user 93.20` sys 0.13 (100%). Peak memory: 297076KB

## 3. RISCV32i Design
### TEST 1: Runtimes of the default RISCV32i Design
#### Overall Performance Runtime
`Elapsed time: 0:03.01`[h:]min:sec. `CPU time: user 2.86` sys 0.15 (100%). Peak memory: 446068KB.
cp results/asap7/riscv32i/base/6_1_merged.gds results/asap7/riscv32i/base/6_final.gds

    Log                       Elapsed seconds
    3_4_resizer                      513
    2_3_tdms_place                   129
    6_1_merge                        181
    5_2_TritonRoute                28521
    3_1_place_gp_skip_io             131
    2_4_mplace                      9082
    1_1_yosys                       1951
    5_1_fastroute                    819
    3_5_opendp                       367
    3_3_place_gp                    2162
    2_2_floorplan_io                 150
    6_report                        2137
    2_5_tapcell                      147
    1_1_yosys_hier_report           2436
    2_1_floorplan                    209
    3_2_place_iop                    146
    4_1_cts                         2045 <-- To be improved.
    4_2_cts_fillcell                 125
    2_6_pdn                          147

![image](https://user-images.githubusercontent.com/42606968/228983294-23eedc26-ef36-4872-ba49-e1b1b24c5459.png)

#### Timing report from 4_1_cts.log
`Elapsed time: 0:34.05`[h:]min:sec. `CPU time: user 33.93` sys 0.11 (99%). Peak memory: 296856KB.

***

### TEST 2: Runtimes after making changes to CTS TCL script in ORFS
#### Overall Performance Runtime
`Elapsed time: 0:02.48`[h:]min:sec. `CPU time: user 2.38` sys 0.09 (99%). Peak memory: 445452KB.
cp results/asap7/riscv32i/base/6_1_merged.gds results/asap7/riscv32i/base/6_final.gds

    Log                       Elapsed seconds
    3_4_resizer                      497
    2_3_tdms_place                   121
    6_1_merge                        168
    5_2_TritonRoute                27441
    3_1_place_gp_skip_io             116
    2_4_mplace                      8111
    1_1_yosys                       1758
    5_1_fastroute                    793
    3_5_opendp                       365
    3_3_place_gp                    2136
    2_2_floorplan_io                 119
    6_report                        1804
    2_5_tapcell                      114
    1_1_yosys_hier_report           2435
    2_1_floorplan                    183
    3_2_place_iop                    117
    4_1_cts                         1824 <-- CTS Stage Elapsed Seconds time taken to run CTS Stage for RISCV32i Design, found to be improved.
    4_2_cts_fillcell                 150
    2_6_pdn                          136

![image](https://user-images.githubusercontent.com/42606968/228987075-de1e491a-3aec-4235-bdd0-265a5c58fadd.png)  

#### Timing report from 4_1_cts.log
`Elapsed time: 0:30.24`[h:]min:sec. `CPU time: user 30.12` sys 0.11 (99%). Peak memory: 296780KB.

***

## Quick links
* [Logs of unchanged design](https://github.com/KunalKokate/OpenROAD-flow-scripts/tree/7nmcontest/flow/Logs_of%20unchanged_design/logs/asap7)  
* [Script changes](https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts/commit/74e54ad036be324b81139d02c42bd494fcf3288a)  

## Conclusion and Future Scope
In conclusion, we were able to see the improvement by changing code in the TCL Script of OpenRoAD flow for CTS Stage to see the `Elapsed time of complete RTL to GDSII runtime`, `CPU user time` & `CTS Elapsed seconds`. Also, the timing reports from the `logs/asap7/<design>/base/4_1_cts.log` has also indicated the considerable improvement in the `CTS Elapsed Time` & `CPU time`.
There is huge scope for OpenROAD Design Flow Scripts in improvements of the designs flow in each stage and overall flow by optimizing the code complexity and reliability.

## Acknowledgement
1. OpenROAD Team
2. VSD (VLSI System Design Corporation Pvt. Ltd) and UCSD (University of California, San Deigo)
