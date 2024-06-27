---
title: 在 MacOS 上搭建 SMB 簡易檔案分享伺服器
tags:
  - MacOS
  - SMB
keywords:
  - MacOS
  - 檔案分享
  - SMB
abbrlink: b7400b18
date: 2019-10-04 21:00:00
---

有時候會想要在iPad上無線存取一些電腦裡的檔案，卻沒有NAS或在外面不方便，其實 MacOS 也有提供像 Windows 網路芳鄰的檔案分享功能，而且設定操作非常簡單，步驟如下。<!--more-->

# MacOS 搭建 SMB 伺服器

首先開啟系統設定(System Preferences)，接著選擇分享(Sharing)

![](https://static.driftking.tw/2024/06/ded4f5b1e960b9019328d2ac493327ca.jpg)

接著勾選左邊的檔案分享(File Sharing)

![](https://static.driftking.tw/2024/06/c9eeeebf55ddc60dfc3bfb3f7205b2a0.png)

在分享資料夾列表下面，點選"+"符號可以新增要分享的資料夾

![](https://static.driftking.tw/2024/06/d7104eb71d3ca2cae59a9c4f842803ce.png)

接著其他電腦就會像網路上的芳鄰一樣，能夠存取分享的檔案了！
行動裝置的話，iOS可以利用檔案管理的App來進行存取，這邊用的是Documents來做示範。

開啟 Documents 後，選擇附近的裝置(Nearby)，可以看到同網域下的電腦(SMB Server)列表，點進去登入電腦上的帳號就可以對分享檔案進行存取了！

![](https://static.driftking.tw/2024/06/38b4af9bd1cd68e266edd1f6c66fde94.png)

# 備註

要特別注意，如果使用的是 Android 系統，利用 MacOS 本身自帶的 SMB Server 是無法連上的，一開始我以為是設定錯誤，結果爬了文才發現原來這問題已經存在很久了，不過 Apple 看起來沒要修復的打算(ry

所以下一篇就要來介紹如何利用 SFTP 讓 Android 也能同樣輕鬆存取 MacOS 上的分享檔案囉！

# 參考

[How to connect with File Sharing on your Mac](https://support.apple.com/HT204445)