---
title: 利用 Raspberry Pi 搭建 NAS
tags:
  - MacOS
  - NAS
keywords:
  - MacOS
  - NAS
  - Samba
  - Smb
abbrlink: c138150d
date: 2019-10-12 21:25:00
---

就只是個基於 Samba 的簡易 NAS 架設過程，沒有任何動靜。

BTW 目前還是使用 Pi3B ，升級後會在補上 Pi4B 速度的差異心得。未來打算再裝上 Aria2 讓 Pi 具有下載機的功能。<!--more-->

# 準備工作

首先安裝好最新的 Raspbian Lite 版本，開機後先進行基本設定（ 可以單純ssh操作省去外接的麻煩，關於headless可以參考我之前的文章：[Raspberry Zero W Headless 自動連上無線網路與開啟SSH](https://blog.driftkingtw.me/archives/6759087b.html) ）

` sudo raspi-config `

把預設密碼之類改一改，還有地區之類等基本設定。
設定完後先更新一下套件。

` sudo apt-get update && sudo apt-get upgrade `

# 外接硬碟設定

首先用 `lsblk` 找到要當 NAS 硬碟及分區(這邊是 `sda2` 代表 a 磁碟的 2 號分區)

格式化為 `ext4` 格式，~~這邊推薦用 `ext4` 效能最好~~（最近在做 NAS 優化，發現XFS的效果會更提升一些，可以參考[XFS 檔案格式](#XFS-檔案格式)）

`sudo mkfs.ext4 /dev/sda2`

*有些人可能會有疑問，如果用 `ext4` 會不會讓我無法在其他系統上讀取，這部分並不用擔心，因為這個硬碟格式只是給 Server 讀取寫入用的，客戶端支援的檔案格式不會影響到*

建立掛載點

`sudo mkdir /mnt/Share`

查看硬碟分區的 `UUID` 並把它複製下來

` sudo blkid `

編輯 ` fstab ` 添加掛載資訊

` sudo vim /etc/fstab `

在最底行填上（"YOUR_UUID"替換成剛才複製的UUID）

` UUID=YOUR_UUID  /mnt/Share  ext4  defaults  0 2 `

掛載硬碟(分區)

` sudo mount -a `

查看掛載狀態是否正確

` df -h `

最後開啟硬碟權限

` chmod 777 /mnt/Share `

# 設定 Samba Server

接著安裝 `Samba`

`sudo apt-get install samba samba-common-bin`

完成後編輯 `/etc/samba/smb.conf`

`sudo vim /etc/samba/smb.conf`

Global 部分填入限制使用者登入才能存取的設定

```conf
[global]
  security = user
```

在文件最下面填入如下的分享目錄設定

```conf
[share]
  Comment = Pi Shared Folder
  Path = /mnt/Share
  Browseable = yes
  Writable = yes
  guest ok = no
  valid users = username
```

PS：valid users 後面替換成自己的 username。

接著我們要加入 smb 的登入帳號：

`sudo smbpasswd -a username`

依照提示輸入密碼設定完成，最後重啟 Samba 服務完成設定。

`sudo service smbd restart`

都設定完成後就可以在 Windows 網路芳鄰上看到 NAS 了（由於有限定使用者，要同樣帳號名稱密碼才能進的去，若要不希望限定帳號記得在 `smb.conf` 把 guest 功能打開）

MacOS 的話則在 Finder 下按 `CMD+K` 填入 `smb://raspberrypi.local/Share` ，輸入剛才設定的 smb 使用者帳號密碼即可連入 NAS 。

# 補充

## XFS 檔案格式

安裝 XFS 工具

`sudo apt-get install xfsprogs`

格式化 sda 硬碟為 xfs 格式

`sudo mkfs.xfs -f /dev/sda`

查看掛載狀態

`df -h`

## 本地硬碟讀取速度測試

安裝 `hdparm`

`sudo apt-get install hdparm`

測試讀取：

`hdparm -t /dev/sda`

測試寫入：

`hdparm -t --direct /dev/sda`

# 參考

[Browse Raspberry Pi in OSX Finder via Samba](http://blog.jachobsen.com/2013/04/29/browse-raspberry-pi-in-osx-finder-via-samba/)

[How to restart samba server?](https://askubuntu.com/questions/79078/how-to-restart-samba-server)

[How to create and mount an XFS file system on Linux](http://ask.xmodulo.com/create-mount-xfs-file-system-linux.html)

[hdparm 測試硬碟讀寫速度](https://shazi.info/hdparm-%E6%B8%AC%E8%A9%A6%E7%A1%AC%E7%A2%9F%E8%AE%80%E5%AF%AB%E9%80%9F%E5%BA%A6/)