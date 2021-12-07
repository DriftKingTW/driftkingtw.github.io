---
title: 找不到喜歡的單手鍵盤？那就自己做阿！
tags:
  - Keyboard
  - DIY
  - iPad
keywords:
  - DIY
  - Keyboard
  - TinyPICO
  - Marco Pad
abbrlink: f7332e8c
date: 2021-07-13 00:00:00
---

![Keypad Using with iPad](https://res.cloudinary.com/driftkingtw/image/upload/q_auto:eco/v1626107749/blog/2021/07/IMG_2151.jpg)

在這繪圖螢幕入門門檻越來越低及iPad逐漸成為專業繪圖板的時代，市面上也跟著出現不少主打創作者/遊戲用的單手鍵盤或是快捷操作裝置。但是等了好幾年了，還是找不到「適合」自己的鍵盤 🤨 那麼，與其繼續等下去不如自幹一個適合自己的單手鍵盤吧！

<!--more-->

# 動機 & 需求

分享下想做這個鍵盤的原因，如果沒興趣可以跳到 [鍵盤設計](#鍵盤設計)

也不是說市面上沒一個好用的鍵盤，但我不知道是比較龜毛還是廠商單純想賺錢，有不少功能是在贊助提案階段就許多人提出的意見，說個下版本改進但實際上市之後下一代完全就沒消息了。

對，我就是說藍芽。桌機+繪圖螢幕可能還好，那麼多人用平板畫圖有誰會想在平板上接個有線鍵盤嗎 XD，在這連一般機械鍵盤都越來越多轉向無線的趨勢下居然沒一家單手鍵盤/控制器願意做成無線的 🤯

而且我想要的功能其實也沒那麼複雜，列幾點參考：

- 藍芽連接
- 機械式(不喜歡矮軸)
- Type - C 接口
- 按鍵做到TGB那行，帶1.5u以上的Modifier鍵
- 可自定義鍵位/巨集/Layer
- 旋鈕(可有可無，個人使用並不太常需要)

應該也不是太難或多稀有的需求吧 🥺 以下來就開始來聊聊設計跟製作的過程給大家參考，或許你/妳會有更好的點子或想法也歡迎跟我分享，Let's get strated.

# 鍵盤設計

一開始是有考慮純手焊鍵盤，不過相對的麻煩，而且對於整齊度還是有點強迫症。最後我還是決定製作 PCB 鍵盤，雖然從沒設計過 PCB 不過對於電路有點基本的認識所以製作起來是沒什麼太大困難的，而且鍵盤電路也相當簡單，就算是不熟悉的朋友如果想做也不必太擔心 😎

雖然是單手鍵盤但它本質上就是鍵盤，所以設計方式跟一般鍵盤是一樣的！在做了一番功課之後，我找到了 [Keyboard Layout Editor](http://www.keyboard-layout-editor.com/) 這個工具，可以先在這網頁上畫出自己所需要的鍵盤 Layout 跟一些基本參數設定，這部分我認為操作已經相當直觀就不深入贅述佔版面了，需要的話可以參考我這次的 [設計圖](http://www.keyboard-layout-editor.com/#/gists/ae0e1241eea27b182c199b1275c0703b) 。

![Layout Design](https://res.cloudinary.com/driftkingtw/image/upload/q_auto:eco/v1626102954/blog/2021/07/Screen_Shot_2021-07-12_at_11.13.33_PM.png)

如果是鍵盤玩家應該看得出來我是從 ErgoDox 的設計下去做改變的，不過這邊有個小提醒記得要考慮好鍵帽的問題XD 否則到時候找適合的鍵帽會找的很辛苦 😅 (原本喜歡原廠或OEM高度的，後來發現只能挑DSA)

另外其實我自己也沒打過 ErgoDox ，就抱持著反正第一次應該會失敗的心情下去弄了，不過後來成品還算能用就是。

完成好鍵位設計之後可以把得到的 JSON 資料導入到 [Plate & Case Builder](http://builder.swillkb.com/) 這個網站產生 CAD 檔案了，好像還有提供外殼與訂位板的訂購有需要的話可以參考看看，我這次是沒用到定位板，外殼打算用 3D 列印。

![CAD File Generate from Builder](https://res.cloudinary.com/driftkingtw/image/upload/q_auto:eco/v1626103663/blog/2021/07/Screen_Shot_2021-07-12_at_11.26.48_PM.png)

完成後就可以進到下個步驟，開始設計 PCB 了！

我選擇的是 EasyEDA ，這部分就看個人喜好跟習慣了，走線部分我也不是專業的所以建議大家可以去找網路上的各種文件跟教學，注意下不要犯常犯的錯跟照著軟體的建議走基本上就不會有問題了 💪

另外我這次使用的開發板是由 [Unexpected Maker](https://www.youtube.com/c/unexpectedmaker) 所做的 [TinyPICO](https://www.tinypico.com/) ，這是一塊 ESP32 開發板，選擇這塊板子的原因有以下幾點：

1. 內建藍芽，不用另外煩惱藍芽模組
2. 體積非常小巧，未來要做成搖桿型的控制器也沒問題
3. 內建電池管理晶片，省去電源電路的設計
4. Type-C! Type-C! Type-C! 
5. 整體消耗跟睡眠能耗在 ESP32 中相對好（當然比不過 Pro Micro 那種好就是）

這幾點正好適合這次的專案 🥰 ，缺點大概就是 [QMK](https://docs.qmk.fm/#/) 不支援 ESP32（有人 Fork 出來不過我還沒搞成功）變成軟體要自己寫就是，幸好 ESP32 支援 Arduino Library 應該不會太麻煩，還有功耗上可能比較不理想，~~沒關係自幹的話無限加大電池就好了。~~

以下是電路圖設計好的樣子，旋鈕我自己不急所以打算下一版再加(有的話)，另外有留個 0.96 吋 OLED 的位置跟走線可以擴充：

![Sheet](https://res.cloudinary.com/driftkingtw/image/upload/q_auto:eco/v1626104478/blog/2021/07/Screen_Shot_2021-07-12_at_11.40.42_PM.png)

GPIO 不足的部分使用陣列輕鬆解決，鍵盤陣列詳細的解說網路上資源蠻多了就不再贅述，大致上就是透過縱橫的開關去定位哪個按鍵是觸發的狀態。

接著畫好後就是 PCB 走線了，第一次畫的不怎麼樣，獻醜了：

![PCB](https://res.cloudinary.com/driftkingtw/image/upload/q_auto:eco/v1626104789/blog/2021/07/Screen_Shot_2021-07-12_at_11.46.14_PM.png)

各種元件的部分可以善用社群提供的資源直接使用，才不用自己還要找規格書刻尺寸喔～

接著找個 PCB 廠商印刷即可，我找的是 [JLCPCB](https://jlcpcb.com/) (非業配)，雖然我知道他們在 Youtube 上跟另一家 PCBway 廣告都打的很兇，~~沒錯我就是看別人業配不小心上鉤的 😏~~

不過第一次印起來是真的挺新鮮的，廠商每個處理細節都會有記錄，可以上去看看板子是在鑽洞阿上 Silkscreen 上錫之類的流程一清二楚很有趣 🤯 速度跟價格也都蠻 OK ，不過我對黑色 PCB 情有獨鍾所以會比綠色板子多等一些時間就是（約五天），印了5張黑色、HASL(with lead) 表面處理、尺寸在 \\(150mm * 130mm\\) 左右的板子價格約 12 USD （不含運），給大家參考個。

![PCB Progress](https://res.cloudinary.com/driftkingtw/image/upload/q_auto:eco/v1626105738/blog/2021/07/Screen_Shot_2021-07-13_at_12.02.04_AM.png)

# 鍵盤製作

接著就是等各種零件跟 PCB 到了開始組裝製作了！這邊列下大致用到的零件，工具那些基本烙鐵、螺絲起子之類的就不提了：

- PCB
- 五腳紅軸 x 40 (沒定位板建議五腳比較穩)
- ErgoDox DSA鍵帽一套
- 1.25u DSA增補
- PCB衛星軸 300 兩套
- 0.91" OLED 螢幕
- TinyPICO ESP32開發板
- M3 螺絲數個
- 1N4148 二極體 x 40
- 鋰聚合物電池，這次使用的容量為 4000mah

另外在等待的期間，可以開始來設計 3D 列印外殼的檔案了，因為算是測試版本所以沒有設計上蓋，大概長這個樣子：

![Case Design](https://res.cloudinary.com/driftkingtw/image/upload/q_auto:eco/v1626107046/blog/2021/07/Screen_Shot_2021-07-13_at_12.23.36_AM.png)

btw 軟體是 Autodesk Fusion 360，個人使用是免費的。

等了一週東西都來了，來看下PCB 完成的樣子：

![PCB Front](https://res.cloudinary.com/driftkingtw/image/upload/q_auto:eco/v1626106448/blog/2021/07/IMG_2147.jpg)

![PCB Back](https://res.cloudinary.com/driftkingtw/image/upload/q_auto:eco/v1626106448/blog/2021/07/IMG_2146.jpg)

另外我原本以為是亮黑色，因為沒有消光的選項，結果居然是我最想要的消光黑太美了 🥺🥺🥺

組裝的部分很簡單就插上去焊一焊衛星軸裝一裝就好，不過外殼設計部分有點失算 🥶，距離有點沒抓好所以後來得磨掉側邊一些才裝的進去，加上高度也有點問題後來乾脆加個螺帽墊高卡上去 🥲，下一次印的時候再改善吧，或許接著可以玩玩看 CNC 銑個鋁製的外殼。

![Finish Keypad](https://res.cloudinary.com/driftkingtw/image/upload/q_auto:eco/v1626107749/blog/2021/07/IMG_2148.jpg)

搭配 iPad 使用的情境（iPad 的手托板可以參考 [如何讓 iPad 成為一個專業的畫板](https://blog.driftking.tw/2021/03/Make-an-iPad-Drawing-Board-That-Improve-Your%20Drawing-Experience/)）：

![Using with iPad](https://res.cloudinary.com/driftkingtw/image/upload/q_auto:eco/v1626107749/blog/2021/07/IMG_2151.jpg)

到這部分鍵盤基本功能都算完成了，可喜可賀！不過外殼設計失誤導致鍵盤高度變高，可能要用手托會比較舒服 😭

等等，好像還漏了重要的部分？

軟體嘞 💩

其實軟體部分在拿到開發板跟設計 PCB 前就開始做撰寫跟測試了，但是因為功能上還沒完全達到自己一開始所需要的功能跟規劃，目前只實現了無線連接、自定不同層級的各種按鍵、螢幕顯示連線狀態及所按按鍵等，巨集也尚未完成，計劃到時候比較完善時再另外寫一篇軟體開發的分享。不過現在至少能正常使用，雖然目前的 Code 還挺亂就是XD 有興趣的可以參考 Github 的 WIP Code [DriftKingTW/TinyPICO-BLE-Marco-Pad](https://github.com/DriftKingTW/TinyPICO-BLE-Marco-Pad)

另外做個 Todo 好了，順便勾一下目前有做到的功能：

- [x] 藍芽連接
- [x] 機械式
- [x] Type - C 接口
- [x] 按鍵做到TGB那行，帶1.5u以上的Modifier鍵
- [ ] 螢幕擴充支援（目前做半套）
- [ ] 可自定義鍵位/巨集/Layer（目前做半套）
- [ ] 旋鈕（預計下個版本支援）

未來還想用 TinyPICO 再做一個類似 Joycon 那種搖桿的快捷手把，在外出或比較擁擠的地方應該挺適合也方便拿在手上，有機會再來試試看。

那這次簡單的設計跟製作跟大家分享到這，感謝你/妳的閱讀，那我們下次見 🖐

# 參考

[TinyPICO](https://www.tinypico.com/)
[swillkb Plate & Case Builder](http://builder.swillkb.com/)
[keyboard-layout-editor.com](http://www.keyboard-layout-editor.com/)
