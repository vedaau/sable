---
title: API
---

## API

This page lists the messages that the Audio Subsystem sends, receives, and passes along on the team's daisy chained UART network. Every message follows the class wide protocol frame, which is 64 bytes long. The first two bytes are 0x41 and 0x5A. The next two bytes are the source ID and the destination ID. Bytes 4 through 61 hold the message data. The last two bytes are 0x59 and 0x42. The tables below show only the message data part. The prefix, suffix, source, and destination bytes are the same for every member of the class and are not shown.

### Subsystem ID

| Member | ID Character | Hex |
|--------|--------------|-----|
| Vedaa (Audio Subsystem, me) | V | 0x56 |
| Khalid | K | 0x4B |
| Armando | A | 0x41 |
| Matthew | M | 0x4D |
| Manny | C | 0x43 |
| Lia | L | 0x4C |
| Broadcast (everyone) | X | 0x58 |

### Messages Sent by the Audio Subsystem

#### Message Type 6: Microphone Audio Feed Stream Information

This message is sent out to everyone on the team about twice per second. It tells the rest of the team if the microphone is currently picking up sound louder than the noise gate. The message is 7 bytes long. The first byte is the message type, which is always 6. The next 6 bytes are ASCII characters that spell either MIC_ON when sound is detected or MICOFF when the room is quiet.

| | Byte 1 | Byte 2 | Byte 3 | Byte 4 | Byte 5 | Byte 6 | Byte 7 |
|---|--------|--------|--------|--------|--------|--------|--------|
| Variable Name | message_type | mic_state[0] | mic_state[1] | mic_state[2] | mic_state[3] | mic_state[4] | mic_state[5] |
| Variable Type | uint8_t | char | char | char | char | char | char |
| Min Value | 6 | M (0x4D) | I (0x49) | C (0x43) | _ (0x5F) | O (0x4F) | F (0x46) |
| Max Value | 6 | M (0x4D) | I (0x49) | C (0x43) | _ (0x5F) | O (0x4F) | N (0x4E) |
| Example (sound) | 6 | M | I | C | _ | O | N |
| Example (quiet) | 6 | M | I | C | O | F | F |

Source: V (Audio Subsystem)
Destination: X (Broadcast)
Full frame example when sound is detected: A Z V X 6 M I C _ O N Y B

The team's original message page lists Type 6 as a 16 bit number in bytes 3 and 4. The audio subsystem ended up sending a short text string instead, since sending raw audio over UART is too slow to be useful for a quick status check. The team page should be updated so the other subsystems read this message correctly.

### Messages Received and Acted On by the Audio Subsystem

The audio subsystem does not currently use any directed messages from other team members. Any message addressed to V is acknowledged but does not change the subsystem's behavior.

If a future version adds remote control of microphone gain or a way to turn the mic on and off, the slot below is reserved for that purpose.

#### Message Type 8 (Reserved for future use): Microphone Control

| | Byte 1 | Byte 2 |
|---|--------|--------|
| Variable Name | message_type | mic_command |
| Variable Type | uint8_t | uint8_t |
| Min Value | 8 | 0 |
| Max Value | 8 | 2 |
| Example (turn off) | 8 | 0 |
| Example (turn on) | 8 | 1 |
| Example (calibrate) | 8 | 2 |

This message type has not been assigned by the team yet. It is shown here only to plan for future use.

### Messages Forwarded by the Audio Subsystem

The audio subsystem is part of the daisy chained UART network. It passes along any message whose destination byte is not V and whose source byte is not V. The forwarding logic does not look at the message data. The following message types are expected to pass through.

| Type | Description | Origin | Forwarded |
|------|-------------|--------|-----------|
| 1 | Arm position X, Y, Z (request) | HMI or Webuser | Yes |
| 2 | Arm position X, Y, Z (from Armando) | Armando | Yes |
| 3 | Speed and movement type (request) | HMI or Webuser | Yes |
| 4 | Speed and movement type (from Khalid) | Khalid | Yes |
| 5 | Video feed stream information | Manny or Lia | Yes |
| 7 | Speaker audio sound effect number (stretch) | HMI or Webuser | Yes |

The audio subsystem will also forward Type 6 messages that it did not send itself, in case a second mic is ever added to the system.

### Error Handling

The receiver follows these rules.

1. If byte 2 is not one of V, K, A, M, C, L, or X, the message is thrown away because the source is not a known team member.
2. If byte 2 is V, the message came from this subsystem and traveled all the way around the chain. The message is thrown away so it does not loop forever.
3. If a frame is longer than the 64 byte buffer, it is thrown away. Bytes that arrive without a valid 0x41 0x5A prefix are also thrown away.
4. If bytes 62 and 63 are not 0x59 and 0x42, the frame is malformed and is thrown away.

The error case shown for the demo is rule 2. A message that comes back to the audio subsystem with V as its source is dropped, which keeps the daisy chain from forwarding the same frame in a circle.

### Acknowledgement Behavior

When a valid frame addressed to V is received, the firmware does the following.

1. Reads the message_type byte to figure out what kind of message it is.
2. Updates the matching subsystem state if that type is implemented. None of the currently implemented types are addressed to V, so this step does not run today.
3. Blinks the status LED on RC4 to show that a valid frame meant for V was received.
4. Adds one to the internal counter g_word_count so the message count can be checked in the debugger.

When a frame is forwarded instead of processed, the LED does not blink. The frame is simply sent on to the next member of the daisy chain.

### Source Code

The full firmware is packaged as an MPLAB X project.

[Download AUDIOSUB.zip](AUDIOSUB.zip)

The message handling code lives in main.c. The important pieces are listed below.

uart1_setup sets up the UART1 pins on RC6 and RC7 at the team's standard baud rate.

uart_rx_fwd reads bytes from the UART receive register and pushes them into a small ring buffer for forwarding.

uart_poll is a non blocking transmit routine. It sends bytes from the forwarding buffer first and then sends any frames the audio subsystem generated on its own.

uart_queue builds a Type 6 frame with either MIC_ON or MICOFF whenever the noise gate state changes.

uart_loopback_test runs once at startup. It sends and reads back 12 bytes locally to confirm the UART hardware works before the main loop starts.
