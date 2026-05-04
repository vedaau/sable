---
title: Module's Requirements
---

## Module Requirements
The following sections document the requirements that the this module need to fulfills to provide clear audio input and output, detect vibrations in the ground, and communicate properly with the rest of the robot. These requirements make sure the subsystem can help locate survivors, allow rescuers to talk to them, and meet all project and course system constraints.

| **Requirement Description** | **Measure of<br> Threshold** | **Target<br>Measure** |**Stretch<br>Requirement<br>(Y-N)**|
|-----------------------------| ----------------- | ----------------- | :-----: |
| Surface mounted, 3.3V switching power regulator | Output voltage within ±5% of 3.3V | Stable 3.3V output under full subsystem load | N |
| Surface mounted microcontroller | 1 PIC or ESP | 8-bit PIC | N |
| Wireless Communication | Able to send or receive a Wi-Fi data | Send and receive Wi-Fi Data to MQTT | Y |
| Microphone | Captures audible human speech within 1 meter of the robot | Clearly captures normal-volume speech at 1 meter with sufficient SNR for intelligibility | N |
| Human Design Interface Integration | The audio subsystem shall interface with the HMI so that sensor readings and operating states can be viewed on an OLED display and adjusted using physical pushbuttons | The audio subsystem shall support all MoT features and allow on-device tuning of microphone gain and speaker output through external controls | N |
| Peripheral Communication | The audio peripherals shall communicate with the main controller using the I²S protocol | The audio subsystem shall maintain reliable I²S communication at 32 kHz sample rate, 24-bit depth | N |
| UART Networking | The audio subsystem shall support a daisy-chained UART connection to the rest of the robot subsystems | The audio subsystem shall support dedicated UART links for higher-speed communication where required | N |
| Speaker | The subsystem shall be capable of playing operator-provided audio through an onboard speaker while simultaneously monitoring ambient sound | The subsystem shall reproduce speech at sound levels up to 90 dB in the 2–5 kHz frequency range while continuing to capture environmental audio | Y |
| Ground Vibration Detection | The subsystem shall detect the presence or absence of ground vibrations | The subsystem shall be able to sense vibration frequencies associated with human footsteps | Y |
