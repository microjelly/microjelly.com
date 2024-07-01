---
title: "Reflow controller update"
date: 2021-08-13T18:13:29Z
tags: ["reflow", "stm8s", "n76e003"]
draft: false
---
This is an update to a previous log entry [Reflow controller replacement](/reflow-controller-replacement/).

# Progress
![v1 front](/img/reflow_v1_front.png#25right)
![v1 back](/img/reflow_v1_back.png#25right)
The current version of the reflow controller board has been in use here in the shop for the last few months. No issues so far, which can be expected when you are not running on a protoboard with wild wires everywhere.
\
\
The [repository](https://github.com/microjelly/toaster/tree/master/build) has been updated to include the KiCad project as well as both the STM8s and N76E003 version of ebake1.

## Changes from plan
I did not end up replacing the MAX6675 as expected. I found a supplier with the MAX6675 in stock and at a reasonable price. Also managed to get the same board to support either a STM8S103 or a N76E003. I now know that "pin compatible" does not equal "drop in replacement", though it was not that difficult to have a single PCB support both. I ended up using a different display all-together, I ran into issues trying to reliably source bare ssd1306 OLED displays at a low cost. Ended up choosing a 0.96" 128x64 COG (chip on glass) lcd module with UC1701C controller[^1]. I wish I had found these years ago, easy to source and even easier use.

# Hardware
Here is what ended up on the final board.
- STM8S103F3P6 -or- N76E003AT20
- 2x 2N7002 N−Channel MOSFETs
- 1 front push button (for action)
- 1 back/side push button (for reset)
- 0.96" 128x64 COG (UC1701C controller)
- Small Piezo Buzzer
- MAX6675 K Type Thermocouple Temperature Sensor
- 1 green LED (power)
- 1 red LED (hot)
- 1 3/pin connector for servo control (tested but not implemented in ebake1)

# Software
The current version of ebake1 consists of a simple state machine that runs a hard coded reflow profile. That profile can be modified in **ebake1/defaults.h**.
```
const uint8_t profile[3][2] = {{150,40},{190,10},{245,12}};
```
- Ramps to 150&deg;C for 40 seconds.
- Ramps up to 190&deg;C for 10 seconds.
- Ramps up to 245&deg;C for 12 seconds.
- Cools down.

Currently there is a 'overshoot' option to compensate for the lack of PID tuning. At its default **10** will cause each of the profile temp targets to be **+10** &deg;C, once the target is reached the maintain temp will be what is in the profile. This is gross, and will be eliminated in a future code update.
```
#define PROFILE_OVERSHOOT 10 // FIX the PID, don't do this!!
```

# Next Steps
Auto-PID tuning/setup process, I already wrote this for the STM8s as a demo but unfortunately could not have that in the ebake1 firmware as the code was to large. Now that I have a N76E003 (with its additional 10k flash) I will set to combine them. Also would like to add the ability to run custom profiles that are entered via the serial interface.
\
\
Also working on a small 3d-printed case that I can attach to the front of the toaster oven, covering the holes left by removing the original controls.

# References
[^1]: [Blue Serial SPI Small 0.96" 128x64 Graphic COG LCD Display Module](https://www.buydisplay.com/blue-serial-spi-small-0-96-inch-128x64-graphic-cog-lcd-display-module#?)
