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
### xschem
### netgen

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

# Day 2 - Introduction to DRC and LVS

## Basic of Magic

### GDS format
Tools for Manipilating GDS
### Streaming Format
### Hierarchy
### SPICE extraction and formats
LVS extraction
Parasitic extraction
Full R-C extraction
### Manipulating GDS in Magic
Readonly
Flatten
Hierarchy and Magic's Database
Polygon Subcells
Library
Addendum
Nuduplicates
GDS styles
### DRC Rules in Magic
Interactive DRC
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
