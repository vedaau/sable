---
title: Microcontroller-Selection
tags:
- tag1
- tag2
---

| PIC Info                                      |  Answer                                                                                                      |
| --------------------------------------------- |  --------------------------------------------------------------------------------------------------------- |
| Model                                          | PIC18F27Q43-I/SS         |
| Product Page URL                                    | [Microchip PIC18F27Q43](https://www.microchip.com/en-us/product/pic18f27q43)                                                                                    |
| PIC18F27Q43 Datasheet URL                           | [PIC18F27Q43 Datasheet](https://ww1.microchip.com/downloads/aemDocuments/documents/MCU08/ProductDocuments/DataSheets/PIC18F27_47Q43-Data-Sheet-DS40002147.pdf)                                              |
| PIC18-Q43 Family Reference                          | [PIC18-Q43 Product Family Page](https://www.microchip.com/en-us/parametric-search/v3?series=PIC18-Q43)                                                                              |
| MPLAB X IDE / MCC Documentation                     | [MPLAB X IDE Documentation](https://microchipdeveloper.com/mplabx:start)                                                        |
| Vendor link                                         | [Newark](https://www.newark.com/microchip/pic18f27q43-i-ss/mcu-8bit-64mhz-dip-28/dp/01AH3802?CMP=AFC-DIGIPART)                       |
| Code Examples                                       | [Microchip MPLAB Discover Examples](https://mplab-discover.microchip.com/), [PIC18F-Q43 I²S via SPI Application Note](https://www.microchip.com/en-us/application-notes)  |
| External Resources URL(s)                           | [MCC Melody User Guide](https://onlinedocs.microchip.com/oxy/GUID-5A41FAB1-94F6-4574-A89F-B16D24330BB7-en-US-2/index.html), [ICS-43434 Microphone Datasheet](https://invensense.tdk.com/wp-content/uploads/2016/02/DS-000069-ICS-43434-v1.2.pdf), [PIC18F27Q43 SPI/I²S Configuration Guide](https://onlinedocs.microchip.com/oxy/GUID-9A1A39D4-F5A9-4AB5-89CC-20C51E2EECD5-en-US-2/index.html)  |
| Unit cost                                           | $2.43                                                               |
| Absolute Maximum Current for entire IC              | 250 mA                                                                 |
| Supply Voltage Range                                 | 1.8V – 5.5V                                                |
| Absolute Maximum current <br> (for entire IC)       | 250 mA                                                                                     |
| Maximum GPIO current <br> (per pin)                 | 50 mA (source/sink)                                                                                     |
| Supports External Interrupts?                       | Yes (Interrupt-on-Change on most pins, plus dedicated INT0/INT1/INT2)                                                                                     |
| Required Programming Hardware, Cost, URL            | [MPLAB Snap In-Circuit Debugger – $34.95](https://www.microchip.com/en-us/development-tool/pg164100)                                                                                  |

| Module         | # Available | Needed | Associated Pins (or * for any) |
| -------------- | ----------- | ------ | ------------------------------ |
| UART           | 5           | 1      |     RC6 (TX), RC7 (RX)                         |
| SPI            | 2           | 1      | RB3 (SCK), RB4 (SDI) — used for I²S emulation                              |
| I²C            | 2           | 0      | -                             |
| GPIO           | 25          | 5      | RA0–RA5 (extra headers), RB5 (WS output)                              |
| ADC            | 1 (with 35 channels)           | 0     | -                              |
| PWM (16-bit)   | 4           | 1      | RB5 (PWM1_16BIT Output1 → I²S WS)                            |
| DMA            | 8           | 1      | DMA1: SPI1RXB → RAM buffer                            |
| Timer          | 6           | 1      | TMR2 (PWM clock source)                            |
| Programmer Interface | 1     | 1      | MCLR (RE3), PGC1 (RB6), PGD1 (RB7)                            |




The PIC18F27Q43 was selected for the Audio (Microphone) Subsystem because it provides the peripheral mix needed to interface a digital I²S microphone (ICS-43434) without requiring a more expensive 32-bit MCU. Although the PIC18-Q43 family does not have a dedicated I²S peripheral, its SPI module combined with a 16-bit PWM and the on-chip DMA controller allows the I²S protocol to be emulated in hardware: SPI1 generates the bit clock and shifts in serial data on RB3/RB4, the PWM1_16BIT module produces the 32 kHz word-select signal on RB5, and DMA1 streams received bytes from the SPI receive buffer directly into RAM with no CPU intervention. UART1 (RC6/RC7) is used to forward processed audio data to the rest of the robot over the daisy-chained subsystem bus, and additional GPIO is reserved for status indication and interfacing with the Human-Machine Interface. The 64 MHz internal oscillator provides enough headroom to handle continuous audio capture at 32 kHz / 24-bit while leaving CPU cycles available for buffer processing and communication, making the PIC18F27Q43 a cost-effective and well-matched choice for this subsystem.
