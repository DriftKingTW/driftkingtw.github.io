---
title: DIY IR Tracker 紅外線頭戴追蹤器筆記
tags:
  - DIY
  - SIM-Racing
keywords:
  - IR
  - Tracker
  - 紅外線
  - 頭戴追蹤
abbrlink: e0d62e9c
date: 2018-03-20 23:05:00
---

![Project CARS 2](https://static.driftking.tw/2024/06/d09e85e80aed5f40c462f1451113d335.jpg)

用VR玩模擬駕駛之類的遊戲是很多玩家的夢想, 不過目前VR頭戴顯示器還不算相當成熟, 而且價格又偏高的情況下<del>(對拉這才是重點)</del>, 不過<del>窮</del>人是很聰明的, 沒有VR我們當然有便宜的替代品!
那就是IR Tracker(紅外線追蹤器, Infrared:紅外線縮寫為IR), 可以搭配[OpenTrack](https://github.com/geolink/opentracker)等開源軟體, 利用IR攝影機拍攝IR LED做定位, 來達到偵測物體移動的功能
材料買下來成本差不多可以控制在NTD500以內, 當然網路上也有賣現成的, 不過相對價格都比較高, 當然是選擇DIY既便宜又不失樂趣!<!--more-->

# Ready

<strong>所需材料</strong>

* PSEYE 攝影機 x 1 (注意!市面上有兩種版本, 我會在步驟中提及其中的差異)
* IR LED x 3 (推薦SFH485P, 亮度高效果較佳)
* 6.8Ω 1/4W電阻 x 1 (串聯5V, 每顆LED電流約75mA) 這邊可以用[參考阻值計算機](http://www.free-track.net/english/hardware/calcled/)計算
* 5V電源(電池、USB, etc)
* 磁碟片一張 <del>就是那個存檔圖案的實體拉</del>

<strong>所需工具</strong>

* 烙鐵
* 焊錫
* 一些連接用線材
* 熱縮套/絕緣膠帶

# Let's Do It!

## PSEYE

首先處理PSEYE, 也就是我們的IR攝影機, 他原本只是個普通的攝影機, 但是我們可以手動拆除IR濾鏡改造成IR攝影機
小科普:CMOS感光元件對於IR的敏感度很高, 所以普通相機都會裝上IR濾鏡濾除大多數的紅外線
(不是完全濾除, 所以可以利用手機鏡頭等去照IR遙控器, 就能夠透過相機看到IR了!)
至於選PSEYE而不是其他WebCam的原因, 主要就是因為PSEYE的幀率可以高達100以上, 對於移動是非常靈敏的
做IR Traceker也不需要很高的解析度, 有480P就已經綽綽有餘了, PSEYE價格也不高, 所以當然是首選囉!
用其他的攝影機當然也可以, 原理都是一樣的, 只是大部分都會推薦PSEYE

## PSEYE比較

不過問題來了, 在準備部分提及的有兩種PSEYE, 一種是能夠拆除IR濾鏡一種無法(放在夾層中)

![PSEYE比較](https://static.driftking.tw/2024/06/f068d09a23e7c9682c7aa2b1a5d8100f.jpg)

從上圖中可以看到左邊的那種(應該是較新款), 鏡頭中有白圈的是無法拆除IR濾鏡的!
不過不必太過擔心, 因為功能上基本也是沒問題的, 只是條件許可下最好能選到右邊這種, 能達到最好的效果
(說真的我覺得使用上沒差多少就是)

## 拆解

首先先把PSEYE後面的墊片拆起(新款的是軟墊, 舊款的是硬的要注意)
由於舊款是塑膠墊片, 所以我直接用手鑽把它鑽出來(注意不要鑽到底下的螺絲)

![PSEYE背面](https://static.driftking.tw/2024/06/a3136d73015c895430e0986fc25a4d51.jpg)

![拆墊片](https://static.driftking.tw/2024/06/5ecd6a96d7d014f83d908bf815e3545d.jpg)

接著把螺絲轉開, 然後沿著邊邊撬開後蓋(非常...難拆, 尤其到圓球那部分要很用力地把它暴力扯開)

![撬開後蓋](https://static.driftking.tw/2024/06/20cfe80deb3654d7ad5243fc01711966.jpg)

小心拆解過程不要傷到手跟內部的PCB

![拆開後](https://static.driftking.tw/2024/06/104a7507a626ecbece57e111a14cfc51.jpg)

接下來比較輕鬆, 把PCB上的螺絲都轉下來, 分離下方的支架, 並且拆下前鏡頭

![PCB與鏡頭](https://static.driftking.tw/2024/06/3c71ad8ad81a1bb0bb856ea2faf4fdfa.jpg)

接下來拆解IR濾鏡, 如果是買到無法拆IR濾鏡的可以直接[跳到這](#過濾可見光)繼續

## 拆解IR Filter

這部分需要比較大的耐心, <del>除了拆背蓋在這裡也會有種讓人想去買成品的衝動</del>

![IR Filter](https://static.driftking.tw/2024/06/37f75a26fb63f5f426577d3781e67758.jpg)

上圖中最外層的圓形玻璃小透鏡就是IR濾鏡, 我們要將它拆下才能達到最好的IR感測效果
可以拿小刀或是像我沒耐心直接手鑽往旁邊鑽, 不過不管怎樣都要很小心而且要花些時間
至於多難拆, 體驗一下就知道了(笑)

![拆除IR濾鏡](https://static.driftking.tw/2024/06/30098f072374801e08189976db0ba41d.jpg)

成功拆除!

## 過濾可見光

接著我們拿出磁碟片, <del>然後把它給拆爆</del>溫柔地拆開

![磁碟片](https://static.driftking.tw/2024/06/9bee728f5f5318afed337cdbd77648d4.jpg)

拿出剪刀, 把磁片剪成跟鏡頭那塊方形差不多的大小並放上去

![過濾可見光](https://static.driftking.tw/2024/06/07063f67a1f8becb8551d18b61845d6a.jpg)

## 還原PSEYE

好了, 此時完成IR攝影機的改裝, 現在按著步驟反向裝回去原本的樣子
先丟一邊, 開始做IR Tracker的本體 <del>可以順便通個電看剛才有沒有把它毀了</del>

![PSEYE](https://static.driftking.tw/2024/06/450e1e8615ed6ccbc70817cf87819462.jpg)

## IR Tracker

先來處理電路的部分, 電路很簡單就是單純串聯 5V->電阻->LED->LED->LED->GND

![電路圖](https://static.driftking.tw/2024/06/bbc1907af27d47d0c58943cd532eb448.png)

電源看想用電池或USB供電都可以(打算之後要用小型鋰聚電池供電, 目前先接USB)

![電路完成](https://static.driftking.tw/2024/06/eac97e9a1a807ead35623b1d9f6713f4.jpg)

此時我們要先準備個支架, 我直接用了YouTuber [Seytonic](https://www.youtube.com/channel/UCW6xlqxSY3gGur4PkGPEUeA) (推薦去逛逛, 他的頻道有許多好玩的東西) 提供的[版型](http://ge.tt/7e7RQJn2)去修改成3D列印檔案, 然後請人列印出來
可以順便按照個人喜好在上面做裝飾, 這時候當然貼上Carbon紋路<del>超輕量化</del>

![支架](https://static.driftking.tw/2024/06/5a65998e6f3866175bb753fd1dd42c18.jpg)

接著看要如何安裝固定, 我個人是綁在耳機上, 如圖(歪歪的沒關係 因為軟體可以校正)

![固定](https://static.driftking.tw/2024/06/148906844a600e4db6885280af1b55a3.jpg)

## 軟體設定

軟體的部分首先要下載[CL-EYE DRIVER](https://codelaboratories.com/downloads/), 連結是Donate版, 免費版可以自己搜尋, 畢竟也不貴應該支持一下開發者
安裝過程就是不斷的下一步就可以, 不多做說明, 裝好後可以開啟"CL-EYE TEST"這個程式看看有沒有正常(已經改裝好攝影機的話應該看不太到可見光, 所以可以拿剛才做好的頭戴追蹤器或遙控器之類對著鏡頭測試)

![測試](https://static.driftking.tw/2024/06/ec754bb6486b5d701a1237c5fffa63f2.png)

如上圖有看到亮點就代表驅動跟硬體都正常囉!
<b>注意!如果覺得亮點很模糊, 那是因為拔除IR濾鏡之後造成焦距改變, 這時候我們可以藉由轉動鏡頭調整卡在適合的焦距上
另外也可以進入裝置的屬性設定裡調整曝光等參數, 我一開始身邊找不到磁碟片就是靠軟體改變曝光, 同樣也能夠正常使用哦!</b>

接著安裝[OpenTrack](https://github.com/opentrack/opentrack/releases), 選擇最新版下載後一樣是無腦下一步安裝

![OpenTrack](https://static.driftking.tw/2024/06/4057f0736024cceaa4dabb9231828315.png)

接著開啟Input那欄右側的設定圖案

![Input設定](https://static.driftking.tw/2024/06/c7c18b55985421b45b6ba13fbb0a3123.png)

Device裝置的部分選擇你的攝影機(PSEYE), 下方可以調整解析度和FPS等(FPS盡量拉高)
然後開啟右邊的設定對話框, 選擇Shortcuts快捷鍵, 在Center項目設定一個按鍵讓你可以在遊戲/模擬器中重新校正置中

![Shortcuts](https://static.driftking.tw/2024/06/da2f4f6deed507f898472166960c877b.png)

接著切換到Output, 在這裡可以選擇要啟動的感測項目Yaw(偏擺), Pitch(俯仰), Roll(翻滾), XYZ(XYZ軸移動)

![](https://static.driftking.tw/2024/06/7bd485e3ce4c5b60e5384a743e2aaac5.png)

像是我開歐卡或賽車等, 只希望左右轉頭時候能看到左右的景物, 所以就單純開啟Yaw其他設定Disabled
如果像Warthunder要看四面八方, 就需要開其他的選項, 這部分就看個人需求了

設定完成按下OK回到主畫面, 然後按Start就會啟動OpenTrack囉!此時可以看到三個IR LED的光點正確地出現在方框中
只要做出動作旁邊的小章魚就會跟著做相同的動作

![](https://static.driftking.tw/2024/06/428777d56ea2d71b94d0e778f477a081.png)

接著就可以開啟遊戲, <del>體驗窮人的半VR拉</del>
歐卡之類可以直接支援, 像Project CARS 2的話要開Steam VR模式才有用哦! 每個遊戲可能要自己另外注意一下
雖然說價格不貴, 但是效果的確不錯呢!原先以為只有頭部轉動螢幕沒有跟上會很難用, 結果是多操心了
基本上可以很自然地看向左右兩側, 也沒什麼延遲, 前面的辛苦值得啦!

## 參考資料

[$10 Gaming Head Tracker | DIY Tutorial](https://www.youtube.com/watch?v=M_VN4mMA6Cw)
[CL-EYE DRIVER](https://codelaboratories.com/downloads/)
[OpenTrack](https://github.com/opentrack)
[Easy build setup with SFH485P, Anyone can build this ! ](http://www.free-track.net/forum/index.php?showtopic=1856)
[LED Assembly Wizard](http://www.free-track.net/english/hardware/calcled/)