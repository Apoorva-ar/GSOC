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
The main idea behind scheduling is to ensure that high priority tasks get served before lower priority tasks. The input command is captured from software via AXI lite register. 
- Based on the priority of the service mentioned in the scheduler, the data latch FSM stores the command word in respective priority FIFO and the subsequent data is further stored in relevant FIFO based on the priority. 
- The master user FSM concurrently checks availability of data in command FIFOs based on priority (hence the command from higher priority is parsed first). After extracting and latching in the command, the burst information, read/write transaction information as well as data (from relevant FIFO) is passed on the packet layer to continue the transaction. </br>

`Scheduler_Master.vhd` : VHDL code for scheduling tasks (command and data words) and controlling internal FIFO banks as well as lower Master Packet layer to perform transactions based on service priority.
#### Packet Layer
This layer accepts commands from upper layers namely scheduling layer in order to control data flow of the physical layer.</br>
- The command contain virtual address, burst length and read/write information.
- The packet layer FSM ensures a ”command packet” is first transmitted over the LVDS serial link so that the 
data flow between master and slave devices is synchronised and controlled. </br>
- The master packet layer FSM decodes the command in order to generate/receive data packets (burst).
- The slave packet layer on the other hand reads command packet and then demands/produces data from/to the slave peripheral. </br>
- `Packet_Layer_Master.vhd` : VHDL code for generating/receiving packets as well as controlling read/write transactions as well as burst cycle of the lower PHY Master layer.
- `Packet_Layer_Slave.vhd` : VHDL code for generating/receving packets as well as controlling read/write transactions as well as burst cycle of the lower PHY Slave layer.

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



