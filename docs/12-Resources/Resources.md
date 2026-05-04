---
title: Resources
---

## Resources

### Overview

This page collects the supporting files for the Audio Subsystem in one place. The main item is a packaged copy of the final MPLAB X project, which contains all of the source code, MCC generated drivers, configuration bits, and project settings used to build and program the firmware on the PIC18F27Q43. Anyone who wants to rebuild the firmware, inspect the peripheral configuration, or use this subsystem as a starting point for a similar design can download the zip file below and open it directly in MPLAB X. Additional supporting documents from earlier phases of the project, such as the power budget and the API specification, are linked from their own pages on this site and are not duplicated here.

### Final Project Files

[Download the final MPLAB X project (AUDIOSUB.zip)](AUDIOSUB.zip)

The zip file contains the complete MPLAB X project for the Audio Subsystem, including the main.c source file, the MCC generated configuration for SPI1, DMA1, PWM1_16BIT, TMR2, and UART1, and the project settings needed to build with the XC8 compiler. To open it, unzip the folder somewhere on your computer, launch MPLAB X, and use File then Open Project to point at the unzipped folder.
