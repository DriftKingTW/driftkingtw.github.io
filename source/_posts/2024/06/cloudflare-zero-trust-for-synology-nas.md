---
title: 透過 Cloudflare Zero Trust 與 WARP Client 從外網連回 Synology DSM
tags:
  - NAS
  - Synology
  - DSM
  - Cloudflare
keywords:
  - NAS
  - Synology
  - DSM
  - Cloudflare
  - ZeroTrust
  - WARP
abbrlink: 3d6096f8
date: 2024-06-24 21:00:00
---

![透過 Cloudflare Zero Trust 與 WARP Client 從外網連回 Synology DSM](https://static.driftking.tw/2024/06/b3f87fbf3e051cc939e6fa28896a872a.webp)

由於現在的網路環境有兩層路由，且對外的路由我沒有控制權，以前要從外網連回家中的 Synology NAS 都要透過群暉的 `QuickConnect` 實現，後來經朋友推薦發現 `Cloudflare Zero Trust` 可以達成類似功能，並且可以掛上自己的域名！！(重要)

搜尋了一下發現設定方式相對不算太難，所以把設定過程紀錄下來供大家參考或是未來自己複習用！

<!--more-->

在開始之前，這邊要特別感謝 `sakkyoi` 的教學文章： [用 Cloudflare Tunnel 打通 Synology NAS](https://sakkyoi.tech/articles/123#Tunnel%20%E6%90%AD%E9%85%8D%20WARP%EF%BC%8C%E7%9B%B4%E6%8E%A5%E8%A8%AA%E5%95%8F%E4%BD%A0%E7%9A%84%E5%85%A7%E7%B6%B2)，
非常清晰易懂也推薦各位參考，本文過程參照此文設定並加入一些個人筆記的細節及 Cloudflare 功能上的小更新。

## 事前準備

- 運行 Synology DSM 的 NAS (撰文時所需套件要求為 DSM 5 以上)
- 自己的 Domain Name
- Cloudflare 帳號
- 能夠刷的信用卡 (提供 Cloudflare 付費資訊用不會產生費用)
- 將 DNS 託管在 Cloudflare 上 (用其他 DNS Provider 應該也可以只是要多做設定，因為我原本就是用他們家的服務)

## Zero Trust 團隊設定

首先登入 [Cloudflare Zero Trust](https://one.dash.cloudflare.com/) 並建立團隊 (名稱任意)

選擇付費方案：一般個人使用 Free 應該就相當夠用，但記得即使免費方案還是需要輸入付費資訊才能夠使用喔！

成功建立好團隊及付款設定後會進到如下的 Dashboard 頁面

![Zero Trust Dashboard](https://static.driftking.tw/2024/06/189164e66a8800e0406aaf8eb5409022.webp)

## 建立 Application

接下來要建立一層 Cloudflare Zero Trust 的保護應用在我們的 Tunnel 之前，這樣就可以讓使用者連入相關服務時讓平台
協助確認使用者的身份 (須先登入稍候設定的帳號或經過第三方登入驗證) 確保應用的安全，強力建議設定！

### 新增 Application

在左側導覽的 `Access` 選擇 `Application` 進入以下頁面，點擊 `+ Add an application` (首次設定的話頁面會稍有不同)

![Application](https://static.driftking.tw/2024/06/a7e256cbfcbea23c35dae518e5541bd3.webp)

接著選擇 `Self-hosted`

![Self-hosted](https://static.driftking.tw/2024/06/b2ba4bd4ba880456c9a3e5f92c178891.webp)

### Application 設定

自行設定想要的 `Application name`，另外可以選擇性調整 `Session Duration` 讓使用者可以保持驗證狀態多長時間。

在 `Application domain` 設定自己的域名，若要掛在 Subdomain 底下也請再此設定。

另外下方的 `Identity providers` 則是可以設定不同的登入方式 (預設為 OTP 登入)，這邊可以先不做變動，
稍候到 [第三方登入驗證方式](#第三方登入驗證方式) 會做 Github 登入的設定範例：

![alt text](https://static.driftking.tw/2024/06/cd064f17315a2954d9a7fcaca6ff2069.webp)

### Policy 設定

`Policy Name` 隨意填入，`Action` 選擇 `Allow` 代表我們正在設定允許連入的規則，`Session duration` 預設為跟應用相同即可。

由於要限制只有我們自己的 Email 能夠登入，所以在 `Configure rules` 的 `Selector` 選擇 `Emails`，
並在 `Value` 填入自己的信箱。同理若要讓其他人連入也可進行相對的設定。

![Policy Configuration](https://static.driftking.tw/2024/06/b517f4ebfceaf02903cc5496a33b75ce.webp)

接下來 `Next` 下一頁後，可以直接使用預設設定拉到最下面並點擊 `Add application` 完成新增！

## 建立與 NAS 連線的 Tunnel

接著我們要建立一個與 NAS 連線的 Tunnel，在左側導覽找到 `Network` 並選擇 `Tunnels` 進入設定頁面。

點擊 `+ Create a tunnel` 來建立通道 (首次設定的話頁面會稍有不同)：

![Tunnels](https://static.driftking.tw/2024/06/d9349cf0168bdbf6019b1825c35746c7.webp)

這邊選擇預設的 `Cloudflared` 連接器即可點選下一步：

![Cloudflared Tunnel](https://static.driftking.tw/2024/06/13c11a751f8698ca0bd50559be6f358e.webp)

輸入喜歡的 Tunnel 名稱，點擊 `Save tunnel`：

![Tunnel Configuration](https://static.driftking.tw/2024/06/66c4ce0eeb015efc7e935b8ff6296c29.webp)

接著會顯示如何安裝及運行連接器，如果是要在其他的一般機器設定可以透過提供的指令安裝。
我們這邊要設定在 DSM 上設定所以可以把指令複製下來後只截取後面 `ey...` 那段的 Token 保存即可，稍候設定會用到。

![Connector](https://static.driftking.tw/2024/06/ab63b40cad71da5ba7cb49e528296024.webp)

下一步後，設定 `Public Hostnames`，`Domain` 與剛才的設定相同，`Service`
部分則填入 NAS 在內網的 IP 位址 (DSM 預設 HTTP 為 `5000`，HTTPS 則為 `5001`)。

接著點開 `Additional application settings`，若使用 Synology 預設的憑證則要開啟 `No TLS Verify`。

接著選擇 `Access` 套用剛才設定的 `Access Application` 即可儲存 Tunnel：

![Route Traffic](https://static.driftking.tw/2024/06/22af506d7a75d54cd95d352654802b48.webp)

## 在 DSM 安裝 Cloudflare Tunnel

### 新增套件來源

首先透過內網登入 DSM，進入套件中心 (Package Center)，點選右上角的設定 (Settings) 並選擇套件來源 (Package Sources) 後按下新增 (Add)，
輸入以下資訊 (名稱選用自己看的懂得即可)：

```
https://packages.synocommunity.com/
```

![Package Center](https://static.driftking.tw/2024/06/330c96ebef43742c1aeed4377aebb628.webp)

### 安裝 Cloudflare Tunnel

接著在套件中心 (Package Center) 中搜尋 `Cloudflare Tunnel` 安裝：

![DSM Cloudflare Tunnel](https://static.driftking.tw/2024/06/50122fb46abec734808b178fe1e02c02.webp)

安裝完成後按照提示輸入剛才取得的 Token 並執行即可。

## 完成 Tunnel 並測試

接著就可以到瀏覽器中輸入剛才設定過的 Domain 測試看能不能成功進入 Cloudflare Access 的頁面
(未設定第三方登入的話不會顯示 Github 的登入欄位)：

![Cloudflare Access](https://static.driftking.tw/2024/06/668eea5e53dc19a7bc8eb042e6adcdaa.webp)

登入後即可透過外網輸入自訂的網址進入 DSM 跟各種 HTTP 服務囉！ (如 `/file`、`/audio` 等 DSM 上安裝的服務)

## 第三方登入驗證方式（可選）

由於 OTP 每次都要透過 Email 接收相當麻煩，我們可以加入不同的第三方登入服務讓登入更方便！

回到 Zero Trust Dashboard 選擇左側導覽的 `Settings` 後點選 `Authentication` 新增不同的驗證方式：

![Authentication](https://static.driftking.tw/2024/06/26784ae6dfb1af19166245735b6f9b80.webp)

進入後選擇下方 `Login methods` 點選 `Add new` 新增不同的第三方登入設定：

![Login methods](https://static.driftking.tw/2024/06/b0caee4ac543e4be8433cd1a52231878.webp)

選擇需要的第三方服務後就會出現各服務所需要的驗證資訊，Cloudflare 都有附上圖文操作安照對應的方式填入資訊即可啟用囉！
設定完成後回到 Zero Trust Access 驗證頁面時就會出現 `Sign in with` 的欄位了。

## 設定 Cloudflare WARP 搭配 WARP Client 訪問內網 Non-HTTP 服務

以上的設定能夠讓 HTTP 服務透過外網連入了，但由於 Tunnel 是透過 WebSocket 實現並不支援 Non-HTTP 的服務 (如 SMB 等)，
同時官方的行動 App 會因為 Access 驗證被阻擋無法使用，所以如果需要連入其他協議的服務或訪問其他內網裝置的話，
我們可以透過 WARP 這個服務建立一個 Private Network 解決。

### 路由設定

首先從左側導覽的 `Networks` 進入 `Routes`，點選 `+ Create route`：

![Routes](https://static.driftking.tw/2024/06/030dfddfd8e907696371e8f58667cc79.webp)

我的內網環境是 `192.168.1.0 ~ 192.168.255` 故 `CIDR` 欄位需填入 `192.168.1.0/24`，
描述可以任意填寫，Tunnel 則選擇剛才建立的 Tunnel 即可。

完成後點擊 `Create` 即可完成路由設定。

> CIDR 相關可參考：[WIKI - CIDR](https://zh.wikipedia.org/zh-tw/%E6%97%A0%E7%B1%BB%E5%88%AB%E5%9F%9F%E9%97%B4%E8%B7%AF%E7%94%B1)

![Edit route](https://static.driftking.tw/2024/06/fa96ad92a24eb5ba304dfb983d6c6c54.webp)

### WARP Client 設定

左側導覽選擇 `Settings`，點擊 `WARP Client` 子項目：

![WARP Client](https://static.driftking.tw/2024/06/a2e8a628db257c28fe007aa7d6978cb1.webp)

在 `Device enrollment` 點選 `Manage` 設定裝置驗證：

![Device enrollment](https://static.driftking.tw/2024/06/4abfe07656619eeeb560b25c66e7e61d.webp)

點擊 `+ Add a rule` 加入驗證規則，這邊跟之前設定的驗證規則類似不再贅述，完成後點選 `Save` 即可。

![Rules](https://static.driftking.tw/2024/06/8d37cfbd53d0b626d72897626197f772.webp)

接著回到上一頁的 `WARP Client` 設定頁面 (`Settings` > `WARP Client`)

找到 `Device settings` 區塊，在 `Default` Profile 右側點擊 `Configure`：

![Default Profile](https://static.driftking.tw/2024/06/ae66adaa596ca6936a4bdfe10cd074d6.webp)

找到 `Split Tunnels`，點擊 `Manage`：

![Manage Split Tunnels](https://static.driftking.tw/2024/06/7001a9b18df2bfd3fb9520cad3e3406b.webp)

找到包含自己網域的設定項目並把他刪除 (此為 Exclude 規則故需刪除要連線的網段)

![Remove from Exclude](https://static.driftking.tw/2024/06/95e442d7c95413932061b2c3ce760cf4.webp)

接著從左側導覽 `Settings` 選擇 `Network`：

![Network](https://static.driftking.tw/2024/06/87d0340d843ecb0e6751543387178807.webp)

到 `Firewall` 把 `Proxy` 啟用，協議部分可以把 `UDP` 一併啟用：

![Firewall](https://static.driftking.tw/2024/06/201667ed8c1a5db7caaf0461a67cec27.webp)

### WARP Client 安裝

接下來到 [WARP Client](https://one.one.one.one/) 下載頁面下載對應的客戶端並安裝。

安裝 App 後按照只是填入 Team Name 進行驗證跟 VPN 安裝成功後會出現類似畫面 (原先顯示 WARP，完成後顯示 Zero Trust)：

> 我一開始在 iOS 安裝 VPN 時卡住導致無法驗證，建議先將系統內的原本使用的 VPN 等先關閉在進行設定

![iOS WARP Client](https://static.driftking.tw/2024/06/38536fb73c17f585d4a451c43a486b30.webp)

完成後點擊開關即可透過 WARP 連入區網，可以在外網透過 smb 等協議連上 NAS 測試是否設定成功。
現在就可以透過 WARP Client 夠連入內網中的其他機器囉，另外 Synology 官方的 App 也能夠直接透過區網 IP 連入使用了！

## 參考資料

- [用 Cloudflare Tunnel 打通 Synology NAS](https://sakkyoi.tech/articles/123#Tunnel%20%E6%90%AD%E9%85%8D%20WARP%EF%BC%8C%E7%9B%B4%E6%8E%A5%E8%A8%AA%E5%95%8F%E4%BD%A0%E7%9A%84%E5%85%A7%E7%B6%B2)
- [Securely access home network with Cloudflare Tunnel and WARP](https://simplyexplained.com/blog/securely-access-home-network-with-Cloudflare-Tunnel-and-WARP/)
- [WIKI - CIDR](https://zh.wikipedia.org/zh-tw/%E6%97%A0%E7%B1%BB%E5%88%AB%E5%9F%9F%E9%97%B4%E8%B7%AF%E7%94%B1)
- [1.1.1.1](https://one.one.one.one/)
