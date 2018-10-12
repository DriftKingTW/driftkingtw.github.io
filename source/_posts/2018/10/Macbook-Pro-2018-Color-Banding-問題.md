---
title: Macbook Pro 2018 Color Banding 問題
tags:
  - Macbook Pro
  - 螢幕
keywords:
  - Macbook Pro
  - 螢幕
  - Color Banding
abbrlink: c3ffdb1b
date: 2018-10-12 17:51:04
---

# 前言

在測試螢幕RGB設定時順便測試了一下MBP2018的螢幕效能（一開始來只有看有無亮暗點），發現了在Gradient測試下居然出現了明顯的Banding現象...如下圖。

# Gradient Test

**都有在瀏覽器跟Photoshop內做測試，MacOS部分有另外在Safari測試**

**測試時皆爲最大亮度**

自己的MBP2018 13"預設色彩設定檔
測試值RGB(0,0,0)->RGB(100,100,100)

![Macbook Pro 2018](https://res.cloudinary.com/driftkingtw/image/upload/v1539338402/blog/2018/10/Macbook-Pro-2018-Color-Banding-%E5%95%8F%E9%A1%8C/P_20181012_160038_vHDR_Auto.jpg)

---

* 朋友的MBP2018 13"預設色彩設定檔
測試值RGB(0,0,0)->RGB(100,100,100)

![朋友的MBP2018 13"](https://res.cloudinary.com/driftkingtw/image/upload/v1539338685/blog/2018/10/Macbook-Pro-2018-Color-Banding-%E5%95%8F%E9%A1%8C/43787909_254069108589932_8509140437481553920_n.jpg)

---

* 借來的MBP2017 15"預設色彩設定檔
測試值RGB(0,0,0)->RGB(100,100,100)

![朋友借來的MBP2017 15"](https://res.cloudinary.com/driftkingtw/image/upload/v1539338685/blog/2018/10/Macbook-Pro-2018-Color-Banding-%E5%95%8F%E9%A1%8C/43788171_276891582933239_8832958624885112832_n.jpg)

---

* 工作螢幕EIZO EV2450，接MacOS 強制sRGB色彩設定檔（非YCbCr）
測試值RGB(0,0,0)->RGB(100,100,100)

![EIZO EV2450](https://res.cloudinary.com/driftkingtw/image/upload/v1539338403/blog/2018/10/Macbook-Pro-2018-Color-Banding-%E5%95%8F%E9%A1%8C/P_20181012_160311_vHDR_Auto.jpg)

---

* 副螢幕DELL P2214H，接Windows10 預設sRGB色彩設定檔
測試值RGB(0,0,0)->RGB(255,255,255)

![Dell P2214H](https://res.cloudinary.com/driftkingtw/image/upload/v1539338403/blog/2018/10/Macbook-Pro-2018-Color-Banding-%E5%95%8F%E9%A1%8C/P_20181012_175652_vHDR_Auto_HP.jpg)

---

* iPad Pro 2017 10.5"
測試值RGB(0,0,0)->RGB(255,255,255)

![iPad Pro 2017 10.5"](https://res.cloudinary.com/driftkingtw/image/upload/v1539338403/blog/2018/10/Macbook-Pro-2018-Color-Banding-%E5%95%8F%E9%A1%8C/P_20181012_175612_vHDR_Auto_HP.jpg)

---

另外上面兩台18年MBP查詢發現應該都是三星的面板，不確定供應商是否影響，有待確認。

查詢面板方法(Pro Retina)： `ioreg -lw0 | grep -i "DisplayVendorID"`

Mojave若遇到錯誤可以安裝XCode開發工具解決 `xcode-select --install`

* Samsung ID：1552
* LG ID：7789

以上結果可以看出現Color Banding應該也不是個案，而且iPad螢幕雖然開到最亮也會有稍微的Banding情況，但都沒有MBP嚴重，雖然平時外接不影響但是這價格的筆電螢幕出現這種問題還是令人不太舒服啊......上網找都是2010左右的討論，後來好像都沒聽說有人提起(ry

## 參考資料

[測試網站 The Lagom LCD monitor test](http://www.lagom.nl/lcd-test/gradient.php#gradient-h.png)

[檢查MacBook的LCD面板製造廠商（包含Air & Pro Retina）](https://dotblogs.com.tw/aquarius6913/2014/01/25/142159)

[macOS Mojave: invalid active developer path](https://apple.stackexchange.com/questions/254380/macos-mojave-invalid-active-developer-path)