---
title: MacOS強制外接螢幕輸出RGB
abbrlink: d7c7ea31
date: 2018-10-12 09:16:28
tags:
  - MacOS
  - 螢幕
keywords:
  - MacOS
  - 螢幕
  - RGB
  - 文字不清
---

之前發現Mac外接螢幕的灰階有點奇怪，感覺反而沒在Windows上順暢，於是Google了一下發現原來MacOS用HDMI接非Apple官方認可的螢幕時會當成電視用YCbCr輸出而不是RGB，所以會造成顯示器顏色上出現問題或是字體渲染不清的情況。~~人性化的系統~~

一開始想說Retina用的字體到1080p上會比較糢糊是正常的就沒特別注意到，顏色部分因爲我的螢幕（EIZO EV2450）也有支援YCbCr 4:4:4輸入又開著TrueTone，所以平常也沒特別看出有問題。既然是電腦螢幕那還是乖乖修改成RGB輸出吧嗯！<!--more-->

# How

下圖是初始的顏色設定：

![顏色設定](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1539307427/blog/2018/10/MacOS%E5%BC%B7%E5%88%B6%E5%A4%96%E6%8E%A5%E8%9E%A2%E5%B9%95%E8%BC%B8%E5%87%BARGB/Screen_Shot_2018-10-10_at_8.56.45_PM.png)

![初始狀態](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1539307702/blog/2018/10/MacOS%E5%BC%B7%E5%88%B6%E5%A4%96%E6%8E%A5%E8%9E%A2%E5%B9%95%E8%BC%B8%E5%87%BARGB/Screen_Shot_2018-10-10_at_8.56.58_PM.png)

在做之前我們需要先關閉 MacOS 的 Rootless 功能（如果會用 `sudo` 也可以不關，直接往下的步驟）

關閉 MacOS 後，按住 `CMD+R` 開機，上方Utilities選單找到Terminal開啓，輸入 `csrutil disable` ，在重新開機就可以了，做完步驟後可以再用一樣的方法開啓（改成輸入 `csrutil enable` ）。

1. 首先下載 [Edid-Patch](https://gist.github.com/adaugherity/7435890)
   
2. 確定外接螢幕接上 Mac
   
3. 打開Terminal，進入下載 `edid-patch` 的資料夾（不會的可以直接放在家目錄），然後執行 `ruby patch-edid.rb` 。

4. 然後會在同目錄下產生一個資料夾 `DisplayVenderID-xxx` ， 打開Finder按 `Cmd+Shift+G` 前往目錄 `/System/Library/Displays/Contents/Resources/Overrides`，把剛才產生的資料夾放進去。
   (沒有關閉Rootless的話可以用 `sudo cp` 移動資料夾)
   
5. 重開機，看一下顏色設定確認強制RGB後就完成了！（有關Rootless記得開回來）

![強制RGB輸出](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1539307715/blog/2018/10/MacOS%E5%BC%B7%E5%88%B6%E5%A4%96%E6%8E%A5%E8%9E%A2%E5%B9%95%E8%BC%B8%E5%87%BARGB/Screen_Shot_2018-10-10_at_9.38.03_PM.png)

P.S. 我的EV2450強制RGB後發現對比很奇怪，後來螢幕上的設定看了一下原來Range Extension預設Auto，設定成關閉後，螢幕就正常了！

經過強制RGB後灰階也正常囉。

## 參考資料

[Github - adaugherity/patch-edid.rb](https://gist.github.com/adaugherity/7435890)

[Force RGB mode in Mac OS X to fix the picture quality of an external monitor](https://www.mathewinkson.com/2013/03/force-rgb-mode-in-mac-os-x-to-fix-the-picture-quality-of-an-external-monitor)