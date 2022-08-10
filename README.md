# Physical-Design-Using-OpenLANE-SKY130
This repository contains all the information studied and created during the Advanced Physical Design Using OpenLANE / SKY130 workshop. It is primarily foucused on a complete RTL2GDS flow using the open-soucre flow named OpenLANE. PICORV32A RISC-V core design is used for the purpose.

# Table of Contents
  - [Introduction To RTL to GDSII Flow](#introduction-to-rtl-to-gdsii-flow)
  - [List of All Open-Source Tools Used](#list-of-all-open-source-tools-used)
  - [Day 1 - Inception of open-source EDA, OpenLANE and Sky130 PDK](#day-1---inception-of-open-source-eda-openlane-and-sky130-pdk)
    - [Basic IC Design Terminologies](#basic-ic-design-terminologies)
    - [Introduction To RISC-V](#introduction-to-risc-v)
    - [SoC Design and OpenLANE](#soc-design-and-openlane)
      - [Open-Source PDK Directory Structure](#open-source-pdk-directory-structure)
      - [What is OpenLANE](#what-is-openlane)
    - [Open-Source EDA Tools](#open-source-eda-tools)
      - [OpenLANE Initialization](#openlane-initialization)
      - [Design Preparation](#design-preparation)
      - [Design Synthesis and Results](#design-synthesis-and-results)
  - [Day 2 - Good floorplan vs bad floorplan and introduction to library cells](#day-2---good-floorplan-vs-bad-floorplan-and-introduction-to-library-cells)
    - [Chip Floorplanning](#chip-floorplanning)
      - [Utilization Factor and Aspect Ratio](#utilization-factor-and-aspect-ratio)
      - [Power Planning](#power-planning)
      - [Pin Placement](#pin-placement)
      - [Floorplan using OpenLANE](#floorplan-using-openlane)
      - [Review Floorplan Layout in Magic](#review-floorplan-layout-in-magic)
    - [Placement](#placement)
      - [Placement and Optimization](#placement-and-optimization)
      - [Placement using OpenLANE](#placement-using-openlane)
    - [Cell Design and Characterization Flows](#cell-design-and-characterization-flows)
      - [Characterization Flow](#characterization-flow)
  - [Day 3 - Design library cell using Magic Layout and ngspice characterization](#day-3---design-library-cell-using-magic-layout-and-ngspice-characterization)
    - [CMOS Inverter Design using Magic](#cmos-inverter-design-using-magic)
    - [Extract SPICE Netlist from Standard Cell Layout](#extract-spice-netlist-from-standard-cell-layout)
    - [Transient Analysis using NGSPICE](#transient-analysis-using-ngspice)
  - [Day 4 - Pre-layout timing analysis and importance of good clock tree](#day-4---pre-layout-timing-analysis-and-importance-of-good-clock-tree)
    - [Magic Layout to Standard Cell LEF](#magic-layout-to-standard-cell-lef)
    - [Timing Analysis using OpenSTA](#timing-analysis-using-opensta)
    - [Clock Tree Synthesis using TritonCTS](#clock-tree-synthesis-using-tritoncts)
  - [Day 5 - Final steps for RTL2GDS](#day-5---final-steps-for-rtl2gds)
    - [Generation of Power Distribution Network](#generation-of-power-distribution-network)
    - [Routing using TritonRoute](#routing-using-tritonroute)
    - [SPEF File Generation](#spef-file-generation)
  - [References](#references)
  - [Acknowledgement](#acknowledgement)
 
# Introduction To RTL to GDSII Flow
  RTL to GDSII Flow refers to all of the steps involved in converting a logical Register Transfer Level (RTL) Design to a fabrication ready GDSII format. GDSII is a database file format that is an industry standard for data exchange of IC layout artwork.
  The RTL to GSDII flow consists of following steps:
  - RTL Synthesis
  - Static Timing Analysis(STA)
  - Design for Testability(DFT)
  - Floorplanning
  - Placement
  - Clock Tree Synthesis(CTS)
  - Routing
  - GDSII Streaming
 
 All the steps are further discussed in details in the repository.
  

# List of All Open-Source Tools Used
  | Name of Tool | Application / Usage |
  | --- | --- |
  | [Yosys](https://github.com/YosysHQ/yosys) | Synthesis of RTL Design |
  | ABC | Mapping of Netlist |
  | [OpenSTA](https://github.com/The-OpenROAD-Project/OpenSTA) | Static Timing Analysis |
  | [OpenROAD](https://github.com/The-OpenROAD-Project/OpenROAD) | Floorplanning, Placement, CTS, Optimization, Routing |
  | [TritonRoute](https://github.com/The-OpenROAD-Project/TritonRoute) | Detailed Routing |
  | [Magic VLSI](http://opencircuitdesign.com/magic/) | Layout Tool |
  | [NGSPICE](https://github.com/imr/ngspice) | SPICE Extraction and Simulation |
  | SPEF_EXTRACTOR | Generation of SPEF file from DEF file |
  
  
# Day 1 - Inception of open-source EDA, OpenLANE and Sky130 PDK
 ## Basic IC Design Terminologies
  Several commonly used terminologies will be encountered during the Physical Designing process. Some of them are as follows:
- Package: A case that protects the circuit material from physical damage or corrosion and allows the electrical contacts connecting it to the printed circuit board to be mounted (PCB). The image below depicts an IC with 48 pins and a Quad Flat No-Leads (QFN) package.
- Die: A die is a small block of semiconducting material used to fabricate a specific functional circuit.
- Core: The actual area of the IC where the logic is located.
- Pads: These are the interfaces between a chip's internal signals and its external pins.
 
 ![image](https://user-images.githubusercontent.com/46182864/183931776-52ec8555-f9eb-4fa8-8a9d-2987081d1f04.png)
 
 ## Introduction To RISC-V
   RISC-V is a new ISA with open, free, and non-restrictive licencing. The RISC-V ISA introduces a new level of architectural freedom in terms of free, extensible software and hardware.
- It is much simpler and smaller than other commercial ISAs on the market.
- It avoids microarchitecture and features that are dependent on technology.
- It has a small standard base ISA as well as numerous standard extensions.
- It can encode variable-length instructions.

   
 ## SoC Design and OpenLANE
 ### Open-Source PDK Directory Structure
   The 'pdks/' directory contains all of the Process Design Kits (PDK). Along with the 'Sky130A,' we use a few other open-source PDKs, and other related files are included in the directory. The variable '$PDK ROOT' specifies the location of the PDK directory.
    
  
 ### What is OpenLANE
   [OpenLANE](https://github.com/efabless/openlane) is an automated RTL to GDSII flow that includes open-source components such as OpenROAD, Yosys, Magic, Fault, Netgen, and SPEF-Extractor. It also makes custom design exploration and optimization scripts easier to add.
   
   
   OpenLANE flow consists of several stages. By default all flow steps are run in sequence. Each stage may consist of multiple sub-stages. OpenLANE can also be run interactively as shown here.

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
  5. Routing *
      1. `FastRoute` - Performs global routing to generate a guide file for the detailed router
      2. `TritonRoute` - Performs detailed routing
      3. `SPEF-Extractor` - Performs SPEF extraction
  6. GDSII Generation
      1. `Magic` - Streams out the final GDSII layout file from the routed def
  7. Checks
      1. `Magic` - Performs DRC Checks & Antenna Checks
      2. `Netgen` - Performs LVS Checks
      
 ## Open-Source EDA Tools
 ### OpenLANE Initialization
   For invoking OpenLANE in Linux Ubuntu, we should first run the docker everytime we use OpenLANE. This is done by using the following script:
    
    docker
   
   A custom shell script or commands can be generated to make the task simpler.
   
    ./flow.tcl -interactive
    
   
 ### Design Preparation
   The first step after invoking OpenLANE is to import the openlane package of required version. This is done using following command. Here 0.9 is the required version of OpenLANE.
   
    package require openlane 0.9
    
   The next step is to prepare our design for the OpenLANE flow. This is done using following command:
       
    prep -design picorv32a
   
    
   
   During the design preparation process, the technology LEF and cell LEF files are merged to create a'merged.lef' file. The LEF file contains information such as layer information, design rules, and information about each standard cell needed for place and route.
    
 ### Design Synthesis and Results
   The first step in OpenLANE flow is RTL Synthesis of the design loaded. This is done using the following command.
   
    run_synthesis
   
  ![image](https://user-images.githubusercontent.com/46182864/183928695-6e194d94-f9de-4ddb-9a3a-b9305be95f4b.png)
   
# Day 2 - Good floorplan vs bad floorplan and introduction to library cells
 ## Chip Floorplanning
   The arrangement of logical blocks, library cells, and pins on a silicon chip is known as chip floorplanning. It ensures that each module has an appropriate area and aspect ratio, that every pin of the module is connected to other modules or the chip's periphery, and that modules are arranged in such a way that they consume less area on a chip.
   
 ### Utilization Factor and Aspect Ratio
   The Utilization Factor is the ratio of standard cell core area to total core area. The utilisation factor is typically kept between 0.5 and 0.7, or 50% and 60%. Keeping a proper utilisation factor allows for better placement and routing optimization.
   
 ### Power Planning
   Power planning is the process of creating a power grid network to distribute power evenly to each component of the design. This step addresses the unwelcome voltage drop and ground bounce. The resistance of the metal wires that comprise the power distribution network causes steady-state IR Drop. Stable-state IR Drop reduces both the speed and noise immunity of local cells and macros by decreasing the voltage difference between local power and ground.
   
 ### Pin Placement
   The position of the pin determines the timing delays and the number of buffers required, so pin placement is critical in floorplanning. There are several pin placement options available, including equidistant and high-density placement.
 
 ### Floorplan using OpenLANE
   Floorplanning in OpenLANE is done using the following command. 
    
    run_floorplan
   
   Successful floorplanning gives a `def` file as output. This file contains the die area and placement of standard cells.
   
  
 
 ### Review Floorplan Layout in Magic
   The Magic Layout Tool is used to visualise the layout after the floorplan has been created. The following three files are required to view a floorplan in Magic:
  1. Sky130A.tech Technology File
  2. LEF file that has been merged ('merged.lef')
  3. DEF Document     
       
    magic -T /home/f20180533/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read          picorv32a.floorplan.def & 
   
   
   ![image](https://user-images.githubusercontent.com/46182864/183928340-14100b29-58c2-4b78-a225-3fbc40fa173d.png)
 
 ## Placement
 ### Placement and Optimization
   After floorplanning, the next step is placement. The placement of each component on the die is determined by placement. Placement does not simply place the standard cells from the synthesised netlist. It also optimises the design, removing any timing violations caused by die relative placement.
   
 ### Placement using OpenLANE
   Placement in OpenLANE is done using the following command. 
    
    place_io
    global_placement_or
    detailed_placement
    tap_decap_or
    detailed_placement
   
   The DEF file created during floorplan is used as an input to placement. Placement in OpenLANE occurs in two stages:
   - Global Placement
   - Detailed Placement
   
   Placement is carried out as an iterative process till the value of overflow converges to 0.
   
    magic -T /home/f20180533/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def & 

   ![image](https://user-images.githubusercontent.com/46182864/183928127-5bde55d7-71a4-4465-af4e-f5c9af6ec70b.png)
   
 ## Cell Design and Characterization Flows
 ### Cell Design Flow
 
 ### Characterization Flow
  Standard Cells in polygon level format have a few issues (GDSII). Among them are:
- As is well known, functionality extraction is complicated and unnecessary. - As is well known, functionality extraction is complicated and unnecessary. 
- As is well known, functionality extraction is complicated and unnecessary. 
- Functional/Delay simulation takes far too long. 
- Power extraction for an entire chip takes far too long. 
- Automatic detection of timing constraints (e.g. Setup time) is difficult.

  Cell Characterization is a solution to the problems listed above. It is a straightforward model for delay, function, constraints, and power at the cell/gate level. The following stages comprise the Characterization Flow:
1. Netlist Extraction - Using special tools, transistors, resistances, and capacitances are extracted and saved as SPICE netlists (or similar)
2. Parameter specification - Library-wide parameters must be specified: For example, maximum Transition Time
3. Model selection and specification - The models used determine the necessary data.
4. Measurement - To obtain the required data, the cells are simulated using a SPICE-like tool.
5. Model Generation – The data collected is fed into the models.
6. Verification - Various checks are performed to ensure that the characterization is correct.
 
# Day 3 - Design library cell using Magic Layout and ngspice characterization
  Each Design is represented by an identical cell design. The Cell Library contains all of the standard cell designs. A completely unique cell design that complies with all rules can be added to the library. To begin, a CMOS inverter is designed in Magic Layout Tool and analysed with the NGSPICE tool.
  
 ## CMOS Inverter Design using Magic
  Magic Layout Tool is used to design the inverter. It accepts the technology file ('sky130A.tech' in this case) as input. The Magic tool provides a very simple interface for designing various layers of the layout. It also includes a DRC check fetaure.
The snippet below depicts a CMOS inverter layout with and without design rule violations.
  
  ![image](https://user-images.githubusercontent.com/46182864/183922310-af585a99-9740-4341-ac8c-6661b2e4e95a.png)

  
 ## Extract SPICE Netlist from Standard Cell Layout
  A SPICE netlist of a given layout is required to simulate and verify the functionality of the designed standard cell layout. To summarise, "Simulation Program with Integrated Circuit Emphasis (SPICE)" is a design language for electronic circuitry that is widely used in the industry. The SPICE model closely resembles the actual circuit behaviour.
  Extraction of SPICE model for a given layout is done in two stages.
  1. Extract the circuit from the layout design.
  
    extract all
  
  2. Convert the extracted circuit to SPICE model.
    
    ext2spice cthresh 0 rthresh 0
    ext2spice
   
   ![image](https://user-images.githubusercontent.com/46182864/183926280-de2e79a6-7df9-4328-9021-d69a77e7588f.png)

  
 ## Transient Analysis using NGSPICE
  The NGSPICE tool is used to simulate the SPICE netlist generated in the previous step. NGSPICE is a free and open-source mixed-level/mixed-signal circuit simulator.
  The command used to invoke NGSPICE is shown below.
  
    ngspice sky130_inv.spice
    
  Following command is used to plot waveform in ngspice tool.
    
    ngspice 1 -> plot Y vs time A
    
![image](https://user-images.githubusercontent.com/46182864/183923259-2d3a11ab-f3a6-4cce-9b60-71eafb154cc9.png)
   
   Below figure shows the waveform of Inverter output vs input w.r.t. time. Many timing parameters like rise time delay, fall time delay, propagation delay are calculated using this waveform.
   
   ![image](https://user-images.githubusercontent.com/46182864/183923368-1df708b8-af2e-4ecc-86eb-a055354b3fbb.png)
   ![image](https://user-images.githubusercontent.com/46182864/183926028-c98da6b6-c3ea-4c49-85a5-7a3ed34fc215.png)

  
# Day 4 - Pre-layout timing analysis and importance of good clock tree
  To use a standard cell layout design in an OpenLANE RTL2GDS flow, it is converted to a standard cell LEF. LEF is an abbreviation for Library Exchange Format. After any addition or change to the design, the entire design must be analysed for any timing violations.
  
 ## Magic Layout to Standard Cell LEF
  We need some information about the layers in the designs before we can create the LEF file. This information is available in a 'tracks.info' file, as shown below. It specifies the 'offset' and 'pitch' of a track in a given layer in both the horizontal and vertical directions. The track information is presented in the format shown below.
  
    <layer-name> <X-or-Y> <track-offset> <track-pitch>
    
![image](https://user-images.githubusercontent.com/46182864/183921824-5e91c2ea-24f4-46b3-895d-9558ca214101.png)
  
  Some important factors must be considered when creating a standard cell LEF from an existing layout.
1. The cell's height must be sufficient so that the 'VPWR' and 'VGND' properly fall on the power distribution network.
2. The cell width should be an odd multiple of the minimum allowable grid size.
3. The cell's input and output are located at the intersection of the vertical and horizontal grid lines.

 ## Timing Analysis using OpenSTA
  The OpenSTA tool is used to perform the design's Static Timing Analysis (STA). The analysis can be carried out in a variety of ways.
- Within the OpenLANE flow: This is accomplished by invoking the 'openroad' command within the OpenLANE flow. OpenSTA is used in the openroad.
- Outside of the OpenLANE flow: This is accomplished by directly invoking OpenSTA from the command line. This necessitates additional configuration to specify the verilog file, constraints, clcok period, and other required parameters.
   
  OpenSTA is invoked using the below mentioned command.
  
    sta pre_sta.conf
  
  The above command gives an Timing Analysis Report which contains:
   1. Hold Time Slack
   2. Setup Time Slack
   3. Total Negative Slack (= 0.00, if no negative slack)
   4. Worst Negative Slack (= 0.00, if no negative slack)
  
  
  
  If the design causes any setup timing violations in the analysis, the following techniques can be used to eliminate or reduce them:
1. Extend the clock period (Not always possible as generally operating frequency is freezed in the specifications)
2. Buffer Scaling (Causes increase in design area)
3. Limiting an element's maximum fan-out
  
 ## Clock Tree Synthesis using TritonCTS
  Clock Tree Synthesis (CTS) is a process that ensures that the clock is evenly distributed to all sequential elements in a design. CTS's goal is to reduce clock latency and skew.
  
  
  The TritonCTS tool is used to generate clock trees in OpenLANE. CTS should always be performed after floorplanning and placement because CTS is performed on a 'placement.def' file created during the placement stage.
  
  The command used for running CTS in OpenLANE is given below.
  
    run_cts
    


# Day 5 - Final steps for RTL2GDS
 ## Generation of Power Distribution Network
   In a normal RTL to GDSII flow, the power distribution network is generated before the placement step, but in an OpenLANE flow, the PDN is generated after the Clock Tree Synthesis (CTS). This step creates all of the tracks and rails needed to route power to the entire chip.
The following command is used to create a power distribution network.
   
    gen_pdn
    
 ## Routing using TritonRoute
   TritonRoute, an open source router for modern industrial designs, is used by OpenLANE. The router is made up of several major components, such as pin access analysis, track assignment, initial detailed routing, search and repair, and a DRC engine.
The routing procedure is divided into two stages:
1. Global Routing - Interconnect routing guides are generated.
2. Detailed Routing - Tracks are generated in an interactive fashion.
TritonRoute 14 checks for DRC violations after routing.
   
   The following command is used for routing.
   
    run_routing
    
![image](https://user-images.githubusercontent.com/46182864/183929642-3a5df5b4-158c-4d84-ad83-d0e6f096ec34.png)

    
 ## SPEF File Generation
   The IEEE standard Standard Parasitic Exchange Format (SPEF) is used to represent parasitic data of wires in a chip in ASCII format. SPEF captures parasitic resistance and capacitance in non-ideal wires.
For the generation of SPEF files, OpenLANE includes a tool called SPEF EXTRACTOR. It's a Python-based parser that takes the 'LEF' and 'DEF' files as input and generates the SPEF file. The SPEC EXTRACTOR is invoked using the following command.
   
    cd <path-to-SPEF_EXTRACTOR-tool-directory>
    python3 main.py <path-to-LEF-file> <path-to-DEF-file-created-after-routing>
      
  
   
# References
  - RISC-V: https://riscv.org/
  - VLSI System Design: https://www.vlsisystemdesign.com/
  - OpenLANE: https://github.com/The-OpenROAD-Project/OpenLane

# Acknowledgement
  - [Kunal Ghosh](https://github.com/kunalg123), Co-founder, VSD Corp. Pvt. Ltd.
  - [Nickson Jose](https://github.com/nickson-jose)
  - [Shon Taware](https://github.com/ShonTaware/OpenSource_Physical_Design))
  - Akurathi Radhika
