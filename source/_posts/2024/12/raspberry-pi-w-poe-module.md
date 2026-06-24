---
title: 一定要用 PoE HAT 嗎？ 試試自己動手替樹莓派加上符合 IEEE802.3af 標準的 PoE 模組吧！
tags:
  - Raspberry Pi
  - PoE
  - PiKVM
keywords:
  - Raspberry Pi
  - PoE
  - PiKVM
abbrlink: 79a87c3e
date: 2024-12-04 23:00:00
---

![樹莓派 4B + SDAPO DP1435-5v](https://static.driftking.tw/2024/12/5e4a61857fc05da806e38f4d0d618399.webp)

看到標題後，你的內心 OS 可能是「市面上這麼多 PoE HAT 即插即用，幹嘛這麼麻煩？」🥺

或是「PoE 是什麼鬼 😱？！」如果有以上疑問，請容我先簡單說明一下什麼是 PoE 以及這麼做的理由 ⬇️

<!--more-->

## 什麼是 PoE？

Power over Ethernet (簡稱 PoE) 簡單來說就是我們可以透過網路線來提供連接的設備電力，這麼做最顯而易見的好處便是不需要替裝置另外接上電源，在同一條網路線上同時傳輸資料及供電，大幅增加佈線的方便程度。

![僅透過一條網路線為樹莓派供電及連接網路](https://static.driftking.tw/2024/12/f09d00bdd4dccfade17928b52f9e2ad5.webp)

樹莓派在第三代的時候加入了四根所謂的 PoE 針腳 (PoE Pin Headers) 來支援這方面的應用，但因為從乙太網插座中引出腳位後沒有完整供電電路（猜測是變壓器等元件體積原因），所以還須搭配官方或是第三方廠商所推出的 PoE HAT（擴充板）才能使用這個功能 🛠️

![樹莓派官方 PoE+ HAT](https://static.driftking.tw/2024/12/c53dc0dbfb1cd1bbb14006ad33f104e8.webp)

## 為什麼不使用現成 PoE HAT？

瞭解什麼是 PoE 後回到第一個問題，既然這樣買個現成的 PoE HAT 使用就好，何必這麼麻煩？確實大多情況下，就算使用 HAT 也能繼續在上方進行擴充堆疊不同的 HAT 做使用。

但如果我有外殼或尺寸的限制，這個方法其實挺不現實的，拿我這次的使用舉例：

![PiKVM](https://static.driftking.tw/2024/12/c7e78b4837fa18f1257514c2922166ce.webp)

圖中這是我使用的 PiKVM 擴充套件以及裝上底殼的樣子（Geekworm KVM-A3 V1.6），使用了之後想加上 PoE 來減少電源走線，但可以很明顯看到，要另外加上任何一張 PoE HAT 都會影響整體的尺寸以及上蓋的安裝，雖然也是可以重新設計外殼，但我最大要求體積能夠盡可能的保持原本的小巧機身，~~不然房間太小我都快沒地方可以移動了~~

## 解決方式 - 自己安裝 PoE 模組

為了解決這個問題，我東找西找發現市面上有單獨的 PoE 模組能夠購買，這樣只需要將樹莓派上的 PoE 針腳從模組輸入，就能得到穩定的 5V 輸出來供給樹莓派了，並且不會像一般 PoE HAT 會佔用到 GPIO 影響到原本 HAT 的使用，~~可以說是非常完美~~

![SDAPO DP1435-5v](https://static.driftking.tw/2024/12/c5342a5b6d77116f45c1cfb0b44ed3a1.webp)

模組的部分我選擇了 [SDAPO DP1435-5v](https://www.sdapo.net/product/product-2-259.html) 他符合 `IEEE802.3af` 協議，並且有隔離變壓，輸出能力為 5.15Vdc/2.4A (12.4W)，在提供整套系統足夠電流的前提下，體積也適合這次所剩不多的改裝的空間。

## PoE 模組與樹莓派接線

首先先來看看 PoE 模組提供的輸入輸出腳位，分別正負極 5V 輸出以及四個 PoE 輸入腳位：

![SDAPO DP1435-5v Pinouts](https://static.driftking.tw/2024/12/905973187d31ab8eea1d137a5aafcb8d.webp)

接著是樹莓派的 PoE 腳位定義，這部分其實資源意外的有點少：

![Pi 4 PoE Reduced Schematic](https://static.driftking.tw/2024/12/d761bc4b4c1d2524ec5fdf9336d13b47.webp)

對照 PoE 定義：

![PoE Pinouts](https://static.driftking.tw/2024/12/f37ce79070c5b6e9641b99c79c0e452c.webp)

![PoE Headers on Raspberry Pi 4](https://static.driftking.tw/2024/12/edbce6dea0e49fc5ebae23098c59f93d.webp)

如上圖，與樹莓派官方提供的腳位定義來看應該同樣以 `J14` 印刷方向為準，而不是以 `PoE` 字樣的方向。（順帶一提，因為手邊剩下一張 Pi 4，PoE 沒有針腳是因為之前做其他專案所以把它拆了 🤣）

有關樹莓派 PoE 的相關說明，十分推薦這篇文章：[關於 PoE 以及 Raspberry Pi 的 PoE](https://magicjackting.pixnet.net/blog/post/227003620)

我也是與文中一樣查到相同的錯誤腳位資訊被搞混了一陣子，非常感謝該文作者清晰的梳理 XD

整理好定義和確認模組針腳資料後按照文章首圖接線即可，並且把 PoE 模組輸出接入 GPIO 的電源腳位：

![樹莓派 4B + SDAPO DP1435-5v](https://static.driftking.tw/2024/12/5e4a61857fc05da806e38f4d0d618399.webp)

⚠️ **通電前一定要再三檢查樹莓派電源腳位沒有接錯** ⚠️ ~~請不要問我為什麼特別強調這點~~

測試沒問題可以正常透過 PoE 交換器供電後，我另外有把 PoE 模組的針腳解焊可以進一步節省空間。

## 模組安裝方案

原本 PiKVM 套件的散熱方案是使用 30mm 風扇，但溫度其實都不高加上無法控速的小風扇太吵，趁這次順便改為被動散熱器。

更換的被動散熱器上有預留 30mm 風扇的鎖孔，因為不需要風扇所以我就把它拿來做固定 PoE 模組用，然後用 3D 列印印了一個固定架：

![](https://static.driftking.tw/2024/12/42eaffb8028989666f23ddae74cc03a5.webp)

![](https://static.driftking.tw/2024/12/3fb9cea35e458a15471e36e5024ac3ec.webp)

![](https://static.driftking.tw/2024/12/3e877143197f3ed51613ef46fa93a39c.webp)

可以看到圖中隔離變壓器的部分其實有點太高，但我把位置設計在 PiKVM 擴充板的風扇開孔中就沒問題了 🎉

## 引出 GPIO 電源腳位進行供電以及組裝

由於安裝 PiKVM HAT 後會佔據樹莓派上所有的 GPIO，我們需要改從 HAT 上的延伸 GPIO 接電，但接上杜邦安端子的話會卡到上蓋導致外殼無法組裝 😱

所以我這邊透過 2P 杜邦彎針把 5V 跟 GND 引出來從側邊接到 PoE 模組上，剛好在外殼旁邊的小空間足夠走線：

![杜邦 90 度彎針](https://static.driftking.tw/2024/12/5e40457fae652feb69dbd0fb258d7a36.webp)

![接上 5V 輸出](https://static.driftking.tw/2024/12/bd9e3f942f2abce4a6aee5ecdd51f56c.webp)

可以看下外殼組裝後的樣子，空間可說是剛剛好：

![上方視角](https://static.driftking.tw/2024/12/7b7e29d649597e7a1526aca61b6f6878.webp)

![側面視角](https://static.driftking.tw/2024/12/b1ec015ffdf5a3c918eafcdb55f4d509.webp)

組裝外殼再次測試，大功告成！

![由 PoE 供電的 PiKVM 改裝成功](https://static.driftking.tw/2024/12/3ab35f8ee43e28c7078fbc8e3d9be330.webp)

## 結語

後來比較忙最近才想到要把過程記錄下來，中間這段時間已經一段時間了使用上都相當穩定，雖然看似只省了一根電源線，但實際上還多省了一個插座 XD

整體走線相對方便了不少個人覺得非常值得，未來有遇到這類問題的朋友也不妨來試試這個方案 👌

## 參考

[SDAPO DP1435-5v Support 10M/100M/1000M IEEE802.3af Standard PoE Module](https://www.sdapo.net/product/product-2-259.html)

[SDAPO DP1435-5v Datasheet](https://www.lcsc.com/datasheet/lcsc_datasheet_2304031800_SDAPO-DP1435-5V_C5380097.pdf)

[關於 PoE 以及 Raspberry Pi 的 PoE](https://magicjackting.pixnet.net/blog/post/227003620)

[PoE+ HAT - Raspberry Pi](https://www.raspberrypi.com/products/poe-plus-hat/)

[Raspberry Pi 4 Reduced Schematics](https://datasheets.raspberrypi.com/rpi4/raspberry-pi-4-reduced-schematics.pdf)
