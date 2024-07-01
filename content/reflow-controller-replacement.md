---
title: "Reflow controller replacement"
date: 2020-08-22T18:13:29Z
tags: ["reflow", "stm8s", "n76e003"]
draft: false
---
Years ago when I wanted to upgrade from the skillet method I bought myself a small inexpensive toaster oven and gutted the stock controls. Using a kl25z development board[^1] stuck to the side of the oven and a few ui components on a tiny breadboard stuck onto the front, I had a "good enough" reflow toaster. This version lasted me several years, until recently some issues surrounding the connections on the breadboard made it unreliable.

## Planning the rebuild
After searching online I came across a great site[^2] which has awesome instructions on how (and why) to build a more thermally efficient "toaster" reflow oven. So I started to gather the components (including ordering the same model of toaster oven as shown in the detailed assembly instructions), sourcing some of the components from overseas took several months. Like a lot of my projects that have a lot of time between planning and execution, it was placed on the shelf and ignored.

## Melting point
The project was awoken when I attempted to reflow a small test board in my original oven and it ended up turning the PCB into a pile of goo. I didn't even know that a PCB could melt like that. Knowing that I could simply patch up the previous controller and quickly get back up and running I instead decided to make a few changes. The next day I began to rebuild my existing oven (it has a smaller capacity, meaning less to heat up) using the components that I had previously sourced. Assembling it per the instructions[^3] was rather easy. I opted to keep just the original two heaters (didn't bother to install the "boost" heater that I had purchased). I did not purchase the reflow controller (however I would suggest that you check it out as it looks great) when I was originally sourcing components as I was unsure if I needed something that elaborate (fully functioning) considering that my previous controller was quite simple.

# Hardware
![Protoboard mess](/img/toaster-proto-board.png#40right)
After some thought as to what a barebones controller would need to have, I came up with the following list.
- Ability to turn on/off 2 SSRs
- Single push button to support start/cancel
- Display for status
- Piezo Buzzer for alarms
- Ability to track temperature
- A "warning" led to show that the oven is hot

Having a few STM8s blue dev boards kicking around that I had been wanting to play with, I decided that this would be a great project to finally put one to use. Taking the existing code base from a 32 bit 48Mhz M0+ with 128KB flash and 16KB sram down to an 8 bit 16Mhz STM8s with 8KB flash and 1KB ram was not difficult as the original controller was oversized for the simple state machine/reflow profile it was performing.

Currently the controller hardware consists of the following components on an old proto-board.
- STM8S103F3P6 STM8 Minimum System Development Board Module
- A pair of N−Channel MOSFETs
- Simple push button
- 0.96 inch OLED White Display Module 128X64 (SSD1306)
- Small Piezo Buzzer
- MAX6675 K Type Thermocouple Temperature Sensor Board
- 1 red LED

# Software
As I prefer to use open-source friendly tools/software, SDCC - Small Device C Compiler[^4] is used. It took a bit of research to get a good workflow for compiling the project, but the effort was worth it.
\
\
The first pass at the software to reproduce the previous simple state machine/pid loop fits nicely on to the target board. You can check out the [build workflow](https://github.com/microjelly/toaster/tree/master/build) as well as the source code for [ebake1](https://github.com/microjelly/toaster/tree/master/fw/stm8s/ebake1) (EasyBake v1) on my github repository. 

# Next steps
I ordered a dev board to replace the MAX6675 for a MAX31855K to reduce costs further (as this is the most expensive part of the controller). Once the software has been adjusted for that I will design/build a few boards (taking just the components, not dev boards) so I can finally eliminate the rats nest on the proto-board that has plagued my reflow oven over the years.
\
\
Also (just for fun) I'm swapping out the ST STM8S103[^5] for a Nuvoton N76E003[^6] while it does reduce overall cost (not by much, about 10&cent;) the fact that it provides and additional 10 KB of flash will make adding more features (multiple profiles, auto-calibrate, etc..) possible. Its pin compatible so in theory any PCB that I create can be populated with either chip. So far I have added the workflow to the project but have not ported any of the existing code to the new platform.

# Next
[Reflow controller update](/reflow-controller-update/)

# References
[^1]: [FRDM-KL25Z: Freedom Development Platform](https://www.nxp.com/design/development-boards/freedom-development-boards/mcu-boards/freedom-development-platform-for-kinetis-kl14-kl15-kl24-kl25-mcus:FRDM-KL25Z#?)
[^2]: [Whizoo](https://www.whizoo.com/#?)
[^3]: [Whizoo: Building a reflow oven](https://www.whizoo.com/reflowoven#?)
[^4]: [SDCC - Small Device C Compiler](http://sdcc.sourceforge.net/#?)
[^5]: [ST STM8S103](https://www.st.com/en/microcontrollers-microprocessors/stm8s103-105.html#?)
[^6]: [Nuvoton N76E003](https://www.nuvoton.com/products/microcontrollers/8bit-8051-mcus/low-pin-count-8051-series/n76e003/#?)