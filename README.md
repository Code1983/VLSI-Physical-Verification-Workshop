# Physical Verification using SKY130


# Workshop Structure

### Day 1 - Introduction to Skywater SKY130

### Day 2 - Introduction to DRC and LVS

### Day 3 - Front-end and back-end DRC

### Day 4 - PNR and physical verification

### Day 5 - LVS


# Day 1 - Introduction to Skywater SKY130

## Open Source packages

### Open PDKs
### Skywater PDK
Skywater process is described as a standard 130nm/180 nm process.FET transistor 150 nm. specifically designed memory layout use 130nm devices.
### Magic
Some Magic commands
* magic -noconsole - This would bring the tcl console on command line
* magic -dnull -noconsole - This would bring up Magic without GUI. This is the way Magic should be run from script.
 * magic -dnull -noconsole test.tcl   (test.tcl has only one line with text "quit")
* magic -d XR (invokes cairo graphics package)
* magic -d OGL (uses openGL graphics package)
* Typing semicolon on GUI will automatically redirect the commands to tcl command window.
* import spice - File menu -> import spice
### xschem
* xschem --tcl test.tcl -q (This will throw an error if previous file is used since "quit" is not a tcl command)
### netgen
* netgen -noconsole - This has same behaviour as for for magic i.e console on commandline
* netgen -batch source test.tcl (test.tcl has only one line with text "quit")
### ngspice
* ngspice -b (to run ngspice in batch mode)

## Building Blocks

### Layers
* 5 layers of Aluminium metal plus a layer of Tinanium Nitride (Local interconnect)
* Local interconnect (LI) is used to connect neighbouring devices. Skywater standard cell uses it for power and ground rails.
* Contact to polysilicon requiring a Nitride a polycut layer.
* Metal layers are in progressive thickness bottom up.
* For Digital flows, tools reserve Metal5 for routing.
* Metal layers ard vias are called back-end layers. Everything below is called front-end layers. They are different as different precision required for masks and different fabrication process. They share the steps of oxide(insulation layer) growth but front end is mainly diffusion and ion implantation while back-end is metal deposition by sputtering.
* Some unique thinhs about this process
  * separate tap and diffusion layers 
  * deep nwell layer. This is accompanied with circling that area with nwell for isolation. We can diffusse a pwell above this nwell and create p-type devices such as FET, bipolars and diodes.
  * HVI (high voltage implant) layer has more than one use - nwell implanted with HVI is tolerant to higher voltage; grow additional insulation under the gates of mosfet forming a thick oxide gate transistor. Thick oxide devices are tolerant to 5 v. Thin oxide are tolerant upto 2v. This can help dual voltage domain design.
  * MiM (metal insulator metal) layers are part of backend process although they form devices that are extracted and simulation. This is created by creating metal plates between two metal routing layers to create capacitors.skyawater has dual MiM layers.
  * Reistribution layer (RDL). Fabricated by 3rd party manufacturer. used to form bump bonds which is part of WLCSP (wafer level chip scale packaging). Solder bumps are added directly on the RDL. Die itself becomes it's own packaging. No wire bonding is needed. RDL is not a designlayer. It is part of packaging. Howerver copper layers are thick and can be useful for forming inductors.
 
### Rules
### Devices
* Thin oxide
* Thick oxide
* Mim Caps
* Resistors
* Bipolar devices - vertical bipolar making use of diffussion regions and wells has high enough beta. It can have proper NPN transistor.
* Resistors
 * Polysilion - P+ and p- dope
 * diffussion resisitor
 * pwell is highest resistance resistor
 * high precision resistors are discrete sizes
* devices with a restricted number of approved layout. GDS is provided for these throughly tested devices.
### Libraries
### Digital Standard Cells
### I/O Cells

## Project set-up best practices
### Folder structure
Project_root  
|--xschem  
|--mag  
|--netgen  
### Xschem
* It is preferebale to keep the circuit part that will be turned into layout self-contained. i.e no components that would not be part of layout like voltage or current sources. Ideally, schematic to be laid out is to appear as a symbol to test bench.
* Not to use any specific power supply pins.
# Day 2 - Introduction to DRC and LVS

## Fundamentals of Physical Verification
### Design Rule Checking (DRC)
Ensures design meets silicon foundry rules for masks
### Layout vs Schematic(LVS)
Ensures design layout matches schematic or simulatable netlist by electrical connectivity and devices.  
This is kind of tools checking tools. If something is flagged by LVS, it is more often the set-ups provided by the tool.  

Some schematic capture tools  
* xschem
* xcircuit
* eeschema
* esim

some layout tools
* magic
* klayout
* electric

## Basic of Magic

### GDS format
Earlier format used by Magic is Caltech Intermediate Format (CIF). It had two nice properties
* human readable ascii format
* naturally extensible
some questionable features of CIF
* stem from expectation that chip data would be curvy [1]
* introduce new format type extensibility and broke the spec.

Calma Inc came us with file format called GDS stream format which has kind of morphed into GDS II.  
In Magic, we can use `cif see <layer>` to overlay the GDS layout.  

In GDS every layer is denoted by two numbers (layer-purpose pair) 
layer - diffussion, poly, metal1  
Purpose - drawing, label, pin, blockage, net  
Examples  
DIFF    65:20
NSDM    93:44

These number are inconsistent within differnet processes and foundries.   

GDS format summary
Data in Rectangles, Polygons, Subcells and have layer associated with them  
Collect a bunch of geometry together and call it a cell. We can make instances of cell and put them in another cell.  
Metadata is not part of physical Mask like label, cell definition, cell boundries  
GDS does not include Device types, current sinks, power supplies etc. This lead to invention of new formats like lef and def.
Detailed GDS specification in [2]  

There is a new file format called OASIS. This creates more smaller file at the expense of readability.

### SPICE extraction and formats
GDS does not store the netlist information. Tools can independently generate a netlist by looking at nothing but the mask. This process is called extraction.  
Magic has a two step process for extraction.  
Layout -> Intermediate Form (.ext file) -> netlist

Extraction commands from Magic
* extract do local <- optional. ensures that files are created locally.
* extract all <- creates .ext file
* extresist tolerance 10
* extresist all
* ext2spice lvs <- option set-up for running LVS.
* ext2spice cthresh 0
* ext2spice extresist on
* ext2spice <- creates spice file

Extraction styles available in Magic
* ngspice() ensures ngspice compatible but takes parameters from ext2spice
* ngspice(orig) deals with some older files
* ngspice(si) converts all units to SI units

Ext2sice options
* ext2spice lvs - sets number of same options
* ext2spice cthres value - sets threshold of capacitances for extarction. Parasitic resistances are done ina completly different way.
* ext2spice scale on|off - off is preferred for skywater process
* ext2spice hierarchy on|off - one big netlist or hierarchy.
* ext2spice subcircuit top on|off - needs ports to be defined.
* ext2spice global on|off - sets the behaviours of declaring global nets.
* ext2spice merge on|off - device with the same size are merged together.

LVS extraction
Parasitic extraction
Full R-C extraction
### Manipulating GDS in Magic
This is done by gsd command.
`gds read <file>`  

Important options
* gds readonly true|false 
  * they are pointing toa source file that cannot be moved
  * view in magic may not have anything to do with source data. We can manipulate it in magic but it does not get saved.
* gds flatglob - helps read vendor file were parts of a device are stored in hierarcy.
* gds flatten - 
* gds noduplicates true - can help preload an abstract view of some cells and then read in rest from GDS.
* gds library true - has bunch of cell but does not have a top level like a standard cell library.
* gds addendum true - ignores any readonly options. need to read the read-olny files again while reopening with file.
* gds merge true|false - merges all polygon
GDS input styles
* `style sky130 variants (), (vendor)` is equivalent to
 * `cif istyle sky130()`
 * `cif istyle sky130(vendor)`
* style rdlimport
GDS output styles
* style gdsii
* stye drc - for drc only
* style density - used by scripts for fill density
* style wafflefill - used by scripts
### DRC Rules in Magic
* Interactive DRC
* DRC styles
 * fast - slowest
 * full
 * routing - fast basic metal layer rules - fatest
* DRC rule checking methods in magic
 * edge-based- spacing, width, surround, extend 
 * boolean geometry rules - and, or
 * other operator - grow, shrink, squares

DRC vs correct-by-design
DRC rules in hierarchy
DRC in tech file
DRC verification layout
Extraction rules
Extraction styles
Device types
Hierrarchical extraction
DRC errors uncovered by extraction
## Netgen
LVS setup
Device description
## Black boxing
## Verification by XOR
## ECO


# Day 3 - Front-end and back-end DRC
## Rules
Back-end (metal layer) rules
Width
Spacing Notch
Wide-Spacing
Minimum Area
Via overlaps
Minimum hole
Slot rules
Via generation rules
Local interconnect
Front-end rules
Transistor rules
Implants, ID layers, Boundary layers
Wells, same-net rules
Deep N-well
High Voltage rules
Resistors
Capacitors
Diodes
Fixed-layout devices
Miscellaneous rules
Off-grid errors
Restricted angles
Latchup rules
Antenna rules
Stress rules
Density rules
Double vias
## Fill generation algorithm
Fill blocking
## DRC in Magic
DRC styles
Dumping and viewing DRC results
Fixing DRC errors

# Day 4 - PNR and physical verification
## OpenLANE/OpenRoad automation
Non-interactive mode
Interactive mode
Common DRC errors
Minually fixing violation

# Day 5 - LVS
## Netlists
netlits from schematic
metlist from layout

## Netgen
Pre-match analysis
Hierarchical checking and flattening
Property checking
Pin checking
Series/Parallel combing
Symmetry breaking
Interpreting results
Fixing errors

# References
[1] Telle Whitney, caltech, 1985, https://thesis.library.caltech.edu/1101/1/Whitney_te_1985.pdf  
[2] https://boolean.klassholwerda.nl/interface/bnf/gdsformat.html
