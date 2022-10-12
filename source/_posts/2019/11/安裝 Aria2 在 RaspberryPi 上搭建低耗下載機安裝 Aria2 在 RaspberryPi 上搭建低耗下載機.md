---
title: 安裝 Aria2 在 RaspberryPi 上搭建低耗下載機
tags:
  - RaspberryPi/樹莓派
keywords:
  - RaspberryPi
  - 樹莓派
  - 下載機
abbrlink: '8907503'
date: 2019-11-16 00:15:00
---

[Aria2](https://aria2.github.io/) 是一個超輕量但功能的強大開源下載器，支援多點下載、多種協定、檔案分割下載、可透過 JSON-PRC 或 XML-PRC 遠端控制等功能。<!--more-->

[Aria2](https://aria2.github.io/) 的特性讓他非常適合放在樹莓派等單晶片上執行，輕量不耗資源、又能遠端操作，我的 Pi 剛好作為 NAS，搭配下載器可以直接存入 NAS 硬碟中，在做遠端存取且不用關機非常方便，本篇將會記錄安裝 [Aria2](https://aria2.github.io/) 在 Pi 上的過程。

# 安裝 Aria2

`sudo apt-get update`

`sudo apt-get install aria2`

# 設定 Aria2

建立設定資料夾與檔案(注意這邊不用 `sudo`，否則還要回過頭來改資料夾權限給 user)

`mkdir -p ~/.aria2/`

`touch ~/.aria2/aria2.session`

`vim ~/.aria2/aria2.conf`

```conf
## Files
dir=/mnt/Share/Downloads
disk-cache=32M
file-allocation=trunc
continue=true

## Downloads
max-concurrent-downloads=10
max-connection-per-server=16
min-split-size=10M
split=5
max-overall-download-limit=0
#max-download-limit=0
#max-overall-upload-limit=0
#max-upload-limit=0
disable-ipv6=false

## Sessions
save-session=/home/pi/.aria2/aria2.session
input-file=/home/pi/.aria2/aria2.session
save-session-interval=60

## RPC Settings
enable-rpc=true
rpc-allow-origin-all=true
rpc-listen-all=true
#rpc-secret=secret
#event-poll=select
#rpc-listen-port=6800

## Others
enable-dht=true
enable-dht6=true
enable-peer-exchange=true
```

在 `Files` 部分的 `dir` 是指檔案下載路徑，這裡我把它掛在外接硬碟上方便存取。

接著啟動 `aria2c` 並指定設定文件所在位置。

`aria2c --conf-path=/home/pi/.aria2/aria2.conf`

看一下進程有沒有成功

`ps aux | grep aria2`

# 設定開機啟動服務

建立設定文件

`sudo vim /lib/systemd/system/aria2.service`

```conf
[Unit]
Description=Aria2 Service
Requires=network.target
After=dhcpcd.service

[Service]
User=pi
RemainAfterExit=yes
ExecStart=/usr/bin/aria2c --conf-path=/home/pi/.aria2/aria2.conf
ExecReload=/usr/bin/kill -HUP $MAINPID
ExecStop=/usr/bin/kill -s STOP $MAINPID
RestartSec=1min
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

接著載入服務並啟動檢查是否成功：

`sudo systemctl daemon-reload`

`sudo systemctl enable aria2`

`sudo systemctl start aria2`

`sudo systemctl status aria2`

成功的話會顯示類似下方狀態

```shell
● aria2.service - Aria2 Service
   Loaded: loaded (/lib/systemd/system/aria2.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2019-11-15 16:01:37 GMT; 4s ago
 Main PID: 1509 (aria2c)
    Tasks: 1 (limit: 4915)
   Memory: 3.4M
   CGroup: /system.slice/aria2.service
           └─1509 /usr/bin/aria2c --conf-path=/home/pi/.aria2/aria2.conf

Nov 15 16:01:37 pinas systemd[1]: Started Aria2 Service.
Nov 15 16:01:37 pinas aria2c[1509]: 11/15 16:01:37 [WARN] Neither --rpc-secret nor a combination of --rpc-user and --rpc
Nov 15 16:01:37 pinas aria2c[1509]: 11/15 16:01:37 [NOTICE] IPv4 RPC: listening on TCP port 6800
Nov 15 16:01:37 pinas aria2c[1509]: 11/15 16:01:37 [NOTICE] IPv6 RPC: listening on TCP port 6800
```

# 安裝 Nginx 網頁伺服器

安裝 nginx （若有其他網頁伺服器也可以，如 Apache 等）

`sudo apt install nginx`

啟動服務

`sudo /etc/init.d/nginx start`

在瀏覽器輸入 http://RASPBERRYPI-IP/

看到 `Welcome to nginx!` 等字樣代表伺服器成功安裝並且正常執行了

# 安裝 AriaNG 網頁操作界面

下載 [AriaNG](https://github.com/mayswind/AriaNg/releases)，撰文時最新版為1.1.4，有更新版可以自行檢查。

`wget https://github.com/mayswind/AriaNg/releases/download/1.1.4/AriaNg-1.1.4.zip`

解壓縮

`unzip AriaNg-1.1.4.zip -d aria-ng`

把資料夾搬去伺服器的網頁目錄底下（ nginx 預設為 /var/www/html ）

`sudo mv aria-ng /var/www/html/`

此時瀏覽器輸入 `http://RASPBERRYPI-IP/aria-ng` 即可看到 AriaNG 的界面了。

在如下設定分頁內填入相關設定（ IP 或 hostname，預設 Port 為 `6800` ），看到 `Connected` 即可開始遠端操作下載等任務了！

![](https://res.cloudinary.com/driftkingtw/image/upload/v1573834062/blog/2019/11/%E5%AE%89%E8%A3%9D%20Aria2%20%E5%9C%A8%20RaspberryPi%20%E4%B8%8A%E6%90%AD%E5%BB%BA%E4%BD%8E%E8%80%97%E4%B8%8B%E8%BC%89%E6%A9%9F/Screen_Shot_2019-11-16_at_12.07.37_AM.png)

# 參考

[aria2](https://github.com/aria2/aria2)

[AriaNG](https://github.com/mayswind/AriaNg)

[Remote Download Server in Linux using Aria2](https://medium.com/@sajithneyo/remote-download-server-in-linux-using-aria2-5bd3ee1a54b2)

[用树莓派 Raspberry Pi 远程下载 (aria2)](https://li-aaron.github.io/2019/01/aira2-on-raspberry/)

[Setting up an NGINX web server on a Raspberry Pi](https://www.raspberrypi.org/documentation/remote-access/web-server/nginx.md)