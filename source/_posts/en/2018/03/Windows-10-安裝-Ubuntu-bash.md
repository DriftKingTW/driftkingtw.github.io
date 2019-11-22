---
title: Windows 10 安裝 Ubuntu bash
tags:
  - Win10
  - Ubuntu
abbrlink: bad29b8c
date: 2018-03-03 11:06:46
keywords:
  - win10
  - ubuntu
---

本篇記錄如何在 Windows10 上安裝 Ubuntu bash<!--more-->

1. 在 Microsoft App 商店搜尋 "Ubuntu" 並安裝

2. 開啟 "設定" -> "App" -> "應用程式與功能"

3. 在最下方 "相關設定" -> "程式與功能" -> "開啟或關閉Windows功能"

4. 確認 "適用於 Linux 的 Windwos 子系統" 已勾選(可能需要重新啟動)

5. 再次打開 "設定" -> "更新與安全性" -> "開發人員專用" -> 選擇 "開發人員模式"

6. 開啟提示命令字元(系統管理員權限執行) -> 輸入以下指令

```shell
dism /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux
```

完成後執行 Ubuntu App, 即可開始使用

![Ubuntu bash on Win10](https://res.cloudinary.com/driftkingtw/image/upload/v1520047838/blog/2018/03/03/Windows-10-安裝-Ubuntu-bash/ubuntu.png)
