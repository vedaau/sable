---
title: Hardware V2.0
---

## Hardware V2.0

If a second revision of the Audio Subsystem hardware were to be designed, several improvements could be made based on lessons learned during firmware bring-up, debugging, and integration with the rest of the robot. The current Version 1.0 board is functional and meets the core requirements, but the experience of working with it revealed a number of areas where small changes to the schematic and PCB would significantly improve reliability, ease of debugging, and audio quality. The following sections describe each proposed change and the rationale behind it, with reference to the existing schematic where applicable.

### Switch to a Microcontroller with a Native I²S Peripheral

The single biggest hardware improvement for Version 2.0 would be replacing the PIC18F27Q43 with a microcontroller that has a dedicated I²S peripheral, such as a PIC32MZ, SAMD21, ESP32-S3, or RP2040. The current design uses the PIC18's SPI module to receive serial audio data and a separate PWM output to generate the word-select (WS) signal, with DMA streaming bytes from the SPI receive buffer into RAM. While this approach functions correctly, it consumed a disproportionate amount of project time because the SPI byte boundaries are not phase-locked to the WS edges, which forces software-based frame alignment and complicates the buffer-parsing logic. A native I²S peripheral generates BCLK and WS in hardware with the correct timing relationship, exposes a properly framed receive register, and integrates cleanly with DMA without the alignment workaround. This change alone would simplify the firmware substantially, free up a PWM module and a timer for other purposes, and reduce the risk of subtle audio glitches caused by mis-aligned frames.

### Replace the LM2575 Switching Regulator with a Quieter Alternative

The schematic's power supply section uses an LM2575GR-3.3 to step the +12V rail down to +3.3V. The LM2575 is reliable, inexpensive, and well-supported, but it is also an older switcher with a 52 kHz switching frequency and relatively poor noise performance for an audio application. Switching noise from the regulator can couple into the analog reference and supply pins of the microphone, raising the noise floor and reducing the effective signal-to-noise ratio of the captured audio. A Version 2.0 design should consider either a lower-noise switching regulator with a higher switching frequency (such as the TPS62933 or LMR33630, both of which switch above 1 MHz and offer significantly better ripple performance) or a hybrid approach where a switcher steps +12V down to +5V and a low-dropout linear regulator produces a clean +3.3V locally for the microphone. The LDO option costs slightly more current but yields the cleanest possible analog-domain supply, which is the right trade-off for an audio subsystem where the absolute current draw is small.

### Add Local Decoupling and a Ferrite Bead at the Microphone

The schematic shows a single 0.1 µF capacitor (C4) labeled as the power flag for the +3.3V rail near the microcontroller, but no dedicated decoupling network at the ICS-43434 microphone itself. Best practice for I²S MEMS microphones is to place a 0.1 µF ceramic capacitor and a 1–10 µF ceramic capacitor as close to the mic's VDD pin as possible, ideally with a ferrite bead between the main +3.3V rail and the microphone's local supply node. This isolates the mic from supply transients caused by the digital activity of the PIC and from any residual switcher ripple. In Version 2.0, the schematic should be updated to include a small ferrite bead in series with the mic's VDD trace, two ceramic decoupling capacitors directly at the mic, and a star-grounded analog ground island under the microphone footprint to reduce the chance that digital return currents corrupt the audio signal.

### Add a Dedicated Programming and Debug Header

The current schematic relies on the +12V power input and ICSP signals being available somewhere on the board, but there does not appear to be a dedicated, clearly labeled programming header for MCLR, PGC1, PGD1, VDD, and GND. During firmware bring-up the MPLAB Snap had to be connected through whatever pins were accessible, which is fragile and error-prone. Version 2.0 should include a properly labeled six-pin ICSP header in the standard Microchip pinout, placed near the edge of the board for easy access. A small status LED on the board, driven directly by the PIC18's RC4 "Debug_LED" pin shown on the existing schematic, should also be retained and ideally relocated to a clearly visible location near the programming header so that "is the chip alive" can be answered in a single glance.

### Provide Test Points for I²S Signals

While debugging the SPI-as-I²S implementation, it was necessary to probe the BCLK, WS, and SD lines on the microphone to confirm that the clocks were present and at the correct frequencies. On the current board, these signals are only accessible at the microphone's footprint pads, which are small and inconvenient for an oscilloscope or logic analyzer probe. Version 2.0 should add three labeled through-hole test points (or small loop pads) for BCLK, WS, and SD on the +3.3V side of the microphone connection. Test points add almost nothing to the bill of materials but pay for themselves the first time a firmware bug needs to be traced. Similarly, dedicated test points for the +3.3V rail, +12V input, and ground would speed up power-rail verification.

### Improve the UART Subsystem Connectors

The current design uses two 2x4 IDC connectors (U4 and U5) for daisy-chained UART communication with the rest of the robot. While IDC connectors are inexpensive and easy to crimp, they are also keyless and easy to misorient, and the existing schematic labels several of the IDC pins simply as "TBD." A Version 2.0 design should replace these with keyed JST or Molex connectors with a clearly defined pinout, finalize the function of each pin (rather than leaving any as TBD), and silkscreen the orientation directly on the PCB. This eliminates an entire category of integration-day mistakes where a cable is plugged in backwards and damages the +3.3V or +12V rails.

### Reroute the Microphone Away from Switching Regulator Components

Although the current schematic's exact PCB layout is not visible in the provided images, switching regulator inductors, diodes, and input/output capacitors are inherently noisy and should be physically distant from sensitive analog components like the MEMS microphone. In Version 2.0, the PCB layout should explicitly partition the board into a "noisy" region for the switching power supply and a "quiet" region for the microphone, op-amp, and analog ground island. The microphone should ideally be on the opposite side of the board from the LM2575 inductor (L1) and its associated components, and the analog ground should be tied to the digital ground at a single, well-defined point under the microcontroller. This kind of layout discipline is hard to enforce after the fact but is straightforward when planned from the start of a board revision.

### Remove or Repurpose the Op-Amp Block

The original schematic includes a "Microphone & Op-Amp" subsystem block that was intended to support an analog audio path, presumably for the speaker stretch goal. In Version 1.0 this block was wired but not validated, and the chosen ICS-43434 microphone is a fully digital part that does not require any op-amp conditioning on its input side. If the speaker stretch requirement is dropped from Version 2.0, the entire op-amp block should be removed to save board area and bill-of-materials cost. If the speaker requirement is retained, the op-amp block should be redesigned around a proper Class-D audio amplifier driven by an I²S DAC (such as the MAX98357A), which would integrate cleanly with the same I²S bus that the microphone uses and avoid the noise problems associated with an op-amp-and-discrete-driver approach.

### Add Reverse-Polarity and Overcurrent Protection on the +12V Input

The +12V barrel jack input feeds directly into the LM2575 with no protection beyond what the regulator itself provides. A Version 2.0 design should add a series Schottky diode or a P-channel MOSFET in the supply line to protect against reverse polarity (a common mistake when plugging in barrel-jack adapters), and a resettable polyfuse rated slightly above the worst-case current draw of the entire subsystem to protect against shorts during integration. These two components add only a few cents to the board cost but can prevent the kind of catastrophic failure that destroys an entire subsystem during a benchtop accident.

### Consolidate the Extra Pin Headers

The current schematic includes a generic "Extra_Pin_Headers" connector (U1) exposing four spare pins from the PIC and a ground reference. While useful during prototyping, by the second revision these pins should either be assigned to specific, documented functions (for example, an HMI interface, a debug UART, or an external sensor input) or removed if they are not needed. Leaving them as undocumented "extra" pins encourages future students or teammates to use them inconsistently, which makes integration and bring-up harder. Version 2.0 should make every pin on every connector intentional and documented in the schematic title block.

### Summary

Taken together, these changes would transform the Audio Subsystem from a working prototype into a production-quality module. The most impactful single change is the migration to a microcontroller with native I²S support, which would eliminate the firmware complexity that consumed the largest portion of the development effort. After that, the highest-leverage improvements are the analog supply cleanup around the microphone, the addition of dedicated debug and test infrastructure, and the introduction of basic power input protection. None of these changes are exotic; they are all standard practices that would normally be applied during a board revision, and applying them here would result in a Version 2.0 board that is easier to debug, easier to integrate, and produces measurably better audio.
