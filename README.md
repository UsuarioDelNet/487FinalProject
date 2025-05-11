# CPE 487 Final Project: Frogger
![frogg.jpg](https://github.com/user-attachments/assets/0385a71c-9e93-42b2-8d4e-b9d27be4b676)
<p align="center">
  <img src="https://github.com/UsuarioDelNet/487FinalProject/blob/main/FinalProjectReferences/frogger_success.gif" alt="frogg_success">
</p>

## Table of Contents
- [Expected Behavior](#1)
- [Necessary Hardware](#2)
- [Project Setup Instructions](#3)
- [Modules and Ports](#4)
- [Modifications](#5)
- [Division of Labor](#6)


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

<h2 id="4">Modules and Ports</h2>

### clk_wiz_0.vhd: 
- This generates the clock signals for the system, but it was unmodified.
- The VGA_Top module takes the FPGA’s input clock and generates a 25 MHz clock for the VGA_Sync module.

### clk_wiz_0_clk_wiz.vhd:
- This is the supporting module for clk_wiz_0 that contains the actual clock generation logic, also unmodified.

### enemy_data.vhd: 
- A code from the Crossy Roads project, removed because it served no purpose and was not referenced anywhere.

### enemy_package.vhd: 
- Another code from the original Crossy Roads project.
- Rather than a module in the heiarchy, it is a package file used to define the coordinates, velocity, and position of the cars as well as certain cars speeding up more.
- However, this package is never referenced or declared in the project.

### frog.vhd:
The most utilized module in the project, it contained:
- Color logic. Frog.vdh processed all the entities and gave them each a color. It made the frog green, the cars red, the river blue, and the coins yellow.
- Frog logic. Frog.vdh both created our frog as well as controlled its movement and state of survival (alive or dead).
- Car drawing logic. Frog.vhd similarly created the cars, and it controlled their horizontal movements and gave them a collision hitbox.
- River drawing logic. Frog.vhd created the river and gave it a similar collision hitbox to the cars.
- Coin drawing logic. Frog.vhd created the coins and gave them a hitbox. 
- Collision detection (for car, river, coin). If the frog's hitbox collided with a car or the river, it would change its state to dead and disappear, ending the game. Similarly, the frog could collide with a coin and that would make the coin disappear.
- Score logic. If the frog collided with the coin and it disappeared, then the score counter would increase by 1. Then, frog.vhd sends the score to the VGA_Top module to export it to the board LEDs.

### frogger.xdc:
- This is where the pins for the buttons, vga, and anodes are declared.

### leddec.vhd:
- This is the module that controls the board anodes.
- It accepts the score from VGA_Top and outputs it to the board.
- It turns on anode 0 and turns off anodes 1-7.

### vga_sync.vhd:
- This is the standard vga_sync from the labs, made to set colors and video resolution.

### vga_top.vhd:
- This is the top level module. It acts as an intermediary for the score between frog.vhd and leddec.vhd.
- It generates the 25 MHz clock that we use from 50 MHz system clock.

<h2 id="5">Modifications</h2>

<h2 id="6">Division of Labor</h2>
