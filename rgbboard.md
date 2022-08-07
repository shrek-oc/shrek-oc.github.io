# ESP32-Based Addressable RGB controller board
### My First Attempt at a Controller Designed For Desktop PCs

## Background

As the corporate world moves from desktop to laptop, and the world at large moves from desktop to mobile,
desktop PCs are finding a niche as gaming devices. With this, PC components increasingly include features
designed to appeal to gamers. Most current motherboards come with built-in RGB LED controllers and bundle
software allowing for customization of the effects.

When I recently became interested again in building desktop PCs, I was surprised by how popular it had become,
and how much the scene had changed. When I started researching to bring myself up to speed, it seemed everyone's
build had RGB lighting. There were many components available, including fans, adhesive strips, RAM, video cards  and
even SSD's with built-in RGB LEDs. It seemed like a gimmick at the time, but the case I ended up buying happened to include
two RGB fans, and I figured I might as well get my money's worth and use the lighting. I remember being disappointed
in the lack of customization offered by my motherboard's included software, but I settled on an effect I liked and
left it a that.

Fast forward a few months and I found out about the Arduino framework and [FastLED](https://fastled.io) library, which
allows to write your own C++ code to control addressable RGB LEDs. It, along with other LED libraries, had been around
for years before such lighting was ever included in PC components. Owing to the popularity of [Adafruit's](https://adafruit.com)
Neopixel-branded products in the maker community, WS2812B has become the most popular addressable LED chipset and is the de
facto standard for addressable RGB in PCs. Although PC RGB seems like a mess, with many manufacturers using different connector
types, these differences are largely superficial. No matter the connector type, most devices are electronically compatible,
and only need an adapter. Every WS2812B device has 3 leads. One connects a 5V power source, another is for the data signal,
and the third connects to ground. Figure 1 shows the connector type used on most motherboards and many devices.

## Designing a Custom Board

Projects using Arduino and largely compatible microcontrollers for lighting control are easy to find, but there are very few
geared towards PC components. While It's possible to use these projects with your PC, they are very awkward. WS2812 LEDs need
a 5V power supply and a controller board. It's generally expected that the LED strips come without a connector or even leads.
You are expected solder leads to the strips and connect the data and ground leads to your controller and the 5V and another
ground lead to your power supply. It would be easier if you could just use the same type of connector as your motherboard
It makes no sense to use a separate power supply when every desktop PC has its own power supply with a regulated 5V rail. Once
you have everything connected, how do you mount and protect your components? A PC chassis is designed to hold PC components.
To mount other things, you'd have to drill you own holes or use double-sided tape. Then you have to make sure your board is
properly insulated.

The obvious solution was a custom PCB, but I had no idea how to design a PCB. Watching [Electronoobs](https://www.youtube.com/c/ELECTRONOOBS)
I found EasyEDA, a CAD utility which is offered for free by JLCPCB and has integration with their component library. Hearing
electrical engineering terms like specific impedence and dielectric contstant was a little scary. Watching [Robert Feranec](https://www.youtube.com/channel/UCJQkHVpk3A8bgDmPlJlOJOA),
reassured me these things are not a concern for the short traces and sub 1MHz singals used in this application. I decided on signal trace
width just by making them wider than necessary. I used Advanced Circuits' [trace width calculator](https://www.4pcb.com/trace-width-calculator.html)
to calculate the width of the power traces. Two layers with most of the action happening on the top would be appropriate
for a simple circuit like this and easy enough for a noob like me to deal with.

ESP32 was clearly the best choice for a microcontroller, offering a 240MHz CPU, 512K of RAM and 4MB of flash memory for less money
than even the cheapest and slowest Arduino. It offers 18 useable data pins and is fast enough to address them simultaneously. I chose the DOIT
DEVKIT V1 because it's the most common and least expensive ESP32 development board layout and it provides access to all of the
useable IO pins. Programming and power happens through the USB port.

The first thing the board needed was a way to mount to a PC chassis without modification. I figured I'd design the board to
mount in place of a 2.5" SSD, since every PC case has mounts for them. To do this, I consulted the [datasheet](https://www.intel.com/content/dam/support/us/en/documents/ssdc/hpssd/sb/Intel_SSD_320_Series_Product_specification.pdf)
from intel's SSD 320 series to get the hole spacing and device dimensions. I designed the mounting holes on the board to line up with the
bottom mounting holes on the SSD and the board to have the same overall outline. 4mm M3 standoffs prevent contact with the chassis and allow for mounting with the same screws.
If your case mounts 2.5" drives using the side rather than the bottom holes or the board is too thick, a commonly available 2.5 to
3.5" adapter can be used. 

In order to power the board with the PC power supply, I used the 4-pin floppy (or "Berg") connector. It supplies 5 volts, and
its small size makes it an ideal choice for a space-constrained board. For maximum compatibility, I put 4 motherboard-style ARGB
connectors on the left edge of the board. Most PC RGB devices either use these connectors or provide an adapter for them. I wanted
to support then Lian-Li Strimer plus series as well. These are unusual for PC RGB devices because while they use WS2812 compatible
LED's, they use 4 or 6 data lines per device, rather than the usual single data line, making full control of them impossible from
a typical header. Finally, I added a 2-pin header connected to one of the ESP32's input-only pins. This is to allow a button, such
as a PC's seldom-used reset button to control any aspect of the program running on the board.  

