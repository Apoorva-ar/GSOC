# FPGA based Bidirectional Packet protocol design (GSOC-2020)

This protocol is designed for extending IO services of main board (XILINX-ZYNQ) to external routing fabric (Lattice-MACHXO2) over single high speed LVDS link.

## Project
Aim of the project is to design a packet based bidirectional protocol over single LVDS link that can fully utilize the available band width based on priority based task scheduling.</br >

### System Architecture
- The Scheduler accepts user commands including address as well as bursts size. Based on the priority of the service, a specific FIFO is used to store the data of corresponding service (address). 
- The scheduler then redirects specific command as well as FIFO link to the packet layer which parses the command to generate the required packets.
  - First packet transferred over the LVDS link is command packet which specifies IO address as well as burst size.
  - Then based on the command either data is written to the LVDS link or is read from it. The schedulers on both the master as well as slave sides ensures that there is no bus contention.
- The physical layer PHY acts as bidirectional SERDES. The master PHY produces both clock and data while slave PHY produces/receives data on the clock provided by master.

#### Scheduling Layer
#### Packet Layer
#### PHY Layer
This layer acts as bidirectional SERDES for the user. There are two major modules involved mainly master and slave. </br>
- Master produces clock while slave transmits and receives data on that clock.
Both master as well as slave FSMs are initiated on write/read transaction enable signals from upper layers (in this case the packet layer). Hence, the upper layer has full control over the SERDES in terms of avoiding any possible cross-talks.</br>
- `PHY_Master_controller.vhd` : VHDL Controller for controlling PHY Master </br>
  - `PHY_Master.vhd: VHDL` code for Master PHY layer -> SERDES that controls clock as well as data. </br>
    - `PHY_Data_Path.vhd` : VHDL code for controlling SERDES data path.</br>
    - `PHY_sclk_gen.vhd`  : VHDL code for controlling LVDS clock.</br>
- `PHY_Slave_Controller.vhd` : VHDL Controller for controlling PHY Slave  </br>

## Applications

## Structure

### Modules invovled

## Testing



