# OpenROAD_7nm Physical Design Contest by VSD (VLSI System Design) & UCSD (University of California San Diego)
The repository reflects my learnings and concepts I understood during the OpenROAD Design Contest. Details for the contest can be found on (https://www.openroaddesigncontest.org/)

## About OpenROAD and it's Flow Controllers (OpenROAD Flow Scripts and OpenLANE)
**ORFS** is a short form for OpenROAD Flow Scripts. **OpenROAD flow scripts** and **OpenLANE** are flow-controllers supported by the OpenROAD Design project repository.  
ORFS provides a collection of open-source tools required for automating the digital design from synthesis to layout for complete RTL to GDSII flow, includes Synthesis, Placement and Routing (PnR), Static Timing Analysis (STA), Design Rule check (DRC) and Layout versus Schematic (LVS) check.

**OpenROAD** is a foundational application for building designs from complete RTL-to-GDSII flow which is built entirely on open-source tools for semiconductor digital designs. It is an integrated chip design which takes a design from synthesis, floorplanning, placement, routing, sign-off parasitic extraction, timing analysis leading to a final GDSII layout.
It is also a tool that focuses on delivering autonomous, no-human intervention in-loop and 24 hour turnaround for RTL to GDSII for design exploration and physical design implementation. The placement algorithm works on minimizing the wire length, improving the timing, area and power consumption. Since it’s an open-source design, it allows the users to add their own algorithm if optimized and update more features to it.

## The Design Flow and Tools utilized in OpenROAD Flow Scripts: 
**Verilog** is used as a basic input method to do the design entries for any specific design project.  
And once ORFS is installed, we can specify the design parameters like technology node, the design constraints and tools settings to meet the user’s design projects.  
And then, for **Synthesis to Layout** process, below tools were used,  
* Yosys and ABC for Synthesis  
* Capo and RePIAce for Floorplanning  
* OpenROAD for Placement, which is also a plugin for Physical Design Stage, that can be configured and customized  to meet specific design projects  
* FastRoute and TritonRoute for Routing purpose  
* Magic for layout corrections and layout verification  
* Magic and kLayout for final GDSII Layout file  

More details on the OpenROAD flow design can be found [here](https://openroad.readthedocs.io/en/latest/main/README.html#build-using-support-script).  
The tutorial to run the complete flow from RTL to GDSII using OpenROAD flow scripts can be found [here](https://openroad-flow-scripts.readthedocs.io/en/latest/tutorials/FlowTutorial.html).  

## Installation of ORFS tool on your system  
You can also find the resource for the steps of installation [here](https://openroad-flow-scripts.readthedocs.io/en/latest/user/BuildLocally.html). I have demonstrated the steps I took for installation below.  
I use Windows by default on my system. But, Since ORFS Supports Linux based systems, I have downloaded and configured the Ubuntu 22.04 OS on a Virtual machine platform called Virtual Box.  
Other supported Linux based OS are Centos7, Ubuntu 20.04, Ubuntu 22.04, RHEL 8, Debian 10 and Debian 11. To correctly install the dependencies with the script you need to run as root or prepend sudo to the commands below.  

After logging to the Ubuntu 22.04 OS, follow the below steps,  
Clone Repository of ORFS  

    git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts

Change directory to the ORFS folder and install all the dependencies using the script.  
The script installs all the required dependencies and packages for the OpenROAD flow.  

    cd OpenROAD-flow-scripts
    sudo ./etc/DependencyInstaller.sh

Now, for CentOS 7 users, run below two commands before moving to further to build an openroad script.  

    source /opt/rh/devtoolset-8/enable
    source /opt/rh/llvm-toolset-7.0/enable

> Note: If you are Ubuntu or Debian OS versions, skip the above steps.  

Next is to build and install the OpenROAD tool  

    ./build_openroad.sh --local

To verify the installation, you can run below and check  

    source ./setup_env.sh
    yosys -help
    openroad -help
    exit

> Note: Every time when you open the new terminal, you need to source the setup_env.sh file  

## Directory Structure and File Formats in OpenROAD Flow Scripts: 
It is recommended strictly to not rename any files and directories in the OpenROAD-flow-scripts directory as it may give compilation errors in future.
You can add new designs inside the "flow" directory with the required directory structures. I will give a brief overview of the directories inside ORFS - 
* docker - For the docker based installation, it has a Dependency Installer Script which installs all the required tools and packages for ORFS design.  
* docs - It contains basis documentation or tutorial for the flow. Here is a direct link for the [Flow Tutorial](https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts/blob/master/docs/tutorials/FlowTutorial.md)  
* flow - Contains all the required files responsible to compile complete RTL to GDSII flow including Synthesis, Floorplanning, Placement, Routing, Parasitic Extraction and Timing.  
* etc - This is a main directory when it comes to new installation of ORFS. It contains a Dependency Installer, which takes care all the installation resources for design.  
* jenkins - Contains the regression test files for build updates.  
* tools - Contains all the required tools for running complete RTL to GDSII flow as listed above.  
* setup_env.sh - This is a source file which sources all the openroad tools to run full RTL to GDSII flow.
* build_openroad.sh -  To build the openroad tools.  

## Updating OpenROAD Flow Scripts
Keeping the repository up-to-date is better as it keeps changing and updating by the contributors for improvements and optimization of the tool scripts

    cd OpenROAD-flow-scripts
    git checkout master
    git pull
    git submodule update
    ./build_openroad.sh --local --clean

## How to run the processor designs with desired supported PDKs to perform the RTL to GDSII flow?
OpenROAD has made it way easier to run the designs for testing the performance metrics of any design. You just need to modify the **MakeFile** in the flow directory.
There are several designs already present in the Makefile configured for running on different supported PDKs. The default PDK is set as 'NANGATE45' to run the 'gcd' processor design.  
To run the design, simply enter below command,  

    make
    
If you wish to run some other design with different PDK, you can comment the default NANGATE45 based design line 'DESIGN_CONFIG ?= ./designs/nangate45/gcd/config.mk' as below,

    #DESIGN_CONFIG ?= ./designs/nangate45/gcd/config.mk

And uncommment the design you wish to run to perform the full RTL to GDSII flow for the same.  
For example, if I want to run 'swerv_wrapper' processor using 'TSMC651P' PDK
Uncomment the line 'DESIGN_CONFIG=./designs/tsmc65lp/swerv_wrapper/config.mk' in the Makefile.

    # DESIGN_CONFIG=./designs/tsmc65lp/jpeg/config.mk
    # DESIGN_CONFIG=./designs/tsmc65lp/swerv/config.mk
    DESIGN_CONFIG=./designs/tsmc65lp/swerv_wrapper/config.mk
    # DESIGN_CONFIG=./designs/tsmc65lp/tinyRocket/config.mk
    # DESIGN_CONFIG=./designs/tsmc65lp/vanilla5/config.mk

And then run make. That's it.  

