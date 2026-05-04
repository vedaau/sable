---
title: Welcome
tags:
- tag1
- tag2
---
<center>
<font size="6" face="Georgia">Vedaa Ubale's Datasheet</font><br>
as part of<br>
<font size="8" face="Impact">Sable</font><br>
for<br>
<font size="5" face="Courier New">Team 303</font><br>
**Submission: May 4, 2026**
</center>

## Introduction

This datasheet documents my individual contribution to the Sable project, which is a search and rescue robot designed by Team 303 for EGR 314. The site brings together every step of the design process for my subsystem, from the early block diagram through the final firmware. Each page captures the decisions I made, the parts I selected, and the reasoning behind them, so that anyone reviewing the project can follow the path from initial requirements to a working board. This datasheet is meant to be read alongside the team report, which describes how all six subsystems fit together into the complete robot.

### Project Summary

The Sable project is a small mobile robot built to help locate survivors in disaster scenarios. It moves through tight spaces, captures audio and video from the environment, and reports what it finds back to a human operator over a wireless link. The robot is split into six subsystems, each owned by one team member, that talk to one another over a shared UART daisy chain. My role on the team is to build the Audio Subsystem, which is responsible for capturing clear audio from the environment so that the operator can hear sounds from a survivor or from the area around the robot. The audio subsystem also passes messages between other subsystems on the network, so it has to be reliable and fast enough not to slow down communication for the rest of the team. More information about how all the subsystems work together is available in the [team report](https://egr314-s-2026-303.github.io/).

### My Contribution

My part in the Sable project was to design the Audio Subsystem. To do this, I selected a digital MEMS microphone, chose a microcontroller that could handle the audio data and communicate with the rest of the team, designed the supporting power and communication circuitry, and wrote firmware to capture, process, and forward audio related information. The following are the major steps I took in designing my subsystem.

- To view my [Block Diagram](https://vedaau.github.io/sable/02-Block-Diagram/Block-Diagram/), click here. This page shows the general idea of how my PCB will function and how it captures audio with the ICS-43434 microphone, processes it on the PIC18F27Q43 microcontroller, and shares status information with the rest of the robot over UART.
- To view my [Component Selection](https://vedaau.github.io/sable/03-Component-Selection/Component-Selection/), click here. This page displays the major components needed to build my subsystem, including the microphone and the voltage regulator, along with the parts I reviewed to choose the best fit for my design.
- To view my [Microcontroller Selection](https://vedaau.github.io/sable/04-Microcontroller-Selection/Microcontroller-Selection/), click here. This page describes the PIC18F27Q43 microcontroller, why I picked it for the audio subsystem, and which peripherals I planned to use.
- To view my [Power Budget](https://vedaau.github.io/sable/05-Power-Budget/Power-Budget/), click here. This page shows the expected power consumption of all components in my subsystem and confirms that the LM2575 regulator and the 12V wall supply have enough headroom to run everything safely.
- To view my [Bill of Materials](https://vedaau.github.io/sable/06-BOM/BOM/), click here. This page lists every component used in the audio subsystem along with quantities and sources.
- To view my [Schematic](https://vedaau.github.io/sable/07-Schematic/schematic/), click here. This page shows the schematic for my subsystem, created in KiCad 9.0.
- To view my [API](https://vedaau.github.io/sable/08-API/API/), click here. This page lists every message my subsystem sends, receives, and forwards on the team's UART network, along with the byte by byte format of each message.
- To view my [Reflection](https://vedaau.github.io/sable/09-Reflection/Reflection/), click here. This page reviews which requirements were met, what I learned from the project, and what I would recommend to future students.
- To view my [Hardware V2.0](https://vedaau.github.io/sable/10-Hardware-V2/Hardware-V2/) ideas, click here. This page describes the changes I would make if I were to design a second version of the audio subsystem hardware.
- To download the final source code and supporting files, visit the [Resources](https://vedaau.github.io/sable/11-Resources/Resources/) page.
