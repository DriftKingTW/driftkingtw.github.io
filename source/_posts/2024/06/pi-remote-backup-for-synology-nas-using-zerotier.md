---
title: 透過樹莓派 5 及 ZeroTier 幫 NAS 做異地備份
tags:
  - NAS
  - Synology
  - DSM
  - Raspberry Pi
keywords:
  - NAS
  - Synology
  - DSM
  - Raspberry Pi
  - Zero Tier
abbrlink: 97cef381
date: 2024-06-25 22:00:00
---

這玩意看起來很適合做異地備份欸！ <- 這是我某天看到樹莓派 5 的 PCIe 轉雙 M.2 HAT 推出之後的反應 XD

前陣子 (其實也挺久了) 樹莓派推出了第五代，這次終於提供了 PCIe 接口 ~~真是振奮人心~~ 😭

恰巧前陣子亂逛時發現有幾家廠商推出了雙 M.2 版本的 HAT，這個組合讓我想到可以拿來做因為種種原因而沒搞定的異地備份。

<!--more-->

我看上樹莓派 5 來做異地備份主要有以下幾個原因：

1. 體積小功耗低，適合放在各種地方 (放老家/辦公室/朋友家等)
2. 價格(?)優勢，不含硬碟的核心的零件約 $100 美金能夠買到，加上現在供貨總算比較穩定了
3. 硬體自訂性高，雖然現在有 N100 等 CP 值極高的 x86 小主機，~~但用樹莓派就是比較浪漫比較酷~~
4. 效能足夠 (4G 足矣)，因為單純作為異地備份 Pi 5 的效能甚至有些過盛？
5. PCIe 插槽讓 SSD 不用再透過 USB，速度及穩定性 ⬆️⬆️
6. 這代加入了如官方主動散熱模組、電源開關等功能，除了安裝設定都更輕鬆，也可以縮小體積 (我能夠放異地備份的空間都不算很大，能省則省)

這邊可以參考我之前用 Pi 4 製作的 [Pi-Dash](https://blog.driftking.tw/2023/10/pi-dashboard/) 應該就會發現這代把一些我之前需要自幹的功能都已經內建或提供官方的整合方案了，不用自己弄一堆有的沒的還會減少原本就已經不多的 IO 🙏

當然你可能會說能用另一台更正式的 NAS 作為異地備份是最好的選擇，確實 (？ 但我最重要且需要異地備份的量也不多(可能頂多 2T 短期內就算綽綽有餘)，加上上述幾項優點我認為這是目前最適合的選擇了！

那麼如果你覺得這個方案不錯並有興趣，我也把軟硬體的一些設定寫成筆記分享給大家，歡迎繼續往下閱讀：

## 資料備份 3-2-1

資料備份有個重要的 3-2-1 規則，相信應該不少人已經耳熟能詳，可以直接跳至 [硬體 / 軟體需求](#硬體-amp-軟體需求)，如果沒聽過的話也沒關係這邊簡單介紹一下：

3-2-1 代表

- 至少 3 份相同的備份資料
- 至少 2 種不同的儲存媒介
- 至少 1 分資料要存放在物理上不同的位置

並且這邊有些常見的誤區要注意：

- 重要資料對每個人定義不同，並不是所有資料都需要完整的備份處理
- RAID (硬碟陣列) 上儲存的資料並不代表兩份資料，只能算一份，RAID 或 Snapshot 等只是降低了該份資料因為硬體損壞導致資料遺失的機率
- 資料存放在物理上不同的位置就是俗稱的異地備份，定義上通常是越遠越好，但也不用太誇張，通常放幾公里外的親友家中就是可以接受的選擇 (雲端儲存也可以算是一種)

異地備份主要保護的是同一個地點的事故可能會導致所有的備份同時被一波帶走，例如火災、雷擊、地震等災害，如果遇到這種事故在家放了幾百份資料可能也沒什麼用了 😭

那麼了解了備份的規則及重要性後我們廢話不多說進入本文的重點吧！

## 硬體 & 軟體需求

### 硬體

這邊列一下我所使用的硬體清單，當然可以針對個人需求做調整：

1. [Raspberry Pi 5 (4G RAM)](https://www.raspberrypi.com/products/raspberry-pi-5/) (Pi 4 應該也沒什麼問題，但沒有 PCIe 只能透過 USB 連接硬碟)
2. [Geekworm (SupTronics) X1004](https://wiki.geekworm.com/X1004) (撰文時雙 M.2 的還有 [Pimoroni](https://shop.pimoroni.com/products/nvme-base-duo-for-raspberry-pi-5), [WaveShare](https://www.waveshare.com/wiki/PCIe_TO_2-CH_M.2_HAT+) 等廠商可選，或是使用單 M.2 HAT 當然也是沒問題的)
3. 一或兩條 HAT 支援的 M.2 NVMe SSD (注意必須要是 NVMe 協議才可以使用在 M.2 HAT 上)
4. 一台 NAS，備份資料的來源

NAS 部分我所使用的是 Synology DS220+，其他 NAS 當然也可以，不過這篇文會以我自己使用的環境為主。
外殼、散熱模組等非必要零件就不另外提到了，可以按照自己的需求喜好安裝 🙌

### 軟體

1. x86 DSM 7.2 (我自己 NAS 的環境)
2. Raspberry Pi OS (Bookworm)
3. rsync 作為備份同步用
4. ZeroTier 作虛擬內網的服務讓 NAS 能夠遠端訪問到 Pi
5. ufw 安裝在 Pi 上的防火牆

由於在 DSM 上跑 ZeroTier 會使用到 Docker，之前聽說 ARM 的 DSM 容易遇到架構支援問題，這邊可能需要注意一下！

完成之後異地備份的服務會如下圖執行：

![DSM Offsite Backup Using ZeroTier](https://static.driftking.tw/2024/06/203280990e36b9f56dd46a4e9ee82599.webp)

在了解了基本服務架構之後，由於硬體安裝方式不難參照各家說明書即可，接下來為安裝軟體及設定的部分。

## 在 Pi 上安裝 rsync 伺服器

rsync 伺服器的作用簡單來說其實有點像 SMB，但是專門設計為備份用途，完成後會讓 NAS 透過 Hyper Backup 連入樹莓派的 rsync 進行自動備份。當然 rsync 可以安裝在任何 Linux 主機上，只是本文會以樹莓派的 Raspberry Pi OS 來做設定 ⚡️

首先會需要讓樹莓派能夠執行 Raspberry Pi OS，系統可以安裝在 NVMe SSD、USB 硬碟或是 SD 卡上 (有做好系統備份的話應該還可以，但還是要準備有 SSD 或 HDD 來儲存備份資料用)，這部份因為不是本次設定重點，還請參考[樹莓派 OS 官方說明](https://www.raspberrypi.com/software/)

將樹莓派安裝好系統並開機後，可以透過 GUI 或 SSH 進入 Terminal 開始安裝 rsync。

### 備份資料用硬碟分區及格式化

首先執行 `sudo fdisk -l` (使用 `sudo` 會跳出要求管理員密碼，輸入後按 `Enter` 即可)

接著會出現一串硬碟資料，找到要當作備份用的目標硬碟，例如我的其中一段顯示 (輸出每個人會不同)：

```txt
...
Disk /dev/nvme0n1: 1.86 TiB, 2048408248320 bytes, 4000797360 sectors
Disk model: TEAM TM8FPD002T
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: XXX

Device         Start        End    Sectors  Size Type
/dev/nvme0n1p1  2048 4000796671 4000794624  1.9T [分區類型]
...
```

這邊注意如果是 USB 外接硬碟可能會顯示 `/dev/sda` 、 `/dev/sdb` 等，若跟我一樣使用 Pi 5 的 PCIe 連接 SSD 則會顯示類似如上 `/dev/nvme0n1`

接著需要把硬碟格式化為 `ext4` 格式

{% colorquote danger %}
此動作會清除硬碟上所有資料
{% endcolorquote %}

```shell
sudo fdisk /dev/nvme0n
```

進入後輸入指令 `d` 刪除分區

```shell
Command (m for help): d
```

輸入指令 `n` 新增分區

```shell
Command (m for help): n
```

最後輸入 `w` 完成操作

```shell
Command (m for help): w
```

接下來格式化硬碟為 `ext4`

```shell
sudo mkfs -t ext4 /dev/nvme0n1p1
```

等待跑完無錯誤後就代表成功把硬碟格式化為 `ext4` 了。

### 備份資料用硬碟分區掛載

建立一個掛載分區用的資料夾 (名字可以自行挑選，後續的設定記得使用相同的路徑即可)

```shell
sudo mkdir /mnt/backup
```

接下來要取得對應分區的 UUID：

```shell
sudo blkid
```

在輸出的資料中找到對應的分區，例如：

```txt
/dev/nvme0n1p1: UUID="539f659e-cd1d-4f81-9ea5-6ec0f1344c66" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="1685ae4a-c0c5-4259-8f96-38a660deda8a"
/dev/nvme1n1p2: LABEL="rootfs" UUID="d1f0e69f-2ab2-4f75-bbe1-84db9d036629" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="423c4ab8-02"
/dev/nvme1n1p1: LABEL_FATBOOT="bootfs" LABEL="bootfs" UUID="50C8-AEAE" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="423c4ab8-01"
```

由於我們的目標硬碟是 `/dev/nvme0n1p1` 所以複製對應的 UUID `539f659e-cd1d-4f81-9ea5-6ec0f1344c66` (此 UUID 為範例用)

接著我們要設定系統啟動時自動掛載這個分區

```shell
sudo nano /etc/fstab
```

進去後將游標移到最下方 (不需理會井字號開頭的部分)，接著參考下方第四行 `UUID=...` 填入設定，空格部分用 `Tab` 或空白鍵分隔都可以

```txt
proc            /proc           proc    defaults          0       0
PARTUUID=423c4ab8-01  /boot/firmware  vfat    defaults          0       2
PARTUUID=423c4ab8-02  /               ext4    defaults,noatime  0       1
UUID=539f659e-cd1d-4f81-9ea5-6ec0f1344c66       /mnt/backup     ext4    defaults,auto,users,rw,nofail   0       0
# a swapfile is not a swap partition, no line here
#   use  dphys-swapfile swap[on|off]  for that
```

完成後 `Control + S` & `Control + X` 儲存並離開編輯器。

接下來就可以掛載所有硬碟：

```shell
sudo mount -a
```

檢查掛載是否成功

```shell
lsblk
```

如果能看到剛才設定的 `/mnt/backup` 就代表成功掛載了

```txt
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
nvme0n1     259:0    0   1.9T  0 disk
└─nvme0n1p1 259:1    0   1.9T  0 part /mnt/backup <- 這裡
nvme1n1     259:2    0 476.9G  0 disk
├─nvme1n1p1 259:3    0   512M  0 part /boot/firmware
└─nvme1n1p2 259:4    0 476.4G  0 part /
```

接下來給予掛載路徑權限 (這邊給 777 是因為備份的檔案會加密所以沒有太大問題)

```shell
sudo chmod -R 777 /mnt/backup
```

這邊測試建立一個資料夾

```shell
touch /mnt/backup/test
```

如果沒有報錯代表設定無誤，可以刪除該測試檔案

```shell
rm /mnt/backup/test
```

### 安裝 rsync 伺服器

更新套件包

```shell
sudo apt update
```

安裝 rsync

```shell
sudo apt install rsync
```

建立 rsync 設定檔

```shell
sudo nano /etc/rsyncd.conf
```

輸入以下設定

```txt
[backup]
        path = /mnt/backup
        comment = For the rsync backup for Synology NAS
        read only = false
        timeout = 300
        auth users = synology
        secrets file = /etc/rsyncd.secrets
```

完成後 `Control + S` & `Control + X` 儲存並離開編輯器。

建立 rsync 密碼檔

```shell
sudo nano /etc/rsyncd.secrets
```

輸入以下設定

```txt
synology:[輸入 rsync 密碼]
```

完成後 `Control + S` & `Control + X` 儲存並離開編輯器。

更新 secrets 檔案權限只有 root 能讀寫

```shell
sudo chmod 600 /etc/rsyncd.secrets
```

進入 rsync 設定開啟服務

```shell
sudo nano /etc/default/rsync
```

找到 `RSYNC_ENABLE=false` 並改為

```txt
RSYNC_ENABLE=true
```

完成後 `Control + S` & `Control + X` 儲存並離開編輯器。

最後重啟樹莓派檢查 rsync 服務是否自動啟動

```shell
sudo reboot
```

重啟後輸入以下指令檢查是否成功啟動 rsync

```shell
ps -ax | grep rsync
```

如果成功應該會有類似以下的輸出

```txt
    860 ?        Ss     0:01 /usr/bin/rsync --daemon --no-detach
  12942 pts/0    S+     0:00 grep --color=auto rsync
```

恭喜完成！接下來可以準備前往 DSM 做設定並連到剛設定好的 rsync server 了！ 🙌

## 在 DSM 7 中安裝 Hyper Backup 並在區網中執行首次備份

登入 DSM 後進入 `Package Center` 套件中心進行 `Hyper Backup` 的安裝

![DSM Package Center - Hyper Backup](https://static.driftking.tw/2024/06/770c3672713fe28d71a2cb8532b5ae37.webp)

安裝完成後開啟 `Hyper Backup` 並使用第一個 `Folders and Packages` 按下一步

![Backup Type](https://static.driftking.tw/2024/06/e2feb0b8c2cf25800a2073d35a6397b8.webp)

接下來拉到最下方的 `File Server` 區域，選擇 `rsync` 後下一步

![rsync server](https://static.driftking.tw/2024/06/2c5a27e2b53664845db5b5d2f15efba2.webp)

這邊選擇 `Multiple versions` 即可，運作原理類似 Snapshot 會儲存不同的檔案更變，`Single version` 則是單純把檔案拷貝過去

![Backup Version Type](https://static.driftking.tw/2024/06/137e2163af4afaebbf71512975b5b1c1.webp)

接著在

- `Server name or IP address` 輸入樹莓派所在的區網 IP
- `Username` 輸入剛才設定的 rsync 使用者名稱 `synology`
- `Password` 輸入剛才設定的 rsync 使用者密碼 (secrets)

Transfer encryption 可以留著 OFF 即可，因為我們使用的 ZeroTier 連線方式已經有加密的功能，這樣可以讓備份速度更快一點。

以上設定正確的話應該就能在 `Shared Folder` 下拉選單選擇剛才在 rsync 伺服器建立的資料夾了！

![Backup Destination](https://static.driftking.tw/2024/06/389ad8fb3482a1109ac030a369cac6d8.webp)

下一步之後就可以選擇我們在 NAS 中所需要備份的資料夾，在下一步會選擇想備份的應用程式，這邊按自己需求選擇即可。

![Data Backup](https://static.driftking.tw/2024/06/c77caa54f08c57aa35c56cdef95f0a15.webp)

最後進行備份任務的設定，這邊按自己需求調整，並記得開啟 `Enable client-side encryption` 並牢記密碼

![Backup Settings](https://static.driftking.tw/2024/06/93ee3bd6c541e6822e2db75f8df790f4.webp)

下一步設定 Rotation 自動清除舊資料，選擇 `Smart Recycle` 即可

![Rotation Settings](https://static.driftking.tw/2024/06/86fa63df8f7fb28c9998fc359bb1b1f6.webp)

完成後就可以來執行第一次備份啦！建議第一次檔案比較大先在區網備份速度較快，之後只會做差異備份再移至異地即可！

![HyperBackup First Backup](https://static.driftking.tw/2024/06/e35b7574604b28aa8dd905b62ccad453.webp)

當檔案備份的時候，我們可以繼續往下設定 ZeroTier 的部分為外網連線做準備！

## 設定 ZeroTier 虛擬區網服務

簡單來說 ZeroTier 是一個虛擬區網的服務，可以讓處在不同網路的裝置加入這個虛擬區網透過 P2P 達成互相連線，就像在一個區網中一樣。在設定上相當簡易，而且作為個人備份是完全免費的 (免費版提供 25 個裝置加入，並且無限制流量)！我們可以透過這個功能讓 NAS 透過外網連到 Pi 上的 rsync 伺服器達成備份的任務。

首先需要先到 [ZeroTier](https://my.zerotier.com/) 註冊一個帳號並登入

成功進入控制面板後會看到如下畫面，一開始應該是沒有任何網路設定的，此時按 `Create A Network` 建立虛擬區網，並點新增的項目進入設定：

![ZeroTier Dashboard](https://static.driftking.tw/2024/06/0d793c79207de8b8a388a9b39cde1973.webp)

設定值基本上沒有特別需求用預設即可，點進此區網後我們先複製如圖中的 `Network ID` 的值方便設定

![Network ID](https://static.driftking.tw/2024/06/b5b5f9ce94058c904d6b1219254528fe.webp)

接著就可以準備把各個需要連線的設備加入這個區網中囉！

## 在 DSM 7 上連入 ZeroTier

首先先將 NAS 連入 ZeroTier 中，這部份也可以參考[官方文件](https://docs.zerotier.com/synology/)操作。

### 安裝 Docker (Container Manager)

DSM 進入 Package Center (套件中心) 並搜尋 `container`

選擇 `Container Manager` 並安裝：

{% colorquote warning %}
舊版 DSM 套件名稱為 Docker，新版已改名為 Container Manager，這兩個是相同的東西 👌
{% endcolorquote %}

![DSM Package Center - Container Manager](https://static.driftking.tw/2024/06/ad5a3a8b5613030db0d97bf2a6670d5f.webp)

### 開啟 SSH

接著我們先將 DSM 的 SSH 功能從控制台中打開，並記得點擊套用。

![Enabel DSM SSH](https://static.driftking.tw/2024/06/fa0be144e7047e873e5e848efc206253.webp)

### 建立 Persistent TUN

SSH 進入 DSM (macOS 及 Linux 透過 Terminal 即可，Windows 可使用 Putty 等軟體)，user 使用者及 local-ip 替換為自己設定的 DSM 使用者及區網 IP

```shell
ssh user@local-ip
```

取得 root 權限

```shell
sudo -i
```

加入啟動時的自動設定腳本

```shell
echo -e '#!/bin/sh -e \ninsmod /lib/modules/tun.ko' > /usr/local/etc/rc.d/tun.sh
```

設定執行腳本所需的權限

```shell
chmod a+x /usr/local/etc/rc.d/tun.sh
```

執行腳本

```shell
/usr/local/etc/rc.d/tun.sh
```

測試通道是否建立

```shell
ls /dev/net/tun
```

若輸出 `/dev/net/tun` 並無報錯代表成功。

### 設定容器

建立資料夾儲存 ZeroTier 相關的檔案及設定

```shell
mkdir /var/lib/zerotier-one
```

建立 Docker 容器

```shell
docker run -d           \
  --name zt             \
  --restart=always      \
  --device=/dev/net/tun \
  --net=host            \
  --cap-add=NET_ADMIN   \
  --cap-add=SYS_ADMIN   \
  -v /var/lib/zerotier-one:/var/lib/zerotier-one zerotier/zerotier-synology:latest
```

跑完之後可以透過以下指令確認容器是否正常執行

```shell
docker ps
```

若顯示類似如下圖即可

```txt
CONTAINER ID   IMAGE                                 COMMAND            CREATED       STATUS       PORTS     NAMES
451a2e9d6d02   zerotier/zerotier-synology:latest     "/entrypoint.sh"   2 weeks ago   Up 2 weeks             zt
```

接著我們要把 NAS 加入到 ZeroTier 的區網 (`[Network_ID]` 記得要換成自己的)

```shell
docker exec -it zt zerotier-cli join [Network_ID]
```

成功的話會回應 `200 join OK` 此時我們可以回到 ZeroTier 的控制台拉到下方 `Members` 區塊查看

![ZeroTier Members](https://static.driftking.tw/2024/06/5030cfd9db2702542d28da9b024e0712.webp)

此時手動把前方的 Auth? 選取格打勾即可認證，過幾秒後 Managed IPs 下方應該就會出現該裝置在這個虛擬區網的 IP 了 (此範例為 192.168.193.78)

![Auth Member](https://static.driftking.tw/2024/06/70f1a0d10fd8c7e1c93dfce24329458d.webp)

{% colorquote info %}
這邊建議可以在 `Name` 與 `Description` 中填入自己看的懂得名稱和描述方便辨認裝置喔！
{% endcolorquote %}

接著在 SSH 中可透過 `sudo zerotier-cli listnetworks` 來查看加入的區網狀態

最後我們還需要回到 DSM 中允許這個網域經過防火牆

從 `控制台` > `安全性` > `防火牆` 頁籤 > `編輯規則` 進入設定，並點擊 `新增` 增加規則

![DSM Firewall](https://static.driftking.tw/2024/06/441b63640fd04ca4525f491bc0c0ad88.webp)

設定如下，連接埠看需求開啟， `來源 IP` 選擇指定 IP，點擊選擇後輸入剛才 ZeroTier 中的 IP 範圍 (例如範例中的 `192.168.193.0` ~ `192.168.193.255`)

![DSM Firewall Configuration](https://static.driftking.tw/2024/06/24fc0d147890ac40098c9d8b8f5d0980.webp)

接著套用設定並確認 `Enable` 打勾即可，到這邊防火牆設定就完成了！

## 在 Pi 上連入 ZeroTier

最後我們需要讓 Pi 也連上 ZeroTier，連入 Pi SSH 透過 Github 安裝 ZeroTier

新增 GPG Key

```shell
curl https://raw.githubusercontent.com/zerotier/ZeroTierOne/master/doc/contact%40zerotier.com.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/zerotierone-archive-keyring.gpg >/dev/null
```

接著加入 Source List 到系統中

```shell
RELEASE=$(lsb_release -cs)
echo "deb [signed-by=/usr/share/keyrings/zerotierone-archive-keyring.gpg] http://download.zerotier.com/debian/$RELEASE $RELEASE main" | sudo tee /etc/apt/sources.list.d/zerotier.list
```

更新套件倉庫

```shell
sudo apt update
```

即可安裝 ZeroTier

```shell
sudo apt install -y zerotier-one
```

安裝完成後就可以讓樹莓派加入 ZeroTier 區網了 (`[Network_ID]` 記得要換成自己的)

```shell
sudo zerotier-cli join [Network_ID]
```

成功的話會回應 `200 join OK` 此時操作與 DSM 加入後一致再次回到 ZeroTier 的控制台拉到下方 `Members` 區塊查看，並勾選 Auth? 方塊。

接著在 SSH 中可透過 `sudo zerotier-cli listnetworks` 來查看加入的區網狀態

上述步驟都成功完成後 ZeroTier 的控制面板 `Member` 區域應該會有類似下表的裝置設定

![ZeroTier Members](https://static.driftking.tw/2024/06/c38ea250465757d394697b9b5f8176aa.webp)

## 測試遠端備份

恭喜！設定到這邊大致就完成了，最後只要把樹莓派在 ZeroTier 中的 IP 複製下來，並回到 DSM 的 HyperBackup 中更改任務中 Target 的 Server IP 即可。

![Connection Successful](https://static.driftking.tw/2024/06/44ee9a5a49a213e38f75d4acd8dadb14.webp)

完成後應該會顯示 Target On-line 綠色字樣，接著只要樹莓派在有網路的地方都能夠透過 ZeroTier 幫 NAS 做定期備份囉！

## 啟用 UFW 防火牆 (可選)

通常情況下 ZeroTier 區網應該是安全的，但如果有各種不同的設備使用者在同個虛擬區網的話可以設定一下樹莓派的防火牆比較令人安心，這邊使用的是 UFW

更新套件倉庫及系統

```shell
sudo apt update
sudo apt full-upgrade
```

安裝 UFW

```shell
sudo apt install ufw
```

接下來這部非常重要，我們要先設定讓 SSH 能夠通過防火牆，如果沒有設定的話就再也無法在透過 Headless (無頭) 模式連上樹莓派，需要接螢幕鍵盤等操作來恢復了

```shell
sudo ufw allow 22
```

接著我們到 ZeroTier 主控台中找到 NAS 的 IP 並允許該 IP 的連線 (`[YOUR_NAS_IP]` 記得替換為實際的 IP)

```shell
sudo ufw allow [YOUR_NAS_IP]
```

啟動防火牆

```shell
sudo ufw enable
```

此時系統會提示如下啟用防火牆可能導致目前的 SSH Session 斷線，輸入 `y` 按下 `Enter` 即可啟用防火牆

`Command may disrupt existing ssh connections. Proceed with operation (y|n)?`

最後可以透過 `sudo ufw status` 確認防火牆的狀態，若要停用防火牆則使用 `sudo ufw disable`。

以上！ ~~接下來應該不用擔心資料安全的問題了~~

## 參考

[SpaceRex - Backup Synology NAS To a $35 RaspberryPi with HyperBackup and Rsync Daemon! | 4K TUTORIAL](https://www.youtube.com/watch?v=-dhnkncnLWs)

[Running ZeroTier on the Raspberry Pi](https://pimylifeup.com/raspberry-pi-zerotier/)

[Using the UFW Firewall on the Raspberry Pi](https://pimylifeup.com/raspberry-pi-ufw/)
