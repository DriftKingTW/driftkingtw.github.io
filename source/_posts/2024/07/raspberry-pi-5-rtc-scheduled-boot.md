---
title: 樹莓派 5 透過 RTC 自動定時開機
tags:
  - NAS
  - Synology
  - DSM
  - Raspberry Pi
keywords:
  - NAS
  - Synology
  - DSM
  - Raspberry Pi
  - RTC
abbrlink: 9bf653c5
date: 2024-12-01 22:00:00
---

![Raspberry Pi RTC Battery (https://www.raspberrypi.com/products/rtc-battery/)](https://static.driftking.tw/2024/12/1cd79caeda0d50c7ea9ad7cfcccd7dd9.webp)

在上一篇 [透過樹莓派 5 及 ZeroTier 幫 NAS 做異地備份](/2024/06/pi-remote-backup-for-synology-nas-using-zerotier/) 設定完成後，立刻發現了個小問題：樹莓派會需要整天開機~~偷懶~~等待 NAS 的備份請求，但我一天只有跑一次備份然後一次備份時間也不多，這樣讓樹莓派整天開機無所事事實在有夠浪費電，~~秉持著環保精神(電費)~~於是著手研究了本次的自動開關機的方案。

<!--more-->

這次 Pi 5 的更新加上了 RTC (Real Time Clock)，簡單來說 RTC 可以讓裝置在極低功耗的狀態下保持時間同步並可以拿來控制啟動高功耗的主要處理器 (在這個情況下只的就是讓 Pi 從 Halt 狀態下重新開機)。

RTC 模組通常會接上一個備用電池，這樣就可以在完全無電源的情況也繼續讓 RTC 模組工作。本次我們便是要利用這個功能來實作自動開機 👌

## 測試 RTC 模組自動喚醒功能

首先我們要先修改樹莓派的 `eeprom` 讓機器在進入 halt 狀態下能進入超低功耗狀態，並讓 RTC 的 `wakealarm` 能夠喚醒樹莓派：

```shell
sudo -E rpi-eeprom-config --edit
```

在文件底部新增這兩行設定，分別是啟動低功耗模式及關閉 GPIO 喚醒功能：

```txt
POWER_OFF_ON_HALT=1
WAKE_ON_GPIO=0
```

接著我們就可以透過設定 `wakealarm` 來測試是否可以成功定時自動換醒，指令中透過 `date` 的 `%s` 參數將設定的時間轉為秒數，並讓主機在過了設定的秒數後自動開機，所以這個範例中樹莓派應該會在 1 分鐘後重新開機：

{% colorquote info %}
透過 `date` 設定相當簡單易懂，相關使用可以參考 [man page](https://man7.org/linux/man-pages/man1/date.1.html)
{% endcolorquote %}

```shell
date -d "+1 minutes" +'%s' | sudo tee /sys/class/rtc/rtc0/wakealarm
sudo halt
```

{% colorquote success %}
RTC 模組雖然有提供電池接口，但在不拔電源的情況下不需要電池也能使用 RTC 功能的喔！
{% endcolorquote %}

## 應用實例

到這邊已經知道如何設定自動開機了，接著就是任務完成後要讓樹莓派自動關機。由於這部份每個人使用的情況可能也不盡相同，我這邊提供自己的 NAS 備份案例來說明：

### 開機邏輯

首先假設我在 NAS 端的設定是每天凌晨兩點自動備份到樹莓派上，那麼這邊就需要讓樹莓派在每天四點前先自動開機等待備份請求，並偵測到備份完成後設定下一次開機時間並自動關機。

此時我的 `wakealarm` 設定明天的凌晨 1:50 啟動，指令如下：

```shell
date -d "+tomorrow 1:50" +'%s' > /sys/class/rtc/rtc0/wakealarm
```

### 關機邏輯

由於需要偵測 NAS 那邊是否備份完成，在經過一番搜尋之後決定使用 [N4S4](https://github.com/N4S4) 提供的 [synology-api](https://github.com/N4S4/synology-api) 來實作備份進度的檢測。

簡單來說我是透過 API 在每天設定的備份時間過 30 分鐘後，自動執行一個 Python Script 去發請求檢查 `HyperBackup` 的備份進度，如果完成就設定 `wakealarm` 並關機，如果還未完成就每 30 秒詢問進度直到備份完成。

由於此腳本不是本篇重點就不細說，有興趣的朋友可以直接參考或使用我所寫的腳本：[pi-backup-power-scheduler](https://github.com/DriftKingTW/pi-backup-power-scheduler) 裡面有提供使用說明及設定方式ㄛ！

## 參考

[Buy a Raspberry Pi RTC Battery - Raspberry Pi](https://www.raspberrypi.com/products/rtc-battery/)

[Raspberry Pi hardware - Raspberry Pi Documentation # Real Time Clock (RTC)](https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#real-time-clock-rtc)

[synology-api](https://github.com/N4S4/synology-api)
