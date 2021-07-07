# USB Atmel in-circuit programmer for AVR controllers #

A light (or color) organ is an electronic device which converts an audio signal into rhythmic light effects. Invented in the 70's they are still popular in discotheques and party rooms though.
Now that **LED Strips** became fairly cheap they can replace normal light bulbs used in the earlier years and make the color organ look more modern.

<img src="Doc/SequenceNormalMode.jpg">

The electronic circuit of a normal light organ separates the audio signal into several frequency bands and dimms the light bulbs or LEDs according to the average level of each band.

For a decent visual impression at least 3 bands (channels) are needed: low frequencies (bass), middle frequencies and high frequencies (treble). For best visual results each channel should control a light source of different color, e.g bass = red, middle = yellow and treble = green.

## Technical Infos ##

We have 3 channels and therefore control 3 LED Strips. Their specified working voltage must be 12V DC. The maximum length of the LED Strips the device can handle is mainly determined by the current consumption (amperes per meter length) of each LED Strip and the maximum current rating of the external 12V power supply used.

To give you a real world example: The external power supply I tested the device with was a MeanWell GS90A12 with a maximum current rating of 6.7A DC. Therefore I set the overcurrent limit to 2.2A per channel (the device itself doesn't need much). That limit allowed me to use LED Strips of 5m length with 60 LEDs (Type 3528 SMD in red, yellow or green) per meter. The overcurrent protection circuitry never got triggered.

The light organ has 3 working modes: [Normal](https://github.com/yellobyte/LED-Strip-Light-Organ/blob/main/Doc/NormalMode.mp4), [Rhythm](https://github.com/yellobyte/LED-Strip-Light-Organ/blob/main/Doc/RhythmMode.mp4) and [Cyclic](https://github.com/yellobyte/LED-Strip-Light-Organ/blob/main/Doc/Cyclic.mp4), which can be selected by pressing the **mode selection** button. 

An Arduino Nano 328P continuously samples all 3 filter outputs und transforms their voltage levels into digital PWM signals which feed the output stages with their IRF540N Power MOSFETs. Overtemperature & overcurrent protection (overload OL protection) has been integrated. 

The electrical circuit had to be devided into 2 separate [PCBs](https://github.com/yellobyte/LED-Strip-Light-Organ/blob/main/Doc/BoardTop.JPG), a **Filter-PCB** and a **Power-PCB** as I only call the basic version of the Eagle Design Tool my own and PCB size is limited to Eurocard size. But this proved to be very fortunate in the end for I redesigned the output stages a few times but didn't have to redo the analog stages. Please have a look at folder [**EagleFiles**](https://github.com/yellobyte/LED-Strip-Light-Organ/blob/main/EagleFiles) for schematic & PCB details.

The block diagram of the light organ is as follows:

<img src="Doc/BlockDiagram.jpg">
   
### The Filter-PCB: ###

The MC33078 Op amps in the pre-amplifier and AGC stages proved perfect for the task. They might be substituted with cheaper Op amps, e.g. TL082, TLC277 or MC1458 but this comes with a loss of signal quality (distortion!). Especially the MC1458 wasn't able to pull the signal fully down to the lower rail and the TLC277 showed some crossover distortion.

The cheap LM358 Op amps used in the 3 filter stages generate crossover distortion as well but this is of no consequence in that particular application.

The AGC stage is simple but very effective and produces hardly any distortion even with high input signals applied. This stage is needed in order to normalize the signal level fed into the filter stages as the signal level from the many audio sources can vary by an order of magnitude (MP3/CD/Vinyl player, FM or internet radio, etc.).

Each of the 3 filter circuits uses an Op amp and some passive components (Rs/Cs). The resistors with strange values (6k16, 14k7, 16k9, 23k7, 71k5) belong to the E48 (2%) or E96 (1%) series. The filter circuits (2nd order) were calculated with the help of **FilterLab** from Microchip. It's available at no cost, easy to use and very intuitive. 

LED1 to LED6 have been added mainly for testing purposes. They come handy when you don't have any LED-Strips available but still want to do some tests on the device. They too are PWM controlled and show the exact same visual effects as the LED-Strips would.

<img src="EagleFiles/Filter-PCB/Schematic.JPG">
     
### The Power-PCB: ###

The Arduino Nano 328P module is an ideal device for sampling the three filter outputs and generating PWM signals needed by the power stages. It only runs on 16MHz but this proved fast enough for the task.

The code running on the Arduino Nano is available in folder [Software](https://github.com/yellobyte/LED-Strip-Light-Organ/blob/main/Software). It was generated with VSCode/PlatformIO.

The Power MOSFETs IRF540N work as High-Side Switches (in "common drain" configuration) and need a gate voltage higher than the Nano can provide in this application, thats why 12V DC-DC converters (I/O isolated -> output side floating) had to be used. The reason is that the source voltage of the MOSFETs approaches the +12V supply voltage when switched on. To keep the MOSFETs turned on the IR2125 outputs must drive the MOSFETs gate voltage 10-12V higher than the supply voltage.

Shunt resistors (R6/R12/R18 - 0.15Ohm/2W/5%/Wire) sit between the MOSFETs and the output sockets in order to measure the output current and utilize the current sense input of the MOSFET drivers IR2125. When the IR2125 detects overcurrent it shuts down immediately until the next PWM impuls. The software in the Arduino Nano detects this condition and shuts down the affected channel even longer (5 sec) before releasing it again. No harm is done at all in case you have a permanent short on the output lines, provided you properly calibrated the trigger point of course!

The power stages by design could deliver 3-4 amperes per channel without problems. In practice the limiting factor probably will be the external 12V DC power supply. The driver MOSFETs IRF540N (Idmax > 20A) operate way from their absolute maximum ratings and only get handwarm in the enclosure (Model EXN-23361-SV from Bud Industries).

A temperature sensor LM35DZ connected to an Arduino Nano analog input pin is used to measure the temperature inside the enclosure. Temperatures over 50° celsius will shut down all channels and only temperatures below 40° celsius will release them. However, during my hour long tests the temperature overload never got triggered for the temperature inside the enclosure always hovered around 35-38° celsius.

JP1/JP2/JP3 are only needed for testing purposes, in normal working mode pins 1 & 2 should be shorted in order to allow the overload signal from the IR2125 ICs reach the Arduino Nano. R2/SV3 are only used for testing purposes as well.

Push button S1 is used for selecting the working mode.

The three TVS Diodes P6KE20A (D5/D9/D13) in parallel with the output sockets have a very fast response time and protect the device from possibly harmful voltage transients that might travel in from connected LED Strips. The latter act like big antennas when a few meters long!

D1 provides a very stable reference voltage needed by the A/D converters in the Arduino Nano.

<img src="EagleFiles/Power-PCB/Schematic.JPG">
   
### The two [boards](https://github.com/yellobyte/LED-Strip-Light-Organ/blob/main/Doc/BoardTop.JPG) are connected as follows: ###

```c
 Filter-PCB ---  Power-PCB
      LSP7  ---  LSP9  (analog audio)
      LSP8  ---  LSP2  (+12V=)
      A-IN  ---  A-OUT (PWM channel A)
      B-IN  ---  B-OUT (PWM channel B)
      C-IN  ---  C-OUT (PWM channel C)
      A-OUT ---  A-IN  (filter output channel A)
      B-OUT ---  B-IN  (filter output channel B)
      C-OUT ---  C-IN  (filter output channel C)  
      GNDs  ---  GNDs   
```        

## How to calibrate the circuit ##

### Filter-PCB: ###
The pre-amplifier stage, the automatic gain control (AGC) stage and all three filter circuits have to be calibrated in order to produce optimal results. The procedure itself is fairly easy. Only obstacle is you need a function generator and ideally an oscilloscope. A cheap multimeter will do if you can´t get hold of such equipment but might spoil optimal performance of the device.

Necessary steps in correct order:

1) Adjust the trimpot R8 so that pins 1 & 2 of R8 are shorted (AGC is inactive that way). Apply a signal of 1kHz/0.5Vpp to X5 **or** X6 on the power board (one of the two Cinch/RCA sockets). It gets forwarded to LSP7 on the filter board (inverting input of pre-amplifier Op amp IC1A). 

2) Choose a value of R12 so that the signal level at LSP2 (output of Op amp IC1B) is close to 2.70Vpp. In my case I ended up with a R12 of 470k.

3) Increase the amplitude of the input signal to 2.0Vpp. Now adjust the trimpot R8 so that the signal level at LSP2 is close to 3.2Vpp.

At this point the pre-amplifier & AGC stages are properly calibrated. The filter stages follow:

4) Change the input signal to 50Hz/0.7Vpp and adjust R17 so that you get 3.6Vpp at LSP4.

5) Change the input signal to 1.3kHz/0.7Vpp and adjust R22 so that you get 3.6Vpp at LSP5.

6) Change the input signal to 6kHz/0.7Vpp and adjust R30 so that you get 3.6Vpp at LSP6.

That's it. The analog circuit on the **Filter-PCB** is now fully calibrated.

### Power-PCB: ###
Only the current limits of each channel (A/B/C) need to be calibrated. That's a bit tricky as you need a current running through the MOSFETs that has exactly the value you want the IR2125 to trigger. I used a cheap electronic load from Aliexpress (XY-FZ35) to accomplish that. 

Before you start make sure the Arduino Nano is REMOVED from the Power-PCB, your external power supply is NOT attached to the 12V power socket and there are NO jumpers on JP1/JP2/JP3 !

The following steps have to be done for each channel A/B/C, starting with channel A:

7) Adjust the trimpot R7 (R13/R19) so that U1 (U2/U3) pins 5 & 6 (CS & VS of IR2125) are shorted
8) Attach a voltmeter to LSP12 (LSP22/LSP32) and Ground
9) Attach the electronic load to the channel output socket (the socket for the LED-strip) and set the current limit to the desired trigger value 
10) Connect A-OUT (B/C-OUT) (IR2125 pin 2) with LSP2 (+12V=). That will switch the MOSFET on at the next step.
11) Now connect the external 12V power supply, the electronic load should see the programmed current flow
12) Slowly turn the screw on trimpot R7 (R13/R19) until the voltage on LSP12 (LSP22/LSP32) jumps from 0 to 4.7V. Stop turning immediately!
13) Remove the external 12V power supply and the electronic load 
14) Put the jumper back on pins 1 & 2 of JP1 (JP2/JP3) and remove the connection between LSP2 and A-OUT (B/C-OUT)
15) proceed with the next channel

Having all channels calibrated you can put the Arduino Nano back onto the board.
Those settings properly done for all channels, even a permanent short on the output sockets won't destroy your output stages.
