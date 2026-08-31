
Bike Auxiliary System

To power a buzzer, head lights, and tail lights with one (or more) rechargeable Li-ion battery.
 
<img width="1200" height="900" alt="bike aux" src="https://github.com/user-attachments/assets/766c6112-1625-423f-9183-f2460c320e6b" />
Diagram 1: Rough image visualization of how the system will behave
Where the buzzer will act as a bell which will be activated by a button. The head lights and tail lights will be activated with one switch.

--Overall design Considerations--
- as small as possible
- minimize the number of re-order PCBs due to non-design related issues

(All blue texts are links to component datasheet)
Buzzer Sub-system

Problem: My standard bike bell cannot be rung with gloves in the winter
Solution: Make a 'bell' that can be activated with a button

Buzzer used: AI-3035-TWT-3V-R
- resonance frequency (3500+-500Hz) and sound pressure level (100dB at 10cm) is similar to that of a typical bike bell
- continuous tone, more control from the user
- through hole, easier to test and solder

To achieve the desired 4000Hz, LTC6990 is used to set the output frequency (div code = 7). The LTC6990 online tool is used to help determine the value of resistance needed to set the frequency to 4kHz

The plan: First simulate the design in LTspice to ensure the resistance setting will achieve 4kHz, then design the PCB board with Altium Designer

Image 1: LTC6990 being simulated in LTspice, with the output frequency of 4kHz at a 3V output when the input is a 3VDC supply

After confirming the design will achieve 4kHz output, I used Altium Designer to design the schematic layout, then designed the PCB layout for the board

Image 2: Buzzer board schematic with the resistance value simulated in LTspice and 3V3 input and output both with B2B-XH-A 2-pin headers

Image 3: Buzzer board PCB layout viewed from top layer with components from schematic and I/O ports labeled

--Specific Design Considerations--
- N/A

Head Light

Headlights requirements:
- White
- >600lm
- <3A current to avoid overheating
According to the datasheet, XMLBWT-00-0000-0000U3051 (P.4) can achieve 628 lumens at 1.5A and 798 lumens at 2A (XMLBWT-00-0000-0000U4051 is brighter, but it is only sold in 1000s on digikey)
Therefore XMLBWT-00-0000-0000U3051 is chosen to be the headlights
LED board coming soon...

Tail Light

Coming soon...
Power Distribution Sub-system

Problem: Li-ion Batteries varies from 2.8V - 4.2V and the LEDs need constant current
Solution: Supply constant voltage and constant current via DC-DC converters and LED drivers

More updates coming soon... 
