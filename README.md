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
- [Summary and Conclusion](#6)


<h2 id="1">Expected Behavior</h2>
The program aims to mimic the behavior of a typical game of Frogger, with some additional modifications. 

- The frog starts at the bottom center of the screen.
- The frog can move in four directions and must get to the other end of the screen to win, avoiding obstacles along the way.
- The cars are moving obstacles that are in fixed vertical positions on the screen and move horizontally at a set speed that is slightly faster than the frog.
- The river is a static obstacle with a bridge on one side that the frog must cross.
- The frog “dies” if it collides with a car or the river and the game must be reset.
- There are three coins that the frog can choose to collect along the way to the finish.
- Collecting coins increases the score, which is displayed in binary on the board.

<p align="center">
  <img src="https://github.com/UsuarioDelNet/487FinalProject/blob/main/FinalProjectReferences/frogger_fail_car.gif" alt="frogg_fail">
</p>

<p align="center">
  <img src="https://github.com/UsuarioDelNet/487FinalProject/blob/main/FinalProjectReferences/frogger_coin.gif" alt="frogg_coin">
</p>

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

### enemy_package.vhd: 
- Another code from the original Crossy Roads project.
- Rather than a module in the heiarchy, it is a package file used to define the coordinates, velocity, and position of the cars as well as certain cars speeding up more.
- However, this package is never referenced or declared in the project.

### frog.vhd:
The most utilized module in the project, it contained:
- **Color logic:** processed all the entities and gave them each a color. It made the frog green, the cars red, the river blue, and the coins yellow.
- **Frog logic:** created the frog as well as controlled its movement and state of survival (alive or dead).
- **Car drawing logic:** created the cars, controlled their horizontal movements, and gave them a collision hitbox.
- **River drawing logic:** created the river and gave it a similar collision hitbox to the cars.
- **Coin drawing logic:** created the coins and gave them a hitbox. 
- **Collision detection:** If the frog's hitbox collided with a car or the river, it would change its state to dead and disappear, ending the game. Similarly, the frog could collide with a coin and that would make the coin disappear.
- **Score logic:** If the frog collided with the coin and it disappeared, then the score counter would increase by 1. Then, frog.vhd sends the score to the VGA_Top module to export it to the board LEDs.

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

To build this project, we used Labs 3 and 6 as well as two previous projects—[Crossy Road](https://github.com/alex-waldron/CPE487/tree/main/FinalProject) and [Subway Surfers](https://github.com/ryanvconnolly/CPE487finalproject/blob/main/README.md)—as a base for our code. We made the following modifications:

### frog.vhd:
#### Score
- To change the score from a timer to a coin counter, the score vector was reduced from a 16-bit into a 2-bit vector that only counts up to 3.
- The `s_score` and `score_incr` signals were used to increment the timer were removed, as well as the points in which they were used,
- The signals `s_score1`, `s_score2`, and `s_score3` were added that each begin at `00`, and change to `01` when a collision between their respective coin and the frog is registered.
- The 3 new `s_score` signals were summed and score was set to their value, and sent to the VGA_Top file.

#### Colors
- The `red`, `blue`, and `green` signals were changed from 3-bit and 2-bit vectors to 4-bit vectors in line with the color signals in the pong lab.
- The structure of the color declarations were changed; in the prior code both OR and NOT OR declarations were used, whereas now only OR declarations are used.
- Frog color was changed from black to green.
- Background color was changed from green to black.
- Coin color was set to yellow (red and green).
- River color was set to blue.

#### Frog
- To make the game more difficult, the frog's movement speed was changed from 4 pixels per frame (`00000000100`) to 2 pixels per frame (`00000000010`).

#### Car
- The vertical positions of the cars were slightly moved to make space for the river.

#### River
- The river was added as an entity with similar x and y signals to the cars as another obstacle for the frog.
- The signals `river_w` and `river_h` were added as constants to act as the width and height for the river, and to act as the space where the river hitbox registers.
- The river was given the same hitbox as the cars.
- The code to implement it as a rectangle was inspired by the bat from the pong lab.

#### Coin
- The coins were added as a beneficial entity using similar x and y signals to the cars, as well as similar hitbox rendering. 
- The signals `coin_off` and `coin_on` were added, with one for each coin. Within their individual processes, the `coin_on` is used to draw the coin, and `coin_off` is used as a check in order to prevent the coins from respawning before the reset button is hit.
- If the coin hitbox registers a collision the `coin_off` changes from `1` to `0` to indicate the coin turns off, and the respective `s_score` changes to `01`.
- When `direction = 8`, the respective `coin_off` returns to `1` and `s_score` changes to `00`.
- The code to implement them as circles was inspired by lab 3.

### frogger.xdc:
- Because the 3 `color` signals went from 3-bit to 4-bit, the appropriate VGA pins had to be registered.
- Because of issues with the board LEDs, all the LED pins had to be registered in order for the leddec module to turn them off.

### leddec.vhd:
- The vector `f_data` that reads the score was changed from 16-bit to 2-bit.
- The `seg` segments that could activate were only set from 1 to 3 due to no numbers of 3-bit or above possible for the code.
- `anode` was changed to only accept one string -- `1111110`, in order to turn off all the LEDs on the board save for the rightmost LED, anode 0.

### vga_sync.vhd:
- The `red`, `blue`, and `green` signals were changed from 3-bit and 2-bit vectors to 4-bit vectors to be in line with the color signals in the pong lab.
- The code was changed from the version used in the Crossy Road code to the version used by the pong lab, as the prior code didn't support 4-bit color vectors. 

### vga_top.vhd:
- The `red`, `blue`, and `green` signals were changed from 3-bit and 2-bit vectors to 4-bit vectors to be in line with the color signals in the pong lab.
- The `score` vector was added to the `frog` entity in order to send it to the leddec file properly.
- Due to the code using a custom 25 MHz clock, the original code that used the automatically generated clk_wiz and clk_wiz_0 was commented out.

### enemy_data.vhd: 
- A file from the Crossy Roads project, removed from the repository because it served no purpose and was not referenced anywhere.

<h2 id="6">Summary and Conclusion</h2>

**Daniel**

- Frog drawing, positioning, and logic
- Color logic
- Clock and collision logic debugging
- Car modifications

**Rae**

- Coin drawing, positioning, and logic
- Score logic
- River positioning and collision logic
- Debugging

**Issues and Fixes**

1. Coins respawning
    - Coins would unintentionally respawn when the player moved up and to the right simultaneously.
    - This error was caused by the direction logic within the code: each direction was assigned a number from 1 to 4, and the "reset" state was assigned a direction of 5. When the player would move up and to the right, the directions 1 and 4 were triggered simultaneously, making 5 and forcing the game into a state where it thought the board reset.
    - This was fixed by changing the "reset" state direction number to 8, though any higher number would have also worked.
2. Errors in bitstream generation
   - When compiling the project through Vivado, the code would make it through synthesis and implementation, but failed to write to the bitstream.
   - This failure was caused by errors with the color and the clock.
   - This was fixed by first editing the frogger.xdc file to make sure all the colors were properly declared, and deleting the initial clock function in order to use the custom 25 MHz clock to bypass all the persistent clock issues.
3. River collision detection
   - The frog would only "die" when hitting specific parts of the river instead of any part of it.
   - This was fixed by properly rendering the river's hitbox, as it had overflowed due to a negative position.
