---
title: Raspberry Zero W Headless 自動連上無線網路與開啟SSH
tags:
  - Raspberry Pi
abbrlink: 6759087b
date: 2017-12-14 00:35:47
keywords:
  - Raspberry pi
  - wifi
  - 自動
---

沒想到一拖就拖到12月才更新了, 應該勤奮點的(ry
之前因緣際會下, 請朋友從英國帶回來了觀望已久的 RPI ZERO W ✧◝(⁰▿⁰)◜✧
先上張美圖

![](https://res.cloudinary.com/driftkingtw/image/upload/v1518274118/blog/2017/12/14/Raspberry_Zero_W_Headless_自動連上無線網路與開啟SSH筆記/P_20171011_181938-1024x778.jpg)

原本想多帶幾張回來的, 想說台灣代理的都好貴...但是發現一單只能買一張, 只好先收一張回來玩, 看來真的很搶手阿QQ
加碼自然風外殼!! 這簡直超棒阿XDDD 雖然我也有買官方的Zero殼, 以後如果要用到攝影機相關的project會比較方便(^u^)

![](https://res.cloudinary.com/driftkingtw/image/upload/v1518274129/blog/2017/12/14/Raspberry_Zero_W_Headless_自動連上無線網路與開啟SSH筆記/358402-1024x609.jpg)

好了, 廢話少說, 立刻進入正題：
雖然RPI有HDMI跟USB接口, 但我就是很懶得接東接西, 希望能夠直接在PC上控制他, 這種叫做Headless的啟動設定 <del>沒裝螢幕 稱為無頭</del>
Zero W跟普通PI3的差異除了外觀還有就是Zero W並沒有乙太網路口

所以平常把PI3接上路由器設定的方法是沒辦法直接移植到Zero W身上的
不過Zero W有內建WIFI晶片, 所以我們可以設定一下讓Zero自動連上家中的WIFI來進行操作, 而不需要外接螢幕鍵盤或OTG轉乙太再接路由器如此麻煩

1.  先將要裝的系統image燒錄到SD卡中, 然後在Windows下應該能看到一個label為BOOT, 且檔案格式為FAT32的分區(另一個為ext4, Windows下無法讀取)

2.  首先開啟SSH功能, 記得很久以前Raspbian預設是開的, 但後來關掉了, 不過我們還是能手動開啟他
首先打開Notepad++之類的軟體, 新增一個完全空白的檔案, 然後另存在boot根目錄, 存檔時選擇所有類型, 檔名"ssh" **(注意 無副檔名)
![](https://res.cloudinary.com/driftkingtw/image/upload/v1518274138/blog/2017/12/14/Raspberry_Zero_W_Headless_自動連上無線網路與開啟SSH筆記/Untitled-1.jpg)
**這樣就會啟動SSH的功能

3.  接著要設定無線網路, 首先新建一個名為"wpa_supplicant.conf"的檔案, 一樣另存在boot的根目錄, 接著在內容打上：
```ini
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdevupdate_config=1
network={
    ssid="SSID" //打上要連的無線網路SSID
    psk="PASSWORD" //打上要連的無線網路的密碼
}
```
這樣開機的時候, 系統就會去把設定檔抓到 /etc/wpa_supplicant/底下, 開機自動連線

4.  接著就可以把SD卡插進RPI裡面接上電源囉
稍等幾秒後, 打開提示命令字元, 輸入 arp -a 查看有沒有多出一組新的連線(或是到路由器的管理頁面找), 然後再將IP打上putty等SSH軟體就大功告成拉！
![](https://res.cloudinary.com/driftkingtw/image/upload/v1518274145/blog/2017/12/14/Raspberry_Zero_W_Headless_自動連上無線網路與開啟SSH筆記/Untitled-2.jpg)