---
title: 利用 Raspberry Pi 搭建 TimeCapsule（仮）- 硬體篇
tags:
  - MacOS
  - RaspberryPi/樹莓派
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
  - 時光機備份
abbrlink: ca77c25e
date: 2019-11-12 21:25:00
---

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573533117/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191112_122944.jpg)

前陣子看到國外有人把舊的 Mac Mini 的機箱拿來改造，弄成 Router 或 HTPC 等，不得不說 Mac 的全鋁一體機箱真的挺好看，雖然設計已近十年未改，但放在 2019 年的桌面上仍不顯老氣 ~~反正中間那個商標放哪都能裝逼~~。<!--more-->

看著別人改的成品很是心動~~手癢~~，於是上網搜了下，發現有不少人在賣二手的機殼，在國外看到了個價格及品相都非常好的機殼(約NT600)就買了下來(Mid 2010/A1347)。時間上正好 Raspberry Pi 更新了第四代，I/O效能上也終於符合了基本 NAS 的需求，於是想到可以把 Pi 裝在 Mac Mini 的機殼中，來做個簡易的偽 TimeCapsule + NAS，來當做無線 Time Machine 備份用及網路存儲設備。

本篇就來記錄一些想法跟製作過程，以及碰到的問題。 關於 Pi 4 的軟體設定部分，為了版面簡潔我把它寫在另外的兩篇，這裡就純記錄硬體部分：[利用 Raspberry Pi 搭建 TimeCapsule（仮）- 軟體篇](https://blog.driftkingtw.me/archives/613e9fa3.html)、[利用 Raspberry Pi 搭建 NAS](https://blog.driftkingtw.me/archives/c138150d.html)

# 想法 && 設計

![收到的機殼，品相良好，只有一個非常不顯眼的小喀傷](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573474005/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20190924_200321.jpg)

首先依照 Mac Mini 機殼的容量，大約能放下 17*17cm 的主板（ 網路上大部分是拿 Thin Mini-ITX來裝 ），高不能超過 2.5cm ，約兩個 USB Type-A 堆疊的高度，那當然我用的是更小的 Pi，只要另外考慮硬碟的擺放及走線即可。

以下是一開始的設計圖（ 後來 Hub 的設計被廢掉了，詳細問題有記錄在下方遇到的問題裡 ）

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573474811/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_D5FEECE2800F-1.jpg)

接著得想個辦法固定這堆玩意，最省心的方法大概就是直接做個絕緣塞進去，但是這樣實在太醜了 ~~用 Mac 機殼不就是要好看~~，而且不知道時間久了會發生什麼問題，Server 是打算 7x24 不關機的，考量到散熱及穩定性 ~~還有不想要裡面太醜的強迫症~~，最後還是乖乖畫個 3D 設計稿然後找代印來做固定支撐的部分。

然而這才是麻煩的開始，由於上網翻不到 Mac Mini Case 的機械圖，也沒有人分享螺絲孔距等數據，只好自力救濟徒手量測。單純用直尺量測實在很麻煩，空間不大遊標卡尺也不方便，只好先量兩孔間直角三角形的兩邊大概長度，再對照直線距離用畢式定理去推算量出來的距離對不對（感覺挺蠢的但是也想不到其他方法），才勉強量出了孔距。

幸好最後印出來是吻合的沒問題（孔徑我開2mm，距離是以孔心為基準），這邊放個測量結果，有需要的人可以少走歪路（直接看垂直的距離就好，直線距離可能稍微跑掉）。

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573476073/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_B9730CE991B7-1.jpg)

再來是比較棘手的硬碟支架部分，原本看上蘋果原廠自己的就支援了雙硬碟（Server版本、無光碟機版之類，記得到 2014 機箱都是 A1347），我買的機殼並沒有附送，硬碟架比較難找，後來看看只有 eBay 上有價格可以也有貨的。

不過原廠的支架在不破壞結構的前提下可能要另外製作特殊的 Sata 7+15 Pin 延長線才有辦法塞雙硬碟，而且不幸的是我沒仔細看規格，以致於買回來的支架其實是有光碟機版本（那賣家圖片放成雙硬碟位的...我就傻傻相信了 ｡ﾟヽ(ﾟ´Д`)ﾉﾟ｡ ）最後只能認命自己列印硬碟架了，原廠硬碟架再另外售出給剛好需要的人。

![買錯的支架](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573476669/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191102_152723.jpg)

原本硬碟支架的設計圖，後來發現樹脂這個厚度不太穩，裡面的卡楯點又沒量準所以把旁邊的固定臂鋸掉了，只留下中間支撐硬碟的部分：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573488724/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/HDDMount.png)

最麻煩的底部固定螺絲部分，我參考了 [ifixit](https://www.ifixit.com/Teardown/Mac+Mini+Mid+2010+Teardown/3094) 的各種拆解，都只提到底座的螺絲是 " 7.9mm Torx 6 Screw "，完全找不到有人提到直徑是多少... 在完全缺乏資訊的狀況下，我找了幾個螺絲試鎖，有一個能夠鎖進去鎖緊，但是太短了。

由於沒有遊標卡尺所以只好買了 M1.4、M1.5、M1.6 的回來測試，薑薑！全錯，最後只能認命買了遊標卡尺回來測量。正確尺寸應該是 M1.7，因為是鎖金屬殼所以用自攻螺絲，更詳細的我就不太確定了（手邊工具無法量測），至少 M1.7 自攻是鎖的進去而且穩定，一樣有需要的人可以再少走一條歪路了 ｡ﾟヽ(ﾟ´Д`)ﾉﾟ｡

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573487949/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191111_211918.jpg)

GPIO 旁邊設計了擋板以免其他線材觸碰到：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573488945/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/PiMountFront.png)

背面加上了一些墊高用的腳腳：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573488967/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/PiMountBack.png)

再來要考慮的是擋板（外框這裡是購買別人設計好的現成可以裝 Mini-ITX 擋板的塑膠框），稍微在腦中模擬了一下走線，考慮美觀的情況希望 USB-C 電源跟網路口要在一起，所以開關放左邊（以底蓋朝上的方向為基準），電源跟網路放右邊，靠樹莓派那測設計了一些出風口增加散熱能力，中間也開了一段方便進風。

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573489134/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IOPanel.png)

中間兩個突起的三角塊原本是打算跟底板用機械螺絲固定，不過尺寸沒量好，後來直接靠網路口鎖螺絲上去固定住IO擋板，Pi 固定底座原本要固定擋板的部分就鋸掉。

最後按照官方的 Pi 4 機械繪圖來做風扇支架：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573489312/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/FanMount.png)

 這次用到的 3D 列印的檔案在最下面有連結，歡迎自行免費取用，但請不要拿去用於商業用途。
 
 接下來就是把東西準備好開始裝機跟佈線囉 (ﾟ∀ﾟ)

# 製作過程

這是拿到 3D 列印成品的照片，接著要把硬碟架兩側的腳跟後面量錯的定位口弄掉，然後打磨後上漆：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573489504/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191105_133114.jpg)

IO擋板的部分我也另外作了處理，首先是 Type-C 我測量成公頭的尺寸，所以用銼刀擴孔後母座才放的進去，以及電源開關的部分尺寸剛好，稍微擴孔後才能順暢的按壓。

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573489722/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191105_144436.jpg)

上圖是處理過的硬碟支架，有個三角缺口是鋸的時候可能力道不平衡 (((ﾟДﾟ;)))，不過放在裡面不太影響，把尖銳的部分打磨即可。

噴漆灌上漆（可以看到上面的橫條可能太細，樹脂強度不夠，不過主結構沒問題）：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573489873/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191106_135017.jpg)

Pi 支架的部分，設計的時候忘記了 Micro SD 會突出一些，所以只好擴一些空間。不過弄的時候樹脂不夠強不小心弄斷了，還好螺絲兩邊都會鎖到，所以補強一下沒問題：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573489870/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191106_131709.jpg)

原本要使用 Hub 來管理硬碟，不過遇到了些問題，索性把 USB to SATA 的轉接線拆了，把供電的部分另外拉出來（原 USB 線與 PCB 的 Vbus 供電要隔離）：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573490103/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191107_115612.jpg)

上蓋就不蓋回去了，用抗靜電耐熱膠帶貼著，可以降低厚度

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573490109/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191107_151151.jpg)

每個步驟都要先用電錶量過確認，然後上機測試：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573490260/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191107_121531.jpg)

裝上硬碟架，測試高度厚度，然後假組確認尺寸跟線材長度有沒有問題：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573490360/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191107_152116.jpg)

（有在硬碟上下加上 1mm 的抗靜電棉，減少震動跟防止靜電，硬碟上在仿原廠貼導電織布與機殼接觸）

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573490432/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191107_155424.jpg)

接著把兩個硬碟的電源線接上 Type-C 座，然後拉另一條給 Pi 供電，這邊使用了 5A 的線材所以有點粗，費了一番功夫才焊好（ Type-C 還真不適合手焊粗線材，後來我還有另外從兩個 CC Pin 拉出杜邦母頭，可以加上兩個 5k1 電阻讓 Pi4 支援 E-mark 線材

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573490576/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191107_214430.jpg)

帶燈開關的部分，跟機殼前方的 LED 並聯，並各串上 1k 電阻（亮度我不希望太亮），關於添加燈以及風扇控制等方法請參考另一篇文章:[替 Raspberry Pi 添加開關、外接電源燈、狀態燈，以及風扇控制](https://blog.driftkingtw.me/archives/59bd89a7.html)

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573490776/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191108_215111.jpg)

裸機測試，確定沒問題再裝機：

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573490872/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191108_223930.jpg)

面板我沒想到很好的固定方法，除了網路口用螺絲外，電源跟開關都是用熱溶膠 + 防靜電膠帶弄上的，這部分設計上還有改進空間。

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573490996/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191111_221457.jpg)

最後完整裝機，鎖上螺絲！

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573532268/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191112_120240.jpg)

關上背蓋（有點清潔時候的水漬還沒乾XD）

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573532315/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191112_120504.jpg)

完成！來幾張照片ヽ(✿ﾟ▽ﾟ)ノ

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573533117/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191112_122944.jpg)

屁屁

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573533113/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191112_121231.jpg)

Bay ~

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573533114/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191112_122934.jpg)

~~好吧外觀其實看起來就是個普通 Mac Mini~~

PS 風扇啟動時（無散熱片）溫度可以維持在不超過 50°C （室溫約 26~28°C），散熱效果還算不錯。

# 遇到的問題 && 解決方法

### 機箱固定螺絲問題

如上述，我用的底座機箱固定用螺絲規格為 M1.7 x 6 自攻螺絲 + M2 華司。

### USB 外部供電 Hub 問題

Pi 4 除了 USB-C 設計會 [使 E-mark 線材無法使用之外](https://blog.driftkingtw.me/archives/b2345c23.html)，還有個問題是：使用帶獨立電源的 USB 3 Hub 在接上 Pi 的 USB 3 口上可能會導致開機或重啟時，無法進入 boot 程序。官方論壇也有人提出這個問題 [Pi 4 fails to boot when active USB switch attached](https://www.raspberrypi.org/forums/viewtopic.php?t=244900) ，雖然可以啟動時拔起 Hub 再插回去正常運作，但是麻煩而且裝箱後不可能重啟又打開插拔。

大致推測下來，可能是 Pi 4 使用 USB 3 時，有獨立供電的 Hub 會反向供電（Power feedback）給 Pi，於是可以考慮將 Hub 與 Pi 之間的 Vbus 隔離，但我的 Orico Hub 仍然無法正常啟動（有些人回報切斷 Vbus 後就正常運作），論壇裡也有人和我一樣切 Vbus 仍然失敗的，韌體等更新也測試過了無法。

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573528606/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191106_221851.jpg)

最後只好改成直接把 SATA to USB 轉接線的電源拉出來做使用，目前解決了無法啟動的問題。目前只有少數 Hub 有辦法正常運作（通常比較貴，可能 USB 線那有做二極體防止反向供電？）選購時要注意一下。

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1573490109/blog/2019/11/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E7%A1%AC%E9%AB%94%E7%AF%87/IMG_20191107_151151.jpg)

### 2 個 HDD 直接用樹莓派官方電供供電？

從文中可以看出其實我 2 HDD 的電源也是直接接上與 Pi 同一個電源的，那為何要多此一舉把電源獨立拉出來（總電流還是只有 3A）？主要是因為兩個 2.5" HDD + Pi 本身的電流，3A 是足夠驅動的，不過 Pi 的 USB downstream 限制在 1.2A （可以理解為四個 USB 口只能消耗最高 1.2A ），雖然硬碟上標示 0.55A ，但還要考慮到轉接線的耗電。實際測試後單顆 2.5" HDD 啟動電流約為 0.7~0.8A 左右，Pi 本身耗電約 0.6~1.2A 之間。

最後的解決方法就是必須使用外接的 Hub 確保兩個 HDD 啟動電流足夠，繞過 USB downstream 1.2A 的限制（但是可以接同一個 3A 電源沒問題），或是如上把轉接線那電源供應獨立接出來。至於直接接上兩個 HDD 也是有實際實驗過了，真的沒辦法，其中一個硬碟會無法掛載 ╮(╯_╰)╭

以上大概就是這次踩到比較大的坑，以及解決方法，也想把 Pi 裝在 Mac Mini 機箱的朋友可以參考。

# 材料清單

- 1 x Pi 4 4GB
- 1 x 8G MicroSD Card for boot
- 1 x A1347 Apple Mac mini Case (Mid 2010)
- 2 x 2.5" HDD
- 2 x SATA to USB3 Cable
- STL 3D Printed Mount / IO Panel
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

# 本次所使用的 3D 檔案（STL）

[PiMacMiniCaseSTLFiles](https://drive.google.com/open?id=1iUhxCqxGfxcIEYv6l-oQD4FoOvjMj0Gv)

注意，目前還沒優化檔案，所以硬碟支架及 Pi 底座有瑕疵的部分需要的話可能還要自行調整或印出來後自行切割多餘的部分，孔位是沒問題的。

# 參考

[Mac Mini Mid 2010 Teardown](https://www.ifixit.com/Teardown/Mac+Mini+Mid+2010+Teardown/3094)

[Raspberry Pi GPIO Pinout](https://pinout.xyz/)

[Raspberry Pi 4 Tech Specs](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/specifications/)

[Power Supply](https://www.raspberrypi.org/documentation/hardware/raspberrypi/power/README.md)

[Pi 4 fails to boot when active USB switch attached](https://www.raspberrypi.org/forums/viewtopic.php?t=244900)

