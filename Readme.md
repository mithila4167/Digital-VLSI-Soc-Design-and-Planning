# PicoRV32a ASIC Flow (RTL → GDSII) Using SkyWater SKY130
# Commands to launch OPENLANE
```bash
cd Desktop/work/tools/openlane_working_dir/openlane
unalias docker
docker run -it -v "$(pwd):/openLANE_flow" -v "$PDK_ROOT:$PDK_ROOT" -e PDK_ROOT="$PDK_ROOT" -u "$(id -u):$(id -g)" efabless/openlane:v0.21
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
```
# Spice Simulations

```bash
# Clone custom inverter standard cell design from github repository
cd Desktop/work/tools/openlane_working_dir/openlane
git clone https://github.com/nickson-jose/vsdstdcelldesign
cd vsdstdcelldesign
cp /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .
magic -T sky130A.tech sky130_inv.mag &
# Spice extraction of inverter in magic.
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
# Commands for ngspice simulation
ngspice sky130_inv.spice
plot y vs time a
```
<img width="957" height="1017" alt="inv_layout" src="https://github.com/user-attachments/assets/dfca5184-cc3e-49d8-b162-93053ff0cbe4" />
<img width="957" height="1017" alt="inv_simulation" src="https://github.com/user-attachments/assets/e2a3b4c8-0596-4958-827b-ca74ca300820" />
<img width="957" height="1017" alt="inverter_spice" src="https://github.com/user-attachments/assets/8f712e2e-fc1d-46bd-883a-e5312109a4cd" />

# Synthesis

```bash
run_synthesis
```

Converts RTL (Verilog) into a gate-level netlist using technology-mapped standard cells.

# Floorplanning

```bash
init_floorplan
place_io
tap_decap_or
gen_pdn
```
Performs the initial physical design setup by generating the floorplan, placing IO pins, inserting tap/decap cells, and constructing the power-delivery network.

<img width="1919" height="992" alt="Floorplan" src="https://github.com/user-attachments/assets/b648691a-a9dd-415c-87d7-25eab1c7cb2f" />
<img width="1920" height="1020" alt="Floorplan1" src="https://github.com/user-attachments/assets/82d578fe-47ae-4f40-b70a-781912b52091" />

# Placement

```bash
run_placement
```

Places all standard cells in legal positions while optimizing wirelength and timing.

<img width="1919" height="1079" alt="inv_placement" src="https://github.com/user-attachments/assets/f967fd2d-c731-45ad-a0a5-f6da47500b6b" />
<img width="1919" height="1079" alt="inv_placement1" src="https://github.com/user-attachments/assets/c67c96db-06a1-46b6-8c27-f05f2eb02d35" />
<img width="1919" height="954" alt="Placement" src="https://github.com/user-attachments/assets/202b5ebd-913c-4805-b6de-0d9fb6e38ecd" />
<img width="1920" height="1020" alt="placement4" src="https://github.com/user-attachments/assets/a4c7ea18-7d17-4f2b-b4e7-64b802d49f05" />

## Clock-Tree Synthesis (CTS)

```bash
run_cts
```

Builds the clock tree by inserting clock buffers/inverters and balancing skew.

<img width="1919" height="931" alt="cts" src="https://github.com/user-attachments/assets/37d8ed3b-b31f-4ab2-952d-4dc7dfb35ffb" />

## Timing Optimization (Resizer)

```bash
run_resizer_timing
```

Automatically fixes timing violations (setup/hold) using physical optimization:
- cell upsizing / downsizing  
- buffer insertion  
- pin swapping  
- net rebuffering  


# Routing

```bash
run_routing
```

Creates actual metal interconnects (global + detailed routing) to connect all cells and macros.

<img width="1858" height="941" alt="Routing1" src="https://github.com/user-attachments/assets/0e15ded0-fa23-4a8d-99a8-271ecae7335a" />
<img width="1919" height="958" alt="Routing2" src="https://github.com/user-attachments/assets/c910b34e-3988-4fae-8a04-6a6d198d9e7d" />
<img width="1919" height="921" alt="Routing3" src="https://github.com/user-attachments/assets/6a583e76-3c2b-40b9-aed8-3e43b4e146ef" />
<img width="1919" height="1025" alt="routing_4" src="https://github.com/user-attachments/assets/69f962d0-f955-4ddd-b743-31c31e3b1d2c" />

# GDS

```bash
run_klayout
```
Opens the generated GDS layout in the KLayout viewer for inspection and sign-off checks.

<img width="709" height="679" alt="gds1" src="https://github.com/user-attachments/assets/6f3bf0dc-792e-470d-acd9-6bea1656d727" />


