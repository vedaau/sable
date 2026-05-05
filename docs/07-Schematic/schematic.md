---
title: Schematic
---

## Schematic

### Overview

This page shows the final schematic for the Sable Audio Subsystem, drawn in KiCad 9.0. The schematic is split into six labeled sections to make it easier to follow. The Power Supply section takes a 12V input from a barrel jack and uses an LM2575 switching regulator to produce the 3.3V rail that powers the rest of the board, with a 2A fuse and a Schottky diode for protection. The Micro Controller section shows the PIC18F27Q43 with its decoupling capacitors, reset pull up resistor, and the extra pin header used during bring up. The Microphone and Op Amp section shows the ICS-43434 digital MEMS microphone connected to the SCK, WS, and SD pins of the PIC, with its select pin tied to ground so it speaks on the left channel of the I2S frame. The Subsystem Connections section shows the two 2x4 IDC connectors used to daisy chain UART communication to the rest of the robot. The Debug LED section is a simple status LED with a current limiting resistor that the firmware uses to show when the microphone is picking up sound. The ICSP Header section provides a clean programming and debug interface for the MPLAB Snap so the chip can be flashed and inspected without disturbing the rest of the board.

### Schematic Diagram

![Sable Audio Subsystem Schematic](schematicaudio.png)

---

**Downloads:** [KiCad project (.zip)](audiosubsystem.zip) | [PDF](audiosubsystem.pdf)
