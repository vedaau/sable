---
title: PCB
---

## PCB

### Overview

This page documents the final printed circuit board for the Sable Audio Subsystem. The board was designed in KiCad 9.0 from the schematic shown on the Schematic page, then exported as Gerber files and sent out for fabrication. The layout places the noisy switching regulator components on one side of the board and keeps the microphone and microcontroller on the quieter side, with a single point ground tie under the microcontroller to reduce supply ripple at the audio inputs. Two 2x4 IDC connectors on the edge of the board carry power and the daisy chained UART signals to the neighboring subsystems on the robot. After fabrication the board was hand soldered, tested for shorts on the 3.3V rail, programmed through the ICSP header, and verified to capture audio from the microphone and forward UART traffic correctly. The downloads at the bottom of the page include the full KiCad project, the Gerber files used for fabrication, and front and back images of the board.

### ECAD Render (Front and Back)

**Front view (ECAD render)**

![PCB front view from KiCad](pcb_front_render.PNG)

**Back view (ECAD render)**

![PCB back view from KiCad](pcb_back_render.PNG)

### Raw PCB (Before Population)

This is the bare board as it arrived from the fabricator, before any components were soldered down.

![Raw PCB front, before population](pcb_raw_front.jpg)

![Raw PCB back, before population](pcb_raw_back.jpg)

### Finalized PCB (After Soldering and Testing)

This is the populated and tested board after every component was placed, soldered, and verified.

![Finalized PCB front, after soldering](pcb_final_front.jpg)

![Finalized PCB back, after soldering](pcb_final_back.jpg)

---

**Downloads:** [KiCad ECAD project (.zip)](audiosubsystem_kicad.zip) | [Gerber files (.zip)](audiosubsystem_gerbers.zip)
