---
title: 利用 SFTP 在 Android 上存取 MacOS 檔案
tags:
  - MacOS
  - SFTP
keywords:
  - MacOS
  - 檔案分享
  - SFTP
abbrlink: 1a5b208d
date: 2019-10-04 22:00:00
---

上一篇提到了如何利用 MacOS 本身自帶的檔案分享功能搭建 SMB 進行分享，不過因為一些 MacOS 自帶 SMB 的一些 bug 所以 Android 的用戶就沒那麼方便了(~~Apple你到底修不修~~)，幸好有另一種方法，也就是本文的主題：利用 SFTP 在 MacOS 上搭建檔案分享伺服器。<!--more-->

# MacOS 搭建 SFTP 伺服器

首先同樣進到系統設定(System Preferences)，接著選擇分享(Sharing)

![](https://static.driftking.tw/2024/06/ded4f5b1e960b9019328d2ac493327ca.jpg)

在這邊我們不選擇檔案分享 (File Sharing) ，而是選擇遠端登入 (Remote Login)

![](https://static.driftking.tw/2024/06/610f21baa079a4d896f17d15b539cfdd.png)

這時我們會看到下方有一行資訊顯示"ssh username@192.168.xxx.xxx"類似的相關字串(依照IP跟使用者而定)，這邊先記下我們的區網IP。

接著就可以切換到 Android 端，這邊用 ES File Explorer 做示範

側邊欄選擇 FTP ，接著按右下角加號新增伺服器

![](https://static.driftking.tw/2024/06/0fc3f2633d5a402301e1f1f0df27d062.png)

Server 欄輸入剛才記下的區網 IP，Port 預設 22 沒改動就維持原樣
然後填入電腦帳號密碼按下 OK 就能夠成功連上看到 MacOS 裡的內容囉。

![](https://static.driftking.tw/2024/06/ee154955f813183de9df45f9b07f2597.png)

# 參考

[How to Browse/Share Files on Mac and Android with ES File Explorer SFTP](https://www.mjdtech.net/browse-files-on-mac-with-es-file-explorer-and-sftp/)