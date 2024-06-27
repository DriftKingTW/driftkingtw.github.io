---
title: 替 Raspberry Pi 添加開關、外接電源燈、狀態燈
tags:
  - RaspberryPi/樹莓派
  - DIY
keywords:
  - RaspberryPi
  - DIY
  - 電源燈
  - 風扇控制
abbrlink: 59bd89a7
date: 2019-11-11 21:25:00
---

![](https://static.driftking.tw/2024/06/c36bffe0d1a09c00da331e8abfb12524.jpg)

用 Headless 方式玩樹莓派的朋友肯定都遇過一個問題，就是每次關機都要登入 ssh 才能正常關機，懶一點的直接拔電源，就算沒問題也很麻煩（ 懶到過去延長線切電源都不肯 （欸），所以不如加個開關吧，不僅省時省力還能用正常的 shutdown 程序關機，一石二鳥豈不美哉 (:3[__]4 <!--more-->

還有很多時候因為 Pi 裝著殼，要得知是否開機、狀態燈有沒有在閃爍並不是那麼方便，本篇也一並記錄了裝上外接電源燈、狀態燈的步驟！

最後另外在補上風扇的控制方式（ Pi 4 工作時很熱情，大部分人可能會選擇上風扇XD ）但是溫度也不是隨時都那麼高，有了風扇控制可以依照溫度所需啟動，不但降低噪音也能增加風扇使用壽命，順便省一點點電，拯救北極熊（？）（風扇控制目前還在準備中，測試完成後會在更新電路圖）

# 硬體設定

開關 && 電源燈線路圖

![](https://static.driftking.tw/2024/06/817190ebf39aab00325c0ad380d6b41e.jpg)

SD讀取燈 && 風扇（控制電路待補）

![](https://static.driftking.tw/2024/06/ea4b380b30126c29f6206665c1ab09d7.jpg)

# 軟體設定

### 開關（正常軟體關機程序）

這邊開關的部分參考了 howchoo 上的一篇文章：[How to Add a Power Button to Your Raspberry Pi](https://howchoo.com/g/mwnlytk3zmm/how-to-add-a-power-button-to-your-raspberry-pi)，以下是直接使用作者所寫好的 Script 安裝，詳細背後的 Code 以及原理可以點進連結裡查看（英文）。

安裝 Git

`sudo apt-get install git`

接著 clone 作者所寫好的 Script [Howchoo/pi-power-button](https://github.com/Howchoo/pi-power-button)

`git clone https://github.com/Howchoo/pi-power-button.git`

安裝即可

`./pi-power-button/script/install`

### 電源燈

進入樹莓派設定界面

`sudo raspi-config`

選擇 `Interface Options` 開啟 `Serial` 來允許串列讀取，這樣我們就可以利用 GPIO 上的 TXD 來讓 LED 顯示目前是否為開機狀態（直接接 5V 的話只要插上電就會亮，沒辦法判斷樹莓派是否在開機狀態）

接著重啟樹莓派即可看到電源燈正常運作了

`sudo reboot`

### SD 卡讀取燈

將 SD 卡插入電腦中，打開至根目錄找到一個叫做 `config.txt` 的設定檔，在底部添加以下設定。
最後的 GPIO 選擇自己要用的腳位即可（[腳位參考](https://pinout.xyz/pinout/)）

```
# Use external LED as ACT LED
dtoverlay=pi3-act-led,gpio=19
```

### 風扇控制

**待補**

# 參考

[Add An External Sd Activity Light Raspberry Pi 3 Retropie Or Raspbian](https://www.youtube.com/watch?v=_REhMZJ8GJY)

[How to Add a Power Button to Your Raspberry Pi](https://howchoo.com/g/mwnlytk3zmm/how-to-add-a-power-button-to-your-raspberry-pi)

[#131 Pimp my Raspberry: Automatic fan, automatic shutdown, automatic Python](https://www.youtube.com/watch?v=P5o0PpfzuW8)

[Build a Simple Raspberry Pi LED Power/Status Indicator](https://howchoo.com/g/ytzjyzy4m2e/build-a-simple-raspberry-pi-led-power-status-indicator)

[IRL530N Datasheet](http://www.irf.com/product-info/datasheets/data/irl530n.pdf)

[#131 Pimp my Raspberry: Automatic fan, automatic shutdown, automatic Python](https://www.youtube.com/watch?v=P5o0PpfzuW8)