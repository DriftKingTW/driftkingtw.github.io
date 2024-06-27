---
title: Pi Dash - 一個比較大的桌上型 Touch Bar 還是 Cyberdeck ？
tags:
  - Raspberry Pi
keywords:
  - raspberry pi
  - 儀表板
  - 樹莓派
abbrlink: e9d3f177
date: 2023-10-18 21:00:00
---

![Pi Dash - Raspberry Pi Dashboard](https://static.driftking.tw/2024/06/76e853e176c597b1e52eb6b67b06e654.jpg)

那個被詬病已久，大家喜聞樂見被砍掉的 Touch Bar，偏偏是我最喜歡的功能之一！！ 😭😭😭（~~蘋果怎麼在這種地方聽到了使用者的回饋~~）
既然官方不幹了，那就是時候來弄一個更強並更符合自己需要的替代品吧。

偷偷說一下，其實這個小專案已經完成並使用超過一年了，中間也陸續對軟硬體設計進行改良，至於為什麼現在才有這篇文就...不要問 🤣

<!--more-->

## 發想

起初我是先從一些 PC 機箱內的長條狀 Performance Monitor 找到靈感，想找一塊長條的觸控螢幕來做，
玩家常會用長條形的螢幕放置在機箱中顯示一些系統溫度/效能等訊息（~~還有一些油油或潮到出水的圖~~ 💦）
這種螢幕通常是直接當作一個小的外接螢幕接到顯示輸出，或 USB 傳送到螢幕上附帶的處理器去顯示資料。

但外接螢幕的話對我來說會有些問題，首先是這種小外接螢幕容易影響到原本遊標的操作，
要是不小心移動到小螢幕裡還以為主螢幕內的遊標消失了。
再來就是我目前的主力機 Macbook Pro 14 M1 Pro 只支援兩個外接顯示器，
沒辦法再多接一個螢幕（~~為了 Apple Silicon 真是有得必有失~~）。

加上不想被現成的軟體功能綁著，便想到了用樹莓派跑獨立的系統去控制螢幕顯示跟內容部分，
再透過 API 與電腦主機這邊溝通後透過網頁 UI 進行操作顯示的方式實作。
這樣不但可以不影響原本電腦的使用，也能夠把一些定期任務跟家庭控制丟給樹莓派處理，
使用上相對一般的做法可說是靈活了不少（缺點就是軟體部分很多要自己搞）

把問題點與需求列出來之後，就可以把需要的材料跟技術整理一下開始進行：

- 硬體：
  由於這次硬體核心部分就是樹莓派搭配觸控螢幕而已，~~相對簡單~~，只需要另外自己做個外殼跟支架。
  螢幕部分我找的是 Waveshare 的 11.9 吋長條形螢幕，剛好有設計可以直接把樹莓派裝在後面的機構，能夠省下不少工夫。
  樹莓派則是使用 Pi 4B 8G。另外偷抱怨一下這螢幕雖然是 IPS 但不是全貼合，感覺讓色彩跟抗反射效果略差...但也暫時找不到更好的替代方案了 🥹

  後續還有加上了一些外掛功能，首先是在外殼加入了 Apple Watch 充電器，以及一個給桌機使用的 Windows Hello 紅外線相機。

- 軟體：
  由於我主要是搭配 Mac 使用，所以利用了之前就有在使用的 BetterTouchTool，作為 Mac 端大部分操作的控制/存取中心
  它正好有提供 http server 及一些 API 可以取得資料，執行自動動作等功能，真是幫我省下大量自行開發的時間。
  - 後端：Node.js + Express 開發
  - 前端：Vue.js + Vuetify 開發
  
  PC 部分較單純，我選則 LibreHardwareMonitor 提供的 HTTP Server 來作硬體溫度資訊顯示。

## 硬體設計

### 外殼

首先我將樹莓派與螢幕組裝後，把軟體跟 API 服務等裝好確認動作沒問題後，便借廠商提供的螢幕 3D 檔案設計了一個 3D 列印的外殼：

![Pi Dash 外殼設計 - 正面](https://static.driftking.tw/2024/06/37248013638d658eed26f0d887acb0df.png)

![Pi Dash 外殼設計 - 反面](https://static.driftking.tw/2024/06/d92f0b378faecce793549ecce3f0e845.png)

並且在側面做了一個帶燈電源的開關，這部份跟我之前的這篇文實作方式差不多：[替 Raspberry Pi 添加開關、外接電源燈、狀態燈](https://blog.driftking.tw/2019/11/%E6%9B%BFRaspberryPi%E6%B7%BB%E5%8A%A0%E9%96%8B%E9%97%9C_%E9%9B%BB%E6%BA%90%E7%87%88_%E7%A1%AC%E7%A2%9F%E7%87%88/)

風扇部分則是整合了之前這篇文的電路及控制腳本：[利用 Raspberry Pi 控制 PWM 風扇及轉速偵測](https://blog.driftking.tw/2019/11/Using-Raspberry-Pi-to-Control-a-PWM-Fan-and-Monitor-its-Speed/)

![Pi Dash 外殼設計 - 電源按鈕開孔](https://static.driftking.tw/2024/06/00216456f580a4531fa35c833f40e424.png)

大家如果眼力好可能會發現這是 Rev 2，沒錯！廠商提供的模型尺寸有點不準，所以搞了兩次才成功 😭

### Windows Hello 相機

上方相機的部分則是去買了個筆電相機模組來製作，外殼透過雷射切割壓克力堆疊製作：

![相機模組 + 壓克力外殼](https://static.driftking.tw/2024/06/638bd9d4c3738705d42ac8fb594c5c85.jpg)

並且相機透過這個支架固定在螢幕上方：

![Pi Dash 相機支架](https://static.driftking.tw/2024/06/86425adcf849c8f7bc6f3af4b61cfece.jpg)

![Pi Dash 相機支架(固定於外殼上)](https://static.driftking.tw/2024/06/650cb9b478f6108cf56dda6bece865d1.jpg)

### 整體腳架

本體完成後支架還需要做兩個讓螢幕能夠立起來的腳架，這邊透過 5mm 壓克力雷切完成並固定在轉接的 3D 列印件上：

![Pi Dash 背面/壓克力腳架](https://static.driftking.tw/2024/06/27152d86638d34c940969ad7010836bf.jpg)

最後整體的背面設計如下：

![Pi Dash 背面設計](https://static.driftking.tw/2024/06/a370a6ba2c461507cfa1986f7eb87a59.jpg)

因為是事後拍的照片，所以順序可能會有點亂還請見諒 XD

### Fan & Power HAT

後來有另外為這整個系統設計了一塊樹莓派擴充板，把開關/電源/風扇等電路整合在一起讓走線更為簡潔：

![Pi Dash Fan Power HAT 設計](https://static.driftking.tw/2024/06/e35aa8ac51270625ebd6b2b3073eddf5.png)

這邊值得一提的是由於我要使用 SSD 作為開機碟，並且螢幕本身及 Apple Watch 充電器也都是由 Pi 的 USB 口取電，但 Pi 的 USB 口總電流有著總共 1.2A 的限制，這導致使用 Watch 充電器時會造成系統不穩重開機 🫠 還好經過計算後其實電供的 3A 是足夠應付滿載電流的，所以設計了一個直插 USB 供電口在風扇旁邊，直接由電供供應（透過軟排線連接的位置），並插到 Pi 本身的 USB 孔上提高供電能力解決了這個問題。

### 開關/充電區

帶燈開關的部分很單純，焊上線連接到擴充板上的對應接口就行：

![帶燈電源開關/充電使用例](https://static.driftking.tw/2024/06/ea10359bf9686ad5c66ebc3a52237009.jpg)

Apple Watch 及支援 Magsafe 的 AirPods Pro 2 都可以透過這個充電器充電！

## 軟體設計

軟體的部分直接看成果吧，主要就是網頁開發跟一些後端處理，其實不太確定可以記錄些什麼，全部寫出來又太雜 XD

(Demo 圖片由於沒有連接 API 故資訊無法顯示，僅作參考)

![Web UI](https://static.driftking.tw/2024/06/41ef6f7d74e4d80c892a7fcaae3899a1.png)

簡單描述一下功能跟設計：
首先開機後自動啟動 Chromium Kiosk 模式，UI 部分主要分成四個區塊：

- 上方 Navbar 顯示一些基本資訊(樹莓派溫度等)，並保留分頁切換提供未來使用，右方則是各種樹莓派系統層面的操作，像是重啟/關閉螢幕等。
- 中間三大區塊設計為可以互換的卡片式 UI，可以按照我想要的新增不同的區塊提供顯示/操作功能。
  - 目前左區塊可以顯示時間日曆/天氣等相關資訊
  - 中間為主要操作互動區塊，透過傳送指令到 BetterTouchTool 實現一些自動操作或是音樂播放等功能，並顯示目前播放影音的相關資訊
  - 右側區塊則是顯示我自己一些 SNS 帳號相關的資訊，類似一些蠻流行的追蹤數計數器
  - 右側區塊還有個頁籤可以切換，目前是弄了個定時器

串接 BetterTouchTool http server 的部分，由於 https 自簽憑證已經不被 Chrome 認可一樣會跳不安全訊息，所以後來是在 Pi 上執行的 Node.js Server
建立 Proxy 來處理對 BTT server 的請求，然後 Node.js Server 再用 [mkcert](https://github.com/FiloSottile/mkcert) 做本地憑證，並且再設備上信任來避免不安全 https 的警告。
搞得有點複雜...... 不過應該是目前比較方便的做法（但好像還是會顯示不安全，至少不用手動繞過）。

另外在螢幕開關的部分也遇到了一些問題，Waveshare 官方是使用 `vcgencmd display_power 0` 的方式關閉螢幕，一開始沒問題，但在升級系統後就無法使用了，
後來在這邊找了方法實現螢幕開啟關閉的功能：[Turn Official Raspberry Pi 7" Touchscreen Display Off / On #1740](https://github.com/raspberrypi/firmware/issues/1740)

1. 啟動後執行(每次重啟系統都需要，才能夠透過遠端 ssh 控制) `export DISPLAY=:0.0 && xhost +`
2. 透過 `xrandr --output HDMI-1 --off` 關閉螢幕，或 `xrandr --output HDMI-1 --auto` 開啟螢幕

> 由於這塊螢幕預設是直向，我重啟螢幕後需要 `xrandr --output HDMI-1 --auto --rotate right` 來將螢幕旋轉到橫向

最後，搭配 PC 時也透過 LibreHardwareMonitor 提供的 HTTP Server 做了一個可以顯示系統基本資訊/溫度的區塊可以自由切換：

![PC Hardware Monitor](https://static.driftking.tw/2024/06/8cf179c26c56c311d7bd6ffd1df0fcea.jpg)

最後大功告成！（那個~~推特~~ X 的 API 改成要收錢了，就放著他壞掉懶得改了...）

![Pi Dash - Raspberry Pi Dashboard](https://static.driftking.tw/2024/06/76e853e176c597b1e52eb6b67b06e654.jpg)

其他我可能等想到有什麼比較有趣值得記錄的再補充了XD 或是會有特別想要瞭解的部分也可以留言討論看看。

以上，雖然東西有點雜也沒辦法全部記錄下來，但希望能拋磚引玉給有同樣想法的人一些啟發！Adios.

參考

- [Turn Official Raspberry Pi 7" Touchscreen Display Off / On #1740](https://github.com/raspberrypi/firmware/issues/1740)
