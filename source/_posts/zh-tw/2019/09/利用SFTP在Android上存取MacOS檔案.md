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

# 前言

上一篇提到了如何利用 MacOS 本身自帶的檔案分享功能搭建 SMB 進行分享，不過因為一些 MacOS 自帶 SMB 的一些 bug 所以 Android 的用戶就沒那麼方便了(~~Apple你到底修不修~~)，幸好有另一種方法，也就是本文的主題：利用 SFTP 在 MacOS 上搭建檔案分享伺服器。

# MacOS 搭建 SFTP 伺服器

首先同樣進到系統設定(System Preferences)，接著選擇分享(Sharing)

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1570189697/blog/2019/10/%E5%9C%A8%20MacOS%20%E4%B8%8A%E6%90%AD%E5%BB%BA%20SMB%20%E7%B0%A1%E6%98%93%E6%AA%94%E6%A1%88%E5%88%86%E4%BA%AB%E4%BC%BA%E6%9C%8D%E5%99%A8/Screen_Shot_2019-10-04_at_7.44.42_PM.jpg)

在這邊我們不選擇檔案分享 (File Sharing) ，而是選擇遠端登入 (Remote Login)

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1570191030/blog/2019/10/%E5%88%A9%E7%94%A8%20SFTP%20%E5%9C%A8%20Android%20%E4%B8%8A%E5%AD%98%E5%8F%96%20MacOS%20%E6%AA%94%E6%A1%88/.Screen_Shot_2019-10-04_at_8.10.17_PM.png)

這時我們會看到下方有一行資訊顯示"ssh username@192.168.xxx.xxx"類似的相關字串(依照IP跟使用者而定)，這邊先記下我們的區網IP。

接著就可以切換到 Android 端，這邊用 ES File Explorer 做示範

側邊欄選擇 FTP ，接著按右下角加號新增伺服器

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1570191510/blog/2019/10/%E5%88%A9%E7%94%A8%20SFTP%20%E5%9C%A8%20Android%20%E4%B8%8A%E5%AD%98%E5%8F%96%20MacOS%20%E6%AA%94%E6%A1%88/Screenshot_20191004-201308.png)

Server 欄輸入剛才記下的區網 IP，Port 預設 22 沒改動就維持原樣
然後填入電腦帳號密碼按下 OK 就能夠成功連上看到 MacOS 裡的內容囉。

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1570191514/blog/2019/10/%E5%88%A9%E7%94%A8%20SFTP%20%E5%9C%A8%20Android%20%E4%B8%8A%E5%AD%98%E5%8F%96%20MacOS%20%E6%AA%94%E6%A1%88/Screenshot_20191004-201757.png)

# 參考

[How to Browse/Share Files on Mac and Android with ES File Explorer SFTP](https://www.mjdtech.net/browse-files-on-mac-with-es-file-explorer-and-sftp/)