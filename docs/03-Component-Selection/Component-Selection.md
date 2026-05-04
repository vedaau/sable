---
title: Module's Selected Major Components
---

## Module's Selected Major Components

The following sections describe the major components used in the Audio(microphone) Subsystem. These include the voltage regulator that provides a stable 3.3 V power supply and the microphone module that captures sound from the environment. Each component was chosen because it works well with the subsystem microcontroller and meets the power and performance needs of the project. Together, these parts allow the subsystem to reliably detect audio signals while keeping the design simple and efficient within the overall system.

### Power Management

**Selected Voltage Switching Regulator: LM2575GR-3.3V Regulator**

<img src="https://mm.digikey.com/Volume0/opasdata/d220001/derivates/1/001/176/122/MFG_TO-263-5L_sml%28200x200%29.jpg" 
     width="300" 
     alt="Product Image">


[Link to product](https://www.digikey.com/en/products/detail/taejin/LM2575GR-3-3/22237505)


### Microphone

**Selected Sensor: ICS-43434 Digital I²S MEMS Microphone**

<img src="https://mm.digikey.com/Volume0/opasdata/d220001/derivates/1/002/379/783/MFG_ICS-43434_sml.jpg" 
     width="300" 
     alt="Product Image">


 [Link to product](https://www.digikey.com/en/products/detail/tdk-invensense/ICS-43434/6140298)  



-----------

*Table 1: Component selection*

**3.3 Volt-switching Regulator**

| **Component**                                                                                                                                                                                      | **Pros**                                                                                                                                    | **Cons**                                                                                            |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| ![](https://mm.digikey.com/Volume0/opasdata/d220001/derivates/1/001/176/122/MFG_TO-263-5L_sml%28200x200%29.jpg)<br> LM2575GR-3.3V Regulator <br>$0.60/each<br>[link to product](https://www.digikey.com/en/products/detail/taejin/LM2575GR-3-3/22237505)                 |Wide Input Voltage Range<br><br> Built-in Protection<br><br>Efficient Power Conversion                                               | Larger and Older Design <br><br> Noise |
| ![](https://cdn-shop.adafruit.com/970x728/2165-00.jpg)<br> LD1117-3.3TO-220 3.3V Voltage Regulator <br>$1.25/each<br>[link to product](https://www.adafruit.com/product/2165)                 |Inexpensive<br><br> Built-in protection<br><br> Widely available <br><br>Easy to use                                               | Inefficient for large voltage drops <br><br>Gets hot at higher current<br><br> Not suitable for battery efficiency|
| ![](https://mm.digikey.com/Volume0/opasdata/d220001/derivates/1/001/202/815/296%7E4202561%7EDDA%7E8_sml%28200x200%29.jpg)<br> Texas Instruments TPS7B8633DQDDARQ1 3.3V LDO <br>$2.30/each<br>[link to product](https://www.digikey.com/en/products/detail/texas-instruments/TPS7B8633DQDDARQ1/16627798)                 | Low idle current <br><br> Easy Integration <br><br> Wide Input Voltage Range                                               | Lower efficiency <br><br>Wastes power as heat <br><br> Requires external capacitors |

**Choice:** Option 1: LM2575GR-3.3V Regulator

**Rationale:** The LM2575GR-3.3V Regulator was chosen because it efficiently converts higher voltages to 3.3 V with low heat loss, and it can supply up to about 1 amp for microcontrollers and other circuits. It works from a wide range of input voltages and only needs a few extra parts, which is useful for most power supply designs. While not the smallest or newest part, it's simple and reliable for stepping down to 3.3 V in many embedded projects.


**Microphone**

| **Component**                                                                                                                                                                                      | **Pros**                                                                                                                                    | **Cons**                                                                                            |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| ![](https://mm.digikey.com/Volume0/opasdata/d220001/derivates/1/002/379/783/MFG_ICS-43434_sml.jpg)<br>Option 1.<br> ICS-43434 Digital I²S MEMS Microphone <br>$3.20/each<br>[link to product](https://www.digikey.com/en/products/detail/tdk-invensense/ICS-43434/6140298)                 | Digital I²S output (no analog noise pickup) <br><br> High SNR (65 dB) <br><br> Built-in 24-bit ADC <br><br> Wide frequency range (50 Hz – 20 kHz) <br><br> Low power consumption | Higher cost than analog mics <br><br> Requires I²S-capable controller <br><br> More complex firmware setup |        
| ![](https://mm.digikey.com/Volume0/opasdata/d220001/derivates/2/001/212/MFG_MFG_CMA-4544PF-W%28640x640%29.jpg?hidebanner=true)<br> Option 2. <br> CMA-4544PF-W Microphone <br> $0.76/each <br> [Link to product](https://www.digikey.com/en/products/detail/same-sky-formerly-cui-devices-/CMA-4544PF-W/1869981) |  Good sound range <br><br> Low power use <br><br> Omnidirectional | Sensitive to Noise <br><br> Basic audio quality <br><br> Limited temperature range|
| ![](https://mm.digikey.com/Volume0/opasdata/d220001/derivates/1/010/508/MFG_CMEJ-0413-42-SMT-TR_sml.jpg)<br> Option 3. <br> CMEJ-0413-42-SMT-TR Microphone <br> $0.59/each <br> [Link to product](https://www.digikey.com/en/products/detail/same-sky-formerly-cui-devices-/CMEJ-0413-42-SMT-TR/10253448) |  Low Cost <br><br> Omnidirectional Pickup <br><br> Very Compact & Easy to Integrate <br><br> Good for general sound |  Limited frequency range <br><br> not suitable for professional audio <br><br> more noise and lower dynamic range |   

**Choice:** Option 1: ICS-43434 Digital I²S MEMS Microphone

**Rationale:** The ICS-43434 was chosen because it provides high-quality digital audio output directly over I²S, eliminating the need for an external ADC or analog signal conditioning circuitry. With a 65 dB signal-to-noise ratio and a flat frequency response from 50 Hz to 20 kHz, it captures clear human speech at conversational distances, which directly supports the subsystem's goal of detecting and transmitting audio from survivors. Its digital interface is robust against electrical noise from nearby motors and switching regulators, which is important in a robotics platform where analog mic signals would otherwise pick up interference. While more expensive than analog electret options, the improved audio clarity, integrated 24-bit ADC, and noise immunity justify the cost for this application.
