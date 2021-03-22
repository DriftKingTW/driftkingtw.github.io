---
title: 利用 Raspberry Pi 搭建 TimeCapsule（仮）- 軟體篇
tags:
  - MacOS
  - TimeCapsule
  - TimeMachine
keywords:
  - MacOS
  - TimeCapsule
  - TimeMachine
  - 時光機備份
abbrlink: 613e9fa3
date: 2019-10-11 21:25:00
---

Sorry, this post in currently in Chinese only :(

相信 MacOS 的使用者應該都熟悉官方提供的備份軟體「TimeMachine」，不過如果是 Macbook 等筆電就比較麻煩了，三不五時就要接上隨身硬碟來備份對於我這種懶人來說，定時備份根本是不可能的任務。

不過備份依然是相當重要的，除了升級系統有機會用到，再來就是現在的 Macbook 硬體如此不可靠你敢不備份嗎(笑)。為了解決懶的問題，我想用無線的方式進行備份。停產的 Airport ，或是群輝（Synology）的 NAS 都有提供 TimeMachine 無線備份的功能，然而這兩者的價格都還是偏高的QwQ

所以在一輪 Google 後，我的解決方法就是利用此篇的主角：樹莓派（Raspberry Pi）來完成這項任務，在 Pi 4 推出後，硬體規格升級到了USB3.0、Gigabit Ethrnet，做個人用的簡易 NAS 已經算是堪用，更不用說對速度要求不高的 TimeMachine 了！<!--more-->

# 準備工作

首先安裝好最新的 Raspbian Lite 版本，開機後先進行基本設定（ 可以單純ssh操作省去外接的麻煩，關於headless可以參考我之前的文章：[Raspberry Zero W Headless 自動連上無線網路與開啟SSH](https://blog.driftkingtw.me/archives/6759087b.html) ）

` sudo raspi-config `

把預設密碼之類改一改，還有地區之類等基本設定。
設定完後先更新一下套件。

` sudo apt-get update && sudo apt-get upgrade `

接著重啟樹莓派。

` sudo reboot `

# 分割磁碟(可選)

如果整顆硬碟都是要拿來做 TimeCapsule 的話此步驟可以跳過，直接續讀<strong>[安裝過程](#安裝過程)</strong>。

首先列出硬碟情況：

` lsblk `

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1570812124/blog/2019/10/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E8%BB%9F%E9%AB%94%E7%AF%87/Screen_Shot_2019-10-12_at_12.41.53_AM.png)

找到硬碟代號，例如這邊sda是我的外接硬碟，裡面有之前的分區。
首先用 `fdisk` 重新分割這顆硬碟：

` sudo fdisk /dev/sda `

進入 `fdisk` 可以按 `m` 顯示使用說明，這邊簡述我的硬碟分割過程：

1. 輸入兩次 `d` 刪除兩個原先的分區

2. `n` 新增分區，分區代號以及起始區塊預設，結束區塊 `+450G` (這樣會建立一個代號1，450G大小的分區)

3. 再次用 `n` 新增第二分區，一路 `Enter` 完成剩下硬碟空間的分區

4. `w` 寫入硬碟分區資料並退出 `fdisk`

接著再次查看分割結果是否正確

` lsblk `

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1570812186/blog/2019/10/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E8%BB%9F%E9%AB%94%E7%AF%87/Screen_Shot_2019-10-12_at_12.42.56_AM.png)

圖中可以看到硬碟已經成功分割成希望的數量以及大小
(注意：`fdisk` 僅支援分割2TB內的硬碟，若要分割更大的需要安裝 `gparted` 在桌面環境下操作)

# 安裝過程

## 準備硬碟

首先格式化硬碟(`sda1`是我要格式化的分區，請依照`lsblk`顯示的自行更換)

` sudo mkfs.ext4 /dev/sda1 `

這邊建議以 `ext4` 格式化達到最佳效能，雖然有工具可以讓 Linux 讀取 `hfsplus` 等蘋果檔案格式，但是我測試之後速度慢到想哭(／‵Д′)／~ ╧╧（這邊不太確定是不是我不會設定問題，若有錯誤歡迎指正

而且 `hfsplus` 也無法把硬碟拆下來接上 MacOS 就直接兩邊互通，索性直接用 `ext4`，以後連復原都透過網路了。

建立掛載點

` sudo mkdir /mnt/TimeCapsule `

查看分區的 `UUID` 並把它複製下來

` sudo blkid `

編輯 ` fstab ` 添加掛載資訊

` sudo vim /etc/fstab `

在最底行填上（"YOUR_UUID"替換成剛才複製的UUID）

` UUID=YOUR_UUID  /mnt/TimeCapsule  ext4  defaults  0 2 `

掛載硬碟(分區)

` sudo mount -a `

查看掛載狀態

` df -h `

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1570812250/blog/2019/10/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E8%BB%9F%E9%AB%94%E7%AF%87/Screen_Shot_2019-10-12_at_12.44.00_AM.png)

可以看到 ` sda1 ` 已成功掛載到 ` /mnt/TimeCapsule ` 了

最後開啟硬碟權限

` chmod 777 /mnt/TimeCapsule `

## 安裝及設定 Netatalk

安裝 `Netatalk`

` sudo apt-get install netatalk `

安裝完成後用 ` netatalk -v ` 查看版本以及設定檔位置

接著編輯 `nsswitch.conf`

` sudo vim /etc/nsswitch.conf `

在 `host` 那行的最後加上 `mdns4 mdns` 如下

` hosts: files mdns4_minimal [NOTFOUND=return] dns mdns4 mdns `

設定 `afp.conf`

` sudo vim /etc/netatalk/afp.conf `

內容如下

```conf
[Global]
  mimic model = TimeCapsule6,106

[Time Machine]
  path = /mnt/TimeCapsule
  time machine = yes
```

保存後退出，最後啟動服務：

`sudo service avahi-daemon start`

`sudo service netatalk start`

這時應該能在 MacOS 看到你的網路硬碟了！

為了確定開機時會自動啟動服務，輸入以下指令：

`sudo systemctl enable avahi-daemon`

`sudo systemctl enable netatalk`

## MacOS 端設定

接著打開 TimeMachine 設定，按選擇備份碟之後就能夠看到我們用 Raspberry Pi 的備份硬碟囉！選擇它之後輸入 Pi 的帳號密碼即可開始備份（第一次備份時間通常較長，如果開啟加密會更久），完成首次備份後接著以後就是差異備份，只要連上就會自動定期執行，在也不用怕麻煩或忘記備份了！

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1570812960/blog/2019/10/%E5%88%A9%E7%94%A8%20Raspberry%20Pi%20%E6%90%AD%E5%BB%BA%20TimeCapsule%EF%BC%88%E4%BB%AE%EF%BC%89-%20%E8%BB%9F%E9%AB%94%E7%AF%87/Screen_Shot_2019-10-12_at_12.55.43_AM.png)

## 進階設定

### Raspberry Pi 端

#### 用戶管理

新增用戶（username改成自己需要的使用者名稱）

` useradd --create-home --groups users --user-group username `

建立密碼

` passwd username `

編輯 `afp.conf`

` sudo vim /etc/netatalk/afp.conf `

在 `[Time Machine]` (顯示名稱依照之前設定的可能會有不同)新增使用者

```conf
[Time Machine]
  path = /mnt/TimeCapsule
  time machine = yes
  valid user = username
```

#### 靜態 IP

固定 Pi 的 IP，這部分可以前往路由器設定，由於每家路由器不一定相同，所以另外依照各廠商提供的說明設定。

### Mac 端

首次備份時若覺得備份速度太慢可以執行以下 Script 關閉限流：

` sudo sysctl debug.lowpri_throttle_enabled=0 `

恢復方式：

` sudo sysctl debug.lowpri_throttle_enabled=1 `

# 參考

[替 Linux 新增硬碟（磁碟分割、格式化與掛載）](https://blog.gtwang.org/linux/linux-add-format-mount-harddisk/)

[How to Use Your Raspberry Pi as a Time Machine Backup Server for macOS](https://www.youtube.com/watch?v=yJs8f0spJO4)

[How to speed up your Time Machine backups](https://www.imore.com/how-speed-your-time-machine-backups)

[AFP Netatalk share configuration (aka Apple Time Machine).](https://openwrt.org/docs/guide-user/services/nas/netatalk_configuration)

[Raspberry Pi Time Machine (2019 update)](https://gregology.net/2018/09/raspberry-pi-time-machine/)

[How to use a Raspberry Pi for your Time Machine backups](https://www.jannikarndt.de/blog/2018/01/how_to_use_a_raspberry_pi_for_your_time_machine_backups/)

[Apple Time machine on RaspberryPI](https://github.com/mr-bt/raspberrypi-timemachine)

[How to use a Raspberry Pi for your Time Machine backups](https://www.jannikarndt.de/blog/2018/01/how_to_use_a_raspberry_pi_for_your_time_machine_backups/)

[利用 Raspberry Pi（树莓派）打造 Time Capsule（时间胶囊）](https://aunicorn.io/2018/08/15/how-to-make-a-time-capsule-with-your-raspberry-pi/)

PS 因為各種文章新舊版本問題，所以參考了不少資料才整理出一個目前可以使用，快速的設定過程。