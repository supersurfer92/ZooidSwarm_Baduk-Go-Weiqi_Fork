# BadukBot: Swarming robots to play the game Go/Baduk

Based on Zooids: Building Blocks for Swarm User Interfaces

![Teaser](/Images/Teaser3.png)
Zooids, an open-source open-hardware platform for developing tabletop swarm interfaces, consist of a collection of custom-designed wheeled micro robots (each 2.6 cm in diameter), a radio base-station, a highspeed DLP structured light projector for optical tracking, and a software framework for application development and control.

Initial sketch of the redesigned Zooid, the BadukBot:
![Teaser](/Images/BadukBotSketch_0.0.1.JPG)

Revision 3 of the new pcb:
![Teaser](/Images/Rv3_proto.jpg)

Original board:

![Teaser](/Images/OG_boardTop.jpg)
![Teaser](/Images/OH_boardBottom.jpg)

BadukBot is based on Zooid software and hardware modified to play the game Go/Baduk/Weiqi. https://en.wikipedia.org/wiki/Go_(game)

Zooids in action: https://www.youtube.com/watch?time_continue=125&v=ZVdAfDMP3m0

[![IMAGE ALT TEXT](http://img.youtube.com/vi/ZVdAfDMP3m0/0.jpg)](http://www.youtube.com/watch?v=ZVdAfDMP3m0 "Zooids: Building Blocks for Swarm User Interfaces")

## Hardware
<p align="center">
<img src="Images/exploded.PNG" alt="exploded" width="400">
</p>

The original Zooids are small custom-made robots as shown above; their dimensions are 26 mm in diameter, 21 mm in height
and they weight about 12 g. Each robot is powered by a 100 mAh LiPo battery and uses motor driven wheels. They contain a flexible elctrode for capacitive touch sensing. It communicates with the main computer through a NRF24L01+ chip on a separate receiver. Currently the limit for bots per receiver is 10. More can be added at a cost to performance/communication speed.

BadukBots are 22.5 mm in diameter and ~50 mm in height. Each bot is powered by a 200 mAh lipo battery with motor driven wheels.

The original Zooid files have been converted from Altium schematics to KiCAD from scratch based on the original Zooid schematic pdf (original files break when converted.) The new PCBs and schematics are located in the BadukBot directory. The Proto...BOM consolidates the components for bots, Receivers, chargers, and tools required into one sheet. The new BOM is mostly the same with changes to include prices, links, and modifications to quantities to make 81 (for a 9x9 board) and 361 (for a 19x19 board) bots in the Master BOM spreadsheet.

The PCB is designed using KiCAD and FreeRouting. The 3D files are created in MODO. The BadukBot breaks the design into 3 single boards that encase the battery to both make it possible to reflow at home and reduce the foot print to 22.5 mm from the Zooid's 26 mm (go stones are 22.5 mm and allow for a full 19x19 board to fit in the projection area.) Dimensions for bots and the playspace are based on https://senseis.xmp.net/?EquipmentDimensions and will be scaled if needed accordingly.

We may try other DLP Lightcrafter EVMs to reduce cost ($140 vs $1400.)

### Tracking
The system uses a projector-based tracking system for robot position tracking as shown below. Using a high frame rate (3000Hz) projector (DLP LightCrafter Evaluation Module) from Texas Instruments Inc., a sequence of gray-coded patterns are projected onto a flat surface. Then, the photodiodes on the robot independently decodes into a location within the projected area. 
The instruction for setting up this projector-based tracking system is included in the repository. 
There are other DLPLCEVM projectors available at lower resolutions and refresh rates ranging from $140 to $1600.

The play area will be mapped to make a main play area, a go bowl area and a capture pieces area

![Teaser](/Images/playspaceMapping.JPG)

## Software
<p align="center">
<img src="Images/architecture.PNG" alt="architecture" width="700">
</p>

The communication structure consists of four main layers from highest to lowest level: Application, Simulation, Server, and Hardware. 

At the application level, the desired positions of the robots are computed. These desired positions are transmitted to
the simulation layer through a network socket. The application programmer can choose between two control strategies:
Proportional-Integral-Derivative (PID) position control or Hybrid Reciprocal Velocity Obstacles (HRVO) combined
with PID (these options are explained in the next paragraphs). Based on the chosen control strategy, the simulation layer
computes the goal positions of the robots, either final positions for PID or intermediate points for HRVO, and sends them to
the server. Finally, the server layer dispatches commands to the individual zooids, while at the same time monitoring their status and position.

Each robot independently controls its motion through a PID controller based on the state machine shown below. Given a final goal, the robot initially turns itself in the right direction and, once aligned, accelerates to its user-defined preferred speed. When it reaches the speed, it maintains it with a PID control on the orientation to ensure its direction towards the final goal. When a new incremental goal is given, it will still move at same speed but the PID control on orientation will direct the robot towards the new intermediate goal. When the robot arrives within 5 cm of the final goal, it slows down to its minimum velocity and once within 1 cm of the final goal, it stops and orients itself as commanded by the application programmer. To enable smooth transitions between the incremental goal positions, robots are given their next position at 60 Hz.

The modified software for playing Go using SwarmUI (GoSwarm) can be found at https://github.com/nheyn/GoSwarm and https://github.com/nheyn/react-swarm-ui and modified firmware will be maintained in this repo.

Goals include:

-defined areas for the bots including game play area on a grid

-automatically arranging bots to a Go bowl area

-automatically moving captured stones/bots to a defined captured bowl area

-queueing bots for the next turn

-game play rules like passing/resigning

-automatic scoring (and the ability to adjust captured areas)

-the ability to show which player is the winner (2 bots emerge from the winners go bowl, 1 from the loser after both sides pass or one resigns)

Resources and goals for features can be found at https://github.com/supersurfer92/SwarmUI-BadukBots/blob/master/Software/Go%20Features%2C%20Programming%20Gits%2C%20and%20theory.txt

GoSwarm has rewritten the ZooidManager program from C++ to Java and is currently developing swarming functions

[![IMAGE ALT TEXT](http://img.youtube.com/vi/S-AZOblyv-w/0.jpg)](https://youtu.be/S-AZOblyv-w "GoSwarm BadukBot playing behavior test")

<p align="center">
<img src="Images/local_control.PNG" alt="control" width="700">
</p>

## Acknowledgments 

BadukBot and GoSwarm/react-SwarmUI is a hobby project created by Caleb Cover and Nick Heyn based on SwarmUI.

The original SwarmUI project (https://github.com/ShapeLab/SwarmUI) is a joint work between the Shape Lab at Stanford University (USA) and the Aviz team at Inria (France). It was partially funded by the Région Ile de France, DIM ISC-PIF. We would also like to thank Alexa Siu, Shenli Yuan, Ernesto Ramirez and Pham Minh Hieu for investing so much time and efforts in making this work possible. 

The original licenses are retained where applicable and the licenses used for new hardware developed will use a license that follows the Open Source Hardware Association definition of Open Source Hardware (https://www.oshwa.org/definition/.)

<p align="center">
<img src="Images/logos.png" alt="logos" width="700">
</p>

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
