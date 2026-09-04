
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

## Headlight

Headlight requirements:
- White
- greater than 600lm (similar to an off-the-sheleve part)
- <3A current to avoid overheating
According to the datasheet, [XMLBWT-00-0000-0000U3051](https://downloads.cree-led.com/files/ds/x/XLamp-XML2.pdf) (P.4) can achieve 628 lumens at 1.5A and 798 lumens at 2A (XMLBWT-00-0000-0000U4051 is brighter, but it is only sold in 1000s on Digi-Key)
Therefore, XMLBWT-00-0000-0000U3051 is chosen to be the headlights
### Headlight Board
--Specific Design Considerations--
- heat dissipation via vias on the GND pad of the LED
- aluminum PCB to maximize heat dissipation (as I am writing this, I realized I don't need heat dissipation vias as aluminum PCBs only have 1 layer, but I already ordered it)
- GND plated through holes so that it can be connected to the bike frame
- power port
<p align="center"><img width="50%" alt="Front LED board" src="https://github.com/user-attachments/assets/3b620ce6-f5ff-4b15-9047-c43bc5c4bf55" /></p>
Image 4: Headlight board layout with design considerations in mind

## Taillight

Taillight requirements:
- Red
- 100lm (similar to an off-the-sheleve part)

I chose [JE2835AHR-N-0001A0000-N0000001](https://downloads.cree-led.com/files/ds/j/JSeries-2835-Color.pdf), thinking it was a red light with 130lm, but I didn't notice it refers to radiant flux instead of luminous flux.
This will not work as intended with my current setup; I will integrate and test everything before changing the taillight setup.
### Taillight Board
--Specific Design Considerations--
- power port
- mounting holes
<p align="center"><img width="50%" alt="Rear LED board" src="https://github.com/user-attachments/assets/a719d024-1bb3-4ebb-bb28-f6b6378dc71d" /></p>
Image 5: Taillight board layout with a power port and mounting holes

## BMS (used in both Power Distribution Board and Charger Board)
BMS requirements:
- OVP: 4.2V
- UVP: 2.8V
[BQ297xx](https://www.ti.com/lit/ds/symlink/bq2972.pdf) family is chosen for its small BOM size and low shutdown current, I chose BQ29737 because it is the closest one that matches my requirements
following their design procedure with max load discharge current = 5A (headroom from 4A) 20mΩ is needed across both FET and V_gs=3.5, so CSD16406Q3 is likely going to work as the charging and discharging FETs

following the Typical Application from the datasheet, I created the schematic for BMS
<p align="center"><img width="50%" alt="image" src="https://github.com/user-attachments/assets/f174c445-50bc-4645-91af-d2046e05c874" /></p>
Image 6: schematic of BMS system with BQ29737DSER as the BMS IC and CSD16406Q3 as the external charging and discharging FETs as the typical application have instructed

## Power Distribution Board
This might be the most complex system in this project... many things can (and probably will) go wrong here...
<p align="center"><img width="50%" alt="PDB" src="https://github.com/user-attachments/assets/2775d804-fbe9-40b7-a745-857b54bb4ca7" /></p>
Image 7: rough visualization of what the power distribution board consists of, blue arrows indicates power input/output ports

### Voltage Regulator
Voltage regulator requirement:
- be able to supply 5V with 4A max
I input my requirements into TI's Webench tool and it returned TPS61089 with this configuration is able to reach 5V 4A max
<p align="center"><img width="50%" alt="TPS61089RNRR 5V4A" src="https://github.com/user-attachments/assets/eb53ff0f-be8e-4a7d-8cbf-de164c09f408" /></p>
Image 8: how TPS61089 should be configured according to Webench

I then copied it to my schematic document in Altium
<p align="center"><img width="50%" alt="VR in Altium" src="https://github.com/user-attachments/assets/88727eeb-1d1d-4f12-ae2c-954ea3767128" />
</p>
image 9: my schematic of TPS61089 without some of the input capacitors

### Front LED (Headlight) driver
Front LED driver requirements:
- be able to supply 2A, 3.5V(V_fled)
- easy to use
[LED2000](https://www.st.com/content/ccc/resource/technical/document/datasheet/0f/92/f3/d6/fb/2e/49/ef/DM00060040.pdf/files/DM00060040.pdf/jcr:content/translations/en.DM00060040.pdf) is able to supply 2A and 3.5V with this configuration according ST's eDesignSuite, so I copied it to my schematic document in Altium
<p align="center"><img width="50%" alt="Front driver config according to ST" src="https://github.com/user-attachments/assets/99b18fa2-6722-4579-9160-2bd0162dc3ef" /></p>
Image 10: how LED2000 should be configured according to eDesignSuite
<p align="center"><img width="50%" alt="Front driver in Altium" src="https://github.com/user-attachments/assets/f54594e3-7554-4be3-9c47-6388247863ac" /></p>
image 11: my schematic of LED2000

### 1 Hz oscillator
The original plan was to either use a 555timer or dividing a crystal signal, but then i found the [SIT1534AC-J5-DCC-00.001E](https://www.digikey.ca/en/products/detail/sitime/SIT1534AC-J5-DCC-00-001E/7793956) can produce a 1 Hz signal, so I opted for the easy solution as this is not the focus of this project and I don't need the timer to be 100% accurate, just ~1 Hz

The footprint I downloaded from Ultra Librarian doesn't specificify which pin is which while the symbol does, so I guessed the layout, so it might not work as intended(i.e. the taillight won't blink)

### Rear LED (taillight) driver
Rear LED driver requirements:
- able to supply >250mA
- consist of an enable pin
- 5V input
- easy to use


I was debating between [NCP3066](https://mm.digikey.com/Volume0/opasdata/d220001/medias/docus/1149/NCP%2CNCV3066.pdf) and [Pam2804](https://www.diodes.com/assets/Datasheets/PAM2804.pdf)
<table>
  <tr>
    <th></th>
    <th>NCP3066</th>
    <th>PAM2804</th>
  </tr>
  <tr>
    <td>Max current supply</td>
    <td>1.5A</td>
    <td>1A</td>
  </tr>
  <tr>
    <td>External parts</td>
    <td>7</td>
    <td>4</td>
  </tr>
  <tr>
    <td>Input voltage</td>
    <td>0-42V</td>
    <td>2.5-6V</td>
  </tr>  
  <tr>
   <td>How to determine components</td>
   <td>9 equations</td>
   <td>I_led = 0.1/Rs</td>
  </tr>
</table>
Table 1: a table I made for myself to compare which one is the better option, both matches the requirements, but PAM2804 is easier to use, therefore PAM2804 is chosen as the rear led driver

To determine Rs, I used the provided equation : I_led = 0.25A = 0.1/Rs -> 0.4Ω and created the schematic accordingly
<p align="center"><img width="50%" alt="Rear LED driver in Altium" src="https://github.com/user-attachments/assets/dde34d7a-1c5f-4211-b2c2-166e8cd96fb7" /></p>
Image 12: schematic of the rear led driver with R9 = 0.4Ω to set the I_led = 0.25A

### Board layout
requirements for TPS61089:
- Minimize length and area connected to SW pin
- Capacitors should be as close to their respectful pins as possible
- Vout path should be as short as possible
- Current carrying paths should be wider
requirements for LED2000:
- FB connections be far from high current paths
- FB -> R_sense path be as short as possible
- 2 GND plans connected at Cout (C6 in my schematic)
-  - Signal GND: AGND(Pin6), R3, C7
   - PWR GND: Cout(C6), Cin(C3), GND(Pin7), PWR GND(Pin2)
Personal requirements:
- section by section so that it's easier to separate parts
- test points (at Vin,Vout,CLK_out,I_fled,I_rled)
- raw 3.7V input port for testing
- smaller than 10cm x 5cm

to determine the trace width, I used the [digikey trace width calculator](https://www.digikey.ca/en/resources/conversion-calculators/conversion-calculator-pcb-trace-width) to determine the width at different traces while assuming 10C rise:
<table style="width:100%; border-collapse: collapse; border: 1px solid #ddd;">
  <tr style="background-color: #2196F3; color: white;">
    <th style="border: 1px solid #ddd; padding: 12px;">Input Current (A)</th>
    <th style="border: 1px solid #ddd; padding: 12px;">External Trace Width (mm)</th>
  </tr>
  <tr>
    <td style="border: 1px solid #ddd; padding: 12px;">6</td>
    <td style="border: 1px solid #ddd; padding: 12px;">3.55</td>
  </tr>
  <tr style="background-color: #f9f9f9;">
    <td style="border: 1px solid #ddd; padding: 12px;">4</td>
    <td style="border: 1px solid #ddd; padding: 12px;">2.03</td>
  </tr>
  <tr>
    <td style="border: 1px solid #ddd; padding: 12px;">2</td>
    <td style="border: 1px solid #ddd; padding: 12px;">0.78</td>
  </tr>
  <tr style="background-color: #f9f9f9;">
    <td style="border: 1px solid #ddd; padding: 12px;">0.25</td>
    <td style="border: 1px solid #ddd; padding: 12px;">0.0444</td>
  </tr>
</table>
table 2: how wide should the trace be depending on the current with temperature rise = 10C, only external width matter since I am only designing a 2 layer PCB
<p align="center"><img width="50%" alt="PDB" src="https://github.com/user-attachments/assets/a84dc339-ae24-42e3-88dc-4307fb76ea9f" /></p>
image 13: final design of the PDB with all the design requirements followed as closely as possible. includes 1 input port, 3 output port, 1 test input port, mounting holes, and words to indicate what is what.


## Charger
Board holders and mechanical integration coming soon... 
