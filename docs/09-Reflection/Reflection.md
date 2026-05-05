---
title: Reflection
---

## Reflection

### Review of Module's Success

Looking back on the original Audio Subsystem requirements, the module met most of its core goals, even though a few stretch goals were not finished.

**Requirements that were met:**

- The surface mounted 3.3V switching regulator (LM2575GR 3.3) was built and produces a stable rail well within the plus or minus 5 percent tolerance from the requirements document.
- A surface mounted PIC18F27Q43 I/SS microcontroller was selected, set up in MCC Melody, and programmed using MPLAB X IDE.
- The microphone captures clear human speech at conversational distance using the ICS 43434 I2S MEMS microphone.
- I2S communication was set up by using the PIC SPI module to receive serial data, the PWM1_16BIT module to make the WS signal, and DMA1 to move bytes from the SPI receive buffer into RAM with no help from the CPU.
- UART daisy chain communication with the rest of the robot was wired through the U4 and U5 2x4 IDC connectors and checked at the schematic and pinout level.

**Requirements that were missed or only partly met:**

- The Wi Fi stretch goal was not done in this subsystem because the PIC18 Q43 has no Wi Fi built in. That job was left to a different subsystem on the team.
- The speaker stretch goal was not finished. The audio output path through the on board op amp section was drawn in the schematic but never tested at the 90 dB and 2 to 5 kHz target.
- The ground vibration detection stretch goal was not pursued because the sensor hardware needed (a geophone or an accelerometer) was not part of the parts order.
- HMI integration was wired up at the connector level but was not fully tested with the OLED and pushbutton subsystem before the deadline.

### Microcontroller and Module Startup Tips

A few tips that would have saved a lot of time at the start of the project:

- **Read the peripheral documentation before setting anything up in MCC.** MCC Melody hides a lot of complexity, but the field names are different from the older MCC and from the datasheet. Guessing at what a setting means wastes hours. Open the generated `dma1.c` or `spi1.c` file after every change and check that the registers actually look right.
- **The PIC18F27Q43 does not have a real I2S peripheral.** Plan from the very start to fake I2S using SPI, PWM, and DMA together. This choice changes the pin map, the clock budget, and the way the buffer has to be parsed. Finding out halfway through is painful.
- **Check the Destination Region field in DMA before generating code.** If it is left as SFR, the DMA writes into a random register instead of into the RAM buffer and silently does nothing useful. This one setting cost more debug time than anything else.
- **The MPLAB Snap does not power the target board.** Always plug in the external supply before starting a debug session, and check the 3.3V rail with a multimeter before assuming the chip is even running.
- **Use `DMA1_TransferWithTriggerStart()`, not `DMA1_TransferStart()`.** The first one sets the SIRQEN bit so the DMA arms on the SPI receive interrupt. The second one only sets DGO, and the transfer never fires.
- **Check the clocks on a scope before debugging firmware.** SCK, WS, and SD on the I2S bus should all be visibly toggling at the right frequencies before any data check in the debugger is meaningful. If the clocks look wrong, no amount of buffer parsing will fix the problem.
- **MCC Melody field names do not always match the datasheet register names.** When unsure which trigger source to pick or what a setting maps to, use the Register Initialization tab to read the actual register values and look them up in the datasheet directly.
- **Do not use I2C and I2S as if they are the same thing.** They share no protocol, no pin count, and no timing. The names look alike, but the protocols are completely different and one cannot replace the other.
- **The ICS 43434 BCLK must sit between about 2.048 MHz and 4.096 MHz** for normal mode. Picking a sample rate that pushes BCLK outside this window will cause the mic to output nothing or output garbage, with no clear error message.
- **Set up UART printf early, even before the main feature works.** Being able to watch "buffer[0] = 0xXX" stream out over a serial terminal is much faster than relying only on the debugger variable window, especially when the Snap throws warnings.

### Lessons Learned

The following ten lessons sum up the most important takeaways from the audio subsystem work:

1. **Pick the microcontroller based on what the peripherals actually need, not on what brand the team is most familiar with.** The PIC18F27Q43 was cheap and easy to get, but it had no real I2S peripheral and forced a workaround using SPI, PWM, and DMA together. The time spent faking I2S could have been avoided by picking a chip with a real I2S module. That tradeoff should be looked at early during component selection, not discovered halfway through firmware work.

2. **A clear schematic matters as much as a correct schematic.** Several debug sessions were lost trying to figure out whether WS was on RB5 or RB6 because the labels in the original schematic photo were small and easy to mix up. Clean pin labels, consistent net names, and a high resolution export save hours during firmware bring up and during design review conversations.

3. **Datasheet specs limit the design more than they look like they will.** The ICS 43434 minimum BCLK of about 2.048 MHz forced the sample rate to stay at or above 32 kHz, which then fixed the WS frequency, the SPI clock divider, and the DMA buffer size. Reading the operating modes section of the datasheet carefully before picking a sample rate would have made the whole setup feel less like guesswork.

4. **Generated code is not a replacement for understanding the registers underneath.** MCC Melody produces a working `DMA1_Initialize()` function, but reading that function and checking that the values for `DMAnDSA`, `DMAnSIRQ`, and `DMAnCON0` looked right was the only way to catch the destination register bug. Generated code speeds up the boring parts but cannot debug itself.

5. **Hardware mistakes are easier to catch with a scope than with a debugger.** Spending an hour stepping through firmware to figure out why DMA never fired could have been replaced with five minutes of probing SCK and WS to check that the clocks were even there. Looking at the actual signals on the wire takes care of a whole class of bugs at once.

6. **Power budgets should be done before the schematic, not after.** The audio subsystem only ended up needing one rail (3.3V) with plenty of margin, but other team members found partway through the project that their rails were too small. A clear power budget at the requirements stage prevents redesigns later during integration.

7. **Documentation written during the project is much better than documentation written at the end.** Markdown pages for component selection, requirements, and the power budget were easier to write while the decisions were fresh. Trying to remember weeks later why the 12V supply was chosen, for example, is much harder than just writing it down at the time.

8. **Teams need to agree on physical interfaces early.** UART pinouts, voltage levels, connector orientation, and ground references between the audio module and the rest of the robot caused friction during integration. A short signed off interface document at the start would have prevented several late rework cycles.

9. **Vague requirements lead to vague results.** The original microphone requirement said it should "transmit captured audio" with no mention of format, sample rate, or quality. Tightening that wording to include the I2S protocol, the sample rate, and a measurable speech capture target made it possible at the end to actually check whether the subsystem met the requirement.

10. **Asking for help earlier is faster than struggling alone longer.** The DMA destination region bug, the confusion between I2S and I2C, and the MPLAB Snap power issue were all problems that could have been solved in minutes by asking a teammate, the professor, or an AI tool. Instead they ate up hours of solo debugging. Knowing when to keep pushing and when to ask for help is a real skill worth building on purpose.

### Recommendations for Future Students

1. **Get comfortable reading datasheets before the class starts, especially the peripheral block diagrams and timing diagrams.** Most of the early debugging time comes from not understanding what a peripheral can and cannot do, and the answer is almost always sitting in the datasheet long before it shows up on a forum post.

2. **Pick a microcontroller that already supports the protocols your peripherals need, not just the chip your team has used before.** A little research during component selection saves a lot of firmware effort later, especially for protocols like I2S, USB, or CAN that work much better on chips with real hardware support.

3. **Get hold of a cheap logic analyzer or oscilloscope before the project starts and practice using it on easy signals first.** Almost every embedded bug becomes obvious the moment the signals are visible, and learning to read a scope under deadline pressure is the worst time to start.

4. **Write your documentation as you go, not at the end of the project.** Every design choice, component selection, and debugging note should be written in markdown right away, with the reason included. Trying to write all the documentation at the end of the semester produces weaker documents and loses information that was clear at the time.

5. **Treat your team interfaces (power rails, UART pinouts, ground references, connector orientation) as agreements that need to be signed off before any subsystem starts building.** Almost every surprise on integration day comes from one small assumption that one subsystem made about another, and a single page interface document prevents almost all of them.
