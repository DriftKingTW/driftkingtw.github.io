---
title: Use Raspberry Pi to build a FAKE TimeCapsule (and NAS) - Hardware
tags:
  - MacOS
  - NAS
  - DIY
  - TimeCapsule
  - TimeMachine
keywords:
  - MacOS
  - NAS
  - MacMini
  - TimeCapsule
  - TimeMachine
  - TimeMachineBackup
abbrlink: ca77c25e
lang: en
date: 2019-11-12 21:25:00
---

![](https://static.driftking.tw/2024/06/6315a42c851dd716a1274ec285fa016b.jpg)

There are plenty of Mac mini case modification projects on the internet. Though the unibody aluminum housing design had been there for almost a decade, it's still the best looking mini computer case on the market imo.

In mid 2019, Raspberry Pi receive a huge update - the Pi 4. We finally got true gigabit ethernet, USB3.0 support, and a lot more. This makes Pi 4 suitable for simple NAS project (also for TimeMachine backup). So I decide to build a fake(?) TimeCapsule with Pi (at least the case is real, or should I just call it "Fake Mac mini" ? lol)<!--more-->

This article will show how I build this project, and the issue that I ran into (hardware side, the software configuration can be found at: [Use Raspberry Pi to build a FAKE TimeCapsule - Software](https://blog.driftkingtw.me/archives/613e9fa3.html), [Use Raspberry Pi as a NAS](https://blog.driftkingtw.me/archives/c138150d.html))(haven't translate to English yet).

# Idea && Design

(https://static.driftking.tw/2024/06/e39d19076b26e35c8b8e075f87a977ed.jpg)

I bought this used Mac mini case for cheap (around 20USD), it still in very good shape. You can buy it from ebay or AliExpress.

First, let's do some simple measure. The maximum motherboard you can put inside is around 17cm*17inch (lots of people use Mini-ITX in Mac mini's case.) Height is about 2.5cm, for our Pi project is more than enough.

Here's the first design diagram (the hub will cause some issue so I connect the HDDs direct to Pi in final product, see [Troubleshooting](#Troubleshooting)):

![](https://static.driftking.tw/2024/06/61ff68d9ae9472e9ac6aab614fd0d862.jpg)

All I need to do is mount these things up. So I decide to draw some 3D printed files to do the job.

![](https://static.driftking.tw/2024/06/a1ee6a45480b4903575330dba00b6b2d.jpg)

And here's the diagram that shows each mounting hole's distance I measured.

Next, the HDD mount. The arm on both side aren't strong enough, so I just cut them off.

![](https://static.driftking.tw/2024/06/fe99dba2aaa69183f4de094afe6f88b0.png)

Here comes the tricky part. Unfortunately, there's no information about the mounting screw for Mac mini case. [ifixit](https://www.ifixit.com/Teardown/Mac+Mini+Mid+2010+Teardown/3094) only says it's four 7.9mm long Torx screw, didn't mention the shaft diameter.

I tried several screw that I have, luckily, one screw can screw in and quite stable. So I can use caliper to measure it's size: 1.7mm (You can use M1.7 phillips tapping screw instead of official torx screw)

![](https://static.driftking.tw/2024/06/e4f9022a65f2953d14c5868a92f17ccd.jpg)

Pi mounting plate. An isolation brick to protect GPIO pins is nice to have:

![](https://static.driftking.tw/2024/06/e4c0a556fc13cdaf6b502f1aa0f6e685.png)

Make some feet under the mounting plate.

![](https://static.driftking.tw/2024/06/aa2edadab8db3c3f55c77575867ce45a.png)

The I/O shield (the plastic frame for Mini-ITX I/O shield is brought from other maker) Left side is for USB-C female connector and ethernet extender cable, right side for momentary power switch.

![](https://static.driftking.tw/2024/06/8f905ade40585d751041177b08e79241.png)

I made some mistake about the side mounting holes on I/O shield, so just ignore it. (The ethernet extender cable' screw can hold I/O shield in place) Also need to cut the Pi mounting plate to fit in the case.

Finally, the 30mm fan mount:

![](https://static.driftking.tw/2024/06/9663539750fc6e1a01d72a0e6ca2cce7.png)

The STL files I used in this project can be download at: [PiMacMiniCaseSTLFiles](https://drive.google.com/open?id=1iUhxCqxGfxcIEYv6l-oQD4FoOvjMj0Gv)

# Installation

Alright, here's the finished 3D printed items. (SLA Resin)

![](https://static.driftking.tw/2024/06/4cb977f902b7db70268216fc77f01e08.jpg)

I've found that the holes on I/O shield are a bit too small. So need to enlarge the Type-C and switch hole for a better fit.

Sand down the surface and paint them in matte black.

![](https://static.driftking.tw/2024/06/e4965259cb7fd1610a97228bfad1ff1a.jpg)

Ouch! There's a crack on the edge when I try to trim it. But since it's hide in the case, not a big deal.

![](https://static.driftking.tw/2024/06/e2a8bc4e88ac7b2eaaa1ed576fcf357d.jpg)

And I forgot the Micro SD on pi protrudes from the edge, so more trimming work need to be done.

![](https://static.driftking.tw/2024/06/c91adb1c6577f9c8fcc2d50734105097.jpg)

Then opens up the USB to SATA cable and cut out the VCC connect on board.

![](https://static.driftking.tw/2024/06/abc4ff7471f8b0b06f3ebf29e8614199.jpg)

Use some electrical tape to cover the board.

![](https://static.driftking.tw/2024/06/e0cdf2e8daec0e2f074fa3de042b7652.jpg)

Remember to check the pins with multimeter, make sure we didn't ruined the cable.

![](https://static.driftking.tw/2024/06/170e14d2dcf3194d6f20d2a5878ba36e.jpg)

Screw in HDDs and test the mount.

![](https://static.driftking.tw/2024/06/b2d869165c6180294e208a67c36faea0.jpg)

![](https://static.driftking.tw/2024/06/b96bcb88af19d84a031b431492c73aff.jpg)

And solder the power line to Type-C connector. One for HDDs, other for Pi.

![](https://static.driftking.tw/2024/06/0372a2ae7e6892a3183cf6cf289273f4.jpg)

Momentary switch with LED. I use 1k Ohm resistor here.

About the wiring diagram you can check my other article: [Raspberry Pi - Power switch, External power LED, SD Act LED](https://blog.driftkingtw.me/archives/59bd89a7.html)(Haven't translate to English yet)

![](https://static.driftking.tw/2024/06/57ec55a5b017802d4b385da3cf0e484a.jpg)

Test it!

![](https://static.driftking.tw/2024/06/c36bffe0d1a09c00da331e8abfb12524.jpg)

Glue the power switch and Type-C connector in place.

![](https://static.driftking.tw/2024/06/11ca018c567c7754a629beaa46d880ff.jpg)

Some simple wiring.

![](https://static.driftking.tw/2024/06/52e77f5c79bc616936d3ddd435c9be17.jpg)

Put on the bottom cover.

![](https://static.driftking.tw/2024/06/f2a9ba93a6642e86a56ccc2059ac9437.jpg)

Done!

![](https://static.driftking.tw/2024/06/6315a42c851dd716a1274ec285fa016b.jpg)

![](https://static.driftking.tw/2024/06/795a2ea5cf93b1406de9a8217f6fdf90.jpg)

Bay ~

![](https://static.driftking.tw/2024/06/ded31ae5bcd83fd0e493c96cea042921.jpg)

~~OK I think it just looks like a real Mac mini~~

PS The max cpu temperature is around 50°C (room temp:26~28°C)

# Troubleshooting

### Mac mini housing screw spec?

I use phillips M1.7 x 6mm tapping screw.

### USB powered hub issue

Some usb 3 powered hub will prevent Pi 4 from booting, here's some discussion on Pi's official forum: [Pi 4 fails to boot when active USB switch attached](https://www.raspberrypi.org/forums/viewtopic.php?t=244900). You can unplug the powered hub and plug it back. Pi will starts to boot and everything worked fine, but it's really annoying.

Some people reports that cut out the Vcc on hub can solve the issue, but I tried and nothing happened :(

![](https://static.driftking.tw/2024/06/957b60c62cffeb6803c3d5e14f2db66b.jpg)

So I end up modify the USB to SATA cable instead.

![](https://static.driftking.tw/2024/06/e0cdf2e8daec0e2f074fa3de042b7652.jpg)

### HDD and pi both using a 3A Official Power Supply？

I actually connect 2 HDD and Pi to the same power source, which is an official power supply. The main reason I do this is to avoid Pi's USB port maximum 1.2A downstream limit. 

I've measure the current drain on both HDD(w/ USB to SATA cable) and Pi, and it's enough to be powered by a 3A power supply (my WD HDD's peak current when activating is about 750mA) but can't be power direct through Pi's USB port due to 1.2A current limit. (I tried to connect direct to Pi's USB, and one HDD fail to mount)

Note that 1 HDD is fine to direct plug it into Pi's USB port.

# Item list

- 1 x Pi 4 4GB
- 1 x 8G MicroSD Card for boot
- 1 x A1347 Apple Mac mini Case (Mid 2010)
- 2 x 2.5" HDD
- 2 x SATA to USB3 Cable
- 3D Printed Mount / IO Panel
- 1 x 30mm Fan
- 1 x Momentary Switch w/ LED & Cap
- 1 x 3mm Front Panel LED
- 2 x Current-limiting Resistor (330Ω ~ 1kΩ)
- 1 x Gigabit Ethernet Network Extension Cable
- 1 x USB Type-C Male Connector w/ PCB
- 1 x USB Type-C Female Connector w/ PCB
- Some USB Cable (Current support 3A at least)
- 3 x M3 Screw && M3 Hex Nut for Fan Mounting
- 4 x M3 Screw for HDD Mounting
- 2 x M3 Screw for Ethernet Port Mounting
- 3 x M1.7 Tapping Screw for Case Mounting w/ M2 Washer
- Some DuPont Line

# Reference

[Mac Mini Mid 2010 Teardown](https://www.ifixit.com/Teardown/Mac+Mini+Mid+2010+Teardown/3094)

[Raspberry Pi GPIO Pinout](https://pinout.xyz/)

[Raspberry Pi 4 Tech Specs](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/specifications/)

[Power Supply](https://www.raspberrypi.org/documentation/hardware/raspberrypi/power/README.md)

[Pi 4 fails to boot when active USB switch attached](https://www.raspberrypi.org/forums/viewtopic.php?t=244900)

