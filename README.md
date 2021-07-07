# USB Atmel in-circuit programmer for AVR controllers #

This is yet another version of Thomas Fischl's [USBasp](https://www.fischl.de/usbasp/) device, an USB in-circuit programmer for Atmel AVR controllers.

The idea to put the USBasp into a nice little enclosure from [Fischer Elektronik](https://www.fischerelektronik.de) (Enclosure Model: FR 55 25 80 ME) I got from [Ulrich Radig's](https://www.ulrichradig.de/home/index.php/avr/usb-avr-prog) website.

What I really missed were some buttons to quickly select/deselect the two very helpful features "Slow Speed" and "Powering Target" if needed. Further I missed a small 6-pin box header with 1.27mm pitch for connections to small targets that don't have enough space on their PCB to fit a big 10-pin/6-pin box header with 2.54mm pitch (20.3mm x 8.9mm resp. 15.22mm x 8.9mm in size).

Therefore I integrated those little goodies and that's the result as shown below.

### Front side with 2 Buttons and USB Socket: ###
  
![github](https://github.com/yellobyte/USB-Atmel-In-Circuit-Programmer/raw/main/Doc/USBaspPic1.jpg)
  
### Rear side with box headers (1.27mm/2.54mm pitch) and LEDs: ###
  
![github](https://github.com/yellobyte/USB-Atmel-In-Circuit-Programmer/raw/main/Doc/USBaspPic4.jpg)
  
### Flashing a target via small box header: ###
  
![github](https://github.com/yellobyte/USB-Atmel-In-Circuit-Programmer/raw/main/Doc/FlashingUsingSmallHeader.jpg)
  
## Some technical infos ##

The PCB is very similar to the one Ulrich Radig created for the same enclosure. As mentioned before, I additionally added 2 subminiature push buttons and a small Samtec 6-pin box header with 1.27mm pitch.

The software on the Atmega8 is original USBasp code from Thomas Fischl.

Button S2 is for powering the target. The 5V power supply to the target is fused with a 0.2A self recovering fuse. If the target needs more current it must be powered seperately.
  
![github](https://github.com/yellobyte/USB-Atmel-In-Circuit-Programmer/raw/main/Doc/PCB-Top.jpg)
  
Button S1 is for selecting a slow target programming speed. This is very helpful if one tries to flash new AVR controller chips for the very first time. Some AVR controllers, when delivered, use by default an internal clock generator running on a low frequency and therefore won't answer when addressed by an USBasp with normal programming speed, because it is simply too fast for them. That can be a real trap for AVR novices...

Jumper JP1 is only (temporarily) shorted when the Atmega8 in the programmer must be programmed itself. It should be open by default.

The 2 subminiature push buttons (Manufacturer "NKK Switches", Model "BB16AH-FA") and the little box header with 1.27mm pitch (Manufacturer Samtec, Model "SHF-103-01-L-D-TH") are normally available from Mouser/Farnell/etc.

## PCB Files ##

You find the schematic, board pictures and the Gerber production files (rev 1.1) in folder [**PCB**](https://github.com/yellobyte/USB-Atmel-In-Circuit-Programmer/blob/main/PCB). I zipped all necessary Gerber files into file "USBasp Gerber Files Rev1.1.zip".  

All you need is to send the zip file to a decent PCB manufacturer, pay and wait for the PCB(s) to arrive at your doorstep.

Remark:  
The pictures in folder [**Doc**](https://github.com/yellobyte/USB-Atmel-In-Circuit-Programmer/blob/main/Doc) still show board revision 1.0.  Revision 1.1 is almost identical and contains only cosmetic changes.
   
