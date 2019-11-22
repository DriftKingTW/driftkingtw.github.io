---
title: 利用BetterTouchTool在Touchbar上顯示CPU溫度
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

# How

1. 下載 [osx-cpu-temp](https://github.com/lavoiesl/osx-cpu-temp) 。
2. 進入下載的資料夾後執行 `sudo make install` 安裝。
3. 在 BetterTouchTool 裡新增 Widget/Gesture，選擇 'Run Shell Script and Show Return Value'。
4. 雙擊打開進階設定，更改 LaunchPath `/usr/local/bin/osx-cpu-temp` ，下方可以調整更新間隔（預設5秒）。
5. 選擇要不要新增icon跟其他外觀設定，完成！

![Touchbar顯示CPU溫度](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1540517774/blog/2018/10/%E5%88%A9%E7%94%A8BetterTouchTool%E5%9C%A8Touchbar%E4%B8%8A%E9%A1%AF%E7%A4%BACPU%E6%BA%AB%E5%BA%A6/Touch_Bar_Shot_2018-10-26_at_9.34.17_AM.png)

## 參考資料

[Display CPU Temp](https://community.folivora.ai/t/display-cpu-temp/1082)