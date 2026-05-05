---
title: Hardware V2.0
---

## Hardware V2.0

If a second version of the Audio Subsystem hardware were to be designed, several improvements could be made based on what was learned during firmware bring up, debugging, and integration with the rest of the robot. The current Version 1.0 board works and meets the core requirements, but using it showed a few areas where small changes to the schematic and PCB would improve reliability, make debugging easier, and produce cleaner audio. The sections below describe each proposed change and why it should be made, with reference to the existing schematic where it helps.

### Switch to a Microcontroller with a Native I2S Peripheral

The biggest hardware improvement for Version 2.0 would be replacing the PIC18F27Q43 with a microcontroller that has a real I2S peripheral built in, such as a PIC32MZ, SAMD21, ESP32 S3, or RP2040. The current design uses the PIC18 SPI module to receive serial audio data and a separate PWM output to make the word select signal, with DMA moving bytes from the SPI receive buffer into RAM. This approach works, but it took a large amount of project time because the SPI byte boundaries are not locked to the WS edges. That mismatch forces the firmware to align frames in software and makes the buffer parsing more complicated. A real I2S peripheral creates BCLK and WS in hardware with the right timing, hands the firmware a properly framed receive register, and connects to DMA without any extra workaround. This change alone would simplify the firmware a lot, free up a PWM module and a timer for other uses, and remove the risk of small audio glitches caused by misaligned frames.

### Replace the LM2575 Switching Regulator with a Quieter Alternative

The power supply section uses an LM2575GR 3.3 to step the 12V input down to 3.3V. The LM2575 is reliable, cheap, and easy to find, but it is also an older switcher with a 52 kHz switching frequency and fairly noisy output for an audio application. Switching noise can leak into the supply pins of the microphone, raise the noise floor, and lower the audio quality. A Version 2.0 design should use either a quieter switcher with a higher switching frequency, like the TPS62933 or LMR33630 (both switch above 1 MHz and have much cleaner output), or a hybrid approach where a switcher steps 12V down to 5V and a small linear regulator makes a clean 3.3V locally for the microphone. The linear regulator wastes a tiny bit of power but gives the cleanest possible supply, which is the right tradeoff for an audio board where the total current draw is small.

### Add Local Decoupling and a Ferrite Bead at the Microphone

The schematic shows a single 0.1 uF capacitor (C4) near the microcontroller for the 3.3V rail, but no dedicated decoupling at the ICS 43434 microphone itself. The standard practice for I2S MEMS microphones is to place a 0.1 uF ceramic capacitor and a 1 to 10 uF ceramic capacitor as close to the mic VDD pin as possible, with a small ferrite bead between the main 3.3V rail and the local mic supply. This setup keeps the mic isolated from voltage spikes caused by the digital activity of the PIC and from any leftover ripple from the regulator. In Version 2.0, the schematic should add a ferrite bead in series with the mic VDD trace, two ceramic capacitors right at the mic, and an analog ground island under the mic footprint that connects to the rest of the ground at a single point.

### Add a Dedicated Programming and Debug Header

The current schematic does include an ICSP header (U7) for programming, but it could be made more usable. During firmware bring up the MPLAB Snap had to be connected carefully because the header was not in the most convenient location and the surrounding components made probing tight. Version 2.0 should keep a six pin ICSP header in the standard Microchip pinout, but place it right at the edge of the board with clear silkscreen labels for MCLR, PGC, PGD, VDD, and GND. A small status LED, driven by the same RC4 Debug_LED pin already in the design, should sit next to the header so that the answer to "is the chip alive" is visible in one quick glance.

### Provide Test Points for I2S Signals

While debugging the SPI as I2S setup, it was necessary to probe the BCLK, WS, and SD lines at the microphone to confirm that the clocks were present and at the right frequencies. On the current board these signals are only available at the small microphone pads, which are hard to reach with an oscilloscope or logic analyzer probe. Version 2.0 should add three labeled through hole test points (or small loop pads) for BCLK, WS, and SD on the 3.3V side of the microphone connection. Test points cost almost nothing on the bill of materials but pay for themselves the first time a firmware bug needs to be traced. Test points for the 3.3V rail, 12V input, and ground would also speed up power supply checks.

### Improve the UART Subsystem Connectors

The current design uses two 2x4 IDC connectors (U4 and U5) for the daisy chained UART link to the rest of the robot. IDC connectors are cheap and easy to crimp, but they are not keyed, so they are easy to plug in backwards. Several of the IDC pins are also still labeled as "TBD" on the schematic. A Version 2.0 design should replace these with keyed JST or Molex connectors that only fit one way, finalize the function of every pin, and add silkscreen markings on the PCB to show the correct orientation. This change removes a whole category of integration day mistakes where a cable is plugged in backwards and shorts the 3.3V or 12V rails.

### Move the Microphone Away from the Switching Regulator

Switching regulator parts (the inductor, the diode, and the input and output capacitors) make a lot of high frequency noise. Sensitive analog parts like the MEMS microphone should sit far away from them. In Version 2.0, the PCB layout should split the board into a "noisy" area for the switcher and a "quiet" area for the microphone and its decoupling. The microphone should ideally sit on the opposite side of the board from the LM2575 inductor (L1) and its supporting parts. The analog ground should connect to the digital ground at a single point under the microcontroller. This kind of layout planning is hard to fix after the fact but easy to do at the start of a new revision.

### Remove or Replace the Op Amp Block

The original schematic includes a "Microphone and Op Amp" block that was meant to support an analog audio path, most likely for the speaker stretch goal. In Version 1.0 this block was wired up but never tested, and the ICS 43434 microphone is fully digital, so it does not need any op amp on its input side. If the speaker stretch goal is dropped in Version 2.0, the whole op amp block should be removed to save board space and parts cost. If the speaker is kept, the op amp block should be replaced with a small Class D audio amplifier driven by an I2S DAC (such as the MAX98357A). That choice would share the same I2S bus as the microphone and avoid the noise problems that come with a discrete op amp and speaker driver.

### Add Reverse Polarity and Overcurrent Protection on the 12V Input

The 12V barrel jack input goes straight into the LM2575 with no protection beyond what the regulator itself offers. A Version 2.0 design should add a Schottky diode or a P channel MOSFET in series with the supply to protect against a reversed barrel jack adapter, plus a resettable polyfuse rated just above the worst case current draw to protect against shorts during integration. These two parts cost very little but they prevent the kind of catastrophic failure that can destroy an entire subsystem during a simple bench accident.

### Consolidate the Extra Pin Headers

The current schematic includes a generic "Extra_Pin_Headers" connector (U1) that exposes four spare pins from the PIC and a ground reference. Spare pins are useful during prototyping, but in a second revision these pins should either be given a specific, documented job (such as an HMI interface, a debug UART, or an external sensor input) or removed entirely if they are not needed. Leaving them labeled as generic extra pins makes it more likely that future teammates or students will use them in inconsistent ways, which makes integration harder. Version 2.0 should make every pin on every connector intentional and clearly documented in the schematic title block.

### Summary

Taken together, these changes would turn the Audio Subsystem from a working prototype into a much more polished design. The single biggest improvement is the move to a microcontroller with built in I2S support, which would remove the firmware complexity that took up the largest part of the development effort. After that, the most useful improvements are the cleaner power supply around the microphone, the addition of test points and a better debug header, and the basic input protection on the 12V supply. None of these changes are unusual; they are all standard practices for a second board revision, and applying them here would produce a Version 2.0 that is easier to debug, easier to integrate, and produces noticeably cleaner audio.
