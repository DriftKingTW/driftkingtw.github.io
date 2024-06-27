---
title: 電腦/伺服器跨平臺的開源監控好幫手 - Glances
tags:
  - RaspberryPi/樹莓派
  - MonitoringTool/監控軟體
keywords:
  - RaspberryPi
  - 監控軟體
abbrlink: cdb5d655
lang: zh-tw
date: 2019-11-18 19:50:00
---

原先是在尋找一個能夠遠端監控伺服器狀態（基於樹莓派的 NAS）的軟體，由於每次都要連上 ssh 查看比較麻煩，加上 `htop` 我覺得能看到的訊息也不夠齊全（如溫度等），於是發現了一個跨平臺、支援遠端 Web-UI 且開源免費的方便監控軟體 - [Glances](https://nicolargo.github.io/glances/)<!--more-->，且安裝操作都非常簡單，有需要的朋友不坊試試，唯一的缺點大概就是基於 Python ，效能不是那麼理想吧（在 Pi 上感覺 CPU 佔用率比預期稍高了一點XD）

# 安裝

在 Linux（Debian、Ubuntu等）可以直接 `apt-get` 安裝

`sudo apt-get install glances`

MacOS 有安裝 `Homebrew` 的話可以用 `brew` 安裝

`brew install glances`

我自己在 MacOS 安裝時有發生小問題，安裝完後執行出現以下錯誤

```shell
Traceback (most recent call last):
  File "/usr/local/Cellar/glances/3.1.3/libexec/bin/glances", line 6, in <module>
    from pkg_resources import load_entry_point
ModuleNotFoundError: No module named 'pkg_resources'
```

用 `brew doctor` 無法解決問題，查詢了一下用 `postinstall python3` 即可解決

`brew postinstall python3`

Windows 等系統安裝好 `Python`，可透過 `pip` 安裝

`pip install glances`

# 使用

## 一般主機模式

在 Command Line 直接輸入即可啟動

`glances`

顯示 JSON 格式（後面接上想看的狀態）

`glances --stdout cpu.user,mem.used,load`

CSV 格式

`glances --stdout-csv now,cpu.user,mem.used,load`

按 `q` 或 `Esc` 離開

## Server 模式

在想查看狀態的電腦上

`glances -s`

在客戶端輸入查看伺服器的狀態（SERVER-IP 替換為上述 Server 的 IP）

`glances -s SERVER-IP`

## 遠端 Web-UI 模式

在想查看狀態的電腦上

`glances -w`

在同網域下的任何裝置瀏覽器上輸入（SERVER-IP 替換為上述 Server 的 IP），預設 Port 為 `61208`

`http://SERVER-IP:61208`

就能夠遠端監控了，除了CPU、MEM等狀態，同時也能看到溫度，如下圖所示（圖中是樹莓派 NAS 的狀態）

![](https://static.driftking.tw/2024/06/4173dbe27d6a5e064534433d0216abd2.png)


# 參考

[Glances](https://nicolargo.github.io/glances/)

[fails after brew installation: ModuleNotFoundError: No module named 'pkg_resources' #260](https://github.com/asciinema/asciinema/issues/260)