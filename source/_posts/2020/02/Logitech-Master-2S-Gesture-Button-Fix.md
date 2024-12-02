---
title: 羅技 Master 2S 拇指鍵維修筆記
tags:
  - DIY
  - 維修筆記
keywords:
  - Logitech Master
  - 維修
abbrlink: 59ef095c
date: 2020-02-09 23:48:17
---

![](https://static.driftking.tw/2024/06/9009f1d2818a90e0c6d0f1ea899ae3a6.jpg)

前陣子我的滑鼠：Logitech Master 2S 左邊的拇指鍵突然按下之後卡住了，連帶的整個滑鼠都沒辦法使用（包括去 Logitech Option 裡面把拇指鍵禁用都無效），看了下保固發現剛好也過了(´･_･`) 

Google 了下發現不少人都有同樣問題，幸好維修不難，只要把兩個螺絲轉鬆即可，也不用買新零件來替換。<!-- more -->

# 材料及工具

- Torx T5 梅花起子
- #PH1 十字起子
- 替換用滑鼠腳墊

# 維修過程

首先拆下腳墊（膠跟表層容易分開拆的時候要注意）。

![](https://static.driftking.tw/2024/06/afc05a128fe4b30608e909ccb4de010f.jpg)

上方為兩個 T5 梅花螺絲，兩側腳墊下則是四個 #1 十字螺絲。

接著抓住上殼兩側稍微施力打開，動作不要太大力不然會扯到上下 PCB 排線。

![](https://static.driftking.tw/2024/06/8d589af58625d467b78c7dd661fbd3d7.jpg)

如果只是要修拇指鍵的話其實大可不必拆排線，只需將下圖中兩個黑色螺絲稍微轉鬆半圈到一圈即可（可以按按看按鈕是否恢復正常），不過相較正常的狀況按鍵會稍微肉一點。

![](https://static.driftking.tw/2024/06/b6b0f552c2071f38a0e317288198dfa8.jpg)

由於我打算順便把微動換掉所以把排線先拆下來，方法是將米白色那片往後拉，就可以卸下排線。

# 更換微動

首先將電池艙螺絲及電池艙卸下後，拆下四顆固定左右鍵 PCB 的螺絲：

![](https://static.driftking.tw/2024/06/6a251fbab0ec48fadb9902d16de1c2a9.jpg)

原廠的微動是中國製 D2FC-F-7N(10M) 十萬點擊壽命的版本，我覺得手感普普而且聲音蠻大，這次順便換成了日本製 D2F-F-3-7（傳說中的頂級微動？），換了之後聲音小了不少（但絕對不是到靜音那麼誇張）。用起來回彈跟手感也都蠻合自己胃口的，這部分玩過的滑鼠微動不多不太會形容 XD

![](https://static.driftking.tw/2024/06/50cebf29125c9f15e833a6dddb78caba.jpg)

關於要怎麼換微動應該蠻容易的，不是本篇主題就不詳述了，完成後按順序鎖回去即可。

# Murmur

這次問題的罪魁禍首如下圖，萬惡的羅技塑膠部件（兩銀色螺絲鎖住的部分）：

![](https://static.driftking.tw/2024/06/227e57978f7d5f595c5f72fa03d32ba4.jpg)

推測是因為那片壓在拇指鍵微動上的塑膠件用久了就很容易有些變形，由於塑膠件及微動非常接近，稍微變形後容易變成按下去後就一直壓住微動，網路上蠻多人也都是一年多開始出現問題，恰好是保固過的時間(ry

這情況好像跟在 G27 上遇到的問題有點類似，同樣都是塑膠老化造成...... 雖然維修不算困難不過對於一個官方要價 3,000 NT 的滑鼠實在有點...... ╮(╯_╰)╭

# 參考

[Fixing the Logitech MX Master Scroll Wheel](https://toemat.com/logitech-mx-master-fix/)