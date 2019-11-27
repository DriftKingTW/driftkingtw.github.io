---
title: 理解 Raspberry 4 Type C 設計問題
tags:
  - RaspberryPi/樹莓派
  - USB-C
keywords:
  - RaspberryPi
  - USB-C
abbrlink: b2345c23
date: 2019-10-26 21:25:00
---

今年(2019)樹莓派基金會發表了久違的硬體更新，升級到了4代，相信有在關注的朋友們都知道，本次供電設計終於換成了 Type-C ~~(終於有種進化成現代化單片機的感覺)~~
不過也因為電源電路設計的一些問題導致了有 E-Mark 晶片的 USB-C 線材無法為樹莓派供電（例如 Apple 的 Macbook USB-C 充電線、Google Pixel 3 附帶的 USB-C 線材等），反倒是便宜的一般線材能夠正常運作XD<!--more--> ~~證明樹莓派是給窮人家的玩具~~ 。

出於好奇我搜尋研究了一下原因，並且想了解決方法記錄在這篇文章中，本人不是這方面的專業人士，若有錯誤歡迎指出討論。

# Type-C 供電定義及設計

下圖是 Type-C 的腳位圖，這次設計出現問題是位於兩個 CC 腳位的部分（ CC1 & CC2 ），
CC (Configuration Channel) 腳位是 Type-C 重要的設定通道，可以利用不同的 CC 電阻設定判斷裝置資訊、決定模式，以及藉此支援正反插。

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1572070453/blog/2019/10/%E7%90%86%E8%A7%A3%20Raspberry%204%20Type%20C%20%E8%A8%AD%E8%A8%88%E5%95%8F%E9%A1%8C/Screen_Shot_2019-10-26_at_2.13.59_PM.png)

根據 [Type-C 定義文件](https://www.usb.org/document-library/usb-type-cr-cable-and-connector-specification-revision-20-august-2019)，藉由 CC 腳位的設定我們可以有以下不同的狀態：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1572070948/blog/2019/10/%E7%90%86%E8%A7%A3%20Raspberry%204%20Type%20C%20%E8%A8%AD%E8%A8%88%E5%95%8F%E9%A1%8C/Screen_Shot_2019-10-26_at_2.22.22_PM.png)

樹莓派所需要的是從供電端拉 5V/3A 的電力，為接受電力的設備（ Sink Device ）（這裡我不知道怎麽翻譯比較適當XD），由圖表可知我們應該要將 CC 設定在第二或第三種狀態才能正常為樹莓派提供正確的電壓。

瞭解接腳定義後來看供電端與設備端連線時的簡化電路圖：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1572071612/blog/2019/10/%E7%90%86%E8%A7%A3%20Raspberry%204%20Type%20C%20%E8%A8%AD%E8%A8%88%E5%95%8F%E9%A1%8C/Screen_Shot_2019-10-26_at_2.33.18_PM.png)

上圖為一般 Type-C 線材連接時的樣子，可以看到只有一條 CC 線接通兩邊，並且在右側設備端有接一個下拉電阻（ Pull-down Resistor：簡稱Rd ），供電端會藉由偵測到的阻值判斷所需要的 USB-C 狀態。

接著看看連上 E-mark 線材時的電路圖：（ E-mark 是在線裡的 IC ，可以提供 PD 通訊以及宣告這條線的資訊 ）

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1572072226/blog/2019/10/%E7%90%86%E8%A7%A3%20Raspberry%204%20Type%20C%20%E8%A8%AD%E8%A8%88%E5%95%8F%E9%A1%8C/Screen_Shot_2019-10-26_at_2.43.37_PM.png)

由上圖可以看到，線材內會有一個 Ra 下拉電阻，可以告訴供電端（DFP：Downstream-Facing Port ）在 CC2 上對 E-mark IC 提供 Vconn 電力。

簡單理解了 Type-C 的供電定義後，接著看樹莓派4的電源電路設計。

# Raspberry Pi 4 電源電路

樹莓派基金會已經釋出了除了 SoC 以外的[電路設計圖](https://www.raspberrypi.org/documentation/hardware/raspberrypi/schematics/rpi_SCH_4b_4p0_reduced.pdf)，首先來看看電源電路的部分：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1572071448/blog/2019/10/%E7%90%86%E8%A7%A3%20Raspberry%204%20Type%20C%20%E8%A8%AD%E8%A8%88%E5%95%8F%E9%A1%8C/Screen_Shot_2019-10-26_at_2.30.35_PM.png)

左側是 Type-C 的母座，接著把注意力放到 CC1 以及 CC2 兩隻腳位上，這裡的設計是把 CC1 及 CC2 短路後共同接一個 5k1Ω Rd 下拉電阻，在普通的線材下這個設計是完全沒問題的，PD 供電端因為線材只有一條 CC 相連，只會偵測到Rd電阻，另一條 CC 空接，按照前面的圖表可以看到樹莓派狀態是 Sink attached ，可以對其供電 5V/3A。

但是這設計接到 E-mark 線材的時候就不一樣了，由於 E-mark 線材裡面的 CC2 會有 Ra 下拉電阻，連接後 Ra(1kΩ) 會跟電源電路裡的 Rd(5k1Ω) 電阻並聯，分壓後變成 836Ω 等效電阻，供電端偵測到這阻值會把它當成 Ra（ 836Ω 屬於 Ra 的許可誤差範圍），參照上面 Type-C 狀態表可以發現，此時樹莓派被當成了音訊裝置（ Audio Adapter Accessory ，例如 USB-C 轉 3.5mm 音訊轉接器等裝置），於是供電端就不會對樹莓派輸出任何電力了！這就是為什麼普通線材可以正常使用，反而是較高級(?)的 E-mark 線材無法使用的原因。

順帶補充一下必須使用 E-mark 晶片的時機（ 只要滿足其中一項就需要 E-mark ）：

1. VBUS 需要提供 3A 以上的電流
2. USB 3.1 Gen1 或 Gen2 協定
3. 需要用到 Alternate Modes 時

# 修正方法

理論上來說，應該要將樹莓派電源電路中的 CC1 以及 CC2 隔開後各接一個 5k1Ω 的 Rd 即可正常使用 E-mark 充電線了，但是由於那部分的電路設計在 Type-C 連接器下方，所以不容易施工，需要找另外的解決方案：

1. 翹起 Type-C 連接器上的 CC1 腳讓他另外接一個 Rd 電阻到 GND 請參考此[樹莓派論壇文章](https://www.raspberrypi.org/forums/viewtopic.php?t=249975)
   
2. 另外做一條 Type-C 延長線，自行在中間切斷 CC1 及 CC2 連接並自行接 Rd電阻到 GND

實際操作待補上（買的材料還沒來，無法測試，等測試後在詳細記錄QQ）

PS. 有風聲說之後會有小改 PCB 解決此問題，不過那麼久了都沒消息，不知道何時才會改善

# 結論

因為設計上的瑕疵，所以無法使用 E-mark 線材來為樹莓派供電，網路上有些說 PD 充電器無法使用其實算是不夠嚴謹的說法，因為若搭上無 E-mark 的線材還是能夠正常使用的（ 因為只有一條 CC 連通，且線材內沒有 Ra 並聯電源電路裡的 Rd，不會造成識別錯誤 ），省了一個 Rd 電阻但造成 E-mark 線材無法使用，真是這次樹莓派4最可惜的部分了。

# 參考

[EXPLORING THE RASPBERRY PI 4 USB-C ISSUE IN-DEPTH](https://hackaday.com/2019/07/16/exploring-the-raspberry-pi-4-usb-c-issue-in-depth/)

[USB Type-C® Cable and Connector Specification Revision 2.0, August 2019](https://www.usb.org/document-library/usb-type-cr-cable-and-connector-specification-revision-20-august-2019)

[SCHEMATIC1 : REDUCED SCHEMATICS](https://www.raspberrypi.org/documentation/hardware/raspberrypi/schematics/rpi_SCH_4b_4p0_reduced.pdf)

[Fixing the Pi 4 usb C issue by hand](https://www.raspberrypi.org/forums/viewtopic.php?t=249975)

[Designing a Type-C Electronically Marked Cable - Part 1](https://www.ecnmag.com/article/2016/06/designing-type-c-electronically-marked-cable-part-1)