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
### Magic
### xschem
### netgen

## Building Blocks

### Layers
### Rules
### Devices
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
