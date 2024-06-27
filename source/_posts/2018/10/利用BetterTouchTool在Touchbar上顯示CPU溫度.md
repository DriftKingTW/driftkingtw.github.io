---
title: 利用 BetterTouchTool 在 Touchbar 上顯示 CPU 溫度
tags:
  - MacOS
  - Touchbar
keywords:
  - MacOS
  - Touchbar
  - 溫度
abbrlink: cc83b42a
date: 2018-10-26 09:16:36
---

![Touchbar顯示CPU溫度](https://static.driftking.tw/2024/06/0c34eba51dc0eab28bce1b9c8826260a.png)

TouchBar?覺得很雞肋的話，不如手動讓他實用吧！<!--more-->

# How

1. 下載 [osx-cpu-temp](https://github.com/lavoiesl/osx-cpu-temp) 。
2. 進入下載的資料夾後執行 `sudo make install` 安裝。
3. 在 BetterTouchTool 裡新增 Widget/Gesture，選擇 'Run Shell Script and Show Return Value'。
4. 雙擊打開進階設定，更改 LaunchPath `/usr/local/bin/osx-cpu-temp` ，下方可以調整更新間隔（預設5秒）。
5. 選擇要不要新增icon跟其他外觀設定，完成！

## 參考資料

[Display CPU Temp](https://community.folivora.ai/t/display-cpu-temp/1082)