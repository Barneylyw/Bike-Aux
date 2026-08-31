
# Bike Auxiliary System

To power a buzzer, head lights, and tail lights with one (or more) rechargeable Li-ion battery.
 
<p align="center"><img width="50%" alt="bike aux" src="https://github.com/user-attachments/assets/766c6112-1625-423f-9183-f2460c320e6b" /></p>
Diagram 1: Rough image visualization of how the system will behave
Where the buzzer will act as a bell which will be activated by a button. The head lights and tail lights will be activated with one switch.

--Overall design Considerations--
- as small as possible
- minimize the number of re-order PCBs due to non-design related issues

(All blue texts are links to component datasheet)

## Buzzer Sub-system

Problem: My standard bike bell cannot be rung with gloves in the winter
Solution: Make a 'bell' that can be activated with a button

Buzzer used: [AI-3035-TWT-3V-R](https://puiaudio.com/file/specs-AI-3035-TWT-3V-R.pdf)
- resonance frequency (3500+-500Hz) and sound pressure level (100dB at 10cm) is similar to that of a typical bike bell
- continuous tone, more control from the user
- through hole, easier to test and solder

To achieve the desired 4000Hz, [LTC6990](https://www.analog.com/media/en/technical-documentation/data-sheets/ltc6990.pdf) is used to set the output frequency (div code = 7). The [LTC6990 online tool](https://beta-tools.analog.com/timerblox/LTC6990#) is used to help determine the value of resistance needed to set the frequency to 4kHz

The plan: First simulate the design in LTspice to ensure the resistance setting will achieve 4kHz, then design the PCB board with Altium Designer

<p align="center"><img width="50%" alt="6990 simulated" src="https://github.com/user-attachments/assets/b942a11c-4cdb-4f6a-8a77-31d2f1305d7f" /></p>

Image 1: LTC6990 being simulated in LTspice, with the output frequency of 4kHz at a 3V output when the input is a 3VDC supply

After confirming the design will achieve 4kHz output, I used Altium Designer to design the schematic layout, then designed the PCB layout for the board

<p align="center"><img width="50%" alt="Buzzer Board schematic" src="https://github.com/user-attachments/assets/7174550f-72d9-43bc-8322-e1a79f293f98" /></p>

Image 2: Buzzer board schematic with the resistance value simulated in LTspice and 3V3 input and output both with B2B-XH-A 2-pin headers

<p align="center"><img width="50%" alt="Buzzer Board" src="https://github.com/user-attachments/assets/671e69bc-77f5-41c9-bdb6-9b08769c611d" /></p>

Image 3: Buzzer board PCB layout viewed from top layer with components from schematic and I/O ports labeled

--Specific Design Considerations--
- N/A

## Head Light

Headlights requirements:
- White
- greater than 600lm
- <3A current to avoid overheating
According to the datasheet, [XMLBWT-00-0000-0000U3051](https://downloads.cree-led.com/files/ds/x/XLamp-XML2.pdf) (P.4) can achieve 628 lumens at 1.5A and 798 lumens at 2A (XMLBWT-00-0000-0000U4051 is brighter, but it is only sold in 1000s on Digi-Key)
Therefore, XMLBWT-00-0000-0000U3051 is chosen to be the headlights
### Front LED Board
--Specific Design Considerations--
- heat dissipation via vias on the GND pad of the LED
- aluminum PCB to maximize heat dissipation (as I am writing this, I realized I don't need heat dissipation vias as aluminum PCBs only have 1 layer, but I already ordered it)
- GND plated through holes so that it can be connected to the bike frame
<p align="center"><img width="50%" alt="Front LED board" src="https://github.com/user-attachments/assets/3b620ce6-f5ff-4b15-9047-c43bc5c4bf55" /></p>
Image 4: Front LED board layout with design considerations in mind

## Tail Light

Coming soon...
Power Distribution Sub-system

Problem: Li-ion Batteries varies from 2.8V - 4.2V and the LEDs need constant current
Solution: Supply constant voltage and constant current via DC-DC converters and LED drivers

More updates coming soon... 
