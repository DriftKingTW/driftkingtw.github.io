---
title: PM2 各專案 Dotenv 路徑設定
tags:
  - pm2
keywords:
  - vue
  - express
  - dotenv
abbrlink: fb831668
date: 2021-10-15 16:00:00
---

使用 `PM2` 管理 Node App 時如果有使用 `doenv` 會讀取目前目錄的 `.env` 檔案，例如在以下的資料結構下：

```
myApp/
    app.js
    .env
```

在 `myApp` 同級目錄中執行 `pm2 start myApp/app.js` 時會去讀取同層級的 `.env` ，而不會讀取到 `myApp/.env`

此時除了可以進入 `myApp` 後再執行 `pm2 start` 外，也可以在 `app.js` 中設定 `.env` 路徑，如下：

<!--more-->

```javascript
const path = require('path'); 
require('dotenv').config({ path: path.join(__dirname, '.env') });
```

這樣設定後在使用 `ecosystem.config.js` 管理多個 App 時，就可以讀取到各 App 資料夾中個別的 `.env`。

# 參考資料

[node dotenv won't work with pm2](https://stackoverflow.com/questions/50689644/node-dotenv-wont-work-with-pm2/55853036#55853036)
