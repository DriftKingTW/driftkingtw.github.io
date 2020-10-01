---
title: MacOS - 軟體無法開啟
tags:
  - MacOS
keywords:
  - MacOS
  - 無法開啟
abbrlink: 71ead4f1
date: 2020-06-08 20:44:57
---

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1591620644/blog/2020/06/Screen_Shot_2020-06-08_at_8.47.52_PM.png)

在網路上下載了一個 MacOS 的 App，但卻提示如上圖一樣無法開啟該如何處理？
<!-- more -->

{% colorquote info %}
注意！這跟因為 MacOS 保護機制被攔截下來而無法開啟的 App 是不一樣的哦（非App Store下載或未識別開發者的軟體），想開啟此類警告的方法可以參照 [Apple 官方文件](https://support.apple.com/zh-tw/HT202491)
{% endcolorquote %}

# 解決方法

1. 解決方法是透過終端機（Terminal）操作，首先 `Cmd(⌘)+空白鍵` 輸入 `Terminal` 啟動終端機。
2. 接著輸入 App 所在的路徑如： `/Users/[YourUserName]/Downloads/`，或是簡單的打開 Finder 把 App 所在的資料夾拖到 Terminal 視窗後按 Enter 也可以。
3. 再來更改軟體內啟動程式的權限(Test.app 自行替換換成要開啟的 App 名稱)： `chmod +x Test.app/Contents/MacOS/*` (+x代表可執行)
4. 此時 App 應該就能夠正常執行，如果遇到非 App Store 等安全性問題被攔下，按照上述 [Apple 官方文件](https://support.apple.com/zh-tw/HT202491) 開啟即可。

# 參考

[The application “SomeApp.app” can’t be opened](https://superuser.com/questions/898124/the-application-someapp-app-can-t-be-opened)
[在 Mac 上開啟 app](https://support.apple.com/zh-tw/HT202491)
