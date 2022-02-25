---
title: 在 Apple Silicon Mac 上使用 DisplayCAL 校正外接顯示器
tags:
  - Apple Silicon
  - macOS
  - DisplayCAL
keywords:
  - Apple Silicon
  - Apple
  - M1
  - Mac
  - 校色
  - DisplayCAL
abbrlink: 6c619daf
date: 2022-02-07 19:00:00
---

最近又到了要重新幫螢幕校色的時候了，之前都是使用 DisplayCAL 這套軟體做色彩校正，但去年年底將電腦換成 Apple Silicon Mac 後，在軟體使用上遇到了無法偵測外接螢幕的問題，這邊分享一下解決方式：

![DisplayCAL](https://res.cloudinary.com/driftkingtw/image/upload/g_auto/v1644288292/blog/2022/02/calibrate-external-monitor-on-apple-silicon-mac-by-displaycal/displaycal.png)

<!-- more -->

# 問題與解決方式

`DisplayCAL` 在安裝後會安裝一套叫做 `ArgyllCMS` 開源的色彩管理系統，
但是安裝完成後可以看到我的外接螢幕 Dell U2520D 並沒有顯示在顯示器列表中...如下圖：

![DisplayCAL 無法偵測外接顯示器](https://res.cloudinary.com/driftkingtw/image/upload/g_auto/v1644288292/blog/2022/02/calibrate-external-monitor-on-apple-silicon-mac-by-displaycal/no-external-screen-detected.png)

造成這個問題的原因是 `DisplayCAL` 雖然安裝時說會下載最新的 `ArgyllCMS` ，
但其實預設安裝的版本為 `v2.1.2`

目前的解決方法則是透過手動下載新版 `ArgyllCMS` 後覆蓋掉 `DisplayCAL` 安裝的版本：

1. 首先到 [DisplayCAL](https://displaycal.net/) 下載安裝最新版本

2. 插上校色儀後啟動 `DisplayCAL` ，筆者自己的校色器是 `SpyderX`

3. 首次啟動時會提示需要安裝 `ArgyllCMS`：

![DisplayCAL 安裝 ArgyllCMS](https://res.cloudinary.com/driftkingtw/image/upload/g_auto/v1644288292/blog/2022/02/calibrate-external-monitor-on-apple-silicon-mac-by-displaycal/displaycal-argyllcms-install.png)

4. 這邊先選擇取消安裝，然後到 [ArgyllCMS](https://www.argyllcms.com/downloadmac.html) 下載 `Intel OS X 10.6 64 bit or later` 的最新版本（22年2月撰文時為 `v2.3.0`，接下來提到的路徑可能版本號會有不同請注意）

5. 解壓縮下載回來的 `.tgz` 檔案，並將解壓出來的 `Argyll_V2.3.0` 資料夾整個移動至 
`~/Library/Application Support/DisplayCAL/dl` (可透過在 `Finder` 中按下 `⌘Command + ⇧Shift + G` 後貼上路徑直接開啟資料夾)

6. 接著回到 `DisplayCAL` 中上方菜單欄選擇 `File > Locate ArgyllCMS executables...`

7. 選擇 `~/Library/Application Support/DisplayCAL/dl/Argyll_V2.3.0/bin/` 點選開啟

8. 接下來應該就能夠正常識別外接螢幕了，校色過程中可能會出現 `macOS` 阻擋非安全執行的對話框，記得去系統隱私安全設定那邊點選允許執行

![DisplayCAL 成功識別外接螢幕](https://res.cloudinary.com/driftkingtw/image/upload/g_auto/v1644288292/blog/2022/02/calibrate-external-monitor-on-apple-silicon-mac-by-displaycal/external-screen-detected.png)

{% colorquote warning %}
在校色過程中有遇到一些奇怪的小bug，在校色快完成時如果跳出非安全執行的對話框會有可能導致外接顯示器全黑沒畫面需要重新連接，推測可能是在套用 ICC Profile 時被擋住的問題，這部分可以自行嘗試時間比較短的校色流程試試，流程跑完沒問題後再做詳細校色比較省時間
{% endcolorquote %}

# 參考

- [Mac Mini M1 color calibration](https://www.reddit.com/r/macmini/comments/kembz2/mac_mini_m1_color_calibration/)
- [DisplayCAL](https://displaycal.net/)
- [ArgyllCMS](https://www.argyllcms.com/)