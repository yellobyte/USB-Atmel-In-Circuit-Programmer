# USB Atmel in-circuit programmer for AVR controllers #

This is another version of Thomas Fischl's [USBasp](https://www.fischl.de/usbasp/) device, an USB in-circuit programmer for Atmel AVR controllers.

The idea to put the USBasp into a nice little enclosure from [Fischer Elektronik](https://www.fischerelektronik.de) (Enclosure Model: FR 55 25 80 ME) I got from [Ulrich Radig's](https://www.ulrichradig.de/home/index.php/avr/usb-avr-prog) website.

What I missed were some buttons to select "Slow Speed" and "Powering Target". Further I missed a small box header with 1.27mm pitch for connections to small targets that don't have enough space on their PCB to fit a standard sized 10-pin/6-pin box header with 2.54mm pitch.

### Front Side with Switches and USB Socket ###
  
![github](https://github.com/yellobyte/USB-Atmel-In-Circuit-Programmer/raw/main/Doc/USBaspPic1.jpg)
  
### Rear Side with Sockets and LEDs ###
  
![github](https://github.com/yellobyte/USB-Atmel-In-Circuit-Programmer/raw/main/Doc/USBaspPic4.jpg)
  
## Technical Infos ##

The PCB is very similar to the one Ulrich Radig created for the same enclosure. I additionally included 2 subminiature push buttons and a small Samtec 6-pin box header with 1.27mm pitch.

Button S2 is for powering the target. The 5V power supply to the target is fused with a 0.2A self recovering fuse.
  
![github](https://github.com/yellobyte/USB-Atmel-In-Circuit-Programmer/raw/main/Doc/PCB-Top.jpg)
  
The other button S1 is for selecting a slow target programming speed. This is very helpful if one tries to flash new chips for the very first time. Some chips, when delivered, use an internal 1MHz clock generator by default and therefore won't answer when the normal USBasp programming speed is used, because it is simply too fast for them. That can be a real trap for AVR novices...

Jumper JP1 is only used for programming the Atmega8 in the programmer itself. It should be open by default.

The 2 subminiature push buttons (Manufacturer "NKK Switches", Model "BB16AH-FA") and the Samtec header with 1.27mm pitch, Model "SHF-103-01-L-D-TH" I purchased from Mouser.

## PCB Files ##

You find the schematic, board pictures and the Gerber production files (rev 1.1) in folder [**PCB**](https://github.com/yellobyte/USB-Atmel-In-Circuit-Programmer/blob/main/PCB)

The pictures in folder [**Doc**](https://github.com/yellobyte/USB-Atmel-In-Circuit-Programmer/blob/main/Doc) still show board revision 1.0. The Gerber files in rev 1.1 contain only minor improvements.
   
