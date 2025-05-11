# CPE 487 Final Project: Frogger
![frogg.jpg](https://github.com/user-attachments/assets/0385a71c-9e93-42b2-8d4e-b9d27be4b676)
## Table of Contents
- [Expected Behavior](#1)
- [Necessary Hardware](#2)
- [Project Setup Instructions](#3)
- [Modifications](#4)

<h2 id="1">Expected Behavior</h2>
The program aims to mimic the behavior of a typical game of Frogger, with some additional modifications. 
- The frog starts at the bottom center of the screen.
- The frog can move in four directions and must get to the other end of the screen to win, avoiding obstacles along the way.
- The cars are moving obstacles that are in fixed vertical positions on the screen and move horizontally at a set speed that is slightly faster than the frog.
- The river is a static obstacle with a bridge on one side that the frog must cross.
- The frog “dies” if it collides with a car or the river and the game must be reset.
- There are three coins that the frog can choose to collect along the way to the finish.
- Collecting coins increases the score, which is displayed in binary on the board.

<h2 id="2">Necessary Hardware</h2>
- Nexys A7-100T FPGA Board
- Computer with a USB port and Vivado installed.
- Micro-USB to USB Cable
- VGA Cable
- Monitor with VGA port or VGA adapter

<h2 id="3">Project Setup Instructions</h2>
1. Connect the Nexys A7-100T FPGA board to the computer with Vivado installed via the Micro-USB to USB cable, using an adapter if necessary.
2. Connect the monitor to the board using the VGA cable, using adapters if necessary.
3. Ensure that the board is switched on.
4. Download the files on this GitHub repository.
5. Open Vivado and create a new RTL project. On the "Add Sources" page, add all of the downloaded .vhd files. On the "Add Constraints" page, and add all of the downloaded .xdc files. On the "Default Part" page, select "Nexys A7-100T" under “Boards”.
6. Once the project has been created, run synthesis, implementation, and generate the bitstream.
7. Open the Hardware Manager and program the board. The game should now appear on the monitor.

<h2 id="4">Modifications</h2>

