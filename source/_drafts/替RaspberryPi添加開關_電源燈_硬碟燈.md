---
title: 替 Raspberry Pi 添加開關以及外接電源燈、狀態燈
tags:
  - MacOS
  - TimeCapsule
  - TimeMachine
keywords:
  - MacOS
  - TimeCapsule
  - TimeMachine
  - 時光機備份
abbrlink: 59bd89a7
date: 2019-10-11 21:25:00
---

# 前言

# 軟體設定

### 開關

這邊開關的部分參考了 howchoo 上的一篇文章：[How to Add a Power Button to Your Raspberry Pi](https://howchoo.com/g/mwnlytk3zmm/how-to-add-a-power-button-to-your-raspberry-pi)，以下是直接使用作者所寫好的 Script 安裝，詳細背後的 Code 以及原理可以點進連結裡查看（英文）。

安裝 Git

`sudo apt-get install git`

接著 clone 作者所寫好的 Script [Howchoo/pi-power-button](https://github.com/Howchoo/pi-power-button)

`git clone https://github.com/Howchoo/pi-power-button.git`

安裝

`./pi-power-button/script/install`

### SD 卡讀取燈

將 SD 卡插入電腦中，打開至根目錄找到一個叫做 `config.txt` 的設定檔，在底部添加以下設定。
最後的 GPIO 選擇自己要用的腳位即可（[腳位參考](https://pinout.xyz/pinout/)）

```
# Use external LED as ACT LED
dtoverlay=pi3-act-led,gpio=19
```

# 硬體設定

# 參考

[Add An External Sd Activity Light Raspberry Pi 3 Retropie Or Raspbian](https://www.youtube.com/watch?v=_REhMZJ8GJY)

[RetroPie Add A Power Button / Switch Raspberry Pi 1 2 3](https://www.youtube.com/watch?v=4nTuzIY0i3k)

[How To Add A Power LED RetroPie Raspberry Pi 1 2 3 Or Zero](https://www.youtube.com/watch?v=of2p-FHg6nY)

[How to Add a Power Button to Your Raspberry Pi](https://howchoo.com/g/mwnlytk3zmm/how-to-add-a-power-button-to-your-raspberry-pi)