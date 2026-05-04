---
title: Reflection
---

## Reflection

### Review of Module's Success

Looking back on the original Audio Subsystem requirements, the module met most of its core goals, although a few stretch items were not fully realized.

**Requirements successfully accomplished:**

- The surface-mounted +3.3V switching regulator (LM2575GR-3.3) was implemented and produces a stable rail well within the ±5% tolerance specified in the requirements document.
- A surface-mounted PIC18F27Q43-I/SS microcontroller was selected, configured, and successfully programmed using MPLAB X IDE with MCC Melody.
- The microphone successfully captures audible human speech at conversational distance using the ICS-43434 I²S MEMS microphone.
- Peripheral communication using the I²S protocol was implemented by emulating I²S on the PIC18's SPI peripheral, with PWM1_16BIT generating the WS signal and DMA1 streaming received data into a RAM buffer with no CPU intervention.
- UART daisy-chain connectivity to the rest of the robot was wired through the U4 and U5 2x4 IDC connectors and verified at the schematic and pinout level.

**Requirements that were missed or only partially met:**

- The wireless communication stretch goal (Wi-Fi via MQTT) was not implemented within this subsystem since the PIC18-Q43 has no on-board Wi-Fi and that responsibility was left to a different subsystem.
- The speaker stretch requirement was not completed; the audio output path through the on-board op-amp section was designed in the schematic but not validated for the 90 dB / 2–5 kHz target.
- The ground vibration detection stretch requirement was not pursued because the necessary sensor hardware (geophone or accelerometer) was outside the scope of the parts ordered.
- HMI integration was wired at the connector level but not exercised through complete software integration with the OLED and pushbutton subsystem before the deadline.

### Microcontroller / Module Startup Tip

A few tips that would have saved significant time at the beginning of the project:

- **Read the peripheral documentation before configuring anything in MCC.** MCC Melody hides a lot of complexity, but the option names differ from older MCC and from the datasheet, and guessing at field meanings wastes hours. Open the `dma1.c` or `spi1.c` generated file after every change to confirm the registers actually look right.
- **The PIC18F27Q43 does not have a native I²S peripheral.** Plan for SPI + PWM + DMA emulation from the start — this changes the pin map, the clock budget, and the buffer parsing strategy. Discovering this halfway through is painful.
- **Check the `Destination Region` field in DMA before generating.** If it is left as `SFR`, the DMA writes into a random register instead of your RAM buffer and silently does nothing useful. This single setting cost more debug time than any other.
- **The MPLAB Snap does not provide power to the target board.** Always plug in the external supply before launching a debug session, and confirm the 3.3V rail with a multimeter before assuming the chip is even running.
- **Use `DMA1_TransferWithTriggerStart()`, not `DMA1_TransferStart()`** for hardware-triggered DMA. The former sets the SIRQEN bit; the latter just sets DGO and the transfer never arms on the SPI receive interrupt.
- **Verify clocks on a scope before debugging firmware.** SCK, WS, and SD on the I²S bus should all be visibly toggling at the expected frequencies before any data inspection in the debugger is meaningful. If the clocks look wrong, no amount of buffer parsing will fix it.
- **MCC Melody field names do not always map cleanly to datasheet register names.** When unsure which trigger source to pick or what a setting maps to, use the Register Initialization tab to read the actual register values and cross-reference the datasheet directly.
- **Do not use I²C and I²S interchangeably.** They share no protocol, no pin count, and no timing. The naming similarity tricks people into thinking one can be substituted for the other; it cannot.
- **The ICS-43434's BCLK must be between roughly 2.048 MHz and 4.096 MHz** for normal mode. Picking a sample rate that pushes BCLK outside this window will cause the mic to output nothing or output garbage, with no obvious error indication.
- **Set up UART printf early** — even before the main feature works. Being able to see "buffer[0] = 0xXX" stream out over a serial terminal is dramatically faster than relying solely on the debugger's variable inspector, especially when the Snap throws warnings.

### Lessons Learned

The following ten lessons summarize the most important takeaways from the audio subsystem effort:

1. **Choose the microcontroller around the actual peripheral requirements, not just brand familiarity.** The PIC18F27Q43 was inexpensive and accessible, but it lacked a native I²S peripheral and forced a workaround using SPI, PWM, and DMA together. Time spent emulating I²S could have been avoided by selecting a part with a dedicated I²S module, and that trade-off should be evaluated explicitly during component selection rather than discovered halfway through firmware development.

2. **Schematic readability matters as much as schematic correctness.** Several debugging sessions involved confusion about whether WS was on RB5 or RB6 because the labels in the original schematic photo were small and ambiguous. Clear pin labeling, consistent net names, and legible export resolution save hours during firmware bring-up and design review conversations.

3. **Datasheet specs constrain the system more than expected.** The ICS-43434's minimum BCLK requirement of about 2.048 MHz forced the sample rate to stay at or above 32 kHz, which in turn fixed the WS frequency, the SPI clock divider, and the DMA buffer size. Reading the datasheet's "operating modes" section thoroughly before picking a sample rate would have made the entire configuration sequence feel less like guesswork.

4. **Tool-generated code is not a substitute for understanding the underlying registers.** MCC Melody produces a working `DMA1_Initialize()` function, but reading that function and confirming the `DMAnDSA`, `DMAnSIRQ`, and `DMAnCON0` values match expectation was the only way to catch the destination-register bug. Generated code accelerates the boring parts but cannot diagnose itself.

5. **Hardware setup mistakes are easier to catch with an oscilloscope than with a debugger.** Wasting an hour stepping through firmware to figure out why DMA never fired could have been replaced with five minutes of probing SCK and WS to confirm they were physically present and at the correct frequencies. Visualizing the signals on the wire short-circuits a huge class of bugs.

6. **Power budgets should be drawn up before the schematic, not after.** The audio subsystem only ended up needing one rail (+3.3V) with a comfortable margin, but other students on the team discovered partway through that their rails were under-provisioned. A clear budget at the requirements stage prevents redesign during integration.

7. **Documentation written during the project is dramatically better than documentation written after.** Markdown files for component selection, requirements, and the power budget were easier to write while the decisions were fresh. Reconstructing the rationale for choices made weeks earlier — like why the 12V external supply was chosen — is much harder than simply writing it down at the time of decision.

8. **Subsystem teams need to agree on physical interfaces early.** UART pinouts, voltage levels, connector orientation, and ground references between the audio module and the rest of the robot caused integration friction. A signed-off interface document at the start would have prevented several rework cycles late in the project.

9. **Vague requirements lead to vague evaluation.** The original microphone requirement said it should "transmit captured audio" without specifying what format, sample rate, or quality. Tightening that language to include I²S protocol, sample rate, and a measurable speech-capture target made it possible to evaluate whether the subsystem actually met the requirement at the end.

10. **Asking for help earlier is faster than struggling alone longer.** The DMA destination-region bug, the I²S vs. I²C confusion, and the MPLAB Snap power issue were all things that would have been resolved in minutes by asking a teammate, professor, or AI tool, but instead consumed hours of self-directed debugging. Recognizing the boundary between productive struggle and stuck struggle is a skill worth building intentionally.

### Recommendations for Future Students

1. **Get comfortable with reading datasheets before the class begins, particularly the section on peripheral block diagrams and timing diagrams.** Most of the early debugging effort comes from misunderstanding what a peripheral can and cannot do, and the datasheet always has the answer well before any forum post does.

2. **Pick your microcontroller to match the protocols your peripherals actually require, not the chip your team is most familiar with.** A small amount of research at the component-selection stage saves enormous amounts of firmware effort later, especially for protocols like I²S, USB, or CAN that benefit hugely from native hardware support.

3. **Buy or borrow an inexpensive logic analyzer or oscilloscope before the project starts and learn how to use it on simple signals first.** Almost every embedded bug becomes obvious the moment the signals are visible, and learning to read a scope under deadline pressure is a poor time to start.

4. **Write your documentation as you go, not at the end of the project.** Every design choice, component selection, and debugging insight should be recorded in markdown immediately, with the rationale included. End-of-semester documentation crunches produce worse documents and lose information that was clear at the time of the decision.

5. **Treat your team's interfaces — power rails, UART pinouts, ground references, connector orientations — as contracts that need to be signed off before any subsystem starts building.** Nearly every integration-day surprise comes from a small assumption that one subsystem made about another, and a one-page interface specification prevents almost all of them.
